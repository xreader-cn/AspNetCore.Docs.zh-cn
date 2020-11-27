---
title: dotnet aspnet-codegenerator 命令
author: rick-anderson
description: dotnet aspnet-codegenerator 命令可用于搭建 ASP.NET Core 项目的基架。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/16/2020
no-loc:
- appsettings.json
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
uid: fundamentals/tools/dotnet-aspnet-codegenerator
ms.openlocfilehash: 8844b0014cac58f414d79df4c64bc0efac75bfe1
ms.sourcegitcommit: d29535ea0b4197443fd884aaa6e5b4b763d04fc7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/19/2020
ms.locfileid: "94920698"
---
# <a name="dotnet-aspnet-codegenerator"></a><span data-ttu-id="cd2b6-103">dotnet aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="cd2b6-103">dotnet aspnet-codegenerator</span></span>

<span data-ttu-id="cd2b6-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="cd2b6-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="cd2b6-105">`dotnet aspnet-codegenerator` - 运行 ASP.NET Core 基架引擎。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-105">`dotnet aspnet-codegenerator` - Runs the ASP.NET Core scaffolding engine.</span></span> <span data-ttu-id="cd2b6-106">使用 `dotnet aspnet-codegenerator` 只需要从命令行搭建基架，不必使用 Visual Studio 搭建基架。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-106">`dotnet aspnet-codegenerator` is only required to scaffold from the command line, it's not needed to use scaffolding with Visual Studio.</span></span>

## <a name="install-and-update-aspnet-codegenerator"></a><span data-ttu-id="cd2b6-107">安装和更新 aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="cd2b6-107">Install and update aspnet-codegenerator</span></span>

