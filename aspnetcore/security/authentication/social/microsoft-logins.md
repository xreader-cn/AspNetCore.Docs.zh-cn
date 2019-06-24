---
title: 使用 ASP.NET Core 的 Microsoft 帐户外部登录设置
author: rick-anderson
description: 此示例演示如何集成到现有的 ASP.NET Core 应用的 Microsoft 帐户用户身份验证。
ms.author: riande
ms.custom: mvc
ms.date: 5/11/2019
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 16ec2d5f2bccc59958b884869ef42af9cfa13df0
ms.sourcegitcommit: 06a455d63ff7d6b571ca832e8117f4ac9d646baf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316588"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="bcf1e-103">使用 ASP.NET Core 的 Microsoft 帐户外部登录设置</span><span class="sxs-lookup"><span data-stu-id="bcf1e-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="bcf1e-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="bcf1e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="bcf1e-105">此示例演示如何使用户能够使用 ASP.NET Core 2.2 项目上创建其 Microsoft 帐户登录[上一页](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 2.2 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="bcf1e-106">在 Microsoft 开发人员门户中创建应用</span><span class="sxs-lookup"><span data-stu-id="bcf1e-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="bcf1e-107">导航到[Azure 门户-应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页面上，创建或登录到 Microsoft 帐户：</span><span class="sxs-lookup"><span data-stu-id="bcf1e-107">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="bcf1e-108">如果你没有 Microsoft 帐户，选择**创建一个**。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-108">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="bcf1e-109">在登录后你将重定向到**应用注册**页：</span><span class="sxs-lookup"><span data-stu-id="bcf1e-109">After signing in you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="bcf1e-110">选择**新注册**</span><span class="sxs-lookup"><span data-stu-id="bcf1e-110">Select **New registration**</span></span>
* <span data-ttu-id="bcf1e-111">输入**名称**。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-111">Enter a **Name**.</span></span>
* <span data-ttu-id="bcf1e-112">选择一个选项来**支持的帐户类型**。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-112">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="bcf1e-113">下**重定向 URI**，输入与你开发的 URL`/signin-microsoft`追加。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-113">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="bcf1e-114">例如 `https://localhost:44389/signin-microsoft`。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-114">For example, `https://localhost:44389/signin-microsoft`.</span></span> <span data-ttu-id="bcf1e-115">配置更高版本在此示例中的 Microsoft 身份验证方案将自动处理在请求`/signin-microsoft`路由实现 OAuth 流。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-115">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="bcf1e-116">选择**注册**</span><span class="sxs-lookup"><span data-stu-id="bcf1e-116">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="bcf1e-117">创建客户端机密</span><span class="sxs-lookup"><span data-stu-id="bcf1e-117">Create client secret</span></span>

* <span data-ttu-id="bcf1e-118">在左窗格中，选择**证书和机密**。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-118">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="bcf1e-119">下**客户端机密**，选择**新客户端机密**</span><span class="sxs-lookup"><span data-stu-id="bcf1e-119">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="bcf1e-120">添加客户端机密的说明。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-120">Add a description for the client secret.</span></span>
  * <span data-ttu-id="bcf1e-121">选择**添加**按钮。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-121">Select the **Add** button.</span></span>

* <span data-ttu-id="bcf1e-122">下**客户端机密**，复制客户端机密的值。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-122">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="bcf1e-123">URI 段`/signin-microsoft`设置为默认的 Microsoft 身份验证提供程序的回调。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-123">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="bcf1e-124">配置 Microsoft 身份验证中间件通过继承时，可以更改默认的回调 URI [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性的[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)类。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-124">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="bcf1e-125">Microsoft 客户端 ID 和客户端机密存储</span><span class="sxs-lookup"><span data-stu-id="bcf1e-125">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="bcf1e-126">运行以下命令来安全地存储`ClientId`并`ClientSecret`使用[机密管理器](xref:security/app-secrets):</span><span class="sxs-lookup"><span data-stu-id="bcf1e-126">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```console
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="bcf1e-127">链接敏感设置，例如 Microsoft`ClientId`并`ClientSecret`到应用程序的配置使用[机密管理器](xref:security/app-secrets)。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-127">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="bcf1e-128">对于本示例的目的，命名为令牌`Authentication:Microsoft:ClientId`和`Authentication:Microsoft:ClientSecret`。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-128">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="bcf1e-129">配置 Microsoft 帐户身份验证</span><span class="sxs-lookup"><span data-stu-id="bcf1e-129">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="bcf1e-130">添加 Microsoft 帐户服务的目标`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="bcf1e-130">Add the Microsoft Account service to `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/StartupMS.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="bcf1e-131">请参阅[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)支持的 Microsoft 帐户身份验证的配置选项的详细信息的 API 参考。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-131">See the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference for more information on configuration options supported by Microsoft Account authentication.</span></span> <span data-ttu-id="bcf1e-132">这可以用于请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-132">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="bcf1e-133">使用 Microsoft 帐户登录</span><span class="sxs-lookup"><span data-stu-id="bcf1e-133">Sign in with Microsoft Account</span></span>

<span data-ttu-id="bcf1e-134">运行并单击**登录**。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-134">Run the and click **Log in**.</span></span> <span data-ttu-id="bcf1e-135">将显示一个选项以使用 Microsoft 登录。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-135">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="bcf1e-136">当单击 Microsoft 时，将重定向的身份验证到 Microsoft。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-136">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="bcf1e-137">（如果尚未登录），使用你的 Microsoft 帐户登录后您将会提示您允许应用访问你的信息：</span><span class="sxs-lookup"><span data-stu-id="bcf1e-137">After signing in with your Microsoft Account (if not already signed in) you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="bcf1e-138">点击**是**，并且将重定向回 web 站点，你可以设置你的电子邮件。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-138">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="bcf1e-139">现在已登录中使用 Microsoft 凭据：</span><span class="sxs-lookup"><span data-stu-id="bcf1e-139">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="bcf1e-140">疑难解答</span><span class="sxs-lookup"><span data-stu-id="bcf1e-140">Troubleshooting</span></span>

* <span data-ttu-id="bcf1e-141">如果 Microsoft 帐户提供程序将你重定向到登录错误页，请记下错误标题和说明查询字符串后面的参数直接`#`（井号标签） 的 Uri 中。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-141">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="bcf1e-142">错误消息似乎指出了使用 Microsoft 身份验证问题，尽管最常见的原因是你的应用程序 Uri 不匹配的任何**重定向 Uri**指定为**Web**平台.</span><span class="sxs-lookup"><span data-stu-id="bcf1e-142">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="bcf1e-143">如果不通过调用配置标识`services.AddIdentity`中`ConfigureServices`，尝试进行身份验证将导致*ArgumentException:必须提供 SignInScheme 选项*。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-143">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="bcf1e-144">此示例中使用的项目模板可确保，此操作。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-144">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="bcf1e-145">如果尚未通过应用初始迁移创建站点数据库，则会收到*处理请求时，数据库操作失败*错误。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-145">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="bcf1e-146">点击**应用迁移**创建数据库，并刷新以忽略错误继续。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-146">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="bcf1e-147">后续步骤</span><span class="sxs-lookup"><span data-stu-id="bcf1e-147">Next steps</span></span>

* <span data-ttu-id="bcf1e-148">本文介绍了如何向 Microsoft。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-148">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="bcf1e-149">可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-149">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="bcf1e-150">一旦将您的网站发布到 Azure web 应用，创建新的客户端机密在 Microsoft 开发人员门户中。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-150">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="bcf1e-151">设置`Authentication:Microsoft:ClientId`和`Authentication:Microsoft:ClientSecret`作为在 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-151">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="bcf1e-152">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="bcf1e-152">The configuration system is set up to read keys from environment variables.</span></span>