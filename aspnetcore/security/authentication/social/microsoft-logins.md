---
title: 使用 ASP.NET Core 的 Microsoft 帐户外部登录设置
author: rick-anderson
description: 此示例演示如何将 Microsoft 帐户用户身份验证集成到现有 ASP.NET Core 应用中。
ms.author: riande
ms.custom: mvc
ms.date: 12/4/2019
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: ddaae1a25a1dcf167ffae0f24b480e2cde6aca5b
ms.sourcegitcommit: f4cd3828e26e6d549ba8d0c36a17be35ad9e5a51
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74825464"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="090ae-103">使用 ASP.NET Core 的 Microsoft 帐户外部登录设置</span><span class="sxs-lookup"><span data-stu-id="090ae-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="090ae-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="090ae-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="090ae-105">此示例演示如何使用户能够使用在[前一页](xref:security/authentication/social/index)上创建的 ASP.NET Core 3.0 项目使用其 Microsoft 帐户进行登录。</span><span class="sxs-lookup"><span data-stu-id="090ae-105">This sample shows you how to enable users to sign in with their Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="090ae-106">在 Microsoft 开发人员门户中创建应用</span><span class="sxs-lookup"><span data-stu-id="090ae-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="090ae-107">将[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet 包添加到项目。</span><span class="sxs-lookup"><span data-stu-id="090ae-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="090ae-108">导航到 " [Azure 门户应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)" 页，创建或登录到 Microsoft 帐户：</span><span class="sxs-lookup"><span data-stu-id="090ae-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="090ae-109">如果没有 Microsoft 帐户，请选择 "**创建**"。</span><span class="sxs-lookup"><span data-stu-id="090ae-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="090ae-110">登录后，会重定向到**应用注册**页面：</span><span class="sxs-lookup"><span data-stu-id="090ae-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="090ae-111">选择**新注册**</span><span class="sxs-lookup"><span data-stu-id="090ae-111">Select **New registration**</span></span>
* <span data-ttu-id="090ae-112">输入“名称”。</span><span class="sxs-lookup"><span data-stu-id="090ae-112">Enter a **Name**.</span></span>
* <span data-ttu-id="090ae-113">为**支持的帐户类型**选择一个选项。</span><span class="sxs-lookup"><span data-stu-id="090ae-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts -->
* <span data-ttu-id="090ae-114">在 "**重定向 URI**" 下，输入 `/signin-microsoft` 追加的开发 URL。</span><span class="sxs-lookup"><span data-stu-id="090ae-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="090ae-115">例如 `https://localhost:5001/signin-microsoft`。</span><span class="sxs-lookup"><span data-stu-id="090ae-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="090ae-116">稍后在本示例中配置的 Microsoft 身份验证方案将自动处理 `/signin-microsoft` 路由中的请求以实现 OAuth 流。</span><span class="sxs-lookup"><span data-stu-id="090ae-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="090ae-117">选择**注册**</span><span class="sxs-lookup"><span data-stu-id="090ae-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="090ae-118">创建客户端密码</span><span class="sxs-lookup"><span data-stu-id="090ae-118">Create client secret</span></span>

* <span data-ttu-id="090ae-119">在左侧窗格中，选择 "**证书" & "机密**"。</span><span class="sxs-lookup"><span data-stu-id="090ae-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="090ae-120">在 "**客户端密码**" 下，选择**新的客户端密码**</span><span class="sxs-lookup"><span data-stu-id="090ae-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="090ae-121">添加客户端密码的说明。</span><span class="sxs-lookup"><span data-stu-id="090ae-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="090ae-122">选择“添加”按钮。</span><span class="sxs-lookup"><span data-stu-id="090ae-122">Select the **Add** button.</span></span>

* <span data-ttu-id="090ae-123">在 "**客户端**密码" 下，复制 "客户端密钥" 的值。</span><span class="sxs-lookup"><span data-stu-id="090ae-123">Under **Client secrets**, copy the value of the client secret.</span></span>

> [!NOTE]
> <span data-ttu-id="090ae-124">URI 段 `/signin-microsoft` 设置为 Microsoft 身份验证提供程序的默认回调。</span><span class="sxs-lookup"><span data-stu-id="090ae-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="090ae-125">通过[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)类的继承的[RemoteAuthenticationOptions. CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Microsoft 身份验证中间件时，可以更改默认的回调 URI。</span><span class="sxs-lookup"><span data-stu-id="090ae-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-client-secret"></a><span data-ttu-id="090ae-126">存储 Microsoft 客户端 ID 和客户端密钥</span><span class="sxs-lookup"><span data-stu-id="090ae-126">Store the Microsoft client ID and client secret</span></span>

<span data-ttu-id="090ae-127">运行以下命令，使用[机密管理器](xref:security/app-secrets)安全地存储 `ClientId` 和 `ClientSecret`：</span><span class="sxs-lookup"><span data-stu-id="090ae-127">Run the following commands to securely store `ClientId` and `ClientSecret` using [Secret Manager](xref:security/app-secrets):</span></span>

