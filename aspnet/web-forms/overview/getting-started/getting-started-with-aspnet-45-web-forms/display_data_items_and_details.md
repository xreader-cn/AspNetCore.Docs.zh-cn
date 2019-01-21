---
uid: web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
title: 显示数据项和详细信息 |Microsoft Docs
author: Erikre
description: 本教程系列将演示构建具有 ASP.NET 4.7 和 Microsoft Visual Studio 2017 的 ASP.NET Web 窗体应用程序的基础知识
ms.author: riande
ms.date: 1/04/2019
ms.assetid: 64a491a8-0ed6-4c2f-9c1c-412962eb6006
msc.legacyurl: /web-forms/overview/getting-started/getting-started-with-aspnet-45-web-forms/display_data_items_and_details
msc.type: authoredcontent
ms.openlocfilehash: acc2f8e78375ef0455d467e2af750ecbee623224
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396215"
---
<a name="display-data-items-and-details"></a><span data-ttu-id="9f174-103">显示数据项和详细信息</span><span class="sxs-lookup"><span data-stu-id="9f174-103">Display data items and details</span></span>
====================
<span data-ttu-id="9f174-104">通过[Erik Reitan](https://github.com/Erikre)</span><span class="sxs-lookup"><span data-stu-id="9f174-104">by [Erik Reitan](https://github.com/Erikre)</span></span>

> <span data-ttu-id="9f174-105">本系列教程介绍了构建具有 ASP.NET 4.7 和 Microsoft Visual Studio 2017 的 ASP.NET Web 窗体应用程序的基础知识。</span><span class="sxs-lookup"><span data-stu-id="9f174-105">This tutorial series teaches you the basics of building an ASP.NET Web Forms application with ASP.NET 4.7 and Microsoft Visual Studio 2017.</span></span>

<span data-ttu-id="9f174-106">在本教程中，您将学习如何显示数据项和使用 ASP.NET Web 窗体和 Entity Framework Code First 数据项详细信息。</span><span class="sxs-lookup"><span data-stu-id="9f174-106">In this tutorial, you'll learn how to display data items and data item details with ASP.NET Web Forms and Entity Framework Code First.</span></span> <span data-ttu-id="9f174-107">本教程基于前面的"用户界面和导航"教程 Wingtip Toy 存储教程系列的一部分。</span><span class="sxs-lookup"><span data-stu-id="9f174-107">This tutorial builds on the previous "UI and Navigation" tutorial as part of the Wingtip Toy Store tutorial series.</span></span> <span data-ttu-id="9f174-108">完成本教程后，您将看到产品*ProductsList.aspx*页和产品的详细信息*ProductDetails.aspx*页。</span><span class="sxs-lookup"><span data-stu-id="9f174-108">After completing this tutorial, you'll see products on the *ProductsList.aspx* page and a product's details on the *ProductDetails.aspx* page.</span></span>

## <a name="youll-learn-how-to"></a><span data-ttu-id="9f174-109">你将了解如何：</span><span class="sxs-lookup"><span data-stu-id="9f174-109">You'll learn how to:</span></span>

- <span data-ttu-id="9f174-110">添加数据控件用于显示从数据库产品</span><span class="sxs-lookup"><span data-stu-id="9f174-110">Add a data control to display products from the database</span></span>
- <span data-ttu-id="9f174-111">连接到所选数据的数据控件</span><span class="sxs-lookup"><span data-stu-id="9f174-111">Connect a data control to the selected data</span></span>
- <span data-ttu-id="9f174-112">添加用来显示产品详细信息，从数据库的数据控件</span><span class="sxs-lookup"><span data-stu-id="9f174-112">Add a data control to display product details from the database</span></span>
- <span data-ttu-id="9f174-113">从查询字符串中检索值并使用该值来限制从数据库中检索的数据</span><span class="sxs-lookup"><span data-stu-id="9f174-113">Retrieve a value from the query string and use that value to limit the data that's retrieved from the database</span></span>

### <a name="features-introduced-in-this-tutorial"></a><span data-ttu-id="9f174-114">本教程中介绍的功能：</span><span class="sxs-lookup"><span data-stu-id="9f174-114">Features introduced in this tutorial:</span></span>

- <span data-ttu-id="9f174-115">模型绑定</span><span class="sxs-lookup"><span data-stu-id="9f174-115">Model binding</span></span>
- <span data-ttu-id="9f174-116">值提供程序</span><span class="sxs-lookup"><span data-stu-id="9f174-116">Value providers</span></span>

## <a name="add-a-data-control"></a><span data-ttu-id="9f174-117">添加数据控件</span><span class="sxs-lookup"><span data-stu-id="9f174-117">Add a data control</span></span>

<span data-ttu-id="9f174-118">几个不同的选项可用于将数据绑定到服务器控件。</span><span class="sxs-lookup"><span data-stu-id="9f174-118">You can use a few different options to bind data to a server control.</span></span> <span data-ttu-id="9f174-119">最常见的包括：</span><span class="sxs-lookup"><span data-stu-id="9f174-119">The most common include:</span></span>

 * <span data-ttu-id="9f174-120">添加数据源控件</span><span class="sxs-lookup"><span data-stu-id="9f174-120">Adding a data source control</span></span>
 * <span data-ttu-id="9f174-121">手动添加代码</span><span class="sxs-lookup"><span data-stu-id="9f174-121">Adding code by hand</span></span>
 * <span data-ttu-id="9f174-122">使用模型绑定</span><span class="sxs-lookup"><span data-stu-id="9f174-122">Using model binding</span></span>

### <a name="use-a-data-source-control-to-bind-data"></a><span data-ttu-id="9f174-123">使用数据源控件将数据绑定</span><span class="sxs-lookup"><span data-stu-id="9f174-123">Use a data source control to bind data</span></span>

<span data-ttu-id="9f174-124">添加数据源控件，可将数据源控件链接到显示的数据的控件。</span><span class="sxs-lookup"><span data-stu-id="9f174-124">Adding a data source control allows you to link the data source control to the control that displays the data.</span></span> <span data-ttu-id="9f174-125">使用此方法时，你可以以声明方式，而不是以编程方式连接到数据源的服务器端控件。</span><span class="sxs-lookup"><span data-stu-id="9f174-125">With this approach, you can declaratively,  rather than programmatically, connect server-side controls to data sources.</span></span>

### <a name="code-by-hand-to-bind-data"></a><span data-ttu-id="9f174-126">手动将数据绑定的代码</span><span class="sxs-lookup"><span data-stu-id="9f174-126">Code by hand to bind data</span></span>

<span data-ttu-id="9f174-127">手动编写代码的过程包括：</span><span class="sxs-lookup"><span data-stu-id="9f174-127">Coding by hand involves:</span></span>

1. <span data-ttu-id="9f174-128">读取的值</span><span class="sxs-lookup"><span data-stu-id="9f174-128">Reading a value</span></span>
2. <span data-ttu-id="9f174-129">检查是否为 null</span><span class="sxs-lookup"><span data-stu-id="9f174-129">Checking if it's null</span></span>
3. <span data-ttu-id="9f174-130">将其转换为适当的类型</span><span class="sxs-lookup"><span data-stu-id="9f174-130">Converting it to an appropriate type</span></span>
4. <span data-ttu-id="9f174-131">检查转换成功</span><span class="sxs-lookup"><span data-stu-id="9f174-131">Checking conversion success</span></span>
5. <span data-ttu-id="9f174-132">在查询中使用的值</span><span class="sxs-lookup"><span data-stu-id="9f174-132">Using the value in the query</span></span> 

<span data-ttu-id="9f174-133">这种方法可让你可以完全控制数据访问逻辑。</span><span class="sxs-lookup"><span data-stu-id="9f174-133">This approach lets you have full control over your data-access logic.</span></span>

### <a name="use-model-binding-to-bind-data"></a><span data-ttu-id="9f174-134">使用模型绑定将数据绑定</span><span class="sxs-lookup"><span data-stu-id="9f174-134">Use model binding to bind data</span></span>

<span data-ttu-id="9f174-135">模型绑定允许您将绑定包含很少的代码的结果，并使你能够重复使用在整个应用程序的功能。</span><span class="sxs-lookup"><span data-stu-id="9f174-135">Model binding lets you bind results with far less code and gives you the ability to reuse the functionality throughout your application.</span></span> <span data-ttu-id="9f174-136">它同时仍提供一个丰富的数据绑定框架简化了使用代码为中心的数据访问逻辑。</span><span class="sxs-lookup"><span data-stu-id="9f174-136">It simplifies working with code-focused data-access logic while still providing a rich, data-binding framework.</span></span>

## <a name="display-products"></a><span data-ttu-id="9f174-137">显示产品</span><span class="sxs-lookup"><span data-stu-id="9f174-137">Display products</span></span>

<span data-ttu-id="9f174-138">在本教程中，您将使用模型绑定将数据绑定。</span><span class="sxs-lookup"><span data-stu-id="9f174-138">In this tutorial, you'll use model binding to bind data.</span></span> <span data-ttu-id="9f174-139">若要配置以使用模型绑定选择数据的数据控件，设置控件的`SelectMethod`中页面的代码的方法名称的属性。</span><span class="sxs-lookup"><span data-stu-id="9f174-139">To configure a data control to use model binding to select data, you set the control's `SelectMethod` property to a method name in the page's code.</span></span> <span data-ttu-id="9f174-140">数据控件在页生命周期中的适当时间调用的方法，并自动将绑定返回的数据。</span><span class="sxs-lookup"><span data-stu-id="9f174-140">The data control calls the method at the appropriate time in the page life cycle and automatically binds the returned data.</span></span> <span data-ttu-id="9f174-141">无需显式调用`DataBind`方法。</span><span class="sxs-lookup"><span data-stu-id="9f174-141">There's no need to explicitly call the `DataBind` method.</span></span>

1. <span data-ttu-id="9f174-142">在中**解决方案资源管理器**，打开*ProductList.aspx*。</span><span class="sxs-lookup"><span data-stu-id="9f174-142">In **Solution Explorer**, open *ProductList.aspx*.</span></span>
2. <span data-ttu-id="9f174-143">现有标记替换此标记：</span><span class="sxs-lookup"><span data-stu-id="9f174-143">Replace the existing markup with this markup:</span></span>   

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample1.aspx)]

