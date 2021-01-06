---
title: 使用授权保护的用户数据创建 ASP.NET Core 应用
author: rick-anderson
description: 了解如何使用授权保护的用户数据创建 ASP.NET Core web 应用。 包括 HTTPS、身份验证、安全性 ASP.NET Core Identity 。
ms.author: riande
ms.date: 7/18/2020
ms.custom: mvc, seodec18
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
uid: security/authorization/secure-data
ms.openlocfilehash: dc70cfe7cb0c0f044f5f1e7ee68a293b3ea7507f
ms.sourcegitcommit: 04a404a9655c59ad1ea02aff5d399ae1b833ad6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2021
ms.locfileid: "97854647"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="40de9-104">使用受授权保护的用户数据创建 ASP.NET Core web 应用</span><span class="sxs-lookup"><span data-stu-id="40de9-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="40de9-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="40de9-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="40de9-106">查看 [此 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="40de9-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="40de9-107">本教程演示如何创建 ASP.NET Core 的 web 应用，其中包含由授权保护的用户数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="40de9-108">它将显示已进行身份验证 (已创建的已注册) 用户的联系人列表。</span><span class="sxs-lookup"><span data-stu-id="40de9-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="40de9-109">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="40de9-109">There are three security groups:</span></span>

* <span data-ttu-id="40de9-110">**已注册的用户** 可以查看所有已批准的数据，并可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="40de9-111">**经理** 可以批准或拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="40de9-112">只有已批准的联系人对用户可见。</span><span class="sxs-lookup"><span data-stu-id="40de9-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="40de9-113">**管理员** 可以批准/拒绝和编辑/删除任何数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="40de9-114">此文档中的图像与最新模板并不完全匹配。</span><span class="sxs-lookup"><span data-stu-id="40de9-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="40de9-115">在下图中，用户 Rick (`rick@example.com`) 已登录。</span><span class="sxs-lookup"><span data-stu-id="40de9-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="40de9-116">Rick 只能查看已批准的联系人，**编辑** / **删除** / 为其联系人 **创建新** 链接。</span><span class="sxs-lookup"><span data-stu-id="40de9-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="40de9-117">只有 Rick 创建的最后一条记录才会显示 " **编辑** " 和 " **删除** " 链接。</span><span class="sxs-lookup"><span data-stu-id="40de9-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="40de9-118">在经理或管理员将状态更改为 "已批准" 之前，其他用户将看不到最后一条记录。</span><span class="sxs-lookup"><span data-stu-id="40de9-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![显示已登录的 Rick 的屏幕截图](secure-data/_static/rick.png)

<span data-ttu-id="40de9-120">在下图中， `manager@contoso.com` 已登录，并在管理器的角色中：</span><span class="sxs-lookup"><span data-stu-id="40de9-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![显示 manager@contoso.com 已登录的屏幕截图](secure-data/_static/manager1.png)

<span data-ttu-id="40de9-122">下图显示了联系人的经理详细信息视图：</span><span class="sxs-lookup"><span data-stu-id="40de9-122">The following image shows the managers details view of a contact:</span></span>

![联系人的经理视图](secure-data/_static/manager.png)

<span data-ttu-id="40de9-124">" **批准** " 和 " **拒绝** " 按钮仅为经理和管理员显示。</span><span class="sxs-lookup"><span data-stu-id="40de9-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="40de9-125">在下图中，以 `admin@contoso.com` 管理员的角色登录和：</span><span class="sxs-lookup"><span data-stu-id="40de9-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![显示 admin@contoso.com 已登录的屏幕截图](secure-data/_static/admin.png)

<span data-ttu-id="40de9-127">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="40de9-127">The administrator has all privileges.</span></span> <span data-ttu-id="40de9-128">她可以读取/编辑/删除任何联系人并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="40de9-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="40de9-129">此应用是通过 [基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 创建的 `Contact` ：以下模型：</span><span class="sxs-lookup"><span data-stu-id="40de9-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="40de9-130">该示例包含以下授权处理程序：</span><span class="sxs-lookup"><span data-stu-id="40de9-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="40de9-131">`ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="40de9-132">`ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="40de9-133">`ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="40de9-134">必备知识</span><span class="sxs-lookup"><span data-stu-id="40de9-134">Prerequisites</span></span>

<span data-ttu-id="40de9-135">本教程是高级教程。</span><span class="sxs-lookup"><span data-stu-id="40de9-135">This tutorial is advanced.</span></span> <span data-ttu-id="40de9-136">你应该熟悉：</span><span class="sxs-lookup"><span data-stu-id="40de9-136">You should be familiar with:</span></span>

