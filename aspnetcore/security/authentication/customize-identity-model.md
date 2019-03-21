---
title: 在 ASP.NET Core 中的标识模型自定义
author: ajcvickers
description: 本文介绍如何为 ASP.NET Core 标识自定义的基础的实体框架核心数据模型。
ms.author: avickers
ms.date: 09/24/2018
uid: security/authentication/customize_identity_model
ms.openlocfilehash: 55346c571f180fa17a1108a622d991d15f365bae
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58209457"
---
# <a name="identity-model-customization-in-aspnet-core"></a><span data-ttu-id="68570-103">在 ASP.NET Core 中的标识模型自定义</span><span class="sxs-lookup"><span data-stu-id="68570-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="68570-104">通过[Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="68570-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="68570-105">ASP.NET Core 标识提供了一个框架，用于管理和存储在 ASP.NET Core 应用中的用户帐户。</span><span class="sxs-lookup"><span data-stu-id="68570-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="68570-106">标识添加到项目时**单个用户帐户**选择作为身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="68570-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="68570-107">默认情况下，标识，可以使用的 Entity Framework (EF) Core 数据模型。</span><span class="sxs-lookup"><span data-stu-id="68570-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="68570-108">本文介绍如何自定义的身份标识模型。</span><span class="sxs-lookup"><span data-stu-id="68570-108">This article describes how to customize the Identity model.</span></span>

## <a name="identity-and-ef-core-migrations"></a><span data-ttu-id="68570-109">标识和 EF Core 迁移</span><span class="sxs-lookup"><span data-stu-id="68570-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="68570-110">之前检查模型，它是有助于了解如何标识配合[EF Core 迁移](/ef/core/managing-schemas/migrations/)创建和更新数据库。</span><span class="sxs-lookup"><span data-stu-id="68570-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="68570-111">最高级别的过程是：</span><span class="sxs-lookup"><span data-stu-id="68570-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="68570-112">定义或更新[代码中的数据模型](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="68570-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="68570-113">添加迁移，以将此模型转换为可以应用到数据库的更改。</span><span class="sxs-lookup"><span data-stu-id="68570-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="68570-114">检查迁移正确表示你的意图。</span><span class="sxs-lookup"><span data-stu-id="68570-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="68570-115">将应用迁移来更新要与模型同步的数据库。</span><span class="sxs-lookup"><span data-stu-id="68570-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="68570-116">重复步骤 1 至 4 以进一步优化模型并使数据库保持同步。</span><span class="sxs-lookup"><span data-stu-id="68570-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="68570-117">使用以下方法之一来添加和应用迁移：</span><span class="sxs-lookup"><span data-stu-id="68570-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="68570-118">**程序包管理器控制台**(PMC) 窗口，如果使用 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="68570-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="68570-119">有关详细信息，请参阅[EF Core PMC 工具](/ef/core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="68570-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="68570-120">使用命令行在.NET Core CLI。</span><span class="sxs-lookup"><span data-stu-id="68570-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="68570-121">有关详细信息，请参阅[EF Core.NET 命令行工具](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="68570-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="68570-122">单击**应用迁移**时运行该应用程序错误页上的按钮。</span><span class="sxs-lookup"><span data-stu-id="68570-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="68570-123">ASP.NET Core 具有开发时间错误页处理程序。</span><span class="sxs-lookup"><span data-stu-id="68570-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="68570-124">当应用运行时，该处理程序可以将应用迁移。</span><span class="sxs-lookup"><span data-stu-id="68570-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="68570-125">对于生产应用程序，它通常会更适合从迁移生成 SQL 脚本并将其作为受控的应用和数据库部署的一部分部署数据库更改。</span><span class="sxs-lookup"><span data-stu-id="68570-125">For production apps, it's often more appropriate to generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="68570-126">创建新的应用使用标识时，已经完成上述步骤 1 和 2。</span><span class="sxs-lookup"><span data-stu-id="68570-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="68570-127">也就是说，初始数据模型已存在，并已向项目添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="68570-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="68570-128">初始迁移仍需要将应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="68570-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="68570-129">可以通过以下方法之一应用初始迁移：</span><span class="sxs-lookup"><span data-stu-id="68570-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="68570-130">运行`Update-Database`在 PMC 中。</span><span class="sxs-lookup"><span data-stu-id="68570-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="68570-131">运行`dotnet ef database update`命令行界面中。</span><span class="sxs-lookup"><span data-stu-id="68570-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="68570-132">单击**应用迁移**时运行该应用程序错误页上的按钮。</span><span class="sxs-lookup"><span data-stu-id="68570-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="68570-133">根据对模型进行更改，请重复前面的步骤。</span><span class="sxs-lookup"><span data-stu-id="68570-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-identity-model"></a><span data-ttu-id="68570-134">标识模型</span><span class="sxs-lookup"><span data-stu-id="68570-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="68570-135">实体类型</span><span class="sxs-lookup"><span data-stu-id="68570-135">Entity types</span></span>

