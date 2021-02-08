---
title: ASP.NET Core 简介 Identity
author: rick-anderson
description: Identity与 ASP.NET Core 应用一起使用。 了解如何 (RequireDigit、RequiredLength、RequiredUniqueChars 等) 设置密码要求。
ms.author: riande
ms.date: 7/15/2020
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
uid: security/authentication/identity
ms.openlocfilehash: 266781d0abc564952e124bc6eca3805c07592251
ms.sourcegitcommit: 50d3e939a90c5480df480f651dda032901468dd5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99819050"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="64627-104">ASP.NET Core 简介 Identity</span><span class="sxs-lookup"><span data-stu-id="64627-104">Introduction to Identity on ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="64627-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="64627-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="64627-106">ASP.NET Core Identity:</span><span class="sxs-lookup"><span data-stu-id="64627-106">ASP.NET Core Identity:</span></span>

* <span data-ttu-id="64627-107">是一个 API，它支持用户界面) 登录功能 (UI。</span><span class="sxs-lookup"><span data-stu-id="64627-107">Is an API that supports user interface (UI) login functionality.</span></span>
* <span data-ttu-id="64627-108">管理用户、密码、配置文件数据、角色、声明、令牌、电子邮件确认等。</span><span class="sxs-lookup"><span data-stu-id="64627-108">Manages users, passwords, profile data, roles, claims, tokens, email confirmation, and more.</span></span>

<span data-ttu-id="64627-109">用户可以使用存储在中的登录信息创建一个帐户， Identity 也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="64627-109">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="64627-110">支持的外部登录提供程序包括 [Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="64627-110">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

[!INCLUDE[](~/includes/requireAuth.md)]

<span data-ttu-id="64627-111">GitHub 上提供了[ Identity 源代码](https://github.com/dotnet/AspNetCore/tree/main/src/Identity)。</span><span class="sxs-lookup"><span data-stu-id="64627-111">The [Identity source code](https://github.com/dotnet/AspNetCore/tree/main/src/Identity) is available on GitHub.</span></span> <span data-ttu-id="64627-112">[基架 Identity ](xref:security/authentication/scaffold-identity)并查看生成的文件以查看与的模板交互 Identity 。</span><span class="sxs-lookup"><span data-stu-id="64627-112">[Scaffold Identity](xref:security/authentication/scaffold-identity) and view the generated files to review the template interaction with Identity.</span></span>

<span data-ttu-id="64627-113">Identity 通常使用 SQL Server 数据库配置以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="64627-113">Identity is typically configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="64627-114">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="64627-114">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="64627-115">在本主题中，你将了解如何使用 Identity 注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="64627-115">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="64627-116">注意：这些模板将用户的用户名和电子邮件视为相同。</span><span class="sxs-lookup"><span data-stu-id="64627-116">Note: the templates treat username and email as the same for users.</span></span> <span data-ttu-id="64627-117">有关创建使用的应用程序的更多详细说明 Identity ，请参阅 [后续步骤](#next)。</span><span class="sxs-lookup"><span data-stu-id="64627-117">For more detailed instructions about creating apps that use Identity, see [Next Steps](#next).</span></span>

<span data-ttu-id="64627-118">[Microsoft 标识平台](/azure/active-directory/develop/) 是：</span><span class="sxs-lookup"><span data-stu-id="64627-118">[Microsoft identity platform](/azure/active-directory/develop/) is:</span></span>

* <span data-ttu-id="64627-119">) 开发人员平台 Azure AD Azure Active Directory (的演变。</span><span class="sxs-lookup"><span data-stu-id="64627-119">An evolution of the Azure Active Directory (Azure AD) developer platform.</span></span>
* <span data-ttu-id="64627-120">与无关 ASP.NET Core Identity 。</span><span class="sxs-lookup"><span data-stu-id="64627-120">Unrelated to ASP.NET Core Identity.</span></span>

[!INCLUDE[](~/includes/IdentityServer4.md)]

<span data-ttu-id="64627-121">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([如何下载](xref:index#how-to-download-a-sample)) 。</span><span class="sxs-lookup"><span data-stu-id="64627-121">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<a name="adi"></a>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="64627-122">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="64627-122">Create a Web app with authentication</span></span>

<span data-ttu-id="64627-123">使用单独的用户帐户创建 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="64627-123">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64627-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64627-124">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="64627-125">选择 " **文件**" " > **新建** > **项目**"。</span><span class="sxs-lookup"><span data-stu-id="64627-125">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="64627-126">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="64627-126">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="64627-127">将项目命名为 **WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="64627-127">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="64627-128">单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="64627-128">Click **OK**.</span></span>
* <span data-ttu-id="64627-129">选择 ASP.NET Core **Web 应用程序**，然后选择 " **更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="64627-129">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="64627-130">选择 **单个用户帐户** ，然后单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="64627-130">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="64627-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="64627-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

<span data-ttu-id="64627-132">上述命令 Razor 使用 SQLite 创建 web 应用。</span><span class="sxs-lookup"><span data-stu-id="64627-132">The preceding command creates a Razor web app using SQLite.</span></span> <span data-ttu-id="64627-133">若要创建具有 LocalDB 的 web 应用，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64627-133">To create the web app with LocalDB, run the following command:</span></span>

```dotnetcli
dotnet new webapp --auth Individual -uld -o WebApp1
```

---

<span data-ttu-id="64627-134">生成的项目 [ASP.NET Core Identity](xref:security/authentication/identity) 以类库形式[ Razor ](xref:razor-pages/ui-class)提供。</span><span class="sxs-lookup"><span data-stu-id="64627-134">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="64627-135">类库 Identity Razor 公开的终结点 `Identity` 。</span><span class="sxs-lookup"><span data-stu-id="64627-135">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="64627-136">例如： 。</span><span class="sxs-lookup"><span data-stu-id="64627-136">For example:</span></span>

* <span data-ttu-id="64627-137">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="64627-137">/Identity/Account/Login</span></span>
* <span data-ttu-id="64627-138">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="64627-138">/Identity/Account/Logout</span></span>
* <span data-ttu-id="64627-139">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="64627-139">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="64627-140">应用迁移</span><span class="sxs-lookup"><span data-stu-id="64627-140">Apply migrations</span></span>

<span data-ttu-id="64627-141">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="64627-141">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64627-142">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64627-142">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64627-143">在包管理器控制台中运行以下命令 (PMC) ：</span><span class="sxs-lookup"><span data-stu-id="64627-143">Run the following command in the Package Manager Console (PMC):</span></span>

`PM> Update-Database`

# <a name="net-core-cli"></a>[<span data-ttu-id="64627-144">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="64627-144">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="64627-145">使用 SQLite 时，此步骤不需要迁移。</span><span class="sxs-lookup"><span data-stu-id="64627-145">Migrations are not necessary at this step when using SQLite.</span></span>

[!INCLUDE [more information on the CLI for EF Core](~/includes/ef-cli.md)]

<span data-ttu-id="64627-146">对于 LocalDB，请运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="64627-146">For LocalDB, run the following command:</span></span>

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="64627-147">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="64627-147">Test Register and Login</span></span>

<span data-ttu-id="64627-148">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="64627-148">Run the app and register a user.</span></span> <span data-ttu-id="64627-149">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 " **寄存器** " 和 " **登录** " 链接。</span><span class="sxs-lookup"><span data-stu-id="64627-149">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="64627-150">配置 Identity 服务</span><span class="sxs-lookup"><span data-stu-id="64627-150">Configure Identity services</span></span>

<span data-ttu-id="64627-151">中添加了服务 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="64627-151">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="64627-152">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="64627-152">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configureservices&highlight=11-99)]

<span data-ttu-id="64627-153">前面突出显示的代码 Identity 用默认选项值进行配置。</span><span class="sxs-lookup"><span data-stu-id="64627-153">The preceding highlighted code configures Identity with default option values.</span></span> <span data-ttu-id="64627-154">服务通过 [依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="64627-154">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="64627-155">Identity 通过调用启用 <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*> 。</span><span class="sxs-lookup"><span data-stu-id="64627-155">Identity is enabled by calling <xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>.</span></span> <span data-ttu-id="64627-156">`UseAuthentication` 将身份验证 [中间件](xref:fundamentals/middleware/index) 添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="64627-156">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp3/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configureservices&highlight=12-99)]

<span data-ttu-id="64627-157">前面的代码 Identity 用默认选项值进行配置。</span><span class="sxs-lookup"><span data-stu-id="64627-157">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="64627-158">服务通过 [依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="64627-158">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="64627-159">Identity 通过调用 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)启用。</span><span class="sxs-lookup"><span data-stu-id="64627-159">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="64627-160">`UseAuthentication` 将身份验证 [中间件](xref:fundamentals/middleware/index) 添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="64627-160">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp5x/Startup.cs?name=snippet_configure&highlight=19)]

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="64627-161">模板生成的应用不使用 [授权](xref:security/authorization/secure-data)。</span><span class="sxs-lookup"><span data-stu-id="64627-161">The template-generated app doesn't use [authorization](xref:security/authorization/secure-data).</span></span> <span data-ttu-id="64627-162">`app.UseAuthorization` 包括，以确保在应用添加授权时，按正确的顺序添加。</span><span class="sxs-lookup"><span data-stu-id="64627-162">`app.UseAuthorization` is included to ensure it's added in the correct order should the app add authorization.</span></span> <span data-ttu-id="64627-163">`UseRouting`、 `UseAuthentication` 、 `UseAuthorization` 和 `UseEndpoints` 必须按前面的代码中所示的顺序调用。</span><span class="sxs-lookup"><span data-stu-id="64627-163">`UseRouting`, `UseAuthentication`, `UseAuthorization`, and `UseEndpoints` must be called in the order shown in the preceding code.</span></span>

<span data-ttu-id="64627-164">有关和的详细 `IdentityOptions` 信息 `Startup` ，请参阅 <xref:Microsoft.AspNetCore.Identity.IdentityOptions> 和 [应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="64627-164">For more information on `IdentityOptions` and `Startup`, see <xref:Microsoft.AspNetCore.Identity.IdentityOptions> and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-logout-and-registerconfirmation"></a><span data-ttu-id="64627-165">基架注册、登录、注销和 RegisterConfirmation</span><span class="sxs-lookup"><span data-stu-id="64627-165">Scaffold Register, Login, LogOut, and RegisterConfirmation</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64627-166">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64627-166">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64627-167">添加 `Register` 、 `Login` 、 `LogOut` 和 `RegisterConfirmation` 文件。</span><span class="sxs-lookup"><span data-stu-id="64627-167">Add the `Register`, `Login`, `LogOut`, and `RegisterConfirmation` files.</span></span> <span data-ttu-id="64627-168">将 [基架标识置于 Razor 具有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 说明的项目中，以生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="64627-168">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="64627-169">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="64627-169">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="64627-170">如果创建的项目的名称为 **WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="64627-170">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="64627-171">否则，请使用正确的命名空间 `ApplicationDbContext` ：</span><span class="sxs-lookup"><span data-stu-id="64627-171">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout;Account.RegisterConfirmation"
```

<span data-ttu-id="64627-172">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="64627-172">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="64627-173">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="64627-173">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

<span data-ttu-id="64627-174">有关基架的详细信息 Identity ，请参阅 [ Razor 使用授权将基架标识转换为项目](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)。</span><span class="sxs-lookup"><span data-stu-id="64627-174">For more information on scaffolding Identity, see [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization).</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="64627-175">检查注册</span><span class="sxs-lookup"><span data-stu-id="64627-175">Examine Register</span></span>

<span data-ttu-id="64627-176">当用户单击页面上的 " **注册** " 按钮时 `Register` ，将 `RegisterModel.OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="64627-176">When a user clicks the **Register** button on the `Register` page, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="64627-177">用户由 [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 在对象上创建 `_userManager` ：</span><span class="sxs-lookup"><span data-stu-id="64627-177">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=9)]

<!-- .NET 5 fixes this, see
https://github.com/dotnet/aspnetcore/blob/master/src/Identity/UI/src/Areas/Identity/Pages/V4/Account/RegisterConfirmation.cshtml.cs#L74-L77
-->
[!INCLUDE[](~/includes/disableVer.md)]

### <a name="log-in"></a><span data-ttu-id="64627-178">登录</span><span class="sxs-lookup"><span data-stu-id="64627-178">Log in</span></span>

<span data-ttu-id="64627-179">当出现以下情况时，将显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="64627-179">The Login form is displayed when:</span></span>

* <span data-ttu-id="64627-180">选择 " **登录** " 链接。</span><span class="sxs-lookup"><span data-stu-id="64627-180">The **Log in** link is selected.</span></span>
* <span data-ttu-id="64627-181">用户尝试访问他们无权访问的受限制的页面， **或** 未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="64627-181">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="64627-182">提交登录页上的窗体时，将 `OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="64627-182">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="64627-183">`PasswordSignInAsync` 对 `_signInManager` 对象调用。</span><span class="sxs-lookup"><span data-stu-id="64627-183">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="64627-184">有关如何做出授权决策的信息，请参阅 <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="64627-184">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="64627-185">注销</span><span class="sxs-lookup"><span data-stu-id="64627-185">Log out</span></span>

<span data-ttu-id="64627-186">" **注销** " 链接将调用该 `LogoutModel.OnPost` 操作。</span><span class="sxs-lookup"><span data-stu-id="64627-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp3/Areas/Identity/Pages/Account/Logout.cshtml.cs?highlight=36)]

<span data-ttu-id="64627-187">在前面的代码中，代码 `return RedirectToPage();` 需要是重定向，以便浏览器执行新请求并更新用户的标识。</span><span class="sxs-lookup"><span data-stu-id="64627-187">In the preceding code, the code `return RedirectToPage();` needs to be a redirect so that the browser performs a new request and the identity for the user gets updated.</span></span>

<span data-ttu-id="64627-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 清除存储在中的用户声明 cookie 。</span><span class="sxs-lookup"><span data-stu-id="64627-188">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="64627-189">Post 在 *Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="64627-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp3/Pages/Shared/_LoginPartial.cshtml?highlight=15)]

## <a name="test-identity"></a><span data-ttu-id="64627-190">考试 Identity</span><span class="sxs-lookup"><span data-stu-id="64627-190">Test Identity</span></span>

<span data-ttu-id="64627-191">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="64627-191">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="64627-192">若要测试 Identity ，请添加 [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute) ：</span><span class="sxs-lookup"><span data-stu-id="64627-192">To test Identity, add [`[Authorize]`](xref:Microsoft.AspNetCore.Authorization.AuthorizeAttribute):</span></span>

[!code-csharp[](identity/sample/WebApp3/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="64627-193">如果已登录，请注销。运行应用并选择 " **隐私** " 链接。</span><span class="sxs-lookup"><span data-stu-id="64627-193">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="64627-194">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="64627-194">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="64627-195">浏览 Identity</span><span class="sxs-lookup"><span data-stu-id="64627-195">Explore Identity</span></span>

<span data-ttu-id="64627-196">Identity了解更多详细信息：</span><span class="sxs-lookup"><span data-stu-id="64627-196">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="64627-197">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="64627-197">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="64627-198">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="64627-198">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="64627-199">Identity 组分</span><span class="sxs-lookup"><span data-stu-id="64627-199">Identity Components</span></span>

<span data-ttu-id="64627-200">所有 Identity 相关的 NuGet 包都包含在 [ASP.NET Core 共享框架](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework)中。</span><span class="sxs-lookup"><span data-stu-id="64627-200">All the Identity-dependent NuGet packages are included in the [ASP.NET Core shared framework](xref:aspnetcore-3.0#use-the-aspnet-core-shared-framework).</span></span>

<span data-ttu-id="64627-201">的主包为 Identity [AspNetCore。 Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="64627-201">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="64627-202">此包包含的核心接口集 ASP.NET Core Identity ，由提供 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="64627-202">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="64627-203">迁移到 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="64627-203">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="64627-204">有关迁移现有存储的详细信息和指南 Identity ，请参阅[迁移身份验证 Identity 和](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="64627-204">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="64627-205">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="64627-205">Setting password strength</span></span>

<span data-ttu-id="64627-206">有关设置最小密码要求的示例，请参阅 [配置](#pw) 。</span><span class="sxs-lookup"><span data-stu-id="64627-206">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="64627-207">AddDefault Identity 并添加Identity</span><span class="sxs-lookup"><span data-stu-id="64627-207">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="64627-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="64627-208"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="64627-209">调用 `AddDefaultIdentity` 类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="64627-209">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="64627-210">有关详细信息，请参阅 [AddDefault Identity 源](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 。</span><span class="sxs-lookup"><span data-stu-id="64627-210">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/3.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="prevent-publish-of-static-identity-assets"></a><span data-ttu-id="64627-211">禁止发布静态 Identity 资产</span><span class="sxs-lookup"><span data-stu-id="64627-211">Prevent publish of static Identity assets</span></span>

<span data-ttu-id="64627-212">若要防止将静态 Identity 资产发布 (用于 UI) 的用户的样式和 JavaScript 文件 Identity ，请将以下 `ResolveStaticWebAssetsInputsDependsOn` 属性和目标添加 `RemoveIdentityAssets` 到应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="64627-212">To prevent publishing static Identity assets (stylesheets and JavaScript files for Identity UI) to the web root, add the following `ResolveStaticWebAssetsInputsDependsOn` property and `RemoveIdentityAssets` target to the app's project file:</span></span>

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

## <a name="next-steps"></a><span data-ttu-id="64627-213">后续步骤</span><span class="sxs-lookup"><span data-stu-id="64627-213">Next Steps</span></span>

* <span data-ttu-id="64627-214">[ASP.NET Core Identity 源代码](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span><span class="sxs-lookup"><span data-stu-id="64627-214">[ASP.NET Core Identity source code](https://github.com/dotnet/aspnetcore/tree/master/src/Identity)</span></span>
* <span data-ttu-id="64627-215">有关使用 SQLite 进行配置的信息，请参阅 [此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity 。</span><span class="sxs-lookup"><span data-stu-id="64627-215">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="64627-216">配置 Identity</span><span class="sxs-lookup"><span data-stu-id="64627-216">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="64627-217">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="64627-217">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="64627-218">ASP.NET Core Identity 是将登录功能添加到 ASP.NET Core 应用的成员资格系统。</span><span class="sxs-lookup"><span data-stu-id="64627-218">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="64627-219">用户可以使用存储在中的登录信息创建一个帐户， Identity 也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="64627-219">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="64627-220">支持的外部登录提供程序包括 [Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="64627-220">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="64627-221">Identity 可以使用 SQL Server 数据库配置以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="64627-221">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="64627-222">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="64627-222">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="64627-223">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([如何下载](xref:index#how-to-download-a-sample)) 。</span><span class="sxs-lookup"><span data-stu-id="64627-223">[View or download the sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="64627-224">在本主题中，你将了解如何使用 Identity 注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="64627-224">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="64627-225">有关创建使用的应用程序的更多详细说明 Identity ，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="64627-225">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="64627-226">AddDefault Identity 并添加Identity</span><span class="sxs-lookup"><span data-stu-id="64627-226">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="64627-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> 是在 ASP.NET Core 2.1 中引入的。</span><span class="sxs-lookup"><span data-stu-id="64627-227"><xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionUIExtensions.AddDefaultIdentity*> was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="64627-228">调用 `AddDefaultIdentity` 类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="64627-228">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* <xref:Microsoft.Extensions.DependencyInjection.IdentityServiceCollectionExtensions.AddIdentity*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>
* <xref:Microsoft.AspNetCore.Identity.IdentityBuilderExtensions.AddDefaultTokenProviders*>

<span data-ttu-id="64627-229">有关详细信息，请参阅 [AddDefault Identity 源](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) 。</span><span class="sxs-lookup"><span data-stu-id="64627-229">See [AddDefaultIdentity source](https://github.com/dotnet/AspNetCore/blob/release/2.1/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="64627-230">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="64627-230">Create a Web app with authentication</span></span>

<span data-ttu-id="64627-231">使用单独的用户帐户创建 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="64627-231">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64627-232">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64627-232">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="64627-233">选择 " **文件**" " > **新建** > **项目**"。</span><span class="sxs-lookup"><span data-stu-id="64627-233">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="64627-234">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="64627-234">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="64627-235">将项目命名为 **WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="64627-235">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="64627-236">单击“确定”。</span><span class="sxs-lookup"><span data-stu-id="64627-236">Click **OK**.</span></span>
* <span data-ttu-id="64627-237">选择 ASP.NET Core **Web 应用程序**，然后选择 " **更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="64627-237">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="64627-238">选择 **单个用户帐户** ，然后单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="64627-238">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="64627-239">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="64627-239">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="64627-240">生成的项目 [ASP.NET Core Identity](xref:security/authentication/identity) 以类库形式[ Razor ](xref:razor-pages/ui-class)提供。</span><span class="sxs-lookup"><span data-stu-id="64627-240">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="64627-241">类库 Identity Razor 公开的终结点 `Identity` 。</span><span class="sxs-lookup"><span data-stu-id="64627-241">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="64627-242">例如： 。</span><span class="sxs-lookup"><span data-stu-id="64627-242">For example:</span></span>

* <span data-ttu-id="64627-243">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="64627-243">/Identity/Account/Login</span></span>
* <span data-ttu-id="64627-244">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="64627-244">/Identity/Account/Logout</span></span>
* <span data-ttu-id="64627-245">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="64627-245">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="64627-246">应用迁移</span><span class="sxs-lookup"><span data-stu-id="64627-246">Apply migrations</span></span>

<span data-ttu-id="64627-247">应用迁移以初始化数据库。</span><span class="sxs-lookup"><span data-stu-id="64627-247">Apply the migrations to initialize the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64627-248">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64627-248">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64627-249">在包管理器控制台中运行以下命令 (PMC) ：</span><span class="sxs-lookup"><span data-stu-id="64627-249">Run the following command in the Package Manager Console (PMC):</span></span>

```powershell
Update-Database
```

# <a name="net-core-cli"></a>[<span data-ttu-id="64627-250">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="64627-250">.NET Core CLI</span></span>](#tab/netcore-cli)

```dotnetcli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="64627-251">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="64627-251">Test Register and Login</span></span>

<span data-ttu-id="64627-252">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="64627-252">Run the app and register a user.</span></span> <span data-ttu-id="64627-253">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 " **寄存器** " 和 " **登录** " 链接。</span><span class="sxs-lookup"><span data-stu-id="64627-253">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="64627-254">配置 Identity 服务</span><span class="sxs-lookup"><span data-stu-id="64627-254">Configure Identity services</span></span>

<span data-ttu-id="64627-255">中添加了服务 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="64627-255">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="64627-256">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="64627-256">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

<span data-ttu-id="64627-257">前面的代码 Identity 用默认选项值进行配置。</span><span class="sxs-lookup"><span data-stu-id="64627-257">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="64627-258">服务通过 [依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="64627-258">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="64627-259">Identity 通过调用 [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)启用。</span><span class="sxs-lookup"><span data-stu-id="64627-259">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="64627-260">`UseAuthentication` 将身份验证 [中间件](xref:fundamentals/middleware/index) 添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="64627-260">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

<span data-ttu-id="64627-261">有关详细信息，请参阅[ Identity Options 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="64627-261">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="64627-262">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="64627-262">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="64627-263">将 [基架标识置于 Razor 具有授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) 说明的项目中，以生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="64627-263">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="64627-264">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="64627-264">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="64627-265">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="64627-265">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="64627-266">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="64627-266">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="64627-267">如果创建的项目的名称为 **WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="64627-267">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="64627-268">否则，请使用正确的命名空间 `ApplicationDbContext` ：</span><span class="sxs-lookup"><span data-stu-id="64627-268">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="64627-269">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="64627-269">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="64627-270">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="64627-270">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="64627-271">检查注册</span><span class="sxs-lookup"><span data-stu-id="64627-271">Examine Register</span></span>

<span data-ttu-id="64627-272">当用户单击 " **注册** " 链接时，将 `RegisterModel.OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="64627-272">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="64627-273">用户由 [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) 在对象上创建 `_userManager` ：</span><span class="sxs-lookup"><span data-stu-id="64627-273">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object:</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7)]

<span data-ttu-id="64627-274">如果用户已成功创建，则对的调用会登录该用户 `_signInManager.SignInAsync` 。</span><span class="sxs-lookup"><span data-stu-id="64627-274">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

<span data-ttu-id="64627-275">**注意：** 请参阅 [帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration) 以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="64627-275">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="64627-276">登录</span><span class="sxs-lookup"><span data-stu-id="64627-276">Log in</span></span>

<span data-ttu-id="64627-277">当出现以下情况时，将显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="64627-277">The Login form is displayed when:</span></span>

* <span data-ttu-id="64627-278">选择 " **登录** " 链接。</span><span class="sxs-lookup"><span data-stu-id="64627-278">The **Log in** link is selected.</span></span>
* <span data-ttu-id="64627-279">用户尝试访问他们无权访问的受限制的页面， **或** 未经系统的身份验证。</span><span class="sxs-lookup"><span data-stu-id="64627-279">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="64627-280">提交登录页上的窗体时，将 `OnPostAsync` 调用该操作。</span><span class="sxs-lookup"><span data-stu-id="64627-280">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="64627-281">`PasswordSignInAsync` 对 `_signInManager` 对象调用。</span><span class="sxs-lookup"><span data-stu-id="64627-281">`PasswordSignInAsync` is called on the `_signInManager` object.</span></span>

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

<span data-ttu-id="64627-282">有关如何做出授权决策的信息，请参阅 <xref:security/authorization/introduction> 。</span><span class="sxs-lookup"><span data-stu-id="64627-282">For information on how to make authorization decisions, see <xref:security/authorization/introduction>.</span></span>

### <a name="log-out"></a><span data-ttu-id="64627-283">注销</span><span class="sxs-lookup"><span data-stu-id="64627-283">Log out</span></span>

<span data-ttu-id="64627-284">" **注销** " 链接将调用该 `LogoutModel.OnPost` 操作。</span><span class="sxs-lookup"><span data-stu-id="64627-284">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="64627-285">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) 清除存储在中的用户声明 cookie 。</span><span class="sxs-lookup"><span data-stu-id="64627-285">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="64627-286">Post 在 *Pages/Shared/_LoginPartial 中指定。 cshtml*：</span><span class="sxs-lookup"><span data-stu-id="64627-286">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-cshtml[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

## <a name="test-identity"></a><span data-ttu-id="64627-287">考试 Identity</span><span class="sxs-lookup"><span data-stu-id="64627-287">Test Identity</span></span>

<span data-ttu-id="64627-288">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="64627-288">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="64627-289">若要进行测试 Identity ，请将添加 [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) 到 "隐私" 页。</span><span class="sxs-lookup"><span data-stu-id="64627-289">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=7)]

<span data-ttu-id="64627-290">如果已登录，请注销。运行应用并选择 " **隐私** " 链接。</span><span class="sxs-lookup"><span data-stu-id="64627-290">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="64627-291">将被重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="64627-291">You are redirected to the login page.</span></span>

### <a name="explore-identity"></a><span data-ttu-id="64627-292">浏览 Identity</span><span class="sxs-lookup"><span data-stu-id="64627-292">Explore Identity</span></span>

<span data-ttu-id="64627-293">Identity了解更多详细信息：</span><span class="sxs-lookup"><span data-stu-id="64627-293">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="64627-294">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="64627-294">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="64627-295">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="64627-295">Examine the source of each page and step through the debugger.</span></span>

## <a name="identity-components"></a><span data-ttu-id="64627-296">Identity 组分</span><span class="sxs-lookup"><span data-stu-id="64627-296">Identity Components</span></span>

<span data-ttu-id="64627-297">所有 Identity 相关 NuGet 包都包含在 [AspNetCore 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="64627-297">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="64627-298">的主包为 Identity [AspNetCore。 Identity ](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)</span><span class="sxs-lookup"><span data-stu-id="64627-298">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="64627-299">此包包含的核心接口集 ASP.NET Core Identity ，由提供 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 。</span><span class="sxs-lookup"><span data-stu-id="64627-299">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="64627-300">迁移到 ASP.NET Core Identity</span><span class="sxs-lookup"><span data-stu-id="64627-300">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="64627-301">有关迁移现有存储的详细信息和指南 Identity ，请参阅[迁移身份验证 Identity 和](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="64627-301">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="64627-302">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="64627-302">Setting password strength</span></span>

<span data-ttu-id="64627-303">有关设置最小密码要求的示例，请参阅 [配置](#pw) 。</span><span class="sxs-lookup"><span data-stu-id="64627-303">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="64627-304">后续步骤</span><span class="sxs-lookup"><span data-stu-id="64627-304">Next Steps</span></span>

* <span data-ttu-id="64627-305">有关使用 SQLite 进行配置的信息，请参阅 [此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/5131) Identity 。</span><span class="sxs-lookup"><span data-stu-id="64627-305">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/5131) for information on configuring Identity using SQLite.</span></span>
* [<span data-ttu-id="64627-306">配置 Identity</span><span class="sxs-lookup"><span data-stu-id="64627-306">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>

::: moniker-end
