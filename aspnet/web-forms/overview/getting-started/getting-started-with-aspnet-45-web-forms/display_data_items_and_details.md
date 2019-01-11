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
<a name="display-data-items-and-details"></a><span data-ttu-id="f91f9-103">显示数据项和详细信息</span><span class="sxs-lookup"><span data-stu-id="f91f9-103">Display data items and details</span></span>
====================
<span data-ttu-id="f91f9-104">通过[Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="f91f9-104">by [Erik Reitan](https://github.com/Erikre)</span></span>

> <span data-ttu-id="f91f9-105">本系列教程介绍了为 Web 构建具有 ASP.NET 4.7 和 Microsoft Visual Studio Community 2017 的 ASP.NET Web 窗体应用程序的基础知识。</span><span class="sxs-lookup"><span data-stu-id="f91f9-105">This tutorial series teaches you the basics of building an ASP.NET Web Forms application with ASP.NET 4.7 and Microsoft Visual Studio Community 2017 for the Web.</span></span>

<span data-ttu-id="f91f9-106">在本教程中，您将学习如何显示数据项和使用 ASP.NET Web 窗体和 Entity Framework Code First 数据项详细信息。</span><span class="sxs-lookup"><span data-stu-id="f91f9-106">In this tutorial, you learn how to display data items and data item details with ASP.NET Web Forms and Entity Framework Code First.</span></span> <span data-ttu-id="f91f9-107">本教程基于前面的"用户界面和导航"教程 Wingtip Toy 存储教程系列的一部分。</span><span class="sxs-lookup"><span data-stu-id="f91f9-107">This tutorial builds on the previous "UI and Navigation" tutorial as part of the Wingtip Toy Store tutorial series.</span></span> <span data-ttu-id="f91f9-108">在已完成的教程中上的产品*ProductsList.aspx*页和产品的详细信息*ProductDetails.aspx*页会显示。</span><span class="sxs-lookup"><span data-stu-id="f91f9-108">In the completed tutorial, products on the *ProductsList.aspx* page and a product's details on the *ProductDetails.aspx* page are displayed.</span></span>

## <a name="what-you-learn"></a><span data-ttu-id="f91f9-109">学习内容</span><span class="sxs-lookup"><span data-stu-id="f91f9-109">What you learn</span></span>

- <span data-ttu-id="f91f9-110">添加一个数据控件用于显示数据库产品。</span><span class="sxs-lookup"><span data-stu-id="f91f9-110">Add a data control to display database products.</span></span>
- <span data-ttu-id="f91f9-111">连接到所选数据的数据控件。</span><span class="sxs-lookup"><span data-stu-id="f91f9-111">Connect a data control to selected data.</span></span>
- <span data-ttu-id="f91f9-112">添加一个数据控件来显示产品详细信息。</span><span class="sxs-lookup"><span data-stu-id="f91f9-112">Add a data control to display product details.</span></span>
- <span data-ttu-id="f91f9-113">分析查询字符串值，并使用它来检索到的数据库数据进行筛选。</span><span class="sxs-lookup"><span data-stu-id="f91f9-113">Parse a query string value and use it to filter retrieved database data.</span></span>

<span data-ttu-id="f91f9-114">本教程中介绍的功能包括模型绑定和值提供程序。</span><span class="sxs-lookup"><span data-stu-id="f91f9-114">Features introduced in this tutorial include model binding and value providers.</span></span>

## <a name="add-a-data-control-to-display-products"></a><span data-ttu-id="f91f9-115">添加用来显示产品的数据控件</span><span class="sxs-lookup"><span data-stu-id="f91f9-115">Add a data control to display products</span></span>
 
<span data-ttu-id="f91f9-116">有几种方法可以将数据绑定到服务器控件。</span><span class="sxs-lookup"><span data-stu-id="f91f9-116">You have a few options to bind data to a server control.</span></span> <span data-ttu-id="f91f9-117">最常见的包括：</span><span class="sxs-lookup"><span data-stu-id="f91f9-117">The most common include:</span></span>

 * <span data-ttu-id="f91f9-118">添加数据源控件</span><span class="sxs-lookup"><span data-stu-id="f91f9-118">Adding a data source control</span></span>
 * <span data-ttu-id="f91f9-119">手动添加代码</span><span class="sxs-lookup"><span data-stu-id="f91f9-119">Adding code by hand</span></span>
 * <span data-ttu-id="f91f9-120">实现模型绑定</span><span class="sxs-lookup"><span data-stu-id="f91f9-120">Implementing model binding</span></span>

### <a name="use-a-data-source-control-to-bind-data"></a><span data-ttu-id="f91f9-121">使用数据源控件将数据绑定</span><span class="sxs-lookup"><span data-stu-id="f91f9-121">Use a data source control to bind data</span></span>

<span data-ttu-id="f91f9-122">添加数据源控件链接到显示的数据的控件的数据源控件。</span><span class="sxs-lookup"><span data-stu-id="f91f9-122">Adding a data source control links the data source control to the control that displays the data.</span></span> <span data-ttu-id="f91f9-123">使用此方法时，你可以以声明方式，而不是以编程方式连接到数据源的服务器端控件。</span><span class="sxs-lookup"><span data-stu-id="f91f9-123">With this approach, you can declaratively, rather than programmatically, connect server-side controls to data sources.</span></span>

### <a name="code-by-hand-to-bind-data"></a><span data-ttu-id="f91f9-124">手动将数据绑定的代码</span><span class="sxs-lookup"><span data-stu-id="f91f9-124">Code by hand to bind data</span></span>

<span data-ttu-id="f91f9-125">手动编写代码的过程包括：</span><span class="sxs-lookup"><span data-stu-id="f91f9-125">Coding by hand involves:</span></span>

1. <span data-ttu-id="f91f9-126">读取的值</span><span class="sxs-lookup"><span data-stu-id="f91f9-126">Reading a value</span></span>
2. <span data-ttu-id="f91f9-127">检查是否为 null</span><span class="sxs-lookup"><span data-stu-id="f91f9-127">Checking if it's null</span></span>
3. <span data-ttu-id="f91f9-128">将其转换为适当的类型</span><span class="sxs-lookup"><span data-stu-id="f91f9-128">Converting it to an appropriate type</span></span>
4. <span data-ttu-id="f91f9-129">检查转换成功</span><span class="sxs-lookup"><span data-stu-id="f91f9-129">Checking conversion success</span></span>
5. <span data-ttu-id="f91f9-130">使具有转换后的值的查询</span><span class="sxs-lookup"><span data-stu-id="f91f9-130">Making a query with the converted value</span></span> 

<span data-ttu-id="f91f9-131">使用此方法时，可以完全控制数据访问逻辑。</span><span class="sxs-lookup"><span data-stu-id="f91f9-131">With this approach, you have full control over your data-access logic.</span></span>

### <a name="use-model-binding-to-bind-data"></a><span data-ttu-id="f91f9-132">使用模型绑定将数据绑定</span><span class="sxs-lookup"><span data-stu-id="f91f9-132">Use model binding to bind data</span></span>

<span data-ttu-id="f91f9-133">使用模型绑定，将绑定包含很少的代码的结果，并且它使你能够重复使用在整个应用程序的功能。</span><span class="sxs-lookup"><span data-stu-id="f91f9-133">With model binding, you bind results with far less code and it gives you the ability to reuse the functionality throughout your application.</span></span> <span data-ttu-id="f91f9-134">它同时仍提供一个丰富的数据绑定框架简化了使用代码为中心的数据访问逻辑。</span><span class="sxs-lookup"><span data-stu-id="f91f9-134">It simplifies working with code-focused data-access logic while still providing a rich, data-binding framework.</span></span>

## <a name="display-products"></a><span data-ttu-id="f91f9-135">显示产品</span><span class="sxs-lookup"><span data-stu-id="f91f9-135">Display products</span></span>

<span data-ttu-id="f91f9-136">在本教程中，使用模型绑定将数据绑定。</span><span class="sxs-lookup"><span data-stu-id="f91f9-136">In this tutorial, you use model binding to bind data.</span></span> <span data-ttu-id="f91f9-137">若要配置以使用模型绑定选择数据的数据控件，设置控件的`SelectMethod`属性页面的代码中的方法。</span><span class="sxs-lookup"><span data-stu-id="f91f9-137">To configure a data control to use model binding to select data, you set the control's `SelectMethod` property to a method in the page's code.</span></span> <span data-ttu-id="f91f9-138">数据控件在页生命周期中的适当时间调用的方法，并自动将绑定返回的数据。</span><span class="sxs-lookup"><span data-stu-id="f91f9-138">The data control calls the method at the appropriate time in the page life cycle and automatically binds the returned data.</span></span> <span data-ttu-id="f91f9-139">无需显式调用`DataBind`方法。</span><span class="sxs-lookup"><span data-stu-id="f91f9-139">There's no need to explicitly call the `DataBind` method.</span></span>

<span data-ttu-id="f91f9-140">使用以下步骤，修改*ProductList.aspx*显示产品的标记。</span><span class="sxs-lookup"><span data-stu-id="f91f9-140">Working through the following steps, you modify *ProductList.aspx* markup to display products.</span></span>

1. <span data-ttu-id="f91f9-141">在中**解决方案资源管理器**，打开*ProductList.aspx*。</span><span class="sxs-lookup"><span data-stu-id="f91f9-141">In **Solution Explorer**, open *ProductList.aspx*.</span></span>

2. <span data-ttu-id="f91f9-142">现有标记替换为以下标记：</span><span class="sxs-lookup"><span data-stu-id="f91f9-142">Replace the existing markup with the following markup:</span></span> 

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

<span data-ttu-id="f91f9-143">上述标记使用**ListView**控件命名为`productList`用于显示产品。</span><span class="sxs-lookup"><span data-stu-id="f91f9-143">The preceding markup uses a **ListView** control named `productList` to display products.</span></span>

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

<span data-ttu-id="f91f9-144">使用模板和样式，可以定义如何**ListView**控件显示数据。</span><span class="sxs-lookup"><span data-stu-id="f91f9-144">With templates and styles, you define how the **ListView** control displays data.</span></span> <span data-ttu-id="f91f9-145">它可用于任何重复结构中的数据。</span><span class="sxs-lookup"><span data-stu-id="f91f9-145">It's useful for data in any repeating structure.</span></span> <span data-ttu-id="f91f9-146">尽管这**ListView**示例仅显示数据库数据，你还可以无需代码，使用户可以编辑、 插入和删除数据，并可进行排序和页数据。</span><span class="sxs-lookup"><span data-stu-id="f91f9-146">Though this **ListView** example simply displays database data, you can also, without code, enable users to edit, insert, and delete data, and to sort and page data.</span></span>

<span data-ttu-id="f91f9-147">当您将设置`ItemType`中的属性**ListView**控件，数据绑定表达式`Item`可用和控件将成为强类型化。</span><span class="sxs-lookup"><span data-stu-id="f91f9-147">When you set the `ItemType` property in the **ListView** control, the data-binding expression `Item` is available and the control becomes strongly typed.</span></span> <span data-ttu-id="f91f9-148">例如，指定上一教程中所述，可以选择项对象的详细信息，IntelliSense、 `ProductName`:</span><span class="sxs-lookup"><span data-stu-id="f91f9-148">As mentioned in the previous tutorial, you can select Item object details with IntelliSense, such as specifying the `ProductName`:</span></span>

![显示数据的项和详细信息-IntelliSense](display_data_items_and_details/_static/image1.png)

<span data-ttu-id="f91f9-150">使用模型绑定，指定`SelectMethod`值 (`GetProducts`)。</span><span class="sxs-lookup"><span data-stu-id="f91f9-150">With model binding, you're specifying a `SelectMethod` value (`GetProducts`).</span></span> <span data-ttu-id="f91f9-151">这是将添加到代码的方法滞后，若要在下一步中显示的产品。</span><span class="sxs-lookup"><span data-stu-id="f91f9-151">This is the method you add to the code behind to display products in the next step.</span></span>

### <a name="add-code-to-display-products"></a><span data-ttu-id="f91f9-152">添加代码以显示产品</span><span class="sxs-lookup"><span data-stu-id="f91f9-152">Add code to display products</span></span>

<span data-ttu-id="f91f9-153">在此步骤中，添加代码以填充**ListView**数据库产品数据的控件。</span><span class="sxs-lookup"><span data-stu-id="f91f9-153">In this step, you add code to populate the **ListView** control with database product data.</span></span> <span data-ttu-id="f91f9-154">该代码支持显示所有产品和各个类别的产品。</span><span class="sxs-lookup"><span data-stu-id="f91f9-154">The code supports showing all products and individual category products.</span></span>

1. <span data-ttu-id="f91f9-155">在中**解决方案资源管理器**，右键单击*ProductList.aspx* ，然后选择**查看代码**。</span><span class="sxs-lookup"><span data-stu-id="f91f9-155">In **Solution Explorer**, right-click *ProductList.aspx* and then select **View Code**.</span></span>
2. <span data-ttu-id="f91f9-156">中的现有代码替换*ProductList.aspx.cs*与此文件：</span><span class="sxs-lookup"><span data-stu-id="f91f9-156">Replace the existing code in the *ProductList.aspx.cs* file with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

<span data-ttu-id="f91f9-157">此代码演示`GetProducts`方法的**ListView**控件的`ItemType`属性中引用*ProductList.aspx*。</span><span class="sxs-lookup"><span data-stu-id="f91f9-157">This code shows the `GetProducts` method that the **ListView** control's `ItemType` property references in *ProductList.aspx*.</span></span> <span data-ttu-id="f91f9-158">若要将结果限制为特定数据库类别，该代码将设置`categoryId`值从查询字符串传递给*ProductList.aspx*。</span><span class="sxs-lookup"><span data-stu-id="f91f9-158">To limit the results to a specific database category, the code sets the `categoryId` value from the query string passed to *ProductList.aspx*.</span></span> <span data-ttu-id="f91f9-159">`QueryStringAttribute`类中`System.Web.ModelBinding`命名空间用于检索查询字符串变量`id`的值。</span><span class="sxs-lookup"><span data-stu-id="f91f9-159">The `QueryStringAttribute` class in the `System.Web.ModelBinding` namespace is used to retrieve the query string variable `id`'s value.</span></span> <span data-ttu-id="f91f9-160">这会指示模型绑定，在运行时，绑定到的查询字符串值`categoryId`参数。</span><span class="sxs-lookup"><span data-stu-id="f91f9-160">This instructs model binding to, at run time, bind a query string value to the `categoryId` parameter.</span></span>

<span data-ttu-id="f91f9-161">时有效的类别 (`categoryId`) 是传递，结果会限制为该类别的数据库产品。</span><span class="sxs-lookup"><span data-stu-id="f91f9-161">When a valid category (`categoryId`) is passed, the results are limited to that category's database products.</span></span> <span data-ttu-id="f91f9-162">例如，如果*ProductsList.aspx*页 URL 是：</span><span class="sxs-lookup"><span data-stu-id="f91f9-162">For instance, if the *ProductsList.aspx* page URL is this:</span></span>

[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

<span data-ttu-id="f91f9-163">页面仅显示产品其中`categoryId`等于`1`。</span><span class="sxs-lookup"><span data-stu-id="f91f9-163">The page displays only the products where the `categoryId` equals `1`.</span></span>

<span data-ttu-id="f91f9-164">如果没有查询字符串传递，则显示所有产品。</span><span class="sxs-lookup"><span data-stu-id="f91f9-164">All products are displayed if no query string is passed.</span></span>

<span data-ttu-id="f91f9-165">调用这些方法的值来源*值提供程序*(如`QueryString`)，并为参数属性，指示要使用的值提供程序*值提供程序属性*(如`id`)。</span><span class="sxs-lookup"><span data-stu-id="f91f9-165">The value sources for these methods are called *value providers* (such as `QueryString`), and the parameter attributes that indicate which value provider to use are called *value provider attributes* (such as `id`).</span></span> <span data-ttu-id="f91f9-166">ASP.NET 包括值提供程序和所有典型 Web 窗体应用程序用户输入源的属性。</span><span class="sxs-lookup"><span data-stu-id="f91f9-166">ASP.NET includes value providers and attributes for all typical Web Forms application user input sources.</span></span> <span data-ttu-id="f91f9-167">其中包括查询字符串、 cookie、 窗体值、 控件、 视图状态，会话状态和配置文件属性。</span><span class="sxs-lookup"><span data-stu-id="f91f9-167">These include the query string, cookies, form values, controls, view state, session state, and profile properties.</span></span> <span data-ttu-id="f91f9-168">此外可以编写自定义值提供程序。</span><span class="sxs-lookup"><span data-stu-id="f91f9-168">You can also write custom value providers.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="f91f9-169">运行此应用程序</span><span class="sxs-lookup"><span data-stu-id="f91f9-169">Run the application</span></span>

<span data-ttu-id="f91f9-170">运行应用程序现在可以查看所有产品或类别的产品。</span><span class="sxs-lookup"><span data-stu-id="f91f9-170">Run the application now to view all products or a category's products.</span></span>

1. <span data-ttu-id="f91f9-171">在 Visual Studio 中，按**F5**运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="f91f9-171">In Visual Studio, press **F5** to run the application.</span></span>
 <span data-ttu-id="f91f9-172">在浏览器将打开并显示*Default.aspx*页。</span><span class="sxs-lookup"><span data-stu-id="f91f9-172">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="f91f9-173">从产品类别菜单中，选择**汽车**。</span><span class="sxs-lookup"><span data-stu-id="f91f9-173">From the product category menu, select **Cars**.</span></span>

   <span data-ttu-id="f91f9-174">*ProductList.aspx*页出现后，显示仅从产品**汽车**类别。</span><span class="sxs-lookup"><span data-stu-id="f91f9-174">The *ProductList.aspx* page appears, showing only products from the **Cars** category.</span></span> <span data-ttu-id="f91f9-175">稍后在本教程中，您显示产品详细信息。</span><span class="sxs-lookup"><span data-stu-id="f91f9-175">Later in this tutorial, you display product details.</span></span>

    ![显示数据的项和详细信息-汽车](display_data_items_and_details/_static/image2.png)

3. <span data-ttu-id="f91f9-177">选择**产品**顶部菜单中。</span><span class="sxs-lookup"><span data-stu-id="f91f9-177">Select **Products** from the top menu.</span></span>
 <span data-ttu-id="f91f9-178">*ProductList.aspx*页现在将显示所有产品。</span><span class="sxs-lookup"><span data-stu-id="f91f9-178">The *ProductList.aspx* page now displays all products.</span></span> 

    ![显示数据的项和详细信息-产品](display_data_items_and_details/_static/image3.png)

4. <span data-ttu-id="f91f9-180">关闭浏览器并返回到 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="f91f9-180">Close the browser and return to Visual Studio.</span></span>

### <a name="add-a-data-control-to-display-product-details"></a><span data-ttu-id="f91f9-181">添加一个数据控件来显示产品详细信息</span><span class="sxs-lookup"><span data-stu-id="f91f9-181">Add a Data Control to display product details</span></span>

<span data-ttu-id="f91f9-182">修改*ProductDetails.aspx*标记添加在上一教程，以显示特定产品信息：</span><span class="sxs-lookup"><span data-stu-id="f91f9-182">Modify the *ProductDetails.aspx* markup that you added in the previous tutorial to display specific product information:</span></span>

1. <span data-ttu-id="f91f9-183">在中**解决方案资源管理器**，打开*ProductDetails.aspx*。</span><span class="sxs-lookup"><span data-stu-id="f91f9-183">In **Solution Explorer**, open *ProductDetails.aspx*.</span></span>

2. <span data-ttu-id="f91f9-184">现有标记替换此标记：</span><span class="sxs-lookup"><span data-stu-id="f91f9-184">Replace the existing markup with this markup:</span></span>

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

<span data-ttu-id="f91f9-185">此标记使用**FormView**控件来显示特定产品的详细信息。</span><span class="sxs-lookup"><span data-stu-id="f91f9-185">This markup uses a **FormView** control to display specific product details.</span></span> <span data-ttu-id="f91f9-186">它使用方法，如那些用来显示中的数据*ProductList.aspx*。</span><span class="sxs-lookup"><span data-stu-id="f91f9-186">It uses methods like those used to display data in *ProductList.aspx*.</span></span> <span data-ttu-id="f91f9-187">**FormView**控件用于从数据源一次显示一条记录。</span><span class="sxs-lookup"><span data-stu-id="f91f9-187">The **FormView** control is used to display a single record at a time from a data source.</span></span> <span data-ttu-id="f91f9-188">当你使用**FormView**控件，您创建模板来显示和编辑数据绑定值。</span><span class="sxs-lookup"><span data-stu-id="f91f9-188">When you use the **FormView** control, you create templates to display and edit data-bound values.</span></span> <span data-ttu-id="f91f9-189">这些模板包含控件，绑定表达式，并在窗体的外观和功能的格式设置定义。</span><span class="sxs-lookup"><span data-stu-id="f91f9-189">These templates contain controls, binding expressions, and formatting that define the form's look and functionality.</span></span>

<span data-ttu-id="f91f9-190">连接到数据库的以前的标记需要额外的代码。</span><span class="sxs-lookup"><span data-stu-id="f91f9-190">Connecting the previous markup to the database requires additional code.</span></span>

1. <span data-ttu-id="f91f9-191">在中**解决方案资源管理器**，右键单击*ProductDetails.aspx* ，然后选择**查看代码**。</span><span class="sxs-lookup"><span data-stu-id="f91f9-191">In **Solution Explorer**, right-click *ProductDetails.aspx* and then select **View Code**.</span></span>  
   <span data-ttu-id="f91f9-192">*ProductDetails.aspx.cs*显示文件。</span><span class="sxs-lookup"><span data-stu-id="f91f9-192">The *ProductDetails.aspx.cs* file is displayed.</span></span>

2. <span data-ttu-id="f91f9-193">与此替换现有代码：</span><span class="sxs-lookup"><span data-stu-id="f91f9-193">Replace the existing code with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

<span data-ttu-id="f91f9-194">此代码检查"`productID`"查询字符串值。</span><span class="sxs-lookup"><span data-stu-id="f91f9-194">This code checks for a "`productID`" query string value.</span></span> <span data-ttu-id="f91f9-195">如果找到有效的值，则将显示匹配的产品。</span><span class="sxs-lookup"><span data-stu-id="f91f9-195">If a valid value is found, the matching product is displayed.</span></span> <span data-ttu-id="f91f9-196">如果找不到查询字符串，或它的值无效，将显示没有任何产品。</span><span class="sxs-lookup"><span data-stu-id="f91f9-196">If the query string isn't found, or its value isn't valid, no product is displayed.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="f91f9-197">运行此应用程序</span><span class="sxs-lookup"><span data-stu-id="f91f9-197">Run the application</span></span>

<span data-ttu-id="f91f9-198">现在，可以运行应用程序以查看特定产品详细信息，根据产品 id。</span><span class="sxs-lookup"><span data-stu-id="f91f9-198">Now you can run the application to see specific product details based on product ID.</span></span>

1. <span data-ttu-id="f91f9-199">在 Visual Studio 中，按**F5**运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="f91f9-199">In Visual Studio, press **F5** to run the application.</span></span>  
 <span data-ttu-id="f91f9-200">浏览器打开到*Default.aspx*。</span><span class="sxs-lookup"><span data-stu-id="f91f9-200">The browser opens to *Default.aspx*.</span></span>

2. <span data-ttu-id="f91f9-201">从类别菜单中，选择**船**。</span><span class="sxs-lookup"><span data-stu-id="f91f9-201">From the category menu, select **Boats**.</span></span>  
 <span data-ttu-id="f91f9-202">*ProductList.aspx*显示页。</span><span class="sxs-lookup"><span data-stu-id="f91f9-202">The *ProductList.aspx* page is displayed.</span></span>

3. <span data-ttu-id="f91f9-203">选择**纸张船**。</span><span class="sxs-lookup"><span data-stu-id="f91f9-203">Select **Paper Boat**.</span></span>  
 <span data-ttu-id="f91f9-204">*ProductDetails.aspx*显示页。</span><span class="sxs-lookup"><span data-stu-id="f91f9-204">The *ProductDetails.aspx* page is displayed.</span></span>   

    ![显示数据的项和详细信息-产品](display_data_items_and_details/_static/image4.png)

<span data-ttu-id="f91f9-206">在下一步的教程中，您将购物车添加到 Wingtip Toys 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f91f9-206">In the next tutorial, you add a shopping cart to the Wingtip Toys application.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f91f9-207">其他资源</span><span class="sxs-lookup"><span data-stu-id="f91f9-207">Additional resources</span></span>

[<span data-ttu-id="f91f9-208">检索和使用模型绑定和 web 窗体显示数据</span><span class="sxs-lookup"><span data-stu-id="f91f9-208">Retrieving and displaying data with model binding and web forms</span></span>](../../presenting-and-managing-data/model-binding/retrieving-data.md)

> [!div class="step-by-step"]
> <span data-ttu-id="f91f9-209">[上一页](ui_and_navigation.md)
> [下一页](shopping-cart.md)</span><span class="sxs-lookup"><span data-stu-id="f91f9-209">[Previous](ui_and_navigation.md)
[Next](shopping-cart.md)</span></span>
