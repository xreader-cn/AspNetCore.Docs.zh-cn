---
title: ASP.NET Core 中的 Razor 文件编译和预编译
author: rick-anderson
description: 了解预编译 Razor 文件的好处以及如何在 ASP.NET Core 应用中完成 Razor 文件预编译。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: 2720708f8e58fdc55b82bfb56665005170e79934
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889751"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="0e192-103">ASP.NET Core 中的 Razor 文件编译</span><span class="sxs-lookup"><span data-stu-id="0e192-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="0e192-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0e192-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="0e192-105">调用相关的 MVC 视图时，Razor 文件在运行时进行编译。</span><span class="sxs-lookup"><span data-stu-id="0e192-105">A Razor file is compiled at runtime, when the associated MVC view is invoked.</span></span> <span data-ttu-id="0e192-106">不支持在生成时发布 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="0e192-106">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="0e192-107">可以使用预编译工具，在发布时选择编译 Razor 文件并将其与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="0e192-107">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0e192-108">调用相关的 Razor 页和 MVC 视图时，Razor 文件在运行时进行编译。</span><span class="sxs-lookup"><span data-stu-id="0e192-108">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="0e192-109">不支持在生成时发布 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="0e192-109">Build-time Razor file publishing is unsupported.</span></span> <span data-ttu-id="0e192-110">可以使用预编译工具，在发布时选择编译 Razor 文件并将其与应用一起部署。</span><span class="sxs-lookup"><span data-stu-id="0e192-110">Razor files can optionally be compiled at publish time and deployed with the app&mdash;using the precompilation tool.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0e192-111">调用相关的 Razor 页和 MVC 视图时，Razor 文件在运行时进行编译。</span><span class="sxs-lookup"><span data-stu-id="0e192-111">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="0e192-112">在生成时和发布时使用 [Razor SDK](xref:razor-pages/sdk) 编译 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="0e192-112">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

::: moniker-end

## <a name="precompilation-considerations"></a><span data-ttu-id="0e192-113">预编译注意事项</span><span class="sxs-lookup"><span data-stu-id="0e192-113">Precompilation considerations</span></span>

<span data-ttu-id="0e192-114">以下是预编译 Razor 文件的意外后果：</span><span class="sxs-lookup"><span data-stu-id="0e192-114">The following are side effects of precompiling Razor files:</span></span>

* <span data-ttu-id="0e192-115">发布捆绑包更小</span><span class="sxs-lookup"><span data-stu-id="0e192-115">A smaller published bundle</span></span>
* <span data-ttu-id="0e192-116">启动速度更快</span><span class="sxs-lookup"><span data-stu-id="0e192-116">A faster startup time</span></span>
* <span data-ttu-id="0e192-117">无法编辑 Razor 文件&mdash;关联内容不会出现在发布捆绑包中。</span><span class="sxs-lookup"><span data-stu-id="0e192-117">You can't edit Razor files&mdash;the associated content is absent from the published bundle.</span></span>

## <a name="deploy-precompiled-files"></a><span data-ttu-id="0e192-118">部署预编译文件</span><span class="sxs-lookup"><span data-stu-id="0e192-118">Deploy precompiled files</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0e192-119">Razor SDK 默认启用 Razor 文件的生成时和发布时编译。</span><span class="sxs-lookup"><span data-stu-id="0e192-119">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="0e192-120">Razor 文件更新后，支持在生成时编辑这些文件。</span><span class="sxs-lookup"><span data-stu-id="0e192-120">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="0e192-121">默认情况下，仅通过应用部署编译的 Views.dll 而不部署 cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="0e192-121">By default, only the compiled *Views.dll* and no *.cshtml* files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0e192-122">ASP.NET Core 3.0 中将删除预编译工具。</span><span class="sxs-lookup"><span data-stu-id="0e192-122">The precompilation tool will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="0e192-123">建议迁移到 [Razor Sdk](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="0e192-123">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="0e192-124">仅当项目文件中未设置特定于预编译的属性时，Razor SDK 才有效。</span><span class="sxs-lookup"><span data-stu-id="0e192-124">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="0e192-125">例如，通过将 .csproj 文件的 `MvcRazorCompileOnPublish` 属性设置为 `true` 来禁用 Razor SDK。</span><span class="sxs-lookup"><span data-stu-id="0e192-125">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0e192-126">如果项目面向 .NET Framework，请安装 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="0e192-126">If your project targets .NET Framework, install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package:</span></span>

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

