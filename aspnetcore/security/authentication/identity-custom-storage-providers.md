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
# <a name="custom-storage-providers-for-aspnet-core-identity"></a>ASP.NET Core标识的自定义的存储提供程序

作者：[Steve Smith](https://ardalis.com/)

ASP.NET Core标识是一种可扩展系统，可用于创建自定义存储提供程序并将其连接到你的应用。 本主题介绍如何创建 ASP.NET Core标识的自定义的存储提供程序。 它介绍如何创建自己的存储提供程序的重要概念，但不是分步演练。

[查看或下载 GitHub 中的示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/security/authentication/identity/sample)。

## <a name="introduction"></a>介绍

默认情况下，ASP.NET Core标识系统中使用实体框架核心的 SQL Server 数据库存储用户信息。 对于许多应用程序，此方法也适用。 但是，您可能更愿意使用不同的持久性机制或数据架构。 例如：

* 您使用[Azure 表存储](/azure/storage/)或其他数据存储。
* 对数据库表具有不同的结构。 
* 您可能希望使用不同的数据访问方法，如[Dapper](https://github.com/StackExchange/Dapper)。 

在每个这种情况下，可以为你的存储机制编写自定义提供程序和该提供程序插入您的应用程序。

ASP.NET Core标识包含在 Visual Studio 中使用"单个用户帐户"选项的项目模板中。

在使用.NET Core CLI 时，添加`-au Individual`:

```console
dotnet new mvc -au Individual
dotnet new webapi -au Individual
```

## <a name="the-aspnet-core-identity-architecture"></a>ASP.NET Core标识体系结构

ASP.NET Core标识包含类称为管理器和存储区。 *管理器*是高级类应用程序开发人员使用来执行操作，如创建标识用户。 *存储*是指定如何保持实体，例如用户和角色，较低级别类。 存储遵循存储库模式和紧密结合的持久性机制。 管理器被分开存储，这意味着您可以替换持久性机制，而无需更改应用程序代码中的 （除配置）。

下图显示了 web 应用与交互的方式管理器，而存储与数据访问层进行交互。

![ASP.NET Core 应用适用于管理器 （例如，UserManager、 RoleManager）。 管理器处理存储 (例如，UserStore) 它们进行通信的数据源使用 Entity Framework Core 等库。](identity-custom-storage-providers/_static/identity-architecture-diagram.png)

若要创建自定义存储提供程序，请使用此数据访问层 （绿色和灰色框上面的关系图中） 创建数据源、 数据访问层和交互的存储类。 不需要自定义管理器或与它们 （蓝色框上面） 进行交互的应用程序代码。

创建的新实例时`UserManager`或`RoleManager`提供用户类的类型和作为参数传递存储类的实例。 此方法使您可以插入到 ASP.NET Core的自定义的类。 

[重新配置应用程序以使用新的存储提供程序](#reconfigure-app-to-use-a-new-storage-provider)说明如何实例化`UserManager`和`RoleManager`使用自定义存储。

## <a name="aspnet-core-identity-stores-data-types"></a>ASP.NET Core标识将存储的数据类型

[ASP.NET Core标识](https://github.com/aspnet/identity)数据类型进行详细介绍下列各节：

### <a name="users"></a>用户

你的网站的已注册的用户。 [IdentityUser](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)可能扩展类型，或将其用作自定义类型的示例。 您不必继承来实现自定义标识存储解决方案的特定类型。

### <a name="user-claims"></a>用户声明

一组语句 (或[声明](/dotnet/api/system.security.claims.claim)) 有关的用户的表示用户的标识。 可以启用的用户的标识不是可以通过角色实现更大的表达式。

### <a name="user-logins"></a>用户登录名

有关外部身份验证提供程序 （如 Facebook 或 Microsoft 帐户） 的信息日志记录使用户登录时使用。 [示例](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)

### <a name="roles"></a>角色

你的站点的授权组。 包含角色 Id 和角色名称 （如"管理员"或"Employee"）。 [示例](/dotnet/api/microsoft.aspnet.identity.corecompat.identityrole)

## <a name="the-data-access-layer"></a>数据访问层

本主题假定你熟悉要使用的持久性机制以及如何创建实体，可用于该机制。 本主题不提供有关如何创建存储库或数据访问类; 详细信息使用 ASP.NET Core标识时，它提供了有关设计决策的一些建议。

为自定义的存储提供程序设计的数据访问层时具有的自由度。 只需创建要在应用中使用的功能的持久性机制。 例如，如果不应用程序中使用角色，你不必创建的角色或用户角色关联的存储。 你的技术和现有基础结构可能需要的 ASP.NET Core标识的默认实现不同的结构。 在数据访问层，提供用于存储实现的结构的逻辑。

数据访问层提供逻辑来将数据从 ASP.NET Core标识保存到数据源。 自定义的存储提供程序的数据访问层可能包括以下类用于存储用户和角色信息。

### <a name="context-class"></a>Context 类

若要连接到持久性机制并执行查询的信息进行封装。 多个数据类需要通常情况下通过依赖关系注入提供此类的实例。 [示例](/dotnet/api/microsoft.aspnet.identity.corecompat.identitydbcontext-1)。

### <a name="user-storage"></a>用户存储

存储和检索用户信息 （如用户名称和密码哈希）。 [示例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="role-storage"></a>角色存储

存储和检索角色信息 （例如角色名称）。 [示例](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)

### <a name="userclaims-storage"></a>UserClaims 存储

存储和检索用户声明信息 （如的声明类型和值）。 [示例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userlogins-storage"></a>UserLogins 存储

存储和检索用户登录信息 （例如外部身份验证提供程序）。 [示例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

### <a name="userrole-storage"></a>UserRole 存储

存储和检索哪些角色分配给哪些用户。 [示例](/dotnet/api/microsoft.aspnet.identity.corecompat.userstore-1)

**提示：** 仅实现想要使用应用程序中的类。

在数据访问类中，提供代码要执行数据操作的持久性机制。 例如，在自定义提供程序，可能会具有以下代码以创建新的用户在*存储*类：

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs?name=createuser&highlight=7)]

创建用户的实现逻辑位于`_usersTable.CreateAsync`方法，如下所示。

## <a name="customize-the-user-class"></a>自定义用户类

在实现存储提供程序时，创建一个用户类等效于[IdentityUser 类](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuser)。

您的用户类必须包含最低限度`Id`和一个`UserName`属性。

`IdentityUser`类定义的属性的`UserManager`调用时执行请求的操作。 默认类型`Id`属性是一个字符串，但可以继承自`IdentityUser<TKey, TUserClaim, TUserRole, TUserLogin, TUserToken>`和指定不同的类型。 框架需要要处理的数据类型转换的存储实现。

## <a name="customize-the-user-store"></a>自定义用户存储

创建`UserStore`类，该类提供用户的所有数据操作的方法。 此类是等效于[UserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.userstore-1)类。 在你`UserStore`类，请实现`IUserStore<TUser>`和所需的可选接口。 您选择实现的可选接口基于应用程序中提供的功能。

### <a name="optional-interfaces"></a>可选接口

* [IUserRoleStore](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)
* [IUserClaimStore](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)
* [IUserPasswordStore](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)
* [IUserSecurityStampStore](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)
* [IUserEmailStore](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)
* [IUserPhoneNumberStore](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)
* [IQueryableUserStore](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)
* [IUserLoginStore](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)
* [IUserTwoFactorStore](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)
* [IUserLockoutStore](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)

可选接口继承自`IUserStore<TUser>`。 可以看到存储中的部分实现的示例用户[示例应用](https://github.com/aspnet/Docs/blob/master/aspnetcore/security/authentication/identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/CustomUserStore.cs)。

在`UserStore`类，您使用您创建的用于执行操作的数据访问类。 这些通过依赖关系注入进行传递。 例如，在使用 Dapper 实现，SQL Server`UserStore`类具有`CreateAsync`使用的实例方法`DapperUsersTable`来插入新记录：

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/DapperUsersTable.cs?name=createuser&highlight=7)]

### <a name="interfaces-to-implement-when-customizing-user-store"></a>要实现自定义用户存储区时接口

* **IUserStore**  
 [IUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserstore-1)接口是在用户存储中必须实现的唯一接口。 它定义用于创建、 更新、 删除和检索用户的方法。
* **IUserClaimStore**  
 [IUserClaimStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserclaimstore-1)接口定义实现启用用户声明的方法。 它包含用于添加、 删除和检索用户声明的方法。
* **IUserLoginStore**  
 [IUserLoginStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserloginstore-1)定义实现以启用外部身份验证提供程序的方法。 它包含用于添加、 删除和检索用户登录名和用于检索用户的登录信息基于方法的方法。
* **IUserRoleStore**  
 [IUserRoleStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserrolestore-1)接口定义实现将用户映射到角色的方法。 它包含方法以添加、 删除和检索用户的角色和一个方法以检查是否将用户分配到角色。
* **IUserPasswordStore**  
 [IUserPasswordStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserpasswordstore-1)接口定义实现保留哈希的密码的方法。 它包含用于获取和设置哈希的密码和用于指示用户是否已设置密码的方法的方法。
* **IUserSecurityStampStore**  
 [IUserSecurityStampStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusersecuritystampstore-1)接口定义实现用于安全戳，该值指示是否已更改用户的帐户信息的方法。 用户更改密码，或添加或删除登录名时，将更新此 stamp。 它包含方法用于获取和设置的安全戳。
* **IUserTwoFactorStore**  
 [IUserTwoFactorStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iusertwofactorstore-1)接口定义实现以支持双因素身份验证的方法。 它包含用于获取和设置是否为用户启用双因素身份验证方法。
* **IUserPhoneNumberStore**  
 [IUserPhoneNumberStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserphonenumberstore-1)接口定义实现来存储用户的电话号码的方法。 它包含用于获取和设置的电话号码和电话号码是否已确认的方法。
* **IUserEmailStore**  
 [IUserEmailStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuseremailstore-1)接口定义实现来存储用户的电子邮件地址的方法。 它包含用于获取和设置的电子邮件地址和电子邮件是否已确认的方法。
* **IUserLockoutStore**  
 [IUserLockoutStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iuserlockoutstore-1)接口定义实现以存储有关锁定的帐户信息的方法。 它包含用于跟踪失败的访问尝试和锁定的方法。
* **IQueryableUserStore**  
 [IQueryableUserStore&lt;TUser&gt; ](/dotnet/api/microsoft.aspnetcore.identity.iqueryableuserstore-1)接口定义实现以提供可查询的用户存储区的成员。

在应用中实现所需的接口。 例如：

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

### <a name="identityuserclaim-identityuserlogin-and-identityuserrole"></a>IdentityUserClaim、 IdentityUserLogin 和 IdentityUserRole

`Microsoft.AspNet.Identity.EntityFramework`命名空间包含的实现[IdentityUserClaim](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserclaim-1)， [IdentityUserLogin](/dotnet/api/microsoft.aspnet.identity.corecompat.identityuserlogin)，并且[IdentityUserRole](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.identityuserrole-1)类。 如果使用这些功能，您可能想要创建这些类的版本并定义您的应用程序的属性。 但是，有时它是更高效，若要执行基本操作 （如添加或删除用户的声明） 时不将这些实体加载到内存。 相反后, 端存储类可以执行这些操作直接在数据源上。 例如，`UserStore.GetClaimsAsync`方法可以调用`userClaimTable.FindByUserId(user.Id)`方法来执行查询，在直接表并返回声明的列表。

## <a name="customize-the-role-class"></a>自定义角色类

在实现角色存储提供程序时，您可以创建自定义角色类型。 无需实现特定接口，但是它必须具有`Id`，其中包含通常`Name`属性。

下面是示例角色类：

[!code-csharp[](identity-custom-storage-providers/sample/CustomIdentityProviderSample/CustomProvider/ApplicationRole.cs)]

## <a name="customize-the-role-store"></a>自定义角色存储

您可以创建`RoleStore`类，该类提供对角色的所有数据操作的方法。 此类是等效于[RoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.entityframeworkcore.rolestore-1)类。 在中`RoleStore`类，实现`IRoleStore<TRole>`和 （可选）`IQueryableRoleStore<TRole>`接口。

* **IRoleStore&lt;TRole&gt;**  
 [IRoleStore&lt;TRole&gt; ](/dotnet/api/microsoft.aspnetcore.identity.irolestore-1)接口定义了要在角色存储类中实现的方法。 它包含用于创建、 更新、 删除和检索角色的方法。
* **RoleStore&lt;TRole&gt;**  
 若要自定义`RoleStore`，创建一个实现类`IRoleStore<TRole>`接口。 

## <a name="reconfigure-app-to-use-a-new-storage-provider"></a>重新配置应用程序以使用新的存储提供程序

已实现存储提供程序之后, 你将应用配置为使用它。 如果您的应用程序使用默认的提供程序，请将它都替换为自定义提供程序。

1. 删除`Microsoft.AspNetCore.EntityFramework.Identity`NuGet 包。
1. 如果存储提供程序驻留在单独的项目或包中，添加对它的引用。
1. 替换为对所有引用`Microsoft.AspNetCore.EntityFramework.Identity`使用 using 语句的命名空间的存储提供程序。
1. 在中`ConfigureServices`方法中，更改`AddIdentity`方法以使用自定义类型。 您可以创建你自己的扩展方法实现此目的。 请参阅[IdentityServiceCollectionExtensions](https://github.com/aspnet/Identity/blob/rel/1.1.0/src/Microsoft.AspNetCore.Identity/IdentityServiceCollectionExtensions.cs)有关的示例。
1. 如果使用的角色，更新`RoleManager`若要使用你`RoleStore`类。
1. 对应用程序的配置更新连接字符串和凭据。

示例:

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

## <a name="references"></a>参考资料

* [ASP.NET 4.x 标识的自定义存储提供程序](/aspnet/identity/overview/extensibility/overview-of-custom-storage-providers-for-aspnet-identity)
* [ASP.NET Core 标识](https://github.com/aspnet/identity)&ndash;此存储库包括社区维护存储提供程序的链接。
