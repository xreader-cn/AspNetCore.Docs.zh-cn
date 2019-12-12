---
title: 使用受授权的用户数据创建 ASP.NET Core 应用
author: rick-anderson
description: 了解如何使用受保护的授权的用户数据创建 Razor 页面应用。 包括 HTTPS、 身份验证、 安全性、 ASP.NET Core 标识。
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 65c72d4dd457f85451796c5713bedebafec7a7de
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239836"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="336eb-104">使用受授权的用户数据创建 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="336eb-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="336eb-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="336eb-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="336eb-106">请参阅[此 PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) ASP.NET Core MVC 版本。</span><span class="sxs-lookup"><span data-stu-id="336eb-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="336eb-107">本教程的 ASP.NET Core 1.1 版本是[这](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data)文件夹。</span><span class="sxs-lookup"><span data-stu-id="336eb-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="336eb-108">ASP.NET Core 示例是在 1.1[示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)。</span><span class="sxs-lookup"><span data-stu-id="336eb-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="336eb-109">请参阅[此 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="336eb-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="336eb-110">本教程演示如何使用受授权的用户数据创建 ASP.NET Core web 应用。</span><span class="sxs-lookup"><span data-stu-id="336eb-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="336eb-111">它显示的身份验证 （注册） 的用户的联系人列表已创建。</span><span class="sxs-lookup"><span data-stu-id="336eb-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="336eb-112">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="336eb-112">There are three security groups:</span></span>

* <span data-ttu-id="336eb-113">**注册用户**可以查看所有已批准的数据还可以编辑/删除其自己的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="336eb-114">**管理器**可以批准或拒绝的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="336eb-115">仅已批准的联系人是对用户可见。</span><span class="sxs-lookup"><span data-stu-id="336eb-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="336eb-116">**管理员**可以批准/拒绝和编辑/删除的任何数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="336eb-117">此文档中的图像与最新模板并不完全匹配。</span><span class="sxs-lookup"><span data-stu-id="336eb-117">The images in this document don't exactly match the latest templates.</span></span>

<span data-ttu-id="336eb-118">在下图中，用户 Rick (`rick@example.com`) 登录。</span><span class="sxs-lookup"><span data-stu-id="336eb-118">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="336eb-119">Rick 只能查看允许的联系人和**编辑**/**删除**/**新建**其联系人的链接。</span><span class="sxs-lookup"><span data-stu-id="336eb-119">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="336eb-120">只有最后一个记录，创建由 Rick，显示**编辑**并**删除**链接。</span><span class="sxs-lookup"><span data-stu-id="336eb-120">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="336eb-121">其他用户不会看到的最后一个记录，直到经理或管理员的状态更改为"已批准"。</span><span class="sxs-lookup"><span data-stu-id="336eb-121">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![屏幕截图显示 Rick 登录](secure-data/_static/rick.png)

<span data-ttu-id="336eb-123">在下图中，`manager@contoso.com` 已登录，并在管理器的角色中：</span><span class="sxs-lookup"><span data-stu-id="336eb-123">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![屏幕截图显示manager@contoso.com登录](secure-data/_static/manager1.png)

<span data-ttu-id="336eb-125">下图显示在管理器的联系人的详细信息视图：</span><span class="sxs-lookup"><span data-stu-id="336eb-125">The following image shows the managers details view of a contact:</span></span>

![联系人的经理的视图](secure-data/_static/manager.png)

<span data-ttu-id="336eb-127">**批准**并**拒绝**按钮仅显示经理和管理员。</span><span class="sxs-lookup"><span data-stu-id="336eb-127">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="336eb-128">在下图中，`admin@contoso.com` 以管理员角色登录：</span><span class="sxs-lookup"><span data-stu-id="336eb-128">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![屏幕截图显示admin@contoso.com登录](secure-data/_static/admin.png)

<span data-ttu-id="336eb-130">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="336eb-130">The administrator has all privileges.</span></span> <span data-ttu-id="336eb-131">她可以读取、 编辑或删除任何联系，并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="336eb-131">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="336eb-132">通过创建该应用[基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)以下`Contact`模型：</span><span class="sxs-lookup"><span data-stu-id="336eb-132">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="336eb-133">此示例包含以下授权处理程序：</span><span class="sxs-lookup"><span data-stu-id="336eb-133">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="336eb-134">`ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-134">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="336eb-135">`ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-135">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="336eb-136">`ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-136">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="336eb-137">系统必备</span><span class="sxs-lookup"><span data-stu-id="336eb-137">Prerequisites</span></span>

<span data-ttu-id="336eb-138">本教程被高级。</span><span class="sxs-lookup"><span data-stu-id="336eb-138">This tutorial is advanced.</span></span> <span data-ttu-id="336eb-139">您应熟悉：</span><span class="sxs-lookup"><span data-stu-id="336eb-139">You should be familiar with:</span></span>

