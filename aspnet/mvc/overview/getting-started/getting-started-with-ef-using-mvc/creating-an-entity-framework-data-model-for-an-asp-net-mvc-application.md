---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
title: 教程：开始使用 Entity Framework 6 Code First 通过 MVC 5 |Microsoft Docs
description: 在本系列教程，您将学习如何构建用于数据访问使用 Entity Framework 6 的 ASP.NET MVC 5 应用程序。
author: tdykstra
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: 00bc8b51-32ed-4fd3-9745-be4c2a9c1eaf
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: b72a4ae1a89fd47d9c6ff63ccd45b26324508a63
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836176"
---
# <a name="tutorial-get-started-with-entity-framework-6-code-first-using-mvc-5"></a>教程：开始使用 Entity Framework 6 Code First 通过 MVC 5

> [!NOTE]
> 对于新开发，我们建议[ASP.NET Core Razor 页面](/aspnet/core/razor-pages)通过 ASP.NET MVC 控制器和视图。 有关类似于此系列教程使用 Razor 页面，请参阅[教程：开始使用 ASP.NET Core 中 Razor 页面](/aspnet/core/tutorials/razor-pages/razor-pages-start)。 新教程：
> * 易于关注。
> * 提供更多 EF Core 最佳做法。
> * 使用更高效的查询。
> * 通过最新 API 更新到更高版本。
> * 涵盖更多功能。
> * 是开发新应用程序的首选方法。

在本系列教程，您将学习如何构建用于数据访问使用 Entity Framework 6 的 ASP.NET MVC 5 应用程序。 本教程使用 Code First 工作流。 有关如何选择第一个代码、 数据库第一个和第一个模型之间的信息，请参阅[创建模型](/ef/ef6/modeling/)。

此教程系列介绍了如何构建 Contoso University 示例应用程序。 示例应用程序是一个简单的大学网站。 使用它，可以查看和更新学生、 课程和教师信息。 下面是两个您创建的屏幕：

![Students_Index_page](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image1.png)

![编辑学生](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image2.png)

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 MVC web 应用
> * 设置网站样式
> * 安装 Entity Framework 6
> * 创建数据模型
> * 创建数据库上下文
> * 使用测试数据初始化 DB
> * 设置为使用 LocalDB 的 EF 6
> * 创建控制器和视图
> * 查看数据库

## <a name="prerequisites"></a>系统必备

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=button+cta&utm_content=download+vs2017)

## <a name="create-an-mvc-web-app"></a>创建 MVC web 应用

1. 打开 Visual Studio 并创建C#web 项目使用**ASP.NET Web 应用程序 (.NET Framework)** 模板。 将项目命名*ContosoUniversity* ，然后选择**确定**。

   ![在 Visual Studio 中的新建项目对话框](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/new-project-dialog.png)

1. 在中**新 ASP.NET Web 应用程序-ContosoUniversity**，选择**MVC**。

   ![在 Visual Studio 中的新 web 应用程序对话框中](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/new-web-app-dialog.png)

    > [!NOTE]
    > 默认情况下**身份验证**选项设置为**无身份验证**。 对于本教程，web 应用不需要用户进行登录。 此外，它并不限制基于谁登录访问。

1. 选择“确定”创建项目。

## <a name="set-up-the-site-style"></a>设置网站样式

通过几个简单的更改设置站点菜单、 布局和主页。

1. 打开*views/shared\\_Layout.cshtml*，并进行以下更改：

   - 将每个匹配项的"My ASP.NET Application"和"Application name"更改为"Contoso University"。
   - 面向学生、 课程、 讲师和部门，添加菜单项和删除联系人条目。

   以下代码片段中突出显示所做的更改：

   [!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample1.cshtml?highlight=6,19,24-27,38)]

2. 在中*Views\Home\Index.cshtml*，该文件的内容替换为以下代码以将有关 ASP.NET 和 MVC 文本有关此应用程序的文本替换为：

   [!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample2.cshtml)]

3. 按 Ctrl + F5 以运行该 web 站点。 请参阅主页，其中主菜单。

## <a name="install-entity-framework-6"></a>安装 Entity Framework 6

1. 从**工具**菜单中，选择**NuGet 包管理器**，然后选择**程序包管理器控制台**。

2. 在中**程序包管理器控制台**窗口中，输入以下命令：

   ```text
   Install-Package EntityFramework
   ```

