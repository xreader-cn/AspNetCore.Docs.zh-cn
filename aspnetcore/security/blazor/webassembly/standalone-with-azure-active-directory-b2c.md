---
title: 使用 AzureBlazor活动目录 B2C 保护 ASP.NET核心 Web 组件独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/09/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-azure-active-directory-b2c
ms.openlocfilehash: 96e39a4c975a65fd11776f774fb1799acab525b9
ms.sourcegitcommit: e8dc30453af8bbefcb61857987090d79230a461d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/11/2020
ms.locfileid: "81123457"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-azure-active-directory-b2c"></a>使用 AzureBlazor活动目录 B2C 保护 ASP.NET核心 Web 组件独立应用

哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)和[卢克·莱瑟姆](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

要创建Blazor使用[Azure 活动目录 （AAD） B2C](/azure/active-directory-b2c/overview)进行身份验证的 Web 大会独立应用，请执行：

1. 按照以下主题中的指南创建租户并在 Azure 门户中注册 Web 应用：

   * [创建 AAD B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)&ndash;记录以下信息：

     1\. AAD B2C 实例（例如`https://contoso.b2clogin.com/`，包括尾随斜杠）<br>
     2\. AAD B2C 租户域（例如`contoso.onmicrosoft.com`，

   * [注册 Web 应用程序](/azure/active-directory-b2c/tutorial-register-applications)&ndash;在应用注册期间进行以下选择：

     1\. 将**Web 应用/Web API**设置为 **"是**"。<br>
     2\. 将**允许隐式流**设置为 **"是**"。<br>
     3\. 添加 的`https://localhost:5001/authentication/login-callback`**回复 URL。**

     记录应用程序 ID（客户端 ID）（例如， `11111111-1111-1111-1111-111111111111`

   * [创建用户流](/azure/active-directory-b2c/tutorial-create-user-flows)&ndash;创建注册和登录用户流。

     至少，选择**应用程序声明** > **显示名称**用户属性以填充`context.User.Identity.Name``LoginDisplay`组件中的 （*共享/LoginDisplay.razor）。*

     记录为应用创建的注册和登录用户流名称（例如。 `B2C_1_signupsignin`

1. 将以下命令中的占位符替换为前面记录的信息，并在命令 shell 中执行该命令：

   ```dotnetcli
   dotnet new blazorwasm -au IndividualB2C --aad-b2c-instance "{AAD B2C INSTANCE}" --client-id "{CLIENT ID}" --domain "{DOMAIN}" -ssp "{SIGN UP OR SIGN IN POLICY}"
   ```

   要指定输出位置（如果不存在，则创建项目文件夹）请在命令中包含具有路径的输出选项（例如。 `-o BlazorSample` 文件夹名称也将成为项目名称的一部分。

## <a name="authentication-package"></a>身份验证包

当创建应用以使用单个 B2C 帐户 （）`IndividualB2C`时，应用会自动接收 Microsoft[身份验证库](/azure/active-directory/develop/msal-overview)（）`Microsoft.Authentication.WebAssembly.Msal`的包引用 。 该包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌来调用受保护的 API。

如果向应用添加身份验证，则手动将包添加到应用的项目文件中：

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

在前面`{VERSION}`的包引用中替换为`Microsoft.AspNetCore.Blazor.Templates`<xref:blazor/get-started>本文中显示的包版本。

包`Microsoft.Authentication.WebAssembly.Msal`会临时将`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包添加到应用。

## <a name="authentication-service-support"></a>身份验证服务支持

使用`AddMsalAuthentication``Microsoft.Authentication.WebAssembly.Msal`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。 此方法设置应用与标识提供程序 （IP） 交互所需的所有服务。

*Program.cs*：

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

该方法`AddMsalAuthentication`接受回调以配置验证应用所需的参数。 注册应用时，可以从 Azure 门户 AAD 配置中获取配置应用所需的值。

## <a name="access-token-scopes"></a>访问令牌作用域

WebAssemblyBlazor模板不会自动配置应用以请求安全 API 的访问令牌。 要将令牌预配为登录流的一部分，请将作用域添加到 的`MsalProviderOptions`默认访问令牌作用域中：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

> [!NOTE]
> 如果 Azure 门户提供作用域 URI，并且应用在收到来自 API 的*401 未授权*响应时**引发未处理的异常**，请尝试使用不包括方案和主机的范围 URI。 例如，Azure 门户可以提供以下作用域 URI 格式之一：
>
> * `https://{ORGANIZATION}.onmicrosoft.com/{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
> * `api://{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}`
>
> 提供无方案和主机的范围 URI：
>
> ```csharp
> options.ProviderOptions.DefaultAccessTokenScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

有关详细信息，请参阅 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>。

## <a name="imports-file"></a>导入文件

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>索引页面

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

## <a name="app-component"></a>应用组件

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>重定向到登录组件

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>登录显示组件

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>身份验证组件

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/wasm-aad-b2c-userflows.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* [请求其他访问令牌](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
* <xref:security/authentication/azure-ad-b2c>
* [教程：创建 Azure Active Directory B2C 租户](/azure/active-directory-b2c/tutorial-create-tenant)
* [Microsoft 标识平台文档](/azure/active-directory/develop/)
