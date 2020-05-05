---
title: 使用授权保护的用户数据创建 ASP.NET Core 应用
author: rick-anderson
description: 了解如何使用授权保护Razor的用户数据创建页面应用。 包括 HTTPS、身份验证、安全性 ASP.NET Core Identity。
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/secure-data
ms.openlocfilehash: f52b08786dde54e7dcbd2e00f43badb58879cf79
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775747"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="32115-104">使用授权保护的用户数据创建 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="32115-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="32115-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="32115-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="32115-106">请参阅[此 PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf)以获取 ASP.NET Core MVC 版本。</span><span class="sxs-lookup"><span data-stu-id="32115-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="32115-107">本教程的 ASP.NET Core 1.1 版本位于[此](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data)文件夹中。</span><span class="sxs-lookup"><span data-stu-id="32115-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="32115-108">1.1 ASP.NET Core 示例位于[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)中。</span><span class="sxs-lookup"><span data-stu-id="32115-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="32115-109">查看[此 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="32115-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="32115-110">本教程演示如何创建 ASP.NET Core 的 web 应用，其中包含由授权保护的用户数据。</span><span class="sxs-lookup"><span data-stu-id="32115-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="32115-111">它显示已验证（注册）用户已创建的联系人的列表。</span><span class="sxs-lookup"><span data-stu-id="32115-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="32115-112">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="32115-112">There are three security groups:</span></span>

* <span data-ttu-id="32115-113">**已注册的用户**可以查看所有已批准的数据，并可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="32115-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="32115-114">**经理**可以批准或拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="32115-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="32115-115">只有已批准的联系人对用户可见。</span><span class="sxs-lookup"><span data-stu-id="32115-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="32115-116">**管理员**可以批准/拒绝和编辑/删除任何数据。</span><span class="sxs-lookup"><span data-stu-id="32115-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="32115-117">此文档中的图像与最新模板并不完全匹配。</span><span class="sxs-lookup"><span data-stu-id="32115-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="32115-118">在下图中，用户 Rick （`rick@example.com`）已登录。</span><span class="sxs-lookup"><span data-stu-id="32115-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="32115-119">Rick 只能查看已批准的联系人，**编辑**/**删除**/为其联系人**创建新**链接。</span><span class="sxs-lookup"><span data-stu-id="32115-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="32115-120">只有 Rick 创建的最后一条记录才会显示 "**编辑**" 和 "**删除**" 链接。</span><span class="sxs-lookup"><span data-stu-id="32115-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="32115-121">在经理或管理员将状态更改为 "已批准" 之前，其他用户将看不到最后一条记录。</span><span class="sxs-lookup"><span data-stu-id="32115-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![显示已登录的 Rick 的屏幕截图](secure-data/_static/rick.png)

<span data-ttu-id="32115-123">在下图中， `manager@contoso.com`已登录，并在管理器的角色中：</span><span class="sxs-lookup"><span data-stu-id="32115-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![显示manager@contoso.com已登录的屏幕截图](secure-data/_static/manager1.png)

<span data-ttu-id="32115-125">下图显示了联系人的经理详细信息视图：</span><span class="sxs-lookup"><span data-stu-id="32115-125">The following image shows the managers details view of a contact:</span></span>

![联系人的经理视图](secure-data/_static/manager.png)

<span data-ttu-id="32115-127">"**批准**" 和 "**拒绝**" 按钮仅为经理和管理员显示。</span><span class="sxs-lookup"><span data-stu-id="32115-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="32115-128">在下图中， `admin@contoso.com`以管理员的角色登录和：</span><span class="sxs-lookup"><span data-stu-id="32115-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![显示admin@contoso.com已登录的屏幕截图](secure-data/_static/admin.png)

<span data-ttu-id="32115-130">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="32115-130">The administrator has all privileges.</span></span> <span data-ttu-id="32115-131">她可以读取/编辑/删除任何联系人并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="32115-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="32115-132">此应用是通过[基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)创建的`Contact` ：以下模型：</span><span class="sxs-lookup"><span data-stu-id="32115-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="32115-133">该示例包含以下授权处理程序：</span><span class="sxs-lookup"><span data-stu-id="32115-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="32115-134">`ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="32115-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="32115-135">`ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="32115-136">`ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="32115-137">先决条件</span><span class="sxs-lookup"><span data-stu-id="32115-137">Prerequisites</span></span>

<span data-ttu-id="32115-138">本教程是高级教程。</span><span class="sxs-lookup"><span data-stu-id="32115-138">This tutorial is advanced.</span></span> <span data-ttu-id="32115-139">你应该熟悉：</span><span class="sxs-lookup"><span data-stu-id="32115-139">You should be familiar with:</span></span>

