---
title: 教程：使用迁移功能 - ASP.NET MVC 和 EF Core
description: 本教程使用 EF Core 迁移功能管理 ASP.NET Core MVC 应用程序中的数据模型更改。
author: rick-anderson
ms.author: tdykstra
ms.custom: mvc
ms.date: 02/04/2019
ms.topic: tutorial
uid: data/ef-mvc/migrations
ms.openlocfilehash: 6d4ed0e95499c30417e1cfd07f57de824a8a62ed
ms.sourcegitcommit: 57792e5f594db1574742588017c708350958bdf0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2019
ms.locfileid: "58265520"
---
# <a name="tutorial-using-the-migrations-feature---aspnet-mvc-with-ef-core"></a><span data-ttu-id="3c265-103">教程：使用迁移功能 - ASP.NET MVC 和 EF Core</span><span class="sxs-lookup"><span data-stu-id="3c265-103">Tutorial: Using the migrations feature - ASP.NET MVC with EF Core</span></span>

<span data-ttu-id="3c265-104">本教程使用 EF Core 迁移功能管理数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="3c265-104">In this tutorial, you start using the EF Core migrations feature for managing data model changes.</span></span> <span data-ttu-id="3c265-105">后续教程将在更改数据模型时添加更多迁移。</span><span class="sxs-lookup"><span data-stu-id="3c265-105">In later tutorials, you'll add more migrations as you change the data model.</span></span>

<span data-ttu-id="3c265-106">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="3c265-106">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c265-107">了解迁移相关信息</span><span class="sxs-lookup"><span data-stu-id="3c265-107">Learn about migrations</span></span>
> * <span data-ttu-id="3c265-108">了解 NuGet 迁移包</span><span class="sxs-lookup"><span data-stu-id="3c265-108">Learn about NuGet migration packages</span></span>
> * <span data-ttu-id="3c265-109">更改连接字符串</span><span class="sxs-lookup"><span data-stu-id="3c265-109">Change the connection string</span></span>
> * <span data-ttu-id="3c265-110">创建初始迁移</span><span class="sxs-lookup"><span data-stu-id="3c265-110">Create an initial migration</span></span>
> * <span data-ttu-id="3c265-111">检查 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="3c265-111">Examine Up and Down methods</span></span>
> * <span data-ttu-id="3c265-112">了解数据模型快照</span><span class="sxs-lookup"><span data-stu-id="3c265-112">Learn about the data model snapshot</span></span>
> * <span data-ttu-id="3c265-113">应用迁移</span><span class="sxs-lookup"><span data-stu-id="3c265-113">Apply the migration</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3c265-114">系统必备</span><span class="sxs-lookup"><span data-stu-id="3c265-114">Prerequisites</span></span>

* [<span data-ttu-id="3c265-115">在 ASP.NET Core MVC 应用中使用 EF Core 添加排序、筛选和分页</span><span class="sxs-lookup"><span data-stu-id="3c265-115">Add sorting, filtering, and paging with EF Core in an ASP.NET Core MVC app</span></span>](sort-filter-page.md)

## <a name="about-migrations"></a><span data-ttu-id="3c265-116">关于迁移</span><span class="sxs-lookup"><span data-stu-id="3c265-116">About migrations</span></span>

<span data-ttu-id="3c265-117">开发新应用程序时，数据模型会频繁更改。每当模型更改时，模型都无法与数据库保持同步。</span><span class="sxs-lookup"><span data-stu-id="3c265-117">When you develop a new application, your data model changes frequently, and each time the model changes, it gets out of sync with the database.</span></span> <span data-ttu-id="3c265-118">本系列教程首先配置 Entity Framework 以创建数据库（如果不存在）。</span><span class="sxs-lookup"><span data-stu-id="3c265-118">You started these tutorials by configuring the Entity Framework to create the database if it doesn't exist.</span></span> <span data-ttu-id="3c265-119">之后，每当更改数据模型（添加、删除或更改实体类或更改 DbContext 类）时，你都可以删除数据库，EF 将创建匹配该模型的新数据库并用测试数据为其设定种子。</span><span class="sxs-lookup"><span data-stu-id="3c265-119">Then each time you change the data model -- add, remove, or change entity classes or change your DbContext class -- you can delete the database and EF creates a new one that matches the model, and seeds it with test data.</span></span>

