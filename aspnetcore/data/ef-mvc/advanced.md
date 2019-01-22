---
title: 使用 EF Core 创建 ASP.NET Core MVC - 高级 - 第 10 个教程（共 10 个）
author: rick-anderson
description: 本教程不止会介绍使用 Entity Framework Core 开发 ASP.NET Core Web 应用的基础知识，还会介绍其他有用主题。
ms.author: tdykstra
ms.custom: mvc
ms.date: 10/24/2018
uid: data/ef-mvc/advanced
ms.openlocfilehash: ba3834b29e78972bf914a5cba1a2cae3cc19a315
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "50090774"
---
# <a name="aspnet-core-mvc-with-ef-core---advanced---10-of-10"></a><span data-ttu-id="dd568-103">使用 EF Core 创建 ASP.NET Core MVC - 高级 - 第 10 个教程（共 10 个）</span><span class="sxs-lookup"><span data-stu-id="dd568-103">ASP.NET Core MVC with EF Core - Advanced - 10 of 10</span></span>

[!INCLUDE [RP better than MVC](~/includes/RP-EF/rp-over-mvc-21.md)]

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="dd568-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dd568-104">By [Tom Dykstra](https://github.com/tdykstra) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dd568-105">Contoso 大学示例 web 应用程序演示如何使用 Entity Framework Core 和 Visual Studio 创建 ASP.NET Core MVC web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="dd568-105">The Contoso University sample web application demonstrates how to create ASP.NET Core MVC web applications using Entity Framework Core and Visual Studio.</span></span> <span data-ttu-id="dd568-106">若要了解系列教程，请参阅[本系列中的第一个教程](intro.md)。</span><span class="sxs-lookup"><span data-stu-id="dd568-106">For information about the tutorial series, see [the first tutorial in the series](intro.md).</span></span>

<span data-ttu-id="dd568-107">之前的教程中，已经以每个类一张表的方式实现了继承。</span><span class="sxs-lookup"><span data-stu-id="dd568-107">In the previous tutorial, you implemented table-per-hierarchy inheritance.</span></span> <span data-ttu-id="dd568-108">本教程将会介绍在掌握开发基础 ASP.NET Core web 应用程序之后使用 Entity Framework Core 开发时需要注意的几个问题。</span><span class="sxs-lookup"><span data-stu-id="dd568-108">This tutorial introduces several topics that are useful to be aware of when you go beyond the basics of developing ASP.NET Core web applications that use Entity Framework Core.</span></span>

## <a name="raw-sql-queries"></a><span data-ttu-id="dd568-109">原生 SQL 查询</span><span class="sxs-lookup"><span data-stu-id="dd568-109">Raw SQL Queries</span></span>

<span data-ttu-id="dd568-110">使用 Entity Framework 的优点之一是它可避免你编写跟数据库过于耦合的代码</span><span class="sxs-lookup"><span data-stu-id="dd568-110">One of the advantages of using the Entity Framework is that it avoids tying your code too closely to a particular method of storing data.</span></span> <span data-ttu-id="dd568-111">它会自动生成 SQL 查询和命令，使得你无需自行编写。</span><span class="sxs-lookup"><span data-stu-id="dd568-111">It does this by generating SQL queries and commands for you, which also frees you from having to write them yourself.</span></span> <span data-ttu-id="dd568-112">但有一些特殊情况，你需要执行手动创建的特定 SQL 查询。</span><span class="sxs-lookup"><span data-stu-id="dd568-112">But there are exceptional scenarios when you need to run specific SQL queries that you have manually created.</span></span> <span data-ttu-id="dd568-113">对于这些情况下， Entity Framework Code First API 包括直接传递 SQL 命令将到数据库的方法。</span><span class="sxs-lookup"><span data-stu-id="dd568-113">For these scenarios, the Entity Framework Code First API includes methods that enable you to pass SQL commands directly to the database.</span></span> <span data-ttu-id="dd568-114">在 EF Core 1.0 中具有以下选项：</span><span class="sxs-lookup"><span data-stu-id="dd568-114">You have the following options in EF Core 1.0:</span></span>

* <span data-ttu-id="dd568-115">使用`DbSet.FromSql`返回实体类型的查询方法。</span><span class="sxs-lookup"><span data-stu-id="dd568-115">Use the `DbSet.FromSql` method for queries that return entity types.</span></span> <span data-ttu-id="dd568-116">返回的对象必须是`DbSet`对象期望的类型，并且它们会自动跟踪数据库上下文中除非你[手动关闭跟踪](crud.md#no-tracking-queries)。</span><span class="sxs-lookup"><span data-stu-id="dd568-116">The returned objects must be of the type expected by the `DbSet` object, and they're automatically tracked by the database context unless you [turn tracking off](crud.md#no-tracking-queries).</span></span>

* <span data-ttu-id="dd568-117">对于非查询命令使用`Database.ExecuteSqlCommand`。</span><span class="sxs-lookup"><span data-stu-id="dd568-117">Use the `Database.ExecuteSqlCommand` for non-query commands.</span></span>

<span data-ttu-id="dd568-118">如果需要运行该返回类型不是实体的查询，你可以使用由 EF 提供的 ADO.NET 中使用数据库连接。</span><span class="sxs-lookup"><span data-stu-id="dd568-118">If you need to run a query that returns types that aren't entities, you can use ADO.NET with the database connection provided by EF.</span></span> <span data-ttu-id="dd568-119">数据库上下文不会跟踪返回的数据，即使你使用该方法来检索实体类型也是如此。</span><span class="sxs-lookup"><span data-stu-id="dd568-119">The returned data isn't tracked by the database context, even if you use this method to retrieve entity types.</span></span>

<span data-ttu-id="dd568-120">在 Web 应用程序中执行 SQL 命令时，请务必采取预防措施来保护站点免受 SQL 注入攻击。</span><span class="sxs-lookup"><span data-stu-id="dd568-120">As is always true when you execute SQL commands in a web application, you must take precautions to protect your site against SQL injection attacks.</span></span> <span data-ttu-id="dd568-121">一种方法是使用参数化查询，确保不会将网页提交的字符串视为 SQL 命令。</span><span class="sxs-lookup"><span data-stu-id="dd568-121">One way to do that is to use parameterized queries to make sure that strings submitted by a web page can't be interpreted as SQL commands.</span></span> <span data-ttu-id="dd568-122">在本教程中，将用户输入集成到查询中时会使用参数化查询。</span><span class="sxs-lookup"><span data-stu-id="dd568-122">In this tutorial you'll use parameterized queries when integrating user input into a query.</span></span>

## <a name="call-a-query-that-returns-entities"></a><span data-ttu-id="dd568-123">调用返回实体的查询</span><span class="sxs-lookup"><span data-stu-id="dd568-123">Call a query that returns entities</span></span>

<span data-ttu-id="dd568-124">`DbSet<TEntity>` 类提供了可用于执行查询并返回`TEntity`类型实体的方法。</span><span class="sxs-lookup"><span data-stu-id="dd568-124">The `DbSet<TEntity>` class provides a method that you can use to execute a query that returns an entity of type `TEntity`.</span></span> <span data-ttu-id="dd568-125">若要查看实现细节，你需要更改部门控制器中`Details`方法的代码。</span><span class="sxs-lookup"><span data-stu-id="dd568-125">To see how this works you'll change the code in the `Details` method of the Department controller.</span></span>

<span data-ttu-id="dd568-126">在*DepartmentsController.cs*中的`Details`方法，通过代码调用`FromSql`方法检索一个部门，如以下高亮代码所示：</span><span class="sxs-lookup"><span data-stu-id="dd568-126">In *DepartmentsController.cs*, in the `Details` method, replace the code that retrieves a department with a `FromSql` method call, as shown in the following highlighted code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/DepartmentsController.cs?name=snippet_RawSQL&highlight=8,9,10,13)]