<span data-ttu-id="9f174-144">此代码使用**ListView**控件命名为`productList`用于显示产品。</span><span class="sxs-lookup"><span data-stu-id="9f174-144">This code uses a **ListView** control named `productList` to display products.</span></span>

[!code-aspx-csharp[Main](display_data_items_and_details/samples/sample2.aspx)]

<span data-ttu-id="9f174-145">使用模板和样式，可以定义如何**ListView**控件显示数据。</span><span class="sxs-lookup"><span data-stu-id="9f174-145">With templates and styles, you define how the **ListView** control displays data.</span></span> <span data-ttu-id="9f174-146">它可用于任何重复结构中的数据。</span><span class="sxs-lookup"><span data-stu-id="9f174-146">It's useful for data in any repeating structure.</span></span> <span data-ttu-id="9f174-147">尽管这**ListView**示例仅显示数据库数据，你还可以无需代码，使用户可以编辑、 插入和删除数据，并可进行排序和页数据。</span><span class="sxs-lookup"><span data-stu-id="9f174-147">Though this **ListView** example simply displays database data, you can also, without code, enable users to edit, insert, and delete data, and to sort and page data.</span></span>

<span data-ttu-id="9f174-148">通过设置`ItemType`中的属性**ListView**控件，数据绑定表达式`Item`可用和控件将成为强类型化。</span><span class="sxs-lookup"><span data-stu-id="9f174-148">By setting the `ItemType` property in the **ListView** control, the data-binding expression `Item` is available and the control becomes strongly typed.</span></span> <span data-ttu-id="9f174-149">例如，指定上一教程中所述，可以选择项对象的详细信息，IntelliSense、 `ProductName`:</span><span class="sxs-lookup"><span data-stu-id="9f174-149">As mentioned in the previous tutorial, you can select Item object details with IntelliSense, such as specifying the `ProductName`:</span></span>

