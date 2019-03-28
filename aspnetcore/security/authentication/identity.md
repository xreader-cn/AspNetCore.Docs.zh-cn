---
title: ASP.NET Core 上的标识简介
author: rick-anderson
description: 将标识与 ASP.NET Core 应用配合使用。 了解如何设置密码要求 （RequireDigit、 RequiredLength、 RequiredUniqueChars，和的详细信息）。
ms.author: riande
ms.date: 08/08/2018
uid: security/authentication/identity
ms.openlocfilehash: 355c53e0c957944cb35c37c6b01e724af5f93f44
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265471"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>ASP.NET Core 上的标识简介

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 标识是一个成员身份系统，将登录功能添加到 ASP.NET Core 应用。 用户可以标识中存储的登录信息创建一个帐户，或它们可以使用外部登录提供程序。 受支持的外部登录提供程序包括[Facebook、 Google、 Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。

可以使用 SQL Server 数据库来存储用户名、 密码和配置文件数据配置标识。 或者，另一个的持久存储区可用，例如，Azure 表存储。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)([如何下载)](xref:index#how-to-download-a-sample))。

在本主题中，将了解如何使用标识来注册、 登录和注销用户。 有关创建使用标识的应用程序的更多详细说明，请参阅本文末尾的后续步骤部分。

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity 和 AddIdentity

[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) ASP.NET Core 2.1 中引入了。 调用`AddDefaultIdentity`类似于调用以下：

* [AddIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [AddDefaultUI](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [AddDefaultTokenProviders](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

请参阅[AddDefaultIdentity 源](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)有关详细信息。

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a>使用身份验证创建的 Web 应用

使用单个用户帐户创建一个 ASP.NET Core Web 应用程序项目。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 选择“文件” > “新建” > “项目”。
* 选择“ASP.NET Core Web 应用程序”。 将项目命名**WebApp1**具有项目下载相同的命名空间。 单击 **“确定”**。
* 选择 ASP.NET Core **Web 应用程序**，然后选择**更改身份验证**。
* 选择**单个用户帐户**然后单击**确定**。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

生成的项目提供了[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。 标识 Razor 类库公开终结点与`Identity`区域。 例如：

* / 标识/Account/Login
* / 标识/帐户/注销
* / Identity/帐户/管理

### <a name="test-register-and-login"></a>测试注册和登录名

运行应用并注册用户。 具体取决于你的屏幕大小，可能需要选择导航切换按钮以查看**注册**并**登录名**链接。

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>配置标识服务

在添加服务`ConfigureServices`。 典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

上述代码使用默认的选项值配置标识。 服务可用于通过应用[依赖关系注入](xref:fundamentals/dependency-injection)。

   标识启用通过调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)。 `UseAuthentication` 添加了身份验证[中间件](xref:fundamentals/middleware/index)到请求管道。

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   服务都提供给应用程序通过[依赖关系注入](xref:fundamentals/dependency-injection)。

   通过调用情况下，启用应用程序的身份标识`UseAuthentication`在`Configure`方法。 `UseAuthentication` 添加了身份验证[中间件](xref:fundamentals/middleware/index)到请求管道。

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   这些服务都提供给应用程序通过[依赖关系注入](xref:fundamentals/dependency-injection)。

   通过调用情况下，启用应用程序的身份标识`UseIdentity`在`Configure`方法。 `UseIdentity` 添加了基于 cookie 的身份验证[中间件](xref:fundamentals/middleware/index)到请求管道。

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

有关详细信息，请参阅[IdentityOptions 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)并[应用程序启动](xref:fundamentals/startup)。

## <a name="scaffold-register-login-and-logout"></a>基架注册、 登录和注销

请按照[创建标识为具有授权的 Razor 项目基架](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)生成在本部分中所示的代码说明。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

添加注册、 登录和注销文件。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果使用名称创建项目**WebApp1**，运行以下命令。 否则，请使用正确的命名空间为`ApplicationDbContext`:

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell 使用分号作为命令分隔符。 使用 PowerShell 时，转义分号的文件列表中，或将文件列表放在双引号内，如前面的示例所示。

---

### <a name="examine-register"></a>检查注册

::: moniker range=">= aspnetcore-2.1"

   当用户单击**注册**链接，`RegisterModel.OnPostAsync`调用操作。 通过创建用户[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)上`_userManager`对象。 `_userManager` 提供依赖关系注入）：

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   当用户单击**注册**链接，链接`Register`上调用操作`AccountController`。 `Register`操作将创建用户，通过调用`CreateAsync`上`_userManager`对象 (提供给`AccountController`通过依赖关系注入):

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   如果已成功创建用户，用户记录通过调用`_signInManager.SignInAsync`。

   **注意：** 请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)有关步骤，以防止在登记时立即登录。

### <a name="log-in"></a>登录

::: moniker range=">= aspnetcore-2.1"

显示登录窗体时：

* **登录**选择链接。
* 用户尝试访问受限的页面，他们不向被授权访问**或**时在还没有已完成身份验证系统。

登录页上的表单提交时，`OnPostAsync`调用操作。 `PasswordSignInAsync` 对调用`_signInManager`对象 （由依赖关系注入提供）。

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   基`Controller`类公开`User`，可以从控制器方法访问的属性。 例如，可以枚举`User.Claims`和做出授权决定。 有关详细信息，请参阅 <xref:security/authorization/introduction>。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

当用户选择时，将显示登录窗体**登录**访问要求进行身份验证的页面时将被重定向或链接。 当用户提交窗体上的登录页中， `AccountController` `Login`调用操作。

`Login`操作调用`PasswordSignInAsync`上`_signInManager`对象 (提供给`AccountController`通过依赖关系注入)。

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

基本 (`Controller`或`PageModel`) 类公开`User`属性。 例如，`User.Claims`可以枚举才能做出授权决定。

::: moniker-end

### <a name="log-out"></a>注销

::: moniker range=">= aspnetcore-2.1"

**注销**链接调用`LogoutModel.OnPost`操作。 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除存储在 cookie 中的用户的声明。 不要在调用`SignOutAsync`后进行重定向，否则用户将被注销。

中指定 post *Pages/Shared/_LoginPartial.cshtml*:

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   单击**注销**链接调用`LogOut`操作。

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   上述代码调用`_signInManager.SignOutAsync`方法。 `SignOutAsync`方法将清除用户的声明存储在 cookie 中。

::: moniker-end

## <a name="test-identity"></a>测试标识

默认 web 项目模板允许匿名访问主页。 若要测试标识，将添加[ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)到隐私页。

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

如果你登录，请注销。运行应用并选择**隐私**链接。 你将重定向到登录页。

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a>了解标识

若要更详细地了解标识：

* [创建完整的标识 UI 源](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 检查每个页面和单步执行调试器的源。

::: moniker-end

## <a name="identity-components"></a>标识组件

::: moniker range=">= aspnetcore-2.1"

中包含所有标识依赖 NuGet 包[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。

::: moniker-end

标识将主包是[Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。 此包包含 ASP.NET Core 标识的核心接口集，是 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 提供的。

## <a name="migrating-to-aspnet-core-identity"></a>迁移到 ASP.NET Core 标识

有关详细信息和指南迁移现有的标识存储，请参阅[迁移身份验证和标识](xref:migration/identity)。

## <a name="setting-password-strength"></a>设置密码强度

请参阅[配置](#pw)有关设置的最小密码要求的示例。

## <a name="next-steps"></a>后续步骤

* [配置标识](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
