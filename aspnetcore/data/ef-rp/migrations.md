---
title: ASP.NET Core 中的 Razor 页面和 EF Core - 迁移 - 第 4 个教程（共 8 个）
author: rick-anderson
description: 本教程使用 EF Core 迁移功能管理 ASP.NET Core MVC 应用中的数据模型更改。
ms.author: riande
ms.date: 6/31/2017
uid: data/ef-rp/migrations
ms.openlocfilehash: 406b94d74a99dcec3b2ce01a5eb3313c48819148
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65517004"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="1b3b4-103">ASP.NET Core 中的 Razor 页面和 EF Core - 迁移 - 第 4 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="1b3b4-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

<span data-ttu-id="1b3b4-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1b3b4-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

<span data-ttu-id="1b3b4-105">本教程使用 EF Core 迁移功能管理数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-105">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="1b3b4-106">如果遇到无法解决的问题，请下载[已完成应用](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-106">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="1b3b4-107">开发新应用时，数据模型会频繁更改。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-107">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="1b3b4-108">每当模型发生更改时，都无法与数据库进行同步。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-108">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="1b3b4-109">本教程首先配置 Entity Framework 以创建数据库（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-109">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="1b3b4-110">每当数据模型发生更改时：</span><span class="sxs-lookup"><span data-stu-id="1b3b4-110">Each time the data model changes:</span></span>

* <span data-ttu-id="1b3b4-111">DB 都会被删除。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-111">The DB is dropped.</span></span>
* <span data-ttu-id="1b3b4-112">EF 都会创建一个新数据库来匹配该模型。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-112">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="1b3b4-113">应用使用测试数据为 DB 设定种子。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-113">The app seeds the DB with test data.</span></span>

<span data-ttu-id="1b3b4-114">这种使 DB 与数据模型保持同步的方法适用于多种情况，但将应用部署到生产环境的情况除外。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-114">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="1b3b4-115">当应用在生产环境中运行时，应用通常会存储需要保留的数据。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-115">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="1b3b4-116">每当发生更改（例如添加新列）时，应用都无法在具有测试 DB 的环境下启动。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-116">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="1b3b4-117">EF Core 迁移功能可通过使 EF Core 更新 DB 架构而不是创建新 DB 来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-117">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="1b3b4-118">数据模型发生更改时，迁移将更新架构并保留现有数据，而无需删除或重新创建 DB。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-118">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="1b3b4-119">删除数据库</span><span class="sxs-lookup"><span data-stu-id="1b3b4-119">Drop the database</span></span>

<span data-ttu-id="1b3b4-120">使用 SQL Server 对象资源管理器 (SSOX) 或 `database drop` 命令：</span><span class="sxs-lookup"><span data-stu-id="1b3b4-120">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1b3b4-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b3b4-121">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1b3b4-122">在“包管理器控制台”(PMC) 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1b3b4-122">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
```

<span data-ttu-id="1b3b4-123">从 PMC 运行 `Get-Help about_EntityFrameworkCore`，获取帮助信息。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-123">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1b3b4-124">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1b3b4-124">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="1b3b4-125">打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-125">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="1b3b4-126">项目文件夹包含 Startup.cs 文件。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-126">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="1b3b4-127">在命令窗口中输入以下内容：</span><span class="sxs-lookup"><span data-stu-id="1b3b4-127">Enter the following in the command window:</span></span>

 ```console
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="1b3b4-128">创建初始迁移并更新 DB</span><span class="sxs-lookup"><span data-stu-id="1b3b4-128">Create an initial migration and update the DB</span></span>

<span data-ttu-id="1b3b4-129">生成项目并创建第一个迁移。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-129">Build the project and create the first migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1b3b4-130">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b3b4-130">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1b3b4-131">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1b3b4-131">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="1b3b4-132">了解 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="1b3b4-132">Examine the Up and Down methods</span></span>

<span data-ttu-id="1b3b4-133">EF Core `migrations add` 命令已生成用于创建 DB 的代码。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-133">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="1b3b4-134">此迁移代码位于 Migrations\<timestamp>_InitialCreate.cs 文件中。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-134">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="1b3b4-135">`InitialCreate` 类的 `Up` 的方法创建与数据模型实体集相对应的 DB 表。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-135">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="1b3b4-136">`Down` 方法删除这些表，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="1b3b4-136">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="1b3b4-137">迁移调用 `Up` 方法为迁移实现数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-137">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="1b3b4-138">输入用于回退更新的命令时，迁移调用 `Down` 方法。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-138">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="1b3b4-139">前面的代码适用于初始迁移。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-139">The preceding code is for the initial migration.</span></span> <span data-ttu-id="1b3b4-140">该代码是运行 `migrations add InitialCreate` 命令时创建的。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-140">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="1b3b4-141">迁移名称参数（本示例中为“InitialCreate”）用于指定文件名。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-141">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="1b3b4-142">迁移名称可以是任何有效的文件名。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-142">The migration name can be any valid file name.</span></span> <span data-ttu-id="1b3b4-143">最好选择能概括迁移中所执行操作的字词或短语。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-143">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="1b3b4-144">例如，添加了系表的迁移可称为“AddDepartmentTable”。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-144">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="1b3b4-145">如果创建了初始迁移并且存在 DB：</span><span class="sxs-lookup"><span data-stu-id="1b3b4-145">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="1b3b4-146">会生成 DB 创建代码。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-146">The DB creation code is generated.</span></span>
* <span data-ttu-id="1b3b4-147">DB 创建代码不需要运行，因为 DB 已与数据模型相匹配。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-147">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="1b3b4-148">即使 DB 创建代码运行也不会做出任何更改，因为 DB 已与数据模型相匹配。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-148">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="1b3b4-149">如果将应用部署到新环境，则必须运行 DB 创建代码才能创建 DB。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-149">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="1b3b4-150">先前删除了 DB，因此已不存在，所以迁移会创建新的 DB。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-150">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="1b3b4-151">数据模型快照</span><span class="sxs-lookup"><span data-stu-id="1b3b4-151">The data model snapshot</span></span>

