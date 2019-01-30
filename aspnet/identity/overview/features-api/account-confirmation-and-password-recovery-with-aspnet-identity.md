---
uid: identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity
title: 帐户确认和密码恢复与 ASP.NET 标识 (C#) |Microsoft Docs
author: HaoK
description: 执行操作，应该先完成本教程之前具有登录、 电子邮件确认及密码重置创建安全的 ASP.NET MVC 5 web 应用程序。 本教程...
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 8d54180d-f826-4df7-b503-7debf5ed9fb3
msc.legacyurl: /identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity
msc.type: authoredcontent
ms.openlocfilehash: 47dc2c1044a5964624ba2f8af4f174a2fd99d3e8
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236401"
---
# <a name="account-confirmation-and-password-recovery-with-aspnet-identity-c"></a>帐户确认和密码恢复与 ASP.NET 标识 (C#)

> 在学习本教程之前，应该先完成[安全的 ASP.NET MVC 5 web 应用程序创建具有登录、 电子邮件确认及密码重置](../../../mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md)。 本教程包含更多详细信息，并演示如何设置本地帐户确认的电子邮件，并允许用户在 ASP.NET 标识中的忘记了的密码重置。

本地用户帐户要求用户创建的帐户的密码，而该密码 （安全） 存储在 web 应用中。 ASP.NET 标识还支持社交帐户，不需要用户创建应用密码。 [社交帐户](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)使用第三方 （如 Google、 Twitter、 Facebook 或 Microsoft） 对用户进行身份验证。 本主题涵盖以下产品：

- [创建 ASP.NET MVC 应用](#createMvc)和浏览 ASP.NET 标识功能。
- [生成标识示例](#build)
- [设置电子邮件确认](#email)

新用户注册其电子邮件别名，这将创建本地帐户。

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image1.png)

选择注册按钮将发送确认电子邮件包含到其电子邮件地址的验证令牌。

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image2.png)

在向用户发送包含其帐户的确认令牌的电子邮件。

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image3.png)

选择此链接确认该帐户。

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image4.png)

<a id="passwordReset"></a>

## <a name="password-recoveryreset"></a>密码恢复/重置

本地用户都忘记了密码可以具有的安全令牌发送到用户的电子邮件帐户，使他们能够重置其密码。  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image5.png)  
  
从而允许它们重置其密码的链接，用户将很快收到一封电子邮件。  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image6.png)  
选择该链接会将他们重置页。  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image7.png)  
  
选择**重置**按钮将确认密码已重置。  
  
![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image8.png)

<a id="createMvc"></a>

## <a name="create-an-aspnet-web-app"></a>创建 ASP.NET Web 应用

