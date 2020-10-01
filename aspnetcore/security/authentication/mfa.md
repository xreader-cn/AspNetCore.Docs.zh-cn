---
title: ASP.NET Core 中的多重身份验证
author: damienbod
description: 了解如何在 ASP.NET Core 应用中 (MFA) 设置多重身份验证。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/mfa
ms.openlocfilehash: 76a11aa7b89b3ce60ed11bd7553a7e5898f661f4
ms.sourcegitcommit: d1a897ebd89daa05170ac448e4831d327f6b21a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91606794"
---
# <a name="multi-factor-authentication-in-aspnet-core"></a><span data-ttu-id="fa779-103">ASP.NET Core 中的多重身份验证</span><span class="sxs-lookup"><span data-stu-id="fa779-103">Multi-factor authentication in ASP.NET Core</span></span>

<span data-ttu-id="fa779-104">作者： [Damien Bowden](https://github.com/damienbod)</span><span class="sxs-lookup"><span data-stu-id="fa779-104">By [Damien Bowden](https://github.com/damienbod)</span></span>

[<span data-ttu-id="fa779-105"> (damienbod/AspNetCoreHybridFlowWithApi GitHub 存储库查看或下载示例代码) </span><span class="sxs-lookup"><span data-stu-id="fa779-105">View or download sample code (damienbod/AspNetCoreHybridFlowWithApi GitHub repository)</span></span>](https://github.com/damienbod/AspNetCoreHybridFlowWithApi)

<span data-ttu-id="fa779-106">多重身份验证 (MFA) 是在登录事件期间请求用户执行其他形式的标识的过程。</span><span class="sxs-lookup"><span data-stu-id="fa779-106">Multi-factor authentication (MFA) is a process in which a user is requested during a sign-in event for additional forms of identification.</span></span> <span data-ttu-id="fa779-107">此提示可以是输入手机中的代码，使用 FIDO2 键，或提供指纹扫描。</span><span class="sxs-lookup"><span data-stu-id="fa779-107">This prompt could be to enter a code from a cellphone, use a FIDO2 key, or to provide a fingerprint scan.</span></span> <span data-ttu-id="fa779-108">当你需要另一种形式的身份验证时，安全性得到了增强。</span><span class="sxs-lookup"><span data-stu-id="fa779-108">When you require a second form of authentication, security is enhanced.</span></span> <span data-ttu-id="fa779-109">攻击者无法轻松获取或复制额外的因素。</span><span class="sxs-lookup"><span data-stu-id="fa779-109">The additional factor isn't easily obtained or duplicated by an attacker.</span></span>

<span data-ttu-id="fa779-110">本文涵盖以下几个方面：</span><span class="sxs-lookup"><span data-stu-id="fa779-110">This article covers the following areas:</span></span>

* <span data-ttu-id="fa779-111">什么是 MFA 以及建议使用哪些 MFA 流</span><span class="sxs-lookup"><span data-stu-id="fa779-111">What is MFA and what MFA flows are recommended</span></span>
* <span data-ttu-id="fa779-112">使用为管理页配置 MFA ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="fa779-112">Configure MFA for administration pages using ASP.NET Core Identity</span></span>
* <span data-ttu-id="fa779-113">将 MFA 登录要求发送到 OpenID Connect 服务器</span><span class="sxs-lookup"><span data-stu-id="fa779-113">Send MFA sign-in requirement to OpenID Connect server</span></span>
* <span data-ttu-id="fa779-114">强制 ASP.NET Core OpenID Connect 客户端要求 MFA</span><span class="sxs-lookup"><span data-stu-id="fa779-114">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

## <a name="mfa-2fa"></a><span data-ttu-id="fa779-115">MFA，2FA</span><span class="sxs-lookup"><span data-stu-id="fa779-115">MFA, 2FA</span></span>

<span data-ttu-id="fa779-116">MFA 至少需要两种或更多类型的身份验证，如你知道的东西、你拥有的内容或对用户进行身份验证的生物识别验证。</span><span class="sxs-lookup"><span data-stu-id="fa779-116">MFA requires at least two or more types of proof for an identity like something you know, something you possess, or biometric validation for the user to authenticate.</span></span>

<span data-ttu-id="fa779-117">双因素身份验证 (2FA) 类似于 MFA 的子集，但不同之处在于，MFA 可能需要两个或多个因素来证明身份。</span><span class="sxs-lookup"><span data-stu-id="fa779-117">Two-factor authentication (2FA) is like a subset of MFA, but the difference being that MFA can require two or more factors to prove the identity.</span></span>

### <a name="mfa-totp-time-based-one-time-password-algorithm"></a><span data-ttu-id="fa779-118">MFA TOTP (基于时间的一次性密码算法) </span><span class="sxs-lookup"><span data-stu-id="fa779-118">MFA TOTP (Time-based One-time Password Algorithm)</span></span>

<span data-ttu-id="fa779-119">使用 TOTP 的 MFA 是受支持的实现 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="fa779-119">MFA using TOTP is a supported implementation using ASP.NET Core Identity.</span></span> <span data-ttu-id="fa779-120">这可以与任何兼容的验证器应用一起使用，包括：</span><span class="sxs-lookup"><span data-stu-id="fa779-120">This can be used together with any compliant authenticator app, including:</span></span>

* <span data-ttu-id="fa779-121">Microsoft Authenticator 应用</span><span class="sxs-lookup"><span data-stu-id="fa779-121">Microsoft Authenticator App</span></span>
* <span data-ttu-id="fa779-122">Google 验证器应用</span><span class="sxs-lookup"><span data-stu-id="fa779-122">Google Authenticator App</span></span>

<span data-ttu-id="fa779-123">有关实现的详细信息，请参阅以下链接：</span><span class="sxs-lookup"><span data-stu-id="fa779-123">See the following link for implementation details:</span></span>

[<span data-ttu-id="fa779-124">为 ASP.NET Core 中的 TOTP 验证器应用启用 QR 代码生成</span><span class="sxs-lookup"><span data-stu-id="fa779-124">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)

### <a name="mfa-fido2-or-passwordless"></a><span data-ttu-id="fa779-125">MFA FIDO2 或无密码</span><span class="sxs-lookup"><span data-stu-id="fa779-125">MFA FIDO2 or passwordless</span></span>

<span data-ttu-id="fa779-126">FIDO2 目前：</span><span class="sxs-lookup"><span data-stu-id="fa779-126">FIDO2 is currently:</span></span>

* <span data-ttu-id="fa779-127">实现 MFA 的最安全方法。</span><span class="sxs-lookup"><span data-stu-id="fa779-127">The most secure way of achieving MFA.</span></span>
* <span data-ttu-id="fa779-128">唯一防止仿冒攻击的 MFA 流。</span><span class="sxs-lookup"><span data-stu-id="fa779-128">The only MFA flow that protects against phishing attacks.</span></span>

<span data-ttu-id="fa779-129">目前，ASP.NET Core 不能直接支持 FIDO2。</span><span class="sxs-lookup"><span data-stu-id="fa779-129">At present, ASP.NET Core doesn't support FIDO2 directly.</span></span> <span data-ttu-id="fa779-130">FIDO2 可用于 MFA 或无密码流。</span><span class="sxs-lookup"><span data-stu-id="fa779-130">FIDO2 can be used for MFA or passwordless flows.</span></span>

<span data-ttu-id="fa779-131">Azure Active Directory 提供对 FIDO2 和无密码流的支持。</span><span class="sxs-lookup"><span data-stu-id="fa779-131">Azure Active Directory provides support for FIDO2 and passwordless flows.</span></span> <span data-ttu-id="fa779-132">有关详细信息，请参阅 [无密码 authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless)。</span><span class="sxs-lookup"><span data-stu-id="fa779-132">For more information, see [Passwordless authentication options for Azure Active Directory](/azure/active-directory/authentication/concept-authentication-passwordless).</span></span>

### <a name="mfa-sms"></a><span data-ttu-id="fa779-133">MFA 短信</span><span class="sxs-lookup"><span data-stu-id="fa779-133">MFA SMS</span></span>

<span data-ttu-id="fa779-134">与密码身份验证相比，具有短信的 MFA 增加了高度的安全性 (单个因素) 。</span><span class="sxs-lookup"><span data-stu-id="fa779-134">MFA with SMS increases security massively compared with password authentication (single factor).</span></span> <span data-ttu-id="fa779-135">但是，不再建议使用短信作为第二个因素。</span><span class="sxs-lookup"><span data-stu-id="fa779-135">However, using SMS as a second factor is no longer recommended.</span></span> <span data-ttu-id="fa779-136">此类型的实现存在太多已知攻击媒介。</span><span class="sxs-lookup"><span data-stu-id="fa779-136">Too many known attack vectors exist for this type of implementation.</span></span>

[<span data-ttu-id="fa779-137">NIST 指导原则</span><span class="sxs-lookup"><span data-stu-id="fa779-137">NIST guidelines</span></span>](https://pages.nist.gov/800-63-3/sp800-63b.html)

## <a name="configure-mfa-for-administration-pages-using-no-locaspnet-core-identity"></a><span data-ttu-id="fa779-138">使用为管理页配置 MFA ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="fa779-138">Configure MFA for administration pages using ASP.NET Core Identity</span></span>

<span data-ttu-id="fa779-139">可以强制用户在应用中访问敏感页面 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="fa779-139">MFA could be forced on users to access sensitive pages within an ASP.NET Core Identity app.</span></span> <span data-ttu-id="fa779-140">对于不同标识存在不同级别访问权限的应用，这可能很有用。</span><span class="sxs-lookup"><span data-stu-id="fa779-140">This could be useful for apps where different levels of access exist for the different identities.</span></span> <span data-ttu-id="fa779-141">例如，用户可以使用密码登录名查看配置文件数据，但管理员需要使用 MFA 来访问管理页面。</span><span class="sxs-lookup"><span data-stu-id="fa779-141">For example, users might be able to view the profile data using a password login, but an administrator would be required to use MFA to access the administrative pages.</span></span>

### <a name="extend-the-login-with-an-mfa-claim"></a><span data-ttu-id="fa779-142">使用 MFA 声明扩展登录名</span><span class="sxs-lookup"><span data-stu-id="fa779-142">Extend the login with an MFA claim</span></span>

<span data-ttu-id="fa779-143">演示代码是使用和页 ASP.NET Core 设置 Identity 的 Razor 。</span><span class="sxs-lookup"><span data-stu-id="fa779-143">The demo code is setup using ASP.NET Core with Identity and Razor Pages.</span></span> <span data-ttu-id="fa779-144">`AddIdentity`使用方法而不是 `AddDefaultIdentity` 一个方法，因此，在 `IUserClaimsPrincipalFactory` 成功登录后，可以使用实现将声明添加到标识。</span><span class="sxs-lookup"><span data-stu-id="fa779-144">The `AddIdentity` method is used instead of `AddDefaultIdentity` one, so an `IUserClaimsPrincipalFactory` implementation can be used to add claims to the identity after a successful login.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(
            Configuration.GetConnectionString("DefaultConnection")));
    
    services.AddIdentity<IdentityUser, IdentityRole>(
            options => options.SignIn.RequireConfirmedAccount = false)
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddSingleton<IEmailSender, EmailSender>();
    services.AddScoped<IUserClaimsPrincipalFactory<IdentityUser>, 
        AdditionalUserClaimsPrincipalFactory>();

    services.AddAuthorization(options =>
        options.AddPolicy("TwoFactorEnabled",
            x => x.RequireClaim("amr", "mfa")));

    services.AddRazorPages();
}
```

<span data-ttu-id="fa779-145">`AdditionalUserClaimsPrincipalFactory`类 `amr` 仅在成功登录后将声明添加到用户声明。</span><span class="sxs-lookup"><span data-stu-id="fa779-145">The `AdditionalUserClaimsPrincipalFactory` class adds the `amr` claim to the user claims only after a successful login.</span></span> <span data-ttu-id="fa779-146">将从数据库中读取声明的值。</span><span class="sxs-lookup"><span data-stu-id="fa779-146">The claim's value is read from the database.</span></span> <span data-ttu-id="fa779-147">此处添加了声明，因为如果该标识已使用 MFA 登录，则该用户只应访问受保护的视图。</span><span class="sxs-lookup"><span data-stu-id="fa779-147">The claim is added here because the user should only access the higher protected view if the identity has logged in with MFA.</span></span> <span data-ttu-id="fa779-148">如果直接从数据库中读取数据库视图而不是使用声明，则在激活 MFA 后，可以直接访问该视图，而无需进行 MFA。</span><span class="sxs-lookup"><span data-stu-id="fa779-148">If the database view is read from the database directly instead of using the claim, it's possible to access the view without MFA directly after activating the MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Identity;
using Microsoft.Extensions.Options;
using System.Collections.Generic;
using System.Security.Claims;
using System.Threading.Tasks;

namespace IdentityStandaloneMfa
{
    public class AdditionalUserClaimsPrincipalFactory : 
        UserClaimsPrincipalFactory<IdentityUser, IdentityRole>
    {
        public AdditionalUserClaimsPrincipalFactory( 
            UserManager<IdentityUser> userManager,
            RoleManager<IdentityRole> roleManager, 
            IOptions<IdentityOptions> optionsAccessor) 
            : base(userManager, roleManager, optionsAccessor)
        {
        }

        public async override Task<ClaimsPrincipal> CreateAsync(IdentityUser user)
        {
            var principal = await base.CreateAsync(user);
            var identity = (ClaimsIdentity)principal.Identity;

            var claims = new List<Claim>();

            if (user.TwoFactorEnabled)
            {
                claims.Add(new Claim("amr", "mfa"));
            }
            else
            {
                claims.Add(new Claim("amr", "pwd"));
            }

            identity.AddClaims(claims);
            return principal;
        }
    }
}
```

<span data-ttu-id="fa779-149">由于 Identity 服务设置在类中发生了更改 `Startup` ， Identity 需要更新的布局。</span><span class="sxs-lookup"><span data-stu-id="fa779-149">Because the Identity service setup changed in the `Startup` class, the layouts of the Identity need to be updated.</span></span> <span data-ttu-id="fa779-150">将 Identity 页面基架到应用。</span><span class="sxs-lookup"><span data-stu-id="fa779-150">Scaffold the Identity pages into the app.</span></span> <span data-ttu-id="fa779-151">在\* Identity /Account/Manage/_Layout cshtml\*文件中定义布局。</span><span class="sxs-lookup"><span data-stu-id="fa779-151">Define the layout in the *Identity/Account/Manage/_Layout.cshtml* file.</span></span>

```cshtml
@{
    Layout = "/Pages/Shared/_Layout.cshtml";
}
```

<span data-ttu-id="fa779-152">同时为所有页面的 "管理" 页指定布局 Identity ：</span><span class="sxs-lookup"><span data-stu-id="fa779-152">Also assign the layout for all the manage pages from the Identity pages:</span></span>

```cshtml
@{
    Layout = "_Layout.cshtml";
}
```

### <a name="validate-the-mfa-requirement-in-the-administration-page"></a><span data-ttu-id="fa779-153">在管理页中验证 MFA 要求</span><span class="sxs-lookup"><span data-stu-id="fa779-153">Validate the MFA requirement in the administration page</span></span>

<span data-ttu-id="fa779-154">"管理" Razor 页将验证用户是否已使用 MFA 登录。</span><span class="sxs-lookup"><span data-stu-id="fa779-154">The administration Razor Page validates that the user has logged in using MFA.</span></span> <span data-ttu-id="fa779-155">在 `OnGet` 方法中，标识用于访问用户声明。</span><span class="sxs-lookup"><span data-stu-id="fa779-155">In the `OnGet` method, the identity is used to access the user claims.</span></span> <span data-ttu-id="fa779-156">`amr`检查此声明的值 `mfa` 。</span><span class="sxs-lookup"><span data-stu-id="fa779-156">The `amr` claim is checked for the value `mfa`.</span></span> <span data-ttu-id="fa779-157">如果标识缺少此声明或为 `false` ，则页面重定向到 "启用 MFA" 页。</span><span class="sxs-lookup"><span data-stu-id="fa779-157">If the identity is missing this claim or is `false`, the page redirects to the Enable MFA page.</span></span> <span data-ttu-id="fa779-158">这是可能的，因为用户已登录，但没有 MFA。</span><span class="sxs-lookup"><span data-stu-id="fa779-158">This is possible because the user has logged in already, but without MFA.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;

namespace IdentityStandaloneMfa
{
    public class AdminModel : PageModel
    {
        public IActionResult OnGet()
        {
            var claimTwoFactorEnabled = 
                User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (claimTwoFactorEnabled != null && 
                "mfa".Equals(claimTwoFactorEnabled.Value))
            {
                // You logged in with MFA, do the administrative stuff
            }
            else
            {
                return Redirect(
                    "/Identity/Account/Manage/TwoFactorAuthentication");
            }

            return Page();
        }
    }
}
```

### <a name="ui-logic-to-toggle-user-login-information"></a><span data-ttu-id="fa779-159">用于切换用户登录信息的 UI 逻辑</span><span class="sxs-lookup"><span data-stu-id="fa779-159">UI logic to toggle user login information</span></span>

<span data-ttu-id="fa779-160">在启动时添加了授权策略。</span><span class="sxs-lookup"><span data-stu-id="fa779-160">An authorization policy was added at startup.</span></span> <span data-ttu-id="fa779-161">策略要求声明的 `amr` 值为 `mfa` 。</span><span class="sxs-lookup"><span data-stu-id="fa779-161">The policy requires the `amr` claim with the value `mfa`.</span></span>

```csharp
services.AddAuthorization(options =>
    options.AddPolicy("TwoFactorEnabled",
        x => x.RequireClaim("amr", "mfa")));
```

<span data-ttu-id="fa779-162">然后，可在视图中使用此策略 `_Layout` 来显示或隐藏带有警告的 **管理** 菜单：</span><span class="sxs-lookup"><span data-stu-id="fa779-162">This policy can then be used in the `_Layout` view to show or hide the **Admin** menu with the warning:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@using Microsoft.AspNetCore.Identity
@inject SignInManager<IdentityUser> SignInManager
@inject UserManager<IdentityUser> UserManager
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="fa779-163">如果标识已使用 MFA 登录，则会显示 " **管理** " 菜单而不显示工具提示警告。</span><span class="sxs-lookup"><span data-stu-id="fa779-163">If the identity has logged in using MFA, the **Admin** menu is displayed without the tooltip warning.</span></span> <span data-ttu-id="fa779-164">如果用户登录时未启用 MFA，将显示 " \*\*管理员 (未启用") \*\* 菜单与工具提示一起显示，通知用户 (解释警告) 。</span><span class="sxs-lookup"><span data-stu-id="fa779-164">When the user has logged in without MFA, the **Admin (Not Enabled)** menu is displayed along with the tooltip that informs the user (explaining the warning).</span></span>

```cshtml
@if (SignInManager.IsSignedIn(User))
{
    @if ((AuthorizationService.AuthorizeAsync(User, "TwoFactorEnabled")).Result.Succeeded)
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin">Admin</a>
        </li>
    }
    else
    {
        <li class="nav-item">
            <a class="nav-link text-dark" asp-area="" asp-page="/Admin" 
               id="tooltip-demo"  
               data-toggle="tooltip" 
               data-placement="bottom" 
               title="MFA is NOT enabled. This is required for the Admin Page. If you have activated MFA, then logout, login again.">
                Admin (Not Enabled)
            </a>
        </li>
    }
}
```

<span data-ttu-id="fa779-165">如果用户在没有 MFA 的情况下登录，将显示警告：</span><span class="sxs-lookup"><span data-stu-id="fa779-165">If the user logs in without MFA, the warning is displayed:</span></span>

![管理员 MFA 身份验证](mfa/_static/identitystandalonemfa_01.png)

<span data-ttu-id="fa779-167">单击 " **管理** " 链接时，用户将重定向到 "MFA 启用" 视图：</span><span class="sxs-lookup"><span data-stu-id="fa779-167">The user is redirected to the MFA enable view when clicking the **Admin** link:</span></span>

![管理员激活 MFA 身份验证](mfa/_static/identitystandalonemfa_02.png)

## <a name="send-mfa-sign-in-requirement-to-openid-connect-server"></a><span data-ttu-id="fa779-169">将 MFA 登录要求发送到 OpenID Connect 服务器</span><span class="sxs-lookup"><span data-stu-id="fa779-169">Send MFA sign-in requirement to OpenID Connect server</span></span> 

<span data-ttu-id="fa779-170">`acr_values`参数可用于 `mfa` 在身份验证请求中将所需的值从客户端传递到服务器。</span><span class="sxs-lookup"><span data-stu-id="fa779-170">The `acr_values` parameter can be used to pass the `mfa` required value from the client to the server in an authentication request.</span></span>

> [!NOTE]
> <span data-ttu-id="fa779-171">`acr_values`需要在 OpenID connect 服务器上处理参数，此参数才有效。</span><span class="sxs-lookup"><span data-stu-id="fa779-171">The `acr_values` parameter needs to be handled on the OpenID Connect server for this to work.</span></span>

### <a name="openid-connect-aspnet-core-client"></a><span data-ttu-id="fa779-172">OpenID Connect ASP.NET Core 客户端</span><span class="sxs-lookup"><span data-stu-id="fa779-172">OpenID Connect ASP.NET Core client</span></span>

<span data-ttu-id="fa779-173">ASP.NET Core Razor 页面 OpenID connect 客户端应用使用 `AddOpenIdConnect` 方法登录到 OpenID connect 服务器。</span><span class="sxs-lookup"><span data-stu-id="fa779-173">The ASP.NET Core Razor Pages OpenID Connect client app uses the `AddOpenIdConnect` method to login to the OpenID Connect server.</span></span> <span data-ttu-id="fa779-174">`acr_values`参数设置为 `mfa` 值，并随身份验证请求一起发送。</span><span class="sxs-lookup"><span data-stu-id="fa779-174">The `acr_values` parameter is set with the `mfa` value and sent with the authentication request.</span></span> <span data-ttu-id="fa779-175">`OpenIdConnectEvents`用于添加此。</span><span class="sxs-lookup"><span data-stu-id="fa779-175">The `OpenIdConnectEvents` is used to add this.</span></span>

<span data-ttu-id="fa779-176">有关推荐的 `acr_values` 参数值，请参阅 [身份验证方法引用值](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)。</span><span class="sxs-lookup"><span data-stu-id="fa779-176">For recommended `acr_values` parameter values, see [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08).</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "<OpenID Connect server URL>";
        options.RequireHttpsMetadata = true;
        options.ClientId = "<OpenID Connect client ID>";
        options.ClientSecret = "<>";
        // Code with PKCE can also be used here
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
        options.Events = new OpenIdConnectEvents
        {
            OnRedirectToIdentityProvider = context =>
            {
                context.ProtocolMessage.SetParameter("acr_values", "mfa");
                return Task.FromResult(0);
            }
        };
    });
```