<span data-ttu-id="1b3b4-152">迁移在 Migrations/SchoolContextModelSnapshot.cs 中创建当前数据库架构的快照。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-152">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="1b3b4-153">添加迁移时，EF 会通过将数据模型与快照文件进行对比来确定已更改的内容。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-153">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="1b3b4-154">若要删除迁移，请使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="1b3b4-154">To delete a migration, use the following command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1b3b4-155">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1b3b4-155">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1b3b4-156">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="1b3b4-156">Remove-Migration</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="1b3b4-157">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="1b3b4-157">.NET Core CLI</span></span>](#tab/netcore-cli)

```console
dotnet ef migrations remove
```

<span data-ttu-id="1b3b4-158">有关详细信息，请参阅 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-158">For more information, see  [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="1b3b4-159">删除迁移命令会删除迁移并确保正确重置快照。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-159">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="1b3b4-160">删除 EnsureCreated 并测试应用</span><span class="sxs-lookup"><span data-stu-id="1b3b4-160">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="1b3b4-161">早期开发使用了 `EnsureCreated`。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-161">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="1b3b4-162">本教程将使用迁移。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-162">In this tutorial, migrations are used.</span></span> <span data-ttu-id="1b3b4-163">`EnsureCreated` 具有以下限制：</span><span class="sxs-lookup"><span data-stu-id="1b3b4-163">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="1b3b4-164">绕过迁移并创建 DB 和架构。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-164">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="1b3b4-165">不会创建迁移表。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-165">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="1b3b4-166">不能与迁移一起使用。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-166">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="1b3b4-167">专门用于在频繁删除并重新创建 DB 的情况下进行测试或快速制作原型。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-167">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="1b3b4-168">删除 `EnsureCreated`：</span><span class="sxs-lookup"><span data-stu-id="1b3b4-168">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="1b3b4-169">运行应用并验证 DB 设定为种子。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-169">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="1b3b4-170">检查数据库</span><span class="sxs-lookup"><span data-stu-id="1b3b4-170">Inspect the database</span></span>

<span data-ttu-id="1b3b4-171">使用 SQL Server 对象资源管理器检查 DB。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-171">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="1b3b4-172">请注意，增加了 `__EFMigrationsHistory` 表。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-172">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="1b3b4-173">`__EFMigrationsHistory` 表跟踪已应用到 DB 的迁移。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-173">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="1b3b4-174">查看 `__EFMigrationsHistory` 表中的数据，其中显示对应初始迁移的一行数据。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-174">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="1b3b4-175">上面的 CLI 输出示例中最后部分的日志显示了创建此行的 INSERT 语句。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-175">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="1b3b4-176">运行应用并验证一切正常运行。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-176">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="1b3b4-177">在生产环境中应用迁移</span><span class="sxs-lookup"><span data-stu-id="1b3b4-177">Applying migrations in production</span></span>

<span data-ttu-id="1b3b4-178">不建议生产应用在应用程序启动时调用 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-178">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="1b3b4-179">不应从服务器场中的应用调用 `Migrate`。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-179">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="1b3b4-180">例如，已将应用在云中部署为横向扩展（运行应用的多个示例）的情况。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-180">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="1b3b4-181">应在部署过程中以受控的方式执行数据库迁移。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-181">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="1b3b4-182">生产数据库迁移方法包括：</span><span class="sxs-lookup"><span data-stu-id="1b3b4-182">Production database migration approaches include:</span></span>

* <span data-ttu-id="1b3b4-183">使用迁移创建 SQL 脚本，并在部署过程中使用 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-183">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="1b3b4-184">在受控的环境中运行 `dotnet ef database update`。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-184">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="1b3b4-185">EF Core 使用 `__MigrationsHistory` 表查看是否需要运行任何迁移。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-185">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="1b3b4-186">如果 DB 已是最新，则无需运行迁移。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-186">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="1b3b4-187">疑难解答</span><span class="sxs-lookup"><span data-stu-id="1b3b4-187">Troubleshooting</span></span>

<span data-ttu-id="1b3b4-188">下载[已完成应用](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations)。</span><span class="sxs-lookup"><span data-stu-id="1b3b4-188">Download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/StageSnapShots/cu-part4-migrations).</span></span>

<span data-ttu-id="1b3b4-189">应用会生成以下异常：</span><span class="sxs-lookup"><span data-stu-id="1b3b4-189">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="1b3b4-190">解决方案：运行 `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="1b3b4-190">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="1b3b4-191">其他资源</span><span class="sxs-lookup"><span data-stu-id="1b3b4-191">Additional resources</span></span>

* [<span data-ttu-id="1b3b4-192">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="1b3b4-192">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="1b3b4-193">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="1b3b4-193">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="1b3b4-194">包管理器控制台 (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="1b3b4-194">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="1b3b4-195">[上一页](xref:data/ef-rp/sort-filter-page)
> [下一页](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="1b3b4-195">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>
