---
title: 使用受授权的用户数据创建 ASP.NET Core 应用
author: rick-anderson
description: 了解如何使用受保护的授权的用户数据创建 Razor 页面应用。 包括 HTTPS、 身份验证、 安全性、 ASP.NET Core 标识。
ms.author: riande
ms.date: 12/18/2018
ms.custom: mvc, seodec18
uid: security/authorization/secure-data
ms.openlocfilehash: 4b94cc53777308deb26521a079d8a1c2742744db
ms.sourcegitcommit: 4fe3ae892f54dc540859bff78741a28c2daa9a38
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/04/2019
ms.locfileid: "68776747"
---
# <a name="create-an-aspnet-core-app-with-user-data-protected-by-authorization"></a>使用受授权的用户数据创建 ASP.NET Core 应用

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Joe Audette](https://twitter.com/joeaudette)

::: moniker range="<= aspnetcore-1.1"

请参阅[此 PDF](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_1-16-18.pdf) ASP.NET Core MVC 版本。 本教程的 ASP.NET Core 1.1 版本是[这](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data)文件夹。 ASP.NET Core 示例是在 1.1[示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/final2)。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

请参阅[此 pdf](https://webpifeed.blob.core.windows.net/webpifeed/Partners/asp.net_repo_pdf_July16_18.pdf)

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

本教程演示如何使用受授权的用户数据创建 ASP.NET Core web 应用。 它显示的身份验证 （注册） 的用户的联系人列表已创建。 有三个安全组：

* **注册用户**可以查看所有已批准的数据还可以编辑/删除其自己的数据。
* **管理器**可以批准或拒绝的联系人数据。 仅已批准的联系人是对用户可见。
* **管理员**可以批准/拒绝和编辑/删除的任何数据。

此文档中的图像与最新模板完全不匹配。

在下图中，用户 Rick (`rick@example.com`) 登录。 Rick 只能查看允许的联系人和**编辑**/**删除**/**新建**其联系人的链接。 只有最后一个记录，创建由 Rick，显示**编辑**并**删除**链接。 其他用户不会看到的最后一个记录，直到经理或管理员的状态更改为"已批准"。

![屏幕截图显示 Rick 登录](secure-data/_static/rick.png)

在下图中, `manager@contoso.com`已登录, 并在管理器的角色中:

![屏幕截图显示manager@contoso.com登录](secure-data/_static/manager1.png)

下图显示在管理器的联系人的详细信息视图：

![联系人的经理的视图](secure-data/_static/manager.png)

**批准**并**拒绝**按钮仅显示经理和管理员。

在下图中, `admin@contoso.com`以管理员的角色登录和:

![屏幕截图显示admin@contoso.com登录](secure-data/_static/admin.png)

管理员具有所有权限。 她可以读取、 编辑或删除任何联系，并更改联系人的状态。

通过创建该应用[基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)以下`Contact`模型：

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

此示例包含以下授权处理程序：

* `ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。
* `ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。
* `ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。

## <a name="prerequisites"></a>系统必备

本教程被高级。 您应熟悉：

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [身份验证](xref:security/authentication/identity)
* [帐户确认和密码恢复](xref:security/authentication/accconfirm)
* [授权](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Starter 和已完成应用程序

[下载](xref:index#how-to-download-a-sample)[完成](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)应用。 [测试](#test-the-completed-app)已完成的应用，使你熟悉其安全功能。

### <a name="the-starter-app"></a>入门级应用

[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。

运行应用，点击**ContactManager**链接，并验证是否可以创建、 编辑和删除联系人。

## <a name="secure-user-data"></a>保护用户数据

以下部分介绍了所有主要的步骤以创建安全的用户数据应用程序。 您可能会发现引用的已完成项目很有帮助。

### <a name="tie-the-contact-data-to-the-user"></a>将绑定到用户的联系人数据

使用 ASP.NET[标识](xref:security/authentication/identity)用户 ID，以确保用户可以编辑其数据，而不是其他用户数据。 添加`OwnerID`并`ContactStatus`到`Contact`模型：

[!code-csharp[](secure-data/samples/final3/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` 是从用户的 ID`AspNetUser`表中[标识](xref:security/authentication/identity)数据库。 `Status`字段确定是否可由普通用户查看联系人。

创建新的迁移并更新数据库：

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>将角色服务添加到标识

追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)添加角色服务：

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet2&highlight=9)]

