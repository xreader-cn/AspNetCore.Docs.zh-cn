---
title: 配置 ASP.NET Core 标识
author: AdrienTorris
description: 了解 ASP.NET Core 标识默认值，并了解如何配置要使用自定义值的标识属性。
ms.author: riande
ms.date: 02/11/2019
uid: security/authentication/identity-configuration
ms.openlocfilehash: 823182bed2cb953e07f9374d135868aeb2be9c60
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892344"
---
# <a name="configure-aspnet-core-identity"></a>配置 ASP.NET Core 标识

ASP.NET Core 标识设置，例如密码策略、 锁定和 cookie 配置使用默认值。 在中，可以重写这些设置`Startup`类。

## <a name="identity-options"></a>标识选项

[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)类表示可用于标识系统配置的选项。 `IdentityOptions` 必须设置**后**调用`AddIdentity`或`AddDefaultIdentity`。

### <a name="claims-identity"></a>声明标识

[IdentityOptions.ClaimsIdentity](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.claimsidentity)指定[ClaimsIdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions)与下表中所示的属性。

| 属性 | 描述 | 默认 |
| -------- | ----------- | :-----: |
| [RoleClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.roleclaimtype) | 获取或设置用于为角色声明的声明类型。 | [ClaimTypes.Role](/dotnet/api/system.security.claims.claimtypes.role) |
| [SecurityStampClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.securitystampclaimtype) | 获取或设置用于安全戳声明的声明类型。 | `AspNet.Identity.SecurityStamp` |
| [UserIdClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.useridclaimtype) | 获取或设置用于的用户标识符声明的声明类型。 | [ClaimTypes.NameIdentifier](/dotnet/api/system.security.claims.claimtypes.nameidentifier) |
| [UserNameClaimType](/dotnet/api/microsoft.aspnetcore.identity.claimsidentityoptions.usernameclaimtype) | 获取或设置用于用户名称声明的声明类型。 | [ClaimTypes.Name](/dotnet/api/system.security.claims.claimtypes.name) |

### <a name="lockout"></a>锁定

在中设置锁定[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync#Microsoft_AspNetCore_Identity_SignInManager_1_PasswordSignInAsync_System_String_System_String_System_Boolean_System_Boolean_)方法：

[!code-csharp[](identity-configuration/sample/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=9)]

前面的代码基于`Login`标识模板。 

在中设置锁定选项`StartUp.ConfigureServices`:

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_lock)]

上述代码会设置[IdentityOptions](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) [LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions)具有默认值。

成功的身份验证失败的访问尝试计数重置并重置时钟。

[IdentityOptions.Lockout](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.lockout)指定[LockoutOptions](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions)与表中所示的属性。

| 属性 | 描述 | 默认 |
| -------- | ----------- | :-----: |
| [AllowedForNewUsers](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.allowedfornewusers) | 确定是否新用户会被锁定。 | `true` |
| [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan) | 时间量用户已锁定时在锁定时发生。 | 5 分钟 |
| [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) | 用户已被锁定，如果启用了锁定前的失败的访问尝试数。 | 5 |

### <a name="password"></a>Password

默认情况下，标识要求密码包含大写字符、 小写字符、 数字、 和非字母数字字符。 密码必须至少为六个字符。 [PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions)可以设置`Startup.ConfigureServices`。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_pw)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-37,50-52)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-65,84)]

::: moniker-end

[IdentityOptions.Password](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.password)指定[PasswordOptions](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions)与表中所示的属性。

::: moniker range=">= aspnetcore-2.0"

| 属性 | 描述 | 默认 |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | 需要介于 0-9 的密码。 | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | 密码最小长度。 | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | 要求密码中的小写字符。 | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | 需要在密码中的非字母数字字符。 | `true` |
| [RequiredUniqueChars](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireduniquechars) | 仅适用于 ASP.NET Core 2.0 或更高版本。<br><br> 要求在密码中非重复字符数。 | 1 |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | 需要大写字符的密码。 | `true` |

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| 属性 | 描述 | 默认 |
| -------- | ----------- | :-----: |
| [RequireDigit](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredigit) | 需要介于 0-9 的密码。 | `true` |
| [RequiredLength](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requiredlength) | 密码最小长度。 | 6 |
| [RequireLowercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirelowercase) | 要求密码中的小写字符。 | `true` |
| [RequireNonAlphanumeric](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requirenonalphanumeric) | 需要在密码中的非字母数字字符。 | `true` |
| [RequireUppercase](/dotnet/api/microsoft.aspnetcore.identity.passwordoptions.requireuppercase) | 需要大写字符的密码。 | `true` |

::: moniker-end

### <a name="sign-in"></a>登录

下面的代码设置`SignIn`（为默认值） 的设置：

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_si)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?range=29-30,44-46,50-52)] 

::: moniker-end

