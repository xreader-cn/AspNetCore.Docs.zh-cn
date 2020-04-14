---
title: 带有ASP.NET核心的Twitter外部登录设置
author: rick-anderson
description: 本教程演示了将 Twitter 帐户用户身份验证集成到现有ASP.NET核心应用。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: 1f5d667e905e49ae05f5aa31bd5b69ad126f6e28
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277283"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a>带有ASP.NET核心的Twitter外部登录设置

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

此示例演示如何允许用户使用[上一页上](xref:security/authentication/social/index)创建的示例ASP.NET Core 3.0 项目[使用 Twitter 帐户登录](https://dev.twitter.com/web/sign-in/desktop-browser)。

## <a name="create-the-app-in-twitter"></a>在 Twitter 中创建应用程序

* 将[Microsoft.AspNetCore.身份验证.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet 包添加到项目中。

* 导航到[https://apps.twitter.com/](https://apps.twitter.com/)并登录。 如果您还没有 Twitter 帐户，请使用**["立即注册"](https://twitter.com/signup)** 链接创建一个帐户。

* 选择 **"创建应用**"。 填写**应用名称**、**应用程序说明**和公共**网站**URI（在注册域名之前，这可能是暂时的）：

* 选中使用 Twitter**启用登录**旁边的复选框

* 默认情况下，Microsoft.AspNetCore.标识要求用户拥有电子邮件地址。 转到 **"权限"** 选项卡，单击 **"编辑"** 按钮，然后选中 **"向用户请求电子邮件地址"** 旁边的复选框。

* 输入您的开发 URI，并`/signin-twitter`追加到**回调 URL**字段（例如： `https://webapp128.azurewebsites.net/signin-twitter`。 此示例中稍后配置的 Twitter 身份验证方案将自动处理路由中`/signin-twitter`实现 OAuth 流的请求。

  > [!NOTE]
  > URI 段`/signin-twitter`设置为 Twitter 身份验证提供程序的默认回调。 您可以通过[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)类的继承[远程身份验证选项.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Twitter 身份验证中间件，同时更改默认回调 URI。

* 填写表单的其余部分并选择 **"创建**"。 将显示新的应用程序详细信息：

## <a name="store-the-twitter-consumer-api-key-and-secret"></a>存储 Twitter 消费者 API 密钥和机密

存储敏感设置，如Twitter消费者API密钥和[秘密管理器](xref:security/app-secrets)。 对于此示例，请使用以下步骤：

1. 根据[启用密钥存储](xref:security/app-secrets#enable-secret-storage)的指令初始化了机密存储的项目。
1. 使用机密密钥`Authentication:Twitter:ConsumerKey`将敏感设置存储在本地机密存储中，并`Authentication:Twitter:ConsumerSecret`：

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

创建新的 Twitter 应用程序后，可以在 **"密钥和访问令牌"** 选项卡上找到这些令牌：

## <a name="configure-twitter-authentication"></a>配置 Twitter 身份验证

在`ConfigureServices`*Startup.cs*文件中的方法中添加 Twitter 服务：

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

有关 Twitter 身份验证支持的配置选项的详细信息，请参阅[TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API 参考。 这可用于请求有关用户的不同信息。

## <a name="sign-in-with-twitter"></a>使用 Twitter 登录

运行应用并选择 **"登录**"。 出现使用 Twitter 登录的选项：

点击**Twitter**重定向到 Twitter 进行身份验证：

输入 Twitter 凭据后，您将被重定向回可以设置电子邮件的网站。

您现在使用 Twitter 凭据登录：

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a>故障排除

* **仅ASP.NET核心 2.x：** 如果未通过调用`services.AddIdentity`配置标识`ConfigureServices`，则尝试身份验证将导致*参数异常：必须提供"SignInScheme"选项*。 此示例中使用的项目模板可确保完成此操作。
* 如果尚未通过应用初始迁移创建站点数据库，则在处理请求错误时，将获取*数据库操作失败*。 点按 **"应用迁移**"以创建数据库并刷新以继续结束错误。

## <a name="next-steps"></a>后续步骤

* 本文展示了如何使用 Twitter 进行身份验证。 您可以采用类似的方法对[上一页](xref:security/authentication/social/index)中列出的其他提供程序进行身份验证。

* 将网站发布到 Azure Web 应用后，应重置 Twitter`ConsumerSecret`开发人员门户中的 。

* 在`Authentication:Twitter:ConsumerKey`Azure`Authentication:Twitter:ConsumerSecret`门户中将 和 设置为应用程序设置。 配置系统设置为从环境变量读取密钥。
