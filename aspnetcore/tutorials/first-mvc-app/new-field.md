---
title: 将新字段添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: 了解如何使用 Entity Framework Code First 迁移将新字段添加到模型，并将此更改迁移到数据库。
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: cf8bb67703b564a711105123117498c94ab44e68
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750511"
---
# <a name="add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="d9472-103">将新字段添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="d9472-103">Add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="d9472-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d9472-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d9472-105">在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：</span><span class="sxs-lookup"><span data-stu-id="d9472-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="d9472-106">将新字段添加到模型。</span><span class="sxs-lookup"><span data-stu-id="d9472-106">Add a new field to the model.</span></span>
* <span data-ttu-id="d9472-107">将新字段迁移到数据库。</span><span class="sxs-lookup"><span data-stu-id="d9472-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="d9472-108">使用 EF Code First 自动创建数据库时，Code First 将：</span><span class="sxs-lookup"><span data-stu-id="d9472-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="d9472-109">将表添加到数据库，以跟踪数据库的架构。</span><span class="sxs-lookup"><span data-stu-id="d9472-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="d9472-110">验证数据库与生成它的模型类是否同步。</span><span class="sxs-lookup"><span data-stu-id="d9472-110">Verify the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="d9472-111">如果它们不同步，EF 则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="d9472-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="d9472-112">这使查找不一致的数据库/代码问题变得更加轻松。</span><span class="sxs-lookup"><span data-stu-id="d9472-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="d9472-113">向电影模型添加分级属性</span><span class="sxs-lookup"><span data-stu-id="d9472-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="d9472-114">将 `Rating` 属性添加到 Models/Movie.cs：</span><span class="sxs-lookup"><span data-stu-id="d9472-114">Add a `Rating` property to *Models/Movie.cs*:</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="d9472-115">生成应用 (Ctrl+Shift+B)。</span><span class="sxs-lookup"><span data-stu-id="d9472-115">Build the app (Ctrl+Shift+B).</span></span>

<span data-ttu-id="d9472-116">因为已经添加新字段到 `Movie` 类，所以需要更新绑定允许名单，将此新属性纳入其中。</span><span class="sxs-lookup"><span data-stu-id="d9472-116">Because you've added a new field to the `Movie` class, you need to update the binding white list so this new property will be included.</span></span> <span data-ttu-id="d9472-117">在 MoviesController.cs 中，更新 `Create` 和 `Edit` 操作方法的 `[Bind]` 属性，以包括 `Rating` 属性：</span><span class="sxs-lookup"><span data-stu-id="d9472-117">In *MoviesController.cs*, update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="d9472-118">更新视图模板以在浏览器视图中显示、创建和编辑新的 `Rating` 属性。</span><span class="sxs-lookup"><span data-stu-id="d9472-118">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="d9472-119">编辑 /Views/Movies/Index.cshtml 文件并添加 `Rating` 字段：</span><span class="sxs-lookup"><span data-stu-id="d9472-119">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-HTML[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="d9472-120">使用 `Rating` 字段更新 /Views/Movies/Create.cshtml。</span><span class="sxs-lookup"><span data-stu-id="d9472-120">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mactabvisual-studiovisual-studio-mac"></a>[<span data-ttu-id="d9472-121">Visual Studio / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d9472-121">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="d9472-122">可以复制/粘贴之前的“窗体组”，并让 intelliSense 帮助更新字段。</span><span class="sxs-lookup"><span data-stu-id="d9472-122">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="d9472-123">IntelliSense 适用于[标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="d9472-123">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![开发人员已在视图的第二个标签元素中键入字母 R 用作 asp-for 的特性值。](new-field/_static/cr.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="d9472-127">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d9472-127">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="d9472-128">更新 `SeedData` 类，使它提供新列的值。</span><span class="sxs-lookup"><span data-stu-id="d9472-128">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="d9472-129">示例更改如下所示，但可能需要对每个 `new Movie` 做出此更改。</span><span class="sxs-lookup"><span data-stu-id="d9472-129">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="d9472-130">在 DB 更新为包括新字段之前，应用将不会正常工作。</span><span class="sxs-lookup"><span data-stu-id="d9472-130">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="d9472-131">如果它现在运行，将引发以下 `SqlException`：</span><span class="sxs-lookup"><span data-stu-id="d9472-131">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="d9472-132">发生此错误是因为更新的 Movie 模型类与现有数据库的 Movie 表架构不同。</span><span class="sxs-lookup"><span data-stu-id="d9472-132">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="d9472-133">（数据库表中没有 `Rating` 列。）</span><span class="sxs-lookup"><span data-stu-id="d9472-133">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="d9472-134">可通过几种方法解决此错误：</span><span class="sxs-lookup"><span data-stu-id="d9472-134">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="d9472-135">让 Entity Framework 自动丢弃，并基于新的模型类架构重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="d9472-135">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="d9472-136">在测试数据库上进行开发时，此方法在开发周期早期很方便；通过它可以一起快速改进模型和数据库架构。</span><span class="sxs-lookup"><span data-stu-id="d9472-136">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="d9472-137">但其缺点是会丢失数据库中的现有数据 - 因此请勿对生产数据库使用此方法！</span><span class="sxs-lookup"><span data-stu-id="d9472-137">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="d9472-138">使用初始值设定项，以使用测试数据自动设定数据库种子，这通常是开发应用程序的有效方式。</span><span class="sxs-lookup"><span data-stu-id="d9472-138">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="d9472-139">对于早期开发和使用 SQLite 的情况，这是一个不错的方法。</span><span class="sxs-lookup"><span data-stu-id="d9472-139">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="d9472-140">对现有数据库架构进行显式修改，使它与模型类相匹配。</span><span class="sxs-lookup"><span data-stu-id="d9472-140">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="d9472-141">此方法的优点是可以保留数据。</span><span class="sxs-lookup"><span data-stu-id="d9472-141">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="d9472-142">可以手动或通过创建数据库更改脚本进行此更改。</span><span class="sxs-lookup"><span data-stu-id="d9472-142">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="d9472-143">使用 Code First 迁移更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="d9472-143">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="d9472-144">对于本教程，请使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="d9472-144">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="d9472-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d9472-145">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d9472-146">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。</span><span class="sxs-lookup"><span data-stu-id="d9472-146">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC 菜单](adding-model/_static/pmc.png)

