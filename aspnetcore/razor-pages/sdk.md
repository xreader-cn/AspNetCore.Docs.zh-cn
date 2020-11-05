---
title: 'ASP.NET Core :::no-loc(Razor)::: SDK'
author: Rick-Anderson
description: '了解 ASP.NET Core 中的 :::no-loc(Razor)::: Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。'
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 03/26/2020
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
uid: razor-pages/sdk
ms.openlocfilehash: d3b01889b7634dce8ef1d6a4886a9a6ac39a6473
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060867"
---
# <a name="aspnet-core-no-locrazor-sdk"></a><span data-ttu-id="4bf0c-103">ASP.NET Core :::no-loc(Razor)::: SDK</span><span class="sxs-lookup"><span data-stu-id="4bf0c-103">ASP.NET Core :::no-loc(Razor)::: SDK</span></span>

<span data-ttu-id="4bf0c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="4bf0c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

## <a name="overview"></a><span data-ttu-id="4bf0c-105">概述</span><span class="sxs-lookup"><span data-stu-id="4bf0c-105">Overview</span></span>

<span data-ttu-id="4bf0c-106">[!INCLUDE[](~/includes/2.1-SDK.md)] 包含 `Microsoft.NET.Sdk.:::no-loc(Razor):::` MSBuild SDK (:::no-loc(Razor)::: SDK)。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-106">The [!INCLUDE[](~/includes/2.1-SDK.md)] includes the `Microsoft.NET.Sdk.:::no-loc(Razor):::` MSBuild SDK (:::no-loc(Razor)::: SDK).</span></span> <span data-ttu-id="4bf0c-107">:::no-loc(Razor)::: SDK：</span><span class="sxs-lookup"><span data-stu-id="4bf0c-107">The :::no-loc(Razor)::: SDK:</span></span>

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="4bf0c-108">需要生成、打包和发布包含 [:::no-loc(Razor):::](xref:mvc/views/razor) 文件的项目，这些项目用于基于 ASP.NET Core MVC 的项目或 [:::no-loc(Blazor):::](xref:blazor/index) 项目。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-108">Is required to build, package, and publish projects containing [:::no-loc(Razor):::](xref:mvc/views/razor) files for ASP.NET Core MVC-based or [:::no-loc(Blazor):::](xref:blazor/index) projects.</span></span>
* <span data-ttu-id="4bf0c-109">包含一组预定义的目标、属性和项目，它们可用于自定义 :::no-loc(Razor):::（.cshtml 或 .razor）文件的编译 。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-109">Includes a set of predefined targets, properties, and items that allow customizing the compilation of :::no-loc(Razor)::: ( *.cshtml* or *.razor* ) files.</span></span>

<span data-ttu-id="4bf0c-110">:::no-loc(Razor)::: SDK 包含 `Content` 项，其 `Include` 属性设置为 `**\*.cshtml` 和 `**\*.razor` 通配模式。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-110">The :::no-loc(Razor)::: SDK includes `Content` items with `Include` attributes set to the `**\*.cshtml` and `**\*.razor` globbing patterns.</span></span> <span data-ttu-id="4bf0c-111">发布匹配文件。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-111">Matching files are published.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* <span data-ttu-id="4bf0c-112">针对基于 ASP.NET Core MVC 的项目，围绕包含 [:::no-loc(Razor):::](xref:mvc/views/razor) 文件的项目的生成、打包和发布设定了体验标准。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-112">Standardizes the experience around building, packaging, and publishing projects containing [:::no-loc(Razor):::](xref:mvc/views/razor) files for ASP.NET Core MVC-based projects.</span></span>
* <span data-ttu-id="4bf0c-113">包含一组预定义的目标、属性和项目，它们允许自定义 :::no-loc(Razor)::: 文件的编译。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-113">Includes a set of predefined targets, properties, and items that allow customizing the compilation of :::no-loc(Razor)::: files.</span></span>

<span data-ttu-id="4bf0c-114">:::no-loc(Razor)::: SDK 包含 `Content` 项，其 `Include` 属性设置为 `**\*.cshtml` 通配模式。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-114">The :::no-loc(Razor)::: SDK includes a `Content` item with an `Include` attribute set to the `**\*.cshtml` globbing pattern.</span></span> <span data-ttu-id="4bf0c-115">发布匹配文件。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-115">Matching files are published.</span></span>

::: moniker-end

## <a name="prerequisites"></a><span data-ttu-id="4bf0c-116">先决条件</span><span class="sxs-lookup"><span data-stu-id="4bf0c-116">Prerequisites</span></span>

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-no-locrazor-sdk"></a><span data-ttu-id="4bf0c-117">使用 :::no-loc(Razor)::: SDK</span><span class="sxs-lookup"><span data-stu-id="4bf0c-117">Use the :::no-loc(Razor)::: SDK</span></span>

