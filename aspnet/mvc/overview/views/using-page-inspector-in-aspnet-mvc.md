---
uid: mvc/overview/views/using-page-inspector-in-aspnet-mvc
title: 在 ASP.NET MVC 中使用 Page Inspector |Microsoft Docs
author: rick-anderson
description: 在 Visual Studio 2012 中的 Page Inspector 是一个 web 开发工具，使用集成浏览器。 选择任何元素中的集成浏览器和 Page Inspector i...
ms.author: riande
ms.date: 08/15/2012
ms.assetid: c7e4e1ab-4932-4614-9f53-aaf7c706d498
msc.legacyurl: /mvc/overview/views/using-page-inspector-in-aspnet-mvc
msc.type: authoredcontent
ms.openlocfilehash: 0dea8b077878139a3f513cb51447b86a93fe55b8
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236531"
---
<a name="using-page-inspector-in-aspnet-mvc"></a>在 ASP.NET MVC 中使用 Page Inspector
====================
由 Tim Ammann

> 在 Visual Studio 2012 中的 Page Inspector 是一个 web 开发工具，使用集成浏览器。 在集成浏览器中选择任何元素和 Page Inspector 立即突出显示元素的源和 CSS。 可以浏览任何 MVC 视图、 快速查找源呈现的标记，并使用浏览器工具直接在 Visual Studio 环境。
> 
> [观看视频](../../videos/mvc-4/using-page-inspector-in-aspnet-mvc.md)
> 
> 本教程演示如何启用检查模式下，然后快速找到并编辑你的 web 项目中的标记和 CSS。 本教程使用 MVC 项目中，但也可以使用用于 Page Inspector [Web 窗体](https://go.microsoft.com/?linkid=9802001)和其他 ASP.NET 应用程序。
> 
> 本教程包含以下部分：
> 
> - [系统必备](#_1_prerequisites)
> - [创建 Web 应用程序](#_2_creating_a)
> - [使用 Page Inspector 浏览到视图](#_3_using_page)
> - [启用检查模式](#_4_inspection_mode)
> - [使用 Page Inspector 对标记进行的更改](#_5_using_page)
> - [检查模式下和 HTML 窗口](#_6_inspection_mode)
> - [在样式窗口中预览 CSS 更改](#_7_previewing_css)
> - [CSS 自动同步](#css_auto_sync)
> - [使用 CSS 颜色选取器](#css_color_picker)
> - [动态页元素映射到 JavaScript](#map_dynamic_elements)


<a id="_prerequisites"></a><a id="_1_prerequisites"></a>

## <a name="prerequisites"></a>系统必备

- [Visual Studio 2012](https://www.microsoft.com/visualstudio/11)或[Visual Studio Express 2012 for Web](https://www.microsoft.com/visualstudio/11/downloads#express-web)。

> [!NOTE]
> 若要获取 Page Inspector 的最新版本，请使用[Web 平台安装程序](https://go.microsoft.com/fwlink/?LinkId=255386)要安装 Windows Azure SDK for.NET 2.0。


Page Inspector 与 Microsoft Web 开发人员工具捆绑在一起。 最新版本是 1.3。 若要检查的版本，运行 Visual Studio 并选择**关于 Microsoft Visual Studio**从**帮助**菜单。

<a id="_creating_a_web"></a><a id="_2_creating_a"></a>

## <a name="create-a-web-application"></a>创建 Web 应用程序

首先，创建将使用与 Page Inspector 的 web 应用程序。 在 Visual Studio 中，选择**文件** &gt; **新项目**。 在左侧，展开**Visual C#**，选择**Web**，然后选择**ASP.NET MVC4 Web 应用程序**。

![新的 ASP.NET MVC 应用程序](using-page-inspector-in-aspnet-mvc/_static/image2.png)

单击 **“确定”**。

在中**新的 ASP.NET MVC 4 项目**对话框中，选择**Internet 应用程序**。 将保留**Razor**作为默认视图引擎。

![新的 ASP.NET MVC 项目的 Internet 应用程序](using-page-inspector-in-aspnet-mvc/_static/image4.png)

应用程序中打开**源**视图。

![在源视图中的新 ASP.NET MVC 应用程序](using-page-inspector-in-aspnet-mvc/_static/image6.png)

现在，已有要使用的应用程序，可以使用 Page Inspector 检查并对其进行修改。

<a id="_starting_page_inspector"></a><a id="_3_using_page"></a>

## <a name="use-page-inspector-to-browse-to-a-view"></a>使用 Page Inspector 浏览到视图

在 Visual Studio 2012 中，您可以右键单击任何视图在项目中，选择**在 Page Inspector 中的查看**，Page Inspector 将找出路由并显示的页面。

在中**解决方案资源管理器**，展开**视图**文件夹，然后**主页**文件夹。 右键单击 Index.cshtml 文件，然后选择**在 Page Inspector 中的查看**。

![在 Page Inspector 中的视图 Index.cshtml](using-page-inspector-in-aspnet-mvc/_static/image8.png)

默认情况下，Page Inspector 停靠为窗口在左侧和右侧的 Visual Studio 环境。 如果您愿意，可以将它停靠在其他位置，也可以取消停靠该窗口。 请参阅[如何：排列和停靠窗口](https://msdn.microsoft.com/library/z4y0hsax.aspx)。

页面检查器窗口的顶部窗格会显示在浏览器窗口中当前页。 在底部窗格显示在 HTML 标记，以及允许您检查页的不同方面某些选项卡中的页。 在底部窗格是类似于[F12 开发人员工具](https://msdn.microsoft.com/ie/aa740478)Internet 资源管理器中。

![在 Page Inspector 中的 ASP.NET MVC 应用程序](using-page-inspector-in-aspnet-mvc/_static/image10.png)

在本教程中，您将使用**HTML**并**样式**选项卡来快速导航并对应用程序进行更改。

<a id="_examining_(&quot;decomposing&quot;)_the"></a><a id="_inspection_mode_and"></a><a id="_4_inspection_mode"></a>

## <a name="enableinspection-mode"></a>EnableInspection 模式

若要将 Page Inspector 置于检查模式，请单击**检查**按钮。 在检查模式下，将鼠标指针拖过任何部分呈现的页中，相应的源标记或代码会突出显示。

![切换检查模式](using-page-inspector-in-aspnet-mvc/_static/image12.png)

现在将鼠标移动 Page Inspector 中页面的不同部分。 执行操作时，鼠标指针更改为较大的加号，并突出显示下面的元素：

![悬停在 div.content 包装器](using-page-inspector-in-aspnet-mvc/_static/image14.png)

移动鼠标指针时，Visual Studio 突出显示相应的 Razor 语法的源文件中。 如果 HTML 元素来自另一个源文件，Visual Studio 会自动打开该文件。

在 Page Inspector **HTML**选项卡显示了从 Razor 语法生成的 HTML。 移动鼠标指针时，将突出显示的 HTML 元素。 **样式**选项卡显示该元素的 CSS 规则。

<a id="_5_using_page"></a>

## <a name="use-page-inspector-to-make-changes-to-markup"></a>使用 Page Inspector 对标记进行的更改

Page Inspector 允许您查找其位置可能不太明显的标记。 然后可以修改标记，并查看所产生的更改。

若要看到此错误，请单击**检查**然后向下滚动到页面检查器窗口中的页的底部。

当您将鼠标指针移动到的页脚区域时，Page Inspector 将打开\_Layout.cshtml 文件并突出显示所选的布局页部分。 如您所见，页脚会在布局文件和视图本身中定义。

![页脚](using-page-inspector-in-aspnet-mvc/_static/image16.png)

现在将鼠标指针移在行的版权<a id="a"></a>注意到。 在\_Layout.cshtml 页上，相应的行突出显示。

![页脚版权行突出显示](using-page-inspector-in-aspnet-mvc/_static/image18.png)

将一些文本添加到中的行末尾\_Layout.cshtml 文件。

&lt;p&gt;&amp;复制;@DateTime.Now.Year -我的 ASP.NET MVC 应用程序 Rocks ！ &lt; /p&gt;

现在，按 Ctrl + Alt + Enter 或单击更新条以查看 Page Inspector 浏览器窗口中的结果。

![我的 ASP.NET 应用程序 Rocks ！](using-page-inspector-in-aspnet-mvc/_static/image20.png)

您可能会想到页脚定义在 Index.cshtml，但事实证明，在\_Layout.cshtml，并为你找到的 Page Inspector。

<a id="_inspection_mode_and_1"></a><a id="_6_inspection_mode"></a>

## <a name="inspection-mode-and-the-html-window"></a>检查模式下和 HTML 窗口

接下来，您必须快速看一下 HTML 窗口以及它如何为您映射元素。

单击**检查**将 Page Inspector 置于检查模式。

单击显示"你 logohere"页的上半部分。 正在检查中更多详细信息，因此您将鼠标指针移动无法再更改浏览器窗口中的显示的特定元素。

现在，转到鼠标指针**HTML**窗口。 移动鼠标指针时，Page Inspector 概述了中的元素**HTML**窗口，并突出显示浏览器窗口中的相应元素。

![HTML 窗口](using-page-inspector-in-aspnet-mvc/_static/image22.png)

如前面一样，Page Inspector 将打开\_为你临时选项卡中的 Layout.cshtml 文件。单击\_Layout.cshtml 临时选项卡，并相应的标记将突出显示为&lt;标头&gt;为您的部分：

![突出显示的标记](using-page-inspector-in-aspnet-mvc/_static/image24.png)

<a id="_using_page_inspector"></a><a id="_7_previewing_css"></a>

## <a name="preview-css-changes-in-the-styles-window"></a>在样式窗口中预览 CSS 更改

接下来，将使用 Page Inspector**样式**窗口来预览对 CSS 的更改。

单击**检查**将 Page Inspector 置于检查模式。

在 Page Inspector 浏览器窗口中，将鼠标指针移动到"主页"部分**div.content 包装**显示标签。

![悬停在 div.content 包装器](using-page-inspector-in-aspnet-mvc/_static/image26.png)

单击后，div.content 包装器部分中，然后移动鼠标指针指向**样式**窗口。 **样式**窗口将显示所有此元素的 CSS 规则。 向下滚动到查找.featured.content 包装器类选择器。 现在，请清除复选框的背景色属性。

![清除背景色](using-page-inspector-in-aspnet-mvc/_static/image28.png)

请注意如何更改预览可立即在 Page Inspector 浏览器窗口。

再次选中的复选框，然后双击属性值，并将其更改为红色。 更改立即显示：

![红色背景色](using-page-inspector-in-aspnet-mvc/_static/image30.png)

**样式**窗口进行，可以轻松地测试并预览 CSS 更改之前将更改提交到样式表本身。

<a id="css_auto_sync"></a>
## <a name="css-auto-sync"></a>CSS 自动同步

> [!NOTE]
> 此功能需要 Page Inspector 的 1.3 版。


CSS 自动同步功能，可直接编辑 CSS 文件并查看立即在 Page Inspector 浏览器中的更改。

单击**检查**将 Page Inspector 置于检查模式。

在 Page Inspector 浏览器中，将移动鼠标指针的"主页"部分直到**div.content 包装**显示标签。 单击一次以选择此元素。

**样式**窗口将显示所有此元素的 CSS 规则。 向下滚动到查找.featured.content 包装器类选择器。 单击"包装器.featured.content"。 Page Inspector 将打开定义此样式 (Site.css) 并突出显示相应的 CSS 样式的 CSS 文件。

![](using-page-inspector-in-aspnet-mvc/_static/image32.png)

现在，更改的值为`background-color`为"red"。 Page Inspector 浏览器中立即显示更改。

![](using-page-inspector-in-aspnet-mvc/_static/image34.png)

<a id="css_color_picker"></a>
## <a name="using-the-css-color-picker"></a>使用 CSS 颜色选取器

在 Visual Studio 2012 CSS 编辑器具有颜色选取器，轻松地选择和插入颜色。 颜色选取器包括一个标准颜色的调色板，支持标准颜色名称、 哈希代码、 RGB、 RGBA、 HSL 和 HSLA 颜色和维护已在文档中最近使用的颜色的列表。

在上一节中您已更改的值`background-color`属性。 若要调用的颜色选取器，将插入点放在属性名称和类型之后**#** 或**rgb (**。

![CSS 颜色选取器栏](using-page-inspector-in-aspnet-mvc/_static/image36.png)

单击一种颜色来选择它，或按向下箭头键，然后使用左侧和右侧的箭头键遍历颜色。 当您访问一种颜色时，预览相应的十六进制值：

![预览的背景色属性值](using-page-inspector-in-aspnet-mvc/_static/image38.png)

如果在颜色栏不具有所需的确切颜色，可以使用颜色选取器 pop 列表。 若要打开它，请单击颜色栏右侧的双 v 形图标或按键盘上一次或两次的向下箭头。

![CSS 颜色选取器 Pop 列表](using-page-inspector-in-aspnet-mvc/_static/image40.png)

单击从右侧的垂直条的颜色。 这将在主窗口中显示该颜色渐变。 通过按 Enter，直接从垂直条中选择一种颜色，或单击以选择具有更高的精度在主窗口中的任意点。

你想要使用的计算机屏幕上是否存在一种颜色 （不一定要在 Visual Studio 用户界面内），你可以通过使用取色器工具右下角中捕获它的值。

此外可以通过移动颜色选取器底部的滑块来更改一种颜色的不透明度。 这样做的更改颜色值为 RGBA 值，因为 RGBA 格式可以表示不透明度。

选择一种颜色后，按 Enter，，然后键入分号完成中的背景色条目*Site.css*文件。

<a id="_the_update_bar"></a>

### <a name="the-page-inspector-update-bar"></a>页面检查器更新条

Page Inspector 立即检测到更改*Site.css*文件，并显示更新条中的警报。

![更新条](using-page-inspector-in-aspnet-mvc/_static/image42.png)

若要保存所有文件并刷新 Page Inspector 浏览器，请按 Ctrl + Alt + Enter 或单击更新条。 中的突出显示颜色更改显示在浏览器中。

<a id="map_dynamic_elements"></a>
## <a name="mapping-dynamic-page-elements-to-javascript"></a>动态页元素映射到 JavaScript

在新式 web 应用程序页面中的元素是通常动态生成的与 JavaScript 一起。 这意味着对应于这些页面元素没有静态标记 （HTML 或 Razor）。

使用版本 1.3，Page Inspector 现在映射到页回发至相应的 JavaScript 代码动态添加的项。 为了演示此功能，我们将使用[单页面应用程序 (SPA) 模板](../../../single-page-application/overview/introduction/knockoutjs-template.md)。

> [!NOTE]
> SPA 模板需要[ASP.NET 和 Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650)更新。


在 Visual Studio 中，选择**文件** &gt; **新项目**。 在左侧，展开**Visual C#**，选择**Web**，然后选择**ASP.NET MVC4 Web 应用程序**。 单击 **“确定”**。

在中**新的 ASP.NET MVC 4 项目**对话框中，选择**单页面应用程序**。

在解决方案资源管理器，展开**视图**文件夹，然后**主页**文件夹。 右键单击 Index.cshtml 文件，然后选择**在 Page Inspector 中的查看**。

第一件事就是显示在 Page Inspector 浏览器中是一个登录页面。 单击"注册"，创建一个用户名和密码。 一旦注册，应用程序会使你登录并创建包含一些示例项目的待办事项列表。

单击**检查**将 Page Inspector 置于检查模式。 在 Page Inspector 浏览器中，单击上一个待办事项。 请注意，而不是正在以蓝色突出显示，该元素以橙色突出显示，使用"JS"元素名称旁边。 这表示该元素已动态创建通过脚本。

![](using-page-inspector-in-aspnet-mvc/_static/image44.png)

此外，在出现一个橙色的下划线**调用堆栈**选项卡。这指示**调用堆栈**窗格会显示有关该元素的详细信息。

单击**调用堆栈**选项卡。**调用堆栈**窗格将显示创建该元素的 JavaScript 调用的调用堆栈。 调用外部库如 jQuery 处于折叠状态，以便可以轻松地查看对您的应用程序脚本的调用。

![](using-page-inspector-in-aspnet-mvc/_static/image46.png)

若要查看完整的堆栈，包括对外部库的调用可以展开标记为"外部库"的节点：

![](using-page-inspector-in-aspnet-mvc/_static/image48.png)

如果单击调用堆栈中的项，Visual Studio 将打开代码文件，并突出显示相应的脚本。

![](using-page-inspector-in-aspnet-mvc/_static/image50.png)

## <a name="see-also"></a>请参阅

[使用 Visual Studio 的 ASP.NET MVC 4 简介](../older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md)（ASP.net 网站）

[引入了 Page Inspector](https://channel9.msdn.com/posts/visual-studio-vnext-introducing-page-inspector/) （第 9 频道视频）

[Page Inspector 错误信息](https://go.microsoft.com/?linkid=9813062)(MSDN)
