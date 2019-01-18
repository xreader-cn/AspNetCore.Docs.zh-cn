---
title: 在 ASP.NET Core Google 外部登录安装程序
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用程序的 Google 帐户用户身份验证。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 1/11/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 98857a84238124e75d695242c8d421b9a29f02e7
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396104"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>在 ASP.NET Core Google 外部登录安装程序

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

与 Google 启动在 2019 年 1 月[关闭](https://developers.google.com/+/api-shutdown)Google + 中登录和开发人员必须将移到新的 Google 登录系统中的年 3 月。 将在二月以适应所做的更改更新 ASP.NET Core 2.1 和 2.2 包进行 Google 身份验证。 有关详细信息和针对 ASP.NET Core 的临时缓解措施，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/6486)。 本教程已更新使用新的安装程序进程。

本教程演示如何使用户能够使用 ASP.NET Core 2.2 项目上创建其 Google 帐户登录[上一页](xref:security/authentication/social/index)。

## <a name="create-a-google-api-console-project-and-client-id"></a>创建 Google API 控制台项目和客户端 ID

* 导航到[集成 Google 登录到你的 web 应用](https://developers.google.com/identity/sign-in/web/devconsole-project)，然后选择**配置项目**。
* 在中**配置 OAuth 客户端**对话框中，选择**Web 服务器**。
* 在中**已授权重定向 Uri**文本输入框中，将设置重定向 URI。 例如，`https://localhost:5001/signin-google`
* 保存**客户端 ID**并**客户端机密**。
* 在部署站点时，注册中的新公共 url **Google Console**。

## <a name="store-google-clientid-and-clientsecret"></a>应用商店 Google ClientID 和 ClientSecret

存储敏感设置，例如 Google`Client ID`并`Client Secret`与[机密管理器](xref:security/app-secrets)。 对于本教程的目的，命名为令牌`Authentication:Google:ClientId`和`Authentication:Google:ClientSecret`:

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

你可以管理 API 凭据和中的使用情况[API 控制台](https://console.developers.google.com/apis/dashboard)。

## <a name="configure-google-authentication"></a>配置 Google 身份验证

添加 Google 服务的目标`Startup.ConfigureServices`。

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>使用 Google 登录

* 运行应用并单击**登录**。 将显示一个选项以使用 Google 登录。
* 单击**Google**按钮，将重定向到 Google 进行身份验证。
* 输入 Google 凭据后，你将重定向回 web 站点。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

请参阅[GoogleOptions](/dotnet/api/microsoft.aspnetcore.builder.googleoptions) Google 身份验证支持的配置选项的详细信息的 API 参考。 这可以用于请求有关用户的不同信息。

## <a name="change-the-default-callback-uri"></a>更改默认的回调 URI

URI 段`/signin-google`设置为默认的 Google 身份验证提供程序的回调。 配置 Google 身份验证中间件通过继承时，可以更改默认的回调 URI [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)的属性[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)类。

## <a name="troubleshooting"></a>疑难解答

* 如果登录不起作用，并且如果没有收到任何错误，切换到开发模式，以使问题更易于调试。
* 如果不通过调用配置标识`services.AddIdentity`中`ConfigureServices`，尝试进行身份验证中的结果*ArgumentException:必须提供 SignInScheme 选项*。 在本教程中使用的项目模板可确保，此操作。
* 如果尚未通过应用初始迁移创建站点数据库，则获取*处理请求时，数据库操作失败*错误。 点击**应用迁移**创建数据库，并刷新以忽略错误继续。

## <a name="next-steps"></a>后续步骤

* 本文介绍了您如何可以使用 Google 进行验证。 可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。
* 将应用发布到 Azure 时，会重置`ClientSecret`在 Google API 控制台中。
* 设置`Authentication:Google:ClientId`和`Authentication:Google:ClientSecret`作为在 Azure 门户中的应用程序设置。 配置系统设置以从环境变量读取密钥。
