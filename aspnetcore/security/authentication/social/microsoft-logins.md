---
title: 微软帐户外部登录设置与ASP.NET核心
author: rick-anderson
description: 此示例演示了 Microsoft 帐户用户身份验证集成到现有ASP.NET核心应用。
ms.author: riande
ms.custom: mvc
ms.date: 03/19/2020
monikerRange: '>= aspnetcore-3.0'
uid: security/authentication/microsoft-logins
ms.openlocfilehash: 12c86456dad86731b86487a3a4dd725f36677002
ms.sourcegitcommit: f29a12486313e38e0163a643d8a97c8cecc7e871
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/14/2020
ms.locfileid: "81384045"
---
# <a name="microsoft-account-external-login-setup-with-aspnet-core"></a><span data-ttu-id="cb340-103">微软帐户外部登录设置与ASP.NET核心</span><span class="sxs-lookup"><span data-stu-id="cb340-103">Microsoft Account external login setup with ASP.NET Core</span></span>

<span data-ttu-id="cb340-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cb340-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cb340-105">此示例演示如何允许用户使用[上一页上](xref:security/authentication/social/index)创建的 ASP.NET Core 3.0 项目使用他们的工作、学校或个人 Microsoft 帐户登录。</span><span class="sxs-lookup"><span data-stu-id="cb340-105">This sample shows you how to enable users to sign in with their work, school, or personal Microsoft account using the ASP.NET Core 3.0 project created on the [previous page](xref:security/authentication/social/index).</span></span>

## <a name="create-the-app-in-microsoft-developer-portal"></a><span data-ttu-id="cb340-106">在 Microsoft 开发人员门户中创建应用</span><span class="sxs-lookup"><span data-stu-id="cb340-106">Create the app in Microsoft Developer Portal</span></span>

