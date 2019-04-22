---
title: ASP.NET Core 项目中的基架标识
author: rick-anderson
description: 了解如何创建标识的基架 ASP.NET Core 项目中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 7445db31e461bf61e8a91af7239187a6ece9d011
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59705466"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a>ASP.NET Core 项目中的基架标识

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

ASP.NET Core 2.1 及更高版本提供了[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。 包含标识的应用程序可以应用基架来有选择地添加包含在标识 Razor 类库 (RCL) 的源代码。 建议生成源代码，以便修改代码和更改行为。 例如，可以指示基架生成在注册过程中使用的代码。 生成的代码优先于标识 RCL 中的相同代码。 若要获取的用户界面的完全控制，并且使用默认 RCL，请参阅部分[创建完整的标识 UI 源](#full)。

执行操作的应用程序**不**包括身份验证可以应用基架添加 RCL 标识包。 可以选择要生成的标识代码。

虽然基架生成大部分所需的代码，但您必须更新项目以完成该过程。 本文档介绍完成标识基架更新所需的步骤。

当运行时标识基架时， *ScaffoldingReadme.txt*在项目目录中创建文件。 *ScaffoldingReadme.txt*文件包含所需完成标识基架更新的一般说明。 本文档包含更完整说明比*ScaffoldingReadme.txt*文件。

我们建议使用一个源控制系统，显示文件的差异，并允许您后退的更改。 运行标识基架后检查所做的更改。

> [!NOTE]
> 使用时，服务所需[双因素身份验证](xref:security/authentication/identity-enable-qrcodes)，[帐户确认和密码恢复](xref:security/authentication/accconfirm)，和使用标识其他安全功能。 基架标识时，不会生成服务或服务存根 （stub）。 必须手动添加服务，以启用这些功能。 有关示例，请参阅[需要电子邮件确认](xref:security/authentication/accconfirm#require-email-confirmation)。

## <a name="scaffold-identity-into-an-empty-project"></a>基架标识为一个空项目

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

添加以下突出显示的调用到`Startup`类：

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a>基架的 Razor 项目没有现有的授权到标识

<!--
set projNam=RPnoAuth
set projType=webapp
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

在中配置标识*Areas/Identity/IdentityHostingStartup.cs*。 有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a>迁移、 UseAuthentication 和布局

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a>启用身份验证

在中`Configure`方法`Startup`类中，调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)后`UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a>布局更改

可选：添加部分的登录名 (`_LoginPartial`) 的布局文件：

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a>基架标识为具有授权的 Razor 项目

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
在中配置一些标识选项*Areas/Identity/IdentityHostingStartup.cs*。 有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a>基架标识到 MVC 项目中没有现有的授权

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

可选：添加部分的登录名 (`_LoginPartial`) 到*Views/Shared/_Layout.cshtml*文件：

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* 移动*Pages/Shared/_LoginPartial.cshtml*文件为*Views/Shared/_LoginPartial.cshtml*

在中配置标识*Areas/Identity/IdentityHostingStartup.cs*。 有关详细信息，请参阅 IHostingStartup。

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)后`UseStaticFiles`:

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a>基架标识为具有授权的 MVC 项目

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

删除*Pages/共享*文件夹和该文件夹中的文件。

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a>创建完整的标识 UI 源

若要维护标识用户界面的完全控制，运行标识基架，并选择**重写的所有文件**。

以下突出显示的代码显示默认标识 UI 替换标识在 ASP.NET Core 2.1 web 应用的更改。 您可能想要执行此操作以具有完全控制权限的标识 UI。

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

在下面的代码替换默认标识：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

下面的代码集[LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)， [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)，并[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

注册`IEmailSender`实现，例如：

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a>其他资源

* [更改身份验证代码到 ASP.NET Core 2.1 及更高版本](xref:migration/20_21#changes-to-authentication-code)