### <a name="require-authenticated-users"></a>需要身份验证的用户

设置默认身份验证策略以要求用户进行身份验证：

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet&highlight=15-99)] 

 可以选择在 Razor 页面、 控制器或操作方法级别使用的身份验证禁用`[AllowAnonymous]`属性。 设置默认身份验证策略以要求用户进行身份验证来保护新添加的 Razor 页面和控制器。 默认情况下所需的身份验证是比依赖于新的控制器和 Razor 页，以包含更安全`[Authorize]`属性。

将[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)添加到 "索引" 和 "隐私" 页, 以便匿名用户在注册之前可以获取有关站点的信息。

[!code-csharp[](secure-data/samples/final3/Pages/Index.cshtml.cs?highlight=1,7)]

### <a name="configure-the-test-account"></a>配置测试帐户

`SeedData`类创建两个帐户： 管理员和管理员。 使用[机密管理器工具](xref:security/app-secrets)设置这些帐户的密码。 从项目目录中设置密码 (目录包含*Program.cs*):

```console
dotnet user-secrets set SeedUserPW <PW>
```

如果未指定强密码，将引发异常时`SeedData.Initialize`调用。

更新`Main`使用测试密码：

[!code-csharp[](secure-data/samples/final3/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>创建测试帐户和更新联系人

更新`Initialize`中的方法`SeedData`类，以创建测试帐户：

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet_Initialize)]

添加管理员用户 ID 和`ContactStatus`到联系人。 先创建一个"已提交"和一个"已拒绝"的联系人。 将用户 ID 和状态添加到所有联系人。 只能有一个联系人所示：

