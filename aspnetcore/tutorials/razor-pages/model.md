---
title: 第 2 部分，在 ASP.NET Core 中向 Razor 页面应用添加模型
author: rick-anderson
description: Razor 页面教程系列的第 2 部分。
ms.author: riande
ms.date: 12/05/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/model
ms.openlocfilehash: 4099873142b99afb7f0659dfd9a4fde8bec3081d
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633768"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="5b5a2-103">第 2 部分，在 ASP.NET Core 中向 Razor 页面应用添加模型</span><span class="sxs-lookup"><span data-stu-id="5b5a2-103">Part 2, add a model to a Razor Pages app in ASP.NET Core</span></span>

<span data-ttu-id="5b5a2-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5b5a2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="5b5a2-105">在该部分，会添加类管理影片。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="5b5a2-106">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="5b5a2-107">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="5b5a2-108">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="5b5a2-109">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="5b5a2-110">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="5b5a2-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b5a2-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b5a2-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5b5a2-112">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-112">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="5b5a2-113">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-113">Name the folder *Models*.</span></span>

<span data-ttu-id="5b5a2-114">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-114">Right click the *Models* folder.</span></span> <span data-ttu-id="5b5a2-115">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="5b5a2-116">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b5a2-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b5a2-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5b5a2-118">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="5b5a2-119">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b5a2-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5b5a2-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5b5a2-121">在 Solution Pad 中，右键单击“RazorPagesMovie”项目，然后选择“添加”>“新建文件夹...”  。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-121">In Solution Pad, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="5b5a2-122">右键单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="5b5a2-123">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5b5a2-124">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="5b5a2-125">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="5b5a2-126">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="5b5a2-127">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="5b5a2-128">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="5b5a2-128">Scaffold the movie model</span></span>

<span data-ttu-id="5b5a2-129">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="5b5a2-130">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b5a2-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b5a2-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5b5a2-132">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5b5a2-133">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5b5a2-134">将文件夹命名为“Movies”</span><span class="sxs-lookup"><span data-stu-id="5b5a2-134">Name the folder *Movies*</span></span>

<span data-ttu-id="5b5a2-135">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="5b5a2-137">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="5b5a2-137">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="5b5a2-139">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-139">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5b5a2-140">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-140">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="5b5a2-141">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 RazorPagesMovie.Models.RazorPagesMovieContext 更改为 RazorPagesMovie.Data.RazorPagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from RazorPagesMovie.**Models**.RazorPagesMovieContext to RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="5b5a2-142">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="5b5a2-143">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="5b5a2-144">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-144">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="5b5a2-146">appsettings.json 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-146">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b5a2-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b5a2-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="5b5a2-148">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-148">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>
* <span data-ttu-id="5b5a2-149">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="5b5a2-150">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-150">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="5b5a2-151">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-151">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b5a2-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5b5a2-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5b5a2-153">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5b5a2-154">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5b5a2-155">将文件夹命名为“Movies”</span><span class="sxs-lookup"><span data-stu-id="5b5a2-155">Name the folder *Movies*</span></span>

<span data-ttu-id="5b5a2-156">右键单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="5b5a2-158">在“新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="5b5a2-158">In the **New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="5b5a2-160">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-160">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5b5a2-161">在“模型类”下拉列表中，选择或键入“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-161">In the **Model class** drop down, select, or type, **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="5b5a2-162">在“数据上下文类”行中，键入新类的名称“RazorPagesMovie.Data.RazorPagesMovieContext”。 </span><span class="sxs-lookup"><span data-stu-id="5b5a2-162">In the **Data context class** row, type the name for the new class, RazorPagesMovie.**Data**.RazorPagesMovieContext.</span></span> <span data-ttu-id="5b5a2-163">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="5b5a2-164">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="5b5a2-165">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-165">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="5b5a2-167">appsettings.json 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-167">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="5b5a2-168">添加 EF 工具</span><span class="sxs-lookup"><span data-stu-id="5b5a2-168">Add EF tools</span></span>

<span data-ttu-id="5b5a2-169">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="5b5a2-170">前面的命令将添加适用于 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="5b5a2-171">创建的文件</span><span class="sxs-lookup"><span data-stu-id="5b5a2-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b5a2-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b5a2-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5b5a2-173">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="5b5a2-174">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-174">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5b5a2-175">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="5b5a2-175">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="5b5a2-176">已更新</span><span class="sxs-lookup"><span data-stu-id="5b5a2-176">Updated</span></span>

* <span data-ttu-id="5b5a2-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5b5a2-177">*Startup.cs*</span></span>

<span data-ttu-id="5b5a2-178">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b5a2-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5b5a2-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5b5a2-180">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="5b5a2-181">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-181">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5b5a2-182">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="5b5a2-182">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="5b5a2-183">已更新</span><span class="sxs-lookup"><span data-stu-id="5b5a2-183">Updated</span></span>