### <a name="example-openid-connect-no-locidentityserver-4-server-with-no-locaspnet-core-identity"></a><span data-ttu-id="fa779-177">示例 OpenID Connect Identity server 4 服务器与 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="fa779-177">Example OpenID Connect IdentityServer 4 server with ASP.NET Core Identity</span></span>

<span data-ttu-id="fa779-178">在使用 MVC 视图实现的 OpenID Connect 服务器上 ASP.NET Core Identity ，将创建一个名为 *ErrorEnable2FA* 的新视图。</span><span class="sxs-lookup"><span data-stu-id="fa779-178">On the OpenID Connect server, which is implemented using ASP.NET Core Identity with MVC views, a new view named *ErrorEnable2FA.cshtml* is created.</span></span> <span data-ttu-id="fa779-179">视图：</span><span class="sxs-lookup"><span data-stu-id="fa779-179">The view:</span></span>

* <span data-ttu-id="fa779-180">显示来自 Identity 需要 MFA 但用户未在中激活此应用程序的应用程序 Identity 。</span><span class="sxs-lookup"><span data-stu-id="fa779-180">Displays if the Identity comes from an app that requires MFA but the user hasn't activated this in Identity.</span></span>
* <span data-ttu-id="fa779-181">通知用户并添加一个用于激活此的链接。</span><span class="sxs-lookup"><span data-stu-id="fa779-181">Informs the user and adds a link to activate this.</span></span>

