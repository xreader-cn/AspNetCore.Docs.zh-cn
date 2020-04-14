---
title: 微软帐户外部登录设置与ASP.NET核心
author: rick-anderson
description: 此示例演示了 Microsoft 帐户用户身份验证集成到现有ASP.NET核心应用。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 32315267e0672b0747917228f08591a15e4449f8
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277244"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a>微软帐户外部登录设置与ASP.NET核心

作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

此示例演示如何允许用户使用[上一页上](xref:security/authentication/social/index)创建的ASP.NET Core 3.0 项目使用其 Microsoft 帐户登录。

## <a name="create-the-app-in-microsoft-developer-portal"></a>在 Microsoft 开发人员门户中创建应用

* 将[Microsoft.AspNetCore.身份验证.Microsoft 帐户](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/)NuGet 包添加到项目中。
* 导航到[Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页面，然后创建或登录 Microsoft 帐户：

如果您没有 Microsoft 帐户，请选择"**创建一个**"。 登录后，您将重定向到**应用注册**页面：

* 选择**新注册**
* 输入“名称”****。
* 为**受支持的帐户类型**选择一个选项。  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* 在**重定向 URI**下，输入追加`/signin-microsoft`的开发 URL。 例如，`https://localhost:5001/signin-microsoft` 。 本示例中稍后配置的 Microsoft 身份验证方案将自动处理路由中`/signin-microsoft`实现 OAuth 流的请求。
* 选择 **"注册"**

### <a name="create-client-secret"></a>创建客户端机密

* 在左窗格中，选择“证书和机密”****。
* 在 **"客户端机密**"下，选择 **"新客户端机密**"

  * 添加客户端机密的说明。
  * 选择"**添加**"按钮。

* 在**客户端机密**下，复制客户端机密的值。

URI 段`/signin-microsoft`设置为 Microsoft 身份验证提供程序的默认回调。 您可以通过 Microsoft[帐户选项](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)类的继承[远程身份验证选项.callbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Microsoft 身份验证中间件时更改默认回调 URI。

## <a name="store-the-microsoft-client-id-and-secret"></a>存储 Microsoft 客户端 ID 和机密

使用[密钥管理器](xref:security/app-secrets)存储敏感设置，如 Microsoft 客户端 ID 和机密值。 对于此示例，请使用以下步骤：

1. 根据[启用密钥存储](xref:security/app-secrets#enable-secret-storage)的指令初始化了机密存储的项目。
1. 使用密钥`Authentication:Microsoft:ClientId`和 将敏感设置存储在本地机密存储中`Authentication:Microsoft:ClientSecret`：

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a>配置 Microsoft 帐户身份验证

将 Microsoft 帐户服务添加到`Startup.ConfigureServices`：

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

有关 Microsoft 帐户身份验证支持的配置选项的详细信息，请参阅[Microsoft 帐户选项](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)API 参考。 这可用于请求有关用户的不同信息。

## <a name="sign-in-with-microsoft-account"></a>使用 Microsoft 帐户登录

运行应用并单击"**登录**"。 将显示一个与 Microsoft 登录的选项。 单击 Microsoft 时，您将重定向到 Microsoft 进行身份验证。 使用 Microsoft 帐户登录后，系统将提示您让应用访问您的信息：

点按 **"是**"，您将被重定向回网站，您可以在该网站设置电子邮件。

您现在使用 Microsoft 凭据登录：

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a>故障排除

* 如果 Microsoft 帐户提供程序将您重定向到登录错误页，请记下错误标题和说明查询字符串参数，直接遵循 Uri`#`中的 （hashtag）。

  尽管错误消息似乎表明 Microsoft 身份验证有问题，但最常见的原因是应用程序 Uri 与为**Web**平台指定的重定向**URI**不匹配。
* 如果未通过调用`services.AddIdentity`配置标识`ConfigureServices`，则尝试身份验证将导致*参数异常：必须提供"SignInScheme"选项*。 此示例中使用的项目模板可确保完成此操作。
* 如果尚未通过应用初始迁移创建站点数据库，则在处理请求错误时，将获取*数据库操作失败*。 点按 **"应用迁移**"以创建数据库并刷新以继续结束错误。

## <a name="next-steps"></a>后续步骤

* 本文展示了如何使用 Microsoft 进行身份验证。 您可以采用类似的方法对[上一页](xref:security/authentication/social/index)中列出的其他提供程序进行身份验证。

* 将网站发布到 Azure Web 应用后，在 Microsoft 开发人员门户中创建新的客户端机密。

* 在`Authentication:Microsoft:ClientId`Azure`Authentication:Microsoft:ClientSecret`门户中将 和 设置为应用程序设置。 配置系统设置为从环境变量读取密钥。