* <span data-ttu-id="5b5a2-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5b5a2-184">*Startup.cs*</span></span>

<span data-ttu-id="5b5a2-185">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b5a2-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b5a2-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5b5a2-187">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="5b5a2-188">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-188">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="5b5a2-189">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="5b5a2-190">初始迁移</span><span class="sxs-lookup"><span data-stu-id="5b5a2-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b5a2-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b5a2-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5b5a2-192">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="5b5a2-193">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-193">Add an initial migration.</span></span>
* <span data-ttu-id="5b5a2-194">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="5b5a2-195">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="5b5a2-197">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b5a2-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b5a2-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b5a2-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5b5a2-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="5b5a2-200">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="5b5a2-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="5b5a2-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="5b5a2-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="5b5a2-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="5b5a2-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="5b5a2-203">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="5b5a2-204">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="5b5a2-205">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="5b5a2-206">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="5b5a2-207">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="5b5a2-208">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="5b5a2-209">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b5a2-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b5a2-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5b5a2-211">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="5b5a2-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5b5a2-212">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5b5a2-213">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5b5a2-214">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-214">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5b5a2-215">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5b5a2-216">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5b5a2-217">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5b5a2-218">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="5b5a2-219">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-219">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="5b5a2-220">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-220">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5b5a2-221">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="5b5a2-222">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="5b5a2-223">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="5b5a2-224">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5b5a2-225">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5b5a2-226">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b5a2-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b5a2-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5b5a2-228">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b5a2-229">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5b5a2-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5b5a2-230">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="5b5a2-231">测试应用</span><span class="sxs-lookup"><span data-stu-id="5b5a2-231">Test the app</span></span>

* <span data-ttu-id="5b5a2-232">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="5b5a2-233">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-233">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="5b5a2-234">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="5b5a2-235">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-235">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="5b5a2-237">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="5b5a2-238">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="5b5a2-239">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="5b5a2-240">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-240">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="5b5a2-241">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b5a2-242">其他资源</span><span class="sxs-lookup"><span data-stu-id="5b5a2-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5b5a2-243">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="5b5a2-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5b5a2-244">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="5b5a2-245">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="5b5a2-246">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="5b5a2-247">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="5b5a2-248">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="5b5a2-249">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="5b5a2-250">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="5b5a2-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b5a2-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b5a2-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5b5a2-252">右键单击“RazorPagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-252">Right-click the **RazorPagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="5b5a2-253">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-253">Name the folder *Models*.</span></span>

<span data-ttu-id="5b5a2-254">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-254">Right click the *Models* folder.</span></span> <span data-ttu-id="5b5a2-255">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="5b5a2-256">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b5a2-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b5a2-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5b5a2-258">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="5b5a2-259">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b5a2-260">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5b5a2-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5b5a2-261">在解决方案资源管理器中，右键单击“RazorPagesMovie”项目，然后选择“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-261">In Solution Explorer, right-click the **RazorPagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="5b5a2-262">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="5b5a2-263">右键单击“Models”文件夹，然后选择“添加”>“新建文件”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="5b5a2-264">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5b5a2-265">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="5b5a2-266">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="5b5a2-267">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="5b5a2-268">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="5b5a2-269">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="5b5a2-269">Scaffold the movie model</span></span>

<span data-ttu-id="5b5a2-270">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="5b5a2-271">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b5a2-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b5a2-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5b5a2-273">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5b5a2-274">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5b5a2-275">将文件夹命名为“Movies”</span><span class="sxs-lookup"><span data-stu-id="5b5a2-275">Name the folder *Movies*</span></span>

<span data-ttu-id="5b5a2-276">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="5b5a2-278">在“添加基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="5b5a2-278">In the **Add Scaffold** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="5b5a2-280">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-280">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="5b5a2-281">在“模型类”下拉列表中，选择“Movie (RazorPagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-281">In the **Model class** drop down, select **Movie (RazorPagesMovie.Models)**.</span></span>
* <span data-ttu-id="5b5a2-282">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“RazorPagesMovie.Models.RazorPagesMovieContext”  。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="5b5a2-283">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-283">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="5b5a2-285">appsettings.json 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-285">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b5a2-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b5a2-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace RazorPagesMovie.Pages_Movies rather than namespace RazorPagesMovie.Pages.Movies
-->

* <span data-ttu-id="5b5a2-287">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-287">Open a command window in the project directory (The directory that contains the *Program.cs*, *Startup.cs*, and *.csproj* files).</span></span>

