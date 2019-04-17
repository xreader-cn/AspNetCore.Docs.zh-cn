---
title: 教程：使用迁移功能 - ASP.NET MVC 和 EF Core
description: 本教程使用 EF Core 迁移功能管理 ASP.NET Core MVC 应用程序中的数据模型更改。
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/27/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 3d2ae12bf8eda4f7997008758d4d29434a8371a7
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012599"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="9f44e-103">教程：使用迁移功能 - ASP.NET MVC 和 EF Core</span><span class="sxs-lookup"><span data-stu-id="9f44e-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="9f44e-104">本教程使用 EF Core 迁移功能管理数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="9f44e-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="9f44e-105">后续教程将在更改数据模型时添加更多迁移。</span><span class="sxs-lookup"><span data-stu-id="9f44e-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="9f44e-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="9f44e-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9f44e-107">了解迁移相关信息</span><span class="sxs-lookup"><span data-stu-id="9f44e-107">Learn about migrations</span></span>
> * <span data-ttu-id="9f44e-108">更改连接字符串</span><span class="sxs-lookup"><span data-stu-id="9f44e-108">Change the connection string</span></span>
> * <span data-ttu-id="9f44e-109">创建初始迁移</span><span class="sxs-lookup"><span data-stu-id="9f44e-109">Create an initial migration</span></span>
> * <span data-ttu-id="9f44e-110">检查 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="9f44e-110">Examine Up and Down methods</span></span>
> * <span data-ttu-id="9f44e-111">了解数据模型快照</span><span class="sxs-lookup"><span data-stu-id="9f44e-111">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="9f44e-112">应用迁移</span><span class="sxs-lookup"><span data-stu-id="9f44e-112">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9f44e-113">系统必备</span><span class="sxs-lookup"><span data-stu-id="9f44e-113">Prerequisites</span></span>