此步骤是几个步骤，本教程要求您手动执行此，但是，无法进行了自动通过 ASP.NET MVC 基架功能之一。 要执行这些手动，以便您可以看到使用 Entity Framework (EF) 所需的步骤。 您将更高版本使用基架创建 MVC 控制器和视图。 一种替代方法是让基架会自动安装 EF NuGet 包、 创建数据库上下文类，并创建连接字符串。 如果你已准备好执行此操作通过这种方式，只需是跳过这些步骤，在创建实体类后，在 MVC 控制器搭建基架。

## <a name="create-the-data-model"></a>创建数据模型

接下来你将创建 Contoso 大学应用程序的实体类。 首先将以下三个实体：

**课程** <-> **注册** <-> **学生**

| 实体 | 关系 |
| -------- | ------------ |
| 注册的课程 | 一个对多 |
| 注册的学生 | 一个对多 |

`Student` 和 `Enrollment`实体之间是一对多的关系，`Course` 和`Enrollment` 实体之间也是一个对多的关系。 换而言之，一名学生可以修读任意数量的课程, 并且某一课程可以被任意数量的学生修读。

在以下部分中，你将创建这些实体的每个类。

> [!NOTE]
> 如果您尝试编译项目，然后完成创建所有这些实体类，将收到编译器错误。

### <a name="the-student-entity"></a>Student 实体

- 在中*模型*文件夹中，创建名为的类文件*Student.cs*上的文件夹中右键单击**解决方案资源管理器**，然后选择**添加**  > **类**。 将模板代码替换为以下代码：

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample3.cs)]

`ID` 属性将成为对应于此类的数据库表中的主键。 名为的属性解释默认情况下，实体框架`ID`或*classname* `ID`为主键。

`Enrollments` 属性是*导航属性*。 导航属性中包含与此实体相关的其他实体。 在这种情况下，`Enrollments`的属性`Student`实体将保留所有`Enrollment`实体相关的`Student`实体。 换而言之，如果给定`Student`数据库中的行具有两个相关`Enrollment`行 (包含该学生的主键的行中的值及其`StudentID`外键列)，则该`Student`实体的`Enrollments`导航属性将包含这两个`Enrollment`实体。

导航属性通常定义为`virtual`，以便它们可以充分利用 Entity Framework 的特定功能，如*延迟加载*。 (将解释延迟加载在后面[读取相关数据](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)本系列后面的教程。)

如果导航属性可以具有多个实体 （如多对多或一对多关系），那么导航属性的类型必须是可以添加、 删除和更新条目的容器，如 `ICollection`。

### <a name="the-enrollment-entity"></a>Enrollment 实体

- 在 *Models* 文件夹中，创建 *Enrollment.cs* 并且用以下代码替换现有代码：

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample4.cs)]

`EnrollmentID`属性将被设为主键; 此实体使用*classname* `ID`模式而不是`ID`如示`Student`实体。 通常情况下，你选择一个主键模式，并在你的数据模型自始至终使用这种模式。 在这里，使用了两种不同的模式只是为了说明你可以使用任一模式来指定主键。 在后面的教程，您将看到如何使用`ID`而无需`classname`轻松地在数据模型中实现继承。

`Grade`属性是[枚举](/ef/ef6/modeling/code-first/data-types/enums)。 问号后`Grade`类型声明指示`Grade`属性是[可以为 null](/dotnet/csharp/programming-guide/nullable-types/using-nullable-types)。 评级为 null 是不同于评级为零，null 表示一个等级未知或者尚未分配。

`StudentID` 属性是外键，其对应的导航属性为 `Student`。 `Enrollment` 实体与一个 `Student` 实体相关联，因此该属性只包含单个 `Student` 实体 (与前面所看到的 `Student.Enrollments` 导航属性不同后，`Student`中可以容纳多个 `Enrollment` 实体)。

`CourseID` 属性是一个外键， `Course` 是与其对应的导航属性。 `Enrollment` 实体与一个 `Course` 实体相关联。

实体框架的属性解释为外键属性如果它名为 *&lt;导航属性名称&gt;&lt;主键属性名称&gt;* (例如， `StudentID`对于`Student`导航属性所以`Student`实体的主键是`ID`)。 外键属性还可以进行命名相同只需 *&lt;主键属性名称&gt;* (例如，`CourseID`由于`Course`实体的主键是`CourseID`)。

### <a name="the-course-entity"></a>Course 实体

- 在中*模型*文件夹中，创建*Course.cs*，模板代码替换为以下代码：

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample5.cs)]

`Enrollments` 属性是导航属性。 `Course` 实体可与任意数量的 `Enrollment` 实体相关。

