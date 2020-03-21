---
title: 在 ASP.NET Core Facebook 外部登录安装程序
author: rick-anderson
description: 包含代码示例的教程演示如何将 Facebook 帐户用户身份验证集成到现有 ASP.NET Core 应用。
ms.author: riande
ms.custom: seoapril2019, mvc, seodec18
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/facebook-logins
ms.openlocfilehash: bb26a27f026e744c7d4925aa2281bf0625fff8a2
ms.sourcegitcommit: 9b6e7f421c243963d5e419bdcfc5c4bde71499aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/21/2020
ms.locfileid: "79989783"
---
# <a name="facebook-external-login-setup-in-aspnet-core"></a><span data-ttu-id="0ccf0-103">在 ASP.NET Core Facebook 外部登录安装程序</span><span class="sxs-lookup"><span data-stu-id="0ccf0-103">Facebook external login setup in ASP.NET Core</span></span>

<span data-ttu-id="0ccf0-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0ccf0-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0ccf0-105">本教程中的代码示例演示如何使用户能够使用在[前一页](xref:security/authentication/social/index)上创建的示例 ASP.NET Core 3.0 项目登录其 Facebook 帐户。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-105">This tutorial with code examples shows how to enable your users to sign in with their Facebook account using a sample ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span> <span data-ttu-id="0ccf0-106">首先，我们要按照[官方步骤](https://developers.facebook.com)创建 FACEBOOK 应用 ID。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-106">We start by creating a Facebook App ID by following the [official steps](https://developers.facebook.com).</span></span>

## <a name="create-the-app-in-facebook"></a><span data-ttu-id="0ccf0-107">在 Facebook 中创建应用</span><span class="sxs-lookup"><span data-stu-id="0ccf0-107">Create the app in Facebook</span></span>

* <span data-ttu-id="0ccf0-108">将[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-108">Add the [Microsoft.AspNetCore.Authentication.Facebook](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.Facebook) NuGet package to the project.</span></span>

* <span data-ttu-id="0ccf0-109">导航到[Facebook 开发人员应用](https://developers.facebook.com/apps/)页面并登录。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-109">Navigate to the [Facebook Developers app](https://developers.facebook.com/apps/) page and sign in.</span></span> <span data-ttu-id="0ccf0-110">如果还没有 Facebook 帐户，请使用登录页上的 "**注册 Facebook** " 链接创建一个。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-110">If you don't already have a Facebook account, use the **Sign up for Facebook** link on the login page to create one.</span></span>  <span data-ttu-id="0ccf0-111">获得 Facebook 帐户后，请按照说明注册为 Facebook 开发人员。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-111">Once you have a Facebook account, follow the instructions to register as a Facebook Developer.</span></span>

* <span data-ttu-id="0ccf0-112">从 "**我的应用**" 菜单中，选择 "**创建应用**" 以创建新的应用 ID。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-112">From the **My Apps** menu select **Create App** to create a new App ID.</span></span>

   ![Microsoft Edge 中打开的 Facebook 开发人员门户](index/_static/FBMyApps.png)

* <span data-ttu-id="0ccf0-114">填写表单，然后点击 "**创建应用 ID** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-114">Fill out the form and tap the **Create App ID** button.</span></span>

  ![创建新的应用 ID 窗体](index/_static/FBNewAppId.png)

* <span data-ttu-id="0ccf0-116">在 "新建应用" 卡中，选择 "**添加产品**"。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-116">On the new App card, select **Add a Product**.</span></span>  <span data-ttu-id="0ccf0-117">在**Facebook 登录**卡上，单击 "**设置**"</span><span class="sxs-lookup"><span data-stu-id="0ccf0-117">On the **Facebook Login** card, click **Set Up**</span></span> 

  ![产品安装程序页](index/_static/FBProductSetup.png)

* <span data-ttu-id="0ccf0-119">**快速入门**向导会启动，并**选择一个平台**作为第一页。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-119">The **Quickstart** wizard launches with **Choose a Platform** as the first page.</span></span> <span data-ttu-id="0ccf0-120">现在，通过单击左下方菜单中的 " **FaceBook 登录** **设置**" 链接，绕过向导：</span><span class="sxs-lookup"><span data-stu-id="0ccf0-120">Bypass the wizard for now by clicking the **FaceBook Login** **Settings** link in the menu on the lower left:</span></span>

  ![跳过快速入门](index/_static/FBSkipQuickStart.png)

* <span data-ttu-id="0ccf0-122">将显示 "**客户端 OAuth 设置**" 页：</span><span class="sxs-lookup"><span data-stu-id="0ccf0-122">You are presented with the **Client OAuth Settings** page:</span></span>

  ![客户端 OAuth 设置页](index/_static/FBOAuthSetup.png)

* <span data-ttu-id="0ccf0-124">输入包含 */signin-facebook*的开发 URI，并将其追加到 "**有效的 OAuth 重定向 uri** " 字段中（例如： `https://localhost:44320/signin-facebook`）。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-124">Enter your development URI with */signin-facebook* appended into the **Valid OAuth Redirect URIs** field (for example: `https://localhost:44320/signin-facebook`).</span></span> <span data-ttu-id="0ccf0-125">稍后在本教程中配置的 Facebook 身份验证将自动处理 */signin-facebook*路由中的请求以实现 OAuth 流。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-125">The Facebook authentication configured later in this tutorial will automatically handle requests at */signin-facebook* route to implement the OAuth flow.</span></span>

> [!NOTE]
> <span data-ttu-id="0ccf0-126">URI */signin-facebook*设置为 facebook 身份验证提供程序的默认回调。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-126">The URI */signin-facebook* is set as the default callback of the Facebook authentication provider.</span></span> <span data-ttu-id="0ccf0-127">通过[FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Facebook 身份验证中间件时，可以更改默认的回叫 URI。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-127">You can change the default callback URI while configuring the Facebook authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.authentication.facebook.facebookoptions) class.</span></span>

* <span data-ttu-id="0ccf0-128">单击“保存更改”。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-128">Click **Save Changes**.</span></span>

* <span data-ttu-id="0ccf0-129">单击左侧导航栏中的 "**设置**" > **基本**"链接。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-129">Click **Settings** > **Basic** link in the left navigation.</span></span>

  <span data-ttu-id="0ccf0-130">在此页上，记下你的 `App ID` 和 `App Secret`。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-130">On this page, make a note of your `App ID` and your `App Secret`.</span></span> <span data-ttu-id="0ccf0-131">你将添加到 ASP.NET Core 应用程序下一节中：</span><span class="sxs-lookup"><span data-stu-id="0ccf0-131">You will add both into your ASP.NET Core application in the next section:</span></span>

* <span data-ttu-id="0ccf0-132">部署站点时，需要重新访问**Facebook 登录**设置页面并注册新的公共 URI。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-132">When deploying the site you need to revisit the **Facebook Login** setup page and register a new public URI.</span></span>

## <a name="store-the-facebook-app-id-and-secret"></a><span data-ttu-id="0ccf0-133">存储 Facebook 应用 ID 和机密</span><span class="sxs-lookup"><span data-stu-id="0ccf0-133">Store the Facebook app ID and secret</span></span>

<span data-ttu-id="0ccf0-134">用[机密管理器](xref:security/app-secrets)存储敏感设置，如 FACEBOOK 应用 ID 和机密值。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-134">Store sensitive settings such as the Facebook app ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="0ccf0-135">对于本示例，请使用以下步骤：</span><span class="sxs-lookup"><span data-stu-id="0ccf0-135">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="0ccf0-136">按照[启用密钥存储](xref:security/app-secrets#enable-secret-storage)中的说明初始化密钥存储的项目。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-136">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="0ccf0-137">将敏感设置存储在本地密钥存储中，并将机密密钥 `Authentication:Facebook:AppId` 和 `Authentication:Facebook:AppSecret`：</span><span class="sxs-lookup"><span data-stu-id="0ccf0-137">Store the sensitive settings in the local secret store with the secret keys `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Facebook:AppId" "<app-id>"
    dotnet user-secrets set "Authentication:Facebook:AppSecret" "<app-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-facebook-authentication"></a><span data-ttu-id="0ccf0-138">配置 Facebook 身份验证</span><span class="sxs-lookup"><span data-stu-id="0ccf0-138">Configure Facebook Authentication</span></span>

<span data-ttu-id="0ccf0-139">在*Startup.cs*文件的 `ConfigureServices` 方法中添加 Facebook 服务：</span><span class="sxs-lookup"><span data-stu-id="0ccf0-139">Add the Facebook service in the `ConfigureServices` method in the *Startup.cs* file:</span></span>

```csharp
services.AddAuthentication().AddFacebook(facebookOptions =>
{
    facebookOptions.AppId = Configuration["Authentication:Facebook:AppId"];
    facebookOptions.AppSecret = Configuration["Authentication:Facebook:AppSecret"];
});
```

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="0ccf0-140">有关 Facebook 身份验证支持的配置选项的详细信息，请参阅[FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API 参考。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-140">See the [FacebookOptions](/dotnet/api/microsoft.aspnetcore.builder.facebookoptions) API reference for more information on configuration options supported by Facebook authentication.</span></span> <span data-ttu-id="0ccf0-141">配置选项可用于：</span><span class="sxs-lookup"><span data-stu-id="0ccf0-141">Configuration options can be used to:</span></span>

* <span data-ttu-id="0ccf0-142">请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-142">Request different information about the user.</span></span>
* <span data-ttu-id="0ccf0-143">添加查询字符串参数以自定义登录体验。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-143">Add query string arguments to customize the login experience.</span></span>

## <a name="sign-in-with-facebook"></a><span data-ttu-id="0ccf0-144">使用 Facebook 登录</span><span class="sxs-lookup"><span data-stu-id="0ccf0-144">Sign in with Facebook</span></span>

<span data-ttu-id="0ccf0-145">运行应用程序，并单击 "**登录"** 。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-145">Run your application and click **Log in**.</span></span> <span data-ttu-id="0ccf0-146">请参阅使用 Facebook 登录的选项。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-146">You see an option to sign in with Facebook.</span></span>

![Web 应用程序： 用户未经过身份验证](index/_static/DoneFacebook.png)

<span data-ttu-id="0ccf0-148">单击**facebook**时，会重定向到 facebook 进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="0ccf0-148">When you click on **Facebook**, you are redirected to Facebook for authentication:</span></span>

![Facebook 身份验证页面](index/_static/FBLogin.png)

<span data-ttu-id="0ccf0-150">Facebook 身份验证请求的默认公共配置文件和电子邮件地址：</span><span class="sxs-lookup"><span data-stu-id="0ccf0-150">Facebook authentication requests public profile and email address by default:</span></span>

![Facebook 身份验证页许可屏幕](index/_static/FBLoginDone.png)

<span data-ttu-id="0ccf0-152">输入你的 Facebook 凭据后你重定向回你的站点，你可以设置你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-152">Once you enter your Facebook credentials you are redirected back to your site where you can set your email.</span></span>

<span data-ttu-id="0ccf0-153">现在已在使用 Facebook 凭据登录：</span><span class="sxs-lookup"><span data-stu-id="0ccf0-153">You are now logged in using your Facebook credentials:</span></span>

![Web 应用程序： 用户通过身份验证](index/_static/Done.png)

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="0ccf0-155">故障排除</span><span class="sxs-lookup"><span data-stu-id="0ccf0-155">Troubleshooting</span></span>

* <span data-ttu-id="0ccf0-156">**仅 ASP.NET Core 2.x：** 如果未通过在 `ConfigureServices`中调用 `services.AddIdentity` 来配置标识，则尝试进行身份验证将导致*ArgumentException：必须提供 "SignInScheme" 选项*。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-156">**ASP.NET Core 2.x only:** If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="0ccf0-157">在本教程中使用的项目模板可确保，此操作。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-157">The project template used in this tutorial ensures that this is done.</span></span>
* <span data-ttu-id="0ccf0-158">如果尚未通过应用初始迁移来创建站点数据库，则在处理请求错误时，将会出现*数据库操作失败*的情况。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-158">If the site database has not been created by applying the initial migration, you get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="0ccf0-159">点击 "**应用迁移**" 以创建数据库，然后单击 "刷新" 以继续出现错误。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-159">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0ccf0-160">后续步骤</span><span class="sxs-lookup"><span data-stu-id="0ccf0-160">Next steps</span></span>

* <span data-ttu-id="0ccf0-161">本文介绍了您如何可以使用 Facebook 进行验证。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-161">This article showed how you can authenticate with Facebook.</span></span> <span data-ttu-id="0ccf0-162">您可以遵循类似的方法向[前一页](xref:security/authentication/social/index)上列出的其他提供程序进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-162">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="0ccf0-163">将网站发布到 Azure web 应用后，应重置 Facebook 开发人员门户中的 `AppSecret`。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-163">Once you publish your web site to Azure web app, you should reset the `AppSecret` in the Facebook developer portal.</span></span>

* <span data-ttu-id="0ccf0-164">将 `Authentication:Facebook:AppId` 和 `Authentication:Facebook:AppSecret` 设置为 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-164">Set the `Authentication:Facebook:AppId` and `Authentication:Facebook:AppSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="0ccf0-165">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="0ccf0-165">The configuration system is set up to read keys from environment variables.</span></span>
