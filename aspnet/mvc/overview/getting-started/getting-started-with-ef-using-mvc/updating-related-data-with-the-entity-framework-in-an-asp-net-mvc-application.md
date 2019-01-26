---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
title: 教程：在 ASP.NET MVC 应用中使用 EF 更新相关的数据
description: 在本教程中将更新相关的数据。 对于大多数关系，这可以通过更新外键字段或导航属性。
author: tdykstra
ms.author: riande
ms.date: 01/17/2019
ms.topic: tutorial
ms.assetid: 7ba88418-5d0a-437d-b6dc-7c3816d4ec07
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: daefbcea39dc341c6fc72eb344b05eb2dbf0c3ba
ms.sourcegitcommit: d5223cf6a2cf80b4f5dc54169b0e376d493d2d3a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54889842"
---
# <a name="tutorial-update-related-data-with-ef-in-an-aspnet-mvc-app"></a>教程：在 ASP.NET MVC 应用中使用 EF 更新相关的数据

上一教程中显示相关的数据。 在本教程中将更新相关的数据。 对于大多数关系，这可以通过更新外键字段或导航属性。 对于多对多关系，实体框架不会联接表直接公开，因此添加和删除实体与相应的导航属性。

下图是一些将会用到的页面。

![Course_create_page](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![Instructor_edit_page_with_courses](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

![讲师编辑课程](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

在本教程中，你将了解：

> [!div class="checklist"]
> * 自定义课程页
> * 将 office 添加到讲师页
> * 向讲师页添加课程
> * 更新 DeleteConfirmed
> * 向创建页添加办公室位置和课程

## <a name="prerequisites"></a>系统必备

* [读取相关数据](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="customize-courses-pages"></a>自定义课程页

创建新的课程实体时，新实体必须与现有院系有关系。 为此，基架代码需包括控制器方法、创建视图和编辑视图，且视图中应包括用于选择院系的下拉列表。 下拉列表设置`Course.DepartmentID`外键属性，而这正是 Entity Framework 加载所需`Department`导航属性具有相应`Department`实体。 将用到基架代码，但需对其稍作更改，以便添加错误处理和对下拉列表进行排序。

在中*CourseController.cs*，删除四个`Create`和`Edit`方法，并用它们替换下面的代码：

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=3,11-12,19-25,40,44,46,48-78)]

以下代码添加到`using`文件的开头的语句：

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

`PopulateDepartmentsDropDownList`方法获取按名称排序的所有部门列表，创建`SelectList`集合有关的下拉列表，并将集合传递给视图`ViewBag`属性。 该方法可以使用可选的 `selectedDepartment` 参数，而调用的代码可以通过该参数来指定呈现下拉列表时被选择的项。 该视图会将名称传递`DepartmentID`到[DropDownList](../../older-versions/working-with-the-dropdownlist-box-and-jquery/using-the-dropdownlist-helper-with-aspnet-mvc.md)帮助器，该帮助器就会知道要查找的`ViewBag`对象`SelectList`名为`DepartmentID`。

`HttpGet` `Create`方法调用`PopulateDepartmentsDropDownList`方法而无需设置的所选的项，因为新课程的院系尚未建立：

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs)]

`HttpGet` `Edit`方法设置选定的项，基于已分配给正在编辑的课程的院系 ID:

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs?highlight=12)]

`HttpPost`两个方法`Create`和`Edit`还包括设置选定的项时它们在错误之后重新显示页面的代码：

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=6)]

此代码可确保当页面重新显示错误消息，选择任何院系保持所选。

课程视图都已基架的 department 字段的下拉列表，但不想 DepartmentID 标题为此字段，以便进行以下突出显示将更改为*Views\Course\Create.cshtml*文件为更改标题。

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml?highlight=43)]

进行中相同的更改*Views\Course\Edit.cshtml*。

通常情况下基架不会创建基架主键因为键值由数据库生成和不能更改并不是有意义的值来向用户显示。 基架为 Course 实体包括文本框`CourseID`字段，因为它能够理解的`DatabaseGeneratedOption.None`属性意味着用户应该可以输入的主键值。 但并不理解，因为的数量是有意义你想要在其他视图中看到它，因此需要手动添加它。

