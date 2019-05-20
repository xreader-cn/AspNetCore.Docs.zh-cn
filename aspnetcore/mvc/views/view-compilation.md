---
title: ASP.NET Core 中的 Razor 文件编译
author: rick-anderson
description: 了解 Razor 文件编译在 ASP.NET Core 应用中的发生方式。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: df25c9ad03ff52c43aac1a761462772547b3b954
ms.sourcegitcommit: 6afe57fb8d9055f88fedb92b16470398c4b9b24a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65610145"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="a953b-103">ASP.NET Core 中的 Razor 文件编译</span><span class="sxs-lookup"><span data-stu-id="a953b-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="a953b-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a953b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="a953b-105">调用相关的 MVC 视图时，Razor 文件在运行时进行编译。</span><span class="sxs-lookup"><span data-stu-id="a953b-105">A Razor file is compiled at runtime, when the associated MVC view is invoked.</span></span> <span data-ttu-id="a953b-106">不支持在生成时发布 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="a953b-106">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="a953b-107">可以使用预编译工具，在发布时选择编译 Razor 文件并将其与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="a953b-107">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="a953b-108">调用相关的 Razor 页和 MVC 视图时，Razor 文件在运行时进行编译。</span><span class="sxs-lookup"><span data-stu-id="a953b-108">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="a953b-109">不支持在生成时发布 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="a953b-109">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="a953b-110">可以使用预编译工具，在发布时选择编译 Razor 文件并将其与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="a953b-110">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="a953b-111">调用相关的 Razor 页和 MVC 视图时，Razor 文件在运行时进行编译。</span><span class="sxs-lookup"><span data-stu-id="a953b-111">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="a953b-112">在生成时和发布时使用 [Razor SDK](xref:razor-pages/sdk) 编译 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="a953b-112">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a953b-113">在生成时和发布时使用 [Razor SDK](xref:razor-pages/sdk) 编译 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="a953b-113">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="a953b-114">通过配置应用程序，可以选择启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="a953b-114">Runtime compilation may be optionally enabled by configuring your application.</span></span>

::: moniker-end

## <a name="razor-compilation"></a><span data-ttu-id="a953b-115">Razor 编译</span><span class="sxs-lookup"><span data-stu-id="a953b-115">Razor compilation</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="a953b-116">Razor SDK 默认启用 Razor 文件的生成时和发布时编译。</span><span class="sxs-lookup"><span data-stu-id="a953b-116">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="a953b-117">启用后，运行时编译将补充生成时编译，允许更新 Razor 文件（如果对其进行编辑）。</span><span class="sxs-lookup"><span data-stu-id="a953b-117">When enabled, runtime compilation, will complement build time compilation allowing Razor files to be updated if they are editied.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="a953b-118">Razor SDK 默认启用 Razor 文件的生成时和发布时编译。</span><span class="sxs-lookup"><span data-stu-id="a953b-118">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="a953b-119">Razor 文件更新后，支持在生成时编辑这些文件。</span><span class="sxs-lookup"><span data-stu-id="a953b-119">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="a953b-120">默认情况下，只有编译 Razor 文件所需的编译的 Views.dll（而非 .cshtml）文件或引用程序集随应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="a953b-120">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a953b-121">已弃用预编译工具，并且将在 ASP.NET Core 3.0 中删除该工具。</span><span class="sxs-lookup"><span data-stu-id="a953b-121">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="a953b-122">建议迁移到 [Razor Sdk](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="a953b-122">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="a953b-123">仅当项目文件中未设置特定于预编译的属性时，Razor SDK 才有效。</span><span class="sxs-lookup"><span data-stu-id="a953b-123">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="a953b-124">例如，通过将 .csproj 文件的 `MvcRazorCompileOnPublish` 属性设置为 `true` 来禁用 Razor SDK。</span><span class="sxs-lookup"><span data-stu-id="a953b-124">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="a953b-125">如果项目面向 .NET Framework，请安装 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="a953b-125">If your project targets .NET Framework, install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package:</span></span>

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

