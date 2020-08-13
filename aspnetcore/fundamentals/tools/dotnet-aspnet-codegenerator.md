---
title: dotnet aspnet-codegenerator 命令
author: rick-anderson
description: dotnet aspnet-codegenerator 命令可用于搭建 ASP.NET Core 项目的基架。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 07/04/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 071f2269081e63ad1355547bccb449180c59c997
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016500"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="3009c-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="3009c-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="3009c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3009c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="3009c-105">`dotnet aspnet-codegenerator` - 运行 ASP.NET Core 基架引擎。</span><span class="sxs-lookup"><span data-stu-id="3009c-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="3009c-106">使用 `dotnet aspnet-codegenerator` 只需要从命令行搭建基架，不必使用 Visual Studio 搭建基架。</span><span class="sxs-lookup"><span data-stu-id="3009c-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

<span data-ttu-id="3009c-107">本文适用于：[.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="3009c-107">This article applies to [.NET Core 2.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/2.1) and later.</span></span>

## <a name="installing-aspnet-codegenerator"></a><span data-ttu-id="3009c-108">安装 aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="3009c-108">Installing aspnet-codegenerator</span></span>

<span data-ttu-id="3009c-109">`dotnet-aspnet-codegenerator` 是必须安装的一个[全局工具](/dotnet/core/tools/global-tools)。</span><span class="sxs-lookup"><span data-stu-id="3009c-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="3009c-110">以下命令安装 `dotnet-aspnet-codegenerator` 工具的最新稳定版本：</span><span class="sxs-lookup"><span data-stu-id="3009c-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="3009c-111">以下命令将 `dotnet-aspnet-codegenerator` 更新到已安装的.NET Core SDK 提供的最新稳定版本：</span><span class="sxs-lookup"><span data-stu-id="3009c-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="3009c-112">摘要</span><span class="sxs-lookup"><span data-stu-id="3009c-112">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="3009c-113">描述</span><span class="sxs-lookup"><span data-stu-id="3009c-113">Description</span></span>

<span data-ttu-id="3009c-114">`dotnet aspnet-codegenerator` 全局命令运行 ASP.NET Core 代码生成器和基架引擎。</span><span class="sxs-lookup"><span data-stu-id="3009c-114">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="3009c-115">自变量</span><span class="sxs-lookup"><span data-stu-id="3009c-115">Arguments</span></span>

`generator`

<span data-ttu-id="3009c-116">要运行的代码生成器。</span><span class="sxs-lookup"><span data-stu-id="3009c-116">The code generator to run.</span></span> <span data-ttu-id="3009c-117">以下是可用的生成器：</span><span class="sxs-lookup"><span data-stu-id="3009c-117">The following generators are available:</span></span>

| <span data-ttu-id="3009c-118">Generator</span><span class="sxs-lookup"><span data-stu-id="3009c-118">Generator</span></span>  | <span data-ttu-id="3009c-119">操作</span><span class="sxs-lookup"><span data-stu-id="3009c-119">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="3009c-120">area</span><span class="sxs-lookup"><span data-stu-id="3009c-120">area</span></span>       | [<span data-ttu-id="3009c-121">搭建区域的基架</span><span class="sxs-lookup"><span data-stu-id="3009c-121">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="3009c-122">controller</span><span class="sxs-lookup"><span data-stu-id="3009c-122">controller</span></span> | [<span data-ttu-id="3009c-123">搭建控制器的基架</span><span class="sxs-lookup"><span data-stu-id="3009c-123">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="3009c-124">标识</span><span class="sxs-lookup"><span data-stu-id="3009c-124">identity</span></span>   | [<span data-ttu-id="3009c-125">构建 Identity</span><span class="sxs-lookup"><span data-stu-id="3009c-125">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="3009c-126">razorpage</span><span class="sxs-lookup"><span data-stu-id="3009c-126">razorpage</span></span>  | [<span data-ttu-id="3009c-127">构建 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="3009c-127">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="3009c-128">查看</span><span class="sxs-lookup"><span data-stu-id="3009c-128">view</span></span>       | [<span data-ttu-id="3009c-129">搭建视图的基架</span><span class="sxs-lookup"><span data-stu-id="3009c-129">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="3009c-130">选项</span><span class="sxs-lookup"><span data-stu-id="3009c-130">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="3009c-131">指定 NuGet 包目录。</span><span class="sxs-lookup"><span data-stu-id="3009c-131">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="3009c-132">定义生成配置。</span><span class="sxs-lookup"><span data-stu-id="3009c-132">Defines the build configuration.</span></span> <span data-ttu-id="3009c-133">默认值为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="3009c-133">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="3009c-134">要使用的目标[框架](/dotnet/standard/frameworks)。</span><span class="sxs-lookup"><span data-stu-id="3009c-134">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="3009c-135">例如 `net46`。</span><span class="sxs-lookup"><span data-stu-id="3009c-135">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="3009c-136">生成基本路径。</span><span class="sxs-lookup"><span data-stu-id="3009c-136">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="3009c-137">打印出有关命令的简短帮助。</span><span class="sxs-lookup"><span data-stu-id="3009c-137">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="3009c-138">运行前不生成项目。</span><span class="sxs-lookup"><span data-stu-id="3009c-138">Doesn't build the project before running.</span></span> <span data-ttu-id="3009c-139">还将隐式设置 `--no-restore` 标记。</span><span class="sxs-lookup"><span data-stu-id="3009c-139">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="3009c-140">指定要运行的项目文件的路径（文件夹名称或完整路径）。</span><span class="sxs-lookup"><span data-stu-id="3009c-140">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="3009c-141">如果未指定，则默认为当前目录。</span><span class="sxs-lookup"><span data-stu-id="3009c-141">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="3009c-142">生成器选项</span><span class="sxs-lookup"><span data-stu-id="3009c-142">Generator options</span></span>

<span data-ttu-id="3009c-143">以下各节详细说明了受支持的生成器的可用选项：</span><span class="sxs-lookup"><span data-stu-id="3009c-143">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="3009c-144">区域</span><span class="sxs-lookup"><span data-stu-id="3009c-144">Area</span></span>
* <span data-ttu-id="3009c-145">控制器</span><span class="sxs-lookup"><span data-stu-id="3009c-145">Controller</span></span>
* Identity  
* <span data-ttu-id="3009c-146">Razorpage</span><span class="sxs-lookup"><span data-stu-id="3009c-146">Razorpage</span></span>
* <span data-ttu-id="3009c-147">视图</span><span class="sxs-lookup"><span data-stu-id="3009c-147">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="3009c-148">区域选项</span><span class="sxs-lookup"><span data-stu-id="3009c-148">Area options</span></span>

<span data-ttu-id="3009c-149">此工具适用于具有控制器和视图的 ASP.NET Core Web 项目。</span><span class="sxs-lookup"><span data-stu-id="3009c-149">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="3009c-150">它不适用于 Razor Pages 应用。</span><span class="sxs-lookup"><span data-stu-id="3009c-150">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="3009c-151">用法：`dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="3009c-151">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="3009c-152">前面的命令生成以下文件夹：</span><span class="sxs-lookup"><span data-stu-id="3009c-152">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="3009c-153">*Areas*</span><span class="sxs-lookup"><span data-stu-id="3009c-153">*Areas*</span></span>
  * <span data-ttu-id="3009c-154">AreaNameToGenerate</span><span class="sxs-lookup"><span data-stu-id="3009c-154">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="3009c-155">*Controllers*</span><span class="sxs-lookup"><span data-stu-id="3009c-155">*Controllers*</span></span>
    * <span data-ttu-id="3009c-156">*Data*</span><span class="sxs-lookup"><span data-stu-id="3009c-156">*Data*</span></span>
    * <span data-ttu-id="3009c-157">Models</span><span class="sxs-lookup"><span data-stu-id="3009c-157">*Models*</span></span>
    * <span data-ttu-id="3009c-158">*Views*</span><span class="sxs-lookup"><span data-stu-id="3009c-158">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="3009c-159">控制器选项</span><span class="sxs-lookup"><span data-stu-id="3009c-159">Controller options</span></span>

<span data-ttu-id="3009c-160">下表列出了 `aspnet-codegenerator` `controller` 和 `razorpage` 的选项：</span><span class="sxs-lookup"><span data-stu-id="3009c-160">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="3009c-161">下表列出了对于 `aspnet-codegenerator controller` 是唯一的选项：</span><span class="sxs-lookup"><span data-stu-id="3009c-161">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="3009c-162">选项</span><span class="sxs-lookup"><span data-stu-id="3009c-162">Option</span></span>                         | <span data-ttu-id="3009c-163">描述</span><span class="sxs-lookup"><span data-stu-id="3009c-163">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="3009c-164">--controllerName 或 -name</span><span class="sxs-lookup"><span data-stu-id="3009c-164">--controllerName or -name</span></span>      | <span data-ttu-id="3009c-165">控制器的名称。</span><span class="sxs-lookup"><span data-stu-id="3009c-165">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="3009c-166">--useAsyncActions 或 -async</span><span class="sxs-lookup"><span data-stu-id="3009c-166">--useAsyncActions or -async</span></span>    | <span data-ttu-id="3009c-167">生成异步控制器操作。</span><span class="sxs-lookup"><span data-stu-id="3009c-167">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="3009c-168">--noViews 或 -nv</span><span class="sxs-lookup"><span data-stu-id="3009c-168">--noViews or -nv</span></span>               | <span data-ttu-id="3009c-169">不生成任何视图。</span><span class="sxs-lookup"><span data-stu-id="3009c-169">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="3009c-170">--restWithNoViews 或 -api</span><span class="sxs-lookup"><span data-stu-id="3009c-170">--restWithNoViews or -api</span></span>      | <span data-ttu-id="3009c-171">生成具有 REST 样式 API 的控制器。</span><span class="sxs-lookup"><span data-stu-id="3009c-171">Generate a Controller with REST style API.</span></span> <span data-ttu-id="3009c-172">假设 `noViews` 并且忽略任何与视图相关的选项。</span><span class="sxs-lookup"><span data-stu-id="3009c-172">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="3009c-173">--readWriteActions 或 -actions</span><span class="sxs-lookup"><span data-stu-id="3009c-173">--readWriteActions or -actions</span></span> | <span data-ttu-id="3009c-174">不使用模型生成具有读/写操作的控制器。</span><span class="sxs-lookup"><span data-stu-id="3009c-174">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="3009c-175">使用 `-h` 开关获取 `aspnet-codegenerator controller` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="3009c-175">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="3009c-176">请参阅[搭建 movie 模型的基架](xref:tutorials/first-mvc-app/adding-model)，查看 `dotnet aspnet-codegenerator controller` 示例。</span><span class="sxs-lookup"><span data-stu-id="3009c-176">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="3009c-177">Razorpage</span><span class="sxs-lookup"><span data-stu-id="3009c-177">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="3009c-178">可以通过指定新页面的名称和要使用的模板来单独搭建 Razor Pages 的基架。</span><span class="sxs-lookup"><span data-stu-id="3009c-178">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="3009c-179">支持如下模板：</span><span class="sxs-lookup"><span data-stu-id="3009c-179">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="3009c-180">例如，以下命令使用 Edit 模板生成MyEdit.cshtml  和 MyEdit.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="3009c-180">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="3009c-181">通常不指定模板和生成的文件名，而是创建以下模板：</span><span class="sxs-lookup"><span data-stu-id="3009c-181">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="3009c-182">下表列出了 `aspnet-codegenerator` `razorpage` 和 `controller` 的选项：</span><span class="sxs-lookup"><span data-stu-id="3009c-182">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="3009c-183">下表列出了对于 `aspnet-codegenerator razorpage` 是唯一的选项：</span><span class="sxs-lookup"><span data-stu-id="3009c-183">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="3009c-184">选项</span><span class="sxs-lookup"><span data-stu-id="3009c-184">Option</span></span>                        | <span data-ttu-id="3009c-185">描述</span><span class="sxs-lookup"><span data-stu-id="3009c-185">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="3009c-186">--namespaceName 或 -namespace</span><span class="sxs-lookup"><span data-stu-id="3009c-186">--namespaceName or -namespace</span></span> | <span data-ttu-id="3009c-187">用于生成的 PageModel 的命名空间的名称</span><span class="sxs-lookup"><span data-stu-id="3009c-187">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="3009c-188">--partialView 或 -partial</span><span class="sxs-lookup"><span data-stu-id="3009c-188">--partialView or -partial</span></span>     | <span data-ttu-id="3009c-189">生成分部视图。</span><span class="sxs-lookup"><span data-stu-id="3009c-189">Generate a partial view.</span></span> <span data-ttu-id="3009c-190">如果指定此选项，将忽略布局选项 -l 和 -udl。</span><span class="sxs-lookup"><span data-stu-id="3009c-190">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="3009c-191">--noPageModel 或 -npm</span><span class="sxs-lookup"><span data-stu-id="3009c-191">--noPageModel or -npm</span></span>         | <span data-ttu-id="3009c-192">切换为不针对 Empty 模板生成 PageModel 类</span><span class="sxs-lookup"><span data-stu-id="3009c-192">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="3009c-193">使用 `-h` 开关获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="3009c-193">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="3009c-194">请参阅[搭建 movie 模型的基架](xref:tutorials/razor-pages/model)，查看 `dotnet aspnet-codegenerator razorpage` 示例。</span><span class="sxs-lookup"><span data-stu-id="3009c-194">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="3009c-195">请参阅[基架Identity](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="3009c-195">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
