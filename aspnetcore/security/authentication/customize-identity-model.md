---
title: ASP.NET Core 中的标识模型自定义
author: ajcvickers
description: 本文介绍如何为 ASP.NET Core 标识自定义的基础的实体框架核心数据模型。
ms.author: avickers
ms.date: 07/01/2019
uid: security/authentication/customize_identity_model
ms.openlocfilehash: f549fdff4a416b5fadcb2b1078b051bbab8e402e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651456"
---
# <a name="identity-model-customization-in-aspnet-core"></a><span data-ttu-id="e4e12-103">ASP.NET Core 中的标识模型自定义</span><span class="sxs-lookup"><span data-stu-id="e4e12-103">Identity model customization in ASP.NET Core</span></span>

<span data-ttu-id="e4e12-104">作者： [Arthur Vickers](https://github.com/ajcvickers)</span><span class="sxs-lookup"><span data-stu-id="e4e12-104">By [Arthur Vickers](https://github.com/ajcvickers)</span></span>

<span data-ttu-id="e4e12-105">ASP.NET Core 标识提供了一个框架，用于管理和存储 ASP.NET Core 应用中的用户帐户。</span><span class="sxs-lookup"><span data-stu-id="e4e12-105">ASP.NET Core Identity provides a framework for managing and storing user accounts in ASP.NET Core apps.</span></span> <span data-ttu-id="e4e12-106">选择**单个用户帐户**作为身份验证机制时，会将标识添加到你的项目中。</span><span class="sxs-lookup"><span data-stu-id="e4e12-106">Identity is added to your project when **Individual User Accounts** is selected as the authentication mechanism.</span></span> <span data-ttu-id="e4e12-107">默认情况下，标识使用实体框架（EF）核心数据模型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-107">By default, Identity makes use of an Entity Framework (EF) Core data model.</span></span> <span data-ttu-id="e4e12-108">本文介绍如何自定义标识模型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-108">This article describes how to customize the Identity model.</span></span>

## <a name="identity-and-ef-core-migrations"></a><span data-ttu-id="e4e12-109">标识和 EF Core 迁移</span><span class="sxs-lookup"><span data-stu-id="e4e12-109">Identity and EF Core Migrations</span></span>

<span data-ttu-id="e4e12-110">在检查模型之前，了解身份如何与[EF Core 迁移](/ef/core/managing-schemas/migrations/)来创建和更新数据库，这一点很有用。</span><span class="sxs-lookup"><span data-stu-id="e4e12-110">Before examining the model, it's useful to understand how Identity works with [EF Core Migrations](/ef/core/managing-schemas/migrations/) to create and update a database.</span></span> <span data-ttu-id="e4e12-111">在顶级，此过程如下：</span><span class="sxs-lookup"><span data-stu-id="e4e12-111">At the top level, the process is:</span></span>

1. <span data-ttu-id="e4e12-112">[在代码中](/ef/core/modeling/)定义或更新数据模型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-112">Define or update a [data model in code](/ef/core/modeling/).</span></span>
1. <span data-ttu-id="e4e12-113">添加迁移，将此模型转换为可应用于数据库的更改。</span><span class="sxs-lookup"><span data-stu-id="e4e12-113">Add a Migration to translate this model into changes that can be applied to the database.</span></span>
1. <span data-ttu-id="e4e12-114">检查迁移是否正确表示你的意图。</span><span class="sxs-lookup"><span data-stu-id="e4e12-114">Check that the Migration correctly represents your intentions.</span></span>
1. <span data-ttu-id="e4e12-115">应用迁移以更新数据库，使其与模型保持同步。</span><span class="sxs-lookup"><span data-stu-id="e4e12-115">Apply the Migration to update the database to be in sync with the model.</span></span>
1. <span data-ttu-id="e4e12-116">重复步骤1到4，进一步优化模型并使数据库保持同步。</span><span class="sxs-lookup"><span data-stu-id="e4e12-116">Repeat steps 1 through 4 to further refine the model and keep the database in sync.</span></span>

<span data-ttu-id="e4e12-117">使用以下方法之一来添加和应用迁移：</span><span class="sxs-lookup"><span data-stu-id="e4e12-117">Use one of the following approaches to add and apply Migrations:</span></span>

* <span data-ttu-id="e4e12-118">如果使用 Visual Studio，则为**包管理器控制台**（PMC）窗口。</span><span class="sxs-lookup"><span data-stu-id="e4e12-118">The **Package Manager Console** (PMC) window if using Visual Studio.</span></span> <span data-ttu-id="e4e12-119">有关详细信息，请参阅[EF CORE PMC 工具](/ef/core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="e4e12-119">For more information, see [EF Core PMC tools](/ef/core/miscellaneous/cli/powershell).</span></span>
* <span data-ttu-id="e4e12-120">使用命令行时的 .NET Core CLI。</span><span class="sxs-lookup"><span data-stu-id="e4e12-120">The .NET Core CLI if using the command line.</span></span> <span data-ttu-id="e4e12-121">有关详细信息，请参阅[EF Core .net 命令行工具](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="e4e12-121">For more information, see [EF Core .NET command line tools](/ef/core/miscellaneous/cli/dotnet).</span></span>
* <span data-ttu-id="e4e12-122">当应用运行时，单击 "错误" 页上的 "**应用迁移**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="e4e12-122">Clicking the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="e4e12-123">ASP.NET Core 具有一个开发时错误页面处理程序。</span><span class="sxs-lookup"><span data-stu-id="e4e12-123">ASP.NET Core has a development-time error page handler.</span></span> <span data-ttu-id="e4e12-124">在运行应用程序时，处理程序可以应用迁移。</span><span class="sxs-lookup"><span data-stu-id="e4e12-124">The handler can apply migrations when the app is run.</span></span> <span data-ttu-id="e4e12-125">生产应用通常从迁移生成 SQL 脚本，并将数据库更改作为受控应用和数据库部署的一部分进行部署。</span><span class="sxs-lookup"><span data-stu-id="e4e12-125">Production apps typically generate SQL scripts from the migrations and deploy database changes as part of a controlled app and database deployment.</span></span>

<span data-ttu-id="e4e12-126">当创建使用标识的新应用时，上面的步骤1和2已经完成。</span><span class="sxs-lookup"><span data-stu-id="e4e12-126">When a new app using Identity is created, steps 1 and 2 above have already been completed.</span></span> <span data-ttu-id="e4e12-127">也就是说，初始数据模型已存在，初始迁移已添加到该项目中。</span><span class="sxs-lookup"><span data-stu-id="e4e12-127">That is, the initial data model already exists, and the initial migration has been added to the project.</span></span> <span data-ttu-id="e4e12-128">初始迁移仍需应用于数据库。</span><span class="sxs-lookup"><span data-stu-id="e4e12-128">The initial migration still needs to be applied to the database.</span></span> <span data-ttu-id="e4e12-129">可以通过以下方法之一来应用初始迁移：</span><span class="sxs-lookup"><span data-stu-id="e4e12-129">The initial migration can be applied via one of the following approaches:</span></span>

* <span data-ttu-id="e4e12-130">在 PMC 中运行 `Update-Database`。</span><span class="sxs-lookup"><span data-stu-id="e4e12-130">Run `Update-Database` in PMC.</span></span>
* <span data-ttu-id="e4e12-131">在命令行界面中运行 `dotnet ef database update`。</span><span class="sxs-lookup"><span data-stu-id="e4e12-131">Run `dotnet ef database update` in a command shell.</span></span>
* <span data-ttu-id="e4e12-132">运行应用时，单击 "错误" 页上的 "**应用迁移**" 按钮。</span><span class="sxs-lookup"><span data-stu-id="e4e12-132">Click the **Apply Migrations** button on the error page when the app is run.</span></span>

<span data-ttu-id="e4e12-133">对模型进行更改时重复前面的步骤。</span><span class="sxs-lookup"><span data-stu-id="e4e12-133">Repeat the preceding steps as changes are made to the model.</span></span>

## <a name="the-identity-model"></a><span data-ttu-id="e4e12-134">标识模型</span><span class="sxs-lookup"><span data-stu-id="e4e12-134">The Identity model</span></span>

### <a name="entity-types"></a><span data-ttu-id="e4e12-135">实体类型</span><span class="sxs-lookup"><span data-stu-id="e4e12-135">Entity types</span></span>

<span data-ttu-id="e4e12-136">标识模型包含以下实体类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-136">The Identity model consists of the following entity types.</span></span>

|<span data-ttu-id="e4e12-137">实体类型</span><span class="sxs-lookup"><span data-stu-id="e4e12-137">Entity type</span></span>|<span data-ttu-id="e4e12-138">说明</span><span class="sxs-lookup"><span data-stu-id="e4e12-138">Description</span></span>                                                  |
|-----------|-------------------------------------------------------------|
|`User`     |<span data-ttu-id="e4e12-139">表示用户。</span><span class="sxs-lookup"><span data-stu-id="e4e12-139">Represents the user.</span></span>                                         |
|`Role`     |<span data-ttu-id="e4e12-140">表示角色。</span><span class="sxs-lookup"><span data-stu-id="e4e12-140">Represents a role.</span></span>                                           |
|`UserClaim`|<span data-ttu-id="e4e12-141">表示用户拥有的声明。</span><span class="sxs-lookup"><span data-stu-id="e4e12-141">Represents a claim that a user possesses.</span></span>                    |
|`UserToken`|<span data-ttu-id="e4e12-142">表示用户的身份验证令牌。</span><span class="sxs-lookup"><span data-stu-id="e4e12-142">Represents an authentication token for a user.</span></span>               |
|`UserLogin`|<span data-ttu-id="e4e12-143">将用户与登录名相关联。</span><span class="sxs-lookup"><span data-stu-id="e4e12-143">Associates a user with a login.</span></span>                              |
|`RoleClaim`|<span data-ttu-id="e4e12-144">表示向角色内的所有用户授予的声明。</span><span class="sxs-lookup"><span data-stu-id="e4e12-144">Represents a claim that's granted to all users within a role.</span></span>|
|`UserRole` |<span data-ttu-id="e4e12-145">关联用户和角色的联接实体。</span><span class="sxs-lookup"><span data-stu-id="e4e12-145">A join entity that associates users and roles.</span></span>               |

### <a name="entity-type-relationships"></a><span data-ttu-id="e4e12-146">实体类型关系</span><span class="sxs-lookup"><span data-stu-id="e4e12-146">Entity type relationships</span></span>

<span data-ttu-id="e4e12-147">[实体类型](#entity-types)通过以下方式彼此相关：</span><span class="sxs-lookup"><span data-stu-id="e4e12-147">The [entity types](#entity-types) are related to each other in the following ways:</span></span>

* <span data-ttu-id="e4e12-148">每个 `User` 可以有多个 `UserClaims`。</span><span class="sxs-lookup"><span data-stu-id="e4e12-148">Each `User` can have many `UserClaims`.</span></span>
* <span data-ttu-id="e4e12-149">每个 `User` 可以有多个 `UserLogins`。</span><span class="sxs-lookup"><span data-stu-id="e4e12-149">Each `User` can have many `UserLogins`.</span></span>
* <span data-ttu-id="e4e12-150">每个 `User` 可以有多个 `UserTokens`。</span><span class="sxs-lookup"><span data-stu-id="e4e12-150">Each `User` can have many `UserTokens`.</span></span>
* <span data-ttu-id="e4e12-151">每个 `Role` 可以有多个关联的 `RoleClaims`。</span><span class="sxs-lookup"><span data-stu-id="e4e12-151">Each `Role` can have many associated `RoleClaims`.</span></span>
* <span data-ttu-id="e4e12-152">每个 `User` 可以有多个关联的 `Roles`，每个 `Role` 可以与多个 `Users`关联。</span><span class="sxs-lookup"><span data-stu-id="e4e12-152">Each `User` can have many associated `Roles`, and each `Role` can be associated with many `Users`.</span></span> <span data-ttu-id="e4e12-153">这是一个多对多关系，需要数据库中的联接表。</span><span class="sxs-lookup"><span data-stu-id="e4e12-153">This is a many-to-many relationship that requires a join table in the database.</span></span> <span data-ttu-id="e4e12-154">联接表由 `UserRole` 实体表示。</span><span class="sxs-lookup"><span data-stu-id="e4e12-154">The join table is represented by the `UserRole` entity.</span></span>

### <a name="default-model-configuration"></a><span data-ttu-id="e4e12-155">默认模型配置</span><span class="sxs-lookup"><span data-stu-id="e4e12-155">Default model configuration</span></span>

<span data-ttu-id="e4e12-156">标识定义了许多继承自[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)的*上下文类*来配置和使用模型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-156">Identity defines many *context classes* that inherit from [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) to configure and use the model.</span></span> <span data-ttu-id="e4e12-157">此配置是使用上下文类的[OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating)方法中的[EF CORE Code First 熟知 API](/ef/core/modeling/)完成的。</span><span class="sxs-lookup"><span data-stu-id="e4e12-157">This configuration is done using the [EF Core Code First Fluent API](/ef/core/modeling/) in the [OnModelCreating](/dotnet/api/microsoft.entityframeworkcore.dbcontext.onmodelcreating) method of the context class.</span></span> <span data-ttu-id="e4e12-158">默认配置为：</span><span class="sxs-lookup"><span data-stu-id="e4e12-158">The default configuration is:</span></span>

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

### <a name="model-generic-types"></a><span data-ttu-id="e4e12-159">模型泛型类型</span><span class="sxs-lookup"><span data-stu-id="e4e12-159">Model generic types</span></span>

<span data-ttu-id="e4e12-160">标识为上面列出的每个实体类型定义默认的[公共语言运行时](/dotnet/standard/glossary#clr)（CLR）类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-160">Identity defines default [Common Language Runtime](/dotnet/standard/glossary#clr) (CLR) types for each of the entity types listed above.</span></span> <span data-ttu-id="e4e12-161">这些类型都带有*标识*：</span><span class="sxs-lookup"><span data-stu-id="e4e12-161">These types are all prefixed with *Identity*:</span></span>

* `IdentityUser`
* `IdentityRole`
* `IdentityUserClaim`
* `IdentityUserToken`
* `IdentityUserLogin`
* `IdentityRoleClaim`
* `IdentityUserRole`

<span data-ttu-id="e4e12-162">可以将类型用作应用自己的类型的基类，而不是直接使用这些类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-162">Rather than using these types directly, the types can be used as base classes for the app's own types.</span></span> <span data-ttu-id="e4e12-163">标识定义的 `DbContext` 类是泛型类，因此，不同的 CLR 类型可用于模型中的一个或多个实体类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-163">The `DbContext` classes defined by Identity are generic, such that different CLR types can be used for one or more of the entity types in the model.</span></span> <span data-ttu-id="e4e12-164">这些泛型类型还允许更改 `User` 主键（PK）数据类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-164">These generic types also allow the `User` primary key (PK) data type to be changed.</span></span>

<span data-ttu-id="e4e12-165">将标识与支持角色一起使用时，应使用 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> 类。</span><span class="sxs-lookup"><span data-stu-id="e4e12-165">When using Identity with support for roles, an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext> class should be used.</span></span> <span data-ttu-id="e4e12-166">例如：</span><span class="sxs-lookup"><span data-stu-id="e4e12-166">For example:</span></span>

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

<span data-ttu-id="e4e12-167">也可以使用不带角色的标识（仅限声明），在这种情况下，应使用 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> 类：</span><span class="sxs-lookup"><span data-stu-id="e4e12-167">It's also possible to use Identity without roles (only claims), in which case an <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUserContext%601> class should be used:</span></span>

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

## <a name="customize-the-model"></a><span data-ttu-id="e4e12-168">自定义模型</span><span class="sxs-lookup"><span data-stu-id="e4e12-168">Customize the model</span></span>

<span data-ttu-id="e4e12-169">模型自定义的起点是派生自适当的上下文类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-169">The starting point for model customization is to derive from the appropriate context type.</span></span> <span data-ttu-id="e4e12-170">请参阅[模型泛型类型](#model-generic-types)部分。</span><span class="sxs-lookup"><span data-stu-id="e4e12-170">See the [Model generic types](#model-generic-types) section.</span></span> <span data-ttu-id="e4e12-171">此上下文类型通常称为 `ApplicationDbContext`，由 ASP.NET Core 模板创建。</span><span class="sxs-lookup"><span data-stu-id="e4e12-171">This context type is customarily called `ApplicationDbContext` and is created by the ASP.NET Core templates.</span></span>

<span data-ttu-id="e4e12-172">上下文用于通过两种方式配置模型：</span><span class="sxs-lookup"><span data-stu-id="e4e12-172">The context is used to configure the model in two ways:</span></span>

* <span data-ttu-id="e4e12-173">为泛型类型参数提供实体和键类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-173">Supplying entity and key types for the generic type parameters.</span></span>
* <span data-ttu-id="e4e12-174">重写 `OnModelCreating` 修改这些类型的映射。</span><span class="sxs-lookup"><span data-stu-id="e4e12-174">Overriding `OnModelCreating` to modify the mapping of these types.</span></span>

<span data-ttu-id="e4e12-175">重写 `OnModelCreating`时，应首先调用 `base.OnModelCreating`;接下来应调用重写配置。</span><span class="sxs-lookup"><span data-stu-id="e4e12-175">When overriding `OnModelCreating`, `base.OnModelCreating` should be called first; the overriding configuration should be called next.</span></span> <span data-ttu-id="e4e12-176">EF Core 通常具有配置的最后一个 wins 策略。</span><span class="sxs-lookup"><span data-stu-id="e4e12-176">EF Core generally has a last-one-wins policy for configuration.</span></span> <span data-ttu-id="e4e12-177">例如，如果先使用一个表名称调用实体类型的 `ToTable` 方法，然后使用不同的表名称再次调用该方法，则使用第二个调用中的表名。</span><span class="sxs-lookup"><span data-stu-id="e4e12-177">For example, if the `ToTable` method for an entity type is called first with one table name and then again later with a different table name, the table name in the second call is used.</span></span>

### <a name="custom-user-data"></a><span data-ttu-id="e4e12-178">自定义用户数据</span><span class="sxs-lookup"><span data-stu-id="e4e12-178">Custom user data</span></span>

<!--
set projNam=WebApp1
dotnet new webapp -o %projNam%
cd %projNam%
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design 
dotnet aspnet-codegenerator identity  -dc ApplicationDbContext --useDefaultUI 
dotnet ef migrations add CreateIdentitySchema
dotnet ef database update
 -->

<span data-ttu-id="e4e12-179">[自定义用户数据](xref:security/authentication/add-user-data)是通过从 `IdentityUser`继承来支持的。</span><span class="sxs-lookup"><span data-stu-id="e4e12-179">[Custom user data](xref:security/authentication/add-user-data) is supported by inheriting from `IdentityUser`.</span></span> <span data-ttu-id="e4e12-180">建议将此类型命名 `ApplicationUser`：</span><span class="sxs-lookup"><span data-stu-id="e4e12-180">It's customary to name this type `ApplicationUser`:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public string CustomTag { get; set; }
}
```

<span data-ttu-id="e4e12-181">使用 `ApplicationUser` 类型作为上下文的泛型参数：</span><span class="sxs-lookup"><span data-stu-id="e4e12-181">Use the `ApplicationUser` type as a generic argument for the context:</span></span>

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

<span data-ttu-id="e4e12-182">不需要重写 `ApplicationDbContext` 类中的 `OnModelCreating`。</span><span class="sxs-lookup"><span data-stu-id="e4e12-182">There's no need to override `OnModelCreating` in the `ApplicationDbContext` class.</span></span> <span data-ttu-id="e4e12-183">EF Core 按约定映射 `CustomTag` 属性。</span><span class="sxs-lookup"><span data-stu-id="e4e12-183">EF Core maps the `CustomTag` property by convention.</span></span> <span data-ttu-id="e4e12-184">但是，需要更新数据库以创建新的 `CustomTag` 列。</span><span class="sxs-lookup"><span data-stu-id="e4e12-184">However, the database needs to be updated to create a new `CustomTag` column.</span></span> <span data-ttu-id="e4e12-185">若要创建该列，请添加迁移，然后根据[标识和 EF Core 迁移](#identity-and-ef-core-migrations)中所述更新数据库。</span><span class="sxs-lookup"><span data-stu-id="e4e12-185">To create the column, add a migration, and then update the database as described in [Identity and EF Core Migrations](#identity-and-ef-core-migrations).</span></span>

<span data-ttu-id="e4e12-186">更新*Pages/Shared/_LoginPartial* ，并将 `IdentityUser` 替换为 `ApplicationUser`：</span><span class="sxs-lookup"><span data-stu-id="e4e12-186">Update *Pages/Shared/_LoginPartial.cshtml* and replace `IdentityUser` with `ApplicationUser`:</span></span>

```cshtml
@using Microsoft.AspNetCore.Identity
@using WebApp1.Areas.Identity.Data
@inject SignInManager<ApplicationUser> SignInManager
@inject UserManager<ApplicationUser> UserManager
```

<span data-ttu-id="e4e12-187">更新*Areas/Identity/IdentityHostingStartup*或 `Startup.ConfigureServices`，并将 `IdentityUser` 替换为 `ApplicationUser`。</span><span class="sxs-lookup"><span data-stu-id="e4e12-187">Update *Areas/Identity/IdentityHostingStartup.cs*  or `Startup.ConfigureServices` and replace `IdentityUser` with `ApplicationUser`.</span></span>

```csharp
services.AddDefaultIdentity<ApplicationUser>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultUI();
```

<span data-ttu-id="e4e12-188">在 ASP.NET Core 2.1 或更高版本中，标识作为 Razor 类库提供。</span><span class="sxs-lookup"><span data-stu-id="e4e12-188">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="e4e12-189">有关详细信息，请参阅 <xref:security/authentication/scaffold-identity>。</span><span class="sxs-lookup"><span data-stu-id="e4e12-189">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="e4e12-190">因此，前面的代码需要调用 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>。</span><span class="sxs-lookup"><span data-stu-id="e4e12-190">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="e4e12-191">如果使用标识 scaffolder 将标识文件添加到项目中，请删除对 `AddDefaultUI`的调用。</span><span class="sxs-lookup"><span data-stu-id="e4e12-191">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span> <span data-ttu-id="e4e12-192">有关详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="e4e12-192">For more information, see:</span></span>

* [<span data-ttu-id="e4e12-193">基架标识</span><span class="sxs-lookup"><span data-stu-id="e4e12-193">Scaffold Identity</span></span>](xref:security/authentication/scaffold-identity)
* [<span data-ttu-id="e4e12-194">向标识添加、下载和删除自定义用户数据</span><span class="sxs-lookup"><span data-stu-id="e4e12-194">Add, download, and delete custom user data to Identity</span></span>](xref:security/authentication/add-user-data)

### <a name="change-the-primary-key-type"></a><span data-ttu-id="e4e12-195">更改主键类型</span><span class="sxs-lookup"><span data-stu-id="e4e12-195">Change the primary key type</span></span>

<span data-ttu-id="e4e12-196">在创建数据库后，对 PK 列的数据类型的更改在许多数据库系统上都有问题。</span><span class="sxs-lookup"><span data-stu-id="e4e12-196">A change to the PK column's data type after the database has been created is problematic on many database systems.</span></span> <span data-ttu-id="e4e12-197">更改 PK 通常涉及删除并重新创建表。</span><span class="sxs-lookup"><span data-stu-id="e4e12-197">Changing the PK typically involves dropping and re-creating the table.</span></span> <span data-ttu-id="e4e12-198">因此，在创建数据库时，应在初始迁移中指定密钥类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-198">Therefore, key types should be specified in the initial migration when the database is created.</span></span>

<span data-ttu-id="e4e12-199">按照以下步骤更改 PK 类型：</span><span class="sxs-lookup"><span data-stu-id="e4e12-199">Follow these steps to change the PK type:</span></span>

1. <span data-ttu-id="e4e12-200">如果数据库是在 PK 更改之前创建的，请运行 `Drop-Database` （PMC）或 `dotnet ef database drop` （.NET Core CLI）以删除它。</span><span class="sxs-lookup"><span data-stu-id="e4e12-200">If the database was created before the PK change, run `Drop-Database` (PMC) or `dotnet ef database drop` (.NET Core CLI) to delete it.</span></span>
2. <span data-ttu-id="e4e12-201">确认删除数据库后，删除带有 `Remove-Migration` （PMC）或 `dotnet ef migrations remove` （.NET Core CLI）的初始迁移。</span><span class="sxs-lookup"><span data-stu-id="e4e12-201">After confirming deletion of the database, remove the initial migration with `Remove-Migration` (PMC) or `dotnet ef migrations remove` (.NET Core CLI).</span></span>
3. <span data-ttu-id="e4e12-202">更新 `ApplicationDbContext` 类，使其从 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>派生。</span><span class="sxs-lookup"><span data-stu-id="e4e12-202">Update the `ApplicationDbContext` class to derive from <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityDbContext%603>.</span></span> <span data-ttu-id="e4e12-203">为 `TKey`指定新的密钥类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-203">Specify the new key type for `TKey`.</span></span> <span data-ttu-id="e4e12-204">例如，若要使用 `Guid` 密钥类型：</span><span class="sxs-lookup"><span data-stu-id="e4e12-204">For example, to use a `Guid` key type:</span></span>

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

    <span data-ttu-id="e4e12-205">在前面的代码中，必须指定泛型类 <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> 和 <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> 才能使用新的密钥类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-205">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    <span data-ttu-id="e4e12-206">在前面的代码中，必须指定泛型类 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> 和 <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> 才能使用新的密钥类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-206">In the preceding code, the generic classes <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityUser%601> and <xref:Microsoft.AspNetCore.Identity.EntityFrameworkCore.IdentityRole%601> must be specified to use the new key type.</span></span>

    ::: moniker-end

    <span data-ttu-id="e4e12-207">若要使用一般用户，必须更新 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="e4e12-207">`Startup.ConfigureServices` must be updated to use the generic user:</span></span>

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

4. <span data-ttu-id="e4e12-208">如果正在使用自定义 `ApplicationUser` 类，请将类更新为从 `IdentityUser`继承。</span><span class="sxs-lookup"><span data-stu-id="e4e12-208">If a custom `ApplicationUser` class is being used, update the class to inherit from `IdentityUser`.</span></span> <span data-ttu-id="e4e12-209">例如：</span><span class="sxs-lookup"><span data-stu-id="e4e12-209">For example:</span></span>

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Models/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    ::: moniker range=">= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationUser.cs?name=snippet_ApplicationUser&highlight=4)]

    ::: moniker-end

    <span data-ttu-id="e4e12-210">更新 `ApplicationDbContext` 以引用自定义 `ApplicationUser` 类：</span><span class="sxs-lookup"><span data-stu-id="e4e12-210">Update `ApplicationDbContext` to reference the custom `ApplicationUser` class:</span></span>

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

    <span data-ttu-id="e4e12-211">在 `Startup.ConfigureServices`中添加标识服务时注册自定义数据库上下文类：</span><span class="sxs-lookup"><span data-stu-id="e4e12-211">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    ```csharp
    services.AddDefaultIdentity<ApplicationUser>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultUI()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="e4e12-212">通过分析[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)对象来推断主键的数据类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-212">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="e4e12-213">在 ASP.NET Core 2.1 或更高版本中，标识作为 Razor 类库提供。</span><span class="sxs-lookup"><span data-stu-id="e4e12-213">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="e4e12-214">有关详细信息，请参阅 <xref:security/authentication/scaffold-identity>。</span><span class="sxs-lookup"><span data-stu-id="e4e12-214">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="e4e12-215">因此，前面的代码需要调用 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>。</span><span class="sxs-lookup"><span data-stu-id="e4e12-215">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="e4e12-216">如果使用标识 scaffolder 将标识文件添加到项目中，请删除对 `AddDefaultUI`的调用。</span><span class="sxs-lookup"><span data-stu-id="e4e12-216">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="e4e12-217">通过分析[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)对象来推断主键的数据类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-217">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    ```csharp
    services.AddIdentity<ApplicationUser, IdentityRole>()
            .AddEntityFrameworkStores<ApplicationDbContext, Guid>()
            .AddDefaultTokenProviders();
    ```

    <span data-ttu-id="e4e12-218"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> 方法接受指示主键的数据类型的 `TKey` 类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-218">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

5. <span data-ttu-id="e4e12-219">如果正在使用自定义 `ApplicationRole` 类，请将类更新为从 `IdentityRole<TKey>`继承。</span><span class="sxs-lookup"><span data-stu-id="e4e12-219">If a custom `ApplicationRole` class is being used, update the class to inherit from `IdentityRole<TKey>`.</span></span> <span data-ttu-id="e4e12-220">例如：</span><span class="sxs-lookup"><span data-stu-id="e4e12-220">For example:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationRole.cs?name=snippet_ApplicationRole&highlight=4)]

    <span data-ttu-id="e4e12-221">更新 `ApplicationDbContext` 以引用自定义 `ApplicationRole` 类。</span><span class="sxs-lookup"><span data-stu-id="e4e12-221">Update `ApplicationDbContext` to reference the custom `ApplicationRole` class.</span></span> <span data-ttu-id="e4e12-222">例如，下面的类引用自定义 `ApplicationUser` 和自定义 `ApplicationRole`：</span><span class="sxs-lookup"><span data-stu-id="e4e12-222">For example, the following class references a custom `ApplicationUser` and a custom `ApplicationRole`:</span></span>

    ::: moniker range=">= aspnetcore-2.1"

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="e4e12-223">在 `Startup.ConfigureServices`中添加标识服务时注册自定义数据库上下文类：</span><span class="sxs-lookup"><span data-stu-id="e4e12-223">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.1/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=13-16)]

    <span data-ttu-id="e4e12-224">通过分析[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)对象来推断主键的数据类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-224">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    <span data-ttu-id="e4e12-225">在 ASP.NET Core 2.1 或更高版本中，标识作为 Razor 类库提供。</span><span class="sxs-lookup"><span data-stu-id="e4e12-225">In ASP.NET Core 2.1 or later, Identity is provided as a Razor Class Library.</span></span> <span data-ttu-id="e4e12-226">有关详细信息，请参阅 <xref:security/authentication/scaffold-identity>。</span><span class="sxs-lookup"><span data-stu-id="e4e12-226">For more information, see <xref:security/authentication/scaffold-identity>.</span></span> <span data-ttu-id="e4e12-227">因此，前面的代码需要调用 <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>。</span><span class="sxs-lookup"><span data-stu-id="e4e12-227">Consequently, the preceding code requires a call to <xref:Microsoft.AspNetCore.Identity.IdentityBuilderUIExtensions.AddDefaultUI*>.</span></span> <span data-ttu-id="e4e12-228">如果使用标识 scaffolder 将标识文件添加到项目中，请删除对 `AddDefaultUI`的调用。</span><span class="sxs-lookup"><span data-stu-id="e4e12-228">If the Identity scaffolder was used to add Identity files to the project, remove the call to `AddDefaultUI`.</span></span>

    ::: moniker-end

    ::: moniker range="= aspnetcore-2.0"

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="e4e12-229">在 `Startup.ConfigureServices`中添加标识服务时注册自定义数据库上下文类：</span><span class="sxs-lookup"><span data-stu-id="e4e12-229">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/2.0/RazorPagesSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="e4e12-230">通过分析[DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)对象来推断主键的数据类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-230">The primary key's data type is inferred by analyzing the [DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext) object.</span></span>

    ::: moniker-end

    ::: moniker range="<= aspnetcore-1.1"

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Data/ApplicationDbContext.cs?name=snippet_ApplicationDbContext&highlight=5-6)]

    <span data-ttu-id="e4e12-231">在 `Startup.ConfigureServices`中添加标识服务时注册自定义数据库上下文类：</span><span class="sxs-lookup"><span data-stu-id="e4e12-231">Register the custom database context class when adding the Identity service in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](customize-identity-model/samples/1.1/MvcSampleApp/Startup.cs?name=snippet_ConfigureServices&highlight=7-9)]

    <span data-ttu-id="e4e12-232"><xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> 方法接受指示主键的数据类型的 `TKey` 类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-232">The <xref:Microsoft.Extensions.DependencyInjection.IdentityEntityFrameworkBuilderExtensions.AddEntityFrameworkStores*> method accepts a `TKey` type indicating the primary key's data type.</span></span>

    ::: moniker-end

