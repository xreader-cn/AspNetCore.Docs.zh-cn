---
title: 使用受授权的用户数据创建 ASP.NET Core 应用
author: rick-anderson
description: 了解如何使用受保护的授权的用户数据创建 Razor 页面应用。 包括 HTTPS、 身份验证、 安全性、 ASP.NET Core 标识。
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 9b01aca0cb2b33f5561193ffdce4aa4950485fce
ms.sourcegitcommit: 8a84ce880b4c40d6694ba6423038f18fc2eb5746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60165233"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a><span data-ttu-id="326dc-104">使用受授权的用户数据创建 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="326dc-104">Create an ASP.NET Core app with user data protected by authorization</span></span>

<span data-ttu-id="326dc-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)</span><span class="sxs-lookup"><span data-stu-id="326dc-105">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Joe Audette](https://twitter.com/joeaudette)</span></span>

::: moniker range="<= aspnetcore-1.1"

<span data-ttu-id="326dc-106">请参阅[此 PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) ASP.NET Core MVC 版本。</span><span class="sxs-lookup"><span data-stu-id="326dc-106">See [this PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) for the ASP.NET Core MVC version.</span></span> <span data-ttu-id="326dc-107">本教程的 ASP.NET Core 1.1 版本是[这](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data)文件夹。</span><span class="sxs-lookup"><span data-stu-id="326dc-107">The ASP.NET Core 1.1 version of this tutorial is in [this](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data) folder.</span></span> <span data-ttu-id="326dc-108">ASP.NET Core 示例是在 1.1[示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)。</span><span class="sxs-lookup"><span data-stu-id="326dc-108">The 1.1 ASP.NET Core sample is in the [samples](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="326dc-109">请参阅[此 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span><span class="sxs-lookup"><span data-stu-id="326dc-109">See [this pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="326dc-110">本教程演示如何使用受授权的用户数据创建 ASP.NET Core web 应用。</span><span class="sxs-lookup"><span data-stu-id="326dc-110">This tutorial shows how to create an ASP.NET Core web app with user data protected by authorization.</span></span> <span data-ttu-id="326dc-111">它显示的身份验证 （注册） 的用户的联系人列表已创建。</span><span class="sxs-lookup"><span data-stu-id="326dc-111">It displays a list of contacts that authenticated (registered) users have created.</span></span> <span data-ttu-id="326dc-112">有三个安全组：</span><span class="sxs-lookup"><span data-stu-id="326dc-112">There are three security groups:</span></span>

* <span data-ttu-id="326dc-113">**注册用户**可以查看所有已批准的数据还可以编辑/删除其自己的数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-113">**Registered users** can view all the approved data and can edit/delete their own data.</span></span>
* <span data-ttu-id="326dc-114">**管理器**可以批准或拒绝的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-114">**Managers** can approve or reject contact data.</span></span> <span data-ttu-id="326dc-115">仅已批准的联系人是对用户可见。</span><span class="sxs-lookup"><span data-stu-id="326dc-115">Only approved contacts are visible to users.</span></span>
* <span data-ttu-id="326dc-116">**管理员**可以批准/拒绝和编辑/删除的任何数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-116">**Administrators** can approve/reject and edit/delete any data.</span></span>

<span data-ttu-id="326dc-117">在下图中，用户 Rick (`rick@example.com`) 登录。</span><span class="sxs-lookup"><span data-stu-id="326dc-117">In the following image, user Rick (`rick@example.com`) is signed in.</span></span> <span data-ttu-id="326dc-118">Rick 只能查看允许的联系人和**编辑**/**删除**/**新建**其联系人的链接。</span><span class="sxs-lookup"><span data-stu-id="326dc-118">Rick can only view approved contacts and **Edit**/**Delete**/**Create New** links for his contacts.</span></span> <span data-ttu-id="326dc-119">只有最后一个记录，创建由 Rick，显示**编辑**并**删除**链接。</span><span class="sxs-lookup"><span data-stu-id="326dc-119">Only the last record, created by Rick, displays **Edit** and **Delete** links.</span></span> <span data-ttu-id="326dc-120">其他用户不会看到的最后一个记录，直到经理或管理员的状态更改为"已批准"。</span><span class="sxs-lookup"><span data-stu-id="326dc-120">Other users won't see the last record until a manager or administrator changes the status to "Approved".</span></span>

![屏幕截图显示 Rick 登录](secure-data/_static/rick.png)

<span data-ttu-id="326dc-122">在下图中，`manager@contoso.com`在和中的管理器角色进行签名：</span><span class="sxs-lookup"><span data-stu-id="326dc-122">In the following image, `manager@contoso.com` is signed in and in the managers role:</span></span>

![屏幕截图显示manager@contoso.com登录](secure-data/_static/manager1.png)

<span data-ttu-id="326dc-124">下图显示在管理器的联系人的详细信息视图：</span><span class="sxs-lookup"><span data-stu-id="326dc-124">The following image shows the managers details view of a contact:</span></span>

![联系人的经理的视图](secure-data/_static/manager.png)

<span data-ttu-id="326dc-126">**批准**并**拒绝**按钮仅显示经理和管理员。</span><span class="sxs-lookup"><span data-stu-id="326dc-126">The **Approve** and **Reject** buttons are only displayed for managers and administrators.</span></span>

<span data-ttu-id="326dc-127">在下图中，`admin@contoso.com`进行签名和管理员角色中：</span><span class="sxs-lookup"><span data-stu-id="326dc-127">In the following image, `admin@contoso.com` is signed in and in the administrators role:</span></span>

![屏幕截图显示admin@contoso.com登录](secure-data/_static/admin.png)

<span data-ttu-id="326dc-129">管理员具有所有权限。</span><span class="sxs-lookup"><span data-stu-id="326dc-129">The administrator has all privileges.</span></span> <span data-ttu-id="326dc-130">她可以读取、 编辑或删除任何联系，并更改联系人的状态。</span><span class="sxs-lookup"><span data-stu-id="326dc-130">She can read/edit/delete any contact and change the status of contacts.</span></span>

<span data-ttu-id="326dc-131">通过创建该应用[基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)以下`Contact`模型：</span><span class="sxs-lookup"><span data-stu-id="326dc-131">The app was created by [scaffolding](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model) the following `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

<span data-ttu-id="326dc-132">此示例包含以下授权处理程序：</span><span class="sxs-lookup"><span data-stu-id="326dc-132">The sample contains the following authorization handlers:</span></span>

* <span data-ttu-id="326dc-133">`ContactIsOwnerAuthorizationHandler`：可确保用户只能编辑其数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-133">`ContactIsOwnerAuthorizationHandler`: Ensures that a user can only edit their data.</span></span>
* <span data-ttu-id="326dc-134">`ContactManagerAuthorizationHandler`：允许管理人员批准或拒绝的联系人。</span><span class="sxs-lookup"><span data-stu-id="326dc-134">`ContactManagerAuthorizationHandler`: Allows managers to approve or reject contacts.</span></span>
* <span data-ttu-id="326dc-135">`ContactAdministratorsAuthorizationHandler`：允许管理员可以批准或拒绝联系人并将编辑/删除联系人。</span><span class="sxs-lookup"><span data-stu-id="326dc-135">`ContactAdministratorsAuthorizationHandler`: Allows administrators to approve or reject contacts and to edit/delete contacts.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="326dc-136">系统必备</span><span class="sxs-lookup"><span data-stu-id="326dc-136">Prerequisites</span></span>

<span data-ttu-id="326dc-137">本教程被高级。</span><span class="sxs-lookup"><span data-stu-id="326dc-137">This tutorial is advanced.</span></span> <span data-ttu-id="326dc-138">您应熟悉：</span><span class="sxs-lookup"><span data-stu-id="326dc-138">You should be familiar with:</span></span>

* [<span data-ttu-id="326dc-139">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="326dc-139">ASP.NET Core</span></span>](xref:tutorials/first-mvc-app/start-mvc)
* [<span data-ttu-id="326dc-140">身份验证</span><span class="sxs-lookup"><span data-stu-id="326dc-140">Authentication</span></span>](xref:security/authentication/identity)
* [<span data-ttu-id="326dc-141">帐户确认和密码恢复</span><span class="sxs-lookup"><span data-stu-id="326dc-141">Account Confirmation and Password Recovery</span></span>](xref:security/authentication/accconfirm)
* [<span data-ttu-id="326dc-142">授权</span><span class="sxs-lookup"><span data-stu-id="326dc-142">Authorization</span></span>](xref:security/authorization/introduction)
* [<span data-ttu-id="326dc-143">Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="326dc-143">Entity Framework Core</span></span>](xref:data/ef-mvc/intro)

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="326dc-144">在 ASP.NET Core 2.1`User.IsInRole`使用时，失败`AddDefaultIdentity`。</span><span class="sxs-lookup"><span data-stu-id="326dc-144">In ASP.NET Core 2.1, `User.IsInRole` fails when using `AddDefaultIdentity`.</span></span> <span data-ttu-id="326dc-145">本教程使用`AddDefaultIdentity`，因此需要 ASP.NET Core 2.2 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="326dc-145">This tutorial uses `AddDefaultIdentity` and therefore requires ASP.NET Core 2.2 or later.</span></span> <span data-ttu-id="326dc-146">请参阅[此 GitHub 问题](https://github.com/aspnet/Identity/issues/1813#issuecomment-394543909)的解决办法。</span><span class="sxs-lookup"><span data-stu-id="326dc-146">See [this GitHub issue](https://github.com/aspnet/Identity/issues/1813#issuecomment-394543909) for a work-around.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

## <a name="the-starter-and-completed-app"></a><span data-ttu-id="326dc-147">Starter 和已完成应用程序</span><span class="sxs-lookup"><span data-stu-id="326dc-147">The starter and completed app</span></span>

<span data-ttu-id="326dc-148">[下载](xref:index#how-to-download-a-sample)[完成](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)应用。</span><span class="sxs-lookup"><span data-stu-id="326dc-148">[Download](xref:index#how-to-download-a-sample) the [completed](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples) app.</span></span> <span data-ttu-id="326dc-149">[测试](#test-the-completed-app)已完成的应用，使你熟悉其安全功能。</span><span class="sxs-lookup"><span data-stu-id="326dc-149">[Test](#test-the-completed-app) the completed app so you become familiar with its security features.</span></span>

### <a name="the-starter-app"></a><span data-ttu-id="326dc-150">入门级应用</span><span class="sxs-lookup"><span data-stu-id="326dc-150">The starter app</span></span>

<span data-ttu-id="326dc-151">[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2)应用。</span><span class="sxs-lookup"><span data-stu-id="326dc-151">[Download](xref:index#how-to-download-a-sample) the [starter](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2) app.</span></span>

<span data-ttu-id="326dc-152">运行应用，点击**ContactManager**链接，并验证是否可以创建、 编辑和删除联系人。</span><span class="sxs-lookup"><span data-stu-id="326dc-152">Run the app, tap the **ContactManager** link, and verify you can create, edit, and delete a contact.</span></span>

## <a name="secure-user-data"></a><span data-ttu-id="326dc-153">保护用户数据</span><span class="sxs-lookup"><span data-stu-id="326dc-153">Secure user data</span></span>

<span data-ttu-id="326dc-154">以下部分介绍了所有主要的步骤以创建安全的用户数据应用程序。</span><span class="sxs-lookup"><span data-stu-id="326dc-154">The following sections have all the major steps to create the secure user data app.</span></span> <span data-ttu-id="326dc-155">您可能会发现引用的已完成项目很有帮助。</span><span class="sxs-lookup"><span data-stu-id="326dc-155">You may find it helpful to refer to the completed project.</span></span>

### <a name="tie-the-contact-data-to-the-user"></a><span data-ttu-id="326dc-156">将绑定到用户的联系人数据</span><span class="sxs-lookup"><span data-stu-id="326dc-156">Tie the contact data to the user</span></span>

<span data-ttu-id="326dc-157">使用 ASP.NET[标识](xref:security/authentication/identity)用户 ID，以确保用户可以编辑其数据，而不是其他用户数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-157">Use the ASP.NET [Identity](xref:security/authentication/identity) user ID to ensure users can edit their data, but not other users data.</span></span> <span data-ttu-id="326dc-158">添加`OwnerID`并`ContactStatus`到`Contact`模型：</span><span class="sxs-lookup"><span data-stu-id="326dc-158">Add `OwnerID` and `ContactStatus` to the `Contact` model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

<span data-ttu-id="326dc-159">`OwnerID` 是从用户的 ID`AspNetUser`表中[标识](xref:security/authentication/identity)数据库。</span><span class="sxs-lookup"><span data-stu-id="326dc-159">`OwnerID` is the user's ID from the `AspNetUser` table in the [Identity](xref:security/authentication/identity) database.</span></span> <span data-ttu-id="326dc-160">`Status`字段确定是否可由普通用户查看联系人。</span><span class="sxs-lookup"><span data-stu-id="326dc-160">The `Status` field determines if a contact is viewable by general users.</span></span>

<span data-ttu-id="326dc-161">创建新的迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="326dc-161">Create a new migration and update the database:</span></span>

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a><span data-ttu-id="326dc-162">将角色服务添加到标识</span><span class="sxs-lookup"><span data-stu-id="326dc-162">Add Role services to Identity</span></span>

<span data-ttu-id="326dc-163">追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)添加角色服务：</span><span class="sxs-lookup"><span data-stu-id="326dc-163">Append [AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1) to add Role services:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a><span data-ttu-id="326dc-164">需要身份验证的用户</span><span class="sxs-lookup"><span data-stu-id="326dc-164">Require authenticated users</span></span>

<span data-ttu-id="326dc-165">设置默认身份验证策略以要求用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="326dc-165">Set the default authentication policy to require users to be authenticated:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 <span data-ttu-id="326dc-166">可以选择在 Razor 页面、 控制器或操作方法级别使用的身份验证禁用`[AllowAnonymous]`属性。</span><span class="sxs-lookup"><span data-stu-id="326dc-166">You can opt out of authentication at the Razor Page, controller, or action method level with the `[AllowAnonymous]` attribute.</span></span> <span data-ttu-id="326dc-167">设置默认身份验证策略以要求用户进行身份验证来保护新添加的 Razor 页面和控制器。</span><span class="sxs-lookup"><span data-stu-id="326dc-167">Setting the default authentication policy to require users to be authenticated protects newly added Razor Pages and controllers.</span></span> <span data-ttu-id="326dc-168">默认情况下所需的身份验证是比依赖于新的控制器和 Razor 页，以包含更安全`[Authorize]`属性。</span><span class="sxs-lookup"><span data-stu-id="326dc-168">Having authentication required by default is more secure than relying on new controllers and Razor Pages to include the `[Authorize]` attribute.</span></span>

<span data-ttu-id="326dc-169">添加[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)到索引中，因此匿名用户可以获取有关站点的信息注册有关，和联系人页面。</span><span class="sxs-lookup"><span data-stu-id="326dc-169">Add [AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute) to the Index, About, and Contact pages so anonymous users can get information about the site before they register.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a><span data-ttu-id="326dc-170">配置测试帐户</span><span class="sxs-lookup"><span data-stu-id="326dc-170">Configure the test account</span></span>

<span data-ttu-id="326dc-171">`SeedData`类创建两个帐户： 管理员和管理员。</span><span class="sxs-lookup"><span data-stu-id="326dc-171">The `SeedData` class creates two accounts: administrator and manager.</span></span> <span data-ttu-id="326dc-172">使用[机密管理器工具](xref:security/app-secrets)设置这些帐户的密码。</span><span class="sxs-lookup"><span data-stu-id="326dc-172">Use the [Secret Manager tool](xref:security/app-secrets) to set a password for these accounts.</span></span> <span data-ttu-id="326dc-173">从项目目录中设置密码 (目录包含*Program.cs*):</span><span class="sxs-lookup"><span data-stu-id="326dc-173">Set the password from the project directory (the directory containing *Program.cs*):</span></span>

```console
dotnet user-secrets set SeedUserPW <PW>
```

<span data-ttu-id="326dc-174">如果未指定强密码，将引发异常时`SeedData.Initialize`调用。</span><span class="sxs-lookup"><span data-stu-id="326dc-174">If a strong password is not specified, an exception is thrown when `SeedData.Initialize` is called.</span></span>

<span data-ttu-id="326dc-175">更新`Main`使用测试密码：</span><span class="sxs-lookup"><span data-stu-id="326dc-175">Update `Main` to use the test password:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a><span data-ttu-id="326dc-176">创建测试帐户和更新联系人</span><span class="sxs-lookup"><span data-stu-id="326dc-176">Create the test accounts and update the contacts</span></span>

<span data-ttu-id="326dc-177">更新`Initialize`中的方法`SeedData`类，以创建测试帐户：</span><span class="sxs-lookup"><span data-stu-id="326dc-177">Update the `Initialize` method in the `SeedData` class to create the test accounts:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

<span data-ttu-id="326dc-178">添加管理员用户 ID 和`ContactStatus`到联系人。</span><span class="sxs-lookup"><span data-stu-id="326dc-178">Add the administrator user ID and `ContactStatus` to the contacts.</span></span> <span data-ttu-id="326dc-179">先创建一个"已提交"和一个"已拒绝"的联系人。</span><span class="sxs-lookup"><span data-stu-id="326dc-179">Make one of the contacts "Submitted" and one "Rejected".</span></span> <span data-ttu-id="326dc-180">将用户 ID 和状态添加到所有联系人。</span><span class="sxs-lookup"><span data-stu-id="326dc-180">Add the user ID and status to all the contacts.</span></span> <span data-ttu-id="326dc-181">只能有一个联系人所示：</span><span class="sxs-lookup"><span data-stu-id="326dc-181">Only one contact is shown:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a><span data-ttu-id="326dc-182">创建所有者、 经理和管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="326dc-182">Create owner, manager, and administrator authorization handlers</span></span>

<span data-ttu-id="326dc-183">创建`ContactIsOwnerAuthorizationHandler`类中*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="326dc-183">Create a `ContactIsOwnerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="326dc-184">`ContactIsOwnerAuthorizationHandler`验证对资源进行操作的用户拥有的资源。</span><span class="sxs-lookup"><span data-stu-id="326dc-184">The `ContactIsOwnerAuthorizationHandler` verifies that the user acting on a resource owns the resource.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

<span data-ttu-id="326dc-185">`ContactIsOwnerAuthorizationHandler`调用[上下文。成功](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)当前经过身份验证的用户是否联系所有者。</span><span class="sxs-lookup"><span data-stu-id="326dc-185">The `ContactIsOwnerAuthorizationHandler` calls [context.Succeed](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_) if the current authenticated user is the contact owner.</span></span> <span data-ttu-id="326dc-186">授权处理程序通常：</span><span class="sxs-lookup"><span data-stu-id="326dc-186">Authorization handlers generally:</span></span>

* <span data-ttu-id="326dc-187">返回`context.Succeed`满足的要求。</span><span class="sxs-lookup"><span data-stu-id="326dc-187">Return `context.Succeed` when the requirements are met.</span></span>
* <span data-ttu-id="326dc-188">返回`Task.CompletedTask`时不符合要求。</span><span class="sxs-lookup"><span data-stu-id="326dc-188">Return `Task.CompletedTask` when requirements aren't met.</span></span> <span data-ttu-id="326dc-189">`Task.CompletedTask` 既不成功或失败&mdash;它允许运行其他授权处理程序。</span><span class="sxs-lookup"><span data-stu-id="326dc-189">`Task.CompletedTask` is neither success or failure&mdash;it allows other authorization handlers to run.</span></span>

<span data-ttu-id="326dc-190">如果你需要将显式失败，返回[上下文。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。</span><span class="sxs-lookup"><span data-stu-id="326dc-190">If you need to explicitly fail, return [context.Fail](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail).</span></span>

<span data-ttu-id="326dc-191">应用程序允许联系所有者到编辑/删除/创建他们自己的数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-191">The app allows contact owners to edit/delete/create their own data.</span></span> <span data-ttu-id="326dc-192">`ContactIsOwnerAuthorizationHandler` 不需要检查要求参数中传递该操作。</span><span class="sxs-lookup"><span data-stu-id="326dc-192">`ContactIsOwnerAuthorizationHandler` doesn't need to check the operation passed in the requirement parameter.</span></span>

### <a name="create-a-manager-authorization-handler"></a><span data-ttu-id="326dc-193">创建管理器授权处理程序</span><span class="sxs-lookup"><span data-stu-id="326dc-193">Create a manager authorization handler</span></span>

<span data-ttu-id="326dc-194">创建`ContactManagerAuthorizationHandler`类中*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="326dc-194">Create a `ContactManagerAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="326dc-195">`ContactManagerAuthorizationHandler`验证对资源进行操作的用户是管理员。</span><span class="sxs-lookup"><span data-stu-id="326dc-195">The `ContactManagerAuthorizationHandler` verifies the user acting on the resource is a manager.</span></span> <span data-ttu-id="326dc-196">只有经理们才可以批准或拒绝内容的更改 （新的或已更改）。</span><span class="sxs-lookup"><span data-stu-id="326dc-196">Only managers can approve or reject content changes (new or changed).</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a><span data-ttu-id="326dc-197">创建管理员授权处理程序</span><span class="sxs-lookup"><span data-stu-id="326dc-197">Create an administrator authorization handler</span></span>

<span data-ttu-id="326dc-198">创建`ContactAdministratorsAuthorizationHandler`类中*授权*文件夹。</span><span class="sxs-lookup"><span data-stu-id="326dc-198">Create a `ContactAdministratorsAuthorizationHandler` class in the *Authorization* folder.</span></span> <span data-ttu-id="326dc-199">`ContactAdministratorsAuthorizationHandler`验证对资源进行操作的用户是管理员。</span><span class="sxs-lookup"><span data-stu-id="326dc-199">The `ContactAdministratorsAuthorizationHandler` verifies the user acting on the resource is an administrator.</span></span> <span data-ttu-id="326dc-200">管理员可以执行所有操作。</span><span class="sxs-lookup"><span data-stu-id="326dc-200">Administrator can do all operations.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a><span data-ttu-id="326dc-201">注册授权处理程序</span><span class="sxs-lookup"><span data-stu-id="326dc-201">Register the authorization handlers</span></span>

<span data-ttu-id="326dc-202">必须为注册服务使用 Entity Framework Core[依赖关系注入](xref:fundamentals/dependency-injection)使用[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)。</span><span class="sxs-lookup"><span data-stu-id="326dc-202">Services using Entity Framework Core must be registered for [dependency injection](xref:fundamentals/dependency-injection) using [AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions).</span></span> <span data-ttu-id="326dc-203">`ContactIsOwnerAuthorizationHandler`使用 ASP.NET Core[标识](xref:security/authentication/identity)，这基于实体框架核心。</span><span class="sxs-lookup"><span data-stu-id="326dc-203">The `ContactIsOwnerAuthorizationHandler` uses ASP.NET Core [Identity](xref:security/authentication/identity), which is built on Entity Framework Core.</span></span> <span data-ttu-id="326dc-204">注册服务集合的处理程序，以便它们可供`ContactsController`通过[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="326dc-204">Register the handlers with the service collection so they're available to the `ContactsController` through [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="326dc-205">将以下代码添加到末尾`ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="326dc-205">Add the following code to the end of `ConfigureServices`:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

<span data-ttu-id="326dc-206">`ContactAdministratorsAuthorizationHandler` 和`ContactManagerAuthorizationHandler`添加为单一实例。</span><span class="sxs-lookup"><span data-stu-id="326dc-206">`ContactAdministratorsAuthorizationHandler` and `ContactManagerAuthorizationHandler` are added as singletons.</span></span> <span data-ttu-id="326dc-207">它们是单一实例，因为它们不使用 EF 和所需的所有信息都位于`Context`参数的`HandleRequirementAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="326dc-207">They're singletons because they don't use EF and all the information needed is in the `Context` parameter of the `HandleRequirementAsync` method.</span></span>

## <a name="support-authorization"></a><span data-ttu-id="326dc-208">支持授权</span><span class="sxs-lookup"><span data-stu-id="326dc-208">Support authorization</span></span>

<span data-ttu-id="326dc-209">在本部分中，将更新 Razor 页面和添加操作要求类。</span><span class="sxs-lookup"><span data-stu-id="326dc-209">In this section, you update the Razor Pages and add an operations requirements class.</span></span>

### <a name="review-the-contact-operations-requirements-class"></a><span data-ttu-id="326dc-210">查看联系人操作要求类</span><span class="sxs-lookup"><span data-stu-id="326dc-210">Review the contact operations requirements class</span></span>

<span data-ttu-id="326dc-211">查看`ContactOperations`类。</span><span class="sxs-lookup"><span data-stu-id="326dc-211">Review the `ContactOperations` class.</span></span> <span data-ttu-id="326dc-212">此类包含要求应用支持：</span><span class="sxs-lookup"><span data-stu-id="326dc-212">This class contains the requirements the app supports:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a><span data-ttu-id="326dc-213">创建联系人 Razor 页面的基类</span><span class="sxs-lookup"><span data-stu-id="326dc-213">Create a base class for the Contacts Razor Pages</span></span>

<span data-ttu-id="326dc-214">创建一个包含在联系人 Razor 页面使用的服务的基类。</span><span class="sxs-lookup"><span data-stu-id="326dc-214">Create a base class that contains the services used in the contacts Razor Pages.</span></span> <span data-ttu-id="326dc-215">基类将初始化代码放在一个位置：</span><span class="sxs-lookup"><span data-stu-id="326dc-215">The base class puts the initialization code in one location:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

<span data-ttu-id="326dc-216">前面的代码：</span><span class="sxs-lookup"><span data-stu-id="326dc-216">The preceding code:</span></span>

* <span data-ttu-id="326dc-217">添加`IAuthorizationService`服务对授权处理程序的访问权限。</span><span class="sxs-lookup"><span data-stu-id="326dc-217">Adds the `IAuthorizationService` service to access to the authorization handlers.</span></span>
* <span data-ttu-id="326dc-218">将标识添加`UserManager`服务。</span><span class="sxs-lookup"><span data-stu-id="326dc-218">Adds the Identity `UserManager` service.</span></span>
* <span data-ttu-id="326dc-219">添加 `ApplicationDbContext`。</span><span class="sxs-lookup"><span data-stu-id="326dc-219">Add the `ApplicationDbContext`.</span></span>

### <a name="update-the-createmodel"></a><span data-ttu-id="326dc-220">更新 CreateModel</span><span class="sxs-lookup"><span data-stu-id="326dc-220">Update the CreateModel</span></span>

<span data-ttu-id="326dc-221">更新创建页面模型构造函数以使用`DI_BasePageModel`基类：</span><span class="sxs-lookup"><span data-stu-id="326dc-221">Update the create page model constructor to use the `DI_BasePageModel` base class:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

<span data-ttu-id="326dc-222">更新`CreateModel.OnPostAsync`方法：</span><span class="sxs-lookup"><span data-stu-id="326dc-222">Update the `CreateModel.OnPostAsync` method to:</span></span>

* <span data-ttu-id="326dc-223">添加到的用户 ID`Contact`模型。</span><span class="sxs-lookup"><span data-stu-id="326dc-223">Add the user ID to the `Contact` model.</span></span>
* <span data-ttu-id="326dc-224">调用授权处理程序，以验证用户有权创建联系人。</span><span class="sxs-lookup"><span data-stu-id="326dc-224">Call the authorization handler to verify the user has permission to create contacts.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a><span data-ttu-id="326dc-225">更新 IndexModel</span><span class="sxs-lookup"><span data-stu-id="326dc-225">Update the IndexModel</span></span>

<span data-ttu-id="326dc-226">更新`OnGetAsync`方法，以便仅被批准的联系人显示为普通用户：</span><span class="sxs-lookup"><span data-stu-id="326dc-226">Update the `OnGetAsync` method so only approved contacts are shown to general users:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a><span data-ttu-id="326dc-227">更新 EditModel</span><span class="sxs-lookup"><span data-stu-id="326dc-227">Update the EditModel</span></span>

<span data-ttu-id="326dc-228">添加授权处理程序以验证的用户拥有联系人。</span><span class="sxs-lookup"><span data-stu-id="326dc-228">Add an authorization handler to verify the user owns the contact.</span></span> <span data-ttu-id="326dc-229">正在验证资源授权，因为`[Authorize]`属性不能满足。</span><span class="sxs-lookup"><span data-stu-id="326dc-229">Because resource authorization is being validated, the `[Authorize]` attribute is not enough.</span></span> <span data-ttu-id="326dc-230">评估属性时，应用程序不具有对资源的访问。</span><span class="sxs-lookup"><span data-stu-id="326dc-230">The app doesn't have access to the resource when attributes are evaluated.</span></span> <span data-ttu-id="326dc-231">基于资源的授权必须是命令性。</span><span class="sxs-lookup"><span data-stu-id="326dc-231">Resource-based authorization must be imperative.</span></span> <span data-ttu-id="326dc-232">应用程序页面模型中加载或加载处理程序本身内获得资源的访问权限，则必须执行检查。</span><span class="sxs-lookup"><span data-stu-id="326dc-232">Checks must be performed once the app has access to the resource, either by loading it in the page model or by loading it within the handler itself.</span></span> <span data-ttu-id="326dc-233">您经常访问的资源，通过传入的资源键。</span><span class="sxs-lookup"><span data-stu-id="326dc-233">You frequently access the resource by passing in the resource key.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a><span data-ttu-id="326dc-234">更新 DeleteModel</span><span class="sxs-lookup"><span data-stu-id="326dc-234">Update the DeleteModel</span></span>

<span data-ttu-id="326dc-235">更新要使用授权处理程序来验证用户具有 delete 权限 contact 上的删除页面模型。</span><span class="sxs-lookup"><span data-stu-id="326dc-235">Update the delete page model to use the authorization handler to verify the user has delete permission on the contact.</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a><span data-ttu-id="326dc-236">将授权服务注入到视图</span><span class="sxs-lookup"><span data-stu-id="326dc-236">Inject the authorization service into the views</span></span>

<span data-ttu-id="326dc-237">目前，此 UI 显示编辑和删除的用户无法修改的联系人的链接。</span><span class="sxs-lookup"><span data-stu-id="326dc-237">Currently, the UI shows edit and delete links for contacts the user can't modify.</span></span>

<span data-ttu-id="326dc-238">注入中的授权服务*views/_viewimports.cshtml*文件，以便它可供所有视图：</span><span class="sxs-lookup"><span data-stu-id="326dc-238">Inject the authorization service in the *Views/_ViewImports.cshtml* file so it's available to all views:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

<span data-ttu-id="326dc-239">上述标记添加了多种`using`语句。</span><span class="sxs-lookup"><span data-stu-id="326dc-239">The preceding markup adds several `using` statements.</span></span>

<span data-ttu-id="326dc-240">更新**编辑**并**删除**中的链接*Pages/Contacts/Index.cshtml*以便仅在呈现具有适当权限的用户：</span><span class="sxs-lookup"><span data-stu-id="326dc-240">Update the **Edit** and **Delete** links in *Pages/Contacts/Index.cshtml* so they're only rendered for users with the appropriate permissions:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> <span data-ttu-id="326dc-241">隐藏用户没有权限更改的数据中的链接不会保护应用。</span><span class="sxs-lookup"><span data-stu-id="326dc-241">Hiding links from users that don't have permission to change data doesn't secure the app.</span></span> <span data-ttu-id="326dc-242">隐藏链接使应用更加友好的用户显示唯一有效的链接。</span><span class="sxs-lookup"><span data-stu-id="326dc-242">Hiding links makes the app more user-friendly by displaying only valid links.</span></span> <span data-ttu-id="326dc-243">用户可以 hack 生成的 Url 以调用编辑和删除操作上不拥有的数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-243">Users can hack the generated URLs to invoke edit and delete operations on data they don't own.</span></span> <span data-ttu-id="326dc-244">Razor 页面或控制器必须强制执行访问检查，以保护数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-244">The Razor Page or controller must enforce access checks to secure the data.</span></span>

### <a name="update-details"></a><span data-ttu-id="326dc-245">更新详细信息</span><span class="sxs-lookup"><span data-stu-id="326dc-245">Update Details</span></span>

<span data-ttu-id="326dc-246">更新的详细信息视图，使管理员可以批准或拒绝联系人：</span><span class="sxs-lookup"><span data-stu-id="326dc-246">Update the details view so managers can approve or reject contacts:</span></span>

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

<span data-ttu-id="326dc-247">更新详细信息页模型：</span><span class="sxs-lookup"><span data-stu-id="326dc-247">Update the details page model:</span></span>

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a><span data-ttu-id="326dc-248">添加或删除角色对用户的</span><span class="sxs-lookup"><span data-stu-id="326dc-248">Add or remove a user to a role</span></span>

<span data-ttu-id="326dc-249">请参阅[本期](https://github.com/aspnet/Docs/issues/8502)有关的信息：</span><span class="sxs-lookup"><span data-stu-id="326dc-249">See [this issue](https://github.com/aspnet/Docs/issues/8502) for information on:</span></span>

* <span data-ttu-id="326dc-250">从用户删除的特权。</span><span class="sxs-lookup"><span data-stu-id="326dc-250">Removing privileges from a user.</span></span> <span data-ttu-id="326dc-251">例如静音聊天应用程序中的用户。</span><span class="sxs-lookup"><span data-stu-id="326dc-251">For example muting a user in a chat app.</span></span>
* <span data-ttu-id="326dc-252">将权限添加到用户。</span><span class="sxs-lookup"><span data-stu-id="326dc-252">Adding privileges to a user.</span></span>

## <a name="test-the-completed-app"></a><span data-ttu-id="326dc-253">测试已完成的应用程序</span><span class="sxs-lookup"><span data-stu-id="326dc-253">Test the completed app</span></span>

<span data-ttu-id="326dc-254">如果你尚未设置设定为种子的用户帐户的密码，使用[机密管理器工具](xref:security/app-secrets#secret-manager)设置密码：</span><span class="sxs-lookup"><span data-stu-id="326dc-254">If you haven't already set a password for seeded user accounts, use the [Secret Manager tool](xref:security/app-secrets#secret-manager) to set a password:</span></span>

* <span data-ttu-id="326dc-255">选择一个强密码：使用八个或更多的字符和至少一个大写字符，其编号，和符号。</span><span class="sxs-lookup"><span data-stu-id="326dc-255">Choose a strong password: Use eight or more characters and at least one upper-case character, number, and symbol.</span></span> <span data-ttu-id="326dc-256">例如，`Passw0rd!`符合强密码要求。</span><span class="sxs-lookup"><span data-stu-id="326dc-256">For example, `Passw0rd!` meets the strong password requirements.</span></span>
* <span data-ttu-id="326dc-257">执行以下命令从项目的文件夹，其中`<PW>`的密码：</span><span class="sxs-lookup"><span data-stu-id="326dc-257">Execute the following command from the project's folder, where `<PW>` is the password:</span></span>

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

<span data-ttu-id="326dc-258">如果应用了联系人：</span><span class="sxs-lookup"><span data-stu-id="326dc-258">If the app has contacts:</span></span>

* <span data-ttu-id="326dc-259">删除所有记录中`Contact`表。</span><span class="sxs-lookup"><span data-stu-id="326dc-259">Delete all of the records in the `Contact` table.</span></span>
* <span data-ttu-id="326dc-260">重新启动应用以设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="326dc-260">Restart the app to seed the database.</span></span>

<span data-ttu-id="326dc-261">测试已完成的应用程序的简单方法是启动三个不同的浏览器 （或 incognito/InPrivate 会话）。</span><span class="sxs-lookup"><span data-stu-id="326dc-261">An easy way to test the completed app is to launch three different browsers (or incognito/InPrivate sessions).</span></span> <span data-ttu-id="326dc-262">在一个浏览器中注册一个新用户 (例如， `test@example.com`)。</span><span class="sxs-lookup"><span data-stu-id="326dc-262">In one browser, register a new user (for example, `test@example.com`).</span></span> <span data-ttu-id="326dc-263">登录到每个浏览器使用不同的用户。</span><span class="sxs-lookup"><span data-stu-id="326dc-263">Sign in to each browser with a different user.</span></span> <span data-ttu-id="326dc-264">验证以下操作：</span><span class="sxs-lookup"><span data-stu-id="326dc-264">Verify the following operations:</span></span>

* <span data-ttu-id="326dc-265">已注册的用户可以查看所有已批准的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-265">Registered users can view all of the approved contact data.</span></span>
* <span data-ttu-id="326dc-266">已注册的用户可以编辑/删除其自己的数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-266">Registered users can edit/delete their own data.</span></span>
* <span data-ttu-id="326dc-267">经理可以批准/拒绝的联系人数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-267">Managers can approve/reject contact data.</span></span> <span data-ttu-id="326dc-268">`Details`视图视图将显示**批准**并**拒绝**按钮。</span><span class="sxs-lookup"><span data-stu-id="326dc-268">The `Details` view shows **Approve** and **Reject** buttons.</span></span>
* <span data-ttu-id="326dc-269">管理员可以批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-269">Administrators can approve/reject and edit/delete all data.</span></span>

| <span data-ttu-id="326dc-270">“用户”</span><span class="sxs-lookup"><span data-stu-id="326dc-270">User</span></span>                | <span data-ttu-id="326dc-271">由应用程序进行种子设定</span><span class="sxs-lookup"><span data-stu-id="326dc-271">Seeded by the app</span></span> | <span data-ttu-id="326dc-272">选项</span><span class="sxs-lookup"><span data-stu-id="326dc-272">Options</span></span>                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | <span data-ttu-id="326dc-273">否</span><span class="sxs-lookup"><span data-stu-id="326dc-273">No</span></span>                | <span data-ttu-id="326dc-274">编辑/删除自己的数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-274">Edit/delete the own data.</span></span>                |
| manager@contoso.com | <span data-ttu-id="326dc-275">是</span><span class="sxs-lookup"><span data-stu-id="326dc-275">Yes</span></span>               | <span data-ttu-id="326dc-276">批准/拒绝和编辑/删除拥有的数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-276">Approve/reject and edit/delete own data.</span></span> |
| admin@contoso.com   | <span data-ttu-id="326dc-277">是</span><span class="sxs-lookup"><span data-stu-id="326dc-277">Yes</span></span>               | <span data-ttu-id="326dc-278">批准/拒绝和编辑/删除所有数据。</span><span class="sxs-lookup"><span data-stu-id="326dc-278">Approve/reject and edit/delete all data.</span></span> |

<span data-ttu-id="326dc-279">在管理员的浏览器中创建联系人。</span><span class="sxs-lookup"><span data-stu-id="326dc-279">Create a contact in the administrator's browser.</span></span> <span data-ttu-id="326dc-280">删除 URL 复制并编辑从管理员的联系信息。</span><span class="sxs-lookup"><span data-stu-id="326dc-280">Copy the URL for delete and edit from the administrator contact.</span></span> <span data-ttu-id="326dc-281">将下面的链接粘贴到测试用户的浏览器以验证测试用户不能执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="326dc-281">Paste these links into the test user's browser to verify the test user can't perform these operations.</span></span>

## <a name="create-the-starter-app"></a><span data-ttu-id="326dc-282">创建入门级应用</span><span class="sxs-lookup"><span data-stu-id="326dc-282">Create the starter app</span></span>

* <span data-ttu-id="326dc-283">创建名为"ContactManager"Razor 页面应用</span><span class="sxs-lookup"><span data-stu-id="326dc-283">Create a Razor Pages app named "ContactManager"</span></span>
  * <span data-ttu-id="326dc-284">创建包含应用**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="326dc-284">Create the app with **Individual User Accounts**.</span></span>
  * <span data-ttu-id="326dc-285">它命名为"ContactManager"使命名空间匹配的示例中使用的命名空间。</span><span class="sxs-lookup"><span data-stu-id="326dc-285">Name it "ContactManager" so the namespace matches the namespace used in the sample.</span></span>
  * <span data-ttu-id="326dc-286">`-uld` 指定 LocalDB，而不是 SQLite</span><span class="sxs-lookup"><span data-stu-id="326dc-286">`-uld` specifies LocalDB instead of SQLite</span></span>

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* <span data-ttu-id="326dc-287">添加*Models/Contact.cs*:</span><span class="sxs-lookup"><span data-stu-id="326dc-287">Add *Models/Contact.cs*:</span></span>

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* <span data-ttu-id="326dc-288">基架`Contact`模型。</span><span class="sxs-lookup"><span data-stu-id="326dc-288">Scaffold the `Contact` model.</span></span>
* <span data-ttu-id="326dc-289">创建初始迁移并更新数据库：</span><span class="sxs-lookup"><span data-stu-id="326dc-289">Create initial migration and update the database:</span></span>

  ```console
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* <span data-ttu-id="326dc-290">更新**ContactManager**中的定位点*pages/_layout.cshtml*文件：</span><span class="sxs-lookup"><span data-stu-id="326dc-290">Update the **ContactManager** anchor in the *Pages/_Layout.cshtml* file:</span></span>

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* <span data-ttu-id="326dc-291">测试应用程序的创建、 编辑和删除联系人</span><span class="sxs-lookup"><span data-stu-id="326dc-291">Test the app by creating, editing, and deleting a contact</span></span>

### <a name="seed-the-database"></a><span data-ttu-id="326dc-292">设定数据库种子</span><span class="sxs-lookup"><span data-stu-id="326dc-292">Seed the database</span></span>

<span data-ttu-id="326dc-293">添加[SeedData](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)类来*数据*文件夹。</span><span class="sxs-lookup"><span data-stu-id="326dc-293">Add the [SeedData](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs) class to the *Data* folder.</span></span>

<span data-ttu-id="326dc-294">调用`SeedData.Initialize`从`Main`:</span><span class="sxs-lookup"><span data-stu-id="326dc-294">Call `SeedData.Initialize` from `Main`:</span></span>

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

<span data-ttu-id="326dc-295">测试应用程序设定数据库种子。</span><span class="sxs-lookup"><span data-stu-id="326dc-295">Test that the app seeded the database.</span></span> <span data-ttu-id="326dc-296">如果联系人 DB 中有任何行，则不会运行 seed 方法。</span><span class="sxs-lookup"><span data-stu-id="326dc-296">If there are any rows in the contact DB, the seed method doesn't run.</span></span>

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a><span data-ttu-id="326dc-297">其他资源</span><span class="sxs-lookup"><span data-stu-id="326dc-297">Additional resources</span></span>

* [<span data-ttu-id="326dc-298">生成 Azure 应用服务中的.NET Core 和 SQL 数据库 web 应用</span><span class="sxs-lookup"><span data-stu-id="326dc-298">Build a .NET Core and SQL Database web app in Azure App Service</span></span>](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* <span data-ttu-id="326dc-299">[ASP.NET Core 授权实验室](https://github.com/blowdart/AspNetAuthorizationWorkshop)。</span><span class="sxs-lookup"><span data-stu-id="326dc-299">[ASP.NET Core Authorization Lab](https://github.com/blowdart/AspNetAuthorizationWorkshop).</span></span> <span data-ttu-id="326dc-300">此实验室进入的安全功能，本教程中介绍的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="326dc-300">This lab goes into more detail on the security features introduced in this tutorial.</span></span>
* <xref:security/authorization/introduction>
* [<span data-ttu-id="326dc-301">基于自定义策略的授权</span><span class="sxs-lookup"><span data-stu-id="326dc-301">Custom policy-based authorization</span></span>](xref:security/authorization/policies)

::: moniker-end