我们说更多有关<xref:System.ComponentModel.DataAnnotations.Schema.DatabaseGeneratedAttribute>本系列后面的教程中的属性。 简单来说，此特性让你能自行指定主键，而不是让数据库自动指定主键。

## <a name="create-the-database-context"></a>创建数据库上下文

协调为给定的数据模型的实体框架功能的主类是*数据库上下文*类。 通过从派生来创建此类[System.Data.Entity.DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=vs.113).aspx)类。 在代码中，指定数据模型中包含哪些实体。 你还可以定义某些 Entity Framework 行为。 在此项目中将数据库上下文类命名为 `SchoolContext`。

- 若要为 ContosoUniversity 项目中创建一个文件夹，右键单击项目中的**解决方案资源管理器**并单击**添加**，然后单击**新文件夹**。 将新文件夹命名*DAL* （适用于数据访问层）。 在该文件夹中创建名为的新类文件*SchoolContext.cs*，并使用以下代码替换模板代码：

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample6.cs)]

### <a name="specify-entity-sets"></a>指定实体集

此代码将创建[DbSet](https://msdn.microsoft.com/library/system.data.entity.dbset(v=vs.113).aspx)每个实体集的属性。 在实体框架术语中，*实体集*通常对应于数据库表和一个*实体*对应于表中的行。

> [!NOTE]
>
> 可以省略`DbSet<Enrollment>`和`DbSet<Course>`语句和它的工作方式相同。 实体框架因为将隐式包括它们`Student`实体引用`Enrollment`实体以及`Enrollment`实体引用`Course`实体。

### <a name="specify-the-connection-string"></a>指定连接字符串

连接字符串 （它稍后将添加到 Web.config 文件） 的名称被传递给构造函数。

[!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample7.cs?highlight=1)]

此外可以传递连接字符串本身而不是一个存储在 Web.config 文件的名称中。 有关使用用于指定数据库选项的详细信息，请参阅[连接字符串和模型](/ef/ef6/fundamentals/configuring/connection-strings)。

如果未显式指定连接字符串或其中一个的名称，实体框架将假定该连接字符串名称是与类名称相同。 在此示例中的默认连接字符串名称都将`SchoolContext`，与您要指定的显式。

### <a name="specify-singular-table-names"></a>指定单数形式的表名称

`modelBuilder.Conventions.Remove`中的语句[OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating(v=vs.103).aspx)方法会阻止表名正在变为复数形式。 如果不这样做，将命名为数据库中生成的表`Students`， `Courses`，和`Enrollments`。 相反，将表名`Student`， `Course`，和`Enrollment`。 开发者对表名称是否应为复数意见不一。 本教程使用单数形式，但重要的一点是，你可以选择任意窗体，您的首选，通过包括或省略这行代码。

## <a name="initialize-db-with-test-data"></a>使用测试数据初始化 DB

实体框架可以自动创建 （或删除并重新创建） 为您的应用程序运行时的数据库。 您可以指定应此操作每次你的应用程序运行时或仅在与现有的数据库不同步模型时。 此外可以编写`Seed`方法的实体框架会自动调用后才能填充测试数据创建数据库。

默认行为是创建数据库，仅当它不存在 （和引发异常，如果模型已更改，并且数据库已存在）。 在本部分中，将指定应删除并重新创建该模型发生更改时数据库。 删除数据库导致所有数据的丢失。 这通常是好了在开发期间，因为`Seed`方法将运行时重新创建数据库，并将重新创建您的测试数据。 但在生产环境中通常不想丢失所有数据，每次需要更改数据库架构。 稍后你将了解如何通过使用 Code First 迁移来更改数据库架构而不是删除并重新创建数据库来处理模型更改。

1. 在 DAL 文件夹中，创建名为的新类文件*SchoolInitializer.cs*和模板代码替换为以下代码，这会导致要在需要时创建的数据库和加载到新的数据库测试数据。

   [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample8.cs)]

   `Seed`方法采用数据库上下文对象作为输入参数，并在方法中的代码使用该对象来将新实体添加到数据库。 对于每个实体类型，代码创建新实体的集合，将它们添加到相应`DbSet`属性，然后单击保存到数据库的更改。 但并不需要调用`SaveChanges`后的实体的每个组的方法，正如在这里，但这样做可帮助您找到问题的根源，如果向数据库写入代码时出现异常。