[IdentityOptions.SignIn](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.signin)指定[SignInOptions](/dotnet/api/microsoft.aspnetcore.identity.signinoptions)与表中所示的属性。

| 属性 | 描述 | 默认 |
| -------- | ----------- | :-----: |
| [RequireConfirmedEmail](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedemail) | 需要已确认的电子邮件，登录。 | `false` |
| [RequireConfirmedPhoneNumber](/dotnet/api/microsoft.aspnetcore.identity.signinoptions.requireconfirmedphonenumber) | 需要确认的电话号码进行登录。 | `false` |

### <a name="tokens"></a>标记

[IdentityOptions.Tokens](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.tokens)指定[TokenOptions](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions)与表中所示的属性。

|                                                        属性                                                         |                                                                                      描述                                                                                      |
|-------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     [AuthenticatorTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.authenticatortokenprovider)     |                                       获取或设置`AuthenticatorTokenProvider`用于验证身份验证器使用双因素登录名。                                       |
|       [ChangeEmailTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changeemailtokenprovider)       |                                     获取或设置`ChangeEmailTokenProvider`用于生成电子邮件更改确认电子邮件中使用的令牌。                                     |
| [ChangePhoneNumberTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.changephonenumbertokenprovider) |                                      获取或设置`ChangePhoneNumberTokenProvider`用于生成令牌更改电话号码时使用。                                      |
| [EmailConfirmationTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.emailconfirmationtokenprovider) |                                             获取或设置用于生成帐户确认电子邮件中使用的令牌的令牌提供程序。                                              |
|     [PasswordResetTokenProvider](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.passwordresettokenprovider)     | 获取或设置[IUserTwoFactorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactortokenprovider-1)用于生成在密码重置电子邮件中使用的令牌。 |
|                    [ProviderMap](/dotnet/api/microsoft.aspnetcore.identity.tokenoptions.providermap)                    |                用于构造[用户令牌提供程序](/dotnet/api/microsoft.aspnetcore.identity.tokenproviderdescriptor)与键用作提供程序的名称。                 |

### <a name="user"></a>用户

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_user)]

[IdentityOptions.User](/dotnet/api/microsoft.aspnetcore.identity.identityoptions.user)指定[UserOptions](/dotnet/api/microsoft.aspnetcore.identity.useroptions)与表中所示的属性。

| 属性 | 描述 | 默认 |
| -------- | ----------- | :-----: |
| [AllowedUserNameCharacters](/dotnet/api/microsoft.aspnetcore.identity.useroptions.allowedusernamecharacters) | 在用户名中允许的字符。 | abcdefghijklmnopqrstuvwxyz<br>ABCDEFGHIJKLMNOPQRSTUVWXYZ<br>0123456789<br>-.\_@+ |
| [RequireUniqueEmail](/dotnet/api/microsoft.aspnetcore.identity.useroptions.requireuniqueemail) | 要求每个用户必须拥有唯一的电子邮件。 | `false` |

### <a name="cookie-settings"></a>Cookie 设置

配置中的应用程序的 cookie `Startup.ConfigureServices`。 [ConfigureApplicationCookie](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.configureapplicationcookie#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_ConfigureApplicationCookie_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Authentication_Cookies_CookieAuthenticationOptions__)必须在调用**后**调用`AddIdentity`或`AddDefaultIdentity`。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity-configuration/sample/Startup.cs?name=snippet_cookie)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

[!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo-Configuration/Startup.cs?name=snippet_configurecookie)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo-PrimaryKeysConfig/Startup.cs?range=58-59,72-80,84)]

::: moniker-end

有关详细信息，请参阅[CookieAuthenticationOptions](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions)。

## <a name="password-hasher-options"></a>密码哈希计算器选项

<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions> 获取和设置进行密码哈希处理的选项。

| 选项 | 描述 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode> | 新密码哈希处理时使用的兼容性模式。 默认为 <xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>。 经过哈希处理密码，调用的第一个字节*格式标记*，指定用于对密码进行加密的哈希算法的版本。 验证密码与哈希时<xref:Microsoft.AspNetCore.Identity.PasswordHasher`1.VerifyHashedPassword*>方法选择正确的算法根据第一个字节。 客户端是能够进行身份验证而不考虑的这一版算法用于对密码进行加密。 设置兼容性模式将影响的哈希*新密码*。 |
| <xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount> | 使用哈希密码使用 PBKDF2 时的迭代次数。 此值是时，才使用<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.CompatibilityMode>设置为<xref:Microsoft.AspNetCore.Identity.PasswordHasherCompatibilityMode.IdentityV3>。 值必须是一个正整数，默认值为`10000`。 |

在以下示例中，<xref:Microsoft.AspNetCore.Identity.PasswordHasherOptions.IterationCount>设置为`12000`中`Startup.ConfigureServices`:

```csharp
// using Microsoft.AspNetCore.Identity;

services.Configure<PasswordHasherOptions>(option =>
{
    option.IterationCount = 12000;
});
```
