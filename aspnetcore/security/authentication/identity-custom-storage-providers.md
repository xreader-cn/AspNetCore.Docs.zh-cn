---
title: ASP.NET Core 的自定义存储提供程序Identity
author: ardalis
description: 了解如何为 ASP.NET Core 配置自定义存储提供程序 Identity 。
ms.author: riande
ms.custom: mvc
ms.date: 07/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: 792a9e5f776e345fbee5726b676fe148ecaf1657
ms.sourcegitcommit: 6a71b560d897e13ad5b61d07afe4fcb57f8ef6dc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2020
ms.locfileid: "84106580"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a><span data-ttu-id="4f834-103">ASP.NET Core 的自定义存储提供程序Identity</span><span class="sxs-lookup"><span data-stu-id="4f834-103">Custom storage providers for ASP.NET Core Identity</span></span>

<span data-ttu-id="4f834-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="4f834-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="4f834-105">ASP.NET Core Identity 是一种可扩展系统，可让你创建自定义存储提供程序，并将其连接到你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="4f834-105">ASP.NET Core Identity is an extensible system which enables you to create a custom storage provider and connect it to your app.</span></span> <span data-ttu-id="4f834-106">本主题介绍如何为 ASP.NET Core 创建自定义的存储提供程序 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4f834-106">This topic describes how to create a customized storage provider for ASP.NET Core Identity.</span></span> <span data-ttu-id="4f834-107">它介绍了用于创建自己的存储提供程序的重要概念，但并不是分步演练。</span><span class="sxs-lookup"><span data-stu-id="4f834-107">It covers the important concepts for creating your own storage provider, but isn't a step-by-step walkthrough.</span></span>

<span data-ttu-id="4f834-108">[查看或下载 GitHub 中的示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample)。</span><span class="sxs-lookup"><span data-stu-id="4f834-108">[View or download sample from GitHub](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/identity/sample).</span></span>

## <a name="introduction"></a><span data-ttu-id="4f834-109">简介</span><span class="sxs-lookup"><span data-stu-id="4f834-109">Introduction</span></span>

<span data-ttu-id="4f834-110">默认情况下，ASP.NET Core Identity 系统使用 Entity Framework Core 将用户信息存储在 SQL Server 数据库中。</span><span class="sxs-lookup"><span data-stu-id="4f834-110">By default, the ASP.NET Core Identity system stores user information in a SQL Server database using Entity Framework Core.</span></span> <span data-ttu-id="4f834-111">对于许多应用程序而言，这种方法的效果很好。</span><span class="sxs-lookup"><span data-stu-id="4f834-111">For many apps, this approach works well.</span></span> <span data-ttu-id="4f834-112">但是，你可能希望使用不同的持久性机制或数据架构。</span><span class="sxs-lookup"><span data-stu-id="4f834-112">However, you may prefer to use a different persistence mechanism or data schema.</span></span> <span data-ttu-id="4f834-113">例如：</span><span class="sxs-lookup"><span data-stu-id="4f834-113">For example:</span></span>