2. 若要指示 Entity Framework 使用初始值设定项类，将元素添加到`entityFramework`应用程序中的元素*Web.config*文件 （一个根项目文件夹中），如下面的示例中所示：

   [!code-xml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample9.xml?highlight=2-6)]

   `context type`指定完全限定的上下文类名称和它所在的程序集和`databaseinitializer type`指定初始值设定项类和它所在的程序集的完全限定的名称。 (如果不希望 EF 使用初始值设定项，您可以设置属性`context`元素： `disableDatabaseInitialization="true"`。)有关详细信息，请参阅[配置文件设置](/ef/ef6/fundamentals/configuring/config-file)。

   设置初始值设定项的一种替代方法*Web.config*文件是在代码中执行该操作通过添加`Database.SetInitializer`语句`Application_Start`中的方法*Global.asax.cs*文件。 有关详细信息，请参阅[了解数据库初始值设定项中 Entity Framework Code First](http://www.codeguru.com/csharp/article.php/c19999/Understanding-Database-Initializers-in-Entity-Framework-Code-First.htm)。

应用程序现在已设置，以便在首次在给定运行的应用程序中访问数据库时，实体框架进行比较对模型数据库 (您`SchoolContext`和实体类)。 如果差异，应用程序删除并重新创建数据库。

> [!NOTE]
> 在部署到生产 web 服务器的应用程序时，必须删除或禁用删除并重新创建数据库的代码。 在本系列中下一个教程，将执行该操作。

## <a name="set-up-ef-6-to-use-localdb"></a>设置为使用 LocalDB 的 EF 6

[LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb?view=sql-server-2017)是 SQL Server Express 数据库引擎的轻型版本。 它易于安装和配置、 按需、 启动并在用户模式下运行。 中的 SQL Server Express，使您可以使用数据库作为特殊执行模式运行 LocalDB *.mdf*文件。 可以将 LocalDB 数据库文件放入*应用程序\_数据*web 项目，如果你想要能够复制数据库与项目的文件夹。 在 SQL Server Express 用户实例功能还使您可以使用 *.mdf*文件，但用户实例功能已弃用; 因此，用于处理建议 LocalDB *.mdf*文件。 默认情况下使用 Visual Studio 安装 LocalDB。

通常情况下，SQL Server Express 不用于生产 web 应用程序。 LocalDB 具体而言不是建议用于生产 web 应用程序因为它不旨在与 IIS 配合使用。

- 在本教程中，您将使用 LocalDB。 打开应用程序*Web.config*文件，并添加`connectionStrings`元素前面`appSettings`元素，如下面的示例中所示。 (请确保更新*Web.config*根项目文件夹中的文件。 此外，还有*Web.config*中的文件*视图*无需更新的子文件夹。)

   [!code-xml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample10.xml?highlight=1-3)]

已添加的连接字符串指定实体框架将使用名为的 LocalDB 数据库*ContosoUniversity1.mdf*。 （该数据库尚不存在，但 EF 将创建它。）如果你想要在其中创建数据库你*应用程序\_数据*文件夹中，您可以添加`AttachDBFilename=|DataDirectory|\ContosoUniversity1.mdf`到连接字符串。 有关连接字符串的详细信息，请参阅[ASP.NET Web 应用程序的 SQL Server 连接字符串](/previous-versions/aspnet/jj653752(v=vs.110))。

实际上并不需要的连接字符串*Web.config*文件。 如果不提供连接字符串，实体框架使用基于上下文类的默认连接字符串。 有关详细信息，请参阅[到新数据库 Code First](/ef/ef6/modeling/code-first/workflows/new-database)。

## <a name="create-controller-and-views"></a>创建控制器和视图

现在将创建一个用于显示数据 web 页。 自动请求数据的过程将触发创建数据库。 您将首先创建一个新的控制器。 但这样做之前，请生成项目以使模型和上下文类可用于 MVC 控制器基架。

1. 右键单击**控制器**中的文件夹**解决方案资源管理器**，选择**添加**，然后单击**新基架项**。
2. 在中**添加基架**对话框中，选择**包含的 MVC 5 控制器视图，使用实体框架**，然后选择**添加**。

     ![在 Visual Studio 中添加基架对话框](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/add-scaffold.png)

