---
title: 帐户确认和 ASP.NET Core 中的密码恢复
author: rick-anderson
description: 了解如何生成使用电子邮件确认及密码重置功能的 ASP.NET Core 应用程序。
ms.author: riande
ms.date: 2/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 77d7b209d57f9ee44f158798ff780ce85c87aaf2
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56159403"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="a5775-103">帐户确认和 ASP.NET Core 中的密码恢复</span><span class="sxs-lookup"><span data-stu-id="a5775-103">Account confirmation and password recovery in ASP.NET Core</span></span>

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="a5775-104">请参阅[此 PDF 文件](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)ASP.NET Core 1.1 和 2.1 版本。</span><span class="sxs-lookup"><span data-stu-id="a5775-104">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 and 2.1 version.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="a5775-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="a5775-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="a5775-106">本教程演示如何生成 ASP.NET Core 应用使用电子邮件确认及密码重置。</span><span class="sxs-lookup"><span data-stu-id="a5775-106">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="a5775-107">本教程是**不**开头主题。</span><span class="sxs-lookup"><span data-stu-id="a5775-107">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="a5775-108">您应熟悉：</span><span class="sxs-lookup"><span data-stu-id="a5775-108">You should be familiar with:</span></span>

* [<span data-ttu-id="a5775-109">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a5775-109">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="a5775-110">身份验证</span><span class="sxs-lookup"><span data-stu-id="a5775-110">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="a5775-111">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="a5775-111">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

## <a name="prerequisites"></a><span data-ttu-id="a5775-112">系统必备</span><span class="sxs-lookup"><span data-stu-id="a5775-112">Prerequisites</span></span>

[!INCLUDE [](~/includes/2.1-SDK.md)]

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="a5775-113">创建 web 应用并创建标识的基架</span><span class="sxs-lookup"><span data-stu-id="a5775-113">Create a web  app and scaffold Identity</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5775-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5775-114">Visual Studio</span></span>](#tab/visual-studio) 

* <span data-ttu-id="a5775-115">在 Visual Studio 中，创建一个新**Web 应用程序**名为项目**WebPWrecover**。</span><span class="sxs-lookup"><span data-stu-id="a5775-115">In Visual Studio, create a new **Web Application** project named **WebPWrecover**.</span></span>
* <span data-ttu-id="a5775-116">选择“ASP.NET Core 2.1”。</span><span class="sxs-lookup"><span data-stu-id="a5775-116">Select **ASP.NET Core 2.1**.</span></span>
* <span data-ttu-id="a5775-117">保留默认值**身份验证**设置为**无身份验证**。</span><span class="sxs-lookup"><span data-stu-id="a5775-117">Keep the default **Authentication** set to **No Authentication**.</span></span> <span data-ttu-id="a5775-118">下一步中添加身份验证。</span><span class="sxs-lookup"><span data-stu-id="a5775-118">Authentication is added in the next step.</span></span>

<span data-ttu-id="a5775-119">在下一步：</span><span class="sxs-lookup"><span data-stu-id="a5775-119">In the next step:</span></span>

* <span data-ttu-id="a5775-120">设置布局页为 *~/Pages/Shared/_Layout.cshtml*</span><span class="sxs-lookup"><span data-stu-id="a5775-120">Set the layout page to *~/Pages/Shared/_Layout.cshtml*</span></span>
* <span data-ttu-id="a5775-121">选择*帐户/注册*</span><span class="sxs-lookup"><span data-stu-id="a5775-121">Select *Account/Register*</span></span>
* <span data-ttu-id="a5775-122">创建一个新**数据上下文类**</span><span class="sxs-lookup"><span data-stu-id="a5775-122">Create a new **Data context class**</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5775-123">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5775-123">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet new webapp -o WebPWrecover
cd WebPWrecover
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
dotnet aspnet-codegenerator identity -fi Account.Register -dc WebPWrecover.Models.WebPWrecoverContext
dotnet ef migrations add CreateIdentitySchema
dotnet ef database drop -f
dotnet ef database update
dotnet build
```

<span data-ttu-id="a5775-124">运行`dotnet aspnet-codegenerator identity --help`以获取有关基架工具的帮助。</span><span class="sxs-lookup"><span data-stu-id="a5775-124">Run `dotnet aspnet-codegenerator identity --help` to get help on the scaffolding tool.</span></span>

------

<span data-ttu-id="a5775-125">按照中的说明[启用身份验证](xref:security/authentication/scaffold-identity#useauthentication):</span><span class="sxs-lookup"><span data-stu-id="a5775-125">Follow the instructions in [Enable authentication](xref:security/authentication/scaffold-identity#useauthentication):</span></span>

* <span data-ttu-id="a5775-126">添加`app.UseAuthentication();`到 `Startup.Configure`</span><span class="sxs-lookup"><span data-stu-id="a5775-126">Add `app.UseAuthentication();` to `Startup.Configure`</span></span>
* <span data-ttu-id="a5775-127">添加`<partial name="_LoginPartial" />`布局文件。</span><span class="sxs-lookup"><span data-stu-id="a5775-127">Add `<partial name="_LoginPartial" />` to the layout file.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="a5775-128">测试新的用户注册</span><span class="sxs-lookup"><span data-stu-id="a5775-128">Test new user registration</span></span>

<span data-ttu-id="a5775-129">运行该应用程序中，选择**注册**链接，并注册用户。</span><span class="sxs-lookup"><span data-stu-id="a5775-129">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="a5775-130">在此情况下，电子邮件的唯一验证是使用[[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute)属性。</span><span class="sxs-lookup"><span data-stu-id="a5775-130">At this point, the only validation on the email is with the [[EmailAddress]](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="a5775-131">提交后注册，登录到应用程序。</span><span class="sxs-lookup"><span data-stu-id="a5775-131">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="a5775-132">更高版本在本教程中，以便新用户不能登录，直到其电子邮件验证更新代码。</span><span class="sxs-lookup"><span data-stu-id="a5775-132">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="a5775-133">请注意此表的`EmailConfirmed`字段是`False`。</span><span class="sxs-lookup"><span data-stu-id="a5775-133">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="a5775-134">您可能想要再次在下一步中使用此电子邮件，该应用将发送确认电子邮件时。</span><span class="sxs-lookup"><span data-stu-id="a5775-134">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="a5775-135">右键单击行并选择**删除**。</span><span class="sxs-lookup"><span data-stu-id="a5775-135">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="a5775-136">删除电子邮件别名便于您在以下步骤。</span><span class="sxs-lookup"><span data-stu-id="a5775-136">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>
## <a name="require-email-confirmation"></a><span data-ttu-id="a5775-137">需要电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="a5775-137">Require email confirmation</span></span>

<span data-ttu-id="a5775-138">它是最佳做法以确认新的用户注册的电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="a5775-138">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="a5775-139">电子邮件确认可帮助以验证它们没有模拟其他人 （即，他们尚未注册与其他人的电子邮件）。</span><span class="sxs-lookup"><span data-stu-id="a5775-139">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="a5775-140">假设有论坛，并且您想阻止"yli@example.com"中注册为"nolivetto@contoso.com"。</span><span class="sxs-lookup"><span data-stu-id="a5775-140">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="a5775-141">而无需电子邮件确认"nolivetto@contoso.com"可能会从您的应用程序收到不需要的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-141">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="a5775-142">假设用户意外地注册为"ylo@example.com"和"yli"的拼写错误的重视。</span><span class="sxs-lookup"><span data-stu-id="a5775-142">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="a5775-143">它们将无法使用密码恢复，因为此应用没有其正确的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-143">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="a5775-144">电子邮件确认从智能机器人提供有限的保护。</span><span class="sxs-lookup"><span data-stu-id="a5775-144">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="a5775-145">电子邮件确认不提供保护，免受恶意用户的与许多电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="a5775-145">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="a5775-146">你通常想要发布到网站的任何数据，有已确认的电子邮件之前阻止新用户。</span><span class="sxs-lookup"><span data-stu-id="a5775-146">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="a5775-147">更新*Areas/Identity/IdentityHostingStartup.cs*需要确认电子邮件：</span><span class="sxs-lookup"><span data-stu-id="a5775-147">Update *Areas/Identity/IdentityHostingStartup.cs*  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Areas/Identity/IdentityHostingStartup.cs?name=snippet1&highlight=10-13)]

<span data-ttu-id="a5775-148">`config.SignIn.RequireConfirmedEmail = true;` 可防止已注册的用户登录之前确认其电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-148">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="a5775-149">配置电子邮件提供商</span><span class="sxs-lookup"><span data-stu-id="a5775-149">Configure email provider</span></span>

<span data-ttu-id="a5775-150">在本教程中， [SendGrid](https://sendgrid.com)用于发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-150">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="a5775-151">你需要一个 SendGrid 帐户和密钥用于发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-151">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="a5775-152">可以使用其他电子邮件提供商。</span><span class="sxs-lookup"><span data-stu-id="a5775-152">You can use other email providers.</span></span> <span data-ttu-id="a5775-153">ASP.NET Core 2.x 包括`System.Net.Mail`，这允许你从你的应用程序发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-153">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="a5775-154">我们建议你使用 SendGrid 或另一个电子邮件服务发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-154">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="a5775-155">SMTP 很难保护并正确设置。</span><span class="sxs-lookup"><span data-stu-id="a5775-155">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="a5775-156">创建一个类来提取的安全电子邮件键。</span><span class="sxs-lookup"><span data-stu-id="a5775-156">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="a5775-157">对于此示例中，创建*Services/AuthMessageSenderOptions.cs*:</span><span class="sxs-lookup"><span data-stu-id="a5775-157">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="a5775-158">配置 SendGrid 用户机密</span><span class="sxs-lookup"><span data-stu-id="a5775-158">Configure SendGrid user secrets</span></span>

<span data-ttu-id="a5775-159">添加一个唯一`<UserSecretsId>`值设为`<PropertyGroup>`项目文件的元素：</span><span class="sxs-lookup"><span data-stu-id="a5775-159">Add a unique `<UserSecretsId>` value to the `<PropertyGroup>` element of the project file:</span></span>

[!code-xml[](accconfirm/sample/WebPWrecover21/WebPWrecover.csproj?highlight=5)]

<span data-ttu-id="a5775-160">设置`SendGridUser`并`SendGridKey`与[机密管理器工具](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="a5775-160">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="a5775-161">例如：</span><span class="sxs-lookup"><span data-stu-id="a5775-161">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="a5775-162">在 Windows 中，机密管理器存储中的键/值对*secrets.json*文件中`%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>`目录。</span><span class="sxs-lookup"><span data-stu-id="a5775-162">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="a5775-163">内容*secrets.json*未加密文件。</span><span class="sxs-lookup"><span data-stu-id="a5775-163">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="a5775-164">*Secrets.json*文件如下所示 (`SendGridKey`删除值。)</span><span class="sxs-lookup"><span data-stu-id="a5775-164">The *secrets.json* file is shown below (the `SendGridKey` value has been removed.)</span></span>

 ```json
  {
    "SendGridUser": "RickAndMSFT",
    "SendGridKey": "<key removed>"
  }
  ```
 
<span data-ttu-id="a5775-165">有关详细信息，请参阅[选项模式](xref:fundamentals/configuration/options)并[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="a5775-165">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="a5775-166">安装 SendGrid</span><span class="sxs-lookup"><span data-stu-id="a5775-166">Install SendGrid</span></span>

<span data-ttu-id="a5775-167">本教程演示如何添加通过电子邮件通知[SendGrid](https://sendgrid.com/)，但是你可以发送电子邮件使用 SMTP 和其他机制。</span><span class="sxs-lookup"><span data-stu-id="a5775-167">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="a5775-168">安装`SendGrid`NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="a5775-168">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="a5775-169">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a5775-169">Visual Studio</span></span>](#tab/visual-studio) 

<span data-ttu-id="a5775-170">从包管理器控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="a5775-170">From the Package Manager Console, enter the following command:</span></span>

``` PMC
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="a5775-171">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a5775-171">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a5775-172">从控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="a5775-172">From the console, enter the following command:</span></span>

```cli
dotnet add package SendGrid
```

------

<span data-ttu-id="a5775-173">请参阅[免费开始使用 SendGrid](https://sendgrid.com/free/)注册一个免费的 SendGrid 帐户。</span><span class="sxs-lookup"><span data-stu-id="a5775-173">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>
### <a name="implement-iemailsender"></a><span data-ttu-id="a5775-174">实现 IEmailSender</span><span class="sxs-lookup"><span data-stu-id="a5775-174">Implement IEmailSender</span></span>

<span data-ttu-id="a5775-175">为实现`IEmailSender`，创建*Services/EmailSender.cs* ，类似于以下代码：</span><span class="sxs-lookup"><span data-stu-id="a5775-175">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="a5775-176">将启动以支持电子邮件配置</span><span class="sxs-lookup"><span data-stu-id="a5775-176">Configure startup to support email</span></span>

<span data-ttu-id="a5775-177">将以下代码添加到`ConfigureServices`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="a5775-177">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="a5775-178">添加`EmailSender`作为暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="a5775-178">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="a5775-179">注册`AuthMessageSenderOptions`配置实例。</span><span class="sxs-lookup"><span data-stu-id="a5775-179">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Startup.cs?name=snippet2&highlight=12-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="a5775-180">启用帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="a5775-180">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="a5775-181">该模板具有帐户确认和密码恢复的代码。</span><span class="sxs-lookup"><span data-stu-id="a5775-181">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="a5775-182">查找`OnPostAsync`中的方法*Areas/Identity/Pages/Account/Register.cshtml.cs*。</span><span class="sxs-lookup"><span data-stu-id="a5775-182">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="a5775-183">禁止新注册的用户将自动记录通过注释掉以下行：</span><span class="sxs-lookup"><span data-stu-id="a5775-183">Prevent newly registered users from being automatically logged on by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="a5775-184">完整的方法与更改突出显示的行所示：</span><span class="sxs-lookup"><span data-stu-id="a5775-184">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover21/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="a5775-185">注册、 确认电子邮件，和重置密码</span><span class="sxs-lookup"><span data-stu-id="a5775-185">Register, confirm email, and reset password</span></span>

<span data-ttu-id="a5775-186">运行 web 应用和测试的帐户确认和密码恢复流。</span><span class="sxs-lookup"><span data-stu-id="a5775-186">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="a5775-187">运行应用并注册一个新用户</span><span class="sxs-lookup"><span data-stu-id="a5775-187">Run the app and register a new user</span></span>

  ![Web 应用程序帐户注册视图](accconfirm/_static/loginaccconfirm1.png)

* <span data-ttu-id="a5775-189">检查你的帐户确认链接的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-189">Check your email for the account confirmation link.</span></span> <span data-ttu-id="a5775-190">请参阅[调试电子邮件](#debug)如果没有收到电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-190">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="a5775-191">单击链接以确认你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-191">Click the link to confirm your email.</span></span>
* <span data-ttu-id="a5775-192">使用你的电子邮件和密码登录。</span><span class="sxs-lookup"><span data-stu-id="a5775-192">Sign in with your email and password.</span></span>
* <span data-ttu-id="a5775-193">注销。</span><span class="sxs-lookup"><span data-stu-id="a5775-193">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="a5775-194">查看管理页</span><span class="sxs-lookup"><span data-stu-id="a5775-194">View the manage page</span></span>

<span data-ttu-id="a5775-195">在浏览器中选择你的用户名：![用户名的浏览器窗口](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="a5775-195">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="a5775-196">您可能需要展开导航栏以查看用户名称。</span><span class="sxs-lookup"><span data-stu-id="a5775-196">You might need to expand the navbar to see user name.</span></span>

![navbar](accconfirm/_static/x.png)

<span data-ttu-id="a5775-198">管理页显示与**配置文件**选定的选项卡。</span><span class="sxs-lookup"><span data-stu-id="a5775-198">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="a5775-199">**电子邮件**显示已确认一个复选框，表明该电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-199">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="a5775-200">测试密码重置</span><span class="sxs-lookup"><span data-stu-id="a5775-200">Test password reset</span></span>

* <span data-ttu-id="a5775-201">如果在登录，请选择**注销**。</span><span class="sxs-lookup"><span data-stu-id="a5775-201">If you're logged in, select **Logout**.</span></span>
* <span data-ttu-id="a5775-202">选择**登录**链接，然后选择**忘记了密码？** 链接。</span><span class="sxs-lookup"><span data-stu-id="a5775-202">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="a5775-203">输入用于注册该帐户的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-203">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="a5775-204">发送一封电子邮件包含用于重置密码的链接。</span><span class="sxs-lookup"><span data-stu-id="a5775-204">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="a5775-205">检查你的电子邮件，并单击链接以重置密码。</span><span class="sxs-lookup"><span data-stu-id="a5775-205">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="a5775-206">已成功重置密码后，你可以使用你的电子邮件和新密码登录。</span><span class="sxs-lookup"><span data-stu-id="a5775-206">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="a5775-207">调试电子邮件</span><span class="sxs-lookup"><span data-stu-id="a5775-207">Debug email</span></span>

<span data-ttu-id="a5775-208">如果无法获取电子邮件工作：</span><span class="sxs-lookup"><span data-stu-id="a5775-208">If you can't get email working:</span></span>

* <span data-ttu-id="a5775-209">中设置断点`EmailSender.Execute`若要验证`SendGridClient.SendEmailAsync`调用。</span><span class="sxs-lookup"><span data-stu-id="a5775-209">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="a5775-210">创建[控制台应用以发送电子邮件](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)使用类似的代码，到`EmailSender.Execute`。</span><span class="sxs-lookup"><span data-stu-id="a5775-210">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="a5775-211">审阅[电子邮件活动](https://sendgrid.com/docs/User_Guide/email_activity.html)页。</span><span class="sxs-lookup"><span data-stu-id="a5775-211">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="a5775-212">请检查垃圾邮件文件夹。</span><span class="sxs-lookup"><span data-stu-id="a5775-212">Check your spam folder.</span></span>
* <span data-ttu-id="a5775-213">请尝试其他电子邮件提供程序 （Microsoft、 Yahoo、 Gmail 等） 上的另一个电子邮件别名</span><span class="sxs-lookup"><span data-stu-id="a5775-213">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="a5775-214">尝试发送到不同的电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="a5775-214">Try sending to different email accounts.</span></span>

<span data-ttu-id="a5775-215">**最佳安全方案**设置为**不**中使用生产机密中测试和开发。</span><span class="sxs-lookup"><span data-stu-id="a5775-215">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="a5775-216">如果将应用发布到 Azure，可以为 Azure Web 应用门户中的应用程序设置来设置 SendGrid 机密。</span><span class="sxs-lookup"><span data-stu-id="a5775-216">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="a5775-217">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="a5775-217">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="a5775-218">合并的社交和本地登录帐户</span><span class="sxs-lookup"><span data-stu-id="a5775-218">Combine social and local login accounts</span></span>

<span data-ttu-id="a5775-219">若要完成本部分中，必须先启用外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="a5775-219">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="a5775-220">请参阅[Facebook、 Google、 和外部提供程序身份验证](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="a5775-220">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="a5775-221">您可以通过单击电子邮件链接组合本地和社交帐户。</span><span class="sxs-lookup"><span data-stu-id="a5775-221">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="a5775-222">按以下顺序"RickAndMSFT@gmail.com"首次创建为本地登录名; 但是，您可以创建帐户为社交登录名，然后添加本地登录名。</span><span class="sxs-lookup"><span data-stu-id="a5775-222">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web 应用程序：RickAndMSFT@gmail.com用户通过身份验证](accconfirm/_static/rick.png)

<span data-ttu-id="a5775-224">单击**管理**链接。</span><span class="sxs-lookup"><span data-stu-id="a5775-224">Click on the **Manage** link.</span></span> <span data-ttu-id="a5775-225">请注意与此帐户关联的 0 外部 （社交登录名）。</span><span class="sxs-lookup"><span data-stu-id="a5775-225">Note the 0 external (social logins) associated with this account.</span></span>

![管理视图](accconfirm/_static/manage.png)

<span data-ttu-id="a5775-227">单击链接到另一个登录名服务，接受应用程序请求。</span><span class="sxs-lookup"><span data-stu-id="a5775-227">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="a5775-228">下图中，在 Facebook 是外部身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="a5775-228">In the following image, Facebook is the external authentication provider:</span></span>

![管理你的外部登录名视图，其中列出 Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="a5775-230">已合并两个帐户。</span><span class="sxs-lookup"><span data-stu-id="a5775-230">The two accounts have been combined.</span></span> <span data-ttu-id="a5775-231">你将能够使用任一帐户登录。</span><span class="sxs-lookup"><span data-stu-id="a5775-231">You are able to sign in with either account.</span></span> <span data-ttu-id="a5775-232">您可能希望用户其社交登录身份验证服务发生故障，或已为其社交帐户无法访问更有可能的情况下添加本地帐户。</span><span class="sxs-lookup"><span data-stu-id="a5775-232">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="a5775-233">后一个站点中有用户启用帐户确认</span><span class="sxs-lookup"><span data-stu-id="a5775-233">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="a5775-234">与用户启用帐户确认站点上的阻止所有现有用户。</span><span class="sxs-lookup"><span data-stu-id="a5775-234">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="a5775-235">现有用户被锁定，因为不确认其帐户。</span><span class="sxs-lookup"><span data-stu-id="a5775-235">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="a5775-236">若要解决现有用户锁定，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="a5775-236">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="a5775-237">更新要将标记为正在确认的所有现有用户的数据库。</span><span class="sxs-lookup"><span data-stu-id="a5775-237">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="a5775-238">确认现有用户。</span><span class="sxs-lookup"><span data-stu-id="a5775-238">Confirm existing users.</span></span> <span data-ttu-id="a5775-239">例如，批的发送确认链接的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a5775-239">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
