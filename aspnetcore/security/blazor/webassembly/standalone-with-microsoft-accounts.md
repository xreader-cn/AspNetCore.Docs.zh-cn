---
title: 使用 Microsoft 帐户保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-microsoft-accounts
ms.openlocfilehash: be73bec971f96bd64afc735a1ea750d47c7bc383
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219254"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-microsoft-accounts"></a>使用 Microsoft 帐户保护 ASP.NET Core Blazor WebAssembly 独立应用

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

若要创建一个使用[Microsoft Azure Active Directory 帐户](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)进行身份验证的 Blazor WebAssembly 独立应用，请执行以下操作：

1. [创建 AAD 租户和 web 应用程序](/azure/active-directory/develop/v2-overview)

   在 Azure 门户的**Azure Active Directory** > **应用注册**区域中注册 AAD 应用：

   1\. 提供应用的**名称**（例如， **Blazor 客户端 AAD**）。<br>
   2\. 在 "**支持的帐户类型**" 中，选择**任何组织目录中的帐户**。<br>
   3\. 将 "**重定向 uri** " 下拉集保持设置为 " **Web**"，并提供 `https://localhost:5001/authentication/login-callback`的重定向 uri。<br>
   4\. 禁用 "**权限** > **向 openid 和 offline_access 权限授予管理员**权限" 复选框。<br>
   5\. 选择“注册”。

   在 "**身份验证**" > **平台配置** > **Web**：

   1\. 确认存在 `https://localhost:5001/authentication/login-callback` 的**重定向 URI** 。<br>
   2\. 对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。<br>
   3\. 此体验可接受应用的其余默认值。<br>
   4\. 选择“保存”按钮。

   记录应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111`）。

1. 将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：

   ```dotnetcli
   dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
   ```

   若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。 文件夹名称还会成为项目名称的一部分。

创建应用后，应该能够：

* 使用 Microsoft 帐户登录到应用。
* 使用与独立 Blazor 应用相同的方法为 Microsoft Api 请求访问令牌，前提是已正确配置应用。 有关详细信息，请参阅[快速入门：将应用程序配置为公开 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。

## <a name="authentication-package"></a>身份验证包

创建应用以使用工作或学校帐户（`SingleOrg`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。 包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。

如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

将前面的包引用中的 `{VERSION}` 替换为 <xref:blazor/get-started> 一文中所示 `Microsoft.AspNetCore.Blazor.Templates` 包的版本。

`Microsoft.Authentication.WebAssembly.Msal` 包可向应用程序中添加 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包。

## <a name="authentication-service-support"></a>身份验证服务支持

使用 `Microsoft.Authentication.WebAssembly.Msal` 包提供的 `AddMsalAuthentication` 扩展方法在服务容器中注册对用户进行身份验证。 此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。

Program.cs：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "{AUTHORITY}";
    authentication.ClientId = "{CLIENT ID}";
});
```

`AddMsalAuthentication` 方法接受回调，以配置对应用进行身份验证所需的参数。 注册应用时，可以从 Microsoft 帐户配置获取配置该应用所需的值。

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

* [快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [快速入门：将应用程序配置为公开 web Api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
