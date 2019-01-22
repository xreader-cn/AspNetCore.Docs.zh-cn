---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
title: 教程：添加排序、 筛选和分页与实体框架在 ASP.NET MVC 应用程序 |Microsoft Docs
author: tdykstra
description: 在本教程中添加排序、 筛选和分页功能**学生**索引页。 您还可以创建简单分组页。
ms.author: riande
ms.date: 01/14/2019
ms.assetid: d5723e46-41fe-4d09-850a-e03b9e285bfa
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.topic: tutorial
ms.openlocfilehash: 1f18a15d39d58ffb4ac48cfccee6519d33294e85
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444190"
---
# <a name="tutorial-add-sorting-filtering-and-paging-with-the-entity-framework-in-an-aspnet-mvc-application"></a><span data-ttu-id="649f0-104">教程：添加排序、 筛选和分页与 ASP.NET MVC 应用程序中的实体框架</span><span class="sxs-lookup"><span data-stu-id="649f0-104">Tutorial: Add sorting, filtering, and paging with the Entity Framework in an ASP.NET MVC application</span></span>

<span data-ttu-id="649f0-105">在中[前一篇教程](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)，实现一组基本的 CRUD 操作执行的 web 页面`Student`实体。</span><span class="sxs-lookup"><span data-stu-id="649f0-105">In the [previous tutorial](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md), you implemented a set of web pages for basic CRUD operations for `Student` entities.</span></span> <span data-ttu-id="649f0-106">在本教程中添加排序、 筛选和分页功能**学生**索引页。</span><span class="sxs-lookup"><span data-stu-id="649f0-106">In this tutorial you add sorting, filtering, and paging functionality to the **Students** Index page.</span></span> <span data-ttu-id="649f0-107">您还可以创建简单分组页。</span><span class="sxs-lookup"><span data-stu-id="649f0-107">You also create a simple grouping page.</span></span>

<span data-ttu-id="649f0-108">下图显示了完成后页面外观。</span><span class="sxs-lookup"><span data-stu-id="649f0-108">The following image shows what the page will look like when you're done.</span></span> <span data-ttu-id="649f0-109">列标题是用户可以单击以按该列排序的链接。</span><span class="sxs-lookup"><span data-stu-id="649f0-109">The column headings are links that the user can click to sort by that column.</span></span> <span data-ttu-id="649f0-110">重复单击列标题可在升降排序顺序之间切换。</span><span class="sxs-lookup"><span data-stu-id="649f0-110">Clicking a column heading repeatedly toggles between ascending and descending sort order.</span></span>

