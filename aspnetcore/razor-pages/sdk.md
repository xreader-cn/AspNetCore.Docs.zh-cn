---
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---
# <a name="aspnet-core-razor-sdk"></a>ASP.NET Core Razor SDK

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

## <a name="overview"></a>概述

[!INCLUDE[](~/includes/2.1-SDK.md)] 包含 `Microsoft.NET.Sdk.Razor` MSBuild SDK (Razor SDK)。 Razor SDK：

::: moniker range=">= aspnetcore-3.0"

* 需要生成、打包和发布包含 [Razor](xref:mvc/views/razor) 文件的项目，这些项目用于基于 ASP.NET Core MVC 的项目或 [Blazor](xref:blazor/index) 项目。
* 包含一组预定义的目标、属性和项目，它们可用于自定义 Razor（.cshtml 或 .razor）文件的编译 。

Razor SDK 包含 `Content` 项，其 `Include` 属性设置为 `**\*.cshtml` 和 `**\*.razor` 通配模式。 发布匹配文件。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

* 针对基于 ASP.NET Core MVC 的项目，围绕包含 [Razor](xref:mvc/views/razor) 文件的项目的生成、打包和发布设定了体验标准。
* 包含一组预定义的目标、属性和项目，它们允许自定义 Razor 文件的编译。

Razor SDK 包含 `Content` 项，其 `Include` 属性设置为 `**\*.cshtml` 通配模式。 发布匹配文件。

::: moniker-end

## <a name="prerequisites"></a>先决条件

[!INCLUDE[](~/includes/2.1-SDK.md)]

## <a name="use-the-razor-sdk"></a>使用 Razor SDK

大多数 Web 应用不需要显式引用 Razor SDK。

::: moniker range=">= aspnetcore-3.0"

