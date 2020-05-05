---
title: 使用身份验证Blazor库保护 ASP.NET Core WebAssembly 独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/24/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 6907a1213a6a9089e2aed885093c2fd38f972ad0
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82768047"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-the-authentication-library"></a>使用身份验证Blazor库保护 ASP.NET Core WebAssembly 独立应用

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*对于 Azure Active Directory （AAD）和 Azure Active Directory B2C （AAD B2C），请勿按照本主题中的指导进行操作。请参阅此目录节点中的 AAD 和 AAD B2C 主题。*

若要创建Blazor使用`Microsoft.AspNetCore.Components.WebAssembly.Authentication`库的 WebAssembly 独立应用程序，请在命令行界面中执行以下命令：

```dotnetcli
dotnet new blazorwasm -au Individual
```

若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如`-o BlazorSample`，）。 文件夹名称还会成为项目名称的一部分。

在 Visual Studio 中，[创建Blazor一个 WebAssembly 应用](xref:blazor/get-started)。 将**身份验证**设置为具有 "**应用商店用户帐户应用内**" 选项的**单个用户帐户**。

## <a name="authentication-package"></a>身份验证包

创建应用以使用单个用户帐户时，应用会在应用的项目文件中自动接收`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包的包引用。 包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。

如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

将`{VERSION}`前面的包引用中的替换为<xref:blazor/get-started>本文中`Microsoft.AspNetCore.Blazor.Templates`所示的包版本。

## <a name="authentication-service-support"></a>身份验证服务支持

使用`AddOidcAuthentication` `Microsoft.AspNetCore.Components.WebAssembly.Authentication`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。 此方法设置应用与Identity提供程序（IP）交互所需的所有服务。

Program.cs  :

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

使用 Open ID Connect （OIDC）提供对独立应用程序的身份验证支持。 `AddOidcAuthentication`方法接受回调来配置使用 OIDC 对应用进行身份验证所需的参数。 配置应用所需的值可以从 OIDC 兼容的 IP 获得。 注册应用时获取值，此操作通常发生在其联机门户中。

## <a name="access-token-scopes"></a>访问令牌范围

Blazor WebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。 若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认令牌范围中`OidcProviderOptions`：

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

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

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>身份验证组件

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* <xref:security/blazor/webassembly/additional-scenarios>
