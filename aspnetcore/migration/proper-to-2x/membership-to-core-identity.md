---
title: 从 ASP.NET 成员身份验证迁移到 ASP.NET Core 2。0Identity
author: isaac2004
description: 了解如何使用成员身份验证将现有的 ASP.NET 应用迁移到IdentityASP.NET Core 2.0。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: b5205ef69943f3744bba8381701008369dd0843c
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774504"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="daa98-103">从 ASP.NET 成员身份验证迁移到 ASP.NET Core 2.0 标识</span><span class="sxs-lookup"><span data-stu-id="daa98-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="daa98-104">作者：[Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="daa98-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="daa98-105">本文演示了如何使用成员身份验证将 ASP.NET 应用的数据库架构迁移到 ASP.NET Core 2.0 标识。</span><span class="sxs-lookup"><span data-stu-id="daa98-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="daa98-106">本文档提供将基于 ASP.NET 成员身份的应用的数据库架构迁移到用于 ASP.NET Core 标识的数据库架构所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="daa98-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="daa98-107">有关从基于 ASP.NET 成员身份的身份验证迁移到 ASP.NET Identity 的详细信息，请参阅[将现有应用从 SQL 成员身份迁移到 ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)。</span><span class="sxs-lookup"><span data-stu-id="daa98-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="daa98-108">有关 ASP.NET Core 标识的详细信息，请参阅[ASP.NET Core 上的标识简介](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="daa98-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="daa98-109">成员资格架构评审</span><span class="sxs-lookup"><span data-stu-id="daa98-109">Review of Membership schema</span></span>

<span data-ttu-id="daa98-110">在 ASP.NET 2.0 之前，开发人员负责为其应用创建整个身份验证和授权过程。</span><span class="sxs-lookup"><span data-stu-id="daa98-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="daa98-111">使用 ASP.NET 2.0，引入了成员身份，提供了一个样本解决方案来处理 ASP.NET 应用中的安全性。</span><span class="sxs-lookup"><span data-stu-id="daa98-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="daa98-112">开发人员现在可以使用[aspnet_regsql](https://msdn.microsoft.com/library/ms229862.aspx)的命令将架构启动到 SQL Server 数据库中。</span><span class="sxs-lookup"><span data-stu-id="daa98-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="daa98-113">运行此命令后，将在数据库中创建以下表。</span><span class="sxs-lookup"><span data-stu-id="daa98-113">After running this command, the following tables were created in the database.</span></span>

  ![成员资格表](identity/_static/membership-tables.png)

<span data-ttu-id="daa98-115">若要将现有应用迁移到 ASP.NET Core 2.0 标识，需要将这些表中的数据迁移到新的标识架构所使用的表中。</span><span class="sxs-lookup"><span data-stu-id="daa98-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="daa98-116">ASP.NET Core 标识2.0 架构</span><span class="sxs-lookup"><span data-stu-id="daa98-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="daa98-117">ASP.NET Core 2.0 遵循 ASP.NET 4.5 中引入的[标识](/aspnet/identity/index)原则。</span><span class="sxs-lookup"><span data-stu-id="daa98-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="daa98-118">尽管此原则是共享的，但在不同版本的 ASP.NET Core （请参阅将[身份验证和标识迁移到 ASP.NET Core 2.0](xref:migration/1x-to-2x/index)）之间的实现不同。</span><span class="sxs-lookup"><span data-stu-id="daa98-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="daa98-119">查看 ASP.NET Core 2.0 标识的架构的最快方法是创建新的 ASP.NET Core 2.0 应用。</span><span class="sxs-lookup"><span data-stu-id="daa98-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="daa98-120">在 Visual Studio 2017 中执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="daa98-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="daa98-121">选择 "**文件** > " "**新建** > **项目**"。</span><span class="sxs-lookup"><span data-stu-id="daa98-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="daa98-122">创建一个名为*CoreIdentitySample*的新**ASP.NET Core Web 应用程序**项目。</span><span class="sxs-lookup"><span data-stu-id="daa98-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="daa98-123">在下拉列表中选择**ASP.NET Core 2.0** ，然后选择 " **Web 应用程序**"。</span><span class="sxs-lookup"><span data-stu-id="daa98-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="daa98-124">此模板生成[Razor Pages](xref:razor-pages/index)应用。</span><span class="sxs-lookup"><span data-stu-id="daa98-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="daa98-125">单击 **"确定"** 之前，请单击 "**更改身份验证**"。</span><span class="sxs-lookup"><span data-stu-id="daa98-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="daa98-126">为标识模板选择**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="daa98-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="daa98-127">最后，单击 **"确定**"，然后单击 **"确定"**。</span><span class="sxs-lookup"><span data-stu-id="daa98-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="daa98-128">Visual Studio 使用 ASP.NET Core 标识模板来创建项目。</span><span class="sxs-lookup"><span data-stu-id="daa98-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="daa98-129">选择 "**工具** > " "**NuGet 包管理器** > " "**包管理器控制台**" 打开**包管理器控制台**（PMC）窗口。</span><span class="sxs-lookup"><span data-stu-id="daa98-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="daa98-130">导航到 PMC 中的项目根，并运行[实体框架（EF） Core](/ef/core) `Update-Database`命令。</span><span class="sxs-lookup"><span data-stu-id="daa98-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="daa98-131">ASP.NET Core 2.0 标识使用 EF Core 与存储身份验证数据的数据库进行交互。</span><span class="sxs-lookup"><span data-stu-id="daa98-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="daa98-132">为了使新创建的应用程序正常工作，需要有数据库来存储此数据。</span><span class="sxs-lookup"><span data-stu-id="daa98-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="daa98-133">创建新应用后，在数据库环境中检查架构的最快方法是使用[EF Core 迁移](/ef/core/managing-schemas/migrations/)来创建数据库。</span><span class="sxs-lookup"><span data-stu-id="daa98-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="daa98-134">此过程将创建一个在本地或其他位置模拟该架构的数据库。</span><span class="sxs-lookup"><span data-stu-id="daa98-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="daa98-135">有关详细信息，请查看前面的文档。</span><span class="sxs-lookup"><span data-stu-id="daa98-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="daa98-136">EF Core 命令使用*appsettings*中指定的数据库的连接字符串。</span><span class="sxs-lookup"><span data-stu-id="daa98-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="daa98-137">以下连接字符串针对名为 *.asp 的* *localhost*上的数据库。</span><span class="sxs-lookup"><span data-stu-id="daa98-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="daa98-138">在此设置中，EF Core 配置为使用`DefaultConnection`连接字符串。</span><span class="sxs-lookup"><span data-stu-id="daa98-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="daa98-139">选择 "**查看** > **SQL Server 对象资源管理器**"。</span><span class="sxs-lookup"><span data-stu-id="daa98-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="daa98-140">展开对应于`ConnectionStrings:DefaultConnection` *appsettings*的属性中指定的数据库名称的节点。</span><span class="sxs-lookup"><span data-stu-id="daa98-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="daa98-141">该`Update-Database`命令创建了用架构指定的数据库以及应用初始化所需的任何数据。</span><span class="sxs-lookup"><span data-stu-id="daa98-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="daa98-142">下图描绘了在前面的步骤中创建的表结构。</span><span class="sxs-lookup"><span data-stu-id="daa98-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![标识表](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="daa98-144">迁移架构</span><span class="sxs-lookup"><span data-stu-id="daa98-144">Migrate the schema</span></span>

<span data-ttu-id="daa98-145">成员身份和 ASP.NET Core 标识的表结构和字段之间存在细微的差异。</span><span class="sxs-lookup"><span data-stu-id="daa98-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="daa98-146">此模式已因 ASP.NET 和 ASP.NET Core 应用的身份验证/授权而发生了重大更改。</span><span class="sxs-lookup"><span data-stu-id="daa98-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="daa98-147">仍用于标识的关键对象是*用户*和*角色*。</span><span class="sxs-lookup"><span data-stu-id="daa98-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="daa98-148">下面是*用户*、*角色*和*UserRoles*的映射表。</span><span class="sxs-lookup"><span data-stu-id="daa98-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="daa98-149">用户</span><span class="sxs-lookup"><span data-stu-id="daa98-149">Users</span></span>

|<span data-ttu-id="daa98-150">*标识<br>（dbo。AspNetUsers*</span><span class="sxs-lookup"><span data-stu-id="daa98-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="daa98-151">*成员<br>身份（aspnet_Users/dbo. aspnet_Membership）*</span><span class="sxs-lookup"><span data-stu-id="daa98-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="daa98-152">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="daa98-152">**Field Name**</span></span>                 |<span data-ttu-id="daa98-153">**类型**</span><span class="sxs-lookup"><span data-stu-id="daa98-153">**Type**</span></span>|<span data-ttu-id="daa98-154">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="daa98-154">**Field Name**</span></span>                                    |<span data-ttu-id="daa98-155">**类型**</span><span class="sxs-lookup"><span data-stu-id="daa98-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="daa98-156">string</span><span class="sxs-lookup"><span data-stu-id="daa98-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="daa98-157">string</span><span class="sxs-lookup"><span data-stu-id="daa98-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="daa98-158">string</span><span class="sxs-lookup"><span data-stu-id="daa98-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="daa98-159">string</span><span class="sxs-lookup"><span data-stu-id="daa98-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="daa98-160">string</span><span class="sxs-lookup"><span data-stu-id="daa98-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="daa98-161">string</span><span class="sxs-lookup"><span data-stu-id="daa98-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="daa98-162">string</span><span class="sxs-lookup"><span data-stu-id="daa98-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="daa98-163">string</span><span class="sxs-lookup"><span data-stu-id="daa98-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="daa98-164">string</span><span class="sxs-lookup"><span data-stu-id="daa98-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="daa98-165">string</span><span class="sxs-lookup"><span data-stu-id="daa98-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="daa98-166">string</span><span class="sxs-lookup"><span data-stu-id="daa98-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="daa98-167">string</span><span class="sxs-lookup"><span data-stu-id="daa98-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="daa98-168">bit</span><span class="sxs-lookup"><span data-stu-id="daa98-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="daa98-169">bit</span><span class="sxs-lookup"><span data-stu-id="daa98-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="daa98-170">并非所有字段映射都类似于从成员身份到 ASP.NET Core 标识的一对一关系。</span><span class="sxs-lookup"><span data-stu-id="daa98-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="daa98-171">上表采用默认的成员资格用户架构，并将其映射到 ASP.NET Core 的标识架构。</span><span class="sxs-lookup"><span data-stu-id="daa98-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="daa98-172">需要手动映射用于成员身份的任何其他自定义字段。</span><span class="sxs-lookup"><span data-stu-id="daa98-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="daa98-173">在此映射中，无密码映射，因为密码条件和密码 salts 不会在两者之间迁移。</span><span class="sxs-lookup"><span data-stu-id="daa98-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="daa98-174">**建议将密码保留为 null 并要求用户重置其密码。**</span><span class="sxs-lookup"><span data-stu-id="daa98-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="daa98-175">在 ASP.NET Core 标识中`LockoutEnd` ，如果用户被锁定，则应设置为将来的某个日期。这会显示在迁移脚本中。</span><span class="sxs-lookup"><span data-stu-id="daa98-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="daa98-176">角色</span><span class="sxs-lookup"><span data-stu-id="daa98-176">Roles</span></span>

|<span data-ttu-id="daa98-177">*标识<br>（dbo。AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="daa98-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="daa98-178">*成员<br>身份（aspnet_Roles）*</span><span class="sxs-lookup"><span data-stu-id="daa98-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="daa98-179">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="daa98-179">**Field Name**</span></span>                 |<span data-ttu-id="daa98-180">**类型**</span><span class="sxs-lookup"><span data-stu-id="daa98-180">**Type**</span></span>|<span data-ttu-id="daa98-181">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="daa98-181">**Field Name**</span></span>   |<span data-ttu-id="daa98-182">**类型**</span><span class="sxs-lookup"><span data-stu-id="daa98-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="daa98-183">string</span><span class="sxs-lookup"><span data-stu-id="daa98-183">string</span></span>  |`RoleId`         | <span data-ttu-id="daa98-184">string</span><span class="sxs-lookup"><span data-stu-id="daa98-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="daa98-185">string</span><span class="sxs-lookup"><span data-stu-id="daa98-185">string</span></span>  |`RoleName`       | <span data-ttu-id="daa98-186">string</span><span class="sxs-lookup"><span data-stu-id="daa98-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="daa98-187">string</span><span class="sxs-lookup"><span data-stu-id="daa98-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="daa98-188">string</span><span class="sxs-lookup"><span data-stu-id="daa98-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="daa98-189">用户角色</span><span class="sxs-lookup"><span data-stu-id="daa98-189">User Roles</span></span>

|<span data-ttu-id="daa98-190">*标识<br>（dbo。AspNetUserRoles*</span><span class="sxs-lookup"><span data-stu-id="daa98-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="daa98-191">*成员<br>身份（aspnet_UsersInRoles）*</span><span class="sxs-lookup"><span data-stu-id="daa98-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="daa98-192">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="daa98-192">**Field Name**</span></span>           |<span data-ttu-id="daa98-193">**类型**</span><span class="sxs-lookup"><span data-stu-id="daa98-193">**Type**</span></span>  |<span data-ttu-id="daa98-194">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="daa98-194">**Field Name**</span></span>|<span data-ttu-id="daa98-195">**类型**</span><span class="sxs-lookup"><span data-stu-id="daa98-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="daa98-196">string</span><span class="sxs-lookup"><span data-stu-id="daa98-196">string</span></span>    |`RoleId`      |<span data-ttu-id="daa98-197">string</span><span class="sxs-lookup"><span data-stu-id="daa98-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="daa98-198">string</span><span class="sxs-lookup"><span data-stu-id="daa98-198">string</span></span>    |`UserId`      |<span data-ttu-id="daa98-199">string</span><span class="sxs-lookup"><span data-stu-id="daa98-199">string</span></span>                     |

<span data-ttu-id="daa98-200">创建*用户*和*角色*的迁移脚本时引用前面的映射表。</span><span class="sxs-lookup"><span data-stu-id="daa98-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="daa98-201">下面的示例假定数据库服务器上有两个数据库。</span><span class="sxs-lookup"><span data-stu-id="daa98-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="daa98-202">一个数据库包含现有的 ASP.NET 成员身份架构和数据。</span><span class="sxs-lookup"><span data-stu-id="daa98-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="daa98-203">使用前面所述的步骤创建了另一个*CoreIdentitySample*数据库。</span><span class="sxs-lookup"><span data-stu-id="daa98-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="daa98-204">有关详细信息，请以内联方式包含注释。</span><span class="sxs-lookup"><span data-stu-id="daa98-204">Comments are included inline for more details.</span></span>

```sql
-- THIS SCRIPT NEEDS TO RUN FROM THE CONTEXT OF THE MEMBERSHIP DB
BEGIN TRANSACTION MigrateUsersAndRoles
USE aspnetdb

-- INSERT USERS
INSERT INTO CoreIdentitySample.dbo.AspNetUsers
            (Id,
             UserName,
             NormalizedUserName,
             PasswordHash,
             SecurityStamp,
             EmailConfirmed,
             PhoneNumber,
             PhoneNumberConfirmed,
             TwoFactorEnabled,
             LockoutEnd,
             LockoutEnabled,
             AccessFailedCount,
             Email,
             NormalizedEmail)
SELECT aspnet_Users.UserId,
       aspnet_Users.UserName,
       -- The NormalizedUserName value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Users.UserName),
       -- Creates an empty password since passwords don't map between the 2 schemas
       '',
       /*
        The SecurityStamp token is used to verify the state of an account and
        is subject to change at any time. It should be initialized as a new ID.
       */
       NewID(),
       /*
        EmailConfirmed is set when a new user is created and confirmed via email.
        Users must have this set during migration to reset passwords.
       */
       1,
       aspnet_Users.MobileAlias,
       CASE
         WHEN aspnet_Users.MobileAlias IS NULL THEN 0
         ELSE 1
       END,
       -- 2FA likely wasn't setup in Membership for users, so setting as false.
       0,
       CASE
         -- Setting lockout date to time in the future (1,000 years)
         WHEN aspnet_Membership.IsLockedOut = 1 THEN Dateadd(year, 1000,
                                                     Sysutcdatetime())
         ELSE NULL
       END,
       aspnet_Membership.IsLockedOut,
       /*
        AccessFailedAccount is used to track failed logins. This is stored in
        Membership in multiple columns. Setting to 0 arbitrarily.
       */
       0,
       aspnet_Membership.Email,
       -- The NormalizedEmail value is upper case in ASP.NET Core Identity
       UPPER(aspnet_Membership.Email)
FROM   aspnet_Users
       LEFT OUTER JOIN aspnet_Membership
                    ON aspnet_Membership.ApplicationId =
                       aspnet_Users.ApplicationId
                       AND aspnet_Users.UserId = aspnet_Membership.UserId
       LEFT OUTER JOIN CoreIdentitySample.dbo.AspNetUsers
                    ON aspnet_Membership.UserId = AspNetUsers.Id
WHERE  AspNetUsers.Id IS NULL

-- INSERT ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetRoles(Id, Name)
SELECT RoleId, RoleName
FROM aspnet_Roles;

-- INSERT USER ROLES
INSERT INTO CoreIdentitySample.dbo.AspNetUserRoles(UserId, RoleId)
SELECT UserId, RoleId
FROM aspnet_UsersInRoles;

IF @@ERROR <> 0
  BEGIN
    ROLLBACK TRANSACTION MigrateUsersAndRoles
    RETURN
  END

COMMIT TRANSACTION MigrateUsersAndRoles
```

<span data-ttu-id="daa98-205">完成上述脚本后，将向成员资格Identity用户填充先前创建的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="daa98-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="daa98-206">用户需要在登录之前更改密码。</span><span class="sxs-lookup"><span data-stu-id="daa98-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="daa98-207">如果成员资格系统中的用户的用户名与其电子邮件地址不匹配，则需要对之前创建的应用进行更改，以满足此要求。</span><span class="sxs-lookup"><span data-stu-id="daa98-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="daa98-208">默认模板需要`UserName`和`Email`相同。</span><span class="sxs-lookup"><span data-stu-id="daa98-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="daa98-209">对于不同的情况，需要将登录过程修改为使用`UserName`而不是。 `Email`</span><span class="sxs-lookup"><span data-stu-id="daa98-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="daa98-210">在登录`PageModel`页的 " *Pages\Account\Login.cshtml.cs*" 中，从 " `[EmailAddress]` *电子邮件*" 属性中删除该属性。</span><span class="sxs-lookup"><span data-stu-id="daa98-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="daa98-211">将其重命名为*UserName*。</span><span class="sxs-lookup"><span data-stu-id="daa98-211">Rename it to *UserName*.</span></span> <span data-ttu-id="daa98-212">这需要在*视图*和`EmailAddress` *PageModel*中提到的任何位置进行更改。</span><span class="sxs-lookup"><span data-stu-id="daa98-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="daa98-213">结果应类似如下所示：</span><span class="sxs-lookup"><span data-stu-id="daa98-213">The result looks like the following:</span></span>

 ![固定登录](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="daa98-215">后续步骤</span><span class="sxs-lookup"><span data-stu-id="daa98-215">Next steps</span></span>

<span data-ttu-id="daa98-216">在本教程中，已了解如何将用户从 SQL 成员身份移植到IdentityASP.NET Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="daa98-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="daa98-217">有关 ASP.NET Core Identity的详细信息，请参阅[介绍Identity ](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="daa98-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