<span data-ttu-id="dd568-127">为了验证新代码是否工作正常，请选择**Department**选项卡，然后点击某个部门的**Detail**。</span><span class="sxs-lookup"><span data-stu-id="dd568-127">To verify that the new code works correctly, select the **Departments** tab and then **Details** for one of the departments.</span></span>

![院系详细信息](advanced/_static/department-details.png)

## <a name="call-a-query-that-returns-other-types"></a><span data-ttu-id="dd568-129">调用返回其他类型的查询</span><span class="sxs-lookup"><span data-stu-id="dd568-129">Call a query that returns other types</span></span>

<span data-ttu-id="dd568-130">之前你在“关于”页面创建了一个学生统计信息网格，显示每个注册日期的学生数量。</span><span class="sxs-lookup"><span data-stu-id="dd568-130">Earlier you created a student statistics grid for the About page that showed the number of students for each enrollment date.</span></span> <span data-ttu-id="dd568-131">可以从学生实体集中获取数据 (`_context.Students`) ，使用 LINQ 将结果投影到`EnrollmentDateGroup`视图模型对象的列表。</span><span class="sxs-lookup"><span data-stu-id="dd568-131">You got the data from the Students entity set (`_context.Students`) and used LINQ to project the results into a list of `EnrollmentDateGroup` view model objects.</span></span> <span data-ttu-id="dd568-132">假设你想要 SQL 本身编写，而不使用 LINQ。</span><span class="sxs-lookup"><span data-stu-id="dd568-132">Suppose you want to write the SQL itself rather than using LINQ.</span></span> <span data-ttu-id="dd568-133">需要运行 SQL 查询中返回实体对象之外的内容。</span><span class="sxs-lookup"><span data-stu-id="dd568-133">To do that you need to run a SQL query that returns something other than entity objects.</span></span> <span data-ttu-id="dd568-134">在 EF Core 1.0 中，执行该操作的另一种方法是编写 ADO.NET 代码，并从 EF 获取数据库连接。</span><span class="sxs-lookup"><span data-stu-id="dd568-134">In EF Core 1.0, one way to do that is write ADO.NET code and get the database connection from EF.</span></span>

<span data-ttu-id="dd568-135">在*HomeController.cs*中，将`About`方法替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="dd568-135">In *HomeController.cs*, replace the `About` method with the following code:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_UseRawSQL&highlight=3-32)]

<span data-ttu-id="dd568-136">添加 using 语句：</span><span class="sxs-lookup"><span data-stu-id="dd568-136">Add a using statement:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/HomeController.cs?name=snippet_Usings2)]

<span data-ttu-id="dd568-137">运行应用并转到“关于”页面。</span><span class="sxs-lookup"><span data-stu-id="dd568-137">Run the app and go to the About page.</span></span> <span data-ttu-id="dd568-138">显示的数据和之前一样。</span><span class="sxs-lookup"><span data-stu-id="dd568-138">It displays the same data it did before.</span></span>

![“关于”页面](advanced/_static/about.png)

## <a name="call-an-update-query"></a><span data-ttu-id="dd568-140">调用更新查询</span><span class="sxs-lookup"><span data-stu-id="dd568-140">Call an update query</span></span>