* <span data-ttu-id="cb340-107">将[Microsoft.AspNetCore.身份验证.Microsoft 帐户](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/)NuGet 包添加到项目中。</span><span class="sxs-lookup"><span data-stu-id="cb340-107">Add the [Microsoft.AspNetCore.Authentication.MicrosoftAccount](https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.MicrosoftAccount/) NuGet package to the project.</span></span>
* <span data-ttu-id="cb340-108">导航到[Azure 门户 - 应用注册](https://go.microsoft.com/fwlink/?linkid=2083908)页面，然后创建或登录 Microsoft 帐户：</span><span class="sxs-lookup"><span data-stu-id="cb340-108">Navigate to the [Azure portal - App registrations](https://go.microsoft.com/fwlink/?linkid=2083908) page and create or sign into a Microsoft account:</span></span>

<span data-ttu-id="cb340-109">如果您没有 Microsoft 帐户，请选择"**创建一个**"。</span><span class="sxs-lookup"><span data-stu-id="cb340-109">If you don't have a Microsoft account, select **Create one**.</span></span> <span data-ttu-id="cb340-110">登录后，您将重定向到**应用注册**页面：</span><span class="sxs-lookup"><span data-stu-id="cb340-110">After signing in, you are redirected to the **App registrations** page:</span></span>

* <span data-ttu-id="cb340-111">选择**新注册**</span><span class="sxs-lookup"><span data-stu-id="cb340-111">Select **New registration**</span></span>
* <span data-ttu-id="cb340-112">输入“名称”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="cb340-112">Enter a **Name**.</span></span>
* <span data-ttu-id="cb340-113">为**受支持的帐户类型**选择一个选项。</span><span class="sxs-lookup"><span data-stu-id="cb340-113">Select an option for **Supported account types**.</span></span>  <!-- Accounts for any org work with MS domain accounts. Most folks probably want the last option, personal MS accounts. It took 24 hours after setting this up for the keys to work -->
* <span data-ttu-id="cb340-114">在**重定向 URI**下，输入追加`/signin-microsoft`的开发 URL。</span><span class="sxs-lookup"><span data-stu-id="cb340-114">Under **Redirect URI**, enter your development URL with `/signin-microsoft` appended.</span></span> <span data-ttu-id="cb340-115">例如，`https://localhost:5001/signin-microsoft` 。</span><span class="sxs-lookup"><span data-stu-id="cb340-115">For example, `https://localhost:5001/signin-microsoft`.</span></span> <span data-ttu-id="cb340-116">本示例中稍后配置的 Microsoft 身份验证方案将自动处理路由中`/signin-microsoft`实现 OAuth 流的请求。</span><span class="sxs-lookup"><span data-stu-id="cb340-116">The Microsoft authentication scheme configured later in this sample will automatically handle requests at `/signin-microsoft` route to implement the OAuth flow.</span></span>
* <span data-ttu-id="cb340-117">选择 **"注册"**</span><span class="sxs-lookup"><span data-stu-id="cb340-117">Select **Register**</span></span>

### <a name="create-client-secret"></a><span data-ttu-id="cb340-118">创建客户端机密</span><span class="sxs-lookup"><span data-stu-id="cb340-118">Create client secret</span></span>

* <span data-ttu-id="cb340-119">在左窗格中，选择“证书和机密”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="cb340-119">In the left pane, select **Certificates & secrets**.</span></span>
* <span data-ttu-id="cb340-120">在 **"客户端机密**"下，选择 **"新客户端机密**"</span><span class="sxs-lookup"><span data-stu-id="cb340-120">Under **Client secrets**, select **New client secret**</span></span>

  * <span data-ttu-id="cb340-121">添加客户端机密的说明。</span><span class="sxs-lookup"><span data-stu-id="cb340-121">Add a description for the client secret.</span></span>
  * <span data-ttu-id="cb340-122">选择"**添加**"按钮。</span><span class="sxs-lookup"><span data-stu-id="cb340-122">Select the **Add** button.</span></span>

* <span data-ttu-id="cb340-123">在**客户端机密**下，复制客户端机密的值。</span><span class="sxs-lookup"><span data-stu-id="cb340-123">Under **Client secrets**, copy the value of the client secret.</span></span>

<span data-ttu-id="cb340-124">URI 段`/signin-microsoft`设置为 Microsoft 身份验证提供程序的默认回调。</span><span class="sxs-lookup"><span data-stu-id="cb340-124">The URI segment `/signin-microsoft` is set as the default callback of the Microsoft authentication provider.</span></span> <span data-ttu-id="cb340-125">您可以通过 Microsoft[帐户选项](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions)类的继承[远程身份验证选项.callbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath)属性配置 Microsoft 身份验证中间件时更改默认回调 URI。</span><span class="sxs-lookup"><span data-stu-id="cb340-125">You can change the default callback URI while configuring the Microsoft authentication middleware via the inherited [RemoteAuthenticationOptions.CallbackPath](/dotnet/api/microsoft.aspnetcore.authentication.remoteauthenticationoptions.callbackpath) property of the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.authentication.microsoftaccount.microsoftaccountoptions) class.</span></span>

## <a name="store-the-microsoft-client-id-and-secret"></a><span data-ttu-id="cb340-126">存储 Microsoft 客户端 ID 和机密</span><span class="sxs-lookup"><span data-stu-id="cb340-126">Store the Microsoft client ID and secret</span></span>

<span data-ttu-id="cb340-127">使用[密钥管理器](xref:security/app-secrets)存储敏感设置，如 Microsoft 客户端 ID 和机密值。</span><span class="sxs-lookup"><span data-stu-id="cb340-127">Store sensitive settings such as the Microsoft client ID and secret values with [Secret Manager](xref:security/app-secrets).</span></span> <span data-ttu-id="cb340-128">对于此示例，请使用以下步骤：</span><span class="sxs-lookup"><span data-stu-id="cb340-128">For this sample, use the following steps:</span></span>

