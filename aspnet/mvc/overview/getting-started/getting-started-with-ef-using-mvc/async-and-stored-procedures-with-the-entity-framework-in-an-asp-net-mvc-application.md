---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
title: 教程：使用 async 和 EF 在 ASP.NET MVC 应用中使用存储的过程
description: 在本教程中，请参阅如何实现异步编程模型，并了解如何使用存储的过程。
author: tdykstra
ms.author: riande
ms.date: 01/18/2019
ms.topic: tutorial
ms.assetid: 27d110fc-d1b7-4628-a763-26f1e6087549
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 0896664174bc2fee65b73ecf256d994f2abacc0a
ms.sourcegitcommit: 728f4e47be91e1c87bb7c0041734191b5f5c6da3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54444358"
---
# <a name="tutorial-use-async-and-stored-procedures-with-ef-in-an-aspnet-mvc-app"></a><span data-ttu-id="fa782-103">教程：使用 async 和 EF 在 ASP.NET MVC 应用中使用存储的过程</span><span class="sxs-lookup"><span data-stu-id="fa782-103">Tutorial: Use async and stored procedures with EF in an ASP.NET MVC App</span></span>

<span data-ttu-id="fa782-104">在之前的教程中，您学习了如何读取和更新使用同步编程模型的数据。</span><span class="sxs-lookup"><span data-stu-id="fa782-104">In earlier tutorials you learned how to read and update data using the synchronous programming model.</span></span> <span data-ttu-id="fa782-105">在本教程中，请参阅如何实现异步编程模型。</span><span class="sxs-lookup"><span data-stu-id="fa782-105">In this tutorial you see how to implement the asynchronous programming model.</span></span> <span data-ttu-id="fa782-106">异步代码可以帮助更好地执行，因为这样可以更好地利用服务器资源的应用程序。</span><span class="sxs-lookup"><span data-stu-id="fa782-106">Asynchronous code can help an application perform better because it makes better use of server resources.</span></span>

<span data-ttu-id="fa782-107">在本教程还了解如何将存储的过程用于插入、 更新和删除实体上的操作。</span><span class="sxs-lookup"><span data-stu-id="fa782-107">In this tutorial you also see how to use stored procedures for insert, update, and delete operations on an entity.</span></span>

<span data-ttu-id="fa782-108">最后，您重新部署到 Azure，以及它实现了自第一次部署后的数据库更改的所有应用程序。</span><span class="sxs-lookup"><span data-stu-id="fa782-108">Finally, you redeploy the application to Azure, along with all of the database changes that you've implemented since the first time you deployed.</span></span>

<span data-ttu-id="fa782-109">下图是一些将会用到的页面。</span><span class="sxs-lookup"><span data-stu-id="fa782-109">The following illustrations show some of the pages that you'll work with.</span></span>