![Students_Index_page_with_paging](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

<span data-ttu-id="649f0-112">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="649f0-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="649f0-113">添加列排序链接</span><span class="sxs-lookup"><span data-stu-id="649f0-113">Add column sort links</span></span>
> * <span data-ttu-id="649f0-114">添加搜索框</span><span class="sxs-lookup"><span data-stu-id="649f0-114">Add a Search box</span></span>
> * <span data-ttu-id="649f0-115">添加分页</span><span class="sxs-lookup"><span data-stu-id="649f0-115">Add paging</span></span>
> * <span data-ttu-id="649f0-116">创建一个关于页</span><span class="sxs-lookup"><span data-stu-id="649f0-116">Create an About page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="649f0-117">系统必备</span><span class="sxs-lookup"><span data-stu-id="649f0-117">Prerequisites</span></span>

* [<span data-ttu-id="649f0-118">实现基本的 CRUD 功能</span><span class="sxs-lookup"><span data-stu-id="649f0-118">Implementing Basic CRUD Functionality</span></span>](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)

## <a name="add-column-sort-links"></a><span data-ttu-id="649f0-119">添加列排序链接</span><span class="sxs-lookup"><span data-stu-id="649f0-119">Add column sort links</span></span>

<span data-ttu-id="649f0-120">若要添加到学生索引页进行排序，你将更改`Index`方法`Student`控制器并将代码添加到`Student`索引视图。</span><span class="sxs-lookup"><span data-stu-id="649f0-120">To add sorting to the Student Index page, you'll change the `Index` method of the `Student` controller and add code to the `Student` Index view.</span></span>

### <a name="add-sorting-functionality-to-the-index-method"></a><span data-ttu-id="649f0-121">向 Index 方法添加排序功能</span><span class="sxs-lookup"><span data-stu-id="649f0-121">Add sorting functionality to the Index method</span></span>

- <span data-ttu-id="649f0-122">在中*Controllers\StudentController.cs*，将为`Index`方法使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="649f0-122">In *Controllers\StudentController.cs*, replace the `Index` method with the following code:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

<span data-ttu-id="649f0-123">此代码接收来自 URL 中的查询字符串的 `sortOrder` 参数。</span><span class="sxs-lookup"><span data-stu-id="649f0-123">This code receives a `sortOrder` parameter from the query string in the URL.</span></span> <span data-ttu-id="649f0-124">ASP.NET MVC 提供查询字符串值作为操作方法的参数。</span><span class="sxs-lookup"><span data-stu-id="649f0-124">The query string value is provided by ASP.NET MVC as a parameter to the action method.</span></span> <span data-ttu-id="649f0-125">参数是一个字符串，为"Name"Date"，可以选择后跟一个下划线和字符串"desc"来指定降序。</span><span class="sxs-lookup"><span data-stu-id="649f0-125">The parameter is a string that's either "Name" or "Date", optionally followed by an underscore and the string "desc" to specify descending order.</span></span> <span data-ttu-id="649f0-126">默认排序顺序为升序。</span><span class="sxs-lookup"><span data-stu-id="649f0-126">The default sort order is ascending.</span></span>

<span data-ttu-id="649f0-127">首次请求索引页时，没有任何查询字符串。</span><span class="sxs-lookup"><span data-stu-id="649f0-127">The first time the Index page is requested, there's no query string.</span></span> <span data-ttu-id="649f0-128">学生显示按升序排序依据`LastName`，这是默认设置，如在 fall-through 事例所建立`switch`语句。</span><span class="sxs-lookup"><span data-stu-id="649f0-128">The students are displayed in ascending order by `LastName`, which is the default as established by the fall-through case in the `switch` statement.</span></span> <span data-ttu-id="649f0-129">当用户单击列标题超链接时，查询字符串中会提供相应的 `sortOrder` 值。</span><span class="sxs-lookup"><span data-stu-id="649f0-129">When the user clicks a column heading hyperlink, the appropriate `sortOrder` value is provided in the query string.</span></span>

<span data-ttu-id="649f0-130">这两个`ViewBag`使用变量，以便该视图可以配置与相应的查询字符串值的列标题超链接：</span><span class="sxs-lookup"><span data-stu-id="649f0-130">The two `ViewBag` variables are used so that the view can configure the column heading hyperlinks with the appropriate query string values:</span></span>

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

<span data-ttu-id="649f0-131">这些是三元语句。</span><span class="sxs-lookup"><span data-stu-id="649f0-131">These are ternary statements.</span></span> <span data-ttu-id="649f0-132">第一个指定，如果`sortOrder`参数为 null 或为空，`ViewBag.NameSortParm`应设置为"名称\_desc"; 否则为它应设置为空字符串。</span><span class="sxs-lookup"><span data-stu-id="649f0-132">The first one specifies that if the `sortOrder` parameter is null or empty, `ViewBag.NameSortParm` should be set to "name\_desc"; otherwise, it should be set to an empty string.</span></span> <span data-ttu-id="649f0-133">通过这两个语句，视图可如下设置列标题超链接：</span><span class="sxs-lookup"><span data-stu-id="649f0-133">These two statements enable the view to set the column heading hyperlinks as follows:</span></span>

| <span data-ttu-id="649f0-134">当前排序顺序</span><span class="sxs-lookup"><span data-stu-id="649f0-134">Current sort order</span></span> | <span data-ttu-id="649f0-135">姓氏超链接</span><span class="sxs-lookup"><span data-stu-id="649f0-135">Last Name Hyperlink</span></span> | <span data-ttu-id="649f0-136">日期超链接</span><span class="sxs-lookup"><span data-stu-id="649f0-136">Date Hyperlink</span></span> |
| --- | --- | --- |
| <span data-ttu-id="649f0-137">姓氏升序</span><span class="sxs-lookup"><span data-stu-id="649f0-137">Last Name ascending</span></span> | <span data-ttu-id="649f0-138">descending</span><span class="sxs-lookup"><span data-stu-id="649f0-138">descending</span></span> | <span data-ttu-id="649f0-139">ascending</span><span class="sxs-lookup"><span data-stu-id="649f0-139">ascending</span></span> |
| <span data-ttu-id="649f0-140">姓氏降序</span><span class="sxs-lookup"><span data-stu-id="649f0-140">Last Name descending</span></span> | <span data-ttu-id="649f0-141">ascending</span><span class="sxs-lookup"><span data-stu-id="649f0-141">ascending</span></span> | <span data-ttu-id="649f0-142">ascending</span><span class="sxs-lookup"><span data-stu-id="649f0-142">ascending</span></span> |
| <span data-ttu-id="649f0-143">日期升序</span><span class="sxs-lookup"><span data-stu-id="649f0-143">Date ascending</span></span> | <span data-ttu-id="649f0-144">ascending</span><span class="sxs-lookup"><span data-stu-id="649f0-144">ascending</span></span> | <span data-ttu-id="649f0-145">descending</span><span class="sxs-lookup"><span data-stu-id="649f0-145">descending</span></span> |
| <span data-ttu-id="649f0-146">日期降序</span><span class="sxs-lookup"><span data-stu-id="649f0-146">Date descending</span></span> | <span data-ttu-id="649f0-147">ascending</span><span class="sxs-lookup"><span data-stu-id="649f0-147">ascending</span></span> | <span data-ttu-id="649f0-148">ascending</span><span class="sxs-lookup"><span data-stu-id="649f0-148">ascending</span></span> |

<span data-ttu-id="649f0-149">该方法使用[LINQ to Entities](/dotnet/framework/data/adonet/ef/language-reference/linq-to-entities)指定要作为排序依据的列。</span><span class="sxs-lookup"><span data-stu-id="649f0-149">The method uses [LINQ to Entities](/dotnet/framework/data/adonet/ef/language-reference/linq-to-entities) to specify the column to sort by.</span></span> <span data-ttu-id="649f0-150">该代码会创建<xref:System.Linq.IQueryable%601>变量之前`switch`语句，将修改在`switch`语句，然后调用`ToList`方法之后`switch`语句。</span><span class="sxs-lookup"><span data-stu-id="649f0-150">The code creates an <xref:System.Linq.IQueryable%601> variable before the `switch` statement, modifies it in the `switch` statement, and calls the `ToList` method after the `switch` statement.</span></span> <span data-ttu-id="649f0-151">当创建和修改 `IQueryable` 变量时，不会向数据库发送任何查询。</span><span class="sxs-lookup"><span data-stu-id="649f0-151">When you create and modify `IQueryable` variables, no query is sent to the database.</span></span> <span data-ttu-id="649f0-152">您将转换之前未执行查询`IQueryable`对象通过调用一个方法，如集合`ToList`。</span><span class="sxs-lookup"><span data-stu-id="649f0-152">The query is not executed until you convert the `IQueryable` object into a collection by calling a method such as `ToList`.</span></span> <span data-ttu-id="649f0-153">因此，此代码导致直到才执行单个查询`return View`语句。</span><span class="sxs-lookup"><span data-stu-id="649f0-153">Therefore, this code results in a single query that is not executed until the `return View` statement.</span></span>

<span data-ttu-id="649f0-154">编写每个排序顺序不同的 LINQ 语句的替代方法，可以动态创建的 LINQ 语句。</span><span class="sxs-lookup"><span data-stu-id="649f0-154">As an alternative to writing different LINQ statements for each sort order, you can dynamically create a LINQ statement.</span></span> <span data-ttu-id="649f0-155">有关动态 LINQ 的信息，请参阅[动态 LINQ](https://go.microsoft.com/fwlink/?LinkID=323957)。</span><span class="sxs-lookup"><span data-stu-id="649f0-155">For information about dynamic LINQ, see [Dynamic LINQ](https://go.microsoft.com/fwlink/?LinkID=323957).</span></span>

### <a name="add-column-heading-hyperlinks-to-the-student-index-view"></a><span data-ttu-id="649f0-156">将列标题超链接添加到学生索引视图</span><span class="sxs-lookup"><span data-stu-id="649f0-156">Add column heading hyperlinks to the Student index view</span></span>

1. <span data-ttu-id="649f0-157">在中*Views\Student\Index.cshtml*，替换`<tr>`和`<th>`的标题行的突出显示的代码元素：</span><span class="sxs-lookup"><span data-stu-id="649f0-157">In *Views\Student\Index.cshtml*, replace the `<tr>` and `<th>` elements for the heading row with the highlighted code:</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cshtml?highlight=5-15)]

   <span data-ttu-id="649f0-158">此代码使用中的信息`ViewBag`属性具有相应查询的超链接设置字符串值。</span><span class="sxs-lookup"><span data-stu-id="649f0-158">This code uses the information in the `ViewBag` properties to set up hyperlinks with the appropriate query string values.</span></span>