* <span data-ttu-id="4f834-114">使用[Azure 表存储](/azure/storage/)或其他数据存储。</span><span class="sxs-lookup"><span data-stu-id="4f834-114">You use [Azure Table Storage](/azure/storage/) or another data store.</span></span>
* <span data-ttu-id="4f834-115">数据库表具有不同的结构。</span><span class="sxs-lookup"><span data-stu-id="4f834-115">Your database tables have a different structure.</span></span> 
* <span data-ttu-id="4f834-116">你可能想要使用不同的数据访问方法，例如[Dapper](https://github.com/StackExchange/Dapper)。</span><span class="sxs-lookup"><span data-stu-id="4f834-116">You may wish to use a different data access approach, such as [Dapper](https://github.com/StackExchange/Dapper).</span></span> 

<span data-ttu-id="4f834-117">在上述每种情况下，都可以为存储机制编写自定义的提供程序，并将该提供程序插入到应用程序中。</span><span class="sxs-lookup"><span data-stu-id="4f834-117">In each of these cases, you can write a customized provider for your storage mechanism and plug that provider into your app.</span></span>

<span data-ttu-id="4f834-118">ASP.NET Core Identity 包含在 Visual Studio 中具有 "单独用户帐户" 选项的项目模板中。</span><span class="sxs-lookup"><span data-stu-id="4f834-118">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="4f834-119">使用 .NET Core CLI 时，请添加 `-au Individual` ：</span><span class="sxs-lookup"><span data-stu-id="4f834-119">When using the .NET Core CLI, add `-au Individual`:</span></span>

```dotnetcli
dotnet new mvc -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a><span data-ttu-id="4f834-120">ASP.NET Core Identity 体系结构</span><span class="sxs-lookup"><span data-stu-id="4f834-120">The ASP.NET Core Identity architecture</span></span>

<span data-ttu-id="4f834-121">ASP.NET Core Identity 由名为管理器和存储的类组成。</span><span class="sxs-lookup"><span data-stu-id="4f834-121">ASP.NET Core Identity consists of classes called managers and stores.</span></span> <span data-ttu-id="4f834-122">*管理*层是应用程序开发人员用来执行操作（如创建用户）的高级类 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4f834-122">*Managers* are high-level classes which an app developer uses to perform operations, such as creating an Identity user.</span></span> <span data-ttu-id="4f834-123">*存储*是用于指定如何保存实体（如用户和角色）的较低级别类。</span><span class="sxs-lookup"><span data-stu-id="4f834-123">*Stores* are lower-level classes that specify how entities, such as users and roles, are persisted.</span></span> <span data-ttu-id="4f834-124">存储遵循存储库模式，并与持久性机制紧密耦合。</span><span class="sxs-lookup"><span data-stu-id="4f834-124">Stores follow the repository pattern and are closely coupled with the persistence mechanism.</span></span> <span data-ttu-id="4f834-125">管理器与存储分离，这意味着，你可以在不更改应用程序代码的情况下替换持久性机制（配置除外）。</span><span class="sxs-lookup"><span data-stu-id="4f834-125">Managers are decoupled from stores, which means you can replace the persistence mechanism without changing your application code (except for configuration).</span></span>

<span data-ttu-id="4f834-126">下图显示了 web 应用如何与管理器进行交互，同时存储与数据访问层交互。</span><span class="sxs-lookup"><span data-stu-id="4f834-126">The following diagram shows how a web app interacts with the managers, while stores interact with the data access layer.</span></span>

![ASP.NET Core 应用使用管理器（例如，"UserManager"、"RoleManager"）。](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

<span data-ttu-id="4f834-129">若要创建自定义存储提供程序，请创建数据源、数据访问层以及与此数据访问层交互的存储类（上图中的绿色和灰色框）。</span><span class="sxs-lookup"><span data-stu-id="4f834-129">To create a custom storage provider, create the data source, the data access layer, and the store classes that interact with this data access layer (the green and grey boxes in the diagram above).</span></span> <span data-ttu-id="4f834-130">不需要自定义管理器或与之交互的应用程序代码（上面的蓝色框）。</span><span class="sxs-lookup"><span data-stu-id="4f834-130">You don't need to customize the managers or your app code that interacts with them (the blue boxes above).</span></span>

<span data-ttu-id="4f834-131">在创建的新实例时， `UserManager` 或 `RoleManager` 提供 user 类的类型并将 store 类的实例作为参数传递。</span><span class="sxs-lookup"><span data-stu-id="4f834-131">When creating a new instance of `UserManager` or `RoleManager` you provide the type of the user class and pass an instance of the store class as an argument.</span></span> <span data-ttu-id="4f834-132">此方法使你能够将自定义类插入 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="4f834-132">This approach enables you to plug your customized classes into ASP.NET Core.</span></span> 

<span data-ttu-id="4f834-133">[重新配置应用程序以使用新的存储提供程序](#reconfigure-app-to-use-a-new-storage-provider)演示如何实例化 `UserManager` 和 `RoleManager` 使用自定义存储。</span><span class="sxs-lookup"><span data-stu-id="4f834-133">[Reconfigure app to use new storage provider](#reconfigure-app-to-use-a-new-storage-provider) shows how to instantiate `UserManager` and `RoleManager` with a customized store.</span></span>

## <a name="aspnet-core-identity-stores-data-types"></a><span data-ttu-id="4f834-134">ASP.NET Core Identity 存储数据类型</span><span class="sxs-lookup"><span data-stu-id="4f834-134">ASP.NET Core Identity stores data types</span></span>

<span data-ttu-id="4f834-135">[ASP.NET Core Identity ](https://github.com/aspnet/identity)以下部分详细介绍了数据类型：</span><span class="sxs-lookup"><span data-stu-id="4f834-135">[ASP.NET Core Identity](https://github.com/aspnet/identity) data types are detailed in the following sections:</span></span>

### <a name="users"></a><span data-ttu-id="4f834-136">用户</span><span class="sxs-lookup"><span data-stu-id="4f834-136">Users</span></span>

<span data-ttu-id="4f834-137">网站的已注册用户。</span><span class="sxs-lookup"><span data-stu-id="4f834-137">Registered users of your web site.</span></span> <span data-ttu-id="4f834-138">可以扩展[IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)类型，也可以将其用作你自己的自定义类型的示例。</span><span class="sxs-lookup"><span data-stu-id="4f834-138">The [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) type may be extended or used as an example for your own custom type.</span></span> <span data-ttu-id="4f834-139">无需从特定类型继承即可实现您自己的自定义标识存储解决方案。</span><span class="sxs-lookup"><span data-stu-id="4f834-139">You don't need to inherit from a particular type to implement your own custom identity storage solution.</span></span>

### <a name="user-claims"></a><span data-ttu-id="4f834-140">用户声明</span><span class="sxs-lookup"><span data-stu-id="4f834-140">User Claims</span></span>

<span data-ttu-id="4f834-141">有关表示用户标识的用户的一组语句（或[声明](/dotnet/api/system.security.claims.claim)）。</span><span class="sxs-lookup"><span data-stu-id="4f834-141">A set of statements (or [Claims](/dotnet/api/system.security.claims.claim)) about the user that represent the user's identity.</span></span> <span data-ttu-id="4f834-142">可以启用用户标识的更大表达式，而不能通过角色来实现。</span><span class="sxs-lookup"><span data-stu-id="4f834-142">Can enable greater expression of the user's identity than can be achieved through roles.</span></span>

### <a name="user-logins"></a><span data-ttu-id="4f834-143">用户登录</span><span class="sxs-lookup"><span data-stu-id="4f834-143">User Logins</span></span>

<span data-ttu-id="4f834-144">有关在用户登录时要使用的外部身份验证提供程序（如 Facebook 或 Microsoft 帐户）的信息。</span><span class="sxs-lookup"><span data-stu-id="4f834-144">Information about the external authentication provider (like Facebook or a Microsoft account) to use when logging in a user.</span></span> [<span data-ttu-id="4f834-145">示例</span><span class="sxs-lookup"><span data-stu-id="4f834-145">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a><span data-ttu-id="4f834-146">角色</span><span class="sxs-lookup"><span data-stu-id="4f834-146">Roles</span></span>

<span data-ttu-id="4f834-147">站点的授权组。</span><span class="sxs-lookup"><span data-stu-id="4f834-147">Authorization groups for your site.</span></span> <span data-ttu-id="4f834-148">包括角色 Id 和角色名称（如 "管理员" 或 "Employee"）。</span><span class="sxs-lookup"><span data-stu-id="4f834-148">Includes the role Id and role name (like "Admin" or "Employee").</span></span> [<span data-ttu-id="4f834-149">示例</span><span class="sxs-lookup"><span data-stu-id="4f834-149">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a><span data-ttu-id="4f834-150">数据访问层</span><span class="sxs-lookup"><span data-stu-id="4f834-150">The data access layer</span></span>

<span data-ttu-id="4f834-151">本主题假定您熟悉要使用的持久性机制以及如何创建该机制的实体。</span><span class="sxs-lookup"><span data-stu-id="4f834-151">This topic assumes you are familiar with the persistence mechanism that you are going to use and how to create entities for that mechanism.</span></span> <span data-ttu-id="4f834-152">本主题不提供有关如何创建存储库或数据访问类的详细信息;在使用 ASP.NET Core 时，它提供了有关设计决策的一些建议 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4f834-152">This topic doesn't provide details about how to create the repositories or data access classes; it provides some suggestions about design decisions when working with ASP.NET Core Identity.</span></span>

<span data-ttu-id="4f834-153">设计自定义存储提供程序的数据访问层时，有很多自由。</span><span class="sxs-lookup"><span data-stu-id="4f834-153">You have a lot of freedom when designing the data access layer for a customized store provider.</span></span> <span data-ttu-id="4f834-154">你只需为你打算在应用程序中使用的功能创建持久性机制。</span><span class="sxs-lookup"><span data-stu-id="4f834-154">You only need to create persistence mechanisms for features that you intend to use in your app.</span></span> <span data-ttu-id="4f834-155">例如，如果你未在应用中使用角色，则无需为角色或用户角色关联创建存储。</span><span class="sxs-lookup"><span data-stu-id="4f834-155">For example, if you are not using roles in your app, you don't need to create storage for roles or user role associations.</span></span> <span data-ttu-id="4f834-156">你的技术和现有基础结构可能需要结构，这与 ASP.NET Core 的默认实现非常不同 Identity 。</span><span class="sxs-lookup"><span data-stu-id="4f834-156">Your technology and existing infrastructure may require a structure that's very different from the default implementation of ASP.NET Core Identity.</span></span> <span data-ttu-id="4f834-157">在数据访问层中，提供用于处理存储实现结构的逻辑。</span><span class="sxs-lookup"><span data-stu-id="4f834-157">In your data access layer, you provide the logic to work with the structure of your storage implementation.</span></span>

<span data-ttu-id="4f834-158">数据访问层提供了用于将数据从 ASP.NET Core 保存 Identity 到数据源的逻辑。</span><span class="sxs-lookup"><span data-stu-id="4f834-158">The data access layer provides the logic to save the data from ASP.NET Core Identity to a data source.</span></span> <span data-ttu-id="4f834-159">自定义存储提供程序的数据访问层可能包含以下类来存储用户和角色信息。</span><span class="sxs-lookup"><span data-stu-id="4f834-159">The data access layer for your customized storage provider might include the following classes to store user and role information.</span></span>

### <a name="context-class"></a><span data-ttu-id="4f834-160">Context 类</span><span class="sxs-lookup"><span data-stu-id="4f834-160">Context class</span></span>

<span data-ttu-id="4f834-161">封装信息以连接到永久性机制并执行查询。</span><span class="sxs-lookup"><span data-stu-id="4f834-161">Encapsulates the information to connect to your persistence mechanism and execute queries.</span></span> <span data-ttu-id="4f834-162">几个数据类需要此类的实例，通常通过依赖关系注入来提供此类实例。</span><span class="sxs-lookup"><span data-stu-id="4f834-162">Several data classes require an instance of this class, typically provided through dependency injection.</span></span> <span data-ttu-id="4f834-163">[示例](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)。</span><span class="sxs-lookup"><span data-stu-id="4f834-163">[Example](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).</span></span>

### <a name="user-storage"></a><span data-ttu-id="4f834-164">用户存储</span><span class="sxs-lookup"><span data-stu-id="4f834-164">User Storage</span></span>

<span data-ttu-id="4f834-165">存储和检索用户信息（例如用户名和密码哈希）。</span><span class="sxs-lookup"><span data-stu-id="4f834-165">Stores and retrieves user information (such as user name and password hash).</span></span> [<span data-ttu-id="4f834-166">示例</span><span class="sxs-lookup"><span data-stu-id="4f834-166">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a><span data-ttu-id="4f834-167">角色存储</span><span class="sxs-lookup"><span data-stu-id="4f834-167">Role Storage</span></span>

<span data-ttu-id="4f834-168">存储和检索角色信息（如角色名称）。</span><span class="sxs-lookup"><span data-stu-id="4f834-168">Stores and retrieves role information (such as the role name).</span></span> [<span data-ttu-id="4f834-169">示例</span><span class="sxs-lookup"><span data-stu-id="4f834-169">Example</span></span>](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a><span data-ttu-id="4f834-170">UserClaims 存储</span><span class="sxs-lookup"><span data-stu-id="4f834-170">UserClaims Storage</span></span>

<span data-ttu-id="4f834-171">存储和检索用户声明信息（如声明类型和值）。</span><span class="sxs-lookup"><span data-stu-id="4f834-171">Stores and retrieves user claim information (such as the claim type and value).</span></span> [<span data-ttu-id="4f834-172">示例</span><span class="sxs-lookup"><span data-stu-id="4f834-172">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a><span data-ttu-id="4f834-173">UserLogins 存储</span><span class="sxs-lookup"><span data-stu-id="4f834-173">UserLogins Storage</span></span>

<span data-ttu-id="4f834-174">存储和检索用户登录信息（如外部身份验证提供程序）。</span><span class="sxs-lookup"><span data-stu-id="4f834-174">Stores and retrieves user login information (such as an external authentication provider).</span></span> [<span data-ttu-id="4f834-175">示例</span><span class="sxs-lookup"><span data-stu-id="4f834-175">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a><span data-ttu-id="4f834-176">UserRole 存储</span><span class="sxs-lookup"><span data-stu-id="4f834-176">UserRole Storage</span></span>

<span data-ttu-id="4f834-177">存储和检索为哪些用户分配了哪些角色。</span><span class="sxs-lookup"><span data-stu-id="4f834-177">Stores and retrieves which roles are assigned to which users.</span></span> [<span data-ttu-id="4f834-178">示例</span><span class="sxs-lookup"><span data-stu-id="4f834-178">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

<span data-ttu-id="4f834-179">**提示：** 仅实现你打算在应用程序中使用的类。</span><span class="sxs-lookup"><span data-stu-id="4f834-179">**TIP:** Only implement the classes you intend to use in your app.</span></span>

<span data-ttu-id="4f834-180">在数据访问类中，提供代码来执行持久性机制的数据操作。</span><span class="sxs-lookup"><span data-stu-id="4f834-180">In the data access classes, provide code to perform data operations for your persistence mechanism.</span></span> <span data-ttu-id="4f834-181">例如，在自定义提供程序中，你可能具有以下代码，以便在*store*类中创建新用户：</span><span class="sxs-lookup"><span data-stu-id="4f834-181">For example, within a custom provider, you might have the following code to create a new user in the *store* class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

<span data-ttu-id="4f834-182">用于创建用户的实现逻辑位于 `_usersTable.CreateAsync` 方法中，如下所示。</span><span class="sxs-lookup"><span data-stu-id="4f834-182">The implementation logic for creating the user is in the `_usersTable.CreateAsync` method, shown below.</span></span>

## <a name="customize-the-user-class"></a><span data-ttu-id="4f834-183">自定义用户类</span><span class="sxs-lookup"><span data-stu-id="4f834-183">Customize the user class</span></span>

<span data-ttu-id="4f834-184">实现存储提供程序时，创建一个与[IdentityUser 类](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)等效的用户类。</span><span class="sxs-lookup"><span data-stu-id="4f834-184">When implementing a storage provider, create a user class which is equivalent to the [IdentityUser class](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).</span></span>

<span data-ttu-id="4f834-185">用户类至少必须包括 `Id` 和 `UserName` 属性。</span><span class="sxs-lookup"><span data-stu-id="4f834-185">At a minimum, your user class must include an `Id` and a `UserName` property.</span></span>

<span data-ttu-id="4f834-186">`IdentityUser`类定义 `UserManager` 执行请求的操作时调用的属性。</span><span class="sxs-lookup"><span data-stu-id="4f834-186">The `IdentityUser` class defines the properties that the `UserManager` calls when performing requested operations.</span></span> <span data-ttu-id="4f834-187">属性的默认类型 `Id` 是字符串，但您可以从继承 `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` 并指定其他类型。</span><span class="sxs-lookup"><span data-stu-id="4f834-187">The default type of the `Id` property is a string, but you can inherit from `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` and specify a different type.</span></span> <span data-ttu-id="4f834-188">框架需要存储实现来处理数据类型转换。</span><span class="sxs-lookup"><span data-stu-id="4f834-188">The framework expects the storage implementation to handle data type conversions.</span></span>

## <a name="customize-the-user-store"></a><span data-ttu-id="4f834-189">自定义用户存储</span><span class="sxs-lookup"><span data-stu-id="4f834-189">Customize the user store</span></span>

<span data-ttu-id="4f834-190">创建一个 `UserStore` 类，该类提供针对用户的所有数据操作的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-190">Create a `UserStore` class that provides the methods for all data operations on the user.</span></span> <span data-ttu-id="4f834-191">此类等效于[UserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1)类。</span><span class="sxs-lookup"><span data-stu-id="4f834-191">This class is equivalent to the [UserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) class.</span></span> <span data-ttu-id="4f834-192">在 `UserStore` 类中，实现 `IUserStore<TUser>` 和所需的可选接口。</span><span class="sxs-lookup"><span data-stu-id="4f834-192">In your `UserStore` class, implement `IUserStore<TUser>` and the optional interfaces required.</span></span> <span data-ttu-id="4f834-193">根据应用中提供的功能，选择要实现的可选接口。</span><span class="sxs-lookup"><span data-stu-id="4f834-193">You select which optional interfaces to implement based on the functionality provided in your app.</span></span>

### <a name="optional-interfaces"></a><span data-ttu-id="4f834-194">可选接口</span><span class="sxs-lookup"><span data-stu-id="4f834-194">Optional interfaces</span></span>

* [<span data-ttu-id="4f834-195">IUserRoleStore</span><span class="sxs-lookup"><span data-stu-id="4f834-195">IUserRoleStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [<span data-ttu-id="4f834-196">IUserClaimStore</span><span class="sxs-lookup"><span data-stu-id="4f834-196">IUserClaimStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [<span data-ttu-id="4f834-197">IUserPasswordStore</span><span class="sxs-lookup"><span data-stu-id="4f834-197">IUserPasswordStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [<span data-ttu-id="4f834-198">IUserSecurityStampStore</span><span class="sxs-lookup"><span data-stu-id="4f834-198">IUserSecurityStampStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [<span data-ttu-id="4f834-199">IUserEmailStore</span><span class="sxs-lookup"><span data-stu-id="4f834-199">IUserEmailStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [<span data-ttu-id="4f834-200">IUserPhoneNumberStore</span><span class="sxs-lookup"><span data-stu-id="4f834-200">IUserPhoneNumberStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [<span data-ttu-id="4f834-201">IQueryableUserStore</span><span class="sxs-lookup"><span data-stu-id="4f834-201">IQueryableUserStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [<span data-ttu-id="4f834-202">IUserLoginStore</span><span class="sxs-lookup"><span data-stu-id="4f834-202">IUserLoginStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [<span data-ttu-id="4f834-203">IUserTwoFactorStore</span><span class="sxs-lookup"><span data-stu-id="4f834-203">IUserTwoFactorStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [<span data-ttu-id="4f834-204">IUserLockoutStore</span><span class="sxs-lookup"><span data-stu-id="4f834-204">IUserLockoutStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

<span data-ttu-id="4f834-205">可选接口继承自 `IUserStore<TUser>` 。</span><span class="sxs-lookup"><span data-stu-id="4f834-205">The optional interfaces inherit from `IUserStore<TUser>`.</span></span> <span data-ttu-id="4f834-206">可以在[示例应用](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)中查看部分实现的示例用户存储。</span><span class="sxs-lookup"><span data-stu-id="4f834-206">You can see a partially implemented sample user store in the [sample app](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).</span></span>

<span data-ttu-id="4f834-207">在 `UserStore` 类中，可以使用您创建的数据访问类来执行操作。</span><span class="sxs-lookup"><span data-stu-id="4f834-207">Within the `UserStore` class, you use the data access classes that you created to perform operations.</span></span> <span data-ttu-id="4f834-208">使用依赖关系注入传入它们。</span><span class="sxs-lookup"><span data-stu-id="4f834-208">These are passed in using dependency injection.</span></span> <span data-ttu-id="4f834-209">例如，在使用 Dapper 实现的 SQL Server 中， `UserStore` 类具有方法，该 `CreateAsync` 方法使用的实例 `DapperUsersTable` 插入新记录：</span><span class="sxs-lookup"><span data-stu-id="4f834-209">For example, in the SQL Server with Dapper implementation, the `UserStore` class has the `CreateAsync` method which uses an instance of `DapperUsersTable` to insert a new record:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a><span data-ttu-id="4f834-210">自定义用户存储时要实现的接口</span><span class="sxs-lookup"><span data-stu-id="4f834-210">Interfaces to implement when customizing user store</span></span>

* <span data-ttu-id="4f834-211">**IUserStore**</span><span class="sxs-lookup"><span data-stu-id="4f834-211">**IUserStore**</span></span>  
 <span data-ttu-id="4f834-212">[IUserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1)接口是必须在用户存储中实现的唯一接口。</span><span class="sxs-lookup"><span data-stu-id="4f834-212">The [IUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) interface is the only interface you must implement in the user store.</span></span> <span data-ttu-id="4f834-213">它定义用于创建、更新、删除和检索用户的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-213">It defines methods for creating, updating, deleting, and retrieving users.</span></span>
* <span data-ttu-id="4f834-214">**IUserClaimStore**</span><span class="sxs-lookup"><span data-stu-id="4f834-214">**IUserClaimStore**</span></span>  
 <span data-ttu-id="4f834-215">[IUserClaimStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)接口定义为启用用户声明而实现的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-215">The [IUserClaimStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) interface defines the methods you implement to enable user claims.</span></span> <span data-ttu-id="4f834-216">它包含添加、删除和检索用户声明的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-216">It contains methods for adding, removing and retrieving user claims.</span></span>
* <span data-ttu-id="4f834-217">**IUserLoginStore**</span><span class="sxs-lookup"><span data-stu-id="4f834-217">**IUserLoginStore**</span></span>  
 <span data-ttu-id="4f834-218">[IUserLoginStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)定义用于启用外部身份验证提供程序的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-218">The [IUserLoginStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) defines the methods you implement to enable external authentication providers.</span></span> <span data-ttu-id="4f834-219">它包含添加、删除和检索用户登录名的方法，以及用于根据登录信息检索用户的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-219">It contains methods for adding, removing and retrieving user logins, and a method for retrieving a user based on the login information.</span></span>
* <span data-ttu-id="4f834-220">**IUserRoleStore**</span><span class="sxs-lookup"><span data-stu-id="4f834-220">**IUserRoleStore**</span></span>  
 <span data-ttu-id="4f834-221">[IUserRoleStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)接口定义你实现的方法，用于将用户映射到角色。</span><span class="sxs-lookup"><span data-stu-id="4f834-221">The [IUserRoleStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) interface defines the methods you implement to map a user to a role.</span></span> <span data-ttu-id="4f834-222">它包含添加、删除和检索用户角色的方法，以及用于检查是否向用户分配了角色的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-222">It contains methods to add, remove, and retrieve a user's roles, and a method to check if a user is assigned to a role.</span></span>
* <span data-ttu-id="4f834-223">**IUserPasswordStore**</span><span class="sxs-lookup"><span data-stu-id="4f834-223">**IUserPasswordStore**</span></span>  
 <span data-ttu-id="4f834-224">[IUserPasswordStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)接口定义你实现的用于保存哈希密码的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-224">The [IUserPasswordStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) interface defines the methods you implement to persist hashed passwords.</span></span> <span data-ttu-id="4f834-225">它包含用于获取和设置哈希密码的方法，以及一个指示用户是否已设置密码的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-225">It contains methods for getting and setting the hashed password, and a method that indicates whether the user has set a password.</span></span>
* <span data-ttu-id="4f834-226">**IUserSecurityStampStore**</span><span class="sxs-lookup"><span data-stu-id="4f834-226">**IUserSecurityStampStore**</span></span>  
 <span data-ttu-id="4f834-227">[IUserSecurityStampStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)接口定义你实现的方法，以使用安全戳记来指示用户的帐户信息是否已更改。</span><span class="sxs-lookup"><span data-stu-id="4f834-227">The [IUserSecurityStampStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) interface defines the methods you implement to use a security stamp for indicating whether the user's account information has changed.</span></span> <span data-ttu-id="4f834-228">当用户更改密码或添加或删除登录名时，此戳记会更新。</span><span class="sxs-lookup"><span data-stu-id="4f834-228">This stamp is updated when a user changes the password, or adds or removes logins.</span></span> <span data-ttu-id="4f834-229">它包含用于获取和设置安全标记的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-229">It contains methods for getting and setting the security stamp.</span></span>
* <span data-ttu-id="4f834-230">**IUserTwoFactorStore**</span><span class="sxs-lookup"><span data-stu-id="4f834-230">**IUserTwoFactorStore**</span></span>  
 <span data-ttu-id="4f834-231">[IUserTwoFactorStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)接口定义你实现的方法，以支持双重身份验证。</span><span class="sxs-lookup"><span data-stu-id="4f834-231">The [IUserTwoFactorStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) interface defines the methods you implement to support two factor authentication.</span></span> <span data-ttu-id="4f834-232">它包含的方法可用于获取和设置是否为用户启用了双因素身份验证。</span><span class="sxs-lookup"><span data-stu-id="4f834-232">It contains methods for getting and setting whether two factor authentication is enabled for a user.</span></span>
* <span data-ttu-id="4f834-233">**IUserPhoneNumberStore**</span><span class="sxs-lookup"><span data-stu-id="4f834-233">**IUserPhoneNumberStore**</span></span>  
 <span data-ttu-id="4f834-234">[IUserPhoneNumberStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)接口定义你为存储用户电话号码而实现的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-234">The [IUserPhoneNumberStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) interface defines the methods you implement to store user phone numbers.</span></span> <span data-ttu-id="4f834-235">它包含的方法可用于获取和设置电话号码，以及电话号码是否已确认。</span><span class="sxs-lookup"><span data-stu-id="4f834-235">It contains methods for getting and setting the phone number and whether the phone number is confirmed.</span></span>
* <span data-ttu-id="4f834-236">**IUserEmailStore**</span><span class="sxs-lookup"><span data-stu-id="4f834-236">**IUserEmailStore**</span></span>  
 <span data-ttu-id="4f834-237">[IUserEmailStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)接口定义你实现的用于存储用户电子邮件地址的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-237">The [IUserEmailStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) interface defines the methods you implement to store user email addresses.</span></span> <span data-ttu-id="4f834-238">它包含用于获取和设置电子邮件地址以及是否已确认电子邮件的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-238">It contains methods for getting and setting the email address and whether the email is confirmed.</span></span>
* <span data-ttu-id="4f834-239">**IUserLockoutStore**</span><span class="sxs-lookup"><span data-stu-id="4f834-239">**IUserLockoutStore**</span></span>  
 <span data-ttu-id="4f834-240">[IUserLockoutStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)接口定义用于存储有关锁定帐户的信息的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-240">The [IUserLockoutStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) interface defines the methods you implement to store information about locking an account.</span></span> <span data-ttu-id="4f834-241">它包含用于跟踪失败的访问尝试和锁定的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-241">It contains methods for tracking failed access attempts and lockouts.</span></span>
* <span data-ttu-id="4f834-242">**IQueryableUserStore**</span><span class="sxs-lookup"><span data-stu-id="4f834-242">**IQueryableUserStore**</span></span>  
 <span data-ttu-id="4f834-243">[IQueryableUserStore &lt; TUser &gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)接口定义为提供可查询用户存储而实现的成员。</span><span class="sxs-lookup"><span data-stu-id="4f834-243">The [IQueryableUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) interface defines the members you implement to provide a queryable user store.</span></span>

<span data-ttu-id="4f834-244">只需实现应用程序中所需的接口。</span><span class="sxs-lookup"><span data-stu-id="4f834-244">You implement only the interfaces that are needed in your app.</span></span> <span data-ttu-id="4f834-245">例如：</span><span class="sxs-lookup"><span data-stu-id="4f834-245">For example:</span></span>

```csharp
public class UserStore : IUserStore<IdentityUser>,
                         IUserClaimStore<IdentityUser>,
                         IUserLoginStore<IdentityUser>,
                         IUserRoleStore<IdentityUser>,
                         IUserPasswordStore<IdentityUser>,
                         IUserSecurityStampStore<IdentityUser>
{
    // interface implementations not shown
}
```

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a><span data-ttu-id="4f834-246">IdentityUserClaim、IdentityUserLogin 和 IdentityUserRole</span><span class="sxs-lookup"><span data-stu-id="4f834-246">IdentityUserClaim, IdentityUserLogin, and IdentityUserRole</span></span>

<span data-ttu-id="4f834-247">`Microsoft.AspNet.Identity.EntityFramework`命名空间包含[IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1)、 [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)和[IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1)类的实现。</span><span class="sxs-lookup"><span data-stu-id="4f834-247">The `Microsoft.AspNet.Identity.EntityFramework` namespace contains implementations of the [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), and [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) classes.</span></span> <span data-ttu-id="4f834-248">如果使用这些功能，可能需要创建自己版本的这些类并定义应用的属性。</span><span class="sxs-lookup"><span data-stu-id="4f834-248">If you are using these features, you may want to create your own versions of these classes and define the properties for your app.</span></span> <span data-ttu-id="4f834-249">但是，在执行基本操作（如添加或删除用户的声明）时，如果不将这些实体加载到内存中，则可能会更有效。</span><span class="sxs-lookup"><span data-stu-id="4f834-249">However, sometimes it's more efficient to not load these entities into memory when performing basic operations (such as adding or removing a user's claim).</span></span> <span data-ttu-id="4f834-250">而后端存储类可以直接对数据源执行这些操作。</span><span class="sxs-lookup"><span data-stu-id="4f834-250">Instead, the backend store classes can execute these operations directly on the data source.</span></span> <span data-ttu-id="4f834-251">例如， `UserStore.GetClaimsAsync` 方法可以调用 `userClaimTable.FindByUserId(user.Id)` 方法来对该表直接执行查询，并返回声明列表。</span><span class="sxs-lookup"><span data-stu-id="4f834-251">For example, the `UserStore.GetClaimsAsync` method can call the `userClaimTable.FindByUserId(user.Id)` method to execute a query on that table directly and return a list of claims.</span></span>

## <a name="customize-the-role-class"></a><span data-ttu-id="4f834-252">自定义 role 类</span><span class="sxs-lookup"><span data-stu-id="4f834-252">Customize the role class</span></span>

<span data-ttu-id="4f834-253">实现角色存储提供程序时，可以创建自定义角色类型。</span><span class="sxs-lookup"><span data-stu-id="4f834-253">When implementing a role storage provider, you can create a custom role type.</span></span> <span data-ttu-id="4f834-254">它不需要实现特定接口，但它必须具有 `Id` ，并且通常具有一个 `Name` 属性。</span><span class="sxs-lookup"><span data-stu-id="4f834-254">It need not implement a particular interface, but it must have an `Id` and typically it will have a `Name` property.</span></span>

<span data-ttu-id="4f834-255">下面是一个示例 role 类：</span><span class="sxs-lookup"><span data-stu-id="4f834-255">The following is an example role class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a><span data-ttu-id="4f834-256">自定义角色存储</span><span class="sxs-lookup"><span data-stu-id="4f834-256">Customize the role store</span></span>

<span data-ttu-id="4f834-257">你可以创建一个 `RoleStore` 类，该类提供针对角色的所有数据操作的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-257">You can create a `RoleStore` class that provides the methods for all data operations on roles.</span></span> <span data-ttu-id="4f834-258">此类等效于[RoleStore &lt; TRole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)类。</span><span class="sxs-lookup"><span data-stu-id="4f834-258">This class is equivalent to the [RoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) class.</span></span> <span data-ttu-id="4f834-259">在 `RoleStore` 类中，你可以实现 `IRoleStore<TRole>` 和（可选） `IQueryableRoleStore<TRole>` 接口。</span><span class="sxs-lookup"><span data-stu-id="4f834-259">In the `RoleStore` class, you implement the `IRoleStore<TRole>` and optionally the `IQueryableRoleStore<TRole>` interface.</span></span>

* <span data-ttu-id="4f834-260">**IRoleStore &lt; TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="4f834-260">**IRoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="4f834-261">[IRoleStore &lt; TRole &gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1)接口定义要在角色存储区类中实现的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-261">The [IRoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) interface defines the methods to implement in the role store class.</span></span> <span data-ttu-id="4f834-262">它包含用于创建、更新、删除和检索角色的方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-262">It contains methods for creating, updating, deleting, and retrieving roles.</span></span>
* <span data-ttu-id="4f834-263">**RoleStore &lt; TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="4f834-263">**RoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="4f834-264">若要自定义 `RoleStore` ，请创建实现接口的类 `IRoleStore<TRole>` 。</span><span class="sxs-lookup"><span data-stu-id="4f834-264">To customize `RoleStore`, create a class that implements the `IRoleStore<TRole>` interface.</span></span> 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a><span data-ttu-id="4f834-265">重新配置应用程序以使用新的存储提供程序</span><span class="sxs-lookup"><span data-stu-id="4f834-265">Reconfigure app to use a new storage provider</span></span>

<span data-ttu-id="4f834-266">实现存储提供程序后，可将应用程序配置为使用该访问接口。</span><span class="sxs-lookup"><span data-stu-id="4f834-266">Once you have implemented a storage provider, you configure your app to use it.</span></span> <span data-ttu-id="4f834-267">如果你的应用程序使用了默认提供程序，请将其替换为你的自定义提供程序。</span><span class="sxs-lookup"><span data-stu-id="4f834-267">If your app used the default provider, replace it with your custom provider.</span></span>

1. <span data-ttu-id="4f834-268">删除 `Microsoft.AspNetCore.EntityFramework.Identity` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="4f834-268">Remove the `Microsoft.AspNetCore.EntityFramework.Identity` NuGet package.</span></span>
1. <span data-ttu-id="4f834-269">如果存储提供程序驻留在单独的项目或包中，请添加对它的引用。</span><span class="sxs-lookup"><span data-stu-id="4f834-269">If the storage provider resides in a separate project or package, add a reference to it.</span></span>
1. <span data-ttu-id="4f834-270">将所有对的引用替换为 `Microsoft.AspNetCore.EntityFramework.Identity` 存储提供程序的命名空间的 using 语句。</span><span class="sxs-lookup"><span data-stu-id="4f834-270">Replace all references to `Microsoft.AspNetCore.EntityFramework.Identity` with a using statement for the namespace of your storage provider.</span></span>
1. <span data-ttu-id="4f834-271">在 `ConfigureServices` 方法中，将 `AddIdentity` 方法更改为使用您的自定义类型。</span><span class="sxs-lookup"><span data-stu-id="4f834-271">In the `ConfigureServices` method, change the `AddIdentity` method to use your custom types.</span></span> <span data-ttu-id="4f834-272">出于此目的，你可以创建自己的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="4f834-272">You can create your own extension methods for this purpose.</span></span> <span data-ttu-id="4f834-273">有关示例，请参阅[IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) 。</span><span class="sxs-lookup"><span data-stu-id="4f834-273">See [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) for an example.</span></span>
1. <span data-ttu-id="4f834-274">如果使用的是角色，请更新 `RoleManager` 以使用 `RoleStore` 类。</span><span class="sxs-lookup"><span data-stu-id="4f834-274">If you are using Roles, update the `RoleManager` to use your `RoleStore` class.</span></span>
1. <span data-ttu-id="4f834-275">更新应用程序配置的连接字符串和凭据。</span><span class="sxs-lookup"><span data-stu-id="4f834-275">Update the connection string and credentials to your app's configuration.</span></span>

<span data-ttu-id="4f834-276">示例：</span><span class="sxs-lookup"><span data-stu-id="4f834-276">Example:</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Add identity types
    services.AddIdentity<ApplicationUser, ApplicationRole>()
        .AddDefaultTokenProviders();

    // Identity Services
    services.AddTransient<IUserStore<ApplicationUser>, CustomUserStore>();
    services.AddTransient<IRoleStore<ApplicationRole>, CustomRoleStore>();
    string connectionString = Configuration.GetConnectionString("DefaultConnection");
    services.AddTransient<SqlConnection>(e => new SqlConnection(connectionString));
    services.AddTransient<DapperUsersTable>();

    // additional configuration
}
```

## <a name="references"></a><span data-ttu-id="4f834-277">参考</span><span class="sxs-lookup"><span data-stu-id="4f834-277">References</span></span>

* <span data-ttu-id="4f834-278">[ASP.NET 4.x 的自定义存储提供程序Identity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)</span><span class="sxs-lookup"><span data-stu-id="4f834-278">[Custom Storage Providers for ASP.NET 4.x Identity](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)</span></span>
* <span data-ttu-id="4f834-279">[ASP.NET Core Identity ](https://github.com/dotnet/AspNetCore/tree/master/src/Identity)：此存储库包含指向社区维护的存储提供程序的链接。</span><span class="sxs-lookup"><span data-stu-id="4f834-279">[ASP.NET Core Identity](https://github.com/dotnet/AspNetCore/tree/master/src/Identity): This repository includes links to community maintained store providers.</span></span>
