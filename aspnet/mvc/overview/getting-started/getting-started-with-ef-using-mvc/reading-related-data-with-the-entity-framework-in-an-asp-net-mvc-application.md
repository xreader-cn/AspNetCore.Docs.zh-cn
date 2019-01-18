---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
title: 教程：读取使用 EF 在 ASP.NET MVC 应用中的相关的数据
description: 在本教程将读取并显示相关的数据-即 Entity Framework 加载到导航属性的数据。
author: tdykstra
ms.author: riande
ms.date: 01/17/2019
ms.topic: tutorial
ms.assetid: 18cdd896-8ed9-4547-b143-114711e3eafb
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 8660a75655b801364cce7c4b59847c5c00562a27
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396202"
---
# <a name="tutorial-read-related-data-with-ef-in-an-aspnet-mvc-app"></a><span data-ttu-id="408c7-103">教程：读取使用 EF 在 ASP.NET MVC 应用中的相关的数据</span><span class="sxs-lookup"><span data-stu-id="408c7-103">Tutorial: Read related data with EF in an ASP.NET MVC app</span></span>

<span data-ttu-id="408c7-104">在上一教程中，您将完成学校数据模型。</span><span class="sxs-lookup"><span data-stu-id="408c7-104">In the previous tutorial you completed the School data model.</span></span> <span data-ttu-id="408c7-105">在本教程将读取并显示相关的数据-即 Entity Framework 加载到导航属性的数据。</span><span class="sxs-lookup"><span data-stu-id="408c7-105">In this tutorial you'll read and display related data — that is, data that the Entity Framework loads into navigation properties.</span></span>

<span data-ttu-id="408c7-106">下图是将会用到的页面。</span><span class="sxs-lookup"><span data-stu-id="408c7-106">The following illustrations show the pages that you'll work with.</span></span>

