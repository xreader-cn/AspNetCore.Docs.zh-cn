---
uid: mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1
title: 使用 HTML5 和 jQuery UI Datepicker 快捷日历与 ASP.NET MVC-第 1 部分 |Microsoft Docs
author: Rick-Anderson
description: 本教程将讲述如何使用编辑器模板、 显示模板和 jQuery UI datepicker 快捷日历 ASP.NET MV 中的基础知识...
ms.author: riande
ms.date: 08/29/2011
ms.assetid: c23d27f7-b0cf-44f2-8445-fb69e045c674
msc.legacyurl: /mvc/overview/older-versions/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc/using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1
msc.type: authoredcontent
ms.openlocfilehash: 3e700d2db4f86fe6734e2f08b01c9f8a8a69b6c3
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667786"
---
<a name="using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc---part-1"></a>使用 HTML5 和 jQuery UI Datepicker 快捷日历与 ASP.NET MVC-第 1 部分
====================
通过[Rick Anderson]((https://twitter.com/RickAndMSFT))

> 本教程将讲述如何使用编辑器模板、 显示模板和 jQuery UI datepicker 快捷日历中的 ASP.NET MVC Web 应用程序的基础知识。


本教程将讲述如何使用编辑器模板、 显示模板和 jQuery 的基础知识[UI datepicker 快捷日历](http://plugins.jquery.com/project/datepicker)ASP.NET MVC Web 应用程序中。 对于本教程中，可以使用 Microsoft Visual Web Developer 2010 Express Service Pack 1 (&quot;Visual Web Developer&quot;)，这是免费版本的 Microsoft Visual Studio 中，或如果已有的则可以使用 Visual Studio 2010 SP1。

在开始之前，请确保已安装以下列出的先决条件。 可以通过单击以下链接安装所有这些：[Web 平台安装程序](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)。 或者，您可以单独安装所需的软件，使用以下链接：

- [Visual Studio Web Developer Express SP1 必备组件](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
- [ASP.NET MVC 3 工具更新](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
- [SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)（运行时和工具支持）

如果你使用 Visual Studio 2010 而不 Visual Web Developer 中，通过单击以下链接安装必备组件：[Visual Studio 2010 必备组件](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack)。

本教程假定你已完成[MVC 3 入门](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md)教程或您熟悉 ASP.NET MVC 开发。 本教程开头的已完成项目[MVC 3 入门](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md)教程。

本教程介绍了 C# 中的代码。 但是，[初学者项目](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800)和已完成的项目也是 Visual Basic 中可用。

使用 Visual Studio 项目C#和 Visual Basic 源代码是可随附于本主题：[下载](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800)。

### <a name="what-youll-build"></a>你将生成

你将添加模板 （具体而言，编辑和显示模板） 的简单的电影列表应用程序中创建[MVC 3 入门](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md)教程。 您还将添加[jQuery UI datepicker](http://jqueryui.com/demos/datepicker/)弹出式日历来简化过程的输入日期。 下面的屏幕截图显示 jQuery UI datepicker 快捷日历与显示修改后的应用程序。

![已完成的 jQuery](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image1.png)

### <a name="skills-youll-learn"></a>你将学习的技能

下面是你将了解：

- 如何使用从特性[DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx)命名空间来显示时控制数据的格式和处于编辑模式。
- 如何创建模板 （编辑和显示模板） 来控制数据的格式设置。
- How to add [jQuery UI datepicker](http://jqueryui.com/demos/datepicker/)作为一种方法来输入日期字段。

### <a name="getting-started"></a>入门

如果还没有初学者项目提供的影片列表应用程序，下载它： 

* [下载](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098)。
* 在 Windows 资源管理器中右键单击*MvcMovie.zip*文件，然后选择**属性**。 
* 在中**MvcMovie.zip 属性**对话框中，选择**解除阻止**。 (取消阻止您尝试使用的安全警告 *.zip*已从 web 下载的文件。)

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image2.png)

右键单击*MvcMovie.zip*文件，然后选择**全部提取**来解压缩该文件。 在 Visual Web Developer 或 Visual Studio 2010 中，打开*MvcMovieCS\_TU.sln*文件。

在中**解决方案资源管理器**，双击*views/shared\\_Layout.cshtml*以将其打开。 更改`H1`标头**MVC 电影应用**到**电影 jQuery**。 按 CTRL + f5 键以运行该应用程序，然后单击**主页**选项卡上，转到`Index`电影控制器的方法。 若要试用该应用程序，选择**编辑**链接并**详细信息**一部电影的链接。 请注意，在索引中，编辑，并可以很好地设置格式的详细信息视图、 发布日期和价格：

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image3.png)

日期和价格的格式设置是使用的结果[DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx)上的属性的特性`Movie`类。

打开*Movie.cs*文件并注释掉`DisplayFormat`特性，可以在`ReleaseDate`和`Price`属性。 生成`Movie`类如下所示：

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/samples/sample1.cs)]

按 CTRL + F5 以运行该应用程序，并选择**主页**选项卡以查看电影列表。 这一次发布日期显示的日期和时间，并将价格字段不再显示的货币符号。 中的更改`Movie`类撤消很容易阅读了前面看到的但将解决此问题在一段时间。

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image4.png)

### <a name="using-the-dataannotations-datatype-attribute-to-specify-the-data-type"></a>使用 DataAnnotations DataType 特性以指定数据类型

将注释掉`DisplayFormat`特性`ReleaseDate`具有属性[数据类型](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx)属性，使用`Date`枚举。 替换`DisplayFormat`特性`Price`具有属性[数据类型](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx)同样，属性这一次使用`Currency`枚举。 这是已完成的代码如下所示：

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/samples/sample2.cs)]

运行该应用程序。 现在发布日期和价格属性被格式正确 （即使用相应的日期和货币格式）。 [数据类型](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx)属性提供类型元数据的内置的 ASP.NET MVC 模板，以便在正确的格式中呈现的字段。 使用`DataType`属性是比使用更好`DisplayFormat`最初是在代码中，因为属性`DataType`属性使模型更简洁、 更灵活的目的，例如国际化。

下一节中您将了解如何进行自定义模板来显示日期字段。

> [!div class="step-by-step"]
> [下一页](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2.md)
