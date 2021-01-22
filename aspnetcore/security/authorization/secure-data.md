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
ms.openlocfilehash: ebd3c0dc9baa63b30f142773d7a3d621ce4082d9
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689300"
---
# <a name="create-an-aspnet-core-web-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="d8bc9-104">使用受授权保护的用户数据创建 ASP.NET Core web 应用</span><span class="sxs-lookup"><span data-stu-id="d8bc9-104">Create an ASP.NET Core web app with user data protected by authorization</span></span>

<span data-ttu-id="d8bc9-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="d8bc9-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="d8bc9-106">查看 [此 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="d8bc9-106">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d8bc9-107">本教程演示如何创建 ASP.NET Core 的 web 应用，其中包含由授权保护的用户数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-107">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="d8bc9-108">它将显示已进行身份验证 (已创建的已注册) 用户的联系人列表。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-108">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="d8bc9-109">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-109">There are three security groups:</span></span>

* <span data-ttu-id="d8bc9-110">**已注册的用户** 可以查看所有已批准的数据，并可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-110">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="d8bc9-111">**经理** 可以批准或拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-111">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="d8bc9-112">只有已批准的联系人对用户可见。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-112">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="d8bc9-113">**管理员** 可以批准/拒绝和编辑/删除任何数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-113">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="d8bc9-114">此文档中的图像与最新模板并不完全匹配。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-114">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="d8bc9-115">在下图中，用户 Rick (`rick@example.com`) 已登录。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-115">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="d8bc9-116">Rick 只能查看已批准的联系人，**编辑** / **删除** / 为其联系人 **创建新** 链接。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-116">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="d8bc9-117">只有 Rick 创建的最后一条记录才会显示 " **编辑** " 和 " **删除** " 链接。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-117">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="d8bc9-118">在经理或管理员将状态更改为 "已批准" 之前，其他用户将看不到最后一条记录。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-118">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![显示已登录的 Rick 的屏幕截图](secure-data/_static/rick.png)

<span data-ttu-id="d8bc9-120">在下图中， `manager@contoso.com` 已登录，并在管理器的角色中：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-120">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![显示 manager@contoso.com 已登录的屏幕截图](secure-data/_static/manager1.png)

<span data-ttu-id="d8bc9-122">下图显示了联系人的经理详细信息视图：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-122">The following image shows the managers details view of a contact:</span></span>

![联系人的经理视图](secure-data/_static/manager.png)

<span data-ttu-id="d8bc9-124">" **批准** " 和 " **拒绝** " 按钮仅为经理和管理员显示。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-124">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="d8bc9-125">在下图中，以 `admin@contoso.com` 管理员的角色登录和：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-125">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![显示 admin@contoso.com 已登录的屏幕截图](secure-data/_static/admin.png)

<span data-ttu-id="d8bc9-127">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-127">The administrator has all privileges.</span></span> <span data-ttu-id="d8bc9-128">她可以读取/编辑/删除任何联系人并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-128">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="d8bc9-129">此应用是通过 [基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 创建的 `Contact` ：以下模型：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-129">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="d8bc9-130">该示例包含以下授权处理程序：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-130">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="d8bc9-131">`ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-131">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="d8bc9-132">`ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-132">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="d8bc9-133">`ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-133">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8bc9-134">先决条件</span><span class="sxs-lookup"><span data-stu-id="d8bc9-134">Prerequisites</span></span>

<span data-ttu-id="d8bc9-135">本教程是高级教程。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-135">This tutorial is advanced.</span></span> <span data-ttu-id="d8bc9-136">你应该熟悉：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-136">You should be familiar with:</span></span>

* [<span data-ttu-id="d8bc9-137">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8bc9-137">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="d8bc9-138">身份验证</span><span class="sxs-lookup"><span data-stu-id="d8bc9-138">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="d8bc9-139">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="d8bc9-139">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="d8bc9-140">授权</span><span class="sxs-lookup"><span data-stu-id="d8bc9-140">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="d8bc9-141">实体框架核心</span><span class="sxs-lookup"><span data-stu-id="d8bc9-141">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="d8bc9-142">入门和已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-142">The starter and completed app</span></span>

<span data-ttu-id="d8bc9-143">[下载](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)的应用。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-143">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="d8bc9-144">[测试](#test-the-completed-app) 已完成的应用程序，使其安全功能熟悉。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-144">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="d8bc9-145">入门应用</span><span class="sxs-lookup"><span data-stu-id="d8bc9-145">The starter app</span></span>

<span data-ttu-id="d8bc9-146">[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-146">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="d8bc9-147">运行应用程序，点击 " **ContactManager** " 链接，并验证是否可以创建、编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-147">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span> <span data-ttu-id="d8bc9-148">若要创建初学者应用，请参阅 [创建初学者应用](#create-the-starter-app)。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-148">To create the starter app, see [Create the starter app](#create-the-starter-app).</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="d8bc9-149">保护用户数据</span><span class="sxs-lookup"><span data-stu-id="d8bc9-149">Secure user data</span></span>

<span data-ttu-id="d8bc9-150">以下部分包含创建安全用户数据应用的所有主要步骤。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-150">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="d8bc9-151">你可能会发现，引用已完成的项目非常有用。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-151">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="d8bc9-152">将联系人数据与用户关联</span><span class="sxs-lookup"><span data-stu-id="d8bc9-152">Tie the contact data to the user</span></span>

<span data-ttu-id="d8bc9-153">使用 ASP.NET [Identity](xref:security/authentication/identity) 用户 ID 可确保用户能够编辑其数据，而不是其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-153">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="d8bc9-154">将 `OwnerID` 和添加 `ContactStatus` 到 `Contact` 模型：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-154">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="d8bc9-155">`OwnerID` 数据库中的表的用户 ID `AspNetUser` [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-155">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="d8bc9-156">此 `Status` 字段确定常规用户是否可查看联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-156">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="d8bc9-157">创建新的迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-157">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="d8bc9-158">将角色服务添加到 Identity</span><span class="sxs-lookup"><span data-stu-id="d8bc9-158">Add Role services to Identity</span></span>

<span data-ttu-id="d8bc9-159">追加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 以添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-159">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

<a name="rau"></a>

### <a name="require-authenticated-users"></a><span data-ttu-id="d8bc9-160">需要经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="d8bc9-160">Require authenticated users</span></span>

<span data-ttu-id="d8bc9-161">设置后备身份验证策略，要求对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-161">Set the fallback authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=13-99)]

<span data-ttu-id="d8bc9-162">前面突出显示的代码设置了 [后备身份验证策略](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy)。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-162">The preceding highlighted code sets the [fallback authentication policy](xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.FallbackPolicy).</span></span> <span data-ttu-id="d8bc9-163">回退身份验证策略要求 \**_所有_* _ 用户进行身份验证，但 Razor 页面、控制器或操作方法除外。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-163">The fallback authentication policy requires \**_all_* _ users to be authenticated, except for Razor Pages, controllers, or action methods with an authentication attribute.</span></span> <span data-ttu-id="d8bc9-164">例如， Razor 使用或的页、控制器或操作方法 `[AllowAnonymous]` `[Authorize(PolicyName="MyPolicy")]` 使用应用的身份验证属性而不是后备身份验证策略。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-164">For example, Razor Pages, controllers, or action methods with `[AllowAnonymous]` or `[Authorize(PolicyName="MyPolicy")]` use the applied authentication attribute rather than the fallback authentication policy.</span></span>

<span data-ttu-id="d8bc9-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> 添加 <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> 到当前实例，这将强制对当前用户进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-165"><xref:Microsoft.AspNetCore.Authorization.AuthorizationPolicyBuilder.RequireAuthenticatedUser%2A> adds <xref:Microsoft.AspNetCore.Authorization.Infrastructure.DenyAnonymousAuthorizationRequirement> to the current instance, which enforces that the current user is authenticated.</span></span>

<span data-ttu-id="d8bc9-166">回退身份验证策略：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-166">The fallback authentication policy:</span></span>

<span data-ttu-id="d8bc9-167">_ 适用于所有未显式指定身份验证策略的请求。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-167">_ Is applied to all requests that do not explicitly specify an authentication policy.</span></span> <span data-ttu-id="d8bc9-168">对于终结点路由服务的请求，这将包括未指定授权属性的任何终结点。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-168">For requests served by endpoint routing, this would include any endpoint that does not specify an authorization attribute.</span></span> <span data-ttu-id="d8bc9-169">对于在授权中间件之后由其他中间件提供服务的请求，例如 [静态文件](xref:fundamentals/static-files)，这会将该策略应用到所有请求。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-169">For requests served by other middleware after the authorization middleware, such as [static files](xref:fundamentals/static-files), this would apply the policy to all requests.</span></span>

<span data-ttu-id="d8bc9-170">将后备身份验证策略设置为 "要求用户进行身份验证" 可保护新添加的 Razor 页面和控制器。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-170">Setting the fallback authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="d8bc9-171">默认情况下，需要进行身份验证比依赖新控制器和 Razor 页面以包括属性更安全 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-171">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="d8bc9-172"><xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions>类还包含 <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType> 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-172">The <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions> class also contains <xref:Microsoft.AspNetCore.Authorization.AuthorizationOptions.DefaultPolicy?displayProperty=nameWithType>.</span></span> <span data-ttu-id="d8bc9-173">`DefaultPolicy` `[Authorize]` 当未指定策略时，是与特性一起使用的策略。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-173">The `DefaultPolicy` is the policy used with the `[Authorize]` attribute when no policy is specified.</span></span> <span data-ttu-id="d8bc9-174">`[Authorize]` 不包含命名策略，与不同 `[Authorize(PolicyName="MyPolicy")]` 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-174">`[Authorize]` doesn't contain a named policy, unlike `[Authorize(PolicyName="MyPolicy")]`.</span></span>

<span data-ttu-id="d8bc9-175">有关策略的详细信息，请参阅 <xref:security/authorization/policies> 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-175">For more information on policies, see <xref:security/authorization/policies>.</span></span>

<span data-ttu-id="d8bc9-176">MVC 控制器和 Razor 页面要求对所有用户进行身份验证的另一种方法是添加授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-176">An alternative way for MVC controllers and Razor Pages to require all users be authenticated is adding an authorization filter:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup2.cs?name=snippet&highlight=14-99)]

<span data-ttu-id="d8bc9-177">前面的代码使用授权筛选器，设置回退策略使用终结点路由。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-177">The preceding code uses an authorization filter, setting the fallback policy uses endpoint routing.</span></span> <span data-ttu-id="d8bc9-178">设置回退策略是要求对所有用户进行身份验证的首选方式。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-178">Setting the fallback policy is the preferred way to require all users be authenticated.</span></span>

<span data-ttu-id="d8bc9-179">将 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 添加到 `Index` 和 `Privacy` 页面，以便匿名用户在注册之前可以获取有关站点的信息：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-179">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the `Index` and `Privacy` pages so anonymous users can get information about the site before they register:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="d8bc9-180">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="d8bc9-180">Configure the test account</span></span>

<span data-ttu-id="d8bc9-181">`SeedData`类创建两个帐户：管理员和管理器。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-181">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="d8bc9-182">使用 [机密管理器工具](xref:security/app-secrets) 来设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-182">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="d8bc9-183">将项目目录中的密码设置 (包含 *Program.cs*) 的目录：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-183">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="d8bc9-184">如果未指定强密码，则在调用时会引发异常 `SeedData.Initialize` 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-184">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="d8bc9-185">更新 `Main` 以使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-185">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="d8bc9-186">创建测试帐户并更新联系人</span><span class="sxs-lookup"><span data-stu-id="d8bc9-186">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="d8bc9-187">更新 `Initialize` 类中的方法 `SeedData` ，以创建测试帐户：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-187">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="d8bc9-188">向联系人添加管理员用户 ID 和 `ContactStatus` 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-188">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="d8bc9-189">使其中一个联系人 "已提交" 和一个 "已拒绝"。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-189">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="d8bc9-190">将用户 ID 和状态添加到所有联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-190">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="d8bc9-191">只显示一个联系人：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-191">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="d8bc9-192">创建所有者、经理和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-192">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="d8bc9-193">`ContactIsOwnerAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-193">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d8bc9-194">`ContactIsOwnerAuthorizationHandler`验证对资源的用户是否拥有该资源。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-194">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="d8bc9-195">`ContactIsOwnerAuthorizationHandler`调用[上下文。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果当前经过身份验证的用户是联系人所有者，则会成功。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-195">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="d8bc9-196">授权处理程序通常：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-196">Authorization handlers generally:</span></span>

* <span data-ttu-id="d8bc9-197">`context.Succeed`满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-197">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="d8bc9-198">`Task.CompletedTask`当不满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-198">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="d8bc9-199">`Task.CompletedTask` 不是成功或失败， &mdash; 它允许其他授权处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-199">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="d8bc9-200">如果需要显式失败，请返回 [context。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-200">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="d8bc9-201">该应用程序允许联系人所有者编辑/删除/创建他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-201">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="d8bc9-202">`ContactIsOwnerAuthorizationHandler` 不需要检查在要求参数中传递的操作。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-202">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="d8bc9-203">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-203">Create a manager authorization handler</span></span>

<span data-ttu-id="d8bc9-204">`ContactManagerAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-204">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d8bc9-205">`ContactManagerAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-205">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="d8bc9-206">只有管理人员才能 (新的或更改的) 批准或拒绝内容更改。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-206">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="d8bc9-207">创建管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-207">Create an administrator authorization handler</span></span>

<span data-ttu-id="d8bc9-208">`ContactAdministratorsAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-208">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d8bc9-209">`ContactAdministratorsAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-209">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="d8bc9-210">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-210">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="d8bc9-211">注册授权处理程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-211">Register the authorization handlers</span></span>

<span data-ttu-id="d8bc9-212">Entity Framework Core 使用 AddScoped 的服务必须使用[](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)注册以进行[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-212">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="d8bc9-213">`ContactIsOwnerAuthorizationHandler`使用 [Identity](xref:security/authentication/identity) 在 Entity Framework Core 上构建 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-213">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="d8bc9-214">向服务集合注册处理程序，使其可 `ContactsController` 通过 [依赖关系注入](xref:fundamentals/dependency-injection)获得。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-214">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d8bc9-215">将以下代码添加到的末尾 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-215">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="d8bc9-216">`ContactAdministratorsAuthorizationHandler` 和 `ContactManagerAuthorizationHandler` 将添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-216">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="d8bc9-217">它们是单一实例的，因为它们不使用 EF，并且所需的所有信息都在 `Context` 方法的参数中 `HandleRequirementAsync` 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-217">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="d8bc9-218">支持授权</span><span class="sxs-lookup"><span data-stu-id="d8bc9-218">Support authorization</span></span>

<span data-ttu-id="d8bc9-219">在本部分中，将更新 Razor 页面并添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-219">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="d8bc9-220">查看联系操作要求类</span><span class="sxs-lookup"><span data-stu-id="d8bc9-220">Review the contact operations requirements class</span></span>

<span data-ttu-id="d8bc9-221">查看 `ContactOperations` 类。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-221">Review the `ContactOperations` class.</span></span> <span data-ttu-id="d8bc9-222">此类包含应用支持的要求：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-222">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="d8bc9-223">为联系人页创建基类 Razor</span><span class="sxs-lookup"><span data-stu-id="d8bc9-223">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="d8bc9-224">创建一个包含联系人页中使用的服务的基类 Razor 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-224">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="d8bc9-225">基类将初始化代码放在一个位置：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-225">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="d8bc9-226">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-226">The preceding code:</span></span>

* <span data-ttu-id="d8bc9-227">添加 `IAuthorizationService` 服务以访问授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-227">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="d8bc9-228">添加 Identity `UserManager` 服务。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-228">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="d8bc9-229">添加 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-229">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="d8bc9-230">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="d8bc9-230">Update the CreateModel</span></span>

<span data-ttu-id="d8bc9-231">更新 "创建页模型" 构造函数以使用 `DI_BasePageModel` 基类：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-231">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="d8bc9-232">将 `CreateModel.OnPostAsync` 方法更新为：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-232">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="d8bc9-233">将用户 ID 添加到 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-233">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="d8bc9-234">调用授权处理程序以验证用户是否有权创建联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-234">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="d8bc9-235">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="d8bc9-235">Update the IndexModel</span></span>

<span data-ttu-id="d8bc9-236">更新 `OnGetAsync` 方法以便仅向一般用户显示已批准的联系人：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-236">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="d8bc9-237">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="d8bc9-237">Update the EditModel</span></span>

<span data-ttu-id="d8bc9-238">添加一个授权处理程序来验证用户是否拥有该联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-238">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="d8bc9-239">由于正在验证资源授权，因此 `[Authorize]` 属性不够。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-239">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="d8bc9-240">计算属性时，应用无法访问资源。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-240">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="d8bc9-241">基于资源的授权必须是必需的。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-241">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="d8bc9-242">如果应用有权访问该资源，则必须执行检查，方法是将其加载到页面模型中，或在处理程序本身中加载它。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-242">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="d8bc9-243">通过传入资源键，可以频繁地访问资源。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-243">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="d8bc9-244">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="d8bc9-244">Update the DeleteModel</span></span>

<span data-ttu-id="d8bc9-245">更新 "删除" 页模型，以使用授权处理程序来验证用户是否具有对联系人的 "删除" 权限。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-245">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="d8bc9-246">将授权服务注入视图</span><span class="sxs-lookup"><span data-stu-id="d8bc9-246">Inject the authorization service into the views</span></span>

<span data-ttu-id="d8bc9-247">目前，UI 会显示用户不能修改的联系人的编辑和删除链接。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-247">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="d8bc9-248">将授权服务注入 *Pages/_ViewImports cshtml* 文件，使其可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-248">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="d8bc9-249">前面的标记添加了多个 `using` 语句。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-249">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="d8bc9-250">更新 *页面/联系人/索引* 中的 "**编辑**" 和 "**删除**" 链接，以便仅为具有适当权限的用户呈现它们：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-250">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="d8bc9-251">隐藏不具有更改数据权限的用户的链接不会保护应用的安全。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-251">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="d8bc9-252">隐藏链接使应用程序更易于用户理解，只显示有效的链接。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-252">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="d8bc9-253">用户可以通过攻击生成的 Url 来对其不拥有的数据调用编辑和删除操作。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-253">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="d8bc9-254">Razor页或控制器必须强制进行访问检查以确保数据的安全。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-254">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="d8bc9-255">更新详细信息</span><span class="sxs-lookup"><span data-stu-id="d8bc9-255">Update Details</span></span>

<span data-ttu-id="d8bc9-256">更新详细信息视图，以便经理可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-256">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="d8bc9-257">更新详细信息页模型：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-257">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="d8bc9-258">在角色中添加或删除用户</span><span class="sxs-lookup"><span data-stu-id="d8bc9-258">Add or remove a user to a role</span></span>

<span data-ttu-id="d8bc9-259">有关信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/8502) ：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-259">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="d8bc9-260">正在删除用户的权限。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-260">Removing privileges from a user.</span></span> <span data-ttu-id="d8bc9-261">例如，在聊天应用中对用户进行静音。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-261">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="d8bc9-262">向用户添加特权。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-262">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="d8bc9-263">质询和禁止之间的差异</span><span class="sxs-lookup"><span data-stu-id="d8bc9-263">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="d8bc9-264">此应用将默认策略设置为 " [需要经过身份验证的用户](#rau)"。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-264">This app sets the default policy to [require authenticated users](#rau).</span></span> <span data-ttu-id="d8bc9-265">以下代码允许匿名用户。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-265">The following code allows anonymous users.</span></span> <span data-ttu-id="d8bc9-266">允许匿名用户显示质询与禁止之间的差异。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-266">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="d8bc9-267">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-267">In the preceding code:</span></span>

* <span data-ttu-id="d8bc9-268">如果用户 **未通过身份** 验证， `ChallengeResult` 则返回。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-268">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="d8bc9-269">返回后 `ChallengeResult` ，会将用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-269">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="d8bc9-270">如果用户已通过身份验证，但未获得授权， `ForbidResult` 则返回。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-270">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="d8bc9-271">返回后 `ForbidResult` ，会将用户重定向到 "拒绝访问" 页。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-271">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="d8bc9-272">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-272">Test the completed app</span></span>

<span data-ttu-id="d8bc9-273">如果尚未为种子设定用户帐户设置密码，请使用 [机密管理器工具](xref:security/app-secrets#secret-manager) 设置密码：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-273">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="d8bc9-274">选择强密码：使用八个或更多字符，并且至少使用一个大写字符、数字和符号。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-274">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="d8bc9-275">例如， `Passw0rd!` 满足强密码要求。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-275">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="d8bc9-276">从项目的文件夹中执行以下命令，其中 `<PW>` 是密码：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-276">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="d8bc9-277">如果应用有联系人：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-277">If the app has contacts:</span></span>

* <span data-ttu-id="d8bc9-278">删除表中的所有记录 `Contact` 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-278">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="d8bc9-279">重新启动应用以对数据库进行种子设定。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-279">Restart the app to seed the database.</span></span>

<span data-ttu-id="d8bc9-280">测试已完成应用程序的一种简单方法是启动三个不同的浏览器 (或 incognito/InPrivate 会话) 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-280">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="d8bc9-281">在一个浏览器中，注册新用户 (例如 `test@example.com`) 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-281">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="d8bc9-282">使用其他用户登录到每个浏览器。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-282">Sign in to each browser with a different user.</span></span> <span data-ttu-id="d8bc9-283">验证下列操作：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-283">Verify the following operations:</span></span>

* <span data-ttu-id="d8bc9-284">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-284">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="d8bc9-285">已注册的用户可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-285">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="d8bc9-286">经理可以批准/拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-286">Managers can approve/reject contact data.</span></span> <span data-ttu-id="d8bc9-287">此 `Details` 视图显示 " **批准** " 和 " **拒绝** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-287">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="d8bc9-288">管理员可以批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-288">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="d8bc9-289">用户</span><span class="sxs-lookup"><span data-stu-id="d8bc9-289">User</span></span>                | <span data-ttu-id="d8bc9-290">应用程序的种子</span><span class="sxs-lookup"><span data-stu-id="d8bc9-290">Seeded by the app</span></span> | <span data-ttu-id="d8bc9-291">选项</span><span class="sxs-lookup"><span data-stu-id="d8bc9-291">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="d8bc9-292">否</span><span class="sxs-lookup"><span data-stu-id="d8bc9-292">No</span></span>                | <span data-ttu-id="d8bc9-293">编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-293">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="d8bc9-294">是</span><span class="sxs-lookup"><span data-stu-id="d8bc9-294">Yes</span></span>               | <span data-ttu-id="d8bc9-295">批准/拒绝和编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-295">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="d8bc9-296">是</span><span class="sxs-lookup"><span data-stu-id="d8bc9-296">Yes</span></span>               | <span data-ttu-id="d8bc9-297">批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-297">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="d8bc9-298">在管理员的浏览器中创建联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-298">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="d8bc9-299">复制管理员联系人的 "删除" 和 "编辑" 的 URL。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-299">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="d8bc9-300">将这些链接粘贴到测试用户的浏览器中，以验证测试用户是否无法执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-300">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="d8bc9-301">创建初学者应用</span><span class="sxs-lookup"><span data-stu-id="d8bc9-301">Create the starter app</span></span>

* <span data-ttu-id="d8bc9-302">创建 Razor 名为 "ContactManager" 的页面应用</span><span class="sxs-lookup"><span data-stu-id="d8bc9-302">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="d8bc9-303">创建具有 **单个用户帐户** 的应用。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-303">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="d8bc9-304">将其命名为 "ContactManager"，使命名空间与该示例中使用的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-304">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="d8bc9-305">`-uld` 指定 LocalDB 而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="d8bc9-305">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="d8bc9-306">添加 *模型/联系方式*：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-306">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="d8bc9-307">基架 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-307">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="d8bc9-308">创建初始迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-308">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="d8bc9-309">如果使用命令遇到 bug `dotnet aspnet-codegenerator razorpage` ，请参阅 [此 GitHub 问题](https://github.com/aspnet/Scaffolding/issues/984)。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-309">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="d8bc9-310">更新 *Pages/Shared/_Layout cshtml* 文件中的 **ContactManager** 定位点：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-310">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="d8bc9-311">通过创建、编辑和删除联系人来测试应用</span><span class="sxs-lookup"><span data-stu-id="d8bc9-311">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="d8bc9-312">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="d8bc9-312">Seed the database</span></span>

<span data-ttu-id="d8bc9-313">将 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) 类添加到 *Data* 文件夹：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-313">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="d8bc9-314">调用 `SeedData.Initialize` 自 `Main` ：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-314">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="d8bc9-315">测试该应用是否为该数据库的种子。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-315">Test that the app seeded the database.</span></span> <span data-ttu-id="d8bc9-316">如果 contact DB 中存在任何行，则 seed 方法不会运行。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-316">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="d8bc9-317">本教程演示如何创建 ASP.NET Core 的 web 应用，其中包含由授权保护的用户数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-317">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="d8bc9-318">它将显示已进行身份验证 (已创建的已注册) 用户的联系人列表。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-318">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="d8bc9-319">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-319">There are three security groups:</span></span>

* <span data-ttu-id="d8bc9-320">**已注册的用户** 可以查看所有已批准的数据，并可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-320">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="d8bc9-321">**经理** 可以批准或拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-321">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="d8bc9-322">只有已批准的联系人对用户可见。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-322">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="d8bc9-323">**管理员** 可以批准/拒绝和编辑/删除任何数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-323">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="d8bc9-324">在下图中，用户 Rick (`rick@example.com`) 已登录。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-324">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="d8bc9-325">Rick 只能查看已批准的联系人，**编辑** / **删除** / 为其联系人 **创建新** 链接。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-325">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="d8bc9-326">只有 Rick 创建的最后一条记录才会显示 " **编辑** " 和 " **删除** " 链接。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-326">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="d8bc9-327">在经理或管理员将状态更改为 "已批准" 之前，其他用户将看不到最后一条记录。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-327">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![显示已登录的 Rick 的屏幕截图](secure-data/_static/rick.png)

<span data-ttu-id="d8bc9-329">在下图中， `manager@contoso.com` 已登录，并在管理器的角色中：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-329">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![显示 manager@contoso.com 已登录的屏幕截图](secure-data/_static/manager1.png)

<span data-ttu-id="d8bc9-331">下图显示了联系人的经理详细信息视图：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-331">The following image shows the managers details view of a contact:</span></span>

![联系人的经理视图](secure-data/_static/manager.png)

<span data-ttu-id="d8bc9-333">" **批准** " 和 " **拒绝** " 按钮仅为经理和管理员显示。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-333">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="d8bc9-334">在下图中，以 `admin@contoso.com` 管理员的角色登录和：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-334">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![显示 admin@contoso.com 已登录的屏幕截图](secure-data/_static/admin.png)

<span data-ttu-id="d8bc9-336">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-336">The administrator has all privileges.</span></span> <span data-ttu-id="d8bc9-337">她可以读取/编辑/删除任何联系人并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-337">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="d8bc9-338">此应用是通过 [基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) 创建的 `Contact` ：以下模型：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-338">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="d8bc9-339">该示例包含以下授权处理程序：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-339">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="d8bc9-340">`ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-340">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="d8bc9-341">`ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-341">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="d8bc9-342">`ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-342">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="d8bc9-343">先决条件</span><span class="sxs-lookup"><span data-stu-id="d8bc9-343">Prerequisites</span></span>

<span data-ttu-id="d8bc9-344">本教程是高级教程。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-344">This tutorial is advanced.</span></span> <span data-ttu-id="d8bc9-345">你应该熟悉：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-345">You should be familiar with:</span></span>

* [<span data-ttu-id="d8bc9-346">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8bc9-346">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="d8bc9-347">身份验证</span><span class="sxs-lookup"><span data-stu-id="d8bc9-347">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="d8bc9-348">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="d8bc9-348">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="d8bc9-349">授权</span><span class="sxs-lookup"><span data-stu-id="d8bc9-349">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="d8bc9-350">实体框架核心</span><span class="sxs-lookup"><span data-stu-id="d8bc9-350">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="d8bc9-351">入门和已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-351">The starter and completed app</span></span>

<span data-ttu-id="d8bc9-352">[下载](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)的应用。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-352">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="d8bc9-353">[测试](#test-the-completed-app) 已完成的应用程序，使其安全功能熟悉。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-353">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="d8bc9-354">入门应用</span><span class="sxs-lookup"><span data-stu-id="d8bc9-354">The starter app</span></span>

<span data-ttu-id="d8bc9-355">[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-355">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="d8bc9-356">运行应用程序，点击 " **ContactManager** " 链接，并验证是否可以创建、编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-356">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="d8bc9-357">保护用户数据</span><span class="sxs-lookup"><span data-stu-id="d8bc9-357">Secure user data</span></span>

<span data-ttu-id="d8bc9-358">以下部分包含创建安全用户数据应用的所有主要步骤。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-358">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="d8bc9-359">你可能会发现，引用已完成的项目非常有用。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-359">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="d8bc9-360">将联系人数据与用户关联</span><span class="sxs-lookup"><span data-stu-id="d8bc9-360">Tie the contact data to the user</span></span>

<span data-ttu-id="d8bc9-361">使用 ASP.NET [Identity](xref:security/authentication/identity) 用户 ID 可确保用户能够编辑其数据，而不是其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-361">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="d8bc9-362">将 `OwnerID` 和添加 `ContactStatus` 到 `Contact` 模型：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-362">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="d8bc9-363">`OwnerID` 数据库中的表的用户 ID `AspNetUser` [Identity](xref:security/authentication/identity) 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-363">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="d8bc9-364">此 `Status` 字段确定常规用户是否可查看联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-364">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="d8bc9-365">创建新的迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-365">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-no-locidentity"></a><span data-ttu-id="d8bc9-366">将角色服务添加到 Identity</span><span class="sxs-lookup"><span data-stu-id="d8bc9-366">Add Role services to Identity</span></span>

<span data-ttu-id="d8bc9-367">追加 [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) 以添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-367">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=11)]

### <a name="require-authenticated-users"></a><span data-ttu-id="d8bc9-368">需要经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="d8bc9-368">Require authenticated users</span></span>

<span data-ttu-id="d8bc9-369">将默认的 "身份验证策略" 设置为 "要求用户进行身份验证"：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-369">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="d8bc9-370">您可以 Razor 通过属性在页、控制器或操作方法级别选择不进行身份验证 `[AllowAnonymous]` 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-370">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="d8bc9-371">将默认身份验证策略设置为 "要求用户进行身份验证" 可保护新添加的 Razor 页面和控制器。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-371">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="d8bc9-372">默认情况下，需要进行身份验证比依赖新控制器和 Razor 页面以包括属性更安全 `[Authorize]` 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-372">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="d8bc9-373">将 [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) 添加到 "索引"、"关于" 和 "联系人" 页，以便匿名用户在注册之前可以获取有关站点的信息。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-373">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="d8bc9-374">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="d8bc9-374">Configure the test account</span></span>

<span data-ttu-id="d8bc9-375">`SeedData`类创建两个帐户：管理员和管理器。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-375">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="d8bc9-376">使用 [机密管理器工具](xref:security/app-secrets) 来设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-376">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="d8bc9-377">将项目目录中的密码设置 (包含 *Program.cs*) 的目录：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-377">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="d8bc9-378">如果未指定强密码，则在调用时会引发异常 `SeedData.Initialize` 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-378">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="d8bc9-379">更新 `Main` 以使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-379">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="d8bc9-380">创建测试帐户并更新联系人</span><span class="sxs-lookup"><span data-stu-id="d8bc9-380">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="d8bc9-381">更新 `Initialize` 类中的方法 `SeedData` ，以创建测试帐户：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-381">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="d8bc9-382">向联系人添加管理员用户 ID 和 `ContactStatus` 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-382">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="d8bc9-383">使其中一个联系人 "已提交" 和一个 "已拒绝"。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-383">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="d8bc9-384">将用户 ID 和状态添加到所有联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-384">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="d8bc9-385">只显示一个联系人：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-385">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="d8bc9-386">创建所有者、经理和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-386">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="d8bc9-387">创建一个 *授权* 文件夹并 `ContactIsOwnerAuthorizationHandler` 在其中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-387">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="d8bc9-388">`ContactIsOwnerAuthorizationHandler`验证对资源的用户是否拥有该资源。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-388">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="d8bc9-389">`ContactIsOwnerAuthorizationHandler`调用[上下文。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果当前经过身份验证的用户是联系人所有者，则会成功。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-389">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="d8bc9-390">授权处理程序通常：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-390">Authorization handlers generally:</span></span>

* <span data-ttu-id="d8bc9-391">`context.Succeed`满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-391">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="d8bc9-392">`Task.CompletedTask`当不满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-392">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="d8bc9-393">`Task.CompletedTask` 不是成功或失败， &mdash; 它允许其他授权处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-393">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="d8bc9-394">如果需要显式失败，请返回 [context。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-394">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="d8bc9-395">该应用程序允许联系人所有者编辑/删除/创建他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-395">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="d8bc9-396">`ContactIsOwnerAuthorizationHandler` 不需要检查在要求参数中传递的操作。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-396">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="d8bc9-397">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-397">Create a manager authorization handler</span></span>

<span data-ttu-id="d8bc9-398">`ContactManagerAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-398">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d8bc9-399">`ContactManagerAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-399">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="d8bc9-400">只有管理人员才能 (新的或更改的) 批准或拒绝内容更改。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-400">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="d8bc9-401">创建管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-401">Create an administrator authorization handler</span></span>

<span data-ttu-id="d8bc9-402">`ContactAdministratorsAuthorizationHandler`在 *Authorization* 文件夹中创建一个类。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-402">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="d8bc9-403">`ContactAdministratorsAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-403">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="d8bc9-404">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-404">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="d8bc9-405">注册授权处理程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-405">Register the authorization handlers</span></span>

<span data-ttu-id="d8bc9-406">Entity Framework Core 使用 AddScoped 的服务必须使用[](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)注册以进行[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-406">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="d8bc9-407">`ContactIsOwnerAuthorizationHandler`使用 [Identity](xref:security/authentication/identity) 在 Entity Framework Core 上构建 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-407">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="d8bc9-408">向服务集合注册处理程序，使其可 `ContactsController` 通过 [依赖关系注入](xref:fundamentals/dependency-injection)获得。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-408">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d8bc9-409">将以下代码添加到的末尾 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-409">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="d8bc9-410">`ContactAdministratorsAuthorizationHandler` 和 `ContactManagerAuthorizationHandler` 将添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-410">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="d8bc9-411">它们是单一实例的，因为它们不使用 EF，并且所需的所有信息都在 `Context` 方法的参数中 `HandleRequirementAsync` 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-411">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="d8bc9-412">支持授权</span><span class="sxs-lookup"><span data-stu-id="d8bc9-412">Support authorization</span></span>

<span data-ttu-id="d8bc9-413">在本部分中，将更新 Razor 页面并添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-413">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="d8bc9-414">查看联系操作要求类</span><span class="sxs-lookup"><span data-stu-id="d8bc9-414">Review the contact operations requirements class</span></span>

<span data-ttu-id="d8bc9-415">查看 `ContactOperations` 类。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-415">Review the `ContactOperations` class.</span></span> <span data-ttu-id="d8bc9-416">此类包含应用支持的要求：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-416">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-no-locrazor-pages"></a><span data-ttu-id="d8bc9-417">为联系人页创建基类 Razor</span><span class="sxs-lookup"><span data-stu-id="d8bc9-417">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="d8bc9-418">创建一个包含联系人页中使用的服务的基类 Razor 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-418">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="d8bc9-419">基类将初始化代码放在一个位置：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-419">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="d8bc9-420">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-420">The preceding code:</span></span>

* <span data-ttu-id="d8bc9-421">添加 `IAuthorizationService` 服务以访问授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-421">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="d8bc9-422">添加 Identity `UserManager` 服务。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-422">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="d8bc9-423">添加 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-423">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="d8bc9-424">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="d8bc9-424">Update the CreateModel</span></span>

<span data-ttu-id="d8bc9-425">更新 "创建页模型" 构造函数以使用 `DI_BasePageModel` 基类：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-425">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="d8bc9-426">将 `CreateModel.OnPostAsync` 方法更新为：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-426">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="d8bc9-427">将用户 ID 添加到 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-427">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="d8bc9-428">调用授权处理程序以验证用户是否有权创建联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-428">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="d8bc9-429">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="d8bc9-429">Update the IndexModel</span></span>

<span data-ttu-id="d8bc9-430">更新 `OnGetAsync` 方法以便仅向一般用户显示已批准的联系人：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-430">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="d8bc9-431">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="d8bc9-431">Update the EditModel</span></span>

<span data-ttu-id="d8bc9-432">添加一个授权处理程序来验证用户是否拥有该联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-432">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="d8bc9-433">由于正在验证资源授权，因此 `[Authorize]` 属性不够。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-433">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="d8bc9-434">计算属性时，应用无法访问资源。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-434">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="d8bc9-435">基于资源的授权必须是必需的。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-435">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="d8bc9-436">如果应用有权访问该资源，则必须执行检查，方法是将其加载到页面模型中，或在处理程序本身中加载它。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-436">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="d8bc9-437">通过传入资源键，可以频繁地访问资源。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-437">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="d8bc9-438">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="d8bc9-438">Update the DeleteModel</span></span>

<span data-ttu-id="d8bc9-439">更新 "删除" 页模型，以使用授权处理程序来验证用户是否具有对联系人的 "删除" 权限。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-439">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="d8bc9-440">将授权服务注入视图</span><span class="sxs-lookup"><span data-stu-id="d8bc9-440">Inject the authorization service into the views</span></span>

<span data-ttu-id="d8bc9-441">目前，UI 会显示用户不能修改的联系人的编辑和删除链接。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-441">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="d8bc9-442">将授权服务注入到 *views/_ViewImports cshtml* 文件中，使其可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-442">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="d8bc9-443">前面的标记添加了多个 `using` 语句。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-443">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="d8bc9-444">更新 *页面/联系人/索引* 中的 "**编辑**" 和 "**删除**" 链接，以便仅为具有适当权限的用户呈现它们：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-444">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="d8bc9-445">隐藏不具有更改数据权限的用户的链接不会保护应用的安全。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-445">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="d8bc9-446">隐藏链接使应用程序更易于用户理解，只显示有效的链接。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-446">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="d8bc9-447">用户可以通过攻击生成的 Url 来对其不拥有的数据调用编辑和删除操作。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-447">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="d8bc9-448">Razor页或控制器必须强制进行访问检查以确保数据的安全。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-448">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="d8bc9-449">更新详细信息</span><span class="sxs-lookup"><span data-stu-id="d8bc9-449">Update Details</span></span>

<span data-ttu-id="d8bc9-450">更新详细信息视图，以便经理可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-450">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="d8bc9-451">更新详细信息页模型：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-451">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="d8bc9-452">在角色中添加或删除用户</span><span class="sxs-lookup"><span data-stu-id="d8bc9-452">Add or remove a user to a role</span></span>

<span data-ttu-id="d8bc9-453">有关信息，请参阅 [此问题](https://github.com/dotnet/AspNetCore.Docs/issues/8502) ：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-453">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="d8bc9-454">正在删除用户的权限。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-454">Removing privileges from a user.</span></span> <span data-ttu-id="d8bc9-455">例如，在聊天应用中对用户进行静音。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-455">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="d8bc9-456">向用户添加特权。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-456">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="d8bc9-457">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="d8bc9-457">Test the completed app</span></span>

<span data-ttu-id="d8bc9-458">如果尚未为种子设定用户帐户设置密码，请使用 [机密管理器工具](xref:security/app-secrets#secret-manager) 设置密码：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-458">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="d8bc9-459">选择强密码：使用八个或更多字符，并且至少使用一个大写字符、数字和符号。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-459">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="d8bc9-460">例如， `Passw0rd!` 满足强密码要求。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-460">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="d8bc9-461">从项目的文件夹中执行以下命令，其中 `<PW>` 是密码：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-461">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="d8bc9-462">删除和更新数据库</span><span class="sxs-lookup"><span data-stu-id="d8bc9-462">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="d8bc9-463">重新启动应用以对数据库进行种子设定。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-463">Restart the app to seed the database.</span></span>

<span data-ttu-id="d8bc9-464">测试已完成应用程序的一种简单方法是启动三个不同的浏览器 (或 incognito/InPrivate 会话) 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-464">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="d8bc9-465">在一个浏览器中，注册新用户 (例如 `test@example.com`) 。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-465">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="d8bc9-466">使用其他用户登录到每个浏览器。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-466">Sign in to each browser with a different user.</span></span> <span data-ttu-id="d8bc9-467">验证下列操作：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-467">Verify the following operations:</span></span>

* <span data-ttu-id="d8bc9-468">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-468">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="d8bc9-469">已注册的用户可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-469">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="d8bc9-470">经理可以批准/拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-470">Managers can approve/reject contact data.</span></span> <span data-ttu-id="d8bc9-471">此 `Details` 视图显示 " **批准** " 和 " **拒绝** " 按钮。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-471">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="d8bc9-472">管理员可以批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-472">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="d8bc9-473">用户</span><span class="sxs-lookup"><span data-stu-id="d8bc9-473">User</span></span>                | <span data-ttu-id="d8bc9-474">应用程序的种子</span><span class="sxs-lookup"><span data-stu-id="d8bc9-474">Seeded by the app</span></span> | <span data-ttu-id="d8bc9-475">选项</span><span class="sxs-lookup"><span data-stu-id="d8bc9-475">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="d8bc9-476">否</span><span class="sxs-lookup"><span data-stu-id="d8bc9-476">No</span></span>                | <span data-ttu-id="d8bc9-477">编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-477">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="d8bc9-478">是</span><span class="sxs-lookup"><span data-stu-id="d8bc9-478">Yes</span></span>               | <span data-ttu-id="d8bc9-479">批准/拒绝和编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-479">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="d8bc9-480">是</span><span class="sxs-lookup"><span data-stu-id="d8bc9-480">Yes</span></span>               | <span data-ttu-id="d8bc9-481">批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-481">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="d8bc9-482">在管理员的浏览器中创建联系人。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-482">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="d8bc9-483">复制管理员联系人的 "删除" 和 "编辑" 的 URL。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-483">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="d8bc9-484">将这些链接粘贴到测试用户的浏览器中，以验证测试用户是否无法执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-484">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="d8bc9-485">创建初学者应用</span><span class="sxs-lookup"><span data-stu-id="d8bc9-485">Create the starter app</span></span>

* <span data-ttu-id="d8bc9-486">创建 Razor 名为 "ContactManager" 的页面应用</span><span class="sxs-lookup"><span data-stu-id="d8bc9-486">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="d8bc9-487">创建具有 **单个用户帐户** 的应用。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-487">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="d8bc9-488">将其命名为 "ContactManager"，使命名空间与该示例中使用的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-488">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="d8bc9-489">`-uld` 指定 LocalDB 而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="d8bc9-489">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="d8bc9-490">添加 *模型/联系方式*：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-490">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="d8bc9-491">基架 `Contact` 模型。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-491">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="d8bc9-492">创建初始迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-492">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="d8bc9-493">更新 *Pages/_Layout cshtml* 文件中的 **ContactManager** 定位点：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-493">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="d8bc9-494">通过创建、编辑和删除联系人来测试应用</span><span class="sxs-lookup"><span data-stu-id="d8bc9-494">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="d8bc9-495">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="d8bc9-495">Seed the database</span></span>

<span data-ttu-id="d8bc9-496">将 [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) 类添加到 *Data* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-496">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="d8bc9-497">调用 `SeedData.Initialize` 自 `Main` ：</span><span class="sxs-lookup"><span data-stu-id="d8bc9-497">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="d8bc9-498">测试该应用是否为该数据库的种子。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-498">Test that the app seeded the database.</span></span> <span data-ttu-id="d8bc9-499">如果 contact DB 中存在任何行，则 seed 方法不会运行。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-499">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="d8bc9-500">其他资源</span><span class="sxs-lookup"><span data-stu-id="d8bc9-500">Additional resources</span></span>

* [<span data-ttu-id="d8bc9-501">在 Azure 应用服务中生成 .NET Core 和 SQL 数据库 Web 应用</span><span class="sxs-lookup"><span data-stu-id="d8bc9-501">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="d8bc9-502">[ASP.NET Core 授权实验室](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-502">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="d8bc9-503">此实验室更详细地介绍了本教程中所介绍的安全功能。</span><span class="sxs-lookup"><span data-stu-id="d8bc9-503">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="d8bc9-504">自定义基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="d8bc9-504">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