### <a name="add-navigation-properties"></a><span data-ttu-id="e4e12-233">添加导航属性</span><span class="sxs-lookup"><span data-stu-id="e4e12-233">Add navigation properties</span></span>

<span data-ttu-id="e4e12-234">更改关系的模型配置可能比进行其他更改更难。</span><span class="sxs-lookup"><span data-stu-id="e4e12-234">Changing the model configuration for relationships can be more difficult than making other changes.</span></span> <span data-ttu-id="e4e12-235">必须小心替换现有关系，而不是创建新的其他关系。</span><span class="sxs-lookup"><span data-stu-id="e4e12-235">Care must be taken to replace the existing relationships rather than create new, additional relationships.</span></span> <span data-ttu-id="e4e12-236">特别是，更改的关系必须指定与现有关系相同的外键（FK）属性。</span><span class="sxs-lookup"><span data-stu-id="e4e12-236">In particular, the changed relationship must specify the same foreign key (FK) property as the existing relationship.</span></span> <span data-ttu-id="e4e12-237">例如，默认情况下，`Users` 和 `UserClaims` 之间的关系按如下方式指定：</span><span class="sxs-lookup"><span data-stu-id="e4e12-237">For example, the relationship between `Users` and `UserClaims` is, by default, specified as follows:</span></span>

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

