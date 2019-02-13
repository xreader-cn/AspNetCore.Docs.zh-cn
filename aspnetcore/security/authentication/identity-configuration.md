---
title: 配置 ASP.NET Core 标识
author: AdrienTorris
description: 了解 ASP.NET Core 标识默认值，并了解如何配置要使用自定义值的标识属性。
ms.author: riande
ms.date: 02/11/2019
uid: security/authentication/identity-configuration
ms.openlocfilehash: 3213f669cbfccdcda7cc7c0142b8101e696678e6
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159508"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="941da-103">配置 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="941da-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="941da-104">ASP.NET Core 标识设置，例如密码策略、 锁定和 cookie 配置使用默认值。</span><span class="sxs-lookup"><span data-stu-id="941da-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="941da-105">在中，可以重写这些设置`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="941da-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a><span data-ttu-id="941da-106">标识选项</span><span class="sxs-lookup"><span data-stu-id="941da-106">Identity options</span></span>

<span data-ttu-id="941da-107">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)类表示可用于标识系统配置的选项。</span><span class="sxs-lookup"><span data-stu-id="941da-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="941da-108">`IdentityOptions` 必须设置**后**调用`AddIdentity`或`AddDefaultIdentity`。</span><span class="sxs-lookup"><span data-stu-id="941da-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="941da-109">声明标识</span><span class="sxs-lookup"><span data-stu-id="941da-109">Claims Identity</span></span>

<span data-ttu-id="941da-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity)指定[ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions)与下表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="941da-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="941da-111">属性</span><span class="sxs-lookup"><span data-stu-id="941da-111">Property</span></span> | <span data-ttu-id="941da-112">描述</span><span class="sxs-lookup"><span data-stu-id="941da-112">Description</span></span> | <span data-ttu-id="941da-113">默认</span><span class="sxs-lookup"><span data-stu-id="941da-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="941da-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="941da-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="941da-115">获取或设置用于为角色声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="941da-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="941da-116">ClaimTypes.Role</span><span class="sxs-lookup"><span data-stu-id="941da-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="941da-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="941da-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="941da-118">获取或设置用于安全戳声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="941da-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="941da-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="941da-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="941da-120">获取或设置用于的用户标识符声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="941da-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="941da-121">ClaimTypes.NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="941da-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="941da-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="941da-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="941da-123">获取或设置用于用户名称声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="941da-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="941da-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="941da-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="941da-125">锁定</span><span class="sxs-lookup"><span data-stu-id="941da-125">Lockout</span></span>

