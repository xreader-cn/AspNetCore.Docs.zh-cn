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
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="62e03-103">ASP.NET Core 项目中的基架标识</span><span class="sxs-lookup"><span data-stu-id="62e03-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="62e03-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="62e03-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="62e03-105">ASP.NET Core 2.1 及更高版本提供了[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="62e03-105">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="62e03-106">包含标识的应用程序可以应用基架来有选择地添加包含在标识 Razor 类库 (RCL) 的源代码。</span><span class="sxs-lookup"><span data-stu-id="62e03-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="62e03-107">建议生成源代码，以便修改代码和更改行为。</span><span class="sxs-lookup"><span data-stu-id="62e03-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="62e03-108">例如，可以指示基架生成在注册过程中使用的代码。</span><span class="sxs-lookup"><span data-stu-id="62e03-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="62e03-109">生成的代码优先于标识 RCL 中的相同代码。</span><span class="sxs-lookup"><span data-stu-id="62e03-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="62e03-110">若要获取的用户界面的完全控制，并且使用默认 RCL，请参阅部分[创建完整的标识 UI 源](#full)。</span><span class="sxs-lookup"><span data-stu-id="62e03-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="62e03-111">执行操作的应用程序**不**包括身份验证可以应用基架添加 RCL 标识包。</span><span class="sxs-lookup"><span data-stu-id="62e03-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="62e03-112">可以选择要生成的标识代码。</span><span class="sxs-lookup"><span data-stu-id="62e03-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="62e03-113">虽然基架生成大部分所需的代码，但您必须更新项目以完成该过程。</span><span class="sxs-lookup"><span data-stu-id="62e03-113">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="62e03-114">本文档介绍完成标识基架更新所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="62e03-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="62e03-115">当运行时标识基架时， *ScaffoldingReadme.txt*在项目目录中创建文件。</span><span class="sxs-lookup"><span data-stu-id="62e03-115">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="62e03-116">*ScaffoldingReadme.txt*文件包含所需完成标识基架更新的一般说明。</span><span class="sxs-lookup"><span data-stu-id="62e03-116">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="62e03-117">本文档包含更完整说明比*ScaffoldingReadme.txt*文件。</span><span class="sxs-lookup"><span data-stu-id="62e03-117">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="62e03-118">我们建议使用一个源控制系统，显示文件的差异，并允许您后退的更改。</span><span class="sxs-lookup"><span data-stu-id="62e03-118">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="62e03-119">运行标识基架后检查所做的更改。</span><span class="sxs-lookup"><span data-stu-id="62e03-119">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="62e03-120">使用时，服务所需[双因素身份验证](xref:security/authentication/identity-enable-qrcodes)，[帐户确认和密码恢复](xref:security/authentication/accconfirm)，和使用标识其他安全功能。</span><span class="sxs-lookup"><span data-stu-id="62e03-120">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="62e03-121">基架标识时，不会生成服务或服务存根 （stub）。</span><span class="sxs-lookup"><span data-stu-id="62e03-121">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="62e03-122">必须手动添加服务，以启用这些功能。</span><span class="sxs-lookup"><span data-stu-id="62e03-122">Services to enable these features must be added manually.</span></span> <span data-ttu-id="62e03-123">有关示例，请参阅[需要电子邮件确认](xref:security/authentication/accconfirm#require-email-confirmation)。</span><span class="sxs-lookup"><span data-stu-id="62e03-123">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="62e03-124">基架标识为一个空项目</span><span class="sxs-lookup"><span data-stu-id="62e03-124">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="62e03-125">添加以下突出显示的调用到`Startup`类：</span><span class="sxs-lookup"><span data-stu-id="62e03-125">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="62e03-126">基架的 Razor 项目没有现有的授权到标识</span><span class="sxs-lookup"><span data-stu-id="62e03-126">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="62e03-127">在中配置标识*Areas/Identity/IdentityHostingStartup.cs*。</span><span class="sxs-lookup"><span data-stu-id="62e03-127">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="62e03-128">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="62e03-128">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="62e03-129">迁移、 UseAuthentication 和布局</span><span class="sxs-lookup"><span data-stu-id="62e03-129">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="62e03-130">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="62e03-130">Enable authentication</span></span>

<span data-ttu-id="62e03-131">在中`Configure`方法`Startup`类中，调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)后`UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="62e03-131">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="62e03-132">布局更改</span><span class="sxs-lookup"><span data-stu-id="62e03-132">Layout changes</span></span>

<span data-ttu-id="62e03-133">可选：添加部分的登录名 (`_LoginPartial`) 的布局文件：</span><span class="sxs-lookup"><span data-stu-id="62e03-133">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="62e03-134">基架标识为具有授权的 Razor 项目</span><span class="sxs-lookup"><span data-stu-id="62e03-134">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="62e03-135">在中配置一些标识选项*Areas/Identity/IdentityHostingStartup.cs*。</span><span class="sxs-lookup"><span data-stu-id="62e03-135">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="62e03-136">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="62e03-136">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="62e03-137">基架标识到 MVC 项目中没有现有的授权</span><span class="sxs-lookup"><span data-stu-id="62e03-137">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="62e03-138">可选：添加部分的登录名 (`_LoginPartial`) 到*Views/Shared/_Layout.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="62e03-138">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="62e03-139">移动*Pages/Shared/_LoginPartial.cshtml*文件为*Views/Shared/_LoginPartial.cshtml*</span><span class="sxs-lookup"><span data-stu-id="62e03-139">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="62e03-140">在中配置标识*Areas/Identity/IdentityHostingStartup.cs*。</span><span class="sxs-lookup"><span data-stu-id="62e03-140">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="62e03-141">有关详细信息，请参阅 IHostingStartup。</span><span class="sxs-lookup"><span data-stu-id="62e03-141">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="62e03-142">调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)后`UseStaticFiles`:</span><span class="sxs-lookup"><span data-stu-id="62e03-142">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="62e03-143">基架标识为具有授权的 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="62e03-143">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext --files Account.Register
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="62e03-144">删除*Pages/共享*文件夹和该文件夹中的文件。</span><span class="sxs-lookup"><span data-stu-id="62e03-144">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="62e03-145">创建完整的标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="62e03-145">Create full identity UI source</span></span>

<span data-ttu-id="62e03-146">若要维护标识用户界面的完全控制，运行标识基架，并选择**重写的所有文件**。</span><span class="sxs-lookup"><span data-stu-id="62e03-146">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="62e03-147">以下突出显示的代码显示默认标识 UI 替换标识在 ASP.NET Core 2.1 web 应用的更改。</span><span class="sxs-lookup"><span data-stu-id="62e03-147">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="62e03-148">您可能想要执行此操作以具有完全控制权限的标识 UI。</span><span class="sxs-lookup"><span data-stu-id="62e03-148">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="62e03-149">在下面的代码替换默认标识：</span><span class="sxs-lookup"><span data-stu-id="62e03-149">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="62e03-150">下面的代码集[LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)， [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)，并[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span><span class="sxs-lookup"><span data-stu-id="62e03-150">The following the code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="62e03-151">注册`IEmailSender`实现，例如：</span><span class="sxs-lookup"><span data-stu-id="62e03-151">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="62e03-152">其他资源</span><span class="sxs-lookup"><span data-stu-id="62e03-152">Additional resources</span></span>

* [<span data-ttu-id="62e03-153">更改身份验证代码到 ASP.NET Core 2.1 及更高版本</span><span class="sxs-lookup"><span data-stu-id="62e03-153">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)
