---
title: :::no-loc(Identity):::ASP.NET Core 项目中的基架
author: rick-anderson
description: '了解如何 :::no-loc(Identity)::: 在 ASP.NET Core 项目中进行基架。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/1/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/scaffold-identity
ms.openlocfilehash: c79dfc64d4311088c3f9ea03aad7570189000e2a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053314"
---
# <a name="scaffold-no-locidentity-in-aspnet-core-projects"></a><span data-ttu-id="31f9f-103">:::no-loc(Identity):::ASP.NET Core 项目中的基架</span><span class="sxs-lookup"><span data-stu-id="31f9f-103">Scaffold :::no-loc(Identity)::: in ASP.NET Core projects</span></span>

<span data-ttu-id="31f9f-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="31f9f-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="31f9f-105">ASP.NET Core 提供 [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) 为类库[ :::no-loc(Razor)::: ](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="31f9f-105">ASP.NET Core provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="31f9f-106">包含的应用程序 :::no-loc(Identity)::: 可以应用 scaffolder 将类库中包含的源代码选择性地添加 :::no-loc(Identity)::: :::no-loc(Razor)::: (RCL) 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-106">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="31f9f-107">建议生成源代码，以便修改代码和更改行为。</span><span class="sxs-lookup"><span data-stu-id="31f9f-107">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="31f9f-108">例如，可以指示基架生成在注册过程中使用的代码。</span><span class="sxs-lookup"><span data-stu-id="31f9f-108">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="31f9f-109">生成的代码优先于 :::no-loc(Identity)::: RCL 中的相同代码。</span><span class="sxs-lookup"><span data-stu-id="31f9f-109">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="31f9f-110">若要完全控制 UI，而不使用默认的 RCL，请参阅 [创建完整 :::no-loc(Identity)::: UI 源](#full)部分。</span><span class="sxs-lookup"><span data-stu-id="31f9f-110">To gain full control of the UI and not use the default RCL, see the section [Create full :::no-loc(Identity)::: UI source](#full).</span></span>

<span data-ttu-id="31f9f-111">**不** 包含身份验证的应用程序可以应用 scaffolder 来添加 RCL :::no-loc(Identity)::: 包。</span><span class="sxs-lookup"><span data-stu-id="31f9f-111">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="31f9f-112">可以选择要生成的 :::no-loc(Identity)::: 代码。</span><span class="sxs-lookup"><span data-stu-id="31f9f-112">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="31f9f-113">尽管 scaffolder 生成了大部分必要的代码，但你需要更新项目以完成该过程。</span><span class="sxs-lookup"><span data-stu-id="31f9f-113">Although the scaffolder generates most of the necessary code, you need to update your project to complete the process.</span></span> <span data-ttu-id="31f9f-114">本文档介绍完成基架更新所需的步骤 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-114">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="31f9f-115">建议使用显示文件差异的源代码管理系统，并使您能够回退更改。</span><span class="sxs-lookup"><span data-stu-id="31f9f-115">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="31f9f-116">运行 scaffolder 后检查更改 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-116">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

<span data-ttu-id="31f9f-117">使用 [双重身份验证](xref:security/authentication/identity-enable-qrcodes)、 [帐户确认和密码恢复](xref:security/authentication/accconfirm)和其他安全功能时，需要提供服务 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-117">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="31f9f-118">基架时不生成服务或服务存根 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-118">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="31f9f-119">要启用这些功能，必须手动添加服务。</span><span class="sxs-lookup"><span data-stu-id="31f9f-119">Services to enable these features must be added manually.</span></span> <span data-ttu-id="31f9f-120">例如，请参阅 [需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。</span><span class="sxs-lookup"><span data-stu-id="31f9f-120">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

<span data-ttu-id="31f9f-121">当 :::no-loc(Identity)::: 使用现有个人帐户将具有新数据上下文的基架插入到项目中时：</span><span class="sxs-lookup"><span data-stu-id="31f9f-121">When scaffolding :::no-loc(Identity)::: with a new data context into a project with existing individual accounts:</span></span>

* <span data-ttu-id="31f9f-122">在中 `Startup.ConfigureServices` ，删除对的调用：</span><span class="sxs-lookup"><span data-stu-id="31f9f-122">In `Startup.ConfigureServices`, remove the calls to:</span></span>
  * `AddDbContext`
  * `AddDefault:::no-loc(Identity):::`

<span data-ttu-id="31f9f-123">例如， `AddDbContext` `AddDefault:::no-loc(Identity):::` 在以下代码中注释掉和：</span><span class="sxs-lookup"><span data-stu-id="31f9f-123">For example, `AddDbContext` and `AddDefault:::no-loc(Identity):::` are commented out in the following code:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRemove.cs?name=snippet)]

<span data-ttu-id="31f9f-124">前面的代码注释了 *区域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中的重复代码。</span><span class="sxs-lookup"><span data-stu-id="31f9f-124">The preceeding code comments out the code that is duplicated in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs*</span></span>

<span data-ttu-id="31f9f-125">通常，使用个人帐户创建的应用应 \* **not** _ 创建新的数据上下文。</span><span class="sxs-lookup"><span data-stu-id="31f9f-125">Typically, apps that were created with individual accounts should \* **not** _ create a new data context.</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="31f9f-126">基架 :::no-loc(Identity)::: 到空项目</span><span class="sxs-lookup"><span data-stu-id="31f9f-126">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="31f9f-127">`Startup`用类似于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="31f9f-127">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="31f9f-128">基架 :::no-loc(Identity)::: 到 :::no-loc(Razor)::: 无现有授权的项目</span><span class="sxs-lookup"><span data-stu-id="31f9f-128">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet add package Microsoft.EntityFrameworkCore.Tools
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef database drop
dotnet ef migrations add Create:::no-loc(Identity):::Schema0
dotnet ef database update
-->

<!-- ERROR
There is already an object named 'AspNetRoles' in the database.

Fixed via dotnet ef database drop
before dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="31f9f-129">:::no-loc(Identity):::在 _Areas/HostingStartup.cs \* 中进行配置 :::no-loc(Identity)::: / :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-129">:::no-loc(Identity)::: is configured in _Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs\*.</span></span> <span data-ttu-id="31f9f-130">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="31f9f-130">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="31f9f-131">迁移、UseAuthentication 和布局</span><span class="sxs-lookup"><span data-stu-id="31f9f-131">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="31f9f-132">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="31f9f-132">Enable authentication</span></span>

<span data-ttu-id="31f9f-133">`Startup`用类似于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="31f9f-133">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupRP.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="31f9f-134">布局更改</span><span class="sxs-lookup"><span data-stu-id="31f9f-134">Layout changes</span></span>