[!code-csharp[](secure-data/samples/final3/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>创建所有者、 经理和管理员授权处理程序

创建`ContactIsOwnerAuthorizationHandler`类中*授权*文件夹。 `ContactIsOwnerAuthorizationHandler`验证对资源进行操作的用户拥有的资源。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`调用[上下文。成功](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)当前经过身份验证的用户是否联系所有者。 授权处理程序通常：

* 返回`context.Succeed`满足的要求。
* 返回`Task.CompletedTask`时不符合要求。 `Task.CompletedTask`不是成功或失败&mdash;, 它允许其他授权处理程序运行。

如果你需要将显式失败，返回[上下文。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。

应用程序允许联系所有者到编辑/删除/创建他们自己的数据。 `ContactIsOwnerAuthorizationHandler` 不需要检查要求参数中传递该操作。

### <a name="create-a-manager-authorization-handler"></a>创建管理器授权处理程序

创建`ContactManagerAuthorizationHandler`类中*授权*文件夹。 `ContactManagerAuthorizationHandler`验证对资源进行操作的用户是管理员。 只有经理们才可以批准或拒绝内容的更改 （新的或已更改）。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>创建管理员授权处理程序

创建`ContactAdministratorsAuthorizationHandler`类中*授权*文件夹。 `ContactAdministratorsAuthorizationHandler`验证对资源进行操作的用户是管理员。 管理员可以执行所有操作。

[!code-csharp[](secure-data/samples/final3/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>注册授权处理程序

必须为注册服务使用 Entity Framework Core[依赖关系注入](xref:fundamentals/dependency-injection)使用[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)。           `ContactIsOwnerAuthorizationHandler`使用 ASP.NET Core[标识](xref:security/authentication/identity)，这基于实体框架核心。 注册服务集合的处理程序，以便它们可供`ContactsController`通过[依赖关系注入](xref:fundamentals/dependency-injection)。 将以下代码添加到末尾`ConfigureServices`:

[!code-csharp[](secure-data/samples/final3/Startup.cs?name=snippet_defaultPolicy&highlight=23-99)]

`ContactAdministratorsAuthorizationHandler` 和`ContactManagerAuthorizationHandler`添加为单一实例。 它们是单一实例，因为它们不使用 EF 和所需的所有信息都位于`Context`参数的`HandleRequirementAsync`方法。

## <a name="support-authorization"></a>支持授权

在本部分中，将更新 Razor 页面和添加操作要求类。

### <a name="review-the-contact-operations-requirements-class"></a>查看联系人操作要求类

查看`ContactOperations`类。 此类包含要求应用支持：

[!code-csharp[](secure-data/samples/final3/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>创建联系人 Razor 页面的基类

创建一个包含在联系人 Razor 页面使用的服务的基类。 基类将初始化代码放在一个位置：

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/DI_BasePageModel.cs)]

前面的代码：

* 添加`IAuthorizationService`服务对授权处理程序的访问权限。
* 将标识添加`UserManager`服务。
* 添加 `ApplicationDbContext`。

### <a name="update-the-createmodel"></a>更新 CreateModel

更新创建页面模型构造函数以使用`DI_BasePageModel`基类：

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

更新`CreateModel.OnPostAsync`方法：

* 添加到的用户 ID`Contact`模型。
* 调用授权处理程序，以验证用户有权创建联系人。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>更新 IndexModel

更新`OnGetAsync`方法，以便仅被批准的联系人显示为普通用户：

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>更新 EditModel

添加授权处理程序以验证的用户拥有联系人。 正在验证资源授权，因为`[Authorize]`属性不能满足。 评估属性时，应用程序不具有对资源的访问。 基于资源的授权必须是命令性。 应用程序页面模型中加载或加载处理程序本身内获得资源的访问权限，则必须执行检查。 您经常访问的资源，通过传入的资源键。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>更新 DeleteModel

更新要使用授权处理程序来验证用户具有 delete 权限 contact 上的删除页面模型。

[!code-csharp[](secure-data/samples/final3/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>将授权服务注入到视图

目前，此 UI 显示编辑和删除的用户无法修改的联系人的链接。

将授权服务注入*Pages/_ViewImports*文件中, 使其可供所有视图使用:

[!code-cshtml[](secure-data/samples/final3/Pages/_ViewImports.cshtml?highlight=6-99)]

上述标记添加了多种`using`语句。

更新**编辑**并**删除**中的链接*Pages/Contacts/Index.cshtml*以便仅在呈现具有适当权限的用户：

[!code-cshtml[](secure-data/samples/final3/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> 隐藏用户没有权限更改的数据中的链接不会保护应用。 隐藏链接使应用更加友好的用户显示唯一有效的链接。 用户可以 hack 生成的 Url 以调用编辑和删除操作上不拥有的数据。 Razor 页面或控制器必须强制执行访问检查，以保护数据。

### <a name="update-details"></a>更新详细信息

更新的详细信息视图，使管理员可以批准或拒绝联系人：

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

更新详细信息页模型：

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>添加或删除角色对用户的

请参阅[本期](https://github.com/aspnet/AspNetCore.Docs/issues/8502)有关的信息：

* 从用户删除的特权。 例如, 在聊天应用中对用户进行静音。
* 将权限添加到用户。

## <a name="test-the-completed-app"></a>测试已完成的应用程序

如果你尚未设置设定为种子的用户帐户的密码，使用[机密管理器工具](xref:security/app-secrets#secret-manager)设置密码：

* 选择强密码:使用八个或更多字符, 并且至少使用一个大写字符、数字和符号。 例如，`Passw0rd!`符合强密码要求。
* 执行以下命令从项目的文件夹，其中`<PW>`的密码：

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

如果应用了联系人：

* 删除所有记录中`Contact`表。
* 重新启动应用以设定数据库种子。

测试已完成的应用程序的简单方法是启动三个不同的浏览器 （或 incognito/InPrivate 会话）。 在一个浏览器中注册一个新用户 (例如， `test@example.com`)。 登录到每个浏览器使用不同的用户。 验证以下操作：

* 已注册的用户可以查看所有已批准的联系人数据。
* 已注册的用户可以编辑/删除其自己的数据。
* 经理可以批准/拒绝的联系人数据。 `Details`视图视图将显示**批准**并**拒绝**按钮。
* 管理员可以批准/拒绝和编辑/删除所有数据。

| “用户”                | 由应用程序进行种子设定 | 选项                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | 否                | 编辑/删除自己的数据。                |
| manager@contoso.com | 是               | 批准/拒绝和编辑/删除拥有的数据。 |
| admin@contoso.com   | 是               | 批准/拒绝和编辑/删除所有数据。 |

在管理员的浏览器中创建联系人。 删除 URL 复制并编辑从管理员的联系信息。 将下面的链接粘贴到测试用户的浏览器以验证测试用户不能执行这些操作。

## <a name="create-the-starter-app"></a>创建入门级应用

* 创建名为"ContactManager"Razor 页面应用
  * 创建包含应用**单个用户帐户**。
  * 它命名为"ContactManager"使命名空间匹配的示例中使用的命名空间。
  * `-uld` 指定 LocalDB，而不是 SQLite

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* 添加*模型/联系方式*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* 基架`Contact`模型。
* 创建初始迁移并更新数据库：

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet tool install -g dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
dotnet ef database drop -f
dotnet ef migrations add initial
dotnet ef database update
  ```

如果使用`dotnet aspnet-codegenerator razorpage`命令遇到 bug, 请参阅[此 GitHub 问题](https://github.com/aspnet/Scaffolding/issues/984)。

* 更新*Pages/Shared/_Layout*文件中的**ContactManager**定位点:

 ```cshtml
<a class="navbar-brand" asp-area="" asp-page="/Contacts/Index">ContactManager</a>
  ```

* 测试应用程序的创建、 编辑和删除联系人

### <a name="seed-the-database"></a>设定数据库种子

将[SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter3/Data/SeedData.cs)类添加到*Data*文件夹:

[!code-csharp[](secure-data/samples/starter3/Data/SeedData.cs)]

调用`SeedData.Initialize`从`Main`:

[!code-csharp[](secure-data/samples/starter3/Program.cs)]

测试应用程序设定数据库种子。 如果联系人 DB 中有任何行，则不会运行 seed 方法。

::: moniker-end

::: moniker range=">= aspnetcore-2.1 < aspnetcore-3.0"

本教程演示如何使用受授权的用户数据创建 ASP.NET Core web 应用。 它显示的身份验证 （注册） 的用户的联系人列表已创建。 有三个安全组：

* **注册用户**可以查看所有已批准的数据还可以编辑/删除其自己的数据。
* **管理器**可以批准或拒绝的联系人数据。 仅已批准的联系人是对用户可见。
* **管理员**可以批准/拒绝和编辑/删除的任何数据。

在下图中，用户 Rick (`rick@example.com`) 登录。 Rick 只能查看允许的联系人和**编辑**/**删除**/**新建**其联系人的链接。 只有最后一个记录，创建由 Rick，显示**编辑**并**删除**链接。 其他用户不会看到的最后一个记录，直到经理或管理员的状态更改为"已批准"。

![屏幕截图显示 Rick 登录](secure-data/_static/rick.png)

在下图中, `manager@contoso.com`已登录, 并在管理器的角色中:

![屏幕截图显示manager@contoso.com登录](secure-data/_static/manager1.png)

下图显示在管理器的联系人的详细信息视图：

![联系人的经理的视图](secure-data/_static/manager.png)

**批准**并**拒绝**按钮仅显示经理和管理员。

在下图中, `admin@contoso.com`以管理员的角色登录和:

![屏幕截图显示admin@contoso.com登录](secure-data/_static/admin.png)

管理员具有所有权限。 她可以读取、 编辑或删除任何联系，并更改联系人的状态。

通过创建该应用[基架](xref:tutorials/first-mvc-app/adding-model#scaffold-the-movie-model)以下`Contact`模型：

[!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

此示例包含以下授权处理程序：

* `ContactIsOwnerAuthorizationHandler`：确保用户只能编辑其数据。
* `ContactManagerAuthorizationHandler`：允许经理批准或拒绝联系人。
* `ContactAdministratorsAuthorizationHandler`：允许管理员批准或拒绝联系人以及编辑/删除联系人。

## <a name="prerequisites"></a>系统必备

本教程被高级。 您应熟悉：

* [ASP.NET Core](xref:tutorials/first-mvc-app/start-mvc)
* [身份验证](xref:security/authentication/identity)
* [帐户确认和密码恢复](xref:security/authentication/accconfirm)
* [授权](xref:security/authorization/introduction)
* [Entity Framework Core](xref:data/ef-mvc/intro)

## <a name="the-starter-and-completed-app"></a>Starter 和已完成应用程序

[下载](xref:index#how-to-download-a-sample)[完成](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples)应用。 [测试](#test-the-completed-app)已完成的应用，使你熟悉其安全功能。

### <a name="the-starter-app"></a>入门级应用

[下载](xref:index#how-to-download-a-sample)[初学者](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/)应用。

运行应用，点击**ContactManager**链接，并验证是否可以创建、 编辑和删除联系人。

## <a name="secure-user-data"></a>保护用户数据

以下部分介绍了所有主要的步骤以创建安全的用户数据应用程序。 您可能会发现引用的已完成项目很有帮助。

### <a name="tie-the-contact-data-to-the-user"></a>将绑定到用户的联系人数据

使用 ASP.NET[标识](xref:security/authentication/identity)用户 ID，以确保用户可以编辑其数据，而不是其他用户数据。 添加`OwnerID`并`ContactStatus`到`Contact`模型：

[!code-csharp[](secure-data/samples/final2.1/Models/Contact.cs?name=snippet1&highlight=5-6,16-999)]

`OwnerID` 是从用户的 ID`AspNetUser`表中[标识](xref:security/authentication/identity)数据库。 `Status`字段确定是否可由普通用户查看联系人。

创建新的迁移并更新数据库：

```console
dotnet ef migrations add userID_Status
dotnet ef database update
```

### <a name="add-role-services-to-identity"></a>将角色服务添加到标识

追加[AddRoles](/dotnet/api/microsoft.aspnetcore.identity.identitybuilder.addroles#Microsoft_AspNetCore_Identity_IdentityBuilder_AddRoles__1)添加角色服务：

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet2&highlight=12)]

### <a name="require-authenticated-users"></a>需要身份验证的用户

设置默认身份验证策略以要求用户进行身份验证：

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet&highlight=17-99)] 

 可以选择在 Razor 页面、 控制器或操作方法级别使用的身份验证禁用`[AllowAnonymous]`属性。 设置默认身份验证策略以要求用户进行身份验证来保护新添加的 Razor 页面和控制器。 默认情况下所需的身份验证是比依赖于新的控制器和 Razor 页，以包含更安全`[Authorize]`属性。

添加[AllowAnonymous](/dotnet/api/microsoft.aspnetcore.authorization.allowanonymousattribute)到索引中，因此匿名用户可以获取有关站点的信息注册有关，和联系人页面。

[!code-csharp[](secure-data/samples/final2.1/Pages/Index.cshtml.cs?highlight=1,6)]

### <a name="configure-the-test-account"></a>配置测试帐户

`SeedData`类创建两个帐户： 管理员和管理员。 使用[机密管理器工具](xref:security/app-secrets)设置这些帐户的密码。 从项目目录中设置密码 (目录包含*Program.cs*):

```console
dotnet user-secrets set SeedUserPW <PW>
```

如果未指定强密码，将引发异常时`SeedData.Initialize`调用。

更新`Main`使用测试密码：

[!code-csharp[](secure-data/samples/final2.1/Program.cs?name=snippet)]

### <a name="create-the-test-accounts-and-update-the-contacts"></a>创建测试帐户和更新联系人

更新`Initialize`中的方法`SeedData`类，以创建测试帐户：

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet_Initialize)]

添加管理员用户 ID 和`ContactStatus`到联系人。 先创建一个"已提交"和一个"已拒绝"的联系人。 将用户 ID 和状态添加到所有联系人。 只能有一个联系人所示：

[!code-csharp[](secure-data/samples/final2.1/Data/SeedData.cs?name=snippet1&highlight=17,18)]

## <a name="create-owner-manager-and-administrator-authorization-handlers"></a>创建所有者、 经理和管理员授权处理程序

创建`ContactIsOwnerAuthorizationHandler`类中*授权*文件夹。 `ContactIsOwnerAuthorizationHandler`验证对资源进行操作的用户拥有的资源。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactIsOwnerAuthorizationHandler.cs)]