![](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Instructors_index_page_with_instructor_and_course_selected](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

<span data-ttu-id="408c7-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="408c7-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="408c7-109">了解如何将加载相关的数据</span><span class="sxs-lookup"><span data-stu-id="408c7-109">Learn how to load related data</span></span>
> * <span data-ttu-id="408c7-110">创建课程页</span><span class="sxs-lookup"><span data-stu-id="408c7-110">Create a Courses page</span></span>
> * <span data-ttu-id="408c7-111">创建讲师页</span><span class="sxs-lookup"><span data-stu-id="408c7-111">Create an Instructors page</span></span>

## <a name="prerequisites"></a><span data-ttu-id="408c7-112">系统必备</span><span class="sxs-lookup"><span data-stu-id="408c7-112">Prerequisites</span></span>

* [<span data-ttu-id="408c7-113">创建更复杂的数据模型</span><span class="sxs-lookup"><span data-stu-id="408c7-113">Create a more complex data model</span></span>](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)

## <a name="learn-how-to-load-related-data"></a><span data-ttu-id="408c7-114">了解如何将加载相关的数据</span><span class="sxs-lookup"><span data-stu-id="408c7-114">Learn how to load related data</span></span>

<span data-ttu-id="408c7-115">有几种方法，实体框架可以相关的数据加载到实体的导航属性：</span><span class="sxs-lookup"><span data-stu-id="408c7-115">There are several ways that the Entity Framework can load related data into the navigation properties of an entity:</span></span>

- <span data-ttu-id="408c7-116">*延迟加载*。</span><span class="sxs-lookup"><span data-stu-id="408c7-116">*Lazy loading*.</span></span> <span data-ttu-id="408c7-117">首次读取实体时，不检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="408c7-117">When the entity is first read, related data isn't retrieved.</span></span> <span data-ttu-id="408c7-118">然而，首次尝试访问导航属性时，会自动检索导航属性所需的数据。</span><span class="sxs-lookup"><span data-stu-id="408c7-118">However, the first time you attempt to access a navigation property, the data required for that navigation property is automatically retrieved.</span></span> <span data-ttu-id="408c7-119">这会导致发送到数据库的多个查询 — 一个用于实体本身，一个必须检索每个相关实体数据的时间。</span><span class="sxs-lookup"><span data-stu-id="408c7-119">This results in multiple queries sent to the database — one for the entity itself and one each time that related data for the entity must be retrieved.</span></span> <span data-ttu-id="408c7-120">`DbContext`类默认情况下启用延迟加载。</span><span class="sxs-lookup"><span data-stu-id="408c7-120">The `DbContext` class enables lazy loading by default.</span></span>

    ![Lazy_loading_example](https://asp.net/media/2577850/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Lazy_loading_example_2c44eabb-5fd3-485a-837d-8e3d053f2c0c.png)
- <span data-ttu-id="408c7-122">*预先加载*。</span><span class="sxs-lookup"><span data-stu-id="408c7-122">*Eager loading*.</span></span> <span data-ttu-id="408c7-123">读取该实体时，会同时检索相关数据。</span><span class="sxs-lookup"><span data-stu-id="408c7-123">When the entity is read, related data is retrieved along with it.</span></span> <span data-ttu-id="408c7-124">此时通常会出现单一联接查询，检索所有必需数据。</span><span class="sxs-lookup"><span data-stu-id="408c7-124">This typically results in a single join query that retrieves all of the data that's needed.</span></span> <span data-ttu-id="408c7-125">使用指定预先加载`Include`方法。</span><span class="sxs-lookup"><span data-stu-id="408c7-125">You specify eager loading by using the `Include` method.</span></span>

    ![Eager_loading_example](https://asp.net/media/2577856/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Eager_loading_example_33f907ff-f0b0-4057-8e75-05a8cacac807.png)
- <span data-ttu-id="408c7-127">*显式加载*。</span><span class="sxs-lookup"><span data-stu-id="408c7-127">*Explicit loading*.</span></span> <span data-ttu-id="408c7-128">这是类似于延迟加载的只不过显式检索相关的数据中的代码;它不会自动发生时您访问导航属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-128">This is similar to lazy loading, except that you explicitly retrieve the related data in code; it doesn't happen automatically when you access a navigation property.</span></span> <span data-ttu-id="408c7-129">加载相关的数据手动通过实体和调用获取对象状态管理器入口[Collection.Load](https://msdn.microsoft.com/library/gg696220(v=vs.103).aspx)集合的方法或[Reference.Load](https://msdn.microsoft.com/library/gg679166(v=vs.103).aspx)方法保存的属性单个实体。</span><span class="sxs-lookup"><span data-stu-id="408c7-129">You load related data manually by getting the object state manager entry for an entity and calling the [Collection.Load](https://msdn.microsoft.com/library/gg696220(v=vs.103).aspx) method for collections or the [Reference.Load](https://msdn.microsoft.com/library/gg679166(v=vs.103).aspx) method for properties that hold a single entity.</span></span> <span data-ttu-id="408c7-130">(在以下示例中，如果你想要加载管理员导航属性，您会取代`Collection(x => x.Courses)`与`Reference(x => x.Administrator)`。)通常会使用显式加载，仅当你已启用延迟加载关闭时。</span><span class="sxs-lookup"><span data-stu-id="408c7-130">(In the following example, if you wanted to load the Administrator navigation property, you'd replace `Collection(x => x.Courses)` with `Reference(x => x.Administrator)`.) Typically you'd use explicit loading only when you've turned lazy loading off.</span></span>

    ![Explicit_loading_example](https://asp.net/media/2577862/Windows-Live-Writer_Reading-Re.NET-MVC-Application-5-of-10h1_ADC3_Explicit_loading_example_79d8c368-6d82-426f-be9a-2b443644ab15.png)

<span data-ttu-id="408c7-132">因为它们不立即检索属性值，延迟加载和显式加载也都称为*延迟加载*。</span><span class="sxs-lookup"><span data-stu-id="408c7-132">Because they don't immediately retrieve the property values, lazy loading and explicit loading are also both known as *deferred loading*.</span></span>

### <a name="performance-considerations"></a><span data-ttu-id="408c7-133">性能注意事项</span><span class="sxs-lookup"><span data-stu-id="408c7-133">Performance considerations</span></span>

<span data-ttu-id="408c7-134">如果知道自己需要每个检索的实体的相关数据，选择预先加载可获得最佳性能，因为相比每个检索的实体的单独查询，发送到数据库的单个查询更加有效。</span><span class="sxs-lookup"><span data-stu-id="408c7-134">If you know you need related data for every entity retrieved, eager loading often offers the best performance, because a single query sent to the database is typically more efficient than separate queries for each entity retrieved.</span></span> <span data-ttu-id="408c7-135">例如，在上面的示例中，假设每个系有十个相关的课程。</span><span class="sxs-lookup"><span data-stu-id="408c7-135">For example, in the above examples, suppose that each department has ten related courses.</span></span> <span data-ttu-id="408c7-136">预先加载示例会导致只需单一 （联接） 查询，将单个往返到数据库。</span><span class="sxs-lookup"><span data-stu-id="408c7-136">The eager loading example would result in just a single (join) query and a single round trip to the database.</span></span> <span data-ttu-id="408c7-137">延迟加载和显式加载示例将同时导致 11 个查询和 11 个往返到数据库。</span><span class="sxs-lookup"><span data-stu-id="408c7-137">The lazy loading and explicit loading examples would both result in eleven queries and eleven round trips to the database.</span></span> <span data-ttu-id="408c7-138">延迟较高时，额外往返数据库对性能尤为不利。</span><span class="sxs-lookup"><span data-stu-id="408c7-138">The extra round trips to the database are especially detrimental to performance when latency is high.</span></span>

<span data-ttu-id="408c7-139">但是，在某些情况下延迟加载是更高效。</span><span class="sxs-lookup"><span data-stu-id="408c7-139">On the other hand, in some scenarios lazy loading is more efficient.</span></span> <span data-ttu-id="408c7-140">预先加载可能会导致非常复杂的联接为其生成 SQL Server 无法有效地处理它。</span><span class="sxs-lookup"><span data-stu-id="408c7-140">Eager loading might cause a very complex join to be generated, which SQL Server can't process efficiently.</span></span> <span data-ttu-id="408c7-141">或者，如果您需要访问实体的导航属性仅对一组实体的子集进行处理，延迟加载可能会更好地执行，因为预先加载将检索数据超过所需的数据。</span><span class="sxs-lookup"><span data-stu-id="408c7-141">Or if you need to access an entity's navigation properties only for a subset of a set of the entities you're processing, lazy loading might perform better because eager loading would retrieve more data than you need.</span></span> <span data-ttu-id="408c7-142">如果看重性能，那么最好测试两种方式的性能，以便做出最佳选择。</span><span class="sxs-lookup"><span data-stu-id="408c7-142">If performance is critical, it's best to test performance both ways in order to make the best choice.</span></span>

<span data-ttu-id="408c7-143">延迟加载可以屏蔽会导致性能问题的代码。</span><span class="sxs-lookup"><span data-stu-id="408c7-143">Lazy loading can mask code that causes performance problems.</span></span> <span data-ttu-id="408c7-144">例如，（由于与数据库的多个往返） 未指定预先或显式加载，但处理大量的实体并在每次迭代中使用多个导航属性的代码可能是非常低效。</span><span class="sxs-lookup"><span data-stu-id="408c7-144">For example, code that doesn't specify eager or explicit loading but processes a high volume of entities and uses several navigation properties in each iteration might be very inefficient (because of many round trips to the database).</span></span> <span data-ttu-id="408c7-145">在开发使用本地 SQL 服务器中执行的应用程序可能具有性能问题时增加延迟时间和延迟加载由于移到 Azure SQL 数据库。</span><span class="sxs-lookup"><span data-stu-id="408c7-145">An application that performs well in development using an on premise SQL server might have performance problems when moved to Azure SQL Database due to the increased latency and lazy loading.</span></span> <span data-ttu-id="408c7-146">分析数据库查询的实际测试负载将有助于确定延迟加载是否适当。</span><span class="sxs-lookup"><span data-stu-id="408c7-146">Profiling the database queries with a realistic test load will help you determine if lazy loading is appropriate.</span></span> <span data-ttu-id="408c7-147">有关详细信息请参阅[揭开面纱实体框架策略：加载相关的数据](https://msdn.microsoft.com/magazine/hh205756.aspx)并[使用 Entity Framework 减少 SQL Azure 的网络延迟](https://msdn.microsoft.com/magazine/gg309181.aspx)。</span><span class="sxs-lookup"><span data-stu-id="408c7-147">For more information see [Demystifying Entity Framework Strategies: Loading Related Data](https://msdn.microsoft.com/magazine/hh205756.aspx) and [Using the Entity Framework to Reduce Network Latency to SQL Azure](https://msdn.microsoft.com/magazine/gg309181.aspx).</span></span>

### <a name="disable-lazy-loading-before-serialization"></a><span data-ttu-id="408c7-148">禁用延迟加载序列化之前</span><span class="sxs-lookup"><span data-stu-id="408c7-148">Disable lazy loading before serialization</span></span>

<span data-ttu-id="408c7-149">如果将延迟加载已启用序列化期间，您可以得到查询比你预期更多的数据。</span><span class="sxs-lookup"><span data-stu-id="408c7-149">If you leave lazy loading enabled during serialization, you can end up querying significantly more data than you intended.</span></span> <span data-ttu-id="408c7-150">序列化通常可通过访问类型的实例上每个属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-150">Serialization generally works by accessing each property on an instance of a type.</span></span> <span data-ttu-id="408c7-151">属性访问触发延迟加载，并为这些延迟加载的实体进行序列化。</span><span class="sxs-lookup"><span data-stu-id="408c7-151">Property access triggers lazy loading, and those lazy loaded entities are serialized.</span></span> <span data-ttu-id="408c7-152">然后，序列化过程访问的延迟加载的实体，可能会导致更多的延迟加载和序列化的每个属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-152">The serialization process then accesses each property of the lazy-loaded entities, potentially causing even more lazy loading and serialization.</span></span> <span data-ttu-id="408c7-153">若要防止此用尽链式反应，启用延迟加载关闭之前序列化实体。</span><span class="sxs-lookup"><span data-stu-id="408c7-153">To prevent this run-away chain reaction, turn lazy loading off before you serialize an entity.</span></span>

<span data-ttu-id="408c7-154">此外将序列化复杂由实体框架使用，代理类，如中所述[高级方案教程](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#proxies)。</span><span class="sxs-lookup"><span data-stu-id="408c7-154">Serialization can also be complicated by the proxy classes that the Entity Framework uses, as explained in the [Advanced Scenarios tutorial](advanced-entity-framework-scenarios-for-an-mvc-web-application.md#proxies).</span></span>

<span data-ttu-id="408c7-155">若要避免序列化问题的一种方法是要序列化数据传输对象 (Dto)，而不是实体对象，如中所示[使用实体框架使用 Web API](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-5.md)教程。</span><span class="sxs-lookup"><span data-stu-id="408c7-155">One way to avoid serialization problems is to serialize data transfer objects (DTOs) instead of entity objects, as shown in the [Using Web API with Entity Framework](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-5.md) tutorial.</span></span>

<span data-ttu-id="408c7-156">如果不使用 Dto，可以禁用延迟加载，从而避免由代理问题[禁用代理创建](https://msdn.microsoft.com/data/jj592886.aspx)。</span><span class="sxs-lookup"><span data-stu-id="408c7-156">If you don't use DTOs, you can disable lazy loading and avoid proxy issues by [disabling proxy creation](https://msdn.microsoft.com/data/jj592886.aspx).</span></span>

<span data-ttu-id="408c7-157">以下是一些其他[方式禁用延迟加载](https://msdn.microsoft.com/data/jj574232):</span><span class="sxs-lookup"><span data-stu-id="408c7-157">Here are some other [ways to disable lazy loading](https://msdn.microsoft.com/data/jj574232):</span></span>

- <span data-ttu-id="408c7-158">对于特定的导航属性，省略`virtual`关键字声明的属性时。</span><span class="sxs-lookup"><span data-stu-id="408c7-158">For specific navigation properties, omit the `virtual` keyword when you declare the property.</span></span>
- <span data-ttu-id="408c7-159">对于所有导航属性，设置`LazyLoadingEnabled`到`false`，上下文类的构造函数中添加以下代码：</span><span class="sxs-lookup"><span data-stu-id="408c7-159">For all navigation properties, set `LazyLoadingEnabled` to `false`, put the following code in the constructor of your context class:</span></span>

    [!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

## <a name="create-a-courses-page"></a><span data-ttu-id="408c7-160">创建课程页</span><span class="sxs-lookup"><span data-stu-id="408c7-160">Create a Courses page</span></span>

<span data-ttu-id="408c7-161">`Course`实体包括导航属性包含`Department`院系的课程将分配到的实体。</span><span class="sxs-lookup"><span data-stu-id="408c7-161">The `Course` entity includes a navigation property that contains the `Department` entity of the department that the course is assigned to.</span></span> <span data-ttu-id="408c7-162">若要在课程列表中显示的分配系的名称，您需要获取`Name`属性从`Department`中的实体`Course.Department`导航属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-162">To display the name of the assigned department in a list of courses, you need to get the `Name` property from the `Department` entity that is in the `Course.Department` navigation property.</span></span>

<span data-ttu-id="408c7-163">创建名为的控制器`CourseController`(不 CoursesController) 用于`Course`实体类型，使用相同的选项**包含的 MVC 5 控制器视图，使用实体框架**用于之前所做的基架`Student`控制器：</span><span class="sxs-lookup"><span data-stu-id="408c7-163">Create a controller named `CourseController` (not CoursesController) for the `Course` entity type, using the same options for the **MVC 5 Controller with views, using Entity Framework** scaffolder that you did earlier for the `Student` controller:</span></span>

| <span data-ttu-id="408c7-164">设置</span><span class="sxs-lookup"><span data-stu-id="408c7-164">Setting</span></span> | <span data-ttu-id="408c7-165">值</span><span class="sxs-lookup"><span data-stu-id="408c7-165">Value</span></span> |
| ------- | ----- |
| <span data-ttu-id="408c7-166">Model 类</span><span class="sxs-lookup"><span data-stu-id="408c7-166">Model class</span></span> | <span data-ttu-id="408c7-167">选择**课程 (ContosoUniversity.Models)**。</span><span class="sxs-lookup"><span data-stu-id="408c7-167">Select **Course (ContosoUniversity.Models)**.</span></span> |
| <span data-ttu-id="408c7-168">数据上下文类</span><span class="sxs-lookup"><span data-stu-id="408c7-168">Data context class</span></span> | <span data-ttu-id="408c7-169">选择**SchoolContext (ContosoUniversity.DAL)**。</span><span class="sxs-lookup"><span data-stu-id="408c7-169">Select **SchoolContext (ContosoUniversity.DAL)**.</span></span> |
| <span data-ttu-id="408c7-170">控制器名称</span><span class="sxs-lookup"><span data-stu-id="408c7-170">Controller name</span></span> | <span data-ttu-id="408c7-171">输入*CourseController*。</span><span class="sxs-lookup"><span data-stu-id="408c7-171">Enter *CourseController*.</span></span> <span data-ttu-id="408c7-172">同样，不*CoursesController*与*s*。</span><span class="sxs-lookup"><span data-stu-id="408c7-172">Again, not *CoursesController* with an *s*.</span></span> <span data-ttu-id="408c7-173">如果选择了**课程 (ContosoUniversity.Models)**，则**控制器名称**自动填充值。</span><span class="sxs-lookup"><span data-stu-id="408c7-173">When you selected **Course (ContosoUniversity.Models)**, the **Controller name** value was automatically populated.</span></span> <span data-ttu-id="408c7-174">必须更改的值。</span><span class="sxs-lookup"><span data-stu-id="408c7-174">You have to change the value.</span></span> |

<span data-ttu-id="408c7-175">保留其他默认值，并添加控制器。</span><span class="sxs-lookup"><span data-stu-id="408c7-175">Leave the other default values and add the controller.</span></span>

<span data-ttu-id="408c7-176">打开*Controllers\CourseController.cs*并查看`Index`方法：</span><span class="sxs-lookup"><span data-stu-id="408c7-176">Open *Controllers\CourseController.cs* and look at the `Index` method:</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

<span data-ttu-id="408c7-177">自动基架使用 `Include` 方法为 `Department` 导航属性指定了预先加载。</span><span class="sxs-lookup"><span data-stu-id="408c7-177">The automatic scaffolding has specified eager loading for the `Department` navigation property by using the `Include` method.</span></span>

<span data-ttu-id="408c7-178">打开*Views\Course\Index.cshtml*和模板代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="408c7-178">Open *Views\Course\Index.cshtml* and replace the template code with the following code.</span></span> <span data-ttu-id="408c7-179">突出显示所作更改：</span><span class="sxs-lookup"><span data-stu-id="408c7-179">The changes are highlighted:</span></span>

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cshtml?highlight=4,7,14-16,23-25,31-33,40-42)]

<span data-ttu-id="408c7-180">已对基架代码进行了如下更改：</span><span class="sxs-lookup"><span data-stu-id="408c7-180">You've made the following changes to the scaffolded code:</span></span>

- <span data-ttu-id="408c7-181">更改将标题从**索引**到**课程**。</span><span class="sxs-lookup"><span data-stu-id="408c7-181">Changed the heading from **Index** to **Courses**.</span></span>
- <span data-ttu-id="408c7-182">添加了显示 `CourseID` 属性值的“数字”列。</span><span class="sxs-lookup"><span data-stu-id="408c7-182">Added a **Number** column that shows the `CourseID` property value.</span></span> <span data-ttu-id="408c7-183">默认情况下，主键不是基架，因为它们通常是向最终用户无意义。</span><span class="sxs-lookup"><span data-stu-id="408c7-183">By default, primary keys aren't scaffolded because normally they are meaningless to end users.</span></span> <span data-ttu-id="408c7-184">但在这种情况下主键是有意义的，而你需要将其呈现出来。</span><span class="sxs-lookup"><span data-stu-id="408c7-184">However, in this case the primary key is meaningful and you want to show it.</span></span>
- <span data-ttu-id="408c7-185">移动**部门**列的右侧，更改其标题。</span><span class="sxs-lookup"><span data-stu-id="408c7-185">Moved the **Department** column to the right side and changed its heading.</span></span> <span data-ttu-id="408c7-186">基架正确选择显示`Name`属性从`Department`实体，但应为列标题的课程页中的此处**部门**而非**名称**。</span><span class="sxs-lookup"><span data-stu-id="408c7-186">The scaffolder correctly chose to display the `Name` property from the `Department` entity, but here in the Course page the column heading should be **Department** rather than **Name**.</span></span>

<span data-ttu-id="408c7-187">请注意，对于部门列中，基架的代码将显示`Name`的属性`Department`实体加载到`Department`导航属性：</span><span class="sxs-lookup"><span data-stu-id="408c7-187">Notice that for the Department column, the scaffolded code displays the `Name` property of the `Department` entity that's loaded into the `Department` navigation property:</span></span>

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=2)]

<span data-ttu-id="408c7-188">运行页 (选择**课程**Contoso University 主页上的选项卡) 若要查看包含系名称列表。</span><span class="sxs-lookup"><span data-stu-id="408c7-188">Run the page (select the **Courses** tab on the Contoso University home page) to see the list with department names.</span></span>

## <a name="create-an-instructors-page"></a><span data-ttu-id="408c7-189">创建讲师页</span><span class="sxs-lookup"><span data-stu-id="408c7-189">Create an Instructors page</span></span>

<span data-ttu-id="408c7-190">将在本部分中创建一个控制器并查看`Instructor`实体以显示讲师页。</span><span class="sxs-lookup"><span data-stu-id="408c7-190">In this section you'll create a controller and view for the `Instructor` entity in order to display the Instructors page.</span></span> <span data-ttu-id="408c7-191">该页面通过以下方式读取和显示相关数据：</span><span class="sxs-lookup"><span data-stu-id="408c7-191">This page reads and displays related data in the following ways:</span></span>

- <span data-ttu-id="408c7-192">讲师列表显示的相关的数据`OfficeAssignment`实体。</span><span class="sxs-lookup"><span data-stu-id="408c7-192">The list of instructors displays related data from the `OfficeAssignment` entity.</span></span> <span data-ttu-id="408c7-193">`Instructor` 和 `OfficeAssignment` 实体之间存在一对零或一的关系。</span><span class="sxs-lookup"><span data-stu-id="408c7-193">The `Instructor` and `OfficeAssignment` entities are in a one-to-zero-or-one relationship.</span></span> <span data-ttu-id="408c7-194">你将使用预先加载`OfficeAssignment`实体。</span><span class="sxs-lookup"><span data-stu-id="408c7-194">You'll use eager loading for the `OfficeAssignment` entities.</span></span> <span data-ttu-id="408c7-195">如前所述，需要主表所有检索行的相关数据时，预先加载通常更有效。</span><span class="sxs-lookup"><span data-stu-id="408c7-195">As explained earlier, eager loading is typically more efficient when you need the related data for all retrieved rows of the primary table.</span></span> <span data-ttu-id="408c7-196">在这种情况下，你希望显示所有显示的讲师的办公室分配情况。</span><span class="sxs-lookup"><span data-stu-id="408c7-196">In this case, you want to display office assignments for all displayed instructors.</span></span>
- <span data-ttu-id="408c7-197">当用户选择一名讲师，相关`Course`实体的显示。</span><span class="sxs-lookup"><span data-stu-id="408c7-197">When the user selects an instructor, related `Course` entities are displayed.</span></span> <span data-ttu-id="408c7-198">`Instructor` 和 `Course` 实体之间存在多对多关系。</span><span class="sxs-lookup"><span data-stu-id="408c7-198">The `Instructor` and `Course` entities are in a many-to-many relationship.</span></span> <span data-ttu-id="408c7-199">你将使用预先加载`Course`实体和其相关`Department`实体。</span><span class="sxs-lookup"><span data-stu-id="408c7-199">You'll use eager loading for the `Course` entities and their related `Department` entities.</span></span> <span data-ttu-id="408c7-200">在这种情况下，可能延迟加载是更高效，因为你需要仅对所选讲师的课程。</span><span class="sxs-lookup"><span data-stu-id="408c7-200">In this case, lazy loading might be more efficient because you need courses only for the selected instructor.</span></span> <span data-ttu-id="408c7-201">但此示例显示的是如何在本身就位于导航属性内的实体中预先加载导航属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-201">However, this example shows how to use eager loading for navigation properties within entities that are themselves in navigation properties.</span></span>
- <span data-ttu-id="408c7-202">当用户选择一门课程时，与相关的数据从`Enrollments`显示实体集。</span><span class="sxs-lookup"><span data-stu-id="408c7-202">When the user selects a course, related data from the `Enrollments` entity set is displayed.</span></span> <span data-ttu-id="408c7-203">`Course` 和 `Enrollment` 实体之间存在一对多的关系。</span><span class="sxs-lookup"><span data-stu-id="408c7-203">The `Course` and `Enrollment` entities are in a one-to-many relationship.</span></span> <span data-ttu-id="408c7-204">你将添加用于显式加载`Enrollment`实体和其相关`Student`实体。</span><span class="sxs-lookup"><span data-stu-id="408c7-204">You'll add explicit loading for `Enrollment` entities and their related `Student` entities.</span></span> <span data-ttu-id="408c7-205">（显式加载无需因为启用了延迟加载，但这将显示如何执行显式加载。）</span><span class="sxs-lookup"><span data-stu-id="408c7-205">(Explicit loading isn't necessary because lazy loading is enabled, but this shows how to do explicit loading.)</span></span>

### <a name="create-a-view-model-for-the-instructor-index-view"></a><span data-ttu-id="408c7-206">为讲师索引视图创建视图模型</span><span class="sxs-lookup"><span data-stu-id="408c7-206">Create a View Model for the Instructor Index View</span></span>

<span data-ttu-id="408c7-207">讲师页显示了三个不同的表。</span><span class="sxs-lookup"><span data-stu-id="408c7-207">The Instructors page shows three different tables.</span></span> <span data-ttu-id="408c7-208">因此将创建包含三个属性的视图模型，每个属性都包含一个表的数据。</span><span class="sxs-lookup"><span data-stu-id="408c7-208">Therefore, you'll create a view model that includes three properties, each holding the data for one of the tables.</span></span>

<span data-ttu-id="408c7-209">在中*Viewmodel*文件夹中，创建*InstructorIndexData.cs*和现有代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="408c7-209">In the *ViewModels* folder, create *InstructorIndexData.cs* and replace the existing code with the following code:</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

### <a name="create-the-instructor-controller-and-views"></a><span data-ttu-id="408c7-210">创建讲师控制器和视图</span><span class="sxs-lookup"><span data-stu-id="408c7-210">Create the Instructor Controller and Views</span></span>

<span data-ttu-id="408c7-211">创建`InstructorController`(不 InstructorsController) 使用 EF 读/写操作的控制器：</span><span class="sxs-lookup"><span data-stu-id="408c7-211">Create an `InstructorController` (not InstructorsController) controller with EF read/write action:</span></span>

| <span data-ttu-id="408c7-212">设置</span><span class="sxs-lookup"><span data-stu-id="408c7-212">Setting</span></span> | <span data-ttu-id="408c7-213">值</span><span class="sxs-lookup"><span data-stu-id="408c7-213">Value</span></span> |
| ------- | ----- |
| <span data-ttu-id="408c7-214">Model 类</span><span class="sxs-lookup"><span data-stu-id="408c7-214">Model class</span></span> | <span data-ttu-id="408c7-215">选择**讲师 (ContosoUniversity.Models)**。</span><span class="sxs-lookup"><span data-stu-id="408c7-215">Select **Instructor (ContosoUniversity.Models)**.</span></span> |
| <span data-ttu-id="408c7-216">数据上下文类</span><span class="sxs-lookup"><span data-stu-id="408c7-216">Data context class</span></span> | <span data-ttu-id="408c7-217">选择**SchoolContext (ContosoUniversity.DAL)**。</span><span class="sxs-lookup"><span data-stu-id="408c7-217">Select **SchoolContext (ContosoUniversity.DAL)**.</span></span> |
| <span data-ttu-id="408c7-218">控制器名称</span><span class="sxs-lookup"><span data-stu-id="408c7-218">Controller name</span></span> | <span data-ttu-id="408c7-219">输入*InstructorController*。</span><span class="sxs-lookup"><span data-stu-id="408c7-219">Enter *InstructorController*.</span></span> <span data-ttu-id="408c7-220">同样，不*InstructorsController*与*s*。</span><span class="sxs-lookup"><span data-stu-id="408c7-220">Again, not *InstructorsController* with an *s*.</span></span> <span data-ttu-id="408c7-221">如果选择了**课程 (ContosoUniversity.Models)**，则**控制器名称**自动填充值。</span><span class="sxs-lookup"><span data-stu-id="408c7-221">When you selected **Course (ContosoUniversity.Models)**, the **Controller name** value was automatically populated.</span></span> <span data-ttu-id="408c7-222">必须更改的值。</span><span class="sxs-lookup"><span data-stu-id="408c7-222">You have to change the value.</span></span> |

<span data-ttu-id="408c7-223">保留其他默认值，并添加控制器。</span><span class="sxs-lookup"><span data-stu-id="408c7-223">Leave the other default values and add the controller.</span></span>

<span data-ttu-id="408c7-224">打开*Controllers\InstructorController.cs*并添加`using`语句`ViewModels`命名空间：</span><span class="sxs-lookup"><span data-stu-id="408c7-224">Open *Controllers\InstructorController.cs* and add a `using` statement for the `ViewModels` namespace:</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

<span data-ttu-id="408c7-225">中的基架的代码`Index`方法指定预先加载仅`OfficeAssignment`导航属性：</span><span class="sxs-lookup"><span data-stu-id="408c7-225">The scaffolded code in the `Index` method specifies eager loading only for the `OfficeAssignment` navigation property:</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

<span data-ttu-id="408c7-226">替换为`Index`方法与以下代码以加载其他相关数据并将其放在视图模型中：</span><span class="sxs-lookup"><span data-stu-id="408c7-226">Replace the `Index` method with the following code to load additional related data and put it in the view model:</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

<span data-ttu-id="408c7-227">该方法接受可选路由数据 (`id`) 和查询字符串参数 (`courseID`)，提供 ID 值的所选的讲师和课程，并将所有所需的数据传递给视图。</span><span class="sxs-lookup"><span data-stu-id="408c7-227">The method accepts optional route data (`id`) and a query string parameter (`courseID`) that provide the ID values of the selected instructor and selected course, and passes all of the required data to the view.</span></span> <span data-ttu-id="408c7-228">参数由页面上的“选择”超链接提供。</span><span class="sxs-lookup"><span data-stu-id="408c7-228">The parameters are provided by the **Select** hyperlinks on the page.</span></span>

<span data-ttu-id="408c7-229">代码先创建一个视图模型实例，并在其中放入讲师列表。</span><span class="sxs-lookup"><span data-stu-id="408c7-229">The code begins by creating an instance of the view model and putting in it the list of instructors.</span></span> <span data-ttu-id="408c7-230">该代码指定预先加载`Instructor.OfficeAssignment`和`Instructor.Courses`导航属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-230">The code specifies eager loading for the `Instructor.OfficeAssignment` and the `Instructor.Courses` navigation property.</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs?highlight=3-4)]

<span data-ttu-id="408c7-231">第二个`Include`方法加载课程，并为每个课程加载完成了预先加载`Course.Department`导航属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-231">The second `Include` method loads Courses, and for each Course that is loaded it does eager loading for the `Course.Department` navigation property.</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

<span data-ttu-id="408c7-232">正如前面提到，预先加载不是必需的但做是为了提高性能。</span><span class="sxs-lookup"><span data-stu-id="408c7-232">As mentioned previously, eager loading is not required but is done to improve performance.</span></span> <span data-ttu-id="408c7-233">由于视图始终需要`OfficeAssignment`实体，它是更有效地在同一查询中提取的。</span><span class="sxs-lookup"><span data-stu-id="408c7-233">Since the view always requires the `OfficeAssignment` entity, it's more efficient to fetch that in the same query.</span></span> <span data-ttu-id="408c7-234">`Course` 实体是必需的因此预先加载是优于延迟加载，仅当与选中课程更频繁地显示的页中 web 页上，选择一名讲师时。</span><span class="sxs-lookup"><span data-stu-id="408c7-234">`Course` entities are required when an instructor is selected in the web page, so eager loading is better than lazy loading only if the page is displayed more often with a course selected than without.</span></span>

<span data-ttu-id="408c7-235">如果选择了讲师 ID，则从视图模型中的讲师列表检索所选的讲师。</span><span class="sxs-lookup"><span data-stu-id="408c7-235">If an instructor ID was selected, the selected instructor is retrieved from the list of instructors in the view model.</span></span> <span data-ttu-id="408c7-236">视图模型`Courses`属性然后加载`Course`讲师实体`Courses`导航属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-236">The view model's `Courses` property is then loaded with the `Course` entities from that instructor's `Courses` navigation property.</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

<span data-ttu-id="408c7-237">`Where`方法将返回一个集合，但在这种情况下只有一个在向该方法传入条件`Instructor`所返回的实体。</span><span class="sxs-lookup"><span data-stu-id="408c7-237">The `Where` method returns a collection, but in this case the criteria passed to that method result in only a single `Instructor` entity being returned.</span></span> <span data-ttu-id="408c7-238">`Single`方法将集合转换为一个`Instructor`实体，这将使您可以访问该实体的`Courses`属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-238">The `Single` method converts the collection into a single `Instructor` entity, which gives you access to that entity's `Courses` property.</span></span>

<span data-ttu-id="408c7-239">您使用[单个](https://msdn.microsoft.com/library/system.linq.enumerable.single.aspx)集合时知道集合上的方法将具有只有一个项。</span><span class="sxs-lookup"><span data-stu-id="408c7-239">You use the [Single](https://msdn.microsoft.com/library/system.linq.enumerable.single.aspx) method on a collection when you know the collection will have only one item.</span></span> <span data-ttu-id="408c7-240">`Single`方法将引发异常，如果传递给它的集合为空或多个项。</span><span class="sxs-lookup"><span data-stu-id="408c7-240">The `Single` method throws an exception if the collection passed to it is empty or if there's more than one item.</span></span> <span data-ttu-id="408c7-241">一种替代方法是[SingleOrDefault](https://msdn.microsoft.com/library/bb342451.aspx)，它返回默认值 (`null`这种情况下) 是否为空集合。</span><span class="sxs-lookup"><span data-stu-id="408c7-241">An alternative is [SingleOrDefault](https://msdn.microsoft.com/library/bb342451.aspx), which returns a default value (`null` in this case) if the collection is empty.</span></span> <span data-ttu-id="408c7-242">但是，在这种情况下，仍会引发异常 (尝试查找`Courses`属性上的`null`引用)，和异常消息会清楚指出问题的原因。</span><span class="sxs-lookup"><span data-stu-id="408c7-242">However, in this case that would still result in an exception (from trying to find a `Courses` property on a `null` reference), and the exception message would less clearly indicate the cause of the problem.</span></span> <span data-ttu-id="408c7-243">当您调用`Single`方法，您还可以传入`Where`条件而不是调用`Where`方法分开：</span><span class="sxs-lookup"><span data-stu-id="408c7-243">When you call the `Single` method, you can also pass in the `Where` condition instead of calling the `Where` method separately:</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cs)]

<span data-ttu-id="408c7-244">而不是：</span><span class="sxs-lookup"><span data-stu-id="408c7-244">Instead of:</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

<span data-ttu-id="408c7-245">接着，如果选择了课程，则从视图模型中的课程列表中检索所选课程。</span><span class="sxs-lookup"><span data-stu-id="408c7-245">Next, if a course was selected, the selected course is retrieved from the list of courses in the view model.</span></span> <span data-ttu-id="408c7-246">然后，视图模型的`Enrollments`属性加载`Enrollment`从该课程实体`Enrollments`导航属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-246">Then the view model's `Enrollments` property is loaded with the `Enrollment` entities from that course's `Enrollments` navigation property.</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs)]

### <a name="modify-the-instructor-index-view"></a><span data-ttu-id="408c7-247">修改讲师索引视图</span><span class="sxs-lookup"><span data-stu-id="408c7-247">Modify the Instructor Index View</span></span>

<span data-ttu-id="408c7-248">在中*Views\Instructor\Index.cshtml*，模板代码替换为以下代码。</span><span class="sxs-lookup"><span data-stu-id="408c7-248">In *Views\Instructor\Index.cshtml*, replace the template code with the following code.</span></span> <span data-ttu-id="408c7-249">突出显示所作更改：</span><span class="sxs-lookup"><span data-stu-id="408c7-249">The changes are highlighted:</span></span>

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cshtml?highlight=1,4,14-18,21,23-28,38-43,45)]

<span data-ttu-id="408c7-250">已对现有代码进行了如下更改：</span><span class="sxs-lookup"><span data-stu-id="408c7-250">You've made the following changes to the existing code:</span></span>

- <span data-ttu-id="408c7-251">将模型类更改为了 `InstructorIndexData`。</span><span class="sxs-lookup"><span data-stu-id="408c7-251">Changed the model class to `InstructorIndexData`.</span></span>
- <span data-ttu-id="408c7-252">将页标题从“索引”更改为了“讲师”。</span><span class="sxs-lookup"><span data-stu-id="408c7-252">Changed the page title from **Index** to **Instructors**.</span></span>
- <span data-ttu-id="408c7-253">添加**Office**显示的列`item.OfficeAssignment.Location`仅当`item.OfficeAssignment`不为 null。</span><span class="sxs-lookup"><span data-stu-id="408c7-253">Added an **Office** column that displays `item.OfficeAssignment.Location` only if `item.OfficeAssignment` is not null.</span></span> <span data-ttu-id="408c7-254">(这是一对零或一一关系，因为可能不会有相关`OfficeAssignment`实体。)</span><span class="sxs-lookup"><span data-stu-id="408c7-254">(Because this is a one-to-zero-or-one relationship, there might not be a related `OfficeAssignment` entity.)</span></span>

    [!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cshtml)]
- <span data-ttu-id="408c7-255">添加了的代码，将动态添加`class="success"`到`tr`的所选讲师的元素。</span><span class="sxs-lookup"><span data-stu-id="408c7-255">Added code that will dynamically add `class="success"` to the `tr` element of the selected instructor.</span></span> <span data-ttu-id="408c7-256">这将设置为所选的行使用背景色[Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap)类。</span><span class="sxs-lookup"><span data-stu-id="408c7-256">This sets a background color for the selected row using a [Bootstrap](../../../../visual-studio/overview/2013/creating-web-projects-in-visual-studio.md#bootstrap) class.</span></span>

    [!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml)]
- <span data-ttu-id="408c7-257">添加了新`ActionLink`标记为**选择**之前每个行中的其他链接，这将导致所选的讲师 ID 发送到`Index`方法。</span><span class="sxs-lookup"><span data-stu-id="408c7-257">Added a new `ActionLink` labeled **Select** immediately before the other links in each row, which causes the selected instructor ID to be sent to the `Index` method.</span></span>

<span data-ttu-id="408c7-258">运行应用程序，并选择**讲师**选项卡。页将显示`Location`属性的相关`OfficeAssignment`实体和一个空表单元时有无相关`OfficeAssignment`实体。</span><span class="sxs-lookup"><span data-stu-id="408c7-258">Run the application and select the **Instructors** tab. The page displays the `Location` property of related `OfficeAssignment` entities and an empty table cell when there's no related `OfficeAssignment` entity.</span></span>

<span data-ttu-id="408c7-259">在中*Views\Instructor\Index.cshtml*文件之后，结束`table`元素 （在文件末尾），添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="408c7-259">In the *Views\Instructor\Index.cshtml* file, after the closing `table` element (at the end of the file), add the following code.</span></span> <span data-ttu-id="408c7-260">选择讲师时，此代码显示与讲师相关的课程列表。</span><span class="sxs-lookup"><span data-stu-id="408c7-260">This code displays a list of courses related to an instructor when an instructor is selected.</span></span>

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cshtml)]

