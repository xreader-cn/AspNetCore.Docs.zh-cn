---
title: 在 ASP.NET Core Facebook 外部登录安装程序
author: rick-anderson
description: 代码示例演示的现有 ASP.NET Core 应用程序的 Facebook 帐户用户身份验证集成的教程。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/04/2019
uid: security/authentication/facebook-logins
ms.openlocfilehash: 84b891f6cee71a26726d49a9d42ae45007d2b429
ms.sourcegitcommit: 258a97159da206f9009f23fdf6f8fa32f178e50b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2019
ms.locfileid: "59425031"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>在 ASP.NET Core Facebook 外部登录安装程序

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

本教程中的使用代码示例演示如何让用户使用其使用示例 ASP.NET Core 2.0 项目上创建的 Facebook 帐户登录[上一页](xref:security/authentication/social/index)。 我们先按照创建 Facebook 应用程序 ID[官方步骤](https://developers.facebook.com)。

## <a name="create-the-app-in-facebook"></a>在 Facebook 中创建应用

* 导航到[Facebook 开发人员应用](https://developers.facebook.com/apps/)页上，并登录。 如果还没有 Facebook 帐户，使用**注册适用于 Facebook**上登录页后，可以创建一个链接。

* 点击**添加新的应用**中创建新的应用程序 id。 在右上角的按钮

   ![Microsoft Edge 中打开的 Facebook 开发人员门户](index/_static/FBMyApps.png)

* 填写表单，然后点击**创建应用程序 ID**按钮。

  ![创建新的应用 ID 窗体](index/_static/FBNewAppId.png)

* 上**选择一个产品**页上，单击**Set Up**上**Facebook 登录**卡。

  ![产品安装程序页](index/_static/FBProductSetup.png)

* **快速入门**向导将启动与**选择一个平台**作为第一页。 现在跳过该向导，通过单击**设置**在左侧菜单中的链接：

  ![跳过快速入门](index/_static/FBSkipQuickStart.png)

* 此时会显示**客户端 OAuth 设置**页：

  ![客户端 OAuth 设置页](index/_static/FBOAuthSetup.png)

* 输入你的开发 URI 与 */signin-facebook*追加到**有效的 OAuth 重定向 Uri**字段 (例如： `https://localhost:44320/signin-facebook`)。 本教程中稍后配置 Facebook 身份验证将自动处理在请求 */signin-facebook*路由实现 OAuth 流。

> [!NOTE]
> URI */signin-facebook*设置为 Facebook 身份验证提供程序的默认回调。 配置 Facebook 身份验证中间件通过继承时，可以更改默认的回调 URI [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)的属性[FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)类。

* 单击**保存更改**。

* 单击**设置** > **基本**左侧导航窗格中的链接。

  在此页上，记下你`App ID`和你`App Secret`。 你将添加到 ASP.NET Core 应用程序下一节中：

* 部署站点时需要重新访问**Facebook 登录**安装页并注册一个新的公共 URI。

## <a name="store-facebook-app-id-and-app-secret"></a>应用商店 Facebook 应用程序 ID 和应用程序密码

链接敏感设置，例如 Facebook`App ID`并`App Secret`到应用程序的配置使用[机密管理器](xref:security/app-secrets)。 对于本教程的目的，命名为令牌`Authentication:Facebook:AppId`和`Authentication:Facebook:AppSecret`。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

执行以下命令来安全地存储`App ID`和`App Secret`使用机密管理器：

```console
dotnet user-secrets set Authentication:Facebook:AppId <app-id>
dotnet user-secrets set Authentication:Facebook:AppSecret <app-secret>
```

## <a name="configure-facebook-authentication"></a>配置 Facebook 身份验证

::: moniker range=">= aspnetcore-2.0"

将 Facebook 服务中的添加`ConfigureServices`中的方法*Startup.cs*文件：

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

安装[Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook)包。

* 若要使用 Visual Studio 2017 中安装此包，请右键单击项目并选择**管理 NuGet 包**。
* 若要使用.NET Core CLI 安装，请在项目目录中执行以下命令：

   `dotnet add package Microsoft.AspNetCore.Authentication.Facebook`

添加在到 Facebook 中间件`Configure`中的方法*Startup.cs*文件：

```csharp
app.UseFacebookAuthentication(new FacebookOptions()
{
    AppId = Configuration["Authentication:Facebook:AppId"],
    AppSecret = Configuration["Authentication:Facebook:AppSecret"]
});
```

::: moniker-end

请参阅[FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) Facebook 身份验证支持的配置选项的详细信息的 API 参考。 配置选项可用于：

* 请求有关用户的不同信息。
* 添加查询字符串参数以自定义登录体验。

## <a name="sign-in-with-facebook"></a>使用 Facebook 登录

运行你的应用程序，然后单击**登录**。 请参阅使用 Facebook 登录的选项。

![Web 应用程序：用户未经过身份验证](index/_static/DoneFacebook.png)

当您单击**Facebook**，你将重定向到 Facebook 进行身份验证：

![Facebook 身份验证页面](index/_static/FBLogin.png)

Facebook 身份验证请求的默认公共配置文件和电子邮件地址：

![Facebook 身份验证页许可屏幕](index/_static/FBLoginDone.png)

输入你的 Facebook 凭据后你重定向回你的站点，你可以设置你的电子邮件。

现在已在使用 Facebook 凭据登录：

![Web 应用程序：用户通过身份验证](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>疑难解答

* **ASP.NET Core 仅限 2.x:** 如果不通过调用配置标识`services.AddIdentity`中`ConfigureServices`，尝试进行身份验证将导致*ArgumentException:必须提供 SignInScheme 选项*。 在本教程中使用的项目模板可确保，此操作。
* 如果尚未通过应用初始迁移创建站点数据库，则获取*处理请求时，数据库操作失败*错误。 点击**应用迁移**创建数据库，并刷新以忽略错误继续。

## <a name="next-steps"></a>后续步骤

* 添加[Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook)到 Facebook 身份验证的高级方案的项目的 NuGet 包。 此包不需要将 Facebook 外部登录功能与您的应用程序集成。 

* 本文介绍了您如何可以使用 Facebook 进行验证。 可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。

* 一旦您将您的网站发布到 Azure web 应用时，应重置`AppSecret`Facebook 开发人员门户中。

* 设置`Authentication:Facebook:AppId`和`Authentication:Facebook:AppSecret`作为在 Azure 门户中的应用程序设置。 配置系统设置以从环境变量读取密钥。