* [<span data-ttu-id="9f44e-114">排序、筛选和分页</span><span class="sxs-lookup"><span data-stu-id="9f44e-114">Sorting, filtering, and paging</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="9f44e-115">关于迁移</span><span class="sxs-lookup"><span data-stu-id="9f44e-115">About migrations</span></span>

<span data-ttu-id="9f44e-116">开发新应用程序时，数据模型会频繁更改。每当模型更改时，模型都无法与数据库保持同步。</span><span class="sxs-lookup"><span data-stu-id="9f44e-116">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="9f44e-117">本系列教程首先配置 Entity Framework 以创建数据库（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="9f44e-117">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="9f44e-118">之后，每当更改数据模型（添加、删除或更改实体类或更改 DbContext 类）时，你都可以删除数据库，EF 将创建匹配该模型的新数据库并用测试数据为其设定种子。</span><span class="sxs-lookup"><span data-stu-id="9f44e-118">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="9f44e-119">这种使数据库与数据模型保持同步的方法适用于多种情况，但将应用程序部署到生产环境的情况除外。</span><span class="sxs-lookup"><span data-stu-id="9f44e-119">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="9f44e-120">当应用程序在生产环境中运行时，它通常会存储要保留的数据，以便不会在每次更改（如添加新列）时丢失所有数据。</span><span class="sxs-lookup"><span data-stu-id="9f44e-120">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="9f44e-121">EF Core 迁移功能可通过使 EF 更新数据库 架构而不是创建新数据库来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="9f44e-121">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

<span data-ttu-id="9f44e-122">要使用迁移，可使用“包管理器控制台”(PMC) 或命令行接口 (CLI)。</span><span class="sxs-lookup"><span data-stu-id="9f44e-122">To work with migrations, you can use the **Package Manager Console** (PMC) or the command-line interface (CLI).</span></span>  <span data-ttu-id="9f44e-123">以下教程演示如何使用 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="9f44e-123">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="9f44e-124">有关 PMC 的信息，请转到[本教程末尾](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="9f44e-124">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="9f44e-125">更改连接字符串</span><span class="sxs-lookup"><span data-stu-id="9f44e-125">Change the connection string</span></span>

<span data-ttu-id="9f44e-126">在 appsettings.json 文件中，将连接字符串中的数据库的名称更改为 ContosoUniversity2 或正在使用的计算机上未使用过的其他名称。</span><span class="sxs-lookup"><span data-stu-id="9f44e-126">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="9f44e-127">此更改将设置项目，以便初始迁移创建新的数据库。</span><span class="sxs-lookup"><span data-stu-id="9f44e-127">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="9f44e-128">这并不是开始使用迁移的必要操作，但稍后你便会了解这样做的好处。</span><span class="sxs-lookup"><span data-stu-id="9f44e-128">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="9f44e-129">除更改数据库名称外，删除数据库同样可行。</span><span class="sxs-lookup"><span data-stu-id="9f44e-129">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="9f44e-130">使用 SQL Server 对象资源管理器 (SSOX) 或 `database drop` CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="9f44e-130">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```console
> dotnet ef database drop
> ```
>
> <span data-ttu-id="9f44e-131">下面的部分说明如何运行 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="9f44e-131">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="9f44e-132">创建初始迁移</span><span class="sxs-lookup"><span data-stu-id="9f44e-132">Create an initial migration</span></span>

<span data-ttu-id="9f44e-133">保存更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="9f44e-133">Save your changes and build the project.</span></span> <span data-ttu-id="9f44e-134">然后打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="9f44e-134">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="9f44e-135">下面是执行此操作的快速方法：</span><span class="sxs-lookup"><span data-stu-id="9f44e-135">Here's a quick way to do that:</span></span>

* <span data-ttu-id="9f44e-136">在解决方案资源管理器中，右键单击项目，然后从上下文菜单中选择“在文件资源管理器中打开文件夹”。</span><span class="sxs-lookup"><span data-stu-id="9f44e-136">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![“在文件资源管理器中打开”菜单项](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="9f44e-138">在地址栏中输入“cmd”，然后按 Enter。</span><span class="sxs-lookup"><span data-stu-id="9f44e-138">Enter "cmd" in the address bar and press Enter.</span></span>

  ![打开命令窗口](migrations/_static/open-command-window.png)

<span data-ttu-id="9f44e-140">在命令窗口中输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="9f44e-140">Enter the following command in the command window:</span></span>

```console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="9f44e-141">命令窗口中出现如下输出：</span><span class="sxs-lookup"><span data-stu-id="9f44e-141">You see output like the following in the command window:</span></span>

```console
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="9f44e-142">如果出现错误消息“找不到任何匹配 "dotnet-ef" 命令的可执行文件”，请参阅[此博客文章](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/)获取故障排除帮助。</span><span class="sxs-lookup"><span data-stu-id="9f44e-142">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="9f44e-143">如果看到错误消息“无法访问文件...ContosoUniversity.dll，因为它正被另一个进程使用。”，请在 Windows 系统托盘中找到 IIS Express 图标并右键单击，然后单击“ContosoUniversity”>“停止站点”。</span><span class="sxs-lookup"><span data-stu-id="9f44e-143">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="9f44e-144">检查 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="9f44e-144">Examine Up and Down methods</span></span>

<span data-ttu-id="9f44e-145">执行 `migrations add` 命令时，EF 已生成将用于从头创建数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="9f44e-145">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="9f44e-146">此代码位于“Migrations”文件夹中名为 \<timestamp>_InitialCreate.cs 的文件中。</span><span class="sxs-lookup"><span data-stu-id="9f44e-146">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="9f44e-147">`InitialCreate` 类的 `Up` 的方法将创建与数据模型实体集相对应的数据库表，`Down` 方法将删除这些表，如下面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="9f44e-147">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="9f44e-148">迁移调用 `Up` 方法为迁移实现数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="9f44e-148">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="9f44e-149">输入用于回退更新的命令时，迁移调用 `Down` 方法。</span><span class="sxs-lookup"><span data-stu-id="9f44e-149">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="9f44e-150">此代码适用于输入 `migrations add InitialCreate` 命令时所创建的初始迁移。</span><span class="sxs-lookup"><span data-stu-id="9f44e-150">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="9f44e-151">迁移名称参数（本示例中为“InitialCreate”）用于指定文件名，并且你可以按需使用任何名称。</span><span class="sxs-lookup"><span data-stu-id="9f44e-151">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="9f44e-152">最好选择能概括迁移中所执行操作的字词或短语。</span><span class="sxs-lookup"><span data-stu-id="9f44e-152">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="9f44e-153">例如，可将后面的迁移命名为“AddDepartmentTable”。</span><span class="sxs-lookup"><span data-stu-id="9f44e-153">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="9f44e-154">如果创建初始迁移时已存在数据库，则会生成数据库创建代码，但此代码不必运行，因为数据库已与数据库模型相匹配。</span><span class="sxs-lookup"><span data-stu-id="9f44e-154">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="9f44e-155">将应用部署到其中尚不存在数据库的其他环境时，此代码将运行以创建数据库，因此最好提前进行测试。</span><span class="sxs-lookup"><span data-stu-id="9f44e-155">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="9f44e-156">这也是提前更改连接字符串中数据库的名称的原因，这样迁移才能从头创建新数据库。</span><span class="sxs-lookup"><span data-stu-id="9f44e-156">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="9f44e-157">数据模型快照</span><span class="sxs-lookup"><span data-stu-id="9f44e-157">The data model snapshot</span></span>

<span data-ttu-id="9f44e-158">迁移在 Migrations/SchoolContextModelSnapshot.cs 中创建当前数据库架构的快照。</span><span class="sxs-lookup"><span data-stu-id="9f44e-158">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="9f44e-159">添加迁移时，EF 会通过将数据模型与快照文件进行对比来确定已更改的内容。</span><span class="sxs-lookup"><span data-stu-id="9f44e-159">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="9f44e-160">删除迁移时，请使用 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) 命令。</span><span class="sxs-lookup"><span data-stu-id="9f44e-160">When deleting a migration, use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command.</span></span> `dotnet ef migrations remove` <span data-ttu-id="9f44e-161">删除迁移，并确保正确重置快照。</span><span class="sxs-lookup"><span data-stu-id="9f44e-161">deletes the migration and ensures the snapshot is correctly reset.</span></span>

<span data-ttu-id="9f44e-162">有关如何使用快照文件的详细信息，请参阅[团队环境中的 EF Core 迁移](/ef/core/managing-schemas/migrations/teams)。</span><span class="sxs-lookup"><span data-stu-id="9f44e-162">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="9f44e-163">应用迁移</span><span class="sxs-lookup"><span data-stu-id="9f44e-163">Apply the migration</span></span>

<span data-ttu-id="9f44e-164">在命令窗口中，输入以下命令以创建数据库并在其中创建表。</span><span class="sxs-lookup"><span data-stu-id="9f44e-164">In the command window, enter the following command to create the database and tables in it.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="9f44e-165">该命令的输出与 `migrations add` 命令的输出相似，但其中还包含设置该数据库的 SQL 命令的日志。</span><span class="sxs-lookup"><span data-stu-id="9f44e-165">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="9f44e-166">下面的示例输出中省略了大部分日志。</span><span class="sxs-lookup"><span data-stu-id="9f44e-166">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="9f44e-167">如果希望日志消息中不使用此详细级别，则可更改 appsettings.Development.json 文件中的日志级别。</span><span class="sxs-lookup"><span data-stu-id="9f44e-167">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="9f44e-168">有关更多信息，请参见<xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="9f44e-168">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.EntityFrameworkCore.Infrastructure[10403]
      Entity Framework Core 2.2.0-rtm-35687 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (274ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (60ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      IF SERVERPROPERTY('EngineEdition') <> 5
      BEGIN
          ALTER DATABASE [ContosoUniversity2] SET READ_COMMITTED_SNAPSHOT ON;
      END;
info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (15ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[20101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20190327172701_InitialCreate', N'2.2.0-rtm-35687');
Done.
```

<span data-ttu-id="9f44e-169">使用 SQL Server 对象资源管理器检查数据库（与第一个教程中的做法相同）。</span><span class="sxs-lookup"><span data-stu-id="9f44e-169">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="9f44e-170">你会发现添加了 \_\_EFMigrationsHistory 表，该表可用于跟踪已应用到数据库的迁移。</span><span class="sxs-lookup"><span data-stu-id="9f44e-170">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="9f44e-171">查看该表中的数据，其中显示对应初始迁移的一行数据。</span><span class="sxs-lookup"><span data-stu-id="9f44e-171">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="9f44e-172">（上面的 CLI 输出示例中最后部分的日志显示了创建此行的 INSERT 语句。）</span><span class="sxs-lookup"><span data-stu-id="9f44e-172">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="9f44e-173">运行应用程序以验证所有内容照旧运行。</span><span class="sxs-lookup"><span data-stu-id="9f44e-173">Run the application to verify that everything still works the same as before.</span></span>

![“学生索引”页](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="9f44e-175">比较 CLI 和 PMC</span><span class="sxs-lookup"><span data-stu-id="9f44e-175">Compare CLI and PMC</span></span>

<span data-ttu-id="9f44e-176">可通过 .NET Core CLI 命令或 Visual Studio 包管理器控制台 (PMC) 窗口中的 PowerShell cmdlet 使用可管理迁移的 EF 工具。</span><span class="sxs-lookup"><span data-stu-id="9f44e-176">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="9f44e-177">本教程演示如何使用 CLI，但也可以根据喜好使用 PMC。</span><span class="sxs-lookup"><span data-stu-id="9f44e-177">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="9f44e-178">适用于 PMC 命令的 EF 命令位于 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) 程序包中。</span><span class="sxs-lookup"><span data-stu-id="9f44e-178">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="9f44e-179">此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中，因此，如果应用具有对 `Microsoft.AspNetCore.App` 的包引用，则无需添加包引用。</span><span class="sxs-lookup"><span data-stu-id="9f44e-179">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="9f44e-180">**重要提示：** 此包与通过编辑 .csproj 文件为 CLI 安装的包不同。</span><span class="sxs-lookup"><span data-stu-id="9f44e-180">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="9f44e-181">此程序包的名称以 `Tools` 结尾，而 CLI 程序包的名称以 `Tools.DotNet` 结尾。</span><span class="sxs-lookup"><span data-stu-id="9f44e-181">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="9f44e-182">有关 CLI 命令的详细信息，请参阅 [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="9f44e-182">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="9f44e-183">有关 PMC 命令的详细信息，请参阅[包管理器控制台 (Visual Studio)](/ef/core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="9f44e-183">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="9f44e-184">获取代码</span><span class="sxs-lookup"><span data-stu-id="9f44e-184">Get the code</span></span>

[<span data-ttu-id="9f44e-185">下载或查看已完成的应用。</span><span class="sxs-lookup"><span data-stu-id="9f44e-185">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="9f44e-186">下一步</span><span class="sxs-lookup"><span data-stu-id="9f44e-186">Next step</span></span>

<span data-ttu-id="9f44e-187">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="9f44e-187">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="9f44e-188">已了解迁移相关信息</span><span class="sxs-lookup"><span data-stu-id="9f44e-188">Learned about migrations</span></span>
> * <span data-ttu-id="9f44e-189">已了解 NuGet 迁移包</span><span class="sxs-lookup"><span data-stu-id="9f44e-189">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="9f44e-190">已更改连接字符串</span><span class="sxs-lookup"><span data-stu-id="9f44e-190">Changed the connection string</span></span>
> * <span data-ttu-id="9f44e-191">已创建初始迁移</span><span class="sxs-lookup"><span data-stu-id="9f44e-191">Created an initial migration</span></span>
> * <span data-ttu-id="9f44e-192">已检查 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="9f44e-192">Examined Up and Down methods</span></span>
> * <span data-ttu-id="9f44e-193">已了解数据模型快照</span><span class="sxs-lookup"><span data-stu-id="9f44e-193">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="9f44e-194">已应用迁移</span><span class="sxs-lookup"><span data-stu-id="9f44e-194">Applied the migration</span></span>

<span data-ttu-id="9f44e-195">请继续阅读下一篇教程，开始了解有关展开数据模型的更高级主题。</span><span class="sxs-lookup"><span data-stu-id="9f44e-195">Advance to the next tutorial to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="9f44e-196">同时还将介绍创建并应用其他迁移的方法。</span><span class="sxs-lookup"><span data-stu-id="9f44e-196">Along the way you'll create and apply additional migrations.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="9f44e-197">创建并应用其他迁移</span><span class="sxs-lookup"><span data-stu-id="9f44e-197">Create and apply additional migrations</span></span>](complex-data-model.md)