2. <span data-ttu-id="649f0-159">运行页面，然后单击**姓氏**并**注册日期**列标题以验证排序是否工作。</span><span class="sxs-lookup"><span data-stu-id="649f0-159">Run the page and click the **Last Name** and **Enrollment Date** column headings to verify that sorting works.</span></span>

   <span data-ttu-id="649f0-160">单击后**姓氏**标题下方，学生显示按降序姓氏的顺序。</span><span class="sxs-lookup"><span data-stu-id="649f0-160">After you click the **Last Name** heading, students are displayed in descending last name order.</span></span>

## <a name="add-a-search-box"></a><span data-ttu-id="649f0-161">添加搜索框</span><span class="sxs-lookup"><span data-stu-id="649f0-161">Add a Search box</span></span>

<span data-ttu-id="649f0-162">若要添加筛选到学生索引页，将向视图添加一个文本框和一个提交按钮，并进行相应更改中的`Index`方法。</span><span class="sxs-lookup"><span data-stu-id="649f0-162">To add filtering to the Students index page, you'll add a text box and a submit button to the view and make corresponding changes in the `Index` method.</span></span> <span data-ttu-id="649f0-163">在文本框中，可以输入要在名字和姓氏字段中搜索的字符串。</span><span class="sxs-lookup"><span data-stu-id="649f0-163">The text box lets you enter a string to search for in the first name and last name fields.</span></span>

### <a name="add-filtering-functionality-to-the-index-method"></a><span data-ttu-id="649f0-164">向 Index 方法添加筛选功能</span><span class="sxs-lookup"><span data-stu-id="649f0-164">Add filtering functionality to the Index method</span></span>

- <span data-ttu-id="649f0-165">在中*Controllers\StudentController.cs*，将为`Index`用下面的代码 （所做的更改会突出显示） 的方法：</span><span class="sxs-lookup"><span data-stu-id="649f0-165">In *Controllers\StudentController.cs*, replace the `Index` method with the following code (the changes are highlighted):</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs?highlight=1,7-11)]

<span data-ttu-id="649f0-166">该代码将添加`searchString`参数`Index`方法。</span><span class="sxs-lookup"><span data-stu-id="649f0-166">The code adds a `searchString` parameter to the `Index` method.</span></span> <span data-ttu-id="649f0-167">从要添加到索引视图的文本框中接收搜索字符串值。</span><span class="sxs-lookup"><span data-stu-id="649f0-167">The search string value is received from a text box that you'll add to the Index view.</span></span> <span data-ttu-id="649f0-168">它还添加了`where`子句仅选择的学生的名字或姓氏中包含搜索字符串的 LINQ 语句。</span><span class="sxs-lookup"><span data-stu-id="649f0-168">It also adds a `where` clause to the LINQ statement that selects only students whose first name or last name contains the search string.</span></span> <span data-ttu-id="649f0-169">添加的语句<xref:System.Linq.Queryable.Where%2A>子句执行仅当没有要搜索的值。</span><span class="sxs-lookup"><span data-stu-id="649f0-169">The statement that adds the <xref:System.Linq.Queryable.Where%2A> clause executes only if there's a value to search for.</span></span>

