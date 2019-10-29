---
title: ASP.NET Core 的类库中的可重用 Razor UI
author: Rick-Anderson
description: 说明如何在 ASP.NET Core 中使用类库中的分部视图创建可重用 Razor UI。
ms.author: riande
ms.date: 10/26/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: ff12eea5406c4f5392a466728741000e3dd16fc1
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73034224"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>在 ASP.NET Core 中使用 Razor 类库项目创建可重用的 UI

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

Razor 视图、页、控制器、页模型、 [razor 组件](xref:blazor/class-libraries)、[视图组件](xref:mvc/views/view-components)和数据模型可以内置于 Razor 类库（RCL）中。 RCL 可以打包并重复使用。 应用程序可以包括 RCL，并重写其中包含的视图和页面。 如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="create-a-class-library-containing-razor-ui"></a>创建一个包含 Razor UI 的类库

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从 Visual Studio 中选择 "新建**项目**"。
* 选择 " **Razor 类库**>"**下一步**"。
* 命名库（例如 "RazorClassLib"），>**创建**。 为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。
* 如果需要支持视图，请选择 "**支持页和视图**"。 默认情况下，仅支持 Razor Pages。 选择“创建”。

默认情况下，Razor 类库 (RCL) 模板默认为 Razor 组件开发。 **支持页和视图**选项支持页和视图。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

从命令行中，运行 `dotnet new razorclasslib`。 例如:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

默认情况下，Razor 类库 (RCL) 模板默认为 Razor 组件开发。 传递 `--support-pages-and-views` 选项（`dotnet new razorclasslib --support-pages-and-views`）以提供对页面和视图的支持。

有关详细信息，请查看 [dotnet new](/dotnet/core/tools/dotnet-new)。 为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。

---

将 Razor 文件添加到 RCL。

