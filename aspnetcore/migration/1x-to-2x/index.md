---
title: 从 ASP.NET Core 1.x 迁移到 2.0
author: scottaddie
description: 本文概述了将 ASP.NET Core 1.x 项目迁移到 ASP.NET Core 2.0 的先决条件和最常见步骤。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/1x-to-2x/index
ms.openlocfilehash: 1b7b89b130f66c851bf01d0eb6d643e4b3676a1e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774218"
---
# <a name="migrate-from-aspnet-core-1x-to-20"></a><span data-ttu-id="3d32c-103">从 ASP.NET Core 1.x 迁移到 2.0</span><span class="sxs-lookup"><span data-stu-id="3d32c-103">Migrate from ASP.NET Core 1.x to 2.0</span></span>

<span data-ttu-id="3d32c-104">作者：[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="3d32c-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="3d32c-105">本文演示如何将现有 ASP.NET Core 1.x 项目更新到 ASP.NET Core 2.0。</span><span class="sxs-lookup"><span data-stu-id="3d32c-105">In this article, we walk you through updating an existing ASP.NET Core 1.x project to ASP.NET Core 2.0.</span></span> <span data-ttu-id="3d32c-106">通过将应用程序迁移到 ASP.NET Core 2.0，可利用[大量新功能和改进功能](xref:aspnetcore-2.0)。</span><span class="sxs-lookup"><span data-stu-id="3d32c-106">Migrating your application to ASP.NET Core 2.0 enables you to take advantage of [many new features and performance improvements](xref:aspnetcore-2.0).</span></span>

<span data-ttu-id="3d32c-107">现有 ASP.NET Core 1.x 应用程序基于版本特定的项目模板。</span><span class="sxs-lookup"><span data-stu-id="3d32c-107">Existing ASP.NET Core 1.x applications are based off of version-specific project templates.</span></span> <span data-ttu-id="3d32c-108">随着 ASP.NET Core 框架不断演变，其中的项目模板和起始代码也在变化。</span><span class="sxs-lookup"><span data-stu-id="3d32c-108">As the ASP.NET Core framework evolves, so do the project templates and the starter code contained within them.</span></span> <span data-ttu-id="3d32c-109">除了更新 ASP.NET Core 框架外，还需要为应用程序更新代码。</span><span class="sxs-lookup"><span data-stu-id="3d32c-109">In addition to updating the ASP.NET Core framework, you need to update the code for your application.</span></span>

<a name="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="3d32c-110">先决条件</span><span class="sxs-lookup"><span data-stu-id="3d32c-110">Prerequisites</span></span>

<span data-ttu-id="3d32c-111">请参阅 [ASP.NET Core 入门](xref:getting-started)。</span><span class="sxs-lookup"><span data-stu-id="3d32c-111">See [Get Started with ASP.NET Core](xref:getting-started).</span></span>

<a name="tfm"></a>

## <a name="update-target-framework-moniker-tfm"></a><span data-ttu-id="3d32c-112">更新目标框架名字对象 (TFM)</span><span class="sxs-lookup"><span data-stu-id="3d32c-112">Update Target Framework Moniker (TFM)</span></span>

<span data-ttu-id="3d32c-113">面向 .NET Core 的项目需使用大于或等于 .NET Core 2.0 版本的 [TFM](/dotnet/standard/frameworks)。</span><span class="sxs-lookup"><span data-stu-id="3d32c-113">Projects targeting .NET Core should use the [TFM](/dotnet/standard/frameworks) of a version greater than or equal to .NET Core 2.0.</span></span> <span data-ttu-id="3d32c-114">在“.csproj”文件中搜索 `<TargetFramework>` 节点，并将其内部文本替换为 `netcoreapp2.0` ：</span><span class="sxs-lookup"><span data-stu-id="3d32c-114">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `netcoreapp2.0`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=3)]

<span data-ttu-id="3d32c-115">面向 .NET Framework 的项目需使用大于或等于 .NET Framework 4.6.1 版本的 TFM。</span><span class="sxs-lookup"><span data-stu-id="3d32c-115">Projects targeting .NET Framework should use the TFM of a version greater than or equal to .NET Framework 4.6.1.</span></span> <span data-ttu-id="3d32c-116">在“.csproj”文件中搜索 `<TargetFramework>` 节点，并将其内部文本替换为 `net461` ：</span><span class="sxs-lookup"><span data-stu-id="3d32c-116">Search for the `<TargetFramework>` node in the *.csproj* file, and replace its inner text with `net461`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=4)]

