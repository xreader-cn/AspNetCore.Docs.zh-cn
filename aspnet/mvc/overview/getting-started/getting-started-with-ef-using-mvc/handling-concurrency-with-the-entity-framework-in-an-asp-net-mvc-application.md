---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
title: 教程：在 ASP.NET MVC 5 应用中处理并发和 EF
description: 本教程演示如何使用乐观并发，多个用户在同一时间更新同一实体时处理冲突。
author: tdykstra
ms.author: riande
ms.topic: tutorial
ms.date: 01/15/2019
ms.assetid: be0c098a-1fb2-457e-b815-ddca601afc65
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: b513d7d86d382068bc1a8f1bcc61289ee946d38b
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248298"
---
<a name="handling-concurrency-with-the-entity-framework-6-in-an-aspnet-mvc-5-application-10-of-12"></a>处理并发使用 Entity Framework 6 中的 ASP.NET MVC 5 应用程序 (10 / 12)
====================

# <a name="tutorial-handle-concurrency-with-ef-in-an-aspnet-mvc-5-app"></a>教程：在 ASP.NET MVC 5 应用中处理并发和 EF

之前的教程介绍了如何更新数据。 本教程演示如何使用乐观并发，多个用户在同一时间更新同一实体时处理冲突。 更改使用的网页`Department`实体以使它们处理并发错误。 下图显示了“编辑”和“删除”页面，包括发生并发冲突时显示的一些消息。

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

在本教程中，你将了解：


> [!div class="checklist"]
> * 了解并发冲突
> * 添加乐观并发
> * 修改部门控制器
> * 测试并发处理
> * 更新“删除”页


## <a name="prerequisites"></a>系统必备

