---
title: ASP.NET Core 简介 Identity
author: rick-anderson
description: Identity与 ASP.NET Core 应用一起使用。 了解如何 (RequireDigit、RequiredLength、RequiredUniqueChars 等) 设置密码要求。
ms.author: riande
ms.date: 7/15/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity
ms.openlocfilehash: 67bf24d8f871c4e80ed91f5f437895fe29e09087
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021232"
---
# <a name="introduction-to-no-locidentity-on-aspnet-core"></a><span data-ttu-id="41bfb-104">ASP.NET Core 简介 Identity</span><span class="sxs-lookup"><span data-stu-id="41bfb-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="41bfb-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="41bfb-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="41bfb-106">ASP.NET Core Identity：</span><span class="sxs-lookup"><span data-stu-id="41bfb-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="41bfb-107">是一个 API，它支持用户界面) 登录功能 (UI。</span><span class="sxs-lookup"><span data-stu-id="41bfb-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="41bfb-108">管理用户、密码、配置文件数据、角色、声明、令牌、电子邮件确认等。</span><span class="sxs-lookup"><span data-stu-id="41bfb-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="41bfb-109">用户可以使用存储在中的登录信息创建一个帐户， Identity 也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="41bfb-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="41bfb-110">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="41bfb-111">GitHub 上提供了[ Identity 源代码](https://github.com/dotnet/AspNetCore/tree/master/src/Identity)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/master/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="41bfb-112">[基架 Identity ](xref:security/authentication/scaffold-identity)并查看生成的文件以查看与的模板交互 Identity 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="41bfb-113">Identity通常使用 SQL Server 数据库配置以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="41bfb-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="41bfb-114">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="41bfb-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="41bfb-115">在本主题中，你将了解如何使用 Identity 注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="41bfb-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="41bfb-116">注意：这些模板将用户的用户名和电子邮件视为相同。</span><span class="sxs-lookup"><span data-stu-id="41bfb-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="41bfb-117">有关创建使用的应用程序的更多详细说明 Identity ，请参阅[后续步骤](#next)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="41bfb-118">[Microsoft 标识平台](/azure/active-directory/develop/)是：</span><span class="sxs-lookup"><span data-stu-id="41bfb-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="41bfb-119">) 开发人员平台 Azure AD Azure Active Directory (的演变。</span><span class="sxs-lookup"><span data-stu-id="41bfb-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="41bfb-120">与 ASP.NET Core 无关 Identity 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="41bfb-121">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([如何下载](xref:index#how-to-download-a-sample)) 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="41bfb-122">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="41bfb-122">Create a Web app with authentication</span></span>