<span data-ttu-id="dd568-141">假设 Contoso 大学管理员想要在数据库中执行全局更改，例如如更改的每个课程的可修读人数。</span><span class="sxs-lookup"><span data-stu-id="dd568-141">Suppose Contoso University administrators want to perform global changes in the database, such as changing the number of credits for every course.</span></span> <span data-ttu-id="dd568-142">如果该大学有大量的课程，检索所有实体并单独更改会降低效率。</span><span class="sxs-lookup"><span data-stu-id="dd568-142">If the university has a large number of courses, it would be inefficient to retrieve them all as entities and change them individually.</span></span> <span data-ttu-id="dd568-143">在本节中，你将实现一个页面，使用户能够指定一个参数，通过这个参数可以更改所有课程的可修读人数，在这里你会通过执行 SQL UPDATE 语句来进行更改。</span><span class="sxs-lookup"><span data-stu-id="dd568-143">In this section you'll implement a web page that enables the user to specify a factor by which to change the number of credits for all courses, and you'll make the change by executing a SQL UPDATE statement.</span></span> <span data-ttu-id="dd568-144">页面外观类似于下图：</span><span class="sxs-lookup"><span data-stu-id="dd568-144">The web page will look like the following illustration:</span></span>

![“更新课程学分”页面](advanced/_static/update-credits.png)

<span data-ttu-id="dd568-146">在 CoursesContoller.cs 中，为 HttpGet 和 HttpPost 添加 UpdateCourseCredits 方法：</span><span class="sxs-lookup"><span data-stu-id="dd568-146">In *CoursesContoller.cs*, add UpdateCourseCredits methods for HttpGet and HttpPost:</span></span>

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdateGet)]

[!code-csharp[](intro/samples/cu/Controllers/CoursesController.cs?name=snippet_UpdatePost)]

<span data-ttu-id="dd568-147">当控制器处理 HttpGet 请求时，`ViewData["RowsAffected"]`中不会返回任何东西，并且在视图中显示一个空文本框和提交按钮，如上图所示。</span><span class="sxs-lookup"><span data-stu-id="dd568-147">When the controller processes an HttpGet request, nothing is returned in `ViewData["RowsAffected"]`, and the view displays an empty text box and a submit button, as shown in the preceding illustration.</span></span>

<span data-ttu-id="dd568-148">当单击**Update**按钮时，将调用 HttpPost 方法，且从文本框中输入的值获取乘数。</span><span class="sxs-lookup"><span data-stu-id="dd568-148">When the **Update** button is clicked, the HttpPost method is called, and multiplier has the value entered in the text box.</span></span> <span data-ttu-id="dd568-149">代码接着执行 SQL 语句更新课程，并向视图的`ViewData`返回受影响的行数。</span><span class="sxs-lookup"><span data-stu-id="dd568-149">The code then executes the SQL that updates courses and returns the number of affected rows to the view in `ViewData`.</span></span> <span data-ttu-id="dd568-150">当视图获取`RowsAffected`值，它将显示更新的行数。</span><span class="sxs-lookup"><span data-stu-id="dd568-150">When the view gets a `RowsAffected` value, it displays the number of rows updated.</span></span>

<span data-ttu-id="dd568-151">在“解决方案资源管理器”中，右键单击“Views/Courses”文件夹，然后依次单击“添加”和“新建项”。</span><span class="sxs-lookup"><span data-stu-id="dd568-151">In **Solution Explorer**, right-click the *Views/Courses* folder, and then click **Add > New Item**.</span></span>

<span data-ttu-id="dd568-152">在**添加新项**对话框中，在**已安装**下单击**ASP.NET**，在左窗格中，单击**MVC 视图页**，并将新视图命名为*UpdateCourseCredits.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="dd568-152">In the **Add New Item** dialog, click **ASP.NET** under **Installed** in the left pane, click **MVC View Page**, and name the new view *UpdateCourseCredits.cshtml*.</span></span>

<span data-ttu-id="dd568-153">在*Views/Courses/UpdateCourseCredits.cshtml*中，将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="dd568-153">In *Views/Courses/UpdateCourseCredits.cshtml*, replace the template code with the following code:</span></span>

[!code-html[](intro/samples/cu/Views/Courses/UpdateCourseCredits.cshtml)]

<span data-ttu-id="dd568-154">通过选择**Courses**选项卡运行`UpdateCourseCredits`方法，然后在浏览器地址栏中 URL 的末尾添加"/ UpdateCourseCredits"到 (例如： `http://localhost:5813/Courses/UpdateCourseCredits`)。</span><span class="sxs-lookup"><span data-stu-id="dd568-154">Run the `UpdateCourseCredits` method by selecting the **Courses** tab, then adding "/UpdateCourseCredits" to the end of the URL in the browser's address bar (for example: `http://localhost:5813/Courses/UpdateCourseCredits`).</span></span> <span data-ttu-id="dd568-155">在文本框中输入数字：</span><span class="sxs-lookup"><span data-stu-id="dd568-155">Enter a number in the text box:</span></span>

![“更新课程学分”页面](advanced/_static/update-credits.png)

<span data-ttu-id="dd568-157">单击**Update**。</span><span class="sxs-lookup"><span data-stu-id="dd568-157">Click **Update**.</span></span> <span data-ttu-id="dd568-158">你会看到受影响的行数：</span><span class="sxs-lookup"><span data-stu-id="dd568-158">You see the number of rows affected:</span></span>

![“更新课程学分”页面中受影响的行](advanced/_static/update-credits-rows-affected.png)