* [<span data-ttu-id="32115-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="32115-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="32115-141">身份验证</span><span class="sxs-lookup"><span data-stu-id="32115-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="32115-142">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="32115-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="32115-143">授权</span><span class="sxs-lookup"><span data-stu-id="32115-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="32115-144">实体框架核心</span><span class="sxs-lookup"><span data-stu-id="32115-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="32115-145">入门和已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="32115-145">The starter and completed app</span></span>

<span data-ttu-id="32115-146">[下载](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)的应用。</span><span class="sxs-lookup"><span data-stu-id="32115-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="32115-147">[测试](#test-the-completed-app)已完成的应用程序，使其安全功能熟悉。</span><span class="sxs-lookup"><span data-stu-id="32115-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="32115-148">入门应用</span><span class="sxs-lookup"><span data-stu-id="32115-148">The starter app</span></span>

<span data-ttu-id="32115-149">[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。</span><span class="sxs-lookup"><span data-stu-id="32115-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="32115-150">运行应用程序，点击 " **ContactManager** " 链接，并验证是否可以创建、编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="32115-151">保护用户数据</span><span class="sxs-lookup"><span data-stu-id="32115-151">Secure user data</span></span>

<span data-ttu-id="32115-152">以下部分包含创建安全用户数据应用的所有主要步骤。</span><span class="sxs-lookup"><span data-stu-id="32115-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="32115-153">你可能会发现，引用已完成的项目非常有用。</span><span class="sxs-lookup"><span data-stu-id="32115-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="32115-154">将联系人数据与用户关联</span><span class="sxs-lookup"><span data-stu-id="32115-154">Tie the contact data to the user</span></span>

<span data-ttu-id="32115-155">使用 ASP.NET [Identity](xref:security/authentication/identity) user ID 可确保用户能够编辑其数据，而不是其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="32115-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="32115-156">将`OwnerID`和`ContactStatus`添加到`Contact`模型：</span><span class="sxs-lookup"><span data-stu-id="32115-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="32115-157">`OwnerID``AspNetUser` [标识](xref:security/authentication/identity)数据库中的表的用户 ID。</span><span class="sxs-lookup"><span data-stu-id="32115-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="32115-158">此`Status`字段确定常规用户是否可查看联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="32115-159">创建新的迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="32115-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="32115-160">将角色服务添加到标识</span><span class="sxs-lookup"><span data-stu-id="32115-160">Add Role services to Identity</span></span>

<span data-ttu-id="32115-161">追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)以添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="32115-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="32115-162">需要经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="32115-162">Require authenticated users</span></span>

<span data-ttu-id="32115-163">将默认的 "身份验证策略" 设置为 "要求用户进行身份验证"：</span><span class="sxs-lookup"><span data-stu-id="32115-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="32115-164">您可以通过`[AllowAnonymous]`属性在 Razor 页、控制器或操作方法级别选择不进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="32115-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="32115-165">将默认身份验证策略设置为 "要求用户进行身份验证" 可保护新添加的 Razor Pages 和控制器。</span><span class="sxs-lookup"><span data-stu-id="32115-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="32115-166">默认情况下，需要进行身份验证比依赖新控制器和 Razor Pages 来包括`[Authorize]`属性更安全。</span><span class="sxs-lookup"><span data-stu-id="32115-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="32115-167">将[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)添加到 "索引" 和 "隐私" 页，以便匿名用户在注册之前可以获取有关站点的信息。</span><span class="sxs-lookup"><span data-stu-id="32115-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="32115-168">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="32115-168">Configure the test account</span></span>

<span data-ttu-id="32115-169">`SeedData`类创建两个帐户：管理员和管理器。</span><span class="sxs-lookup"><span data-stu-id="32115-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="32115-170">使用[机密管理器工具](xref:security/app-secrets)来设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="32115-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="32115-171">从项目目录（包含*Program.cs*的目录）设置密码：</span><span class="sxs-lookup"><span data-stu-id="32115-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="32115-172">如果未指定强密码，则在`SeedData.Initialize`调用时会引发异常。</span><span class="sxs-lookup"><span data-stu-id="32115-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="32115-173">更新`Main`以使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="32115-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="32115-174">创建测试帐户并更新联系人</span><span class="sxs-lookup"><span data-stu-id="32115-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="32115-175">更新`SeedData`类`Initialize`中的方法，以创建测试帐户：</span><span class="sxs-lookup"><span data-stu-id="32115-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="32115-176">向联系人添加管理员用户 ID `ContactStatus`和。</span><span class="sxs-lookup"><span data-stu-id="32115-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="32115-177">使其中一个联系人 "已提交" 和一个 "已拒绝"。</span><span class="sxs-lookup"><span data-stu-id="32115-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="32115-178">将用户 ID 和状态添加到所有联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="32115-179">只显示一个联系人：</span><span class="sxs-lookup"><span data-stu-id="32115-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="32115-180">创建所有者、经理和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="32115-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="32115-181">在 Authorization `ContactIsOwnerAuthorizationHandler`文件夹中创建*Authorization*一个类。</span><span class="sxs-lookup"><span data-stu-id="32115-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="32115-182">`ContactIsOwnerAuthorizationHandler`验证对资源的用户是否拥有该资源。</span><span class="sxs-lookup"><span data-stu-id="32115-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="32115-183">`ContactIsOwnerAuthorizationHandler`调用[上下文。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果当前经过身份验证的用户是联系人所有者，则会成功。</span><span class="sxs-lookup"><span data-stu-id="32115-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="32115-184">授权处理程序通常：</span><span class="sxs-lookup"><span data-stu-id="32115-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="32115-185">满足`context.Succeed`要求时返回。</span><span class="sxs-lookup"><span data-stu-id="32115-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="32115-186">当`Task.CompletedTask`不满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="32115-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="32115-187">`Task.CompletedTask`不是成功或失败&mdash;，它允许其他授权处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="32115-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="32115-188">如果需要显式失败，请返回[context。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="32115-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="32115-189">该应用程序允许联系人所有者编辑/删除/创建他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="32115-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="32115-190">`ContactIsOwnerAuthorizationHandler`不需要检查在要求参数中传递的操作。</span><span class="sxs-lookup"><span data-stu-id="32115-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="32115-191">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="32115-191">Create a manager authorization handler</span></span>

<span data-ttu-id="32115-192">在 Authorization `ContactManagerAuthorizationHandler`文件夹中创建*Authorization*一个类。</span><span class="sxs-lookup"><span data-stu-id="32115-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="32115-193">`ContactManagerAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="32115-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="32115-194">只有经理才能批准或拒绝内容更改（新的或更改的更改）。</span><span class="sxs-lookup"><span data-stu-id="32115-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="32115-195">创建管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="32115-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="32115-196">在 Authorization `ContactAdministratorsAuthorizationHandler`文件夹中创建*Authorization*一个类。</span><span class="sxs-lookup"><span data-stu-id="32115-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="32115-197">`ContactAdministratorsAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="32115-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="32115-198">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="32115-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="32115-199">注册授权处理程序</span><span class="sxs-lookup"><span data-stu-id="32115-199">Register the authorization handlers</span></span>

<span data-ttu-id="32115-200">Entity Framework Core 使用 AddScoped 的服务必须使用[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)注册以进行[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="32115-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="32115-201">`ContactIsOwnerAuthorizationHandler`使用 Entity Framework Core 上构建 ASP.NET Core[标识](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="32115-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="32115-202">向服务集合注册处理程序，使其可`ContactsController`通过[依赖关系注入](xref:fundamentals/dependency-injection)获得。</span><span class="sxs-lookup"><span data-stu-id="32115-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="32115-203">将以下代码添加到的末尾`ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="32115-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="32115-204">`ContactAdministratorsAuthorizationHandler`和`ContactManagerAuthorizationHandler`将添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="32115-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="32115-205">它们是单一实例的，因为它们不使用 EF，并且所需的所有`Context`信息都在`HandleRequirementAsync`方法的参数中。</span><span class="sxs-lookup"><span data-stu-id="32115-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="32115-206">支持授权</span><span class="sxs-lookup"><span data-stu-id="32115-206">Support authorization</span></span>

<span data-ttu-id="32115-207">在本部分中，将更新 Razor Pages 并添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="32115-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="32115-208">查看联系操作要求类</span><span class="sxs-lookup"><span data-stu-id="32115-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="32115-209">查看`ContactOperations`类。</span><span class="sxs-lookup"><span data-stu-id="32115-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="32115-210">此类包含应用支持的要求：</span><span class="sxs-lookup"><span data-stu-id="32115-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="32115-211">为联系人创建基类 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="32115-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="32115-212">创建一个基类，该基类包含联系人 Razor Pages 中使用的服务。</span><span class="sxs-lookup"><span data-stu-id="32115-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="32115-213">基类将初始化代码放在一个位置：</span><span class="sxs-lookup"><span data-stu-id="32115-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="32115-214">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="32115-214">The preceding code:</span></span>

* <span data-ttu-id="32115-215">添加`IAuthorizationService`服务以访问授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="32115-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="32115-216">添加标识`UserManager`服务。</span><span class="sxs-lookup"><span data-stu-id="32115-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="32115-217">添加 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="32115-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="32115-218">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="32115-218">Update the CreateModel</span></span>

<span data-ttu-id="32115-219">更新 "创建页模型" 构造函数以`DI_BasePageModel`使用基类：</span><span class="sxs-lookup"><span data-stu-id="32115-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="32115-220">将`CreateModel.OnPostAsync`方法更新为：</span><span class="sxs-lookup"><span data-stu-id="32115-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="32115-221">将用户 ID 添加到`Contact`模型。</span><span class="sxs-lookup"><span data-stu-id="32115-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="32115-222">调用授权处理程序以验证用户是否有权创建联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="32115-223">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="32115-223">Update the IndexModel</span></span>

<span data-ttu-id="32115-224">更新`OnGetAsync`方法以便仅向一般用户显示已批准的联系人：</span><span class="sxs-lookup"><span data-stu-id="32115-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="32115-225">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="32115-225">Update the EditModel</span></span>

<span data-ttu-id="32115-226">添加一个授权处理程序来验证用户是否拥有该联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="32115-227">由于正在验证资源授权，因此`[Authorize]`属性不够。</span><span class="sxs-lookup"><span data-stu-id="32115-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="32115-228">计算属性时，应用无法访问资源。</span><span class="sxs-lookup"><span data-stu-id="32115-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="32115-229">基于资源的授权必须是必需的。</span><span class="sxs-lookup"><span data-stu-id="32115-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="32115-230">如果应用有权访问该资源，则必须执行检查，方法是将其加载到页面模型中，或在处理程序本身中加载它。</span><span class="sxs-lookup"><span data-stu-id="32115-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="32115-231">通过传入资源键，可以频繁地访问资源。</span><span class="sxs-lookup"><span data-stu-id="32115-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="32115-232">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="32115-232">Update the DeleteModel</span></span>

<span data-ttu-id="32115-233">更新 "删除" 页模型，以使用授权处理程序来验证用户是否具有对联系人的 "删除" 权限。</span><span class="sxs-lookup"><span data-stu-id="32115-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="32115-234">将授权服务注入视图</span><span class="sxs-lookup"><span data-stu-id="32115-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="32115-235">目前，UI 会显示用户不能修改的联系人的编辑和删除链接。</span><span class="sxs-lookup"><span data-stu-id="32115-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="32115-236">将授权服务注入*Pages/_ViewImports cshtml*文件，使其可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="32115-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="32115-237">前面的标记添加了`using`多个语句。</span><span class="sxs-lookup"><span data-stu-id="32115-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="32115-238">更新*页面/联系人/索引*中的 "**编辑**" 和 "**删除**" 链接，以便仅为具有适当权限的用户呈现它们：</span><span class="sxs-lookup"><span data-stu-id="32115-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="32115-239">隐藏不具有更改数据权限的用户的链接不会保护应用的安全。</span><span class="sxs-lookup"><span data-stu-id="32115-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="32115-240">隐藏链接使应用程序更易于用户理解，只显示有效的链接。</span><span class="sxs-lookup"><span data-stu-id="32115-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="32115-241">用户可以通过攻击生成的 Url 来对其不拥有的数据调用编辑和删除操作。</span><span class="sxs-lookup"><span data-stu-id="32115-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="32115-242">Razor 页或控制器必须强制进行访问检查以确保数据的安全。</span><span class="sxs-lookup"><span data-stu-id="32115-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="32115-243">更新详细信息</span><span class="sxs-lookup"><span data-stu-id="32115-243">Update Details</span></span>

<span data-ttu-id="32115-244">更新详细信息视图，以便经理可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="32115-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="32115-245">更新详细信息页模型：</span><span class="sxs-lookup"><span data-stu-id="32115-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="32115-246">在角色中添加或删除用户</span><span class="sxs-lookup"><span data-stu-id="32115-246">Add or remove a user to a role</span></span>

<span data-ttu-id="32115-247">有关信息，请参阅[此问题](https://github.com/dotnet/AspNetCore.Docs/issues/8502)：</span><span class="sxs-lookup"><span data-stu-id="32115-247">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="32115-248">正在删除用户的权限。</span><span class="sxs-lookup"><span data-stu-id="32115-248">Removing privileges from a user.</span></span> <span data-ttu-id="32115-249">例如，在聊天应用中对用户进行静音。</span><span class="sxs-lookup"><span data-stu-id="32115-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="32115-250">向用户添加特权。</span><span class="sxs-lookup"><span data-stu-id="32115-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="32115-251">质询和禁止之间的差异</span><span class="sxs-lookup"><span data-stu-id="32115-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="32115-252">此应用将默认策略设置为 "[需要经过身份验证的用户](#require-authenticated-users)"。</span><span class="sxs-lookup"><span data-stu-id="32115-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="32115-253">以下代码允许匿名用户。</span><span class="sxs-lookup"><span data-stu-id="32115-253">The following code allows anonymous users.</span></span> <span data-ttu-id="32115-254">允许匿名用户显示质询与禁止之间的差异。</span><span class="sxs-lookup"><span data-stu-id="32115-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="32115-255">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="32115-255">In the preceding code:</span></span>

* <span data-ttu-id="32115-256">如果用户**未通过身份**验证， `ChallengeResult`则返回。</span><span class="sxs-lookup"><span data-stu-id="32115-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="32115-257">`ChallengeResult`返回后，会将用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="32115-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="32115-258">如果用户已通过身份验证，但未获得授权`ForbidResult` ，则返回。</span><span class="sxs-lookup"><span data-stu-id="32115-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="32115-259">`ForbidResult`返回后，会将用户重定向到 "拒绝访问" 页。</span><span class="sxs-lookup"><span data-stu-id="32115-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="32115-260">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="32115-260">Test the completed app</span></span>

<span data-ttu-id="32115-261">如果尚未为种子设定用户帐户设置密码，请使用[机密管理器工具](xref:security/app-secrets#secret-manager)设置密码：</span><span class="sxs-lookup"><span data-stu-id="32115-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="32115-262">选择强密码：使用八个或更多字符，并且至少使用一个大写字符、数字和符号。</span><span class="sxs-lookup"><span data-stu-id="32115-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="32115-263">例如， `Passw0rd!`满足强密码要求。</span><span class="sxs-lookup"><span data-stu-id="32115-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="32115-264">从项目的文件夹中执行以下命令，其中`<PW>`是密码：</span><span class="sxs-lookup"><span data-stu-id="32115-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="32115-265">如果应用有联系人：</span><span class="sxs-lookup"><span data-stu-id="32115-265">If the app has contacts:</span></span>

* <span data-ttu-id="32115-266">删除`Contact`表中的所有记录。</span><span class="sxs-lookup"><span data-stu-id="32115-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="32115-267">重新启动应用以对数据库进行种子设定。</span><span class="sxs-lookup"><span data-stu-id="32115-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="32115-268">测试已完成应用程序的一种简单方法是启动三个不同的浏览器（或 incognito/InPrivate 会话）。</span><span class="sxs-lookup"><span data-stu-id="32115-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="32115-269">在一个浏览器中，注册新用户（例如`test@example.com`）。</span><span class="sxs-lookup"><span data-stu-id="32115-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="32115-270">使用其他用户登录到每个浏览器。</span><span class="sxs-lookup"><span data-stu-id="32115-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="32115-271">验证下列操作：</span><span class="sxs-lookup"><span data-stu-id="32115-271">Verify the following operations:</span></span>

* <span data-ttu-id="32115-272">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="32115-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="32115-273">已注册的用户可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="32115-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="32115-274">经理可以批准/拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="32115-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="32115-275">此`Details`视图显示 "**批准**" 和 "**拒绝**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="32115-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="32115-276">管理员可以批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="32115-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="32115-277">用户</span><span class="sxs-lookup"><span data-stu-id="32115-277">User</span></span>                | <span data-ttu-id="32115-278">应用程序的种子</span><span class="sxs-lookup"><span data-stu-id="32115-278">Seeded by the app</span></span> | <span data-ttu-id="32115-279">选项</span><span class="sxs-lookup"><span data-stu-id="32115-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="32115-280">否</span><span class="sxs-lookup"><span data-stu-id="32115-280">No</span></span>                | <span data-ttu-id="32115-281">编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="32115-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="32115-282">是</span><span class="sxs-lookup"><span data-stu-id="32115-282">Yes</span></span>               | <span data-ttu-id="32115-283">批准/拒绝和编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="32115-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="32115-284">是</span><span class="sxs-lookup"><span data-stu-id="32115-284">Yes</span></span>               | <span data-ttu-id="32115-285">批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="32115-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="32115-286">在管理员的浏览器中创建联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="32115-287">复制管理员联系人的 "删除" 和 "编辑" 的 URL。</span><span class="sxs-lookup"><span data-stu-id="32115-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="32115-288">将这些链接粘贴到测试用户的浏览器中，以验证测试用户是否无法执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="32115-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="32115-289">创建初学者应用</span><span class="sxs-lookup"><span data-stu-id="32115-289">Create the starter app</span></span>

* <span data-ttu-id="32115-290">创建名为 "ContactManager" 的 Razor Pages 应用</span><span class="sxs-lookup"><span data-stu-id="32115-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="32115-291">创建具有**单个用户帐户**的应用。</span><span class="sxs-lookup"><span data-stu-id="32115-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="32115-292">将其命名为 "ContactManager"，使命名空间与该示例中使用的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="32115-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="32115-293">`-uld`指定 LocalDB 而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="32115-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="32115-294">添加*模型/联系方式*：</span><span class="sxs-lookup"><span data-stu-id="32115-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="32115-295">基架`Contact`模型。</span><span class="sxs-lookup"><span data-stu-id="32115-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="32115-296">创建初始迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="32115-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="32115-297">如果使用`dotnet aspnet-codegenerator razorpage`命令遇到 bug，请参阅[此 GitHub 问题](https://github.com/aspnet/Scaffolding/issues/984)。</span><span class="sxs-lookup"><span data-stu-id="32115-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="32115-298">更新*Pages/Shared/_Layout cshtml*文件中的**ContactManager**定位点：</span><span class="sxs-lookup"><span data-stu-id="32115-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="32115-299">通过创建、编辑和删除联系人来测试应用</span><span class="sxs-lookup"><span data-stu-id="32115-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="32115-300">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="32115-300">Seed the database</span></span>

<span data-ttu-id="32115-301">将[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)类添加到*Data*文件夹：</span><span class="sxs-lookup"><span data-stu-id="32115-301">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="32115-302">调用`SeedData.Initialize`自`Main`：</span><span class="sxs-lookup"><span data-stu-id="32115-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="32115-303">测试该应用是否为该数据库的种子。</span><span class="sxs-lookup"><span data-stu-id="32115-303">Test that the app seeded the database.</span></span> <span data-ttu-id="32115-304">如果 contact DB 中存在任何行，则 seed 方法不会运行。</span><span class="sxs-lookup"><span data-stu-id="32115-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="32115-305">本教程演示如何创建 ASP.NET Core 的 web 应用，其中包含由授权保护的用户数据。</span><span class="sxs-lookup"><span data-stu-id="32115-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="32115-306">它显示已验证（注册）用户已创建的联系人的列表。</span><span class="sxs-lookup"><span data-stu-id="32115-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="32115-307">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="32115-307">There are three security groups:</span></span>

* <span data-ttu-id="32115-308">**已注册的用户**可以查看所有已批准的数据，并可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="32115-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="32115-309">**经理**可以批准或拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="32115-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="32115-310">只有已批准的联系人对用户可见。</span><span class="sxs-lookup"><span data-stu-id="32115-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="32115-311">**管理员**可以批准/拒绝和编辑/删除任何数据。</span><span class="sxs-lookup"><span data-stu-id="32115-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="32115-312">在下图中，用户 Rick （`rick@example.com`）已登录。</span><span class="sxs-lookup"><span data-stu-id="32115-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="32115-313">Rick 只能查看已批准的联系人，**编辑**/**删除**/为其联系人**创建新**链接。</span><span class="sxs-lookup"><span data-stu-id="32115-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="32115-314">只有 Rick 创建的最后一条记录才会显示 "**编辑**" 和 "**删除**" 链接。</span><span class="sxs-lookup"><span data-stu-id="32115-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="32115-315">在经理或管理员将状态更改为 "已批准" 之前，其他用户将看不到最后一条记录。</span><span class="sxs-lookup"><span data-stu-id="32115-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![显示已登录的 Rick 的屏幕截图](secure-data/_static/rick.png)

<span data-ttu-id="32115-317">在下图中， `manager@contoso.com`已登录，并在管理器的角色中：</span><span class="sxs-lookup"><span data-stu-id="32115-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![显示manager@contoso.com已登录的屏幕截图](secure-data/_static/manager1.png)

<span data-ttu-id="32115-319">下图显示了联系人的经理详细信息视图：</span><span class="sxs-lookup"><span data-stu-id="32115-319">The following image shows the managers details view of a contact:</span></span>

![联系人的经理视图](secure-data/_static/manager.png)

<span data-ttu-id="32115-321">"**批准**" 和 "**拒绝**" 按钮仅为经理和管理员显示。</span><span class="sxs-lookup"><span data-stu-id="32115-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="32115-322">在下图中， `admin@contoso.com`以管理员的角色登录和：</span><span class="sxs-lookup"><span data-stu-id="32115-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![显示admin@contoso.com已登录的屏幕截图](secure-data/_static/admin.png)

<span data-ttu-id="32115-324">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="32115-324">The administrator has all privileges.</span></span> <span data-ttu-id="32115-325">她可以读取/编辑/删除任何联系人并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="32115-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="32115-326">此应用是通过[基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)创建的`Contact` ：以下模型：</span><span class="sxs-lookup"><span data-stu-id="32115-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="32115-327">该示例包含以下授权处理程序：</span><span class="sxs-lookup"><span data-stu-id="32115-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="32115-328">`ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="32115-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="32115-329">`ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="32115-330">`ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="32115-331">先决条件</span><span class="sxs-lookup"><span data-stu-id="32115-331">Prerequisites</span></span>

<span data-ttu-id="32115-332">本教程是高级教程。</span><span class="sxs-lookup"><span data-stu-id="32115-332">This tutorial is advanced.</span></span> <span data-ttu-id="32115-333">你应该熟悉：</span><span class="sxs-lookup"><span data-stu-id="32115-333">You should be familiar with:</span></span>

* [<span data-ttu-id="32115-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="32115-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="32115-335">身份验证</span><span class="sxs-lookup"><span data-stu-id="32115-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="32115-336">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="32115-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="32115-337">授权</span><span class="sxs-lookup"><span data-stu-id="32115-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="32115-338">实体框架核心</span><span class="sxs-lookup"><span data-stu-id="32115-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="32115-339">入门和已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="32115-339">The starter and completed app</span></span>

<span data-ttu-id="32115-340">[下载](xref:index#how-to-download-a-sample)[已完成](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)的应用。</span><span class="sxs-lookup"><span data-stu-id="32115-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="32115-341">[测试](#test-the-completed-app)已完成的应用程序，使其安全功能熟悉。</span><span class="sxs-lookup"><span data-stu-id="32115-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="32115-342">入门应用</span><span class="sxs-lookup"><span data-stu-id="32115-342">The starter app</span></span>

<span data-ttu-id="32115-343">[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。</span><span class="sxs-lookup"><span data-stu-id="32115-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="32115-344">运行应用程序，点击 " **ContactManager** " 链接，并验证是否可以创建、编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="32115-345">保护用户数据</span><span class="sxs-lookup"><span data-stu-id="32115-345">Secure user data</span></span>

<span data-ttu-id="32115-346">以下部分包含创建安全用户数据应用的所有主要步骤。</span><span class="sxs-lookup"><span data-stu-id="32115-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="32115-347">你可能会发现，引用已完成的项目非常有用。</span><span class="sxs-lookup"><span data-stu-id="32115-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="32115-348">将联系人数据与用户关联</span><span class="sxs-lookup"><span data-stu-id="32115-348">Tie the contact data to the user</span></span>

<span data-ttu-id="32115-349">使用 ASP.NET [Identity](xref:security/authentication/identity) user ID 可确保用户能够编辑其数据，而不是其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="32115-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="32115-350">将`OwnerID`和`ContactStatus`添加到`Contact`模型：</span><span class="sxs-lookup"><span data-stu-id="32115-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="32115-351">`OwnerID``AspNetUser` [标识](xref:security/authentication/identity)数据库中的表的用户 ID。</span><span class="sxs-lookup"><span data-stu-id="32115-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="32115-352">此`Status`字段确定常规用户是否可查看联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="32115-353">创建新的迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="32115-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="32115-354">将角色服务添加到标识</span><span class="sxs-lookup"><span data-stu-id="32115-354">Add Role services to Identity</span></span>

<span data-ttu-id="32115-355">追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)以添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="32115-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="32115-356">需要经过身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="32115-356">Require authenticated users</span></span>

<span data-ttu-id="32115-357">将默认的 "身份验证策略" 设置为 "要求用户进行身份验证"：</span><span class="sxs-lookup"><span data-stu-id="32115-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="32115-358">您可以通过`[AllowAnonymous]`属性在 Razor 页、控制器或操作方法级别选择不进行身份验证。</span><span class="sxs-lookup"><span data-stu-id="32115-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="32115-359">将默认身份验证策略设置为 "要求用户进行身份验证" 可保护新添加的 Razor Pages 和控制器。</span><span class="sxs-lookup"><span data-stu-id="32115-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="32115-360">默认情况下，需要进行身份验证比依赖新控制器和 Razor Pages 来包括`[Authorize]`属性更安全。</span><span class="sxs-lookup"><span data-stu-id="32115-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="32115-361">将[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)添加到 "索引"、"关于" 和 "联系人" 页，以便匿名用户在注册之前可以获取有关站点的信息。</span><span class="sxs-lookup"><span data-stu-id="32115-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="32115-362">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="32115-362">Configure the test account</span></span>

<span data-ttu-id="32115-363">`SeedData`类创建两个帐户：管理员和管理器。</span><span class="sxs-lookup"><span data-stu-id="32115-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="32115-364">使用[机密管理器工具](xref:security/app-secrets)来设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="32115-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="32115-365">从项目目录（包含*Program.cs*的目录）设置密码：</span><span class="sxs-lookup"><span data-stu-id="32115-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="32115-366">如果未指定强密码，则在`SeedData.Initialize`调用时会引发异常。</span><span class="sxs-lookup"><span data-stu-id="32115-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="32115-367">更新`Main`以使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="32115-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="32115-368">创建测试帐户并更新联系人</span><span class="sxs-lookup"><span data-stu-id="32115-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="32115-369">更新`SeedData`类`Initialize`中的方法，以创建测试帐户：</span><span class="sxs-lookup"><span data-stu-id="32115-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="32115-370">向联系人添加管理员用户 ID `ContactStatus`和。</span><span class="sxs-lookup"><span data-stu-id="32115-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="32115-371">使其中一个联系人 "已提交" 和一个 "已拒绝"。</span><span class="sxs-lookup"><span data-stu-id="32115-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="32115-372">将用户 ID 和状态添加到所有联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="32115-373">只显示一个联系人：</span><span class="sxs-lookup"><span data-stu-id="32115-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="32115-374">创建所有者、经理和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="32115-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="32115-375">创建一个*授权*文件夹并在其中`ContactIsOwnerAuthorizationHandler`创建一个类。</span><span class="sxs-lookup"><span data-stu-id="32115-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="32115-376">`ContactIsOwnerAuthorizationHandler`验证对资源的用户是否拥有该资源。</span><span class="sxs-lookup"><span data-stu-id="32115-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="32115-377">`ContactIsOwnerAuthorizationHandler`调用[上下文。](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)如果当前经过身份验证的用户是联系人所有者，则会成功。</span><span class="sxs-lookup"><span data-stu-id="32115-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="32115-378">授权处理程序通常：</span><span class="sxs-lookup"><span data-stu-id="32115-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="32115-379">满足`context.Succeed`要求时返回。</span><span class="sxs-lookup"><span data-stu-id="32115-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="32115-380">当`Task.CompletedTask`不满足要求时返回。</span><span class="sxs-lookup"><span data-stu-id="32115-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="32115-381">`Task.CompletedTask`不是成功或失败&mdash;，它允许其他授权处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="32115-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="32115-382">如果需要显式失败，请返回[context。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="32115-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="32115-383">该应用程序允许联系人所有者编辑/删除/创建他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="32115-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="32115-384">`ContactIsOwnerAuthorizationHandler`不需要检查在要求参数中传递的操作。</span><span class="sxs-lookup"><span data-stu-id="32115-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="32115-385">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="32115-385">Create a manager authorization handler</span></span>

<span data-ttu-id="32115-386">在 Authorization `ContactManagerAuthorizationHandler`文件夹中创建*Authorization*一个类。</span><span class="sxs-lookup"><span data-stu-id="32115-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="32115-387">`ContactManagerAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="32115-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="32115-388">只有经理才能批准或拒绝内容更改（新的或更改的更改）。</span><span class="sxs-lookup"><span data-stu-id="32115-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="32115-389">创建管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="32115-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="32115-390">在 Authorization `ContactAdministratorsAuthorizationHandler`文件夹中创建*Authorization*一个类。</span><span class="sxs-lookup"><span data-stu-id="32115-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="32115-391">`ContactAdministratorsAuthorizationHandler`验证对资源的用户是否为管理员。</span><span class="sxs-lookup"><span data-stu-id="32115-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="32115-392">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="32115-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="32115-393">注册授权处理程序</span><span class="sxs-lookup"><span data-stu-id="32115-393">Register the authorization handlers</span></span>

<span data-ttu-id="32115-394">Entity Framework Core 使用 AddScoped 的服务必须使用[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)注册以进行[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="32115-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="32115-395">`ContactIsOwnerAuthorizationHandler`使用 Entity Framework Core 上构建 ASP.NET Core[标识](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="32115-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="32115-396">向服务集合注册处理程序，使其可`ContactsController`通过[依赖关系注入](xref:fundamentals/dependency-injection)获得。</span><span class="sxs-lookup"><span data-stu-id="32115-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="32115-397">将以下代码添加到的末尾`ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="32115-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="32115-398">`ContactAdministratorsAuthorizationHandler`和`ContactManagerAuthorizationHandler`将添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="32115-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="32115-399">它们是单一实例的，因为它们不使用 EF，并且所需的所有`Context`信息都在`HandleRequirementAsync`方法的参数中。</span><span class="sxs-lookup"><span data-stu-id="32115-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="32115-400">支持授权</span><span class="sxs-lookup"><span data-stu-id="32115-400">Support authorization</span></span>

<span data-ttu-id="32115-401">在本部分中，将更新 Razor Pages 并添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="32115-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="32115-402">查看联系操作要求类</span><span class="sxs-lookup"><span data-stu-id="32115-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="32115-403">查看`ContactOperations`类。</span><span class="sxs-lookup"><span data-stu-id="32115-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="32115-404">此类包含应用支持的要求：</span><span class="sxs-lookup"><span data-stu-id="32115-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="32115-405">为联系人创建基类 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="32115-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="32115-406">创建一个基类，该基类包含联系人 Razor Pages 中使用的服务。</span><span class="sxs-lookup"><span data-stu-id="32115-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="32115-407">基类将初始化代码放在一个位置：</span><span class="sxs-lookup"><span data-stu-id="32115-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="32115-408">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="32115-408">The preceding code:</span></span>

* <span data-ttu-id="32115-409">添加`IAuthorizationService`服务以访问授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="32115-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="32115-410">添加标识`UserManager`服务。</span><span class="sxs-lookup"><span data-stu-id="32115-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="32115-411">添加 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="32115-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="32115-412">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="32115-412">Update the CreateModel</span></span>

<span data-ttu-id="32115-413">更新 "创建页模型" 构造函数以`DI_BasePageModel`使用基类：</span><span class="sxs-lookup"><span data-stu-id="32115-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="32115-414">将`CreateModel.OnPostAsync`方法更新为：</span><span class="sxs-lookup"><span data-stu-id="32115-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="32115-415">将用户 ID 添加到`Contact`模型。</span><span class="sxs-lookup"><span data-stu-id="32115-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="32115-416">调用授权处理程序以验证用户是否有权创建联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="32115-417">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="32115-417">Update the IndexModel</span></span>

<span data-ttu-id="32115-418">更新`OnGetAsync`方法以便仅向一般用户显示已批准的联系人：</span><span class="sxs-lookup"><span data-stu-id="32115-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="32115-419">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="32115-419">Update the EditModel</span></span>

<span data-ttu-id="32115-420">添加一个授权处理程序来验证用户是否拥有该联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="32115-421">由于正在验证资源授权，因此`[Authorize]`属性不够。</span><span class="sxs-lookup"><span data-stu-id="32115-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="32115-422">计算属性时，应用无法访问资源。</span><span class="sxs-lookup"><span data-stu-id="32115-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="32115-423">基于资源的授权必须是必需的。</span><span class="sxs-lookup"><span data-stu-id="32115-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="32115-424">如果应用有权访问该资源，则必须执行检查，方法是将其加载到页面模型中，或在处理程序本身中加载它。</span><span class="sxs-lookup"><span data-stu-id="32115-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="32115-425">通过传入资源键，可以频繁地访问资源。</span><span class="sxs-lookup"><span data-stu-id="32115-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="32115-426">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="32115-426">Update the DeleteModel</span></span>

<span data-ttu-id="32115-427">更新 "删除" 页模型，以使用授权处理程序来验证用户是否具有对联系人的 "删除" 权限。</span><span class="sxs-lookup"><span data-stu-id="32115-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="32115-428">将授权服务注入视图</span><span class="sxs-lookup"><span data-stu-id="32115-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="32115-429">目前，UI 会显示用户不能修改的联系人的编辑和删除链接。</span><span class="sxs-lookup"><span data-stu-id="32115-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="32115-430">将授权服务注入到*views/_ViewImports cshtml*文件中，使其可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="32115-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="32115-431">前面的标记添加了`using`多个语句。</span><span class="sxs-lookup"><span data-stu-id="32115-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="32115-432">更新*页面/联系人/索引*中的 "**编辑**" 和 "**删除**" 链接，以便仅为具有适当权限的用户呈现它们：</span><span class="sxs-lookup"><span data-stu-id="32115-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="32115-433">隐藏不具有更改数据权限的用户的链接不会保护应用的安全。</span><span class="sxs-lookup"><span data-stu-id="32115-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="32115-434">隐藏链接使应用程序更易于用户理解，只显示有效的链接。</span><span class="sxs-lookup"><span data-stu-id="32115-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="32115-435">用户可以通过攻击生成的 Url 来对其不拥有的数据调用编辑和删除操作。</span><span class="sxs-lookup"><span data-stu-id="32115-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="32115-436">Razor 页或控制器必须强制进行访问检查以确保数据的安全。</span><span class="sxs-lookup"><span data-stu-id="32115-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="32115-437">更新详细信息</span><span class="sxs-lookup"><span data-stu-id="32115-437">Update Details</span></span>

<span data-ttu-id="32115-438">更新详细信息视图，以便经理可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="32115-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="32115-439">更新详细信息页模型：</span><span class="sxs-lookup"><span data-stu-id="32115-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="32115-440">在角色中添加或删除用户</span><span class="sxs-lookup"><span data-stu-id="32115-440">Add or remove a user to a role</span></span>

<span data-ttu-id="32115-441">有关信息，请参阅[此问题](https://github.com/dotnet/AspNetCore.Docs/issues/8502)：</span><span class="sxs-lookup"><span data-stu-id="32115-441">See [this issue](https://github.com/dotnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="32115-442">正在删除用户的权限。</span><span class="sxs-lookup"><span data-stu-id="32115-442">Removing privileges from a user.</span></span> <span data-ttu-id="32115-443">例如，在聊天应用中对用户进行静音。</span><span class="sxs-lookup"><span data-stu-id="32115-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="32115-444">向用户添加特权。</span><span class="sxs-lookup"><span data-stu-id="32115-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="32115-445">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="32115-445">Test the completed app</span></span>

<span data-ttu-id="32115-446">如果尚未为种子设定用户帐户设置密码，请使用[机密管理器工具](xref:security/app-secrets#secret-manager)设置密码：</span><span class="sxs-lookup"><span data-stu-id="32115-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="32115-447">选择强密码：使用八个或更多字符，并且至少使用一个大写字符、数字和符号。</span><span class="sxs-lookup"><span data-stu-id="32115-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="32115-448">例如， `Passw0rd!`满足强密码要求。</span><span class="sxs-lookup"><span data-stu-id="32115-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="32115-449">从项目的文件夹中执行以下命令，其中`<PW>`是密码：</span><span class="sxs-lookup"><span data-stu-id="32115-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="32115-450">删除和更新数据库</span><span class="sxs-lookup"><span data-stu-id="32115-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="32115-451">重新启动应用以对数据库进行种子设定。</span><span class="sxs-lookup"><span data-stu-id="32115-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="32115-452">测试已完成应用程序的一种简单方法是启动三个不同的浏览器（或 incognito/InPrivate 会话）。</span><span class="sxs-lookup"><span data-stu-id="32115-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="32115-453">在一个浏览器中，注册新用户（例如`test@example.com`）。</span><span class="sxs-lookup"><span data-stu-id="32115-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="32115-454">使用其他用户登录到每个浏览器。</span><span class="sxs-lookup"><span data-stu-id="32115-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="32115-455">验证下列操作：</span><span class="sxs-lookup"><span data-stu-id="32115-455">Verify the following operations:</span></span>

* <span data-ttu-id="32115-456">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="32115-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="32115-457">已注册的用户可以编辑/删除他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="32115-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="32115-458">经理可以批准/拒绝联系人数据。</span><span class="sxs-lookup"><span data-stu-id="32115-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="32115-459">此`Details`视图显示 "**批准**" 和 "**拒绝**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="32115-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="32115-460">管理员可以批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="32115-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="32115-461">用户</span><span class="sxs-lookup"><span data-stu-id="32115-461">User</span></span>                | <span data-ttu-id="32115-462">应用程序的种子</span><span class="sxs-lookup"><span data-stu-id="32115-462">Seeded by the app</span></span> | <span data-ttu-id="32115-463">选项</span><span class="sxs-lookup"><span data-stu-id="32115-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="32115-464">否</span><span class="sxs-lookup"><span data-stu-id="32115-464">No</span></span>                | <span data-ttu-id="32115-465">编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="32115-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="32115-466">是</span><span class="sxs-lookup"><span data-stu-id="32115-466">Yes</span></span>               | <span data-ttu-id="32115-467">批准/拒绝和编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="32115-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="32115-468">是</span><span class="sxs-lookup"><span data-stu-id="32115-468">Yes</span></span>               | <span data-ttu-id="32115-469">批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="32115-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="32115-470">在管理员的浏览器中创建联系人。</span><span class="sxs-lookup"><span data-stu-id="32115-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="32115-471">复制管理员联系人的 "删除" 和 "编辑" 的 URL。</span><span class="sxs-lookup"><span data-stu-id="32115-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="32115-472">将这些链接粘贴到测试用户的浏览器中，以验证测试用户是否无法执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="32115-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="32115-473">创建初学者应用</span><span class="sxs-lookup"><span data-stu-id="32115-473">Create the starter app</span></span>

* <span data-ttu-id="32115-474">创建名Razor为 "ContactManager" 的页面应用</span><span class="sxs-lookup"><span data-stu-id="32115-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="32115-475">创建具有**单个用户帐户**的应用。</span><span class="sxs-lookup"><span data-stu-id="32115-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="32115-476">将其命名为 "ContactManager"，使命名空间与该示例中使用的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="32115-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="32115-477">`-uld`指定 LocalDB 而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="32115-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="32115-478">添加*模型/联系方式*：</span><span class="sxs-lookup"><span data-stu-id="32115-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="32115-479">基架`Contact`模型。</span><span class="sxs-lookup"><span data-stu-id="32115-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="32115-480">创建初始迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="32115-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="32115-481">更新*Pages/_Layout cshtml*文件中的**ContactManager**定位点：</span><span class="sxs-lookup"><span data-stu-id="32115-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="32115-482">通过创建、编辑和删除联系人来测试应用</span><span class="sxs-lookup"><span data-stu-id="32115-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="32115-483">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="32115-483">Seed the database</span></span>

<span data-ttu-id="32115-484">将[SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)类添加到*Data*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="32115-484">Add the [SeedData](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="32115-485">调用`SeedData.Initialize`自`Main`：</span><span class="sxs-lookup"><span data-stu-id="32115-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="32115-486">测试该应用是否为该数据库的种子。</span><span class="sxs-lookup"><span data-stu-id="32115-486">Test that the app seeded the database.</span></span> <span data-ttu-id="32115-487">如果 contact DB 中存在任何行，则 seed 方法不会运行。</span><span class="sxs-lookup"><span data-stu-id="32115-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="32115-488">其他资源</span><span class="sxs-lookup"><span data-stu-id="32115-488">Additional resources</span></span>

* [<span data-ttu-id="32115-489">在 Azure 应用服务中生成 .NET Core 和 SQL 数据库 Web 应用</span><span class="sxs-lookup"><span data-stu-id="32115-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="32115-490">[ASP.NET Core 授权实验室](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="32115-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="32115-491">此实验室更详细地介绍了本教程中所介绍的安全功能。</span><span class="sxs-lookup"><span data-stu-id="32115-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="32115-492">自定义基于策略的授权</span><span class="sxs-lookup"><span data-stu-id="32115-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