```cshtml
@{
    ViewData["Title"] = "ErrorEnable2FA";
}

<h1>The client application requires you to have MFA enabled. Enable this, try login again.</h1>

<br />

You can enable MFA to login here:

<br />

<a asp-controller="Manage" asp-action="TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="fa779-182">在 `Login` 方法中， `IIdentityServerInteractionService` 接口实现 `_interaction` 用于访问 OpenID connect 请求参数。</span><span class="sxs-lookup"><span data-stu-id="fa779-182">In the `Login` method, the `IIdentityServerInteractionService` interface implementation `_interaction` is used to access the OpenID Connect request parameters.</span></span> <span data-ttu-id="fa779-183">`acr_values`使用属性访问参数 `AcrValues` 。</span><span class="sxs-lookup"><span data-stu-id="fa779-183">The `acr_values` parameter is accessed using the `AcrValues` property.</span></span> <span data-ttu-id="fa779-184">当客户端通过集发送此时 `mfa` ，可以检查此情况。</span><span class="sxs-lookup"><span data-stu-id="fa779-184">As the client sent this with `mfa` set, this can then be checked.</span></span>

<span data-ttu-id="fa779-185">如果需要 MFA，并且中的用户 ASP.NET Core Identity 启用了 mfa，则登录将继续。</span><span class="sxs-lookup"><span data-stu-id="fa779-185">If MFA is required, and the user in ASP.NET Core Identity has MFA enabled, then the login continues.</span></span> <span data-ttu-id="fa779-186">如果用户未启用 MFA，则会将用户重定向到自定义视图 *ErrorEnable2FA*。</span><span class="sxs-lookup"><span data-stu-id="fa779-186">When the user has no MFA enabled, the user is redirected to the custom view *ErrorEnable2FA.cshtml*.</span></span> <span data-ttu-id="fa779-187">然后 ASP.NET Core Identity 在中对用户进行签名。</span><span class="sxs-lookup"><span data-stu-id="fa779-187">Then ASP.NET Core Identity signs the user in.</span></span>

```csharp
//
// POST: /Account/Login
[HttpPost]
[AllowAnonymous]
[ValidateAntiForgeryToken]
public async Task<IActionResult> Login(LoginInputModel model)
{
    var returnUrl = model.ReturnUrl;
    var context = 
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa = 
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    var user = await _userManager.FindByNameAsync(model.Email);
    if (user != null && !user.TwoFactorEnabled && requires2Fa)
    {
        return RedirectToAction(nameof(ErrorEnable2FA));
    }

    // code omitted for brevity
```

<span data-ttu-id="fa779-188">`ExternalLoginCallback`方法的工作方式类似于 Identity 本地登录。</span><span class="sxs-lookup"><span data-stu-id="fa779-188">The `ExternalLoginCallback` method works like the local Identity login.</span></span> <span data-ttu-id="fa779-189">将 `AcrValues` 检查该属性的 `mfa` 值。</span><span class="sxs-lookup"><span data-stu-id="fa779-189">The `AcrValues` property is checked for the `mfa` value.</span></span> <span data-ttu-id="fa779-190">如果 `mfa` 值存在，则在登录完成之前强制执行 MFA (例如，重定向到) 的 `ErrorEnable2FA` 视图。</span><span class="sxs-lookup"><span data-stu-id="fa779-190">If the `mfa` value is present, MFA is forced before the login completes (for example, redirected to the `ErrorEnable2FA` view).</span></span>

```csharp
//
// GET: /Account/ExternalLoginCallback
[HttpGet]
[AllowAnonymous]
public async Task<IActionResult> ExternalLoginCallback(
    string returnUrl = null,
    string remoteError = null)
{
    var context =
        await _interaction.GetAuthorizationContextAsync(returnUrl);
    var requires2Fa =
        context?.AcrValues.Count(t => t.Contains("mfa")) >= 1;

    if (remoteError != null)
    {
        ModelState.AddModelError(
            string.Empty,
            _sharedLocalizer["EXTERNAL_PROVIDER_ERROR", 
            remoteError]);
        return View(nameof(Login));
    }
    var info = await _signInManager.GetExternalLoginInfoAsync();

    if (info == null)
    {
        return RedirectToAction(nameof(Login));
    }

    var email = info.Principal.FindFirstValue(ClaimTypes.Email);

    if (!string.IsNullOrEmpty(email))
    {
        var user = await _userManager.FindByNameAsync(email);
        if (user != null && !user.TwoFactorEnabled && requires2Fa)
        {
            return RedirectToAction(nameof(ErrorEnable2FA));
        }
    }

    // Sign in the user with this external login provider if the user already has a login.
    var result = await _signInManager
        .ExternalLoginSignInAsync(
            info.LoginProvider, 
            info.ProviderKey, 
            isPersistent: 
            false);

    // code omitted for brevity
```

<span data-ttu-id="fa779-191">如果用户已登录，则客户端应用：</span><span class="sxs-lookup"><span data-stu-id="fa779-191">If the user is already logged in, the client app:</span></span>

* <span data-ttu-id="fa779-192">仍验证 `amr` 声明。</span><span class="sxs-lookup"><span data-stu-id="fa779-192">Still validates the `amr` claim.</span></span>
* <span data-ttu-id="fa779-193">可以使用指向视图的链接来设置 MFA ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="fa779-193">Can set up the MFA with a link to the ASP.NET Core Identity view.</span></span>

![acr_values-1](mfa/_static/acr_values-1.png)

## <a name="force-aspnet-core-openid-connect-client-to-require-mfa"></a><span data-ttu-id="fa779-195">强制 ASP.NET Core OpenID Connect 客户端要求 MFA</span><span class="sxs-lookup"><span data-stu-id="fa779-195">Force ASP.NET Core OpenID Connect client to require MFA</span></span>

<span data-ttu-id="fa779-196">此示例演示如何 Razor 使用 OpenID connect 登录的 ASP.NET Core 页面应用程序可能要求用户使用 MFA 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="fa779-196">This example shows how an ASP.NET Core Razor Page app, which uses OpenID Connect to sign in, can require that users have authenticated using MFA.</span></span>

<span data-ttu-id="fa779-197">若要验证 MFA 要求，请 `IAuthorizationRequirement` 创建一个要求。</span><span class="sxs-lookup"><span data-stu-id="fa779-197">To validate the MFA requirement, an `IAuthorizationRequirement` requirement is created.</span></span> <span data-ttu-id="fa779-198">这将使用需要 MFA 的策略添加到页面中。</span><span class="sxs-lookup"><span data-stu-id="fa779-198">This will be added to the pages using a policy that requires MFA.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
 
namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfa : IAuthorizationRequirement{}
}
```

<span data-ttu-id="fa779-199">`AuthorizationHandler`实现，它将使用 `amr` 声明并检查值 `mfa` 。</span><span class="sxs-lookup"><span data-stu-id="fa779-199">An `AuthorizationHandler` is implemented that will use the `amr` claim and check for the value `mfa`.</span></span> <span data-ttu-id="fa779-200">`amr`在 `id_token` 身份验证成功的中返回，并且可以有多个不同的值，如[身份验证方法引用值](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08)规范中所定义。</span><span class="sxs-lookup"><span data-stu-id="fa779-200">The `amr` is returned in the `id_token` of a successful authentication and can have many different values as defined in the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="fa779-201">返回的值取决于标识身份验证的方式，以及 OpenID Connect 服务器实现上的身份验证方式。</span><span class="sxs-lookup"><span data-stu-id="fa779-201">The returned value depends on how the identity authenticated and on the OpenID Connect server implementation.</span></span>

<span data-ttu-id="fa779-202">`AuthorizationHandler`使用 `RequireMfa` 要求并验证 `amr` 声明。</span><span class="sxs-lookup"><span data-stu-id="fa779-202">The `AuthorizationHandler` uses the `RequireMfa` requirement and validates the `amr` claim.</span></span> <span data-ttu-id="fa779-203">OpenID Connect 服务器可以通过使用服务器4来实现 Identity ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="fa779-203">The OpenID Connect server can be implemented using IdentityServer4 with ASP.NET Core Identity.</span></span> <span data-ttu-id="fa779-204">当用户使用 TOTP 登录时，将 `amr` 使用 MFA 值返回声明。</span><span class="sxs-lookup"><span data-stu-id="fa779-204">When a user logs in using TOTP, the `amr` claim is returned with an MFA value.</span></span> <span data-ttu-id="fa779-205">如果使用不同的 OpenID Connect 服务器实现或不同的 MFA 类型，则 `amr` 声明或可以具有不同的值。</span><span class="sxs-lookup"><span data-stu-id="fa779-205">If using a different OpenID Connect server implementation or a different MFA type, the `amr` claim will, or can, have a different value.</span></span> <span data-ttu-id="fa779-206">要接受此代码，还必须对代码进行扩展。</span><span class="sxs-lookup"><span data-stu-id="fa779-206">The code must be extended to accept this as well.</span></span>

```csharp
using Microsoft.AspNetCore.Authorization;
using System;
using System.Linq;
using System.Threading.Tasks;

namespace AspNetCoreRequireMfaOidc
{
    public class RequireMfaHandler : AuthorizationHandler<RequireMfa>
    {
        protected override Task HandleRequirementAsync(
            AuthorizationHandlerContext context, 
            RequireMfa requirement)
        {
            if (context == null)
                throw new ArgumentNullException(nameof(context));
            if (requirement == null)
                throw new ArgumentNullException(nameof(requirement));

            var amrClaim =
                context.User.Claims.FirstOrDefault(t => t.Type == "amr");

            if (amrClaim != null && amrClaim.Value == Amr.Mfa)
            {
                context.Succeed(requirement);
            }

            return Task.CompletedTask;
        }
    }
}
```

<span data-ttu-id="fa779-207">在 `Startup.ConfigureServices` 方法中，将 `AddOpenIdConnect` 方法用作默认质询方案。</span><span class="sxs-lookup"><span data-stu-id="fa779-207">In the `Startup.ConfigureServices` method, the `AddOpenIdConnect` method is used as the default challenge scheme.</span></span> <span data-ttu-id="fa779-208">用于检查声明的授权处理程序 `amr` 将添加到控制容器的反转。</span><span class="sxs-lookup"><span data-stu-id="fa779-208">The authorization handler, which is used to check the `amr` claim, is added to the Inversion of Control container.</span></span> <span data-ttu-id="fa779-209">然后，将创建一个策略来添加 `RequireMfa` 要求。</span><span class="sxs-lookup"><span data-stu-id="fa779-209">A policy is then created which adds the `RequireMfa` requirement.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.ConfigureApplicationCookie(options =>
        options.Cookie.SecurePolicy =
            CookieSecurePolicy.Always);

    services.AddSingleton<IAuthorizationHandler, RequireMfaHandler>();

    services.AddAuthentication(options =>
    {
        options.DefaultScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.DefaultChallengeScheme =
            OpenIdConnectDefaults.AuthenticationScheme;
    })
    .AddCookie()
    .AddOpenIdConnect(options =>
    {
        options.SignInScheme =
            CookieAuthenticationDefaults.AuthenticationScheme;
        options.Authority = "https://localhost:44352";
        options.RequireHttpsMetadata = true;
        options.ClientId = "AspNetCoreRequireMfaOidc";
        options.ClientSecret = "AspNetCoreRequireMfaOidcSecret";
        options.ResponseType = "code id_token";
        options.Scope.Add("profile");
        options.Scope.Add("offline_access");
        options.SaveTokens = true;
    });

    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireMfa", policyIsAdminRequirement =>
        {
            policyIsAdminRequirement.Requirements.Add(new RequireMfa());
        });
    });

    services.AddRazorPages();
}
```

<span data-ttu-id="fa779-210">然后，将在页面中根据需要使用此策略 Razor 。</span><span class="sxs-lookup"><span data-stu-id="fa779-210">This policy is then used in the Razor page as required.</span></span> <span data-ttu-id="fa779-211">也可以全局为整个应用程序添加策略。</span><span class="sxs-lookup"><span data-stu-id="fa779-211">The policy could be added globally for the entire app as well.</span></span>

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Mvc;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Microsoft.Extensions.Logging;

namespace AspNetCoreRequireMfaOidc.Pages
{
    [Authorize(Policy= "RequireMfa")]
    public class IndexModel : PageModel
    {
        private readonly ILogger<IndexModel> _logger;

        public IndexModel(ILogger<IndexModel> logger)
        {
            _logger = logger;
        }

        public void OnGet()
        {
        }
    }
}
```

