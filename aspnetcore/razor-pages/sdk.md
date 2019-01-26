---
title: ASP.NET Core Razor SDK
author: Rick-Anderson
description: 了解 ASP.NET Core 中的 Razor 页面如何使基于页面的编码方式比使用 MVC 更简单高效。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 10/25/2018
uid: razor-pages/sdk
ms.openlocfilehash: 0e6cfeb1863ed14ffe670cf082e99f28b26718dd
ms.sourcegitcommit: ca5f03210bedc61c6639a734ae5674bfe095dee8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/26/2019
ms.locfileid: "55073096"
---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

[!INCLUDE[](~/includes/2.1-SDK.md)]包括`Microsoft.NET.Sdk.Razor`MSBuild SDK (Razor SDK)。 Razor SDK：

* 针对基于 ASP.NET Core MVC 的项目，围绕包含 [Razor](xref:mvc/views/razor) 文件的项目的生成、打包和发布设定了体验标准。
* 包含一组预定义的目标、属性和项目，它们允许自定义 Razor 文件的编译。

## <a name="prerequisites"></a>系统必备

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="using-the-razor-sdk"></a>使用 Razor SDK

大多数 web 应用程序无需显式引用 Razor SDK。

要使用 Razor SDK 来生成包含 Razor 视图或 Razor 页面的类库，请执行以下操作：

* 使用 `Microsoft.NET.Sdk.Razor` 而非 `Microsoft.NET.Sdk`：

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    ...
  </Project>
  ```

* 通常情况下，对的包引用`Microsoft.AspNetCore.Mvc`，才能接收生成和编译 Razor 页面和 Razor 视图所需的附加依赖项。 至少，你的项目应添加到包引用：

  * `Microsoft.AspNetCore.Razor.Design` 
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
    
  `Microsoft.AspNetCore.Razor.Design`包提供的 Razor 编译任务和目标项目。

  前面的包包含在 `Microsoft.AspNetCore.Mvc` 中。 以下标记显示了使用 Razor SDK 用于生成 ASP.NET Core Razor 页面应用程序的 Razor 文件的项目文件：
    
  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design`并`Microsoft.AspNetCore.Mvc.Razor.Extensions`包中包含[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。 但是，与版本无关`Microsoft.AspNetCore.App`包引用提供一个元包不包括的最新版本的应用程序到`Microsoft.AspNetCore.Razor.Design`。 项目必须引用的一致版本`Microsoft.AspNetCore.Razor.Design`(或`Microsoft.AspNetCore.Mvc`)，以便包含 razor 的最新生成时修补程序。 有关详细信息，请参阅[此 GitHub 问题](https://github.com/aspnet/Razor/issues/2553)。

::: moniker-end

### <a name="properties"></a>Properties

以下属性控制项目生成过程中 Razor 的 SDK 行为：

* `RazorCompileOnBuild` &ndash; 当`true`、 编译并发出作为生成项目的一部分 Razor 程序集。 默认为 `true`。
* `RazorCompileOnPublish` &ndash; 当`true`、 编译并发出作为发布项目的一部分 Razor 程序集。 默认为 `true`。

配置输入和输出到 Razor SDK 用于属性和下表中的项。

| 项 | 描述 |
| ----- | ----------- |
| `RazorGenerate` | 输入到代码生成目标的项元素（.cshtml 文件）。 |
| `RazorCompile` | 项元素 (*.cs*文件)，是 Razor 编译目标的输入。 使用此 ItemGroup 指定要编译到 Razor 程序集中的其他文件。 |
| `RazorTargetAssemblyAttribute` | 用于编码生成 Razor 程序集属性的项元素。 例如：  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` |
| `RazorEmbeddedResource` | 作为嵌入资源添加到生成的 Razor 程序集的项元素。 |

| 属性 | 描述 |
| -------- | ----------- |
| `RazorTargetName` | Razor 生成的程序集的文件名（不含扩展名）。 | 
| `RazorOutputPath` | Razor 输出目录。 |
| `RazorCompileToolset` | 用于确定用于生成 Razor 程序集的工具集。 有效值为 `Implicit`、`RazorSDK` 和 `PrecompilationTool`。 |
| [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 默认值为 `true`。 当`true`，包括*web.config*， *.json*，和 *.cshtml*文件作为项目中的内容。 当通过引用`Microsoft.NET.Sdk.Web`，文件下*wwwroot*和，还提供了配置文件。 |
| `EnableDefaultRazorGenerateItems` | 为 `true` 时，包括 `RazorGenerate` 项中 `Content` 项的 .cshtml 文件。 |
| `GenerateRazorTargetAssemblyInfo` | 当`true`，生成 *.cs*包含指定的属性文件`RazorAssemblyAttribute`和编译输出中包括的文件。 |
| `EnableDefaultRazorTargetAssemblyInfoAttributes` | 为 `true` 时，将一组默认的程序集属性添加到 `RazorAssemblyAttribute`。 |
| `CopyRazorGenerateFilesToPublishDirectory` | 当`true`，复制`RazorGenerate`项 (*.cshtml*) 文件复制到发布目录。 通常情况下，Razor 文件不需要的已发布的应用，如果他们参与在生成时或发布时编译。 默认为 `false`。 |
| `CopyRefAssembliesToPublishDirectory` | 为 `true` 时，将引用程序集项复制到发布目录。 通常情况下，引用程序集不需要的已发布的应用，如果 Razor 编译发生在生成时或发布时间。 设置为`true`如果你已发布的应用需要运行时编译。 例如，将值设置为`true`如果应用程序修改 *.cshtml*文件在运行时或使用嵌入的视图。 默认为 `false`。 |
| `IncludeRazorContentInPack` | 当`true`，所有 Razor 内容项 (*.cshtml*文件) 标记为要包含在生成的 NuGet 包中。 默认为 `false`。 |
| `EmbedRazorGenerateSources` | 为 `true` 时，将 RazorGenerate (.cshtml) 项作为嵌入的文件添加到生成的 Razor 程序集中。 默认为 `false`。 |
| `UseRazorBuildServer` | 为 `true` 时，使用永久生成服务器进程来卸载代码生成工作。 默认值为 `UseSharedCompilation`。 |

有关属性的详细信息，请参阅 [MSBuild 属性](/visualstudio/msbuild/msbuild-properties)。

### <a name="targets"></a>目标

Razor SDK 定义两个主要目标：

* `RazorGenerate` &ndash; 代码将生成 *.cs*文件从`RazorGenerate`项元素。 使用 `RazorGenerateDependsOn` 属性指定可在此目标之前或之后运行的其他目标。
* `RazorCompile` &ndash; 生成的编译 *.cs*到 Razor 程序集文件中。 使用 `RazorCompileDependsOn` 指定可在此目标之前或之后运行的其他目标。

### <a name="runtime-compilation-of-razor-views"></a>Razor 视图的运行时编译

* 默认情况下，Razor SDK 不发布执行运行时编译所需的引用程序集。 当应用程序模型依赖于运行时编译时，这会导致编译失败&mdash;例如，应用在发布后使用嵌入视图或更改视图。 将 `CopyRefAssembliesToPublishDirectory` 设置为 `true`，以继续发布引用程序集。

* 对于 web 应用，请确保您的应用程序所面向`Microsoft.NET.Sdk.Web`SDK。
