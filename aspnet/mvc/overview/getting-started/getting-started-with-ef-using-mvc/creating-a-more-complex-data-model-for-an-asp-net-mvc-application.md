---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-a-more-complex-data-model-for-an-asp-net-mvc-application
title: 教程：创建 ASP.NET MVC 应用的更复杂数据模型
author: tdykstra
description: 在本教程将添加更多实体和关系并将通过指定格式设置、 验证和数据库映射规则来自定义数据模型。
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: 46f7f3c9-274f-4649-811d-92222a9b27e2
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-a-more-complex-data-model-for-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 5d05d456315c019c545a91b527bb05510126c06b
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836618"
---
# <a name="tutorial-create-a-more-complex-data-model-for-an-aspnet-mvc-app"></a>教程：创建 ASP.NET MVC 应用的更复杂数据模型

在前面的教程中，您过了由三个实体组成的简单数据模型。 在本教程中添加更多实体和关系，并通过指定格式设置、 验证和数据库映射规则来自定义数据模型。 本文演示两种方式自定义数据模型： 通过将属性添加到实体类并通过将代码添加到数据库上下文类。

完成本教程后，实体类将构成下图所示的完整数据模型：

![School_class_diagram](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image1.png)

在本教程中，你将了解：

> [!div class="checklist"]
> * 自定义数据模型
> * 更新 Student 实体
> * 创建 Instructor 实体
> * 创建 OfficeAssignment 实体
> * 修改 Course 实体
> * 创建 Department 实体
> * 修改 Enrollment 实体
> * 将代码添加到数据库上下文
> * 使用测试数据的种子数据库
> * 添加迁移
> * 更新数据库

## <a name="prerequisites"></a>系统必备

* [第一个迁移和部署代码](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="customize-the-data-model"></a>自定义数据模型

本节介绍如何使用指定格式化、验证和数据库映射规则的特性来自定义数据模型。 然后在多个以下各节，你将创建一种完整`School`通过添加数据模型属性对类已在模型中剩余的实体类型的创建和创建新类。

### <a name="the-datatype-attribute"></a>DataType 特性

对于学生注册日期，目前所有网页都显示有时间和日期，尽管对此字段而言重要的只是日期。 使用数据注释特性，可更改一次代码，修复每个视图中数据的显示格式。 若要查看如何执行此操作，请向 `Student` 类的 `EnrollmentDate` 属性添加一个特性。

在*Models\Student.cs*，添加`using`语句`System.ComponentModel.DataAnnotations`命名空间，并添加`DataType`并`DisplayFormat`属性到`EnrollmentDate`属性，如下面的示例中所示：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample1.cs?highlight=3,12-13)]

