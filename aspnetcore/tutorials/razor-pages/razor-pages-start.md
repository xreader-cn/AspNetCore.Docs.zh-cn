---
title: 教程：开始使用ASP.NET Core中的Razor Pages
author: rick-anderson
description: 此系列教程演示了如何在 ASP.NET Core 中使用 Razor Pages。 了解如何创建模型、为 Razor Pages 生成代码、将 Entity Framework Core 和 SQL Server 用于数据访问、添加搜索功能、添加输入验证及使用迁移更新模型。
ms.author: riande
ms.date: 6/3/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: d843e47ccb5180fab34b4c4c4a4b5cbda21289bf
ms.sourcegitcommit: a1364109d11d414121a6337b611bee61d6e489e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66491218"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a>教程：开始使用ASP.NET Core中的Razor Pages

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

这是系列中的第一个教程。 [本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor Pages Web 应用的基础知识。

[!INCLUDE[](~/includes/advancedRP.md)]

在本系列结束时，你将拥有一个管理电影数据库的应用。  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Razor Pages Web 应用。
> * 运行应用。
> * 检查项目文件。

在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。

![主页或索引页](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a>创建 Razor Pages Web 应用

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”    。

* 创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* 将项目命名为“RazorPagesMovie”  。 将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时，命名空间才会匹配  。

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* 在下拉列表中选择“ASP.NET Core 2.2”，然后依次选择“Web 应用程序”和“创建”    。

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  创建以下初学者项目：

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

* 打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。

* 更改为将包含项目的目录 (`cd`)。

* 运行以下命令：

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * `dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。
  * `code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。

* 状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?” 选择 **“是”** 。

  将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

从终端运行以下命令：

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 Razor Pages 项目。

## <a name="open-the-project"></a>打开项目

在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“RazorPagesMovie.csproj”  文件。

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a>运行应用

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 按 Ctrl+F5 以在不使用调试程序的情况下运行。

  [!INCLUDE[](~/includes/trustCertVS.md)]

  Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。 地址栏显示 `localhost:port#`，而不是显示 `example.com`。 这是因为 `localhost` 是本地计算机的标准主机名。 Localhost 仅为来自本地计算机的 Web 请求提供服务。 Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。

* 在应用的主页上，选择“接受”以同意跟踪  。

  此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  下图展示了同意跟踪后的应用：

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* 按 **Ctrl-F5** 以在不使用调试程序的情况下运行。

  Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。 地址栏显示 `localhost:port#`，而不是显示 `example.com`。 这是因为 `localhost` 是本地计算机的标准主机名。 Localhost 仅为来自本地计算机的 Web 请求提供服务。

* 在应用的主页上，选择“接受”以同意跟踪  。

  此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  下图展示了同意跟踪后的应用：

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* 按 Cmd-Opt-F5  ，以在不使用调试器的情况下运行。

  Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。

* 在应用的主页上，选择“接受”以同意跟踪  。

  此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  下图展示了同意跟踪后的应用：

  ![主页或索引页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a>检查项目文件

下面是主项目文件夹和文件的概述，将在后续教程中使用。

### <a name="pages-folder"></a>Pages 文件夹

包含 Razor 页面和支持文件。 每个 Razor 页面都是一对文件：

* 一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。
* 一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。

支持文件的名称以下划线开头。 例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。 此文件设置页面顶部的导航菜单和页面底部的版权声明。 有关更多信息，请参见<xref:mvc/views/layout>。

### <a name="wwwroot-folder"></a>wwwroot 文件夹

包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。 有关更多信息，请参见<xref:fundamentals/static-files>。

### <a name="appsettingsjson"></a>appSettings.json

包含配置数据，如连接字符串。 有关更多信息，请参见<xref:fundamentals/configuration/index>。

### <a name="programcs"></a>Program.cs

包含程序的入口点。 有关更多信息，请参见<xref:fundamentals/host/web-host>。

### <a name="startupcs"></a>Startup.cs

包含配置应用行为的代码，例如，是否需要同意 cookie。 有关更多信息，请参见<xref:fundamentals/startup>。

## <a name="additional-resources"></a>其他资源

* [本教程的 YouTube 版本](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 Razor Pages Web 应用。
> * 运行应用。
> * 检查项目文件。

进入系列的下一教程：

> [!div class="step-by-step"]
> [添加模型](xref:tutorials/razor-pages/model)
