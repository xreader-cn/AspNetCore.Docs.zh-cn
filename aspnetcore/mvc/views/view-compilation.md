---
title: ASP.NET Core 中的 Razor 文件编译
author: rick-anderson
description: 了解 Razor 文件编译在 ASP.NET Core 应用中的发生方式。
ms.author: riande
ms.custom: mvc
ms.date: 04/14/2020
uid: mvc/views/view-compilation
ms.openlocfilehash: 3d871ab960de28a565280d9e4cb2c597832e2455
ms.sourcegitcommit: 6c8cff2d6753415c4f5d2ffda88159a7f6f7431a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81440930"
---
# <a name="razor-file-compilation-in-aspnet-core"></a>ASP.NET Core 中的 Razor 文件编译

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.1"

在生成和发布时均使用 [Razor SDK](xref:razor-pages/sdk) 编译扩展名为 .cshtml 的 Razor 文件**。 通过配置项目，可以选择启用运行时编译。

## <a name="razor-compilation"></a>Razor 编译

Razor SDK 默认启用 Razor 文件的生成时间和发布时间编译。 启用后，运行时编译将补充生成时间编译，允许在编辑 Razor 文件时对其进行更新。

## <a name="enable-runtime-compilation-at-project-creation"></a>在项目创建时启用运行时编译

Razor 页面和 MVC 项目模板包括一个选项，用于在创建项目时启用运行时编译。 ASP.NET酷3.1及更高版本支持此选项。

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

在 **"创建新ASP.NET核心 Web 应用程序**对话框中：

1. 选择**Web 应用程序**或**Web 应用程序（模型视图控制器）** 项目模板。
1. 选中启用**Razor 运行时编译**复选框。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli)

使用`-rrc`或`--razor-runtime-compilation`模板选项。 例如，以下命令创建启用运行时编译的新 Razor Pages 项目：

```dotnetcli
dotnet new webapp --razor-runtime-compilation
```

---

## <a name="enable-runtime-compilation-in-an-existing-project"></a>在现有项目中启用运行时编译

要为现有项目中的所有环境启用运行时编译，：

1. 安装[微软.AspNetCore.Mvc.Razor.运行时编译](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)NuGet包。
1. 更新项目的 `Startup.ConfigureServices` 方法以包含对 <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> 的调用。 例如：

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddRazorPages()
            .AddRazorRuntimeCompilation();

        // code omitted for brevity
    }
    ```

## <a name="conditionally-enable-runtime-compilation-in-an-existing-project"></a>在现有项目中有条件地启用运行时编译

启用运行时编译时可使其仅用于本地开发。 以这种方式有条件地启用可确保已发布的输出：

* 使用编译视图。
* 不会在生产环境中启用文件观察程序。

要仅在开发环境中启用运行时编译：

1. 安装[微软.AspNetCore.Mvc.Razor.运行时编译](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)NuGet包。
1. 修改`environmentVariables`*启动设置中的*启动配置文件部分 ：
    * 验证`ASPNETCORE_ENVIRONMENT`设置为`"Development"`。
    * 将 `ASPNETCORE_HOSTINGSTARTUPASSEMBLIES` 设置为 `"Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation"`。

在下面的示例中，在 和`IIS Express``RazorPagesApp`启动配置文件的开发环境中启用了运行时编译：

[!code-json[](~/mvc/views/view-compilation/samples/3.1/launchSettings.json?highlight=15-16,24-25)]

项目`Startup`类不需要更改代码。 在运行时，ASP.NET Core 搜索 中的`Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation`[程序集级托管启动属性](xref:fundamentals/configuration/platform-specific-configuration#hostingstartup-attribute)。 该`HostingStartup`属性指定要执行的应用启动代码。 该启动代码支持运行时编译。

## <a name="enable-runtime-compilation-for-a-razor-class-library"></a>为 Razor 类库启用运行时编译

请考虑 Razor 页面项目引用名为*MyClassLib*的[Razor 类库 （RCL）](xref:razor-pages/ui-class)的方案。 RCL 包含一个 *_Layout.cshtml*文件，所有团队的 MVC 和 Razor 页面项目都使用该文件。 您希望为该 RCL 中的 *_Layout.cshtml*文件启用运行时编译。 在 Razor 页面项目中进行以下更改：

1. 使用["有条件地"中的指令在现有项目中启用运行时编译](#conditionally-enable-runtime-compilation-in-an-existing-project)， 启用运行时编译。
1. 在 中`Startup.ConfigureServices`配置运行时编译选项。

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.1/Startup.cs?name=snippet_ConfigureServices&highlight=5-10)]

    在前面的代码中，构造了*MyClassLib* RCL 的绝对路径。 [物理文件提供程序 API](xref:fundamentals/file-providers#physicalfileprovider)用于查找该绝对路径上的目录和文件。 最后，实例`PhysicalFileProvider`被添加到文件提供程序集合中，允许访问 RCL 的 *.cshtml*文件。

## <a name="additional-resources"></a>其他资源

* [Razor 编译上构建和 Razor 编译上发布](xref:razor-pages/sdk#properties)属性。
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

在生成和发布时均使用 [Razor SDK](xref:razor-pages/sdk) 编译扩展名为 .cshtml 的 Razor 文件**。 通过配置应用程序，可以选择启用运行时编译。

## <a name="razor-compilation"></a>Razor 编译

Razor SDK 默认启用 Razor 文件的生成时间和发布时间编译。 启用后，运行时编译将补充生成时间编译，允许在编辑 Razor 文件时对其进行更新。

## <a name="runtime-compilation"></a>运行时编译

为所有环境和配置模式启用运行时编译：

1. 安装[微软.AspNetCore.Mvc.Razor.运行时编译](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Razor.RuntimeCompilation/)NuGet包。

1. 更新项目的 `Startup.ConfigureServices` 方法以包含对 <xref:Microsoft.Extensions.DependencyInjection.RazorRuntimeCompilationMvcBuilderExtensions.AddRazorRuntimeCompilation*> 的调用。 例如：

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

    [!code-csharp[](~/mvc/views/view-compilation/samples/3.0/Startup.cs?name=snippet)]

## <a name="additional-resources"></a>其他资源

* [Razor 编译上构建和 Razor 编译上发布](xref:razor-pages/sdk#properties)属性。
* <xref:razor-pages/index>
* <xref:mvc/views/overview>
* <xref:razor-pages/sdk>
* 有关显示跨项目进行运行时编译工作的示例，请参阅[GitHub 上的运行时编译示例](https://github.com/aspnet/samples/tree/master/samples/aspnetcore/mvc/runtimecompilation)。

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
