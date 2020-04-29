---
title: ASP.NET Core 上的标识简介
author: rick-anderson
description: 将身份用于 ASP.NET Core 应用。 了解如何设置密码要求（RequireDigit、RequiredLength、RequiredUniqueChars 等）。
ms.author: riande
ms.date: 01/15/2020
uid: security/authentication/identity
ms.openlocfilehash: 4bc5f206b3aee7c2d34055703acc5b6c5218f964
ms.sourcegitcommit: 56861af66bb364a5d60c3c72d133d854b4cf292d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205938"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="a9598-104">ASP.NET Core 上的标识简介</span><span class="sxs-lookup"><span data-stu-id="a9598-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a9598-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9598-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a9598-106">ASP.NET Core 标识：</span><span class="sxs-lookup"><span data-stu-id="a9598-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="a9598-107">是支持用户界面（UI）登录功能的 API。</span><span class="sxs-lookup"><span data-stu-id="a9598-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="a9598-108">管理用户、密码、配置文件数据、角色、声明、令牌、电子邮件确认等。</span><span class="sxs-lookup"><span data-stu-id="a9598-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="a9598-109">用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="a9598-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="a9598-110">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="a9598-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="a9598-111">GitHub 上提供了[标识源代码](https://github.com/dotnet/AspNetCore/tree/master/src/Identity)。</span><span class="sxs-lookup"><span data-stu-id="a9598-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="a9598-112">[基架标识](xref:security/authentication/scaffold-identity)并查看生成的文件以查看模板与标识的交互。</span><span class="sxs-lookup"><span data-stu-id="a9598-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="a9598-113">通常使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="a9598-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="a9598-114">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="a9598-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="a9598-115">本主题介绍如何使用标识注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="a9598-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="a9598-116">注意：这些模板将用户的用户名和电子邮件视为相同。</span><span class="sxs-lookup"><span data-stu-id="a9598-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="a9598-117">有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="a9598-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="a9598-118">[Microsoft 标识平台](/azure/active-directory/develop/)是：</span><span class="sxs-lookup"><span data-stu-id="a9598-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="a9598-119">Azure Active Directory （Azure AD）开发人员平台的演变。</span><span class="sxs-lookup"><span data-stu-id="a9598-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="a9598-120">与 ASP.NET Core 标识无关。</span><span class="sxs-lookup"><span data-stu-id="a9598-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="a9598-121">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)（[如何下载）](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="a9598-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="a9598-122">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="a9598-122">Create a Web app with authentication</span></span>

<span data-ttu-id="a9598-123">使用单独的用户帐户创建 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="a9598-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a9598-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9598-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a9598-125">选择 "**文件** > " "**新建** > **项目**"。</span><span class="sxs-lookup"><span data-stu-id="a9598-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="a9598-126">选择“ASP.NET Core Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="a9598-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a9598-127">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="a9598-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="a9598-128">单击" **确定**"。</span><span class="sxs-lookup"><span data-stu-id="a9598-128">Click **OK**.</span></span>
* <span data-ttu-id="a9598-129">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="a9598-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="a9598-130">选择**单个用户帐户**，然后单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="a9598-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a9598-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9598-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="a9598-132">上述命令使用 SQLite 创建 Razor web 应用。</span><span class="sxs-lookup"><span data-stu-id="a9598-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="a9598-133">若要创建具有 LocalDB 的 web 应用，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="a9598-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="a9598-134">生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="a9598-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a9598-135">标识 Razor 类库公开带有`Identity`区域的终结点。</span><span class="sxs-lookup"><span data-stu-id="a9598-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="a9598-136">例如：</span><span class="sxs-lookup"><span data-stu-id="a9598-136">For example:</span></span>

* <span data-ttu-id="a9598-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="a9598-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="a9598-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="a9598-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="a9598-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="a9598-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="a9598-140">应用迁移</span><span class="sxs-lookup"><span data-stu-id="a9598-140">Apply migrations</span></span>

<span data-ttu-id="a9598-141">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="a9598-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a9598-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9598-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9598-143">在包管理器控制台中运行以下命令（PMC）：</span><span class="sxs-lookup"><span data-stu-id="a9598-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="a9598-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9598-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a9598-145">使用 SQLite 时，此步骤不需要迁移。</span><span class="sxs-lookup"><span data-stu-id="a9598-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="a9598-146">对于 LocalDB，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="a9598-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="a9598-147">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="a9598-147">Test Register and Login</span></span>

<span data-ttu-id="a9598-148">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="a9598-148">Run the app and register a user.</span></span> <span data-ttu-id="a9598-149">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="a9598-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="a9598-150">配置标识服务</span><span class="sxs-lookup"><span data-stu-id="a9598-150">Configure Identity services</span></span>

<span data-ttu-id="a9598-151">中`ConfigureServices`添加了服务。</span><span class="sxs-lookup"><span data-stu-id="a9598-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="a9598-152">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="a9598-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="a9598-153">前面突出显示的代码用默认选项值配置标识。</span><span class="sxs-lookup"><span data-stu-id="a9598-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="a9598-154">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="a9598-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="a9598-155">标识是通过调用<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>启用的。</span><span class="sxs-lookup"><span data-stu-id="a9598-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="a9598-156">`UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="a9598-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="a9598-157">模板生成的应用不使用[授权](xref:security/authorization/secure-data)。</span><span class="sxs-lookup"><span data-stu-id="a9598-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="a9598-158">`app.UseAuthorization`包括，以确保在应用添加授权时，按正确的顺序添加。</span><span class="sxs-lookup"><span data-stu-id="a9598-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="a9598-159">`UseRouting``UseAuthorization` `UseEndpoints` 、 `UseAuthentication`、和必须按前面的代码中所示的顺序调用。</span><span class="sxs-lookup"><span data-stu-id="a9598-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="a9598-160">有关`IdentityOptions`和`Startup`的详细信息，请<xref:Microsoft.AspNetCore.Identity.IdentityOptions>参阅和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="a9598-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="a9598-161">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="a9598-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a9598-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9598-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9598-163">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="a9598-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="a9598-164">按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="a9598-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a9598-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9598-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a9598-166">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="a9598-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="a9598-167">否则，请使用正确的命名空间`ApplicationDbContext`：</span><span class="sxs-lookup"><span data-stu-id="a9598-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="a9598-168">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="a9598-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="a9598-169">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="a9598-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="a9598-170">有关基架标识的详细信息，请参阅[使用授权将基架标识导入 Razor 项目](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="a9598-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="a9598-171">检查注册</span><span class="sxs-lookup"><span data-stu-id="a9598-171">Examine Register</span></span>

<span data-ttu-id="a9598-172">当用户单击 "**注册**" 链接时， `RegisterModel.OnPostAsync`将调用该操作。</span><span class="sxs-lookup"><span data-stu-id="a9598-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="a9598-173">用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对`_userManager`对象创建的。</span><span class="sxs-lookup"><span data-stu-id="a9598-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="a9598-174">`_userManager`由依赖关系注入提供：</span><span class="sxs-lookup"><span data-stu-id="a9598-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="a9598-175">如果用户已成功创建，则对的调用会登录该用户`_signInManager.SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="a9598-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="a9598-176">请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="a9598-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="a9598-177">登录</span><span class="sxs-lookup"><span data-stu-id="a9598-177">Log in</span></span>

<span data-ttu-id="a9598-178">当出现以下情况时，将显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="a9598-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="a9598-179">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="a9598-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="a9598-180">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="a9598-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="a9598-181">提交登录页上的窗体时，将调用`OnPostAsync`该操作。</span><span class="sxs-lookup"><span data-stu-id="a9598-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="a9598-182">`PasswordSignInAsync`对`_signInManager`对象调用（由依赖关系注入提供）。</span><span class="sxs-lookup"><span data-stu-id="a9598-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="a9598-183">基类`Controller`公开了`User`可从控制器方法访问的属性。</span><span class="sxs-lookup"><span data-stu-id="a9598-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="a9598-184">例如，可以枚举`User.Claims`并做出授权决策。</span><span class="sxs-lookup"><span data-stu-id="a9598-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="a9598-185">有关详细信息，请参阅 <xref:security/authorization/introduction>。</span><span class="sxs-lookup"><span data-stu-id="a9598-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="a9598-186">注销</span><span class="sxs-lookup"><span data-stu-id="a9598-186">Log out</span></span>

<span data-ttu-id="a9598-187">"**注销**" 链接将调用`LogoutModel.OnPost`该操作。</span><span class="sxs-lookup"><span data-stu-id="a9598-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="a9598-188">在前面的代码中，代码`return RedirectToPage();`需要是重定向，以便浏览器执行新请求并更新用户的标识。</span><span class="sxs-lookup"><span data-stu-id="a9598-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="a9598-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="a9598-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="a9598-190">Post 在*Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="a9598-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="a9598-191">测试标识</span><span class="sxs-lookup"><span data-stu-id="a9598-191">Test Identity</span></span>

<span data-ttu-id="a9598-192">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="a9598-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="a9598-193">若要测试标识， [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)请添加：</span><span class="sxs-lookup"><span data-stu-id="a9598-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="a9598-194">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="a9598-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="a9598-195">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="a9598-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="a9598-196">浏览标识</span><span class="sxs-lookup"><span data-stu-id="a9598-196">Explore Identity</span></span>

<span data-ttu-id="a9598-197">更详细地了解标识：</span><span class="sxs-lookup"><span data-stu-id="a9598-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="a9598-198">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="a9598-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="a9598-199">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="a9598-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="a9598-200">标识组件</span><span class="sxs-lookup"><span data-stu-id="a9598-200">Identity Components</span></span>

<span data-ttu-id="a9598-201">所有标识相关 NuGet 包都包含在[ASP.NET Core 共享框架](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)中。</span><span class="sxs-lookup"><span data-stu-id="a9598-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="a9598-202">标识的主包为[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="a9598-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="a9598-203">此程序包包含用于 ASP.NET Core 标识的核心接口集，由`Microsoft.AspNetCore.Identity.EntityFrameworkCore`提供。</span><span class="sxs-lookup"><span data-stu-id="a9598-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="a9598-204">迁移到 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="a9598-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="a9598-205">有关迁移现有标识存储的详细信息和指南，请参阅[迁移身份验证和标识](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="a9598-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="a9598-206">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="a9598-206">Setting password strength</span></span>

<span data-ttu-id="a9598-207">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="a9598-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="a9598-208">AddDefaultIdentity 和 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="a9598-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="a9598-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="a9598-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="a9598-210">调用`AddDefaultIdentity`类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="a9598-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="a9598-211">有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="a9598-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="a9598-212">禁止发布静态标识资产</span><span class="sxs-lookup"><span data-stu-id="a9598-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="a9598-213">若要防止将静态标识资产（用于标识 UI 的样式表和 JavaScript 文件）发布到 web 根目录， `ResolveStaticWebAssetsInputsDependsOn`请将`RemoveIdentityAssets`以下属性和目标添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="a9598-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="a9598-214">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a9598-214">Next Steps</span></span>

* <span data-ttu-id="a9598-215">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131)，了解如何使用 SQLite 配置标识。</span><span class="sxs-lookup"><span data-stu-id="a9598-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="a9598-216">配置标识</span><span class="sxs-lookup"><span data-stu-id="a9598-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a9598-217">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a9598-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="a9598-218">ASP.NET Core 标识是将登录功能添加到 ASP.NET Core 应用的成员资格系统。</span><span class="sxs-lookup"><span data-stu-id="a9598-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="a9598-219">用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="a9598-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="a9598-220">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="a9598-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="a9598-221">可以使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="a9598-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="a9598-222">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="a9598-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="a9598-223">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)（[如何下载）](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="a9598-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="a9598-224">本主题介绍如何使用标识注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="a9598-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="a9598-225">有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="a9598-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="a9598-226">AddDefaultIdentity 和 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="a9598-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="a9598-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="a9598-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="a9598-228">调用`AddDefaultIdentity`类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="a9598-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="a9598-229">有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="a9598-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="a9598-230">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="a9598-230">Create a Web app with authentication</span></span>

<span data-ttu-id="a9598-231">使用单独的用户帐户创建 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="a9598-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a9598-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9598-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a9598-233">选择 "**文件** > " "**新建** > **项目**"。</span><span class="sxs-lookup"><span data-stu-id="a9598-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="a9598-234">选择“ASP.NET Core Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="a9598-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="a9598-235">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="a9598-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="a9598-236">单击" **确定**"。</span><span class="sxs-lookup"><span data-stu-id="a9598-236">Click **OK**.</span></span>
* <span data-ttu-id="a9598-237">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="a9598-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="a9598-238">选择**单个用户帐户**，然后单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="a9598-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a9598-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9598-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="a9598-240">生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="a9598-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="a9598-241">标识 Razor 类库公开带有`Identity`区域的终结点。</span><span class="sxs-lookup"><span data-stu-id="a9598-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="a9598-242">例如：</span><span class="sxs-lookup"><span data-stu-id="a9598-242">For example:</span></span>

* <span data-ttu-id="a9598-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="a9598-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="a9598-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="a9598-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="a9598-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="a9598-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="a9598-246">应用迁移</span><span class="sxs-lookup"><span data-stu-id="a9598-246">Apply migrations</span></span>

<span data-ttu-id="a9598-247">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="a9598-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a9598-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9598-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9598-249">在包管理器控制台中运行以下命令（PMC）：</span><span class="sxs-lookup"><span data-stu-id="a9598-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="a9598-250">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9598-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="a9598-251">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="a9598-251">Test Register and Login</span></span>

<span data-ttu-id="a9598-252">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="a9598-252">Run the app and register a user.</span></span> <span data-ttu-id="a9598-253">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="a9598-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="a9598-254">配置标识服务</span><span class="sxs-lookup"><span data-stu-id="a9598-254">Configure Identity services</span></span>

<span data-ttu-id="a9598-255">中`ConfigureServices`添加了服务。</span><span class="sxs-lookup"><span data-stu-id="a9598-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="a9598-256">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="a9598-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="a9598-257">前面的代码用默认选项值配置标识。</span><span class="sxs-lookup"><span data-stu-id="a9598-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="a9598-258">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="a9598-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="a9598-259">通过调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)来启用标识。</span><span class="sxs-lookup"><span data-stu-id="a9598-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="a9598-260">`UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="a9598-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="a9598-261">有关详细信息，请参阅[IdentityOptions 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="a9598-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="a9598-262">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="a9598-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="a9598-263">按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="a9598-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a9598-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a9598-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a9598-265">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="a9598-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="a9598-266">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="a9598-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="a9598-267">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="a9598-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="a9598-268">否则，请使用正确的命名空间`ApplicationDbContext`：</span><span class="sxs-lookup"><span data-stu-id="a9598-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="a9598-269">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="a9598-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="a9598-270">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="a9598-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="a9598-271">检查注册</span><span class="sxs-lookup"><span data-stu-id="a9598-271">Examine Register</span></span>

<span data-ttu-id="a9598-272">当用户单击 "**注册**" 链接时， `RegisterModel.OnPostAsync`将调用该操作。</span><span class="sxs-lookup"><span data-stu-id="a9598-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="a9598-273">用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对`_userManager`对象创建的。</span><span class="sxs-lookup"><span data-stu-id="a9598-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="a9598-274">`_userManager`由依赖关系注入提供：</span><span class="sxs-lookup"><span data-stu-id="a9598-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="a9598-275">如果用户已成功创建，则对的调用会登录该用户`_signInManager.SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="a9598-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="a9598-276">**注意：** 请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="a9598-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="a9598-277">登录</span><span class="sxs-lookup"><span data-stu-id="a9598-277">Log in</span></span>

<span data-ttu-id="a9598-278">当出现以下情况时，将显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="a9598-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="a9598-279">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="a9598-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="a9598-280">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="a9598-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="a9598-281">提交登录页上的窗体时，将调用`OnPostAsync`该操作。</span><span class="sxs-lookup"><span data-stu-id="a9598-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="a9598-282">`PasswordSignInAsync`对`_signInManager`对象调用（由依赖关系注入提供）。</span><span class="sxs-lookup"><span data-stu-id="a9598-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="a9598-283">基类`Controller`公开了`User`可从控制器方法访问的属性。</span><span class="sxs-lookup"><span data-stu-id="a9598-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="a9598-284">例如，可以枚举`User.Claims`并做出授权决策。</span><span class="sxs-lookup"><span data-stu-id="a9598-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="a9598-285">有关详细信息，请参阅 <xref:security/authorization/introduction>。</span><span class="sxs-lookup"><span data-stu-id="a9598-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="a9598-286">注销</span><span class="sxs-lookup"><span data-stu-id="a9598-286">Log out</span></span>

<span data-ttu-id="a9598-287">"**注销**" 链接将调用`LogoutModel.OnPost`该操作。</span><span class="sxs-lookup"><span data-stu-id="a9598-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="a9598-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="a9598-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="a9598-289">Post 在*Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="a9598-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="a9598-290">测试标识</span><span class="sxs-lookup"><span data-stu-id="a9598-290">Test Identity</span></span>

<span data-ttu-id="a9598-291">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="a9598-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="a9598-292">若要测试标识， [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)请将添加到 "隐私" 页。</span><span class="sxs-lookup"><span data-stu-id="a9598-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="a9598-293">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="a9598-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="a9598-294">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="a9598-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="a9598-295">浏览标识</span><span class="sxs-lookup"><span data-stu-id="a9598-295">Explore Identity</span></span>

<span data-ttu-id="a9598-296">更详细地了解标识：</span><span class="sxs-lookup"><span data-stu-id="a9598-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="a9598-297">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="a9598-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="a9598-298">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="a9598-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="a9598-299">标识组件</span><span class="sxs-lookup"><span data-stu-id="a9598-299">Identity Components</span></span>

<span data-ttu-id="a9598-300">所有标识相关 NuGet 包都包含在[AspNetCore 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="a9598-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="a9598-301">标识的主包为[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="a9598-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="a9598-302">此程序包包含用于 ASP.NET Core 标识的核心接口集，由`Microsoft.AspNetCore.Identity.EntityFrameworkCore`提供。</span><span class="sxs-lookup"><span data-stu-id="a9598-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="a9598-303">迁移到 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="a9598-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="a9598-304">有关迁移现有标识存储的详细信息和指南，请参阅[迁移身份验证和标识](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="a9598-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="a9598-305">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="a9598-305">Setting password strength</span></span>

<span data-ttu-id="a9598-306">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="a9598-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a9598-307">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a9598-307">Next Steps</span></span>

* <span data-ttu-id="a9598-308">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131)，了解如何使用 SQLite 配置标识。</span><span class="sxs-lookup"><span data-stu-id="a9598-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="a9598-309">配置标识</span><span class="sxs-lookup"><span data-stu-id="a9598-309">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
