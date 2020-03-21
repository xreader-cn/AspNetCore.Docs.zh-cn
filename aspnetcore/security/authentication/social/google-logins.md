---
title: 在 ASP.NET Core Google 外部登录安装程序
author: rick-anderson
description: 本教程演示的集成到现有的 ASP.NET Core 应用程序的 Google 帐户用户身份验证。
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/19/2020
uid: security/authentication/google-logins
ms.openlocfilehash: a114d23c25201c9fe31ad0397efaf99fe98a312a
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989774"
---
# <a name="google-external-login-setup-in-aspnet-core"></a><span data-ttu-id="99ef5-103">在 ASP.NET Core Google 外部登录安装程序</span><span class="sxs-lookup"><span data-stu-id="99ef5-103">Google external login setup in ASP.NET Core</span></span>

<span data-ttu-id="99ef5-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="99ef5-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="99ef5-105">本教程演示如何让用户能够使用在[前一页](xref:security/authentication/social/index)上创建的 ASP.NET Core 3.0 项目使用其 Google 帐户登录。</span><span class="sxs-lookup"><span data-stu-id="99ef5-105">This tutorial shows you how to enable users to sign in with their Google account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-a-google-api-console-project-and-client-id"></a><span data-ttu-id="99ef5-106">创建 Google API 控制台项目和客户端 ID</span><span class="sxs-lookup"><span data-stu-id="99ef5-106">Create a Google API Console project and client ID</span></span>

