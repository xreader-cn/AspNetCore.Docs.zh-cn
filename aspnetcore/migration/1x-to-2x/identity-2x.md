---
title: 将身份验证和标识迁移到 ASP.NET Core 2.0
author: scottaddie
description: 本文概述了迁移 ASP.NET Core 1.x 身份验证和标识为 ASP.NET Core 2.0 的最常见步骤。
ms.author: scaddie
ms.date: 12/18/2018
uid: migration/1x-to-2x/identity-2x
ms.openlocfilehash: d11d41c82236436096660a24df81a3df4da0fb8e
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265361"
---
# <a name="migrate-authentication-and-identity-to-aspnet-core-20"></a><span data-ttu-id="ca561-103">将身份验证和标识迁移到 ASP.NET Core 2.0</span><span class="sxs-lookup"><span data-stu-id="ca561-103">Migrate authentication and Identity to ASP.NET Core 2.0</span></span>

<span data-ttu-id="ca561-104">通过[Scott Addie](https://github.com/scottaddie)和[Hao 永远](https://github.com/HaoK)</span><span class="sxs-lookup"><span data-stu-id="ca561-104">By [Scott Addie](https://github.com/scottaddie) and [Hao Kung](https://github.com/HaoK)</span></span>

<span data-ttu-id="ca561-105">ASP.NET Core 2.0 具有用于身份验证的新模型和[标识](xref:security/authentication/identity)这简化了使用服务的配置。</span><span class="sxs-lookup"><span data-stu-id="ca561-105">ASP.NET Core 2.0 has a new model for authentication and [Identity](xref:security/authentication/identity) which simplifies configuration by using services.</span></span> <span data-ttu-id="ca561-106">ASP.NET Core 1.x 应用程序使用身份验证或标识可以更新以使用新的模型，如下所述。</span><span class="sxs-lookup"><span data-stu-id="ca561-106">ASP.NET Core 1.x applications that use authentication or Identity can be updated to use the new model as outlined below.</span></span>

<a name="auth-middleware"></a>

## <a name="authentication-middleware-and-services"></a><span data-ttu-id="ca561-107">身份验证中间件和服务</span><span class="sxs-lookup"><span data-stu-id="ca561-107">Authentication Middleware and services</span></span>

<span data-ttu-id="ca561-108">在 1.x 项目中，通过中间件配置身份验证。</span><span class="sxs-lookup"><span data-stu-id="ca561-108">In 1.x projects, authentication is configured via middleware.</span></span> <span data-ttu-id="ca561-109">中间件方法调用每个你想要支持的身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="ca561-109">A middleware method is invoked for each authentication scheme you want to support.</span></span>

<span data-ttu-id="ca561-110">下面的 1.x 示例中的标识配置 Facebook 身份验证*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca561-110">The following 1.x example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="ca561-111">在 2.0 项目中，通过服务配置身份验证。</span><span class="sxs-lookup"><span data-stu-id="ca561-111">In 2.0 projects, authentication is configured via services.</span></span> <span data-ttu-id="ca561-112">在中注册每个身份验证方案`ConfigureServices`方法*Startup.cs*。</span><span class="sxs-lookup"><span data-stu-id="ca561-112">Each authentication scheme is registered in the `ConfigureServices` method of *Startup.cs*.</span></span> <span data-ttu-id="ca561-113">`UseIdentity`方法替换`UseAuthentication`。</span><span class="sxs-lookup"><span data-stu-id="ca561-113">The `UseIdentity` method is replaced with `UseAuthentication`.</span></span>

<span data-ttu-id="ca561-114">下面的 2.0 示例中的标识配置 Facebook 身份验证*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca561-114">The following 2.0 example configures Facebook authentication with Identity in *Startup.cs*:</span></span>

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

<span data-ttu-id="ca561-115">`UseAuthentication`方法将添加一个单一的身份验证中间件组件，它负责进行自动身份验证和远程身份验证请求的处理。</span><span class="sxs-lookup"><span data-stu-id="ca561-115">The `UseAuthentication` method adds a single authentication middleware component which is responsible for automatic authentication and the handling of remote authentication requests.</span></span> <span data-ttu-id="ca561-116">它会替换所有单独的中间件组件共有的中间件组件。</span><span class="sxs-lookup"><span data-stu-id="ca561-116">It replaces all of the individual middleware components with a single, common middleware component.</span></span>

<span data-ttu-id="ca561-117">下面是有关每个主要身份验证方案的 2.0 迁移说明。</span><span class="sxs-lookup"><span data-stu-id="ca561-117">Below are 2.0 migration instructions for each major authentication scheme.</span></span>

### <a name="cookie-based-authentication"></a><span data-ttu-id="ca561-118">基于 cookie 的身份验证</span><span class="sxs-lookup"><span data-stu-id="ca561-118">Cookie-based authentication</span></span>

<span data-ttu-id="ca561-119">选择以下两个选项之一并进行必要的更改在*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca561-119">Select one of the two options below, and make the necessary changes in *Startup.cs*:</span></span>

1. <span data-ttu-id="ca561-120">标识与使用 cookie</span><span class="sxs-lookup"><span data-stu-id="ca561-120">Use cookies with Identity</span></span>
    - <span data-ttu-id="ca561-121">替换`UseIdentity`与`UseAuthentication`中`Configure`方法：</span><span class="sxs-lookup"><span data-stu-id="ca561-121">Replace `UseIdentity` with `UseAuthentication` in the `Configure` method:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="ca561-122">调用`AddIdentity`中的方法`ConfigureServices`方法添加 cookie 身份验证服务。</span><span class="sxs-lookup"><span data-stu-id="ca561-122">Invoke the `AddIdentity` method in the `ConfigureServices` method to add the cookie authentication services.</span></span>
    - <span data-ttu-id="ca561-123">（可选） 调用`ConfigureApplicationCookie`或`ConfigureExternalCookie`中的方法`ConfigureServices`标识 cookie 设置进行调整的方法。</span><span class="sxs-lookup"><span data-stu-id="ca561-123">Optionally, invoke the `ConfigureApplicationCookie` or `ConfigureExternalCookie` method in the `ConfigureServices` method to tweak the Identity cookie settings.</span></span>

        ```csharp
        services.AddIdentity<ApplicationUser, IdentityRole>()
                .AddEntityFrameworkStores<ApplicationDbContext>()
                .AddDefaultTokenProviders();

        services.ConfigureApplicationCookie(options => options.LoginPath = "/Account/LogIn");
        ```

2. <span data-ttu-id="ca561-124">使用 cookie，而无需标识</span><span class="sxs-lookup"><span data-stu-id="ca561-124">Use cookies without Identity</span></span>
    - <span data-ttu-id="ca561-125">替换`UseCookieAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="ca561-125">Replace the `UseCookieAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

        ```csharp
        app.UseAuthentication();
        ```

    - <span data-ttu-id="ca561-126">调用`AddAuthentication`并`AddCookie`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="ca561-126">Invoke the `AddAuthentication` and `AddCookie` methods in the `ConfigureServices` method:</span></span>

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

### <a name="jwt-bearer-authentication"></a><span data-ttu-id="ca561-127">JWT 持有者身份验证</span><span class="sxs-lookup"><span data-stu-id="ca561-127">JWT Bearer Authentication</span></span>

<span data-ttu-id="ca561-128">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca561-128">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="ca561-129">替换`UseJwtBearerAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="ca561-129">Replace the `UseJwtBearerAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="ca561-130">调用`AddJwtBearer`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="ca561-130">Invoke the `AddJwtBearer` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddJwtBearer(options =>
            {
                options.Audience = "http://localhost:5001/";
                options.Authority = "http://localhost:5000/";
            });
    ```

    <span data-ttu-id="ca561-131">此代码片段不使用标识，因此应通过将传递设置的默认方案`JwtBearerDefaults.AuthenticationScheme`到`AddAuthentication`方法。</span><span class="sxs-lookup"><span data-stu-id="ca561-131">This code snippet doesn't use Identity, so the default scheme should be set by passing `JwtBearerDefaults.AuthenticationScheme` to the `AddAuthentication` method.</span></span>

### <a name="openid-connect-oidc-authentication"></a><span data-ttu-id="ca561-132">OpenID Connect (OIDC) 身份验证</span><span class="sxs-lookup"><span data-stu-id="ca561-132">OpenID Connect (OIDC) authentication</span></span>

<span data-ttu-id="ca561-133">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca561-133">Make the following changes in *Startup.cs*:</span></span>

- <span data-ttu-id="ca561-134">替换`UseOpenIdConnectAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="ca561-134">Replace the `UseOpenIdConnectAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="ca561-135">调用`AddOpenIdConnect`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="ca561-135">Invoke the `AddOpenIdConnect` method in the `ConfigureServices` method:</span></span>

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

### <a name="facebook-authentication"></a><span data-ttu-id="ca561-136">Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="ca561-136">Facebook authentication</span></span>

<span data-ttu-id="ca561-137">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca561-137">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="ca561-138">替换`UseFacebookAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="ca561-138">Replace the `UseFacebookAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="ca561-139">调用`AddFacebook`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="ca561-139">Invoke the `AddFacebook` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddFacebook(options =>
            {
                options.AppId = Configuration["auth:facebook:appid"];
                options.AppSecret = Configuration["auth:facebook:appsecret"];
            });
    ```

