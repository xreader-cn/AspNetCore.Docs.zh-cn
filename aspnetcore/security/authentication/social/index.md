---
title: ASP.NET Core 中的 Facebook、Google 和外部提供程序身份验证
author: rick-anderson
description: 本教程演示如何使用外部身份验证提供程序通过 OAuth 2.0 生成 ASP.NET Core 应用。
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/social/index
ms.openlocfilehash: ca5fd8f746e759d1994dde9a2a0d5b5fd6c88d1a
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870446"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="b7073-103">ASP.NET Core 中的 Facebook、Google 和外部提供程序身份验证</span><span class="sxs-lookup"><span data-stu-id="b7073-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="b7073-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b7073-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b7073-105">本教程演示如何生成 ASP.NET Core 应用，该应用可让用户使用外部身份验证提供程序提供的凭据通过 OAuth 2.0 登录。</span><span class="sxs-lookup"><span data-stu-id="b7073-105">This tutorial demonstrates how to build an ASP.NET Core app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="b7073-106">以下几节中介绍了 [Facebook](xref:security/authentication/facebook-logins)、[Twitter](xref:security/authentication/twitter-logins)、[Google](xref:security/authentication/google-logins) 和 [Microsoft](xref:security/authentication/microsoft-logins) 提供程序，这些提供程序使用本文中创建的初学者项目。</span><span class="sxs-lookup"><span data-stu-id="b7073-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections and use the starter project created in this article.</span></span> <span data-ttu-id="b7073-107">第三方程序包中提供了其他提供程序，例如 [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) 和 [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)。</span><span class="sxs-lookup"><span data-stu-id="b7073-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

<span data-ttu-id="b7073-108">让用户使用其现有凭据登录的好处：</span><span class="sxs-lookup"><span data-stu-id="b7073-108">Enabling users to sign in with their existing credentials:</span></span>

* <span data-ttu-id="b7073-109">方便用户操作。</span><span class="sxs-lookup"><span data-stu-id="b7073-109">Is convenient for the users.</span></span>
* <span data-ttu-id="b7073-110">将管理登录流程的许多复杂性转移到了第三方。</span><span class="sxs-lookup"><span data-stu-id="b7073-110">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span>

