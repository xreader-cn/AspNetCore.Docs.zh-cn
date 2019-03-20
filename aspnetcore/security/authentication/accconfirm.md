---
title: 帐户确认和 ASP.NET Core 中的密码恢复
author: rick-anderson
description: 了解如何生成使用电子邮件确认及密码重置功能的 ASP.NET Core 应用程序。
ms.author: riande
ms.date: 3/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: d102ed0a4a75f6273fcda0a8cc7e9d091ff94b50
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209911"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="209aa-103">帐户确认和 ASP.NET Core 中的密码恢复</span><span class="sxs-lookup"><span data-stu-id="209aa-103">Account confirmation and password recovery in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="209aa-104">请参阅[此 PDF 文件](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)ASP.NET Core 1.1 和 2.1 版本。</span><span class="sxs-lookup"><span data-stu-id="209aa-104">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 and 2.1 version.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="209aa-105">通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Ponant](https://github.com/Ponant)，和[Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="209aa-105">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="209aa-106">本教程演示如何生成 ASP.NET Core 应用使用电子邮件确认及密码重置。</span><span class="sxs-lookup"><span data-stu-id="209aa-106">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="209aa-107">本教程是**不**开头主题。</span><span class="sxs-lookup"><span data-stu-id="209aa-107">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="209aa-108">您应熟悉：</span><span class="sxs-lookup"><span data-stu-id="209aa-108">You should be familiar with:</span></span>

