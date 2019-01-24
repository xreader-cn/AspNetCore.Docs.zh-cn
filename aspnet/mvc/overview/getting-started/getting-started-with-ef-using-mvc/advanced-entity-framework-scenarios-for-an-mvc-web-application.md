---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application
title: 教程：了解有关 EF 的 MVC 5 Web 应用的高级方案
description: 本教程包括引入了几个主题都有可用于注意当超出使用 Entity Framework Code First 开发 ASP.NET web 应用程序的基础知识。
author: tdykstra
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: f35a9b0c-49ef-4cde-b06d-19d1543feb0b
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application
msc.type: authoredcontent
ms.openlocfilehash: ff480f7e8c2801fcb6a64c37d95e7e15467acde6
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837489"
---
# <a name="tutorial-learn-about-advanced-ef-scenarios-for-an-mvc-5-web-app"></a><span data-ttu-id="a6c41-103">教程：了解有关 EF 的 MVC 5 Web 应用的高级方案</span><span class="sxs-lookup"><span data-stu-id="a6c41-103">Tutorial: Learn about advanced EF Scenarios for an MVC 5 Web app</span></span>

<span data-ttu-id="a6c41-104">在上一教程中，您将实现每个层次结构一个表继承。</span><span class="sxs-lookup"><span data-stu-id="a6c41-104">In the previous tutorial you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="a6c41-105">本教程包括引入了几个主题都有可用于注意当超出使用 Entity Framework Code First 开发 ASP.NET web 应用程序的基础知识。</span><span class="sxs-lookup"><span data-stu-id="a6c41-105">This tutorial includes introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET web applications that use Entity Framework Code First.</span></span> <span data-ttu-id="a6c41-106">第一个几部分提供的分步说明，引导您完成代码，并使用 Visual Studio 来完成任务以下各节简要的概述，然后链接到有关详细信息的资源与引入几个主题。</span><span class="sxs-lookup"><span data-stu-id="a6c41-106">The first few sections have step-by-step instructions that walk you through the code and using Visual Studio to complete tasks The sections that follow introduce several topics with brief introductions followed by links to resources for more information.</span></span>

<span data-ttu-id="a6c41-107">对于大多数这些主题，您将使用已创建的页。</span><span class="sxs-lookup"><span data-stu-id="a6c41-107">For most of these topics, you'll work with pages that you already created.</span></span> <span data-ttu-id="a6c41-108">若要使用原始 SQL 执行批量更新将创建新页面的更新的数据库中的所有课程的可修读人数：</span><span class="sxs-lookup"><span data-stu-id="a6c41-108">To use raw SQL to do bulk updates you'll create a new page that updates the number of credits of all courses in the database:</span></span>

![Update_Course_Credits_initial_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

<span data-ttu-id="a6c41-110">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="a6c41-110">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a6c41-111">执行原始 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="a6c41-111">Perform raw SQL queries</span></span>
> * <span data-ttu-id="a6c41-112">执行非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="a6c41-112">Perform no-tracking queries</span></span>
> * <span data-ttu-id="a6c41-113">检查 SQL 查询发送到数据库</span><span class="sxs-lookup"><span data-stu-id="a6c41-113">Examine SQL queries sent to database</span></span>

<span data-ttu-id="a6c41-114">此外将了解如何：</span><span class="sxs-lookup"><span data-stu-id="a6c41-114">You also learn about:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a6c41-115">创建一个抽象层</span><span class="sxs-lookup"><span data-stu-id="a6c41-115">Creating an abstraction layer</span></span>
> * <span data-ttu-id="a6c41-116">代理类</span><span class="sxs-lookup"><span data-stu-id="a6c41-116">Proxy classes</span></span>
> * <span data-ttu-id="a6c41-117">自动脏值检测</span><span class="sxs-lookup"><span data-stu-id="a6c41-117">Automatic change detection</span></span>
> * <span data-ttu-id="a6c41-118">自动验证</span><span class="sxs-lookup"><span data-stu-id="a6c41-118">Automatic validation</span></span>
> * <span data-ttu-id="a6c41-119">Entity Framework Power Tools</span><span class="sxs-lookup"><span data-stu-id="a6c41-119">Entity Framework Power Tools</span></span>
> * <span data-ttu-id="a6c41-120">实体框架源代码</span><span class="sxs-lookup"><span data-stu-id="a6c41-120">Entity Framework source code</span></span>

## <a name="prerequisite"></a><span data-ttu-id="a6c41-121">必备组件</span><span class="sxs-lookup"><span data-stu-id="a6c41-121">Prerequisite</span></span>

* [<span data-ttu-id="a6c41-122">实现继承</span><span class="sxs-lookup"><span data-stu-id="a6c41-122">Implementing Inheritance</span></span>](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="perform-raw-sql-queries"></a><span data-ttu-id="a6c41-123">执行原始 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="a6c41-123">Perform raw SQL queries</span></span>

<span data-ttu-id="a6c41-124">实体框架 Code First API 包括使您能够直接向数据库传递 SQL 命令的方法。</span><span class="sxs-lookup"><span data-stu-id="a6c41-124">The Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="a6c41-125">有下列选项：</span><span class="sxs-lookup"><span data-stu-id="a6c41-125">You have the following options:</span></span>

- <span data-ttu-id="a6c41-126">使用[DbSet.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.dbset.sqlquery.aspx)返回实体类型的查询的方法。</span><span class="sxs-lookup"><span data-stu-id="a6c41-126">Use the [DbSet.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.dbset.sqlquery.aspx) method for queries that return entity types.</span></span> <span data-ttu-id="a6c41-127">返回的对象必须是预期的类型的`DbSet`对象，并且它们会自动跟踪的数据库上下文除非关闭跟踪。</span><span class="sxs-lookup"><span data-stu-id="a6c41-127">The returned objects must be of the type expected by the `DbSet` object, and they are automatically tracked by the database context unless you turn tracking off.</span></span> <span data-ttu-id="a6c41-128">(参见下一节[AsNoTracking](https://msdn.microsoft.com/library/system.data.entity.dbextensions.asnotracking.aspx)方法。)</span><span class="sxs-lookup"><span data-stu-id="a6c41-128">(See the following section about the [AsNoTracking](https://msdn.microsoft.com/library/system.data.entity.dbextensions.asnotracking.aspx) method.)</span></span>
- <span data-ttu-id="a6c41-129">使用[Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery.aspx)方法的返回类型不是实体的查询。</span><span class="sxs-lookup"><span data-stu-id="a6c41-129">Use the [Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery.aspx) method for queries that return types that aren't entities.</span></span> <span data-ttu-id="a6c41-130">数据库上下文不会跟踪返回的数据，即使你使用该方法来检索实体类型也是如此。</span><span class="sxs-lookup"><span data-stu-id="a6c41-130">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>
- <span data-ttu-id="a6c41-131">使用[Database.ExecuteSqlCommand](https://msdn.microsoft.com/library/gg679456.aspx)非查询命令。</span><span class="sxs-lookup"><span data-stu-id="a6c41-131">Use the [Database.ExecuteSqlCommand](https://msdn.microsoft.com/library/gg679456.aspx) for non-query commands.</span></span>

<span data-ttu-id="a6c41-132">使用 Entity Framework 的优点之一是它可避免你编写跟数据库过于耦合的代码</span><span class="sxs-lookup"><span data-stu-id="a6c41-132">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="a6c41-133">它会自动生成 SQL 查询和命令，使得你无需自行编写。</span><span class="sxs-lookup"><span data-stu-id="a6c41-133">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="a6c41-134">当您需要运行的手动创建，特定 SQL 查询时有一些特殊情况，但这些方法使你能够处理此类异常。</span><span class="sxs-lookup"><span data-stu-id="a6c41-134">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created, and these methods make it possible for you to handle such exceptions.</span></span>

<span data-ttu-id="a6c41-135">在 Web 应用程序中执行 SQL 命令时，请务必采取预防措施来保护站点免受 SQL 注入攻击。</span><span class="sxs-lookup"><span data-stu-id="a6c41-135">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="a6c41-136">一种方法是使用参数化查询，确保不会将网页提交的字符串视为 SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="a6c41-136">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="a6c41-137">在本教程中，将用户输入集成到查询中时会使用参数化查询。</span><span class="sxs-lookup"><span data-stu-id="a6c41-137">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

### <a name="calling-a-query-that-returns-entities"></a><span data-ttu-id="a6c41-138">调用一个查询返回实体</span><span class="sxs-lookup"><span data-stu-id="a6c41-138">Calling a Query that Returns Entities</span></span>

<span data-ttu-id="a6c41-139">[DbSet&lt;TEntity&gt; ](https://msdn.microsoft.com/library/gg696460.aspx)类提供了可用于执行查询的返回类型的实体的方法`TEntity`。</span><span class="sxs-lookup"><span data-stu-id="a6c41-139">The [DbSet&lt;TEntity&gt;](https://msdn.microsoft.com/library/gg696460.aspx) class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="a6c41-140">若要查看细节，你将更改中的代码`Details`方法的`Department`控制器。</span><span class="sxs-lookup"><span data-stu-id="a6c41-140">To see how this works you'll change the code in the `Details` method of the `Department` controller.</span></span>

<span data-ttu-id="a6c41-141">在*DepartmentController.cs*，在`Details`方法中，替换`db.Departments.FindAsync`使用的方法调用`db.Departments.SqlQuery`方法调用，如以下突出显示的代码中所示：</span><span class="sxs-lookup"><span data-stu-id="a6c41-141">In *DepartmentController.cs*, in the `Details` method, replace the `db.Departments.FindAsync` method call with a `db.Departments.SqlQuery` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample1.cs?highlight=8-14)]

<span data-ttu-id="a6c41-142">为了验证新代码是否工作正常，请选择**Department**选项卡，然后点击某个部门的**Detail**。</span><span class="sxs-lookup"><span data-stu-id="a6c41-142">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span> <span data-ttu-id="a6c41-143">请确保所有数据将按预期方式显示。</span><span class="sxs-lookup"><span data-stu-id="a6c41-143">Make sure all of the data displays as expected.</span></span>

### <a name="calling-a-query-that-returns-other-types-of-objects"></a><span data-ttu-id="a6c41-144">调用一个查询返回其他类型的对象</span><span class="sxs-lookup"><span data-stu-id="a6c41-144">Calling a Query that Returns Other Types of Objects</span></span>

<span data-ttu-id="a6c41-145">之前你在“关于”页面创建了一个学生统计信息网格，显示每个注册日期的学生数量。</span><span class="sxs-lookup"><span data-stu-id="a6c41-145">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="a6c41-146">此代码*HomeController.cs*使用 LINQ:</span><span class="sxs-lookup"><span data-stu-id="a6c41-146">The code that does this in *HomeController.cs* uses LINQ:</span></span>

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample2.cs)]

<span data-ttu-id="a6c41-147">假设你想要编写检索此数据直接在 SQL，而不是使用 LINQ 中的代码。</span><span class="sxs-lookup"><span data-stu-id="a6c41-147">Suppose you want to write the code that retrieves this data directly in SQL rather than using LINQ.</span></span> <span data-ttu-id="a6c41-148">若要执行此操作需要运行的查询返回实体对象以外的内容，这意味着您需要使用[Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery(v=VS.103).aspx)方法。</span><span class="sxs-lookup"><span data-stu-id="a6c41-148">To do that you need to run a query that returns something other than entity objects, which means you need to use the [Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery(v=VS.103).aspx) method.</span></span>

<span data-ttu-id="a6c41-149">在中*HomeController.cs*，将 LINQ 语句中的为`About`方法使用 SQL 语句，如以下突出显示的代码中所示：</span><span class="sxs-lookup"><span data-stu-id="a6c41-149">In *HomeController.cs*, replace the LINQ statement in the `About` method with a SQL statement, as shown in the following highlighted code:</span></span>

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample3.cs?highlight=3-18)]