3. 在中**添加控制器**对话框中，进行以下选择，然后选择**添加**:

   - 模型类：**学生 (ContosoUniversity.Models)**。 （如果看不到下拉列表中的此选项，生成项目，然后重试。）
   - 数据上下文类：**SchoolContext (ContosoUniversity.DAL)**。
   - 控制器名称：**StudentController** (不 StudentsController)。
   - 保留其他字段的默认值。

     当您单击**添加**，创建基架*StudentController.cs*文件和一组视图 (*.cshtml*文件) 与控制器的工作。 在将来创建使用实体框架的项目时，还可使用的基架一些附加功能： 创建第一个模型类，不要创建连接字符串，然后在**添加控制器**框中指定**新建数据上下文** 通过选择 **+** 旁边 **数据上下文类**。 基架将创建在`DbContext`类和你的连接字符串以及控制器和视图。
4. Visual Studio 将打开*Controllers\StudentController.cs*文件。 您看到类变量已创建了实例化数据库上下文对象：

     [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample11.cs)]

     `Index`操作方法获取的学生列表*学生*实体集通过阅读`Students`数据库上下文实例的属性：

     [!code-csharp[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample12.cs)]

     *Student\Index.cshtml*视图的表中显示此列表：

     [!code-cshtml[Main](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/samples/sample13.cshtml)]
5. 按 Ctrl + F5 以运行该项目。 （如果收到"无法创建卷影副本"错误，关闭浏览器和重试。）

     单击**学生**选项卡以查看测试数据的`Seed`插入的方法。 具体取决于如何窄浏览器窗口，您将看到顶部的地址栏中的学生选项卡链接或您必须单击右上角才能看到该链接。

     ![菜单按钮](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application/_static/image14.png)

## <a name="view-the-database"></a>查看数据库

当运行学生页面和应用程序尝试访问数据库时，EF 发现存在已没有数据库并创建了一个。 然后，EF 运行 seed 方法来使用数据填充数据库。

你可以使用**服务器资源管理器**或**SQL Server 对象资源管理器**(SSOX) 在 Visual Studio 中查看数据库。 对于本教程中，你将使用**服务器资源管理器**。

1. 关闭浏览器。
2. 在中**服务器资源管理器**，展开**数据连接**（可能需要首先选择刷新按钮），展开**学校上下文 (ContosoUniversity)**，然后展开**表**以查看新数据库中的表。

3. 右键单击**学生**表，然后单击**显示表数据**若要查看已创建的列和插入到表的行。

4. 关闭**服务器资源管理器**连接。

*ContosoUniversity1.mdf*并 *.ldf*数据库文件位于 *%USERPROFILE%* 文件夹。

正在使用，因此`DropCreateDatabaseIfModelChanges`初始值设定项，可以现在更改到`Student`类、 运行一次，应用程序和数据库会自动将重新创建，以匹配所做的更改。 例如，如果您将添加`EmailAddress`属性设置为`Student`类，学生页上再次运行，并可以看到一个新的然后查看表再次，`EmailAddress`列。

## <a name="conventions"></a>约定

您必须在实体框架能够为您创建完整的数据库中编写的代码量是由于最小*约定*，或使实体框架的假设。 其中一些已记下或使用而无需你不知道的：

- 复数的形式的实体类名称用作表名。
- 使用实体属性名作为列名。
- 命名的实体属性`ID`或*classname* `ID`被视为主键属性。
- 如果它名为一个属性将被解释为外键属性 *&lt;导航属性名称&gt;&lt;主键属性名称&gt;* (例如， `StudentID` 为`Student`导航属性所以`Student`实体的主键是`ID`)。 外键属性还可以进行命名相同只需&lt;主键属性名称&gt;(例如，`EnrollmentID`由于`Enrollment`实体的主键是`EnrollmentID`)。

您已了解约定可以重写。 例如，指定表名不应变为复数形式，并稍后您将看到如何显式标记为外键属性的属性。

## <a name="get-the-code"></a>获取代码

[下载已完成的项目](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>其他资源

有关 EF 6 的详细信息，请参阅以下文章：

* [ASP.NET 数据访问 - 推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)

* [代码优先约定](/ef/ef6/modeling/code-first/conventions/built-in)

* [创建更复杂的数据模型](creating-a-more-complex-data-model-for-an-asp-net-mvc-application.md)

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建 MVC web 应用
> * 设置网站样式
> * 已安装的实体框架 6
> * 创建数据模型
> * 创建数据库上下文
> * 使用测试数据初始化的 DB
> * 设置为使用 LocalDB 的 EF 6
> * 创建的控制器和视图
> * 查看数据库

转到下一步的文章，了解如何查看和自定义创建、 读取、 更新、 删除 (CRUD) 在控制器和视图中的代码。
> [!div class="nextstepaction"]
> [实现基本的 CRUD 功能](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)