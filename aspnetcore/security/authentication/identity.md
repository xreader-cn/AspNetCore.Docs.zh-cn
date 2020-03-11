---
title: ASP.NET Core 上的标识简介
author: rick-anderson
description: 将标识与 ASP.NET Core 应用配合使用。 了解如何设置密码要求（RequireDigit、RequiredLength、RequiredUniqueChars 等）。
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 2e0723d34a09109a034f3375c4e94aedab2a5427
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78653154"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="998c4-104">ASP.NET Core 上的标识简介</span><span class="sxs-lookup"><span data-stu-id="998c4-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="998c4-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="998c4-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="998c4-106">ASP.NET Core 标识：</span><span class="sxs-lookup"><span data-stu-id="998c4-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="998c4-107">是支持用户界面（UI）登录功能的 API。</span><span class="sxs-lookup"><span data-stu-id="998c4-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="998c4-108">管理用户、密码、配置文件数据、角色、声明、令牌、电子邮件确认等。</span><span class="sxs-lookup"><span data-stu-id="998c4-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="998c4-109">用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="998c4-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="998c4-110">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="998c4-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="998c4-111">GitHub 上提供了[标识源代码](https://github.com/dotnet/AspNetCore/tree/master/src/Identity)。</span><span class="sxs-lookup"><span data-stu-id="998c4-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="998c4-112">[基架标识](xref:security/authentication/scaffold-identity)并查看生成的文件以查看模板与标识的交互。</span><span class="sxs-lookup"><span data-stu-id="998c4-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="998c4-113">通常使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="998c4-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="998c4-114">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="998c4-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="998c4-115">本主题介绍如何使用标识注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="998c4-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="998c4-116">有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="998c4-116">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="998c4-117">[Microsoft 标识平台](/azure/active-directory/develop/)是：</span><span class="sxs-lookup"><span data-stu-id="998c4-117">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="998c4-118">Azure Active Directory （Azure AD）开发人员平台的演变。</span><span class="sxs-lookup"><span data-stu-id="998c4-118">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="998c4-119">与 ASP.NET Core 标识无关。</span><span class="sxs-lookup"><span data-stu-id="998c4-119">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="998c4-120">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)（[如何下载）](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="998c4-120">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="998c4-121">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="998c4-121">Create a Web app with authentication</span></span>

<span data-ttu-id="998c4-122">使用单个用户帐户创建一个 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="998c4-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="998c4-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="998c4-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="998c4-124">选择 "**文件**" >**新建**>**项目**"。</span><span class="sxs-lookup"><span data-stu-id="998c4-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="998c4-125">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="998c4-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="998c4-126">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="998c4-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="998c4-127">单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="998c4-127">Click **OK**.</span></span>
* <span data-ttu-id="998c4-128">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="998c4-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="998c4-129">选择**单个用户帐户**，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="998c4-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="998c4-130">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="998c4-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="998c4-131">上述命令使用 SQLite 创建 Razor web 应用。</span><span class="sxs-lookup"><span data-stu-id="998c4-131">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="998c4-132">若要创建具有 LocalDB 的 web 应用，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="998c4-132">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="998c4-133">生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="998c4-133">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="998c4-134">标识 Razor 类库公开 `Identity` 区域的终结点。</span><span class="sxs-lookup"><span data-stu-id="998c4-134">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="998c4-135">例如：</span><span class="sxs-lookup"><span data-stu-id="998c4-135">For example:</span></span>

* <span data-ttu-id="998c4-136">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="998c4-136">/Identity/Account/Login</span></span>
* <span data-ttu-id="998c4-137">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="998c4-137">/Identity/Account/Logout</span></span>
* <span data-ttu-id="998c4-138">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="998c4-138">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="998c4-139">应用迁移</span><span class="sxs-lookup"><span data-stu-id="998c4-139">Apply migrations</span></span>

<span data-ttu-id="998c4-140">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="998c4-140">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="998c4-141">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="998c4-141">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="998c4-142">在包管理器控制台中运行以下命令（PMC）：</span><span class="sxs-lookup"><span data-stu-id="998c4-142">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="998c4-143">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="998c4-143">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="998c4-144">使用 SQLite 时，此步骤不需要迁移。</span><span class="sxs-lookup"><span data-stu-id="998c4-144">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="998c4-145">对于 LocalDB，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="998c4-145">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="998c4-146">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="998c4-146">Test Register and Login</span></span>

<span data-ttu-id="998c4-147">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="998c4-147">Run the app and register a user.</span></span> <span data-ttu-id="998c4-148">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="998c4-148">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="998c4-149">配置标识服务</span><span class="sxs-lookup"><span data-stu-id="998c4-149">Configure Identity services</span></span>

<span data-ttu-id="998c4-150">在 `ConfigureServices`中添加服务。</span><span class="sxs-lookup"><span data-stu-id="998c4-150">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="998c4-151">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="998c4-151">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="998c4-152">前面突出显示的代码用默认选项值配置标识。</span><span class="sxs-lookup"><span data-stu-id="998c4-152">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="998c4-153">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="998c4-153">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="998c4-154">通过调用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>来启用标识。</span><span class="sxs-lookup"><span data-stu-id="998c4-154">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="998c4-155">`UseAuthentication` 将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="998c4-155">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="998c4-156">模板生成的应用不使用[授权](xref:security/authorization/secure-data)。</span><span class="sxs-lookup"><span data-stu-id="998c4-156">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="998c4-157">包括 `app.UseAuthorization` 以确保在应用添加授权时，按正确的顺序添加。</span><span class="sxs-lookup"><span data-stu-id="998c4-157">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="998c4-158">必须按前面的代码中所示的顺序调用 `UseRouting`、`UseAuthentication`、`UseAuthorization`和 `UseEndpoints`。</span><span class="sxs-lookup"><span data-stu-id="998c4-158">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="998c4-159">有关 `IdentityOptions` 和 `Startup`的详细信息，请参阅 <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="998c4-159">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="998c4-160">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="998c4-160">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="998c4-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="998c4-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="998c4-162">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="998c4-162">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="998c4-163">按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="998c4-163">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="998c4-164">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="998c4-164">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="998c4-165">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="998c4-165">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="998c4-166">否则，请使用 `ApplicationDbContext`的正确命名空间：</span><span class="sxs-lookup"><span data-stu-id="998c4-166">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="998c4-167">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="998c4-167">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="998c4-168">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="998c4-168">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="998c4-169">有关基架标识的详细信息，请参阅[使用授权将基架标识导入 Razor 项目](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="998c4-169">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="998c4-170">检查注册</span><span class="sxs-lookup"><span data-stu-id="998c4-170">Examine Register</span></span>

<span data-ttu-id="998c4-171">当用户单击 "**注册**" 链接时，将调用 `RegisterModel.OnPostAsync` 操作。</span><span class="sxs-lookup"><span data-stu-id="998c4-171">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="998c4-172">用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对 `_userManager` 对象创建的。</span><span class="sxs-lookup"><span data-stu-id="998c4-172">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="998c4-173">`_userManager` 由依赖关系注入提供）：</span><span class="sxs-lookup"><span data-stu-id="998c4-173">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="998c4-174">如果已成功创建用户，则会通过调用 `_signInManager.SignInAsync`登录该用户。</span><span class="sxs-lookup"><span data-stu-id="998c4-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="998c4-175">请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="998c4-175">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="998c4-176">登录</span><span class="sxs-lookup"><span data-stu-id="998c4-176">Log in</span></span>

<span data-ttu-id="998c4-177">发生下列情况时，会显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="998c4-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="998c4-178">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="998c4-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="998c4-179">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="998c4-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="998c4-180">提交登录页上的窗体时，将调用 `OnPostAsync` 操作。</span><span class="sxs-lookup"><span data-stu-id="998c4-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="998c4-181">对 `_signInManager` 对象（由依赖关系注入提供）调用 `PasswordSignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="998c4-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="998c4-182">Base `Controller` 类公开可从控制器方法访问的 `User` 属性。</span><span class="sxs-lookup"><span data-stu-id="998c4-182">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="998c4-183">例如，可以枚举 `User.Claims` 并做出授权决策。</span><span class="sxs-lookup"><span data-stu-id="998c4-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="998c4-184">有关详细信息，请参阅 <xref:security/authorization/introduction>。</span><span class="sxs-lookup"><span data-stu-id="998c4-184">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="998c4-185">注销</span><span class="sxs-lookup"><span data-stu-id="998c4-185">Log out</span></span>

<span data-ttu-id="998c4-186">"**注销**" 链接调用 `LogoutModel.OnPost` 操作。</span><span class="sxs-lookup"><span data-stu-id="998c4-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="998c4-187">在前面的代码中，代码 `return RedirectToPage();` 需要是重定向，以便浏览器执行新请求并更新用户的标识。</span><span class="sxs-lookup"><span data-stu-id="998c4-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="998c4-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="998c4-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="998c4-189">Post 在*Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="998c4-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="998c4-190">测试标识</span><span class="sxs-lookup"><span data-stu-id="998c4-190">Test Identity</span></span>

<span data-ttu-id="998c4-191">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="998c4-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="998c4-192">若要测试标识，请添加[`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)：</span><span class="sxs-lookup"><span data-stu-id="998c4-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="998c4-193">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="998c4-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="998c4-194">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="998c4-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="998c4-195">浏览标识</span><span class="sxs-lookup"><span data-stu-id="998c4-195">Explore Identity</span></span>

<span data-ttu-id="998c4-196">更详细地了解标识：</span><span class="sxs-lookup"><span data-stu-id="998c4-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="998c4-197">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="998c4-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="998c4-198">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="998c4-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="998c4-199">标识组件</span><span class="sxs-lookup"><span data-stu-id="998c4-199">Identity Components</span></span>

<span data-ttu-id="998c4-200">所有标识相关 NuGet 包都包含在[ASP.NET Core 共享框架](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)中。</span><span class="sxs-lookup"><span data-stu-id="998c4-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="998c4-201">标识的主包为[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="998c4-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="998c4-202">此程序包包含用于 ASP.NET Core 标识的核心接口集，由 `Microsoft.AspNetCore.Identity.EntityFrameworkCore`包含。</span><span class="sxs-lookup"><span data-stu-id="998c4-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="998c4-203">迁移到 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="998c4-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="998c4-204">有关迁移现有标识存储的详细信息和指南，请参阅[迁移身份验证和标识](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="998c4-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="998c4-205">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="998c4-205">Setting password strength</span></span>

<span data-ttu-id="998c4-206">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="998c4-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="998c4-207">AddDefaultIdentity 和 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="998c4-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="998c4-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="998c4-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="998c4-209">调用 `AddDefaultIdentity` 类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="998c4-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="998c4-210">有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="998c4-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="998c4-211">禁止发布静态标识资产</span><span class="sxs-lookup"><span data-stu-id="998c4-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="998c4-212">若要防止将静态标识资产（用于标识 UI 的样式表和 JavaScript 文件）发布到 web 根目录，请将以下 `ResolveStaticWebAssetsInputsDependsOn` 属性和 `RemoveIdentityAssets` 目标添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="998c4-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

```xml
<PropertyGroup>
  <ResolveStaticWebAssetsInputsDependsOn>RemoveIdentityAssets</ResolveStaticWebAssetsInputsDependsOn>
</PropertyGroup>

<Target Name="RemoveIdentityAssets">
  <ItemGroup>
    <StaticWebAsset Remove="@(StaticWebAsset)" Condition="%(SourceId) == 'Microsoft.AspNetCore.Identity.UI'" />
  </ItemGroup>
</Target>
```

## <a name="next-steps"></a><span data-ttu-id="998c4-213">后续步骤</span><span class="sxs-lookup"><span data-stu-id="998c4-213">Next Steps</span></span>

* <span data-ttu-id="998c4-214">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131)，了解如何使用 SQLite 配置标识。</span><span class="sxs-lookup"><span data-stu-id="998c4-214">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="998c4-215">配置标识</span><span class="sxs-lookup"><span data-stu-id="998c4-215">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="998c4-216">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="998c4-216">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="998c4-217">ASP.NET Core 标识是将登录功能添加到 ASP.NET Core 应用的成员资格系统。</span><span class="sxs-lookup"><span data-stu-id="998c4-217">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="998c4-218">用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="998c4-218">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="998c4-219">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="998c4-219">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="998c4-220">可以使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="998c4-220">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="998c4-221">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="998c4-221">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="998c4-222">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)（[如何下载）](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="998c4-222">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="998c4-223">本主题介绍如何使用标识注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="998c4-223">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="998c4-224">有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="998c4-224">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="998c4-225">AddDefaultIdentity 和 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="998c4-225">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="998c4-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="998c4-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="998c4-227">调用 `AddDefaultIdentity` 类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="998c4-227">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="998c4-228">有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="998c4-228">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="998c4-229">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="998c4-229">Create a Web app with authentication</span></span>

<span data-ttu-id="998c4-230">使用单个用户帐户创建一个 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="998c4-230">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="998c4-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="998c4-231">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="998c4-232">选择 "**文件**" >**新建**>**项目**"。</span><span class="sxs-lookup"><span data-stu-id="998c4-232">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="998c4-233">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="998c4-233">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="998c4-234">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="998c4-234">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="998c4-235">单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="998c4-235">Click **OK**.</span></span>
* <span data-ttu-id="998c4-236">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="998c4-236">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="998c4-237">选择**单个用户帐户**，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="998c4-237">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="998c4-238">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="998c4-238">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="998c4-239">生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="998c4-239">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="998c4-240">标识 Razor 类库公开 `Identity` 区域的终结点。</span><span class="sxs-lookup"><span data-stu-id="998c4-240">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="998c4-241">例如：</span><span class="sxs-lookup"><span data-stu-id="998c4-241">For example:</span></span>

* <span data-ttu-id="998c4-242">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="998c4-242">/Identity/Account/Login</span></span>
* <span data-ttu-id="998c4-243">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="998c4-243">/Identity/Account/Logout</span></span>
* <span data-ttu-id="998c4-244">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="998c4-244">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="998c4-245">应用迁移</span><span class="sxs-lookup"><span data-stu-id="998c4-245">Apply migrations</span></span>

<span data-ttu-id="998c4-246">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="998c4-246">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="998c4-247">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="998c4-247">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="998c4-248">在包管理器控制台中运行以下命令（PMC）：</span><span class="sxs-lookup"><span data-stu-id="998c4-248">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="998c4-249">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="998c4-249">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="998c4-250">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="998c4-250">Test Register and Login</span></span>

<span data-ttu-id="998c4-251">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="998c4-251">Run the app and register a user.</span></span> <span data-ttu-id="998c4-252">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="998c4-252">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="998c4-253">配置标识服务</span><span class="sxs-lookup"><span data-stu-id="998c4-253">Configure Identity services</span></span>

<span data-ttu-id="998c4-254">在 `ConfigureServices`中添加服务。</span><span class="sxs-lookup"><span data-stu-id="998c4-254">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="998c4-255">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="998c4-255">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="998c4-256">前面的代码用默认选项值配置标识。</span><span class="sxs-lookup"><span data-stu-id="998c4-256">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="998c4-257">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="998c4-257">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="998c4-258">通过调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)来启用标识。</span><span class="sxs-lookup"><span data-stu-id="998c4-258">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="998c4-259">`UseAuthentication` 将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="998c4-259">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="998c4-260">有关详细信息，请参阅[IdentityOptions 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="998c4-260">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="998c4-261">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="998c4-261">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="998c4-262">按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="998c4-262">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="998c4-263">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="998c4-263">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="998c4-264">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="998c4-264">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="998c4-265">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="998c4-265">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="998c4-266">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="998c4-266">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="998c4-267">否则，请使用 `ApplicationDbContext`的正确命名空间：</span><span class="sxs-lookup"><span data-stu-id="998c4-267">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="998c4-268">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="998c4-268">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="998c4-269">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="998c4-269">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="998c4-270">检查注册</span><span class="sxs-lookup"><span data-stu-id="998c4-270">Examine Register</span></span>

<span data-ttu-id="998c4-271">当用户单击 "**注册**" 链接时，将调用 `RegisterModel.OnPostAsync` 操作。</span><span class="sxs-lookup"><span data-stu-id="998c4-271">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="998c4-272">用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对 `_userManager` 对象创建的。</span><span class="sxs-lookup"><span data-stu-id="998c4-272">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="998c4-273">`_userManager` 由依赖关系注入提供）：</span><span class="sxs-lookup"><span data-stu-id="998c4-273">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="998c4-274">如果已成功创建用户，则会通过调用 `_signInManager.SignInAsync`登录该用户。</span><span class="sxs-lookup"><span data-stu-id="998c4-274">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="998c4-275">**注意：** 请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="998c4-275">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="998c4-276">登录</span><span class="sxs-lookup"><span data-stu-id="998c4-276">Log in</span></span>

<span data-ttu-id="998c4-277">发生下列情况时，会显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="998c4-277">The Login form is displayed when:</span></span>

* <span data-ttu-id="998c4-278">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="998c4-278">The **Log in** link is selected.</span></span>
* <span data-ttu-id="998c4-279">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="998c4-279">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="998c4-280">提交登录页上的窗体时，将调用 `OnPostAsync` 操作。</span><span class="sxs-lookup"><span data-stu-id="998c4-280">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="998c4-281">对 `_signInManager` 对象（由依赖关系注入提供）调用 `PasswordSignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="998c4-281">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="998c4-282">Base `Controller` 类公开可从控制器方法访问的 `User` 属性。</span><span class="sxs-lookup"><span data-stu-id="998c4-282">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="998c4-283">例如，可以枚举 `User.Claims` 并做出授权决策。</span><span class="sxs-lookup"><span data-stu-id="998c4-283">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="998c4-284">有关详细信息，请参阅 <xref:security/authorization/introduction>。</span><span class="sxs-lookup"><span data-stu-id="998c4-284">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="998c4-285">注销</span><span class="sxs-lookup"><span data-stu-id="998c4-285">Log out</span></span>

<span data-ttu-id="998c4-286">"**注销**" 链接调用 `LogoutModel.OnPost` 操作。</span><span class="sxs-lookup"><span data-stu-id="998c4-286">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="998c4-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="998c4-287">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="998c4-288">Post 在*Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="998c4-288">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="998c4-289">测试标识</span><span class="sxs-lookup"><span data-stu-id="998c4-289">Test Identity</span></span>

<span data-ttu-id="998c4-290">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="998c4-290">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="998c4-291">若要测试标识，请将[`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)添加到 "隐私" 页。</span><span class="sxs-lookup"><span data-stu-id="998c4-291">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="998c4-292">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="998c4-292">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="998c4-293">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="998c4-293">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="998c4-294">浏览标识</span><span class="sxs-lookup"><span data-stu-id="998c4-294">Explore Identity</span></span>