![部门页](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![创建部门](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

<span data-ttu-id="fa782-112">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="fa782-112">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fa782-113">了解异步代码</span><span class="sxs-lookup"><span data-stu-id="fa782-113">Learn about asynchronous code</span></span>
> * <span data-ttu-id="fa782-114">创建一个部门控制器</span><span class="sxs-lookup"><span data-stu-id="fa782-114">Create a Department controller</span></span>
> * <span data-ttu-id="fa782-115">使用存储的过程</span><span class="sxs-lookup"><span data-stu-id="fa782-115">Use stored procedures</span></span>
> * <span data-ttu-id="fa782-116">部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="fa782-116">Deploy to Azure</span></span>

## <a name="prerequisites"></a><span data-ttu-id="fa782-117">系统必备</span><span class="sxs-lookup"><span data-stu-id="fa782-117">Prerequisites</span></span>

* [<span data-ttu-id="fa782-118">更新相关数据</span><span class="sxs-lookup"><span data-stu-id="fa782-118">Updating Related Data</span></span>](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="why-use-asynchronous-code"></a><span data-ttu-id="fa782-119">为什么要使用异步代码</span><span class="sxs-lookup"><span data-stu-id="fa782-119">Why use asynchronous code</span></span>

<span data-ttu-id="fa782-120">Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。</span><span class="sxs-lookup"><span data-stu-id="fa782-120">A web server has a limited number of threads available, and in high load situations all of the available threads might be in use.</span></span> <span data-ttu-id="fa782-121">当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。</span><span class="sxs-lookup"><span data-stu-id="fa782-121">When that happens, the server can't process new requests until the threads are freed up.</span></span> <span data-ttu-id="fa782-122">使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。</span><span class="sxs-lookup"><span data-stu-id="fa782-122">With synchronous code, many threads may be tied up while they aren't actually doing any work because they're waiting for I/O to complete.</span></span> <span data-ttu-id="fa782-123">使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。</span><span class="sxs-lookup"><span data-stu-id="fa782-123">With asynchronous code, when a process is waiting for I/O to complete, its thread is freed up for the server to use for processing other requests.</span></span> <span data-ttu-id="fa782-124">因此，异步代码可以使服务器资源能够更有效地使用和服务器能够处理更多流量不会延迟。</span><span class="sxs-lookup"><span data-stu-id="fa782-124">As a result, asynchronous code enables server resources to be use more efficiently, and the server is enabled to handle more traffic without delays.</span></span>

<span data-ttu-id="fa782-125">在早期版本的.NET 中，编写和测试异步代码较为复杂，容易出错且难进行调试。</span><span class="sxs-lookup"><span data-stu-id="fa782-125">In earlier versions of .NET, writing and testing asynchronous code was complex, error prone, and hard to debug.</span></span> <span data-ttu-id="fa782-126">在.NET 4.5 中，编写、 测试和调试异步代码是，您应除非有理由不这么地通常编写异步代码变得如此简单。</span><span class="sxs-lookup"><span data-stu-id="fa782-126">In .NET 4.5, writing, testing, and debugging asynchronous code is so much easier that you should generally write asynchronous code unless you have a reason not to.</span></span> <span data-ttu-id="fa782-127">异步代码会引入少量开销，但流量较低性能影响可以忽略不计，同时针对高流量情况，潜在的性能改善非常显著。</span><span class="sxs-lookup"><span data-stu-id="fa782-127">Asynchronous code does introduce a small amount of overhead, but for low traffic situations the performance hit is negligible, while for high traffic situations, the potential performance improvement is substantial.</span></span>

<span data-ttu-id="fa782-128">有关异步编程的详细信息，请参阅[使用.NET 4.5 的异步支持以避免阻塞调用](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async)。</span><span class="sxs-lookup"><span data-stu-id="fa782-128">For more information about asynchronous programming, see [Use .NET 4.5's async support to avoid blocking calls](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async).</span></span>

## <a name="create-department-controller"></a><span data-ttu-id="fa782-129">创建部门控制器</span><span class="sxs-lookup"><span data-stu-id="fa782-129">Create Department controller</span></span>

<span data-ttu-id="fa782-130">创建采用相同的方式更早版本的控制器，只不过这次选择一个部门控制器**使用异步控制器操作**复选框。</span><span class="sxs-lookup"><span data-stu-id="fa782-130">Create a Department controller the same way you did the earlier controllers, except this time select the **Use async controller actions** check box.</span></span>

<span data-ttu-id="fa782-131">以下突出显示出已添加到的同步代码`Index`方法，使其异步：</span><span class="sxs-lookup"><span data-stu-id="fa782-131">The following highlights show what was added to the synchronous code for the `Index` method to make it asynchronous:</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=1,4)]

<span data-ttu-id="fa782-132">四个更改已应用以启用要以异步方式执行的 Entity Framework 数据库查询：</span><span class="sxs-lookup"><span data-stu-id="fa782-132">Four changes were applied to enable the Entity Framework database query to execute asynchronously:</span></span>

- <span data-ttu-id="fa782-133">方法将标有`async`关键字，它会告知编译器方法正文的各部分生成回调并自动创建`Task<ActionResult>`返回对象。</span><span class="sxs-lookup"><span data-stu-id="fa782-133">The method is marked with the `async` keyword, which tells the compiler to generate callbacks for parts of the method body and to automatically create the `Task<ActionResult>` object that is returned.</span></span>
- <span data-ttu-id="fa782-134">返回类型已从`ActionResult`到`Task<ActionResult>`。</span><span class="sxs-lookup"><span data-stu-id="fa782-134">The return type was changed from `ActionResult` to `Task<ActionResult>`.</span></span> <span data-ttu-id="fa782-135">`Task<T>`类型表示正在进行的工作类型的结果`T`。</span><span class="sxs-lookup"><span data-stu-id="fa782-135">The `Task<T>` type represents ongoing work with a result of type `T`.</span></span>
- <span data-ttu-id="fa782-136">`await`关键字已应用于 web 服务调用。</span><span class="sxs-lookup"><span data-stu-id="fa782-136">The `await` keyword was applied to the web service call.</span></span> <span data-ttu-id="fa782-137">当编译器发现此关键字时，在后台它将拆分该方法为两个部分。</span><span class="sxs-lookup"><span data-stu-id="fa782-137">When the compiler sees this keyword, behind the scenes it splits the method into two parts.</span></span> <span data-ttu-id="fa782-138">第一个部分结尾以异步方式启动该操作。</span><span class="sxs-lookup"><span data-stu-id="fa782-138">The first part ends with the operation that is started asynchronously.</span></span> <span data-ttu-id="fa782-139">第二部分放入操作完成时调用的回调方法。</span><span class="sxs-lookup"><span data-stu-id="fa782-139">The second part is put into a callback method that is called when the operation completes.</span></span>
- <span data-ttu-id="fa782-140">异步版本`ToList`调用扩展方法。</span><span class="sxs-lookup"><span data-stu-id="fa782-140">The asynchronous version of the `ToList` extension method was called.</span></span>

<span data-ttu-id="fa782-141">为什么是`departments.ToList`语句但不是修改`departments = db.Departments`语句？</span><span class="sxs-lookup"><span data-stu-id="fa782-141">Why is the `departments.ToList` statement modified but not the `departments = db.Departments` statement?</span></span> <span data-ttu-id="fa782-142">原因是以异步方式执行导致查询或命令被发送到数据库的语句。</span><span class="sxs-lookup"><span data-stu-id="fa782-142">The reason is that only statements that cause queries or commands to be sent to the database are executed asynchronously.</span></span> <span data-ttu-id="fa782-143">`departments = db.Departments`语句设置了一个查询，但不是执行查询之前`ToList`调用方法。</span><span class="sxs-lookup"><span data-stu-id="fa782-143">The `departments = db.Departments` statement sets up a query but the query is not executed until the `ToList` method is called.</span></span> <span data-ttu-id="fa782-144">因此，仅`ToList`以异步方式执行方法。</span><span class="sxs-lookup"><span data-stu-id="fa782-144">Therefore, only the `ToList` method is executed asynchronously.</span></span>

<span data-ttu-id="fa782-145">在中`Details`方法和`HttpGet``Edit`并`Delete`方法，`Find`方法是，则查询发送到数据库，因此这就是以异步方式执行的方法：</span><span class="sxs-lookup"><span data-stu-id="fa782-145">In the `Details` method and the `HttpGet` `Edit` and `Delete` methods, the `Find` method is the one that causes a query to be sent to the database, so that's the method that gets executed asynchronously:</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs?highlight=7)]

<span data-ttu-id="fa782-146">在`Create`， `HttpPost Edit`，并`DeleteConfirmed`方法，它是`SaveChanges`会导致要执行的命令的方法调用、 不等的语句`db.Departments.Add(department)`这只导致要修改的内存中的实体。</span><span class="sxs-lookup"><span data-stu-id="fa782-146">In the `Create`, `HttpPost Edit`, and `DeleteConfirmed` methods, it is the `SaveChanges` method call that causes a command to be executed, not statements such as `db.Departments.Add(department)` which only cause entities in memory to be modified.</span></span>

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=6)]