<span data-ttu-id="dd568-160">单击**Back To List**可以看到课程列表，其中可修读人数已经替换成修改后的数字。</span><span class="sxs-lookup"><span data-stu-id="dd568-160">Click **Back to List** to see the list of courses with the revised number of credits.</span></span>

<span data-ttu-id="dd568-161">请注意生产代码将确保更新最终得到有效的数据。</span><span class="sxs-lookup"><span data-stu-id="dd568-161">Note that production code would ensure that updates always result in valid data.</span></span> <span data-ttu-id="dd568-162">此处所示的简化代码会使得相乘后可修读人数大于 5。</span><span class="sxs-lookup"><span data-stu-id="dd568-162">The simplified code shown here could multiply the number of credits enough to result in numbers greater than 5.</span></span> <span data-ttu-id="dd568-163">(`Credits`属性具有`[Range(0, 5)]`特性。)更新查询将起作用，但无效的数据会导致意外的结果，例如在系统的其他部分中加入可修读人数为 5 或更少可能会导致意外的结果。</span><span class="sxs-lookup"><span data-stu-id="dd568-163">(The `Credits` property has a `[Range(0, 5)]` attribute.) The update query would work but the invalid data could cause unexpected results in other parts of the system that assume the number of credits is 5 or less.</span></span>

<span data-ttu-id="dd568-164">有关原生 SQL 查询的详细信息，请参阅[原生 SQL 查询](/ef/core/querying/raw-sql)。</span><span class="sxs-lookup"><span data-stu-id="dd568-164">For more information about raw SQL queries, see [Raw SQL Queries](/ef/core/querying/raw-sql).</span></span>

## <a name="examine-sql-sent-to-the-database"></a><span data-ttu-id="dd568-165">检查发送到数据库的 SQL</span><span class="sxs-lookup"><span data-stu-id="dd568-165">Examine SQL sent to the database</span></span>

<span data-ttu-id="dd568-166">有时能够以查看发送到数据库的实际 SQL 查询对于开发者来说是很有用的。</span><span class="sxs-lookup"><span data-stu-id="dd568-166">Sometimes it's helpful to be able to see the actual SQL queries that are sent to the database.</span></span> <span data-ttu-id="dd568-167">EF Core 自动使用 ASP.NET Core 的内置日志记录功能来编写包含 SQL 查询和更新的日志。</span><span class="sxs-lookup"><span data-stu-id="dd568-167">Built-in logging functionality for ASP.NET Core is automatically used by EF Core to write logs that contain the SQL for queries and updates.</span></span> <span data-ttu-id="dd568-168">在本部分中，你将看到记录 SQL 日志的一些示例。</span><span class="sxs-lookup"><span data-stu-id="dd568-168">In this section you'll see some examples of SQL logging.</span></span>

<span data-ttu-id="dd568-169">打开*StudentsController.cs*并在`Details`方法的`if (student == null)`语句上设置断点。</span><span class="sxs-lookup"><span data-stu-id="dd568-169">Open *StudentsController.cs* and in the `Details` method set a breakpoint on the `if (student == null)` statement.</span></span>

<span data-ttu-id="dd568-170">在调试模式下运行应用，并转到某位学生的“详细信息”页面。</span><span class="sxs-lookup"><span data-stu-id="dd568-170">Run the app in debug mode, and go to the Details page for a student.</span></span>

<span data-ttu-id="dd568-171">转到**输出**窗口显示调试输出，就可以看到查询语句：</span><span class="sxs-lookup"><span data-stu-id="dd568-171">Go to the **Output** window showing debug output, and you see the query:</span></span>

```
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (56ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT TOP(2) [s].[ID], [s].[Discriminator], [s].[FirstName], [s].[LastName], [s].[EnrollmentDate]
FROM [Person] AS [s]
WHERE ([s].[Discriminator] = N'Student') AND ([s].[ID] = @__id_0)
ORDER BY [s].[ID]
Microsoft.EntityFrameworkCore.Database.Command:Information: Executed DbCommand (122ms) [Parameters=[@__id_0='?'], CommandType='Text', CommandTimeout='30']
SELECT [s.Enrollments].[EnrollmentID], [s.Enrollments].[CourseID], [s.Enrollments].[Grade], [s.Enrollments].[StudentID], [e.Course].[CourseID], [e.Course].[Credits], [e.Course].[DepartmentID], [e.Course].[Title]
FROM [Enrollment] AS [s.Enrollments]
INNER JOIN [Course] AS [e.Course] ON [s.Enrollments].[CourseID] = [e.Course].[CourseID]
INNER JOIN (
    SELECT TOP(1) [s0].[ID]
    FROM [Person] AS [s0]
    WHERE ([s0].[Discriminator] = N'Student') AND ([s0].[ID] = @__id_0)
    ORDER BY [s0].[ID]
) AS [t] ON [s.Enrollments].[StudentID] = [t].[ID]
ORDER BY [t].[ID]
```

<span data-ttu-id="dd568-172">你会注意到一些可能会让你觉得惊讶的操作： SQL 从 Person 表最多选择 2 行 (`TOP(2)`) 。</span><span class="sxs-lookup"><span data-stu-id="dd568-172">You'll notice something here that might surprise you: the SQL selects up to 2 rows (`TOP(2)`) from the Person table.</span></span> <span data-ttu-id="dd568-173">`SingleOrDefaultAsync`方法服务器上不会解析为 1 行。</span><span class="sxs-lookup"><span data-stu-id="dd568-173">The `SingleOrDefaultAsync` method doesn't resolve to 1 row on the server.</span></span> <span data-ttu-id="dd568-174">原因是：</span><span class="sxs-lookup"><span data-stu-id="dd568-174">Here's why:</span></span>