<span data-ttu-id="4bf0c-118">大多数 Web 应用不需要显式引用 :::no-loc(Razor)::: SDK。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-118">Most web apps aren't required to explicitly reference the :::no-loc(Razor)::: SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4bf0c-119">若要使用 :::no-loc(Razor)::: SDK 生成包含 :::no-loc(Razor)::: 视图或 :::no-loc(Razor)::: Pages 的类库，建议从 :::no-loc(Razor)::: 类库 (RCL) 项目模板开始。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-119">To use the :::no-loc(Razor)::: SDK to build class libraries containing :::no-loc(Razor)::: views or :::no-loc(Razor)::: Pages, we recommend starting with the :::no-loc(Razor)::: class library (RCL) project template.</span></span> <span data-ttu-id="4bf0c-120">用于生成 :::no-loc(Blazor)::: (.razor) 文件的 RCL 至少需要引用 [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) 包。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-120">An RCL that's used to build :::no-loc(Blazor)::: ( *.razor* ) files minimally requires a reference to the [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) package.</span></span> <span data-ttu-id="4bf0c-121">用于生成 :::no-loc(Razor)::: 视图或页面（.cshtml 文件）的 RCL 至少需要面向 `netcoreapp3.0` 或更高版本，且其项目文件中至少具有指向 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的 `FrameworkReference`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-121">An RCL that's used to build :::no-loc(Razor)::: views or pages ( *.cshtml* files) minimally requires targeting `netcoreapp3.0` or later and has a `FrameworkReference` to the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) in its project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4bf0c-122">若要使用 :::no-loc(Razor)::: SDK 来生成包含 :::no-loc(Razor)::: 视图或 :::no-loc(Razor)::: Pages 的类库，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="4bf0c-122">To use the :::no-loc(Razor)::: SDK to build class libraries containing :::no-loc(Razor)::: views or :::no-loc(Razor)::: Pages:</span></span>

* <span data-ttu-id="4bf0c-123">使用 `Microsoft.NET.Sdk.:::no-loc(Razor):::` 而非 `Microsoft.NET.Sdk`：</span><span class="sxs-lookup"><span data-stu-id="4bf0c-123">Use `Microsoft.NET.Sdk.:::no-loc(Razor):::` instead of `Microsoft.NET.Sdk`:</span></span>

  ```xml
  <Project SDK="Microsoft.NET.Sdk.:::no-loc(Razor):::">
    <!-- omitted for brevity -->
  </Project>
  ```