<span data-ttu-id="a953b-126">如果项目面向 .NET Core，则无需进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="a953b-126">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="a953b-127">默认情况下，ASP.NET Core 2.x 项目模板将 `MvcRazorCompileOnPublish` 属性隐式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="a953b-127">The ASP.NET Core 2.x project templates implicitly set the `MvcRazorCompileOnPublish` property to `true` by default.</span></span> <span data-ttu-id="a953b-128">因此，可以从 .csproj 文件中安全地删除此元素。</span><span class="sxs-lookup"><span data-stu-id="a953b-128">Consequently, this element can be safely removed from the *.csproj* file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a953b-129">已弃用预编译工具，并且将在 ASP.NET Core 3.0 中删除该工具。</span><span class="sxs-lookup"><span data-stu-id="a953b-129">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="a953b-130">建议迁移到 [Razor Sdk](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="a953b-130">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="a953b-131">在 ASP.NET Core 2.0 中执行[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时，无法使用 Razor 文件预编译。</span><span class="sxs-lookup"><span data-stu-id="a953b-131">Razor file precompilation is unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="a953b-132">将 `MvcRazorCompileOnPublish` 属性设置为 `true`，然后安装 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a953b-132">Set the `MvcRazorCompileOnPublish` property to `true`, and install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package.</span></span> <span data-ttu-id="a953b-133">以下 .csproj 示例突出显示了这些设置：</span><span class="sxs-lookup"><span data-stu-id="a953b-133">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="a953b-134">使用 [.NET Core CLI 发布命令](/dotnet/core/tools/dotnet-publish)，让应用做好[框架依赖型部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)的准备。</span><span class="sxs-lookup"><span data-stu-id="a953b-134">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) with the [.NET Core CLI publish command](/dotnet/core/tools/dotnet-publish).</span></span> <span data-ttu-id="a953b-135">例如，在项目根目录中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="a953b-135">For example, execute the following command at the project root:</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="a953b-136">预编译成功后，将生成包含已编译 Razor 文件的 \<project_name>.PrecompiledViews.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="a953b-136">A *\<project_name>.PrecompiledViews.dll* file, containing the compiled Razor files, is produced when precompilation succeeds.</span></span> <span data-ttu-id="a953b-137">例如，以下屏幕截图描述了 WebApplication1.PrecompiledViews.dll 中 Index.cshtml 的内容：</span><span class="sxs-lookup"><span data-stu-id="a953b-137">For example, the screenshot below depicts the contents of *Index.cshtml* within *WebApplication1.PrecompiledViews.dll*:</span></span>

![DLL 中的 Razor 视图](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="runtime-compilation"></a><span data-ttu-id="a953b-139">运行时编译</span><span class="sxs-lookup"><span data-stu-id="a953b-139">Runtime compilation</span></span>

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="a953b-140">通过 Razor 文件的运行时编译补充生成时编译。</span><span class="sxs-lookup"><span data-stu-id="a953b-140">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="a953b-141">当 .cshtml 文件的内容发生更改时，ASP.NET Core MVC 将重新编译 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="a953b-141">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="a953b-142">通过 Razor 文件的运行时编译补充生成时编译。</span><span class="sxs-lookup"><span data-stu-id="a953b-142">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="a953b-143"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> 获取或设置一个值，该值确定当磁盘上的文件发生更改时是否重新编译和更新 Razor 文件（Razor 视图和 Razor Pages）。</span><span class="sxs-lookup"><span data-stu-id="a953b-143">The <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> gets or sets a value that determines if Razor files (Razor views and Razor Pages) are recompiled and updated if files change on disk.</span></span>

<span data-ttu-id="a953b-144">对于以下项，默认值为 `true`：</span><span class="sxs-lookup"><span data-stu-id="a953b-144">The default value is `true` for:</span></span>

* <span data-ttu-id="a953b-145">将应用的兼容性版本设置为 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> 或更早版本</span><span class="sxs-lookup"><span data-stu-id="a953b-145">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> or earlier</span></span>
* <span data-ttu-id="a953b-146">如果应用的兼容性版本设置为 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> 或更高版本，并且应用位于开发环境 <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*> 中。</span><span class="sxs-lookup"><span data-stu-id="a953b-146">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> or later and the app is in the Development environment <xref:Microsoft.AspNetCore.Hosting.HostingEnvironmentExtensions.IsDevelopment*>.</span></span> <span data-ttu-id="a953b-147">换句话说，除非明确设置 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange>，否则 Razor 文件不会在非开发环境中重新编译。</span><span class="sxs-lookup"><span data-stu-id="a953b-147">In other words, Razor files would not recompile in non-Development environment unless <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> is explicitly set.</span></span>

<span data-ttu-id="a953b-148">有关设置应用的兼容性版本的指导和示例，请参阅 <xref:mvc/compatibility-version>。</span><span class="sxs-lookup"><span data-stu-id="a953b-148">For guidance and examples of setting the app's compatibility version, see <xref:mvc/compatibility-version>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="a953b-149">使用 `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` 包启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="a953b-149">Runtime compilation is enabled using the `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` package.</span></span> <span data-ttu-id="a953b-150">要启用运行时编译，应用必须：</span><span class="sxs-lookup"><span data-stu-id="a953b-150">To enable runtime compilation, apps must:</span></span>

* <span data-ttu-id="a953b-151">安装 [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="a953b-151">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
* <span data-ttu-id="a953b-152">更新应用程序的 `ConfigureServices` 以包含对 `AddMvcRazorRuntimeCompilation` 的调用：</span><span class="sxs-lookup"><span data-stu-id="a953b-152">Update the application's `ConfigureServices` to include a call to `AddMvcRazorRuntimeCompilation`:</span></span>

  ```csharp
  services
      .AddMvc()
      .AddRazorRuntimeCompilation()
  ```

<span data-ttu-id="a953b-153">为了在部署时使运行时编译正常工作，应用必须修改其项目文件，以将 `PreserveCompilationReferences` 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="a953b-153">For runtime compilation to work when deployed, apps must modify their project files to set the `PreserveCompilationReferences` to `true`:</span></span>

[!code-xml[](view-compilation/sample/RuntimeCompilation.csproj?highlight=4)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="a953b-154">其他资源</span><span class="sxs-lookup"><span data-stu-id="a953b-154">Additional resources</span></span>

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
