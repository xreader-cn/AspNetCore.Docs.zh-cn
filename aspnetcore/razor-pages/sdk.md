---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: 了解 ASP.NET Core 中的 Razor 页面如何使基于页面的编码方式比使用 MVC 更简单高效。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 06/18/2019
uid: razor-pages/sdk
ms.openlocfilehash: fa69e4840377e0c1c8291c7ba9305a27bd3e6b82
ms.sourcegitcommit: 516f166c5f7cec54edf3d9c71e6e2ba53fb3b0e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67196365"
---
# <a name="aspnet-core-razor-sdk"></a><span data-ttu-id="b9cb0-103">ASP.NET Core Razor SDK</span><span class="sxs-lookup"><span data-stu-id="b9cb0-103">ASP.NET Core Razor SDK</span></span>

<span data-ttu-id="b9cb0-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b9cb0-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="b9cb0-105">概述</span><span class="sxs-lookup"><span data-stu-id="b9cb0-105">Overview</span></span>

<span data-ttu-id="b9cb0-106">[!INCLUDE[](~/includes/2.1-SDK.md)]包括`Microsoft.NET.Sdk.Razor`MSBuild SDK (Razor SDK)。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK).</span></span> <span data-ttu-id="b9cb0-107">Razor SDK：</span><span class="sxs-lookup"><span data-stu-id="b9cb0-107">The Razor SDK:</span></span>

* <span data-ttu-id="b9cb0-108">针对基于 ASP.NET Core MVC 的项目，围绕包含 [Razor](xref:mvc/views/razor) 文件的项目的生成、打包和发布设定了体验标准。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-108">Standardizes the experience around building, packaging, and publishing projects containing [Razor](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="b9cb0-109">包含一组预定义的目标、属性和项目，它们允许自定义 Razor 文件的编译。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of Razor files.</span></span>

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="b9cb0-110">Razor SDK 包括`<Content>`具有元素`Include`属性设置为`**\*.cshtml`通配模式。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-110">The Razor SDK includes a `<Content>` element with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="b9cb0-111">匹配的文件将发布。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b9cb0-112">Razor SDK 包括`<Content>`具有的元素`Include`属性设置为`**\*.cshtml`和`**\*.razor`通配模式。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-112">The Razor SDK includes `<Content>` elements with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="b9cb0-113">匹配的文件将发布。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-113">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="b9cb0-114">系统必备</span><span class="sxs-lookup"><span data-stu-id="b9cb0-114">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a><span data-ttu-id="b9cb0-115">使用 Razor SDK</span><span class="sxs-lookup"><span data-stu-id="b9cb0-115">Use the Razor SDK</span></span>

<span data-ttu-id="b9cb0-116">大多数 web 应用程序无需显式引用 Razor SDK。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-116">Most web apps aren't required to explicitly reference the Razor SDK.</span></span>

<span data-ttu-id="b9cb0-117">要使用 Razor SDK 来生成包含 Razor 视图或 Razor 页面的类库，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b9cb0-117">To use the Razor SDK to build class libraries containing Razor views or Razor Pages:</span></span>

