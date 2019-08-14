---
title: 具有 ASP.NET Core 的 Twitter 外部登录设置
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用的 Twitter 帐户用户身份验证。
ms.author: riande
ms.custom: mvc
ms.date: 05/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 6b6fa3e50f602a92fec9112ac3ba43583de33a70
ms.sourcegitcommit: 89fcc6cb3e12790dca2b8b62f86609bed6335be9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2019
ms.locfileid: "68994282"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="927ad-103">具有 ASP.NET Core 的 Twitter 外部登录设置</span><span class="sxs-lookup"><span data-stu-id="927ad-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="927ad-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="927ad-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="927ad-105">此示例演示如何使用户能够使用在[前一页](xref:security/authentication/social/index)上创建的示例 ASP.NET Core 2.2 项目[登录其 Twitter 帐户](https://dev.twitter.com/web/sign-in/desktop-browser)。</span><span class="sxs-lookup"><span data-stu-id="927ad-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="927ad-106">在 Twitter 中创建应用</span><span class="sxs-lookup"><span data-stu-id="927ad-106">Create the app in Twitter</span></span>

* <span data-ttu-id="927ad-107">导航到[ https://apps.twitter.com/ ](https://apps.twitter.com/)并登录。</span><span class="sxs-lookup"><span data-stu-id="927ad-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="927ad-108">如果还没有 Twitter 帐户，使用 **[立即注册](https://twitter.com/signup)** 链接创建一个链接。</span><span class="sxs-lookup"><span data-stu-id="927ad-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="927ad-109">点击 "**创建新应用**", 并填写应用程序**名称**、**说明**和公共**网站**URI (在注册域名之前, 这可能是暂时的):</span><span class="sxs-lookup"><span data-stu-id="927ad-109">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="927ad-110">输入`/signin-twitter`附加到 "**有效的 OAuth 重定向 uri** " 字段中的开发 URI ( `https://webapp128.azurewebsites.net/signin-twitter`例如:)。</span><span class="sxs-lookup"><span data-stu-id="927ad-110">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="927ad-111">稍后在本示例中配置的 Twitter 身份验证方案将自动处理`/signin-twitter`路由中的请求以实现 OAuth 流。</span><span class="sxs-lookup"><span data-stu-id="927ad-111">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="927ad-112">URI 段`/signin-twitter`设置为 Twitter 身份验证提供程序的默认回调。</span><span class="sxs-lookup"><span data-stu-id="927ad-112">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="927ad-113">通过[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Twitter 身份验证中间件时, 可以更改默认的回叫 URI。</span><span class="sxs-lookup"><span data-stu-id="927ad-113">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="927ad-114">填写表单的其余部分, 然后点击 "**创建 Twitter 应用程序**"。</span><span class="sxs-lookup"><span data-stu-id="927ad-114">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="927ad-115">显示新应用程序详细信息:</span><span class="sxs-lookup"><span data-stu-id="927ad-115">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="927ad-116">存储 Twitter 使用者 API 密钥和机密</span><span class="sxs-lookup"><span data-stu-id="927ad-116">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="927ad-117">运行以下命令来安全地存储`ClientId`和`ClientSecret`使用[机密管理器](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="927ad-117">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerSecret <Secret>
```

<span data-ttu-id="927ad-118">使用[机密管理器](xref:security/app-secrets) `Consumer Key` `Consumer Secret`将 Twitter 等敏感设置链接到应用程序配置。</span><span class="sxs-lookup"><span data-stu-id="927ad-118">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="927ad-119">出于本示例的目的, 请将令牌`Authentication:Twitter:ConsumerKey`命名为和。 `Authentication:Twitter:ConsumerSecret`</span><span class="sxs-lookup"><span data-stu-id="927ad-119">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="927ad-120">创建新的 Twitter 应用程序后, 可以在 "**密钥和访问令牌**" 选项卡上找到这些令牌:</span><span class="sxs-lookup"><span data-stu-id="927ad-120">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="927ad-121">配置 Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="927ad-121">Configure Twitter Authentication</span></span>

<span data-ttu-id="927ad-122">将 Twitter 服务添加到`ConfigureServices` *Startup.cs*文件的方法中:</span><span class="sxs-lookup"><span data-stu-id="927ad-122">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="927ad-123">有关 Twitter 身份验证支持的配置选项的详细信息, 请参阅[TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API 参考。</span><span class="sxs-lookup"><span data-stu-id="927ad-123">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="927ad-124">这可以用于请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="927ad-124">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="927ad-125">通过 Twitter 登录</span><span class="sxs-lookup"><span data-stu-id="927ad-125">Sign in with Twitter</span></span>

<span data-ttu-id="927ad-126">运行应用并选择 **"登录"** 。</span><span class="sxs-lookup"><span data-stu-id="927ad-126">Run the app and select **Log in**.</span></span> <span data-ttu-id="927ad-127">将显示使用 Twitter 登录的选项:</span><span class="sxs-lookup"><span data-stu-id="927ad-127">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="927ad-128">单击**twitter**重定向到 twitter 进行身份验证:</span><span class="sxs-lookup"><span data-stu-id="927ad-128">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="927ad-129">输入 Twitter 凭据后, 会重定向回到网站, 你可以在其中设置电子邮件。</span><span class="sxs-lookup"><span data-stu-id="927ad-129">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="927ad-130">你现在已使用 Twitter 凭据登录:</span><span class="sxs-lookup"><span data-stu-id="927ad-130">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="927ad-131">疑难解答</span><span class="sxs-lookup"><span data-stu-id="927ad-131">Troubleshooting</span></span>

* <span data-ttu-id="927ad-132">**仅 ASP.NET Core 2.x:** 如果未通过调用`services.AddIdentity` `ConfigureServices`来配置标识, 尝试进行身份验证将导致*ArgumentException:必须提供*"SignInScheme" 选项。</span><span class="sxs-lookup"><span data-stu-id="927ad-132">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="927ad-133">本示例中使用的项目模板可确保完成此操作。</span><span class="sxs-lookup"><span data-stu-id="927ad-133">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="927ad-134">如果尚未通过应用初始迁移创建站点数据库，则会收到*处理请求时，数据库操作失败*错误。</span><span class="sxs-lookup"><span data-stu-id="927ad-134">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="927ad-135">点击**应用迁移**创建数据库，并刷新以忽略错误继续。</span><span class="sxs-lookup"><span data-stu-id="927ad-135">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="927ad-136">后续步骤</span><span class="sxs-lookup"><span data-stu-id="927ad-136">Next steps</span></span>

* <span data-ttu-id="927ad-137">本文介绍了如何通过 Twitter 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="927ad-137">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="927ad-138">可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="927ad-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="927ad-139">将网站发布到 Azure web 应用后, 应`ConsumerSecret`在 Twitter 开发人员门户中重置。</span><span class="sxs-lookup"><span data-stu-id="927ad-139">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="927ad-140">设置`Authentication:Twitter:ConsumerKey`和`Authentication:Twitter:ConsumerSecret`作为在 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="927ad-140">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="927ad-141">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="927ad-141">The configuration system is set up to read keys from environment variables.</span></span>