### <a name="google-authentication"></a><span data-ttu-id="ca561-140">Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="ca561-140">Google authentication</span></span>

<span data-ttu-id="ca561-141">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca561-141">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="ca561-142">替换`UseGoogleAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="ca561-142">Replace the `UseGoogleAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="ca561-143">调用`AddGoogle`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="ca561-143">Invoke the `AddGoogle` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddGoogle(options =>
            {
                options.ClientId = Configuration["auth:google:clientid"];
                options.ClientSecret = Configuration["auth:google:clientsecret"];
            });
    ```

### <a name="microsoft-account-authentication"></a><span data-ttu-id="ca561-144">Microsoft 帐户身份验证</span><span class="sxs-lookup"><span data-stu-id="ca561-144">Microsoft Account authentication</span></span>

<span data-ttu-id="ca561-145">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca561-145">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="ca561-146">替换`UseMicrosoftAccountAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="ca561-146">Replace the `UseMicrosoftAccountAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="ca561-147">调用`AddMicrosoftAccount`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="ca561-147">Invoke the `AddMicrosoftAccount` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddMicrosoftAccount(options =>
            {
                options.ClientId = Configuration["auth:microsoft:clientid"];
                options.ClientSecret = Configuration["auth:microsoft:clientsecret"];
            });
    ```

