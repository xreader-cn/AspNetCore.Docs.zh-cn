---
title: 在 ASP.NET Core Google 外部登录安装程序
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用程序的 Google 帐户用户身份验证。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 1/11/2019
uid: security/authentication/google-logins
ms.openlocfilehash: 5b6bfaafba68eaf15a60b7c512a9e7406e3112ee
ms.sourcegitcommit: 6d8c362a3cb4c634e64cbb3d28069c36bc973581
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418661"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="55226-103">在 ASP.NET Core Google 外部登录安装程序</span><span class="sxs-lookup"><span data-stu-id="55226-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="55226-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="55226-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="55226-105">与 Google 启动在 2019 年 1 月[关闭](https://developers.google.com/+/api-shutdown)Google + 中登录和开发人员必须将移到新的 Google 登录系统中的年 3 月。</span><span class="sxs-lookup"><span data-stu-id="55226-105">In January 2019 Google started to [shut down](https://developers.google.com/+/api-shutdown) Google+ sign in and developers must move to a new Google sign in system by March.</span></span> <span data-ttu-id="55226-106">将在二月以适应所做的更改更新 ASP.NET Core 2.1 和 2.2 包进行 Google 身份验证。</span><span class="sxs-lookup"><span data-stu-id="55226-106">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication will be updated in February to accommodate the changes.</span></span> <span data-ttu-id="55226-107">有关详细信息和针对 ASP.NET Core 的临时缓解措施，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/6486)。</span><span class="sxs-lookup"><span data-stu-id="55226-107">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="55226-108">本教程已更新使用新的安装程序进程。</span><span class="sxs-lookup"><span data-stu-id="55226-108">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="55226-109">本教程演示如何使用户能够使用 ASP.NET Core 2.2 项目上创建其 Google 帐户登录[上一页](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="55226-109">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="55226-110">创建 Google API 控制台项目和客户端 ID</span><span class="sxs-lookup"><span data-stu-id="55226-110">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="55226-111">导航到[集成 Google 登录到你的 web 应用](https://developers.google.com/identity/sign-in/web/devconsole-project)，然后选择**配置项目**。</span><span class="sxs-lookup"><span data-stu-id="55226-111">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="55226-112">在中**配置 OAuth 客户端**对话框中，选择**Web 服务器**。</span><span class="sxs-lookup"><span data-stu-id="55226-112">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="55226-113">在中**已授权重定向 Uri**文本输入框中，将设置重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="55226-113">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="55226-114">例如，`https://localhost:5001/signin-google`</span><span class="sxs-lookup"><span data-stu-id="55226-114">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="55226-115">保存**客户端 ID**并**客户端机密**。</span><span class="sxs-lookup"><span data-stu-id="55226-115">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="55226-116">在部署站点时，注册中的新公共 url **Google Console**。</span><span class="sxs-lookup"><span data-stu-id="55226-116">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="55226-117">应用商店 Google ClientID 和 ClientSecret</span><span class="sxs-lookup"><span data-stu-id="55226-117">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="55226-118">存储敏感设置，例如 Google`Client ID`并`Client Secret`与[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="55226-118">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="55226-119">对于本教程的目的，命名为令牌`Authentication:Google:ClientId`和`Authentication:Google:ClientSecret`:</span><span class="sxs-lookup"><span data-stu-id="55226-119">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```console
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

<span data-ttu-id="55226-120">你可以管理 API 凭据和中的使用情况[API 控制台](https://console.developers.google.com/apis/dashboard)。</span><span class="sxs-lookup"><span data-stu-id="55226-120">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="55226-121">配置 Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="55226-121">Configure Google authentication</span></span>

<span data-ttu-id="55226-122">添加 Google 服务的目标`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="55226-122">Add the Google service to `Startup.ConfigureServices`.</span></span>

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="55226-123">使用 Google 登录</span><span class="sxs-lookup"><span data-stu-id="55226-123">Sign in with Google</span></span>

* <span data-ttu-id="55226-124">运行应用并单击**登录**。</span><span class="sxs-lookup"><span data-stu-id="55226-124">Run the app and click **Log in**.</span></span> <span data-ttu-id="55226-125">将显示一个选项以使用 Google 登录。</span><span class="sxs-lookup"><span data-stu-id="55226-125">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="55226-126">单击**Google**按钮，将重定向到 Google 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="55226-126">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="55226-127">输入 Google 凭据后，你将重定向回 web 站点。</span><span class="sxs-lookup"><span data-stu-id="55226-127">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="55226-128">请参阅[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) Google 身份验证支持的配置选项的详细信息的 API 参考。</span><span class="sxs-lookup"><span data-stu-id="55226-128">See the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="55226-129">这可以用于请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="55226-129">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="55226-130">更改默认的回调 URI</span><span class="sxs-lookup"><span data-stu-id="55226-130">Change the default callback URI</span></span>

<span data-ttu-id="55226-131">URI 段`/signin-google`设置为默认的 Google 身份验证提供程序的回调。</span><span class="sxs-lookup"><span data-stu-id="55226-131">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="55226-132">配置 Google 身份验证中间件通过继承时，可以更改默认的回调 URI [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)的属性[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)类。</span><span class="sxs-lookup"><span data-stu-id="55226-132">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="55226-133">疑难解答</span><span class="sxs-lookup"><span data-stu-id="55226-133">Troubleshooting</span></span>

* <span data-ttu-id="55226-134">如果登录不起作用，并且如果没有收到任何错误，切换到开发模式，以使问题更易于调试。</span><span class="sxs-lookup"><span data-stu-id="55226-134">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="55226-135">如果不通过调用配置标识`services.AddIdentity`中`ConfigureServices`，尝试进行身份验证中的结果*ArgumentException:必须提供 SignInScheme 选项*。</span><span class="sxs-lookup"><span data-stu-id="55226-135">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="55226-136">在本教程中使用的项目模板可确保，此操作。</span><span class="sxs-lookup"><span data-stu-id="55226-136">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="55226-137">如果尚未通过应用初始迁移创建站点数据库，则获取*处理请求时，数据库操作失败*错误。</span><span class="sxs-lookup"><span data-stu-id="55226-137">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="55226-138">点击**应用迁移**创建数据库，并刷新以忽略错误继续。</span><span class="sxs-lookup"><span data-stu-id="55226-138">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="55226-139">后续步骤</span><span class="sxs-lookup"><span data-stu-id="55226-139">Next steps</span></span>

* <span data-ttu-id="55226-140">本文介绍了您如何可以使用 Google 进行验证。</span><span class="sxs-lookup"><span data-stu-id="55226-140">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="55226-141">可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="55226-141">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="55226-142">将应用发布到 Azure 时，会重置`ClientSecret`在 Google API 控制台中。</span><span class="sxs-lookup"><span data-stu-id="55226-142">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="55226-143">设置`Authentication:Google:ClientId`和`Authentication:Google:ClientSecret`作为在 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="55226-143">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="55226-144">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="55226-144">The configuration system is set up to read keys from environment variables.</span></span>
