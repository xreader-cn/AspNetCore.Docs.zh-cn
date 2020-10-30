---
title: '配置 :::no-loc(ASP.NET Core Identity):::'
author: AdrienTorris
description: '了解 :::no-loc(ASP.NET Core Identity)::: 默认值并了解如何配置 :::no-loc(Identity)::: 属性以使用自定义值。'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/identity-configuration
ms.openlocfilehash: b11a2d584b7275a9065c9915021ac945823531f8
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051988"
---
# <a name="configure-no-locaspnet-core-identity"></a><span data-ttu-id="3a271-103">配置 :::no-loc(ASP.NET Core Identity):::</span><span class="sxs-lookup"><span data-stu-id="3a271-103">Configure :::no-loc(ASP.NET Core Identity):::</span></span>

<span data-ttu-id="3a271-104">:::no-loc(ASP.NET Core Identity)::: 使用密码策略、锁定和配置等设置的默认值 :::no-loc(cookie)::: 。</span><span class="sxs-lookup"><span data-stu-id="3a271-104">:::no-loc(ASP.NET Core Identity)::: uses default values for settings such as password policy, lockout, and :::no-loc(cookie)::: configuration.</span></span> <span data-ttu-id="3a271-105">可以在类中重写这些设置 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="3a271-105">These settings can be overridden in the `Startup` class.</span></span>

## <a name="no-locidentity-options"></a><span data-ttu-id="3a271-106">:::no-loc(Identity)::: 选项</span><span class="sxs-lookup"><span data-stu-id="3a271-106">:::no-loc(Identity)::: options</span></span>

<span data-ttu-id="3a271-107">[ :::no-loc(Identity)::: Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)类表示可用于配置系统的选项 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="3a271-107">The [:::no-loc(Identity):::Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) class represents the options that can be used to configure the :::no-loc(Identity)::: system.</span></span> <span data-ttu-id="3a271-108">`:::no-loc(Identity):::Options` 必须 **在** 调用或之后 `Add:::no-loc(Identity):::` 设置 `AddDefault:::no-loc(Identity):::` 。</span><span class="sxs-lookup"><span data-stu-id="3a271-108">`:::no-loc(Identity):::Options` must be set **after** calling `Add:::no-loc(Identity):::` or `AddDefault:::no-loc(Identity):::`.</span></span>

### <a name="claims-no-locidentity"></a><span data-ttu-id="3a271-109">支付 :::no-loc(Identity):::</span><span class="sxs-lookup"><span data-stu-id="3a271-109">Claims :::no-loc(Identity):::</span></span>

<span data-ttu-id="3a271-110">[ :::no-loc(Identity)::: Options。声明 :::no-loc(Identity)::: ](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity)指定[声明 :::no-loc(Identity)::: 选项](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions)，其中包含下表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3a271-110">[:::no-loc(Identity):::Options.Claims:::no-loc(Identity):::](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity) specifies the [Claims:::no-loc(Identity):::Options](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) with the properties shown in the following table.</span></span>