<span data-ttu-id="e4e12-238">此关系的 FK 作为 `UserClaim.UserId` 属性指定。</span><span class="sxs-lookup"><span data-stu-id="e4e12-238">The FK for this relationship is specified as the `UserClaim.UserId` property.</span></span> <span data-ttu-id="e4e12-239">无需参数即可调用 `HasMany` 和 `WithOne` 来创建不带导航属性的关系。</span><span class="sxs-lookup"><span data-stu-id="e4e12-239">`HasMany` and `WithOne` are called without arguments to create the relationship without navigation properties.</span></span>

<span data-ttu-id="e4e12-240">向 `ApplicationUser` 添加一个导航属性，该属性允许从用户引用关联的 `UserClaims`：</span><span class="sxs-lookup"><span data-stu-id="e4e12-240">Add a navigation property to `ApplicationUser` that allows associated `UserClaims` to be referenced from the user:</span></span>

```csharp
public class ApplicationUser : IdentityUser
{
    public virtual ICollection<IdentityUserClaim<string>> Claims { get; set; }
}
```

<span data-ttu-id="e4e12-241">`IdentityUserClaim<TKey>` 的 `TKey` 是为用户的 PK 指定的类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-241">The `TKey` for `IdentityUserClaim<TKey>` is the type specified for the PK of users.</span></span> <span data-ttu-id="e4e12-242">在这种情况下，`TKey` `string`，因为正在使用默认值。</span><span class="sxs-lookup"><span data-stu-id="e4e12-242">In this case, `TKey` is `string` because the defaults are being used.</span></span> <span data-ttu-id="e4e12-243">它**不**是 `UserClaim` 实体类型的 PK 类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-243">It's **not** the PK type for the `UserClaim` entity type.</span></span>