```dotnetcli
dotnet user-secrets set Authentication:Microsoft:ClientId <Client-Id>
dotnet user-secrets set Authentication:Microsoft:ClientSecret <Client-Secret>
```

<span data-ttu-id="090ae-128">使用[机密管理器](xref:security/app-secrets)将敏感设置（如 Microsoft `ClientId` 和 `ClientSecret`）链接到应用程序配置。</span><span class="sxs-lookup"><span data-stu-id="090ae-128">Link sensitive settings like Microsoft `ClientId` and `ClientSecret` to your application configuration using the [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="090ae-129">出于本示例的目的，请将令牌命名 `Authentication:Microsoft:ClientId` 和 `Authentication:Microsoft:ClientSecret`。</span><span class="sxs-lookup"><span data-stu-id="090ae-129">For the purposes of this sample, name the tokens `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`.</span></span>

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="090ae-130">配置 Microsoft 帐户身份验证</span><span class="sxs-lookup"><span data-stu-id="090ae-130">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="090ae-131">将 Microsoft 帐户服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="090ae-131">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

[!INCLUDE[](includes/chain-auth-providers.md)]

<span data-ttu-id="090ae-132">有关 Microsoft 帐户身份验证支持的配置选项的详细信息，请参阅[MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API 参考。</span><span class="sxs-lookup"><span data-stu-id="090ae-132">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="090ae-133">这可以用于请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="090ae-133">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="090ae-134">Microsoft 登录帐户</span><span class="sxs-lookup"><span data-stu-id="090ae-134">Sign in with Microsoft Account</span></span>

<span data-ttu-id="090ae-135">运行应用程序，并单击 "**登录"** 。</span><span class="sxs-lookup"><span data-stu-id="090ae-135">Run the app and click **Log in**.</span></span> <span data-ttu-id="090ae-136">此时会显示一个用于使用 Microsoft 登录的选项。</span><span class="sxs-lookup"><span data-stu-id="090ae-136">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="090ae-137">当你单击 "Microsoft" 时，你将重定向到 Microsoft 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="090ae-137">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="090ae-138">使用你的 Microsoft 帐户登录后，系统会提示你让应用访问你的信息：</span><span class="sxs-lookup"><span data-stu-id="090ae-138">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="090ae-139">点击 **"是"** ，你会被重定向回到网站，你可以在其中设置电子邮件。</span><span class="sxs-lookup"><span data-stu-id="090ae-139">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="090ae-140">你现在已使用 Microsoft 凭据登录：</span><span class="sxs-lookup"><span data-stu-id="090ae-140">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="090ae-141">故障排除</span><span class="sxs-lookup"><span data-stu-id="090ae-141">Troubleshooting</span></span>

* <span data-ttu-id="090ae-142">如果 Microsoft 帐户提供程序将您重定向到登录错误页，请记下 Uri 中的 `#` （井号）后面的错误标题和说明查询字符串参数。</span><span class="sxs-lookup"><span data-stu-id="090ae-142">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="090ae-143">尽管错误消息似乎指出了 Microsoft 身份验证存在问题，但最常见的原因是应用程序 Uri 与为**Web**平台指定的任何**重定向 uri**都不匹配。</span><span class="sxs-lookup"><span data-stu-id="090ae-143">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="090ae-144">如果未通过在 `ConfigureServices`中调用 `services.AddIdentity` 来配置标识，则尝试进行身份验证将导致*ArgumentException：必须提供 "SignInScheme" 选项*。</span><span class="sxs-lookup"><span data-stu-id="090ae-144">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="090ae-145">本示例中使用的项目模板可确保完成此操作。</span><span class="sxs-lookup"><span data-stu-id="090ae-145">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="090ae-146">如果尚未通过应用初始迁移创建站点数据库，则会收到*处理请求时，数据库操作失败*错误。</span><span class="sxs-lookup"><span data-stu-id="090ae-146">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="090ae-147">点击**应用迁移**创建数据库，并刷新以忽略错误继续。</span><span class="sxs-lookup"><span data-stu-id="090ae-147">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="090ae-148">后续步骤</span><span class="sxs-lookup"><span data-stu-id="090ae-148">Next steps</span></span>

* <span data-ttu-id="090ae-149">本文演示了如何向 Microsoft 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="090ae-149">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="090ae-150">可以遵循类似的方法来使用上列出其他提供程序进行身份验证[上一页](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="090ae-150">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="090ae-151">将网站发布到 Azure web 应用后，在 Microsoft 开发人员门户中创建新的客户端密码。</span><span class="sxs-lookup"><span data-stu-id="090ae-151">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="090ae-152">设置`Authentication:Microsoft:ClientId`和`Authentication:Microsoft:ClientSecret`作为在 Azure 门户中的应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="090ae-152">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="090ae-153">配置系统设置以从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="090ae-153">The configuration system is set up to read keys from environment variables.</span></span>
