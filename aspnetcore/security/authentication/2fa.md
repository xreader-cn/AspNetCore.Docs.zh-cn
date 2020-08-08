---
title: 在 ASP.NET Core 中通过 SMS 进行双因素身份验证
author: rick-anderson
description: 了解如何使用 ASP.NET Core 应用程序 (2FA) 设置双因素身份验证。
monikerRange: < aspnetcore-2.0
ms.author: riande
ms.date: 09/22/2018
ms.custom: mvc, seodec18
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/2fa
ms.openlocfilehash: 28aef65234eaf162ba6e18a2594feb575c93b02f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88019480"
---
# <a name="two-factor-authentication-with-sms-in-aspnet-core"></a><span data-ttu-id="66f1b-103">在 ASP.NET Core 中通过 SMS 进行双因素身份验证</span><span class="sxs-lookup"><span data-stu-id="66f1b-103">Two-factor authentication with SMS in ASP.NET Core</span></span>

<span data-ttu-id="66f1b-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)和[瑞士-开发人员](https://github.com/Swiss-Devs)</span><span class="sxs-lookup"><span data-stu-id="66f1b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Swiss-Devs](https://github.com/Swiss-Devs)</span></span>

>[!WARNING]
> <span data-ttu-id="66f1b-105">双因素身份验证 (2FA) 验证器应用，使用基于时间的一次性密码算法 (TOTP) ，是2FA 的行业建议方法。</span><span class="sxs-lookup"><span data-stu-id="66f1b-105">Two factor authentication (2FA) authenticator apps, using a Time-based One-time Password Algorithm (TOTP), are the industry recommended approach for 2FA.</span></span> <span data-ttu-id="66f1b-106">2FA 使用 TOTP 是 SMS 2FA 的首选。</span><span class="sxs-lookup"><span data-stu-id="66f1b-106">2FA using TOTP is preferred to SMS 2FA.</span></span> <span data-ttu-id="66f1b-107">有关详细信息，请参阅为 ASP.NET Core 2.0 及更高版本[ASP.NET Core 中的 TOTP 验证器应用启用 QR 码生成](xref:security/authentication/identity-enable-qrcodes)。</span><span class="sxs-lookup"><span data-stu-id="66f1b-107">For more information, see [Enable QR Code generation for TOTP authenticator apps in ASP.NET Core](xref:security/authentication/identity-enable-qrcodes) for ASP.NET Core 2.0 and later.</span></span>

<span data-ttu-id="66f1b-108">本教程演示如何使用短信 (2FA) 设置双因素身份验证。</span><span class="sxs-lookup"><span data-stu-id="66f1b-108">This tutorial shows how to set up two-factor authentication (2FA) using SMS.</span></span> <span data-ttu-id="66f1b-109">说明适用于[twilio](https://www.twilio.com/)和[ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)，但你可以使用任何其他 SMS 提供程序。</span><span class="sxs-lookup"><span data-stu-id="66f1b-109">Instructions are given for [twilio](https://www.twilio.com/) and [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/), but you can use any other SMS provider.</span></span> <span data-ttu-id="66f1b-110">建议你在开始学习本教程之前完成[帐户确认和密码恢复](xref:security/authentication/accconfirm)。</span><span class="sxs-lookup"><span data-stu-id="66f1b-110">We recommend you complete [Account Confirmation and Password Recovery](xref:security/authentication/accconfirm) before starting this tutorial.</span></span>

<span data-ttu-id="66f1b-111">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA)。</span><span class="sxs-lookup"><span data-stu-id="66f1b-111">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/2fa/sample/Web2FA).</span></span> <span data-ttu-id="66f1b-112">[如何下载](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="66f1b-112">[How to download](xref:index#how-to-download-a-sample).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="66f1b-113">创建新的 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="66f1b-113">Create a new ASP.NET Core project</span></span>

<span data-ttu-id="66f1b-114">使用单个用户帐户创建名为的新 ASP.NET Core web 应用 `Web2FA` 。</span><span class="sxs-lookup"><span data-stu-id="66f1b-114">Create a new ASP.NET Core web app named `Web2FA` with individual user accounts.</span></span> <span data-ttu-id="66f1b-115">按照中的说明 <xref:security/enforcing-ssl> 进行操作，设置并要求 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="66f1b-115">Follow the instructions in <xref:security/enforcing-ssl> to set up and require HTTPS.</span></span>

### <a name="create-an-sms-account"></a><span data-ttu-id="66f1b-116">创建 SMS 帐户</span><span class="sxs-lookup"><span data-stu-id="66f1b-116">Create an SMS account</span></span>

<span data-ttu-id="66f1b-117">创建一个 SMS 帐户，例如[twilio](https://www.twilio.com/)或[ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/)。</span><span class="sxs-lookup"><span data-stu-id="66f1b-117">Create an SMS account, for example, from [twilio](https://www.twilio.com/) or [ASPSMS](https://www.aspsms.com/asp.net/identity/core/testcredits/).</span></span> <span data-ttu-id="66f1b-118">为 ASPSMS：用户密钥和 Password) 记录 twilio： accountSid 和 authToken (的身份验证凭据。</span><span class="sxs-lookup"><span data-stu-id="66f1b-118">Record the authentication credentials (for twilio: accountSid and authToken, for ASPSMS: Userkey and Password).</span></span>

#### <a name="figuring-out-sms-provider-credentials"></a><span data-ttu-id="66f1b-119">了解 SMS 提供程序凭据</span><span class="sxs-lookup"><span data-stu-id="66f1b-119">Figuring out SMS Provider credentials</span></span>

<span data-ttu-id="66f1b-120">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="66f1b-120">**Twilio:**</span></span>

<span data-ttu-id="66f1b-121">在 Twilio 帐户的 "仪表板" 选项卡中，复制 "**帐户 SID** " 和 "**身份验证令牌**"。</span><span class="sxs-lookup"><span data-stu-id="66f1b-121">From the Dashboard tab of your Twilio account, copy the **Account SID** and **Auth token**.</span></span>

<span data-ttu-id="66f1b-122">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="66f1b-122">**ASPSMS:**</span></span>

<span data-ttu-id="66f1b-123">从帐户设置中，导航到**用户密钥**，并将其与**密码**一起复制。</span><span class="sxs-lookup"><span data-stu-id="66f1b-123">From your account settings, navigate to **Userkey** and copy it together with your **Password**.</span></span>

<span data-ttu-id="66f1b-124">稍后，我们会将这些值存储在中，并在密钥和中存储机密管理器工具 `SMSAccountIdentification` `SMSAccountPassword` 。</span><span class="sxs-lookup"><span data-stu-id="66f1b-124">We will later store these values in with the secret-manager tool within the keys `SMSAccountIdentification` and `SMSAccountPassword`.</span></span>

#### <a name="specifying-senderid--originator"></a><span data-ttu-id="66f1b-125">指定 SenderID/发起方</span><span class="sxs-lookup"><span data-stu-id="66f1b-125">Specifying SenderID / Originator</span></span>

<span data-ttu-id="66f1b-126">**Twilio：** 从 "数字" 选项卡中，复制 Twilio 的**电话号码**。</span><span class="sxs-lookup"><span data-stu-id="66f1b-126">**Twilio:** From the Numbers tab, copy your Twilio **phone number**.</span></span>

<span data-ttu-id="66f1b-127">**ASPSMS：** 在 "解锁始发者" 菜单中，解锁一个或多个发信方，或者选择 "所有网络) 不支持 (的字母数字发信方"。</span><span class="sxs-lookup"><span data-stu-id="66f1b-127">**ASPSMS:** Within the Unlock Originators Menu, unlock one or more Originators or choose an alphanumeric Originator (Not supported by all networks).</span></span>

<span data-ttu-id="66f1b-128">稍后我们将在密钥中将此值与机密管理器工具存储在一起 `SMSAccountFrom` 。</span><span class="sxs-lookup"><span data-stu-id="66f1b-128">We will later store this value with the secret-manager tool within the key `SMSAccountFrom`.</span></span>

### <a name="provide-credentials-for-the-sms-service"></a><span data-ttu-id="66f1b-129">提供 SMS 服务的凭据</span><span class="sxs-lookup"><span data-stu-id="66f1b-129">Provide credentials for the SMS service</span></span>

<span data-ttu-id="66f1b-130">我们将使用[Options 模式](xref:fundamentals/configuration/options)来访问用户帐户和密钥设置。</span><span class="sxs-lookup"><span data-stu-id="66f1b-130">We'll use the [Options pattern](xref:fundamentals/configuration/options) to access the user account and key settings.</span></span>

* <span data-ttu-id="66f1b-131">创建一个类以提取安全短信密钥。</span><span class="sxs-lookup"><span data-stu-id="66f1b-131">Create a class to fetch the secure SMS key.</span></span> <span data-ttu-id="66f1b-132">对于本示例， `SMSoptions` 类在*服务/SMSoptions*文件中创建。</span><span class="sxs-lookup"><span data-stu-id="66f1b-132">For this sample, the `SMSoptions` class is created in the *Services/SMSoptions.cs* file.</span></span>

[!code-csharp[](2fa/sample/Web2FA/Services/SMSoptions.cs)]

<span data-ttu-id="66f1b-133">`SMSAccountIdentification` `SMSAccountPassword` `SMSAccountFrom` 用[机密管理器工具](xref:security/app-secrets)设置和。</span><span class="sxs-lookup"><span data-stu-id="66f1b-133">Set the `SMSAccountIdentification`, `SMSAccountPassword` and `SMSAccountFrom` with the [secret-manager tool](xref:security/app-secrets).</span></span> <span data-ttu-id="66f1b-134">例如：</span><span class="sxs-lookup"><span data-stu-id="66f1b-134">For example:</span></span>

```none
C:/Web2FA/src/WebApp1>dotnet user-secrets set SMSAccountIdentification 12345
info: Successfully saved SMSAccountIdentification = 12345 to the secret store.
```

* <span data-ttu-id="66f1b-135">为 SMS 提供程序添加 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="66f1b-135">Add the NuGet package for the SMS provider.</span></span> <span data-ttu-id="66f1b-136">从包管理器控制台中 (PMC) 运行：</span><span class="sxs-lookup"><span data-stu-id="66f1b-136">From the Package Manager Console (PMC) run:</span></span>

<span data-ttu-id="66f1b-137">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="66f1b-137">**Twilio:**</span></span>

`Install-Package Twilio`

<span data-ttu-id="66f1b-138">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="66f1b-138">**ASPSMS:**</span></span>

`Install-Package ASPSMS`

* <span data-ttu-id="66f1b-139">添加*服务/MessageServices*文件中的代码以启用 SMS。</span><span class="sxs-lookup"><span data-stu-id="66f1b-139">Add code in the *Services/MessageServices.cs* file to enable SMS.</span></span> <span data-ttu-id="66f1b-140">使用 Twilio 或 ASPSMS 部分：</span><span class="sxs-lookup"><span data-stu-id="66f1b-140">Use either the Twilio or the ASPSMS section:</span></span>

<span data-ttu-id="66f1b-141">**Twilio**</span><span class="sxs-lookup"><span data-stu-id="66f1b-141">**Twilio:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_twilio.cs)]

<span data-ttu-id="66f1b-142">**ASPSMS:**</span><span class="sxs-lookup"><span data-stu-id="66f1b-142">**ASPSMS:**</span></span>  
[!code-csharp[](2fa/sample/Web2FA/Services/MessageServices_ASPSMS.cs)]

### <a name="configure-startup-to-use-smsoptions"></a><span data-ttu-id="66f1b-143">配置要使用的启动`SMSoptions`</span><span class="sxs-lookup"><span data-stu-id="66f1b-143">Configure startup to use `SMSoptions`</span></span>

<span data-ttu-id="66f1b-144">将添加到 Startup.cs 中的方法中的 `SMSoptions` 服务容器 `ConfigureServices` ： *Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="66f1b-144">Add `SMSoptions` to the service container in the `ConfigureServices` method in the *Startup.cs*:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet1&highlight=4)]

