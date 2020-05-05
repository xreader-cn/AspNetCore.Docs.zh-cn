---
title: Identity ASP.NET Core 简介
author: rick-anderson
description: 与Identity ASP.NET Core 应用一起使用。 了解如何设置密码要求（RequireDigit、RequiredLength、RequiredUniqueChars 等）。
ms.author: riande
ms.date: 01/15/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: d596a8357c5c812b94950809eedf35718328747c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777002"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="f3236-104">ASP.NET Core 上的标识简介</span><span class="sxs-lookup"><span data-stu-id="f3236-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f3236-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f3236-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f3236-106">ASP.NET Core 标识：</span><span class="sxs-lookup"><span data-stu-id="f3236-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="f3236-107">是支持用户界面（UI）登录功能的 API。</span><span class="sxs-lookup"><span data-stu-id="f3236-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="f3236-108">管理用户、密码、配置文件数据、角色、声明、令牌、电子邮件确认等。</span><span class="sxs-lookup"><span data-stu-id="f3236-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="f3236-109">用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="f3236-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f3236-110">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="f3236-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="f3236-111">GitHub 上提供了[标识源代码](https://github.com/dotnet/AspNetCore/tree/master/src/Identity)。</span><span class="sxs-lookup"><span data-stu-id="f3236-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="f3236-112">[基架标识](xref:security/authentication/scaffold-identity)并查看生成的文件以查看模板与标识的交互。</span><span class="sxs-lookup"><span data-stu-id="f3236-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="f3236-113">通常使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="f3236-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f3236-114">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="f3236-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f3236-115">本主题介绍如何使用标识注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="f3236-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f3236-116">注意：这些模板将用户的用户名和电子邮件视为相同。</span><span class="sxs-lookup"><span data-stu-id="f3236-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="f3236-117">有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="f3236-117">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<span data-ttu-id="f3236-118">[Microsoft 标识平台](/azure/active-directory/develop/)是：</span><span class="sxs-lookup"><span data-stu-id="f3236-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="f3236-119">Azure Active Directory （Azure AD）开发人员平台的演变。</span><span class="sxs-lookup"><span data-stu-id="f3236-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="f3236-120">与 ASP.NET Core 标识无关。</span><span class="sxs-lookup"><span data-stu-id="f3236-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="f3236-121">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)（[如何下载）](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="f3236-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f3236-122">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="f3236-122">Create a Web app with authentication</span></span>

<span data-ttu-id="f3236-123">使用单独的用户帐户创建 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="f3236-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3236-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3236-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3236-125">选择 "**文件** > " "**新建** > **项目**"。</span><span class="sxs-lookup"><span data-stu-id="f3236-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f3236-126">选择“ASP.NET Core Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="f3236-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f3236-127">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="f3236-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f3236-128">单击 **“确定”** 。</span><span class="sxs-lookup"><span data-stu-id="f3236-128">Click **OK**.</span></span>
* <span data-ttu-id="f3236-129">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="f3236-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f3236-130">选择**单个用户帐户**，然后单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="f3236-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f3236-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f3236-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="f3236-132">上述命令使用 SQLite 创建 Razor web 应用。</span><span class="sxs-lookup"><span data-stu-id="f3236-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="f3236-133">若要创建具有 LocalDB 的 web 应用，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f3236-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="f3236-134">生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="f3236-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f3236-135">标识 Razor 类库公开带有`Identity`区域的终结点。</span><span class="sxs-lookup"><span data-stu-id="f3236-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f3236-136">例如：</span><span class="sxs-lookup"><span data-stu-id="f3236-136">For example:</span></span>

* <span data-ttu-id="f3236-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="f3236-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="f3236-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="f3236-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f3236-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="f3236-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f3236-140">应用迁移</span><span class="sxs-lookup"><span data-stu-id="f3236-140">Apply migrations</span></span>

