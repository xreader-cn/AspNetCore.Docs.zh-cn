---
title: 使用身份验证库保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: be87257c5f901e9b3d1ba6a8d7c6b811419c433f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402190"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a><span data-ttu-id="c26e7-102">使用身份验证库保护 ASP.NET Core Blazor WebAssembly 独立应用</span><span class="sxs-lookup"><span data-stu-id="c26e7-102">Secure an ASP.NET Core Blazor WebAssembly standalone app with the Authentication library</span></span>

<span data-ttu-id="c26e7-103">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="c26e7-103">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="c26e7-104">对于 Azure Active Directory (AAD) 和 Azure Active Directory B2C (AAD B2C)，请勿按照本主题中的指南进行操作。请参阅此目录节点中的 AAD 和 AAD B2C 主题。</span><span class="sxs-lookup"><span data-stu-id="c26e7-104">*For Azure Active Directory (AAD) and Azure Active Directory B2C (AAD B2C), don't follow the guidance in this topic. See the AAD and AAD B2C topics in this table of contents node.*</span></span>

<span data-ttu-id="c26e7-105">要创建使用 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 库的 Blazor WebAssembly 独立应用，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="c26e7-105">To create a Blazor WebAssembly standalone app that uses [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) library, execute the following command in a command shell:</span></span>

```dotnetcli
dotnet new blazorwasm -au Individual
```

<span data-ttu-id="c26e7-106">要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径（例如 `-o BlazorSample`）的输出选项。</span><span class="sxs-lookup"><span data-stu-id="c26e7-106">To specify the output location, which creates a project folder if it doesn't exist, include the output option in the command with a path (for example, `-o BlazorSample`).</span></span> <span data-ttu-id="c26e7-107">该文件夹名称还会成为项目名称的一部分。</span><span class="sxs-lookup"><span data-stu-id="c26e7-107">The folder name also becomes part of the project's name.</span></span>

<span data-ttu-id="c26e7-108">在 Visual Studio 中，[创建 Blazor WebAssembly 应用](xref:blazor/get-started)。</span><span class="sxs-lookup"><span data-stu-id="c26e7-108">In Visual Studio, [create a Blazor WebAssembly app](xref:blazor/get-started).</span></span> <span data-ttu-id="c26e7-109">使用“存储应用内的用户帐户”选项将“身份验证”设置为“个人用户帐户”  。</span><span class="sxs-lookup"><span data-stu-id="c26e7-109">Set **Authentication** to **Individual User Accounts** with the **Store user accounts in-app** option.</span></span>

## <a name="authentication-package"></a><span data-ttu-id="c26e7-110">身份验证包</span><span class="sxs-lookup"><span data-stu-id="c26e7-110">Authentication package</span></span>

<span data-ttu-id="c26e7-111">创建应用以使用个人用户帐户时，该应用会在其项目文件中自动接收 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="c26e7-111">When an app is created to use Individual User Accounts, the app automatically receives a package reference for the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package in the app's project file.</span></span> <span data-ttu-id="c26e7-112">此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。</span><span class="sxs-lookup"><span data-stu-id="c26e7-112">The package provides a set of primitives that help the app authenticate users and obtain tokens to call protected APIs.</span></span>

<span data-ttu-id="c26e7-113">如果向应用添加身份验证，请手动将包添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="c26e7-113">If adding authentication to an app, manually add the package to the app's project file:</span></span>

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a><span data-ttu-id="c26e7-114">身份验证服务支持</span><span class="sxs-lookup"><span data-stu-id="c26e7-114">Authentication service support</span></span>