![显示数据的项和详细信息-IntelliSense](display_data_items_and_details/_static/image1.png)

<span data-ttu-id="9f174-151">此外使用模型绑定来指定`SelectMethod`值。</span><span class="sxs-lookup"><span data-stu-id="9f174-151">You're also using model binding to specify a `SelectMethod` value.</span></span> <span data-ttu-id="9f174-152">此值 (`GetProducts`) 将添加到代码的方法对应滞后，若要在下一步中显示的产品。</span><span class="sxs-lookup"><span data-stu-id="9f174-152">This value (`GetProducts`) corresponds to the method you'll add to the code behind to display products in the next step.</span></span>

### <a name="add-code-to-display-products"></a><span data-ttu-id="9f174-153">添加代码以显示产品</span><span class="sxs-lookup"><span data-stu-id="9f174-153">Add code to display products</span></span>

<span data-ttu-id="9f174-154">在此步骤中，将添加代码以填充**ListView**产品数据库中的数据的控件。</span><span class="sxs-lookup"><span data-stu-id="9f174-154">In this step, you'll add code to populate the **ListView** control with product data from the database.</span></span> <span data-ttu-id="9f174-155">该代码支持显示所有产品和各个类别的产品。</span><span class="sxs-lookup"><span data-stu-id="9f174-155">The code supports showing all products and  individual category products.</span></span>