首先，安装并运行[Visual Studio 2017](https://visualstudio.microsoft.com/)。


1. 创建新的 ASP.NET Web 项目并选择 MVC 模板。 Web 窗体还支持 ASP.NET 标识，因此无法执行类似的步骤，在 web 窗体应用中。
2. 更改到身份验证**单个用户帐户**。
3. 运行该应用程序中，选择**注册**链接并注册用户。 在此情况下，电子邮件的唯一验证是使用[[EmailAddress]](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx)属性。
4. 在服务器资源管理器，导航到**数据 Connections\DefaultConnection\Tables\AspNetUsers**，右键单击并选择**打开表定义**。

    下图显示了`AspNetUsers`架构：

    ![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image9.png)
5. 右键单击**AspNetUsers**表，然后选择**显示表数据**。  
  
    ![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image10.png)  
  
   此时该电子邮件尚未被确认。

ASP.NET 标识的默认数据存储是实体框架中，但你可以配置它以使用其他数据存储并添加其他字段。 请参阅[其他资源](#addRes)本教程结尾部分。

[OWIN 启动类](../../../aspnet/overview/owin-and-katana/owin-startup-class-detection.md)( *Startup.cs* ) 时应用启动并调用调用`ConfigureAuth`中的方法*应用\_Start\Startup.Auth.cs*，这将配置 OWIN 管道并初始化 ASP.NET 标识。 检查 `ConfigureAuth` 方法。 每个`CreatePerOwinContext`调用注册的回调 (保存在`OwinContext`)，将调用一次每个请求创建指定类型的实例。 可以在构造函数中设置断点并`Create`每种类型的方法 (`ApplicationDbContext, ApplicationUserManager`)，并验证每个请求调用它们。 实例`ApplicationDbContext`和`ApplicationUserManager`存储在可以访问整个应用程序的 OWIN 上下文。 ASP.NET 标识挂接到通过 cookie 中间件在 OWIN 管道。 有关详细信息，请参阅[每个请求生存期管理 UserManager 类在 ASP.NET 标识中](https://blogs.msdn.com/b/webdev/archive/2014/02/12/per-request-lifetime-management-for-usermanager-class-in-asp-net-identity.aspx)。

更改安全配置文件时，生成并存储在一个新的安全戳`SecurityStamp`字段*AspNetUsers*表。 请注意，`SecurityStamp`字段是不同的安全 cookie。 安全 cookie 不会存储在`AspNetUsers`表 （或标识 DB 中的其他任何位置）。 使用自签名安全 cookie 令牌[DPAPI](https://msdn.microsoft.com/library/system.security.cryptography.protecteddata.aspx)并使用创建`UserId, SecurityStamp`和过期时间信息。

Cookie 中间件将检查每个请求的 cookie。 `SecurityStampValidator`中的方法`Startup`类达到数据库，并定期检查安全戳指定的`validateInterval`。 除非您更改安全配置文件，这仅发生 （在本示例中） 每隔 30 分钟。 选择 30 分钟时间间隔内的目的是尽量减少对数据库的访问。 请参阅我[双因素身份验证教程](index.md)的更多详细信息。

在代码中，注释每`UseCookieAuthentication`方法支持 cookie 身份验证。 `SecurityStamp`字段和关联代码提供额外一层向应用程序的安全性、 时更改密码，你将记录在您登录所用的浏览器之外。 `SecurityStampValidator.OnValidateIdentity`方法使应用能够验证安全令牌，当用户登录时更改密码或使用外部登录时使用。 这被需要确保任何使用旧密码生成的令牌 (cookie) 将会失效。 在示例项目中，如果更改为用户生成用户密码然后新的令牌，任何以前的令牌都无效和`SecurityStamp`字段会进行更新。

标识系统，可以配置您的应用程序，用户的安全配置文件发生更改时 (例如，当用户更改其密码或更改关联的登录名 (如 Facebook、 Google、 Microsoft 帐户，等等)，从所有注销用户浏览器实例。 例如下, 图显示[单点注销示例](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/SingleSignOutSample/readme.txt)应用，这样用户就可以通过选择一个按钮中注销所有浏览器实例 （在此情况下，IE、 Firefox 和 Chrome）。 或者，该示例，可仅注销特定浏览器实例。

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image11.png)

[单点注销示例](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/SingleSignOutSample/readme.txt)应用将显示 ASP.NET 标识如何帮助您重新生成安全令牌。 这被需要确保任何使用旧密码生成的令牌 (cookie) 将会失效。 此功能提供额外一层的应用程序的安全性更改你的密码时，你将被注销已登录此应用程序。

*应用程序\_Start\IdentityConfig.cs*文件包含`ApplicationUserManager`，`EmailService`和`SmsService`类。 `EmailService`并`SmsService`类均可实现`IIdentityMessageService`接口，因此必须在每个类以配置电子邮件和短信中常见的方法。 虽然本教程仅演示了如何添加通过电子邮件通知[SendGrid](http://sendgrid.com/)，可以发送电子邮件使用 SMTP 和其他机制。

`Startup`类还包含样板添加社交登录 （Facebook、 Twitter 等），请参阅我的教程[MVC 5 应用程序使用 Facebook、 Twitter、 LinkedIn 和 Google OAuth2 登录](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)的详细信息。

检查`ApplicationUserManager`类，该类包含用户标识信息并配置以下功能：

- 密码强度要求。
- 用户锁定 （尝试次数和时间）。
- 双因素身份验证 (2FA)。 在另一个教程中，我将介绍 2FA 和短信。
- 挂接的电子邮件和短信服务。 （我将介绍 SMS 另一个教程中）。

`ApplicationUserManager`类派生自泛型`UserManager<ApplicationUser>`类。 `ApplicationUser` 派生自[IdentityUser](https://msdn.microsoft.com/library/microsoft.aspnet.identity.entityframework.identityuser.aspx)。 `IdentityUser` 派生自泛型`IdentityUser`类：

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample1.cs)]

上述属性中的属性与同时发生`AspNetUsers`表，如上所示。

泛型参数上的`IUser`帮助您获得对主键使用不同类型的类。 请参阅[ChangePK](https://aspnet.codeplex.com/SourceControl/latest#Samples/Identity/ChangePK/readme.txt)示例，其中说明了如何将主键从字符串更改为 int 或 GUID。

### <a name="applicationuser"></a>ApplicationUser

`ApplicationUser` (`public class ApplicationUserManager : UserManager<ApplicationUser>`) 中定义*Models\IdentityModels.cs*作为：

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample2.cs?highlight=8-9)]

上面的突出显示的代码生成[ClaimsIdentity](https://msdn.microsoft.com/library/system.security.claims.claimsidentity.aspx)。 ASP.NET 标识和 OWIN Cookie 身份验证是基于声明的因此，框架需要应用程序以生成`ClaimsIdentity`用户。 `ClaimsIdentity` 具有信息有关的用户，如用户的名称的所有声明 age 和用户属于哪些角色。 在此阶段，还可以添加更多的用户声明。

OWIN`AuthenticationManager.SignIn`方法将传递在`ClaimsIdentity`并在用户对进行签名：

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample3.cs?highlight=4-6)]

[使用 Facebook、 Twitter、 LinkedIn 和 Google OAuth2 登录的 MVC 5 应用程序](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)显示了如何将添加到的其他属性`ApplicationUser`类。

## <a name="email-confirmation"></a>电子邮件确认

它是一个好办法确认新用户注册以验证它们不模拟其他人的电子邮件 （即，他们尚未注册与其他人的电子邮件）。 假设你有讨论论坛，你会想要防止`"bob@example.com"`注册为`"joe@contoso.com"`。 而无需电子邮件确认`"joe@contoso.com"`无法收到您的应用程序不需要的电子邮件。 假设 Bob 意外注册为`"bib@example.com"`没有注意到，他将无法使用密码恢复，因为此应用没有其正确的电子邮件。 电子邮件确认从智能机器人提供有限的保护，并不会从确定垃圾邮件发送者提供保护，它们具有许多可用于注册的工作电子邮件别名。在下面的示例中，用户将无法更改其密码，直到其帐户已确认 （通过其选择与它们注册的电子邮件帐户上接收的确认链接。）到其他方案中，可以应用此工作流为例发送用于确认和新建的管理员，它们已更改其配置文件等时向用户发送一封电子邮件帐户的密码重置的链接。 你通常想要阻止新用户之前他们确认已通过电子邮件、 短信或另一种机制将发布到网站的任何数据。 <a id="build"></a>

## <a name="build-a-more-complete-sample"></a>生成更完整示例

在本部分中，您将使用 NuGet 下载我们将使用的更完整示例。

1. 创建一个新***空***ASP.NET Web 项目。
2. 在包管理器控制台中，输入以下命令： 

    [!code-console[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample4.cmd)]

   在本教程中，我们将使用[SendGrid](http://sendgrid.com/)发送电子邮件。 `Identity.Samples`包将安装我们将使用的代码。
3. 设置[项目，以使用 SSL](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)。
4. 通过运行应用程序中，测试创建本地帐户选择**注册**链接，并将注册表单。
5. 选择演示电子邮件链接，以模拟电子邮件确认。
6. 删除示例中演示电子邮件链接确认代码 (`ViewBag.Link`帐户控制器中的代码。 请参阅`DisplayEmail`和`ForgotPasswordConfirmation`操作方法和 razor 视图)。

> [!WARNING]
> 如果在此示例中的安全设置的任何更改，生产应用程序将需要进行显式调用所做的更改的安全审核。


## <a name="examine-the-code-in-appstartidentityconfigcs"></a>检查应用中的代码\_Start\IdentityConfig.cs

此示例演示如何创建帐户并将其添加到*管理员*角色。 你将使用管理员帐户的电子邮件中应替换示例中的电子邮件。 现在创建管理员帐户的最简单方法是以编程方式在`Seed`方法。 我们希望可以在将来将允许你创建和管理用户和角色的工具。 示例代码允许您创建和管理用户和角色，但你必须首先运行的角色和用户管理页面的管理员帐户。 在此示例中，将植入数据库时创建的管理员帐户。

更改密码，然后将名称更改为可以接收电子邮件通知的位置的帐户。

> [!WARNING]
> 安全性-永远不会存储在源代码中敏感数据。

如前文所述， `app.CreatePerOwinContext` startup 类中的调用添加到的回调`Create`应用 DB 内容，用户管理器和角色管理器类的方法。 OWIN 管道调用`Create`方法这些类为每个请求，并存储每个类的上下文。 帐户控制器公开的 HTTP 上下文 （其中包含 OWIN 上下文） 中的用户管理器：

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample5.cs)]

当用户注册一个本地帐户，`HTTP Post Register`调用方法：

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample6.cs)]

