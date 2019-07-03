---
title: 用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件
author: rick-anderson
description: 了解如何在 Visual Studio 中创建发布配置文件，并将它们用于管理针对各种目标的 ASP.NET Core 应用部署。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/18/2019
uid: host-and-deploy/visual-studio-publish-profiles
ms.openlocfilehash: ac243a3898553b2e14a6c15d311afaf62f112a24
ms.sourcegitcommit: a1283d486ac1dcedfc7ea302e1cc882833e2c515
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67207813"
---
# <a name="visual-studio-publish-profiles-for-aspnet-core-app-deployment"></a><span data-ttu-id="1b726-103">用于 ASP.NET Core 应用部署的 Visual Studio 发布配置文件</span><span class="sxs-lookup"><span data-stu-id="1b726-103">Visual Studio publish profiles for ASP.NET Core app deployment</span></span>

<span data-ttu-id="1b726-104">作者：[Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1b726-104">By [Sayed Ibrahim Hashimi](https://github.com/sayedihashimi) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="1b726-105">本文档重点介绍如何使用 Visual Studio 2017 或更高版本创建并使用发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-105">This document focuses on using Visual Studio 2017 or later to create and use publish profiles.</span></span> <span data-ttu-id="1b726-106">可以从 MSBuild 和 Visual Studio 运行使用 Visual Studio 创建的发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-106">The publish profiles created with Visual Studio can be run from MSBuild and Visual Studio.</span></span> <span data-ttu-id="1b726-107">有关发布到 Azure 的说明，请参阅[使用 Visual Studio 将 ASP.NET Core Web 应用发布到 Azure App Service](xref:tutorials/publish-to-azure-webapp-using-vs)。</span><span class="sxs-lookup"><span data-stu-id="1b726-107">See [Publish an ASP.NET Core web app to Azure App Service using Visual Studio](xref:tutorials/publish-to-azure-webapp-using-vs) for instructions on publishing to Azure.</span></span>

<span data-ttu-id="1b726-108">`dotnet new mvc` 命令生成包含以下顶级 `<Project>` 元素的项目文件：</span><span class="sxs-lookup"><span data-stu-id="1b726-108">The `dotnet new mvc` command produces a project file containing the following top-level `<Project>` element:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    <!-- omitted for brevity -->
</Project>
```

<span data-ttu-id="1b726-109">前导 `<Project>` 元素的 `Sdk` 属性分别从 $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props  和 $(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets  导入 MSBuild [属性](/visualstudio/msbuild/msbuild-properties)和[目标](/visualstudio/msbuild/msbuild-targets)。</span><span class="sxs-lookup"><span data-stu-id="1b726-109">The preceding `<Project>` element's `Sdk` attribute imports the MSBuild [properties](/visualstudio/msbuild/msbuild-properties) and [targets](/visualstudio/msbuild/msbuild-targets) from *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.props* and *$(MSBuildSDKsPath)\Microsoft.NET.Sdk.Web\Sdk\Sdk.targets*, respectively.</span></span> <span data-ttu-id="1b726-110">`$(MSBuildSDKsPath)`（装有 Visual Studio 2019 Enterprise）的默认位置是 %programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1b726-110">The default location for `$(MSBuildSDKsPath)` (with Visual Studio 2019 Enterprise) is the *%programfiles(x86)%\Microsoft Visual Studio\2019\Enterprise\MSBuild\Sdks* folder.</span></span>

<span data-ttu-id="1b726-111">`Microsoft.NET.Sdk.Web` (Web SDK) 依赖于其他 SDK，包括 `Microsoft.NET.Sdk` (.NET Core SDK) 和 `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk))。</span><span class="sxs-lookup"><span data-stu-id="1b726-111">`Microsoft.NET.Sdk.Web` (Web SDK) depends on other SDKs, including `Microsoft.NET.Sdk` (.NET Core SDK) and `Microsoft.NET.Sdk.Razor` ([Razor SDK](xref:razor-pages/sdk)).</span></span> <span data-ttu-id="1b726-112">将导入与每个从属 SDK 关联的 MSBuild 属性和目标。</span><span class="sxs-lookup"><span data-stu-id="1b726-112">The MSBuild properties and targets associated with each dependent SDK are imported.</span></span> <span data-ttu-id="1b726-113">发布目标将根据使用的发布方法，导入相应的目标集。</span><span class="sxs-lookup"><span data-stu-id="1b726-113">Publish targets import the appropriate set of targets based on the publish method used.</span></span>

<span data-ttu-id="1b726-114">MSBuild 或 Visual Studio 加载项目时，执行下列高级别操作：</span><span class="sxs-lookup"><span data-stu-id="1b726-114">When MSBuild or Visual Studio loads a project, the following high-level actions occur:</span></span>

* <span data-ttu-id="1b726-115">生成项目</span><span class="sxs-lookup"><span data-stu-id="1b726-115">Build project</span></span>
* <span data-ttu-id="1b726-116">计算要发布的文件</span><span class="sxs-lookup"><span data-stu-id="1b726-116">Compute files to publish</span></span>
* <span data-ttu-id="1b726-117">将文件发布到目标</span><span class="sxs-lookup"><span data-stu-id="1b726-117">Publish files to destination</span></span>

## <a name="compute-project-items"></a><span data-ttu-id="1b726-118">计算项目项</span><span class="sxs-lookup"><span data-stu-id="1b726-118">Compute project items</span></span>

<span data-ttu-id="1b726-119">加载项目时，将计算 [MSBuild 项目项](/visualstudio/msbuild/common-msbuild-project-items)（文件）。</span><span class="sxs-lookup"><span data-stu-id="1b726-119">When the project is loaded, the [MSBuild project items](/visualstudio/msbuild/common-msbuild-project-items) (files) are computed.</span></span> <span data-ttu-id="1b726-120">项类型确定如何处理该文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-120">The item type determines how the file is processed.</span></span> <span data-ttu-id="1b726-121">默认情况下，.cs  文件包含在 `Compile` 项列表内。</span><span class="sxs-lookup"><span data-stu-id="1b726-121">By default, *.cs* files are included in the `Compile` item list.</span></span> <span data-ttu-id="1b726-122">会对 `Compile` 项列表中的文件进行编译。</span><span class="sxs-lookup"><span data-stu-id="1b726-122">Files in the `Compile` item list are compiled.</span></span>

