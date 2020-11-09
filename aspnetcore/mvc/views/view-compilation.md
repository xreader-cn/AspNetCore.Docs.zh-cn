---
title: 'Razor ASP.NET Core 中的文件编译'
author: rick-anderson
description: '了解文件编译如何 Razor 出现在 ASP.NET Core 的应用程序中。'
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: mvc/views/view-compilation
ms.openlocfilehash: 77ca96b329136ee044ab6fc5f6b5ebb5b67fe64c
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059073"
---
# <a name="no-locrazor-file-compilation-in-aspnet-core"></a><span data-ttu-id="3a553-103">Razor ASP.NET Core 中的文件编译</span><span class="sxs-lookup"><span data-stu-id="3a553-103">Razor file compilation in ASP.NET Core</span></span>

<span data-ttu-id="3a553-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="3a553-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.1"

<span data-ttu-id="3a553-105">Razor使用 [ Razor SDK](xref:razor-pages/sdk)的生成和发布时间都将编译带有 *..* 扩展名的文件。</span><span class="sxs-lookup"><span data-stu-id="3a553-105">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="3a553-106">可以选择通过配置项目来启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="3a553-106">Runtime compilation may be optionally enabled by configuring your project.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="3a553-107">Razor 汇编</span><span class="sxs-lookup"><span data-stu-id="3a553-107">Razor compilation</span></span>

<span data-ttu-id="3a553-108">Razor默认情况下，SDK 会启用文件的生成时间和发布时编译 Razor 。</span><span class="sxs-lookup"><span data-stu-id="3a553-108">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="3a553-109">启用后，运行时编译会补充生成时编译，允许在 Razor 编辑文件时对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="3a553-109">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="enable-runtime-compilation-at-project-creation"></a><span data-ttu-id="3a553-110">在创建项目时启用运行时编译</span><span class="sxs-lookup"><span data-stu-id="3a553-110">Enable runtime compilation at project creation</span></span>

<span data-ttu-id="3a553-111">Razor页面和 MVC 项目模板包括一个选项，用于在创建项目时启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="3a553-111">The Razor Pages and MVC project templates include an option to enable runtime compilation when the project is created.</span></span> <span data-ttu-id="3a553-112">ASP.NET Core 3.1 及更高版本中支持此选项。</span><span class="sxs-lookup"><span data-stu-id="3a553-112">This option is supported in ASP.NET Core 3.1 and later.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3a553-113">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3a553-113">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3a553-114">在 " **新建 ASP.NET Core web 应用程序** " 对话框中：</span><span class="sxs-lookup"><span data-stu-id="3a553-114">In the **Create a new ASP.NET Core web application** dialog:</span></span>