1. <span data-ttu-id="9f174-156">在中**解决方案资源管理器**，右键单击*ProductList.aspx* ，然后选择**查看代码**。</span><span class="sxs-lookup"><span data-stu-id="9f174-156">In **Solution Explorer**, right-click *ProductList.aspx* and then select **View Code**.</span></span>
2. <span data-ttu-id="9f174-157">中的现有代码替换*ProductList.aspx.cs*与此文件：</span><span class="sxs-lookup"><span data-stu-id="9f174-157">Replace the existing code in the *ProductList.aspx.cs* file with this:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample3.cs)]

<span data-ttu-id="9f174-158">此代码演示`GetProducts`方法的**ListView**控件的`ItemType`属性中引用*ProductList.aspx*页。</span><span class="sxs-lookup"><span data-stu-id="9f174-158">This code shows the `GetProducts` method that the **ListView** control's `ItemType` property references in the *ProductList.aspx* page.</span></span> <span data-ttu-id="9f174-159">若要将结果限制为特定数据库类别，该代码将设置`categoryId`值从查询字符串值传递给*ProductList.aspx*时页*ProductList.aspx*页导航到。</span><span class="sxs-lookup"><span data-stu-id="9f174-159">To limit the results to a specific database category, the code sets the `categoryId` value from the query string value passed to the *ProductList.aspx* page when the *ProductList.aspx* page is navigated to.</span></span> <span data-ttu-id="9f174-160">`QueryStringAttribute`类中`System.Web.ModelBinding`命名空间用于检索查询字符串变量的值`id`。</span><span class="sxs-lookup"><span data-stu-id="9f174-160">The `QueryStringAttribute` class in the `System.Web.ModelBinding` namespace is used to retrieve the value of the query string variable `id`.</span></span> <span data-ttu-id="9f174-161">这会指示要尝试将值绑定到查询字符串中的模型绑定`categoryId`在运行时参数。</span><span class="sxs-lookup"><span data-stu-id="9f174-161">This instructs model binding to try to bind a value from the query string to the `categoryId` parameter at run time.</span></span>

<span data-ttu-id="9f174-162">有效的类别作为查询字符串传递给页上，当查询的结果被限制为匹配数据库中这些产品`categoryId`值。</span><span class="sxs-lookup"><span data-stu-id="9f174-162">When a valid category is passed as a query string to the page, the results of the query are limited to those products in the database that match the `categoryId` value.</span></span> <span data-ttu-id="9f174-163">例如，如果*ProductsList.aspx*页 URL 是：</span><span class="sxs-lookup"><span data-stu-id="9f174-163">For instance, if the *ProductsList.aspx* page URL is this:</span></span>


