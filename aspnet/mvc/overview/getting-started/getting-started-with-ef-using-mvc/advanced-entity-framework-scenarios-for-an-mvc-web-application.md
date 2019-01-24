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
# <a name="tutorial-learn-about-advanced-ef-scenarios-for-an-mvc-5-web-app"></a>教程：了解有关 EF 的 MVC 5 Web 应用的高级方案

在上一教程中，您将实现每个层次结构一个表继承。 本教程包括引入了几个主题都有可用于注意当超出使用 Entity Framework Code First 开发 ASP.NET web 应用程序的基础知识。 第一个几部分提供的分步说明，引导您完成代码，并使用 Visual Studio 来完成任务以下各节简要的概述，然后链接到有关详细信息的资源与引入几个主题。

对于大多数这些主题，您将使用已创建的页。 若要使用原始 SQL 执行批量更新将创建新页面的更新的数据库中的所有课程的可修读人数：

![Update_Course_Credits_initial_page](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

在本教程中，你将了解：

> [!div class="checklist"]
> * 执行原始 SQL 查询
> * 执行非跟踪查询
> * 检查 SQL 查询发送到数据库

此外将了解如何：

> [!div class="checklist"]
> * 创建一个抽象层
> * 代理类
> * 自动脏值检测
> * 自动验证
> * Entity Framework Power Tools
> * 实体框架源代码

## <a name="prerequisite"></a>必备组件

* [实现继承](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="perform-raw-sql-queries"></a>执行原始 SQL 查询

实体框架 Code First API 包括使您能够直接向数据库传递 SQL 命令的方法。 有下列选项：

- 使用[DbSet.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.dbset.sqlquery.aspx)返回实体类型的查询的方法。 返回的对象必须是预期的类型的`DbSet`对象，并且它们会自动跟踪的数据库上下文除非关闭跟踪。 (参见下一节[AsNoTracking](https://msdn.microsoft.com/library/system.data.entity.dbextensions.asnotracking.aspx)方法。)
- 使用[Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery.aspx)方法的返回类型不是实体的查询。 数据库上下文不会跟踪返回的数据，即使你使用该方法来检索实体类型也是如此。
- 使用[Database.ExecuteSqlCommand](https://msdn.microsoft.com/library/gg679456.aspx)非查询命令。

使用 Entity Framework 的优点之一是它可避免你编写跟数据库过于耦合的代码 它会自动生成 SQL 查询和命令，使得你无需自行编写。 当您需要运行的手动创建，特定 SQL 查询时有一些特殊情况，但这些方法使你能够处理此类异常。

在 Web 应用程序中执行 SQL 命令时，请务必采取预防措施来保护站点免受 SQL 注入攻击。 一种方法是使用参数化查询，确保不会将网页提交的字符串视为 SQL 命令。 在本教程中，将用户输入集成到查询中时会使用参数化查询。

### <a name="calling-a-query-that-returns-entities"></a>调用一个查询返回实体

[DbSet&lt;TEntity&gt; ](https://msdn.microsoft.com/library/gg696460.aspx)类提供了可用于执行查询的返回类型的实体的方法`TEntity`。 若要查看细节，你将更改中的代码`Details`方法的`Department`控制器。

在*DepartmentController.cs*，在`Details`方法中，替换`db.Departments.FindAsync`使用的方法调用`db.Departments.SqlQuery`方法调用，如以下突出显示的代码中所示：

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample1.cs?highlight=8-14)]

为了验证新代码是否工作正常，请选择**Department**选项卡，然后点击某个部门的**Detail**。 请确保所有数据将按预期方式显示。

### <a name="calling-a-query-that-returns-other-types-of-objects"></a>调用一个查询返回其他类型的对象

之前你在“关于”页面创建了一个学生统计信息网格，显示每个注册日期的学生数量。 此代码*HomeController.cs*使用 LINQ:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample2.cs)]

假设你想要编写检索此数据直接在 SQL，而不是使用 LINQ 中的代码。 若要执行此操作需要运行的查询返回实体对象以外的内容，这意味着您需要使用[Database.SqlQuery](https://msdn.microsoft.com/library/system.data.entity.database.sqlquery(v=VS.103).aspx)方法。

在中*HomeController.cs*，将 LINQ 语句中的为`About`方法使用 SQL 语句，如以下突出显示的代码中所示：

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample3.cs?highlight=3-18)]

运行关于页面。 验证，它将显示它以前的相同的数据。

### <a name="calling-an-update-query"></a>调用更新查询

假设 Contoso 大学管理员想要能够在数据库中，如更改的每个课程的可修读人数执行大容量更改。 如果该大学有大量的课程，检索所有实体并单独更改会降低效率。 在本部分中，你将实现一个网页，使用户能够指定一个参数，通过要更改所有课程的可修读人数和将通过执行 SQL 中进行更改`UPDATE`语句。 

在中*CourseContoller.cs*，添加`UpdateCourseCredits`方法`HttpGet`和`HttpPost`:

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample4.cs)]

