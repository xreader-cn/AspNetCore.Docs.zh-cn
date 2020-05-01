---
title: ASP.NET Core 项目中的基架标识
author: rick-anderson
description: 了解如何在 ASP.NET Core 项目中基架标识。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
uid: security/authentication/scaffold-identity
ms.openlocfilehash: ac95035b114274ddaa6ccb0b5b6e3da98885e39e
ms.sourcegitcommit: 6318d2bdd63116e178c34492a904be85ec9ac108
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82604722"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="28eea-103">ASP.NET Core 项目中的基架标识</span><span class="sxs-lookup"><span data-stu-id="28eea-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="28eea-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="28eea-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="28eea-105">ASP.NET Core 提供作为[Razor 类库](xref:razor-pages/ui-class) [ASP.NET Core 标识](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="28eea-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="28eea-106">包含标识的应用程序可以应用 scaffolder 来有选择地添加标识 Razor 类库中包含的源代码（RCL）。</span><span class="sxs-lookup"><span data-stu-id="28eea-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="28eea-107">建议生成源代码，以便修改代码和更改行为。</span><span class="sxs-lookup"><span data-stu-id="28eea-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="28eea-108">例如，可以指示基架生成在注册过程中使用的代码。</span><span class="sxs-lookup"><span data-stu-id="28eea-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="28eea-109">生成的代码优先于标识 RCL 中的相同代码。</span><span class="sxs-lookup"><span data-stu-id="28eea-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="28eea-110">若要完全控制 UI，而不使用默认的 RCL，请参阅[创建完全标识 UI 源](#full)部分。</span><span class="sxs-lookup"><span data-stu-id="28eea-110">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="28eea-111">**不**包含身份验证的应用程序可以应用 scaffolder 来添加 RCL 标识包。</span><span class="sxs-lookup"><span data-stu-id="28eea-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="28eea-112">可以选择要生成的标识代码。</span><span class="sxs-lookup"><span data-stu-id="28eea-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="28eea-113">尽管 scaffolder 生成了大部分必要的代码，但你需要更新项目以完成该过程。</span><span class="sxs-lookup"><span data-stu-id="28eea-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="28eea-114">本文档介绍完成标识基架更新所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="28eea-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="28eea-115">建议使用显示文件差异的源代码管理系统，并使您能够回退更改。</span><span class="sxs-lookup"><span data-stu-id="28eea-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="28eea-116">运行标识 scaffolder 后检查更改。</span><span class="sxs-lookup"><span data-stu-id="28eea-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="28eea-117">使用[双重身份验证](xref:security/authentication/identity-enable-qrcodes)、[帐户确认和密码恢复](xref:security/authentication/accconfirm)，以及使用标识的其他安全功能时，需要提供服务。</span><span class="sxs-lookup"><span data-stu-id="28eea-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="28eea-118">基架标识时不生成服务或服务存根。</span><span class="sxs-lookup"><span data-stu-id="28eea-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="28eea-119">要启用这些功能，必须手动添加服务。</span><span class="sxs-lookup"><span data-stu-id="28eea-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="28eea-120">例如，请参阅[需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。</span><span class="sxs-lookup"><span data-stu-id="28eea-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="28eea-121">当使用现有个人帐户将基架标识包含新数据上下文的项目时：</span><span class="sxs-lookup"><span data-stu-id="28eea-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="28eea-122">在`Startup.ConfigureServices`中，删除对的调用：</span><span class="sxs-lookup"><span data-stu-id="28eea-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="28eea-123">例如， `AddDbContext`在以下`AddDefaultIdentity`代码中注释掉和：</span><span class="sxs-lookup"><span data-stu-id="28eea-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="28eea-124">前面的代码注释了*区域/标识/IdentityHostingStartup*中的重复代码。</span><span class="sxs-lookup"><span data-stu-id="28eea-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="28eea-125">通常，使用各个帐户创建的应用***不***应创建新的数据上下文。</span><span class="sxs-lookup"><span data-stu-id="28eea-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="28eea-126">将标识基架到空项目中</span><span class="sxs-lookup"><span data-stu-id="28eea-126">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="28eea-127">用类似`Startup`于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="28eea-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="28eea-128">将标识基架到 Razor 项目，而无需现有授权</span><span class="sxs-lookup"><span data-stu-id="28eea-128">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="28eea-129">在*区域/标识/IdentityHostingStartup*中配置标识。</span><span class="sxs-lookup"><span data-stu-id="28eea-129">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="28eea-130">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="28eea-130">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="28eea-131">迁移、UseAuthentication 和布局</span><span class="sxs-lookup"><span data-stu-id="28eea-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="28eea-132">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="28eea-132">Enable authentication</span></span>

<span data-ttu-id="28eea-133">用类似`Startup`于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="28eea-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="28eea-134">布局更改</span><span class="sxs-lookup"><span data-stu-id="28eea-134">Layout changes</span></span>

<span data-ttu-id="28eea-135">可选：将登录名 partial （`_LoginPartial`）添加到布局文件中：</span><span class="sxs-lookup"><span data-stu-id="28eea-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="28eea-136">使用授权将标识基架到 Razor 项目</span><span class="sxs-lookup"><span data-stu-id="28eea-136">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="28eea-137">某些标识选项在*区域/标识/IdentityHostingStartup*中配置。</span><span class="sxs-lookup"><span data-stu-id="28eea-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="28eea-138">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="28eea-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="28eea-139">不使用现有授权将标识基架到 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="28eea-139">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="28eea-140">可选：将登录名 partial （`_LoginPartial`）添加到*Views/Shared/_Layout cshtml*文件中：</span><span class="sxs-lookup"><span data-stu-id="28eea-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[Main](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="28eea-141">将*Pages/shared/_LoginPartial cshtml*文件移动到*Views/shared/_LoginPartial。 cshtml*</span><span class="sxs-lookup"><span data-stu-id="28eea-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="28eea-142">在*区域/标识/IdentityHostingStartup*中配置标识。</span><span class="sxs-lookup"><span data-stu-id="28eea-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="28eea-143">有关详细信息，请参阅 IHostingStartup。</span><span class="sxs-lookup"><span data-stu-id="28eea-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="28eea-144">用类似`Startup`于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="28eea-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="28eea-145">使用授权将标识基架到 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="28eea-145">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="28eea-146">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="28eea-146">Create full identity UI source</span></span>

<span data-ttu-id="28eea-147">若要保持对标识 UI 的完全控制，请运行标识 scaffolder，并选择 "**替代所有文件**"。</span><span class="sxs-lookup"><span data-stu-id="28eea-147">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="28eea-148">以下突出显示的代码显示了将默认标识 UI 替换为 ASP.NET Core 2.1 web 应用中的标识所做的更改。</span><span class="sxs-lookup"><span data-stu-id="28eea-148">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="28eea-149">你可能希望执行此操作以对标识 UI 具有完全控制。</span><span class="sxs-lookup"><span data-stu-id="28eea-149">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="28eea-150">在以下代码中，将替换默认标识：</span><span class="sxs-lookup"><span data-stu-id="28eea-150">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="28eea-151">下面的代码将设置[LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：</span><span class="sxs-lookup"><span data-stu-id="28eea-151">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="28eea-152">注册`IEmailSender`实现，例如：</span><span class="sxs-lookup"><span data-stu-id="28eea-152">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="28eea-153">禁用注册页</span><span class="sxs-lookup"><span data-stu-id="28eea-153">Disable register page</span></span>

<span data-ttu-id="28eea-154">禁用用户注册：</span><span class="sxs-lookup"><span data-stu-id="28eea-154">To disable user registration:</span></span>

* <span data-ttu-id="28eea-155">基架标识。</span><span class="sxs-lookup"><span data-stu-id="28eea-155">Scaffold Identity.</span></span> <span data-ttu-id="28eea-156">包括帐户. Register、RegisterConfirmation。</span><span class="sxs-lookup"><span data-stu-id="28eea-156">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="28eea-157">例如：</span><span class="sxs-lookup"><span data-stu-id="28eea-157">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="28eea-158">更新*区域/标识/页/帐户/注册. .cs* ，使用户无法从此终结点注册：</span><span class="sxs-lookup"><span data-stu-id="28eea-158">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="28eea-159">更新*区域/标识/页/帐户/Register. cshtml* ，使其与前面的更改一致：</span><span class="sxs-lookup"><span data-stu-id="28eea-159">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="28eea-160">注释掉或删除*区域/标识/页面/帐户/登录名*中的注册链接</span><span class="sxs-lookup"><span data-stu-id="28eea-160">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="28eea-161">更新*Areas/Identity/Pages/Account/RegisterConfirmation*页。</span><span class="sxs-lookup"><span data-stu-id="28eea-161">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="28eea-162">删除来自 cshtml 文件的代码和链接。</span><span class="sxs-lookup"><span data-stu-id="28eea-162">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="28eea-163">从中删除确认代码`PageModel`：</span><span class="sxs-lookup"><span data-stu-id="28eea-163">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="28eea-164">使用其他应用添加用户</span><span class="sxs-lookup"><span data-stu-id="28eea-164">Use another app to add users</span></span>

<span data-ttu-id="28eea-165">提供一种在 web 应用外部添加用户的机制。</span><span class="sxs-lookup"><span data-stu-id="28eea-165">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="28eea-166">用于添加用户的选项包括：</span><span class="sxs-lookup"><span data-stu-id="28eea-166">Options to add users include:</span></span>

* <span data-ttu-id="28eea-167">专用的管理 web 应用。</span><span class="sxs-lookup"><span data-stu-id="28eea-167">A dedicated admin web app.</span></span>
* <span data-ttu-id="28eea-168">控制台应用。</span><span class="sxs-lookup"><span data-stu-id="28eea-168">A console app.</span></span>

<span data-ttu-id="28eea-169">下面的代码概述了一种添加用户的方法：</span><span class="sxs-lookup"><span data-stu-id="28eea-169">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="28eea-170">用户列表将读入内存中。</span><span class="sxs-lookup"><span data-stu-id="28eea-170">A list of users is read into memory.</span></span>
* <span data-ttu-id="28eea-171">为每个用户生成一个强唯一密码。</span><span class="sxs-lookup"><span data-stu-id="28eea-171">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="28eea-172">用户已添加到标识数据库。</span><span class="sxs-lookup"><span data-stu-id="28eea-172">The user is added to the Identity database.</span></span>
* <span data-ttu-id="28eea-173">系统会通知用户并通知用户更改密码。</span><span class="sxs-lookup"><span data-stu-id="28eea-173">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="28eea-174">下面的代码概述了如何添加用户：</span><span class="sxs-lookup"><span data-stu-id="28eea-174">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="28eea-175">对于生产方案，可以遵循类似的方法。</span><span class="sxs-lookup"><span data-stu-id="28eea-175">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="28eea-176">禁止发布静态标识资产</span><span class="sxs-lookup"><span data-stu-id="28eea-176">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="28eea-177">要阻止将静态标识资产发布到 Web 根目录，请参阅 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>。</span><span class="sxs-lookup"><span data-stu-id="28eea-177">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28eea-178">其他资源</span><span class="sxs-lookup"><span data-stu-id="28eea-178">Additional resources</span></span>

* [<span data-ttu-id="28eea-179">更改为 ASP.NET Core 2.1 及更高版本的身份验证代码</span><span class="sxs-lookup"><span data-stu-id="28eea-179">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="28eea-180">ASP.NET Core 2.1 和更高版本提供作为[Razor 类库](xref:razor-pages/ui-class) [ASP.NET Core 标识](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="28eea-180">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="28eea-181">包含标识的应用程序可以应用 scaffolder 来有选择地添加标识 Razor 类库中包含的源代码（RCL）。</span><span class="sxs-lookup"><span data-stu-id="28eea-181">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="28eea-182">建议生成源代码，以便修改代码和更改行为。</span><span class="sxs-lookup"><span data-stu-id="28eea-182">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="28eea-183">例如，可以指示基架生成在注册过程中使用的代码。</span><span class="sxs-lookup"><span data-stu-id="28eea-183">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="28eea-184">生成的代码优先于标识 RCL 中的相同代码。</span><span class="sxs-lookup"><span data-stu-id="28eea-184">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="28eea-185">若要完全控制 UI，而不使用默认的 RCL，请参阅[创建完全标识 UI 源](#full)部分。</span><span class="sxs-lookup"><span data-stu-id="28eea-185">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="28eea-186">**不**包含身份验证的应用程序可以应用 scaffolder 来添加 RCL 标识包。</span><span class="sxs-lookup"><span data-stu-id="28eea-186">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="28eea-187">可以选择要生成的标识代码。</span><span class="sxs-lookup"><span data-stu-id="28eea-187">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="28eea-188">尽管 scaffolder 生成了大部分必要的代码，但你必须更新项目才能完成此过程。</span><span class="sxs-lookup"><span data-stu-id="28eea-188">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="28eea-189">本文档介绍完成标识基架更新所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="28eea-189">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="28eea-190">运行标识 scaffolder 时，会在项目目录中创建一个*ScaffoldingReadme*文件。</span><span class="sxs-lookup"><span data-stu-id="28eea-190">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="28eea-191">*ScaffoldingReadme*文件包含有关完成标识基架更新所需内容的一般说明。</span><span class="sxs-lookup"><span data-stu-id="28eea-191">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="28eea-192">本文档包含的有关*ScaffoldingReadme*文件的完整说明。</span><span class="sxs-lookup"><span data-stu-id="28eea-192">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="28eea-193">建议使用显示文件差异的源代码管理系统，并使您能够回退更改。</span><span class="sxs-lookup"><span data-stu-id="28eea-193">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="28eea-194">运行标识 scaffolder 后检查更改。</span><span class="sxs-lookup"><span data-stu-id="28eea-194">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="28eea-195">使用[双重身份验证](xref:security/authentication/identity-enable-qrcodes)、[帐户确认和密码恢复](xref:security/authentication/accconfirm)，以及使用标识的其他安全功能时，需要提供服务。</span><span class="sxs-lookup"><span data-stu-id="28eea-195">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="28eea-196">基架标识时不生成服务或服务存根。</span><span class="sxs-lookup"><span data-stu-id="28eea-196">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="28eea-197">要启用这些功能，必须手动添加服务。</span><span class="sxs-lookup"><span data-stu-id="28eea-197">Services to enable these features must be added manually.</span></span> <span data-ttu-id="28eea-198">例如，请参阅[需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。</span><span class="sxs-lookup"><span data-stu-id="28eea-198">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="28eea-199">将标识基架到空项目中</span><span class="sxs-lookup"><span data-stu-id="28eea-199">Scaffold identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="28eea-200">将以下突出显示的调用添加`Startup`到类：</span><span class="sxs-lookup"><span data-stu-id="28eea-200">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="28eea-201">将标识基架到 Razor 项目，而无需现有授权</span><span class="sxs-lookup"><span data-stu-id="28eea-201">Scaffold identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="28eea-202">在*区域/标识/IdentityHostingStartup*中配置标识。</span><span class="sxs-lookup"><span data-stu-id="28eea-202">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="28eea-203">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="28eea-203">for more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="28eea-204">迁移、UseAuthentication 和布局</span><span class="sxs-lookup"><span data-stu-id="28eea-204">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="28eea-205">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="28eea-205">Enable authentication</span></span>

<span data-ttu-id="28eea-206">`Startup`在类`Configure`的方法中，在`UseStaticFiles`以下情况下调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) ：</span><span class="sxs-lookup"><span data-stu-id="28eea-206">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="28eea-207">布局更改</span><span class="sxs-lookup"><span data-stu-id="28eea-207">Layout changes</span></span>

<span data-ttu-id="28eea-208">可选：将登录名 partial （`_LoginPartial`）添加到布局文件中：</span><span class="sxs-lookup"><span data-stu-id="28eea-208">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[Main](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="28eea-209">使用授权将标识基架到 Razor 项目</span><span class="sxs-lookup"><span data-stu-id="28eea-209">Scaffold identity into a Razor project with authorization</span></span>

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
<span data-ttu-id="28eea-210">某些标识选项在*区域/标识/IdentityHostingStartup*中配置。</span><span class="sxs-lookup"><span data-stu-id="28eea-210">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="28eea-211">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="28eea-211">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="28eea-212">不使用现有授权将标识基架到 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="28eea-212">Scaffold identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="28eea-213">可选：将登录名 partial （`_LoginPartial`）添加到*Views/Shared/_Layout cshtml*文件中：</span><span class="sxs-lookup"><span data-stu-id="28eea-213">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="28eea-214">将*Pages/shared/_LoginPartial cshtml*文件移动到*Views/shared/_LoginPartial。 cshtml*</span><span class="sxs-lookup"><span data-stu-id="28eea-214">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="28eea-215">在*区域/标识/IdentityHostingStartup*中配置标识。</span><span class="sxs-lookup"><span data-stu-id="28eea-215">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="28eea-216">有关详细信息，请参阅 IHostingStartup。</span><span class="sxs-lookup"><span data-stu-id="28eea-216">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="28eea-217">调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)之后`UseStaticFiles`：</span><span class="sxs-lookup"><span data-stu-id="28eea-217">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="28eea-218">使用授权将标识基架到 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="28eea-218">Scaffold identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="28eea-219">删除*页面/共享*文件夹和该文件夹中的文件。</span><span class="sxs-lookup"><span data-stu-id="28eea-219">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="28eea-220">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="28eea-220">Create full identity UI source</span></span>

<span data-ttu-id="28eea-221">若要保持对标识 UI 的完全控制，请运行标识 scaffolder，并选择 "**替代所有文件**"。</span><span class="sxs-lookup"><span data-stu-id="28eea-221">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="28eea-222">以下突出显示的代码显示了将默认标识 UI 替换为 ASP.NET Core 2.1 web 应用中的标识所做的更改。</span><span class="sxs-lookup"><span data-stu-id="28eea-222">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="28eea-223">你可能希望执行此操作以对标识 UI 具有完全控制。</span><span class="sxs-lookup"><span data-stu-id="28eea-223">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="28eea-224">在以下代码中，将替换默认标识：</span><span class="sxs-lookup"><span data-stu-id="28eea-224">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="28eea-225">下面的代码将设置[LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：</span><span class="sxs-lookup"><span data-stu-id="28eea-225">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="28eea-226">注册`IEmailSender`实现，例如：</span><span class="sxs-lookup"><span data-stu-id="28eea-226">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="28eea-227">禁用注册页</span><span class="sxs-lookup"><span data-stu-id="28eea-227">Disable register page</span></span>

<span data-ttu-id="28eea-228">禁用用户注册：</span><span class="sxs-lookup"><span data-stu-id="28eea-228">To disable user registration:</span></span>

* <span data-ttu-id="28eea-229">基架标识。</span><span class="sxs-lookup"><span data-stu-id="28eea-229">Scaffold Identity.</span></span> <span data-ttu-id="28eea-230">包括帐户. Register、RegisterConfirmation。</span><span class="sxs-lookup"><span data-stu-id="28eea-230">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="28eea-231">例如：</span><span class="sxs-lookup"><span data-stu-id="28eea-231">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="28eea-232">更新*区域/标识/页/帐户/注册. .cs* ，使用户无法从此终结点注册：</span><span class="sxs-lookup"><span data-stu-id="28eea-232">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="28eea-233">更新*区域/标识/页/帐户/Register. cshtml* ，使其与前面的更改一致：</span><span class="sxs-lookup"><span data-stu-id="28eea-233">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="28eea-234">注释掉或删除*区域/标识/页面/帐户/登录名*中的注册链接</span><span class="sxs-lookup"><span data-stu-id="28eea-234">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="28eea-235">更新*Areas/Identity/Pages/Account/RegisterConfirmation*页。</span><span class="sxs-lookup"><span data-stu-id="28eea-235">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="28eea-236">删除来自 cshtml 文件的代码和链接。</span><span class="sxs-lookup"><span data-stu-id="28eea-236">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="28eea-237">从中删除确认代码`PageModel`：</span><span class="sxs-lookup"><span data-stu-id="28eea-237">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="28eea-238">使用其他应用添加用户</span><span class="sxs-lookup"><span data-stu-id="28eea-238">Use another app to add users</span></span>

<span data-ttu-id="28eea-239">提供一种在 web 应用外部添加用户的机制。</span><span class="sxs-lookup"><span data-stu-id="28eea-239">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="28eea-240">用于添加用户的选项包括：</span><span class="sxs-lookup"><span data-stu-id="28eea-240">Options to add users include:</span></span>

* <span data-ttu-id="28eea-241">专用的管理 web 应用。</span><span class="sxs-lookup"><span data-stu-id="28eea-241">A dedicated admin web app.</span></span>
* <span data-ttu-id="28eea-242">控制台应用。</span><span class="sxs-lookup"><span data-stu-id="28eea-242">A console app.</span></span>

<span data-ttu-id="28eea-243">下面的代码概述了一种添加用户的方法：</span><span class="sxs-lookup"><span data-stu-id="28eea-243">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="28eea-244">用户列表将读入内存中。</span><span class="sxs-lookup"><span data-stu-id="28eea-244">A list of users is read into memory.</span></span>
* <span data-ttu-id="28eea-245">为每个用户生成一个强唯一密码。</span><span class="sxs-lookup"><span data-stu-id="28eea-245">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="28eea-246">用户已添加到标识数据库。</span><span class="sxs-lookup"><span data-stu-id="28eea-246">The user is added to the Identity database.</span></span>
* <span data-ttu-id="28eea-247">系统会通知用户并通知用户更改密码。</span><span class="sxs-lookup"><span data-stu-id="28eea-247">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="28eea-248">下面的代码概述了如何添加用户：</span><span class="sxs-lookup"><span data-stu-id="28eea-248">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="28eea-249">对于生产方案，可以遵循类似的方法。</span><span class="sxs-lookup"><span data-stu-id="28eea-249">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="28eea-250">其他资源</span><span class="sxs-lookup"><span data-stu-id="28eea-250">Additional resources</span></span>

* [<span data-ttu-id="28eea-251">更改为 ASP.NET Core 2.1 及更高版本的身份验证代码</span><span class="sxs-lookup"><span data-stu-id="28eea-251">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end