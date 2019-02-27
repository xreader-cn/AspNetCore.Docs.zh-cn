---
uid: web-forms/overview/security/create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset
title: 创建安全的 ASP.NET Web 窗体应用程序具有用户注册、 电子邮件确认及密码重置 (C#) |Microsoft Docs
author: Erikre
description: 本教程演示如何构建包含用户注册、 电子邮件确认和密码重置使用 ASP.NET 标识成员的 ASP.NET Web 窗体应用...
ms.author: riande
ms.date: 10/02/2014
ms.assetid: 0a8d6044-5fab-4213-82d6-5618d5601358
msc.legacyurl: /web-forms/overview/security/create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset
msc.type: authoredcontent
ms.openlocfilehash: acc13776840408756901e20589b9efacc83ff2a9
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833678"
---
<a name="create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset-c"></a>创建具有用户注册、电子邮件确认和密码重置功能的安全 ASP.NET Web 窗体应用 (C#)
====================
通过[Erik Reitan](https://github.com/Erikre)

> 本教程演示如何构建包含用户注册、 电子邮件确认和密码重置使用 ASP.NET 标识成员资格系统的 ASP.NET Web 窗体应用。 本教程基于 Rick Anderson [MVC 教程](../../../mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md)。


## <a name="introduction"></a>介绍

本教程将指导您完成创建使用 Visual Studio 和 ASP.NET 4.5 来创建安全的 Web 窗体应用程序具有用户注册、 电子邮件确认及密码重置的 ASP.NET Web 窗体应用程序所需的步骤。

### <a name="tutorial-tasks-and-information"></a>教程任务和信息：

- [创建 ASP.NET Web 窗体应用](#createWebForms)
- [将挂接到 SendGrid](#SG)
- [需要电子邮件确认之前登录](#require)
- [密码恢复和重置](#reset)
- [重新发送电子邮件确认链接](#rsend)
- [故障排除应用程序](#dbg)
- [其他资源](#addRes)

<a id="createWebForms"></a>
## <a name="create-an-aspnet-web-forms-app"></a>创建 ASP.NET Web 窗体应用

首先，安装并运行[Visual Studio Express 2013 for Web](https://go.microsoft.com/fwlink/?LinkId=299058)或[Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566)。 安装[Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465)或更高版本以及。

> [!NOTE]
> 警告：必须安装[Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465)或更高版本，若要完成本教程。


1. 创建新的项目 (**文件** - &gt; **新项目**)，然后选择**ASP.NET Web 应用程序**模板和最新的.NET Framework从版本**新的项目**对话框。
2. 从**新建 ASP.NET 项目**对话框中，选择**Web 窗体**模板。 将保留为默认的身份验证**单个用户帐户**。 如果你想要托管该应用程序在 Azure 中的，将保留**在云中托管**选中的复选框。   
 然后，单击**确定**创建新项目。  
    ![新建 ASP.NET 项目对话框](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image1.png)
3. 为项目启用安全套接字层 (SSL)。 请按照步骤中提供**为项目启用 SSL**一部分[开始使用 Web 窗体的系列教程](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#SSLWebForms)。
4. 运行该应用程序中，单击**注册**链接并注册一个新用户。 此时，基于电子邮件的唯一验证[[EmailAddress]](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx)属性，以确保电子邮件地址的格式正确。 您将修改代码以添加电子邮件确认。 关闭浏览器窗口。
5. 在中**服务器资源管理器**的 Visual Studio (**视图** - &gt; **服务器资源管理器**)，导航到**数据 Connections\DefaultConnection\Tables\AspNetUsers**，右键单击并选择**打开表定义**。 

    下图显示了`AspNetUsers`表架构：

    ![AspNetUsers 表架构](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image2.png)
6. 在中**服务器资源管理器**，右键单击**AspNetUsers**表，然后选择**显示表数据**。  
  
    ![AspNetUsers 表数据](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image3.png)  
 此时将注册的用户的电子邮件尚未被确认。
7. 单击行，然后选择删除到删除的用户。 将在下一步中重新添加此电子邮件，并将一条确认消息发送到电子邮件地址。

## <a name="email-confirmation"></a>电子邮件确认

它是一个新的用户，以验证它们不模拟其他用户在注册期间确认电子邮件的最佳做法 （也就是说，他们尚未注册与其他人的电子邮件）。 假设你有讨论论坛，你会想要防止`"bob@cpandl.com"`注册为`"joe@contoso.com"`。 而无需电子邮件确认`"joe@contoso.com"`无法收到您的应用程序不需要的电子邮件。 假设 Bob 意外注册为`"bib@cpandl.com"`没有注意到，他将无法使用密码恢复，因为此应用没有其正确的电子邮件。 电子邮件确认从智能机器人提供有限的保护，并且不从确定垃圾邮件发送者提供保护。

你通常想要发布到你的网站的任何数据，它们已由 SMS 文本消息或另一种机制是电子邮件确认之前阻止新用户。 在以下部分中，我们将启用电子邮件确认，并修改代码以防止新注册的用户登录之前确认其电子邮件。 在本教程中，将使用电子邮件服务 SendGrid。

<a id="SG"></a>
## <a name="hook-up-sendgrid"></a>将挂接到 SendGrid

本教程后，SendGrid 已更改它的 API。 有关当前 SendGrid 的说明，请参阅[SendGrid](http://sendgrid.com/)或[启用帐户确认和密码恢复](xref:security/authentication/accconfirm#enable-account-confirmation-and-password-recovery)。

虽然本教程仅演示了如何添加通过电子邮件通知[SendGrid](http://sendgrid.com/)，可以发送电子邮件使用 SMTP 和其他机制 (请参阅[的其他资源](#addRes))。

1. 在 Visual Studio 中打开**程序包管理器控制台**(**工具** - &gt; **NuGet 程序包管理器** - &gt;**程序包管理器控制台**)，并输入以下命令：  
    `Install-Package SendGrid`
2. 转到[注册页面，Azure SendGrid](https://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/)并注册免费的 SendGrid 帐户。 可以是直接在免费的 SendGrid 帐户还注册[SendGrid 的站点](http://www.sendgrid.com)。
3. 从**解决方案资源管理器**打开*IdentityConfig.cs*文件中*应用\_启动*文件夹并添加以下代码以的黄色突出显示`EmailService`类，以配置**SendGrid**:

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample1.cs?highlight=3,5,8-37)]
4. 此外，添加以下`using`语句的开头*IdentityConfig.cs*文件： 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample2.cs?highlight=1-4)]
5. 若要使此示例简单明了，将存储中的电子邮件服务帐户值`appSettings`一部分*web.config*文件。 添加以下 XML 中为黄色突出显示*web.config*你的项目的根目录下的文件：

    [!code-xml[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample3.xml?highlight=2-5)]

    > [!WARNING]
    > 安全性-永远不会存储在源代码中敏感数据。 在此示例中，帐户和凭据存储在**appSetting**一部分*Web.config*文件。 在 Azure 上，您可以安全地存储这些值在 **[配置](https://blogs.msdn.com/b/webdev/archive/2014/06/04/queuebackgroundworkitem-to-reliably-schedule-and-run-long-background-process-in-asp-net.aspx)** 在 Azure 门户中的选项卡。 有关相关信息请参阅标题为 Rick Anderson 主题[的密码和其他敏感数据部署到 ASP.NET 和 Azure 最佳做法](https://go.microsoft.com/fwlink/?LinkId=513141)。
6. 添加电子邮件服务值以反映你的 SendGrid 身份验证值 （用户名和密码），以便可以成功从应用发送电子邮件。 请务必使用你的 SendGrid 帐户名称而不是你提供 SendGrid 的电子邮件地址。

### <a name="enable-email-confirmation"></a>启用电子邮件确认

 若要启用电子邮件确认，您将修改的注册代码，使用以下步骤。  
 

1. 在中*帐户*文件夹中，打开*Register.aspx.cs*的代码隐藏和更新`CreateUser_Click`方法，以使以下突出显示的更改： 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample4.cs?highlight=9-11)]
2. 在中**解决方案资源管理器**，右键单击*Default.aspx* ，然后选择**设为起始页**。
3. 运行应用，请按**F5。** 将显示的页后，单击**注册**链接以显示注册页。
4. 输入你的电子邮件和密码，然后单击**注册**按钮发送通过 SendGrid 电子邮件。  
   你的项目和代码的当前状态将允许用户登录后其完成注册表单中，即使他们尚未确认其帐户也是如此。
5. 检查你的电子邮件帐户，并单击链接以确认你的电子邮件。  
   一旦提交注册表单，您将登录。  
    ![示例网站的登录](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image4.png)

<a id="require"></a>
## <a name="require-email-confirmation-before-log-in"></a>需要电子邮件确认之前登录

尽管已确认的电子邮件帐户，此时不需要完全签名中的验证电子邮件中包含的链接上单击。 在以下部分中，将修改要求新用户在登录之前能够确认电子邮件 （通过身份验证） 的代码。

1. 在中**解决方案资源管理器**的 Visual Studio 中，更新`CreateUser_Click`中的事件*Register.aspx.cs*隐藏代码中包含*帐户*文件夹以下突出显示的更改： 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample5.cs?highlight=13-14,17-21)]
2. 更新`LogIn`中的方法*Login.aspx.cs*代码隐藏有以下突出显示更改： 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample6.cs?highlight=9-19,45-46)]

### <a name="run-the-application"></a>运行应用程序

 现在，已实现的代码来检查是否已确认用户的电子邮件地址，你可以检查的功能上都**注册**并**登录名**页。 

1. 删除中的任何帐户**AspNetUsers**包含你要测试的电子邮件别名的表。
2. 运行该应用程序 (**F5**)，并验证直到您已确认你的电子邮件地址不能注册为用户。
3. 在确认新的帐户通过刚发送的电子邮件前, 尝试新的帐户登录。  
 将看到你不能以用户身份登录，您必须具有一个已确认电子邮件帐户。
4. 一旦确认电子邮件地址，请登录到应用程序。

<a id="reset"></a>
## <a name="password-recovery-and-reset"></a>密码恢复和重置

1. 在 Visual Studio 中，删除中的注释字符`Forgot`中的方法*Forgot.aspx.cs*隐藏代码中包含*帐户*文件夹中，因此，该方法如下所示： 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample7.cs?highlight=16-18)]
2. 打开*Login.aspx*页。 替换标记末尾附近**loginForm**部分下面突出显示的那样： 

    [!code-aspx[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample8.aspx?highlight=52-53)]
3. 打开*Login.aspx.cs*的代码隐藏和取消注释以下行以从黄色突出显示的代码`Page_Load`事件处理程序： 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample9.cs?highlight=5)]
4. 运行应用，请按**F5。** 将显示的页后，单击**登录**链接。
5. 单击**忘记了密码？** 链接以显示**忘记了密码**页。
6. 输入你的电子邮件地址，然后单击**提交**按钮将发送一封电子邮件给你这将允许您重置密码的地址。   
   检查您的电子邮件帐户，然后单击链接以显示**重置密码**页。
7. 上**重置密码**页上，输入电子邮件、 密码和确认的密码。 然后，按**重置**按钮。  
   已成功重置密码，当**更改密码**，将显示页。 现在可以登录您的新密码。

<a id="rsend"></a>
## <a name="resend-email-confirmation-link"></a>重新发送电子邮件确认链接

一旦用户创建新的本地帐户时，它们是电子邮件通知他们需要使用在登录之前确认链接。 如果用户意外删除的确认电子邮件，或者永远不会收到电子邮件，他们将需要再次发送的确认链接。 以下代码更改演示如何启用此功能。

1. 在 Visual Studio 中打开**Login.aspx.cs**隐藏代码，并添加以下事件处理程序后的`LogIn`事件处理程序：   

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample10.cs)]
2. 修改`LogIn`中的事件处理程序*Login.aspx.cs*代码隐藏，如下所示以黄色突出显示的代码： 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample11.cs?highlight=15-17)]
3. 更新*Login.aspx*通过添加，如下所示以黄色突出显示的代码页： 

    [!code-aspx[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample12.aspx?highlight=45-46)]
