---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
title: 教程：在 ASP.NET MVC 中实现与实体框架 CRUD 功能 |Microsoft Docs
description: 查看和自定义创建、 读取、 更新、 删除 (CRUD) 代码，可在控制器和视图中自动创建的 MVC 基架。
author: tdykstra
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: a2f70ba4-83d1-4002-9255-24732726c4f2
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 9ed388543dd54d209ff2a0b92df4f7659962582c
ms.sourcegitcommit: c47d7c131eebbcd8811e31edda210d64cf4b9d6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55236466"
---
# <a name="tutorial-implement-crud-functionality-with-the-entity-framework-in-aspnet-mvc"></a>教程：在 ASP.NET MVC 中实现与实体框架 CRUD 功能

在中[前一篇教程](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)，创建的存储和显示数据使用 Entity Framework (EF) 6 和 SQL Server LocalDB 的 MVC 应用程序。 在本教程中，您查看和自定义创建、 读取、 更新、 删除 (CRUD) MVC 基架自动为你创建在控制器和视图中的代码。

> [!NOTE]
> 为了在控制器和数据访问层之间创建一个抽象层，常见的做法是实现存储库模式。 若要保持这些教程内容简单并重点介绍如何使用 EF 6 本身，它们不使用存储库。 有关如何实现存储库的信息，请参阅[ASP.NET 数据访问内容映射](../../../../whitepapers/aspnet-data-access-content-map.md)。

下面是你创建的 web 页面的示例：

![学生详细信息页的屏幕截图。](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image1.png)

![学生的屏幕截图创建页。](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image2.png)

![屏幕截图 ot 学生删除页。](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image3.png)

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建详细信息页
> * 更新“创建”页
> * 更新的 HttpPost 编辑方法
> * 更新“删除”页
> * 关闭数据库连接
> * 处理事务

## <a name="prerequisites"></a>系统必备

* [创建实体框架数据模型](creating-an-entity-framework-data-model-for-an-asp-net-mvc-application.md)

## <a name="create-a-details-page"></a>创建详细信息页

面向学生的基架的代码`Index`页上留出`Enrollments`属性，因为该属性包含一个集合。 在`Details`页上，将集合的内容显示在 HTML 表。

在中*Controllers\StudentController.cs*，为操作方法`Details`查看使用[查找](https://msdn.microsoft.com/library/gg696418(v=VS.103).aspx)方法来检索单个`Student`实体。

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample1.cs)]

密钥的值传递给方法作为`id`参数和来自*将数据路由*中**详细信息**索引页上的超链接。

### <a name="tip-route-data"></a>提示:**路由数据**

路由数据是在路由表中指定的 URL 段中找到的模型联编程序的数据。 例如，指定默认路由`controller`， `action`，和`id`段：

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample2.cs?highlight=3)]

在以下 URL，默认路由映射`Instructor`作为`controller`，`Index`作为`action`1 赋`id`; 这些是路由数据值。

`http://localhost:1230/Instructor/Index/1?courseID=2021`

`?courseID=2021` 查询字符串值。 如果传递的模型联编程序也能`id`作为查询字符串值：

`http://localhost:1230/Instructor/Index?id=1&CourseID=2021`

通过创建 Url `ActionLink` Razor 视图中的语句。 在下面的代码中，`id`参数匹配的默认路由，因此`id`添加到路由数据。

[!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample3.cshtml)]

在下面的代码，`courseID`不匹配默认路由中的参数，因此，它将添加为查询字符串。

[!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample4.cshtml)]

### <a name="to-create-the-details-page"></a>若要创建的详细信息页

1. 打开*Views\Student\Details.cshtml*。

   使用显示每个字段`DisplayFor`帮助程序，如下面的示例中所示：

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample5.cshtml)]

2. 之后`EnrollmentDate`字段，然后立即在关闭前`</dl>`标记中，添加突出显示的代码以显示注册列表，如下面的示例中所示：

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample6.cshtml?highlight=8-29)]

    如果代码缩进在粘贴代码后出现错误，按**Ctrl**+**K**， **Ctrl**+**D**要设置格式它。

    此代码循环通过 `Enrollments` 导航属性中的实体。 每个`Enrollment`实体在属性中，它将显示课程标题和成绩。 课程标题检索从`Course`中存储的实体`Course`导航属性`Enrollments`实体。 所有这些数据是从数据库中检索自动在需要时。 换而言之，使用延迟加载此处。 未指定*预先加载*为`Courses`导航属性，因此有学生在同一查询中检索不到注册的。 相反，第一次尝试访问`Enrollments`导航属性，一个新的查询发送到数据库以检索数据。 你可以阅读更多有关延迟加载和预先加载在[读取相关数据](reading-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)本系列后面的教程。

