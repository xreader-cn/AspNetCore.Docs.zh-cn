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
<a name="using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc---part-1"></a><span data-ttu-id="9b2de-103">使用 HTML5 和 jQuery UI Datepicker 快捷日历与 ASP.NET MVC-第 1 部分</span><span class="sxs-lookup"><span data-stu-id="9b2de-103">Using the HTML5 and jQuery UI Datepicker Popup Calendar with ASP.NET MVC - Part 1</span></span>
====================
<span data-ttu-id="9b2de-104">通过[Rick Anderson]((https://twitter.com/RickAndMSFT))</span><span class="sxs-lookup"><span data-stu-id="9b2de-104">by [Rick Anderson]((https://twitter.com/RickAndMSFT))</span></span>

> <span data-ttu-id="9b2de-105">本教程将讲述如何使用编辑器模板、 显示模板和 jQuery UI datepicker 快捷日历中的 ASP.NET MVC Web 应用程序的基础知识。</span><span class="sxs-lookup"><span data-stu-id="9b2de-105">This tutorial will teach you the basics of how to work with editor templates, display templates, and the jQuery UI datepicker popup calendar in an ASP.NET MVC Web application.</span></span>


<span data-ttu-id="9b2de-106">本教程将讲述如何使用编辑器模板、 显示模板和 jQuery 的基础知识[UI datepicker 快捷日历](http://plugins.jquery.com/project/datepicker)ASP.NET MVC Web 应用程序中。</span><span class="sxs-lookup"><span data-stu-id="9b2de-106">This tutorial will teach you the basics of how to work with editor templates, display templates, and the jQuery [UI datepicker popup calendar](http://plugins.jquery.com/project/datepicker) in an ASP.NET MVC Web application.</span></span> <span data-ttu-id="9b2de-107">对于本教程中，可以使用 Microsoft Visual Web Developer 2010 Express Service Pack 1 (&quot;Visual Web Developer&quot;)，这是免费版本的 Microsoft Visual Studio 中，或如果已有的则可以使用 Visual Studio 2010 SP1。</span><span class="sxs-lookup"><span data-stu-id="9b2de-107">For this tutorial, you can use Microsoft Visual Web Developer 2010 Express Service Pack 1 (&quot;Visual Web Developer&quot;), which is a free version of Microsoft Visual Studio, or you can use Visual Studio 2010 SP1 if you already have that.</span></span>

<span data-ttu-id="9b2de-108">在开始之前，请确保已安装以下列出的先决条件。</span><span class="sxs-lookup"><span data-stu-id="9b2de-108">Before you start, make sure you've installed the prerequisites listed below.</span></span> <span data-ttu-id="9b2de-109">可以通过单击以下链接安装所有这些：[Web 平台安装程序](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)。</span><span class="sxs-lookup"><span data-stu-id="9b2de-109">You can install all of them by clicking the following link: [Web Platform Installer](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack).</span></span> <span data-ttu-id="9b2de-110">或者，您可以单独安装所需的软件，使用以下链接：</span><span class="sxs-lookup"><span data-stu-id="9b2de-110">Alternatively, you can individually install the required software using the following links:</span></span>

- [<span data-ttu-id="9b2de-111">Visual Studio Web Developer Express SP1 必备组件</span><span class="sxs-lookup"><span data-stu-id="9b2de-111">Visual Studio Web Developer Express SP1 prerequisites</span></span>](https://www.microsoft.com/web/gallery/install.aspx?appid=VWD2010SP1Pack)
- [<span data-ttu-id="9b2de-112">ASP.NET MVC 3 工具更新</span><span class="sxs-lookup"><span data-stu-id="9b2de-112">ASP.NET MVC 3 Tools Update</span></span>](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=MVC3)
- <span data-ttu-id="9b2de-113">[SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)（运行时和工具支持）</span><span class="sxs-lookup"><span data-stu-id="9b2de-113">[SQL Server Compact 4.0](https://www.microsoft.com/web/gallery/install.aspx?appid=SQLCE;SQLCEVSTools_4_0)(runtime + tools support)</span></span>

<span data-ttu-id="9b2de-114">如果你使用 Visual Studio 2010 而不 Visual Web Developer 中，通过单击以下链接安装必备组件：[Visual Studio 2010 必备组件](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack)。</span><span class="sxs-lookup"><span data-stu-id="9b2de-114">If you're using Visual Studio 2010 instead of Visual Web Developer, install the prerequisites by clicking the following link: [Visual Studio 2010 prerequisites](https://www.microsoft.com/web/gallery/install.aspx?appsxml=&amp;appid=VS2010SP1Pack).</span></span>

<span data-ttu-id="9b2de-115">本教程假定你已完成[MVC 3 入门](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md)教程或您熟悉 ASP.NET MVC 开发。</span><span class="sxs-lookup"><span data-stu-id="9b2de-115">This tutorial assumes you have completed the [Getting Started with MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) tutorial or that you're familiar with ASP.NET MVC development.</span></span> <span data-ttu-id="9b2de-116">本教程开头的已完成项目[MVC 3 入门](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md)教程。</span><span class="sxs-lookup"><span data-stu-id="9b2de-116">This tutorial starts with the completed project from the [Getting Started with MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) tutorial.</span></span>

<span data-ttu-id="9b2de-117">本教程介绍了 C# 中的代码。</span><span class="sxs-lookup"><span data-stu-id="9b2de-117">This tutorial shows code in C#.</span></span> <span data-ttu-id="9b2de-118">但是，[初学者项目](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800)和已完成的项目也是 Visual Basic 中可用。</span><span class="sxs-lookup"><span data-stu-id="9b2de-118">However, the [starter project](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800) and completed project are also available in Visual Basic.</span></span>

<span data-ttu-id="9b2de-119">使用 Visual Studio 项目C#和 Visual Basic 源代码是可随附于本主题：[下载](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800)。</span><span class="sxs-lookup"><span data-stu-id="9b2de-119">A Visual Studio project with C# and Visual Basic source code is available to accompany this topic: [Download](https://archive.msdn.microsoft.com/Project/Download/FileDownload.aspx?ProjectName=aspnetmvcsamples&amp;DownloadId=15800).</span></span>

### <a name="what-youll-build"></a><span data-ttu-id="9b2de-120">你将生成</span><span class="sxs-lookup"><span data-stu-id="9b2de-120">What You'll Build</span></span>

<span data-ttu-id="9b2de-121">你将添加模板 （具体而言，编辑和显示模板） 的简单的电影列表应用程序中创建[MVC 3 入门](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md)教程。</span><span class="sxs-lookup"><span data-stu-id="9b2de-121">You'll add templates (specifically, edit and display templates) to the simple movie-listing application that was created in the [Getting Started with MVC 3](../getting-started-with-aspnet-mvc3/cs/intro-to-aspnet-mvc-3.md) tutorial.</span></span> <span data-ttu-id="9b2de-122">您还将添加[jQuery UI datepicker](http://jqueryui.com/demos/datepicker/)弹出式日历来简化过程的输入日期。</span><span class="sxs-lookup"><span data-stu-id="9b2de-122">You will also add a [jQuery UI datepicker](http://jqueryui.com/demos/datepicker/) popup calendar to simplify the process of entering dates.</span></span> <span data-ttu-id="9b2de-123">下面的屏幕截图显示 jQuery UI datepicker 快捷日历与显示修改后的应用程序。</span><span class="sxs-lookup"><span data-stu-id="9b2de-123">The following screenshot shows the modified application with the jQuery UI datepicker popup calendar displayed.</span></span>

![已完成的 jQuery](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image1.png)

### <a name="skills-youll-learn"></a><span data-ttu-id="9b2de-125">你将学习的技能</span><span class="sxs-lookup"><span data-stu-id="9b2de-125">Skills You'll Learn</span></span>

<span data-ttu-id="9b2de-126">下面是你将了解：</span><span class="sxs-lookup"><span data-stu-id="9b2de-126">Here's what you'll learn:</span></span>

- <span data-ttu-id="9b2de-127">如何使用从特性[DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx)命名空间来显示时控制数据的格式和处于编辑模式。</span><span class="sxs-lookup"><span data-stu-id="9b2de-127">How to use attributes from the [DataAnnotations](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.aspx) namespace to control the format of data when it's displayed and when it's in edit mode.</span></span>
- <span data-ttu-id="9b2de-128">如何创建模板 （编辑和显示模板） 来控制数据的格式设置。</span><span class="sxs-lookup"><span data-stu-id="9b2de-128">How to create templates (edit and display templates) to control the formatting of data.</span></span>
- <span data-ttu-id="9b2de-129">How to add [jQuery UI datepicker](http://jqueryui.com/demos/datepicker/)作为一种方法来输入日期字段。</span><span class="sxs-lookup"><span data-stu-id="9b2de-129">How to add the [jQuery UI datepicker](http://jqueryui.com/demos/datepicker/) as a way to enter date fields.</span></span>

### <a name="getting-started"></a><span data-ttu-id="9b2de-130">入门</span><span class="sxs-lookup"><span data-stu-id="9b2de-130">Getting Started</span></span>

<span data-ttu-id="9b2de-131">如果还没有初学者项目提供的影片列表应用程序，下载它：</span><span class="sxs-lookup"><span data-stu-id="9b2de-131">If you don't already have the movie-listing application from the starter project, download it:</span></span> 

* <span data-ttu-id="9b2de-132">[下载](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098)。</span><span class="sxs-lookup"><span data-stu-id="9b2de-132">[Download](https://code.msdn.microsoft.com/Introduction-to-MVC-3-10d1b098).</span></span>
* <span data-ttu-id="9b2de-133">在 Windows 资源管理器中右键单击*MvcMovie.zip*文件，然后选择**属性**。</span><span class="sxs-lookup"><span data-stu-id="9b2de-133">In Windows Explorer, right-click the *MvcMovie.zip* file and select **Properties**.</span></span> 
* <span data-ttu-id="9b2de-134">在中**MvcMovie.zip 属性**对话框中，选择**解除阻止**。</span><span class="sxs-lookup"><span data-stu-id="9b2de-134">In the **MvcMovie.zip Properties** dialog box, select **Unblock**.</span></span> <span data-ttu-id="9b2de-135">(取消阻止您尝试使用的安全警告 *.zip*已从 web 下载的文件。)</span><span class="sxs-lookup"><span data-stu-id="9b2de-135">(Unblocking prevents a security warning that occurs when you try to use a *.zip* file that you've downloaded from the web.)</span></span>

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image2.png)

<span data-ttu-id="9b2de-136">右键单击*MvcMovie.zip*文件，然后选择**全部提取**来解压缩该文件。</span><span class="sxs-lookup"><span data-stu-id="9b2de-136">Right-click the *MvcMovie.zip* file and select **Extract All** to unzip the file.</span></span> <span data-ttu-id="9b2de-137">在 Visual Web Developer 或 Visual Studio 2010 中，打开*MvcMovieCS\_TU.sln*文件。</span><span class="sxs-lookup"><span data-stu-id="9b2de-137">In Visual Web Developer or Visual Studio 2010, open the *MvcMovieCS\_TU.sln* file.</span></span>

<span data-ttu-id="9b2de-138">在中**解决方案资源管理器**，双击*views/shared\\_Layout.cshtml*以将其打开。</span><span class="sxs-lookup"><span data-stu-id="9b2de-138">In **Solution Explorer**, double-click the *Views\Shared\\_Layout.cshtml* to open it.</span></span> <span data-ttu-id="9b2de-139">更改`H1`标头**MVC 电影应用**到**电影 jQuery**。</span><span class="sxs-lookup"><span data-stu-id="9b2de-139">Change the `H1` header from **MVC Movie App** to **Movie jQuery**.</span></span> <span data-ttu-id="9b2de-140">按 CTRL + f5 键以运行该应用程序，然后单击**主页**选项卡上，转到`Index`电影控制器的方法。</span><span class="sxs-lookup"><span data-stu-id="9b2de-140">Press CTRL+F5 to run the application and click the **Home** tab, which takes you to the `Index` method of the movie controller.</span></span> <span data-ttu-id="9b2de-141">若要试用该应用程序，选择**编辑**链接并**详细信息**一部电影的链接。</span><span class="sxs-lookup"><span data-stu-id="9b2de-141">To try out the application, select the **Edit** link and the **Details** link for one of the movies.</span></span> <span data-ttu-id="9b2de-142">请注意，在索引中，编辑，并可以很好地设置格式的详细信息视图、 发布日期和价格：</span><span class="sxs-lookup"><span data-stu-id="9b2de-142">Notice that in the Index, Edit, and Details views, the release date and price are nicely formatted:</span></span>

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image3.png)

<span data-ttu-id="9b2de-143">日期和价格的格式设置是使用的结果[DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx)上的属性的特性`Movie`类。</span><span class="sxs-lookup"><span data-stu-id="9b2de-143">The formatting for the date and the price is the result of using the [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx) attribute on properties of the `Movie` class.</span></span>

<span data-ttu-id="9b2de-144">打开*Movie.cs*文件并注释掉`DisplayFormat`特性，可以在`ReleaseDate`和`Price`属性。</span><span class="sxs-lookup"><span data-stu-id="9b2de-144">Open the *Movie.cs* file and comment out the `DisplayFormat` attribute on the `ReleaseDate` and `Price` properties.</span></span> <span data-ttu-id="9b2de-145">生成`Movie`类如下所示：</span><span class="sxs-lookup"><span data-stu-id="9b2de-145">The resulting `Movie` class looks like this:</span></span>

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/samples/sample1.cs)]

<span data-ttu-id="9b2de-146">按 CTRL + F5 以运行该应用程序，并选择**主页**选项卡以查看电影列表。</span><span class="sxs-lookup"><span data-stu-id="9b2de-146">Press CTRL+F5 again to run the application and select the **Home** tab to view the movie list.</span></span> <span data-ttu-id="9b2de-147">这一次发布日期显示的日期和时间，并将价格字段不再显示的货币符号。</span><span class="sxs-lookup"><span data-stu-id="9b2de-147">This time the release date shows the date and time, and the price field no longer shows the currency symbol.</span></span> <span data-ttu-id="9b2de-148">中的更改`Movie`类撤消很容易阅读了前面看到的但将解决此问题在一段时间。</span><span class="sxs-lookup"><span data-stu-id="9b2de-148">Your change in the `Movie` class has undone the nice formatting that you saw earlier, but you'll fix that in a moment.</span></span>

![](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/_static/image4.png)

### <a name="using-the-dataannotations-datatype-attribute-to-specify-the-data-type"></a><span data-ttu-id="9b2de-149">使用 DataAnnotations DataType 特性以指定数据类型</span><span class="sxs-lookup"><span data-stu-id="9b2de-149">Using the DataAnnotations DataType Attribute to Specify the Data Type</span></span>

<span data-ttu-id="9b2de-150">将注释掉`DisplayFormat`特性`ReleaseDate`具有属性[数据类型](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx)属性，使用`Date`枚举。</span><span class="sxs-lookup"><span data-stu-id="9b2de-150">Replace the commented-out `DisplayFormat` attribute for the `ReleaseDate` property with the [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) attribute, using the `Date` enumeration.</span></span> <span data-ttu-id="9b2de-151">替换`DisplayFormat`特性`Price`具有属性[数据类型](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx)同样，属性这一次使用`Currency`枚举。</span><span class="sxs-lookup"><span data-stu-id="9b2de-151">Replace the `DisplayFormat` attribute for the `Price` property with the [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) attribute again, this time using the `Currency` enumeration.</span></span> <span data-ttu-id="9b2de-152">这是已完成的代码如下所示：</span><span class="sxs-lookup"><span data-stu-id="9b2de-152">This is what the completed code looks like:</span></span>

[!code-csharp[Main](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-1/samples/sample2.cs)]

<span data-ttu-id="9b2de-153">运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="9b2de-153">Run the application.</span></span> <span data-ttu-id="9b2de-154">现在发布日期和价格属性被格式正确 （即使用相应的日期和货币格式）。</span><span class="sxs-lookup"><span data-stu-id="9b2de-154">Now the release date and the price properties are formatted correctly (that is, using appropriate date and currency formats).</span></span> <span data-ttu-id="9b2de-155">[数据类型](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx)属性提供类型元数据的内置的 ASP.NET MVC 模板，以便在正确的格式中呈现的字段。</span><span class="sxs-lookup"><span data-stu-id="9b2de-155">The [DataType](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx) attribute provides type metadata for the built-in ASP.NET MVC templates so that the fields render in the correct format.</span></span> <span data-ttu-id="9b2de-156">使用`DataType`属性是比使用更好`DisplayFormat`最初是在代码中，因为属性`DataType`属性使模型更简洁、 更灵活的目的，例如国际化。</span><span class="sxs-lookup"><span data-stu-id="9b2de-156">Using the `DataType` attribute is preferable to using the `DisplayFormat` attribute that was originally in the code, because the `DataType` attribute makes the model cleaner and more flexible for purposes like internationalization.</span></span>

<span data-ttu-id="9b2de-157">下一节中您将了解如何进行自定义模板来显示日期字段。</span><span class="sxs-lookup"><span data-stu-id="9b2de-157">In the next section you'll see how to make custom templates to display date fields.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="9b2de-158">下一页</span><span class="sxs-lookup"><span data-stu-id="9b2de-158">Next</span></span>](using-the-html5-and-jquery-ui-datepicker-popup-calendar-with-aspnet-mvc-part-2.md)
