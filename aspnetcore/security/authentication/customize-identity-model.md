---
title: 在 ASP.NET Core 中的标识模型自定义
author: ajcvickers
description: 本文介绍如何为 ASP.NET Core 标识自定义的基础的实体框架核心数据模型。
ms.author: avickers
ms.date: 07/01/2019
uid: security/authentication/customize_identity_model
ms.openlocfilehash: f549fdff4a416b5fadcb2b1078b051bbab8e402e
ms.sourcegitcommit: eb3e51d58dd713eefc242148f45bd9486be3a78a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67500477"
---
# <a name="identity-model-customization-in-aspnet-core"></a><span data-ttu-id="3efeb-103">在 ASP.NET Core 中的标识模型自定义</span><span class="sxs-lookup"><span data-stu-id="3efeb-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="3efeb-104">通过[Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="3efeb-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="3efeb-105">ASP.NET Core 标识提供了一个框架，用于管理和存储在 ASP.NET Core 应用中的用户帐户。</span><span class="sxs-lookup"><span data-stu-id="3efeb-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="3efeb-106">标识添加到项目时**单个用户帐户**选择作为身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="3efeb-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="3efeb-107">默认情况下，标识，可以使用的 Entity Framework (EF) Core 数据模型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="3efeb-108">本文介绍如何自定义的身份标识模型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-108">This article describes how to customize the Identity model.</span></span>

## <a name="identity-and-ef-core-migrations"></a><span data-ttu-id="3efeb-109">标识和 EF Core 迁移</span><span class="sxs-lookup"><span data-stu-id="3efeb-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="3efeb-110">之前检查模型，它是有助于了解如何标识配合[EF Core 迁移](/ef/core/managing-schemas/migrations/)创建和更新数据库。</span><span class="sxs-lookup"><span data-stu-id="3efeb-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="3efeb-111">最高级别的过程是：</span><span class="sxs-lookup"><span data-stu-id="3efeb-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="3efeb-112">定义或更新[代码中的数据模型](/ef/core/modeling/)。</span><span class="sxs-lookup"><span data-stu-id="3efeb-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="3efeb-113">添加迁移，以将此模型转换为可以应用到数据库的更改。</span><span class="sxs-lookup"><span data-stu-id="3efeb-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="3efeb-114">检查迁移正确表示你的意图。</span><span class="sxs-lookup"><span data-stu-id="3efeb-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="3efeb-115">将应用迁移来更新要与模型同步的数据库。</span><span class="sxs-lookup"><span data-stu-id="3efeb-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="3efeb-116">重复步骤 1 至 4 以进一步优化模型并使数据库保持同步。</span><span class="sxs-lookup"><span data-stu-id="3efeb-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="3efeb-117">使用以下方法之一来添加和应用迁移：</span><span class="sxs-lookup"><span data-stu-id="3efeb-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="3efeb-118">**程序包管理器控制台**(PMC) 窗口，如果使用 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="3efeb-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="3efeb-119">有关详细信息，请参阅[EF Core PMC 工具](/ef/core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="3efeb-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="3efeb-120">使用命令行在.NET Core CLI。</span><span class="sxs-lookup"><span data-stu-id="3efeb-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="3efeb-121">有关详细信息，请参阅[EF Core.NET 命令行工具](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="3efeb-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="3efeb-122">单击**应用迁移**时运行该应用程序错误页上的按钮。</span><span class="sxs-lookup"><span data-stu-id="3efeb-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="3efeb-123">ASP.NET Core 具有开发时间错误页处理程序。</span><span class="sxs-lookup"><span data-stu-id="3efeb-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="3efeb-124">当应用运行时，该处理程序可以将应用迁移。</span><span class="sxs-lookup"><span data-stu-id="3efeb-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="3efeb-125">生产应用程序通常从迁移生成 SQL 脚本和部署数据库更改受控制的应用程序和数据库部署的一部分。</span><span class="sxs-lookup"><span data-stu-id="3efeb-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="3efeb-126">创建新的应用使用标识时，已经完成上述步骤 1 和 2。</span><span class="sxs-lookup"><span data-stu-id="3efeb-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="3efeb-127">也就是说，初始数据模型已存在，并已向项目添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="3efeb-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="3efeb-128">初始迁移仍需要将应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="3efeb-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="3efeb-129">可以通过以下方法之一应用初始迁移：</span><span class="sxs-lookup"><span data-stu-id="3efeb-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="3efeb-130">运行`Update-Database`在 PMC 中。</span><span class="sxs-lookup"><span data-stu-id="3efeb-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="3efeb-131">运行`dotnet ef database update`命令行界面中。</span><span class="sxs-lookup"><span data-stu-id="3efeb-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="3efeb-132">单击**应用迁移**时运行该应用程序错误页上的按钮。</span><span class="sxs-lookup"><span data-stu-id="3efeb-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="3efeb-133">根据对模型进行更改，请重复前面的步骤。</span><span class="sxs-lookup"><span data-stu-id="3efeb-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-identity-model"></a><span data-ttu-id="3efeb-134">标识模型</span><span class="sxs-lookup"><span data-stu-id="3efeb-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="3efeb-135">实体类型</span><span class="sxs-lookup"><span data-stu-id="3efeb-135">Entity types</span></span>

