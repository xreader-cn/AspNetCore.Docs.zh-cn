---
title: 配置 ASP.NET CoreIdentity
author: AdrienTorris
description: 了解 ASP.NET Core Identity默认值，并了解如何配置Identity属性以使用自定义值。
ms.author: riande
ms.date: 02/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-configuration
ms.openlocfilehash: b88f2627eabc536f2d3b8e677020a67bfd1a40ba
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775643"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="4b5b3-103">配置 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="4b5b3-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="4b5b3-104">ASP.NET Core 标识将默认值用于设置密码策略、锁定和 cookie 配置等设置。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="4b5b3-105">可以在`Startup`类中重写这些设置。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a><span data-ttu-id="4b5b3-106">标识选项</span><span class="sxs-lookup"><span data-stu-id="4b5b3-106">Identity options</span></span>

<span data-ttu-id="4b5b3-107">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)类表示可用于配置标识系统的选项。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="4b5b3-108">`IdentityOptions`必须**在**调用`AddIdentity`或`AddDefaultIdentity`之后设置。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="4b5b3-109">声明标识</span><span class="sxs-lookup"><span data-stu-id="4b5b3-109">Claims Identity</span></span>

<span data-ttu-id="4b5b3-110">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity)指定[ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) ，其中包含下表所示的属性。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="4b5b3-111">属性</span><span class="sxs-lookup"><span data-stu-id="4b5b3-111">Property</span></span> | <span data-ttu-id="4b5b3-112">说明</span><span class="sxs-lookup"><span data-stu-id="4b5b3-112">Description</span></span> | <span data-ttu-id="4b5b3-113">默认</span><span class="sxs-lookup"><span data-stu-id="4b5b3-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="4b5b3-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="4b5b3-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="4b5b3-115">获取或设置用于角色声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="4b5b3-116">ClaimTypes</span><span class="sxs-lookup"><span data-stu-id="4b5b3-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="4b5b3-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="4b5b3-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="4b5b3-118">获取或设置用于安全戳声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="4b5b3-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="4b5b3-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="4b5b3-120">获取或设置用于用户标识符声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="4b5b3-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="4b5b3-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="4b5b3-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="4b5b3-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="4b5b3-123">获取或设置用于用户名声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="4b5b3-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="4b5b3-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="4b5b3-125">锁定</span><span class="sxs-lookup"><span data-stu-id="4b5b3-125">Lockout</span></span>