当控制器处理`HttpGet`请求，未返回任何内容中`ViewBag.RowsAffected`变量，并查看显示的空文本框和一个提交按钮。

当**更新**单击按钮时，`HttpPost`调用方法时，和`multiplier`在文本框中输入的值。 代码然后执行 SQL 语句更新课程，并返回受影响的行数中的视图`ViewBag.RowsAffected`变量。 当视图在该变量中获取一个值时，它显示而不是在文本框中更新的行数，并提交按钮。

在中*CourseController.cs*，右键单击其中一个`UpdateCourseCredits`方法，并单击**添加视图**。 **添加视图**此时将显示对话框。 保留默认值并选择**添加**。

在中*Views\Course\UpdateCourseCredits.cshtml*，模板代码替换为以下代码：

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample5.cshtml)]

通过选择**Courses**选项卡运行`UpdateCourseCredits`方法，然后在浏览器地址栏中 URL 的末尾添加"/ UpdateCourseCredits"到 (例如： `http://localhost:50205/Course/UpdateCourseCredits`)。 在文本框中输入数字：

![Update_Course_Credits_initial_page_with_2_entered](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image1.png)

单击**Update**。 请参阅受影响的行数。

单击**Back To List**可以看到课程列表，其中可修读人数已经替换成修改后的数字。

有关原始 SQL 查询的详细信息，请参阅[原始 SQL 查询](https://msdn.microsoft.com/data/jj592907)MSDN 上。

## <a name="no-tracking-queries"></a>非跟踪查询

当数据库上下文检索表行并创建表示它们的实体对象时，默认情况下，它会跟踪内存中的实体是否与数据库中的内容同步。 更新实体时，内存中的数据充当缓存并使用该数据。 在 Web 应用程序中，此缓存通常是不必要的，因为上下文实例通常生存期较短（创建新的实例并用于处理每个请求），并且通常在再次使用该实体之前处理读取实体的上下文。

可以使用禁用的实体对象在内存中的跟踪[AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx)方法。 可能想要执行的典型方案包括以下操作：

- 查询将检索此类大型的数据量关闭跟踪可能会显著提高性能。
- 你想要附加一个实体来更新它，但前面要检索同一实体用于其他目的。 由于数据库上下文已跟踪了该实体，因此无法附加要更改的实体。 若要处理这种情况的一种方法是使用`AsNoTracking`与以前的查询选项。

有关示例，演示如何使用[AsNoTracking](https://msdn.microsoft.com/library/gg679352(v=vs.103).aspx)方法，请参阅[本教程的早期版本](../../older-versions/getting-started-with-ef-5-using-mvc-4/advanced-entity-framework-scenarios-for-an-mvc-web-application.md)。 在本教程的此版本不会修改标志实体上设置模型联编程序创建在编辑方法中，因此它不需要`AsNoTracking`。

## <a name="examine-sql-sent-to-database"></a>检查发送到数据库的 SQL

有时能够以查看发送到数据库的实际 SQL 查询对于开发者来说是很有用的。 在前面的教程中您已了解如何执行该操作在侦听器代码;现在您会看到无需编写侦听器代码执行此操作的一些方法。 若要试用此项，将看看简单查询并再看会发生什么情况向其添加此类预先加载、 筛选和排序选项。

在中*控制器/CourseController*，将为`Index`用下面的代码，若要暂时停止预先加载的方法：

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample6.cs)]

现在上设置断点`return`语句 (F9 在该行光标)。 按**F5**以在调试模式下运行该项目并选择课程索引页。 当代码到达该断点时，检查`sql`变量。 可以看到发送到 SQL Server 的查询。 它是一个简单`Select`语句。