* <span data-ttu-id="dd568-175">如果查询返回多个行，该方法会返回 null。</span><span class="sxs-lookup"><span data-stu-id="dd568-175">If the query would return multiple rows, the method returns null.</span></span>
* <span data-ttu-id="dd568-176">如果想知道查询是否返回多个行，EF 必须检查是否至少返回 2。</span><span class="sxs-lookup"><span data-stu-id="dd568-176">To determine whether the query would return multiple rows, EF has to check if it returns at least 2.</span></span>

<span data-ttu-id="dd568-177">请注意，你不必使用调试模式，并在断点处停止，然后在**输出**窗口获取日志记录。</span><span class="sxs-lookup"><span data-stu-id="dd568-177">Note that you don't have to use debug mode and stop at a breakpoint to get logging output in the **Output** window.</span></span> <span data-ttu-id="dd568-178">这种方法非常便捷，只需在想查看输出时停止日志记录即可。</span><span class="sxs-lookup"><span data-stu-id="dd568-178">It's just a convenient way to stop the logging at the point you want to look at the output.</span></span> <span data-ttu-id="dd568-179">如果不进行此操作，程序将继续进行日志记录，要查看感兴趣的部分则必须向后滚动。</span><span class="sxs-lookup"><span data-stu-id="dd568-179">If you don't do that, logging continues and you have to scroll back to find the parts you're interested in.</span></span>

## <a name="repository-and-unit-of-work-patterns"></a><span data-ttu-id="dd568-180">存储库和工作单元模式</span><span class="sxs-lookup"><span data-stu-id="dd568-180">Repository and unit of work patterns</span></span>

<span data-ttu-id="dd568-181">许多开发人员编写代码实现存储库和工作模式单元以作为使用 Entity Framework 代码的包装器。</span><span class="sxs-lookup"><span data-stu-id="dd568-181">Many developers write code to implement the repository and unit of work patterns as a wrapper around code that works with the Entity Framework.</span></span> <span data-ttu-id="dd568-182">这些模式用于在应用程序的数据访问层和业务逻辑层之间创建抽象层。</span><span class="sxs-lookup"><span data-stu-id="dd568-182">These patterns are intended to create an abstraction layer between the data access layer and the business logic layer of an application.</span></span> <span data-ttu-id="dd568-183">实现这些模式可让你的应用程序对数据存储介质的更改不敏感，而且很容易进行自动化单元测试和进行测试驱动开发 (TDD)。</span><span class="sxs-lookup"><span data-stu-id="dd568-183">Implementing these patterns can help insulate your application from changes in the data store and can facilitate automated unit testing or test-driven development (TDD).</span></span> <span data-ttu-id="dd568-184">但是，编写附加代码以实现这些模式对于使用 EF 的应用程序并不总是最好的选择，原因有以下几个：</span><span class="sxs-lookup"><span data-stu-id="dd568-184">However, writing additional code to implement these patterns isn't always the best choice for applications that use EF, for several reasons:</span></span>

* <span data-ttu-id="dd568-185">EF 上下文类可以为使用 EF 的数据库更新充当工作单位类。</span><span class="sxs-lookup"><span data-stu-id="dd568-185">The EF context class itself insulates your code from data-store-specific code.</span></span>

* <span data-ttu-id="dd568-186">对于使用 EF 进行的数据库更新，EF 上下文类可充当工作单元类。</span><span class="sxs-lookup"><span data-stu-id="dd568-186">The EF context class can act as a unit-of-work class for database updates that you do using EF.</span></span>

* <span data-ttu-id="dd568-187">EF 包括用于无需编写存储库代码就实现 TDD 的功能。</span><span class="sxs-lookup"><span data-stu-id="dd568-187">EF includes features for implementing TDD without writing repository code.</span></span>

<span data-ttu-id="dd568-188">有关如何实现存储库和工作单元模式的详细信息，请参阅[本系列教程的 Entity Framework 5 版本](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application)。</span><span class="sxs-lookup"><span data-stu-id="dd568-188">For information about how to implement the repository and unit of work patterns, see [the Entity Framework 5 version of this tutorial series](/aspnet/mvc/overview/older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application).</span></span>

<span data-ttu-id="dd568-189">Entity Framework Core 的实现可用于测试的内存数据库驱动程序。</span><span class="sxs-lookup"><span data-stu-id="dd568-189">Entity Framework Core implements an in-memory database provider that can be used for testing.</span></span> <span data-ttu-id="dd568-190">有关详细信息，请参阅[测试以及 InMemory](/ef/core/miscellaneous/testing/in-memory)。</span><span class="sxs-lookup"><span data-stu-id="dd568-190">For more information, see [Test with InMemory](/ef/core/miscellaneous/testing/in-memory).</span></span>

## <a name="automatic-change-detection"></a><span data-ttu-id="dd568-191">自动脏值检测</span><span class="sxs-lookup"><span data-stu-id="dd568-191">Automatic change detection</span></span>

<span data-ttu-id="dd568-192">Entity Framework 通过比较的实体的当前值与原始值来判断更改实体的方式 （因此需要发送更新到数据库）。</span><span class="sxs-lookup"><span data-stu-id="dd568-192">The Entity Framework determines how an entity has changed (and therefore which updates need to be sent to the database) by comparing the current values of an entity with the original values.</span></span> <span data-ttu-id="dd568-193">查询或附加该实体时会存储的原始值。</span><span class="sxs-lookup"><span data-stu-id="dd568-193">The original values are stored when the entity is queried or attached.</span></span> <span data-ttu-id="dd568-194">如下方法会导致自动脏值：</span><span class="sxs-lookup"><span data-stu-id="dd568-194">Some of the methods that cause automatic change detection are the following:</span></span>