* <span data-ttu-id="5b5a2-288">**对于 Windows**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-288">**For Windows**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="5b5a2-289">**对于 macOS 和 Linux**：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-289">**For macOS and Linux**: Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc RazorPagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b5a2-290">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5b5a2-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5b5a2-291">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5b5a2-292">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5b5a2-293">将文件夹命名为“Movies”</span><span class="sxs-lookup"><span data-stu-id="5b5a2-293">Name the folder *Movies*</span></span>

<span data-ttu-id="5b5a2-294">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="5b5a2-296">在“添加新基架”对话框中，依次选择“使用实体框架的 Razor 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="5b5a2-296">In the **Add New Scaffolding** dialog, select **Razor Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="5b5a2-298">完成“添加使用实体框架的 Razor 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-298">Complete the **Add Razor Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5b5a2-299">在“模型类”下拉列表中，选择或键入“Movie” 。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="5b5a2-300">在“数据上下文类”行中，键入或选择“RazorPagesMovieContext”，这将创建一个具有正确命名空间的新数据库上下文类 。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-300">In the **Data context class** row, type select the **RazorPagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="5b5a2-301">在此示例中为“RazorPagesMovie.Models.RazorPagesMovieContext”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-301">In this case it will be  **RazorPagesMovie.Models.RazorPagesMovieContext**.</span></span>
* <span data-ttu-id="5b5a2-302">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-302">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="5b5a2-304">appsettings.json 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-304">The *appsettings.json* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="5b5a2-305">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="5b5a2-306">创建的文件</span><span class="sxs-lookup"><span data-stu-id="5b5a2-306">Files created</span></span>

* <span data-ttu-id="5b5a2-307">*Pages/Movies*：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-307">*Pages/Movies*: Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5b5a2-308">Data/RazorPagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="5b5a2-308">*Data/RazorPagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="5b5a2-309">文件已更新</span><span class="sxs-lookup"><span data-stu-id="5b5a2-309">File updated</span></span>

* <span data-ttu-id="5b5a2-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5b5a2-310">*Startup.cs*</span></span>

<span data-ttu-id="5b5a2-311">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="5b5a2-312">初始迁移</span><span class="sxs-lookup"><span data-stu-id="5b5a2-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b5a2-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b5a2-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5b5a2-314">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="5b5a2-315">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-315">Add an initial migration.</span></span>
* <span data-ttu-id="5b5a2-316">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="5b5a2-317">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="5b5a2-319">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="5b5a2-320">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="5b5a2-321">此架构的依据为 `DbContext` 中指定的模型（在 RazorPagesMovieContext.cs 文件中）。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-321">The schema is based on the model specified in the `DbContext` (In the *RazorPagesMovieContext.cs* file).</span></span> <span data-ttu-id="5b5a2-322">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="5b5a2-323">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="5b5a2-324">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="5b5a2-325">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="5b5a2-326">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b5a2-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b5a2-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b5a2-328">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5b5a2-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="5b5a2-329">前面的命令生成以下警告："*No type was specified for the decimal column 'Price' on entity type 'Movie'.This will cause values to be silently truncated if they do not fit in the default precision and scale.Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " 你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-329">The preceding commands generate the following warning: "*No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.*" You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5b5a2-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5b5a2-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5b5a2-331">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="5b5a2-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5b5a2-332">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5b5a2-333">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5b5a2-334">需要这些服务（如 Razor 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-334">Components that require these services (such as Razor Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5b5a2-335">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5b5a2-336">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5b5a2-337">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5b5a2-338">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="5b5a2-339">`RazorPagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-339">The `RazorPagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="5b5a2-340">数据上下文 (`RazorPagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-340">The data context (`RazorPagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5b5a2-341">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie22/Data/RazorPagesMovieContext.cs)]

<span data-ttu-id="5b5a2-342">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="5b5a2-343">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="5b5a2-344">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5b5a2-345">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5b5a2-346">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *appsettings.json* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5b5a2-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5b5a2-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5b5a2-348">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5b5a2-349">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5b5a2-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5b5a2-350">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="5b5a2-351">测试应用</span><span class="sxs-lookup"><span data-stu-id="5b5a2-351">Test the app</span></span>

* <span data-ttu-id="5b5a2-352">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="5b5a2-353">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="5b5a2-353">If you get the error:</span></span>

```console
SqlException: Cannot open database "RazorPagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="5b5a2-354">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="5b5a2-355">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-355">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="5b5a2-357">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="5b5a2-358">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="5b5a2-359">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="5b5a2-360">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-360">Test the **Edit**, **Details**, and **Delete** links.</span></span>

<span data-ttu-id="5b5a2-361">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="5b5a2-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5b5a2-362">其他资源</span><span class="sxs-lookup"><span data-stu-id="5b5a2-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5b5a2-363">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 Razor Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="5b5a2-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded Razor Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