[!code-json[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample7.json)]

单击以查看查询中的放大镜**文本可视化工具**。

![](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image10.png)

现在将向课程索引页面添加下拉列表，以便用户可以筛选为特定部门。 您将对课程进行排序的标题，并且你将指定预先加载`Department`导航属性。

在中*CourseController.cs*，将为`Index`方法使用以下代码：

[!code-csharp[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample8.cs)]

在还原该断点`return`语句。

该方法接收的下拉列表中所选的值`SelectedDepartment`参数。 如果未选择任何项，则此参数将为 null。

一个`SelectList`集合，其中包含所有部门的下拉列表将传递给视图。 参数传递给`SelectList`构造函数指定值字段名称、 文本字段名称和所选的项。

有关`Get`方法`Course`存储库中，代码将指定的筛选器表达式、 排序顺序和预先加载`Department`导航属性。 筛选器表达式始终返回`true`如果未选择下拉列表中 (即，`SelectedDepartment`为 null)。

在中*Views\Course\Index.cshtml*，打开之前立即`table`标记中，添加以下代码以创建下拉列表和一个提交按钮：

[!code-cshtml[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample9.cshtml)]

断点仍然设置，运行课程索引页。 继续操作，代码命中断点的行，第一次，以便将在浏览器中显示的页。 从下拉列表中选择一个部门，然后单击**筛选器**。

这一次第一个断点将在下拉列表的部门查询。 跳过该步骤，并查看`query`变量下一次代码达到断点才能看到什么`Course`查询现在如下所示。 你将看到类似于以下内容：

[!code-sql[Main](advanced-entity-framework-scenarios-for-an-mvc-web-application/samples/sample10.sql)]

您可以看到查询现`JOIN`加载的查询`Department`数据以及`Course`数据，并且必须包含`WHERE`子句。

删除`var sql = courses.ToString()`行。

## <a name="create-an-abstraction-layer"></a>创建一个抽象层

许多开发人员编写代码实现存储库和工作模式单元以作为使用 Entity Framework 代码的包装器。 这些模式用于在应用程序的数据访问层和业务逻辑层之间创建抽象层。 实现这些模式可让你的应用程序对数据存储介质的更改不敏感，而且很容易进行自动化单元测试和进行测试驱动开发 (TDD)。 但是，编写附加代码以实现这些模式并不总是有几个原因使用 EF 的应用程序的最佳选择：

- EF 上下文类可以为使用 EF 的数据库更新充当工作单位类。
- 对于使用 EF 进行的数据库更新，EF 上下文类可充当工作单元类。
- Entity Framework 6 中引入的功能使其更轻松地实现 TDD，而无需编写代码存储库。

有关如何实现存储库和工作单元模式的详细信息，请参阅[本系列教程的 Entity Framework 5 版本](../../older-versions/getting-started-with-ef-5-using-mvc-4/implementing-the-repository-and-unit-of-work-patterns-in-an-asp-net-mvc-application.md)。 方法在 Entity Framework 6 中实现 TDD 的信息，请参阅以下资源：

