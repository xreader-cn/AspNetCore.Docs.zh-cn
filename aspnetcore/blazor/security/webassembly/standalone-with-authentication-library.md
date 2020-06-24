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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/webassembly/standalone-with-authentication-library
ms.openlocfilehash: ba6b3a333a021184ad8a42d6292915e908cc6eb7
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85103216"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>使用身份验证库保护 ASP.NET Core Blazor WebAssembly 独立应用

作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)

对于 Azure Active Directory (AAD) 和 Azure Active Directory B2C (AAD B2C)，请勿按照本主题中的指南进行操作。** 请参阅此目录节点中的 AAD 和 AAD B2C 主题。

要创建使用 [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 库的 Blazor WebAssembly 独立应用，请在命令行界面中执行以下命令：

```dotnetcli
dotnet new blazorwasm -au Individual
```

要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径（例如 `-o BlazorSample`）的输出选项。 该文件夹名称还会成为项目名称的一部分。

在 Visual Studio 中，[创建 Blazor WebAssembly 应用](xref:blazor/get-started)。 使用“存储应用内的用户帐户”选项将“身份验证”设置为“个人用户帐户”**** **** ****。

## <a name="authentication-package"></a>身份验证包

创建应用以使用个人用户帐户时，该应用会在其项目文件中自动接收 [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 包的包引用。 此包提供了一组基元，可帮助应用验证用户身份并获取令牌以调用受保护的 API。

如果向应用添加身份验证，请手动将包添加到应用的项目文件中：

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a>身份验证服务支持

使用由 [Microsoft.AspNetCore.Components.WebAssembly.Authentication](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/) 包提供的 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 扩展方法在服务容器中注册对用户进行身份验证的支持。 此方法设置应用与 Identity 提供者 (IP) 交互所需的服务。

Program.cs**:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

配置由 wwwroot/appsettings.json 文件提供**：

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

使用 Open ID Connect (OIDC) 提供对独立应用的身份验证支持。 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> 方法接受回叫，以配置使用 OIDC 验证应用所需的参数。 可以从 OIDC 兼容的 IP 中获取配置应用所需的值。 注册应用时获取值，通常在其在线门户中执行此操作。

## <a name="access-token-scopes"></a>访问令牌作用域

Blazor WebAssembly 模板不会自动将应用配置为请求安全 API 的访问令牌。 要将访问令牌作为登录流程的一部分进行预配，请将作用域添加到 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> 的默认令牌作用域中：

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

有关详细信息，请参阅“其他方案”一文的以下部分**：

* [请求其他访问令牌](xref:blazor/security/webassembly/additional-scenarios#request-additional-access-tokens)
* [将令牌附加到传出请求](xref:blazor/security/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>导入文件

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>索引页

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>应用组件

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 组件

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 组件

`LoginDisplay` 组件（Shared/LoginDisplay.razor**）在 `MainLayout` 组件（Shared/MainLayout.razor**）中呈现，并管理以下行为：

* 对于经过身份验证的用户：
  * 显示当前用户名。
  * 提供用于注销应用的按钮。
* 对于匿名用户，提供登录选项。

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

## <a name="authentication-component"></a>身份验证组件

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* <xref:blazor/security/webassembly/additional-scenarios>
* [使用安全默认客户端的应用中未经身份验证或未经授权的 Web API 请求](xref:blazor/security/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
