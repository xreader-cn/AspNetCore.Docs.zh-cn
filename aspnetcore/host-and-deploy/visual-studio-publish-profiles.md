---
title: 用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件 (.pubxml)
author: rick-anderson
description: 了解如何在 Visual Studio 中创建发布配置文件，并将它们用于管理针对各种目标的 ASP.NET Core 应用部署。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/28/2020
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
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: eae4a19042efded03f10e9ebd17122232f0323eb
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97854634"
---
# <a name="visual-studio-publish-profiles-pubxml-for-aspnet-core-app-deployment"></a><span data-ttu-id="c48f0-103">用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件 (.pubxml)</span><span class="sxs-lookup"><span data-stu-id="c48f0-103">Visual Studio publish profiles (.pubxml) for ASP.NET Core app deployment</span></span>

<span data-ttu-id="c48f0-104">作者：[Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="c48f0-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="c48f0-105">本文档重点介绍了如何通过 Visual Studio 2019 或更高版本创建和使用发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-105">This document focuses on using Visual Studio 2019 or later to create and use publish profiles.</span></span> <span data-ttu-id="c48f0-106">通过 Visual Studio 创建的发布配置文件可用于 MSBuild 和 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="c48f0-106">The publish profiles created with Visual Studio can be used with MSBuild and Visual Studio.</span></span> <span data-ttu-id="c48f0-107">要查看说明了解如何发布到 Azure，请参阅 <xref:tutorials/publish-to-azure-webapp-using-vs>。</span><span class="sxs-lookup"><span data-stu-id="c48f0-107">For instructions on publishing to Azure, see <xref:tutorials/publish-to-azure-webapp-using-vs>.</span></span>

<span data-ttu-id="c48f0-108">`dotnet new mvc` 命令生成包含以下根级别 [\<Project> 元素](/visualstudio/msbuild/project-element-msbuild)的项目文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-108">The `dotnet new mvc` command produces a project file containing the following root-level [\<Project> element](/visualstudio/msbuild/project-element-msbuild):</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="c48f0-109">前导 `<Project>` 元素的 `Sdk` 属性分别从 $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props 和 $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets 导入 MSBuild [属性](/visualstudio/msbuild/msbuild-properties)和[目标](/visualstudio/msbuild/msbuild-targets)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="c48f0-110">`$(MSBuildSDKsPath)`（装有 Visual Studio 2019 Enterprise）的默认位置是 %programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c48f0-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="c48f0-111">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) 依赖其他 SDK，包括 `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) 和 `Microsoft.NET.Sdk.Razor` ([Razor Razor SDK](xref:razor-pages/sdk))。</span><span class="sxs-lookup"><span data-stu-id="c48f0-111">`Microsoft.NET.Sdk.Web` ([Web SDK](xref:razor-pages/web-sdk)) depends on other SDKs, including `Microsoft.NET.Sdk` ([.NET Core SDK](/dotnet/core/project-sdk/msbuild-props)) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="c48f0-112">将导入与每个从属 SDK 关联的 MSBuild 属性和目标。</span><span class="sxs-lookup"><span data-stu-id="c48f0-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="c48f0-113">发布目标将根据使用的发布方法，导入相应的目标集。</span><span class="sxs-lookup"><span data-stu-id="c48f0-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="c48f0-114">MSBuild 或 Visual Studio 加载项目时，执行下列高级别操作：</span><span class="sxs-lookup"><span data-stu-id="c48f0-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="c48f0-115">生成项目</span><span class="sxs-lookup"><span data-stu-id="c48f0-115">Build project</span></span>
* <span data-ttu-id="c48f0-116">计算要发布的文件</span><span class="sxs-lookup"><span data-stu-id="c48f0-116">Compute files to publish</span></span>
* <span data-ttu-id="c48f0-117">将文件发布到目标</span><span class="sxs-lookup"><span data-stu-id="c48f0-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="c48f0-118">计算项目项</span><span class="sxs-lookup"><span data-stu-id="c48f0-118">Compute project items</span></span>

<span data-ttu-id="c48f0-119">加载项目时，将计算 [MSBuild 项目项](/visualstudio/msbuild/common-msbuild-project-items)（文件）。</span><span class="sxs-lookup"><span data-stu-id="c48f0-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="c48f0-120">项类型确定如何处理该文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="c48f0-121">默认情况下，.cs 文件包含在 `Compile` 项列表内。</span><span class="sxs-lookup"><span data-stu-id="c48f0-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="c48f0-122">会对 `Compile` 项列表中的文件进行编译。</span><span class="sxs-lookup"><span data-stu-id="c48f0-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="c48f0-123">除了生成输出，`Content` 项列表还包含已发布的文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="c48f0-124">默认情况下，匹配模式 `wwwroot\**`、`**\*.config` 和 `**\*.json` 的文件包含在 `Content` 项列表内。</span><span class="sxs-lookup"><span data-stu-id="c48f0-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="c48f0-125">例如，`wwwroot\**`[glob 模式](https://gruntjs.com/configuring-tasks#globbing-patterns)与 wwwroot 文件夹及其子文件夹中的所有文件相匹配。</span><span class="sxs-lookup"><span data-stu-id="c48f0-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder and its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c48f0-126">[Web SDK](xref:razor-pages/web-sdk) 导入 [Razor SDK](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-126">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="c48f0-127">因此，匹配模式 `**\*.cshtml` 和 `**\*.razor` 的文件也同时包含在 `Content` 项列表内。</span><span class="sxs-lookup"><span data-stu-id="c48f0-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="c48f0-128">[Web SDK](xref:razor-pages/web-sdk) 导入 [Razor SDK](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-128">The [Web SDK](xref:razor-pages/web-sdk) imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="c48f0-129">因此，匹配 `**\*.cshtml` 模式的文件也同时包含在 `Content` 项列表内。</span><span class="sxs-lookup"><span data-stu-id="c48f0-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="c48f0-130">若要将文件显式添加到发布列表，请直接在 .csproj 文件中添加文件，如[包含文件](#include-files)一节中所示。</span><span class="sxs-lookup"><span data-stu-id="c48f0-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="c48f0-131">在 Visual Studio 中选择“发布”按钮时或从命令行发布时：</span><span class="sxs-lookup"><span data-stu-id="c48f0-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="c48f0-132">计算属性/项目（需要生成的文件）。</span><span class="sxs-lookup"><span data-stu-id="c48f0-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="c48f0-133">**仅限 Visual Studio**：NuGet 包已还原。</span><span class="sxs-lookup"><span data-stu-id="c48f0-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="c48f0-134">（用户需要在 CLI 上执行显式还原。）</span><span class="sxs-lookup"><span data-stu-id="c48f0-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="c48f0-135">生成项目。</span><span class="sxs-lookup"><span data-stu-id="c48f0-135">The project builds.</span></span>
* <span data-ttu-id="c48f0-136">计算发布项（需要发布的文件）。</span><span class="sxs-lookup"><span data-stu-id="c48f0-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="c48f0-137">文件已发布（计算的文件将被复制到发布目标）。</span><span class="sxs-lookup"><span data-stu-id="c48f0-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="c48f0-138">当 ASP.NET Core 项目在项目文件中引用 `Microsoft.NET.Sdk.Web` 时，会将 app_offline.htm 文件放在 Web 应用目录的根目录下。</span><span class="sxs-lookup"><span data-stu-id="c48f0-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="c48f0-139">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="c48f0-140">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="c48f0-141">基本命令行发布</span><span class="sxs-lookup"><span data-stu-id="c48f0-141">Basic command-line publishing</span></span>

<span data-ttu-id="c48f0-142">命令行发布适用于所有支持 .NET Core 的平台，而且不需要 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="c48f0-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="c48f0-143">在下面的示例中，从项目目录（其中包含 .csproj文件）运行 .NET Core CLI 的 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令。</span><span class="sxs-lookup"><span data-stu-id="c48f0-143">In the following examples, the .NET Core CLI's [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="c48f0-144">如果当前工作目录中没有项目文件夹，则在项目文件路径中显式传递。</span><span class="sxs-lookup"><span data-stu-id="c48f0-144">If the project folder isn't the current working directory, explicitly pass in the project file path.</span></span> <span data-ttu-id="c48f0-145">例如：</span><span class="sxs-lookup"><span data-stu-id="c48f0-145">For example:</span></span>

```dotnetcli
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="c48f0-146">运行以下命令以创建并发布 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="c48f0-146">Run the following commands to create and publish a web app:</span></span>

```dotnetcli
dotnet new mvc
dotnet publish
```

<span data-ttu-id="c48f0-147">`dotnet publish` 会生成以下输出的变体：</span><span class="sxs-lookup"><span data-stu-id="c48f0-147">The `dotnet publish` command produces a variation of the following output:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {VERSION} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\{TARGET FRAMEWORK MONIKER}\publish\
```

<span data-ttu-id="c48f0-148">默认发布文件夹格式为 bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\。</span><span class="sxs-lookup"><span data-stu-id="c48f0-148">The default publish folder format is *bin\Debug\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="c48f0-149">例如 bin\Debug\netcoreapp2.2\publish\\。</span><span class="sxs-lookup"><span data-stu-id="c48f0-149">For example, *bin\Debug\netcoreapp2.2\publish\\*.</span></span>

<span data-ttu-id="c48f0-150">以下命令指定 `Release` 生成和发布目录：</span><span class="sxs-lookup"><span data-stu-id="c48f0-150">The following command specifies a `Release` build and the publishing directory:</span></span>

```dotnetcli
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="c48f0-151">`dotnet publish` 命令调用 MSBuild，后者会调用 `Publish` 目标。</span><span class="sxs-lookup"><span data-stu-id="c48f0-151">The `dotnet publish` command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="c48f0-152">任何传递给 `dotnet publish` 的参数都将传递给 MSBuild。</span><span class="sxs-lookup"><span data-stu-id="c48f0-152">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="c48f0-153">`-c` 和 `-o` 参数分别映射到 MSBuild 的 `Configuration` 和 `OutputPath` 属性。</span><span class="sxs-lookup"><span data-stu-id="c48f0-153">The `-c` and `-o` parameters map to MSBuild's `Configuration` and `OutputPath` properties, respectively.</span></span>

<span data-ttu-id="c48f0-154">可以使用以下任一格式来传递 MSBuild 属性：</span><span class="sxs-lookup"><span data-stu-id="c48f0-154">MSBuild properties can be passed using either of the following formats:</span></span>

* `-p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="c48f0-155">例如，以下命令将 `Release` 版本发布到网络共享。</span><span class="sxs-lookup"><span data-stu-id="c48f0-155">For example, the following command publishes a `Release` build to a network share.</span></span> <span data-ttu-id="c48f0-156">网络共享通过正斜杠指定 (//r8/) 并适用于所有支持 .NET Core 的平台。</span><span class="sxs-lookup"><span data-stu-id="c48f0-156">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

```dotnetcli
dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb
```

<span data-ttu-id="c48f0-157">确认用于部署的发布应用未在运行。</span><span class="sxs-lookup"><span data-stu-id="c48f0-157">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="c48f0-158">如果应用正在运行，publish 文件夹中的文件会被锁定。</span><span class="sxs-lookup"><span data-stu-id="c48f0-158">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="c48f0-159">部署不会发生，因为无法复制锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-159">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="c48f0-160">发布配置文件</span><span class="sxs-lookup"><span data-stu-id="c48f0-160">Publish profiles</span></span>

<span data-ttu-id="c48f0-161">本部分使用 Visual Studio 2019 或更高版本来创建发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-161">This section uses Visual Studio 2019 or later to create a publishing profile.</span></span> <span data-ttu-id="c48f0-162">创建配置文件后，可以从 Visual Studio 或命令行进行发布。</span><span class="sxs-lookup"><span data-stu-id="c48f0-162">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span> <span data-ttu-id="c48f0-163">发布配置文件可以简化发布过程，并且可以存在任意数量的配置文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-163">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span>

<span data-ttu-id="c48f0-164">通过选择以下路径之一在 Visual Studio 中创建发布配置文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-164">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="c48f0-165">在“解决方案资源管理器”中，右键单击该项目并选择“发布”。</span><span class="sxs-lookup"><span data-stu-id="c48f0-165">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="c48f0-166">可以从“生成”菜单中选择“发布 {项目名称}” 。</span><span class="sxs-lookup"><span data-stu-id="c48f0-166">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="c48f0-167">随即显示应用程序容量页的“发布”选项卡。</span><span class="sxs-lookup"><span data-stu-id="c48f0-167">The **Publish** tab of the app capabilities page is displayed.</span></span> <span data-ttu-id="c48f0-168">如果项目缺少发布配置文件，将显示“选择发布目标”页面。</span><span class="sxs-lookup"><span data-stu-id="c48f0-168">If the project lacks a publish profile, the **Pick a publish target** page is displayed.</span></span> <span data-ttu-id="c48f0-169">系统会要求选择下述发布目标之一：</span><span class="sxs-lookup"><span data-stu-id="c48f0-169">You're asked to select one of the following publish targets:</span></span>

* <span data-ttu-id="c48f0-170">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="c48f0-170">Azure App Service</span></span>
* <span data-ttu-id="c48f0-171">Linux 上的 Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="c48f0-171">Azure App Service on Linux</span></span>
* <span data-ttu-id="c48f0-172">Azure 虚拟机</span><span class="sxs-lookup"><span data-stu-id="c48f0-172">Azure Virtual Machines</span></span>
* <span data-ttu-id="c48f0-173">文件夹</span><span class="sxs-lookup"><span data-stu-id="c48f0-173">Folder</span></span>
* <span data-ttu-id="c48f0-174">IIS、FTP、Web 部署（面向任何 Web 服务器）</span><span class="sxs-lookup"><span data-stu-id="c48f0-174">IIS, FTP, Web Deploy (for any web server)</span></span>
* <span data-ttu-id="c48f0-175">导入配置文件</span><span class="sxs-lookup"><span data-stu-id="c48f0-175">Import Profile</span></span>

<span data-ttu-id="c48f0-176">要确定最适合的发布目标，请参阅[哪些发布选项适合我](/visualstudio/ide/not-in-toc/web-publish-options)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-176">To determine the most appropriate publish target, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="c48f0-177">当选择“文件夹”发布目标时，指定一个文件夹路径来存储发布的资产。</span><span class="sxs-lookup"><span data-stu-id="c48f0-177">When the **Folder** publish target is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="c48f0-178">默认文件夹路径为 bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\。</span><span class="sxs-lookup"><span data-stu-id="c48f0-178">The default folder path is *bin\\{PROJECT CONFIGURATION}\\{TARGET FRAMEWORK MONIKER}\publish\\*.</span></span> <span data-ttu-id="c48f0-179">例如 bin\Release\netcoreapp2.2\publish\\。</span><span class="sxs-lookup"><span data-stu-id="c48f0-179">For example, *bin\Release\netcoreapp2.2\publish\\*.</span></span> <span data-ttu-id="c48f0-180">选择“创建配置文件”按钮完成操作。</span><span class="sxs-lookup"><span data-stu-id="c48f0-180">Select the **Create Profile** button to finish.</span></span>

<span data-ttu-id="c48f0-181">创建发布配置文件后，“发布”选项卡的内容将更改。</span><span class="sxs-lookup"><span data-stu-id="c48f0-181">Once a publish profile is created, the **Publish** tab's content changes.</span></span> <span data-ttu-id="c48f0-182">新创建的配置文件显示在下拉列表中。</span><span class="sxs-lookup"><span data-stu-id="c48f0-182">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="c48f0-183">在下拉列表中，选择“创建新配置文件”，再新建一个配置文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-183">Below the drop-down list, select **Create new profile** to create another new profile.</span></span>

<span data-ttu-id="c48f0-184">Visual Studio 的生成工具会生成一个 Properties/PublishProfiles/{PROFILE NAME}.pubxml MSBuild 文件，它描述了发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-184">Visual Studio's publish tool produces a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file describing the publish profile.</span></span> <span data-ttu-id="c48f0-185">.pubxml 文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-185">The *.pubxml* file:</span></span>

* <span data-ttu-id="c48f0-186">包含发布配置设置并由发布过程使用。</span><span class="sxs-lookup"><span data-stu-id="c48f0-186">Contains publish configuration settings and is consumed by the publishing process.</span></span>
* <span data-ttu-id="c48f0-187">可更改它来自定义生成和发布过程。</span><span class="sxs-lookup"><span data-stu-id="c48f0-187">Can be modified to customize the build and publish process.</span></span>

<span data-ttu-id="c48f0-188">发布到 Azure 目标时，.pubxml 文件包含 Azure 订阅标识符。</span><span class="sxs-lookup"><span data-stu-id="c48f0-188">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="c48f0-189">不建议使用该目标类型将此文件添加到源代码管理。</span><span class="sxs-lookup"><span data-stu-id="c48f0-189">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="c48f0-190">发布到非 Azure 目标时，签入 .pubxml 文件是安全的。</span><span class="sxs-lookup"><span data-stu-id="c48f0-190">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="c48f0-191">敏感信息（如发布密码）在每个用户/机器级别均进行加密。</span><span class="sxs-lookup"><span data-stu-id="c48f0-191">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="c48f0-192">它存储在 Properties/PublishProfiles/{PROFILE NAME}.pubxml.user 文件中。</span><span class="sxs-lookup"><span data-stu-id="c48f0-192">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="c48f0-193">由于此文件可以存储敏感信息，因此不应将其签入源代码管理。</span><span class="sxs-lookup"><span data-stu-id="c48f0-193">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="c48f0-194">要简要了解如何发布 ASP.NET Core Web 应用，请参阅 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="c48f0-194">For an overview of how to publish an ASP.NET Core web app, see <xref:host-and-deploy/index>.</span></span> <span data-ttu-id="c48f0-195">发布 ASP.NET Core Web 应用所需的 MSBuild 任务和目标在 [dotnet/websdk repository](https://github.com/dotnet/websdk) 中是开放源代码的。</span><span class="sxs-lookup"><span data-stu-id="c48f0-195">The MSBuild tasks and targets necessary to publish an ASP.NET Core web app are open-source in the [dotnet/websdk repository](https://github.com/dotnet/websdk).</span></span>

<span data-ttu-id="c48f0-196">以下命令可使用文件夹、MSDeploy 和 [Kudu](https://github.com/projectkudu/kudu/wiki) 发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-196">The following commands can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles.</span></span> <span data-ttu-id="c48f0-197">MSDeploy 缺少跨平台支持，因此仅支持在 Windows 上使用以下 MSDeploy 选项。</span><span class="sxs-lookup"><span data-stu-id="c48f0-197">Because MSDeploy lacks cross-platform support, the following MSDeploy options are supported only on Windows.</span></span>

<span data-ttu-id="c48f0-198">文件夹（跨平台工作）：</span><span class="sxs-lookup"><span data-stu-id="c48f0-198">**Folder (works cross-platform):**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="c48f0-199">MSDeploy：</span><span class="sxs-lookup"><span data-stu-id="c48f0-199">**MSDeploy:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="c48f0-200">MSDeploy 包：</span><span class="sxs-lookup"><span data-stu-id="c48f0-200">**MSDeploy package:**</span></span>

```dotnetcli
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

```dotnetcli
dotnet build WebApplication.csproj /p:DeployOnBuild=true /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="c48f0-201">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="c48f0-201">In the preceding examples:</span></span>

* <span data-ttu-id="c48f0-202">`dotnet publish` 和 `dotnet build` 支持 Kudu API 从任何平台发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="c48f0-202">`dotnet publish` and `dotnet build` support Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="c48f0-203">Visual Studio 发布支持 Kudu API，但 WebSDK 支持其跨平台发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="c48f0-203">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>
* <span data-ttu-id="c48f0-204">不要将 `DeployOnBuild` 传递到 `dotnet publish` 命令。</span><span class="sxs-lookup"><span data-stu-id="c48f0-204">Don't pass `DeployOnBuild` to the `dotnet publish` command.</span></span>

<span data-ttu-id="c48f0-205">有关详细信息，请参阅 [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-205">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/dotnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="c48f0-206">向项目的 Properties/PublishProfiles 文件夹添加包含以下内容的发布配置文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-206">Add a publish profile to the project's *Properties/PublishProfiles* folder with the following content:</span></span>

```xml
<Project>
  <PropertyGroup>
    <PublishProtocol>Kudu</PublishProtocol>
    <PublishSiteName>nodewebapp</PublishSiteName>
    <UserName>username</UserName>
    <Password>password</Password>
  </PropertyGroup>
</Project>
```

## <a name="folder-publish-example"></a><span data-ttu-id="c48f0-207">文件夹发布示例</span><span class="sxs-lookup"><span data-stu-id="c48f0-207">Folder publish example</span></span>

<span data-ttu-id="c48f0-208">当使用名为 FolderProfile 的配置文件发布时，请使用以下命令之一：</span><span class="sxs-lookup"><span data-stu-id="c48f0-208">When publishing with a profile named *FolderProfile*, use any of the following commands:</span></span>

```dotnetcli
dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
```

```dotnetcli
dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

```bash
msbuild /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="c48f0-209">.NET Core CLI 的 [dotnet build](/dotnet/core/tools/dotnet-build) 命令会调用 `msbuild` 来运行生成和发布过程。</span><span class="sxs-lookup"><span data-stu-id="c48f0-209">The .NET Core CLI's [dotnet build](/dotnet/core/tools/dotnet-build) command calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="c48f0-210">在文件夹配置文件中传递时，`dotnet build` 和 `msbuild` 命令作用相同。</span><span class="sxs-lookup"><span data-stu-id="c48f0-210">The `dotnet build` and `msbuild` commands are equivalent when passing in a folder profile.</span></span> <span data-ttu-id="c48f0-211">在 Windows 上直接调用 `msbuild` 时，将使用 MSBuild 的 .NET Framework 版本。</span><span class="sxs-lookup"><span data-stu-id="c48f0-211">When calling `msbuild` directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="c48f0-212">在非文件夹配置文件上调用 `dotnet build`：</span><span class="sxs-lookup"><span data-stu-id="c48f0-212">Calling `dotnet build` on a non-folder profile:</span></span>

* <span data-ttu-id="c48f0-213">调用 `msbuild`后者使用 MSDeploy。</span><span class="sxs-lookup"><span data-stu-id="c48f0-213">Invokes `msbuild`, which uses MSDeploy.</span></span>
* <span data-ttu-id="c48f0-214">结果失败（即使在 Windows 上运行时）。</span><span class="sxs-lookup"><span data-stu-id="c48f0-214">Results in a failure (even when running on Windows).</span></span> <span data-ttu-id="c48f0-215">要使用非文件夹配置文件发布，请直接调用 `msbuild`。</span><span class="sxs-lookup"><span data-stu-id="c48f0-215">To publish with a non-folder profile, call `msbuild` directly.</span></span>

<span data-ttu-id="c48f0-216">以下文件夹发布配置文件通过 Visual Studio 创建，并被发布到网络共享：</span><span class="sxs-lookup"><span data-stu-id="c48f0-216">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

```xml
<?xml version="1.0" encoding="utf-8"?>
<!--
This file is used by the publish/package process of your Web project.
You can customize the behavior of this process by editing this 
MSBuild file.
-->
<Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <PropertyGroup>
    <WebPublishMethod>FileSystem</WebPublishMethod>
    <PublishProvider>FileSystem</PublishProvider>
    <LastUsedBuildConfiguration>Release</LastUsedBuildConfiguration>
    <LastUsedPlatform>Any CPU</LastUsedPlatform>
    <SiteUrlToLaunchAfterPublish />
    <LaunchSiteAfterPublish>True</LaunchSiteAfterPublish>
    <ExcludeApp_Data>False</ExcludeApp_Data>
    <PublishFramework>netcoreapp1.1</PublishFramework>
    <ProjectGuid>c30c453c-312e-40c4-aec9-394a145dee0b</ProjectGuid>
    <publishUrl>\\r8\Release\AdminWeb</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
</Project>
```

<span data-ttu-id="c48f0-217">在上面的示例中：</span><span class="sxs-lookup"><span data-stu-id="c48f0-217">In the preceding example:</span></span>

* <span data-ttu-id="c48f0-218">`<ExcludeApp_Data>` 属性仅为满足 XML 架构要求。</span><span class="sxs-lookup"><span data-stu-id="c48f0-218">The `<ExcludeApp_Data>` property is present merely to satisfy an XML schema requirement.</span></span> <span data-ttu-id="c48f0-219">`<ExcludeApp_Data>` 属性不影响发布过程，即使项目根中存在 App_Data 文件夹也是如此。</span><span class="sxs-lookup"><span data-stu-id="c48f0-219">The `<ExcludeApp_Data>` property has no effect on the publish process, even if there's an *App_Data* folder in the project root.</span></span> <span data-ttu-id="c48f0-220">App_Data 文件夹不会像在 ASP.NET 4.x 项目中那样得到特殊对待。</span><span class="sxs-lookup"><span data-stu-id="c48f0-220">The *App_Data* folder doesn't receive special treatment as it does in ASP.NET 4.x projects.</span></span>
* <span data-ttu-id="c48f0-221">`<LastUsedBuildConfiguration>` 属性设置为 `Release`。</span><span class="sxs-lookup"><span data-stu-id="c48f0-221">The `<LastUsedBuildConfiguration>` property is set to `Release`.</span></span> <span data-ttu-id="c48f0-222">从 Visual Studio 发布时，启动发布过程后将使用该值设置 `<LastUsedBuildConfiguration>` 的值。</span><span class="sxs-lookup"><span data-stu-id="c48f0-222">When publishing from Visual Studio, the value of `<LastUsedBuildConfiguration>` is set using the value when the publish process is started.</span></span> <span data-ttu-id="c48f0-223">`<LastUsedBuildConfiguration>` 比较特殊，不得在导入的 MSBuild 文件中覆盖它。</span><span class="sxs-lookup"><span data-stu-id="c48f0-223">`<LastUsedBuildConfiguration>` is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="c48f0-224">但是，可通过下述方法之一在命令行中覆盖此属性。</span><span class="sxs-lookup"><span data-stu-id="c48f0-224">This property can, however, be overridden from the command line using one of the following approaches.</span></span>
  * <span data-ttu-id="c48f0-225">使用 .NET Core CLI：</span><span class="sxs-lookup"><span data-stu-id="c48f0-225">Using the .NET Core CLI:</span></span>

    ```dotnetcli
    dotnet publish /p:Configuration=Release /p:PublishProfile=FolderProfile
    ```

    ```dotnetcli
    dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  * <span data-ttu-id="c48f0-226">使用 MSBuild：</span><span class="sxs-lookup"><span data-stu-id="c48f0-226">Using MSBuild:</span></span>

    ```bash
    msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
    ```

  <span data-ttu-id="c48f0-227">有关详细信息，请参阅 [MSBuild：如何设置配置属性](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-227">For more information, see [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx).</span></span>

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="c48f0-228">从命令行发布到 MSDeploy 终结点</span><span class="sxs-lookup"><span data-stu-id="c48f0-228">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="c48f0-229">下面的示例使用由 Visual Studio 创建的 ASP.NET Core Web 应用，名为 AzureWebApp。</span><span class="sxs-lookup"><span data-stu-id="c48f0-229">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="c48f0-230">通过 Visual Studio 添加 Azure 应用发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-230">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="c48f0-231">有关如何创建配置文件的详细信息，请参阅[发布配置文件](#publish-profiles)部分。</span><span class="sxs-lookup"><span data-stu-id="c48f0-231">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="c48f0-232">若要使用发布配置文件部署应用，请在 Visual Studio 开发人员命令提示中执行 `msbuild` 命令。</span><span class="sxs-lookup"><span data-stu-id="c48f0-232">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="c48f0-233">Windows 任务栏上的“开始”菜单的“Visual Studio”文件夹中提供命令提示符。</span><span class="sxs-lookup"><span data-stu-id="c48f0-233">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="c48f0-234">为了便于访问，可将命令提示符添加到 Visual Studio 中的“工具”菜单中。</span><span class="sxs-lookup"><span data-stu-id="c48f0-234">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="c48f0-235">有关详细信息，请参阅 [Visual Studio 开发人员命令提示符](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-235">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="c48f0-236">MSBuild 使用以下命令语法：</span><span class="sxs-lookup"><span data-stu-id="c48f0-236">MSBuild uses the following command syntax:</span></span>

```bash
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="c48f0-237">`{PATH}`：应用的项目文件的路径。</span><span class="sxs-lookup"><span data-stu-id="c48f0-237">`{PATH}`: Path to the app's project file.</span></span>
* <span data-ttu-id="c48f0-238">`{PROFILE}`：发布配置文件的名称。</span><span class="sxs-lookup"><span data-stu-id="c48f0-238">`{PROFILE}`: Name of the publish profile.</span></span>
* <span data-ttu-id="c48f0-239">`{USERNAME}`：MSDeploy 用户名。</span><span class="sxs-lookup"><span data-stu-id="c48f0-239">`{USERNAME}`: MSDeploy username.</span></span> <span data-ttu-id="c48f0-240">可以在发布配置文件中找到 `{USERNAME}`。</span><span class="sxs-lookup"><span data-stu-id="c48f0-240">The `{USERNAME}` can be found in the publish profile.</span></span>
* <span data-ttu-id="c48f0-241">`{PASSWORD}`：MSDeploy 密码。</span><span class="sxs-lookup"><span data-stu-id="c48f0-241">`{PASSWORD}`: MSDeploy password.</span></span> <span data-ttu-id="c48f0-242">从 {PROFILE}.PublishSettings 文件中获取 `{PASSWORD}`。</span><span class="sxs-lookup"><span data-stu-id="c48f0-242">Obtain the `{PASSWORD}` from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="c48f0-243">可以从以下位置下载 .PublishSettings 文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-243">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="c48f0-244">解决方案资源管理器：选择“视图” > “Cloud Explorer” 。</span><span class="sxs-lookup"><span data-stu-id="c48f0-244">**Solution Explorer**: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="c48f0-245">连接你的 Azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="c48f0-245">Connect with your Azure subscription.</span></span> <span data-ttu-id="c48f0-246">打开“应用服务”。</span><span class="sxs-lookup"><span data-stu-id="c48f0-246">Open **App Services**.</span></span> <span data-ttu-id="c48f0-247">右键单击应用。</span><span class="sxs-lookup"><span data-stu-id="c48f0-247">Right-click the app.</span></span> <span data-ttu-id="c48f0-248">选择“下载发布配置文件”。</span><span class="sxs-lookup"><span data-stu-id="c48f0-248">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="c48f0-249">Azure 门户：选择 Web 应用“概述”面板上的“获取发布配置文件” 。</span><span class="sxs-lookup"><span data-stu-id="c48f0-249">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="c48f0-250">下面的示例使用名为“AzureWebApp - Web 部署”的发布配置文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-250">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```bash
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="c48f0-251">发布配置文件还可通过 Windows 命令行界面与 .NET Core CLI 的 [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 一起使用：</span><span class="sxs-lookup"><span data-stu-id="c48f0-251">A publish profile can also be used with the .NET Core CLI's [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command shell:</span></span>

```dotnetcli
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!IMPORTANT]
> <span data-ttu-id="c48f0-252">`dotnet msbuild` 命令是一个跨平台命令，可在 macOS 和 Linux 上编译 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c48f0-252">The `dotnet msbuild` command is a cross-platform command and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="c48f0-253">但是，macOS 和 Linux 上的 MSBuild 不能将应用部署到 Azure 或其他 MSDeploy 终结点。</span><span class="sxs-lookup"><span data-stu-id="c48f0-253">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoints.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="c48f0-254">设置环境</span><span class="sxs-lookup"><span data-stu-id="c48f0-254">Set the environment</span></span>

<span data-ttu-id="c48f0-255">将 `<EnvironmentName>` 属性包含在发布配置文件（.pubxml）或项目文件中，以设置应用的[环境](xref:fundamentals/environments)：</span><span class="sxs-lookup"><span data-stu-id="c48f0-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="c48f0-256">如果需要 web.config 转换（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="c48f0-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="c48f0-257">排除文件</span><span class="sxs-lookup"><span data-stu-id="c48f0-257">Exclude files</span></span>

<span data-ttu-id="c48f0-258">在发布 ASP.NET Core Web 应用时，包括以下资产：</span><span class="sxs-lookup"><span data-stu-id="c48f0-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="c48f0-259">生成工件</span><span class="sxs-lookup"><span data-stu-id="c48f0-259">Build artifacts</span></span>
* <span data-ttu-id="c48f0-260">与以下 glob 模式匹配的文件夹和文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="c48f0-261">`**\*.config`（例如，web.config）</span><span class="sxs-lookup"><span data-stu-id="c48f0-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="c48f0-262">`**\*.json`（例如，appsettings.json）</span><span class="sxs-lookup"><span data-stu-id="c48f0-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="c48f0-263">MSBuild 支持 [glob 模式](https://gruntjs.com/configuring-tasks#globbing-patterns)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="c48f0-264">例如，以下 `<Content>` 元素禁止在 wwwroot\content 文件夹及其子文件夹中复制文本 (.txt) 文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="c48f0-265">可以将上面的标记添加到发布配置文件或 .csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="c48f0-266">添加到 .csproj 文件时，会将该规则添加到项目中的所有发布配置文件中。</span><span class="sxs-lookup"><span data-stu-id="c48f0-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="c48f0-267">以下 `<MsDeploySkipRules>` 元素排除 wwwroot\content 文件夹中的所有文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="c48f0-268">`<MsDeploySkipRules>` 不会从部署站点删除跳过目标。</span><span class="sxs-lookup"><span data-stu-id="c48f0-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="c48f0-269">从部署站点中删除 `<Content>` 目标文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="c48f0-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="c48f0-270">例如，假设部署的 Web 应用具有以下文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="c48f0-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c48f0-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="c48f0-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c48f0-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="c48f0-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="c48f0-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="c48f0-274">如果添加了以下 `<MsDeploySkipRules>` 元素，则不会在部署站点上删除这些文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About1.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About2.cshtml</AbsolutePath>
  </MsDeploySkipRules>

  <MsDeploySkipRules Include="CustomSkipFile">
    <ObjectName>filePath</ObjectName>
    <AbsolutePath>Views\\Home\\About3.cshtml</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="c48f0-275">之前的 `<MsDeploySkipRules>` 元素阻止部署跳过的文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="c48f0-276">一旦部署这些文件就不会删除它们。</span><span class="sxs-lookup"><span data-stu-id="c48f0-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="c48f0-277">以下 `<Content>` 元素将在部署站点中删除目标文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="c48f0-278">如果将命令行部署和前面的 `<Content>` 元素一起使用，则将生成以下输出的变体：</span><span class="sxs-lookup"><span data-stu-id="c48f0-278">Using command-line deployment with the preceding `<Content>` element yields a variation of the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\{TARGET FRAMEWORK MONIKER}\PubTmp\Web1.SourceManifest.
  xml) to Destination: auto().
  Deleting file (Web11112\Views\Home\About1.cshtml).
  Deleting file (Web11112\Views\Home\About2.cshtml).
  Deleting file (Web11112\Views\Home\About3.cshtml).
  Updating file (Web11112\web.config).
  Updating file (Web11112\Web1.deps.json).
  Updating file (Web11112\Web1.dll).
  Updating file (Web11112\Web1.pdb).
  Updating file (Web11112\Web1.runtimeconfig.json).
  Successfully executed Web deployment task.
  Publish Succeeded.
Done Building Project "C:\Webs\Web1\Web1.csproj" (default targets).
```

## <a name="include-files"></a><span data-ttu-id="c48f0-279">包含文件</span><span class="sxs-lookup"><span data-stu-id="c48f0-279">Include files</span></span>

<span data-ttu-id="c48f0-280">下列各部分简要介绍了发布时用于包含文件的不同方法。</span><span class="sxs-lookup"><span data-stu-id="c48f0-280">The following sections outline different approaches for file inclusion at publish time.</span></span> <span data-ttu-id="c48f0-281">[常规文件包含](#general-file-inclusion)部分使用了 `DotNetPublishFiles` 项，后者由 [Web SDK](xref:razor-pages/web-sdk) 中的发布目标文件提供。</span><span class="sxs-lookup"><span data-stu-id="c48f0-281">The [General file inclusion](#general-file-inclusion) section uses the `DotNetPublishFiles` item, which is provided by a publish targets file in the [Web SDK](xref:razor-pages/web-sdk).</span></span> <span data-ttu-id="c48f0-282">[选择性文件包含](#selective-file-inclusion)部分使用了 `ResolvedFileToPublish` 项，它由 [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props) 中的发布目标文件提供。</span><span class="sxs-lookup"><span data-stu-id="c48f0-282">The [Selective file inclusion](#selective-file-inclusion) section uses the `ResolvedFileToPublish` item, which is provided by a publish targets file in the [.NET Core SDK](/dotnet/core/project-sdk/msbuild-props).</span></span> <span data-ttu-id="c48f0-283">Web SDK 依赖于 .NET Core SDK，因此上述任一项都可在 ASP.NET Core 项目中使用。</span><span class="sxs-lookup"><span data-stu-id="c48f0-283">Because the Web SDK depends on the .NET Core SDK, either item can be used in an ASP.NET Core project.</span></span>

### <a name="general-file-inclusion"></a><span data-ttu-id="c48f0-284">常规文件包含</span><span class="sxs-lookup"><span data-stu-id="c48f0-284">General file inclusion</span></span>

<span data-ttu-id="c48f0-285">下例中的 `<ItemGroup>` 元素展示了将位于项目目录之外的文件夹复制到已发布的站点的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="c48f0-285">The following example's `<ItemGroup>` element demonstrates copying a folder located outside of the project directory to a folder of the published site.</span></span> <span data-ttu-id="c48f0-286">添加到下述标记的 `<ItemGroup>` 的所有文件都将默认包含在内。</span><span class="sxs-lookup"><span data-stu-id="c48f0-286">Any files added to the following markup's `<ItemGroup>` are included by default.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotNetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotNetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="c48f0-287">前面的标记：</span><span class="sxs-lookup"><span data-stu-id="c48f0-287">The preceding markup:</span></span>

* <span data-ttu-id="c48f0-288">可以将标记添加到 .csproj 文件或发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-288">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="c48f0-289">如果将其添加到 .csproj 文件，它会包含在项目的每个发布配置文件中。</span><span class="sxs-lookup"><span data-stu-id="c48f0-289">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>
* <span data-ttu-id="c48f0-290">声明一个 `_CustomFiles` 项来存储与 `Include` 属性的 glob 模式匹配的文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-290">Declares a `_CustomFiles` item to store files matching the `Include` attribute's globbing pattern.</span></span> <span data-ttu-id="c48f0-291">模式中引用的“图像”文件夹未在项目目录中。</span><span class="sxs-lookup"><span data-stu-id="c48f0-291">The *images* folder referenced in the pattern is located outside of the project directory.</span></span> <span data-ttu-id="c48f0-292">名为 `$(MSBuildProjectDirectory)` 的[保留属性](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties)会解析为项目文件的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="c48f0-292">A [reserved property](/visualstudio/msbuild/msbuild-reserved-and-well-known-properties), named `$(MSBuildProjectDirectory)`, resolves to the project file's absolute path.</span></span>
* <span data-ttu-id="c48f0-293">向 `DotNetPublishFiles` 项提供一个文件列表。</span><span class="sxs-lookup"><span data-stu-id="c48f0-293">Provides a list of files to the `DotNetPublishFiles` item.</span></span> <span data-ttu-id="c48f0-294">默认情况下，该项的 `<DestinationRelativePath>` 元素为空。</span><span class="sxs-lookup"><span data-stu-id="c48f0-294">By default, the item's `<DestinationRelativePath>` element is empty.</span></span> <span data-ttu-id="c48f0-295">默认值在标记中重写，且使用 `%(RecursiveDir)` 等[常见的项元数据](/visualstudio/msbuild/msbuild-well-known-item-metadata)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-295">The default value is overridden in the markup and uses [well-known item metadata](/visualstudio/msbuild/msbuild-well-known-item-metadata) such as `%(RecursiveDir)`.</span></span> <span data-ttu-id="c48f0-296">内部文本表示已发布的站点的 wwwroot/images 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c48f0-296">The inner text represents the *wwwroot/images* folder of the published site.</span></span>

### <a name="selective-file-inclusion"></a><span data-ttu-id="c48f0-297">选择性文件包含</span><span class="sxs-lookup"><span data-stu-id="c48f0-297">Selective file inclusion</span></span>

<span data-ttu-id="c48f0-298">下例中突出显示的标记展示了：</span><span class="sxs-lookup"><span data-stu-id="c48f0-298">The highlighted markup in the following example demonstrates:</span></span>

* <span data-ttu-id="c48f0-299">将项目之外的文件复制到已发布的站点的 wwwroot 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c48f0-299">Copying a file located outside of the project into the published site's *wwwroot* folder.</span></span> <span data-ttu-id="c48f0-300">保留文件名 ReadMe2.md。</span><span class="sxs-lookup"><span data-stu-id="c48f0-300">The file name of *ReadMe2.md* is maintained.</span></span>
* <span data-ttu-id="c48f0-301">排除 wwwroot\Content 文件夹。</span><span class="sxs-lookup"><span data-stu-id="c48f0-301">Excluding the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="c48f0-302">排除 Views\Home\About2.cshtml。</span><span class="sxs-lookup"><span data-stu-id="c48f0-302">Excluding *Views\Home\About2.cshtml*.</span></span>

[!code-xml[](visual-studio-publish-profiles/samples/Web1.pubxml?highlight=18-23)]

<span data-ttu-id="c48f0-303">前述示例使用 `ResolvedFileToPublish` 项，其默认行为是始终将 `Include` 中提供的文件复制到已发布的站点中。</span><span class="sxs-lookup"><span data-stu-id="c48f0-303">The preceding example uses the `ResolvedFileToPublish` item, whose default behavior is to always copy the files provided in the `Include` attribute to the published site.</span></span> <span data-ttu-id="c48f0-304">通过 `Never` 或 `PreserveNewest` 的内部文本包含 `<CopyToPublishDirectory>`覆盖默认行为。</span><span class="sxs-lookup"><span data-stu-id="c48f0-304">Override the default behavior by including a `<CopyToPublishDirectory>` child element with inner text of either `Never` or `PreserveNewest`.</span></span> <span data-ttu-id="c48f0-305">例如：</span><span class="sxs-lookup"><span data-stu-id="c48f0-305">For example:</span></span>

```xml
<ResolvedFileToPublish Include="..\ReadMe2.md">
  <RelativePath>wwwroot\ReadMe2.md</RelativePath>
  <CopyToPublishDirectory>PreserveNewest</CopyToPublishDirectory>
</ResolvedFileToPublish>
```

<span data-ttu-id="c48f0-306">有关更多部署示例，请参阅 [Web SDK README 文件](https://github.com/dotnet/sdk/tree/master/src/WebSdk)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-306">For more deployment samples, see the [Web SDK README file](https://github.com/dotnet/sdk/tree/master/src/WebSdk).</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="c48f0-307">在发布前或发布后运行目标</span><span class="sxs-lookup"><span data-stu-id="c48f0-307">Run a target before or after publishing</span></span>

<span data-ttu-id="c48f0-308">内置 `BeforePublish` 和 `AfterPublish` 目标在发布目标前/后执行目标。</span><span class="sxs-lookup"><span data-stu-id="c48f0-308">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="c48f0-309">将以下元素添加到发布配置文件，以在发布前/后均记录控制台消息：</span><span class="sxs-lookup"><span data-stu-id="c48f0-309">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="c48f0-310">使用不受信任的证书发布到服务器</span><span class="sxs-lookup"><span data-stu-id="c48f0-310">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="c48f0-311">将值为 `True` 的 `<AllowUntrustedCertificate>` 属性添加到发布配置文件：</span><span class="sxs-lookup"><span data-stu-id="c48f0-311">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="c48f0-312">Kudu 服务</span><span class="sxs-lookup"><span data-stu-id="c48f0-312">The Kudu service</span></span>

<span data-ttu-id="c48f0-313">要查看 Azure 应用服务 Web 应用部署中的文件，请使用 [Kudu 服务](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)。</span><span class="sxs-lookup"><span data-stu-id="c48f0-313">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="c48f0-314">将 `scm` 令牌追加到 Web 应用名称。</span><span class="sxs-lookup"><span data-stu-id="c48f0-314">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="c48f0-315">例如：</span><span class="sxs-lookup"><span data-stu-id="c48f0-315">For example:</span></span>

| <span data-ttu-id="c48f0-316">URL</span><span class="sxs-lookup"><span data-stu-id="c48f0-316">URL</span></span>                                    | <span data-ttu-id="c48f0-317">结果</span><span class="sxs-lookup"><span data-stu-id="c48f0-317">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="c48f0-318">Web 应用</span><span class="sxs-lookup"><span data-stu-id="c48f0-318">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="c48f0-319">Kudu 服务</span><span class="sxs-lookup"><span data-stu-id="c48f0-319">Kudu service</span></span> |

<span data-ttu-id="c48f0-320">选择[调试控制台](https://github.com/projectkudu/kudu/wiki/Kudu-console)菜单项来查看、编辑、删除或添加文件。</span><span class="sxs-lookup"><span data-stu-id="c48f0-320">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="c48f0-321">其他资源</span><span class="sxs-lookup"><span data-stu-id="c48f0-321">Additional resources</span></span>

* <span data-ttu-id="c48f0-322">[Web 部署](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) 简化了 Web 应用和网站到 IIS 服务器的部署。</span><span class="sxs-lookup"><span data-stu-id="c48f0-322">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="c48f0-323">[Web SDK GitHub 存储库](https://github.com/dotnet/websdk/issues)：文件问题和部署的请求功能。</span><span class="sxs-lookup"><span data-stu-id="c48f0-323">[Web SDK GitHub repository](https://github.com/dotnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="c48f0-324">从 Visual Studio 将 ASP.NET Web 应用发布到 Azure VM</span><span class="sxs-lookup"><span data-stu-id="c48f0-324">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