<span data-ttu-id="e4e12-244">由于导航属性存在，因此必须在 `OnModelCreating`中进行配置：</span><span class="sxs-lookup"><span data-stu-id="e4e12-244">Now that the navigation property exists, it must be configured in `OnModelCreating`:</span></span>

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

<span data-ttu-id="e4e12-245">请注意，关系的配置与之前完全相同，只是在对的调用中指定的导航属性 `HasMany`。</span><span class="sxs-lookup"><span data-stu-id="e4e12-245">Notice that relationship is configured exactly as it was before, only with a navigation property specified in the call to `HasMany`.</span></span>

<span data-ttu-id="e4e12-246">导航属性仅存在于 EF 模型中，而不存在于数据库中。</span><span class="sxs-lookup"><span data-stu-id="e4e12-246">The navigation properties only exist in the EF model, not the database.</span></span> <span data-ttu-id="e4e12-247">由于关系的 FK 未更改，这种类型的模型更改不需要更新数据库。</span><span class="sxs-lookup"><span data-stu-id="e4e12-247">Because the FK for the relationship hasn't changed, this kind of model change doesn't require the database to be updated.</span></span> <span data-ttu-id="e4e12-248">这可以通过在更改后添加迁移来进行检查。</span><span class="sxs-lookup"><span data-stu-id="e4e12-248">This can be checked by adding a migration after making the change.</span></span> <span data-ttu-id="e4e12-249">`Up` 和 `Down` 方法为空。</span><span class="sxs-lookup"><span data-stu-id="e4e12-249">The `Up` and `Down` methods are empty.</span></span>

