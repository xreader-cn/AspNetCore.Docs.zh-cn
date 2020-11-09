---
title: ASP.NET Core 中的帐户确认和密码恢复
author: rick-anderson
description: 了解如何使用电子邮件确认和密码重置构建 ASP.NET Core 应用。
ms.author: riande
ms.date: 03/11/2019
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authentication/accconfirm
ms.openlocfilehash: 91148c67d5dc0bf97e2f926f50dcff5dd0708f4b
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052313"
---
# <a name="account-confirmation-and-password-recovery-in-aspnet-core"></a><span data-ttu-id="ad3ad-103">ASP.NET Core 中的帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="ad3ad-103">Account confirmation and password recovery in ASP.NET Core</span></span>

<span data-ttu-id="ad3ad-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Ponant](https://github.com/Ponant)和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="ad3ad-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Ponant](https://github.com/Ponant), and [Joe Audette](https://twitter.com/joeaudette)</span></span>

<span data-ttu-id="ad3ad-105">本教程介绍如何使用电子邮件确认和密码重置构建 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-105">This tutorial shows how to build an ASP.NET Core app with email confirmation and password reset.</span></span> <span data-ttu-id="ad3ad-106">本教程 **不** 是开始主题。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-106">This tutorial is **not** a beginning topic.</span></span> <span data-ttu-id="ad3ad-107">你应该熟悉：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-107">You should be familiar with:</span></span>

* [<span data-ttu-id="ad3ad-108">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ad3ad-108">ASP.NET Core</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="ad3ad-109">身份验证</span><span class="sxs-lookup"><span data-stu-id="ad3ad-109">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="ad3ad-110">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="ad3ad-110">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

<!-- see C:/Dropbox/wrk/Code/SendGridConsole/Program.cs -->

::: moniker range=">= aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="ad3ad-111">先决条件</span><span class="sxs-lookup"><span data-stu-id="ad3ad-111">Prerequisites</span></span>

[<span data-ttu-id="ad3ad-112">.NET Core 3.0 SDK 或更高版本</span><span class="sxs-lookup"><span data-stu-id="ad3ad-112">.NET Core 3.0 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core/3.0)

## <a name="create-and-test-a-web-app-with-authentication"></a><span data-ttu-id="ad3ad-113">创建和测试使用身份验证的 web 应用</span><span class="sxs-lookup"><span data-stu-id="ad3ad-113">Create and test a web app with authentication</span></span>

<span data-ttu-id="ad3ad-114">运行以下命令，创建具有身份验证的 web 应用。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-114">Run the following commands to create a web app with authentication.</span></span>

```dotnetcli
dotnet new webapp -au Individual -uld -o WebPWrecover
cd WebPWrecover
dotnet run
```

<span data-ttu-id="ad3ad-115">运行应用，选择 " **注册** " 链接，然后注册用户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-115">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="ad3ad-116">注册后，你会被重定向到 "目标" `/Identity/Account/RegisterConfirmation` 页，其中包含用于模拟电子邮件确认的链接：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-116">Once registered, you are redirected to the to `/Identity/Account/RegisterConfirmation` page which contains a link to simulate email confirmation:</span></span>

* <span data-ttu-id="ad3ad-117">选择 `Click here to confirm your account` 链接。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-117">Select the `Click here to confirm your account` link.</span></span>
* <span data-ttu-id="ad3ad-118">选择 " **登录** " 链接，并以相同的凭据登录。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-118">Select the **Login** link and sign-in with the same credentials.</span></span>
* <span data-ttu-id="ad3ad-119">选择将 `Hello YourEmail@provider.com!` 您重定向到页面的链接 `/Identity/Account/Manage/PersonalData` 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-119">Select the `Hello YourEmail@provider.com!` link, which redirects you to the `/Identity/Account/Manage/PersonalData` page.</span></span>
* <span data-ttu-id="ad3ad-120">选择左侧的 " **个人数据** " 选项卡，然后选择 " **删除** "。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-120">Select the **Personal data** tab on the left, and then select **Delete** .</span></span>

### <a name="configure-an-email-provider"></a><span data-ttu-id="ad3ad-121">配置电子邮件提供程序</span><span class="sxs-lookup"><span data-stu-id="ad3ad-121">Configure an email provider</span></span>

<span data-ttu-id="ad3ad-122">在本教程中，使用 [SendGrid](https://sendgrid.com) 发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-122">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="ad3ad-123">需要使用 SendGrid 帐户和密钥来发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-123">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="ad3ad-124">您可以使用其他电子邮件提供程序。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-124">You can use other email providers.</span></span> <span data-ttu-id="ad3ad-125">建议使用 SendGrid 或其他电子邮件服务发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-125">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="ad3ad-126">SMTP 难于保护和正确设置。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-126">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="ad3ad-127">SendGrid 帐户可能需要 [添加发送方](https://sendgrid.com/docs/ui/sending-email/senders/)。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-127">The SendGrid account may require [adding a Sender](https://sendgrid.com/docs/ui/sending-email/senders/).</span></span>

<span data-ttu-id="ad3ad-128">创建一个类以获取安全电子邮件密钥。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-128">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="ad3ad-129">对于本示例，请创建 *服务/AuthMessageSenderOptions* ：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-129">For this sample, create *Services/AuthMessageSenderOptions.cs* :</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="ad3ad-130">配置 SendGrid 用户机密</span><span class="sxs-lookup"><span data-stu-id="ad3ad-130">Configure SendGrid user secrets</span></span>

<span data-ttu-id="ad3ad-131">`SendGridUser` `SendGridKey` 用[机密管理器工具](xref:security/app-secrets)设置和。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-131">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ad3ad-132">例如： 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-132">For example:</span></span>

```dotnetcli
dotnet user-secrets set SendGridUser RickAndMSFT
dotnet user-secrets set SendGridKey <key>

Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="ad3ad-133">在 Windows 上，机密管理器将密钥/值对存储在目录中的文件 *secrets.js上* `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-133">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="ad3ad-134">不会对文件 *中secrets.js* 的内容进行加密。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-134">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="ad3ad-135">以下标记显示了文件 *上的secrets.js* 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-135">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="ad3ad-136">已 `SendGridKey` 删除该值。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-136">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="ad3ad-137">有关详细信息，请参阅 [Options 模式](xref:fundamentals/configuration/options) 和 [配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-137">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="ad3ad-138">安装 SendGrid</span><span class="sxs-lookup"><span data-stu-id="ad3ad-138">Install SendGrid</span></span>

<span data-ttu-id="ad3ad-139">本教程介绍如何通过 [SendGrid](https://sendgrid.com/)添加电子邮件通知，但你可以使用 SMTP 和其他机制发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-139">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="ad3ad-140">安装 `SendGrid` NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-140">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad3ad-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad3ad-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ad3ad-142">在 "包管理器控制台" 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-142">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="ad3ad-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ad3ad-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ad3ad-144">在控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-144">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="ad3ad-145">请参阅 [SendGrid 的入门免费](https://sendgrid.com/free/) 版，注册免费 SendGrid 帐户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-145">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="ad3ad-146">实现 IEmailSender</span><span class="sxs-lookup"><span data-stu-id="ad3ad-146">Implement IEmailSender</span></span>

<span data-ttu-id="ad3ad-147">若要实现 `IEmailSender` ，请创建具有类似于下面的代码的 *服务/EmailSender* ：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-147">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="ad3ad-148">配置启动以支持电子邮件</span><span class="sxs-lookup"><span data-stu-id="ad3ad-148">Configure startup to support email</span></span>

<span data-ttu-id="ad3ad-149">将以下代码添加到 `ConfigureServices` *Startup.cs* 文件中的方法：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-149">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="ad3ad-150">添加 `EmailSender` 为暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-150">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="ad3ad-151">注册 `AuthMessageSenderOptions` 配置实例。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-151">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/Startup.cs?name=snippet1&highlight=11-15)]

## <a name="scaffold-registerconfirmation"></a><span data-ttu-id="ad3ad-152">基架 RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="ad3ad-152">Scaffold RegisterConfirmation</span></span>

<span data-ttu-id="ad3ad-153">按照[基架 Identity ](xref:security/authentication/scaffold-identity)和基架的说明进行操作 `RegisterConfirmation` 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-153">Follow the instructions for [Scaffold Identity](xref:security/authentication/scaffold-identity) and scaffold `RegisterConfirmation`.</span></span>

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->

[!INCLUDE[](~/includes/disableVer.md)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="ad3ad-154">注册、确认电子邮件并重置密码</span><span class="sxs-lookup"><span data-stu-id="ad3ad-154">Register, confirm email, and reset password</span></span>

<span data-ttu-id="ad3ad-155">运行 web 应用，并测试帐户确认和密码恢复流。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-155">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="ad3ad-156">运行应用并注册新用户</span><span class="sxs-lookup"><span data-stu-id="ad3ad-156">Run the app and register a new user</span></span>
* <span data-ttu-id="ad3ad-157">检查电子邮件中的 "帐户确认" 链接。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-157">Check your email for the account confirmation link.</span></span> <span data-ttu-id="ad3ad-158">如果没有收到电子邮件，请参阅 [调试电子邮件](#debug) 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-158">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="ad3ad-159">单击链接以确认你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-159">Click the link to confirm your email.</span></span>
* <span data-ttu-id="ad3ad-160">用电子邮件和密码登录。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-160">Sign in with your email and password.</span></span>
* <span data-ttu-id="ad3ad-161">注销。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-161">Sign out.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="ad3ad-162">测试密码重置</span><span class="sxs-lookup"><span data-stu-id="ad3ad-162">Test password reset</span></span>

* <span data-ttu-id="ad3ad-163">如果已登录，请选择 " **注销** "。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-163">If you're signed in, select **Logout** .</span></span>
* <span data-ttu-id="ad3ad-164">选择 " **登录** " 链接，然后选择 " **忘记了密码？"** 链接。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-164">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="ad3ad-165">输入用于注册该帐户的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-165">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="ad3ad-166">发送了一封电子邮件，其中包含用于重置密码的链接。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-166">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="ad3ad-167">检查你的电子邮件，然后单击链接以重置你的密码。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-167">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="ad3ad-168">密码重置成功后，可以用电子邮件和新密码登录。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-168">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

<a name="resend"></a>

## <a name="resend-email-confirmation"></a><span data-ttu-id="ad3ad-169">重新发送电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="ad3ad-169">Resend email confirmation</span></span>

<span data-ttu-id="ad3ad-170">在 ASP.NET Core 5.0 及更高版本中，选择 " **登录** " 页上的 " **重新发送电子邮件** " 链接。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-170">In ASP.NET Core 5.0 and later, select the **Resend email confirmation** link on the **Login** page.</span></span>

### <a name="change-email-and-activity-timeout"></a><span data-ttu-id="ad3ad-171">更改电子邮件和活动超时</span><span class="sxs-lookup"><span data-stu-id="ad3ad-171">Change email and activity timeout</span></span>

<span data-ttu-id="ad3ad-172">默认的非活动超时为14天。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-172">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="ad3ad-173">下面的代码将非活动超时设置为5天：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-173">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="ad3ad-174">更改所有数据保护令牌 lifespans</span><span class="sxs-lookup"><span data-stu-id="ad3ad-174">Change all data protection token lifespans</span></span>

<span data-ttu-id="ad3ad-175">以下代码将所有数据保护令牌超时期限更改为3小时：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-175">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupAllTokens.cs?name=snippet1&highlight=11-12)]

<span data-ttu-id="ad3ad-176">内置 Identity 用户令牌 (参阅 [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) 具有一天的 [超时时间](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-176">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="ad3ad-177">更改电子邮件令牌的生命周期</span><span class="sxs-lookup"><span data-stu-id="ad3ad-177">Change the email token lifespan</span></span>

<span data-ttu-id="ad3ad-178">[ Identity 用户令牌](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)的默认令牌生存期为[1 天](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-178">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="ad3ad-179">本部分介绍如何更改电子邮件令牌的生命周期。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-179">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="ad3ad-180">添加自定义[DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)和 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> ：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-180">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="ad3ad-181">将自定义提供程序添加到服务容器：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-181">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover30/StartupEmail.cs?name=snippet1&highlight=10-16)]

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="ad3ad-182">调试电子邮件</span><span class="sxs-lookup"><span data-stu-id="ad3ad-182">Debug email</span></span>

<span data-ttu-id="ad3ad-183">如果无法使用电子邮件：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-183">If you can't get email working:</span></span>

* <span data-ttu-id="ad3ad-184">在中设置一个断点 `EmailSender.Execute` ，以验证 `SendGridClient.SendEmailAsync` 调用。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-184">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="ad3ad-185">创建一个 [控制台应用，用于](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 使用类似的代码向发送电子邮件 `EmailSender.Execute` 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-185">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="ad3ad-186">查看 [电子邮件活动](https://sendgrid.com/docs/User_Guide/email_activity.html) 页。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-186">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="ad3ad-187">检查垃圾邮件文件夹。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-187">Check your spam folder.</span></span>
* <span data-ttu-id="ad3ad-188">尝试在 Microsoft、Yahoo、Gmail 等不同的电子邮件 (提供程序上使用另一个电子邮件别名 ) </span><span class="sxs-lookup"><span data-stu-id="ad3ad-188">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="ad3ad-189">尝试发送到不同的电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-189">Try sending to different email accounts.</span></span>

<span data-ttu-id="ad3ad-190">**最佳安全做法** 是 **不** 在测试和开发中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-190">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="ad3ad-191">如果将应用发布到 Azure，请在 Azure Web 应用门户中将 "SendGrid 机密" 设置为 "应用程序设置"。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-191">If you publish the app to Azure, set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="ad3ad-192">配置系统设置为从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-192">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="ad3ad-193">合并社会和本地登录帐户</span><span class="sxs-lookup"><span data-stu-id="ad3ad-193">Combine social and local login accounts</span></span>

<span data-ttu-id="ad3ad-194">若要完成本部分，必须首先启用外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-194">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="ad3ad-195">请参阅 [Facebook、Google 和外部提供程序身份验证](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-195">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="ad3ad-196">可以通过单击电子邮件链接来合并本地帐户和社交帐户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-196">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="ad3ad-197">按照以下顺序，" RickAndMSFT@gmail.com " 首先创建为本地登录名; 但是，你可以先将该帐户创建为社交登录名，然后添加本地登录名。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-197">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web 应用程序： RickAndMSFT@gmail.com 用户已进行身份验证](accconfirm/_static/rick.png)

<span data-ttu-id="ad3ad-199">单击 " **管理** " 链接。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-199">Click on the **Manage** link.</span></span> <span data-ttu-id="ad3ad-200">请注意，0外部 (社交登录) 与此帐户关联。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-200">Note the 0 external (social logins) associated with this account.</span></span>

![管理视图](accconfirm/_static/manage.png)

<span data-ttu-id="ad3ad-202">单击指向另一登录服务的链接，并接受应用请求。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-202">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="ad3ad-203">在下图中，Facebook 是外部身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-203">In the following image, Facebook is the external authentication provider:</span></span>

![管理你的外部登录名视图列表 Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="ad3ad-205">这两个帐户已组合在一起。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-205">The two accounts have been combined.</span></span> <span data-ttu-id="ad3ad-206">你可以用任一帐户登录。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-206">You are able to sign in with either account.</span></span> <span data-ttu-id="ad3ad-207">你可能希望用户在社交登录身份验证服务关闭时添加本地帐户，或者更可能的情况是他们失去了社交帐户的访问权限。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-207">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="ad3ad-208">在站点包含用户后启用帐户确认</span><span class="sxs-lookup"><span data-stu-id="ad3ad-208">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="ad3ad-209">在具有用户的站点上启用帐户确认会锁定所有现有用户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-209">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="ad3ad-210">现有用户被锁定，因为其帐户未得到确认。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-210">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="ad3ad-211">若要解决现有用户锁定，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-211">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="ad3ad-212">更新数据库，将所有现有用户标记为已确认。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-212">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="ad3ad-213">确认现有用户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-213">Confirm existing users.</span></span> <span data-ttu-id="ad3ad-214">例如，批处理-发送包含确认链接的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-214">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

## <a name="prerequisites"></a><span data-ttu-id="ad3ad-215">先决条件</span><span class="sxs-lookup"><span data-stu-id="ad3ad-215">Prerequisites</span></span>

[<span data-ttu-id="ad3ad-216">.NET Core 2.2 SDK 或更高版本</span><span class="sxs-lookup"><span data-stu-id="ad3ad-216">.NET Core 2.2 SDK or later</span></span>](https://dotnet.microsoft.com/download/dotnet-core)

## <a name="create-a-web--app-and-scaffold-no-locidentity"></a><span data-ttu-id="ad3ad-217">创建 web 应用和基架 Identity</span><span class="sxs-lookup"><span data-stu-id="ad3ad-217">Create a web  app and scaffold Identity</span></span>

<span data-ttu-id="ad3ad-218">运行以下命令，创建具有身份验证的 web 应用。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-218">Run the following commands to create a web app with authentication.</span></span>

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

> [!NOTE]
> <span data-ttu-id="ad3ad-219">如果 <xref:Microsoft.AspNetCore.Identity.PasswordOptions> 在中配置了 `Startup.ConfigureServices` ，则基架页中的属性可能需要[ `[StringLength]` 属性](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute)配置 `Password` Identity 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-219">If <xref:Microsoft.AspNetCore.Identity.PasswordOptions> are configured in `Startup.ConfigureServices`, [`[StringLength]` attribute](xref:System.ComponentModel.DataAnnotations.StringLengthAttribute) configuration might be required for the `Password` property in scaffolded Identity pages.</span></span> <span data-ttu-id="ad3ad-220">在 `InputModel` `Password` `Areas/Identity/Pages/Account/Register.cshtml.cs` 基架后面的文件中找到属性 Identity 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-220">An `InputModel` `Password` property is found in the `Areas/Identity/Pages/Account/Register.cshtml.cs` file after scaffolding Identity.</span></span>

## <a name="test-new-user-registration"></a><span data-ttu-id="ad3ad-221">测试新用户注册</span><span class="sxs-lookup"><span data-stu-id="ad3ad-221">Test new user registration</span></span>

<span data-ttu-id="ad3ad-222">运行应用，选择 " **注册** " 链接，然后注册用户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-222">Run the app, select the **Register** link, and register a user.</span></span> <span data-ttu-id="ad3ad-223">此时，电子邮件的唯一验证是具有 [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) 属性。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-223">At this point, the only validation on the email is with the [`[EmailAddress]`](/dotnet/api/system.componentmodel.dataannotations.emailaddressattribute) attribute.</span></span> <span data-ttu-id="ad3ad-224">提交注册后，将登录到应用。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-224">After submitting the registration, you are logged into the app.</span></span> <span data-ttu-id="ad3ad-225">在本教程的后面部分，将更新代码，以便新用户在验证其电子邮件之前无法登录。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-225">Later in the tutorial, the code is updated so new users can't sign in until their email is validated.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<span data-ttu-id="ad3ad-226">请注意，表的 `EmailConfirmed` 字段为 `False` 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-226">Note the table's `EmailConfirmed` field is `False`.</span></span>

<span data-ttu-id="ad3ad-227">当应用发送确认电子邮件时，可能需要在下一步中再次使用此电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-227">You might want to use this email again in the next step when the app sends a confirmation email.</span></span> <span data-ttu-id="ad3ad-228">右键单击该行，然后选择 " **删除** "。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-228">Right-click on the row and select **Delete** .</span></span> <span data-ttu-id="ad3ad-229">删除电子邮件别名可以简化以下步骤。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-229">Deleting the email alias makes it easier in the following steps.</span></span>

<a name="prevent-login-at-registration"></a>

## <a name="require-email-confirmation"></a><span data-ttu-id="ad3ad-230">需要确认电子邮件</span><span class="sxs-lookup"><span data-stu-id="ad3ad-230">Require email confirmation</span></span>

<span data-ttu-id="ad3ad-231">最佳做法是确认新用户注册的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-231">It's a best practice to confirm the email of a new user registration.</span></span> <span data-ttu-id="ad3ad-232">电子邮件确认有助于验证他们是否未模拟其他人 (也就是说，他们尚未注册其他人的电子邮件) 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-232">Email confirmation helps to verify they're not impersonating someone else (that is, they haven't registered with someone else's email).</span></span> <span data-ttu-id="ad3ad-233">假设你有讨论论坛，并且想要阻止 " yli@example.com " 注册为 " nolivetto@contoso.com "。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-233">Suppose you had a discussion forum, and you wanted to prevent "yli@example.com" from registering as "nolivetto@contoso.com".</span></span> <span data-ttu-id="ad3ad-234">如果未确认电子邮件，" nolivetto@contoso.com " 可能会从你的应用收到不需要的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-234">Without email confirmation, "nolivetto@contoso.com" could receive unwanted email from your app.</span></span> <span data-ttu-id="ad3ad-235">假设用户意外注册为 " ylo@example.com "，但未注意到 "yli" 的拼写错误。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-235">Suppose the user accidentally registered as "ylo@example.com" and hadn't noticed the misspelling of "yli".</span></span> <span data-ttu-id="ad3ad-236">它们不能使用密码恢复，因为该应用没有正确的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-236">They wouldn't be able to use password recovery because the app doesn't have their correct email.</span></span> <span data-ttu-id="ad3ad-237">电子邮件确认为 bot 提供有限的保护。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-237">Email confirmation provides limited protection from bots.</span></span> <span data-ttu-id="ad3ad-238">电子邮件确认不会为具有多个电子邮件帐户的恶意用户提供保护。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-238">Email confirmation doesn't provide protection from malicious users with many email accounts.</span></span>

<span data-ttu-id="ad3ad-239">通常，在用户确认电子邮件之前，会阻止新用户将任何数据发布到您的网站。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-239">You generally want to prevent new users from posting any data to your web site before they have a confirmed email.</span></span>

<span data-ttu-id="ad3ad-240">更新 `Startup.ConfigureServices`  以要求确认电子邮件：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-240">Update `Startup.ConfigureServices`  to require a confirmed email:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=8-11)]

<span data-ttu-id="ad3ad-241">`config.SignIn.RequireConfirmedEmail = true;` 阻止注册的用户登录，直到其电子邮件得到确认。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-241">`config.SignIn.RequireConfirmedEmail = true;` prevents registered users from logging in until their email is confirmed.</span></span>

### <a name="configure-email-provider"></a><span data-ttu-id="ad3ad-242">配置电子邮件提供程序</span><span class="sxs-lookup"><span data-stu-id="ad3ad-242">Configure email provider</span></span>

<span data-ttu-id="ad3ad-243">在本教程中，使用 [SendGrid](https://sendgrid.com) 发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-243">In this tutorial, [SendGrid](https://sendgrid.com) is used to send email.</span></span> <span data-ttu-id="ad3ad-244">需要使用 SendGrid 帐户和密钥来发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-244">You need a SendGrid account and key to send email.</span></span> <span data-ttu-id="ad3ad-245">您可以使用其他电子邮件提供程序。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-245">You can use other email providers.</span></span> <span data-ttu-id="ad3ad-246">ASP.NET Core 1.x 包括 `System.Net.Mail` ，这允许你从应用发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-246">ASP.NET Core 2.x includes `System.Net.Mail`, which allows you to send email from your app.</span></span> <span data-ttu-id="ad3ad-247">建议使用 SendGrid 或其他电子邮件服务发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-247">We recommend you use SendGrid or another email service to send email.</span></span> <span data-ttu-id="ad3ad-248">SMTP 难于保护和正确设置。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-248">SMTP is difficult to secure and set up correctly.</span></span>

<span data-ttu-id="ad3ad-249">创建一个类以获取安全电子邮件密钥。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-249">Create a class to fetch the secure email key.</span></span> <span data-ttu-id="ad3ad-250">对于本示例，请创建 *服务/AuthMessageSenderOptions* ：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-250">For this sample, create *Services/AuthMessageSenderOptions.cs* :</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/AuthMessageSenderOptions.cs?name=snippet1)]

#### <a name="configure-sendgrid-user-secrets"></a><span data-ttu-id="ad3ad-251">配置 SendGrid 用户机密</span><span class="sxs-lookup"><span data-stu-id="ad3ad-251">Configure SendGrid user secrets</span></span>

<span data-ttu-id="ad3ad-252">`SendGridUser` `SendGridKey` 用[机密管理器工具](xref:security/app-secrets)设置和。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-252">Set the `SendGridUser` and `SendGridKey` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="ad3ad-253">例如： 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-253">For example:</span></span>

```console
C:/WebAppl>dotnet user-secrets set SendGridUser RickAndMSFT
info: Successfully saved SendGridUser = RickAndMSFT to the secret store.
```

<span data-ttu-id="ad3ad-254">在 Windows 上，机密管理器将密钥/值对存储在目录中的文件 *secrets.js上* `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-254">On Windows, Secret Manager stores keys/value pairs in a *secrets.json* file in the `%APPDATA%/Microsoft/UserSecrets/<WebAppName-userSecretsId>` directory.</span></span>

<span data-ttu-id="ad3ad-255">不会对文件 *中secrets.js* 的内容进行加密。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-255">The contents of the *secrets.json* file aren't encrypted.</span></span> <span data-ttu-id="ad3ad-256">以下标记显示了文件 *上的secrets.js* 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-256">The following markup shows the *secrets.json* file.</span></span> <span data-ttu-id="ad3ad-257">已 `SendGridKey` 删除该值。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-257">The `SendGridKey` value has been removed.</span></span>

```json
{
  "SendGridUser": "RickAndMSFT",
  "SendGridKey": "<key removed>"
}
```

<span data-ttu-id="ad3ad-258">有关详细信息，请参阅 [Options 模式](xref:fundamentals/configuration/options) 和 [配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-258">For more information, see the [Options pattern](xref:fundamentals/configuration/options) and [configuration](xref:fundamentals/configuration/index).</span></span>

### <a name="install-sendgrid"></a><span data-ttu-id="ad3ad-259">安装 SendGrid</span><span class="sxs-lookup"><span data-stu-id="ad3ad-259">Install SendGrid</span></span>

<span data-ttu-id="ad3ad-260">本教程介绍如何通过 [SendGrid](https://sendgrid.com/)添加电子邮件通知，但你可以使用 SMTP 和其他机制发送电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-260">This tutorial shows how to add email notifications through [SendGrid](https://sendgrid.com/), but you can send email using SMTP and other mechanisms.</span></span>

<span data-ttu-id="ad3ad-261">安装 `SendGrid` NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-261">Install the `SendGrid` NuGet package:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="ad3ad-262">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="ad3ad-262">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="ad3ad-263">在 "包管理器控制台" 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-263">From the Package Manager Console, enter the following command:</span></span>

```powershell
Install-Package SendGrid
```

# <a name="net-core-cli"></a>[<span data-ttu-id="ad3ad-264">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="ad3ad-264">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="ad3ad-265">在控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-265">From the console, enter the following command:</span></span>

```dotnetcli
dotnet add package SendGrid
```

---

<span data-ttu-id="ad3ad-266">请参阅 [SendGrid 的入门免费](https://sendgrid.com/free/) 版，注册免费 SendGrid 帐户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-266">See [Get Started with SendGrid for Free](https://sendgrid.com/free/) to register for a free SendGrid account.</span></span>

### <a name="implement-iemailsender"></a><span data-ttu-id="ad3ad-267">实现 IEmailSender</span><span class="sxs-lookup"><span data-stu-id="ad3ad-267">Implement IEmailSender</span></span>

<span data-ttu-id="ad3ad-268">若要实现 `IEmailSender` ，请创建具有类似于下面的代码的 *服务/EmailSender* ：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-268">To Implement `IEmailSender`, create *Services/EmailSender.cs* with code similar to the following:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Services/EmailSender.cs)]

### <a name="configure-startup-to-support-email"></a><span data-ttu-id="ad3ad-269">配置启动以支持电子邮件</span><span class="sxs-lookup"><span data-stu-id="ad3ad-269">Configure startup to support email</span></span>

<span data-ttu-id="ad3ad-270">将以下代码添加到 `ConfigureServices` *Startup.cs* 文件中的方法：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-270">Add the following code to the `ConfigureServices` method in the *Startup.cs* file:</span></span>

* <span data-ttu-id="ad3ad-271">添加 `EmailSender` 为暂时性服务。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-271">Add `EmailSender` as a transient service.</span></span>
* <span data-ttu-id="ad3ad-272">注册 `AuthMessageSenderOptions` 配置实例。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-272">Register the `AuthMessageSenderOptions` configuration instance.</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Startup.cs?name=snippet1&highlight=15-99)]

## <a name="enable-account-confirmation-and-password-recovery"></a><span data-ttu-id="ad3ad-273">启用帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="ad3ad-273">Enable account confirmation and password recovery</span></span>

<span data-ttu-id="ad3ad-274">该模板包含用于帐户确认和密码恢复的代码。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-274">The template has the code for account confirmation and password recovery.</span></span> <span data-ttu-id="ad3ad-275">`OnPostAsync`在 *区域/ Identity /Pages/Account/Register.cshtml.cs* 中查找方法。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-275">Find the `OnPostAsync` method in *Areas/Identity/Pages/Account/Register.cshtml.cs* .</span></span>

<span data-ttu-id="ad3ad-276">通过注释掉以下行，阻止新注册的用户自动登录：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-276">Prevent newly registered users from being automatically signed in by commenting out the following line:</span></span>

```csharp
await _signInManager.SignInAsync(user, isPersistent: false);
```

<span data-ttu-id="ad3ad-277">将显示完整的方法，其中突出显示了已更改的行：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-277">The complete method is shown with the changed line highlighted:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/Areas/Identity/Pages/Account/Register.cshtml.cs?highlight=22&name=snippet_Register)]

## <a name="register-confirm-email-and-reset-password"></a><span data-ttu-id="ad3ad-278">注册、确认电子邮件并重置密码</span><span class="sxs-lookup"><span data-stu-id="ad3ad-278">Register, confirm email, and reset password</span></span>

<span data-ttu-id="ad3ad-279">运行 web 应用，并测试帐户确认和密码恢复流。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-279">Run the web app, and test the account confirmation and password recovery flow.</span></span>

* <span data-ttu-id="ad3ad-280">运行应用并注册新用户</span><span class="sxs-lookup"><span data-stu-id="ad3ad-280">Run the app and register a new user</span></span>
* <span data-ttu-id="ad3ad-281">检查电子邮件中的 "帐户确认" 链接。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-281">Check your email for the account confirmation link.</span></span> <span data-ttu-id="ad3ad-282">如果没有收到电子邮件，请参阅 [调试电子邮件](#debug) 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-282">See [Debug email](#debug) if you don't get the email.</span></span>
* <span data-ttu-id="ad3ad-283">单击链接以确认你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-283">Click the link to confirm your email.</span></span>
* <span data-ttu-id="ad3ad-284">用电子邮件和密码登录。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-284">Sign in with your email and password.</span></span>
* <span data-ttu-id="ad3ad-285">注销。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-285">Sign out.</span></span>

### <a name="view-the-manage-page"></a><span data-ttu-id="ad3ad-286">查看 "管理" 页</span><span class="sxs-lookup"><span data-stu-id="ad3ad-286">View the manage page</span></span>

<span data-ttu-id="ad3ad-287">在浏览器中选择你的用户名，并在浏览器窗口中选择用户名 ![](accconfirm/_static/un.png)</span><span class="sxs-lookup"><span data-stu-id="ad3ad-287">Select your user name in the browser: ![browser window with user name](accconfirm/_static/un.png)</span></span>

<span data-ttu-id="ad3ad-288">将显示 "管理" 页，并选中 " **配置文件** " 选项卡。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-288">The manage page is displayed with the **Profile** tab selected.</span></span> <span data-ttu-id="ad3ad-289">该 **电子邮件** 将显示一个复选框，指示已确认电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-289">The **Email** shows a check box indicating the email has been confirmed.</span></span>

### <a name="test-password-reset"></a><span data-ttu-id="ad3ad-290">测试密码重置</span><span class="sxs-lookup"><span data-stu-id="ad3ad-290">Test password reset</span></span>

* <span data-ttu-id="ad3ad-291">如果已登录，请选择 " **注销** "。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-291">If you're signed in, select **Logout** .</span></span>
* <span data-ttu-id="ad3ad-292">选择 " **登录** " 链接，然后选择 " **忘记了密码？"** 链接。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-292">Select the **Log in** link and select the **Forgot your password?** link.</span></span>
* <span data-ttu-id="ad3ad-293">输入用于注册该帐户的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-293">Enter the email you used to register the account.</span></span>
* <span data-ttu-id="ad3ad-294">发送了一封电子邮件，其中包含用于重置密码的链接。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-294">An email with a link to reset your password is sent.</span></span> <span data-ttu-id="ad3ad-295">检查你的电子邮件，然后单击链接以重置你的密码。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-295">Check your email and click the link to reset your password.</span></span> <span data-ttu-id="ad3ad-296">密码重置成功后，可以用电子邮件和新密码登录。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-296">After your password has been successfully reset, you can sign in with your email and new password.</span></span>

## <a name="change-email-and-activity-timeout"></a><span data-ttu-id="ad3ad-297">更改电子邮件和活动超时</span><span class="sxs-lookup"><span data-stu-id="ad3ad-297">Change email and activity timeout</span></span>

<span data-ttu-id="ad3ad-298">默认的非活动超时为14天。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-298">The default inactivity timeout is 14 days.</span></span> <span data-ttu-id="ad3ad-299">下面的代码将非活动超时设置为5天：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-299">The following code sets the inactivity timeout to 5 days:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAppCookie.cs?name=snippet1)]

### <a name="change-all-data-protection-token-lifespans"></a><span data-ttu-id="ad3ad-300">更改所有数据保护令牌 lifespans</span><span class="sxs-lookup"><span data-stu-id="ad3ad-300">Change all data protection token lifespans</span></span>

<span data-ttu-id="ad3ad-301">以下代码将所有数据保护令牌超时期限更改为3小时：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-301">The following code changes all data protection tokens timeout period to 3 hours:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupAllTokens.cs?name=snippet1&highlight=15-16)]

<span data-ttu-id="ad3ad-302">内置 Identity 用户令牌 (参阅 [AspNetCore/src/ Identity /Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) ) 具有一天的 [超时时间](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-302">The built in Identity user tokens (see [AspNetCore/src/Identity/Extensions.Core/src/TokenOptions.cs](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) )have a [one day timeout](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span>

### <a name="change-the-email-token-lifespan"></a><span data-ttu-id="ad3ad-303">更改电子邮件令牌的生命周期</span><span class="sxs-lookup"><span data-stu-id="ad3ad-303">Change the email token lifespan</span></span>

<span data-ttu-id="ad3ad-304">[ Identity 用户令牌](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs)的默认令牌生存期为[1 天](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs)。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-304">The default token lifespan of [the Identity user tokens](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Extensions.Core/src/TokenOptions.cs) is [one day](https://github.com/dotnet/AspNetCore/blob/v2.2.2/src/Identity/Core/src/DataProtectionTokenProviderOptions.cs).</span></span> <span data-ttu-id="ad3ad-305">本部分介绍如何更改电子邮件令牌的生命周期。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-305">This section shows how to change the email token lifespan.</span></span>

<span data-ttu-id="ad3ad-306">添加自定义[DataProtectorTokenProvider \<TUser> ](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1)和 <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions> ：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-306">Add a custom [DataProtectorTokenProvider\<TUser>](/dotnet/api/microsoft.aspnetcore.identity.dataprotectortokenprovider-1) and <xref:Microsoft.AspNetCore.Identity.DataProtectionTokenProviderOptions>:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/TokenProviders/CustomTokenProvider.cs?name=snippet1)]

<span data-ttu-id="ad3ad-307">将自定义提供程序添加到服务容器：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-307">Add the custom provider to the service container:</span></span>

[!code-csharp[](accconfirm/sample/WebPWrecover22/StartupEmail.cs?name=snippet1&highlight=10-13,18)]

### <a name="resend-email-confirmation"></a><span data-ttu-id="ad3ad-308">重新发送电子邮件确认</span><span class="sxs-lookup"><span data-stu-id="ad3ad-308">Resend email confirmation</span></span>

<span data-ttu-id="ad3ad-309">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/5410)。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-309">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5410).</span></span>

<a name="debug"></a>

### <a name="debug-email"></a><span data-ttu-id="ad3ad-310">调试电子邮件</span><span class="sxs-lookup"><span data-stu-id="ad3ad-310">Debug email</span></span>

<span data-ttu-id="ad3ad-311">如果无法使用电子邮件：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-311">If you can't get email working:</span></span>

* <span data-ttu-id="ad3ad-312">在中设置一个断点 `EmailSender.Execute` ，以验证 `SendGridClient.SendEmailAsync` 调用。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-312">Set a breakpoint in `EmailSender.Execute` to verify `SendGridClient.SendEmailAsync` is called.</span></span>
* <span data-ttu-id="ad3ad-313">创建一个 [控制台应用，用于](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) 使用类似的代码向发送电子邮件 `EmailSender.Execute` 。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-313">Create a [console app to send email](https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html) using similar code to `EmailSender.Execute`.</span></span>
* <span data-ttu-id="ad3ad-314">查看 [电子邮件活动](https://sendgrid.com/docs/User_Guide/email_activity.html) 页。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-314">Review the [Email Activity](https://sendgrid.com/docs/User_Guide/email_activity.html) page.</span></span>
* <span data-ttu-id="ad3ad-315">检查垃圾邮件文件夹。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-315">Check your spam folder.</span></span>
* <span data-ttu-id="ad3ad-316">尝试在 Microsoft、Yahoo、Gmail 等不同的电子邮件 (提供程序上使用另一个电子邮件别名 ) </span><span class="sxs-lookup"><span data-stu-id="ad3ad-316">Try another email alias on a different email provider (Microsoft, Yahoo, Gmail, etc.)</span></span>
* <span data-ttu-id="ad3ad-317">尝试发送到不同的电子邮件帐户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-317">Try sending to different email accounts.</span></span>

<span data-ttu-id="ad3ad-318">**最佳安全做法** 是 **不** 在测试和开发中使用生产机密。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-318">**A security best practice** is to **not** use production secrets in test and development.</span></span> <span data-ttu-id="ad3ad-319">如果将应用发布到 Azure，则可以在 Azure Web 应用门户中将 SendGrid 机密设置为应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-319">If you publish the app to Azure, you can set the SendGrid secrets as application settings in the Azure Web App portal.</span></span> <span data-ttu-id="ad3ad-320">配置系统设置为从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-320">The configuration system is set up to read keys from environment variables.</span></span>

## <a name="combine-social-and-local-login-accounts"></a><span data-ttu-id="ad3ad-321">合并社会和本地登录帐户</span><span class="sxs-lookup"><span data-stu-id="ad3ad-321">Combine social and local login accounts</span></span>

<span data-ttu-id="ad3ad-322">若要完成本部分，必须首先启用外部身份验证提供程序。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-322">To complete this section, you must first enable an external authentication provider.</span></span> <span data-ttu-id="ad3ad-323">请参阅 [Facebook、Google 和外部提供程序身份验证](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-323">See [Facebook, Google, and external provider authentication](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="ad3ad-324">可以通过单击电子邮件链接来合并本地帐户和社交帐户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-324">You can combine local and social accounts by clicking on your email link.</span></span> <span data-ttu-id="ad3ad-325">按照以下顺序，" RickAndMSFT@gmail.com " 首先创建为本地登录名; 但是，你可以先将该帐户创建为社交登录名，然后添加本地登录名。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-325">In the following sequence, "RickAndMSFT@gmail.com" is first created as a local login; however, you can create the account as a social login first, then add a local login.</span></span>

![Web 应用程序： RickAndMSFT@gmail.com 用户已进行身份验证](accconfirm/_static/rick.png)

<span data-ttu-id="ad3ad-327">单击 " **管理** " 链接。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-327">Click on the **Manage** link.</span></span> <span data-ttu-id="ad3ad-328">请注意，0外部 (社交登录) 与此帐户关联。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-328">Note the 0 external (social logins) associated with this account.</span></span>

![管理视图](accconfirm/_static/manage.png)

<span data-ttu-id="ad3ad-330">单击指向另一登录服务的链接，并接受应用请求。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-330">Click the link to another login service and accept the app requests.</span></span> <span data-ttu-id="ad3ad-331">在下图中，Facebook 是外部身份验证提供程序：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-331">In the following image, Facebook is the external authentication provider:</span></span>

![管理你的外部登录名视图列表 Facebook](accconfirm/_static/fb.png)

<span data-ttu-id="ad3ad-333">这两个帐户已组合在一起。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-333">The two accounts have been combined.</span></span> <span data-ttu-id="ad3ad-334">你可以用任一帐户登录。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-334">You are able to sign in with either account.</span></span> <span data-ttu-id="ad3ad-335">你可能希望用户在社交登录身份验证服务关闭时添加本地帐户，或者更可能的情况是他们失去了社交帐户的访问权限。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-335">You might want your users to add local accounts in case their social login authentication service is down, or more likely they've lost access to their social account.</span></span>

## <a name="enable-account-confirmation-after-a-site-has-users"></a><span data-ttu-id="ad3ad-336">在站点包含用户后启用帐户确认</span><span class="sxs-lookup"><span data-stu-id="ad3ad-336">Enable account confirmation after a site has users</span></span>

<span data-ttu-id="ad3ad-337">在具有用户的站点上启用帐户确认会锁定所有现有用户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-337">Enabling account confirmation on a site with users locks out all the existing users.</span></span> <span data-ttu-id="ad3ad-338">现有用户被锁定，因为其帐户未得到确认。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-338">Existing users are locked out because their accounts aren't confirmed.</span></span> <span data-ttu-id="ad3ad-339">若要解决现有用户锁定，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="ad3ad-339">To work around existing user lockout, use one of the following approaches:</span></span>

* <span data-ttu-id="ad3ad-340">更新数据库，将所有现有用户标记为已确认。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-340">Update the database to mark all existing users as being confirmed.</span></span>
* <span data-ttu-id="ad3ad-341">确认现有用户。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-341">Confirm existing users.</span></span> <span data-ttu-id="ad3ad-342">例如，批处理-发送包含确认链接的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ad3ad-342">For example, batch-send emails with confirmation links.</span></span>

::: moniker-end
