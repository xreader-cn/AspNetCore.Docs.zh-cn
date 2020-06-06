---
title: Blazor使用 Azure Active Directory 保护 ASP.NET Core WebAssembly 托管应用
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
uid: security/blazor/webassembly/hosted-with-azure-active-directory
ms.openlocfilehash: 9e76b300c159a2a1432aa4b1c6e47b3d91084a85
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2020
ms.locfileid: "84215101"
---
# <a name="secure-an-aspnet-core-blazor-webassembly-hosted-app-with-azure-active-directory"></a>Blazor使用 Azure Active Directory 保护 ASP.NET Core WebAssembly 托管应用

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

本文介绍如何创建使用[Azure Active Directory （AAD）](https://azure.microsoft.com/services/active-directory/)进行身份验证的[ Blazor WebAssembly 托管应用](xref:blazor/hosting-models#blazor-webassembly)。

## <a name="register-apps-in-aad-and-create-solution"></a>在 AAD 中注册应用并创建解决方案

### <a name="create-a-tenant"></a>创建租户

按照[快速入门：设置租户](/azure/active-directory/develop/quickstart-create-new-tenant)中的指导在 AAD 中创建租户。

### <a name="register-a-server-api-app"></a>注册服务器 API 应用

按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题，为*服务器 API 应用*注册 AAD 应用，然后执行以下操作：

1. 在**Azure Active Directory**  >  **应用注册**中，选择 "**新建注册**"。
1. 提供应用的**名称**（例如， ** Blazor 服务器 AAD**）。
1. 选择**受支持的帐户类型**。 对于此体验，你可以选择**仅在此组织目录中的帐户**（单租户）。
1. 在这种情况下，*服务器 API 应用*不需要**重定向 uri** ，因此请将下拉集设置为 " **Web** "，并不要输入 "重定向 uri"。
1. 禁用 "**将**  >  **管理员以免授予 openid 并 offline_access 权限**" 复选框。
1. 选择“注册”。

记录以下信息：

* *服务器 API 应用*应用程序 ID （客户端 ID）（例如， `11111111-1111-1111-1111-111111111111` ）
* 目录 ID （租户 ID）（例如， `222222222-2222-2222-2222-222222222222` ）
* AAD 租户域（例如， `contoso.onmicrosoft.com` ）：在注册的应用的 Azure 门户的 "**品牌**" 边栏选项卡中，该域作为**发布者域**提供。

在 " **API 权限**" 中，删除**Microsoft Graph**  >  **用户. 读取**权限，因为应用无需登录或用户配置文件访问。

在中**公开 API**：

1. 选择“添加范围”。
1. 选择“保存并继续”。 
1. 提供**作用域名称**（例如 `API.Access` ）。
1. 提供**管理员同意显示名称**（例如 `Access API` ）。
1. 提供**管理员同意说明**（例如 `Allows the app to access server app API endpoints.` ）。
1. 确认 "**状态**" 设置为 "**已启用**"。
1. 选择“添加作用域”。

记录以下信息：

* 应用 ID URI （例如，、 `https://contoso.onmicrosoft.com/11111111-1111-1111-1111-111111111111` `api://11111111-1111-1111-1111-111111111111` 或提供的自定义值）
* 默认作用域（例如 `API.Access` ）

### <a name="register-a-client-app"></a>注册客户端应用

按照[快速入门：向 Microsoft 标识平台注册应用程序](/azure/active-directory/develop/quickstart-register-app)和后续 Azure AAD 主题中的指导，为*客户端应用程序*注册 AAD 应用，然后执行以下操作：

1. 在**Azure Active Directory**  >  **应用注册**中，选择 "**新建注册**"。
1. 提供应用的**名称**（例如， ** Blazor 客户端 AAD**）。
1. 选择**受支持的帐户类型**。 对于此体验，你可以选择**仅在此组织目录中的帐户**（单租户）。
1. 将 "**重定向 uri** " 下拉菜单保留设置为 " **Web** "，并提供以下重定向 uri： `https://localhost:{PORT}/authentication/login-callback` 。 在 Kestrel 上运行的应用的默认端口为5001。 如果应用在不同的 Kestrel 端口上运行，请使用应用的端口。 对于 IIS Express，可以在 "**调试**" 面板的服务器应用的属性中找到该应用的随机生成的端口。 由于此时应用不存在，并且 IIS Express 端口未知，因此在创建应用后返回到此步骤并更新重定向 URI。 "[创建应用"](#create-the-app)部分中将出现一个批注，以提醒 IIS Express 的用户更新重定向 URI。
1. 禁用 "**将**  >  **管理员以免授予 openid 并 offline_access 权限**" 复选框。
1. 选择“注册”。

记录*客户端应用*应用程序 Id （客户端 id）（例如 `33333333-3333-3333-3333-333333333333` ）。

在 "**身份验证**  >  **平台配置**"  >  **Web**：

1. 确认存在的**重定向 URI** `https://localhost:{PORT}/authentication/login-callback` 。
1. 对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。
1. 此体验可接受应用的其余默认值。
1. 选择“保存”按钮  。

在 " **API 权限**：

1. 确认应用程序具有**Microsoft Graph**的 "  >  **用户**" 权限。
1. 选择 "**添加权限**"，然后选择 **"我的 api"**。
1. 从 "**名称**" 列中选择*服务器 API 应用*（例如， ** Blazor 服务器 AAD**）。
1. 打开**API**列表。
1. 启用对 API 的访问（例如 `API.Access` ）。
1. 选择“添加权限”。
1. 选择 "**为 {租户名称} 授予管理内容**" 按钮。 请选择“是”以确认。 

### <a name="create-the-app"></a>创建应用

将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行命令：

```dotnetcli
dotnet new blazorwasm -au SingleOrg --api-client-id "{SERVER API APP CLIENT ID}" --app-id-uri "{SERVER API APP ID URI}" --client-id "{CLIENT APP CLIENT ID}" --default-scope "{DEFAULT SCOPE}" --domain "{TENANT DOMAIN}" -ho --tenant-id "{TENANT ID}"
```

若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。 文件夹名称还会成为项目名称的一部分。

> [!NOTE]
> 将应用 ID URI 传递给 `app-id-uri` 选项，但请注意，可能需要在客户端应用中进行配置更改，这在 "[访问令牌范围](#access-token-scopes)" 部分中进行了介绍。

> [!NOTE]
> 在 Azure 门户中，*客户端应用的***身份验证**  >  **平台配置**  >  **Web**  >  **重定向 URI**为端口5001配置，适用于在 Kestrel 服务器上用默认设置运行的应用。
>
> 如果*客户端应用*是在随机 IIS Express 端口上运行，则可以在 "**调试**" 面板中的*服务器应用*的属性中找到该应用的端口。
>
> 如果先前未将此端口配置为*客户端应用的*已知端口，请返回到 Azure 门户中的*客户端应用*注册，并更新具有正确端口的重定向 URI。

## <a name="server-app-configuration"></a>服务器应用配置

*本部分适用于解决方案的**服务器**应用。*

### <a name="authentication-package"></a>身份验证包

对 ASP.NET Core Web Api 进行身份验证和授权的支持是由[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.AzureAD.UI/)包提供的：

```xml
<PackageReference Include="Microsoft.AspNetCore.Authentication.AzureAD.UI" 
  Version="3.1.4" />
```

### <a name="authentication-service-support"></a>身份验证服务支持

`AddAuthentication`方法在应用中设置身份验证服务，并将 JWT 持有者处理程序配置为默认的身份验证方法。 <xref:Microsoft.AspNetCore.Authentication.AzureADAuthenticationBuilderExtensions.AddAzureADBearer%2A>方法在验证 Azure Active Directory 发出的令牌所需的 JWT 持有者处理程序中设置特定参数：

```csharp
services.AddAuthentication(AzureADDefaults.BearerAuthenticationScheme)
    .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication%2A>并 <xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization%2A> 确保：

* 应用尝试分析和验证传入请求的令牌。
* 任何试图访问受保护资源的请求均不正确。

```csharp
app.UseAuthentication();
app.UseAuthorization();
```

### <a name="useridentityname"></a>用户。 Identity路径名

默认情况下，服务器应用 API `User.Identity.Name` 使用声明类型中的值 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` （例如）进行填充 `2d64b3da-d9d5-42c6-9352-53d8df33d770@contoso.onmicrosoft.com` 。

若要将应用配置为接收来自 `name` 声明类型的值，请在中配置[TokenValidationParameters](xref:Microsoft.IdentityModel.Tokens.TokenValidationParameters.NameClaimType) <xref:Microsoft.AspNetCore.Authentication.JwtBearer.JwtBearerOptions> `Startup.ConfigureServices` ：

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;

...

services.Configure<JwtBearerOptions>(
    AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        options.TokenValidationParameters.NameClaimType = "name";
    });
