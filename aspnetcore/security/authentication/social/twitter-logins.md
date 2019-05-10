---
title: Twitter 使用 ASP.NET Core 的外部登录设置
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用的 Twitter 帐户用户身份验证。
ms.author: riande
ms.custom: mvc
ms.date: 5/11/2019
uid: security/authentication/twitter-logins
ms.openlocfilehash: 486d58b600ca5326a0728de40bb386fbb9440f67
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516875"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="ff7bd-103">Twitter 使用 ASP.NET Core 的外部登录设置</span><span class="sxs-lookup"><span data-stu-id="ff7bd-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="ff7bd-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ff7bd-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="ff7bd-105">此示例演示如何使用户能够[使用 Twitter 帐户登录](https://dev.twitter.com/web/sign-in/desktop-browser)将示例 ASP.NET Core 2.2 项目上创建[上一页](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="ff7bd-106">在 Twitter 中创建应用程序</span><span class="sxs-lookup"><span data-stu-id="ff7bd-106">Create the app in Twitter</span></span>

* <span data-ttu-id="ff7bd-107">导航到[ https://apps.twitter.com/ ](https://apps.twitter.com/)并登录。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-107">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="ff7bd-108">如果还没有 Twitter 帐户，使用 **[立即注册](https://twitter.com/signup)** 链接创建一个链接。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-108">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="ff7bd-109">点击**创建新的应用程序**，应用程序中填写**名称**，**说明**和公共**网站**URI （可以是临时直到注册的域名）：</span><span class="sxs-lookup"><span data-stu-id="ff7bd-109">Tap **Create New App** and fill out the application **Name**, **Description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="ff7bd-110">输入你的开发 URI 与`/signin-twitter`追加到**有效的 OAuth 重定向 Uri**字段 (例如： `https://webapp128.azurewebsites.net/signin-twitter`)。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-110">Enter your development URI with `/signin-twitter` appended into the **Valid OAuth Redirect URIs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="ff7bd-111">配置更高版本在此示例中的 Twitter 身份验证方案将自动处理在请求`/signin-twitter`路由实现 OAuth 流。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-111">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="ff7bd-112">URI 段`/signin-twitter`设置为 Twitter 身份验证提供程序的默认回调。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-112">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="ff7bd-113">配置 Twitter 身份验证中间件通过继承时，可以更改默认的回调 URI [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)的属性[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)类。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-113">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="ff7bd-114">填写窗体的其余部分，然后点击**创建 Twitter 应用程序**。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-114">Fill out the rest of the form and tap **Create your Twitter application**.</span></span> <span data-ttu-id="ff7bd-115">显示新的应用程序详细信息：</span><span class="sxs-lookup"><span data-stu-id="ff7bd-115">New application details are displayed:</span></span>

## <a name="storing-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="ff7bd-116">存储的 Twitter 使用者 API 密钥和机密</span><span class="sxs-lookup"><span data-stu-id="ff7bd-116">Storing Twitter Consumer API key and secret</span></span>

<span data-ttu-id="ff7bd-117">运行以下命令来安全地存储`ClientId`并`ClientSecret`使用[机密管理器](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="ff7bd-117">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Twitter:ConsumerAPIKey <Key>
dotnet user-secrets set Authentication:Twitter:ConsumerAPISecret <Secret>
```

<span data-ttu-id="ff7bd-118">链接敏感设置，例如 Twitter`Consumer Key`并`Consumer Secret`到应用程序的配置使用[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-118">Link sensitive settings like Twitter `Consumer Key` and `Consumer Secret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="ff7bd-119">对于本示例的目的，命名为令牌`Authentication:Twitter:ConsumerKey`和`Authentication:Twitter:ConsumerSecret`。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-119">For the purposes of this sample, name the tokens `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`.</span></span>

<span data-ttu-id="ff7bd-120">这些令牌可在**密钥和访问令牌**选项卡后创建新的 Twitter 应用程序：</span><span class="sxs-lookup"><span data-stu-id="ff7bd-120">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="ff7bd-121">配置 Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="ff7bd-121">Configure Twitter Authentication</span></span>

<span data-ttu-id="ff7bd-122">将 Twitter 服务中的添加`ConfigureServices`中的方法*Startup.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="ff7bd-122">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupTwitter.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="ff7bd-123">请参阅[TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) Twitter 身份验证支持的配置选项的详细信息的 API 参考。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-123">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="ff7bd-124">这可以用于请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-124">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="ff7bd-125">使用 Twitter 登录</span><span class="sxs-lookup"><span data-stu-id="ff7bd-125">Sign in with Twitter</span></span>

<span data-ttu-id="ff7bd-126">运行应用并选择**登录**。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-126">Run the app and select **Log in**.</span></span> <span data-ttu-id="ff7bd-127">通过 Twitter 进行登录的选项将显示：</span><span class="sxs-lookup"><span data-stu-id="ff7bd-127">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="ff7bd-128">单击**Twitter**将重定向到 Twitter 进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="ff7bd-128">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="ff7bd-129">输入你的 Twitter 凭据后，你将重定向回 web 站点，你可以设置你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-129">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="ff7bd-130">现在已在使用你的 Twitter 凭据进行登录：</span><span class="sxs-lookup"><span data-stu-id="ff7bd-130">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="ff7bd-131">疑难解答</span><span class="sxs-lookup"><span data-stu-id="ff7bd-131">Troubleshooting</span></span>

* <span data-ttu-id="ff7bd-132">**ASP.NET Core 仅限 2.x:** 如果不通过调用配置标识`services.AddIdentity`中`ConfigureServices`，尝试进行身份验证将导致*ArgumentException:必须提供 SignInScheme 选项*。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-132">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="ff7bd-133">此示例中使用的项目模板可确保，此操作。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-133">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="ff7bd-134">如果尚未通过应用初始迁移创建站点数据库，则会收到*处理请求时，数据库操作失败*错误。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-134">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="ff7bd-135">点击**应用迁移**创建数据库，并刷新以忽略错误继续。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-135">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="ff7bd-136">后续步骤</span><span class="sxs-lookup"><span data-stu-id="ff7bd-136">Next steps</span></span>

* <span data-ttu-id="ff7bd-137">本文介绍了您如何可以使用 Twitter 进行验证。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-137">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="ff7bd-138">可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-138">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="ff7bd-139">一旦您将您的网站发布到 Azure web 应用时，应重置`ConsumerSecret`Twitter 开发人员门户中。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-139">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="ff7bd-140">设置`Authentication:Twitter:ConsumerKey`和`Authentication:Twitter:ConsumerSecret`作为在 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-140">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="ff7bd-141">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="ff7bd-141">The configuration system is set up to read keys from environment variables.</span></span>