[!code-console[Main](display_data_items_and_details/samples/sample4.cmd)]

<span data-ttu-id="9f174-164">页面仅显示产品其中`categoryId`等于`1`。</span><span class="sxs-lookup"><span data-stu-id="9f174-164">The page displays only the products where the `categoryId` equals `1`.</span></span>

<span data-ttu-id="9f174-165">如果没有查询字符串时，包含显示所有产品*ProductList.aspx*调用页面。</span><span class="sxs-lookup"><span data-stu-id="9f174-165">All products are displayed if no query string is included when the *ProductList.aspx* page is called.</span></span>

<span data-ttu-id="9f174-166">这些方法的值的源嘿*值提供程序*(如*查询字符串*)，指示要使用的值提供程序的参数属性称为*值提供程序属性*(如`id`)。</span><span class="sxs-lookup"><span data-stu-id="9f174-166">The sources of values for these methods are referred to as *value providers* (such as *QueryString*), and the parameter attributes that indicate which value provider to use are referred to as *value provider attributes* (such as `id`).</span></span> <span data-ttu-id="9f174-167">ASP.NET 包括值提供程序和所有典型的资源，用户输入的对应属性中的 Web 窗体应用程序，例如查询字符串、 cookie、 窗体值、 控件、 视图状态，会话状态和配置文件属性。</span><span class="sxs-lookup"><span data-stu-id="9f174-167">ASP.NET includes value providers and corresponding attributes for all of the typical sources of user input in a Web Forms application such as the query string, cookies, form values, controls, view state, session state, and profile properties.</span></span> <span data-ttu-id="9f174-168">此外可以编写自定义值提供程序。</span><span class="sxs-lookup"><span data-stu-id="9f174-168">You can also write custom value providers.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="9f174-169">运行此应用程序</span><span class="sxs-lookup"><span data-stu-id="9f174-169">Run the application</span></span>

<span data-ttu-id="9f174-170">运行应用程序现在可以查看所有产品或类别的产品。</span><span class="sxs-lookup"><span data-stu-id="9f174-170">Run the application now to view all products or a category's products.</span></span>

1. <span data-ttu-id="9f174-171">按**F5**在 Visual Studio 运行应用程序中。</span><span class="sxs-lookup"><span data-stu-id="9f174-171">Press **F5** while in Visual Studio to run the application.</span></span>  
   <span data-ttu-id="9f174-172">在浏览器将打开并显示*Default.aspx*页。</span><span class="sxs-lookup"><span data-stu-id="9f174-172">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="9f174-173">选择**汽车**产品类别导航菜单中。</span><span class="sxs-lookup"><span data-stu-id="9f174-173">Select **Cars** from the product category navigation menu.</span></span>  
   <span data-ttu-id="9f174-174">*ProductList.aspx*页显示只显示**汽车**类别产品。</span><span class="sxs-lookup"><span data-stu-id="9f174-174">The *ProductList.aspx* page displays showing only **Cars** category products.</span></span> <span data-ttu-id="9f174-175">稍后在本教程中，将显示产品详细信息。</span><span class="sxs-lookup"><span data-stu-id="9f174-175">Later in this tutorial, you'll display product details.</span></span>  

    ![显示数据的项和详细信息-汽车](display_data_items_and_details/_static/image2.png)

3. <span data-ttu-id="9f174-177">选择**产品**从顶部导航菜单。</span><span class="sxs-lookup"><span data-stu-id="9f174-177">Select **Products** from the navigation menu at the top.</span></span>  
   <span data-ttu-id="9f174-178">同样， *ProductList.aspx*页将显示，但这次它显示了产品的完整列表。</span><span class="sxs-lookup"><span data-stu-id="9f174-178">Again, the *ProductList.aspx* page is displayed, however this time it shows the entire list of products.</span></span>   

    ![显示数据的项和详细信息-产品](display_data_items_and_details/_static/image3.png)

