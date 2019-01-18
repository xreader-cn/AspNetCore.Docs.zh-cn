---
uid: web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
title: 检索和显示数据与模型绑定和 web 窗体 |Microsoft Docs
author: Rick-Anderson
description: 本系列教程演示了一个 ASP.NET Web 窗体项目中使用模型绑定的基本方面。 模型绑定使数据交互...更多直接-
ms.author: riande
ms.date: 02/27/2014
ms.assetid: 9f24fb82-c7ac-48da-b8e2-51b3da17e365
msc.legacyurl: /web-forms/overview/presenting-and-managing-data/model-binding/retrieving-data
msc.type: authoredcontent
ms.openlocfilehash: c53c27f4852eab9813bd917315111e7cd3b04953
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396280"
---
<a name="retrieving-and-displaying-data-with-model-binding-and-web-forms"></a>检索和使用模型绑定和 web 窗体显示数据
====================

> 本系列教程演示了一个 ASP.NET Web 窗体项目中使用模型绑定的基本方面。 模型绑定可以更直接的比处理数据源对象 （如 ObjectDataSource 或 SqlDataSource） 的数据交互。 本系列开始介绍性材料，后续教程将移动到更高级的概念。
> 
>  模型绑定模式适用于任何数据访问技术。 在本教程中，将使用实体框架中，但您可以使用最熟悉的数据访问技术。 从数据绑定服务器控件，如 GridView、 ListView、 detailsview 和 FormView 控件，指定要用于选择、 更新、 删除和创建数据的方法的名称。 在本教程中，将 SelectMethod 为指定值。 
> 
> 在该方法中，您提供用于检索数据的逻辑。 在下一步的教程中，将为 UpdateMethod、 DeleteMethod 和 InsertMethod 设置值。
>
> 你可以[下载](https://go.microsoft.com/fwlink/?LinkId=286116)中的完整项目C#或 Visual Basic。 可下载代码适用于 Visual Studio 2012 和更高版本。 它使用 Visual Studio 2012 模板，为在本教程中所示的 Visual Studio 2017 模板稍有不同。
> 
> 在本教程在 Visual Studio 中运行应用程序。 此外可以部署到宿主提供程序应用程序，并使其可通过 internet。 Microsoft 提供了免费的 web 宿主中的最多 10 个网站  
>  [Azure 免费试用帐户](https://azure.microsoft.com/free/?WT.mc_id=A443DD604)。 有关如何将 Visual Studio web 项目部署到 Azure 应用服务 Web 应用的信息，请参阅[使用 Visual Studio 的 ASP.NET Web 部署](../../deployment/visual-studio-web-deployment/introduction.md)系列。 该教程还演示如何使用 Entity Framework Code First 迁移将 SQL Server 数据库部署到 Azure SQL 数据库。
> 
> ## <a name="software-versions-used-in-the-tutorial"></a>在本教程中使用的软件版本
> 
> - Microsoft Visual Studio 2017 或 Microsoft Visual Studio Community 2017
>   
> 本教程还适用于 Visual Studio 2012 和 Visual Studio 2013 中，但有一些用户界面和项目模板中的差异。


## <a name="what-youll-build"></a>你将生成

在本教程中，你将：

* 生成与注册的课程的学生反映一所大学的数据对象
* 生成从对象的数据库表
* 使用测试数据填充数据库
* 在 web 窗体中显示数据

## <a name="create-the-project"></a>创建项目

1. 在 Visual Studio 2017 中，创建**ASP.NET Web 应用程序 (.NET Framework)** 名为项目**ContosoUniversityModelBinding**。

   ![创建项目](retrieving-data/_static/image19.png)

2. 选择“确定”。 会显示对话框以选择一个模板。

   ![选择 web 窗体](retrieving-data/_static/image3.png)

3. 选择**Web 窗体**模板。 

4. 如有必要，更改到身份验证**单个用户帐户**。 

5. 选择“确定”创建项目。

## <a name="modify-site-appearance"></a>修改站点外观

   进行一些更改，以自定义站点外观。 
   
   1. 打开 Site.Master 文件。
   
   2. 更改显示的标题**Contoso University**而不**My ASP.NET Application**。

      [!code-aspx-csharp[Main](retrieving-data/samples/sample1.aspx?highlight=1)]

   3. 更改中的标头文本**应用程序名称**到**Contoso University**。

      [!code-aspx-csharp[Main](retrieving-data/samples/sample2.aspx?highlight=7)]

   4. 更改导航标题链接到合适的站点。 
   
      删除的链接**有关**并**联系人**并改为，将链接到**学生**页上，您将创建。

      [!code-aspx-csharp[Main](retrieving-data/samples/sample3.aspx)]

   5. 保存 Site.Master。

## <a name="add-a-web-form-to-display-student-data"></a>添加 web 窗体以显示学生数据

   1. 在中**解决方案资源管理器**，右键单击项目，选择**添加**，然后**新项**。 
   
   2. 在中**添加新项**对话框中，选择**包含母版页的 Web 窗体**模板并将其命名**Students.aspx**。

      ![创建页](retrieving-data/_static/image5.png)

   3. 选择“添加”。
   
   4. 对于 web 窗体的主页上，选择**Site.Master**。
   
   5. 选择“确定”。
   

## <a name="add-the-data-model"></a>添加数据模型

在中**模型**文件夹中，添加一个名为类**UniversityModels.cs**。

   1. 右键单击**模型**，选择**添加**，然后**新项**。 “添加新项”对话框随即出现。

   2. 从左侧的导航菜单中选择**代码**，然后**类**。

      ![创建模型类](retrieving-data/_static/image20.png)

   3. 将类命名**UniversityModels.cs** ，然后选择**添加**。

      在此文件中，定义`SchoolContext`， `Student`， `Enrollment`，和`Course`类，如下所示：

      [!code-csharp[Main](retrieving-data/samples/sample4.cs)]

      `SchoolContext`类派生自`DbContext`，这会管理数据库连接并更改数据中。

      在中`Student`类中，请注意，应用于的特性`FirstName`， `LastName`，和`Year`属性。 本教程中使用这些属性进行数据验证。 若要简化代码，只对这些属性用数据验证特性进行标记。 在实际项目中，会将验证特性应用到需要验证的所有属性。

   4. 保存 UniversityModels.cs。

## <a name="set-up-the-database-based-on-classes"></a>将基于类数据库设置

本教程使用[Code First 迁移](https://docs.microsoft.com/en-us/ef/ef6/modeling/code-first/migrations/)创建对象和数据库表。 这些表存储有关学生和他们的课程的信息。

   1. 选择**工具** > **NuGet 包管理器** > **程序包管理器控制台**。

   2. 在中**程序包管理器控制台**，运行以下命令：  
      `enable-migrations -ContextTypeName ContosoUniversityModelBinding.Models.SchoolContext`

      如果命令成功完成，会显示一条消息表明已启用迁移。

      ![启用迁移](retrieving-data/_static/image8.png)

      请注意，文件命名为*Configuration.cs*已创建。 `Configuration`类具有`Seed`方法，可以预先填充测试数据与数据库表。

## <a name="pre-populate-the-database"></a>在数据库中预先填充

   1. 打开 Configuration.cs。
   
   2. 将以下代码添加到 `Seed` 方法中。 此外，添加`using`语句`ContosoUniversityModelBinding. Models`命名空间。

      [!code-csharp[Main](retrieving-data/samples/sample5.cs)]

   3. 保存 Configuration.cs。

   4. 在包管理器控制台中，运行命令**添加迁移初始**。

   5. 运行命令**更新数据库**。

      如果运行此命令时收到异常`StudentID`并`CourseID`的值可能不同于`Seed`方法的值。 打开这些数据库表，并找到的现有值`StudentID`和`CourseID`。 将这些值添加到为种子设定代码`Enrollments`表。

## <a name="add-a-gridview-control"></a>添加 GridView 控件

使用填充的数据库数据，现在，你准备好检索该数据并将其显示。 

1. 打开 Students.aspx。

2. 找到`MainContent`占位符。 该占位符，在添加**GridView**包括此代码的控制。

   [!code-aspx-csharp[Main](retrieving-data/samples/sample6.aspx)]

   需要注意的事项：
   * 请注意，值设置为`SelectMethod`GridView 元素中的属性。 此值指定用于检索下一步中创建的 GridView 数据的方法。 
   
   * `ItemType`属性设置为`Student`前面创建的类。 此设置，可以引用的标记中的类属性。 例如，`Student`类具有一个名为集合`Enrollments`。 可以使用`Item.Enrollments`检索该集合，然后使用[LINQ 语法](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/linq/query-syntax-and-method-syntax-in-linq)若要检索每个学生的注册信用额度之和。
   
3. 保存 Students.aspx。

## <a name="add-code-to-retrieve-data"></a>添加代码以检索数据

   在 Students.aspx 代码隐藏文件中，将为指定的方法添加`SelectMethod`值。 
   
   1. 打开 Students.aspx.cs。
   
   2. 添加`using`语句`ContosoUniversityModelBinding. Models`和`System.Data.Entity`命名空间。

      [!code-csharp[Main](retrieving-data/samples/sample7.cs)]

   3. 将为指定的方法添加`SelectMethod`:

      [!code-csharp[Main](retrieving-data/samples/sample8.cs)]

      `Include`子句可以提高查询性能，但不是必需的。 无需`Include`子句中，数据使用检索[*延迟加载*](https://en.wikipedia.org/wiki/Lazy_loading)，其中涉及每次向数据库发送一个单独的查询相关的检索数据。 与`Include`子句中，数据使用检索*预先加载*，这意味着单一数据库查询检索所有相关的数据。 如果不使用相关的数据，预先加载是效率较低，因为检索更多的数据。 但是，在这种情况下，预先加载提供最佳性能，因为相关的数据显示为每个记录。

      有关加载时的性能注意事项的详细信息相关的数据，请参阅**延迟、 预先和显式加载相关数据的**主题中[使用实体框架在 ASP.NET 中读取相关数据MVC 应用程序](../../../../mvc/overview/getting-started/getting-started-with-ef-using-mvc/reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)一文。

      默认情况下，数据按标记为键属性的值。 您可以添加`OrderBy`子句指定不同的排序值。 在此示例中，默认值`StudentID`使用属性进行排序。 在中[排序、 分页和筛选数据](sorting-paging-and-filtering-data.md)文章中，用户能够选择列进行排序。
 
   4. 保存 Students.aspx.cs。

## <a name="run-your-application"></a>运行应用程序 

运行 web 应用程序 (**F5**)，并导航到**学生**页，该页显示以下信息：

   ![显示数据](retrieving-data/_static/image16.png)

## <a name="automatic-generation-of-model-binding-methods"></a>自动生成的模型绑定方法

学习本系列教程中，当您可以只需复制代码从本教程到你的项目。 但是，此方法的一个缺点是功能的，您可能会意识到的 Visual Studio 以自动生成的模型绑定方法代码提供。 您自己的项目上工作时，自动代码生成可以节省时间并获得如何实现的操作的某种意义上的帮助。 本部分介绍自动代码生成功能。 本部分只是信息性并不包含任何您需要在项目中实现的代码。 

当设置为值`SelectMethod`， `UpdateMethod`， `InsertMethod`，或`DeleteMethod`标记代码中的属性，可以选择**创建的新方法**选项。

![创建一个方法](retrieving-data/_static/image18.png)

Visual Studio 不仅具有正确签名的代码隐藏中创建一个方法，而且还会生成实现代码，以执行该操作。 如果首次设置`ItemType`属性前使用的自动代码生成功能，生成的代码使用该类型的操作。 例如，当设置`UpdateMethod`自动生成属性，以下代码：

[!code-csharp[Main](retrieving-data/samples/sample9.cs)]

同样，此代码不需要添加到你的项目。 在下一步的教程中，将实现用于更新、 删除和添加新数据的方法。

## <a name="summary"></a>总结

在本教程中，将创建数据模型类，并基于这些类生成数据库。 使用测试数据填充数据库表。 模型绑定用于从数据库检索数据，然后在 GridView 中显示数据。

在接下来[教程](updating-deleting-and-creating-data.md)在此系列中，则将启用更新、 删除和创建数据。

> [!div class="step-by-step"]
> [下一页](updating-deleting-and-creating-data.md)
