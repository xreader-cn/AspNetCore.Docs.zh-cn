---
title: dotnet aspnet-codegenerator 命令
author: rick-anderson
description: dotnet aspnet-codegenerator 命令可用于搭建 ASP.NET Core 项目的基架。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 1043a578f66d5bb57f4a81e9fe21afa5e3c37cb8
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649854"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="d024e-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="d024e-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="d024e-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d024e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d024e-105">`dotnet aspnet-codegenerator` - 运行 ASP.NET Core 基架引擎。</span><span class="sxs-lookup"><span data-stu-id="d024e-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="d024e-106">使用 `dotnet aspnet-codegenerator` 只需要从命令行搭建基架，不必使用 Visual Studio 搭建基架。</span><span class="sxs-lookup"><span data-stu-id="d024e-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="d024e-107">本文适用于：[.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="d024e-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="d024e-108">安装 aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="d024e-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="d024e-109">`dotnet-aspnet-codegenerator` 是必须安装的一个[全局工具](/dotnet/core/tools/global-tools)。</span><span class="sxs-lookup"><span data-stu-id="d024e-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="d024e-110">以下命令安装 `dotnet-aspnet-codegenerator` 工具的最新稳定版本：</span><span class="sxs-lookup"><span data-stu-id="d024e-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="d024e-111">以下命令将 `dotnet-aspnet-codegenerator` 更新到已安装的.NET Core SDK 提供的最新稳定版本：</span><span class="sxs-lookup"><span data-stu-id="d024e-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="d024e-112">摘要</span><span class="sxs-lookup"><span data-stu-id="d024e-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="d024e-113">描述</span><span class="sxs-lookup"><span data-stu-id="d024e-113">Description</span></span>

<span data-ttu-id="d024e-114">`dotnet aspnet-codegenerator` 全局命令运行 ASP.NET Core 代码生成器和基架引擎。</span><span class="sxs-lookup"><span data-stu-id="d024e-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="d024e-115">自变量</span><span class="sxs-lookup"><span data-stu-id="d024e-115">Arguments</span></span>

`generator`

<span data-ttu-id="d024e-116">要运行的代码生成器。</span><span class="sxs-lookup"><span data-stu-id="d024e-116">The code generator to run.</span></span> <span data-ttu-id="d024e-117">以下是可用的生成器：</span><span class="sxs-lookup"><span data-stu-id="d024e-117">The following generators are available:</span></span>

| <span data-ttu-id="d024e-118">Generator</span><span class="sxs-lookup"><span data-stu-id="d024e-118">Generator</span></span> | <span data-ttu-id="d024e-119">操作</span><span class="sxs-lookup"><span data-stu-id="d024e-119">Operation</span></span> |
| ----------------- | ------------ | 
| <span data-ttu-id="d024e-120">area</span><span class="sxs-lookup"><span data-stu-id="d024e-120">area</span></span>      | [<span data-ttu-id="d024e-121">搭建区域的基架</span><span class="sxs-lookup"><span data-stu-id="d024e-121">Scaffolds an Area</span></span>](/aspnet/core/mvc/controllers/areas) |
  <span data-ttu-id="d024e-122">controller</span><span class="sxs-lookup"><span data-stu-id="d024e-122">controller</span></span>| [<span data-ttu-id="d024e-123">搭建控制器的基架</span><span class="sxs-lookup"><span data-stu-id="d024e-123">Scaffolds a controller</span></span>](/aspnet/core/tutorials/first-mvc-app/adding-model) |
  <span data-ttu-id="d024e-124">标识</span><span class="sxs-lookup"><span data-stu-id="d024e-124">identity</span></span>  | [<span data-ttu-id="d024e-125">搭建标识的基架</span><span class="sxs-lookup"><span data-stu-id="d024e-125">Scaffolds Identity</span></span>](/aspnet/core/security/authentication/scaffold-identity) |
  <span data-ttu-id="d024e-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="d024e-126">razorpage</span></span> | [<span data-ttu-id="d024e-127">搭建 Razor Pages 的基架</span><span class="sxs-lookup"><span data-stu-id="d024e-127">Scaffolds Razor Pages</span></span>](/aspnet/core/tutorials/razor-pages/model) |
  <span data-ttu-id="d024e-128">查看</span><span class="sxs-lookup"><span data-stu-id="d024e-128">view</span></span>      | [<span data-ttu-id="d024e-129">搭建视图的基架</span><span class="sxs-lookup"><span data-stu-id="d024e-129">Scaffolds a view</span></span>](/aspnet/core/mvc/views/overview) |