[数据类型](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx)特性用于指定比数据库内部类型更具体的数据类型。 在此示例中，我们只想跟踪日期，而不是日期和时间。 [DataType 枚举](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx)提供了多种数据类型，如*日期、 时间、 电话号码、 货币、 电子邮件地址*和的详细信息。 应用程序还可通过 `DataType` 特性自动提供类型特定的功能。 例如，`mailto:`可以为创建链接[DataType.EmailAddress](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx)，和日期选择器可提供用于[DataType.Date](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatype.aspx)中支持的浏览器[HTML5](http://html5.org/). [数据类型](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx)特性发出 HTML 5 [data-](http://ejohn.org/blog/html-5-data-attributes/) (读作*数据 dash*) 特性供 HTML 5 浏览器理解。 [数据类型](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx)特性不提供任何验证。

`DataType.Date` 不指定显示日期的格式。 默认情况下，显示该数据字段根据基于服务器的默认格式[CultureInfo](https://msdn.microsoft.com/library/vstudio/system.globalization.cultureinfo(v=vs.110).aspx)。

`DisplayFormat` 特性用于显式指定日期格式：


[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample2.cs)]


`ApplyFormatInEditMode`设置指定，指定的格式设置也应该应用时的值显示在文本框中以进行编辑。 (您可能不想为某些字段 — 例如，对于货币值，您可能不希望在文本框中的货币符号以进行编辑。)

可以使用[DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx)特性本身，但它通常是使用一个好办法[数据类型](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx)特性。 `DataType`特性传达*语义*的数据作为相对于屏幕上的呈现方式，提供了以下具有前所未有的优势`DisplayFormat`:

- 浏览器可启用 HTML5 功能（例如，显示日历控件、区域设置适用的货币符号、电子邮件链接、某种客户端输入验证等）。
- 默认情况下，在浏览器将呈现数据采用正确的格式基于你[区域设置](https://msdn.microsoft.com/library/vstudio/wyzd2bce.aspx)。
- [数据类型](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.datatypeattribute.aspx)特性使 MVC 能够选择正确的字段模板来呈现数据 ( [DisplayFormat](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.displayformatattribute.aspx)使用字符串模板)。 有关详细信息，请参阅 Brad Wilson [ASP.NET MVC 2 模板](http://bradwilson.typepad.com/blog/2009/10/aspnet-mvc-2-templates-part-1-introduction.html)。 （为 MVC 2 编写的但本文仍适用于 ASP.NET MVC 的当前版本。）

如果您使用`DataType`属性与日期字段，则必须指定`DisplayFormat`还以确保字段正确呈现在 Chrome 浏览器中的属性。 有关详细信息，请参阅[此 StackOverflow 线程](http://stackoverflow.com/questions/12633471/mvc4-datatype-date-editorfor-wont-display-date-value-in-chrome-fine-in-ie)。

有关如何处理在 MVC 中的其他日期格式的详细信息，请转到[MVC 5 简介：检查编辑方法和编辑视图](../introduction/examining-the-edit-methods-and-edit-view.md)中的页和搜索&quot;国际化&quot;。

再次运行学生索引页，请注意注册日期不再显示时间。 相同，则为 true 的任何视图，它使用`Student`模型。

![Students_index_page_with_formatted_date](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image2.png)

### <a name="the-stringlengthattribute"></a>StringLengthAttribute

还可使用特性指定数据验证规则和验证错误消息。 [StringLength 特性](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx)设置数据库中的最大长度，并提供客户端和服务器端的 ASP.NET MVC 验证。 还可在此属性中指定最小字符串长度，但最小值对数据库架构没有影响。

假设要确保用户输入的名称不超过 50 个字符。 若要添加此限制，添加[StringLength](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx)向属性`LastName`和`FirstMidName`属性，如下面的示例中所示：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample3.cs?highlight=10,12)]

[StringLength](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx)属性不会阻止用户输入一个名称的空格。 可以使用[正则表达式](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.regularexpressionattribute.aspx)属性来限制应用于输入。 例如，下面的代码要求第一个字符为大写，其余字符按字母顺序排列：

`[RegularExpression(@"^[A-Z]+[a-zA-Z""'\s-]*$")]`

[MaxLength](https://msdn.microsoft.com/library/System.ComponentModel.DataAnnotations.MaxLengthAttribute.aspx)属性提供了与类似的功能[StringLength](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.stringlengthattribute.aspx)属性但不提供客户端验证。

运行应用程序，然后单击**学生**选项卡。你收到以下错误：

*创建数据库后，支持 SchoolContext 上下文的模型已更改。请考虑使用 Code First 迁移来更新数据库 ([https://go.microsoft.com/fwlink/?LinkId=238269](https://go.microsoft.com/fwlink/?LinkId=238269))。*

数据库模型已更改需要在数据库架构中，更改的方式，实体框架检测到的。 将使用迁移来更新架构，而不会丢失任何数据，使用 UI 添加到数据库。 如果更改由创建的数据`Seed`方法，将更改回其原始状态由于[AddOrUpdate](https://msdn.microsoft.com/library/hh846520(v=vs.103).aspx)方法中使用`Seed`方法。 ([AddOrUpdate](https://msdn.microsoft.com/library/hh846520(v=vs.103).aspx)等效于"upsert"操作从数据库术语中。)

在包管理器控制台 (PMC) 中输入以下命令：

[!code-console[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample4.cmd)]

`add-migration`命令创建名为的文件*&lt;时间戳&gt;\_MaxLengthOnNames.cs*。 此文件包含 `Up` 方法中的代码，该代码将更新数据库以匹配当前数据模型。 `update-database` 命令运行该代码。

Entity Framework 使用迁移文件名前面预置的时间戳进行排序的迁移。 您可以创建多个迁移，然后再运行`update-database`命令和所有迁移都应用中已创建的顺序。

运行**创建**页上，然后输入名称超过 50 个字符。 当您单击**创建**，客户端端验证会显示一条错误消息：*姓氏字段必须是具有最大长度为 50 的字符串。*

### <a name="the-column-attribute"></a>列属性

还可使用特性来控制类和属性映射到数据库的方式。 假设在名字字段使用了 `FirstMidName`，这是因为该字段也可能包含中间名。 但却希望将数据库列命名为 `FirstName`，因为要针对数据库编写即席查询的用户习惯使用该姓名。 若要进行此映射，可使用 `Column` 特性。

`Column` 特性指定，创建数据库时，映射到 `FirstMidName` 属性的 `Student` 表的列将被命名为 `FirstName`。 换言之，在代码引用 `Student.FirstMidName` 时，数据来源将是 `Student` 表的 `FirstName` 列或在其中进行更新。 如果未指定列名称，系统会提供名称与属性名称相同。

在中*Student.cs*文件中，添加`using`语句[System.ComponentModel.DataAnnotations.Schema](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.aspx) ，并添加到列名称特性`FirstMidName`属性，如中所示以下突出显示的代码：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample5.cs?highlight=4,14)]

添加了[列属性](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute.aspx)更改备份 SchoolContext，因此它不会与数据库匹配的模型。 在 PMC 创建另一个迁移中输入以下命令：

[!code-console[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample6.cmd)]

在中**服务器资源管理器**，打开*学生*表设计器通过双击*学生*表。

下图显示的原始列名称之前应用的前两个迁移。 除了从更改的列名称`FirstMidName`到`FirstName`，从已更改的两个名称列`MAX`长度为 50 个字符。

![](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image5.png)

您还可以进行数据库映射更改使用[Fluent API](https://msdn.microsoft.com/data/jj591617)，正如您将看到在本教程的后面。

> [!NOTE]
> 如果尚未按以下各节所述创建所有实体类就尝试进行编译，则可能会出现编译器错误。

## <a name="update-student-entity"></a>更新 Student 实体

在中*Models\Student.cs*，添加的代码之前替换以下代码。 突出显示所作更改。

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample7.cs?highlight=11,13,15,18,22,25-32)]

### <a name="the-required-attribute"></a>所需的属性

[所需的属性](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute.aspx)使名称属性必填的字段。 `Required attribute`不需要值类型，如 DateTime、 int、 double、 和 float。 值类型无法将分配一个 null 值，因此它们本质上是被视为必填字段。 无法删除[所需的属性](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.requiredattribute.aspx)并将其替换的最小长度参数为`StringLength`属性：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample8.cs?highlight=2)]

### <a name="the-display-attribute"></a>Display 特性

`Display` 特性指定文本框的标题应是“名”、“姓”、“全名”和“注册日期”，而不是每个实例中的属性名称（其中没有分隔单词的空格）。

### <a name="the-fullname-calculated-property"></a>FullName 计算属性

`FullName` 是计算属性，可返回通过串联两个其他属性创建的值。 因此只有`get`访问器，但没有`FullName`将在数据库中生成列。

## <a name="create-instructor-entity"></a>创建 Instructor 实体

创建*Models\Instructor.cs*，模板代码替换为以下代码：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample9.cs)]

