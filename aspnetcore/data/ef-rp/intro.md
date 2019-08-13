---
title: ASP.NET Core 中的 Razor 页面和 Entity Framework Core - 第 1 个教程（共 8 个）
author: rick-anderson
description: 介绍了如何使用 Entity Framework Core 创建 Razor 页面应用
ms.author: riande
ms.custom: mvc, seodec18
ms.date: 11/22/2018
uid: data/ef-rp/intro
ms.openlocfilehash: dea178d169cb8f2ae807caa2ef495d4243b1f59f
ms.sourcegitcommit: 3376f224b47a89acf329b2d2f9260046a372f924
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65516891"
---
# <a name="razor-pages-with-entity-framework-core-in-aspnet-core---tutorial-1-of-8"></a>ASP.NET Core 中的 Razor 页面和 Entity Framework Core - 第 1 个教程（共 8 个）

作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

Contoso University 示例 Web 应用演示了如何使用 Entity Framework (EF) Core 创建 ASP.NET Core Razor Pages 应用。

该示例应用是一个虚构的 Contoso University 的网站。 其中包括学生录取、课程创建和讲师分配等功能。 本页是介绍如何构建 Contoso University 示例应用系列教程中的第一部分。

[下载或查看已完成的应用。](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples) [下载说明](xref:index#how-to-download-a-sample)。

## <a name="prerequisites"></a>系统必备

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

[!INCLUDE [](~/includes/net-core-prereqs-windows.md)]

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

[!INCLUDE [](~/includes/2.1-SDK.md)]

---

熟悉 [Razor 页面](xref:razor-pages/index)。 新程序员在开始学习本系列之前，应先完成 [Razor 页面入门](xref:tutorials/razor-pages/razor-pages-start)。

## <a name="troubleshooting"></a>疑难解答

如果遇到无法解决的问题，可以通过与 [已完成的项目](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/data/ef-rp/intro/samples)对比代码来查找解决方案。 获取帮助的一个好方法是将问题发布到适用于 [ASP.NET Core](https://stackoverflow.com/questions/tagged/asp.net-core) 或 [EF Core](https://stackoverflow.com/questions/tagged/entity-framework-core) 的 [StackOverflow.com](https://stackoverflow.com/questions/tagged/asp.net-core)。

## <a name="the-contoso-university-web-app"></a>Contoso University Web 应用

这些教程中所构建的应用是一个基本的大学网站。

用户可以查看和更新学生、课程和讲师信息。 以下是在本教程中创建的几个屏幕。

![“学生索引”页](intro/_static/students-index.png)

![学生编辑页](intro/_static/student-edit.png)

此网站的 UI 样式与内置模板生成的 UI 样式类似。 教程的重点是 EF Core 和 Razor 页面，而非 UI。

## <a name="create-the-contosouniversity-razor-pages-web-app"></a>创建 ContosoUniversity Razor Pages Web 应用

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 从 Visual Studio“文件”菜单中选择“新建” > “项目”    。
* 创建新的 ASP.NET Core Web 应用呈现。 将该项目命名为 ContosoUniversity  。 务必将该项目命名为 ContosoUniversity，以便复制/粘贴代码时命名空间相匹配  。
* 在下拉列表中选择“ASP.NET Core 2.1”，然后选择“Web 应用程序”   。

有关上述步骤的图像，请参阅[创建 Razor Web 应用](xref:tutorials/razor-pages/razor-pages-start#create-a-razor-pages-web-app)。
运行应用。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

```CLI
dotnet new webapp -o ContosoUniversity
cd ContosoUniversity
dotnet run
```

---

## <a name="set-up-the-site-style"></a>设置网站样式

设置网站菜单、布局和主页时需作少量更改。 进行以下更改以更新 Pages/Shared/_Layout.cshtml  ：

* 将文件中的"ContosoUniversity"更改为"Contoso University"。 需要更改三个地方。

* 添加菜单项 **Students**，**Courses**，**Instructors**，和 **Department**，并删除 **Contact**菜单项。

突出显示所作更改。 （没有显示全部标记  。）

[!code-html[](intro/samples/cu21/Pages/Shared/_Layout.cshtml?highlight=6,29,35-38,50&name=snippet)]

在 Pages/Index.cshtml 中，将文件内容替换为以下代码，以将有关 ASP.NET 和 MVC 的文本替换为有关本应用的文本  ：

[!code-html[](intro/samples/cu21/Pages/Index.cshtml)]

## <a name="create-the-data-model"></a>创建数据模型

创建 Contoso University 应用的实体类。 从以下三个实体开始：

![Course-Enrollment-Student 数据模型关系图](intro/_static/data-model-diagram.png)

`Student` 和 `Enrollment` 实体之间存在一对多关系。 `Course` 和 `Enrollment` 实体之间存在一对多关系。 一名学生可以报名参加任意数量的课程。 一门课程中可以包含任意数量的学生。

以下部分将为这几个实体中的每一个实体创建一个类。

### <a name="the-student-entity"></a>Student 实体

![Student 实体关系图](intro/_static/student-entity.png)

创建 Models 文件夹  。 在 Models 文件夹中，使用以下代码创建一个名为 Student.cs 的类文件   ：

[!code-csharp[](intro/samples/cu21/Models/Student.cs?name=snippet_Intro)]

`ID` 属性成为此类对应的数据库 (DB) 表的主键列。 默认情况下，EF Core 将名为 `ID` 或 `classnameID` 的属性视为主键。 在 `classnameID` 中，`classname` 为类名称。 另一种自动识别的主键是上例中的 `StudentID`。

`Enrollments` 属性是[导航属性](/ef/core/modeling/relationships)。 导航属性链接到与此实体相关的其他实体。 在这种情况下，`Student entity` 的 `Enrollments` 属性包含与该 `Student` 相关的所有 `Enrollment` 实体。 例如，如果数据库中的 Student 行有两个相关的 Enrollment 行，则 `Enrollments` 导航属性包含这两个 `Enrollment` 实体。 相关的 `Enrollment` 行是 `StudentID` 列中包含该学生的主键值的行。 例如，假设 ID=1 的学生在 `Enrollment` 表中有两行。 `Enrollment` 表中有两行的 `StudentID` = 1。 `StudentID` 是 `Enrollment` 表中的外键，用于指定 `Student` 表中的学生。

如果导航属性包含多个实体，则导航属性必须是列表类型，例如 `ICollection<T>`。 可以指定 `ICollection<T>` 或诸如 `List<T>` 或 `HashSet<T>` 的类型。 使用 `ICollection<T>` 时，EF Core 会默认创建 `HashSet<T>` 集合。 包含多个实体的导航属性来自于多对多和一对多关系。

### <a name="the-enrollment-entity"></a>Enrollment 实体

![Enrollment 实体关系图](intro/_static/enrollment-entity.png)

在 Models 文件夹中，使用以下代码创建 Enrollment.cs   ：

[!code-csharp[](intro/samples/cu21/Models/Enrollment.cs?name=snippet_Intro)]

`EnrollmentID` 属性为主键。 `Student` 实体使用的是 `ID` 模式，而本实体使用的是 `classnameID` 模式。 通常情况下，开发者会选择一种模式并在整个数据模型中都使用该模式。 下一个教程将介绍如何使用不带类名的 ID，以便更轻松地在数据模型中实现集成。

`Grade` 属性为 `enum`。 `Grade` 声明类型后的`?`表示 `Grade` 属性可以为 null。 评级为 null 和评级为零是有区别的 --null 意味着评级未知或者尚未分配。

`StudentID` 属性是外键，其对应的导航属性为 `Student`。 `Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含一个 `Student` 实体。 `Student` 实体与 `Student.Enrollments` 导航属性不同，后者包含多个 `Enrollment` 实体。

`CourseID` 属性是外键，其对应的导航属性为 `Course`。 `Enrollment` 实体与一个 `Course` 实体相关联。

如果属性命名为 `<navigation property name><primary key property name>`，EF Core 会将其视为外键。 例如 `Student` 导航属性的 `StudentID`，因为 `Student` 实体的主键为 `ID`。 还可以将外键属性命名为 `<primary key property name>`。 例如 `CourseID`，因为 `Course` 实体的主键为 `CourseID`。

### <a name="the-course-entity"></a>Course 实体

![Course 实体关系图](intro/_static/course-entity.png)

在 Models 文件夹中，使用以下代码创建 Course.cs   ：

[!code-csharp[](intro/samples/cu21/Models/Course.cs?name=snippet_Intro)]

`Enrollments` 属性是导航属性。 `Course` 实体可与任意数量的 `Enrollment` 实体相关。

应用可以通过 `DatabaseGenerated` 特性指定主键，而无需靠数据库生成。

## <a name="scaffold-the-student-model"></a>为“学生”模型搭建基架

本部分将为“学生”模型搭建基架。 确切地说，基架工具将生成页面，用于对“学生”模型执行创建、读取、更新和删除 (CRUD) 操作。

* 生成项目。
* 创建 Pages/Students 文件夹  。

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

* 在“解决方案资源管理器”  中，右键单击“Pages/Students”  文件夹 > “添加”   > “新建基架项”  。
* 在“添加基架”  对话框中，依次选择“使用实体框架的 Razor Pages (CRUD)”   > “添加”  。

完成“使用实体框架(CRUD)添加 Razor Pages”对话框  ：

* 在“模型类”下拉列表中，选择“Student (ContosoUniversity.Models)”   。
* 在“数据上下文类”行中，选择加号 (+) 并将生成的名称更改为 ContosoUniversity.Models.SchoolContext    。
* 在“数据上下文类”下拉列表中，选择“ContosoUniversity.Models.SchoolContext”  
* 选择“添加”  。

![CRUD 对话框](intro/_static/s1.png)

如果对前面的步骤有疑问，请参阅[搭建“电影”模型的基架](xref:tutorials/razor-pages/model#scaffold-the-movie-model)。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli)

运行以下命令，搭建“学生”模型的基架。

```console
dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design --version 2.1.0
dotnet tool install --global dotnet-aspnet-codegenerator
dotnet aspnet-codegenerator razorpage -m Student -dc ContosoUniversity.Models.SchoolContext -udl -outDir Pages/Students --referenceScriptLibraries
```

---

搭建基架的过程会创建并更改以下文件：

### <a name="files-created"></a>创建的文件

* Pages/Students：“创建”、“删除”、“详细信息”、“编辑”、“索引”  。
* Data/SchoolContext.cs 

### <a name="file-updates"></a>文件更新

* *Startup.cs*：下一部分详细介绍对此文件所作的更改。
* *appsettings.json*：添加用于连接到本地数据库的连接字符串。

## <a name="examine-the-context-registered-with-dependency-injection"></a>检查通过依赖关系注入注册的上下文

ASP.NET Core 通过[依赖关系注入](xref:fundamentals/dependency-injection)进行生成。 服务（例如 EF Core 数据库上下文）在应用程序启动期间通过依赖关系注入进行注册。 需要这些服务（如 Razor 页面）的组件通过构造函数提供相应服务。 本教程的后续部分介绍了用于获取数据库上下文实例的构造函数代码。

基架工具自动创建 DB 上下文并将其注册到依赖关系注入容器。

在 Startup.cs 中检查 `ConfigureServices` 方法  。 基架添加了突出显示的行：

[!code-csharp[](intro/samples/cu21/Startup.cs?name=snippet_SchoolContext&highlight=13-14)]

通过调用 [DbContextOptions](/dotnet/api/microsoft.entityframeworkcore.dbcontextoptions) 对象中的一个方法将连接字符串名称传递到上下文。 进行本地开发时， [ASP.NET Core 配置系统](xref:fundamentals/configuration/index) 在 *appsettings.json* 文件中读取数据库连接字符串。

## <a name="update-main"></a>更新 main

在 Program.cs 中，修改 `Main` 方法以执行以下操作  ：

* 从依赖关系注入容器获取数据库上下文实例。
* 调用 [EnsureCreated](/dotnet/api/microsoft.entityframeworkcore.infrastructure.databasefacade.ensurecreated#Microsoft_EntityFrameworkCore_Infrastructure_DatabaseFacade_EnsureCreated)。
* `EnsureCreated` 方法完成时释放上下文。

下面的代码显示更新后的 Program.cs  文件。

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet)]

`EnsureCreated` 确保存在上下文数据库。 如果存在，则不需要任何操作。 如果不存在，则会创建数据库及其所有架构。 `EnsureCreated` 不使用迁移创建数据库。 使用 `EnsureCreated` 创建的数据库稍后无法使用迁移更新。

启动应用时会调用 `EnsureCreated`，以进行以下工作流：

* 删除数据库。
* 更改数据库架构（例如添加一个 `EmailAddress` 字段）。
* 运行应用。
* `EnsureCreated` 创建一个带有 `EmailAddress` 列的数据库。

架构快速演变时，在开发初期使用 `EnsureCreated` 很方便。 本教程后面将删除 DB 并使用迁移。

### <a name="test-the-app"></a>测试应用

运行应用并接受 cookie 策略。 此应用不保留个人信息。 有关 cookie 策略的信息，请参阅[欧盟一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。

* 依次选择“学生”链接、“新建”   。
* 测试“编辑”、“详细信息”和“删除”链接。

## <a name="examine-the-schoolcontext-db-context"></a>检查 SchoolContext DB 上下文

数据库上下文类是为给定数据模型协调 EF Core 功能的主类。 数据上下文派生自 [Microsoft.EntityFrameworkCore.DbContext](/dotnet/api/microsoft.entityframeworkcore.dbcontext)。 数据上下文指定数据模型中包含哪些实体。 在此项目中将数据库上下文类命名为 `SchoolContext`。

使用以下代码更新 SchoolContext.cs  ：

[!code-csharp[](intro/samples/cu21/Data/SchoolContext.cs?name=snippet_Intro&highlight=12-14)]

突出显示的代码为每个实体集创建 [DbSet\<TEntity>](/dotnet/api/microsoft.entityframeworkcore.dbset-1) 属性。 在 EF Core 术语中：

* 实体集通常对应一个数据库表。
* 实体对应表中的行。

可以省略 `DbSet<Enrollment>` 和 `DbSet<Course>`。 EF Core 隐式包含了它们，因为 `Student` 实体引用 `Enrollment` 实体，而 `Enrollment` 实体引用 `Course` 实体。 在本教程中，将 `DbSet<Enrollment>` 和 `DbSet<Course>` 保留在 `SchoolContext` 中。

### <a name="sql-server-express-localdb"></a>SQL Server Express LocalDB

连接字符串指定 [SQL Server LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb)。 LocalDB 是轻型版本 SQL Server Express 数据库引擎，专门针对应用开发，而非生产使用。 LocalDB 作为按需启动并在用户模式下运行的轻量级数据库没有复杂的配置。 默认情况下，LocalDB 会在 `C:/Users/<user>` 目录中创建 .mdf 数据库文件  。

## <a name="add-code-to-initialize-the-db-with-test-data"></a>添加代码，以使用测试数据初始化该数据库

EF Core 会创建一个空的数据库。 本部分中编写了 `Initialize` 方法来使用测试数据填充该数据库。

在 Data 文件夹中，新建一个名为 DbInitializer.cs 的类文件，并添加以下代码   ：

[!code-csharp[](intro/samples/cu21/Data/DbInitializer.cs?name=snippet_Intro)]

注意:上面的代码对命名空间使用 `Models` (`namespace ContosoUniversity.Models`)，而不是 `Data`。 `Models` 与基架生成的代码一致。 有关详细信息，请参阅[此 GitHub 基架问题](https://github.com/aspnet/Scaffolding/issues/822)。

该代码会检查数据库中是否存在任何学生。 如果 DB 中没有任何学生，则会使用测试数据初始化该 DB。 代码中使用数组存放测试数据而不是使用 `List<T>` 集合是为了优化性能。

`EnsureCreated` 方法自动为数据库上下文创建数据库。 如果数据库已存在，则返回 `EnsureCreated`，并且不修改数据库。

在 Program.cs 中，将 `Main` 方法修改为调用 `Initialize`  ：

[!code-csharp[](intro/samples/cu21/Program.cs?name=snippet2&highlight=14-15)]

删除任何学生记录并重启应用。 如果未初始化 DB，则在 `Initialize` 中设置断点以诊断问题。

## <a name="view-the-db"></a>查看数据库

数据库名称由之前提供的上下文名称以及短划线和 GUID 组成。 因此，数据库名称为“SchoolContext-{GUID}”。 GUID 因用户而异。
从 Visual Studio 中的“视图”菜单打开 SQL Server 对象资源管理器 (SSOX)   。
在 SSOX 中，依次单击“(localdb)\MSSQLLocalDB”>“数据库”>“SchoolContext-{GUID}”  。

展开“表”节点  。

右键单击 Student 表，然后单击“查看数据”，以查看创建的列和插入到表中的行   。

## <a name="asynchronous-code"></a>异步代码

异步编程是 ASP.NET Core 和 EF Core 的默认模式。

Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。 当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。 使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。 使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。 因此，使用异步代码可以更有效地利用服务器资源，并且可以让服务器在没有延迟的情况下处理更多流量。

异步代码会在运行时引入少量开销。 流量较低时，对性能的影响可以忽略不计，但流量较高时，潜在的性能改善非常显著。

在以下代码中，[async](/dotnet/csharp/language-reference/keywords/async) 关键字和 `Task<T>` 返回值，`await` 关键字和 `ToListAsync` 方法让代码异步执行。

[!code-csharp[](intro/samples/cu21/Pages/Students/Index.cshtml.cs?name=snippet_ScaffoldedIndex)]

* `async` 关键字让编译器执行以下操作：
  * 为方法主体的各部分生成回调。
  * 自动创建返回的 [Task](/dotnet/api/system.threading.tasks.task) 对象。 有关详细信息，请参阅[任务返回类型](/dotnet/csharp/programming-guide/concepts/async/async-return-types#BKMK_TaskReturnType)。

* 隐式返回类型 `Task` 表示正在进行的工作。
* `await` 关键字让编译器将该方法拆分为两个部分。 第一部分是以异步方式结束已启动的操作。 第二部分是当操作完成时注入调用回调方法的地方。
* `ToListAsync` 是 `ToList` 扩展方法的异步版本。

编写使用 EF Core 的异步代码时需要注意的一些事项：

* 只会异步执行导致查询或命令被发送到数据库的语句。 这包括 `ToListAsync`、`SingleOrDefaultAsync`、`FirstOrDefaultAsync` 和 `SaveChangesAsync`。 不包括只会更改 `IQueryable` 的语句，例如 `var students = context.Students.Where(s => s.LastName == "Davolio")`。
* EF Core 上下文并非线程安全：请勿尝试并行执行多个操作。
* 若要利用异步代码的性能优势，请验证在调用向数据库发送查询的 EF Core 方法时，库程序包（如用于分页）是否使用异步。

有关 .NET 中异步编程的详细信息，请参阅[异步概述](/dotnet/standard/async)和[使用 Async 和 Await 的异步编程](/dotnet/csharp/programming-guide/concepts/async/)。

下一个教程将介绍基本的 CRUD（创建、读取、更新、删除）操作。



## <a name="additional-resources"></a>其他资源

* [本教程的 YouTube 版本](https://www.youtube.com/watch?v=P7iTtQnkrNs)

> [!div class="step-by-step"]
> [下一页](xref:data/ef-rp/crud)
