---
title: 使用类库中的 ASP.NET Core API
author: scottaddie
description: 了解如何使用类库中的 ASP.NET Core API。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/16/2019
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
uid: fundamentals/target-aspnetcore
ms.openlocfilehash: c012658a6f48247af60c8bfd56a7d987f6aa8a68
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "93061504"
---
# <a name="use-aspnet-core-apis-in-a-class-library"></a><span data-ttu-id="2c7ad-103">使用类库中的 ASP.NET Core API</span><span class="sxs-lookup"><span data-stu-id="2c7ad-103">Use ASP.NET Core APIs in a class library</span></span>

<span data-ttu-id="2c7ad-104">作者：[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="2c7ad-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="2c7ad-105">此文档提供了关于如何使用类库中的 ASP.NET Core API 的指南。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-105">This document provides guidance for using ASP.NET Core APIs in a class library.</span></span> <span data-ttu-id="2c7ad-106">若要查看所有其他的库指南，请参阅[开放源代码库指南](/dotnet/standard/library-guidance/)。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-106">For all other library guidance, see [Open-source library guidance](/dotnet/standard/library-guidance/).</span></span>

## <a name="determine-which-aspnet-core-versions-to-support"></a><span data-ttu-id="2c7ad-107">确定要支持哪些 ASP.NET Core 版本</span><span class="sxs-lookup"><span data-stu-id="2c7ad-107">Determine which ASP.NET Core versions to support</span></span>

<span data-ttu-id="2c7ad-108">ASP.NET Core 遵从 [.NET Core 支持策略](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-108">ASP.NET Core adheres to the [.NET Core support policy](https://dotnet.microsoft.com/platform/support/policy/dotnet-core).</span></span> <span data-ttu-id="2c7ad-109">确定库要支持哪些 ASP.NET Core 版本时，请参阅支持策略。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-109">Consult the support policy when determining which ASP.NET Core versions to support in a library.</span></span> <span data-ttu-id="2c7ad-110">库应符合以下条件：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-110">A library should:</span></span>

* <span data-ttu-id="2c7ad-111">努力支持列为“长期支持”(LTS) 类别的所有 ASP.NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-111">Make an effort to support all ASP.NET Core versions classified as *Long-Term Support* (LTS).</span></span>
* <span data-ttu-id="2c7ad-112">无需支持列为“生命周期结束”(EOL) 类别的 ASP.NET Core 版本。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-112">Not feel obligated to support ASP.NET Core versions classified as *End of Life* (EOL).</span></span>

<span data-ttu-id="2c7ad-113">由于 ASP.NET Core 预览版已推出，因此已在 [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub 存储库中发布中断性变更。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-113">As preview releases of ASP.NET Core are made available, breaking changes are posted in the [aspnet/Announcements](https://github.com/aspnet/Announcements/issues) GitHub repository.</span></span> <span data-ttu-id="2c7ad-114">开发框架功能时，可执行库兼容性测试。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-114">Compatibility testing of libraries can be conducted as framework features are being developed.</span></span>

## <a name="use-the-aspnet-core-shared-framework"></a><span data-ttu-id="2c7ad-115">使用 ASP.NET Core 共享框架</span><span class="sxs-lookup"><span data-stu-id="2c7ad-115">Use the ASP.NET Core shared framework</span></span>

<span data-ttu-id="2c7ad-116">随着 .NET Core 3.0 发布，许多 ASP.NET Core 程序集不再作为包发布到 NuGet。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-116">With the release of .NET Core 3.0, many ASP.NET Core assemblies are no longer published to NuGet as packages.</span></span> <span data-ttu-id="2c7ad-117">而是改为将这些程序集包含在通过 .NET Core SDK 和运行时安装程序安装的 `Microsoft.AspNetCore.App` 共享框架中。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-117">Instead, the assemblies are included in the `Microsoft.AspNetCore.App` shared framework, which is installed with the .NET Core SDK and runtime installers.</span></span> <span data-ttu-id="2c7ad-118">若要查看不再发布的包列表，请参阅[删除过时的包引用](xref:migration/22-to-30#remove-obsolete-package-references)。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-118">For a list of packages no longer being published, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="2c7ad-119">自 .NET Core 3.0 起，使用 `Microsoft.NET.Sdk.Web` MSBuild SDK 的项目隐式引用此共享框架。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-119">As of .NET Core 3.0, projects using the `Microsoft.NET.Sdk.Web` MSBuild SDK implicitly reference the shared framework.</span></span> <span data-ttu-id="2c7ad-120">使用 `Microsoft.NET.Sdk` 或 `Microsoft.NET.Sdk.Razor` SDK 的项目必须引用 ASP.NET Core，才能使用共享框架中的 ASP.NET Core API。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-120">Projects using the `Microsoft.NET.Sdk` or `Microsoft.NET.Sdk.Razor` SDK must reference ASP.NET Core to use ASP.NET Core APIs in the shared framework.</span></span>

<span data-ttu-id="2c7ad-121">若要引用 ASP.NET Core，请将以下 `<FrameworkReference>` 元素添加到项目文件：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-121">To reference ASP.NET Core, add the following `<FrameworkReference>` element to your project file:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj?highlight=8)]

<span data-ttu-id="2c7ad-122">仅面向 .NET Core 3.x 的项目支持使用此方式引用 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-122">Referencing ASP.NET Core in this manner is only supported for projects targeting .NET Core 3.x.</span></span>

## <a name="include-no-locblazor-extensibility"></a><span data-ttu-id="2c7ad-123">包括 Blazor 扩展性</span><span class="sxs-lookup"><span data-stu-id="2c7ad-123">Include Blazor extensibility</span></span>

<span data-ttu-id="2c7ad-124">Blazor 支持 WebAssembly (WASM) 和服务器[托管模型](xref:blazor/hosting-models)。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-124">Blazor supports WebAssembly (WASM) and Server [hosting models](xref:blazor/hosting-models).</span></span> <span data-ttu-id="2c7ad-125">除非出于特定原因无法实现支持，否则 [Razor 组件](xref:blazor/components/index)库应同时支持这两种托管模型。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-125">Unless there's a specific reason not to, a [Razor components](xref:blazor/components/index) library should support both hosting models.</span></span> <span data-ttu-id="2c7ad-126">Razor 组件库必须使用 [Microsoft.NET.Sdk.RazorSDK](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-126">A Razor components library must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

### <a name="support-both-hosting-models"></a><span data-ttu-id="2c7ad-127">同时支持两种托管模型</span><span class="sxs-lookup"><span data-stu-id="2c7ad-127">Support both hosting models</span></span>

<span data-ttu-id="2c7ad-128">请针对自己的编辑器使用以下说明，以同时支持 [Blazor Server](xref:blazor/hosting-models#blazor-server) 和 [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) 项目的 Razor 组件消耗。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-128">To support Razor component consumption from both [Blazor Server](xref:blazor/hosting-models#blazor-server) and [Blazor WASM](xref:blazor/hosting-models#blazor-webassembly) projects, use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c7ad-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c7ad-129">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c7ad-130">使用 Razor 类库项目模板。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-130">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="2c7ad-131">应取消选中此模板的“支持页和视图”复选框。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-131">The template's **Support pages and views** checkbox should be deselected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c7ad-132">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c7ad-132">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2c7ad-133">在“集成终端”中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-133">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c7ad-134">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c7ad-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c7ad-135">使用 Razor 类库项目模板。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-135">Use the **Razor Class Library** project template.</span></span>

---

<span data-ttu-id="2c7ad-136">模板生成的项目执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-136">The project generated from the template does the following things:</span></span>

* <span data-ttu-id="2c7ad-137">面向 .NET Standard 2.0。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-137">Targets .NET Standard 2.0.</span></span>
* <span data-ttu-id="2c7ad-138">将 `RazorLangVersion` 属性设置为 `3.0`。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-138">Sets the `RazorLangVersion` property to `3.0`.</span></span> <span data-ttu-id="2c7ad-139">.NET Core 3.x 的默认值是 `3.0`。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-139">`3.0` is the default value for .NET Core 3.x.</span></span>
* <span data-ttu-id="2c7ad-140">添加以下包引用：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-140">Adds the following package references:</span></span>
  * [<span data-ttu-id="2c7ad-141">Microsoft.AspNetCore.Components</span><span class="sxs-lookup"><span data-stu-id="2c7ad-141">Microsoft.AspNetCore.Components</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)
  * [<span data-ttu-id="2c7ad-142">Microsoft.AspNetCore.Components.Web</span><span class="sxs-lookup"><span data-stu-id="2c7ad-142">Microsoft.AspNetCore.Components.Web</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.Web)

<span data-ttu-id="2c7ad-143">例如：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-143">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-components-library.csproj)]

### <a name="support-a-specific-hosting-model"></a><span data-ttu-id="2c7ad-144">支持特定托管模型</span><span class="sxs-lookup"><span data-stu-id="2c7ad-144">Support a specific hosting model</span></span>

<span data-ttu-id="2c7ad-145">支持单个 Blazor 托管模型并不常见。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-145">It's far less common to support a single Blazor hosting model.</span></span> <span data-ttu-id="2c7ad-146">例如，通过完成以下操作来仅支持 [Blazor Server](xref:blazor/hosting-models#blazor-server) 项目的 Razor 组件消耗：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-146">As an example, to support Razor component consumption from [Blazor Server](xref:blazor/hosting-models#blazor-server) projects only:</span></span>

* <span data-ttu-id="2c7ad-147">面向 .NET Core 3.x。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-147">Target .NET Core 3.x.</span></span>
* <span data-ttu-id="2c7ad-148">添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-148">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="2c7ad-149">例如：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-149">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-components-library.csproj)]

<span data-ttu-id="2c7ad-150">有关包含 Razor 组件的库的详细信息，请参阅 [ASP.NET Core Razor 组件类库](xref:blazor/components/class-libraries)。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-150">For more information on libraries containing Razor components, see [ASP.NET Core Razor components class libraries](xref:blazor/components/class-libraries).</span></span>

## <a name="include-mvc-extensibility"></a><span data-ttu-id="2c7ad-151">包括 MVC 扩展性</span><span class="sxs-lookup"><span data-stu-id="2c7ad-151">Include MVC extensibility</span></span>

<span data-ttu-id="2c7ad-152">此部分概述了针对包括以下内容的库的建议：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-152">This section outlines recommendations for libraries that include:</span></span>

* [<span data-ttu-id="2c7ad-153">Razor 视图或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="2c7ad-153">Razor views or Razor Pages</span></span>](#razor-views-or-razor-pages)
* [<span data-ttu-id="2c7ad-154">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="2c7ad-154">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="2c7ad-155">视图组件</span><span class="sxs-lookup"><span data-stu-id="2c7ad-155">View components</span></span>](#view-components)

<span data-ttu-id="2c7ad-156">此部分未探讨用于支持多个 MVC 版本的多目标。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-156">This section doesn't discuss multi-targeting to support multiple versions of MVC.</span></span> <span data-ttu-id="2c7ad-157">若要查看关于支持多个 ASP.NET Core 版本的指南，请参阅[支持多个 ASP.NET Core 版本](#support-multiple-aspnet-core-versions)。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-157">For guidance on supporting multiple ASP.NET Core versions, see [Support multiple ASP.NET Core versions](#support-multiple-aspnet-core-versions).</span></span>

### <a name="no-locrazor-views-or-no-locrazor-pages"></a><span data-ttu-id="2c7ad-158">Razor 视图或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="2c7ad-158">Razor views or Razor Pages</span></span>

<span data-ttu-id="2c7ad-159">包括 [Razor 视图](xref:mvc/views/overview)或 [Razor 页面](xref:razor-pages/index)的项目必须使用 [Microsoft.NET.Sdk.RazorSDK](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-159">A project that includes [Razor views](xref:mvc/views/overview) or [Razor Pages](xref:razor-pages/index) must use the [Microsoft.NET.Sdk.Razor SDK](xref:razor-pages/sdk).</span></span>

<span data-ttu-id="2c7ad-160">若项目面向 .NET Core 3.x，它必须满足以下要求：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-160">If the project targets .NET Core 3.x, it requires:</span></span>

* <span data-ttu-id="2c7ad-161">`AddRazorSupportForMvc` MSBuild 属性设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-161">An `AddRazorSupportForMvc` MSBuild property set to `true`.</span></span>
* <span data-ttu-id="2c7ad-162">具有针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-162">A `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="2c7ad-163">Razor 类库项目模板符合针对面向 .NET Core 3.x 的项目的上述要求。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-163">The **Razor Class Library** project template satisfies the preceding requirements for projects targeting .NET Core 3.x.</span></span> <span data-ttu-id="2c7ad-164">请针对自己的编辑器使用以下说明。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-164">Use the following instructions for your editor.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="2c7ad-165">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c7ad-165">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2c7ad-166">使用 Razor 类库项目模板。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-166">Use the **Razor Class Library** project template.</span></span> <span data-ttu-id="2c7ad-167">应选中此模板的“支持页和视图”复选框。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-167">The template's **Support pages and views** checkbox should be selected.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="2c7ad-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c7ad-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2c7ad-169">在“集成终端”中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-169">Run the following command in the integrated terminal:</span></span>

```dotnetcli
dotnet new razorclasslib -s
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="2c7ad-170">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c7ad-170">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c7ad-171">此时无任何项目模板支持。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-171">No project template support at this time.</span></span>

---

<span data-ttu-id="2c7ad-172">例如：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-172">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-razor-views-pages-library.csproj)]

<span data-ttu-id="2c7ad-173">若项目改为面向 .NET Standard，则需要 [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) 包引用。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-173">If the project targets .NET Standard instead, a [Microsoft.AspNetCore.Mvc](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc) package reference is required.</span></span> <span data-ttu-id="2c7ad-174">`Microsoft.AspNetCore.Mvc` 包移到了 ASP.NET Core 3.0 的共享框架中，因此不再发布。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-174">The `Microsoft.AspNetCore.Mvc` package moved into the shared framework in ASP.NET Core 3.0 and is therefore no longer published.</span></span> <span data-ttu-id="2c7ad-175">例如：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-175">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-razor-views-pages-library.csproj?highlight=8)]

### <a name="tag-helpers"></a><span data-ttu-id="2c7ad-176">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="2c7ad-176">Tag Helpers</span></span>

<span data-ttu-id="2c7ad-177">包含[标记帮助器](xref:mvc/views/tag-helpers/intro)的项目应使用 `Microsoft.NET.Sdk` SDK。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-177">A project that includes [Tag Helpers](xref:mvc/views/tag-helpers/intro) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="2c7ad-178">若面向 .NET Core 3.x，则添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-178">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="2c7ad-179">例如：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-179">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="2c7ad-180">若面向 .NET Standard（以支持 ASP.NET Core 3.x 之前的版本），则添加对 [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-180">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor).</span></span> <span data-ttu-id="2c7ad-181">`Microsoft.AspNetCore.Mvc.Razor` 包移到了共享框架，因此不再发布。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-181">The `Microsoft.AspNetCore.Mvc.Razor` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="2c7ad-182">例如：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-182">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-tag-helpers-library.csproj)]

### <a name="view-components"></a><span data-ttu-id="2c7ad-183">视图组件</span><span class="sxs-lookup"><span data-stu-id="2c7ad-183">View components</span></span>

<span data-ttu-id="2c7ad-184">包含[视图组件](xref:mvc/views/view-components)的项目应使用 `Microsoft.NET.Sdk` SDK。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-184">A project that includes [View components](xref:mvc/views/view-components) should use the `Microsoft.NET.Sdk` SDK.</span></span> <span data-ttu-id="2c7ad-185">若面向 .NET Core 3.x，则添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-185">If targeting .NET Core 3.x, add a `<FrameworkReference>` element for the shared framework.</span></span> <span data-ttu-id="2c7ad-186">例如：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-186">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netcoreapp3.0-basic-library.csproj)]

<span data-ttu-id="2c7ad-187">若面向 .NET Standard（以支持 ASP.NET Core 3.x 之前的版本），则添加 [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-187">If targeting .NET Standard (to support versions earlier than ASP.NET Core 3.x), add a package reference to [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures).</span></span> <span data-ttu-id="2c7ad-188">`Microsoft.AspNetCore.Mvc.ViewFeatures` 包移到了共享框架，因此不再发布。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-188">The `Microsoft.AspNetCore.Mvc.ViewFeatures` package moved into the shared framework and is therefore no longer published.</span></span> <span data-ttu-id="2c7ad-189">例如：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-189">For example:</span></span>

[!code-xml[](target-aspnetcore/samples/single-tfm/netstandard2.0-view-components-library.csproj)]

## <a name="support-multiple-aspnet-core-versions"></a><span data-ttu-id="2c7ad-190">支持多个 ASP.NET Core 版本</span><span class="sxs-lookup"><span data-stu-id="2c7ad-190">Support multiple ASP.NET Core versions</span></span>

<span data-ttu-id="2c7ad-191">创作支持多个 ASP.NET Core 变体的库需要多目标。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-191">Multi-targeting is required to author a library that supports multiple variants of ASP.NET Core.</span></span> <span data-ttu-id="2c7ad-192">以下列情况为例：标记帮助器库必须支持以下 ASP.NET Core 变体：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-192">Consider a scenario in which a Tag Helpers library must support the following ASP.NET Core variants:</span></span>

* <span data-ttu-id="2c7ad-193">面向 .NET Framework 4.6.1 的 ASP.NET Core 2.1</span><span class="sxs-lookup"><span data-stu-id="2c7ad-193">ASP.NET Core 2.1 targeting .NET Framework 4.6.1</span></span>
* <span data-ttu-id="2c7ad-194">面向 .NET Core 2.x 的 ASP.NET Core 2.x</span><span class="sxs-lookup"><span data-stu-id="2c7ad-194">ASP.NET Core 2.x targeting .NET Core 2.x</span></span>
* <span data-ttu-id="2c7ad-195">面向 .NET Core 3.x 的 ASP.NET Core 3.x</span><span class="sxs-lookup"><span data-stu-id="2c7ad-195">ASP.NET Core 3.x targeting .NET Core 3.x</span></span>

<span data-ttu-id="2c7ad-196">以下项目文件通过 `TargetFrameworks` 属性支持这些变体：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-196">The following project file supports these variants via the `TargetFrameworks` property:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

<span data-ttu-id="2c7ad-197">上面的项目文件将完成以下操作：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-197">With the preceding project file:</span></span>

* <span data-ttu-id="2c7ad-198">为所有使用者添加 `Markdig` 包。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-198">The `Markdig` package is added for all consumers.</span></span>
* <span data-ttu-id="2c7ad-199">[Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor) 的引用</span><span class="sxs-lookup"><span data-stu-id="2c7ad-199">A reference to [Microsoft.AspNetCore.Mvc.Razor](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor)</span></span> <span data-ttu-id="2c7ad-200">已针对面向 .NET Framework 4.6.1 或更高版本或者 .NET Core 2.x 的使用者添加。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-200">is added for consumers targeting .NET Framework 4.6.1 or later or .NET Core 2.x.</span></span> <span data-ttu-id="2c7ad-201">由于向后兼容性，此包的版本 2.1.0 适用于 ASP.NET Core 2.2。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-201">Version 2.1.0 of the package works with ASP.NET Core 2.2 because of backwards compatibility.</span></span>
* <span data-ttu-id="2c7ad-202">为面向 .NET Core 3.x 的使用者引用共享框架。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-202">The shared framework is referenced for consumers targeting .NET Core 3.x.</span></span> <span data-ttu-id="2c7ad-203">共享框架中包括 `Microsoft.AspNetCore.Mvc.Razor` 包。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-203">The `Microsoft.AspNetCore.Mvc.Razor` package is included in the shared framework.</span></span>

<span data-ttu-id="2c7ad-204">或者，可以面向 .NET Standard 2.0，而不面向 .NET Core 2.1 和 .NET Framework 4.6.1：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-204">Alternatively, .NET Standard 2.0 could be targeted instead of targeting both .NET Core 2.1 and .NET Framework 4.6.1:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/alternative-tag-helpers-library.csproj?highlight=4)]

<span data-ttu-id="2c7ad-205">对于上面的项目文件，有以下注意事项：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-205">With the preceding project file, the following caveats exist:</span></span>

* <span data-ttu-id="2c7ad-206">由于库只包含标记帮助器，面向 ASP.NET Core 运行的特定平台（.NET Core 和 .NET Framework）更为简单。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-206">Since the library only contains Tag Helpers, it's more straightforward to target the specific platforms on which ASP.NET Core runs: .NET Core and .NET Framework.</span></span> <span data-ttu-id="2c7ad-207">其他兼容 .NET Standard 2.0 的目标框架（如 Unity、UWP 和 Xamarin）无法使用标记帮助器。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-207">Tag Helpers can't be used by other .NET Standard 2.0-compliant target frameworks such as Unity, UWP, and Xamarin.</span></span>
* <span data-ttu-id="2c7ad-208">在 .NET Framework 中使用 .NET Standard 2.0 存在一些问题，这些在 .NET Framework 4.7.2 中已得到解决。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-208">Using .NET Standard 2.0 from .NET Framework has some issues that were addressed in .NET Framework 4.7.2.</span></span> <span data-ttu-id="2c7ad-209">通过面向 .NET Framework 4.6.1，可以改进使用 .NET Framework 4.6.1 到 4.7.1 的使用者的体验。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-209">You can improve the experience for consumers using .NET Framework 4.6.1 through 4.7.1 by targeting .NET Framework 4.6.1.</span></span>

<span data-ttu-id="2c7ad-210">如果库需要调用平台特定的 API，则面向特定 .NET 实现，而不面向 .NET Standard。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-210">If your library needs to call platform-specific APIs, target specific .NET implementations instead of .NET Standard.</span></span> <span data-ttu-id="2c7ad-211">有关详细信息，请参阅[多目标](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting)。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-211">For more information, see [Multi-targeting](/dotnet/standard/library-guidance/cross-platform-targeting#multi-targeting).</span></span>

## <a name="use-an-api-that-hasnt-changed"></a><span data-ttu-id="2c7ad-212">使用未变更的 API</span><span class="sxs-lookup"><span data-stu-id="2c7ad-212">Use an API that hasn't changed</span></span>

<span data-ttu-id="2c7ad-213">以下面的情况为例：你要将中间件库从 .NET Core 2.2 升级到 3.0。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-213">Imagine a scenario in which you're upgrading a middleware library from .NET Core 2.2 to 3.0.</span></span> <span data-ttu-id="2c7ad-214">库正在使用的 ASP.NET Core 中间件 API 尚未从 ASP.NET Core 2.2 变更到 3.0。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-214">The ASP.NET Core middleware APIs being used in the library haven't changed between ASP.NET Core 2.2 and 3.0.</span></span> <span data-ttu-id="2c7ad-215">若要继续让 .NET Core 3.0 支持此中间件库，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-215">To continue supporting the middleware library in .NET Core 3.0, take the following steps:</span></span>

* <span data-ttu-id="2c7ad-216">按照[标准库指南](/dotnet/standard/library-guidance/)操作。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-216">Follow the [standard library guidance](/dotnet/standard/library-guidance/).</span></span>
* <span data-ttu-id="2c7ad-217">若共享框架中不存在相应的程序集，则添加针对每个 API 的 NuGet 包的包引用。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-217">Add a package reference for each API's NuGet package if the corresponding assembly doesn't exist in the shared framework.</span></span>

## <a name="use-an-api-that-changed"></a><span data-ttu-id="2c7ad-218">使用已变更的 API</span><span class="sxs-lookup"><span data-stu-id="2c7ad-218">Use an API that changed</span></span>

<span data-ttu-id="2c7ad-219">以下面的情况为例：你要将库从 .NET Core 2.2 升级到 .NET Core 3.0。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-219">Imagine a scenario in which you're upgrading a library from .NET Core 2.2 to .NET Core 3.0.</span></span> <span data-ttu-id="2c7ad-220">库正在使用的 ASP.NET Core API 包含 ASP.NET Core 3.0 的[中断性变更](/dotnet/core/compatibility/breaking-changes)。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-220">An ASP.NET Core API being used in the library has a [breaking change](/dotnet/core/compatibility/breaking-changes) in ASP.NET Core 3.0.</span></span> <span data-ttu-id="2c7ad-221">请考虑此库是否可以重写为不使用所有版本中已损坏的 API。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-221">Consider whether the library can be rewritten to not use the broken API in all versions.</span></span>

<span data-ttu-id="2c7ad-222">若可以重写此库，则进行重写，并通过包引用继续面向早期版本的目标框架（例如 .NET Standard 2.0 或 .NET Framework 4.6.1）。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-222">If you can rewrite the library, do so and continue to target an earlier target framework (for example, .NET Standard 2.0 or .NET Framework 4.6.1) with package references.</span></span>

<span data-ttu-id="2c7ad-223">若无法重写此库，则执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-223">If you can't rewrite the library, take the following steps:</span></span>

* <span data-ttu-id="2c7ad-224">添加针对 .NET Core 3.0 的目标。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-224">Add a target for .NET Core 3.0.</span></span>
* <span data-ttu-id="2c7ad-225">添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-225">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="2c7ad-226">结合使用 [#if 预处理器指令](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)和对应的目标框架符号，以进行有条件的代码编译。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-226">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="2c7ad-227">例如，自 ASP.NET Core 3.0 起默认不可对 HTTP 请求和响应流进行同步读写。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-227">For example, synchronous reads and writes on HTTP request and response streams are disabled by default as of ASP.NET Core 3.0.</span></span> <span data-ttu-id="2c7ad-228">默认情况下，ASP.NET Core 2.2 支持同步行为。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-228">ASP.NET Core 2.2 supports the synchronous behavior by default.</span></span> <span data-ttu-id="2c7ad-229">以一个中间件库为例，在该库中，发生 I/O 时应可进行同步读写。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-229">Consider a middleware library in which synchronous reads and writes should be enabled where I/O is occurring.</span></span> <span data-ttu-id="2c7ad-230">此库应将用于启用同步功能的代码括在相应的预处理器指令中。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-230">The library should enclose the code to enable synchronous features in the appropriate preprocessor directive.</span></span> <span data-ttu-id="2c7ad-231">例如：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-231">For example:</span></span>

[!code-csharp[](target-aspnetcore/samples/middleware.cs?highlight=9-24)]

## <a name="use-an-api-introduced-in-30"></a><span data-ttu-id="2c7ad-232">使用 3.0 引入的 API</span><span class="sxs-lookup"><span data-stu-id="2c7ad-232">Use an API introduced in 3.0</span></span>

<span data-ttu-id="2c7ad-233">假设你想要使用 ASP.NET Core 3.0 引入的 ASP.NET Core API。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-233">Imagine that you want to use an ASP.NET Core API that was introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="2c7ad-234">请考虑下列问题：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-234">Consider the following questions:</span></span>

1. <span data-ttu-id="2c7ad-235">此库在功能上是否需要此新 API？</span><span class="sxs-lookup"><span data-stu-id="2c7ad-235">Does the library functionally require the new API?</span></span>
1. <span data-ttu-id="2c7ad-236">库是否可以通过其他方式实现此功能？</span><span class="sxs-lookup"><span data-stu-id="2c7ad-236">Can the library implement this feature in a different way?</span></span>

<span data-ttu-id="2c7ad-237">若此库在功能上需要此 API，并且没有实现它的下层方法：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-237">If the library functionally requires the API and there's no way to implement it down-level:</span></span>

* <span data-ttu-id="2c7ad-238">仅面向 .NET Core 3.x。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-238">Target .NET Core 3.x only.</span></span>
* <span data-ttu-id="2c7ad-239">添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-239">Add a `<FrameworkReference>` element for the shared framework.</span></span>

<span data-ttu-id="2c7ad-240">若此库可以通过其他方式实现此功能：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-240">If the library can implement the feature in a different way:</span></span>

* <span data-ttu-id="2c7ad-241">将 .NET Core 3.x 添加为目标框架。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-241">Add .NET Core 3.x as a target framework.</span></span>
* <span data-ttu-id="2c7ad-242">添加针对共享框架的 `<FrameworkReference>` 元素。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-242">Add a `<FrameworkReference>` element for the shared framework.</span></span>
* <span data-ttu-id="2c7ad-243">结合使用 [#if 预处理器指令](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)和对应的目标框架符号，以进行有条件的代码编译。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-243">Use the [#if preprocessor directive](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if) with the appropriate target framework symbol to conditionally compile code.</span></span>

<span data-ttu-id="2c7ad-244">例如，以下标记帮助器使用 ASP.NET Core 3.0 引入的 <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> 接口。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-244">For example, the following Tag Helper uses the <xref:Microsoft.AspNetCore.Hosting.IWebHostEnvironment> interface introduced in ASP.NET Core 3.0.</span></span> <span data-ttu-id="2c7ad-245">面向 .NET Core 3.0 的使用者执行 `NETCOREAPP3_0` 目标框架符号定义的代码路径。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-245">Consumers targeting .NET Core 3.0 execute the code path defined by the `NETCOREAPP3_0` target framework symbol.</span></span> <span data-ttu-id="2c7ad-246">对于 .NET Core 2.1 和 .NET Framework 4.6.1 使用者，标记帮助器的构造函数参数类型更改为 <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment>。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-246">The Tag Helper's constructor parameter type changes to <xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment> for .NET Core 2.1 and .NET Framework 4.6.1 consumers.</span></span> <span data-ttu-id="2c7ad-247">此更改很有必要，因为 ASP.NET Core 3.0 将 `IHostingEnvironment` 标记为过时，并推荐将 `IWebHostEnvironment` 作为替代项。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-247">This change was necessary because ASP.NET Core 3.0 marked `IHostingEnvironment` as obsolete and recommended `IWebHostEnvironment` as the replacement.</span></span>

```csharp
[HtmlTargetElement("script", Attributes = "asp-inline")]
public class ScriptInliningTagHelper : TagHelper
{
    private readonly IFileProvider _wwwroot;

#if NETCOREAPP3_0
    public ScriptInliningTagHelper(IWebHostEnvironment env)
#else
    public ScriptInliningTagHelper(IHostingEnvironment env)
#endif
    {
        _wwwroot = env.WebRootFileProvider;
    }

    // code omitted for brevity
}
```

<span data-ttu-id="2c7ad-248">以下多目标项目文件支持此标记帮助器方案：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-248">The following multi-targeted project file supports this Tag Helper scenario:</span></span>

[!code-xml[](target-aspnetcore/samples/multi-tfm/recommended-tag-helpers-library.csproj)]

## <a name="use-an-api-removed-from-the-shared-framework"></a><span data-ttu-id="2c7ad-249">使用共享框架已删除的 API</span><span class="sxs-lookup"><span data-stu-id="2c7ad-249">Use an API removed from the shared framework</span></span>

<span data-ttu-id="2c7ad-250">若要使用共享框架已删除的 ASP.NET Core 程序集，请添加相应的包引用。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-250">To use an ASP.NET Core assembly that was removed from the shared framework, add the appropriate package reference.</span></span> <span data-ttu-id="2c7ad-251">若要查看 ASP.NET Core 3.0 的共享框架已删除的包列表，请参阅[删除过时的包引用](xref:migration/22-to-30#remove-obsolete-package-references)。</span><span class="sxs-lookup"><span data-stu-id="2c7ad-251">For a list of packages removed from the shared framework in ASP.NET Core 3.0, see [Remove obsolete package references](xref:migration/22-to-30#remove-obsolete-package-references).</span></span>

<span data-ttu-id="2c7ad-252">例如，添加 Web API 客户端：</span><span class="sxs-lookup"><span data-stu-id="2c7ad-252">For example, to add the web API client:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>netcoreapp3.0</TargetFramework>
  </PropertyGroup>

  <ItemGroup>
    <FrameworkReference Include="Microsoft.AspNetCore.App" />
  </ItemGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.AspNet.WebApi.Client" Version="5.2.7" />
  </ItemGroup>

</Project>
```

## <a name="additional-resources"></a><span data-ttu-id="2c7ad-253">其他资源</span><span class="sxs-lookup"><span data-stu-id="2c7ad-253">Additional resources</span></span>

* <xref:razor-pages/ui-class>
* <xref:blazor/components/class-libraries>
* [<span data-ttu-id="2c7ad-254">.NET 实现支持</span><span class="sxs-lookup"><span data-stu-id="2c7ad-254">.NET implementation support</span></span>](/dotnet/standard/net-standard#net-implementation-support)
* [<span data-ttu-id="2c7ad-255">.NET 支持策略</span><span class="sxs-lookup"><span data-stu-id="2c7ad-255">.NET support policies</span></span>](https://dotnet.microsoft.com/platform/support/policy)