<span data-ttu-id="68570-136">标识模型包含以下实体类型。</span><span class="sxs-lookup"><span data-stu-id="68570-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="68570-137">实体类型</span><span class="sxs-lookup"><span data-stu-id="68570-137">Entity type</span></span>|<span data-ttu-id="68570-138">描述</span><span class="sxs-lookup"><span data-stu-id="68570-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="68570-139">表示的用户。</span><span class="sxs-lookup"><span data-stu-id="68570-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="68570-140">表示一个角色。</span><span class="sxs-lookup"><span data-stu-id="68570-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="68570-141">表示一个用户拥有的声明。</span><span class="sxs-lookup"><span data-stu-id="68570-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="68570-142">表示用户的身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="68570-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="68570-143">将用户与登录名相关联。</span><span class="sxs-lookup"><span data-stu-id="68570-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="68570-144">表示一个角色内的所有用户授予的声明。</span><span class="sxs-lookup"><span data-stu-id="68570-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="68570-145">联接实体相关联的用户和角色。</span><span class="sxs-lookup"><span data-stu-id="68570-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="68570-146">实体的类型关系</span><span class="sxs-lookup"><span data-stu-id="68570-146">Entity type relationships</span></span>

<span data-ttu-id="68570-147">[实体类型](#entity-types)彼此相关的以下方面：</span><span class="sxs-lookup"><span data-stu-id="68570-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="68570-148">每个`User`都可以具有许多`UserClaims`。</span><span class="sxs-lookup"><span data-stu-id="68570-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="68570-149">每个`User`都可以具有许多`UserLogins`。</span><span class="sxs-lookup"><span data-stu-id="68570-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="68570-150">每个`User`都可以具有许多`UserTokens`。</span><span class="sxs-lookup"><span data-stu-id="68570-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="68570-151">每个`Role`都可以具有许多关联`RoleClaims`。</span><span class="sxs-lookup"><span data-stu-id="68570-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="68570-152">每个`User`都可以具有许多关联`Roles`，和每个`Role`可与许多关联`Users`。</span><span class="sxs-lookup"><span data-stu-id="68570-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="68570-153">这是需要在数据库中的联接表的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="68570-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="68570-154">联接表由`UserRole`实体。</span><span class="sxs-lookup"><span data-stu-id="68570-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="68570-155">默认模型配置</span><span class="sxs-lookup"><span data-stu-id="68570-155">Default model configuration</span></span>

<span data-ttu-id="68570-156">标识定义许多*上下文类*继承自<xref:Microsoft.EntityFrameworkCore.DbContext>若要配置和使用模型。</span><span class="sxs-lookup"><span data-stu-id="68570-156">Identity defines many *context classes* that inherit from <xref:Microsoft.EntityFrameworkCore.DbContext> to configure and use the model.</span></span> <span data-ttu-id="68570-157">进行此配置使用[EF Core Code First Fluent API](/ef/core/modeling/)中<xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating*>上下文类的方法。</span><span class="sxs-lookup"><span data-stu-id="68570-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the <xref:Microsoft.EntityFrameworkCore.DbContext.OnModelCreating*> method of the context class.</span></span> <span data-ttu-id="68570-158">默认配置是：</span><span class="sxs-lookup"><span data-stu-id="68570-158">The default configuration is:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Primary key
    b.HasKey(u => u.Id);

    // Indexes for "normalized" username and email, to allow efficient lookups
    b.HasIndex(u => u.NormalizedUserName).HasName("UserNameIndex").IsUnique();
    b.HasIndex(u => u.NormalizedEmail).HasName("EmailIndex");

    // Maps to the AspNetUsers table
    b.ToTable("AspNetUsers");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(u => u.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.UserName).HasMaxLength(256);
    b.Property(u => u.NormalizedUserName).HasMaxLength(256);
    b.Property(u => u.Email).HasMaxLength(256);
    b.Property(u => u.NormalizedEmail).HasMaxLength(256);

    // The relationships between User and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each User can have many UserClaims
    b.HasMany<TUserClaim>().WithOne().HasForeignKey(uc => uc.UserId).IsRequired();

    // Each User can have many UserLogins
    b.HasMany<TUserLogin>().WithOne().HasForeignKey(ul => ul.UserId).IsRequired();

    // Each User can have many UserTokens
    b.HasMany<TUserToken>().WithOne().HasForeignKey(ut => ut.UserId).IsRequired();

    // Each User can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.UserId).IsRequired();
});

builder.Entity<TUserClaim>(b =>
{
    // Primary key
    b.HasKey(uc => uc.Id);

    // Maps to the AspNetUserClaims table
    b.ToTable("AspNetUserClaims");
});

builder.Entity<TUserLogin>(b =>
{
    // Composite primary key consisting of the LoginProvider and the key to use
    // with that provider
    b.HasKey(l => new { l.LoginProvider, l.ProviderKey });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(l => l.LoginProvider).HasMaxLength(128);
    b.Property(l => l.ProviderKey).HasMaxLength(128);

    // Maps to the AspNetUserLogins table
    b.ToTable("AspNetUserLogins");
});

builder.Entity<TUserToken>(b =>
{
    // Composite primary key consisting of the UserId, LoginProvider and Name
    b.HasKey(t => new { t.UserId, t.LoginProvider, t.Name });

    // Limit the size of the composite key columns due to common DB restrictions
    b.Property(t => t.LoginProvider).HasMaxLength(maxKeyLength);
    b.Property(t => t.Name).HasMaxLength(maxKeyLength);

    // Maps to the AspNetUserTokens table
    b.ToTable("AspNetUserTokens");
});

