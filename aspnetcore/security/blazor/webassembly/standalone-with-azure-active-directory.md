---
title: 使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory
ms.openlocfilehash: 76bcac29d86a236e56c0eaea24a694c4845ecbcf
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083583"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory"></a>使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 独立应用

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

创建使用[Azure Active Directory （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的 Blazor WebAssembly 独立应用程序：

[创建 AAD 租户和 web 应用程序](/azure/active-directory/develop/v2-overview)：

在 Azure 门户的**Azure Active Directory** > **应用注册**区域中注册 AAD 应用：

1. 提供应用的**名称**（例如， **Blazor 客户端 AAD**）。
1. 选择**受支持的帐户类型**。 你**只能在此组织目录中选择帐户**。
1. 将 "**重定向 uri** " 下拉集保持设置为 " **Web**"，并提供 `https://localhost:5001/authentication/login-callback`的重定向 uri。
1. 禁用 "**权限** > **向 openid 和 offline_access 权限授予管理员**权限" 复选框。
1. 选择“注册”。

在 "**身份验证**" > **平台配置** > **Web**：

1. 确认存在 `https://localhost:5001/authentication/login-callback` 的**重定向 URI** 。
1. 对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。
1. 此体验可接受应用的其余默认值。
1. 选择“保存”按钮。

记录以下信息：

* 应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111`）
* 目录 ID （租户 ID）（例如 `22222222-2222-2222-2222-222222222222`）

将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "{TENANT ID}"
```

若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。 文件夹名称还会成为项目名称的一部分。

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

Program.cs:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
});
```

`AddMsalAuthentication` 方法接受回调，以配置对应用进行身份验证所需的参数。 注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。

Blazor WebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。 若要将令牌预配为登录流的一部分，请将该作用域添加到 `MsalProviderOptions`的默认访问令牌作用域中：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> 默认访问令牌范围必须是 `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` 格式（例如，`11111111-1111-1111-1111-111111111111/API.Access`）。 如果向范围设置提供方案或方案和主机（如 Azure 门户中所示），则当*客户端应用*收到来自*服务器 API 应用*的*401 未经授权*响应时，将引发未处理的异常。

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

* <xref:security/authentication/azure-active-directory/index>