ASP.NET Core 模板假设 RCL 内容位于 "*区域*" 文件夹中。 请参阅[RCL Pages layout](#rcl-pages-layout) ，以创建 RCL，以在 `~/Pages` 而不是 `~/Areas/Pages`公开内容。

## <a name="reference-rcl-content"></a>引用 RCL 内容

可以通过以下方式引用 RCL：

* NuGet 包。 请参阅[创建 NuGet 包](/nuget/create-packages/creating-a-package)、[dotnet 添加包](/dotnet/core/tools/dotnet-add-package)和[创建和发布 NuGet 包](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。
* {ProjectName}.csproj。 请查看 [dotnet-add 引用](/dotnet/core/tools/dotnet-add-reference)。

## <a name="override-views-partial-views-and-pages"></a>重写视图、分部视图和页面

如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。 例如，将*WebApp1/Areas/MyFeature/Pages/Page1. cshtml*添加到 WebApp1，WebApp1 中的 page1 将优先于 RCL 中的 page1。

在示例下载中，将 WebApp1/Areas/MyFeature2 重命名为 WebApp1/Areas/MyFeature 来测试优先级。

将 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 分部视图复制到 WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml。 更新标记以指示新的位置。 生成并运行应用，验证使用部分的应用版本。

### <a name="rcl-pages-layout"></a>RCL 页面布局

若要引用 RCL 内容（如同它是 web 应用的*Pages*文件夹的一部分），请创建具有以下文件结构的 RCL 项目：

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Shared*

假设*RazorUIClassLib/Pages/Shared*包含两部分文件： *_Header*和 *_Footer*。 可以将 `<partial>` 标记添加到 *_Layout*文件中：

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

## <a name="create-an-rcl-with-static-assets"></a>使用静态资产创建 RCL

RCL 可能需要随附静态资产，这些资产可由 RCL 的使用应用程序引用。 ASP.NET Core 允许创建包含可用于使用中的应用的静态资产的 RCLs。

若要将配套资产作为 RCL 的一部分包括在内，请在类库中创建*wwwroot*文件夹，并在该文件夹中包含所有必需的文件。

当对 RCL 进行打包时， *wwwroot*文件夹中的所有伴随资产将自动包含在包中。

### <a name="exclude-static-assets"></a>排除静态资产

若要排除静态资产，请将所需的排除路径添加到项目文件中的 `$(DefaultItemExcludes)` 属性组。 用分号（`;`）分隔条目。

在下面的示例中， *wwwroot*文件夹中的*lib*样式表不被视为静态资产，而且不包含在已发布的 RCL 中：

```xml
<PropertyGroup>
  <DefaultItemExcludes>$(DefaultItemExcludes);wwwroot\lib.css</DefaultItemExcludes>
</PropertyGroup>
```

### <a name="typescript-integration"></a>Typescript 集成

若要在 RCL 中包含 TypeScript 文件：

1. 将 TypeScript 文件（*ts*）置于*wwwroot*文件夹之外。 例如，将文件放在*客户端*文件夹中。

1. 为*wwwroot*文件夹配置 TypeScript 生成输出。 在项目文件中的 `PropertyGroup` 内设置 `TypescriptOutDir` 属性：

   ```xml
   <TypescriptOutDir>wwwroot</TypescriptOutDir>
   ```

1. 将 TypeScript 目标作为 `ResolveCurrentProjectStaticWebAssets` 目标的依赖项，方法是将以下目标添加到项目文件中的 `PropertyGroup` 内：

   ```xml
   <ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
     CompileTypeScript;
     $(ResolveCurrentProjectStaticWebAssetsInputs)
   </ResolveCurrentProjectStaticWebAssetsInputsDependsOn>
   ```

### <a name="consume-content-from-a-referenced-rcl"></a>使用引用 RCL 中的内容

RCL 的*wwwroot*文件夹中包含的文件会公开给使用的应用的前缀 `_content/{LIBRARY NAME}/`。 例如，名为*Razor*的库会导致 `_content/Razor.Class.Lib/`的静态内容的路径。

使用应用引用库提供的静态资产，其中包含 `<script>`、`<style>`、`<img>`和其他 HTML 标记。 使用的应用必须在 `Startup.Configure`中启用[静态文件支持](xref:fundamentals/static-files)：

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

从生成输出（`dotnet run`）运行使用应用时，默认情况下会在开发环境中启用静态 web 资产。 若要在从生成输出运行时支持其他环境中的资产，请在*Program.cs*中的主机生成器上调用 `UseStaticWebAssets`：

```csharp
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.Hosting;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            {
                webBuilder.UseStaticWebAssets();
                webBuilder.UseStartup<Startup>();
            });
}
```

从已发布的输出（`dotnet publish`）运行应用时，不需要调用 `UseStaticWebAssets`。

### <a name="multi-project-development-flow"></a>多项目开发流程

当使用中的应用运行时：

* RCL 中的资产保留在其原始文件夹中。 资产不会移动到使用的应用。
* 重新生成 RCL 后，RCL 的*wwwroot*文件夹内的任何更改都会反映在使用的应用程序中，而无需重新生成使用的应用。

生成 RCL 时，将生成描述静态 web 资产位置的清单。 使用的应用程序会在运行时读取清单，以使用来自引用项目和包的资产。 将新资产添加到 RCL 时，必须重新生成 RCL 以更新其清单，然后使用的应用才能访问新资产。

### <a name="publish"></a>发布

在发布应用程序时，所有被引用项目和包中的助理资产都将复制到 `_content/{LIBRARY NAME}/`下的已发布应用程序的*wwwroot*文件夹中。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

Razor 视图、页、控制器、页模型、 [razor 组件](xref:blazor/class-libraries)、[视图组件](xref:mvc/views/view-components)和数据模型可以内置于 Razor 类库（RCL）中。 RCL 可以打包并重复使用。 应用程序可以包括 RCL，并重写其中包含的视图和页面。 如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="create-a-class-library-containing-razor-ui"></a>创建一个包含 Razor UI 的类库

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从 Visual Studio“文件”菜单中，选择“新建”>“项目”。
* 选择“ASP.NET Core Web 应用程序”。
* 命名库（例如，“RazorClassLib”）>“确定”。 为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。
* 验证是否已选择 ASP.NET Core 2.1 或更高版本。
* 选择**Razor 类库**> **"确定"** 。

RCL 具有以下项目文件：

[!code-xml[](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

从命令行中，运行 `dotnet new razorclasslib`。 例如:

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
```

有关详细信息，请查看 [dotnet new](/dotnet/core/tools/dotnet-new)。 为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。

---

将 Razor 文件添加到 RCL。

ASP.NET Core 模板假设 RCL 内容位于 "*区域*" 文件夹中。 请参阅[RCL Pages layout](#rcl-pages-layout) ，以创建 RCL，以在 `~/Pages` 而不是 `~/Areas/Pages`公开内容。

## <a name="reference-rcl-content"></a>引用 RCL 内容

可以通过以下方式引用 RCL：

* NuGet 包。 请参阅[创建 NuGet 包](/nuget/create-packages/creating-a-package)、[dotnet 添加包](/dotnet/core/tools/dotnet-add-package)和[创建和发布 NuGet 包](/nuget/quickstart/create-and-publish-a-package-using-visual-studio)。
* {ProjectName}.csproj。 请查看 [dotnet-add 引用](/dotnet/core/tools/dotnet-add-reference)。

## <a name="walkthrough-create-an-rcl-project-and-use-from-a-razor-pages-project"></a>演练：创建 RCL 项目并从 Razor Pages 项目使用

可以下载并测试[完整项目](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)，无需创建项目。 示例下载包含附加代码和链接，以方便测试项目。 可以在[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/6098)中留下反馈，评论下载示例和分步说明的对比。

### <a name="test-the-download-app"></a>测试下载应用

如果尚未下载已完成的应用，并更愿意创建演练项目，请跳转至[下一节](#create-an-rcl)。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

在 Visual Studio 中打开 .sln 文件。 运行应用。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

在 cli 目录中的命令提示符下生成 RCL 和 Web 应用。

```dotnetcli
dotnet build
```

移动至 WebApp1 目录，并运行应用：

```dotnetcli
dotnet run
```

---

按[“测试 Test WebApp1”](#test-webapp1)中的说明进行操作

## <a name="create-an-rcl"></a>创建 RCL

在本部分中，将创建一个 RCL。 将 Razor 文件添加到 RCL。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

创建 RCL 项目：

* 从 Visual Studio“文件”菜单中，选择“新建”>“项目”。
* 选择“ASP.NET Core Web 应用程序”。
* 将应用命名为 " **RazorUIClassLib** > **" 确定 "** 。
* 验证是否已选择 ASP.NET Core 2.1 或更高版本。
* 选择**Razor 类库**> **"确定"** 。
* 添加一个名为 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 的 Razor 分部视图文件。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

从命令行运行以下命令：

```dotnetcli
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

前面的命令：

* 创建 `RazorUIClassLib` RCL。
* 创建 Razor _Message 页面，并将其添加至 RCL。 `-np` 参数创建不含 `PageModel` 的页面。
* 创建[_ViewStart](xref:mvc/views/layout#running-code-before-each-view)文件并将其添加到 RCL 中。

需要使用 *_ViewStart*文件才能使用 Razor Pages 项目的布局（将在下一节中添加）。

---

### <a name="add-razor-files-and-folders-to-the-project"></a>将 Razor 文件和文件夹添加到项目

* 使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 中的标记：

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* 使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml 中的标记：

  [!code-cshtml[](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

  使用分步视图 (`<partial name="_Message" />`) 需要 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`。 可以添加一个 _ViewImports.cshtml 文件，无需包含 `@addTagHelper` 指令。 例如:

  ```dotnetcli
  dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
  ```

  有关 *_ViewImports*的详细信息，请参阅[导入共享指令](xref:mvc/views/layout#importing-shared-directives)

* 生成类库以验证是否不存在编译器错误：

  ```dotnetcli
  dotnet build RazorUIClassLib
  ```

生成输出内容包含 RazorUIClassLib.dll 和 RazorUIClassLib.Views.dll。 RazorUIClassLib.Views.dll 包含已编译的 Razor 内容。

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>从 Razor 页面项目使用 Razor UI 库

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

创建 Razor 页面 Web 应用：

* 在**解决方案资源管理器**中，右键单击解决方案 >**添加**> "**新建项目**"。
* 选择“ASP.NET Core Web 应用程序”。
* 将应用命名为 WebApp1。
* 验证是否已选择 ASP.NET Core 2.1 或更高版本。
* 选择 " **Web 应用程序**> **" 确定 "** 。

* 在解决方案资源管理器中，右键单击“WebApp1”，然后选择“设为启动项目”。
* 在**解决方案资源管理器**中，右键单击**WebApp1** ，然后选择 "**生成依赖**关系 >**项目依赖项**"。
* 将 RazorUIClassLib 勾选为 WebApp1 的依赖项。
* 在**解决方案资源管理器**中，右键单击**WebApp1** ，然后选择 "**添加**>**引用**"。
* 在 "**引用管理器**" 对话框中，选中 " **RazorUIClassLib** > **" 确定 "** 。

运行应用。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

创建 Razor Pages web 应用和包含 Razor Pages 应用和 RCL 的解决方案文件：

```dotnetcli
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

生成并运行 Web 应用：

```dotnetcli
cd WebApp1
dotnet run
```

---

### <a name="test-webapp1"></a>测试 WebApp1

浏览到 `/MyFeature/Page1`，验证 Razor UI 类库是否正在使用中。

## <a name="override-views-partial-views-and-pages"></a>重写视图、分部视图和页面

如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。 例如，将*WebApp1/Areas/MyFeature/Pages/Page1. cshtml*添加到 WebApp1，WebApp1 中的 page1 将优先于 RCL 中的 page1。

在示例下载中，将 WebApp1/Areas/MyFeature2 重命名为 WebApp1/Areas/MyFeature 来测试优先级。

将 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 分部视图复制到 WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml。 更新标记以指示新的位置。 生成并运行应用，验证使用部分的应用版本。

### <a name="rcl-pages-layout"></a>RCL 页面布局

若要引用 RCL 内容（如同它是 web 应用的*Pages*文件夹的一部分），请创建具有以下文件结构的 RCL 项目：

* *RazorUIClassLib/Pages*
* *RazorUIClassLib/Pages/Shared*

假设*RazorUIClassLib/Pages/Shared*包含两部分文件： *_Header*和 *_Footer*。 可以将 `<partial>` 标记添加到 *_Layout*文件中：

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker-end
