---
title: Blazor使用身份验证库保护 ASP.NET Core WebAssembly 独立应用
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
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: efd63c2fc533175b034b8311c2b7a6b9e6f4375b
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "83851095"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>Blazor使用身份验证库保护 ASP.NET Core WebAssembly 独立应用

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

*对于 Azure Active Directory （AAD）和 Azure Active Directory B2C （AAD B2C），请勿按照本主题中的指导进行操作。请参阅此目录节点中的 AAD 和 AAD B2C 主题。*

若要创建 Blazor 使用[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)库的 WebAssembly 独立应用程序，请在命令行界面中执行以下命令：

```dotnetcli
dotnet new blazorwasm -au Individual
```

若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。 文件夹名称还会成为项目名称的一部分。

在 Visual Studio 中，[创建一个 Blazor WebAssembly 应用](xref:blazor/get-started)。 将**身份验证**设置为具有 "**应用商店用户帐户应用内**" 选项的**单个用户帐户**。

## <a name="authentication-package"></a>身份验证包

当创建应用程序以使用单个用户帐户时，应用程序会自动在应用程序的项目文件中接收[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包的包引用。 包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。

如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：

```xml
<PackageReference 
  Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
  Version="3.2.0" />
```

## <a name="authentication-service-support"></a>身份验证服务支持

使用 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A> [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。 此方法设置应用程序与 Identity 提供程序（IP）进行交互所需的服务。

Program.cs:

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    builder.Configuration.Bind("Local", options.ProviderOptions);
});
```

配置由*wwwroot/appsettings*文件提供：

```json
{
    "Local": {
        "Authority": "{AUTHORITY}",
        "ClientId": "{CLIENT ID}"
    }
}
```

使用 Open ID Connect （OIDC）提供对独立应用程序的身份验证支持。 <xref:Microsoft.Extensions.DependencyInjection.WebAssemblyAuthenticationServiceCollectionExtensions.AddOidcAuthentication%2A>方法接受回调来配置使用 OIDC 对应用进行身份验证所需的参数。 配置应用所需的值可以从 OIDC 兼容的 IP 获得。 注册应用时获取值，此操作通常发生在其联机门户中。

## <a name="access-token-scopes"></a>访问令牌范围

BlazorWebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。 若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认令牌范围中 <xref:Microsoft.AspNetCore.Components.WebAssembly.Authentication.OidcProviderOptions> ：

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

有关详细信息，请参阅*其他方案*一文中的以下部分：

* [请求其他访问令牌](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [将令牌附加到传出请求](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>导入文件

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>索引页面

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>应用组件

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 组件

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 组件

`LoginDisplay`组件（*Shared/LoginDisplay*）在 `MainLayout` 组件（*shared/MainLayout*）中呈现并管理以下行为：

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

* <xref:security/blazor/webassembly/additional-scenarios>
* [使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
