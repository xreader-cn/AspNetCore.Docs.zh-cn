---
title: IdentityASP.NET Core 项目中的基架
author: rick-anderson
description: '了解如何 Identity 在 ASP.NET Core 项目中进行基架。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/scaffold-identity
ms.openlocfilehash: 813dd7837c265c78c584d66dd51bc23399d12fbe
ms.sourcegitcommit: 5156eab2118584405eb663e1fcd82f8bd7764504
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/31/2020
ms.locfileid: "93141490"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="4e963-103">IdentityASP.NET Core 项目中的基架</span><span class="sxs-lookup"><span data-stu-id="4e963-103">Scaffold Identity in ASP.NET Core projects</span></span>

<span data-ttu-id="4e963-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4e963-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4e963-105">ASP.NET Core 提供 [ASP.NET Core Identity](xref:security/authentication/identity) 为类库[ Razor ](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="4e963-105">ASP.NET Core provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="4e963-106">包含的应用程序 Identity 可以应用 scaffolder 将类库中包含的源代码选择性地添加 Identity Razor (RCL) 。</span><span class="sxs-lookup"><span data-stu-id="4e963-106">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="4e963-107">建议生成源代码，以便修改代码和更改行为。</span><span class="sxs-lookup"><span data-stu-id="4e963-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="4e963-108">例如，可以指示基架生成在注册过程中使用的代码。</span><span class="sxs-lookup"><span data-stu-id="4e963-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="4e963-109">生成的代码优先于 Identity RCL 中的相同代码。</span><span class="sxs-lookup"><span data-stu-id="4e963-109">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="4e963-110">若要完全控制 UI，而不使用默认的 RCL，请参阅 [创建完整 Identity UI 源](#full)部分。</span><span class="sxs-lookup"><span data-stu-id="4e963-110">To gain full control of the UI and not use the default RCL, see the section [Create full Identity UI source](#full).</span></span>

<span data-ttu-id="4e963-111">**不** 包含身份验证的应用程序可以应用 scaffolder 来添加 RCL Identity 包。</span><span class="sxs-lookup"><span data-stu-id="4e963-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="4e963-112">可以选择要生成的 Identity 代码。</span><span class="sxs-lookup"><span data-stu-id="4e963-112">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="4e963-113">尽管 scaffolder 生成了大部分必要的代码，但你需要更新项目以完成该过程。</span><span class="sxs-lookup"><span data-stu-id="4e963-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="4e963-114">本文档介绍完成基架更新所需的步骤 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-114">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="4e963-115">建议使用显示文件差异的源代码管理系统，并使您能够回退更改。</span><span class="sxs-lookup"><span data-stu-id="4e963-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="4e963-116">运行 scaffolder 后检查更改 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-116">Inspect the changes after running the Identity scaffolder.</span></span>

<span data-ttu-id="4e963-117">使用 [双重身份验证](xref:security/authentication/identity-enable-qrcodes)、 [帐户确认和密码恢复](xref:security/authentication/accconfirm)和其他安全功能时，需要提供服务 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="4e963-118">基架时不生成服务或服务存根 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-118">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="4e963-119">要启用这些功能，必须手动添加服务。</span><span class="sxs-lookup"><span data-stu-id="4e963-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="4e963-120">例如，请参阅 [需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。</span><span class="sxs-lookup"><span data-stu-id="4e963-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="4e963-121">当 Identity 使用现有个人帐户将具有新数据上下文的基架插入到项目中时：</span><span class="sxs-lookup"><span data-stu-id="4e963-121">When scaffolding Identity with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="4e963-122">在中 `Startup.ConfigureServices` ，删除对的调用：</span><span class="sxs-lookup"><span data-stu-id="4e963-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefaultIdentity`

<span data-ttu-id="4e963-123">例如， `AddDbContext` `AddDefaultIdentity` 在以下代码中注释掉和：</span><span class="sxs-lookup"><span data-stu-id="4e963-123">For example, `AddDbContext` and `AddDefaultIdentity` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="4e963-124">前面的代码注释了 *区域/ Identity / Identity HostingStartup.cs* 中的重复代码。</span><span class="sxs-lookup"><span data-stu-id="4e963-124">The preceeding code comments out the code that is duplicated in *Areas/Identity/IdentityHostingStartup.cs*</span></span>

<span data-ttu-id="4e963-125">通常，使用个人帐户创建的应用应 \* **not** _ 创建新的数据上下文。</span><span class="sxs-lookup"><span data-stu-id="4e963-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="4e963-126">基架 Identity 到空项目</span><span class="sxs-lookup"><span data-stu-id="4e963-126">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="4e963-127">`Startup`用类似于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="4e963-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="4e963-128">基架 Identity 到 Razor 无现有授权的项目</span><span class="sxs-lookup"><span data-stu-id="4e963-128">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="4e963-129">Identity在 _Areas/HostingStartup.cs \* 中进行配置 Identity / Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-129">Identity is configured in _Areas/Identity/IdentityHostingStartup.cs\*.</span></span> <span data-ttu-id="4e963-130">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="4e963-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="4e963-131">迁移、UseAuthentication 和布局</span><span class="sxs-lookup"><span data-stu-id="4e963-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="4e963-132">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="4e963-132">Enable authentication</span></span>

<span data-ttu-id="4e963-133">`Startup`用类似于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="4e963-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="4e963-134">布局更改</span><span class="sxs-lookup"><span data-stu-id="4e963-134">Layout changes</span></span>

<span data-ttu-id="4e963-135">可选：将登录部分 (`_LoginPartial`) 添加到布局文件中：</span><span class="sxs-lookup"><span data-stu-id="4e963-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="4e963-136">Identity Razor 使用授权基架到项目中</span><span class="sxs-lookup"><span data-stu-id="4e963-136">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="4e963-137">某些 Identity 选项在 *区域/ Identity / Identity HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="4e963-137">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="4e963-138">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="4e963-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="4e963-139">基架 Identity 到没有现有授权的 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="4e963-139">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="4e963-140">可选：将登录名部分 (`_LoginPartial`) 添加到 *Views/Shared/_Layout* 文件中：</span><span class="sxs-lookup"><span data-stu-id="4e963-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="4e963-141">将 *Pages/shared/_LoginPartial cshtml* 文件移动到 *Views/shared/_LoginPartial。 cshtml*</span><span class="sxs-lookup"><span data-stu-id="4e963-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="4e963-142">Identity在 *区域/ Identity / Identity HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="4e963-142">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="4e963-143">有关详细信息，请参阅 IHostingStartup。</span><span class="sxs-lookup"><span data-stu-id="4e963-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="4e963-144">`Startup`用类似于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="4e963-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="4e963-145">Identity使用授权基架到 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="4e963-145">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="4e963-146">基架 Identity 到 Blazor Server 无现有授权的项目</span><span class="sxs-lookup"><span data-stu-id="4e963-146">Scaffold Identity into a Blazor Server project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="4e963-147">Identity在 *区域/ Identity / Identity HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="4e963-147">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="4e963-148">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="4e963-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="4e963-149">迁移</span><span class="sxs-lookup"><span data-stu-id="4e963-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="4e963-150">将 XSRF 令牌传递给应用</span><span class="sxs-lookup"><span data-stu-id="4e963-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="4e963-151">可以将令牌传递给组件：</span><span class="sxs-lookup"><span data-stu-id="4e963-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="4e963-152">设置身份验证令牌并将其保存到身份验证后 cookie ，可以将其传递给组件。</span><span class="sxs-lookup"><span data-stu-id="4e963-152">When authentication tokens are provisioned and saved to the authentication cookie, they can be passed to components.</span></span>
* <span data-ttu-id="4e963-153">Razor 组件不能 `HttpContext` 直接使用，因此无法获取 [ (XSRF 的) 令牌的反请求伪造](xref:security/anti-request-forgery) Identity `/Identity/Account/Logout` 。</span><span class="sxs-lookup"><span data-stu-id="4e963-153">Razor components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to Identity's logout endpoint at `/Identity/Account/Logout`.</span></span> <span data-ttu-id="4e963-154">可以将 XSRF 令牌传递给组件。</span><span class="sxs-lookup"><span data-stu-id="4e963-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="4e963-155">有关详细信息，请参阅 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>。</span><span class="sxs-lookup"><span data-stu-id="4e963-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="4e963-156">在 *Pages/_Host cshtml* 文件中，在将其添加到和类后建立该令牌 `InitialApplicationState` `TokenProvider` ：</span><span class="sxs-lookup"><span data-stu-id="4e963-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="4e963-157">`App`将组件更新 ( *App.razor* ) ，以分配 `InitialState.XsrfToken` ：</span><span class="sxs-lookup"><span data-stu-id="4e963-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="4e963-158">本 `TokenProvider` 主题中演示的服务在 `LoginDisplay` 以下 [布局和身份验证流更改](#layout-and-authentication-flow-changes) 部分的组件中使用。</span><span class="sxs-lookup"><span data-stu-id="4e963-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="4e963-159">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="4e963-159">Enable authentication</span></span>

<span data-ttu-id="4e963-160">在 `Startup` 类中：</span><span class="sxs-lookup"><span data-stu-id="4e963-160">In the `Startup` class:</span></span>

* <span data-ttu-id="4e963-161">确认 Razor 在中添加了页面服务 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="4e963-161">Confirm that Razor Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="4e963-162">如果使用 [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)，请注册服务。</span><span class="sxs-lookup"><span data-stu-id="4e963-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="4e963-163">`UseDatabaseErrorPage`对于开发环境，请在中的应用程序生成器上调用 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="4e963-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="4e963-164">调用 `UseAuthentication` and `UseAuthorization` after `UseRouting` 。</span><span class="sxs-lookup"><span data-stu-id="4e963-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="4e963-165">添加页的终结点 Razor 。</span><span class="sxs-lookup"><span data-stu-id="4e963-165">Add an endpoint for Razor Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupBlazor.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="4e963-166">布局和身份验证流更改</span><span class="sxs-lookup"><span data-stu-id="4e963-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="4e963-167">将 `RedirectToLogin` *RedirectToLogin* ) 的 (组件添加到项目根目录中应用的 *共享* 文件夹中：</span><span class="sxs-lookup"><span data-stu-id="4e963-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

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

将 `LoginDisplay` () 的 *LoginDisplay.razor* 组件添加到应用的 *共享* 文件夹。 <span data-ttu-id="4e963-169">[TokenProvider 服务](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)提供 HTML 窗体的 XSRF 标记，该标记将发布到 Identity 注销终结点：</span><span class="sxs-lookup"><span data-stu-id="4e963-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to Identity's logout endpoint:</span></span>

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

<span data-ttu-id="4e963-170">在 `MainLayout` ( *Shared/MainLayout* ) 的组件中，将组件添加 `LoginDisplay` 到顶层行 `<div>` 元素的内容：</span><span class="sxs-lookup"><span data-stu-id="4e963-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="4e963-171">样式身份验证终结点</span><span class="sxs-lookup"><span data-stu-id="4e963-171">Style authentication endpoints</span></span>

<span data-ttu-id="4e963-172">由于 Blazor Server 使用 Razor 页面 Identity 页，当访问者在页面和组件间导航时，UI 的样式会发生变化 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-172">Because Blazor Server uses Razor Pages Identity pages, the styling of the UI changes when a visitor navigates between Identity pages and components.</span></span> <span data-ttu-id="4e963-173">可以使用两个选项来处理 incongruous 样式：</span><span class="sxs-lookup"><span data-stu-id="4e963-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="4e963-174">生成 Identity 组件</span><span class="sxs-lookup"><span data-stu-id="4e963-174">Build Identity components</span></span>

<span data-ttu-id="4e963-175">使用而不是页的组件的方法 Identity 是生成 Identity 组件。</span><span class="sxs-lookup"><span data-stu-id="4e963-175">An approach to using components for Identity instead of pages is to build Identity components.</span></span> <span data-ttu-id="4e963-176">由于 `SignInManager` `UserManager` 在组件中不支持和 Razor ，因此在应用程序中使用 API 终结点 Blazor Server 来处理用户帐户操作。</span><span class="sxs-lookup"><span data-stu-id="4e963-176">Because `SignInManager` and `UserManager` aren't supported in Razor components, use API endpoints in the Blazor Server app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="4e963-177">在应用程序样式中使用自定义布局 Blazor</span><span class="sxs-lookup"><span data-stu-id="4e963-177">Use a custom layout with Blazor app styles</span></span>

<span data-ttu-id="4e963-178">Identity可以修改页面布局和样式，以生成使用默认主题的页面 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="4e963-178">The Identity pages layout and styles can be modified to produce pages that use the default Blazor theme.</span></span>

> [!NOTE]
> <span data-ttu-id="4e963-179">本节中的示例只是一个自定义的起点。</span><span class="sxs-lookup"><span data-stu-id="4e963-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="4e963-180">为了获得最佳用户体验，可能需要额外的工作。</span><span class="sxs-lookup"><span data-stu-id="4e963-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="4e963-181">`NavMenu_IdentityLayout` ( *Shared/NavMenu_ Identity Layout* ) 创建新的组件。</span><span class="sxs-lookup"><span data-stu-id="4e963-181">Create a new `NavMenu_IdentityLayout` component ( *Shared/NavMenu_IdentityLayout.razor* ).</span></span> <span data-ttu-id="4e963-182">对于组件的标记和代码，请 `NavMenu` ( *Shared/NavMenu* ) 使用应用组件的相同内容。</span><span class="sxs-lookup"><span data-stu-id="4e963-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="4e963-183">去除 `NavLink` 无法匿名访问的组件，因为组件中的自动重定向 `RedirectToLogin` 对于需要身份验证或授权的组件失败。</span><span class="sxs-lookup"><span data-stu-id="4e963-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="4e963-184">在 *Pages/Shared/Layout* 文件中，进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="4e963-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="4e963-185">将 Razor 指令添加到文件顶部，以使用 *共享* 文件夹中的标记帮助程序和应用组件：</span><span class="sxs-lookup"><span data-stu-id="4e963-185">Add Razor directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="4e963-186">替换 `{APPLICATION ASSEMBLY}` 为应用程序的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="4e963-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="4e963-187">向 `<base>` 内容添加标记和 Blazor 样式 `<link>` 表 `<head>` ：</span><span class="sxs-lookup"><span data-stu-id="4e963-187">Add a `<base>` tag and Blazor stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="4e963-188">将标记的内容更改 `<body>` 为以下内容：</span><span class="sxs-lookup"><span data-stu-id="4e963-188">Change the content of the `<body>` tag to the following:</span></span>

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

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="4e963-189">Identity Blazor Server 使用授权基架到项目中</span><span class="sxs-lookup"><span data-stu-id="4e963-189">Scaffold Identity into a Blazor Server project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="4e963-190">某些 Identity 选项在 *区域/ Identity / Identity HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="4e963-190">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="4e963-191">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="4e963-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="standalone-or-hosted-no-locblazor-webassembly-apps"></a><span data-ttu-id="4e963-192">独立应用或托管 Blazor WebAssembly 应用</span><span class="sxs-lookup"><span data-stu-id="4e963-192">Standalone or hosted Blazor WebAssembly apps</span></span>

<span data-ttu-id="4e963-193">客户端 Blazor WebAssembly 应用使用其自己的 Identity UI 方法，不能使用 ASP.NET Core Identity 基架。</span><span class="sxs-lookup"><span data-stu-id="4e963-193">Client-side Blazor WebAssembly apps use their own Identity UI approaches and can't use ASP.NET Core Identity scaffolding.</span></span> <span data-ttu-id="4e963-194">托管解决方案的服务器端 ASP.NET Core 应用 Blazor 可以遵循 Razor 本文中的页面/MVC 指南，并配置为与支持的任何其他类型的 ASP.NET Core 应用一样 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-194">Server-side ASP.NET Core apps of hosted Blazor solutions can follow the Razor Pages/MVC guidance in this article and are configured just like any other type of ASP.NET Core app that supports Identity.</span></span>

<span data-ttu-id="4e963-195">此 Blazor 框架不包括 Razor UI 页面的组件版本 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-195">The Blazor framework doesn't include Razor component versions of Identity UI pages.</span></span> <span data-ttu-id="4e963-196">Identity UI Razor 组件可以是自定义生成的，也可以从不受支持的第三方源获取。</span><span class="sxs-lookup"><span data-stu-id="4e963-196">Identity UI Razor components can be custom built or obtained from unsupported third-party sources.</span></span>

<span data-ttu-id="4e963-197">有关详细信息，请参阅[ Blazor 安全性和 Identity 文章](xref:blazor/security/index)。</span><span class="sxs-lookup"><span data-stu-id="4e963-197">For more information, see the [Blazor Security and Identity articles](xref:blazor/security/index).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="4e963-198">创建完整的 Identity UI 源</span><span class="sxs-lookup"><span data-stu-id="4e963-198">Create full Identity UI source</span></span>

<span data-ttu-id="4e963-199">若要维护 UI 的完全控制 Identity ，请运行 Identity scaffolder 并选择 " **替代所有文件** "。</span><span class="sxs-lookup"><span data-stu-id="4e963-199">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="4e963-200">以下突出显示的代码显示了将默认 UI 替换为 Identity Identity ASP.NET Core 2.1 web 应用中的更改。</span><span class="sxs-lookup"><span data-stu-id="4e963-200">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="4e963-201">你可能希望执行此操作以对 UI 具有完全控制 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-201">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="4e963-202">Identity在以下代码中，将替换默认值：</span><span class="sxs-lookup"><span data-stu-id="4e963-202">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="4e963-203">下面的代码将设置 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：</span><span class="sxs-lookup"><span data-stu-id="4e963-203">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="4e963-204">注册 `IEmailSender` 实现，例如：</span><span class="sxs-lookup"><span data-stu-id="4e963-204">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="4e963-205">密码配置</span><span class="sxs-lookup"><span data-stu-id="4e963-205">Password configuration</span></span>

<span data-ttu-id="4e963-206">如果 <xref:Microsoft.AspNetCore.Identity.PasswordOptions> 在中配置了 `Startup.ConfigureServices` ，则基架页中的属性可能需要[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)配置 `Password` Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-206">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="4e963-207">`InputModel``Password`在以下文件中可以找到属性：</span><span class="sxs-lookup"><span data-stu-id="4e963-207">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="4e963-208">禁用页面</span><span class="sxs-lookup"><span data-stu-id="4e963-208">Disable a page</span></span>

<span data-ttu-id="4e963-209">本部分介绍如何禁用注册页面，但该方法可用于禁用任何页面。</span><span class="sxs-lookup"><span data-stu-id="4e963-209">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="4e963-210">禁用用户注册：</span><span class="sxs-lookup"><span data-stu-id="4e963-210">To disable user registration:</span></span>

* <span data-ttu-id="4e963-211">基架 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-211">Scaffold Identity.</span></span> <span data-ttu-id="4e963-212">包括帐户. Register、RegisterConfirmation。</span><span class="sxs-lookup"><span data-stu-id="4e963-212">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="4e963-213">例如：</span><span class="sxs-lookup"><span data-stu-id="4e963-213">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="4e963-214">更新 *区域/ Identity /Pages/Account/Register.cshtml.cs* ，使用户无法从此终结点注册：</span><span class="sxs-lookup"><span data-stu-id="4e963-214">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="4e963-215">更新 *区域/ Identity /Pages/Account/Register.cshtml* ，使其与前面的更改一致：</span><span class="sxs-lookup"><span data-stu-id="4e963-215">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="4e963-216">注释掉或删除 *区域/ Identity /Pages/Account/Login.cshtml* 中的注册链接</span><span class="sxs-lookup"><span data-stu-id="4e963-216">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="4e963-217">更新 " *区域/ Identity /Pages/Account/RegisterConfirmation* " 页。</span><span class="sxs-lookup"><span data-stu-id="4e963-217">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="4e963-218">删除来自 cshtml 文件的代码和链接。</span><span class="sxs-lookup"><span data-stu-id="4e963-218">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="4e963-219">从中删除确认代码 `PageModel` ：</span><span class="sxs-lookup"><span data-stu-id="4e963-219">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="4e963-220">使用其他应用添加用户</span><span class="sxs-lookup"><span data-stu-id="4e963-220">Use another app to add users</span></span>

<span data-ttu-id="4e963-221">提供一种在 web 应用外部添加用户的机制。</span><span class="sxs-lookup"><span data-stu-id="4e963-221">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="4e963-222">用于添加用户的选项包括：</span><span class="sxs-lookup"><span data-stu-id="4e963-222">Options to add users include:</span></span>

* <span data-ttu-id="4e963-223">专用的管理 web 应用。</span><span class="sxs-lookup"><span data-stu-id="4e963-223">A dedicated admin web app.</span></span>
* <span data-ttu-id="4e963-224">控制台应用。</span><span class="sxs-lookup"><span data-stu-id="4e963-224">A console app.</span></span>

<span data-ttu-id="4e963-225">下面的代码概述了一种添加用户的方法：</span><span class="sxs-lookup"><span data-stu-id="4e963-225">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="4e963-226">用户列表将读入内存中。</span><span class="sxs-lookup"><span data-stu-id="4e963-226">A list of users is read into memory.</span></span>
* <span data-ttu-id="4e963-227">为每个用户生成一个强唯一密码。</span><span class="sxs-lookup"><span data-stu-id="4e963-227">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="4e963-228">用户已添加到 Identity 数据库。</span><span class="sxs-lookup"><span data-stu-id="4e963-228">The user is added to the Identity database.</span></span>
* <span data-ttu-id="4e963-229">系统会通知用户并通知用户更改密码。</span><span class="sxs-lookup"><span data-stu-id="4e963-229">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="4e963-230">下面的代码概述了如何添加用户：</span><span class="sxs-lookup"><span data-stu-id="4e963-230">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="4e963-231">对于生产方案，可以遵循类似的方法。</span><span class="sxs-lookup"><span data-stu-id="4e963-231">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="4e963-232">禁止发布静态 Identity 资产</span><span class="sxs-lookup"><span data-stu-id="4e963-232">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="4e963-233">若要防止 Identity 将静态资产发布到 web 根目录，请参阅 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> 。</span><span class="sxs-lookup"><span data-stu-id="4e963-233">To prevent publishing static Identity assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e963-234">其他资源</span><span class="sxs-lookup"><span data-stu-id="4e963-234">Additional resources</span></span>

* [<span data-ttu-id="4e963-235">更改为 ASP.NET Core 2.1 及更高版本的身份验证代码</span><span class="sxs-lookup"><span data-stu-id="4e963-235">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e963-236">ASP.NET Core 2.1 和更高版本提供 [ASP.NET Core Identity](xref:security/authentication/identity) 为[ Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="4e963-236">ASP.NET Core 2.1 and later provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="4e963-237">包含的应用程序 Identity 可以应用 scaffolder 将类库中包含的源代码选择性地添加 Identity Razor (RCL) 。</span><span class="sxs-lookup"><span data-stu-id="4e963-237">Applications that include Identity can apply the scaffolder to selectively add the source code contained in the Identity Razor Class Library (RCL).</span></span> <span data-ttu-id="4e963-238">建议生成源代码，以便修改代码和更改行为。</span><span class="sxs-lookup"><span data-stu-id="4e963-238">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="4e963-239">例如，可以指示基架生成在注册过程中使用的代码。</span><span class="sxs-lookup"><span data-stu-id="4e963-239">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="4e963-240">生成的代码优先于 Identity RCL 中的相同代码。</span><span class="sxs-lookup"><span data-stu-id="4e963-240">Generated code takes precedence over the same code in the Identity RCL.</span></span> <span data-ttu-id="4e963-241">若要完全控制 UI，而不使用默认的 RCL，请参阅 [创建完全标识 UI 源](#full)部分。</span><span class="sxs-lookup"><span data-stu-id="4e963-241">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="4e963-242">**不** 包含身份验证的应用程序可以应用 scaffolder 来添加 RCL Identity 包。</span><span class="sxs-lookup"><span data-stu-id="4e963-242">Applications that do **not** include authentication can apply the scaffolder to add the RCL Identity package.</span></span> <span data-ttu-id="4e963-243">可以选择要生成的 Identity 代码。</span><span class="sxs-lookup"><span data-stu-id="4e963-243">You have the option of selecting Identity code to be generated.</span></span>

<span data-ttu-id="4e963-244">尽管 scaffolder 生成了大部分必要的代码，但你必须更新项目才能完成此过程。</span><span class="sxs-lookup"><span data-stu-id="4e963-244">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="4e963-245">本文档介绍完成基架更新所需的步骤 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-245">This document explains the steps needed to complete an Identity scaffolding update.</span></span>

<span data-ttu-id="4e963-246">当 Identity scaffolder 运行时，将在项目目录中创建一个 *ScaffoldingReadme.txt* 文件。</span><span class="sxs-lookup"><span data-stu-id="4e963-246">When the Identity scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="4e963-247">*ScaffoldingReadme.txt* 文件包含有关完成基架更新所需内容的一般说明 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-247">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the Identity scaffolding update.</span></span> <span data-ttu-id="4e963-248">本文档包含与 *ScaffoldingReadme.txt* 文件更完整的说明。</span><span class="sxs-lookup"><span data-stu-id="4e963-248">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="4e963-249">建议使用显示文件差异的源代码管理系统，并使您能够回退更改。</span><span class="sxs-lookup"><span data-stu-id="4e963-249">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="4e963-250">运行 scaffolder 后检查更改 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-250">Inspect the changes after running the Identity scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="4e963-251">使用 [双重身份验证](xref:security/authentication/identity-enable-qrcodes)、 [帐户确认和密码恢复](xref:security/authentication/accconfirm)和其他安全功能时，需要提供服务 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-251">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with Identity.</span></span> <span data-ttu-id="4e963-252">基架时不生成服务或服务存根 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-252">Services or service stubs aren't generated when scaffolding Identity.</span></span> <span data-ttu-id="4e963-253">要启用这些功能，必须手动添加服务。</span><span class="sxs-lookup"><span data-stu-id="4e963-253">Services to enable these features must be added manually.</span></span> <span data-ttu-id="4e963-254">例如，请参阅 [需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。</span><span class="sxs-lookup"><span data-stu-id="4e963-254">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="4e963-255">基架 Identity 到空项目</span><span class="sxs-lookup"><span data-stu-id="4e963-255">Scaffold Identity into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="4e963-256">将以下突出显示的调用添加到 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="4e963-256">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="4e963-257">基架 Identity 到 Razor 无现有授权的项目</span><span class="sxs-lookup"><span data-stu-id="4e963-257">Scaffold Identity into a Razor project without existing authorization</span></span>

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

<span data-ttu-id="4e963-258">Identity在 *区域/ Identity / Identity HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="4e963-258">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="4e963-259">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="4e963-259">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="4e963-260">迁移、UseAuthentication 和布局</span><span class="sxs-lookup"><span data-stu-id="4e963-260">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="4e963-261">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="4e963-261">Enable authentication</span></span>

<span data-ttu-id="4e963-262">在 `Configure` 类的方法中 `Startup` ，在以下情况下调用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> `UseStaticFiles` ：</span><span class="sxs-lookup"><span data-stu-id="4e963-262">In the `Configure` method of the `Startup` class, call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="4e963-263">布局更改</span><span class="sxs-lookup"><span data-stu-id="4e963-263">Layout changes</span></span>

<span data-ttu-id="4e963-264">可选：将登录部分 (`_LoginPartial`) 添加到布局文件中：</span><span class="sxs-lookup"><span data-stu-id="4e963-264">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="4e963-265">Identity Razor 使用授权基架到项目中</span><span class="sxs-lookup"><span data-stu-id="4e963-265">Scaffold Identity into a Razor project with authorization</span></span>

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

<span data-ttu-id="4e963-266">某些 Identity 选项在 *区域/ Identity / Identity HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="4e963-266">Some Identity options are configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="4e963-267">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="4e963-267">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="4e963-268">基架 Identity 到没有现有授权的 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="4e963-268">Scaffold Identity into an MVC project without existing authorization</span></span>

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

<span data-ttu-id="4e963-269">可选：将登录名部分 (`_LoginPartial`) 添加到 *Views/Shared/_Layout* 文件中：</span><span class="sxs-lookup"><span data-stu-id="4e963-269">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="4e963-270">将 *Pages/shared/_LoginPartial cshtml* 文件移动到 *Views/shared/_LoginPartial。 cshtml*</span><span class="sxs-lookup"><span data-stu-id="4e963-270">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="4e963-271">Identity在 *区域/ Identity / Identity HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="4e963-271">Identity is configured in *Areas/Identity/IdentityHostingStartup.cs* .</span></span> <span data-ttu-id="4e963-272">有关详细信息，请参阅 IHostingStartup。</span><span class="sxs-lookup"><span data-stu-id="4e963-272">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="4e963-273">调用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> 后 `UseStaticFiles` ：</span><span class="sxs-lookup"><span data-stu-id="4e963-273">Call <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A> after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="4e963-274">Identity使用授权基架到 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="4e963-274">Scaffold Identity into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="4e963-275">删除 *页面/共享* 文件夹和该文件夹中的文件。</span><span class="sxs-lookup"><span data-stu-id="4e963-275">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="4e963-276">创建完整的 Identity UI 源</span><span class="sxs-lookup"><span data-stu-id="4e963-276">Create full Identity UI source</span></span>

<span data-ttu-id="4e963-277">若要维护 UI 的完全控制 Identity ，请运行 Identity scaffolder 并选择 " **替代所有文件** "。</span><span class="sxs-lookup"><span data-stu-id="4e963-277">To maintain full control of the Identity UI, run the Identity scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="4e963-278">以下突出显示的代码显示了将默认 UI 替换为 Identity Identity ASP.NET Core 2.1 web 应用中的更改。</span><span class="sxs-lookup"><span data-stu-id="4e963-278">The following highlighted code shows the changes to replace the default Identity UI with Identity in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="4e963-279">你可能希望执行此操作以对 UI 具有完全控制 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-279">You might want to do this to have full control of the Identity UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="4e963-280">Identity在以下代码中，将替换默认值：</span><span class="sxs-lookup"><span data-stu-id="4e963-280">The default Identity is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="4e963-281">下面的代码将设置 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath)和 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath)：</span><span class="sxs-lookup"><span data-stu-id="4e963-281">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.cookies.cookieauthenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="4e963-282">注册 `IEmailSender` 实现，例如：</span><span class="sxs-lookup"><span data-stu-id="4e963-282">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="4e963-283">密码配置</span><span class="sxs-lookup"><span data-stu-id="4e963-283">Password configuration</span></span>

<span data-ttu-id="4e963-284">如果 <xref:Microsoft.AspNetCore.Identity.PasswordOptions> 在中配置了 `Startup.ConfigureServices` ，则基架页中的属性可能需要[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)配置 `Password` Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-284">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="4e963-285">`InputModel``Password`在以下文件中可以找到属性：</span><span class="sxs-lookup"><span data-stu-id="4e963-285">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/Identity/Pages/Account/Register.cshtml.cs`
* `Areas/Identity/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="4e963-286">禁用注册页</span><span class="sxs-lookup"><span data-stu-id="4e963-286">Disable register page</span></span>

<span data-ttu-id="4e963-287">禁用用户注册：</span><span class="sxs-lookup"><span data-stu-id="4e963-287">To disable user registration:</span></span>

* <span data-ttu-id="4e963-288">基架 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4e963-288">Scaffold Identity.</span></span> <span data-ttu-id="4e963-289">包括帐户. Register、RegisterConfirmation。</span><span class="sxs-lookup"><span data-stu-id="4e963-289">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="4e963-290">例如：</span><span class="sxs-lookup"><span data-stu-id="4e963-290">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="4e963-291">更新 *区域/ Identity /Pages/Account/Register.cshtml.cs* ，使用户无法从此终结点注册：</span><span class="sxs-lookup"><span data-stu-id="4e963-291">Update *Areas/Identity/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="4e963-292">更新 *区域/ Identity /Pages/Account/Register.cshtml* ，使其与前面的更改一致：</span><span class="sxs-lookup"><span data-stu-id="4e963-292">Update *Areas/Identity/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="4e963-293">注释掉或删除 *区域/ Identity /Pages/Account/Login.cshtml* 中的注册链接</span><span class="sxs-lookup"><span data-stu-id="4e963-293">Comment out or remove the registration link from *Areas/Identity/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="4e963-294">更新 " *区域/ Identity /Pages/Account/RegisterConfirmation* " 页。</span><span class="sxs-lookup"><span data-stu-id="4e963-294">Update the *Areas/Identity/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="4e963-295">删除来自 cshtml 文件的代码和链接。</span><span class="sxs-lookup"><span data-stu-id="4e963-295">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="4e963-296">从中删除确认代码 `PageModel` ：</span><span class="sxs-lookup"><span data-stu-id="4e963-296">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="4e963-297">使用其他应用添加用户</span><span class="sxs-lookup"><span data-stu-id="4e963-297">Use another app to add users</span></span>

<span data-ttu-id="4e963-298">提供一种在 web 应用外部添加用户的机制。</span><span class="sxs-lookup"><span data-stu-id="4e963-298">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="4e963-299">用于添加用户的选项包括：</span><span class="sxs-lookup"><span data-stu-id="4e963-299">Options to add users include:</span></span>

* <span data-ttu-id="4e963-300">专用的管理 web 应用。</span><span class="sxs-lookup"><span data-stu-id="4e963-300">A dedicated admin web app.</span></span>
* <span data-ttu-id="4e963-301">控制台应用。</span><span class="sxs-lookup"><span data-stu-id="4e963-301">A console app.</span></span>

<span data-ttu-id="4e963-302">下面的代码概述了一种添加用户的方法：</span><span class="sxs-lookup"><span data-stu-id="4e963-302">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="4e963-303">用户列表将读入内存中。</span><span class="sxs-lookup"><span data-stu-id="4e963-303">A list of users is read into memory.</span></span>
* <span data-ttu-id="4e963-304">为每个用户生成一个强唯一密码。</span><span class="sxs-lookup"><span data-stu-id="4e963-304">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="4e963-305">用户已添加到 Identity 数据库。</span><span class="sxs-lookup"><span data-stu-id="4e963-305">The user is added to the Identity database.</span></span>
* <span data-ttu-id="4e963-306">系统会通知用户并通知用户更改密码。</span><span class="sxs-lookup"><span data-stu-id="4e963-306">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="4e963-307">下面的代码概述了如何添加用户：</span><span class="sxs-lookup"><span data-stu-id="4e963-307">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="4e963-308">对于生产方案，可以遵循类似的方法。</span><span class="sxs-lookup"><span data-stu-id="4e963-308">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4e963-309">其他资源</span><span class="sxs-lookup"><span data-stu-id="4e963-309">Additional resources</span></span>

* [<span data-ttu-id="4e963-310">更改为 ASP.NET Core 2.1 及更高版本的身份验证代码</span><span class="sxs-lookup"><span data-stu-id="4e963-310">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