4. <span data-ttu-id="9f174-180">关闭浏览器并返回到 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="9f174-180">Close the browser and return to Visual Studio.</span></span>

### <a name="add-a-data-control-to-display-product-details"></a><span data-ttu-id="9f174-181">添加一个数据控件来显示产品详细信息</span><span class="sxs-lookup"><span data-stu-id="9f174-181">Add a data control to display product details</span></span>

<span data-ttu-id="9f174-182">接下来，将修改中的标记*ProductDetails.aspx*添加在上一教程，以显示特定产品信息页。</span><span class="sxs-lookup"><span data-stu-id="9f174-182">Next, you'll modify the markup in the *ProductDetails.aspx* page that you added in the previous tutorial to display specific product information.</span></span>

1. <span data-ttu-id="9f174-183">在中**解决方案资源管理器**，打开*ProductDetails.aspx*。</span><span class="sxs-lookup"><span data-stu-id="9f174-183">In **Solution Explorer**, open *ProductDetails.aspx*.</span></span>

2. <span data-ttu-id="9f174-184">现有标记替换此标记：</span><span class="sxs-lookup"><span data-stu-id="9f174-184">Replace the existing markup with this markup:</span></span>

    [!code-aspx-csharp[Main](display_data_items_and_details/samples/sample5.aspx)] 

    <span data-ttu-id="9f174-185">此代码使用**FormView**控件来显示特定产品的详细信息。</span><span class="sxs-lookup"><span data-stu-id="9f174-185">This code uses a **FormView** control to display specific product details.</span></span> <span data-ttu-id="9f174-186">此标记使用之类的方法用来显示中的数据的方法*ProductList.aspx*页。</span><span class="sxs-lookup"><span data-stu-id="9f174-186">This markup uses methods like the methods used to display data in the *ProductList.aspx* page.</span></span> <span data-ttu-id="9f174-187">**FormView**控件用于从数据源一次显示一条记录。</span><span class="sxs-lookup"><span data-stu-id="9f174-187">The **FormView** control is used to display a single record at a time from a data source.</span></span> <span data-ttu-id="9f174-188">当你使用**FormView**控件，您创建模板来显示和编辑数据绑定值。</span><span class="sxs-lookup"><span data-stu-id="9f174-188">When you use the **FormView** control, you create templates to display and edit data-bound values.</span></span> <span data-ttu-id="9f174-189">这些模板包含控件，绑定表达式，并在窗体的外观和功能的格式设置定义。</span><span class="sxs-lookup"><span data-stu-id="9f174-189">These templates contain controls, binding expressions, and formatting that define the form's look and functionality.</span></span>

<span data-ttu-id="9f174-190">连接到数据库的以前的标记需要额外的代码。</span><span class="sxs-lookup"><span data-stu-id="9f174-190">Connecting the previous markup to the database requires additional code.</span></span>

1. <span data-ttu-id="9f174-191">在中**解决方案资源管理器**，右键单击*ProductDetails.aspx* ，然后单击**查看代码**。</span><span class="sxs-lookup"><span data-stu-id="9f174-191">In **Solution Explorer**, right-click *ProductDetails.aspx* and then click **View Code**.</span></span>  
   <span data-ttu-id="9f174-192">*ProductDetails.aspx.cs*显示文件。</span><span class="sxs-lookup"><span data-stu-id="9f174-192">The *ProductDetails.aspx.cs* file is displayed.</span></span>

2. <span data-ttu-id="9f174-193">将现有代码替换此代码为：</span><span class="sxs-lookup"><span data-stu-id="9f174-193">Replace the existing code with this code:</span></span>   

    [!code-csharp[Main](display_data_items_and_details/samples/sample6.cs)]