`ContactIsOwnerAuthorizationHandler`调用[上下文。成功](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.succeed#Microsoft_AspNetCore_Authorization_AuthorizationHandlerContext_Succeed_Microsoft_AspNetCore_Authorization_IAuthorizationRequirement_)当前经过身份验证的用户是否联系所有者。 授权处理程序通常：

* 返回`context.Succeed`满足的要求。
* 返回`Task.CompletedTask`时不符合要求。 `Task.CompletedTask`不是成功或失败&mdash;, 它允许其他授权处理程序运行。

如果你需要将显式失败，返回[上下文。失败](/dotnet/api/microsoft.aspnetcore.authorization.authorizationhandlercontext.fail)。

应用程序允许联系所有者到编辑/删除/创建他们自己的数据。 `ContactIsOwnerAuthorizationHandler` 不需要检查要求参数中传递该操作。

### <a name="create-a-manager-authorization-handler"></a>创建管理器授权处理程序

创建`ContactManagerAuthorizationHandler`类中*授权*文件夹。 `ContactManagerAuthorizationHandler`验证对资源进行操作的用户是管理员。 只有经理们才可以批准或拒绝内容的更改 （新的或已更改）。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactManagerAuthorizationHandler.cs)]

### <a name="create-an-administrator-authorization-handler"></a>创建管理员授权处理程序

