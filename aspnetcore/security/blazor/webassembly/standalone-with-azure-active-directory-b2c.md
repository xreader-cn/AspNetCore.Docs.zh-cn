---
title: Blazor使用 Azure Active Directory B2C 保护 ASP.NET Core WebAssembly 独立应用
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
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: ec35614e3bc4b5b6422b254dfe579c1cb7ca8310
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724388"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>Blazor使用 Azure Active Directory B2C 保护 ASP.NET Core WebAssembly 独立应用

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

创建 Blazor 使用[AZURE ACTIVE DIRECTORY （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 WebAssembly 独立应用程序：

按照以下主题中的指导在 Azure 门户中创建租户并注册 web 应用：

[创建 AAD B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)

记录以下信息：

* AAD B2C 实例（例如， `https://contoso.b2clogin.com/` 包括尾部斜杠）。
* AAD B2C 租户域（例如 `contoso.onmicrosoft.com` ）。

按照[教程：将应用程序注册到 Azure Active Directory B2C 中](/azure/active-directory-b2c/tutorial-register-applications)的指导再次注册*客户端应用*程序的 AAD 应用程序，然后执行以下操作：

1. 在**Azure Active Directory**  >  **应用注册**中，选择 "**新建注册**"。
1. 提供应用的**名称**（例如， ** Blazor 独立 AAD B2C**）。
1. 对于 "**支持的帐户类型**"，请选择 "多租户" 选项：**任何组织目录或任何标识提供者中的帐户。用于对具有 Azure AD B2C 的用户进行身份验证。**
1. 将 "**重定向 uri** " 下拉菜单保留设置为 " **Web** "，并提供以下重定向 uri： `https://localhost:{PORT}/authentication/login-callback` 。 在 Kestrel 上运行的应用的默认端口为5001。 如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。 对于 IIS Express，可以在 "**调试**" 面板的应用程序属性中找到应用程序的随机生成端口。 由于此时应用不存在，并且 IIS Express 端口未知，因此在创建应用后返回到此步骤并更新重定向 URI。 本主题稍后会显示一个批注，以提醒 IIS Express 用户更新重定向 URI。
1. 确认**权限**  >  **授予了对 openid 的管理员同意并启用 offline_access 权限**。
1. 选择“注册”。

记录应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111` ）。

在 "**身份验证**  >  **平台配置**"  >  **Web**：

1. 确认存在的**重定向 URI** `https://localhost:{PORT}/authentication/login-callback` 。
1. 对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。
1. 此体验可接受应用的其余默认值。
1. 选择“保存”按钮  。

在**家庭**  >  **Azure AD B2C**  >  **用户流**：

[创建注册和登录用户流](/azure/active-directory-b2c/tutorial-create-user-flows)

至少，选择 "**应用程序声明**  >  **显示名称**用户" 属性以 `context.User.Identity.Name` 在组件中填充 `LoginDisplay` （*Shared/LoginDisplay*）。

记录为应用创建的注册和登录用户流名称（例如 `B2C_1_signupsignin` ）。

将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：

```dotnetcli
dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{TENANT DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
```

若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。 文件夹名称还会成为项目名称的一部分。

> [!NOTE]
> 在 Azure 门户中，将为在**Authentication**  >  **Platform configurations**  >  **Web**  >  Kestrel 服务器上运行的应用配置默认设置，为端口5001配置应用的身份验证平台配置 Web**重定向 URI** 。
>
> 如果应用在随机 IIS Express 端口上运行，则可在 "**调试**" 面板的应用属性中找到应用的端口。
>
> 如果先前未将此端口配置为应用的已知端口，请返回到 Azure 门户中的应用注册，并更新具有正确端口的重定向 URI。

创建应用后，应该能够：

* 使用 AAD 用户帐户登录到应用。
* 请求 Microsoft Api 的访问令牌。 有关详细信息，请参阅：
  * [访问令牌范围](#access-token-scopes)
  * [快速入门：将应用程序配置为公开 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。

## <a name="authentication-package"></a>身份验证包

当创建应用以使用单个 B2C 帐户（ `IndividualB2C` ）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（[WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)）的包引用。 包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。

如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

[WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)包可向应用程序中添加[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包，并将其添加到应用中。

## <a name="authentication-service-support"></a>身份验证服务支持

使用 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。 此方法设置应用与 Identity 提供程序（IP）交互所需的所有服务。

Program.cs:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法接受回调，以配置对应用进行身份验证所需的参数。 注册应用时，可以从 AAD 配置获取配置应用所需的值。

配置由文件上的*wwwroot/appsettings.js*提供：

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": false
  }
}
```

示例：

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": false
  }
}
```

## <a name="access-token-scopes"></a>访问令牌范围

BlazorWebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。 若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌作用域中 <xref:Microsoft.Authentication.WebAssembly.Msal.Models.MsalProviderOptions> ：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

[!INCLUDE[](~/includes/blazor-security/azure-scope.md)]

有关详细信息，请参阅*其他方案*一文中的以下部分：

* [请求其他访问令牌](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* [将令牌附加到传出请求](xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests)

## <a name="imports-file"></a>导入文件

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>索引页面

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>应用组件

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>RedirectToLogin 组件

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>LoginDisplay 组件

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>身份验证组件

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* <xref:security/blazor/webassembly/additional-scenarios>
* [使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/authentication/azure-ad-b2c>
* [教程：创建 Azure Active Directory B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft 标识平台文档](/azure/active-directory/develop/)