上面的代码使用模型数据创建新的用户帐户使用的电子邮件和输入的密码。 如果电子邮件别名是在数据存储区中，帐户创建失败，并再次显示窗体。 `GenerateEmailConfirmationTokenAsync`方法创建的安全确认令牌并将其存储在 ASP.NET 标识数据存储区。 [Url.Action](https://msdn.microsoft.com/library/dd505232(v=vs.118).aspx)方法创建一个链接，其中包含`UserId`和确认令牌。 然后向用户电子邮件发送此链接，用户可以选择其电子邮件应用中的链接以确认其帐户。

<a id="email"></a>

## <a name="set-up-email-confirmation"></a>设置电子邮件确认

转到[Azure SendGrid 注册页面](https://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/)并注册免费帐户。 添加类似于以下操作来配置 SendGrid 的代码：

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample7.cs?highlight=5)]

> [!NOTE]
> 电子邮件客户端通常只接受文本消息 (HTML)。 应提供文本和 HTML 中的消息。 在上述 SendGrid 示例中，这通过`myMessage.Text`和`myMessage.Html`如上所示的代码。


下面的代码演示如何使用电子邮件发送[MailMessage](https://msdn.microsoft.com/library/system.net.mail.mailmessage.aspx)类`message.Body`返回的链接。

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample8.cs)]