### <a name="add-all-user-navigation-properties"></a><span data-ttu-id="e4e12-250">添加所有用户导航属性</span><span class="sxs-lookup"><span data-stu-id="e4e12-250">Add all User navigation properties</span></span>

<span data-ttu-id="e4e12-251">以下示例使用上述部分作为指导，为用户上的所有关系配置单向导航属性：</span><span class="sxs-lookup"><span data-stu-id="e4e12-251">Using the section above as guidance, the following example configures unidirectional navigation properties for all relationships on User:</span></span>

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

### <a name="add-user-and-role-navigation-properties"></a><span data-ttu-id="e4e12-252">添加用户和角色导航属性</span><span class="sxs-lookup"><span data-stu-id="e4e12-252">Add User and Role navigation properties</span></span>

<span data-ttu-id="e4e12-253">以下示例使用上述部分作为指导，为用户和角色上的所有关系配置导航属性：</span><span class="sxs-lookup"><span data-stu-id="e4e12-253">Using the section above as guidance, the following example configures navigation properties for all relationships on User and Role:</span></span>

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

<span data-ttu-id="e4e12-254">说明：</span><span class="sxs-lookup"><span data-stu-id="e4e12-254">Notes:</span></span>

* <span data-ttu-id="e4e12-255">此示例还包括 `UserRole` 联接实体，该实体是从用户到角色导航多对多关系所必需的。</span><span class="sxs-lookup"><span data-stu-id="e4e12-255">This example also includes the `UserRole` join entity, which is needed to navigate the many-to-many relationship from Users to Roles.</span></span>
* <span data-ttu-id="e4e12-256">请记住更改导航属性的类型，以反映现在正在使用 `ApplicationXxx` 类型，而不是 `IdentityXxx` 类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-256">Remember to change the types of the navigation properties to reflect that `ApplicationXxx` types are now being used instead of `IdentityXxx` types.</span></span>
* <span data-ttu-id="e4e12-257">请记得使用一般 `ApplicationContext` 定义中的 `ApplicationXxx`。</span><span class="sxs-lookup"><span data-stu-id="e4e12-257">Remember to use the `ApplicationXxx` in the generic `ApplicationContext` definition.</span></span>

