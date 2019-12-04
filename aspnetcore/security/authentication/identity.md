---
title: ASP.NET Core 上的标识简介
author: rick-anderson
description: 将标识与 ASP.NET Core 应用配合使用。 了解如何设置密码要求（RequireDigit、RequiredLength、RequiredUniqueChars 等）。
ms.author: riande
ms.date: 12/7/2019
uid: security/authentication/identity
ms.openlocfilehash: 331ebe36eb4bb7fa694de8daa969bcabcab1c974
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803391"
---
# <a name="introduction-to-identity-on-aspnet-core"></a>ASP.NET Core 上的标识简介

::: moniker range=">= aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 标识：

* 是支持用户界面（UI）登录功能的 API。
* 管理用户、密码、配置文件数据、角色、声明、令牌、电子邮件确认等。

用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。 支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。

GitHub 上提供了[标识源代码](https://github.com/aspnet/AspNetCore/tree/master/src/Identity)。 [基架标识](xref:security/authentication/scaffold-identity)并查看生成的文件以查看模板与标识的交互。

通常使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。 另外，还可以使用另一个永久性存储，例如 Azure 表存储。

本主题介绍如何使用标识注册、登录和注销用户。 有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。

[Microsoft 标识平台](/azure/active-directory/develop/)是：

* Azure Active Directory （Azure AD）开发人员平台的演变。
* 与 ASP.NET Core 标识无关。

[!INCLUDE[](~/includes/IdentityServer4.md)]

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)（[如何下载）](xref:index#how-to-download-a-sample)。

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a>创建具有身份验证的 Web 应用

使用单个用户帐户创建一个 ASP.NET Core Web 应用程序项目。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 选择“文件”>“新建”>“项目”。
* 选择“ASP.NET Core Web 应用程序”。 将项目命名为**WebApp1** ，使其命名空间与项目下载相同。 单击“确定”。
* 选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。
* 选择**单个用户帐户**，然后单击 **"确定"** 。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

上述命令使用 SQLite 创建 Razor web 应用。 若要创建具有 LocalDB 的 web 应用，请运行以下命令：

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。 标识 Razor 类库公开 `Identity` 区域的终结点。 例如：

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>应用迁移

应用迁移以初始化数据库。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

在包管理器控制台中运行以下命令（PMC）：

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

使用 SQLite 时，此步骤不需要迁移。 对于 LocalDB，请运行以下命令：

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>测试注册和登录

运行应用并注册用户。 根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>配置标识服务

在 `ConfigureServices`中添加服务。 典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

前面突出显示的代码用默认选项值配置标识。 服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。

通过调用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>来启用标识。 `UseAuthentication` 将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

模板生成的应用不使用[授权](xref:security/authorization/secure-data)。 包括 `app.UseAuthorization` 以确保在应用添加授权时，按正确的顺序添加。 必须按前面的代码中所示的顺序调用 `UseRouting`、`UseAuthentication`、`UseAuthorization`和 `UseEndpoints`。

有关 `IdentityOptions` 和 `Startup`的详细信息，请参阅 <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 和[应用程序启动](xref:fundamentals/startup)。

## <a name="scaffold-register-login-and-logout"></a>基架注册、登录和注销

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

添加注册、登录和注销文件。 按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果创建的项目的名称为**WebApp1**，请运行以下命令。 否则，请使用 `ApplicationDbContext`的正确命名空间：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell 使用分号作为命令分隔符。 使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。

有关基架标识的详细信息，请参阅[使用授权将基架标识导入 Razor 项目](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)。

---

### <a name="examine-register"></a>检查注册

当用户单击 "**注册**" 链接时，将调用 `RegisterModel.OnPostAsync` 操作。 用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对 `_userManager` 对象创建的。 `_userManager` 由依赖关系注入提供）：

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

如果已成功创建用户，则会通过调用 `_signInManager.SignInAsync`登录该用户。

请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。

### <a name="log-in"></a>Log in

发生下列情况时，会显示登录窗体：

* 选择 "**登录**" 链接。
* 用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。

提交“登录”页上的表单时，会调用 `OnPostAsync` 操作。 会在 `_signInManager` 对象（通过注入依赖项的方式提供）上调用 `PasswordSignInAsync`。

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

Base `Controller` 类公开可从控制器方法访问的 `User` 属性。 例如，可以枚举 `User.Claims` 并进行授权决策。 有关更多信息，请参见<xref:security/authorization/introduction>。

### <a name="log-out"></a>Log out

"**注销**" 链接调用 `LogoutModel.OnPost` 操作。 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

在前面的代码中，代码 `return RedirectToPage();` 需要是重定向，以便浏览器执行新请求并更新用户的标识。

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。

在 *Pages/Shared/_LoginPartial.cshtml* 中指定 post：

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a>测试标识

默认 web 项目模板允许匿名访问主页。 若要测试标识，请添加[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)：

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

如果已登录，请注销。运行应用并选择 "**隐私**" 链接。 你将重定向到登录页。

### <a name="explore-identity"></a>浏览标识

更详细地了解标识：

* [创建完全标识 UI 源](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 检查每个页面的源，并单步执行调试程序。

## <a name="identity-components"></a>标识组件

所有标识相关 NuGet 包都包含在[ASP.NET Core 共享框架](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)中。

标识的主包为[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。 此包包含 ASP.NET Core 标识的核心接口集，是 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 提供的。

## <a name="migrating-to-aspnet-core-identity"></a>迁移到 ASP.NET Core 标识

有关迁移现有标识存储的详细信息和指南，请参阅[迁移身份验证和标识](xref:migration/identity)。

## <a name="setting-password-strength"></a>设置密码强度

有关设置最小密码要求的示例，请参阅[配置](#pw)。

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity 和 AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 是在 ASP.NET Core 2.1 中引入的。 调用 `AddDefaultIdentity` 类似于调用以下内容：

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。

## <a name="next-steps"></a>后续步骤

* [配置标识](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 标识是将登录功能添加到 ASP.NET Core 应用的成员资格系统。 用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。 支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。

可以使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。 另外，还可以使用另一个永久性存储，例如 Azure 表存储。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)（[如何下载）](xref:index#how-to-download-a-sample)。

本主题介绍如何使用标识注册、登录和注销用户。 有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a>AddDefaultIdentity 和 AddIdentity

<xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 是在 ASP.NET Core 2.1 中引入的。 调用 `AddDefaultIdentity` 类似于调用以下内容：

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。

## <a name="create-a-web-app-with-authentication"></a>创建具有身份验证的 Web 应用

使用单个用户帐户创建一个 ASP.NET Core Web 应用程序项目。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 选择“文件”>“新建”>“项目”。
* 选择“ASP.NET Core Web 应用程序”。 将项目命名为**WebApp1** ，使其命名空间与项目下载相同。 单击“确定”。
* 选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。
* 选择**单个用户帐户**，然后单击 **"确定"** 。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。 标识 Razor 类库公开 `Identity` 区域的终结点。 例如：

* /Identity/Account/Login
* /Identity/Account/Logout
* /Identity/Account/Manage

### <a name="apply-migrations"></a>应用迁移

应用迁移以初始化数据库。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

在包管理器控制台中运行以下命令（PMC）：

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a>测试注册和登录

运行应用并注册用户。 根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a>配置标识服务

在 `ConfigureServices`中添加服务。 典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

前面的代码用默认选项值配置标识。 服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。

通过调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)来启用标识。 `UseAuthentication` 将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

有关详细信息，请参阅[IdentityOptions 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)和[应用程序启动](xref:fundamentals/startup)。

## <a name="scaffold-register-login-and-logout"></a>基架注册、登录和注销

按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

添加注册、登录和注销文件。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果创建的项目的名称为**WebApp1**，请运行以下命令。 否则，请使用 `ApplicationDbContext`的正确命名空间：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

PowerShell 使用分号作为命令分隔符。 使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。

---

### <a name="examine-register"></a>检查注册

当用户单击 "**注册**" 链接时，将调用 `RegisterModel.OnPostAsync` 操作。 用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对 `_userManager` 对象创建的。 `_userManager` 由依赖关系注入提供）：

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

如果已成功创建用户，则会通过调用 `_signInManager.SignInAsync`登录该用户。

**注意：** 请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。

### <a name="log-in"></a>Log in

发生下列情况时，会显示登录窗体：

* 选择 "**登录**" 链接。
* 用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。

提交“登录”页上的表单时，会调用 `OnPostAsync` 操作。 会在 `_signInManager` 对象（通过注入依赖项的方式提供）上调用 `PasswordSignInAsync`。

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

基 `Controller` 类公开了一个 `User` 属性，方便你通过控制器方法对其进行访问。 例如，可以枚举 `User.Claims` 并进行授权决策。 有关更多信息，请参见<xref:security/authorization/introduction>。

### <a name="log-out"></a>Log out

"**注销**" 链接调用 `LogoutModel.OnPost` 操作。 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。

在 *Pages/Shared/_LoginPartial.cshtml* 中指定 post：

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a>测试标识

默认 web 项目模板允许匿名访问主页。 若要测试标识，请将[`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)添加到 "隐私" 页。

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

如果已登录，请注销。运行应用并选择 "**隐私**" 链接。 你将重定向到登录页。

### <a name="explore-identity"></a>浏览标识

更详细地了解标识：

* [创建完全标识 UI 源](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* 检查每个页面的源，并单步执行调试程序。

## <a name="identity-components"></a>标识组件

所有标识相关 NuGet 包都包含在[AspNetCore 元包](xref:fundamentals/metapackage-app)中。

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

::: moniker-end
