---
title: 将新字段添加到 ASP.NET Core 中的 Razor 页面
author: rick-anderson
description: 演示如何使用 Entity Framework Core 将新字段添加到 Razor 页面
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/new-field
ms.openlocfilehash: d34b938dbd1b512ddb167cac0c035837889cd38f
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78646602"
---
# <a name="add-a-new-field-to-a-razor-page-in-aspnet-core"></a><span data-ttu-id="5776a-103">将新字段添加到 ASP.NET Core 中的 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="5776a-103">Add a new field to a Razor Page in ASP.NET Core</span></span>

<span data-ttu-id="5776a-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5776a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="5776a-105">在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：</span><span class="sxs-lookup"><span data-stu-id="5776a-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="5776a-106">将新字段添加到模型。</span><span class="sxs-lookup"><span data-stu-id="5776a-106">Add a new field to the model.</span></span>
* <span data-ttu-id="5776a-107">将新字段架构更改迁移到数据库。</span><span class="sxs-lookup"><span data-stu-id="5776a-107">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="5776a-108">使用 EF Code First 自动创建数据库时，Code First 将：</span><span class="sxs-lookup"><span data-stu-id="5776a-108">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="5776a-109">向数据库添加 `__EFMigrationsHistory` 表格，以跟踪数据库的架构是否与从生成它的模型类同步。</span><span class="sxs-lookup"><span data-stu-id="5776a-109">Adds an `__EFMigrationsHistory` table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="5776a-110">如果该模型类未与数据库同步，EF 将引发异常。</span><span class="sxs-lookup"><span data-stu-id="5776a-110">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="5776a-111">通过自动验证同步的架构/模型可以更容易地发现不一致的数据库/代码问题。</span><span class="sxs-lookup"><span data-stu-id="5776a-111">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="5776a-112">向电影模型添加分级属性</span><span class="sxs-lookup"><span data-stu-id="5776a-112">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="5776a-113">打开 Models/Movie.cs 文件，并添加  *属性：* `Rating`</span><span class="sxs-lookup"><span data-stu-id="5776a-113">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="5776a-114">构建应用程序。</span><span class="sxs-lookup"><span data-stu-id="5776a-114">Build the app.</span></span>

<span data-ttu-id="5776a-115">编辑 Pages/Movies/Index.cshtml，并添加  *字段：* `Rating`</span><span class="sxs-lookup"><span data-stu-id="5776a-115">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

<a name="addrat"></a>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie30/SnapShots/IndexRating.cshtml?highlight=40-42,62-64)]

<span data-ttu-id="5776a-116">更新以下页面：</span><span class="sxs-lookup"><span data-stu-id="5776a-116">Update the following pages:</span></span>

* <span data-ttu-id="5776a-117">将 `Rating` 字段添加到“删除”和“详细信息”页面。</span><span class="sxs-lookup"><span data-stu-id="5776a-117">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="5776a-118">使用 [ 字段更新 ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml)Create.cshtml`Rating`。</span><span class="sxs-lookup"><span data-stu-id="5776a-118">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="5776a-119">将 `Rating` 字段添加到“编辑”页面。</span><span class="sxs-lookup"><span data-stu-id="5776a-119">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="5776a-120">在 DB 更新为包括新字段之前，应用将不会正常工作。</span><span class="sxs-lookup"><span data-stu-id="5776a-120">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="5776a-121">在不更新数据库的情况下运行应用会引发 `SqlException`：</span><span class="sxs-lookup"><span data-stu-id="5776a-121">Running the app without updating the database throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="5776a-122">`SqlException` 异常是由于更新的 Movie 模型类与数据库的 Movie 表架构不同导致的。</span><span class="sxs-lookup"><span data-stu-id="5776a-122">The `SqlException` exception is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="5776a-123">（数据库表中没有 `Rating` 列。）</span><span class="sxs-lookup"><span data-stu-id="5776a-123">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="5776a-124">可通过几种方法解决此错误：</span><span class="sxs-lookup"><span data-stu-id="5776a-124">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="5776a-125">让 Entity Framework 自动丢弃并使用新的模型类架构重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="5776a-125">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="5776a-126">此方法在开发周期早期很方便；通过它可以一起快速改进模型和数据库架构。</span><span class="sxs-lookup"><span data-stu-id="5776a-126">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="5776a-127">此方法的缺点是会导致数据库中的现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="5776a-127">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="5776a-128">请勿对生产数据库使用此方法！</span><span class="sxs-lookup"><span data-stu-id="5776a-128">Don't use this approach on a production database!</span></span> <span data-ttu-id="5776a-129">当架构更改时丢弃数据库并使用初始值设定项以使用测试数据自动设定数据库种子，这通常是开发应用的有效方式。</span><span class="sxs-lookup"><span data-stu-id="5776a-129">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="5776a-130">对现有数据库架构进行显式修改，使它与模型类相匹配。</span><span class="sxs-lookup"><span data-stu-id="5776a-130">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="5776a-131">此方法的优点是可以保留数据。</span><span class="sxs-lookup"><span data-stu-id="5776a-131">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="5776a-132">可以手动或通过创建数据库更改脚本进行此更改。</span><span class="sxs-lookup"><span data-stu-id="5776a-132">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="5776a-133">使用 Code First 迁移更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="5776a-133">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="5776a-134">对于本教程，请使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="5776a-134">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="5776a-135">更新 `SeedData` 类，使它提供新列的值。</span><span class="sxs-lookup"><span data-stu-id="5776a-135">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="5776a-136">示例更改如下所示，但可能需要对每个 `new Movie` 块做出此更改。</span><span class="sxs-lookup"><span data-stu-id="5776a-136">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="5776a-137">请参阅[已完成的 SeedData.cs 文件](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs)。</span><span class="sxs-lookup"><span data-stu-id="5776a-137">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="5776a-138">生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="5776a-138">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5776a-139">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5776a-139">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="5776a-140">添加用于评级字段的迁移</span><span class="sxs-lookup"><span data-stu-id="5776a-140">Add a migration for the rating field</span></span>