* <span data-ttu-id="99ef5-107">请安装[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google)。</span><span class="sxs-lookup"><span data-stu-id="99ef5-107">Install [Microsoft.AspNetCore.Authentication.Google](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Google).</span></span>
* <span data-ttu-id="99ef5-108">导航到 "将[Google 登录集成到你的 web 应用"](https://developers.google.com/identity/sign-in/web/devconsole-project) ，然后选择 "**配置项目**"。</span><span class="sxs-lookup"><span data-stu-id="99ef5-108">Navigate to [Integrating Google Sign-In into your web app](https://developers.google.com/identity/sign-in/web/devconsole-project) and select **CONFIGURE A PROJECT**.</span></span>
* <span data-ttu-id="99ef5-109">在 "**配置 OAuth 客户端**" 对话框中，选择 " **Web 服务器**"。</span><span class="sxs-lookup"><span data-stu-id="99ef5-109">In the **Configure your OAuth client** dialog, select **Web server**.</span></span>
* <span data-ttu-id="99ef5-110">在 "**授权重定向 uri** " 文本输入框中，设置重定向 URI。</span><span class="sxs-lookup"><span data-stu-id="99ef5-110">In the **Authorized redirect URIs** text entry box, set the redirect URI.</span></span> <span data-ttu-id="99ef5-111">例如，`https://localhost:44312/signin-google`</span><span class="sxs-lookup"><span data-stu-id="99ef5-111">For example, `https://localhost:44312/signin-google`</span></span>
* <span data-ttu-id="99ef5-112">保存**客户端 ID**和**客户端密码**。</span><span class="sxs-lookup"><span data-stu-id="99ef5-112">Save the **Client ID** and **Client Secret**.</span></span>
* <span data-ttu-id="99ef5-113">部署站点时，从**Google 控制台**注册新的公共 url。</span><span class="sxs-lookup"><span data-stu-id="99ef5-113">When deploying the site, register the new public url from the **Google Console**.</span></span>

## <a name="store-the-google-client-id-and-secret"></a><span data-ttu-id="99ef5-114">存储 Google 客户端 ID 和机密</span><span class="sxs-lookup"><span data-stu-id="99ef5-114">Store the Google client ID and secret</span></span>

<span data-ttu-id="99ef5-115">用[机密管理器](xref:security/app-secrets)存储敏感设置，例如 Google 客户端 ID 和机密值。</span><span class="sxs-lookup"><span data-stu-id="99ef5-115">Store sensitive settings such as the Google client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="99ef5-116">对于本示例，请使用以下步骤：</span><span class="sxs-lookup"><span data-stu-id="99ef5-116">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="99ef5-117">按照[启用密钥存储](xref:security/app-secrets#enable-secret-storage)中的说明初始化密钥存储的项目。</span><span class="sxs-lookup"><span data-stu-id="99ef5-117">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="99ef5-118">将敏感设置存储在本地密钥存储中，并将机密密钥 `Authentication:Google:ClientId` 和 `Authentication:Google:ClientSecret`：</span><span class="sxs-lookup"><span data-stu-id="99ef5-118">Store the sensitive settings in the local secret store with the secret keys `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Google:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Google:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

<span data-ttu-id="99ef5-119">你可以在[Api 控制台](https://console.developers.google.com/apis/dashboard)中管理 api 凭据和使用情况。</span><span class="sxs-lookup"><span data-stu-id="99ef5-119">You can manage your API credentials and usage in the [API Console](https://console.developers.google.com/apis/dashboard).</span></span>

## <a name="configure-google-authentication"></a><span data-ttu-id="99ef5-120">配置 Google 身份验证</span><span class="sxs-lookup"><span data-stu-id="99ef5-120">Configure Google authentication</span></span>

<span data-ttu-id="99ef5-121">将 Google 服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="99ef5-121">Add the Google service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupGoogle3x.cs?highlight=11-19)]

[!INCLUDE [default settings configuration](includes/default-settings2-2.md)]

## <a name="sign-in-with-google"></a><span data-ttu-id="99ef5-122">登录 Google</span><span class="sxs-lookup"><span data-stu-id="99ef5-122">Sign in with Google</span></span>

* <span data-ttu-id="99ef5-123">运行应用程序，并单击 "**登录"** 。</span><span class="sxs-lookup"><span data-stu-id="99ef5-123">Run the app and click **Log in**.</span></span> <span data-ttu-id="99ef5-124">此时将显示使用 Google 登录的选项。</span><span class="sxs-lookup"><span data-stu-id="99ef5-124">An option to sign in with Google appears.</span></span>
* <span data-ttu-id="99ef5-125">单击 " **google** " 按钮，该按钮将重定向到 google 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="99ef5-125">Click the **Google** button, which redirects to Google for authentication.</span></span>
* <span data-ttu-id="99ef5-126">输入 Google 凭据后，会重定向回网站。</span><span class="sxs-lookup"><span data-stu-id="99ef5-126">After entering your Google credentials, you are redirected back to the web site.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="99ef5-127">有关 Google authentication 支持的配置选项的详细信息，请参阅 <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API 参考。</span><span class="sxs-lookup"><span data-stu-id="99ef5-127">See the <xref:Microsoft.AspNetCore.Authentication.Google.GoogleOptions> API reference for more information on configuration options supported by Google authentication.</span></span> <span data-ttu-id="99ef5-128">这可以用于请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="99ef5-128">This can be used to request different information about the user.</span></span>

## <a name="change-the-default-callback-uri"></a><span data-ttu-id="99ef5-129">更改默认回调 URI</span><span class="sxs-lookup"><span data-stu-id="99ef5-129">Change the default callback URI</span></span>

<span data-ttu-id="99ef5-130">URI 段 `/signin-google` 设置为 Google 身份验证提供程序的默认回调。</span><span class="sxs-lookup"><span data-stu-id="99ef5-130">The URI segment `/signin-google` is set as the default callback of the Google authentication provider.</span></span> <span data-ttu-id="99ef5-131">通过[GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Google 身份验证中间件时，可以更改默认的回叫 URI。</span><span class="sxs-lookup"><span data-stu-id="99ef5-131">You can change the default callback URI while configuring the Google authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [GoogleOptions](/dotnet/api/microsoft.aspnetcore.authentication.google.googleoptions) class.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="99ef5-132">故障排除</span><span class="sxs-lookup"><span data-stu-id="99ef5-132">Troubleshooting</span></span>

* <span data-ttu-id="99ef5-133">如果登录不起作用，并且没有出现任何错误，请切换到开发模式，以便更轻松地进行调试。</span><span class="sxs-lookup"><span data-stu-id="99ef5-133">If the sign-in doesn't work and you aren't getting any errors, switch to development mode to make the issue easier to debug.</span></span>
* <span data-ttu-id="99ef5-134">如果未通过在 `ConfigureServices`中调用 `services.AddIdentity` 来配置标识，尝试对 ArgumentException 中的结果进行身份验证 *：必须提供 "SignInScheme" 选项*。</span><span class="sxs-lookup"><span data-stu-id="99ef5-134">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate results in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="99ef5-135">在本教程中使用的项目模板可确保，此操作。</span><span class="sxs-lookup"><span data-stu-id="99ef5-135">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="99ef5-136">如果尚未通过应用初始迁移来创建站点数据库，则在处理请求错误时，将会出现*数据库操作失败*的情况。</span><span class="sxs-lookup"><span data-stu-id="99ef5-136">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="99ef5-137">选择 "**应用迁移**" 以创建数据库，并刷新页面以继续出现错误。</span><span class="sxs-lookup"><span data-stu-id="99ef5-137">Select **Apply Migrations** to create the database, and refresh the page to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="99ef5-138">后续步骤</span><span class="sxs-lookup"><span data-stu-id="99ef5-138">Next steps</span></span>

* <span data-ttu-id="99ef5-139">本文介绍了您如何可以使用 Google 进行验证。</span><span class="sxs-lookup"><span data-stu-id="99ef5-139">This article showed how you can authenticate with Google.</span></span> <span data-ttu-id="99ef5-140">您可以遵循类似的方法向[前一页](xref:security/authentication/social/index)上列出的其他提供程序进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="99ef5-140">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>
* <span data-ttu-id="99ef5-141">将应用发布到 Azure 后，请在 Google API 控制台中重置 `ClientSecret`。</span><span class="sxs-lookup"><span data-stu-id="99ef5-141">Once you publish the app to Azure, reset the `ClientSecret` in the Google API Console.</span></span>
* <span data-ttu-id="99ef5-142">将 `Authentication:Google:ClientId` 和 `Authentication:Google:ClientSecret` 设置为 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="99ef5-142">Set the `Authentication:Google:ClientId` and `Authentication:Google:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="99ef5-143">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="99ef5-143">The configuration system is set up to read keys from environment variables.</span></span>