* [<span data-ttu-id="209aa-109">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="209aa-109">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="209aa-110">身份验证</span><span class="sxs-lookup"><span data-stu-id="209aa-110">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="209aa-111">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="209aa-111">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

## <a name="prerequisites"></a><span data-ttu-id="209aa-112">系统必备</span><span class="sxs-lookup"><span data-stu-id="209aa-112">Prerequisites</span></span>

[<span data-ttu-id="209aa-113">.NET core 2.2 SDK 或更高版本</span><span class="sxs-lookup"><span data-stu-id="209aa-113">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="209aa-114">创建 web 应用并创建标识的基架</span><span class="sxs-lookup"><span data-stu-id="209aa-114">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="209aa-115">运行以下命令以创建具有身份验证的 web 应用。</span><span class="sxs-lookup"><span data-stu-id="209aa-115">Run the following commands to create a web app with authentication.</span></span>

```console
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="209aa-116">测试新的用户注册</span><span class="sxs-lookup"><span data-stu-id="209aa-116">Test new user registration</span></span>

<span data-ttu-id="209aa-117">运行该应用程序中，选择**注册**链接，并注册用户。</span><span class="sxs-lookup"><span data-stu-id="209aa-117">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="209aa-118">在此情况下，电子邮件的唯一验证是使用[[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute)属性。</span><span class="sxs-lookup"><span data-stu-id="209aa-118">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="209aa-119">提交后注册，登录到应用程序。</span><span class="sxs-lookup"><span data-stu-id="209aa-119">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="209aa-120">更高版本在本教程中，以便新用户不能登录，直到其电子邮件验证更新代码。</span><span class="sxs-lookup"><span data-stu-id="209aa-120">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="209aa-121">请注意此表的`EmailConfirmed`字段是`False`。</span><span class="sxs-lookup"><span data-stu-id="209aa-121">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="209aa-122">您可能想要再次在下一步中使用此电子邮件，该应用将发送确认电子邮件时。</span><span class="sxs-lookup"><span data-stu-id="209aa-122">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="209aa-123">右键单击行并选择**删除**。</span><span class="sxs-lookup"><span data-stu-id="209aa-123">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="209aa-124">删除电子邮件别名便于您在以下步骤。</span><span class="sxs-lookup"><span data-stu-id="209aa-124">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>
## <a name="require-email-confirmation"></a><span data-ttu-id="209aa-125">需要电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="209aa-125">Require email confirmation</span></span>

<span data-ttu-id="209aa-126">它是最佳做法以确认新的用户注册的电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="209aa-126">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="209aa-127">电子邮件确认可帮助以验证它们没有模拟其他人 （即，他们尚未注册与其他人的电子邮件）。</span><span class="sxs-lookup"><span data-stu-id="209aa-127">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="209aa-128">假设有论坛，并且您想阻止"yli@example.com"中注册为"nolivetto@contoso.com"。</span><span class="sxs-lookup"><span data-stu-id="209aa-128">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="209aa-129">而无需电子邮件确认"nolivetto@contoso.com"可能会从您的应用程序收到不需要的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-129">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="209aa-130">假设用户意外地注册为"ylo@example.com"和"yli"的拼写错误的重视。</span><span class="sxs-lookup"><span data-stu-id="209aa-130">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="209aa-131">它们将无法使用密码恢复，因为此应用没有其正确的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-131">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="209aa-132">电子邮件确认从智能机器人提供有限的保护。</span><span class="sxs-lookup"><span data-stu-id="209aa-132">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="209aa-133">电子邮件确认不提供保护，免受恶意用户的与许多电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="209aa-133">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="209aa-134">你通常想要发布到网站的任何数据，有已确认的电子邮件之前阻止新用户。</span><span class="sxs-lookup"><span data-stu-id="209aa-134">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="209aa-135">更新`Startup.ConfigureServices`需要确认电子邮件：</span><span class="sxs-lookup"><span data-stu-id="209aa-135">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="209aa-136">`config.SignIn.RequireConfirmedEmail = true;` 可防止已注册的用户登录之前确认其电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-136">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="209aa-137">配置电子邮件提供商</span><span class="sxs-lookup"><span data-stu-id="209aa-137">Configure email provider</span></span>

<span data-ttu-id="209aa-138">在本教程中， [SendGrid](https://sendgrid.com)用于发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-138">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="209aa-139">你需要一个 SendGrid 帐户和密钥用于发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-139">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="209aa-140">可以使用其他电子邮件提供商。</span><span class="sxs-lookup"><span data-stu-id="209aa-140">You can use other email providers.</span></span> <span data-ttu-id="209aa-141">ASP.NET Core 2.x 包括`System.Net.Mail`，这允许你从你的应用程序发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-141">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="209aa-142">我们建议你使用 SendGrid 或另一个电子邮件服务发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-142">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="209aa-143">SMTP 很难保护并正确设置。</span><span class="sxs-lookup"><span data-stu-id="209aa-143">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="209aa-144">创建一个类来提取的安全电子邮件键。</span><span class="sxs-lookup"><span data-stu-id="209aa-144">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="209aa-145">对于此示例中，创建*Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="209aa-145">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="209aa-146">配置 SendGrid 用户机密</span><span class="sxs-lookup"><span data-stu-id="209aa-146">Configure SendGrid user secrets</span></span>

<span data-ttu-id="209aa-147">设置`SendGridUser`并`SendGridKey`与[机密管理器工具](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="209aa-147">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="209aa-148">例如：</span><span class="sxs-lookup"><span data-stu-id="209aa-148">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="209aa-149">在 Windows 中，机密管理器存储中的键/值对*secrets.json*文件中`%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`目录。</span><span class="sxs-lookup"><span data-stu-id="209aa-149">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="209aa-150">内容*secrets.json*未加密文件。</span><span class="sxs-lookup"><span data-stu-id="209aa-150">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="209aa-151">下面的标记演示*secrets.json*文件。</span><span class="sxs-lookup"><span data-stu-id="209aa-151">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="209aa-152">`SendGridKey`删除值。</span><span class="sxs-lookup"><span data-stu-id="209aa-152">The `SendGridKey` value has been removed.</span></span>

 ```json
  {
    "SendGridUser": "RickAndMSFT",
    "SendGridKey": "<key removed>"
  }
  ```
 
<span data-ttu-id="209aa-153">有关详细信息，请参阅[选项模式](xref:fundamentals/configuration/options)并[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="209aa-153">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="209aa-154">安装 SendGrid</span><span class="sxs-lookup"><span data-stu-id="209aa-154">Install SendGrid</span></span>

<span data-ttu-id="209aa-155">本教程演示如何添加通过电子邮件通知[SendGrid](https://sendgrid.com/)，但是你可以发送电子邮件使用 SMTP 和其他机制。</span><span class="sxs-lookup"><span data-stu-id="209aa-155">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="209aa-156">安装`SendGrid`NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="209aa-156">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="209aa-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="209aa-157">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="209aa-158">从包管理器控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="209aa-158">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="209aa-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="209aa-159">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="209aa-160">从控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="209aa-160">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

------

<span data-ttu-id="209aa-161">请参阅[免费开始使用 SendGrid](https://sendgrid.com/free/)注册一个免费的 SendGrid 帐户。</span><span class="sxs-lookup"><span data-stu-id="209aa-161">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>
### <a name="implement-iemailsender"></a><span data-ttu-id="209aa-162">实现 IEmailSender</span><span class="sxs-lookup"><span data-stu-id="209aa-162">Implement IEmailSender</span></span>

<span data-ttu-id="209aa-163">为实现`IEmailSender`，创建*Services/EmailSender.cs* ，类似于以下代码：</span><span class="sxs-lookup"><span data-stu-id="209aa-163">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="209aa-164">将启动以支持电子邮件配置</span><span class="sxs-lookup"><span data-stu-id="209aa-164">Configure startup to support email</span></span>

<span data-ttu-id="209aa-165">将以下代码添加到`ConfigureServices`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="209aa-165">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="209aa-166">添加`EmailSender`作为暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="209aa-166">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="209aa-167">注册`AuthMessageSenderOptions`配置实例。</span><span class="sxs-lookup"><span data-stu-id="209aa-167">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="209aa-168">启用帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="209aa-168">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="209aa-169">该模板具有帐户确认和密码恢复的代码。</span><span class="sxs-lookup"><span data-stu-id="209aa-169">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="209aa-170">查找`OnPostAsync`中的方法*Areas/Identity/Pages/Account/Register.cshtml.cs*。</span><span class="sxs-lookup"><span data-stu-id="209aa-170">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="209aa-171">阻止新注册的用户自动登录通过注释掉以下行：</span><span class="sxs-lookup"><span data-stu-id="209aa-171">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="209aa-172">完整的方法与更改突出显示的行所示：</span><span class="sxs-lookup"><span data-stu-id="209aa-172">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="209aa-173">注册、 确认电子邮件，和重置密码</span><span class="sxs-lookup"><span data-stu-id="209aa-173">Register, confirm email, and reset password</span></span>

<span data-ttu-id="209aa-174">运行 web 应用和测试的帐户确认和密码恢复流。</span><span class="sxs-lookup"><span data-stu-id="209aa-174">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="209aa-175">运行应用并注册一个新用户</span><span class="sxs-lookup"><span data-stu-id="209aa-175">Run the app and register a new user</span></span>
* <span data-ttu-id="209aa-176">检查你的帐户确认链接的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-176">Check your email for the account confirmation link.</span></span> <span data-ttu-id="209aa-177">请参阅[调试电子邮件](#debug)如果没有收到电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-177">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="209aa-178">单击链接以确认你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-178">Click the link to confirm your email.</span></span>
* <span data-ttu-id="209aa-179">使用你的电子邮件和密码登录。</span><span class="sxs-lookup"><span data-stu-id="209aa-179">Sign in with your email and password.</span></span>
* <span data-ttu-id="209aa-180">注销。</span><span class="sxs-lookup"><span data-stu-id="209aa-180">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="209aa-181">查看管理页</span><span class="sxs-lookup"><span data-stu-id="209aa-181">View the manage page</span></span>

<span data-ttu-id="209aa-182">在浏览器中选择你的用户名：![用户名的浏览器窗口](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="209aa-182">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="209aa-183">管理页显示与**配置文件**选定的选项卡。</span><span class="sxs-lookup"><span data-stu-id="209aa-183">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="209aa-184">**电子邮件**显示已确认一个复选框，表明该电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-184">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="209aa-185">测试密码重置</span><span class="sxs-lookup"><span data-stu-id="209aa-185">Test password reset</span></span>

* <span data-ttu-id="209aa-186">如果你要登录，请选择**注销**。</span><span class="sxs-lookup"><span data-stu-id="209aa-186">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="209aa-187">选择**登录**链接，然后选择**忘记了密码？** 链接。</span><span class="sxs-lookup"><span data-stu-id="209aa-187">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="209aa-188">输入用于注册该帐户的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-188">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="209aa-189">发送一封电子邮件包含用于重置密码的链接。</span><span class="sxs-lookup"><span data-stu-id="209aa-189">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="209aa-190">检查你的电子邮件，并单击链接以重置密码。</span><span class="sxs-lookup"><span data-stu-id="209aa-190">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="209aa-191">已成功重置密码后，你可以使用你的电子邮件和新密码登录。</span><span class="sxs-lookup"><span data-stu-id="209aa-191">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="209aa-192">更改电子邮件和活动的超时值</span><span class="sxs-lookup"><span data-stu-id="209aa-192">Change email and activity timeout</span></span>

<span data-ttu-id="209aa-193">默认处于非活动状态超时值为 14 天。</span><span class="sxs-lookup"><span data-stu-id="209aa-193">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="209aa-194">下面的代码设置为 5 天的非活动超时：</span><span class="sxs-lookup"><span data-stu-id="209aa-194">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="209aa-195">更改所有数据保护令牌期限</span><span class="sxs-lookup"><span data-stu-id="209aa-195">Change all data protection token lifespans</span></span>

<span data-ttu-id="209aa-196">下面的代码更改为 3 个小时的所有数据保护令牌超时期限：</span><span class="sxs-lookup"><span data-stu-id="209aa-196">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="209aa-197">内置中标识的用户令牌 (请参阅[AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) 有[一天超时](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="209aa-197">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="209aa-198">更改电子邮件令牌有效期</span><span class="sxs-lookup"><span data-stu-id="209aa-198">Change the email token lifespan</span></span>

<span data-ttu-id="209aa-199">默认令牌有效期[标识的用户令牌](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)是[有一天](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="209aa-199">The default token lifespan of [the Identity user tokens](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/aspnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="209aa-200">本部分演示如何更改电子邮件令牌有效期。</span><span class="sxs-lookup"><span data-stu-id="209aa-200">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="209aa-201">添加自定义[DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)和<xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span><span class="sxs-lookup"><span data-stu-id="209aa-201">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="209aa-202">将自定义提供程序添加到服务容器：</span><span class="sxs-lookup"><span data-stu-id="209aa-202">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="209aa-203">重新发送电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="209aa-203">Resend email confirmation</span></span>

<span data-ttu-id="209aa-204">请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/5410)。</span><span class="sxs-lookup"><span data-stu-id="209aa-204">See [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>
### <a name="debug-email"></a><span data-ttu-id="209aa-205">调试电子邮件</span><span class="sxs-lookup"><span data-stu-id="209aa-205">Debug email</span></span>

<span data-ttu-id="209aa-206">如果无法获取电子邮件工作：</span><span class="sxs-lookup"><span data-stu-id="209aa-206">If you can't get email working:</span></span>

* <span data-ttu-id="209aa-207">中设置断点`EmailSender.Execute`若要验证`SendGridClient.SendEmailAsync`调用。</span><span class="sxs-lookup"><span data-stu-id="209aa-207">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="209aa-208">创建[控制台应用以发送电子邮件](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)使用类似的代码，到`EmailSender.Execute`。</span><span class="sxs-lookup"><span data-stu-id="209aa-208">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="209aa-209">审阅[电子邮件活动](https://sendgrid.com/docs/User_Guide/email_activity.html)页。</span><span class="sxs-lookup"><span data-stu-id="209aa-209">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="209aa-210">请检查垃圾邮件文件夹。</span><span class="sxs-lookup"><span data-stu-id="209aa-210">Check your spam folder.</span></span>
* <span data-ttu-id="209aa-211">请尝试其他电子邮件提供程序 （Microsoft、 Yahoo、 Gmail 等） 上的另一个电子邮件别名</span><span class="sxs-lookup"><span data-stu-id="209aa-211">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="209aa-212">尝试发送到不同的电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="209aa-212">Try sending to different email accounts.</span></span>

<span data-ttu-id="209aa-213">**最佳安全方案**设置为**不**中使用生产机密中测试和开发。</span><span class="sxs-lookup"><span data-stu-id="209aa-213">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="209aa-214">如果将应用发布到 Azure，可以为 Azure Web 应用门户中的应用程序设置来设置 SendGrid 机密。</span><span class="sxs-lookup"><span data-stu-id="209aa-214">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="209aa-215">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="209aa-215">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="209aa-216">合并的社交和本地登录帐户</span><span class="sxs-lookup"><span data-stu-id="209aa-216">Combine social and local login accounts</span></span>

<span data-ttu-id="209aa-217">若要完成本部分中，必须先启用外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="209aa-217">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="209aa-218">请参阅[Facebook、 Google、 和外部提供程序身份验证](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="209aa-218">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="209aa-219">您可以通过单击电子邮件链接组合本地和社交帐户。</span><span class="sxs-lookup"><span data-stu-id="209aa-219">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="209aa-220">按以下顺序"RickAndMSFT@gmail.com"首次创建为本地登录名; 但是，您可以创建帐户为社交登录名，然后添加本地登录名。</span><span class="sxs-lookup"><span data-stu-id="209aa-220">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web 应用程序：RickAndMSFT@gmail.com用户通过身份验证](accconfirm/_static/rick.png)

<span data-ttu-id="209aa-222">单击**管理**链接。</span><span class="sxs-lookup"><span data-stu-id="209aa-222">Click on the **Manage** link.</span></span> <span data-ttu-id="209aa-223">请注意与此帐户关联的 0 外部 （社交登录名）。</span><span class="sxs-lookup"><span data-stu-id="209aa-223">Note the 0 external (social logins) associated with this account.</span></span>

![管理视图](accconfirm/_static/manage.png)

<span data-ttu-id="209aa-225">单击链接到另一个登录名服务，接受应用程序请求。</span><span class="sxs-lookup"><span data-stu-id="209aa-225">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="209aa-226">下图中，在 Facebook 是外部身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="209aa-226">In the following image, Facebook is the external authentication provider:</span></span>

![管理你的外部登录名视图，其中列出 Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="209aa-228">已合并两个帐户。</span><span class="sxs-lookup"><span data-stu-id="209aa-228">The two accounts have been combined.</span></span> <span data-ttu-id="209aa-229">你将能够使用任一帐户登录。</span><span class="sxs-lookup"><span data-stu-id="209aa-229">You are able to sign in with either account.</span></span> <span data-ttu-id="209aa-230">您可能希望用户其社交登录身份验证服务发生故障，或已为其社交帐户无法访问更有可能的情况下添加本地帐户。</span><span class="sxs-lookup"><span data-stu-id="209aa-230">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="209aa-231">后一个站点中有用户启用帐户确认</span><span class="sxs-lookup"><span data-stu-id="209aa-231">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="209aa-232">与用户启用帐户确认站点上的阻止所有现有用户。</span><span class="sxs-lookup"><span data-stu-id="209aa-232">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="209aa-233">现有用户被锁定，因为不确认其帐户。</span><span class="sxs-lookup"><span data-stu-id="209aa-233">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="209aa-234">若要解决现有用户锁定，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="209aa-234">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="209aa-235">更新要将标记为正在确认的所有现有用户的数据库。</span><span class="sxs-lookup"><span data-stu-id="209aa-235">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="209aa-236">确认现有用户。</span><span class="sxs-lookup"><span data-stu-id="209aa-236">Confirm existing users.</span></span> <span data-ttu-id="209aa-237">例如，批的发送确认链接的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="209aa-237">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