<span data-ttu-id="408c7-261">此代码读取视图模型的 `Courses` 属性以显示课程列表。</span><span class="sxs-lookup"><span data-stu-id="408c7-261">This code reads the `Courses` property of the view model to display a list of courses.</span></span> <span data-ttu-id="408c7-262">它还提供了`Select`发送到所选课程的 ID 的超链接`Index`操作方法。</span><span class="sxs-lookup"><span data-stu-id="408c7-262">It also provides a `Select` hyperlink that sends the ID of the selected course to the `Index` action method.</span></span>

<span data-ttu-id="408c7-263">运行该页并选择讲师。</span><span class="sxs-lookup"><span data-stu-id="408c7-263">Run the page and select an instructor.</span></span> <span data-ttu-id="408c7-264">此时会出现一个网格，其中显示有分配给所选讲师的课程，且还显示有每个课程的分配系的名称。</span><span class="sxs-lookup"><span data-stu-id="408c7-264">Now you see a grid that displays courses assigned to the selected instructor, and for each course you see the name of the assigned department.</span></span>

<span data-ttu-id="408c7-265">在刚添加的代码块之后，添加以下代码。</span><span class="sxs-lookup"><span data-stu-id="408c7-265">After the code block you just added, add the following code.</span></span> <span data-ttu-id="408c7-266">选择课程后，代码将显示参与课程的学生列表。</span><span class="sxs-lookup"><span data-stu-id="408c7-266">This displays a list of the students who are enrolled in a course when that course is selected.</span></span>

