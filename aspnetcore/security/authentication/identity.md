---
title: ASP.NET Core 简介 Identity
author: rick-anderson
description: Identity与 ASP.NET Core 应用一起使用。 了解如何 (RequireDigit、RequiredLength、RequiredUniqueChars 等) 设置密码要求。
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 6ac565bfa4862168fa143417ab5a81c51b620f16
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86212443"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="4fa72-104">ASP.NET Core 简介 Identity</span><span class="sxs-lookup"><span data-stu-id="4fa72-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4fa72-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4fa72-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4fa72-106">ASP.NET Core Identity：</span><span class="sxs-lookup"><span data-stu-id="4fa72-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="4fa72-107">是一个 API，它支持用户界面) 登录功能 (UI。</span><span class="sxs-lookup"><span data-stu-id="4fa72-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="4fa72-108">管理用户、密码、配置文件数据、角色、声明、令牌、电子邮件确认等。</span><span class="sxs-lookup"><span data-stu-id="4fa72-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="4fa72-109">用户可以使用存储在中的登录信息创建一个帐户， Identity 也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="4fa72-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="4fa72-110">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="4fa72-111">GitHub 上提供了[ Identity 源代码](https://github.com/dotnet/AspNetCore/tree/master/src/Identity)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="4fa72-112">[基架 Identity ](xref:security/authentication/scaffold-identity)并查看生成的文件以查看与的模板交互 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

Identity<span data-ttu-id="4fa72-113">通常使用 SQL Server 数据库配置以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="4fa72-113"> is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="4fa72-114">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="4fa72-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="4fa72-115">在本主题中，你将了解如何使用 Identity 注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="4fa72-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="4fa72-116">注意：这些模板将用户的用户名和电子邮件视为相同。</span><span class="sxs-lookup"><span data-stu-id="4fa72-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="4fa72-117">有关创建使用的应用程序的更多详细说明 Identity ，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="4fa72-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="4fa72-118">[Microsoft 标识平台](/azure/active-directory/develop/)是：</span><span class="sxs-lookup"><span data-stu-id="4fa72-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="4fa72-119">) 开发人员平台 Azure AD Azure Active Directory (的演变。</span><span class="sxs-lookup"><span data-stu-id="4fa72-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="4fa72-120">与 ASP.NET Core 无关 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/Identity<span data-ttu-id="4fa72-121">Server4.md) ]</span><span class="sxs-lookup"><span data-stu-id="4fa72-121">Server4.md)]</span></span>

