---
title: 具有 ASP.NET Core 的 Twitter 外部登录设置
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用的 Twitter 帐户用户身份验证。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/twitter-logins
ms.openlocfilehash: b848486415fd72ce6180b4cf8fc1ba00410d694a
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989745"
---
# <a name="twitter-external-sign-in-setup-with-aspnet-core"></a><span data-ttu-id="1f67f-103">具有 ASP.NET Core 的 Twitter 外部登录设置</span><span class="sxs-lookup"><span data-stu-id="1f67f-103">Twitter external sign-in setup with ASP.NET Core</span></span>

<span data-ttu-id="1f67f-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1f67f-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1f67f-105">此示例演示如何使用户能够使用在[前一页](xref:security/authentication/social/index)上创建的示例 ASP.NET Core 3.0 项目[登录其 Twitter 帐户](https://dev.twitter.com/web/sign-in/desktop-browser)。</span><span class="sxs-lookup"><span data-stu-id="1f67f-105">This sample shows how to enable users to [sign in with their Twitter account](https://dev.twitter.com/web/sign-in/desktop-browser) using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-twitter"></a><span data-ttu-id="1f67f-106">在 Twitter 中创建应用</span><span class="sxs-lookup"><span data-stu-id="1f67f-106">Create the app in Twitter</span></span>

* <span data-ttu-id="1f67f-107">将[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="1f67f-107">Add the [Microsoft.AspNetCore.Authentication.Twitter](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Twitter/3.0.0) NuGet package to the project.</span></span>

* <span data-ttu-id="1f67f-108">导航到[https://apps.twitter.com/](https://apps.twitter.com/)并登录。</span><span class="sxs-lookup"><span data-stu-id="1f67f-108">Navigate to [https://apps.twitter.com/](https://apps.twitter.com/) and sign in.</span></span> <span data-ttu-id="1f67f-109">如果还没有 Twitter 帐户，请使用 " **[立即注册](https://twitter.com/signup)** " 链接创建一个。</span><span class="sxs-lookup"><span data-stu-id="1f67f-109">If you don't already have a Twitter account, use the **[Sign up now](https://twitter.com/signup)** link to create one.</span></span>

* <span data-ttu-id="1f67f-110">选择“创建应用”。</span><span class="sxs-lookup"><span data-stu-id="1f67f-110">Select **Create an app**.</span></span> <span data-ttu-id="1f67f-111">填写应用程序**名称**、**应用程序说明**和公共**网站**URI （在注册域名之前，这可能是暂时的）：</span><span class="sxs-lookup"><span data-stu-id="1f67f-111">Fill out the **App name**, **Application description** and public **Website** URI (this can be temporary until you register the domain name):</span></span>

* <span data-ttu-id="1f67f-112">选中 "**启用使用 Twitter 登录**" 旁边的框</span><span class="sxs-lookup"><span data-stu-id="1f67f-112">Check the box next to **Enable Sign in with Twitter**</span></span>

* <span data-ttu-id="1f67f-113">AspNetCore 要求用户在默认情况下具有电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="1f67f-113">Microsoft.AspNetCore.Identity requires users to have an email address by default.</span></span> <span data-ttu-id="1f67f-114">中转到 "**权限**" 选项卡，单击 "**编辑**" 按钮，然后选中 "**请求用户的电子邮件地址**" 旁边的框。</span><span class="sxs-lookup"><span data-stu-id="1f67f-114">Go to the **Permissions** tab, click the **Edit** button and check the box next to **Request email address from users**.</span></span>

* <span data-ttu-id="1f67f-115">输入 `/signin-twitter` 追加到 "**回调 url** " 字段中的开发 URI （例如： `https://webapp128.azurewebsites.net/signin-twitter`）。</span><span class="sxs-lookup"><span data-stu-id="1f67f-115">Enter your development URI with `/signin-twitter` appended into the **Callback URLs** field (for example: `https://webapp128.azurewebsites.net/signin-twitter`).</span></span> <span data-ttu-id="1f67f-116">稍后在本示例中配置的 Twitter 身份验证方案将自动处理 `/signin-twitter` 路由中的请求以实现 OAuth 流。</span><span class="sxs-lookup"><span data-stu-id="1f67f-116">The Twitter authentication scheme configured later in this sample will automatically handle requests at `/signin-twitter` route to implement the OAuth flow.</span></span>

  > [!NOTE]
  > <span data-ttu-id="1f67f-117">URI 段 `/signin-twitter` 设置为 Twitter 身份验证提供程序的默认回调。</span><span class="sxs-lookup"><span data-stu-id="1f67f-117">The URI segment `/signin-twitter` is set as the default callback of the Twitter authentication provider.</span></span> <span data-ttu-id="1f67f-118">通过[TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Twitter 身份验证中间件时，可以更改默认的回叫 URI。</span><span class="sxs-lookup"><span data-stu-id="1f67f-118">You can change the default callback URI while configuring the Twitter authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.authentication.twitter.twitteroptions) class.</span></span>

* <span data-ttu-id="1f67f-119">填写表单的其余部分，然后选择 "**创建**"。</span><span class="sxs-lookup"><span data-stu-id="1f67f-119">Fill out the rest of the form and select **Create**.</span></span> <span data-ttu-id="1f67f-120">显示新应用程序详细信息：</span><span class="sxs-lookup"><span data-stu-id="1f67f-120">New application details are displayed:</span></span>

## <a name="store-the-twitter-consumer-api-key-and-secret"></a><span data-ttu-id="1f67f-121">存储 Twitter 使用者 API 密钥和机密</span><span class="sxs-lookup"><span data-stu-id="1f67f-121">Store the Twitter consumer API key and secret</span></span>

<span data-ttu-id="1f67f-122">使用[机密管理器](xref:security/app-secrets)存储敏感设置，如 Twitter 使用者 API 密钥和机密。</span><span class="sxs-lookup"><span data-stu-id="1f67f-122">Store sensitive settings such as the Twitter consumer API key and secret with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="1f67f-123">对于本示例，请使用以下步骤：</span><span class="sxs-lookup"><span data-stu-id="1f67f-123">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="1f67f-124">按照[启用密钥存储](xref:security/app-secrets#enable-secret-storage)中的说明初始化密钥存储的项目。</span><span class="sxs-lookup"><span data-stu-id="1f67f-124">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="1f67f-125">将敏感设置存储在本地密钥存储中，其中包含机密密钥 `Authentication:Twitter:ConsumerKey` 和 `Authentication:Twitter:ConsumerSecret`：</span><span class="sxs-lookup"><span data-stu-id="1f67f-125">Store the sensitive settings in the local secret store with the secrets keys `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Twitter:ConsumerAPIKey" "<consumer-api-key>"
    dotnet user-secrets set "Authentication:Twitter:ConsumerSecret" "<consumer-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="1f67f-126">创建新的 Twitter 应用程序后，可以在 "**密钥和访问令牌**" 选项卡上找到这些令牌：</span><span class="sxs-lookup"><span data-stu-id="1f67f-126">These tokens can be found on the **Keys and Access Tokens** tab after creating a new Twitter application:</span></span>

## <a name="configure-twitter-authentication"></a><span data-ttu-id="1f67f-127">配置 Twitter 身份验证</span><span class="sxs-lookup"><span data-stu-id="1f67f-127">Configure Twitter Authentication</span></span>

<span data-ttu-id="1f67f-128">将 Twitter 服务添加到*Startup.cs*文件的 `ConfigureServices` 方法中：</span><span class="sxs-lookup"><span data-stu-id="1f67f-128">Add the Twitter service in the `ConfigureServices` method in *Startup.cs* file:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupTwitter3x.cs?name=snippet&highlight=10-15)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="1f67f-129">有关 Twitter 身份验证支持的配置选项的详细信息，请参阅[TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API 参考。</span><span class="sxs-lookup"><span data-stu-id="1f67f-129">See the [TwitterOptions](/dotnet/api/microsoft.aspnetcore.builder.twitteroptions) API reference for more information on configuration options supported by Twitter authentication.</span></span> <span data-ttu-id="1f67f-130">这可以用于请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="1f67f-130">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-twitter"></a><span data-ttu-id="1f67f-131">通过 Twitter 登录</span><span class="sxs-lookup"><span data-stu-id="1f67f-131">Sign in with Twitter</span></span>

<span data-ttu-id="1f67f-132">运行应用并选择 **"登录"** 。</span><span class="sxs-lookup"><span data-stu-id="1f67f-132">Run the app and select **Log in**.</span></span> <span data-ttu-id="1f67f-133">将显示使用 Twitter 登录的选项：</span><span class="sxs-lookup"><span data-stu-id="1f67f-133">An option to sign in with Twitter appears:</span></span>

<span data-ttu-id="1f67f-134">单击**twitter**重定向到 twitter 进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="1f67f-134">Clicking on **Twitter** redirects to Twitter for authentication:</span></span>

<span data-ttu-id="1f67f-135">输入 Twitter 凭据后，会重定向回到网站，你可以在其中设置电子邮件。</span><span class="sxs-lookup"><span data-stu-id="1f67f-135">After entering your Twitter credentials, you are redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="1f67f-136">你现在已使用 Twitter 凭据登录：</span><span class="sxs-lookup"><span data-stu-id="1f67f-136">You are now logged in using your Twitter credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="1f67f-137">故障排除</span><span class="sxs-lookup"><span data-stu-id="1f67f-137">Troubleshooting</span></span>

* <span data-ttu-id="1f67f-138">**仅 ASP.NET Core 2.x：** 如果未通过在 `ConfigureServices`中调用 `services.AddIdentity` 来配置标识，则尝试进行身份验证将导致*ArgumentException：必须提供 "SignInScheme" 选项*。</span><span class="sxs-lookup"><span data-stu-id="1f67f-138">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="1f67f-139">本示例中使用的项目模板可确保完成此操作。</span><span class="sxs-lookup"><span data-stu-id="1f67f-139">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="1f67f-140">如果尚未通过应用初始迁移来创建站点数据库，则在处理请求错误时将会获得*数据库操作失败*。</span><span class="sxs-lookup"><span data-stu-id="1f67f-140">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="1f67f-141">点击 "**应用迁移**" 以创建数据库，然后单击 "刷新" 以继续出现错误。</span><span class="sxs-lookup"><span data-stu-id="1f67f-141">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1f67f-142">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1f67f-142">Next steps</span></span>

* <span data-ttu-id="1f67f-143">本文介绍了如何通过 Twitter 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="1f67f-143">This article showed how you can authenticate with Twitter.</span></span> <span data-ttu-id="1f67f-144">您可以遵循类似的方法向[前一页](xref:security/authentication/social/index)上列出的其他提供程序进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="1f67f-144">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="1f67f-145">将网站发布到 Azure web 应用后，应重置 Twitter 开发人员门户中的 `ConsumerSecret`。</span><span class="sxs-lookup"><span data-stu-id="1f67f-145">Once you publish your web site to Azure web app, you should reset the `ConsumerSecret` in the Twitter developer portal.</span></span>

* <span data-ttu-id="1f67f-146">将 `Authentication:Twitter:ConsumerKey` 和 `Authentication:Twitter:ConsumerSecret` 设置为 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="1f67f-146">Set the `Authentication:Twitter:ConsumerKey` and `Authentication:Twitter:ConsumerSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="1f67f-147">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="1f67f-147">The configuration system is set up to read keys from environment variables.</span></span>