> [!NOTE]
> <span data-ttu-id="649f0-170">在许多情况下可以对实体框架实体集或作为内存中集合上的扩展方法调用相同的方法。</span><span class="sxs-lookup"><span data-stu-id="649f0-170">In many cases you can call the same method either on an Entity Framework entity set or as an extension method on an in-memory collection.</span></span> <span data-ttu-id="649f0-171">结果通常都是相同，但在某些情况下可能会不同。</span><span class="sxs-lookup"><span data-stu-id="649f0-171">The results are normally the same but in some cases may be different.</span></span>
>
> <span data-ttu-id="649f0-172">例如，.NET Framework 实现`Contains`方法将空字符串传递给它，而 SQL Server Compact 4.0 的实体框架提供程序返回空字符串零行，则返回所有行。</span><span class="sxs-lookup"><span data-stu-id="649f0-172">For example, the .NET Framework implementation of the `Contains` method returns all rows when you pass an empty string to it, but the Entity Framework provider for SQL Server Compact 4.0 returns zero rows for empty strings.</span></span> <span data-ttu-id="649f0-173">因此该示例中的代码 (将放`Where`内的语句`if`语句) 可确保对于所有版本的 SQL Server 获得相同的结果。</span><span class="sxs-lookup"><span data-stu-id="649f0-173">Therefore the code in the example (putting the `Where` statement inside an `if` statement) makes sure that you get the same results for all versions of SQL Server.</span></span> <span data-ttu-id="649f0-174">此外，.NET Framework 实现`Contains`方法默认情况下，执行区分大小写比较，但实体框架 SQL Server 提供程序默认情况下执行不区分大小写的比较。</span><span class="sxs-lookup"><span data-stu-id="649f0-174">Also, the .NET Framework implementation of the `Contains` method performs a case-sensitive comparison by default, but Entity Framework SQL Server providers perform case-insensitive comparisons by default.</span></span> <span data-ttu-id="649f0-175">因此，调用`ToUpper`使测试显式不区分大小写的方法可确保结果不更改时更改代码更高版本以使用存储库，它将返回`IEnumerable`而不是集合`IQueryable`对象。</span><span class="sxs-lookup"><span data-stu-id="649f0-175">Therefore, calling the `ToUpper` method to make the test explicitly case-insensitive ensures that results do not change when you change the code later to use a repository, which will return an `IEnumerable` collection instead of an `IQueryable` object.</span></span> <span data-ttu-id="649f0-176">（在 `IEnumerable` 集合上调用 `Contains` 方法时，将获得 .NET Framework 实现；当在 `IQueryable` 对象上调用它时，将获得数据库提供程序实现。）</span><span class="sxs-lookup"><span data-stu-id="649f0-176">(When you call the `Contains` method on an `IEnumerable` collection, you get the .NET Framework implementation; when you call it on an `IQueryable` object, you get the database provider implementation.)</span></span>
>
> <span data-ttu-id="649f0-177">Null 处理也可能会因不同的数据库提供程序，或者在使用`IQueryable`使用时，对象相比`IEnumerable`集合。</span><span class="sxs-lookup"><span data-stu-id="649f0-177">Null handling may also be different for different database providers or when you use an `IQueryable` object compared to when you use an `IEnumerable` collection.</span></span> <span data-ttu-id="649f0-178">例如，在某些情况下`Where`如条件`table.Column != 0`可能不会返回具有列`null`作为值。</span><span class="sxs-lookup"><span data-stu-id="649f0-178">For example, in some scenarios a `Where` condition such as `table.Column != 0` may not return columns that have `null` as the value.</span></span> <span data-ttu-id="649f0-179">有关详细信息，请参阅[的 where 子句中的 null 变量不正确处理](https://data.uservoice.com/forums/72025-entity-framework-feature-suggestions/suggestions/1015361-incorrect-handling-of-null-variables-in-where-cl)。</span><span class="sxs-lookup"><span data-stu-id="649f0-179">For more information, see [Incorrect handling of null variables in 'where' clause](https://data.uservoice.com/forums/72025-entity-framework-feature-suggestions/suggestions/1015361-incorrect-handling-of-null-variables-in-where-cl).</span></span>

### <a name="add-a-search-box-to-the-student-index-view"></a><span data-ttu-id="649f0-180">向学生索引视图添加搜索框</span><span class="sxs-lookup"><span data-stu-id="649f0-180">Add a search box to the Student index view</span></span>

1. <span data-ttu-id="649f0-181">在中*Views\Student\Index.cshtml*，添加打开之前立即突出显示的代码`table`若要创建标题栏、 文本框中，标记和一个**搜索**按钮。</span><span class="sxs-lookup"><span data-stu-id="649f0-181">In *Views\Student\Index.cshtml*, add the highlighted code immediately before the opening `table` tag in order to create a caption, a text box, and a **Search** button.</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml?highlight=4-11)]