请注意，`Student` 和 `Instructor` 实体中具有几个相同属性。 本系列后面的[实现继承](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)教程将重构此代码以消除冗余。

因此您也可以编写 instructor 类，如下所示，可以在同一行，将多个属性：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample10.cs)]

### <a name="the-courses-and-officeassignment-navigation-properties"></a>课程和 OfficeAssignment 导航属性

`Courses` 和 `OfficeAssignment` 是导航属性。 如前所述，它们通常定义为[虚拟](https://msdn.microsoft.com/library/9fkccyh4(v=vs.110).aspx)，以便他们可以利用名为实体框架功能[延迟加载](https://msdn.microsoft.com/magazine/hh205756.aspx)。 此外，如果导航属性可以包含多个实体，其类型必须实现[ICollection&lt;T&gt; ](https://msdn.microsoft.com/library/92t2ye13.aspx)接口。 例如[IList&lt;T&gt; ](https://msdn.microsoft.com/library/5y536ey6.aspx)但不是限定[IEnumerable&lt;T&gt; ](https://msdn.microsoft.com/library/9eekhta0.aspx)因为`IEnumerable<T>`不会实现[添加](https://msdn.microsoft.com/library/63ywd54z.aspx).

一名讲师可以教授任意数量的课程，因此`Courses`定义为一系列`Course`实体。

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample11.cs)]

