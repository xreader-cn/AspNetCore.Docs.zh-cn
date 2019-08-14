---
title: 具有 ASP.NET Core 的 Twitter 外部登录设置
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用的 Twitter 帐户用户身份验证。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 6b6fa3e50f602a92fec9112ac3ba43583de33a70
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994282"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>具有 ASP.NET Core 的 Twitter 外部登录设置

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

此示例演示如何使用户能够使用在[前一页](xref:security/authentication/social/index)上创建的示例 ASP.NET Core 2.2 项目[登录其 Twitter 帐户](https://dev.twitter.com/web/sign-in/desktop-browser)。

## <a name="create-the-app-in-twitter"></a>在 Twitter 中创建应用

* 导航到[ https://apps.twitter.com/ ](https://apps.twitter.com/)并登录。 如果还没有 Twitter 帐户，使用 **[立即注册](https://twitter.com/signup)** 链接创建一个链接。

* 点击 "**创建新应用**", 并填写应用程序**名称**、**说明**和公共**网站**URI (在注册域名之前, 这可能是暂时的):

* 输入`/signin-twitter`附加到 "**有效的 OAuth 重定向 uri** " 字段中的开发 URI ( `https://webapp128.azurewebsites.net/signin-twitter`例如:)。 稍后在本示例中配置的 Twitter 身份验证方案将自动处理`/signin-twitter`路由中的请求以实现 OAuth 流。

  > [!NOTE]
  > URI 段`/signin-twitter`设置为 Twitter 身份验证提供程序的默认回调。 通过[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Twitter 身份验证中间件时, 可以更改默认的回叫 URI。

* 填写表单的其余部分, 然后点击 "**创建 Twitter 应用程序**"。 显示新应用程序详细信息:

## <a name="storing-twitter-consumer-api-key-and-secret"></a>存储 Twitter 使用者 API 密钥和机密

运行以下命令来安全地存储`ClientId`和`ClientSecret`使用[机密管理器](xref:security/app-secrets):

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

使用[机密管理器](xref:security/app-secrets) `Consumer Key` `Consumer Secret`将 Twitter 等敏感设置链接到应用程序配置。 出于本示例的目的, 请将令牌`Authentication:Twitter:ConsumerKey`命名为和。 `Authentication:Twitter:ConsumerSecret`

创建新的 Twitter 应用程序后, 可以在 "**密钥和访问令牌**" 选项卡上找到这些令牌:

## <a name="configure-twitter-authentication"></a>配置 Twitter 身份验证

将 Twitter 服务添加到`ConfigureServices` *Startup.cs*文件的方法中:

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

有关 Twitter 身份验证支持的配置选项的详细信息, 请参阅[TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API 参考。 这可以用于请求有关用户的不同信息。

## <a name="sign-in-with-twitter"></a>通过 Twitter 登录

运行应用并选择 **"登录"** 。 将显示使用 Twitter 登录的选项:

单击**twitter**重定向到 twitter 进行身份验证:

输入 Twitter 凭据后, 会重定向回到网站, 你可以在其中设置电子邮件。

你现在已使用 Twitter 凭据登录:

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>疑难解答

* **仅 ASP.NET Core 2.x:** 如果未通过调用`services.AddIdentity` `ConfigureServices`来配置标识, 尝试进行身份验证将导致*ArgumentException:必须提供*"SignInScheme" 选项。 本示例中使用的项目模板可确保完成此操作。
* 如果尚未通过应用初始迁移创建站点数据库，则会收到*处理请求时，数据库操作失败*错误。 点击**应用迁移**创建数据库，并刷新以忽略错误继续。

## <a name="next-steps"></a>后续步骤

* 本文介绍了如何通过 Twitter 进行身份验证。 可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。

* 将网站发布到 Azure web 应用后, 应`ConsumerSecret`在 Twitter 开发人员门户中重置。

* 设置`Authentication:Twitter:ConsumerKey`和`Authentication:Twitter:ConsumerSecret`作为在 Azure 门户中的应用程序设置。 配置系统设置以从环境变量读取密钥。
