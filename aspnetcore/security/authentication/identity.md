---
title: ASP.NET Core 上的标识简介
author: rick-anderson
description: 将标识与 ASP.NET Core 应用配合使用。 了解如何设置密码要求（RequireDigit、RequiredLength、RequiredUniqueChars 等）。
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: 325a61e6038e79b9a0db72c8360a5cbff2c8ddae
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011208"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>ASP.NET Core 上的标识简介

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 标识是将登录功能添加到 ASP.NET Core 应用的成员资格系统。 用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。 支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。

可以使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。 另外，还可以使用另一个永久性存储，例如 Azure 表存储。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)（[如何下载）](xref:index#how-to-download-a-sample)。

本主题介绍如何使用标识注册、登录和注销用户。 有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity 和 AddIdentity

ASP.NET Core 2.1 中引入了[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) 。 调用`AddDefaultIdentity`类似于调用以下内容：

* [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [AddDefaultUI](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [AddDefaultTokenProviders](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a>创建具有身份验证的 Web 应用

使用单个用户帐户创建一个 ASP.NET Core Web 应用程序项目。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 选择“文件” > “新建” > “项目”。
* 选择“ASP.NET Core Web 应用程序”。 将项目命名为**WebApp1** ，使其命名空间与项目下载相同。 单击 **“确定”** 。
* 选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。
* 选择**单个用户帐户**，然后单击 **"确定"** 。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。 标识 Razor 类库公开带有`Identity`区域的终结点。 例如：

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>应用迁移

将迁移应用到数据库初始化。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

在包管理器控制台中运行以下命令（PMC）：

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```cli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>测试注册和登录

运行应用并注册用户。 根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>配置标识服务

中`ConfigureServices`添加了服务。 典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

前面的代码用默认选项值配置标识。 服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。

   通过调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)来启用标识。 `UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。

   `UseAuthentication` 通过`Configure`在方法中调用来为应用程序启用标识。 `UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   这些服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。

   `UseIdentity` 通过`Configure`在方法中调用来为应用程序启用标识。 `UseIdentity`将基于 cookie 的身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

有关详细信息，请参阅[IdentityOptions 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)和[应用程序启动](xref:fundamentals/startup)。

## <a name="scaffold-register-login-and-logout"></a>基架注册、登录和注销

按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

添加注册、登录和注销文件。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果创建的项目的名称为**WebApp1**，请运行以下命令。 否则，请使用正确的命名空间`ApplicationDbContext`：

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell 使用分号作为命令分隔符。 使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。

---

### <a name="examine-register"></a>检查注册

::: moniker range=">= aspnetcore-2.1"

   当用户单击 "**注册**" 链接时， `RegisterModel.OnPostAsync`将调用该操作。 用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对`_userManager`对象创建的。 `_userManager`由依赖关系注入提供：

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   当用户单击 "**注册**" 链接时， `Register`将在上`AccountController`调用该操作。 操作通过对`CreateAsync` `AccountController`对象调用来创建用户（由依赖项注入提供）： `_userManager` `Register`

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   如果用户已成功创建，则对的调用`_signInManager.SignInAsync`会登录该用户。

   **注意：** 请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。

### <a name="log-in"></a>登录

::: moniker range=">= aspnetcore-2.1"

发生下列情况时，会显示登录窗体：

* 选择 "**登录**" 链接。
* 用户在无权访问的情况下或未经系统进行身份验证的情况下尝试访问受限页面。

提交“登录”页上的表单时，会调用 `OnPostAsync` 操作。 会在 `_signInManager` 对象（通过注入依赖项的方式提供）上调用 `PasswordSignInAsync`。

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   基 `Controller` 类公开了一个 `User` 属性，方便你通过控制器方法对其进行访问。 例如，可以枚举 `User.Claims` 并进行授权决策。 有关详细信息，请参阅 <xref:security/authorization/introduction>。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

当用户选择 "**登录**" 链接或访问需要身份验证的页面时，将显示登录窗体。 当用户在登录页上提交窗体时，将`AccountController`调用该`Login`操作。

操作对对象`_signInManager`调用`AccountController` （由依赖关系注入提供）。 `PasswordSignInAsync` `Login`

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

基（`Controller`或`PageModel`）类公开了一个`User`属性。 例如， `User.Claims`可以枚举来做出授权决策。

::: moniker-end

### <a name="log-out"></a>注销

::: moniker range=">= aspnetcore-2.1"

"**注销**" 链接将调用`LogoutModel.OnPost`该操作。 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。

在 *Pages/Shared/_LoginPartial.cshtml* 中指定 post：

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   单击 "**注销**" 链接将调用`LogOut`该操作。

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   前面的代码调用`_signInManager.SignOutAsync`方法。 `SignOutAsync`方法会清除 cookie 中存储的用户声明。

::: moniker-end

## <a name="test-identity"></a>测试标识

默认 web 项目模板允许匿名访问主页。 若要测试标识， [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)请将添加到 "隐私" 页。

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

如果已登录，请注销。运行应用并选择 "**隐私**" 链接。 你将重定向到登录页。

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a>浏览标识

更详细地了解标识：

* [创建完全标识 UI 源](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 检查每个页面的源，并单步执行调试程序。

::: moniker-end

## <a name="identity-components"></a>标识组件

::: moniker range=">= aspnetcore-2.1"

所有标识相关 NuGet 包都包含在[AspNetCore 元包](xref:fundamentals/metapackage-app)中。

::: moniker-end

标识的主包为[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。 此包包含 ASP.NET Core 标识的核心接口集，是 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 提供的。

## <a name="migrating-to-aspnet-core-identity"></a>迁移到 ASP.NET Core 标识

有关迁移现有标识存储的详细信息和指南，请参阅[迁移身份验证和标识](xref:migration/identity)。

## <a name="setting-password-strength"></a>设置密码强度

有关设置最小密码要求的示例，请参阅[配置](#pw)。

## <a name="next-steps"></a>后续步骤

* [配置标识](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
