---
title: 使用身份验证库保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: 了解如何使用身份验证库保护 ASP.NET Core Blazor WebAssembly 独立应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: a198606caf55232c221f1d1f1224918d3f87f04c
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280892"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="98721-103">使用身份验证库保护 ASP.NET Core Blazor WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="98721-103">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="98721-104">对于 Azure Active Directory (AAD) 和 Azure Active Directory B2C (AAD B2C)，请勿按照本主题中的指南进行操作。请参阅此目录节点中的 AAD 和 AAD B2C 主题。</span><span class="sxs-lookup"><span data-stu-id="98721-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="98721-105">若要创建使用 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 库的[独立 Blazor WebAssembly 应用](xref:blazor/hosting-models#blazor-webassembly)，请按照适用于所选工具的指南操作。</span><span class="sxs-lookup"><span data-stu-id="98721-105">To create a [standalone Blazor WebAssembly app](xref:blazor/hosting-models#blazor-webassembly) that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) library, follow the guidance for your choice of tooling.</span></span> <span data-ttu-id="98721-106">如果添加对身份验证的支持，请参阅本文中的以下部分，了解有关设置和配置应用的指南。</span><span class="sxs-lookup"><span data-stu-id="98721-106">If adding support for authentication, see the following sections of this article for guidance on setting up and configuring the app.</span></span>

> [!NOTE]
> <span data-ttu-id="98721-107">Identity 提供程序 (IP) 必须使用 [OpenID Connect (OIDC)](https://openid.net/connect/)。</span><span class="sxs-lookup"><span data-stu-id="98721-107">The Identity Provider (IP) must use [OpenID Connect (OIDC)](https://openid.net/connect/).</span></span> <span data-ttu-id="98721-108">例如，Facebook 的 IP 不是符合 OIDC 的提供程序，因此本主题中的指南不适用于 Facebook IP。</span><span class="sxs-lookup"><span data-stu-id="98721-108">For example, Facebook's IP isn't an OIDC-compliant provider, so the guidance in this topic doesn't work with the Facebook IP.</span></span> <span data-ttu-id="98721-109">有关详细信息，请参阅 <xref:blazor/security/webassembly/index#authentication-library>。</span><span class="sxs-lookup"><span data-stu-id="98721-109">For more information, see <xref:blazor/security/webassembly/index#authentication-library>.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="98721-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="98721-110">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="98721-111">新建具有身份验证机制的 Blazor WebAssembly 项目：</span><span class="sxs-lookup"><span data-stu-id="98721-111">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="98721-112">在“新建 ASP.NET Core Web 应用”对话框中选择“Blazor WebAssembly应用”模板后，选择“身份验证”下的“更改”。</span><span class="sxs-lookup"><span data-stu-id="98721-112">After choosing the **Blazor WebAssembly App** template in the **Create a new ASP.NET Core Web Application** dialog, select **Change** under **Authentication**.</span></span>

1. <span data-ttu-id="98721-113">通过“存储应用内的用户帐户”选项选择“单个用户帐户”，以使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统。 </span><span class="sxs-lookup"><span data-stu-id="98721-113">Select **Individual User Accounts** with the **Store user accounts in-app** option to use ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span> <span data-ttu-id="98721-114">此选择将添加身份验证支持，并且最终不会将用户存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="98721-114">This selection adds authentication support and doesn't result in storing users in a database.</span></span> <span data-ttu-id="98721-115">本文的以下部分提供了更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="98721-115">The following sections of this article provide further details.</span></span>

# <a name="visual-studio-code--net-core-cli"></a>[<span data-ttu-id="98721-116">Visual Studio Code/.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="98721-116">Visual Studio Code / .NET Core CLI</span></span>](#tab/visual-studio-code+netcore-cli)

<span data-ttu-id="98721-117">在空文件夹中新建具有身份验证机制的 Blazor WebAssembly项目。</span><span class="sxs-lookup"><span data-stu-id="98721-117">Create a new Blazor WebAssembly project with an authentication mechanism in an empty folder.</span></span> <span data-ttu-id="98721-118">通过 `-au|--auth` 选项指定 `Individual` 身份验证机制，以使用 ASP.NET Core 的 [Identity](xref:security/authentication/identity) 系统。</span><span class="sxs-lookup"><span data-stu-id="98721-118">Specify the `Individual` authentication mechanism with the `-au|--auth` option to use ASP.NET Core's [Identity](xref:security/authentication/identity) system.</span></span> <span data-ttu-id="98721-119">此选择将添加身份验证支持，并且最终不会将用户存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="98721-119">This selection adds authentication support and doesn't result in storing users in a database.</span></span> <span data-ttu-id="98721-120">本文的以下部分提供了更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="98721-120">The following sections of this article provide further details.</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual -o {APP NAME}
```

| <span data-ttu-id="98721-121">占位符</span><span class="sxs-lookup"><span data-stu-id="98721-121">Placeholder</span></span>  | <span data-ttu-id="98721-122">示例</span><span class="sxs-lookup"><span data-stu-id="98721-122">Example</span></span>        |
| ------------ | -------------- |
| `{APP NAME}` | `BlazorSample` |

<span data-ttu-id="98721-123">使用 `-o|--output` 选项指定的输出位置将创建一个项目文件夹（如果该文件夹不存在）并成为应用程序名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="98721-123">The output location specified with the `-o|--output` option creates a project folder if it doesn't exist and becomes part of the app's name.</span></span>

<span data-ttu-id="98721-124">有关详细信息，请参阅 .NET Core 指南中的 [`dotnet new`](/dotnet/core/tools/dotnet-new) 命令。</span><span class="sxs-lookup"><span data-stu-id="98721-124">For more information, see the [`dotnet new`](/dotnet/core/tools/dotnet-new) command in the .NET Core Guide.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="98721-125">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="98721-125">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="98721-126">新建具有身份验证机制的 Blazor WebAssembly 项目：</span><span class="sxs-lookup"><span data-stu-id="98721-126">To create a new Blazor WebAssembly project with an authentication mechanism:</span></span>

1. <span data-ttu-id="98721-127">在“配置新的 Blazor WebAssembly应用”步骤中，从“身份验证”下拉列表中选择“个人身份验证(应用内)”。</span><span class="sxs-lookup"><span data-stu-id="98721-127">On the **Configure your new Blazor WebAssembly App** step, select **Individual Authentication (in-app)** from the **Authentication** drop down.</span></span>

1. <span data-ttu-id="98721-128">创建应用以使用 ASP.NET Core [Identity](xref:security/authentication/identity)，这样便不会将用户存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="98721-128">The app is created to use ASP.NET Core [Identity](xref:security/authentication/identity) and doesn't result in storing users in a database.</span></span> <span data-ttu-id="98721-129">本文的以下部分提供了更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="98721-129">The following sections of this article provide further details.</span></span>

---

## <a name="authentication-package"></a><span data-ttu-id="98721-130">身份验证包</span><span class="sxs-lookup"><span data-stu-id="98721-130">Authentication package</span></span>

<span data-ttu-id="98721-131">创建应用以使用个人用户帐户时，该应用会在其项目文件中自动接收 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="98721-131">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package in the app's project file.</span></span> <span data-ttu-id="98721-132">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="98721-132">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="98721-133">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="98721-133">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="{VERSION}" />
```

<span data-ttu-id="98721-134">对于占位符 `{VERSION}`，可在包的版本历史记录（位于 [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication)）中找到与应用的共享框架版本匹配的最新稳定版本的包。</span><span class="sxs-lookup"><span data-stu-id="98721-134">For the placeholder `{VERSION}`, the latest stable version of the package that matches the app's shared framework version can be found in the package's **Version History** at [NuGet.org](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication).</span></span>

## <a name="authentication-service-support"></a><span data-ttu-id="98721-135">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="98721-135">Authentication service support</span></span>

<span data-ttu-id="98721-136">使用由 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 扩展方法在服务容器中注册用户身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="98721-136">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication) package.</span></span> <span data-ttu-id="98721-137">此方法设置应用与 Identity 提供者 (IP) 交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="98721-137">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="98721-138">对于新应用，请为以下配置中的 `{AUTHORITY}` 和 `{CLIENT ID}` 占位符提供值。</span><span class="sxs-lookup"><span data-stu-id="98721-138">For a new app, provide values for the `{AUTHORITY}` and `{CLIENT ID}` placeholders in the following configuration.</span></span> <span data-ttu-id="98721-139">提供与应用的 IP 一起使用所需的其他配置值。</span><span class="sxs-lookup"><span data-stu-id="98721-139">Provide other configuration values that are required for use with the app's IP.</span></span> <span data-ttu-id="98721-140">例如用于 Google 的相关值，它需要 `PostLogoutRedirectUri`、`RedirectUri` 和 `ResponseType`。</span><span class="sxs-lookup"><span data-stu-id="98721-140">The example is for Google, which requires `PostLogoutRedirectUri`, `RedirectUri`, and `ResponseType`.</span></span> <span data-ttu-id="98721-141">如果向应用添加身份验证，请使用占位符值和其他配置值将以下代码和配置手动添加到应用中。</span><span class="sxs-lookup"><span data-stu-id="98721-141">If adding authentication to an app, manually add the following code and configuration to the app with values for the placeholders and other configuration values.</span></span>

<span data-ttu-id="98721-142">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="98721-142">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="98721-143">配置由 `wwwroot/appsettings.json` 文件提供：</span><span class="sxs-lookup"><span data-stu-id="98721-143">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
  "Local": {
    "Authority": "{AUTHORITY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

<span data-ttu-id="98721-144">Google OAuth 2.0 OIDC 示例：</span><span class="sxs-lookup"><span data-stu-id="98721-144">Google OAuth 2.0 OIDC example:</span></span>

```json
{
  "Local": {
    "Authority": "https://accounts.google.com/",
    "ClientId": "2.......7-e.....................q.apps.googleusercontent.com",
    "PostLogoutRedirectUri": "https://localhost:5001/authentication/logout-callback",
    "RedirectUri": "https://localhost:5001/authentication/login-callback",
    "ResponseType": "id_token"
  }
}
```

<span data-ttu-id="98721-145">重定向 URI (`https://localhost:5001/authentication/login-callback`) 在 [Google API 控制台](https://console.developers.google.com/apis/dashboard)的“凭据” > `{NAME}` > “授权重定向 URI” 中注册，其中 `{NAME}` 是 Google API 控制台的“OAuth 2.0 客户端 ID”应用列表中的应用客户端名称。</span><span class="sxs-lookup"><span data-stu-id="98721-145">The redirect URI (`https://localhost:5001/authentication/login-callback`) is registered in the [Google APIs console](https://console.developers.google.com/apis/dashboard) in **Credentials** > **`{NAME}`** > **Authorized redirect URIs**, where `{NAME}` is the app's client name in the **OAuth 2.0 Client IDs** app list of the Google APIs console.</span></span>

<span data-ttu-id="98721-146">使用 OpenID Connect (OIDC) 提供对独立应用的身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="98721-146">Authentication support for standalone apps is offered using OpenID Connect (OIDC).</span></span> <span data-ttu-id="98721-147"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 方法接受回叫，以配置使用 OIDC 验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="98721-147">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="98721-148">可以从 OIDC 兼容的 IP 中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="98721-148">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="98721-149">注册应用时获取值，通常在其在线门户中执行此操作。</span><span class="sxs-lookup"><span data-stu-id="98721-149">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="98721-150">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="98721-150">Access token scopes</span></span>

<span data-ttu-id="98721-151">Blazor WebAssembly 模板自动为 `openid` 和 `profile` 配置默认作用域。</span><span class="sxs-lookup"><span data-stu-id="98721-151">The Blazor WebAssembly template automatically configures default scopes for `openid` and `profile`.</span></span>

<span data-ttu-id="98721-152">Blazor WebAssembly 模板不会自动将应用配置为请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="98721-152">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="98721-153">若要将访问令牌预配为登录流的一部分，请将范围添加到 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> 的默认令牌范围中。</span><span class="sxs-lookup"><span data-stu-id="98721-153">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>.</span></span> <span data-ttu-id="98721-154">如果向应用添加身份验证，请手动添加以下代码并配置范围 URI。</span><span class="sxs-lookup"><span data-stu-id="98721-154">If adding authentication to an app, manually add the following code and configure the scope URI.</span></span>

<span data-ttu-id="98721-155">`Program.cs`:</span><span class="sxs-lookup"><span data-stu-id="98721-155">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

<span data-ttu-id="98721-156">有关详细信息，请参阅“其他方案”一文的以下部分：</span><span class="sxs-lookup"><span data-stu-id="98721-156">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="98721-157">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="98721-157">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="98721-158">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="98721-158">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="98721-159">导入文件</span><span class="sxs-lookup"><span data-stu-id="98721-159">Imports file</span></span>

[!INCLUDE[](~/blazor/includes/security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="98721-160">索引页</span><span class="sxs-lookup"><span data-stu-id="98721-160">Index page</span></span>

[!INCLUDE[](~/blazor/includes/security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="98721-161">应用组件</span><span class="sxs-lookup"><span data-stu-id="98721-161">App component</span></span>

[!INCLUDE[](~/blazor/includes/security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="98721-162">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="98721-162">RedirectToLogin component</span></span>

[!INCLUDE[](~/blazor/includes/security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="98721-163">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="98721-163">LoginDisplay component</span></span>

<span data-ttu-id="98721-164">`LoginDisplay` 组件 (`Shared/LoginDisplay.razor`) 在 `MainLayout` 组件 (`Shared/MainLayout.razor`) 中呈现并管理以下行为：</span><span class="sxs-lookup"><span data-stu-id="98721-164">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="98721-165">对于经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="98721-165">For authenticated users:</span></span>
  * <span data-ttu-id="98721-166">显示当前用户名。</span><span class="sxs-lookup"><span data-stu-id="98721-166">Displays the current username.</span></span>
  * <span data-ttu-id="98721-167">提供用于注销应用的按钮。</span><span class="sxs-lookup"><span data-stu-id="98721-167">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="98721-168">对于匿名用户，提供登录选项。</span><span class="sxs-lookup"><span data-stu-id="98721-168">For anonymous users, offers the option to log in.</span></span>

```razor
@using Microsoft.AspNetCore.Components.Authorization
@using Microsoft.AspNetCore.Components.WebAssembly.Authentication
@inject NavigationManager Navigation
@inject SignOutSessionStateManager SignOutManager

<AuthorizeView>
    <Authorized>
        Hello, @context.User.Identity.Name!
        <button class="nav-link btn btn-link" @onclick="BeginSignOut">
            Log out
        </button>
    </Authorized>
    <NotAuthorized>
        <a href="authentication/login">Log in</a>
    </NotAuthorized>
</AuthorizeView>

@code {
    private async Task BeginSignOut(MouseEventArgs args)
    {
        await SignOutManager.SetSignOutState();
        Navigation.NavigateTo("authentication/logout");
    }
}
```

## <a name="authentication-component"></a><span data-ttu-id="98721-169">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="98721-169">Authentication component</span></span>

[!INCLUDE[](~/blazor/includes/security/authentication-component.md)]

[!INCLUDE[](~/blazor/includes/security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="98721-170">其他资源</span><span class="sxs-lookup"><span data-stu-id="98721-170">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="98721-171">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="98721-171">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <span data-ttu-id="98721-172"><xref:host-and-deploy/proxy-load-balancer>：包含有关以下内容的指导：</span><span class="sxs-lookup"><span data-stu-id="98721-172"><xref:host-and-deploy/proxy-load-balancer>: Includes guidance on:</span></span>
  * <span data-ttu-id="98721-173">使用转接头中间件跨代理服务器和内部网络保留 HTTPS 方案信息。</span><span class="sxs-lookup"><span data-stu-id="98721-173">Using Forwarded Headers Middleware to preserve HTTPS scheme information across proxy servers and internal networks.</span></span>
  * <span data-ttu-id="98721-174">其他方案和用例，包括手动方案配置、请求路径更改以进行正确请求路由，以及转发适用于 Linux 和非 IIS 反向代理的请求方案。</span><span class="sxs-lookup"><span data-stu-id="98721-174">Additional scenarios and use cases, including manual scheme configuration, request path changes for correct request routing, and forwarding the request scheme for Linux and non-IIS reverse proxies.</span></span>
