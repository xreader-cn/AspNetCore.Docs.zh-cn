---
title: ASP.NET Core 中的 Razor 文件编译
author: rick-anderson
description: 了解 Razor 文件编译在 ASP.NET Core 应用中的发生方式。
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440930"
---
# <a name="razor-file-compilation-in-aspnet-core"></a><span data-ttu-id="39cc9-103">ASP.NET Core 中的 Razor 文件编译</span><span class="sxs-lookup"><span data-stu-id="39cc9-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="39cc9-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="39cc9-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="39cc9-105">在生成和发布时均使用 [Razor SDK](xref:razor-pages/sdk) 编译扩展名为 .cshtml 的 Razor 文件\*\*。</span><span class="sxs-lookup"><span data-stu-id="39cc9-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="39cc9-106">通过配置项目，可以选择启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="39cc9-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="39cc9-107">Razor 编译</span><span class="sxs-lookup"><span data-stu-id="39cc9-107">Razor compilation</span></span>

<span data-ttu-id="39cc9-108">Razor SDK 默认启用 Razor 文件的生成时间和发布时间编译。</span><span class="sxs-lookup"><span data-stu-id="39cc9-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="39cc9-109">启用后，运行时编译将补充生成时间编译，允许在编辑 Razor 文件时对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="39cc9-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="39cc9-110">在项目创建时启用运行时编译</span><span class="sxs-lookup"><span data-stu-id="39cc9-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="39cc9-111">Razor 页面和 MVC 项目模板包括一个选项，用于在创建项目时启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="39cc9-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="39cc9-112">ASP.NET酷3.1及更高版本支持此选项。</span><span class="sxs-lookup"><span data-stu-id="39cc9-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="39cc9-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="39cc9-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="39cc9-114">在 **"创建新ASP.NET核心 Web 应用程序**对话框中：</span><span class="sxs-lookup"><span data-stu-id="39cc9-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="39cc9-115">选择**Web 应用程序**或**Web 应用程序（模型视图控制器）** 项目模板。</span><span class="sxs-lookup"><span data-stu-id="39cc9-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="39cc9-116">选中启用**Razor 运行时编译**复选框。</span><span class="sxs-lookup"><span data-stu-id="39cc9-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="39cc9-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="39cc9-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="39cc9-118">使用`-rrc`或`--razor-runtime-compilation`模板选项。</span><span class="sxs-lookup"><span data-stu-id="39cc9-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="39cc9-119">例如，以下命令创建启用运行时编译的新 Razor Pages 项目：</span><span class="sxs-lookup"><span data-stu-id="39cc9-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="39cc9-120">在现有项目中启用运行时编译</span><span class="sxs-lookup"><span data-stu-id="39cc9-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="39cc9-121">要为现有项目中的所有环境启用运行时编译，：</span><span class="sxs-lookup"><span data-stu-id="39cc9-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="39cc9-122">安装[微软.AspNetCore.Mvc.Razor.运行时编译](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)NuGet包。</span><span class="sxs-lookup"><span data-stu-id="39cc9-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="39cc9-123">更新项目的 `Startup.ConfigureServices` 方法以包含对 <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="39cc9-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="39cc9-124">例如：</span><span class="sxs-lookup"><span data-stu-id="39cc9-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="39cc9-125">在现有项目中有条件地启用运行时编译</span><span class="sxs-lookup"><span data-stu-id="39cc9-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="39cc9-126">启用运行时编译时可使其仅用于本地开发。</span><span class="sxs-lookup"><span data-stu-id="39cc9-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="39cc9-127">以这种方式有条件地启用可确保已发布的输出：</span><span class="sxs-lookup"><span data-stu-id="39cc9-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="39cc9-128">使用编译视图。</span><span class="sxs-lookup"><span data-stu-id="39cc9-128">Uses compiled views.</span></span>
* <span data-ttu-id="39cc9-129">不会在生产环境中启用文件观察程序。</span><span class="sxs-lookup"><span data-stu-id="39cc9-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="39cc9-130">要仅在开发环境中启用运行时编译：</span><span class="sxs-lookup"><span data-stu-id="39cc9-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="39cc9-131">安装[微软.AspNetCore.Mvc.Razor.运行时编译](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)NuGet包。</span><span class="sxs-lookup"><span data-stu-id="39cc9-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="39cc9-132">修改`environmentVariables`*启动设置中的*启动配置文件部分 ：</span><span class="sxs-lookup"><span data-stu-id="39cc9-132">Modify the launch profile `environmentVariables` section in *launchSettings.json*:</span></span>
    * <span data-ttu-id="39cc9-133">验证`ASPNETCORE_ENVIRONMENT`设置为`"Development"`。</span><span class="sxs-lookup"><span data-stu-id="39cc9-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="39cc9-134">将 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 设置为 `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`。</span><span class="sxs-lookup"><span data-stu-id="39cc9-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="39cc9-135">在下面的示例中，在 和`IIS Express``RazorPagesApp`启动配置文件的开发环境中启用了运行时编译：</span><span class="sxs-lookup"><span data-stu-id="39cc9-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="39cc9-136">项目`Startup`类不需要更改代码。</span><span class="sxs-lookup"><span data-stu-id="39cc9-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="39cc9-137">在运行时，ASP.NET Core 搜索 中的`Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`[程序集级托管启动属性](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute)。</span><span class="sxs-lookup"><span data-stu-id="39cc9-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="39cc9-138">该`HostingStartup`属性指定要执行的应用启动代码。</span><span class="sxs-lookup"><span data-stu-id="39cc9-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="39cc9-139">该启动代码支持运行时编译。</span><span class="sxs-lookup"><span data-stu-id="39cc9-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a><span data-ttu-id="39cc9-140">为 Razor 类库启用运行时编译</span><span class="sxs-lookup"><span data-stu-id="39cc9-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="39cc9-141">请考虑 Razor 页面项目引用名为*MyClassLib*的[Razor 类库 （RCL）](xref:razor-pages/ui-class)的方案。</span><span class="sxs-lookup"><span data-stu-id="39cc9-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib*.</span></span> <span data-ttu-id="39cc9-142">RCL 包含一个 *_Layout.cshtml*文件，所有团队的 MVC 和 Razor 页面项目都使用该文件。</span><span class="sxs-lookup"><span data-stu-id="39cc9-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="39cc9-143">您希望为该 RCL 中的 *_Layout.cshtml*文件启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="39cc9-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="39cc9-144">在 Razor 页面项目中进行以下更改：</span><span class="sxs-lookup"><span data-stu-id="39cc9-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="39cc9-145">使用["有条件地"中的指令在现有项目中启用运行时编译](#conditionally-enable-runtime-compilation-in-an-existing-project)， 启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="39cc9-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="39cc9-146">在 中`Startup.ConfigureServices`配置运行时编译选项。</span><span class="sxs-lookup"><span data-stu-id="39cc9-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="39cc9-147">在前面的代码中，构造了*MyClassLib* RCL 的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="39cc9-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="39cc9-148">[物理文件提供程序 API](xref:fundamentals/file-providers#physicalfileprovider)用于查找该绝对路径上的目录和文件。</span><span class="sxs-lookup"><span data-stu-id="39cc9-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="39cc9-149">最后，实例`PhysicalFileProvider`被添加到文件提供程序集合中，允许访问 RCL 的 *.cshtml*文件。</span><span class="sxs-lookup"><span data-stu-id="39cc9-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39cc9-150">其他资源</span><span class="sxs-lookup"><span data-stu-id="39cc9-150">Additional resources</span></span>

* <span data-ttu-id="39cc9-151">[Razor 编译上构建和 Razor 编译上发布](xref:razor-pages/sdk#properties)属性。</span><span class="sxs-lookup"><span data-stu-id="39cc9-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="39cc9-152">在生成和发布时均使用 [Razor SDK](xref:razor-pages/sdk) 编译扩展名为 .cshtml 的 Razor 文件\*\*。</span><span class="sxs-lookup"><span data-stu-id="39cc9-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="39cc9-153">通过配置应用程序，可以选择启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="39cc9-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="39cc9-154">Razor 编译</span><span class="sxs-lookup"><span data-stu-id="39cc9-154">Razor compilation</span></span>

<span data-ttu-id="39cc9-155">Razor SDK 默认启用 Razor 文件的生成时间和发布时间编译。</span><span class="sxs-lookup"><span data-stu-id="39cc9-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="39cc9-156">启用后，运行时编译将补充生成时间编译，允许在编辑 Razor 文件时对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="39cc9-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="39cc9-157">运行时编译</span><span class="sxs-lookup"><span data-stu-id="39cc9-157">Runtime compilation</span></span>

<span data-ttu-id="39cc9-158">为所有环境和配置模式启用运行时编译：</span><span class="sxs-lookup"><span data-stu-id="39cc9-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="39cc9-159">安装[微软.AspNetCore.Mvc.Razor.运行时编译](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)NuGet包。</span><span class="sxs-lookup"><span data-stu-id="39cc9-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="39cc9-160">更新项目的 `Startup.ConfigureServices` 方法以包含对 <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="39cc9-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="39cc9-161">例如：</span><span class="sxs-lookup"><span data-stu-id="39cc9-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="39cc9-162">有条件地启用运行时编译</span><span class="sxs-lookup"><span data-stu-id="39cc9-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="39cc9-163">启用运行时编译时可使其仅用于本地开发。</span><span class="sxs-lookup"><span data-stu-id="39cc9-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="39cc9-164">以这种方式有条件地启用可确保已发布的输出：</span><span class="sxs-lookup"><span data-stu-id="39cc9-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="39cc9-165">使用编译视图。</span><span class="sxs-lookup"><span data-stu-id="39cc9-165">Uses compiled views.</span></span>
* <span data-ttu-id="39cc9-166">较小。</span><span class="sxs-lookup"><span data-stu-id="39cc9-166">Is smaller in size.</span></span>
* <span data-ttu-id="39cc9-167">不会在生产环境中启用文件观察程序。</span><span class="sxs-lookup"><span data-stu-id="39cc9-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="39cc9-168">基于环境和配置模式启用运行时编译：</span><span class="sxs-lookup"><span data-stu-id="39cc9-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="39cc9-169">根据活动的 `Configuration` 值，有条件地引用 [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) 包：</span><span class="sxs-lookup"><span data-stu-id="39cc9-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="39cc9-170">更新项目的 `Startup.ConfigureServices` 方法以包含对 `AddRazorRuntimeCompilation` 的调用。</span><span class="sxs-lookup"><span data-stu-id="39cc9-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="39cc9-171">有条件地执行 `AddRazorRuntimeCompilation`，使其仅当 `ASPNETCORE_ENVIRONMENT` 变量设置为 `Development`时在调试模式下运行：</span><span class="sxs-lookup"><span data-stu-id="39cc9-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="39cc9-172">其他资源</span><span class="sxs-lookup"><span data-stu-id="39cc9-172">Additional resources</span></span>

* <span data-ttu-id="39cc9-173">[Razor 编译上构建和 Razor 编译上发布](xref:razor-pages/sdk#properties)属性。</span><span class="sxs-lookup"><span data-stu-id="39cc9-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="39cc9-174">有关显示跨项目进行运行时编译工作的示例，请参阅[GitHub 上的运行时编译示例](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation)。</span><span class="sxs-lookup"><span data-stu-id="39cc9-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="39cc9-175">调用相关的 Razor 页和 MVC 视图时，Razor 文件在运行时进行编译。</span><span class="sxs-lookup"><span data-stu-id="39cc9-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="39cc9-176">在生成时和发布时使用 [Razor SDK](xref:razor-pages/sdk) 编译 Razor 文件。</span><span class="sxs-lookup"><span data-stu-id="39cc9-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="razor-compilation"></a><span data-ttu-id="39cc9-177">Razor 编译</span><span class="sxs-lookup"><span data-stu-id="39cc9-177">Razor compilation</span></span>

<span data-ttu-id="39cc9-178">Razor SDK 默认启用 Razor 文件的生成时和发布时编译。</span><span class="sxs-lookup"><span data-stu-id="39cc9-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="39cc9-179">Razor 文件更新后，支持在生成时编辑这些文件。</span><span class="sxs-lookup"><span data-stu-id="39cc9-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="39cc9-180">默认情况下，只有编译 Razor 文件所需的编译的 Views.dll（而非 .cshtml）文件或引用程序集随应用一起部署\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="39cc9-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="39cc9-181">已弃用预编译工具，并且将在 ASP.NET Core 3.0 中删除该工具。</span><span class="sxs-lookup"><span data-stu-id="39cc9-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="39cc9-182">建议迁移到 [Razor Sdk](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="39cc9-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="39cc9-183">仅当项目文件中未设置特定于预编译的属性时，Razor SDK 才有效。</span><span class="sxs-lookup"><span data-stu-id="39cc9-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="39cc9-184">例如，通过将 .csproj 文件的 `MvcRazorCompileOnPublish` 属性设置为 `true` 来禁用 Razor SDK\*\*。</span><span class="sxs-lookup"><span data-stu-id="39cc9-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="39cc9-185">运行时编译</span><span class="sxs-lookup"><span data-stu-id="39cc9-185">Runtime compilation</span></span>

<span data-ttu-id="39cc9-186">通过 Razor 文件的运行时编译补充生成时编译。</span><span class="sxs-lookup"><span data-stu-id="39cc9-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="39cc9-187">当 .cshtml 文件的内容发生更改时，ASP.NET Core MVC 将重新编译 Razor 文件\*\*。</span><span class="sxs-lookup"><span data-stu-id="39cc9-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="39cc9-188">其他资源</span><span class="sxs-lookup"><span data-stu-id="39cc9-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
