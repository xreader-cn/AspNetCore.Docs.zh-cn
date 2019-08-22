---
title: ASP.NET Core 的类库中的可重用 Razor UI
author: Rick-Anderson
description: 说明如何创建可重复使用 Razor UI 在 ASP.NET Core 中的类库中使用分部视图。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 08/20/2019
ms.custom: mvc, seodec18
uid: razor-pages/ui-class
ms.openlocfilehash: 468d961c291810ca4dfbe615acd972cfd6e7572a
ms.sourcegitcommit: 41f2c1a6b316e6e368a4fd27a8b18d157cef91e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69886395"
---
# <a name="create-reusable-ui-using-the-razor-class-library-project-in-aspnet-core"></a>在 ASP.NET Core 中使用 Razor 类库项目创建可重用的 UI

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

Razor 视图、页、控制器、页模型、 [razor 组件](xref:blazor/class-libraries)、[视图组件](xref:mvc/views/view-components)和数据模型可以内置于 Razor 类库 (RCL) 中。 RCL 可以打包并重复使用。 应用程序可以包括 RCL，并重写其中包含的视图和页面。 如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。

此功能要求 [!INCLUDE[](~/includes/2.1-SDK.md)]

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/ui-class/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="create-a-class-library-containing-razor-ui"></a>创建一个包含 Razor UI 的类库

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”。
* 选择“ASP.NET Core Web 应用程序”。
* 命名库（例如，“RazorClassLib”）>“确定”。 为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。
* 验证是否已选择 ASP.NET Core 2.1 或更高版本。
* 选择“Razor 类库” > “确定”。

RCL 具有以下项目文件:

