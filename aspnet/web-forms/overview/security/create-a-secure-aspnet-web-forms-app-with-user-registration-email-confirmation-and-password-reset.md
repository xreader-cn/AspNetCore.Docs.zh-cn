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
<a name="create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset-c"></a><span data-ttu-id="042d1-103">创建具有用户注册、电子邮件确认和密码重置功能的安全 ASP.NET Web 窗体应用 (C#)</span><span class="sxs-lookup"><span data-stu-id="042d1-103">Create a secure ASP.NET Web Forms app with user registration, email confirmation and password reset (C#)</span></span>
====================
<span data-ttu-id="042d1-104">通过[Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="042d1-104">by [Erik Reitan](https://github.com/Erikre)</span></span>

> <span data-ttu-id="042d1-105">本教程演示如何构建包含用户注册、 电子邮件确认和密码重置使用 ASP.NET 标识成员资格系统的 ASP.NET Web 窗体应用。</span><span class="sxs-lookup"><span data-stu-id="042d1-105">This tutorial shows you how to build an ASP.NET Web Forms app with user registration, email confirmation and password reset using the ASP.NET Identity membership system.</span></span> <span data-ttu-id="042d1-106">本教程基于 Rick Anderson [MVC 教程](../../../mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md)。</span><span class="sxs-lookup"><span data-stu-id="042d1-106">This tutorial was based on Rick Anderson's [MVC tutorial](../../../mvc/overview/security/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset.md).</span></span>


## <a name="introduction"></a><span data-ttu-id="042d1-107">介绍</span><span class="sxs-lookup"><span data-stu-id="042d1-107">Introduction</span></span>

<span data-ttu-id="042d1-108">本教程将指导您完成创建使用 Visual Studio 和 ASP.NET 4.5 来创建安全的 Web 窗体应用程序具有用户注册、 电子邮件确认及密码重置的 ASP.NET Web 窗体应用程序所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="042d1-108">This tutorial guides you through the steps required to create an ASP.NET Web Forms application using Visual Studio and ASP.NET 4.5 to create a secure Web Forms app with user registration, email confirmation and password reset.</span></span>

### <a name="tutorial-tasks-and-information"></a><span data-ttu-id="042d1-109">教程任务和信息：</span><span class="sxs-lookup"><span data-stu-id="042d1-109">Tutorial Tasks and Information:</span></span>

- [<span data-ttu-id="042d1-110">创建 ASP.NET Web 窗体应用</span><span class="sxs-lookup"><span data-stu-id="042d1-110">Create an ASP.NET Web Forms app</span></span>](#createWebForms)
- [<span data-ttu-id="042d1-111">将挂接到 SendGrid</span><span class="sxs-lookup"><span data-stu-id="042d1-111">Hook Up SendGrid</span></span>](#SG)
- [<span data-ttu-id="042d1-112">需要电子邮件确认之前登录</span><span class="sxs-lookup"><span data-stu-id="042d1-112">Require Email Confirmation Before Log In</span></span>](#require)
- [<span data-ttu-id="042d1-113">密码恢复和重置</span><span class="sxs-lookup"><span data-stu-id="042d1-113">Password Recovery and Reset</span></span>](#reset)
- [<span data-ttu-id="042d1-114">重新发送电子邮件确认链接</span><span class="sxs-lookup"><span data-stu-id="042d1-114">Resend Email Confirmation Link</span></span>](#rsend)
- [<span data-ttu-id="042d1-115">故障排除应用程序</span><span class="sxs-lookup"><span data-stu-id="042d1-115">Troubleshooting the App</span></span>](#dbg)
- [<span data-ttu-id="042d1-116">其他资源</span><span class="sxs-lookup"><span data-stu-id="042d1-116">Additional Resources</span></span>](#addRes)

<a id="createWebForms"></a>
## <a name="create-an-aspnet-web-forms-app"></a><span data-ttu-id="042d1-117">创建 ASP.NET Web 窗体应用</span><span class="sxs-lookup"><span data-stu-id="042d1-117">Create an ASP.NET Web Forms App</span></span>

<span data-ttu-id="042d1-118">首先，安装并运行[Visual Studio Express 2013 for Web](https://go.microsoft.com/fwlink/?LinkId=299058)或[Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566)。</span><span class="sxs-lookup"><span data-stu-id="042d1-118">Start by installing and running [Visual Studio Express 2013 for Web](https://go.microsoft.com/fwlink/?LinkId=299058) or [Visual Studio 2013](https://go.microsoft.com/fwlink/?LinkId=306566).</span></span> <span data-ttu-id="042d1-119">安装[Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465)或更高版本以及。</span><span class="sxs-lookup"><span data-stu-id="042d1-119">Install [Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465) or higher as well.</span></span>

> [!NOTE]
> <span data-ttu-id="042d1-120">警告：必须安装[Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465)或更高版本，若要完成本教程。</span><span class="sxs-lookup"><span data-stu-id="042d1-120">Warning: You must install [Visual Studio 2013 Update 3](https://go.microsoft.com/fwlink/?LinkId=390465) or higher to complete this tutorial.</span></span>


1. <span data-ttu-id="042d1-121">创建新的项目 (**文件** - &gt; **新项目**)，然后选择**ASP.NET Web 应用程序**模板和最新的.NET Framework从版本**新的项目**对话框。</span><span class="sxs-lookup"><span data-stu-id="042d1-121">Create a new project (**File** -&gt; **New Project**) and select the **ASP.NET Web Application** template and the latest .NET Framework version from the **New Project** dialog box.</span></span>
2. <span data-ttu-id="042d1-122">从**新建 ASP.NET 项目**对话框中，选择**Web 窗体**模板。</span><span class="sxs-lookup"><span data-stu-id="042d1-122">From the **New ASP.NET Project** dialog box, select the **Web Forms** template.</span></span> <span data-ttu-id="042d1-123">将保留为默认的身份验证**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="042d1-123">Leave the default authentication as **Individual User Accounts**.</span></span> <span data-ttu-id="042d1-124">如果你想要托管该应用程序在 Azure 中的，将保留**在云中托管**选中的复选框。</span><span class="sxs-lookup"><span data-stu-id="042d1-124">If you'd like to host the app in Azure, leave the **Host in the cloud** check box checked.</span></span>   
 <span data-ttu-id="042d1-125">然后，单击**确定**创建新项目。</span><span class="sxs-lookup"><span data-stu-id="042d1-125">Then, click **OK** to create the new project.</span></span>  
    <span data-ttu-id="042d1-126">![新建 ASP.NET 项目对话框](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image1.png)</span><span class="sxs-lookup"><span data-stu-id="042d1-126">![New ASP.NET Project dialog box](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image1.png)</span></span>
3. <span data-ttu-id="042d1-127">为项目启用安全套接字层 (SSL)。</span><span class="sxs-lookup"><span data-stu-id="042d1-127">Enable Secure Sockets Layer (SSL) for the project.</span></span> <span data-ttu-id="042d1-128">请按照步骤中提供**为项目启用 SSL**一部分[开始使用 Web 窗体的系列教程](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#SSLWebForms)。</span><span class="sxs-lookup"><span data-stu-id="042d1-128">Follow the steps available in the **Enable SSL for the Project** section of the [Getting Started with Web Forms tutorial series](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#SSLWebForms).</span></span>
4. <span data-ttu-id="042d1-129">运行该应用程序中，单击**注册**链接并注册一个新用户。</span><span class="sxs-lookup"><span data-stu-id="042d1-129">Run the app, click the **Register** link and register a new user.</span></span> <span data-ttu-id="042d1-130">此时，基于电子邮件的唯一验证[[EmailAddress]](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx)属性，以确保电子邮件地址的格式正确。</span><span class="sxs-lookup"><span data-stu-id="042d1-130">At this point, the only validation on the email is based on the [[EmailAddress]](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.emailaddressattribute(v=vs.110).aspx) attribute to ensure the email address is well-formed.</span></span> <span data-ttu-id="042d1-131">您将修改代码以添加电子邮件确认。</span><span class="sxs-lookup"><span data-stu-id="042d1-131">You will modify the code to add email confirmation.</span></span> <span data-ttu-id="042d1-132">关闭浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="042d1-132">Close the browser window.</span></span>
5. <span data-ttu-id="042d1-133">在中**服务器资源管理器**的 Visual Studio (**视图** - &gt; **服务器资源管理器**)，导航到**数据 Connections\DefaultConnection\Tables\AspNetUsers**，右键单击并选择**打开表定义**。</span><span class="sxs-lookup"><span data-stu-id="042d1-133">In **Server Explorer** of Visual Studio (**View** -&gt; **Server Explorer**), navigate to **Data Connections\DefaultConnection\Tables\AspNetUsers**, right click and select **Open table definition**.</span></span> 

    <span data-ttu-id="042d1-134">下图显示了`AspNetUsers`表架构：</span><span class="sxs-lookup"><span data-stu-id="042d1-134">The following image shows the `AspNetUsers` table schema:</span></span>

    ![AspNetUsers 表架构](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image2.png)
6. <span data-ttu-id="042d1-136">在中**服务器资源管理器**，右键单击**AspNetUsers**表，然后选择**显示表数据**。</span><span class="sxs-lookup"><span data-stu-id="042d1-136">In **Server Explorer**, right-click on the **AspNetUsers** table and select **Show Table Data**.</span></span>  
  
    ![AspNetUsers 表数据](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image3.png)  
 <span data-ttu-id="042d1-138">此时将注册的用户的电子邮件尚未被确认。</span><span class="sxs-lookup"><span data-stu-id="042d1-138">At this point the email for the registered user has not been confirmed.</span></span>
7. <span data-ttu-id="042d1-139">单击行，然后选择删除到删除的用户。</span><span class="sxs-lookup"><span data-stu-id="042d1-139">Click on the row and select delete to delete the user.</span></span> <span data-ttu-id="042d1-140">将在下一步中重新添加此电子邮件，并将一条确认消息发送到电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="042d1-140">You'll add this email again in the next step and send a confirmation message to the email address.</span></span>

## <a name="email-confirmation"></a><span data-ttu-id="042d1-141">电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="042d1-141">Email Confirmation</span></span>

<span data-ttu-id="042d1-142">它是一个新的用户，以验证它们不模拟其他用户在注册期间确认电子邮件的最佳做法 （也就是说，他们尚未注册与其他人的电子邮件）。</span><span class="sxs-lookup"><span data-stu-id="042d1-142">It's a best practice to confirm the email during the registration of a new user to verify they are not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="042d1-143">假设你有讨论论坛，你会想要防止`"bob@cpandl.com"`注册为`"joe@contoso.com"`。</span><span class="sxs-lookup"><span data-stu-id="042d1-143">Suppose you had a discussion forum, you would want to prevent `"bob@cpandl.com"` from registering as `"joe@contoso.com"`.</span></span> <span data-ttu-id="042d1-144">而无需电子邮件确认`"joe@contoso.com"`无法收到您的应用程序不需要的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="042d1-144">Without email confirmation, `"joe@contoso.com"` could get unwanted email from your app.</span></span> <span data-ttu-id="042d1-145">假设 Bob 意外注册为`"bib@cpandl.com"`没有注意到，他将无法使用密码恢复，因为此应用没有其正确的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="042d1-145">Suppose Bob accidently registered as `"bib@cpandl.com"` and hadn't noticed it, he wouldn't be able to use password recovery because the app doesn't have his correct email.</span></span> <span data-ttu-id="042d1-146">电子邮件确认从智能机器人提供有限的保护，并且不从确定垃圾邮件发送者提供保护。</span><span class="sxs-lookup"><span data-stu-id="042d1-146">Email confirmation provides only limited protection from bots and doesn't provide protection from determined spammers.</span></span>

<span data-ttu-id="042d1-147">你通常想要发布到你的网站的任何数据，它们已由 SMS 文本消息或另一种机制是电子邮件确认之前阻止新用户。</span><span class="sxs-lookup"><span data-stu-id="042d1-147">You generally want to prevent new users from posting any data to your website before they have been confirmed by either email, an SMS text message or another mechanism.</span></span> <span data-ttu-id="042d1-148">在以下部分中，我们将启用电子邮件确认，并修改代码以防止新注册的用户登录之前确认其电子邮件。</span><span class="sxs-lookup"><span data-stu-id="042d1-148">In the sections below, we will enable email confirmation and modify the code to prevent newly registered users from logging in until their email has been confirmed.</span></span> <span data-ttu-id="042d1-149">在本教程中，将使用电子邮件服务 SendGrid。</span><span class="sxs-lookup"><span data-stu-id="042d1-149">You'll use the email service SendGrid in this tutorial.</span></span>

<a id="SG"></a>
## <a name="hook-up-sendgrid"></a><span data-ttu-id="042d1-150">将挂接到 SendGrid</span><span class="sxs-lookup"><span data-stu-id="042d1-150">Hook up SendGrid</span></span>

<span data-ttu-id="042d1-151">本教程后，SendGrid 已更改它的 API。</span><span class="sxs-lookup"><span data-stu-id="042d1-151">SendGrid has changed it's API since this tutorial was written.</span></span> <span data-ttu-id="042d1-152">有关当前 SendGrid 的说明，请参阅[SendGrid](http://sendgrid.com/)或[启用帐户确认和密码恢复](xref:security/authentication/accconfirm#enable-account-confirmation-and-password-recovery)。</span><span class="sxs-lookup"><span data-stu-id="042d1-152">For current SendGrid instructions, see [SendGrid](http://sendgrid.com/) or [Enable account confirmation and password recovery](xref:security/authentication/accconfirm#enable-account-confirmation-and-password-recovery).</span></span>

<span data-ttu-id="042d1-153">虽然本教程仅演示了如何添加通过电子邮件通知[SendGrid](http://sendgrid.com/)，可以发送电子邮件使用 SMTP 和其他机制 (请参阅[的其他资源](#addRes))。</span><span class="sxs-lookup"><span data-stu-id="042d1-153">Although this tutorial only shows how to add email notification through [SendGrid](http://sendgrid.com/), you can send email using SMTP and other mechanisms (see [additional resources](#addRes)).</span></span>

1. <span data-ttu-id="042d1-154">在 Visual Studio 中打开**程序包管理器控制台**(**工具** - &gt; **NuGet 程序包管理器** - &gt;**程序包管理器控制台**)，并输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="042d1-154">In Visual Studio, open the **Package Manager Console** (**Tools** -&gt; **NuGet Package Manger** -&gt; **Package Manager Console**), and enter the following command:</span></span>  
    `Install-Package SendGrid`
2. <span data-ttu-id="042d1-155">转到[注册页面，Azure SendGrid](https://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/)并注册免费的 SendGrid 帐户。</span><span class="sxs-lookup"><span data-stu-id="042d1-155">Go to the [Azure SendGrid sign-up page](https://azure.microsoft.com/gallery/store/sendgrid/sendgrid-azure/) and register for free SendGrid account.</span></span> <span data-ttu-id="042d1-156">可以是直接在免费的 SendGrid 帐户还注册[SendGrid 的站点](http://www.sendgrid.com)。</span><span class="sxs-lookup"><span data-stu-id="042d1-156">You can also sign-up for a free SendGrid account directly on [SendGrid's site](http://www.sendgrid.com).</span></span>
3. <span data-ttu-id="042d1-157">从**解决方案资源管理器**打开*IdentityConfig.cs*文件中*应用\_启动*文件夹并添加以下代码以的黄色突出显示`EmailService`类，以配置**SendGrid**:</span><span class="sxs-lookup"><span data-stu-id="042d1-157">From **Solution Explorer** open the *IdentityConfig.cs* file in the *App\_Start* folder and add the following code highlighted in yellow to the `EmailService` class to configure **SendGrid**:</span></span>

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample1.cs?highlight=3,5,8-37)]
4. <span data-ttu-id="042d1-158">此外，添加以下`using`语句的开头*IdentityConfig.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="042d1-158">Also, add the following `using` statements to the beginning of the *IdentityConfig.cs* file:</span></span> 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample2.cs?highlight=1-4)]
5. <span data-ttu-id="042d1-159">若要使此示例简单明了，将存储中的电子邮件服务帐户值`appSettings`一部分*web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="042d1-159">To keep this sample simple, you'll store the email service account values in the `appSettings` section of the *web.config* file.</span></span> <span data-ttu-id="042d1-160">添加以下 XML 中为黄色突出显示*web.config*你的项目的根目录下的文件：</span><span class="sxs-lookup"><span data-stu-id="042d1-160">Add the following XML highlighted in yellow to the *web.config* file at the root of your project:</span></span>

    [!code-xml[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample3.xml?highlight=2-5)]

    > [!WARNING]
    > <span data-ttu-id="042d1-161">安全性-永远不会存储在源代码中敏感数据。</span><span class="sxs-lookup"><span data-stu-id="042d1-161">Security - Never store sensitive data in your source code.</span></span> <span data-ttu-id="042d1-162">在此示例中，帐户和凭据存储在**appSetting**一部分*Web.config*文件。</span><span class="sxs-lookup"><span data-stu-id="042d1-162">In this example, the account and credentials are stored in the **appSetting** section of the *Web.config* file.</span></span> <span data-ttu-id="042d1-163">在 Azure 上，您可以安全地存储这些值在 **[配置](https://blogs.msdn.com/b/webdev/archive/2014/06/04/queuebackgroundworkitem-to-reliably-schedule-and-run-long-background-process-in-asp-net.aspx)** 在 Azure 门户中的选项卡。</span><span class="sxs-lookup"><span data-stu-id="042d1-163">On Azure, you can securely store these values on the **[Configure](https://blogs.msdn.com/b/webdev/archive/2014/06/04/queuebackgroundworkitem-to-reliably-schedule-and-run-long-background-process-in-asp-net.aspx)** tab in the Azure portal.</span></span> <span data-ttu-id="042d1-164">有关相关信息请参阅标题为 Rick Anderson 主题[的密码和其他敏感数据部署到 ASP.NET 和 Azure 最佳做法](https://go.microsoft.com/fwlink/?LinkId=513141)。</span><span class="sxs-lookup"><span data-stu-id="042d1-164">For related information see Rick Anderson's topic titled [Best practices for deploying passwords and other sensitive data to ASP.NET and Azure](https://go.microsoft.com/fwlink/?LinkId=513141).</span></span>
6. <span data-ttu-id="042d1-165">添加电子邮件服务值以反映你的 SendGrid 身份验证值 （用户名和密码），以便可以成功从应用发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="042d1-165">Add the email service values to reflect your SendGrid authentication values (User Name and Password) so that you can successful send email from your app.</span></span> <span data-ttu-id="042d1-166">请务必使用你的 SendGrid 帐户名称而不是你提供 SendGrid 的电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="042d1-166">Be sure to use your SendGrid account name rather than the email address you provided SendGrid.</span></span>

### <a name="enable-email-confirmation"></a><span data-ttu-id="042d1-167">启用电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="042d1-167">Enable Email Confirmation</span></span>

 <span data-ttu-id="042d1-168">若要启用电子邮件确认，您将修改的注册代码，使用以下步骤。</span><span class="sxs-lookup"><span data-stu-id="042d1-168">To enable email confirmation, you'll modify the registration code using the following steps.</span></span>  
 

1. <span data-ttu-id="042d1-169">在中*帐户*文件夹中，打开*Register.aspx.cs*的代码隐藏和更新`CreateUser_Click`方法，以使以下突出显示的更改：</span><span class="sxs-lookup"><span data-stu-id="042d1-169">In the *Account* folder, open the *Register.aspx.cs* code-behind and update the `CreateUser_Click` method to enable the following highlighted changes:</span></span> 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample4.cs?highlight=9-11)]
2. <span data-ttu-id="042d1-170">在中**解决方案资源管理器**，右键单击*Default.aspx* ，然后选择**设为起始页**。</span><span class="sxs-lookup"><span data-stu-id="042d1-170">In **Solution Explorer**, right-click *Default.aspx* and select **Set As Start Page**.</span></span>
3. <span data-ttu-id="042d1-171">运行应用，请按**F5。**</span><span class="sxs-lookup"><span data-stu-id="042d1-171">Run the app by pressing **F5.**</span></span> <span data-ttu-id="042d1-172">将显示的页后，单击**注册**链接以显示注册页。</span><span class="sxs-lookup"><span data-stu-id="042d1-172">After the page is displayed, click the **Register** link to display the Register page.</span></span>
4. <span data-ttu-id="042d1-173">输入你的电子邮件和密码，然后单击**注册**按钮发送通过 SendGrid 电子邮件。</span><span class="sxs-lookup"><span data-stu-id="042d1-173">Enter your email and password, then click the **Register** button to send an email message via SendGrid.</span></span>  
   <span data-ttu-id="042d1-174">你的项目和代码的当前状态将允许用户登录后其完成注册表单中，即使他们尚未确认其帐户也是如此。</span><span class="sxs-lookup"><span data-stu-id="042d1-174">The current state of your project and code will allow the user to log in once they complete the registration form, even though they haven't confirmed their account.</span></span>
5. <span data-ttu-id="042d1-175">检查你的电子邮件帐户，并单击链接以确认你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="042d1-175">Check your email account and click on the link to confirm your email.</span></span>  
   <span data-ttu-id="042d1-176">一旦提交注册表单，您将登录。</span><span class="sxs-lookup"><span data-stu-id="042d1-176">Once you submit the registration form, you will be logged in.</span></span>  
    ![示例网站的登录](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/_static/image4.png)

<a id="require"></a>
## <a name="require-email-confirmation-before-log-in"></a><span data-ttu-id="042d1-178">需要电子邮件确认之前登录</span><span class="sxs-lookup"><span data-stu-id="042d1-178">Require Email Confirmation Before Log In</span></span>

<span data-ttu-id="042d1-179">尽管已确认的电子邮件帐户，此时不需要完全签名中的验证电子邮件中包含的链接上单击。</span><span class="sxs-lookup"><span data-stu-id="042d1-179">Although you have confirmed the email account, at this point you would not need to click on the link contained in the verification email to be fully signed-in.</span></span> <span data-ttu-id="042d1-180">在以下部分中，将修改要求新用户在登录之前能够确认电子邮件 （通过身份验证） 的代码。</span><span class="sxs-lookup"><span data-stu-id="042d1-180">In the following section, you will modify the code requiring new users to have a confirmed email before they are logged in (authenticated).</span></span>

1. <span data-ttu-id="042d1-181">在中**解决方案资源管理器**的 Visual Studio 中，更新`CreateUser_Click`中的事件*Register.aspx.cs*隐藏代码中包含*帐户*文件夹以下突出显示的更改：</span><span class="sxs-lookup"><span data-stu-id="042d1-181">In **Solution Explorer** of Visual Studio, update the `CreateUser_Click` event in the *Register.aspx.cs* code-behind contained in the *Accounts* folder with the following highlighted changes:</span></span> 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample5.cs?highlight=13-14,17-21)]
2. <span data-ttu-id="042d1-182">更新`LogIn`中的方法*Login.aspx.cs*代码隐藏有以下突出显示更改：</span><span class="sxs-lookup"><span data-stu-id="042d1-182">Update the `LogIn` method in the *Login.aspx.cs* code-behind with the following highlighted changes:</span></span> 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample6.cs?highlight=9-19,45-46)]

### <a name="run-the-application"></a><span data-ttu-id="042d1-183">运行应用程序</span><span class="sxs-lookup"><span data-stu-id="042d1-183">Run the Application</span></span>

 <span data-ttu-id="042d1-184">现在，已实现的代码来检查是否已确认用户的电子邮件地址，你可以检查的功能上都**注册**并**登录名**页。</span><span class="sxs-lookup"><span data-stu-id="042d1-184">Now that you have implemented the code to check whether a user's email address has been confirmed, you can check the functionality on both the **Register** and **Login** pages.</span></span> 

1. <span data-ttu-id="042d1-185">删除中的任何帐户**AspNetUsers**包含你要测试的电子邮件别名的表。</span><span class="sxs-lookup"><span data-stu-id="042d1-185">Delete any accounts in the **AspNetUsers** table that contain the email alias you wish to test.</span></span>
2. <span data-ttu-id="042d1-186">运行该应用程序 (**F5**)，并验证直到您已确认你的电子邮件地址不能注册为用户。</span><span class="sxs-lookup"><span data-stu-id="042d1-186">Run the app (**F5**) and verify you cannot register as a user until you have confirmed your email address.</span></span>
3. <span data-ttu-id="042d1-187">在确认新的帐户通过刚发送的电子邮件前, 尝试新的帐户登录。</span><span class="sxs-lookup"><span data-stu-id="042d1-187">Before confirming your new account via the email that was just sent, attempt to log in with the new account.</span></span>  
 <span data-ttu-id="042d1-188">将看到你不能以用户身份登录，您必须具有一个已确认电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="042d1-188">You'll see that you are unable to log in and that you must have a confirmed email account.</span></span>
4. <span data-ttu-id="042d1-189">一旦确认电子邮件地址，请登录到应用程序。</span><span class="sxs-lookup"><span data-stu-id="042d1-189">Once you confirm your email address, log in to the app.</span></span>

<a id="reset"></a>
## <a name="password-recovery-and-reset"></a><span data-ttu-id="042d1-190">密码恢复和重置</span><span class="sxs-lookup"><span data-stu-id="042d1-190">Password Recovery and Reset</span></span>

1. <span data-ttu-id="042d1-191">在 Visual Studio 中，删除中的注释字符`Forgot`中的方法*Forgot.aspx.cs*隐藏代码中包含*帐户*文件夹中，因此，该方法如下所示：</span><span class="sxs-lookup"><span data-stu-id="042d1-191">In Visual Studio, remove the comment characters from the `Forgot` method in the *Forgot.aspx.cs* code-behind contained in the *Account* folder, so that the method appears as follows:</span></span> 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample7.cs?highlight=16-18)]
2. <span data-ttu-id="042d1-192">打开*Login.aspx*页。</span><span class="sxs-lookup"><span data-stu-id="042d1-192">Open the *Login.aspx* page.</span></span> <span data-ttu-id="042d1-193">替换标记末尾附近**loginForm**部分下面突出显示的那样：</span><span class="sxs-lookup"><span data-stu-id="042d1-193">Replace the markup near the end of the **loginForm** section as highlighted below:</span></span> 

    [!code-aspx[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample8.aspx?highlight=52-53)]
3. <span data-ttu-id="042d1-194">打开*Login.aspx.cs*的代码隐藏和取消注释以下行以从黄色突出显示的代码`Page_Load`事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="042d1-194">Open the *Login.aspx.cs* code-behind and uncomment the following line of code highlighted in yellow from the `Page_Load` event handler:</span></span> 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample9.cs?highlight=5)]
4. <span data-ttu-id="042d1-195">运行应用，请按**F5。**</span><span class="sxs-lookup"><span data-stu-id="042d1-195">Run the app by pressing **F5.**</span></span> <span data-ttu-id="042d1-196">将显示的页后，单击**登录**链接。</span><span class="sxs-lookup"><span data-stu-id="042d1-196">After the page is displayed, click the **Log in** link.</span></span>
5. <span data-ttu-id="042d1-197">单击**忘记了密码？** 链接以显示**忘记了密码**页。</span><span class="sxs-lookup"><span data-stu-id="042d1-197">Click the **Forgot your password?** link to display the **Forgot Password** page.</span></span>
6. <span data-ttu-id="042d1-198">输入你的电子邮件地址，然后单击**提交**按钮将发送一封电子邮件给你这将允许您重置密码的地址。</span><span class="sxs-lookup"><span data-stu-id="042d1-198">Enter your email address and click the **Submit** button to send an email to your address which will allow you to reset your password.</span></span>   
   <span data-ttu-id="042d1-199">检查您的电子邮件帐户，然后单击链接以显示**重置密码**页。</span><span class="sxs-lookup"><span data-stu-id="042d1-199">Check your email account and click on the link to display the **Reset Password** page.</span></span>
7. <span data-ttu-id="042d1-200">上**重置密码**页上，输入电子邮件、 密码和确认的密码。</span><span class="sxs-lookup"><span data-stu-id="042d1-200">On the **Reset Password** page, enter your email, password, and confirmed password.</span></span> <span data-ttu-id="042d1-201">然后，按**重置**按钮。</span><span class="sxs-lookup"><span data-stu-id="042d1-201">Then, press the **Reset** button.</span></span>  
   <span data-ttu-id="042d1-202">已成功重置密码，当**更改密码**，将显示页。</span><span class="sxs-lookup"><span data-stu-id="042d1-202">When you successfully reset your password, the **Password Changed** page will be displayed.</span></span> <span data-ttu-id="042d1-203">现在可以登录您的新密码。</span><span class="sxs-lookup"><span data-stu-id="042d1-203">Now you can log in with your new password.</span></span>

<a id="rsend"></a>
## <a name="resend-email-confirmation-link"></a><span data-ttu-id="042d1-204">重新发送电子邮件确认链接</span><span class="sxs-lookup"><span data-stu-id="042d1-204">Resend Email Confirmation Link</span></span>

<span data-ttu-id="042d1-205">一旦用户创建新的本地帐户时，它们是电子邮件通知他们需要使用在登录之前确认链接。</span><span class="sxs-lookup"><span data-stu-id="042d1-205">Once a user creates a new local account, they are emailed a confirmation link they are required to use before they can log on.</span></span> <span data-ttu-id="042d1-206">如果用户意外删除的确认电子邮件，或者永远不会收到电子邮件，他们将需要再次发送的确认链接。</span><span class="sxs-lookup"><span data-stu-id="042d1-206">If the user accidently deletes the confirmation email, or the email never arrives, they will need the confirmation link sent again.</span></span> <span data-ttu-id="042d1-207">以下代码更改演示如何启用此功能。</span><span class="sxs-lookup"><span data-stu-id="042d1-207">The following code changes show how to enable this.</span></span>

1. <span data-ttu-id="042d1-208">在 Visual Studio 中打开**Login.aspx.cs**隐藏代码，并添加以下事件处理程序后的`LogIn`事件处理程序：</span><span class="sxs-lookup"><span data-stu-id="042d1-208">In Visual Studio, open the **Login.aspx.cs** code-behind and add the following event handler after the `LogIn` event handler:</span></span>   

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample10.cs)]
2. <span data-ttu-id="042d1-209">修改`LogIn`中的事件处理程序*Login.aspx.cs*代码隐藏，如下所示以黄色突出显示的代码：</span><span class="sxs-lookup"><span data-stu-id="042d1-209">Modify the `LogIn` event handler in the *Login.aspx.cs* code-behind by changing the code highlighted in yellow as follows:</span></span> 

    [!code-csharp[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample11.cs?highlight=15-17)]
