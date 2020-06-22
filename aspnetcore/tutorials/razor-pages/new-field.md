---
title: 第 7 部分，将新字段添加到 ASP.NET Core 中的 Razor 页面
author: rick-anderson
description: Razor 页面教程系列第 7 部分。
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: 15d4ccbe88c2147210918a3db1416983fb30132b
ms.sourcegitcommit: fa67462abdf0cc4051977d40605183c629db7c64
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2020
ms.locfileid: "84652797"
---
# <a name="part-7-add-a-new-field-to-a-razor-page-in-aspnet-core"></a>第 7 部分，将新字段添加到 ASP.NET Core 中的 Razor 页面

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：

* 将新字段添加到模型。
* 将新字段架构更改迁移到数据库。

使用 EF Code First 自动创建数据库时，Code First 将：

* 向数据库添加 `__EFMigrationsHistory` 表格，以跟踪数据库的架构是否与从生成它的模型类同步。
* 如果该模型类未与数据库同步，EF 将引发异常。

通过自动验证同步的架构/模型可以更容易地发现不一致的数据库/代码问题。

## <a name="adding-a-rating-property-to-the-movie-model"></a>向电影模型添加分级属性

打开 Models/Movie.cs 文件，并添加 `Rating` 属性：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

构建应用程序。

编辑 Pages/Movies/Index.cshtml，并添加 `Rating` 字段：

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

更新以下页面：

* 将 `Rating` 字段添加到“删除”和“详细信息”页面。
* 使用 `Rating` 字段更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml)。
* 将 `Rating` 字段添加到“编辑”页面。

在 DB 更新为包括新字段之前，应用将不会正常工作。 在不更新数据库的情况下运行应用会引发 `SqlException`：

`SqlException: Invalid column name 'Rating'.`

`SqlException` 异常是由于更新的 Movie 模型类与数据库的 Movie 表架构不同导致的。 （数据库表中没有 `Rating` 列。）

可通过几种方法解决此错误：

1. 让 Entity Framework 自动丢弃并使用新的模型类架构重新创建数据库。 此方法在开发周期早期很方便；通过它可以一起快速改进模型和数据库架构。 此方法的缺点是会导致数据库中的现有数据丢失。 请勿对生产数据库使用此方法！ 当架构更改时丢弃数据库并使用初始值设定项以使用测试数据自动设定数据库种子，这通常是开发应用的有效方式。

2. 对现有数据库架构进行显式修改，使它与模型类相匹配。 此方法的优点是可以保留数据。 可以手动或通过创建数据库更改脚本进行此更改。

3. 使用 Code First 迁移更新数据库架构。

对于本教程，请使用 Code First 迁移。

更新 `SeedData` 类，使它提供新列的值。 示例更改如下所示，但可能需要对每个 `new Movie` 块做出此更改。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

请参阅[已完成的 SeedData.cs 文件](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs)。

生成解决方案。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>添加用于评级字段的迁移

