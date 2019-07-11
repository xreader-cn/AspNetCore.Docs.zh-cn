---
title: 使用 ASP.NET Core 的 Microsoft 帐户外部登录设置
author: rick-anderson
description: 此示例演示如何集成到现有的 ASP.NET Core 应用的 Microsoft 帐户用户身份验证。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 2c690e5bd8465806d42091616917cfdd747ef8f0
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815572"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>使用 ASP.NET Core 的 Microsoft 帐户外部登录设置

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

此示例演示如何使用户能够使用 ASP.NET Core 2.2 项目上创建其 Microsoft 帐户登录[上一页](xref:security/authentication/social/index)。

## <a name="create-the-app-in-microsoft-developer-portal"></a>在 Microsoft 开发人员门户中创建应用

* 导航到[Azure 门户-应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页面上，创建或登录到 Microsoft 帐户：

如果你没有 Microsoft 帐户，选择**创建一个**。 在登录后你将重定向到**应用注册**页：

* 选择**新注册**
* 输入**名称**。
* 选择一个选项来**支持的帐户类型**。  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* 下**重定向 URI**，输入与你开发的 URL`/signin-microsoft`追加。 例如， `https://localhost:44389/signin-microsoft` 。 配置更高版本在此示例中的 Microsoft 身份验证方案将自动处理在请求`/signin-microsoft`路由实现 OAuth 流。
* 选择**注册**

### <a name="create-client-secret"></a>创建客户端机密

* 在左窗格中，选择**证书和机密**。
* 下**客户端机密**，选择**新客户端机密**

  * 添加客户端机密的说明。
  * 选择**添加**按钮。

* 下**客户端机密**，复制客户端机密的值。

> [!NOTE]
> URI 段`/signin-microsoft`设置为默认的 Microsoft 身份验证提供程序的回调。 配置 Microsoft 身份验证中间件通过继承时，可以更改默认的回调 URI [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性的[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)类。

## <a name="store-the-microsoft-client-id-and-client-secret"></a>Microsoft 客户端 ID 和客户端机密存储

运行以下命令来安全地存储`ClientId`并`ClientSecret`使用[机密管理器](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

链接敏感设置，例如 Microsoft`ClientId`并`ClientSecret`到应用程序的配置使用[机密管理器](xref:security/app-secrets)。 对于本示例的目的，命名为令牌`Authentication:Microsoft:ClientId`和`Authentication:Microsoft:ClientSecret`。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>配置 Microsoft 帐户身份验证

添加 Microsoft 帐户服务的目标`Startup.ConfigureServices`:

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

请参阅[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)支持的 Microsoft 帐户身份验证的配置选项的详细信息的 API 参考。 这可以用于请求有关用户的不同信息。

## <a name="sign-in-with-microsoft-account"></a>使用 Microsoft 帐户登录

运行并单击**登录**。 将显示一个选项以使用 Microsoft 登录。 当单击 Microsoft 时，将重定向的身份验证到 Microsoft。 （如果尚未登录），使用你的 Microsoft 帐户登录后您将会提示您允许应用访问你的信息：

点击**是**，并且将重定向回 web 站点，你可以设置你的电子邮件。

现在已登录中使用 Microsoft 凭据：

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>疑难解答

* 如果 Microsoft 帐户提供程序将你重定向到登录错误页，请记下错误标题和说明查询字符串后面的参数直接`#`（井号标签） 的 Uri 中。

  错误消息似乎指出了使用 Microsoft 身份验证问题，尽管最常见的原因是你的应用程序 Uri 不匹配的任何**重定向 Uri**指定为**Web**平台.
* 如果不通过调用配置标识`services.AddIdentity`中`ConfigureServices`，尝试进行身份验证将导致*ArgumentException:必须提供 SignInScheme 选项*。 此示例中使用的项目模板可确保，此操作。
* 如果尚未通过应用初始迁移创建站点数据库，则会收到*处理请求时，数据库操作失败*错误。 点击**应用迁移**创建数据库，并刷新以忽略错误继续。

## <a name="next-steps"></a>后续步骤

* 本文介绍了如何向 Microsoft。 可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。

* 一旦将您的网站发布到 Azure web 应用，创建新的客户端机密在 Microsoft 开发人员门户中。

* 设置`Authentication:Microsoft:ClientId`和`Authentication:Microsoft:ClientSecret`作为在 Azure 门户中的应用程序设置。 配置系统设置以从环境变量读取密钥。