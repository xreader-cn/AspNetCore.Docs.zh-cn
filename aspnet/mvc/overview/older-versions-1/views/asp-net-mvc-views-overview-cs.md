---
uid: mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-cs
title: ASP.NET MVC 视图概述 (C#) |Microsoft Docs
author: StephenWalther
description: 什么是 ASP.NET MVC 视图，它与有何 HTML 页面？ 在本教程中，Stephen Walther 向您介绍视图，并演示如何将 t...
ms.author: riande
ms.date: 02/16/2008
ms.assetid: 152ab1e5-aec2-4ea7-b8cc-27a24dd9acb8
msc.legacyurl: /mvc/overview/older-versions-1/views/asp-net-mvc-views-overview-cs
msc.type: authoredcontent
ms.openlocfilehash: a8e64a99549584f150d64d909ac97210257b1147
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444124"
---
<a name="aspnet-mvc-views-overview-c"></a>ASP.NET MVC 视图概述 (C#)
====================
通过[Stephen Walther](https://github.com/StephenWalther)

> 什么是 ASP.NET MVC 视图，它与有何 HTML 页面？ 在本教程中，Stephen Walther 向您介绍视图，并演示如何，您可以充分利用查看数据和视图中的 HTML 帮助程序。


本教程的目的是为你提供简要介绍 ASP.NET MVC 视图、 视图数据和 HTML 帮助程序。 本教程结束时，应了解如何创建新的视图、 将数据从控制器传递到视图，以及使用 HTML 帮助程序生成的内容视图中。

## <a name="understanding-views"></a>了解视图

对于 ASP.NET 或 Active Server Pages，ASP.NET MVC 不包括任何内容直接对应于一个页面。 ASP.NET MVC 应用程序中没有页面在浏览器的地址栏中键入的 URL 中的路径相对应的磁盘上。 到页面的 ASP.NET MVC 应用程序中最近的就是调用*视图*。

在 ASP.NET MVC 应用程序中，浏览器的传入请求映射到控制器操作。 控制器操作可能会返回一个视图。 但是，控制器操作可能会执行一些其他类型的操作时，例如将你重定向到另一个控制器操作。

代码清单 1 包含名为 HomeController 的简单控制器。 HomeController 公开名为 index （） 和 Details() 的两个控制器操作。

**代码清单 1-HomeController.cs**

[!code-csharp[Main](asp-net-mvc-views-overview-cs/samples/sample1.cs)]

你可以调用的第一个操作，index （） 操作中，通过浏览器地址栏中键入以下 URL:

/ Home/Index

你可以调用第二个操作，Details() 操作中，通过在浏览器中键入此地址：

/ 主页/详细信息

Index （） 操作返回的视图。 您创建的大多数操作将返回视图。 但是，操作可以返回其他类型的操作结果。 例如，Details() 操作返回传入的请求重定向到 index （） 操作 RedirectToActionResult。

Index （） 操作包含以下的单个代码行：

View();

这行代码返回一个视图，它必须位于你的 web 服务器上的以下路径：

\Views\Home\Index.aspx

视图的路径可以推断出的控制器的名称和控制器操作的名称。

如果您愿意，你可以明确地视图。 以下代码行返回名为 Fred 的视图：

视图 (Fred);

执行这行代码时，视图将返回从以下路径：

\Views\Home\Fred.aspx

> [!NOTE] 
> 
> 如果你打算为 ASP.NET MVC 应用程序创建单元测试它是一个好办法明确视图名称。 这样一来，可以创建单元测试以验证控制器操作返回预期的视图。


## <a name="adding-content-to-a-view"></a>将内容添加到视图

视图是 (X) HTML 文档可以包含脚本的标准。 使用脚本来向视图添加动态内容。

例如，代码清单 2 中的视图显示当前日期和时间。

**Listing 2 - \Views\Home\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample2.aspx)]

请注意，代码清单 2 中的 HTML 页的正文包含以下脚本：

&lt;% Response.Write(DateTime.Now);%&gt;

使用脚本分隔符&lt;%和 %&gt;标记的开头和结尾的脚本。 此脚本是用 C# 编写的。 它通过调用 response.write （） 方法将内容呈现到浏览器中显示当前日期和时间。 脚本分隔符&lt;%和 %&gt;可用于执行一个或多个语句。

由于经常调用 response.write （），Microsoft 可快捷方式来调用 response.write （） 方法。 列表 3 中的视图使用分隔符&lt;%= 和 %&gt;作为调用 response.write （） 的快捷方式。

**Listing 3 - Views\Home\Index2.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample3.aspx)]