```

### <a name="app-settings"></a>应用设置

*Appsettings*文件包含用于配置用于验证访问令牌的 JWT 持有者处理程序的选项：

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "{DOMAIN}",
    "TenantId": "{TENANT ID}",
    "ClientId": "{SERVER API APP CLIENT ID}",
  }
}
```

示例：

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "contoso.onmicrosoft.com",
    "TenantId": "e86c78e2-8bb4-4c41-aefd-918e0565a45e",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
  }
}
```

### <a name="weatherforecast-controller"></a>WeatherForecast 控制器

WeatherForecast 控制器（*控制器/WeatherForecastController*）公开受保护的 API，该 API 的 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) 属性应用到控制器。 **务必**要了解：

* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)此 api 控制器中的属性只是保护此 api 不受未经授权的访问。
* [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)WebAssembly 应用程序中使用的属性 Blazor 仅作为对应用程序的提示，用户应授权该应用程序正常工作。

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

创建应用以使用工作或学校帐户（ `SingleOrg` ）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（[WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)）的包引用。 包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。

如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

[WebAssembly. Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)包可向应用程序中添加[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Authentication/)包，并将其添加到应用中。

### <a name="authentication-service-support"></a>身份验证服务支持

添加了对实例的支持 <xref:System.Net.Http.HttpClient> ，其中包括对服务器项目发出请求时的访问令牌。

Program.cs:

```csharp
builder.Services.AddHttpClient("{APP ASSEMBLY}.ServerAPI", client => 
        client.BaseAddress = new Uri(builder.HostEnvironment.BaseAddress))
    .AddHttpMessageHandler<BaseAddressAuthorizationMessageHandler>();