在中*Views\Course\Edit.cshtml*，添加一个课程编号字段之前**标题**字段。 因为它是主键，它将显示，但不能更改。

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cshtml)]

已存在一个隐藏的字段 (`Html.HiddenFor`帮助器) 编辑视图中的课程编号。 添加*Html.LabelFor*帮助程序也同样需要隐藏字段，因为它不会导致当用户单击时，已发布数据中包括课程编号**保存**编辑页上。

在中*Views\Course\Delete.cshtml*并*Views\Course\Details.cshtml*，更改为"部门"院系名称标题从"名称"并添加一个课程编号字段之前**标题**字段。

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cshtml?highlight=2,9-15)]

运行**创建**页面 (显示课程索引页，然后单击**创建新**) 并输入新课程的数据：

| “值” | 设置 |
| ----- | ------- |
| 数字 | 输入*1000年*。 |
| 标题 | 输入*代数*。 |
| 信用 | 输入*4*。 |
|Department | 选择**数学**。 |

单击 **“创建”**。 课程索引页将显示新课程添加到列表中。 索引页列表中的院系名称来自导航属性，表明已正确建立关系。

运行**编辑**页面 (显示课程索引页，然后单击**编辑**课程上)。

更改页面上的数据，然后单击“保存”。 课程索引页将显示已更新的课程数据。

## <a name="add-office-to-instructors-page"></a>将 office 添加到讲师页

编辑讲师记录时，有时希望能更新讲师的办公室分配。 `Instructor`实体具有对零或一一的关系与`OfficeAssignment`实体，这意味着必须处理以下情况：

- 如果用户清除办公室分配，并且它最初具有一个值，必须删除并删除`OfficeAssignment`实体。
- 如果用户输入了办公室分配值并且该值最初为空，则必须创建一个新`OfficeAssignment`实体。
- 如果用户更改了办公室分配的值，则必须更改中的现有值`OfficeAssignment`实体。

打开*InstructorController.cs*并查看`HttpGet``Edit`方法：

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

基架的代码不符合要求。 它设置数据的下拉列表中，但您所需要的是文本框。 此方法替换为以下代码：

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs?highlight=7-10)]

此代码将删除`ViewBag`语句，并将关联的预先加载添加`OfficeAssignment`实体。 无法执行与预先加载`Find`方法，因此`Where`和`Single`改为使用方法来选择讲师。

替换`HttpPost``Edit`用下面的代码的方法。 用于处理办公室分配更新：

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

对引用`RetryLimitExceededException`需要`using`语句。 若要将其添加，悬停在`RetryLimitExceededException`。 将出现此问题的说明。 选择**显示潜在修复**，然后单击**使用 System.Data.Entity.Infrastructure;**。

![解析为重试异常](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image7.png)

该代码执行以下操作：

- 将方法名称更改为`EditPost`由于签名现与相同`HttpGet`方法 (`ActionName`特性指定仍然使用 /Edit/ URL)。
- 使用 `OfficeAssignment` 导航属性的预先加载从数据库获取当前的 `Instructor` 实体。 这是与中的操作相同`HttpGet``Edit`方法。
- 用模型绑定器中的值更新检索到的 `Instructor` 实体。 [TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.108).aspx)使用重载使你能够*允许列表*你想要包括的属性。 这可以防止过度提交，如中所述[第二个教程](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)。

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cs)]
- 如果办公室位置为空，则设置`Instructor.OfficeAssignment`属性设置为 null，以便中的相关的行`OfficeAssignment`表将被删除。

    [!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]
- 将更改保存到数据库。

在*Views\Instructor\Edit.cshtml*后面`div`元素**雇佣日期**字段中，添加新的字段编辑办公室位置：

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cshtml)]

运行页 (选择**讲师**选项卡，然后单击**编辑**上)。 更改“办公室位置”，然后单击“保存”。

## <a name="add-courses-to-instructors-page"></a>向讲师页添加课程

讲师可能教授任意数量的课程。 现在，您将通过添加的功能来更改课程分配使用一组复选框来增强讲师编辑页面。

之间的关系`Course`和`Instructor`实体是多对多，这意味着您不具有直接访问该联接表中的外键属性。 相反，添加和删除实体和`Instructor.Courses`导航属性。

