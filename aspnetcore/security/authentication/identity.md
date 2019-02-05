---
title: ASP.NET Core 上的标识简介
author: rick-anderson
description: 将标识与 ASP.NET Core 应用配合使用。 了解如何设置密码要求 （RequireDigit、 RequiredLength、 RequiredUniqueChars，和的详细信息）。
ms.author: riande
ms.date: 08/08/2018
uid: security/authentication/identity
ms.openlocfilehash: 1a4e7fb3ac6a767ca17127dd58a9b9e65ed9a00b
ms.sourcegitcommit: e418cb9cddeb3de06fa0cb4fdb5529da03ff6d63
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2019
ms.locfileid: "55739678"
---
# <a name="introduction-to-identity-on-aspnet-core"></a><span data-ttu-id="f36c3-104">ASP.NET Core 上的标识简介</span><span class="sxs-lookup"><span data-stu-id="f36c3-104">Introduction to Identity on ASP.NET Core</span></span>

<span data-ttu-id="f36c3-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="f36c3-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="f36c3-106">ASP.NET Core 标识是一个成员身份系统，将登录功能添加到 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="f36c3-106">ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps.</span></span> <span data-ttu-id="f36c3-107">用户可以标识中存储的登录信息创建一个帐户，或它们可以使用外部登录提供程序。</span><span class="sxs-lookup"><span data-stu-id="f36c3-107">Users can create an account with the login information stored in Identity or they can use an external login provider.</span></span> <span data-ttu-id="f36c3-108">受支持的外部登录提供程序包括[Facebook、 Google、 Microsoft 帐户和 Twitter](xref:security/authentication/social/index)。</span><span class="sxs-lookup"><span data-stu-id="f36c3-108">Supported external login providers include [Facebook, Google, Microsoft Account, and Twitter](xref:security/authentication/social/index).</span></span>

<span data-ttu-id="f36c3-109">可以使用 SQL Server 数据库来存储用户名、 密码和配置文件数据配置标识。</span><span class="sxs-lookup"><span data-stu-id="f36c3-109">Identity can be configured using a SQL Server database to store user names, passwords, and profile data.</span></span> <span data-ttu-id="f36c3-110">或者，另一个的持久存储区可用，例如，Azure 表存储。</span><span class="sxs-lookup"><span data-stu-id="f36c3-110">Alternatively, another persistent store can be used, for example, Azure Table Storage.</span></span>

