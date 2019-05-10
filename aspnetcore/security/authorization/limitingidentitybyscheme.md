---
title: 使用 ASP.NET Core 中的特定方案授权
author: rick-anderson
description: 本文介绍如何使用多个身份验证方法时限制到特定方案的标识。
ms.author: riande
ms.date: 10/22/2018
uid: security/authorization/limitingidentitybyscheme
ms.openlocfilehash: 778bb61f472ab2e76f85da5999d3c79238188f19
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64897334"
---
# <a name="authorize-with-a-specific-scheme-in-aspnet-core"></a><span data-ttu-id="50b33-103">使用 ASP.NET Core 中的特定方案授权</span><span class="sxs-lookup"><span data-stu-id="50b33-103">Authorize with a specific scheme in ASP.NET Core</span></span>

<span data-ttu-id="50b33-104">在某些情况下，如单页面应用程序 (Spa) 是通常使用多个身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="50b33-104">In some scenarios, such as Single Page Applications (SPAs), it's common to use multiple authentication methods.</span></span> <span data-ttu-id="50b33-105">例如，应用可能会使用基于 cookie 的身份验证登录和 JWT 持有者身份验证的 JavaScript 请求。</span><span class="sxs-lookup"><span data-stu-id="50b33-105">For example, the app may use cookie-based authentication to log in and JWT bearer authentication for JavaScript requests.</span></span> <span data-ttu-id="50b33-106">在某些情况下，应用程序可能具有的身份验证处理程序的多个实例。</span><span class="sxs-lookup"><span data-stu-id="50b33-106">In some cases, the app may have multiple instances of an authentication handler.</span></span> <span data-ttu-id="50b33-107">例如，其中一个包含基本身份标识的两个 cookie 处理程序，另一个时创建已触发多重身份验证 (MFA)。</span><span class="sxs-lookup"><span data-stu-id="50b33-107">For example, two cookie handlers where one contains a basic identity and one is created when a multi-factor authentication (MFA) has been triggered.</span></span> <span data-ttu-id="50b33-108">用户请求需要额外的安全性的操作，因此，可能会触发 MFA。</span><span class="sxs-lookup"><span data-stu-id="50b33-108">MFA may be triggered because the user requested an operation that requires extra security.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="50b33-109">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="50b33-109">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

<span data-ttu-id="50b33-110">在身份验证过程中配置身份验证服务时，名为身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="50b33-110">An authentication scheme is named when the authentication service is configured during authentication.</span></span> <span data-ttu-id="50b33-111">例如：</span><span class="sxs-lookup"><span data-stu-id="50b33-111">For example:</span></span>

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

<span data-ttu-id="50b33-112">已在前面的代码中，添加两个身份验证处理程序： 一个用于 cookie，一个为持有者。</span><span class="sxs-lookup"><span data-stu-id="50b33-112">In the preceding code, two authentication handlers have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="50b33-113">指定的默认方案会导致`HttpContext.User`属性设置为该标识。</span><span class="sxs-lookup"><span data-stu-id="50b33-113">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="50b33-114">如果不需要该行为，请禁用调用的无参数形式`AddAuthentication`。</span><span class="sxs-lookup"><span data-stu-id="50b33-114">If that behavior isn't desired, disable it by invoking the parameterless form of `AddAuthentication`.</span></span>

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="50b33-115">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="50b33-115">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

<span data-ttu-id="50b33-116">在身份验证过程配置身份验证中间件时，被命名为身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="50b33-116">Authentication schemes are named when authentication middlewares are configured during authentication.</span></span> <span data-ttu-id="50b33-117">例如：</span><span class="sxs-lookup"><span data-stu-id="50b33-117">For example:</span></span>

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

<span data-ttu-id="50b33-118">已在前面的代码中，添加两个身份验证中间件： 分别用于 cookie 和持有者。</span><span class="sxs-lookup"><span data-stu-id="50b33-118">In the preceding code, two authentication middlewares have been added: one for cookies and one for bearer.</span></span>

