---
title: 使用 Azure Active Directory B2C 保护Blazor ASP.NET Core WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 4ccf86550a520f1d001088859ef5909041178781
ms.sourcegitcommit: 6d271f4b4c3cd1e82267f51d9bfb6de221c394fe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150002"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>使用 Azure Active Directory B2C 保护Blazor ASP.NET Core WebAssembly 独立应用

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

创建使用Blazor [Azure Active Directory （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 WebAssembly 独立应用程序：

1. 按照以下主题中的指导在 Azure 门户中创建租户并注册 web 应用：

   * [创建 AAD B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant) &ndash;记录以下信息：

     1 \。 AAD B2C 实例（例如`https://contoso.b2clogin.com/`，包含尾随斜杠）<br>
     2。 AAD B2C 租户域（例如`contoso.onmicrosoft.com`）

   * [注册 web 应用程序](/azure/active-directory-b2c/tutorial-register-applications) &ndash;在应用注册过程中进行以下选择：

     1 \。 将**Web 应用/WEB API**设置为 **"是"**。<br>
     2。 将 "**允许隐式流**" 设置为 **"是"**。<br>
     3。 添加`https://localhost:5001/authentication/login-callback`的**回复 URL** 。

     记录应用程序 ID （客户端 ID）（例如`11111111-1111-1111-1111-111111111111`）。

   * [创建用户流](/azure/active-directory-b2c/tutorial-create-user-flows) &ndash;创建注册和登录用户流。

     至少，选择 "**应用程序声明** > **显示名称**用户" 属性以`context.User.Identity.Name`在`LoginDisplay`组件中填充（*Shared/LoginDisplay*）。

     记录为应用创建的注册和登录用户流名称（例如`B2C_1_signupsignin`）。

1. 将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如`-o BlazorSample`，）。 文件夹名称还会成为项目名称的一部分。

## <a name="authentication-package"></a>身份验证包

当创建应用以使用单个 B2C 帐户（`IndividualB2C`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。 包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。

如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

将`{VERSION}`前面的包引用中的替换为<xref:blazor/get-started>本文中`Microsoft.AspNetCore.Blazor.Templates`所示的包版本。

`Microsoft.Authentication.WebAssembly.Msal`包可传递将`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包添加到应用。

## <a name="authentication-service-support"></a>身份验证服务支持

使用`AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。 此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。

Program.cs  :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAdB2C", options.ProviderOptions.Authentication);
});
```

`AddMsalAuthentication`方法接受回调，以配置对应用进行身份验证所需的参数。 注册应用时，可以从 Microsoft 帐户配置获取配置该应用所需的值。

配置由*wwwroot/appsettings*文件提供：

```json
{
  "AzureAdB2C": {
    "Authority": "{AAD B2C INSTANCE}{DOMAIN}/{SIGN UP OR SIGN IN POLICY}",
    "ClientId": "{CLIENT ID}"
  }
}
```

示例：

```json
{
  "AzureAdB2C": {
    "Authority": "https://contoso.b2clogin.com/contoso.onmicrosoft.com/B2C_1_signupsignin1",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd"
  }
}
```

## <a name="access-token-scopes"></a>访问令牌范围

Blazor WebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。 若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌作用域中`MsalProviderOptions`：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> 如果 Azure 门户提供了作用域 URI，并且应用在从 API 收到*401 的未经授权*响应时**引发了未处理的异常**，请尝试使用不包括方案和主机的范围 uri。 例如，Azure 门户可能提供以下作用域 URI 格式之一：
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> 提供不含方案和主机的作用域 URI：
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

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
* <xref:security/authentication/azure-ad-b2c>
* [教程：创建 Azure Active Directory B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft 标识平台文档](/azure/active-directory/develop/)