* <span data-ttu-id="b9cb0-118">使用 `Microsoft.NET.Sdk.Razor` 而非 `Microsoft.NET.Sdk`：</span><span class="sxs-lookup"><span data-stu-id="b9cb0-118">Use `Microsoft.NET.Sdk.Razor` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="b9cb0-119">通常情况下，对的包引用`Microsoft.AspNetCore.Mvc`，才能接收生成和编译 Razor 页面和 Razor 视图所需的附加依赖项。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-119">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile Razor Pages and Razor views.</span></span> <span data-ttu-id="b9cb0-120">至少，你的项目应添加到包引用：</span><span class="sxs-lookup"><span data-stu-id="b9cb0-120">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`
    
  <span data-ttu-id="b9cb0-121">`Microsoft.AspNetCore.Razor.Design`包提供的 Razor 编译任务和目标项目。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-121">The `Microsoft.AspNetCore.Razor.Design` package provides the Razor compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="b9cb0-122">前面的包包含在 `Microsoft.AspNetCore.Mvc` 中。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-122">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="b9cb0-123">以下标记显示了使用 Razor SDK 用于生成 ASP.NET Core Razor 页面应用程序的 Razor 文件的项目文件：</span><span class="sxs-lookup"><span data-stu-id="b9cb0-123">The following markup shows a project file that uses the Razor SDK to build Razor files for an ASP.NET Core Razor Pages app:</span></span>
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="b9cb0-124">`Microsoft.AspNetCore.Razor.Design`并`Microsoft.AspNetCore.Mvc.Razor.Extensions`包中包含[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-124">The `Microsoft.AspNetCore.Razor.Design` and `Microsoft.AspNetCore.Mvc.Razor.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="b9cb0-125">但是，与版本无关`Microsoft.AspNetCore.App`包引用提供一个元包不包括的最新版本的应用程序到`Microsoft.AspNetCore.Razor.Design`。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-125">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.Razor.Design`.</span></span> <span data-ttu-id="b9cb0-126">项目必须引用的一致版本`Microsoft.AspNetCore.Razor.Design`(或`Microsoft.AspNetCore.Mvc`)，以便包含 razor 的最新生成时修补程序。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-126">Projects must reference a consistent version of `Microsoft.AspNetCore.Razor.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for Razor are included.</span></span> <span data-ttu-id="b9cb0-127">有关详细信息，请参阅[此 GitHub 问题](https://github.com/aspnet/Razor/issues/2553)。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-127">For more information, see [this GitHub issue](https://github.com/aspnet/Razor/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="b9cb0-128">Properties</span><span class="sxs-lookup"><span data-stu-id="b9cb0-128">Properties</span></span>

<span data-ttu-id="b9cb0-129">以下属性控制项目生成过程中 Razor 的 SDK 行为：</span><span class="sxs-lookup"><span data-stu-id="b9cb0-129">The following properties control the Razor's SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="b9cb0-130">`RazorCompileOnBuild` &ndash; 当`true`、 编译并发出作为生成项目的一部分 Razor 程序集。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-130">`RazorCompileOnBuild` &ndash; When `true`, compiles and emits the Razor assembly as part of building the project.</span></span> <span data-ttu-id="b9cb0-131">默认为 `true`。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-131">Defaults to `true`.</span></span>
* <span data-ttu-id="b9cb0-132">`RazorCompileOnPublish` &ndash; 当`true`、 编译并发出作为发布项目的一部分 Razor 程序集。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-132">`RazorCompileOnPublish` &ndash; When `true`, compiles and emits the Razor assembly as part of publishing the project.</span></span> <span data-ttu-id="b9cb0-133">默认为 `true`。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-133">Defaults to `true`.</span></span>

<span data-ttu-id="b9cb0-134">配置输入和输出到 Razor SDK 用于属性和下表中的项。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-134">The properties and items in the following table are used to configure inputs and output to the Razor SDK.</span></span>

| <span data-ttu-id="b9cb0-135">项</span><span class="sxs-lookup"><span data-stu-id="b9cb0-135">Items</span></span> | <span data-ttu-id="b9cb0-136">描述</span><span class="sxs-lookup"><span data-stu-id="b9cb0-136">Description</span></span> |
| ----- | ----------- |
| `RazorGenerate` | <span data-ttu-id="b9cb0-137">输入到代码生成目标的项元素（.cshtml 文件）  。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-137">Item elements (*.cshtml* files) that are inputs to code generation targets.</span></span> |
| `RazorCompile` | <span data-ttu-id="b9cb0-138">项元素 ( *.cs*文件)，是 Razor 编译目标的输入。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-138">Item elements (*.cs* files) that are inputs to Razor compilation targets.</span></span> <span data-ttu-id="b9cb0-139">使用此`ItemGroup`来指定要编译到 Razor 程序集的其他文件。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-139">Use this `ItemGroup` to specify additional files to be compiled into the Razor assembly.</span></span> |
| `RazorTargetAssemblyAttribute` | <span data-ttu-id="b9cb0-140">用于编码生成 Razor 程序集属性的项元素。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-140">Item elements used to code generate attributes for the Razor assembly.</span></span> <span data-ttu-id="b9cb0-141">例如：</span><span class="sxs-lookup"><span data-stu-id="b9cb0-141">For example:</span></span>  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | <span data-ttu-id="b9cb0-142">作为嵌入资源添加到生成的 Razor 程序集的项元素。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-142">Item elements added as embedded resources to the generated Razor assembly.</span></span> |

| <span data-ttu-id="b9cb0-143">属性</span><span class="sxs-lookup"><span data-stu-id="b9cb0-143">Property</span></span> | <span data-ttu-id="b9cb0-144">描述</span><span class="sxs-lookup"><span data-stu-id="b9cb0-144">Description</span></span> |
| -------- | ----------- |
| `RazorTargetName` | <span data-ttu-id="b9cb0-145">Razor 生成的程序集的文件名（不含扩展名）。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-145">File name (without extension) of the assembly produced by Razor.</span></span> | 
| `RazorOutputPath` | <span data-ttu-id="b9cb0-146">Razor 输出目录。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-146">The Razor output directory.</span></span> |
| `RazorCompileToolset` | <span data-ttu-id="b9cb0-147">用于确定用于生成 Razor 程序集的工具集。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-147">Used to determine the toolset used to build the Razor assembly.</span></span> <span data-ttu-id="b9cb0-148">有效值为 `Implicit`、`RazorSDK` 和 `PrecompilationTool`。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-148">Valid values are `Implicit`, `RazorSDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="b9cb0-149">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="b9cb0-149">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="b9cb0-150">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-150">Default is `true`.</span></span> <span data-ttu-id="b9cb0-151">当`true`，包括*web.config*， *.json*，和 *.cshtml*文件作为项目中的内容。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-151">When `true`, includes *web.config*, *.json*, and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="b9cb0-152">当通过引用`Microsoft.NET.Sdk.Web`，文件下*wwwroot*和，还提供了配置文件。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-152">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefaultRazorGenerateItems` | <span data-ttu-id="b9cb0-153">为 `true` 时，包括 `RazorGenerate` 项中 `Content` 项的 .cshtml 文件  。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-153">When `true`, includes *.cshtml* files from `Content` items in `RazorGenerate` items.</span></span> |
| `GenerateRazorTargetAssemblyInfo` | <span data-ttu-id="b9cb0-154">当`true`，生成 *.cs*包含指定的属性文件`RazorAssemblyAttribute`和编译输出中包括的文件。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-154">When `true`, generates a *.cs* file containing attributes specified by `RazorAssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | <span data-ttu-id="b9cb0-155">为 `true` 时，将一组默认的程序集属性添加到 `RazorAssemblyAttribute`。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-155">When `true`, adds a default set of assembly attributes to `RazorAssemblyAttribute`.</span></span> |
| `CopyRazorGenerateFilesToPublishDirectory` | <span data-ttu-id="b9cb0-156">当`true`，复制`RazorGenerate`项 ( *.cshtml*) 文件复制到发布目录。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-156">When `true`, copies `RazorGenerate` items (*.cshtml*) files to the publish directory.</span></span> <span data-ttu-id="b9cb0-157">通常情况下，Razor 文件不需要的已发布的应用，如果他们参与在生成时或发布时编译。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-157">Typically, Razor files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="b9cb0-158">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-158">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="b9cb0-159">为 `true` 时，将引用程序集项复制到发布目录。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-159">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="b9cb0-160">通常情况下，引用程序集不需要的已发布的应用，如果 Razor 编译发生在生成时或发布时间。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-160">Typically, reference assemblies aren't required for a published app if Razor compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="b9cb0-161">设置为`true`如果你已发布的应用需要运行时编译。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-161">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="b9cb0-162">例如，将值设置为`true`如果应用程序修改 *.cshtml*文件在运行时或使用嵌入的视图。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-162">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="b9cb0-163">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-163">Defaults to `false`.</span></span> |
| `IncludeRazorContentInPack` | <span data-ttu-id="b9cb0-164">当`true`，所有 Razor 内容项 ( *.cshtml*文件) 标记为要包含在生成的 NuGet 包中。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-164">When `true`, all Razor content items (*.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="b9cb0-165">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-165">Defaults to `false`.</span></span> |
| `EmbedRazorGenerateSources` | <span data-ttu-id="b9cb0-166">为 `true` 时，将 RazorGenerate (.cshtml) 项作为嵌入的文件添加到生成的 Razor 程序集中  。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-166">When `true`, adds RazorGenerate (*.cshtml*) items as embedded files to the generated Razor assembly.</span></span> <span data-ttu-id="b9cb0-167">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-167">Defaults to `false`.</span></span> |
| `UseRazorBuildServer` | <span data-ttu-id="b9cb0-168">为 `true` 时，使用永久生成服务器进程来卸载代码生成工作。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-168">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="b9cb0-169">默认值为 `UseSharedCompilation`。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-169">Defaults to the value of `UseSharedCompilation`.</span></span> |

<span data-ttu-id="b9cb0-170">有关属性的详细信息，请参阅 [MSBuild 属性](/visualstudio/msbuild/msbuild-properties)。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-170">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="b9cb0-171">目标</span><span class="sxs-lookup"><span data-stu-id="b9cb0-171">Targets</span></span>

<span data-ttu-id="b9cb0-172">Razor SDK 定义两个主要目标：</span><span class="sxs-lookup"><span data-stu-id="b9cb0-172">The Razor SDK defines two primary targets:</span></span>

* <span data-ttu-id="b9cb0-173">`RazorGenerate` &ndash; 代码将生成 *.cs*文件从`RazorGenerate`项元素。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-173">`RazorGenerate` &ndash; Code generates *.cs* files from `RazorGenerate` item elements.</span></span> <span data-ttu-id="b9cb0-174">使用 `RazorGenerateDependsOn` 属性指定可在此目标之前或之后运行的其他目标。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-174">Use `RazorGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="b9cb0-175">`RazorCompile` &ndash; 生成的编译 *.cs*到 Razor 程序集文件中。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-175">`RazorCompile` &ndash; Compiles generated *.cs* files in to a Razor assembly.</span></span> <span data-ttu-id="b9cb0-176">使用 `RazorCompileDependsOn` 指定可在此目标之前或之后运行的其他目标。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-176">Use `RazorCompileDependsOn` to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-razor-views"></a><span data-ttu-id="b9cb0-177">Razor 视图的运行时编译</span><span class="sxs-lookup"><span data-stu-id="b9cb0-177">Runtime compilation of Razor views</span></span>

* <span data-ttu-id="b9cb0-178">默认情况下，Razor SDK 不发布执行运行时编译所需的引用程序集。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-178">By default, the Razor SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="b9cb0-179">当应用程序模型依赖于运行时编译时，这会导致编译失败&mdash;例如，应用在发布后使用嵌入视图或更改视图。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-179">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="b9cb0-180">将 `CopyRefAssembliesToPublishDirectory` 设置为 `true`，以继续发布引用程序集。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-180">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="b9cb0-181">对于 web 应用，请确保您的应用程序所面向`Microsoft.NET.Sdk.Web`SDK。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-181">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="razor-language-version"></a><span data-ttu-id="b9cb0-182">Razor 语言版本</span><span class="sxs-lookup"><span data-stu-id="b9cb0-182">Razor language version</span></span>

<span data-ttu-id="b9cb0-183">面向时`Microsoft.NET.Sdk.Web`SDK，Razor 语言版本来推断应用程序的目标框架版本。</span><span class="sxs-lookup"><span data-stu-id="b9cb0-183">When targeting the `Microsoft.NET.Sdk.Web` SDK, the Razor language version is inferred from the the app's target framework version.</span></span> <span data-ttu-id="b9cb0-184">对于项目面向`Microsoft.NET.Sdk.Razor`SDK 或在极少数的情况下应用需要推断值的 Razor 语言版本不同，可以通过设置配置版本`<RazorLangVersion>`应用的项目文件中的属性：</span><span class="sxs-lookup"><span data-stu-id="b9cb0-184">For projects targeting the `Microsoft.NET.Sdk.Razor` SDK or in the rare case that the app requires a different Razor language version than the inferred value, a version can be configured by setting the `<RazorLangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

## <a name="additional-resources"></a><span data-ttu-id="b9cb0-185">其他资源</span><span class="sxs-lookup"><span data-stu-id="b9cb0-185">Additional resources</span></span>

* [<span data-ttu-id="b9cb0-186">.NET Core 的 csproj 格式的新增内容</span><span class="sxs-lookup"><span data-stu-id="b9cb0-186">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="b9cb0-187">常用的 MSBuild 项目项</span><span class="sxs-lookup"><span data-stu-id="b9cb0-187">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