builder.Entity<TRole>(b =>
{
    // Primary key
    b.HasKey(r => r.Id);

    // Index for "normalized" role name to allow efficient lookups
    b.HasIndex(r => r.NormalizedName).HasName("RoleNameIndex").IsUnique();

    // Maps to the AspNetRoles table
    b.ToTable("AspNetRoles");

    // A concurrency token for use with the optimistic concurrency checking
    b.Property(r => r.ConcurrencyStamp).IsConcurrencyToken();

    // Limit the size of columns to use efficient database types
    b.Property(u => u.Name).HasMaxLength(256);
    b.Property(u => u.NormalizedName).HasMaxLength(256);

    // The relationships between Role and other entity types
    // Note that these relationships are configured with no navigation properties

    // Each Role can have many entries in the UserRole join table
    b.HasMany<TUserRole>().WithOne().HasForeignKey(ur => ur.RoleId).IsRequired();

    // Each Role can have many associated RoleClaims
    b.HasMany<TRoleClaim>().WithOne().HasForeignKey(rc => rc.RoleId).IsRequired();
});

builder.Entity<TRoleClaim>(b =>
{
    // Primary key
    b.HasKey(rc => rc.Id);

    // Maps to the AspNetRoleClaims table
    b.ToTable("AspNetRoleClaims");
});

builder.Entity<TUserRole>(b =>
{
    // Primary key
    b.HasKey(r => new { r.UserId, r.RoleId });

    // Maps to the AspNetUserRoles table
    b.ToTable("AspNetUserRoles");
});
```

### <a name="model-generic-types"></a><span data-ttu-id="68570-159">模型的泛型类型</span><span class="sxs-lookup"><span data-stu-id="68570-159">Model generic types</span></span>

<span data-ttu-id="68570-160">标识定义默认值[公共语言运行时](/dotnet/standard/glossary#clr)上面列出的每个实体类型 (CLR) 类型。</span><span class="sxs-lookup"><span data-stu-id="68570-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="68570-161">这些类型带有前缀*标识*:</span><span class="sxs-lookup"><span data-stu-id="68570-161">These types are all prefixed with *Identity*:</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="68570-162">而不是直接使用这些类型，类型可以用作基类，这些类对于应用自己的类型。</span><span class="sxs-lookup"><span data-stu-id="68570-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="68570-163">`DbContext`定义标识的类是通用的这样不同的 CLR 类型可以用于一个或多个模型中的实体类型。</span><span class="sxs-lookup"><span data-stu-id="68570-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="68570-164">这些泛型类型还允许`User`主键 (PK) 数据类型更改。</span><span class="sxs-lookup"><span data-stu-id="68570-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="68570-165">对于角色，支持使用标识时<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>应使用类。</span><span class="sxs-lookup"><span data-stu-id="68570-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="68570-166">例如：</span><span class="sxs-lookup"><span data-stu-id="68570-166">For example:</span></span>

```csharp
// Uses all the built-in Identity types
// Uses `string` as the key type
public class IdentityDbContext
    : IdentityDbContext<IdentityUser, IdentityRole, string>
{
}

// Uses the built-in Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityDbContext<TUser>
    : IdentityDbContext<TUser, IdentityRole, string>
        where TUser : IdentityUser
{
}

// Uses the built-in Identity types except with custom User and Role types
// The key type is defined by TKey
public class IdentityDbContext<TUser, TRole, TKey> : IdentityDbContext<
    TUser, TRole, TKey, IdentityUserClaim<TKey>, IdentityUserRole<TKey>,
    IdentityUserLogin<TKey>, IdentityRoleClaim<TKey>, IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TRole : IdentityRole<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments
// The key type is defined by TKey
public abstract class IdentityDbContext<
    TUser, TRole, TKey, TUserClaim, TUserRole, TUserLogin, TRoleClaim, TUserToken>
    : IdentityUserContext<TUser, TKey, TUserClaim, TUserLogin, TUserToken>
         where TUser : IdentityUser<TKey>
         where TRole : IdentityRole<TKey>
         where TKey : IEquatable<TKey>
         where TUserClaim : IdentityUserClaim<TKey>
         where TUserRole : IdentityUserRole<TKey>
         where TUserLogin : IdentityUserLogin<TKey>
         where TRoleClaim : IdentityRoleClaim<TKey>
         where TUserToken : IdentityUserToken<TKey>
```

<span data-ttu-id="68570-167">还有可能在这种情况下使用而无需角色 （仅声明），标识<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext`1>应使用类：</span><span class="sxs-lookup"><span data-stu-id="68570-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext`1> class should be used:</span></span>

