---
title: 帐户确认和 ASP.NET Core 中的密码恢复
author: rick-anderson
description: 了解如何生成使用电子邮件确认及密码重置功能的 ASP.NET Core 应用程序。
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 802ba446af04df6a35ac73187ad693b8ec80c654
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67814836"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a>帐户确认和 ASP.NET Core 中的密码恢复

通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Ponant](https://github.com/Ponant)，和[Joe Audette](https://twitter.com/joeaudette)

本教程演示如何生成 ASP.NET Core 应用使用电子邮件确认及密码重置。 本教程是**不**开头主题。 您应熟悉：

* [ASP.NET Core](xref:tutorials/razor-pages/razor-pages-start)
* [身份验证](xref:security/authentication/identity)
* [Entity Framework Core](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

请参阅[此 PDF 文件](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)ASP.NET Core 1.1 版本。

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a>系统必备

[.NET core 3.0 SDK 或更高版本](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a>创建和测试使用身份验证的 web 应用

运行以下命令以创建具有身份验证的 web 应用。

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

运行该应用程序中，选择**注册**链接，并注册用户。 注册后，你将重定向到到`/Identity/Account/RegisterConfirmation`页其中包含用于模拟电子邮件确认的链接：

* 选择`Click here to confirm your account`链接。
* 选择**登录名**链接并使用相同的凭据登录。
* 选择`Hello YourEmail@provider.com!`链接，它将重定向到`/Identity/Account/Manage/PersonalData`页。
* 选择**个人数据**在左侧选项卡，然后选择**删除**。

### <a name="configure-an-email-provider"></a>配置电子邮件提供程序

在本教程中， [SendGrid](https://sendgrid.com)用于发送电子邮件。 你需要一个 SendGrid 帐户和密钥用于发送电子邮件。 可以使用其他电子邮件提供商。 我们建议你使用 SendGrid 或另一个电子邮件服务发送电子邮件。 SMTP 很难保护并正确设置。

创建一个类来提取的安全电子邮件键。 对于此示例中，创建*Services/AuthMessageSenderOptions.cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>配置 SendGrid 用户机密

设置`SendGridUser`并`SendGridKey`与[机密管理器工具](xref:security/app-secrets)。 例如：

```console
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

在 Windows 中，机密管理器存储中的键/值对*secrets.json*文件中`%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`目录。

内容*secrets.json*未加密文件。 下面的标记演示*secrets.json*文件。 `SendGridKey`删除值。

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

有关详细信息，请参阅[选项模式](xref:fundamentals/configuration/options)并[配置](xref:fundamentals/configuration/index)。

### <a name="install-sendgrid"></a>安装 SendGrid

本教程演示如何添加通过电子邮件通知[SendGrid](https://sendgrid.com/)，但是你可以发送电子邮件使用 SMTP 和其他机制。

安装`SendGrid`NuGet 包：

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

从包管理器控制台中，输入以下命令：

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

从控制台中，输入以下命令：

```cli
dotnet add package SendGrid
```

---

请参阅[免费开始使用 SendGrid](https://sendgrid.com/free/)注册一个免费的 SendGrid 帐户。

### <a name="implement-iemailsender"></a>实现 IEmailSender

为实现`IEmailSender`，创建*Services/EmailSender.cs* ，类似于以下代码：

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>将启动以支持电子邮件配置

将以下代码添加到`ConfigureServices`中的方法*Startup.cs*文件：

* 添加`EmailSender`作为暂时性服务。
* 注册`AuthMessageSenderOptions`配置实例。

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a>注册、 确认电子邮件，和重置密码

运行 web 应用和测试的帐户确认和密码恢复流。

* 运行应用并注册一个新用户
* 检查你的帐户确认链接的电子邮件。 请参阅[调试电子邮件](#debug)如果没有收到电子邮件。
* 单击链接以确认你的电子邮件。
* 使用你的电子邮件和密码登录。
* 注销。

### <a name="test-password-reset"></a>测试密码重置

* 如果你要登录，请选择**注销**。
* 选择**登录**链接，然后选择**忘记了密码？** 链接。
* 输入用于注册该帐户的电子邮件。
* 发送一封电子邮件包含用于重置密码的链接。 检查你的电子邮件，并单击链接以重置密码。 已成功重置密码后，你可以使用你的电子邮件和新密码登录。

## <a name="change-email-and-activity-timeout"></a>更改电子邮件和活动的超时值

默认处于非活动状态超时值为 14 天。 下面的代码设置为 5 天的非活动超时：

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>更改所有数据保护令牌期限

下面的代码更改为 3 个小时的所有数据保护令牌超时期限：

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

内置中标识的用户令牌 (请参阅[AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) 有[一天超时](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。

### <a name="change-the-email-token-lifespan"></a>更改电子邮件令牌有效期

默认令牌有效期[标识的用户令牌](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)是[有一天](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。 本部分演示如何更改电子邮件令牌有效期。

添加自定义[DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)和<xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

将自定义提供程序添加到服务容器：

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a>重新发送电子邮件确认

请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/5410)。

<a name="debug"></a>

### <a name="debug-email"></a>调试电子邮件

如果无法获取电子邮件工作：

* 中设置断点`EmailSender.Execute`若要验证`SendGridClient.SendEmailAsync`调用。
* 创建[控制台应用以发送电子邮件](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)使用类似的代码，到`EmailSender.Execute`。
* 审阅[电子邮件活动](https://sendgrid.com/docs/User_Guide/email_activity.html)页。
* 请检查垃圾邮件文件夹。
* 请尝试其他电子邮件提供程序 （Microsoft、 Yahoo、 Gmail 等） 上的另一个电子邮件别名
* 尝试发送到不同的电子邮件帐户。

**最佳安全方案**设置为**不**中使用生产机密中测试和开发。 如果将应用发布到 Azure，以在 Azure Web 应用门户中的应用程序设置设置 SendGrid 机密。 配置系统设置以从环境变量读取密钥。

## <a name="combine-social-and-local-login-accounts"></a>合并的社交和本地登录帐户

若要完成本部分中，必须先启用外部身份验证提供程序。 请参阅[Facebook、 Google、 和外部提供程序身份验证](xref:security/authentication/social/index)。

您可以通过单击电子邮件链接组合本地和社交帐户。 按以下顺序"RickAndMSFT@gmail.com"首次创建为本地登录名; 但是，您可以创建帐户为社交登录名，然后添加本地登录名。

![Web 应用程序：RickAndMSFT@gmail.com用户通过身份验证](accconfirm/_static/rick.png)

单击**管理**链接。 请注意与此帐户关联的 0 外部 （社交登录名）。

![管理视图](accconfirm/_static/manage.png)

单击链接到另一个登录名服务，接受应用程序请求。 下图中，在 Facebook 是外部身份验证提供程序：

![管理你的外部登录名视图，其中列出 Facebook](accconfirm/_static/fb.png)

已合并两个帐户。 你将能够使用任一帐户登录。 您可能希望用户其社交登录身份验证服务发生故障，或已为其社交帐户无法访问更有可能的情况下添加本地帐户。

## <a name="enable-account-confirmation-after-a-site-has-users"></a>后一个站点中有用户启用帐户确认

与用户启用帐户确认站点上的阻止所有现有用户。 现有用户被锁定，因为不确认其帐户。 若要解决现有用户锁定，请使用以下方法之一：

* 更新要将标记为正在确认的所有现有用户的数据库。
* 确认现有用户。 例如，批的发送确认链接的电子邮件。

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a>系统必备

[.NET core 2.2 SDK 或更高版本](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a>创建 web 应用并创建标识的基架

运行以下命令以创建具有身份验证的 web 应用。

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a>测试新的用户注册

运行该应用程序中，选择**注册**链接，并注册用户。 在此情况下，电子邮件的唯一验证是使用[[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute)属性。 提交后注册，登录到应用程序。 更高版本在本教程中，以便新用户不能登录，直到其电子邮件验证更新代码。

[!INCLUDE[](~/includes/view-identity-db.md)]

请注意此表的`EmailConfirmed`字段是`False`。

您可能想要再次在下一步中使用此电子邮件，该应用将发送确认电子邮件时。 右键单击行并选择**删除**。 删除电子邮件别名便于您在以下步骤。

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a>需要电子邮件确认

它是最佳做法以确认新的用户注册的电子邮件地址。 电子邮件确认可帮助以验证它们没有模拟其他人 （即，他们尚未注册与其他人的电子邮件）。 假设有论坛，并且您想阻止"yli@example.com"中注册为"nolivetto@contoso.com"。 而无需电子邮件确认"nolivetto@contoso.com"可能会从您的应用程序收到不需要的电子邮件。 假设用户意外地注册为"ylo@example.com"和"yli"的拼写错误的重视。 它们将无法使用密码恢复，因为此应用没有其正确的电子邮件。 电子邮件确认从智能机器人提供有限的保护。 电子邮件确认不提供保护，免受恶意用户的与许多电子邮件帐户。

你通常想要发布到网站的任何数据，有已确认的电子邮件之前阻止新用户。

更新`Startup.ConfigureServices`需要确认电子邮件：

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

`config.SignIn.RequireConfirmedEmail = true;` 可防止已注册的用户登录之前确认其电子邮件。

### <a name="configure-email-provider"></a>配置电子邮件提供商

在本教程中， [SendGrid](https://sendgrid.com)用于发送电子邮件。 你需要一个 SendGrid 帐户和密钥用于发送电子邮件。 可以使用其他电子邮件提供商。 ASP.NET Core 2.x 包括`System.Net.Mail`，这允许你从你的应用程序发送电子邮件。 我们建议你使用 SendGrid 或另一个电子邮件服务发送电子邮件。 SMTP 很难保护并正确设置。

创建一个类来提取的安全电子邮件键。 对于此示例中，创建*Services/AuthMessageSenderOptions.cs*:

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a>配置 SendGrid 用户机密

设置`SendGridUser`并`SendGridKey`与[机密管理器工具](xref:security/app-secrets)。 例如:

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

在 Windows 中，机密管理器存储中的键/值对*secrets.json*文件中`%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`目录。

内容*secrets.json*未加密文件。 下面的标记演示*secrets.json*文件。 `SendGridKey`删除值。

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

有关详细信息，请参阅[选项模式](xref:fundamentals/configuration/options)并[配置](xref:fundamentals/configuration/index)。

### <a name="install-sendgrid"></a>安装 SendGrid

本教程演示如何添加通过电子邮件通知[SendGrid](https://sendgrid.com/)，但是你可以发送电子邮件使用 SMTP 和其他机制。

安装`SendGrid`NuGet 包：

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

从包管理器控制台中，输入以下命令：

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

从控制台中，输入以下命令：

```cli
dotnet add package SendGrid
```

---

请参阅[免费开始使用 SendGrid](https://sendgrid.com/free/)注册一个免费的 SendGrid 帐户。

### <a name="implement-iemailsender"></a>实现 IEmailSender

为实现`IEmailSender`，创建*Services/EmailSender.cs* ，类似于以下代码：

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a>将启动以支持电子邮件配置

将以下代码添加到`ConfigureServices`中的方法*Startup.cs*文件：

* 添加`EmailSender`作为暂时性服务。
* 注册`AuthMessageSenderOptions`配置实例。

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a>启用帐户确认和密码恢复

该模板具有帐户确认和密码恢复的代码。 查找`OnPostAsync`中的方法*Areas/Identity/Pages/Account/Register.cshtml.cs*。

阻止新注册的用户自动登录通过注释掉以下行：

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

完整的方法与更改突出显示的行所示：

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a>注册、 确认电子邮件，和重置密码

运行 web 应用和测试的帐户确认和密码恢复流。

* 运行应用并注册一个新用户
* 检查你的帐户确认链接的电子邮件。 请参阅[调试电子邮件](#debug)如果没有收到电子邮件。
* 单击链接以确认你的电子邮件。
* 使用你的电子邮件和密码登录。
* 注销。

### <a name="view-the-manage-page"></a>查看管理页

在浏览器中选择你的用户名：![用户名的浏览器窗口](accconfirm/_static/un.png)

管理页显示与**配置文件**选定的选项卡。 **电子邮件**显示已确认一个复选框，表明该电子邮件。

### <a name="test-password-reset"></a>测试密码重置

* 如果你要登录，请选择**注销**。
* 选择**登录**链接，然后选择**忘记了密码？** 链接。
* 输入用于注册该帐户的电子邮件。
* 发送一封电子邮件包含用于重置密码的链接。 检查你的电子邮件，并单击链接以重置密码。 已成功重置密码后，你可以使用你的电子邮件和新密码登录。

## <a name="change-email-and-activity-timeout"></a>更改电子邮件和活动的超时值

默认处于非活动状态超时值为 14 天。 下面的代码设置为 5 天的非活动超时：

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a>更改所有数据保护令牌期限

下面的代码更改为 3 个小时的所有数据保护令牌超时期限：

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

内置中标识的用户令牌 (请参阅[AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) 有[一天超时](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。

### <a name="change-the-email-token-lifespan"></a>更改电子邮件令牌有效期

默认令牌有效期[标识的用户令牌](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)是[有一天](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。 本部分演示如何更改电子邮件令牌有效期。

添加自定义[DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)和<xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

将自定义提供程序添加到服务容器：

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a>重新发送电子邮件确认

请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/5410)。

<a name="debug"></a>

### <a name="debug-email"></a>调试电子邮件

如果无法获取电子邮件工作：

* 中设置断点`EmailSender.Execute`若要验证`SendGridClient.SendEmailAsync`调用。
* 创建[控制台应用以发送电子邮件](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)使用类似的代码，到`EmailSender.Execute`。
* 审阅[电子邮件活动](https://sendgrid.com/docs/User_Guide/email_activity.html)页。
* 请检查垃圾邮件文件夹。
* 请尝试其他电子邮件提供程序 （Microsoft、 Yahoo、 Gmail 等） 上的另一个电子邮件别名
* 尝试发送到不同的电子邮件帐户。

**最佳安全方案**设置为**不**中使用生产机密中测试和开发。 如果将应用发布到 Azure，可以为 Azure Web 应用门户中的应用程序设置来设置 SendGrid 机密。 配置系统设置以从环境变量读取密钥。

## <a name="combine-social-and-local-login-accounts"></a>合并的社交和本地登录帐户

若要完成本部分中，必须先启用外部身份验证提供程序。 请参阅[Facebook、 Google、 和外部提供程序身份验证](xref:security/authentication/social/index)。

您可以通过单击电子邮件链接组合本地和社交帐户。 按以下顺序"RickAndMSFT@gmail.com"首次创建为本地登录名; 但是，您可以创建帐户为社交登录名，然后添加本地登录名。

![Web 应用程序：RickAndMSFT@gmail.com用户通过身份验证](accconfirm/_static/rick.png)

单击**管理**链接。 请注意与此帐户关联的 0 外部 （社交登录名）。

![管理视图](accconfirm/_static/manage.png)

单击链接到另一个登录名服务，接受应用程序请求。 下图中，在 Facebook 是外部身份验证提供程序：

![管理你的外部登录名视图，其中列出 Facebook](accconfirm/_static/fb.png)

已合并两个帐户。 你将能够使用任一帐户登录。 您可能希望用户其社交登录身份验证服务发生故障，或已为其社交帐户无法访问更有可能的情况下添加本地帐户。

## <a name="enable-account-confirmation-after-a-site-has-users"></a>后一个站点中有用户启用帐户确认

与用户启用帐户确认站点上的阻止所有现有用户。 现有用户被锁定，因为不确认其帐户。 若要解决现有用户锁定，请使用以下方法之一：

* 更新要将标记为正在确认的所有现有用户的数据库。
* 确认现有用户。 例如，批的发送确认链接的电子邮件。

::: moniker-end
