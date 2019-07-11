---
title: Twitter 使用 ASP.NET Core 的外部登录设置
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用的 Twitter 帐户用户身份验证。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: d816ed27898639b0af6896a51ac035d5526c5d29
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814074"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>Twitter 使用 ASP.NET Core 的外部登录设置

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

此示例演示如何使用户能够[使用 Twitter 帐户登录](https://dev.twitter.com/web/sign-in/desktop-browser)将示例 ASP.NET Core 2.2 项目上创建[上一页](xref:security/authentication/social/index)。

## <a name="create-the-app-in-twitter"></a>在 Twitter 中创建应用程序

* 导航到[ https://apps.twitter.com/ ](https://apps.twitter.com/)并登录。 如果还没有 Twitter 帐户，使用 **[立即注册](https://twitter.com/signup)** 链接创建一个链接。

* 点击**创建新的应用程序**，应用程序中填写**名称**，**说明**和公共**网站**URI （可以是临时直到注册的域名）：

* 输入你的开发 URI 与`/signin-twitter`追加到**有效的 OAuth 重定向 Uri**字段 (例如： `https://webapp128.azurewebsites.net/signin-twitter`)。 配置更高版本在此示例中的 Twitter 身份验证方案将自动处理在请求`/signin-twitter`路由实现 OAuth 流。

  > [!NOTE]
  > URI 段`/signin-twitter`设置为 Twitter 身份验证提供程序的默认回调。 配置 Twitter 身份验证中间件通过继承时，可以更改默认的回调 URI [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)的属性[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)类。

* 填写窗体的其余部分，然后点击**创建 Twitter 应用程序**。 显示新的应用程序详细信息：

## <a name="storing-twitter-consumer-api-key-and-secret"></a>存储的 Twitter 使用者 API 密钥和机密

运行以下命令来安全地存储`ClientId`并`ClientSecret`使用[机密管理器](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerAPISecret <Secret>
```

链接敏感设置，例如 Twitter`Consumer Key`并`Consumer Secret`到应用程序的配置使用[机密管理器](xref:security/app-secrets)。 对于本示例的目的，命名为令牌`Authentication:Twitter:ConsumerKey`和`Authentication:Twitter:ConsumerSecret`。

这些令牌可在**密钥和访问令牌**选项卡后创建新的 Twitter 应用程序：

## <a name="configure-twitter-authentication"></a>配置 Twitter 身份验证

将 Twitter 服务中的添加`ConfigureServices`中的方法*Startup.cs*文件：

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

请参阅[TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) Twitter 身份验证支持的配置选项的详细信息的 API 参考。 这可以用于请求有关用户的不同信息。

## <a name="sign-in-with-twitter"></a>使用 Twitter 登录

运行应用并选择**登录**。 通过 Twitter 进行登录的选项将显示：

单击**Twitter**将重定向到 Twitter 进行身份验证：

输入你的 Twitter 凭据后，你将重定向回 web 站点，你可以设置你的电子邮件。

现在已在使用你的 Twitter 凭据进行登录：

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>疑难解答

* **ASP.NET Core 仅限 2.x:** 如果不通过调用配置标识`services.AddIdentity`中`ConfigureServices`，尝试进行身份验证将导致*ArgumentException:必须提供 SignInScheme 选项*。 此示例中使用的项目模板可确保，此操作。
* 如果尚未通过应用初始迁移创建站点数据库，则会收到*处理请求时，数据库操作失败*错误。 点击**应用迁移**创建数据库，并刷新以忽略错误继续。

## <a name="next-steps"></a>后续步骤

* 本文介绍了您如何可以使用 Twitter 进行验证。 可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。

* 一旦您将您的网站发布到 Azure web 应用时，应重置`ConsumerSecret`Twitter 开发人员门户中。

* 设置`Authentication:Twitter:ConsumerKey`和`Authentication:Twitter:ConsumerSecret`作为在 Azure 门户中的应用程序设置。 配置系统设置以从环境变量读取密钥。