### <a name="add-all-navigation-properties"></a><span data-ttu-id="e4e12-258">添加所有导航属性</span><span class="sxs-lookup"><span data-stu-id="e4e12-258">Add all navigation properties</span></span>

<span data-ttu-id="e4e12-259">以下示例使用上述部分作为指导，为所有实体类型上的所有关系配置导航属性：</span><span class="sxs-lookup"><span data-stu-id="e4e12-259">Using the section above as guidance, the following example configures navigation properties for all relationships on all entity types:</span></span>

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

### <a name="use-composite-keys"></a><span data-ttu-id="e4e12-260">使用组合键</span><span class="sxs-lookup"><span data-stu-id="e4e12-260">Use composite keys</span></span>

<span data-ttu-id="e4e12-261">前面几节演示了如何更改标识模型中使用的密钥类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-261">The preceding sections demonstrated changing the type of key used in the Identity model.</span></span> <span data-ttu-id="e4e12-262">不支持或建议将标识密钥模型更改为使用组合键。</span><span class="sxs-lookup"><span data-stu-id="e4e12-262">Changing the Identity key model to use composite keys isn't supported or recommended.</span></span> <span data-ttu-id="e4e12-263">使用带有标识的组合键涉及更改标识管理器代码与模型交互的方式。</span><span class="sxs-lookup"><span data-stu-id="e4e12-263">Using a composite key with Identity involves changing how the Identity manager code interacts with the model.</span></span> <span data-ttu-id="e4e12-264">此自定义超出了本文档的范围。</span><span class="sxs-lookup"><span data-stu-id="e4e12-264">This customization is beyond the scope of this document.</span></span>