2. <span data-ttu-id="649f0-182">运行页上，输入搜索字符串，然后单击**搜索**以验证筛选是否正常工作。</span><span class="sxs-lookup"><span data-stu-id="649f0-182">Run the page, enter a search string, and click **Search** to verify that filtering is working.</span></span>

   <span data-ttu-id="649f0-183">请注意，URL 不包含"an"搜索字符串，这意味着，如果此页加入书签，您不会获得筛选的列表，当您使用书签。</span><span class="sxs-lookup"><span data-stu-id="649f0-183">Notice the URL doesn't contain the "an" search string, which means that if you bookmark this page, you won't get the filtered list when you use the bookmark.</span></span> <span data-ttu-id="649f0-184">这同样适用于列排序链接，因为它们将对整个列表进行排序。</span><span class="sxs-lookup"><span data-stu-id="649f0-184">This applies also to the column sort links, as they will sort the whole list.</span></span> <span data-ttu-id="649f0-185">将更改**搜索**按钮，本教程的后面使用筛选器条件的查询字符串。</span><span class="sxs-lookup"><span data-stu-id="649f0-185">You'll change the **Search** button to use query strings for filter criteria later in the tutorial.</span></span>

## <a name="add-paging"></a><span data-ttu-id="649f0-186">添加分页</span><span class="sxs-lookup"><span data-stu-id="649f0-186">Add paging</span></span>

<span data-ttu-id="649f0-187">若要向学生索引页添加分页，首先你将安装**PagedList.Mvc** NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="649f0-187">To add paging to the Students index page, you'll start by installing the **PagedList.Mvc** NuGet package.</span></span> <span data-ttu-id="649f0-188">然后你将进行中的其他更改`Index`方法并添加分页链接到`Index`视图。</span><span class="sxs-lookup"><span data-stu-id="649f0-188">Then you'll make additional changes in the `Index` method and add paging links to the `Index` view.</span></span> <span data-ttu-id="649f0-189">**PagedList.Mvc**是许多很好的分页和排序的 ASP.NET MVC 中，包之一，此处使用旨在仅作为示例，不为相对于其他选择为其建议。</span><span class="sxs-lookup"><span data-stu-id="649f0-189">**PagedList.Mvc** is one of many good paging and sorting packages for ASP.NET MVC, and its use here is intended only as an example, not as a recommendation for it over other options.</span></span>

### <a name="install-the-pagedlistmvc-nuget-package"></a><span data-ttu-id="649f0-190">安装 PagedList.MVC NuGet 包</span><span class="sxs-lookup"><span data-stu-id="649f0-190">Install the PagedList.MVC NuGet package</span></span>

<span data-ttu-id="649f0-191">NuGet **PagedList.Mvc**会自动安装包**PagedList**包作为依赖项。</span><span class="sxs-lookup"><span data-stu-id="649f0-191">The NuGet **PagedList.Mvc** package automatically installs the **PagedList** package as a dependency.</span></span> <span data-ttu-id="649f0-192">**PagedList**打包安装`PagedList`的集合类型和扩展方法`IQueryable`和`IEnumerable`集合。</span><span class="sxs-lookup"><span data-stu-id="649f0-192">The **PagedList** package installs a `PagedList` collection type and extension methods for `IQueryable` and `IEnumerable` collections.</span></span> <span data-ttu-id="649f0-193">扩展方法创建单个页面中的数据`PagedList`共集合您`IQueryable`或`IEnumerable`，和`PagedList`集合提供了一些属性和方法的简化分页。</span><span class="sxs-lookup"><span data-stu-id="649f0-193">The extension methods create a single page of data in a `PagedList` collection out of your `IQueryable` or `IEnumerable`, and the `PagedList` collection provides several properties and methods that facilitate paging.</span></span> <span data-ttu-id="649f0-194">**PagedList.Mvc**包将安装的分页帮助器显示分页按钮。</span><span class="sxs-lookup"><span data-stu-id="649f0-194">The **PagedList.Mvc** package installs a paging helper that displays the paging buttons.</span></span>

1. <span data-ttu-id="649f0-195">从**工具**菜单中，选择**NuGet 包管理器**，然后**程序包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="649f0-195">From the **Tools** menu, select **NuGet Package Manager** and then **Package Manager Console**.</span></span>

2. <span data-ttu-id="649f0-196">在中**程序包管理器控制台**窗口中，请确保**包源**是**nuget.org**并**默认项目**是**ContosoUniversity**，然后输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="649f0-196">In the **Package Manager Console** window, make sure the **Package source** is **nuget.org** and the **Default project** is **ContosoUniversity**, and then enter the following command:</span></span>

   ```text
   Install-Package PagedList.Mvc
   ```

3. <span data-ttu-id="649f0-197">生成项目。</span><span class="sxs-lookup"><span data-stu-id="649f0-197">Build the project.</span></span>

### <a name="add-paging-functionality-to-the-index-method"></a><span data-ttu-id="649f0-198">向 Index 方法添加分页功能</span><span class="sxs-lookup"><span data-stu-id="649f0-198">Add paging functionality to the Index method</span></span>

1. <span data-ttu-id="649f0-199">在中*Controllers\StudentController.cs*，添加`using`语句`PagedList`命名空间：</span><span class="sxs-lookup"><span data-stu-id="649f0-199">In *Controllers\StudentController.cs*, add a `using` statement for the `PagedList` namespace:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

