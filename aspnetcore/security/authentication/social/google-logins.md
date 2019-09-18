---
title: 在 ASP.NET Core Google 外部登录安装程序
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用程序的 Google 帐户用户身份验证。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: e12d831d2e0a5c9acae5ea41fb4187ad4ca6b0ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082477"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>在 ASP.NET Core Google 外部登录安装程序

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

[从2019年3月7日起已关闭旧版 Google + api](https://developers.google.com/+/api-shutdown)。 Google + 登录和开发人员必须迁移到新的 Google 登录系统。 已更新用于 Google 身份验证的 ASP.NET Core 2.1 和2.2 包以适应这些更改。 有关 ASP.NET Core 的详细信息和临时缓解，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/6486)。 本教程已在新的安装过程中更新。

本教程演示如何让用户能够使用在[前一页](xref:security/authentication/social/index)上创建的 ASP.NET Core 2.2 项目使用其 Google 帐户登录。

## <a name="create-a-google-api-console-project-and-client-id"></a>创建 Google API 控制台项目和客户端 ID

* 导航到 "将[Google 登录集成到你的 web 应用"](https://developers.google.com/identity/sign-in/web/devconsole-project) ，然后选择 "**配置项目**"。
* 在 "**配置 OAuth 客户端**" 对话框中，选择 " **Web 服务器**"。
* 在 "**授权重定向 uri** " 文本输入框中，设置重定向 URI。 例如，`https://localhost:5001/signin-google`
* 保存**客户端 ID**和**客户端密码**。
* 部署站点时，从**Google 控制台**注册新的公共 url。

## <a name="store-google-clientid-and-clientsecret"></a>应用商店 Google ClientID 和 ClientSecret

用[机密管理器](xref:security/app-secrets)存储敏感设置`Client ID` ， `Client Secret`例如 Google 和。 出于本教程的目的，请为令牌`Authentication:Google:ClientId`命名和： `Authentication:Google:ClientSecret`

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

你可以在[Api 控制台](https://console.developers.google.com/apis/dashboard)中管理 api 凭据和使用情况。

## <a name="configure-google-authentication"></a>配置 Google 身份验证

将 Google 服务添加到`Startup.ConfigureServices`：

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>使用 Google 登录

* 运行应用程序，并单击 "**登录"** 。 此时将显示使用 Google 登录的选项。
* 单击 " **google** " 按钮，该按钮将重定向到 google 进行身份验证。
* 输入 Google 凭据后，会重定向回网站。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

有关 Google 身份验证支持的配置选项的详细信息，请参阅API参考。<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> 这可以用于请求有关用户的不同信息。

## <a name="change-the-default-callback-uri"></a>更改默认回调 URI

URI 段`/signin-google`设置为默认的 Google 身份验证提供程序的回调。 配置 Google 身份验证中间件通过继承时，可以更改默认的回调 URI [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)的属性[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)类。

## <a name="troubleshooting"></a>疑难解答

* 如果登录不起作用，并且没有出现任何错误，请切换到开发模式，以便更轻松地进行调试。
* 如果未通过调用`services.AddIdentity` `ConfigureServices`来配置标识，则尝试在 ArgumentException 中*对结果进行身份验证：必须提供*"SignInScheme" 选项。 在本教程中使用的项目模板可确保，此操作。
* 如果尚未通过应用初始迁移创建站点数据库，则获取*处理请求时，数据库操作失败*错误。 选择 "**应用迁移**" 以创建数据库，并刷新页面以继续出现错误。

## <a name="next-steps"></a>后续步骤

* 本文介绍了您如何可以使用 Google 进行验证。 可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。
* 将应用发布到 Azure 后，请`ClientSecret`在 Google API 控制台中重置。
* 设置`Authentication:Google:ClientId`和`Authentication:Google:ClientSecret`作为在 Azure 门户中的应用程序设置。 配置系统设置以从环境变量读取密钥。
