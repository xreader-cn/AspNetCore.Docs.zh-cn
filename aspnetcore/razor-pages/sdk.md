---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: 了解 ASP.NET Core 中的 Razor 页面如何使基于页面的编码方式比使用 MVC 更简单高效。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 08/23/2019
no-loc:
- Blazor
uid: razor-pages/sdk
ms.openlocfilehash: 872d90662494735dc0e4caa01c46fcdcc2606bc6
ms.sourcegitcommit: b5ceb0a46d0254cc3425578116e2290142eec0f0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2020
ms.locfileid: "76809128"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>概述

[!INCLUDE[](~/includes/2.1-SDK.md)]包括`Microsoft.NET.Sdk.Razor`MSBuild SDK (Razor SDK)。 Razor SDK：

::: moniker range=">= aspnetcore-3.0"

* 对于基于 MVC 的 ASP.NET Core 或[Blazor](xref:blazor/index)项目，需要生成、打包和发布包含[Razor](xref:mvc/views/razor)文件的项目。
* 包括一组预定义的目标、属性和项，它们允许自定义 Razor （*cshtml*或*Razor*）文件的编译。

Razor SDK 包括 `Content` 项，其 `Include` 属性设置为 `**\*.cshtml` 和 `**\*.razor` 的组合模式。 发布匹配的文件。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* 针对基于 ASP.NET Core MVC 的项目，围绕包含 [Razor](xref:mvc/views/razor) 文件的项目的生成、打包和发布设定了体验标准。
* 包含一组预定义的目标、属性和项目，它们允许自定义 Razor 文件的编译。

Razor SDK 包括 `Content` 项，其 `Include` 属性设置为 `**\*.cshtml` 的任意组合模式。 发布匹配的文件。

::: moniker-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>使用 Razor SDK

大多数 web 应用程序无需显式引用 Razor SDK。

::: moniker range=">= aspnetcore-3.0"

若要使用 Razor SDK 生成包含 Razor 视图或 Razor Pages 的类库，我们建议从 Razor 类库（RCL）项目模板开始。 用于生成 Blazor （*razor*）文件的 RCL 最低要求对[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Components)包的引用。 用于生成 Razor 视图或页面（*cshtml*文件）的 RCL 最低要求目标 `netcoreapp3.0` 或更高版本，并在其项目文件中具有[AspNetCore 元包](xref:fundamentals/metapackage-app)的 `FrameworkReference`。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

要使用 Razor SDK 来生成包含 Razor 视图或 Razor 页面的类库，请执行以下操作：

