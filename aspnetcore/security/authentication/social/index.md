---
title: ASP.NET Core 中的 Facebook、Google 和外部提供程序身份验证
author: rick-anderson
description: 本教程演示如何使用外部身份验证提供程序通过 OAuth 2.0 生成 ASP.NET Core 2.x 应用。
ms.author: riande
ms.custom: mvc
ms.date: 05/10/2019
uid: security/authentication/social/index
ms.openlocfilehash: edaf9eeaf02879b2f7816bab0eb373a7de640c05
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71082509"
---
# <a name="facebook-google-and-external-provider-authentication-in-aspnet-core"></a><span data-ttu-id="8982e-103">ASP.NET Core 中的 Facebook、Google 和外部提供程序身份验证</span><span class="sxs-lookup"><span data-stu-id="8982e-103">Facebook, Google, and external provider authentication in ASP.NET Core</span></span>

<span data-ttu-id="8982e-104">作者：[Valeriy Novytskyy](https://github.com/01binary) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="8982e-104">By [Valeriy Novytskyy](https://github.com/01binary) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="8982e-105">本教程演示如何生成 ASP.NET Core 2.2 应用，该应用可让用户使用外部身份验证提供程序提供的凭据通过 OAuth 2.0 登录。</span><span class="sxs-lookup"><span data-stu-id="8982e-105">This tutorial demonstrates how to build an ASP.NET Core 2.2 app that enables users to sign in using OAuth 2.0 with credentials from external authentication providers.</span></span>

<span data-ttu-id="8982e-106">以下几节中介绍了 [Facebook](xref:security/authentication/facebook-logins)、[Twitter](xref:security/authentication/twitter-logins)、[Google](xref:security/authentication/google-logins) 和 [Microsoft](xref:security/authentication/microsoft-logins) 提供程序。</span><span class="sxs-lookup"><span data-stu-id="8982e-106">[Facebook](xref:security/authentication/facebook-logins), [Twitter](xref:security/authentication/twitter-logins), [Google](xref:security/authentication/google-logins), and [Microsoft](xref:security/authentication/microsoft-logins) providers are covered in the following sections.</span></span> <span data-ttu-id="8982e-107">第三方程序包中提供了其他提供程序，例如 [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) 和 [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers)。</span><span class="sxs-lookup"><span data-stu-id="8982e-107">Other providers are available in third-party packages such as [AspNet.Security.OAuth.Providers](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers) and [AspNet.Security.OpenId.Providers](https://github.com/aspnet-contrib/AspNet.Security.OpenId.Providers).</span></span>

![Facebook、Twitter、Google plus 和 Windows 的社交媒体图标](index/_static/social.png)

<span data-ttu-id="8982e-109">让用户使用其现有凭据登录的好处：</span><span class="sxs-lookup"><span data-stu-id="8982e-109">Enabling users to sign in with their existing credentials:</span></span>
* <span data-ttu-id="8982e-110">方便用户操作。</span><span class="sxs-lookup"><span data-stu-id="8982e-110">Is convenient for the users.</span></span>
* <span data-ttu-id="8982e-111">将管理登录流程的许多复杂性转移到了第三方。</span><span class="sxs-lookup"><span data-stu-id="8982e-111">Shifts many of the complexities of managing the sign-in process onto a third party.</span></span> 

<span data-ttu-id="8982e-112">有关社交登录如何驱动流量和客户转换的示例，请参阅 [Facebook](https://www.facebook.com/unsupportedbrowser) 和 [Twitter](https://dev.twitter.com/resources/case-studies) 的案例分析。</span><span class="sxs-lookup"><span data-stu-id="8982e-112">For examples of how social logins can drive traffic and customer conversions, see case studies by [Facebook](https://www.facebook.com/unsupportedbrowser) and [Twitter](https://dev.twitter.com/resources/case-studies).</span></span>

## <a name="create-a-new-aspnet-core-project"></a><span data-ttu-id="8982e-113">创建新的 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="8982e-113">Create a New ASP.NET Core Project</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="8982e-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8982e-114">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="8982e-115">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="8982e-115">Create a new project.</span></span>
* <span data-ttu-id="8982e-116">依次选择“ASP.NET Core Web 应用程序”和“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="8982e-116">Select **ASP.NET Core Web Application** and **Next**.</span></span>
* <span data-ttu-id="8982e-117">提供项目名称，再确认或更改位置   。</span><span class="sxs-lookup"><span data-stu-id="8982e-117">Provide a **Project name** and confirm or change the **Location**.</span></span> <span data-ttu-id="8982e-118">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="8982e-118">Select **Create**.</span></span>
* <span data-ttu-id="8982e-119">在下拉列表中选择 ASP.NET Core 2.2  。</span><span class="sxs-lookup"><span data-stu-id="8982e-119">Select **ASP.NET Core 2.2** in the drop down.</span></span> <span data-ttu-id="8982e-120">在模板列表中选择“Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="8982e-120">Select **Web Application** in the template list.</span></span>
* <span data-ttu-id="8982e-121">在“身份验证”下，选择“更改”再设置针对单个用户帐户的身份验证    。</span><span class="sxs-lookup"><span data-stu-id="8982e-121">Under **Authentication**, select **Change** and set the authentication to **Individual User Accounts**.</span></span> <span data-ttu-id="8982e-122">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="8982e-122">Select **OK**.</span></span>
* <span data-ttu-id="8982e-123">在“创建新的 ASP.NET Core Web 应用程序”窗口中，选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="8982e-123">In the **Create a new ASP.NET Core Web Application** window, select **Create**.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="8982e-124">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8982e-124">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="8982e-125">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="8982e-125">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="8982e-126">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="8982e-126">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="8982e-127">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8982e-127">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o WebApp1 -au Individual -uld
  code -r WebApp1
  ```

  * <span data-ttu-id="8982e-128">`dotnet new` 命令可在“WebApp1”文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="8982e-128">The `dotnet new` command creates a new Razor Pages project in the *WebApp1* folder.</span></span>
  * <span data-ttu-id="8982e-129">`-uld` 使用 LocalDB，而不是 SQLite。</span><span class="sxs-lookup"><span data-stu-id="8982e-129">`-uld` uses LocalDB instead of SQLite.</span></span> <span data-ttu-id="8982e-130">省略 `-uld` 以使用 SQLite。</span><span class="sxs-lookup"><span data-stu-id="8982e-130">Omit `-uld` to use SQLite.</span></span>
  * <span data-ttu-id="8982e-131">`-au Individual` 创建用于个人身份验证的代码。</span><span class="sxs-lookup"><span data-stu-id="8982e-131">`-au Individual` creates the code for Individual authentication.</span></span>
  * <span data-ttu-id="8982e-132">`code` 命令将在新 Visual Studio Code 实例中打开“WebApp1”文件夹  。</span><span class="sxs-lookup"><span data-stu-id="8982e-132">The `code` command opens the *WebApp1* folder in a new instance of Visual Studio Code.</span></span>

* <span data-ttu-id="8982e-133">一个对话框随即出现，其中包含：“‘WebApp1’中缺少进行生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="8982e-133">A dialog box appears with **Required assets to build and debug are missing from 'WebApp1'. Add them?**</span></span> <span data-ttu-id="8982e-134">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="8982e-134">Select **Yes**.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="8982e-135">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8982e-135">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="8982e-136">选择“文件”   > “新建解决方案”  。</span><span class="sxs-lookup"><span data-stu-id="8982e-136">Select **File** > **New Solution**.</span></span>
* <span data-ttu-id="8982e-137">在边栏中选择 .NET Core  > “应用”   。</span><span class="sxs-lookup"><span data-stu-id="8982e-137">Select **.NET Core** > **App** in the sidebar.</span></span> <span data-ttu-id="8982e-138">选择“Web 应用程序”模板  。</span><span class="sxs-lookup"><span data-stu-id="8982e-138">Select the **Web Application** template.</span></span> <span data-ttu-id="8982e-139">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="8982e-139">Select **Next**.</span></span>
* <span data-ttu-id="8982e-140">将“目标框架”下拉项设置为 .NET Core 2.2   。</span><span class="sxs-lookup"><span data-stu-id="8982e-140">Set the **Target Framework** drop down to **.NET Core 2.2**.</span></span> <span data-ttu-id="8982e-141">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="8982e-141">Select **Next**.</span></span>
* <span data-ttu-id="8982e-142">提供项目名称  。</span><span class="sxs-lookup"><span data-stu-id="8982e-142">Provide a **Project Name**.</span></span> <span data-ttu-id="8982e-143">确认或更改位置  。</span><span class="sxs-lookup"><span data-stu-id="8982e-143">Confirm or change the **Location**.</span></span> <span data-ttu-id="8982e-144">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="8982e-144">Select **Create**.</span></span>

---

## <a name="apply-migrations"></a><span data-ttu-id="8982e-145">应用迁移</span><span class="sxs-lookup"><span data-stu-id="8982e-145">Apply migrations</span></span>

* <span data-ttu-id="8982e-146">运行应用并选择“注册”链接  。</span><span class="sxs-lookup"><span data-stu-id="8982e-146">Run the app and select the **Register** link.</span></span>
* <span data-ttu-id="8982e-147">输入新帐户的电子邮件地址和密码，再选择“注册”  。</span><span class="sxs-lookup"><span data-stu-id="8982e-147">Enter the email and password for the new account, and then select **Register**.</span></span>
* <span data-ttu-id="8982e-148">按照说明操作来应用迁移。</span><span class="sxs-lookup"><span data-stu-id="8982e-148">Follow the instructions to apply migrations.</span></span>

[!INCLUDE[Forward request information when behind a proxy or load balancer section](includes/forwarded-headers-middleware.md)]

## <a name="use-secretmanager-to-store-tokens-assigned-by-login-providers"></a><span data-ttu-id="8982e-149">使用 SecretManager 存储登录提供程序分配的令牌</span><span class="sxs-lookup"><span data-stu-id="8982e-149">Use SecretManager to store tokens assigned by login providers</span></span>

<span data-ttu-id="8982e-150">社交登录提供程序在注册过程中分配“应用程序 ID”  和“应用程序机密”  。</span><span class="sxs-lookup"><span data-stu-id="8982e-150">Social login providers assign **Application Id** and **Application Secret** tokens during the registration process.</span></span> <span data-ttu-id="8982e-151">确切的令牌名称因提供程序而异。</span><span class="sxs-lookup"><span data-stu-id="8982e-151">The exact token names vary by provider.</span></span> <span data-ttu-id="8982e-152">这些令牌代表应用用来访问其 API 的凭据。</span><span class="sxs-lookup"><span data-stu-id="8982e-152">These tokens represent the credentials your app uses to access their API.</span></span> <span data-ttu-id="8982e-153">令牌构成“机密”，可利用[机密管理器](xref:security/app-secrets#secret-manager)将其链接到应用配置。</span><span class="sxs-lookup"><span data-stu-id="8982e-153">The tokens constitute the "secrets" that can be linked to your app configuration with the help of [Secret Manager](xref:security/app-secrets#secret-manager).</span></span> <span data-ttu-id="8982e-154">机密管理器是在配置文件（例如 appsettings.json  ）中存储令牌更安全替代方法。</span><span class="sxs-lookup"><span data-stu-id="8982e-154">Secret Manager is a more secure alternative to storing the tokens in a configuration file, such as *appsettings.json*.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8982e-155">机密管理器仅用于开发目的。</span><span class="sxs-lookup"><span data-stu-id="8982e-155">Secret Manager is for development purposes only.</span></span> <span data-ttu-id="8982e-156">可使用 [Azure Key Vault 配置提供程序](xref:security/key-vault-configuration)存储和保护 Azure 测试和生产机密。</span><span class="sxs-lookup"><span data-stu-id="8982e-156">You can store and protect Azure test and production secrets with the [Azure Key Vault configuration provider](xref:security/key-vault-configuration).</span></span>

<span data-ttu-id="8982e-157">按照[在 ASP.NET Core 中进行开发期间安全存储应用机密](xref:security/app-secrets)主题中的步骤进行操作，以便存储以下每个登录提供程序分配的令牌。</span><span class="sxs-lookup"><span data-stu-id="8982e-157">Follow the steps in [Safe storage of app secrets in development in ASP.NET Core](xref:security/app-secrets) topic to store tokens assigned by each login provider below.</span></span>

## <a name="setup-login-providers-required-by-your-application"></a><span data-ttu-id="8982e-158">应用程序所需的安装登录提供程序</span><span class="sxs-lookup"><span data-stu-id="8982e-158">Setup login providers required by your application</span></span>

<span data-ttu-id="8982e-159">使用以下主题配置应用程序，以使用相应的提供程序：</span><span class="sxs-lookup"><span data-stu-id="8982e-159">Use the following topics to configure your application to use the respective providers:</span></span>

* <span data-ttu-id="8982e-160">[Facebook](xref:security/authentication/facebook-logins) 相关说明</span><span class="sxs-lookup"><span data-stu-id="8982e-160">[Facebook](xref:security/authentication/facebook-logins) instructions</span></span>
* <span data-ttu-id="8982e-161">[Twitter](xref:security/authentication/twitter-logins) 相关说明</span><span class="sxs-lookup"><span data-stu-id="8982e-161">[Twitter](xref:security/authentication/twitter-logins) instructions</span></span>
* <span data-ttu-id="8982e-162">[Google](xref:security/authentication/google-logins) 相关说明</span><span class="sxs-lookup"><span data-stu-id="8982e-162">[Google](xref:security/authentication/google-logins) instructions</span></span>
* <span data-ttu-id="8982e-163">[Microsoft](xref:security/authentication/microsoft-logins) 相关说明</span><span class="sxs-lookup"><span data-stu-id="8982e-163">[Microsoft](xref:security/authentication/microsoft-logins) instructions</span></span>
* <span data-ttu-id="8982e-164">[其他提供程序](xref:security/authentication/otherlogins)相关说明</span><span class="sxs-lookup"><span data-stu-id="8982e-164">[Other provider](xref:security/authentication/otherlogins) instructions</span></span>

[!INCLUDE[](includes/chain-auth-providers.md)]

## <a name="optionally-set-password"></a><span data-ttu-id="8982e-165">选择性地设置密码</span><span class="sxs-lookup"><span data-stu-id="8982e-165">Optionally set password</span></span>

<span data-ttu-id="8982e-166">使用外部登录提供程序注册，即表明还没有向应用注册密码。</span><span class="sxs-lookup"><span data-stu-id="8982e-166">When you register with an external login provider, you don't have a password registered with the app.</span></span> <span data-ttu-id="8982e-167">这可让用户无需创建和记住站点密码，但也会使用户依赖外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="8982e-167">This alleviates you from creating and remembering a password for the site, but it also makes you dependent on the external login provider.</span></span> <span data-ttu-id="8982e-168">如果外部登录提供程序不可用，则无法登录网站。</span><span class="sxs-lookup"><span data-stu-id="8982e-168">If the external login provider is unavailable, you won't be able to sign in to the web site.</span></span>

<span data-ttu-id="8982e-169">使用外部提供程序在登录过程中设置的电子邮箱创建密码和登录：</span><span class="sxs-lookup"><span data-stu-id="8982e-169">To create a password and sign in using your email that you set during the sign in process with external providers:</span></span>

* <span data-ttu-id="8982e-170">选择右上角的“Hello &lt;电子邮件别名&gt;”链接，导航到“管理”视图   。</span><span class="sxs-lookup"><span data-stu-id="8982e-170">Select the **Hello &lt;email alias&gt;** link at the top-right corner to navigate to the **Manage** view.</span></span>

![Web 应用程序“管理”视图](index/_static/pass1a.png)

* <span data-ttu-id="8982e-172">选择“创建” </span><span class="sxs-lookup"><span data-stu-id="8982e-172">Select **Create**</span></span>

![“设置密码”页](index/_static/pass2a.png)

* <span data-ttu-id="8982e-174">设置一个有效密码，可以用此密码和邮箱登录。</span><span class="sxs-lookup"><span data-stu-id="8982e-174">Set a valid password and you can use this to sign in with your email.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8982e-175">后续步骤</span><span class="sxs-lookup"><span data-stu-id="8982e-175">Next steps</span></span>

* <span data-ttu-id="8982e-176">本文介绍了外部身份验证，并说明了向 ASP.NET Core 应用添加外部登录所需的先决条件。</span><span class="sxs-lookup"><span data-stu-id="8982e-176">This article introduced external authentication and explained the prerequisites required to add external logins to your ASP.NET Core app.</span></span>

* <span data-ttu-id="8982e-177">引用特定于提供程序的页，为应用所需的提供程序配置登录。</span><span class="sxs-lookup"><span data-stu-id="8982e-177">Reference provider-specific pages to configure logins for the providers required by your app.</span></span>

* <span data-ttu-id="8982e-178">可能需要保留有关用户及其访问和刷新令牌的其他数据。</span><span class="sxs-lookup"><span data-stu-id="8982e-178">You may want to persist additional data about the user and their access and refresh tokens.</span></span> <span data-ttu-id="8982e-179">有关详细信息，请参阅 <xref:security/authentication/social/additional-claims>。</span><span class="sxs-lookup"><span data-stu-id="8982e-179">For more information, see <xref:security/authentication/social/additional-claims>.</span></span>
