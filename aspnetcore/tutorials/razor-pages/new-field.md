---
title: 将新字段添加到 ASP.NET Core 中的 Razor 页面
author: rick-anderson
description: 演示如何使用 Entity Framework Core 将新字段添加到 Razor 页面
ms.author: riande
ms.custom: mvc
ms.date: 12/5/2018
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: f8661a48ddd6fc616c141435edc603117b4925fb
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345881"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="d9b71-103">将新字段添加到 ASP.NET Core 中的 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="d9b71-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="d9b71-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d9b71-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="d9b71-105">在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：</span><span class="sxs-lookup"><span data-stu-id="d9b71-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="d9b71-106">将新字段添加到模型。</span><span class="sxs-lookup"><span data-stu-id="d9b71-106">Add a new field to the model.</span></span>
* <span data-ttu-id="d9b71-107">将新字段架构更改迁移到数据库。</span><span class="sxs-lookup"><span data-stu-id="d9b71-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="d9b71-108">使用 EF Code First 自动创建数据库时，Code First 将：</span><span class="sxs-lookup"><span data-stu-id="d9b71-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="d9b71-109">向数据库添加表格，以跟踪数据库的架构是否与从生成它的模型类同步。</span><span class="sxs-lookup"><span data-stu-id="d9b71-109">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="d9b71-110">如果该模型类未与数据库同步，EF 将引发异常。</span><span class="sxs-lookup"><span data-stu-id="d9b71-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="d9b71-111">通过自动验证同步的架构/模型可以更容易地发现不一致的数据库/代码问题。</span><span class="sxs-lookup"><span data-stu-id="d9b71-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="d9b71-112">向电影模型添加分级属性</span><span class="sxs-lookup"><span data-stu-id="d9b71-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="d9b71-113">打开 Models/Movie.cs 文件，并添加 `Rating` 属性：</span><span class="sxs-lookup"><span data-stu-id="d9b71-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="d9b71-114">构建应用程序。</span><span class="sxs-lookup"><span data-stu-id="d9b71-114">Build the app.</span></span>

<span data-ttu-id="d9b71-115">编辑 Pages/Movies/Index.cshtml，并添加 `Rating` 字段：</span><span class="sxs-lookup"><span data-stu-id="d9b71-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml.?highlight=40-42,61-63)]

<span data-ttu-id="d9b71-116">更新以下页面：</span><span class="sxs-lookup"><span data-stu-id="d9b71-116">Update the following pages:</span></span>

* <span data-ttu-id="d9b71-117">将 `Rating` 字段添加到“删除”和“详细信息”页面。</span><span class="sxs-lookup"><span data-stu-id="d9b71-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="d9b71-118">使用 `Rating` 字段更新 [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="d9b71-118">Update [Create.cshtml](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="d9b71-119">将 `Rating` 字段添加到“编辑”页面。</span><span class="sxs-lookup"><span data-stu-id="d9b71-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="d9b71-120">在 DB 更新为包括新字段之前，应用将不会正常工作。</span><span class="sxs-lookup"><span data-stu-id="d9b71-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="d9b71-121">如果立即运行，应用会引发 `SqlException`：</span><span class="sxs-lookup"><span data-stu-id="d9b71-121">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="d9b71-122">此错误是由于更新的 Movie 模型类与数据库的 Movie 表架构不同导致的。</span><span class="sxs-lookup"><span data-stu-id="d9b71-122">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="d9b71-123">（数据库表中没有 `Rating` 列。）</span><span class="sxs-lookup"><span data-stu-id="d9b71-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="d9b71-124">可通过几种方法解决此错误：</span><span class="sxs-lookup"><span data-stu-id="d9b71-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="d9b71-125">让 Entity Framework 自动丢弃并使用新的模型类架构重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="d9b71-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="d9b71-126">此方法在开发周期早期很方便；通过它可以一起快速改进模型和数据库架构。</span><span class="sxs-lookup"><span data-stu-id="d9b71-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="d9b71-127">此方法的缺点是会导致数据库中的现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="d9b71-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="d9b71-128">请勿对生产数据库使用此方法！</span><span class="sxs-lookup"><span data-stu-id="d9b71-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="d9b71-129">当架构更改时丢弃数据库并使用初始值设定项以使用测试数据自动设定数据库种子，这通常是开发应用的有效方式。</span><span class="sxs-lookup"><span data-stu-id="d9b71-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="d9b71-130">对现有数据库架构进行显式修改，使它与模型类相匹配。</span><span class="sxs-lookup"><span data-stu-id="d9b71-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="d9b71-131">此方法的优点是可以保留数据。</span><span class="sxs-lookup"><span data-stu-id="d9b71-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="d9b71-132">可以手动或通过创建数据库更改脚本进行此更改。</span><span class="sxs-lookup"><span data-stu-id="d9b71-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="d9b71-133">使用 Code First 迁移更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="d9b71-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="d9b71-134">对于本教程，请使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="d9b71-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="d9b71-135">更新 `SeedData` 类，使它提供新列的值。</span><span class="sxs-lookup"><span data-stu-id="d9b71-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="d9b71-136">示例更改如下所示，但可能需要对每个 `new Movie` 块做出此更改。</span><span class="sxs-lookup"><span data-stu-id="d9b71-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="d9b71-137">请参阅[已完成的 SeedData.cs 文件](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)。</span><span class="sxs-lookup"><span data-stu-id="d9b71-137">See the [completed SeedData.cs file](https://github.com/aspnet/Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="d9b71-138">生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="d9b71-138">Build the solution.</span></span>

