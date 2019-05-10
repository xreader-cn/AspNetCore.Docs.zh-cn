---
title: 在 Visual Studio 中的 ASP.NET Core 中使用 LibMan
author: scottaddie
description: 了解如何使用 Visual Studio 的 ASP.NET Core 项目中使用 LibMan。
ms.author: scaddie
ms.custom: mvc
ms.date: 08/20/2018
uid: client-side/libman/libman-vs
ms.openlocfilehash: 41a5a41c8921b04290784d26441ecb46aea753e7
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64894964"
---
# <a name="use-libman-with-aspnet-core-in-visual-studio"></a>在 Visual Studio 中的 ASP.NET Core 中使用 LibMan

作者：[Scott Addie](https://twitter.com/Scott_Addie)

Visual Studio 在 ASP.NET Core 项目中内置了对 [LibMan](xref:client-side/libman/index) 的支持，包括：

* 支持在生成时配置和运行 LibMan 还原操作。
* 用于触发 LibMan 还原和清理操作的菜单项。
* 搜索对话框，查找库并将文件添加到项目中。
* 编辑对*libman.json*（LibMan  清单文件）的支持。&mdash;

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/client-side/libman/samples/) [（如何下载）](xref:index#how-to-download-a-sample)

## <a name="prerequisites"></a>系统必备

* Visual Studio 2017 版本 15.8 或更高版本，且具有**ASP.NET 和 Web 开发**工作负载

## <a name="add-library-files"></a>添加库文件

可通过两种不同的方式将库文件添加到 ASP.NET Core 项目中：

1. [使用“添加客户端库”对话框](#use-the-add-client-side-library-dialog)
1. [手动配置 LibMan 清单文件条目](#manually-configure-libman-manifest-file-entries)

### <a name="use-the-add-client-side-library-dialog"></a>使用“添加客户端库”对话框

请按照下列步骤以安装客户端库：

* 在中**解决方案资源管理器**，右键单击项目文件夹应在其中添加文件。 选择**添加** > **客户端库**。 **添加客户端库**此时将显示对话框：

  ![添加客户端库对话框](_static/add-library-dialog.png)

* 选择从库提供程序**提供程序**下拉列表。 CDNJS 是默认提供程序。
* 类型库名称中提取**库**文本框。 IntelliSense 提供了一系列从提供的文本的库。
* 从智能感知列表中选择的库。 请注意，库名称作为后缀`@`符号和已知的所选提供程序的最新稳定版本。
* 确定要包括的文件：
  * 选择**包括所有库文件**单选按钮，以都包括所有库的文件。
  * 选择**选择特定的文件**单选按钮，以包含库的文件的子集。 选中的单选按钮后，启用文件选择器树。 检查要下载的文件名称左侧的框。
* 指定用于存储中的文件的项目文件夹**目标位置**文本框。 为建议，将每个库存储在单独的文件夹。

  建议**目标位置**文件夹根据从中启动对话框的位置：

  * 如果从项目根目录中启动：
    * *wwwroot/lib*如果，则使用*wwwroot*存在。
    * *lib*如果，则使用*wwwroot*不存在。
  * 如果从项目文件夹启动，则使用相应的文件夹名称。

  库名称作为后缀文件夹建议。 Razor 页面项目中安装 jQuery 时下, 表说明了文件夹的建议。
  
  |启动位置                           |建议的文件夹      |
  |------------------------------------------|----------------------|
  |项目根目录 (如果*wwwroot*存在)        |*wwwroot/lib/jquery /* |
  |项目根目录 (如果*wwwroot*不存在) |*lib/jquery /*         |
  |*页*项目文件夹中                 |*页/jquery /*       |

* 单击**安装**按钮以下载的文件，每个中配置*libman.json*。
* 审阅**库管理器**的源**输出**安装详细信息窗口。 例如：

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

### <a name="manually-configure-libman-manifest-file-entries"></a>手动配置 LibMan 清单文件条目

在 Visual Studio 中的所有 LibMan 操作都基于项目根 LibMan 清单的内容 (*libman.json*)。 您可以手动编辑*libman.json*若要配置的项目的库文件。 Visual Studio 还原所有库文件一次*libman.json*保存。

若要打开*libman.json*进行编辑，存在以下选项：

* 双击*libman.json*中的文件**解决方案资源管理器**。
* 右键单击该项目中的**解决方案资源管理器**，然后选择**管理客户端库**。 **&#8224;**
* 选择**管理客户端库**从 Visual Studio**项目**菜单。 **&#8224;**

**&#8224;** 如果*libman.json*文件尚不存在项目根目录中，它将使用默认项目模板内容的创建。

Visual Studio 提供了丰富 JSON 编辑支持，例如颜色设置、 格式设置、 IntelliSense 和架构验证。 在找到 LibMan 清单的 JSON 架构[ http://json.schemastore.org/libman ](http://json.schemastore.org/libman)。

使用以下清单文件，LibMan 检索每中定义的配置文件`libraries`属性。 中定义的对象文字说明`libraries`后面：

* 一个子集[jQuery](https://jquery.com/)版本 3.3.1 检索从 CDNJS 提供程序。 在中定义的子集`files`属性&mdash;*jquery.min.js*，*只需要 jquery.js*，以及*jquery.min.map*。 将文件放置在项目的*wwwroot/lib/jquery*文件夹。
* 整个[Bootstrap](https://getbootstrap.com/)检索并放入版本 4.1.3 *wwwroot/lib/bootstrap*文件夹。 对象文字`provider`属性重写`defaultProvider`属性值。 LibMan 从 unpkg 提供程序中检索启动文件。
* 一个子集[lodash 等](https://lodash.com/)监管主体在组织内已批准。 *Lodash.js*并*lodash.min.js*从本地文件系统中检索文件*c:\\temp\\lodash 等\\*。 将文件复制到项目的*lodash 等wwwroot/lib/* 文件夹。

[!code-json[](samples/LibManSample/libman.json)]

> [!NOTE]
> LibMan 仅支持每个提供程序从每个库的一个版本。 *Libman.json*文件未通过架构验证，如果它包含与给定的提供程序库同名的两个库。

## <a name="restore-library-files"></a>库将文件还原

若要还原从 Visual Studio 中的库文件，必须有一个有效*libman.json*项目根目录中的文件。 还原的文件放置在项目中指定每个库的位置。

可以通过两种方式的 ASP.NET Core 项目中还原库文件：

1. [在生成过程中还原文件](#restore-files-during-build)
1. [手动还原文件](#restore-files-manually)

### <a name="restore-files-during-build"></a>在生成过程中还原文件

LibMan 可以作为生成过程的一部分还原的已定义的库文件。 默认情况下*还原上生成*禁用行为。

若要启用和测试还原上生成行为：

* 右键单击*libman.json*中**解决方案资源管理器**，然后选择**启用还原客户端上的库生成**从上下文菜单。
* 单击**是**按钮当系统提示安装 NuGet 包。 [Microsoft.Web.LibraryManager.Build](https://www.nuget.org/packages/Microsoft.Web.LibraryManager.Build/) NuGet 包添加到项目：

  [!code-xml[](samples/LibManSample/LibManSample.csproj?name=snippet_RestoreOnBuildPackage)]

* 生成项目，以确认 LibMan 文件还原发生。 `Microsoft.Web.LibraryManager.Build`包注入项目的生成操作期间运行 LibMan 的 MSBuild 目标。
* 审阅**构建**的源**输出**LibMan 活动日志窗口：

  ```console
  1>------ Build started: Project: LibManSample, Configuration: Debug Any CPU ------
  1>
  1>Restore operation started...
  1>Restoring library jquery@3.3.1...
  1>Restoring library bootstrap@4.1.3...
  1>
  1>2 libraries restored in 10.66 seconds
  1>LibManSample -> C:\LibManSample\bin\Debug\netcoreapp2.1\LibManSample.dll
  ========== Build: 1 succeeded, 0 failed, 0 up-to-date, 0 skipped ==========
  ```

启用还原上生成行为后， *libman.json*上下文菜单将显示**禁用还原客户端上的库生成**选项。 选择此选项删除`Microsoft.Web.LibraryManager.Build`包从项目文件的引用。 因此，客户端库不再存储在每个版本。

而不考虑还原上生成设置中，您可以手动还原在从任何时候*libman.json*上下文菜单。 有关详细信息，请参阅[手动还原文件](#restore-files-manually)。

### <a name="restore-files-manually"></a>手动还原文件

若要手动还原库文件：

* 对于解决方案中的所有项目：
  * 右键单击解决方案的名称**解决方案资源管理器**。
  * 选择**还原客户端库**选项。
* 对于特定的项目：
  * 右键单击*libman.json*中的文件**解决方案资源管理器**。
  * 选择**还原客户端库**选项。

尽管还原操作正在运行：

* Visual Studio 在状态栏上的任务状态中心 (TSC) 图标进行动画处理，并将读取*还原操作已启动*。 单击该图标可打开列出已知的后台任务的工具提示。
* 消息将发送到状态栏并**库管理器**的源**输出**窗口。 例如：

  ```console
  Restore operation started...
  Restoring libraries for project LibManSample
  Restoring library jquery@3.3.1... (LibManSample)
  wwwroot/lib/jquery/jquery.min.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.js written to destination (LibManSample)
  wwwroot/lib/jquery/jquery.min.map written to destination (LibManSample)
  Restore operation completed
  1 libraries restored in 2.32 seconds
  ```

## <a name="delete-library-files"></a>删除库文件

若要执行*干净*操作，这将删除以前还原在 Visual Studio 中的库文件：

* 右键单击*libman.json*中的文件**解决方案资源管理器**。
* 选择**干净的客户端库**选项。

若要防止无意删除非库文件，清除操作不会删除整个目录。 而只会删除包括在上一个还原的文件。

尽管运行清理操作：

* Visual Studio 在状态栏上的 TSC 图标进行动画处理，并将读取*启动客户端库操作*。 单击该图标可打开列出已知的后台任务的工具提示。
* 消息发送到状态栏并**库管理器**的源**输出**窗口。 例如：

```console
Clean libraries operation started...
Clean libraries operation completed
2 libraries were successfully deleted in 1.91 secs
```

清理操作只会从项目删除文件。 在将来还原操作的更快地检索缓存中保留库文件。 若要管理存储在本地计算机的缓存中的库文件，请使用[LibMan CLI](xref:client-side/libman/libman-cli)。

## <a name="uninstall-library-files"></a>卸载库文件

若要卸载库文件：

* 打开*libman.json*。
* 定位插入符号置于相应`libraries`对象文字。
* 单击左边距中显示的灯泡图标，然后选择**卸载\<library_name > @\<library_version >**:

  ![卸载库上下文菜单选项](_static/uninstall-menu-option.png)

或者，可以手动编辑和保存 LibMan 清单 (*libman.json*)。 [还原操作](#restore-library-files)运行时保存该文件。 不能再中定义的库文件*libman.json*从项目中删除。

## <a name="update-library-version"></a>更新库版本

若要检查有更新的库版本：

* 打开*libman.json*。
* 定位插入符号置于相应`libraries`对象文字。
* 单击灯泡图标显示在左边距中。 将鼠标悬停**检查是否有更新**。

LibMan 检查安装的版本比新的库版本。 会出现以下结果：

* 一个**找不到更新**如果已安装最新版本，则会显示消息。
* 最新稳定版本是显示如果还没有安装。

  ![检查更新上下文菜单选项](_static/update-menu-option.png)

* 如果现已安装的版本比新预发布版本，会显示预发行版。

若要降级到较旧的库版本，手动编辑*libman.json*文件。 保存文件时，LibMan[还原操作](#restore-library-files):

* 从以前的版本中移除冗余的文件。
* 新版本中添加新的和更新文件。

## <a name="additional-resources"></a>其他资源

* <xref:client-side/libman/libman-cli>
* [LibMan GitHub 存储库](https://github.com/aspnet/LibraryManager)
