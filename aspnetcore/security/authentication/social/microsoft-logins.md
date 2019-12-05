---
title: 使用 ASP.NET Core 的 Microsoft 帐户外部登录设置
author: rick-anderson
description: 此示例演示如何将 Microsoft 帐户用户身份验证集成到现有 ASP.NET Core 应用中。
ms.author: riande
ms.custom: mvc
ms.date: 12/4/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: ddaae1a25a1dcf167ffae0f24b480e2cde6aca5b
ms.sourcegitcommit: f4cd3828e26e6d549ba8d0c36a17be35ad9e5a51
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74825464"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>使用 ASP.NET Core 的 Microsoft 帐户外部登录设置

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

此示例演示如何使用户能够使用在[前一页](xref:security/authentication/social/index)上创建的 ASP.NET Core 3.0 项目使用其 Microsoft 帐户进行登录。

## <a name="create-the-app-in-microsoft-developer-portal"></a>在 Microsoft 开发人员门户中创建应用

* 将[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet 包添加到项目。
* 导航到 " [Azure 门户应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)" 页，创建或登录到 Microsoft 帐户：

如果没有 Microsoft 帐户，请选择 "**创建**"。 登录后，会重定向到**应用注册**页面：

* 选择**新注册**
* 输入“名称”。
* 为**支持的帐户类型**选择一个选项。  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* 在 "**重定向 URI**" 下，输入 `/signin-microsoft` 追加的开发 URL。 例如 `https://localhost:5001/signin-microsoft`。 稍后在本示例中配置的 Microsoft 身份验证方案将自动处理 `/signin-microsoft` 路由中的请求以实现 OAuth 流。
* 选择**注册**

### <a name="create-client-secret"></a>创建客户端密码

* 在左侧窗格中，选择 "**证书" & "机密**"。
* 在 "**客户端密码**" 下，选择**新的客户端密码**

  * 添加客户端密码的说明。
  * 选择“添加”按钮。

* 在 "**客户端**密码" 下，复制 "客户端密钥" 的值。

> [!NOTE]
> URI 段 `/signin-microsoft` 设置为 Microsoft 身份验证提供程序的默认回调。 通过[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Microsoft 身份验证中间件时，可以更改默认的回调 URI。

## <a name="store-the-microsoft-client-id-and-client-secret"></a>存储 Microsoft 客户端 ID 和客户端密钥

运行以下命令，使用[机密管理器](xref:security/app-secrets)安全地存储 `ClientId` 和 `ClientSecret`：

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

使用[机密管理器](xref:security/app-secrets)将敏感设置（如 Microsoft `ClientId` 和 `ClientSecret`）链接到应用程序配置。 出于本示例的目的，请将令牌命名 `Authentication:Microsoft:ClientId` 和 `Authentication:Microsoft:ClientSecret`。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>配置 Microsoft 帐户身份验证

将 Microsoft 帐户服务添加到 `Startup.ConfigureServices`：

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

有关 Microsoft 帐户身份验证支持的配置选项的详细信息，请参阅[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API 参考。 这可以用于请求有关用户的不同信息。

## <a name="sign-in-with-microsoft-account"></a>Microsoft 登录帐户

运行应用程序，并单击 "**登录"** 。 此时会显示一个用于使用 Microsoft 登录的选项。 当你单击 "Microsoft" 时，你将重定向到 Microsoft 进行身份验证。 使用你的 Microsoft 帐户登录后，系统会提示你让应用访问你的信息：

点击 **"是"** ，你会被重定向回到网站，你可以在其中设置电子邮件。

你现在已使用 Microsoft 凭据登录：

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>故障排除

* 如果 Microsoft 帐户提供程序将您重定向到登录错误页，请记下 Uri 中的 `#` （井号）后面的错误标题和说明查询字符串参数。

  尽管错误消息似乎指出了 Microsoft 身份验证存在问题，但最常见的原因是应用程序 Uri 与为**Web**平台指定的任何**重定向 uri**都不匹配。
* 如果未通过在 `ConfigureServices`中调用 `services.AddIdentity` 来配置标识，则尝试进行身份验证将导致*ArgumentException：必须提供 "SignInScheme" 选项*。 本示例中使用的项目模板可确保完成此操作。
* 如果尚未通过应用初始迁移创建站点数据库，则会收到*处理请求时，数据库操作失败*错误。 点击**应用迁移**创建数据库，并刷新以忽略错误继续。

## <a name="next-steps"></a>后续步骤

* 本文演示了如何向 Microsoft 进行身份验证。 可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。

* 将网站发布到 Azure web 应用后，在 Microsoft 开发人员门户中创建新的客户端密码。

* 设置`Authentication:Microsoft:ClientId`和`Authentication:Microsoft:ClientSecret`作为在 Azure 门户中的应用程序设置。 配置系统设置以从环境变量读取密钥。