> [!NOTE]
> <span data-ttu-id="3d32c-117">相比于 .NET Core 1.x，.NET Core 2.0 提供更多的外围应用。</span><span class="sxs-lookup"><span data-stu-id="3d32c-117">.NET Core 2.0 offers a much larger surface area than .NET Core 1.x.</span></span> <span data-ttu-id="3d32c-118">如果仅因为 .NET Core 1.x 中缺少 API 而要面向 .NET Framework，则定向于 .NET Core 2.0 可能有用。</span><span class="sxs-lookup"><span data-stu-id="3d32c-118">If you're targeting .NET Framework solely because of missing APIs in .NET Core 1.x, targeting .NET Core 2.0 is likely to work.</span></span>

<span data-ttu-id="3d32c-119">如果项目文件包含 `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268)。</span><span class="sxs-lookup"><span data-stu-id="3d32c-119">If the project file contains `<RuntimeFrameworkVersion>1.{sub-version}</RuntimeFrameworkVersion>`, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/3221#issuecomment-413094268).</span></span>

<a name="global-json"></a>

## <a name="update-net-core-sdk-version-in-globaljson"></a><span data-ttu-id="3d32c-120">在 global.json 中更新 .NET Core SDK 版本</span><span class="sxs-lookup"><span data-stu-id="3d32c-120">Update .NET Core SDK version in global.json</span></span>

<span data-ttu-id="3d32c-121">如果解决方案依靠 [global.json](/dotnet/core/tools/global-json) 文件来定向于特定 .NET Core SDK 版本，请更新其 `version` 属性以使用计算机上安装的 2.0 版本：</span><span class="sxs-lookup"><span data-stu-id="3d32c-121">If your solution relies upon a [global.json](/dotnet/core/tools/global-json) file to target a specific .NET Core SDK version, update its `version` property to use the 2.0 version installed on your machine:</span></span>

[!code-json[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/global.json?highlight=3)]

<a name="package-reference"></a>

## <a name="update-package-references"></a><span data-ttu-id="3d32c-122">更新包引用</span><span class="sxs-lookup"><span data-stu-id="3d32c-122">Update package references</span></span>

<span data-ttu-id="3d32c-123">1\.x 项目中的“.csproj”文件列出了该项目使用的每个 NuGet 包  。</span><span class="sxs-lookup"><span data-stu-id="3d32c-123">The *.csproj* file in a 1.x project lists each NuGet package used by the project.</span></span>

<span data-ttu-id="3d32c-124">在面向 .NET Core 2.0 的 ASP.NET Core 2.0 项目中，“.csproj”文件中的单个 [metapackage](xref:fundamentals/metapackage) 引用将替换包的集合  ：</span><span class="sxs-lookup"><span data-stu-id="3d32c-124">In an ASP.NET Core 2.0 project targeting .NET Core 2.0, a single [metapackage](xref:fundamentals/metapackage) reference in the *.csproj* file replaces the collection of packages:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=8-10)]

<span data-ttu-id="3d32c-125">该元包中具备 ASP.NET Core 2.0 和 Entity Framework Core 2.0 的所有功能。</span><span class="sxs-lookup"><span data-stu-id="3d32c-125">All the features of ASP.NET Core 2.0 and Entity Framework Core 2.0 are included in the metapackage.</span></span>

<span data-ttu-id="3d32c-126">面向 .NET Framework 的 ASP.NET Core 2.0 项目应继续引用单个 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="3d32c-126">ASP.NET Core 2.0 projects targeting .NET Framework should continue to reference individual NuGet packages.</span></span> <span data-ttu-id="3d32c-127">将每个 `<PackageReference />` 节点的 `Version` 特性更新至 2.0.0。</span><span class="sxs-lookup"><span data-stu-id="3d32c-127">Update the `Version` attribute of each `<PackageReference />` node to 2.0.0.</span></span>

<span data-ttu-id="3d32c-128">例如，下述列表列出了面向 .NET Framework 的典型 ASP.NET Core 2.0 项目中使用的 `<PackageReference />` 节点：</span><span class="sxs-lookup"><span data-stu-id="3d32c-128">For example, here's the list of `<PackageReference />` nodes used in a typical ASP.NET Core 2.0 project targeting .NET Framework:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=9-22)]

<a name="dot-net-cli-tool-reference"></a>

## <a name="update-net-core-cli-tools"></a><span data-ttu-id="3d32c-129">更新 .NET Core CLI 工具</span><span class="sxs-lookup"><span data-stu-id="3d32c-129">Update .NET Core CLI tools</span></span>

<span data-ttu-id="3d32c-130">在“.csproj”文件中，将每个 `<DotNetCliToolReference />` 节点的 `Version` 特性更新至 2.0.0  。</span><span class="sxs-lookup"><span data-stu-id="3d32c-130">In the *.csproj* file, update the `Version` attribute of each `<DotNetCliToolReference />` node to 2.0.0.</span></span>

<span data-ttu-id="3d32c-131">例如，下述列表列出了面向 .NET Core 2.0 的典型 ASP.NET Core 2.0 项目中使用的 CLI 工具：</span><span class="sxs-lookup"><span data-stu-id="3d32c-131">For example, here's the list of CLI tools used in a typical ASP.NET Core 2.0 project targeting .NET Core 2.0:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=12-16)]

<a name="package-target-fallback"></a>

## <a name="rename-package-target-fallback-property"></a><span data-ttu-id="3d32c-132">重命名“包目标回退”属性</span><span class="sxs-lookup"><span data-stu-id="3d32c-132">Rename Package Target Fallback property</span></span>

<span data-ttu-id="3d32c-133">1\.x 项目的“.csproj”文件使用了 `PackageTargetFallback` 节点和变量  ：</span><span class="sxs-lookup"><span data-stu-id="3d32c-133">The *.csproj* file of a 1.x project used a `PackageTargetFallback` node and variable:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=5)]

<span data-ttu-id="3d32c-134">将节点和变量重命名为 `AssetTargetFallback`：</span><span class="sxs-lookup"><span data-stu-id="3d32c-134">Rename both the node and variable to `AssetTargetFallback`:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App.csproj?range=4)]

<a name="program-cs"></a>

## <a name="update-main-method-in-programcs"></a><span data-ttu-id="3d32c-135">更新 Program.cs 中的 Main 方法</span><span class="sxs-lookup"><span data-stu-id="3d32c-135">Update Main method in Program.cs</span></span>

<span data-ttu-id="3d32c-136">在 1.x 项目中，“Program.cs”的 `Main` 方法如下所示  ：</span><span class="sxs-lookup"><span data-stu-id="3d32c-136">In 1.x projects, the `Main` method of *Program.cs* looked like this:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCs&highlight=8-19)]

<span data-ttu-id="3d32c-137">在 2.0 项目中，简化了“Program.cs”的 `Main` 方法  ：</span><span class="sxs-lookup"><span data-stu-id="3d32c-137">In 2.0 projects, the `Main` method of *Program.cs* has been simplified:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program.cs?highlight=8-11)]

<span data-ttu-id="3d32c-138">强烈建议采用新的 2.0 模式，[Entity Framework (EF) Core 迁移](xref:data/ef-mvc/migrations)等产品功能需要此模式才能正常运行。</span><span class="sxs-lookup"><span data-stu-id="3d32c-138">The adoption of this new 2.0 pattern is highly recommended and is required for product features like [Entity Framework (EF) Core Migrations](xref:data/ef-mvc/migrations) to work.</span></span> <span data-ttu-id="3d32c-139">例如，从“包管理器控制台”窗口运行 `Update-Database`，或从命令行（位于转换为 ASP.NET Core 2.0 的项目上）运行 `dotnet ef database update`时，都会生成以下错误：</span><span class="sxs-lookup"><span data-stu-id="3d32c-139">For example, running `Update-Database` from the Package Manager Console window or `dotnet ef database update` from the command line (on projects converted to ASP.NET Core 2.0) generates the following error:</span></span>

```
Unable to create an object of type '<Context>'. Add an implementation of 'IDesignTimeDbContextFactory<Context>' to the project, or see https://go.microsoft.com/fwlink/?linkid=851728 for additional patterns supported at design time.
```

<a name="add-modify-configuration"></a>

## <a name="add-configuration-providers"></a><span data-ttu-id="3d32c-140">添加配置提供程序</span><span class="sxs-lookup"><span data-stu-id="3d32c-140">Add configuration providers</span></span>

<span data-ttu-id="3d32c-141">在 1.x 项目中，已通过 `Startup` 构造函数将配置提供程序添加到了某个应用。</span><span class="sxs-lookup"><span data-stu-id="3d32c-141">In 1.x projects, adding configuration providers to an app was accomplished via the `Startup` constructor.</span></span> <span data-ttu-id="3d32c-142">涉及的步骤包括创建 `ConfigurationBuilder` 实例、加载适用的提供程序（环境变量、应用设置等）以及初始化 `IConfigurationRoot` 的成员。</span><span class="sxs-lookup"><span data-stu-id="3d32c-142">The steps involved creating an instance of `ConfigurationBuilder`, loading applicable providers (environment variables, app settings, etc.), and initializing a member of `IConfigurationRoot`.</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_1xStartup)]

<span data-ttu-id="3d32c-143">上例使用 psettings.json 以及任何与 `IHostingEnvironment.EnvironmentName` 属性匹配的 appsettings.\<EnvironmentName\>.json 文件中的配置设置加载 `Configuration` 成员   。</span><span class="sxs-lookup"><span data-stu-id="3d32c-143">The preceding example loads the `Configuration` member with configuration settings from *appsettings.json* as well as any *appsettings.\<EnvironmentName\>.json* file matching the `IHostingEnvironment.EnvironmentName` property.</span></span> <span data-ttu-id="3d32c-144">这些文件所在位置与 Startup.cs  的路径相同。</span><span class="sxs-lookup"><span data-stu-id="3d32c-144">The location of these files is at the same path as *Startup.cs*.</span></span>

<span data-ttu-id="3d32c-145">在 2.0 项目中，样板配置代码会继承在幕后运行的 1.x 代码。</span><span class="sxs-lookup"><span data-stu-id="3d32c-145">In 2.0 projects, the boilerplate configuration code inherent to 1.x projects runs behind-the-scenes.</span></span> <span data-ttu-id="3d32c-146">例如，启动时就加载环境变量和应用设置。</span><span class="sxs-lookup"><span data-stu-id="3d32c-146">For example, environment variables and app settings are loaded at startup.</span></span> <span data-ttu-id="3d32c-147">等效的 Startup.cs  代码减少到 `IConfiguration` 初始化设置并包括插入的实例：</span><span class="sxs-lookup"><span data-stu-id="3d32c-147">The equivalent *Startup.cs* code is reduced to `IConfiguration` initialization with the injected instance:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Startup.cs?name=snippet_2xStartup)]

<span data-ttu-id="3d32c-148">若要删除由 `WebHostBuilder.CreateDefaultBuilder` 添加的默认提供程序，请对 `ConfigureAppConfiguration` 内的 `IConfigurationBuilder.Sources`属性调用 `Clear` 方法。</span><span class="sxs-lookup"><span data-stu-id="3d32c-148">To remove the default providers added by `WebHostBuilder.CreateDefaultBuilder`, invoke the `Clear` method on the `IConfigurationBuilder.Sources` property inside of `ConfigureAppConfiguration`.</span></span> <span data-ttu-id="3d32c-149">若要添加回提供程序，请使用 Program.cs 中的 `ConfigureAppConfiguration` 方法  ：</span><span class="sxs-lookup"><span data-stu-id="3d32c-149">To add providers back, utilize the `ConfigureAppConfiguration` method in *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/Program.cs?name=snippet_ProgramMainConfigProviders&highlight=9-14)]

<span data-ttu-id="3d32c-150">若要查看上一代码片段中 `CreateDefaultBuilder` 方法使用的配置，请参阅[此处](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152)。</span><span class="sxs-lookup"><span data-stu-id="3d32c-150">The configuration used by the `CreateDefaultBuilder` method in the preceding code snippet can be seen [here](https://github.com/aspnet/MetaPackages/blob/rel/2.0.0/src/Microsoft.AspNetCore/WebHost.cs#L152).</span></span>

<span data-ttu-id="3d32c-151">有关详细信息，请参阅 [ ASP.NET Core 中的配置](xref:fundamentals/configuration/index)。</span><span class="sxs-lookup"><span data-stu-id="3d32c-151">For more information, see [Configuration in ASP.NET Core](xref:fundamentals/configuration/index).</span></span>

<a name="db-init-code"></a>

## <a name="move-database-initialization-code"></a><span data-ttu-id="3d32c-152">移动数据库初始化代码</span><span class="sxs-lookup"><span data-stu-id="3d32c-152">Move database initialization code</span></span>

<span data-ttu-id="3d32c-153">在 1.x 项目中使用 EF Core 1.x（类似 `dotnet ef migrations add` 的命令）执行下列任务：</span><span class="sxs-lookup"><span data-stu-id="3d32c-153">In 1.x projects using EF Core 1.x, a command such as `dotnet ef migrations add` does the following:</span></span>

1. <span data-ttu-id="3d32c-154">实例化 `Startup` 实例</span><span class="sxs-lookup"><span data-stu-id="3d32c-154">Instantiates a `Startup` instance</span></span>
1. <span data-ttu-id="3d32c-155">调用 `ConfigureServices` 方法，为所有服务注册依赖关系注入（包括 `DbContext` 类型）</span><span class="sxs-lookup"><span data-stu-id="3d32c-155">Invokes the `ConfigureServices` method to register all services with dependency injection (including `DbContext` types)</span></span>
1. <span data-ttu-id="3d32c-156">执行其必要任务</span><span class="sxs-lookup"><span data-stu-id="3d32c-156">Performs its requisite tasks</span></span>

<span data-ttu-id="3d32c-157">在 2.0 项目中使用 EF Core 2.0，调用 `Program.BuildWebHost` 以获取应用程序服务。</span><span class="sxs-lookup"><span data-stu-id="3d32c-157">In 2.0 projects using EF Core 2.0, `Program.BuildWebHost` is invoked to obtain the application services.</span></span> <span data-ttu-id="3d32c-158">与 1.x 不同，这有调用 `Startup.Configure` 的副作用。</span><span class="sxs-lookup"><span data-stu-id="3d32c-158">Unlike 1.x, this has the additional side effect of invoking `Startup.Configure`.</span></span> <span data-ttu-id="3d32c-159">如果 1.x 应用在其 `Configure` 方法中调用了数据库初始化代码，可能出现意外问题。</span><span class="sxs-lookup"><span data-stu-id="3d32c-159">If your 1.x app invoked database initialization code in its `Configure` method, unexpected problems can occur.</span></span> <span data-ttu-id="3d32c-160">例如，如果数据库尚不存在，种子设定代码将在 EF Core 迁移命令执行前运行。</span><span class="sxs-lookup"><span data-stu-id="3d32c-160">For example, if the database doesn't yet exist, the seeding code runs before the EF Core Migrations command execution.</span></span> <span data-ttu-id="3d32c-161">如果数据库尚不存在，此问题将导致 `dotnet ef migrations list` 命令失败。</span><span class="sxs-lookup"><span data-stu-id="3d32c-161">This problem causes a `dotnet ef migrations list` command to fail if the database doesn't yet exist.</span></span>

<span data-ttu-id="3d32c-162">请考虑在 *Startup.cs* 的 `Configure` 方法中使用以下 1.x 种子初始化代码。</span><span class="sxs-lookup"><span data-stu-id="3d32c-162">Consider the following 1.x seed initialization code in the `Configure` method of *Startup.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Startup.cs?name=snippet_ConfigureSeedData&highlight=8)]

<span data-ttu-id="3d32c-163">在 2.0 项目中，将 `SeedData.Initialize` 调用移动到 *Program.cs* 的 `Main` 方法：</span><span class="sxs-lookup"><span data-stu-id="3d32c-163">In 2.0 projects, move the `SeedData.Initialize` call to the `Main` method of *Program.cs*:</span></span>

[!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore2App/AspNetCoreDotNetCore2App/Program2.cs?name=snippet_Main2Code&highlight=10)]

<span data-ttu-id="3d32c-164">从 2.0 开始，`BuildWebHost` 只应用于生成和配置 Web 主机。</span><span class="sxs-lookup"><span data-stu-id="3d32c-164">As of 2.0, it's bad practice to do anything in `BuildWebHost` except build and configure the web host.</span></span> <span data-ttu-id="3d32c-165">有关运行应用程序的所有内容都应在 `BuildWebHost` 外部处理 &mdash; 通常是在 Program.cs 的 `Main` 方法中  。</span><span class="sxs-lookup"><span data-stu-id="3d32c-165">Anything that's about running the application should be handled outside of `BuildWebHost` &mdash; typically in the `Main` method of *Program.cs*.</span></span>

<a name="view-compilation"></a>

## <a name="review-razor-view-compilation-setting"></a><span data-ttu-id="3d32c-166">查看 Razor 视图编译设置</span><span class="sxs-lookup"><span data-stu-id="3d32c-166">Review Razor view compilation setting</span></span>

<span data-ttu-id="3d32c-167">加快应用程序启动速度和缩小已发布的捆绑包至关重要。</span><span class="sxs-lookup"><span data-stu-id="3d32c-167">Faster application startup time and smaller published bundles are of utmost importance to you.</span></span> <span data-ttu-id="3d32c-168">为此，ASP.NET Core 2.0 中默认启用 [Razor 视图编译](xref:mvc/views/view-compilation)。</span><span class="sxs-lookup"><span data-stu-id="3d32c-168">For these reasons, [Razor view compilation](xref:mvc/views/view-compilation) is enabled by default in ASP.NET Core 2.0.</span></span>

<span data-ttu-id="3d32c-169">无需再将 `MvcRazorCompileOnPublish` 属性设置为 true。</span><span class="sxs-lookup"><span data-stu-id="3d32c-169">Setting the `MvcRazorCompileOnPublish` property to true is no longer required.</span></span> <span data-ttu-id="3d32c-170">若不禁用视图编译，可能会从“.csproj”文件中删除此属性  。</span><span class="sxs-lookup"><span data-stu-id="3d32c-170">Unless you're disabling view compilation, the property may be removed from the *.csproj* file.</span></span>

<span data-ttu-id="3d32c-171">以 .NET Framework 为目标时，仍需显式引用“.csproj”文件中的 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet 包  ：</span><span class="sxs-lookup"><span data-stu-id="3d32c-171">When targeting .NET Framework, you still need to explicitly reference the [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation) NuGet package in your *.csproj* file:</span></span>

[!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App/AspNetCoreDotNetFx2.0App.csproj?range=15)]

<a name="app-insights"></a>

## <a name="rely-on-application-insights-light-up-features"></a><span data-ttu-id="3d32c-172">依靠 Application Insights“启动”功能</span><span class="sxs-lookup"><span data-stu-id="3d32c-172">Rely on Application Insights "light-up" features</span></span>

<span data-ttu-id="3d32c-173">能够轻松设置应用程序性能检测非常重要。</span><span class="sxs-lookup"><span data-stu-id="3d32c-173">Effortless setup of application performance instrumentation is important.</span></span> <span data-ttu-id="3d32c-174">现可依靠 Visual Studio 2017 工具中推出的新的 [Application Insights](/azure/application-insights/app-insights-overview)“启动”功能。</span><span class="sxs-lookup"><span data-stu-id="3d32c-174">You can now rely on the new [Application Insights](/azure/application-insights/app-insights-overview) "light-up" features available in the Visual Studio 2017 tooling.</span></span>

<span data-ttu-id="3d32c-175">Visual Studio 2017 中创建的 ASP.NET Core 1.1 项目默认添加 Application Insights。</span><span class="sxs-lookup"><span data-stu-id="3d32c-175">ASP.NET Core 1.1 projects created in Visual Studio 2017 added Application Insights by default.</span></span> <span data-ttu-id="3d32c-176">若不直接使用 Application Insights SDK，则除了执行“Program.cs”和“Startup.cs”，还请执行以下步骤   ：</span><span class="sxs-lookup"><span data-stu-id="3d32c-176">If you're not using the Application Insights SDK directly, outside of *Program.cs* and *Startup.cs*, follow these steps:</span></span>

1. <span data-ttu-id="3d32c-177">如果定目标到 .NET Core，请从 .csproj  文件中删除以下 `<PackageReference />` 节点：</span><span class="sxs-lookup"><span data-stu-id="3d32c-177">If targeting .NET Core, remove the following `<PackageReference />` node from the *.csproj* file:</span></span>

    [!code-xml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App.csproj?range=10)]

2. <span data-ttu-id="3d32c-178">如果定目标到 .NET Core，请从 Program.cs  中删除 `UseApplicationInsights` 扩展方法调用：</span><span class="sxs-lookup"><span data-stu-id="3d32c-178">If targeting .NET Core, remove the `UseApplicationInsights` extension method invocation from *Program.cs*:</span></span>

    [!code-csharp[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Program.cs?name=snippet_ProgramCsMain&highlight=8)]

3. <span data-ttu-id="3d32c-179">从“_Layout.cshtml”中删除 Application Insights 客户端 API 调用  。</span><span class="sxs-lookup"><span data-stu-id="3d32c-179">Remove the Application Insights client-side API call from *_Layout.cshtml*.</span></span> <span data-ttu-id="3d32c-180">它会比较以下两行代码：</span><span class="sxs-lookup"><span data-stu-id="3d32c-180">It comprises the following two lines of code:</span></span>

    [!code-cshtml[](../1x-to-2x/samples/AspNetCoreDotNetCore1App/AspNetCoreDotNetCore1App/Views/Shared/_Layout.cshtml?range=1,19&dedent=4)]

<span data-ttu-id="3d32c-181">若要直接使用 Application Insights SDK，请继续此操作。</span><span class="sxs-lookup"><span data-stu-id="3d32c-181">If you are using the Application Insights SDK directly, continue to do so.</span></span> <span data-ttu-id="3d32c-182">2\.0 [元包](xref:fundamentals/metapackage)中具备最新版本的 Application Insights，因此如果引用较旧版本，将出现包降级错误。</span><span class="sxs-lookup"><span data-stu-id="3d32c-182">The 2.0 [metapackage](xref:fundamentals/metapackage) includes the latest version of Application Insights, so a package downgrade error appears if you're referencing an older version.</span></span>

<a name="auth-and-identity"></a>

## <a name="adopt-authenticationidentity-improvements"></a><span data-ttu-id="3d32c-183">采用身份验证/Identity 改进</span><span class="sxs-lookup"><span data-stu-id="3d32c-183">Adopt authentication/Identity improvements</span></span>

<span data-ttu-id="3d32c-184">ASP.NET Core 2.0 具有新的身份验证模型和大量针对 ASP.NET Core Identity 的重大更改。</span><span class="sxs-lookup"><span data-stu-id="3d32c-184">ASP.NET Core 2.0 has a new authentication model and a number of significant changes to ASP.NET Core Identity.</span></span> <span data-ttu-id="3d32c-185">如果在启用个人用户帐户的情况下创建了项目，或者已手动添加身份验证或 Identity，请参阅[将身份验证和 Identity 迁移到 ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x)。</span><span class="sxs-lookup"><span data-stu-id="3d32c-185">If you created your project with Individual User Accounts enabled, or if you have manually added authentication or Identity, see [Migrate Authentication and Identity to ASP.NET Core 2.0](xref:migration/1x-to-2x/identity-2x).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3d32c-186">其他资源</span><span class="sxs-lookup"><span data-stu-id="3d32c-186">Additional resources</span></span>

* [<span data-ttu-id="3d32c-187">ASP.NET Core 2.0 中的重大更改</span><span class="sxs-lookup"><span data-stu-id="3d32c-187">Breaking Changes in ASP.NET Core 2.0</span></span>](https://github.com/aspnet/announcements/issues?page=1&q=is%3Aissue+is%3Aopen+label%3A2.0.0+label%3A%22Breaking+change%22&utf8=%E2%9C%93)