[!code-cshtml[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml)]

<span data-ttu-id="408c7-267">此代码读取`Enrollments`以便显示学生列表视图模型属性参加该课程。</span><span class="sxs-lookup"><span data-stu-id="408c7-267">This code reads the `Enrollments` property of the view model in order to display a list of students enrolled in the course.</span></span>

<span data-ttu-id="408c7-268">运行该页并选择讲师。</span><span class="sxs-lookup"><span data-stu-id="408c7-268">Run the page and select an instructor.</span></span> <span data-ttu-id="408c7-269">然后选择一门课程，查看参与的学生列表及其成绩。</span><span class="sxs-lookup"><span data-stu-id="408c7-269">Then select a course to see the list of enrolled students and their grades.</span></span>

### <a name="adding-explicit-loading"></a><span data-ttu-id="408c7-270">添加显式加载</span><span class="sxs-lookup"><span data-stu-id="408c7-270">Adding Explicit Loading</span></span>

<span data-ttu-id="408c7-271">打开*InstructorController.cs* ，并查看如何`Index`方法获取所选课程的注册列表：</span><span class="sxs-lookup"><span data-stu-id="408c7-271">Open *InstructorController.cs* and look at how the `Index` method gets the list of enrollments for a selected course:</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs)]

<span data-ttu-id="408c7-272">当检索讲师列表时，您指定了预先加载`Courses`导航属性和`Department`属性的每个课程。</span><span class="sxs-lookup"><span data-stu-id="408c7-272">When you retrieved the list of instructors, you specified eager loading for the `Courses` navigation property and for the `Department` property of each course.</span></span> <span data-ttu-id="408c7-273">然后您将放`Courses`集合视图模型中，现在正在访问`Enrollments`从该集合中的一个实体的导航属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-273">Then you put the `Courses` collection in the view model, and now you're accessing the `Enrollments` navigation property from one entity in that collection.</span></span> <span data-ttu-id="408c7-274">因为未指定预先加载`Course.Enrollments`导航属性，作为延迟加载结果页中显示来自该属性的数据。</span><span class="sxs-lookup"><span data-stu-id="408c7-274">Because you didn't specify eager loading for the `Course.Enrollments` navigation property, the data from that property is appearing in the page as a result of lazy loading.</span></span>

