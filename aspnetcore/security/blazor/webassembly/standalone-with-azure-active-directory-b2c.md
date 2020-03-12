---
title: 使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 0ea42943c908d8cf9d083c1cfc568c1835588ce9
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083655"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 保护 ASP.NET Core Blazor WebAssembly 独立应用

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

创建使用[Azure Active Directory （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 Blazor WebAssembly 独立应用程序：

1. 按照以下主题中的指导在 Azure 门户中创建租户并注册 web 应用：

   * [创建 AAD B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)&ndash; 记录以下信息：

     1 \。 AAD B2C 实例（例如，`https://contoso.b2clogin.com/`，其中包含尾随斜杠）<br>
     2。 AAD B2C 租户域（例如，`contoso.onmicrosoft.com`）

   * [注册 web 应用程序](/azure/active-directory-b2c/tutorial-register-applications)&ndash; 在应用注册过程中进行以下选择：

     1 \。 将**Web 应用/WEB API**设置为 **"是"** 。<br>
     2。 将 "**允许隐式流**" 设置为 **"是"** 。<br>
     3。 添加 `https://localhost:5001/authentication/login-callback`的**回复 URL** 。

     记录应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111`）。

   * [创建 & 的用户流](/azure/active-directory-b2c/tutorial-create-user-flows)创建注册和登录用户流。

     记录为应用创建的注册和登录用户流名称（例如 `B2C_1_signupsignin`）。

1. 将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。 文件夹名称还会成为项目名称的一部分。

## <a name="authentication-package"></a>身份验证包

创建应用以使用单个 B2C 帐户（`IndividualB2C`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。 包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。

如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

将前面的包引用中的 `{VERSION}` 替换为 <xref:blazor/get-started> 一文中所示 `Microsoft.AspNetCore.Blazor.Templates` 包的版本。

`Microsoft.Authentication.WebAssembly.Msal` 包可向应用程序中添加 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包。

## <a name="authentication-service-support"></a>身份验证服务支持

使用 `Microsoft.Authentication.WebAssembly.Msal` 包提供的 `AddMsalAuthentication` 扩展方法在服务容器中注册对用户进行身份验证。 此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。

Program.cs:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = 
        "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}";
    authentication.ClientId = "{CLIENT ID}";
    authentication.ValidateAuthority = false;
});
```

`AddMsalAuthentication` 方法接受回调，以配置对应用进行身份验证所需的参数。 注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。

Blazor WebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。 若要将令牌预配为登录流的一部分，请将该作用域添加到 `MsalProviderOptions`的默认访问令牌作用域中：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{API ID URI}/{SCOPE}");
});
```

## <a name="index-page"></a>索引页

[!INCLUDE[](~/includes/blazor-security/index-page.md)]

## <a name="app-component"></a>应用组件

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 组件

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 组件

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>身份验证组件

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* <xref:security/authentication/azure-ad-b2c>
* [教程：创建 Azure Active Directory B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)
