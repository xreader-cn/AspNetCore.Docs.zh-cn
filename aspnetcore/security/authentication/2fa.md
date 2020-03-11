---
title: 在 ASP.NET Core SMS 的双因素身份验证
author: rick-anderson
description: 了解如何设置双因素身份验证 (2FA) 与 ASP.NET Core 应用。
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
uid: security/authentication/2fa
ms.openlocfilehash: 424d21e446de02b39daa7685205a00931361b326
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652722"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a>在 ASP.NET Core SMS 的双因素身份验证

作者： [Rick Anderson](https://twitter.com/RickAndMSFT)和[瑞士-开发人员](https://github.com/Swiss-Devs)

>[!WARNING]
> 两个身份验证 (2FA) 身份验证器应用程序，使用基于时间的一次性密码算法 (TOTP)，是推荐的方法用于 2FA 的行业。 2FA 使用 TOTP 优于 SMS 2FA。 有关详细信息，请参阅为 ASP.NET Core 2.0 及更高版本[ASP.NET Core 中的 TOTP 验证器应用启用 QR 码生成](xref:security/authentication/identity-enable-qrcodes)。

本教程演示如何设置双因素身份验证 (2FA) 使用短信。 说明适用于[twilio](https://www.twilio.com/)和[ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)，但你可以使用任何其他 SMS 提供程序。 建议你在开始学习本教程之前完成[帐户确认和密码恢复](xref:security/authentication/accconfirm)。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)。 [如何下载](xref:index#how-to-download-a-sample)。

## <a name="create-a-new-aspnet-core-project"></a>创建新的 ASP.NET Core 项目

使用单个用户帐户创建名为 `Web2FA` 的新 ASP.NET Core web 应用。 按照 <xref:security/enforcing-ssl> 中的说明设置和需要 HTTPS。

### <a name="create-an-sms-account"></a>创建 SMS 帐户

创建一个 SMS 帐户，例如[twilio](https://www.twilio.com/)或[ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)。 记录身份验证凭据 (twilio: accountSid 和 authToken 的 ASPSMS： 用户密钥和密码)。

#### <a name="figuring-out-sms-provider-credentials"></a>找出 SMS 提供程序凭据

**Twilio**

在 Twilio 帐户的 "仪表板" 选项卡中，复制 "**帐户 SID** " 和 "**身份验证令牌**"。

**ASPSMS:**

从帐户设置中，导航到**用户密钥**，并将其与**密码**一起复制。

稍后我们将这些值存储在中，密钥管理器工具 `SMSAccountIdentification` 和 `SMSAccountPassword`中。

#### <a name="specifying-senderid--originator"></a>指定 SenderID / 原始发件人

**Twilio：** 从 "数字" 选项卡中，复制 Twilio 的**电话号码**。

**ASPSMS：** 在 "解锁工作项" 菜单中，解锁一个或多个发信方，或选择一个字母数字发信方（并非所有网络都支持）。

稍后我们将在密钥 `SMSAccountFrom`中将此值与机密管理器工具存储在一起。

### <a name="provide-credentials-for-the-sms-service"></a>SMS 服务提供的凭据

我们将使用[Options 模式](xref:fundamentals/configuration/options)来访问用户帐户和密钥设置。

* 创建一个类来提取安全 SMS 项。 对于本示例，在*SMSoptions*文件中创建了 `SMSoptions` 类。

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

用[机密管理器工具](xref:security/app-secrets)设置 `SMSAccountIdentification`、`SMSAccountPassword` 和 `SMSAccountFrom`。 例如：

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* SMS 提供程序添加 NuGet 包。 从包管理器控制台 (PMC) 运行：

**Twilio**

`Install-Package Twilio`

**ASPSMS:**

`Install-Package ASPSMS`

* 添加*服务/MessageServices*文件中的代码以启用 SMS。 使用 Twilio 或 ASPSMS 部分：

**Twilio**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

**ASPSMS:**  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a>配置要使用的启动 `SMSoptions`

将 `SMSoptions` 添加到*Startup.cs*中 `ConfigureServices` 方法中的服务容器：

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a>启用双因素身份验证

打开*Views/管理/索引。 cshtml* Razor 视图文件并删除注释字符（因此不会注释掉标记）。

## <a name="log-in-with-two-factor-authentication"></a>使用双因素身份验证登录

* 运行应用并注册一个新用户

![Web 应用程序注册 Microsoft Edge 中打开视图](2fa/_static/login2fa1.png)

* 点击你的用户名，该名称将激活管理控制器中的 `Index` 操作方法。 然后点击 "电话号码" "**添加**" 链接。

![管理视图-点击"添加"链接](2fa/_static/login2fa2.png)

* 添加将接收验证码的电话号码，然后点击 "**发送验证码**"。

![添加电话号码页](2fa/_static/login2fa3.png)

* 则会使用验证码的短信。 输入它，然后点击 "**提交**"

![验证电话号码页](2fa/_static/login2fa4.png)

如果没有收到短信，请参阅 twilio 日志页。

* 管理视图显示已成功添加你的电话号码。

![管理视图-已成功添加电话号码](2fa/_static/login2fa5.png)

* 点击 "**启用**" 以启用双因素身份验证。

![管理视图： 启用双因素身份验证](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a>测试双因素身份验证

* 注销。

* 登录。

* 用户帐户已启用双因素身份验证，因此你必须提供身份验证的第二个因素。 在本教程中已启用电话验证。 内置的模板还可以设置电子邮件作为第二个因素。 您可以设置其他身份验证的 QR 代码如第二个因素。 点击 "**提交**"。

![发送验证代码视图](2fa/_static/login2fa7.png)

* 输入你将获得的 SMS 消息中的代码。

* 单击 "**记住此浏览器**" 复选框会使你不需要使用2FA 在使用同一设备和浏览器时进行登录。 如果启用2FA 并单击 "记住"，**此浏览器**将为你提供强大的2FA 防护，防止恶意用户尝试访问你的帐户，只要他们无权访问你的设备。 可以在您经常使用任何专用设备上执行此操作。 通过设置**记住此浏览器**，你可以从不经常使用的设备中获得2FA 的增强的安全性，并且你无需在自己的设备上通过2FA。

![验证视图](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a>用于防范暴力破解攻击的帐户锁定

帐户锁定，建议使用 2FA。 用户登录后通过本地帐户或社交帐户，存储在 2FA 每次失败的尝试。 如果达到最大的失败的访问尝试，则用户锁定 (默认值： 5 分钟锁定 5 失败访问尝试后)。 成功的身份验证失败的访问尝试计数重置并重置时钟。 可通过[MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts)和[DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)设置最大失败访问尝试和锁定时间。 以下 10 分钟后访问尝试失败 10 次配置帐户锁定：

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

确认[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync)将 `lockoutOnFailure` 设置为 `true`：

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