创建`ContactAdministratorsAuthorizationHandler`类中*授权*文件夹。 `ContactAdministratorsAuthorizationHandler`验证对资源进行操作的用户是管理员。 管理员可以执行所有操作。

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactAdministratorsAuthorizationHandler.cs)]

## <a name="register-the-authorization-handlers"></a>注册授权处理程序

必须为注册服务使用 Entity Framework Core[依赖关系注入](xref:fundamentals/dependency-injection)使用[AddScoped](/dotnet/api/microsoft.extensions.dependencyinjection.servicecollectionserviceextensions)。           `ContactIsOwnerAuthorizationHandler`使用 ASP.NET Core[标识](xref:security/authentication/identity)，这基于实体框架核心。 注册服务集合的处理程序，以便它们可供`ContactsController`通过[依赖关系注入](xref:fundamentals/dependency-injection)。 将以下代码添加到末尾`ConfigureServices`:

[!code-csharp[](secure-data/samples/final2.1/Startup.cs?name=snippet_defaultPolicy&highlight=27-99)]

`ContactAdministratorsAuthorizationHandler` 和`ContactManagerAuthorizationHandler`添加为单一实例。 它们是单一实例，因为它们不使用 EF 和所需的所有信息都位于`Context`参数的`HandleRequirementAsync`方法。

