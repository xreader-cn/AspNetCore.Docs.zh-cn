---
title: '第 2 部分，在 ASP.NET Core 中向 :::no-loc(Razor)::: 页面应用添加模型'
author: rick-anderson
description: ':::no-loc(Razor)::: 页面教程系列的第 2 部分。'
ms.author: riande
ms.date: 12/05/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/razor-pages/model
ms.openlocfilehash: 84198760cf8302d379c7630b65641e65b66d72a2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93050922"
---
# <a name="part-2-add-a-model-to-a-no-locrazor-pages-app-in-aspnet-core"></a><span data-ttu-id="5869a-103">第 2 部分，在 ASP.NET Core 中向 :::no-loc(Razor)::: 页面应用添加模型</span><span class="sxs-lookup"><span data-stu-id="5869a-103">Part 2, add a model to a :::no-loc(Razor)::: Pages app in ASP.NET Core</span></span>

<span data-ttu-id="5869a-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5869a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<!-- In the next update on the CLI version, let the scaffolder do the same work the VS driven scaffolder does. That is, create the DB context, etc -->

<span data-ttu-id="5869a-105">在该部分，会添加类管理影片。</span><span class="sxs-lookup"><span data-stu-id="5869a-105">In this section, classes are added for managing movies.</span></span> <span data-ttu-id="5869a-106">应用的模型类使用 [Entity Framework Core (EF Core)](/ef/core) 来处理数据库。</span><span class="sxs-lookup"><span data-stu-id="5869a-106">The app's model classes use [Entity Framework Core (EF Core)](/ef/core) to work with the database.</span></span> <span data-ttu-id="5869a-107">EF Core 是一种对象关系映射器 (ORM)，可简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="5869a-107">EF Core is an object-relational mapper (O/RM) that simplifies data access.</span></span>

<span data-ttu-id="5869a-108">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5869a-108">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="5869a-109">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="5869a-109">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="5869a-110">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="5869a-110">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5869a-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5869a-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5869a-112">右键单击“:::no-loc(Razor):::PagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5869a-112">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="5869a-113">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5869a-113">Name the folder *Models*.</span></span>

<span data-ttu-id="5869a-114">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5869a-114">Right click the *Models* folder.</span></span> <span data-ttu-id="5869a-115">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="5869a-115">Select **Add** > **Class**.</span></span> <span data-ttu-id="5869a-116">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="5869a-116">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5869a-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5869a-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5869a-118">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5869a-118">Add a folder named *Models*.</span></span>
* <span data-ttu-id="5869a-119">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5869a-119">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5869a-120">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5869a-120">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5869a-121">在 Solution Pad 中，右键单击“:::no-loc(Razor):::PagesMovie”项目，然后选择“添加”>“新建文件夹...”  。将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5869a-121">In Solution Pad, right-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder...**. Name the folder *Models*.</span></span>
* <span data-ttu-id="5869a-122">右键单击“Models”文件夹，然后选择“添加”>“新建文件...”。</span><span class="sxs-lookup"><span data-stu-id="5869a-122">Right-click the *Models* folder, and then select **Add** > **New File...**.</span></span>
* <span data-ttu-id="5869a-123">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="5869a-123">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5869a-124">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="5869a-124">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="5869a-125">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="5869a-125">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="5869a-126">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="5869a-126">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="5869a-127">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="5869a-127">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="5869a-128">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="5869a-128">Scaffold the movie model</span></span>

<span data-ttu-id="5869a-129">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="5869a-129">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="5869a-130">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="5869a-130">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5869a-131">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5869a-131">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5869a-132">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5869a-132">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5869a-133">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5869a-133">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5869a-134">将文件夹命名为“Movies”</span><span class="sxs-lookup"><span data-stu-id="5869a-134">Name the folder *Movies*</span></span>