## <a name="options"></a><span data-ttu-id="d024e-130">选项</span><span class="sxs-lookup"><span data-stu-id="d024e-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="d024e-131">指定 NuGet 包目录。</span><span class="sxs-lookup"><span data-stu-id="d024e-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="d024e-132">定义生成配置。</span><span class="sxs-lookup"><span data-stu-id="d024e-132">Defines the build configuration.</span></span> <span data-ttu-id="d024e-133">默认值为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="d024e-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="d024e-134">要使用的目标[框架](/dotnet/standard/frameworks)。</span><span class="sxs-lookup"><span data-stu-id="d024e-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="d024e-135">例如 `net46`。</span><span class="sxs-lookup"><span data-stu-id="d024e-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="d024e-136">生成基本路径。</span><span class="sxs-lookup"><span data-stu-id="d024e-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="d024e-137">打印出有关命令的简短帮助。</span><span class="sxs-lookup"><span data-stu-id="d024e-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="d024e-138">运行前不生成项目。</span><span class="sxs-lookup"><span data-stu-id="d024e-138">Doesn't build the project before running.</span></span> <span data-ttu-id="d024e-139">还将隐式设置 `--no-restore` 标记。</span><span class="sxs-lookup"><span data-stu-id="d024e-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="d024e-140">指定要运行的项目文件的路径（文件夹名称或完整路径）。</span><span class="sxs-lookup"><span data-stu-id="d024e-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="d024e-141">如果未指定，则默认为当前目录。</span><span class="sxs-lookup"><span data-stu-id="d024e-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="d024e-142">生成器选项</span><span class="sxs-lookup"><span data-stu-id="d024e-142">Generator options</span></span>

<span data-ttu-id="d024e-143">以下各节详细说明了受支持的生成器的可用选项：</span><span class="sxs-lookup"><span data-stu-id="d024e-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="d024e-144">区域</span><span class="sxs-lookup"><span data-stu-id="d024e-144">Area</span></span>
* <span data-ttu-id="d024e-145">控制器</span><span class="sxs-lookup"><span data-stu-id="d024e-145">Controller</span></span>
* <span data-ttu-id="d024e-146">标识</span><span class="sxs-lookup"><span data-stu-id="d024e-146">Identity</span></span>  
* <span data-ttu-id="d024e-147">Razorpage</span><span class="sxs-lookup"><span data-stu-id="d024e-147">Razorpage</span></span>
* <span data-ttu-id="d024e-148">视图</span><span class="sxs-lookup"><span data-stu-id="d024e-148">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="d024e-149">区域选项</span><span class="sxs-lookup"><span data-stu-id="d024e-149">Area options</span></span>