### <a name="enable-two-factor-authentication"></a><span data-ttu-id="66f1b-145">启用双因素身份验证</span><span class="sxs-lookup"><span data-stu-id="66f1b-145">Enable two-factor authentication</span></span>

<span data-ttu-id="66f1b-146">打开*Views/管理/索引* Razor 视图文件并删除注释字符 (因此不) 注释掉标记。</span><span class="sxs-lookup"><span data-stu-id="66f1b-146">Open the *Views/Manage/Index.cshtml* Razor view file and remove the comment characters (so no markup is commented out).</span></span>

## <a name="log-in-with-two-factor-authentication"></a><span data-ttu-id="66f1b-147">用双因素身份验证登录</span><span class="sxs-lookup"><span data-stu-id="66f1b-147">Log in with two-factor authentication</span></span>

* <span data-ttu-id="66f1b-148">运行应用并注册新用户</span><span class="sxs-lookup"><span data-stu-id="66f1b-148">Run the app and register a new user</span></span>

![在 Microsoft Edge 中打开的 Web 应用程序注册视图](2fa/_static/login2fa1.png)

* <span data-ttu-id="66f1b-150">点击你的用户名，该名称将激活 `Index` 管理控制器中的操作方法。</span><span class="sxs-lookup"><span data-stu-id="66f1b-150">Tap on your user name, which activates the `Index` action method in Manage controller.</span></span> <span data-ttu-id="66f1b-151">然后点击 "电话号码" "**添加**" 链接。</span><span class="sxs-lookup"><span data-stu-id="66f1b-151">Then tap the phone number **Add** link.</span></span>

