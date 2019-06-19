---
title: 添加、 下载和删除标识到 ASP.NET Core 项目中的用户数据
author: rick-anderson
description: 了解如何在 ASP.NET Core 项目中添加到标识的自定义用户数据。 删除每个 GDPR 的数据。
ms.author: riande
ms.date: 06/18/2019
ms.custom: mvc, seodec18
uid: security/authentication/add-user-data
ms.openlocfilehash: c219500b7595fd8d200e4e5e742b1e1fda836ba3
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207737"
---
# <a name="add-download-and-delete-custom-user-data-to-identity-in-an-aspnet-core-project"></a>添加、 下载和删除标识到 ASP.NET Core 项目中的自定义用户数据

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本文介绍如何：

* 将自定义用户数据添加到 ASP.NET Core web 应用程序。
* 修饰自定义用户数据模型的<xref:Microsoft.AspNetCore.Identity.PersonalDataAttribute>属性是自动可供下载和删除。 使能够下载和删除数据可帮助满足[GDPR](xref:security/gdpr)要求。

项目示例将创建从 Razor 页 web 应用，但了 ASP.NET Core MVC web 应用的类似的说明。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authentication/add-user-data)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>系统必备

[!INCLUDE [](~/includes/2.2-SDK.md)]

## <a name="create-a-razor-web-app"></a>创建 Razor Web 应用

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”    。 将项目命名**WebApp1**如果你想与其匹配的命名空间[下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/authentication/add-user-data)代码。
* 选择**ASP.NET Core Web 应用程序** > **确定**
* 选择**ASP.NET Core 2.2**下拉列表中
* 选择**Web 应用程序**  > **确定**
* 生成并运行该项目。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```cli
dotnet new webapp -o WebApp1
```

---

## <a name="run-the-identity-scaffolder"></a>运行标识基架

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从**解决方案资源管理器**，右键单击该项目 >**添加** > **新基架项**。
* 从左窗格**添加基架**对话框中，选择**标识** > **添加**。
* 在中**ADD 标识添加**对话框中，以下选项：
  * 选择现有的布局文件 *~/Pages/Shared/_Layout.cshtml*
  * 选择要重写的以下文件：
    * **帐户/注册**
    * **帐户/管理/索引**
  * 选择 **+** 按钮以创建一个新**数据上下文类**。 接受的类型 (**WebApp1.Models.WebApp1Context**如果项目命名为**WebApp1**)。
  * 选择 **+** 按钮以创建一个新**User 类**。 接受的类型 (**WebApp1User**如果项目命名为**WebApp1**) >**添加**。
* 选择**添加**。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

如果以前未安装 ASP.NET Core 基架，请立即进行安装：

```cli
dotnet tool install -g dotnet-aspnet-codegenerator
```

添加到包引用[Microsoft.VisualStudio.Web.CodeGeneration.Design](https://www.nuget.org/packages/Microsoft.VisualStudio.Web.CodeGeneration.Design/)项目 (.csproj) 文件。 在项目目录中运行以下命令：

```cli
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
dotnet restore
```

运行以下命令以列出标识基架选项：

```cli
dotnet aspnet-codegenerator identity -h
```

在项目文件夹中，运行标识基架：

```cli
dotnet aspnet-codegenerator identity -u WebApp1User -fi Account.Register;Account.Manage.Index
```

---

按照中的说明[迁移、 UseAuthentication 和布局](xref:security/authentication/scaffold-identity#efm)来执行以下步骤：

* 创建迁移并更新数据库。
* 将 `UseAuthentication` 添加到 `Startup.Configure`。
* 添加`<partial name="_LoginPartial" />`布局文件。
* 测试应用：
  * 注册用户
  * 选择新的用户名称 (旁边**注销**链接)。 您可能需要展开窗口或选择要显示的用户名称和其他链接的导航栏图标。
  * 选择**个人数据**选项卡。
  * 选择**下载**按钮，然后检查*PersonalData.json*文件。
  * 测试**删除**按钮，删除已登录用户。

## <a name="add-custom-user-data-to-the-identity-db"></a>向标识数据库中添加自定义用户数据

更新`IdentityUser`派生类使用自定义属性。 如果项目 WebApp1 命名为，将该文件命名*Areas/Identity/Data/WebApp1User.cs*。 使用以下代码更新文件：

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Data/WebApp1User.cs)]

使用属性修饰[PersonalData](/dotnet/api/microsoft.aspnetcore.identity.personaldataattribute?view=aspnetcore-2.1)特性是：

* 时删除*Areas/Identity/Pages/Account/Manage/DeletePersonalData.cshtml* Razor 页面调用`UserManager.Delete`。
* 通过下载的数据中包含*Areas/Identity/Pages/Account/Manage/DownloadPersonalData.cshtml* Razor 页面。

### <a name="update-the-accountmanageindexcshtml-page"></a>更新 Account/Manage/Index.cshtml 页

更新`InputModel`中*Areas/Identity/Pages/Account/Manage/Index.cshtml.cs*用以下突出显示的代码：

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Manage/Index.cshtml.cs?name=snippet&highlight=28-36,63-64,98-106,119)]

更新*Areas/Identity/Pages/Account/Manage/Index.cshtml*与以下突出显示的标记：

[!code-html[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Manage/Index.cshtml?highlight=35-42)]

### <a name="update-the-accountregistercshtml-page"></a>更新 account/Register.cshtml 页面

更新`InputModel`中*Areas/Identity/Pages/Account/Register.cshtml.cs*用以下突出显示的代码：

[!code-csharp[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Register.cshtml.cs?name=snippet&highlight=28-36,67,66)]

更新*Areas/Identity/Pages/Account/Register.cshtml*与以下突出显示的标记：

[!code-html[Main](add-user-data/sample-2.2/Areas/Identity/Pages/Account/Register.cshtml?highlight=16-25)]

生成项目。

### <a name="add-a-migration-for-the-custom-user-data"></a>添加自定义用户数据的迁移

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

在 Visual Studio**程序包管理器控制台**:

```PMC
Add-Migration CustomUserData
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```cli
dotnet ef migrations add CustomUserData
dotnet ef database update
```

---

## <a name="test-create-view-download-delete-custom-user-data"></a>测试创建、 查看、 下载和删除自定义用户数据

测试应用：

* 注册一个新用户。
* 查看自定义用户数据`/Identity/Account/Manage`页。
* 下载并查看用户个人数据从`/Identity/Account/Manage/PersonalData`页。