## <a name="support-authorization"></a>支持授权

在本部分中，将更新 Razor 页面和添加操作要求类。

### <a name="review-the-contact-operations-requirements-class"></a>查看联系人操作要求类

查看`ContactOperations`类。 此类包含要求应用支持：

[!code-csharp[](secure-data/samples/final2.1/Authorization/ContactOperations.cs)]

### <a name="create-a-base-class-for-the-contacts-razor-pages"></a>创建联系人 Razor 页面的基类

创建一个包含在联系人 Razor 页面使用的服务的基类。 基类将初始化代码放在一个位置：

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/DI_BasePageModel.cs)]

前面的代码：

* 添加`IAuthorizationService`服务对授权处理程序的访问权限。
* 将标识添加`UserManager`服务。
* 添加 `ApplicationDbContext`。

### <a name="update-the-createmodel"></a>更新 CreateModel

更新创建页面模型构造函数以使用`DI_BasePageModel`基类：

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippetCtor)]

更新`CreateModel.OnPostAsync`方法：

* 添加到的用户 ID`Contact`模型。
* 调用授权处理程序，以验证用户有权创建联系人。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Create.cshtml.cs?name=snippet_Create)]

### <a name="update-the-indexmodel"></a>更新 IndexModel

更新`OnGetAsync`方法，以便仅被批准的联系人显示为普通用户：

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml.cs?name=snippet)]

