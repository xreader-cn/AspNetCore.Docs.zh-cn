---
title: 在 ASP.NET Core Google 外部登录安装程序
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用程序的 Google 帐户用户身份验证。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/19/2019
uid: security/authentication/google-logins
ms.openlocfilehash: e12d831d2e0a5c9acae5ea41fb4187ad4ca6b0ea
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082477"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="7ac51-103">在 ASP.NET Core Google 外部登录安装程序</span><span class="sxs-lookup"><span data-stu-id="7ac51-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="7ac51-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7ac51-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="7ac51-105">[从2019年3月7日起已关闭旧版 Google + api](https://developers.google.com/+/api-shutdown)。</span><span class="sxs-lookup"><span data-stu-id="7ac51-105">[Legacy Google+ APIs have been shut down as of March 7, 2019](https://developers.google.com/+/api-shutdown).</span></span> <span data-ttu-id="7ac51-106">Google + 登录和开发人员必须迁移到新的 Google 登录系统。</span><span class="sxs-lookup"><span data-stu-id="7ac51-106">Google+ sign in and developers must move to a new Google sign in system.</span></span> <span data-ttu-id="7ac51-107">已更新用于 Google 身份验证的 ASP.NET Core 2.1 和2.2 包以适应这些更改。</span><span class="sxs-lookup"><span data-stu-id="7ac51-107">The ASP.NET Core 2.1 and 2.2 packages for Google Authentication have be updated to accommodate the changes.</span></span> <span data-ttu-id="7ac51-108">有关 ASP.NET Core 的详细信息和临时缓解，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/6486)。</span><span class="sxs-lookup"><span data-stu-id="7ac51-108">For more information and temporary mitigations for ASP.NET Core, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/6486).</span></span> <span data-ttu-id="7ac51-109">本教程已在新的安装过程中更新。</span><span class="sxs-lookup"><span data-stu-id="7ac51-109">This tutorial has been updated with the new setup process.</span></span>

<span data-ttu-id="7ac51-110">本教程演示如何让用户能够使用在[前一页](xref:security/authentication/social/index)上创建的 ASP.NET Core 2.2 项目使用其 Google 帐户登录。</span><span class="sxs-lookup"><span data-stu-id="7ac51-110">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="7ac51-111">创建 Google API 控制台项目和客户端 ID</span><span class="sxs-lookup"><span data-stu-id="7ac51-111">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="7ac51-112">导航到 "将[Google 登录集成到你的 web 应用"](https://developers.google.com/identity/sign-in/web/devconsole-project) ，然后选择 "**配置项目**"。</span><span class="sxs-lookup"><span data-stu-id="7ac51-112">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="7ac51-113">在 "**配置 OAuth 客户端**" 对话框中，选择 " **Web 服务器**"。</span><span class="sxs-lookup"><span data-stu-id="7ac51-113">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="7ac51-114">在 "**授权重定向 uri** " 文本输入框中，设置重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="7ac51-114">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="7ac51-115">例如，`https://localhost:5001/signin-google`</span><span class="sxs-lookup"><span data-stu-id="7ac51-115">For example, `https://localhost:5001/signin-google`</span></span>
* <span data-ttu-id="7ac51-116">保存**客户端 ID**和**客户端密码**。</span><span class="sxs-lookup"><span data-stu-id="7ac51-116">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="7ac51-117">部署站点时，从**Google 控制台**注册新的公共 url。</span><span class="sxs-lookup"><span data-stu-id="7ac51-117">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-google-clientid-and-clientsecret"></a><span data-ttu-id="7ac51-118">应用商店 Google ClientID 和 ClientSecret</span><span class="sxs-lookup"><span data-stu-id="7ac51-118">Store Google ClientID and ClientSecret</span></span>

<span data-ttu-id="7ac51-119">用[机密管理器](xref:security/app-secrets)存储敏感设置`Client ID` ， `Client Secret`例如 Google 和。</span><span class="sxs-lookup"><span data-stu-id="7ac51-119">Store sensitive settings such as the Google `Client ID` and `Client Secret` with the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="7ac51-120">出于本教程的目的，请为令牌`Authentication:Google:ClientId`命名和： `Authentication:Google:ClientSecret`</span><span class="sxs-lookup"><span data-stu-id="7ac51-120">For the purposes of this tutorial, name the tokens `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

```dotnetcli
dotnet user-secrets set "Authentication:Google:ClientId" "X.apps.googleusercontent.com"
dotnet user-secrets set "Authentication:Google:ClientSecret" "<client secret>"
```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="7ac51-121">你可以在[Api 控制台](https://console.developers.google.com/apis/dashboard)中管理 api 凭据和使用情况。</span><span class="sxs-lookup"><span data-stu-id="7ac51-121">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="7ac51-122">配置 Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="7ac51-122">Configure Google authentication</span></span>

<span data-ttu-id="7ac51-123">将 Google 服务添加到`Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="7ac51-123">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupGoogle.cs?name=snippet_ConfigureServices&highlight=10-18)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="7ac51-124">使用 Google 登录</span><span class="sxs-lookup"><span data-stu-id="7ac51-124">Sign in with Google</span></span>

