---
title: ASP.NET核心中的 Facebook 外部登录设置
author: rick-anderson
description: 包含代码示例的教程，演示 Facebook 帐户用户身份验证集成到现有ASP.NET核心应用。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: 9b3128addafb41ad6ec44af5cb12e89607e1ae59
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277296"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a>ASP.NET核心中的 Facebook 外部登录设置

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

<!-- per @rick-anderson and scott addie, don't update images. Remove images and point the customer to the FB set up page. FB needs to maintain  instructions to get key and secret.
-->

本教程包含代码示例演示如何允许用户使用[上一页上](xref:security/authentication/social/index)创建的示例ASP.NET Core 3.0 项目使用 Facebook 帐户登录。 我们首先按照[官方步骤](https://developers.facebook.com)创建 Facebook 应用程序 ID。

## <a name="create-the-app-in-facebook"></a>在 Facebook 中创建应用

* 将[Microsoft.AspNetCore.身份验证.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet 包添加到项目中。

* 导航到[Facebook 开发人员应用](https://developers.facebook.com/apps/)页面并登录。 如果您还没有 Facebook 帐户，请使用登录页面上**的"注册 Facebook"** 链接创建一个帐户。  拥有 Facebook 帐户后，请按照说明注册为 Facebook 开发人员。

* 从"**我的应用"** 菜单中选择 **"创建应用**"以创建新的应用 ID。

   ![Facebook 面向开发人员门户在微软边缘打开](index/_static/FBMyApps.png)

* 填写表单并点击"**创建应用 ID"** 按钮。

  ![创建新的应用 ID 窗体](index/_static/FBNewAppId.png)

* 在新应用卡上，选择 **"添加产品**"。  在**Facebook 登录**卡上，单击"**设置"** 

  ![产品设置页面](index/_static/FBProductSetup.png)

* **"快速入门**"向导以 **"选择平台**作为第一页"启动。 单击左下角菜单中的**FaceBook 登录****设置**链接，立即绕过向导：

  ![跳过快速入门](index/_static/FBSkipQuickStart.png)

* 将显示"**客户端设置"** 页面：

  ![客户端 Auth 设置页面](index/_static/FBOAuthSetup.png)

* 输入您的开发*URI，将 /signin-facebook*追加到**有效 OAuth 重定向 URI**字段`https://localhost:44320/signin-facebook`（例如： 本教程稍后配置的 Facebook 身份验证将自动处理 */signin-facebook*路由上的请求，以实现 OAuth 流。

> [!NOTE]
> URI */signin-facebook*设置为 Facebook 身份验证提供商的默认回调。 您可以通过继承的["远程身份验证选项.CallbackPath"](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Facebook 身份验证中间件，同时更改默认回调 URI。 [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)

* 单击“保存更改”****。

* 单击左侧导航中的 **"设置** > **基本"** 链接。

  在此页上，记下您和您的`App ID``App Secret`。 在下一节中，您将将两者添加到ASP.NET核心应用程序中：

* 部署网站时，您需要重新访问**Facebook 登录**设置页面并注册新的公共 URI。

## <a name="store-the-facebook-app-id-and-secret"></a>存储 Facebook 应用 ID 和机密

使用[秘密管理器](xref:security/app-secrets)存储敏感设置，如 Facebook 应用 ID 和机密值。 对于此示例，请使用以下步骤：

1. 根据[启用密钥存储](xref:security/app-secrets#enable-secret-storage)的指令初始化了机密存储的项目。
1. 使用密钥`Authentication:Facebook:AppId`和 将敏感设置存储在本地机密存储中`Authentication:Facebook:AppSecret`：

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a>配置 Facebook 身份验证

在`ConfigureServices`*Startup.cs*文件中的方法中添加 Facebook 服务：

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

## <a name="sign-in-with-facebook"></a>使用 Facebook 登录

* 运行应用并选择 **"登录**"。 
* 在 **"使用其他服务登录"** 下，选择 Facebook。
* 您将被重定向到**Facebook**进行身份验证。
* 输入您的 Facebook 凭据。
* 您将被重定向回您的网站，您可以在其中设置电子邮件。

您现在使用 Facebook 凭据登录：

<a name="react"></a>

## <a name="react-to-cancel-authorize-external-sign-in"></a>响应取消授权外部登录

<xref:Microsoft.AspNetCore.Authentication.RemoteAuthenticationOptions.AccessDeniedPath>当用户不批准请求的授权请求时，可以为用户代理提供重定向路径。

以下代码将 集`AccessDeniedPath` `"/AccessDeniedPathInfo"`：

[!code-csharp[](~/security/authentication/social/social-code/StartupAccessDeniedPath.cs?name=snippetFB)]

我们建议该`AccessDeniedPath`页面包含以下信息：

*  远程身份验证已取消。
* 此应用程序需要身份验证。
* 要再次尝试登录，请选择"登录"链接。

### <a name="test-accessdeniedpath"></a>测试访问拒绝路径

* 导航到[facebook.com](https://www.facebook.com/)
* 如果您已登录，则必须注销。
* 运行应用并选择 Facebook 登录。
* 选择 **"现在不**"。 您将重定向到指定的`AccessDeniedPath`页面。

<!-- End of React  -->
[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

有关 Facebook 身份验证支持的配置选项的详细信息，请参阅[Facebook 选项](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions)API 参考。 配置选项可用于：

* 请求有关用户的不同信息。
* 添加查询字符串参数以自定义登录体验。

## <a name="troubleshooting"></a>故障排除

* **仅ASP.NET核心 2.x：** 如果未通过调用`services.AddIdentity`配置标识`ConfigureServices`，则尝试身份验证将导致*参数异常：必须提供"SignInScheme"选项*。 本教程中使用的项目模板可确保完成此操作。
* 如果尚未通过应用初始迁移创建站点数据库，则在处理请求错误时，将获取*数据库操作失败*。 点按 **"应用迁移**"以创建数据库并刷新以继续结束错误。

## <a name="next-steps"></a>后续步骤

* 本文展示了如何使用 Facebook 进行身份验证。 您可以采用类似的方法对[上一页](xref:security/authentication/social/index)中列出的其他提供程序进行身份验证。

* 将网站发布到 Azure Web 应用后，应重置 Facebook`AppSecret`开发人员门户中的 。

* 在`Authentication:Facebook:AppId`Azure`Authentication:Facebook:AppSecret`门户中将 和 设置为应用程序设置。 配置系统设置为从环境变量读取密钥。