3. <span data-ttu-id="042d1-210">更新*Login.aspx*通过添加，如下所示以黄色突出显示的代码页：</span><span class="sxs-lookup"><span data-stu-id="042d1-210">Update the *Login.aspx* page by adding the code highlighted in yellow as follows:</span></span> 

    [!code-aspx[Main](create-a-secure-aspnet-web-forms-app-with-user-registration-email-confirmation-and-password-reset/samples/sample12.aspx?highlight=45-46)]
4. <span data-ttu-id="042d1-211">删除中的任何帐户**AspNetUsers**包含你要测试的电子邮件别名的表。</span><span class="sxs-lookup"><span data-stu-id="042d1-211">Delete any accounts in the **AspNetUsers** table that contain the email alias you wish to test.</span></span>
5. <span data-ttu-id="042d1-212">运行该应用程序 (**F5**)，并注册你的电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="042d1-212">Run the app (**F5**) and register your email address.</span></span>
6. <span data-ttu-id="042d1-213">在确认新的帐户通过刚发送的电子邮件前, 尝试新的帐户登录。</span><span class="sxs-lookup"><span data-stu-id="042d1-213">Before confirming your new account via the email that was just sent, attempt to log in with the new account.</span></span>  
   <span data-ttu-id="042d1-214">将看到你不能以用户身份登录，您必须具有一个已确认电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="042d1-214">You'll see that you are unable to log in and that you must have a confirmed email account.</span></span> <span data-ttu-id="042d1-215">此外，现在可以重新一条确认消息发送到你的电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="042d1-215">In addition, you can now resend a confirmation message to your email account.</span></span>
