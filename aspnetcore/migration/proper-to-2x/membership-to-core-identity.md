---
title: ASP.NET 成员资格身份验证从迁移到 ASP.NET Core 2.0 标识
author: isaac2004
description: 了解如何迁移现有的 ASP.NET 应用程序使用成员资格到 ASP.NET Core 2.0 标识的身份验证。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58264724"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a><span data-ttu-id="ce4b5-103">ASP.NET 成员资格身份验证从迁移到 ASP.NET Core 2.0 标识</span><span class="sxs-lookup"><span data-stu-id="ce4b5-103">Migrate from ASP.NET Membership authentication to ASP.NET Core 2.0 Identity</span></span>

<span data-ttu-id="ce4b5-104">作者：[Isaac Levin](https://isaaclevin.com)</span><span class="sxs-lookup"><span data-stu-id="ce4b5-104">By [Isaac Levin](https://isaaclevin.com)</span></span>

<span data-ttu-id="ce4b5-105">本文演示如何迁移使用成员资格到 ASP.NET Core 2.0 标识的身份验证的 ASP.NET 应用程序的数据库架构。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-105">This article demonstrates migrating the database schema for ASP.NET apps using Membership authentication to ASP.NET Core 2.0 Identity.</span></span>

> [!NOTE]
> <span data-ttu-id="ce4b5-106">本文档提供将基于 ASP.NET 成员资格的应用程序的数据库架构迁移到用于 ASP.NET Core 标识的数据库架构所需的步骤。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-106">This document provides the steps needed to migrate the database schema for ASP.NET Membership-based apps to the database schema used for ASP.NET Core Identity.</span></span> <span data-ttu-id="ce4b5-107">有关从基于 ASP.NET 成员资格的身份验证迁移到 ASP.NET 标识的详细信息，请参阅[将现有应用程序从 SQL 成员身份迁移到 ASP.NET 标识](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-107">For more information about migrating from ASP.NET Membership-based authentication to ASP.NET Identity, see [Migrate an existing app from SQL Membership to ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity).</span></span> <span data-ttu-id="ce4b5-108">有关 ASP.NET Core 标识的详细信息，请参阅[ASP.NET Core 上的标识简介](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-108">For more information about ASP.NET Core Identity, see [Introduction to Identity on ASP.NET Core](xref:security/authentication/identity).</span></span>

## <a name="review-of-membership-schema"></a><span data-ttu-id="ce4b5-109">查看的成员身份架构</span><span class="sxs-lookup"><span data-stu-id="ce4b5-109">Review of Membership schema</span></span>

<span data-ttu-id="ce4b5-110">在 ASP.NET 2.0 之前开发人员被负责创建自己的应用的整个身份验证和授权过程。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-110">Prior to ASP.NET 2.0, developers were tasked with creating the entire authentication and authorization process for their apps.</span></span> <span data-ttu-id="ce4b5-111">使用 ASP.NET 2.0 成员身份引入时，提供处理 ASP.NET 应用程序中的安全性的样本解决方案。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-111">With ASP.NET 2.0, Membership was introduced, providing a boilerplate solution to handling security within ASP.NET apps.</span></span> <span data-ttu-id="ce4b5-112">开发人员现在可以启动到与 SQL Server 数据库架构[aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx)命令。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-112">Developers were now able to bootstrap a schema into a SQL Server database with the [aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx) command.</span></span> <span data-ttu-id="ce4b5-113">运行此命令后，已在数据库中创建以下表。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-113">After running this command, the following tables were created in the database.</span></span>

  ![成员资格表](identity/_static/membership-tables.png)

<span data-ttu-id="ce4b5-115">若要将现有应用迁移到 ASP.NET Core 2.0 标识，这些表中的数据需要迁移到使用新的标识架构的表。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-115">To migrate existing apps to ASP.NET Core 2.0 Identity, the data in these tables needs to be migrated to the tables used by the new Identity schema.</span></span>

## <a name="aspnet-core-identity-20-schema"></a><span data-ttu-id="ce4b5-116">ASP.NET Core 标识 2.0 架构</span><span class="sxs-lookup"><span data-stu-id="ce4b5-116">ASP.NET Core Identity 2.0 schema</span></span>

<span data-ttu-id="ce4b5-117">ASP.NET Core 2.0 遵循[标识](/aspnet/identity/index)ASP.NET 4.5 中引入的原则。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-117">ASP.NET Core 2.0 follows the [Identity](/aspnet/identity/index) principle introduced in ASP.NET 4.5.</span></span> <span data-ttu-id="ce4b5-118">尽管共享的原则，框架之间的实现是不同的甚至 ASP.NET Core 的版本之间 (请参阅[将身份验证和标识迁移到 ASP.NET Core 2.0](xref:migration/1x-to-2x/index))。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-118">Though the principle is shared, the implementation between the frameworks is different, even between versions of ASP.NET Core (see [Migrate authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/index)).</span></span>

<span data-ttu-id="ce4b5-119">ASP.NET Core 2.0 标识查看架构的最快方法是创建新的 ASP.NET Core 2.0 应用程序。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-119">The fastest way to view the schema for ASP.NET Core 2.0 Identity is to create a new ASP.NET Core 2.0 app.</span></span> <span data-ttu-id="ce4b5-120">请按照 Visual Studio 2017 中的执行以下步骤操作：</span><span class="sxs-lookup"><span data-stu-id="ce4b5-120">Follow these steps in Visual Studio 2017:</span></span>

1. <span data-ttu-id="ce4b5-121">选择“文件” > “新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-121">Select **File** > **New** > **Project**.</span></span>
1. <span data-ttu-id="ce4b5-122">创建一个新**ASP.NET Core Web 应用程序**名为项目*CoreIdentitySample*。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-122">Create a new **ASP.NET Core Web Application** project named *CoreIdentitySample*.</span></span>
1. <span data-ttu-id="ce4b5-123">选择**ASP.NET Core 2.0**在下拉列表中，然后选择**Web 应用程序**。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-123">Select **ASP.NET Core 2.0** in the dropdown and then select **Web Application**.</span></span> <span data-ttu-id="ce4b5-124">此模板会生成[Razor 页面](xref:razor-pages/index)应用。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-124">This template produces a [Razor Pages](xref:razor-pages/index) app.</span></span> <span data-ttu-id="ce4b5-125">然后再单击**确定**，单击**更改身份验证**。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-125">Before clicking **OK**, click **Change Authentication**.</span></span>
1. <span data-ttu-id="ce4b5-126">选择**单个用户帐户**标识模板。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-126">Choose **Individual User Accounts** for the Identity templates.</span></span> <span data-ttu-id="ce4b5-127">最后，单击**确定**，然后**确定**。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-127">Finally, click **OK**, then **OK**.</span></span> <span data-ttu-id="ce4b5-128">Visual Studio 创建项目时使用 ASP.NET Core 标识模板。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-128">Visual Studio creates a project using the ASP.NET Core Identity template.</span></span>
1. <span data-ttu-id="ce4b5-129">选择**工具** > **NuGet 包管理器** > **程序包管理器控制台**打开**包管理器控制台**(PMC) 窗口。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-129">Select **Tools** > **NuGet Package Manager** > **Package Manager Console** to open the **Package Manager Console** (PMC) window.</span></span>
1. <span data-ttu-id="ce4b5-130">导航到项目根目录中 PMC 中，然后运行[Entity Framework (EF) Core](/ef/core) `Update-Database`命令。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-130">Navigate to the project root in PMC, and run the [Entity Framework (EF) Core](/ef/core) `Update-Database` command.</span></span>

    <span data-ttu-id="ce4b5-131">ASP.NET Core 2.0 标识使用 EF Core 与存储身份验证数据的数据库进行交互。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-131">ASP.NET Core 2.0 Identity uses EF Core to interact with the database storing the authentication data.</span></span> <span data-ttu-id="ce4b5-132">为了使新创建的应用程序能够，那里需要使用数据库来存储此数据。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-132">In order for the newly created app to work, there needs to be a database to store this data.</span></span> <span data-ttu-id="ce4b5-133">创建新的应用程序之后, 检查的数据库环境中的架构的最快方法是创建数据库使用[EF Core 迁移](/ef/core/managing-schemas/migrations/)。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-133">After creating a new app, the fastest way to inspect the schema in a database environment is to create the database using [EF Core Migrations](/ef/core/managing-schemas/migrations/).</span></span> <span data-ttu-id="ce4b5-134">此过程创建数据库、 在本地或其他位置，模拟该架构。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-134">This process creates a database, either locally or elsewhere, which mimics that schema.</span></span> <span data-ttu-id="ce4b5-135">查看上述文档，有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-135">Review the preceding documentation for more information.</span></span>

    <span data-ttu-id="ce4b5-136">EF Core 命令中指定的数据库使用连接字符串*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-136">EF Core commands use the connection string for the database specified in *appsettings.json*.</span></span> <span data-ttu-id="ce4b5-137">下面的连接字符串上的目标数据库*localhost*名为*asp net core 标识*。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-137">The following connection string targets a database on *localhost* named *asp-net-core-identity*.</span></span> <span data-ttu-id="ce4b5-138">在此设置中，EF Core 配置为使用`DefaultConnection`连接字符串。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-138">In this setting, EF Core is configured to use the `DefaultConnection` connection string.</span></span>

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. <span data-ttu-id="ce4b5-139">选择**视图** > **SQL Server 对象资源管理器**。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-139">Select **View** > **SQL Server Object Explorer**.</span></span> <span data-ttu-id="ce4b5-140">展开中指定的数据库名称与对应的节点`ConnectionStrings:DefaultConnection`的属性*appsettings.json*。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-140">Expand the node corresponding to the database name specified in the `ConnectionStrings:DefaultConnection` property of *appsettings.json*.</span></span>

    <span data-ttu-id="ce4b5-141">`Update-Database`命令创建与架构指定的数据库和任何所需的应用程序初始化的数据。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-141">The `Update-Database` command created the database specified with the schema and any data needed for app initialization.</span></span> <span data-ttu-id="ce4b5-142">下图描绘了使用上述步骤创建的表结构。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-142">The following image depicts the table structure that's created with the preceding steps.</span></span>

    ![标识表](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a><span data-ttu-id="ce4b5-144">迁移架构</span><span class="sxs-lookup"><span data-stu-id="ce4b5-144">Migrate the schema</span></span>

<span data-ttu-id="ce4b5-145">有细微的差别的表结构和成员身份和 ASP.NET Core 标识字段。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-145">There are subtle differences in the table structures and fields for both Membership and ASP.NET Core Identity.</span></span> <span data-ttu-id="ce4b5-146">模式已显著更改身份验证/授权与 ASP.NET 和 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-146">The pattern has changed substantially for authentication/authorization with ASP.NET and ASP.NET Core apps.</span></span> <span data-ttu-id="ce4b5-147">仍用于标识的关键对象是*用户*并*角色*。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-147">The key objects that are still used with Identity are *Users* and *Roles*.</span></span> <span data-ttu-id="ce4b5-148">以下是有关映射表*用户*，*角色*，并*UserRoles*。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-148">Here are mapping tables for *Users*, *Roles*, and *UserRoles*.</span></span>

### <a name="users"></a><span data-ttu-id="ce4b5-149">用户</span><span class="sxs-lookup"><span data-stu-id="ce4b5-149">Users</span></span>

|<span data-ttu-id="ce4b5-150">*Identity<br>(dbo.AspNetUsers)*</span><span class="sxs-lookup"><span data-stu-id="ce4b5-150">*Identity<br>(dbo.AspNetUsers)*</span></span>        ||<span data-ttu-id="ce4b5-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span><span class="sxs-lookup"><span data-stu-id="ce4b5-151">*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*</span></span>||
|----------------------------------------|-----------------------------------------------------------|
|<span data-ttu-id="ce4b5-152">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-152">**Field Name**</span></span>                 |<span data-ttu-id="ce4b5-153">**Type**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-153">**Type**</span></span>|<span data-ttu-id="ce4b5-154">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-154">**Field Name**</span></span>                                    |<span data-ttu-id="ce4b5-155">**Type**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-155">**Type**</span></span>|
|`Id`                           |<span data-ttu-id="ce4b5-156">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-156">string</span></span>  |`aspnet_Users.UserId`                             |<span data-ttu-id="ce4b5-157">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-157">string</span></span>  |
|`UserName`                     |<span data-ttu-id="ce4b5-158">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-158">string</span></span>  |`aspnet_Users.UserName`                           |<span data-ttu-id="ce4b5-159">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-159">string</span></span>  |
|`Email`                        |<span data-ttu-id="ce4b5-160">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-160">string</span></span>  |`aspnet_Membership.Email`                         |<span data-ttu-id="ce4b5-161">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-161">string</span></span>  |
|`NormalizedUserName`           |<span data-ttu-id="ce4b5-162">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-162">string</span></span>  |`aspnet_Users.LoweredUserName`                    |<span data-ttu-id="ce4b5-163">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-163">string</span></span>  |
|`NormalizedEmail`              |<span data-ttu-id="ce4b5-164">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-164">string</span></span>  |`aspnet_Membership.LoweredEmail`                  |<span data-ttu-id="ce4b5-165">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-165">string</span></span>  |
|`PhoneNumber`                  |<span data-ttu-id="ce4b5-166">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-166">string</span></span>  |`aspnet_Users.MobileAlias`                        |<span data-ttu-id="ce4b5-167">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-167">string</span></span>  |
|`LockoutEnabled`               |<span data-ttu-id="ce4b5-168">位</span><span class="sxs-lookup"><span data-stu-id="ce4b5-168">bit</span></span>     |`aspnet_Membership.IsLockedOut`                   |<span data-ttu-id="ce4b5-169">位</span><span class="sxs-lookup"><span data-stu-id="ce4b5-169">bit</span></span>     |

> [!NOTE]
> <span data-ttu-id="ce4b5-170">并非所有字段映射类似都于从成员资格到 ASP.NET Core 标识的一对一关系。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-170">Not all the field mappings resemble one-to-one relationships from Membership to ASP.NET Core Identity.</span></span> <span data-ttu-id="ce4b5-171">前面的表使用默认成员资格用户架构，并将其映射到 ASP.NET Core 标识架构。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-171">The preceding table takes the default Membership User schema and maps it to the ASP.NET Core Identity schema.</span></span> <span data-ttu-id="ce4b5-172">用于成员资格的任何其他自定义字段需要手动映射。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-172">Any other custom fields that were used for Membership need to be mapped manually.</span></span> <span data-ttu-id="ce4b5-173">在此映射中，则没有密码的映射因为两者之间不迁移密码条件和密码 salt。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-173">In this mapping, there's no map for passwords, as both password criteria and password salts don't migrate between the two.</span></span> <span data-ttu-id="ce4b5-174">**建议可保留为 null 的密码，并要求用户重置其密码。**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-174">**It's recommended to leave the password as null and to ask users to reset their passwords.**</span></span> <span data-ttu-id="ce4b5-175">在 ASP.NET Core 标识`LockoutEnd`应设置为在将来的某个日期中，如果用户被锁定。迁移脚本所示。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-175">In ASP.NET Core Identity, `LockoutEnd` should be set to some date in the future if the user is locked out. This is shown in the migration script.</span></span>

### <a name="roles"></a><span data-ttu-id="ce4b5-176">角色</span><span class="sxs-lookup"><span data-stu-id="ce4b5-176">Roles</span></span>

|<span data-ttu-id="ce4b5-177">*Identity<br>(dbo.AspNetRoles)*</span><span class="sxs-lookup"><span data-stu-id="ce4b5-177">*Identity<br>(dbo.AspNetRoles)*</span></span>        ||<span data-ttu-id="ce4b5-178">*Membership<br>(dbo.aspnet_Roles)*</span><span class="sxs-lookup"><span data-stu-id="ce4b5-178">*Membership<br>(dbo.aspnet_Roles)*</span></span>||
|----------------------------------------|-----------------------------------|
|<span data-ttu-id="ce4b5-179">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-179">**Field Name**</span></span>                 |<span data-ttu-id="ce4b5-180">**Type**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-180">**Type**</span></span>|<span data-ttu-id="ce4b5-181">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-181">**Field Name**</span></span>   |<span data-ttu-id="ce4b5-182">**Type**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-182">**Type**</span></span>         |
|`Id`                           |<span data-ttu-id="ce4b5-183">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-183">string</span></span>  |`RoleId`         | <span data-ttu-id="ce4b5-184">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-184">string</span></span>          |
|`Name`                         |<span data-ttu-id="ce4b5-185">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-185">string</span></span>  |`RoleName`       | <span data-ttu-id="ce4b5-186">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-186">string</span></span>          |
|`NormalizedName`               |<span data-ttu-id="ce4b5-187">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-187">string</span></span>  |`LoweredRoleName`| <span data-ttu-id="ce4b5-188">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-188">string</span></span>          |

### <a name="user-roles"></a><span data-ttu-id="ce4b5-189">用户角色</span><span class="sxs-lookup"><span data-stu-id="ce4b5-189">User Roles</span></span>

|<span data-ttu-id="ce4b5-190">*Identity<br>(dbo.AspNetUserRoles)*</span><span class="sxs-lookup"><span data-stu-id="ce4b5-190">*Identity<br>(dbo.AspNetUserRoles)*</span></span>||<span data-ttu-id="ce4b5-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span><span class="sxs-lookup"><span data-stu-id="ce4b5-191">*Membership<br>(dbo.aspnet_UsersInRoles)*</span></span>||
|------------------------------------|------------------------------------------|
|<span data-ttu-id="ce4b5-192">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-192">**Field Name**</span></span>           |<span data-ttu-id="ce4b5-193">**Type**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-193">**Type**</span></span>  |<span data-ttu-id="ce4b5-194">**字段名称**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-194">**Field Name**</span></span>|<span data-ttu-id="ce4b5-195">**Type**</span><span class="sxs-lookup"><span data-stu-id="ce4b5-195">**Type**</span></span>                   |
|`RoleId`                 |<span data-ttu-id="ce4b5-196">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-196">string</span></span>    |`RoleId`      |<span data-ttu-id="ce4b5-197">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-197">string</span></span>                     |
|`UserId`                 |<span data-ttu-id="ce4b5-198">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-198">string</span></span>    |`UserId`      |<span data-ttu-id="ce4b5-199">string</span><span class="sxs-lookup"><span data-stu-id="ce4b5-199">string</span></span>                     |

<span data-ttu-id="ce4b5-200">创建的迁移脚本时，引用前面的映射表*用户*并*角色*。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-200">Reference the preceding mapping tables when creating a migration script for *Users* and *Roles*.</span></span> <span data-ttu-id="ce4b5-201">下面的示例假定数据库服务器上有两个数据库。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-201">The following example assumes you have two databases on a database server.</span></span> <span data-ttu-id="ce4b5-202">一个数据库包含的现有 ASP.NET 成员身份架构和数据。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-202">One database contains the existing ASP.NET Membership schema and data.</span></span> <span data-ttu-id="ce4b5-203">另*CoreIdentitySample*使用前面所述的步骤创建数据库。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-203">The other *CoreIdentitySample* database was created using steps described earlier.</span></span> <span data-ttu-id="ce4b5-204">注释是以内联形式包含有关更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-204">Comments are included inline for more details.</span></span>

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

<span data-ttu-id="ce4b5-205">完成操作后的脚本，使用成员资格用户填充前面创建的 ASP.NET Core 标识应用。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-205">After completion of the preceding script, the ASP.NET Core Identity app created earlier is populated with Membership users.</span></span> <span data-ttu-id="ce4b5-206">用户需要在登录之前更改其密码。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-206">Users need to change their passwords before logging in.</span></span>

> [!NOTE]
> <span data-ttu-id="ce4b5-207">如果成员资格系统必须具有不匹配其电子邮件地址的用户名的用户，不需要对创建早期为解决此问题的应用更改。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-207">If the Membership system had users with user names that didn't match their email address, changes are required to the app created earlier to accommodate this.</span></span> <span data-ttu-id="ce4b5-208">默认模板需要`UserName`和`Email`相同。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-208">The default template expects `UserName` and `Email` to be the same.</span></span> <span data-ttu-id="ce4b5-209">它们是不同的情况下，登录过程需要进行修改以使用`UserName`而不是`Email`。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-209">For situations in which they're different, the login process needs to be modified to use `UserName` instead of `Email`.</span></span>

<span data-ttu-id="ce4b5-210">在中`PageModel`的登录页上，位于*Pages\Account\Login.cshtml.cs*，删除`[EmailAddress]`属性从*电子邮件*属性。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-210">In the `PageModel` of the Login Page, located at *Pages\Account\Login.cshtml.cs*, remove the `[EmailAddress]` attribute from the *Email* property.</span></span> <span data-ttu-id="ce4b5-211">其重命名为*用户名*。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-211">Rename it to *UserName*.</span></span> <span data-ttu-id="ce4b5-212">这需要更改无论在何处`EmailAddress`所述，在*视图*并*PageModel*。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-212">This requires a change wherever `EmailAddress` is mentioned, in the *View* and *PageModel*.</span></span> <span data-ttu-id="ce4b5-213">结果看起来如下所示：</span><span class="sxs-lookup"><span data-stu-id="ce4b5-213">The result looks like the following:</span></span>

 ![固定的登录名](identity/_static/fixed-login.png)

## <a name="next-steps"></a><span data-ttu-id="ce4b5-215">后续步骤</span><span class="sxs-lookup"><span data-stu-id="ce4b5-215">Next steps</span></span>

<span data-ttu-id="ce4b5-216">在本教程中，您学习了如何移植到 ASP.NET Core 2.0 标识从 SQL 成员资格用户。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-216">In this tutorial, you learned how to port users from SQL membership to ASP.NET Core 2.0 Identity.</span></span> <span data-ttu-id="ce4b5-217">有关 ASP.NET Core 标识的详细信息，请参阅[标识简介](xref:security/authentication/identity)。</span><span class="sxs-lookup"><span data-stu-id="ce4b5-217">For more information regarding ASP.NET Core Identity, see [Introduction to Identity](xref:security/authentication/identity).</span></span>