<span data-ttu-id="31f9f-135">可选：将登录部分 (`_LoginPartial`) 添加到布局文件中：</span><span class="sxs-lookup"><span data-stu-id="31f9f-135">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="31f9f-136">:::no-loc(Identity)::: :::no-loc(Razor)::: 使用授权基架到项目中</span><span class="sxs-lookup"><span data-stu-id="31f9f-136">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="31f9f-137">某些 :::no-loc(Identity)::: 选项在 *区域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="31f9f-137">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="31f9f-138">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="31f9f-138">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="31f9f-139">基架 :::no-loc(Identity)::: 到没有现有授权的 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="31f9f-139">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="31f9f-140">可选：将登录名部分 (`_LoginPartial`) 添加到 *Views/Shared/_Layout* 文件中：</span><span class="sxs-lookup"><span data-stu-id="31f9f-140">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/3.1sample/_Layout.cshtml?highlight=20)]

* <span data-ttu-id="31f9f-141">将 *Pages/shared/_LoginPartial cshtml* 文件移动到 *Views/shared/_LoginPartial。 cshtml*</span><span class="sxs-lookup"><span data-stu-id="31f9f-141">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="31f9f-142">:::no-loc(Identity):::在 *区域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="31f9f-142">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="31f9f-143">有关详细信息，请参阅 IHostingStartup。</span><span class="sxs-lookup"><span data-stu-id="31f9f-143">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="31f9f-144">`Startup`用类似于下面的代码更新类：</span><span class="sxs-lookup"><span data-stu-id="31f9f-144">Update the `Startup` class with code similar to the following:</span></span>