### <a name="update-the-editmodel"></a>更新 EditModel

添加授权处理程序以验证的用户拥有联系人。 正在验证资源授权，因为`[Authorize]`属性不能满足。 评估属性时，应用程序不具有对资源的访问。 基于资源的授权必须是命令性。 应用程序页面模型中加载或加载处理程序本身内获得资源的访问权限，则必须执行检查。 您经常访问的资源，通过传入的资源键。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Edit.cshtml.cs?name=snippet)]

### <a name="update-the-deletemodel"></a>更新 DeleteModel

更新要使用授权处理程序来验证用户具有 delete 权限 contact 上的删除页面模型。

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Delete.cshtml.cs?name=snippet)]

## <a name="inject-the-authorization-service-into-the-views"></a>将授权服务注入到视图

目前，此 UI 显示编辑和删除的用户无法修改的联系人的链接。

注入中的授权服务*views/_viewimports.cshtml*文件，以便它可供所有视图：

[!code-cshtml[](secure-data/samples/final2.1/Pages/_ViewImports.cshtml?highlight=6-99)]

上述标记添加了多种`using`语句。

更新**编辑**并**删除**中的链接*Pages/Contacts/Index.cshtml*以便仅在呈现具有适当权限的用户：

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Index.cshtml?highlight=34-36,62-999)]

> [!WARNING]
> 隐藏用户没有权限更改的数据中的链接不会保护应用。 隐藏链接使应用更加友好的用户显示唯一有效的链接。 用户可以 hack 生成的 Url 以调用编辑和删除操作上不拥有的数据。 Razor 页面或控制器必须强制执行访问检查，以保护数据。

### <a name="update-details"></a>更新详细信息

更新的详细信息视图，使管理员可以批准或拒绝联系人：

[!code-cshtml[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml?name=snippet)]

更新详细信息页模型：

[!code-csharp[](secure-data/samples/final2.1/Pages/Contacts/Details.cshtml.cs?name=snippet)]

## <a name="add-or-remove-a-user-to-a-role"></a>添加或删除角色对用户的