<span data-ttu-id="9f174-194">此代码检查"`productID`"查询字符串值。</span><span class="sxs-lookup"><span data-stu-id="9f174-194">This code checks for a "`productID`" query-string value.</span></span> <span data-ttu-id="9f174-195">如果找到有效的查询字符串值，则将显示匹配的产品。</span><span class="sxs-lookup"><span data-stu-id="9f174-195">If a valid query-string value is found, the matching product is displayed.</span></span> <span data-ttu-id="9f174-196">如果找不到查询字符串，或它的值无效，将显示没有任何产品。</span><span class="sxs-lookup"><span data-stu-id="9f174-196">If the query-string isn't found, or its value isn't valid, no product is displayed.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="9f174-197">运行此应用程序</span><span class="sxs-lookup"><span data-stu-id="9f174-197">Run the application</span></span>

<span data-ttu-id="9f174-198">现在可以运行应用程序以查看显示的各个产品根据产品 id。</span><span class="sxs-lookup"><span data-stu-id="9f174-198">Now you can run the application to see an individual product displayed based on product ID.</span></span>

1. <span data-ttu-id="9f174-199">按**F5**在 Visual Studio 运行应用程序中。</span><span class="sxs-lookup"><span data-stu-id="9f174-199">Press **F5** while in Visual Studio to run the application.</span></span>  
   <span data-ttu-id="9f174-200">在浏览器将打开并显示*Default.aspx*页。</span><span class="sxs-lookup"><span data-stu-id="9f174-200">The browser opens and shows the *Default.aspx* page.</span></span>

2. <span data-ttu-id="9f174-201">选择**船**类别导航菜单中。</span><span class="sxs-lookup"><span data-stu-id="9f174-201">Select **Boats** from the category navigation menu.</span></span>  
   <span data-ttu-id="9f174-202">*ProductList.aspx*显示页。</span><span class="sxs-lookup"><span data-stu-id="9f174-202">The *ProductList.aspx* page is displayed.</span></span>

3. <span data-ttu-id="9f174-203">选择**纸张船**从产品列表。</span><span class="sxs-lookup"><span data-stu-id="9f174-203">Select **Paper Boat** from the product list.</span></span>
   <span data-ttu-id="9f174-204">*ProductDetails.aspx*显示页。</span><span class="sxs-lookup"><span data-stu-id="9f174-204">The *ProductDetails.aspx* page is displayed.</span></span>

    ![显示数据的项和详细信息-产品](display_data_items_and_details/_static/image4.png)
    
4. <span data-ttu-id="9f174-206">关闭浏览器。</span><span class="sxs-lookup"><span data-stu-id="9f174-206">Close the browser.</span></span>


## <a name="additional-resources"></a><span data-ttu-id="9f174-207">其他资源</span><span class="sxs-lookup"><span data-stu-id="9f174-207">Additional resources</span></span>

[<span data-ttu-id="9f174-208">检索和使用模型绑定和 web 窗体显示数据</span><span class="sxs-lookup"><span data-stu-id="9f174-208">Retrieving and displaying data with model binding and web forms</span></span>](../../presenting-and-managing-data/model-binding/retrieving-data.md)

## <a name="next-steps"></a><span data-ttu-id="9f174-209">后续步骤</span><span class="sxs-lookup"><span data-stu-id="9f174-209">Next steps</span></span>

<span data-ttu-id="9f174-210">在本教程中，您可以添加标记和代码，以显示产品和产品详细信息。</span><span class="sxs-lookup"><span data-stu-id="9f174-210">In this tutorial, you added markup and code to display products and product details.</span></span> <span data-ttu-id="9f174-211">您学习了强类型的数据控件、 模型绑定和值提供程序。</span><span class="sxs-lookup"><span data-stu-id="9f174-211">You learned about strongly typed data controls, model binding, and value providers.</span></span> <span data-ttu-id="9f174-212">在下一步的教程中，你将在 Wingtip Toys 示例应用程序中添加购物车。</span><span class="sxs-lookup"><span data-stu-id="9f174-212">In the next tutorial, you'll add a shopping cart to the Wingtip Toys sample application.</span></span> 

> [!div class="step-by-step"]
> <span data-ttu-id="9f174-213">[上一页](ui_and_navigation.md)
> [下一页](shopping-cart.md)</span><span class="sxs-lookup"><span data-stu-id="9f174-213">[Previous](ui_and_navigation.md)
[Next](shopping-cart.md)</span></span>
