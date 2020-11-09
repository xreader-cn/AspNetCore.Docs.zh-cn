---
title: 具有 ASP.NET Core 的 Twitter 外部登录设置
author: rick-anderson
description: 本教程演示如何将 Twitter 帐户用户身份验证集成到现有 ASP.NET Core 应用。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
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
uid: security/authentication/twitter-logins
ms.openlocfilehash: 47926d12ac5f922f2937df164d38ff6eb63cacf1
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053275"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="2ae85-103">具有 ASP.NET Core 的 Twitter 外部登录设置</span><span class="sxs-lookup"><span data-stu-id="2ae85-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="2ae85-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2ae85-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2ae85-105">此示例演示如何使用户能够使用在[前一页](xref:security/authentication/social/index)上创建的示例 ASP.NET Core 3.0 项目[登录其 Twitter 帐户](https://dev.twitter.com/web/sign-in/desktop-browser)。</span><span class="sxs-lookup"><span data-stu-id="2ae85-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="2ae85-106">在 Twitter 中创建应用</span><span class="sxs-lookup"><span data-stu-id="2ae85-106">Create the app in Twitter</span></span>

* <span data-ttu-id="2ae85-107">将 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="2ae85-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="2ae85-108">导航到 [https://apps.twitter.com/](https://apps.twitter.com/) 并登录。</span><span class="sxs-lookup"><span data-stu-id="2ae85-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="2ae85-109">如果还没有 Twitter 帐户，请使用 " **[立即注册](https://twitter.com/signup)** " 链接创建一个。</span><span class="sxs-lookup"><span data-stu-id="2ae85-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="2ae85-110">选择 " **创建应用** "。</span><span class="sxs-lookup"><span data-stu-id="2ae85-110">Select **Create an app** .</span></span> <span data-ttu-id="2ae85-111">填写 "应用 **名称** "、" **应用程序说明** " 和 "公共 **网站** URI" (这在您注册域名) 之前是暂时的：</span><span class="sxs-lookup"><span data-stu-id="2ae85-111">Fill out the **App name** , **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="2ae85-112">选中 " **启用使用 Twitter 登录** " 旁边的框</span><span class="sxs-lookup"><span data-stu-id="2ae85-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="2ae85-113">AspNetCore。Identity</span><span class="sxs-lookup"><span data-stu-id="2ae85-113">Microsoft.AspNetCore.Identity</span></span> <span data-ttu-id="2ae85-114">要求用户在默认情况下具有电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="2ae85-114">requires users to have an email address by default.</span></span> <span data-ttu-id="2ae85-115">中转到 " **权限** " 选项卡，单击 " **编辑** " 按钮，然后选中 " **请求用户的电子邮件地址** " 旁边的框。</span><span class="sxs-lookup"><span data-stu-id="2ae85-115">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users** .</span></span>

* <span data-ttu-id="2ae85-116">输入 `/signin-twitter` 附加到 " **回调 url** " 字段中的开发 URI (例如： `https://webapp128.azurewebsites.net/signin-twitter`) 。</span><span class="sxs-lookup"><span data-stu-id="2ae85-116">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="2ae85-117">稍后在本示例中配置的 Twitter 身份验证方案将自动处理路由中的请求 `/signin-twitter` 以实现 OAuth 流。</span><span class="sxs-lookup"><span data-stu-id="2ae85-117">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="2ae85-118">URI 段 `/signin-twitter` 设置为 Twitter 身份验证提供程序的默认回调。</span><span class="sxs-lookup"><span data-stu-id="2ae85-118">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="2ae85-119">通过[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Twitter 身份验证中间件时，可以更改默认的回叫 URI。</span><span class="sxs-lookup"><span data-stu-id="2ae85-119">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="2ae85-120">填写表单的其余部分，然后选择 " **创建** "。</span><span class="sxs-lookup"><span data-stu-id="2ae85-120">Fill out the rest of the form and select **Create** .</span></span> <span data-ttu-id="2ae85-121">显示新应用程序详细信息：</span><span class="sxs-lookup"><span data-stu-id="2ae85-121">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="2ae85-122">存储 Twitter 使用者 API 密钥和机密</span><span class="sxs-lookup"><span data-stu-id="2ae85-122">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="2ae85-123">使用 [机密管理器](xref:security/app-secrets)存储敏感设置，如 Twitter 使用者 API 密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="2ae85-123">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="2ae85-124">对于本示例，请使用以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2ae85-124">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="2ae85-125">按照 [启用密钥存储](xref:security/app-secrets#enable-secret-storage)中的说明初始化密钥存储的项目。</span><span class="sxs-lookup"><span data-stu-id="2ae85-125">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="2ae85-126">将敏感设置存储在本地密钥存储中，并提供机密密钥 `Authentication:Twitter:ConsumerKey` 和 `Authentication:Twitter:ConsumerSecret` ：</span><span class="sxs-lookup"><span data-stu-id="2ae85-126">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="2ae85-127">创建新的 Twitter 应用程序后，可以在 " **密钥和访问令牌** " 选项卡上找到这些令牌：</span><span class="sxs-lookup"><span data-stu-id="2ae85-127">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="2ae85-128">配置 Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="2ae85-128">Configure Twitter Authentication</span></span>

<span data-ttu-id="2ae85-129">将 Twitter 服务添加到 `ConfigureServices` *Startup.cs* 文件的方法中：</span><span class="sxs-lookup"><span data-stu-id="2ae85-129">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="2ae85-130">有关 Twitter 身份验证支持的配置选项的详细信息，请参阅 [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API 参考。</span><span class="sxs-lookup"><span data-stu-id="2ae85-130">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="2ae85-131">这可用于请求有关用户的其他信息。</span><span class="sxs-lookup"><span data-stu-id="2ae85-131">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="2ae85-132">通过 Twitter 登录</span><span class="sxs-lookup"><span data-stu-id="2ae85-132">Sign in with Twitter</span></span>

<span data-ttu-id="2ae85-133">运行应用并选择 **"登录"** 。</span><span class="sxs-lookup"><span data-stu-id="2ae85-133">Run the app and select **Log in** .</span></span> <span data-ttu-id="2ae85-134">将显示使用 Twitter 登录的选项：</span><span class="sxs-lookup"><span data-stu-id="2ae85-134">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="2ae85-135">单击 **twitter** 重定向到 twitter 进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="2ae85-135">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="2ae85-136">输入 Twitter 凭据后，会重定向回到网站，你可以在其中设置电子邮件。</span><span class="sxs-lookup"><span data-stu-id="2ae85-136">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="2ae85-137">你现在已使用 Twitter 凭据登录：</span><span class="sxs-lookup"><span data-stu-id="2ae85-137">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

<!-- 
### React to cancel Authorize External sign-in
Twitter doesn't support AccessDeniedPath
Rather in the twitter setup, you can provide an External sign-in homepage. The external sign-in homepage doesn't support localhost. Tested with https://cors3.azurewebsites.net/ and that works.
-->

## <a name="troubleshooting"></a><span data-ttu-id="2ae85-138">疑难解答</span><span class="sxs-lookup"><span data-stu-id="2ae85-138">Troubleshooting</span></span>

* <span data-ttu-id="2ae85-139">**仅 ASP.NET Core 2.x：** 如果 Identity 未通过调用进行 `services.AddIdentity` 配置 `ConfigureServices` ，尝试进行身份验证会导致 *ArgumentException：必须提供 "SignInScheme" 选项* 。</span><span class="sxs-lookup"><span data-stu-id="2ae85-139">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="2ae85-140">本示例中使用的项目模板可确保完成此操作。</span><span class="sxs-lookup"><span data-stu-id="2ae85-140">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="2ae85-141">如果尚未通过应用初始迁移来创建站点数据库，则在处理请求错误时将会获得 *数据库操作失败* 。</span><span class="sxs-lookup"><span data-stu-id="2ae85-141">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="2ae85-142">点击 " **应用迁移** " 以创建数据库，然后单击 "刷新" 以继续出现错误。</span><span class="sxs-lookup"><span data-stu-id="2ae85-142">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2ae85-143">后续步骤</span><span class="sxs-lookup"><span data-stu-id="2ae85-143">Next steps</span></span>

* <span data-ttu-id="2ae85-144">本文介绍了如何通过 Twitter 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="2ae85-144">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="2ae85-145">您可以遵循类似的方法向 [前一页](xref:security/authentication/social/index)上列出的其他提供程序进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="2ae85-145">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="2ae85-146">将网站发布到 Azure web 应用后，应 `ConsumerSecret` 在 Twitter 开发人员门户中重置。</span><span class="sxs-lookup"><span data-stu-id="2ae85-146">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="2ae85-147">`Authentication:Twitter:ConsumerKey` `Authentication:Twitter:ConsumerSecret` 在 Azure 门户中将和设置为应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="2ae85-147">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="2ae85-148">配置系统设置为从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="2ae85-148">The configuration system is set up to read keys from environment variables.</span></span>
