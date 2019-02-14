---
title: ASP.NET Core 中的 Razor 文件编译和预编译
author: rick-anderson
description: 了解预编译 Razor 文件的好处以及如何在 ASP.NET Core 应用中完成 Razor 文件预编译。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: mvc/views/view-compilation
ms.openlocfilehash: c4e8f722fdf3d3f64807cc35ff9f349af7f32abd
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248181"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>ASP.NET Core 中的 Razor 文件编译

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range="= aspnetcore-1.1"

调用相关的 MVC 视图时，Razor 文件在运行时进行编译。 不支持在生成时发布 Razor 文件。 可以使用预编译工具，在发布时选择编译 Razor 文件并将其与应用一起部署。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

调用相关的 Razor 页和 MVC 视图时，Razor 文件在运行时进行编译。 不支持在生成时发布 Razor 文件。 可以使用预编译工具，在发布时选择编译 Razor 文件并将其与应用一起部署。

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

调用相关的 Razor 页和 MVC 视图时，Razor 文件在运行时进行编译。 在生成时和发布时使用 [Razor SDK](xref:razor-pages/sdk) 编译 Razor 文件。

::: moniker-end

## <a name="precompilation-considerations"></a>预编译注意事项

以下是预编译 Razor 文件的意外后果：

* 发布捆绑包更小
* 启动速度更快
* 无法编辑 Razor 文件&mdash;关联内容不会出现在发布捆绑包中。

## <a name="deploy-precompiled-files"></a>部署预编译文件

::: moniker range=">= aspnetcore-2.1"

Razor SDK 默认启用 Razor 文件的生成时和发布时编译。 Razor 文件更新后，支持在生成时编辑这些文件。 默认情况下，仅通过应用部署编译的 Views.dll 而不部署 cshtml 文件。

> [!IMPORTANT]
> ASP.NET Core 3.0 中将删除预编译工具。 建议迁移到 [Razor Sdk](xref:razor-pages/sdk)。
>
> 仅当项目文件中未设置特定于预编译的属性时，Razor SDK 才有效。 例如，通过将 .csproj 文件的 `MvcRazorCompileOnPublish` 属性设置为 `true` 来禁用 Razor SDK。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

如果项目面向 .NET Framework，请安装 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet 包：

[!code-xml[](view-compilation/sample/DotNetFrameworkProject.csproj?name=snippet_ViewCompilationPackage)]

如果项目面向 .NET Core，则无需进行任何更改。

默认情况下，ASP.NET Core 2.x 项目模板将 `MvcRazorCompileOnPublish` 属性隐式设置为 `true`。 因此，可以从 .csproj 文件中安全地删除此元素。

> [!IMPORTANT]
> ASP.NET Core 3.0 中将删除预编译工具。 建议迁移到 [Razor Sdk](xref:razor-pages/sdk)。
>
> 在 ASP.NET Core 2.0 中执行[独立部署 (SCD)](/dotnet/core/deploying/#self-contained-deployments-scd) 时，无法使用 Razor 文件预编译。

::: moniker-end

::: moniker range="= aspnetcore-1.1"

将 `MvcRazorCompileOnPublish` 属性设置为 `true`，然后安装 [Microsoft.AspNetCore.Mvc.Razor.ViewCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.ViewCompilation/) NuGet 包。 以下 .csproj 示例突出显示了这些设置：

[!code-xml[](view-compilation/sample/MvcRazorCompileOnPublish.csproj?highlight=4,10)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

使用 [.NET Core CLI 发布命令](/dotnet/core/tools/dotnet-publish)，让应用做好[框架依赖型部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)的准备。 例如，在项目根目录中执行以下命令：

```console
dotnet publish -c Release
```

预编译成功后，将生成包含已编译 Razor 文件的 <project_name>.PrecompiledViews.dll 文件。 例如，以下屏幕截图描述了 WebApplication1.PrecompiledViews.dll 中 Index.cshtml 的内容：

![DLL 中的 Razor 视图](view-compilation/_static/razor-views-in-dll.png)

::: moniker-end

## <a name="recompile-razor-files-on-change"></a>在更改时重新编译 Razor 文件

<xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions> <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> 获取或设置一个值，该值确定当磁盘上的文件发生更改时是否重新编译和更新 Razor 文件（Razor 视图和 Razor Pages）。

当设置为 `true` 时，[IFileProvider.Watch](xref:Microsoft.Extensions.FileProviders.IFileProvider.Watch*) 监视对配置的 <xref:Microsoft.Extensions.FileProviders.IFileProvider> 实例中的 Razor 文件所做的更改。

对于以下项，默认值为 `true`：

* ASP.NET Core 2.1 或更早版本的应用。
* 开发环境中的 ASP.NET Core 2.2 或更高版本的应用。

<xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> 与兼容性开关相关联，并可根据为应用配置的兼容性版本来提供不同的行为。 通过设置 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> 配置应用优先于由应用的兼容性版本表示的值。

如果将应用的兼容性版本设置为 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_1> 或更早版本，则将 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> 设置为 `true`，除非对其进行显式配置。

如果将应用的兼容性版本设置为 <xref:Microsoft.AspNetCore.Mvc.CompatibilityVersion.Version_2_2> 或更高版本，则将 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.AllowRecompilingViewsOnFileChange> 设置为 `false`，除非环境是开发环境或显式配置该值。

有关设置应用的兼容性版本的指导和示例，请参阅 <xref:mvc/compatibility-version>。

## <a name="additional-resources"></a>其他资源

::: moniker range="= aspnetcore-1.1"

* <xref:mvc/views/overview>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>

::: moniker-end

::: moniker range=">= aspnetcore-2.1"

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end
