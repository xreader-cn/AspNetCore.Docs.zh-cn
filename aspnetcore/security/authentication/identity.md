---
title: ASP.NET Core 上的标识简介
author: rick-anderson
description: 将标识与 ASP.NET Core 应用配合使用。 了解如何设置密码要求（RequireDigit、RequiredLength、RequiredUniqueChars 等）。
ms.author: riande
ms.date: 12/7/2019
uid: security/authentication/identity
ms.openlocfilehash: 331ebe36eb4bb7fa694de8daa969bcabcab1c974
ms.sourcegitcommit: b3e1e31e5d8bdd94096cf27444594d4a7b065525
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/04/2019
ms.locfileid: "74803391"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="942ab-104">ASP.NET Core 上的标识简介</span><span class="sxs-lookup"><span data-stu-id="942ab-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="942ab-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="942ab-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="942ab-106">ASP.NET Core 标识：</span><span class="sxs-lookup"><span data-stu-id="942ab-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="942ab-107">是支持用户界面（UI）登录功能的 API。</span><span class="sxs-lookup"><span data-stu-id="942ab-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="942ab-108">管理用户、密码、配置文件数据、角色、声明、令牌、电子邮件确认等。</span><span class="sxs-lookup"><span data-stu-id="942ab-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="942ab-109">用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="942ab-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="942ab-110">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="942ab-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="942ab-111">GitHub 上提供了[标识源代码](https://github.com/aspnet/AspNetCore/tree/master/src/Identity)。</span><span class="sxs-lookup"><span data-stu-id="942ab-111">The [Identity source code](https://github.com/aspnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="942ab-112">[基架标识](xref:security/authentication/scaffold-identity)并查看生成的文件以查看模板与标识的交互。</span><span class="sxs-lookup"><span data-stu-id="942ab-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="942ab-113">通常使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="942ab-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="942ab-114">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="942ab-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="942ab-115">本主题介绍如何使用标识注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="942ab-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="942ab-116">有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="942ab-116">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="942ab-117">[Microsoft 标识平台](/azure/active-directory/develop/)是：</span><span class="sxs-lookup"><span data-stu-id="942ab-117">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="942ab-118">Azure Active Directory （Azure AD）开发人员平台的演变。</span><span class="sxs-lookup"><span data-stu-id="942ab-118">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="942ab-119">与 ASP.NET Core 标识无关。</span><span class="sxs-lookup"><span data-stu-id="942ab-119">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="942ab-120">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)（[如何下载）](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="942ab-120">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="942ab-121">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="942ab-121">Create a Web app with authentication</span></span>

<span data-ttu-id="942ab-122">使用单个用户帐户创建一个 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="942ab-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="942ab-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="942ab-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="942ab-124">选择“文件”>“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="942ab-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="942ab-125">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="942ab-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="942ab-126">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="942ab-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="942ab-127">单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="942ab-127">Click **OK**.</span></span>
* <span data-ttu-id="942ab-128">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="942ab-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="942ab-129">选择**单个用户帐户**，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="942ab-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="942ab-130">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="942ab-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="942ab-131">上述命令使用 SQLite 创建 Razor web 应用。</span><span class="sxs-lookup"><span data-stu-id="942ab-131">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="942ab-132">若要创建具有 LocalDB 的 web 应用，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="942ab-132">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="942ab-133">生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="942ab-133">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="942ab-134">标识 Razor 类库公开 `Identity` 区域的终结点。</span><span class="sxs-lookup"><span data-stu-id="942ab-134">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="942ab-135">例如：</span><span class="sxs-lookup"><span data-stu-id="942ab-135">For example:</span></span>

* <span data-ttu-id="942ab-136">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="942ab-136">/Identity/Account/Login</span></span>
* <span data-ttu-id="942ab-137">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="942ab-137">/Identity/Account/Logout</span></span>
* <span data-ttu-id="942ab-138">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="942ab-138">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="942ab-139">应用迁移</span><span class="sxs-lookup"><span data-stu-id="942ab-139">Apply migrations</span></span>

<span data-ttu-id="942ab-140">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="942ab-140">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="942ab-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="942ab-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="942ab-142">在包管理器控制台中运行以下命令（PMC）：</span><span class="sxs-lookup"><span data-stu-id="942ab-142">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="942ab-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="942ab-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="942ab-144">使用 SQLite 时，此步骤不需要迁移。</span><span class="sxs-lookup"><span data-stu-id="942ab-144">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="942ab-145">对于 LocalDB，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="942ab-145">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="942ab-146">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="942ab-146">Test Register and Login</span></span>

<span data-ttu-id="942ab-147">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="942ab-147">Run the app and register a user.</span></span> <span data-ttu-id="942ab-148">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="942ab-148">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="942ab-149">配置标识服务</span><span class="sxs-lookup"><span data-stu-id="942ab-149">Configure Identity services</span></span>

<span data-ttu-id="942ab-150">在 `ConfigureServices`中添加服务。</span><span class="sxs-lookup"><span data-stu-id="942ab-150">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="942ab-151">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="942ab-151">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="942ab-152">前面突出显示的代码用默认选项值配置标识。</span><span class="sxs-lookup"><span data-stu-id="942ab-152">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="942ab-153">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="942ab-153">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="942ab-154">通过调用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>来启用标识。</span><span class="sxs-lookup"><span data-stu-id="942ab-154">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="942ab-155">`UseAuthentication` 将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="942ab-155">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="942ab-156">模板生成的应用不使用[授权](xref:security/authorization/secure-data)。</span><span class="sxs-lookup"><span data-stu-id="942ab-156">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="942ab-157">包括 `app.UseAuthorization` 以确保在应用添加授权时，按正确的顺序添加。</span><span class="sxs-lookup"><span data-stu-id="942ab-157">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="942ab-158">必须按前面的代码中所示的顺序调用 `UseRouting`、`UseAuthentication`、`UseAuthorization`和 `UseEndpoints`。</span><span class="sxs-lookup"><span data-stu-id="942ab-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="942ab-159">有关 `IdentityOptions` 和 `Startup`的详细信息，请参阅 <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="942ab-159">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="942ab-160">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="942ab-160">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="942ab-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="942ab-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="942ab-162">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="942ab-162">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="942ab-163">按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="942ab-163">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="942ab-164">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="942ab-164">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="942ab-165">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="942ab-165">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="942ab-166">否则，请使用 `ApplicationDbContext`的正确命名空间：</span><span class="sxs-lookup"><span data-stu-id="942ab-166">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="942ab-167">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="942ab-167">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="942ab-168">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="942ab-168">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="942ab-169">有关基架标识的详细信息，请参阅[使用授权将基架标识导入 Razor 项目](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="942ab-169">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="942ab-170">检查注册</span><span class="sxs-lookup"><span data-stu-id="942ab-170">Examine Register</span></span>

<span data-ttu-id="942ab-171">当用户单击 "**注册**" 链接时，将调用 `RegisterModel.OnPostAsync` 操作。</span><span class="sxs-lookup"><span data-stu-id="942ab-171">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="942ab-172">用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对 `_userManager` 对象创建的。</span><span class="sxs-lookup"><span data-stu-id="942ab-172">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="942ab-173">`_userManager` 由依赖关系注入提供）：</span><span class="sxs-lookup"><span data-stu-id="942ab-173">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="942ab-174">如果已成功创建用户，则会通过调用 `_signInManager.SignInAsync`登录该用户。</span><span class="sxs-lookup"><span data-stu-id="942ab-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="942ab-175">请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="942ab-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="942ab-176">Log in</span><span class="sxs-lookup"><span data-stu-id="942ab-176">Log in</span></span>

<span data-ttu-id="942ab-177">发生下列情况时，会显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="942ab-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="942ab-178">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="942ab-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="942ab-179">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="942ab-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="942ab-180">提交“登录”页上的表单时，会调用 `OnPostAsync` 操作。</span><span class="sxs-lookup"><span data-stu-id="942ab-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="942ab-181">会在 `_signInManager` 对象（通过注入依赖项的方式提供）上调用 `PasswordSignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="942ab-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="942ab-182">Base `Controller` 类公开可从控制器方法访问的 `User` 属性。</span><span class="sxs-lookup"><span data-stu-id="942ab-182">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="942ab-183">例如，可以枚举 `User.Claims` 并进行授权决策。</span><span class="sxs-lookup"><span data-stu-id="942ab-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="942ab-184">有关更多信息，请参见<xref:security/authorization/introduction>。</span><span class="sxs-lookup"><span data-stu-id="942ab-184">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="942ab-185">Log out</span><span class="sxs-lookup"><span data-stu-id="942ab-185">Log out</span></span>

<span data-ttu-id="942ab-186">"**注销**" 链接调用 `LogoutModel.OnPost` 操作。</span><span class="sxs-lookup"><span data-stu-id="942ab-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="942ab-187">在前面的代码中，代码 `return RedirectToPage();` 需要是重定向，以便浏览器执行新请求并更新用户的标识。</span><span class="sxs-lookup"><span data-stu-id="942ab-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="942ab-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="942ab-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="942ab-189">在 *Pages/Shared/_LoginPartial.cshtml* 中指定 post：</span><span class="sxs-lookup"><span data-stu-id="942ab-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="942ab-190">测试标识</span><span class="sxs-lookup"><span data-stu-id="942ab-190">Test Identity</span></span>

<span data-ttu-id="942ab-191">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="942ab-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="942ab-192">若要测试标识，请添加[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)：</span><span class="sxs-lookup"><span data-stu-id="942ab-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="942ab-193">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="942ab-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="942ab-194">你将重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="942ab-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="942ab-195">浏览标识</span><span class="sxs-lookup"><span data-stu-id="942ab-195">Explore Identity</span></span>

<span data-ttu-id="942ab-196">更详细地了解标识：</span><span class="sxs-lookup"><span data-stu-id="942ab-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="942ab-197">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="942ab-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="942ab-198">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="942ab-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="942ab-199">标识组件</span><span class="sxs-lookup"><span data-stu-id="942ab-199">Identity Components</span></span>

<span data-ttu-id="942ab-200">所有标识相关 NuGet 包都包含在[ASP.NET Core 共享框架](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)中。</span><span class="sxs-lookup"><span data-stu-id="942ab-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="942ab-201">标识的主包为[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="942ab-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="942ab-202">此包包含 ASP.NET Core 标识的核心接口集，是 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 提供的。</span><span class="sxs-lookup"><span data-stu-id="942ab-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="942ab-203">迁移到 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="942ab-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="942ab-204">有关迁移现有标识存储的详细信息和指南，请参阅[迁移身份验证和标识](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="942ab-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="942ab-205">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="942ab-205">Setting password strength</span></span>

<span data-ttu-id="942ab-206">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="942ab-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="942ab-207">AddDefaultIdentity 和 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="942ab-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="942ab-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="942ab-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="942ab-209">调用 `AddDefaultIdentity` 类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="942ab-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="942ab-210">有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="942ab-210">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="next-steps"></a><span data-ttu-id="942ab-211">后续步骤</span><span class="sxs-lookup"><span data-stu-id="942ab-211">Next Steps</span></span>

* [<span data-ttu-id="942ab-212">配置标识</span><span class="sxs-lookup"><span data-stu-id="942ab-212">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="942ab-213">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="942ab-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="942ab-214">ASP.NET Core 标识是将登录功能添加到 ASP.NET Core 应用的成员资格系统。</span><span class="sxs-lookup"><span data-stu-id="942ab-214">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="942ab-215">用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="942ab-215">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="942ab-216">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="942ab-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="942ab-217">可以使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="942ab-217">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="942ab-218">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="942ab-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="942ab-219">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)（[如何下载）](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="942ab-219">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="942ab-220">本主题介绍如何使用标识注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="942ab-220">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="942ab-221">有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="942ab-221">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="942ab-222">AddDefaultIdentity 和 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="942ab-222">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="942ab-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="942ab-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="942ab-224">调用 `AddDefaultIdentity` 类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="942ab-224">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="942ab-225">有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="942ab-225">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="942ab-226">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="942ab-226">Create a Web app with authentication</span></span>

<span data-ttu-id="942ab-227">使用单个用户帐户创建一个 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="942ab-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="942ab-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="942ab-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="942ab-229">选择“文件”>“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="942ab-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="942ab-230">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="942ab-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="942ab-231">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="942ab-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="942ab-232">单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="942ab-232">Click **OK**.</span></span>
* <span data-ttu-id="942ab-233">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="942ab-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="942ab-234">选择**单个用户帐户**，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="942ab-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="942ab-235">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="942ab-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="942ab-236">生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="942ab-236">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="942ab-237">标识 Razor 类库公开 `Identity` 区域的终结点。</span><span class="sxs-lookup"><span data-stu-id="942ab-237">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="942ab-238">例如：</span><span class="sxs-lookup"><span data-stu-id="942ab-238">For example:</span></span>

* <span data-ttu-id="942ab-239">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="942ab-239">/Identity/Account/Login</span></span>
* <span data-ttu-id="942ab-240">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="942ab-240">/Identity/Account/Logout</span></span>
* <span data-ttu-id="942ab-241">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="942ab-241">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="942ab-242">应用迁移</span><span class="sxs-lookup"><span data-stu-id="942ab-242">Apply migrations</span></span>

<span data-ttu-id="942ab-243">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="942ab-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="942ab-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="942ab-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="942ab-245">在包管理器控制台中运行以下命令（PMC）：</span><span class="sxs-lookup"><span data-stu-id="942ab-245">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="942ab-246">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="942ab-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="942ab-247">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="942ab-247">Test Register and Login</span></span>

<span data-ttu-id="942ab-248">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="942ab-248">Run the app and register a user.</span></span> <span data-ttu-id="942ab-249">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="942ab-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="942ab-250">配置标识服务</span><span class="sxs-lookup"><span data-stu-id="942ab-250">Configure Identity services</span></span>

<span data-ttu-id="942ab-251">在 `ConfigureServices`中添加服务。</span><span class="sxs-lookup"><span data-stu-id="942ab-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="942ab-252">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="942ab-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="942ab-253">前面的代码用默认选项值配置标识。</span><span class="sxs-lookup"><span data-stu-id="942ab-253">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="942ab-254">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="942ab-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="942ab-255">通过调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)来启用标识。</span><span class="sxs-lookup"><span data-stu-id="942ab-255">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="942ab-256">`UseAuthentication` 将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="942ab-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="942ab-257">有关详细信息，请参阅[IdentityOptions 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="942ab-257">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="942ab-258">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="942ab-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="942ab-259">按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="942ab-259">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="942ab-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="942ab-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="942ab-261">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="942ab-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="942ab-262">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="942ab-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="942ab-263">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="942ab-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="942ab-264">否则，请使用 `ApplicationDbContext`的正确命名空间：</span><span class="sxs-lookup"><span data-stu-id="942ab-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="942ab-265">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="942ab-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="942ab-266">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="942ab-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="942ab-267">检查注册</span><span class="sxs-lookup"><span data-stu-id="942ab-267">Examine Register</span></span>

<span data-ttu-id="942ab-268">当用户单击 "**注册**" 链接时，将调用 `RegisterModel.OnPostAsync` 操作。</span><span class="sxs-lookup"><span data-stu-id="942ab-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="942ab-269">用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对 `_userManager` 对象创建的。</span><span class="sxs-lookup"><span data-stu-id="942ab-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="942ab-270">`_userManager` 由依赖关系注入提供）：</span><span class="sxs-lookup"><span data-stu-id="942ab-270">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="942ab-271">如果已成功创建用户，则会通过调用 `_signInManager.SignInAsync`登录该用户。</span><span class="sxs-lookup"><span data-stu-id="942ab-271">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="942ab-272">**注意：** 请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="942ab-272">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="942ab-273">Log in</span><span class="sxs-lookup"><span data-stu-id="942ab-273">Log in</span></span>

<span data-ttu-id="942ab-274">发生下列情况时，会显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="942ab-274">The Login form is displayed when:</span></span>

* <span data-ttu-id="942ab-275">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="942ab-275">The **Log in** link is selected.</span></span>
* <span data-ttu-id="942ab-276">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="942ab-276">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="942ab-277">提交“登录”页上的表单时，会调用 `OnPostAsync` 操作。</span><span class="sxs-lookup"><span data-stu-id="942ab-277">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="942ab-278">会在 `_signInManager` 对象（通过注入依赖项的方式提供）上调用 `PasswordSignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="942ab-278">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="942ab-279">基 `Controller` 类公开了一个 `User` 属性，方便你通过控制器方法对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="942ab-279">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="942ab-280">例如，可以枚举 `User.Claims` 并进行授权决策。</span><span class="sxs-lookup"><span data-stu-id="942ab-280">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="942ab-281">有关更多信息，请参见<xref:security/authorization/introduction>。</span><span class="sxs-lookup"><span data-stu-id="942ab-281">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="942ab-282">Log out</span><span class="sxs-lookup"><span data-stu-id="942ab-282">Log out</span></span>

<span data-ttu-id="942ab-283">"**注销**" 链接调用 `LogoutModel.OnPost` 操作。</span><span class="sxs-lookup"><span data-stu-id="942ab-283">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="942ab-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="942ab-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="942ab-285">在 *Pages/Shared/_LoginPartial.cshtml* 中指定 post：</span><span class="sxs-lookup"><span data-stu-id="942ab-285">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="942ab-286">测试标识</span><span class="sxs-lookup"><span data-stu-id="942ab-286">Test Identity</span></span>

<span data-ttu-id="942ab-287">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="942ab-287">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="942ab-288">若要测试标识，请将[`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)添加到 "隐私" 页。</span><span class="sxs-lookup"><span data-stu-id="942ab-288">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="942ab-289">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="942ab-289">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="942ab-290">你将重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="942ab-290">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="942ab-291">浏览标识</span><span class="sxs-lookup"><span data-stu-id="942ab-291">Explore Identity</span></span>

<span data-ttu-id="942ab-292">更详细地了解标识：</span><span class="sxs-lookup"><span data-stu-id="942ab-292">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="942ab-293">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="942ab-293">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="942ab-294">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="942ab-294">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="942ab-295">标识组件</span><span class="sxs-lookup"><span data-stu-id="942ab-295">Identity Components</span></span>

<span data-ttu-id="942ab-296">所有标识相关 NuGet 包都包含在[AspNetCore 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="942ab-296">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="942ab-297">标识的主包为[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="942ab-297">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="942ab-298">此包包含 ASP.NET Core 标识的核心接口集，是 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 提供的。</span><span class="sxs-lookup"><span data-stu-id="942ab-298">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="942ab-299">迁移到 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="942ab-299">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="942ab-300">有关迁移现有标识存储的详细信息和指南，请参阅[迁移身份验证和标识](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="942ab-300">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="942ab-301">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="942ab-301">Setting password strength</span></span>

<span data-ttu-id="942ab-302">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="942ab-302">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="942ab-303">后续步骤</span><span class="sxs-lookup"><span data-stu-id="942ab-303">Next Steps</span></span>

* [<span data-ttu-id="942ab-304">配置标识</span><span class="sxs-lookup"><span data-stu-id="942ab-304">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