>[!NOTE]
><span data-ttu-id="50b33-119">指定的默认方案会导致`HttpContext.User`属性设置为该标识。</span><span class="sxs-lookup"><span data-stu-id="50b33-119">Specifying the default scheme results in the `HttpContext.User` property being set to that identity.</span></span> <span data-ttu-id="50b33-120">如果不需要该行为，它通过设置来禁用`AuthenticationOptions.AutomaticAuthenticate`属性设置为`false`。</span><span class="sxs-lookup"><span data-stu-id="50b33-120">If that behavior isn't desired, disable it by setting the `AuthenticationOptions.AutomaticAuthenticate` property to `false`.</span></span>

---

## <a name="selecting-the-scheme-with-the-authorize-attribute"></a><span data-ttu-id="50b33-121">选择具有 Authorize 属性的使用方案</span><span class="sxs-lookup"><span data-stu-id="50b33-121">Selecting the scheme with the Authorize attribute</span></span>

<span data-ttu-id="50b33-122">在授权，终端应用指示要使用的处理程序。</span><span class="sxs-lookup"><span data-stu-id="50b33-122">At the point of authorization, the app indicates the handler to be used.</span></span> <span data-ttu-id="50b33-123">选择与应用程序将授权通过将传递到身份验证方案的以逗号分隔列表的处理程序`[Authorize]`。</span><span class="sxs-lookup"><span data-stu-id="50b33-123">Select the handler with which the app will authorize by passing a comma-delimited list of authentication schemes to `[Authorize]`.</span></span> <span data-ttu-id="50b33-124">`[Authorize]`特性指定身份验证方案或方案使用而不考虑是否配置默认值。</span><span class="sxs-lookup"><span data-stu-id="50b33-124">The `[Authorize]` attribute specifies the authentication scheme or schemes to use regardless of whether a default is configured.</span></span> <span data-ttu-id="50b33-125">例如：</span><span class="sxs-lookup"><span data-stu-id="50b33-125">For example:</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="50b33-126">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="50b33-126">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

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

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="50b33-127">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="50b33-127">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

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

<span data-ttu-id="50b33-128">在前面的示例中，cookie 和持有者处理程序运行，并有机会创建并追加当前用户的标识。</span><span class="sxs-lookup"><span data-stu-id="50b33-128">In the preceding example, both the cookie and bearer handlers run and have a chance to create and append an identity for the current user.</span></span> <span data-ttu-id="50b33-129">通过指定一种方案，在运行相应的处理程序。</span><span class="sxs-lookup"><span data-stu-id="50b33-129">By specifying a single scheme only, the corresponding handler runs.</span></span>

# <a name="aspnet-core-2xtabaspnetcore2x"></a>[<span data-ttu-id="50b33-130">ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="50b33-130">ASP.NET Core 2.x</span></span>](#tab/aspnetcore2x)

```csharp
[Authorize(AuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

# <a name="aspnet-core-1xtabaspnetcore1x"></a>[<span data-ttu-id="50b33-131">ASP.NET Core 1.x</span><span class="sxs-lookup"><span data-stu-id="50b33-131">ASP.NET Core 1.x</span></span>](#tab/aspnetcore1x)

```csharp
[Authorize(ActiveAuthenticationSchemes = 
    JwtBearerDefaults.AuthenticationScheme)]