<span data-ttu-id="fa782-147">打开*Views\Department\Index.cshtml*，并使用以下代码替换模板代码：</span><span class="sxs-lookup"><span data-stu-id="fa782-147">Open *Views\Department\Index.cshtml*, and replace the template code with the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=3,5,20-22,36-38)]

<span data-ttu-id="fa782-148">此代码更改标题从索引到部门，将管理员名称移动到右侧，并提供管理员的完整名称。</span><span class="sxs-lookup"><span data-stu-id="fa782-148">This code changes the title from Index to Departments, moves the Administrator name to the right, and provides the full name of the administrator.</span></span>

<span data-ttu-id="fa782-149">在创建、 删除、 详细信息，并编辑视图，更改的标题`InstructorID`字段为"Administrator"相同的方式 Course 视图中的院系名称字段更改为"部门"。</span><span class="sxs-lookup"><span data-stu-id="fa782-149">In the Create, Delete, Details, and Edit views, change the caption for the `InstructorID` field to "Administrator" the same way you changed the department name field to "Department" in the Course views.</span></span>

<span data-ttu-id="fa782-150">在创建和编辑视图使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="fa782-150">In the Create and Edit views use the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml)]

<span data-ttu-id="fa782-151">在删除和详细信息视图中使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="fa782-151">In the Delete and Details views use the following code:</span></span>

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml)]