<span data-ttu-id="a6c41-150">运行关于页面。</span><span class="sxs-lookup"><span data-stu-id="a6c41-150">Run the About page.</span></span> <span data-ttu-id="a6c41-151">验证，它将显示它以前的相同的数据。</span><span class="sxs-lookup"><span data-stu-id="a6c41-151">Verify that it displays the same data it did before.</span></span>

### <a name="calling-an-update-query"></a><span data-ttu-id="a6c41-152">调用更新查询</span><span class="sxs-lookup"><span data-stu-id="a6c41-152">Calling an Update Query</span></span>

<span data-ttu-id="a6c41-153">假设 Contoso 大学管理员想要能够在数据库中，如更改的每个课程的可修读人数执行大容量更改。</span><span class="sxs-lookup"><span data-stu-id="a6c41-153">Suppose Contoso University administrators want to be able to perform bulk changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="a6c41-154">如果该大学有大量的课程，检索所有实体并单独更改会降低效率。</span><span class="sxs-lookup"><span data-stu-id="a6c41-154">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="a6c41-155">在本部分中，你将实现一个网页，使用户能够指定一个参数，通过要更改所有课程的可修读人数和将通过执行 SQL 中进行更改`UPDATE`语句。</span><span class="sxs-lookup"><span data-stu-id="a6c41-155">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL `UPDATE` statement.</span></span> 

<span data-ttu-id="a6c41-156">在中*CourseContoller.cs*，添加`UpdateCourseCredits`方法`HttpGet`和`HttpPost`:</span><span class="sxs-lookup"><span data-stu-id="a6c41-156">In *CourseContoller.cs*, add `UpdateCourseCredits` methods for `HttpGet` and `HttpPost`:</span></span>

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample4.cs)]

<span data-ttu-id="a6c41-157">当控制器处理`HttpGet`请求，未返回任何内容中`ViewBag.RowsAffected`变量，并查看显示的空文本框和一个提交按钮。</span><span class="sxs-lookup"><span data-stu-id="a6c41-157">When the controller processes an `HttpGet` request, nothing is returned in the `ViewBag.RowsAffected` variable, and the view displays an empty text box and a submit button.</span></span>

<span data-ttu-id="a6c41-158">当**更新**单击按钮时，`HttpPost`调用方法时，和`multiplier`在文本框中输入的值。</span><span class="sxs-lookup"><span data-stu-id="a6c41-158">When the **Update** button is clicked, the `HttpPost` method is called, and `multiplier` has the value entered in the text box.</span></span> <span data-ttu-id="a6c41-159">代码然后执行 SQL 语句更新课程，并返回受影响的行数中的视图`ViewBag.RowsAffected`变量。</span><span class="sxs-lookup"><span data-stu-id="a6c41-159">The code then executes the SQL that updates courses and returns the number of affected rows to the view in the `ViewBag.RowsAffected` variable.</span></span> <span data-ttu-id="a6c41-160">当视图在该变量中获取一个值时，它显示而不是在文本框中更新的行数，并提交按钮。</span><span class="sxs-lookup"><span data-stu-id="a6c41-160">When the view gets a value in that variable, it displays the number of rows updated instead of the text box and submit button.</span></span>

