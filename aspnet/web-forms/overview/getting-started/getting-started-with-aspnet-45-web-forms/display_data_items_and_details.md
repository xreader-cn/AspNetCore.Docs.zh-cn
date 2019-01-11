---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: 显示数据项和详细信息 |Microsoft Docs
author: Erikre
description: 本教程系列将指导您学习为 Web 构建具有 ASP.NET 4.7 和 Microsoft Visual Studio Community 2017 的 ASP.NET Web 窗体应用程序的基础知识
ms.author: riande
ms.date: 1/09/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: 73ae1660f5d6e3e28c1c155e745a62936e3502df
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207429"
---
<a name="display-data-items-and-details"></a>显示数据项和详细信息
====================
通过[Erik Reitan](https://github.com/Erikre)

> 本系列教程介绍了为 Web 构建具有 ASP.NET 4.7 和 Microsoft Visual Studio Community 2017 的 ASP.NET Web 窗体应用程序的基础知识。

在本教程中，您将学习如何显示数据项和使用 ASP.NET Web 窗体和 Entity Framework Code First 数据项详细信息。 本教程基于前面的"用户界面和导航"教程 Wingtip Toy 存储教程系列的一部分。 在已完成的教程中上的产品*ProductsList.aspx*页和产品的详细信息*ProductDetails.aspx*页会显示。

## <a name="what-you-learn"></a>学习内容

- 添加一个数据控件用于显示数据库产品。
- 连接到所选数据的数据控件。
- 添加一个数据控件来显示产品详细信息。
- 分析查询字符串值，并使用它来检索到的数据库数据进行筛选。

本教程中介绍的功能包括模型绑定和值提供程序。

## <a name="add-a-data-control-to-display-products"></a>添加用来显示产品的数据控件
 
有几种方法可以将数据绑定到服务器控件。 最常见的包括：

 * 添加数据源控件
 * 手动添加代码
 * 实现模型绑定

### <a name="use-a-data-source-control-to-bind-data"></a>使用数据源控件将数据绑定

添加数据源控件链接到显示的数据的控件的数据源控件。 使用此方法时，你可以以声明方式，而不是以编程方式连接到数据源的服务器端控件。

### <a name="code-by-hand-to-bind-data"></a>手动将数据绑定的代码

手动编写代码的过程包括：

1. 读取的值
2. 检查是否为 null
3. 将其转换为适当的类型
4. 检查转换成功
5. 使具有转换后的值的查询 

使用此方法时，可以完全控制数据访问逻辑。

### <a name="use-model-binding-to-bind-data"></a>使用模型绑定将数据绑定

使用模型绑定，将绑定包含很少的代码的结果，并且它使你能够重复使用在整个应用程序的功能。 它同时仍提供一个丰富的数据绑定框架简化了使用代码为中心的数据访问逻辑。

## <a name="display-products"></a>显示产品

在本教程中，使用模型绑定将数据绑定。 若要配置以使用模型绑定选择数据的数据控件，设置控件的`SelectMethod`属性页面的代码中的方法。 数据控件在页生命周期中的适当时间调用的方法，并自动将绑定返回的数据。 无需显式调用`DataBind`方法。

使用以下步骤，修改*ProductList.aspx*显示产品的标记。

1. 在中**解决方案资源管理器**，打开*ProductList.aspx*。

2. 现有标记替换为以下标记： 

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

上述标记使用**ListView**控件命名为`productList`用于显示产品。

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

使用模板和样式，可以定义如何**ListView**控件显示数据。 它可用于任何重复结构中的数据。 尽管这**ListView**示例仅显示数据库数据，你还可以无需代码，使用户可以编辑、 插入和删除数据，并可进行排序和页数据。

当您将设置`ItemType`中的属性**ListView**控件，数据绑定表达式`Item`可用和控件将成为强类型化。 例如，指定上一教程中所述，可以选择项对象的详细信息，IntelliSense、 `ProductName`:

![显示数据的项和详细信息-IntelliSense](display_data_items_and_details/_static/image1.png)

使用模型绑定，指定`SelectMethod`值 (`GetProducts`)。 这是将添加到代码的方法滞后，若要在下一步中显示的产品。

### <a name="add-code-to-display-products"></a>添加代码以显示产品

在此步骤中，添加代码以填充**ListView**数据库产品数据的控件。 该代码支持显示所有产品和各个类别的产品。

1. 在中**解决方案资源管理器**，右键单击*ProductList.aspx* ，然后选择**查看代码**。
2. 中的现有代码替换*ProductList.aspx.cs*与此文件：   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

此代码演示`GetProducts`方法的**ListView**控件的`ItemType`属性中引用*ProductList.aspx*。 若要将结果限制为特定数据库类别，该代码将设置`categoryId`值从查询字符串传递给*ProductList.aspx*。 `QueryStringAttribute`类中`System.Web.ModelBinding`命名空间用于检索查询字符串变量`id`的值。 这会指示模型绑定，在运行时，绑定到的查询字符串值`categoryId`参数。

时有效的类别 (`categoryId`) 是传递，结果会限制为该类别的数据库产品。 例如，如果*ProductsList.aspx*页 URL 是：

[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

页面仅显示产品其中`categoryId`等于`1`。

如果没有查询字符串传递，则显示所有产品。

调用这些方法的值来源*值提供程序*(如`QueryString`)，并为参数属性，指示要使用的值提供程序*值提供程序属性*(如`id`)。 ASP.NET 包括值提供程序和所有典型 Web 窗体应用程序用户输入源的属性。 其中包括查询字符串、 cookie、 窗体值、 控件、 视图状态，会话状态和配置文件属性。 此外可以编写自定义值提供程序。

### <a name="run-the-application"></a>运行此应用程序

运行应用程序现在可以查看所有产品或类别的产品。

1. 在 Visual Studio 中，按**F5**运行该应用程序。
 在浏览器将打开并显示*Default.aspx*页。

2. 从产品类别菜单中，选择**汽车**。

   *ProductList.aspx*页出现后，显示仅从产品**汽车**类别。 稍后在本教程中，您显示产品详细信息。

    ![显示数据的项和详细信息-汽车](display_data_items_and_details/_static/image2.png)

3. 选择**产品**顶部菜单中。
 *ProductList.aspx*页现在将显示所有产品。 

    ![显示数据的项和详细信息-产品](display_data_items_and_details/_static/image3.png)

4. 关闭浏览器并返回到 Visual Studio。

### <a name="add-a-data-control-to-display-product-details"></a>添加一个数据控件来显示产品详细信息

修改*ProductDetails.aspx*标记添加在上一教程，以显示特定产品信息：

1. 在中**解决方案资源管理器**，打开*ProductDetails.aspx*。

2. 现有标记替换此标记：

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

此标记使用**FormView**控件来显示特定产品的详细信息。 它使用方法，如那些用来显示中的数据*ProductList.aspx*。 **FormView**控件用于从数据源一次显示一条记录。 当你使用**FormView**控件，您创建模板来显示和编辑数据绑定值。 这些模板包含控件，绑定表达式，并在窗体的外观和功能的格式设置定义。

连接到数据库的以前的标记需要额外的代码。

1. 在中**解决方案资源管理器**，右键单击*ProductDetails.aspx* ，然后选择**查看代码**。  
   *ProductDetails.aspx.cs*显示文件。

2. 与此替换现有代码：   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

此代码检查"`productID`"查询字符串值。 如果找到有效的值，则将显示匹配的产品。 如果找不到查询字符串，或它的值无效，将显示没有任何产品。

### <a name="run-the-application"></a>运行此应用程序

现在，可以运行应用程序以查看特定产品详细信息，根据产品 id。

1. 在 Visual Studio 中，按**F5**运行该应用程序。  
 浏览器打开到*Default.aspx*。

2. 从类别菜单中，选择**船**。  
 *ProductList.aspx*显示页。

3. 选择**纸张船**。  
 *ProductDetails.aspx*显示页。   

    ![显示数据的项和详细信息-产品](display_data_items_and_details/_static/image4.png)

在下一步的教程中，您将购物车添加到 Wingtip Toys 应用程序。

## <a name="additional-resources"></a>其他资源

[检索和使用模型绑定和 web 窗体显示数据](../../presenting-and-managing-data/model-binding/retrieving-data.md)

> [!div class="step-by-step"]
> [上一页](ui_and_navigation.md)
> [下一页](shopping-cart.md)
