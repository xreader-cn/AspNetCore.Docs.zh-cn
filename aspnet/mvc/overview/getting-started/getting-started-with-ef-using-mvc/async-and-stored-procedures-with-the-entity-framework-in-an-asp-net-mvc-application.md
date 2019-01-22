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
# <a name="tutorial-use-async-and-stored-procedures-with-ef-in-an-aspnet-mvc-app"></a>教程：使用 async 和 EF 在 ASP.NET MVC 应用中使用存储的过程

在之前的教程中，您学习了如何读取和更新使用同步编程模型的数据。 在本教程中，请参阅如何实现异步编程模型。 异步代码可以帮助更好地执行，因为这样可以更好地利用服务器资源的应用程序。

在本教程还了解如何将存储的过程用于插入、 更新和删除实体上的操作。

最后，您重新部署到 Azure，以及它实现了自第一次部署后的数据库更改的所有应用程序。

下图是一些将会用到的页面。

![部门页](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)

![创建部门](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

在本教程中，你将了解：

> [!div class="checklist"]
> * 了解异步代码
> * 创建一个部门控制器
> * 使用存储的过程
> * 部署到 Azure

## <a name="prerequisites"></a>系统必备

* [更新相关数据](updating-related-data-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="why-use-asynchronous-code"></a>为什么要使用异步代码

Web 服务器的可用线程是有限的，而在高负载情况下的可能所有线程都被占用。 当发生这种情况的时候，服务器就无法处理新请求，直到线程被释放。 使用同步代码时，可能会出现多个线程被占用但不能执行任何操作的情况，因为它们正在等待 I/O 完成。 使用异步代码时，当进程正在等待 I/O 完成，服务器可以将其线程释放用于处理其他请求。 因此，异步代码可以使服务器资源能够更有效地使用和服务器能够处理更多流量不会延迟。

在早期版本的.NET 中，编写和测试异步代码较为复杂，容易出错且难进行调试。 在.NET 4.5 中，编写、 测试和调试异步代码是，您应除非有理由不这么地通常编写异步代码变得如此简单。 异步代码会引入少量开销，但流量较低性能影响可以忽略不计，同时针对高流量情况，潜在的性能改善非常显著。

有关异步编程的详细信息，请参阅[使用.NET 4.5 的异步支持以避免阻塞调用](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices.md#async)。

## <a name="create-department-controller"></a>创建部门控制器

创建采用相同的方式更早版本的控制器，只不过这次选择一个部门控制器**使用异步控制器操作**复选框。

以下突出显示出已添加到的同步代码`Index`方法，使其异步：

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs?highlight=1,4)]

四个更改已应用以启用要以异步方式执行的 Entity Framework 数据库查询：

- 方法将标有`async`关键字，它会告知编译器方法正文的各部分生成回调并自动创建`Task<ActionResult>`返回对象。
- 返回类型已从`ActionResult`到`Task<ActionResult>`。 `Task<T>`类型表示正在进行的工作类型的结果`T`。
- `await`关键字已应用于 web 服务调用。 当编译器发现此关键字时，在后台它将拆分该方法为两个部分。 第一个部分结尾以异步方式启动该操作。 第二部分放入操作完成时调用的回调方法。
- 异步版本`ToList`调用扩展方法。

为什么是`departments.ToList`语句但不是修改`departments = db.Departments`语句？ 原因是以异步方式执行导致查询或命令被发送到数据库的语句。 `departments = db.Departments`语句设置了一个查询，但不是执行查询之前`ToList`调用方法。 因此，仅`ToList`以异步方式执行方法。

在中`Details`方法和`HttpGet``Edit`并`Delete`方法，`Find`方法是，则查询发送到数据库，因此这就是以异步方式执行的方法：

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs?highlight=7)]

在`Create`， `HttpPost Edit`，并`DeleteConfirmed`方法，它是`SaveChanges`会导致要执行的命令的方法调用、 不等的语句`db.Departments.Add(department)`这只导致要修改的内存中的实体。

[!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=6)]

打开*Views\Department\Index.cshtml*，并使用以下代码替换模板代码：

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cshtml?highlight=3,5,20-22,36-38)]

此代码更改标题从索引到部门，将管理员名称移动到右侧，并提供管理员的完整名称。

在创建、 删除、 详细信息，并编辑视图，更改的标题`InstructorID`字段为"Administrator"相同的方式 Course 视图中的院系名称字段更改为"部门"。