从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。 
在 PMC 中，输入以下命令：

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` 命令会通知框架执行以下操作：

* 将 `Movie` 模型与 `Movie` DB 架构进行比较。
* 创建代码以将 DB 架构迁移到新模型。

名称“Rating”是任意的，用于对迁移文件进行命名。 为迁移文件使用有意义的名称是有帮助的。

`Update-Database` 命令指示框架将架构更改应用到数据库并保留现有数据。

<a name="ssox"></a>

如果删除 DB 中的所有记录，种子初始值设定项会设定 DB 种子，并将包括 `Rating` 字段。 可以使用浏览器中的删除链接，也可以从 [Sql Server 对象资源管理器](xref:tutorials/razor-pages/sql#ssox) (SSOX) 执行此操作。

另一个方案是删除数据库，并使用迁移来重新创建该数据库。 删除 SSOX 中的数据库：

* 在 SSOX 中选择数据库。
* 右键单击数据库，并选择“删除”。
* 检查“关闭现有连接”。
* 选择“确定”。
* 在 [PMC](xref:tutorials/razor-pages/new-field#pmc) 中更新数据库：

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>删除并重新创建数据库

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

删除迁移文件夹。  使用以下命令重新创建数据库。

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。 如果数据库未设定种子，则在 `SeedData.Initialize` 方法中设置断点。

## <a name="additional-resources"></a>其他资源

* [本教程的 YouTube 版本](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [上一篇：添加搜索](xref:tutorials/razor-pages/search)
> [下一篇：添加验证](xref:tutorials/razor-pages/validation)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：

* 将新字段添加到模型。
* 将新字段架构更改迁移到数据库。

使用 EF Code First 自动创建数据库时，Code First 将：

* 向数据库添加表格，以跟踪数据库的架构是否与从生成它的模型类同步。
* 如果该模型类未与数据库同步，EF 将引发异常。

通过自动验证同步的架构/模型可以更容易地发现不一致的数据库/代码问题。

## <a name="adding-a-rating-property-to-the-movie-model"></a>向电影模型添加分级属性

打开 Models/Movie.cs 文件，并添加 `Rating` 属性：

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

构建应用程序。

编辑 Pages/Movies/Index.cshtml，并添加 `Rating` 字段：

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

更新以下页面：

* 将 `Rating` 字段添加到“删除”和“详细信息”页面。
* 使用 `Rating` 字段更新 [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)。
* 将 `Rating` 字段添加到“编辑”页面。

在 DB 更新为包括新字段之前，应用将不会正常工作。 如果立即运行，应用会引发 `SqlException`：

`SqlException: Invalid column name 'Rating'.`

此错误是由于更新的 Movie 模型类与数据库的 Movie 表架构不同导致的。 （数据库表中没有 `Rating` 列。）

可通过几种方法解决此错误：

1. 让 Entity Framework 自动丢弃并使用新的模型类架构重新创建数据库。 此方法在开发周期早期很方便；通过它可以一起快速改进模型和数据库架构。 此方法的缺点是会导致数据库中的现有数据丢失。 请勿对生产数据库使用此方法！ 当架构更改时丢弃数据库并使用初始值设定项以使用测试数据自动设定数据库种子，这通常是开发应用的有效方式。

2. 对现有数据库架构进行显式修改，使它与模型类相匹配。 此方法的优点是可以保留数据。 可以手动或通过创建数据库更改脚本进行此更改。

3. 使用 Code First 迁移更新数据库架构。

对于本教程，请使用 Code First 迁移。

更新 `SeedData` 类，使它提供新列的值。 示例更改如下所示，但可能需要对每个 `new Movie` 块做出此更改。

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

请参阅[已完成的 SeedData.cs 文件](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)。

生成解决方案。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a>添加用于评级字段的迁移

从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。 
在 PMC 中，输入以下命令：

```powershell
Add-Migration Rating
Update-Database
```

`Add-Migration` 命令会通知框架执行以下操作：

* 将 `Movie` 模型与 `Movie` DB 架构进行比较。
* 创建代码以将 DB 架构迁移到新模型。

名称“Rating”是任意的，用于对迁移文件进行命名。 为迁移文件使用有意义的名称是有帮助的。

`Update-Database` 命令指示框架将架构更改应用到数据库。

<a name="ssox"></a>

如果删除 DB 中的所有记录，种子初始值设定项会设定 DB 种子，并将包括 `Rating` 字段。 可以使用浏览器中的删除链接，也可以从 [Sql Server 对象资源管理器](xref:tutorials/razor-pages/sql#ssox) (SSOX) 执行此操作。

另一个方案是删除数据库，并使用迁移来重新创建该数据库。 删除 SSOX 中的数据库：

* 在 SSOX 中选择数据库。
* 右键单击数据库，并选择“删除”。
* 检查“关闭现有连接”。
* 选择“确定”。
* 在 [PMC](xref:tutorials/razor-pages/new-field#pmc) 中更新数据库：

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[Visual Studio Code / Visual Studio for Mac](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a>删除并重新创建数据库

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

删除数据库并通过迁移重新创建数据库。 若要删除该数据库，请删除数据库文件 (MvcMovie.db)。 然后运行 `ef database update` 命令：

```dotnetcli
dotnet ef database update
```

---

运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。 如果数据库未设定种子，则在 `SeedData.Initialize` 方法中设置断点。

## <a name="additional-resources"></a>其他资源

* [本教程的 YouTube 版本](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> [上一篇：添加搜索](xref:tutorials/razor-pages/search)
> [下一篇：添加验证](xref:tutorials/razor-pages/validation)

::: moniker-end