<span data-ttu-id="5869a-135">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="5869a-135">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="5869a-137">在“添加基架”对话框中，依次选择“使用实体框架的 :::no-loc(Razor)::: 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="5869a-137">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="5869a-139">完成“添加使用实体框架的 :::no-loc(Razor)::: 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5869a-139">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5869a-140">在“模型类”下拉列表中，选择“Movie (:::no-loc(Razor):::PagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="5869a-140">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="5869a-141">在“数据上下文类”行中，选择 +（加号）并将生成的名称从 :::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext 更改为 :::no-loc(Razor):::PagesMovie.Data.:::no-loc(Razor):::PagesMovieContext   。</span><span class="sxs-lookup"><span data-stu-id="5869a-141">In the **Data context class** row, select the **+** (plus) sign and change the generated name from :::no-loc(Razor):::PagesMovie. **Models**.:::no-loc(Razor):::PagesMovieContext to :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="5869a-142">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="5869a-142">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="5869a-143">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="5869a-143">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="5869a-144">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5869a-144">Select **Add**.</span></span>

![上述说明的图像。](model/_static/3/arp.png)

<span data-ttu-id="5869a-146">*:::no-loc(appsettings.json):::* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5869a-146">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5869a-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5869a-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="5869a-148">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="5869a-148">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>
* <span data-ttu-id="5869a-149">安装基架工具：</span><span class="sxs-lookup"><span data-stu-id="5869a-149">Install the scaffolding tool:</span></span>

  ```dotnetcli
   dotnet tool install --global dotnet-aspnet-codegenerator
   ```

* <span data-ttu-id="5869a-150">**对于 Windows** ：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5869a-150">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="5869a-151">**对于 macOS 和 Linux** ：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5869a-151">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  dotnet tool install --global dotnet-aspnet-codegenerator
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

[!INCLUDE [use SQL Server in production](~/includes/RP/sqlitedev.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5869a-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5869a-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5869a-153">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5869a-153">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5869a-154">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5869a-154">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5869a-155">将文件夹命名为“Movies”</span><span class="sxs-lookup"><span data-stu-id="5869a-155">Name the folder *Movies*</span></span>

<span data-ttu-id="5869a-156">右键单击“Pages/Movies”文件夹 >“添加”>“新基架...”。</span><span class="sxs-lookup"><span data-stu-id="5869a-156">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolding...**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="5869a-158">在“新基架”对话框中，依次选择“使用实体框架的 :::no-loc(Razor)::: 页面 (CRUD)”>“下一步”。  </span><span class="sxs-lookup"><span data-stu-id="5869a-158">In the **New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Next**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="5869a-160">完成“添加使用实体框架的 :::no-loc(Razor)::: 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5869a-160">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5869a-161">在“模型类”下拉列表中，选择或键入“Movie (:::no-loc(Razor):::PagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="5869a-161">In the **Model class** drop down, select, or type, **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="5869a-162">在“数据上下文类”行中，键入新类的名称“:::no-loc(Razor):::PagesMovie.Data.:::no-loc(Razor):::PagesMovieContext”。 </span><span class="sxs-lookup"><span data-stu-id="5869a-162">In the **Data context class** row, type the name for the new class, :::no-loc(Razor):::PagesMovie. **Data**.:::no-loc(Razor):::PagesMovieContext.</span></span> <span data-ttu-id="5869a-163">不需要[此更新](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html)。</span><span class="sxs-lookup"><span data-stu-id="5869a-163">[This change](https://developercommunity.visualstudio.com/content/problem/652166/aspnet-core-ef-scaffolder-uses-incorrect-namespace.html) is not required.</span></span> <span data-ttu-id="5869a-164">它创建具有正确命名空间的数据库上下文类。</span><span class="sxs-lookup"><span data-stu-id="5869a-164">It creates the database context class with the correct namespace.</span></span>
* <span data-ttu-id="5869a-165">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5869a-165">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="5869a-167">*:::no-loc(appsettings.json):::* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5869a-167">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

### <a name="add-ef-tools"></a><span data-ttu-id="5869a-168">添加 EF 工具</span><span class="sxs-lookup"><span data-stu-id="5869a-168">Add EF tools</span></span>

<span data-ttu-id="5869a-169">运行以下 .NET Core CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="5869a-169">Run the following .NET Core CLI command:</span></span>

```dotnetcli
dotnet tool install --global dotnet-ef
```

<span data-ttu-id="5869a-170">前面的命令将添加适用于 .NET Core CLI 的 Entity Framework Core 工具。</span><span class="sxs-lookup"><span data-stu-id="5869a-170">The preceding command adds the Entity Framework Core Tools for the .NET Core CLI.</span></span>

---

### <a name="files-created"></a><span data-ttu-id="5869a-171">创建的文件</span><span class="sxs-lookup"><span data-stu-id="5869a-171">Files created</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5869a-172">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5869a-172">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5869a-173">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="5869a-173">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="5869a-174">*Pages/Movies* ：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="5869a-174">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5869a-175">Data/:::no-loc(Razor):::PagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="5869a-175">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="5869a-176">已更新</span><span class="sxs-lookup"><span data-stu-id="5869a-176">Updated</span></span>

* <span data-ttu-id="5869a-177">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5869a-177">*Startup.cs*</span></span>

<span data-ttu-id="5869a-178">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5869a-178">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5869a-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5869a-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5869a-180">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="5869a-180">The scaffold process creates and updates the following files:</span></span>

* <span data-ttu-id="5869a-181">*Pages/Movies* ：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="5869a-181">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5869a-182">Data/:::no-loc(Razor):::PagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="5869a-182">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="updated"></a><span data-ttu-id="5869a-183">已更新</span><span class="sxs-lookup"><span data-stu-id="5869a-183">Updated</span></span>

* <span data-ttu-id="5869a-184">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5869a-184">*Startup.cs*</span></span>

<span data-ttu-id="5869a-185">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5869a-185">The created and updated files are explained in the next section.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5869a-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5869a-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5869a-187">在搭建基架时，会创建以下文件：</span><span class="sxs-lookup"><span data-stu-id="5869a-187">The scaffold process creates the following files:</span></span>

* <span data-ttu-id="5869a-188">*Pages/Movies* ：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="5869a-188">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>

<span data-ttu-id="5869a-189">创建的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5869a-189">The created files are explained in the next section.</span></span>

---

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="5869a-190">初始迁移</span><span class="sxs-lookup"><span data-stu-id="5869a-190">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5869a-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5869a-191">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5869a-192">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="5869a-192">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="5869a-193">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="5869a-193">Add an initial migration.</span></span>
* <span data-ttu-id="5869a-194">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="5869a-194">Update the database with the initial migration.</span></span>

<span data-ttu-id="5869a-195">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="5869a-195">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="5869a-197">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="5869a-197">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration InitialCreate
Update-Database
```

# <a name="visual-studio-code"></a>[<span data-ttu-id="5869a-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5869a-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5869a-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5869a-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---

<span data-ttu-id="5869a-200">前面的命令生成以下警告：“No type was specified for the decimal column 'Price' on entity type 'Movie'.</span><span class="sxs-lookup"><span data-stu-id="5869a-200">The preceding commands generate the following warning: "No type was specified for the decimal column 'Price' on entity type 'Movie'.</span></span> <span data-ttu-id="5869a-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span><span class="sxs-lookup"><span data-stu-id="5869a-201">This will cause values to be silently truncated if they do not fit in the default precision and scale.</span></span> <span data-ttu-id="5869a-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.”</span><span class="sxs-lookup"><span data-stu-id="5869a-202">Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'."</span></span>

<span data-ttu-id="5869a-203">你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="5869a-203">You can ignore that warning, it will be fixed in a later tutorial.</span></span>

<span data-ttu-id="5869a-204">migrations 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="5869a-204">The migrations command generates code to create the initial database schema.</span></span> <span data-ttu-id="5869a-205">该架构基于在 `DbContext` 中指定的模型。</span><span class="sxs-lookup"><span data-stu-id="5869a-205">The schema is based on the model specified in `DbContext`.</span></span> <span data-ttu-id="5869a-206">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="5869a-206">The `InitialCreate` argument is used to name the migrations.</span></span> <span data-ttu-id="5869a-207">可以使用任何名称，但是按照惯例，会选择可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="5869a-207">Any name can be used, but by convention a name is selected that describes the migration.</span></span>

<span data-ttu-id="5869a-208">`update` 命令在尚未应用的迁移中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5869a-208">The `update` command runs the `Up` method in migrations that have not been applied.</span></span> <span data-ttu-id="5869a-209">在这种情况下，`update` 在用于创建数据库的 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5869a-209">In this case, `update` runs the `Up` method in  *Migrations/\<time-stamp>_InitialCreate.cs* file, which creates the database.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5869a-210">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5869a-210">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5869a-211">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="5869a-211">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5869a-212">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="5869a-212">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5869a-213">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="5869a-213">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5869a-214">需要这些服务（如 :::no-loc(Razor)::: 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="5869a-214">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5869a-215">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="5869a-215">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5869a-216">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="5869a-216">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5869a-217">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="5869a-217">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5869a-218">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="5869a-218">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie30/Startup.cs?name=snippet_ConfigureServices&highlight=5-6)]

<span data-ttu-id="5869a-219">`:::no-loc(Razor):::PagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="5869a-219">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="5869a-220">数据上下文 (`:::no-loc(Razor):::PagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="5869a-220">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5869a-221">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="5869a-221">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="5869a-222">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="5869a-222">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="5869a-223">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="5869a-223">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="5869a-224">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="5869a-224">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5869a-225">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="5869a-225">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5869a-226">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *:::no-loc(appsettings.json):::* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="5869a-226">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5869a-227">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5869a-227">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5869a-228">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5869a-228">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5869a-229">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5869a-229">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5869a-230">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5869a-230">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="5869a-231">测试应用</span><span class="sxs-lookup"><span data-stu-id="5869a-231">Test the app</span></span>

* <span data-ttu-id="5869a-232">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="5869a-232">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="5869a-233">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="5869a-233">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="5869a-234">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="5869a-234">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="5869a-235">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="5869a-235">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="5869a-237">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="5869a-237">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="5869a-238">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="5869a-238">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="5869a-239">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="5869a-239">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="5869a-240">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="5869a-240">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="5869a-241">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="5869a-241">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5869a-242">其他资源</span><span class="sxs-lookup"><span data-stu-id="5869a-242">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5869a-243">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="5869a-243">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end

<!--  ::: moniker previous version   -->
::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="5869a-244">在本节中，添加了用于管理跨平台 [SQLite 数据库](https://www.sqlite.org/index.html)中的电影的类。</span><span class="sxs-lookup"><span data-stu-id="5869a-244">In this section, classes are added for managing movies in a cross-platform [SQLite database](https://www.sqlite.org/index.html).</span></span> <span data-ttu-id="5869a-245">从 ASP.NET Core 模板创建的应用使用 SQLite 数据库。</span><span class="sxs-lookup"><span data-stu-id="5869a-245">Apps created from an ASP.NET Core template use a SQLite database.</span></span> <span data-ttu-id="5869a-246">应用的模型类配合 [Entity Framework Core (EF Core)](/ef/core)（[SQLite EF Core 数据库提供程序](/ef/core/providers/sqlite)）使用，以处理数据库。</span><span class="sxs-lookup"><span data-stu-id="5869a-246">The app's model classes are used with [Entity Framework Core (EF Core)](/ef/core) ([SQLite EF Core Database Provider](/ef/core/providers/sqlite)) to work with the database.</span></span> <span data-ttu-id="5869a-247">EF Core 是一种对象关系映射 (ORM) 框架，可以简化数据访问。</span><span class="sxs-lookup"><span data-stu-id="5869a-247">EF Core is an object-relational mapping (ORM) framework that simplifies data access.</span></span>

<span data-ttu-id="5869a-248">模型类称为 POCO 类（源自“简单传统 CLR 对象”），因为它们与 EF Core 没有任何依赖关系。</span><span class="sxs-lookup"><span data-stu-id="5869a-248">The model classes are known as POCO classes (from "plain-old CLR objects") because they don't have any dependency on EF Core.</span></span> <span data-ttu-id="5869a-249">它们定义数据库中存储的数据属性。</span><span class="sxs-lookup"><span data-stu-id="5869a-249">They define the properties of the data that are stored in the database.</span></span>

[!INCLUDE[](~/includes/rp/download.md)]

## <a name="add-a-data-model"></a><span data-ttu-id="5869a-250">添加数据模型</span><span class="sxs-lookup"><span data-stu-id="5869a-250">Add a data model</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5869a-251">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5869a-251">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5869a-252">右键单击“:::no-loc(Razor):::PagesMovie”项目 >“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5869a-252">Right-click the **:::no-loc(Razor):::PagesMovie** project > **Add** > **New Folder**.</span></span> <span data-ttu-id="5869a-253">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5869a-253">Name the folder *Models*.</span></span>

<span data-ttu-id="5869a-254">右键单击“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5869a-254">Right click the *Models* folder.</span></span> <span data-ttu-id="5869a-255">选择“添加” > “类” 。</span><span class="sxs-lookup"><span data-stu-id="5869a-255">Select **Add** > **Class**.</span></span> <span data-ttu-id="5869a-256">将类命名“Movie”。</span><span class="sxs-lookup"><span data-stu-id="5869a-256">Name the class **Movie**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="5869a-257">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5869a-257">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5869a-258">添加名为“Models”的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5869a-258">Add a folder named *Models*.</span></span>
* <span data-ttu-id="5869a-259">将类添加到名为“Movie.cs”的“Models”文件夹。</span><span class="sxs-lookup"><span data-stu-id="5869a-259">Add a class to the *Models* folder named *Movie.cs*.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

[!INCLUDE [model 2](~/includes/RP/model2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5869a-260">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5869a-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="5869a-261">在解决方案资源管理器中，右键单击“:::no-loc(Razor):::PagesMovie”项目，然后选择“添加” > “新建文件夹”  。</span><span class="sxs-lookup"><span data-stu-id="5869a-261">In Solution Explorer, right-click the **:::no-loc(Razor):::PagesMovie** project, and then select **Add** > **New Folder**.</span></span> <span data-ttu-id="5869a-262">将文件夹命名为“Models”。</span><span class="sxs-lookup"><span data-stu-id="5869a-262">Name the folder *Models*.</span></span>
* <span data-ttu-id="5869a-263">右键单击“Models”文件夹，然后选择“添加”>“新建文件”。</span><span class="sxs-lookup"><span data-stu-id="5869a-263">Right-click the *Models* folder, and then select **Add** > **New File**.</span></span>
* <span data-ttu-id="5869a-264">在“新建文件”对话框中：</span><span class="sxs-lookup"><span data-stu-id="5869a-264">In the **New File** dialog:</span></span>

  * <span data-ttu-id="5869a-265">在左侧窗格中，选择“常规”。</span><span class="sxs-lookup"><span data-stu-id="5869a-265">Select **General** in the left pane.</span></span>
  * <span data-ttu-id="5869a-266">在中间窗格中，选择“空类”。</span><span class="sxs-lookup"><span data-stu-id="5869a-266">Select **Empty Class** in the center pane.</span></span>
  * <span data-ttu-id="5869a-267">将此类命名为“Movie”，然后选择“新建”。</span><span class="sxs-lookup"><span data-stu-id="5869a-267">Name the class **Movie** and select **New**.</span></span>

[!INCLUDE [model 1b](~/includes/RP/model1b.md)]

---

<span data-ttu-id="5869a-268">生成项目以验证没有任何编译错误。</span><span class="sxs-lookup"><span data-stu-id="5869a-268">Build the project to verify there are no compilation errors.</span></span>

## <a name="scaffold-the-movie-model"></a><span data-ttu-id="5869a-269">搭建“电影”模型的基架</span><span class="sxs-lookup"><span data-stu-id="5869a-269">Scaffold the movie model</span></span>

<span data-ttu-id="5869a-270">在此部分，将搭建“电影”模型的基架。</span><span class="sxs-lookup"><span data-stu-id="5869a-270">In this section, the movie model is scaffolded.</span></span> <span data-ttu-id="5869a-271">确切地说，基架工具将生成页面，用于对“电影”模型执行创建、读取、更新和删除 (CRUD) 操作。</span><span class="sxs-lookup"><span data-stu-id="5869a-271">That is, the scaffolding tool produces pages for Create, Read, Update, and Delete (CRUD) operations for the movie model.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5869a-272">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5869a-272">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5869a-273">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5869a-273">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5869a-274">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5869a-274">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5869a-275">将文件夹命名为“Movies”</span><span class="sxs-lookup"><span data-stu-id="5869a-275">Name the folder *Movies*</span></span>

<span data-ttu-id="5869a-276">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="5869a-276">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/sca.png)

<span data-ttu-id="5869a-278">在“添加基架”对话框中，依次选择“使用实体框架的 :::no-loc(Razor)::: 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="5869a-278">In the **Add Scaffold** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffold.png)

<span data-ttu-id="5869a-280">完成“添加使用实体框架的 :::no-loc(Razor)::: 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5869a-280">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>
<!-- In the next section, change 
(plus) sign and accept the generated name 
to use Data, it should not use models. That will make the namespace the same for the VS version and the CLI version
-->

* <span data-ttu-id="5869a-281">在“模型类”下拉列表中，选择“Movie (:::no-loc(Razor):::PagesMovie.Models)” 。</span><span class="sxs-lookup"><span data-stu-id="5869a-281">In the **Model class** drop down, select **Movie (:::no-loc(Razor):::PagesMovie.Models)**.</span></span>
* <span data-ttu-id="5869a-282">在“数据上下文类”行中，选择 +（加号）并接受生成的名称“:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext”  。</span><span class="sxs-lookup"><span data-stu-id="5869a-282">In the **Data context class** row, select the **+** (plus) sign and accept the generated name **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="5869a-283">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5869a-283">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arp.png)

<span data-ttu-id="5869a-285">*:::no-loc(appsettings.json):::* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5869a-285">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5869a-286">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5869a-286">Visual Studio Code</span></span>](#tab/visual-studio-code)

<!--  Until https://github.com/aspnet/Scaffolding/issues/582 is fixed windows needs backslash or the namespace is namespace :::no-loc(Razor):::PagesMovie.Pages_Movies rather than namespace :::no-loc(Razor):::PagesMovie.Pages.Movies
-->

* <span data-ttu-id="5869a-287">打开项目目录（包含 Program.cs、Startup.cs 和 .csproj 文件的目录）中的命令窗口。</span><span class="sxs-lookup"><span data-stu-id="5869a-287">Open a command window in the project directory (The directory that contains the *Program.cs* , *Startup.cs* , and *.csproj* files).</span></span>

* <span data-ttu-id="5869a-288">**对于 Windows** ：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5869a-288">**For Windows** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages\Movies --referenceScriptLibraries
  ```

* <span data-ttu-id="5869a-289">**对于 macOS 和 Linux** ：运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="5869a-289">**For macOS and Linux** : Run the following command:</span></span>

  ```dotnetcli
  dotnet aspnet-codegenerator razorpage -m Movie -dc :::no-loc(Razor):::PagesMovieContext -udl -outDir Pages/Movies --referenceScriptLibraries
  ```

[!INCLUDE [explains scaffold gen params](~/includes/RP/model4.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5869a-290">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5869a-290">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5869a-291">创建“Pages/Movies”文件夹：</span><span class="sxs-lookup"><span data-stu-id="5869a-291">Create a *Pages/Movies* folder:</span></span>

* <span data-ttu-id="5869a-292">右键单击“Pages”文件夹 >“添加”>“新建文件夹”。</span><span class="sxs-lookup"><span data-stu-id="5869a-292">Right click on the *Pages* folder > **Add** > **New Folder**.</span></span>
* <span data-ttu-id="5869a-293">将文件夹命名为“Movies”</span><span class="sxs-lookup"><span data-stu-id="5869a-293">Name the folder *Movies*</span></span>

<span data-ttu-id="5869a-294">右键单击“Pages/Movies”文件夹 >“添加”>“新搭建基架的项目”。</span><span class="sxs-lookup"><span data-stu-id="5869a-294">Right click on the *Pages/Movies* folder > **Add** > **New Scaffolded Item**.</span></span>

![上述说明的图像。](model/_static/scaMac.png)

<span data-ttu-id="5869a-296">在“添加新基架”对话框中，依次选择“使用实体框架的 :::no-loc(Razor)::: 页面 (CRUD)”>“添加”。  </span><span class="sxs-lookup"><span data-stu-id="5869a-296">In the **Add New Scaffolding** dialog, select **:::no-loc(Razor)::: Pages using Entity Framework (CRUD)** > **Add**.</span></span>

![上述说明的图像。](model/_static/add_scaffoldMac.png)

<span data-ttu-id="5869a-298">完成“添加使用实体框架的 :::no-loc(Razor)::: 页面 (CRUD)”对话框：</span><span class="sxs-lookup"><span data-stu-id="5869a-298">Complete the **Add :::no-loc(Razor)::: Pages using Entity Framework (CRUD)** dialog:</span></span>

* <span data-ttu-id="5869a-299">在“模型类”下拉列表中，选择或键入“Movie” 。</span><span class="sxs-lookup"><span data-stu-id="5869a-299">In the **Model class** drop down, select or type **Movie**.</span></span>
* <span data-ttu-id="5869a-300">在“数据上下文类”行中，键入或选择“:::no-loc(Razor):::PagesMovieContext”，这将创建一个具有正确命名空间的新数据库上下文类 。</span><span class="sxs-lookup"><span data-stu-id="5869a-300">In the **Data context class** row, type select the **:::no-loc(Razor):::PagesMovieContext** this will create a new db context class with the correct namespace.</span></span> <span data-ttu-id="5869a-301">在此示例中为“:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext”。</span><span class="sxs-lookup"><span data-stu-id="5869a-301">In this case it will be  **:::no-loc(Razor):::PagesMovie.Models.:::no-loc(Razor):::PagesMovieContext**.</span></span>
* <span data-ttu-id="5869a-302">选择“添加”。</span><span class="sxs-lookup"><span data-stu-id="5869a-302">Select **Add**.</span></span>

![上述说明的图像。](model/_static/arpMac.png)

<span data-ttu-id="5869a-304">*:::no-loc(appsettings.json):::* 文件通过用于连接到本地数据的连接字符串进行更新。</span><span class="sxs-lookup"><span data-stu-id="5869a-304">The *:::no-loc(appsettings.json):::* file is updated with the connection string used to connect to a local database.</span></span>

---

<span data-ttu-id="5869a-305">在搭建基架时，会创建并更新以下文件：</span><span class="sxs-lookup"><span data-stu-id="5869a-305">The scaffold process creates and updates the following files:</span></span>

### <a name="files-created"></a><span data-ttu-id="5869a-306">创建的文件</span><span class="sxs-lookup"><span data-stu-id="5869a-306">Files created</span></span>

* <span data-ttu-id="5869a-307">*Pages/Movies* ：“创建”、“删除”、“详细信息”、“编辑”和“索引”。</span><span class="sxs-lookup"><span data-stu-id="5869a-307">*Pages/Movies* : Create, Delete, Details, Edit, and Index.</span></span>
* <span data-ttu-id="5869a-308">Data/:::no-loc(Razor):::PagesMovieContext.cs</span><span class="sxs-lookup"><span data-stu-id="5869a-308">*Data/:::no-loc(Razor):::PagesMovieContext.cs*</span></span>

### <a name="file-updated"></a><span data-ttu-id="5869a-309">文件已更新</span><span class="sxs-lookup"><span data-stu-id="5869a-309">File updated</span></span>

* <span data-ttu-id="5869a-310">*Startup.cs*</span><span class="sxs-lookup"><span data-stu-id="5869a-310">*Startup.cs*</span></span>

<span data-ttu-id="5869a-311">创建和更新的文件将在下一节中说明。</span><span class="sxs-lookup"><span data-stu-id="5869a-311">The created and updated files are explained in the next section.</span></span>

<a name="pmc"></a>

## <a name="initial-migration"></a><span data-ttu-id="5869a-312">初始迁移</span><span class="sxs-lookup"><span data-stu-id="5869a-312">Initial migration</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5869a-313">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5869a-313">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="5869a-314">在此部分中，程序包管理器控制台 (PMC) 用于：</span><span class="sxs-lookup"><span data-stu-id="5869a-314">In this section, the Package Manager Console (PMC) is used to:</span></span>

* <span data-ttu-id="5869a-315">添加初始迁移。</span><span class="sxs-lookup"><span data-stu-id="5869a-315">Add an initial migration.</span></span>
* <span data-ttu-id="5869a-316">使用初始迁移来更新数据库。</span><span class="sxs-lookup"><span data-stu-id="5869a-316">Update the database with the initial migration.</span></span>

<span data-ttu-id="5869a-317">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台” 。</span><span class="sxs-lookup"><span data-stu-id="5869a-317">From the **Tools** menu, select **NuGet Package Manager** > **Package Manager Console**.</span></span>

  ![PMC 菜单](../first-mvc-app/adding-model/_static/pmc.png)

<span data-ttu-id="5869a-319">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="5869a-319">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration Initial
Update-Database
```

<span data-ttu-id="5869a-320">`Add-Migration` 命令生成用于创建初始数据库架构的代码。</span><span class="sxs-lookup"><span data-stu-id="5869a-320">The `Add-Migration` command generates code to create the initial database schema.</span></span> <span data-ttu-id="5869a-321">此架构的依据为 `DbContext` 中指定的模型（在 :::no-loc(Razor):::PagesMovieContext.cs 文件中）。</span><span class="sxs-lookup"><span data-stu-id="5869a-321">The schema is based on the model specified in the `DbContext` (In the *:::no-loc(Razor):::PagesMovieContext.cs* file).</span></span> <span data-ttu-id="5869a-322">`InitialCreate` 参数用于为迁移命名。</span><span class="sxs-lookup"><span data-stu-id="5869a-322">The `InitialCreate` argument is used to name the migration.</span></span> <span data-ttu-id="5869a-323">可以使用任何名称，但是按照惯例，会使用可说明迁移的名称。</span><span class="sxs-lookup"><span data-stu-id="5869a-323">Any name can be used, but by convention a name that describes the migration is used.</span></span> <span data-ttu-id="5869a-324">有关详细信息，请参阅 <xref:data/ef-mvc/migrations>。</span><span class="sxs-lookup"><span data-stu-id="5869a-324">For more information, see <xref:data/ef-mvc/migrations>.</span></span>

<span data-ttu-id="5869a-325">`Update-Database` 命令在 Migrations/\<time-stamp>_InitialCreate.cs 文件中运行 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5869a-325">The `Update-Database` command runs the `Up` method in the *Migrations/\<time-stamp>_InitialCreate.cs* file.</span></span> <span data-ttu-id="5869a-326">`Up` 方法会创建数据库。</span><span class="sxs-lookup"><span data-stu-id="5869a-326">The `Up` method creates the database.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5869a-327">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5869a-327">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5869a-328">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5869a-328">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE [initial migration](~/includes/RP/model3.md)]

---
> [!NOTE]
> <span data-ttu-id="5869a-329">前面的命令生成以下警告：" *No type was specified for the decimal column 'Price' on entity type 'Movie'.This will cause values to be silently truncated if they do not fit in the default precision and scale.Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " 你可以忽略该警告，它将后面的教程中得到修复。</span><span class="sxs-lookup"><span data-stu-id="5869a-329">The preceding commands generate the following warning: " *No type was specified for the decimal column 'Price' on entity type 'Movie'. This will cause values to be silently truncated if they do not fit in the default precision and scale. Explicitly specify the SQL server column type that can accommodate all the values using 'HasColumnType()'.* " You can ignore that warning, it will be fixed in a later tutorial.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="5869a-330">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5869a-330">Visual Studio</span></span>](#tab/visual-studio)

### <a name="examine-the-context-registered-with-dependency-injection"></a><span data-ttu-id="5869a-331">检查通过依赖关系注入注册的上下文</span><span class="sxs-lookup"><span data-stu-id="5869a-331">Examine the context registered with dependency injection</span></span>

<span data-ttu-id="5869a-332">ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。</span><span class="sxs-lookup"><span data-stu-id="5869a-332">ASP.NET Core is built with [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="5869a-333">服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。</span><span class="sxs-lookup"><span data-stu-id="5869a-333">Services (such as the EF Core DB context) are registered with dependency injection during application startup.</span></span> <span data-ttu-id="5869a-334">需要这些服务（如 :::no-loc(Razor)::: 页面）的组件通过构造函数参数提供相应服务。</span><span class="sxs-lookup"><span data-stu-id="5869a-334">Components that require these services (such as :::no-loc(Razor)::: Pages) are provided these services via constructor parameters.</span></span> <span data-ttu-id="5869a-335">本教程的后续部分介绍了用于获取 DB 上下文实例的构造函数代码。</span><span class="sxs-lookup"><span data-stu-id="5869a-335">The constructor code that gets a DB context instance is shown later in the tutorial.</span></span>

<span data-ttu-id="5869a-336">基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。</span><span class="sxs-lookup"><span data-stu-id="5869a-336">The scaffolding tool automatically created a DB context and registered it with the dependency injection container.</span></span>

<span data-ttu-id="5869a-337">检查 `Startup.ConfigureServices` 方法。</span><span class="sxs-lookup"><span data-stu-id="5869a-337">Examine the `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5869a-338">基架添加了突出显示的行：</span><span class="sxs-lookup"><span data-stu-id="5869a-338">The highlighted line was added by the scaffolder:</span></span>

[!code-csharp[](razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Startup.cs?name=snippet_ConfigureServices&highlight=15-18)]

<span data-ttu-id="5869a-339">`:::no-loc(Razor):::PagesMovieContext` 为 `Movie` 模型协调 EF Core 功能（创建、读取、更新、删除等）。</span><span class="sxs-lookup"><span data-stu-id="5869a-339">The `:::no-loc(Razor):::PagesMovieContext` coordinates EF Core functionality (Create, Read, Update, Delete, etc.) for the `Movie` model.</span></span> <span data-ttu-id="5869a-340">数据上下文 (`:::no-loc(Razor):::PagesMovieContext`) 派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="5869a-340">The data context (`:::no-loc(Razor):::PagesMovieContext`) is derived from [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext).</span></span> <span data-ttu-id="5869a-341">数据上下文指定数据模型中包含哪些实体。</span><span class="sxs-lookup"><span data-stu-id="5869a-341">The data context specifies which entities are included in the data model.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/:::no-loc(Razor):::PagesMovie22/Data/:::no-loc(Razor):::PagesMovieContext.cs)]

<span data-ttu-id="5869a-342">前面的代码为实体集创建 [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。</span><span class="sxs-lookup"><span data-stu-id="5869a-342">The preceding code creates a [DbSet\<Movie>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) property for the entity set.</span></span> <span data-ttu-id="5869a-343">在实体框架术语中，实体集通常与数据表相对应。</span><span class="sxs-lookup"><span data-stu-id="5869a-343">In Entity Framework terminology, an entity set typically corresponds to a database table.</span></span> <span data-ttu-id="5869a-344">实体对应表中的行。</span><span class="sxs-lookup"><span data-stu-id="5869a-344">An entity corresponds to a row in the table.</span></span>

<span data-ttu-id="5869a-345">通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。</span><span class="sxs-lookup"><span data-stu-id="5869a-345">The name of the connection string is passed in to the context by calling a method on a [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) object.</span></span> <span data-ttu-id="5869a-346">进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index)在 *:::no-loc(appsettings.json):::* 文件中读取连接字符串。</span><span class="sxs-lookup"><span data-stu-id="5869a-346">For local development, the [ASP.NET Core configuration system](xref:fundamentals/configuration/index) reads the connection string from the *:::no-loc(appsettings.json):::* file.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="5869a-347">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5869a-347">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="5869a-348">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5869a-348">Examine the `Up` method.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="5869a-349">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5869a-349">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5869a-350">检查 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="5869a-350">Examine the `Up` method.</span></span>

---

<a name="test"></a>

### <a name="test-the-app"></a><span data-ttu-id="5869a-351">测试应用</span><span class="sxs-lookup"><span data-stu-id="5869a-351">Test the app</span></span>

* <span data-ttu-id="5869a-352">运行应用并将 `/Movies` 追加到浏览器中的 URL (`http://localhost:port/movies`)。</span><span class="sxs-lookup"><span data-stu-id="5869a-352">Run the app and append `/Movies` to the URL in the browser (`http://localhost:port/movies`).</span></span>

<span data-ttu-id="5869a-353">如果收到错误：</span><span class="sxs-lookup"><span data-stu-id="5869a-353">If you get the error:</span></span>

```console
SqlException: Cannot open database ":::no-loc(Razor):::PagesMovieContext-GUID" requested by the login. The login failed.
Login failed for user 'User-name'.
```

<span data-ttu-id="5869a-354">缺少[迁移步骤](#pmc)。</span><span class="sxs-lookup"><span data-stu-id="5869a-354">You missed the [migrations step](#pmc).</span></span>

* <span data-ttu-id="5869a-355">测试“创建”链接。</span><span class="sxs-lookup"><span data-stu-id="5869a-355">Test the **Create** link.</span></span>

  ![创建页面](model/_static/conan.png)

  > [!NOTE]
  > <span data-ttu-id="5869a-357">可能无法在 `Price` 字段中输入十进制逗号。</span><span class="sxs-lookup"><span data-stu-id="5869a-357">You may not be able to enter decimal commas in the `Price` field.</span></span> <span data-ttu-id="5869a-358">若要使 [jQuery 验证](https://jqueryvalidation.org/)支持使用逗号（“,”）表示小数点的非英语区域设置，以及支持非美国英语日期格式，应用必须进行全球化。</span><span class="sxs-lookup"><span data-stu-id="5869a-358">To support [jQuery validation](https://jqueryvalidation.org/) for non-English locales that use a comma (",") for a decimal point and for non US-English date formats, the app must be globalized.</span></span> <span data-ttu-id="5869a-359">有关全球化的说明，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420)。</span><span class="sxs-lookup"><span data-stu-id="5869a-359">For globalization instructions, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/4076#issuecomment-326590420).</span></span>

* <span data-ttu-id="5869a-360">测试“编辑”、“详细信息”和“删除”链接。</span><span class="sxs-lookup"><span data-stu-id="5869a-360">Test the **Edit** , **Details** , and **Delete** links.</span></span>

<span data-ttu-id="5869a-361">下一个教程介绍由基架创建的文件。</span><span class="sxs-lookup"><span data-stu-id="5869a-361">The next tutorial explains the files created by scaffolding.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5869a-362">其他资源</span><span class="sxs-lookup"><span data-stu-id="5869a-362">Additional resources</span></span>

> [!div class="step-by-step"]
> <span data-ttu-id="5869a-363">[上一篇：入门](xref:tutorials/razor-pages/razor-pages-start)
> [下一篇：基架 :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span><span class="sxs-lookup"><span data-stu-id="5869a-363">[Previous: Get Started](xref:tutorials/razor-pages/razor-pages-start)
[Next: Scaffolded :::no-loc(Razor)::: Pages](xref:tutorials/razor-pages/page)</span></span>

::: moniker-end