在创建和编辑视图使用以下代码：

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cshtml)]

在删除和详细信息视图中使用以下代码：

[!code-cshtml[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cshtml)]

运行该应用程序，并单击**部门**选项卡。

一切都与其他控制器中的相同，但在此控制器中的所有 SQL 查询都异步执行。

需要注意当与实体框架配合使用异步编程的一些事项：

- 异步代码不是线程安全。 换而言之，换而言之，请勿尝试执行并行使用的同一上下文实例的多个操作。
- 如果你想要利用异步代码的性能优势，请确保你所使用的任何库和包在它们调用导致 Entity Framework 数据库查询方法时也使用异步。

## <a name="use-stored-procedures"></a>使用存储的过程

一些开发人员和 Dba 更愿意将存储的过程用于数据库访问权限。 在早期版本的实体框架中，您可以检索数据使用存储的过程来[执行原始 SQL 查询](advanced-entity-framework-scenarios-for-an-mvc-web-application.md)，但您不能指示 EF 以使用存储的过程以执行更新操作。 EF 6 中很容易配置 Code First 以使用存储的过程。

1. 在中*DAL\SchoolContext.cs*，将突出显示的代码添加到`OnModelCreating`方法。

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs?highlight=9)]

    此代码指示实体框架使用存储过程的插入、 更新和删除操作上`Department`实体。
2. 在包管理控制台中，输入以下命令：

    `add-migration DepartmentSP`

    打开*迁移\&l t; 时间戳&gt;\_DepartmentSP.cs*若要查看中的代码`Up`方法，可创建插入、 更新和删除存储过程：

    [!code-csharp[Main](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs?highlight=3-4,26-27,42-43)]
3. 在包管理控制台中，输入以下命令：

     `update-database`
4. 在调试模式下运行应用程序中，单击**部门**选项卡，然后依次**创建新**。
5. 为新的院系中，输入数据，然后单击**创建**。

6. 在 Visual Studio 中查看的日志**输出**窗口以查看存储的过程用于插入新 Department 行。

     ![部门插入 SP](async-and-stored-procedures-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image6.png)

代码首先创建默认存储过程名称。 如果使用现有数据库，您可能需要自定义以便使用已在数据库中定义的存储的过程的存储的过程名称。 有关如何执行该操作的信息，请参阅[实体框架代码第一个 Insert/Update/Delete 存储过程](https://msdn.microsoft.com/data/dn468673)。

如果你想要自定义哪些生成存储的过程执行操作，则可以编辑迁移的基架的代码`Up`创建存储的过程的方法。 通过这种方式所做的更改将反映每当迁移运行时和迁移后部署在生产中自动运行时将应用于生产数据库。

如果你想要更改现有的存储的过程在先前的迁移中创建，可添加迁移命令用于生成空白迁移时，然后手动编写代码以调用[AlterStoredProcedure](https://msdn.microsoft.com/library/system.data.entity.migrations.dbmigration.alterstoredprocedure.aspx)方法.

## <a name="deploy-to-azure"></a>部署到 Azure

该部分要求你已完成的可选**将其部署到 Azure**主题中[迁移和部署](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md)本系列教程。 如果必须通过删除在本地项目中的数据库解决的迁移错误，跳过此部分。

1. 在 Visual Studio 中，右键单击该项目中的**解决方案资源管理器**，然后选择**发布**从上下文菜单。
2. 单击“发布” 。

    Visual Studio 将部署到 Azure，应用程序和应用程序打开在默认浏览器中，在 Azure 中运行。
3. 测试应用程序以验证它是否正常工作。

    首次运行页面访问数据库，Entity Framework 将运行所有迁移`Up`使数据库保持最新的当前数据模型所必需的方法。 现在可以使用的所有自上次部署，包括在本教程中添加的部门页添加的 web 页面。

## <a name="get-the-code"></a>获取代码

[下载已完成的项目](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>其他资源

其他实体框架资源的链接可在[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 介绍了异步代码
> * 创建一个部门控制器
> * 使用存储的过程
> * 部署到 Azure

转到下一步的文章，了解如何在多个用户在同一时间更新同一实体时处理冲突。
> [!div class="nextstepaction"]
> [处理并发](handling-concurrency-with-the-entity-framework-in-an-asp-net-mvc-application.md)
