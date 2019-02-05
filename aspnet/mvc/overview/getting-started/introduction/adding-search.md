---
uid: mvc/overview/getting-started/introduction/adding-search
title: 搜索 |Microsoft Docs
author: Rick-Anderson
description: ''
ms.author: riande
ms.date: 05/22/2015
ms.assetid: df001954-18bf-4550-b03d-43911a0ea186
msc.legacyurl: /mvc/overview/getting-started/introduction/adding-search
msc.type: authoredcontent
ms.openlocfilehash: 31fd35ac63f3eb31d824e1710833ad83a0852ac9
ms.sourcegitcommit: a91e8dd2f4b788114c8bc834507277f4b5e8d6c5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2019
ms.locfileid: "55712258"
---
<a name="search"></a>搜索
====================
通过[Rick Anderson]((https://twitter.com/RickAndMSFT))

[!INCLUDE [Tutorial Note](sample/code-location.md)]

## <a name="adding-a-search-method-and-search-view"></a>添加搜索方法和搜索视图

在本部分中，你将添加到搜索功能`Index`操作方法，您可以搜索电影的流派或名称。

## <a name="updating-the-index-form"></a>更新索引窗体

通过更新开始`Index`到现有的操作方法`MoviesController`类。 下面是代码：

[!code-csharp[Main](adding-search/samples/sample1.cs?highlight=1,6-9)]

第一行`Index`方法将创建以下[LINQ](https://msdn.microsoft.com/library/bb397926.aspx)查询用于选择电影：

[!code-csharp[Main](adding-search/samples/sample2.cs)]

该查询在此情况下，定义，但尚未尚未针对数据库运行。

如果`searchString`参数包含一个字符串，电影查询则修改要筛选的搜索字符串，使用下面的代码的值：

[!code-csharp[Main](adding-search/samples/sample3.cs)]

上面的 `s => s.Title` 代码是 [Lambda 表达式](https://msdn.microsoft.com/library/bb397687.aspx)。 Lambda 在基于方法的用于[LINQ](https://msdn.microsoft.com/library/bb397926.aspx)查询用作标准查询运算符方法的参数，如[其中](https://msdn.microsoft.com/library/system.linq.enumerable.where.aspx)上述代码中使用的方法。 进行定义或通过调用一个方法，如修改后不会执行 LINQ 查询`Where`或`OrderBy`。 相反，延迟查询执行，这意味着表达式的计算延迟，直到真正循环访问其实现的值或[ `ToList` ](https://msdn.microsoft.com/library/bb342261.aspx)调用方法。 在中`Search`示例中，该查询中执行*Index.cshtml*视图。 有关延迟执行查询的详细信息，请参阅[Query Execution](https://msdn.microsoft.com/library/bb738633.aspx)（查询执行）。

> [!NOTE]
> [Contains](https://msdn.microsoft.com/library/bb155125.aspx)方法运行数据库，而不是 c# 代码更高版本。 在数据库中， [Contains](https://msdn.microsoft.com/library/bb155125.aspx)映射到[SQL LIKE](https://msdn.microsoft.com/library/ms179859.aspx)，这是不区分大小写。

现在可以更新`Index`将向用户显示窗体的视图。

运行应用程序并导航到 */Movies/Index*。 将查询字符串（如 `?searchString=ghost`）追加到 URL。 筛选的电影将显示出来。

![SearchQryStr](adding-search/_static/image1.png)

如果您更改的签名`Index`方法，以使名为的参数`id`，则`id`参数将匹配`{id}`占位符默认路由中的设置*应用\_用户RouteConfig.cs*文件。

[!code-json[Main](adding-search/samples/sample4.json)]

原始`Index`方法如下所示::

[!code-csharp[Main](adding-search/samples/sample5.cs)]

已修改`Index`方法看起来，如下所示：

[!code-csharp[Main](adding-search/samples/sample6.cs?highlight=1,3)]

现可将搜索标题作为路由数据（ URL 段）而非查询字符串值进行传递。

![](adding-search/_static/image2.png)

但是，不能指望用户在每次要搜索电影时都修改 URL。 因此，现在您需要添加 UI 来帮助他们筛选电影。 如果已更改的签名`Index`方法来测试如何传递绑定路由的 ID 参数，将其更改回来，以使您`Index`方法采用字符串参数名为`searchString`:

[!code-csharp[Main](adding-search/samples/sample7.cs)]

打开*Views\Movies\Index.cshtml*文件，并紧后面`@Html.ActionLink("Create New", "Create")`，添加以下突出显示的窗体标记：

[!code-cshtml[Main](adding-search/samples/sample8.cshtml?highlight=12-15)]

`Html.BeginForm`帮助程序将创建一个打开`<form>`标记。 `Html.BeginForm`帮助器后，窗体会发布到其自身，当用户通过单击提交窗体**筛选器**按钮。

Visual Studio 2013 具有较好的改进时显示和编辑视图文件。 当你运行应用程序时的视图文件打开时，Visual Studio 2013 调用正确的控制器操作方法，以显示该视图。

![](adding-search/_static/image3.png)

索引视图 （如在上图中所示） 在 Visual Studio 中打开，点击 Ctr F5 或按 F5 运行应用程序，然后再尝试搜索电影。

![](adding-search/_static/image4.png)

没有任何`HttpPost`重载`Index`方法。 您不需要它，因为该方法不更改状态的应用程序，只需筛选数据。

可添加以下 `HttpPost Index` 方法。 在这种情况下，操作调用程序将匹配`HttpPost Index`方法，和`HttpPost Index`方法将运行下图中所示。

[!code-csharp[Main](adding-search/samples/sample9.cs)]

![SearchPostGhost](adding-search/_static/image5.png)

但是，即使添加 `Index` 方法的 `HttpPost` 版本，其实现方式也受到限制。 假设你想要将特定搜索加入书签，或向朋友发送一个链接，让他们单击链接即可查看筛选出的相同电影列表。 请注意，HTTP POST 请求的 URL 是 GET 请求 （localhost:xxxxx/Movies/Index） 的 URL 相同--没有 URL 本身中的搜索信息。 右现在，搜索字符串信息发送到服务器作为窗体字段值。 这意味着无法捕获该搜索信息加入书签或发送给朋友，在 URL 中。

解决方法是使用的重载`BeginForm`，它指定 POST 请求，应将搜索信息添加到 URL，并且应路由到`HttpGet`版本的`Index`方法。 替换现有无参数`BeginForm`方法替换为以下标记：

[!code-cshtml[Main](adding-search/samples/sample10.cshtml)]

![BeginFormPost_SM](adding-search/_static/image6.png)

现在提交搜索时，URL 包含搜索查询字符串。 即使具备 `HttpPost Index` 方法，搜索也将转到 `HttpGet Index` 操作方法。

![IndexWithGetURL](adding-search/_static/image7.png)

## <a name="adding-search-by-genre"></a>添加按流派搜索

如果添加了`HttpPost`版本的`Index`方法，现在将其删除。

接下来，你将添加特定功能，让用户按流派搜索电影。 将 `Index` 方法替换为以下代码：

[!code-csharp[Main](adding-search/samples/sample11.cs)]

此版本的`Index`方法采用附加参数，即`movieGenre`。 前的几行代码创建`List`对象以保存从数据库的电影流派。

下面的代码是一种 LINQ 查询，可从数据库中检索所有流派。

[!code-csharp[Main](adding-search/samples/sample12.cs)]

该代码使用`AddRange`方法的泛型`List`集合以添加到列表中的所有不同的流派。 (而无需`Distinct`修饰符，则添加重复的流派 — 例如，将在我们的示例中两次添加喜剧)。 然后代码将存储在流派列表的`ViewBag.MovieGenre`对象。 存储类别数据 （此类电影流派的） 作为[SelectList](https://msdn.microsoft.cus/library/system.web.mvc.selectlist(v=vs.108).aspx)对象中`ViewBag`，则访问下拉列表框中的类别数据是典型的 MVC 应用程序的方法。

下面的代码演示了如何检查`movieGenre`参数。 如果不为空，则代码进一步约束电影查询，以限制到指定类型的所选的电影。

[!code-csharp[Main](adding-search/samples/sample13.cs)]

如前面所述，运行查询时不在数据库上直到电影列表循环访问 (这在视图中，会发生后`Index`操作方法返回)。

## <a name="adding-markup-to-the-index-view-to-support-search-by-genre"></a>将标记添加到索引视图，以支持按流派搜索

添加`Html.DropDownList`到帮助程序*Views\Movies\Index.cshtml*文件，再`TextBox`帮助器。 完成的标记如下所示：

[!code-cshtml[Main](adding-search/samples/sample14.cshtml?highlight=11)]

在下面的代码：

[!code-cshtml[Main](adding-search/samples/sample15.cshtml)]

参数"MovieGenre"提供的键`DropDownList`帮助器以查找`IEnumerable<SelectListItem>`中`ViewBag`。 `ViewBag`填充操作方法中：

[!code-csharp[Main](adding-search/samples/sample16.cs?highlight=10)]

"All"提供选项标签参数。 如果在浏览器中查看该选择，您将看到其"值"属性为空。 由于我们的控制器仅筛选`if`字符串不是`null`或为空，将提交为空值`movieGenre`显示所有流派。

此外可以设置默认情况下选择的选项。 如果您希望"喜剧"作为默认选项，则会更改控制器中的代码如下所示：

[!code-cshtml[Main](adding-search/samples/sample17.cshtml)]

运行应用程序，并浏览到 */Movies/Index*。 按流派、 电影名称和这两个条件，请尝试搜索。

![](adding-search/_static/image8.png)

在本部分中创建了搜索操作方法和视图，以让用户搜索电影标题和流派。 在下一部分中，将探讨如何将属性添加到`Movie`模型以及如何添加初始值设定项将自动创建测试数据库。

> [!div class="step-by-step"]
> [上一页](examining-the-edit-methods-and-edit-view.md)
> [下一页](adding-a-new-field.md)