* [<span data-ttu-id="40de9-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40de9-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="40de9-138">身份验证</span><span class="sxs-lookup"><span data-stu-id="40de9-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="40de9-139">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="40de9-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="40de9-140">授权</span><span class="sxs-lookup"><span data-stu-id="40de9-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="40de9-141">实体框架核心</span><span class="sxs-lookup"><span data-stu-id="40de9-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="40de9-142">入门和已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="40de9-142">The starter and completed app</span></span>

<span data-ttu-id="40de9-143">[下载](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)的应用。</span><span class="sxs-lookup"><span data-stu-id="40de9-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="40de9-144">[测试](#test-the-completed-app) 已完成的应用程序，使其安全功能熟悉。</span><span class="sxs-lookup"><span data-stu-id="40de9-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="40de9-145">入门应用</span><span class="sxs-lookup"><span data-stu-id="40de9-145">The starter app</span></span>

<span data-ttu-id="40de9-146">[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。</span><span class="sxs-lookup"><span data-stu-id="40de9-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="40de9-147">运行应用程序，点击 " **ContactManager** " 链接，并验证是否可以创建、编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="40de9-148">若要创建初学者应用，请参阅 [创建初学者应用](#create-the-starter-app)。</span><span class="sxs-lookup"><span data-stu-id="40de9-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="40de9-149">保护用户数据</span><span class="sxs-lookup"><span data-stu-id="40de9-149">Secure user data</span></span>

<span data-ttu-id="40de9-150">以下部分包含创建安全用户数据应用的所有主要步骤。</span><span class="sxs-lookup"><span data-stu-id="40de9-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="40de9-151">你可能会发现，引用已完成的项目非常有用。</span><span class="sxs-lookup"><span data-stu-id="40de9-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="40de9-152">将联系人数据与用户关联</span><span class="sxs-lookup"><span data-stu-id="40de9-152">Tie the contact data to the user</span></span>

<span data-ttu-id="40de9-153">使用 ASP.NET [Identity](xref:security/authentication/identity) 用户 ID 可确保用户能够编辑其数据，而不是其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="40de9-154">将 `OwnerID` 和添加 `ContactStatus` 到 `Contact` 模型：</span><span class="sxs-lookup"><span data-stu-id="40de9-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="40de9-155">`OwnerID` 数据库中的表的用户 ID `AspNetUser` [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="40de9-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="40de9-156">此 `Status` 字段确定常规用户是否可查看联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="40de9-157">创建新的迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="40de9-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="40de9-158">将角色服务添加到 Identity</span><span class="sxs-lookup"><span data-stu-id="40de9-158">Add Role services to Identity</span></span>

<span data-ttu-id="40de9-159">追加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 以添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="40de9-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="40de9-160">需要经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="40de9-160">Require authenticated users</span></span>

<span data-ttu-id="40de9-161">设置后备身份验证策略，要求对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="40de9-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="40de9-162">前面突出显示的代码设置了 [后备身份验证策略](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)。</span><span class="sxs-lookup"><span data-stu-id="40de9-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="40de9-163">回退身份验证策略要求 \**_所有_* _ 用户进行身份验证，但 Razor 页面、控制器或操作方法除外。</span><span class="sxs-lookup"><span data-stu-id="40de9-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="40de9-164">例如， Razor 使用或的页、控制器或操作方法 `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` 使用应用的身份验证属性而不是后备身份验证策略。</span><span class="sxs-lookup"><span data-stu-id="40de9-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="40de9-165">回退身份验证策略：</span><span class="sxs-lookup"><span data-stu-id="40de9-165">The fallback authentication policy:</span></span>

<span data-ttu-id="40de9-166">_ 适用于所有未显式指定身份验证策略的请求。</span><span class="sxs-lookup"><span data-stu-id="40de9-166">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="40de9-167">对于终结点路由服务的请求，这将包括未指定授权属性的任何终结点。</span><span class="sxs-lookup"><span data-stu-id="40de9-167">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="40de9-168">对于在授权中间件之后由其他中间件提供服务的请求，例如 [静态文件](xref:fundamentals/static-files)，这会将该策略应用到所有请求。</span><span class="sxs-lookup"><span data-stu-id="40de9-168">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="40de9-169">将后备身份验证策略设置为 "要求用户进行身份验证" 可保护新添加的 Razor 页面和控制器。</span><span class="sxs-lookup"><span data-stu-id="40de9-169">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="40de9-170">默认情况下，需要进行身份验证比依赖新控制器和 Razor 页面以包括属性更安全 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="40de9-170">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="40de9-171"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>类还包含 <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="40de9-171">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="40de9-172">`DefaultPolicy` `[Authorize]` 当未指定策略时，是与特性一起使用的策略。</span><span class="sxs-lookup"><span data-stu-id="40de9-172">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="40de9-173">`[Authorize]` 不包含命名策略，与不同 `[Authorize(PolicyName="MyPolicy")]` 。</span><span class="sxs-lookup"><span data-stu-id="40de9-173">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="40de9-174">有关策略的详细信息，请参阅 <xref:security/authorization/policies> 。</span><span class="sxs-lookup"><span data-stu-id="40de9-174">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="40de9-175">MVC 控制器和 Razor 页面要求对所有用户进行身份验证的另一种方法是添加授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="40de9-175">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="40de9-176">前面的代码使用授权筛选器，设置回退策略使用终结点路由。</span><span class="sxs-lookup"><span data-stu-id="40de9-176">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="40de9-177">设置回退策略是要求对所有用户进行身份验证的首选方式。</span><span class="sxs-lookup"><span data-stu-id="40de9-177">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="40de9-178">将 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 添加到 `Index` 和 `Privacy` 页面，以便匿名用户在注册之前可以获取有关站点的信息：</span><span class="sxs-lookup"><span data-stu-id="40de9-178">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="40de9-179">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="40de9-179">Configure the test account</span></span>

<span data-ttu-id="40de9-180">`SeedData`类创建两个帐户：管理员和管理器。</span><span class="sxs-lookup"><span data-stu-id="40de9-180">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="40de9-181">使用 [机密管理器工具](xref:security/app-secrets) 来设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="40de9-181">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="40de9-182">将项目目录中的密码设置 (包含 *Program.cs*) 的目录：</span><span class="sxs-lookup"><span data-stu-id="40de9-182">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="40de9-183">如果未指定强密码，则在调用时会引发异常 `SeedData.Initialize` 。</span><span class="sxs-lookup"><span data-stu-id="40de9-183">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="40de9-184">更新 `Main` 以使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="40de9-184">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="40de9-185">创建测试帐户并更新联系人</span><span class="sxs-lookup"><span data-stu-id="40de9-185">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="40de9-186">更新 `Initialize` 类中的方法 `SeedData` ，以创建测试帐户：</span><span class="sxs-lookup"><span data-stu-id="40de9-186">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="40de9-187">向联系人添加管理员用户 ID 和 `ContactStatus` 。</span><span class="sxs-lookup"><span data-stu-id="40de9-187">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="40de9-188">使其中一个联系人 "已提交" 和一个 "已拒绝"。</span><span class="sxs-lookup"><span data-stu-id="40de9-188">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="40de9-189">将用户 ID 和状态添加到所有联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-189">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="40de9-190">只显示一个联系人：</span><span class="sxs-lookup"><span data-stu-id="40de9-190">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="40de9-191">创建所有者、经理和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="40de9-191">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="40de9-192">`ContactIsOwnerAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="40de9-192">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="40de9-193">`ContactIsOwnerAuthorizationHandler`验证对资源的用户是否拥有该资源。</span><span class="sxs-lookup"><span data-stu-id="40de9-193">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="40de9-194">`ContactIsOwnerAuthorizationHandler`调用[上下文。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果当前经过身份验证的用户是联系人所有者，则会成功。</span><span class="sxs-lookup"><span data-stu-id="40de9-194">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="40de9-195">授权处理程序通常：</span><span class="sxs-lookup"><span data-stu-id="40de9-195">Authorization handlers generally:</span></span>

* <span data-ttu-id="40de9-196">`context.Succeed`满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="40de9-196">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="40de9-197">`Task.CompletedTask`当不满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="40de9-197">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="40de9-198">`Task.CompletedTask` 不是成功或失败， &mdash; 它允许其他授权处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="40de9-198">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="40de9-199">如果需要显式失败，请返回 [context。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="40de9-199">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="40de9-200">该应用程序允许联系人所有者编辑/删除/创建他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-200">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="40de9-201">`ContactIsOwnerAuthorizationHandler` 不需要检查在要求参数中传递的操作。</span><span class="sxs-lookup"><span data-stu-id="40de9-201">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="40de9-202">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="40de9-202">Create a manager authorization handler</span></span>

<span data-ttu-id="40de9-203">`ContactManagerAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="40de9-203">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="40de9-204">`ContactManagerAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="40de9-204">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="40de9-205">只有管理人员才能 (新的或更改的) 批准或拒绝内容更改。</span><span class="sxs-lookup"><span data-stu-id="40de9-205">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="40de9-206">创建管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="40de9-206">Create an administrator authorization handler</span></span>

<span data-ttu-id="40de9-207">`ContactAdministratorsAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="40de9-207">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="40de9-208">`ContactAdministratorsAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="40de9-208">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="40de9-209">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="40de9-209">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="40de9-210">注册授权处理程序</span><span class="sxs-lookup"><span data-stu-id="40de9-210">Register the authorization handlers</span></span>

<span data-ttu-id="40de9-211">Entity Framework Core 使用 AddScoped 的服务必须使用[](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)注册以进行[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="40de9-211">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="40de9-212">`ContactIsOwnerAuthorizationHandler`使用 [Identity](xref:security/authentication/identity) 在 Entity Framework Core 上构建 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="40de9-212">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="40de9-213">向服务集合注册处理程序，使其可 `ContactsController` 通过 [依赖关系注入](xref:fundamentals/dependency-injection)获得。</span><span class="sxs-lookup"><span data-stu-id="40de9-213">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="40de9-214">将以下代码添加到的末尾 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="40de9-214">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="40de9-215">`ContactAdministratorsAuthorizationHandler` 和 `ContactManagerAuthorizationHandler` 将添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="40de9-215">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="40de9-216">它们是单一实例的，因为它们不使用 EF，并且所需的所有信息都在 `Context` 方法的参数中 `HandleRequirementAsync` 。</span><span class="sxs-lookup"><span data-stu-id="40de9-216">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="40de9-217">支持授权</span><span class="sxs-lookup"><span data-stu-id="40de9-217">Support authorization</span></span>

<span data-ttu-id="40de9-218">在本部分中，将更新 Razor 页面并添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="40de9-218">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="40de9-219">查看联系操作要求类</span><span class="sxs-lookup"><span data-stu-id="40de9-219">Review the contact operations requirements class</span></span>

<span data-ttu-id="40de9-220">查看 `ContactOperations` 类。</span><span class="sxs-lookup"><span data-stu-id="40de9-220">Review the `ContactOperations` class.</span></span> <span data-ttu-id="40de9-221">此类包含应用支持的要求：</span><span class="sxs-lookup"><span data-stu-id="40de9-221">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="40de9-222">为联系人页创建基类 Razor</span><span class="sxs-lookup"><span data-stu-id="40de9-222">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="40de9-223">创建一个包含联系人页中使用的服务的基类 Razor 。</span><span class="sxs-lookup"><span data-stu-id="40de9-223">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="40de9-224">基类将初始化代码放在一个位置：</span><span class="sxs-lookup"><span data-stu-id="40de9-224">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="40de9-225">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="40de9-225">The preceding code:</span></span>

* <span data-ttu-id="40de9-226">添加 `IAuthorizationService` 服务以访问授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="40de9-226">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="40de9-227">添加 Identity `UserManager` 服务。</span><span class="sxs-lookup"><span data-stu-id="40de9-227">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="40de9-228">添加 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="40de9-228">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="40de9-229">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="40de9-229">Update the CreateModel</span></span>

<span data-ttu-id="40de9-230">更新 "创建页模型" 构造函数以使用 `DI_BasePageModel` 基类：</span><span class="sxs-lookup"><span data-stu-id="40de9-230">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="40de9-231">将 `CreateModel.OnPostAsync` 方法更新为：</span><span class="sxs-lookup"><span data-stu-id="40de9-231">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="40de9-232">将用户 ID 添加到 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="40de9-232">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="40de9-233">调用授权处理程序以验证用户是否有权创建联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-233">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="40de9-234">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="40de9-234">Update the IndexModel</span></span>

<span data-ttu-id="40de9-235">更新 `OnGetAsync` 方法以便仅向一般用户显示已批准的联系人：</span><span class="sxs-lookup"><span data-stu-id="40de9-235">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="40de9-236">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="40de9-236">Update the EditModel</span></span>

<span data-ttu-id="40de9-237">添加一个授权处理程序来验证用户是否拥有该联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-237">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="40de9-238">由于正在验证资源授权，因此 `[Authorize]` 属性不够。</span><span class="sxs-lookup"><span data-stu-id="40de9-238">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="40de9-239">计算属性时，应用无法访问资源。</span><span class="sxs-lookup"><span data-stu-id="40de9-239">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="40de9-240">基于资源的授权必须是必需的。</span><span class="sxs-lookup"><span data-stu-id="40de9-240">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="40de9-241">如果应用有权访问该资源，则必须执行检查，方法是将其加载到页面模型中，或在处理程序本身中加载它。</span><span class="sxs-lookup"><span data-stu-id="40de9-241">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="40de9-242">通过传入资源键，可以频繁地访问资源。</span><span class="sxs-lookup"><span data-stu-id="40de9-242">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="40de9-243">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="40de9-243">Update the DeleteModel</span></span>

<span data-ttu-id="40de9-244">更新 "删除" 页模型，以使用授权处理程序来验证用户是否具有对联系人的 "删除" 权限。</span><span class="sxs-lookup"><span data-stu-id="40de9-244">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="40de9-245">将授权服务注入视图</span><span class="sxs-lookup"><span data-stu-id="40de9-245">Inject the authorization service into the views</span></span>

<span data-ttu-id="40de9-246">目前，UI 会显示用户不能修改的联系人的编辑和删除链接。</span><span class="sxs-lookup"><span data-stu-id="40de9-246">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="40de9-247">将授权服务注入 *Pages/_ViewImports cshtml* 文件，使其可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="40de9-247">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="40de9-248">前面的标记添加了多个 `using` 语句。</span><span class="sxs-lookup"><span data-stu-id="40de9-248">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="40de9-249">更新 *页面/联系人/索引* 中的 "**编辑**" 和 "**删除**" 链接，以便仅为具有适当权限的用户呈现它们：</span><span class="sxs-lookup"><span data-stu-id="40de9-249">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="40de9-250">隐藏不具有更改数据权限的用户的链接不会保护应用的安全。</span><span class="sxs-lookup"><span data-stu-id="40de9-250">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="40de9-251">隐藏链接使应用程序更易于用户理解，只显示有效的链接。</span><span class="sxs-lookup"><span data-stu-id="40de9-251">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="40de9-252">用户可以通过攻击生成的 Url 来对其不拥有的数据调用编辑和删除操作。</span><span class="sxs-lookup"><span data-stu-id="40de9-252">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="40de9-253">Razor页或控制器必须强制进行访问检查以确保数据的安全。</span><span class="sxs-lookup"><span data-stu-id="40de9-253">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="40de9-254">更新详细信息</span><span class="sxs-lookup"><span data-stu-id="40de9-254">Update Details</span></span>

<span data-ttu-id="40de9-255">更新详细信息视图，以便经理可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="40de9-255">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="40de9-256">更新详细信息页模型：</span><span class="sxs-lookup"><span data-stu-id="40de9-256">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="40de9-257">在角色中添加或删除用户</span><span class="sxs-lookup"><span data-stu-id="40de9-257">Add or remove a user to a role</span></span>

<span data-ttu-id="40de9-258">有关信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/8502) ：</span><span class="sxs-lookup"><span data-stu-id="40de9-258">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="40de9-259">正在删除用户的权限。</span><span class="sxs-lookup"><span data-stu-id="40de9-259">Removing privileges from a user.</span></span> <span data-ttu-id="40de9-260">例如，在聊天应用中对用户进行静音。</span><span class="sxs-lookup"><span data-stu-id="40de9-260">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="40de9-261">向用户添加特权。</span><span class="sxs-lookup"><span data-stu-id="40de9-261">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="40de9-262">质询和禁止之间的差异</span><span class="sxs-lookup"><span data-stu-id="40de9-262">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="40de9-263">此应用将默认策略设置为 " [需要经过身份验证的用户](#rau)"。</span><span class="sxs-lookup"><span data-stu-id="40de9-263">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="40de9-264">以下代码允许匿名用户。</span><span class="sxs-lookup"><span data-stu-id="40de9-264">The following code allows anonymous users.</span></span> <span data-ttu-id="40de9-265">允许匿名用户显示质询与禁止之间的差异。</span><span class="sxs-lookup"><span data-stu-id="40de9-265">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="40de9-266">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="40de9-266">In the preceding code:</span></span>

* <span data-ttu-id="40de9-267">如果用户 **未通过身份** 验证， `ChallengeResult` 则返回。</span><span class="sxs-lookup"><span data-stu-id="40de9-267">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="40de9-268">返回后 `ChallengeResult` ，会将用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="40de9-268">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="40de9-269">如果用户已通过身份验证，但未获得授权， `ForbidResult` 则返回。</span><span class="sxs-lookup"><span data-stu-id="40de9-269">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="40de9-270">返回后 `ForbidResult` ，会将用户重定向到 "拒绝访问" 页。</span><span class="sxs-lookup"><span data-stu-id="40de9-270">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="40de9-271">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="40de9-271">Test the completed app</span></span>

<span data-ttu-id="40de9-272">如果尚未为种子设定用户帐户设置密码，请使用 [机密管理器工具](xref:security/app-secrets#secret-manager) 设置密码：</span><span class="sxs-lookup"><span data-stu-id="40de9-272">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="40de9-273">选择强密码：使用八个或更多字符，并且至少使用一个大写字符、数字和符号。</span><span class="sxs-lookup"><span data-stu-id="40de9-273">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="40de9-274">例如， `Passw0rd!` 满足强密码要求。</span><span class="sxs-lookup"><span data-stu-id="40de9-274">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="40de9-275">从项目的文件夹中执行以下命令，其中 `<PW>` 是密码：</span><span class="sxs-lookup"><span data-stu-id="40de9-275">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="40de9-276">如果应用有联系人：</span><span class="sxs-lookup"><span data-stu-id="40de9-276">If the app has contacts:</span></span>

* <span data-ttu-id="40de9-277">删除表中的所有记录 `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="40de9-277">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="40de9-278">重新启动应用以对数据库进行种子设定。</span><span class="sxs-lookup"><span data-stu-id="40de9-278">Restart the app to seed the database.</span></span>

<span data-ttu-id="40de9-279">测试已完成应用程序的一种简单方法是启动三个不同的浏览器 (或 incognito/InPrivate 会话) 。</span><span class="sxs-lookup"><span data-stu-id="40de9-279">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="40de9-280">在一个浏览器中，注册新用户 (例如 `test@example.com`) 。</span><span class="sxs-lookup"><span data-stu-id="40de9-280">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="40de9-281">使用其他用户登录到每个浏览器。</span><span class="sxs-lookup"><span data-stu-id="40de9-281">Sign in to each browser with a different user.</span></span> <span data-ttu-id="40de9-282">验证下列操作：</span><span class="sxs-lookup"><span data-stu-id="40de9-282">Verify the following operations:</span></span>

* <span data-ttu-id="40de9-283">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-283">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="40de9-284">已注册的用户可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-284">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="40de9-285">经理可以批准/拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-285">Managers can approve/reject contact data.</span></span> <span data-ttu-id="40de9-286">此 `Details` 视图显示 " **批准** " 和 " **拒绝** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="40de9-286">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="40de9-287">管理员可以批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-287">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="40de9-288">用户</span><span class="sxs-lookup"><span data-stu-id="40de9-288">User</span></span>                | <span data-ttu-id="40de9-289">应用程序的种子</span><span class="sxs-lookup"><span data-stu-id="40de9-289">Seeded by the app</span></span> | <span data-ttu-id="40de9-290">选项</span><span class="sxs-lookup"><span data-stu-id="40de9-290">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="40de9-291">否</span><span class="sxs-lookup"><span data-stu-id="40de9-291">No</span></span>                | <span data-ttu-id="40de9-292">编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-292">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="40de9-293">是</span><span class="sxs-lookup"><span data-stu-id="40de9-293">Yes</span></span>               | <span data-ttu-id="40de9-294">批准/拒绝和编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-294">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="40de9-295">是</span><span class="sxs-lookup"><span data-stu-id="40de9-295">Yes</span></span>               | <span data-ttu-id="40de9-296">批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-296">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="40de9-297">在管理员的浏览器中创建联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-297">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="40de9-298">复制管理员联系人的 "删除" 和 "编辑" 的 URL。</span><span class="sxs-lookup"><span data-stu-id="40de9-298">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="40de9-299">将这些链接粘贴到测试用户的浏览器中，以验证测试用户是否无法执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="40de9-299">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="40de9-300">创建初学者应用</span><span class="sxs-lookup"><span data-stu-id="40de9-300">Create the starter app</span></span>

* <span data-ttu-id="40de9-301">创建 Razor 名为 "ContactManager" 的页面应用</span><span class="sxs-lookup"><span data-stu-id="40de9-301">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="40de9-302">创建具有 **单个用户帐户** 的应用。</span><span class="sxs-lookup"><span data-stu-id="40de9-302">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="40de9-303">将其命名为 "ContactManager"，使命名空间与该示例中使用的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="40de9-303">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="40de9-304">`-uld` 指定 LocalDB 而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="40de9-304">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="40de9-305">添加 *模型/联系方式*：</span><span class="sxs-lookup"><span data-stu-id="40de9-305">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="40de9-306">基架 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="40de9-306">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="40de9-307">创建初始迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="40de9-307">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="40de9-308">如果使用命令遇到 bug `dotnet aspnet-codegenerator razorpage` ，请参阅 [此 GitHub 问题](https://github.com/aspnet/Scaffolding/issues/984)。</span><span class="sxs-lookup"><span data-stu-id="40de9-308">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="40de9-309">更新 *Pages/Shared/_Layout cshtml* 文件中的 **ContactManager** 定位点：</span><span class="sxs-lookup"><span data-stu-id="40de9-309">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="40de9-310">通过创建、编辑和删除联系人来测试应用</span><span class="sxs-lookup"><span data-stu-id="40de9-310">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="40de9-311">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="40de9-311">Seed the database</span></span>

<span data-ttu-id="40de9-312">将 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) 类添加到 *Data* 文件夹：</span><span class="sxs-lookup"><span data-stu-id="40de9-312">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="40de9-313">调用 `SeedData.Initialize` 自 `Main` ：</span><span class="sxs-lookup"><span data-stu-id="40de9-313">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="40de9-314">测试该应用是否为该数据库的种子。</span><span class="sxs-lookup"><span data-stu-id="40de9-314">Test that the app seeded the database.</span></span> <span data-ttu-id="40de9-315">如果 contact DB 中存在任何行，则 seed 方法不会运行。</span><span class="sxs-lookup"><span data-stu-id="40de9-315">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="40de9-316">本教程演示如何创建 ASP.NET Core 的 web 应用，其中包含由授权保护的用户数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-316">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="40de9-317">它将显示已进行身份验证 (已创建的已注册) 用户的联系人列表。</span><span class="sxs-lookup"><span data-stu-id="40de9-317">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="40de9-318">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="40de9-318">There are three security groups:</span></span>

* <span data-ttu-id="40de9-319">**已注册的用户** 可以查看所有已批准的数据，并可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-319">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="40de9-320">**经理** 可以批准或拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-320">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="40de9-321">只有已批准的联系人对用户可见。</span><span class="sxs-lookup"><span data-stu-id="40de9-321">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="40de9-322">**管理员** 可以批准/拒绝和编辑/删除任何数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-322">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="40de9-323">在下图中，用户 Rick (`rick@example.com`) 已登录。</span><span class="sxs-lookup"><span data-stu-id="40de9-323">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="40de9-324">Rick 只能查看已批准的联系人，**编辑** / **删除** / 为其联系人 **创建新** 链接。</span><span class="sxs-lookup"><span data-stu-id="40de9-324">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="40de9-325">只有 Rick 创建的最后一条记录才会显示 " **编辑** " 和 " **删除** " 链接。</span><span class="sxs-lookup"><span data-stu-id="40de9-325">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="40de9-326">在经理或管理员将状态更改为 "已批准" 之前，其他用户将看不到最后一条记录。</span><span class="sxs-lookup"><span data-stu-id="40de9-326">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![显示已登录的 Rick 的屏幕截图](secure-data/_static/rick.png)

<span data-ttu-id="40de9-328">在下图中， `manager@contoso.com` 已登录，并在管理器的角色中：</span><span class="sxs-lookup"><span data-stu-id="40de9-328">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![显示 manager@contoso.com 已登录的屏幕截图](secure-data/_static/manager1.png)

<span data-ttu-id="40de9-330">下图显示了联系人的经理详细信息视图：</span><span class="sxs-lookup"><span data-stu-id="40de9-330">The following image shows the managers details view of a contact:</span></span>

![联系人的经理视图](secure-data/_static/manager.png)

<span data-ttu-id="40de9-332">" **批准** " 和 " **拒绝** " 按钮仅为经理和管理员显示。</span><span class="sxs-lookup"><span data-stu-id="40de9-332">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="40de9-333">在下图中，以 `admin@contoso.com` 管理员的角色登录和：</span><span class="sxs-lookup"><span data-stu-id="40de9-333">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![显示 admin@contoso.com 已登录的屏幕截图](secure-data/_static/admin.png)

<span data-ttu-id="40de9-335">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="40de9-335">The administrator has all privileges.</span></span> <span data-ttu-id="40de9-336">她可以读取/编辑/删除任何联系人并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="40de9-336">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="40de9-337">此应用是通过 [基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 创建的 `Contact` ：以下模型：</span><span class="sxs-lookup"><span data-stu-id="40de9-337">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="40de9-338">该示例包含以下授权处理程序：</span><span class="sxs-lookup"><span data-stu-id="40de9-338">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="40de9-339">`ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-339">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="40de9-340">`ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-340">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="40de9-341">`ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-341">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="40de9-342">必备知识</span><span class="sxs-lookup"><span data-stu-id="40de9-342">Prerequisites</span></span>

<span data-ttu-id="40de9-343">本教程是高级教程。</span><span class="sxs-lookup"><span data-stu-id="40de9-343">This tutorial is advanced.</span></span> <span data-ttu-id="40de9-344">你应该熟悉：</span><span class="sxs-lookup"><span data-stu-id="40de9-344">You should be familiar with:</span></span>

* [<span data-ttu-id="40de9-345">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="40de9-345">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="40de9-346">身份验证</span><span class="sxs-lookup"><span data-stu-id="40de9-346">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="40de9-347">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="40de9-347">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="40de9-348">授权</span><span class="sxs-lookup"><span data-stu-id="40de9-348">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="40de9-349">实体框架核心</span><span class="sxs-lookup"><span data-stu-id="40de9-349">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="40de9-350">入门和已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="40de9-350">The starter and completed app</span></span>

<span data-ttu-id="40de9-351">[下载](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)的应用。</span><span class="sxs-lookup"><span data-stu-id="40de9-351">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="40de9-352">[测试](#test-the-completed-app) 已完成的应用程序，使其安全功能熟悉。</span><span class="sxs-lookup"><span data-stu-id="40de9-352">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="40de9-353">入门应用</span><span class="sxs-lookup"><span data-stu-id="40de9-353">The starter app</span></span>

<span data-ttu-id="40de9-354">[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。</span><span class="sxs-lookup"><span data-stu-id="40de9-354">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="40de9-355">运行应用程序，点击 " **ContactManager** " 链接，并验证是否可以创建、编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-355">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="40de9-356">保护用户数据</span><span class="sxs-lookup"><span data-stu-id="40de9-356">Secure user data</span></span>

<span data-ttu-id="40de9-357">以下部分包含创建安全用户数据应用的所有主要步骤。</span><span class="sxs-lookup"><span data-stu-id="40de9-357">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="40de9-358">你可能会发现，引用已完成的项目非常有用。</span><span class="sxs-lookup"><span data-stu-id="40de9-358">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="40de9-359">将联系人数据与用户关联</span><span class="sxs-lookup"><span data-stu-id="40de9-359">Tie the contact data to the user</span></span>

<span data-ttu-id="40de9-360">使用 ASP.NET [Identity](xref:security/authentication/identity) 用户 ID 可确保用户能够编辑其数据，而不是其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-360">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="40de9-361">将 `OwnerID` 和添加 `ContactStatus` 到 `Contact` 模型：</span><span class="sxs-lookup"><span data-stu-id="40de9-361">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="40de9-362">`OwnerID` 数据库中的表的用户 ID `AspNetUser` [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="40de9-362">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="40de9-363">此 `Status` 字段确定常规用户是否可查看联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-363">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="40de9-364">创建新的迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="40de9-364">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="40de9-365">将角色服务添加到 Identity</span><span class="sxs-lookup"><span data-stu-id="40de9-365">Add Role services to Identity</span></span>

<span data-ttu-id="40de9-366">追加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 以添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="40de9-366">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="40de9-367">需要经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="40de9-367">Require authenticated users</span></span>

<span data-ttu-id="40de9-368">将默认的 "身份验证策略" 设置为 "要求用户进行身份验证"：</span><span class="sxs-lookup"><span data-stu-id="40de9-368">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="40de9-369">您可以 Razor 通过属性在页、控制器或操作方法级别选择不进行身份验证 `[AllowAnonymous]` 。</span><span class="sxs-lookup"><span data-stu-id="40de9-369">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="40de9-370">将默认身份验证策略设置为 "要求用户进行身份验证" 可保护新添加的 Razor 页面和控制器。</span><span class="sxs-lookup"><span data-stu-id="40de9-370">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="40de9-371">默认情况下，需要进行身份验证比依赖新控制器和 Razor 页面以包括属性更安全 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="40de9-371">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="40de9-372">将 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 添加到 "索引"、"关于" 和 "联系人" 页，以便匿名用户在注册之前可以获取有关站点的信息。</span><span class="sxs-lookup"><span data-stu-id="40de9-372">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="40de9-373">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="40de9-373">Configure the test account</span></span>

<span data-ttu-id="40de9-374">`SeedData`类创建两个帐户：管理员和管理器。</span><span class="sxs-lookup"><span data-stu-id="40de9-374">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="40de9-375">使用 [机密管理器工具](xref:security/app-secrets) 来设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="40de9-375">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="40de9-376">将项目目录中的密码设置 (包含 *Program.cs*) 的目录：</span><span class="sxs-lookup"><span data-stu-id="40de9-376">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="40de9-377">如果未指定强密码，则在调用时会引发异常 `SeedData.Initialize` 。</span><span class="sxs-lookup"><span data-stu-id="40de9-377">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="40de9-378">更新 `Main` 以使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="40de9-378">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="40de9-379">创建测试帐户并更新联系人</span><span class="sxs-lookup"><span data-stu-id="40de9-379">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="40de9-380">更新 `Initialize` 类中的方法 `SeedData` ，以创建测试帐户：</span><span class="sxs-lookup"><span data-stu-id="40de9-380">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="40de9-381">向联系人添加管理员用户 ID 和 `ContactStatus` 。</span><span class="sxs-lookup"><span data-stu-id="40de9-381">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="40de9-382">使其中一个联系人 "已提交" 和一个 "已拒绝"。</span><span class="sxs-lookup"><span data-stu-id="40de9-382">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="40de9-383">将用户 ID 和状态添加到所有联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-383">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="40de9-384">只显示一个联系人：</span><span class="sxs-lookup"><span data-stu-id="40de9-384">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="40de9-385">创建所有者、经理和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="40de9-385">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="40de9-386">创建一个 *授权* 文件夹并 `ContactIsOwnerAuthorizationHandler` 在其中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="40de9-386">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="40de9-387">`ContactIsOwnerAuthorizationHandler`验证对资源的用户是否拥有该资源。</span><span class="sxs-lookup"><span data-stu-id="40de9-387">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="40de9-388">`ContactIsOwnerAuthorizationHandler`调用[上下文。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果当前经过身份验证的用户是联系人所有者，则会成功。</span><span class="sxs-lookup"><span data-stu-id="40de9-388">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="40de9-389">授权处理程序通常：</span><span class="sxs-lookup"><span data-stu-id="40de9-389">Authorization handlers generally:</span></span>

* <span data-ttu-id="40de9-390">`context.Succeed`满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="40de9-390">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="40de9-391">`Task.CompletedTask`当不满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="40de9-391">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="40de9-392">`Task.CompletedTask` 不是成功或失败， &mdash; 它允许其他授权处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="40de9-392">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="40de9-393">如果需要显式失败，请返回 [context。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="40de9-393">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="40de9-394">该应用程序允许联系人所有者编辑/删除/创建他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-394">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="40de9-395">`ContactIsOwnerAuthorizationHandler` 不需要检查在要求参数中传递的操作。</span><span class="sxs-lookup"><span data-stu-id="40de9-395">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="40de9-396">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="40de9-396">Create a manager authorization handler</span></span>

<span data-ttu-id="40de9-397">`ContactManagerAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="40de9-397">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="40de9-398">`ContactManagerAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="40de9-398">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="40de9-399">只有管理人员才能 (新的或更改的) 批准或拒绝内容更改。</span><span class="sxs-lookup"><span data-stu-id="40de9-399">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="40de9-400">创建管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="40de9-400">Create an administrator authorization handler</span></span>

<span data-ttu-id="40de9-401">`ContactAdministratorsAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="40de9-401">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="40de9-402">`ContactAdministratorsAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="40de9-402">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="40de9-403">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="40de9-403">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="40de9-404">注册授权处理程序</span><span class="sxs-lookup"><span data-stu-id="40de9-404">Register the authorization handlers</span></span>

<span data-ttu-id="40de9-405">Entity Framework Core 使用 AddScoped 的服务必须使用[](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)注册以进行[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="40de9-405">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="40de9-406">`ContactIsOwnerAuthorizationHandler`使用 [Identity](xref:security/authentication/identity) 在 Entity Framework Core 上构建 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="40de9-406">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="40de9-407">向服务集合注册处理程序，使其可 `ContactsController` 通过 [依赖关系注入](xref:fundamentals/dependency-injection)获得。</span><span class="sxs-lookup"><span data-stu-id="40de9-407">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="40de9-408">将以下代码添加到的末尾 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="40de9-408">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="40de9-409">`ContactAdministratorsAuthorizationHandler` 和 `ContactManagerAuthorizationHandler` 将添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="40de9-409">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="40de9-410">它们是单一实例的，因为它们不使用 EF，并且所需的所有信息都在 `Context` 方法的参数中 `HandleRequirementAsync` 。</span><span class="sxs-lookup"><span data-stu-id="40de9-410">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="40de9-411">支持授权</span><span class="sxs-lookup"><span data-stu-id="40de9-411">Support authorization</span></span>

<span data-ttu-id="40de9-412">在本部分中，将更新 Razor 页面并添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="40de9-412">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="40de9-413">查看联系操作要求类</span><span class="sxs-lookup"><span data-stu-id="40de9-413">Review the contact operations requirements class</span></span>

<span data-ttu-id="40de9-414">查看 `ContactOperations` 类。</span><span class="sxs-lookup"><span data-stu-id="40de9-414">Review the `ContactOperations` class.</span></span> <span data-ttu-id="40de9-415">此类包含应用支持的要求：</span><span class="sxs-lookup"><span data-stu-id="40de9-415">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="40de9-416">为联系人页创建基类 Razor</span><span class="sxs-lookup"><span data-stu-id="40de9-416">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="40de9-417">创建一个包含联系人页中使用的服务的基类 Razor 。</span><span class="sxs-lookup"><span data-stu-id="40de9-417">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="40de9-418">基类将初始化代码放在一个位置：</span><span class="sxs-lookup"><span data-stu-id="40de9-418">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="40de9-419">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="40de9-419">The preceding code:</span></span>

* <span data-ttu-id="40de9-420">添加 `IAuthorizationService` 服务以访问授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="40de9-420">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="40de9-421">添加 Identity `UserManager` 服务。</span><span class="sxs-lookup"><span data-stu-id="40de9-421">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="40de9-422">添加 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="40de9-422">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="40de9-423">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="40de9-423">Update the CreateModel</span></span>

<span data-ttu-id="40de9-424">更新 "创建页模型" 构造函数以使用 `DI_BasePageModel` 基类：</span><span class="sxs-lookup"><span data-stu-id="40de9-424">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="40de9-425">将 `CreateModel.OnPostAsync` 方法更新为：</span><span class="sxs-lookup"><span data-stu-id="40de9-425">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="40de9-426">将用户 ID 添加到 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="40de9-426">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="40de9-427">调用授权处理程序以验证用户是否有权创建联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-427">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="40de9-428">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="40de9-428">Update the IndexModel</span></span>

<span data-ttu-id="40de9-429">更新 `OnGetAsync` 方法以便仅向一般用户显示已批准的联系人：</span><span class="sxs-lookup"><span data-stu-id="40de9-429">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="40de9-430">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="40de9-430">Update the EditModel</span></span>

<span data-ttu-id="40de9-431">添加一个授权处理程序来验证用户是否拥有该联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-431">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="40de9-432">由于正在验证资源授权，因此 `[Authorize]` 属性不够。</span><span class="sxs-lookup"><span data-stu-id="40de9-432">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="40de9-433">计算属性时，应用无法访问资源。</span><span class="sxs-lookup"><span data-stu-id="40de9-433">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="40de9-434">基于资源的授权必须是必需的。</span><span class="sxs-lookup"><span data-stu-id="40de9-434">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="40de9-435">如果应用有权访问该资源，则必须执行检查，方法是将其加载到页面模型中，或在处理程序本身中加载它。</span><span class="sxs-lookup"><span data-stu-id="40de9-435">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="40de9-436">通过传入资源键，可以频繁地访问资源。</span><span class="sxs-lookup"><span data-stu-id="40de9-436">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="40de9-437">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="40de9-437">Update the DeleteModel</span></span>

<span data-ttu-id="40de9-438">更新 "删除" 页模型，以使用授权处理程序来验证用户是否具有对联系人的 "删除" 权限。</span><span class="sxs-lookup"><span data-stu-id="40de9-438">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="40de9-439">将授权服务注入视图</span><span class="sxs-lookup"><span data-stu-id="40de9-439">Inject the authorization service into the views</span></span>

<span data-ttu-id="40de9-440">目前，UI 会显示用户不能修改的联系人的编辑和删除链接。</span><span class="sxs-lookup"><span data-stu-id="40de9-440">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="40de9-441">将授权服务注入到 *views/_ViewImports cshtml* 文件中，使其可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="40de9-441">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="40de9-442">前面的标记添加了多个 `using` 语句。</span><span class="sxs-lookup"><span data-stu-id="40de9-442">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="40de9-443">更新 *页面/联系人/索引* 中的 "**编辑**" 和 "**删除**" 链接，以便仅为具有适当权限的用户呈现它们：</span><span class="sxs-lookup"><span data-stu-id="40de9-443">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="40de9-444">隐藏不具有更改数据权限的用户的链接不会保护应用的安全。</span><span class="sxs-lookup"><span data-stu-id="40de9-444">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="40de9-445">隐藏链接使应用程序更易于用户理解，只显示有效的链接。</span><span class="sxs-lookup"><span data-stu-id="40de9-445">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="40de9-446">用户可以通过攻击生成的 Url 来对其不拥有的数据调用编辑和删除操作。</span><span class="sxs-lookup"><span data-stu-id="40de9-446">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="40de9-447">Razor页或控制器必须强制进行访问检查以确保数据的安全。</span><span class="sxs-lookup"><span data-stu-id="40de9-447">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="40de9-448">更新详细信息</span><span class="sxs-lookup"><span data-stu-id="40de9-448">Update Details</span></span>

<span data-ttu-id="40de9-449">更新详细信息视图，以便经理可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="40de9-449">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="40de9-450">更新详细信息页模型：</span><span class="sxs-lookup"><span data-stu-id="40de9-450">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="40de9-451">在角色中添加或删除用户</span><span class="sxs-lookup"><span data-stu-id="40de9-451">Add or remove a user to a role</span></span>

<span data-ttu-id="40de9-452">有关信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/8502) ：</span><span class="sxs-lookup"><span data-stu-id="40de9-452">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="40de9-453">正在删除用户的权限。</span><span class="sxs-lookup"><span data-stu-id="40de9-453">Removing privileges from a user.</span></span> <span data-ttu-id="40de9-454">例如，在聊天应用中对用户进行静音。</span><span class="sxs-lookup"><span data-stu-id="40de9-454">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="40de9-455">向用户添加特权。</span><span class="sxs-lookup"><span data-stu-id="40de9-455">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="40de9-456">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="40de9-456">Test the completed app</span></span>

<span data-ttu-id="40de9-457">如果尚未为种子设定用户帐户设置密码，请使用 [机密管理器工具](xref:security/app-secrets#secret-manager) 设置密码：</span><span class="sxs-lookup"><span data-stu-id="40de9-457">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="40de9-458">选择强密码：使用八个或更多字符，并且至少使用一个大写字符、数字和符号。</span><span class="sxs-lookup"><span data-stu-id="40de9-458">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="40de9-459">例如， `Passw0rd!` 满足强密码要求。</span><span class="sxs-lookup"><span data-stu-id="40de9-459">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="40de9-460">从项目的文件夹中执行以下命令，其中 `<PW>` 是密码：</span><span class="sxs-lookup"><span data-stu-id="40de9-460">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="40de9-461">删除和更新数据库</span><span class="sxs-lookup"><span data-stu-id="40de9-461">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="40de9-462">重新启动应用以对数据库进行种子设定。</span><span class="sxs-lookup"><span data-stu-id="40de9-462">Restart the app to seed the database.</span></span>

<span data-ttu-id="40de9-463">测试已完成应用程序的一种简单方法是启动三个不同的浏览器 (或 incognito/InPrivate 会话) 。</span><span class="sxs-lookup"><span data-stu-id="40de9-463">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="40de9-464">在一个浏览器中，注册新用户 (例如 `test@example.com`) 。</span><span class="sxs-lookup"><span data-stu-id="40de9-464">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="40de9-465">使用其他用户登录到每个浏览器。</span><span class="sxs-lookup"><span data-stu-id="40de9-465">Sign in to each browser with a different user.</span></span> <span data-ttu-id="40de9-466">验证下列操作：</span><span class="sxs-lookup"><span data-stu-id="40de9-466">Verify the following operations:</span></span>

* <span data-ttu-id="40de9-467">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-467">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="40de9-468">已注册的用户可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-468">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="40de9-469">经理可以批准/拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-469">Managers can approve/reject contact data.</span></span> <span data-ttu-id="40de9-470">此 `Details` 视图显示 " **批准** " 和 " **拒绝** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="40de9-470">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="40de9-471">管理员可以批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-471">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="40de9-472">用户</span><span class="sxs-lookup"><span data-stu-id="40de9-472">User</span></span>                | <span data-ttu-id="40de9-473">应用程序的种子</span><span class="sxs-lookup"><span data-stu-id="40de9-473">Seeded by the app</span></span> | <span data-ttu-id="40de9-474">选项</span><span class="sxs-lookup"><span data-stu-id="40de9-474">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="40de9-475">否</span><span class="sxs-lookup"><span data-stu-id="40de9-475">No</span></span>                | <span data-ttu-id="40de9-476">编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-476">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="40de9-477">是</span><span class="sxs-lookup"><span data-stu-id="40de9-477">Yes</span></span>               | <span data-ttu-id="40de9-478">批准/拒绝和编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-478">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="40de9-479">是</span><span class="sxs-lookup"><span data-stu-id="40de9-479">Yes</span></span>               | <span data-ttu-id="40de9-480">批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="40de9-480">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="40de9-481">在管理员的浏览器中创建联系人。</span><span class="sxs-lookup"><span data-stu-id="40de9-481">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="40de9-482">复制管理员联系人的 "删除" 和 "编辑" 的 URL。</span><span class="sxs-lookup"><span data-stu-id="40de9-482">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="40de9-483">将这些链接粘贴到测试用户的浏览器中，以验证测试用户是否无法执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="40de9-483">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="40de9-484">创建初学者应用</span><span class="sxs-lookup"><span data-stu-id="40de9-484">Create the starter app</span></span>

* <span data-ttu-id="40de9-485">创建 Razor 名为 "ContactManager" 的页面应用</span><span class="sxs-lookup"><span data-stu-id="40de9-485">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="40de9-486">创建具有 **单个用户帐户** 的应用。</span><span class="sxs-lookup"><span data-stu-id="40de9-486">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="40de9-487">将其命名为 "ContactManager"，使命名空间与该示例中使用的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="40de9-487">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="40de9-488">`-uld` 指定 LocalDB 而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="40de9-488">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="40de9-489">添加 *模型/联系方式*：</span><span class="sxs-lookup"><span data-stu-id="40de9-489">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="40de9-490">基架 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="40de9-490">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="40de9-491">创建初始迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="40de9-491">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="40de9-492">更新 *Pages/_Layout cshtml* 文件中的 **ContactManager** 定位点：</span><span class="sxs-lookup"><span data-stu-id="40de9-492">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="40de9-493">通过创建、编辑和删除联系人来测试应用</span><span class="sxs-lookup"><span data-stu-id="40de9-493">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="40de9-494">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="40de9-494">Seed the database</span></span>

<span data-ttu-id="40de9-495">将 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) 类添加到 *Data* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="40de9-495">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="40de9-496">调用 `SeedData.Initialize` 自 `Main` ：</span><span class="sxs-lookup"><span data-stu-id="40de9-496">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="40de9-497">测试该应用是否为该数据库的种子。</span><span class="sxs-lookup"><span data-stu-id="40de9-497">Test that the app seeded the database.</span></span> <span data-ttu-id="40de9-498">如果 contact DB 中存在任何行，则 seed 方法不会运行。</span><span class="sxs-lookup"><span data-stu-id="40de9-498">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="40de9-499">其他资源</span><span class="sxs-lookup"><span data-stu-id="40de9-499">Additional resources</span></span>

* [<span data-ttu-id="40de9-500">在 Azure 应用服务中生成 .NET Core 和 SQL 数据库 Web 应用</span><span class="sxs-lookup"><span data-stu-id="40de9-500">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="40de9-501">[ASP.NET Core 授权实验室](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="40de9-501">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="40de9-502">此实验室更详细地介绍了本教程中所介绍的安全功能。</span><span class="sxs-lookup"><span data-stu-id="40de9-502">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="40de9-503">自定义基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="40de9-503">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