[!code-csharp[](scaffold-identity/3.1sample/StartupMVC.cs?name=snippet)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="31f9f-145">:::no-loc(Identity):::使用授权基架到 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="31f9f-145">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-without-existing-authorization"></a><span data-ttu-id="31f9f-146">基架 :::no-loc(Identity)::: 到 :::no-loc(Blazor Server)::: 无现有授权的项目</span><span class="sxs-lookup"><span data-stu-id="31f9f-146">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project without existing authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="31f9f-147">:::no-loc(Identity):::在 *区域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="31f9f-147">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="31f9f-148">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="31f9f-148">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

### <a name="migrations"></a><span data-ttu-id="31f9f-149">迁移</span><span class="sxs-lookup"><span data-stu-id="31f9f-149">Migrations</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

### <a name="pass-an-xsrf-token-to-the-app"></a><span data-ttu-id="31f9f-150">将 XSRF 令牌传递给应用</span><span class="sxs-lookup"><span data-stu-id="31f9f-150">Pass an XSRF token to the app</span></span>

<span data-ttu-id="31f9f-151">可以将令牌传递给组件：</span><span class="sxs-lookup"><span data-stu-id="31f9f-151">Tokens can be passed to components:</span></span>

* <span data-ttu-id="31f9f-152">设置身份验证令牌并将其保存到身份验证后 :::no-loc(cookie)::: ，可以将其传递给组件。</span><span class="sxs-lookup"><span data-stu-id="31f9f-152">When authentication tokens are provisioned and saved to the authentication :::no-loc(cookie):::, they can be passed to components.</span></span>
* <span data-ttu-id="31f9f-153">:::no-loc(Razor)::: 组件不能 `HttpContext` 直接使用，因此无法获取 [ (XSRF 的) 令牌的反请求伪造](xref:security/anti-request-forgery) :::no-loc(Identity)::: `/:::no-loc(Identity):::/Account/Logout` 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-153">:::no-loc(Razor)::: components can't use `HttpContext` directly, so there's no way to obtain an [anti-request forgery (XSRF) token](xref:security/anti-request-forgery) to POST to :::no-loc(Identity):::'s logout endpoint at `/:::no-loc(Identity):::/Account/Logout`.</span></span> <span data-ttu-id="31f9f-154">可以将 XSRF 令牌传递给组件。</span><span class="sxs-lookup"><span data-stu-id="31f9f-154">An XSRF token can be passed to components.</span></span>

<span data-ttu-id="31f9f-155">有关详细信息，请参阅 <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>。</span><span class="sxs-lookup"><span data-stu-id="31f9f-155">For more information, see <xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app>.</span></span>

<span data-ttu-id="31f9f-156">在 *Pages/_Host cshtml* 文件中，在将其添加到和类后建立该令牌 `InitialApplicationState` `TokenProvider` ：</span><span class="sxs-lookup"><span data-stu-id="31f9f-156">In the *Pages/_Host.cshtml* file, establish the token after adding it to the `InitialApplicationState` and `TokenProvider` classes:</span></span>

```csharp
@inject Microsoft.AspNetCore.Antiforgery.IAntiforgery Xsrf

...

var tokens = new InitialApplicationState
{
    ...

    XsrfToken = Xsrf.GetAndStoreTokens(HttpContext).RequestToken
};
```

<span data-ttu-id="31f9f-157">`App`将组件更新 ( *App.razor* ) ，以分配 `InitialState.XsrfToken` ：</span><span class="sxs-lookup"><span data-stu-id="31f9f-157">Update the `App` component ( *App.razor* ) to assign the `InitialState.XsrfToken`:</span></span>

```csharp
@inject TokenProvider TokenProvider

...

TokenProvider.XsrfToken = InitialState.XsrfToken;
```

<span data-ttu-id="31f9f-158">本 `TokenProvider` 主题中演示的服务在 `LoginDisplay` 以下 [布局和身份验证流更改](#layout-and-authentication-flow-changes) 部分的组件中使用。</span><span class="sxs-lookup"><span data-stu-id="31f9f-158">The `TokenProvider` service demonstrated in the topic is used in the `LoginDisplay` component in the following [Layout and authentication flow changes](#layout-and-authentication-flow-changes) section.</span></span>

### <a name="enable-authentication"></a><span data-ttu-id="31f9f-159">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="31f9f-159">Enable authentication</span></span>

<span data-ttu-id="31f9f-160">在 `Startup` 类中：</span><span class="sxs-lookup"><span data-stu-id="31f9f-160">In the `Startup` class:</span></span>

* <span data-ttu-id="31f9f-161">确认 :::no-loc(Razor)::: 在中添加了页面服务 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-161">Confirm that :::no-loc(Razor)::: Pages services are added in `Startup.ConfigureServices`.</span></span>
* <span data-ttu-id="31f9f-162">如果使用 [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)，请注册服务。</span><span class="sxs-lookup"><span data-stu-id="31f9f-162">If using the [TokenProvider](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app), register the service.</span></span>
* <span data-ttu-id="31f9f-163">`UseDatabaseErrorPage`对于开发环境，请在中的应用程序生成器上调用 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-163">Call `UseDatabaseErrorPage` on the application builder in `Startup.Configure` for the Development environment.</span></span>
* <span data-ttu-id="31f9f-164">调用 `UseAuthentication` and `UseAuthorization` after `UseRouting` 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-164">Call `UseAuthentication` and `UseAuthorization` after `UseRouting`.</span></span>
* <span data-ttu-id="31f9f-165">添加页的终结点 :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-165">Add an endpoint for :::no-loc(Razor)::: Pages.</span></span>

[!code-csharp[](scaffold-identity/3.1sample/Startup:::no-loc(Blazor):::.cs?highlight=3,6,14,27-28,32)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-and-authentication-flow-changes"></a><span data-ttu-id="31f9f-166">布局和身份验证流更改</span><span class="sxs-lookup"><span data-stu-id="31f9f-166">Layout and authentication flow changes</span></span>

<span data-ttu-id="31f9f-167">将 `RedirectToLogin` *RedirectToLogin* ) 的 (组件添加到项目根目录中应用的 *共享* 文件夹中：</span><span class="sxs-lookup"><span data-stu-id="31f9f-167">Add a `RedirectToLogin` component ( *RedirectToLogin.razor* ) to the app's *Shared* folder in the project root:</span></span>

```razor
@inject NavigationManager Navigation
@code {
    protected override void OnInitialized()
    {
        Navigation.NavigateTo(":::no-loc(Identity):::/Account/Login?returnUrl=" +
            Uri.EscapeDataString(Navigation.Uri), true);
    }
}
```

将 `LoginDisplay` () 的 *LoginDisplay.razor* 组件添加到应用的 *共享* 文件夹。 <span data-ttu-id="31f9f-169">[TokenProvider 服务](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app)提供 HTML 窗体的 XSRF 标记，该标记将发布到 :::no-loc(Identity)::: 注销终结点：</span><span class="sxs-lookup"><span data-stu-id="31f9f-169">The [TokenProvider service](xref:blazor/security/server/additional-scenarios#pass-tokens-to-a-blazor-server-app) provides the XSRF token for the HTML form that POSTs to :::no-loc(Identity):::'s logout endpoint:</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@inject NavigationManager Navigation
@inject TokenProvider TokenProvider

<AuthorizeView>
    <Authorized>
        <a href=":::no-loc(Identity):::/Account/Manage/Index">
            Hello, @context.User.:::no-loc(Identity):::.Name!
        </a>
        <form action="/:::no-loc(Identity):::/Account/Logout?returnUrl=%2F" method="post">
            <button class="nav-link btn btn-link" type="submit">Logout</button>
            <input name="__RequestVerificationToken" type="hidden" 
                value="@TokenProvider.XsrfToken">
        </form>
    </Authorized>
    <NotAuthorized>
        <a href=":::no-loc(Identity):::/Account/Register">Register</a>
        <a href=":::no-loc(Identity):::/Account/Login">Login</a>
    </NotAuthorized>
</AuthorizeView>
```

<span data-ttu-id="31f9f-170">在 `MainLayout` ( *Shared/MainLayout* ) 的组件中，将组件添加 `LoginDisplay` 到顶层行 `<div>` 元素的内容：</span><span class="sxs-lookup"><span data-stu-id="31f9f-170">In the `MainLayout` component ( *Shared/MainLayout.razor* ), add the `LoginDisplay` component to the top-row `<div>` element's content:</span></span>

```razor
<div class="top-row px-4 auth">
    <LoginDisplay />
    <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
</div>
```

### <a name="style-authentication-endpoints"></a><span data-ttu-id="31f9f-171">样式身份验证终结点</span><span class="sxs-lookup"><span data-stu-id="31f9f-171">Style authentication endpoints</span></span>

<span data-ttu-id="31f9f-172">由于 :::no-loc(Blazor Server)::: 使用 :::no-loc(Razor)::: 页面 :::no-loc(Identity)::: 页，当访问者在页面和组件间导航时，UI 的样式会发生变化 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-172">Because :::no-loc(Blazor Server)::: uses :::no-loc(Razor)::: Pages :::no-loc(Identity)::: pages, the styling of the UI changes when a visitor navigates between :::no-loc(Identity)::: pages and components.</span></span> <span data-ttu-id="31f9f-173">可以使用两个选项来处理 incongruous 样式：</span><span class="sxs-lookup"><span data-stu-id="31f9f-173">You have two options to address the incongruous styles:</span></span>

#### <a name="build-no-locidentity-components"></a><span data-ttu-id="31f9f-174">生成 :::no-loc(Identity)::: 组件</span><span class="sxs-lookup"><span data-stu-id="31f9f-174">Build :::no-loc(Identity)::: components</span></span>

<span data-ttu-id="31f9f-175">使用而不是页的组件的方法 :::no-loc(Identity)::: 是生成 :::no-loc(Identity)::: 组件。</span><span class="sxs-lookup"><span data-stu-id="31f9f-175">An approach to using components for :::no-loc(Identity)::: instead of pages is to build :::no-loc(Identity)::: components.</span></span> <span data-ttu-id="31f9f-176">由于 `SignInManager` `UserManager` 在组件中不支持和 :::no-loc(Razor)::: ，因此在应用程序中使用 API 终结点 :::no-loc(Blazor Server)::: 来处理用户帐户操作。</span><span class="sxs-lookup"><span data-stu-id="31f9f-176">Because `SignInManager` and `UserManager` aren't supported in :::no-loc(Razor)::: components, use API endpoints in the :::no-loc(Blazor Server)::: app to process user account actions.</span></span>

#### <a name="use-a-custom-layout-with-no-locblazor-app-styles"></a><span data-ttu-id="31f9f-177">在应用程序样式中使用自定义布局 :::no-loc(Blazor):::</span><span class="sxs-lookup"><span data-stu-id="31f9f-177">Use a custom layout with :::no-loc(Blazor)::: app styles</span></span>

<span data-ttu-id="31f9f-178">:::no-loc(Identity):::可以修改页面布局和样式，以生成使用默认主题的页面 :::no-loc(Blazor)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-178">The :::no-loc(Identity)::: pages layout and styles can be modified to produce pages that use the default :::no-loc(Blazor)::: theme.</span></span>

> [!NOTE]
> <span data-ttu-id="31f9f-179">本节中的示例只是一个自定义的起点。</span><span class="sxs-lookup"><span data-stu-id="31f9f-179">The example in this section is merely a starting point for customization.</span></span> <span data-ttu-id="31f9f-180">为了获得最佳用户体验，可能需要额外的工作。</span><span class="sxs-lookup"><span data-stu-id="31f9f-180">Additional work is likely required for the best user experience.</span></span>

<span data-ttu-id="31f9f-181">`NavMenu_:::no-loc(Identity):::Layout` ( *Shared/NavMenu_ :::no-loc(Identity)::: Layout* ) 创建新的组件。</span><span class="sxs-lookup"><span data-stu-id="31f9f-181">Create a new `NavMenu_:::no-loc(Identity):::Layout` component ( *Shared/NavMenu_:::no-loc(Identity):::Layout.razor* ).</span></span> <span data-ttu-id="31f9f-182">对于组件的标记和代码，请 `NavMenu` ( *Shared/NavMenu* ) 使用应用组件的相同内容。</span><span class="sxs-lookup"><span data-stu-id="31f9f-182">For the markup and code of the component, use the same content of the app's `NavMenu` component ( *Shared/NavMenu.razor* ).</span></span> <span data-ttu-id="31f9f-183">去除 `NavLink` 无法匿名访问的组件，因为组件中的自动重定向 `RedirectToLogin` 对于需要身份验证或授权的组件失败。</span><span class="sxs-lookup"><span data-stu-id="31f9f-183">Strip out any `NavLink`s to components that can't be reached anonymously because automatic redirects in the `RedirectToLogin` component fail for components requiring authentication or authorization.</span></span>

<span data-ttu-id="31f9f-184">在 *Pages/Shared/Layout* 文件中，进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="31f9f-184">In the *Pages/Shared/Layout.cshtml* file, make the following changes:</span></span>

* <span data-ttu-id="31f9f-185">将 :::no-loc(Razor)::: 指令添加到文件顶部，以使用 *共享* 文件夹中的标记帮助程序和应用组件：</span><span class="sxs-lookup"><span data-stu-id="31f9f-185">Add :::no-loc(Razor)::: directives to the top of the file to use Tag Helpers and the app's components in the *Shared* folder:</span></span>

  ```cshtml
  @addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
  @using {APPLICATION ASSEMBLY}.Shared
  ```

  <span data-ttu-id="31f9f-186">替换 `{APPLICATION ASSEMBLY}` 为应用程序的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="31f9f-186">Replace `{APPLICATION ASSEMBLY}` with the app's assembly name.</span></span>

* <span data-ttu-id="31f9f-187">向 `<base>` 内容添加标记和 :::no-loc(Blazor)::: 样式 `<link>` 表 `<head>` ：</span><span class="sxs-lookup"><span data-stu-id="31f9f-187">Add a `<base>` tag and :::no-loc(Blazor)::: stylesheet `<link>` to the `<head>` content:</span></span>

  ```cshtml
  <base href="~/" />
  <link rel="stylesheet" href="~/css/site.css" />
  ```

* <span data-ttu-id="31f9f-188">将标记的内容更改 `<body>` 为以下内容：</span><span class="sxs-lookup"><span data-stu-id="31f9f-188">Change the content of the `<body>` tag to the following:</span></span>

  ```cshtml
  <div class="sidebar" style="float:left">
      <component type="typeof(NavMenu_:::no-loc(Identity):::Layout)" 
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
              throw new InvalidOperationException("The default :::no-loc(Identity)::: UI " +
                  "layout requires a partial view '_LoginPartial'.");
          }
          <a href="https://docs.microsoft.com/aspnet/" target="_blank">About</a>
      </div>

      <div class="content px-4">
          @RenderBody()
      </div>
  </div>

  <script src="~/:::no-loc(Identity):::/lib/jquery/dist/jquery.min.js"></script>
  <script src="~/:::no-loc(Identity):::/lib/bootstrap/dist/js/bootstrap.bundle.min.js"></script>
  <script src="~/:::no-loc(Identity):::/js/site.js" asp-append-version="true"></script>
  @RenderSection("Scripts", required: false)
  <script src="_framework/blazor.server.js"></script>
  ```

## <a name="scaffold-no-locidentity-into-a-no-locblazor-server-project-with-authorization"></a><span data-ttu-id="31f9f-189">:::no-loc(Identity)::: :::no-loc(Blazor Server)::: 使用授权基架到项目中</span><span class="sxs-lookup"><span data-stu-id="31f9f-189">Scaffold :::no-loc(Identity)::: into a :::no-loc(Blazor Server)::: project with authorization</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="31f9f-190">某些 :::no-loc(Identity)::: 选项在 *区域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="31f9f-190">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="31f9f-191">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="31f9f-191">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="31f9f-192">创建完整的 :::no-loc(Identity)::: UI 源</span><span class="sxs-lookup"><span data-stu-id="31f9f-192">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="31f9f-193">若要维护 UI 的完全控制 :::no-loc(Identity)::: ，请运行 :::no-loc(Identity)::: scaffolder 并选择 " **替代所有文件** "。</span><span class="sxs-lookup"><span data-stu-id="31f9f-193">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="31f9f-194">以下突出显示的代码显示了将默认 UI 替换为 :::no-loc(Identity)::: :::no-loc(Identity)::: ASP.NET Core 2.1 web 应用中的更改。</span><span class="sxs-lookup"><span data-stu-id="31f9f-194">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="31f9f-195">你可能希望执行此操作以对 UI 具有完全控制 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-195">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="31f9f-196">:::no-loc(Identity):::在以下代码中，将替换默认值：</span><span class="sxs-lookup"><span data-stu-id="31f9f-196">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="31f9f-197">下面的代码将设置 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)和 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)：</span><span class="sxs-lookup"><span data-stu-id="31f9f-197">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="31f9f-198">注册 `IEmailSender` 实现，例如：</span><span class="sxs-lookup"><span data-stu-id="31f9f-198">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="31f9f-199">密码配置</span><span class="sxs-lookup"><span data-stu-id="31f9f-199">Password configuration</span></span>

<span data-ttu-id="31f9f-200">如果 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> 在中配置了 `Startup.ConfigureServices` ，则基架页中的属性可能需要[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)配置 `Password` :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-200">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="31f9f-201">`InputModel``Password`在以下文件中可以找到属性：</span><span class="sxs-lookup"><span data-stu-id="31f9f-201">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-a-page"></a><span data-ttu-id="31f9f-202">禁用页面</span><span class="sxs-lookup"><span data-stu-id="31f9f-202">Disable a page</span></span>

<span data-ttu-id="31f9f-203">本部分介绍如何禁用注册页面，但该方法可用于禁用任何页面。</span><span class="sxs-lookup"><span data-stu-id="31f9f-203">This sections show how to disable the register page but the approach can be used to disable any page.</span></span>

<span data-ttu-id="31f9f-204">禁用用户注册：</span><span class="sxs-lookup"><span data-stu-id="31f9f-204">To disable user registration:</span></span>

* <span data-ttu-id="31f9f-205">基架 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-205">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="31f9f-206">包括帐户. Register、RegisterConfirmation。</span><span class="sxs-lookup"><span data-stu-id="31f9f-206">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="31f9f-207">例如： 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-207">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="31f9f-208">更新 *区域/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* ，使用户无法从此终结点注册：</span><span class="sxs-lookup"><span data-stu-id="31f9f-208">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="31f9f-209">更新 *区域/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml* ，使其与前面的更改一致：</span><span class="sxs-lookup"><span data-stu-id="31f9f-209">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="31f9f-210">注释掉或删除 *区域/ :::no-loc(Identity)::: /Pages/Account/Login.cshtml* 中的注册链接</span><span class="sxs-lookup"><span data-stu-id="31f9f-210">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

  ```cshtml
  @*
  <p>
      <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
  </p>
  *@
  ```

* <span data-ttu-id="31f9f-211">更新 " *区域/ :::no-loc(Identity)::: /Pages/Account/RegisterConfirmation* " 页。</span><span class="sxs-lookup"><span data-stu-id="31f9f-211">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="31f9f-212">删除来自 cshtml 文件的代码和链接。</span><span class="sxs-lookup"><span data-stu-id="31f9f-212">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="31f9f-213">从中删除确认代码 `PageModel` ：</span><span class="sxs-lookup"><span data-stu-id="31f9f-213">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="31f9f-214">使用其他应用添加用户</span><span class="sxs-lookup"><span data-stu-id="31f9f-214">Use another app to add users</span></span>

<span data-ttu-id="31f9f-215">提供一种在 web 应用外部添加用户的机制。</span><span class="sxs-lookup"><span data-stu-id="31f9f-215">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="31f9f-216">用于添加用户的选项包括：</span><span class="sxs-lookup"><span data-stu-id="31f9f-216">Options to add users include:</span></span>

* <span data-ttu-id="31f9f-217">专用的管理 web 应用。</span><span class="sxs-lookup"><span data-stu-id="31f9f-217">A dedicated admin web app.</span></span>
* <span data-ttu-id="31f9f-218">控制台应用。</span><span class="sxs-lookup"><span data-stu-id="31f9f-218">A console app.</span></span>

<span data-ttu-id="31f9f-219">下面的代码概述了一种添加用户的方法：</span><span class="sxs-lookup"><span data-stu-id="31f9f-219">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="31f9f-220">用户列表将读入内存中。</span><span class="sxs-lookup"><span data-stu-id="31f9f-220">A list of users is read into memory.</span></span>
* <span data-ttu-id="31f9f-221">为每个用户生成一个强唯一密码。</span><span class="sxs-lookup"><span data-stu-id="31f9f-221">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="31f9f-222">用户已添加到 :::no-loc(Identity)::: 数据库。</span><span class="sxs-lookup"><span data-stu-id="31f9f-222">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="31f9f-223">系统会通知用户并通知用户更改密码。</span><span class="sxs-lookup"><span data-stu-id="31f9f-223">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="31f9f-224">下面的代码概述了如何添加用户：</span><span class="sxs-lookup"><span data-stu-id="31f9f-224">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="31f9f-225">对于生产方案，可以遵循类似的方法。</span><span class="sxs-lookup"><span data-stu-id="31f9f-225">A similar approach can be followed for production scenarios.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="31f9f-226">禁止发布静态 :::no-loc(Identity)::: 资产</span><span class="sxs-lookup"><span data-stu-id="31f9f-226">Prevent publish of static :::no-loc(Identity)::: assets</span></span>

<span data-ttu-id="31f9f-227">若要防止 :::no-loc(Identity)::: 将静态资产发布到 web 根目录，请参阅 <xref:security/authentication/identity#prevent-publish-of-static-identity-assets> 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-227">To prevent publishing static :::no-loc(Identity)::: assets to the web root, see <xref:security/authentication/identity#prevent-publish-of-static-identity-assets>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="31f9f-228">其他资源</span><span class="sxs-lookup"><span data-stu-id="31f9f-228">Additional resources</span></span>

* [<span data-ttu-id="31f9f-229">更改为 ASP.NET Core 2.1 及更高版本的身份验证代码</span><span class="sxs-lookup"><span data-stu-id="31f9f-229">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="31f9f-230">ASP.NET Core 2.1 和更高版本提供 [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) 为[ :::no-loc(Razor)::: 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="31f9f-230">ASP.NET Core 2.1 and later provides [:::no-loc(ASP.NET Core Identity):::](xref:security/authentication/identity) as a [:::no-loc(Razor)::: Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="31f9f-231">包含的应用程序 :::no-loc(Identity)::: 可以应用 scaffolder 将类库中包含的源代码选择性地添加 :::no-loc(Identity)::: :::no-loc(Razor)::: (RCL) 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-231">Applications that include :::no-loc(Identity)::: can apply the scaffolder to selectively add the source code contained in the :::no-loc(Identity)::: :::no-loc(Razor)::: Class Library (RCL).</span></span> <span data-ttu-id="31f9f-232">建议生成源代码，以便修改代码和更改行为。</span><span class="sxs-lookup"><span data-stu-id="31f9f-232">You might want to generate source code so you can modify the code and change the behavior.</span></span> <span data-ttu-id="31f9f-233">例如，可以指示基架生成在注册过程中使用的代码。</span><span class="sxs-lookup"><span data-stu-id="31f9f-233">For example, you could instruct the scaffolder to generate the code used in registration.</span></span> <span data-ttu-id="31f9f-234">生成的代码优先于 :::no-loc(Identity)::: RCL 中的相同代码。</span><span class="sxs-lookup"><span data-stu-id="31f9f-234">Generated code takes precedence over the same code in the :::no-loc(Identity)::: RCL.</span></span> <span data-ttu-id="31f9f-235">若要完全控制 UI，而不使用默认的 RCL，请参阅 [创建完全标识 UI 源](#full)部分。</span><span class="sxs-lookup"><span data-stu-id="31f9f-235">To gain full control of the UI and not use the default RCL, see the section [Create full identity UI source](#full).</span></span>

<span data-ttu-id="31f9f-236">**不** 包含身份验证的应用程序可以应用 scaffolder 来添加 RCL :::no-loc(Identity)::: 包。</span><span class="sxs-lookup"><span data-stu-id="31f9f-236">Applications that do **not** include authentication can apply the scaffolder to add the RCL :::no-loc(Identity)::: package.</span></span> <span data-ttu-id="31f9f-237">可以选择要生成的 :::no-loc(Identity)::: 代码。</span><span class="sxs-lookup"><span data-stu-id="31f9f-237">You have the option of selecting :::no-loc(Identity)::: code to be generated.</span></span>

<span data-ttu-id="31f9f-238">尽管 scaffolder 生成了大部分必要的代码，但你必须更新项目才能完成此过程。</span><span class="sxs-lookup"><span data-stu-id="31f9f-238">Although the scaffolder generates most of the necessary code, you'll have to update your project to complete the process.</span></span> <span data-ttu-id="31f9f-239">本文档介绍完成基架更新所需的步骤 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-239">This document explains the steps needed to complete an :::no-loc(Identity)::: scaffolding update.</span></span>

<span data-ttu-id="31f9f-240">当 :::no-loc(Identity)::: scaffolder 运行时，将在项目目录中创建一个 *ScaffoldingReadme.txt* 文件。</span><span class="sxs-lookup"><span data-stu-id="31f9f-240">When the :::no-loc(Identity)::: scaffolder is run, a *ScaffoldingReadme.txt* file is created in the project directory.</span></span> <span data-ttu-id="31f9f-241">*ScaffoldingReadme.txt* 文件包含有关完成基架更新所需内容的一般说明 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-241">The *ScaffoldingReadme.txt* file contains general instructions on what's needed to complete the :::no-loc(Identity)::: scaffolding update.</span></span> <span data-ttu-id="31f9f-242">本文档包含与 *ScaffoldingReadme.txt* 文件更完整的说明。</span><span class="sxs-lookup"><span data-stu-id="31f9f-242">This document contains more complete instructions than the *ScaffoldingReadme.txt* file.</span></span>

<span data-ttu-id="31f9f-243">建议使用显示文件差异的源代码管理系统，并使您能够回退更改。</span><span class="sxs-lookup"><span data-stu-id="31f9f-243">We recommend using a source control system that shows file differences and allows you to back out of changes.</span></span> <span data-ttu-id="31f9f-244">运行 scaffolder 后检查更改 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-244">Inspect the changes after running the :::no-loc(Identity)::: scaffolder.</span></span>

> [!NOTE]
> <span data-ttu-id="31f9f-245">使用 [双重身份验证](xref:security/authentication/identity-enable-qrcodes)、 [帐户确认和密码恢复](xref:security/authentication/accconfirm)和其他安全功能时，需要提供服务 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-245">Services are required when using [Two Factor Authentication](xref:security/authentication/identity-enable-qrcodes), [Account confirmation and password recovery](xref:security/authentication/accconfirm), and other security features with :::no-loc(Identity):::.</span></span> <span data-ttu-id="31f9f-246">基架时不生成服务或服务存根 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-246">Services or service stubs aren't generated when scaffolding :::no-loc(Identity):::.</span></span> <span data-ttu-id="31f9f-247">要启用这些功能，必须手动添加服务。</span><span class="sxs-lookup"><span data-stu-id="31f9f-247">Services to enable these features must be added manually.</span></span> <span data-ttu-id="31f9f-248">例如，请参阅 [需要确认电子邮件](xref:security/authentication/accconfirm#require-email-confirmation)。</span><span class="sxs-lookup"><span data-stu-id="31f9f-248">For example, see [Require Email Confirmation](xref:security/authentication/accconfirm#require-email-confirmation).</span></span>

## <a name="scaffold-no-locidentity-into-an-empty-project"></a><span data-ttu-id="31f9f-249">基架 :::no-loc(Identity)::: 到空项目</span><span class="sxs-lookup"><span data-stu-id="31f9f-249">Scaffold :::no-loc(Identity)::: into an empty project</span></span>

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="31f9f-250">将以下突出显示的调用添加到 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="31f9f-250">Add the following highlighted calls to the `Startup` class:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupEmpty.cs?name=snippet1&highlight=5,20-23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-without-existing-authorization"></a><span data-ttu-id="31f9f-251">基架 :::no-loc(Identity)::: 到 :::no-loc(Razor)::: 无现有授权的项目</span><span class="sxs-lookup"><span data-stu-id="31f9f-251">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project without existing authorization</span></span>

<!--  Updated for 3.0
set projNam=RPnoAuth
set projType=webapp

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet add package Microsoft.EntityFrameworkCore.Design
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.EntityFrameworkCore
dotnet add package Microsoft.AspNetCore.:::no-loc(Identity):::.UI
dotnet add package Microsoft.EntityFrameworkCore.SqlServer
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="31f9f-252">:::no-loc(Identity):::在 *区域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="31f9f-252">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="31f9f-253">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="31f9f-253">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

<a name="efm"></a>

### <a name="migrations-useauthentication-and-layout"></a><span data-ttu-id="31f9f-254">迁移、UseAuthentication 和布局</span><span class="sxs-lookup"><span data-stu-id="31f9f-254">Migrations, UseAuthentication, and layout</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<a name="useauthentication"></a>

### <a name="enable-authentication"></a><span data-ttu-id="31f9f-255">启用身份验证</span><span class="sxs-lookup"><span data-stu-id="31f9f-255">Enable authentication</span></span>

<span data-ttu-id="31f9f-256">在 `Configure` 类的方法中 `Startup` ，在以下情况下调用 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) `UseStaticFiles` ：</span><span class="sxs-lookup"><span data-stu-id="31f9f-256">In the `Configure` method of the `Startup` class, call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupRPnoAuth.cs?name=snippet1&highlight=29)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

### <a name="layout-changes"></a><span data-ttu-id="31f9f-257">布局更改</span><span class="sxs-lookup"><span data-stu-id="31f9f-257">Layout changes</span></span>

<span data-ttu-id="31f9f-258">可选：将登录部分 (`_LoginPartial`) 添加到布局文件中：</span><span class="sxs-lookup"><span data-stu-id="31f9f-258">Optional: Add the login partial (`_LoginPartial`) to the layout file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_Layout.cshtml?highlight=37)]

## <a name="scaffold-no-locidentity-into-a-no-locrazor-project-with-authorization"></a><span data-ttu-id="31f9f-259">:::no-loc(Identity)::: :::no-loc(Razor)::: 使用授权基架到项目中</span><span class="sxs-lookup"><span data-stu-id="31f9f-259">Scaffold :::no-loc(Identity)::: into a :::no-loc(Razor)::: project with authorization</span></span>

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

<span data-ttu-id="31f9f-260">某些 :::no-loc(Identity)::: 选项在 *区域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="31f9f-260">Some :::no-loc(Identity)::: options are configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="31f9f-261">有关详细信息，请参阅 [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration)。</span><span class="sxs-lookup"><span data-stu-id="31f9f-261">For more information, see [IHostingStartup](xref:fundamentals/configuration/platform-specific-configuration).</span></span>

## <a name="scaffold-no-locidentity-into-an-mvc-project-without-existing-authorization"></a><span data-ttu-id="31f9f-262">基架 :::no-loc(Identity)::: 到没有现有授权的 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="31f9f-262">Scaffold :::no-loc(Identity)::: into an MVC project without existing authorization</span></span>

<!--
set projNam=MvcNoAuth
set projType=mvc
set version=2.1.0

dotnet new %projType% -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design -v %version%
dotnet restore
dotnet aspnet-codegenerator identity --useDefaultUI
dotnet ef migrations add Create:::no-loc(Identity):::Schema
dotnet ef database update
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg.md)]

<span data-ttu-id="31f9f-263">可选：将登录名部分 (`_LoginPartial`) 添加到 *Views/Shared/_Layout* 文件中：</span><span class="sxs-lookup"><span data-stu-id="31f9f-263">Optional: Add the login partial (`_LoginPartial`) to the *Views/Shared/_Layout.cshtml* file:</span></span>

[!code-cshtml[](scaffold-identity/sample/_LayoutMvc.cshtml?highlight=37)]

* <span data-ttu-id="31f9f-264">将 *Pages/shared/_LoginPartial cshtml* 文件移动到 *Views/shared/_LoginPartial。 cshtml*</span><span class="sxs-lookup"><span data-stu-id="31f9f-264">Move the *Pages/Shared/_LoginPartial.cshtml* file to *Views/Shared/_LoginPartial.cshtml*</span></span>

<span data-ttu-id="31f9f-265">:::no-loc(Identity):::在 *区域/ :::no-loc(Identity)::: / :::no-loc(Identity)::: HostingStartup.cs* 中配置。</span><span class="sxs-lookup"><span data-stu-id="31f9f-265">:::no-loc(Identity)::: is configured in *Areas/:::no-loc(Identity):::/:::no-loc(Identity):::HostingStartup.cs* .</span></span> <span data-ttu-id="31f9f-266">有关详细信息，请参阅 IHostingStartup。</span><span class="sxs-lookup"><span data-stu-id="31f9f-266">For more information, see IHostingStartup.</span></span>

[!INCLUDE[](~/includes/scaffold-identity/migrations.md)]

<span data-ttu-id="31f9f-267">调用 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) 之后 `UseStaticFiles` ：</span><span class="sxs-lookup"><span data-stu-id="31f9f-267">Call [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication?view=aspnetcore-2.0#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_) after `UseStaticFiles`:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupMvcNoAuth.cs?name=snippet1&highlight=23)]

[!INCLUDE[](~/includes/scaffold-identity/hsts.md)]

## <a name="scaffold-no-locidentity-into-an-mvc-project-with-authorization"></a><span data-ttu-id="31f9f-268">:::no-loc(Identity):::使用授权基架到 MVC 项目</span><span class="sxs-lookup"><span data-stu-id="31f9f-268">Scaffold :::no-loc(Identity)::: into an MVC project with authorization</span></span>

<!--
dotnet new mvc -au Individual -o MvcAuth
cd MvcAuth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc MvcAuth.Data.ApplicationDbContext  --files "Account.Login;Account.Register"
-->

[!INCLUDE[](~/includes/scaffold-identity/id-scaffold-dlg-auth.md)]

<span data-ttu-id="31f9f-269">删除 *页面/共享* 文件夹和该文件夹中的文件。</span><span class="sxs-lookup"><span data-stu-id="31f9f-269">Delete the *Pages/Shared* folder and the files in that folder.</span></span>

<a name="full"></a>

## <a name="create-full-no-locidentity-ui-source"></a><span data-ttu-id="31f9f-270">创建完整的 :::no-loc(Identity)::: UI 源</span><span class="sxs-lookup"><span data-stu-id="31f9f-270">Create full :::no-loc(Identity)::: UI source</span></span>

<span data-ttu-id="31f9f-271">若要维护 UI 的完全控制 :::no-loc(Identity)::: ，请运行 :::no-loc(Identity)::: scaffolder 并选择 " **替代所有文件** "。</span><span class="sxs-lookup"><span data-stu-id="31f9f-271">To maintain full control of the :::no-loc(Identity)::: UI, run the :::no-loc(Identity)::: scaffolder and select **Override all files** .</span></span>

<span data-ttu-id="31f9f-272">以下突出显示的代码显示了将默认 UI 替换为 :::no-loc(Identity)::: :::no-loc(Identity)::: ASP.NET Core 2.1 web 应用中的更改。</span><span class="sxs-lookup"><span data-stu-id="31f9f-272">The following highlighted code shows the changes to replace the default :::no-loc(Identity)::: UI with :::no-loc(Identity)::: in an ASP.NET Core 2.1 web app.</span></span> <span data-ttu-id="31f9f-273">你可能希望执行此操作以对 UI 具有完全控制 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-273">You might want to do this to have full control of the :::no-loc(Identity)::: UI.</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet1&highlight=13-14,17-999)]

<span data-ttu-id="31f9f-274">:::no-loc(Identity):::在以下代码中，将替换默认值：</span><span class="sxs-lookup"><span data-stu-id="31f9f-274">The default :::no-loc(Identity)::: is replaced in the following code:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet2)]

<span data-ttu-id="31f9f-275">下面的代码将设置 [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath)、 [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath)和 [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath)：</span><span class="sxs-lookup"><span data-stu-id="31f9f-275">The following code sets the [LoginPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.loginpath), [LogoutPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.logoutpath), and [AccessDeniedPath](/dotnet/api/microsoft.aspnetcore.authentication.:::no-loc(cookie):::s.:::no-loc(cookie):::authenticationoptions.accessdeniedpath):</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet3)]

<span data-ttu-id="31f9f-276">注册 `IEmailSender` 实现，例如：</span><span class="sxs-lookup"><span data-stu-id="31f9f-276">Register an `IEmailSender` implementation, for example:</span></span>

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet4)]

[!code-csharp[](scaffold-identity/sample/StartupFull.cs?name=snippet)]

<!--
uld option: Use Local DB, not SQLite

dotnet new webapp -au Individual -uld -o RPauth
cd RPauth
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
-->

## <a name="password-configuration"></a><span data-ttu-id="31f9f-277">密码配置</span><span class="sxs-lookup"><span data-stu-id="31f9f-277">Password configuration</span></span>

<span data-ttu-id="31f9f-278">如果 <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> 在中配置了 `Startup.ConfigureServices` ，则基架页中的属性可能需要[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)配置 `Password` :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-278">If <xref:Microsoft.AspNetCore.:::no-loc(Identity):::.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded :::no-loc(Identity)::: pages.</span></span> <span data-ttu-id="31f9f-279">`InputModel``Password`在以下文件中可以找到属性：</span><span class="sxs-lookup"><span data-stu-id="31f9f-279">`InputModel` `Password` properties are found in the following files:</span></span>

* `Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs`
* `Areas/:::no-loc(Identity):::/Pages/Account/ResetPassword.cshtml.cs`

## <a name="disable-register-page"></a><span data-ttu-id="31f9f-280">禁用注册页</span><span class="sxs-lookup"><span data-stu-id="31f9f-280">Disable register page</span></span>

<span data-ttu-id="31f9f-281">禁用用户注册：</span><span class="sxs-lookup"><span data-stu-id="31f9f-281">To disable user registration:</span></span>

* <span data-ttu-id="31f9f-282">基架 :::no-loc(Identity)::: 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-282">Scaffold :::no-loc(Identity):::.</span></span> <span data-ttu-id="31f9f-283">包括帐户. Register、RegisterConfirmation。</span><span class="sxs-lookup"><span data-stu-id="31f9f-283">Include Account.Register, Account.Login, and Account.RegisterConfirmation.</span></span> <span data-ttu-id="31f9f-284">例如： 。</span><span class="sxs-lookup"><span data-stu-id="31f9f-284">For example:</span></span>

  ```dotnetcli
   dotnet aspnet-codegenerator identity -dc RPauth.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.RegisterConfirmation"
  ```

* <span data-ttu-id="31f9f-285">更新 *区域/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml.cs* ，使用户无法从此终结点注册：</span><span class="sxs-lookup"><span data-stu-id="31f9f-285">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml.cs* so users can't register from this endpoint:</span></span>

  [!code-csharp[](scaffold-identity/sample/Register.cshtml.cs?name=snippet)]

* <span data-ttu-id="31f9f-286">更新 *区域/ :::no-loc(Identity)::: /Pages/Account/Register.cshtml* ，使其与前面的更改一致：</span><span class="sxs-lookup"><span data-stu-id="31f9f-286">Update *Areas/:::no-loc(Identity):::/Pages/Account/Register.cshtml* to be consistent with the preceding changes:</span></span>

  [!code-cshtml[](scaffold-identity/sample/Register.cshtml)]

* <span data-ttu-id="31f9f-287">注释掉或删除 *区域/ :::no-loc(Identity)::: /Pages/Account/Login.cshtml* 中的注册链接</span><span class="sxs-lookup"><span data-stu-id="31f9f-287">Comment out or remove the registration link from *Areas/:::no-loc(Identity):::/Pages/Account/Login.cshtml*</span></span>

```cshtml
@*
<p>
    <a asp-page="./Register" asp-route-returnUrl="@Model.ReturnUrl">Register as a new user</a>
</p>
*@
```

* <span data-ttu-id="31f9f-288">更新 " *区域/ :::no-loc(Identity)::: /Pages/Account/RegisterConfirmation* " 页。</span><span class="sxs-lookup"><span data-stu-id="31f9f-288">Update the *Areas/:::no-loc(Identity):::/Pages/Account/RegisterConfirmation* page.</span></span>

  * <span data-ttu-id="31f9f-289">删除来自 cshtml 文件的代码和链接。</span><span class="sxs-lookup"><span data-stu-id="31f9f-289">Remove the code and links from the cshtml file.</span></span>
  * <span data-ttu-id="31f9f-290">从中删除确认代码 `PageModel` ：</span><span class="sxs-lookup"><span data-stu-id="31f9f-290">Remove the confirmation code from the `PageModel`:</span></span>

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
  
### <a name="use-another-app-to-add-users"></a><span data-ttu-id="31f9f-291">使用其他应用添加用户</span><span class="sxs-lookup"><span data-stu-id="31f9f-291">Use another app to add users</span></span>

<span data-ttu-id="31f9f-292">提供一种在 web 应用外部添加用户的机制。</span><span class="sxs-lookup"><span data-stu-id="31f9f-292">Provide a mechanism to add users outside the web app.</span></span> <span data-ttu-id="31f9f-293">用于添加用户的选项包括：</span><span class="sxs-lookup"><span data-stu-id="31f9f-293">Options to add users include:</span></span>

* <span data-ttu-id="31f9f-294">专用的管理 web 应用。</span><span class="sxs-lookup"><span data-stu-id="31f9f-294">A dedicated admin web app.</span></span>
* <span data-ttu-id="31f9f-295">控制台应用。</span><span class="sxs-lookup"><span data-stu-id="31f9f-295">A console app.</span></span>

<span data-ttu-id="31f9f-296">下面的代码概述了一种添加用户的方法：</span><span class="sxs-lookup"><span data-stu-id="31f9f-296">The following code outlines one approach to adding users:</span></span>

* <span data-ttu-id="31f9f-297">用户列表将读入内存中。</span><span class="sxs-lookup"><span data-stu-id="31f9f-297">A list of users is read into memory.</span></span>
* <span data-ttu-id="31f9f-298">为每个用户生成一个强唯一密码。</span><span class="sxs-lookup"><span data-stu-id="31f9f-298">A strong unique password is generated for each user.</span></span>
* <span data-ttu-id="31f9f-299">用户已添加到 :::no-loc(Identity)::: 数据库。</span><span class="sxs-lookup"><span data-stu-id="31f9f-299">The user is added to the :::no-loc(Identity)::: database.</span></span>
* <span data-ttu-id="31f9f-300">系统会通知用户并通知用户更改密码。</span><span class="sxs-lookup"><span data-stu-id="31f9f-300">The user is notified and told to change the password.</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Program.cs?name=snippet)]

<span data-ttu-id="31f9f-301">下面的代码概述了如何添加用户：</span><span class="sxs-lookup"><span data-stu-id="31f9f-301">The following code outlines adding a user:</span></span>

[!code-csharp[](scaffold-identity/consoleAddUser/Data/SeedData.cs?name=snippet)]

<span data-ttu-id="31f9f-302">对于生产方案，可以遵循类似的方法。</span><span class="sxs-lookup"><span data-stu-id="31f9f-302">A similar approach can be followed for production scenarios.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="31f9f-303">其他资源</span><span class="sxs-lookup"><span data-stu-id="31f9f-303">Additional resources</span></span>

* [<span data-ttu-id="31f9f-304">更改为 ASP.NET Core 2.1 及更高版本的身份验证代码</span><span class="sxs-lookup"><span data-stu-id="31f9f-304">Changes to authentication code to ASP.NET Core 2.1 and later</span></span>](xref:migration/20_21#changes-to-authentication-code)

::: moniker-end