### <a name="change-tablecolumn-names-and-facets"></a><span data-ttu-id="e4e12-265">更改表/列名称和方面</span><span class="sxs-lookup"><span data-stu-id="e4e12-265">Change table/column names and facets</span></span>

<span data-ttu-id="e4e12-266">若要更改表和列的名称，请调用 `base.OnModelCreating`。</span><span class="sxs-lookup"><span data-stu-id="e4e12-266">To change the names of tables and columns, call `base.OnModelCreating`.</span></span> <span data-ttu-id="e4e12-267">然后，添加配置以覆盖任何默认值。</span><span class="sxs-lookup"><span data-stu-id="e4e12-267">Then, add configuration to override any of the defaults.</span></span> <span data-ttu-id="e4e12-268">例如，若要更改所有标识表的名称，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e4e12-268">For example, to change the name of all the Identity tables:</span></span>

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

<span data-ttu-id="e4e12-269">这些示例使用默认的标识类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-269">These examples use the default Identity types.</span></span> <span data-ttu-id="e4e12-270">如果使用 `ApplicationUser`之类的应用程序类型，请配置该类型而不是默认类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-270">If using an app type such as `ApplicationUser`, configure that type instead of the default type.</span></span>

<span data-ttu-id="e4e12-271">下面的示例将更改某些列名：</span><span class="sxs-lookup"><span data-stu-id="e4e12-271">The following example changes some column names:</span></span>

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