请参阅[本期](https://github.com/aspnet/AspNetCore.Docs/issues/8502)有关的信息：

* 从用户删除的特权。 例如, 在聊天应用中对用户进行静音。
* 将权限添加到用户。

## <a name="test-the-completed-app"></a>测试已完成的应用程序

如果你尚未设置设定为种子的用户帐户的密码，使用[机密管理器工具](xref:security/app-secrets#secret-manager)设置密码：

* 选择强密码:使用八个或更多字符, 并且至少使用一个大写字符、数字和符号。 例如，`Passw0rd!`符合强密码要求。
* 执行以下命令从项目的文件夹，其中`<PW>`的密码：

  ```console
  dotnet user-secrets set SeedUserPW <PW>
  ```

* 删除和更新数据库

    ```console
     dotnet ef database drop -f
     dotnet ef database update  
     ```

* 重新启动应用以设定数据库种子。

测试已完成的应用程序的简单方法是启动三个不同的浏览器 （或 incognito/InPrivate 会话）。 在一个浏览器中注册一个新用户 (例如， `test@example.com`)。 登录到每个浏览器使用不同的用户。 验证以下操作：

* 已注册的用户可以查看所有已批准的联系人数据。
* 已注册的用户可以编辑/删除其自己的数据。
* 经理可以批准/拒绝的联系人数据。 `Details`视图视图将显示**批准**并**拒绝**按钮。
* 管理员可以批准/拒绝和编辑/删除所有数据。

| “用户”                | 由应用程序进行种子设定 | 选项                                  |
| ------------------- | :---------------: | ---------------------------------------- |
| test@example.com    | 否                | 编辑/删除自己的数据。                |
| manager@contoso.com | 是               | 批准/拒绝和编辑/删除拥有的数据。 |
| admin@contoso.com   | 是               | 批准/拒绝和编辑/删除所有数据。 |

在管理员的浏览器中创建联系人。 删除 URL 复制并编辑从管理员的联系信息。 将下面的链接粘贴到测试用户的浏览器以验证测试用户不能执行这些操作。

## <a name="create-the-starter-app"></a>创建入门级应用

* 创建名为"ContactManager"Razor 页面应用
  * 创建包含应用**单个用户帐户**。
  * 它命名为"ContactManager"使命名空间匹配的示例中使用的命名空间。
  * `-uld` 指定 LocalDB，而不是 SQLite

  ```console
  dotnet new webapp -o ContactManager -au Individual -uld
  ```

* 添加*模型/联系方式*:

  [!code-csharp[](secure-data/samples/starter2.1/Models/Contact.cs?name=snippet1)]

* 基架`Contact`模型。
* 创建初始迁移并更新数据库：

  ```console
  dotnet aspnet-codegenerator razorpage -m Contact -udl -dc ApplicationDbContext -outDir Pages\Contacts --referenceScriptLibraries
  dotnet ef database drop -f
  dotnet ef migrations add initial
  dotnet ef database update
  ```

* 更新**ContactManager**中的定位点*pages/_layout.cshtml*文件：

  ```cshtml
  <a asp-page="/Contacts/Index" class="navbar-brand">ContactManager</a>
  ```

* 测试应用程序的创建、 编辑和删除联系人

### <a name="seed-the-database"></a>设定数据库种子

添加[SeedData](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/secure-data/samples/starter2.1/Data/SeedData.cs)类来*数据*文件夹。

调用`SeedData.Initialize`从`Main`:

[!code-csharp[](secure-data/samples/starter2.1/Program.cs?name=snippet)]

测试应用程序设定数据库种子。 如果联系人 DB 中有任何行，则不会运行 seed 方法。

::: moniker-end

<a name="secure-data-add-resources-label"></a>

### <a name="additional-resources"></a>其他资源

* [生成 Azure 应用服务中的.NET Core 和 SQL 数据库 web 应用](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)
* [ASP.NET Core 授权实验室](https://github.com/blowdart/AspNetAuthorizationWorkshop)。 此实验室进入的安全功能，本教程中介绍的更多详细信息。
* <xref:security/authorization/introduction>
* [基于自定义策略的授权](xref:security/authorization/policies)
