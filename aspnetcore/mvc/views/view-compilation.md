---
title: ASP.NET Core 中的 Razor 文件编译
author: rick-anderson
description: 了解 Razor 文件编译在 ASP.NET Core 应用中的发生方式。
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994610"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="44449-103">ASP.NET Core 中的 Razor 文件编译</span><span class="sxs-lookup"><span data-stu-id="44449-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="44449-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="44449-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="44449-105">在生成和发布时均使用 [Razor SDK](xref:razor-pages/sdk) 编译扩展名为 .cshtml 的 Razor 文件\*\*。</span><span class="sxs-lookup"><span data-stu-id="44449-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="44449-106">通过配置应用程序，可以选择启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="44449-106">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="44449-107">Razor 编译</span><span class="sxs-lookup"><span data-stu-id="44449-107">Razor compilation</span></span>

<span data-ttu-id="44449-108">Razor SDK 默认启用 Razor 文件的生成时和发布时编译。</span><span class="sxs-lookup"><span data-stu-id="44449-108">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="44449-109">启用后，运行时编译将补充生成时编译，允许更新 Razor 文件（如果对其进行编辑）。</span><span class="sxs-lookup"><span data-stu-id="44449-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they are edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="44449-110">运行时编译</span><span class="sxs-lookup"><span data-stu-id="44449-110">Runtime compilation</span></span>

<span data-ttu-id="44449-111">为所有环境和配置模式启用运行时编译：</span><span class="sxs-lookup"><span data-stu-id="44449-111">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="44449-112">安装[微软.AspNetCore.Mvc.Razor.运行时编译](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)NuGet包。</span><span class="sxs-lookup"><span data-stu-id="44449-112">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="44449-113">更新项目的 `Startup.ConfigureServices` 方法以包含对 `AddRazorRuntimeCompilation` 的调用。</span><span class="sxs-lookup"><span data-stu-id="44449-113">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="44449-114">例如：</span><span class="sxs-lookup"><span data-stu-id="44449-114">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="44449-115">有条件地启用运行时编译</span><span class="sxs-lookup"><span data-stu-id="44449-115">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="44449-116">启用运行时编译时可使其仅用于本地开发。</span><span class="sxs-lookup"><span data-stu-id="44449-116">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="44449-117">以这种方式有条件地启用可确保已发布的输出：</span><span class="sxs-lookup"><span data-stu-id="44449-117">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="44449-118">使用编译视图。</span><span class="sxs-lookup"><span data-stu-id="44449-118">Uses compiled views.</span></span>
* <span data-ttu-id="44449-119">较小。</span><span class="sxs-lookup"><span data-stu-id="44449-119">Is smaller in size.</span></span>
* <span data-ttu-id="44449-120">不会在生产环境中启用文件观察程序。</span><span class="sxs-lookup"><span data-stu-id="44449-120">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="44449-121">基于环境和配置模式启用运行时编译：</span><span class="sxs-lookup"><span data-stu-id="44449-121">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="44449-122">根据活动的 `Configuration` 值，有条件地引用 [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) 包：</span><span class="sxs-lookup"><span data-stu-id="44449-122">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="44449-123">更新项目的 `Startup.ConfigureServices` 方法以包含对 `AddRazorRuntimeCompilation` 的调用。</span><span class="sxs-lookup"><span data-stu-id="44449-123">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="44449-124">有条件地执行 `AddRazorRuntimeCompilation`，使其仅当 `ASPNETCORE_ENVIRONMENT` 变量设置为 `Development`时在调试模式下运行：</span><span class="sxs-lookup"><span data-stu-id="44449-124">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

## <a name="additional-resources"></a><span data-ttu-id="44449-125">其他资源</span><span class="sxs-lookup"><span data-stu-id="44449-125">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="44449-126">调用相关的 Razor 页和 MVC 视图时，Razor 文件在运行时进行编译。</span><span class="sxs-lookup"><span data-stu-id="44449-126">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="44449-127">在生成时和发布时使用 [Razor SDK](xref:razor-pages/sdk) 编译 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="44449-127">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="44449-128">Razor 编译</span><span class="sxs-lookup"><span data-stu-id="44449-128">Razor compilation</span></span>

<span data-ttu-id="44449-129">Razor SDK 默认启用 Razor 文件的生成时和发布时编译。</span><span class="sxs-lookup"><span data-stu-id="44449-129">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="44449-130">Razor 文件更新后，支持在生成时编辑这些文件。</span><span class="sxs-lookup"><span data-stu-id="44449-130">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="44449-131">默认情况下，只有编译 Razor 文件所需的编译的 Views.dll（而非 .cshtml）文件或引用程序集随应用一起部署\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="44449-131">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="44449-132">已弃用预编译工具，并且将在 ASP.NET Core 3.0 中删除该工具。</span><span class="sxs-lookup"><span data-stu-id="44449-132">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="44449-133">建议迁移到 [Razor Sdk](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="44449-133">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="44449-134">仅当项目文件中未设置特定于预编译的属性时，Razor SDK 才有效。</span><span class="sxs-lookup"><span data-stu-id="44449-134">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="44449-135">例如，通过将 .csproj 文件的 `MvcRazorCompileOnPublish` 属性设置为 `true` 来禁用 Razor SDK\*\*。</span><span class="sxs-lookup"><span data-stu-id="44449-135">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="44449-136">运行时编译</span><span class="sxs-lookup"><span data-stu-id="44449-136">Runtime compilation</span></span>

<span data-ttu-id="44449-137">通过 Razor 文件的运行时编译补充生成时编译。</span><span class="sxs-lookup"><span data-stu-id="44449-137">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="44449-138">当 .cshtml 文件的内容发生更改时，ASP.NET Core MVC 将重新编译 Razor 文件\*\*。</span><span class="sxs-lookup"><span data-stu-id="44449-138">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="44449-139">其他资源</span><span class="sxs-lookup"><span data-stu-id="44449-139">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end