<span data-ttu-id="fa782-152">运行该应用程序，并单击**部门**选项卡。</span><span class="sxs-lookup"><span data-stu-id="fa782-152">Run the application, and click the **Departments** tab.</span></span>

<span data-ttu-id="fa782-153">一切都与其他控制器中的相同，但在此控制器中的所有 SQL 查询都异步执行。</span><span class="sxs-lookup"><span data-stu-id="fa782-153">Everything works the same as in the other controllers, but in this controller all of the SQL queries are executing asynchronously.</span></span>

<span data-ttu-id="fa782-154">需要注意当与实体框架配合使用异步编程的一些事项：</span><span class="sxs-lookup"><span data-stu-id="fa782-154">Some things to be aware of when you are using asynchronous programming with the Entity Framework:</span></span>

- <span data-ttu-id="fa782-155">异步代码不是线程安全。</span><span class="sxs-lookup"><span data-stu-id="fa782-155">The async code is not thread safe.</span></span> <span data-ttu-id="fa782-156">换而言之，换而言之，请勿尝试执行并行使用的同一上下文实例的多个操作。</span><span class="sxs-lookup"><span data-stu-id="fa782-156">In other words, in other words, don't try to do multiple operations in parallel using the same context instance.</span></span>
- <span data-ttu-id="fa782-157">如果你想要利用异步代码的性能优势，请确保你所使用的任何库和包在它们调用导致 Entity Framework 数据库查询方法时也使用异步。</span><span class="sxs-lookup"><span data-stu-id="fa782-157">If you want to take advantage of the performance benefits of async code, make sure that any library packages that you're using (such as for paging), also use async if they call any Entity Framework methods that cause queries to be sent to the database.</span></span>

## <a name="use-stored-procedures"></a><span data-ttu-id="fa782-158">使用存储的过程</span><span class="sxs-lookup"><span data-stu-id="fa782-158">Use stored procedures</span></span>

<span data-ttu-id="fa782-159">一些开发人员和 Dba 更愿意将存储的过程用于数据库访问权限。</span><span class="sxs-lookup"><span data-stu-id="fa782-159">Some developers and DBAs prefer to use stored procedures for database access.</span></span> <span data-ttu-id="fa782-160">在早期版本的实体框架中，您可以检索数据使用存储的过程来[执行原始 SQL 查询](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)，但您不能指示 EF 以使用存储的过程以执行更新操作。</span><span class="sxs-lookup"><span data-stu-id="fa782-160">In earlier versions of Entity Framework you can retrieve data using a stored procedure by [executing a raw SQL query](advanced-entity-framework-scenarios-for-an-mvc-web-application.md), but you can't instruct EF to use stored procedures for update operations.</span></span> <span data-ttu-id="fa782-161">EF 6 中很容易配置 Code First 以使用存储的过程。</span><span class="sxs-lookup"><span data-stu-id="fa782-161">In EF 6 it's easy to configure Code First to use stored procedures.</span></span>

1. <span data-ttu-id="fa782-162">在中*DAL\SchoolContext.cs*，将突出显示的代码添加到`OnModelCreating`方法。</span><span class="sxs-lookup"><span data-stu-id="fa782-162">In *DAL\SchoolContext.cs*, add the highlighted code to the `OnModelCreating` method.</span></span>

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=9)]

    <span data-ttu-id="fa782-163">此代码指示实体框架使用存储过程的插入、 更新和删除操作上`Department`实体。</span><span class="sxs-lookup"><span data-stu-id="fa782-163">This code instructs Entity Framework to use stored procedures for insert, update, and delete operations on the `Department` entity.</span></span>
