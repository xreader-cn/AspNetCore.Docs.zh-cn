---
title: 帐户确认和 ASP.NET Core 中的密码恢复
author: rick-anderson
description: 了解如何生成使用电子邮件确认及密码重置功能的 ASP.NET Core 应用程序。
ms.author: riande
ms.date: 03/11/2019
uid: security/authentication/accconfirm
ms.openlocfilehash: 49d3d214fd64edc5b17df2df929ddc3c2af47ede
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829265"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="95f25-103">帐户确认和 ASP.NET Core 中的密码恢复</span><span class="sxs-lookup"><span data-stu-id="95f25-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="95f25-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Ponant](https://github.com/Ponant)和[Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="95f25-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="95f25-105">本教程介绍如何使用电子邮件确认和密码重置构建 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="95f25-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="95f25-106">本教程**不**是开始主题。</span><span class="sxs-lookup"><span data-stu-id="95f25-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="95f25-107">您应熟悉：</span><span class="sxs-lookup"><span data-stu-id="95f25-107">You should be familiar with:</span></span>

* [<span data-ttu-id="95f25-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="95f25-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="95f25-109">身份验证</span><span class="sxs-lookup"><span data-stu-id="95f25-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="95f25-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="95f25-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="95f25-111">有关 ASP.NET Core 1.1 版本，请参阅[此 PDF 文件](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)。</span><span class="sxs-lookup"><span data-stu-id="95f25-111">See [this PDF file](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core 1.1 version.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.2"

## <a name="prerequisites"></a><span data-ttu-id="95f25-112">先决条件</span><span class="sxs-lookup"><span data-stu-id="95f25-112">Prerequisites</span></span>

[<span data-ttu-id="95f25-113">.NET Core 3.0 SDK 或更高版本</span><span class="sxs-lookup"><span data-stu-id="95f25-113">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="95f25-114">创建和测试使用身份验证的 web 应用</span><span class="sxs-lookup"><span data-stu-id="95f25-114">Create and test a web app with authentication</span></span>

<span data-ttu-id="95f25-115">运行以下命令，创建具有身份验证的 web 应用。</span><span class="sxs-lookup"><span data-stu-id="95f25-115">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="95f25-116">运行应用，选择 "**注册**" 链接，然后注册用户。</span><span class="sxs-lookup"><span data-stu-id="95f25-116">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="95f25-117">注册后，会重定向到 `/Identity/Account/RegisterConfirmation` "页面，其中包含用于模拟电子邮件确认的链接：</span><span class="sxs-lookup"><span data-stu-id="95f25-117">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="95f25-118">选择 "`Click here to confirm your account`" 链接。</span><span class="sxs-lookup"><span data-stu-id="95f25-118">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="95f25-119">选择 "**登录**" 链接，并以相同的凭据登录。</span><span class="sxs-lookup"><span data-stu-id="95f25-119">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="95f25-120">选择 "`Hello YourEmail@provider.com!`" 链接，该链接会将你重定向到 `/Identity/Account/Manage/PersonalData` 页面。</span><span class="sxs-lookup"><span data-stu-id="95f25-120">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="95f25-121">选择左侧的 "**个人数据**" 选项卡，然后选择 "**删除**"。</span><span class="sxs-lookup"><span data-stu-id="95f25-121">Select the **Personal data** tab on the left, and then select **Delete**.</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="95f25-122">配置电子邮件提供程序</span><span class="sxs-lookup"><span data-stu-id="95f25-122">Configure an email provider</span></span>

<span data-ttu-id="95f25-123">在本教程中，使用[SendGrid](https://sendgrid.com)发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-123">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="95f25-124">需要使用 SendGrid 帐户和密钥来发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-124">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="95f25-125">您可以使用其他电子邮件提供程序。</span><span class="sxs-lookup"><span data-stu-id="95f25-125">You can use other email providers.</span></span> <span data-ttu-id="95f25-126">建议使用 SendGrid 或其他电子邮件服务发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-126">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="95f25-127">SMTP 难于保护和正确设置。</span><span class="sxs-lookup"><span data-stu-id="95f25-127">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="95f25-128">创建一个类以获取安全电子邮件密钥。</span><span class="sxs-lookup"><span data-stu-id="95f25-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="95f25-129">对于本示例，请创建*服务/AuthMessageSenderOptions*：</span><span class="sxs-lookup"><span data-stu-id="95f25-129">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="95f25-130">配置 SendGrid 用户机密</span><span class="sxs-lookup"><span data-stu-id="95f25-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="95f25-131">用[机密管理器工具](xref:security/app-secrets)设置 `SendGridUser` 和 `SendGridKey`。</span><span class="sxs-lookup"><span data-stu-id="95f25-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="95f25-132">例如：</span><span class="sxs-lookup"><span data-stu-id="95f25-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="95f25-133">在 Windows 上，机密管理器将密钥/值对存储在 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 目录*中的 Secret 文件中*。</span><span class="sxs-lookup"><span data-stu-id="95f25-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="95f25-134">不会对*机密 json*文件的内容进行加密。</span><span class="sxs-lookup"><span data-stu-id="95f25-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="95f25-135">以下标记显示了*机密的 json*文件。</span><span class="sxs-lookup"><span data-stu-id="95f25-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="95f25-136">已删除 `SendGridKey` 值。</span><span class="sxs-lookup"><span data-stu-id="95f25-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="95f25-137">有关详细信息，请参阅[Options 模式](xref:fundamentals/configuration/options)和[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="95f25-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="95f25-138">安装 SendGrid</span><span class="sxs-lookup"><span data-stu-id="95f25-138">Install SendGrid</span></span>

<span data-ttu-id="95f25-139">本教程介绍如何通过[SendGrid](https://sendgrid.com/)添加电子邮件通知，但你可以使用 SMTP 和其他机制发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="95f25-140">安装 `SendGrid` NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="95f25-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="95f25-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95f25-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95f25-142">在 "包管理器控制台" 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="95f25-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="95f25-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="95f25-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="95f25-144">在控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="95f25-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="95f25-145">请参阅[SendGrid 的入门免费](https://sendgrid.com/free/)版，注册免费 SendGrid 帐户。</span><span class="sxs-lookup"><span data-stu-id="95f25-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="95f25-146">实现 IEmailSender</span><span class="sxs-lookup"><span data-stu-id="95f25-146">Implement IEmailSender</span></span>

<span data-ttu-id="95f25-147">若要实现 `IEmailSender`，请创建具有类似于下面的代码的*服务 EmailSender* ：</span><span class="sxs-lookup"><span data-stu-id="95f25-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="95f25-148">配置启动以支持电子邮件</span><span class="sxs-lookup"><span data-stu-id="95f25-148">Configure startup to support email</span></span>

<span data-ttu-id="95f25-149">将以下代码添加到*Startup.cs*文件的 `ConfigureServices` 方法中：</span><span class="sxs-lookup"><span data-stu-id="95f25-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="95f25-150">将 `EmailSender` 添加为暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="95f25-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="95f25-151">注册 `AuthMessageSenderOptions` 配置实例。</span><span class="sxs-lookup"><span data-stu-id="95f25-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="95f25-152">注册、确认电子邮件并重置密码</span><span class="sxs-lookup"><span data-stu-id="95f25-152">Register, confirm email, and reset password</span></span>

<span data-ttu-id="95f25-153">运行 web 应用，并测试帐户确认和密码恢复流。</span><span class="sxs-lookup"><span data-stu-id="95f25-153">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="95f25-154">运行应用并注册一个新用户</span><span class="sxs-lookup"><span data-stu-id="95f25-154">Run the app and register a new user</span></span>
* <span data-ttu-id="95f25-155">检查电子邮件中的 "帐户确认" 链接。</span><span class="sxs-lookup"><span data-stu-id="95f25-155">Check your email for the account confirmation link.</span></span> <span data-ttu-id="95f25-156">如果没有收到电子邮件，请参阅[调试电子邮件](#debug)。</span><span class="sxs-lookup"><span data-stu-id="95f25-156">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="95f25-157">单击链接以确认你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-157">Click the link to confirm your email.</span></span>
* <span data-ttu-id="95f25-158">用电子邮件和密码登录。</span><span class="sxs-lookup"><span data-stu-id="95f25-158">Sign in with your email and password.</span></span>
* <span data-ttu-id="95f25-159">注销。</span><span class="sxs-lookup"><span data-stu-id="95f25-159">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="95f25-160">测试密码重置</span><span class="sxs-lookup"><span data-stu-id="95f25-160">Test password reset</span></span>

* <span data-ttu-id="95f25-161">如果已登录，请选择 "**注销**"。</span><span class="sxs-lookup"><span data-stu-id="95f25-161">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="95f25-162">选择 "**登录**" 链接，然后选择 "**忘记了密码？"** 链接。</span><span class="sxs-lookup"><span data-stu-id="95f25-162">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="95f25-163">输入用于注册该帐户的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-163">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="95f25-164">发送了一封电子邮件，其中包含用于重置密码的链接。</span><span class="sxs-lookup"><span data-stu-id="95f25-164">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="95f25-165">检查你的电子邮件，然后单击链接以重置你的密码。</span><span class="sxs-lookup"><span data-stu-id="95f25-165">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="95f25-166">密码重置成功后，可以用电子邮件和新密码登录。</span><span class="sxs-lookup"><span data-stu-id="95f25-166">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="95f25-167">更改电子邮件和活动超时</span><span class="sxs-lookup"><span data-stu-id="95f25-167">Change email and activity timeout</span></span>

<span data-ttu-id="95f25-168">默认的非活动超时为14天。</span><span class="sxs-lookup"><span data-stu-id="95f25-168">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="95f25-169">下面的代码将非活动超时设置为5天：</span><span class="sxs-lookup"><span data-stu-id="95f25-169">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="95f25-170">更改所有数据保护令牌 lifespans</span><span class="sxs-lookup"><span data-stu-id="95f25-170">Change all data protection token lifespans</span></span>

<span data-ttu-id="95f25-171">以下代码将所有数据保护令牌超时期限更改为3小时：</span><span class="sxs-lookup"><span data-stu-id="95f25-171">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="95f25-172">内置标识用户令牌（请参阅[AspNetCore/src/Identity/extension/src/src/TokenOptions](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ）具有[一天的超时时间](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="95f25-172">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="95f25-173">更改电子邮件令牌的生命周期</span><span class="sxs-lookup"><span data-stu-id="95f25-173">Change the email token lifespan</span></span>

<span data-ttu-id="95f25-174">[标识用户令牌](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)的默认令牌生存期为[1 天](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="95f25-174">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="95f25-175">本部分介绍如何更改电子邮件令牌的生命周期。</span><span class="sxs-lookup"><span data-stu-id="95f25-175">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="95f25-176">添加自定义的[DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)并 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>：</span><span class="sxs-lookup"><span data-stu-id="95f25-176">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="95f25-177">将自定义提供程序添加到服务容器：</span><span class="sxs-lookup"><span data-stu-id="95f25-177">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="95f25-178">重新发送电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="95f25-178">Resend email confirmation</span></span>

<span data-ttu-id="95f25-179">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/5410)。</span><span class="sxs-lookup"><span data-stu-id="95f25-179">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="95f25-180">调试电子邮件</span><span class="sxs-lookup"><span data-stu-id="95f25-180">Debug email</span></span>

<span data-ttu-id="95f25-181">如果无法使用电子邮件：</span><span class="sxs-lookup"><span data-stu-id="95f25-181">If you can't get email working:</span></span>

* <span data-ttu-id="95f25-182">在 `EmailSender.Execute` 中设置断点以验证是否调用了 `SendGridClient.SendEmailAsync`。</span><span class="sxs-lookup"><span data-stu-id="95f25-182">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="95f25-183">创建一个[控制台应用程序，以便](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)使用类似的代码将电子邮件发送到 `EmailSender.Execute`。</span><span class="sxs-lookup"><span data-stu-id="95f25-183">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="95f25-184">查看[电子邮件活动](https://sendgrid.com/docs/User_Guide/email_activity.html)页。</span><span class="sxs-lookup"><span data-stu-id="95f25-184">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="95f25-185">检查垃圾邮件文件夹。</span><span class="sxs-lookup"><span data-stu-id="95f25-185">Check your spam folder.</span></span>
* <span data-ttu-id="95f25-186">尝试使用其他电子邮件提供商（Microsoft、Yahoo、Gmail 等）中的另一个电子邮件别名</span><span class="sxs-lookup"><span data-stu-id="95f25-186">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="95f25-187">尝试发送到不同的电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="95f25-187">Try sending to different email accounts.</span></span>

<span data-ttu-id="95f25-188">**最佳安全做法**是**不**在测试和开发中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="95f25-188">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="95f25-189">如果将应用发布到 Azure，请在 Azure Web 应用门户中将 "SendGrid 机密" 设置为 "应用程序设置"。</span><span class="sxs-lookup"><span data-stu-id="95f25-189">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="95f25-190">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="95f25-190">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="95f25-191">合并社会和本地登录帐户</span><span class="sxs-lookup"><span data-stu-id="95f25-191">Combine social and local login accounts</span></span>

<span data-ttu-id="95f25-192">若要完成本部分，必须首先启用外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="95f25-192">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="95f25-193">请参阅[Facebook、Google 和外部提供程序身份验证](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="95f25-193">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="95f25-194">可以通过单击电子邮件链接来合并本地帐户和社交帐户。</span><span class="sxs-lookup"><span data-stu-id="95f25-194">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="95f25-195">按照以下顺序，"RickAndMSFT@gmail.com" 首先创建为本地登录名;但是，你可以先将帐户创建为社交登录名，然后添加本地登录名。</span><span class="sxs-lookup"><span data-stu-id="95f25-195">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web 应用程序： RickAndMSFT@gmail.com 用户已进行身份验证](accconfirm/_static/rick.png)

<span data-ttu-id="95f25-197">单击 "**管理**" 链接。</span><span class="sxs-lookup"><span data-stu-id="95f25-197">Click on the **Manage** link.</span></span> <span data-ttu-id="95f25-198">请注意与此帐户关联的0个外部（社交登录）。</span><span class="sxs-lookup"><span data-stu-id="95f25-198">Note the 0 external (social logins) associated with this account.</span></span>

![管理视图](accconfirm/_static/manage.png)

<span data-ttu-id="95f25-200">单击指向另一登录服务的链接，并接受应用请求。</span><span class="sxs-lookup"><span data-stu-id="95f25-200">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="95f25-201">在下图中，Facebook 是外部身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="95f25-201">In the following image, Facebook is the external authentication provider:</span></span>

![管理你的外部登录名视图列表 Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="95f25-203">这两个帐户已组合在一起。</span><span class="sxs-lookup"><span data-stu-id="95f25-203">The two accounts have been combined.</span></span> <span data-ttu-id="95f25-204">你可以用任一帐户登录。</span><span class="sxs-lookup"><span data-stu-id="95f25-204">You are able to sign in with either account.</span></span> <span data-ttu-id="95f25-205">你可能希望用户在社交登录身份验证服务关闭时添加本地帐户，或者更可能的情况是他们失去了社交帐户的访问权限。</span><span class="sxs-lookup"><span data-stu-id="95f25-205">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="95f25-206">在站点包含用户后启用帐户确认</span><span class="sxs-lookup"><span data-stu-id="95f25-206">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="95f25-207">在具有用户的站点上启用帐户确认会锁定所有现有用户。</span><span class="sxs-lookup"><span data-stu-id="95f25-207">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="95f25-208">现有用户被锁定，因为其帐户未得到确认。</span><span class="sxs-lookup"><span data-stu-id="95f25-208">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="95f25-209">若要解决现有用户锁定，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="95f25-209">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="95f25-210">更新数据库，将所有现有用户标记为已确认。</span><span class="sxs-lookup"><span data-stu-id="95f25-210">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="95f25-211">确认现有用户。</span><span class="sxs-lookup"><span data-stu-id="95f25-211">Confirm existing users.</span></span> <span data-ttu-id="95f25-212">例如，批处理-发送包含确认链接的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-212">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0 < aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="95f25-213">先决条件</span><span class="sxs-lookup"><span data-stu-id="95f25-213">Prerequisites</span></span>

[<span data-ttu-id="95f25-214">.NET Core 2.2 SDK 或更高版本</span><span class="sxs-lookup"><span data-stu-id="95f25-214">.NET Core 2.2 SDK or later</span></span>](https://www.microsoft.com/net/download/all)

## <a name="create-a-web--app-and-scaffold-identity"></a><span data-ttu-id="95f25-215">创建 web 应用和基架标识</span><span class="sxs-lookup"><span data-stu-id="95f25-215">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="95f25-216">运行以下命令，创建具有身份验证的 web 应用。</span><span class="sxs-lookup"><span data-stu-id="95f25-216">Run the following commands to create a web app with authentication.</span></span>

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

## <a name="test-new-user-registration"></a><span data-ttu-id="95f25-217">测试新用户注册</span><span class="sxs-lookup"><span data-stu-id="95f25-217">Test new user registration</span></span>

<span data-ttu-id="95f25-218">运行应用，选择 "**注册**" 链接，然后注册用户。</span><span class="sxs-lookup"><span data-stu-id="95f25-218">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="95f25-219">此时，该电子邮件的唯一验证是带有[`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute)特性。</span><span class="sxs-lookup"><span data-stu-id="95f25-219">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="95f25-220">提交注册后，将登录到应用。</span><span class="sxs-lookup"><span data-stu-id="95f25-220">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="95f25-221">在本教程的后面部分，将更新代码，以便新用户在验证其电子邮件之前无法登录。</span><span class="sxs-lookup"><span data-stu-id="95f25-221">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="95f25-222">请注意，表的 `EmailConfirmed` 字段是 `False`的。</span><span class="sxs-lookup"><span data-stu-id="95f25-222">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="95f25-223">当应用发送确认电子邮件时，可能需要在下一步中再次使用此电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-223">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="95f25-224">右键单击该行，然后选择 "**删除**"。</span><span class="sxs-lookup"><span data-stu-id="95f25-224">Right-click on the row and select **Delete**.</span></span> <span data-ttu-id="95f25-225">删除电子邮件别名可以简化以下步骤。</span><span class="sxs-lookup"><span data-stu-id="95f25-225">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="95f25-226">需要确认电子邮件</span><span class="sxs-lookup"><span data-stu-id="95f25-226">Require email confirmation</span></span>

<span data-ttu-id="95f25-227">最佳做法是确认新用户注册的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-227">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="95f25-228">电子邮件确认有助于验证他们是否未模拟其他人（即，他们未注册其他人的电子邮件）。</span><span class="sxs-lookup"><span data-stu-id="95f25-228">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="95f25-229">假设你有讨论论坛，并且想要阻止 "yli@example.com" 注册为 "nolivetto@contoso.com"。</span><span class="sxs-lookup"><span data-stu-id="95f25-229">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="95f25-230">如果未确认电子邮件，"nolivetto@contoso.com" 可能会从你的应用收到不需要的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-230">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="95f25-231">假设用户意外注册为 "ylo@example.com"，未注意到 "yli" 的拼写错误。</span><span class="sxs-lookup"><span data-stu-id="95f25-231">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="95f25-232">它们不能使用密码恢复，因为该应用没有正确的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-232">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="95f25-233">电子邮件确认为 bot 提供有限的保护。</span><span class="sxs-lookup"><span data-stu-id="95f25-233">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="95f25-234">电子邮件确认不会为具有多个电子邮件帐户的恶意用户提供保护。</span><span class="sxs-lookup"><span data-stu-id="95f25-234">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="95f25-235">通常，在用户确认电子邮件之前，会阻止新用户将任何数据发布到您的网站。</span><span class="sxs-lookup"><span data-stu-id="95f25-235">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="95f25-236">更新 `Startup.ConfigureServices`，要求确认电子邮件：</span><span class="sxs-lookup"><span data-stu-id="95f25-236">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="95f25-237">`config.SignIn.RequireConfirmedEmail = true;` 阻止注册的用户登录，直到其电子邮件得到确认。</span><span class="sxs-lookup"><span data-stu-id="95f25-237">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="95f25-238">配置电子邮件提供程序</span><span class="sxs-lookup"><span data-stu-id="95f25-238">Configure email provider</span></span>

<span data-ttu-id="95f25-239">在本教程中，使用[SendGrid](https://sendgrid.com)发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-239">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="95f25-240">需要使用 SendGrid 帐户和密钥来发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-240">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="95f25-241">您可以使用其他电子邮件提供程序。</span><span class="sxs-lookup"><span data-stu-id="95f25-241">You can use other email providers.</span></span> <span data-ttu-id="95f25-242">ASP.NET Core 2.x 包括`System.Net.Mail`，这允许你从你的应用程序发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-242">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="95f25-243">建议使用 SendGrid 或其他电子邮件服务发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-243">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="95f25-244">SMTP 难于保护和正确设置。</span><span class="sxs-lookup"><span data-stu-id="95f25-244">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="95f25-245">创建一个类以获取安全电子邮件密钥。</span><span class="sxs-lookup"><span data-stu-id="95f25-245">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="95f25-246">对于本示例，请创建*服务/AuthMessageSenderOptions*：</span><span class="sxs-lookup"><span data-stu-id="95f25-246">For this sample, create *Services/AuthMessageSenderOptions.cs*:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="95f25-247">配置 SendGrid 用户机密</span><span class="sxs-lookup"><span data-stu-id="95f25-247">Configure SendGrid user secrets</span></span>

<span data-ttu-id="95f25-248">用[机密管理器工具](xref:security/app-secrets)设置 `SendGridUser` 和 `SendGridKey`。</span><span class="sxs-lookup"><span data-stu-id="95f25-248">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="95f25-249">例如：</span><span class="sxs-lookup"><span data-stu-id="95f25-249">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="95f25-250">在 Windows 上，机密管理器将密钥/值对存储在 `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 目录*中的 Secret 文件中*。</span><span class="sxs-lookup"><span data-stu-id="95f25-250">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="95f25-251">不会对*机密 json*文件的内容进行加密。</span><span class="sxs-lookup"><span data-stu-id="95f25-251">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="95f25-252">以下标记显示了*机密的 json*文件。</span><span class="sxs-lookup"><span data-stu-id="95f25-252">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="95f25-253">已删除 `SendGridKey` 值。</span><span class="sxs-lookup"><span data-stu-id="95f25-253">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="95f25-254">有关详细信息，请参阅[Options 模式](xref:fundamentals/configuration/options)和[配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="95f25-254">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="95f25-255">安装 SendGrid</span><span class="sxs-lookup"><span data-stu-id="95f25-255">Install SendGrid</span></span>

<span data-ttu-id="95f25-256">本教程介绍如何通过[SendGrid](https://sendgrid.com/)添加电子邮件通知，但你可以使用 SMTP 和其他机制发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-256">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="95f25-257">安装 `SendGrid` NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="95f25-257">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="95f25-258">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95f25-258">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95f25-259">在 "包管理器控制台" 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="95f25-259">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="95f25-260">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="95f25-260">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="95f25-261">在控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="95f25-261">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="95f25-262">请参阅[SendGrid 的入门免费](https://sendgrid.com/free/)版，注册免费 SendGrid 帐户。</span><span class="sxs-lookup"><span data-stu-id="95f25-262">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="95f25-263">实现 IEmailSender</span><span class="sxs-lookup"><span data-stu-id="95f25-263">Implement IEmailSender</span></span>

<span data-ttu-id="95f25-264">若要实现 `IEmailSender`，请创建具有类似于下面的代码的*服务 EmailSender* ：</span><span class="sxs-lookup"><span data-stu-id="95f25-264">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="95f25-265">配置启动以支持电子邮件</span><span class="sxs-lookup"><span data-stu-id="95f25-265">Configure startup to support email</span></span>

<span data-ttu-id="95f25-266">将以下代码添加到*Startup.cs*文件的 `ConfigureServices` 方法中：</span><span class="sxs-lookup"><span data-stu-id="95f25-266">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="95f25-267">将 `EmailSender` 添加为暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="95f25-267">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="95f25-268">注册 `AuthMessageSenderOptions` 配置实例。</span><span class="sxs-lookup"><span data-stu-id="95f25-268">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="95f25-269">启用帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="95f25-269">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="95f25-270">该模板包含用于帐户确认和密码恢复的代码。</span><span class="sxs-lookup"><span data-stu-id="95f25-270">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="95f25-271">在*区域/标识/页面/帐户/注册. .cs*中查找 `OnPostAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="95f25-271">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs*.</span></span>

<span data-ttu-id="95f25-272">通过注释掉以下行，阻止新注册的用户自动登录：</span><span class="sxs-lookup"><span data-stu-id="95f25-272">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="95f25-273">将显示完整的方法，其中突出显示了已更改的行：</span><span class="sxs-lookup"><span data-stu-id="95f25-273">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="95f25-274">注册、确认电子邮件并重置密码</span><span class="sxs-lookup"><span data-stu-id="95f25-274">Register, confirm email, and reset password</span></span>

<span data-ttu-id="95f25-275">运行 web 应用，并测试帐户确认和密码恢复流。</span><span class="sxs-lookup"><span data-stu-id="95f25-275">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="95f25-276">运行应用并注册一个新用户</span><span class="sxs-lookup"><span data-stu-id="95f25-276">Run the app and register a new user</span></span>
* <span data-ttu-id="95f25-277">检查电子邮件中的 "帐户确认" 链接。</span><span class="sxs-lookup"><span data-stu-id="95f25-277">Check your email for the account confirmation link.</span></span> <span data-ttu-id="95f25-278">如果没有收到电子邮件，请参阅[调试电子邮件](#debug)。</span><span class="sxs-lookup"><span data-stu-id="95f25-278">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="95f25-279">单击链接以确认你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-279">Click the link to confirm your email.</span></span>
* <span data-ttu-id="95f25-280">用电子邮件和密码登录。</span><span class="sxs-lookup"><span data-stu-id="95f25-280">Sign in with your email and password.</span></span>
* <span data-ttu-id="95f25-281">注销。</span><span class="sxs-lookup"><span data-stu-id="95f25-281">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="95f25-282">查看 "管理" 页</span><span class="sxs-lookup"><span data-stu-id="95f25-282">View the manage page</span></span>

<span data-ttu-id="95f25-283">在浏览器中选择你的用户名，并在浏览器中选择用户名](accconfirm/_static/un.png) ![浏览器窗口</span><span class="sxs-lookup"><span data-stu-id="95f25-283">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="95f25-284">将显示 "管理" 页，并选中 "**配置文件**" 选项卡。</span><span class="sxs-lookup"><span data-stu-id="95f25-284">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="95f25-285">该**电子邮件**将显示一个复选框，指示已确认电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-285">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="95f25-286">测试密码重置</span><span class="sxs-lookup"><span data-stu-id="95f25-286">Test password reset</span></span>

* <span data-ttu-id="95f25-287">如果已登录，请选择 "**注销**"。</span><span class="sxs-lookup"><span data-stu-id="95f25-287">If you're signed in, select **Logout**.</span></span>
* <span data-ttu-id="95f25-288">选择 "**登录**" 链接，然后选择 "**忘记了密码？"** 链接。</span><span class="sxs-lookup"><span data-stu-id="95f25-288">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="95f25-289">输入用于注册该帐户的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-289">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="95f25-290">发送了一封电子邮件，其中包含用于重置密码的链接。</span><span class="sxs-lookup"><span data-stu-id="95f25-290">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="95f25-291">检查你的电子邮件，然后单击链接以重置你的密码。</span><span class="sxs-lookup"><span data-stu-id="95f25-291">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="95f25-292">密码重置成功后，可以用电子邮件和新密码登录。</span><span class="sxs-lookup"><span data-stu-id="95f25-292">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="95f25-293">更改电子邮件和活动超时</span><span class="sxs-lookup"><span data-stu-id="95f25-293">Change email and activity timeout</span></span>

<span data-ttu-id="95f25-294">默认的非活动超时为14天。</span><span class="sxs-lookup"><span data-stu-id="95f25-294">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="95f25-295">下面的代码将非活动超时设置为5天：</span><span class="sxs-lookup"><span data-stu-id="95f25-295">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="95f25-296">更改所有数据保护令牌 lifespans</span><span class="sxs-lookup"><span data-stu-id="95f25-296">Change all data protection token lifespans</span></span>

<span data-ttu-id="95f25-297">以下代码将所有数据保护令牌超时期限更改为3小时：</span><span class="sxs-lookup"><span data-stu-id="95f25-297">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="95f25-298">内置标识用户令牌（请参阅[AspNetCore/src/Identity/extension/src/src/TokenOptions](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ）具有[一天的超时时间](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="95f25-298">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="95f25-299">更改电子邮件令牌的生命周期</span><span class="sxs-lookup"><span data-stu-id="95f25-299">Change the email token lifespan</span></span>

<span data-ttu-id="95f25-300">[标识用户令牌](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)的默认令牌生存期为[1 天](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="95f25-300">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="95f25-301">本部分介绍如何更改电子邮件令牌的生命周期。</span><span class="sxs-lookup"><span data-stu-id="95f25-301">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="95f25-302">添加自定义的[DataProtectorTokenProvider\<TUser >](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)并 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>：</span><span class="sxs-lookup"><span data-stu-id="95f25-302">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="95f25-303">将自定义提供程序添加到服务容器：</span><span class="sxs-lookup"><span data-stu-id="95f25-303">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="95f25-304">重新发送电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="95f25-304">Resend email confirmation</span></span>

<span data-ttu-id="95f25-305">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/5410)。</span><span class="sxs-lookup"><span data-stu-id="95f25-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="95f25-306">调试电子邮件</span><span class="sxs-lookup"><span data-stu-id="95f25-306">Debug email</span></span>

<span data-ttu-id="95f25-307">如果无法使用电子邮件：</span><span class="sxs-lookup"><span data-stu-id="95f25-307">If you can't get email working:</span></span>

* <span data-ttu-id="95f25-308">在 `EmailSender.Execute` 中设置断点以验证是否调用了 `SendGridClient.SendEmailAsync`。</span><span class="sxs-lookup"><span data-stu-id="95f25-308">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="95f25-309">创建一个[控制台应用程序，以便](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html)使用类似的代码将电子邮件发送到 `EmailSender.Execute`。</span><span class="sxs-lookup"><span data-stu-id="95f25-309">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="95f25-310">查看[电子邮件活动](https://sendgrid.com/docs/User_Guide/email_activity.html)页。</span><span class="sxs-lookup"><span data-stu-id="95f25-310">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="95f25-311">检查垃圾邮件文件夹。</span><span class="sxs-lookup"><span data-stu-id="95f25-311">Check your spam folder.</span></span>
* <span data-ttu-id="95f25-312">尝试使用其他电子邮件提供商（Microsoft、Yahoo、Gmail 等）中的另一个电子邮件别名</span><span class="sxs-lookup"><span data-stu-id="95f25-312">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="95f25-313">尝试发送到不同的电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="95f25-313">Try sending to different email accounts.</span></span>

<span data-ttu-id="95f25-314">**最佳安全做法**是**不**在测试和开发中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="95f25-314">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="95f25-315">如果将应用发布到 Azure，则可以在 Azure Web 应用门户中将 SendGrid 机密设置为应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="95f25-315">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="95f25-316">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="95f25-316">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="95f25-317">合并社会和本地登录帐户</span><span class="sxs-lookup"><span data-stu-id="95f25-317">Combine social and local login accounts</span></span>

<span data-ttu-id="95f25-318">若要完成本部分，必须首先启用外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="95f25-318">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="95f25-319">请参阅[Facebook、Google 和外部提供程序身份验证](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="95f25-319">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="95f25-320">可以通过单击电子邮件链接来合并本地帐户和社交帐户。</span><span class="sxs-lookup"><span data-stu-id="95f25-320">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="95f25-321">按照以下顺序，"RickAndMSFT@gmail.com" 首先创建为本地登录名;但是，你可以先将帐户创建为社交登录名，然后添加本地登录名。</span><span class="sxs-lookup"><span data-stu-id="95f25-321">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web 应用程序： RickAndMSFT@gmail.com 用户已进行身份验证](accconfirm/_static/rick.png)

<span data-ttu-id="95f25-323">单击 "**管理**" 链接。</span><span class="sxs-lookup"><span data-stu-id="95f25-323">Click on the **Manage** link.</span></span> <span data-ttu-id="95f25-324">请注意与此帐户关联的0个外部（社交登录）。</span><span class="sxs-lookup"><span data-stu-id="95f25-324">Note the 0 external (social logins) associated with this account.</span></span>

![管理视图](accconfirm/_static/manage.png)

<span data-ttu-id="95f25-326">单击指向另一登录服务的链接，并接受应用请求。</span><span class="sxs-lookup"><span data-stu-id="95f25-326">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="95f25-327">在下图中，Facebook 是外部身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="95f25-327">In the following image, Facebook is the external authentication provider:</span></span>

![管理你的外部登录名视图列表 Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="95f25-329">这两个帐户已组合在一起。</span><span class="sxs-lookup"><span data-stu-id="95f25-329">The two accounts have been combined.</span></span> <span data-ttu-id="95f25-330">你可以用任一帐户登录。</span><span class="sxs-lookup"><span data-stu-id="95f25-330">You are able to sign in with either account.</span></span> <span data-ttu-id="95f25-331">你可能希望用户在社交登录身份验证服务关闭时添加本地帐户，或者更可能的情况是他们失去了社交帐户的访问权限。</span><span class="sxs-lookup"><span data-stu-id="95f25-331">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="95f25-332">在站点包含用户后启用帐户确认</span><span class="sxs-lookup"><span data-stu-id="95f25-332">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="95f25-333">在具有用户的站点上启用帐户确认会锁定所有现有用户。</span><span class="sxs-lookup"><span data-stu-id="95f25-333">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="95f25-334">现有用户被锁定，因为其帐户未得到确认。</span><span class="sxs-lookup"><span data-stu-id="95f25-334">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="95f25-335">若要解决现有用户锁定，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="95f25-335">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="95f25-336">更新数据库，将所有现有用户标记为已确认。</span><span class="sxs-lookup"><span data-stu-id="95f25-336">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="95f25-337">确认现有用户。</span><span class="sxs-lookup"><span data-stu-id="95f25-337">Confirm existing users.</span></span> <span data-ttu-id="95f25-338">例如，批处理-发送包含确认链接的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="95f25-338">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