```csharp
// Uses the built-in non-role Identity types except with a custom User type
// Uses `string` as the key type
public class IdentityUserContext<TUser>
    : IdentityUserContext<TUser, string>
        where TUser : IdentityUser
{
}

// Uses the built-in non-role Identity types except with a custom User type
// The key type is defined by TKey
public class IdentityUserContext<TUser, TKey> : IdentityUserContext<
    TUser, TKey, IdentityUserClaim<TKey>, IdentityUserLogin<TKey>,
    IdentityUserToken<TKey>>
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
{
}

// No built-in Identity types are used; all are specified by generic arguments, with no roles
// The key type is defined by TKey
public abstract class IdentityUserContext<
    TUser, TKey, TUserClaim, TUserLogin, TUserToken> : DbContext
        where TUser : IdentityUser<TKey>
        where TKey : IEquatable<TKey>
        where TUserClaim : IdentityUserClaim<TKey>
        where TUserLogin : IdentityUserLogin<TKey>
        where TUserToken : IdentityUserToken<TKey>
{
}
```

## <a name="customize-the-model"></a><span data-ttu-id="68570-168">自定义模型</span><span class="sxs-lookup"><span data-stu-id="68570-168">Customize the model</span></span>

<span data-ttu-id="68570-169">模型自定义的起始点是派生自相应的上下文类型。</span><span class="sxs-lookup"><span data-stu-id="68570-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="68570-170">请参阅[泛型类型的模型](#model-generic-types)部分。</span><span class="sxs-lookup"><span data-stu-id="68570-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="68570-171">此上下文类型通常称为`ApplicationDbContext`和创建的 ASP.NET Core 模板。</span><span class="sxs-lookup"><span data-stu-id="68570-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="68570-172">使用的上下文来将模型配置为通过两种方式：</span><span class="sxs-lookup"><span data-stu-id="68570-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="68570-173">提供实体和键类型作为泛型类型参数。</span><span class="sxs-lookup"><span data-stu-id="68570-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="68570-174">重写`OnModelCreating`若要修改这些类型的映射。</span><span class="sxs-lookup"><span data-stu-id="68570-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="68570-175">重写时`OnModelCreating`，`base.OnModelCreating`应首先调用; 重写配置，应调用下一步。</span><span class="sxs-lookup"><span data-stu-id="68570-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="68570-176">EF Core 通常具有配置的最后一个 wins 策略。</span><span class="sxs-lookup"><span data-stu-id="68570-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="68570-177">例如，如果`ToTable`的实体类型的方法首先调用具有一个表名称，并用再说一遍更高版本使用不同的表名称，第二次调用中的表名。</span><span class="sxs-lookup"><span data-stu-id="68570-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="68570-178">自定义用户数据</span><span class="sxs-lookup"><span data-stu-id="68570-178">Custom user data</span></span>

<span data-ttu-id="68570-179">[自定义用户数据](xref:security/authentication/add-user-data)支持通过继承`IdentityUser`。</span><span class="sxs-lookup"><span data-stu-id="68570-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="68570-180">此类型命名惯例做法`ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="68570-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="68570-181">使用`ApplicationUser`作为上下文的泛型参数的类型：</span><span class="sxs-lookup"><span data-stu-id="68570-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }
}
```

<span data-ttu-id="68570-182">无需重写`OnModelCreating`在`ApplicationDbContext`类。</span><span class="sxs-lookup"><span data-stu-id="68570-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="68570-183">EF Core 映射`CustomTag`按照约定的属性。</span><span class="sxs-lookup"><span data-stu-id="68570-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="68570-184">但是，需要更新，以创建新数据库`CustomTag`列。</span><span class="sxs-lookup"><span data-stu-id="68570-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="68570-185">若要创建列，请添加迁移时，并如中所述，然后更新数据库[标识和 EF Core 迁移](#identity-and-ef-core-migrations)。</span><span class="sxs-lookup"><span data-stu-id="68570-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="68570-186">更新`Startup.ConfigureServices`以使用新`ApplicationUser`类：</span><span class="sxs-lookup"><span data-stu-id="68570-186">Update `Startup.ConfigureServices` to use the new `ApplicationUser` class:</span></span>

::: moniker range=">= aspnetcore-2.1"

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="68570-187">在 ASP.NET Core 2.1 或更高版本，作为一个 Razor 类库提供标识。</span><span class="sxs-lookup"><span data-stu-id="68570-187">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="68570-188">有关详细信息，请参阅 <xref:security/authentication/scaffold-identity>。</span><span class="sxs-lookup"><span data-stu-id="68570-188">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="68570-189">因此，前面的代码需要调用<xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>。</span><span class="sxs-lookup"><span data-stu-id="68570-189">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="68570-190">如果标识基架用于标识文件添加到项目，删除对调用`AddDefaultUI`。</span><span class="sxs-lookup"><span data-stu-id="68570-190">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="68570-191">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="68570-191">For more information, see:</span></span>

* [<span data-ttu-id="68570-192">基架标识</span><span class="sxs-lookup"><span data-stu-id="68570-192">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="68570-193">添加、 下载和删除到标识的自定义用户数据</span><span class="sxs-lookup"><span data-stu-id="68570-193">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

::: moniker-end

::: moniker range="= aspnetcore-2.0"

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
        .AddDefaultTokenProviders();
```

::: moniker-end

### <a name="change-the-primary-key-type"></a><span data-ttu-id="68570-194">更改主键类型</span><span class="sxs-lookup"><span data-stu-id="68570-194">Change the primary key type</span></span>