用于更改讲师所对应的课程的 UI 是一组复选框。 该复选框中会显示数据库中的所有课程，选中讲师当前对应的课程即可。 用户可以通过选择或清除复选框来更改课程分配。 如果课程数量过大，可能想要使用不同的方法在视图中，显示数据的但会使用相同的操作才能创建或删除关系的导航属性的方法。

若要为复选框列表的视图提供数据，将使用视图模型类。 创建*AssignedCourseData.cs*中*Viewmodel*文件夹并将替换为以下代码的现有代码：

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs)]

在中*InstructorController.cs*，替换`HttpGet``Edit`用下面的代码的方法。 突出显示所作更改。

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs?highlight=9,12,20-35)]

该代码为 `Courses` 导航属性添加了预先加载，并调用新的 `PopulateAssignedCourseData` 方法使用 `AssignedCourseData` 视图模型类为复选框数组提供信息。

中的代码`PopulateAssignedCourseData`方法读取所有`Course`才能加载一系列课程使用视图的实体的模型。 对每门课程而言，该代码都会检查讲师的 `Courses` 导航属性中是否存在该课程。 若要创建高效的查找，检查是否分配给讲师的课程时，分配给讲师的课程将放入[HashSet](https://msdn.microsoft.com/library/bb359438.aspx)集合。 `Assigned`属性设置为`true`讲师的课程分配。 视图将使用此属性来确定应将哪些复选框显示为选中状态。 最后，该列表传递给视图中`ViewBag`属性。

接下来，添加用户单击“保存”时执行的代码。 替换`EditPost`方法调用更新的新方法的以下代码替换`Courses`导航属性`Instructor`实体。 突出显示所作更改。

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cs?highlight=3,11,25,37,40-68)]

方法签名现在是不同于`HttpGet``Edit`方法，因此方法名称将从`EditPost`回`Edit`。

由于视图不包含一系列`Course`实体，模型绑定器不能自动更新`Courses`导航属性。 而不是使用模型联编程序来更新`Courses`导航属性，则将执行该操作在新`UpdateInstructorCourses`方法。 为此，需要从模型绑定中排除 `Courses` 属性。 这不需要对调用代码的任何更改[TryUpdateModel](https://msdn.microsoft.com/library/dd470908(v=vs.98).aspx)因为您正在使用*允许列表*重载和`Courses`不在包括列表中。

如果没有复选框已选中中的代码`UpdateInstructorCourses`初始化`Courses`具有一个空集合导航属性：

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cs)]

之后，代码会循环访问数据库中的所有课程，并逐一检查当前分配给讲师的课程和视图中处于选中状态的课程。 为便于高效查找，后两个集合存储在 `HashSet` 对象中。

如果某课程的复选框处于选中状态，但该课程不在 `Instructor.Courses` 导航属性中，则会将该课程添加到导航属性中的集合中。

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cs)]

如果某课程的复选框未处于选中状态，但该课程存在 `Instructor.Courses` 导航属性中，则会从导航属性中删除该课程。

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cs)]

在中*Views\Instructor\Edit.cshtml*，添加**课程**字段添加下面的复选框的数组的代码后立即`div`元素`OfficeAssignment`字段和之前`div`的元素**保存**按钮：

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample21.cshtml)]

粘贴后的代码中，如果其中的换行符和缩进看起来不像此处，手动修复所有问题，以便它如下所示，请参阅此处。 缩进不一定要完美，但 `@</tr><tr>`、`@:<td>`、`@:</td>` 和 `@</tr>` 行必须各成一行（如下所示），否则会出现运行时错误。

此代码将创建一个具有三列的 HTML 表。 每个列中都有一个复选框，随后是一段由课程编号和标题组成的描述文字。 所有复选框均都具有相同的名称 ("selectedCourses")，通知它们是作为一个组来处理的模型联编程序。 `value`的每个复选框的属性设置为值`CourseID.`发布页面时，模型绑定器会将数组传递给控制器组成`CourseID`仅复选框进行选择的值。

最初呈现复选框，用于分配给讲师的课程具有`checked`属性，后者选择它们 （显示处于选中状态）。