<!-- VS -------------------------->
# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d9b71-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9b71-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="d9b71-140">添加用于评级字段的迁移</span><span class="sxs-lookup"><span data-stu-id="d9b71-140">Add a migration for the rating field</span></span>

<span data-ttu-id="d9b71-141">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。</span><span class="sxs-lookup"><span data-stu-id="d9b71-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="d9b71-142">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="d9b71-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="d9b71-143">`Add-Migration` 命令会通知框架执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d9b71-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="d9b71-144">将 `Movie` 模型与 `Movie` DB 架构进行比较。</span><span class="sxs-lookup"><span data-stu-id="d9b71-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="d9b71-145">创建代码以将 DB 架构迁移到新模型。</span><span class="sxs-lookup"><span data-stu-id="d9b71-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="d9b71-146">名称“Rating”是任意的，用于对迁移文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="d9b71-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d9b71-147">为迁移文件使用有意义的名称是有帮助的。</span><span class="sxs-lookup"><span data-stu-id="d9b71-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="d9b71-148">`Update-Database` 命令指示框架将架构更改应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="d9b71-148">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="d9b71-149">如果删除 DB 中的所有记录，种子初始值设定项会设定 DB 种子，并将包括 `Rating` 字段。</span><span class="sxs-lookup"><span data-stu-id="d9b71-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="d9b71-150">可以使用浏览器中的删除链接，也可以从 [Sql Server 对象资源管理器](xref:tutorials/razor-pages/sql#ssox) (SSOX) 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="d9b71-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="d9b71-151">另一个方案是删除数据库，并使用迁移来重新创建该数据库。</span><span class="sxs-lookup"><span data-stu-id="d9b71-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d9b71-152">删除 SSOX 中的数据库：</span><span class="sxs-lookup"><span data-stu-id="d9b71-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="d9b71-153">在 SSOX 中选择数据库。</span><span class="sxs-lookup"><span data-stu-id="d9b71-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="d9b71-154">右键单击数据库，并选择“删除”。</span><span class="sxs-lookup"><span data-stu-id="d9b71-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="d9b71-155">检查“关闭现有连接”。</span><span class="sxs-lookup"><span data-stu-id="d9b71-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="d9b71-156">选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="d9b71-156">Select **OK**.</span></span>
* <span data-ttu-id="d9b71-157">在 [PMC](xref:tutorials/razor-pages/new-field#pmc) 中更新数据库：</span><span class="sxs-lookup"><span data-stu-id="d9b71-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

<!-- Code -------------------------->
# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d9b71-158">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d9b71-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<!-- copy/paste this tab to the next. Not worth an include  -->

<span data-ttu-id="d9b71-159">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="d9b71-159">Run the following .NET Core CLI commands:</span></span>

```console
dotnet ef migrations add Rating
dotnet ef database update
```

<span data-ttu-id="d9b71-160">`ef migrations add` 命令会通知框架执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d9b71-160">The `ef migrations add` command tells the framework to:</span></span>

* <span data-ttu-id="d9b71-161">将 `Movie` 模型与 `Movie` DB 架构进行比较。</span><span class="sxs-lookup"><span data-stu-id="d9b71-161">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="d9b71-162">创建代码以将 DB 架构迁移到新模型。</span><span class="sxs-lookup"><span data-stu-id="d9b71-162">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="d9b71-163">名称“Rating”是任意的，用于对迁移文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="d9b71-163">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d9b71-164">为迁移文件使用有意义的名称是有帮助的。</span><span class="sxs-lookup"><span data-stu-id="d9b71-164">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="d9b71-165">`ef database update` 命令指示框架将架构更改应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="d9b71-165">The `ef database update` command tells the framework to apply the schema changes to the database.</span></span>

<span data-ttu-id="d9b71-166">如果删除 DB 中的所有记录，种子初始值设定项会设定 DB 种子，并将包括 `Rating` 字段。</span><span class="sxs-lookup"><span data-stu-id="d9b71-166">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="d9b71-167">可以使用浏览器中的删除链接，也可以使用 SQLite 工具执行此操作。</span><span class="sxs-lookup"><span data-stu-id="d9b71-167">You can do this with the delete links in the browser or by using a SQLite tool.</span></span>

<span data-ttu-id="d9b71-168">另一个方案是删除数据库，并使用迁移来重新创建该数据库。</span><span class="sxs-lookup"><span data-stu-id="d9b71-168">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d9b71-169">若要删除该数据库，请删除数据库文件 (MvcMovie.db)。</span><span class="sxs-lookup"><span data-stu-id="d9b71-169">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="d9b71-170">然后运行 `ef database update` 命令：</span><span class="sxs-lookup"><span data-stu-id="d9b71-170">Then run the `ef database update` command:</span></span> 

```console
dotnet ef database update
```

> [!NOTE]
> <span data-ttu-id="d9b71-171">许多架构更改操作不受 EF Core SQLite 提供程序支持。</span><span class="sxs-lookup"><span data-stu-id="d9b71-171">Many schema change operations are not supported by the EF Core SQLite provider.</span></span> <span data-ttu-id="d9b71-172">例如，支持添加列，但不支持删除列。</span><span class="sxs-lookup"><span data-stu-id="d9b71-172">For example, adding a column is supported, but removing a column is not supported.</span></span> <span data-ttu-id="d9b71-173">如果添加迁移以删除列，则 `ef migrations add` 命令成功，而 `ef database update` 命令失败。</span><span class="sxs-lookup"><span data-stu-id="d9b71-173">If you add a migration to remove a column, the `ef migrations add` command succeeds but the `ef database update` command fails.</span></span> <span data-ttu-id="d9b71-174">可以通过手动编写迁移代码来重新生成表，从而解决部分限制。</span><span class="sxs-lookup"><span data-stu-id="d9b71-174">You can work around some of the limitations by manually writing migrations code to perform a table rebuild.</span></span> <span data-ttu-id="d9b71-175">表重新生成包括重命名现有表、创建新表、将数据复制到新表和删除旧表。</span><span class="sxs-lookup"><span data-stu-id="d9b71-175">A table rebuild involves renaming the existing table, creating a new table, copying data to the new table, and dropping the old table.</span></span> <span data-ttu-id="d9b71-176">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="d9b71-176">For more information, see the following resources:</span></span>
> * [<span data-ttu-id="d9b71-177">SQLite EF Core 数据库提供程序限制</span><span class="sxs-lookup"><span data-stu-id="d9b71-177">SQLite EF Core Database Provider Limitations</span></span>](/ef/core/providers/sqlite/limitations)
> * [<span data-ttu-id="d9b71-178">自定义迁移代码</span><span class="sxs-lookup"><span data-stu-id="d9b71-178">Customize migration code</span></span>](/ef/core/managing-schemas/migrations/#customize-migration-code)
> * [<span data-ttu-id="d9b71-179">数据种子设定</span><span class="sxs-lookup"><span data-stu-id="d9b71-179">Data seeding</span></span>](/ef/core/modeling/data-seeding)

---  
<!-- End of VS tabs -->

<span data-ttu-id="d9b71-180">运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。</span><span class="sxs-lookup"><span data-stu-id="d9b71-180">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="d9b71-181">如果数据库未设定种子，则在 `SeedData.Initialize` 方法中设置断点。</span><span class="sxs-lookup"><span data-stu-id="d9b71-181">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d9b71-182">其他资源</span><span class="sxs-lookup"><span data-stu-id="d9b71-182">Additional resources</span></span>

* [<span data-ttu-id="d9b71-183">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="d9b71-183">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="d9b71-184">[上一篇：添加搜索](xref:tutorials/razor-pages/search)
> [下一篇：添加验证](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="d9b71-184">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>