![管理视图-点击 "添加" 链接](2fa/_static/login2fa2.png)

* <span data-ttu-id="66f1b-153">添加将接收验证码的电话号码，然后点击 "**发送验证码**"。</span><span class="sxs-lookup"><span data-stu-id="66f1b-153">Add a phone number that will receive the verification code, and tap **Send verification code**.</span></span>

![添加电话号码页面](2fa/_static/login2fa3.png)

* <span data-ttu-id="66f1b-155">你将收到一条包含验证码的短信。</span><span class="sxs-lookup"><span data-stu-id="66f1b-155">You will get a text message with the verification code.</span></span> <span data-ttu-id="66f1b-156">输入它，然后点击 "**提交**"</span><span class="sxs-lookup"><span data-stu-id="66f1b-156">Enter it and tap **Submit**</span></span>

![验证电话号码页面](2fa/_static/login2fa4.png)

<span data-ttu-id="66f1b-158">如果未收到短信，请参阅 twilio log page。</span><span class="sxs-lookup"><span data-stu-id="66f1b-158">If you don't get a text message, see twilio log page.</span></span>

* <span data-ttu-id="66f1b-159">"管理" 视图显示已成功添加的电话号码。</span><span class="sxs-lookup"><span data-stu-id="66f1b-159">The Manage view shows your phone number was added successfully.</span></span>