<span data-ttu-id="cd2b6-108">安装 [.NET SDK](https://dotnet.microsoft.com/download)。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-108">Install the [.NET SDK](https://dotnet.microsoft.com/download).</span></span>

<span data-ttu-id="cd2b6-109">`dotnet-aspnet-codegenerator` 是必须安装的一个[全局工具](/dotnet/core/tools/global-tools)。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-109">`dotnet-aspnet-codegenerator` is a [global tool](/dotnet/core/tools/global-tools) that must be installed.</span></span> <span data-ttu-id="cd2b6-110">以下命令安装 `dotnet-aspnet-codegenerator` 工具的最新稳定版本：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-110">The following command installs the latest stable version of the `dotnet-aspnet-codegenerator` tool:</span></span>

```dotnetcli
dotnet tool install -g dotnet-aspnet-codegenerator
```

<span data-ttu-id="cd2b6-111">以下命令将 `dotnet-aspnet-codegenerator` 更新到已安装的.NET Core SDK 提供的最新稳定版本：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-111">The following command updates `dotnet-aspnet-codegenerator` to the latest stable version available from the installed .NET Core SDKs:</span></span>

```dotnetcli
dotnet tool update -g dotnet-aspnet-codegenerator
```

## <a name="uninstall-aspnet-codegenerator"></a><span data-ttu-id="cd2b6-112">卸载 aspnet-codegenerator</span><span class="sxs-lookup"><span data-stu-id="cd2b6-112">Uninstall aspnet-codegenerator</span></span>

<span data-ttu-id="cd2b6-113">可能需要卸载 `aspnet-codegenerator` 才能解决问题。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-113">It may be necessary to uninstall the `aspnet-codegenerator` to resolve problems.</span></span> <span data-ttu-id="cd2b6-114">例如，如果安装了 `aspnet-codegenerator` 的预览版本，请在安装发布版本之前卸载它。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-114">For example, if you installed a preview version of `aspnet-codegenerator`, uninstall it before installing the released version.</span></span>

<span data-ttu-id="cd2b6-115">以下命令卸载 `dotnet-aspnet-codegenerator` 工具并安装最新稳定版本：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-115">The following commands uninstall the `dotnet-aspnet-codegenerator` tool and installs the latest stable version:</span></span>

```dotnetcli
dotnet tool uninstall -g dotnet-aspnet-codegenerator
dotnet tool install -g dotnet-aspnet-codegenerator
```

## <a name="synopsis"></a><span data-ttu-id="cd2b6-116">摘要</span><span class="sxs-lookup"><span data-stu-id="cd2b6-116">Synopsis</span></span>

```
dotnet aspnet-codegenerator [arguments] [-p|--project] [-n|--nuget-package-dir] [-c|--configuration] [-tfm|--target-framework] [-b|--build-base-path] [--no-build] 
dotnet aspnet-codegenerator [-h|--help]
```

## <a name="description"></a><span data-ttu-id="cd2b6-117">描述</span><span class="sxs-lookup"><span data-stu-id="cd2b6-117">Description</span></span>

<span data-ttu-id="cd2b6-118">`dotnet aspnet-codegenerator` 全局命令运行 ASP.NET Core 代码生成器和基架引擎。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-118">The `dotnet aspnet-codegenerator` global command runs the ASP.NET Core code generator and scaffolding engine.</span></span>

## <a name="arguments"></a><span data-ttu-id="cd2b6-119">自变量</span><span class="sxs-lookup"><span data-stu-id="cd2b6-119">Arguments</span></span>

`generator`

<span data-ttu-id="cd2b6-120">要运行的代码生成器。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-120">The code generator to run.</span></span> <span data-ttu-id="cd2b6-121">以下是可用的生成器：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-121">The following generators are available:</span></span>

| <span data-ttu-id="cd2b6-122">Generator</span><span class="sxs-lookup"><span data-stu-id="cd2b6-122">Generator</span></span>  | <span data-ttu-id="cd2b6-123">操作</span><span class="sxs-lookup"><span data-stu-id="cd2b6-123">Operation</span></span>                                                            |
| ---------- | -------------------------------------------------------------------- |
| <span data-ttu-id="cd2b6-124">area</span><span class="sxs-lookup"><span data-stu-id="cd2b6-124">area</span></span>       | [<span data-ttu-id="cd2b6-125">搭建区域的基架</span><span class="sxs-lookup"><span data-stu-id="cd2b6-125">Scaffolds an Area</span></span>](xref:mvc/controllers/areas)                      |
| <span data-ttu-id="cd2b6-126">controller</span><span class="sxs-lookup"><span data-stu-id="cd2b6-126">controller</span></span> | [<span data-ttu-id="cd2b6-127">搭建控制器的基架</span><span class="sxs-lookup"><span data-stu-id="cd2b6-127">Scaffolds a controller</span></span>](xref:tutorials/first-mvc-app/adding-model)  |
| <span data-ttu-id="cd2b6-128">标识</span><span class="sxs-lookup"><span data-stu-id="cd2b6-128">identity</span></span>   | [<span data-ttu-id="cd2b6-129">构建 Identity</span><span class="sxs-lookup"><span data-stu-id="cd2b6-129">Scaffolds Identity</span></span>](xref:security/authentication/scaffold-identity) |
| <span data-ttu-id="cd2b6-130">razorpage</span><span class="sxs-lookup"><span data-stu-id="cd2b6-130">razorpage</span></span>  | [<span data-ttu-id="cd2b6-131">构建 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="cd2b6-131">Scaffolds Razor Pages</span></span>](xref:tutorials/razor-pages/model)            |
| <span data-ttu-id="cd2b6-132">查看</span><span class="sxs-lookup"><span data-stu-id="cd2b6-132">view</span></span>       | [<span data-ttu-id="cd2b6-133">搭建视图的基架</span><span class="sxs-lookup"><span data-stu-id="cd2b6-133">Scaffolds a view</span></span>](xref:mvc/views/overview)                          |

## <a name="options"></a><span data-ttu-id="cd2b6-134">选项</span><span class="sxs-lookup"><span data-stu-id="cd2b6-134">Options</span></span>

`-n|--nuget-package-dir`

<span data-ttu-id="cd2b6-135">指定 NuGet 包目录。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-135">Specifies the NuGet package directory.</span></span>

`-c|--configuration {Debug|Release}`

<span data-ttu-id="cd2b6-136">定义生成配置。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-136">Defines the build configuration.</span></span> <span data-ttu-id="cd2b6-137">默认值为 `Debug`。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-137">The default value is `Debug`.</span></span>

`-tfm|--target-framework`

<span data-ttu-id="cd2b6-138">要使用的目标[框架](/dotnet/standard/frameworks)。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-138">Target [Framework](/dotnet/standard/frameworks) to use.</span></span> <span data-ttu-id="cd2b6-139">例如 `net46`。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-139">For example, `net46`.</span></span>

`-b|--build-base-path`

<span data-ttu-id="cd2b6-140">生成基本路径。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-140">The build base path.</span></span>

`-h|--help`

<span data-ttu-id="cd2b6-141">打印出有关命令的简短帮助。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-141">Prints out a short help for the command.</span></span>

`--no-build`

<span data-ttu-id="cd2b6-142">运行前不生成项目。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-142">Doesn't build the project before running.</span></span> <span data-ttu-id="cd2b6-143">还将隐式设置 `--no-restore` 标记。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-143">It also implicitly sets the `--no-restore` flag.</span></span>

`-p|--project <PATH>`

<span data-ttu-id="cd2b6-144">指定要运行的项目文件的路径（文件夹名称或完整路径）。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-144">Specifies the path of the project file to run (folder name or full path).</span></span> <span data-ttu-id="cd2b6-145">如果未指定，则默认为当前目录。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-145">If not specified, it defaults to the current directory.</span></span>

## <a name="generator-options"></a><span data-ttu-id="cd2b6-146">生成器选项</span><span class="sxs-lookup"><span data-stu-id="cd2b6-146">Generator options</span></span>

<span data-ttu-id="cd2b6-147">以下各节详细说明了受支持的生成器的可用选项：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-147">The following sections detail the options available for the supported generators:</span></span>

* <span data-ttu-id="cd2b6-148">区域</span><span class="sxs-lookup"><span data-stu-id="cd2b6-148">Area</span></span>
* <span data-ttu-id="cd2b6-149">控制器</span><span class="sxs-lookup"><span data-stu-id="cd2b6-149">Controller</span></span>
* Identity  
* <span data-ttu-id="cd2b6-150">Razorpage</span><span class="sxs-lookup"><span data-stu-id="cd2b6-150">Razorpage</span></span>
* <span data-ttu-id="cd2b6-151">视图</span><span class="sxs-lookup"><span data-stu-id="cd2b6-151">View</span></span>

<a name="area"></a>

### <a name="area-options"></a><span data-ttu-id="cd2b6-152">区域选项</span><span class="sxs-lookup"><span data-stu-id="cd2b6-152">Area options</span></span>

<span data-ttu-id="cd2b6-153">此工具适用于具有控制器和视图的 ASP.NET Core Web 项目。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-153">This tool is intended for ASP.NET Core web projects with controllers and views.</span></span> <span data-ttu-id="cd2b6-154">它不适用于 Razor Pages 应用。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-154">It's not intended for Razor Pages apps.</span></span>

<span data-ttu-id="cd2b6-155">用法：`dotnet aspnet-codegenerator area AreaNameToGenerate`</span><span class="sxs-lookup"><span data-stu-id="cd2b6-155">Usage: `dotnet aspnet-codegenerator area AreaNameToGenerate`</span></span>

<span data-ttu-id="cd2b6-156">前面的命令生成以下文件夹：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-156">The preceding command generates the following folders:</span></span>

* <span data-ttu-id="cd2b6-157">*Areas*</span><span class="sxs-lookup"><span data-stu-id="cd2b6-157">*Areas*</span></span>
  * <span data-ttu-id="cd2b6-158">AreaNameToGenerate</span><span class="sxs-lookup"><span data-stu-id="cd2b6-158">*AreaNameToGenerate*</span></span>
    * <span data-ttu-id="cd2b6-159">*Controllers*</span><span class="sxs-lookup"><span data-stu-id="cd2b6-159">*Controllers*</span></span>
    * <span data-ttu-id="cd2b6-160">*Data*</span><span class="sxs-lookup"><span data-stu-id="cd2b6-160">*Data*</span></span>
    * <span data-ttu-id="cd2b6-161">Models</span><span class="sxs-lookup"><span data-stu-id="cd2b6-161">*Models*</span></span>
    * <span data-ttu-id="cd2b6-162">*Views*</span><span class="sxs-lookup"><span data-stu-id="cd2b6-162">*Views*</span></span>

<a name="ctl"></a>

### <a name="controller-options"></a><span data-ttu-id="cd2b6-163">控制器选项</span><span class="sxs-lookup"><span data-stu-id="cd2b6-163">Controller options</span></span>

<span data-ttu-id="cd2b6-164">下表列出了 `aspnet-codegenerator` `controller` 和 `razorpage` 的选项：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-164">The following table lists options for  `aspnet-codegenerator` `controller` and `razorpage`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="cd2b6-165">下表列出了对于 `aspnet-codegenerator controller` 是唯一的选项：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-165">The following table lists options unique to  `aspnet-codegenerator controller`:</span></span>

| <span data-ttu-id="cd2b6-166">选项</span><span class="sxs-lookup"><span data-stu-id="cd2b6-166">Option</span></span>                         | <span data-ttu-id="cd2b6-167">描述</span><span class="sxs-lookup"><span data-stu-id="cd2b6-167">Description</span></span>                                                                                               |
| ------------------------------ | --------------------------------------------------------------------------------------------------------- |
| <span data-ttu-id="cd2b6-168">--controllerName 或 -name</span><span class="sxs-lookup"><span data-stu-id="cd2b6-168">--controllerName or -name</span></span>      | <span data-ttu-id="cd2b6-169">控制器的名称。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-169">Name of the controller.</span></span>                                                                                   |
| <span data-ttu-id="cd2b6-170">--useAsyncActions 或 -async</span><span class="sxs-lookup"><span data-stu-id="cd2b6-170">--useAsyncActions or -async</span></span>    | <span data-ttu-id="cd2b6-171">生成异步控制器操作。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-171">Generate async controller actions.</span></span>                                                                        |
| <span data-ttu-id="cd2b6-172">--noViews 或 -nv</span><span class="sxs-lookup"><span data-stu-id="cd2b6-172">--noViews or -nv</span></span>               | <span data-ttu-id="cd2b6-173">不生成任何视图。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-173">Generate **no** views.</span></span>                                                                                    |
| <span data-ttu-id="cd2b6-174">--restWithNoViews 或 -api</span><span class="sxs-lookup"><span data-stu-id="cd2b6-174">--restWithNoViews or -api</span></span>      | <span data-ttu-id="cd2b6-175">生成具有 REST 样式 API 的控制器。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-175">Generate a Controller with REST style API.</span></span> <span data-ttu-id="cd2b6-176">假设 `noViews` 并且忽略任何与视图相关的选项。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-176">`noViews` is assumed and any view related options are ignored.</span></span> |
| <span data-ttu-id="cd2b6-177">--readWriteActions 或 -actions</span><span class="sxs-lookup"><span data-stu-id="cd2b6-177">--readWriteActions or -actions</span></span> | <span data-ttu-id="cd2b6-178">不使用模型生成具有读/写操作的控制器。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-178">Generate controller with read/write actions without a model.</span></span>                                              |

<span data-ttu-id="cd2b6-179">使用 `-h` 开关获取 `aspnet-codegenerator controller` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-179">Use the `-h` switch for help on the `aspnet-codegenerator controller` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator controller -h
```

<span data-ttu-id="cd2b6-180">请参阅[搭建 movie 模型的基架](xref:tutorials/first-mvc-app/adding-model)，查看 `dotnet aspnet-codegenerator controller` 示例。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-180">See [Scaffold the movie model](xref:tutorials/first-mvc-app/adding-model) for an example of `dotnet aspnet-codegenerator controller`.</span></span>

### <a name="no-locrazorpage"></a><span data-ttu-id="cd2b6-181">Razorpage</span><span class="sxs-lookup"><span data-stu-id="cd2b6-181">Razorpage</span></span>

<a name="rp"></a>

<span data-ttu-id="cd2b6-182">可以通过指定新页面的名称和要使用的模板来单独搭建 Razor Pages 的基架。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-182">Razor Pages can be individually scaffolded by specifying the name of the new page and the template to use.</span></span> <span data-ttu-id="cd2b6-183">支持如下模板：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-183">The supported templates are:</span></span>

* `Empty`
* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="cd2b6-184">例如，以下命令使用 Edit 模板生成MyEdit.cshtml  和 MyEdit.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-184">For example, the following command uses the Edit template to generate *MyEdit.cshtml* and *MyEdit.cshtml.cs*:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage MyEdit Edit -m Movie -dc RazorPagesMovieContext -outDir Pages/Movies
```

<span data-ttu-id="cd2b6-185">通常不指定模板和生成的文件名，而是创建以下模板：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-185">Typically, the template and generated file name is not specified, and the following templates are created:</span></span>

* `Create`
* `Edit`
* `Delete`
* `Details`
* `List`

<span data-ttu-id="cd2b6-186">下表列出了 `aspnet-codegenerator` `razorpage` 和 `controller` 的选项：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-186">The following table lists options for  `aspnet-codegenerator` `razorpage` and `controller`:</span></span>

[!INCLUDE [aspnet-codegenerator-args-md.md](~/includes/aspnet-codegenerator-args-md.md)]

<span data-ttu-id="cd2b6-187">下表列出了对于 `aspnet-codegenerator razorpage` 是唯一的选项：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-187">The following table lists options unique to  `aspnet-codegenerator razorpage`:</span></span>

| <span data-ttu-id="cd2b6-188">选项</span><span class="sxs-lookup"><span data-stu-id="cd2b6-188">Option</span></span>                        | <span data-ttu-id="cd2b6-189">描述</span><span class="sxs-lookup"><span data-stu-id="cd2b6-189">Description</span></span>                                                                           |
| ----------------------------- | ------------------------------------------------------------------------------------- |
| <span data-ttu-id="cd2b6-190">--namespaceName 或 -namespace</span><span class="sxs-lookup"><span data-stu-id="cd2b6-190">--namespaceName or -namespace</span></span> | <span data-ttu-id="cd2b6-191">用于生成的 PageModel 的命名空间的名称</span><span class="sxs-lookup"><span data-stu-id="cd2b6-191">The name of the namespace to use for the generated PageModel</span></span>                          |
| <span data-ttu-id="cd2b6-192">--partialView 或 -partial</span><span class="sxs-lookup"><span data-stu-id="cd2b6-192">--partialView or -partial</span></span>     | <span data-ttu-id="cd2b6-193">生成分部视图。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-193">Generate a partial view.</span></span> <span data-ttu-id="cd2b6-194">如果指定此选项，将忽略布局选项 -l 和 -udl。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-194">Layout options -l and -udl are ignored if this is specified.</span></span> |
| <span data-ttu-id="cd2b6-195">--noPageModel 或 -npm</span><span class="sxs-lookup"><span data-stu-id="cd2b6-195">--noPageModel or -npm</span></span>         | <span data-ttu-id="cd2b6-196">切换为不针对 Empty 模板生成 PageModel 类</span><span class="sxs-lookup"><span data-stu-id="cd2b6-196">Switch to not generate a PageModel class for Empty template</span></span>                           |

<span data-ttu-id="cd2b6-197">使用 `-h` 开关获取 `aspnet-codegenerator razorpage` 命令方面的帮助：</span><span class="sxs-lookup"><span data-stu-id="cd2b6-197">Use the `-h` switch for help on the `aspnet-codegenerator razorpage` command:</span></span>

```dotnetcli
dotnet aspnet-codegenerator razorpage -h
```

<span data-ttu-id="cd2b6-198">请参阅[搭建 movie 模型的基架](xref:tutorials/razor-pages/model)，查看 `dotnet aspnet-codegenerator razorpage` 示例。</span><span class="sxs-lookup"><span data-stu-id="cd2b6-198">See [Scaffold the movie model](xref:tutorials/razor-pages/model) for an example of `dotnet aspnet-codegenerator razorpage`.</span></span>

### Identity

<span data-ttu-id="cd2b6-199">请参阅[基架Identity](xref:security/authentication/scaffold-identity)</span><span class="sxs-lookup"><span data-stu-id="cd2b6-199">See [Scaffold Identity](xref:security/authentication/scaffold-identity)</span></span>