<span data-ttu-id="68570-195">为 PK 列的数据类型创建数据库后更改是很多数据库系统有问题。</span><span class="sxs-lookup"><span data-stu-id="68570-195">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="68570-196">更改 PK 通常涉及删除并重新创建表。</span><span class="sxs-lookup"><span data-stu-id="68570-196">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="68570-197">因此，密钥类型时，应指定在初始迁移创建的数据库。</span><span class="sxs-lookup"><span data-stu-id="68570-197">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="68570-198">请按照下列步骤更改 PK 类型：</span><span class="sxs-lookup"><span data-stu-id="68570-198">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="68570-199">如果数据库已创建的 PK 更改之前，运行`Drop-Database`(PMC) 或`dotnet ef database drop`(.NET Core CLI) 将其删除。</span><span class="sxs-lookup"><span data-stu-id="68570-199">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="68570-200">在确认删除数据库，删除以进行初始迁移`Remove-Migration`(PMC) 或`dotnet ef migrations remove`(.NET Core CLI)。</span><span class="sxs-lookup"><span data-stu-id="68570-200">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="68570-201">更新`ApplicationDbContext`类进行派生<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext`3>。</span><span class="sxs-lookup"><span data-stu-id="68570-201">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext`3>.</span></span> <span data-ttu-id="68570-202">指定的新类型`TKey`。</span><span class="sxs-lookup"><span data-stu-id="68570-202">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="68570-203">例如，若要使用`Guid`密钥类型：</span><span class="sxs-lookup"><span data-stu-id="68570-203">For example, to use a `Guid` key type:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<IdentityUser<Guid>, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    ::: moniker range=">= aspnetcore-2.0"

    <span data-ttu-id="68570-204">在前面的代码中，泛型类<xref:Microsoft.AspNetCore.Identity.IdentityUser`1>和<xref:Microsoft.AspNetCore.Identity.IdentityRole`1>必须指定要使用新的密钥类型。</span><span class="sxs-lookup"><span data-stu-id="68570-204">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser`1> and <xref:Microsoft.AspNetCore.Identity.IdentityRole`1> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="68570-205">在前面的代码中，泛型类<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser`1>和<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole`1>必须指定要使用新的密钥类型。</span><span class="sxs-lookup"><span data-stu-id="68570-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser`1> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole`1> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="68570-206">`Startup.ConfigureServices` 必须更新为使用一般用户：</span><span class="sxs-lookup"><span data-stu-id="68570-206">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<IdentityUser<Guid>>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<IdentityUser<Guid>, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    ::: moniker-end

4. <span data-ttu-id="68570-207">如果自定义`ApplicationUser`正在使用类中，更新要继承的类`IdentityUser`。</span><span class="sxs-lookup"><span data-stu-id="68570-207">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="68570-208">例如：</span><span class="sxs-lookup"><span data-stu-id="68570-208">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="68570-209">更新`ApplicationDbContext`来引用自定义`ApplicationUser`类：</span><span class="sxs-lookup"><span data-stu-id="68570-209">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

    ```csharp
    public class ApplicationDbContext
        : IdentityDbContext<ApplicationUser, IdentityRole<Guid>, Guid>
    {
        public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
            : base(options)
        {
        }
    }
    ```

    <span data-ttu-id="68570-210">注册自定义数据库上下文类添加中的标识服务时`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="68570-210">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="68570-211">主键的数据类型推断通过分析<xref:Microsoft.EntityFrameworkCore.DbContext>对象。</span><span class="sxs-lookup"><span data-stu-id="68570-211">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    <span data-ttu-id="68570-212">在 ASP.NET Core 2.1 或更高版本，作为一个 Razor 类库提供标识。</span><span class="sxs-lookup"><span data-stu-id="68570-212">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="68570-213">有关详细信息，请参阅 <xref:security/authentication/scaffold-identity>。</span><span class="sxs-lookup"><span data-stu-id="68570-213">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="68570-214">因此，前面的代码需要调用<xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>。</span><span class="sxs-lookup"><span data-stu-id="68570-214">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="68570-215">如果标识基架用于标识文件添加到项目，删除对调用`AddDefaultUI`。</span><span class="sxs-lookup"><span data-stu-id="68570-215">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="68570-216">主键的数据类型推断通过分析<xref:Microsoft.EntityFrameworkCore.DbContext>对象。</span><span class="sxs-lookup"><span data-stu-id="68570-216">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="68570-217"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>方法接受`TKey`，该值指示主键的数据类型的类型。</span><span class="sxs-lookup"><span data-stu-id="68570-217">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="68570-218">如果自定义`ApplicationRole`正在使用类中，更新要继承的类`IdentityRole<TKey>`。</span><span class="sxs-lookup"><span data-stu-id="68570-218">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="68570-219">例如：</span><span class="sxs-lookup"><span data-stu-id="68570-219">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="68570-220">更新`ApplicationDbContext`来引用自定义`ApplicationRole`类。</span><span class="sxs-lookup"><span data-stu-id="68570-220">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="68570-221">例如，下面的类引用自定义`ApplicationUser`和自定义`ApplicationRole`:</span><span class="sxs-lookup"><span data-stu-id="68570-221">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="68570-222">注册自定义数据库上下文类添加中的标识服务时`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="68570-222">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="68570-223">主键的数据类型推断通过分析<xref:Microsoft.EntityFrameworkCore.DbContext>对象。</span><span class="sxs-lookup"><span data-stu-id="68570-223">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    <span data-ttu-id="68570-224">在 ASP.NET Core 2.1 或更高版本，作为一个 Razor 类库提供标识。</span><span class="sxs-lookup"><span data-stu-id="68570-224">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="68570-225">有关详细信息，请参阅 <xref:security/authentication/scaffold-identity>。</span><span class="sxs-lookup"><span data-stu-id="68570-225">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="68570-226">因此，前面的代码需要调用<xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>。</span><span class="sxs-lookup"><span data-stu-id="68570-226">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="68570-227">如果标识基架用于标识文件添加到项目，删除对调用`AddDefaultUI`。</span><span class="sxs-lookup"><span data-stu-id="68570-227">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="68570-228">注册自定义数据库上下文类添加中的标识服务时`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="68570-228">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="68570-229">主键的数据类型推断通过分析<xref:Microsoft.EntityFrameworkCore.DbContext>对象。</span><span class="sxs-lookup"><span data-stu-id="68570-229">The primary key's data type is inferred by analyzing the <xref:Microsoft.EntityFrameworkCore.DbContext> object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="68570-230">注册自定义数据库上下文类添加中的标识服务时`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="68570-230">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="68570-231"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>方法接受`TKey`，该值指示主键的数据类型的类型。</span><span class="sxs-lookup"><span data-stu-id="68570-231">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="68570-232">添加导航属性</span><span class="sxs-lookup"><span data-stu-id="68570-232">Add navigation properties</span></span>