<span data-ttu-id="3efeb-136">标识模型包含以下实体类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="3efeb-137">实体类型</span><span class="sxs-lookup"><span data-stu-id="3efeb-137">Entity type</span></span>|<span data-ttu-id="3efeb-138">描述</span><span class="sxs-lookup"><span data-stu-id="3efeb-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="3efeb-139">表示的用户。</span><span class="sxs-lookup"><span data-stu-id="3efeb-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="3efeb-140">表示一个角色。</span><span class="sxs-lookup"><span data-stu-id="3efeb-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="3efeb-141">表示一个用户拥有的声明。</span><span class="sxs-lookup"><span data-stu-id="3efeb-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="3efeb-142">表示用户的身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="3efeb-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="3efeb-143">将用户与登录名相关联。</span><span class="sxs-lookup"><span data-stu-id="3efeb-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="3efeb-144">表示一个角色内的所有用户授予的声明。</span><span class="sxs-lookup"><span data-stu-id="3efeb-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="3efeb-145">联接实体相关联的用户和角色。</span><span class="sxs-lookup"><span data-stu-id="3efeb-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="3efeb-146">实体的类型关系</span><span class="sxs-lookup"><span data-stu-id="3efeb-146">Entity type relationships</span></span>

<span data-ttu-id="3efeb-147">[实体类型](#entity-types)彼此相关的以下方面：</span><span class="sxs-lookup"><span data-stu-id="3efeb-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="3efeb-148">每个`User`都可以具有许多`UserClaims`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="3efeb-149">每个`User`都可以具有许多`UserLogins`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="3efeb-150">每个`User`都可以具有许多`UserTokens`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="3efeb-151">每个`Role`都可以具有许多关联`RoleClaims`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="3efeb-152">每个`User`都可以具有许多关联`Roles`，和每个`Role`可与许多关联`Users`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="3efeb-153">这是需要在数据库中的联接表的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="3efeb-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="3efeb-154">联接表由`UserRole`实体。</span><span class="sxs-lookup"><span data-stu-id="3efeb-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="3efeb-155">默认模型配置</span><span class="sxs-lookup"><span data-stu-id="3efeb-155">Default model configuration</span></span>

<span data-ttu-id="3efeb-156">标识定义许多*上下文类*继承自[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)若要配置和使用模型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-156">Identity defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="3efeb-157">进行此配置使用[EF Core 代码 First Fluent API](/ef/core/modeling/)中[OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating)上下文类的方法。</span><span class="sxs-lookup"><span data-stu-id="3efeb-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="3efeb-158">默认配置是：</span><span class="sxs-lookup"><span data-stu-id="3efeb-158">The default configuration is:</span></span>

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

### <a name="model-generic-types"></a><span data-ttu-id="3efeb-159">模型的泛型类型</span><span class="sxs-lookup"><span data-stu-id="3efeb-159">Model generic types</span></span>

<span data-ttu-id="3efeb-160">标识定义默认值[公共语言运行时](/dotnet/standard/glossary#clr)上面列出的每个实体类型 (CLR) 类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="3efeb-161">这些类型带有前缀*标识*:</span><span class="sxs-lookup"><span data-stu-id="3efeb-161">These types are all prefixed with *Identity*:</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="3efeb-162">而不是直接使用这些类型，类型可以用作基类，这些类对于应用自己的类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="3efeb-163">`DbContext`定义标识的类是通用的这样不同的 CLR 类型可以用于一个或多个模型中的实体类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="3efeb-164">这些泛型类型还允许`User`主键 (PK) 数据类型更改。</span><span class="sxs-lookup"><span data-stu-id="3efeb-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="3efeb-165">对于角色，支持使用标识时<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext>应使用类。</span><span class="sxs-lookup"><span data-stu-id="3efeb-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="3efeb-166">例如：</span><span class="sxs-lookup"><span data-stu-id="3efeb-166">For example:</span></span>

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

<span data-ttu-id="3efeb-167">还有可能在这种情况下使用而无需角色 （仅声明），标识<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601>应使用类：</span><span class="sxs-lookup"><span data-stu-id="3efeb-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> class should be used:</span></span>

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

## <a name="customize-the-model"></a><span data-ttu-id="3efeb-168">自定义模型</span><span class="sxs-lookup"><span data-stu-id="3efeb-168">Customize the model</span></span>

<span data-ttu-id="3efeb-169">模型自定义的起始点是派生自相应的上下文类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="3efeb-170">请参阅[泛型类型的模型](#model-generic-types)部分。</span><span class="sxs-lookup"><span data-stu-id="3efeb-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="3efeb-171">此上下文类型通常称为`ApplicationDbContext`和创建的 ASP.NET Core 模板。</span><span class="sxs-lookup"><span data-stu-id="3efeb-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="3efeb-172">使用的上下文来将模型配置为通过两种方式：</span><span class="sxs-lookup"><span data-stu-id="3efeb-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="3efeb-173">提供实体和键类型作为泛型类型参数。</span><span class="sxs-lookup"><span data-stu-id="3efeb-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="3efeb-174">重写`OnModelCreating`若要修改这些类型的映射。</span><span class="sxs-lookup"><span data-stu-id="3efeb-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="3efeb-175">重写时`OnModelCreating`，`base.OnModelCreating`应首先调用; 重写配置，应调用下一步。</span><span class="sxs-lookup"><span data-stu-id="3efeb-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="3efeb-176">EF Core 通常具有配置的最后一个 wins 策略。</span><span class="sxs-lookup"><span data-stu-id="3efeb-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="3efeb-177">例如，如果`ToTable`的实体类型的方法首先调用具有一个表名称，并用再说一遍更高版本使用不同的表名称，第二次调用中的表名。</span><span class="sxs-lookup"><span data-stu-id="3efeb-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="3efeb-178">自定义用户数据</span><span class="sxs-lookup"><span data-stu-id="3efeb-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

<span data-ttu-id="3efeb-179">[自定义用户数据](xref:security/authentication/add-user-data)支持通过继承`IdentityUser`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="3efeb-180">此类型命名惯例做法`ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="3efeb-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="3efeb-181">使用`ApplicationUser`作为上下文的泛型参数的类型：</span><span class="sxs-lookup"><span data-stu-id="3efeb-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

```csharp
public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    protected override void OnModelCreating(ModelBuilder builder)
    {
        base.OnModelCreating(builder);
    }
}
```

<span data-ttu-id="3efeb-182">无需重写`OnModelCreating`在`ApplicationDbContext`类。</span><span class="sxs-lookup"><span data-stu-id="3efeb-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="3efeb-183">EF Core 映射`CustomTag`按照约定的属性。</span><span class="sxs-lookup"><span data-stu-id="3efeb-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="3efeb-184">但是，需要更新，以创建新数据库`CustomTag`列。</span><span class="sxs-lookup"><span data-stu-id="3efeb-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="3efeb-185">若要创建列，请添加迁移时，并如中所述，然后更新数据库[标识和 EF Core 迁移](#identity-and-ef-core-migrations)。</span><span class="sxs-lookup"><span data-stu-id="3efeb-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="3efeb-186">更新*Pages/Shared/_LoginPartial.cshtml*和替换`IdentityUser`与`ApplicationUser`:</span><span class="sxs-lookup"><span data-stu-id="3efeb-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `IdentityUser` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="3efeb-187">更新*Areas/Identity/IdentityHostingStartup.cs*或`Startup.ConfigureServices`和替换`IdentityUser`与`ApplicationUser`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-187">Update *Areas/Identity/IdentityHostingStartup.cs*  or `Startup.ConfigureServices` and replace `IdentityUser` with `ApplicationUser`.</span></span>

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="3efeb-188">在 ASP.NET Core 2.1 或更高版本，作为一个 Razor 类库提供标识。</span><span class="sxs-lookup"><span data-stu-id="3efeb-188">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="3efeb-189">有关详细信息，请参阅 <xref:security/authentication/scaffold-identity>。</span><span class="sxs-lookup"><span data-stu-id="3efeb-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="3efeb-190">因此，前面的代码需要调用<xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>。</span><span class="sxs-lookup"><span data-stu-id="3efeb-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="3efeb-191">如果标识基架用于标识文件添加到项目，删除对调用`AddDefaultUI`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-191">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="3efeb-192">有关详细信息，请参见:</span><span class="sxs-lookup"><span data-stu-id="3efeb-192">For more information, see:</span></span>

* [<span data-ttu-id="3efeb-193">基架标识</span><span class="sxs-lookup"><span data-stu-id="3efeb-193">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="3efeb-194">添加、 下载和删除到标识的自定义用户数据</span><span class="sxs-lookup"><span data-stu-id="3efeb-194">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="3efeb-195">更改主键类型</span><span class="sxs-lookup"><span data-stu-id="3efeb-195">Change the primary key type</span></span>

<span data-ttu-id="3efeb-196">为 PK 列的数据类型创建数据库后更改是很多数据库系统有问题。</span><span class="sxs-lookup"><span data-stu-id="3efeb-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="3efeb-197">更改 PK 通常涉及删除并重新创建表。</span><span class="sxs-lookup"><span data-stu-id="3efeb-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="3efeb-198">因此，密钥类型时，应指定在初始迁移创建的数据库。</span><span class="sxs-lookup"><span data-stu-id="3efeb-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="3efeb-199">请按照下列步骤更改 PK 类型：</span><span class="sxs-lookup"><span data-stu-id="3efeb-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="3efeb-200">如果数据库已创建的 PK 更改之前，运行`Drop-Database`(PMC) 或`dotnet ef database drop`(.NET Core CLI) 将其删除。</span><span class="sxs-lookup"><span data-stu-id="3efeb-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="3efeb-201">在确认删除数据库，删除以进行初始迁移`Remove-Migration`(PMC) 或`dotnet ef migrations remove`(.NET Core CLI)。</span><span class="sxs-lookup"><span data-stu-id="3efeb-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="3efeb-202">更新`ApplicationDbContext`类进行派生<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>。</span><span class="sxs-lookup"><span data-stu-id="3efeb-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span></span> <span data-ttu-id="3efeb-203">指定的新类型`TKey`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="3efeb-204">例如，若要使用`Guid`密钥类型：</span><span class="sxs-lookup"><span data-stu-id="3efeb-204">For example, to use a `Guid` key type:</span></span>

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

    <span data-ttu-id="3efeb-205">在前面的代码中，泛型类<xref:Microsoft.AspNetCore.Identity.IdentityUser%601>和<xref:Microsoft.AspNetCore.Identity.IdentityRole%601>必须指定要使用新的密钥类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="3efeb-206">在前面的代码中，泛型类<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601>和<xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601>必须指定要使用新的密钥类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="3efeb-207">`Startup.ConfigureServices` 必须更新为使用一般用户：</span><span class="sxs-lookup"><span data-stu-id="3efeb-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

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

4. <span data-ttu-id="3efeb-208">如果自定义`ApplicationUser`正在使用类中，更新要继承的类`IdentityUser`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="3efeb-209">例如：</span><span class="sxs-lookup"><span data-stu-id="3efeb-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="3efeb-210">更新`ApplicationDbContext`来引用自定义`ApplicationUser`类：</span><span class="sxs-lookup"><span data-stu-id="3efeb-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

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

    <span data-ttu-id="3efeb-211">注册自定义数据库上下文类添加中的标识服务时`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3efeb-211">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="3efeb-212">通过分析来推断主键的数据类型[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)对象。</span><span class="sxs-lookup"><span data-stu-id="3efeb-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="3efeb-213">在 ASP.NET Core 2.1 或更高版本，作为一个 Razor 类库提供标识。</span><span class="sxs-lookup"><span data-stu-id="3efeb-213">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="3efeb-214">有关详细信息，请参阅 <xref:security/authentication/scaffold-identity>。</span><span class="sxs-lookup"><span data-stu-id="3efeb-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="3efeb-215">因此，前面的代码需要调用<xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>。</span><span class="sxs-lookup"><span data-stu-id="3efeb-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="3efeb-216">如果标识基架用于标识文件添加到项目，删除对调用`AddDefaultUI`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-216">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="3efeb-217">通过分析来推断主键的数据类型[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)对象。</span><span class="sxs-lookup"><span data-stu-id="3efeb-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="3efeb-218"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>方法接受`TKey`，该值指示主键的数据类型的类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-218">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="3efeb-219">如果自定义`ApplicationRole`正在使用类中，更新要继承的类`IdentityRole<TKey>`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="3efeb-220">例如：</span><span class="sxs-lookup"><span data-stu-id="3efeb-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="3efeb-221">更新`ApplicationDbContext`来引用自定义`ApplicationRole`类。</span><span class="sxs-lookup"><span data-stu-id="3efeb-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="3efeb-222">例如，下面的类引用自定义`ApplicationUser`和自定义`ApplicationRole`:</span><span class="sxs-lookup"><span data-stu-id="3efeb-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="3efeb-223">注册自定义数据库上下文类添加中的标识服务时`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3efeb-223">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="3efeb-224">通过分析来推断主键的数据类型[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)对象。</span><span class="sxs-lookup"><span data-stu-id="3efeb-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="3efeb-225">在 ASP.NET Core 2.1 或更高版本，作为一个 Razor 类库提供标识。</span><span class="sxs-lookup"><span data-stu-id="3efeb-225">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="3efeb-226">有关详细信息，请参阅 <xref:security/authentication/scaffold-identity>。</span><span class="sxs-lookup"><span data-stu-id="3efeb-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="3efeb-227">因此，前面的代码需要调用<xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>。</span><span class="sxs-lookup"><span data-stu-id="3efeb-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="3efeb-228">如果标识基架用于标识文件添加到项目，删除对调用`AddDefaultUI`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-228">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="3efeb-229">注册自定义数据库上下文类添加中的标识服务时`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3efeb-229">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="3efeb-230">通过分析来推断主键的数据类型[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)对象。</span><span class="sxs-lookup"><span data-stu-id="3efeb-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="3efeb-231">注册自定义数据库上下文类添加中的标识服务时`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3efeb-231">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="3efeb-232"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*>方法接受`TKey`，该值指示主键的数据类型的类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-232">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="3efeb-233">添加导航属性</span><span class="sxs-lookup"><span data-stu-id="3efeb-233">Add navigation properties</span></span>

<span data-ttu-id="3efeb-234">更改关系的模型配置可能会更难于进行其他更改。</span><span class="sxs-lookup"><span data-stu-id="3efeb-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="3efeb-235">若要替换现有关系，而不是创建新的其他关系，必须格外小心。</span><span class="sxs-lookup"><span data-stu-id="3efeb-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="3efeb-236">具体而言，已更改的关系必须指定相同的外键 (FK) 属性作为现有的关系。</span><span class="sxs-lookup"><span data-stu-id="3efeb-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="3efeb-237">例如，之间的关系`Users`和`UserClaims`是，默认情况下，按如下所示指定：</span><span class="sxs-lookup"><span data-stu-id="3efeb-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

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

<span data-ttu-id="3efeb-238">此关系的 FK 指定为`UserClaim.UserId`属性。</span><span class="sxs-lookup"><span data-stu-id="3efeb-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="3efeb-239">`HasMany` 和`WithOne`调用不带参数，以创建不使用导航属性的关系。</span><span class="sxs-lookup"><span data-stu-id="3efeb-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="3efeb-240">导航属性添加到`ApplicationUser`，允许关联`UserClaims`若要从用户中引用：</span><span class="sxs-lookup"><span data-stu-id="3efeb-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="3efeb-241">`TKey`为`IdentityUserClaim<TKey>`是用户的 PK 为指定的类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-241">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="3efeb-242">在这种情况下，`TKey`是`string`因为正在使用默认值。</span><span class="sxs-lookup"><span data-stu-id="3efeb-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="3efeb-243">它具有**不**的 PK 类型`UserClaim`实体类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="3efeb-244">现在，已存在的导航属性，它必须在配置`OnModelCreating`:</span><span class="sxs-lookup"><span data-stu-id="3efeb-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

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

<span data-ttu-id="3efeb-245">请注意，完全按照以前，只能使用导航属性调用中指定配置关系`HasMany`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="3efeb-246">在 EF 模型中，不是数据库仅存在导航属性。</span><span class="sxs-lookup"><span data-stu-id="3efeb-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="3efeb-247">因为关系 fk 后未发生更改，这种类型的模型更改不需要更新的数据库。</span><span class="sxs-lookup"><span data-stu-id="3efeb-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="3efeb-248">可以通过添加迁移后进行更改选中此项。</span><span class="sxs-lookup"><span data-stu-id="3efeb-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="3efeb-249">`Up`和`Down`方法为空。</span><span class="sxs-lookup"><span data-stu-id="3efeb-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="3efeb-250">添加所有用户导航属性</span><span class="sxs-lookup"><span data-stu-id="3efeb-250">Add all User navigation properties</span></span>

<span data-ttu-id="3efeb-251">下面的示例作为指南使用上的一节中，在用户配置单向导航属性的所有关系：</span><span class="sxs-lookup"><span data-stu-id="3efeb-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

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

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="3efeb-252">添加用户和角色导航属性</span><span class="sxs-lookup"><span data-stu-id="3efeb-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="3efeb-253">下面的示例作为指南使用上的一节中，用户和角色上配置所有关系的导航的属性：</span><span class="sxs-lookup"><span data-stu-id="3efeb-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

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

<span data-ttu-id="3efeb-254">注意：</span><span class="sxs-lookup"><span data-stu-id="3efeb-254">Notes:</span></span>

* <span data-ttu-id="3efeb-255">此示例还包括`UserRole`联接实体，这将需要导航从用户到角色的多对多关系。</span><span class="sxs-lookup"><span data-stu-id="3efeb-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="3efeb-256">请记得要更改类型的导航属性以反映该`ApplicationXxx`类型现在正在使用而不是`IdentityXxx`类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-256">Remember to change the types of the navigation properties to reflect that `ApplicationXxx` types are now being used instead of `IdentityXxx` types.</span></span>
* <span data-ttu-id="3efeb-257">请记住使用`ApplicationXxx`中泛型`ApplicationContext`定义。</span><span class="sxs-lookup"><span data-stu-id="3efeb-257">Remember to use the `ApplicationXxx` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="3efeb-258">添加所有导航属性</span><span class="sxs-lookup"><span data-stu-id="3efeb-258">Add all navigation properties</span></span>

<span data-ttu-id="3efeb-259">下面的示例作为指南使用上的一节中，所有实体类型上配置所有关系的导航的属性：</span><span class="sxs-lookup"><span data-stu-id="3efeb-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

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

### <a name="use-composite-keys"></a><span data-ttu-id="3efeb-260">使用复合键</span><span class="sxs-lookup"><span data-stu-id="3efeb-260">Use composite keys</span></span>

<span data-ttu-id="3efeb-261">前面几节所示更改的标识模型中使用的密钥类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-261">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="3efeb-262">更改要使用的复合键的标识密钥模型不支持或建议。</span><span class="sxs-lookup"><span data-stu-id="3efeb-262">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="3efeb-263">使用复合密钥与标识涉及更改标识管理器代码与模型交互的方式。</span><span class="sxs-lookup"><span data-stu-id="3efeb-263">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="3efeb-264">此自定义已超出本文的讨论范围。</span><span class="sxs-lookup"><span data-stu-id="3efeb-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="3efeb-265">更改表/列名称和分面</span><span class="sxs-lookup"><span data-stu-id="3efeb-265">Change table/column names and facets</span></span>

<span data-ttu-id="3efeb-266">若要更改表和列的名称，请调用`base.OnModelCreating`。</span><span class="sxs-lookup"><span data-stu-id="3efeb-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="3efeb-267">然后，添加配置以重写任何默认值。</span><span class="sxs-lookup"><span data-stu-id="3efeb-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="3efeb-268">例如，若要更改所有标识表的名称：</span><span class="sxs-lookup"><span data-stu-id="3efeb-268">For example, to change the name of all the Identity tables:</span></span>

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

<span data-ttu-id="3efeb-269">这些示例使用默认标识类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-269">These examples use the default Identity types.</span></span> <span data-ttu-id="3efeb-270">如果使用的应用类型，如`ApplicationUser`，配置该类型而不是默认类型。</span><span class="sxs-lookup"><span data-stu-id="3efeb-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="3efeb-271">下面的示例更改某些列名称：</span><span class="sxs-lookup"><span data-stu-id="3efeb-271">The following example changes some column names:</span></span>

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

<span data-ttu-id="3efeb-272">某些类型的数据库列可以配置某些*方面*(例如，最大值`string`允许的长度)。</span><span class="sxs-lookup"><span data-stu-id="3efeb-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="3efeb-273">下面的示例设置多个列的最大长度`string`模型中的属性：</span><span class="sxs-lookup"><span data-stu-id="3efeb-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

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

### <a name="map-to-a-different-schema"></a><span data-ttu-id="3efeb-274">将映射到不同的架构</span><span class="sxs-lookup"><span data-stu-id="3efeb-274">Map to a different schema</span></span>

<span data-ttu-id="3efeb-275">跨数据库提供程序，架构的行为可能有所不同。</span><span class="sxs-lookup"><span data-stu-id="3efeb-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="3efeb-276">对于 SQL Server，默认值是创建中的所有表*dbo*架构。</span><span class="sxs-lookup"><span data-stu-id="3efeb-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="3efeb-277">可以在不同的架构中创建的表。</span><span class="sxs-lookup"><span data-stu-id="3efeb-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="3efeb-278">例如：</span><span class="sxs-lookup"><span data-stu-id="3efeb-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="3efeb-279">延迟加载</span><span class="sxs-lookup"><span data-stu-id="3efeb-279">Lazy loading</span></span>

<span data-ttu-id="3efeb-280">在本部分中，添加了支持的身份标识模型中的延迟加载代理。</span><span class="sxs-lookup"><span data-stu-id="3efeb-280">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="3efeb-281">延迟加载很有用，因为它允许使用而无需首先确保它们正在加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="3efeb-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="3efeb-282">实体类型可适用于采用多种方式的延迟加载中所述[EF Core 文档](/ef/core/querying/related-data#lazy-loading)。</span><span class="sxs-lookup"><span data-stu-id="3efeb-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="3efeb-283">为简单起见，使用该软件需要延迟加载代理：</span><span class="sxs-lookup"><span data-stu-id="3efeb-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="3efeb-284">安装[-Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)包。</span><span class="sxs-lookup"><span data-stu-id="3efeb-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="3efeb-285">调用<xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*>内[AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)。</span><span class="sxs-lookup"><span data-stu-id="3efeb-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="3efeb-286">公共实体类型与`public virtual`导航属性。</span><span class="sxs-lookup"><span data-stu-id="3efeb-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="3efeb-287">下面的示例演示如何调用`UseLazyLoadingProxies`在`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="3efeb-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="3efeb-288">请参阅前面的示例将导航属性添加到实体类型有关的指南。</span><span class="sxs-lookup"><span data-stu-id="3efeb-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3efeb-289">其他资源</span><span class="sxs-lookup"><span data-stu-id="3efeb-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