<span data-ttu-id="fa779-212">如果用户不使用 MFA 进行身份验证，则 `amr` 声明可能具有 `pwd` 值。</span><span class="sxs-lookup"><span data-stu-id="fa779-212">If the user authenticates without MFA, the `amr` claim will probably have a `pwd` value.</span></span> <span data-ttu-id="fa779-213">请求不会被授权访问此页。</span><span class="sxs-lookup"><span data-stu-id="fa779-213">The request won't be authorized to access the page.</span></span> <span data-ttu-id="fa779-214">如果使用默认值，则用户将被重定向到 *Account/AccessDenied* 页。</span><span class="sxs-lookup"><span data-stu-id="fa779-214">Using the default values, the user will be redirected to the *Account/AccessDenied* page.</span></span> <span data-ttu-id="fa779-215">此行为可以更改，也可以在此处实现自己的自定义逻辑。</span><span class="sxs-lookup"><span data-stu-id="fa779-215">This behavior can be changed or you can implement your own custom logic here.</span></span> <span data-ttu-id="fa779-216">在此示例中，添加了一个链接，以便有效的用户可以为其帐户设置 MFA。</span><span class="sxs-lookup"><span data-stu-id="fa779-216">In this example, a link is added so that the valid user can set up MFA for their account.</span></span>

