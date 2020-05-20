---
标题： " Blazor 使用 Microsoft 帐户的 Secure a ASP.NET Core WebAssembly 独立应用作者：说明： monikerRange： ms. 作者： ms. 自定义：毫秒：非位置：
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- " SignalR " uid： 

---
# <a name="secure-an-aspnet-core-blazor-webassembly-standalone-app-with-microsoft-accounts"></a>Blazor使用 Microsoft 帐户保护 ASP.NET Core WebAssembly 独立应用程序

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

若要创建将 Blazor Microsoft 帐户用于身份验证[AZURE ACTIVE DIRECTORY （AAD）](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)的 WebAssembly 独立应用程序，请执行以下操作：

[创建 AAD 租户和 web 应用程序](/azure/active-directory/develop/v2-overview)

在 Azure 门户的**Azure Active Directory**  >  **应用注册**区域中注册 AAD 应用程序：

1. 提供应用的**名称**（例如， ** Blazor 独立 AAD Microsoft 帐户**）。
1. 在 "**支持的帐户类型**" 中，选择**任何组织目录中的帐户**。
1. 将 "**重定向 uri** " 下拉项设置为 " **Web**"，并提供以下重定向 uri： `https://localhost:{PORT}/authentication/login-callback` 。 在 Kestrel 上运行的应用的默认端口为5001。 对于 IIS Express，可以在 "**调试**" 面板的应用程序属性中找到随机生成的端口。
1. 禁用 "**将**  >  **管理员以免授予 openid 并 offline_access 权限**" 复选框。
1. 选择“注册”  。

记录应用程序 ID （客户端 ID）（例如 `11111111-1111-1111-1111-111111111111` ）。

在 "**身份验证**  >  **平台配置**"  >  **Web**：

1. 确认存在的**重定向 URI** `https://localhost:{PORT}/authentication/login-callback` 。
1. 对于 "**隐式授予**"，选中 "**访问令牌**" 和 " **ID 令牌**" 对应的复选框。
1. 此体验可接受应用的其余默认值。
1. 选择“保存”按钮  。

创建应用。 将以下命令中的占位符替换为前面记录的信息，然后在命令行界面中执行以下命令：

```dotnetcli
dotnet new blazorwasm -au SingleOrg --client-id "{CLIENT ID}" --tenant-id "common"
```

若要指定输出位置（如果它不存在，则创建一个项目文件夹），请在命令中包含带有路径的 output 选项（例如， `-o BlazorSample` ）。 文件夹名称还会成为项目名称的一部分。

创建应用后，应该能够：

* 使用 Microsoft 帐户登录到应用。
* 请求 Microsoft Api 的访问令牌。 有关详细信息，请参见:
  * [访问令牌范围](#access-token-scopes)
  * [快速入门：将应用程序配置为公开 Web api](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)。

## <a name="authentication-package"></a>身份验证包

创建应用以使用工作或学校帐户（ `SingleOrg` ）时，应用会自动接收[Microsoft 身份验证库](/azure/active-directory/develop/msal-overview)（）的包引用 `Microsoft.Authentication.WebAssembly.Msal` 。 包提供一组基元，可帮助应用对用户进行身份验证，并获取令牌以调用受保护的 Api。

如果向应用程序中添加身份验证，请将包手动添加到应用的项目文件中：

```xml
<PackageReference Include="Microsoft.Authentication.WebAssembly.Msal" 
  Version="3.2.0" />
```

`Microsoft.Authentication.WebAssembly.Msal`包可传递将 `Microsoft.AspNetCore.Components.WebAssembly.Authentication` 包添加到应用。

## <a name="authentication-service-support"></a>身份验证服务支持

使用包提供的扩展方法在服务容器中注册对用户进行身份验证的支持 `AddMsalAuthentication` `Microsoft.Authentication.WebAssembly.Msal` 。 此方法设置应用与 Identity 提供程序（IP）交互所需的所有服务。

Program.cs  :

```csharp
builder.Services.AddMsalAuthentication(options =>
{
    builder.Configuration.Bind("AzureAd", options.ProviderOptions.Authentication);
});
```

`AddMsalAuthentication`方法接受回调，以配置对应用进行身份验证所需的参数。 注册应用时，可以从 AAD 配置获取配置应用所需的值。

配置由*wwwroot/appsettings*文件提供：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "{CLIENT ID}",
    "ValidateAuthority": true
  }
}
```

示例：

```json
{
  "AzureAd": {
    "Authority": "https://login.microsoftonline.com/common",
    "ClientId": "41451fa7-82d9-4673-8fa5-69eff5a761fd",
    "ValidateAuthority": true
  }
}
```

## <a name="access-token-scopes"></a>访问令牌范围

BlazorWebAssembly 模板不会自动配置应用以请求安全 API 的访问令牌。 若要将访问令牌设置为登录流的一部分，请将作用域添加到的默认访问令牌作用域中 `MsalProviderOptions` ：

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

[!INCLUDE[](~/includes/blazor-security/troubleshoot.md)]

## <a name="additional-resources"></a>其他资源

* <xref:security/blazor/webassembly/additional-scenarios>
* [使用安全的默认客户端的应用中未经身份验证或未授权的 web API 请求](xref:security/blazor/webassembly/additional-scenarios#unauthenticated-or-unauthorized-web-api-requests-in-an-app-with-a-secure-default-client)
* <xref:security/blazor/webassembly/aad-groups-roles>
* [快速入门：将应用程序注册到 Microsoft 标识平台](/azure/active-directory/develop/quickstart-register-app#register-a-new-application-using-the-azure-portal)
* [快速入门：配置应用程序以公开 Web API](/azure/active-directory/develop/quickstart-configure-app-expose-web-apis)
