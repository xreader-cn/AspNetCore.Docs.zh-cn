---
title: ASP.NET Core 中的 Razor 文件编译
author: rick-anderson
description: 了解 Razor 文件编译在 ASP.NET Core 应用中的发生方式。
ms.author: riande
ms.custom: mvc
ms.date: 4/8/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 7f329ffb4c63e8699663f49720145984bb8802fd
ms.sourcegitcommit: 9a46e78c79d167e5fa0cddf89c1ef584e5fe1779
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80994610"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>ASP.NET Core 中的 Razor 文件编译

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

在生成和发布时均使用 [Razor SDK](xref:razor-pages/sdk) 编译扩展名为 .cshtml 的 Razor 文件**。 通过配置应用程序，可以选择启用运行时编译。

## <a name="razor-compilation"></a>Razor 编译

Razor SDK 默认启用 Razor 文件的生成时和发布时编译。 启用后，运行时编译将补充生成时编译，允许更新 Razor 文件（如果对其进行编辑）。

## <a name="runtime-compilation"></a>运行时编译

为所有环境和配置模式启用运行时编译：

1. 安装[微软.AspNetCore.Mvc.Razor.运行时编译](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)NuGet包。

1. 更新项目的 `Startup.ConfigureServices` 方法以包含对 `AddRazorRuntimeCompilation` 的调用。 例如：

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

### <a name="conditionally-enable-runtime-compilation"></a>有条件地启用运行时编译

启用运行时编译时可使其仅用于本地开发。 以这种方式有条件地启用可确保已发布的输出：

* 使用编译视图。
* 较小。
* 不会在生产环境中启用文件观察程序。

基于环境和配置模式启用运行时编译：

1. 根据活动的 `Configuration` 值，有条件地引用 [Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/) 包：

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation" Version="3.1.0" Condition="'$(Configuration)' == 'Debug'" />
    ```

1. 更新项目的 `Startup.ConfigureServices` 方法以包含对 `AddRazorRuntimeCompilation` 的调用。 有条件地执行 `AddRazorRuntimeCompilation`，使其仅当 `ASPNETCORE_ENVIRONMENT` 变量设置为 `Development`时在调试模式下运行：

    ```csharp
    public IWebHostEnvironment Env { get; set; }

    public void ConfigureServices(IServiceCollection services)
    {
        IMvcBuilder builder = services.AddRazorPages();

    #if DEBUG
        if (Env.IsDevelopment())
        {
            builder.AddRazorRuntimeCompilation();
        }
    #endif

        // code omitted for brevity
    }
    ```

## <a name="additional-resources"></a>其他资源

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

调用相关的 Razor 页和 MVC 视图时，Razor 文件在运行时进行编译。 在生成时和发布时使用 [Razor SDK](xref:razor-pages/sdk) 编译 Razor 文件。

## <a name="razor-compilation"></a>Razor 编译

Razor SDK 默认启用 Razor 文件的生成时和发布时编译。 Razor 文件更新后，支持在生成时编辑这些文件。 默认情况下，只有编译 Razor 文件所需的编译的 Views.dll（而非 .cshtml）文件或引用程序集随应用一起部署****。

> [!IMPORTANT]
> 已弃用预编译工具，并且将在 ASP.NET Core 3.0 中删除该工具。 建议迁移到 [Razor Sdk](xref:razor-pages/sdk)。
>
> 仅当项目文件中未设置特定于预编译的属性时，Razor SDK 才有效。 例如，通过将 .csproj 文件的 `MvcRazorCompileOnPublish` 属性设置为 `true` 来禁用 Razor SDK**。

## <a name="runtime-compilation"></a>运行时编译

通过 Razor 文件的运行时编译补充生成时编译。 当 .cshtml 文件的内容发生更改时，ASP.NET Core MVC 将重新编译 Razor 文件**。

## <a name="additional-resources"></a>其他资源

* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end