<span data-ttu-id="0e192-127">如果项目面向 .NET Core，则无需进行任何更改。</span><span class="sxs-lookup"><span data-stu-id="0e192-127">If your project targets .NET Core, no changes are necessary.</span></span>

<span data-ttu-id="0e192-128">默认情况下，ASP.NET Core 2.x 项目模板将 `MvcRazorCompileOnPublish` 属性隐式设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="0e192-128">The ASP.NET Core 2.x project templates implicitly set the `MvcRazorCompileOnPublish` property to `true` by default.</span></span> <span data-ttu-id="0e192-129">因此，可以从 .csproj 文件中安全地删除此元素。</span><span class="sxs-lookup"><span data-stu-id="0e192-129">Consequently, this element can be safely removed from the *.csproj* file.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="0e192-130">ASP.NET Core 3.0 中将删除预编译工具。</span><span class="sxs-lookup"><span data-stu-id="0e192-130">The precompilation tool will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="0e192-131">建议迁移到 [Razor Sdk](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="0e192-131">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="0e192-132">在 ASP.NET Core 2.0 中执行[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时，无法使用 Razor 文件预编译。</span><span class="sxs-lookup"><span data-stu-id="0e192-132">Razor file precompilation is unavailable when performing a [self-contained deployment (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) in ASP.NET Core 2.0.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-1.1"

<span data-ttu-id="0e192-133">将 `MvcRazorCompileOnPublish` 属性设置为 `true`，然后安装 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="0e192-133">Set the `MvcRazorCompileOnPublish` property to `true`, and install the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet package.</span></span> <span data-ttu-id="0e192-134">以下 .csproj 示例突出显示了这些设置：</span><span class="sxs-lookup"><span data-stu-id="0e192-134">The following *.csproj* sample highlights these settings:</span></span>

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="0e192-135">使用 [.NET Core CLI 发布命令](/dotnet/core/tools/dotnet-publish)，让应用做好[框架依赖型部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)的准备。</span><span class="sxs-lookup"><span data-stu-id="0e192-135">Prepare the app for a [framework-dependent deployment](/dotnet/core/deploying/#framework-dependent-deployments-fdd) with the [.NET Core CLI publish command](/dotnet/core/tools/dotnet-publish).</span></span> <span data-ttu-id="0e192-136">例如，在项目根目录中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0e192-136">For example, execute the following command at the project root:</span></span>

```console
dotnet publish -c Release
```

<span data-ttu-id="0e192-137">预编译成功后，将生成包含已编译 Razor 文件的 <project_name>.PrecompiledViews.dll 文件。</span><span class="sxs-lookup"><span data-stu-id="0e192-137">A *<project_name>.PrecompiledViews.dll* file, containing the compiled Razor files, is produced when precompilation succeeds.</span></span> <span data-ttu-id="0e192-138">例如，以下屏幕截图描述了 WebApplication1.PrecompiledViews.dll 中 Index.cshtml 的内容：</span><span class="sxs-lookup"><span data-stu-id="0e192-138">For example, the screenshot below depicts the contents of *Index.cshtml* within *WebApplication1.PrecompiledViews.dll*:</span></span>

![DLL 中的 Razor 视图](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="recompile-razor-files-on-change"></a><span data-ttu-id="0e192-140">在更改时重新编译 Razor 文件</span><span class="sxs-lookup"><span data-stu-id="0e192-140">Recompile Razor files on change</span></span>

<span data-ttu-id="0e192-141"><xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> `AllowRecompilingViewsOnFileChange` 获取或设置一个值，该值确定当磁盘上的文件发生更改时是否重新编译和更新 Razor 文件（Razor 视图和 Razor Pages）。</span><span class="sxs-lookup"><span data-stu-id="0e192-141">The <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> `AllowRecompilingViewsOnFileChange` gets or sets a value that determines if Razor files (Razor views and Razor Pages) are recompiled and updated if files change on disk.</span></span>

<span data-ttu-id="0e192-142">当设置为 `true` 时，[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 监视对配置的 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 实例中的 Razor 文件所做的更改。</span><span class="sxs-lookup"><span data-stu-id="0e192-142">When set to `true`, [IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) watches for changes to Razor files in configured <xref:Microsoft.Extensions.FileProviders.IFileProvider> instances.</span></span>

<span data-ttu-id="0e192-143">对于以下项，默认值为 `true`：</span><span class="sxs-lookup"><span data-stu-id="0e192-143">The default value is `true` for:</span></span>

* <span data-ttu-id="0e192-144">ASP.NET Core 2.1 或更早版本的应用。</span><span class="sxs-lookup"><span data-stu-id="0e192-144">ASP.NET Core 2.1 or earlier apps.</span></span>
* <span data-ttu-id="0e192-145">开发环境中的 ASP.NET Core 2.2 或更高版本的应用。</span><span class="sxs-lookup"><span data-stu-id="0e192-145">ASP.NET Core 2.2 or later apps in the Development environment.</span></span>

<span data-ttu-id="0e192-146">`AllowRecompilingViewsOnFileChange` 与兼容性开关相关联，并可根据为应用配置的兼容性版本来提供不同的行为。</span><span class="sxs-lookup"><span data-stu-id="0e192-146">`AllowRecompilingViewsOnFileChange` is associated with a compatibility switch and can provide different behavior depending on the configured compatibility version for the app.</span></span> <span data-ttu-id="0e192-147">通过设置 `AllowRecompilingViewsOnFileChange` 配置应用优先于由应用的兼容性版本表示的值。</span><span class="sxs-lookup"><span data-stu-id="0e192-147">Configuring the app by setting `AllowRecompilingViewsOnFileChange` takes precedence over the value implied by the app's compatibility version.</span></span>

<span data-ttu-id="0e192-148">如果将应用的兼容性版本设置为 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> 或更早版本，则将 `AllowRecompilingViewsOnFileChange` 设置为 `true`，除非对其进行显式配置。</span><span class="sxs-lookup"><span data-stu-id="0e192-148">If the app's compatibility version is set to <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> or earlier, `AllowRecompilingViewsOnFileChange` is set to `true` unless explicitly configured.</span></span>

<span data-ttu-id="0e192-149">如果将应用的兼容性版本设置为 `CompatibilityVersion.Version_2_2` 或更高版本，则将 `AllowRecompilingViewsOnFileChange` 设置为 `false`，除非环境是开发环境或显式配置该值。</span><span class="sxs-lookup"><span data-stu-id="0e192-149">If the app's compatibility version is set to `CompatibilityVersion.Version_2_2` or later, `AllowRecompilingViewsOnFileChange` is set to `false` unless the environment is Development or the value is explicitly configured.</span></span>

<span data-ttu-id="0e192-150">有关设置应用的兼容性版本的指导和示例，请参阅 <xref:mvc/compatibility-version>。</span><span class="sxs-lookup"><span data-stu-id="0e192-150">For guidance and examples of setting the app's compatibility version, see <xref:mvc/compatibility-version>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0e192-151">其他资源</span><span class="sxs-lookup"><span data-stu-id="0e192-151">Additional resources</span></span>

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
