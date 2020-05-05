---
title: ASP.NET Core Identity项目中的基架
author: rick-anderson
description: 了解如何在 ASP.NET Core Identity项目中进行基架。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 6f1ff69863e14c73e90496ea61188387f5267b19
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768385"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>ASP.NET Core Identity项目中的基架

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET Core[提供Razor类库](xref:razor-pages/ui-class) [ASP.NET Core Identity ](xref:security/authentication/identity) 。 包含Identity的应用程序可以应用 scaffolder 来有选择地添加Identity Razor类库中包含的源代码（RCL）。 建议生成源代码，以便修改代码和更改行为。 例如，可以指示基架生成在注册过程中使用的代码。 生成的代码优先于Identity RCL 中的相同代码。 若要完全控制 UI，而不使用默认的 RCL，请参阅[创建完全标识 UI 源](#full)部分。

**不**包含身份验证的应用程序可以应用 scaffolder 来添加 RCL Identity包。 您可以选择Identity要生成的代码。

尽管 scaffolder 生成了大部分必要的代码，但你需要更新项目以完成该过程。 本文档介绍完成Identity基架更新所需的步骤。

建议使用显示文件差异的源代码管理系统，并使您能够回退更改。 运行Identity scaffolder 后检查更改。

使用[双重身份验证](xref:security/authentication/identity-enable-qrcodes)、[帐户确认和密码恢复](xref:security/authentication/accconfirm)和其他安全功能时，需要提供服务Identity。 基架Identity时不生成服务或服务存根。 要启用这些功能，必须手动添加服务。 例如，请参阅[需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。

当使用Identity现有个人帐户将具有新数据上下文的基架插入到项目中时：

* 在`Startup.ConfigureServices`中，删除对的调用：
  * `AddDbContext`
  * `AddDefaultIdentity`

例如， `AddDbContext`在以下`AddDefaultIdentity`代码中注释掉和：

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

前面的代码注释掉*区域/Identity/IdentityHostingStartup.cs*中的重复代码。

通常，使用各个帐户创建的应用***不***应创建新的数据上下文。

## <a name="scaffold-identity-into-an-empty-project"></a>将标识基架到空项目中

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

用类似`Startup`于下面的代码更新类：

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>不使用现有授权Razor将标识基架到项目中

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

Identity在*区域/Identity/IdentityHostingStartup.cs*中配置。 有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>迁移、UseAuthentication 和布局

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>启用身份验证

用类似`Startup`于下面的代码更新类：

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>布局更改

可选：将登录名 partial （`_LoginPartial`）添加到布局文件中：

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>使用授权将Razor标识基架到项目中

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
某些Identity选项在*区域/Identity/IdentityHostingStartup.cs*中配置。 有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

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

可选：将登录名 partial （`_LoginPartial`）添加到*Views/Shared/_Layout cshtml*文件中：

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* 将*Pages/shared/_LoginPartial cshtml*文件移动到*Views/shared/_LoginPartial。 cshtml*

Identity在*区域/Identity/IdentityHostingStartup.cs*中配置。 有关详细信息，请参阅 IHostingStartup。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

用类似`Startup`于下面的代码更新类：

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

若要维护 UI 的完全Identity控制，请运行Identity Scaffolder 并选择 "**替代所有文件**"。

以下突出显示的代码显示了将默认Identity UI 替换为 ASP.NET Core Identity 2.1 web 应用中的更改。 你可能希望执行此操作以对Identity UI 具有完全控制。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

在以下Identity代码中，将替换默认值：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

下面的代码将设置[LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

注册`IEmailSender`实现，例如：

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

* 基架Identity。 包括帐户. Register、RegisterConfirmation。 例如：

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* 更新*区域/Identity/Pages/Account/Register.cshtml.cs* ，使用户无法从此终结点注册：

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 更新*区域/Identity/Pages/Account/Register.cshtml* ，使其与前面的更改一致：

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* 注释掉或删除*区域/Identity/Pages/Account/Login.cshtml*中的注册链接

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* 更新 "*区域/Identity/Pages/Account/RegisterConfirmation* " 页。

  * 删除来自 cshtml 文件的代码和链接。
  * 从中删除确认代码`PageModel`：

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
* 用户已添加到Identity数据库。
* 系统会通知用户并通知用户更改密码。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

下面的代码概述了如何添加用户：

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

对于生产方案，可以遵循类似的方法。

## <a name="prevent-publish-of-static-identity-assets"></a>禁止发布静态Identity资产

若要防止将Identity静态资产发布到 web 根目录， <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>请参阅。

## <a name="additional-resources"></a>其他资源

* [更改为 ASP.NET Core 2.1 及更高版本的身份验证代码](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core 2.1 和更高版本提供了[ASP.NET Core Identity ](xref:security/authentication/identity)为[ Razor类库](xref:razor-pages/ui-class)。 包含Identity的应用程序可以应用 scaffolder 来有选择地添加Identity Razor类库中包含的源代码（RCL）。 建议生成源代码，以便修改代码和更改行为。 例如，可以指示基架生成在注册过程中使用的代码。 生成的代码优先于Identity RCL 中的相同代码。 若要完全控制 UI，而不使用默认的 RCL，请参阅[创建完全标识 UI 源](#full)部分。

**不**包含身份验证的应用程序可以应用 scaffolder 来添加 RCL Identity包。 您可以选择Identity要生成的代码。

尽管 scaffolder 生成了大部分必要的代码，但你必须更新项目才能完成此过程。 本文档介绍完成Identity基架更新所需的步骤。

运行Identity scaffolder 时，将在项目目录中创建*ScaffoldingReadme*文件。 *ScaffoldingReadme*文件包含有关完成Identity基架更新所需内容的一般说明。 本文档包含的有关*ScaffoldingReadme*文件的完整说明。

建议使用显示文件差异的源代码管理系统，并使您能够回退更改。 运行Identity scaffolder 后检查更改。

> [!NOTE]
> 使用[双重身份验证](xref:security/authentication/identity-enable-qrcodes)、[帐户确认和密码恢复](xref:security/authentication/accconfirm)和其他安全功能时，需要提供服务Identity。 基架Identity时不生成服务或服务存根。 要启用这些功能，必须手动添加服务。 例如，请参阅[需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。

## <a name="scaffold-identity-into-an-empty-project"></a>将标识基架到空项目中

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

将以下突出显示的调用添加`Startup`到类：

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>不使用现有授权Razor将标识基架到项目中

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

Identity在*区域/Identity/IdentityHostingStartup.cs*中配置。 有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>迁移、UseAuthentication 和布局

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>启用身份验证

`Startup`在类`Configure`的方法中，在`UseStaticFiles`以下情况下调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ：

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>布局更改

可选：将登录名 partial （`_LoginPartial`）添加到布局文件中：

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>使用授权将Razor标识基架到项目中

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
某些Identity选项在*区域/Identity/IdentityHostingStartup.cs*中配置。 有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

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

可选：将登录名 partial （`_LoginPartial`）添加到*Views/Shared/_Layout cshtml*文件中：

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* 将*Pages/shared/_LoginPartial cshtml*文件移动到*Views/shared/_LoginPartial。 cshtml*

Identity在*区域/Identity/IdentityHostingStartup.cs*中配置。 有关详细信息，请参阅 IHostingStartup。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)之后`UseStaticFiles`：

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

若要维护 UI 的完全Identity控制，请运行Identity Scaffolder 并选择 "**替代所有文件**"。

以下突出显示的代码显示了将默认Identity UI 替换为 ASP.NET Core Identity 2.1 web 应用中的更改。 你可能希望执行此操作以对Identity UI 具有完全控制。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

在以下Identity代码中，将替换默认值：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

下面的代码将设置[LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

注册`IEmailSender`实现，例如：

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

* 基架Identity。 包括帐户. Register、RegisterConfirmation。 例如：

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* 更新*区域/Identity/Pages/Account/Register.cshtml.cs* ，使用户无法从此终结点注册：

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* 更新*区域/Identity/Pages/Account/Register.cshtml* ，使其与前面的更改一致：

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* 注释掉或删除*区域/Identity/Pages/Account/Login.cshtml*中的注册链接

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* 更新 "*区域/Identity/Pages/Account/RegisterConfirmation* " 页。

  * 删除来自 cshtml 文件的代码和链接。
  * 从中删除确认代码`PageModel`：

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
* 用户已添加到Identity数据库。
* 系统会通知用户并通知用户更改密码。

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

下面的代码概述了如何添加用户：

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

对于生产方案，可以遵循类似的方法。

## <a name="additional-resources"></a>其他资源

* [更改为 ASP.NET Core 2.1 及更高版本的身份验证代码](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end