可以使用任何.NET 语言在视图中生成动态内容。 通常情况下，将使用 Visual Basic.NET 或C#若要编写在控制器和视图。

## <a name="using-html-helpers-to-generate-view-content"></a>使用 HTML 帮助器生成视图的内容

若要更加轻松地将内容添加到视图，可以充分利用所谓*的 HTML 帮助器*。 HTML 帮助器，通常情况下，是生成一个字符串的方法。 HTML 帮助器可用于生成如文本框、 链接、 下拉列表和列表框的标准 HTML 元素。

例如，清单 4 利用了三个 HTML 帮助程序-中的视图的 BeginForm()、 TextBox() 和 Password() 帮助器-生成一个登录名窗体 （请参阅图 1）。

**Listing 4 -- \Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample4.aspx)]


[![新建项目对话框](asp-net-mvc-views-overview-cs/_static/image1.jpg)](asp-net-mvc-views-overview-cs/_static/image1.png)

**图 01**:一个标准的登录窗体 ([单击此项可查看原尺寸图像](asp-net-mvc-views-overview-cs/_static/image2.png))


所有 HTML 帮助器方法被调用视图的 Html 属性。 例如，通过调用 Html.TextBox() 方法呈现一个文本框。

请注意，使用脚本分隔符&lt;%= 和 %&gt;时调用的 Html.TextBox() 和 Html.Password() 帮助程序。 这些帮助程序只需返回的字符串。 您需要调用 response.write （），以便呈现到浏览器的字符串。

使用 HTML 帮助器方法是可选的。 它们使您的生活更轻松通过减少的 HTML 和您需要自己编写的脚本。 列表 5 中的视图而无需使用 HTML 帮助器呈现的视图列表 4 中完全相同的形式。

**Listing 5 -- \Views\Home\Login.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample5.aspx)]

此外可以创建自己的 HTML 帮助程序。 例如，可以创建 HTML 表中自动显示的一组数据库记录的 GridView() 帮助器方法。 我们在本教程中探讨此主题**创建自定义 HTML 帮助程序**。

## <a name="using-view-data-to-pass-data-to-a-view"></a>使用视图数据将数据传递到视图

使用视图数据将数据从控制器传递到视图。 将像通过邮件发送的包的视图数据。 必须使用此包发送所有数据从控制器传递给视图。 例如，控制器列表 6 中的添加一条消息，若要查看数据。

**代码清单 6-ProductController.cs**

[!code-csharp[Main](asp-net-mvc-views-overview-cs/samples/sample6.cs)]

控制器 ViewData 属性表示的名称和值对的集合。 列表 6 中 index （） 方法将项添加到视图的数据集合名为消息，其值 Hello World ！。 Index （） 方法返回视图，则视图数据将自动传递给视图。

列表 7 中的视图从查看数据中检索消息并呈现到浏览器的消息。

**Listing 7 -- \Views\Product\Index.aspx**

[!code-aspx[Main](asp-net-mvc-views-overview-cs/samples/sample7.aspx)]

请注意视图充分利用 Html.Encode() 的 HTML 帮助器方法，当呈现该消息。 Html.Encode() HTML 帮助器将特殊字符的编码等&lt;和&gt;到安全地在 web 页中显示的字符。 只要呈现在用户提交到网站的内容，应该对要阻止 JavaScript 注入攻击的内容进行编码。

（因为我们创建我们自己在 ProductController 中，我们的确不需要对消息进行编码的消息。 但是，它是一个好习惯以显示内容检索从视图中查看数据时，始终调用 Html.Encode() 方法。）

在列表 7 中，我们利用了视图数据将简单的字符串消息从控制器传递到视图。 此外可以使用视图数据将传递其他类型的数据，如数据库记录，从控制器到视图的集合。 例如，如果你想要的产品数据库表的内容显示在视图中，则您会传递数据库的集合视图中记录的数据。

此外可以将强类型的视图数据从控制器传递到视图。 我们在本教程中探讨此主题**了解强类型化视图数据和视图**。

## <a name="summary"></a>总结

本教程提供简要介绍 ASP.NET MVC 视图、 视图数据和 HTML 帮助程序。 在第一个部分中，您学习了如何将新视图添加到你的项目。 您学习了，您必须要从特定控制器调用，到正确的文件夹添加一个视图。 接下来，我们讨论了 HTML 帮助程序的主题。 您学习了如何 HTML 帮助器使您能够轻松地生成标准的 HTML 内容。 最后，您学习了如何利用的视图数据将数据从控制器传递到视图。

> [!div class="step-by-step"]
> [下一页](creating-custom-html-helpers-cs.md)