<span data-ttu-id="68570-233">更改关系的模型配置可能会更难于进行其他更改。</span><span class="sxs-lookup"><span data-stu-id="68570-233">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="68570-234">若要替换现有关系，而不是创建新的其他关系，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="68570-234">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="68570-235">具体而言，已更改的关系必须指定相同的外键 (FK) 属性作为现有的关系。</span><span class="sxs-lookup"><span data-stu-id="68570-235">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="68570-236">例如，之间的关系`Users`和`UserClaims`是，默认情况下，按如下所示指定：</span><span class="sxs-lookup"><span data-stu-id="68570-236">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

```csharp
builder.Entity<TUser>(b =>
{
    // Each User can have many UserClaims
    b.HasMany<TUserClaim>()
     .WithOne()
     .HasForeignKey(uc => uc.UserId)
     .IsRequired();
});
```

<span data-ttu-id="68570-237">此关系的 FK 指定为`UserClaim.UserId`属性。</span><span class="sxs-lookup"><span data-stu-id="68570-237">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="68570-238">`HasMany` 和`WithOne`调用不带参数，以创建不使用导航属性的关系。</span><span class="sxs-lookup"><span data-stu-id="68570-238">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="68570-239">导航属性添加到`ApplicationUser`，允许关联`UserClaims`若要从用户中引用：</span><span class="sxs-lookup"><span data-stu-id="68570-239">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="68570-240">`TKey`为`IdentityUserClaim<TKey>`是用户的 PK 为指定的类型。</span><span class="sxs-lookup"><span data-stu-id="68570-240">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="68570-241">在这种情况下，`TKey`是`string`因为正在使用默认值。</span><span class="sxs-lookup"><span data-stu-id="68570-241">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="68570-242">它具有**不**的 PK 类型`UserClaim`实体类型。</span><span class="sxs-lookup"><span data-stu-id="68570-242">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="68570-243">现在，已存在的导航属性，它必须在配置`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="68570-243">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();
        });
    }
}
```

<span data-ttu-id="68570-244">请注意，完全按照以前，只能使用导航属性调用中指定配置关系`HasMany`。</span><span class="sxs-lookup"><span data-stu-id="68570-244">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="68570-245">在 EF 模型中，不是数据库仅存在导航属性。</span><span class="sxs-lookup"><span data-stu-id="68570-245">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="68570-246">因为关系 fk 后未发生更改，这种类型的模型更改不需要更新的数据库。</span><span class="sxs-lookup"><span data-stu-id="68570-246">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="68570-247">可以通过添加迁移后进行更改选中此项。</span><span class="sxs-lookup"><span data-stu-id="68570-247">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="68570-248">`Up`和`Down`方法为空。</span><span class="sxs-lookup"><span data-stu-id="68570-248">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="68570-249">添加所有用户导航属性</span><span class="sxs-lookup"><span data-stu-id="68570-249">Add all User navigation properties</span></span>

<span data-ttu-id="68570-250">下面的示例作为指南使用上的一节中，在用户配置单向导航属性的所有关系：</span><span class="sxs-lookup"><span data-stu-id="68570-250">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<IdentityUserRole<string>> UserRoles { get; set; }
}
```

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne()
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });
    }
}
```

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="68570-251">添加用户和角色导航属性</span><span class="sxs-lookup"><span data-stu-id="68570-251">Add User and Role navigation properties</span></span>

