---
title: ASP.NET Core 上的标识简介
author: rick-anderson
description: 将标识与 ASP.NET Core 应用配合使用。 了解如何设置密码要求（RequireDigit、RequiredLength、RequiredUniqueChars 等）。
ms.author: riande
ms.date: 03/26/2019
uid: security/authentication/identity
ms.openlocfilehash: 325a61e6038e79b9a0db72c8360a5cbff2c8ddae
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011208"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="2ad3d-104">ASP.NET Core 上的标识简介</span><span class="sxs-lookup"><span data-stu-id="2ad3d-104">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="2ad3d-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2ad3d-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2ad3d-106">ASP.NET Core 标识是将登录功能添加到 ASP.NET Core 应用的成员资格系统。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-106">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="2ad3d-107">用户可以创建一个具有存储在标识中的登录信息的帐户，也可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="2ad3d-108">支持的外部登录提供程序包括[Facebook、Google、Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="2ad3d-109">可以使用 SQL Server 数据库配置标识，以存储用户名、密码和配置文件数据。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="2ad3d-110">另外，还可以使用另一个永久性存储，例如 Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="2ad3d-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)（[如何下载）](xref:index#how-to-download-a-sample)。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-111">[View or download the sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="2ad3d-112">本主题介绍如何使用标识注册、登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-112">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="2ad3d-113">有关创建使用标识的应用的更多详细说明，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-113">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>

## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="2ad3d-114">AddDefaultIdentity 和 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="2ad3d-114">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="2ad3d-115">ASP.NET Core 2.1 中引入了[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) 。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="2ad3d-116">调用`AddDefaultIdentity`类似于调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="2ad3d-116">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* [<span data-ttu-id="2ad3d-117">AddIdentity</span><span class="sxs-lookup"><span data-stu-id="2ad3d-117">AddIdentity</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [<span data-ttu-id="2ad3d-118">AddDefaultUI</span><span class="sxs-lookup"><span data-stu-id="2ad3d-118">AddDefaultUI</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [<span data-ttu-id="2ad3d-119">AddDefaultTokenProviders</span><span class="sxs-lookup"><span data-stu-id="2ad3d-119">AddDefaultTokenProviders</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

<span data-ttu-id="2ad3d-120">有关详细信息，请参阅[AddDefaultIdentity 源](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-120">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="2ad3d-121">创建具有身份验证的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="2ad3d-121">Create a Web app with authentication</span></span>

<span data-ttu-id="2ad3d-122">使用单个用户帐户创建一个 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2ad3d-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2ad3d-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2ad3d-124">选择“文件” > “新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="2ad3d-125">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="2ad3d-126">将项目命名为**WebApp1** ，使其命名空间与项目下载相同。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="2ad3d-127">单击 **“确定”** 。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-127">Click **OK**.</span></span>
* <span data-ttu-id="2ad3d-128">选择 ASP.NET Core **Web 应用程序**，然后选择 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="2ad3d-129">选择**单个用户帐户**，然后单击 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2ad3d-130">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2ad3d-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="2ad3d-131">生成的项目提供[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-131">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="2ad3d-132">标识 Razor 类库公开带有`Identity`区域的终结点。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-132">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="2ad3d-133">例如：</span><span class="sxs-lookup"><span data-stu-id="2ad3d-133">For example:</span></span>

* <span data-ttu-id="2ad3d-134">/Identity/Account/Login</span><span class="sxs-lookup"><span data-stu-id="2ad3d-134">/Identity/Account/Login</span></span>
* <span data-ttu-id="2ad3d-135">/Identity/Account/Logout</span><span class="sxs-lookup"><span data-stu-id="2ad3d-135">/Identity/Account/Logout</span></span>
* <span data-ttu-id="2ad3d-136">/Identity/Account/Manage</span><span class="sxs-lookup"><span data-stu-id="2ad3d-136">/Identity/Account/Manage</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="2ad3d-137">应用迁移</span><span class="sxs-lookup"><span data-stu-id="2ad3d-137">Apply migrations</span></span>

<span data-ttu-id="2ad3d-138">将迁移应用到数据库初始化。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-138">Apply the migrations to initialise the database.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2ad3d-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2ad3d-139">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2ad3d-140">在包管理器控制台中运行以下命令（PMC）：</span><span class="sxs-lookup"><span data-stu-id="2ad3d-140">Run the following command in the Package Manager Console (PMC):</span></span>

```PM> Update-Database```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2ad3d-141">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2ad3d-141">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet ef database update
```

---

### <a name="test-register-and-login"></a><span data-ttu-id="2ad3d-142">测试注册和登录</span><span class="sxs-lookup"><span data-stu-id="2ad3d-142">Test Register and Login</span></span>

<span data-ttu-id="2ad3d-143">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-143">Run the app and register a user.</span></span> <span data-ttu-id="2ad3d-144">根据屏幕大小，你可能需要选择 "导航" 切换按钮以查看 "**寄存器**" 和 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-144">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>

### <a name="configure-identity-services"></a><span data-ttu-id="2ad3d-145">配置标识服务</span><span class="sxs-lookup"><span data-stu-id="2ad3d-145">Configure Identity services</span></span>

<span data-ttu-id="2ad3d-146">中`ConfigureServices`添加了服务。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-146">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="2ad3d-147">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-147">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="2ad3d-148">前面的代码用默认选项值配置标识。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-148">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="2ad3d-149">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-149">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="2ad3d-150">通过调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)来启用标识。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-150">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="2ad3d-151">`UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-151">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   <span data-ttu-id="2ad3d-152">服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-152">Services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="2ad3d-153">`UseAuthentication` 通过`Configure`在方法中调用来为应用程序启用标识。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-153">Identity is enabled for the application by calling `UseAuthentication` in the `Configure` method.</span></span> <span data-ttu-id="2ad3d-154">`UseAuthentication`将身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-154">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   <span data-ttu-id="2ad3d-155">这些服务通过[依赖关系注入](xref:fundamentals/dependency-injection)提供给应用程序。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-155">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="2ad3d-156">`UseIdentity` 通过`Configure`在方法中调用来为应用程序启用标识。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-156">Identity is enabled for the application by calling `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="2ad3d-157">`UseIdentity`将基于 cookie 的身份验证[中间件](xref:fundamentals/middleware/index)添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-157">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

<span data-ttu-id="2ad3d-158">有关详细信息，请参阅[IdentityOptions 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)和[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-158">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="2ad3d-159">基架注册、登录和注销</span><span class="sxs-lookup"><span data-stu-id="2ad3d-159">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="2ad3d-160">按照[基架标识操作，并使用授权](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)说明生成本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-160">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2ad3d-161">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2ad3d-161">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2ad3d-162">添加注册、登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-162">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2ad3d-163">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2ad3d-163">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="2ad3d-164">如果创建的项目的名称为**WebApp1**，请运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-164">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="2ad3d-165">否则，请使用正确的命名空间`ApplicationDbContext`：</span><span class="sxs-lookup"><span data-stu-id="2ad3d-165">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="2ad3d-166">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-166">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="2ad3d-167">使用 PowerShell 时，请对文件列表中的分号进行转义，或将文件列表置于双引号中，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-167">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="2ad3d-168">检查注册</span><span class="sxs-lookup"><span data-stu-id="2ad3d-168">Examine Register</span></span>

::: moniker range=">= aspnetcore-2.1"

   <span data-ttu-id="2ad3d-169">当用户单击 "**注册**" 链接时， `RegisterModel.OnPostAsync`将调用该操作。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-169">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="2ad3d-170">用户是通过[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)对`_userManager`对象创建的。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-170">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="2ad3d-171">`_userManager`由依赖关系注入提供：</span><span class="sxs-lookup"><span data-stu-id="2ad3d-171">`_userManager` is provided by dependency injection):</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="2ad3d-172">当用户单击 "**注册**" 链接时， `Register`将在上`AccountController`调用该操作。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-172">When a user clicks the **Register** link, the `Register` action is invoked on `AccountController`.</span></span> <span data-ttu-id="2ad3d-173">操作通过对`CreateAsync` `AccountController`对象调用来创建用户（由依赖项注入提供）： `_userManager` `Register`</span><span class="sxs-lookup"><span data-stu-id="2ad3d-173">The `Register` action creates the user by calling `CreateAsync` on the `_userManager` object (provided to `AccountController` by dependency injection):</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   <span data-ttu-id="2ad3d-174">如果用户已成功创建，则对的调用`_signInManager.SignInAsync`会登录该用户。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-174">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

   <span data-ttu-id="2ad3d-175">**注意：** 请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)以了解在注册时要阻止立即登录的步骤。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-175">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="2ad3d-176">登录</span><span class="sxs-lookup"><span data-stu-id="2ad3d-176">Log in</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2ad3d-177">发生下列情况时，会显示登录窗体：</span><span class="sxs-lookup"><span data-stu-id="2ad3d-177">The Login form is displayed when:</span></span>

* <span data-ttu-id="2ad3d-178">选择 "**登录**" 链接。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-178">The **Log in** link is selected.</span></span>
* <span data-ttu-id="2ad3d-179">用户在无权访问的情况下或未经系统进行身份验证的情况下尝试访问受限页面。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-179">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="2ad3d-180">提交“登录”页上的表单时，会调用 `OnPostAsync` 操作。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-180">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="2ad3d-181">会在 `_signInManager` 对象（通过注入依赖项的方式提供）上调用 `PasswordSignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-181">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   <span data-ttu-id="2ad3d-182">基 `Controller` 类公开了一个 `User` 属性，方便你通过控制器方法对其进行访问。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-182">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="2ad3d-183">例如，可以枚举 `User.Claims` 并进行授权决策。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-183">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="2ad3d-184">有关详细信息，请参阅 <xref:security/authorization/introduction>。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-184">For more information, see <xref:security/authorization/introduction>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="2ad3d-185">当用户选择 "**登录**" 链接或访问需要身份验证的页面时，将显示登录窗体。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-185">The Login form is displayed when users select the **Log in** link or are redirected when accessing a page that requires authentication.</span></span> <span data-ttu-id="2ad3d-186">当用户在登录页上提交窗体时，将`AccountController`调用该`Login`操作。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-186">When the user submits the form on the Login page, the `AccountController` `Login` action is called.</span></span>

<span data-ttu-id="2ad3d-187">操作对对象`_signInManager`调用`AccountController` （由依赖关系注入提供）。 `PasswordSignInAsync` `Login`</span><span class="sxs-lookup"><span data-stu-id="2ad3d-187">The `Login` action calls `PasswordSignInAsync` on the `_signInManager` object (provided to `AccountController` by dependency injection).</span></span>

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

<span data-ttu-id="2ad3d-188">基（`Controller`或`PageModel`）类公开了一个`User`属性。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-188">The base (`Controller` or `PageModel`) class exposes a `User` property.</span></span> <span data-ttu-id="2ad3d-189">例如， `User.Claims`可以枚举来做出授权决策。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-189">For example, `User.Claims` can be enumerated to make authorization decisions.</span></span>

::: moniker-end

### <a name="log-out"></a><span data-ttu-id="2ad3d-190">注销</span><span class="sxs-lookup"><span data-stu-id="2ad3d-190">Log out</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2ad3d-191">"**注销**" 链接将调用`LogoutModel.OnPost`该操作。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-191">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="2ad3d-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-192">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span>

<span data-ttu-id="2ad3d-193">在 *Pages/Shared/_LoginPartial.cshtml* 中指定 post：</span><span class="sxs-lookup"><span data-stu-id="2ad3d-193">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="2ad3d-194">单击 "**注销**" 链接将调用`LogOut`该操作。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-194">Clicking the **Log out** link calls the `LogOut` action.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   <span data-ttu-id="2ad3d-195">前面的代码调用`_signInManager.SignOutAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-195">The preceding code calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="2ad3d-196">`SignOutAsync`方法会清除 cookie 中存储的用户声明。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-196">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>

::: moniker-end

## <a name="test-identity"></a><span data-ttu-id="2ad3d-197">测试标识</span><span class="sxs-lookup"><span data-stu-id="2ad3d-197">Test Identity</span></span>

<span data-ttu-id="2ad3d-198">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-198">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="2ad3d-199">若要测试标识， [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)请将添加到 "隐私" 页。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-199">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

<span data-ttu-id="2ad3d-200">如果已登录，请注销。运行应用并选择 "**隐私**" 链接。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-200">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="2ad3d-201">你将重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-201">You are redirected to the login page.</span></span>

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a><span data-ttu-id="2ad3d-202">浏览标识</span><span class="sxs-lookup"><span data-stu-id="2ad3d-202">Explore Identity</span></span>

<span data-ttu-id="2ad3d-203">更详细地了解标识：</span><span class="sxs-lookup"><span data-stu-id="2ad3d-203">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="2ad3d-204">创建完全标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="2ad3d-204">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="2ad3d-205">检查每个页面的源，并单步执行调试程序。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-205">Examine the source of each page and step through the debugger.</span></span>

::: moniker-end

## <a name="identity-components"></a><span data-ttu-id="2ad3d-206">标识组件</span><span class="sxs-lookup"><span data-stu-id="2ad3d-206">Identity Components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2ad3d-207">所有标识相关 NuGet 包都包含在[AspNetCore 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-207">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="2ad3d-208">标识的主包为[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-208">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="2ad3d-209">此包包含 ASP.NET Core 标识的核心接口集，是 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 提供的。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-209">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="2ad3d-210">迁移到 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="2ad3d-210">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="2ad3d-211">有关迁移现有标识存储的详细信息和指南，请参阅[迁移身份验证和标识](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-211">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="2ad3d-212">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="2ad3d-212">Setting password strength</span></span>

<span data-ttu-id="2ad3d-213">有关设置最小密码要求的示例，请参阅[配置](#pw)。</span><span class="sxs-lookup"><span data-stu-id="2ad3d-213">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2ad3d-214">后续步骤</span><span class="sxs-lookup"><span data-stu-id="2ad3d-214">Next Steps</span></span>

* [<span data-ttu-id="2ad3d-215">配置标识</span><span class="sxs-lookup"><span data-stu-id="2ad3d-215">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