<span data-ttu-id="d9472-148">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="d9472-148">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="d9472-149">`Add-Migration` 命令会通知迁移框架使用当前 `Movie` DB 架构检查当前 `Movie` 模型，并创建必要的代码，将 DB 迁移到新模型。</span><span class="sxs-lookup"><span data-stu-id="d9472-149">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="d9472-150">名称“Rating”是任意的，用于对迁移文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="d9472-150">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="d9472-151">为迁移文件使用有意义的名称是有帮助的。</span><span class="sxs-lookup"><span data-stu-id="d9472-151">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="d9472-152">如果删除 DB 中的所有记录，初始化方法会设定 DB 种子，并将包括 `Rating` 字段。</span><span class="sxs-lookup"><span data-stu-id="d9472-152">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="d9472-153">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d9472-153">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="d9472-154">删除数据库并通过迁移重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="d9472-154">Delete the database and use migrations to re-create the database.</span></span> <span data-ttu-id="d9472-155">若要删除该数据库，请删除数据库文件 (MvcMovie.db)。</span><span class="sxs-lookup"><span data-stu-id="d9472-155">To delete the database, delete the database file (*MvcMovie.db*).</span></span> <span data-ttu-id="d9472-156">然后运行 `ef database update` 命令：</span><span class="sxs-lookup"><span data-stu-id="d9472-156">Then run the `ef database update` command:</span></span>

```console
dotnet ef database update
```

---
<!-- End of VS tabs -->

<span data-ttu-id="d9472-157">运行应用，并验证是否可以创建/编辑/显示具有 `Rating` 字段的电影。</span><span class="sxs-lookup"><span data-stu-id="d9472-157">Run the app and verify you can create/edit/display movies with a `Rating` field.</span></span> <span data-ttu-id="d9472-158">应向 `Edit`、`Details` 和 `Delete` 视图模板添加 `Rating` 字段。</span><span class="sxs-lookup"><span data-stu-id="d9472-158">You should add the `Rating` field to the `Edit`, `Details`, and `Delete` view templates.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="d9472-159">[上一页](search.md)
> [下一页](validation.md)</span><span class="sxs-lookup"><span data-stu-id="d9472-159">[Previous](search.md)
[Next](validation.md)</span></span>