<span data-ttu-id="68570-252">下面的示例作为指南使用上的一节中，用户和角色上配置所有关系的导航的属性：</span><span class="sxs-lookup"><span data-stu-id="68570-252">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
    public virtual ICollection<IdentityUserLogin<string>> Logins { get; set; }
    public virtual ICollection<IdentityUserToken<string>> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        IdentityUserClaim<string>, ApplicationUserRole, IdentityUserLogin<string>,
        IdentityRoleClaim<string>, IdentityUserToken<string>>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne()
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne()
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne()
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();
        });

    }
}
```

<span data-ttu-id="68570-253">注意：</span><span class="sxs-lookup"><span data-stu-id="68570-253">Notes:</span></span>

* <span data-ttu-id="68570-254">此示例还包括`UserRole`联接实体，这将需要导航从用户到角色的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="68570-254">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="68570-255">请记得要更改类型的导航属性以反映该`ApplicationXxx`类型现在正在使用而不是`IdentityXxx`类型。</span><span class="sxs-lookup"><span data-stu-id="68570-255">Remember to change the types of the navigation properties to reflect that `ApplicationXxx` types are now being used instead of `IdentityXxx` types.</span></span>
* <span data-ttu-id="68570-256">请记住使用`ApplicationXxx`中泛型`ApplicationContext`定义。</span><span class="sxs-lookup"><span data-stu-id="68570-256">Remember to use the `ApplicationXxx` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="68570-257">添加所有导航属性</span><span class="sxs-lookup"><span data-stu-id="68570-257">Add all navigation properties</span></span>

<span data-ttu-id="68570-258">下面的示例作为指南使用上的一节中，所有实体类型上配置所有关系的导航的属性：</span><span class="sxs-lookup"><span data-stu-id="68570-258">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<ApplicationUserClaim> Claims { get; set; }
    public virtual ICollection<ApplicationUserLogin> Logins { get; set; }
    public virtual ICollection<ApplicationUserToken> Tokens { get; set; }
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
}

public class ApplicationRole : IdentityRole
{
    public virtual ICollection<ApplicationUserRole> UserRoles { get; set; }
    public virtual ICollection<ApplicationRoleClaim> RoleClaims { get; set; }
}

public class ApplicationUserRole : IdentityUserRole<string>
{
    public virtual ApplicationUser User { get; set; }
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserClaim : IdentityUserClaim<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationUserLogin : IdentityUserLogin<string>
{
    public virtual ApplicationUser User { get; set; }
}

public class ApplicationRoleClaim : IdentityRoleClaim<string>
{
    public virtual ApplicationRole Role { get; set; }
}

public class ApplicationUserToken : IdentityUserToken<string>
{
    public virtual ApplicationUser User { get; set; }
}
```

```csharp
public class ApplicationDbContext
    : IdentityDbContext<
        ApplicationUser, ApplicationRole, string,
        ApplicationUserClaim, ApplicationUserRole, ApplicationUserLogin,
        ApplicationRoleClaim, ApplicationUserToken>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        modelBuilder.Entity<ApplicationUser>(b =>
        {
            // Each User can have many UserClaims
            b.HasMany(e => e.Claims)
                .WithOne(e => e.User)
                .HasForeignKey(uc => uc.UserId)
                .IsRequired();

            // Each User can have many UserLogins
            b.HasMany(e => e.Logins)
                .WithOne(e => e.User)
                .HasForeignKey(ul => ul.UserId)
                .IsRequired();

            // Each User can have many UserTokens
            b.HasMany(e => e.Tokens)
                .WithOne(e => e.User)
                .HasForeignKey(ut => ut.UserId)
                .IsRequired();

            // Each User can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.User)
                .HasForeignKey(ur => ur.UserId)
                .IsRequired();
        });

        modelBuilder.Entity<ApplicationRole>(b =>
        {
            // Each Role can have many entries in the UserRole join table
            b.HasMany(e => e.UserRoles)
                .WithOne(e => e.Role)
                .HasForeignKey(ur => ur.RoleId)
                .IsRequired();

            // Each Role can have many associated RoleClaims
            b.HasMany(e => e.RoleClaims)
                .WithOne(e => e.Role)
                .HasForeignKey(rc => rc.RoleId)
                .IsRequired();
        });
    }
}
```

### <a name="use-composite-keys"></a><span data-ttu-id="68570-259">使用复合键</span><span class="sxs-lookup"><span data-stu-id="68570-259">Use composite keys</span></span>

<span data-ttu-id="68570-260">前面几节所示更改的标识模型中使用的密钥类型。</span><span class="sxs-lookup"><span data-stu-id="68570-260">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="68570-261">更改要使用的复合键的标识密钥模型不支持或建议。</span><span class="sxs-lookup"><span data-stu-id="68570-261">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="68570-262">使用复合密钥与标识涉及更改标识管理器代码与模型交互的方式。</span><span class="sxs-lookup"><span data-stu-id="68570-262">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="68570-263">此自定义已超出本文的讨论范围。</span><span class="sxs-lookup"><span data-stu-id="68570-263">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="68570-264">更改表/列名称和分面</span><span class="sxs-lookup"><span data-stu-id="68570-264">Change table/column names and facets</span></span>

