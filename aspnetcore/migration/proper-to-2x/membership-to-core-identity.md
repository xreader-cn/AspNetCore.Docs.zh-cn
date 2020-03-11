---
title: ASP.NET 成员资格身份验证从迁移到 ASP.NET Core 2.0 标识
author: isaac2004
description: 了解如何迁移现有的 ASP.NET 应用程序使用成员资格到 ASP.NET Core 2.0 标识的身份验证。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/10/2019
uid: migration/proper-to-2x/membership-to-core-identity
ms.openlocfilehash: 3b708da13ff9f2887eee87ea17844312a4fe1b8d
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78651834"
---
# <a name="migrate-from-aspnet-membership-authentication-to-aspnet-core-20-identity"></a>ASP.NET 成员资格身份验证从迁移到 ASP.NET Core 2.0 标识

作者：[Isaac Levin](https://isaaclevin.com)

本文演示如何迁移使用成员资格到 ASP.NET Core 2.0 标识的身份验证的 ASP.NET 应用程序的数据库架构。

> [!NOTE]
> 本文档提供将基于 ASP.NET 成员资格的应用程序的数据库架构迁移到用于 ASP.NET Core 标识的数据库架构所需的步骤。 有关从基于 ASP.NET 成员身份的身份验证迁移到 ASP.NET Identity 的详细信息，请参阅[将现有应用从 SQL 成员身份迁移到 ASP.NET Identity](/aspnet/identity/overview/migrations/migrating-an-existing-website-from-sql-membership-to-aspnet-identity)。 有关 ASP.NET Core 标识的详细信息，请参阅[ASP.NET Core 上的标识简介](xref:security/authentication/identity)。

## <a name="review-of-membership-schema"></a>成员资格架构评审

在 ASP.NET 2.0 之前，开发人员负责为其应用创建整个身份验证和授权过程。 使用 ASP.NET 2.0，引入了成员身份，提供了一个样本解决方案来处理 ASP.NET 应用中的安全性。 开发人员现在可以使用[aspnet_regsql](https://msdn.microsoft.com/library/ms229862.aspx)的命令将架构启动到 SQL Server 数据库中。 运行此命令后，将在数据库中创建以下表。

  ![成员资格表](identity/_static/membership-tables.png)

若要将现有应用迁移到 ASP.NET Core 2.0 标识，这些表中的数据需要迁移到使用新的标识架构的表。

## <a name="aspnet-core-identity-20-schema"></a>ASP.NET Core 标识 2.0 架构

ASP.NET Core 2.0 遵循 ASP.NET 4.5 中引入的[标识](/aspnet/identity/index)原则。 尽管此原则是共享的，但在不同版本的 ASP.NET Core （请参阅将[身份验证和标识迁移到 ASP.NET Core 2.0](xref:migration/1x-to-2x/index)）之间的实现不同。

ASP.NET Core 2.0 标识查看架构的最快方法是创建新的 ASP.NET Core 2.0 应用程序。 在 Visual Studio 2017 中执行以下步骤：

1. 选择“文件” **“新建”** “项目” >  > 。
1. 创建一个名为*CoreIdentitySample*的新**ASP.NET Core Web 应用程序**项目。
1. 在下拉列表中选择**ASP.NET Core 2.0** ，然后选择 " **Web 应用程序**"。 此模板生成[Razor Pages](xref:razor-pages/index)应用。 单击 **"确定"** 之前，请单击 "**更改身份验证**"。
1. 为标识模板选择**单个用户帐户**。 最后，单击 **"确定**"，然后单击 **"确定"** 。 Visual Studio 创建项目时使用 ASP.NET Core 标识模板。
1. 选择 "**工具**" > **NuGet 包管理器**" > 程序包管理器**控制台**" 打开 "**包管理器控制台**" （PMC）窗口。
1. 导航到 PMC 中的项目根，并运行[实体框架（EF） Core](/ef/core) `Update-Database` 命令。

    ASP.NET Core 2.0 标识使用 EF Core 与存储身份验证数据的数据库进行交互。 为了使新创建的应用程序正常工作，需要有数据库来存储此数据。 创建新应用后，在数据库环境中检查架构的最快方法是使用[EF Core 迁移](/ef/core/managing-schemas/migrations/)来创建数据库。 此过程将创建一个在本地或其他位置模拟该架构的数据库。 有关详细信息，请查看前面的文档。

    EF Core 命令使用*appsettings*中指定的数据库的连接字符串。 以下连接字符串针对名为 *.asp 的* *localhost*上的数据库。 在此设置中，EF Core 配置为使用 `DefaultConnection` 连接字符串。

    ```json
    {
      "ConnectionStrings": {
        "DefaultConnection": "Server=localhost;Database=aspnet-core-identity;Trusted_Connection=True;MultipleActiveResultSets=true"
      }
    }
    ```

1. 选择 "**查看** > **SQL Server 对象资源管理器**。 展开对应于*appsettings*的 `ConnectionStrings:DefaultConnection` 属性中指定的数据库名称的节点。

    `Update-Database` 命令创建了用架构指定的数据库以及应用初始化所需的任何数据。 下图描绘了在前面的步骤中创建的表结构。

    ![标识表](identity/_static/identity-tables.png)

## <a name="migrate-the-schema"></a>迁移架构

有细微的差别的表结构和成员身份和 ASP.NET Core 标识字段。 模式已显著更改身份验证/授权与 ASP.NET 和 ASP.NET Core 应用程序。 仍用于标识的关键对象是*用户*和*角色*。 下面是*用户*、*角色*和*UserRoles*的映射表。

### <a name="users"></a>用户

|*标识<br>（dbo）。AspNetUsers*        ||*成员身份<br>（dbo. aspnet_Users/dbo. aspnet_Membership）*||
|----------------------------------------|-----------------------------------------------------------|
|**字段名称**                 |类型|**字段名称**                                    |类型|
|`Id`                           |字符串  |`aspnet_Users.UserId`                             |字符串  |
|`UserName`                     |字符串  |`aspnet_Users.UserName`                           |字符串  |
|`Email`                        |字符串  |`aspnet_Membership.Email`                         |字符串  |
|`NormalizedUserName`           |字符串  |`aspnet_Users.LoweredUserName`                    |字符串  |
|`NormalizedEmail`              |字符串  |`aspnet_Membership.LoweredEmail`                  |字符串  |
|`PhoneNumber`                  |字符串  |`aspnet_Users.MobileAlias`                        |字符串  |
|`LockoutEnabled`               |bit     |`aspnet_Membership.IsLockedOut`                   |bit     |

> [!NOTE]
> 并非所有字段映射类似都于从成员资格到 ASP.NET Core 标识的一对一关系。 前面的表使用默认成员资格用户架构，并将其映射到 ASP.NET Core 标识架构。 需要手动映射用于成员身份的任何其他自定义字段。 在此映射中，无密码映射，因为密码条件和密码 salts 不会在两者之间迁移。 **建议将密码保留为 null 并要求用户重置其密码。** 在 ASP.NET Core 标识中，如果用户被锁定，应将 `LockoutEnd` 设置为将来的某个日期。这会显示在迁移脚本中。

### <a name="roles"></a>角色

|*标识<br>（dbo）。AspNetRoles)*        ||*成员身份<br>（dbo. aspnet_Roles）*||
|----------------------------------------|-----------------------------------|
|**字段名称**                 |类型|**字段名称**   |类型         |
|`Id`                           |字符串  |`RoleId`         | 字符串          |
|`Name`                         |字符串  |`RoleName`       | 字符串          |
|`NormalizedName`               |字符串  |`LoweredRoleName`| 字符串          |

### <a name="user-roles"></a>用户角色

|*标识<br>（dbo）。AspNetUserRoles*||*成员身份<br>（dbo. aspnet_UsersInRoles）*||
|------------------------------------|------------------------------------------|
|**字段名称**           |类型  |**字段名称**|类型                   |
|`RoleId`                 |字符串    |`RoleId`      |字符串                     |
|`UserId`                 |字符串    |`UserId`      |字符串                     |

创建*用户*和*角色*的迁移脚本时引用前面的映射表。 下面的示例假定数据库服务器上有两个数据库。 一个数据库包含现有的 ASP.NET 成员身份架构和数据。 使用前面所述的步骤创建了另一个*CoreIdentitySample*数据库。 有关详细信息，请以内联方式包含注释。

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

完成上述脚本后，将向成员资格用户填充先前创建的 ASP.NET Core 标识应用。 用户需要在登录之前更改密码。

> [!NOTE]
> 如果成员资格系统中的用户的用户名与其电子邮件地址不匹配，则需要对之前创建的应用进行更改，以满足此要求。 默认模板要求 `UserName` 和 `Email` 相同。 对于它们不同的情况，需要将登录过程修改为使用 `UserName` 而不是 `Email`。

在登录页的 `PageModel`，位于*Pages\Account\Login.cshtml.cs*，从*Email*属性中删除 `[EmailAddress]` 属性。 将其重命名为*UserName*。 这需要在*视图*和*PageModel*中 `EmailAddress` 提到的任何位置进行更改。 结果应类似如下所示：

 ![固定登录](identity/_static/fixed-login.png)

## <a name="next-steps"></a>后续步骤

在本教程中，您学习了如何移植到 ASP.NET Core 2.0 标识从 SQL 成员资格用户。 有关 ASP.NET Core 标识的详细信息，请参阅[标识简介](xref:security/authentication/identity)。