7. <span data-ttu-id="042d1-216">输入你的电子邮件地址和密码，然后按**重新发送确认**按钮。</span><span class="sxs-lookup"><span data-stu-id="042d1-216">Enter your email address and password, then press the **Resend confirmation** button.</span></span>
8. <span data-ttu-id="042d1-217">一旦确认你基于新发送的电子邮件的电子邮件地址，请登录到应用程序。</span><span class="sxs-lookup"><span data-stu-id="042d1-217">Once you confirm your email address based on the newly sent email message, log in to the app.</span></span>

<a id="dbg"></a>
## <a name="troubleshooting-the-app"></a><span data-ttu-id="042d1-218">故障排除应用程序</span><span class="sxs-lookup"><span data-stu-id="042d1-218">Troubleshooting the App</span></span>

<span data-ttu-id="042d1-219">如果未收到一封包含链接以验证您的凭据：</span><span class="sxs-lookup"><span data-stu-id="042d1-219">If you don't receive an email containing the link to verify your credentials:</span></span>

- <span data-ttu-id="042d1-220">请检查垃圾邮件或垃圾邮件文件夹。</span><span class="sxs-lookup"><span data-stu-id="042d1-220">Check your junk or spam folder.</span></span>
- <span data-ttu-id="042d1-221">登录到你的 SendGrid 帐户，然后单击[电子邮件活动链接](https://sendgrid.com/logs/index)。</span><span class="sxs-lookup"><span data-stu-id="042d1-221">Log into your SendGrid account and click on the [Email Activity link](https://sendgrid.com/logs/index).</span></span>
- <span data-ttu-id="042d1-222">请确保使用为您的 SendGrid 用户帐户名称*Web.config*值而不是你的 SendGrid 帐户电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="042d1-222">Be certain you used your SendGrid user account name as a *Web.config* value rather than your SendGrid account email address.</span></span>

<a id="addRes"></a>
## <a name="additional-resources"></a><span data-ttu-id="042d1-223">其他资源</span><span class="sxs-lookup"><span data-stu-id="042d1-223">Additional Resources</span></span>

- [<span data-ttu-id="042d1-224">ASP.NET 标识的链接的推荐资源</span><span class="sxs-lookup"><span data-stu-id="042d1-224">Links to ASP.NET Identity Recommended Resources</span></span>](../../../identity/overview/getting-started/aspnet-identity-recommended-resources.md)
- [<span data-ttu-id="042d1-225">帐户确认和密码恢复与 ASP.NET 标识</span><span class="sxs-lookup"><span data-stu-id="042d1-225">Account Confirmation and Password Recovery with ASP.NET Identity</span></span>](../../../identity/overview/features-api/account-confirmation-and-password-recovery-with-aspnet-identity.md)
- [<span data-ttu-id="042d1-226">ASP.NET Web 窗体的教程系列-添加 OAuth 2.0 提供程序</span><span class="sxs-lookup"><span data-stu-id="042d1-226">ASP.NET Web Forms tutorial series - Add an OAuth 2.0 Provider</span></span>](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#OAuthWebForms)
- [<span data-ttu-id="042d1-227">包含成员资格、 OAuth 和 SQL 数据库的安全的 ASP.NET Web 窗体应用部署到 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="042d1-227">Deploy a Secure ASP.NET Web Forms App with Membership, OAuth, and SQL Database to Azure App Service</span></span>](https://azure.microsoft.com/documentation/articles/web-sites-dotnet-deploy-aspnet-webforms-app-membership-oauth-sql-database/)
- [<span data-ttu-id="042d1-228">ASP.NET Web 窗体教程系列的项目启用 SSL</span><span class="sxs-lookup"><span data-stu-id="042d1-228">ASP.NET Web Forms tutorial series - Enable SSL for the Project</span></span>](../getting-started/getting-started-with-aspnet-45-web-forms/checkout-and-payment-with-paypal.md#SSLWebForms)
