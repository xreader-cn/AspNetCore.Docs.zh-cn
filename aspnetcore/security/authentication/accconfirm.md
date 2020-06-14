---
title: ASP.NET Core 中的帐户确认和密码恢复
author: rick-anderson
description: 了解如何使用电子邮件确认和密码重置构建 ASP.NET Core 应用。
ms.author: riande
ms.date: 03/11/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/accconfirm
ms.openlocfilehash: 8d4488b3953a8c87033d3a092b656409a0c6a52d
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "84724362"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="825dc-103">ASP.NET Core 中的帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="825dc-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="825dc-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Ponant](https://github.com/Ponant)和[Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="825dc-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="825dc-105">本教程介绍如何使用电子邮件确认和密码重置构建 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="825dc-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="825dc-106">本教程**不**是开始主题。</span><span class="sxs-lookup"><span data-stu-id="825dc-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="825dc-107">你应该熟悉：</span><span class="sxs-lookup"><span data-stu-id="825dc-107">You should be familiar with:</span></span>

* [<span data-ttu-id="825dc-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="825dc-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="825dc-109">身份验证</span><span class="sxs-lookup"><span data-stu-id="825dc-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="825dc-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="825dc-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="825dc-111">有关 ASP.NET Core 1.1 版本，请参阅[此 PDF 文件](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)。</span><span class="sxs-lookup"><span data-stu-id="825dc-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="825dc-112">先决条件</span><span class="sxs-lookup"><span data-stu-id="825dc-112">Prerequisites</span></span>

[<span data-ttu-id="825dc-113">.NET Core 3.0 SDK 或更高版本</span><span class="sxs-lookup"><span data-stu-id="825dc-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="825dc-114">创建和测试使用身份验证的 web 应用</span><span class="sxs-lookup"><span data-stu-id="825dc-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="825dc-115">运行以下命令，创建具有身份验证的 web 应用。</span><span class="sxs-lookup"><span data-stu-id="825dc-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="825dc-116">运行应用，选择 "**注册**" 链接，然后注册用户。</span><span class="sxs-lookup"><span data-stu-id="825dc-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="825dc-117">注册后，你会被重定向到 "目标" `/Identity/Account/RegisterConfirmation` 页，其中包含用于模拟电子邮件确认的链接：</span><span class="sxs-lookup"><span data-stu-id="825dc-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="825dc-118">选择 `Click here to confirm your account` 链接。</span><span class="sxs-lookup"><span data-stu-id="825dc-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="825dc-119">选择 "**登录**" 链接，并以相同的凭据登录。</span><span class="sxs-lookup"><span data-stu-id="825dc-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="825dc-120">选择将 `Hello YourEmail@provider.com!` 您重定向到页面的链接 `/Identity/Account/Manage/PersonalData` 。</span><span class="sxs-lookup"><span data-stu-id="825dc-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="825dc-121">选择左侧的 "**个人数据**" 选项卡，然后选择 "**删除**"。</span><span class="sxs-lookup"><span data-stu-id="825dc-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="825dc-122">配置电子邮件提供程序</span><span class="sxs-lookup"><span data-stu-id="825dc-122">Configure an email provider</span></span>

<span data-ttu-id="825dc-123">在本教程中，使用[SendGrid](https://sendgrid.com)发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="825dc-124">需要使用 SendGrid 帐户和密钥来发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="825dc-125">您可以使用其他电子邮件提供程序。</span><span class="sxs-lookup"><span data-stu-id="825dc-125">You can use other email providers.</span></span> <span data-ttu-id="825dc-126">建议使用 SendGrid 或其他电子邮件服务发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="825dc-127">SMTP 难于保护和正确设置。</span><span class="sxs-lookup"><span data-stu-id="825dc-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="825dc-128">SendGrid 帐户可能需要[添加发送方](https://sendgrid.com/docs/ui/sending-email/senders/)。</span><span class="sxs-lookup"><span data-stu-id="825dc-128">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="825dc-129">创建一个类以获取安全电子邮件密钥。</span><span class="sxs-lookup"><span data-stu-id="825dc-129">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="825dc-130">对于本示例，请创建*服务/AuthMessageSenderOptions*：</span><span class="sxs-lookup"><span data-stu-id="825dc-130">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="825dc-131">配置 SendGrid 用户机密</span><span class="sxs-lookup"><span data-stu-id="825dc-131">Configure SendGrid user secrets</span></span>

<span data-ttu-id="825dc-132">`SendGridUser` `SendGridKey` 用[机密管理器工具](xref:security/app-secrets)设置和。</span><span class="sxs-lookup"><span data-stu-id="825dc-132">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="825dc-133">例如：</span><span class="sxs-lookup"><span data-stu-id="825dc-133">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="825dc-134">在 Windows 上，机密管理器将密钥/值对存储在目录中的文件*secrets.js上* `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 。</span><span class="sxs-lookup"><span data-stu-id="825dc-134">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="825dc-135">不会对文件*中secrets.js*的内容进行加密。</span><span class="sxs-lookup"><span data-stu-id="825dc-135">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="825dc-136">以下标记显示了文件*上的secrets.js* 。</span><span class="sxs-lookup"><span data-stu-id="825dc-136">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="825dc-137">已 `SendGridKey` 删除该值。</span><span class="sxs-lookup"><span data-stu-id="825dc-137">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="825dc-138">有关详细信息，请参阅[Options 模式](xref:fundamentals/configuration/options)和[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="825dc-138">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="825dc-139">安装 SendGrid</span><span class="sxs-lookup"><span data-stu-id="825dc-139">Install SendGrid</span></span>

<span data-ttu-id="825dc-140">本教程介绍如何通过[SendGrid](https://sendgrid.com/)添加电子邮件通知，但你可以使用 SMTP 和其他机制发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-140">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="825dc-141">安装 `SendGrid` NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="825dc-141">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="825dc-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="825dc-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="825dc-143">在 "包管理器控制台" 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="825dc-143">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="825dc-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="825dc-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="825dc-145">在控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="825dc-145">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="825dc-146">请参阅[SendGrid 的入门免费](https://sendgrid.com/free/)版，注册免费 SendGrid 帐户。</span><span class="sxs-lookup"><span data-stu-id="825dc-146">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="825dc-147">实现 IEmailSender</span><span class="sxs-lookup"><span data-stu-id="825dc-147">Implement IEmailSender</span></span>

<span data-ttu-id="825dc-148">若要实现 `IEmailSender` ，请创建具有类似于下面的代码的*服务/EmailSender* ：</span><span class="sxs-lookup"><span data-stu-id="825dc-148">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="825dc-149">配置启动以支持电子邮件</span><span class="sxs-lookup"><span data-stu-id="825dc-149">Configure startup to support email</span></span>

<span data-ttu-id="825dc-150">将以下代码添加到 `ConfigureServices` *Startup.cs*文件中的方法：</span><span class="sxs-lookup"><span data-stu-id="825dc-150">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="825dc-151">添加 `EmailSender` 为暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="825dc-151">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="825dc-152">注册 `AuthMessageSenderOptions` 配置实例。</span><span class="sxs-lookup"><span data-stu-id="825dc-152">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="825dc-153">注册、确认电子邮件并重置密码</span><span class="sxs-lookup"><span data-stu-id="825dc-153">Register, confirm email, and reset password</span></span>

<span data-ttu-id="825dc-154">运行 web 应用，并测试帐户确认和密码恢复流。</span><span class="sxs-lookup"><span data-stu-id="825dc-154">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="825dc-155">运行应用并注册新用户</span><span class="sxs-lookup"><span data-stu-id="825dc-155">Run the app and register a new user</span></span>
* <span data-ttu-id="825dc-156">检查电子邮件中的 "帐户确认" 链接。</span><span class="sxs-lookup"><span data-stu-id="825dc-156">Check your email for the account confirmation link.</span></span> <span data-ttu-id="825dc-157">如果没有收到电子邮件，请参阅[调试电子邮件](#debug)。</span><span class="sxs-lookup"><span data-stu-id="825dc-157">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="825dc-158">单击链接以确认你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-158">Click the link to confirm your email.</span></span>
* <span data-ttu-id="825dc-159">用电子邮件和密码登录。</span><span class="sxs-lookup"><span data-stu-id="825dc-159">Sign in with your email and password.</span></span>
* <span data-ttu-id="825dc-160">注销。</span><span class="sxs-lookup"><span data-stu-id="825dc-160">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="825dc-161">测试密码重置</span><span class="sxs-lookup"><span data-stu-id="825dc-161">Test password reset</span></span>

* <span data-ttu-id="825dc-162">如果已登录，请选择 "**注销**"。</span><span class="sxs-lookup"><span data-stu-id="825dc-162">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="825dc-163">选择 "**登录**" 链接，然后选择 "**忘记了密码？"** 链接。</span><span class="sxs-lookup"><span data-stu-id="825dc-163">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="825dc-164">输入用于注册该帐户的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-164">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="825dc-165">发送了一封电子邮件，其中包含用于重置密码的链接。</span><span class="sxs-lookup"><span data-stu-id="825dc-165">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="825dc-166">检查你的电子邮件，然后单击链接以重置你的密码。</span><span class="sxs-lookup"><span data-stu-id="825dc-166">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="825dc-167">密码重置成功后，可以用电子邮件和新密码登录。</span><span class="sxs-lookup"><span data-stu-id="825dc-167">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="825dc-168">更改电子邮件和活动超时</span><span class="sxs-lookup"><span data-stu-id="825dc-168">Change email and activity timeout</span></span>

<span data-ttu-id="825dc-169">默认的非活动超时为14天。</span><span class="sxs-lookup"><span data-stu-id="825dc-169">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="825dc-170">下面的代码将非活动超时设置为5天：</span><span class="sxs-lookup"><span data-stu-id="825dc-170">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="825dc-171">更改所有数据保护令牌 lifespans</span><span class="sxs-lookup"><span data-stu-id="825dc-171">Change all data protection token lifespans</span></span>

<span data-ttu-id="825dc-172">以下代码将所有数据保护令牌超时期限更改为3小时：</span><span class="sxs-lookup"><span data-stu-id="825dc-172">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="825dc-173">内置 Identity 用户令牌（请参阅[AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ）具有[一天的超时时间](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="825dc-173">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="825dc-174">更改电子邮件令牌的生命周期</span><span class="sxs-lookup"><span data-stu-id="825dc-174">Change the email token lifespan</span></span>

<span data-ttu-id="825dc-175">[ Identity 用户令牌](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)的默认令牌生存期为[1 天](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="825dc-175">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="825dc-176">本部分介绍如何更改电子邮件令牌的生命周期。</span><span class="sxs-lookup"><span data-stu-id="825dc-176">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="825dc-177">添加自定义[DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)和 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> ：</span><span class="sxs-lookup"><span data-stu-id="825dc-177">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="825dc-178">将自定义提供程序添加到服务容器：</span><span class="sxs-lookup"><span data-stu-id="825dc-178">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="825dc-179">重新发送电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="825dc-179">Resend email confirmation</span></span>

<span data-ttu-id="825dc-180">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/5410)。</span><span class="sxs-lookup"><span data-stu-id="825dc-180">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="825dc-181">调试电子邮件</span><span class="sxs-lookup"><span data-stu-id="825dc-181">Debug email</span></span>

<span data-ttu-id="825dc-182">如果无法使用电子邮件：</span><span class="sxs-lookup"><span data-stu-id="825dc-182">If you can't get email working:</span></span>

* <span data-ttu-id="825dc-183">在中设置一个断点 `EmailSender.Execute` ，以验证 `SendGridClient.SendEmailAsync` 调用。</span><span class="sxs-lookup"><span data-stu-id="825dc-183">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="825dc-184">创建一个[控制台应用，用于](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)使用类似的代码向发送电子邮件 `EmailSender.Execute` 。</span><span class="sxs-lookup"><span data-stu-id="825dc-184">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="825dc-185">查看[电子邮件活动](https://sendgrid.com/docs/User_Guide/email_activity.html)页。</span><span class="sxs-lookup"><span data-stu-id="825dc-185">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="825dc-186">检查垃圾邮件文件夹。</span><span class="sxs-lookup"><span data-stu-id="825dc-186">Check your spam folder.</span></span>
* <span data-ttu-id="825dc-187">尝试使用其他电子邮件提供商（Microsoft、Yahoo、Gmail 等）中的另一个电子邮件别名</span><span class="sxs-lookup"><span data-stu-id="825dc-187">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="825dc-188">尝试发送到不同的电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="825dc-188">Try sending to different email accounts.</span></span>

<span data-ttu-id="825dc-189">**最佳安全做法**是**不**在测试和开发中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="825dc-189">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="825dc-190">如果将应用发布到 Azure，请在 Azure Web 应用门户中将 "SendGrid 机密" 设置为 "应用程序设置"。</span><span class="sxs-lookup"><span data-stu-id="825dc-190">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="825dc-191">配置系统设置为从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="825dc-191">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="825dc-192">合并社会和本地登录帐户</span><span class="sxs-lookup"><span data-stu-id="825dc-192">Combine social and local login accounts</span></span>

<span data-ttu-id="825dc-193">若要完成本部分，必须首先启用外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="825dc-193">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="825dc-194">请参阅[Facebook、Google 和外部提供程序身份验证](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="825dc-194">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="825dc-195">可以通过单击电子邮件链接来合并本地帐户和社交帐户。</span><span class="sxs-lookup"><span data-stu-id="825dc-195">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="825dc-196">按照以下顺序，" RickAndMSFT@gmail.com " 首先创建为本地登录名; 但是，你可以先将该帐户创建为社交登录名，然后添加本地登录名。</span><span class="sxs-lookup"><span data-stu-id="825dc-196">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web 应用程序： RickAndMSFT@gmail.com 用户已进行身份验证](accconfirm/_static/rick.png)

<span data-ttu-id="825dc-198">单击 "**管理**" 链接。</span><span class="sxs-lookup"><span data-stu-id="825dc-198">Click on the **Manage** link.</span></span> <span data-ttu-id="825dc-199">请注意与此帐户关联的0个外部（社交登录）。</span><span class="sxs-lookup"><span data-stu-id="825dc-199">Note the 0 external (social logins) associated with this account.</span></span>

![管理视图](accconfirm/_static/manage.png)

<span data-ttu-id="825dc-201">单击指向另一登录服务的链接，并接受应用请求。</span><span class="sxs-lookup"><span data-stu-id="825dc-201">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="825dc-202">在下图中，Facebook 是外部身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="825dc-202">In the following image, Facebook is the external authentication provider:</span></span>

![管理你的外部登录名视图列表 Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="825dc-204">这两个帐户已组合在一起。</span><span class="sxs-lookup"><span data-stu-id="825dc-204">The two accounts have been combined.</span></span> <span data-ttu-id="825dc-205">你可以用任一帐户登录。</span><span class="sxs-lookup"><span data-stu-id="825dc-205">You are able to sign in with either account.</span></span> <span data-ttu-id="825dc-206">你可能希望用户在社交登录身份验证服务关闭时添加本地帐户，或者更可能的情况是他们失去了社交帐户的访问权限。</span><span class="sxs-lookup"><span data-stu-id="825dc-206">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="825dc-207">在站点包含用户后启用帐户确认</span><span class="sxs-lookup"><span data-stu-id="825dc-207">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="825dc-208">在具有用户的站点上启用帐户确认会锁定所有现有用户。</span><span class="sxs-lookup"><span data-stu-id="825dc-208">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="825dc-209">现有用户被锁定，因为其帐户未得到确认。</span><span class="sxs-lookup"><span data-stu-id="825dc-209">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="825dc-210">若要解决现有用户锁定，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="825dc-210">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="825dc-211">更新数据库，将所有现有用户标记为已确认。</span><span class="sxs-lookup"><span data-stu-id="825dc-211">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="825dc-212">确认现有用户。</span><span class="sxs-lookup"><span data-stu-id="825dc-212">Confirm existing users.</span></span> <span data-ttu-id="825dc-213">例如，批处理-发送包含确认链接的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-213">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="825dc-214">先决条件</span><span class="sxs-lookup"><span data-stu-id="825dc-214">Prerequisites</span></span>

[<span data-ttu-id="825dc-215">.NET Core 2.2 SDK 或更高版本</span><span class="sxs-lookup"><span data-stu-id="825dc-215">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="825dc-216">创建 web 应用和基架Identity</span><span class="sxs-lookup"><span data-stu-id="825dc-216">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="825dc-217">运行以下命令，创建具有身份验证的 web 应用。</span><span class="sxs-lookup"><span data-stu-id="825dc-217">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator identity -dc WebPWrecover.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.ConfirmEmail"
dotnet ef database drop -f
dotnet ef database update
dotnet run

```

## <a name="test-new-user-registration"></a><span data-ttu-id="825dc-218">测试新用户注册</span><span class="sxs-lookup"><span data-stu-id="825dc-218">Test new user registration</span></span>

<span data-ttu-id="825dc-219">运行应用，选择 "**注册**" 链接，然后注册用户。</span><span class="sxs-lookup"><span data-stu-id="825dc-219">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="825dc-220">此时，电子邮件的唯一验证是具有 [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="825dc-220">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="825dc-221">提交注册后，将登录到应用。</span><span class="sxs-lookup"><span data-stu-id="825dc-221">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="825dc-222">在本教程的后面部分，将更新代码，以便新用户在验证其电子邮件之前无法登录。</span><span class="sxs-lookup"><span data-stu-id="825dc-222">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="825dc-223">请注意，表的 `EmailConfirmed` 字段为 `False` 。</span><span class="sxs-lookup"><span data-stu-id="825dc-223">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="825dc-224">当应用发送确认电子邮件时，可能需要在下一步中再次使用此电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-224">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="825dc-225">右键单击该行，然后选择 "**删除**"。</span><span class="sxs-lookup"><span data-stu-id="825dc-225">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="825dc-226">删除电子邮件别名可以简化以下步骤。</span><span class="sxs-lookup"><span data-stu-id="825dc-226">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="825dc-227">需要确认电子邮件</span><span class="sxs-lookup"><span data-stu-id="825dc-227">Require email confirmation</span></span>

<span data-ttu-id="825dc-228">最佳做法是确认新用户注册的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-228">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="825dc-229">电子邮件确认有助于验证他们是否未模拟其他人（即，他们未注册其他人的电子邮件）。</span><span class="sxs-lookup"><span data-stu-id="825dc-229">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="825dc-230">假设你有讨论论坛，并且想要阻止 " yli@example.com " 注册为 " nolivetto@contoso.com "。</span><span class="sxs-lookup"><span data-stu-id="825dc-230">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="825dc-231">如果未确认电子邮件，" nolivetto@contoso.com " 可能会从你的应用收到不需要的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-231">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="825dc-232">假设用户意外注册为 " ylo@example.com "，但未注意到 "yli" 的拼写错误。</span><span class="sxs-lookup"><span data-stu-id="825dc-232">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="825dc-233">它们不能使用密码恢复，因为该应用没有正确的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-233">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="825dc-234">电子邮件确认为 bot 提供有限的保护。</span><span class="sxs-lookup"><span data-stu-id="825dc-234">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="825dc-235">电子邮件确认不会为具有多个电子邮件帐户的恶意用户提供保护。</span><span class="sxs-lookup"><span data-stu-id="825dc-235">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="825dc-236">通常，在用户确认电子邮件之前，会阻止新用户将任何数据发布到您的网站。</span><span class="sxs-lookup"><span data-stu-id="825dc-236">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="825dc-237">更新 `Startup.ConfigureServices` 以要求确认电子邮件：</span><span class="sxs-lookup"><span data-stu-id="825dc-237">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="825dc-238">`config.SignIn.RequireConfirmedEmail = true;`阻止注册的用户登录，直到其电子邮件得到确认。</span><span class="sxs-lookup"><span data-stu-id="825dc-238">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="825dc-239">配置电子邮件提供程序</span><span class="sxs-lookup"><span data-stu-id="825dc-239">Configure email provider</span></span>

<span data-ttu-id="825dc-240">在本教程中，使用[SendGrid](https://sendgrid.com)发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-240">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="825dc-241">需要使用 SendGrid 帐户和密钥来发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-241">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="825dc-242">您可以使用其他电子邮件提供程序。</span><span class="sxs-lookup"><span data-stu-id="825dc-242">You can use other email providers.</span></span> <span data-ttu-id="825dc-243">ASP.NET Core 1.x 包括 `System.Net.Mail` ，这允许你从应用发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-243">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="825dc-244">建议使用 SendGrid 或其他电子邮件服务发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-244">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="825dc-245">SMTP 难于保护和正确设置。</span><span class="sxs-lookup"><span data-stu-id="825dc-245">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="825dc-246">创建一个类以获取安全电子邮件密钥。</span><span class="sxs-lookup"><span data-stu-id="825dc-246">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="825dc-247">对于本示例，请创建*服务/AuthMessageSenderOptions*：</span><span class="sxs-lookup"><span data-stu-id="825dc-247">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="825dc-248">配置 SendGrid 用户机密</span><span class="sxs-lookup"><span data-stu-id="825dc-248">Configure SendGrid user secrets</span></span>

<span data-ttu-id="825dc-249">`SendGridUser` `SendGridKey` 用[机密管理器工具](xref:security/app-secrets)设置和。</span><span class="sxs-lookup"><span data-stu-id="825dc-249">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="825dc-250">例如：</span><span class="sxs-lookup"><span data-stu-id="825dc-250">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="825dc-251">在 Windows 上，机密管理器将密钥/值对存储在目录中的文件*secrets.js上* `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 。</span><span class="sxs-lookup"><span data-stu-id="825dc-251">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="825dc-252">不会对文件*中secrets.js*的内容进行加密。</span><span class="sxs-lookup"><span data-stu-id="825dc-252">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="825dc-253">以下标记显示了文件*上的secrets.js* 。</span><span class="sxs-lookup"><span data-stu-id="825dc-253">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="825dc-254">已 `SendGridKey` 删除该值。</span><span class="sxs-lookup"><span data-stu-id="825dc-254">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="825dc-255">有关详细信息，请参阅[Options 模式](xref:fundamentals/configuration/options)和[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="825dc-255">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="825dc-256">安装 SendGrid</span><span class="sxs-lookup"><span data-stu-id="825dc-256">Install SendGrid</span></span>

<span data-ttu-id="825dc-257">本教程介绍如何通过[SendGrid](https://sendgrid.com/)添加电子邮件通知，但你可以使用 SMTP 和其他机制发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-257">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="825dc-258">安装 `SendGrid` NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="825dc-258">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="825dc-259">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="825dc-259">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="825dc-260">在 "包管理器控制台" 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="825dc-260">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="825dc-261">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="825dc-261">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="825dc-262">在控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="825dc-262">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="825dc-263">请参阅[SendGrid 的入门免费](https://sendgrid.com/free/)版，注册免费 SendGrid 帐户。</span><span class="sxs-lookup"><span data-stu-id="825dc-263">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="825dc-264">实现 IEmailSender</span><span class="sxs-lookup"><span data-stu-id="825dc-264">Implement IEmailSender</span></span>

<span data-ttu-id="825dc-265">若要实现 `IEmailSender` ，请创建具有类似于下面的代码的*服务/EmailSender* ：</span><span class="sxs-lookup"><span data-stu-id="825dc-265">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="825dc-266">配置启动以支持电子邮件</span><span class="sxs-lookup"><span data-stu-id="825dc-266">Configure startup to support email</span></span>

<span data-ttu-id="825dc-267">将以下代码添加到 `ConfigureServices` *Startup.cs*文件中的方法：</span><span class="sxs-lookup"><span data-stu-id="825dc-267">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="825dc-268">添加 `EmailSender` 为暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="825dc-268">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="825dc-269">注册 `AuthMessageSenderOptions` 配置实例。</span><span class="sxs-lookup"><span data-stu-id="825dc-269">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="825dc-270">启用帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="825dc-270">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="825dc-271">该模板包含用于帐户确认和密码恢复的代码。</span><span class="sxs-lookup"><span data-stu-id="825dc-271">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="825dc-272">`OnPostAsync`在*区域/ Identity /Pages/Account/Register.cshtml.cs*中查找方法。</span><span class="sxs-lookup"><span data-stu-id="825dc-272">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="825dc-273">通过注释掉以下行，阻止新注册的用户自动登录：</span><span class="sxs-lookup"><span data-stu-id="825dc-273">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="825dc-274">将显示完整的方法，其中突出显示了已更改的行：</span><span class="sxs-lookup"><span data-stu-id="825dc-274">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="825dc-275">注册、确认电子邮件并重置密码</span><span class="sxs-lookup"><span data-stu-id="825dc-275">Register, confirm email, and reset password</span></span>

<span data-ttu-id="825dc-276">运行 web 应用，并测试帐户确认和密码恢复流。</span><span class="sxs-lookup"><span data-stu-id="825dc-276">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="825dc-277">运行应用并注册新用户</span><span class="sxs-lookup"><span data-stu-id="825dc-277">Run the app and register a new user</span></span>
* <span data-ttu-id="825dc-278">检查电子邮件中的 "帐户确认" 链接。</span><span class="sxs-lookup"><span data-stu-id="825dc-278">Check your email for the account confirmation link.</span></span> <span data-ttu-id="825dc-279">如果没有收到电子邮件，请参阅[调试电子邮件](#debug)。</span><span class="sxs-lookup"><span data-stu-id="825dc-279">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="825dc-280">单击链接以确认你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-280">Click the link to confirm your email.</span></span>
* <span data-ttu-id="825dc-281">用电子邮件和密码登录。</span><span class="sxs-lookup"><span data-stu-id="825dc-281">Sign in with your email and password.</span></span>
* <span data-ttu-id="825dc-282">注销。</span><span class="sxs-lookup"><span data-stu-id="825dc-282">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="825dc-283">查看 "管理" 页</span><span class="sxs-lookup"><span data-stu-id="825dc-283">View the manage page</span></span>

<span data-ttu-id="825dc-284">在浏览器中选择你的用户名，并在浏览器窗口中选择用户名 ![](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="825dc-284">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="825dc-285">将显示 "管理" 页，并选中 "**配置文件**" 选项卡。</span><span class="sxs-lookup"><span data-stu-id="825dc-285">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="825dc-286">该**电子邮件**将显示一个复选框，指示已确认电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-286">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="825dc-287">测试密码重置</span><span class="sxs-lookup"><span data-stu-id="825dc-287">Test password reset</span></span>

* <span data-ttu-id="825dc-288">如果已登录，请选择 "**注销**"。</span><span class="sxs-lookup"><span data-stu-id="825dc-288">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="825dc-289">选择 "**登录**" 链接，然后选择 "**忘记了密码？"** 链接。</span><span class="sxs-lookup"><span data-stu-id="825dc-289">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="825dc-290">输入用于注册该帐户的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-290">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="825dc-291">发送了一封电子邮件，其中包含用于重置密码的链接。</span><span class="sxs-lookup"><span data-stu-id="825dc-291">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="825dc-292">检查你的电子邮件，然后单击链接以重置你的密码。</span><span class="sxs-lookup"><span data-stu-id="825dc-292">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="825dc-293">密码重置成功后，可以用电子邮件和新密码登录。</span><span class="sxs-lookup"><span data-stu-id="825dc-293">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="825dc-294">更改电子邮件和活动超时</span><span class="sxs-lookup"><span data-stu-id="825dc-294">Change email and activity timeout</span></span>

<span data-ttu-id="825dc-295">默认的非活动超时为14天。</span><span class="sxs-lookup"><span data-stu-id="825dc-295">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="825dc-296">下面的代码将非活动超时设置为5天：</span><span class="sxs-lookup"><span data-stu-id="825dc-296">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="825dc-297">更改所有数据保护令牌 lifespans</span><span class="sxs-lookup"><span data-stu-id="825dc-297">Change all data protection token lifespans</span></span>

<span data-ttu-id="825dc-298">以下代码将所有数据保护令牌超时期限更改为3小时：</span><span class="sxs-lookup"><span data-stu-id="825dc-298">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="825dc-299">内置 Identity 用户令牌（请参阅[AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ）具有[一天的超时时间](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="825dc-299">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="825dc-300">更改电子邮件令牌的生命周期</span><span class="sxs-lookup"><span data-stu-id="825dc-300">Change the email token lifespan</span></span>

<span data-ttu-id="825dc-301">[ Identity 用户令牌](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)的默认令牌生存期为[1 天](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="825dc-301">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="825dc-302">本部分介绍如何更改电子邮件令牌的生命周期。</span><span class="sxs-lookup"><span data-stu-id="825dc-302">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="825dc-303">添加自定义[DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)和 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> ：</span><span class="sxs-lookup"><span data-stu-id="825dc-303">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="825dc-304">将自定义提供程序添加到服务容器：</span><span class="sxs-lookup"><span data-stu-id="825dc-304">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="825dc-305">重新发送电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="825dc-305">Resend email confirmation</span></span>

<span data-ttu-id="825dc-306">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/5410)。</span><span class="sxs-lookup"><span data-stu-id="825dc-306">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="825dc-307">调试电子邮件</span><span class="sxs-lookup"><span data-stu-id="825dc-307">Debug email</span></span>

<span data-ttu-id="825dc-308">如果无法使用电子邮件：</span><span class="sxs-lookup"><span data-stu-id="825dc-308">If you can't get email working:</span></span>

* <span data-ttu-id="825dc-309">在中设置一个断点 `EmailSender.Execute` ，以验证 `SendGridClient.SendEmailAsync` 调用。</span><span class="sxs-lookup"><span data-stu-id="825dc-309">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="825dc-310">创建一个[控制台应用，用于](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)使用类似的代码向发送电子邮件 `EmailSender.Execute` 。</span><span class="sxs-lookup"><span data-stu-id="825dc-310">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="825dc-311">查看[电子邮件活动](https://sendgrid.com/docs/User_Guide/email_activity.html)页。</span><span class="sxs-lookup"><span data-stu-id="825dc-311">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="825dc-312">检查垃圾邮件文件夹。</span><span class="sxs-lookup"><span data-stu-id="825dc-312">Check your spam folder.</span></span>
* <span data-ttu-id="825dc-313">尝试使用其他电子邮件提供商（Microsoft、Yahoo、Gmail 等）中的另一个电子邮件别名</span><span class="sxs-lookup"><span data-stu-id="825dc-313">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="825dc-314">尝试发送到不同的电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="825dc-314">Try sending to different email accounts.</span></span>

<span data-ttu-id="825dc-315">**最佳安全做法**是**不**在测试和开发中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="825dc-315">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="825dc-316">如果将应用发布到 Azure，则可以在 Azure Web 应用门户中将 SendGrid 机密设置为应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="825dc-316">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="825dc-317">配置系统设置为从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="825dc-317">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="825dc-318">合并社会和本地登录帐户</span><span class="sxs-lookup"><span data-stu-id="825dc-318">Combine social and local login accounts</span></span>

<span data-ttu-id="825dc-319">若要完成本部分，必须首先启用外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="825dc-319">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="825dc-320">请参阅[Facebook、Google 和外部提供程序身份验证](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="825dc-320">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="825dc-321">可以通过单击电子邮件链接来合并本地帐户和社交帐户。</span><span class="sxs-lookup"><span data-stu-id="825dc-321">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="825dc-322">按照以下顺序，" RickAndMSFT@gmail.com " 首先创建为本地登录名; 但是，你可以先将该帐户创建为社交登录名，然后添加本地登录名。</span><span class="sxs-lookup"><span data-stu-id="825dc-322">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web 应用程序： RickAndMSFT@gmail.com 用户已进行身份验证](accconfirm/_static/rick.png)

<span data-ttu-id="825dc-324">单击 "**管理**" 链接。</span><span class="sxs-lookup"><span data-stu-id="825dc-324">Click on the **Manage** link.</span></span> <span data-ttu-id="825dc-325">请注意与此帐户关联的0个外部（社交登录）。</span><span class="sxs-lookup"><span data-stu-id="825dc-325">Note the 0 external (social logins) associated with this account.</span></span>

![管理视图](accconfirm/_static/manage.png)

<span data-ttu-id="825dc-327">单击指向另一登录服务的链接，并接受应用请求。</span><span class="sxs-lookup"><span data-stu-id="825dc-327">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="825dc-328">在下图中，Facebook 是外部身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="825dc-328">In the following image, Facebook is the external authentication provider:</span></span>

![管理你的外部登录名视图列表 Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="825dc-330">这两个帐户已组合在一起。</span><span class="sxs-lookup"><span data-stu-id="825dc-330">The two accounts have been combined.</span></span> <span data-ttu-id="825dc-331">你可以用任一帐户登录。</span><span class="sxs-lookup"><span data-stu-id="825dc-331">You are able to sign in with either account.</span></span> <span data-ttu-id="825dc-332">你可能希望用户在社交登录身份验证服务关闭时添加本地帐户，或者更可能的情况是他们失去了社交帐户的访问权限。</span><span class="sxs-lookup"><span data-stu-id="825dc-332">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="825dc-333">在站点包含用户后启用帐户确认</span><span class="sxs-lookup"><span data-stu-id="825dc-333">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="825dc-334">在具有用户的站点上启用帐户确认会锁定所有现有用户。</span><span class="sxs-lookup"><span data-stu-id="825dc-334">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="825dc-335">现有用户被锁定，因为其帐户未得到确认。</span><span class="sxs-lookup"><span data-stu-id="825dc-335">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="825dc-336">若要解决现有用户锁定，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="825dc-336">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="825dc-337">更新数据库，将所有现有用户标记为已确认。</span><span class="sxs-lookup"><span data-stu-id="825dc-337">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="825dc-338">确认现有用户。</span><span class="sxs-lookup"><span data-stu-id="825dc-338">Confirm existing users.</span></span> <span data-ttu-id="825dc-339">例如，批处理-发送包含确认链接的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="825dc-339">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