* [<span data-ttu-id="336eb-140">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="336eb-140">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="336eb-141">身份验证</span><span class="sxs-lookup"><span data-stu-id="336eb-141">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="336eb-142">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="336eb-142">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="336eb-143">授权</span><span class="sxs-lookup"><span data-stu-id="336eb-143">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="336eb-144">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="336eb-144">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="336eb-145">Starter 和已完成应用程序</span><span class="sxs-lookup"><span data-stu-id="336eb-145">The starter and completed app</span></span>

<span data-ttu-id="336eb-146">[下载](xref:index#how-to-download-a-sample)[完成](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)应用。</span><span class="sxs-lookup"><span data-stu-id="336eb-146">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="336eb-147">[测试](#test-the-completed-app)已完成的应用，使你熟悉其安全功能。</span><span class="sxs-lookup"><span data-stu-id="336eb-147">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="336eb-148">入门级应用</span><span class="sxs-lookup"><span data-stu-id="336eb-148">The starter app</span></span>

<span data-ttu-id="336eb-149">[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。</span><span class="sxs-lookup"><span data-stu-id="336eb-149">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="336eb-150">运行应用，点击**ContactManager**链接，并验证是否可以创建、 编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-150">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="336eb-151">保护用户数据</span><span class="sxs-lookup"><span data-stu-id="336eb-151">Secure user data</span></span>

<span data-ttu-id="336eb-152">以下部分介绍了所有主要的步骤以创建安全的用户数据应用程序。</span><span class="sxs-lookup"><span data-stu-id="336eb-152">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="336eb-153">您可能会发现引用的已完成项目很有帮助。</span><span class="sxs-lookup"><span data-stu-id="336eb-153">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="336eb-154">将绑定到用户的联系人数据</span><span class="sxs-lookup"><span data-stu-id="336eb-154">Tie the contact data to the user</span></span>

<span data-ttu-id="336eb-155">使用 ASP.NET[标识](xref:security/authentication/identity)用户 ID，以确保用户可以编辑其数据，而不是其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-155">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="336eb-156">添加`OwnerID`并`ContactStatus`到`Contact`模型：</span><span class="sxs-lookup"><span data-stu-id="336eb-156">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="336eb-157">`OwnerID` 是从用户的 ID`AspNetUser`表中[标识](xref:security/authentication/identity)数据库。</span><span class="sxs-lookup"><span data-stu-id="336eb-157">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="336eb-158">`Status`字段确定是否可由普通用户查看联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-158">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="336eb-159">创建新的迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="336eb-159">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="336eb-160">将角色服务添加到标识</span><span class="sxs-lookup"><span data-stu-id="336eb-160">Add Role services to Identity</span></span>

<span data-ttu-id="336eb-161">追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="336eb-161">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a><span data-ttu-id="336eb-162">需要身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="336eb-162">Require authenticated users</span></span>

<span data-ttu-id="336eb-163">设置默认身份验证策略以要求用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="336eb-163">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 <span data-ttu-id="336eb-164">可以选择在 Razor 页面、 控制器或操作方法级别使用的身份验证禁用`[AllowAnonymous]`属性。</span><span class="sxs-lookup"><span data-stu-id="336eb-164">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="336eb-165">设置默认身份验证策略以要求用户进行身份验证来保护新添加的 Razor 页面和控制器。</span><span class="sxs-lookup"><span data-stu-id="336eb-165">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="336eb-166">默认情况下所需的身份验证是比依赖于新的控制器和 Razor 页，以包含更安全`[Authorize]`属性。</span><span class="sxs-lookup"><span data-stu-id="336eb-166">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="336eb-167">将[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)添加到 "索引" 和 "隐私" 页，以便匿名用户在注册之前可以获取有关站点的信息。</span><span class="sxs-lookup"><span data-stu-id="336eb-167">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index and Privacy pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a><span data-ttu-id="336eb-168">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="336eb-168">Configure the test account</span></span>

<span data-ttu-id="336eb-169">`SeedData`类创建两个帐户： 管理员和管理员。</span><span class="sxs-lookup"><span data-stu-id="336eb-169">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="336eb-170">使用[机密管理器工具](xref:security/app-secrets)设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="336eb-170">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="336eb-171">从项目目录中设置密码 (目录包含*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="336eb-171">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="336eb-172">如果未指定强密码，将引发异常时`SeedData.Initialize`调用。</span><span class="sxs-lookup"><span data-stu-id="336eb-172">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="336eb-173">更新`Main`使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="336eb-173">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="336eb-174">创建测试帐户和更新联系人</span><span class="sxs-lookup"><span data-stu-id="336eb-174">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="336eb-175">更新`Initialize`中的方法`SeedData`类，以创建测试帐户：</span><span class="sxs-lookup"><span data-stu-id="336eb-175">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="336eb-176">添加管理员用户 ID 和`ContactStatus`到联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-176">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="336eb-177">先创建一个"已提交"和一个"已拒绝"的联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-177">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="336eb-178">将用户 ID 和状态添加到所有联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-178">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="336eb-179">只能有一个联系人所示：</span><span class="sxs-lookup"><span data-stu-id="336eb-179">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="336eb-180">创建所有者、 经理和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="336eb-180">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="336eb-181">创建`ContactIsOwnerAuthorizationHandler`类中*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="336eb-181">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="336eb-182">`ContactIsOwnerAuthorizationHandler`验证对资源进行操作的用户拥有的资源。</span><span class="sxs-lookup"><span data-stu-id="336eb-182">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="336eb-183">`ContactIsOwnerAuthorizationHandler`调用[上下文。成功](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)当前经过身份验证的用户是否联系所有者。</span><span class="sxs-lookup"><span data-stu-id="336eb-183">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="336eb-184">授权处理程序通常：</span><span class="sxs-lookup"><span data-stu-id="336eb-184">Authorization handlers generally:</span></span>

* <span data-ttu-id="336eb-185">返回`context.Succeed`满足的要求。</span><span class="sxs-lookup"><span data-stu-id="336eb-185">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="336eb-186">返回`Task.CompletedTask`时不符合要求。</span><span class="sxs-lookup"><span data-stu-id="336eb-186">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="336eb-187">`Task.CompletedTask` 不是成功或失败&mdash;它允许其他授权处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="336eb-187">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="336eb-188">如果你需要将显式失败，返回[上下文。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="336eb-188">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="336eb-189">应用程序允许联系所有者到编辑/删除/创建他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-189">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="336eb-190">`ContactIsOwnerAuthorizationHandler` 不需要检查要求参数中传递该操作。</span><span class="sxs-lookup"><span data-stu-id="336eb-190">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="336eb-191">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="336eb-191">Create a manager authorization handler</span></span>

<span data-ttu-id="336eb-192">创建`ContactManagerAuthorizationHandler`类中*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="336eb-192">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="336eb-193">`ContactManagerAuthorizationHandler`验证对资源进行操作的用户是管理员。</span><span class="sxs-lookup"><span data-stu-id="336eb-193">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="336eb-194">只有经理们才可以批准或拒绝内容的更改 （新的或已更改）。</span><span class="sxs-lookup"><span data-stu-id="336eb-194">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="336eb-195">创建管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="336eb-195">Create an administrator authorization handler</span></span>

<span data-ttu-id="336eb-196">创建`ContactAdministratorsAuthorizationHandler`类中*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="336eb-196">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="336eb-197">`ContactAdministratorsAuthorizationHandler`验证对资源进行操作的用户是管理员。</span><span class="sxs-lookup"><span data-stu-id="336eb-197">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="336eb-198">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="336eb-198">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="336eb-199">注册授权处理程序</span><span class="sxs-lookup"><span data-stu-id="336eb-199">Register the authorization handlers</span></span>

<span data-ttu-id="336eb-200">Entity Framework Core 使用 [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions) 的服务必须使用注册以进行[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="336eb-200">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="336eb-201">`ContactIsOwnerAuthorizationHandler`使用 ASP.NET Core[标识](xref:security/authentication/identity)，这基于实体框架核心。</span><span class="sxs-lookup"><span data-stu-id="336eb-201">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="336eb-202">注册服务集合的处理程序，以便它们可供`ContactsController`通过[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="336eb-202">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="336eb-203">将以下代码添加到末尾`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="336eb-203">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

<span data-ttu-id="336eb-204">`ContactAdministratorsAuthorizationHandler` 和`ContactManagerAuthorizationHandler`添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="336eb-204">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="336eb-205">它们是单一实例，因为它们不使用 EF 和所需的所有信息都位于`Context`参数的`HandleRequirementAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="336eb-205">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="336eb-206">支持授权</span><span class="sxs-lookup"><span data-stu-id="336eb-206">Support authorization</span></span>

<span data-ttu-id="336eb-207">在本部分中，将更新 Razor 页面和添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="336eb-207">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="336eb-208">查看联系人操作要求类</span><span class="sxs-lookup"><span data-stu-id="336eb-208">Review the contact operations requirements class</span></span>

<span data-ttu-id="336eb-209">查看`ContactOperations`类。</span><span class="sxs-lookup"><span data-stu-id="336eb-209">Review the `ContactOperations` class.</span></span> <span data-ttu-id="336eb-210">此类包含要求应用支持：</span><span class="sxs-lookup"><span data-stu-id="336eb-210">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="336eb-211">创建联系人 Razor 页面的基类</span><span class="sxs-lookup"><span data-stu-id="336eb-211">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="336eb-212">创建一个包含在联系人 Razor 页面使用的服务的基类。</span><span class="sxs-lookup"><span data-stu-id="336eb-212">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="336eb-213">基类将初始化代码放在一个位置：</span><span class="sxs-lookup"><span data-stu-id="336eb-213">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="336eb-214">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="336eb-214">The preceding code:</span></span>

* <span data-ttu-id="336eb-215">添加`IAuthorizationService`服务对授权处理程序的访问权限。</span><span class="sxs-lookup"><span data-stu-id="336eb-215">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="336eb-216">将标识添加`UserManager`服务。</span><span class="sxs-lookup"><span data-stu-id="336eb-216">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="336eb-217">添加 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="336eb-217">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="336eb-218">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="336eb-218">Update the CreateModel</span></span>

<span data-ttu-id="336eb-219">更新创建页面模型构造函数以使用`DI_BasePageModel`基类：</span><span class="sxs-lookup"><span data-stu-id="336eb-219">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="336eb-220">更新`CreateModel.OnPostAsync`方法：</span><span class="sxs-lookup"><span data-stu-id="336eb-220">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="336eb-221">添加到的用户 ID`Contact`模型。</span><span class="sxs-lookup"><span data-stu-id="336eb-221">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="336eb-222">调用授权处理程序，以验证用户有权创建联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-222">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="336eb-223">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="336eb-223">Update the IndexModel</span></span>

<span data-ttu-id="336eb-224">更新`OnGetAsync`方法，以便仅被批准的联系人显示为普通用户：</span><span class="sxs-lookup"><span data-stu-id="336eb-224">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="336eb-225">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="336eb-225">Update the EditModel</span></span>

<span data-ttu-id="336eb-226">添加授权处理程序以验证的用户拥有联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-226">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="336eb-227">正在验证资源授权，因为`[Authorize]`属性不能满足。</span><span class="sxs-lookup"><span data-stu-id="336eb-227">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="336eb-228">评估属性时，应用程序不具有对资源的访问。</span><span class="sxs-lookup"><span data-stu-id="336eb-228">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="336eb-229">基于资源的授权必须是命令性。</span><span class="sxs-lookup"><span data-stu-id="336eb-229">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="336eb-230">应用程序页面模型中加载或加载处理程序本身内获得资源的访问权限，则必须执行检查。</span><span class="sxs-lookup"><span data-stu-id="336eb-230">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="336eb-231">您经常访问的资源，通过传入的资源键。</span><span class="sxs-lookup"><span data-stu-id="336eb-231">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="336eb-232">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="336eb-232">Update the DeleteModel</span></span>

<span data-ttu-id="336eb-233">更新要使用授权处理程序来验证用户具有 delete 权限 contact 上的删除页面模型。</span><span class="sxs-lookup"><span data-stu-id="336eb-233">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="336eb-234">将授权服务注入到视图</span><span class="sxs-lookup"><span data-stu-id="336eb-234">Inject the authorization service into the views</span></span>

<span data-ttu-id="336eb-235">目前，此 UI 显示编辑和删除的用户无法修改的联系人的链接。</span><span class="sxs-lookup"><span data-stu-id="336eb-235">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="336eb-236">将授权服务注入*Pages/_ViewImports cshtml*文件，使其可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="336eb-236">Inject the authorization service in the *Pages/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="336eb-237">上述标记添加了多种`using`语句。</span><span class="sxs-lookup"><span data-stu-id="336eb-237">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="336eb-238">更新**编辑**并**删除**中的链接*Pages/Contacts/Index.cshtml*以便仅在呈现具有适当权限的用户：</span><span class="sxs-lookup"><span data-stu-id="336eb-238">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="336eb-239">隐藏用户没有权限更改的数据中的链接不会保护应用。</span><span class="sxs-lookup"><span data-stu-id="336eb-239">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="336eb-240">隐藏链接使应用更加友好的用户显示唯一有效的链接。</span><span class="sxs-lookup"><span data-stu-id="336eb-240">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="336eb-241">用户可以 hack 生成的 Url 以调用编辑和删除操作上不拥有的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-241">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="336eb-242">Razor 页面或控制器必须强制执行访问检查，以保护数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-242">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="336eb-243">更新详细信息</span><span class="sxs-lookup"><span data-stu-id="336eb-243">Update Details</span></span>

<span data-ttu-id="336eb-244">更新的详细信息视图，使管理员可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="336eb-244">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="336eb-245">更新详细信息页模型：</span><span class="sxs-lookup"><span data-stu-id="336eb-245">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="336eb-246">添加或删除角色对用户的</span><span class="sxs-lookup"><span data-stu-id="336eb-246">Add or remove a user to a role</span></span>

<span data-ttu-id="336eb-247">请参阅[本期](https://github.com/aspnet/AspNetCore.Docs/issues/8502)有关的信息：</span><span class="sxs-lookup"><span data-stu-id="336eb-247">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="336eb-248">从用户删除的特权。</span><span class="sxs-lookup"><span data-stu-id="336eb-248">Removing privileges from a user.</span></span> <span data-ttu-id="336eb-249">例如，在聊天应用中对用户进行静音。</span><span class="sxs-lookup"><span data-stu-id="336eb-249">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="336eb-250">将权限添加到用户。</span><span class="sxs-lookup"><span data-stu-id="336eb-250">Adding privileges to a user.</span></span>

<a name="challenge"></a>

## <a name="differences-between-challenge-and-forbid"></a><span data-ttu-id="336eb-251">质询和禁止之间的差异</span><span class="sxs-lookup"><span data-stu-id="336eb-251">Differences between Challenge and Forbid</span></span>

<span data-ttu-id="336eb-252">此应用将默认策略设置为 "[需要经过身份验证的用户](#require-authenticated-users)"。</span><span class="sxs-lookup"><span data-stu-id="336eb-252">This app sets the default policy to [require authenticated users](#require-authenticated-users).</span></span> <span data-ttu-id="336eb-253">以下代码允许匿名用户。</span><span class="sxs-lookup"><span data-stu-id="336eb-253">The following code allows anonymous users.</span></span> <span data-ttu-id="336eb-254">允许匿名用户显示质询与禁止之间的差异。</span><span class="sxs-lookup"><span data-stu-id="336eb-254">Anonymous users are allowed to show the differences between Challenge vs Forbid.</span></span>

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Details2.cshtml.cs?name=snippet)]

<span data-ttu-id="336eb-255">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="336eb-255">In the preceding code:</span></span>

* <span data-ttu-id="336eb-256">如果**用户未通过身份验证**，将返回 `ChallengeResult`。</span><span class="sxs-lookup"><span data-stu-id="336eb-256">When the user is **not** authenticated, a `ChallengeResult` is returned.</span></span> <span data-ttu-id="336eb-257">返回 `ChallengeResult` 后，用户将重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="336eb-257">When a `ChallengeResult` is returned, the user is redirected to the sign-in page.</span></span>
* <span data-ttu-id="336eb-258">如果用户已通过身份验证，但未获得授权，则返回 `ForbidResult`。</span><span class="sxs-lookup"><span data-stu-id="336eb-258">When the user is authenticated, but not authorized, a `ForbidResult` is returned.</span></span> <span data-ttu-id="336eb-259">返回 `ForbidResult` 后，用户将被重定向到 "拒绝访问" 页。</span><span class="sxs-lookup"><span data-stu-id="336eb-259">When a `ForbidResult` is returned, the user is redirected to the access denied page.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="336eb-260">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="336eb-260">Test the completed app</span></span>

<span data-ttu-id="336eb-261">如果你尚未设置设定为种子的用户帐户的密码，使用[机密管理器工具](xref:security/app-secrets#secret-manager)设置密码：</span><span class="sxs-lookup"><span data-stu-id="336eb-261">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="336eb-262">选择强密码：使用八个或更多字符，并且至少使用一个大写字符、数字和符号。</span><span class="sxs-lookup"><span data-stu-id="336eb-262">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="336eb-263">例如，`Passw0rd!`符合强密码要求。</span><span class="sxs-lookup"><span data-stu-id="336eb-263">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="336eb-264">执行以下命令从项目的文件夹，其中`<PW>`的密码：</span><span class="sxs-lookup"><span data-stu-id="336eb-264">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="336eb-265">如果应用了联系人：</span><span class="sxs-lookup"><span data-stu-id="336eb-265">If the app has contacts:</span></span>

* <span data-ttu-id="336eb-266">删除所有记录中`Contact`表。</span><span class="sxs-lookup"><span data-stu-id="336eb-266">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="336eb-267">重新启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="336eb-267">Restart the app to seed the database.</span></span>

<span data-ttu-id="336eb-268">测试已完成的应用程序的简单方法是启动三个不同的浏览器 （或 incognito/InPrivate 会话）。</span><span class="sxs-lookup"><span data-stu-id="336eb-268">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="336eb-269">在一个浏览器中注册一个新用户 (例如， `test@example.com`)。</span><span class="sxs-lookup"><span data-stu-id="336eb-269">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="336eb-270">登录到每个浏览器使用不同的用户。</span><span class="sxs-lookup"><span data-stu-id="336eb-270">Sign in to each browser with a different user.</span></span> <span data-ttu-id="336eb-271">验证以下操作：</span><span class="sxs-lookup"><span data-stu-id="336eb-271">Verify the following operations:</span></span>

* <span data-ttu-id="336eb-272">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-272">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="336eb-273">已注册的用户可以编辑/删除其自己的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-273">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="336eb-274">经理可以批准/拒绝的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-274">Managers can approve/reject contact data.</span></span> <span data-ttu-id="336eb-275">`Details`视图视图将显示**批准**并**拒绝**按钮。</span><span class="sxs-lookup"><span data-stu-id="336eb-275">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="336eb-276">管理员可以批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-276">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="336eb-277">用户</span><span class="sxs-lookup"><span data-stu-id="336eb-277">User</span></span>                | <span data-ttu-id="336eb-278">由应用程序进行种子设定</span><span class="sxs-lookup"><span data-stu-id="336eb-278">Seeded by the app</span></span> | <span data-ttu-id="336eb-279">选项</span><span class="sxs-lookup"><span data-stu-id="336eb-279">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="336eb-280">No</span><span class="sxs-lookup"><span data-stu-id="336eb-280">No</span></span>                | <span data-ttu-id="336eb-281">编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-281">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="336eb-282">是</span><span class="sxs-lookup"><span data-stu-id="336eb-282">Yes</span></span>               | <span data-ttu-id="336eb-283">批准/拒绝和编辑/删除拥有的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-283">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="336eb-284">是</span><span class="sxs-lookup"><span data-stu-id="336eb-284">Yes</span></span>               | <span data-ttu-id="336eb-285">批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-285">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="336eb-286">在管理员的浏览器中创建联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-286">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="336eb-287">删除 URL 复制并编辑从管理员的联系信息。</span><span class="sxs-lookup"><span data-stu-id="336eb-287">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="336eb-288">将下面的链接粘贴到测试用户的浏览器以验证测试用户不能执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="336eb-288">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="336eb-289">创建入门级应用</span><span class="sxs-lookup"><span data-stu-id="336eb-289">Create the starter app</span></span>

* <span data-ttu-id="336eb-290">创建名为"ContactManager"Razor 页面应用</span><span class="sxs-lookup"><span data-stu-id="336eb-290">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="336eb-291">创建包含应用**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="336eb-291">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="336eb-292">它命名为"ContactManager"使命名空间匹配的示例中使用的命名空间。</span><span class="sxs-lookup"><span data-stu-id="336eb-292">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="336eb-293">`-uld` 指定 LocalDB，而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="336eb-293">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="336eb-294">添加*模型/联系方式*：</span><span class="sxs-lookup"><span data-stu-id="336eb-294">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="336eb-295">基架`Contact`模型。</span><span class="sxs-lookup"><span data-stu-id="336eb-295">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="336eb-296">创建初始迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="336eb-296">Create initial migration and update the database:</span></span>

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
```

<span data-ttu-id="336eb-297">如果使用 `dotnet aspnet-codegenerator razorpage` 命令时遇到 bug，请参阅[此 GitHub 问题](https://github.com/aspnet/Scaffolding/issues/984)。</span><span class="sxs-lookup"><span data-stu-id="336eb-297">If you experience a bug with the `dotnet aspnet-codegenerator razorpage` command, see [this GitHub issue](https://github.com/aspnet/Scaffolding/issues/984).</span></span>

* <span data-ttu-id="336eb-298">更新*Pages/Shared/_Layout cshtml*文件中的**ContactManager**定位点：</span><span class="sxs-lookup"><span data-stu-id="336eb-298">Update the **ContactManager** anchor in the *Pages/Shared/_Layout.cshtml* file:</span></span>

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* <span data-ttu-id="336eb-299">测试应用程序的创建、 编辑和删除联系人</span><span class="sxs-lookup"><span data-stu-id="336eb-299">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="336eb-300">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="336eb-300">Seed the database</span></span>

<span data-ttu-id="336eb-301">将[SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)类添加到*Data*文件夹：</span><span class="sxs-lookup"><span data-stu-id="336eb-301">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs) class to the *Data* folder:</span></span>

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

<span data-ttu-id="336eb-302">调用`SeedData.Initialize`从`Main`:</span><span class="sxs-lookup"><span data-stu-id="336eb-302">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

<span data-ttu-id="336eb-303">测试应用程序设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="336eb-303">Test that the app seeded the database.</span></span> <span data-ttu-id="336eb-304">如果联系人 DB 中有任何行，则不会运行 seed 方法。</span><span class="sxs-lookup"><span data-stu-id="336eb-304">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

<span data-ttu-id="336eb-305">本教程演示如何使用受授权的用户数据创建 ASP.NET Core web 应用。</span><span class="sxs-lookup"><span data-stu-id="336eb-305">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="336eb-306">它显示的身份验证 （注册） 的用户的联系人列表已创建。</span><span class="sxs-lookup"><span data-stu-id="336eb-306">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="336eb-307">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="336eb-307">There are three security groups:</span></span>

* <span data-ttu-id="336eb-308">**注册用户**可以查看所有已批准的数据还可以编辑/删除其自己的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-308">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="336eb-309">**管理器**可以批准或拒绝的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-309">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="336eb-310">仅已批准的联系人是对用户可见。</span><span class="sxs-lookup"><span data-stu-id="336eb-310">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="336eb-311">**管理员**可以批准/拒绝和编辑/删除的任何数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-311">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="336eb-312">在下图中，用户 Rick (`rick@example.com`) 登录。</span><span class="sxs-lookup"><span data-stu-id="336eb-312">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="336eb-313">Rick 只能查看允许的联系人和**编辑**/**删除**/**新建**其联系人的链接。</span><span class="sxs-lookup"><span data-stu-id="336eb-313">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="336eb-314">只有最后一个记录，创建由 Rick，显示**编辑**并**删除**链接。</span><span class="sxs-lookup"><span data-stu-id="336eb-314">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="336eb-315">其他用户不会看到的最后一个记录，直到经理或管理员的状态更改为"已批准"。</span><span class="sxs-lookup"><span data-stu-id="336eb-315">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![屏幕截图显示 Rick 登录](secure-data/_static/rick.png)

<span data-ttu-id="336eb-317">在下图中，`manager@contoso.com` 已登录，并在管理器的角色中：</span><span class="sxs-lookup"><span data-stu-id="336eb-317">In the following image, `manager@contoso.com` is signed in and in the manager's role:</span></span>

![屏幕截图显示manager@contoso.com登录](secure-data/_static/manager1.png)

<span data-ttu-id="336eb-319">下图显示在管理器的联系人的详细信息视图：</span><span class="sxs-lookup"><span data-stu-id="336eb-319">The following image shows the managers details view of a contact:</span></span>

![联系人的经理的视图](secure-data/_static/manager.png)

<span data-ttu-id="336eb-321">**批准**并**拒绝**按钮仅显示经理和管理员。</span><span class="sxs-lookup"><span data-stu-id="336eb-321">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="336eb-322">在下图中，`admin@contoso.com` 以管理员角色登录：</span><span class="sxs-lookup"><span data-stu-id="336eb-322">In the following image, `admin@contoso.com` is signed in and in the administrator's role:</span></span>

![屏幕截图显示admin@contoso.com登录](secure-data/_static/admin.png)

<span data-ttu-id="336eb-324">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="336eb-324">The administrator has all privileges.</span></span> <span data-ttu-id="336eb-325">她可以读取、 编辑或删除任何联系，并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="336eb-325">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="336eb-326">通过创建该应用[基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)以下`Contact`模型：</span><span class="sxs-lookup"><span data-stu-id="336eb-326">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="336eb-327">此示例包含以下授权处理程序：</span><span class="sxs-lookup"><span data-stu-id="336eb-327">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="336eb-328">`ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-328">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="336eb-329">`ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-329">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="336eb-330">`ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-330">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="336eb-331">系统必备</span><span class="sxs-lookup"><span data-stu-id="336eb-331">Prerequisites</span></span>

<span data-ttu-id="336eb-332">本教程被高级。</span><span class="sxs-lookup"><span data-stu-id="336eb-332">This tutorial is advanced.</span></span> <span data-ttu-id="336eb-333">您应熟悉：</span><span class="sxs-lookup"><span data-stu-id="336eb-333">You should be familiar with:</span></span>

* [<span data-ttu-id="336eb-334">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="336eb-334">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="336eb-335">身份验证</span><span class="sxs-lookup"><span data-stu-id="336eb-335">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="336eb-336">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="336eb-336">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="336eb-337">授权</span><span class="sxs-lookup"><span data-stu-id="336eb-337">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="336eb-338">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="336eb-338">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="336eb-339">Starter 和已完成应用程序</span><span class="sxs-lookup"><span data-stu-id="336eb-339">The starter and completed app</span></span>

<span data-ttu-id="336eb-340">[下载](xref:index#how-to-download-a-sample)[完成](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)应用。</span><span class="sxs-lookup"><span data-stu-id="336eb-340">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="336eb-341">[测试](#test-the-completed-app)已完成的应用，使你熟悉其安全功能。</span><span class="sxs-lookup"><span data-stu-id="336eb-341">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="336eb-342">入门级应用</span><span class="sxs-lookup"><span data-stu-id="336eb-342">The starter app</span></span>

<span data-ttu-id="336eb-343">[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。</span><span class="sxs-lookup"><span data-stu-id="336eb-343">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/) app.</span></span>

<span data-ttu-id="336eb-344">运行应用，点击**ContactManager**链接，并验证是否可以创建、 编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-344">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="336eb-345">保护用户数据</span><span class="sxs-lookup"><span data-stu-id="336eb-345">Secure user data</span></span>

<span data-ttu-id="336eb-346">以下部分介绍了所有主要的步骤以创建安全的用户数据应用程序。</span><span class="sxs-lookup"><span data-stu-id="336eb-346">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="336eb-347">您可能会发现引用的已完成项目很有帮助。</span><span class="sxs-lookup"><span data-stu-id="336eb-347">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="336eb-348">将绑定到用户的联系人数据</span><span class="sxs-lookup"><span data-stu-id="336eb-348">Tie the contact data to the user</span></span>

<span data-ttu-id="336eb-349">使用 ASP.NET[标识](xref:security/authentication/identity)用户 ID，以确保用户可以编辑其数据，而不是其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-349">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="336eb-350">添加`OwnerID`并`ContactStatus`到`Contact`模型：</span><span class="sxs-lookup"><span data-stu-id="336eb-350">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="336eb-351">`OwnerID` 是从用户的 ID`AspNetUser`表中[标识](xref:security/authentication/identity)数据库。</span><span class="sxs-lookup"><span data-stu-id="336eb-351">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="336eb-352">`Status`字段确定是否可由普通用户查看联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-352">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="336eb-353">创建新的迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="336eb-353">Create a new migration and update the database:</span></span>

```dotnetcli
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="336eb-354">将角色服务添加到标识</span><span class="sxs-lookup"><span data-stu-id="336eb-354">Add Role services to Identity</span></span>

<span data-ttu-id="336eb-355">追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="336eb-355">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="336eb-356">需要身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="336eb-356">Require authenticated users</span></span>

<span data-ttu-id="336eb-357">设置默认身份验证策略以要求用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="336eb-357">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="336eb-358">可以选择在 Razor 页面、 控制器或操作方法级别使用的身份验证禁用`[AllowAnonymous]`属性。</span><span class="sxs-lookup"><span data-stu-id="336eb-358">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="336eb-359">设置默认身份验证策略以要求用户进行身份验证来保护新添加的 Razor 页面和控制器。</span><span class="sxs-lookup"><span data-stu-id="336eb-359">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="336eb-360">默认情况下所需的身份验证是比依赖于新的控制器和 Razor 页，以包含更安全`[Authorize]`属性。</span><span class="sxs-lookup"><span data-stu-id="336eb-360">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="336eb-361">添加[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)到索引中，因此匿名用户可以获取有关站点的信息注册有关，和联系人页面。</span><span class="sxs-lookup"><span data-stu-id="336eb-361">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="336eb-362">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="336eb-362">Configure the test account</span></span>

<span data-ttu-id="336eb-363">`SeedData`类创建两个帐户： 管理员和管理员。</span><span class="sxs-lookup"><span data-stu-id="336eb-363">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="336eb-364">使用[机密管理器工具](xref:security/app-secrets)设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="336eb-364">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="336eb-365">从项目目录中设置密码 (目录包含*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="336eb-365">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```dotnetcli
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="336eb-366">如果未指定强密码，将引发异常时`SeedData.Initialize`调用。</span><span class="sxs-lookup"><span data-stu-id="336eb-366">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="336eb-367">更新`Main`使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="336eb-367">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="336eb-368">创建测试帐户和更新联系人</span><span class="sxs-lookup"><span data-stu-id="336eb-368">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="336eb-369">更新`Initialize`中的方法`SeedData`类，以创建测试帐户：</span><span class="sxs-lookup"><span data-stu-id="336eb-369">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="336eb-370">添加管理员用户 ID 和`ContactStatus`到联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-370">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="336eb-371">先创建一个"已提交"和一个"已拒绝"的联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-371">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="336eb-372">将用户 ID 和状态添加到所有联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-372">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="336eb-373">只能有一个联系人所示：</span><span class="sxs-lookup"><span data-stu-id="336eb-373">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="336eb-374">创建所有者、 经理和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="336eb-374">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="336eb-375">创建一个*授权*文件夹，并在其中创建一个 `ContactIsOwnerAuthorizationHandler` 类。</span><span class="sxs-lookup"><span data-stu-id="336eb-375">Create an *Authorization* folder and create a `ContactIsOwnerAuthorizationHandler` class in it.</span></span> <span data-ttu-id="336eb-376">`ContactIsOwnerAuthorizationHandler`验证对资源进行操作的用户拥有的资源。</span><span class="sxs-lookup"><span data-stu-id="336eb-376">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="336eb-377">`ContactIsOwnerAuthorizationHandler`调用[上下文。成功](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)当前经过身份验证的用户是否联系所有者。</span><span class="sxs-lookup"><span data-stu-id="336eb-377">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="336eb-378">授权处理程序通常：</span><span class="sxs-lookup"><span data-stu-id="336eb-378">Authorization handlers generally:</span></span>

* <span data-ttu-id="336eb-379">返回`context.Succeed`满足的要求。</span><span class="sxs-lookup"><span data-stu-id="336eb-379">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="336eb-380">返回`Task.CompletedTask`时不符合要求。</span><span class="sxs-lookup"><span data-stu-id="336eb-380">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="336eb-381">`Task.CompletedTask` 不是成功或失败&mdash;它允许其他授权处理程序运行。</span><span class="sxs-lookup"><span data-stu-id="336eb-381">`Task.CompletedTask` is not success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="336eb-382">如果你需要将显式失败，返回[上下文。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="336eb-382">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="336eb-383">应用程序允许联系所有者到编辑/删除/创建他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-383">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="336eb-384">`ContactIsOwnerAuthorizationHandler` 不需要检查要求参数中传递该操作。</span><span class="sxs-lookup"><span data-stu-id="336eb-384">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="336eb-385">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="336eb-385">Create a manager authorization handler</span></span>

<span data-ttu-id="336eb-386">创建`ContactManagerAuthorizationHandler`类中*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="336eb-386">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="336eb-387">`ContactManagerAuthorizationHandler`验证对资源进行操作的用户是管理员。</span><span class="sxs-lookup"><span data-stu-id="336eb-387">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="336eb-388">只有经理们才可以批准或拒绝内容的更改 （新的或已更改）。</span><span class="sxs-lookup"><span data-stu-id="336eb-388">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="336eb-389">创建管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="336eb-389">Create an administrator authorization handler</span></span>

<span data-ttu-id="336eb-390">创建`ContactAdministratorsAuthorizationHandler`类中*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="336eb-390">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="336eb-391">`ContactAdministratorsAuthorizationHandler`验证对资源进行操作的用户是管理员。</span><span class="sxs-lookup"><span data-stu-id="336eb-391">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="336eb-392">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="336eb-392">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="336eb-393">注册授权处理程序</span><span class="sxs-lookup"><span data-stu-id="336eb-393">Register the authorization handlers</span></span>

<span data-ttu-id="336eb-394">Entity Framework Core 使用 [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions) 的服务必须使用注册以进行[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="336eb-394">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="336eb-395">`ContactIsOwnerAuthorizationHandler`使用 ASP.NET Core[标识](xref:security/authentication/identity)，这基于实体框架核心。</span><span class="sxs-lookup"><span data-stu-id="336eb-395">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="336eb-396">注册服务集合的处理程序，以便它们可供`ContactsController`通过[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="336eb-396">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="336eb-397">将以下代码添加到末尾`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="336eb-397">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="336eb-398">`ContactAdministratorsAuthorizationHandler` 和`ContactManagerAuthorizationHandler`添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="336eb-398">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="336eb-399">它们是单一实例，因为它们不使用 EF 和所需的所有信息都位于`Context`参数的`HandleRequirementAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="336eb-399">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="336eb-400">支持授权</span><span class="sxs-lookup"><span data-stu-id="336eb-400">Support authorization</span></span>

<span data-ttu-id="336eb-401">在本部分中，将更新 Razor 页面和添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="336eb-401">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="336eb-402">查看联系人操作要求类</span><span class="sxs-lookup"><span data-stu-id="336eb-402">Review the contact operations requirements class</span></span>

<span data-ttu-id="336eb-403">查看`ContactOperations`类。</span><span class="sxs-lookup"><span data-stu-id="336eb-403">Review the `ContactOperations` class.</span></span> <span data-ttu-id="336eb-404">此类包含要求应用支持：</span><span class="sxs-lookup"><span data-stu-id="336eb-404">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="336eb-405">创建联系人 Razor 页面的基类</span><span class="sxs-lookup"><span data-stu-id="336eb-405">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="336eb-406">创建一个包含在联系人 Razor 页面使用的服务的基类。</span><span class="sxs-lookup"><span data-stu-id="336eb-406">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="336eb-407">基类将初始化代码放在一个位置：</span><span class="sxs-lookup"><span data-stu-id="336eb-407">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="336eb-408">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="336eb-408">The preceding code:</span></span>

* <span data-ttu-id="336eb-409">添加`IAuthorizationService`服务对授权处理程序的访问权限。</span><span class="sxs-lookup"><span data-stu-id="336eb-409">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="336eb-410">将标识添加`UserManager`服务。</span><span class="sxs-lookup"><span data-stu-id="336eb-410">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="336eb-411">添加 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="336eb-411">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="336eb-412">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="336eb-412">Update the CreateModel</span></span>

<span data-ttu-id="336eb-413">更新创建页面模型构造函数以使用`DI_BasePageModel`基类：</span><span class="sxs-lookup"><span data-stu-id="336eb-413">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="336eb-414">更新`CreateModel.OnPostAsync`方法：</span><span class="sxs-lookup"><span data-stu-id="336eb-414">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="336eb-415">添加到的用户 ID`Contact`模型。</span><span class="sxs-lookup"><span data-stu-id="336eb-415">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="336eb-416">调用授权处理程序，以验证用户有权创建联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-416">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="336eb-417">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="336eb-417">Update the IndexModel</span></span>

<span data-ttu-id="336eb-418">更新`OnGetAsync`方法，以便仅被批准的联系人显示为普通用户：</span><span class="sxs-lookup"><span data-stu-id="336eb-418">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="336eb-419">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="336eb-419">Update the EditModel</span></span>

<span data-ttu-id="336eb-420">添加授权处理程序以验证的用户拥有联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-420">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="336eb-421">正在验证资源授权，因为`[Authorize]`属性不能满足。</span><span class="sxs-lookup"><span data-stu-id="336eb-421">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="336eb-422">评估属性时，应用程序不具有对资源的访问。</span><span class="sxs-lookup"><span data-stu-id="336eb-422">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="336eb-423">基于资源的授权必须是命令性。</span><span class="sxs-lookup"><span data-stu-id="336eb-423">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="336eb-424">应用程序页面模型中加载或加载处理程序本身内获得资源的访问权限，则必须执行检查。</span><span class="sxs-lookup"><span data-stu-id="336eb-424">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="336eb-425">您经常访问的资源，通过传入的资源键。</span><span class="sxs-lookup"><span data-stu-id="336eb-425">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="336eb-426">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="336eb-426">Update the DeleteModel</span></span>

<span data-ttu-id="336eb-427">更新要使用授权处理程序来验证用户具有 delete 权限 contact 上的删除页面模型。</span><span class="sxs-lookup"><span data-stu-id="336eb-427">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="336eb-428">将授权服务注入到视图</span><span class="sxs-lookup"><span data-stu-id="336eb-428">Inject the authorization service into the views</span></span>

<span data-ttu-id="336eb-429">目前，此 UI 显示编辑和删除的用户无法修改的联系人的链接。</span><span class="sxs-lookup"><span data-stu-id="336eb-429">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="336eb-430">注入中的授权服务*views/_viewimports.cshtml*文件，以便它可供所有视图：</span><span class="sxs-lookup"><span data-stu-id="336eb-430">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="336eb-431">上述标记添加了多种`using`语句。</span><span class="sxs-lookup"><span data-stu-id="336eb-431">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="336eb-432">更新**编辑**并**删除**中的链接*Pages/Contacts/Index.cshtml*以便仅在呈现具有适当权限的用户：</span><span class="sxs-lookup"><span data-stu-id="336eb-432">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="336eb-433">隐藏用户没有权限更改的数据中的链接不会保护应用。</span><span class="sxs-lookup"><span data-stu-id="336eb-433">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="336eb-434">隐藏链接使应用更加友好的用户显示唯一有效的链接。</span><span class="sxs-lookup"><span data-stu-id="336eb-434">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="336eb-435">用户可以 hack 生成的 Url 以调用编辑和删除操作上不拥有的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-435">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="336eb-436">Razor 页面或控制器必须强制执行访问检查，以保护数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-436">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="336eb-437">更新详细信息</span><span class="sxs-lookup"><span data-stu-id="336eb-437">Update Details</span></span>

<span data-ttu-id="336eb-438">更新的详细信息视图，使管理员可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="336eb-438">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="336eb-439">更新详细信息页模型：</span><span class="sxs-lookup"><span data-stu-id="336eb-439">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="336eb-440">添加或删除角色对用户的</span><span class="sxs-lookup"><span data-stu-id="336eb-440">Add or remove a user to a role</span></span>

<span data-ttu-id="336eb-441">请参阅[本期](https://github.com/aspnet/AspNetCore.Docs/issues/8502)有关的信息：</span><span class="sxs-lookup"><span data-stu-id="336eb-441">See [this issue](https://github.com/aspnet/AspNetCore.Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="336eb-442">从用户删除的特权。</span><span class="sxs-lookup"><span data-stu-id="336eb-442">Removing privileges from a user.</span></span> <span data-ttu-id="336eb-443">例如，在聊天应用中对用户进行静音。</span><span class="sxs-lookup"><span data-stu-id="336eb-443">For example, muting a user in a chat app.</span></span>
* <span data-ttu-id="336eb-444">将权限添加到用户。</span><span class="sxs-lookup"><span data-stu-id="336eb-444">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="336eb-445">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="336eb-445">Test the completed app</span></span>

<span data-ttu-id="336eb-446">如果你尚未设置设定为种子的用户帐户的密码，使用[机密管理器工具](xref:security/app-secrets#secret-manager)设置密码：</span><span class="sxs-lookup"><span data-stu-id="336eb-446">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="336eb-447">选择强密码：使用八个或更多字符，并且至少使用一个大写字符、数字和符号。</span><span class="sxs-lookup"><span data-stu-id="336eb-447">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="336eb-448">例如，`Passw0rd!`符合强密码要求。</span><span class="sxs-lookup"><span data-stu-id="336eb-448">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="336eb-449">执行以下命令从项目的文件夹，其中`<PW>`的密码：</span><span class="sxs-lookup"><span data-stu-id="336eb-449">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```dotnetcli
  dotnet user-secrets set SeedUserPW <PW>
  ```

* <span data-ttu-id="336eb-450">删除和更新数据库</span><span class="sxs-lookup"><span data-stu-id="336eb-450">Drop and update the Database</span></span>

  ```dotnetcli
  dotnet ef database drop -f
  dotnet ef database update  
  ```

* <span data-ttu-id="336eb-451">重新启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="336eb-451">Restart the app to seed the database.</span></span>

<span data-ttu-id="336eb-452">测试已完成的应用程序的简单方法是启动三个不同的浏览器 （或 incognito/InPrivate 会话）。</span><span class="sxs-lookup"><span data-stu-id="336eb-452">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="336eb-453">在一个浏览器中注册一个新用户 (例如， `test@example.com`)。</span><span class="sxs-lookup"><span data-stu-id="336eb-453">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="336eb-454">登录到每个浏览器使用不同的用户。</span><span class="sxs-lookup"><span data-stu-id="336eb-454">Sign in to each browser with a different user.</span></span> <span data-ttu-id="336eb-455">验证以下操作：</span><span class="sxs-lookup"><span data-stu-id="336eb-455">Verify the following operations:</span></span>

* <span data-ttu-id="336eb-456">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-456">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="336eb-457">已注册的用户可以编辑/删除其自己的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-457">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="336eb-458">经理可以批准/拒绝的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-458">Managers can approve/reject contact data.</span></span> <span data-ttu-id="336eb-459">`Details`视图视图将显示**批准**并**拒绝**按钮。</span><span class="sxs-lookup"><span data-stu-id="336eb-459">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="336eb-460">管理员可以批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-460">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="336eb-461">用户</span><span class="sxs-lookup"><span data-stu-id="336eb-461">User</span></span>                | <span data-ttu-id="336eb-462">由应用程序进行种子设定</span><span class="sxs-lookup"><span data-stu-id="336eb-462">Seeded by the app</span></span> | <span data-ttu-id="336eb-463">选项</span><span class="sxs-lookup"><span data-stu-id="336eb-463">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="336eb-464">No</span><span class="sxs-lookup"><span data-stu-id="336eb-464">No</span></span>                | <span data-ttu-id="336eb-465">编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-465">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="336eb-466">是</span><span class="sxs-lookup"><span data-stu-id="336eb-466">Yes</span></span>               | <span data-ttu-id="336eb-467">批准/拒绝和编辑/删除拥有的数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-467">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="336eb-468">是</span><span class="sxs-lookup"><span data-stu-id="336eb-468">Yes</span></span>               | <span data-ttu-id="336eb-469">批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="336eb-469">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="336eb-470">在管理员的浏览器中创建联系人。</span><span class="sxs-lookup"><span data-stu-id="336eb-470">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="336eb-471">删除 URL 复制并编辑从管理员的联系信息。</span><span class="sxs-lookup"><span data-stu-id="336eb-471">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="336eb-472">将下面的链接粘贴到测试用户的浏览器以验证测试用户不能执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="336eb-472">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="336eb-473">创建入门级应用</span><span class="sxs-lookup"><span data-stu-id="336eb-473">Create the starter app</span></span>

* <span data-ttu-id="336eb-474">创建名为"ContactManager"Razor 页面应用</span><span class="sxs-lookup"><span data-stu-id="336eb-474">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="336eb-475">创建包含应用**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="336eb-475">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="336eb-476">它命名为"ContactManager"使命名空间匹配的示例中使用的命名空间。</span><span class="sxs-lookup"><span data-stu-id="336eb-476">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="336eb-477">`-uld` 指定 LocalDB，而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="336eb-477">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```dotnetcli
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="336eb-478">添加*模型/联系方式*：</span><span class="sxs-lookup"><span data-stu-id="336eb-478">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="336eb-479">基架`Contact`模型。</span><span class="sxs-lookup"><span data-stu-id="336eb-479">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="336eb-480">创建初始迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="336eb-480">Create initial migration and update the database:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="336eb-481">更新**ContactManager**中的定位点*pages/_layout.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="336eb-481">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="336eb-482">测试应用程序的创建、 编辑和删除联系人</span><span class="sxs-lookup"><span data-stu-id="336eb-482">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="336eb-483">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="336eb-483">Seed the database</span></span>

<span data-ttu-id="336eb-484">添加[SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)类来*数据*文件夹。</span><span class="sxs-lookup"><span data-stu-id="336eb-484">Add the [SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="336eb-485">调用`SeedData.Initialize`从`Main`:</span><span class="sxs-lookup"><span data-stu-id="336eb-485">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="336eb-486">测试应用程序设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="336eb-486">Test that the app seeded the database.</span></span> <span data-ttu-id="336eb-487">如果联系人 DB 中有任何行，则不会运行 seed 方法。</span><span class="sxs-lookup"><span data-stu-id="336eb-487">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="336eb-488">其他资源</span><span class="sxs-lookup"><span data-stu-id="336eb-488">Additional resources</span></span>

* [<span data-ttu-id="336eb-489">生成 Azure 应用服务中的.NET Core 和 SQL 数据库 web 应用</span><span class="sxs-lookup"><span data-stu-id="336eb-489">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="336eb-490">[ASP.NET Core 授权实验室](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="336eb-490">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="336eb-491">此实验室进入的安全功能，本教程中介绍的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="336eb-491">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="336eb-492">基于自定义策略的授权</span><span class="sxs-lookup"><span data-stu-id="336eb-492">Custom policy-based authorization</span></span>](xref:security/authorization/policies)