<span data-ttu-id="c26e7-115">使用由 [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 扩展方法在服务容器中注册用户身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="c26e7-115">Support for authenticating users is registered in the service container with the <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> extension method provided by the [`Microsoft.AspNetCore.Components.WebAssembly.Authentication`](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) package.</span></span> <span data-ttu-id="c26e7-116">此方法设置应用与 Identity 提供者 (IP) 交互所需的服务。</span><span class="sxs-lookup"><span data-stu-id="c26e7-116">This method sets up the services required for the app to interact with the Identity Provider (IP).</span></span>

<span data-ttu-id="c26e7-117">`Program.cs`：</span><span class="sxs-lookup"><span data-stu-id="c26e7-117">`Program.cs`:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

<span data-ttu-id="c26e7-118">配置由 `wwwroot/appsettings.json` 文件提供：</span><span class="sxs-lookup"><span data-stu-id="c26e7-118">Configuration is supplied by the `wwwroot/appsettings.json` file:</span></span>

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

<span data-ttu-id="c26e7-119">使用 Open ID Connect (OIDC) 提供对独立应用的身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="c26e7-119">Authentication support for standalone apps is offered using Open ID Connect (OIDC).</span></span> <span data-ttu-id="c26e7-120"><xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 方法接受回叫，以配置使用 OIDC 验证应用所需的参数。</span><span class="sxs-lookup"><span data-stu-id="c26e7-120">The <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> method accepts a callback to configure the parameters required to authenticate an app using OIDC.</span></span> <span data-ttu-id="c26e7-121">可以从 OIDC 兼容的 IP 中获取配置应用所需的值。</span><span class="sxs-lookup"><span data-stu-id="c26e7-121">The values required for configuring the app can be obtained from the OIDC-compliant IP.</span></span> <span data-ttu-id="c26e7-122">注册应用时获取值，通常在其在线门户中执行此操作。</span><span class="sxs-lookup"><span data-stu-id="c26e7-122">Obtain the values when you register the app, which typically occurs in their online portal.</span></span>

## <a name="access-token-scopes"></a><span data-ttu-id="c26e7-123">访问令牌作用域</span><span class="sxs-lookup"><span data-stu-id="c26e7-123">Access token scopes</span></span>

<span data-ttu-id="c26e7-124">Blazor WebAssembly 模板不会自动将应用配置为请求安全 API 的访问令牌。</span><span class="sxs-lookup"><span data-stu-id="c26e7-124">The Blazor WebAssembly template doesn't automatically configure the app to request an access token for a secure API.</span></span> <span data-ttu-id="c26e7-125">要将访问令牌作为登录流程的一部分进行预配，请将作用域添加到 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> 的默认令牌作用域中：</span><span class="sxs-lookup"><span data-stu-id="c26e7-125">To provision an access token as part of the sign-in flow, add the scope to the default token scopes of the <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions>:</span></span>

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

<span data-ttu-id="c26e7-126">有关详细信息，请参阅“其他方案”一文的以下部分：</span><span class="sxs-lookup"><span data-stu-id="c26e7-126">For more information, see the following sections of the *Additional scenarios* article:</span></span>

* [<span data-ttu-id="c26e7-127">请求其他访问令牌</span><span class="sxs-lookup"><span data-stu-id="c26e7-127">Request additional access tokens</span></span>](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [<span data-ttu-id="c26e7-128">将令牌附加到传出请求</span><span class="sxs-lookup"><span data-stu-id="c26e7-128">Attach tokens to outgoing requests</span></span>](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a><span data-ttu-id="c26e7-129">导入文件</span><span class="sxs-lookup"><span data-stu-id="c26e7-129">Imports file</span></span>

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a><span data-ttu-id="c26e7-130">索引页</span><span class="sxs-lookup"><span data-stu-id="c26e7-130">Index page</span></span>

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a><span data-ttu-id="c26e7-131">应用组件</span><span class="sxs-lookup"><span data-stu-id="c26e7-131">App component</span></span>

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a><span data-ttu-id="c26e7-132">RedirectToLogin 组件</span><span class="sxs-lookup"><span data-stu-id="c26e7-132">RedirectToLogin component</span></span>

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a><span data-ttu-id="c26e7-133">LoginDisplay 组件</span><span class="sxs-lookup"><span data-stu-id="c26e7-133">LoginDisplay component</span></span>

<span data-ttu-id="c26e7-134">`LoginDisplay` 组件 (`Shared/LoginDisplay.razor`) 在 `MainLayout` 组件 (`Shared/MainLayout.razor`) 中呈现并管理以下行为：</span><span class="sxs-lookup"><span data-stu-id="c26e7-134">The `LoginDisplay` component (`Shared/LoginDisplay.razor`) is rendered in the `MainLayout` component (`Shared/MainLayout.razor`) and manages the following behaviors:</span></span>

* <span data-ttu-id="c26e7-135">对于经过身份验证的用户：</span><span class="sxs-lookup"><span data-stu-id="c26e7-135">For authenticated users:</span></span>
  * <span data-ttu-id="c26e7-136">显示当前用户名。</span><span class="sxs-lookup"><span data-stu-id="c26e7-136">Displays the current username.</span></span>
  * <span data-ttu-id="c26e7-137">提供用于注销应用的按钮。</span><span class="sxs-lookup"><span data-stu-id="c26e7-137">Offers a button to log out of the app.</span></span>
* <span data-ttu-id="c26e7-138">对于匿名用户，提供登录选项。</span><span class="sxs-lookup"><span data-stu-id="c26e7-138">For anonymous users, offers the option to log in.</span></span>

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

## <a name="authentication-component"></a><span data-ttu-id="c26e7-139">身份验证组件</span><span class="sxs-lookup"><span data-stu-id="c26e7-139">Authentication component</span></span>

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a><span data-ttu-id="c26e7-140">其他资源</span><span class="sxs-lookup"><span data-stu-id="c26e7-140">Additional resources</span></span>

* <xref:blazor/security/webassembly/additional-scenarios>
* [<span data-ttu-id="c26e7-141">使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求</span><span class="sxs-lookup"><span data-stu-id="c26e7-141">Unauthenticated or unauthorized web API requests in an app with a secure default client</span></span>](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