<span data-ttu-id="f3236-141">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="f3236-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3236-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3236-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3236-143">在包管理器控制台中运行以下命令（PMC）：</span><span class="sxs-lookup"><span data-stu-id="f3236-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="f3236-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f3236-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f3236-145">使用 SQLite 时，此步骤不需要迁移。</span><span class="sxs-lookup"><span data-stu-id="f3236-145">Migrations are not necessary at this step when using SQLite.</span></span> <span data-ttu-id="f3236-146">对于 LocalDB，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f3236-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f3236-147">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="f3236-147">Test Register and Login</span></span>

<span data-ttu-id="f3236-148">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="f3236-148">Run the app and register a user.</span></span> <span data-ttu-id="f3236-149">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="f3236-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="f3236-150">配置标识服务</span><span class="sxs-lookup"><span data-stu-id="f3236-150">Configure Identity services</span></span>

<span data-ttu-id="f3236-151">中`ConfigureServices`添加了服务。</span><span class="sxs-lookup"><span data-stu-id="f3236-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f3236-152">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="f3236-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=10-99)]

<span data-ttu-id="f3236-153">前面突出显示的代码用默认选项值配置标识。</span><span class="sxs-lookup"><span data-stu-id="f3236-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="f3236-154">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="f3236-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f3236-155">标识是通过调用<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>启用的。</span><span class="sxs-lookup"><span data-stu-id="f3236-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="f3236-156">`UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="f3236-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="f3236-157">模板生成的应用不使用[授权](xref:security/authorization/secure-data)。</span><span class="sxs-lookup"><span data-stu-id="f3236-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="f3236-158">`app.UseAuthorization`包括，以确保在应用添加授权时，按正确的顺序添加。</span><span class="sxs-lookup"><span data-stu-id="f3236-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="f3236-159">`UseRouting``UseAuthorization` `UseEndpoints` 、 `UseAuthentication`、和必须按前面的代码中所示的顺序调用。</span><span class="sxs-lookup"><span data-stu-id="f3236-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="f3236-160">有关`IdentityOptions`和`Startup`的详细信息，请<xref:Microsoft.AspNetCore.Identity.IdentityOptions>参阅和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="f3236-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="f3236-161">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="f3236-161">Scaffold Register, Login, and LogOut</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3236-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3236-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3236-163">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="f3236-163">Add the Register, Login, and LogOut files.</span></span> <span data-ttu-id="f3236-164">按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="f3236-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f3236-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f3236-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f3236-166">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="f3236-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f3236-167">否则，请使用正确的命名空间`ApplicationDbContext`：</span><span class="sxs-lookup"><span data-stu-id="f3236-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="f3236-168">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="f3236-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f3236-169">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="f3236-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="f3236-170">有关基架标识的详细信息，请参阅[使用授权将基架标识导入 Razor 项目](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="f3236-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f3236-171">检查注册</span><span class="sxs-lookup"><span data-stu-id="f3236-171">Examine Register</span></span>

<span data-ttu-id="f3236-172">当用户单击 "**注册**" 链接时， `RegisterModel.OnPostAsync`将调用该操作。</span><span class="sxs-lookup"><span data-stu-id="f3236-172">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f3236-173">用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对`_userManager`对象创建的。</span><span class="sxs-lookup"><span data-stu-id="f3236-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="f3236-174">`_userManager`由依赖关系注入提供：</span><span class="sxs-lookup"><span data-stu-id="f3236-174">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<span data-ttu-id="f3236-175">如果用户已成功创建，则对的调用会登录该用户`_signInManager.SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="f3236-175">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="f3236-176">请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="f3236-176">See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="f3236-177">登录</span><span class="sxs-lookup"><span data-stu-id="f3236-177">Log in</span></span>

<span data-ttu-id="f3236-178">当出现以下情况时，将显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="f3236-178">The Login form is displayed when:</span></span>

