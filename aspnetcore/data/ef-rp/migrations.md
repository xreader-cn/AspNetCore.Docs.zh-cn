---
title: ASP.NET Core 中的 Razor 页面和 EF Core - 迁移 - 第 4 个教程（共 8 个）
author: tdykstra
description: 本教程使用 EF Core 迁移功能管理 ASP.NET Core MVC 应用中的数据模型更改。
ms.author: riande
ms.date: 07/22/2019
uid: data/ef-rp/migrations
ms.openlocfilehash: efcf62d56a7b4cee4780d5f0475b4ef363fe1897
ms.sourcegitcommit: d34b2627a69bc8940b76a949de830335db9701d3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71187069"
---
# <a name="razor-pages-with-ef-core-in-aspnet-core---migrations---4-of-8"></a><span data-ttu-id="9478e-103">ASP.NET Core 中的 Razor 页面和 EF Core - 迁移 - 第 4 个教程（共 8 个）</span><span class="sxs-lookup"><span data-stu-id="9478e-103">Razor Pages with EF Core in ASP.NET Core - Migrations - 4 of 8</span></span>

<span data-ttu-id="9478e-104">作者：[Tom Dykstra](https://github.com/tdykstra)、[Jon P Smith](https://twitter.com/thereformedprog) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9478e-104">By [Tom Dykstra](https://github.com/tdykstra), [Jon P Smith](https://twitter.com/thereformedprog), and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

[!INCLUDE [about the series](~/includes/RP-EF/intro.md)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9478e-105">本教程介绍管理数据模型更改的 EF Core 迁移功能。</span><span class="sxs-lookup"><span data-stu-id="9478e-105">This tutorial introduces the EF Core migrations feature for managing data model changes.</span></span>

<span data-ttu-id="9478e-106">开发新应用时，数据模型会频繁更改。</span><span class="sxs-lookup"><span data-stu-id="9478e-106">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="9478e-107">每当模型发生更改时，都无法与数据库进行同步。</span><span class="sxs-lookup"><span data-stu-id="9478e-107">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="9478e-108">本教程从配置实体框架以创建数据库（如果不存在）开始。</span><span class="sxs-lookup"><span data-stu-id="9478e-108">This tutorial series started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="9478e-109">数据模型每次发生更改时，必须删除该数据库。</span><span class="sxs-lookup"><span data-stu-id="9478e-109">Each time the data model changes, you have to drop the database.</span></span> <span data-ttu-id="9478e-110">下次应用运行时，对 `EnsureCreated` 的调用将重新创建数据库以匹配新的数据模型。</span><span class="sxs-lookup"><span data-stu-id="9478e-110">The next time the app runs, the call to `EnsureCreated` re-creates the database to match the new data model.</span></span> <span data-ttu-id="9478e-111">然后 `DbInitializer` 类将运行以设定新数据库的种子。</span><span class="sxs-lookup"><span data-stu-id="9478e-111">The `DbInitializer` class then runs to seed the new database.</span></span>

<span data-ttu-id="9478e-112">这种使 DB 与数据模型保持同步的方法适用于多种情况，但将应用部署到生产环境的情况除外。</span><span class="sxs-lookup"><span data-stu-id="9478e-112">This approach to keeping the database in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="9478e-113">当应用在生产环境中运行时，应用通常会存储需要保留的数据。</span><span class="sxs-lookup"><span data-stu-id="9478e-113">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="9478e-114">每当发生更改（例如添加新列）时，应用都无法在具有测试数据库的环境下启动。</span><span class="sxs-lookup"><span data-stu-id="9478e-114">The app can't start with a test database each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="9478e-115">EF Core 迁移功能通过启用 EF Core 更新数据库架构而不是创建新数据库来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="9478e-115">The EF Core Migrations feature solves this problem by enabling EF Core to update the database schema instead of creating a new database.</span></span>

<span data-ttu-id="9478e-116">数据模型更改时，迁移不会删除并重新创建数据库，而是更新架构并保留现有数据。</span><span class="sxs-lookup"><span data-stu-id="9478e-116">Rather than dropping and recreating the database when the data model changes, migrations updates the schema and retains existing data.</span></span>

[!INCLUDE[](~/includes/sqlite-warn.md)]

## <a name="drop-the-database"></a><span data-ttu-id="9478e-117">删除数据库</span><span class="sxs-lookup"><span data-stu-id="9478e-117">Drop the database</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9478e-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9478e-118">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9478e-119">使用 SQL Server 对象资源管理器 (SSOX) 删除数据库或在包管理器控制台 (PMC) 中运行以下命令   ：</span><span class="sxs-lookup"><span data-stu-id="9478e-119">Use **SQL Server Object Explorer** (SSOX) to delete the database, or run the following command in the **Package Manager Console** (PMC):</span></span>

```powershell
Drop-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9478e-120">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9478e-120">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="9478e-121">在命令提示符下运行以下命令以安装 EF CLI 工具：</span><span class="sxs-lookup"><span data-stu-id="9478e-121">Run the following command at a command prompt to install the EF CLI tools:</span></span>

  ```dotnetcli
  dotnet tool install --global dotnet-ef
  ```

* <span data-ttu-id="9478e-122">在命令提示符下，导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="9478e-122">In the command prompt, navigate to the project folder.</span></span> <span data-ttu-id="9478e-123">项目文件夹包含 ContosoUniversity.csproj 文件  。</span><span class="sxs-lookup"><span data-stu-id="9478e-123">The project folder contains the *ContosoUniversity.csproj* file.</span></span>

* <span data-ttu-id="9478e-124">删除 CU.db 文件，或运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="9478e-124">Delete the *CU.db* file, or run the following command:</span></span>

  ```dotnetcli
  dotnet ef database drop --force
  ```

---

## <a name="create-an-initial-migration"></a><span data-ttu-id="9478e-125">创建初始迁移</span><span class="sxs-lookup"><span data-stu-id="9478e-125">Create an initial migration</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9478e-126">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9478e-126">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9478e-127">在 PMC 中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="9478e-127">Run the following commands in the PMC:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9478e-128">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9478e-128">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9478e-129">确保命令提示符位于项目文件夹中，并运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="9478e-129">Make sure the command prompt is in the project folder, and run the following commands:</span></span>

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

## <a name="up-and-down-methods"></a><span data-ttu-id="9478e-130">Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="9478e-130">Up and Down methods</span></span>

<span data-ttu-id="9478e-131">EF Core `migrations add` 命令已生成用于创建数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="9478e-131">The EF Core `migrations add` command generated code to create the database.</span></span> <span data-ttu-id="9478e-132">此迁移代码位于 Migrations\<timestamp>_InitialCreate.cs 文件中  。</span><span class="sxs-lookup"><span data-stu-id="9478e-132">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="9478e-133">`InitialCreate` 类的 `Up` 方法创建与数据模型实体集对应的数据库表。</span><span class="sxs-lookup"><span data-stu-id="9478e-133">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="9478e-134">`Down` 方法删除这些表，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="9478e-134">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu30/Migrations/20190731193522_InitialCreate.cs)]

<span data-ttu-id="9478e-135">前面的代码适用于初始迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-135">The preceding code is for the initial migration.</span></span> <span data-ttu-id="9478e-136">代码：</span><span class="sxs-lookup"><span data-stu-id="9478e-136">The code:</span></span>

* <span data-ttu-id="9478e-137">由 `migrations add InitialCreate` 命令生成。</span><span class="sxs-lookup"><span data-stu-id="9478e-137">Was generated by the `migrations add InitialCreate` command.</span></span> 
* <span data-ttu-id="9478e-138">由 `database update` 命令执行。</span><span class="sxs-lookup"><span data-stu-id="9478e-138">Is executed by the `database update` command.</span></span>
* <span data-ttu-id="9478e-139">为数据库上下文类指定的数据模型创建数据库。</span><span class="sxs-lookup"><span data-stu-id="9478e-139">Creates a database for the data model specified by the database context class.</span></span>

<span data-ttu-id="9478e-140">迁移名称参数（本示例中为“InitialCreate”）用于指定文件名。</span><span class="sxs-lookup"><span data-stu-id="9478e-140">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="9478e-141">迁移名称可以是任何有效的文件名。</span><span class="sxs-lookup"><span data-stu-id="9478e-141">The migration name can be any valid file name.</span></span> <span data-ttu-id="9478e-142">最好选择能概括迁移中所执行操作的字词或短语。</span><span class="sxs-lookup"><span data-stu-id="9478e-142">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="9478e-143">例如，添加了系表的迁移可称为“AddDepartmentTable”。</span><span class="sxs-lookup"><span data-stu-id="9478e-143">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

## <a name="the-migrations-history-table"></a><span data-ttu-id="9478e-144">迁移历史记录表</span><span class="sxs-lookup"><span data-stu-id="9478e-144">The migrations history table</span></span>

* <span data-ttu-id="9478e-145">使用 SSOX 或 SQLite 工具检查数据库。</span><span class="sxs-lookup"><span data-stu-id="9478e-145">Use SSOX or your SQLite tool to inspect the database.</span></span>
* <span data-ttu-id="9478e-146">请注意，增加了 `__EFMigrationsHistory` 表。</span><span class="sxs-lookup"><span data-stu-id="9478e-146">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="9478e-147">`__EFMigrationsHistory` 表跟踪已应用到数据库的迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-147">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the database.</span></span>
* <span data-ttu-id="9478e-148">查看 `__EFMigrationsHistory` 表中的数据。</span><span class="sxs-lookup"><span data-stu-id="9478e-148">View the data in the `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="9478e-149">它显示第一次迁移的行。</span><span class="sxs-lookup"><span data-stu-id="9478e-149">It shows one row for the first migration.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="9478e-150">数据模型快照</span><span class="sxs-lookup"><span data-stu-id="9478e-150">The data model snapshot</span></span>

<span data-ttu-id="9478e-151">迁移会在 Migrations/SchoolContextModelSnapshot.cs 中创建当前数据模型的快照   。</span><span class="sxs-lookup"><span data-stu-id="9478e-151">Migrations creates a *snapshot* of the current data model in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="9478e-152">添加迁移时，EF 会通过将当前数据模型与快照文件进行对比来确定已更改的内容。</span><span class="sxs-lookup"><span data-stu-id="9478e-152">When you add a migration, EF determines what changed by comparing the current data model to the snapshot file.</span></span>

<span data-ttu-id="9478e-153">由于快照文件跟踪数据模型的状态，因此不能通过删除 `<timestamp>_<migrationname>.cs` 文件来删除迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-153">Because the snapshot file tracks the state of the data model, you can't delete a migration by deleting the `<timestamp>_<migrationname>.cs` file.</span></span> <span data-ttu-id="9478e-154">要返回最近的迁移，必须使用 `migrations remove` 命令。</span><span class="sxs-lookup"><span data-stu-id="9478e-154">To back out the most recent migration, you have to use the `migrations remove` command.</span></span> <span data-ttu-id="9478e-155">该命令删除迁移并确保正确重置快照。</span><span class="sxs-lookup"><span data-stu-id="9478e-155">That command deletes the migration and ensures the snapshot is correctly reset.</span></span> <span data-ttu-id="9478e-156">有关详细信息，请参阅 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)。</span><span class="sxs-lookup"><span data-stu-id="9478e-156">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

## <a name="remove-ensurecreated"></a><span data-ttu-id="9478e-157">删除 EnsureCreated</span><span class="sxs-lookup"><span data-stu-id="9478e-157">Remove EnsureCreated</span></span>

<span data-ttu-id="9478e-158">本系列教程从使用 `EnsureCreated` 开始。</span><span class="sxs-lookup"><span data-stu-id="9478e-158">This tutorial series started by using `EnsureCreated`.</span></span> <span data-ttu-id="9478e-159">`EnsureCreated` 不创建迁移历史记录表，因此不能与迁移一起使用。</span><span class="sxs-lookup"><span data-stu-id="9478e-159">`EnsureCreated` doesn't create a migrations history table and so can't be used with migrations.</span></span> <span data-ttu-id="9478e-160">它专门用于在频繁删除并重新创建 DB 的情况下进行测试或快速制作原型。</span><span class="sxs-lookup"><span data-stu-id="9478e-160">It's designed for testing or rapid prototyping where the database is dropped and re-created frequently.</span></span>

<span data-ttu-id="9478e-161">从这个角度来看，教程将使用迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-161">From this point forward, the tutorials will use migrations.</span></span>

<span data-ttu-id="9478e-162">在 Data/DBInitializer.cs 中，注释掉以下行  ：</span><span class="sxs-lookup"><span data-stu-id="9478e-162">In *Data/DBInitializer.cs*, comment out the following line:</span></span>

```csharp
context.Database.EnsureCreated();
```
<span data-ttu-id="9478e-163">运行应用并验证数据库是否已设定种子。</span><span class="sxs-lookup"><span data-stu-id="9478e-163">Run the app and verify that the database is seeded.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="9478e-164">在生产环境中应用迁移</span><span class="sxs-lookup"><span data-stu-id="9478e-164">Applying migrations in production</span></span>

<span data-ttu-id="9478e-165">不建议生产应用在应用程序启动时调用 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)  。</span><span class="sxs-lookup"><span data-stu-id="9478e-165">We recommend that production apps **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="9478e-166">`Migrate` 不应从部署到服务器场的应用中调用。</span><span class="sxs-lookup"><span data-stu-id="9478e-166">`Migrate` shouldn't be called from an app that is deployed to a server farm.</span></span> <span data-ttu-id="9478e-167">如果应用横向扩展到多个服务器实例，则很难确保多个服务器不会发生数据库架构更新，或者这些更新不会与读/写访问冲突。</span><span class="sxs-lookup"><span data-stu-id="9478e-167">If the app is scaled out to multiple server instances, it's hard to ensure database schema updates don't happen from multiple servers or conflict with read/write access.</span></span>

<span data-ttu-id="9478e-168">应在部署过程中以受控的方式执行数据库迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-168">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="9478e-169">生产数据库迁移方法包括：</span><span class="sxs-lookup"><span data-stu-id="9478e-169">Production database migration approaches include:</span></span>

* <span data-ttu-id="9478e-170">使用迁移创建 SQL 脚本，并在部署过程中使用 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="9478e-170">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="9478e-171">在受控的环境中运行 `dotnet ef database update`。</span><span class="sxs-lookup"><span data-stu-id="9478e-171">Running `dotnet ef database update` from a controlled environment.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="9478e-172">疑难解答</span><span class="sxs-lookup"><span data-stu-id="9478e-172">Troubleshooting</span></span>

<span data-ttu-id="9478e-173">如果应用使用 SQL Server LocalDB 并显示以下异常：</span><span class="sxs-lookup"><span data-stu-id="9478e-173">If the app uses SQL Server LocalDB and displays the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="9478e-174">解决方案可能是在命令提示符下运行 `dotnet ef database update`。</span><span class="sxs-lookup"><span data-stu-id="9478e-174">The solution may be to run `dotnet ef database update` at a command prompt.</span></span>

### <a name="additional-resources"></a><span data-ttu-id="9478e-175">其他资源</span><span class="sxs-lookup"><span data-stu-id="9478e-175">Additional resources</span></span>

* <span data-ttu-id="9478e-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="9478e-176">[EF Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="9478e-177">包管理器控制台 (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="9478e-177">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)

## <a name="next-steps"></a><span data-ttu-id="9478e-178">后续步骤</span><span class="sxs-lookup"><span data-stu-id="9478e-178">Next steps</span></span>

<span data-ttu-id="9478e-179">下一个教程将生成数据模型，并添加实体属性和新实体。</span><span class="sxs-lookup"><span data-stu-id="9478e-179">The next tutorial builds out the data model, adding entity properties and new entities.</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="9478e-180">[上一个教程](xref:data/ef-rp/sort-filter-page)
> [下一个教程](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="9478e-180">[Previous tutorial](xref:data/ef-rp/sort-filter-page)
[Next tutorial](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="9478e-181">本教程使用 EF Core 迁移功能管理数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="9478e-181">In this tutorial, the EF Core migrations feature for managing data model changes is used.</span></span>

<span data-ttu-id="9478e-182">如果遇到无法解决的问题，请下载[已完成应用](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)。</span><span class="sxs-lookup"><span data-stu-id="9478e-182">If you run into problems you can't solve, download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples).</span></span>

<span data-ttu-id="9478e-183">开发新应用时，数据模型会频繁更改。</span><span class="sxs-lookup"><span data-stu-id="9478e-183">When a new app is developed, the data model changes frequently.</span></span> <span data-ttu-id="9478e-184">每当模型发生更改时，都无法与数据库进行同步。</span><span class="sxs-lookup"><span data-stu-id="9478e-184">Each time the model changes, the model gets out of sync with the database.</span></span> <span data-ttu-id="9478e-185">本教程首先配置 Entity Framework 以创建数据库（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="9478e-185">This tutorial started by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="9478e-186">每当数据模型发生更改时：</span><span class="sxs-lookup"><span data-stu-id="9478e-186">Each time the data model changes:</span></span>

* <span data-ttu-id="9478e-187">DB 都会被删除。</span><span class="sxs-lookup"><span data-stu-id="9478e-187">The DB is dropped.</span></span>
* <span data-ttu-id="9478e-188">EF 都会创建一个新数据库来匹配该模型。</span><span class="sxs-lookup"><span data-stu-id="9478e-188">EF creates a new one that matches the model.</span></span>
* <span data-ttu-id="9478e-189">应用使用测试数据为 DB 设定种子。</span><span class="sxs-lookup"><span data-stu-id="9478e-189">The app seeds the DB with test data.</span></span>

<span data-ttu-id="9478e-190">这种使 DB 与数据模型保持同步的方法适用于多种情况，但将应用部署到生产环境的情况除外。</span><span class="sxs-lookup"><span data-stu-id="9478e-190">This approach to keeping the DB in sync with the data model works well until you deploy the app to production.</span></span> <span data-ttu-id="9478e-191">当应用在生产环境中运行时，应用通常会存储需要保留的数据。</span><span class="sxs-lookup"><span data-stu-id="9478e-191">When the app is running in production, it's usually storing data that needs to be maintained.</span></span> <span data-ttu-id="9478e-192">每当发生更改（例如添加新列）时，应用都无法在具有测试 DB 的环境下启动。</span><span class="sxs-lookup"><span data-stu-id="9478e-192">The app can't start with a test DB each time a change is made (such as adding a new column).</span></span> <span data-ttu-id="9478e-193">EF Core 迁移功能可通过使 EF Core 更新 DB 架构而不是创建新 DB 来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="9478e-193">The EF Core Migrations feature solves this problem by enabling EF Core to update the DB schema instead of creating a new DB.</span></span>

<span data-ttu-id="9478e-194">数据模型发生更改时，迁移将更新架构并保留现有数据，而无需删除或重新创建 DB。</span><span class="sxs-lookup"><span data-stu-id="9478e-194">Rather than dropping and recreating the DB when the data model changes, migrations updates the schema and retains existing data.</span></span>

## <a name="drop-the-database"></a><span data-ttu-id="9478e-195">删除数据库</span><span class="sxs-lookup"><span data-stu-id="9478e-195">Drop the database</span></span>

<span data-ttu-id="9478e-196">使用 SQL Server 对象资源管理器 (SSOX) 或 `database drop` 命令  ：</span><span class="sxs-lookup"><span data-stu-id="9478e-196">Use **SQL Server Object Explorer** (SSOX) or the `database drop` command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9478e-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9478e-197">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9478e-198">在“包管理器控制台”(PMC) 中运行以下命令  ：</span><span class="sxs-lookup"><span data-stu-id="9478e-198">In the **Package Manager Console** (PMC), run the following command:</span></span>

```PMC
Drop-Database
```

<span data-ttu-id="9478e-199">从 PMC 运行 `Get-Help about_EntityFrameworkCore`，获取帮助信息。</span><span class="sxs-lookup"><span data-stu-id="9478e-199">Run `Get-Help about_EntityFrameworkCore` from the PMC to get help information.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9478e-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9478e-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="9478e-201">打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="9478e-201">Open a command window and navigate to the project folder.</span></span> <span data-ttu-id="9478e-202">项目文件夹包含 Startup.cs 文件  。</span><span class="sxs-lookup"><span data-stu-id="9478e-202">The project folder contains the *Startup.cs* file.</span></span>

<span data-ttu-id="9478e-203">在命令窗口中输入以下内容：</span><span class="sxs-lookup"><span data-stu-id="9478e-203">Enter the following in the command window:</span></span>

 ```dotnetcli
 dotnet ef database drop
 ```

---

## <a name="create-an-initial-migration-and-update-the-db"></a><span data-ttu-id="9478e-204">创建初始迁移并更新 DB</span><span class="sxs-lookup"><span data-stu-id="9478e-204">Create an initial migration and update the DB</span></span>

<span data-ttu-id="9478e-205">生成项目并创建第一个迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-205">Build the project and create the first migration.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9478e-206">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9478e-206">Visual Studio</span></span>](#tab/visual-studio)

```PMC
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9478e-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9478e-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations add InitialCreate
dotnet ef database update
```

---

### <a name="examine-the-up-and-down-methods"></a><span data-ttu-id="9478e-208">了解 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="9478e-208">Examine the Up and Down methods</span></span>

<span data-ttu-id="9478e-209">EF Core `migrations add` 命令已生成用于创建 DB 的代码。</span><span class="sxs-lookup"><span data-stu-id="9478e-209">The EF Core `migrations add` command  generated code to create the DB.</span></span> <span data-ttu-id="9478e-210">此迁移代码位于 Migrations\<timestamp>_InitialCreate.cs 文件中  。</span><span class="sxs-lookup"><span data-stu-id="9478e-210">This migrations code is in the *Migrations\<timestamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="9478e-211">`InitialCreate` 类的 `Up` 的方法创建与数据模型实体集相对应的 DB 表。</span><span class="sxs-lookup"><span data-stu-id="9478e-211">The `Up` method of the `InitialCreate` class creates the DB tables that correspond to the data model entity sets.</span></span> <span data-ttu-id="9478e-212">`Down` 方法删除这些表，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="9478e-212">The `Down` method deletes them, as shown in the following example:</span></span>

[!code-csharp[](intro/samples/cu21/Migrations/20180626224812_InitialCreate.cs?range=7-24,77-88)]

<span data-ttu-id="9478e-213">迁移调用 `Up` 方法为迁移实现数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="9478e-213">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="9478e-214">输入用于回退更新的命令时，迁移调用 `Down` 方法。</span><span class="sxs-lookup"><span data-stu-id="9478e-214">When you enter a command to roll back the update, migrations calls the `Down` method.</span></span>

<span data-ttu-id="9478e-215">前面的代码适用于初始迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-215">The preceding code is for the initial migration.</span></span> <span data-ttu-id="9478e-216">该代码是运行 `migrations add InitialCreate` 命令时创建的。</span><span class="sxs-lookup"><span data-stu-id="9478e-216">That code was created when the `migrations add InitialCreate` command was run.</span></span> <span data-ttu-id="9478e-217">迁移名称参数（本示例中为“InitialCreate”）用于指定文件名。</span><span class="sxs-lookup"><span data-stu-id="9478e-217">The migration name parameter ("InitialCreate" in the example) is used for the file name.</span></span> <span data-ttu-id="9478e-218">迁移名称可以是任何有效的文件名。</span><span class="sxs-lookup"><span data-stu-id="9478e-218">The migration name can be any valid file name.</span></span> <span data-ttu-id="9478e-219">最好选择能概括迁移中所执行操作的字词或短语。</span><span class="sxs-lookup"><span data-stu-id="9478e-219">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="9478e-220">例如，添加了系表的迁移可称为“AddDepartmentTable”。</span><span class="sxs-lookup"><span data-stu-id="9478e-220">For example, a migration that added a department table might be called "AddDepartmentTable."</span></span>

<span data-ttu-id="9478e-221">如果创建了初始迁移并且存在 DB：</span><span class="sxs-lookup"><span data-stu-id="9478e-221">If the initial migration is created and the DB exists:</span></span>

* <span data-ttu-id="9478e-222">会生成 DB 创建代码。</span><span class="sxs-lookup"><span data-stu-id="9478e-222">The DB creation code is generated.</span></span>
* <span data-ttu-id="9478e-223">DB 创建代码不需要运行，因为 DB 已与数据模型相匹配。</span><span class="sxs-lookup"><span data-stu-id="9478e-223">The DB creation code doesn't need to run because the DB already matches the data model.</span></span> <span data-ttu-id="9478e-224">即使 DB 创建代码运行也不会做出任何更改，因为 DB 已与数据模型相匹配。</span><span class="sxs-lookup"><span data-stu-id="9478e-224">If the DB creation code is run, it doesn't make any changes because the DB already matches the data model.</span></span>

<span data-ttu-id="9478e-225">如果将应用部署到新环境，则必须运行 DB 创建代码才能创建 DB。</span><span class="sxs-lookup"><span data-stu-id="9478e-225">When the app is deployed to a new environment, the DB creation code must be run to create the DB.</span></span>

<span data-ttu-id="9478e-226">先前删除了 DB，因此已不存在，所以迁移会创建新的 DB。</span><span class="sxs-lookup"><span data-stu-id="9478e-226">Previously the DB was dropped and doesn't exist, so migrations creates the new DB.</span></span>

### <a name="the-data-model-snapshot"></a><span data-ttu-id="9478e-227">数据模型快照</span><span class="sxs-lookup"><span data-stu-id="9478e-227">The data model snapshot</span></span>

<span data-ttu-id="9478e-228">迁移在 Migrations/SchoolContextModelSnapshot.cs 中创建当前数据库架构的快照   。</span><span class="sxs-lookup"><span data-stu-id="9478e-228">Migrations create a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="9478e-229">添加迁移时，EF 会通过将数据模型与快照文件进行对比来确定已更改的内容。</span><span class="sxs-lookup"><span data-stu-id="9478e-229">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="9478e-230">若要删除迁移，请使用以下命令：</span><span class="sxs-lookup"><span data-stu-id="9478e-230">To delete a migration, use the following command:</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="9478e-231">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="9478e-231">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="9478e-232">Remove-Migration</span><span class="sxs-lookup"><span data-stu-id="9478e-232">Remove-Migration</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="9478e-233">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="9478e-233">Visual Studio Code</span></span>](#tab/visual-studio-code)

```dotnetcli
dotnet ef migrations remove
```

<span data-ttu-id="9478e-234">有关详细信息，请参阅 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove)。</span><span class="sxs-lookup"><span data-stu-id="9478e-234">For more information, see [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove).</span></span>

---

<span data-ttu-id="9478e-235">删除迁移命令会删除迁移并确保正确重置快照。</span><span class="sxs-lookup"><span data-stu-id="9478e-235">The remove migrations command deletes the migration and ensures the snapshot is correctly reset.</span></span>

### <a name="remove-ensurecreated-and-test-the-app"></a><span data-ttu-id="9478e-236">删除 EnsureCreated 并测试应用</span><span class="sxs-lookup"><span data-stu-id="9478e-236">Remove EnsureCreated and test the app</span></span>

<span data-ttu-id="9478e-237">早期开发使用了 `EnsureCreated`。</span><span class="sxs-lookup"><span data-stu-id="9478e-237">For early development, `EnsureCreated` was used.</span></span> <span data-ttu-id="9478e-238">本教程将使用迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-238">In this tutorial, migrations are used.</span></span> <span data-ttu-id="9478e-239">`EnsureCreated` 具有以下限制：</span><span class="sxs-lookup"><span data-stu-id="9478e-239">`EnsureCreated` has the following limitations:</span></span>

* <span data-ttu-id="9478e-240">绕过迁移并创建 DB 和架构。</span><span class="sxs-lookup"><span data-stu-id="9478e-240">Bypasses migrations and creates the DB and schema.</span></span>
* <span data-ttu-id="9478e-241">不会创建迁移表。</span><span class="sxs-lookup"><span data-stu-id="9478e-241">Doesn't create a migrations table.</span></span>
* <span data-ttu-id="9478e-242">不能与迁移一起使用  。</span><span class="sxs-lookup"><span data-stu-id="9478e-242">Can *not* be used with migrations.</span></span>
* <span data-ttu-id="9478e-243">专门用于在频繁删除并重新创建 DB 的情况下进行测试或快速制作原型。</span><span class="sxs-lookup"><span data-stu-id="9478e-243">Is designed for testing or rapid prototyping where the DB is dropped and re-created frequently.</span></span>

<span data-ttu-id="9478e-244">删除 `EnsureCreated`：</span><span class="sxs-lookup"><span data-stu-id="9478e-244">Remove `EnsureCreated`:</span></span>

```csharp
context.Database.EnsureCreated();
```

<span data-ttu-id="9478e-245">运行应用并验证 DB 设定为种子。</span><span class="sxs-lookup"><span data-stu-id="9478e-245">Run the app and verify the DB is seeded.</span></span>

### <a name="inspect-the-database"></a><span data-ttu-id="9478e-246">检查数据库</span><span class="sxs-lookup"><span data-stu-id="9478e-246">Inspect the database</span></span>

<span data-ttu-id="9478e-247">使用 SQL Server 对象资源管理器检查 DB  。</span><span class="sxs-lookup"><span data-stu-id="9478e-247">Use **SQL Server Object Explorer** to inspect the DB.</span></span> <span data-ttu-id="9478e-248">请注意，增加了 `__EFMigrationsHistory` 表。</span><span class="sxs-lookup"><span data-stu-id="9478e-248">Notice the addition of an `__EFMigrationsHistory` table.</span></span> <span data-ttu-id="9478e-249">`__EFMigrationsHistory` 表跟踪已应用到 DB 的迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-249">The `__EFMigrationsHistory` table keeps track of which migrations have been applied to the DB.</span></span> <span data-ttu-id="9478e-250">查看 `__EFMigrationsHistory` 表中的数据，其中显示对应初始迁移的一行数据。</span><span class="sxs-lookup"><span data-stu-id="9478e-250">View the data in the `__EFMigrationsHistory` table, it shows one row for the first migration.</span></span> <span data-ttu-id="9478e-251">上面的 CLI 输出示例中最后部分的日志显示了创建此行的 INSERT 语句。</span><span class="sxs-lookup"><span data-stu-id="9478e-251">The last log in the preceding CLI output example shows the INSERT statement that creates this row.</span></span>

<span data-ttu-id="9478e-252">运行应用并验证一切正常运行。</span><span class="sxs-lookup"><span data-stu-id="9478e-252">Run the app and verify that everything works.</span></span>

## <a name="applying-migrations-in-production"></a><span data-ttu-id="9478e-253">在生产环境中应用迁移</span><span class="sxs-lookup"><span data-stu-id="9478e-253">Applying migrations in production</span></span>

<span data-ttu-id="9478e-254">不建议生产应用在应用程序启动时调用 [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_)  。</span><span class="sxs-lookup"><span data-stu-id="9478e-254">We recommend production apps should **not** call [Database.Migrate](/dotnet/api/microsoft.entityframeworkcore.relationaldatabasefacadeextensions.migrate?view=efcore-2.0#Microsoft_EntityFrameworkCore_RelationalDatabaseFacadeExtensions_Migrate_Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_) at application startup.</span></span> <span data-ttu-id="9478e-255">不应从服务器场中的应用调用 `Migrate`。</span><span class="sxs-lookup"><span data-stu-id="9478e-255">`Migrate` shouldn't be called from an app in server farm.</span></span> <span data-ttu-id="9478e-256">例如，已将应用在云中部署为横向扩展（运行应用的多个示例）的情况。</span><span class="sxs-lookup"><span data-stu-id="9478e-256">For example, if the app has been cloud deployed with scale-out (multiple instances of the app are running).</span></span>

<span data-ttu-id="9478e-257">应在部署过程中以受控的方式执行数据库迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-257">Database migration should be done as part of deployment, and in a controlled way.</span></span> <span data-ttu-id="9478e-258">生产数据库迁移方法包括：</span><span class="sxs-lookup"><span data-stu-id="9478e-258">Production database migration approaches include:</span></span>

* <span data-ttu-id="9478e-259">使用迁移创建 SQL 脚本，并在部署过程中使用 SQL 脚本。</span><span class="sxs-lookup"><span data-stu-id="9478e-259">Using migrations to create SQL scripts and using the SQL scripts in deployment.</span></span>
* <span data-ttu-id="9478e-260">在受控的环境中运行 `dotnet ef database update`。</span><span class="sxs-lookup"><span data-stu-id="9478e-260">Running `dotnet ef database update` from a controlled environment.</span></span>

<span data-ttu-id="9478e-261">EF Core 使用 `__MigrationsHistory` 表查看是否需要运行任何迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-261">EF Core uses the `__MigrationsHistory` table to see if any migrations need to run.</span></span> <span data-ttu-id="9478e-262">如果 DB 已是最新，则无需运行迁移。</span><span class="sxs-lookup"><span data-stu-id="9478e-262">If the DB is up-to-date, no migration is run.</span></span>

## <a name="troubleshooting"></a><span data-ttu-id="9478e-263">疑难解答</span><span class="sxs-lookup"><span data-stu-id="9478e-263">Troubleshooting</span></span>

<span data-ttu-id="9478e-264">下载[已完成应用](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations)。</span><span class="sxs-lookup"><span data-stu-id="9478e-264">Download the [completed app](
https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples/cu21snapshots/cu-part4-migrations).</span></span>

<span data-ttu-id="9478e-265">应用会生成以下异常：</span><span class="sxs-lookup"><span data-stu-id="9478e-265">The app generates the following exception:</span></span>

```text
SqlException: Cannot open database "ContosoUniversity" requested by the login.
The login failed.
Login failed for user 'user name'.
```

<span data-ttu-id="9478e-266">解决方案：运行 `dotnet ef database update`</span><span class="sxs-lookup"><span data-stu-id="9478e-266">Solution: Run `dotnet ef database update`</span></span>

### <a name="additional-resources"></a><span data-ttu-id="9478e-267">其他资源</span><span class="sxs-lookup"><span data-stu-id="9478e-267">Additional resources</span></span>

* [<span data-ttu-id="9478e-268">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="9478e-268">YouTube version of this tutorial</span></span>](https://www.youtube.com/watch?v=OWSUuMLKTJo)
* <span data-ttu-id="9478e-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span><span class="sxs-lookup"><span data-stu-id="9478e-269">[.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>
* [<span data-ttu-id="9478e-270">包管理器控制台 (Visual Studio)</span><span class="sxs-lookup"><span data-stu-id="9478e-270">Package Manager Console (Visual Studio)</span></span>](/ef/core/miscellaneous/cli/powershell)



> [!div class="step-by-step"]
> <span data-ttu-id="9478e-271">[上一页](xref:data/ef-rp/sort-filter-page)
> [下一页](xref:data/ef-rp/complex-data-model)</span><span class="sxs-lookup"><span data-stu-id="9478e-271">[Previous](xref:data/ef-rp/sort-filter-page)
[Next](xref:data/ef-rp/complex-data-model)</span></span>

::: moniker-end