![管理视图-电话号码已成功添加](2fa/_static/login2fa5.png)

* <span data-ttu-id="66f1b-161">点击 "**启用**" 以启用双因素身份验证。</span><span class="sxs-lookup"><span data-stu-id="66f1b-161">Tap **Enable** to enable two-factor authentication.</span></span>

![管理视图-启用双因素身份验证](2fa/_static/login2fa6.png)

### <a name="test-two-factor-authentication"></a><span data-ttu-id="66f1b-163">测试双因素身份验证</span><span class="sxs-lookup"><span data-stu-id="66f1b-163">Test two-factor authentication</span></span>

* <span data-ttu-id="66f1b-164">注销。</span><span class="sxs-lookup"><span data-stu-id="66f1b-164">Log off.</span></span>

* <span data-ttu-id="66f1b-165">登录。</span><span class="sxs-lookup"><span data-stu-id="66f1b-165">Log in.</span></span>

* <span data-ttu-id="66f1b-166">用户帐户已启用双因素身份验证，因此你必须提供第二个身份验证因素。</span><span class="sxs-lookup"><span data-stu-id="66f1b-166">The user account has enabled two-factor authentication, so you have to provide the second factor of authentication .</span></span> <span data-ttu-id="66f1b-167">在本教程中，你已启用电话验证。</span><span class="sxs-lookup"><span data-stu-id="66f1b-167">In this tutorial you have enabled phone verification.</span></span> <span data-ttu-id="66f1b-168">内置模板还允许您将电子邮件设置为第二个因素。</span><span class="sxs-lookup"><span data-stu-id="66f1b-168">The built in templates also allow you to set up email as the second factor.</span></span> <span data-ttu-id="66f1b-169">你可以设置其他第二种身份验证因素，例如 QR 码。</span><span class="sxs-lookup"><span data-stu-id="66f1b-169">You can set up additional second factors for authentication such as QR codes.</span></span> <span data-ttu-id="66f1b-170">点击 "**提交**"。</span><span class="sxs-lookup"><span data-stu-id="66f1b-170">Tap **Submit**.</span></span>

![发送验证代码视图](2fa/_static/login2fa7.png)

* <span data-ttu-id="66f1b-172">输入在 SMS 消息中获取的代码。</span><span class="sxs-lookup"><span data-stu-id="66f1b-172">Enter the code you get in the SMS message.</span></span>