若要使用 Razor SDK 生成包含 Razor 视图或 Razor Pages 的类库，建议从 Razor 类库 (RCL) 项目模板开始。 用于生成 Blazor (.razor) 文件的 RCL 至少需要引用 [Microsoft.AspNetCore.Components](https://www.nuget.org/packages/Microsoft.AspNetCore.Components) 包。 用于生成 Razor 视图或页面（.cshtml 文件）的 RCL 至少需要面向 `netcoreapp3.0` 或更高版本，且其项目文件中至少具有指向 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)的 `FrameworkReference`。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

若要使用 Razor SDK 来生成包含 Razor 视图或 Razor Pages 的类库，请执行以下操作：

* 使用 `Microsoft.NET.Sdk.Razor` 而非 `Microsoft.NET.Sdk`：

  ```xml
  <Project SDK="Microsoft.NET.Sdk.Razor">
    <!-- omitted for brevity -->
  </Project>
  ```

* 通常，对 `Microsoft.AspNetCore.Mvc` 的包引用需要接收生成和编译 Razor Pages 和 Razor 视图所需的其他依赖项。 项目至少应将包引用添加到：

  * `Microsoft.AspNetCore.Razor.Design`
  * `Microsoft.AspNetCore.Mvc.Razor.Extensions`
  * `Microsoft.AspNetCore.Mvc.Razor`

  `Microsoft.AspNetCore.Razor.Design` 包为项目提供 Razor 编译任务和目标。

  前面的包包含在 `Microsoft.AspNetCore.Mvc` 中。 以下标记显示了使用 Razor SDK 为 ASP.NET Core Razor Pages 应用生成 Razor 文件的项目文件：

  [!code-xml[](sdk/sample/RazorSDK.csproj)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

> [!WARNING]
> `Microsoft.AspNetCore.Razor.Design` 和 `Microsoft.AspNetCore.Mvc.Razor.Extensions` 包均包含在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。 但无版本的 `Microsoft.AspNetCore.App` 包引用可为不含最新版 `Microsoft.AspNetCore.Razor.Design` 的应用提供元包。 项目必须引用一致版本的 `Microsoft.AspNetCore.Razor.Design`（或 `Microsoft.AspNetCore.Mvc`），以便包含 Razor 的最新生成时修补程序。 有关详细信息，请参阅[此 GitHub 问题](https://github.com/aspnet/Razor/issues/2553)。

::: moniker-end

### <a name="properties"></a>属性

以下属性控制项目生成过程中 Razor 的 SDK 行为：

* `RazorCompileOnBuild`：若为 `true`，则在生成项目的过程中，编译并发出 Razor 程序集。 默认为 `true`。
* `RazorCompileOnPublish`：若为 `true`，则在发布项目的过程中，编译并发出 Razor 程序集。 默认为 `true`。

下表中的属性和项用于配置 Razor SDK 的输入和输出。

::: moniker range=">= aspnetcore-3.0"

> [!WARNING]
> 从 ASP.NET Core 3.0 开始，如果禁用项目文件中的 `RazorCompileOnBuild` 或 `RazorCompileOnPublish` MSBuild 属性，则不会默认提供 MVC 视图或 Razor Pages。 如果应用依赖运行时编译来处理 .cshtml 文件，则应用程序必须添加对 [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation) 包的显式引用。

::: moniker-end

| 项 | 描述 |
| ----- | ---
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

------ | | `RazorGenerate` | 输入到代码生成的项元素（.cshtml 文件）。 | | `RazorComponent` | 输入到 Razor 组件代码生成的项元素（.razor 文件）。 | | `RazorCompile` | 输入到 Razor 编译目标的项元素（.cs 文件）。 使用此 `ItemGroup` 指定要编译到 Razor 程序集中的其他文件。 | | `RazorTargetAssemblyAttribute` | 用于编码生成 Razor 程序集属性的项元素。 例如：  <br>`RazorAssemblyAttribute`<br>`Include="System.Reflection.AssemblyMetadataAttribute"`<br>`_Parameter1="BuildSource" _Parameter2="https://docs.microsoft.com/">` | | `RazorEmbeddedResource` | 作为嵌入的资源添加到生成的 Razor 程序集中的项元素。 |

::: moniker range=">= aspnetcore-3.0"

| Property | 描述 |
| ---
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---- | --- title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

------ | | `RazorTargetName` | Razor 生成的程序集的文件名（不含扩展名）。 | | `RazorOutputPath` | Razor 输出目录。 | | `RazorCompileToolset` | 用于确定生成 Razor 程序集所使用的工具集。 有效值为 `Implicit`、`RazorSDK` 和 `PrecompilationTool`。 | | [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 默认值为 `true`。 若为 `true`，则包含 web.config、.json 和 .cshtml 文件作为项目中的内容  。 通过 `Microsoft.NET.Sdk.Web` 引用时，还包括 wwwroot 下的文件和配置文件。 | | `EnableDefaultRazorGenerateItems` | 若为 `true`，则包括 `RazorGenerate` 项中 `Content` 项的 .cshtml 文件。 | | `GenerateRazorTargetAssemblyInfo` | 若为 `true`，则生成 .cs 文件（其中包含由 `RazorAssemblyAttribute` 指定的属性），并将文件包含在编译输出中。 | | `EnableDefaultRazorTargetAssemblyInfoAttributes` | 若为 `true`，则将一组默认的程序集属性添加到 `RazorAssemblyAttribute`。 | | `CopyRazorGenerateFilesToPublishDirectory` | 若为 `true`，则将 `RazorGenerate` 项 (.cshtml) 文件复制到发布目录。 如果在生成时或发布时参与编译，则通常发布的应用无需 Razor 文件。 默认为 `false`。 | | `PreserveCompilationReferences` | 若为 `true`，则将引用程序集项复制到发布目录。 如果在生成时或发布时出现 Razor 编译，则通常发布的应用无需引用程序集。 如果发布的应用需要运行时编译，则设置为 `true`。 例如，如果应用在运行时修改 .cshtml 文件或使用嵌入视图，则将值设置为 `true`。 默认为 `false`。 | | `IncludeRazorContentInPack` | 若为 `true`，则所有 Razor 内容项（.cshtml 文件）标记为包含在生成的 NuGet 包中。 默认为 `false`。 | | `EmbedRazorGenerateSources` | 若为 `true`，将 RazorGenerate (.cshtml) 项作为嵌入的文件添加到生成的 Razor 程序集中。 默认为 `false`。 | | `UseRazorBuildServer` | 若为 `true`，使用永久生成服务器进程来卸载代码生成工作。 默认值为 `UseSharedCompilation`。 | | `GenerateMvcApplicationPartsAssemblyAttributes` | 若为 `true`，则 SDK 会生成 MVC 在运行时使用的其他属性，以执行应用程序部件的发现。 | | `DefaultWebContentItemExcludes` | 要从面向 Web 或 Razor SDK 的项目的 `Content` 项组中排除的项元素的通配模式 | | `ExcludeConfigFilesFromBuildOutput` | 若为 `true`，则 .config 和 .json 文件不会复制到生成输出目录 。 | | `AddRazorSupportForMvc` | 若为 `true`，则配置 Razor SDK，以添加对生成包含 MVC 视图或 Razor Pages 的应用程序时所需的 MVC 配置的支持。 对于面向 Web SDK 的 .NET Core 3.0 或更高版本的项目，隐式设置该属性 | | `RazorLangVersion` | 要面向的 Razor 语言版本。 |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| Property | 描述 |
| ---
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

---- | --- title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

-
title:“ASP.NET Core Razor SDK”author: description:“了解 ASP.NET Core 中的 Razor Pages 如何使基于页面的编码方式比使用 MVC 更简单高效。”
monikerRange: ms.author: ms.custom: ms.date: no-loc:
- 'Blazor'
- 'Identity'
- 'Let's Encrypt'
- 'Razor'
- 'SignalR' uid: 

------ | | `RazorTargetName` | Razor 生成的程序集的文件名（不含扩展名）。 | | `RazorOutputPath` | Razor 输出目录。 | | `RazorCompileToolset` | 用于确定生成 Razor 程序集所使用的工具集。 有效值为 `Implicit`、`RazorSDK` 和 `PrecompilationTool`。 | | [EnableDefaultContentItems](https://github.com/aspnet/websdk/blob/rel-2.0.0/src/ProjectSystem/Microsoft.NET.Sdk.Web.ProjectSystem.Targets/netstandard1.0/Microsoft.NET.Sdk.Web.ProjectSystem.targets#L21) | 默认值为 `true`。 若为 `true`，则包含 web.config、.json 和 .cshtml 文件作为项目中的内容  。 通过 `Microsoft.NET.Sdk.Web` 引用时，还包括 wwwroot 下的文件和配置文件。 | | `EnableDefaultRazorGenerateItems` | 若为 `true`，则包括 `RazorGenerate` 项中 `Content` 项的 .cshtml 文件。 | | `GenerateRazorTargetAssemblyInfo` | 若为 `true`，则生成 .cs 文件（其中包含由 `RazorAssemblyAttribute` 指定的属性），并将文件包含在编译输出中。 | | `EnableDefaultRazorTargetAssemblyInfoAttributes` | 若为 `true`，则将一组默认的程序集属性添加到 `RazorAssemblyAttribute`。 | | `CopyRazorGenerateFilesToPublishDirectory` | 若为 `true`，则将 `RazorGenerate` 项 (.cshtml) 文件复制到发布目录。 如果在生成时或发布时参与编译，则通常发布的应用无需 Razor 文件。 默认为 `false`。 | | `CopyRefAssembliesToPublishDirectory` | 若为 `true`，则将引用程序集项复制到发布目录。 如果在生成时或发布时出现 Razor 编译，则通常发布的应用无需引用程序集。 如果发布的应用需要运行时编译，则设置为 `true`。 例如，如果应用在运行时修改 .cshtml 文件或使用嵌入视图，则将值设置为 `true`。 默认为 `false`。 | | `IncludeRazorContentInPack` | 若为 `true`，则所有 Razor 内容项（.cshtml 文件）标记为包含在生成的 NuGet 包中。 默认为 `false`。 | | `EmbedRazorGenerateSources` | 若为 `true`，将 RazorGenerate (.cshtml) 项作为嵌入的文件添加到生成的 Razor 程序集中。 默认为 `false`。 | | `UseRazorBuildServer` | 若为 `true`，使用永久生成服务器进程来卸载代码生成工作。 默认值为 `UseSharedCompilation`。 | | `GenerateMvcApplicationPartsAssemblyAttributes` | 若为 `true`，则 SDK 会生成 MVC 在运行时使用的其他属性，以执行应用程序部件的发现。 | | `DefaultWebContentItemExcludes` | 要从面向 Web 或 Razor SDK 的项目的 `Content` 项组中排除的项元素的通配模式 | | `ExcludeConfigFilesFromBuildOutput` | 若为 `true`，则 .config 和 .json 文件不会复制到生成输出目录 。 | | `AddRazorSupportForMvc` | 若为 `true`，则配置 Razor SDK，以添加对生成包含 MVC 视图或 Razor Pages 的应用程序时所需的 MVC 配置的支持。 对于面向 Web SDK 的 .NET Core 3.0 或更高版本的项目，隐式设置该属性 | | `RazorLangVersion` | 要面向的 Razor 语言版本。 |

::: moniker-end

有关属性的详细信息，请参阅 [MSBuild 属性](/visualstudio/msbuild/msbuild-properties)。

### <a name="targets"></a>目标

Razor SDK 定义两个主要目标：

* `RazorGenerate`：代码基于 `RazorGenerate` 项元素生成 .cs 文件。 使用 `RazorGenerateDependsOn` 属性指定可在此目标之前或之后运行的其他目标。
* `RazorCompile`：将生成的 .cs 文件编译到 Razor 程序集中。 使用 `RazorCompileDependsOn` 指定可在此目标之前或之后运行的其他目标。
* `RazorComponentGenerate`：代码为 `RazorComponent` 项元素生成 .cs 文件。 使用 `RazorComponentGenerateDependsOn` 属性指定可在此目标之前或之后运行的其他目标。

### <a name="runtime-compilation-of-razor-views"></a>Razor 视图的运行时编译

* 默认情况下，Razor SDK 不发布执行运行时编译所需的引用程序集。 当应用程序模型依赖于运行时编译时，这会导致编译失败&mdash;例如，应用在发布后使用嵌入视图或更改视图。 将 `CopyRefAssembliesToPublishDirectory` 设置为 `true`，以继续发布引用程序集。

* 对于 Web 应用，请确保应用面向 `Microsoft.NET.Sdk.Web` SDK。

## <a name="razor-language-version"></a>Razor 语言版本

面向 `Microsoft.NET.Sdk.Web` SDK 时，Razor 语言版本是从应用的目标框架版本推断出来的。 对于面向 `Microsoft.NET.Sdk.Razor` SDK 的项目，或应用需要不同于推断值的 Razor 语言版本的极少数情况下，可在应用的项目文件中设置 `<RazorLangVersion>` 属性来配置版本：

```xml
<PropertyGroup>
  <RazorLangVersion>{VERSION}</RazorLangVersion>
</PropertyGroup>
```

Razor 的语言版本与其面向的运行时版本紧密集成。 不支持面向不专为运行时而设计的语言版本，这可能会产生生成错误。

## <a name="additional-resources"></a>其他资源

* [.NET Core 的 csproj 格式的新增内容](/dotnet/core/tools/csproj)
* [常用的 MSBuild 项目项](/visualstudio/msbuild/common-msbuild-project-items)
