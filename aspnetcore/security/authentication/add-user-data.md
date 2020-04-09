---
title: 在ASP.NET核心项目中向标识添加、下载和删除用户数据
author: rick-anderson
description: 了解如何在ASP.NET核心项目中向标识添加自定义用户数据。 删除每个 GDPR 的数据。
ms.author: riande
ms.date: 03/26/2020
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 76b83df22381429feab80056c36dbdac1e5f20c7
ms.sourcegitcommit: 1d8f1396ccc66a0c3fcb5e5f36ea29b50db6d92a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501231"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>在 ASP.NET 核心项目中将自定义用户数据添加、下载和删除为标识

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本文介绍以下操作：

* 将自定义用户数据添加到ASP.NET核心 Web 应用。
* 使用<xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute>属性标记自定义用户数据模型，以便它自动可供下载和删除。 使数据能够下载和删除有助于满足[GDPR](xref:security/gdpr)要求。

项目示例是从 Razor Pages Web 应用创建的，但ASP.NET核心 MVC Web 应用的说明类似。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>先决条件

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>创建 Razor Web 应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”************。 如果要命名项目**WebApp1，** 请将其与[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)代码的命名空间匹配。
* 选择**ASP.NET核心 Web 应用程序**>**确定**
* 在下拉列表中**选择ASP.NET核心 3.0**
* 选择**Web 应用程序**>**确定**
* 生成并运行该项目。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”************。 如果要命名项目**WebApp1，** 请将其与[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)代码的命名空间匹配。
* 选择**ASP.NET核心 Web 应用程序**>**确定**
* 在下拉列表中**选择 ASP.NET 核心 2.2**
* 选择**Web 应用程序**>**确定**
* 生成并运行该项目。

::: moniker-end


# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>运行标识基架

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从**解决方案资源管理器**中，右键单击项目>**添加新** > **的脚手架项**。
* 从 **"添加基架"** 对话框的左侧窗格中，选择 **"标识** > **添加**"。
* 在 **"添加标识"** 对话框中，以下选项：
  * 选择现有布局文件 **/页面/共享/_Layout.cshtml*
  * 选择要覆盖的以下文件：
    * **帐户/注册**
    * **帐户/管理/索引**
  * 选择按钮**+** 创建新**的数据上下文类**。 接受类型 **（WebApp1.模型.WebApp1上下文**，如果项目名为**WebApp1**）。
  * 选择按钮**+** 创建新**的用户类**。 接受类型（如果项目名为**WebApp1）>****添加**， 则**接受 WebApp1User。**
* 选择 **添加** 。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果以前未安装ASP.NET核心基架，请立即安装：

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

向[Microsoft.VisualStudio.Web.CodeGeneration.设计](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)添加对 Microsoft 的包引用（.csproj）文件。 在项目目录中运行以下命令：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

运行以下命令以列出标识基架选项：

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

在项目文件夹中，运行标识基架：

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