<span data-ttu-id="f36c3-111">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/)([如何下载)](xref:index#how-to-download-a-sample))。</span><span class="sxs-lookup"><span data-stu-id="f36c3-111">[View or download the sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample/src/ASPNETCore-IdentityDemoComplete/) ([how to download)](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="f36c3-112">在本主题中，将了解如何使用标识来注册、 登录和注销用户。</span><span class="sxs-lookup"><span data-stu-id="f36c3-112">In this topic, you learn how to use Identity to register, log in, and log out a user.</span></span> <span data-ttu-id="f36c3-113">有关创建使用标识的应用程序的更多详细说明，请参阅本文末尾的后续步骤部分。</span><span class="sxs-lookup"><span data-stu-id="f36c3-113">For more detailed instructions about creating apps that use Identity, see the Next Steps section at the end of this article.</span></span>

::: moniker range=">= aspnetcore-2.1"

<a name="adi"></a>
## <a name="adddefaultidentity-and-addidentity"></a><span data-ttu-id="f36c3-114">AddDefaultIdentity 和 AddIdentity</span><span class="sxs-lookup"><span data-stu-id="f36c3-114">AddDefaultIdentity and AddIdentity</span></span>

<span data-ttu-id="f36c3-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) ASP.NET Core 2.1 中引入了。</span><span class="sxs-lookup"><span data-stu-id="f36c3-115">[AddDefaultIdentity](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionuiextensions.adddefaultidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionUIExtensions_AddDefaultIdentity__1_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__) was introduced in ASP.NET Core 2.1.</span></span> <span data-ttu-id="f36c3-116">调用`AddDefaultIdentity`类似于调用以下：</span><span class="sxs-lookup"><span data-stu-id="f36c3-116">Calling `AddDefaultIdentity` is similar to calling the following:</span></span>

* [<span data-ttu-id="f36c3-117">AddIdentity</span><span class="sxs-lookup"><span data-stu-id="f36c3-117">AddIdentity</span></span>](/dotnet/api/microsoft.extensions.dependencyinjection.identityservicecollectionextensions.addidentity?view=aspnetcore-2.1#Microsoft_Extensions_DependencyInjection_IdentityServiceCollectionExtensions_AddIdentity__2_Microsoft_Extensions_DependencyInjection_IServiceCollection_System_Action_Microsoft_AspNetCore_Identity_IdentityOptions__)
* [<span data-ttu-id="f36c3-118">AddDefaultUI</span><span class="sxs-lookup"><span data-stu-id="f36c3-118">AddDefaultUI</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderuiextensions.adddefaultui?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderUIExtensions_AddDefaultUI_Microsoft_AspNetCore_Identity_IdentityBuilder_)
* [<span data-ttu-id="f36c3-119">AddDefaultTokenProviders</span><span class="sxs-lookup"><span data-stu-id="f36c3-119">AddDefaultTokenProviders</span></span>](/dotnet/api/microsoft.aspnetcore.identity.identitybuilderextensions.adddefaulttokenproviders?view=aspnetcore-2.1#Microsoft_AspNetCore_Identity_IdentityBuilderExtensions_AddDefaultTokenProviders_Microsoft_AspNetCore_Identity_IdentityBuilder_)

<span data-ttu-id="f36c3-120">请参阅[AddDefaultIdentity 源](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="f36c3-120">See [AddDefaultIdentity source](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Identity/UI/src/IdentityServiceCollectionUIExtensions.cs#L47-L63) for more information.</span></span>

::: moniker-end

## <a name="create-a-web-app-with-authentication"></a><span data-ttu-id="f36c3-121">使用身份验证创建的 Web 应用</span><span class="sxs-lookup"><span data-stu-id="f36c3-121">Create a Web app with authentication</span></span>

<span data-ttu-id="f36c3-122">使用单个用户帐户创建一个 ASP.NET Core Web 应用程序项目。</span><span class="sxs-lookup"><span data-stu-id="f36c3-122">Create an ASP.NET Core Web Application project with Individual User Accounts.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f36c3-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f36c3-123">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="f36c3-124">选择“文件” > “新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="f36c3-124">Select **File** > **New** > **Project**.</span></span>
* <span data-ttu-id="f36c3-125">选择“ASP.NET Core Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="f36c3-125">Select **ASP.NET Core Web Application**.</span></span> <span data-ttu-id="f36c3-126">将项目命名**WebApp1**具有项目下载相同的命名空间。</span><span class="sxs-lookup"><span data-stu-id="f36c3-126">Name the project **WebApp1** to have the same namespace as the project download.</span></span> <span data-ttu-id="f36c3-127">单击 **“确定”**。</span><span class="sxs-lookup"><span data-stu-id="f36c3-127">Click **OK**.</span></span>
* <span data-ttu-id="f36c3-128">选择 ASP.NET Core **Web 应用程序**，然后选择**更改身份验证**。</span><span class="sxs-lookup"><span data-stu-id="f36c3-128">Select an ASP.NET Core **Web Application**, then select **Change Authentication**.</span></span>
* <span data-ttu-id="f36c3-129">选择**单个用户帐户**然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="f36c3-129">Select **Individual User Accounts** and click **OK**.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f36c3-130">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f36c3-130">.NET Core CLI</span></span>](#tab/netcore-cli)

```cli
dotnet new webapp --auth Individual -o WebApp1
```

---

<span data-ttu-id="f36c3-131">生成的项目提供了[ASP.NET Core 标识](xref:security/authentication/identity)作为[Razor 类库](xref:razor-pages/ui-class)。</span><span class="sxs-lookup"><span data-stu-id="f36c3-131">The generated project provides [ASP.NET Core Identity](xref:security/authentication/identity) as a [Razor Class Library](xref:razor-pages/ui-class).</span></span> <span data-ttu-id="f36c3-132">标识 Razor 类库公开终结点与`Identity`区域。</span><span class="sxs-lookup"><span data-stu-id="f36c3-132">The Identity Razor Class Library exposes endpoints with the `Identity` area.</span></span> <span data-ttu-id="f36c3-133">例如：</span><span class="sxs-lookup"><span data-stu-id="f36c3-133">For example:</span></span>

* <span data-ttu-id="f36c3-134">/ 标识/Account/Login</span><span class="sxs-lookup"><span data-stu-id="f36c3-134">/Identity/Account/Login</span></span>
* <span data-ttu-id="f36c3-135">/ 标识/帐户/注销</span><span class="sxs-lookup"><span data-stu-id="f36c3-135">/Identity/Account/Logout</span></span>
* <span data-ttu-id="f36c3-136">/ Identity/帐户/管理</span><span class="sxs-lookup"><span data-stu-id="f36c3-136">/Identity/Account/Manage</span></span>

### <a name="test-register-and-login"></a><span data-ttu-id="f36c3-137">测试注册和登录名</span><span class="sxs-lookup"><span data-stu-id="f36c3-137">Test Register and Login</span></span>

<span data-ttu-id="f36c3-138">运行应用并注册用户。</span><span class="sxs-lookup"><span data-stu-id="f36c3-138">Run the app and register a user.</span></span> <span data-ttu-id="f36c3-139">具体取决于你的屏幕大小，可能需要选择导航切换按钮以查看**注册**并**登录名**链接。</span><span class="sxs-lookup"><span data-stu-id="f36c3-139">Depending on your screen size, you might need to select the navigation toggle button to see the **Register** and **Login** links.</span></span>

[!INCLUDE[](~/includes/view-identity-db.md)]

<a name="pw"></a>
### <a name="configure-identity-services"></a><span data-ttu-id="f36c3-140">配置标识服务</span><span class="sxs-lookup"><span data-stu-id="f36c3-140">Configure Identity services</span></span>

<span data-ttu-id="f36c3-141">在添加服务`ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="f36c3-141">Services are added in `ConfigureServices`.</span></span> <span data-ttu-id="f36c3-142">典型模式是调用所有 `Add{Service}` 方法，然后调用所有 `services.Configure{Service}` 方法。</span><span class="sxs-lookup"><span data-stu-id="f36c3-142">The typical pattern is to call all the `Add{Service}` methods, and then call all the `services.Configure{Service}` methods.</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configureservices)]

<span data-ttu-id="f36c3-143">上述代码使用默认的选项值配置标识。</span><span class="sxs-lookup"><span data-stu-id="f36c3-143">The preceding code configures Identity with default option values.</span></span> <span data-ttu-id="f36c3-144">服务可用于通过应用[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="f36c3-144">Services are made available to the app through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="f36c3-145">标识启用通过调用[UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_)。</span><span class="sxs-lookup"><span data-stu-id="f36c3-145">Identity is enabled by calling [UseAuthentication](/dotnet/api/microsoft.aspnetcore.builder.authappbuilderextensions.useauthentication#Microsoft_AspNetCore_Builder_AuthAppBuilderExtensions_UseAuthentication_Microsoft_AspNetCore_Builder_IApplicationBuilder_).</span></span> <span data-ttu-id="f36c3-146">`UseAuthentication` 添加了身份验证[中间件](xref:fundamentals/middleware/index)到请求管道。</span><span class="sxs-lookup"><span data-stu-id="f36c3-146">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/WebApp1/Startup.cs?name=snippet_configure&highlight=18)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,11-28,30-42)]

   <span data-ttu-id="f36c3-147">服务都提供给应用程序通过[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="f36c3-147">Services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="f36c3-148">通过调用情况下，启用应用程序的身份标识`UseAuthentication`在`Configure`方法。</span><span class="sxs-lookup"><span data-stu-id="f36c3-148">Identity is enabled for the application by calling `UseAuthentication` in the `Configure` method.</span></span> <span data-ttu-id="f36c3-149">`UseAuthentication` 添加了身份验证[中间件](xref:fundamentals/middleware/index)到请求管道。</span><span class="sxs-lookup"><span data-stu-id="f36c3-149">`UseAuthentication` adds authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNETv2-IdentityDemo/Startup.cs?name=snippet_configure&highlight=17)]

::: moniker-end

::: moniker range="= aspnetcore-1.1"

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configureservices&highlight=7-9,13-33)]

   <span data-ttu-id="f36c3-150">这些服务都提供给应用程序通过[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="f36c3-150">These services are made available to the application through [dependency injection](xref:fundamentals/dependency-injection).</span></span>

   <span data-ttu-id="f36c3-151">通过调用情况下，启用应用程序的身份标识`UseIdentity`在`Configure`方法。</span><span class="sxs-lookup"><span data-stu-id="f36c3-151">Identity is enabled for the application by calling `UseIdentity` in the `Configure` method.</span></span> <span data-ttu-id="f36c3-152">`UseIdentity` 添加了基于 cookie 的身份验证[中间件](xref:fundamentals/middleware/index)到请求管道。</span><span class="sxs-lookup"><span data-stu-id="f36c3-152">`UseIdentity` adds cookie-based authentication [middleware](xref:fundamentals/middleware/index) to the request pipeline.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Startup.cs?name=snippet_configure&highlight=21)]

::: moniker-end

<span data-ttu-id="f36c3-153">有关详细信息，请参阅[IdentityOptions 类](/dotnet/api/microsoft.aspnetcore.identity.identityoptions)并[应用程序启动](xref:fundamentals/startup)。</span><span class="sxs-lookup"><span data-stu-id="f36c3-153">For more information, see the [IdentityOptions Class](/dotnet/api/microsoft.aspnetcore.identity.identityoptions) and [Application Startup](xref:fundamentals/startup).</span></span>

## <a name="scaffold-register-login-and-logout"></a><span data-ttu-id="f36c3-154">基架注册、 登录和注销</span><span class="sxs-lookup"><span data-stu-id="f36c3-154">Scaffold Register, Login, and LogOut</span></span>

<span data-ttu-id="f36c3-155">请按照[创建标识为具有授权的 Razor 项目基架](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization)如何生成在本部分中所示的代码。</span><span class="sxs-lookup"><span data-stu-id="f36c3-155">Follow the [Scaffold identity into a Razor project with authorization](xref:security/authentication/scaffold-identity#scaffold-identity-into-a-razor-project-with-authorization) instructions to generate the the code shown in this section.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="f36c3-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="f36c3-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="f36c3-157">添加注册、 登录和注销文件。</span><span class="sxs-lookup"><span data-stu-id="f36c3-157">Add the Register, Login, and LogOut files.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="f36c3-158">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="f36c3-158">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="f36c3-159">如果使用名称创建项目**WebApp1**，运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="f36c3-159">If you created the project with name **WebApp1**, run the following commands.</span></span> <span data-ttu-id="f36c3-160">否则，请使用正确的命名空间为`ApplicationDbContext`:</span><span class="sxs-lookup"><span data-stu-id="f36c3-160">Otherwise, use the correct namespace for the `ApplicationDbContext`:</span></span>

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet aspnet-codegenerator identity -dc WebApp1.Data.ApplicationDbContext --files "Account.Register;Account.Login;Account.Logout"
```

<span data-ttu-id="f36c3-161">PowerShell 使用分号作为命令分隔符。</span><span class="sxs-lookup"><span data-stu-id="f36c3-161">PowerShell uses semicolon as a command separator.</span></span> <span data-ttu-id="f36c3-162">使用 PowerShell 时，转义分号的文件列表中，或将文件列表放在双引号内，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="f36c3-162">When using PowerShell, escape the semicolons in the file list or put the file list in double quotes, as the preceding example shows.</span></span>

---

### <a name="examine-register"></a><span data-ttu-id="f36c3-163">检查注册</span><span class="sxs-lookup"><span data-stu-id="f36c3-163">Examine Register</span></span>

::: moniker range=">= aspnetcore-2.1"

   <span data-ttu-id="f36c3-164">当用户单击**注册**链接，`RegisterModel.OnPostAsync`调用操作。</span><span class="sxs-lookup"><span data-stu-id="f36c3-164">When a user clicks the **Register** link, the `RegisterModel.OnPostAsync` action is invoked.</span></span> <span data-ttu-id="f36c3-165">通过创建用户[CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_)上`_userManager`对象。</span><span class="sxs-lookup"><span data-stu-id="f36c3-165">The user is created by [CreateAsync](/dotnet/api/microsoft.aspnetcore.identity.usermanager-1.createasync#Microsoft_AspNetCore_Identity_UserManager_1_CreateAsync__0_System_String_) on the `_userManager` object.</span></span> <span data-ttu-id="f36c3-166">`_userManager` 提供依赖关系注入）：</span><span class="sxs-lookup"><span data-stu-id="f36c3-166">`_userManager` is provided by dependency injection):</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=7,22)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="f36c3-167">当用户单击**注册**链接，链接`Register`上调用操作`AccountController`。</span><span class="sxs-lookup"><span data-stu-id="f36c3-167">When a user clicks the **Register** link, the `Register` action is invoked on `AccountController`.</span></span> <span data-ttu-id="f36c3-168">`Register`操作将创建用户，通过调用`CreateAsync`上`_userManager`对象 (提供给`AccountController`通过依赖关系注入):</span><span class="sxs-lookup"><span data-stu-id="f36c3-168">The `Register` action creates the user by calling `CreateAsync` on the `_userManager` object (provided to `AccountController` by dependency injection):</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_register&highlight=11)]

::: moniker-end

   <span data-ttu-id="f36c3-169">如果已成功创建用户，用户记录通过调用`_signInManager.SignInAsync`。</span><span class="sxs-lookup"><span data-stu-id="f36c3-169">If the user was created successfully, the user is logged in by the call to `_signInManager.SignInAsync`.</span></span>

   <span data-ttu-id="f36c3-170">**注意：** 请参阅[帐户确认](xref:security/authentication/accconfirm#prevent-login-at-registration)有关步骤，以防止在登记时立即登录。</span><span class="sxs-lookup"><span data-stu-id="f36c3-170">**Note:** See [account confirmation](xref:security/authentication/accconfirm#prevent-login-at-registration) for steps to prevent immediate login at registration.</span></span>

### <a name="log-in"></a><span data-ttu-id="f36c3-171">登录</span><span class="sxs-lookup"><span data-stu-id="f36c3-171">Log in</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f36c3-172">显示登录窗体时：</span><span class="sxs-lookup"><span data-stu-id="f36c3-172">The Login form is displayed when:</span></span>

* <span data-ttu-id="f36c3-173">**登录**选择链接。</span><span class="sxs-lookup"><span data-stu-id="f36c3-173">The **Log in** link is selected.</span></span>
* <span data-ttu-id="f36c3-174">用户尝试访问受限的页面，他们不向被授权访问**或**时在还没有已完成身份验证系统。</span><span class="sxs-lookup"><span data-stu-id="f36c3-174">A user attempts to access a restricted page that they aren't authorized to access **or** when they haven't been authenticated by the system.</span></span>

<span data-ttu-id="f36c3-175">登录页上的表单提交时，`OnPostAsync`调用操作。</span><span class="sxs-lookup"><span data-stu-id="f36c3-175">When the form on the Login page is submitted, the `OnPostAsync` action is called.</span></span> <span data-ttu-id="f36c3-176">`PasswordSignInAsync` 对调用`_signInManager`对象 （由依赖关系注入提供）。</span><span class="sxs-lookup"><span data-stu-id="f36c3-176">`PasswordSignInAsync` is called on the `_signInManager` object (provided by dependency injection).</span></span>

   [!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Login.cshtml.cs?name=snippet&highlight=10-11)]

   <span data-ttu-id="f36c3-177">基`Controller`类公开`User`，可以从控制器方法访问的属性。</span><span class="sxs-lookup"><span data-stu-id="f36c3-177">The base `Controller` class exposes a `User` property that you can access from controller methods.</span></span> <span data-ttu-id="f36c3-178">例如，可以枚举`User.Claims`和做出授权决定。</span><span class="sxs-lookup"><span data-stu-id="f36c3-178">For instance, you can enumerate `User.Claims` and make authorization decisions.</span></span> <span data-ttu-id="f36c3-179">有关详细信息，请参阅 <xref:security/authorization/introduction>。</span><span class="sxs-lookup"><span data-stu-id="f36c3-179">For more information, see <xref:security/authorization/introduction>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="f36c3-180">当用户选择时，将显示登录窗体**登录**访问要求进行身份验证的页面时将被重定向或链接。</span><span class="sxs-lookup"><span data-stu-id="f36c3-180">The Login form is displayed when users select the **Log in** link or are redirected when accessing a page that requires authentication.</span></span> <span data-ttu-id="f36c3-181">当用户提交窗体上的登录页中， `AccountController` `Login`调用操作。</span><span class="sxs-lookup"><span data-stu-id="f36c3-181">When the user submits the form on the Login page, the `AccountController` `Login` action is called.</span></span>

<span data-ttu-id="f36c3-182">`Login`操作调用`PasswordSignInAsync`上`_signInManager`对象 (提供给`AccountController`通过依赖关系注入)。</span><span class="sxs-lookup"><span data-stu-id="f36c3-182">The `Login` action calls `PasswordSignInAsync` on the `_signInManager` object (provided to `AccountController` by dependency injection).</span></span>

[!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_login&highlight=13-14)]

<span data-ttu-id="f36c3-183">基本 (`Controller`或`PageModel`) 类公开`User`属性。</span><span class="sxs-lookup"><span data-stu-id="f36c3-183">The base (`Controller` or `PageModel`) class exposes a `User` property.</span></span> <span data-ttu-id="f36c3-184">例如，`User.Claims`可以枚举才能做出授权决定。</span><span class="sxs-lookup"><span data-stu-id="f36c3-184">For example, `User.Claims` can be enumerated to make authorization decisions.</span></span>

::: moniker-end

### <a name="log-out"></a><span data-ttu-id="f36c3-185">注销</span><span class="sxs-lookup"><span data-stu-id="f36c3-185">Log out</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f36c3-186">**注销**链接调用`LogoutModel.OnPost`操作。</span><span class="sxs-lookup"><span data-stu-id="f36c3-186">The **Log out** link invokes the `LogoutModel.OnPost` action.</span></span> 

[!code-csharp[](identity/sample/WebApp1/Areas/Identity/Pages/Account/Logout.cshtml.cs)]

<span data-ttu-id="f36c3-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync)清除存储在 cookie 中的用户的声明。</span><span class="sxs-lookup"><span data-stu-id="f36c3-187">[SignOutAsync](/dotnet/api/microsoft.aspnetcore.identity.signinmanager-1.signoutasync#Microsoft_AspNetCore_Identity_SignInManager_1_SignOutAsync) clears the user's claims stored in a cookie.</span></span> <span data-ttu-id="f36c3-188">在调用后不重定向`SignOutAsync`或者在用户会**不**已注销。</span><span class="sxs-lookup"><span data-stu-id="f36c3-188">Don't redirect after calling `SignOutAsync` or the user will **not** be signed out.</span></span>

<span data-ttu-id="f36c3-189">中指定 post *Pages/Shared/_LoginPartial.cshtml*:</span><span class="sxs-lookup"><span data-stu-id="f36c3-189">Post is specified in the *Pages/Shared/_LoginPartial.cshtml*:</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Shared/_LoginPartial.cshtml?highlight=16)]

::: moniker-end

::: moniker range="= aspnetcore-2.0"

   <span data-ttu-id="f36c3-190">单击**注销**链接调用`LogOut`操作。</span><span class="sxs-lookup"><span data-stu-id="f36c3-190">Clicking the **Log out** link calls the `LogOut` action.</span></span>

   [!code-csharp[](identity/sample/src/ASPNET-IdentityDemo/Controllers/AccountController.cs?name=snippet_logout&highlight=7)]

   <span data-ttu-id="f36c3-191">上述代码调用`_signInManager.SignOutAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="f36c3-191">The preceding code calls the `_signInManager.SignOutAsync` method.</span></span> <span data-ttu-id="f36c3-192">`SignOutAsync`方法将清除用户的声明存储在 cookie 中。</span><span class="sxs-lookup"><span data-stu-id="f36c3-192">The `SignOutAsync` method clears the user's claims stored in a cookie.</span></span>

::: moniker-end

## <a name="test-identity"></a><span data-ttu-id="f36c3-193">测试标识</span><span class="sxs-lookup"><span data-stu-id="f36c3-193">Test Identity</span></span>

<span data-ttu-id="f36c3-194">默认 web 项目模板允许匿名访问主页。</span><span class="sxs-lookup"><span data-stu-id="f36c3-194">The default web project templates allow anonymous access to the home pages.</span></span> <span data-ttu-id="f36c3-195">若要测试标识，将添加[ `[Authorize]` ](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute)到隐私页。</span><span class="sxs-lookup"><span data-stu-id="f36c3-195">To test Identity, add [`[Authorize]`](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute) to the Privacy page.</span></span>

[!code-csharp[](identity/sample/WebApp1/Pages/Privacy.cshtml.cs?highlight=6)]

<span data-ttu-id="f36c3-196">如果你登录，请注销。运行应用并选择**隐私**链接。</span><span class="sxs-lookup"><span data-stu-id="f36c3-196">If you are signed in, sign out. Run the app and select the **Privacy** link.</span></span> <span data-ttu-id="f36c3-197">你将重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="f36c3-197">You are redirected to the login page.</span></span>

::: moniker range=">= aspnetcore-2.1"

### <a name="explore-identity"></a><span data-ttu-id="f36c3-198">了解标识</span><span class="sxs-lookup"><span data-stu-id="f36c3-198">Explore Identity</span></span>

<span data-ttu-id="f36c3-199">若要更详细地了解标识：</span><span class="sxs-lookup"><span data-stu-id="f36c3-199">To explore Identity in more detail:</span></span>

* [<span data-ttu-id="f36c3-200">创建完整的标识 UI 源</span><span class="sxs-lookup"><span data-stu-id="f36c3-200">Create full identity UI source</span></span>](xref:security/authentication/scaffold-identity#create-full-identity-ui-source)
* <span data-ttu-id="f36c3-201">检查每个页面和单步执行调试器的源。</span><span class="sxs-lookup"><span data-stu-id="f36c3-201">Examine the source of each page and step through the debugger.</span></span>

::: moniker-end

## <a name="identity-components"></a><span data-ttu-id="f36c3-202">标识组件</span><span class="sxs-lookup"><span data-stu-id="f36c3-202">Identity Components</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="f36c3-203">中包含所有标识依赖 NuGet 包[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="f36c3-203">All the Identity dependent NuGet packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="f36c3-204">标识将主包是[Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/)。</span><span class="sxs-lookup"><span data-stu-id="f36c3-204">The primary package for Identity is [Microsoft.AspNetCore.Identity](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity/).</span></span> <span data-ttu-id="f36c3-205">此包包含 ASP.NET Core 标识的核心接口集，是 `Microsoft.AspNetCore.Identity.EntityFrameworkCore` 提供的。</span><span class="sxs-lookup"><span data-stu-id="f36c3-205">This package contains the core set of interfaces for ASP.NET Core Identity, and is included by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.</span></span>

## <a name="migrating-to-aspnet-core-identity"></a><span data-ttu-id="f36c3-206">迁移到 ASP.NET Core 标识</span><span class="sxs-lookup"><span data-stu-id="f36c3-206">Migrating to ASP.NET Core Identity</span></span>

<span data-ttu-id="f36c3-207">有关详细信息和指南迁移现有的标识存储，请参阅[迁移身份验证和标识](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="f36c3-207">For more information and guidance on migrating your existing Identity store, see [Migrate Authentication and Identity](xref:migration/identity).</span></span>

## <a name="setting-password-strength"></a><span data-ttu-id="f36c3-208">设置密码强度</span><span class="sxs-lookup"><span data-stu-id="f36c3-208">Setting password strength</span></span>

<span data-ttu-id="f36c3-209">请参阅[配置](#pw)有关设置的最小密码要求的示例。</span><span class="sxs-lookup"><span data-stu-id="f36c3-209">See [Configuration](#pw) for a sample that sets the minimum password requirements.</span></span>

## <a name="next-steps"></a><span data-ttu-id="f36c3-210">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f36c3-210">Next Steps</span></span>

* [<span data-ttu-id="f36c3-211">配置标识</span><span class="sxs-lookup"><span data-stu-id="f36c3-211">Configure Identity</span></span>](xref:security/authentication/identity-configuration)
* <xref:security/authorization/secure-data>
* <xref:security/authentication/add-user-data>
* <xref:security/authentication/identity-enable-qrcodes>
* <xref:migration/identity>
* <xref:security/authentication/accconfirm>
* <xref:security/authentication/2fa>
* <xref:host-and-deploy/web-farm>
