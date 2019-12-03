---
title: 向 ASP.NET Core 项目中的标识添加、下载和删除用户数据
author: rick-anderson
description: 了解如何将自定义用户数据添加到 ASP.NET Core 项目中的标识。 删除每个 GDPR 的数据。
ms.author: riande
ms.date: 06/18/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: 6daca5776930f80eec8d81132b5a5c4d4d5c13ad
ms.sourcegitcommit: 0dd224b2b7efca1fda0041b5c3f45080327033f6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74681157"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>向 ASP.NET Core 项目中的标识添加、下载和删除自定义用户数据

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本文介绍如何执行以下操作：

* 向 ASP.NET Core web 应用添加自定义用户数据。
* 使用 <xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute> 特性修饰自定义用户数据模型，使其自动可供下载和删除。 使数据能够下载和删除有助于满足[GDPR](xref:security/gdpr)要求。

此项目示例是从 Razor Pages web 应用创建的，但是 ASP.NET Core MVC web 应用的说明类似。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>先决条件

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [](~/includes/3.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [](~/includes/2.2-SDK.md)]

::: moniker-end

## <a name="create-a-razor-web-app"></a>创建 Razor Web 应用

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

::: moniker range=">= aspnetcore-3.0"

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”。 将项目命名为 " **WebApp1** " （如果你希望它与[下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)代码的命名空间相匹配）。
* 选择**ASP.NET Core Web 应用程序**>**确定**
* 在下拉列表中选择**ASP.NET Core 3.0**
* 选择**Web 应用程序**>**确定**
* 生成并运行该项目。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”。 将项目命名为 " **WebApp1** " （如果你希望它与[下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)代码的命名空间相匹配）。
* 选择**ASP.NET Core Web 应用程序**>**确定**
* 在下拉列表中选择**ASP.NET Core 2.2**
* 选择**Web 应用程序**>**确定**
* 生成并运行该项目。

::: moniker-end


# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>运行标识 scaffolder

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在**解决方案资源管理器**中，右键单击项目 >**添加** > 新的**基架项**。
* 在 "**添加基架**" 对话框的左窗格中，选择 "**标识**" > "**添加**"。
* 在 "**添加标识**" 对话框中，选择以下选项：
  * 选择现有的布局文件 *~/Pages/Shared/_Layout cshtml*
  * 选择以下要重写的文件：
    * **帐户/注册**
    * **帐户/管理/索引**
  * 选择 " **+** " 按钮以创建新的**数据上下文类**。 如果项目命名为**WebApp1**，则接受类型（**WebApp1. WebApp1Context。**
  * 选择 " **+** " 按钮以创建新的**用户类**。 接受类型（如果项目命名为 " **WebApp1**"，则为**WebApp1User** ） > "**添加**"。
* 选择 "**添加**"。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果你之前未安装 ASP.NET Core scaffolder，请立即安装：

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

将对[VisualStudio](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)的包引用添加到项目（.csproj）文件中。 在项目目录中运行以下命令：

```dotnetcli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

运行以下命令以列出 Identity scaffolder 选项：

```dotnetcli
dotnet aspnet-codegenerator identity -h
```

在项目文件夹中，运行标识 scaffolder：

```dotnetcli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

按照 "[迁移"、"UseAuthentication" 和 "布局](xref:security/authentication/scaffold-identity#efm)" 中的说明执行以下步骤：

* 创建迁移并更新数据库。
* 将 `UseAuthentication` 添加到 `Startup.Configure`。
* 将 `<partial name="_LoginPartial" />` 添加到布局文件中。
* 测试应用：
  * 注册用户
  * 选择新用户名（"**注销**" 链接旁边）。 可能需要展开窗口或选择导航栏图标来显示用户名和其他链接。
  * 选择 "**个人数据**" 选项卡。
  * 选择 "**下载**" 按钮，然后检查*PersonalData*文件。
  * 测试**删除**按钮，该按钮将删除已登录的用户。

## <a name="add-custom-user-data-to-the-identity-db"></a>向标识数据库添加自定义用户数据

用自定义属性更新 `IdentityUser` 派生类。 如果已将项目命名为 WebApp1，则该文件的名称为*Areas/Identity/Data/WebApp1User*。 用以下代码更新文件：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Data/WebApp1User.cs)]

::: moniker-end

用[PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute)特性修饰的属性包括：

* 当 "*区域/标识/页面/帐户/管理/DeletePersonalData* " Razor 页面调用 `UserManager.Delete`时删除。
* 按*区域/标识/页面/帐户/管理/DownloadPersonalData* Razor 页面包含在下载的数据中。

### <a name="update-the-accountmanageindexcshtml-page"></a>更新 "帐户/管理/索引" 页

用以下突出显示的代码更新*区域/标识/页/帐户/管理/* `InputModel` 中的：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=24-32,48-49,96-104,106)]

用以下突出显示的标记更新*区域/标识/页/帐户/管理/索引。 cshtml* ：

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=18-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

用以下突出显示的标记更新*区域/标识/页/帐户/管理/索引。 cshtml* ：

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

::: moniker-end

### <a name="update-the-accountregistercshtml-page"></a>更新帐户/注册. cshtml 页

用以下突出显示的代码更新*区域/标识/页/帐户/注册. .cs*中的 `InputModel`：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=30-38,70-71)]

用以下突出显示的标记更新*区域/标识/页/帐户/注册. cshtml* ：

[!code-cshtml[](add-user-data/samples/3.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

用以下突出显示的标记更新*区域/标识/页/帐户/注册. cshtml* ：

[!code-chtml[](add-user-data/samples/2.x/SampleApp/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

::: moniker-end


生成此项目。

### <a name="add-a-migration-for-the-custom-user-data"></a>添加自定义用户数据的迁移

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

在 Visual Studio**包管理器控制台**中：

```powershell
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```dotnetcli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>测试创建、查看、下载、删除自定义用户数据

测试应用：

* 注册新用户。
* 查看 `/Identity/Account/Manage` 页上的自定义用户数据。
* 从 "`/Identity/Account/Manage/PersonalData`" 页下载并查看用户个人数据。