我们的业务规则规定一名讲师只能具有最多一个办公室，因此`OfficeAssignment`定义为单个`OfficeAssignment`实体 (这可能会`null`如果没有 office 分配)。

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample12.cs)]

## <a name="create-officeassignment-entity"></a>创建 OfficeAssignment 实体

创建*Models\OfficeAssignment.cs*使用以下代码：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample13.cs)]

生成项目时，该操作将保存所做的更改并确认没有进行任何复制并粘贴编译器可以捕获的错误。

### <a name="the-key-attribute"></a>键属性

对零或一一之间没有关系`Instructor`和`OfficeAssignment`实体。 分配到办公室的讲师相关才存在办公室分配，因此其主键也是其外的键`Instructor`实体。 但 Entity Framework 无法自动识别`InstructorID`作为主要因为其名称不符合此实体的键`ID`或*classname* `ID`命名约定。 因此，`Key` 特性用于将其识别为主键：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample14.cs)]

此外可以使用`Key`属性如果实体具有其自己的主键，但你想要属性名称不同于`classnameID`或`ID`。 默认情况下 EF 将键视为非数据库生成因为该列面向的是识别关系。

### <a name="the-foreignkey-attribute"></a>ForeignKey 属性

当两个实体之间的一对一关系或一对零或一一种关系 (如`OfficeAssignment`和`Instructor`)，EF 不能计算出的关系的哪一端是主体，依赖哪一端。 一对一关系中与其他类的每个类具有引用导航属性。 [ForeignKey 属性](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute.aspx)可以应用于相关的类来建立此关系。 如果省略[ForeignKey 属性](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.foreignkeyattribute.aspx)，当你尝试创建迁移时出现以下错误：

*无法确定类型 ContosoUniversity.Models.OfficeAssignment 和 ContosoUniversity.Models.Instructor 之间的关联的主体端。此关联的主体端必须使用的关系 fluent API 或数据批注进行显式配置。*

在本教程后面你将了解如何使用 fluent API 配置此关系。

### <a name="the-instructor-navigation-property"></a>Instructor 导航属性

`Instructor`实体具有一个可以为 null`OfficeAssignment`导航属性 （因为一名讲师可能没有办公室分配），并`OfficeAssignment`实体具有不可为 null`Instructor`导航属性 （因为办公室分配不能没有讲师-`InstructorID`不可为 null)。 当`Instructor`实体具有相关`OfficeAssignment`实体，每个实体都有对另一个在其导航属性的引用。

您可以将`[Required]`Instructor 导航属性来指定必须有相关的讲师，但无需这样做是因为 InstructorID 外键 （这也是此表的关键） 是不可以为 null 的属性。

## <a name="modify-the-course-entity"></a>修改 Course 实体

在中*Models\Course.cs*，添加的代码之前替换以下代码：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample15.cs)]

Course 实体具有外键属性`DepartmentID`它指向相关`Department`实体，同时它具有`Department`导航属性。 如果拥有相关实体的导航属性，则 Entity Framework 不会要求为数据模型添加外键属性。 EF 会自动在数据库中创建外键在需要的位置。 但如果数据模型包含外键，则更新会变得更简单、更高效。 例如，当提取 course 实体进行编辑，请`Department`实体为 null 如果未加载它，因此当更新 course 实体，必须先提取`Department`实体。 当外键属性`DepartmentID`包含在数据模型中，您无需提取`Department`实体，然后才能更新。

### <a name="the-databasegenerated-attribute"></a>DatabaseGenerated 特性

[DatabaseGenerated 特性](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedattribute.aspx)与[None](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.databasegeneratedoption(v=vs.110).aspx)参数`CourseID`属性指定主键值是由用户提供而不是由数据库生成。

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample16.cs)]

