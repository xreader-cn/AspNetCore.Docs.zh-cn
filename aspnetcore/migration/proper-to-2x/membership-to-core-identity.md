---
title: ASP.NET 成员资格身份验证从迁移到 ASP.NET Core 2.0 标识
author: isaac2004
description: 了解如何迁移现有的 ASP.NET 应用程序使用成员资格到 ASP.NET Core 2.0 标识的身份验证。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65084877"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>ASP.NET 成员资格身份验证从迁移到 ASP.NET Core 2.0 标识

作者：[Isaac Levin](https://isaaclevin.com)

本文演示如何迁移使用成员资格到 ASP.NET Core 2.0 标识的身份验证的 ASP.NET 应用程序的数据库架构。

> [!NOTE]
> 本文档提供将基于 ASP.NET 成员资格的应用程序的数据库架构迁移到用于 ASP.NET Core 标识的数据库架构所需的步骤。 有关从基于 ASP.NET 成员资格的身份验证迁移到 ASP.NET 标识的详细信息，请参阅[将现有应用程序从 SQL 成员身份迁移到 ASP.NET 标识](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)。 有关 ASP.NET Core 标识的详细信息，请参阅[ASP.NET Core 上的标识简介](xref:security/authentication/identity)。

## <a name="review-of-membership-schema"></a>查看的成员身份架构

在 ASP.NET 2.0 之前开发人员被负责创建自己的应用的整个身份验证和授权过程。 使用 ASP.NET 2.0 成员身份引入时，提供处理 ASP.NET 应用程序中的安全性的样本解决方案。 开发人员现在可以启动到与 SQL Server 数据库架构[aspnet_regsql.exe](https://msdn.microsoft.com/library/ms229862.aspx)命令。 运行此命令后，已在数据库中创建以下表。

  ![成员资格表](identity/_static/membership-tables.png)

若要将现有应用迁移到 ASP.NET Core 2.0 标识，这些表中的数据需要迁移到使用新的标识架构的表。

## <a name="aspnet-core-identity-20-schema"></a>ASP.NET Core 标识 2.0 架构

ASP.NET Core 2.0 遵循[标识](/aspnet/identity/index)ASP.NET 4.5 中引入的原则。 尽管共享的原则，框架之间的实现是不同的甚至 ASP.NET Core 的版本之间 (请参阅[将身份验证和标识迁移到 ASP.NET Core 2.0](xref:migration/1x-to-2x/index))。

ASP.NET Core 2.0 标识查看架构的最快方法是创建新的 ASP.NET Core 2.0 应用程序。 请按照 Visual Studio 2017 中的执行以下步骤操作：

1. 选择“文件” > “新建” > “项目”。
1. 创建一个新**ASP.NET Core Web 应用程序**名为项目*CoreIdentitySample*。
1. 选择**ASP.NET Core 2.0**在下拉列表中，然后选择**Web 应用程序**。 此模板会生成[Razor 页面](xref:razor-pages/index)应用。 然后再单击**确定**，单击**更改身份验证**。
1. 选择**单个用户帐户**标识模板。 最后，单击**确定**，然后**确定**。 Visual Studio 创建项目时使用 ASP.NET Core 标识模板。
1. 选择**工具** > **NuGet 包管理器** > **程序包管理器控制台**打开**包管理器控制台**(PMC) 窗口。
1. 导航到项目根目录中 PMC 中，然后运行[Entity Framework (EF) Core](/ef/core) `Update-Database`命令。

    ASP.NET Core 2.0 标识使用 EF Core 与存储身份验证数据的数据库进行交互。 为了使新创建的应用程序能够，那里需要使用数据库来存储此数据。 创建新的应用程序之后, 检查的数据库环境中的架构的最快方法是创建数据库使用[EF Core 迁移](/ef/core/managing-schemas/migrations/)。 此过程创建数据库、 在本地或其他位置，模拟该架构。 查看上述文档，有关详细信息。

    EF Core 命令中指定的数据库使用连接字符串*appsettings.json*。 下面的连接字符串上的目标数据库*localhost*名为*asp net core 标识*。 在此设置中，EF Core 配置为使用`DefaultConnection`连接字符串。

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. 选择**视图** > **SQL Server 对象资源管理器**。 展开中指定的数据库名称与对应的节点`ConnectionStrings:DefaultConnection`的属性*appsettings.json*。

    `Update-Database`命令创建与架构指定的数据库和任何所需的应用程序初始化的数据。 下图描绘了使用上述步骤创建的表结构。

    ![标识表](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>迁移架构

有细微的差别的表结构和成员身份和 ASP.NET Core 标识字段。 模式已显著更改身份验证/授权与 ASP.NET 和 ASP.NET Core 应用程序。 仍用于标识的关键对象是*用户*并*角色*。 以下是有关映射表*用户*，*角色*，并*UserRoles*。

### <a name="users"></a>用户

|*Identity<br>(dbo.AspNetUsers)*        ||*Membership<br>(dbo.aspnet_Users / dbo.aspnet_Membership)*||
|----------------------------------------|-----------------------------------------------------------|
|**字段名称**                 |**Type**|**字段名称**                                    |**Type**|
|`Id`                           |string  |`aspnet_Users.UserId`                             |string  |
|`UserName`                     |string  |`aspnet_Users.UserName`                           |string  |
|`Email`                        |string  |`aspnet_Membership.Email`                         |string  |
|`NormalizedUserName`           |string  |`aspnet_Users.LoweredUserName`                    |string  |
|`NormalizedEmail`              |string  |`aspnet_Membership.LoweredEmail`                  |string  |
|`PhoneNumber`                  |string  |`aspnet_Users.MobileAlias`                        |string  |
|`LockoutEnabled`               |位     |`aspnet_Membership.IsLockedOut`                   |位     |

> [!NOTE]
> 并非所有字段映射类似都于从成员资格到 ASP.NET Core 标识的一对一关系。 前面的表使用默认成员资格用户架构，并将其映射到 ASP.NET Core 标识架构。 用于成员资格的任何其他自定义字段需要手动映射。 在此映射中，则没有密码的映射因为两者之间不迁移密码条件和密码 salt。 **建议可保留为 null 的密码，并要求用户重置其密码。** 在 ASP.NET Core 标识`LockoutEnd`应设置为在将来的某个日期中，如果用户被锁定。迁移脚本所示。

### <a name="roles"></a>角色

|*Identity<br>(dbo.AspNetRoles)*        ||*Membership<br>(dbo.aspnet_Roles)*||
|----------------------------------------|-----------------------------------|
|**字段名称**                 |**Type**|**字段名称**   |**Type**         |
|`Id`                           |string  |`RoleId`         | string          |
|`Name`                         |string  |`RoleName`       | string          |
|`NormalizedName`               |string  |`LoweredRoleName`| string          |

### <a name="user-roles"></a>用户角色

|*Identity<br>(dbo.AspNetUserRoles)*||*Membership<br>(dbo.aspnet_UsersInRoles)*||
|------------------------------------|------------------------------------------|
|**字段名称**           |**Type**  |**字段名称**|**Type**                   |
|`RoleId`                 |string    |`RoleId`      |string                     |
|`UserId`                 |string    |`UserId`      |string                     |

创建的迁移脚本时，引用前面的映射表*用户*并*角色*。 下面的示例假定数据库服务器上有两个数据库。 一个数据库包含的现有 ASP.NET 成员身份架构和数据。 另*CoreIdentitySample*使用前面所述的步骤创建数据库。 注释是以内联形式包含有关更多详细信息。

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

完成操作后的脚本，使用成员资格用户填充前面创建的 ASP.NET Core 标识应用。 用户需要在登录之前更改其密码。

> [!NOTE]
> 如果成员资格系统必须具有不匹配其电子邮件地址的用户名的用户，不需要对创建早期为解决此问题的应用更改。 默认模板需要`UserName`和`Email`相同。 它们是不同的情况下，登录过程需要进行修改以使用`UserName`而不是`Email`。

在中`PageModel`的登录页上，位于*Pages\Account\Login.cshtml.cs*，删除`[EmailAddress]`属性从*电子邮件*属性。 其重命名为*用户名*。 这需要更改无论在何处`EmailAddress`所述，在*视图*并*PageModel*。 结果看起来如下所示：

 ![固定的登录名](identity/_static/fixed-login.png)

## <a name="next-steps"></a>后续步骤

在本教程中，您学习了如何移植到 ASP.NET Core 2.0 标识从 SQL 成员资格用户。 有关 ASP.NET Core 标识的详细信息，请参阅[标识简介](xref:security/authentication/identity)。