* <span data-ttu-id="66f1b-173">单击 "**记住此浏览器**" 复选框会使你不需要使用2FA 在使用同一设备和浏览器时进行登录。</span><span class="sxs-lookup"><span data-stu-id="66f1b-173">Clicking on the **Remember this browser** check box will exempt you from needing to use 2FA to log on when using the same device and browser.</span></span> <span data-ttu-id="66f1b-174">如果启用2FA 并单击 "记住"，**此浏览器**将为你提供强大的2FA 防护，防止恶意用户尝试访问你的帐户，只要他们无权访问你的设备。</span><span class="sxs-lookup"><span data-stu-id="66f1b-174">Enabling 2FA and clicking on **Remember this browser** will provide you with strong 2FA protection from malicious users trying to access your account, as long as they don't have access to your device.</span></span> <span data-ttu-id="66f1b-175">你可以在你经常使用的任何专用设备上执行此操作。</span><span class="sxs-lookup"><span data-stu-id="66f1b-175">You can do this on any private device you regularly use.</span></span> <span data-ttu-id="66f1b-176">通过设置**记住此浏览器**，你可以从不经常使用的设备中获得2FA 的增强的安全性，并且你无需在自己的设备上通过2FA。</span><span class="sxs-lookup"><span data-stu-id="66f1b-176">By setting  **Remember this browser**, you get the added security of 2FA from devices you don't regularly use, and you get the convenience on not having to go through 2FA on your own devices.</span></span>

![验证视图](2fa/_static/login2fa8.png)

## <a name="account-lockout-for-protecting-against-brute-force-attacks"></a><span data-ttu-id="66f1b-178">帐户锁定，防止暴力破解攻击</span><span class="sxs-lookup"><span data-stu-id="66f1b-178">Account lockout for protecting against brute force attacks</span></span>

<span data-ttu-id="66f1b-179">建议使用2FA 帐户锁定。</span><span class="sxs-lookup"><span data-stu-id="66f1b-179">Account lockout is recommended with 2FA.</span></span> <span data-ttu-id="66f1b-180">用户通过本地帐户或社交帐户登录后，将存储2FA 的每次失败尝试。</span><span class="sxs-lookup"><span data-stu-id="66f1b-180">Once a user signs in through a local account or social account, each failed attempt at 2FA is stored.</span></span> <span data-ttu-id="66f1b-181">如果达到了失败的最大访问尝试次数，则用户将被锁定 (默认值：5分钟在尝试访问尝试失败5次失败后锁定) 。</span><span class="sxs-lookup"><span data-stu-id="66f1b-181">If the maximum failed access attempts is reached, the user is locked out (default: 5 minute lockout after 5 failed access attempts).</span></span> <span data-ttu-id="66f1b-182">身份验证成功后，将重置失败的访问尝试计数并重置时钟。</span><span class="sxs-lookup"><span data-stu-id="66f1b-182">A successful authentication resets the failed access attempts count and resets the clock.</span></span> <span data-ttu-id="66f1b-183">可通过[MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts)和[DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan)设置最大失败访问尝试和锁定时间。</span><span class="sxs-lookup"><span data-stu-id="66f1b-183">The maximum failed access attempts and lockout time can be set with [MaxFailedAccessAttempts](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.maxfailedaccessattempts) and [DefaultLockoutTimeSpan](/dotnet/api/microsoft.aspnetcore.identity.lockoutoptions.defaultlockouttimespan).</span></span> <span data-ttu-id="66f1b-184">以下内容将帐户锁定配置为10分钟后失败的访问尝试次数：</span><span class="sxs-lookup"><span data-stu-id="66f1b-184">The following configures account lockout for 10 minutes after 10 failed access attempts:</span></span>

[!code-csharp[](2fa/sample/Web2FA/Startup.cs?name=snippet2&highlight=13-17)]

<span data-ttu-id="66f1b-185">确认[PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync)设置 `lockoutOnFailure` 为 `true` ：</span><span class="sxs-lookup"><span data-stu-id="66f1b-185">Confirm that [PasswordSignInAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.passwordsigninasync) sets `lockoutOnFailure` to `true`:</span></span>

```csharp
var result = await _signInManager.PasswordSignInAsync(
                 Input.Email, Input.Password, Input.RememberMe, lockoutOnFailure: true);
```