<span data-ttu-id="d024e-150">此工具适用于具有控制器和视图的 ASP.NET Core Web 项目。</span><span class="sxs-lookup"><span data-stu-id="d024e-150">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="d024e-151">它不适用于Razor Pages 应用。</span><span class="sxs-lookup"><span data-stu-id="d024e-151">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="d024e-152">用法：`dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="d024e-152">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="d024e-153">前面的命令生成以下文件夹：</span><span class="sxs-lookup"><span data-stu-id="d024e-153">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="d024e-154">*Areas*</span><span class="sxs-lookup"><span data-stu-id="d024e-154">*Areas*</span></span>
  * <span data-ttu-id="d024e-155">AreaNameToGenerate </span><span class="sxs-lookup"><span data-stu-id="d024e-155">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="d024e-156">*Controllers*</span><span class="sxs-lookup"><span data-stu-id="d024e-156">*Controllers*</span></span>
    * <span data-ttu-id="d024e-157">*Data*</span><span class="sxs-lookup"><span data-stu-id="d024e-157">*Data*</span></span>
    * <span data-ttu-id="d024e-158">Models </span><span class="sxs-lookup"><span data-stu-id="d024e-158">*Models*</span></span>
    * <span data-ttu-id="d024e-159">*Views*</span><span class="sxs-lookup"><span data-stu-id="d024e-159">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="d024e-160">控制器选项</span><span class="sxs-lookup"><span data-stu-id="d024e-160">Controller options</span></span>

<span data-ttu-id="d024e-161">下表列出了 `aspnet-codegenerator``controller` 和 `razorpage` 的选项：</span><span class="sxs-lookup"><span data-stu-id="d024e-161">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="d024e-162">下表列出了对于 `aspnet-codegenerator controller` 是唯一的选项：</span><span class="sxs-lookup"><span data-stu-id="d024e-162">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="d024e-163">选项</span><span class="sxs-lookup"><span data-stu-id="d024e-163">Option</span></span>               | <span data-ttu-id="d024e-164">描述</span><span class="sxs-lookup"><span data-stu-id="d024e-164">Description</span></span>|
| ----------------- | ------------ |
| <span data-ttu-id="d024e-165">--controllerName 或 -name</span><span class="sxs-lookup"><span data-stu-id="d024e-165">--controllerName or -name</span></span> | <span data-ttu-id="d024e-166">控制器的名称。</span><span class="sxs-lookup"><span data-stu-id="d024e-166">Name of the controller.</span></span> |
| <span data-ttu-id="d024e-167">--useAsyncActions 或 -async</span><span class="sxs-lookup"><span data-stu-id="d024e-167">--useAsyncActions or -async</span></span> | <span data-ttu-id="d024e-168">生成异步控制器操作。</span><span class="sxs-lookup"><span data-stu-id="d024e-168">Generate async controller actions.</span></span> |
| <span data-ttu-id="d024e-169">--noViews 或 -nv</span><span class="sxs-lookup"><span data-stu-id="d024e-169">--noViews or -nv</span></span> | <span data-ttu-id="d024e-170">不生成任何视图  。</span><span class="sxs-lookup"><span data-stu-id="d024e-170">Generate **no** views.</span></span> |
| <span data-ttu-id="d024e-171">--restWithNoViews 或 -api</span><span class="sxs-lookup"><span data-stu-id="d024e-171">--restWithNoViews or -api</span></span>  | <span data-ttu-id="d024e-172">生成具有 REST 样式 API 的控制器。</span><span class="sxs-lookup"><span data-stu-id="d024e-172">Generate a Controller with REST style API.</span></span> <span data-ttu-id="d024e-173">假设 `noViews` 并且忽略任何与视图相关的选项。</span><span class="sxs-lookup"><span data-stu-id="d024e-173">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="d024e-174">--readWriteActions 或 -actions</span><span class="sxs-lookup"><span data-stu-id="d024e-174">--readWriteActions or -actions</span></span> | <span data-ttu-id="d024e-175">不使用模型生成具有读/写操作的控制器。</span><span class="sxs-lookup"><span data-stu-id="d024e-175">Generate controller with read/write actions without a model.</span></span> |

<span data-ttu-id="d024e-176">使用 `-h` 开关获取 `aspnet-codegenerator controller` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="d024e-176">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="d024e-177">请参阅[搭建 movie 模型的基架](/aspnet/core/tutorials/razor-pages/model)，查看 `dotnet aspnet-codegenerator controller` 示例。</span><span class="sxs-lookup"><span data-stu-id="d024e-177">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="razorpage"></a><span data-ttu-id="d024e-178">Razorpage</span><span class="sxs-lookup"><span data-stu-id="d024e-178">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="d024e-179">可以通过指定新页面的名称和要使用的模板来单独搭建 Razor Pages 的基架。</span><span class="sxs-lookup"><span data-stu-id="d024e-179">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="d024e-180">支持如下模板：</span><span class="sxs-lookup"><span data-stu-id="d024e-180">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="d024e-181">例如，以下命令使用 Edit 模板生成MyEdit.cshtml  和 MyEdit.cshtml.cs  ：</span><span class="sxs-lookup"><span data-stu-id="d024e-181">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="d024e-182">通常不指定模板和生成的文件名，而是创建以下模板：</span><span class="sxs-lookup"><span data-stu-id="d024e-182">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="d024e-183">下表列出了 `aspnet-codegenerator``razorpage` 和 `controller` 的选项：</span><span class="sxs-lookup"><span data-stu-id="d024e-183">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="d024e-184">下表列出了对于 `aspnet-codegenerator razorpage` 是唯一的选项：</span><span class="sxs-lookup"><span data-stu-id="d024e-184">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="d024e-185">选项</span><span class="sxs-lookup"><span data-stu-id="d024e-185">Option</span></span>               | <span data-ttu-id="d024e-186">描述</span><span class="sxs-lookup"><span data-stu-id="d024e-186">Description</span></span>|
| ----------------- | ------------ |
|   <span data-ttu-id="d024e-187">--namespaceName 或 -namespace</span><span class="sxs-lookup"><span data-stu-id="d024e-187">--namespaceName or -namespace</span></span> | <span data-ttu-id="d024e-188">用于生成的 PageModel 的命名空间的名称</span><span class="sxs-lookup"><span data-stu-id="d024e-188">The name of the namespace to use for the generated PageModel</span></span> |
| <span data-ttu-id="d024e-189">--partialView 或 -partial</span><span class="sxs-lookup"><span data-stu-id="d024e-189">--partialView or -partial</span></span> | <span data-ttu-id="d024e-190">生成分部视图。</span><span class="sxs-lookup"><span data-stu-id="d024e-190">Generate a partial view.</span></span> <span data-ttu-id="d024e-191">如果指定此选项，将忽略布局选项 -l 和 -udl。</span><span class="sxs-lookup"><span data-stu-id="d024e-191">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="d024e-192">--noPageModel 或 -npm</span><span class="sxs-lookup"><span data-stu-id="d024e-192">--noPageModel or -npm</span></span> | <span data-ttu-id="d024e-193">切换为不针对 Empty 模板生成 PageModel 类</span><span class="sxs-lookup"><span data-stu-id="d024e-193">Switch to not generate a PageModel class for Empty template</span></span> |

<span data-ttu-id="d024e-194">使用 `-h` 开关获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="d024e-194">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="d024e-195">请参阅[搭建 movie 模型的基架](/aspnet/core/tutorials/razor-pages/model)，查看 `dotnet aspnet-codegenerator razorpage` 示例。</span><span class="sxs-lookup"><span data-stu-id="d024e-195">See [Scaffold the movie model](/aspnet/core/tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### <a name="identity"></a><span data-ttu-id="d024e-196">标识</span><span class="sxs-lookup"><span data-stu-id="d024e-196">Identity</span></span>

<span data-ttu-id="d024e-197">请参阅[搭建标识的基架](/aspnet/core/security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="d024e-197">See [Scaffold Identity](/aspnet/core/security/authentication/scaffold-identity)</span></span>