> [!WARNING]
> 安全性-永远不会存储在源代码中敏感数据。 AppSetting 中存储的帐户和凭据。 在 Azure 上，您可以安全地存储这些值在 **[配置](https://blogs.msdn.com/b/webdev/archive/2014/06/04/queuebackgroundworkitem-to-reliably-schedule-and-run-long-background-process-in-asp-net.aspx)** 在 Azure 门户中的选项卡。 请参阅[的密码和其他敏感数据部署到 ASP.NET 和 Azure 最佳做法](best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure.md)。


输入你的 SendGrid 凭据，运行该应用，注册到的电子邮件别名可以选择确认的链接在你的电子邮件中。 若要了解如何执行此操作与你[Outlook.com](http://outlook.com)电子邮件帐户，请参阅 John 输入[ C# Outlook.Com SMTP 主机的 SMTP 配置](http://typecastexception.com/post/2013/12/20/C-SMTP-Configuration-for-OutlookCom-SMTP-Host.aspx)，并且其[ASP.NET 标识 2.0:设置帐户验证和授权的双重](http://typecastexception.com/post/2014/04/20/ASPNET-Identity-20-Setting-Up-Account-Validation-and-Two-Factor-Authorization.aspx)文章。

一旦用户选择**注册**按钮确认电子邮件包含验证令牌发送到其电子邮件地址。

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image12.png)

在向用户发送包含其帐户的确认令牌的电子邮件。

![](account-confirmation-and-password-recovery-with-aspnet-identity/_static/image13.png)

## <a name="examine-the-code"></a>检查代码

以下代码演示了 `POST ForgotPassword` 方法。

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample9.cs)]

如果尚未被确认用户电子邮件，该方法将以无提示方式失败。 如果错误无效的电子邮件地址发送的恶意用户无法使用该信息以查找有效用户 Id （电子邮件别名） 攻击。

下面的代码演示`ConfirmEmail`时用户选择的确认链接向他们发送电子邮件中，将调用在 account 控制器中的方法：

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample10.cs)]

一旦忘记了的密码令牌已使用，它会失效。 在以下代码更改`Create`方法 (在*应用程序\_Start\IdentityConfig.cs*文件) 设置为在 3 小时内过期的令牌。

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample11.cs?highlight=6-8)]

上述代码中，忘记了的密码和电子邮件确认令牌将于 3 小时。 默认值`TokenLifespan`为一天。

下面的代码显示了电子邮件确认方法：

[!code-csharp[Main](account-confirmation-and-password-recovery-with-aspnet-identity/samples/sample12.cs)]

 若要使应用能够更安全，ASP.NET 标识支持双重身份验证 (2FA)。 请参阅[ASP.NET 标识 2.0:设置帐户验证和授权的双重](http://typecastexception.com/post/2014/04/20/ASPNET-Identity-20-Setting-Up-Account-Validation-and-Two-Factor-Authorization.aspx)通过 John 输入。 尽管您可以设置帐户锁定登录名的密码尝试失败，这种方法使您的登录名到易受[DOS](http://en.wikipedia.org/wiki/Denial-of-service_attack)锁定。 我们建议仅使用 2FA 使用帐户锁定。  
<a id="addRes"></a>

## <a name="additional-resources"></a>其他资源

- [ASP.NET 标识的自定义存储提供程序概述](../extensibility/overview-of-custom-storage-providers-for-aspnet-identity.md)
- [MVC 5 应用程序使用 Facebook、 Twitter、 LinkedIn 和 Google OAuth2 登录](../../../mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on.md)还演示如何将配置文件信息添加到用户表。
- [ASP.NET MVC 和标识 2.0:了解基础知识](http://typecastexception.com/post/2014/04/20/ASPNET-MVC-and-Identity-20-Understanding-the-Basics.aspx)通过 John 输入。
- [ASP.NET 标识简介](../getting-started/introduction-to-aspnet-identity.md)
- [宣布推出 RTM 的 ASP.NET 标识 2.0.0](https://blogs.msdn.com/b/webdev/archive/2014/03/20/test-announcing-rtm-of-asp-net-identity-2-0-0.aspx) Pranav rastogi 撰写。