1. <span data-ttu-id="3a553-115">选择 " **Web 应用程序** " 或 " **web 应用程序 (模型-视图-控制器)** " 项目模板。</span><span class="sxs-lookup"><span data-stu-id="3a553-115">Select either the **Web Application** or the **Web Application (Model-View-Controller)** project template.</span></span>
1. <span data-ttu-id="3a553-116">选中 " **启用 Razor 运行时编译** " 复选框。</span><span class="sxs-lookup"><span data-stu-id="3a553-116">Select the **Enable Razor runtime compilation** check box.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3a553-117">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3a553-117">.NET Core CLI</span></span>](#tab/netcore-cli)

<span data-ttu-id="3a553-118">使用 `-rrc` 或 `--razor-runtime-compilation` 模板选项。</span><span class="sxs-lookup"><span data-stu-id="3a553-118">Use the `-rrc` or `--razor-runtime-compilation` template option.</span></span> <span data-ttu-id="3a553-119">例如，以下命令将创建一个启用了 Razor 运行时编译的新页项目：</span><span class="sxs-lookup"><span data-stu-id="3a553-119">For example, the following command creates a new Razor Pages project with runtime compilation enabled:</span></span>

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="3a553-120">在现有项目中启用运行时编译</span><span class="sxs-lookup"><span data-stu-id="3a553-120">Enable runtime compilation in an existing project</span></span>

<span data-ttu-id="3a553-121">若要为现有项目中的所有环境启用运行时编译：</span><span class="sxs-lookup"><span data-stu-id="3a553-121">To enable runtime compilation for all environments in an existing project:</span></span>

1. <span data-ttu-id="3a553-122">安装 [AspNetCore Razor .。RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="3a553-122">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="3a553-123">更新项目的 `Startup.ConfigureServices` 方法以包含对 <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="3a553-123">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="3a553-124">例如： 。</span><span class="sxs-lookup"><span data-stu-id="3a553-124">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a><span data-ttu-id="3a553-125">在现有项目中有条件地启用运行时编译</span><span class="sxs-lookup"><span data-stu-id="3a553-125">Conditionally enable runtime compilation in an existing project</span></span>

<span data-ttu-id="3a553-126">启用运行时编译时可使其仅用于本地开发。</span><span class="sxs-lookup"><span data-stu-id="3a553-126">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="3a553-127">以这种方式有条件地启用可确保已发布的输出：</span><span class="sxs-lookup"><span data-stu-id="3a553-127">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="3a553-128">使用编译视图。</span><span class="sxs-lookup"><span data-stu-id="3a553-128">Uses compiled views.</span></span>
* <span data-ttu-id="3a553-129">不会在生产环境中启用文件观察程序。</span><span class="sxs-lookup"><span data-stu-id="3a553-129">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="3a553-130">仅在开发环境中启用运行时编译：</span><span class="sxs-lookup"><span data-stu-id="3a553-130">To enable runtime compilation only in the Development environment:</span></span>

1. <span data-ttu-id="3a553-131">安装 [AspNetCore Razor .。RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="3a553-131">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>
1. <span data-ttu-id="3a553-132">修改 `environmentVariables` *launchSettings.js上* 的启动配置文件部分：</span><span class="sxs-lookup"><span data-stu-id="3a553-132">Modify the launch profile `environmentVariables` section in *launchSettings.json* :</span></span>
    * <span data-ttu-id="3a553-133">验证 `ASPNETCORE_ENVIRONMENT` 是否设置为 `"Development"` 。</span><span class="sxs-lookup"><span data-stu-id="3a553-133">Verify `ASPNETCORE_ENVIRONMENT` is set to `"Development"`.</span></span>
    * <span data-ttu-id="3a553-134">将 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 设置为 `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`。</span><span class="sxs-lookup"><span data-stu-id="3a553-134">Set `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` to `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`.</span></span>

<span data-ttu-id="3a553-135">在下面的示例中，在 `IIS Express` 和启动配置文件的开发环境中启用了运行时编译 `RazorPagesApp` ：</span><span class="sxs-lookup"><span data-stu-id="3a553-135">In the following example, runtime compilation is enabled in the Development environment for the `IIS Express` and `RazorPagesApp` launch profiles:</span></span>

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

<span data-ttu-id="3a553-136">项目的类中不需要更改代码 `Startup` 。</span><span class="sxs-lookup"><span data-stu-id="3a553-136">No code changes are needed in the project's `Startup` class.</span></span> <span data-ttu-id="3a553-137">在运行时，ASP.NET Core 在中搜索 [程序集级别的 HostingStartup 特性](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation` 。</span><span class="sxs-lookup"><span data-stu-id="3a553-137">At runtime, ASP.NET Core searches for an [assembly-level HostingStartup attribute](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute) in `Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`.</span></span> <span data-ttu-id="3a553-138">`HostingStartup`特性指定要执行的应用程序启动代码。</span><span class="sxs-lookup"><span data-stu-id="3a553-138">The `HostingStartup` attribute specifies the app startup code to execute.</span></span> <span data-ttu-id="3a553-139">该启动代码启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="3a553-139">That startup code enables runtime compilation.</span></span>

## <a name="enable-runtime-compilation-for-a-no-locrazor-class-library"></a><span data-ttu-id="3a553-140">为类库启用运行时编译 Razor</span><span class="sxs-lookup"><span data-stu-id="3a553-140">Enable runtime compilation for a Razor Class Library</span></span>

<span data-ttu-id="3a553-141">请考虑以下方案 Razor ：页面项目引用类库 (名为 *MYCLASSLIB* 的 [ Razor RCL)](xref:razor-pages/ui-class) 。</span><span class="sxs-lookup"><span data-stu-id="3a553-141">Consider a scenario in which a Razor Pages project references a [Razor Class Library (RCL)](xref:razor-pages/ui-class) named *MyClassLib* .</span></span> <span data-ttu-id="3a553-142">RCL 包含一个 *_Layout 的 cshtml* 文件，其中的所有团队 MVC 和 Razor 页面项目都使用。</span><span class="sxs-lookup"><span data-stu-id="3a553-142">The RCL contains a *_Layout.cshtml* file that all of your team's MVC and Razor Pages projects consume.</span></span> <span data-ttu-id="3a553-143">需要为 RCL 中的 *_Layout cshtml* 文件启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="3a553-143">You want to enable runtime compilation for the *_Layout.cshtml* file in that RCL.</span></span> <span data-ttu-id="3a553-144">在页面项目中进行以下更改 Razor ：</span><span class="sxs-lookup"><span data-stu-id="3a553-144">Make the following changes in the Razor Pages project:</span></span>

1. <span data-ttu-id="3a553-145">使用在 [现有项目中有条件地启用运行时编译中](#conditionally-enable-runtime-compilation-in-an-existing-project)的说明启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="3a553-145">Enable runtime compilation with the instructions at [Conditionally enable runtime compilation in an existing project](#conditionally-enable-runtime-compilation-in-an-existing-project).</span></span>
1. <span data-ttu-id="3a553-146">配置中的运行时编译选项 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="3a553-146">Configure the runtime compilation options in `Startup.ConfigureServices`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    <span data-ttu-id="3a553-147">在前面的代码中，构造 *MyClassLib* RCL 的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="3a553-147">In the preceding code, an absolute path to the *MyClassLib* RCL is constructed.</span></span> <span data-ttu-id="3a553-148">[PHYSICALFILEPROVIDER API](xref:fundamentals/file-providers#physicalfileprovider)用于查找该绝对路径中的目录和文件。</span><span class="sxs-lookup"><span data-stu-id="3a553-148">The [PhysicalFileProvider API](xref:fundamentals/file-providers#physicalfileprovider) is used to locate directories and files at that absolute path.</span></span> <span data-ttu-id="3a553-149">最后，将 `PhysicalFileProvider` 实例添加到 file 提供程序集合，该集合允许访问 RCL 的文件 *.cshtml* 。</span><span class="sxs-lookup"><span data-stu-id="3a553-149">Finally, the `PhysicalFileProvider` instance is added to a file providers collection, which allows access to the RCL's *.cshtml* files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a553-150">其他资源</span><span class="sxs-lookup"><span data-stu-id="3a553-150">Additional resources</span></span>

* <span data-ttu-id="3a553-151">[ Razor CompileOnBuild 和 Razor CompileOnPublish](xref:razor-pages/sdk#properties)属性。</span><span class="sxs-lookup"><span data-stu-id="3a553-151">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="3a553-152">Razor使用 [ Razor SDK](xref:razor-pages/sdk)的生成和发布时间都将编译带有 *..* 扩展名的文件。</span><span class="sxs-lookup"><span data-stu-id="3a553-152">Razor files with a *.cshtml* extension are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="3a553-153">通过配置应用程序，可以选择启用运行时编译。</span><span class="sxs-lookup"><span data-stu-id="3a553-153">Runtime compilation may be optionally enabled by configuring your application.</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="3a553-154">Razor 汇编</span><span class="sxs-lookup"><span data-stu-id="3a553-154">Razor compilation</span></span>

<span data-ttu-id="3a553-155">Razor默认情况下，SDK 会启用文件的生成时间和发布时编译 Razor 。</span><span class="sxs-lookup"><span data-stu-id="3a553-155">Build-time and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="3a553-156">启用后，运行时编译会补充生成时编译，允许在 Razor 编辑文件时对其进行更新。</span><span class="sxs-lookup"><span data-stu-id="3a553-156">When enabled, runtime compilation complements build-time compilation, allowing Razor files to be updated if they're edited.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="3a553-157">运行时编译</span><span class="sxs-lookup"><span data-stu-id="3a553-157">Runtime compilation</span></span>

<span data-ttu-id="3a553-158">为所有环境和配置模式启用运行时编译：</span><span class="sxs-lookup"><span data-stu-id="3a553-158">To enable runtime compilation for all environments and configuration modes:</span></span>

1. <span data-ttu-id="3a553-159">安装 [AspNetCore Razor .。RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="3a553-159">Install the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) NuGet package.</span></span>

1. <span data-ttu-id="3a553-160">更新项目的 `Startup.ConfigureServices` 方法以包含对 <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> 的调用。</span><span class="sxs-lookup"><span data-stu-id="3a553-160">Update the project's `Startup.ConfigureServices` method to include a call to <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*>.</span></span> <span data-ttu-id="3a553-161">例如： 。</span><span class="sxs-lookup"><span data-stu-id="3a553-161">For example:</span></span>

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a><span data-ttu-id="3a553-162">有条件地启用运行时编译</span><span class="sxs-lookup"><span data-stu-id="3a553-162">Conditionally enable runtime compilation</span></span>

<span data-ttu-id="3a553-163">启用运行时编译时可使其仅用于本地开发。</span><span class="sxs-lookup"><span data-stu-id="3a553-163">Runtime compilation can be enabled such that it's only available for local development.</span></span> <span data-ttu-id="3a553-164">以这种方式有条件地启用可确保已发布的输出：</span><span class="sxs-lookup"><span data-stu-id="3a553-164">Conditionally enabling in this manner ensures that the published output:</span></span>

* <span data-ttu-id="3a553-165">使用编译视图。</span><span class="sxs-lookup"><span data-stu-id="3a553-165">Uses compiled views.</span></span>
* <span data-ttu-id="3a553-166">较小。</span><span class="sxs-lookup"><span data-stu-id="3a553-166">Is smaller in size.</span></span>
* <span data-ttu-id="3a553-167">不会在生产环境中启用文件观察程序。</span><span class="sxs-lookup"><span data-stu-id="3a553-167">Doesn't enable file watchers in production.</span></span>

<span data-ttu-id="3a553-168">基于环境和配置模式启用运行时编译：</span><span class="sxs-lookup"><span data-stu-id="3a553-168">To enable runtime compilation based on the environment and configuration mode:</span></span>

1. <span data-ttu-id="3a553-169">按条件引用 [AspNetCore Razor 。](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) 基于活动值的 RuntimeCompilation 包 `Configuration` ：</span><span class="sxs-lookup"><span data-stu-id="3a553-169">Conditionally reference the [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) package based on the active `Configuration` value:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. <span data-ttu-id="3a553-170">更新项目的 `Startup.ConfigureServices` 方法以包含对 `AddRazorRuntimeCompilation` 的调用。</span><span class="sxs-lookup"><span data-stu-id="3a553-170">Update the project's `Startup.ConfigureServices` method to include a call to `AddRazorRuntimeCompilation`.</span></span> <span data-ttu-id="3a553-171">有条件地执行 `AddRazorRuntimeCompilation`，使其仅当 `ASPNETCORE_ENVIRONMENT` 变量设置为 `Development`时在调试模式下运行：</span><span class="sxs-lookup"><span data-stu-id="3a553-171">Conditionally execute `AddRazorRuntimeCompilation` such that it only runs in Debug mode when the `ASPNETCORE_ENVIRONMENT` variable is set to `Development`:</span></span>

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="3a553-172">其他资源</span><span class="sxs-lookup"><span data-stu-id="3a553-172">Additional resources</span></span>

* <span data-ttu-id="3a553-173">[ Razor CompileOnBuild 和 Razor CompileOnPublish](xref:razor-pages/sdk#properties)属性。</span><span class="sxs-lookup"><span data-stu-id="3a553-173">[RazorCompileOnBuild and RazorCompileOnPublish](xref:razor-pages/sdk#properties) properties.</span></span>
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* <span data-ttu-id="3a553-174">有关演示如何在项目中执行运行时编译的示例，请参阅 [GitHub 上的运行时编译示例](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) 。</span><span class="sxs-lookup"><span data-stu-id="3a553-174">See the [runtime compilation sample on GitHub](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation) for a sample that shows making runtime compilation work across projects.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="3a553-175">Razor调用关联的 Razor 页或 MVC 视图时，将在运行时编译文件。</span><span class="sxs-lookup"><span data-stu-id="3a553-175">A Razor file is compiled at runtime, when the associated Razor Page or MVC view is invoked.</span></span> <span data-ttu-id="3a553-176">Razor使用[ Razor SDK](xref:razor-pages/sdk)在生成和发布时编译文件。</span><span class="sxs-lookup"><span data-stu-id="3a553-176">Razor files are compiled at both build and publish time using the [Razor SDK](xref:razor-pages/sdk).</span></span>

## <a name="no-locrazor-compilation"></a><span data-ttu-id="3a553-177">Razor 汇编</span><span class="sxs-lookup"><span data-stu-id="3a553-177">Razor compilation</span></span>

<span data-ttu-id="3a553-178">Razor默认情况下，SDK 会启用文件的生成和发布时编译 Razor 。</span><span class="sxs-lookup"><span data-stu-id="3a553-178">Build- and publish-time compilation of Razor files is enabled by default by the Razor SDK.</span></span> <span data-ttu-id="3a553-179">在 Razor 生成时，支持更新已更新的文件。</span><span class="sxs-lookup"><span data-stu-id="3a553-179">Editing Razor files after they're updated is supported at build time.</span></span> <span data-ttu-id="3a553-180">默认情况下，仅编译 *Views.dll* 编译的Views.dll *和不需要* 的文件或引用程序集。 Razor</span><span class="sxs-lookup"><span data-stu-id="3a553-180">By default, only the compiled *Views.dll* and no *.cshtml* files or references assemblies required to compile Razor files are deployed with your app.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="3a553-181">已弃用预编译工具，并且将在 ASP.NET Core 3.0 中删除该工具。</span><span class="sxs-lookup"><span data-stu-id="3a553-181">The precompilation tool has been deprecated, and will be removed in ASP.NET Core 3.0.</span></span> <span data-ttu-id="3a553-182">建议迁移到[ Razor Sdk](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="3a553-182">We recommend migrating to [Razor Sdk](xref:razor-pages/sdk).</span></span>
>
> <span data-ttu-id="3a553-183">RazorSDK 仅在项目文件中未设置预编译特定属性时有效。</span><span class="sxs-lookup"><span data-stu-id="3a553-183">The Razor SDK is effective only when no precompilation-specific properties are set in the project file.</span></span> <span data-ttu-id="3a553-184">例如，设置 *.csproj* 文件的 `MvcRazorCompileOnPublish` 属性以 `true` 禁用该 Razor SDK。</span><span class="sxs-lookup"><span data-stu-id="3a553-184">For instance, setting the *.csproj* file's `MvcRazorCompileOnPublish` property to `true` disables the Razor SDK.</span></span>

## <a name="runtime-compilation"></a><span data-ttu-id="3a553-185">运行时编译</span><span class="sxs-lookup"><span data-stu-id="3a553-185">Runtime compilation</span></span>

<span data-ttu-id="3a553-186">生成时编译由文件的运行时编译补充 Razor 。</span><span class="sxs-lookup"><span data-stu-id="3a553-186">Build-time compilation is supplemented by runtime compilation of Razor files.</span></span> <span data-ttu-id="3a553-187">ASP.NET Core MVC 文件的 Razor 内容发生更改时 *，将* 重新编译文件。</span><span class="sxs-lookup"><span data-stu-id="3a553-187">ASP.NET Core MVC will recompile Razor files when the contents of a *.cshtml* file change.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3a553-188">其他资源</span><span class="sxs-lookup"><span data-stu-id="3a553-188">Additional resources</span></span>

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