<span data-ttu-id="4fa72-122">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([如何下载) ](xref:index#how-to-download-a-sample)) 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-122">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="4fa72-123">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="4fa72-123">Create a Web app with authentication</span></span>

<span data-ttu-id="4fa72-124">使用单独的用户帐户创建 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="4fa72-124">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fa72-125">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fa72-125">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4fa72-126">选择 "**文件**" " > **新建** > **项目**"。</span><span class="sxs-lookup"><span data-stu-id="4fa72-126">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="4fa72-127">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="4fa72-127">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="4fa72-128">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="4fa72-128">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="4fa72-129">单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="4fa72-129">Click **OK**.</span></span>
* <span data-ttu-id="4fa72-130">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="4fa72-130">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="4fa72-131">选择**单个用户帐户**，然后单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="4fa72-131">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fa72-132">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fa72-132">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="4fa72-133">上述命令 Razor 使用 SQLite 创建 web 应用。</span><span class="sxs-lookup"><span data-stu-id="4fa72-133">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="4fa72-134">若要创建具有 LocalDB 的 web 应用，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4fa72-134">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="4fa72-135">生成的项目[ Razor 以类库形式](xref:razor-pages/ui-class)提供[ASP.NET Core Identity ](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-135">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="4fa72-136">类库 Identity Razor 公开的终结点 `Identity` 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-136">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="4fa72-137">例如：</span><span class="sxs-lookup"><span data-stu-id="4fa72-137">For example:</span></span>

* <span data-ttu-id="4fa72-138">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="4fa72-138">/Identity/Account/Login</span></span>
* <span data-ttu-id="4fa72-139">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="4fa72-139">/Identity/Account/Logout</span></span>
* <span data-ttu-id="4fa72-140">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="4fa72-140">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="4fa72-141">应用迁移</span><span class="sxs-lookup"><span data-stu-id="4fa72-141">Apply migrations</span></span>

<span data-ttu-id="4fa72-142">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="4fa72-142">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fa72-143">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fa72-143">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4fa72-144">在包管理器控制台中运行以下命令 (PMC) ：</span><span class="sxs-lookup"><span data-stu-id="4fa72-144">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="4fa72-145">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fa72-145">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4fa72-146">使用 SQLite 时，此步骤不需要迁移。</span><span class="sxs-lookup"><span data-stu-id="4fa72-146">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="4fa72-147">对于 LocalDB，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4fa72-147">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="4fa72-148">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="4fa72-148">Test Register and Login</span></span>

<span data-ttu-id="4fa72-149">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="4fa72-149">Run the app and register a user.</span></span> <span data-ttu-id="4fa72-150">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="4fa72-150">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="4fa72-151">配置 Identity 服务</span><span class="sxs-lookup"><span data-stu-id="4fa72-151">Configure Identity services</span></span>

<span data-ttu-id="4fa72-152">中添加了服务 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-152">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="4fa72-153">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="4fa72-153">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="4fa72-154">前面突出显示的代码 Identity 用默认选项值进行配置。</span><span class="sxs-lookup"><span data-stu-id="4fa72-154">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="4fa72-155">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="4fa72-155">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="4fa72-156">通过调用启用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-156"> is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="4fa72-157">`UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="4fa72-157">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="4fa72-158">模板生成的应用不使用[授权](xref:security/authorization/secure-data)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-158">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="4fa72-159">`app.UseAuthorization`包括，以确保在应用添加授权时，按正确的顺序添加。</span><span class="sxs-lookup"><span data-stu-id="4fa72-159">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="4fa72-160">`UseRouting`、 `UseAuthentication` 、 `UseAuthorization` 和 `UseEndpoints` 必须按前面的代码中所示的顺序调用。</span><span class="sxs-lookup"><span data-stu-id="4fa72-160">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="4fa72-161">有关和的详细 `IdentityOptions` 信息 `Startup` ，请参阅 <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-161">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="4fa72-162">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="4fa72-162">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fa72-163">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fa72-163">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4fa72-164">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="4fa72-164">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="4fa72-165">将[基架标识置于 Razor 具有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明的项目中，以生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="4fa72-165">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fa72-166">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fa72-166">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4fa72-167">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="4fa72-167">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="4fa72-168">否则，请使用正确的命名空间 `ApplicationDbContext` ：</span><span class="sxs-lookup"><span data-stu-id="4fa72-168">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="4fa72-169">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="4fa72-169">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="4fa72-170">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="4fa72-170">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="4fa72-171">有关基架的详细信息 Identity ，请参阅[ Razor 使用授权将基架标识转换为项目](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-171">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="4fa72-172">检查注册</span><span class="sxs-lookup"><span data-stu-id="4fa72-172">Examine Register</span></span>

<span data-ttu-id="4fa72-173">当用户单击 "**注册**" 链接时，将 `RegisterModel.OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="4fa72-173">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="4fa72-174">用户由[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)在对象上创建 `_userManager` ：</span><span class="sxs-lookup"><span data-stu-id="4fa72-174">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="4fa72-175">如果用户已成功创建，则对的调用会登录该用户 `_signInManager.SignInAsync` 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="4fa72-176">请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="4fa72-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="4fa72-177">登录</span><span class="sxs-lookup"><span data-stu-id="4fa72-177">Log in</span></span>

<span data-ttu-id="4fa72-178">当出现以下情况时，将显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="4fa72-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="4fa72-179">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="4fa72-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="4fa72-180">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="4fa72-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="4fa72-181">提交登录页上的窗体时，将 `OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="4fa72-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="4fa72-182">`PasswordSignInAsync`对 `_signInManager` 对象调用。</span><span class="sxs-lookup"><span data-stu-id="4fa72-182">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="4fa72-183">有关如何做出授权决策的信息，请参阅 <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-183">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="4fa72-184">注销</span><span class="sxs-lookup"><span data-stu-id="4fa72-184">Log out</span></span>

<span data-ttu-id="4fa72-185">"**注销**" 链接将调用该 `LogoutModel.OnPost` 操作。</span><span class="sxs-lookup"><span data-stu-id="4fa72-185">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="4fa72-186">在前面的代码中，代码 `return RedirectToPage();` 需要是重定向，以便浏览器执行新请求并更新用户的标识。</span><span class="sxs-lookup"><span data-stu-id="4fa72-186">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="4fa72-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="4fa72-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="4fa72-188">Post 在*Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="4fa72-188">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="4fa72-189">考试Identity</span><span class="sxs-lookup"><span data-stu-id="4fa72-189">Test Identity</span></span>

<span data-ttu-id="4fa72-190">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="4fa72-190">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="4fa72-191">若要测试 Identity ，请添加 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ：</span><span class="sxs-lookup"><span data-stu-id="4fa72-191">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="4fa72-192">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="4fa72-192">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="4fa72-193">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="4fa72-193">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="4fa72-194">浏览Identity</span><span class="sxs-lookup"><span data-stu-id="4fa72-194">Explore Identity</span></span>

<span data-ttu-id="4fa72-195">Identity了解更多详细信息：</span><span class="sxs-lookup"><span data-stu-id="4fa72-195">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="4fa72-196">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="4fa72-196">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="4fa72-197">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="4fa72-197">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="4fa72-198">组分</span><span class="sxs-lookup"><span data-stu-id="4fa72-198"> Components</span></span>

<span data-ttu-id="4fa72-199">所有 Identity 相关的 NuGet 包都包含在[ASP.NET Core 共享框架](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)中。</span><span class="sxs-lookup"><span data-stu-id="4fa72-199">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="4fa72-200">的主包为 Identity [AspNetCore。 Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="4fa72-200">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="4fa72-201">此程序包包含用于 ASP.NET Core 的核心接口集 Identity ，由提供 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-201">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="4fa72-202">迁移到 ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="4fa72-202">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="4fa72-203">有关迁移现有存储的详细信息和指南 Identity ，请参阅[迁移身份验证 Identity 和](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-203">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="4fa72-204">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="4fa72-204">Setting password strength</span></span>

<span data-ttu-id="4fa72-205">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-205">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="4fa72-206">AddDefault Identity 并添加Identity</span><span class="sxs-lookup"><span data-stu-id="4fa72-206">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="4fa72-207"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="4fa72-207"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="4fa72-208">调用 `AddDefaultIdentity` 类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="4fa72-208">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="4fa72-209">有关详细信息，请参阅[AddDefault Identity 源](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-209">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="4fa72-210">禁止发布静态 Identity 资产</span><span class="sxs-lookup"><span data-stu-id="4fa72-210">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="4fa72-211">若要防止将静态 Identity 资产发布 (用于 UI) 的用户的样式和 JavaScript 文件 Identity ，请将以下 `ResolveStaticWebAssetsInputsDependsOn` 属性和目标添加 `RemoveIdentityAssets` 到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="4fa72-211">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="4fa72-212">后续步骤</span><span class="sxs-lookup"><span data-stu-id="4fa72-212">Next Steps</span></span>

* <span data-ttu-id="4fa72-213">[ASP.NET Core Identity 源代码](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="4fa72-213">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="4fa72-214">有关使用 SQLite 进行配置的信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-214">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="4fa72-215">[配置 Identity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="4fa72-215">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4fa72-216">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4fa72-216">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="4fa72-217">ASP.NET Core Identity 是将登录功能添加到 ASP.NET Core 应用的成员资格系统。</span><span class="sxs-lookup"><span data-stu-id="4fa72-217">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="4fa72-218">用户可以使用存储在中的登录信息创建一个帐户， Identity 也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="4fa72-218">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="4fa72-219">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-219">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

Identity<span data-ttu-id="4fa72-220">可以使用 SQL Server 数据库配置以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="4fa72-220"> can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="4fa72-221">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="4fa72-221">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="4fa72-222">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([如何下载) ](xref:index#how-to-download-a-sample)) 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-222">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="4fa72-223">在本主题中，你将了解如何使用 Identity 注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="4fa72-223">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="4fa72-224">有关创建使用的应用程序的更多详细说明 Identity ，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="4fa72-224">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="4fa72-225">AddDefault Identity 并添加Identity</span><span class="sxs-lookup"><span data-stu-id="4fa72-225">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="4fa72-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="4fa72-226"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="4fa72-227">调用 `AddDefaultIdentity` 类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="4fa72-227">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="4fa72-228">有关详细信息，请参阅[AddDefault Identity 源](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-228">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="4fa72-229">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="4fa72-229">Create a Web app with authentication</span></span>

<span data-ttu-id="4fa72-230">使用单独的用户帐户创建 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="4fa72-230">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fa72-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fa72-231">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="4fa72-232">选择 "**文件**" " > **新建** > **项目**"。</span><span class="sxs-lookup"><span data-stu-id="4fa72-232">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="4fa72-233">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="4fa72-233">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="4fa72-234">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="4fa72-234">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="4fa72-235">单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="4fa72-235">Click **OK**.</span></span>
* <span data-ttu-id="4fa72-236">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="4fa72-236">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="4fa72-237">选择**单个用户帐户**，然后单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="4fa72-237">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fa72-238">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fa72-238">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="4fa72-239">生成的项目[ Razor 以类库形式](xref:razor-pages/ui-class)提供[ASP.NET Core Identity ](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-239">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="4fa72-240">类库 Identity Razor 公开的终结点 `Identity` 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-240">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="4fa72-241">例如：</span><span class="sxs-lookup"><span data-stu-id="4fa72-241">For example:</span></span>

* <span data-ttu-id="4fa72-242">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="4fa72-242">/Identity/Account/Login</span></span>
* <span data-ttu-id="4fa72-243">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="4fa72-243">/Identity/Account/Logout</span></span>
* <span data-ttu-id="4fa72-244">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="4fa72-244">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="4fa72-245">应用迁移</span><span class="sxs-lookup"><span data-stu-id="4fa72-245">Apply migrations</span></span>

<span data-ttu-id="4fa72-246">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="4fa72-246">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fa72-247">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fa72-247">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4fa72-248">在包管理器控制台中运行以下命令 (PMC) ：</span><span class="sxs-lookup"><span data-stu-id="4fa72-248">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="4fa72-249">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fa72-249">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="4fa72-250">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="4fa72-250">Test Register and Login</span></span>

<span data-ttu-id="4fa72-251">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="4fa72-251">Run the app and register a user.</span></span> <span data-ttu-id="4fa72-252">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="4fa72-252">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="4fa72-253">配置 Identity 服务</span><span class="sxs-lookup"><span data-stu-id="4fa72-253">Configure Identity services</span></span>

<span data-ttu-id="4fa72-254">中添加了服务 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-254">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="4fa72-255">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="4fa72-255">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="4fa72-256">前面的代码 Identity 用默认选项值进行配置。</span><span class="sxs-lookup"><span data-stu-id="4fa72-256">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="4fa72-257">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="4fa72-257">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

Identity<span data-ttu-id="4fa72-258">通过调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)启用。</span><span class="sxs-lookup"><span data-stu-id="4fa72-258"> is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="4fa72-259">`UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="4fa72-259">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="4fa72-260">有关详细信息，请参阅[ Identity Options 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-260">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="4fa72-261">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="4fa72-261">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="4fa72-262">将[基架标识置于 Razor 具有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明的项目中，以生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="4fa72-262">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="4fa72-263">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="4fa72-263">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="4fa72-264">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="4fa72-264">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="4fa72-265">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="4fa72-265">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="4fa72-266">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="4fa72-266">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="4fa72-267">否则，请使用正确的命名空间 `ApplicationDbContext` ：</span><span class="sxs-lookup"><span data-stu-id="4fa72-267">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="4fa72-268">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="4fa72-268">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="4fa72-269">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="4fa72-269">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="4fa72-270">检查注册</span><span class="sxs-lookup"><span data-stu-id="4fa72-270">Examine Register</span></span>

<span data-ttu-id="4fa72-271">当用户单击 "**注册**" 链接时，将 `RegisterModel.OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="4fa72-271">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="4fa72-272">用户由[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)在对象上创建 `_userManager` ：</span><span class="sxs-lookup"><span data-stu-id="4fa72-272">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="4fa72-273">如果用户已成功创建，则对的调用会登录该用户 `_signInManager.SignInAsync` 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-273">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="4fa72-274">**注意：** 请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="4fa72-274">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="4fa72-275">登录</span><span class="sxs-lookup"><span data-stu-id="4fa72-275">Log in</span></span>

<span data-ttu-id="4fa72-276">当出现以下情况时，将显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="4fa72-276">The Login form is displayed when:</span></span>

* <span data-ttu-id="4fa72-277">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="4fa72-277">The **Log in** link is selected.</span></span>
* <span data-ttu-id="4fa72-278">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="4fa72-278">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="4fa72-279">提交登录页上的窗体时，将 `OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="4fa72-279">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="4fa72-280">`PasswordSignInAsync`对 `_signInManager` 对象调用。</span><span class="sxs-lookup"><span data-stu-id="4fa72-280">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="4fa72-281">有关如何做出授权决策的信息，请参阅 <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-281">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="4fa72-282">注销</span><span class="sxs-lookup"><span data-stu-id="4fa72-282">Log out</span></span>

<span data-ttu-id="4fa72-283">"**注销**" 链接将调用该 `LogoutModel.OnPost` 操作。</span><span class="sxs-lookup"><span data-stu-id="4fa72-283">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="4fa72-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="4fa72-284">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="4fa72-285">Post 在*Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="4fa72-285">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="4fa72-286">考试Identity</span><span class="sxs-lookup"><span data-stu-id="4fa72-286">Test Identity</span></span>

<span data-ttu-id="4fa72-287">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="4fa72-287">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="4fa72-288">若要进行测试 Identity ，请将添加 [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 到 "隐私" 页。</span><span class="sxs-lookup"><span data-stu-id="4fa72-288">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="4fa72-289">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="4fa72-289">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="4fa72-290">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="4fa72-290">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="4fa72-291">浏览Identity</span><span class="sxs-lookup"><span data-stu-id="4fa72-291">Explore Identity</span></span>

<span data-ttu-id="4fa72-292">Identity了解更多详细信息：</span><span class="sxs-lookup"><span data-stu-id="4fa72-292">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="4fa72-293">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="4fa72-293">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="4fa72-294">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="4fa72-294">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="4fa72-295">组分</span><span class="sxs-lookup"><span data-stu-id="4fa72-295"> Components</span></span>

<span data-ttu-id="4fa72-296">所有 Identity 相关 NuGet 包都包含在[AspNetCore 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="4fa72-296">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="4fa72-297">的主包为 Identity [AspNetCore。 Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="4fa72-297">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="4fa72-298">此程序包包含用于 ASP.NET Core 的核心接口集 Identity ，由提供 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-298">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="4fa72-299">迁移到 ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="4fa72-299">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="4fa72-300">有关迁移现有存储的详细信息和指南 Identity ，请参阅[迁移身份验证 Identity 和](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-300">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="4fa72-301">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="4fa72-301">Setting password strength</span></span>

<span data-ttu-id="4fa72-302">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="4fa72-302">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="4fa72-303">后续步骤</span><span class="sxs-lookup"><span data-stu-id="4fa72-303">Next Steps</span></span>

* <span data-ttu-id="4fa72-304">有关使用 SQLite 进行配置的信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity 。</span><span class="sxs-lookup"><span data-stu-id="4fa72-304">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="4fa72-305">[配置 Identity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="4fa72-305">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
