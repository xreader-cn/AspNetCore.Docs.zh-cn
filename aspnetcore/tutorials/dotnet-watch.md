---
title: 使用文件观察程序开发 ASP.NET Core 应用
author: rick-anderson
description: 本教程演示如何在 ASP.NET Core 应用中安装和使用 .NET Core CLI 的文件观察程序 (dotnet watch) 工具。
ms.author: riande
ms.date: 05/31/2018
uid: tutorials/dotnet-watch
ms.openlocfilehash: f1e0d91b27df4af7cbfb6f2547c94c0370c65d0d
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207497"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a><span data-ttu-id="33a81-103">使用文件观察程序开发 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="33a81-103">Develop ASP.NET Core apps using a file watcher</span></span>

<span data-ttu-id="33a81-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span><span class="sxs-lookup"><span data-stu-id="33a81-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Victor Hurdugaci](https://twitter.com/victorhurdugaci)</span></span>

<span data-ttu-id="33a81-105">`dotnet watch` 是一种在源文件更改时运行 [.NET Core CLI](/dotnet/core/tools) 命令的工具。</span><span class="sxs-lookup"><span data-stu-id="33a81-105">`dotnet watch` is a tool that runs a [.NET Core CLI](/dotnet/core/tools) command when source files change.</span></span> <span data-ttu-id="33a81-106">例如，文件更改可能触发编译、测试执行或部署。</span><span class="sxs-lookup"><span data-stu-id="33a81-106">For example, a file change can trigger compilation, test execution, or deployment.</span></span>

<span data-ttu-id="33a81-107">本教程使用现有 Web API，它具有两个终结点：分别返回总和和产品。</span><span class="sxs-lookup"><span data-stu-id="33a81-107">This tutorial uses an existing web API with two endpoints: one that returns a sum and one that returns a product.</span></span> <span data-ttu-id="33a81-108">该产品方法有一个 bug，本教程已修复。</span><span class="sxs-lookup"><span data-stu-id="33a81-108">The product method has a bug, which is fixed in this tutorial.</span></span>

<span data-ttu-id="33a81-109">下载[示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)。</span><span class="sxs-lookup"><span data-stu-id="33a81-109">Download the [sample app](https://github.com/aspnet/Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample).</span></span> <span data-ttu-id="33a81-110">它包含两个项目：WebApp (ASP.NET Core Web API) 和 WebAppTests（用于 Web API 的单元测试）。</span><span class="sxs-lookup"><span data-stu-id="33a81-110">It consists of two projects: *WebApp* (an ASP.NET Core web API) and *WebAppTests* (unit tests for the web API).</span></span>

<span data-ttu-id="33a81-111">在命令行界面中，导航到 WebApp 文件夹。</span><span class="sxs-lookup"><span data-stu-id="33a81-111">In a command shell, navigate to the *WebApp* folder.</span></span> <span data-ttu-id="33a81-112">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="33a81-112">Run the following command:</span></span>

```console
dotnet run
```

<span data-ttu-id="33a81-113">控制台输出会显示如下类似的消息（表示应用正在运行且正在等待请求）：</span><span class="sxs-lookup"><span data-stu-id="33a81-113">The console output shows messages similar to the following (indicating that the app is running and awaiting requests):</span></span>

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

<span data-ttu-id="33a81-114">在 Web 浏览器中，导航到 `http://localhost:<port number>/api/math/sum?a=4&b=5`。</span><span class="sxs-lookup"><span data-stu-id="33a81-114">In a web browser, navigate to `http://localhost:<port number>/api/math/sum?a=4&b=5`.</span></span> <span data-ttu-id="33a81-115">应该会显示结果 `9`。</span><span class="sxs-lookup"><span data-stu-id="33a81-115">You should see the result of `9`.</span></span>

<span data-ttu-id="33a81-116">导航到产品 API (`http://localhost:<port number>/api/math/product?a=4&b=5`)。</span><span class="sxs-lookup"><span data-stu-id="33a81-116">Navigate to the product API (`http://localhost:<port number>/api/math/product?a=4&b=5`).</span></span> <span data-ttu-id="33a81-117">它会返回 `9`，而不是所预期的 `20`。</span><span class="sxs-lookup"><span data-stu-id="33a81-117">It returns `9`, not `20` as you'd expect.</span></span> <span data-ttu-id="33a81-118">本教程的后面部分将解决该问题。</span><span class="sxs-lookup"><span data-stu-id="33a81-118">That problem is fixed later in the tutorial.</span></span>

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a><span data-ttu-id="33a81-119">将 `dotnet watch` 添加到项目</span><span class="sxs-lookup"><span data-stu-id="33a81-119">Add `dotnet watch` to a project</span></span>

<span data-ttu-id="33a81-120">`dotnet watch` 文件观察程序工具随 2.1.300 版本的 .NET Core SDK 一同提供。</span><span class="sxs-lookup"><span data-stu-id="33a81-120">The `dotnet watch` file watcher tool is included with version 2.1.300 of the .NET Core SDK.</span></span> <span data-ttu-id="33a81-121">使用早期版本的 .NET Core SDK 时，需要执行以下步骤。</span><span class="sxs-lookup"><span data-stu-id="33a81-121">The following steps are required when using an earlier version of the .NET Core SDK.</span></span>

1. <span data-ttu-id="33a81-122">将 `Microsoft.DotNet.Watcher.Tools` 包引用添加到 .csproj 文件：</span><span class="sxs-lookup"><span data-stu-id="33a81-122">Add a `Microsoft.DotNet.Watcher.Tools` package reference to the *.csproj* file:</span></span>

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. <span data-ttu-id="33a81-123">运行以下命令，安装 `Microsoft.DotNet.Watcher.Tools` 包：</span><span class="sxs-lookup"><span data-stu-id="33a81-123">Install the `Microsoft.DotNet.Watcher.Tools` package by running the following command:</span></span>

    ```console
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a><span data-ttu-id="33a81-124">使用 `dotnet watch` 运行 .NET Core CLI 命令</span><span class="sxs-lookup"><span data-stu-id="33a81-124">Run .NET Core CLI commands using `dotnet watch`</span></span>

<span data-ttu-id="33a81-125">`dotnet watch` 可用于运行任何 [.NET Core CLI 命令](/dotnet/core/tools#cli-commands)</span><span class="sxs-lookup"><span data-stu-id="33a81-125">Any [.NET Core CLI command](/dotnet/core/tools#cli-commands) can be run with `dotnet watch`.</span></span> <span data-ttu-id="33a81-126">例如:</span><span class="sxs-lookup"><span data-stu-id="33a81-126">For example:</span></span>

| <span data-ttu-id="33a81-127">命令</span><span class="sxs-lookup"><span data-stu-id="33a81-127">Command</span></span> | <span data-ttu-id="33a81-128">带 watch 的命令</span><span class="sxs-lookup"><span data-stu-id="33a81-128">Command with watch</span></span> |
| ---- | ----- |
| <span data-ttu-id="33a81-129">dotnet 运行</span><span class="sxs-lookup"><span data-stu-id="33a81-129">dotnet run</span></span> | <span data-ttu-id="33a81-130">dotnet watch run</span><span class="sxs-lookup"><span data-stu-id="33a81-130">dotnet watch run</span></span> |
| <span data-ttu-id="33a81-131">dotnet run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="33a81-131">dotnet run -f netcoreapp2.0</span></span> | <span data-ttu-id="33a81-132">dotnet watch run -f netcoreapp2.0</span><span class="sxs-lookup"><span data-stu-id="33a81-132">dotnet watch run -f netcoreapp2.0</span></span> |
| <span data-ttu-id="33a81-133">dotnet run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="33a81-133">dotnet run -f netcoreapp2.0 -- --arg1</span></span> | <span data-ttu-id="33a81-134">dotnet watch run -f netcoreapp2.0 -- --arg1</span><span class="sxs-lookup"><span data-stu-id="33a81-134">dotnet watch run -f netcoreapp2.0 -- --arg1</span></span> |
| <span data-ttu-id="33a81-135">dotnet test</span><span class="sxs-lookup"><span data-stu-id="33a81-135">dotnet test</span></span> | <span data-ttu-id="33a81-136">dotnet watch test</span><span class="sxs-lookup"><span data-stu-id="33a81-136">dotnet watch test</span></span> |

<span data-ttu-id="33a81-137">运行“WebApp”文件夹中的 `dotnet watch run`。</span><span class="sxs-lookup"><span data-stu-id="33a81-137">Run `dotnet watch run` in the *WebApp* folder.</span></span> <span data-ttu-id="33a81-138">控制台输出指示 `watch` 已启动。</span><span class="sxs-lookup"><span data-stu-id="33a81-138">The console output indicates `watch` has started.</span></span>

## <a name="make-changes-with-dotnet-watch"></a><span data-ttu-id="33a81-139">使用 `dotnet watch` 执行更改</span><span class="sxs-lookup"><span data-stu-id="33a81-139">Make changes with `dotnet watch`</span></span>

<span data-ttu-id="33a81-140">确保 `dotnet watch` 正在运行。</span><span class="sxs-lookup"><span data-stu-id="33a81-140">Make sure `dotnet watch` is running.</span></span>

<span data-ttu-id="33a81-141">修复 MathController 的 `Product` 方法中的 bug，使其返回产品而非总和。</span><span class="sxs-lookup"><span data-stu-id="33a81-141">Fix the bug in the `Product` method of *MathController.cs* so it returns the product and not the sum:</span></span>

```csharp
public static int Product(int a, int b)
{
  return a * b;
}
```

<span data-ttu-id="33a81-142">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="33a81-142">Save the file.</span></span> <span data-ttu-id="33a81-143">控制台输出指示 `dotnet watch` 已检测到文件更改并已重启应用。</span><span class="sxs-lookup"><span data-stu-id="33a81-143">The console output indicates that `dotnet watch` detected a file change and restarted the app.</span></span>

<span data-ttu-id="33a81-144">验证 `http://localhost:<port number>/api/math/product?a=4&b=5` 是否返回正确结果。</span><span class="sxs-lookup"><span data-stu-id="33a81-144">Verify `http://localhost:<port number>/api/math/product?a=4&b=5` returns the correct result.</span></span>

## <a name="run-tests-using-dotnet-watch"></a><span data-ttu-id="33a81-145">使用 `dotnet watch` 运行测试</span><span class="sxs-lookup"><span data-stu-id="33a81-145">Run tests using `dotnet watch`</span></span>

1. <span data-ttu-id="33a81-146">将 MathController.cs 的 `Product` 方法改回返回总和。</span><span class="sxs-lookup"><span data-stu-id="33a81-146">Change the `Product` method of *MathController.cs* back to returning the sum.</span></span> <span data-ttu-id="33a81-147">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="33a81-147">Save the file.</span></span>
1. <span data-ttu-id="33a81-148">在命令外壳中，导航到“WebAppTests”文件夹。</span><span class="sxs-lookup"><span data-stu-id="33a81-148">In a command shell, navigate to the *WebAppTests* folder.</span></span>
1. <span data-ttu-id="33a81-149">运行 [dotnet restore](/dotnet/core/tools/dotnet-restore)。</span><span class="sxs-lookup"><span data-stu-id="33a81-149">Run [dotnet restore](/dotnet/core/tools/dotnet-restore).</span></span>
1. <span data-ttu-id="33a81-150">运行 `dotnet watch test`。</span><span class="sxs-lookup"><span data-stu-id="33a81-150">Run `dotnet watch test`.</span></span> <span data-ttu-id="33a81-151">其输出指示测试失败且观察程序正在等待文件更改：</span><span class="sxs-lookup"><span data-stu-id="33a81-151">Its output indicates that a test failed and that the watcher is awaiting file changes:</span></span>

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. <span data-ttu-id="33a81-152">修复 `Product` 方法代码，使其返回产品。</span><span class="sxs-lookup"><span data-stu-id="33a81-152">Fix the `Product` method code so it returns the product.</span></span> <span data-ttu-id="33a81-153">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="33a81-153">Save the file.</span></span>

<span data-ttu-id="33a81-154">`dotnet watch` 检测到文件更改并重新运行测试。</span><span class="sxs-lookup"><span data-stu-id="33a81-154">`dotnet watch` detects the file change and reruns the tests.</span></span> <span data-ttu-id="33a81-155">控制台输出指示测试通过。</span><span class="sxs-lookup"><span data-stu-id="33a81-155">The console output indicates the tests passed.</span></span>

## <a name="customize-files-list-to-watch"></a><span data-ttu-id="33a81-156">自定义要监视的文件列表</span><span class="sxs-lookup"><span data-stu-id="33a81-156">Customize files list to watch</span></span>

<span data-ttu-id="33a81-157">默认情况下，`dotnet-watch` 跟踪与以下 glob 模式匹配的所有文件：</span><span class="sxs-lookup"><span data-stu-id="33a81-157">By default, `dotnet-watch` tracks all files matching the following glob patterns:</span></span>

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

<span data-ttu-id="33a81-158">通过编辑 .csproj 文件，可向监视列表添加更多项。</span><span class="sxs-lookup"><span data-stu-id="33a81-158">More items can be added to the watch list by editing the *.csproj* file.</span></span> <span data-ttu-id="33a81-159">可以单独指定项或使用 glob 模式指定。</span><span class="sxs-lookup"><span data-stu-id="33a81-159">Items can be specified individually or by using glob patterns.</span></span>

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a><span data-ttu-id="33a81-160">选择退出要监视的文件</span><span class="sxs-lookup"><span data-stu-id="33a81-160">Opt-out of files to be watched</span></span>

<span data-ttu-id="33a81-161">`dotnet-watch` 可配置为忽略其默认设置。</span><span class="sxs-lookup"><span data-stu-id="33a81-161">`dotnet-watch` can be configured to ignore its default settings.</span></span> <span data-ttu-id="33a81-162">要忽略特定文件，请在 .csproj 文件中向某项的定义中添加 `Watch="false"` 特性：</span><span class="sxs-lookup"><span data-stu-id="33a81-162">To ignore specific files, add the `Watch="false"` attribute to an item's definition in the *.csproj* file:</span></span>

```xml
<ItemGroup>
    <!-- exclude Generated.cs from dotnet-watch -->
    <Compile Include="Generated.cs" Watch="false" />

    <!-- exclude Strings.resx from dotnet-watch -->
    <EmbeddedResource Include="Strings.resx" Watch="false" />

    <!-- exclude changes in this referenced project -->
    <ProjectReference Include="..\ClassLibrary1\ClassLibrary1.csproj" Watch="false" />
</ItemGroup>
```

## <a name="custom-watch-projects"></a><span data-ttu-id="33a81-163">自定义监视项目</span><span class="sxs-lookup"><span data-stu-id="33a81-163">Custom watch projects</span></span>

<span data-ttu-id="33a81-164">`dotnet-watch` 不仅限于 C# 项目。</span><span class="sxs-lookup"><span data-stu-id="33a81-164">`dotnet-watch` isn't restricted to C# projects.</span></span> <span data-ttu-id="33a81-165">可以创建自定义监视项目来处理不同的方案。</span><span class="sxs-lookup"><span data-stu-id="33a81-165">Custom watch projects can be created to handle different scenarios.</span></span> <span data-ttu-id="33a81-166">假设项目布局如下：</span><span class="sxs-lookup"><span data-stu-id="33a81-166">Consider the following project layout:</span></span>

* <span data-ttu-id="33a81-167">**test/**</span><span class="sxs-lookup"><span data-stu-id="33a81-167">**test/**</span></span>
  * <span data-ttu-id="33a81-168">*UnitTests/UnitTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="33a81-168">*UnitTests/UnitTests.csproj*</span></span>
  * <span data-ttu-id="33a81-169">*IntegrationTests/IntegrationTests.csproj*</span><span class="sxs-lookup"><span data-stu-id="33a81-169">*IntegrationTests/IntegrationTests.csproj*</span></span>

<span data-ttu-id="33a81-170">如果目标是监视这两个项目，请创建配置为监视这两个项目的自定义项目文件：</span><span class="sxs-lookup"><span data-stu-id="33a81-170">If the goal is to watch both projects, create a custom project file configured to watch both projects:</span></span>

```xml
<Project>
    <ItemGroup>
        <TestProjects Include="**\*.csproj" />
        <Watch Include="**\*.cs" />
    </ItemGroup>

    <Target Name="Test">
        <MSBuild Targets="VSTest" Projects="@(TestProjects)" />
    </Target>

    <Import Project="$(MSBuildExtensionsPath)\Microsoft.Common.targets" />
</Project>
```

<span data-ttu-id="33a81-171">要对这两个项目启动文件监视，请更改为 test 文件夹。</span><span class="sxs-lookup"><span data-stu-id="33a81-171">To start file watching on both projects, change to the *test* folder.</span></span> <span data-ttu-id="33a81-172">请执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="33a81-172">Execute the following command:</span></span>

```console
dotnet watch msbuild /t:Test
```

<span data-ttu-id="33a81-173">当任一测试项目中的任何文件发生更改时，将会执行 VSTest。</span><span class="sxs-lookup"><span data-stu-id="33a81-173">VSTest executes when any file changes in either test project.</span></span>

## <a name="dotnet-watch-in-github"></a><span data-ttu-id="33a81-174">GitHub 中的 `dotnet-watch`</span><span class="sxs-lookup"><span data-stu-id="33a81-174">`dotnet-watch` in GitHub</span></span>

<span data-ttu-id="33a81-175">`dotnet-watch` 是 GitHub [aspnet/AspNetCore 存储库](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch)的一部分。</span><span class="sxs-lookup"><span data-stu-id="33a81-175">`dotnet-watch` is part of the GitHub [aspnet/AspNetCore repository](https://github.com/aspnet/AspNetCore/tree/master/src/Tools/dotnet-watch).</span></span>