3. 通过启动该程序打开的详细信息页 (**Ctrl**+**F5**)，选择**学生**选项卡上，，然后单击**的详细信息** Alexander Carson 的链接。 (如果按下**Ctrl**+**F5**而*Details.cshtml*文件处于打开状态，你收到 HTTP 400 错误。 这是因为 Visual Studio 尝试运行详细信息页上，但它未达到指定的学生，若要显示的链接。 如果发生这种情况，从 URL 中删除"学生/详细信息"和重试，或者，关闭浏览器中，右键单击项目，并单击**视图** > **用浏览器查看**。)

    看到所选学生的课程和成绩列表。

4. 关闭浏览器。

## <a name="update-the-create-page"></a>更新“创建”页

1. 在中*Controllers\StudentController.cs*，替换<xref:System.Web.Mvc.HttpPostAttribute>`Create`用下面的代码的操作方法。 此代码将添加`try-catch`块而不会`ID`从<xref:System.Web.Mvc.BindAttribute>基架生成的方法的属性：

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample7.cs?highlight=3,5-6,13-18)]

    此代码将添加`Student`ASP.NET MVC 模型绑定器创建实体到`Students`实体设置，并将所做的更改保存到数据库。 *模型联编程序*指的是 ASP.NET MVC 功能，使其更轻松地处理提交的窗体的数据; 模型联编程序将发布窗体到 CLR 类型值，并将其传递给操作方法的参数。 在这种情况下，模型绑定器实例化`Student`实体为你使用属性值从`Form`集合。

    您删除`ID`从绑定属性，因为`ID`是 SQL Server 将插入行时自动设置的主键值。 来自用户的输入不会设置`ID`值。

    <a id="overpost"></a>

    ### <a name="security-warning---the-validateantiforgerytoken-attribute-helps-prevent-cross-site-request-forgerysecurityxsrfcsrf-prevention-in-aspnet-mvc-and-web-pagesmd-attacks-it-requires-a-corresponding-htmlantiforgerytoken-statement-in-the-view-which-youll-see-later"></a>安全警告-`ValidateAntiForgeryToken`特性帮助抵御[跨站点请求伪造](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md)攻击。 它需要相应`Html.AntiForgeryToken()`语句在视图中，您将会看到更高版本。

    `Bind`属性是一种方法来防止*过度发布*创建方案中。 例如，假设`Student`实体包含`Secret`不希望此网页设置的属性。

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample8.cs?highlight=7)]

    即使您没有`Secret`字段在网页上，黑客可以使用一种工具如[fiddler](http://fiddler2.com/home)，或编写一些 JavaScript 来发布`Secret`表单值。 无需<xref:System.Web.Mvc.BindAttribute>特性来限制模型绑定器在创建时使用的字段`Student`实例<em>，</em>模型绑定器会选取该`Secret`表单值并使用它来创建`Student`实体实例。 然后将在数据库中更新黑客为 `Secret` 表单字段指定的任意值。 下图显示 fiddler 工具添加`Secret`（具有值"OverPost"） 为已发布的表单值的字段。

    ![](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/_static/image5.png)

    然后值“OverPost”将成功添加到插入行的 `Secret` 属性，尽管你从未打算网页可设置该属性。

    最好是使用`Include`参数与`Bind`归于*允许列表*字段 还有可能要使用`Exclude`参数*阻止列表*你想要排除的字段。 原因`Include`更安全是，当将新属性添加到实体，新字段不会自动受`Exclude`列表。

    可以防止在编辑方案中的过多发布是通过首先从数据库读取实体，然后再调用`TryUpdateModel`，并传递显式允许的属性列表中。 这是在这些教程中使用的方法。

    防止过多发布的许多开发人员的首选的替代方法是使用视图模型而不是实体类与模型绑定。 仅包含想要在视图模型中更新的属性。 完成 MVC 模型绑定器后，将视图模型属性复制到的实体实例，根据需要使用一种工具类似于[AutoMapper](http://automapper.org/)。 使用数据库。要将其状态设置为 Unchanged，然后设置 Property("PropertyName") 的实体实例的项。为 true 的视图模型中包含每个实体属性的 IsModified。 此方法同时适用于编辑和创建方案。

    以外`Bind`属性，`try-catch`块是对基架代码所做的唯一更改。 如果保存更改时捕获到来自 <xref:System.Data.DataException> 的异常，则会显示一般错误消息。 有时 <xref:System.Data.DataException> 异常是由应用程序外部的某些内容而非编程错误引起的，因此建议用户再次尝试。 尽管在本示例中未实现，但生产质量应用程序会记录异常。 有关详细信息，请参阅[监视和遥测（使用 Azure 构建真实世界云应用）](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log)中的“见解记录”部分。

    中的代码*Views\Student\Create.cshtml*类似于您在中看到*Details.cshtml*，只不过`EditorFor`和`ValidationMessageFor`帮助程序用于每个字段而不是`DisplayFor`. 下面是相关的代码：

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample9.cshtml)]

    *Create.cshtml*还包括`@Html.AntiForgeryToken()`，适用于`ValidateAntiForgeryToken`属性中的控制器，以帮助防止[跨站点请求伪造](../../security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages.md)攻击。

    需要在不更改*Create.cshtml*。

