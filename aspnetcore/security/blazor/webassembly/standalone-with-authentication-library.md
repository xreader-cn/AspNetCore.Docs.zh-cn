---
title: 使用身份验证库保护BlazorASP.NET核心 Web 大会独立应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/standalone-with-authentication-library
ms.openlocfilehash: 893fff10df37e1c2be549604f4cb83cd20049108
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977036"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-standalone-app-with-the-authentication-library"></a>使用身份验证库保护BlazorASP.NET核心 Web 大会独立应用

哈维尔[·卡尔瓦罗·纳尔逊](https://github.com/javiercn)和[卢克·莱瑟姆](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]

*对于 Azure 活动目录 （AAD） 和 Azure 活动目录 B2C （AAD B2C），不要遵循本主题中的指南。请参阅此目录节点中的 AAD 和 AAD B2C 主题。*

要创建Blazor使用`Microsoft.AspNetCore.Components.WebAssembly.Authentication`库的 WebAssembly 独立应用，请在命令外壳中执行以下命令：

```dotnetcli
dotnet new blazorwasm -au Individual
```

要指定输出位置（如果不存在，则创建项目文件夹）请在命令中包含具有路径的输出选项（例如。 `-o BlazorSample` 文件夹名称也将成为项目名称的一部分。

在可视化工作室[中，创建BlazorWeb 组装应用](xref:blazor/get-started)。 使用**应用商店用户帐户应用内**选项将**身份验证**设置为**单个用户帐户**。

## <a name="authentication-package"></a>身份验证包

创建应用以使用个人用户帐户时，应用会自动在应用的项目文件中接收`Microsoft.AspNetCore.Components.WebAssembly.Authentication`包的包引用。 该包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌来调用受保护的 API。

如果向应用添加身份验证，则手动将包添加到应用的项目文件中：

```xml
<PackageReference 
    Include="Microsoft.AspNetCore.Components.WebAssembly.Authentication" 
    Version="{VERSION}" />
```

在前面`{VERSION}`的包引用中替换为`Microsoft.AspNetCore.Blazor.Templates`<xref:blazor/get-started>本文中显示的包版本。

## <a name="authentication-service-support"></a>身份验证服务支持

使用`AddOidcAuthentication``Microsoft.AspNetCore.Components.WebAssembly.Authentication`包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。 此方法设置应用与标识提供程序 （IP） 交互所需的所有服务。

*Program.cs*：

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    options.ProviderOptions.Authority = "{AUTHORITY}";
    options.ProviderOptions.ClientId = "{CLIENT ID}";
});
```

使用开放 ID 连接 （OIDC） 为独立应用提供身份验证支持。 该方法`AddOidcAuthentication`接受回调以配置使用 OIDC 对应用进行身份验证所需的参数。 配置应用所需的值可以从符合 OIDC 的 IP 获得。 注册应用时获取值，这些值通常发生在其联机门户中。

## <a name="access-token-scopes"></a>访问令牌作用域

WebAssemblyBlazor模板不会自动配置应用以请求安全 API 的访问令牌。 要将令牌预配为登录流的一部分，请将作用域添加到 的`OidcProviderOptions`默认令牌作用域中：

```csharp
builder.Services.AddOidcAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultScopes.Add("{SCOPE URI}");
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
> options.ProviderOptions.DefaultScopes.Add(
>     "{API CLIENT ID OR CUSTOM VALUE}/{SCOPE NAME}");
> ```

有关详细信息，请参阅 <xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens>。

## <a name="imports-file"></a>导入文件

[!INCLUDE[](~/includes/blazor-security/imports-file-standalone.md)]

## <a name="index-page"></a>索引页面

[!INCLUDE[](~/includes/blazor-security/index-page-authentication.md)]

## <a name="app-component"></a>应用组件

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

## <a name="redirecttologin-component"></a>重定向到登录组件

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

## <a name="logindisplay-component"></a>登录显示组件

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

## <a name="authentication-component"></a>身份验证组件

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* [请求其他访问令牌](xref:security/blazor/webassembly/additional-scenarios#request-additional-access-tokens)