<span data-ttu-id="3c265-120">这种使数据库与数据模型保持同步的方法适用于多种情况，但将应用程序部署到生产环境的情况除外。</span><span class="sxs-lookup"><span data-stu-id="3c265-120">This method of keeping the database in sync with the data model works well until you deploy the application to production.</span></span> <span data-ttu-id="3c265-121">当应用程序在生产环境中运行时，它通常会存储要保留的数据，以便不会在每次更改（如添加新列）时丢失所有数据。</span><span class="sxs-lookup"><span data-stu-id="3c265-121">When the application is running in production it's usually storing data that you want to keep, and you don't want to lose everything each time you make a change such as adding a new column.</span></span> <span data-ttu-id="3c265-122">EF Core 迁移功能可通过使 EF 更新数据库 架构而不是创建新数据库来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="3c265-122">The EF Core Migrations feature solves this problem by enabling EF to update the database schema instead of creating  a new database.</span></span>

## <a name="about-nuget-migration-packages"></a><span data-ttu-id="3c265-123">关于 NuGet 迁移包</span><span class="sxs-lookup"><span data-stu-id="3c265-123">About NuGet migration packages</span></span>

<span data-ttu-id="3c265-124">要使用迁移，可使用“包管理器控制台”(PMC) 或命令行接口 (CLI)。</span><span class="sxs-lookup"><span data-stu-id="3c265-124">To work with migrations, you can use the **Package Manager Console** (PMC) or the command-line interface (CLI).</span></span>  <span data-ttu-id="3c265-125">以下教程演示如何使用 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="3c265-125">These tutorials show how to use CLI commands.</span></span> <span data-ttu-id="3c265-126">有关 PMC 的信息，请转到[本教程末尾](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="3c265-126">Information about the PMC is at [the end of this tutorial](#pmc).</span></span>

<span data-ttu-id="3c265-127">[Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet) 中提供了适用于命令行接口 (CLI) 的 EF 工具。</span><span class="sxs-lookup"><span data-stu-id="3c265-127">The EF tools for the command-line interface (CLI) are provided in [Microsoft.EntityFrameworkCore.Tools.DotNet](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools.DotNet).</span></span> <span data-ttu-id="3c265-128">若要安装此程序包，请将它添加到 .csproj 文件中的 `DotNetCliToolReference` 集合，如下所示。</span><span class="sxs-lookup"><span data-stu-id="3c265-128">To install this package, add it to the `DotNetCliToolReference` collection in the *.csproj* file, as shown.</span></span> <span data-ttu-id="3c265-129">**注意：** 必须通过编辑 .csproj 文件来安装此包；不能使用 `install-package` 命令或包管理器 GUI。</span><span class="sxs-lookup"><span data-stu-id="3c265-129">**Note:** You have to install this package by editing the *.csproj* file; you can't use the `install-package` command or the package manager GUI.</span></span> <span data-ttu-id="3c265-130">若要编辑 .csproj 文件，可右键单击解决方案资源管理器中的项目名称，然后选择“编辑 ContosoUniversity.csproj”。</span><span class="sxs-lookup"><span data-stu-id="3c265-130">You can edit the *.csproj* file by right-clicking the project name in **Solution Explorer** and selecting **Edit ContosoUniversity.csproj**.</span></span>

[!code-xml[](intro/samples/cu/ContosoUniversity.csproj?range=12-15&highlight=2)]

<span data-ttu-id="3c265-131">（编写本教程时，本示例中的版本号是最新的。）</span><span class="sxs-lookup"><span data-stu-id="3c265-131">(The version numbers in this example were current when the tutorial was written.)</span></span>

## <a name="change-the-connection-string"></a><span data-ttu-id="3c265-132">更改连接字符串</span><span class="sxs-lookup"><span data-stu-id="3c265-132">Change the connection string</span></span>

<span data-ttu-id="3c265-133">在 appsettings.json 文件中，将连接字符串中的数据库的名称更改为 ContosoUniversity2 或正在使用的计算机上未使用过的其他名称。</span><span class="sxs-lookup"><span data-stu-id="3c265-133">In the *appsettings.json* file, change the name of the database in the connection string to ContosoUniversity2 or some other name that you haven't used on the computer you're using.</span></span>

[!code-json[](intro/samples/cu/appsettings2.json?range=1-4)]

<span data-ttu-id="3c265-134">此更改将设置项目，以便初始迁移创建新的数据库。</span><span class="sxs-lookup"><span data-stu-id="3c265-134">This change sets up the project so that the first migration will create a new database.</span></span> <span data-ttu-id="3c265-135">这并不是开始使用迁移的必要操作，但稍后你便会了解这样做的好处。</span><span class="sxs-lookup"><span data-stu-id="3c265-135">This isn't required to get started with migrations, but you'll see later why it's a good idea.</span></span>

> [!NOTE]
> <span data-ttu-id="3c265-136">除更改数据库名称外，删除数据库同样可行。</span><span class="sxs-lookup"><span data-stu-id="3c265-136">As an alternative to changing the database name, you can delete the database.</span></span> <span data-ttu-id="3c265-137">使用 SQL Server 对象资源管理器 (SSOX) 或 `database drop` CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="3c265-137">Use **SQL Server Object Explorer** (SSOX) or the `database drop` CLI command:</span></span>
>
> ```console
> dotnet ef database drop
> ```
> <span data-ttu-id="3c265-138">下面的部分说明如何运行 CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="3c265-138">The following section explains how to run CLI commands.</span></span>

## <a name="create-an-initial-migration"></a><span data-ttu-id="3c265-139">创建初始迁移</span><span class="sxs-lookup"><span data-stu-id="3c265-139">Create an initial migration</span></span>

<span data-ttu-id="3c265-140">保存更改并生成项目。</span><span class="sxs-lookup"><span data-stu-id="3c265-140">Save your changes and build the project.</span></span> <span data-ttu-id="3c265-141">然后打开命令窗口并导航到项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="3c265-141">Then open a command window and navigate to the project folder.</span></span> <span data-ttu-id="3c265-142">下面是执行此操作的快速方法：</span><span class="sxs-lookup"><span data-stu-id="3c265-142">Here's a quick way to do that:</span></span>

* <span data-ttu-id="3c265-143">在解决方案资源管理器中，右键单击项目，然后从上下文菜单中选择“在文件资源管理器中打开文件夹”。</span><span class="sxs-lookup"><span data-stu-id="3c265-143">In **Solution Explorer**, right-click the project and choose **Open Folder in File Explorer** from the context menu.</span></span>

  ![“在文件资源管理器中打开”菜单项](migrations/_static/open-in-file-explorer.png)

* <span data-ttu-id="3c265-145">在地址栏中输入“cmd”，然后按 Enter。</span><span class="sxs-lookup"><span data-stu-id="3c265-145">Enter "cmd" in the address bar and press Enter.</span></span>

  ![打开命令窗口](migrations/_static/open-command-window.png)

<span data-ttu-id="3c265-147">在命令窗口中输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="3c265-147">Enter the following command in the command window:</span></span>

```console
dotnet ef migrations add InitialCreate
```

<span data-ttu-id="3c265-148">命令窗口中出现如下输出：</span><span class="sxs-lookup"><span data-stu-id="3c265-148">You see output like the following in the command window:</span></span>

```console
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
Done. To undo this action, use 'ef migrations remove'
```

> [!NOTE]
> <span data-ttu-id="3c265-149">如果出现错误消息“找不到任何匹配 "dotnet-ef" 命令的可执行文件”，请参阅[此博客文章](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/)获取故障排除帮助。</span><span class="sxs-lookup"><span data-stu-id="3c265-149">If you see an error message *No executable found matching command "dotnet-ef"*, see [this blog post](http://thedatafarm.com/data-access/no-executable-found-matching-command-dotnet-ef/) for help troubleshooting.</span></span>

<span data-ttu-id="3c265-150">如果看到错误消息“无法访问文件...ContosoUniversity.dll，因为它正被另一个进程使用。”，请在 Windows 系统托盘中找到 IIS Express 图标并右键单击，然后单击“ContosoUniversity”>“停止站点”\*。</span><span class="sxs-lookup"><span data-stu-id="3c265-150">If you see an error message "*cannot access the file ... ContosoUniversity.dll because it is being used by another process.*", find the IIS Express icon in the Windows System Tray, and right-click it, then click **ContosoUniversity > Stop Site**.</span></span>

## <a name="examine-up-and-down-methods"></a><span data-ttu-id="3c265-151">检查 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="3c265-151">Examine Up and Down methods</span></span>

<span data-ttu-id="3c265-152">执行 `migrations add` 命令时，EF 已生成将用于从头创建数据库的代码。</span><span class="sxs-lookup"><span data-stu-id="3c265-152">When you executed the `migrations add` command, EF generated the code that will create the database from scratch.</span></span> <span data-ttu-id="3c265-153">此代码位于“Migrations”文件夹中名为 \<timestamp>_InitialCreate.cs 的文件中。</span><span class="sxs-lookup"><span data-stu-id="3c265-153">This code is in the *Migrations* folder, in the file named *\<timestamp>_InitialCreate.cs*.</span></span> <span data-ttu-id="3c265-154">`InitialCreate` 类的 `Up` 的方法将创建与数据模型实体集相对应的数据库表，`Down` 方法将删除这些表，如下面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="3c265-154">The `Up` method of the `InitialCreate` class creates the database tables that correspond to the data model entity sets, and the `Down` method deletes them, as shown in the following example.</span></span>

[!code-csharp[](intro/samples/cu/Migrations/20170215220724_InitialCreate.cs?range=92-118)]

<span data-ttu-id="3c265-155">迁移调用 `Up` 方法为迁移实现数据模型更改。</span><span class="sxs-lookup"><span data-stu-id="3c265-155">Migrations calls the `Up` method to implement the data model changes for a migration.</span></span> <span data-ttu-id="3c265-156">输入用于回退更新的命令时，迁移调用 `Down` 方法。</span><span class="sxs-lookup"><span data-stu-id="3c265-156">When you enter a command to roll back the update, Migrations calls the `Down` method.</span></span>

<span data-ttu-id="3c265-157">此代码适用于输入 `migrations add InitialCreate` 命令时所创建的初始迁移。</span><span class="sxs-lookup"><span data-stu-id="3c265-157">This code is for the initial migration that was created when you entered the `migrations add InitialCreate` command.</span></span> <span data-ttu-id="3c265-158">迁移名称参数（本示例中为“InitialCreate”）用于指定文件名，并且你可以按需使用任何名称。</span><span class="sxs-lookup"><span data-stu-id="3c265-158">The migration name parameter ("InitialCreate" in the example) is used for the file name and can be whatever you want.</span></span> <span data-ttu-id="3c265-159">最好选择能概括迁移中所执行操作的字词或短语。</span><span class="sxs-lookup"><span data-stu-id="3c265-159">It's best to choose a word or phrase that summarizes what is being done in the migration.</span></span> <span data-ttu-id="3c265-160">例如，可将后面的迁移命名为“AddDepartmentTable”。</span><span class="sxs-lookup"><span data-stu-id="3c265-160">For example, you might name a later migration "AddDepartmentTable".</span></span>

<span data-ttu-id="3c265-161">如果创建初始迁移时已存在数据库，则会生成数据库创建代码，但此代码不必运行，因为数据库已与数据库模型相匹配。</span><span class="sxs-lookup"><span data-stu-id="3c265-161">If you created the initial migration when the database already exists, the database creation code is generated but it doesn't have to run because the database already matches the data model.</span></span> <span data-ttu-id="3c265-162">将应用部署到其中尚不存在数据库的其他环境时，此代码将运行以创建数据库，因此最好提前进行测试。</span><span class="sxs-lookup"><span data-stu-id="3c265-162">When you deploy the app to another environment where the database doesn't exist yet, this code will run to create your database, so it's a good idea to test it first.</span></span> <span data-ttu-id="3c265-163">这也是提前更改连接字符串中数据库的名称的原因，这样迁移才能从头创建新数据库。</span><span class="sxs-lookup"><span data-stu-id="3c265-163">That's why you changed the name of the database in the connection string earlier -- so that migrations can create a new one from scratch.</span></span>

## <a name="the-data-model-snapshot"></a><span data-ttu-id="3c265-164">数据模型快照</span><span class="sxs-lookup"><span data-stu-id="3c265-164">The data model snapshot</span></span>

<span data-ttu-id="3c265-165">迁移在 Migrations/SchoolContextModelSnapshot.cs 中创建当前数据库架构的快照。</span><span class="sxs-lookup"><span data-stu-id="3c265-165">Migrations creates a *snapshot* of the current database schema in *Migrations/SchoolContextModelSnapshot.cs*.</span></span> <span data-ttu-id="3c265-166">添加迁移时，EF 会通过将数据模型与快照文件进行对比来确定已更改的内容。</span><span class="sxs-lookup"><span data-stu-id="3c265-166">When you add a migration, EF determines what changed by comparing the data model to the snapshot file.</span></span>

<span data-ttu-id="3c265-167">删除迁移时，请使用 [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) 命令。</span><span class="sxs-lookup"><span data-stu-id="3c265-167">When deleting a migration, use the [dotnet ef migrations remove](/ef/core/miscellaneous/cli/dotnet#dotnet-ef-migrations-remove) command.</span></span> <span data-ttu-id="3c265-168">`dotnet ef migrations remove` 删除迁移，并确保正确重置快照。</span><span class="sxs-lookup"><span data-stu-id="3c265-168">`dotnet ef migrations remove` deletes the migration and ensures the snapshot is correctly reset.</span></span>

<span data-ttu-id="3c265-169">有关如何使用快照文件的详细信息，请参阅[团队环境中的 EF Core 迁移](/ef/core/managing-schemas/migrations/teams)。</span><span class="sxs-lookup"><span data-stu-id="3c265-169">See [EF Core Migrations in Team Environments](/ef/core/managing-schemas/migrations/teams) for more information about how the snapshot file is used.</span></span>

## <a name="apply-the-migration"></a><span data-ttu-id="3c265-170">应用迁移</span><span class="sxs-lookup"><span data-stu-id="3c265-170">Apply the migration</span></span>

<span data-ttu-id="3c265-171">在命令窗口中，输入以下命令以创建数据库并在其中创建表。</span><span class="sxs-lookup"><span data-stu-id="3c265-171">In the command window, enter the following command to create the database and tables in it.</span></span>

```console
dotnet ef database update
```

<span data-ttu-id="3c265-172">该命令的输出与 `migrations add` 命令的输出相似，但其中还包含设置该数据库的 SQL 命令的日志。</span><span class="sxs-lookup"><span data-stu-id="3c265-172">The output from the command is similar to the `migrations add` command, except that you see logs for the SQL commands that set up the database.</span></span> <span data-ttu-id="3c265-173">下面的示例输出中省略了大部分日志。</span><span class="sxs-lookup"><span data-stu-id="3c265-173">Most of the logs are omitted in the following sample output.</span></span> <span data-ttu-id="3c265-174">如果希望日志消息中不使用此详细级别，则可更改 appsettings.Development.json 文件中的日志级别。</span><span class="sxs-lookup"><span data-stu-id="3c265-174">If you prefer not to see this level of detail in log messages, you can change the log level in the *appsettings.Development.json* file.</span></span> <span data-ttu-id="3c265-175">有关更多信息，请参见<xref:fundamentals/logging/index>。</span><span class="sxs-lookup"><span data-stu-id="3c265-175">For more information, see <xref:fundamentals/logging/index>.</span></span>

```text
info: Microsoft.AspNetCore.DataProtection.KeyManagement.XmlKeyManager[0]
      User profile is available. Using 'C:\Users\username\AppData\Local\ASP.NET\DataProtection-Keys' as key repository and Windows DPAPI to encrypt keys at rest.
info: Microsoft.EntityFrameworkCore.Infrastructure[100403]
      Entity Framework Core 2.0.0-rtm-26452 initialized 'SchoolContext' using provider 'Microsoft.EntityFrameworkCore.SqlServer' with options: None
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (467ms) [Parameters=[], CommandType='Text', CommandTimeout='60']
      CREATE DATABASE [ContosoUniversity2];
info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (20ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      CREATE TABLE [__EFMigrationsHistory] (
          [MigrationId] nvarchar(150) NOT NULL,
          [ProductVersion] nvarchar(32) NOT NULL,
          CONSTRAINT [PK___EFMigrationsHistory] PRIMARY KEY ([MigrationId])
      );

<logs omitted for brevity>

info: Microsoft.EntityFrameworkCore.Database.Command[200101]
      Executed DbCommand (3ms) [Parameters=[], CommandType='Text', CommandTimeout='30']
      INSERT INTO [__EFMigrationsHistory] ([MigrationId], [ProductVersion])
      VALUES (N'20170816151242_InitialCreate', N'2.0.0-rtm-26452');
Done.
```

<span data-ttu-id="3c265-176">使用 SQL Server 对象资源管理器检查数据库（与第一个教程中的做法相同）。</span><span class="sxs-lookup"><span data-stu-id="3c265-176">Use **SQL Server Object Explorer** to inspect the database as you did in the first tutorial.</span></span>  <span data-ttu-id="3c265-177">你会发现添加了 \_\_EFMigrationsHistory 表，该表可用于跟踪已应用到数据库的迁移。</span><span class="sxs-lookup"><span data-stu-id="3c265-177">You'll notice the addition of an \_\_EFMigrationsHistory table that keeps track of which migrations have been applied to the database.</span></span> <span data-ttu-id="3c265-178">查看该表中的数据，其中显示对应初始迁移的一行数据。</span><span class="sxs-lookup"><span data-stu-id="3c265-178">View the data in that table and you'll see one row for the first migration.</span></span> <span data-ttu-id="3c265-179">（上面的 CLI 输出示例中最后部分的日志显示了创建此行的 INSERT 语句。）</span><span class="sxs-lookup"><span data-stu-id="3c265-179">(The last log in the preceding CLI output example shows the INSERT statement that creates this row.)</span></span>

<span data-ttu-id="3c265-180">运行应用程序以验证所有内容照旧运行。</span><span class="sxs-lookup"><span data-stu-id="3c265-180">Run the application to verify that everything still works the same as before.</span></span>

![“学生索引”页](migrations/_static/students-index.png)

<a id="pmc"></a>

## <a name="compare-cli-and-pmc"></a><span data-ttu-id="3c265-182">比较 CLI 和 PMC</span><span class="sxs-lookup"><span data-stu-id="3c265-182">Compare CLI and PMC</span></span>

<span data-ttu-id="3c265-183">可通过 .NET Core CLI 命令或 Visual Studio 包管理器控制台 (PMC) 窗口中的 PowerShell cmdlet 使用可管理迁移的 EF 工具。</span><span class="sxs-lookup"><span data-stu-id="3c265-183">The EF tooling for managing migrations is available from .NET Core CLI commands or from PowerShell cmdlets in the Visual Studio **Package Manager Console** (PMC) window.</span></span> <span data-ttu-id="3c265-184">本教程演示如何使用 CLI，但也可以根据喜好使用 PMC。</span><span class="sxs-lookup"><span data-stu-id="3c265-184">This tutorial shows how to use the CLI, but you can use the PMC if you prefer.</span></span>

<span data-ttu-id="3c265-185">适用于 PMC 命令的 EF 命令位于 [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) 程序包中。</span><span class="sxs-lookup"><span data-stu-id="3c265-185">The EF commands for the PMC commands are in the [Microsoft.EntityFrameworkCore.Tools](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools) package.</span></span> <span data-ttu-id="3c265-186">此包包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中，因此，如果应用具有对 `Microsoft.AspNetCore.App` 的包引用，则无需添加包引用。</span><span class="sxs-lookup"><span data-stu-id="3c265-186">This package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app), so you don't need to add a package reference if your app has a package reference for `Microsoft.AspNetCore.App`.</span></span>

<span data-ttu-id="3c265-187">**重要提示：** 此包与通过编辑 .csproj 文件为 CLI 安装的包不同。</span><span class="sxs-lookup"><span data-stu-id="3c265-187">**Important:** This isn't the same package as the one you install for the CLI by editing the *.csproj* file.</span></span> <span data-ttu-id="3c265-188">此程序包的名称以 `Tools` 结尾，而 CLI 程序包的名称以 `Tools.DotNet` 结尾。</span><span class="sxs-lookup"><span data-stu-id="3c265-188">The name of this one ends in `Tools`, unlike the CLI package name which ends in `Tools.DotNet`.</span></span>

<span data-ttu-id="3c265-189">有关 CLI 命令的详细信息，请参阅 [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet)。</span><span class="sxs-lookup"><span data-stu-id="3c265-189">For more information about the CLI commands, see [.NET Core CLI](/ef/core/miscellaneous/cli/dotnet).</span></span>

<span data-ttu-id="3c265-190">有关 PMC 命令的详细信息，请参阅[包管理器控制台 (Visual Studio)](/ef/core/miscellaneous/cli/powershell)。</span><span class="sxs-lookup"><span data-stu-id="3c265-190">For more information about the PMC commands, see [Package Manager Console (Visual Studio)](/ef/core/miscellaneous/cli/powershell).</span></span>

## <a name="get-the-code"></a><span data-ttu-id="3c265-191">获取代码</span><span class="sxs-lookup"><span data-stu-id="3c265-191">Get the code</span></span>

[<span data-ttu-id="3c265-192">下载或查看已完成的应用程序。</span><span class="sxs-lookup"><span data-stu-id="3c265-192">Download or view the completed application.</span></span>](https://github.com/aspnet/Docs/tree/master/aspnetcore/data/ef-mvc/intro/samples/cu-final)

## <a name="next-step"></a><span data-ttu-id="3c265-193">下一步</span><span class="sxs-lookup"><span data-stu-id="3c265-193">Next step</span></span>

<span data-ttu-id="3c265-194">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="3c265-194">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="3c265-195">已了解迁移相关信息</span><span class="sxs-lookup"><span data-stu-id="3c265-195">Learned about migrations</span></span>
> * <span data-ttu-id="3c265-196">已了解 NuGet 迁移包</span><span class="sxs-lookup"><span data-stu-id="3c265-196">Learned about NuGet migration packages</span></span>
> * <span data-ttu-id="3c265-197">已更改连接字符串</span><span class="sxs-lookup"><span data-stu-id="3c265-197">Changed the connection string</span></span>
> * <span data-ttu-id="3c265-198">已创建初始迁移</span><span class="sxs-lookup"><span data-stu-id="3c265-198">Created an initial migration</span></span>
> * <span data-ttu-id="3c265-199">已检查 Up 和 Down 方法</span><span class="sxs-lookup"><span data-stu-id="3c265-199">Examined Up and Down methods</span></span>
> * <span data-ttu-id="3c265-200">已了解数据模型快照</span><span class="sxs-lookup"><span data-stu-id="3c265-200">Learned about the data model snapshot</span></span>
> * <span data-ttu-id="3c265-201">已应用迁移</span><span class="sxs-lookup"><span data-stu-id="3c265-201">Applied the migration</span></span>

<span data-ttu-id="3c265-202">请继续阅读下一篇文章，开始了解有关展开数据模型的更高级主题。</span><span class="sxs-lookup"><span data-stu-id="3c265-202">Advance to the next article to begin looking at more advanced topics about expanding the data model.</span></span> <span data-ttu-id="3c265-203">同时还将介绍创建并应用其他迁移的方法。</span><span class="sxs-lookup"><span data-stu-id="3c265-203">Along the way you'll create and apply additional migrations.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="3c265-204">创建并应用其他迁移</span><span class="sxs-lookup"><span data-stu-id="3c265-204">Create and apply additional migrations</span></span>](complex-data-model.md)