<span data-ttu-id="a6c41-161">在中*CourseController.cs*，右键单击其中一个`UpdateCourseCredits`方法，并单击**添加视图**。</span><span class="sxs-lookup"><span data-stu-id="a6c41-161">In *CourseController.cs*, right-click one of the `UpdateCourseCredits` methods, and then click **Add View**.</span></span> <span data-ttu-id="a6c41-162">**添加视图**此时将显示对话框。</span><span class="sxs-lookup"><span data-stu-id="a6c41-162">The **Add View** dialog appears.</span></span> <span data-ttu-id="a6c41-163">保留默认值并选择**添加**。</span><span class="sxs-lookup"><span data-stu-id="a6c41-163">Leave the defaults and select **Add**.</span></span>

<span data-ttu-id="a6c41-164">在中*Views\Course\UpdateCourseCredits.cshtml*，模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="a6c41-164">In *Views\Course\UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample5.cshtml)]

<span data-ttu-id="a6c41-165">通过选择**Courses**选项卡运行`UpdateCourseCredits`方法，然后在浏览器地址栏中 URL 的末尾添加"/ UpdateCourseCredits"到 (例如： `http://localhost:50205/Course/UpdateCourseCredits`)。</span><span class="sxs-lookup"><span data-stu-id="a6c41-165">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:50205/Course/UpdateCourseCredits`).</span></span> <span data-ttu-id="a6c41-166">在文本框中输入数字：</span><span class="sxs-lookup"><span data-stu-id="a6c41-166">Enter a number in the text box:</span></span>

![Update_Course_Credits_initial_page_with_2_entered](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

<span data-ttu-id="a6c41-168">单击**Update**。</span><span class="sxs-lookup"><span data-stu-id="a6c41-168">Click **Update**.</span></span> <span data-ttu-id="a6c41-169">请参阅受影响的行数。</span><span class="sxs-lookup"><span data-stu-id="a6c41-169">You see the number of rows affected.</span></span>

<span data-ttu-id="a6c41-170">单击**Back To List**可以看到课程列表，其中可修读人数已经替换成修改后的数字。</span><span class="sxs-lookup"><span data-stu-id="a6c41-170">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="a6c41-171">有关原始 SQL 查询的详细信息，请参阅[原始 SQL 查询](https://msdn.microsoft.com/data/jj592907)MSDN 上。</span><span class="sxs-lookup"><span data-stu-id="a6c41-171">For more information about raw SQL queries, see [Raw SQL Queries](https://msdn.microsoft.com/data/jj592907) on MSDN.</span></span>

## <a name="no-tracking-queries"></a><span data-ttu-id="a6c41-172">非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="a6c41-172">No-tracking queries</span></span>

<span data-ttu-id="a6c41-173">当数据库上下文检索表行并创建表示它们的实体对象时，默认情况下，它会跟踪内存中的实体是否与数据库中的内容同步。</span><span class="sxs-lookup"><span data-stu-id="a6c41-173">When a database context retrieves table rows and creates entity objects that represent them, by default it keeps track of whether the entities in memory are in sync with what's in the database.</span></span> <span data-ttu-id="a6c41-174">更新实体时，内存中的数据充当缓存并使用该数据。</span><span class="sxs-lookup"><span data-stu-id="a6c41-174">The data in memory acts as a cache and is used when you update an entity.</span></span> <span data-ttu-id="a6c41-175">在 Web 应用程序中，此缓存通常是不必要的，因为上下文实例通常生存期较短（创建新的实例并用于处理每个请求），并且通常在再次使用该实体之前处理读取实体的上下文。</span><span class="sxs-lookup"><span data-stu-id="a6c41-175">This caching is often unnecessary in a web application because context instances are typically short-lived (a new one is created and disposed for each request) and the context that reads an entity is typically disposed before that entity is used again.</span></span>

<span data-ttu-id="a6c41-176">可以使用禁用的实体对象在内存中的跟踪[AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx)方法。</span><span class="sxs-lookup"><span data-stu-id="a6c41-176">You can disable tracking of entity objects in memory by using the [AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx) method.</span></span> <span data-ttu-id="a6c41-177">可能想要执行的典型方案包括以下操作：</span><span class="sxs-lookup"><span data-stu-id="a6c41-177">Typical scenarios in which you might want to do that include the following:</span></span>

- <span data-ttu-id="a6c41-178">查询将检索此类大型的数据量关闭跟踪可能会显著提高性能。</span><span class="sxs-lookup"><span data-stu-id="a6c41-178">A query retrieves such a large volume of data that turning off tracking might noticeably enhance performance.</span></span>
- <span data-ttu-id="a6c41-179">你想要附加一个实体来更新它，但前面要检索同一实体用于其他目的。</span><span class="sxs-lookup"><span data-stu-id="a6c41-179">You want to attach an entity in order to update it, but you earlier retrieved the same entity for a different purpose.</span></span> <span data-ttu-id="a6c41-180">由于数据库上下文已跟踪了该实体，因此无法附加要更改的实体。</span><span class="sxs-lookup"><span data-stu-id="a6c41-180">Because the entity is already being tracked by the database context, you can't attach the entity that you want to change.</span></span> <span data-ttu-id="a6c41-181">若要处理这种情况的一种方法是使用`AsNoTracking`与以前的查询选项。</span><span class="sxs-lookup"><span data-stu-id="a6c41-181">One way to handle this situation is to use the `AsNoTracking` option with the earlier query.</span></span>

<span data-ttu-id="a6c41-182">有关示例，演示如何使用[AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx)方法，请参阅[本教程的早期版本](../../older-versions/getting-started-with-ef-5-using-mvc-4/advanced-entity-framework-scenarios-for-an-mvc-web-application.md)。</span><span class="sxs-lookup"><span data-stu-id="a6c41-182">For an example that demonstrates how to use the [AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx) method, see [the earlier version of this tutorial](../../older-versions/getting-started-with-ef-5-using-mvc-4/advanced-entity-framework-scenarios-for-an-mvc-web-application.md).</span></span> <span data-ttu-id="a6c41-183">在本教程的此版本不会修改标志实体上设置模型联编程序创建在编辑方法中，因此它不需要`AsNoTracking`。</span><span class="sxs-lookup"><span data-stu-id="a6c41-183">This version of the tutorial doesn't set the Modified flag on a model-binder-created entity in the Edit method, so it doesn't need `AsNoTracking`.</span></span>

## <a name="examine-sql-sent-to-database"></a><span data-ttu-id="a6c41-184">检查发送到数据库的 SQL</span><span class="sxs-lookup"><span data-stu-id="a6c41-184">Examine SQL sent to database</span></span>

<span data-ttu-id="a6c41-185">有时能够以查看发送到数据库的实际 SQL 查询对于开发者来说是很有用的。</span><span class="sxs-lookup"><span data-stu-id="a6c41-185">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="a6c41-186">在前面的教程中您已了解如何执行该操作在侦听器代码;现在您会看到无需编写侦听器代码执行此操作的一些方法。</span><span class="sxs-lookup"><span data-stu-id="a6c41-186">In an earlier tutorial you saw how to do that in interceptor code; now you'll see some ways to do it without writing interceptor code.</span></span> <span data-ttu-id="a6c41-187">若要试用此项，将看看简单查询并再看会发生什么情况向其添加此类预先加载、 筛选和排序选项。</span><span class="sxs-lookup"><span data-stu-id="a6c41-187">To try this out, you'll look at a simple query and then look at what happens to it as you add options such eager loading, filtering, and sorting.</span></span>

<span data-ttu-id="a6c41-188">在中*控制器/CourseController*，将为`Index`用下面的代码，若要暂时停止预先加载的方法：</span><span class="sxs-lookup"><span data-stu-id="a6c41-188">In *Controllers/CourseController*, replace the `Index` method with the following code, in order to temporarily stop eager loading:</span></span>

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample6.cs)]

<span data-ttu-id="a6c41-189">现在上设置断点`return`语句 (F9 在该行光标)。</span><span class="sxs-lookup"><span data-stu-id="a6c41-189">Now set a breakpoint on the `return` statement (F9 with the cursor on that line).</span></span> <span data-ttu-id="a6c41-190">按**F5**以在调试模式下运行该项目并选择课程索引页。</span><span class="sxs-lookup"><span data-stu-id="a6c41-190">Press **F5** to run the project in debug mode, and select the Course Index page.</span></span> <span data-ttu-id="a6c41-191">当代码到达该断点时，检查`sql`变量。</span><span class="sxs-lookup"><span data-stu-id="a6c41-191">When the code reaches the breakpoint, examine the `sql` variable.</span></span> <span data-ttu-id="a6c41-192">可以看到发送到 SQL Server 的查询。</span><span class="sxs-lookup"><span data-stu-id="a6c41-192">You see the query that's sent to SQL Server.</span></span> <span data-ttu-id="a6c41-193">它是一个简单`Select`语句。</span><span class="sxs-lookup"><span data-stu-id="a6c41-193">It's a simple `Select` statement.</span></span>

[!code-json[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample7.json)]

<span data-ttu-id="a6c41-194">单击以查看查询中的放大镜**文本可视化工具**。</span><span class="sxs-lookup"><span data-stu-id="a6c41-194">Click the magnifying glass to see the query in the **Text Visualizer**.</span></span>

![](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image10.png)

<span data-ttu-id="a6c41-195">现在将向课程索引页面添加下拉列表，以便用户可以筛选为特定部门。</span><span class="sxs-lookup"><span data-stu-id="a6c41-195">Now you'll add a drop-down list to the Courses Index page so that users can filter for a particular department.</span></span> <span data-ttu-id="a6c41-196">您将对课程进行排序的标题，并且你将指定预先加载`Department`导航属性。</span><span class="sxs-lookup"><span data-stu-id="a6c41-196">You'll sort the courses by title, and you'll specify eager loading for the `Department` navigation property.</span></span>

<span data-ttu-id="a6c41-197">在中*CourseController.cs*，将为`Index`方法使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="a6c41-197">In *CourseController.cs*, replace the `Index` method with the following code:</span></span>

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample8.cs)]

<span data-ttu-id="a6c41-198">在还原该断点`return`语句。</span><span class="sxs-lookup"><span data-stu-id="a6c41-198">Restore the breakpoint on the `return` statement.</span></span>

<span data-ttu-id="a6c41-199">该方法接收的下拉列表中所选的值`SelectedDepartment`参数。</span><span class="sxs-lookup"><span data-stu-id="a6c41-199">The method receives the selected value of the drop-down list in the `SelectedDepartment` parameter.</span></span> <span data-ttu-id="a6c41-200">如果未选择任何项，则此参数将为 null。</span><span class="sxs-lookup"><span data-stu-id="a6c41-200">If nothing is selected, this parameter will be null.</span></span>

<span data-ttu-id="a6c41-201">一个`SelectList`集合，其中包含所有部门的下拉列表将传递给视图。</span><span class="sxs-lookup"><span data-stu-id="a6c41-201">A `SelectList` collection containing all departments is passed to the view for the drop-down list.</span></span> <span data-ttu-id="a6c41-202">参数传递给`SelectList`构造函数指定值字段名称、 文本字段名称和所选的项。</span><span class="sxs-lookup"><span data-stu-id="a6c41-202">The parameters passed to the `SelectList` constructor specify the value field name, the text field name, and the selected item.</span></span>

<span data-ttu-id="a6c41-203">有关`Get`方法`Course`存储库中，代码将指定的筛选器表达式、 排序顺序和预先加载`Department`导航属性。</span><span class="sxs-lookup"><span data-stu-id="a6c41-203">For the `Get` method of the `Course` repository, the code specifies a filter expression, a sort order, and eager loading for the `Department` navigation property.</span></span> <span data-ttu-id="a6c41-204">筛选器表达式始终返回`true`如果未选择下拉列表中 (即，`SelectedDepartment`为 null)。</span><span class="sxs-lookup"><span data-stu-id="a6c41-204">The filter expression always returns `true` if nothing is selected in the drop-down list (that is, `SelectedDepartment` is null).</span></span>

<span data-ttu-id="a6c41-205">在中*Views\Course\Index.cshtml*，打开之前立即`table`标记中，添加以下代码以创建下拉列表和一个提交按钮：</span><span class="sxs-lookup"><span data-stu-id="a6c41-205">In *Views\Course\Index.cshtml*, immediately before the opening `table` tag, add the following code to create the drop-down list and a submit button:</span></span>

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample9.cshtml)]

<span data-ttu-id="a6c41-206">断点仍然设置，运行课程索引页。</span><span class="sxs-lookup"><span data-stu-id="a6c41-206">With the breakpoint still set, run the Course Index page.</span></span> <span data-ttu-id="a6c41-207">继续操作，代码命中断点的行，第一次，以便将在浏览器中显示的页。</span><span class="sxs-lookup"><span data-stu-id="a6c41-207">Continue through the first times that the code hits a breakpoint, so that the page is displayed in the browser.</span></span> <span data-ttu-id="a6c41-208">从下拉列表中选择一个部门，然后单击**筛选器**。</span><span class="sxs-lookup"><span data-stu-id="a6c41-208">Select a department from the drop-down list and click **Filter**.</span></span>

<span data-ttu-id="a6c41-209">这一次第一个断点将在下拉列表的部门查询。</span><span class="sxs-lookup"><span data-stu-id="a6c41-209">This time the first breakpoint will be for the departments query for the drop-down list.</span></span> <span data-ttu-id="a6c41-210">跳过该步骤，并查看`query`变量下一次代码达到断点才能看到什么`Course`查询现在如下所示。</span><span class="sxs-lookup"><span data-stu-id="a6c41-210">Skip that and view the `query` variable the next time the code reaches the breakpoint in order to see what the `Course` query now looks like.</span></span> <span data-ttu-id="a6c41-211">你将看到类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="a6c41-211">You'll see something like the following:</span></span>

[!code-sql[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample10.sql)]

<span data-ttu-id="a6c41-212">您可以看到查询现`JOIN`加载的查询`Department`数据以及`Course`数据，并且必须包含`WHERE`子句。</span><span class="sxs-lookup"><span data-stu-id="a6c41-212">You can see that the query is now a `JOIN` query that loads `Department` data along with the `Course` data, and that it includes a `WHERE` clause.</span></span>

<span data-ttu-id="a6c41-213">删除`var sql = courses.ToString()`行。</span><span class="sxs-lookup"><span data-stu-id="a6c41-213">Remove the `var sql = courses.ToString()` line.</span></span>

## <a name="create-an-abstraction-layer"></a><span data-ttu-id="a6c41-214">创建一个抽象层</span><span class="sxs-lookup"><span data-stu-id="a6c41-214">Create an abstraction layer</span></span>

<span data-ttu-id="a6c41-215">许多开发人员编写代码实现存储库和工作模式单元以作为使用 Entity Framework 代码的包装器。</span><span class="sxs-lookup"><span data-stu-id="a6c41-215">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="a6c41-216">这些模式用于在应用程序的数据访问层和业务逻辑层之间创建抽象层。</span><span class="sxs-lookup"><span data-stu-id="a6c41-216">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="a6c41-217">实现这些模式可让你的应用程序对数据存储介质的更改不敏感，而且很容易进行自动化单元测试和进行测试驱动开发 (TDD)。</span><span class="sxs-lookup"><span data-stu-id="a6c41-217">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="a6c41-218">但是，编写附加代码以实现这些模式并不总是有几个原因使用 EF 的应用程序的最佳选择：</span><span class="sxs-lookup"><span data-stu-id="a6c41-218">However, writing additional code to implement these patterns is not always the best choice for applications that use EF, for several reasons:</span></span>

- <span data-ttu-id="a6c41-219">EF 上下文类可以为使用 EF 的数据库更新充当工作单位类。</span><span class="sxs-lookup"><span data-stu-id="a6c41-219">The EF context class itself insulates your code from data-store-specific code.</span></span>
- <span data-ttu-id="a6c41-220">对于使用 EF 进行的数据库更新，EF 上下文类可充当工作单元类。</span><span class="sxs-lookup"><span data-stu-id="a6c41-220">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>
- <span data-ttu-id="a6c41-221">Entity Framework 6 中引入的功能使其更轻松地实现 TDD，而无需编写代码存储库。</span><span class="sxs-lookup"><span data-stu-id="a6c41-221">Features introduced in Entity Framework 6 make it easier to implement TDD without writing repository code.</span></span>

<span data-ttu-id="a6c41-222">有关如何实现存储库和工作单元模式的详细信息，请参阅[本系列教程的 Entity Framework 5 版本](../../older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application.md)。</span><span class="sxs-lookup"><span data-stu-id="a6c41-222">For more information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](../../older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application.md).</span></span> <span data-ttu-id="a6c41-223">方法在 Entity Framework 6 中实现 TDD 的信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="a6c41-223">For information about ways to implement TDD in Entity Framework 6, see the following resources:</span></span>

- [<span data-ttu-id="a6c41-224">如何 EF6 通过模拟 Dbset 更轻松地</span><span class="sxs-lookup"><span data-stu-id="a6c41-224">How EF6 Enables Mocking DbSets more easily</span></span>](http://thedatafarm.com/data-access/how-ef6-enables-mocking-dbsets-more-easily/)
- [<span data-ttu-id="a6c41-225">使用模拟框架进行测试</span><span class="sxs-lookup"><span data-stu-id="a6c41-225">Testing with a mocking framework</span></span>](https://msdn.microsoft.com/data/dn314429)
- [<span data-ttu-id="a6c41-226">使用你自己的 test double 进行测试</span><span class="sxs-lookup"><span data-stu-id="a6c41-226">Testing with your own test doubles</span></span>](https://msdn.microsoft.com/data/dn314431)

<a id="proxies"></a>

## <a name="proxy-classes"></a><span data-ttu-id="a6c41-227">代理类</span><span class="sxs-lookup"><span data-stu-id="a6c41-227">Proxy classes</span></span>

<span data-ttu-id="a6c41-228">当 Entity Framework 创建实体实例 （例如，当执行查询时） 时，它通常创建其作为动态生成的派生类型的代理的实体的实例。</span><span class="sxs-lookup"><span data-stu-id="a6c41-228">When the Entity Framework creates entity instances (for example, when you execute a query), it often creates them as instances of a dynamically generated derived type that acts as a proxy for the entity.</span></span> <span data-ttu-id="a6c41-229">有关示例，请参阅以下两个调试程序映像。</span><span class="sxs-lookup"><span data-stu-id="a6c41-229">For example, see the following two debugger images.</span></span> <span data-ttu-id="a6c41-230">在第一个图中，请参阅该`student`变量是预期`Student`类型实例化实体后立即。</span><span class="sxs-lookup"><span data-stu-id="a6c41-230">In the first image, you see that the `student` variable is the expected `Student` type immediately after you instantiate the entity.</span></span> <span data-ttu-id="a6c41-231">在第二个图中，EF 已用于从数据库读取 student 实体后，请参阅代理类。</span><span class="sxs-lookup"><span data-stu-id="a6c41-231">In the second image, after EF has been used to read a student entity from the database, you see the proxy class.</span></span>

![代理类之前](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image12.png)

![在代理类](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image13.png)

<span data-ttu-id="a6c41-234">此代理类重写要插入挂钩，以便访问属性时自动执行的操作的实体的某些虚拟属性。</span><span class="sxs-lookup"><span data-stu-id="a6c41-234">This proxy class overrides some virtual properties of the entity to insert hooks for performing actions automatically when the property is accessed.</span></span> <span data-ttu-id="a6c41-235">有关使用此机制的一个函数是延迟加载。</span><span class="sxs-lookup"><span data-stu-id="a6c41-235">One function this mechanism is used for is lazy loading.</span></span>

<span data-ttu-id="a6c41-236">大多数情况下无需注意此使用代理服务器，但有例外情况：</span><span class="sxs-lookup"><span data-stu-id="a6c41-236">Most of the time you don't need to be aware of this use of proxies, but there are exceptions:</span></span>

- <span data-ttu-id="a6c41-237">在某些情况下您可能想要阻止实体框架创建代理实例。</span><span class="sxs-lookup"><span data-stu-id="a6c41-237">In some scenarios you might want to prevent the Entity Framework from creating proxy instances.</span></span> <span data-ttu-id="a6c41-238">例如，要序列化实体时您通常希望 POCO 类，不使用代理类。</span><span class="sxs-lookup"><span data-stu-id="a6c41-238">For example, when you're serializing entities you generally want the POCO classes, not the proxy classes.</span></span> <span data-ttu-id="a6c41-239">若要避免序列化问题的一种方法是要序列化数据传输对象 (Dto)，而不是实体对象，如中所示[使用实体框架使用 Web API](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-1.md)教程。</span><span class="sxs-lookup"><span data-stu-id="a6c41-239">One way to avoid serialization problems is to serialize data transfer objects (DTOs) instead of entity objects, as shown in the [Using Web API with Entity Framework](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-1.md) tutorial.</span></span> <span data-ttu-id="a6c41-240">另一种方法是向[禁用代理创建](https://msdn.microsoft.com/data/jj592886.aspx)。</span><span class="sxs-lookup"><span data-stu-id="a6c41-240">Another way is to [disable proxy creation](https://msdn.microsoft.com/data/jj592886.aspx).</span></span>
- <span data-ttu-id="a6c41-241">当您实例化实体类使用`new`运算符，别让代理实例。</span><span class="sxs-lookup"><span data-stu-id="a6c41-241">When you instantiate an entity class using the `new` operator, you don't get a proxy instance.</span></span> <span data-ttu-id="a6c41-242">这意味着不会功能，如延迟加载和自动更改跟踪。</span><span class="sxs-lookup"><span data-stu-id="a6c41-242">This means you don't get functionality such as lazy loading and automatic change tracking.</span></span> <span data-ttu-id="a6c41-243">这通常是好了;您通常不需要延迟加载，因为您将创建新的实体不在数据库中，且通常无需更改跟踪，如果您显式标记为实体`Added`。</span><span class="sxs-lookup"><span data-stu-id="a6c41-243">This is typically okay; you generally don't need lazy loading, because you're creating a new entity that isn't in the database, and you generally don't need change tracking if you're explicitly marking the entity as `Added`.</span></span> <span data-ttu-id="a6c41-244">但是，如果您需要延迟加载，并且你需要更改跟踪，您可以创建新实体实例与使用的代理[创建](https://msdn.microsoft.com/library/gg679504.aspx)方法的`DbSet`类。</span><span class="sxs-lookup"><span data-stu-id="a6c41-244">However, if you do need lazy loading and you need change tracking, you can create new entity instances with proxies using the [Create](https://msdn.microsoft.com/library/gg679504.aspx) method of the `DbSet` class.</span></span>
- <span data-ttu-id="a6c41-245">你可能想要从代理类型中获取的实际实体类型。</span><span class="sxs-lookup"><span data-stu-id="a6c41-245">You might want to get an actual entity type from a proxy type.</span></span> <span data-ttu-id="a6c41-246">可以使用[GetObjectType](https://msdn.microsoft.com/library/system.data.objects.objectcontext.getobjecttype.aspx)方法的`ObjectContext`类，以获取代理类型实例的实际实体类型。</span><span class="sxs-lookup"><span data-stu-id="a6c41-246">You can use the [GetObjectType](https://msdn.microsoft.com/library/system.data.objects.objectcontext.getobjecttype.aspx) method of the `ObjectContext` class to get the actual entity type of a proxy type instance.</span></span>

<span data-ttu-id="a6c41-247">有关详细信息，请参阅[使用代理](https://msdn.microsoft.com/data/JJ592886.aspx)MSDN 上。</span><span class="sxs-lookup"><span data-stu-id="a6c41-247">For more information, see [Working with Proxies](https://msdn.microsoft.com/data/JJ592886.aspx) on MSDN.</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="a6c41-248">自动脏值检测</span><span class="sxs-lookup"><span data-stu-id="a6c41-248">Automatic change detection</span></span>

<span data-ttu-id="a6c41-249">Entity Framework 通过比较的实体的当前值与原始值来判断更改实体的方式 （因此需要发送更新到数据库）。</span><span class="sxs-lookup"><span data-stu-id="a6c41-249">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="a6c41-250">查询或附加该实体时会存储的原始值。</span><span class="sxs-lookup"><span data-stu-id="a6c41-250">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="a6c41-251">如下方法会导致自动脏值：</span><span class="sxs-lookup"><span data-stu-id="a6c41-251">Some of the methods that cause automatic change detection are the following:</span></span>

- `DbSet.Find`
- `DbSet.Local`
- `DbSet.Remove`
- `DbSet.Add`
- `DbSet.Attach`
- `DbContext.SaveChanges`
- `DbContext.GetValidationErrors`
- `DbContext.Entry`
- `DbChangeTracker.Entries`

<span data-ttu-id="a6c41-252">如果正在跟踪大量实体，并且你调用下列方法之一很多时候在循环中，您可能会显著的性能改进，通过暂时关闭自动更改检测使用[AutoDetectChangesEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.autodetectchangesenabled.aspx)属性。</span><span class="sxs-lookup"><span data-stu-id="a6c41-252">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the [AutoDetectChangesEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.autodetectchangesenabled.aspx) property.</span></span> <span data-ttu-id="a6c41-253">有关详细信息，请参阅[自动检测更改](https://msdn.microsoft.com/data/jj556205)MSDN 上。</span><span class="sxs-lookup"><span data-stu-id="a6c41-253">For more information, see [Automatically Detecting Changes](https://msdn.microsoft.com/data/jj556205) on MSDN.</span></span>

## <a name="automatic-validation"></a><span data-ttu-id="a6c41-254">自动验证</span><span class="sxs-lookup"><span data-stu-id="a6c41-254">Automatic validation</span></span>

<span data-ttu-id="a6c41-255">当您调用`SaveChanges`方法中，默认情况下，实体框架中已更改的所有实体的所有属性的数据之前验证更新数据库。</span><span class="sxs-lookup"><span data-stu-id="a6c41-255">When you call the `SaveChanges` method, by default the Entity Framework validates the data in all properties of all changed entities before updating the database.</span></span> <span data-ttu-id="a6c41-256">如果你已更新大量实体和已验证了数据，这项工作是不必要和无法使保存的过程所做的更改通过暂时关闭验证需要更少的时间。</span><span class="sxs-lookup"><span data-stu-id="a6c41-256">If you've updated a large number of entities and you've already validated the data, this work is unnecessary and you could make the process of saving the changes take less time by temporarily turning off validation.</span></span> <span data-ttu-id="a6c41-257">您可以执行操作，使用[ValidateOnSaveEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.validateonsaveenabled.aspx)属性。</span><span class="sxs-lookup"><span data-stu-id="a6c41-257">You can do that using the [ValidateOnSaveEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.validateonsaveenabled.aspx) property.</span></span> <span data-ttu-id="a6c41-258">有关详细信息，请参阅[验证](https://msdn.microsoft.com/data/gg193959)MSDN 上。</span><span class="sxs-lookup"><span data-stu-id="a6c41-258">For more information, see [Validation](https://msdn.microsoft.com/data/gg193959) on MSDN.</span></span>

## <a name="entity-framework-power-tools"></a><span data-ttu-id="a6c41-259">Entity Framework Power Tools</span><span class="sxs-lookup"><span data-stu-id="a6c41-259">Entity Framework Power Tools</span></span>

<span data-ttu-id="a6c41-260">[Entity Framework Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) Visual Studio 外接程序，用于创建数据模型关系图显示在这些教程。</span><span class="sxs-lookup"><span data-stu-id="a6c41-260">[Entity Framework Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) is a Visual Studio add-in that was used to create the data model diagrams shown in these tutorials.</span></span> <span data-ttu-id="a6c41-261">这些工具还可以执行如生成实体类基于现有数据库中的表，以便您可以使用数据库使用 Code First 其他函数。</span><span class="sxs-lookup"><span data-stu-id="a6c41-261">The tools can also do other function such as generate entity classes based on the tables in an existing database so that you can use the database with Code First.</span></span> <span data-ttu-id="a6c41-262">安装工具后，在上下文菜单中显示一些附加选项。</span><span class="sxs-lookup"><span data-stu-id="a6c41-262">After you install the tools, some additional options appear in context menus.</span></span> <span data-ttu-id="a6c41-263">例如，当您右键单击您的上下文类中**解决方案资源管理器**，您看到并**Entity Framework**选项。</span><span class="sxs-lookup"><span data-stu-id="a6c41-263">For example, when you right-click your context class in **Solution Explorer**, you see and **Entity Framework** option.</span></span> <span data-ttu-id="a6c41-264">这使您能够生成关系图。</span><span class="sxs-lookup"><span data-stu-id="a6c41-264">This gives you the ability to generate a diagram.</span></span> <span data-ttu-id="a6c41-265">在使用 Code First 时无法更改关系图中中的数据模型，但可以来回移动操作，以使其更易于理解。</span><span class="sxs-lookup"><span data-stu-id="a6c41-265">When you're using Code First you can't change the data model in the diagram, but you can move things around to make it easier to understand.</span></span>

![EF 关系图](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image15.png)

## <a name="entity-framework-source-code"></a><span data-ttu-id="a6c41-267">实体框架源代码</span><span class="sxs-lookup"><span data-stu-id="a6c41-267">Entity Framework source code</span></span>

<span data-ttu-id="a6c41-268">Entity Framework 6 的源代码位于[GitHub](https://github.com/aspnet/EntityFramework6)。</span><span class="sxs-lookup"><span data-stu-id="a6c41-268">The source code for Entity Framework 6 is available at [GitHub](https://github.com/aspnet/EntityFramework6).</span></span> <span data-ttu-id="a6c41-269">可以报告 bug，并可以提供您自己的 EF 源代码的增强功能。</span><span class="sxs-lookup"><span data-stu-id="a6c41-269">You can file bugs, and you can contribute your own enhancements to the EF source code.</span></span>

<span data-ttu-id="a6c41-270">尽管源代码处于开源状态，但实体框架完全支持 Microsoft 产品。</span><span class="sxs-lookup"><span data-stu-id="a6c41-270">Although the source code is open, Entity Framework is fully supported as a Microsoft product.</span></span> <span data-ttu-id="a6c41-271">Microsoft Entity Framework 团队将控制接受哪些贡献和测试所有的代码更改，以确保每个版本的质量。</span><span class="sxs-lookup"><span data-stu-id="a6c41-271">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="acknowledgments"></a><span data-ttu-id="a6c41-272">鸣谢</span><span class="sxs-lookup"><span data-stu-id="a6c41-272">Acknowledgments</span></span>

- <span data-ttu-id="a6c41-273">Tom Dykstra 编写本教程中的原始版本，合著了 EF 5 更新，并编写 EF 6 更新。</span><span class="sxs-lookup"><span data-stu-id="a6c41-273">Tom Dykstra wrote the original version of this tutorial, co-authored the EF 5 update, and wrote the EF 6 update.</span></span> <span data-ttu-id="a6c41-274">Tom 是 Microsoft Web 平台和工具内容团队的资深编程作家。</span><span class="sxs-lookup"><span data-stu-id="a6c41-274">Tom is a senior programming writer on the Microsoft Web Platform and Tools Content Team.</span></span>
- <span data-ttu-id="a6c41-275">[Rick Anderson](https://blogs.msdn.com/b/rickandy/) (twitter [ @RickAndMSFT ](http://twitter.com/RickAndMSFT)) 执行大多数更新本教程的 EF 5 和 MVC 4 的工作，并且是 EF 6 更新。</span><span class="sxs-lookup"><span data-stu-id="a6c41-275">[Rick Anderson](https://blogs.msdn.com/b/rickandy/) (twitter [@RickAndMSFT](http://twitter.com/RickAndMSFT)) did most of the work updating the tutorial for EF 5 and MVC 4 and co-authored the EF 6 update.</span></span> <span data-ttu-id="a6c41-276">Rick 是 Microsoft 将重点放在 Azure 和 MVC 的资深编程作家。</span><span class="sxs-lookup"><span data-stu-id="a6c41-276">Rick is a senior programming writer for Microsoft focusing on Azure and MVC.</span></span>
- <span data-ttu-id="a6c41-277">[Rowan Miller](http://www.romiller.com)和实体框架团队的其他成员协助代码评审和帮助调试与我们正在对 EF 5 和 EF 6 更新本教程时出现的迁移的许多问题。</span><span class="sxs-lookup"><span data-stu-id="a6c41-277">[Rowan Miller](http://www.romiller.com) and other members of the Entity Framework team assisted with code reviews and helped debug many issues with migrations that arose while we were updating the tutorial for EF 5 and EF 6.</span></span>

## <a name="troubleshoot-common-errors"></a><span data-ttu-id="a6c41-278">排查常见错误</span><span class="sxs-lookup"><span data-stu-id="a6c41-278">Troubleshoot common errors</span></span>

### <a name="cannot-createshadow-copy"></a><span data-ttu-id="a6c41-279">无法创建/卷影副本</span><span class="sxs-lookup"><span data-stu-id="a6c41-279">Cannot create/shadow copy</span></span>

<span data-ttu-id="a6c41-280">错误消息：</span><span class="sxs-lookup"><span data-stu-id="a6c41-280">Error Message:</span></span>

> <span data-ttu-id="a6c41-281">无法创建/卷影副本&lt;文件名&gt;文件已存在。</span><span class="sxs-lookup"><span data-stu-id="a6c41-281">Cannot create/shadow copy '&lt;filename&gt;' when that file already exists.</span></span>

<span data-ttu-id="a6c41-282">解决方案</span><span class="sxs-lookup"><span data-stu-id="a6c41-282">Solution</span></span>

<span data-ttu-id="a6c41-283">等待几秒钟，并刷新页面。</span><span class="sxs-lookup"><span data-stu-id="a6c41-283">Wait a few seconds and refresh the page.</span></span>

### <a name="update-database-not-recognized"></a><span data-ttu-id="a6c41-284">更新数据库无法识别</span><span class="sxs-lookup"><span data-stu-id="a6c41-284">Update-Database not recognized</span></span>

<span data-ttu-id="a6c41-285">错误消息 (从`Update-Database`命令在 PMC 中):</span><span class="sxs-lookup"><span data-stu-id="a6c41-285">Error Message (from the `Update-Database` command in the PMC):</span></span>

> <span data-ttu-id="a6c41-286">术语更新数据库未识别为 cmdlet、 函数、 脚本文件或可操作程序的名称。</span><span class="sxs-lookup"><span data-stu-id="a6c41-286">The term 'Update-Database' is not recognized as the name of a cmdlet, function, script file, or operable program.</span></span> <span data-ttu-id="a6c41-287">检查名称的拼写或如果已包含路径，验证路径正确，然后重试。</span><span class="sxs-lookup"><span data-stu-id="a6c41-287">Check the spelling of the name, or if a path was included, verify that the path is correct and try again.</span></span>

<span data-ttu-id="a6c41-288">解决方案</span><span class="sxs-lookup"><span data-stu-id="a6c41-288">Solution</span></span>

<span data-ttu-id="a6c41-289">退出 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="a6c41-289">Exit Visual Studio.</span></span> <span data-ttu-id="a6c41-290">重新打开项目，然后重试。</span><span class="sxs-lookup"><span data-stu-id="a6c41-290">Reopen project and try again.</span></span>

### <a name="validation-failed"></a><span data-ttu-id="a6c41-291">验证失败</span><span class="sxs-lookup"><span data-stu-id="a6c41-291">Validation failed</span></span>

<span data-ttu-id="a6c41-292">错误消息 (从`Update-Database`命令在 PMC 中):</span><span class="sxs-lookup"><span data-stu-id="a6c41-292">Error Message (from the `Update-Database` command in the PMC):</span></span>

> <span data-ttu-id="a6c41-293">对一个或多个实体的验证失败。</span><span class="sxs-lookup"><span data-stu-id="a6c41-293">Validation failed for one or more entities.</span></span> <span data-ttu-id="a6c41-294">请参阅 EntityValidationErrors 属性的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="a6c41-294">See 'EntityValidationErrors' property for more details.</span></span>

<span data-ttu-id="a6c41-295">解决方案</span><span class="sxs-lookup"><span data-stu-id="a6c41-295">Solution</span></span>

<span data-ttu-id="a6c41-296">此问题的一个原因是验证错误时`Seed`方法运行。</span><span class="sxs-lookup"><span data-stu-id="a6c41-296">One cause of this problem is validation errors when the `Seed` method runs.</span></span> <span data-ttu-id="a6c41-297">请参阅[种子设定和调试 Entity Framework (EF) 数据库](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)有关调试的提示`Seed`方法。</span><span class="sxs-lookup"><span data-stu-id="a6c41-297">See [Seeding and Debugging Entity Framework (EF) DBs](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx) for tips on debugging the `Seed` method.</span></span>

### <a name="http-50019-error"></a><span data-ttu-id="a6c41-298">HTTP 500.19 错误</span><span class="sxs-lookup"><span data-stu-id="a6c41-298">HTTP 500.19 error</span></span>

<span data-ttu-id="a6c41-299">错误消息：</span><span class="sxs-lookup"><span data-stu-id="a6c41-299">Error Message:</span></span>

> <span data-ttu-id="a6c41-300">不能访问 HTTP 错误 500.19-内部服务器错误，请求的页面，因为该页的相关的配置数据无效。</span><span class="sxs-lookup"><span data-stu-id="a6c41-300">HTTP Error 500.19 - Internal Server Error The requested page cannot be accessed because the related configuration data for the page is invalid.</span></span>

<span data-ttu-id="a6c41-301">解决方案</span><span class="sxs-lookup"><span data-stu-id="a6c41-301">Solution</span></span>

<span data-ttu-id="a6c41-302">出现此错误的一种方法是从拥有多个解决方案，它们使用相同的端口号的每个副本。</span><span class="sxs-lookup"><span data-stu-id="a6c41-302">One way you can get this error is from having multiple copies of the solution, each of them using the same port number.</span></span> <span data-ttu-id="a6c41-303">通常可以通过退出的 Visual Studio 中，所有实例，然后重新启动正在处理的项目来解决此问题。</span><span class="sxs-lookup"><span data-stu-id="a6c41-303">You can usually solve this problem by exiting all instances of Visual Studio, then restarting the project you're working on.</span></span> <span data-ttu-id="a6c41-304">如果这不起作用，请尝试更改端口号。</span><span class="sxs-lookup"><span data-stu-id="a6c41-304">If that doesn't work, try changing the port number.</span></span> <span data-ttu-id="a6c41-305">右键单击项目文件，然后单击属性。</span><span class="sxs-lookup"><span data-stu-id="a6c41-305">Right click on the project file and then click properties.</span></span> <span data-ttu-id="a6c41-306">选择**Web**选项卡，然后将更改中的端口号**项目 Url**文本框。</span><span class="sxs-lookup"><span data-stu-id="a6c41-306">Select the **Web** tab and then change the port number in the **Project Url** text box.</span></span>

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="a6c41-307">定位 SQL Server 实例出错</span><span class="sxs-lookup"><span data-stu-id="a6c41-307">Error locating SQL Server instance</span></span>

<span data-ttu-id="a6c41-308">错误消息：</span><span class="sxs-lookup"><span data-stu-id="a6c41-308">Error Message:</span></span>

> <span data-ttu-id="a6c41-309">建立到 SQL Server 的连接时出现与网络相关或特定于实例的错误。</span><span class="sxs-lookup"><span data-stu-id="a6c41-309">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="a6c41-310">未找到或无法访问服务器。</span><span class="sxs-lookup"><span data-stu-id="a6c41-310">The server was not found or was not accessible.</span></span> <span data-ttu-id="a6c41-311">请验证实例名称是否正确，SQL Server 是否已配置为允许远程连接。</span><span class="sxs-lookup"><span data-stu-id="a6c41-311">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="a6c41-312">（提供程序：SQL 网络接口，错误：26 - 定位指定服务器/实例出错）</span><span class="sxs-lookup"><span data-stu-id="a6c41-312">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="a6c41-313">解决方案</span><span class="sxs-lookup"><span data-stu-id="a6c41-313">Solution</span></span>

<span data-ttu-id="a6c41-314">请检查连接字符串。</span><span class="sxs-lookup"><span data-stu-id="a6c41-314">Check the connection string.</span></span> <span data-ttu-id="a6c41-315">如果你已手动删除数据库，更改构造字符串中的数据库的名称。</span><span class="sxs-lookup"><span data-stu-id="a6c41-315">If you have manually deleted the database, change the name of the database in the construction string.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="a6c41-316">获取代码</span><span class="sxs-lookup"><span data-stu-id="a6c41-316">Get the code</span></span>

[<span data-ttu-id="a6c41-317">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="a6c41-317">Download Completed Project</span></span>](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a><span data-ttu-id="a6c41-318">其他资源</span><span class="sxs-lookup"><span data-stu-id="a6c41-318">Additional resources</span></span>

 <span data-ttu-id="a6c41-319">有关如何使用实体框架处理数据的详细信息，请参阅[EF 在 MSDN 上的文档页](https://msdn.microsoft.com/data/ee712907)并[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。</span><span class="sxs-lookup"><span data-stu-id="a6c41-319">For more information about how to work with data using the Entity Framework, see the [EF documentation page on MSDN](https://msdn.microsoft.com/data/ee712907) and [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

<span data-ttu-id="a6c41-320">有关如何部署 web 应用程序后已生成的详细信息，请参阅[ASP.NET Web 部署-推荐的资源](../../../../whitepapers/aspnet-web-deployment-content-map.md)MSDN 库中。</span><span class="sxs-lookup"><span data-stu-id="a6c41-320">For more information about how to deploy your web application after you've built it, see [ASP.NET Web Deployment - Recommended Resources](../../../../whitepapers/aspnet-web-deployment-content-map.md) in the MSDN Library.</span></span>

<span data-ttu-id="a6c41-321">有关相关的 MVC 中，例如身份验证和授权，其他主题的信息，请参阅[ASP.NET MVC-推荐的资源](../recommended-resources-for-mvc.md)。</span><span class="sxs-lookup"><span data-stu-id="a6c41-321">For information about other topics related to MVC, such as authentication and authorization, see the [ASP.NET MVC - Recommended Resources](../recommended-resources-for-mvc.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="a6c41-322">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a6c41-322">Next steps</span></span>

<span data-ttu-id="a6c41-323">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="a6c41-323">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a6c41-324">执行原始 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="a6c41-324">Performed raw SQL queries</span></span>
> * <span data-ttu-id="a6c41-325">执行非跟踪查询</span><span class="sxs-lookup"><span data-stu-id="a6c41-325">Performed no-tracking queries</span></span>
> * <span data-ttu-id="a6c41-326">检查的 SQL 查询发送到数据库</span><span class="sxs-lookup"><span data-stu-id="a6c41-326">Examined SQL queries sent to the database</span></span>

<span data-ttu-id="a6c41-327">你还了解了有关：</span><span class="sxs-lookup"><span data-stu-id="a6c41-327">You also learned about:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a6c41-328">创建一个抽象层</span><span class="sxs-lookup"><span data-stu-id="a6c41-328">Creating an abstraction layer</span></span>
> * <span data-ttu-id="a6c41-329">代理类</span><span class="sxs-lookup"><span data-stu-id="a6c41-329">Proxy classes</span></span>
> * <span data-ttu-id="a6c41-330">自动脏值检测</span><span class="sxs-lookup"><span data-stu-id="a6c41-330">Automatic change detection</span></span>
> * <span data-ttu-id="a6c41-331">自动验证</span><span class="sxs-lookup"><span data-stu-id="a6c41-331">Automatic validation</span></span>
> * <span data-ttu-id="a6c41-332">Entity Framework Power Tools</span><span class="sxs-lookup"><span data-stu-id="a6c41-332">Entity Framework Power Tools</span></span>
> * <span data-ttu-id="a6c41-333">实体框架源代码</span><span class="sxs-lookup"><span data-stu-id="a6c41-333">Entity Framework source code</span></span>

<span data-ttu-id="a6c41-334">这样就完成这一系列的 ASP.NET MVC 应用程序中使用实体框架的教程。</span><span class="sxs-lookup"><span data-stu-id="a6c41-334">This completes this series of tutorials on using the Entity Framework in an ASP.NET MVC application.</span></span> <span data-ttu-id="a6c41-335">如果你想要了解有关 EF Database First 的信息，请参阅 DB 第一个教程系列。</span><span class="sxs-lookup"><span data-stu-id="a6c41-335">If you want to learn about EF Database First, see the DB First tutorial series.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="a6c41-336">Entity Framework 数据库优先</span><span class="sxs-lookup"><span data-stu-id="a6c41-336">Entity Framework Database First</span></span>](../database-first-development/setting-up-database.md)