* <span data-ttu-id="dd568-195">DbContext.SaveChanges</span><span class="sxs-lookup"><span data-stu-id="dd568-195">DbContext.SaveChanges</span></span>

* <span data-ttu-id="dd568-196">DbContext.Entry</span><span class="sxs-lookup"><span data-stu-id="dd568-196">DbContext.Entry</span></span>

* <span data-ttu-id="dd568-197">ChangeTracker.Entries</span><span class="sxs-lookup"><span data-stu-id="dd568-197">ChangeTracker.Entries</span></span>

<span data-ttu-id="dd568-198">如果正在跟踪大量实体，并且这些方法之一在循环中多次调用，通过使用`ChangeTracker.AutoDetectChangesEnabled`属性暂时关闭自动脏值检测，能够显著改进性能。</span><span class="sxs-lookup"><span data-stu-id="dd568-198">If you're tracking a large number of entities and you call one of these methods many times in a loop, you might get significant performance improvements by temporarily turning off automatic change detection using the `ChangeTracker.AutoDetectChangesEnabled` property.</span></span> <span data-ttu-id="dd568-199">例如:</span><span class="sxs-lookup"><span data-stu-id="dd568-199">For example:</span></span>

```csharp
_context.ChangeTracker.AutoDetectChangesEnabled = false;
```

## <a name="entity-framework-core-source-code-and-development-plans"></a><span data-ttu-id="dd568-200">Entity Framework Core 源代码与开发计划</span><span class="sxs-lookup"><span data-stu-id="dd568-200">Entity Framework Core source code and development plans</span></span>