* <span data-ttu-id="f3236-179">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="f3236-179">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f3236-180">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="f3236-180">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f3236-181">提交登录页上的窗体时，将调用`OnPostAsync`该操作。</span><span class="sxs-lookup"><span data-stu-id="f3236-181">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f3236-182">`PasswordSignInAsync`对`_signInManager`对象调用（由依赖关系注入提供）。</span><span class="sxs-lookup"><span data-stu-id="f3236-182">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f3236-183">基类`Controller`公开了`User`可从控制器方法访问的属性。</span><span class="sxs-lookup"><span data-stu-id="f3236-183">The base `Controller` class exposes a `User` property that can be accessed from controller methods.</span></span> <span data-ttu-id="f3236-184">例如，可以枚举`User.Claims`并做出授权决策。</span><span class="sxs-lookup"><span data-stu-id="f3236-184">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="f3236-185">有关详细信息，请参阅 <xref:security/authorization/introduction>。</span><span class="sxs-lookup"><span data-stu-id="f3236-185">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f3236-186">注销</span><span class="sxs-lookup"><span data-stu-id="f3236-186">Log out</span></span>

<span data-ttu-id="f3236-187">"**注销**" 链接将调用`LogoutModel.OnPost`该操作。</span><span class="sxs-lookup"><span data-stu-id="f3236-187">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="f3236-188">在前面的代码中，代码`return RedirectToPage();`需要是重定向，以便浏览器执行新请求并更新用户的标识。</span><span class="sxs-lookup"><span data-stu-id="f3236-188">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="f3236-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="f3236-189">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f3236-190">Post 在*Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="f3236-190">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="f3236-191">测试标识</span><span class="sxs-lookup"><span data-stu-id="f3236-191">Test Identity</span></span>

<span data-ttu-id="f3236-192">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="f3236-192">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f3236-193">若要测试标识， [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute)请添加：</span><span class="sxs-lookup"><span data-stu-id="f3236-193">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f3236-194">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="f3236-194">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f3236-195">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="f3236-195">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="f3236-196">浏览标识</span><span class="sxs-lookup"><span data-stu-id="f3236-196">Explore Identity</span></span>

<span data-ttu-id="f3236-197">更详细地了解标识：</span><span class="sxs-lookup"><span data-stu-id="f3236-197">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f3236-198">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="f3236-198">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f3236-199">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="f3236-199">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="f3236-200">标识组件</span><span class="sxs-lookup"><span data-stu-id="f3236-200">Identity Components</span></span>

