---
title: ASP.NET Core 项目中的基架标识
author: rick-anderson
description: 了解如何创建标识的基架 ASP.NET Core 项目中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/15/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: b3e077aeac11e62d9e992884100476f7be35b59a
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653712"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>ASP.NET Core 项目中的基架标识

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core 提供作为[Razor 类库](xref:razor-pages/ui-class) [ASP.NET Core 标识](xref:security/authentication/identity)。 包含标识的应用程序可以应用 scaffolder 来有选择地添加标识 Razor 类库中包含的源代码（RCL）。 建议生成源代码，以便修改代码和更改行为。 例如，可以指示基架生成在注册过程中使用的代码。 生成的代码优先于标识 RCL 中的相同代码。 若要完全控制 UI，而不使用默认的 RCL，请参阅[创建完全标识 UI 源](#full)部分。

**不**包含身份验证的应用程序可以应用 scaffolder 来添加 RCL 标识包。 可以选择要生成的标识代码。

尽管 scaffolder 生成了大部分必要的代码，但你需要更新项目以完成该过程。 本文档介绍完成标识基架更新所需的步骤。

建议使用显示文件差异的源代码管理系统，并使您能够回退更改。 运行标识 scaffolder 后检查更改。

使用[双重身份验证](xref:security/authentication/identity-enable-qrcodes)、[帐户确认和密码恢复](xref:security/authentication/accconfirm)，以及使用标识的其他安全功能时，需要提供服务。 基架标识时不生成服务或服务存根。 要启用这些功能，必须手动添加服务。 例如，请参阅[需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。

本文档包含的详细说明比在运行 scaffolder 时生成的*ScaffoldingReadme*文件更完整。

## <a name="scaffold-identity-into-an-empty-project"></a>将标识基架到空项目中

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

用类似于下面的代码更新 `Startup` 类：

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>将标识基架到 Razor 项目，而无需现有授权

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add CreateIdentitySchema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

在*区域/标识/IdentityHostingStartup*中配置标识。 有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>迁移、UseAuthentication 和布局

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>启用身份验证

用类似于下面的代码更新 `Startup` 类：

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>布局更改

可选：将登录名部分（`_LoginPartial`）添加到布局文件中：

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>使用授权将标识基架到 Razor 项目

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
某些标识选项在*区域/标识/IdentityHostingStartup*中配置。 有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>不使用现有授权将标识基架到 MVC 项目

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

可选：将登录名 partial （`_LoginPartial`）添加到*Views/Shared/_Layout*文件：

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* 将*Pages/shared/_LoginPartial cshtml*文件移动到*Views/shared/_LoginPartial。 cshtml*

在*区域/标识/IdentityHostingStartup*中配置标识。 有关详细信息，请参阅 IHostingStartup。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

用类似于下面的代码更新 `Startup` 类：

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>使用授权将标识基架到 MVC 项目

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>创建完全标识 UI 源

若要保持对标识 UI 的完全控制，请运行标识 scaffolder，并选择 "**替代所有文件**"。

以下突出显示的代码显示默认标识 UI 替换标识在 ASP.NET Core 2.1 web 应用的更改。 你可能希望执行此操作以对标识 UI 具有完全控制。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

在以下代码中，将替换默认标识：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

下面的代码将设置[LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

注册 `IEmailSender` 实现，例如：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>禁用注册页

禁用用户注册：

* 基架标识。 包括帐户. Register、RegisterConfirmation。 例如：

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* 更新*区域/标识/页/帐户/注册. .cs* ，使用户无法从此终结点注册：

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 更新*区域/标识/页/帐户/Register. cshtml* ，使其与前面的更改一致：

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* 注释掉或删除*区域/标识/页面/帐户/登录名*中的注册链接

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* 更新*Areas/Identity/Pages/Account/RegisterConfirmation*页。

  * 删除来自 cshtml 文件的代码和链接。
  * 从 `PageModel`中删除确认代码：

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>使用其他应用添加用户

提供一种在 web 应用外部添加用户的机制。 用于添加用户的选项包括：

* 专用的管理 web 应用。
* 控制台应用。

下面的代码概述了一种添加用户的方法：

* 用户列表将读入内存中。
* 为每个用户生成一个强唯一密码。
* 用户已添加到标识数据库。
* 系统会通知用户并通知用户更改密码。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

下面的代码概述了如何添加用户：

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

对于生产方案，可以遵循类似的方法。

## <a name="prevent-publish-of-static-identity-assets"></a>禁止发布静态标识资产

要阻止将静态标识资产发布到 Web 根目录，请参阅 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>。

## <a name="additional-resources"></a>其他资源

* [更改为 ASP.NET Core 2.1 及更高版本的身份验证代码](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.1 和更高版本提供作为[Razor 类库](xref:razor-pages/ui-class) [ASP.NET Core 标识](xref:security/authentication/identity)。 包含标识的应用程序可以应用 scaffolder 来有选择地添加标识 Razor 类库中包含的源代码（RCL）。 建议生成源代码，以便修改代码和更改行为。 例如，可以指示基架生成在注册过程中使用的代码。 生成的代码优先于标识 RCL 中的相同代码。 若要完全控制 UI，而不使用默认的 RCL，请参阅[创建完全标识 UI 源](#full)部分。

**不**包含身份验证的应用程序可以应用 scaffolder 来添加 RCL 标识包。 可以选择要生成的标识代码。

尽管 scaffolder 生成了大部分必要的代码，但你必须更新项目才能完成此过程。 本文档介绍完成标识基架更新所需的步骤。

运行标识 scaffolder 时，会在项目目录中创建一个*ScaffoldingReadme*文件。 *ScaffoldingReadme*文件包含有关完成标识基架更新所需内容的一般说明。 本文档包含的有关*ScaffoldingReadme*文件的完整说明。

建议使用显示文件差异的源代码管理系统，并使您能够回退更改。 运行标识 scaffolder 后检查更改。

> [!NOTE]
> 使用[双重身份验证](xref:security/authentication/identity-enable-qrcodes)、[帐户确认和密码恢复](xref:security/authentication/accconfirm)，以及使用标识的其他安全功能时，需要提供服务。 基架标识时不生成服务或服务存根。 要启用这些功能，必须手动添加服务。 例如，请参阅[需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。

## <a name="scaffold-identity-into-an-empty-project"></a>将标识基架到空项目中

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

将以下突出显示的调用添加到 `Startup` 类：

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>将标识基架到 Razor 项目，而无需现有授权

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.Identity.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.Identity.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

在*区域/标识/IdentityHostingStartup*中配置标识。 有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>迁移、UseAuthentication 和布局

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>启用身份验证

在 `Startup` 类的 `Configure` 方法中，在 `UseStaticFiles`后调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ：

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>布局更改

可选：将登录名部分（`_LoginPartial`）添加到布局文件中：

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>使用授权将标识基架到 Razor 项目

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]
某些标识选项在*区域/标识/IdentityHostingStartup*中配置。 有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>不使用现有授权将标识基架到 MVC 项目

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

可选：将登录名 partial （`_LoginPartial`）添加到*Views/Shared/_Layout*文件：

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* 将*Pages/shared/_LoginPartial cshtml*文件移动到*Views/shared/_LoginPartial。 cshtml*

在*区域/标识/IdentityHostingStartup*中配置标识。 有关详细信息，请参阅 IHostingStartup。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

`UseStaticFiles`后调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ：

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>使用授权将标识基架到 MVC 项目

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

删除*页面/共享*文件夹和该文件夹中的文件。

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>创建完全标识 UI 源

若要保持对标识 UI 的完全控制，请运行标识 scaffolder，并选择 "**替代所有文件**"。

以下突出显示的代码显示默认标识 UI 替换标识在 ASP.NET Core 2.1 web 应用的更改。 你可能希望执行此操作以对标识 UI 具有完全控制。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

在以下代码中，将替换默认标识：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

下面的代码将设置[LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

注册 `IEmailSender` 实现，例如：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a>禁用注册页

禁用用户注册：

* 基架标识。 包括帐户. Register、RegisterConfirmation。 例如：

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* 更新*区域/标识/页/帐户/注册. .cs* ，使用户无法从此终结点注册：

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 更新*区域/标识/页/帐户/Register. cshtml* ，使其与前面的更改一致：

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* 注释掉或删除*区域/标识/页面/帐户/登录名*中的注册链接

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* 更新*Areas/Identity/Pages/Account/RegisterConfirmation*页。

  * 删除来自 cshtml 文件的代码和链接。
  * 从 `PageModel`中删除确认代码：

  ```csharp
   [AllowAnonymous]
    public class RegisterConfirmationModel : PageModel
    {
        public IActionResult OnGet()
        {  
            return Page();
        }
    }
  ```
  
### <a name="use-another-app-to-add-users"></a>使用其他应用添加用户

提供一种在 web 应用外部添加用户的机制。 用于添加用户的选项包括：

* 专用的管理 web 应用。
* 控制台应用。

下面的代码概述了一种添加用户的方法：

* 用户列表将读入内存中。
* 为每个用户生成一个强唯一密码。
* 用户已添加到标识数据库。
* 系统会通知用户并通知用户更改密码。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

下面的代码概述了如何添加用户：

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

对于生产方案，可以遵循类似的方法。

## <a name="additional-resources"></a>其他资源

* [更改为 ASP.NET Core 2.1 及更高版本的身份验证代码](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end