| <span data-ttu-id="3a271-111">属性</span><span class="sxs-lookup"><span data-stu-id="3a271-111">Property</span></span> | <span data-ttu-id="3a271-112">说明</span><span class="sxs-lookup"><span data-stu-id="3a271-112">Description</span></span> | <span data-ttu-id="3a271-113">默认</span><span class="sxs-lookup"><span data-stu-id="3a271-113">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="3a271-114">RoleClaimType</span><span class="sxs-lookup"><span data-stu-id="3a271-114">RoleClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | <span data-ttu-id="3a271-115">获取或设置用于角色声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="3a271-115">Gets or sets the claim type used for a role claim.</span></span> | [<span data-ttu-id="3a271-116">ClaimTypes</span><span class="sxs-lookup"><span data-stu-id="3a271-116">ClaimTypes.Role</span></span>](/dotnet/api/system.security.claims.claimtypes.role) |
| [<span data-ttu-id="3a271-117">SecurityStampClaimType</span><span class="sxs-lookup"><span data-stu-id="3a271-117">SecurityStampClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | <span data-ttu-id="3a271-118">获取或设置用于安全戳声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="3a271-118">Gets or sets the claim type used for the security stamp claim.</span></span> | `AspNet.:::no-loc(Identity):::.SecurityStamp` |
| [<span data-ttu-id="3a271-119">UserIdClaimType</span><span class="sxs-lookup"><span data-stu-id="3a271-119">UserIdClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | <span data-ttu-id="3a271-120">获取或设置用于用户标识符声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="3a271-120">Gets or sets the claim type used for the user identifier claim.</span></span> | [<span data-ttu-id="3a271-121">ClaimTypes. NameIdentifier</span><span class="sxs-lookup"><span data-stu-id="3a271-121">ClaimTypes.NameIdentifier</span></span>](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [<span data-ttu-id="3a271-122">UserNameClaimType</span><span class="sxs-lookup"><span data-stu-id="3a271-122">UserNameClaimType</span></span>](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | <span data-ttu-id="3a271-123">获取或设置用于用户名声明的声明类型。</span><span class="sxs-lookup"><span data-stu-id="3a271-123">Gets or sets the claim type used for the user name claim.</span></span> | [<span data-ttu-id="3a271-124">ClaimTypes.Name</span><span class="sxs-lookup"><span data-stu-id="3a271-124">ClaimTypes.Name</span></span>](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a><span data-ttu-id="3a271-125">锁定</span><span class="sxs-lookup"><span data-stu-id="3a271-125">Lockout</span></span>

<span data-ttu-id="3a271-126">在 [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) 方法中设置锁定：</span><span class="sxs-lookup"><span data-stu-id="3a271-126">Lockout is set in the [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_:::no-loc(Identity):::_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_) method:</span></span>

[!code-csharp[](identity-configuration/sample/Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="3a271-127">前面的代码基于 `Login` :::no-loc(Identity)::: 模板。</span><span class="sxs-lookup"><span data-stu-id="3a271-127">The preceding code is based on the `Login` :::no-loc(Identity)::: template.</span></span> 

<span data-ttu-id="3a271-128">锁定选项设置为 `StartUp.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="3a271-128">Lockout options are set in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

<span data-ttu-id="3a271-129">前面的代码将[ :::no-loc(Identity)::: 选项](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions)设置为默认值。</span><span class="sxs-lookup"><span data-stu-id="3a271-129">The preceding code sets the [:::no-loc(Identity):::Options](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with default values.</span></span>

<span data-ttu-id="3a271-130">身份验证成功后，将重置失败的访问尝试计数并重置时钟。</span><span class="sxs-lookup"><span data-stu-id="3a271-130">A successful authentication resets the failed access attempts count and resets the clock.</span></span>

<span data-ttu-id="3a271-131">[ :::no-loc(Identity)::: 选项。锁定](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout)将指定[LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3a271-131">[:::no-loc(Identity):::Options.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout) specifies the [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="3a271-132">属性</span><span class="sxs-lookup"><span data-stu-id="3a271-132">Property</span></span> | <span data-ttu-id="3a271-133">说明</span><span class="sxs-lookup"><span data-stu-id="3a271-133">Description</span></span> | <span data-ttu-id="3a271-134">默认</span><span class="sxs-lookup"><span data-stu-id="3a271-134">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="3a271-135">AllowedForNewUsers</span><span class="sxs-lookup"><span data-stu-id="3a271-135">AllowedForNewUsers</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | <span data-ttu-id="3a271-136">确定新用户是否可以锁定。</span><span class="sxs-lookup"><span data-stu-id="3a271-136">Determines if a new user can be locked out.</span></span> | `true` |
| [<span data-ttu-id="3a271-137">DefaultLockoutTimeSpan</span><span class="sxs-lookup"><span data-stu-id="3a271-137">DefaultLockoutTimeSpan</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | <span data-ttu-id="3a271-138">锁定发生时用户被锁定的时间长度。</span><span class="sxs-lookup"><span data-stu-id="3a271-138">The amount of time a user is locked out when a lockout occurs.</span></span> | <span data-ttu-id="3a271-139">5 分钟</span><span class="sxs-lookup"><span data-stu-id="3a271-139">5 minutes</span></span> |
| [<span data-ttu-id="3a271-140">MaxFailedAccessAttempts</span><span class="sxs-lookup"><span data-stu-id="3a271-140">MaxFailedAccessAttempts</span></span>](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | <span data-ttu-id="3a271-141">如果启用了锁定，则在用户被锁定之前失败的访问尝试次数。</span><span class="sxs-lookup"><span data-stu-id="3a271-141">The number of failed access attempts until a user is locked out, if lockout is enabled.</span></span> | <span data-ttu-id="3a271-142">5</span><span class="sxs-lookup"><span data-stu-id="3a271-142">5</span></span> |

### <a name="password"></a><span data-ttu-id="3a271-143">密码</span><span class="sxs-lookup"><span data-stu-id="3a271-143">Password</span></span>

<span data-ttu-id="3a271-144">默认情况下， :::no-loc(Identity)::: 要求密码包含大写字符、小写字符、数字以及非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="3a271-144">By default, :::no-loc(Identity)::: requires that passwords contain an uppercase character, lowercase character, a digit, and a non-alphanumeric character.</span></span> <span data-ttu-id="3a271-145">密码长度必须至少为6个字符。</span><span class="sxs-lookup"><span data-stu-id="3a271-145">Passwords must be at least six characters long.</span></span>

<span data-ttu-id="3a271-146">密码配置为：</span><span class="sxs-lookup"><span data-stu-id="3a271-146">Passwords are configured with:</span></span>

* <span data-ttu-id="3a271-147">`Startup.ConfigureServices` 中的 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions>。</span><span class="sxs-lookup"><span data-stu-id="3a271-147"><xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="3a271-148">[ `[StringLength]` ](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) `Password` 如果 :::no-loc(Identity)::: 将[基架到应用程序中](xref:security/authentication/scaffold-identity)，则为属性的属性。</span><span class="sxs-lookup"><span data-stu-id="3a271-148">[`[StringLength]` attributes](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) of `Password` properties if :::no-loc(Identity)::: is [scaffolded into the app](xref:security/authentication/scaffold-identity).</span></span> <span data-ttu-id="3a271-149">`InputModel``Password`在以下文件中可以找到属性：</span><span class="sxs-lookup"><span data-stu-id="3a271-149">`InputModel` `Password` properties are found in the following files:</span></span>
  * `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
  * `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

<span data-ttu-id="3a271-150">[ :::no-loc(Identity)::: Options. Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password)指定[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3a271-150">[:::no-loc(Identity):::Options.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password) specifies the [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="3a271-151">属性</span><span class="sxs-lookup"><span data-stu-id="3a271-151">Property</span></span> | <span data-ttu-id="3a271-152">说明</span><span class="sxs-lookup"><span data-stu-id="3a271-152">Description</span></span> | <span data-ttu-id="3a271-153">默认</span><span class="sxs-lookup"><span data-stu-id="3a271-153">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="3a271-154">RequireDigit</span><span class="sxs-lookup"><span data-stu-id="3a271-154">RequireDigit</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | <span data-ttu-id="3a271-155">要求密码中的数字介于0-9 之间。</span><span class="sxs-lookup"><span data-stu-id="3a271-155">Requires a number between 0-9 in the password.</span></span> | `true` |
| [<span data-ttu-id="3a271-156">RequiredLength</span><span class="sxs-lookup"><span data-stu-id="3a271-156">RequiredLength</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | <span data-ttu-id="3a271-157">密码的最小长度。</span><span class="sxs-lookup"><span data-stu-id="3a271-157">The minimum length of the password.</span></span> | <span data-ttu-id="3a271-158">6</span><span class="sxs-lookup"><span data-stu-id="3a271-158">6</span></span> |
| [<span data-ttu-id="3a271-159">RequireLowercase</span><span class="sxs-lookup"><span data-stu-id="3a271-159">RequireLowercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | <span data-ttu-id="3a271-160">密码中需要小写字符。</span><span class="sxs-lookup"><span data-stu-id="3a271-160">Requires a lowercase character in the password.</span></span> | `true` |
| [<span data-ttu-id="3a271-161">RequireNonAlphanumeric</span><span class="sxs-lookup"><span data-stu-id="3a271-161">RequireNonAlphanumeric</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | <span data-ttu-id="3a271-162">密码中需要一个非字母数字字符。</span><span class="sxs-lookup"><span data-stu-id="3a271-162">Requires a non-alphanumeric character in the password.</span></span> | `true` |
| [<span data-ttu-id="3a271-163">RequiredUniqueChars</span><span class="sxs-lookup"><span data-stu-id="3a271-163">RequiredUniqueChars</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | <span data-ttu-id="3a271-164">仅适用于 ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3a271-164">Only applies to ASP.NET Core 2.0 or later.</span></span><br><br> <span data-ttu-id="3a271-165">需要密码中的非重复字符数。</span><span class="sxs-lookup"><span data-stu-id="3a271-165">Requires the number of distinct characters in the password.</span></span> | <span data-ttu-id="3a271-166">1</span><span class="sxs-lookup"><span data-stu-id="3a271-166">1</span></span> |
| [<span data-ttu-id="3a271-167">RequireUppercase</span><span class="sxs-lookup"><span data-stu-id="3a271-167">RequireUppercase</span></span>](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | <span data-ttu-id="3a271-168">密码中需要大写字符。</span><span class="sxs-lookup"><span data-stu-id="3a271-168">Requires an uppercase character in the password.</span></span> | `true` |

### <a name="sign-in"></a><span data-ttu-id="3a271-169">登录</span><span class="sxs-lookup"><span data-stu-id="3a271-169">Sign-in</span></span>

<span data-ttu-id="3a271-170">下面的代码将 `SignIn` 设置 (设置为默认值) ：</span><span class="sxs-lookup"><span data-stu-id="3a271-170">The following code sets `SignIn` settings (to default values):</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

<span data-ttu-id="3a271-171">" [ :::no-loc(Identity)::: 登录](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin)" 指定[SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3a271-171">[:::no-loc(Identity):::Options.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin) specifies the [SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="3a271-172">属性</span><span class="sxs-lookup"><span data-stu-id="3a271-172">Property</span></span> | <span data-ttu-id="3a271-173">说明</span><span class="sxs-lookup"><span data-stu-id="3a271-173">Description</span></span> | <span data-ttu-id="3a271-174">默认</span><span class="sxs-lookup"><span data-stu-id="3a271-174">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="3a271-175">RequireConfirmedEmail</span><span class="sxs-lookup"><span data-stu-id="3a271-175">RequireConfirmedEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | <span data-ttu-id="3a271-176">需要确认电子邮件登录。</span><span class="sxs-lookup"><span data-stu-id="3a271-176">Requires a confirmed email to sign in.</span></span> | `false` |
| [<span data-ttu-id="3a271-177">RequireConfirmedPhoneNumber</span><span class="sxs-lookup"><span data-stu-id="3a271-177">RequireConfirmedPhoneNumber</span></span>](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | <span data-ttu-id="3a271-178">需要确认电话号码才能登录。</span><span class="sxs-lookup"><span data-stu-id="3a271-178">Requires a confirmed phone number to sign in.</span></span> | `false` |

### <a name="tokens"></a><span data-ttu-id="3a271-179">令牌</span><span class="sxs-lookup"><span data-stu-id="3a271-179">Tokens</span></span>

<span data-ttu-id="3a271-180">[ :::no-loc(Identity)::: 选项。标记](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens)指定[TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3a271-180">[:::no-loc(Identity):::Options.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens) specifies the [TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="3a271-181">属性</span><span class="sxs-lookup"><span data-stu-id="3a271-181">Property</span></span> | <span data-ttu-id="3a271-182">说明</span><span class="sxs-lookup"><span data-stu-id="3a271-182">Description</span></span> |
| -------- | ----------- |
| [<span data-ttu-id="3a271-183">AuthenticatorTokenProvider</span><span class="sxs-lookup"><span data-stu-id="3a271-183">AuthenticatorTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider) | <span data-ttu-id="3a271-184">获取或设置 `AuthenticatorTokenProvider` 用于使用验证器验证双重登录的。</span><span class="sxs-lookup"><span data-stu-id="3a271-184">Gets or sets the `AuthenticatorTokenProvider` used to validate two-factor sign-ins with an authenticator.</span></span> |
| [<span data-ttu-id="3a271-185">ChangeEmailTokenProvider</span><span class="sxs-lookup"><span data-stu-id="3a271-185">ChangeEmailTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider) | <span data-ttu-id="3a271-186">获取或设置 `ChangeEmailTokenProvider` 用于生成电子邮件更改确认电子邮件中使用的令牌的。</span><span class="sxs-lookup"><span data-stu-id="3a271-186">Gets or sets the `ChangeEmailTokenProvider` used to generate tokens used in email change confirmation emails.</span></span> |
| [<span data-ttu-id="3a271-187">ChangePhoneNumberTokenProvider</span><span class="sxs-lookup"><span data-stu-id="3a271-187">ChangePhoneNumberTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) | <span data-ttu-id="3a271-188">获取或设置 `ChangePhoneNumberTokenProvider` 用于生成更改电话号码时使用的令牌的。</span><span class="sxs-lookup"><span data-stu-id="3a271-188">Gets or sets the `ChangePhoneNumberTokenProvider` used to generate tokens used when changing phone numbers.</span></span> |
| [<span data-ttu-id="3a271-189">EmailConfirmationTokenProvider</span><span class="sxs-lookup"><span data-stu-id="3a271-189">EmailConfirmationTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) | <span data-ttu-id="3a271-190">获取或设置用于生成帐户确认电子邮件中使用的令牌的令牌提供程序。</span><span class="sxs-lookup"><span data-stu-id="3a271-190">Gets or sets the token provider used to generate tokens used in account confirmation emails.</span></span> |
| [<span data-ttu-id="3a271-191">PasswordResetTokenProvider</span><span class="sxs-lookup"><span data-stu-id="3a271-191">PasswordResetTokenProvider</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider) | <span data-ttu-id="3a271-192">获取或设置用于生成密码重置电子邮件中使用的令牌的[IUserTwoFactorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) 。</span><span class="sxs-lookup"><span data-stu-id="3a271-192">Gets or sets the [IUserTwoFactorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) used to generate tokens used in password reset emails.</span></span> |
| [<span data-ttu-id="3a271-193">ProviderMap</span><span class="sxs-lookup"><span data-stu-id="3a271-193">ProviderMap</span></span>](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap) | <span data-ttu-id="3a271-194">用于使用用作提供程序名称的密钥构造 [用户令牌提供程序](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) 。</span><span class="sxs-lookup"><span data-stu-id="3a271-194">Used to construct a [User Token Provider](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor) with the key used as the provider's name.</span></span> |

### <a name="user"></a><span data-ttu-id="3a271-195">用户</span><span class="sxs-lookup"><span data-stu-id="3a271-195">User</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

<span data-ttu-id="3a271-196">[ :::no-loc(Identity)::: Options。 User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user)指定[UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) ，其中包含表中所示的属性。</span><span class="sxs-lookup"><span data-stu-id="3a271-196">[:::no-loc(Identity):::Options.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user) specifies the [UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) with the properties shown in the table.</span></span>

| <span data-ttu-id="3a271-197">属性</span><span class="sxs-lookup"><span data-stu-id="3a271-197">Property</span></span> | <span data-ttu-id="3a271-198">说明</span><span class="sxs-lookup"><span data-stu-id="3a271-198">Description</span></span> | <span data-ttu-id="3a271-199">默认</span><span class="sxs-lookup"><span data-stu-id="3a271-199">Default</span></span> |
| -------- | ----------- | :-----: |
| [<span data-ttu-id="3a271-200">AllowedUserNameCharacters</span><span class="sxs-lookup"><span data-stu-id="3a271-200">AllowedUserNameCharacters</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | <span data-ttu-id="3a271-201">用户名中允许使用的字符。</span><span class="sxs-lookup"><span data-stu-id="3a271-201">Allowed characters in the username.</span></span> | <span data-ttu-id="3a271-202">abcdefghijklmnopqrstuvwxyz</span><span class="sxs-lookup"><span data-stu-id="3a271-202">abcdefghijklmnopqrstuvwxyz</span></span><br><span data-ttu-id="3a271-203">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span><span class="sxs-lookup"><span data-stu-id="3a271-203">ABCDEFGHIJKLMNOPQRSTUVWXYZ</span></span><br><span data-ttu-id="3a271-204">0123456789</span><span class="sxs-lookup"><span data-stu-id="3a271-204">0123456789</span></span><br><span data-ttu-id="3a271-205">-.\_@+</span><span class="sxs-lookup"><span data-stu-id="3a271-205">-.\_@+</span></span> |
| [<span data-ttu-id="3a271-206">RequireUniqueEmail</span><span class="sxs-lookup"><span data-stu-id="3a271-206">RequireUniqueEmail</span></span>](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | <span data-ttu-id="3a271-207">要求每个用户都有唯一的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="3a271-207">Requires each user to have a unique email.</span></span> | `false` |

### <a name="no-loccookie-settings"></a><span data-ttu-id="3a271-208">:::no-loc(Cookie)::: 设置</span><span class="sxs-lookup"><span data-stu-id="3a271-208">:::no-loc(Cookie)::: settings</span></span>

<span data-ttu-id="3a271-209">在中配置应用 :::no-loc(cookie)::: 程序 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="3a271-209">Configure the app's :::no-loc(cookie)::: in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="3a271-210">[ConfigureApplication :::no-loc(Cookie):::](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplication:::no-loc(cookie):::#Microsoft_Extensions_DependencyInjection_:::no-loc(Identity):::ServiceCollectionExtensions_ConfigureApplication:::no-loc(Cookie):::_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_:::no-loc(Cookie):::s_:::no-loc(Cookie):::AuthenticationOptions__)调用或 **后** 必须调用 `Add:::no-loc(Identity):::` `AddDefault:::no-loc(Identity):::` 。</span><span class="sxs-lookup"><span data-stu-id="3a271-210">[ConfigureApplication:::no-loc(Cookie):::](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplication:::no-loc(cookie):::#Microsoft_Extensions_DependencyInjection_:::no-loc(Identity):::ServiceCollectionExtensions_ConfigureApplication:::no-loc(Cookie):::_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_:::no-loc(Cookie):::s_:::no-loc(Cookie):::AuthenticationOptions__) must be called **after** calling `Add:::no-loc(Identity):::` or `AddDefault:::no-loc(Identity):::`.</span></span>

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_:::no-loc(cookie):::)]

<span data-ttu-id="3a271-211">有关详细信息，请参阅[ :::no-loc(Cookie)::: AuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions)。</span><span class="sxs-lookup"><span data-stu-id="3a271-211">For more information, see [:::no-loc(Cookie):::AuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions).</span></span>

## <a name="password-hasher-options"></a><span data-ttu-id="3a271-212">Password Hasher 选项</span><span class="sxs-lookup"><span data-stu-id="3a271-212">Password Hasher options</span></span>

<span data-ttu-id="3a271-213"><xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions> 获取和设置用于密码哈希的选项。</span><span class="sxs-lookup"><span data-stu-id="3a271-213"><xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions> gets and sets options for password hashing.</span></span>

| <span data-ttu-id="3a271-214">选项</span><span class="sxs-lookup"><span data-stu-id="3a271-214">Option</span></span> | <span data-ttu-id="3a271-215">说明</span><span class="sxs-lookup"><span data-stu-id="3a271-215">Description</span></span> |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.CompatibilityMode> | <span data-ttu-id="3a271-216">对新密码进行哈希处理时使用的兼容性模式。</span><span class="sxs-lookup"><span data-stu-id="3a271-216">The compatibility mode used when hashing new passwords.</span></span> <span data-ttu-id="3a271-217">默认为 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherCompatibilityMode.:::no-loc(Identity):::V3>。</span><span class="sxs-lookup"><span data-stu-id="3a271-217">Defaults to <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherCompatibilityMode.:::no-loc(Identity):::V3>.</span></span> <span data-ttu-id="3a271-218">哈希密码的第一个字节称为 *格式标记* ，它指定用于对密码进行哈希处理的哈希算法的版本。</span><span class="sxs-lookup"><span data-stu-id="3a271-218">The first byte of a hashed password, called a *format marker* , specifies the version of the hashing algorithm used to hash the password.</span></span> <span data-ttu-id="3a271-219">针对哈希验证密码时，该方法会根据 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasher`1.VerifyHashedPassword*> 第一个字节选择正确的算法。</span><span class="sxs-lookup"><span data-stu-id="3a271-219">When verifying a password against a hash, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasher`1.VerifyHashedPassword*> method selects the correct algorithm based on the first byte.</span></span> <span data-ttu-id="3a271-220">无论使用哪个版本的算法对密码进行哈希处理，客户端都可以进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="3a271-220">A client is able to authenticate regardless of which version of the algorithm was used to hash the password.</span></span> <span data-ttu-id="3a271-221">设置兼容性模式会影响 *新密码* 的哈希。</span><span class="sxs-lookup"><span data-stu-id="3a271-221">Setting the compatibility mode affects the hashing of *new passwords* .</span></span> |
| <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.IterationCount> | <span data-ttu-id="3a271-222">使用 PBKDF2 对密码进行哈希处理时使用的迭代次数。</span><span class="sxs-lookup"><span data-stu-id="3a271-222">The number of iterations used when hashing passwords using PBKDF2.</span></span> <span data-ttu-id="3a271-223">仅当设置为时，才使用此值 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.CompatibilityMode> <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherCompatibilityMode.:::no-loc(Identity):::V3> 。</span><span class="sxs-lookup"><span data-stu-id="3a271-223">This value is only used when the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.CompatibilityMode> is set to <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherCompatibilityMode.:::no-loc(Identity):::V3>.</span></span> <span data-ttu-id="3a271-224">该值必须是正整数并且默认值为 `10000` 。</span><span class="sxs-lookup"><span data-stu-id="3a271-224">The value must be a positive integer and defaults to `10000`.</span></span> |

<span data-ttu-id="3a271-225">在下面的示例中，将 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.IterationCount> 设置为 `12000` in `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="3a271-225">In the following example, the <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordHasherOptions.IterationCount> is set to `12000` in `Startup.ConfigureServices`:</span></span>

```csharp
// using Microsoft.AspNetCore.:::no-loc(Identity):::;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```

## <a name="globally-require-all-users-to-be-authenticated"></a><span data-ttu-id="3a271-226">全局要求对所有用户进行身份验证</span><span class="sxs-lookup"><span data-stu-id="3a271-226">Globally require all users to be authenticated</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]