更改课程分配之后, 你将想要能够验证所做的更改，当在站点恢复时`Index`页。 因此，需要将列添加到该页面中的表。 在这种情况下不需要使用`ViewBag`对象，因为已经在你想要显示的信息`Courses`导航属性`Instructor`您传递到页作为模型的实体。

在中*Views\Instructor\Index.cshtml*，添加**课程**标题紧跟**Office**标题，如下面的示例中所示：

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample22.cshtml?highlight=6)]

然后，添加新的详细信息单元格紧跟 office 位置详细信息单元：

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample23.cshtml?highlight=7-14)]

运行**讲师索引**页面，查看分配给每位讲师的课程。

单击**编辑**上以查看编辑页。

更改某些课程分配，然后单击**保存**。 所作更改将反映在索引页上。

 注意:此处所使用的编辑讲师课程数据的方法适用于有限数量的课程。 若是远大于此的集合，则需要使用不同的 UI 和不同的更新方法。

## <a name="update-deleteconfirmed"></a>更新 DeleteConfirmed

在中*InstructorController.cs*，删除`DeleteConfirmed`方法并插入以下代码在其原位置。

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample24.cs?highlight=5-8,12-18)]

此代码会进行以下更改：

- 如果以管理员身份的任何部门分配讲师，将从该部门中删除讲师分配。 如果尝试删除作为某个部门的管理员分配一名讲师，而无需此代码中，则会得到引用完整性错误。

此代码不会处理作为多个部门的管理员分配的一名讲师对应的方案。 在最后一个教程将添加代码，以防止发生这种情况。

## <a name="add-office-location-and-courses-to-the-create-page"></a>向创建页添加办公室位置和课程

在中*InstructorController.cs*，删除`HttpGet`并`HttpPost``Create`方法，然后在其位置添加以下代码：


[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample25.cs)]

此代码是类似于你看到的针对 Edit 方法只不过最初选择任何课程。 `HttpGet` `Create`方法调用`PopulateAssignedCourseData`方法不是因为可能有课程处于选中状态但在进行排序以提供有关一个空集合`foreach`（否则查看代码将引发空引用异常在视图中的循环).

创建 HttpPost 方法将每个选定的课程添加到之前的模板代码，检查的验证错误并向数据库添加新讲师的课程导航属性。 即使存在模型错误也会添加课程，以便当存在模型错误 （例如，用户键入了无效的日期），以便当页面重新显示并显示错误消息，会自动还原所做的任何课程选择。

请注意，为了能够向 `Courses` 导航属性添加课程，必须将该属性初始化为空集合：

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample26.cs)]

除了在控制器代码中进行此操作之外，还可以在“导师”模型中进行此操作，方法是将该属性更改为不存在集合时自动创建集合，如以下示例所示：

[!code-csharp[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample27.cs)]

如果通过这种方式修改 `Courses` 属性，则可以删除控制器中的显式属性初始化代码。

在中*Views\Instructor\Create.cshtml*、 添加办公室位置文本框和课程的复选框，雇佣日期字段之后，并在**提交**按钮。

[!code-cshtml[Main](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample28.cshtml)]

粘贴代码后，可以修复换行符和缩进，像之前的编辑页。

运行创建页，并添加一名讲师。

<a id="transactions"></a>

## <a name="handling-transactions"></a>处理事务

中所述[基本的 CRUD 功能教程](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application.md)，默认情况下 Entity Framework 隐式实现事务。 有关在需要更多的控制-例如，如果你想要包括在事务中-Entity Framework 外部完成的操作的方案，请参阅[使用事务](https://msdn.microsoft.com/data/dn456843)MSDN 上。

## <a name="get-the-code"></a>获取代码

[下载已完成的项目](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>其他资源

其他实体框架资源的链接可在[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。

## <a name="next-step"></a>下一步

在本教程中，你将了解：

> [!div class="checklist"]
> * 自定义的课程页
> * 添加了的 office 到讲师页
> * 向讲师页添加的课程
> * 更新的 DeleteConfirmed
> * 添加的办公室位置和课程向创建页

转到下一步的文章，了解如何实现异步编程模型。
> [!div class="nextstepaction"]
> [异步编程模型](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application.md)