默认情况下，Entity Framework 假定主键值由数据库生成。 大多数情况下，这是理想情况。 但是，对于`Course`实体，您将使用用户指定的课程编号，例如 1000年系列的一个部门，另一个系为 2000年系列等。

### <a name="foreign-key-and-navigation-properties"></a>外键和导航属性

外键属性和导航属性中的`Course`实体可反映以下关系：

- 向一个系分配课程后，出于上述原因，会出现 `DepartmentID` 外键和 `Department` 导航属性。

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample17.cs)]
- 参与一门课程的学生数量不定，因此 `Enrollments` 导航属性是一个集合：

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample18.cs)]
- 一门课程可能由多位讲师讲授，因此 `Instructors` 导航属性是一个集合：

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample19.cs)]

## <a name="create-the-department-entity"></a>创建 Department 实体

创建*Models\Department.cs*使用以下代码：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample20.cs)]

### <a name="the-column-attribute"></a>列属性

之前用于[列属性](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.schema.columnattribute.aspx)若要更改列名称映射。 中的代码`Department`实体，`Column`特性用于更改 SQL 数据类型映射，以便将使用 SQL Server 定义该列[资金](https://msdn.microsoft.com/library/ms179882.aspx)数据库中的类型：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample21.cs)]

列映射通常不是必需的因为实体框架通常选择适当 SQL Server 数据类型为属性定义的 CLR 类型。 CLR `decimal` 类型会映射到 SQL Server `decimal` 类型。 但在这种情况下您知道列将包含货币金额，并[资金](https://msdn.microsoft.com/library/ms179882.aspx)数据类型是更适合。 有关 CLR 数据类型和如何与 SQL Server 数据类型的匹配的详细信息，请参阅[SqlClient 实体 FrameworkTypes](https://msdn.microsoft.com/library/bb896344.aspx)。

### <a name="foreign-key-and-navigation-properties"></a>外键和导航属性

外键和导航属性可反映以下关系：

- 一个系可能有也可能没有管理员，而管理员始终是讲师。 因此`InstructorID`属性是作为外键到包含`Instructor`实体，并将问号后添加`int`类型指派将标记为可为 null 的属性。导航属性名为`Administrator`但其中包含`Instructor`实体：

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample22.cs)]
- 一个系可以有多门课程，因此没有`Courses`导航属性：

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample23.cs)]

  > [!NOTE]
  > 按照约定，Entity Framework 能针对不可为 null 的外键和多对多关系启用级联删除。 这可能导致循环级联删除规则，尝试添加迁移时该规则会造成异常。 例如，如果你未定义`Department.InstructorID`为可以为 null 的属性，你会收到以下异常消息："引用关系会导致不允许的循环引用。" 如果您的业务规则需要`InstructorID`属性设置为不可为 null，必须使用以下 fluent API 语句禁用关系的级联删除：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample24.cs)]

## <a name="modify-the-enrollment-entity"></a>修改 Enrollment 实体

 在中*Models\Enrollment.cs*，添加的代码之前替换以下代码

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample25.cs?highlight=1,15)]

### <a name="foreign-key-and-navigation-properties"></a>外键和导航属性

外键属性和导航属性可反映以下关系：

- 注册记录面向一门课程，因此存在 `CourseID` 外键属性和 `Course` 导航属性：

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample26.cs)]
- 注册记录面向一名学生，因此存在 `StudentID` 外键属性和 `Student` 导航属性：

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample27.cs)]

### <a name="many-to-many-relationships"></a>多对多关系

没有之间的多对多关系`Student`和`Course`实体，并`Enrollment`实体用作多对多联接表*有效负载为*数据库中。 这意味着`Enrollment`表包含除联接表的外键的其他数据 (在这种情况下，主键和`Grade`属性)。