2. 通过启动该程序，运行页上选择**学生**选项卡上，，然后单击**创建新**。

3. 输入名称和无效的日期，然后单击**创建**可查看错误消息。

    这是默认情况下获取的服务器端验证。 在更高版本的教程中，您将了解如何添加生成的客户端验证代码的属性。 以下突出显示的代码显示了中的模型验证检查**创建**方法。

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample10.cs?highlight=1)]

4. 将日期更改为有效值，并单击“创建”，查看“索引”页中显示的新学生。

5. 关闭浏览器。

## <a name="update-httppost-edit-method"></a>更新 HttpPost 编辑方法

1. 替换<xref:System.Web.Mvc.HttpPostAttribute>`Edit`用下面的代码的操作方法：

   [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample11.cs)]

   > [!NOTE]
   > 在*Controllers\StudentController.cs*，则`HttpGet Edit`方法 (而不是`HttpPost`属性) 使用`Find`方法来检索所选`Student`实体，为你在中看到`Details`方法。 不需要更改此方法。

   这些更改实现安全的最佳做法，以免[过多发布](#overpost)，基架生成`Bind`属性，并添加到的实体集具有已修改标志将模型绑定器创建的实体。 由于不再推荐代码`Bind`特性将清除在字段中未列出任何预先存在的数据`Include`参数。 将来，MVC 控制器基架将进行更新，以便它不会生成`Bind`Edit 方法的属性。

   新代码读取现有实体并调用<xref:System.Web.Mvc.Controller.TryUpdateModel%2A>更新从已发布的表单数据中的用户输入的字段。 实体框架的自动更改跟踪集[EntityState.Modified](<xref:System.Data.EntityState.Modified>)实体上的标志。 当[SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx)调用方法时，<xref:System.Data.EntityState.Modified>标志会导致实体框架来创建 SQL 语句来更新数据库行。 [并发冲突](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)将被忽略，并更新数据库行中的所有列，包括用户未更改。 (更高版本的教程演示如何处理并发冲突，并且如果只想要在数据库中更新的单个字段，可以将实体设置为[EntityState.Unchanged](<xref:System.Data.EntityState.Unchanged>)并将各个字段设置为[EntityState.Modified](<xref:System.Data.EntityState.Modified>)。)

   若要防止过多发布，你想要通过编辑页面可更新的字段是否在允许列表中的`TryUpdateModel`参数。 目前没有要保护的额外字段，但是列出希望模型绑定器绑定的字段可确保以后将字段添加到数据模型时，它们将自动受到保护，直到明确将其添加到此处为止。

   这些更改会导致 HttpPost Edit 方法的方法签名是与 HttpGet 编辑方法; 相同因此，您已重命名方法 EditPost。

   > [!TIP]
   >
   > **实体状态的附加和 SaveChanges 方法**
   >
   > 数据库上下文跟踪内存中的实体是否与数据库中相应的行同步，并且此信息确定调用 `SaveChanges` 方法时会发生的情况。 例如，当传递到新的实体[外](https://msdn.microsoft.com/library/system.data.entity.dbset.add(v=vs.103).aspx)方法，该实体的状态设置为`Added`。 然后调用[SaveChanges](https://msdn.microsoft.com/library/system.data.entity.dbcontext.savechanges(v=VS.103).aspx)方法，将数据库上下文发出 SQL`INSERT`命令。
   >
   > 实体可能在下列情况之一[状态](xref:System.Data.EntityState):
   >
   > - `Added`。 实体在数据库中尚不存在。 `SaveChanges`方法必须发出`INSERT`语句。
   > - `Unchanged`。 不需要通过 `SaveChanges` 方法对此实体执行操作。 从数据库读取实体时，实体将从此状态开始。
   > - `Modified`。 已修改实体的部分或全部属性值。 `SaveChanges`方法必须发出`UPDATE`语句。
   > - `Deleted`。 已标记该实体进行删除。 `SaveChanges`方法必须发出`DELETE`语句。
   > - `Detached`。 数据库上下文未跟踪该实体。
   >
   > 在桌面应用程序中，通常会自动设置状态更改。 在桌面应用程序的类型中，读取一个实体并对它的一些属性值进行更改。 这将使其实体状态自动更改为 `Modified`。 然后调用`SaveChanges`，Entity Framework 生成 SQL`UPDATE`更新仅更改了的实际属性的语句。
   >
   > Web 应用断开连接的特性不允许此连续序列。 [DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx)读取实体将在页面呈现后进行处理。 当`HttpPost``Edit`调用操作方法，将进行新的请求并具有的新实例[DbContext](https://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx)，因此您必须手动将实体状态设置为`Modified.`然后当您调用`SaveChanges`，Entity Framework 会更新所有列的数据库行，因为上下文无法知道已更改的属性。
   >
   > 如果您希望 SQL`Update`语句来更新字段的用户实际更改，您可以以某种方式 （如隐藏字段） 保存的原始值，以便它们时，将用`HttpPost``Edit`调用方法。 然后可以创建`Student`实体使用的原始值，调用`Attach`方法与该原始版本的实体的实体的值更新为新值，然后调用`SaveChanges.`的详细信息，请参阅[实体状态和 SaveChanges](/ef/ef6/saving/change-tracking/entity-state)并[本地数据](/ef/ef6/querying/local-data)。

   中的 HTML 和 Razor 代码*Views\Student\Edit.cshtml*类似于您在中看到*Create.cshtml*，并不不需要任何更改。

2. 通过启动该程序，运行页上选择**学生**选项卡上，，然后单击**编辑**超链接。

3. 更改某些数据并单击“保存”。 请参阅索引页中更改的数据。

4. 关闭浏览器。

## <a name="update-the-delete-page"></a>更新“删除”页

在中*Controllers\StudentController.cs*的模板代码<xref:System.Web.Mvc.HttpGetAttribute>`Delete`方法使用`Find`方法来检索所选`Student`实体，为你在中看到`Details`和`Edit`方法。 但是，若要在调用 `SaveChanges` 失败时实现自定义错误消息，请将部分功能添加到此方法及其相应的视图中。

正如所看到的更新和创建操作，删除操作需要两个操作方法。 调用以响应 GET 请求的方法显示一个视图，使用户有机会批准或取消删除操作。 如果用户批准，则创建 POST 请求。 在这种情况， `HttpPost` `Delete`调用方法，然后该方法实际执行删除操作。

你将添加`try-catch`阻止<xref:System.Web.Mvc.HttpPostAttribute>`Delete`方法以处理更新数据库时可能出现的任何错误。 如果发生错误， <xref:System.Web.Mvc.HttpPostAttribute> `Delete`方法调用<xref:System.Web.Mvc.HttpGetAttribute>`Delete`方法，并向其传递一个参数，指示发生错误。 <xref:System.Web.Mvc.HttpGetAttribute> `Delete`方法然后重新显示确认页以及错误消息，为用户提供取消或稍后重试的机会。

1. 替换<xref:System.Web.Mvc.HttpGetAttribute>`Delete`用下面的代码，用于管理错误报告的操作方法：

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample12.cs?highlight=1,7-10)]

    此代码接受[可选参数](https://msdn.microsoft.com/library/dd264739.aspx)，该值指示是否在发生故障，以保存更改后调用该方法。 此参数是`false`时`HttpGet``Delete`不上一次失败的情况下调用方法。 调用时`HttpPost``Delete`方法以响应数据库更新错误，该参数是`true`和一条错误消息传递给视图。

2. 替换<xref:System.Web.Mvc.HttpPostAttribute>`Delete`操作方法 (名为`DeleteConfirmed`) 使用以下代码，其执行实际删除操作并捕获任何数据库更新错误。

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample13.cs)]

    此代码检索所选的实体，然后调用[删除](https://msdn.microsoft.com/library/system.data.entity.dbset.remove(v=vs.103).aspx)方法将实体的状态设置为`Deleted`。 当`SaveChanges`调用时，SQL`DELETE`生成命令。 你还将操作方法名称从 `DeleteConfirmed` 更改为了 `Delete`。 基架的代码名为`HttpPost``Delete`方法`DeleteConfirmed`以便`HttpPost`方法唯一的签名。 （CLR 要求重载方法具有不同的方法参数。）签名是唯一的现在可以继续使用 MVC 约定，使用相同的名称`HttpPost`和`HttpGet`删除方法。

    如果在大容量应用程序中提高性能是优先考虑，可以避免不必要的 SQL 查询，若要检索的行替换为调用的代码行`Find`和`Remove`方法使用以下代码：

    [!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample14.cs)]

    此代码实例化`Student`实体使用的主要密钥值然后将实体状态设置为`Deleted`。 这是 Entity Framework 删除实体需要执行的所有操作。

    如所述， `HttpGet` `Delete`方法不会删除数据。 执行删除操作以响应 GET 请求 （或者就此而言，执行的任何编辑操作，创建操作或更改数据的任何其他操作） 会产生安全风险。 有关详细信息，请参阅[ASP.NET MVC 提示 #46 — 不使用删除链接，因为他们创建的安全漏洞](http://stephenwalther.com/blog/archive/2009/01/21/asp.net-mvc-tip-46-ndash-donrsquot-use-delete-links-because.aspx)Stephen Walther 的博客上。

3. 在中*Views\Student\Delete.cshtml*，添加一条错误消息之间`h2`标题和`h3`标题，如下面的示例中所示：

    [!code-cshtml[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample15.cshtml?highlight=2)]

4. 通过启动该程序，运行页上选择**学生**选项卡上，，然后单击**删除**超链接。

5. 选择**删除**上显示的页面**是否确实要删除此？**。

    索引页显示不带已删除学生。 (您将看到的错误处理中的操作中的代码示例[并发教程](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)。)

## <a name="close-database-connections"></a>关闭数据库连接

若要关闭的数据库连接并释放尽可能快地保存它们的资源，请释放上下文实例完成后使用它。 基架的代码提供的就是为什么[Dispose](https://msdn.microsoft.com/library/system.idisposable.dispose(v=vs.110).aspx)方法的末尾`StudentController`类*StudentController.cs*，如以下示例所示：

[!code-csharp[Main](implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application/samples/sample16.cs)]

基`Controller`类已经实现`IDisposable`接口，所以此代码只需将添加到替代`Dispose(bool)`方法来显式释放上下文实例。

## <a name="handle-transactions"></a>处理事务

默认情况下，Entity Framework 隐式实现事务。 在方案中，对多个行或表进行更改，然后调用`SaveChanges`，Entity Framework 自动确保所做的更改的所有成功或全部失败。 如果完成某些更改后发生错误，这些更改会自动回退。 在需要更多控制&mdash;例如，如果你想要包括外部实体框架在事务中完成的操作&mdash;请参阅[使用事务](/ef/ef6/saving/transactions)。

## <a name="get-the-code"></a>获取代码

[下载已完成的项目](https://webpifeed.blob.core.windows.net/webpifeed/Partners/ASP.NET%20MVC%20Application%20Using%20Entity%20Framework%20Code%20First.zip)

## <a name="additional-resources"></a>其他资源

现在有了一整套的执行简单的 CRUD 操作执行的页`Student`实体。 MVC 帮助程序用于生成数据字段的 UI 元素。 MVC 帮助程序的详细信息，请参阅[呈现窗体使用 HTML 帮助程序](/previous-versions/aspnet/dd410596(v=vs.98))（文章是的 MVC 3 但仍适用于 MVC 5)。

EF 6 中的其他资源的链接可在[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建详细信息页
> * 更新创建页
> * 更新的 HttpPost 编辑方法
> * 更新删除页
> * 关闭的数据库连接
> * 处理的事务

转到下一步的文章，了解如何添加排序、 筛选和分页到项目。
> [!div class="nextstepaction"]
> [排序、筛选和分页](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md)