<span data-ttu-id="e4e12-272">某些类型的数据库列可以配置某些*方面*（例如，允许的最大 `string` 长度）。</span><span class="sxs-lookup"><span data-stu-id="e4e12-272">Some types of database columns can be configured with certain *facets* (for example, the maximum `string` length allowed).</span></span> <span data-ttu-id="e4e12-273">下面的示例设置了模型中多个 `string` 属性的列最大长度：</span><span class="sxs-lookup"><span data-stu-id="e4e12-273">The following example sets column maximum lengths for several `string` properties in the model:</span></span>

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

### <a name="map-to-a-different-schema"></a><span data-ttu-id="e4e12-274">映射到其他架构</span><span class="sxs-lookup"><span data-stu-id="e4e12-274">Map to a different schema</span></span>

<span data-ttu-id="e4e12-275">架构在数据库提供程序中的行为可能有所不同。</span><span class="sxs-lookup"><span data-stu-id="e4e12-275">Schemas can behave differently across database providers.</span></span> <span data-ttu-id="e4e12-276">对于 SQL Server，默认设置是在*dbo*架构中创建所有表。</span><span class="sxs-lookup"><span data-stu-id="e4e12-276">For SQL Server, the default is to create all tables in the *dbo* schema.</span></span> <span data-ttu-id="e4e12-277">可在其他架构中创建这些表。</span><span class="sxs-lookup"><span data-stu-id="e4e12-277">The tables can be created in a different schema.</span></span> <span data-ttu-id="e4e12-278">例如：</span><span class="sxs-lookup"><span data-stu-id="e4e12-278">For example:</span></span>

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    base.OnModelCreating(modelBuilder);

    modelBuilder.HasDefaultSchema("notdbo");
}
```

::: moniker range=">= aspnetcore-2.1"

### <a name="lazy-loading"></a><span data-ttu-id="e4e12-279">延迟加载</span><span class="sxs-lookup"><span data-stu-id="e4e12-279">Lazy loading</span></span>

<span data-ttu-id="e4e12-280">在本部分中，将添加对标识模型中的延迟加载代理的支持。</span><span class="sxs-lookup"><span data-stu-id="e4e12-280">In this section, support for lazy-loading proxies in the Identity model is added.</span></span> <span data-ttu-id="e4e12-281">延迟加载非常有用，因为它允许使用导航属性，而无需首先确保它们已加载。</span><span class="sxs-lookup"><span data-stu-id="e4e12-281">Lazy-loading is useful since it allows navigation properties to be used without first ensuring they're loaded.</span></span>

<span data-ttu-id="e4e12-282">可以通过多种方式使实体类型适用于延迟加载，如[EF Core 文档](/ef/core/querying/related-data#lazy-loading)中所述。</span><span class="sxs-lookup"><span data-stu-id="e4e12-282">Entity types can be made suitable for lazy-loading in several ways, as described in the [EF Core documentation](/ef/core/querying/related-data#lazy-loading).</span></span> <span data-ttu-id="e4e12-283">为简单起见，请使用延迟加载代理，这需要：</span><span class="sxs-lookup"><span data-stu-id="e4e12-283">For simplicity, use lazy-loading proxies, which requires:</span></span>

* <span data-ttu-id="e4e12-284">[Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/)包的安装。</span><span class="sxs-lookup"><span data-stu-id="e4e12-284">Installation of the [Microsoft.EntityFrameworkCore.Proxies](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Proxies/) package.</span></span>
* <span data-ttu-id="e4e12-285">调用[AddDbContext\<TContext >](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext)中的 <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*>。</span><span class="sxs-lookup"><span data-stu-id="e4e12-285">A call to <xref:Microsoft.EntityFrameworkCore.ProxiesExtensions.UseLazyLoadingProxies*> inside [AddDbContext\<TContext>](/dotnet/api/microsoft.extensions.dependencyinjection.entityframeworkservicecollectionextensions.adddbcontext).</span></span>
* <span data-ttu-id="e4e12-286">具有 `public virtual` 导航属性的公共实体类型。</span><span class="sxs-lookup"><span data-stu-id="e4e12-286">Public entity types with `public virtual` navigation properties.</span></span>

<span data-ttu-id="e4e12-287">下面的示例演示如何在 `Startup.ConfigureServices`中调用 `UseLazyLoadingProxies`：</span><span class="sxs-lookup"><span data-stu-id="e4e12-287">The following example demonstrates calling `UseLazyLoadingProxies` in `Startup.ConfigureServices`:</span></span>

```csharp
services
    .AddDbContext<ApplicationDbContext>(
        b => b.UseSqlServer(connectionString)
              .UseLazyLoadingProxies())
    .AddDefaultIdentity<ApplicationUser>()
    .AddEntityFrameworkStores<ApplicationDbContext>();
```

<span data-ttu-id="e4e12-288">有关将导航属性添加到实体类型的指导，请参阅前面的示例。</span><span class="sxs-lookup"><span data-stu-id="e4e12-288">Refer to the preceding examples for guidance on adding navigation properties to the entity types.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e4e12-289">其他资源</span><span class="sxs-lookup"><span data-stu-id="e4e12-289">Additional resources</span></span>

* <xref:security/authentication/scaffold-identity>

::: moniker-end