* [异步和存储过程](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="concurrency-conflicts"></a>并发冲突

当某用户显示实体数据以对其进行编辑，而另一用户在上一用户的更改写入数据库之前更新同一实体的数据时，会发生并发冲突。 如果不启用此类冲突的检测，则最后更新数据库的人员将覆盖其他用户的更改。 在许多应用程序中，此风险是可接受的：如果用户很少或更新很少，或者一些更改被覆盖并不重要，则并发编程可能弊大于利。 在此情况下，不必配置应用程序来处理并发冲突。

### <a name="pessimistic-concurrency-locking"></a>悲观并发 （锁定）

如果应用程序确实需要防止并发情况下出现意外数据丢失，一种方法是使用数据库锁定。 这称为*保守式并发*。 例如，在从数据库读取一行内容之前，请求锁定为只读或更新访问。 如果将一行锁定为更新访问，则其他用户无法将该行锁定为只读或更新访问，因为他们得到的是正在更改的数据的副本。 如果将一行锁定为只读访问，则其他人也可将其锁定为只读访问，但不能进行更新。

管理锁定有缺点。 编程可能很复杂。 它需要大量的数据库管理资源，且随着应用程序用户数量的增加，可能会导致性能问题。 由于这些原因，并不是所有的数据库管理系统都支持悲观并发。 实体框架不提供内置支持，以及本教程不展示如何实现它。

### <a name="optimistic-concurrency"></a>开放式并发

悲观并发的替代方法是*乐观并发*。 悲观并发是指允许发生并发冲突，并在并发冲突发生时作出正确反应。 例如，John 运行部门编辑页上，更改**预算**金额为 0.00 美元将英语系从 350,000.00 美元。

John 单击之前**保存**，Jane 运行相同的页和更改**开始日期**字段从 9/1/2007年到 2013 年 8 月 8 日。

John 单击**保存**第一个和他的更改时在浏览器返回索引页上，然后 Jane 单击将看到**保存**。 接下来的情况取决于并发冲突的处理方式。 其中一些选项包括：

- 可以跟踪用户已修改的属性，并仅更新数据库中相应的列。 在示例方案中，不会有数据丢失，因为是由两个用户更新不同的属性。 下次有人浏览英语系时，他们将看到 Jane 和 John 的更改 — 一个开始日期为 2013 年 8 月 8 日的和预算为零美元。

    这种更新方法可减少可能导致数据丢失的冲突次数，但是如果对实体的同一属性进行竞争性更改，则数据难免会丢失。 Entity Framework 是否以这种方式工作取决于更新代码的实现方式。 通常不适合在 Web 应用程序中使用，因为它要求保持大量的状态，以便跟踪实体的所有原始属性值以及新值。 维护大量的状态可能会影响应用程序的性能，因为它需要服务器资源或必须包含在网页本身（例如隐藏字段）或 Cookie 中。
- 可以让 John 的更改覆盖 Jane 的更改。 下次有人浏览英语系时，他们将看到 2013 年 8 月 8 日和还原的值 350,000.00 美元。 这称为“客户端优先”或“最后一个优先”。 （客户端的所有值优先于数据存储的值。）正如本部分的介绍所述，如果不为并发处理编写任何代码，则自动执行此操作。
- 您可以防止 Jane 的更改更新数据库中。 通常情况下，将显示一条错误消息，给她提供数据的当前状态，让其以如果仍想要使其重新应用其更改。 这称为“存储优先”方案。 （数据存储值优先于客户端提交的值。）本教程将执行“存储优先”方案。 此方法可确保用户在未收到具体发生内容的警报时，不会覆盖任何更改。

### <a name="detecting-concurrency-conflicts"></a>检测并发冲突

您可以通过处理解决冲突[OptimisticConcurrencyException](https://msdn.microsoft.com/library/system.data.optimisticconcurrencyexception.aspx) Entity Framework 引发的异常。 为了知道何时引发这些异常，Entity Framework 必须能够检测到冲突。 因此，你必须正确配置数据库和数据模型。 启用冲突检测的某些选项包括：

- 数据库表中包含一个可用于确定某行更改时间的跟踪列。 然后，可以配置实体框架，将该列中的包含`Where`的 SQL 子句`Update`或`Delete`命令。

    跟踪列的数据类型通常是[rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx)。 [Rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx)值是每次更新行时递增的序列号。 在中`Update`或`Delete`命令，`Where`子句包含跟踪列 （原始行版本） 的原始值。 如果正在更新的行已由另一个用户中的值更改`rowversion`列是不同于原始值，因此`Update`或`Delete`语句找不到要由于更新的行`Where`子句。 当实体框架将查找已由更新任何行`Update`或`Delete`命令 （即，在受影响的行数为零），将其解释为并发冲突。
- 配置实体框架，以便在表中包含的每个列的原始值`Where`子句`Update`和`Delete`命令。

    如下所示的第一个选项，如果自第一次读取一行，该行中的任何内容已更改`Where`子句不会返回的行，若要更新，该实体框架将解释为并发冲突。 对于包含许多列的数据库表，这种方法可能会导致非常大`Where`子句，并可能需要维护大量的状态。 如前所述，维持大量的状态会影响应用程序的性能。 因此通常不建议使用此方法，并且它也不是本教程中使用的方法。

    如果你确实想要实现此并发方法，则必须将您想要跟踪并发机制，通过添加在实体中的所有非主键属性标记[ConcurrencyCheck](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.concurrencycheckattribute.aspx)属性到它们。 更改使实体框架要包含在 SQL 中的所有列`WHERE`子句`UPDATE`语句。

在本教程的其余部分将添加[rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx)跟踪属性设置为`Department`实体，创建一个控制器和视图，并进行测试以验证是否一切运行正常。

## <a name="add-optimistic-concurrency"></a>添加乐观并发

在中*Models\Department.cs*，添加一个名为跟踪属性`RowVersion`:

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=20-22)]