按照[迁移、使用身份验证和布局](xref:security/authentication/scaffold-identity#efm)中的说明执行以下步骤：

* 创建迁移并更新数据库。
* 已将 `UseAuthentication` 添加到 `Startup.Configure`。
* 添加到`<partial name="_LoginPartial" />`布局文件。
* 测试应用：
  * 注册用户
  * 选择新的用户名（**注销**链接旁边）。 您可能需要展开窗口或选择导航栏图标以显示用户名和其他链接。
  * 选择 **"个人数据**"选项卡。
  * 选择 **"下载**"按钮并检查*个人数据.json*文件。
  * 测试 **"删除**"按钮，该按钮将删除登录的用户。

## <a name="add-custom-user-data-to-the-identity-db"></a>将自定义用户数据添加到标识数据库

使用自定义`IdentityUser`属性更新派生类。 如果命名项目 WebApp1，则文件名为 *"区域/标识/数据/WebApp1User.cs*"。 使用以下代码更新文件：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

具有["个人数据"](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute)属性的属性包括：

* 删除时*的区域/身份/页面/帐户/管理/删除个人数据.cshtml*剃刀页面调用`UserManager.Delete`。
* 包含在下载的数据由*区域/身份/页面/帐户/管理/下载个人数据.cshtml*剃刀页面。

### <a name="update-the-accountmanageindexcshtml-page"></a>更新帐户/管理/索引.cshtml 页面

使用以下`InputModel`突出显示的代码更新*区域/标识/页面/帐户/管理/索引.cshtml.cs：*

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

使用以下突出显示的标记更新*区域/标识/页面/帐户/管理/索引.cshtml：*

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

使用以下突出显示的标记更新*区域/标识/页面/帐户/管理/索引.cshtml：*

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>更新帐户/注册.cshtml 页面

使用以下`InputModel`突出显示的代码更新*区域/标识/页面/帐户/注册.cshtml.cs：*

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

使用以下突出显示的标记更新*区域/标识/页面/帐户/注册.cshtml：*

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

使用以下突出显示的标记更新*区域/标识/页面/帐户/注册.cshtml：*

[!code-cshtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


生成项目。

### <a name="add-a-migration-for-the-custom-user-data"></a>为自定义用户数据添加迁移

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在可视化工作室**包管理器控制台中**：

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>测试创建、查看、下载、删除自定义用户数据

测试应用：

* 注册新用户。
* 查看页面上的`/Identity/Account/Manage`自定义用户数据。
* 从`/Identity/Account/Manage/PersonalData`页面下载并查看用户的个人数据。

## <a name="add-claims-to-identity-using-iuserclaimsprincipalfactoryapplicationuser"></a>使用 IUserClaims 主要工厂向标识添加声明<ApplicationUser>

可以使用`IUserClaimsPrincipalFactory<T>`接口将其他声明添加到ASP.NET核心标识。 类可以添加到`Startup.ConfigureServices`方法中的应用。 将类的自定义实现添加如下：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddIdentity<ApplicationUser, IdentityRole>()
        .AddEntityFrameworkStores<ApplicationDbContext>()
        .AddDefaultTokenProviders();

    services.AddScoped<IUserClaimsPrincipalFactory<ApplicationUser>, 
        AdditionalUserClaimsPrincipalFactory>();
```

演示代码使用类`ApplicationUser`。 此类添加用于`IsAdmin`添加附加声明的属性。

```csharp
public class ApplicationUser : IdentityUser
{
    public bool IsAdmin { get; set; }
}
```

`AdditionalUserClaimsPrincipalFactory` 实现 `UserClaimsPrincipalFactory` 接口。 新角色声明将添加到 。 `ClaimsPrincipal`

```csharp
public class AdditionalUserClaimsPrincipalFactory 
        : UserClaimsPrincipalFactory<ApplicationUser, IdentityRole>
{
    public AdditionalUserClaimsPrincipalFactory( 
        UserManager<ApplicationUser> userManager,
        RoleManager<IdentityRole> roleManager, 
        IOptions<IdentityOptions> optionsAccessor) 
        : base(userManager, roleManager, optionsAccessor)
    {}

    public async override Task<ClaimsPrincipal> CreateAsync(ApplicationUser user)
    {
        var principal = await base.CreateAsync(user);
        var identity = (ClaimsIdentity)principal.Identity;

        var claims = new List<Claim>();
        if (user.IsAdmin)
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "admin"));
        }
        else
        {
            claims.Add(new Claim(JwtClaimTypes.Role, "user"));
        }

        identity.AddClaims(claims);
        return principal;
    }
}
```

然后，可以在应用中使用其他声明。 在 Razor 页中`IAuthorizationService`，实例可用于访问声明值。

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService

@if ((await AuthorizationService.AuthorizeAsync(User, "IsAdmin")).Succeeded)
{
    <ul class="mr-auto navbar-nav">
        <li class="nav-item">
            <a class="nav-link" asp-controller="Admin" asp-action="Index">ADMIN</a>
        </li>
    </ul>
}
```