<span data-ttu-id="1b726-123">除了生成输出，`Content` 项列表还包含已发布的文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-123">The `Content` item list contains files that are published in addition to the build outputs.</span></span> <span data-ttu-id="1b726-124">默认情况下，匹配模式 `wwwroot\**`、`**\*.config` 和 `**\*.json` 的文件包含在 `Content` 项列表内。</span><span class="sxs-lookup"><span data-stu-id="1b726-124">By default, files matching the patterns `wwwroot\**`, `**\*.config`, and `**\*.json` are included in the `Content` item list.</span></span> <span data-ttu-id="1b726-125">例如，`wwwroot\**` [glob 模式](https://gruntjs.com/configuring-tasks#globbing-patterns)匹配 wwwroot  文件夹  及其子文件夹中的所有文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-125">For example, the `wwwroot\**` [globbing pattern](https://gruntjs.com/configuring-tasks#globbing-patterns) matches all files in the *wwwroot* folder **and** its subfolders.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1b726-126">Web SDK 导入 [Razor SDK](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="1b726-126">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="1b726-127">因此，匹配模式 `**\*.cshtml` 和 `**\*.razor` 的文件也同时包含在 `Content` 项列表内。</span><span class="sxs-lookup"><span data-stu-id="1b726-127">As a result, files matching the patterns `**\*.cshtml` and `**\*.razor` are also included in the `Content` item list.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.1 <= aspnetcore-2.2"

<span data-ttu-id="1b726-128">Web SDK 导入 [Razor SDK](xref:razor-pages/sdk)。</span><span class="sxs-lookup"><span data-stu-id="1b726-128">The Web SDK imports the [Razor SDK](xref:razor-pages/sdk).</span></span> <span data-ttu-id="1b726-129">因此，匹配 `**\*.cshtml` 模式的文件也同时包含在 `Content` 项列表内。</span><span class="sxs-lookup"><span data-stu-id="1b726-129">As a result, files matching the `**\*.cshtml` pattern are also included in the `Content` item list.</span></span>

::: moniker-end

<span data-ttu-id="1b726-130">若要将文件显式添加到发布列表，请直接在 .csproj  文件中添加文件，如[包含文件](#include-files)一节中所示。</span><span class="sxs-lookup"><span data-stu-id="1b726-130">To explicitly add a file to the publish list, add the file directly in the *.csproj* file as shown in the [Include Files](#include-files) section.</span></span>

<span data-ttu-id="1b726-131">在 Visual Studio 中选择“发布”  按钮时或从命令行发布时：</span><span class="sxs-lookup"><span data-stu-id="1b726-131">When selecting the **Publish** button in Visual Studio or when publishing from the command line:</span></span>

* <span data-ttu-id="1b726-132">计算属性/项目（需要生成的文件）。</span><span class="sxs-lookup"><span data-stu-id="1b726-132">The properties/items are computed (the files that are needed to build).</span></span>
* <span data-ttu-id="1b726-133">**仅限 Visual Studio**：NuGet 包已还原。</span><span class="sxs-lookup"><span data-stu-id="1b726-133">**Visual Studio only**: NuGet packages are restored.</span></span> <span data-ttu-id="1b726-134">（用户需要在 CLI 上执行显式还原。）</span><span class="sxs-lookup"><span data-stu-id="1b726-134">(Restore needs to be explicit by the user on the CLI.)</span></span>
* <span data-ttu-id="1b726-135">生成项目。</span><span class="sxs-lookup"><span data-stu-id="1b726-135">The project builds.</span></span>
* <span data-ttu-id="1b726-136">计算发布项（需要发布的文件）。</span><span class="sxs-lookup"><span data-stu-id="1b726-136">The publish items are computed (the files that are needed to publish).</span></span>
* <span data-ttu-id="1b726-137">文件已发布（计算的文件将被复制到发布目标）。</span><span class="sxs-lookup"><span data-stu-id="1b726-137">The project is published (the computed files are copied to the publish destination).</span></span>

<span data-ttu-id="1b726-138">当 ASP.NET Core 项目在项目文件中引用 `Microsoft.NET.Sdk.Web` 时，会将 app_offline.htm  文件放在 Web 应用目录的根目录下。</span><span class="sxs-lookup"><span data-stu-id="1b726-138">When an ASP.NET Core project references `Microsoft.NET.Sdk.Web` in the project file, an *app_offline.htm* file is placed at the root of the web app directory.</span></span> <span data-ttu-id="1b726-139">该文件存在时，ASP.NET Core 模块会在部署过程中正常关闭该应用并提供 app_offline.htm 文件  。</span><span class="sxs-lookup"><span data-stu-id="1b726-139">When the file is present, the ASP.NET Core Module gracefully shuts down the app and serves the *app_offline.htm* file during the deployment.</span></span> <span data-ttu-id="1b726-140">有关详细信息，请参阅 [ASP.NET Core 模块配置参考](xref:host-and-deploy/aspnet-core-module#app_offlinehtm)。</span><span class="sxs-lookup"><span data-stu-id="1b726-140">For more information, see the [ASP.NET Core Module configuration reference](xref:host-and-deploy/aspnet-core-module#app_offlinehtm).</span></span>

## <a name="basic-command-line-publishing"></a><span data-ttu-id="1b726-141">基本命令行发布</span><span class="sxs-lookup"><span data-stu-id="1b726-141">Basic command-line publishing</span></span>

<span data-ttu-id="1b726-142">命令行发布适用于所有支持 .NET Core 的平台，而且不需要 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="1b726-142">Command-line publishing works on all .NET Core-supported platforms and doesn't require Visual Studio.</span></span> <span data-ttu-id="1b726-143">在下面的示例中，从项目目录（其中包含 .csproj  文件）运行 [dotnet publish](/dotnet/core/tools/dotnet-publish) 命令。</span><span class="sxs-lookup"><span data-stu-id="1b726-143">In the following samples, the [dotnet publish](/dotnet/core/tools/dotnet-publish) command is run from the project directory (which contains the *.csproj* file).</span></span> <span data-ttu-id="1b726-144">如果不在项目文件夹中，则可以在项目文件路径中显式传递。</span><span class="sxs-lookup"><span data-stu-id="1b726-144">If not in the project folder, explicitly pass in the project file path.</span></span> <span data-ttu-id="1b726-145">例如:</span><span class="sxs-lookup"><span data-stu-id="1b726-145">For example:</span></span>

```console
dotnet publish C:\Webs\Web1
```

<span data-ttu-id="1b726-146">运行以下命令以创建并发布 Web 应用：</span><span class="sxs-lookup"><span data-stu-id="1b726-146">Run the following commands to create and publish a web app:</span></span>

```console
dotnet new mvc
dotnet publish
```

<span data-ttu-id="1b726-147">[dotnet publish](/dotnet/core/tools/dotnet-publish) 命令生成类似下面的输出：</span><span class="sxs-lookup"><span data-stu-id="1b726-147">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command produces output similar to the following:</span></span>

```console
C:\Webs\Web1>dotnet publish
Microsoft (R) Build Engine version {version} for .NET Core
Copyright (C) Microsoft Corporation. All rights reserved.

  Restore completed in 36.81 ms for C:\Webs\Web1\Web1.csproj.
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\Web1.Views.dll
  Web1 -> C:\Webs\Web1\bin\Debug\netcoreapp{X.Y}\publish\
```

<span data-ttu-id="1b726-148">默认发布文件夹为 `bin\$(Configuration)\netcoreapp<version>\publish`。</span><span class="sxs-lookup"><span data-stu-id="1b726-148">The default publish folder is `bin\$(Configuration)\netcoreapp<version>\publish`.</span></span> <span data-ttu-id="1b726-149">`$(Configuration)` 的默认值为 Debug  。</span><span class="sxs-lookup"><span data-stu-id="1b726-149">The default for `$(Configuration)` is *Debug*.</span></span> <span data-ttu-id="1b726-150">在上述示例中，`<TargetFramework>` 是 `netcoreapp{X.Y}`。</span><span class="sxs-lookup"><span data-stu-id="1b726-150">In the preceding sample, the `<TargetFramework>` is `netcoreapp{X.Y}`.</span></span>

<span data-ttu-id="1b726-151">`dotnet publish -h` 显示用于发布的帮助信息。</span><span class="sxs-lookup"><span data-stu-id="1b726-151">`dotnet publish -h` displays help information for publish.</span></span>

<span data-ttu-id="1b726-152">以下命令指定 `Release` 生成和发布目录：</span><span class="sxs-lookup"><span data-stu-id="1b726-152">The following command specifies a `Release` build and the publishing directory:</span></span>

```console
dotnet publish -c Release -o C:\MyWebs\test
```

<span data-ttu-id="1b726-153">[dotnet publish](/dotnet/core/tools/dotnet-publish) 命令调用 MSBuild，它调用 `Publish` 目标。</span><span class="sxs-lookup"><span data-stu-id="1b726-153">The [dotnet publish](/dotnet/core/tools/dotnet-publish) command calls MSBuild, which invokes the `Publish` target.</span></span> <span data-ttu-id="1b726-154">任何传递给 `dotnet publish` 的参数都将传递给 MSBuild。</span><span class="sxs-lookup"><span data-stu-id="1b726-154">Any parameters passed to `dotnet publish` are passed to MSBuild.</span></span> <span data-ttu-id="1b726-155">`-c` 参数映射到 `Configuration` MSBuild 属性。</span><span class="sxs-lookup"><span data-stu-id="1b726-155">The `-c` parameter maps to the `Configuration` MSBuild property.</span></span> <span data-ttu-id="1b726-156">`-o` 参数映射到 `OutputPath`。</span><span class="sxs-lookup"><span data-stu-id="1b726-156">The `-o` parameter maps to `OutputPath`.</span></span>

<span data-ttu-id="1b726-157">可以使用以下任一格式来传递 MSBuild 属性：</span><span class="sxs-lookup"><span data-stu-id="1b726-157">MSBuild properties can be passed using either of the following formats:</span></span>

* `p:<NAME>=<VALUE>`
* `/p:<NAME>=<VALUE>`

<span data-ttu-id="1b726-158">以下命令将 `Release` 版本发布到网络共享：</span><span class="sxs-lookup"><span data-stu-id="1b726-158">The following command publishes a `Release` build to a network share:</span></span>

`dotnet publish -c Release /p:PublishDir=//r8/release/AdminWeb`

<span data-ttu-id="1b726-159">网络共享通过正斜杠指定 (//r8/  ) 并适用于所有支持 .NET Core 的平台。</span><span class="sxs-lookup"><span data-stu-id="1b726-159">The network share is specified with forward slashes (*//r8/*) and works on all .NET Core supported platforms.</span></span>

<span data-ttu-id="1b726-160">确认用于部署的发布应用未在运行。</span><span class="sxs-lookup"><span data-stu-id="1b726-160">Confirm that the published app for deployment isn't running.</span></span> <span data-ttu-id="1b726-161">如果应用正在运行，publish 文件夹中的文件会被锁定  。</span><span class="sxs-lookup"><span data-stu-id="1b726-161">Files in the *publish* folder are locked when the app is running.</span></span> <span data-ttu-id="1b726-162">部署不会发生，因为无法复制锁定的文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-162">Deployment can't occur because locked files can't be copied.</span></span>

## <a name="publish-profiles"></a><span data-ttu-id="1b726-163">发布配置文件</span><span class="sxs-lookup"><span data-stu-id="1b726-163">Publish profiles</span></span>

<span data-ttu-id="1b726-164">本部分使用 Visual Studio 2017 或更高版本创建发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-164">This section uses Visual Studio 2017 or later to create a publishing profile.</span></span> <span data-ttu-id="1b726-165">创建配置文件后，可以从 Visual Studio 或命令行进行发布。</span><span class="sxs-lookup"><span data-stu-id="1b726-165">Once the profile is created, publishing from Visual Studio or the command line is available.</span></span>

<span data-ttu-id="1b726-166">发布配置文件可以简化发布过程，并且可以存在任意数量的配置文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-166">Publish profiles can simplify the publishing process, and any number of profiles can exist.</span></span> <span data-ttu-id="1b726-167">通过选择以下路径之一在 Visual Studio 中创建发布配置文件：</span><span class="sxs-lookup"><span data-stu-id="1b726-167">Create a publish profile in Visual Studio by choosing one of the following paths:</span></span>

* <span data-ttu-id="1b726-168">在“解决方案资源管理器”  中，右键单击该项目并选择“发布”  。</span><span class="sxs-lookup"><span data-stu-id="1b726-168">Right-click the project in **Solution Explorer** and select **Publish**.</span></span>
* <span data-ttu-id="1b726-169">可以从“生成”菜单中选择“发布 {项目名称}”   。</span><span class="sxs-lookup"><span data-stu-id="1b726-169">Select **Publish {PROJECT NAME}** from the **Build** menu.</span></span>

<span data-ttu-id="1b726-170">随即显示应用程序容量页的“发布”  选项卡。</span><span class="sxs-lookup"><span data-stu-id="1b726-170">The **Publish** tab of the app capacities page is displayed.</span></span> <span data-ttu-id="1b726-171">如果项目缺少发布配置文件，将显示以下页面：</span><span class="sxs-lookup"><span data-stu-id="1b726-171">If the project lacks a publish profile, the following page is displayed:</span></span>

![随即显示应用程序容量页的“发布”选项卡](visual-studio-publish-profiles/_static/az.png)

<span data-ttu-id="1b726-173">当选择“文件夹”  时，指定一个文件夹路径来存储发布的资产。</span><span class="sxs-lookup"><span data-stu-id="1b726-173">When **Folder** is selected, specify a folder path to store the published assets.</span></span> <span data-ttu-id="1b726-174">默认文件夹是 bin\Release\PublishOutput  。</span><span class="sxs-lookup"><span data-stu-id="1b726-174">The default folder is *bin\Release\PublishOutput*.</span></span> <span data-ttu-id="1b726-175">单击“创建配置文件”  按钮即可完成。</span><span class="sxs-lookup"><span data-stu-id="1b726-175">Click the **Create Profile** button to finish.</span></span>

<span data-ttu-id="1b726-176">创建发布配置文件后，“发布”  选项卡将更改。</span><span class="sxs-lookup"><span data-stu-id="1b726-176">Once a publish profile is created, the **Publish** tab changes.</span></span> <span data-ttu-id="1b726-177">新创建的配置文件显示在下拉列表中。</span><span class="sxs-lookup"><span data-stu-id="1b726-177">The newly created profile appears in a drop-down list.</span></span> <span data-ttu-id="1b726-178">单击“创建新配置文件”  以创建其他新配置文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-178">Click **Create new profile** to create another new profile.</span></span>

![显示 FolderProfile 的应用程序容量页的“发布”选项卡](visual-studio-publish-profiles/_static/create_new.png)

<span data-ttu-id="1b726-180">发布向导支持以下发布目标：</span><span class="sxs-lookup"><span data-stu-id="1b726-180">The Publish wizard supports the following publish targets:</span></span>

* <span data-ttu-id="1b726-181">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="1b726-181">Azure App Service</span></span>
* <span data-ttu-id="1b726-182">Azure 虚拟机</span><span class="sxs-lookup"><span data-stu-id="1b726-182">Azure Virtual Machines</span></span>
* <span data-ttu-id="1b726-183">IIS、FTP 等（适用于任何 Web 服务器）</span><span class="sxs-lookup"><span data-stu-id="1b726-183">IIS, FTP, etc. (for any web server)</span></span>
* <span data-ttu-id="1b726-184">文件夹</span><span class="sxs-lookup"><span data-stu-id="1b726-184">Folder</span></span>
* <span data-ttu-id="1b726-185">导入配置文件</span><span class="sxs-lookup"><span data-stu-id="1b726-185">Import Profile</span></span>

<span data-ttu-id="1b726-186">有关详细信息，请参阅[哪些发布选项适合我](/visualstudio/ide/not-in-toc/web-publish-options)。</span><span class="sxs-lookup"><span data-stu-id="1b726-186">For more information, see [What publishing options are right for me](/visualstudio/ide/not-in-toc/web-publish-options).</span></span>

<span data-ttu-id="1b726-187">使用 Visual Studio 创建发布配置文件时，将创建 Properties/PublishProfiles/{PROFILE NAME}.pubxml MSBuild 文件  。</span><span class="sxs-lookup"><span data-stu-id="1b726-187">When creating a publish profile with Visual Studio, a *Properties/PublishProfiles/{PROFILE NAME}.pubxml* MSBuild file is created.</span></span> <span data-ttu-id="1b726-188">此 .pubxml  文件为 MSBuild 文件，包含发布配置设置。</span><span class="sxs-lookup"><span data-stu-id="1b726-188">The *.pubxml* file is a MSBuild file and contains publish configuration settings.</span></span> <span data-ttu-id="1b726-189">可以更改此文件以自定义生成和发布过程。</span><span class="sxs-lookup"><span data-stu-id="1b726-189">This file can be changed to customize the build and publish process.</span></span> <span data-ttu-id="1b726-190">通过发布过程读取此文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-190">This file is read by the publishing process.</span></span> <span data-ttu-id="1b726-191">`<LastUsedBuildConfiguration>` 比较特殊，因为它是一个全局属性，不应出现在导入生成的任何文件中。</span><span class="sxs-lookup"><span data-stu-id="1b726-191">`<LastUsedBuildConfiguration>` is special because it's a global property and shouldn't be in any file that's imported in the build.</span></span> <span data-ttu-id="1b726-192">有关详细信息，请参阅 [MSBuild：如何设置配置属性](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx)。</span><span class="sxs-lookup"><span data-stu-id="1b726-192">See [MSBuild: how to set the configuration property](http://sedodream.com/2012/10/27/MSBuildHowToSetTheConfigurationProperty.aspx) for more information.</span></span>

<span data-ttu-id="1b726-193">发布到 Azure 目标时，.pubxml  文件包含 Azure 订阅标识符。</span><span class="sxs-lookup"><span data-stu-id="1b726-193">When publishing to an Azure target, the *.pubxml* file contains your Azure subscription identifier.</span></span> <span data-ttu-id="1b726-194">不建议使用该目标类型将此文件添加到源代码管理。</span><span class="sxs-lookup"><span data-stu-id="1b726-194">With that target type, adding this file to source control is discouraged.</span></span> <span data-ttu-id="1b726-195">发布到非 Azure 目标时，签入 .pubxml  文件是安全的。</span><span class="sxs-lookup"><span data-stu-id="1b726-195">When publishing to a non-Azure target, it's safe to check in the *.pubxml* file.</span></span>

<span data-ttu-id="1b726-196">敏感信息（如发布密码）在每个用户/机器级别均进行加密。</span><span class="sxs-lookup"><span data-stu-id="1b726-196">Sensitive information (like the publish password) is encrypted on a per user/machine level.</span></span> <span data-ttu-id="1b726-197">它存储在 Properties/PublishProfiles/{PROFILE NAME}.pubxml.user 文件中  。</span><span class="sxs-lookup"><span data-stu-id="1b726-197">It's stored in the *Properties/PublishProfiles/{PROFILE NAME}.pubxml.user* file.</span></span> <span data-ttu-id="1b726-198">由于此文件可以存储敏感信息，因此不应将其签入源代码管理。</span><span class="sxs-lookup"><span data-stu-id="1b726-198">Because this file can store sensitive information, it shouldn't be checked into source control.</span></span>

<span data-ttu-id="1b726-199">有关如何在 ASP.NET Core 上发布 Web 应用的概述，请参阅[托管和部署](xref:host-and-deploy/index)。</span><span class="sxs-lookup"><span data-stu-id="1b726-199">For an overview of how to publish a web app on ASP.NET Core, see [Host and deploy](xref:host-and-deploy/index).</span></span> <span data-ttu-id="1b726-200">发布 ASP.NET Core 应用所需的 MSBuild 任务和目标是开放源代码，位于：[aspnet/websdk repository](https://github.com/aspnet/websdk)。</span><span class="sxs-lookup"><span data-stu-id="1b726-200">The MSBuild tasks and targets necessary to publish an ASP.NET Core app are open-source at the [aspnet/websdk repository](https://github.com/aspnet/websdk).</span></span>

<span data-ttu-id="1b726-201">`dotnet publish` 可以使用文件夹、MSDeploy 和 [Kudu](https://github.com/projectkudu/kudu/wiki) 发布配置文件：</span><span class="sxs-lookup"><span data-stu-id="1b726-201">`dotnet publish` can use folder, MSDeploy, and [Kudu](https://github.com/projectkudu/kudu/wiki) publish profiles:</span></span>

<span data-ttu-id="1b726-202">文件夹（跨平台工作）：</span><span class="sxs-lookup"><span data-stu-id="1b726-202">Folder (works cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<FolderProfileName>
```

<span data-ttu-id="1b726-203">MSDeploy（当前仅适用于 Windows，因为 MSDeploy 不是跨平台的）：</span><span class="sxs-lookup"><span data-stu-id="1b726-203">MSDeploy (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployProfileName> /p:Password=<DeploymentPassword>
```

<span data-ttu-id="1b726-204">MSDeploy 包（当前仅适用于 Windows，因为 MSDeploy 不是跨平台的）：</span><span class="sxs-lookup"><span data-stu-id="1b726-204">MSDeploy package (currently this only works in Windows since MSDeploy isn't cross-platform):</span></span>

```console
dotnet publish WebApplication.csproj /p:PublishProfile=<MsDeployPackageProfileName>
```

<span data-ttu-id="1b726-205">在前面的示例中，不会将 `deployonbuild` 传递到 `dotnet publish`。</span><span class="sxs-lookup"><span data-stu-id="1b726-205">In the preceding examples, don't pass `deployonbuild` to `dotnet publish`.</span></span>

<span data-ttu-id="1b726-206">有关详细信息，请参阅 [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish)。</span><span class="sxs-lookup"><span data-stu-id="1b726-206">For more information, see [Microsoft.NET.Sdk.Publish](https://github.com/aspnet/websdk#microsoftnetsdkpublish).</span></span>

<span data-ttu-id="1b726-207">`dotnet publish` 支持 Kudu API 从任何平台发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="1b726-207">`dotnet publish` supports Kudu APIs to publish to Azure from any platform.</span></span> <span data-ttu-id="1b726-208">Visual Studio 发布支持 Kudu API，但 WebSDK 支持其跨平台发布到 Azure。</span><span class="sxs-lookup"><span data-stu-id="1b726-208">Visual Studio publish supports the Kudu APIs, but it's supported by WebSDK for cross-platform publish to Azure.</span></span>

<span data-ttu-id="1b726-209">向“属性/发布配置文件”  文件夹添加包含以下内容的发布配置文件：</span><span class="sxs-lookup"><span data-stu-id="1b726-209">Add a publish profile to the *Properties/PublishProfiles* folder with the following content:</span></span>

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

<span data-ttu-id="1b726-210">运行以下命令将会压缩发布内容并将其发布到使用 Kudu API 的 Azure：</span><span class="sxs-lookup"><span data-stu-id="1b726-210">Run the following command to zip up the publish contents and publish it to Azure using the Kudu APIs:</span></span>

```console
dotnet publish /p:PublishProfile=Azure /p:Configuration=Release
```

<span data-ttu-id="1b726-211">使用发布配置文件时，请设置以下 MSBuild 属性：</span><span class="sxs-lookup"><span data-stu-id="1b726-211">Set the following MSBuild properties when using a publish profile:</span></span>

* `DeployOnBuild=true`
* `PublishProfile={PUBLISH PROFILE}`

<span data-ttu-id="1b726-212">发布名为 FolderProfile  的配置文件时，可以执行以下命令之一：</span><span class="sxs-lookup"><span data-stu-id="1b726-212">When publishing with a profile named *FolderProfile*, either of the commands below can be executed:</span></span>

* `dotnet build /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`
* `msbuild      /p:DeployOnBuild=true /p:PublishProfile=FolderProfile`

<span data-ttu-id="1b726-213">调用 [dotnet build](/dotnet/core/tools/dotnet-build) 时，它调用 `msbuild` 来运行生成和发布过程。</span><span class="sxs-lookup"><span data-stu-id="1b726-213">When invoking [dotnet build](/dotnet/core/tools/dotnet-build), it calls `msbuild` to run the build and publish process.</span></span> <span data-ttu-id="1b726-214">当传递到文件夹配置文件中时，调用 `dotnet build` 或 `msbuild` 的作用是相同的。</span><span class="sxs-lookup"><span data-stu-id="1b726-214">Calling either `dotnet build` or `msbuild` is equivalent when passing in a folder profile.</span></span> <span data-ttu-id="1b726-215">在 Windows 上直接调用 MSBuild 时，将使用 MSBuild 的 .NET Framework 版本。</span><span class="sxs-lookup"><span data-stu-id="1b726-215">When calling MSBuild directly on Windows, the .NET Framework version of MSBuild is used.</span></span> <span data-ttu-id="1b726-216">MSDeploy 目前仅限于在 Windows 计算机上进行发布。</span><span class="sxs-lookup"><span data-stu-id="1b726-216">MSDeploy is currently limited to Windows machines for publishing.</span></span> <span data-ttu-id="1b726-217">在非文件夹配置文件上调用 `dotnet build` 时，会调用 MSBuild，并且 MSBuild 在非文件夹配置文件上使用 MSDeploy。</span><span class="sxs-lookup"><span data-stu-id="1b726-217">Calling `dotnet build` on a non-folder profile invokes MSBuild, and MSBuild uses MSDeploy on non-folder profiles.</span></span> <span data-ttu-id="1b726-218">在非文件夹配置文件上调用 `dotnet build` 时，会调用 MSBuild（使用 MSDeploy）并导致失败（即使在 Windows 平台上运行也是如此）。</span><span class="sxs-lookup"><span data-stu-id="1b726-218">Calling `dotnet build` on a non-folder profile invokes MSBuild (using MSDeploy) and results in a failure (even when running on a Windows platform).</span></span> <span data-ttu-id="1b726-219">若要使用非文件夹配置文件进行发布，请直接调用 MSBuild。</span><span class="sxs-lookup"><span data-stu-id="1b726-219">To publish with a non-folder profile, call MSBuild directly.</span></span>

<span data-ttu-id="1b726-220">以下文件夹发布配置文件通过 Visual Studio 创建，并被发布到网络共享：</span><span class="sxs-lookup"><span data-stu-id="1b726-220">The following folder publish profile was created with Visual Studio and publishes to a network share:</span></span>

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

<span data-ttu-id="1b726-221">在前面的示例中，将 `<LastUsedBuildConfiguration>` 设置为 `Release`。</span><span class="sxs-lookup"><span data-stu-id="1b726-221">In the preceding example, `<LastUsedBuildConfiguration>` is set to `Release`.</span></span> <span data-ttu-id="1b726-222">从 Visual Studio 发布时，在启动发布过程后将使用该值设置 `<LastUsedBuildConfiguration>` 配置属性值。</span><span class="sxs-lookup"><span data-stu-id="1b726-222">When publishing from Visual Studio, the `<LastUsedBuildConfiguration>` configuration property value is set using the value when the publish process is started.</span></span> <span data-ttu-id="1b726-223">`<LastUsedBuildConfiguration>` 配置属性比较特殊，不应在导入的 MSBuild 文件中覆盖该属性。</span><span class="sxs-lookup"><span data-stu-id="1b726-223">The `<LastUsedBuildConfiguration>` configuration property is special and shouldn't be overridden in an imported MSBuild file.</span></span> <span data-ttu-id="1b726-224">可以从命令行覆盖此属性。</span><span class="sxs-lookup"><span data-stu-id="1b726-224">This property can be overridden from the command line.</span></span>

<span data-ttu-id="1b726-225">使用 .NET Core CLI：</span><span class="sxs-lookup"><span data-stu-id="1b726-225">Using the .NET Core CLI:</span></span>

```console
dotnet build -c Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

<span data-ttu-id="1b726-226">使用 MSBuild：</span><span class="sxs-lookup"><span data-stu-id="1b726-226">Using MSBuild:</span></span>

```console
msbuild /p:Configuration=Release /p:DeployOnBuild=true /p:PublishProfile=FolderProfile
```

## <a name="publish-to-an-msdeploy-endpoint-from-the-command-line"></a><span data-ttu-id="1b726-227">从命令行发布到 MSDeploy 终结点</span><span class="sxs-lookup"><span data-stu-id="1b726-227">Publish to an MSDeploy endpoint from the command line</span></span>

<span data-ttu-id="1b726-228">下面的示例使用由 Visual Studio 创建的 ASP.NET Core Web 应用，名为 AzureWebApp  。</span><span class="sxs-lookup"><span data-stu-id="1b726-228">The following example uses an ASP.NET Core web app created by Visual Studio named *AzureWebApp*.</span></span> <span data-ttu-id="1b726-229">通过 Visual Studio 添加 Azure 应用发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-229">An Azure Apps publish profile is added with Visual Studio.</span></span> <span data-ttu-id="1b726-230">有关如何创建配置文件的详细信息，请参阅[发布配置文件](#publish-profiles)部分。</span><span class="sxs-lookup"><span data-stu-id="1b726-230">For more information on how to create a profile, see the [Publish profiles](#publish-profiles) section.</span></span>

<span data-ttu-id="1b726-231">若要使用发布配置文件部署应用，请在 Visual Studio 开发人员命令提示中执行 `msbuild` 命令  。</span><span class="sxs-lookup"><span data-stu-id="1b726-231">To deploy the app using a publish profile, execute the `msbuild` command from a Visual Studio **Developer Command Prompt**.</span></span> <span data-ttu-id="1b726-232">Windows 任务栏上的“开始”菜单的“Visual Studio”文件夹中提供命令提示符   。</span><span class="sxs-lookup"><span data-stu-id="1b726-232">The command prompt is available in the *Visual Studio* folder of the **Start** menu on the Windows taskbar.</span></span> <span data-ttu-id="1b726-233">为了便于访问，可将命令提示符添加到 Visual Studio 中的“工具”菜单中  。</span><span class="sxs-lookup"><span data-stu-id="1b726-233">For easier access, you can add the command prompt to the **Tools** menu in Visual Studio.</span></span> <span data-ttu-id="1b726-234">有关详细信息，请参阅 [Visual Studio 开发人员命令提示符](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio)。</span><span class="sxs-lookup"><span data-stu-id="1b726-234">For more information, see [Developer Command Prompt for Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs#run-the-command-prompt-from-inside-visual-studio).</span></span>

<span data-ttu-id="1b726-235">MSBuild 使用以下命令语法：</span><span class="sxs-lookup"><span data-stu-id="1b726-235">MSBuild uses the following command syntax:</span></span>

```console
msbuild {PATH} 
    /p:DeployOnBuild=true 
    /p:PublishProfile={PROFILE} 
    /p:Username={USERNAME} 
    /p:Password={PASSWORD}
```

* <span data-ttu-id="1b726-236">{PATH} &ndash; 应用的项目文件路径。</span><span class="sxs-lookup"><span data-stu-id="1b726-236">{PATH} &ndash; Path to the app's project file.</span></span>
* <span data-ttu-id="1b726-237">{PROFILE} &ndash; 发布配置文件的名称。</span><span class="sxs-lookup"><span data-stu-id="1b726-237">{PROFILE} &ndash; Name of the publish profile.</span></span>
* <span data-ttu-id="1b726-238">{USERNAME} &ndash; MSDeploy 用户名。</span><span class="sxs-lookup"><span data-stu-id="1b726-238">{USERNAME} &ndash; MSDeploy username.</span></span> <span data-ttu-id="1b726-239">可在发布配置文件中找到 {USERNAME}。</span><span class="sxs-lookup"><span data-stu-id="1b726-239">The {USERNAME} can be found in the publish profile.</span></span>
* <span data-ttu-id="1b726-240">{PASSWORD} &ndash; MSDeploy 密码。</span><span class="sxs-lookup"><span data-stu-id="1b726-240">{PASSWORD} &ndash; MSDeploy password.</span></span> <span data-ttu-id="1b726-241">从 {PROFILE}.PublishSettings 文件中获取 {PASSWORD}  。</span><span class="sxs-lookup"><span data-stu-id="1b726-241">Obtain the {PASSWORD} from the *{PROFILE}.PublishSettings* file.</span></span> <span data-ttu-id="1b726-242">可以从以下位置下载 .PublishSettings  文件：</span><span class="sxs-lookup"><span data-stu-id="1b726-242">Download the *.PublishSettings* file from either:</span></span>
  * <span data-ttu-id="1b726-243">解决方案资源管理器：选择“视图” > “Cloud Explorer”   。</span><span class="sxs-lookup"><span data-stu-id="1b726-243">Solution Explorer: Select **View** > **Cloud Explorer**.</span></span> <span data-ttu-id="1b726-244">连接你的 Azure 订阅。</span><span class="sxs-lookup"><span data-stu-id="1b726-244">Connect with your Azure subscription.</span></span> <span data-ttu-id="1b726-245">打开“应用服务”  。</span><span class="sxs-lookup"><span data-stu-id="1b726-245">Open **App Services**.</span></span> <span data-ttu-id="1b726-246">右键单击应用。</span><span class="sxs-lookup"><span data-stu-id="1b726-246">Right-click the app.</span></span> <span data-ttu-id="1b726-247">选择“下载发布配置文件”  。</span><span class="sxs-lookup"><span data-stu-id="1b726-247">Select **Download Publish Profile**.</span></span>
  * <span data-ttu-id="1b726-248">Azure 门户：选择 Web 应用“概述”面板上的“获取发布配置文件”   。</span><span class="sxs-lookup"><span data-stu-id="1b726-248">Azure portal: Select **Get publish profile** in the web app's **Overview** panel.</span></span>

<span data-ttu-id="1b726-249">下面的示例使用名为“AzureWebApp - Web 部署”的发布配置文件  ：</span><span class="sxs-lookup"><span data-stu-id="1b726-249">The following example uses a publish profile named *AzureWebApp - Web Deploy*:</span></span>

```console
msbuild "AzureWebApp.csproj" 
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

<span data-ttu-id="1b726-250">此外，还可通过 Windows 命令提示符将发布配置文件与 .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 命令配合使用：</span><span class="sxs-lookup"><span data-stu-id="1b726-250">A publish profile can also be used with the .NET Core CLI [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command from a Windows command prompt:</span></span>

```console
dotnet msbuild "AzureWebApp.csproj"
    /p:DeployOnBuild=true 
    /p:PublishProfile="AzureWebApp - Web Deploy" 
    /p:Username="$AzureWebApp" 
    /p:Password=".........."
```

> [!NOTE]
> <span data-ttu-id="1b726-251">可跨平台使用 [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) 命令，并且可在 macOS 和 Linux 上编译 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="1b726-251">The [dotnet msbuild](/dotnet/core/tools/dotnet-msbuild) command is available cross-platform and can compile ASP.NET Core apps on macOS and Linux.</span></span> <span data-ttu-id="1b726-252">但是，macOS 或 Linux 版 MSBuild 并不能将应用部署到 Azure 或其他 MSDeploy 终结点。</span><span class="sxs-lookup"><span data-stu-id="1b726-252">However, MSBuild on macOS and Linux isn't capable of deploying an app to Azure or other MSDeploy endpoint.</span></span> <span data-ttu-id="1b726-253">MSDeploy 仅在 Windows 上可用。</span><span class="sxs-lookup"><span data-stu-id="1b726-253">MSDeploy is only available on Windows.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="1b726-254">设置环境</span><span class="sxs-lookup"><span data-stu-id="1b726-254">Set the environment</span></span>

<span data-ttu-id="1b726-255">将 `<EnvironmentName>` 属性包含在发布配置文件（.pubxml  ）或项目文件中，以设置应用的[环境](xref:fundamentals/environments)：</span><span class="sxs-lookup"><span data-stu-id="1b726-255">Include the `<EnvironmentName>` property in the publish profile (*.pubxml*) or project file to set the app's [environment](xref:fundamentals/environments):</span></span>

```xml
<PropertyGroup>
  <EnvironmentName>Development</EnvironmentName>
</PropertyGroup>
```

<span data-ttu-id="1b726-256">如果需要 web.config  转换（例如，基于配置、配置文件或环境设置环境变量），请参阅 <xref:host-and-deploy/iis/transform-webconfig>。</span><span class="sxs-lookup"><span data-stu-id="1b726-256">If you require *web.config* transformations (for example, setting environment variables based on the configuration, profile, or environment), see <xref:host-and-deploy/iis/transform-webconfig>.</span></span>

## <a name="exclude-files"></a><span data-ttu-id="1b726-257">排除文件</span><span class="sxs-lookup"><span data-stu-id="1b726-257">Exclude files</span></span>

<span data-ttu-id="1b726-258">在发布 ASP.NET Core Web 应用时，包括以下资产：</span><span class="sxs-lookup"><span data-stu-id="1b726-258">When publishing ASP.NET Core web apps, the following assets are included:</span></span>

* <span data-ttu-id="1b726-259">生成工件</span><span class="sxs-lookup"><span data-stu-id="1b726-259">Build artifacts</span></span>
* <span data-ttu-id="1b726-260">与以下 glob 模式匹配的文件夹和文件：</span><span class="sxs-lookup"><span data-stu-id="1b726-260">Folders and files matching the following globbing patterns:</span></span>
  * <span data-ttu-id="1b726-261">`**\*.config`（例如，web.config  ）</span><span class="sxs-lookup"><span data-stu-id="1b726-261">`**\*.config` (for example, *web.config*)</span></span>
  * <span data-ttu-id="1b726-262">`**\*.json`（例如，appsettings.json  ）</span><span class="sxs-lookup"><span data-stu-id="1b726-262">`**\*.json` (for example, *appsettings.json*)</span></span>
  * `wwwroot\**`

<span data-ttu-id="1b726-263">MSBuild 支持 [glob 模式](https://gruntjs.com/configuring-tasks#globbing-patterns)。</span><span class="sxs-lookup"><span data-stu-id="1b726-263">MSBuild supports [globbing patterns](https://gruntjs.com/configuring-tasks#globbing-patterns).</span></span> <span data-ttu-id="1b726-264">例如，以下 `<Content>` 元素禁止在 wwwroot\content  文件夹及其子文件夹中复制文本 (.txt  ) 文件：</span><span class="sxs-lookup"><span data-stu-id="1b726-264">For example, the following `<Content>` element suppresses the copying of text (*.txt*) files in the *wwwroot\content* folder and its subfolders:</span></span>

```xml
<ItemGroup>
  <Content Update="wwwroot/content/**/*.txt" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="1b726-265">可以将上面的标记添加到发布配置文件或 .csproj  文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-265">The preceding markup can be added to a publish profile or the *.csproj* file.</span></span> <span data-ttu-id="1b726-266">添加到 .csproj  文件时，会将该规则添加到项目中的所有发布配置文件中。</span><span class="sxs-lookup"><span data-stu-id="1b726-266">When added to the *.csproj* file, the rule is added to all publish profiles in the project.</span></span>

<span data-ttu-id="1b726-267">以下 `<MsDeploySkipRules>` 元素排除 wwwroot\content  文件夹中的所有文件：</span><span class="sxs-lookup"><span data-stu-id="1b726-267">The following `<MsDeploySkipRules>` element excludes all files from the *wwwroot\content* folder:</span></span>

```xml
<ItemGroup>
  <MsDeploySkipRules Include="CustomSkipFolder">
    <ObjectName>dirPath</ObjectName>
    <AbsolutePath>wwwroot\\content</AbsolutePath>
  </MsDeploySkipRules>
</ItemGroup>
```

<span data-ttu-id="1b726-268">`<MsDeploySkipRules>` 不会从部署站点删除跳过  目标。</span><span class="sxs-lookup"><span data-stu-id="1b726-268">`<MsDeploySkipRules>` won't delete the *skip* targets from the deployment site.</span></span> <span data-ttu-id="1b726-269">从部署站点中删除 `<Content>` 目标文件和文件夹。</span><span class="sxs-lookup"><span data-stu-id="1b726-269">`<Content>` targeted files and folders are deleted from the deployment site.</span></span> <span data-ttu-id="1b726-270">例如，假设部署的 Web 应用具有以下文件：</span><span class="sxs-lookup"><span data-stu-id="1b726-270">For example, suppose a deployed web app had the following files:</span></span>

* <span data-ttu-id="1b726-271">*Views/Home/About1.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1b726-271">*Views/Home/About1.cshtml*</span></span>
* <span data-ttu-id="1b726-272">*Views/Home/About2.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1b726-272">*Views/Home/About2.cshtml*</span></span>
* <span data-ttu-id="1b726-273">*Views/Home/About3.cshtml*</span><span class="sxs-lookup"><span data-stu-id="1b726-273">*Views/Home/About3.cshtml*</span></span>

<span data-ttu-id="1b726-274">如果添加了以下 `<MsDeploySkipRules>` 元素，则不会在部署站点上删除这些文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-274">If the following `<MsDeploySkipRules>` elements are added, those files wouldn't be deleted on the deployment site.</span></span>

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

<span data-ttu-id="1b726-275">之前的 `<MsDeploySkipRules>` 元素阻止部署跳过的  文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-275">The preceding `<MsDeploySkipRules>` elements prevent the *skipped* files from being deployed.</span></span> <span data-ttu-id="1b726-276">一旦部署这些文件就不会删除它们。</span><span class="sxs-lookup"><span data-stu-id="1b726-276">It won't delete those files once they're deployed.</span></span>

<span data-ttu-id="1b726-277">以下 `<Content>` 元素将在部署站点中删除目标文件：</span><span class="sxs-lookup"><span data-stu-id="1b726-277">The following `<Content>` element deletes the targeted files at the deployment site:</span></span>

```xml
<ItemGroup>
  <Content Update="Views/Home/About?.cshtml" CopyToPublishDirectory="Never" />
</ItemGroup>
```

<span data-ttu-id="1b726-278">使用具有上述 `<Content>` 元素的命令行部署可生成以下输出：</span><span class="sxs-lookup"><span data-stu-id="1b726-278">Using command-line deployment with the preceding `<Content>` element yields the following output:</span></span>

```console
MSDeployPublish:
  Starting Web deployment task from source: manifest(C:\Webs\Web1\obj\Release\netcoreapp1.1\PubTmp\Web1.SourceManifest.
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

## <a name="include-files"></a><span data-ttu-id="1b726-279">包含文件</span><span class="sxs-lookup"><span data-stu-id="1b726-279">Include files</span></span>

<span data-ttu-id="1b726-280">下列标记：</span><span class="sxs-lookup"><span data-stu-id="1b726-280">The following markup:</span></span>

* <span data-ttu-id="1b726-281">将项目目录外的 images  文件夹包含到发布站点的 wwwroot/images  文件夹中。</span><span class="sxs-lookup"><span data-stu-id="1b726-281">Includes an *images* folder outside the project directory to the *wwwroot/images* folder of the publish site.</span></span>
* <span data-ttu-id="1b726-282">可以将标记添加到 .csproj  文件或发布配置文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-282">Can be added to the *.csproj* file or the publish profile.</span></span> <span data-ttu-id="1b726-283">如果将其添加到 .csproj  文件，它会包含在项目的每个发布配置文件中。</span><span class="sxs-lookup"><span data-stu-id="1b726-283">If it's added to the *.csproj* file, it's included in each publish profile in the project.</span></span>

```xml
<ItemGroup>
  <_CustomFiles Include="$(MSBuildProjectDirectory)/../images/**/*" />
  <DotnetPublishFiles Include="@(_CustomFiles)">
    <DestinationRelativePath>wwwroot/images/%(RecursiveDir)%(Filename)%(Extension)</DestinationRelativePath>
  </DotnetPublishFiles>
</ItemGroup>
```

<span data-ttu-id="1b726-284">以下突出显示的标记显示如何：</span><span class="sxs-lookup"><span data-stu-id="1b726-284">The following highlighted markup shows how to:</span></span>

* <span data-ttu-id="1b726-285">将文件从项目外部复制到 wwwroot  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1b726-285">Copy a file from outside the project into the *wwwroot* folder.</span></span>
* <span data-ttu-id="1b726-286">排除 wwwroot\Content  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1b726-286">Exclude the *wwwroot\Content* folder.</span></span>
* <span data-ttu-id="1b726-287">排除 Views\Home\About2.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="1b726-287">Exclude *Views\Home\About2.cshtml*.</span></span>

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
    <PublishFramework />
    <ProjectGuid>afa9f185-7ce0-4935-9da1-ab676229d68a</ProjectGuid>
    <publishUrl>bin\Release\PublishOutput</publishUrl>
    <DeleteExistingFiles>False</DeleteExistingFiles>
  </PropertyGroup>
  <ItemGroup>
    <ResolvedFileToPublish Include="..\ReadMe2.MD">
      <RelativePath>wwwroot\ReadMe2.MD</RelativePath>
    </ResolvedFileToPublish>

    <Content Update="wwwroot\Content\**\*" CopyToPublishDirectory="Never" />
    <Content Update="Views\Home\About2.cshtml" CopyToPublishDirectory="Never" />

  </ItemGroup>
</Project>
```

<span data-ttu-id="1b726-288">请参阅 [Web SDK 存储库自述文件](https://github.com/aspnet/websdk)，了解更多部署示例。</span><span class="sxs-lookup"><span data-stu-id="1b726-288">See the [Web SDK repository Readme](https://github.com/aspnet/websdk) for more deployment samples.</span></span>

## <a name="run-a-target-before-or-after-publishing"></a><span data-ttu-id="1b726-289">在发布前或发布后运行目标</span><span class="sxs-lookup"><span data-stu-id="1b726-289">Run a target before or after publishing</span></span>

<span data-ttu-id="1b726-290">内置 `BeforePublish` 和 `AfterPublish` 目标在发布目标前/后执行目标。</span><span class="sxs-lookup"><span data-stu-id="1b726-290">The built-in `BeforePublish` and `AfterPublish` targets execute a target before or after the publish target.</span></span> <span data-ttu-id="1b726-291">将以下元素添加到发布配置文件，以在发布前/后均记录控制台消息：</span><span class="sxs-lookup"><span data-stu-id="1b726-291">Add the following elements to the publish profile to log console messages both before and after publishing:</span></span>

```xml
<Target Name="CustomActionsBeforePublish" BeforeTargets="BeforePublish">
    <Message Text="Inside BeforePublish" Importance="high" />
  </Target>
  <Target Name="CustomActionsAfterPublish" AfterTargets="AfterPublish">
    <Message Text="Inside AfterPublish" Importance="high" />
</Target>
```

## <a name="publish-to-a-server-using-an-untrusted-certificate"></a><span data-ttu-id="1b726-292">使用不受信任的证书发布到服务器</span><span class="sxs-lookup"><span data-stu-id="1b726-292">Publish to a server using an untrusted certificate</span></span>

<span data-ttu-id="1b726-293">将值为 `True` 的 `<AllowUntrustedCertificate>` 属性添加到发布配置文件：</span><span class="sxs-lookup"><span data-stu-id="1b726-293">Add the `<AllowUntrustedCertificate>` property with a value of `True` to the publish profile:</span></span>

```xml
<PropertyGroup>
  <AllowUntrustedCertificate>True</AllowUntrustedCertificate>
</PropertyGroup>
```

## <a name="the-kudu-service"></a><span data-ttu-id="1b726-294">Kudu 服务</span><span class="sxs-lookup"><span data-stu-id="1b726-294">The Kudu service</span></span>

<span data-ttu-id="1b726-295">要查看 Azure 应用服务 Web 应用部署中的文件，请使用 [Kudu 服务](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)。</span><span class="sxs-lookup"><span data-stu-id="1b726-295">To view the files in an Azure App Service web app deployment, use the [Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service).</span></span> <span data-ttu-id="1b726-296">将 `scm` 令牌追加到 Web 应用名称。</span><span class="sxs-lookup"><span data-stu-id="1b726-296">Append the `scm` token to the web app name.</span></span> <span data-ttu-id="1b726-297">例如:</span><span class="sxs-lookup"><span data-stu-id="1b726-297">For example:</span></span>

| <span data-ttu-id="1b726-298">URL</span><span class="sxs-lookup"><span data-stu-id="1b726-298">URL</span></span>                                    | <span data-ttu-id="1b726-299">结果</span><span class="sxs-lookup"><span data-stu-id="1b726-299">Result</span></span>       |
| -------------------------------------- | ------------ |
| `http://mysite.azurewebsites.net/`     | <span data-ttu-id="1b726-300">Web 应用</span><span class="sxs-lookup"><span data-stu-id="1b726-300">Web App</span></span>      |
| `http://mysite.scm.azurewebsites.net/` | <span data-ttu-id="1b726-301">Kudu 服务</span><span class="sxs-lookup"><span data-stu-id="1b726-301">Kudu service</span></span> |

<span data-ttu-id="1b726-302">选择[调试控制台](https://github.com/projectkudu/kudu/wiki/Kudu-console)菜单项来查看、编辑、删除或添加文件。</span><span class="sxs-lookup"><span data-stu-id="1b726-302">Select the [Debug Console](https://github.com/projectkudu/kudu/wiki/Kudu-console) menu item to view, edit, delete, or add files.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1b726-303">其他资源</span><span class="sxs-lookup"><span data-stu-id="1b726-303">Additional resources</span></span>

* <span data-ttu-id="1b726-304">[Web 部署](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) 简化了 Web 应用和网站到 IIS 服务器的部署。</span><span class="sxs-lookup"><span data-stu-id="1b726-304">[Web Deploy](https://www.iis.net/downloads/microsoft/web-deploy) (MSDeploy) simplifies deployment of web apps and websites to IIS servers.</span></span>
* <span data-ttu-id="1b726-305">[Web SDK GitHub 存储库](https://github.com/aspnet/websdk/issues)：文件问题和部署的请求功能。</span><span class="sxs-lookup"><span data-stu-id="1b726-305">[Web SDK GitHub repository](https://github.com/aspnet/websdk/issues): File issues and request features for deployment.</span></span>
* [<span data-ttu-id="1b726-306">从 Visual Studio 将 ASP.NET Web 应用发布到 Azure VM</span><span class="sxs-lookup"><span data-stu-id="1b726-306">Publish an ASP.NET Web App to an Azure VM from Visual Studio</span></span>](/azure/virtual-machines/windows/publish-web-app-from-visual-studio)
* <xref:host-and-deploy/iis/transform-webconfig>