<span data-ttu-id="68570-265">若要更改表和列的名称，请调用`base.OnModelCreating`。</span><span class="sxs-lookup"><span data-stu-id="68570-265">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="68570-266">然后，添加配置以重写任何默认值。</span><span class="sxs-lookup"><span data-stu-id="68570-266">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="68570-267">例如，若要更改所有标识表的名称：</span><span class="sxs-lookup"><span data-stu-id="68570-267">For example, to change the name of all the Identity tables:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.ToTable("MyUsers");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.ToTable("MyUserClaims");
    });

    modelBuilder.Entity<IdentityUserLogin<string>>(b =>
    {
        b.ToTable("MyUserLogins");
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.ToTable("MyUserTokens");
    });

    modelBuilder.Entity<IdentityRole>(b =>
    {
        b.ToTable("MyRoles");
    });

    modelBuilder.Entity<IdentityRoleClaim<string>>(b =>
    {
        b.ToTable("MyRoleClaims");
    });

    modelBuilder.Entity<IdentityUserRole<string>>(b =>
    {
        b.ToTable("MyUserRoles");
    });
}
```

<span data-ttu-id="68570-268">这些示例使用默认标识类型。</span><span class="sxs-lookup"><span data-stu-id="68570-268">These examples use the default Identity types.</span></span> <span data-ttu-id="68570-269">如果使用的应用类型，如`ApplicationUser`，配置该类型而不是默认类型。</span><span class="sxs-lookup"><span data-stu-id="68570-269">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="68570-270">下面的示例更改某些列名称：</span><span class="sxs-lookup"><span data-stu-id="68570-270">The following example changes some column names:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(e => e.Email).HasColumnName("EMail");
    });

    modelBuilder.Entity<IdentityUserClaim<string>>(b =>
    {
        b.Property(e => e.ClaimType).HasColumnName("CType");
        b.Property(e => e.ClaimValue).HasColumnName("CValue");
    });
}
```

<span data-ttu-id="68570-271">某些类型的数据库列可以配置某些*方面*(例如，最大值`string`允许的长度)。</span><span class="sxs-lookup"><span data-stu-id="68570-271">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="68570-272">下面的示例设置多个列的最大长度`string`模型中的属性：</span><span class="sxs-lookup"><span data-stu-id="68570-272">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.Entity<IdentityUser>(b =>
    {
        b.Property(u => u.UserName).HasMaxLength(128);
        b.Property(u => u.NormalizedUserName).HasMaxLength(128);
        b.Property(u => u.Email).HasMaxLength(128);
        b.Property(u => u.NormalizedEmail).HasMaxLength(128);
    });

    modelBuilder.Entity<IdentityUserToken<string>>(b =>
    {
        b.Property(t => t.LoginProvider).HasMaxLength(128);
        b.Property(t => t.Name).HasMaxLength(128);
    });
}
```

### <a name="map-to-a-different-schema"></a><span data-ttu-id="68570-273">将映射到不同的架构</span><span class="sxs-lookup"><span data-stu-id="68570-273">Map to a different schema</span></span>

<span data-ttu-id="68570-274">跨数据库提供程序，架构的行为可能有所不同。</span><span class="sxs-lookup"><span data-stu-id="68570-274">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="68570-275">对于 SQL Server，默认值是创建中的所有表*dbo*架构。</span><span class="sxs-lookup"><span data-stu-id="68570-275">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="68570-276">可以在不同的架构中创建的表。</span><span class="sxs-lookup"><span data-stu-id="68570-276">The tables can be created in a different schema.</span></span> <span data-ttu-id="68570-277">例如：</span><span class="sxs-lookup"><span data-stu-id="68570-277">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="68570-278">延迟加载</span><span class="sxs-lookup"><span data-stu-id="68570-278">Lazy loading</span></span>

<span data-ttu-id="68570-279">在本部分中，添加了支持的身份标识模型中的延迟加载代理。</span><span class="sxs-lookup"><span data-stu-id="68570-279">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="68570-280">延迟加载很有用，因为它允许使用而无需首先确保它们正在加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="68570-280">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="68570-281">实体类型可适用于采用多种方式的延迟加载中所述[EF Core 文档](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="68570-281">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="68570-282">为简单起见，使用该软件需要延迟加载代理：</span><span class="sxs-lookup"><span data-stu-id="68570-282">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="68570-283">安装[-Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)包。</span><span class="sxs-lookup"><span data-stu-id="68570-283">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="68570-284">调用<xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*>内<xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*>。</span><span class="sxs-lookup"><span data-stu-id="68570-284">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside <xref:Microsoft.Extensions.DependencyInjection.EntityFrameworkServiceCollectionExtensions.AddDbContext*>.</span></span>
* <span data-ttu-id="68570-285">公共实体类型与`public virtual`导航属性。</span><span class="sxs-lookup"><span data-stu-id="68570-285">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="68570-286">下面的示例演示如何调用`UseLazyLoadingProxies`在`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="68570-286">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="68570-287">请参阅前面的示例将导航属性添加到实体类型有关的指南。</span><span class="sxs-lookup"><span data-stu-id="68570-287">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="68570-288">其他资源</span><span class="sxs-lookup"><span data-stu-id="68570-288">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