* 使用 `Microsoft.NET.Sdk.Razor` 而非 `Microsoft.NET.Sdk`：

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* 通常情况下，对的包引用`Microsoft.AspNetCore.Mvc`，才能接收生成和编译 Razor 页面和 Razor 视图所需的附加依赖项。 至少，你的项目应添加到包引用：

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design`包提供的 Razor 编译任务和目标项目。

  前面的包包含在 `Microsoft.AspNetCore.Mvc` 中。 以下标记显示了使用 Razor SDK 用于生成 ASP.NET Core Razor 页面应用程序的 Razor 文件的项目文件：

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design`并`Microsoft.AspNetCore.Mvc.Razor.Extensions`包中包含[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。 但是，与版本无关`Microsoft.AspNetCore.App`包引用提供一个元包不包括的最新版本的应用程序到`Microsoft.AspNetCore.Razor.Design`。 项目必须引用的一致版本`Microsoft.AspNetCore.Razor.Design`(或`Microsoft.AspNetCore.Mvc`)，以便包含 razor 的最新生成时修补程序。 有关详细信息，请参阅[此 GitHub 问题](https://github.com/aspnet/Razor/issues/2553)。

::: moniker-end

### <a name="properties"></a>属性

以下属性控制项目生成过程中 Razor 的 SDK 行为：

* `RazorCompileOnBuild` &ndash; 当`true`、 编译并发出作为生成项目的一部分 Razor 程序集。 默认为 `true`。
* `RazorCompileOnPublish` &ndash; 当`true`、 编译并发出作为发布项目的一部分 Razor 程序集。 默认为 `true`。

配置输入和输出到 Razor SDK 用于属性和下表中的项。

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> 从 ASP.NET Core 3.0 开始，如果禁用项目文件中的 `RazorCompileOnBuild` 或 `RazorCompileOnPublish` MSBuild 属性，则默认情况下不会为 MVC 视图或 Razor Pages 提供服务。 如果应用程序依赖运行时编译来处理*cshtml*文件，则应用程序必须添加对[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation)包的显式引用。

::: moniker-end

| 项目 | 描述 |
| ----- | ----------- |
| `RazorGenerate` | 作为代码生成的输入的项元素（*cshtml*文件）。 |
| `RazorComponent` | 作为 Razor 组件代码生成的输入的项元素（*razor*文件）。 |
| `RazorCompile` | 作为 Razor 编译目标的输入的项元素（ *.cs*文件）。 使用此 `ItemGroup` 来指定要编译到 Razor 程序集中的其他文件。 |
| `RazorTargetAssemblyAttribute` | 用于编码生成 Razor 程序集属性的项元素。 例如：  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | 作为嵌入资源添加到生成的 Razor 程序集的项元素。 |

| Property | 描述 |
| -------- | ----------- |
| `RazorTargetName` | Razor 生成的程序集的文件名（不含扩展名）。 |
| `RazorOutputPath` | Razor 输出目录。 |
| `RazorCompileToolset` | 用于确定用于生成 Razor 程序集的工具集。 有效值为 `Implicit`、`RazorSDK` 和 `PrecompilationTool`。 |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 默认值为 `true`。 当 `true`*时，将*在项目中包括 web.config、 *json*和*cshtml*文件作为内容。 当通过引用`Microsoft.NET.Sdk.Web`，文件下*wwwroot*和，还提供了配置文件。 |
| `EnableDefaultRazorGenerateItems` | 为 `true` 时，包括 `RazorGenerate` 项中 `Content` 项的 .cshtml 文件。 |
| `GenerateRazorTargetAssemblyInfo` | 当`true`，生成 *.cs*包含指定的属性文件`RazorAssemblyAttribute`和编译输出中包括的文件。 |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | 为 `true` 时，将一组默认的程序集属性添加到 `RazorAssemblyAttribute`。 |
| `CopyRazorGenerateFilesToPublishDirectory` | 当`true`，复制`RazorGenerate`项 ( *.cshtml*) 文件复制到发布目录。 通常情况下，Razor 文件不需要的已发布的应用，如果他们参与在生成时或发布时编译。 默认为 `false`。 |
| `CopyRefAssembliesToPublishDirectory` | 为 `true` 时，将引用程序集项复制到发布目录。 通常情况下，引用程序集不需要的已发布的应用，如果 Razor 编译发生在生成时或发布时间。 设置为`true`如果你已发布的应用需要运行时编译。 例如，将值设置为`true`如果应用程序修改 *.cshtml*文件在运行时或使用嵌入的视图。 默认为 `false`。 |
| `IncludeRazorContentInPack` | 当`true`，所有 Razor 内容项 ( *.cshtml*文件) 标记为要包含在生成的 NuGet 包中。 默认为 `false`。 |
| `EmbedRazorGenerateSources` | 为 `true` 时，将 RazorGenerate (.cshtml) 项作为嵌入的文件添加到生成的 Razor 程序集中。 默认为 `false`。 |
| `UseRazorBuildServer` | 为 `true` 时，使用永久生成服务器进程来卸载代码生成工作。 默认值为 `UseSharedCompilation`。 |
| `GenerateMvcApplicationPartsAssemblyAttributes` | 当 `true`时，SDK 会在运行时生成由 MVC 用于执行应用程序部件发现的附加属性。 |
| `DefaultWebContentItemExcludes` | 要从面向 Web 或 Razor SDK 的项目中的 `Content` 项组中排除的项元素的组合模式 |
| `ExcludeConfigFilesFromBuildOutput` | 如果 `true`，则不会将 *.config*文件和*json*文件复制到生成输出目录。 |
| `AddRazorSupportForMvc` | `true`时，会将 Razor SDK 配置为添加生成包含 MVC 视图或 Razor Pages 的应用程序时所需的 MVC 配置支持。 为针对 Web SDK 的 .NET Core 3.0 或更高版本项目隐式设置此属性 |
| `RazorLangVersion` | 要面向的 Razor 语言的版本。 |

有关属性的详细信息，请参阅 [MSBuild 属性](/visualstudio/msbuild/msbuild-properties)。

### <a name="targets"></a>目标

Razor SDK 定义两个主要目标：

* `RazorGenerate` &ndash; 代码将生成 *.cs*文件从`RazorGenerate`项元素。 使用 `RazorGenerateDependsOn` 属性指定可在此目标之前或之后运行的其他目标。
* `RazorCompile` &ndash; 生成的编译 *.cs*到 Razor 程序集文件中。 使用 `RazorCompileDependsOn` 指定可以在此目标之前或之后运行的其他目标。
* `RazorComponentGenerate` &ndash; 代码为 `RazorComponent` 项元素生成 *.cs*文件。 使用 `RazorComponentGenerateDependsOn` 属性指定可在此目标之前或之后运行的其他目标。

### <a name="runtime-compilation-of-razor-views"></a>Razor 视图的运行时编译

* 默认情况下，Razor SDK 不发布执行运行时编译所需的引用程序集。 当应用程序模型依赖于运行时编译时，这会导致编译失败&mdash;例如，应用在发布后使用嵌入视图或更改视图。 将 `CopyRefAssembliesToPublishDirectory` 设置为 `true`，以继续发布引用程序集。

* 对于 web 应用，请确保您的应用程序所面向`Microsoft.NET.Sdk.Web`SDK。

## <a name="razor-language-version"></a>Razor 语言版本

以 `Microsoft.NET.Sdk.Web` SDK 为目标时，将从应用的目标框架版本中推断 Razor 语言版本。 对于面向 `Microsoft.NET.Sdk.Razor` SDK 的项目，或在应用需要不同于推断值的 Razor 语言版本的情况下，可以通过在应用的项目文件中设置 `<RazorLangVersion>` 属性来配置版本：

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razor 的语言版本与生成它的运行时版本紧密集成。 针对不是为运行时设计的语言版本，不受支持，并且可能会产生生成错误。

## <a name="additional-resources"></a>其他资源

* [.NET Core 的 csproj 格式的新增内容](/dotnet/core/tools/csproj)
* [常用的 MSBuild 项目项](/visualstudio/msbuild/common-msbuild-project-items)