```cshtml
@page
@model AspNetCoreRequireMfaOidc.AccessDeniedModel
@{
    ViewData["Title"] = "AccessDenied";
    Layout = "~/Pages/Shared/_Layout.cshtml";
}

<h1>AccessDenied</h1>

You require MFA to login here

<a href="https://localhost:44352/Manage/TwoFactorAuthentication">Enable MFA</a>
```

<span data-ttu-id="fa779-217">现在只有通过 MFA 进行身份验证的用户才能访问该页面或网站。</span><span class="sxs-lookup"><span data-stu-id="fa779-217">Now only users that authenticate with MFA can access the page or website.</span></span> <span data-ttu-id="fa779-218">如果使用不同的 MFA 类型，或2FA 为正常，则 `amr` 声明将具有不同的值，并且需要正确处理。</span><span class="sxs-lookup"><span data-stu-id="fa779-218">If different MFA types are used or if 2FA is okay, the `amr` claim will have different values and needs to be processed correctly.</span></span> <span data-ttu-id="fa779-219">不同的 OpenID Connect 服务器还为此声明返回不同的值，并且可能不遵循 [身份验证方法引用值](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) 规范。</span><span class="sxs-lookup"><span data-stu-id="fa779-219">Different OpenID Connect servers also return different values for this claim and might not follow the [Authentication Method Reference Values](https://tools.ietf.org/html/draft-ietf-oauth-amr-values-08) specification.</span></span>

<span data-ttu-id="fa779-220">如果不使用 MFA 登录 (例如，只使用密码) ：</span><span class="sxs-lookup"><span data-stu-id="fa779-220">When logging in without MFA (for example, using just a password):</span></span>

* <span data-ttu-id="fa779-221">`amr`具有 `pwd` 值：</span><span class="sxs-lookup"><span data-stu-id="fa779-221">The `amr` has the `pwd` value:</span></span>

    ![require_mfa_oidc_02.png](mfa/_static/require_mfa_oidc_02.png)

* <span data-ttu-id="fa779-223">拒绝访问：</span><span class="sxs-lookup"><span data-stu-id="fa779-223">Access is denied:</span></span>

    ![require_mfa_oidc_03.png](mfa/_static/require_mfa_oidc_03.png)

<span data-ttu-id="fa779-225">或者，使用 OTP 登录 Identity ：</span><span class="sxs-lookup"><span data-stu-id="fa779-225">Alternatively, logging in using OTP with Identity:</span></span>

![require_mfa_oidc_01.png](mfa/_static/require_mfa_oidc_01.png)

## <a name="additional-resources"></a><span data-ttu-id="fa779-227">其他资源</span><span class="sxs-lookup"><span data-stu-id="fa779-227">Additional resources</span></span>

* [<span data-ttu-id="fa779-228">为 ASP.NET Core 中的 TOTP 验证器应用启用 QR 代码生成</span><span class="sxs-lookup"><span data-stu-id="fa779-228">Enable QR Code generation for TOTP authenticator apps in ASP.NET Core</span></span>](xref:security/authentication/identity-enable-qrcodes)
* [<span data-ttu-id="fa779-229">Azure Active Directory 的无密码 authentication 选项</span><span class="sxs-lookup"><span data-stu-id="fa779-229">Passwordless authentication options for Azure Active Directory</span></span>](/azure/active-directory/authentication/concept-authentication-passwordless)
* [<span data-ttu-id="fa779-230">FIDO2 .NET library for FIDO2/WebAuthn 证明和断言（使用 .NET）</span><span class="sxs-lookup"><span data-stu-id="fa779-230">FIDO2 .NET library for FIDO2 / WebAuthn Attestation and Assertion using .NET</span></span>](https://github.com/abergs/fido2-net-lib)
* [<span data-ttu-id="fa779-231">WebAuthn 出色</span><span class="sxs-lookup"><span data-stu-id="fa779-231">WebAuthn Awesome</span></span>](https://github.com/herrjemand/awesome-webauthn)
