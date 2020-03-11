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
# <a name="configure-aspnet-core-identity"></a>配置 ASP.NET Core 标识

ASP.NET Core 标识将默认值用于设置密码策略、锁定和 cookie 配置等设置。 可以在 `Startup` 类中重写这些设置。

## <a name="identity-options"></a>标识选项

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)类表示可用于配置标识系统的选项。 调用 `AddIdentity` 或 `AddDefaultIdentity`**后**必须设置 `IdentityOptions`。

### <a name="claims-identity"></a>声明标识

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity)指定[ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions) ，其中包含下表所示的属性。

| properties | 说明 | 默认 |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | 获取或设置用于角色声明的声明类型。 | [ClaimTypes](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | 获取或设置用于安全戳声明的声明类型。 | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | 获取或设置用于用户标识符声明的声明类型。 | [ClaimTypes. NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | 获取或设置用于用户名声明的声明类型。 | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>锁定

在[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_)方法中设置锁定：

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

前面的代码基于 `Login` 标识模板。 

在 `StartUp.ConfigureServices`中设置锁定选项：

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

前面的代码将[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions)设置为默认值。

成功的身份验证失败的访问尝试计数重置并重置时钟。

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout)指定[LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions) ，其中包含表中所示的属性。

| properties | 说明 | 默认 |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | 确定新用户是否可以锁定。 | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | 锁定发生时用户被锁定的时间长度。 | 5 分钟 |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | 如果启用了锁定，则在用户被锁定之前失败的访问尝试次数。 | 5 |

### <a name="password"></a>密码

默认情况下，Identity 要求密码包含大写字符、小写字符、数字以及非字母数字字符。 密码长度必须至少为6个字符。 可以 `Startup.ConfigureServices`中设置[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) 。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password)指定[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions) ，其中包含表中所示的属性。

::: moniker range=">= aspnetcore-2.0"

| properties | 说明 | 默认 |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | 要求密码中的数字介于0-9 之间。 | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | 密码的最小长度。 | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | 密码中需要小写字符。 | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | 密码中需要一个非字母数字字符。 | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | 仅适用于 ASP.NET Core 2.0 或更高版本。<br><br> 需要密码中的非重复字符数。 | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | 密码中需要大写字符。 | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| properties | 说明 | 默认 |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | 要求密码中的数字介于0-9 之间。 | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | 密码的最小长度。 | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | 密码中需要小写字符。 | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | 密码中需要一个非字母数字字符。 | `true` |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | 密码中需要大写字符。 | `true` |

::: moniker-end

### <a name="sign-in"></a>登录

下面的代码将 `SignIn` 设置（设置为默认值）：

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin)指定[SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions) ，其中包含表中所示的属性。

| properties | 说明 | 默认 |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | 需要确认电子邮件登录。 | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | 需要确认电话号码才能登录。 | `false` |

### <a name="tokens"></a>令牌

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens)指定[TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions) ，其中包含表中所示的属性。

|                                                        properties                                                         |                                                                                      说明                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       获取或设置用于使用验证器验证双重登录的 `AuthenticatorTokenProvider`。                                       |
|       [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     获取或设置用于生成电子邮件更改确认电子邮件中使用的令牌的 `ChangeEmailTokenProvider`。                                     |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      获取或设置用于生成更改电话号码时使用的令牌的 `ChangePhoneNumberTokenProvider`。                                      |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             获取或设置用于生成帐户确认电子邮件中使用的令牌的令牌提供程序。                                              |
|     [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | 获取或设置用于生成密码重置电子邮件中使用的令牌的[IUserTwoFactorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1) 。 |
|                    [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                用于使用用作提供程序名称的密钥构造[用户令牌提供程序](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor)。                 |

### <a name="user"></a>用户

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user)指定[UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions) ，其中包含表中所示的属性。

| properties | 说明 | 默认 |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | 用户名中允许使用的字符。 | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | 要求每个用户都有唯一的电子邮件。 | `false` |

### <a name="cookie-settings"></a>Cookie 设置

在 `Startup.ConfigureServices`中配置应用的 cookie。 调用 `AddIdentity` 或 `AddDefaultIdentity`**后**，必须调用[ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__) 。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

有关详细信息，请参阅[cookieauthenticationoptions.authenticationtype](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions)。

## <a name="password-hasher-options"></a>Password Hasher 选项

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> 获取和设置用于密码哈希的选项。

| 选项 | 说明 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | 对新密码进行哈希处理时使用的兼容性模式。 默认为 <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>。 哈希密码的第一个字节称为*格式标记*，它指定用于对密码进行哈希处理的哈希算法的版本。 针对哈希验证密码时，<xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*> 方法基于第一个字节选择正确的算法。 无论使用哪个版本的算法对密码进行哈希处理，客户端都可以进行身份验证。 设置兼容性模式会影响*新密码*的哈希。 |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | 使用 PBKDF2 对密码进行哈希处理时使用的迭代次数。 仅当 <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> 设置为 <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>时，才使用此值。 该值必须是一个正整数，默认值为 `10000`。 |

在下面的示例中，将 <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> 设置为 `Startup.ConfigureServices`中 `12000`：

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