### <a name="twitter-authentication"></a><span data-ttu-id="ca561-148">Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="ca561-148">Twitter authentication</span></span>

<span data-ttu-id="ca561-149">进行以下更改中的*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca561-149">Make the following changes in *Startup.cs*:</span></span>
- <span data-ttu-id="ca561-150">替换`UseTwitterAuthentication`方法中调用`Configure`方法替换`UseAuthentication`:</span><span class="sxs-lookup"><span data-stu-id="ca561-150">Replace the `UseTwitterAuthentication` method call in the `Configure` method with `UseAuthentication`:</span></span>

    ```csharp
    app.UseAuthentication();
    ```

- <span data-ttu-id="ca561-151">调用`AddTwitter`中的方法`ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="ca561-151">Invoke the `AddTwitter` method in the `ConfigureServices` method:</span></span>

    ```csharp
    services.AddAuthentication()
            .AddTwitter(options =>
            {
                options.ConsumerKey = Configuration["auth:twitter:consumerkey"];
                options.ConsumerSecret = Configuration["auth:twitter:consumersecret"];
            });
    ```

### <a name="setting-default-authentication-schemes"></a><span data-ttu-id="ca561-152">设置默认身份验证方案</span><span class="sxs-lookup"><span data-stu-id="ca561-152">Setting default authentication schemes</span></span>

<span data-ttu-id="ca561-153">在 1.x 中，`AutomaticAuthenticate`并`AutomaticChallenge`的属性[AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1)基类用于设置单一身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="ca561-153">In 1.x, the `AutomaticAuthenticate` and `AutomaticChallenge` properties of the [AuthenticationOptions](/dotnet/api/Microsoft.AspNetCore.Builder.AuthenticationOptions?view=aspnetcore-1.1) base class were intended to be set on a single authentication scheme.</span></span> <span data-ttu-id="ca561-154">强制实施此没有很好方法。</span><span class="sxs-lookup"><span data-stu-id="ca561-154">There was no good way to enforce this.</span></span>

<span data-ttu-id="ca561-155">在 2.0 中，这两个属性已删除作为单个属性`AuthenticationOptions`实例。</span><span class="sxs-lookup"><span data-stu-id="ca561-155">In 2.0, these two properties have been removed as properties on the individual `AuthenticationOptions` instance.</span></span> <span data-ttu-id="ca561-156">它们可以在中配置`AddAuthentication`中的方法调用`ConfigureServices`方法*Startup.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca561-156">They can be configured in the `AddAuthentication` method call within the `ConfigureServices` method of *Startup.cs*:</span></span>

```csharp
services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme);
```

<span data-ttu-id="ca561-157">在上述代码段中，默认方案设置为`CookieAuthenticationDefaults.AuthenticationScheme`("Cookie")。</span><span class="sxs-lookup"><span data-stu-id="ca561-157">In the preceding code snippet, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme` ("Cookies").</span></span>