下图显示这些关系在实体关系图中的外观。 (使用生成此关系图[Entity Framework Power Tools](https://visualstudiogallery.msdn.microsoft.com/72a60b14-1581-4b9b-89f2-846072eff19d); 创建此关系图不是本教程的一部分，只需使用此处为举例说明。)

![Student-Course_many-to-many_relationship](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image12.png)

每条关系线都为 1，另一端星号 (\*) 在另一个，指示一个对多关系。

如果`Enrollment`表不包含年级信息，它只需包含两个外键`CourseID`和`StudentID`。 在这种情况下，它将对应的多对多联接表*不带有效负载*(或*纯联接表*) 在数据库中，并且不会有根本为它创建的模型类。 `Instructor`和`Course`实体都有这种多对多关系，并且正如您所看到的它们之间没有实体类：

![Instructor-Course_many-to-many_relationship](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image13.png)

但是在数据库中，需要联接表，如以下数据库关系图中所示：

![Instructor-Course_many-to-many_relationship_tables](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image14.png)

实体框架会自动创建`CourseInstructor`表，并且您读取和更新到通过读取和更新的间接`Instructor.Courses`和`Course.Instructors`导航属性。

## <a name="entity-relationship-diagram"></a>实体关系图

下图显示 Entity Framework Power Tools 针对已完成的学校模型创建的关系图。

![School_data_model_diagram](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image1.png)

除了多对多关系线 (\*到\*) 和一个对多关系线 (1 到\*)，您可以看到对零或一一的关系线 (1 到 0..1) 之间`Instructor`和`OfficeAssignment`实体和 0-或--一对多关系线 (0..1 到\*) 之间的 Instructor 和 Department 实体。

## <a name="add-code-to-database-context"></a>将代码添加到数据库上下文

接下来将添加到新的实体`SchoolContext`类，并自定义映射使用的某些[fluent API](https://msdn.microsoft.com/data/jj591617)调用。 API 是"fluent"，因为它通常用于按顺序排列的一系列方法调用连接成单个语句，如以下示例所示：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample28.cs)]

在本教程将使用 fluent API，仅对数据库不能使用特性实现的映射。 但 Fluent API 还可用于指定大多数格式化、验证和映射规则，这可通过特性完成。 `MinimumLength` 等特性不能通过 Fluent API 应用。 如前所述，`MinimumLength`不会更改架构，它仅应用客户端和服务器端验证规则

某些开发者倾向于仅使用 Fluent API 以保持实体类的“纯净”。 如有需要，可混合使用特性和 Fluent API，且有些自定义只能通过 Fluent API 实现，但通常建议选择一种方法并尽可能坚持使用这一种。

若要添加的新实体的数据建模和执行数据库没有做通过使用特性的映射中的代码替换*DAL\SchoolContext.cs*使用以下代码：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample29.cs)]

中的新语句[OnModelCreating](https://msdn.microsoft.com/library/system.data.entity.dbcontext.onmodelcreating(v=vs.103).aspx)方法配置多对多联接表：

- 之间的多对多关系`Instructor`和`Course`实体，该代码指定联接表的表和列名称。 代码首先可以配置多对多关系，而无需此代码，但如果不调用它，则会默认名称如`InstructorInstructorID`为`InstructorID`列。

    [!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample30.cs)]

以下代码举例说明如何您可以使用 fluent API 而不是属性来指定之间的关系`Instructor`和`OfficeAssignment`实体：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample31.cs)]

有关"fluent API"语句会在后台执行的操作的信息，请参阅[Fluent API](https://blogs.msdn.com/b/aspnetue/archive/2011/05/04/entity-framework-code-first-tutorial-supplement-what-is-going-on-in-a-fluent-api-call.aspx)博客文章。

## <a name="seed-database-with-test-data"></a>使用测试数据的种子数据库

中的代码替换*migrations\ configuration.cs*文件与以下代码，以便为已创建的新实体提供种子数据。

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample32.cs)]

如您所看到的第一个教程中，此代码的大部分只需更新或创建新实体对象并将示例数据加载到所需的测试的属性。 但请注意如何`Course`具有多对多关系的实体与`Instructor`实体，进行处理：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample33.cs)]

当您创建`Course`对象，初始化`Instructors`导航属性为空集合使用的代码`Instructors = new List<Instructor>()`。 这样就可以添加`Instructor`与此相关的实体`Course`通过使用`Instructors.Add`方法。 如果未创建一个空列表，您将无法添加这些关系，因为`Instructors`属性将为 null，并且不会有`Add`方法。 您也可以添加到构造函数的列表初始化。

## <a name="add-a-migration"></a>添加迁移

在 PMC 中，输入`add-migration`命令 (不执行操作`update-database`命令):

