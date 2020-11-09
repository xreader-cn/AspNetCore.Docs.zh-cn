---
title: 第 8 部分，将新字段添加到 ASP.NET Core MVC 应用
author: rick-anderson
description: ASP.NET Core MVC 教程系列的第 8 部分。
ms.author: riande
ms.custom: mvc
ms.date: 12/13/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: tutorials/first-mvc-app/new-field
ms.openlocfilehash: d2b3b22a94e3119712e331565cc74ffa60ada726
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050701"
---
# <a name="part-8-add-a-new-field-to-an-aspnet-core-mvc-app"></a><span data-ttu-id="65959-103">第 8 部分，将新字段添加到 ASP.NET Core MVC 应用</span><span class="sxs-lookup"><span data-stu-id="65959-103">Part 8, add a new field to an ASP.NET Core MVC app</span></span>

<span data-ttu-id="65959-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="65959-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="65959-105">在此部分中，[Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First 迁移用于：</span><span class="sxs-lookup"><span data-stu-id="65959-105">In this section [Entity Framework](/ef/core/get-started/aspnetcore/new-db) Code First Migrations is used to:</span></span>

* <span data-ttu-id="65959-106">将新字段添加到模型。</span><span class="sxs-lookup"><span data-stu-id="65959-106">Add a new field to the model.</span></span>
* <span data-ttu-id="65959-107">将新字段迁移到数据库。</span><span class="sxs-lookup"><span data-stu-id="65959-107">Migrate the new field to the database.</span></span>

<span data-ttu-id="65959-108">使用 EF Code First 自动创建数据库时，Code First 将：</span><span class="sxs-lookup"><span data-stu-id="65959-108">When EF Code First is used to automatically create a database, Code First:</span></span>

* <span data-ttu-id="65959-109">将表添加到数据库，以跟踪数据库的架构。</span><span class="sxs-lookup"><span data-stu-id="65959-109">Adds a table to the database to  track the schema of the database.</span></span>
* <span data-ttu-id="65959-110">验证数据库与生成它的模型类是否同步。</span><span class="sxs-lookup"><span data-stu-id="65959-110">Verifies the database is in sync with the model classes it was generated from.</span></span> <span data-ttu-id="65959-111">如果它们不同步，EF 则会引发异常。</span><span class="sxs-lookup"><span data-stu-id="65959-111">If they aren't in sync, EF throws an exception.</span></span> <span data-ttu-id="65959-112">这使查找不一致的数据库/代码问题变得更加轻松。</span><span class="sxs-lookup"><span data-stu-id="65959-112">This makes it easier to find inconsistent database/code issues.</span></span>

## <a name="add-a-rating-property-to-the-movie-model"></a><span data-ttu-id="65959-113">向电影模型添加分级属性</span><span class="sxs-lookup"><span data-stu-id="65959-113">Add a Rating Property to the Movie Model</span></span>

<span data-ttu-id="65959-114">将 `Rating` 属性添加到 Models/Movie.cs：</span><span class="sxs-lookup"><span data-stu-id="65959-114">Add a `Rating` property to *Models/Movie.cs* :</span></span>

[!code-csharp[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Models/MovieDateRating.cs?highlight=13&name=snippet)]

<span data-ttu-id="65959-115">生成应用</span><span class="sxs-lookup"><span data-stu-id="65959-115">Build the app</span></span>

### <a name="visual-studio"></a>[<span data-ttu-id="65959-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65959-116">Visual Studio</span></span>](#tab/visual-studio)

 <span data-ttu-id="65959-117">Ctrl+Shift+B</span><span class="sxs-lookup"><span data-stu-id="65959-117">Ctrl+Shift+B</span></span>

### <a name="visual-studio-code"></a>[<span data-ttu-id="65959-118">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65959-118">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet build
```

### <a name="visual-studio-for-mac"></a>[<span data-ttu-id="65959-119">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65959-119">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="65959-120">命令 ⌘ + B</span><span class="sxs-lookup"><span data-stu-id="65959-120">Command ⌘ + B</span></span>

------

<span data-ttu-id="65959-121">因为已经添加新字段到 `Movie` 类，所以需要更新属性绑定列表，将此新属性纳入其中。</span><span class="sxs-lookup"><span data-stu-id="65959-121">Because you've added a new field to the `Movie` class, you need to update the property binding list so this new property will be included.</span></span> <span data-ttu-id="65959-122">在 MoviesController.cs 中，更新 `Create` 和 `Edit` 操作方法的 `[Bind]` 属性，以包括 `Rating` 属性：</span><span class="sxs-lookup"><span data-stu-id="65959-122">In *MoviesController.cs* , update the `[Bind]` attribute for both the `Create` and `Edit` action methods to include the `Rating` property:</span></span>

```csharp
[Bind("Id,Title,ReleaseDate,Genre,Price,Rating")]
   ```

<span data-ttu-id="65959-123">更新视图模板以在浏览器视图中显示、创建和编辑新的 `Rating` 属性。</span><span class="sxs-lookup"><span data-stu-id="65959-123">Update the view templates in order to display, create, and edit the new `Rating` property in the browser view.</span></span>

<span data-ttu-id="65959-124">编辑 /Views/Movies/Index.cshtml 文件并添加 `Rating` 字段：</span><span class="sxs-lookup"><span data-stu-id="65959-124">Edit the */Views/Movies/Index.cshtml* file and add a `Rating` field:</span></span>

[!code-cshtml[](~/tutorials/first-mvc-app/start-mvc/sample/MvcMovie22/Views/Movies/IndexGenreRating.cshtml?highlight=16,38&range=24-64)]

<span data-ttu-id="65959-125">使用 `Rating` 字段更新 /Views/Movies/Create.cshtml。</span><span class="sxs-lookup"><span data-stu-id="65959-125">Update the */Views/Movies/Create.cshtml* with a `Rating` field.</span></span>

# <a name="visual-studio--visual-studio-for-mac"></a>[<span data-ttu-id="65959-126">Visual Studio / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65959-126">Visual Studio / Visual Studio for Mac</span></span>](#tab/visual-studio+visual-studio-mac)

<span data-ttu-id="65959-127">可以复制/粘贴之前的“窗体组”，并让 intelliSense 帮助更新字段。</span><span class="sxs-lookup"><span data-stu-id="65959-127">You can copy/paste the previous "form group" and let intelliSense help you update the fields.</span></span> <span data-ttu-id="65959-128">IntelliSense 适用于[标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="65959-128">IntelliSense works with [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

![开发人员已在视图的第二个标签元素中键入字母 R 用作 asp-for 的特性值。](new-field/_static/cr.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="65959-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="65959-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!-- This tab intentionally left blank. -->

---

<span data-ttu-id="65959-133">更新剩余模板。</span><span class="sxs-lookup"><span data-stu-id="65959-133">Update the remaining templates.</span></span>

<span data-ttu-id="65959-134">更新 `SeedData` 类，使它提供新列的值。</span><span class="sxs-lookup"><span data-stu-id="65959-134">Update the `SeedData` class so that it provides a value for the new column.</span></span> <span data-ttu-id="65959-135">示例更改如下所示，但可能需要对每个 `new Movie` 做出此更改。</span><span class="sxs-lookup"><span data-stu-id="65959-135">A sample change is shown below, but you'll want to make this change for each `new Movie`.</span></span>

[!code-csharp[](start-mvc/sample/MvcMovie/Models/SeedDataRating.cs?name=snippet1&highlight=6)]

<span data-ttu-id="65959-136">在 DB 更新为包括新字段之前，应用将不会正常工作。</span><span class="sxs-lookup"><span data-stu-id="65959-136">The app won't work until the DB is updated to include the new field.</span></span> <span data-ttu-id="65959-137">如果它现在运行，将引发以下 `SqlException`：</span><span class="sxs-lookup"><span data-stu-id="65959-137">If it's run now, the following `SqlException` is thrown:</span></span>

`SqlException: Invalid column name 'Rating'.`

<span data-ttu-id="65959-138">发生此错误是因为更新的 Movie 模型类与现有数据库的 Movie 表架构不同。</span><span class="sxs-lookup"><span data-stu-id="65959-138">This error occurs because the updated Movie model class is different than the schema of the Movie table of the existing database.</span></span> <span data-ttu-id="65959-139">（数据库表中没有 `Rating` 列。）</span><span class="sxs-lookup"><span data-stu-id="65959-139">(There's no `Rating` column in the database table.)</span></span>

<span data-ttu-id="65959-140">可通过几种方法解决此错误：</span><span class="sxs-lookup"><span data-stu-id="65959-140">There are a few approaches to resolving the error:</span></span>

1. <span data-ttu-id="65959-141">让 Entity Framework 自动丢弃，并基于新的模型类架构重新创建数据库。</span><span class="sxs-lookup"><span data-stu-id="65959-141">Have the Entity Framework automatically drop and re-create the database based on the new model class schema.</span></span> <span data-ttu-id="65959-142">在测试数据库上进行开发时，此方法在开发周期早期很方便；通过它可以一起快速改进模型和数据库架构。</span><span class="sxs-lookup"><span data-stu-id="65959-142">This approach is very convenient early in the development cycle when you're doing active development on a test database; it allows you to quickly evolve the model and database schema together.</span></span> <span data-ttu-id="65959-143">但其缺点是会丢失数据库中的现有数据 - 因此请勿对生产数据库使用此方法！</span><span class="sxs-lookup"><span data-stu-id="65959-143">The downside, though, is that you lose existing data in the database — so you don't want to use this approach on a production database!</span></span> <span data-ttu-id="65959-144">使用初始值设定项，以使用测试数据自动设定数据库种子，这通常是开发应用程序的有效方式。</span><span class="sxs-lookup"><span data-stu-id="65959-144">Using an initializer to automatically seed a database with test data is often a productive way to develop an application.</span></span> <span data-ttu-id="65959-145">对于早期开发和使用 SQLite 的情况，这是一个不错的方法。</span><span class="sxs-lookup"><span data-stu-id="65959-145">This is a good approach for early development and when using SQLite.</span></span>

2. <span data-ttu-id="65959-146">对现有数据库架构进行显式修改，使它与模型类相匹配。</span><span class="sxs-lookup"><span data-stu-id="65959-146">Explicitly modify the schema of the existing database so that it matches the model classes.</span></span> <span data-ttu-id="65959-147">此方法的优点是可以保留数据。</span><span class="sxs-lookup"><span data-stu-id="65959-147">The advantage of this approach is that you keep your data.</span></span> <span data-ttu-id="65959-148">可以手动或通过创建数据库更改脚本进行此更改。</span><span class="sxs-lookup"><span data-stu-id="65959-148">You can make this change either manually or by creating a database change script.</span></span>

3. <span data-ttu-id="65959-149">使用 Code First 迁移更新数据库架构。</span><span class="sxs-lookup"><span data-stu-id="65959-149">Use Code First Migrations to update the database schema.</span></span>

<span data-ttu-id="65959-150">对于本教程，请使用 Code First 迁移。</span><span class="sxs-lookup"><span data-stu-id="65959-150">For this tutorial, Code First Migrations is used.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="65959-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="65959-151">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="65959-152">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。 </span><span class="sxs-lookup"><span data-stu-id="65959-152">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>

  ![PMC 菜单](adding-model/_static/pmc.png)

<span data-ttu-id="65959-154">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="65959-154">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Rating
Update-Database
```

<span data-ttu-id="65959-155">`Add-Migration` 命令会通知迁移框架使用当前 `Movie` DB 架构检查当前 `Movie` 模型，并创建必要的代码，将 DB 迁移到新模型。</span><span class="sxs-lookup"><span data-stu-id="65959-155">The `Add-Migration` command tells the migration framework to examine the current `Movie` model with the current `Movie` DB schema and create the necessary code to migrate the DB to the new model.</span></span>

<span data-ttu-id="65959-156">名称“Rating”是任意的，用于对迁移文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="65959-156">The name "Rating" is arbitrary and is used to name the migration file.</span></span> <span data-ttu-id="65959-157">为迁移文件使用有意义的名称是有帮助的。</span><span class="sxs-lookup"><span data-stu-id="65959-157">It's helpful to use a meaningful name for the migration file.</span></span>

<span data-ttu-id="65959-158">如果删除 DB 中的所有记录，初始化方法会设定 DB 种子，并将包括 `Rating` 字段。</span><span class="sxs-lookup"><span data-stu-id="65959-158">If all the records in the DB are deleted, the initialize method will seed the DB and include the `Rating` field.</span></span>

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="65959-159">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="65959-159">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

[!INCLUDE[](~/includes/RP-mvc-shared/sqlite-warn.md)]

<span data-ttu-id="65959-160">删除数据库和上一次迁移，然后使用迁移重新创建数据库：</span><span class="sxs-lookup"><span data-stu-id="65959-160">Delete the database and the previous migration and use migrations to re-create the database:</span></span>

```dotnetcli
dotnet ef migrations remove
dotnet ef database drop
dotnet ef migrations add InitialCreate
dotnet ef database update
```

<span data-ttu-id="65959-161">`dotnet ef migrations remove` 删除上一次迁移。</span><span class="sxs-lookup"><span data-stu-id="65959-161">`dotnet ef migrations remove` removes the last migration.</span></span> <span data-ttu-id="65959-162">如果有多个迁移，请删除“迁移”文件夹。</span><span class="sxs-lookup"><span data-stu-id="65959-162">If there are more than one migration, delete the Migrations folder.</span></span>

---
<!-- End of VS tabs -->

<span data-ttu-id="65959-163">运行应用，并验证是否可以创建、编辑和显示具有 `Rating` 字段的电影。</span><span class="sxs-lookup"><span data-stu-id="65959-163">Run the app and verify you can create, edit, and display movies with a `Rating` field.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="65959-164">[上一页](search.md)
> [下一页](validation.md)</span><span class="sxs-lookup"><span data-stu-id="65959-164">[Previous](search.md)
[Next](validation.md)</span></span>