* <span data-ttu-id="4bf0c-124">通常，对 `Microsoft.AspNetCore.Mvc` 的包引用需要接收生成和编译 :::no-loc(Razor)::: Pages 和 :::no-loc(Razor)::: 视图所需的其他依赖项。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-124">Typically, a package reference to `Microsoft.AspNetCore.Mvc` is required to receive additional dependencies that are required to build and compile :::no-loc(Razor)::: Pages and :::no-loc(Razor)::: views.</span></span> <span data-ttu-id="4bf0c-125">项目至少应将包引用添加到：</span><span class="sxs-lookup"><span data-stu-id="4bf0c-125">At a minimum, your project should add package references to:</span></span>

  * `Microsoft.AspNetCore.:::no-loc(Razor):::.Design`
  * `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.Extensions`
  * `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::`

  <span data-ttu-id="4bf0c-126">`Microsoft.AspNetCore.:::no-loc(Razor):::.Design` 包为项目提供 :::no-loc(Razor)::: 编译任务和目标。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-126">The `Microsoft.AspNetCore.:::no-loc(Razor):::.Design` package provides the :::no-loc(Razor)::: compilation tasks and targets for the project.</span></span>

  <span data-ttu-id="4bf0c-127">前面的包包含在 `Microsoft.AspNetCore.Mvc` 中。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-127">The preceding packages are included in `Microsoft.AspNetCore.Mvc`.</span></span> <span data-ttu-id="4bf0c-128">以下标记显示了使用 :::no-loc(Razor)::: SDK 为 ASP.NET Core :::no-loc(Razor)::: Pages 应用生成 :::no-loc(Razor)::: 文件的项目文件：</span><span class="sxs-lookup"><span data-stu-id="4bf0c-128">The following markup shows a project file that uses the :::no-loc(Razor)::: SDK to build :::no-loc(Razor)::: files for an ASP.NET Core :::no-loc(Razor)::: Pages app:</span></span>

  [!code-xml[](sdk/sample/:::no-loc(Razor):::SDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> <span data-ttu-id="4bf0c-129">`Microsoft.AspNetCore.:::no-loc(Razor):::.Design` 和 `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.Extensions` 包均包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-129">The `Microsoft.AspNetCore.:::no-loc(Razor):::.Design` and `Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.Extensions` packages are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="4bf0c-130">但无版本的 `Microsoft.AspNetCore.App` 包引用可为不含最新版 `Microsoft.AspNetCore.:::no-loc(Razor):::.Design` 的应用提供元包。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-130">However, the version-less `Microsoft.AspNetCore.App` package reference provides a metapackage to the app that doesn't include the latest version of `Microsoft.AspNetCore.:::no-loc(Razor):::.Design`.</span></span> <span data-ttu-id="4bf0c-131">项目必须引用一致版本的 `Microsoft.AspNetCore.:::no-loc(Razor):::.Design`（或 `Microsoft.AspNetCore.Mvc`），以便包含 :::no-loc(Razor)::: 的最新生成时修补程序。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-131">Projects must reference a consistent version of `Microsoft.AspNetCore.:::no-loc(Razor):::.Design` (or `Microsoft.AspNetCore.Mvc`) so that the latest build-time fixes for :::no-loc(Razor)::: are included.</span></span> <span data-ttu-id="4bf0c-132">有关详细信息，请参阅[此 GitHub 问题](https://github.com/aspnet/:::no-loc(Razor):::/issues/2553)。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-132">For more information, see [this GitHub issue](https://github.com/aspnet/:::no-loc(Razor):::/issues/2553).</span></span>

::: moniker-end

### <a name="properties"></a><span data-ttu-id="4bf0c-133">属性</span><span class="sxs-lookup"><span data-stu-id="4bf0c-133">Properties</span></span>

<span data-ttu-id="4bf0c-134">以下属性控制项目生成过程中 :::no-loc(Razor)::: 的 SDK 行为：</span><span class="sxs-lookup"><span data-stu-id="4bf0c-134">The following properties control the :::no-loc(Razor):::'s SDK behavior as part of a project build:</span></span>

* <span data-ttu-id="4bf0c-135">`:::no-loc(Razor):::CompileOnBuild`：若为 `true`，则在生成项目的过程中，编译并发出 :::no-loc(Razor)::: 程序集。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-135">`:::no-loc(Razor):::CompileOnBuild`: When `true`, compiles and emits the :::no-loc(Razor)::: assembly as part of building the project.</span></span> <span data-ttu-id="4bf0c-136">默认为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-136">Defaults to `true`.</span></span>
* <span data-ttu-id="4bf0c-137">`:::no-loc(Razor):::CompileOnPublish`：若为 `true`，则在发布项目的过程中，编译并发出 :::no-loc(Razor)::: 程序集。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-137">`:::no-loc(Razor):::CompileOnPublish`: When `true`, compiles and emits the :::no-loc(Razor)::: assembly as part of publishing the project.</span></span> <span data-ttu-id="4bf0c-138">默认为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-138">Defaults to `true`.</span></span>

<span data-ttu-id="4bf0c-139">下表中的属性和项用于配置 :::no-loc(Razor)::: SDK 的输入和输出。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-139">The properties and items in the following table are used to configure inputs and output to the :::no-loc(Razor)::: SDK.</span></span>

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> <span data-ttu-id="4bf0c-140">从 ASP.NET Core 3.0 开始，如果禁用项目文件中的 `:::no-loc(Razor):::CompileOnBuild` 或 `:::no-loc(Razor):::CompileOnPublish` MSBuild 属性，则不会默认提供 MVC 视图或 :::no-loc(Razor)::: Pages。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-140">Starting with ASP.NET Core 3.0, MVC Views or :::no-loc(Razor)::: Pages aren't served by default if the `:::no-loc(Razor):::CompileOnBuild` or `:::no-loc(Razor):::CompileOnPublish` MSBuild properties in the project file are disabled.</span></span> <span data-ttu-id="4bf0c-141">如果应用依赖运行时编译来处理 .cshtml 文件，则应用程序必须添加对 [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation) 包的显式引用。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-141">Applications must add an explicit reference to the [Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.:::no-loc(Razor):::.RuntimeCompilation) package if the app relies on runtime compilation to process *.cshtml* files.</span></span>

::: moniker-end

| <span data-ttu-id="4bf0c-142">项</span><span class="sxs-lookup"><span data-stu-id="4bf0c-142">Items</span></span> | <span data-ttu-id="4bf0c-143">描述</span><span class="sxs-lookup"><span data-stu-id="4bf0c-143">Description</span></span> |
| ----- | ----------- |
| `:::no-loc(Razor):::Generate` | <span data-ttu-id="4bf0c-144">输入到代码生成的项元素（.cshtml 文件）。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-144">Item elements ( *.cshtml* files) that are inputs to code generation.</span></span> |
| `:::no-loc(Razor):::Component` | <span data-ttu-id="4bf0c-145">输入到 :::no-loc(Razor)::: 组件代码生成的项元素（.razor 文件）。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-145">Item elements ( *.razor* files) that are inputs to :::no-loc(Razor)::: component code generation.</span></span> |
| `:::no-loc(Razor):::Compile` | <span data-ttu-id="4bf0c-146">输入到 :::no-loc(Razor)::: 编译目标的项元素（.cs 文件）。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-146">Item elements ( *.cs* files) that are inputs to :::no-loc(Razor)::: compilation targets.</span></span> <span data-ttu-id="4bf0c-147">使用此 `ItemGroup` 指定要编译到 :::no-loc(Razor)::: 程序集中的其他文件。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-147">Use this `ItemGroup` to specify additional files to be compiled into the :::no-loc(Razor)::: assembly.</span></span> |
| `:::no-loc(Razor):::TargetAssemblyAttribute` | <span data-ttu-id="4bf0c-148">用于编码生成 :::no-loc(Razor)::: 程序集属性的项元素。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-148">Item elements used to code generate attributes for the :::no-loc(Razor)::: assembly.</span></span> <span data-ttu-id="4bf0c-149">例如：</span><span class="sxs-lookup"><span data-stu-id="4bf0c-149">For example:</span></span>  <br>`:::no-loc(Razor):::AssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `:::no-loc(Razor):::EmbeddedResource` | <span data-ttu-id="4bf0c-150">作为嵌入的资源添加到生成的 :::no-loc(Razor)::: 程序集中的项元素。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-150">Item elements added as embedded resources to the generated :::no-loc(Razor)::: assembly.</span></span> |

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="4bf0c-151">Property</span><span class="sxs-lookup"><span data-stu-id="4bf0c-151">Property</span></span> | <span data-ttu-id="4bf0c-152">描述</span><span class="sxs-lookup"><span data-stu-id="4bf0c-152">Description</span></span> |
| -------- | ----------- |
| `:::no-loc(Razor):::TargetName` | <span data-ttu-id="4bf0c-153">:::no-loc(Razor)::: 生成的程序集的文件名（不含扩展名）。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-153">File name (without extension) of the assembly produced by :::no-loc(Razor):::.</span></span> |
| `:::no-loc(Razor):::OutputPath` | <span data-ttu-id="4bf0c-154">:::no-loc(Razor)::: 输出目录。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-154">The :::no-loc(Razor)::: output directory.</span></span> |
| `:::no-loc(Razor):::CompileToolset` | <span data-ttu-id="4bf0c-155">用于确定用于生成 :::no-loc(Razor)::: 程序集的工具集。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-155">Used to determine the toolset used to build the :::no-loc(Razor)::: assembly.</span></span> <span data-ttu-id="4bf0c-156">有效值为 `Implicit`、`:::no-loc(Razor):::SDK` 和 `PrecompilationTool`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-156">Valid values are `Implicit`, `:::no-loc(Razor):::SDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="4bf0c-157">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="4bf0c-157">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="4bf0c-158">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-158">Default is `true`.</span></span> <span data-ttu-id="4bf0c-159">若为 `true`，则包含 web.config、.json 和 .cshtml 文件作为项目中的内容  。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-159">When `true`, includes *web.config* , *.json* , and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="4bf0c-160">通过 `Microsoft.NET.Sdk.Web` 引用时，还包括 wwwroot 下的文件和配置文件。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-160">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefault:::no-loc(Razor):::GenerateItems` | <span data-ttu-id="4bf0c-161">为 `true` 时，包括 `:::no-loc(Razor):::Generate` 项中 `Content` 项的 .cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-161">When `true`, includes *.cshtml* files from `Content` items in `:::no-loc(Razor):::Generate` items.</span></span> |
| `Generate:::no-loc(Razor):::TargetAssemblyInfo` | <span data-ttu-id="4bf0c-162">若为 `true`，则生成 .cs 文件（其中包含由 `:::no-loc(Razor):::AssemblyAttribute` 指定的属性），并将文件包含在编译输出中。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-162">When `true`, generates a *.cs* file containing attributes specified by `:::no-loc(Razor):::AssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefault:::no-loc(Razor):::TargetAssemblyInfoAttributes` | <span data-ttu-id="4bf0c-163">为 `true` 时，将一组默认的程序集属性添加到 `:::no-loc(Razor):::AssemblyAttribute`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-163">When `true`, adds a default set of assembly attributes to `:::no-loc(Razor):::AssemblyAttribute`.</span></span> |
| `Copy:::no-loc(Razor):::GenerateFilesToPublishDirectory` | <span data-ttu-id="4bf0c-164">若为 `true`，则将 `:::no-loc(Razor):::Generate` 项 (.cshtml) 文件复制到发布目录。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-164">When `true`, copies `:::no-loc(Razor):::Generate` items ( *.cshtml* ) files to the publish directory.</span></span> <span data-ttu-id="4bf0c-165">如果在生成时或发布时参与编译，则通常发布的应用无需 :::no-loc(Razor)::: 文件。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-165">Typically, :::no-loc(Razor)::: files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="4bf0c-166">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-166">Defaults to `false`.</span></span> |
| `PreserveCompilationReferences` | <span data-ttu-id="4bf0c-167">为 `true` 时，将引用程序集项复制到发布目录。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-167">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="4bf0c-168">如果在生成时或发布时出现 :::no-loc(Razor)::: 编译，则通常发布的应用无需引用程序集。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-168">Typically, reference assemblies aren't required for a published app if :::no-loc(Razor)::: compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="4bf0c-169">如果发布的应用需要运行时编译，则设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-169">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="4bf0c-170">例如，如果应用在运行时修改 .cshtml 文件或使用嵌入视图，则将值设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-170">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="4bf0c-171">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-171">Defaults to `false`.</span></span> |
| `Include:::no-loc(Razor):::ContentInPack` | <span data-ttu-id="4bf0c-172">若为 `true`，则所有 :::no-loc(Razor)::: 内容项（.cshtml 文件）标记为包含在生成的 NuGet 包中。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-172">When `true`, all :::no-loc(Razor)::: content items ( *.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="4bf0c-173">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-173">Defaults to `false`.</span></span> |
| `Embed:::no-loc(Razor):::GenerateSources` | <span data-ttu-id="4bf0c-174">若为 `true`，将 :::no-loc(Razor):::Generate (.cshtml) 项作为嵌入的文件添加到生成的 :::no-loc(Razor)::: 程序集中。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-174">When `true`, adds :::no-loc(Razor):::Generate ( *.cshtml* ) items as embedded files to the generated :::no-loc(Razor)::: assembly.</span></span> <span data-ttu-id="4bf0c-175">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-175">Defaults to `false`.</span></span> |
| `Use:::no-loc(Razor):::BuildServer` | <span data-ttu-id="4bf0c-176">为 `true` 时，使用永久生成服务器进程来卸载代码生成工作。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-176">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="4bf0c-177">默认值为 `UseSharedCompilation`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-177">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="4bf0c-178">若为 `true`，则 SDK 会生成 MVC 在运行时使用的其他属性，以执行应用程序部件的发现。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-178">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="4bf0c-179">要从面向 Web 或 :::no-loc(Razor)::: SDK 的项目的 `Content` 项组中排除的项元素的 glob 模式</span><span class="sxs-lookup"><span data-stu-id="4bf0c-179">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or :::no-loc(Razor)::: SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="4bf0c-180">若为 `true`，则 .config 和 .json 文件不会复制到生成输出目录 。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-180">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `Add:::no-loc(Razor):::SupportForMvc` | <span data-ttu-id="4bf0c-181">若为 `true`，则配置 :::no-loc(Razor)::: SDK，以添加对生成包含 MVC 视图或 :::no-loc(Razor)::: Pages 的应用程序时所需的 MVC 配置的支持。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-181">When `true`, configures the :::no-loc(Razor)::: SDK to add support for the MVC configuration that is required when building applications containing MVC views or :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="4bf0c-182">对于面向 Web SDK 的 .NET Core 3.0 或更高版本的项目，隐式设置该属性</span><span class="sxs-lookup"><span data-stu-id="4bf0c-182">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `:::no-loc(Razor):::LangVersion` | <span data-ttu-id="4bf0c-183">要面向的 :::no-loc(Razor)::: 语言版本。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-183">The version of the :::no-loc(Razor)::: Language to target.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="4bf0c-184">Property</span><span class="sxs-lookup"><span data-stu-id="4bf0c-184">Property</span></span> | <span data-ttu-id="4bf0c-185">描述</span><span class="sxs-lookup"><span data-stu-id="4bf0c-185">Description</span></span> |
| -------- | ----------- |
| `:::no-loc(Razor):::TargetName` | <span data-ttu-id="4bf0c-186">:::no-loc(Razor)::: 生成的程序集的文件名（不含扩展名）。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-186">File name (without extension) of the assembly produced by :::no-loc(Razor):::.</span></span> |
| `:::no-loc(Razor):::OutputPath` | <span data-ttu-id="4bf0c-187">:::no-loc(Razor)::: 输出目录。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-187">The :::no-loc(Razor)::: output directory.</span></span> |
| `:::no-loc(Razor):::CompileToolset` | <span data-ttu-id="4bf0c-188">用于确定用于生成 :::no-loc(Razor)::: 程序集的工具集。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-188">Used to determine the toolset used to build the :::no-loc(Razor)::: assembly.</span></span> <span data-ttu-id="4bf0c-189">有效值为 `Implicit`、`:::no-loc(Razor):::SDK` 和 `PrecompilationTool`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-189">Valid values are `Implicit`, `:::no-loc(Razor):::SDK`, and `PrecompilationTool`.</span></span> |
| [<span data-ttu-id="4bf0c-190">EnableDefaultContentItems</span><span class="sxs-lookup"><span data-stu-id="4bf0c-190">EnableDefaultContentItems</span></span>](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | <span data-ttu-id="4bf0c-191">默认值为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-191">Default is `true`.</span></span> <span data-ttu-id="4bf0c-192">若为 `true`，则包含 web.config、.json 和 .cshtml 文件作为项目中的内容  。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-192">When `true`, includes *web.config* , *.json* , and *.cshtml* files as content in the project.</span></span> <span data-ttu-id="4bf0c-193">通过 `Microsoft.NET.Sdk.Web` 引用时，还包括 wwwroot 下的文件和配置文件。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-193">When referenced via `Microsoft.NET.Sdk.Web`, files under *wwwroot* and config files are also included.</span></span> |
| `EnableDefault:::no-loc(Razor):::GenerateItems` | <span data-ttu-id="4bf0c-194">为 `true` 时，包括 `:::no-loc(Razor):::Generate` 项中 `Content` 项的 .cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-194">When `true`, includes *.cshtml* files from `Content` items in `:::no-loc(Razor):::Generate` items.</span></span> |
| `Generate:::no-loc(Razor):::TargetAssemblyInfo` | <span data-ttu-id="4bf0c-195">若为 `true`，则生成 .cs 文件（其中包含由 `:::no-loc(Razor):::AssemblyAttribute` 指定的属性），并将文件包含在编译输出中。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-195">When `true`, generates a *.cs* file containing attributes specified by `:::no-loc(Razor):::AssemblyAttribute` and includes the file in the compile output.</span></span> |
| `EnableDefault:::no-loc(Razor):::TargetAssemblyInfoAttributes` | <span data-ttu-id="4bf0c-196">为 `true` 时，将一组默认的程序集属性添加到 `:::no-loc(Razor):::AssemblyAttribute`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-196">When `true`, adds a default set of assembly attributes to `:::no-loc(Razor):::AssemblyAttribute`.</span></span> |
| `Copy:::no-loc(Razor):::GenerateFilesToPublishDirectory` | <span data-ttu-id="4bf0c-197">若为 `true`，则将 `:::no-loc(Razor):::Generate` 项 (.cshtml) 文件复制到发布目录。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-197">When `true`, copies `:::no-loc(Razor):::Generate` items ( *.cshtml* ) files to the publish directory.</span></span> <span data-ttu-id="4bf0c-198">如果在生成时或发布时参与编译，则通常发布的应用无需 :::no-loc(Razor)::: 文件。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-198">Typically, :::no-loc(Razor)::: files aren't required for a published app if they participate in compilation at build-time or publish-time.</span></span> <span data-ttu-id="4bf0c-199">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-199">Defaults to `false`.</span></span> |
| `CopyRefAssembliesToPublishDirectory` | <span data-ttu-id="4bf0c-200">为 `true` 时，将引用程序集项复制到发布目录。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-200">When `true`, copy reference assembly items to the publish directory.</span></span> <span data-ttu-id="4bf0c-201">如果在生成时或发布时出现 :::no-loc(Razor)::: 编译，则通常发布的应用无需引用程序集。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-201">Typically, reference assemblies aren't required for a published app if :::no-loc(Razor)::: compilation occurs at build-time or publish-time.</span></span> <span data-ttu-id="4bf0c-202">如果发布的应用需要运行时编译，则设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-202">Set to `true` if your published app requires runtime compilation.</span></span> <span data-ttu-id="4bf0c-203">例如，如果应用在运行时修改 .cshtml 文件或使用嵌入视图，则将值设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-203">For example, set the value to `true` if the app modifies *.cshtml* files at runtime or uses embedded views.</span></span> <span data-ttu-id="4bf0c-204">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-204">Defaults to `false`.</span></span> |
| `Include:::no-loc(Razor):::ContentInPack` | <span data-ttu-id="4bf0c-205">若为 `true`，则所有 :::no-loc(Razor)::: 内容项（.cshtml 文件）标记为包含在生成的 NuGet 包中。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-205">When `true`, all :::no-loc(Razor)::: content items ( *.cshtml* files) are marked for inclusion in the generated NuGet package.</span></span> <span data-ttu-id="4bf0c-206">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-206">Defaults to `false`.</span></span> |
| `Embed:::no-loc(Razor):::GenerateSources` | <span data-ttu-id="4bf0c-207">若为 `true`，将 :::no-loc(Razor):::Generate (.cshtml) 项作为嵌入的文件添加到生成的 :::no-loc(Razor)::: 程序集中。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-207">When `true`, adds :::no-loc(Razor):::Generate ( *.cshtml* ) items as embedded files to the generated :::no-loc(Razor)::: assembly.</span></span> <span data-ttu-id="4bf0c-208">默认为 `false`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-208">Defaults to `false`.</span></span> |
| `Use:::no-loc(Razor):::BuildServer` | <span data-ttu-id="4bf0c-209">为 `true` 时，使用永久生成服务器进程来卸载代码生成工作。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-209">When `true`, uses a persistent build server process to offload code generation work.</span></span> <span data-ttu-id="4bf0c-210">默认值为 `UseSharedCompilation`。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-210">Defaults to the value of `UseSharedCompilation`.</span></span> |
| `GenerateMvcApplicationPartsAssemblyAttributes` | <span data-ttu-id="4bf0c-211">若为 `true`，则 SDK 会生成 MVC 在运行时使用的其他属性，以执行应用程序部件的发现。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-211">When `true`, the SDK generates additional attributes used by MVC at runtime to perform application part discovery.</span></span> |
| `DefaultWebContentItemExcludes` | <span data-ttu-id="4bf0c-212">要从面向 Web 或 :::no-loc(Razor)::: SDK 的项目的 `Content` 项组中排除的项元素的 glob 模式</span><span class="sxs-lookup"><span data-stu-id="4bf0c-212">A globbing pattern for item elements that are to be excluded from the `Content` item group in projects targeting the Web or :::no-loc(Razor)::: SDK</span></span> |
| `ExcludeConfigFilesFromBuildOutput` | <span data-ttu-id="4bf0c-213">若为 `true`，则 .config 和 .json 文件不会复制到生成输出目录 。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-213">When `true`, *.config* and *.json* files do not get copied to the build output directory.</span></span> |
| `Add:::no-loc(Razor):::SupportForMvc` | <span data-ttu-id="4bf0c-214">若为 `true`，则配置 :::no-loc(Razor)::: SDK，以添加对生成包含 MVC 视图或 :::no-loc(Razor)::: Pages 的应用程序时所需的 MVC 配置的支持。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-214">When `true`, configures the :::no-loc(Razor)::: SDK to add support for the MVC configuration that is required when building applications containing MVC views or :::no-loc(Razor)::: Pages.</span></span> <span data-ttu-id="4bf0c-215">对于面向 Web SDK 的 .NET Core 3.0 或更高版本的项目，隐式设置该属性</span><span class="sxs-lookup"><span data-stu-id="4bf0c-215">This property is implicitly set for .NET Core 3.0 or later projects targeting the Web SDK</span></span> |
| `:::no-loc(Razor):::LangVersion` | <span data-ttu-id="4bf0c-216">要面向的 :::no-loc(Razor)::: 语言版本。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-216">The version of the :::no-loc(Razor)::: Language to target.</span></span> |

::: moniker-end

<span data-ttu-id="4bf0c-217">有关属性的详细信息，请参阅 [MSBuild 属性](/visualstudio/msbuild/msbuild-properties)。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-217">For more information on properties, see [MSBuild properties](/visualstudio/msbuild/msbuild-properties).</span></span>

### <a name="targets"></a><span data-ttu-id="4bf0c-218">目标</span><span class="sxs-lookup"><span data-stu-id="4bf0c-218">Targets</span></span>

<span data-ttu-id="4bf0c-219">:::no-loc(Razor)::: SDK 定义两个主要目标：</span><span class="sxs-lookup"><span data-stu-id="4bf0c-219">The :::no-loc(Razor)::: SDK defines two primary targets:</span></span>

* <span data-ttu-id="4bf0c-220">`:::no-loc(Razor):::Generate`：代码基于 `:::no-loc(Razor):::Generate` 项元素生成 .cs 文件。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-220">`:::no-loc(Razor):::Generate`: Code generates *.cs* files from `:::no-loc(Razor):::Generate` item elements.</span></span> <span data-ttu-id="4bf0c-221">使用 `:::no-loc(Razor):::GenerateDependsOn` 属性指定可在此目标之前或之后运行的其他目标。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-221">Use the `:::no-loc(Razor):::GenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="4bf0c-222">`:::no-loc(Razor):::Compile`：将生成的 .cs 文件编译到 :::no-loc(Razor)::: 程序集中。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-222">`:::no-loc(Razor):::Compile`: Compiles generated *.cs* files in to a :::no-loc(Razor)::: assembly.</span></span> <span data-ttu-id="4bf0c-223">使用 `:::no-loc(Razor):::CompileDependsOn` 指定可在此目标之前或之后运行的其他目标。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-223">Use the `:::no-loc(Razor):::CompileDependsOn` to specify additional targets that can run before or after this target.</span></span>
* <span data-ttu-id="4bf0c-224">`:::no-loc(Razor):::ComponentGenerate`：代码为 `:::no-loc(Razor):::Component` 项元素生成 .cs 文件。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-224">`:::no-loc(Razor):::ComponentGenerate`: Code generates *.cs* files for `:::no-loc(Razor):::Component` item elements.</span></span> <span data-ttu-id="4bf0c-225">使用 `:::no-loc(Razor):::ComponentGenerateDependsOn` 属性指定可在此目标之前或之后运行的其他目标。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-225">Use the `:::no-loc(Razor):::ComponentGenerateDependsOn` property to specify additional targets that can run before or after this target.</span></span>

### <a name="runtime-compilation-of-no-locrazor-views"></a><span data-ttu-id="4bf0c-226">:::no-loc(Razor)::: 视图的运行时编译</span><span class="sxs-lookup"><span data-stu-id="4bf0c-226">Runtime compilation of :::no-loc(Razor)::: views</span></span>

* <span data-ttu-id="4bf0c-227">默认情况下，:::no-loc(Razor)::: SDK 不发布执行运行时编译所需的引用程序集。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-227">By default, the :::no-loc(Razor)::: SDK doesn't publish reference assemblies that are required to perform runtime compilation.</span></span> <span data-ttu-id="4bf0c-228">当应用程序模型依赖于运行时编译时，这会导致编译失败&mdash;例如，应用在发布后使用嵌入视图或更改视图。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-228">This results in compilation failures when the application model relies on runtime compilation&mdash;for example, the app uses embedded views or changes views after the app is published.</span></span> <span data-ttu-id="4bf0c-229">将 `CopyRefAssembliesToPublishDirectory` 设置为 `true`，以继续发布引用程序集。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-229">Set `CopyRefAssembliesToPublishDirectory` to `true` to continue publishing reference assemblies.</span></span>

* <span data-ttu-id="4bf0c-230">对于 Web 应用，请确保应用面向 `Microsoft.NET.Sdk.Web` SDK。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-230">For a web app, ensure your app is targeting the `Microsoft.NET.Sdk.Web` SDK.</span></span>

## <a name="no-locrazor-language-version"></a><span data-ttu-id="4bf0c-231">:::no-loc(Razor)::: 语言版本</span><span class="sxs-lookup"><span data-stu-id="4bf0c-231">:::no-loc(Razor)::: language version</span></span>

<span data-ttu-id="4bf0c-232">面向 `Microsoft.NET.Sdk.Web` SDK 时，:::no-loc(Razor)::: 语言版本是从应用的目标框架版本推断出来的。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-232">When targeting the `Microsoft.NET.Sdk.Web` SDK, the :::no-loc(Razor)::: language version is inferred from the app's target framework version.</span></span> <span data-ttu-id="4bf0c-233">对于面向 `Microsoft.NET.Sdk.:::no-loc(Razor):::` SDK 的项目，或应用需要不同于推断值的 :::no-loc(Razor)::: 语言版本的极少数情况下，可在应用的项目文件中设置 `<:::no-loc(Razor):::LangVersion>` 属性来配置版本：</span><span class="sxs-lookup"><span data-stu-id="4bf0c-233">For projects targeting the `Microsoft.NET.Sdk.:::no-loc(Razor):::` SDK or in the rare case that the app requires a different :::no-loc(Razor)::: language version than the inferred value, a version can be configured by setting the `<:::no-loc(Razor):::LangVersion>` property in the app's project file:</span></span>

```xml
<PropertyGroup>
  <:::no-loc(Razor):::LangVersion>{VERSION}</:::no-loc(Razor):::LangVersion>
</PropertyGroup>
```

<span data-ttu-id="4bf0c-234">:::no-loc(Razor)::: 的语言版本与其面向的运行时版本紧密集成。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-234">:::no-loc(Razor):::'s language version is tightly integrated with the version of the runtime that it was built for.</span></span> <span data-ttu-id="4bf0c-235">不支持面向不专为运行时而设计的语言版本，这可能会产生生成错误。</span><span class="sxs-lookup"><span data-stu-id="4bf0c-235">Targeting a language version that isn't designed for the runtime is unsupported and likely produces build errors.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="4bf0c-236">其他资源</span><span class="sxs-lookup"><span data-stu-id="4bf0c-236">Additional resources</span></span>

* [<span data-ttu-id="4bf0c-237">.NET Core 的 csproj 格式的新增内容</span><span class="sxs-lookup"><span data-stu-id="4bf0c-237">Additions to the csproj format for .NET Core</span></span>](/dotnet/core/tools/csproj)
* [<span data-ttu-id="4bf0c-238">常用的 MSBuild 项目项</span><span class="sxs-lookup"><span data-stu-id="4bf0c-238">Common MSBuild project items</span></span>](/visualstudio/msbuild/common-msbuild-project-items)