2. <span data-ttu-id="fa782-164">在包管理控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="fa782-164">In Package Manage Console, enter the following command:</span></span>

    `add-migration DepartmentSP`

    <span data-ttu-id="fa782-165">打开*迁移\&l t; 时间戳&gt;\_DepartmentSP.cs*若要查看中的代码`Up`方法，可创建插入、 更新和删除存储过程：</span><span class="sxs-lookup"><span data-stu-id="fa782-165">Open *Migrations\&lt;timestamp&gt;\_DepartmentSP.cs* to see the code in the `Up` method that creates Insert, Update, and Delete stored procedures:</span></span>

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs?highlight=3-4,26-27,42-43)]
3. <span data-ttu-id="fa782-166">在包管理控制台中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="fa782-166">In Package Manage Console, enter the following command:</span></span>

     `update-database`
4. <span data-ttu-id="fa782-167">在调试模式下运行应用程序中，单击**部门**选项卡，然后依次**创建新**。</span><span class="sxs-lookup"><span data-stu-id="fa782-167">Run the application in debug mode, click the **Departments** tab, and then click **Create New**.</span></span>
5. <span data-ttu-id="fa782-168">为新的院系中，输入数据，然后单击**创建**。</span><span class="sxs-lookup"><span data-stu-id="fa782-168">Enter data for a new department, and then click **Create**.</span></span>