[!code-xml[Main](ui-class/samples/cli/RazorUIClassLib/RazorUIClassLib.csproj)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

从命令行中，运行 `dotnet new razorclasslib`。 例如：

```console
dotnet new razorclasslib -o RazorUIClassLib
```

有关详细信息，请查看 [dotnet new](/dotnet/core/tools/dotnet-new)。 为避免与已生成的视图库发生文件名冲突，请确保库名称不以 `.Views` 结尾。

---

将 Razor 文件添加到 RCL。

ASP.NET Core 模板假定 RCL 内容位于*领域*文件夹。 请参阅[RCL Pages layout](#afs) , 以创建在中`~/Pages`而不是`~/Areas/Pages`公开内容的 RCL。

## <a name="referencing-rcl-content"></a>引用 RCL 内容

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

```console
dotnet build
```

移动至 WebApp1 目录，并运行应用：

```console
dotnet run
```

---

按[“测试 Test WebApp1”](#test)中的说明进行操作

## <a name="create-an-rcl"></a>创建 RCL

在本部分中, 将创建一个 RCL。 将 Razor 文件添加到 RCL。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

创建 RCL 项目：

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”。
* 选择“ASP.NET Core Web 应用程序”。
* 命名应用程序**RazorUIClassLib** > **确定**。
* 验证是否已选择 ASP.NET Core 2.1 或更高版本。
* 选择“Razor 类库” > “确定”。
* 添加一个名为 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 的 Razor 分部视图文件。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

从命令行运行以下命令：

```console
dotnet new razorclasslib -o RazorUIClassLib
dotnet new page -n _Message -np -o RazorUIClassLib/Areas/MyFeature/Pages/Shared
dotnet new viewstart -o RazorUIClassLib/Areas/MyFeature/Pages
```

前面的命令：

* `RazorUIClassLib`创建 RCL。
* 创建 Razor _Message 页面，并将其添加至 RCL。 `-np` 参数创建不含 `PageModel` 的页面。
* 创建[_ViewStart.cshtml](xref:mvc/views/layout#running-code-before-each-view)文件，并将其添加到 RCL。

*_ViewStart.cshtml*时需要使用 （其中添加下一节中） 的 Razor 页面项目的布局文件。

---

### <a name="add-razor-files-and-folders-to-the-project"></a>将 Razor 文件和文件夹添加到项目

* 使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 中的标记：

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml)]

* 使用以下代码替换 RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml 中的标记：

[!code-cshtml[Main](ui-class/samples/cli/RazorUIClassLib/Areas/MyFeature/Pages/Page1.cshtml)]

使用分步视图 (`<partial name="_Message" />`) 需要 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`。 可以添加一个 _ViewImports.cshtml 文件，无需包含 `@addTagHelper` 指令。 例如：

```console
dotnet new viewimports -o RazorUIClassLib/Areas/MyFeature/Pages
```

有关详细信息 *_ViewImports.cshtml*，请参阅[导入共享指令](xref:mvc/views/layout#importing-shared-directives)

* 生成类库以验证是否不存在编译器错误：

```console
dotnet build RazorUIClassLib
```

生成输出内容包含 RazorUIClassLib.dll 和 RazorUIClassLib.Views.dll。 RazorUIClassLib.Views.dll 包含已编译的 Razor 内容。

### <a name="use-the-razor-ui-library-from-a-razor-pages-project"></a>从 Razor 页面项目使用 Razor UI 库

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

创建 Razor 页面 Web 应用：

* 在解决方案资源管理器中，右键单击解决方案 >“添加” >  “新建项目”。
* 选择“ASP.NET Core Web 应用程序”。
* 将应用命名为 WebApp1。
* 验证是否已选择 ASP.NET Core 2.1 或更高版本。
* 选择“Web 应用程序” > “确定”。

* 在解决方案资源管理器中，右键单击“WebApp1”，然后选择“设为启动项目”。
* 在解决方案资源管理器中，右键单击“WebApp1”，然后选择“生成依赖项” > “项目依赖项”。
* 将 RazorUIClassLib 勾选为 WebApp1 的依赖项。
* 在解决方案资源管理器中，右键单击“WebApp1”，选择“添加” > “引用”。
* 在“引用管理器”对话框中勾选“RazorUIClassLib” > “确定”。

运行应用。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

创建 Razor Pages web 应用和包含 Razor Pages 应用和 RCL 的解决方案文件:

```console
dotnet new webapp -o WebApp1
dotnet new sln
dotnet sln add WebApp1
dotnet sln add RazorUIClassLib
dotnet add WebApp1 reference RazorUIClassLib
```

生成并运行 Web 应用：

```console
cd WebApp1
dotnet run
```

---

<a name="test"></a>

### <a name="test-webapp1"></a>测试 WebApp1

验证 Razor UI 类库是否正在使用:

* 浏览到 `/MyFeature/Page1`。

## <a name="override-views-partial-views-and-pages"></a>重写视图、分部视图和页面

如果在 Web 应用和 RCL 中都能找到视图、分部视图或 Razor 页面，则 Web 应用中的 Razor 标记（.cshtml 文件）优先。 例如, 将*WebApp1/Areas/MyFeature/Pages/Page1. cshtml*添加到 WebApp1, WebApp1 中的 page1 将优先于 RCL 中的 page1。

在示例下载中，将 WebApp1/Areas/MyFeature2 重命名为 WebApp1/Areas/MyFeature 来测试优先级。

将 RazorUIClassLib/Areas/MyFeature/Pages/Shared/_Message.cshtml 分部视图复制到 WebApp1/Areas/MyFeature/Pages/Shared/_Message.cshtml。 更新标记以指示新的位置。 生成并运行应用，验证使用部分的应用版本。

<a name="afs"></a>

### <a name="rcl-pages-layout"></a>RCL 页面布局

为引用 RCL 内容就好像它是 web 应用的一部分*页面*文件夹中，创建具有以下文件结构 RCL 项目：

* *RazorUIClassLib/页*
* *RazorUIClassLib/页/Shared*

假设*RazorUIClassLib/页/共享*包含两个部分的文件： *_Header.cshtml*并 *_Footer.cshtml*。 `<partial>`标记无法添加到 *_Layout.cshtml*文件：

```cshtml
<body>
  <partial name="_Header">
  @RenderBody()
  <partial name="_Footer">
</body>
```

::: moniker range=">= aspnetcore-3.0"

## <a name="create-an-rcl-with-static-assets"></a>使用静态资产创建 RCL

RCL 可能需要随附静态资产, 这些资产可由 RCL 的使用应用程序引用。 ASP.NET Core 允许创建包含可用于使用中的应用的静态资产的 RCLs。

若要将配套资产作为 RCL 的一部分包括在内, 请在类库中创建*wwwroot*文件夹, 并在该文件夹中包含所有必需的文件。

当对 RCL 进行打包时, *wwwroot*文件夹中的所有伴随资产将自动包含在包中。

### <a name="consume-content-from-a-referenced-rcl"></a>使用引用 RCL 中的内容

RCL 的*wwwroot*文件夹中包含的文件会公开给使用的应用的前缀`_content/{LIBRARY NAME}/`。 例如, 名为*Razor*的库会生成指向中的静态内容`_content/Razor.Class.Lib/`的路径。

使用应用引用库`<script>`提供的静态资产, 以及、 `<style>`、 `<img>`和其他 HTML 标记。 使用的应用必须在中`Startup.Configure`启用[静态文件支持](xref:fundamentals/static-files):

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    ...

    app.UseStaticFiles();

    ...
}
```

从生成输出 (`dotnet run`) 运行使用中的应用时, 默认情况下会在开发环境中启用静态 web 资产。 若要在从生成输出运行时支持其他环境中的`UseStaticWebAssets`资产, 请在*Program.cs*中的主机生成器上调用:

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

从`UseStaticWebAssets`已发布的输出 (`dotnet publish`) 运行应用时, 不需要调用。

### <a name="multi-project-development-flow"></a>多项目开发流程

当使用中的应用运行时:

* RCL 中的资产保留在其原始文件夹中。 资产不会移动到使用的应用。
* 重新生成 RCL 后, RCL 的*wwwroot*文件夹内的任何更改都会反映在使用的应用程序中, 而无需重新生成使用的应用。

生成 RCL 时, 将生成描述静态 web 资产位置的清单。 使用的应用程序会在运行时读取清单, 以使用来自引用项目和包的资产。 将新资产添加到 RCL 时, 必须重新生成 RCL 以更新其清单, 然后使用的应用才能访问新资产。

### <a name="publish"></a>发布

发布应用后, 所有被引用项目和包中的助理资产将复制到下`_content/{LIBRARY NAME}/`的已发布应用的*wwwroot*文件夹中。

::: moniker-end
