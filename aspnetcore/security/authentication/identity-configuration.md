---
title: 配置 ASP.NET Core 标识
author: AdrienTorris
description: 了解 ASP.NET Core 标识默认值，并了解如何配置要使用自定义值的标识属性。
ms.author: riande
ms.date: 02/11/2019
uid: security/authentication/identity-configuration
ms.openlocfilehash: 823182bed2cb953e07f9374d135868aeb2be9c60
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652692"
---
# <a name="configure-aspnet-core-identity"></a><span data-ttu-id="3f69b-103">配置 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="3f69b-103">Configure ASP.NET Core Identity</span></span>

<span data-ttu-id="3f69b-104">ASP.NET Core 标识将默认值用于设置密码策略、锁定和 cookie 配置等设置。</span><span class="sxs-lookup"><span data-stu-id="3f69b-104">ASP.NET Core Identity uses default values for settings such as password policy, lockout, and cookie configuration.</span></span> <span data-ttu-id="3f69b-105">可以在 `Startup` 类中重写这些设置。</span><span class="sxs-lookup"><span data-stu-id="3f69b-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="identity-options"></a><span data-ttu-id="3f69b-106">标识选项</span><span class="sxs-lookup"><span data-stu-id="3f69b-106">Identity options</span></span>

<span data-ttu-id="3f69b-107">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)类表示可用于配置标识系统的选项。</span><span class="sxs-lookup"><span data-stu-id="3f69b-107">The [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the Identity system.</span></span> <span data-ttu-id="3f69b-108">调用 `AddIdentity` 或 `AddDefaultIdentity`**后**必须设置 `IdentityOptions`。</span><span class="sxs-lookup"><span data-stu-id="3f69b-108">`IdentityOptions` must be set **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

### <a name="claims-identity"></a><span data-ttu-id="3f69b-109">声明标识</span><span class="sxs-lookup"><span data-stu-id="3f69b-109">Claims Identity</span></span>

<span data-ttu-id="3f69b-110">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity)指定[ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) ，其中包含下表所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3f69b-110">[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="3f69b-111">properties</span><span class="sxs-lookup"><span data-stu-id="3f69b-111">Property</span></span> | <span data-ttu-id="3f69b-112">说明</span><span class="sxs-lookup"><span data-stu-id="3f69b-112">Description</span></span> | <span data-ttu-id="3f69b-113">默认</span><span class="sxs-lookup"><span data-stu-id="3f69b-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="3f69b-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="3f69b-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="3f69b-115">获取或设置用于角色声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="3f69b-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="3f69b-116">ClaimTypes</span><span class="sxs-lookup"><span data-stu-id="3f69b-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="3f69b-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="3f69b-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="3f69b-118">获取或设置用于安全戳声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="3f69b-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.Identity.SecurityStamp` |
| [<span data-ttu-id="3f69b-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="3f69b-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="3f69b-120">获取或设置用于用户标识符声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="3f69b-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="3f69b-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="3f69b-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="3f69b-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="3f69b-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="3f69b-123">获取或设置用于用户名声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="3f69b-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="3f69b-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="3f69b-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="3f69b-125">锁定</span><span class="sxs-lookup"><span data-stu-id="3f69b-125">Lockout</span></span>

<span data-ttu-id="3f69b-126">在[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_)方法中设置锁定：</span><span class="sxs-lookup"><span data-stu-id="3f69b-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="3f69b-127">前面的代码基于 `Login` 标识模板。</span><span class="sxs-lookup"><span data-stu-id="3f69b-127">The preceding code is based on the `Login` Identity template.</span></span> 

<span data-ttu-id="3f69b-128">在 `StartUp.ConfigureServices`中设置锁定选项：</span><span class="sxs-lookup"><span data-stu-id="3f69b-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="3f69b-129">前面的代码将[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions)设置为默认值。</span><span class="sxs-lookup"><span data-stu-id="3f69b-129">The preceding code sets the [IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="3f69b-130">成功的身份验证失败的访问尝试计数重置并重置时钟。</span><span class="sxs-lookup"><span data-stu-id="3f69b-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="3f69b-131">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout)指定[LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3f69b-131">[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="3f69b-132">properties</span><span class="sxs-lookup"><span data-stu-id="3f69b-132">Property</span></span> | <span data-ttu-id="3f69b-133">说明</span><span class="sxs-lookup"><span data-stu-id="3f69b-133">Description</span></span> | <span data-ttu-id="3f69b-134">默认</span><span class="sxs-lookup"><span data-stu-id="3f69b-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="3f69b-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="3f69b-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="3f69b-136">确定新用户是否可以锁定。</span><span class="sxs-lookup"><span data-stu-id="3f69b-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="3f69b-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="3f69b-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="3f69b-138">锁定发生时用户被锁定的时间长度。</span><span class="sxs-lookup"><span data-stu-id="3f69b-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="3f69b-139">5 分钟</span><span class="sxs-lookup"><span data-stu-id="3f69b-139">5 minutes</span></span> |
| [<span data-ttu-id="3f69b-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="3f69b-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="3f69b-141">如果启用了锁定，则在用户被锁定之前失败的访问尝试次数。</span><span class="sxs-lookup"><span data-stu-id="3f69b-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="3f69b-142">5</span><span class="sxs-lookup"><span data-stu-id="3f69b-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="3f69b-143">密码</span><span class="sxs-lookup"><span data-stu-id="3f69b-143">Password</span></span>

<span data-ttu-id="3f69b-144">默认情况下，Identity 要求密码包含大写字符、小写字符、数字以及非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="3f69b-144">By default, Identity requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="3f69b-145">密码长度必须至少为6个字符。</span><span class="sxs-lookup"><span data-stu-id="3f69b-145">Passwords must be at least six characters long.</span></span> <span data-ttu-id="3f69b-146">可以 `Startup.ConfigureServices`中设置[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) 。</span><span class="sxs-lookup"><span data-stu-id="3f69b-146">[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) can be set in `Startup.ConfigureServices`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

<span data-ttu-id="3f69b-147">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password)指定[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3f69b-147">[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

::: moniker range=">= aspnetcore-2.0"

| <span data-ttu-id="3f69b-148">properties</span><span class="sxs-lookup"><span data-stu-id="3f69b-148">Property</span></span> | <span data-ttu-id="3f69b-149">说明</span><span class="sxs-lookup"><span data-stu-id="3f69b-149">Description</span></span> | <span data-ttu-id="3f69b-150">默认</span><span class="sxs-lookup"><span data-stu-id="3f69b-150">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="3f69b-151">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="3f69b-151">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="3f69b-152">要求密码中的数字介于0-9 之间。</span><span class="sxs-lookup"><span data-stu-id="3f69b-152">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="3f69b-153">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="3f69b-153">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="3f69b-154">密码的最小长度。</span><span class="sxs-lookup"><span data-stu-id="3f69b-154">The minimum length of the password.</span></span> | <span data-ttu-id="3f69b-155">6</span><span class="sxs-lookup"><span data-stu-id="3f69b-155">6</span></span> |
| [<span data-ttu-id="3f69b-156">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="3f69b-156">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="3f69b-157">密码中需要小写字符。</span><span class="sxs-lookup"><span data-stu-id="3f69b-157">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="3f69b-158">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="3f69b-158">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="3f69b-159">密码中需要一个非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="3f69b-159">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="3f69b-160">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="3f69b-160">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="3f69b-161">仅适用于 ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3f69b-161">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="3f69b-162">需要密码中的非重复字符数。</span><span class="sxs-lookup"><span data-stu-id="3f69b-162">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="3f69b-163">1</span><span class="sxs-lookup"><span data-stu-id="3f69b-163">1</span></span> |
| [<span data-ttu-id="3f69b-164">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="3f69b-164">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="3f69b-165">密码中需要大写字符。</span><span class="sxs-lookup"><span data-stu-id="3f69b-165">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| <span data-ttu-id="3f69b-166">properties</span><span class="sxs-lookup"><span data-stu-id="3f69b-166">Property</span></span> | <span data-ttu-id="3f69b-167">说明</span><span class="sxs-lookup"><span data-stu-id="3f69b-167">Description</span></span> | <span data-ttu-id="3f69b-168">默认</span><span class="sxs-lookup"><span data-stu-id="3f69b-168">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="3f69b-169">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="3f69b-169">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="3f69b-170">要求密码中的数字介于0-9 之间。</span><span class="sxs-lookup"><span data-stu-id="3f69b-170">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="3f69b-171">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="3f69b-171">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="3f69b-172">密码的最小长度。</span><span class="sxs-lookup"><span data-stu-id="3f69b-172">The minimum length of the password.</span></span> | <span data-ttu-id="3f69b-173">6</span><span class="sxs-lookup"><span data-stu-id="3f69b-173">6</span></span> |
| [<span data-ttu-id="3f69b-174">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="3f69b-174">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="3f69b-175">密码中需要小写字符。</span><span class="sxs-lookup"><span data-stu-id="3f69b-175">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="3f69b-176">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="3f69b-176">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="3f69b-177">密码中需要一个非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="3f69b-177">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="3f69b-178">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="3f69b-178">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="3f69b-179">密码中需要大写字符。</span><span class="sxs-lookup"><span data-stu-id="3f69b-179">Requires an uppercase character in the password.</span></span> | `true` |

::: moniker-end

### <a name="sign-in"></a><span data-ttu-id="3f69b-180">登录</span><span class="sxs-lookup"><span data-stu-id="3f69b-180">Sign-in</span></span>

<span data-ttu-id="3f69b-181">下面的代码将 `SignIn` 设置（设置为默认值）：</span><span class="sxs-lookup"><span data-stu-id="3f69b-181">The following code sets `SignIn` settings (to default values):</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

<span data-ttu-id="3f69b-182">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin)指定[SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3f69b-182">[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="3f69b-183">properties</span><span class="sxs-lookup"><span data-stu-id="3f69b-183">Property</span></span> | <span data-ttu-id="3f69b-184">说明</span><span class="sxs-lookup"><span data-stu-id="3f69b-184">Description</span></span> | <span data-ttu-id="3f69b-185">默认</span><span class="sxs-lookup"><span data-stu-id="3f69b-185">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="3f69b-186">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="3f69b-186">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="3f69b-187">需要确认电子邮件登录。</span><span class="sxs-lookup"><span data-stu-id="3f69b-187">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="3f69b-188">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="3f69b-188">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="3f69b-189">需要确认电话号码才能登录。</span><span class="sxs-lookup"><span data-stu-id="3f69b-189">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="3f69b-190">令牌</span><span class="sxs-lookup"><span data-stu-id="3f69b-190">Tokens</span></span>

<span data-ttu-id="3f69b-191">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens)指定[TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3f69b-191">[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

|                                                        <span data-ttu-id="3f69b-192">properties</span><span class="sxs-lookup"><span data-stu-id="3f69b-192">Property</span></span>                                                         |                                                                                      <span data-ttu-id="3f69b-193">说明</span><span class="sxs-lookup"><span data-stu-id="3f69b-193">Description</span></span>                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [<span data-ttu-id="3f69b-194">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="3f69b-194">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       <span data-ttu-id="3f69b-195">获取或设置用于使用验证器验证双重登录的 `AuthenticatorTokenProvider`。</span><span class="sxs-lookup"><span data-stu-id="3f69b-195">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span>                                       |
|       [<span data-ttu-id="3f69b-196">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="3f69b-196">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     <span data-ttu-id="3f69b-197">获取或设置用于生成电子邮件更改确认电子邮件中使用的令牌的 `ChangeEmailTokenProvider`。</span><span class="sxs-lookup"><span data-stu-id="3f69b-197">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span>                                     |
| [<span data-ttu-id="3f69b-198">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="3f69b-198">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      <span data-ttu-id="3f69b-199">获取或设置用于生成更改电话号码时使用的令牌的 `ChangePhoneNumberTokenProvider`。</span><span class="sxs-lookup"><span data-stu-id="3f69b-199">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span>                                      |
| [<span data-ttu-id="3f69b-200">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="3f69b-200">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             <span data-ttu-id="3f69b-201">获取或设置用于生成帐户确认电子邮件中使用的令牌的令牌提供程序。</span><span class="sxs-lookup"><span data-stu-id="3f69b-201">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span>                                              |
|     [<span data-ttu-id="3f69b-202">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="3f69b-202">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | <span data-ttu-id="3f69b-203">获取或设置用于生成密码重置电子邮件中使用的令牌的[IUserTwoFactorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) 。</span><span class="sxs-lookup"><span data-stu-id="3f69b-203">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
|                    [<span data-ttu-id="3f69b-204">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="3f69b-204">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                <span data-ttu-id="3f69b-205">用于使用用作提供程序名称的密钥构造[用户令牌提供程序](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor)。</span><span class="sxs-lookup"><span data-stu-id="3f69b-205">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span>                 |

### <a name="user"></a><span data-ttu-id="3f69b-206">用户</span><span class="sxs-lookup"><span data-stu-id="3f69b-206">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="3f69b-207">[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user)指定[UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3f69b-207">[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="3f69b-208">properties</span><span class="sxs-lookup"><span data-stu-id="3f69b-208">Property</span></span> | <span data-ttu-id="3f69b-209">说明</span><span class="sxs-lookup"><span data-stu-id="3f69b-209">Description</span></span> | <span data-ttu-id="3f69b-210">默认</span><span class="sxs-lookup"><span data-stu-id="3f69b-210">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="3f69b-211">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="3f69b-211">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="3f69b-212">用户名中允许使用的字符。</span><span class="sxs-lookup"><span data-stu-id="3f69b-212">Allowed characters in the username.</span></span> | <span data-ttu-id="3f69b-213">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="3f69b-213">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="3f69b-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="3f69b-214">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="3f69b-215">0123456789</span><span class="sxs-lookup"><span data-stu-id="3f69b-215">0123456789</span></span><br><span data-ttu-id="3f69b-216">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="3f69b-216">-.\_@+</span></span> |
| [<span data-ttu-id="3f69b-217">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="3f69b-217">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="3f69b-218">要求每个用户都有唯一的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="3f69b-218">Requires each user to have a unique email.</span></span> | `false` |

### <a name="cookie-settings"></a><span data-ttu-id="3f69b-219">Cookie 设置</span><span class="sxs-lookup"><span data-stu-id="3f69b-219">Cookie settings</span></span>

<span data-ttu-id="3f69b-220">在 `Startup.ConfigureServices`中配置应用的 cookie。</span><span class="sxs-lookup"><span data-stu-id="3f69b-220">Configure the app's cookie in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3f69b-221">调用 `AddIdentity` 或 `AddDefaultIdentity`**后**，必须调用[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) 。</span><span class="sxs-lookup"><span data-stu-id="3f69b-221">[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) must be called **after** calling `AddIdentity` or `AddDefaultIdentity`.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

<span data-ttu-id="3f69b-222">有关详细信息，请参阅[cookieauthenticationoptions.authenticationtype](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions)。</span><span class="sxs-lookup"><span data-stu-id="3f69b-222">For more information, see [CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="3f69b-223">Password Hasher 选项</span><span class="sxs-lookup"><span data-stu-id="3f69b-223">Password Hasher options</span></span>

<span data-ttu-id="3f69b-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> 获取和设置用于密码哈希的选项。</span><span class="sxs-lookup"><span data-stu-id="3f69b-224"><xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="3f69b-225">选项</span><span class="sxs-lookup"><span data-stu-id="3f69b-225">Option</span></span> | <span data-ttu-id="3f69b-226">说明</span><span class="sxs-lookup"><span data-stu-id="3f69b-226">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="3f69b-227">对新密码进行哈希处理时使用的兼容性模式。</span><span class="sxs-lookup"><span data-stu-id="3f69b-227">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="3f69b-228">默认为 <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>。</span><span class="sxs-lookup"><span data-stu-id="3f69b-228">Defaults to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="3f69b-229">哈希密码的第一个字节称为*格式标记*，它指定用于对密码进行哈希处理的哈希算法的版本。</span><span class="sxs-lookup"><span data-stu-id="3f69b-229">The first byte of a hashed password, called a *format marker*, specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="3f69b-230">针对哈希验证密码时，<xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> 方法基于第一个字节选择正确的算法。</span><span class="sxs-lookup"><span data-stu-id="3f69b-230">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="3f69b-231">无论使用哪个版本的算法对密码进行哈希处理，客户端都可以进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="3f69b-231">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="3f69b-232">设置兼容性模式会影响*新密码*的哈希。</span><span class="sxs-lookup"><span data-stu-id="3f69b-232">Setting the compatibility mode affects the hashing of *new passwords*.</span></span> |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | <span data-ttu-id="3f69b-233">使用 PBKDF2 对密码进行哈希处理时使用的迭代次数。</span><span class="sxs-lookup"><span data-stu-id="3f69b-233">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="3f69b-234">仅当 <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> 设置为 <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>时，才使用此值。</span><span class="sxs-lookup"><span data-stu-id="3f69b-234">This value is only used when the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>.</span></span> <span data-ttu-id="3f69b-235">该值必须是一个正整数，默认值为 `10000`。</span><span class="sxs-lookup"><span data-stu-id="3f69b-235">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="3f69b-236">在下面的示例中，将 <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> 设置为 `Startup.ConfigureServices`中 `12000`：</span><span class="sxs-lookup"><span data-stu-id="3f69b-236">In the following example, the <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