2. <span data-ttu-id="649f0-200">将 `Index` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="649f0-200">Replace the `Index` method with the following code:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=1,3,7-16,41-43)]

   <span data-ttu-id="649f0-201">此代码将添加`page`参数、 当前的排序顺序参数和当前的筛选器参数向方法签名：</span><span class="sxs-lookup"><span data-stu-id="649f0-201">This code adds a `page` parameter, a current sort order parameter, and a current filter parameter to the method signature:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

   <span data-ttu-id="649f0-202">第一次显示页面时，或如果用户尚未单击分页或排序链接，所有参数都均为 null。</span><span class="sxs-lookup"><span data-stu-id="649f0-202">The first time the page is displayed, or if the user hasn't clicked a paging or sorting link, all the parameters are null.</span></span> <span data-ttu-id="649f0-203">如果单击分页链接后，`page`变量包含要显示的页码。</span><span class="sxs-lookup"><span data-stu-id="649f0-203">If a paging link is clicked, the `page` variable contains the page number to display.</span></span>

   <span data-ttu-id="649f0-204">一个`ViewBag`属性提供了与当前的排序顺序中，视图，因为此值必须包含在分页链接为了保持排序顺序都分页时相同：</span><span class="sxs-lookup"><span data-stu-id="649f0-204">A `ViewBag` property provides the view with the current sort order, because this must be included in the paging links in order to keep the sort order the same while paging:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

   <span data-ttu-id="649f0-205">另一个属性， `ViewBag.CurrentFilter`，视图提供当前筛选器字符串。</span><span class="sxs-lookup"><span data-stu-id="649f0-205">Another property, `ViewBag.CurrentFilter`, provides the view with the current filter string.</span></span> <span data-ttu-id="649f0-206">此值必须包含在分页链接中，以便在分页过程中保持筛选器设置，并且在页面重新显示时必须将其还原到文本框中。</span><span class="sxs-lookup"><span data-stu-id="649f0-206">This value must be included in the paging links in order to maintain the filter settings during paging, and it must be restored to the text box when the page is redisplayed.</span></span> <span data-ttu-id="649f0-207">如果在分页过程中搜索字符串发生变化，则页面必须重置为 1，因为新的筛选器会导致显示不同的数据。</span><span class="sxs-lookup"><span data-stu-id="649f0-207">If the search string is changed during paging, the page has to be reset to 1, because the new filter can result in different data to display.</span></span> <span data-ttu-id="649f0-208">在文本框中输入值并按下提交按钮，则会更改搜索字符串。</span><span class="sxs-lookup"><span data-stu-id="649f0-208">The search string is changed when a value is entered in the text box and the submit button is pressed.</span></span> <span data-ttu-id="649f0-209">在这种情况下，`searchString`参数不为 null。</span><span class="sxs-lookup"><span data-stu-id="649f0-209">In that case, the `searchString` parameter is not null.</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

   <span data-ttu-id="649f0-210">该方法的末尾`ToPagedList`学生的扩展方法`IQueryable`对象将学生查询转换为支持分页的集合类型中的学生的单个页。</span><span class="sxs-lookup"><span data-stu-id="649f0-210">At the end of the method, the `ToPagedList` extension method on the students `IQueryable` object converts the student query to a single page of students in a collection type that supports paging.</span></span> <span data-ttu-id="649f0-211">该单个学生页面然后传递给视图：</span><span class="sxs-lookup"><span data-stu-id="649f0-211">That single page of students is then passed to the view:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

   <span data-ttu-id="649f0-212">`ToPagedList` 方法需要一个页码。</span><span class="sxs-lookup"><span data-stu-id="649f0-212">The `ToPagedList` method takes a page number.</span></span> <span data-ttu-id="649f0-213">两个问号表示[null 合并运算符](/dotnet/csharp/language-reference/operators/null-coalescing-operator)。</span><span class="sxs-lookup"><span data-stu-id="649f0-213">The two question marks represent the [null-coalescing operator](/dotnet/csharp/language-reference/operators/null-coalescing-operator).</span></span> <span data-ttu-id="649f0-214">NULL 合并运算符为可为 NULL 的类型定义默认值；表达式 `(page ?? 1)` 表示如果 `page` 有值，则返回该值，如果 `page` 为 NULL，则返回 1。</span><span class="sxs-lookup"><span data-stu-id="649f0-214">The null-coalescing operator defines a default value for a nullable type; the expression `(page ?? 1)` means return the value of `page` if it has a value, or return 1 if `page` is null.</span></span>

### <a name="add-paging-links-to-the-student-index-view"></a><span data-ttu-id="649f0-215">向学生索引视图添加分页链接</span><span class="sxs-lookup"><span data-stu-id="649f0-215">Add paging links to the Student index view</span></span>

