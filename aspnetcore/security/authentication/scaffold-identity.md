---
title: IdentityASP.NET Core 项目中的基架
author: rick-anderson
description: 了解如何 Identity 在 ASP.NET Core 项目中进行基架。
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
ms.openlocfilehash: 36afa8ece58843b434ebfba6305bffdb9eb9bca0
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724284"
---
# <a name="scaffold-identity-in-aspnet-core-projects"></a><span data-ttu-id="68b1c-103">IdentityASP.NET Core 项目中的基架</span><span class="sxs-lookup"><span data-stu-id="68b1c-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="68b1c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="68b1c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="68b1c-105">ASP.NET Core 提供[ Razor 类库](xref:razor-pages/ui-class) [ASP.NET Core Identity ](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="68b1c-106">包含的应用程序 Identity 可以应用 scaffolder 来有选择地添加类库中包含的源代码 Identity Razor （RCL）。</span><span class="sxs-lookup"><span data-stu-id="68b1c-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="68b1c-107">建议生成源代码，以便修改代码和更改行为。</span><span class="sxs-lookup"><span data-stu-id="68b1c-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="68b1c-108">例如，可以指示基架生成在注册过程中使用的代码。</span><span class="sxs-lookup"><span data-stu-id="68b1c-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="68b1c-109">生成的代码优先于 Identity RCL 中的相同代码。</span><span class="sxs-lookup"><span data-stu-id="68b1c-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="68b1c-110">若要完全控制 UI，而不使用默认的 RCL，请参阅[创建完整 Identity UI 源](#full)部分。</span><span class="sxs-lookup"><span data-stu-id="68b1c-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="68b1c-111">**不**包含身份验证的应用程序可以应用 scaffolder 来添加 RCL Identity 包。</span><span class="sxs-lookup"><span data-stu-id="68b1c-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="68b1c-112">可以选择要生成的 Identity 代码。</span><span class="sxs-lookup"><span data-stu-id="68b1c-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="68b1c-113">尽管 scaffolder 生成了大部分必要的代码，但你需要更新项目以完成该过程。</span><span class="sxs-lookup"><span data-stu-id="68b1c-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="68b1c-114">本文档介绍完成基架更新所需的步骤 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="68b1c-115">建议使用显示文件差异的源代码管理系统，并使您能够回退更改。</span><span class="sxs-lookup"><span data-stu-id="68b1c-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="68b1c-116">运行 scaffolder 后检查更改 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="68b1c-117">使用[双重身份验证](xref:security/authentication/identity-enable-qrcodes)、[帐户确认和密码恢复](xref:security/authentication/accconfirm)和其他安全功能时，需要提供服务 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="68b1c-118">基架时不生成服务或服务存根 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="68b1c-119">要启用这些功能，必须手动添加服务。</span><span class="sxs-lookup"><span data-stu-id="68b1c-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="68b1c-120">例如，请参阅[需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。</span><span class="sxs-lookup"><span data-stu-id="68b1c-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="68b1c-121">当 Identity 使用现有个人帐户将具有新数据上下文的基架插入到项目中时：</span><span class="sxs-lookup"><span data-stu-id="68b1c-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="68b1c-122">在中 `Startup.ConfigureServices` ，删除对的调用：</span><span class="sxs-lookup"><span data-stu-id="68b1c-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="68b1c-123">例如， `AddDbContext` `AddDefaultIdentity` 在以下代码中注释掉和：</span><span class="sxs-lookup"><span data-stu-id="68b1c-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="68b1c-124">前面的代码注释掉*区域/ Identity /IdentityHostingStartup.cs*中的重复代码。</span><span class="sxs-lookup"><span data-stu-id="68b1c-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="68b1c-125">通常，使用各个帐户创建的应用***不***应创建新的数据上下文。</span><span class="sxs-lookup"><span data-stu-id="68b1c-125">Typically, apps that were created with individual accounts should ***not*** create a new data context.</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="68b1c-126">基架 Identity 到空项目</span><span class="sxs-lookup"><span data-stu-id="68b1c-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="68b1c-127">`Startup`用类似于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="68b1c-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="68b1c-128">基架 Identity 到 Razor 无现有授权的项目</span><span class="sxs-lookup"><span data-stu-id="68b1c-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="68b1c-129">在*区域/ Identity /IdentityHostingStartup.cs*中配置。</span><span class="sxs-lookup"><span data-stu-id="68b1c-129"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="68b1c-130">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="68b1c-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="68b1c-131">迁移、UseAuthentication 和布局</span><span class="sxs-lookup"><span data-stu-id="68b1c-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="68b1c-132">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="68b1c-132">Enable authentication</span></span>