<span data-ttu-id="408c7-275">如果无需更改代码以任何方式禁用延迟加载`Enrollments`属性将为 null 而不考虑该课程实际上有多少注册。</span><span class="sxs-lookup"><span data-stu-id="408c7-275">If you disabled lazy loading without changing the code in any other way, the `Enrollments` property would be null regardless of how many enrollments the course actually had.</span></span> <span data-ttu-id="408c7-276">在此情况下，若要加载`Enrollments`属性，您将需要指定预先加载还是显式加载。</span><span class="sxs-lookup"><span data-stu-id="408c7-276">In that case, to load the `Enrollments` property, you'd have to specify either eager loading or explicit loading.</span></span> <span data-ttu-id="408c7-277">您所见如何执行预先加载。</span><span class="sxs-lookup"><span data-stu-id="408c7-277">You've already seen how to do eager loading.</span></span> <span data-ttu-id="408c7-278">若要查看的显式加载示例，请替换`Index`方法显式加载的以下代码替换`Enrollments`属性。</span><span class="sxs-lookup"><span data-stu-id="408c7-278">In order to see an example of explicit loading, replace the `Index` method with the following code, which explicitly loads the `Enrollments` property.</span></span> <span data-ttu-id="408c7-279">突出显示更改的代码。</span><span class="sxs-lookup"><span data-stu-id="408c7-279">The code changed are highlighted.</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cs?highlight=20-31)]