<span data-ttu-id="941da-126">在中设置锁定[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_)方法：</span><span class="sxs-lookup"><span data-stu-id="941da-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="941da-127">前面的代码基于`Login`标识模板。</span><span class="sxs-lookup"><span data-stu-id="941da-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="941da-128">在中设置锁定选项`StartUp.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="941da-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="941da-129">上述代码会设置[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions)具有默认值。</span><span class="sxs-lookup"><span data-stu-id="941da-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="941da-130">成功的身份验证失败的访问尝试计数重置并重置时钟。</span><span class="sxs-lookup"><span data-stu-id="941da-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="941da-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout)指定[LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions)与表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="941da-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="941da-132">属性</span><span class="sxs-lookup"><span data-stu-id="941da-132">Property</span></span> | <span data-ttu-id="941da-133">描述</span><span class="sxs-lookup"><span data-stu-id="941da-133">Description</span></span> | <span data-ttu-id="941da-134">默认</span><span class="sxs-lookup"><span data-stu-id="941da-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="941da-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="941da-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="941da-136">确定是否新用户会被锁定。</span><span class="sxs-lookup"><span data-stu-id="941da-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="941da-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="941da-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="941da-138">时间量用户已锁定时在锁定时发生。</span><span class="sxs-lookup"><span data-stu-id="941da-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="941da-139">5 分钟</span><span class="sxs-lookup"><span data-stu-id="941da-139">5 minutes</span></span> |
| [<span data-ttu-id="941da-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="941da-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="941da-141">用户已被锁定，如果启用了锁定前的失败的访问尝试数。</span><span class="sxs-lookup"><span data-stu-id="941da-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="941da-142">5</span><span class="sxs-lookup"><span data-stu-id="941da-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="941da-143">Password</span><span class="sxs-lookup"><span data-stu-id="941da-143">Password</span></span>

<span data-ttu-id="941da-144">默认情况下，标识要求密码包含大写字符、 小写字符、 数字、 和非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="941da-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="941da-145">密码必须至少为六个字符。</span><span class="sxs-lookup"><span data-stu-id="941da-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="941da-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions)可以设置`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="941da-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="941da-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password)指定[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions)与表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="941da-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="941da-148">属性</span><span class="sxs-lookup"><span data-stu-id="941da-148">Property</span></span> | <span data-ttu-id="941da-149">描述</span><span class="sxs-lookup"><span data-stu-id="941da-149">Description</span></span> | <span data-ttu-id="941da-150">默认</span><span class="sxs-lookup"><span data-stu-id="941da-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="941da-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="941da-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="941da-152">需要介于 0-9 的密码。</span><span class="sxs-lookup"><span data-stu-id="941da-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="941da-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="941da-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="941da-154">密码最小长度。</span><span class="sxs-lookup"><span data-stu-id="941da-154">The minimum length of the password.</span></span> | <span data-ttu-id="941da-155">6</span><span class="sxs-lookup"><span data-stu-id="941da-155">6</span></span> |
| [<span data-ttu-id="941da-156">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="941da-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="941da-157">要求密码中的小写字符。</span><span class="sxs-lookup"><span data-stu-id="941da-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="941da-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="941da-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="941da-159">需要在密码中的非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="941da-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="941da-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="941da-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="941da-161">仅适用于 ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="941da-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="941da-162">要求在密码中非重复字符数。</span><span class="sxs-lookup"><span data-stu-id="941da-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="941da-163">1</span><span class="sxs-lookup"><span data-stu-id="941da-163">1</span></span> |
| [<span data-ttu-id="941da-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="941da-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="941da-165">需要大写字符的密码。</span><span class="sxs-lookup"><span data-stu-id="941da-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="941da-166">属性</span><span class="sxs-lookup"><span data-stu-id="941da-166">Property</span></span> | <span data-ttu-id="941da-167">描述</span><span class="sxs-lookup"><span data-stu-id="941da-167">Description</span></span> | <span data-ttu-id="941da-168">默认</span><span class="sxs-lookup"><span data-stu-id="941da-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="941da-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="941da-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="941da-170">需要介于 0-9 的密码。</span><span class="sxs-lookup"><span data-stu-id="941da-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="941da-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="941da-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="941da-172">密码最小长度。</span><span class="sxs-lookup"><span data-stu-id="941da-172">The minimum length of the password.</span></span> | <span data-ttu-id="941da-173">6</span><span class="sxs-lookup"><span data-stu-id="941da-173">6</span></span> |
| [<span data-ttu-id="941da-174">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="941da-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="941da-175">要求密码中的小写字符。</span><span class="sxs-lookup"><span data-stu-id="941da-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="941da-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="941da-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="941da-177">需要在密码中的非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="941da-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="941da-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="941da-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="941da-179">需要大写字符的密码。</span><span class="sxs-lookup"><span data-stu-id="941da-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="941da-180">单一登录</span><span class="sxs-lookup"><span data-stu-id="941da-180">Sign-in</span></span>

<span data-ttu-id="941da-181">下面的代码设置`SignIn`（为默认值） 的设置：</span><span class="sxs-lookup"><span data-stu-id="941da-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="941da-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin)指定[SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions)与表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="941da-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="941da-183">属性</span><span class="sxs-lookup"><span data-stu-id="941da-183">Property</span></span> | <span data-ttu-id="941da-184">描述</span><span class="sxs-lookup"><span data-stu-id="941da-184">Description</span></span> | <span data-ttu-id="941da-185">默认</span><span class="sxs-lookup"><span data-stu-id="941da-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="941da-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="941da-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="941da-187">需要已确认的电子邮件，登录。</span><span class="sxs-lookup"><span data-stu-id="941da-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="941da-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="941da-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="941da-189">需要确认的电话号码进行登录。</span><span class="sxs-lookup"><span data-stu-id="941da-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="941da-190">标记</span><span class="sxs-lookup"><span data-stu-id="941da-190">Tokens</span></span>

<span data-ttu-id="941da-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens)指定[TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions)与表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="941da-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>


|                                                        <span data-ttu-id="941da-192">属性</span><span class="sxs-lookup"><span data-stu-id="941da-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="941da-193">描述</span><span class="sxs-lookup"><span data-stu-id="941da-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="941da-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="941da-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="941da-195">获取或设置`AuthenticatorTokenProvider`用于验证身份验证器使用双因素登录名。</span><span class="sxs-lookup"><span data-stu-id="941da-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="941da-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="941da-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="941da-197">获取或设置`ChangeEmailTokenProvider`用于生成电子邮件更改确认电子邮件中使用的令牌。</span><span class="sxs-lookup"><span data-stu-id="941da-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="941da-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="941da-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="941da-199">获取或设置`ChangePhoneNumberTokenProvider`用于生成令牌更改电话号码时使用。</span><span class="sxs-lookup"><span data-stu-id="941da-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="941da-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="941da-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="941da-201">获取或设置用于生成帐户确认电子邮件中使用的令牌的令牌提供程序。</span><span class="sxs-lookup"><span data-stu-id="941da-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="941da-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="941da-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="941da-203">获取或设置[IUserTwoFactorTokenProvider<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1)用于生成在密码重置电子邮件中使用的令牌。</span><span class="sxs-lookup"><span data-stu-id="941da-203">Gets or sets the [IUserTwoFactorTokenProvider<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="941da-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="941da-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="941da-205">用于构造[用户令牌提供程序](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor)与键用作提供程序的名称。</span><span class="sxs-lookup"><span data-stu-id="941da-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="941da-206">用户</span><span class="sxs-lookup"><span data-stu-id="941da-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="941da-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user)指定[UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions)与表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="941da-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="941da-208">属性</span><span class="sxs-lookup"><span data-stu-id="941da-208">Property</span></span> | <span data-ttu-id="941da-209">描述</span><span class="sxs-lookup"><span data-stu-id="941da-209">Description</span></span> | <span data-ttu-id="941da-210">默认</span><span class="sxs-lookup"><span data-stu-id="941da-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="941da-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="941da-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="941da-212">在用户名中允许的字符。</span><span class="sxs-lookup"><span data-stu-id="941da-212">Allowed characters in the username.</span></span> | <span data-ttu-id="941da-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="941da-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="941da-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="941da-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="941da-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="941da-215">0123456789</span></span><br><span data-ttu-id="941da-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="941da-216">-.\_@+</span></span> |
| [<span data-ttu-id="941da-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="941da-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="941da-218">要求每个用户必须拥有唯一的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="941da-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="941da-219">Cookie 设置</span><span class="sxs-lookup"><span data-stu-id="941da-219">Cookie settings</span></span>

<span data-ttu-id="941da-220">配置中的应用程序的 cookie `Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="941da-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="941da-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__)必须在调用**后**调用`AddIdentity`或`AddDefaultIdentity`。</span><span class="sxs-lookup"><span data-stu-id="941da-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="941da-222">有关详细信息，请参阅[CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions)。</span><span class="sxs-lookup"><span data-stu-id="941da-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="941da-223">密码哈希计算器选项</span><span class="sxs-lookup"><span data-stu-id="941da-223">Password Hasher options</span></span>

<span data-ttu-id="941da-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> 获取和设置进行密码哈希处理的选项。</span><span class="sxs-lookup"><span data-stu-id="941da-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="941da-225">选项</span><span class="sxs-lookup"><span data-stu-id="941da-225">Option</span></span> | <span data-ttu-id="941da-226">描述</span><span class="sxs-lookup"><span data-stu-id="941da-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="941da-227">新密码哈希处理时使用的兼容性模式。</span><span class="sxs-lookup"><span data-stu-id="941da-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="941da-228">默认为 <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>。</span><span class="sxs-lookup"><span data-stu-id="941da-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="941da-229">经过哈希处理密码，调用的第一个字节*格式标记*，指定用于对密码进行加密的哈希算法的版本。</span><span class="sxs-lookup"><span data-stu-id="941da-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="941da-230">验证密码与哈希时<xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*>方法选择正确的算法根据第一个字节。</span><span class="sxs-lookup"><span data-stu-id="941da-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="941da-231">客户端是能够进行身份验证而不考虑的这一版算法用于对密码进行加密。</span><span class="sxs-lookup"><span data-stu-id="941da-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="941da-232">设置兼容性模式将影响的哈希*新密码*。</span><span class="sxs-lookup"><span data-stu-id="941da-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="941da-233">使用哈希密码使用 PBKDF2 时的迭代次数。</span><span class="sxs-lookup"><span data-stu-id="941da-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="941da-234">此值是时，才使用<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode>设置为<xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>。</span><span class="sxs-lookup"><span data-stu-id="941da-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="941da-235">值必须是一个正整数，默认值为`10000`。</span><span class="sxs-lookup"><span data-stu-id="941da-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="941da-236">在以下示例中，<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount>设置为`12000`中`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="941da-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
