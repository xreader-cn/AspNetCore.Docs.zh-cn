---
title: ASP.NET Core 中的 Facebook 外部登录设置
author: rick-anderson
description: 包含代码示例的教程演示如何将 Facebook 帐户用户身份验证集成到现有 ASP.NET Core 应用。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 12/02/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 2e4cc04c6e7ff8e5f5701cc7f9ede73dbc1b4685
ms.sourcegitcommit: 3b6b0a54b20dc99b0c8c5978400c60adf431072f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74717077"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET Core 中的 Facebook 外部登录设置

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

本教程中的代码示例演示如何使用户能够使用在[前一页](xref:security/authentication/social/index)上创建的示例 ASP.NET Core 3.0 项目登录其 Facebook 帐户。 首先，我们要按照[官方步骤](https://developers.facebook.com)创建 FACEBOOK 应用 ID。

## <a name="create-the-app-in-facebook"></a>在 Facebook 中创建应用

* 将[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet 包添加到项目。

* 导航到[Facebook 开发人员应用](https://developers.facebook.com/apps/)页面并登录。 如果还没有 Facebook 帐户，请使用登录页上的 "**注册 Facebook** " 链接创建一个。  获得 Facebook 帐户后，请按照说明注册为 Facebook 开发人员。

* 从 "**我的应用**" 菜单中，选择 "**创建应用**" 以创建新的应用 ID。

   ![Facebook for 开发人员门户在 Microsoft Edge 中打开](index/_static/FBMyApps.png)

* 填写表单，然后点击 "**创建应用 ID** " 按钮。

  ![创建新的应用 ID 窗体](index/_static/FBNewAppId.png)

* 在 "新建应用" 卡中，选择 "**添加产品**"。  在**Facebook 登录**卡上，单击 "**设置**" 

  ![产品设置页](index/_static/FBProductSetup.png)

* **快速入门**向导会启动，并**选择一个平台**作为第一页。 现在，通过单击左下方菜单中的 " **FaceBook 登录** **设置**" 链接，绕过向导：

  ![跳过快速入门](index/_static/FBSkipQuickStart.png)

* 将显示 "**客户端 OAuth 设置**" 页：

  !["客户端 OAuth 设置" 页](index/_static/FBOAuthSetup.png)

* 输入包含 */signin-facebook*的开发 URI，并将其追加到 "**有效的 OAuth 重定向 uri** " 字段中（例如： `https://localhost:44320/signin-facebook`）。 稍后在本教程中配置的 Facebook 身份验证将自动处理 */signin-facebook*路由中的请求以实现 OAuth 流。

> [!NOTE]
> URI */signin-facebook*设置为 facebook 身份验证提供程序的默认回调。 通过[FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Facebook 身份验证中间件时，可以更改默认的回叫 URI。

* 单击 "**保存更改**"。

* 单击左侧导航栏中的 "**设置**" > **基本**"链接。

  在此页上，记下你的 `App ID` 和 `App Secret`。 在下一部分中，你将同时添加到 ASP.NET Core 应用程序：

* 部署站点时，需要重新访问**Facebook 登录**设置页面并注册新的公共 URI。

## <a name="store-facebook-app-id-and-app-secret"></a>存储 Facebook 应用 ID 和应用机密

使用[机密管理器](xref:security/app-secrets)将 Facebook `App ID` 和 `App Secret` 等敏感设置链接到应用程序配置。 对于本教程，请将令牌命名 `Authentication:Facebook:AppId` 和 `Authentication:Facebook:AppSecret`。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

执行以下命令，以安全地存储使用机密管理器 `App ID` 和 `App Secret`：

```dotnetcli
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>配置 Facebook 身份验证

在*Startup.cs*文件的 `ConfigureServices` 方法中添加 Facebook 服务：

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

有关 Facebook 身份验证支持的配置选项的详细信息，请参阅[FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API 参考。 配置选项可用于：

* 请求有关用户的其他信息。
* 添加查询字符串参数以自定义登录体验。

## <a name="sign-in-with-facebook"></a>用 Facebook 登录

运行应用程序，并单击 "**登录"** 。 你会看到一个使用 Facebook 登录的选项。

![Web 应用程序：未通过身份验证的用户](index/_static/DoneFacebook.png)

单击**facebook**时，会重定向到 facebook 进行身份验证：

![Facebook 身份验证页](index/_static/FBLogin.png)

默认情况下，Facebook 身份验证请求公用配置文件和电子邮件地址：

![Facebook 身份验证页面同意屏幕](index/_static/FBLoginDone.png)

输入 Facebook 凭据后，会重定向回到你可以在其中设置电子邮件的站点。

你现在已使用 Facebook 凭据登录：

![Web 应用程序：用户已进行身份验证](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>故障排除

* **仅 ASP.NET Core 2.x：** 如果未通过在 `ConfigureServices`中调用 `services.AddIdentity` 来配置标识，则尝试进行身份验证将导致*ArgumentException：必须提供 "SignInScheme" 选项*。 本教程中使用的项目模板可确保完成此操作。
* 如果尚未通过应用初始迁移来创建站点数据库，则在处理请求错误时，将会出现*数据库操作失败*的情况。 点击 "**应用迁移**" 以创建数据库，然后单击 "刷新" 以继续出现错误。

## <a name="next-steps"></a>后续步骤

* 本文演示了如何通过 Facebook 进行身份验证。 您可以遵循类似的方法向[前一页](xref:security/authentication/social/index)上列出的其他提供程序进行身份验证。

* 将网站发布到 Azure web 应用后，应重置 Facebook 开发人员门户中的 `AppSecret`。

* 将 `Authentication:Facebook:AppId` 和 `Authentication:Facebook:AppSecret` 设置为 Azure 门户中的应用程序设置。 配置系统设置为从环境变量读取密钥。