<span data-ttu-id="408c7-280">获取所选后`Course`实体，新代码，显式加载该课程`Enrollments`导航属性：</span><span class="sxs-lookup"><span data-stu-id="408c7-280">After getting the selected `Course` entity, the new code explicitly loads that course's `Enrollments` navigation property:</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cs)]

<span data-ttu-id="408c7-281">然后它显式加载每个`Enrollment`实体的相关`Student`实体：</span><span class="sxs-lookup"><span data-stu-id="408c7-281">Then it explicitly loads each `Enrollment` entity's related `Student` entity:</span></span>

[!code-csharp[Main](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cs)]

<span data-ttu-id="408c7-282">请注意，使用`Collection`方法以加载集合属性，但对于一个属性，保存只是一个实体，你使用`Reference`方法。</span><span class="sxs-lookup"><span data-stu-id="408c7-282">Notice that you use the `Collection` method to load a collection property, but for a property that holds just one entity, you use the `Reference` method.</span></span>

<span data-ttu-id="408c7-283">立即运行讲师索引页，尽管已经更改数据的检索方式，您将看到的页上，显示的内容没有区别。</span><span class="sxs-lookup"><span data-stu-id="408c7-283">Run the Instructor Index page now and you'll see no difference in what's displayed on the page, although you've changed how the data is retrieved.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="408c7-284">其他资源</span><span class="sxs-lookup"><span data-stu-id="408c7-284">Additional resources</span></span>

