---
title: 使用 Microsoft 帐户Blazor保护 ASP.NET Core WebAssembly 独立应用程序
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: a12cc8f94a97882e4a0ac3a6553628df4da2e82c
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111183"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>使用 Microsoft 帐户Blazor保护 ASP.NET Core WebAssembly 独立应用程序

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

> [!NOTE]
> 本文中的指南适用于 ASP.NET Core 3.2 预览版4。 本主题将更新到2005年4月24日星期五的预览版5。

若要创建Blazor将 Microsoft 帐户用于身份验证[Azure Active Directory （AAD）](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)的 WebAssembly 独立应用程序，请执行以下操作：

1. [创建 AAD 租户和 web 应用程序](/azure/active-directory/develop/v2-overview)

   在 Azure 门户的**Azure Active Directory** > **应用注册**区域中注册 AAD 应用程序：

   1 \。 提供应用的**名称**（例如， ** Blazor客户端 AAD**）。<br>
   2。 在 "**支持的帐户类型**" 中，选择**任何组织目录中的帐户**。<br>
   3。 将 "**重定向 uri** " 下拉状态设置为 " **Web**"，并提供`https://localhost:5001/authentication/login-callback`的重定向 uri。<br>
   4 \。 禁用 "**将** > **管理员以免授予 openid 并 offline_access 权限**" 复选框。<br>
   5 \。 选择“注册”  。

   在 "**身份验证** > **平台配置** > "**Web**：

   1 \。 确认存在的**重定向 URI** `https://localhost:5001/authentication/login-callback` 。<br>
   2。 对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。<br>
   3。 此体验可接受应用的其余默认值。<br>
   4 \。 选择 "**保存**" 按钮。

   记录应用程序 ID （客户端 ID）（例如`11111111-1111-1111-1111-111111111111`）。

1. 将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如`-o BlazorSample`，）。 文件夹名称还会成为项目名称的一部分。

创建应用后，应该能够：

* 使用 Microsoft 帐户登录到应用。
* 如果已正确配置应用，请使用与独立Blazor应用相同的方法为 Microsoft api 请求访问令牌。 有关详细信息，请参阅[快速入门：将应用程序配置为公开 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。

## <a name="authentication-package"></a>身份验证包

创建应用以使用工作或学校帐户（`SingleOrg`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。 包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。

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
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

`AddMsalAuthentication`方法接受回调，以配置对应用进行身份验证所需的参数。 注册应用时，可以从 Microsoft 帐户配置获取配置该应用所需的值。

## <a name="access-token-scopes"></a>访问令牌范围

Blazor WebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。 若要将令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌范围中`MsalProviderOptions`：

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

有关详细信息，请参阅 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>。

<!--
    For more information, see <xref:security/blazor/webassembly/additional-scenarios#attach-tokens-to-outgoing-requests>.
-->

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

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* <xref:security/blazor/webassembly/additional-scenarios>
* [快速入门：将应用程序注册到 Microsoft 标识平台](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [快速入门：将应用程序配置为公开 web Api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