6. <span data-ttu-id="fa782-169">在 Visual Studio 中查看的日志**输出**窗口以查看存储的过程用于插入新 Department 行。</span><span class="sxs-lookup"><span data-stu-id="fa782-169">In Visual Studio, look at the logs in the **Output** window to see that a stored procedure was used to insert the new Department row.</span></span>

     ![部门插入 SP](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

<span data-ttu-id="fa782-171">代码首先创建默认存储过程名称。</span><span class="sxs-lookup"><span data-stu-id="fa782-171">Code First creates default stored procedure names.</span></span> <span data-ttu-id="fa782-172">如果使用现有数据库，您可能需要自定义以便使用已在数据库中定义的存储的过程的存储的过程名称。</span><span class="sxs-lookup"><span data-stu-id="fa782-172">If you are using an existing database, you might need to customize the stored procedure names in order to use stored procedures already defined in the database.</span></span> <span data-ttu-id="fa782-173">有关如何执行该操作的信息，请参阅[实体框架代码第一个 Insert/Update/Delete 存储过程](https://msdn.microsoft.com/data/dn468673)。</span><span class="sxs-lookup"><span data-stu-id="fa782-173">For information about how to do that, see [Entity Framework Code First Insert/Update/Delete Stored Procedures](https://msdn.microsoft.com/data/dn468673).</span></span>

<span data-ttu-id="fa782-174">如果你想要自定义哪些生成存储的过程执行操作，则可以编辑迁移的基架的代码`Up`创建存储的过程的方法。</span><span class="sxs-lookup"><span data-stu-id="fa782-174">If you want to customize what generated stored procedures do, you can edit the scaffolded code for the migrations `Up` method that creates the stored procedure.</span></span> <span data-ttu-id="fa782-175">通过这种方式所做的更改将反映每当迁移运行时和迁移后部署在生产中自动运行时将应用于生产数据库。</span><span class="sxs-lookup"><span data-stu-id="fa782-175">That way your changes are reflected whenever that migration is run and will be applied to your production database when migrations runs automatically in production after deployment.</span></span>

<span data-ttu-id="fa782-176">如果你想要更改现有的存储的过程在先前的迁移中创建，可添加迁移命令用于生成空白迁移时，然后手动编写代码以调用[AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx)方法.</span><span class="sxs-lookup"><span data-stu-id="fa782-176">If you want to change an existing stored procedure that was created in a previous migration, you can use the Add-Migration command to generate a blank migration, and then manually write code that calls the [AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx) method.</span></span>

## <a name="deploy-to-azure"></a><span data-ttu-id="fa782-177">部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="fa782-177">Deploy to Azure</span></span>

<span data-ttu-id="fa782-178">该部分要求你已完成的可选**将其部署到 Azure**主题中[迁移和部署](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md)本系列教程。</span><span class="sxs-lookup"><span data-stu-id="fa782-178">This section requires you to have completed the optional **Deploying the app to Azure** section in the [Migrations and Deployment](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md) tutorial of this series.</span></span> <span data-ttu-id="fa782-179">如果必须通过删除在本地项目中的数据库解决的迁移错误，跳过此部分。</span><span class="sxs-lookup"><span data-stu-id="fa782-179">If you had migrations errors that you resolved by deleting the database in your local project, skip this section.</span></span>

1. <span data-ttu-id="fa782-180">在 Visual Studio 中，右键单击该项目中的**解决方案资源管理器**，然后选择**发布**从上下文菜单。</span><span class="sxs-lookup"><span data-stu-id="fa782-180">In Visual Studio, right-click the project in **Solution Explorer** and select **Publish** from the context menu.</span></span>
2. <span data-ttu-id="fa782-181">单击“发布” 。</span><span class="sxs-lookup"><span data-stu-id="fa782-181">Click **Publish**.</span></span>

    <span data-ttu-id="fa782-182">Visual Studio 将部署到 Azure，应用程序和应用程序打开在默认浏览器中，在 Azure 中运行。</span><span class="sxs-lookup"><span data-stu-id="fa782-182">Visual Studio deploys the application to Azure, and the application opens in your default browser, running in Azure.</span></span>
3. <span data-ttu-id="fa782-183">测试应用程序以验证它是否正常工作。</span><span class="sxs-lookup"><span data-stu-id="fa782-183">Test the application to verify it's working.</span></span>

    <span data-ttu-id="fa782-184">首次运行页面访问数据库，Entity Framework 将运行所有迁移`Up`使数据库保持最新的当前数据模型所必需的方法。</span><span class="sxs-lookup"><span data-stu-id="fa782-184">The first time you run a page that accesses the database, the Entity Framework runs all of the migrations `Up` methods required to bring the database up to date with the current data model.</span></span> <span data-ttu-id="fa782-185">现在可以使用的所有自上次部署，包括在本教程中添加的部门页添加的 web 页面。</span><span class="sxs-lookup"><span data-stu-id="fa782-185">You can now use all of the web pages that you added since the last time you deployed, including the Department pages that you added in this tutorial.</span></span>

## <a name="get-the-code"></a><span data-ttu-id="fa782-186">获取代码</span><span class="sxs-lookup"><span data-stu-id="fa782-186">Get the code</span></span>

[<span data-ttu-id="fa782-187">下载已完成的项目</span><span class="sxs-lookup"><span data-stu-id="fa782-187">Download the Completed Project</span></span>](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a><span data-ttu-id="fa782-188">其他资源</span><span class="sxs-lookup"><span data-stu-id="fa782-188">Additional resources</span></span>

<span data-ttu-id="fa782-189">其他实体框架资源的链接可在[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。</span><span class="sxs-lookup"><span data-stu-id="fa782-189">Links to other Entity Framework resources can be found in the [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="fa782-190">后续步骤</span><span class="sxs-lookup"><span data-stu-id="fa782-190">Next steps</span></span>

<span data-ttu-id="fa782-191">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="fa782-191">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="fa782-192">介绍了异步代码</span><span class="sxs-lookup"><span data-stu-id="fa782-192">Learned about asynchronous code</span></span>
> * <span data-ttu-id="fa782-193">创建一个部门控制器</span><span class="sxs-lookup"><span data-stu-id="fa782-193">Created a Department controller</span></span>
> * <span data-ttu-id="fa782-194">使用存储的过程</span><span class="sxs-lookup"><span data-stu-id="fa782-194">Used stored procedures</span></span>
> * <span data-ttu-id="fa782-195">部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="fa782-195">Deployed to Azure</span></span>

<span data-ttu-id="fa782-196">转到下一步的文章，了解如何在多个用户在同一时间更新同一实体时处理冲突。</span><span class="sxs-lookup"><span data-stu-id="fa782-196">Advance to the next article to learn how to handle conflicts when multiple users update the same entity at the same time.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="fa782-197">处理并发</span><span class="sxs-lookup"><span data-stu-id="fa782-197">Handling concurrency</span></span>](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
