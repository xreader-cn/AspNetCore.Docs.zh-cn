---
title: 使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 托管应用
author: guardrex
description: ''
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/16/2020
no-loc:
- Blazor
- SignalR
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: fc16a7212254e73efd4cea8155975f293e5d9ebb
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/24/2020
ms.locfileid: "80219280"
---
# <a name="secure-an-aspnet-core-opno-locblazor-webassembly-hosted-app-with-azure-active-directory"></a>使用 Azure Active Directory 保护 ASP.NET Core Blazor WebAssembly 托管应用

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[!INCLUDE[](~/includes/blazorwasm-3.2-template-article-notice.md)]



本文介绍如何创建使用[Azure Active Directory （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的[Blazor WebAssembly 托管应用](xref:blazor/hosting-models#blazor-webassembly)。

## <a name="register-apps-in-aad-b2c-and-create-solution"></a>在 AAD B2C 中注册应用并创建解决方案

### <a name="create-a-tenant"></a>创建租户

按照[快速入门：设置租户](/azure/active-directory/develop/quickstart-create-new-tenant)中的指导在 AAD 中创建租户。

### <a name="register-a-server-api-app"></a>注册服务器 API 应用

请按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题中的指导，在 Azure 门户的 " **Azure Active Directory** > **应用注册**" 区域中为*服务器 API 应用*注册 AAD 应用：

1. 选择“新注册”。
1. 提供应用的**名称**（例如 **Blazor 服务器 AAD**）。
1. 选择**受支持的帐户类型**。 对于此体验，你可以选择**仅在此组织目录中的帐户**（单租户）。
1. 在这种情况下，*服务器 API 应用*不需要**重定向 uri** ，因此请将下拉集设置为 " **Web** "，并不要输入 "重定向 uri"。
1. 禁用 "**权限** > **向 openid 和 offline_access 权限授予管理员**权限" 复选框。
1. 选择“注册”。

在 " **API 权限**" 中，删除**Microsoft Graph** > **用户 "。读取**权限，因为应用不需要登录或 uer 配置文件访问。

在中**公开 API**：

1. 选择“添加范围”。
1. 选择“保存并继续”。
1. 提供**作用域名称**（例如 `API.Access`）。
1. 提供**管理员同意显示名称**（例如 `Access API`）。
1. 提供**管理员同意说明**（例如 `Allows the app to access server app API endpoints.`）。
1. 确认 "**状态**" 设置为 "**已启用**"。
1. 选择 "**添加作用域**"。

记录以下信息：

* *服务器 API 应用*应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111`）
* 目录 ID （租户 ID）（例如 `222222222-2222-2222-2222-222222222222`）
* AAD 租户域（例如 `contoso.onmicrosoft.com`）
* 默认作用域（例如 `API.Access`）

### <a name="register-a-client-app"></a>注册客户端应用

请按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题中的指导，在 Azure 门户的**Azure Active Directory** > **应用注册**区域中为*客户端应用程序*注册 AAD 应用程序：

1. 选择“新注册”。
1. 提供应用的**名称**（例如， **Blazor 客户端 AAD**）。
1. 选择**受支持的帐户类型**。 对于此体验，你可以选择**仅在此组织目录中的帐户**（单租户）。
1. 将 "**重定向 uri** " 下拉集保持设置为 " **Web**"，并提供 `https://localhost:5001/authentication/login-callback`的重定向 uri。
1. 禁用 "**权限** > **向 openid 和 offline_access 权限授予管理员**权限" 复选框。
1. 选择“注册”。

在 "**身份验证**" > **平台配置** > **Web**：

1. 确认存在 `https://localhost:5001/authentication/login-callback` 的**重定向 URI** 。
1. 对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。
1. 此体验可接受应用的其余默认值。
1. 选择“保存”按钮。

在 " **API 权限**：

1. 确认应用程序已**Microsoft Graph** > **用户。读取**权限。
1. 选择 "**添加权限**"，然后选择 **"我的 api"** 。
1. 从 "**名称**" 列中选择 "*服务器 API 应用*" （例如 **Blazor 服务器 AAD**）。
1. 打开**API**列表。
1. 启用对 API 的访问（例如 `API.Access`）。
1. 选择“添加权限”。
1. 选择 "**为 {租户名称} 授予管理内容**" 按钮。 请选择“是”以确认。

记录*客户端应用*应用程序 Id （客户端 id）（例如 `33333333-3333-3333-3333-333333333333`）。

### <a name="create-the-app"></a>创建应用

将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP CLIENT ID}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含 output 选项，其中包含一个路径（例如 `-o BlazorSample`）。 文件夹名称还会成为项目名称的一部分。

> [!NOTE]
> 请参阅[身份验证服务支持](#Authentication service support)部分，了解对默认访问令牌范围的重要配置更改。 从模板创建*客户端应用*后，必须手动更改 Blazor WebAssembly 模板提供的值。

## <a name="server-app-configuration"></a>服务器应用配置

*本部分适用于解决方案的**服务器**应用。*

### <a name="authentication-package"></a>身份验证包

`Microsoft.AspNetCore.Authentication.AzureAD.UI`提供对 ASP.NET Core Web Api 的身份验证和授权调用的支持：

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
    Version="3.1.0" />
```

### <a name="authentication-service-support"></a>身份验证服务支持

`AddAuthentication` 方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认身份验证方法。 `AddAzureADBearer` 方法在验证 Azure Active Directory 发出的令牌所需的 JWT 持有者处理程序中设置特定参数：

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

`UseAuthentication` 和 `UseAuthorization` 确保：

* 应用尝试分析和验证传入请求的令牌。
* 任何试图访问受保护资源的请求均不正确。

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="app-settings"></a>应用设置

*Appsettings*文件包含用于配置用于验证访问令牌的 JWT 持有者处理程序的选项。

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{API CLIENT ID}",
  }
}
```

### <a name="weatherforecast-controller"></a>WeatherForecast 控制器

WeatherForecast 控制器（*控制器/WeatherForecastController*）公开受保护的 API，并将 `[Authorize]` 特性应用到控制器。 **务必**要了解：

* 此 API 控制器中的 `[Authorize]` 属性是保护此 API 不受未经授权的访问的唯一操作。
* Blazor WebAssembly 应用程序中使用的 `[Authorize]` 属性仅作为对应用程序的提示，用户应授权该应用程序正常工作。

```csharp
[Authorize]
[ApiController]
[Route("[controller]")]
public class WeatherForecastController : ControllerBase
{
    [HttpGet]
    public IEnumerable<WeatherForecast> Get()
    {
        ...
    }
}
```

## <a name="client-app-configuration"></a>客户端应用配置

*本部分适用于解决方案的**客户端**应用。*

### <a name="authentication-package"></a>身份验证包

创建应用以使用工作或学校帐户（`SingleOrg`）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（`Microsoft.Authentication.WebAssembly.Msal`）的包引用。 包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。

如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
    Version="{VERSION}" />
```

将前面的包引用中的 `{VERSION}` 替换为 <xref:blazor/get-started> 一文中所示 `Microsoft.AspNetCore.Blazor.Templates` 包的版本。

`Microsoft.Authentication.WebAssembly.Msal` 包可向应用程序中添加 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包。

### <a name="authentication-service-support"></a>身份验证服务支持

使用 `Microsoft.Authentication.WebAssembly.Msal` 包提供的 `AddMsalAuthentication` 扩展方法在服务容器中注册对用户进行身份验证。 此方法设置应用程序与标识提供程序（IP）交互所需的所有服务。

Program.cs：

生成*客户端应用*时，默认的访问令牌范围为 `api://{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}`格式。 **删除范围值的 `api://` 部分。** 此问题将在将来的预览版本中得到解决。

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    var authentication = options.ProviderOptions.Authentication;
    authentication.Authority = "https://login.microsoftonline.com/{TENANT ID}";
    authentication.ClientId = "{CLIENT ID}";
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}");
});
```

> [!NOTE]
> 默认访问令牌范围必须是 `{SERVER API APP CLIENT ID}/{DEFAULT SCOPE}` 格式（例如，`11111111-1111-1111-1111-111111111111/API.Access`）。 如果向范围设置提供方案或方案和主机（如 Azure 门户中所示），则当*客户端应用*收到来自*服务器 API 应用*的*401 未经授权*响应时，将引发未处理的异常。

`AddMsalAuthentication` 方法接受回调，以配置对应用进行身份验证所需的参数。 注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。

默认访问令牌范围表示访问令牌作用域的列表：

* 默认情况下，在登录请求中包括。
* 用于在身份验证后立即设置访问令牌。

对于每个 Azure Active Directory 规则，所有作用域都必须属于同一应用。 可以根据需要为其他 API 应用添加其他作用域：

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    ...
    options.ProviderOptions.DefaultAccessTokenScopes.Add(
        "{SERVER API APP CLIENT ID}/{SCOPE}");
});
```

### <a name="index-page"></a>索引页面

[!INCLUDE[](~/includes/blazor-security/index-page-msal.md)]

### <a name="app-component"></a>应用组件

[!INCLUDE[](~/includes/blazor-security/app-component.md)]

### <a name="redirecttologin-component"></a>RedirectToLogin 组件

[!INCLUDE[](~/includes/blazor-security/redirecttologin-component.md)]

### <a name="logindisplay-component"></a>LoginDisplay 组件

[!INCLUDE[](~/includes/blazor-security/logindisplay-component.md)]

### <a name="authentication-component"></a>身份验证组件

[!INCLUDE[](~/includes/blazor-security/authentication-component.md)]

### <a name="fetchdata-component"></a>FetchData 组件

[!INCLUDE[](~/includes/blazor-security/fetchdata-component.md)]

## <a name="run-the-app"></a>运行应用

从服务器项目运行应用。 使用 Visual Studio 时，请在**解决方案资源管理器**中选择服务器项目，并在工具栏中选择 "**运行**" 按钮，或从 "**调试**" 菜单启动应用程序。

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* <xref:security/authentication/azure-active-directory/index>