<span data-ttu-id="b7073-111">有关社交登录如何驱动流量和客户转换的示例，请参阅 [Facebook](https://www.facebook.com/unsupportedbrowser) 和 [Twitter](https://dev.twitter.com/resources/case-studies) 的案例分析。</span><span class="sxs-lookup"><span data-stu-id="b7073-111">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="b7073-112">创建新的 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="b7073-112">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="b7073-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="b7073-113">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="b7073-114">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="b7073-114">Create a new project.</span></span>
* <span data-ttu-id="b7073-115">依次选择“ASP.NET Core Web 应用程序”和“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="b7073-115">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="b7073-116">提供项目名称，再确认或更改位置 。</span><span class="sxs-lookup"><span data-stu-id="b7073-116">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="b7073-117">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="b7073-117">Select **Create**.</span></span>
* <span data-ttu-id="b7073-118">在下拉列表 (ASP.NET Core {X.Y}) 中选择最新版 ASP.NET Core，然后选择“Web 应用程序” 。</span><span class="sxs-lookup"><span data-stu-id="b7073-118">Select the latest version of ASP.NET Core in the drop-down (**ASP.NET Core {X.Y}**), and then select **Web Application**.</span></span>
* <span data-ttu-id="b7073-119">在“身份验证”下，选择“更改”再设置针对单个用户帐户的身份验证  。</span><span class="sxs-lookup"><span data-stu-id="b7073-119">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="b7073-120">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="b7073-120">Select **OK**.</span></span>
* <span data-ttu-id="b7073-121">在“创建新的 ASP.NET Core Web 应用程序”窗口中，选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="b7073-121">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="b7073-122">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="b7073-122">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

* <span data-ttu-id="b7073-123">打开终端。</span><span class="sxs-lookup"><span data-stu-id="b7073-123">Open the terminal.</span></span>  <span data-ttu-id="b7073-124">对于 Visual Studio Code，可以打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="b7073-124">For Visual Studio Code you can open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="b7073-125">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="b7073-125">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="b7073-126">对于 Windows，运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="b7073-126">For Windows, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  ```

  <span data-ttu-id="b7073-127">对于 macOS 和 Linux，运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="b7073-127">For macOS and Linux, run the following command:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual
  ```

  * <span data-ttu-id="b7073-128">`dotnet new` 命令在“WebApp1”文件夹中新建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="b7073-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="b7073-129">`-au Individual` 创建用于个人身份验证的代码。</span><span class="sxs-lookup"><span data-stu-id="b7073-129">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="b7073-130">`-uld` 使用 LocalDB，这是 SQL Server Express for Windows 的轻型版本。</span><span class="sxs-lookup"><span data-stu-id="b7073-130">`-uld` uses LocalDB, a lightweight version of SQL Server Express for Windows.</span></span> <span data-ttu-id="b7073-131">省略 `-uld` 以使用 SQLite。</span><span class="sxs-lookup"><span data-stu-id="b7073-131">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="b7073-132">`code` 命令将在新 Visual Studio Code 实例中打开“WebApp1”文件夹。</span><span class="sxs-lookup"><span data-stu-id="b7073-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="b7073-133">应用迁移</span><span class="sxs-lookup"><span data-stu-id="b7073-133">Apply migrations</span></span>

* <span data-ttu-id="b7073-134">运行应用并选择“注册”链接。</span><span class="sxs-lookup"><span data-stu-id="b7073-134">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="b7073-135">输入新帐户的电子邮件地址和密码，再选择“注册”。</span><span class="sxs-lookup"><span data-stu-id="b7073-135">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="b7073-136">按照说明操作来应用迁移。</span><span class="sxs-lookup"><span data-stu-id="b7073-136">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="b7073-137">使用 SecretManager 存储登录提供程序分配的令牌</span><span class="sxs-lookup"><span data-stu-id="b7073-137">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="b7073-138">社交登录提供程序在注册过程中分配“应用程序 ID”和“应用程序机密”。</span><span class="sxs-lookup"><span data-stu-id="b7073-138">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="b7073-139">确切的令牌名称因提供程序而异。</span><span class="sxs-lookup"><span data-stu-id="b7073-139">The exact token names vary by provider.</span></span> <span data-ttu-id="b7073-140">这些令牌代表应用用来访问其 API 的凭据。</span><span class="sxs-lookup"><span data-stu-id="b7073-140">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="b7073-141">令牌构成“机密”，可利用[机密管理器](xref:security/app-secrets#secret-manager)将其链接到应用配置。</span><span class="sxs-lookup"><span data-stu-id="b7073-141">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="b7073-142">机密管理器是一种在配置文件（例如 appsettings.json）中存储令牌的更安全的替代方法。</span><span class="sxs-lookup"><span data-stu-id="b7073-142">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b7073-143">机密管理器仅用于开发目的。</span><span class="sxs-lookup"><span data-stu-id="b7073-143">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="b7073-144">可使用 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。</span><span class="sxs-lookup"><span data-stu-id="b7073-144">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="b7073-145">按照[在 ASP.NET Core 中进行开发期间安全存储应用机密](xref:security/app-secrets)主题中的步骤进行操作，以便存储以下每个登录提供程序分配的令牌。</span><span class="sxs-lookup"><span data-stu-id="b7073-145">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="b7073-146">应用程序所需的安装登录提供程序</span><span class="sxs-lookup"><span data-stu-id="b7073-146">Setup login providers required by your application</span></span>

<span data-ttu-id="b7073-147">使用以下主题配置应用程序，以使用相应的提供程序：</span><span class="sxs-lookup"><span data-stu-id="b7073-147">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="b7073-148">[Facebook](xref:security/authentication/facebook-logins) 相关说明</span><span class="sxs-lookup"><span data-stu-id="b7073-148">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="b7073-149">[Twitter](xref:security/authentication/twitter-logins) 相关说明</span><span class="sxs-lookup"><span data-stu-id="b7073-149">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="b7073-150">[Google](xref:security/authentication/google-logins) 相关说明</span><span class="sxs-lookup"><span data-stu-id="b7073-150">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="b7073-151">[Microsoft](xref:security/authentication/microsoft-logins) 相关说明</span><span class="sxs-lookup"><span data-stu-id="b7073-151">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="b7073-152">[其他提供程序](xref:security/authentication/otherlogins)相关说明</span><span class="sxs-lookup"><span data-stu-id="b7073-152">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="b7073-153">选择性地设置密码</span><span class="sxs-lookup"><span data-stu-id="b7073-153">Optionally set password</span></span>

<span data-ttu-id="b7073-154">使用外部登录提供程序注册，即表明还没有向应用注册密码。</span><span class="sxs-lookup"><span data-stu-id="b7073-154">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="b7073-155">这可让用户无需创建和记住站点密码，但也会使用户依赖外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="b7073-155">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="b7073-156">如果外部登录提供程序不可用，则无法登录网站。</span><span class="sxs-lookup"><span data-stu-id="b7073-156">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="b7073-157">使用外部提供程序在登录过程中设置的电子邮箱创建密码和登录：</span><span class="sxs-lookup"><span data-stu-id="b7073-157">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="b7073-158">选择右上角的“Hello &lt;电子邮件别名&gt;”链接，导航到“管理”视图 。</span><span class="sxs-lookup"><span data-stu-id="b7073-158">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Web 应用程序“管理”视图](index/_static/pass1a.png)

* <span data-ttu-id="b7073-160">选择“创建”</span><span class="sxs-lookup"><span data-stu-id="b7073-160">Select **Create**</span></span>

![“设置密码”页](index/_static/pass2a.png)

* <span data-ttu-id="b7073-162">设置一个有效密码，可以用此密码和邮箱登录。</span><span class="sxs-lookup"><span data-stu-id="b7073-162">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="b7073-163">后续步骤</span><span class="sxs-lookup"><span data-stu-id="b7073-163">Next steps</span></span>

* <span data-ttu-id="b7073-164">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/10563)，了解有关如何自定义登录按钮的信息。</span><span class="sxs-lookup"><span data-stu-id="b7073-164">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/10563) for information on how to customize the login buttons.</span></span>
* <span data-ttu-id="b7073-165">本文介绍了外部身份验证，并说明了向 ASP.NET Core 应用添加外部登录所需的先决条件。</span><span class="sxs-lookup"><span data-stu-id="b7073-165">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>
* <span data-ttu-id="b7073-166">引用特定于提供程序的页，为应用所需的提供程序配置登录。</span><span class="sxs-lookup"><span data-stu-id="b7073-166">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>
* <span data-ttu-id="b7073-167">可能需要保留有关用户及其访问和刷新令牌的其他数据。</span><span class="sxs-lookup"><span data-stu-id="b7073-167">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="b7073-168">有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="b7073-168">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