<span data-ttu-id="5776a-141">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。  </span><span class="sxs-lookup"><span data-stu-id="5776a-141">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="5776a-142">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="5776a-142">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="5776a-143">`Add-Migration` 命令会通知框架执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5776a-143">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="5776a-144">将 `Movie` 模型与 `Movie` DB 架构进行比较。</span><span class="sxs-lookup"><span data-stu-id="5776a-144">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="5776a-145">创建代码以将 DB 架构迁移到新模型。</span><span class="sxs-lookup"><span data-stu-id="5776a-145">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="5776a-146">名称“Rating”是任意的，用于对迁移文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="5776a-146">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="5776a-147">为迁移文件使用有意义的名称是有帮助的。</span><span class="sxs-lookup"><span data-stu-id="5776a-147">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="5776a-148">`Update-Database` 命令指示框架将架构更改应用到数据库并保留现有数据。</span><span class="sxs-lookup"><span data-stu-id="5776a-148">The `Update-Database` command tells the framework to apply the schema changes to the database and to preserve existing data.</span></span>

<a name="ssox"></a>

<span data-ttu-id="5776a-149">如果删除 DB 中的所有记录，种子初始值设定项会设定 DB 种子，并将包括 `Rating` 字段。</span><span class="sxs-lookup"><span data-stu-id="5776a-149">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="5776a-150">可以使用浏览器中的删除链接，也可以从 [Sql Server 对象资源管理器](xref:tutorials/razor-pages/sql#ssox) (SSOX) 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="5776a-150">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="5776a-151">另一个方案是删除数据库，并使用迁移来重新创建该数据库。</span><span class="sxs-lookup"><span data-stu-id="5776a-151">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="5776a-152">删除 SSOX 中的数据库：</span><span class="sxs-lookup"><span data-stu-id="5776a-152">To delete the database in SSOX:</span></span>

* <span data-ttu-id="5776a-153">在 SSOX 中选择数据库。</span><span class="sxs-lookup"><span data-stu-id="5776a-153">Select the database in SSOX.</span></span>
* <span data-ttu-id="5776a-154">右键单击数据库，并选择“删除”。 </span><span class="sxs-lookup"><span data-stu-id="5776a-154">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="5776a-155">检查“关闭现有连接”  。</span><span class="sxs-lookup"><span data-stu-id="5776a-155">Check **Close existing connections**.</span></span>
* <span data-ttu-id="5776a-156">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="5776a-156">Select **OK**.</span></span>
* <span data-ttu-id="5776a-157">在 [PMC](xref:tutorials/razor-pages/new-field#pmc) 中更新数据库：</span><span class="sxs-lookup"><span data-stu-id="5776a-157">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5776a-158">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5776a-158">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="5776a-159">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="5776a-159">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="5776a-160">删除迁移文件夹。</span><span class="sxs-lookup"><span data-stu-id="5776a-160">Delete the migration folder.</span></span>  <span data-ttu-id="5776a-161">使用以下命令重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="5776a-161">Use the following commands to recreate the database.</span></span>

```dotnetcli
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

<span data-ttu-id="5776a-162">运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。</span><span class="sxs-lookup"><span data-stu-id="5776a-162">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="5776a-163">如果数据库未设定种子，则在 `SeedData.Initialize` 方法中设置断点。</span><span class="sxs-lookup"><span data-stu-id="5776a-163">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5776a-164">其他资源</span><span class="sxs-lookup"><span data-stu-id="5776a-164">Additional resources</span></span>

* [<span data-ttu-id="5776a-165">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="5776a-165">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="5776a-166">[上一篇：添加搜索](xref:tutorials/razor-pages/search)
> [下一篇：添加验证](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="5776a-166">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE[](~/includes/rp/download.md)]

<span data-ttu-id="5776a-167">在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：</span><span class="sxs-lookup"><span data-stu-id="5776a-167">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="5776a-168">将新字段添加到模型。</span><span class="sxs-lookup"><span data-stu-id="5776a-168">Add a new field to the model.</span></span>
* <span data-ttu-id="5776a-169">将新字段架构更改迁移到数据库。</span><span class="sxs-lookup"><span data-stu-id="5776a-169">Migrate the new field schema change to the database.</span></span>

<span data-ttu-id="5776a-170">使用 EF Code First 自动创建数据库时，Code First 将：</span><span class="sxs-lookup"><span data-stu-id="5776a-170">When using EF Code First to automatically create a database, Code First:</span></span>

* <span data-ttu-id="5776a-171">向数据库添加表格，以跟踪数据库的架构是否与从生成它的模型类同步。</span><span class="sxs-lookup"><span data-stu-id="5776a-171">Adds a table to the database to track whether the schema of the database is in sync with the model classes it was generated from.</span></span>
* <span data-ttu-id="5776a-172">如果该模型类未与数据库同步，EF 将引发异常。</span><span class="sxs-lookup"><span data-stu-id="5776a-172">If the model classes aren't in sync with the DB, EF throws an exception.</span></span>

<span data-ttu-id="5776a-173">通过自动验证同步的架构/模型可以更容易地发现不一致的数据库/代码问题。</span><span class="sxs-lookup"><span data-stu-id="5776a-173">Automatic verification of schema/model in sync makes it easier to find inconsistent database/code issues.</span></span>

## <a name="adding-a-rating-property-to-the-movie-model"></a><span data-ttu-id="5776a-174">向电影模型添加分级属性</span><span class="sxs-lookup"><span data-stu-id="5776a-174">Adding a Rating Property to the Movie Model</span></span>

<span data-ttu-id="5776a-175">打开 Models/Movie.cs 文件，并添加  *属性：* `Rating`</span><span class="sxs-lookup"><span data-stu-id="5776a-175">Open the *Models/Movie.cs* file and add a `Rating` property:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="5776a-176">构建应用程序。</span><span class="sxs-lookup"><span data-stu-id="5776a-176">Build the app.</span></span>

<span data-ttu-id="5776a-177">编辑 Pages/Movies/Index.cshtml，并添加  *字段：* `Rating`</span><span class="sxs-lookup"><span data-stu-id="5776a-177">Edit *Pages/Movies/Index.cshtml*, and add a `Rating` field:</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/IndexRating.cshtml?highlight=40-42,61-63)]

<span data-ttu-id="5776a-178">更新以下页面：</span><span class="sxs-lookup"><span data-stu-id="5776a-178">Update the following pages:</span></span>

* <span data-ttu-id="5776a-179">将 `Rating` 字段添加到“删除”和“详细信息”页面。</span><span class="sxs-lookup"><span data-stu-id="5776a-179">Add the `Rating` field to the Delete and Details pages.</span></span>
* <span data-ttu-id="5776a-180">使用 [ 字段更新 ](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml)Create.cshtml`Rating`。</span><span class="sxs-lookup"><span data-stu-id="5776a-180">Update [Create.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Pages/Movies/Create.cshtml) with a `Rating` field.</span></span>
* <span data-ttu-id="5776a-181">将 `Rating` 字段添加到“编辑”页面。</span><span class="sxs-lookup"><span data-stu-id="5776a-181">Add the `Rating` field to the Edit Page.</span></span>

<span data-ttu-id="5776a-182">在 DB 更新为包括新字段之前，应用将不会正常工作。</span><span class="sxs-lookup"><span data-stu-id="5776a-182">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="5776a-183">如果立即运行，应用会引发 `SqlException`：</span><span class="sxs-lookup"><span data-stu-id="5776a-183">If run now, the app throws a `SqlException`:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="5776a-184">此错误是由于更新的 Movie 模型类与数据库的 Movie 表架构不同导致的。</span><span class="sxs-lookup"><span data-stu-id="5776a-184">This error is caused by the updated Movie model class being different than the schema of the Movie table of the database.</span></span> <span data-ttu-id="5776a-185">（数据库表中没有 `Rating` 列。）</span><span class="sxs-lookup"><span data-stu-id="5776a-185">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="5776a-186">可通过几种方法解决此错误：</span><span class="sxs-lookup"><span data-stu-id="5776a-186">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="5776a-187">让 Entity Framework 自动丢弃并使用新的模型类架构重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="5776a-187">Have the Entity Framework automatically drop and re-create the database using the new model class schema.</span></span> <span data-ttu-id="5776a-188">此方法在开发周期早期很方便；通过它可以一起快速改进模型和数据库架构。</span><span class="sxs-lookup"><span data-stu-id="5776a-188">This approach is convenient early in the development cycle; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="5776a-189">此方法的缺点是会导致数据库中的现有数据丢失。</span><span class="sxs-lookup"><span data-stu-id="5776a-189">The downside is that you lose existing data in the database.</span></span> <span data-ttu-id="5776a-190">请勿对生产数据库使用此方法！</span><span class="sxs-lookup"><span data-stu-id="5776a-190">Don't use this approach on a production database!</span></span> <span data-ttu-id="5776a-191">当架构更改时丢弃数据库并使用初始值设定项以使用测试数据自动设定数据库种子，这通常是开发应用的有效方式。</span><span class="sxs-lookup"><span data-stu-id="5776a-191">Dropping the DB on schema changes and using an initializer to automatically seed the database with test data is often a productive way to develop an app.</span></span>

2. <span data-ttu-id="5776a-192">对现有数据库架构进行显式修改，使它与模型类相匹配。</span><span class="sxs-lookup"><span data-stu-id="5776a-192">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="5776a-193">此方法的优点是可以保留数据。</span><span class="sxs-lookup"><span data-stu-id="5776a-193">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="5776a-194">可以手动或通过创建数据库更改脚本进行此更改。</span><span class="sxs-lookup"><span data-stu-id="5776a-194">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="5776a-195">使用 Code First 迁移更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="5776a-195">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="5776a-196">对于本教程，请使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="5776a-196">For this tutorial, use Code First Migrations.</span></span>

<span data-ttu-id="5776a-197">更新 `SeedData` 类，使它提供新列的值。</span><span class="sxs-lookup"><span data-stu-id="5776a-197">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="5776a-198">示例更改如下所示，但可能需要对每个 `new Movie` 块做出此更改。</span><span class="sxs-lookup"><span data-stu-id="5776a-198">A sample change is shown below, but you'll want to make this change for each `new Movie` block.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs?name=snippet1&highlight=8)]

<span data-ttu-id="5776a-199">请参阅[已完成的 SeedData.cs 文件](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs)。</span><span class="sxs-lookup"><span data-stu-id="5776a-199">See the [completed SeedData.cs file](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Models/SeedDataRating.cs).</span></span>

<span data-ttu-id="5776a-200">生成解决方案。</span><span class="sxs-lookup"><span data-stu-id="5776a-200">Build the solution.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5776a-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5776a-201">Visual Studio</span></span>](#tab/visual-studio)

<a name="pmc"></a>

### <a name="add-a-migration-for-the-rating-field"></a><span data-ttu-id="5776a-202">添加用于评级字段的迁移</span><span class="sxs-lookup"><span data-stu-id="5776a-202">Add a migration for the rating field</span></span>

<span data-ttu-id="5776a-203">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。  </span><span class="sxs-lookup"><span data-stu-id="5776a-203">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="5776a-204">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="5776a-204">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="5776a-205">`Add-Migration` 命令会通知框架执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="5776a-205">The `Add-Migration` command tells the framework to:</span></span>

* <span data-ttu-id="5776a-206">将 `Movie` 模型与 `Movie` DB 架构进行比较。</span><span class="sxs-lookup"><span data-stu-id="5776a-206">Compare the `Movie` model with the `Movie` DB schema.</span></span>
* <span data-ttu-id="5776a-207">创建代码以将 DB 架构迁移到新模型。</span><span class="sxs-lookup"><span data-stu-id="5776a-207">Create code to migrate the DB schema to the new model.</span></span>

<span data-ttu-id="5776a-208">名称“Rating”是任意的，用于对迁移文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="5776a-208">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="5776a-209">为迁移文件使用有意义的名称是有帮助的。</span><span class="sxs-lookup"><span data-stu-id="5776a-209">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="5776a-210">`Update-Database` 命令指示框架将架构更改应用到数据库。</span><span class="sxs-lookup"><span data-stu-id="5776a-210">The `Update-Database` command tells the framework to apply the schema changes to the database.</span></span>

<a name="ssox"></a>

<span data-ttu-id="5776a-211">如果删除 DB 中的所有记录，种子初始值设定项会设定 DB 种子，并将包括 `Rating` 字段。</span><span class="sxs-lookup"><span data-stu-id="5776a-211">If you delete all the records in the DB, the initializer will seed the DB and include the `Rating` field.</span></span> <span data-ttu-id="5776a-212">可以使用浏览器中的删除链接，也可以从 [Sql Server 对象资源管理器](xref:tutorials/razor-pages/sql#ssox) (SSOX) 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="5776a-212">You can do this with the delete links in the browser or from [Sql Server Object Explorer](xref:tutorials/razor-pages/sql#ssox) (SSOX).</span></span>

<span data-ttu-id="5776a-213">另一个方案是删除数据库，并使用迁移来重新创建该数据库。</span><span class="sxs-lookup"><span data-stu-id="5776a-213">Another option is to delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="5776a-214">删除 SSOX 中的数据库：</span><span class="sxs-lookup"><span data-stu-id="5776a-214">To delete the database in SSOX:</span></span>

* <span data-ttu-id="5776a-215">在 SSOX 中选择数据库。</span><span class="sxs-lookup"><span data-stu-id="5776a-215">Select the database in SSOX.</span></span>
* <span data-ttu-id="5776a-216">右键单击数据库，并选择“删除”。 </span><span class="sxs-lookup"><span data-stu-id="5776a-216">Right click on the database, and select *Delete*.</span></span>
* <span data-ttu-id="5776a-217">检查“关闭现有连接”  。</span><span class="sxs-lookup"><span data-stu-id="5776a-217">Check **Close existing connections**.</span></span>
* <span data-ttu-id="5776a-218">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="5776a-218">Select **OK**.</span></span>
* <span data-ttu-id="5776a-219">在 [PMC](xref:tutorials/razor-pages/new-field#pmc) 中更新数据库：</span><span class="sxs-lookup"><span data-stu-id="5776a-219">In the [PMC](xref:tutorials/razor-pages/new-field#pmc), update the database:</span></span>

  ```powershell
  Update-Database
  ```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="5776a-220">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5776a-220">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

### <a name="drop-and-re-create-the-database"></a><span data-ttu-id="5776a-221">删除并重新创建数据库</span><span class="sxs-lookup"><span data-stu-id="5776a-221">Drop and re-create the database</span></span>

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="5776a-222">删除数据库并通过迁移重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="5776a-222">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="5776a-223">若要删除该数据库，请删除数据库文件 (MvcMovie.db)  。</span><span class="sxs-lookup"><span data-stu-id="5776a-223">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="5776a-224">然后运行 `ef database update` 命令：</span><span class="sxs-lookup"><span data-stu-id="5776a-224">Then run the `ef database update` command:</span></span>

```dotnetcli
dotnet ef database update
```

---

<span data-ttu-id="5776a-225">运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。</span><span class="sxs-lookup"><span data-stu-id="5776a-225">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="5776a-226">如果数据库未设定种子，则在 `SeedData.Initialize` 方法中设置断点。</span><span class="sxs-lookup"><span data-stu-id="5776a-226">If the database isn't seeded, set a break point in the `SeedData.Initialize` method.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5776a-227">其他资源</span><span class="sxs-lookup"><span data-stu-id="5776a-227">Additional resources</span></span>

* [<span data-ttu-id="5776a-228">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="5776a-228">YouTube version of this tutorial</span></span>](https://youtu.be/3i7uMxiGGR8)

> [!div class="step-by-step"]
> <span data-ttu-id="5776a-229">[上一篇：添加搜索](xref:tutorials/razor-pages/search)
> [下一篇：添加验证](xref:tutorials/razor-pages/validation)</span><span class="sxs-lookup"><span data-stu-id="5776a-229">[Previous: Adding Search](xref:tutorials/razor-pages/search)
[Next: Adding Validation](xref:tutorials/razor-pages/validation)</span></span>

::: moniker-end
