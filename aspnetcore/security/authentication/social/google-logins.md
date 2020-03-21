---
title: 在 ASP.NET Core Google 外部登录安装程序
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用程序的 Google 帐户用户身份验证。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
uid: security/authentication/google-logins
ms.openlocfilehash: a114d23c25201c9fe31ad0397efaf99fe98a312a
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989774"
---
# <a name="google-external-login-setup-in-aspnet-core"></a>在 ASP.NET Core Google 外部登录安装程序

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

本教程演示如何让用户能够使用在[前一页](xref:security/authentication/social/index)上创建的 ASP.NET Core 3.0 项目使用其 Google 帐户登录。

## <a name="create-a-google-api-console-project-and-client-id"></a>创建 Google API 控制台项目和客户端 ID

* 请安装[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)。
* 导航到 "将[Google 登录集成到你的 web 应用"](https://developers.google.com/identity/sign-in/web/devconsole-project) ，然后选择 "**配置项目**"。
* 在 "**配置 OAuth 客户端**" 对话框中，选择 " **Web 服务器**"。
* 在 "**授权重定向 uri** " 文本输入框中，设置重定向 URI。 例如，`https://localhost:44312/signin-google`
* 保存**客户端 ID**和**客户端密码**。
* 部署站点时，从**Google 控制台**注册新的公共 url。

## <a name="store-the-google-client-id-and-secret"></a>存储 Google 客户端 ID 和机密

用[机密管理器](xref:security/app-secrets)存储敏感设置，例如 Google 客户端 ID 和机密值。 对于本示例，请使用以下步骤：

1. 按照[启用密钥存储](xref:security/app-secrets#enable-secret-storage)中的说明初始化密钥存储的项目。
1. 将敏感设置存储在本地密钥存储中，并将机密密钥 `Authentication:Google:ClientId` 和 `Authentication:Google:ClientSecret`：

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

你可以在[Api 控制台](https://console.developers.google.com/apis/dashboard)中管理 api 凭据和使用情况。

## <a name="configure-google-authentication"></a>配置 Google 身份验证

将 Google 服务添加到 `Startup.ConfigureServices`：

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a>登录 Google

* 运行应用程序，并单击 "**登录"** 。 此时将显示使用 Google 登录的选项。
* 单击 " **google** " 按钮，该按钮将重定向到 google 进行身份验证。
* 输入 Google 凭据后，会重定向回网站。

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

有关 Google authentication 支持的配置选项的详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API 参考。 这可以用于请求有关用户的不同信息。

## <a name="change-the-default-callback-uri"></a>更改默认回调 URI

URI 段 `/signin-google` 设置为 Google 身份验证提供程序的默认回调。 通过[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Google 身份验证中间件时，可以更改默认的回叫 URI。

## <a name="troubleshooting"></a>故障排除

* 如果登录不起作用，并且没有出现任何错误，请切换到开发模式，以便更轻松地进行调试。
* 如果未通过在 `ConfigureServices`中调用 `services.AddIdentity` 来配置标识，尝试对 ArgumentException 中的结果进行身份验证 *：必须提供 "SignInScheme" 选项*。 在本教程中使用的项目模板可确保，此操作。
* 如果尚未通过应用初始迁移来创建站点数据库，则在处理请求错误时，将会出现*数据库操作失败*的情况。 选择 "**应用迁移**" 以创建数据库，并刷新页面以继续出现错误。

## <a name="next-steps"></a>后续步骤

* 本文介绍了您如何可以使用 Google 进行验证。 您可以遵循类似的方法向[前一页](xref:security/authentication/social/index)上列出的其他提供程序进行身份验证。
* 将应用发布到 Azure 后，请在 Google API 控制台中重置 `ClientSecret`。
* 将 `Authentication:Google:ClientId` 和 `Authentication:Google:ClientSecret` 设置为 Azure 门户中的应用程序设置。 配置系统设置以从环境变量读取密钥。
