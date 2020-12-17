---
title: 教程：在 ASP.NET Core 中开始使用 Razor Pages
author: rick-anderson
description: 本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor 页面 Web 应用的基础知识。
ms.author: riande
ms.date: 09/15/2020
ms.custom: contperf-fy21q2
no-loc:
- Index
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 4d4e50f8acea73859f5e839616f13f90a42291c4
ms.sourcegitcommit: 6299f08aed5b7f0496001d093aae617559d73240
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2020
ms.locfileid: "97486221"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a>教程：在 ASP.NET Core 中开始使用 Razor Pages

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-5.0"
本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor 页面 Web 应用的基础知识。

有关面向熟悉控制器和视图的开发人员的更高级介绍，请参阅 [Razor Pages 简介](xref:razor-pages/index)。

在本系列结束时，你将拥有一个管理电影数据库的应用。  

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie50)（[如何下载](xref:index#how-to-download-a-sample)）。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Razor 页面 Web 应用。
> * 运行应用。
> * 检查项目文件。

在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行增强。

![主页或 Index 页](razor-pages-start/_static/5/home5.png)

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>创建 Razor 页面 Web 应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 启动 Visual Studio 并选择“创建新项目”。 有关详细信息，请参阅[在 Visual Studio 中新建项目](/visualstudio/ide/create-new-project)。

   ![从“启动”窗口创建新项目](razor-pages-start/_static/5/start-window-create-new-project.png)

1. 在“创建新项目”对话框中，选择“ASP.NET Core Web 应用程序”，然后选择“下一步”  。

    ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/5/np.png)
    
1. 在“配置新项目”对话框中，为“项目名称”输入 `RazorPagesMovie`。 请务必将项目命名为“RazorPagesMovie”（包括匹配大小写），这样在复制和粘贴代码时命名空间就会匹配。

1. 选择“创建”  。

    ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

1. 在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择：
    1. 下拉列表中的“.NET Core”和“ASP.NET Core 5.0”。
    1. **Web 应用程序**。
    1. **Create**。

     ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/5/npx.png)

    创建以下初学者项目：

    ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。

1. 更改为将包含项目的目录 (`cd`)。