1. <span data-ttu-id="649f0-216">在中*Views\Student\Index.cshtml*，现有代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="649f0-216">In *Views\Student\Index.cshtml*, replace the existing code with the following code.</span></span> <span data-ttu-id="649f0-217">突出显示所作更改。</span><span class="sxs-lookup"><span data-stu-id="649f0-217">The changes are highlighted.</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=1-3,6,9,14,17,24,30,55-56,58-59)]

   <span data-ttu-id="649f0-218">页面顶部的 `@model` 语句指定视图现在获取的是 `PagedList` 对象，而不是 `List` 对象。</span><span class="sxs-lookup"><span data-stu-id="649f0-218">The `@model` statement at the top of the page specifies that the view now gets a `PagedList` object instead of a `List` object.</span></span>

   <span data-ttu-id="649f0-219">`using`语句`PagedList.Mvc`，访问 MVC 帮助程序的分页按钮。</span><span class="sxs-lookup"><span data-stu-id="649f0-219">The `using` statement for `PagedList.Mvc` gives access to the MVC helper for the paging buttons.</span></span>

   <span data-ttu-id="649f0-220">该代码使用的重载[BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) ，它指定允许[FormMethod.Get](/previous-versions/aspnet/dd460179(v=vs.100))。</span><span class="sxs-lookup"><span data-stu-id="649f0-220">The code uses an overload of [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) that allows it to specify [FormMethod.Get](/previous-versions/aspnet/dd460179(v=vs.100)).</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cshtml?highlight=1)]

   <span data-ttu-id="649f0-221">默认值[BeginForm](/previous-versions/aspnet/dd492719(v=vs.108))提交表单数据对于 POST，这意味着，参数将 HTTP 消息正文中而不是在 URL 作为查询字符串传递。</span><span class="sxs-lookup"><span data-stu-id="649f0-221">The default [BeginForm](/previous-versions/aspnet/dd492719(v=vs.108)) submits form data with a POST, which means that parameters are passed in the HTTP message body and not in the URL as query strings.</span></span> <span data-ttu-id="649f0-222">当指定 HTTP GET 时，表单数据作为查询字符串在 URL 中传递，从而使用户能够将 URL 加入书签。</span><span class="sxs-lookup"><span data-stu-id="649f0-222">When you specify HTTP GET, the form data is passed in the URL as query strings, which enables users to bookmark the URL.</span></span> <span data-ttu-id="649f0-223">[使用 HTTP GET 的 W3C 指南](http://www.w3.org/2001/tag/doc/whenToUseGet.html)建议操作不会导致更新时应使用 GET。</span><span class="sxs-lookup"><span data-stu-id="649f0-223">The [W3C guidelines for the use of HTTP GET](http://www.w3.org/2001/tag/doc/whenToUseGet.html) recommend that you should use GET when the action does not result in an update.</span></span>

   <span data-ttu-id="649f0-224">单击新页面时可以看到当前的搜索字符串，将使用当前搜索字符串初始化文本框。</span><span class="sxs-lookup"><span data-stu-id="649f0-224">The text box is initialized with the current search string so when you click a new page you can see the current search string.</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cshtml?highlight=1)]

   <span data-ttu-id="649f0-225">列标题链接使用查询字符串向控制器传递当前搜索字符串，以便用户可以在筛选结果中进行排序：</span><span class="sxs-lookup"><span data-stu-id="649f0-225">The column header links use the query string to pass the current search string to the controller so that the user can sort within filter results:</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cshtml?highlight=1)]

   <span data-ttu-id="649f0-226">显示当前页和总页数。</span><span class="sxs-lookup"><span data-stu-id="649f0-226">The current page and total number of pages are displayed.</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cshtml)]

   <span data-ttu-id="649f0-227">如果没有要显示的页面，会显示"的 0 页 0"。</span><span class="sxs-lookup"><span data-stu-id="649f0-227">If there are no pages to display, "Page 0 of 0" is shown.</span></span> <span data-ttu-id="649f0-228">(在这种情况下页面数大于页计数因为`Model.PageNumber`为 1，和`Model.PageCount`为 0。)</span><span class="sxs-lookup"><span data-stu-id="649f0-228">(In that case the page number is greater than the page count because `Model.PageNumber` is 1, and `Model.PageCount` is 0.)</span></span>

   <span data-ttu-id="649f0-229">通过显示分页按钮`PagedListPager`帮助程序：</span><span class="sxs-lookup"><span data-stu-id="649f0-229">The paging buttons are displayed by the `PagedListPager` helper:</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml)]

   <span data-ttu-id="649f0-230">`PagedListPager`帮助器提供多种可自定义，包括 Url 和样式设置的选项。</span><span class="sxs-lookup"><span data-stu-id="649f0-230">The `PagedListPager` helper provides a number of options that you can customize, including URLs and styling.</span></span> <span data-ttu-id="649f0-231">有关详细信息，请参阅[TroyGoode / PagedList](https://github.com/TroyGoode/PagedList) GitHub 站点上。</span><span class="sxs-lookup"><span data-stu-id="649f0-231">For more information, see [TroyGoode / PagedList](https://github.com/TroyGoode/PagedList) on the GitHub site.</span></span>

2. <span data-ttu-id="649f0-232">运行页。</span><span class="sxs-lookup"><span data-stu-id="649f0-232">Run the page.</span></span>

   <span data-ttu-id="649f0-233">单击不同排序顺序的分页链接，以确保分页正常工作。</span><span class="sxs-lookup"><span data-stu-id="649f0-233">Click the paging links in different sort orders to make sure paging works.</span></span> <span data-ttu-id="649f0-234">然后输入一个搜索字符串并再次尝试分页，以验证分页也可以正确地进行排序和筛选。</span><span class="sxs-lookup"><span data-stu-id="649f0-234">Then enter a search string and try paging again to verify that paging also works correctly with sorting and filtering.</span></span>

## <a name="create-an-about-page"></a><span data-ttu-id="649f0-235">创建一个关于页</span><span class="sxs-lookup"><span data-stu-id="649f0-235">Create an About page</span></span>

<span data-ttu-id="649f0-236">对于 Contoso 大学网站的一页，将显示每个注册日期注册学生的数量。</span><span class="sxs-lookup"><span data-stu-id="649f0-236">For the Contoso University website's About page, you'll display how many students have enrolled for each enrollment date.</span></span> <span data-ttu-id="649f0-237">这需要对组进行分组和简单计算。</span><span class="sxs-lookup"><span data-stu-id="649f0-237">This requires grouping and simple calculations on the groups.</span></span> <span data-ttu-id="649f0-238">若要完成此操作，需要执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="649f0-238">To accomplish this, you'll do the following:</span></span>

- <span data-ttu-id="649f0-239">为需要传递给视图的数据创建一个视图模型类。</span><span class="sxs-lookup"><span data-stu-id="649f0-239">Create a view model class for the data that you need to pass to the view.</span></span>
- <span data-ttu-id="649f0-240">修改`About`中的方法`Home`控制器。</span><span class="sxs-lookup"><span data-stu-id="649f0-240">Modify the `About` method in the `Home` controller.</span></span>
- <span data-ttu-id="649f0-241">修改`About`视图。</span><span class="sxs-lookup"><span data-stu-id="649f0-241">Modify the `About` view.</span></span>

### <a name="create-the-view-model"></a><span data-ttu-id="649f0-242">创建视图模型</span><span class="sxs-lookup"><span data-stu-id="649f0-242">Create the View Model</span></span>

<span data-ttu-id="649f0-243">创建*Viewmodel*项目文件夹中的文件夹。</span><span class="sxs-lookup"><span data-stu-id="649f0-243">Create a *ViewModels* folder in the project folder.</span></span> <span data-ttu-id="649f0-244">在该文件夹中，将类文件添加*EnrollmentDateGroup.cs*和模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="649f0-244">In that folder, add a class file *EnrollmentDateGroup.cs* and replace the template code with the following code:</span></span>

[!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

### <a name="modify-the-home-controller"></a><span data-ttu-id="649f0-245">修改主控制器</span><span class="sxs-lookup"><span data-stu-id="649f0-245">Modify the Home Controller</span></span>

1. <span data-ttu-id="649f0-246">在中*HomeController.cs*，添加以下`using`语句的文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="649f0-246">In *HomeController.cs*, add the following `using` statements at the top of the file:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cs)]