4. 删除中的任何帐户**AspNetUsers**包含你要测试的电子邮件别名的表。
5. 运行该应用程序 (**F5**)，并注册你的电子邮件地址。
6. 在确认新的帐户通过刚发送的电子邮件前, 尝试新的帐户登录。  
   将看到你不能以用户身份登录，您必须具有一个已确认电子邮件帐户。 此外，现在可以重新一条确认消息发送到你的电子邮件帐户。
7. 输入你的电子邮件地址和密码，然后按**重新发送确认**按钮。
8. 一旦确认你基于新发送的电子邮件的电子邮件地址，请登录到应用程序。

<a id="dbg"></a>
## <a name="troubleshooting-the-app"></a>故障排除应用程序

如果未收到一封包含链接以验证您的凭据：

- 请检查垃圾邮件或垃圾邮件文件夹。
- 登录到你的 SendGrid 帐户，然后单击[电子邮件活动链接](https://sendgrid.com/logs/index)。
- 请确保使用为您的 SendGrid 用户帐户名称*Web.config*值而不是你的 SendGrid 帐户电子邮件地址。

<a id="addRes"></a>
## <a name="additional-resources"></a>其他资源

- [ASP.NET 标识的链接的推荐资源](../../../identity/overview/getting-started/aspnet-identity-recommended-resources.md)
- [帐户确认和密码恢复与 ASP.NET 标识](../../../identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity.md)
- [ASP.NET Web 窗体的教程系列-添加 OAuth 2.0 提供程序](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#OAuthWebForms)
- [包含成员资格、 OAuth 和 SQL 数据库的安全的 ASP.NET Web 窗体应用部署到 Azure 应用服务](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/)
- [ASP.NET Web 窗体教程系列的项目启用 SSL](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#SSLWebForms)