builder.Services.AddTransient(sp => sp.GetRequiredService<IHttpClientFactory>()
    .CreateClient("{APP ASSEMBLY}.ServerAPI"));
```

使用 <xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A> [Msal](https://www.nuget.org/packages/Microsoft.Authentication.WebAssembly.Msal/)包提供的扩展方法在服务容器中注册对用户进行身份验证的支持。 此方法设置应用程序与 Identity 提供程序（IP）进行交互所需的服务。

Program.cs:

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
    options.ProviderOptions.DefaultAccessTokenScopes.Add("{SCOPE URI}");
});
```

<xref:Microsoft.Extensions.DependencyInjection.MsalWebAssemblyServiceCollectionExtensions.AddMsalAuthentication%2A>方法接受回调，以配置对应用进行身份验证所需的参数。 注册应用时，可以从 Azure 门户 AAD 配置获取配置应用所需的值。

配置由*wwwroot/appsettings*文件提供：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/{TENANT ID}",
    "ClientId": "{CLIENT APP CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

示例：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/e86c78e2-...-918e0565a45e",
    "ClientId": "4369008b-21fa-427c-abaa-9b53bf58e538",
    "ValidateAuthority": true
  }
}
```

### <a name="access-token-scopes"></a>访问令牌范围

默认访问令牌范围表示访问令牌作用域的列表：

* 默认情况下，在登录请求中包括。
* 用于在身份验证后立即设置访问令牌。

对于每个 Azure Active Directory 规则，所有作用域都必须属于同一应用。 可以根据需要为其他 API 应用添加其他作用域：

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


### <a name="imports-file"></a>导入文件

[!INCLUDE[](~/includes/blazor-security/imports-file-hosted.md)]

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

从服务器项目运行应用。 使用 Visual Studio 时，可以执行以下任一操作：

* 将工具栏中的 "**启动项目**" 下拉列表设置为 "*服务器 API 应用*"，然后选择 "**运行**" 按钮。
* 在**解决方案资源管理器**中选择服务器项目，并在工具栏中选择 "**运行**" 按钮，或从 "**调试**" 菜单中启动该应用程序。

<!-- HOLD
[!INCLUDE[](~/includes/blazor-security/usermanager-signinmanager.md)]
-->

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* <xref:security/blazor/webassembly/additional-scenarios>
* [使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* <xref:security/authentication/azure-active-directory/index>
* [Microsoft 标识平台文档](/azure/active-directory/develop/)