<span data-ttu-id="ca561-158">或者，使用的重载的版本`AddAuthentication`方法设置多个属性。</span><span class="sxs-lookup"><span data-stu-id="ca561-158">Alternatively, use an overloaded version of the `AddAuthentication` method to set more than one property.</span></span> <span data-ttu-id="ca561-159">在下面的重载的方法示例，默认方案设置为`CookieAuthenticationDefaults.AuthenticationScheme`。</span><span class="sxs-lookup"><span data-stu-id="ca561-159">In the following overloaded method example, the default scheme is set to `CookieAuthenticationDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="ca561-160">身份验证方案可能还可以指定在个人`[Authorize]`属性或授权策略。</span><span class="sxs-lookup"><span data-stu-id="ca561-160">The authentication scheme may alternatively be specified within your individual `[Authorize]` attributes or authorization policies.</span></span>

```csharp
services.AddAuthentication(options =>
{
    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
});
```

<span data-ttu-id="ca561-161">在 2.0 中定义默认方案，如果以下条件之一为 true:</span><span class="sxs-lookup"><span data-stu-id="ca561-161">Define a default scheme in 2.0 if one of the following conditions is true:</span></span>
- <span data-ttu-id="ca561-162">您希望用户以进行自动签名</span><span class="sxs-lookup"><span data-stu-id="ca561-162">You want the user to be automatically signed in</span></span>
- <span data-ttu-id="ca561-163">您使用`[Authorize]`而无需指定方案的属性或授权策略</span><span class="sxs-lookup"><span data-stu-id="ca561-163">You use the `[Authorize]` attribute or authorization policies without specifying schemes</span></span>

<span data-ttu-id="ca561-164">此规则的例外是`AddIdentity`方法。</span><span class="sxs-lookup"><span data-stu-id="ca561-164">An exception to this rule is the `AddIdentity` method.</span></span> <span data-ttu-id="ca561-165">此方法将为你和设置默认值进行身份验证并为应用程序 cookie 质询方案添加 cookie `IdentityConstants.ApplicationScheme`。</span><span class="sxs-lookup"><span data-stu-id="ca561-165">This method adds cookies for you and sets the default authenticate and challenge schemes to the application cookie `IdentityConstants.ApplicationScheme`.</span></span> <span data-ttu-id="ca561-166">此外，它将默认登录方案设置为外部 cookie `IdentityConstants.ExternalScheme`。</span><span class="sxs-lookup"><span data-stu-id="ca561-166">Additionally, it sets the default sign-in scheme to the external cookie `IdentityConstants.ExternalScheme`.</span></span>

<a name="obsolete-interface"></a>

## <a name="use-httpcontext-authentication-extensions"></a><span data-ttu-id="ca561-167">使用 HttpContext 身份验证扩展插件</span><span class="sxs-lookup"><span data-stu-id="ca561-167">Use HttpContext authentication extensions</span></span>

<span data-ttu-id="ca561-168">`IAuthenticationManager`接口是 1.x 身份验证系统的主入口点。</span><span class="sxs-lookup"><span data-stu-id="ca561-168">The `IAuthenticationManager` interface is the main entry point into the 1.x authentication system.</span></span> <span data-ttu-id="ca561-169">它已替换为一组新的`HttpContext`中的扩展方法`Microsoft.AspNetCore.Authentication`命名空间。</span><span class="sxs-lookup"><span data-stu-id="ca561-169">It has been replaced with a new set of `HttpContext` extension methods in the `Microsoft.AspNetCore.Authentication` namespace.</span></span>

<span data-ttu-id="ca561-170">例如，1.x 项目引用`Authentication`属性：</span><span class="sxs-lookup"><span data-stu-id="ca561-170">For example, 1.x projects reference an `Authentication` property:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<span data-ttu-id="ca561-171">在 2.0 项目中，导入`Microsoft.AspNetCore.Authentication`命名空间，并删除`Authentication`属性引用：</span><span class="sxs-lookup"><span data-stu-id="ca561-171">In 2.0 projects, import the `Microsoft.AspNetCore.Authentication` namespace, and delete the `Authentication` property references:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="windows-auth-changes"></a>

## <a name="windows-authentication-httpsys--iisintegration"></a><span data-ttu-id="ca561-172">Windows 身份验证 (HTTP.sys / IISIntegration)</span><span class="sxs-lookup"><span data-stu-id="ca561-172">Windows Authentication (HTTP.sys / IISIntegration)</span></span>

<span data-ttu-id="ca561-173">有两种变体的 Windows 身份验证：</span><span class="sxs-lookup"><span data-stu-id="ca561-173">There are two variations of Windows authentication:</span></span>
1. <span data-ttu-id="ca561-174">主机只允许经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="ca561-174">The host only allows authenticated users</span></span>
2. <span data-ttu-id="ca561-175">主机允许同时匿名和身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="ca561-175">The host allows both anonymous and authenticated users</span></span>

<span data-ttu-id="ca561-176">上面所述的第一种变化形式是不受 2.0 更改的影响。</span><span class="sxs-lookup"><span data-stu-id="ca561-176">The first variation described above is unaffected by the 2.0 changes.</span></span>

<span data-ttu-id="ca561-177">2.0 更改的情况下，会影响上面所述的第二个变体。</span><span class="sxs-lookup"><span data-stu-id="ca561-177">The second variation described above is affected by the 2.0 changes.</span></span> <span data-ttu-id="ca561-178">例如，你可能会将以允许匿名用户到你的应用在 IIS 或[HTTP.sys](xref:fundamentals/servers/httpsys)层在控制器级别但授权用户。</span><span class="sxs-lookup"><span data-stu-id="ca561-178">As an example, you may be allowing anonymous users into your app at the IIS or [HTTP.sys](xref:fundamentals/servers/httpsys) layer but authorizing users at the Controller level.</span></span> <span data-ttu-id="ca561-179">在此方案中，将默认方案设置为`IISDefaults.AuthenticationScheme`在`Startup.ConfigureServices`方法：</span><span class="sxs-lookup"><span data-stu-id="ca561-179">In this scenario, set the default scheme to `IISDefaults.AuthenticationScheme` in the `Startup.ConfigureServices` method:</span></span>

```csharp
services.AddAuthentication(IISDefaults.AuthenticationScheme);
```

<span data-ttu-id="ca561-180">相应地设置默认方案失败会阻止在授权请求，以来自工作的挑战。</span><span class="sxs-lookup"><span data-stu-id="ca561-180">Failure to set the default scheme accordingly prevents the authorize request to challenge from working.</span></span>

<a name="identity-cookie-options"></a>

## <a name="identitycookieoptions-instances"></a><span data-ttu-id="ca561-181">IdentityCookieOptions 实例</span><span class="sxs-lookup"><span data-stu-id="ca561-181">IdentityCookieOptions instances</span></span>

<span data-ttu-id="ca561-182">2.0 更改一个副作用是切换到使用名为选项而不是 cookie 选项实例。</span><span class="sxs-lookup"><span data-stu-id="ca561-182">A side effect of the 2.0 changes is the switch to using named options instead of cookie options instances.</span></span> <span data-ttu-id="ca561-183">删除自定义标识 cookie 方案名称的功能。</span><span class="sxs-lookup"><span data-stu-id="ca561-183">The ability to customize the Identity cookie scheme names is removed.</span></span>

<span data-ttu-id="ca561-184">例如，1.x 项目使用[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)传递`IdentityCookieOptions`到参数*AccountController.cs*。</span><span class="sxs-lookup"><span data-stu-id="ca561-184">For example, 1.x projects use [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to pass an `IdentityCookieOptions` parameter into *AccountController.cs*.</span></span> <span data-ttu-id="ca561-185">从提供的实例访问的外部 cookie 身份验证方案：</span><span class="sxs-lookup"><span data-stu-id="ca561-185">The external cookie authentication scheme is accessed from the provided instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor&highlight=4,11)]

<span data-ttu-id="ca561-186">前面提到的构造函数注入将成为在 2.0 项目中，不必要和`_externalCookieScheme`删除字段，可以：</span><span class="sxs-lookup"><span data-stu-id="ca561-186">The aforementioned constructor injection becomes unnecessary in 2.0 projects, and the `_externalCookieScheme` field can be deleted:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AccountControllerConstructor)]

<span data-ttu-id="ca561-187">`IdentityConstants.ExternalScheme`可以直接使用常量：</span><span class="sxs-lookup"><span data-stu-id="ca561-187">The `IdentityConstants.ExternalScheme` constant can be used directly:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/AccountController.cs?name=snippet_AuthenticationProperty)]

<a name="navigation-properties"></a>

## <a name="add-identityuser-poco-navigation-properties"></a><span data-ttu-id="ca561-188">添加 POCO IdentityUser 导航属性</span><span class="sxs-lookup"><span data-stu-id="ca561-188">Add IdentityUser POCO navigation properties</span></span>

<span data-ttu-id="ca561-189">Entity Framework (EF) Core 导航属性的基本`IdentityUser`POCO （普通旧 CLR 对象） 已被删除。</span><span class="sxs-lookup"><span data-stu-id="ca561-189">The Entity Framework (EF) Core navigation properties of the base `IdentityUser` POCO (Plain Old CLR Object) have been removed.</span></span> <span data-ttu-id="ca561-190">如果在 1.x 项目使用这些属性，手动将它们添加回 2.0 项目：</span><span class="sxs-lookup"><span data-stu-id="ca561-190">If your 1.x project used these properties, manually add them back to the 2.0 project:</span></span>

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

<span data-ttu-id="ca561-191">若要防止重复的外键，运行 EF Core 迁移时，将以下代码添加到你`IdentityDbContext`类的`OnModelCreating`方法 (后`base.OnModelCreating();`调用):</span><span class="sxs-lookup"><span data-stu-id="ca561-191">To prevent duplicate foreign keys when running EF Core Migrations, add the following to your `IdentityDbContext` class' `OnModelCreating` method (after the `base.OnModelCreating();` call):</span></span>

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

## <a name="replace-getexternalauthenticationschemes"></a><span data-ttu-id="ca561-192">替换为 GetExternalAuthenticationSchemes</span><span class="sxs-lookup"><span data-stu-id="ca561-192">Replace GetExternalAuthenticationSchemes</span></span>

<span data-ttu-id="ca561-193">同步方法`GetExternalAuthenticationSchemes`的异步版本，于是取消了。</span><span class="sxs-lookup"><span data-stu-id="ca561-193">The synchronous method `GetExternalAuthenticationSchemes` was removed in favor of an asynchronous version.</span></span> <span data-ttu-id="ca561-194">在 1.x 项目具有以下代码*ManageController.cs*:</span><span class="sxs-lookup"><span data-stu-id="ca561-194">1.x projects have the following code in *ManageController.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemes)]

<span data-ttu-id="ca561-195">此方法将出现在*Login.cshtml*过：</span><span class="sxs-lookup"><span data-stu-id="ca561-195">This method appears in *Login.cshtml* too:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Account/Login.cshtml?range=62,75-84)]

<span data-ttu-id="ca561-196">在 2.0 项目中，使用`GetExternalAuthenticationSchemesAsync`方法：</span><span class="sxs-lookup"><span data-stu-id="ca561-196">In 2.0 projects, use the `GetExternalAuthenticationSchemesAsync` method:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Controllers/ManageController.cs?name=snippet_GetExternalAuthenticationSchemesAsync)]

<span data-ttu-id="ca561-197">在中*Login.cshtml*，则`AuthenticationScheme`中访问属性`foreach`循环更改为`Name`:</span><span class="sxs-lookup"><span data-stu-id="ca561-197">In *Login.cshtml*, the `AuthenticationScheme` property accessed in the `foreach` loop changes to `Name`:</span></span>

[!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Views/Account/Login.cshtml?range=62,75-84)]

<a name="property-change"></a>

## <a name="manageloginsviewmodel-property-change"></a><span data-ttu-id="ca561-198">ManageLoginsViewModel 属性更改</span><span class="sxs-lookup"><span data-stu-id="ca561-198">ManageLoginsViewModel property change</span></span>

<span data-ttu-id="ca561-199">一个`ManageLoginsViewModel`中使用对象`ManageLogins`的操作*ManageController.cs*。</span><span class="sxs-lookup"><span data-stu-id="ca561-199">A `ManageLoginsViewModel` object is used in the `ManageLogins` action of *ManageController.cs*.</span></span> <span data-ttu-id="ca561-200">在 1.x 项目中，该对象的`OtherLogins`属性的返回类型是`IList<AuthenticationDescription>`。</span><span class="sxs-lookup"><span data-stu-id="ca561-200">In 1.x projects, the object's `OtherLogins` property return type is `IList<AuthenticationDescription>`.</span></span> <span data-ttu-id="ca561-201">此返回类型时需要导入`Microsoft.AspNetCore.Http.Authentication`:</span><span class="sxs-lookup"><span data-stu-id="ca561-201">This return type requires an import of `Microsoft.AspNetCore.Http.Authentication`:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<span data-ttu-id="ca561-202">在 2.0 项目中，返回类型更改为`IList<AuthenticationScheme>`。</span><span class="sxs-lookup"><span data-stu-id="ca561-202">In 2.0 projects, the return type changes to `IList<AuthenticationScheme>`.</span></span> <span data-ttu-id="ca561-203">此新的返回类型需要替换`Microsoft.AspNetCore.Http.Authentication`导入具有`Microsoft.AspNetCore.Authentication`导入。</span><span class="sxs-lookup"><span data-stu-id="ca561-203">This new return type requires replacing the `Microsoft.AspNetCore.Http.Authentication` import with a `Microsoft.AspNetCore.Authentication` import.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Models/ManageViewModels/ManageLoginsViewModel.cs?name=snippet_ManageLoginsViewModel&highlight=2,11)]

<a name="additional-resources"></a>

## <a name="additional-resources"></a><span data-ttu-id="ca561-204">其他资源</span><span class="sxs-lookup"><span data-stu-id="ca561-204">Additional resources</span></span>

<span data-ttu-id="ca561-205">有关更多详细信息和讨论，请参阅[讨论身份验证 2.0](https://github.com/aspnet/Security/issues/1338) GitHub 上的问题。</span><span class="sxs-lookup"><span data-stu-id="ca561-205">For additional details and discussion, see the [Discussion for Auth 2.0](https://github.com/aspnet/Security/issues/1338) issue on GitHub.</span></span>