1. <span data-ttu-id="cb340-129">根据[启用密钥存储](xref:security/app-secrets#enable-secret-storage)的指令初始化了机密存储的项目。</span><span class="sxs-lookup"><span data-stu-id="cb340-129">Initialize the project for secret storage per the instructions at [Enable secret storage](xref:security/app-secrets#enable-secret-storage).</span></span>
1. <span data-ttu-id="cb340-130">使用密钥`Authentication:Microsoft:ClientId`和 将敏感设置存储在本地机密存储中`Authentication:Microsoft:ClientSecret`：</span><span class="sxs-lookup"><span data-stu-id="cb340-130">Store the sensitive settings in the local secret store with the secret keys `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret`:</span></span>

    ```dotnetcli
    dotnet user-secrets set "Authentication:Microsoft:ClientId" "<client-id>"
    dotnet user-secrets set "Authentication:Microsoft:ClientSecret" "<client-secret>"
    ```

[!INCLUDE[](~/includes/environmentVarableColon.md)]

## <a name="configure-microsoft-account-authentication"></a><span data-ttu-id="cb340-131">配置 Microsoft 帐户身份验证</span><span class="sxs-lookup"><span data-stu-id="cb340-131">Configure Microsoft Account Authentication</span></span>

<span data-ttu-id="cb340-132">将 Microsoft 帐户服务添加到`Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="cb340-132">Add the Microsoft Account service to the `Startup.ConfigureServices`:</span></span>

[!code-csharp[](~/security/authentication/social/social-code/3.x/StartupMS3x.cs?name=snippet&highlight=10-14)]

[!INCLUDE [default settings configuration](includes/default-settings.md)]

<span data-ttu-id="cb340-133">有关 Microsoft 帐户身份验证支持的配置选项的详细信息，请参阅[Microsoft 帐户选项](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions)API 参考。</span><span class="sxs-lookup"><span data-stu-id="cb340-133">For more information about configuration options supported by Microsoft Account authentication, see the [MicrosoftAccountOptions](/dotnet/api/microsoft.aspnetcore.builder.microsoftaccountoptions) API reference.</span></span> <span data-ttu-id="cb340-134">这可用于请求有关用户的不同信息。</span><span class="sxs-lookup"><span data-stu-id="cb340-134">This can be used to request different information about the user.</span></span>

## <a name="sign-in-with-microsoft-account"></a><span data-ttu-id="cb340-135">使用 Microsoft 帐户登录</span><span class="sxs-lookup"><span data-stu-id="cb340-135">Sign in with Microsoft Account</span></span>

<span data-ttu-id="cb340-136">运行应用并单击"**登录**"。</span><span class="sxs-lookup"><span data-stu-id="cb340-136">Run the app and click **Log in**.</span></span> <span data-ttu-id="cb340-137">将显示一个与 Microsoft 登录的选项。</span><span class="sxs-lookup"><span data-stu-id="cb340-137">An option to sign in with Microsoft appears.</span></span> <span data-ttu-id="cb340-138">单击 Microsoft 时，您将重定向到 Microsoft 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="cb340-138">When you click on Microsoft, you are redirected to Microsoft for authentication.</span></span> <span data-ttu-id="cb340-139">使用 Microsoft 帐户登录后，系统将提示您让应用访问您的信息：</span><span class="sxs-lookup"><span data-stu-id="cb340-139">After signing in with your Microsoft Account, you will be prompted to let the app access your info:</span></span>

<span data-ttu-id="cb340-140">点按 **"是**"，您将被重定向回网站，您可以在该网站设置电子邮件。</span><span class="sxs-lookup"><span data-stu-id="cb340-140">Tap **Yes** and you will be redirected back to the web site where you can set your email.</span></span>

<span data-ttu-id="cb340-141">您现在使用 Microsoft 凭据登录：</span><span class="sxs-lookup"><span data-stu-id="cb340-141">You are now logged in using your Microsoft credentials:</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="troubleshooting"></a><span data-ttu-id="cb340-142">疑难解答</span><span class="sxs-lookup"><span data-stu-id="cb340-142">Troubleshooting</span></span>

* <span data-ttu-id="cb340-143">如果 Microsoft 帐户提供程序将您重定向到登录错误页，请记下错误标题和说明查询字符串参数，直接遵循 Uri`#`中的 （hashtag）。</span><span class="sxs-lookup"><span data-stu-id="cb340-143">If the Microsoft Account provider redirects you to a sign in error page, note the error title and description query string parameters directly following the `#` (hashtag) in the Uri.</span></span>

  <span data-ttu-id="cb340-144">尽管错误消息似乎表明 Microsoft 身份验证有问题，但最常见的原因是应用程序 Uri 与为**Web**平台指定的重定向**URI**不匹配。</span><span class="sxs-lookup"><span data-stu-id="cb340-144">Although the error message seems to indicate a problem with Microsoft authentication, the most common cause is your application Uri not matching any of the **Redirect URIs** specified for the **Web** platform.</span></span>
* <span data-ttu-id="cb340-145">如果未通过调用`services.AddIdentity`配置标识`ConfigureServices`，则尝试身份验证将导致*参数异常：必须提供"SignInScheme"选项*。</span><span class="sxs-lookup"><span data-stu-id="cb340-145">If Identity isn't configured by calling `services.AddIdentity` in `ConfigureServices`, attempting to authenticate will result in *ArgumentException: The 'SignInScheme' option must be provided*.</span></span> <span data-ttu-id="cb340-146">此示例中使用的项目模板可确保完成此操作。</span><span class="sxs-lookup"><span data-stu-id="cb340-146">The project template used in this sample ensures that this is done.</span></span>
* <span data-ttu-id="cb340-147">如果尚未通过应用初始迁移创建站点数据库，则在处理请求错误时，将获取*数据库操作失败*。</span><span class="sxs-lookup"><span data-stu-id="cb340-147">If the site database has not been created by applying the initial migration, you will get *A database operation failed while processing the request* error.</span></span> <span data-ttu-id="cb340-148">点按 **"应用迁移**"以创建数据库并刷新以继续结束错误。</span><span class="sxs-lookup"><span data-stu-id="cb340-148">Tap **Apply Migrations** to create the database and refresh to continue past the error.</span></span>

## <a name="next-steps"></a><span data-ttu-id="cb340-149">后续步骤</span><span class="sxs-lookup"><span data-stu-id="cb340-149">Next steps</span></span>

* <span data-ttu-id="cb340-150">本文展示了如何使用 Microsoft 进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="cb340-150">This article showed how you can authenticate with Microsoft.</span></span> <span data-ttu-id="cb340-151">您可以采用类似的方法对[上一页](xref:security/authentication/social/index)中列出的其他提供程序进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="cb340-151">You can follow a similar approach to authenticate with other providers listed on the [previous page](xref:security/authentication/social/index).</span></span>

* <span data-ttu-id="cb340-152">将网站发布到 Azure Web 应用后，在 Microsoft 开发人员门户中创建新的客户端机密。</span><span class="sxs-lookup"><span data-stu-id="cb340-152">Once you publish your web site to Azure web app, create a new client secrets in the Microsoft Developer Portal.</span></span>

* <span data-ttu-id="cb340-153">在`Authentication:Microsoft:ClientId`Azure`Authentication:Microsoft:ClientSecret`门户中将 和 设置为应用程序设置。</span><span class="sxs-lookup"><span data-stu-id="cb340-153">Set the `Authentication:Microsoft:ClientId` and `Authentication:Microsoft:ClientSecret` as application settings in the Azure portal.</span></span> <span data-ttu-id="cb340-154">配置系统设置为从环境变量读取密钥。</span><span class="sxs-lookup"><span data-stu-id="cb340-154">The configuration system is set up to read keys from environment variables.</span></span>