<span data-ttu-id="dd568-201">Entity Framework Core 源位于 [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore)。</span><span class="sxs-lookup"><span data-stu-id="dd568-201">The Entity Framework Core source is at [https://github.com/aspnet/EntityFrameworkCore](https://github.com/aspnet/EntityFrameworkCore).</span></span> <span data-ttu-id="dd568-202">仓库中除了有源代码，还可包括每夜生成、 问题跟踪、 功能规范、 设计会议备忘录和[将来的开发路线图](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap)。</span><span class="sxs-lookup"><span data-stu-id="dd568-202">The EF Core repository contains nightly builds, issue tracking, feature specs, design meeting notes, and [the roadmap for future development](https://github.com/aspnet/EntityFrameworkCore/wiki/Roadmap).</span></span> <span data-ttu-id="dd568-203">你可以归档或查找 bug 并进行更改。</span><span class="sxs-lookup"><span data-stu-id="dd568-203">You can file or find bugs, and contribute.</span></span>

<span data-ttu-id="dd568-204">尽管源代码处于开源状态， Entity Framework Core 是由 Microsoft 完全支持的产品。</span><span class="sxs-lookup"><span data-stu-id="dd568-204">Although the source code is open, Entity Framework Core is fully supported as a Microsoft product.</span></span> <span data-ttu-id="dd568-205">Microsoft Entity Framework 团队将控制接受哪些贡献和测试所有的代码更改，以确保每个版本的质量。</span><span class="sxs-lookup"><span data-stu-id="dd568-205">The Microsoft Entity Framework team keeps control over which contributions are accepted and tests all code changes to ensure the quality of each release.</span></span>

## <a name="reverse-engineer-from-existing-database"></a><span data-ttu-id="dd568-206">现有数据库逆向工程</span><span class="sxs-lookup"><span data-stu-id="dd568-206">Reverse engineer from existing database</span></span>

<span data-ttu-id="dd568-207">若想要通过对现有数据库中的实体类反向工程得出数据模型，可以使用[scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext)。</span><span class="sxs-lookup"><span data-stu-id="dd568-207">To reverse engineer a data model including entity classes from an existing database, use the [scaffold-dbcontext](/ef/core/miscellaneous/cli/powershell#scaffold-dbcontext) command.</span></span> <span data-ttu-id="dd568-208">可以参阅[入门教程](/ef/core/get-started/aspnetcore/existing-db)。</span><span class="sxs-lookup"><span data-stu-id="dd568-208">See the [getting-started tutorial](/ef/core/get-started/aspnetcore/existing-db).</span></span>

<a id="dynamic-linq"></a>
## <a name="use-dynamic-linq-to-simplify-sort-selection-code"></a><span data-ttu-id="dd568-209">使用动态 LINQ 来简化对所选内容排序的代码</span><span class="sxs-lookup"><span data-stu-id="dd568-209">Use dynamic LINQ to simplify sort selection code</span></span>

<span data-ttu-id="dd568-210">[本系列的第三个教程](sort-filter-page.md)演示如何通过在`switch`语句中对列名称进行硬编码来编写 LINQ 。</span><span class="sxs-lookup"><span data-stu-id="dd568-210">The [third tutorial in this series](sort-filter-page.md) shows how to write LINQ code by hard-coding column names in a `switch` statement.</span></span> <span data-ttu-id="dd568-211">如果只有两列可供选择，这种方法可行，但是如果拥有许多列，代码可能会变得冗长。</span><span class="sxs-lookup"><span data-stu-id="dd568-211">With two columns to choose from, this works fine, but if you have many columns the code could get verbose.</span></span> <span data-ttu-id="dd568-212">要解决该问题，可使用 `EF.Property` 方法将属性名称指定为一个字符串。</span><span class="sxs-lookup"><span data-stu-id="dd568-212">To solve that problem, you can use the `EF.Property` method to specify the name of the property as a string.</span></span> <span data-ttu-id="dd568-213">要尝试此方法，请将 `StudentsController` 中的 `Index` 方法替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="dd568-213">To try out this approach, replace the `Index` method in the `StudentsController` with the following code.</span></span>

[!code-csharp[](intro/samples/cu/Controllers/StudentsController.cs?name=snippet_DynamicLinq)]

## <a name="next-steps"></a><span data-ttu-id="dd568-214">后续步骤</span><span class="sxs-lookup"><span data-stu-id="dd568-214">Next steps</span></span>

<span data-ttu-id="dd568-215">这将完成在 ASP.NET Core MVC 应用程序中使用 Entity Framework Core 这一系列教程。</span><span class="sxs-lookup"><span data-stu-id="dd568-215">This completes this series of tutorials on using the Entity Framework Core in an ASP.NET Core MVC application.</span></span>

<span data-ttu-id="dd568-216">有关 EF Core 的详细信息，请参阅[ Entity Framework 的Core文档](/ef/core)。</span><span class="sxs-lookup"><span data-stu-id="dd568-216">For more information about EF Core, see the [Entity Framework Core documentation](/ef/core).</span></span> <span data-ttu-id="dd568-217">还提供：[Entity Framework Core 实战](https://www.manning.com/books/entity-framework-core-in-action)一书。</span><span class="sxs-lookup"><span data-stu-id="dd568-217">A book is also available: [Entity Framework Core in Action](https://www.manning.com/books/entity-framework-core-in-action).</span></span>

<span data-ttu-id="dd568-218">有关如何部署 Web 应用的信息，请参阅 <xref:host-and-deploy/index>。</span><span class="sxs-lookup"><span data-stu-id="dd568-218">For information on how to deploy a web app, see <xref:host-and-deploy/index>.</span></span>

<span data-ttu-id="dd568-219">有关与 ASP.NET Core MVC 相关的其他主题（如身份验证与授权）的信息，请参阅 <xref:index>。</span><span class="sxs-lookup"><span data-stu-id="dd568-219">For information about other topics related to ASP.NET Core MVC, such as authentication and authorization, see <xref:index>.</span></span>

## <a name="acknowledgments"></a><span data-ttu-id="dd568-220">鸣谢</span><span class="sxs-lookup"><span data-stu-id="dd568-220">Acknowledgments</span></span>

<span data-ttu-id="dd568-221">Tom Dykstra 和 Rick Anderson (twitter @RickAndMSFT) 共同编写了本教程。</span><span class="sxs-lookup"><span data-stu-id="dd568-221">Tom Dykstra and Rick Anderson (twitter @RickAndMSFT) wrote this tutorial.</span></span> <span data-ttu-id="dd568-222">Rowan Miller、 Diego Vega 和 Entity Framework 团队的其他成员协助代码评审和帮助解决编写教程代码时出现的调试问题。</span><span class="sxs-lookup"><span data-stu-id="dd568-222">Rowan Miller, Diego Vega, and other members of the Entity Framework team assisted with code reviews and helped debug issues that arose while we were writing code for the tutorials.</span></span>

## <a name="common-errors"></a><span data-ttu-id="dd568-223">常见错误</span><span class="sxs-lookup"><span data-stu-id="dd568-223">Common errors</span></span>

### <a name="contosouniversitydll-used-by-another-process"></a><span data-ttu-id="dd568-224">ContosoUniversity.dll 被另一个进程使用</span><span class="sxs-lookup"><span data-stu-id="dd568-224">ContosoUniversity.dll used by another process</span></span>

<span data-ttu-id="dd568-225">错误消息：</span><span class="sxs-lookup"><span data-stu-id="dd568-225">Error message:</span></span>

> <span data-ttu-id="dd568-226">无法打开“...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- ”进程无法访问文件“...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll”，因为它正在由其他进程使用。</span><span class="sxs-lookup"><span data-stu-id="dd568-226">Cannot open '...bin\Debug\netcoreapp1.0\ContosoUniversity.dll' for writing -- 'The process cannot access the file '...\bin\Debug\netcoreapp1.0\ContosoUniversity.dll' because it is being used by another process.</span></span>

<span data-ttu-id="dd568-227">解决方案：</span><span class="sxs-lookup"><span data-stu-id="dd568-227">Solution:</span></span>

<span data-ttu-id="dd568-228">停止 IIS Express 中的站点。</span><span class="sxs-lookup"><span data-stu-id="dd568-228">Stop the site in IIS Express.</span></span> <span data-ttu-id="dd568-229">请转到 Windows 系统任务栏中，找到 IIS Express 并右键单击其图标、 选择 Contoso 大学站点，然后单击**停止站点**。</span><span class="sxs-lookup"><span data-stu-id="dd568-229">Go to the Windows System Tray, find IIS Express and right-click its icon, select the Contoso University site, and then click **Stop Site**.</span></span>

### <a name="migration-scaffolded-with-no-code-in-up-and-down-methods"></a><span data-ttu-id="dd568-230">迁移基架的 Up 和 Down 方法中没有代码</span><span class="sxs-lookup"><span data-stu-id="dd568-230">Migration scaffolded with no code in Up and Down methods</span></span>

<span data-ttu-id="dd568-231">可能的原因：</span><span class="sxs-lookup"><span data-stu-id="dd568-231">Possible cause:</span></span>

<span data-ttu-id="dd568-232">EF CLI 命令不会自动关闭并保存代码文件。</span><span class="sxs-lookup"><span data-stu-id="dd568-232">The EF CLI commands don't automatically close and save code files.</span></span> <span data-ttu-id="dd568-233">如果在运行`migrations add`命令时，你未保存更改，EF 将找不到所做的更改。</span><span class="sxs-lookup"><span data-stu-id="dd568-233">If you have unsaved changes when you run the `migrations add` command, EF won't find your changes.</span></span>

<span data-ttu-id="dd568-234">解决方案：</span><span class="sxs-lookup"><span data-stu-id="dd568-234">Solution:</span></span>

<span data-ttu-id="dd568-235">运行`migrations remove`命令，保存你更改的代码并重新运行`migrations add`命令。</span><span class="sxs-lookup"><span data-stu-id="dd568-235">Run the `migrations remove` command, save your code changes and rerun the `migrations add` command.</span></span>

### <a name="errors-while-running-database-update"></a><span data-ttu-id="dd568-236">运行数据库更新时出错</span><span class="sxs-lookup"><span data-stu-id="dd568-236">Errors while running database update</span></span>

<span data-ttu-id="dd568-237">在有数据的数据库中进行架构更改时，很有可能发生其他错误。</span><span class="sxs-lookup"><span data-stu-id="dd568-237">It's possible to get other errors when making schema changes in a database that has existing data.</span></span> <span data-ttu-id="dd568-238">如果遇到无法解决的迁移错误，你可以更改连接字符串中的数据库名称，或删除数据库。</span><span class="sxs-lookup"><span data-stu-id="dd568-238">If you get migration errors you can't resolve, you can either change the database name in the connection string or delete the database.</span></span> <span data-ttu-id="dd568-239">若要迁移，创建新的数据库，在数据库尚没有数据时使用更新数据库命令更有望完成且不发生错误。</span><span class="sxs-lookup"><span data-stu-id="dd568-239">With a new database, there's no data to migrate, and the update-database command is much more likely to complete without errors.</span></span>

<span data-ttu-id="dd568-240">最简单方法是在*appsettings.json*中重命名数据库。</span><span class="sxs-lookup"><span data-stu-id="dd568-240">The simplest approach is to rename the database in *appsettings.json*.</span></span> <span data-ttu-id="dd568-241">下次运行`database update`时，会创建一个新数据库。</span><span class="sxs-lookup"><span data-stu-id="dd568-241">The next time you run `database update`, a new database will be created.</span></span>

<span data-ttu-id="dd568-242">若要在 SSOX 中删除数据库，右键单击数据库，单击**删除**，然后在**删除数据库**对话框框中，选择**关闭现有连接**，单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="dd568-242">To delete a database in SSOX, right-click the database, click **Delete**, and then in the **Delete Database** dialog box select **Close existing connections** and click **OK**.</span></span>

<span data-ttu-id="dd568-243">若要使用 CLI 删除数据库，可以运行`database drop`CLI 命令：</span><span class="sxs-lookup"><span data-stu-id="dd568-243">To delete a database by using the CLI, run the `database drop` CLI command:</span></span>

```console
dotnet ef database drop
```

### <a name="error-locating-sql-server-instance"></a><span data-ttu-id="dd568-244">定位 SQL Server 实例出错</span><span class="sxs-lookup"><span data-stu-id="dd568-244">Error locating SQL Server instance</span></span>

<span data-ttu-id="dd568-245">错误消息：</span><span class="sxs-lookup"><span data-stu-id="dd568-245">Error Message:</span></span>

> <span data-ttu-id="dd568-246">建立到 SQL Server 的连接时出现与网络相关或特定于实例的错误。</span><span class="sxs-lookup"><span data-stu-id="dd568-246">A network-related or instance-specific error occurred while establishing a connection to SQL Server.</span></span> <span data-ttu-id="dd568-247">未找到或无法访问服务器。</span><span class="sxs-lookup"><span data-stu-id="dd568-247">The server was not found or was not accessible.</span></span> <span data-ttu-id="dd568-248">请验证实例名称是否正确，SQL Server 是否已配置为允许远程连接。</span><span class="sxs-lookup"><span data-stu-id="dd568-248">Verify that the instance name is correct and that SQL Server is configured to allow remote connections.</span></span> <span data-ttu-id="dd568-249">（提供程序：SQL 网络接口，错误：26 - 定位指定服务器/实例出错）</span><span class="sxs-lookup"><span data-stu-id="dd568-249">(provider: SQL Network Interfaces, error: 26 - Error Locating Server/Instance Specified)</span></span>

<span data-ttu-id="dd568-250">解决方案：</span><span class="sxs-lookup"><span data-stu-id="dd568-250">Solution:</span></span>

<span data-ttu-id="dd568-251">请检查连接字符串。</span><span class="sxs-lookup"><span data-stu-id="dd568-251">Check the connection string.</span></span> <span data-ttu-id="dd568-252">如果你已手动删除数据库文件，更改数据库的构造字符串中数据库的名称，然后从头开始使用新的数据库。</span><span class="sxs-lookup"><span data-stu-id="dd568-252">If you have manually deleted the database file, change the name of the database in the construction string to start over with a new database.</span></span>

::: moniker-end

> [!div class="step-by-step"]
> [<span data-ttu-id="dd568-253">上一篇</span><span class="sxs-lookup"><span data-stu-id="dd568-253">Previous</span></span>](inheritance.md)
