---
title: Microsoft 帐户外部登录设置与 ASP.NET Core
author: rick-anderson
description: 此示例演示如何将 Microsoft 帐户用户身份验证集成到现有 ASP.NET Core 应用中。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 3161e4f0f735294d69dd51634b424d1ed573e615
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060295"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="a9fb8-103">Microsoft 帐户外部登录设置与 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a9fb8-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="a9fb8-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9fb8-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a9fb8-105">此示例演示如何使用户能够使用在 [前一页](xref:security/authentication/social/index)上创建的 ASP.NET Core 3.0 项目，使用其工作、学校或个人 Microsoft 帐户登录。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="a9fb8-106">在 Microsoft 开发人员门户中创建应用</span><span class="sxs-lookup"><span data-stu-id="a9fb8-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="a9fb8-107">将 [AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="a9fb8-108">导航到 " [Azure 门户应用注册](https://go.microsoft.com/fwlink/?linkid=2083908) " 页，创建或登录到 Microsoft 帐户：</span><span class="sxs-lookup"><span data-stu-id="a9fb8-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="a9fb8-109">如果没有 Microsoft 帐户，请选择 " **创建** "。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-109">If you don't have a Microsoft account, select **Create one** .</span></span> <span data-ttu-id="a9fb8-110">登录后，会重定向到 **应用注册** 页面：</span><span class="sxs-lookup"><span data-stu-id="a9fb8-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="a9fb8-111">选择 **新注册**</span><span class="sxs-lookup"><span data-stu-id="a9fb8-111">Select **New registration**</span></span>
* <span data-ttu-id="a9fb8-112">输入“名称”。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-112">Enter a **Name** .</span></span>
* <span data-ttu-id="a9fb8-113">为 **支持的帐户类型** 选择一个选项。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-113">Select an option for **Supported account types** .</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
  * <span data-ttu-id="a9fb8-114">`MicrosoftAccount`默认情况下，包支持使用 "在任何组织目录中的帐户" 或 "任何组织目录和 Microsoft 帐户中的帐户" 选项创建的应用注册。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-114">The `MicrosoftAccount` package supports App Registrations created using "Accounts in any organizational directory" or "Accounts in any organizational directory and Microsoft accounts" options by default.</span></span>
  * <span data-ttu-id="a9fb8-115">若要使用其他选项，请将和成员设置为， `AuthorizationEndpoint` `TokenEndpoint` 用于在 `MicrosoftAccountOptions` (创建应用注册后， **Endpoints** 通过单击 " **概述** " 页上的 "终结点") 上的 "终结点"，将 Microsoft 帐户身份验证初始化为显示的 url 上显示的 url。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-115">To use other options, set `AuthorizationEndpoint` and `TokenEndpoint` members of `MicrosoftAccountOptions` used to initialize the Microsoft Account authentication to the URLs displayed on **Endpoints** page of the App Registration after it is created (available by clicking Endpoints on the **Overview** page).</span></span>
* <span data-ttu-id="a9fb8-116">在 " **重定向 URI** " 下，输入追加的开发 URL `/signin-microsoft` 。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-116">Under **Redirect URI** , enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="a9fb8-117">例如 `https://localhost:5001/signin-microsoft`。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-117">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="a9fb8-118">稍后在本示例中配置的 Microsoft 身份验证方案将自动处理路由中的请求 `/signin-microsoft` 以实现 OAuth 流。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-118">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="a9fb8-119">选择“注册”</span><span class="sxs-lookup"><span data-stu-id="a9fb8-119">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="a9fb8-120">创建客户端机密</span><span class="sxs-lookup"><span data-stu-id="a9fb8-120">Create client secret</span></span>

* <span data-ttu-id="a9fb8-121">在左窗格中，选择“证书和密码”。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-121">In the left pane, select **Certificates & secrets** .</span></span>
* <span data-ttu-id="a9fb8-122">在 " **客户端密码** " 下，选择 **新的客户端密码**</span><span class="sxs-lookup"><span data-stu-id="a9fb8-122">Under **Client secrets** , select **New client secret**</span></span>

  * <span data-ttu-id="a9fb8-123">添加客户端密码的说明。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-123">Add a description for the client secret.</span></span>
  * <span data-ttu-id="a9fb8-124">选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-124">Select the **Add** button.</span></span>

* <span data-ttu-id="a9fb8-125">在 " **客户端** 密码" 下，复制 "客户端密钥" 的值。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-125">Under **Client secrets** , copy the value of the client secret.</span></span>

<span data-ttu-id="a9fb8-126">URI 段 `/signin-microsoft` 设置为 Microsoft 身份验证提供程序的默认回调。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-126">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="a9fb8-127">通过[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Microsoft 身份验证中间件时，可以更改默认的回调 URI。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-127">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="a9fb8-128">存储 Microsoft 客户端 ID 和机密</span><span class="sxs-lookup"><span data-stu-id="a9fb8-128">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="a9fb8-129">用 [机密管理器](xref:security/app-secrets)存储敏感设置，如 Microsoft 客户端 ID 和机密值。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-129">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="a9fb8-130">对于本示例，请使用以下步骤：</span><span class="sxs-lookup"><span data-stu-id="a9fb8-130">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="a9fb8-131">按照 [启用密钥存储](xref:security/app-secrets#enable-secret-storage)中的说明初始化密钥存储的项目。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-131">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="a9fb8-132">将敏感设置存储在本地密钥存储中，并提供机密密钥 `Authentication:Microsoft:ClientId` 和 `Authentication:Microsoft:ClientSecret` ：</span><span class="sxs-lookup"><span data-stu-id="a9fb8-132">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="a9fb8-133">配置 Microsoft 帐户身份验证</span><span class="sxs-lookup"><span data-stu-id="a9fb8-133">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="a9fb8-134">将 Microsoft 帐户服务添加到 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="a9fb8-134">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="a9fb8-135">有关 Microsoft 帐户身份验证支持的配置选项的详细信息，请参阅 [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API 参考。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-135">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="a9fb8-136">这可用于请求有关用户的其他信息。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-136">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="a9fb8-137">Microsoft 登录帐户</span><span class="sxs-lookup"><span data-stu-id="a9fb8-137">Sign in with Microsoft Account</span></span>

<span data-ttu-id="a9fb8-138">运行应用程序，并单击 " **登录"** 。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-138">Run the app and click **Log in** .</span></span> <span data-ttu-id="a9fb8-139">此时会显示一个用于使用 Microsoft 登录的选项。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-139">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="a9fb8-140">当你单击 "Microsoft" 时，你将重定向到 Microsoft 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-140">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="a9fb8-141">使用你的 Microsoft 帐户登录后，系统会提示你让应用访问你的信息：</span><span class="sxs-lookup"><span data-stu-id="a9fb8-141">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="a9fb8-142">点击 **"是"** ，你会被重定向回到网站，你可以在其中设置电子邮件。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-142">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="a9fb8-143">你现在已使用 Microsoft 凭据登录：</span><span class="sxs-lookup"><span data-stu-id="a9fb8-143">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="a9fb8-144">疑难解答</span><span class="sxs-lookup"><span data-stu-id="a9fb8-144">Troubleshooting</span></span>

* <span data-ttu-id="a9fb8-145">如果 Microsoft 帐户提供程序将您重定向到登录错误页面，请记下 `#` Uri 中 (井号号) 的错误标题和说明查询字符串参数。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-145">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="a9fb8-146">尽管错误消息似乎指出了 Microsoft 身份验证存在问题，但最常见的原因是应用程序 Uri 与为 **Web** 平台指定的任何 **重定向 uri** 都不匹配。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-146">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="a9fb8-147">如果 :::no-loc(Identity)::: 未通过调用进行 `services.Add:::no-loc(Identity):::` 配置 `ConfigureServices` ，尝试进行身份验证会导致 *ArgumentException：必须提供 "SignInScheme" 选项* 。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-147">If :::no-loc(Identity)::: isn't configured by calling `services.Add:::no-loc(Identity):::` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided* .</span></span> <span data-ttu-id="a9fb8-148">本示例中使用的项目模板可确保完成此操作。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-148">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="a9fb8-149">如果尚未通过应用初始迁移来创建站点数据库，则在处理请求错误时将会获得 *数据库操作失败* 。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-149">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="a9fb8-150">点击 " **应用迁移** " 以创建数据库，然后单击 "刷新" 以继续出现错误。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-150">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a9fb8-151">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a9fb8-151">Next steps</span></span>

* <span data-ttu-id="a9fb8-152">本文演示了如何向 Microsoft 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-152">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="a9fb8-153">您可以遵循类似的方法向 [前一页](xref:security/authentication/social/index)上列出的其他提供程序进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-153">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="a9fb8-154">将网站发布到 Azure web 应用后，在 Microsoft 开发人员门户中创建新的客户端密码。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-154">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="a9fb8-155">`Authentication:Microsoft:ClientId` `Authentication:Microsoft:ClientSecret` 在 Azure 门户中将和设置为应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-155">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="a9fb8-156">配置系统设置为从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="a9fb8-156">The configuration system is set up to read keys from environment variables.</span></span>