<span data-ttu-id="408c7-285">其他实体框架资源的链接可在[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。</span><span class="sxs-lookup"><span data-stu-id="408c7-285">Links to other Entity Framework resources can be found in the [ASP.NET Data Access - Recommended Resources](../../../../whitepapers/aspnet-data-access-content-map.md).</span></span>

## <a name="next-steps"></a><span data-ttu-id="408c7-286">后续步骤</span><span class="sxs-lookup"><span data-stu-id="408c7-286">Next steps</span></span>

<span data-ttu-id="408c7-287">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="408c7-287">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="408c7-288">介绍了如何加载相关的数据</span><span class="sxs-lookup"><span data-stu-id="408c7-288">Learned how to load related data</span></span>
> * <span data-ttu-id="408c7-289">创建课程页</span><span class="sxs-lookup"><span data-stu-id="408c7-289">Created a Courses page</span></span>
> * <span data-ttu-id="408c7-290">创建讲师页</span><span class="sxs-lookup"><span data-stu-id="408c7-290">Created an Instructors page</span></span>

<span data-ttu-id="408c7-291">转到下一步的文章，了解如何更新相关的数据。</span><span class="sxs-lookup"><span data-stu-id="408c7-291">Advance to the next article to learn how to update related data.</span></span>

> [!div class="nextstepaction"]
> [<span data-ttu-id="408c7-292">更新相关数据</span><span class="sxs-lookup"><span data-stu-id="408c7-292">Update related data</span></span>](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)