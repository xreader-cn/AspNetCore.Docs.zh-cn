---
title: 使用文件观察程序开发 ASP.NET Core 应用
author: rick-anderson
description: 本教程演示如何在 ASP.NET Core 应用中安装和使用 .NET Core CLI 的文件观察程序 (dotnet watch) 工具。
ms.author: riande
ms.date: 05/31/2018
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/dotnet-watch
ms.openlocfilehash: 17fc7fd2d65fd314d9f6f9530db5d511af248569
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776586"
---
# <a name="develop-aspnet-core-apps-using-a-file-watcher"></a>使用文件观察程序开发 ASP.NET Core 应用

作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Victor Hurdugaci](https://twitter.com/victorhurdugaci)

[dotnet watch](https://www.nuget.org/packages/dotnet-watch) 是一种在源文件更改时运行 [.NET Core CLI](/dotnet/core/tools) 命令的工具。 例如，文件更改可能触发编译、测试执行或部署。

本教程使用一个现有 Web API 和两个终结点：分别返回两个数的总和以及乘积。 乘积的方法有一个 bug，本教程将会对其进行修复。

下载[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/dotnet-watch/sample)。 它包含两个项目：WebApp（一种 ASP.NET Core Web API）和 WebAppTests（Web API 的单元测试）   。

在命令行界面中，导航到 WebApp 文件夹  。 运行下面的命令：

```dotnetcli
dotnet run
```

> [!NOTE]
> 可使用 `dotnet run --project <PROJECT>` 来指定要运行的项目。 例如，从示例应用的根路径运行 `dotnet run --project WebApp` 还会运行 WebApp  项目。

控制台输出会显示如下类似的消息（表示应用正在运行且正在等待请求）：

```console
$ dotnet run
Hosting environment: Development
Content root path: C:/Docs/aspnetcore/tutorials/dotnet-watch/sample/WebApp
Now listening on: http://localhost:5000
Application started. Press Ctrl+C to shut down.
```

在 Web 浏览器中，导航到 `http://localhost:<port number>/api/math/sum?a=4&b=5`。 应该会显示结果 `9`。

导航到产品 API (`http://localhost:<port number>/api/math/product?a=4&b=5`)。 它会返回 `9`，而不是所预期的 `20`。 本教程的后面部分将解决该问题。

::: moniker range="<= aspnetcore-2.0"

## <a name="add-dotnet-watch-to-a-project"></a>将 `dotnet watch` 添加到项目

`dotnet watch` 文件观察程序工具随 2.1.300 版本的 .NET Core SDK 一同提供。 使用早期版本的 .NET Core SDK 时，需要执行以下步骤。

1. 将 `Microsoft.DotNet.Watcher.Tools` 包引用添加到 .csproj 文件  ：

    ```xml
    <ItemGroup>
        <DotNetCliToolReference Include="Microsoft.DotNet.Watcher.Tools" Version="2.0.0" />
    </ItemGroup>
    ```

1. 运行以下命令，安装 `Microsoft.DotNet.Watcher.Tools` 包：

    ```dotnetcli
    dotnet restore
    ```

::: moniker-end

## <a name="run-net-core-cli-commands-using-dotnet-watch"></a>使用 `dotnet watch` 运行 .NET Core CLI 命令

[ 可用于运行任何 ](/dotnet/core/tools#cli-commands).NET Core CLI 命令`dotnet watch` 例如:

| 命令 | 带 watch 的命令 |
| ---- | ----- |
| dotnet run | dotnet watch run |
| dotnet run -f netcoreapp2.0 | dotnet watch run -f netcoreapp2.0 |
| dotnet run -f netcoreapp2.0 -- --arg1 | dotnet watch run -f netcoreapp2.0 -- --arg1 |
| dotnet test | dotnet watch test |

运行“WebApp”文件夹中的 `dotnet watch run`  。 控制台输出指示 `watch` 已启动。

> [!NOTE]
> 可使用 `dotnet watch --project <PROJECT>` 来指定要监视的项目。 例如，从示例应用的根路径运行 `dotnet watch --project WebApp run` 还会运行并监视 WebApp  项目。

## <a name="make-changes-with-dotnet-watch"></a>使用 `dotnet watch` 执行更改

确保 `dotnet watch` 正在运行。

修复 MathController 的 `Product` 方法中的 bug，使其返回乘积而非总和  。

```csharp
public static int Product(int a, int b)
{
    return a * b;
}
```

保存该文件。 控制台输出指示 `dotnet watch` 已检测到文件更改并已重启应用。

验证 `http://localhost:<port number>/api/math/product?a=4&b=5` 是否返回正确结果。

## <a name="run-tests-using-dotnet-watch"></a>使用 `dotnet watch` 运行测试

1. 将 MathController.cs 的 `Product` 方法改回返回总和  。 保存该文件。
1. 在命令外壳中，导航到“WebAppTests”文件夹  。
1. 运行 [dotnet restore](/dotnet/core/tools/dotnet-restore)。
1. 运行 `dotnet watch test`。 其输出指示测试失败且观察程序正在等待文件更改：

     ```console
     Total tests: 2. Passed: 1. Failed: 1. Skipped: 0.
     Test Run Failed.
     ```

1. 修复 `Product` 方法代码，使其返回乘积。 保存该文件。

`dotnet watch` 检测到文件更改并重新运行测试。 控制台输出指示测试通过。

## <a name="customize-files-list-to-watch"></a>自定义要监视的文件列表

默认情况下，`dotnet-watch` 跟踪与以下 glob 模式匹配的所有文件：

* `**/*.cs`
* `*.csproj`
* `**/*.resx`

通过编辑 .csproj 文件，可向监视列表添加更多项  。 可以单独指定项或使用 glob 模式指定。

```xml
<ItemGroup>
    <!-- extends watching group to include *.js files -->
    <Watch Include="**\*.js" Exclude="node_modules\**\*;**\*.js.map;obj\**\*;bin\**\*" />
</ItemGroup>
```

## <a name="opt-out-of-files-to-be-watched"></a>从要监视的文件中排除

`dotnet-watch` 可配置为忽略其默认设置。 要忽略特定文件，请在 .csproj 文件中向某项的定义中添加 `Watch="false"` 特性  ：

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

## <a name="custom-watch-projects"></a>自定义监视项目

`dotnet-watch` 不仅限于 C# 项目。 可以创建自定义监视项目来处理不同的方案。 假设项目布局如下：

* **test/**
  * *UnitTests/UnitTests.csproj*
  * *IntegrationTests/IntegrationTests.csproj*

如果目标是监视这两个项目，请创建配置为监视这两个项目的自定义项目文件：

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

要对这两个项目启动文件监视，请更改为 test 文件夹  。 请执行以下命令：

```dotnetcli
dotnet watch msbuild /t:Test
```

当任一测试项目中的任何文件发生更改时，将会执行 VSTest。

## <a name="dotnet-watch-in-github"></a>GitHub 中的 `dotnet-watch`

`dotnet-watch` 是 GitHub [dotnet/AspNetCore 存储库](https://github.com/dotnet/AspNetCore/tree/master/src/Tools/dotnet-watch)的一部分。
