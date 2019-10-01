---
title: ASP.NET Core 项目中的基架标识
author: rick-anderson
description: 了解如何创建标识的基架 ASP.NET Core 项目中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: f3ae089d344d95ed84c9720ab4ba2c697400901e
ms.sourcegitcommit: dc96d76f6b231de59586fcbb989a7fb5106d26a8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703764"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>ASP.NET Core 项目中的基架标识

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 2.1 及更高版本提供了[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。 包含标识的应用程序可以应用 scaffolder 来有选择地添加标识 Razor 类库中包含的源代码（RCL）。 建议生成源代码，以便修改代码和更改行为。 例如，可以指示基架生成在注册过程中使用的代码。 生成的代码优先于标识 RCL 中的相同代码。 若要完全控制 UI，而不使用默认的 RCL，请参阅[创建完全标识 UI 源](#full)部分。

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

在 @no__t 1 类的 `Configure` 方法中，在 `UseStaticFiles` 后调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ：

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>布局更改

可选：将登录部分（`_LoginPartial`）添加到布局文件中：

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>使用授权将标识基架到 Razor 项目

<!--
Use >=2.1: dotnet new webapp -au Individual -o RPauth
Use = 2.0: dotnet new razor -au Individual -o RPauth
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files Account.Register
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

* 将*Pages/shared/_LoginPartial*文件移动到*Views/shared/_LoginPartial*

在*区域/标识/IdentityHostingStartup*中配置标识。 有关详细信息，请参阅 IHostingStartup。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

在 `UseStaticFiles` 后调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ：

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>使用授权将标识基架到 MVC 项目

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
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

## <a name="additional-resources"></a>其他资源

* [更改为 ASP.NET Core 2.1 及更高版本的身份验证代码](xref:migration/20_21#changes-to-authentication-code)