1. 运行以下命令：

   ```dotnetcli
   dotnet new webapp -o RazorPagesMovie
   code -r RazorPagesMovie
   ```

   * `dotnet new` 命令在“RazorPagesMovie”文件夹中新建 Razor Pages 项目。
   * `code` 命令在 Visual Studio Code 的当前实例中打开“RazorPagesMovie”文件夹。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 选择“文件”>“新建解决方案” 。

    ![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

1. 在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序” > “下一步”   。 在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序” > “下一步”。   

    ![macOS Web 应用模板选择](razor-pages-start/_static/web_app_template_vsmac.png)

1. 在“配置新的 Web 应用程序”对话框中：

    1. 确认已将“身份验证”设置为“无身份验证”。
    1. 如果看到用于选择“目标框架”的选项，请选择最新的 .NET 5.x 版本。
    1. 选择“下一步”。

1. 将项目命名为“PagesMovie”，然后选择“创建” *Razor* 。

    ![macOS 命名项目](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>运行应用

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>检查项目文件

下面是主项目文件夹和文件的概述，将在后续教程中使用。

### <a name="pages-folder"></a>Pages 文件夹

包含 Razor 页面和支持文件。 每个 Razor 页面都是一对文件：

* 一个 .cshtml 文件，其中包含使用 Razor 语法的 C# 代码的 HTML 标记。
* 一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。

支持文件的名称以下划线开头。 例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。 此文件设置页面顶部的导航菜单和页面底部的版权声明。 有关详细信息，请参阅 <xref:mvc/views/layout>。

### <a name="wwwroot-folder"></a>wwwroot 文件夹

包含静态资产，如 HTML 文件、JavaScript 文件和 CSS 文件。 有关详细信息，请参阅 <xref:fundamentals/static-files>。

### appsettings.json

包含配置数据，如连接字符串。 有关详细信息，请参阅 <xref:fundamentals/configuration/index>。

### <a name="programcs"></a>Program.cs

包含应用的入口点。 有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。

### <a name="startupcs"></a>Startup.cs

包含配置应用行为的代码。 有关详细信息，请参阅 <xref:fundamentals/startup>。

## <a name="next-steps"></a>后续步骤

> [!div class="step-by-step"]
> [下一篇：添加模型](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-5.0" -->

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor 页面 Web 应用的基础知识。

有关面向熟悉控制器和视图的开发人员的更高级介绍，请参阅 [Razor Pages 简介](xref:razor-pages/index)。

在本系列结束时，你将拥有一个管理电影数据库的应用。  

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30)（[如何下载](xref:index#how-to-download-a-sample)）。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Razor 页面 Web 应用。
> * 运行应用。
> * 检查项目文件。

在本教程结束时，你将有一个工作的 Razor 页面 Web 应用。在后续教程中，你可以在其基础上进行构建。

![主页或 Index 页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>创建 Razor 页面 Web 应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从 Visual Studio“文件”菜单中选择“新建”>“项目”。
* 创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”。
  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)
* 将项目命名为“RazorPagesMovie”。 请务必将项目命名为“RazorPagesMovie”，这样在复制和粘贴代码时命名空间就会匹配。
  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* 在下拉列表中选择“ASP.NET Core 3.1”，然后依次选择“Web 应用程序”和“创建”  。

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/3/npx.png)

  创建以下初学者项目：

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。

* 更改为将包含项目的目录 (`cd`)。

* 运行以下命令：

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new` 命令在“RazorPagesMovie”文件夹中新建 Razor Pages 项目。
  * `code` 命令在 Visual Studio Code 的当前实例中打开“RazorPagesMovie”文件夹。

* 在状态栏的 OmniSharp 火焰图标变绿后，对话框就会询问“'RazorPagesMovie' 缺少生成和调试所需的资产。是否添加它们?” 选择 **“是”** 。

  将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。  

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 选择“文件”>“新建解决方案” 。

  ![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* 在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序” > “下一步”   。 在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序” > “下一步”。   

  ![macOS Web 应用模板选择](razor-pages-start/_static/web_app_template_vsmac.png)

* 在“配置新的 Web 应用程序”对话框中：

  * 确认已将“身份验证”设置为“无身份验证”。
  * 如果看到用于选择“目标框架”的选项，请选择最新的 3.x 版本。

  选择“下一步”。

* 将项目命名为“RazorPagesMovie”，然后选择“创建”。

  ![macOS 命名项目](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>运行应用

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a>检查项目文件

下面是主项目文件夹和文件的概述，将在后续教程中使用。

### <a name="pages-folder"></a>Pages 文件夹

包含 Razor 页面和支持文件。 每个 Razor 页面都是一对文件：

* 一个 .cshtml 文件，其中包含使用 Razor 语法的 C# 代码的 HTML 标记。
* 一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。

支持文件的名称以下划线开头。 例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。 此文件设置页面顶部的导航菜单和页面底部的版权声明。 有关详细信息，请参阅 <xref:mvc/views/layout>。

### <a name="wwwroot-folder"></a>wwwroot 文件夹

包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。 有关详细信息，请参阅 <xref:fundamentals/static-files>。

### <a name="appsettingsjson"></a>appSettings.json

包含配置数据，如连接字符串。 有关详细信息，请参阅 <xref:fundamentals/configuration/index>。

### <a name="programcs"></a>Program.cs

包含程序的入口点。 有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。

### <a name="startupcs"></a>Startup.cs

包含配置应用行为的代码。 有关详细信息，请参阅 <xref:fundamentals/startup>。

## <a name="next-steps"></a>后续步骤

> [!div class="step-by-step"]
> [下一篇：添加模型](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

这是系列中的第一个教程。 [本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor 页面 Web 应用的基础知识。

有关面向熟悉控制器和视图的开发人员的更高级介绍，请参阅 [Razor Pages 简介](xref:razor-pages/index)。

在本系列结束时，你将拥有一个管理电影数据库的应用。  

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/razor-pages/razor-pages-start)（[如何下载](xref:index#how-to-download-a-sample)）。

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Razor 页面 Web 应用。
> * 运行应用。
> * 检查项目文件。

在本教程结束时，你将有一个工作的 Razor 页面 Web 应用。在后续教程中，你可以在其基础上进行构建。

![主页或 Index 页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a>先决条件

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a>创建 Razor 页面 Web 应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从 Visual Studio“文件”菜单中选择“新建”>“项目”。

* 创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”。

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* 将项目命名为“RazorPagesMovie”。 请务必将项目命名为“RazorPagesMovie”，这样在复制和粘贴代码时命名空间就会匹配。

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* 在下拉列表中选择“ASP.NET Core 2.2”，然后依次选择“Web 应用程序”和“创建”  。

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  创建以下初学者项目：

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。

* 更改为将包含项目的目录 (`cd`)。

* 运行以下命令：

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new` 命令在“RazorPagesMovie”文件夹中新建 Razor Pages 项目。
  * `code` 命令在 Visual Studio Code 的当前实例中打开“RazorPagesMovie”文件夹。

* 在状态栏的 OmniSharp 火焰图标变绿后，对话框就会询问“'RazorPagesMovie' 缺少生成和调试所需的资产。是否添加它们?” 选择 **“是”** 。

  将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。  

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

* 选择“文件”>“新建解决方案” 。

![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* 在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序” > “下一步”   。 在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序” > “下一步”。   

* 在“配置新的 Web 应用程序”对话框中：

  * 确认已将“身份验证”设置为“无身份验证”。
  * 如果看到用于选择“目标框架”的选项，请选择最新的 2.x 版本。

  选择“下一步”。

* 将项目命名为“RazorPagesMovie”，然后选择“创建”。

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>运行应用

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

* 按 Ctrl+F5 以在不使用调试程序的情况下运行。

  使用“Ctrl+F5”<kbd></kbd>启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。 许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。 地址栏显示 `localhost:port#`，而不是显示 `example.com`。 这是因为 `localhost` 是本地计算机的标准主机名。 Localhost 仅为来自本地计算机的 Web 请求提供服务。 Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。

* 在应用的主页上，选择“接受”以同意跟踪。

  此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。

  ![主页或 Index 页](razor-pages-start/_static/homeGDPR2.2.png)

  下图展示了提供同意跟踪后的应用：

  ![主页或 Index 页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* 按 Ctrl+F5<kbd></kbd> 以在不使用调试程序的情况下运行。

  使用“Ctrl+F5”<kbd></kbd>启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。 许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。

  Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并转到 `http://localhost:5001`。 地址栏显示 `localhost:port#`，而不是显示 `example.com`。 这是因为 `localhost` 是本地计算机的标准主机名。 Localhost 仅为来自本地计算机的 Web 请求提供服务。

* 在应用的主页上，选择“接受”以同意跟踪。

  此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。

  ![主页或 Index 页](razor-pages-start/_static/homeGDPR2.2.png)

  下图展示了同意跟踪后的应用：

  ![主页或 Index 页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* 按 Cmd-Opt-F5，以在不使用调试器的情况下运行。

  使用 Cmd+Opt+F5<kbd></kbd> 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改。 许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。

  Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并转到 `http://localhost:5001`。

* 在应用的主页上，选择“接受”以同意跟踪。

  此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。

  ![主页或 Index 页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  下图展示了提供同意跟踪后的应用：

  ![主页或 Index 页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>检查项目文件

下面是主项目文件夹和文件的概述，将在后续教程中使用。

### <a name="pages-folder"></a>Pages 文件夹

包含 Razor 页面和支持文件。 每个 Razor 页面都是一对文件：

* 一个 .cshtml 文件，其中包含使用 Razor 语法的 C# 代码的 HTML 标记。
* 一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。

支持文件的名称以下划线开头。 例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。 此文件设置页面顶部的导航菜单和页面底部的版权声明。 有关详细信息，请参阅 <xref:mvc/views/layout>。

Razor 页面派生自 `PageModel`。 按照约定，`PageModel` 派生的类称为 `<PageName>Model`。

### <a name="wwwroot-folder"></a>wwwroot 文件夹

包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。 有关详细信息，请参阅 <xref:fundamentals/static-files>。

### <a name="appsettingsjson"></a>appSettings.json

包含配置数据，如连接字符串。 有关详细信息，请参阅 <xref:fundamentals/configuration/index>。

### <a name="programcs"></a>Program.cs

包含程序的入口点。 有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。

### <a name="startupcs"></a>Startup.cs

包含配置应用行为的代码，例如是否需要同意 cookie。 有关详细信息，请参阅 <xref:fundamentals/startup>。

## <a name="additional-resources"></a>其他资源

* [本教程的 YouTube 版本](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>后续步骤

> [!div class="step-by-step"]
> [下一篇：添加模型](xref:tutorials/razor-pages/model)

::: moniker-end
