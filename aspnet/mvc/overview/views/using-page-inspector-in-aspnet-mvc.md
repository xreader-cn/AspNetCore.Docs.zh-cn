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
<a name="using-page-inspector-in-aspnet-mvc"></a><span data-ttu-id="aace8-104">在 ASP.NET MVC 中使用 Page Inspector</span><span class="sxs-lookup"><span data-stu-id="aace8-104">Using Page Inspector in ASP.NET MVC</span></span>
====================
<span data-ttu-id="aace8-105">由 Tim Ammann</span><span class="sxs-lookup"><span data-stu-id="aace8-105">by Tim Ammann</span></span>

> <span data-ttu-id="aace8-106">在 Visual Studio 2012 中的 Page Inspector 是一个 web 开发工具，使用集成浏览器。</span><span class="sxs-lookup"><span data-stu-id="aace8-106">Page Inspector in Visual Studio 2012 is a web development tool with an integrated browser.</span></span> <span data-ttu-id="aace8-107">在集成浏览器中选择任何元素和 Page Inspector 立即突出显示元素的源和 CSS。</span><span class="sxs-lookup"><span data-stu-id="aace8-107">Select any element in the integrated browser, and Page Inspector instantly highlights the element's source and CSS.</span></span> <span data-ttu-id="aace8-108">可以浏览任何 MVC 视图、 快速查找源呈现的标记，并使用浏览器工具直接在 Visual Studio 环境。</span><span class="sxs-lookup"><span data-stu-id="aace8-108">You can browse any MVC view, quickly find the sources of rendered markup, and use browser tools right within the Visual Studio environment.</span></span>
> 
> [<span data-ttu-id="aace8-109">观看视频</span><span class="sxs-lookup"><span data-stu-id="aace8-109">Watch the Video</span></span>](../../videos/mvc-4/using-page-inspector-in-aspnet-mvc.md)
> 
> <span data-ttu-id="aace8-110">本教程演示如何启用检查模式下，然后快速找到并编辑你的 web 项目中的标记和 CSS。</span><span class="sxs-lookup"><span data-stu-id="aace8-110">This tutorial shows how to enable Inspection Mode, and then quickly locate and edit markup and CSS within your web project.</span></span> <span data-ttu-id="aace8-111">本教程使用 MVC 项目中，但也可以使用用于 Page Inspector [Web 窗体](https://go.microsoft.com/?linkid=9802001)和其他 ASP.NET 应用程序。</span><span class="sxs-lookup"><span data-stu-id="aace8-111">The tutorial uses an MVC Project, but you can also use Page Inspector for [Web Forms](https://go.microsoft.com/?linkid=9802001) and other ASP.NET applications.</span></span>
> 
> <span data-ttu-id="aace8-112">本教程包含以下部分：</span><span class="sxs-lookup"><span data-stu-id="aace8-112">The tutorial has the following sections:</span></span>
> 
> - [<span data-ttu-id="aace8-113">系统必备</span><span class="sxs-lookup"><span data-stu-id="aace8-113">Prerequisites</span></span>](#_1_prerequisites)
> - [<span data-ttu-id="aace8-114">创建 Web 应用程序</span><span class="sxs-lookup"><span data-stu-id="aace8-114">Create a Web Application</span></span>](#_2_creating_a)
> - [<span data-ttu-id="aace8-115">使用 Page Inspector 浏览到视图</span><span class="sxs-lookup"><span data-stu-id="aace8-115">Use Page Inspector to Browse to a View</span></span>](#_3_using_page)
> - [<span data-ttu-id="aace8-116">启用检查模式</span><span class="sxs-lookup"><span data-stu-id="aace8-116">Enable Inspection Mode</span></span>](#_4_inspection_mode)
> - [<span data-ttu-id="aace8-117">使用 Page Inspector 对标记进行的更改</span><span class="sxs-lookup"><span data-stu-id="aace8-117">Use Page Inspector to Make Changes to Markup</span></span>](#_5_using_page)
> - [<span data-ttu-id="aace8-118">检查模式下和 HTML 窗口</span><span class="sxs-lookup"><span data-stu-id="aace8-118">Inspection Mode and the HTML Window</span></span>](#_6_inspection_mode)
> - [<span data-ttu-id="aace8-119">在样式窗口中预览 CSS 更改</span><span class="sxs-lookup"><span data-stu-id="aace8-119">Preview CSS Changes in the Styles window</span></span>](#_7_previewing_css)
> - [<span data-ttu-id="aace8-120">CSS 自动同步</span><span class="sxs-lookup"><span data-stu-id="aace8-120">CSS Auto Sync</span></span>](#css_auto_sync)
> - [<span data-ttu-id="aace8-121">使用 CSS 颜色选取器</span><span class="sxs-lookup"><span data-stu-id="aace8-121">Using the CSS Color Picker</span></span>](#css_color_picker)
> - [<span data-ttu-id="aace8-122">动态页元素映射到 JavaScript</span><span class="sxs-lookup"><span data-stu-id="aace8-122">Mapping Dynamic Page Elements to JavaScript</span></span>](#map_dynamic_elements)


<a id="_prerequisites"></a><a id="_1_prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="aace8-123">系统必备</span><span class="sxs-lookup"><span data-stu-id="aace8-123">Prerequisites</span></span>

- <span data-ttu-id="aace8-124">[Visual Studio 2012](https://www.microsoft.com/visualstudio/11)或[Visual Studio Express 2012 for Web](https://www.microsoft.com/visualstudio/11/downloads#express-web)。</span><span class="sxs-lookup"><span data-stu-id="aace8-124">[Visual Studio 2012](https://www.microsoft.com/visualstudio/11) or [Visual Studio Express 2012 for Web](https://www.microsoft.com/visualstudio/11/downloads#express-web).</span></span>

> [!NOTE]
> <span data-ttu-id="aace8-125">若要获取 Page Inspector 的最新版本，请使用[Web 平台安装程序](https://go.microsoft.com/fwlink/?LinkId=255386)要安装 Windows Azure SDK for.NET 2.0。</span><span class="sxs-lookup"><span data-stu-id="aace8-125">To get the latest version of Page Inspector, use [Web Platform Installer](https://go.microsoft.com/fwlink/?LinkId=255386) to install the Windows Azure SDK for .NET 2.0.</span></span>


<span data-ttu-id="aace8-126">Page Inspector 与 Microsoft Web 开发人员工具捆绑在一起。</span><span class="sxs-lookup"><span data-stu-id="aace8-126">Page Inspector is bundled with Microsoft Web Developer Tools.</span></span> <span data-ttu-id="aace8-127">最新版本是 1.3。</span><span class="sxs-lookup"><span data-stu-id="aace8-127">The latest version is 1.3.</span></span> <span data-ttu-id="aace8-128">若要检查的版本，运行 Visual Studio 并选择**关于 Microsoft Visual Studio**从**帮助**菜单。</span><span class="sxs-lookup"><span data-stu-id="aace8-128">To check which version you have, run Visual Studio and select **About Microsoft Visual Studio** from the **Help** menu.</span></span>

<a id="_creating_a_web"></a><a id="_2_creating_a"></a>

## <a name="create-a-web-application"></a><span data-ttu-id="aace8-129">创建 Web 应用程序</span><span class="sxs-lookup"><span data-stu-id="aace8-129">Create a Web Application</span></span>

<span data-ttu-id="aace8-130">首先，创建将使用与 Page Inspector 的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="aace8-130">First, create a web application that you will use Page Inspector with.</span></span> <span data-ttu-id="aace8-131">在 Visual Studio 中，选择**文件** &gt; **新项目**。</span><span class="sxs-lookup"><span data-stu-id="aace8-131">In Visual Studio, choose **File** &gt; **New Project**.</span></span> <span data-ttu-id="aace8-132">在左侧，展开**Visual C#**，选择**Web**，然后选择**ASP.NET MVC4 Web 应用程序**。</span><span class="sxs-lookup"><span data-stu-id="aace8-132">On the left, expand **Visual C#**, select **Web**, and then select **ASP.NET MVC4 Web Application**.</span></span>

![新的 ASP.NET MVC 应用程序](using-page-inspector-in-aspnet-mvc/_static/image2.png)

<span data-ttu-id="aace8-134">单击 **“确定”**。</span><span class="sxs-lookup"><span data-stu-id="aace8-134">Click **OK**.</span></span>

<span data-ttu-id="aace8-135">在中**新的 ASP.NET MVC 4 项目**对话框中，选择**Internet 应用程序**。</span><span class="sxs-lookup"><span data-stu-id="aace8-135">In the **New ASP.NET MVC 4 Project** dialog box, select **Internet Application**.</span></span> <span data-ttu-id="aace8-136">将保留**Razor**作为默认视图引擎。</span><span class="sxs-lookup"><span data-stu-id="aace8-136">Leave **Razor** as the default view engine.</span></span>

![新的 ASP.NET MVC 项目的 Internet 应用程序](using-page-inspector-in-aspnet-mvc/_static/image4.png)

<span data-ttu-id="aace8-138">应用程序中打开**源**视图。</span><span class="sxs-lookup"><span data-stu-id="aace8-138">The application opens in **Source** view.</span></span>

![在源视图中的新 ASP.NET MVC 应用程序](using-page-inspector-in-aspnet-mvc/_static/image6.png)

<span data-ttu-id="aace8-140">现在，已有要使用的应用程序，可以使用 Page Inspector 检查并对其进行修改。</span><span class="sxs-lookup"><span data-stu-id="aace8-140">Now that you have an application to work with, you can use Page Inspector to examine and modify it.</span></span>

<a id="_starting_page_inspector"></a><a id="_3_using_page"></a>

## <a name="use-page-inspector-to-browse-to-a-view"></a><span data-ttu-id="aace8-141">使用 Page Inspector 浏览到视图</span><span class="sxs-lookup"><span data-stu-id="aace8-141">Use Page Inspector to Browse to a View</span></span>

<span data-ttu-id="aace8-142">在 Visual Studio 2012 中，您可以右键单击任何视图在项目中，选择**在 Page Inspector 中的查看**，Page Inspector 将找出路由并显示的页面。</span><span class="sxs-lookup"><span data-stu-id="aace8-142">In Visual Studio 2012, you can right-click any view in your project, select **View in Page Inspector**, and Page Inspector will figure out the route and display the page.</span></span>

<span data-ttu-id="aace8-143">在中**解决方案资源管理器**，展开**视图**文件夹，然后**主页**文件夹。</span><span class="sxs-lookup"><span data-stu-id="aace8-143">In **Solution Explorer**, expand the **Views** folder and then the **Home** folder.</span></span> <span data-ttu-id="aace8-144">右键单击 Index.cshtml 文件，然后选择**在 Page Inspector 中的查看**。</span><span class="sxs-lookup"><span data-stu-id="aace8-144">Right click the Index.cshtml file and choose **View in Page Inspector**.</span></span>

![在 Page Inspector 中的视图 Index.cshtml](using-page-inspector-in-aspnet-mvc/_static/image8.png)

<span data-ttu-id="aace8-146">默认情况下，Page Inspector 停靠为窗口在左侧和右侧的 Visual Studio 环境。</span><span class="sxs-lookup"><span data-stu-id="aace8-146">By default, Page Inspector is docked as a window on the left side of the Visual Studio environment.</span></span> <span data-ttu-id="aace8-147">如果您愿意，可以将它停靠在其他位置，也可以取消停靠该窗口。</span><span class="sxs-lookup"><span data-stu-id="aace8-147">If you prefer, you can dock it elsewhere, or undock the window.</span></span> <span data-ttu-id="aace8-148">请参阅[如何：排列和停靠窗口](https://msdn.microsoft.com/library/z4y0hsax.aspx)。</span><span class="sxs-lookup"><span data-stu-id="aace8-148">See [How to: Arrange and Dock Windows](https://msdn.microsoft.com/library/z4y0hsax.aspx).</span></span>

<span data-ttu-id="aace8-149">页面检查器窗口的顶部窗格会显示在浏览器窗口中当前页。</span><span class="sxs-lookup"><span data-stu-id="aace8-149">The top pane of the Page Inspector window shows the current page in a browser window.</span></span> <span data-ttu-id="aace8-150">在底部窗格显示在 HTML 标记，以及允许您检查页的不同方面某些选项卡中的页。</span><span class="sxs-lookup"><span data-stu-id="aace8-150">The bottom pane shows the page in HTML markup, along with some tabs that let you inspect different aspects of the page.</span></span> <span data-ttu-id="aace8-151">在底部窗格是类似于[F12 开发人员工具](https://msdn.microsoft.com/ie/aa740478)Internet 资源管理器中。</span><span class="sxs-lookup"><span data-stu-id="aace8-151">The bottom pane is similar to the [F12 Developer Tools](https://msdn.microsoft.com/ie/aa740478) in Internet Explorer.</span></span>

![在 Page Inspector 中的 ASP.NET MVC 应用程序](using-page-inspector-in-aspnet-mvc/_static/image10.png)

<span data-ttu-id="aace8-153">在本教程中，您将使用**HTML**并**样式**选项卡来快速导航并对应用程序进行更改。</span><span class="sxs-lookup"><span data-stu-id="aace8-153">In this tutorial, you will use the **HTML** and **Styles** tabs to navigate quickly and make changes to the application.</span></span>

<a id="_examining_(&quot;decomposing&quot;)_the"></a><a id="_inspection_mode_and"></a><a id="_4_inspection_mode"></a>

## <a name="enableinspection-mode"></a><span data-ttu-id="aace8-154">EnableInspection 模式</span><span class="sxs-lookup"><span data-stu-id="aace8-154">EnableInspection Mode</span></span>

<span data-ttu-id="aace8-155">若要将 Page Inspector 置于检查模式，请单击**检查**按钮。</span><span class="sxs-lookup"><span data-stu-id="aace8-155">To put Page Inspector into Inspection Mode, click the **Inspect** button.</span></span> <span data-ttu-id="aace8-156">在检查模式下，将鼠标指针拖过任何部分呈现的页中，相应的源标记或代码会突出显示。</span><span class="sxs-lookup"><span data-stu-id="aace8-156">In Inspection Mode, when you hold the mouse pointer over any part of the rendered page, the corresponding source markup or code is highlighted.</span></span>

![切换检查模式](using-page-inspector-in-aspnet-mvc/_static/image12.png)

<span data-ttu-id="aace8-158">现在将鼠标移动 Page Inspector 中页面的不同部分。</span><span class="sxs-lookup"><span data-stu-id="aace8-158">Now move your mouse over different parts of the page within Page Inspector.</span></span> <span data-ttu-id="aace8-159">执行操作时，鼠标指针更改为较大的加号，并突出显示下面的元素：</span><span class="sxs-lookup"><span data-stu-id="aace8-159">As you do, the mouse pointer changes to a large plus sign, and the element underneath is highlighted:</span></span>

![悬停在 div.content 包装器](using-page-inspector-in-aspnet-mvc/_static/image14.png)

<span data-ttu-id="aace8-161">移动鼠标指针时，Visual Studio 突出显示相应的 Razor 语法的源文件中。</span><span class="sxs-lookup"><span data-stu-id="aace8-161">As you move the mouse pointer, Visual Studio highlights the corresponding Razor syntax in the source file.</span></span> <span data-ttu-id="aace8-162">如果 HTML 元素来自另一个源文件，Visual Studio 会自动打开该文件。</span><span class="sxs-lookup"><span data-stu-id="aace8-162">If the HTML element comes from another source file, Visual Studio automatically opens the file.</span></span>

<span data-ttu-id="aace8-163">在 Page Inspector **HTML**选项卡显示了从 Razor 语法生成的 HTML。</span><span class="sxs-lookup"><span data-stu-id="aace8-163">In Page Inspector, the **HTML** tab shows the HTML that was generated from the Razor syntax.</span></span> <span data-ttu-id="aace8-164">移动鼠标指针时，将突出显示的 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="aace8-164">As you move the mouse pointer, the HTML elements are highlighted.</span></span> <span data-ttu-id="aace8-165">**样式**选项卡显示该元素的 CSS 规则。</span><span class="sxs-lookup"><span data-stu-id="aace8-165">The **Styles** tab shows the CSS rules for the element.</span></span>

<a id="_5_using_page"></a>

## <a name="use-page-inspector-to-make-changes-to-markup"></a><span data-ttu-id="aace8-166">使用 Page Inspector 对标记进行的更改</span><span class="sxs-lookup"><span data-stu-id="aace8-166">Use Page Inspector to Make Changes to Markup</span></span>

<span data-ttu-id="aace8-167">Page Inspector 允许您查找其位置可能不太明显的标记。</span><span class="sxs-lookup"><span data-stu-id="aace8-167">Page Inspector lets you find markup whose location might not be obvious.</span></span> <span data-ttu-id="aace8-168">然后可以修改标记，并查看所产生的更改。</span><span class="sxs-lookup"><span data-stu-id="aace8-168">Then you can modify the markup and see the resulting changes.</span></span>

<span data-ttu-id="aace8-169">若要看到此错误，请单击**检查**然后向下滚动到页面检查器窗口中的页的底部。</span><span class="sxs-lookup"><span data-stu-id="aace8-169">To see this, click **Inspect** and then scroll to the bottom of the page in the Page Inspector window.</span></span>

<span data-ttu-id="aace8-170">当您将鼠标指针移动到的页脚区域时，Page Inspector 将打开\_Layout.cshtml 文件并突出显示所选的布局页部分。</span><span class="sxs-lookup"><span data-stu-id="aace8-170">When you move the mouse pointer into the footer area, Page Inspector opens the \_Layout.cshtml file and highlights the section of the layout page that you have selected.</span></span> <span data-ttu-id="aace8-171">如您所见，页脚会在布局文件和视图本身中定义。</span><span class="sxs-lookup"><span data-stu-id="aace8-171">As you can see, the footer are is defined in the layout file, and not the view itself.</span></span>

![页脚](using-page-inspector-in-aspnet-mvc/_static/image16.png)

<span data-ttu-id="aace8-173">现在将鼠标指针移在行的版权<a id="a"></a>注意到。</span><span class="sxs-lookup"><span data-stu-id="aace8-173">Now move your mouse pointer over the line with the copyright <a id="a"></a>notice.</span></span> <span data-ttu-id="aace8-174">在\_Layout.cshtml 页上，相应的行突出显示。</span><span class="sxs-lookup"><span data-stu-id="aace8-174">In the \_Layout.cshtml page, the corresponding line is highlighted.</span></span>

![页脚版权行突出显示](using-page-inspector-in-aspnet-mvc/_static/image18.png)

<span data-ttu-id="aace8-176">将一些文本添加到中的行末尾\_Layout.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="aace8-176">Add some text to the end of the line in the \_Layout.cshtml file.</span></span>

<span data-ttu-id="aace8-177">&lt;p&gt;&amp;复制;@DateTime.Now.Year -我的 ASP.NET MVC 应用程序 Rocks ！ &lt; /p&gt;</span><span class="sxs-lookup"><span data-stu-id="aace8-177">&lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET MVC Application Rocks!&lt;/p&gt;</span></span>

<span data-ttu-id="aace8-178">现在，按 Ctrl + Alt + Enter 或单击更新条以查看 Page Inspector 浏览器窗口中的结果。</span><span class="sxs-lookup"><span data-stu-id="aace8-178">Now, press Ctrl+Alt+Enter or click the Update Bar to see the results in the Page Inspector browser window.</span></span>

![我的 ASP.NET 应用程序 Rocks ！](using-page-inspector-in-aspnet-mvc/_static/image20.png)

<span data-ttu-id="aace8-180">您可能会想到页脚定义在 Index.cshtml，但事实证明，在\_Layout.cshtml，并为你找到的 Page Inspector。</span><span class="sxs-lookup"><span data-stu-id="aace8-180">You might have thought that the footer defined in Index.cshtml, but it turned out to be in the \_Layout.cshtml, and Page Inspector found it for you.</span></span>

<a id="_inspection_mode_and_1"></a><a id="_6_inspection_mode"></a>

## <a name="inspection-mode-and-the-html-window"></a><span data-ttu-id="aace8-181">检查模式下和 HTML 窗口</span><span class="sxs-lookup"><span data-stu-id="aace8-181">Inspection Mode and the HTML Window</span></span>

<span data-ttu-id="aace8-182">接下来，您必须快速看一下 HTML 窗口以及它如何为您映射元素。</span><span class="sxs-lookup"><span data-stu-id="aace8-182">Next, you will have a quick look at the HTML window and how it maps elements for you.</span></span>

<span data-ttu-id="aace8-183">单击**检查**将 Page Inspector 置于检查模式。</span><span class="sxs-lookup"><span data-stu-id="aace8-183">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span>

<span data-ttu-id="aace8-184">单击显示"你 logohere"页的上半部分。</span><span class="sxs-lookup"><span data-stu-id="aace8-184">Click the top part of the page that says "Your logohere".</span></span> <span data-ttu-id="aace8-185">正在检查中更多详细信息，因此您将鼠标指针移动无法再更改浏览器窗口中的显示的特定元素。</span><span class="sxs-lookup"><span data-stu-id="aace8-185">You are examining a particular element in more detail, so the display in the browser window no longer changes as you move the mouse pointer.</span></span>

<span data-ttu-id="aace8-186">现在，转到鼠标指针**HTML**窗口。</span><span class="sxs-lookup"><span data-stu-id="aace8-186">Now move the mouse pointer to the **HTML** window.</span></span> <span data-ttu-id="aace8-187">移动鼠标指针时，Page Inspector 概述了中的元素**HTML**窗口，并突出显示浏览器窗口中的相应元素。</span><span class="sxs-lookup"><span data-stu-id="aace8-187">As you move the mouse pointer, Page Inspector outlines the element within the **HTML** window and highlights the corresponding element in the browser window.</span></span>

![HTML 窗口](using-page-inspector-in-aspnet-mvc/_static/image22.png)

<span data-ttu-id="aace8-189">如前面一样，Page Inspector 将打开\_为你临时选项卡中的 Layout.cshtml 文件。单击\_Layout.cshtml 临时选项卡，并相应的标记将突出显示为&lt;标头&gt;为您的部分：</span><span class="sxs-lookup"><span data-stu-id="aace8-189">As before, Page Inspector opens the \_Layout.cshtml file for you in a temporary tab. Click the \_Layout.cshtml temporary tab, and the corresponding markup will be highlighted in the &lt;header&gt; section for you:</span></span>

![突出显示的标记](using-page-inspector-in-aspnet-mvc/_static/image24.png)

<a id="_using_page_inspector"></a><a id="_7_previewing_css"></a>

## <a name="preview-css-changes-in-the-styles-window"></a><span data-ttu-id="aace8-191">在样式窗口中预览 CSS 更改</span><span class="sxs-lookup"><span data-stu-id="aace8-191">Preview CSS Changes in the Styles Window</span></span>

<span data-ttu-id="aace8-192">接下来，将使用 Page Inspector**样式**窗口来预览对 CSS 的更改。</span><span class="sxs-lookup"><span data-stu-id="aace8-192">Next, you will use the Page Inspector **Styles** window to preview changes to CSS.</span></span>

<span data-ttu-id="aace8-193">单击**检查**将 Page Inspector 置于检查模式。</span><span class="sxs-lookup"><span data-stu-id="aace8-193">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span>

<span data-ttu-id="aace8-194">在 Page Inspector 浏览器窗口中，将鼠标指针移动到"主页"部分**div.content 包装**显示标签。</span><span class="sxs-lookup"><span data-stu-id="aace8-194">In the Page Inspector browser window, move the mouse pointer over the "Home Page" section until the **div.content-wrapper** label appears.</span></span>

![悬停在 div.content 包装器](using-page-inspector-in-aspnet-mvc/_static/image26.png)

<span data-ttu-id="aace8-196">单击后，div.content 包装器部分中，然后移动鼠标指针指向**样式**窗口。</span><span class="sxs-lookup"><span data-stu-id="aace8-196">Click within the div.content-wrapper section once, and then move the mouse pointer to the **Styles** window.</span></span> <span data-ttu-id="aace8-197">**样式**窗口将显示所有此元素的 CSS 规则。</span><span class="sxs-lookup"><span data-stu-id="aace8-197">The **Styles** window shows all of the CSS rules for this element.</span></span> <span data-ttu-id="aace8-198">向下滚动到查找.featured.content 包装器类选择器。</span><span class="sxs-lookup"><span data-stu-id="aace8-198">Scroll down to find the .featured .content-wrapper class selector.</span></span> <span data-ttu-id="aace8-199">现在，请清除复选框的背景色属性。</span><span class="sxs-lookup"><span data-stu-id="aace8-199">Now clear the checkbox for the background-color property.</span></span>

![清除背景色](using-page-inspector-in-aspnet-mvc/_static/image28.png)

<span data-ttu-id="aace8-201">请注意如何更改预览可立即在 Page Inspector 浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="aace8-201">Notice how the change previews instantly in the Page Inspector browser window.</span></span>

<span data-ttu-id="aace8-202">再次选中的复选框，然后双击属性值，并将其更改为红色。</span><span class="sxs-lookup"><span data-stu-id="aace8-202">Select the checkbox again, then double-click the property value and change it to red.</span></span> <span data-ttu-id="aace8-203">更改立即显示：</span><span class="sxs-lookup"><span data-stu-id="aace8-203">The change shows immediately:</span></span>

![红色背景色](using-page-inspector-in-aspnet-mvc/_static/image30.png)

<span data-ttu-id="aace8-205">**样式**窗口进行，可以轻松地测试并预览 CSS 更改之前将更改提交到样式表本身。</span><span class="sxs-lookup"><span data-stu-id="aace8-205">The **Styles** window makes it easy to test and preview CSS changes before you commit the changes to the style sheet itself.</span></span>

<a id="css_auto_sync"></a>
## <a name="css-auto-sync"></a><span data-ttu-id="aace8-206">CSS 自动同步</span><span class="sxs-lookup"><span data-stu-id="aace8-206">CSS Auto Sync</span></span>

> [!NOTE]
> <span data-ttu-id="aace8-207">此功能需要 Page Inspector 的 1.3 版。</span><span class="sxs-lookup"><span data-stu-id="aace8-207">This feature requires version 1.3 of Page Inspector.</span></span>


<span data-ttu-id="aace8-208">CSS 自动同步功能，可直接编辑 CSS 文件并查看立即在 Page Inspector 浏览器中的更改。</span><span class="sxs-lookup"><span data-stu-id="aace8-208">The CSS Auto-Sync feature allows you to edit a CSS file directly, and see the changes immediately in the Page Inspector browser.</span></span>

<span data-ttu-id="aace8-209">单击**检查**将 Page Inspector 置于检查模式。</span><span class="sxs-lookup"><span data-stu-id="aace8-209">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span>

<span data-ttu-id="aace8-210">在 Page Inspector 浏览器中，将移动鼠标指针的"主页"部分直到**div.content 包装**显示标签。</span><span class="sxs-lookup"><span data-stu-id="aace8-210">In the Page Inspector browser, move the mouse pointer over the "Home Page" section until the **div.content-wrapper** label appears.</span></span> <span data-ttu-id="aace8-211">单击一次以选择此元素。</span><span class="sxs-lookup"><span data-stu-id="aace8-211">Click once to select this element.</span></span>

<span data-ttu-id="aace8-212">**样式**窗口将显示所有此元素的 CSS 规则。</span><span class="sxs-lookup"><span data-stu-id="aace8-212">The **Styles** window shows all of the CSS rules for this element.</span></span> <span data-ttu-id="aace8-213">向下滚动到查找.featured.content 包装器类选择器。</span><span class="sxs-lookup"><span data-stu-id="aace8-213">Scroll down to find the .featured .content-wrapper class selector.</span></span> <span data-ttu-id="aace8-214">单击"包装器.featured.content"。</span><span class="sxs-lookup"><span data-stu-id="aace8-214">Click on ".featured .content-wrapper".</span></span> <span data-ttu-id="aace8-215">Page Inspector 将打开定义此样式 (Site.css) 并突出显示相应的 CSS 样式的 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="aace8-215">Page Inspector opens the CSS file that defines this style (Site.css) and highlights the corresponding CSS style.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image32.png)

<span data-ttu-id="aace8-216">现在，更改的值为`background-color`为"red"。</span><span class="sxs-lookup"><span data-stu-id="aace8-216">Now change the value for `background-color` to "red".</span></span> <span data-ttu-id="aace8-217">Page Inspector 浏览器中立即显示更改。</span><span class="sxs-lookup"><span data-stu-id="aace8-217">The change appears immediately in the Page Inspector browser.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image34.png)

<a id="css_color_picker"></a>
## <a name="using-the-css-color-picker"></a><span data-ttu-id="aace8-218">使用 CSS 颜色选取器</span><span class="sxs-lookup"><span data-stu-id="aace8-218">Using the CSS Color Picker</span></span>

<span data-ttu-id="aace8-219">在 Visual Studio 2012 CSS 编辑器具有颜色选取器，轻松地选择和插入颜色。</span><span class="sxs-lookup"><span data-stu-id="aace8-219">The CSS editor in Visual Studio 2012 has a color picker that makes it easy to choose and insert colors.</span></span> <span data-ttu-id="aace8-220">颜色选取器包括一个标准颜色的调色板，支持标准颜色名称、 哈希代码、 RGB、 RGBA、 HSL 和 HSLA 颜色和维护已在文档中最近使用的颜色的列表。</span><span class="sxs-lookup"><span data-stu-id="aace8-220">The color picker includes a standard palette of colors, supports standard color names, hash codes, RGB, RGBA, HSL, and HSLA colors, and maintains a list of the colors you've used most recently in the document.</span></span>

<span data-ttu-id="aace8-221">在上一节中您已更改的值`background-color`属性。</span><span class="sxs-lookup"><span data-stu-id="aace8-221">In the previous section, you changed the value of the `background-color` property.</span></span> <span data-ttu-id="aace8-222">若要调用的颜色选取器，将插入点放在属性名称和类型之后 **#** 或**rgb (**。</span><span class="sxs-lookup"><span data-stu-id="aace8-222">To invoke the color picker, place the insertion point after the property name and type **#** or **rgb(**.</span></span>

![CSS 颜色选取器栏](using-page-inspector-in-aspnet-mvc/_static/image36.png)

<span data-ttu-id="aace8-224">单击一种颜色来选择它，或按向下箭头键，然后使用左侧和右侧的箭头键遍历颜色。</span><span class="sxs-lookup"><span data-stu-id="aace8-224">Click on a color to select it, or press the down arrow key and then use the left and right arrow keys to traverse the colors.</span></span> <span data-ttu-id="aace8-225">当您访问一种颜色时，预览相应的十六进制值：</span><span class="sxs-lookup"><span data-stu-id="aace8-225">When you visit a color, the corresponding hex value is previewed:</span></span>

![预览的背景色属性值](using-page-inspector-in-aspnet-mvc/_static/image38.png)

<span data-ttu-id="aace8-227">如果在颜色栏不具有所需的确切颜色，可以使用颜色选取器 pop 列表。</span><span class="sxs-lookup"><span data-stu-id="aace8-227">If the color bar doesn't have the exact color you want, you can use the color picker pop-down.</span></span> <span data-ttu-id="aace8-228">若要打开它，请单击颜色栏右侧的双 v 形图标或按键盘上一次或两次的向下箭头。</span><span class="sxs-lookup"><span data-stu-id="aace8-228">To open it, click the double chevron at the right end of the color bar, or press the Down Arrow once or twice on the keyboard.</span></span>

![CSS 颜色选取器 Pop 列表](using-page-inspector-in-aspnet-mvc/_static/image40.png)

<span data-ttu-id="aace8-230">单击从右侧的垂直条的颜色。</span><span class="sxs-lookup"><span data-stu-id="aace8-230">Click a color from the vertical bar on the right.</span></span> <span data-ttu-id="aace8-231">这将在主窗口中显示该颜色渐变。</span><span class="sxs-lookup"><span data-stu-id="aace8-231">This shows a gradient for that color in the main window.</span></span> <span data-ttu-id="aace8-232">通过按 Enter，直接从垂直条中选择一种颜色，或单击以选择具有更高的精度在主窗口中的任意点。</span><span class="sxs-lookup"><span data-stu-id="aace8-232">Choose a color directly from the vertical bar by pressing Enter, or click any point in the main window to choose with greater precision.</span></span>

<span data-ttu-id="aace8-233">你想要使用的计算机屏幕上是否存在一种颜色 （不一定要在 Visual Studio 用户界面内），你可以通过使用取色器工具右下角中捕获它的值。</span><span class="sxs-lookup"><span data-stu-id="aace8-233">If there is a color on your computer screen that you want to use (it doesn't have to be inside the Visual Studio user interface), you can capture its value by using the eyedropper tool on the lower right.</span></span>

<span data-ttu-id="aace8-234">此外可以通过移动颜色选取器底部的滑块来更改一种颜色的不透明度。</span><span class="sxs-lookup"><span data-stu-id="aace8-234">You also can change the opacity of a color by moving the slider at the bottom of the color picker.</span></span> <span data-ttu-id="aace8-235">这样做的更改颜色值为 RGBA 值，因为 RGBA 格式可以表示不透明度。</span><span class="sxs-lookup"><span data-stu-id="aace8-235">Doing so changes color values to RGBA values, because the RGBA format can represent opacity.</span></span>

<span data-ttu-id="aace8-236">选择一种颜色后，按 Enter，，然后键入分号完成中的背景色条目*Site.css*文件。</span><span class="sxs-lookup"><span data-stu-id="aace8-236">After you have chosen a color, press Enter, and then type a semicolon to complete the background-color entry in the *Site.css* file.</span></span>

<a id="_the_update_bar"></a>

### <a name="the-page-inspector-update-bar"></a><span data-ttu-id="aace8-237">页面检查器更新条</span><span class="sxs-lookup"><span data-stu-id="aace8-237">The Page Inspector Update Bar</span></span>

<span data-ttu-id="aace8-238">Page Inspector 立即检测到更改*Site.css*文件，并显示更新条中的警报。</span><span class="sxs-lookup"><span data-stu-id="aace8-238">Page Inspector immediately detects the change to the *Site.css* file and displays an alert in an update bar.</span></span>

![更新条](using-page-inspector-in-aspnet-mvc/_static/image42.png)

<span data-ttu-id="aace8-240">若要保存所有文件并刷新 Page Inspector 浏览器，请按 Ctrl + Alt + Enter 或单击更新条。</span><span class="sxs-lookup"><span data-stu-id="aace8-240">To save all your files and refresh the Page Inspector browser, press Ctrl+Alt+Enter or click the update bar.</span></span> <span data-ttu-id="aace8-241">中的突出显示颜色更改显示在浏览器中。</span><span class="sxs-lookup"><span data-stu-id="aace8-241">The change in the highlight color appears in the browser.</span></span>

<a id="map_dynamic_elements"></a>
## <a name="mapping-dynamic-page-elements-to-javascript"></a><span data-ttu-id="aace8-242">动态页元素映射到 JavaScript</span><span class="sxs-lookup"><span data-stu-id="aace8-242">Mapping Dynamic Page Elements to JavaScript</span></span>

<span data-ttu-id="aace8-243">在新式 web 应用程序页面中的元素是通常动态生成的与 JavaScript 一起。</span><span class="sxs-lookup"><span data-stu-id="aace8-243">In modern web applications, elements in the page are often generated dynamically with JavaScript.</span></span> <span data-ttu-id="aace8-244">这意味着对应于这些页面元素没有静态标记 （HTML 或 Razor）。</span><span class="sxs-lookup"><span data-stu-id="aace8-244">That means there is no static markup (either HTML or Razor) that corresponds to these page elements.</span></span>

<span data-ttu-id="aace8-245">使用版本 1.3，Page Inspector 现在映射到页回发至相应的 JavaScript 代码动态添加的项。</span><span class="sxs-lookup"><span data-stu-id="aace8-245">With version 1.3, Page Inspector can now map items that were dynamically added to the page back to the corresponding JavaScript code.</span></span> <span data-ttu-id="aace8-246">为了演示此功能，我们将使用[单页面应用程序 (SPA) 模板](../../../single-page-application/overview/introduction/knockoutjs-template.md)。</span><span class="sxs-lookup"><span data-stu-id="aace8-246">To demonstrate this feature, we'll use the [Single Page Application (SPA) template](../../../single-page-application/overview/introduction/knockoutjs-template.md).</span></span>

> [!NOTE]
> <span data-ttu-id="aace8-247">SPA 模板需要[ASP.NET 和 Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650)更新。</span><span class="sxs-lookup"><span data-stu-id="aace8-247">The SPA template requires the [ASP.NET and Web Tools 2012.2](https://go.microsoft.com/fwlink/?LinkId=282650) update.</span></span>


<span data-ttu-id="aace8-248">在 Visual Studio 中，选择**文件** &gt; **新项目**。</span><span class="sxs-lookup"><span data-stu-id="aace8-248">In Visual Studio, choose **File** &gt; **New Project**.</span></span> <span data-ttu-id="aace8-249">在左侧，展开**Visual C#**，选择**Web**，然后选择**ASP.NET MVC4 Web 应用程序**。</span><span class="sxs-lookup"><span data-stu-id="aace8-249">On the left, expand **Visual C#**, select **Web**, and then select **ASP.NET MVC4 Web Application**.</span></span> <span data-ttu-id="aace8-250">单击 **“确定”**。</span><span class="sxs-lookup"><span data-stu-id="aace8-250">Click **OK**.</span></span>

<span data-ttu-id="aace8-251">在中**新的 ASP.NET MVC 4 项目**对话框中，选择**单页面应用程序**。</span><span class="sxs-lookup"><span data-stu-id="aace8-251">In the **New ASP.NET MVC 4 Project** dialog, select **Single Page Application**.</span></span>

<span data-ttu-id="aace8-252">在解决方案资源管理器，展开**视图**文件夹，然后**主页**文件夹。</span><span class="sxs-lookup"><span data-stu-id="aace8-252">In Solution Explorer, expand the **Views** folder and then the **Home** folder.</span></span> <span data-ttu-id="aace8-253">右键单击 Index.cshtml 文件，然后选择**在 Page Inspector 中的查看**。</span><span class="sxs-lookup"><span data-stu-id="aace8-253">Right click the Index.cshtml file and choose **View in Page Inspector**.</span></span>

<span data-ttu-id="aace8-254">第一件事就是显示在 Page Inspector 浏览器中是一个登录页面。</span><span class="sxs-lookup"><span data-stu-id="aace8-254">The first thing that is displayed in the Page Inspector browser is a login page.</span></span> <span data-ttu-id="aace8-255">单击"注册"，创建一个用户名和密码。</span><span class="sxs-lookup"><span data-stu-id="aace8-255">Click "Sign Up" and create a user name and password.</span></span> <span data-ttu-id="aace8-256">一旦注册，应用程序会使你登录并创建包含一些示例项目的待办事项列表。</span><span class="sxs-lookup"><span data-stu-id="aace8-256">Once you sign up, the application logs you in and creates a to-do list with some sample items.</span></span>

<span data-ttu-id="aace8-257">单击**检查**将 Page Inspector 置于检查模式。</span><span class="sxs-lookup"><span data-stu-id="aace8-257">Click **Inspect** to put Page Inspector in Inspection Mode.</span></span> <span data-ttu-id="aace8-258">在 Page Inspector 浏览器中，单击上一个待办事项。</span><span class="sxs-lookup"><span data-stu-id="aace8-258">In the Page Inspector browser, click on one of the to-do items.</span></span> <span data-ttu-id="aace8-259">请注意，而不是正在以蓝色突出显示，该元素以橙色突出显示，使用"JS"元素名称旁边。</span><span class="sxs-lookup"><span data-stu-id="aace8-259">Notice that instead of being highlighted in blue, the element is highlighted in orange, with "JS" next to the element name.</span></span> <span data-ttu-id="aace8-260">这表示该元素已动态创建通过脚本。</span><span class="sxs-lookup"><span data-stu-id="aace8-260">This indicates that the element was created dynamically through script.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image44.png)

<span data-ttu-id="aace8-261">此外，在出现一个橙色的下划线**调用堆栈**选项卡。这指示**调用堆栈**窗格会显示有关该元素的详细信息。</span><span class="sxs-lookup"><span data-stu-id="aace8-261">In addition, an orange underline appears on the **Call Stack** tab. This indicates that the **Call Stack** pane has more information about the element.</span></span>

<span data-ttu-id="aace8-262">单击**调用堆栈**选项卡。**调用堆栈**窗格将显示创建该元素的 JavaScript 调用的调用堆栈。</span><span class="sxs-lookup"><span data-stu-id="aace8-262">Click on the **Call Stack** tab. The **Call Stack** pane shows the call stack for the JavaScript call that created the element.</span></span> <span data-ttu-id="aace8-263">调用外部库如 jQuery 处于折叠状态，以便可以轻松地查看对您的应用程序脚本的调用。</span><span class="sxs-lookup"><span data-stu-id="aace8-263">Calls to external libraries such as jQuery are collapsed, so that you can easily see the calls to your application script.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image46.png)

<span data-ttu-id="aace8-264">若要查看完整的堆栈，包括对外部库的调用可以展开标记为"外部库"的节点：</span><span class="sxs-lookup"><span data-stu-id="aace8-264">To see the full stack, including calls to external libraries, you can expand the nodes labeled "External Libraries":</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image48.png)

<span data-ttu-id="aace8-265">如果单击调用堆栈中的项，Visual Studio 将打开代码文件，并突出显示相应的脚本。</span><span class="sxs-lookup"><span data-stu-id="aace8-265">If you click an item in the call stack, Visual Studio opens the code file and highlights the corresponding script.</span></span>

![](using-page-inspector-in-aspnet-mvc/_static/image50.png)

## <a name="see-also"></a><span data-ttu-id="aace8-266">请参阅</span><span class="sxs-lookup"><span data-stu-id="aace8-266">See Also</span></span>

<span data-ttu-id="aace8-267">[使用 Visual Studio 的 ASP.NET MVC 4 简介](../older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md)（ASP.net 网站）</span><span class="sxs-lookup"><span data-stu-id="aace8-267">[Intro to ASP.NET MVC 4 with Visual Studio](../older-versions/getting-started-with-aspnet-mvc4/intro-to-aspnet-mvc-4.md) (ASP.net website)</span></span>

<span data-ttu-id="aace8-268">[引入了 Page Inspector](https://channel9.msdn.com/posts/visual-studio-vnext-introducing-page-inspector/) （第 9 频道视频）</span><span class="sxs-lookup"><span data-stu-id="aace8-268">[Introducing Page Inspector](https://channel9.msdn.com/posts/visual-studio-vnext-introducing-page-inspector/) (Channel 9 video)</span></span>

<span data-ttu-id="aace8-269">[Page Inspector 错误信息](https://go.microsoft.com/?linkid=9813062)(MSDN)</span><span class="sxs-lookup"><span data-stu-id="aace8-269">[Page Inspector Error Messages](https://go.microsoft.com/?linkid=9813062) (MSDN)</span></span>