<span data-ttu-id="f3236-201">所有标识相关 NuGet 包都包含在[ASP.NET Core 共享框架](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)中。</span><span class="sxs-lookup"><span data-stu-id="f3236-201">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="f3236-202">标识的主包为[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="f3236-202">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f3236-203">此程序包包含用于 ASP.NET Core 标识的核心接口集，由`Microsoft.AspNetCore.Identity.EntityFrameworkCore`提供。</span><span class="sxs-lookup"><span data-stu-id="f3236-203">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="f3236-204">迁移到 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="f3236-204">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f3236-205">有关迁移现有标识存储的详细信息和指南，请参阅[迁移身份验证和标识](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="f3236-205">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f3236-206">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="f3236-206">Setting password strength</span></span>

<span data-ttu-id="f3236-207">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="f3236-207">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="f3236-208">AddDefaultIdentity 和 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="f3236-208">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f3236-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3236-209"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f3236-210">调用`AddDefaultIdentity`类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="f3236-210">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f3236-211">有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="f3236-211">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="f3236-212">禁止发布静态标识资产</span><span class="sxs-lookup"><span data-stu-id="f3236-212">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="f3236-213">若要防止将静态标识资产（用于标识 UI 的样式表和 JavaScript 文件）发布到 web 根目录， `ResolveStaticWebAssetsInputsDependsOn`请将`RemoveIdentityAssets`以下属性和目标添加到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="f3236-213">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="f3236-214">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f3236-214">Next Steps</span></span>

* <span data-ttu-id="f3236-215">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131)，了解如何使用 SQLite 配置标识。</span><span class="sxs-lookup"><span data-stu-id="f3236-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="f3236-216">配置标识</span><span class="sxs-lookup"><span data-stu-id="f3236-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f3236-217">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f3236-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f3236-218">ASP.NET Core 标识是将登录功能添加到 ASP.NET Core 应用的成员资格系统。</span><span class="sxs-lookup"><span data-stu-id="f3236-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="f3236-219">用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="f3236-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f3236-220">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="f3236-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="f3236-221">可以使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="f3236-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f3236-222">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="f3236-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f3236-223">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)（[如何下载）](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="f3236-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f3236-224">本主题介绍如何使用标识注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="f3236-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f3236-225">有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="f3236-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="f3236-226">AddDefaultIdentity 和 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="f3236-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f3236-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="f3236-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f3236-228">调用`AddDefaultIdentity`类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="f3236-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="f3236-229">有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="f3236-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.0/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f3236-230">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="f3236-230">Create a Web app with authentication</span></span>

<span data-ttu-id="f3236-231">使用单独的用户帐户创建 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="f3236-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3236-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3236-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f3236-233">选择 "**文件** > " "**新建** > **项目**"。</span><span class="sxs-lookup"><span data-stu-id="f3236-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f3236-234">选择“ASP.NET Core Web 应用程序”  。</span><span class="sxs-lookup"><span data-stu-id="f3236-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f3236-235">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="f3236-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f3236-236">单击 **“确定”** 。</span><span class="sxs-lookup"><span data-stu-id="f3236-236">Click **OK**.</span></span>
* <span data-ttu-id="f3236-237">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="f3236-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f3236-238">选择**单个用户帐户**，然后单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="f3236-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f3236-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f3236-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="f3236-240">生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="f3236-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f3236-241">标识 Razor 类库公开带有`Identity`区域的终结点。</span><span class="sxs-lookup"><span data-stu-id="f3236-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f3236-242">例如：</span><span class="sxs-lookup"><span data-stu-id="f3236-242">For example:</span></span>

* <span data-ttu-id="f3236-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="f3236-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="f3236-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="f3236-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f3236-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="f3236-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="f3236-246">应用迁移</span><span class="sxs-lookup"><span data-stu-id="f3236-246">Apply migrations</span></span>

<span data-ttu-id="f3236-247">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="f3236-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3236-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3236-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3236-249">在包管理器控制台中运行以下命令（PMC）：</span><span class="sxs-lookup"><span data-stu-id="f3236-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="f3236-250">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f3236-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="f3236-251">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="f3236-251">Test Register and Login</span></span>

<span data-ttu-id="f3236-252">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="f3236-252">Run the app and register a user.</span></span> <span data-ttu-id="f3236-253">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="f3236-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="f3236-254">配置标识服务</span><span class="sxs-lookup"><span data-stu-id="f3236-254">Configure Identity services</span></span>

<span data-ttu-id="f3236-255">中`ConfigureServices`添加了服务。</span><span class="sxs-lookup"><span data-stu-id="f3236-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f3236-256">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="f3236-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="f3236-257">前面的代码用默认选项值配置标识。</span><span class="sxs-lookup"><span data-stu-id="f3236-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="f3236-258">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="f3236-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="f3236-259">通过调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)来启用标识。</span><span class="sxs-lookup"><span data-stu-id="f3236-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="f3236-260">`UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="f3236-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="f3236-261">有关详细信息，请参阅[IdentityOptions 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="f3236-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="f3236-262">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="f3236-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="f3236-263">按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="f3236-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="f3236-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f3236-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f3236-265">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="f3236-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="f3236-266">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f3236-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f3236-267">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="f3236-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f3236-268">否则，请使用正确的命名空间`ApplicationDbContext`：</span><span class="sxs-lookup"><span data-stu-id="f3236-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="f3236-269">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="f3236-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f3236-270">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="f3236-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f3236-271">检查注册</span><span class="sxs-lookup"><span data-stu-id="f3236-271">Examine Register</span></span>

<span data-ttu-id="f3236-272">当用户单击 "**注册**" 链接时， `RegisterModel.OnPostAsync`将调用该操作。</span><span class="sxs-lookup"><span data-stu-id="f3236-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f3236-273">用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对`_userManager`对象创建的。</span><span class="sxs-lookup"><span data-stu-id="f3236-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="f3236-274">`_userManager`由依赖关系注入提供：</span><span class="sxs-lookup"><span data-stu-id="f3236-274">`_userManager` is provided by dependency injection):</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="f3236-275">如果用户已成功创建，则对的调用会登录该用户`_signInManager.SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="f3236-275">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="f3236-276">**注意：** 请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="f3236-276">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="f3236-277">登录</span><span class="sxs-lookup"><span data-stu-id="f3236-277">Log in</span></span>

<span data-ttu-id="f3236-278">当出现以下情况时，将显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="f3236-278">The Login form is displayed when:</span></span>

* <span data-ttu-id="f3236-279">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="f3236-279">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f3236-280">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="f3236-280">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f3236-281">提交登录页上的窗体时，将调用`OnPostAsync`该操作。</span><span class="sxs-lookup"><span data-stu-id="f3236-281">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f3236-282">`PasswordSignInAsync`对`_signInManager`对象调用（由依赖关系注入提供）。</span><span class="sxs-lookup"><span data-stu-id="f3236-282">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="f3236-283">基类`Controller`公开了`User`可从控制器方法访问的属性。</span><span class="sxs-lookup"><span data-stu-id="f3236-283">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="f3236-284">例如，可以枚举`User.Claims`并做出授权决策。</span><span class="sxs-lookup"><span data-stu-id="f3236-284">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="f3236-285">有关详细信息，请参阅 <xref:security/authorization/introduction>。</span><span class="sxs-lookup"><span data-stu-id="f3236-285">For more information, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="f3236-286">注销</span><span class="sxs-lookup"><span data-stu-id="f3236-286">Log out</span></span>

<span data-ttu-id="f3236-287">"**注销**" 链接将调用`LogoutModel.OnPost`该操作。</span><span class="sxs-lookup"><span data-stu-id="f3236-287">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="f3236-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="f3236-288">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="f3236-289">Post 在*Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="f3236-289">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="f3236-290">考试Identity</span><span class="sxs-lookup"><span data-stu-id="f3236-290">Test Identity</span></span>

<span data-ttu-id="f3236-291">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="f3236-291">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f3236-292">若要Identity进行测试[`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) ，请将添加到 "隐私" 页。</span><span class="sxs-lookup"><span data-stu-id="f3236-292">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="f3236-293">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="f3236-293">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f3236-294">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="f3236-294">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="f3236-295">浏览Identity</span><span class="sxs-lookup"><span data-stu-id="f3236-295">Explore Identity</span></span>

<span data-ttu-id="f3236-296">Identity了解更多详细信息：</span><span class="sxs-lookup"><span data-stu-id="f3236-296">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f3236-297">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="f3236-297">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f3236-298">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="f3236-298">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a>Identity<span data-ttu-id="f3236-299">组分</span><span class="sxs-lookup"><span data-stu-id="f3236-299"> Components</span></span>

<span data-ttu-id="f3236-300">所有Identity相关 NuGet 包都包含在[AspNetCore 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="f3236-300">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f3236-301">的主包Identity为[AspNetCore。Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="f3236-301">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f3236-302">此程序包包含用于 ASP.NET Core Identity的核心接口集，由`Microsoft.AspNetCore.Identity.EntityFrameworkCore`提供。</span><span class="sxs-lookup"><span data-stu-id="f3236-302">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="f3236-303">迁移到 ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="f3236-303">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f3236-304">有关迁移现有Identity存储的详细信息和指南，请参阅[迁移身份验证Identity和](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="f3236-304">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f3236-305">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="f3236-305">Setting password strength</span></span>

<span data-ttu-id="f3236-306">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="f3236-306">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f3236-307">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f3236-307">Next Steps</span></span>

* <span data-ttu-id="f3236-308">有关使用 SQLite 进行配置Identity的信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131)。</span><span class="sxs-lookup"><span data-stu-id="f3236-308">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* <span data-ttu-id="f3236-309">[对Identity](xref:security/authentication/identity-configuration)</span><span class="sxs-lookup"><span data-stu-id="f3236-309">[Configure Identity](xref:security/authentication/identity-configuration)</span></span>
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