`add-Migration ComplexDataModel`

如果此时尝试运行 `update-database` 命令（先不要执行此操作），则会出现以下错误：

*ALTER TABLE 语句与 FOREIGN KEY 约束冲突"FK\_dbo。课程\_dbo。部门\_DepartmentID"。冲突发生于数据库"ContosoUniversity"表"dbo。部门"，列 DepartmentID。*

有时在执行迁移的现有数据时，需将存根 （stub） 数据插入到数据库，满足外键约束，这也必须立即执行操作。 生成的代码中 ComplexDataModel`Up`方法将添加不可为 null`DepartmentID`外键的`Course`表。 因为已经有了中的行`Course`表的代码运行时，`AddColumn`操作将失败，因为 SQL Server 不知道要放入不能为 null 的列的值。 因此需要更改代码来为新列默认值，并创建名为"Temp"作为默认系的存根 （stub） 部门。 因此，现有`Course`行将所有与"Temp"系建立联系后`Up`方法运行。 可以与中的正确部门`Seed`方法。

编辑&lt;*时间戳&gt;\_ComplexDataModel.cs*文件，注释掉的代码将 DepartmentID 列添加到 Course 表行，并添加以下突出显示的代码 （在注释行也会突出显示）：

[!code-csharp[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample34.cs?highlight=14-18)]

当`Seed`方法运行时，它将插入中的行`Department`表和它将与现有`Course`到这些新行`Department`行。 如果尚未在 UI 中添加任何课程，然后不再需要"Temp"系或默认值上`Course.DepartmentID`列。 若要允许，有人可能已添加课程使用应用程序的可能性，您还想要更新`Seed`方法代码，以确保所有`Course`行 (而不仅仅是由早期运行的插入`Seed`方法) 具有有效`DepartmentID`值之前删除默认列中的值并删除"Temp"系。

## <a name="update-the-database"></a>更新数据库

完成编辑后&lt;*时间戳&gt;\_ComplexDataModel.cs*文件中，输入`update-database`PMC 执行迁移命令。

[!code-powershell[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample35.ps1)]

> [!NOTE]
> 就可以将迁移数据，也使架构更改时遇到其他错误。 如果遇到无法解决的迁移错误，你可以更改连接字符串中的数据库名称，或删除数据库。 最简单方法是在数据库重命名*Web.config*文件。 下面的示例演示名称更改为 CU\_测试：
>
> [!code-xml[Main](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/samples/sample36.xml?highlight=1)]
>
> 使用新数据库没有数据迁移，和`update-database`命令是更有望完成且未出错。 有关如何删除数据库的说明，请参阅[如何从 Visual Studio 2012 中删除数据库](http://romiller.com/2013/05/17/how-to-drop-a-database-from-visual-studio-2012/)。
>
> 如果失败，你可以尝试另一件事是通过在 PMC 中输入以下命令会重新初始化数据库：
>
> `update-database -TargetMigration:0`

打开中的数据库**服务器资源管理器**像前面，并展开**表**节点以查看是否已创建的所有表。 (如果仍有**服务器资源管理器**从较早的时间打开，请单击**刷新**按钮。)

![](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image16.png)

未创建的模型类`CourseInstructor`表。 如前面所述，这是一个联接表之间的多对多关系`Instructor`和`Course`实体。

右键单击`CourseInstructor`表，然后选择**显示表数据**以确认它在其中为具有数据`Instructor`添加到实体`Course.Instructors`导航属性。

![Table_data_in_CourseInstructor_table](creating-a-more-complex-data-model-for-an-asp-net-mvc-application/_static/image17.png)

## <a name="get-the-code"></a>获取代码

[下载已完成的项目](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>其他资源

其他实体框架资源的链接可在[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 自定义数据模型
> * 已更新的 Student 实体
> * 创建的 Instructor 实体
> * 创建的 OfficeAssignment 实体
> * 修改 Course 实体
> * 创建 Department 实体
> * 修改 Enrollment 实体
> * 为数据库上下文添加的代码
> * 使用测试数据植入的数据库
> * 添加迁移
> * 更新数据库

转到下一步的文章，了解如何读取和显示 Entity Framework 加载到导航属性的相关的数据。

> [!div class="nextstepaction"]
> [读取相关数据](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)