[时间戳](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx)属性指定此列将包含在`Where`子句`Update`和`Delete`命令发送到数据库。 该属性称为[时间戳](https://msdn.microsoft.com/library/system.componentmodel.dataannotations.timestampattribute.aspx)因为以前版本的 SQL Server 使用 SQL[时间戳](https://msdn.microsoft.com/library/ms182776(v=SQL.90).aspx)之前使用 SQL 数据类型[rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx)替换它。 .Net 类型[rowversion](https://msdn.microsoft.com/library/ms182776(v=sql.110).aspx)是一个字节数组。

如果你愿意使用 fluent API，则可以使用[IsConcurrencyToken](https://msdn.microsoft.com/library/gg679501(v=VS.103).aspx)方法，以指定跟踪属性，如下面的示例中所示：

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]

通过添加属性，更改了数据库模型，因此需要再执行一次迁移。 在包管理器控制台 (PMC) 中输入以下命令：

[!code-console[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cmd)]

## <a name="modify-department-controller"></a>修改部门控制器

在中*Controllers\DepartmentController.cs*，添加`using`语句：

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

在中*DepartmentController.cs*文件中，将所有四个出现的"LastName"更改为"FullName"，以便院系管理员下拉列表将包含讲师的全名，而不是只是最后一个名称。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs?highlight=1)]

为现有代码替换为`HttpPost``Edit`方法使用以下代码：

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

如果 `FindAsync` 方法返回 NULL，则该院系已被另一用户删除。 所示的代码使用已发布的表单值创建 department 实体，以便编辑页可以重新显示一条错误消息。 或者，如果仅显示错误消息而未重新显示院系字段，则不必重新创建 Department 实体。

该视图将存储原始`RowVersion`隐藏的字段和方法中的值接收在`rowVersion`参数。 在调用 `SaveChanges` 之前，必须将该原始 `RowVersion` 属性值置于实体的 `OriginalValues` 集合中。 然后当 Entity Framework 创建 SQL`UPDATE`命令时，命令将包括`WHERE`子句，用于查找的行具有原始`RowVersion`值。

如果没有行受到`UPDATE`命令 (没有行具有原始`RowVersion`值)，实体框架将引发`DbUpdateConcurrencyException`异常和中的代码`catch`块获取受影响`Department`的异常中的实体对象。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

此对象具有新值在用户输入其`Entity`属性，并且可以获取通过调用从数据库读取的值`GetDatabaseValues`方法。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]

`GetDatabaseValues`方法将返回 null，如果有人已从数据库中删除行; 否则，您需要对返回的对象转换`Department`若要访问的类`Department`属性。 (已检查的删除，因为`databaseEntry`将为 null 院系已被删除后，才`FindAsync`执行之前`SaveChanges`执行。)

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs)]

接下来，代码将添加具有不同于在编辑页上输入的用户的数据库值的每个列的自定义错误消息：

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs)]

发生了什么情况以及如何进行处理，解释了较长的错误消息：

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.cs)]

最后，代码将设置`RowVersion`的值`Department`从数据库中检索到的新值的对象。 重新显示“编辑”页时，这个新的 `RowVersion` 值将存储在隐藏字段中，当用户下次单击“保存”时，将只捕获自“编辑”页重新显示起发生的并发错误。

在中*Views\Department\Edit.cshtml*，添加隐藏的字段以保存`RowVersion`属性值，紧跟的隐藏的字段`DepartmentID`属性：

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample12.cshtml?highlight=18)]

## <a name="test-concurrency-handling"></a>测试并发处理

运行站点，并单击**部门**。

右键单击**编辑**英语系和选择的超链接**新选项卡中打开**然后单击**编辑**英语系的超链接。 两个选项卡显示相同的信息。

在第一个浏览器选项卡中更改一个字段，然后单击“保存”。

浏览器显示具有更改值的索引页。

更改第二个浏览器选项卡中的字段，然后单击**保存**。 看见一条错误消息：

![Department_Edit_page_2_after_clicking_Save](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image10.png)

再次单击“保存”。 在第二个浏览器选项卡中输入的值是随原始值的第一个浏览器中更改的数据一起保存。 在索引页中出现时，可以看到已保存的值。

## <a name="update-the-delete-page"></a>更新“删除”页

