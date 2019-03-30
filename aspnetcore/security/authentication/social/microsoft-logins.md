---
title: 使用 ASP.NET Core 的 Microsoft 帐户外部登录设置
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用程序的 Microsoft 帐户用户身份验证。
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2018
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 1733d049d6752c24d7749b5b5ae2a4b866492358
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750997"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>使用 ASP.NET Core 的 Microsoft 帐户外部登录设置

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

本教程演示如何使用户可以使用示例 ASP.NET Core 2.0 项目上创建其 Microsoft 帐户登录[上一页](xref:security/authentication/social/index)。

## <a name="create-the-app-in-microsoft-developer-portal"></a>在 Microsoft 开发人员门户中创建应用

* 导航到[ https://apps.dev.microsoft.com ](https://apps.dev.microsoft.com)和创建或登录到 Microsoft 帐户：

![登录对话框](index/_static/MicrosoftDevLogin.png)

如果还没有 Microsoft 帐户，请点击 **[创建一个 ！](https://signup.live.com/signup?wa=wsignin1.0&rpsnv=13&ct=1478151035&rver=6.7.6643.0&wp=SAPI_LONG&wreply=https%3a%2f%2fapps.dev.microsoft.com%2fLoginPostBack&id=293053&aadredir=1&contextid=D70D4F21246BAB50&bk=1478151036&uiflavor=web&uaid=f0c3de863a914c358b8dc01b1ff49e85&mkt=EN-US&lc=1033&lic=1)** 在登录后你将重定向到 **我的应用程序** 页：

![Microsoft 开发人员门户中，打开 Microsoft Edge 中](index/_static/MicrosoftDev.png)

* 点击**将应用添加**右上角，然后输入你**应用程序名称**并**联系人电子邮件**:

![新建应用程序注册对话框](index/_static/MicrosoftDevAppCreate.png)

* 对于本教程的目的，清除**指导式设置**复选框。

* 点击**创建**以继续进入**注册**页。 提供**名称**并记下的值**应用程序 Id**，其中使用即`ClientId`在本教程的后面：

![注册页](index/_static/MicrosoftDevAppReg.png)

* 点击**添加平台**中**平台**部分，并选择**Web**平台：

![添加平台对话框](index/_static/MicrosoftDevAppPlatform.png)

* 在新**Web**平台部分中，输入与你开发的 URL`/signin-microsoft`追加到**重定向 Url**字段 (例如： `https://localhost:44320/signin-microsoft`)。 稍后在本教程中配置的 Microsoft 身份验证方案将自动处理在请求`/signin-microsoft`路由实现 OAuth 流：

![Web 平台部分](index/_static/MicrosoftRedirectUri.png)

> [!NOTE]
> URI 段`/signin-microsoft`设置为默认的 Microsoft 身份验证提供程序的回调。 配置 Microsoft 身份验证中间件通过继承时，可以更改默认的回调 URI [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性的[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)类。

* 点击**添加 URL**以确保该 URL 已添加。

* 填写任何其他应用程序设置，如有必要，并点击**保存**底部的页后，可以将更改保存到应用配置。

* 部署站点时将需要重新访问**注册**页，并将新的公共 URL。

## <a name="store-microsoft-application-id-and-password"></a>Microsoft 应用程序 Id 和密码存储

* 请注意`Application Id`上显示**注册**页。

* 点击**生成新密码**中**应用程序机密**部分。 这将显示一个框，您可以在其中复制应用程序密码：

![新建生成的密码对话框](index/_static/MicrosoftDevPassword.png)

链接敏感设置，例如 Microsoft`Application ID`并`Password`到应用程序的配置使用[机密管理器](xref:security/app-secrets)。 对于本教程的目的，命名为令牌`Authentication:Microsoft:ApplicationId`和`Authentication:Microsoft:Password`。

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>配置 Microsoft 帐户身份验证

在本教程中使用的项目模板可确保[Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount)包已安装。

* 若要使用 Visual Studio 2017 中安装此包，请右键单击项目并选择**管理 NuGet 包**。
* 若要使用.NET Core CLI 安装，请在项目目录中执行以下命令：

   `dotnet add package Microsoft.AspNetCore.Authentication.MicrosoftAccount`

::: moniker range=">= aspnetcore-2.0"

添加 Microsoft 帐户服务中的`ConfigureServices`中的方法*Startup.cs*文件：

```csharp
services.AddDefaultIdentity<IdentityUser>()
        .AddDefaultUI(UIFramework.Bootstrap4)
        .AddEntityFrameworkStores<ApplicationDbContext>();

services.AddAuthentication().AddMicrosoftAccount(microsoftOptions =>
{
    microsoftOptions.ClientId = Configuration["Authentication:Microsoft:ApplicationId"];
    microsoftOptions.ClientSecret = Configuration["Authentication:Microsoft:Password"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

添加中的 Microsoft 帐户中间件`Configure`中的方法*Startup.cs*文件：

```csharp
app.UseMicrosoftAccountAuthentication(new MicrosoftAccountOptions()
{
    ClientId = Configuration["Authentication:Microsoft:ApplicationId"],
    ClientSecret = Configuration["Authentication:Microsoft:Password"]
});
```

::: moniker-end

尽管 Microsoft 开发人员门户上使用的术语命名这些令牌`ApplicationId`和`Password`，它们作为公开`ClientId`和`ClientSecret`配置 API。

请参阅[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)支持的 Microsoft 帐户身份验证的配置选项的详细信息的 API 参考。 这可以用于请求有关用户的不同信息。

## <a name="sign-in-with-microsoft-account"></a>使用 Microsoft 帐户登录

运行你的应用程序，然后单击**登录**。 若要使用 Microsoft 登录的选项将显示：

![Web 应用程序日志页中：用户未经过身份验证](index/_static/DoneMicrosoft.png)

当单击 Microsoft 时，将重定向的身份验证到 Microsoft。 （如果尚未登录），使用你的 Microsoft 帐户登录后您将会提示您允许应用访问你的信息：

![Microsoft 身份验证对话框](index/_static/MicrosoftLogin.png)

点击**是**，并且将重定向回 web 站点，你可以设置你的电子邮件。

现在已登录中使用 Microsoft 凭据：

![Web 应用程序：用户通过身份验证](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>疑难解答

* 如果 Microsoft 帐户提供程序将你重定向到登录错误页，请记下错误标题和说明查询字符串后面的参数直接`#`（井号标签） 的 Uri 中。

  错误消息似乎指出了使用 Microsoft 身份验证问题，尽管最常见的原因是你的应用程序 Uri 不匹配的任何**重定向 Uri**指定为**Web**平台.
* **ASP.NET Core 仅限 2.x:** 如果不通过调用配置标识`services.AddIdentity`中`ConfigureServices`，尝试进行身份验证将导致*ArgumentException:必须提供 SignInScheme 选项*。 在本教程中使用的项目模板可确保，此操作。
* 如果尚未通过应用初始迁移创建站点数据库，则会收到*处理请求时，数据库操作失败*错误。 点击**应用迁移**创建数据库，并刷新以忽略错误继续。

## <a name="next-steps"></a>后续步骤

* 本文介绍了如何向 Microsoft。 可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。

* 一旦您将您的网站发布到 Azure web 应用时，应创建一个新`Password`Microsoft 开发人员门户中。

* 设置`Authentication:Microsoft:ApplicationId`和`Authentication:Microsoft:Password`作为在 Azure 门户中的应用程序设置。 配置系统设置以从环境变量读取密钥。