* <span data-ttu-id="7ac51-125">运行应用程序，并单击 "**登录"** 。</span><span class="sxs-lookup"><span data-stu-id="7ac51-125">Run the app and click **Log in**.</span></span> <span data-ttu-id="7ac51-126">此时将显示使用 Google 登录的选项。</span><span class="sxs-lookup"><span data-stu-id="7ac51-126">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="7ac51-127">单击 " **google** " 按钮，该按钮将重定向到 google 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="7ac51-127">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="7ac51-128">输入 Google 凭据后，会重定向回网站。</span><span class="sxs-lookup"><span data-stu-id="7ac51-128">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="7ac51-129">有关 Google 身份验证支持的配置选项的详细信息，请参阅API参考。<xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions></span><span class="sxs-lookup"><span data-stu-id="7ac51-129">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="7ac51-130">这可以用于请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="7ac51-130">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="7ac51-131">更改默认回调 URI</span><span class="sxs-lookup"><span data-stu-id="7ac51-131">Change the default callback URI</span></span>

<span data-ttu-id="7ac51-132">URI 段`/signin-google`设置为默认的 Google 身份验证提供程序的回调。</span><span class="sxs-lookup"><span data-stu-id="7ac51-132">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="7ac51-133">配置 Google 身份验证中间件通过继承时，可以更改默认的回调 URI [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)的属性[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)类。</span><span class="sxs-lookup"><span data-stu-id="7ac51-133">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="7ac51-134">疑难解答</span><span class="sxs-lookup"><span data-stu-id="7ac51-134">Troubleshooting</span></span>

* <span data-ttu-id="7ac51-135">如果登录不起作用，并且没有出现任何错误，请切换到开发模式，以便更轻松地进行调试。</span><span class="sxs-lookup"><span data-stu-id="7ac51-135">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="7ac51-136">如果未通过调用`services.AddIdentity` `ConfigureServices`来配置标识，则尝试在 ArgumentException 中*对结果进行身份验证：必须提供*"SignInScheme" 选项。</span><span class="sxs-lookup"><span data-stu-id="7ac51-136">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="7ac51-137">在本教程中使用的项目模板可确保，此操作。</span><span class="sxs-lookup"><span data-stu-id="7ac51-137">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="7ac51-138">如果尚未通过应用初始迁移创建站点数据库，则获取*处理请求时，数据库操作失败*错误。</span><span class="sxs-lookup"><span data-stu-id="7ac51-138">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="7ac51-139">选择 "**应用迁移**" 以创建数据库，并刷新页面以继续出现错误。</span><span class="sxs-lookup"><span data-stu-id="7ac51-139">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7ac51-140">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7ac51-140">Next steps</span></span>

* <span data-ttu-id="7ac51-141">本文介绍了您如何可以使用 Google 进行验证。</span><span class="sxs-lookup"><span data-stu-id="7ac51-141">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="7ac51-142">可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="7ac51-142">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="7ac51-143">将应用发布到 Azure 后，请`ClientSecret`在 Google API 控制台中重置。</span><span class="sxs-lookup"><span data-stu-id="7ac51-143">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="7ac51-144">设置`Authentication:Google:ClientId`和`Authentication:Google:ClientSecret`作为在 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="7ac51-144">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="7ac51-145">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="7ac51-145">The configuration system is set up to read keys from environment variables.</span></span>