<span data-ttu-id="4b5b3-126">在[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_)方法中设置锁定：</span><span class="sxs-lookup"><span data-stu-id="4b5b3-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="4b5b3-127">前面的代码基于`Login`标识模板。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="4b5b3-128">锁定选项设置为`StartUp.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="4b5b3-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="4b5b3-129">前面的代码将[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions)设置为默认值。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="4b5b3-130">身份验证成功后，将重置失败的访问尝试计数并重置时钟。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="4b5b3-131">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout)指定[LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="4b5b3-132">属性</span><span class="sxs-lookup"><span data-stu-id="4b5b3-132">Property</span></span> | <span data-ttu-id="4b5b3-133">说明</span><span class="sxs-lookup"><span data-stu-id="4b5b3-133">Description</span></span> | <span data-ttu-id="4b5b3-134">默认</span><span class="sxs-lookup"><span data-stu-id="4b5b3-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="4b5b3-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="4b5b3-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="4b5b3-136">确定新用户是否可以锁定。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="4b5b3-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="4b5b3-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="4b5b3-138">锁定发生时用户被锁定的时间长度。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="4b5b3-139">5 分钟</span><span class="sxs-lookup"><span data-stu-id="4b5b3-139">5 minutes</span></span> |
| [<span data-ttu-id="4b5b3-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="4b5b3-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="4b5b3-141">如果启用了锁定，则在用户被锁定之前失败的访问尝试次数。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="4b5b3-142">5</span><span class="sxs-lookup"><span data-stu-id="4b5b3-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="4b5b3-143">密码</span><span class="sxs-lookup"><span data-stu-id="4b5b3-143">Password</span></span>

<span data-ttu-id="4b5b3-144">默认情况下，Identity 要求密码包含大写字符、小写字符、数字以及非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="4b5b3-145">密码长度必须至少为6个字符。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="4b5b3-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions)可以在中`Startup.ConfigureServices`设置 PasswordOptions。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="4b5b3-147">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password)指定[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="4b5b3-148">属性</span><span class="sxs-lookup"><span data-stu-id="4b5b3-148">Property</span></span> | <span data-ttu-id="4b5b3-149">说明</span><span class="sxs-lookup"><span data-stu-id="4b5b3-149">Description</span></span> | <span data-ttu-id="4b5b3-150">默认</span><span class="sxs-lookup"><span data-stu-id="4b5b3-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="4b5b3-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="4b5b3-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="4b5b3-152">要求密码中的数字介于0-9 之间。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="4b5b3-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="4b5b3-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="4b5b3-154">密码的最小长度。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-154">The minimum length of the password.</span></span> | <span data-ttu-id="4b5b3-155">6</span><span class="sxs-lookup"><span data-stu-id="4b5b3-155">6</span></span> |
| [<span data-ttu-id="4b5b3-156">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="4b5b3-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="4b5b3-157">密码中需要小写字符。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="4b5b3-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="4b5b3-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="4b5b3-159">密码中需要一个非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="4b5b3-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="4b5b3-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="4b5b3-161">仅适用于 ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="4b5b3-162">需要密码中的非重复字符数。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="4b5b3-163">1</span><span class="sxs-lookup"><span data-stu-id="4b5b3-163">1</span></span> |
| [<span data-ttu-id="4b5b3-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="4b5b3-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="4b5b3-165">密码中需要大写字符。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="4b5b3-166">属性</span><span class="sxs-lookup"><span data-stu-id="4b5b3-166">Property</span></span> | <span data-ttu-id="4b5b3-167">说明</span><span class="sxs-lookup"><span data-stu-id="4b5b3-167">Description</span></span> | <span data-ttu-id="4b5b3-168">默认</span><span class="sxs-lookup"><span data-stu-id="4b5b3-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="4b5b3-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="4b5b3-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="4b5b3-170">要求密码中的数字介于0-9 之间。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="4b5b3-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="4b5b3-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="4b5b3-172">密码的最小长度。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-172">The minimum length of the password.</span></span> | <span data-ttu-id="4b5b3-173">6</span><span class="sxs-lookup"><span data-stu-id="4b5b3-173">6</span></span> |
| [<span data-ttu-id="4b5b3-174">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="4b5b3-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="4b5b3-175">密码中需要小写字符。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="4b5b3-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="4b5b3-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="4b5b3-177">密码中需要一个非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="4b5b3-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="4b5b3-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="4b5b3-179">密码中需要大写字符。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="4b5b3-180">登录</span><span class="sxs-lookup"><span data-stu-id="4b5b3-180">Sign-in</span></span>

<span data-ttu-id="4b5b3-181">下面的代码将`SignIn`设置（到默认值）：</span><span class="sxs-lookup"><span data-stu-id="4b5b3-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="4b5b3-182">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin)指定[SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="4b5b3-183">属性</span><span class="sxs-lookup"><span data-stu-id="4b5b3-183">Property</span></span> | <span data-ttu-id="4b5b3-184">说明</span><span class="sxs-lookup"><span data-stu-id="4b5b3-184">Description</span></span> | <span data-ttu-id="4b5b3-185">默认</span><span class="sxs-lookup"><span data-stu-id="4b5b3-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="4b5b3-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="4b5b3-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="4b5b3-187">需要确认电子邮件登录。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="4b5b3-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="4b5b3-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="4b5b3-189">需要确认电话号码才能登录。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="4b5b3-190">令牌</span><span class="sxs-lookup"><span data-stu-id="4b5b3-190">Tokens</span></span>

<span data-ttu-id="4b5b3-191">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens)指定[TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="4b5b3-192">属性</span><span class="sxs-lookup"><span data-stu-id="4b5b3-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="4b5b3-193">说明</span><span class="sxs-lookup"><span data-stu-id="4b5b3-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="4b5b3-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="4b5b3-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="4b5b3-195">获取或设置用于`AuthenticatorTokenProvider`使用验证器验证双重登录的。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="4b5b3-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="4b5b3-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="4b5b3-197">获取或设置用于`ChangeEmailTokenProvider`生成电子邮件更改确认电子邮件中使用的令牌的。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="4b5b3-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="4b5b3-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="4b5b3-199">获取或设置用于`ChangePhoneNumberTokenProvider`生成更改电话号码时使用的令牌的。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="4b5b3-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="4b5b3-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="4b5b3-201">获取或设置用于生成帐户确认电子邮件中使用的令牌的令牌提供程序。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="4b5b3-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="4b5b3-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="4b5b3-203">获取或设置用于生成密码重置电子邮件中使用的令牌的[\<IUserTwoFactorTokenProvider TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) 。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="4b5b3-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="4b5b3-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="4b5b3-205">用于使用用作提供程序名称的密钥构造[用户令牌提供程序](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor)。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="4b5b3-206">用户</span><span class="sxs-lookup"><span data-stu-id="4b5b3-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="4b5b3-207">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user)指定[UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="4b5b3-208">属性</span><span class="sxs-lookup"><span data-stu-id="4b5b3-208">Property</span></span> | <span data-ttu-id="4b5b3-209">说明</span><span class="sxs-lookup"><span data-stu-id="4b5b3-209">Description</span></span> | <span data-ttu-id="4b5b3-210">默认</span><span class="sxs-lookup"><span data-stu-id="4b5b3-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="4b5b3-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="4b5b3-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="4b5b3-212">用户名中允许使用的字符。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-212">Allowed characters in the username.</span></span> | <span data-ttu-id="4b5b3-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="4b5b3-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="4b5b3-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="4b5b3-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="4b5b3-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="4b5b3-215">0123456789</span></span><br><span data-ttu-id="4b5b3-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="4b5b3-216">-.\_@+</span></span> |
| [<span data-ttu-id="4b5b3-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="4b5b3-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="4b5b3-218">要求每个用户都有唯一的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="4b5b3-219">Cookie 设置</span><span class="sxs-lookup"><span data-stu-id="4b5b3-219">Cookie settings</span></span>

<span data-ttu-id="4b5b3-220">在中`Startup.ConfigureServices`配置应用的 cookie。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4b5b3-221">调用`AddIdentity`或`AddDefaultIdentity`**后**，必须调用[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) 。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="4b5b3-222">有关详细信息，请参阅[cookieauthenticationoptions.authenticationtype](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions)。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="4b5b3-223">Password Hasher 选项</span><span class="sxs-lookup"><span data-stu-id="4b5b3-223">Password Hasher options</span></span>

<span data-ttu-id="4b5b3-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions>获取和设置用于密码哈希的选项。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="4b5b3-225">选项</span><span class="sxs-lookup"><span data-stu-id="4b5b3-225">Option</span></span> | <span data-ttu-id="4b5b3-226">说明</span><span class="sxs-lookup"><span data-stu-id="4b5b3-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="4b5b3-227">对新密码进行哈希处理时使用的兼容性模式。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="4b5b3-228">默认为 <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="4b5b3-229">哈希密码的第一个字节称为*格式标记*，它指定用于对密码进行哈希处理的哈希算法的版本。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="4b5b3-230">针对哈希验证密码时，该<xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*>方法会根据第一个字节选择正确的算法。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="4b5b3-231">无论使用哪个版本的算法对密码进行哈希处理，客户端都可以进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="4b5b3-232">设置兼容性模式会影响*新密码*的哈希。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="4b5b3-233">使用 PBKDF2 对密码进行哈希处理时使用的迭代次数。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="4b5b3-234">仅当设置为<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>时，才使用此值。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="4b5b3-235">该值必须是正整数并且默认值为`10000`。</span><span class="sxs-lookup"><span data-stu-id="4b5b3-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="4b5b3-236">在下面的示例中， <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount>将设置为`12000` in `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="4b5b3-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