<span data-ttu-id="68b1c-133">`Startup`用类似于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="68b1c-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="68b1c-134">布局更改</span><span class="sxs-lookup"><span data-stu-id="68b1c-134">Layout changes</span></span>

<span data-ttu-id="68b1c-135">可选：将登录名 partial （ `_LoginPartial` ）添加到布局文件中：</span><span class="sxs-lookup"><span data-stu-id="68b1c-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="68b1c-136">Identity Razor 使用授权基架到项目中</span><span class="sxs-lookup"><span data-stu-id="68b1c-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="68b1c-137">某些 Identity 选项在*区域/ Identity /IdentityHostingStartup.cs*中配置。</span><span class="sxs-lookup"><span data-stu-id="68b1c-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="68b1c-138">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="68b1c-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="68b1c-139">基架 Identity 到没有现有授权的 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="68b1c-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="68b1c-140">可选：将登录名 partial （ `_LoginPartial` ）添加到*Views/Shared/_Layout cshtml*文件中：</span><span class="sxs-lookup"><span data-stu-id="68b1c-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="68b1c-141">将*Pages/shared/_LoginPartial cshtml*文件移动到*Views/shared/_LoginPartial。 cshtml*</span><span class="sxs-lookup"><span data-stu-id="68b1c-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="68b1c-142">在*区域/ Identity /IdentityHostingStartup.cs*中配置。</span><span class="sxs-lookup"><span data-stu-id="68b1c-142"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="68b1c-143">有关详细信息，请参阅 IHostingStartup。</span><span class="sxs-lookup"><span data-stu-id="68b1c-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="68b1c-144">`Startup`用类似于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="68b1c-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="68b1c-145">Identity使用授权基架到 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="68b1c-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-identity-into-a-blazor-server-project-without-existing-authorization"></a><span data-ttu-id="68b1c-146">基架 Identity 到 Blazor 无现有授权的服务器项目中</span><span class="sxs-lookup"><span data-stu-id="68b1c-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