- [如何 EF6 通过模拟 Dbset 更轻松地](http://thedatafarm.com/data-access/how-ef6-enables-mocking-dbsets-more-easily/)
- [使用模拟框架进行测试](https://msdn.microsoft.com/data/dn314429)
- [使用你自己的 test double 进行测试](https://msdn.microsoft.com/data/dn314431)

<a id="proxies"></a>

## <a name="proxy-classes"></a>代理类

当 Entity Framework 创建实体实例 （例如，当执行查询时） 时，它通常创建其作为动态生成的派生类型的代理的实体的实例。 有关示例，请参阅以下两个调试程序映像。 在第一个图中，请参阅该`student`变量是预期`Student`类型实例化实体后立即。 在第二个图中，EF 已用于从数据库读取 student 实体后，请参阅代理类。

![代理类之前](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image12.png)

![在代理类](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image13.png)

此代理类重写要插入挂钩，以便访问属性时自动执行的操作的实体的某些虚拟属性。 有关使用此机制的一个函数是延迟加载。

大多数情况下无需注意此使用代理服务器，但有例外情况：

- 在某些情况下您可能想要阻止实体框架创建代理实例。 例如，要序列化实体时您通常希望 POCO 类，不使用代理类。 若要避免序列化问题的一种方法是要序列化数据传输对象 (Dto)，而不是实体对象，如中所示[使用实体框架使用 Web API](../../../../web-api/overview/data/using-web-api-with-entity-framework/part-1.md)教程。 另一种方法是向[禁用代理创建](https://msdn.microsoft.com/data/jj592886.aspx)。
- 当您实例化实体类使用`new`运算符，别让代理实例。 这意味着不会功能，如延迟加载和自动更改跟踪。 这通常是好了;您通常不需要延迟加载，因为您将创建新的实体不在数据库中，且通常无需更改跟踪，如果您显式标记为实体`Added`。 但是，如果您需要延迟加载，并且你需要更改跟踪，您可以创建新实体实例与使用的代理[创建](https://msdn.microsoft.com/library/gg679504.aspx)方法的`DbSet`类。
- 你可能想要从代理类型中获取的实际实体类型。 可以使用[GetObjectType](https://msdn.microsoft.com/library/system.data.objects.objectcontext.getobjecttype.aspx)方法的`ObjectContext`类，以获取代理类型实例的实际实体类型。

有关详细信息，请参阅[使用代理](https://msdn.microsoft.com/data/JJ592886.aspx)MSDN 上。

## <a name="automatic-change-detection"></a>自动脏值检测

Entity Framework 通过比较的实体的当前值与原始值来判断更改实体的方式 （因此需要发送更新到数据库）。 查询或附加该实体时会存储的原始值。 如下方法会导致自动脏值：

- `DbSet.Find`
- `DbSet.Local`
- `DbSet.Remove`
- `DbSet.Add`
- `DbSet.Attach`
- `DbContext.SaveChanges`
- `DbContext.GetValidationErrors`
- `DbContext.Entry`
- `DbChangeTracker.Entries`

如果正在跟踪大量实体，并且你调用下列方法之一很多时候在循环中，您可能会显著的性能改进，通过暂时关闭自动更改检测使用[AutoDetectChangesEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.autodetectchangesenabled.aspx)属性。 有关详细信息，请参阅[自动检测更改](https://msdn.microsoft.com/data/jj556205)MSDN 上。

## <a name="automatic-validation"></a>自动验证

当您调用`SaveChanges`方法中，默认情况下，实体框架中已更改的所有实体的所有属性的数据之前验证更新数据库。 如果你已更新大量实体和已验证了数据，这项工作是不必要和无法使保存的过程所做的更改通过暂时关闭验证需要更少的时间。 您可以执行操作，使用[ValidateOnSaveEnabled](https://msdn.microsoft.com/library/system.data.entity.infrastructure.dbcontextconfiguration.validateonsaveenabled.aspx)属性。 有关详细信息，请参阅[验证](https://msdn.microsoft.com/data/gg193959)MSDN 上。

## <a name="entity-framework-power-tools"></a>Entity Framework Power Tools

[Entity Framework Power Tools](https://marketplace.visualstudio.com/items?itemName=ErikEJ.EntityFramework6PowerToolsCommunityEdition) Visual Studio 外接程序，用于创建数据模型关系图显示在这些教程。 这些工具还可以执行如生成实体类基于现有数据库中的表，以便您可以使用数据库使用 Code First 其他函数。 安装工具后，在上下文菜单中显示一些附加选项。 例如，当您右键单击您的上下文类中**解决方案资源管理器**，您看到并**Entity Framework**选项。 这使您能够生成关系图。 在使用 Code First 时无法更改关系图中中的数据模型，但可以来回移动操作，以使其更易于理解。

![EF 关系图](advanced-entity-framework-scenarios-for-an-mvc-web-application/_static/image15.png)

## <a name="entity-framework-source-code"></a>实体框架源代码

Entity Framework 6 的源代码位于[GitHub](https://github.com/aspnet/EntityFramework6)。 可以报告 bug，并可以提供您自己的 EF 源代码的增强功能。

尽管源代码处于开源状态，但实体框架完全支持 Microsoft 产品。 Microsoft Entity Framework 团队将控制接受哪些贡献和测试所有的代码更改，以确保每个版本的质量。

## <a name="acknowledgments"></a>鸣谢

- Tom Dykstra 编写本教程中的原始版本，合著了 EF 5 更新，并编写 EF 6 更新。 Tom 是 Microsoft Web 平台和工具内容团队的资深编程作家。
- [Rick Anderson](https://blogs.msdn.com/b/rickandy/) (twitter [ @RickAndMSFT ](http://twitter.com/RickAndMSFT)) 执行大多数更新本教程的 EF 5 和 MVC 4 的工作，并且是 EF 6 更新。 Rick 是 Microsoft 将重点放在 Azure 和 MVC 的资深编程作家。
- [Rowan Miller](http://www.romiller.com)和实体框架团队的其他成员协助代码评审和帮助调试与我们正在对 EF 5 和 EF 6 更新本教程时出现的迁移的许多问题。

## <a name="troubleshoot-common-errors"></a>排查常见错误

### <a name="cannot-createshadow-copy"></a>无法创建/卷影副本

错误消息：

> 无法创建/卷影副本&lt;文件名&gt;文件已存在。

解决方案

等待几秒钟，并刷新页面。

### <a name="update-database-not-recognized"></a>更新数据库无法识别

错误消息 (从`Update-Database`命令在 PMC 中):

> 术语更新数据库未识别为 cmdlet、 函数、 脚本文件或可操作程序的名称。 检查名称的拼写或如果已包含路径，验证路径正确，然后重试。

解决方案

退出 Visual Studio。 重新打开项目，然后重试。

### <a name="validation-failed"></a>验证失败

错误消息 (从`Update-Database`命令在 PMC 中):

> 对一个或多个实体的验证失败。 请参阅 EntityValidationErrors 属性的更多详细信息。

解决方案

此问题的一个原因是验证错误时`Seed`方法运行。 请参阅[种子设定和调试 Entity Framework (EF) 数据库](https://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx)有关调试的提示`Seed`方法。

### <a name="http-50019-error"></a>HTTP 500.19 错误

错误消息：

> 不能访问 HTTP 错误 500.19-内部服务器错误，请求的页面，因为该页的相关的配置数据无效。

解决方案

出现此错误的一种方法是从拥有多个解决方案，它们使用相同的端口号的每个副本。 通常可以通过退出的 Visual Studio 中，所有实例，然后重新启动正在处理的项目来解决此问题。 如果这不起作用，请尝试更改端口号。 右键单击项目文件，然后单击属性。 选择**Web**选项卡，然后将更改中的端口号**项目 Url**文本框。

### <a name="error-locating-sql-server-instance"></a>定位 SQL Server 实例出错

错误消息：

> 建立到 SQL Server 的连接时出现与网络相关或特定于实例的错误。 未找到或无法访问服务器。 请验证实例名称是否正确，SQL Server 是否已配置为允许远程连接。 （提供程序：SQL 网络接口，错误：26 - 定位指定服务器/实例出错）

解决方案

请检查连接字符串。 如果你已手动删除数据库，更改构造字符串中的数据库的名称。

## <a name="get-the-code"></a>获取代码

[下载已完成的项目](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>其他资源

 有关如何使用实体框架处理数据的详细信息，请参阅[EF 在 MSDN 上的文档页](https://msdn.microsoft.com/data/ee712907)并[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。

有关如何部署 web 应用程序后已生成的详细信息，请参阅[ASP.NET Web 部署-推荐的资源](../../../../whitepapers/aspnet-web-deployment-content-map.md)MSDN 库中。

有关相关的 MVC 中，例如身份验证和授权，其他主题的信息，请参阅[ASP.NET MVC-推荐的资源](../recommended-resources-for-mvc.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 执行原始 SQL 查询
> * 执行非跟踪查询
> * 检查的 SQL 查询发送到数据库

你还了解了有关：

> [!div class="checklist"]
> * 创建一个抽象层
> * 代理类
> * 自动脏值检测
> * 自动验证
> * Entity Framework Power Tools
> * 实体框架源代码

这样就完成这一系列的 ASP.NET MVC 应用程序中使用实体框架的教程。 如果你想要了解有关 EF Database First 的信息，请参阅 DB 第一个教程系列。
> [!div class="nextstepaction"]
> [Entity Framework 数据库优先](../database-first-development/setting-up-database.md)