<span data-ttu-id="998c4-295">更详细地了解标识：</span><span class="sxs-lookup"><span data-stu-id="998c4-295">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="998c4-296">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="998c4-296">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="998c4-297">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="998c4-297">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="998c4-298">标识组件</span><span class="sxs-lookup"><span data-stu-id="998c4-298">Identity Components</span></span>

<span data-ttu-id="998c4-299">所有标识相关 NuGet 包都包含在[AspNetCore 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="998c4-299">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="998c4-300">标识的主包为[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="998c4-300">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="998c4-301">此程序包包含用于 ASP.NET Core 标识的核心接口集，由 `Microsoft.AspNetCore.Identity.EntityFrameworkCore`包含。</span><span class="sxs-lookup"><span data-stu-id="998c4-301">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="998c4-302">迁移到 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="998c4-302">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="998c4-303">有关迁移现有标识存储的详细信息和指南，请参阅[迁移身份验证和标识](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="998c4-303">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="998c4-304">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="998c4-304">Setting password strength</span></span>

<span data-ttu-id="998c4-305">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="998c4-305">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="998c4-306">后续步骤</span><span class="sxs-lookup"><span data-stu-id="998c4-306">Next Steps</span></span>

* <span data-ttu-id="998c4-307">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131)，了解如何使用 SQLite 配置标识。</span><span class="sxs-lookup"><span data-stu-id="998c4-307">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="998c4-308">配置标识</span><span class="sxs-lookup"><span data-stu-id="998c4-308">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