<span data-ttu-id="41bfb-123">使用单独的用户帐户创建 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="41bfb-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41bfb-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41bfb-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="41bfb-125">选择 "**文件**" " > **新建** > **项目**"。</span><span class="sxs-lookup"><span data-stu-id="41bfb-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="41bfb-126">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="41bfb-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="41bfb-127">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="41bfb-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="41bfb-128">单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="41bfb-128">Click **OK**.</span></span>
* <span data-ttu-id="41bfb-129">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="41bfb-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="41bfb-130">选择**单个用户帐户**，然后单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="41bfb-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="41bfb-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="41bfb-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="41bfb-132">上述命令 Razor 使用 SQLite 创建 web 应用。</span><span class="sxs-lookup"><span data-stu-id="41bfb-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="41bfb-133">若要创建具有 LocalDB 的 web 应用，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="41bfb-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="41bfb-134">生成的项目[ Razor 以类库形式](xref:razor-pages/ui-class)提供[ASP.NET Core Identity ](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="41bfb-135">类库 Identity Razor 公开的终结点 `Identity` 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="41bfb-136">例如：</span><span class="sxs-lookup"><span data-stu-id="41bfb-136">For example:</span></span>

* <span data-ttu-id="41bfb-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="41bfb-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="41bfb-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="41bfb-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="41bfb-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="41bfb-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="41bfb-140">应用迁移</span><span class="sxs-lookup"><span data-stu-id="41bfb-140">Apply migrations</span></span>

<span data-ttu-id="41bfb-141">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="41bfb-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41bfb-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41bfb-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="41bfb-143">在包管理器控制台中运行以下命令 (PMC) ：</span><span class="sxs-lookup"><span data-stu-id="41bfb-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="41bfb-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="41bfb-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="41bfb-145">使用 SQLite 时，此步骤不需要迁移。</span><span class="sxs-lookup"><span data-stu-id="41bfb-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="41bfb-146">对于 LocalDB，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="41bfb-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="41bfb-147">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="41bfb-147">Test Register and Login</span></span>

<span data-ttu-id="41bfb-148">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="41bfb-148">Run the app and register a user.</span></span> <span data-ttu-id="41bfb-149">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="41bfb-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="41bfb-150">配置 Identity 服务</span><span class="sxs-lookup"><span data-stu-id="41bfb-150">Configure Identity services</span></span>

<span data-ttu-id="41bfb-151">中添加了服务 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="41bfb-152">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="41bfb-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="41bfb-153">前面突出显示的代码 Identity 用默认选项值进行配置。</span><span class="sxs-lookup"><span data-stu-id="41bfb-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="41bfb-154">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="41bfb-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="41bfb-155">Identity通过调用启用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="41bfb-156">`UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="41bfb-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

<span data-ttu-id="41bfb-157">模板生成的应用不使用[授权](xref:security/authorization/secure-data)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-157">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="41bfb-158">`app.UseAuthorization`包括，以确保在应用添加授权时，按正确的顺序添加。</span><span class="sxs-lookup"><span data-stu-id="41bfb-158">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="41bfb-159">`UseRouting`、 `UseAuthentication` 、 `UseAuthorization` 和 `UseEndpoints` 必须按前面的代码中所示的顺序调用。</span><span class="sxs-lookup"><span data-stu-id="41bfb-159">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="41bfb-160">有关和的详细 `IdentityOptions` 信息 `Startup` ，请参阅 <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-160">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="41bfb-161">基架注册、登录、注销和 RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="41bfb-161">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41bfb-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41bfb-162">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="41bfb-163">添加 `Register` 、 `Login` 、 `LogOut` 和 `RegisterConfirmation` 文件。</span><span class="sxs-lookup"><span data-stu-id="41bfb-163">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="41bfb-164">将[基架标识置于 Razor 具有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明的项目中，以生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="41bfb-164">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="41bfb-165">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="41bfb-165">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="41bfb-166">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="41bfb-166">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="41bfb-167">否则，请使用正确的命名空间 `ApplicationDbContext` ：</span><span class="sxs-lookup"><span data-stu-id="41bfb-167">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="41bfb-168">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="41bfb-168">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="41bfb-169">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="41bfb-169">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="41bfb-170">有关基架的详细信息 Identity ，请参阅[ Razor 使用授权将基架标识转换为项目](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-170">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="41bfb-171">检查注册</span><span class="sxs-lookup"><span data-stu-id="41bfb-171">Examine Register</span></span>

<span data-ttu-id="41bfb-172">当用户单击页面上的 "**注册**" 按钮时 `Register` ，将 `RegisterModel.OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="41bfb-172">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="41bfb-173">用户由[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)在对象上创建 `_userManager` ：</span><span class="sxs-lookup"><span data-stu-id="41bfb-173">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="41bfb-174">登录</span><span class="sxs-lookup"><span data-stu-id="41bfb-174">Log in</span></span>

<span data-ttu-id="41bfb-175">当出现以下情况时，将显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="41bfb-175">The Login form is displayed when:</span></span>

* <span data-ttu-id="41bfb-176">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="41bfb-176">The **Log in** link is selected.</span></span>
* <span data-ttu-id="41bfb-177">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="41bfb-177">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="41bfb-178">提交登录页上的窗体时，将 `OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="41bfb-178">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="41bfb-179">`PasswordSignInAsync`对 `_signInManager` 对象调用。</span><span class="sxs-lookup"><span data-stu-id="41bfb-179">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="41bfb-180">有关如何做出授权决策的信息，请参阅 <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-180">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="41bfb-181">注销</span><span class="sxs-lookup"><span data-stu-id="41bfb-181">Log out</span></span>

<span data-ttu-id="41bfb-182">"**注销**" 链接将调用该 `LogoutModel.OnPost` 操作。</span><span class="sxs-lookup"><span data-stu-id="41bfb-182">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="41bfb-183">在前面的代码中，代码 `return RedirectToPage();` 需要是重定向，以便浏览器执行新请求并更新用户的标识。</span><span class="sxs-lookup"><span data-stu-id="41bfb-183">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="41bfb-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除存储在中的用户声明 cookie 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-184">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="41bfb-185">Post 在*Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="41bfb-185">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-no-locidentity"></a><span data-ttu-id="41bfb-186">考试Identity</span><span class="sxs-lookup"><span data-stu-id="41bfb-186">Test Identity</span></span>

<span data-ttu-id="41bfb-187">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="41bfb-187">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="41bfb-188">若要测试 Identity ，请添加 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ：</span><span class="sxs-lookup"><span data-stu-id="41bfb-188">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="41bfb-189">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="41bfb-189">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="41bfb-190">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="41bfb-190">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="41bfb-191">浏览Identity</span><span class="sxs-lookup"><span data-stu-id="41bfb-191">Explore Identity</span></span>

<span data-ttu-id="41bfb-192">Identity了解更多详细信息：</span><span class="sxs-lookup"><span data-stu-id="41bfb-192">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="41bfb-193">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="41bfb-193">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="41bfb-194">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="41bfb-194">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="41bfb-195">Identity组分</span><span class="sxs-lookup"><span data-stu-id="41bfb-195">Identity Components</span></span>

<span data-ttu-id="41bfb-196">所有 Identity 相关的 NuGet 包都包含在[ASP.NET Core 共享框架](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)中。</span><span class="sxs-lookup"><span data-stu-id="41bfb-196">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="41bfb-197">的主包为 Identity [AspNetCore。 Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="41bfb-197">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="41bfb-198">此程序包包含用于 ASP.NET Core 的核心接口集 Identity ，由提供 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-198">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="41bfb-199">迁移到 ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="41bfb-199">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="41bfb-200">有关迁移现有存储的详细信息和指南 Identity ，请参阅[迁移身份验证 Identity 和](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-200">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="41bfb-201">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="41bfb-201">Setting password strength</span></span>

<span data-ttu-id="41bfb-202">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-202">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="41bfb-203">AddDefault Identity 并添加Identity</span><span class="sxs-lookup"><span data-stu-id="41bfb-203">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="41bfb-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="41bfb-204"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="41bfb-205">调用 `AddDefaultIdentity` 类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="41bfb-205">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="41bfb-206">有关详细信息，请参阅[AddDefault Identity 源](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-206">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-no-locidentity-assets"></a><span data-ttu-id="41bfb-207">禁止发布静态 Identity 资产</span><span class="sxs-lookup"><span data-stu-id="41bfb-207">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="41bfb-208">若要防止将静态 Identity 资产发布 (用于 UI) 的用户的样式和 JavaScript 文件 Identity ，请将以下 `ResolveStaticWebAssetsInputsDependsOn` 属性和目标添加 `RemoveIdentityAssets` 到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="41bfb-208">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

<a name="next"></a>

## <a name="next-steps"></a><span data-ttu-id="41bfb-209">后续步骤</span><span class="sxs-lookup"><span data-stu-id="41bfb-209">Next Steps</span></span>

* <span data-ttu-id="41bfb-210">[ASP.NET Core Identity 源代码](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="41bfb-210">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="41bfb-211">有关使用 SQLite 进行配置的信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-211">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="41bfb-212">配置 Identity</span><span class="sxs-lookup"><span data-stu-id="41bfb-212">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="41bfb-213">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="41bfb-213">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="41bfb-214">ASP.NET Core Identity 是将登录功能添加到 ASP.NET Core 应用的成员资格系统。</span><span class="sxs-lookup"><span data-stu-id="41bfb-214">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="41bfb-215">用户可以使用存储在中的登录信息创建一个帐户， Identity 也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="41bfb-215">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="41bfb-216">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-216">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="41bfb-217">Identity可以使用 SQL Server 数据库配置以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="41bfb-217">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="41bfb-218">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="41bfb-218">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="41bfb-219">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([如何下载](xref:index#how-to-download-a-sample)) 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-219">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="41bfb-220">在本主题中，你将了解如何使用 Identity 注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="41bfb-220">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="41bfb-221">有关创建使用的应用程序的更多详细说明 Identity ，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="41bfb-221">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultno-locidentity-and-addno-locidentity"></a><span data-ttu-id="41bfb-222">AddDefault Identity 并添加Identity</span><span class="sxs-lookup"><span data-stu-id="41bfb-222">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="41bfb-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*>是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="41bfb-223"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="41bfb-224">调用 `AddDefaultIdentity` 类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="41bfb-224">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="41bfb-225">有关详细信息，请参阅[AddDefault Identity 源](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-225">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="41bfb-226">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="41bfb-226">Create a Web app with authentication</span></span>

<span data-ttu-id="41bfb-227">使用单独的用户帐户创建 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="41bfb-227">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41bfb-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41bfb-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="41bfb-229">选择 "**文件**" " > **新建** > **项目**"。</span><span class="sxs-lookup"><span data-stu-id="41bfb-229">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="41bfb-230">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="41bfb-230">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="41bfb-231">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="41bfb-231">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="41bfb-232">单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="41bfb-232">Click **OK**.</span></span>
* <span data-ttu-id="41bfb-233">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="41bfb-233">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="41bfb-234">选择**单个用户帐户**，然后单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="41bfb-234">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="41bfb-235">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="41bfb-235">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="41bfb-236">生成的项目[ Razor 以类库形式](xref:razor-pages/ui-class)提供[ASP.NET Core Identity ](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-236">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="41bfb-237">类库 Identity Razor 公开的终结点 `Identity` 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-237">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="41bfb-238">例如：</span><span class="sxs-lookup"><span data-stu-id="41bfb-238">For example:</span></span>

* <span data-ttu-id="41bfb-239">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="41bfb-239">/Identity/Account/Login</span></span>
* <span data-ttu-id="41bfb-240">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="41bfb-240">/Identity/Account/Logout</span></span>
* <span data-ttu-id="41bfb-241">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="41bfb-241">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="41bfb-242">应用迁移</span><span class="sxs-lookup"><span data-stu-id="41bfb-242">Apply migrations</span></span>

<span data-ttu-id="41bfb-243">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="41bfb-243">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41bfb-244">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41bfb-244">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="41bfb-245">在包管理器控制台中运行以下命令 (PMC) ：</span><span class="sxs-lookup"><span data-stu-id="41bfb-245">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="41bfb-246">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="41bfb-246">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="41bfb-247">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="41bfb-247">Test Register and Login</span></span>

<span data-ttu-id="41bfb-248">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="41bfb-248">Run the app and register a user.</span></span> <span data-ttu-id="41bfb-249">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="41bfb-249">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-no-locidentity-services"></a><span data-ttu-id="41bfb-250">配置 Identity 服务</span><span class="sxs-lookup"><span data-stu-id="41bfb-250">Configure Identity services</span></span>

<span data-ttu-id="41bfb-251">中添加了服务 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-251">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="41bfb-252">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="41bfb-252">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="41bfb-253">前面的代码 Identity 用默认选项值进行配置。</span><span class="sxs-lookup"><span data-stu-id="41bfb-253">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="41bfb-254">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="41bfb-254">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="41bfb-255">Identity通过调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)启用。</span><span class="sxs-lookup"><span data-stu-id="41bfb-255">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="41bfb-256">`UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="41bfb-256">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="41bfb-257">有关详细信息，请参阅[ Identity Options 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-257">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="41bfb-258">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="41bfb-258">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="41bfb-259">将[基架标识置于 Razor 具有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明的项目中，以生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="41bfb-259">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41bfb-260">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41bfb-260">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="41bfb-261">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="41bfb-261">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="41bfb-262">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="41bfb-262">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="41bfb-263">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="41bfb-263">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="41bfb-264">否则，请使用正确的命名空间 `ApplicationDbContext` ：</span><span class="sxs-lookup"><span data-stu-id="41bfb-264">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="41bfb-265">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="41bfb-265">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="41bfb-266">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="41bfb-266">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="41bfb-267">检查注册</span><span class="sxs-lookup"><span data-stu-id="41bfb-267">Examine Register</span></span>

<span data-ttu-id="41bfb-268">当用户单击 "**注册**" 链接时，将 `RegisterModel.OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="41bfb-268">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="41bfb-269">用户由[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)在对象上创建 `_userManager` ：</span><span class="sxs-lookup"><span data-stu-id="41bfb-269">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="41bfb-270">如果用户已成功创建，则对的调用会登录该用户 `_signInManager.SignInAsync` 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-270">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="41bfb-271">**注意：** 请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="41bfb-271">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="41bfb-272">登录</span><span class="sxs-lookup"><span data-stu-id="41bfb-272">Log in</span></span>

<span data-ttu-id="41bfb-273">当出现以下情况时，将显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="41bfb-273">The Login form is displayed when:</span></span>

* <span data-ttu-id="41bfb-274">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="41bfb-274">The **Log in** link is selected.</span></span>
* <span data-ttu-id="41bfb-275">用户尝试访问他们无权访问的受限制的页面，**或**未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="41bfb-275">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="41bfb-276">提交登录页上的窗体时，将 `OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="41bfb-276">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="41bfb-277">`PasswordSignInAsync`对 `_signInManager` 对象调用。</span><span class="sxs-lookup"><span data-stu-id="41bfb-277">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="41bfb-278">有关如何做出授权决策的信息，请参阅 <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-278">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="41bfb-279">注销</span><span class="sxs-lookup"><span data-stu-id="41bfb-279">Log out</span></span>

<span data-ttu-id="41bfb-280">"**注销**" 链接将调用该 `LogoutModel.OnPost` 操作。</span><span class="sxs-lookup"><span data-stu-id="41bfb-280">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="41bfb-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除存储在中的用户声明 cookie 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-281">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="41bfb-282">Post 在*Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="41bfb-282">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-no-locidentity"></a><span data-ttu-id="41bfb-283">考试Identity</span><span class="sxs-lookup"><span data-stu-id="41bfb-283">Test Identity</span></span>

<span data-ttu-id="41bfb-284">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="41bfb-284">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="41bfb-285">若要进行测试 Identity ，请将添加 [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 到 "隐私" 页。</span><span class="sxs-lookup"><span data-stu-id="41bfb-285">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="41bfb-286">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="41bfb-286">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="41bfb-287">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="41bfb-287">You are redirected to the login page.</span></span>

### <a name="explore-no-locidentity"></a><span data-ttu-id="41bfb-288">浏览Identity</span><span class="sxs-lookup"><span data-stu-id="41bfb-288">Explore Identity</span></span>

<span data-ttu-id="41bfb-289">Identity了解更多详细信息：</span><span class="sxs-lookup"><span data-stu-id="41bfb-289">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="41bfb-290">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="41bfb-290">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="41bfb-291">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="41bfb-291">Examine the source of each page and step through the debugger.</span></span>

## <a name="no-locidentity-components"></a><span data-ttu-id="41bfb-292">Identity组分</span><span class="sxs-lookup"><span data-stu-id="41bfb-292">Identity Components</span></span>

<span data-ttu-id="41bfb-293">所有 Identity 相关 NuGet 包都包含在[AspNetCore 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="41bfb-293">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="41bfb-294">的主包为 Identity [AspNetCore。 Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="41bfb-294">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="41bfb-295">此程序包包含用于 ASP.NET Core 的核心接口集 Identity ，由提供 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-295">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-no-locidentity"></a><span data-ttu-id="41bfb-296">迁移到 ASP.NET CoreIdentity</span><span class="sxs-lookup"><span data-stu-id="41bfb-296">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="41bfb-297">有关迁移现有存储的详细信息和指南 Identity ，请参阅[迁移身份验证 Identity 和](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-297">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="41bfb-298">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="41bfb-298">Setting password strength</span></span>

<span data-ttu-id="41bfb-299">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="41bfb-299">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="41bfb-300">后续步骤</span><span class="sxs-lookup"><span data-stu-id="41bfb-300">Next Steps</span></span>

* <span data-ttu-id="41bfb-301">有关使用 SQLite 进行配置的信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity 。</span><span class="sxs-lookup"><span data-stu-id="41bfb-301">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="41bfb-302">配置 Identity</span><span class="sxs-lookup"><span data-stu-id="41bfb-302">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
