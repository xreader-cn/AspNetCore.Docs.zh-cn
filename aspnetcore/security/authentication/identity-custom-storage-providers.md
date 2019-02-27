---
title: ASP.NET Core标识的自定义的存储提供程序
author: ardalis
description: 了解如何配置 ASP.NET Core标识的自定义存储提供程序。
ms.author: riande
ms.custom: mvc
ms.date: 10/24/2018
uid: security/authentication/identity-custom-storage-providers
ms.openlocfilehash: ccd56d0c15639e1ad29094e947f8055702ee2264
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833665"
---
# <a name="custom-storage-providers-for-aspnet-core-identity"></a><span data-ttu-id="2fe0d-103">ASP.NET Core标识的自定义的存储提供程序</span><span class="sxs-lookup"><span data-stu-id="2fe0d-103">Custom storage providers for ASP.NET Core Identity</span></span>

<span data-ttu-id="2fe0d-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2fe0d-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2fe0d-105">ASP.NET Core标识是一种可扩展系统，可用于创建自定义存储提供程序并将其连接到你的应用。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-105">ASP.NET Core Identity is an extensible system which enables you to create a custom storage provider and connect it to your app.</span></span> <span data-ttu-id="2fe0d-106">本主题介绍如何创建 ASP.NET Core标识的自定义的存储提供程序。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-106">This topic describes how to create a customized storage provider for ASP.NET Core Identity.</span></span> <span data-ttu-id="2fe0d-107">它介绍如何创建自己的存储提供程序的重要概念，但不是分步演练。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-107">It covers the important concepts for creating your own storage provider, but isn't a step-by-step walkthrough.</span></span>

<span data-ttu-id="2fe0d-108">[查看或下载 GitHub 中的示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample)。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-108">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample).</span></span>

## <a name="introduction"></a><span data-ttu-id="2fe0d-109">介绍</span><span class="sxs-lookup"><span data-stu-id="2fe0d-109">Introduction</span></span>

<span data-ttu-id="2fe0d-110">默认情况下，ASP.NET Core标识系统中使用实体框架核心的 SQL Server 数据库存储用户信息。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-110">By default, the ASP.NET Core Identity system stores user information in a SQL Server database using Entity Framework Core.</span></span> <span data-ttu-id="2fe0d-111">对于许多应用程序，此方法也适用。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-111">For many apps, this approach works well.</span></span> <span data-ttu-id="2fe0d-112">但是，您可能更愿意使用不同的持久性机制或数据架构。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-112">However, you may prefer to use a different persistence mechanism or data schema.</span></span> <span data-ttu-id="2fe0d-113">例如：</span><span class="sxs-lookup"><span data-stu-id="2fe0d-113">For example:</span></span>