2. <span data-ttu-id="649f0-247">类的左大括号后立即添加数据库上下文的类变量：</span><span class="sxs-lookup"><span data-stu-id="649f0-247">Add a class variable for the database context immediately after the opening curly brace for the class:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs?highlight=3)]

3. <span data-ttu-id="649f0-248">将 `About` 方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="649f0-248">Replace the `About` method with the following code:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cs)]

   <span data-ttu-id="649f0-249">LINQ 语句按注册日期对学生实体进行分组，计算每组中实体的数量，并将结果存储在 `EnrollmentDateGroup` 视图模型对象的集合中。</span><span class="sxs-lookup"><span data-stu-id="649f0-249">The LINQ statement groups the student entities by enrollment date, calculates the number of entities in each group, and stores the results in a collection of `EnrollmentDateGroup` view model objects.</span></span>

4. <span data-ttu-id="649f0-250">添加`Dispose`方法：</span><span class="sxs-lookup"><span data-stu-id="649f0-250">Add a `Dispose` method:</span></span>

   [!code-csharp[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs)]

### <a name="modify-the-about-view"></a><span data-ttu-id="649f0-251">修改“关于”视图</span><span class="sxs-lookup"><span data-stu-id="649f0-251">Modify the About View</span></span>

1. <span data-ttu-id="649f0-252">中的代码替换*Views\Home\About.cshtml*文件使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="649f0-252">Replace the code in the *Views\Home\About.cshtml* file with the following code:</span></span>

   [!code-cshtml[Main](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cshtml)]

2. <span data-ttu-id="649f0-253">运行应用并单击**有关**链接。</span><span class="sxs-lookup"><span data-stu-id="649f0-253">Run the app and click the **About** link.</span></span>

   <span data-ttu-id="649f0-254">在表中显示的每个注册日期的学生计数。</span><span class="sxs-lookup"><span data-stu-id="649f0-254">The count of students for each enrollment date displays in a table.</span></span>

   ![About_page](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image9.png)

## <a name="get-the-code"></a><span data-ttu-id="649f0-256">获取代码</span><span class="sxs-lookup"><span data-stu-id="649f0-256">Get the code</span></span>

[<span data-ttu-id="649f0-257">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="649f0-257">Download the Completed Project</span></span>](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a><span data-ttu-id="649f0-258">其他资源</span><span class="sxs-lookup"><span data-stu-id="649f0-258">Additional resources</span></span>

<span data-ttu-id="649f0-259">其他实体框架资源的链接可在[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。</span><span class="sxs-lookup"><span data-stu-id="649f0-259">Links to other Entity Framework resources can be found in [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="649f0-260">后续步骤</span><span class="sxs-lookup"><span data-stu-id="649f0-260">Next steps</span></span>

<span data-ttu-id="649f0-261">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="649f0-261">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="649f0-262">添加列排序链接</span><span class="sxs-lookup"><span data-stu-id="649f0-262">Add column sort links</span></span>
> * <span data-ttu-id="649f0-263">添加搜索框</span><span class="sxs-lookup"><span data-stu-id="649f0-263">Add a Search box</span></span>
> * <span data-ttu-id="649f0-264">添加分页</span><span class="sxs-lookup"><span data-stu-id="649f0-264">Add paging</span></span>
> * <span data-ttu-id="649f0-265">创建一个关于页</span><span class="sxs-lookup"><span data-stu-id="649f0-265">Create an About page</span></span>

<span data-ttu-id="649f0-266">转到下一步的文章，了解如何使用连接复原和命令拦截。</span><span class="sxs-lookup"><span data-stu-id="649f0-266">Advance to the next article to learn how to use connection resiliency and command interception.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="649f0-267">连接复原和命令截获</span><span class="sxs-lookup"><span data-stu-id="649f0-267">Connection resiliency and command interception</span></span>](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application.md)