public class MixedController : Controller
```

---

<span data-ttu-id="50b33-132">在上述代码中，仅具有"Bearer"方案处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="50b33-132">In the preceding code, only the handler with the "Bearer" scheme runs.</span></span> <span data-ttu-id="50b33-133">任何基于 cookie 的标识将被忽略。</span><span class="sxs-lookup"><span data-stu-id="50b33-133">Any cookie-based identities are ignored.</span></span>

## <a name="selecting-the-scheme-with-policies"></a><span data-ttu-id="50b33-134">选择使用策略的方案</span><span class="sxs-lookup"><span data-stu-id="50b33-134">Selecting the scheme with policies</span></span>

<span data-ttu-id="50b33-135">如果想要指定在所需的方案[策略](xref:security/authorization/policies)，可以设置`AuthenticationSchemes`集合添加你的策略时：</span><span class="sxs-lookup"><span data-stu-id="50b33-135">If you prefer to specify the desired schemes in [policy](xref:security/authorization/policies), you can set the `AuthenticationSchemes` collection when adding your policy:</span></span>

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

<span data-ttu-id="50b33-136">在上述示例中，"Over18"策略只在运行针对"Bearer"处理程序创建的标识。</span><span class="sxs-lookup"><span data-stu-id="50b33-136">In the preceding example, the "Over18" policy only runs against the identity created by the "Bearer" handler.</span></span> <span data-ttu-id="50b33-137">通过设置使用策略`[Authorize]`特性的`Policy`属性：</span><span class="sxs-lookup"><span data-stu-id="50b33-137">Use the policy by setting the `[Authorize]` attribute's `Policy` property:</span></span>

```csharp
[Authorize(Policy = "Over18")]
public class RegistrationController : Controller
```

::: moniker range=">= aspnetcore-2.0"

## <a name="use-multiple-authentication-schemes"></a><span data-ttu-id="50b33-138">使用多个身份验证方案</span><span class="sxs-lookup"><span data-stu-id="50b33-138">Use multiple authentication schemes</span></span>

<span data-ttu-id="50b33-139">某些应用可能需要支持多个类型的身份验证。</span><span class="sxs-lookup"><span data-stu-id="50b33-139">Some apps may need to support multiple types of authentication.</span></span> <span data-ttu-id="50b33-140">例如，您的应用程序可能会对用户从 Azure Active Directory 和用户数据库进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="50b33-140">For example, your app might authenticate users from Azure Active Directory and from a users database.</span></span> <span data-ttu-id="50b33-141">另一个示例是一个应用，用户从 Active Directory 联合身份验证服务和 Azure Active Directory B2C 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="50b33-141">Another example is an app that authenticates users from both Active Directory Federation Services and Azure Active Directory B2C.</span></span> <span data-ttu-id="50b33-142">在这种情况下，应用程序应接受来自多个颁发者的 JWT 持有者令牌。</span><span class="sxs-lookup"><span data-stu-id="50b33-142">In this case, the app should accept a JWT bearer token from several issuers.</span></span>

<span data-ttu-id="50b33-143">添加你想要接受的所有身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="50b33-143">Add all authentication schemes you'd like to accept.</span></span> <span data-ttu-id="50b33-144">例如，以下代码中`Startup.ConfigureServices`添加两个 JWT 持有者身份验证方案使用不同的颁发者：</span><span class="sxs-lookup"><span data-stu-id="50b33-144">For example, the following code in `Startup.ConfigureServices` adds two JWT bearer authentication schemes with different issuers:</span></span>

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
> <span data-ttu-id="50b33-145">只有一个 JWT 持有者身份验证注册的默认身份验证方案使用`JwtBearerDefaults.AuthenticationScheme`。</span><span class="sxs-lookup"><span data-stu-id="50b33-145">Only one JWT bearer authentication is registered with the default authentication scheme `JwtBearerDefaults.AuthenticationScheme`.</span></span> <span data-ttu-id="50b33-146">其他身份验证必须是唯一的身份验证方案使用其注册。</span><span class="sxs-lookup"><span data-stu-id="50b33-146">Additional authentication has to be registered with a unique authentication scheme.</span></span>

<span data-ttu-id="50b33-147">下一步是更新默认授权策略，以接受这两种身份验证方案。</span><span class="sxs-lookup"><span data-stu-id="50b33-147">The next step is to update the default authorization policy to accept both authentication schemes.</span></span> <span data-ttu-id="50b33-148">例如：</span><span class="sxs-lookup"><span data-stu-id="50b33-148">For example:</span></span>

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

<span data-ttu-id="50b33-149">重写默认授权策略，因为它是可以使用`[Authorize]`控制器中的属性。</span><span class="sxs-lookup"><span data-stu-id="50b33-149">As the default authorization policy is overridden, it's possible to use the `[Authorize]` attribute in controllers.</span></span> <span data-ttu-id="50b33-150">然后，该控制器使用由第一个或第二个颁发者颁发的 JWT 接受请求。</span><span class="sxs-lookup"><span data-stu-id="50b33-150">The controller then accepts requests with JWT issued by the first or second issuer.</span></span>

::: moniker-end