* <span data-ttu-id="2fe0d-114">您使用[Azure 表存储](/azure/storage/)或其他数据存储。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-114">You use [Azure Table Storage](/azure/storage/) or another data store.</span></span>
* <span data-ttu-id="2fe0d-115">对数据库表具有不同的结构。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-115">Your database tables have a different structure.</span></span> 
* <span data-ttu-id="2fe0d-116">您可能希望使用不同的数据访问方法，如[Dapper](https://github.com/StackExchange/Dapper)。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-116">You may wish to use a different data access approach, such as [Dapper](https://github.com/StackExchange/Dapper).</span></span> 

<span data-ttu-id="2fe0d-117">在每个这种情况下，可以为你的存储机制编写自定义提供程序和该提供程序插入您的应用程序。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-117">In each of these cases, you can write a customized provider for your storage mechanism and plug that provider into your app.</span></span>

<span data-ttu-id="2fe0d-118">ASP.NET Core标识包含在 Visual Studio 中使用"单个用户帐户"选项的项目模板中。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-118">ASP.NET Core Identity is included in project templates in Visual Studio with the "Individual User Accounts" option.</span></span>

<span data-ttu-id="2fe0d-119">在使用.NET Core CLI 时，添加`-au Individual`:</span><span class="sxs-lookup"><span data-stu-id="2fe0d-119">When using the .NET Core CLI, add `-au Individual`:</span></span>

```console
dotnet new mvc -au Individual
dotnet new webapi -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a><span data-ttu-id="2fe0d-120">ASP.NET Core标识体系结构</span><span class="sxs-lookup"><span data-stu-id="2fe0d-120">The ASP.NET Core Identity architecture</span></span>

<span data-ttu-id="2fe0d-121">ASP.NET Core标识包含类称为管理器和存储区。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-121">ASP.NET Core Identity consists of classes called managers and stores.</span></span> <span data-ttu-id="2fe0d-122">*管理器*是高级类应用程序开发人员使用来执行操作，如创建标识用户。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-122">*Managers* are high-level classes which an app developer uses to perform operations, such as creating an Identity user.</span></span> <span data-ttu-id="2fe0d-123">*存储*是指定如何保持实体，例如用户和角色，较低级别类。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-123">*Stores* are lower-level classes that specify how entities, such as users and roles, are persisted.</span></span> <span data-ttu-id="2fe0d-124">存储遵循存储库模式和紧密结合的持久性机制。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-124">Stores follow the repository pattern and are closely coupled with the persistence mechanism.</span></span> <span data-ttu-id="2fe0d-125">管理器被分开存储，这意味着您可以替换持久性机制，而无需更改应用程序代码中的 （除配置）。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-125">Managers are decoupled from stores, which means you can replace the persistence mechanism without changing your application code (except for configuration).</span></span>

<span data-ttu-id="2fe0d-126">下图显示了 web 应用与交互的方式管理器，而存储与数据访问层进行交互。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-126">The following diagram shows how a web app interacts with the managers, while stores interact with the data access layer.</span></span>

![ASP.NET Core 应用适用于管理器 （例如，UserManager、 RoleManager）。](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

<span data-ttu-id="2fe0d-129">若要创建自定义存储提供程序，请使用此数据访问层 （绿色和灰色框上面的关系图中） 创建数据源、 数据访问层和交互的存储类。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-129">To create a custom storage provider, create the data source, the data access layer, and the store classes that interact with this data access layer (the green and grey boxes in the diagram above).</span></span> <span data-ttu-id="2fe0d-130">不需要自定义管理器或与它们 （蓝色框上面） 进行交互的应用程序代码。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-130">You don't need to customize the managers or your app code that interacts with them (the blue boxes above).</span></span>

<span data-ttu-id="2fe0d-131">创建的新实例时`UserManager`或`RoleManager`提供用户类的类型和作为参数传递存储类的实例。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-131">When creating a new instance of `UserManager` or `RoleManager` you provide the type of the user class and pass an instance of the store class as an argument.</span></span> <span data-ttu-id="2fe0d-132">此方法使您可以插入到 ASP.NET Core的自定义的类。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-132">This approach enables you to plug your customized classes into ASP.NET Core.</span></span> 

<span data-ttu-id="2fe0d-133">[重新配置应用程序以使用新的存储提供程序](#reconfigure-app-to-use-a-new-storage-provider)说明如何实例化`UserManager`和`RoleManager`使用自定义存储。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-133">[Reconfigure app to use new storage provider](#reconfigure-app-to-use-a-new-storage-provider) shows how to instantiate `UserManager` and `RoleManager` with a customized store.</span></span>

## <a name="aspnet-core-identity-stores-data-types"></a><span data-ttu-id="2fe0d-134">ASP.NET Core标识将存储的数据类型</span><span class="sxs-lookup"><span data-stu-id="2fe0d-134">ASP.NET Core Identity stores data types</span></span>

<span data-ttu-id="2fe0d-135">[ASP.NET Core标识](https://github.com/aspnet/identity)数据类型进行详细介绍下列各节：</span><span class="sxs-lookup"><span data-stu-id="2fe0d-135">[ASP.NET Core Identity](https://github.com/aspnet/identity) data types are detailed in the following sections:</span></span>

### <a name="users"></a><span data-ttu-id="2fe0d-136">用户</span><span class="sxs-lookup"><span data-stu-id="2fe0d-136">Users</span></span>

<span data-ttu-id="2fe0d-137">你的网站的已注册的用户。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-137">Registered users of your web site.</span></span> <span data-ttu-id="2fe0d-138">[IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)可能扩展类型，或将其用作自定义类型的示例。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-138">The [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser) type may be extended or used as an example for your own custom type.</span></span> <span data-ttu-id="2fe0d-139">您不必继承来实现自定义标识存储解决方案的特定类型。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-139">You don't need to inherit from a particular type to implement your own custom identity storage solution.</span></span>

### <a name="user-claims"></a><span data-ttu-id="2fe0d-140">用户声明</span><span class="sxs-lookup"><span data-stu-id="2fe0d-140">User Claims</span></span>

<span data-ttu-id="2fe0d-141">一组语句 (或[声明](/dotnet/api/system.security.claims.claim)) 有关的用户的表示用户的标识。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-141">A set of statements (or [Claims](/dotnet/api/system.security.claims.claim)) about the user that represent the user's identity.</span></span> <span data-ttu-id="2fe0d-142">可以启用的用户的标识不是可以通过角色实现更大的表达式。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-142">Can enable greater expression of the user's identity than can be achieved through roles.</span></span>

### <a name="user-logins"></a><span data-ttu-id="2fe0d-143">用户登录名</span><span class="sxs-lookup"><span data-stu-id="2fe0d-143">User Logins</span></span>

<span data-ttu-id="2fe0d-144">有关外部身份验证提供程序 （如 Facebook 或 Microsoft 帐户） 的信息日志记录使用户登录时使用。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-144">Information about the external authentication provider (like Facebook or a Microsoft account) to use when logging in a user.</span></span> [<span data-ttu-id="2fe0d-145">示例</span><span class="sxs-lookup"><span data-stu-id="2fe0d-145">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a><span data-ttu-id="2fe0d-146">角色</span><span class="sxs-lookup"><span data-stu-id="2fe0d-146">Roles</span></span>

<span data-ttu-id="2fe0d-147">你的站点的授权组。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-147">Authorization groups for your site.</span></span> <span data-ttu-id="2fe0d-148">包含角色 Id 和角色名称 （如"管理员"或"Employee"）。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-148">Includes the role Id and role name (like "Admin" or "Employee").</span></span> [<span data-ttu-id="2fe0d-149">示例</span><span class="sxs-lookup"><span data-stu-id="2fe0d-149">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a><span data-ttu-id="2fe0d-150">数据访问层</span><span class="sxs-lookup"><span data-stu-id="2fe0d-150">The data access layer</span></span>

<span data-ttu-id="2fe0d-151">本主题假定你熟悉要使用的持久性机制以及如何创建实体，可用于该机制。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-151">This topic assumes you are familiar with the persistence mechanism that you are going to use and how to create entities for that mechanism.</span></span> <span data-ttu-id="2fe0d-152">本主题不提供有关如何创建存储库或数据访问类; 详细信息使用 ASP.NET Core标识时，它提供了有关设计决策的一些建议。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-152">This topic doesn't provide details about how to create the repositories or data access classes; it provides some suggestions about design decisions when working with ASP.NET Core Identity.</span></span>

<span data-ttu-id="2fe0d-153">为自定义的存储提供程序设计的数据访问层时具有的自由度。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-153">You have a lot of freedom when designing the data access layer for a customized store provider.</span></span> <span data-ttu-id="2fe0d-154">只需创建要在应用中使用的功能的持久性机制。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-154">You only need to create persistence mechanisms for features that you intend to use in your app.</span></span> <span data-ttu-id="2fe0d-155">例如，如果不应用程序中使用角色，你不必创建的角色或用户角色关联的存储。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-155">For example, if you are not using roles in your app, you don't need to create storage for roles or user role associations.</span></span> <span data-ttu-id="2fe0d-156">你的技术和现有基础结构可能需要的 ASP.NET Core标识的默认实现不同的结构。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-156">Your technology and existing infrastructure may require a structure that's very different from the default implementation of ASP.NET Core Identity.</span></span> <span data-ttu-id="2fe0d-157">在数据访问层，提供用于存储实现的结构的逻辑。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-157">In your data access layer, you provide the logic to work with the structure of your storage implementation.</span></span>

<span data-ttu-id="2fe0d-158">数据访问层提供逻辑来将数据从 ASP.NET Core标识保存到数据源。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-158">The data access layer provides the logic to save the data from ASP.NET Core Identity to a data source.</span></span> <span data-ttu-id="2fe0d-159">自定义的存储提供程序的数据访问层可能包括以下类用于存储用户和角色信息。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-159">The data access layer for your customized storage provider might include the following classes to store user and role information.</span></span>

### <a name="context-class"></a><span data-ttu-id="2fe0d-160">Context 类</span><span class="sxs-lookup"><span data-stu-id="2fe0d-160">Context class</span></span>

<span data-ttu-id="2fe0d-161">若要连接到持久性机制并执行查询的信息进行封装。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-161">Encapsulates the information to connect to your persistence mechanism and execute queries.</span></span> <span data-ttu-id="2fe0d-162">多个数据类需要通常情况下通过依赖关系注入提供此类的实例。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-162">Several data classes require an instance of this class, typically provided through dependency injection.</span></span> <span data-ttu-id="2fe0d-163">[示例](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-163">[Example](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1).</span></span>

### <a name="user-storage"></a><span data-ttu-id="2fe0d-164">用户存储</span><span class="sxs-lookup"><span data-stu-id="2fe0d-164">User Storage</span></span>

<span data-ttu-id="2fe0d-165">存储和检索用户信息 （如用户名称和密码哈希）。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-165">Stores and retrieves user information (such as user name and password hash).</span></span> [<span data-ttu-id="2fe0d-166">示例</span><span class="sxs-lookup"><span data-stu-id="2fe0d-166">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a><span data-ttu-id="2fe0d-167">角色存储</span><span class="sxs-lookup"><span data-stu-id="2fe0d-167">Role Storage</span></span>

<span data-ttu-id="2fe0d-168">存储和检索角色信息 （例如角色名称）。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-168">Stores and retrieves role information (such as the role name).</span></span> [<span data-ttu-id="2fe0d-169">示例</span><span class="sxs-lookup"><span data-stu-id="2fe0d-169">Example</span></span>](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a><span data-ttu-id="2fe0d-170">UserClaims 存储</span><span class="sxs-lookup"><span data-stu-id="2fe0d-170">UserClaims Storage</span></span>

<span data-ttu-id="2fe0d-171">存储和检索用户声明信息 （如的声明类型和值）。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-171">Stores and retrieves user claim information (such as the claim type and value).</span></span> [<span data-ttu-id="2fe0d-172">示例</span><span class="sxs-lookup"><span data-stu-id="2fe0d-172">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a><span data-ttu-id="2fe0d-173">UserLogins 存储</span><span class="sxs-lookup"><span data-stu-id="2fe0d-173">UserLogins Storage</span></span>

<span data-ttu-id="2fe0d-174">存储和检索用户登录信息 （例如外部身份验证提供程序）。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-174">Stores and retrieves user login information (such as an external authentication provider).</span></span> [<span data-ttu-id="2fe0d-175">示例</span><span class="sxs-lookup"><span data-stu-id="2fe0d-175">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a><span data-ttu-id="2fe0d-176">UserRole 存储</span><span class="sxs-lookup"><span data-stu-id="2fe0d-176">UserRole Storage</span></span>

<span data-ttu-id="2fe0d-177">存储和检索哪些角色分配给哪些用户。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-177">Stores and retrieves which roles are assigned to which users.</span></span> [<span data-ttu-id="2fe0d-178">示例</span><span class="sxs-lookup"><span data-stu-id="2fe0d-178">Example</span></span>](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

<span data-ttu-id="2fe0d-179">**提示：** 仅实现想要使用应用程序中的类。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-179">**TIP:** Only implement the classes you intend to use in your app.</span></span>

<span data-ttu-id="2fe0d-180">在数据访问类中，提供代码要执行数据操作的持久性机制。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-180">In the data access classes, provide code to perform data operations for your persistence mechanism.</span></span> <span data-ttu-id="2fe0d-181">例如，在自定义提供程序，可能会具有以下代码以创建新的用户在*存储*类：</span><span class="sxs-lookup"><span data-stu-id="2fe0d-181">For example, within a custom provider, you might have the following code to create a new user in the *store* class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

<span data-ttu-id="2fe0d-182">创建用户的实现逻辑位于`_usersTable.CreateAsync`方法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-182">The implementation logic for creating the user is in the `_usersTable.CreateAsync` method, shown below.</span></span>

## <a name="customize-the-user-class"></a><span data-ttu-id="2fe0d-183">自定义用户类</span><span class="sxs-lookup"><span data-stu-id="2fe0d-183">Customize the user class</span></span>

<span data-ttu-id="2fe0d-184">在实现存储提供程序时，创建一个用户类等效于[IdentityUser 类](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-184">When implementing a storage provider, create a user class which is equivalent to the [IdentityUser class](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser).</span></span>

<span data-ttu-id="2fe0d-185">您的用户类必须包含最低限度`Id`和一个`UserName`属性。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-185">At a minimum, your user class must include an `Id` and a `UserName` property.</span></span>

<span data-ttu-id="2fe0d-186">`IdentityUser`类定义的属性的`UserManager`调用时执行请求的操作。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-186">The `IdentityUser` class defines the properties that the `UserManager` calls when performing requested operations.</span></span> <span data-ttu-id="2fe0d-187">默认类型`Id`属性是一个字符串，但可以继承自`IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>`和指定不同的类型。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-187">The default type of the `Id` property is a string, but you can inherit from `IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>` and specify a different type.</span></span> <span data-ttu-id="2fe0d-188">框架需要要处理的数据类型转换的存储实现。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-188">The framework expects the storage implementation to handle data type conversions.</span></span>

## <a name="customize-the-user-store"></a><span data-ttu-id="2fe0d-189">自定义用户存储</span><span class="sxs-lookup"><span data-stu-id="2fe0d-189">Customize the user store</span></span>

<span data-ttu-id="2fe0d-190">创建`UserStore`类，该类提供用户的所有数据操作的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-190">Create a `UserStore` class that provides the methods for all data operations on the user.</span></span> <span data-ttu-id="2fe0d-191">此类是等效于[UserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1)类。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-191">This class is equivalent to the [UserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1) class.</span></span> <span data-ttu-id="2fe0d-192">在你`UserStore`类，请实现`IUserStore<TUser>`和所需的可选接口。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-192">In your `UserStore` class, implement `IUserStore<TUser>` and the optional interfaces required.</span></span> <span data-ttu-id="2fe0d-193">您选择实现的可选接口基于应用程序中提供的功能。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-193">You select which optional interfaces to implement based on the functionality provided in your app.</span></span>

### <a name="optional-interfaces"></a><span data-ttu-id="2fe0d-194">可选接口</span><span class="sxs-lookup"><span data-stu-id="2fe0d-194">Optional interfaces</span></span>

* [<span data-ttu-id="2fe0d-195">IUserRoleStore</span><span class="sxs-lookup"><span data-stu-id="2fe0d-195">IUserRoleStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [<span data-ttu-id="2fe0d-196">IUserClaimStore</span><span class="sxs-lookup"><span data-stu-id="2fe0d-196">IUserClaimStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [<span data-ttu-id="2fe0d-197">IUserPasswordStore</span><span class="sxs-lookup"><span data-stu-id="2fe0d-197">IUserPasswordStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [<span data-ttu-id="2fe0d-198">IUserSecurityStampStore</span><span class="sxs-lookup"><span data-stu-id="2fe0d-198">IUserSecurityStampStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [<span data-ttu-id="2fe0d-199">IUserEmailStore</span><span class="sxs-lookup"><span data-stu-id="2fe0d-199">IUserEmailStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [<span data-ttu-id="2fe0d-200">IUserPhoneNumberStore</span><span class="sxs-lookup"><span data-stu-id="2fe0d-200">IUserPhoneNumberStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [<span data-ttu-id="2fe0d-201">IQueryableUserStore</span><span class="sxs-lookup"><span data-stu-id="2fe0d-201">IQueryableUserStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [<span data-ttu-id="2fe0d-202">IUserLoginStore</span><span class="sxs-lookup"><span data-stu-id="2fe0d-202">IUserLoginStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [<span data-ttu-id="2fe0d-203">IUserTwoFactorStore</span><span class="sxs-lookup"><span data-stu-id="2fe0d-203">IUserTwoFactorStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [<span data-ttu-id="2fe0d-204">IUserLockoutStore</span><span class="sxs-lookup"><span data-stu-id="2fe0d-204">IUserLockoutStore</span></span>](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

<span data-ttu-id="2fe0d-205">可选接口继承自`IUserStore<TUser>`。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-205">The optional interfaces inherit from `IUserStore<TUser>`.</span></span> <span data-ttu-id="2fe0d-206">可以看到存储中的部分实现的示例用户[示例应用](https://github.com/aspnet/Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-206">You can see a partially implemented sample user store in the [sample app](https://github.com/aspnet/Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs).</span></span>

<span data-ttu-id="2fe0d-207">在`UserStore`类，您使用您创建的用于执行操作的数据访问类。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-207">Within the `UserStore` class, you use the data access classes that you created to perform operations.</span></span> <span data-ttu-id="2fe0d-208">这些通过依赖关系注入进行传递。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-208">These are passed in using dependency injection.</span></span> <span data-ttu-id="2fe0d-209">例如，在使用 Dapper 实现，SQL Server`UserStore`类具有`CreateAsync`使用的实例方法`DapperUsersTable`来插入新记录：</span><span class="sxs-lookup"><span data-stu-id="2fe0d-209">For example, in the SQL Server with Dapper implementation, the `UserStore` class has the `CreateAsync` method which uses an instance of `DapperUsersTable` to insert a new record:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a><span data-ttu-id="2fe0d-210">要实现自定义用户存储区时接口</span><span class="sxs-lookup"><span data-stu-id="2fe0d-210">Interfaces to implement when customizing user store</span></span>

* <span data-ttu-id="2fe0d-211">**IUserStore**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-211">**IUserStore**</span></span>  
 <span data-ttu-id="2fe0d-212">[IUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1)接口是在用户存储中必须实现的唯一接口。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-212">The [IUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1) interface is the only interface you must implement in the user store.</span></span> <span data-ttu-id="2fe0d-213">它定义用于创建、 更新、 删除和检索用户的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-213">It defines methods for creating, updating, deleting, and retrieving users.</span></span>
* <span data-ttu-id="2fe0d-214">**IUserClaimStore**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-214">**IUserClaimStore**</span></span>  
 <span data-ttu-id="2fe0d-215">[IUserClaimStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)接口定义实现启用用户声明的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-215">The [IUserClaimStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1) interface defines the methods you implement to enable user claims.</span></span> <span data-ttu-id="2fe0d-216">它包含用于添加、 删除和检索用户声明的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-216">It contains methods for adding, removing and retrieving user claims.</span></span>
* <span data-ttu-id="2fe0d-217">**IUserLoginStore**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-217">**IUserLoginStore**</span></span>  
 <span data-ttu-id="2fe0d-218">[IUserLoginStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)定义实现以启用外部身份验证提供程序的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-218">The [IUserLoginStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1) defines the methods you implement to enable external authentication providers.</span></span> <span data-ttu-id="2fe0d-219">它包含用于添加、 删除和检索用户登录名和用于检索用户的登录信息基于方法的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-219">It contains methods for adding, removing and retrieving user logins, and a method for retrieving a user based on the login information.</span></span>
* <span data-ttu-id="2fe0d-220">**IUserRoleStore**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-220">**IUserRoleStore**</span></span>  
 <span data-ttu-id="2fe0d-221">[IUserRoleStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)接口定义实现将用户映射到角色的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-221">The [IUserRoleStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1) interface defines the methods you implement to map a user to a role.</span></span> <span data-ttu-id="2fe0d-222">它包含方法以添加、 删除和检索用户的角色和一个方法以检查是否将用户分配到角色。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-222">It contains methods to add, remove, and retrieve a user's roles, and a method to check if a user is assigned to a role.</span></span>
* <span data-ttu-id="2fe0d-223">**IUserPasswordStore**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-223">**IUserPasswordStore**</span></span>  
 <span data-ttu-id="2fe0d-224">[IUserPasswordStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)接口定义实现保留哈希的密码的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-224">The [IUserPasswordStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1) interface defines the methods you implement to persist hashed passwords.</span></span> <span data-ttu-id="2fe0d-225">它包含用于获取和设置哈希的密码和用于指示用户是否已设置密码的方法的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-225">It contains methods for getting and setting the hashed password, and a method that indicates whether the user has set a password.</span></span>
* <span data-ttu-id="2fe0d-226">**IUserSecurityStampStore**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-226">**IUserSecurityStampStore**</span></span>  
 <span data-ttu-id="2fe0d-227">[IUserSecurityStampStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)接口定义实现用于安全戳，该值指示是否已更改用户的帐户信息的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-227">The [IUserSecurityStampStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1) interface defines the methods you implement to use a security stamp for indicating whether the user's account information has changed.</span></span> <span data-ttu-id="2fe0d-228">用户更改密码，或添加或删除登录名时，将更新此 stamp。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-228">This stamp is updated when a user changes the password, or adds or removes logins.</span></span> <span data-ttu-id="2fe0d-229">它包含方法用于获取和设置的安全戳。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-229">It contains methods for getting and setting the security stamp.</span></span>
* <span data-ttu-id="2fe0d-230">**IUserTwoFactorStore**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-230">**IUserTwoFactorStore**</span></span>  
 <span data-ttu-id="2fe0d-231">[IUserTwoFactorStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)接口定义实现以支持双因素身份验证的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-231">The [IUserTwoFactorStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1) interface defines the methods you implement to support two factor authentication.</span></span> <span data-ttu-id="2fe0d-232">它包含用于获取和设置是否为用户启用双因素身份验证方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-232">It contains methods for getting and setting whether two factor authentication is enabled for a user.</span></span>
* <span data-ttu-id="2fe0d-233">**IUserPhoneNumberStore**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-233">**IUserPhoneNumberStore**</span></span>  
 <span data-ttu-id="2fe0d-234">[IUserPhoneNumberStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)接口定义实现来存储用户的电话号码的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-234">The [IUserPhoneNumberStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1) interface defines the methods you implement to store user phone numbers.</span></span> <span data-ttu-id="2fe0d-235">它包含用于获取和设置的电话号码和电话号码是否已确认的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-235">It contains methods for getting and setting the phone number and whether the phone number is confirmed.</span></span>
* <span data-ttu-id="2fe0d-236">**IUserEmailStore**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-236">**IUserEmailStore**</span></span>  
 <span data-ttu-id="2fe0d-237">[IUserEmailStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)接口定义实现来存储用户的电子邮件地址的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-237">The [IUserEmailStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1) interface defines the methods you implement to store user email addresses.</span></span> <span data-ttu-id="2fe0d-238">它包含用于获取和设置的电子邮件地址和电子邮件是否已确认的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-238">It contains methods for getting and setting the email address and whether the email is confirmed.</span></span>
* <span data-ttu-id="2fe0d-239">**IUserLockoutStore**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-239">**IUserLockoutStore**</span></span>  
 <span data-ttu-id="2fe0d-240">[IUserLockoutStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)接口定义实现以存储有关锁定的帐户信息的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-240">The [IUserLockoutStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1) interface defines the methods you implement to store information about locking an account.</span></span> <span data-ttu-id="2fe0d-241">它包含用于跟踪失败的访问尝试和锁定的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-241">It contains methods for tracking failed access attempts and lockouts.</span></span>
* <span data-ttu-id="2fe0d-242">**IQueryableUserStore**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-242">**IQueryableUserStore**</span></span>  
 <span data-ttu-id="2fe0d-243">[IQueryableUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)接口定义实现以提供可查询的用户存储区的成员。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-243">The [IQueryableUserStore&lt;TUser&gt;](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1) interface defines the members you implement to provide a queryable user store.</span></span>

<span data-ttu-id="2fe0d-244">在应用中实现所需的接口。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-244">You implement only the interfaces that are needed in your app.</span></span> <span data-ttu-id="2fe0d-245">例如：</span><span class="sxs-lookup"><span data-stu-id="2fe0d-245">For example:</span></span>

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

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a><span data-ttu-id="2fe0d-246">IdentityUserClaim、 IdentityUserLogin 和 IdentityUserRole</span><span class="sxs-lookup"><span data-stu-id="2fe0d-246">IdentityUserClaim, IdentityUserLogin, and IdentityUserRole</span></span>

<span data-ttu-id="2fe0d-247">`Microsoft.AspNet.Identity.EntityFramework`命名空间包含的实现[IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1)， [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)，并且[IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1)类。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-247">The `Microsoft.AspNet.Identity.EntityFramework` namespace contains implementations of the [IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1), [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin), and [IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1) classes.</span></span> <span data-ttu-id="2fe0d-248">如果使用这些功能，您可能想要创建这些类的版本并定义您的应用程序的属性。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-248">If you are using these features, you may want to create your own versions of these classes and define the properties for your app.</span></span> <span data-ttu-id="2fe0d-249">但是，有时它是更高效，若要执行基本操作 （如添加或删除用户的声明） 时不将这些实体加载到内存。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-249">However, sometimes it's more efficient to not load these entities into memory when performing basic operations (such as adding or removing a user's claim).</span></span> <span data-ttu-id="2fe0d-250">相反后, 端存储类可以执行这些操作直接在数据源上。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-250">Instead, the backend store classes can execute these operations directly on the data source.</span></span> <span data-ttu-id="2fe0d-251">例如，`UserStore.GetClaimsAsync`方法可以调用`userClaimTable.FindByUserId(user.Id)`方法来执行查询，在直接表并返回声明的列表。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-251">For example, the `UserStore.GetClaimsAsync` method can call the `userClaimTable.FindByUserId(user.Id)` method to execute a query on that table directly and return a list of claims.</span></span>

## <a name="customize-the-role-class"></a><span data-ttu-id="2fe0d-252">自定义角色类</span><span class="sxs-lookup"><span data-stu-id="2fe0d-252">Customize the role class</span></span>

<span data-ttu-id="2fe0d-253">在实现角色存储提供程序时，您可以创建自定义角色类型。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-253">When implementing a role storage provider, you can create a custom role type.</span></span> <span data-ttu-id="2fe0d-254">无需实现特定接口，但是它必须具有`Id`，其中包含通常`Name`属性。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-254">It need not implement a particular interface, but it must have an `Id` and typically it will have a `Name` property.</span></span>

<span data-ttu-id="2fe0d-255">下面是示例角色类：</span><span class="sxs-lookup"><span data-stu-id="2fe0d-255">The following is an example role class:</span></span>

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a><span data-ttu-id="2fe0d-256">自定义角色存储</span><span class="sxs-lookup"><span data-stu-id="2fe0d-256">Customize the role store</span></span>

<span data-ttu-id="2fe0d-257">您可以创建`RoleStore`类，该类提供对角色的所有数据操作的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-257">You can create a `RoleStore` class that provides the methods for all data operations on roles.</span></span> <span data-ttu-id="2fe0d-258">此类是等效于[RoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)类。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-258">This class is equivalent to the [RoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1) class.</span></span> <span data-ttu-id="2fe0d-259">在中`RoleStore`类，实现`IRoleStore<TRole>`和 （可选）`IQueryableRoleStore<TRole>`接口。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-259">In the `RoleStore` class, you implement the `IRoleStore<TRole>` and optionally the `IQueryableRoleStore<TRole>` interface.</span></span>

* <span data-ttu-id="2fe0d-260">**IRoleStore&lt;TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-260">**IRoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="2fe0d-261">[IRoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1)接口定义了要在角色存储类中实现的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-261">The [IRoleStore&lt;TRole&gt;](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1) interface defines the methods to implement in the role store class.</span></span> <span data-ttu-id="2fe0d-262">它包含用于创建、 更新、 删除和检索角色的方法。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-262">It contains methods for creating, updating, deleting, and retrieving roles.</span></span>
* <span data-ttu-id="2fe0d-263">**RoleStore&lt;TRole&gt;**</span><span class="sxs-lookup"><span data-stu-id="2fe0d-263">**RoleStore&lt;TRole&gt;**</span></span>  
 <span data-ttu-id="2fe0d-264">若要自定义`RoleStore`，创建一个实现类`IRoleStore<TRole>`接口。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-264">To customize `RoleStore`, create a class that implements the `IRoleStore<TRole>` interface.</span></span> 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a><span data-ttu-id="2fe0d-265">重新配置应用程序以使用新的存储提供程序</span><span class="sxs-lookup"><span data-stu-id="2fe0d-265">Reconfigure app to use a new storage provider</span></span>

<span data-ttu-id="2fe0d-266">已实现存储提供程序之后, 你将应用配置为使用它。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-266">Once you have implemented a storage provider, you configure your app to use it.</span></span> <span data-ttu-id="2fe0d-267">如果您的应用程序使用默认的提供程序，请将它都替换为自定义提供程序。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-267">If your app used the default provider, replace it with your custom provider.</span></span>

1. <span data-ttu-id="2fe0d-268">删除`Microsoft.AspNetCore.EntityFramework.Identity`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-268">Remove the `Microsoft.AspNetCore.EntityFramework.Identity` NuGet package.</span></span>
1. <span data-ttu-id="2fe0d-269">如果存储提供程序驻留在单独的项目或包中，添加对它的引用。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-269">If the storage provider resides in a separate project or package, add a reference to it.</span></span>
1. <span data-ttu-id="2fe0d-270">替换为对所有引用`Microsoft.AspNetCore.EntityFramework.Identity`使用 using 语句的命名空间的存储提供程序。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-270">Replace all references to `Microsoft.AspNetCore.EntityFramework.Identity` with a using statement for the namespace of your storage provider.</span></span>
1. <span data-ttu-id="2fe0d-271">在中`ConfigureServices`方法中，更改`AddIdentity`方法以使用自定义类型。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-271">In the `ConfigureServices` method, change the `AddIdentity` method to use your custom types.</span></span> <span data-ttu-id="2fe0d-272">您可以创建你自己的扩展方法实现此目的。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-272">You can create your own extension methods for this purpose.</span></span> <span data-ttu-id="2fe0d-273">请参阅[IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs)有关的示例。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-273">See [IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs) for an example.</span></span>
1. <span data-ttu-id="2fe0d-274">如果使用的角色，更新`RoleManager`若要使用你`RoleStore`类。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-274">If you are using Roles, update the `RoleManager` to use your `RoleStore` class.</span></span>
1. <span data-ttu-id="2fe0d-275">对应用程序的配置更新连接字符串和凭据。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-275">Update the connection string and credentials to your app's configuration.</span></span>

<span data-ttu-id="2fe0d-276">示例:</span><span class="sxs-lookup"><span data-stu-id="2fe0d-276">Example:</span></span>

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

## <a name="references"></a><span data-ttu-id="2fe0d-277">参考资料</span><span class="sxs-lookup"><span data-stu-id="2fe0d-277">References</span></span>

* [<span data-ttu-id="2fe0d-278">ASP.NET 4.x 标识的自定义存储提供程序</span><span class="sxs-lookup"><span data-stu-id="2fe0d-278">Custom Storage Providers for ASP.NET 4.x Identity</span></span>](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* <span data-ttu-id="2fe0d-279">[ASP.NET Core 标识](https://github.com/aspnet/identity)&ndash;此存储库包括社区维护存储提供程序的链接。</span><span class="sxs-lookup"><span data-stu-id="2fe0d-279">[ASP.NET Core Identity](https://github.com/aspnet/identity) &ndash; This repository includes links to community maintained store providers.</span></span>