Identity<span data-ttu-id="68b1c-147">在*区域/ Identity /IdentityHostingStartup.cs*中配置。</span><span class="sxs-lookup"><span data-stu-id="68b1c-147"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="68b1c-148">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="68b1c-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="68b1c-149">迁移</span><span class="sxs-lookup"><span data-stu-id="68b1c-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="68b1c-150">将 XSRF 令牌传递给应用</span><span class="sxs-lookup"><span data-stu-id="68b1c-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="68b1c-151">可以将令牌传递给组件：</span><span class="sxs-lookup"><span data-stu-id="68b1c-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="68b1c-152">设置身份验证令牌并将其保存到身份验证 cookie 后，可以将其传递给组件。</span><span class="sxs-lookup"><span data-stu-id="68b1c-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* Razor<span data-ttu-id="68b1c-153">组件不能 `HttpContext` 直接使用，因此无法获取要在其上发布到的注销终结点的[反请求伪造（XSRF）令牌](xref:security/anti-request-forgery) Identity `/Identity/Account/Logout` 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-153"> components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="68b1c-154">可以将 XSRF 令牌传递给组件。</span><span class="sxs-lookup"><span data-stu-id="68b1c-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="68b1c-155">有关详细信息，请参阅 <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>。</span><span class="sxs-lookup"><span data-stu-id="68b1c-155">For more information, see <xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="68b1c-156">在*Pages/_Host cshtml*文件中，在将其添加到和类后建立该令牌 `InitialApplicationState` `TokenProvider` ：</span><span class="sxs-lookup"><span data-stu-id="68b1c-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="68b1c-157">更新 `App` 组件（*app.config*）以分配 `InitialState.XsrfToken` ：</span><span class="sxs-lookup"><span data-stu-id="68b1c-157">Update the `App` component (*App.razor*) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="68b1c-158">本 `TokenProvider` 主题中演示的服务在 `LoginDisplay` 以下[布局和身份验证流更改](#layout-and-authentication-flow-changes)部分的组件中使用。</span><span class="sxs-lookup"><span data-stu-id="68b1c-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="68b1c-159">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="68b1c-159">Enable authentication</span></span>

<span data-ttu-id="68b1c-160">在 `Startup` 类中：</span><span class="sxs-lookup"><span data-stu-id="68b1c-160">In the `Startup` class:</span></span>

* <span data-ttu-id="68b1c-161">确认 Razor 在中添加了页面服务 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="68b1c-162">如果使用[TokenProvider](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)，请注册服务。</span><span class="sxs-lookup"><span data-stu-id="68b1c-162">If using the [TokenProvider](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="68b1c-163">`UseDatabaseErrorPage`对于开发环境，请在中的应用程序生成器上调用 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="68b1c-164">调用 `UseAuthentication` and `UseAuthorization` after `UseRouting` 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="68b1c-165">添加页的终结点 Razor 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="68b1c-166">布局和身份验证流更改</span><span class="sxs-lookup"><span data-stu-id="68b1c-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="68b1c-167">将 `RedirectToLogin` 组件（*RedirectToLogin*）添加到项目根目录中的应用的*共享*文件夹：</span><span class="sxs-lookup"><span data-stu-id="68b1c-167">Add a `RedirectToLogin` component (*RedirectToLogin.razor*) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo("Identity/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

将 `LoginDisplay` 组件（*LoginDisplay*）添加到应用的*共享*文件夹。 <span data-ttu-id="68b1c-169">[TokenProvider 服务](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)提供 HTML 窗体的 XSRF 标记，该标记将发布到 Identity 注销终结点：</span><span class="sxs-lookup"><span data-stu-id="68b1c-169">The [TokenProvider service](xref:security/blazor/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href="Identity/Account/Manage/Index">
            Hello, @context.User.Identity.Name!
        </a>
        <form action="/Identity/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href="Identity/Account/Register">Register</a>
        <a href="Identity/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="68b1c-170">在 `MainLayout` 组件（*Shared/MainLayout*）中，将 `LoginDisplay` 组件添加到顶层行 `<div>` 元素的内容：</span><span class="sxs-lookup"><span data-stu-id="68b1c-170">In the `MainLayout` component (*Shared/MainLayout.razor*), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="68b1c-171">样式身份验证终结点</span><span class="sxs-lookup"><span data-stu-id="68b1c-171">Style authentication endpoints</span></span>

<span data-ttu-id="68b1c-172">由于 Blazor 服务器使用 Razor 页面 Identity 页，当访问者在页面和组件之间导航时，UI 的样式会发生变化 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="68b1c-173">可以使用两个选项来处理 incongruous 样式：</span><span class="sxs-lookup"><span data-stu-id="68b1c-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-identity-components"></a><span data-ttu-id="68b1c-174">生成 Identity 组件</span><span class="sxs-lookup"><span data-stu-id="68b1c-174">Build Identity components</span></span>

<span data-ttu-id="68b1c-175">使用而不是页的组件的方法 Identity 是生成 Identity 组件。</span><span class="sxs-lookup"><span data-stu-id="68b1c-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="68b1c-176">由于 `SignInManager` `UserManager` 在组件中不支持和 Razor ，因此请使用服务器应用中的 API 终结点 Blazor 来处理用户帐户操作。</span><span class="sxs-lookup"><span data-stu-id="68b1c-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-blazor-app-styles"></a><span data-ttu-id="68b1c-177">在应用程序样式中使用自定义布局 Blazor</span><span class="sxs-lookup"><span data-stu-id="68b1c-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="68b1c-178">Identity可以修改页面布局和样式，以生成使用默认主题的页面 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="68b1c-179">本节中的示例只是一个自定义的起点。</span><span class="sxs-lookup"><span data-stu-id="68b1c-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="68b1c-180">为了获得最佳用户体验，可能需要额外的工作。</span><span class="sxs-lookup"><span data-stu-id="68b1c-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="68b1c-181">创建新 `NavMenu_IdentityLayout` 组件（*Shared/NavMenu_IdentityLayout*）。</span><span class="sxs-lookup"><span data-stu-id="68b1c-181">Create a new `NavMenu_IdentityLayout` component (*Shared/NavMenu_IdentityLayout.razor*).</span></span> <span data-ttu-id="68b1c-182">对于组件的标记和代码，请使用应用 `NavMenu` 组件（*Shared/NavMenu*）的相同内容。</span><span class="sxs-lookup"><span data-stu-id="68b1c-182">For the markup and code of the component, use the same content of the app's `NavMenu` component (*Shared/NavMenu.razor*).</span></span> <span data-ttu-id="68b1c-183">去除 `NavLink` 无法匿名访问的组件，因为组件中的自动重定向 `RedirectToLogin` 对于需要身份验证或授权的组件失败。</span><span class="sxs-lookup"><span data-stu-id="68b1c-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="68b1c-184">在*Pages/Shared/Layout*文件中，进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="68b1c-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="68b1c-185">将 Razor 指令添加到文件顶部，以使用*共享*文件夹中的标记帮助程序和应用组件：</span><span class="sxs-lookup"><span data-stu-id="68b1c-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="68b1c-186">替换 `{APPLICATION ASSEMBLY}` 为应用程序的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="68b1c-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="68b1c-187">向 `<base>` 内容添加标记和 Blazor 样式 `<link>` 表 `<head>` ：</span><span class="sxs-lookup"><span data-stu-id="68b1c-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="68b1c-188">将标记的内容更改 `<body>` 为以下内容：</span><span class="sxs-lookup"><span data-stu-id="68b1c-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_IdentityLayout)" 
          render-mode="ServerPrerendered" />
  </div>

  <div class="main" style="padding-left:250px">
      <div class="top-row px-4">
          @{
              var result = Engine.FindView(ViewContext, "_LoginPartial", 
                  isMainPage: false);
          }
          @if (result.Success)
          {
              await Html.RenderPartialAsync("_LoginPartial");
          }
          else
          {
              throw new InvalidOperationException("The default Identity UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/Identity/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/Identity/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/Identity/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-identity-into-a-blazor-server-project-with-authorization"></a><span data-ttu-id="68b1c-189">Identity Blazor 使用授权基架到服务器项目中</span><span class="sxs-lookup"><span data-stu-id="68b1c-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="68b1c-190">某些 Identity 选项在*区域/ Identity /IdentityHostingStartup.cs*中配置。</span><span class="sxs-lookup"><span data-stu-id="68b1c-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="68b1c-191">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="68b1c-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="68b1c-192">创建完整的 Identity UI 源</span><span class="sxs-lookup"><span data-stu-id="68b1c-192">Create full Identity UI source</span></span>

<span data-ttu-id="68b1c-193">若要维护 UI 的完全控制 Identity ，请运行 Identity scaffolder 并选择 "**替代所有文件**"。</span><span class="sxs-lookup"><span data-stu-id="68b1c-193">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="68b1c-194">以下突出显示的代码显示了将默认 UI 替换为 Identity Identity ASP.NET Core 2.1 web 应用中的更改。</span><span class="sxs-lookup"><span data-stu-id="68b1c-194">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="68b1c-195">你可能希望执行此操作以对 UI 具有完全控制 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-195">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="68b1c-196">Identity在以下代码中，将替换默认值：</span><span class="sxs-lookup"><span data-stu-id="68b1c-196">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="68b1c-197">下面的代码将设置[LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：</span><span class="sxs-lookup"><span data-stu-id="68b1c-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="68b1c-198">注册 `IEmailSender` 实现，例如：</span><span class="sxs-lookup"><span data-stu-id="68b1c-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-a-page"></a><span data-ttu-id="68b1c-199">禁用页面</span><span class="sxs-lookup"><span data-stu-id="68b1c-199">Disable a page</span></span>

<span data-ttu-id="68b1c-200">本部分介绍如何禁用注册页面，但该方法可用于禁用任何页面。</span><span class="sxs-lookup"><span data-stu-id="68b1c-200">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="68b1c-201">禁用用户注册：</span><span class="sxs-lookup"><span data-stu-id="68b1c-201">To disable user registration:</span></span>

* <span data-ttu-id="68b1c-202">基架 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-202">Scaffold Identity.</span></span> <span data-ttu-id="68b1c-203">包括帐户. Register、RegisterConfirmation。</span><span class="sxs-lookup"><span data-stu-id="68b1c-203">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="68b1c-204">例如：</span><span class="sxs-lookup"><span data-stu-id="68b1c-204">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="68b1c-205">更新*区域/ Identity /Pages/Account/Register.cshtml.cs* ，使用户无法从此终结点注册：</span><span class="sxs-lookup"><span data-stu-id="68b1c-205">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="68b1c-206">更新*区域/ Identity /Pages/Account/Register.cshtml* ，使其与前面的更改一致：</span><span class="sxs-lookup"><span data-stu-id="68b1c-206">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="68b1c-207">注释掉或删除*区域/ Identity /Pages/Account/Login.cshtml*中的注册链接</span><span class="sxs-lookup"><span data-stu-id="68b1c-207">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="68b1c-208">更新 "*区域/ Identity /Pages/Account/RegisterConfirmation* " 页。</span><span class="sxs-lookup"><span data-stu-id="68b1c-208">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="68b1c-209">删除来自 cshtml 文件的代码和链接。</span><span class="sxs-lookup"><span data-stu-id="68b1c-209">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="68b1c-210">从中删除确认代码 `PageModel` ：</span><span class="sxs-lookup"><span data-stu-id="68b1c-210">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="68b1c-211">使用其他应用添加用户</span><span class="sxs-lookup"><span data-stu-id="68b1c-211">Use another app to add users</span></span>

<span data-ttu-id="68b1c-212">提供一种在 web 应用外部添加用户的机制。</span><span class="sxs-lookup"><span data-stu-id="68b1c-212">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="68b1c-213">用于添加用户的选项包括：</span><span class="sxs-lookup"><span data-stu-id="68b1c-213">Options to add users include:</span></span>

* <span data-ttu-id="68b1c-214">专用的管理 web 应用。</span><span class="sxs-lookup"><span data-stu-id="68b1c-214">A dedicated admin web app.</span></span>
* <span data-ttu-id="68b1c-215">控制台应用。</span><span class="sxs-lookup"><span data-stu-id="68b1c-215">A console app.</span></span>

<span data-ttu-id="68b1c-216">下面的代码概述了一种添加用户的方法：</span><span class="sxs-lookup"><span data-stu-id="68b1c-216">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="68b1c-217">用户列表将读入内存中。</span><span class="sxs-lookup"><span data-stu-id="68b1c-217">A list of users is read into memory.</span></span>
* <span data-ttu-id="68b1c-218">为每个用户生成一个强唯一密码。</span><span class="sxs-lookup"><span data-stu-id="68b1c-218">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="68b1c-219">用户已添加到 Identity 数据库。</span><span class="sxs-lookup"><span data-stu-id="68b1c-219">The user is added to the Identity database.</span></span>
* <span data-ttu-id="68b1c-220">系统会通知用户并通知用户更改密码。</span><span class="sxs-lookup"><span data-stu-id="68b1c-220">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="68b1c-221">下面的代码概述了如何添加用户：</span><span class="sxs-lookup"><span data-stu-id="68b1c-221">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="68b1c-222">对于生产方案，可以遵循类似的方法。</span><span class="sxs-lookup"><span data-stu-id="68b1c-222">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="68b1c-223">禁止发布静态 Identity 资产</span><span class="sxs-lookup"><span data-stu-id="68b1c-223">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="68b1c-224">若要防止 Identity 将静态资产发布到 web 根目录，请参阅 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-224">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68b1c-225">其他资源</span><span class="sxs-lookup"><span data-stu-id="68b1c-225">Additional resources</span></span>

* [<span data-ttu-id="68b1c-226">更改为 ASP.NET Core 2.1 及更高版本的身份验证代码</span><span class="sxs-lookup"><span data-stu-id="68b1c-226">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="68b1c-227">ASP.NET Core 2.1 和更高版本提供了[ Identity ASP.NET Core](xref:security/authentication/identity)为[ Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="68b1c-227">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="68b1c-228">包含的应用程序 Identity 可以应用 scaffolder 来有选择地添加类库中包含的源代码 Identity Razor （RCL）。</span><span class="sxs-lookup"><span data-stu-id="68b1c-228">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="68b1c-229">建议生成源代码，以便修改代码和更改行为。</span><span class="sxs-lookup"><span data-stu-id="68b1c-229">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="68b1c-230">例如，可以指示基架生成在注册过程中使用的代码。</span><span class="sxs-lookup"><span data-stu-id="68b1c-230">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="68b1c-231">生成的代码优先于 Identity RCL 中的相同代码。</span><span class="sxs-lookup"><span data-stu-id="68b1c-231">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="68b1c-232">若要完全控制 UI，而不使用默认的 RCL，请参阅[创建完全标识 UI 源](#full)部分。</span><span class="sxs-lookup"><span data-stu-id="68b1c-232">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="68b1c-233">**不**包含身份验证的应用程序可以应用 scaffolder 来添加 RCL Identity 包。</span><span class="sxs-lookup"><span data-stu-id="68b1c-233">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="68b1c-234">可以选择要生成的 Identity 代码。</span><span class="sxs-lookup"><span data-stu-id="68b1c-234">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="68b1c-235">尽管 scaffolder 生成了大部分必要的代码，但你必须更新项目才能完成此过程。</span><span class="sxs-lookup"><span data-stu-id="68b1c-235">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="68b1c-236">本文档介绍完成基架更新所需的步骤 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-236">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="68b1c-237">当 Identity scaffolder 运行时，将在项目目录中创建一个*ScaffoldingReadme.txt*文件。</span><span class="sxs-lookup"><span data-stu-id="68b1c-237">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="68b1c-238">*ScaffoldingReadme.txt*文件包含有关完成基架更新所需内容的一般说明 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-238">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="68b1c-239">本文档包含与*ScaffoldingReadme.txt*文件更完整的说明。</span><span class="sxs-lookup"><span data-stu-id="68b1c-239">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="68b1c-240">建议使用显示文件差异的源代码管理系统，并使您能够回退更改。</span><span class="sxs-lookup"><span data-stu-id="68b1c-240">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="68b1c-241">运行 scaffolder 后检查更改 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-241">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="68b1c-242">使用[双重身份验证](xref:security/authentication/identity-enable-qrcodes)、[帐户确认和密码恢复](xref:security/authentication/accconfirm)和其他安全功能时，需要提供服务 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-242">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="68b1c-243">基架时不生成服务或服务存根 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-243">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="68b1c-244">要启用这些功能，必须手动添加服务。</span><span class="sxs-lookup"><span data-stu-id="68b1c-244">Services to enable these features must be added manually.</span></span> <span data-ttu-id="68b1c-245">例如，请参阅[需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。</span><span class="sxs-lookup"><span data-stu-id="68b1c-245">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-identity-into-an-empty-project"></a><span data-ttu-id="68b1c-246">基架 Identity 到空项目</span><span class="sxs-lookup"><span data-stu-id="68b1c-246">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="68b1c-247">将以下突出显示的调用添加到 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="68b1c-247">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-identity-into-a-razor-project-without-existing-authorization"></a><span data-ttu-id="68b1c-248">基架 Identity 到 Razor 无现有授权的项目</span><span class="sxs-lookup"><span data-stu-id="68b1c-248">Scaffold Identity into a Razor project without existing authorization</span></span>

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

Identity<span data-ttu-id="68b1c-249">在*区域/ Identity /IdentityHostingStartup.cs*中配置。</span><span class="sxs-lookup"><span data-stu-id="68b1c-249"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="68b1c-250">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="68b1c-250">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="68b1c-251">迁移、UseAuthentication 和布局</span><span class="sxs-lookup"><span data-stu-id="68b1c-251">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="68b1c-252">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="68b1c-252">Enable authentication</span></span>

<span data-ttu-id="68b1c-253">在 `Configure` 类的方法中 `Startup` ，在以下情况下调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) `UseStaticFiles` ：</span><span class="sxs-lookup"><span data-stu-id="68b1c-253">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="68b1c-254">布局更改</span><span class="sxs-lookup"><span data-stu-id="68b1c-254">Layout changes</span></span>

<span data-ttu-id="68b1c-255">可选：将登录名 partial （ `_LoginPartial` ）添加到布局文件中：</span><span class="sxs-lookup"><span data-stu-id="68b1c-255">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-html[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-identity-into-a-razor-project-with-authorization"></a><span data-ttu-id="68b1c-256">Identity Razor 使用授权基架到项目中</span><span class="sxs-lookup"><span data-stu-id="68b1c-256">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="68b1c-257">某些 Identity 选项在*区域/ Identity /IdentityHostingStartup.cs*中配置。</span><span class="sxs-lookup"><span data-stu-id="68b1c-257">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="68b1c-258">有关详细信息，请参阅[IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="68b1c-258">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-identity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="68b1c-259">基架 Identity 到没有现有授权的 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="68b1c-259">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="68b1c-260">可选：将登录名 partial （ `_LoginPartial` ）添加到*Views/Shared/_Layout cshtml*文件中：</span><span class="sxs-lookup"><span data-stu-id="68b1c-260">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-html[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="68b1c-261">将*Pages/shared/_LoginPartial cshtml*文件移动到*Views/shared/_LoginPartial。 cshtml*</span><span class="sxs-lookup"><span data-stu-id="68b1c-261">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

Identity<span data-ttu-id="68b1c-262">在*区域/ Identity /IdentityHostingStartup.cs*中配置。</span><span class="sxs-lookup"><span data-stu-id="68b1c-262"> is configured in *Areas/Identity/IdentityHostingStartup.cs*.</span></span> <span data-ttu-id="68b1c-263">有关详细信息，请参阅 IHostingStartup。</span><span class="sxs-lookup"><span data-stu-id="68b1c-263">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="68b1c-264">调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)之后 `UseStaticFiles` ：</span><span class="sxs-lookup"><span data-stu-id="68b1c-264">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-identity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="68b1c-265">Identity使用授权基架到 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="68b1c-265">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="68b1c-266">删除*页面/共享*文件夹和该文件夹中的文件。</span><span class="sxs-lookup"><span data-stu-id="68b1c-266">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-identity-ui-source"></a><span data-ttu-id="68b1c-267">创建完整的 Identity UI 源</span><span class="sxs-lookup"><span data-stu-id="68b1c-267">Create full Identity UI source</span></span>

<span data-ttu-id="68b1c-268">若要维护 UI 的完全控制 Identity ，请运行 Identity scaffolder 并选择 "**替代所有文件**"。</span><span class="sxs-lookup"><span data-stu-id="68b1c-268">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files**.</span></span>

<span data-ttu-id="68b1c-269">以下突出显示的代码显示了将默认 UI 替换为 Identity Identity ASP.NET Core 2.1 web 应用中的更改。</span><span class="sxs-lookup"><span data-stu-id="68b1c-269">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="68b1c-270">你可能希望执行此操作以对 UI 具有完全控制 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-270">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="68b1c-271">Identity在以下代码中，将替换默认值：</span><span class="sxs-lookup"><span data-stu-id="68b1c-271">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="68b1c-272">下面的代码将设置[LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和[AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：</span><span class="sxs-lookup"><span data-stu-id="68b1c-272">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="68b1c-273">注册 `IEmailSender` 实现，例如：</span><span class="sxs-lookup"><span data-stu-id="68b1c-273">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->
## <a name="disable-register-page"></a><span data-ttu-id="68b1c-274">禁用注册页</span><span class="sxs-lookup"><span data-stu-id="68b1c-274">Disable register page</span></span>

<span data-ttu-id="68b1c-275">禁用用户注册：</span><span class="sxs-lookup"><span data-stu-id="68b1c-275">To disable user registration:</span></span>

* <span data-ttu-id="68b1c-276">基架 Identity 。</span><span class="sxs-lookup"><span data-stu-id="68b1c-276">Scaffold Identity.</span></span> <span data-ttu-id="68b1c-277">包括帐户. Register、RegisterConfirmation。</span><span class="sxs-lookup"><span data-stu-id="68b1c-277">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="68b1c-278">例如：</span><span class="sxs-lookup"><span data-stu-id="68b1c-278">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="68b1c-279">更新*区域/ Identity /Pages/Account/Register.cshtml.cs* ，使用户无法从此终结点注册：</span><span class="sxs-lookup"><span data-stu-id="68b1c-279">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="68b1c-280">更新*区域/ Identity /Pages/Account/Register.cshtml* ，使其与前面的更改一致：</span><span class="sxs-lookup"><span data-stu-id="68b1c-280">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="68b1c-281">注释掉或删除*区域/ Identity /Pages/Account/Login.cshtml*中的注册链接</span><span class="sxs-lookup"><span data-stu-id="68b1c-281">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="68b1c-282">更新 "*区域/ Identity /Pages/Account/RegisterConfirmation* " 页。</span><span class="sxs-lookup"><span data-stu-id="68b1c-282">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="68b1c-283">删除来自 cshtml 文件的代码和链接。</span><span class="sxs-lookup"><span data-stu-id="68b1c-283">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="68b1c-284">从中删除确认代码 `PageModel` ：</span><span class="sxs-lookup"><span data-stu-id="68b1c-284">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="68b1c-285">使用其他应用添加用户</span><span class="sxs-lookup"><span data-stu-id="68b1c-285">Use another app to add users</span></span>

<span data-ttu-id="68b1c-286">提供一种在 web 应用外部添加用户的机制。</span><span class="sxs-lookup"><span data-stu-id="68b1c-286">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="68b1c-287">用于添加用户的选项包括：</span><span class="sxs-lookup"><span data-stu-id="68b1c-287">Options to add users include:</span></span>

* <span data-ttu-id="68b1c-288">专用的管理 web 应用。</span><span class="sxs-lookup"><span data-stu-id="68b1c-288">A dedicated admin web app.</span></span>
* <span data-ttu-id="68b1c-289">控制台应用。</span><span class="sxs-lookup"><span data-stu-id="68b1c-289">A console app.</span></span>

<span data-ttu-id="68b1c-290">下面的代码概述了一种添加用户的方法：</span><span class="sxs-lookup"><span data-stu-id="68b1c-290">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="68b1c-291">用户列表将读入内存中。</span><span class="sxs-lookup"><span data-stu-id="68b1c-291">A list of users is read into memory.</span></span>
* <span data-ttu-id="68b1c-292">为每个用户生成一个强唯一密码。</span><span class="sxs-lookup"><span data-stu-id="68b1c-292">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="68b1c-293">用户已添加到 Identity 数据库。</span><span class="sxs-lookup"><span data-stu-id="68b1c-293">The user is added to the Identity database.</span></span>
* <span data-ttu-id="68b1c-294">系统会通知用户并通知用户更改密码。</span><span class="sxs-lookup"><span data-stu-id="68b1c-294">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="68b1c-295">下面的代码概述了如何添加用户：</span><span class="sxs-lookup"><span data-stu-id="68b1c-295">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="68b1c-296">对于生产方案，可以遵循类似的方法。</span><span class="sxs-lookup"><span data-stu-id="68b1c-296">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68b1c-297">其他资源</span><span class="sxs-lookup"><span data-stu-id="68b1c-297">Additional resources</span></span>

* [<span data-ttu-id="68b1c-298">更改为 ASP.NET Core 2.1 及更高版本的身份验证代码</span><span class="sxs-lookup"><span data-stu-id="68b1c-298">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