对于“删除”页，Entity Framework 以类似方式检测其他人编辑院系所引起的并发冲突。 当`HttpGet``Delete`方法会显示确认视图，该视图包含原始`RowVersion`隐藏字段中的值。 值为则供`HttpPost``Delete`在用户确认删除时调用的方法。 当 Entity Framework 创建 SQL`DELETE`命令时，它包括`WHERE`子句与原始`RowVersion`值。 如果命令导致零行受影响 （即数据行进行了更改后显示删除确认页），则引发并发异常，并`HttpGet Delete`错误标志设置为调用方法`true`以重新显示具有一条错误消息的确认页面。 还有可能零行受影响，因为该行已由另一个用户删除，因此在这种情况下显示不同的错误消息。

在中*DepartmentController.cs*，替换`HttpGet``Delete`方法使用以下代码：

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample13.cs)]

该方法接受可选参数，该参数指示是否在并发错误之后重新显示页面。 如果此标志`true`，一条错误消息发送到视图使用`ViewBag`属性。

中的代码替换`HttpPost``Delete`方法 (名为`DeleteConfirmed`) 使用以下代码：

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample14.cs)]

在刚替换的基架代码中，此方法仅接受记录 ID：

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample15.cs)]

你已更改此参数为`Department`模型绑定器创建的实体实例。 这使您可以访问`RowVersion`除记录键之外的属性值。

[!code-csharp[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample16.cs)]

你还将操作方法名称从 `DeleteConfirmed` 更改为了 `Delete`。 基架的代码名为`HttpPost``Delete`方法`DeleteConfirmed`以便`HttpPost`方法唯一的签名。 （CLR 要求重载的方法具有不同的方法参数。）签名是唯一的现在可以继续使用 MVC 约定，使用相同的名称`HttpPost`和`HttpGet`删除方法。

如果捕获到并发错误，代码将重新显示“删除”确认页，并提供一个指示它应显示并发错误消息的标志。

在中*Views\Department\Delete.cshtml*，基架的代码替换为以下代码添加错误消息字段和隐藏的字段的 DepartmentID 和 RowVersion 属性。 突出显示所作更改。

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample17.cshtml?highlight=9-10,21,52-54)]

此代码将添加一条错误消息之间`h2`和`h3`标题：

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample18.cshtml)]

它将替换`LastName`与`FullName`中`Administrator`字段：

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample19.cshtml)]

最后，它将添加隐藏的字段`DepartmentID`并`RowVersion`后的，属性`Html.BeginForm`语句：

[!code-cshtml[Main](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample20.cshtml)]

运行院系索引页。 右键单击**删除**英语系和选择的超链接**新选项卡中打开**然后在第一个选项卡中单击**编辑**英语系的超链接。

在第一个窗口，更改其中一个值，然后单击**保存**。

索引页会反映此更改。

在第二个选项卡中，单击“删除”。

你将看到并发错误消息，且已使用数据库中的当前内容刷新了“院系”值。

![Department_Delete_confirmation_page_with_concurrency_error](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image15.png)

如果再次单击“删除”，会重定向到已删除显示院系的索引页。

## <a name="get-the-code"></a>获取代码

[下载已完成的项目](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>其他资源

其他实体框架资源的链接可在[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。

有关其他方法来处理各种方案，并发的信息，请参阅[乐观并发模式](https://msdn.microsoft.com/data/jj592904)并[属性值使用方面](https://msdn.microsoft.com/data/jj592677)MSDN 上。 下一步的教程演示如何实现的每个层次结构一个表继承`Instructor`和`Student`实体。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 介绍了并发冲突
> * 添加了乐观并发
> * 修改后的部门控制器
> * 测试的并发处理
> * 更新删除页

转到下一步的文章，了解如何在数据模型中实现继承。
> [!div class="nextstepaction"]
> [数据模型中实现继承](implementing-inheritance-with-the-entity-framework-in-an-asp-net-mvc-application.md)
