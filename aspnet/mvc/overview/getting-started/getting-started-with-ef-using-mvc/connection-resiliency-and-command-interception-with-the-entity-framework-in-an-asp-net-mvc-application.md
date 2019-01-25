---
uid: mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
title: 教程：与 EF 在 ASP.NET MVC 应用中使用连接复原和命令拦截
author: tdykstra
description: 在本教程介绍如何使用连接复原和命令拦截。 它们是两个重要的 Entity Framework 6 的功能。
ms.author: riande
ms.date: 01/22/2019
ms.topic: tutorial
ms.assetid: c89d809f-6c65-4425-a3fa-c9f6e8ac89f2
msc.legacyurl: /mvc/overview/getting-started/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application
msc.type: authoredcontent
ms.openlocfilehash: 4ef13e676340f1bfac1a25219e6455c89b69df5e
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837619"
---
# <a name="tutorial-use-connection-resiliency-and-command-interception-with-entity-framework-in-an-aspnet-mvc-app"></a>教程：与实体框架在 ASP.NET MVC 应用中使用连接复原和命令拦截

到目前为止该应用程序已经运行本地 IIS Express 中在开发计算机上。 若要使实际的应用程序可供其他人通过 Internet 使用，必须将其部署到 web 宿主提供程序，并需要将数据库部署到数据库服务器。

在本教程介绍如何使用连接复原和命令拦截。 它们是两个重要功能 Entity Framework 6 部署到云环境时的特别有用： 连接复原 （自动重试的暂时性错误） 和命令截获 (catch 所有 SQL 查询都发送到数据库若要记录或更改它们。）

此连接复原和命令拦截教程是可选的。 如果跳过本教程中，需要在后续教程中进行一些细微调整。

在本教程中，你将了解：

> [!div class="checklist"]
> * 启用连接复原
> * 启用命令截获
> * 测试新的配置

## <a name="prerequisites"></a>系统必备

* [排序、筛选和分页](sorting-filtering-and-paging-with-the-entity-framework-in-an-asp-net-mvc-application.md)

## <a name="enable-connection-resiliency"></a>启用连接复原

在部署到 Windows Azure 应用程序时，你将部署到 Windows Azure SQL 数据库、 云数据库服务数据库。 连接到比你的 web 服务器和数据库服务器直接连接时一起位于同一数据中心的云数据库服务时，暂时性连接错误是通常更频繁。 即使在同一数据中心托管的云 web 服务器和云数据库服务，有更多的网络连接，它们可以出现问题，例如负载均衡器之间。

此外云服务是通常由其他用户共享，这意味着其响应能力可能受到它们。 并且您对数据库访问权限可能会受到限制。 当您尝试超过了允许在服务级别协议 (SLA) 中经常访问更多限制意味着数据库服务会引发异常。

许多或大多数连接问题时正在访问云服务是暂时的也就是说，他们自身解决在短时间的时间。 因此在重试数据库操作并获得一种类型的通常是暂时的错误，稍等片刻，该操作可能会成功后无法重试该操作。 如果通过自动尝试再次，处理暂时性错误，可以为用户提供更好的体验使其中的大多数客户不可见。 连接复原功能在 Entity Framework 6 中的自动执行的重试过程失败的 SQL 查询。

针对特定数据库服务，必须适当地配置连接复原功能：

- 它必须知道哪些异常可能是暂时的。 你想要重试错误引起暂时失去网络连接中不由程序 bug，例如引起的错误。
- 它必须等待适当的重试的失败的操作之间的时间。 你可以等待更长时间进行批处理的重试之间不是可以在线网页用户正在等待响应。
- 它具有重试的次数之前放弃适当数量。 您可能想要重试更多次，就像在一个在线应用程序中的批处理中。

可以配置手动为任何数据库环境中支持实体框架提供程序，这些设置，但通常适用于使用 Windows Azure SQL 数据库的联机应用程序的默认值已为你配置和这些是你将实现为 Contoso 大学应用程序的设置。

只需启用连接复原能力是中派生自程序集创建一个类[DbConfiguration](https://msdn.microsoft.com/data/jj680699.aspx)类，并在该类中设置 SQL 数据库*执行策略*，即 EF 中另一种说法*重试策略*。

1. 在 DAL 文件夹中，添加名为的类文件*SchoolConfiguration.cs*。
2. 将模板代码替换为以下代码：

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample1.cs)]

    实体框架将自动运行的代码在派生类中找到`DbConfiguration`。 可以使用`DbConfiguration`类，以执行在中否则所执行的操作的代码中的配置任务*Web.config*文件。 有关详细信息，请参阅[EntityFramework 基于代码的配置](https://msdn.microsoft.com/data/jj680699)。
3. 在中*StudentController.cs*，添加`using`语句`System.Data.Entity.Infrastructure`。

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample2.cs)]
4. 将所有更改`catch`阻止该 catch`DataException`异常，以便它们捕获`RetryLimitExceededException`异常相反。 例如：

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample3.cs?highlight=1)]

    已使用`DataException`来尝试标识可能是为了提供友好的"重试"消息暂时性的错误。 但现在，你已启用了重试策略，可能是暂时性的唯一错误将已尝试并失败几次，返回的实际异常将包装在`RetryLimitExceededException`异常。

有关详细信息，请参阅[实体框架连接复原 / 重试逻辑](https://msdn.microsoft.com/data/dn456835)。

## <a name="enable-command-interception"></a>启用命令截获

现在，你已启用了重试策略，如何你测试以验证它是否按预期工作？ 不可以十分轻松地强制暂时性错误发生，尤其是当您在本地，运行和会特别难以将实际的暂时性错误集成到自动化的单元测试。 若要测试连接复原功能，您需要一种方法截获 Entity Framework 将发送到 SQL Server 的查询和 SQL Server 响应替换通常是暂时异常类型。

此外可以使用查询拦截，为了实现云应用程序的最佳做法：[记录的延迟和成功或失败的所有调用的外部服务](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log)如数据库服务。 EF6 提供[专用日志记录 API](https://msdn.microsoft.com/data/dn469464) ，可以使其更轻松地执行日志记录，但在本教程的本部分中，您将学习如何使用实体框架[拦截功能](https://msdn.microsoft.com/data/dn469464)直接，均适用日志记录和用于模拟暂时性错误。

### <a name="create-a-logging-interface-and-class"></a>创建日志记录接口和类

一个[最佳做法进行日志记录](../../../../aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry.md#log)操作和使用接口而不是硬编码调用 System.Diagnostics.Trace 或日志记录类。 这样，更轻松地更改更高版本的日志记录机制，如果您需要执行此操作。 因此在本部分中，您将创建日志记录接口和类来实现它。 / p >

1. 在项目中创建一个文件夹并将其命名*日志记录*。
2. 在中*日志记录*文件夹中，创建名为的类文件*ILogger.cs*，并使用以下代码替换模板代码：

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample4.cs)]

    该接口提供三种跟踪级别，以指示日志的相对重要性，另一个设计为提供对等数据库查询的外部服务调用的延迟信息。 日志记录方法具有重载，可传入异常。 这是这样实现的接口，而不是依赖于整个应用程序每个日志记录方法调用中完成这些操作后的类可靠地记录异常信息包括堆栈跟踪和内部异常信息。

    TraceApi 方法，可以跟踪 SQL 数据库等外部服务每次调用的延迟。
3. 在中*日志记录*文件夹中，创建名为的类文件*Logger.cs*，并使用以下代码替换模板代码：

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample5.cs)]

    该实现使用 System.Diagnostics，执行跟踪。 这是.NET 可以轻松生成和使用跟踪信息的内置功能。 有许多"侦听器"您可以使用与 System.Diagnostics 跟踪将日志写入文件，例如，或将其写入到 blob 存储在 Azure 中。 了解的一些选项，以及指向其他资源的详细信息，在[Visual Studio 中进行故障排除 Azure Web Sites](https://docs.microsoft.com/azure/app-service-web/web-sites-dotnet-troubleshoot-visual-studio)。 您将只能看到在本教程在 Visual Studio 中的日志**输出**窗口。

    在生产应用程序可能需要考虑以外 System.Diagnostics，跟踪包和 ILogger 接口可以相对轻松，若要切换到不同的跟踪机制，如果你决定要执行此操作。

### <a name="create-interceptor-classes"></a>创建侦听器类

接下来将创建每次它会将查询发送到数据库，另一个来模拟暂时性错误进行日志记录时，实体框架会调用到的类。 这些侦听器类必须派生自`DbCommandInterceptor`类。 在其中编写查询时要在执行时自动调用的方法重写。 在这些方法可以检查或记录查询正被发送到数据库，并可以更改查询发送到数据库之前，也可以返回某些内容到实体框架自己而无需甚至将查询传递到数据库。

1. 若要创建将记录发送到数据库的每个 SQL 查询侦听器类，创建名为的类文件*SchoolInterceptorLogging.cs*中*DAL*文件夹，并替换模板代码使用以下代码：

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample6.cs)]

    对于成功的查询或命令，此代码将写入延迟信息的信息的日志。 有关例外情况，它创建错误日志。
2. 若要创建在输入"引发"时，将生成虚拟的暂时性错误的侦听器类**搜索**框中，创建名为的类文件*SchoolInterceptorTransientErrors.cs*中*DAL*文件夹，并将下面的代码模板代码替换为：

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample7.cs)]

    此代码仅重写`ReaderExecuting`的查询，可以返回多行数据调用的方法。 如果你想要检查连接复原对于其他类型的查询，还可以重写`NonQueryExecuting`和`ScalarExecuting`方法，作为日志记录侦听器执行。

    当运行学生页上，并输入"引发"作为搜索字符串时，此代码将创建虚拟 SQL 数据库异常的错误号 20，确定是通常暂时性的类型。 其他当前被识别为暂时性的错误编号均为 64、 233、 10053、 10054、 10060、 10928、 10929、 40197、 40501 和 40613，但它们是将在新版本的 SQL 数据库中进行。

    该代码对实体框架，而不是运行查询并传递后的查询结果中返回的异常。 暂时性异常返回四次，之后代码恢复为将查询传递给数据库的正常过程。

    记录所有内容，因为您可以查看实体框架会尝试执行查询四次之后才最后成功，并在应用程序中唯一的区别是，花费更长时间才能呈现的页面包含查询结果。

    实体框架将重试次数是可配置;该代码指定四次，因为这是 SQL 数据库执行策略的默认值。 如果更改执行策略，则必须也更改的代码，指定生成暂时性错误的次数。 您也可以更改代码以生成更多的异常，因此，实体框架将引发`RetryLimitExceededException`异常。

    在搜索框中输入的值将用`command.Parameters[0]`和`command.Parameters[1]`（一个用于第一个名称，一个用于最后一个名称）。 当找到值"%throw%"时，"引发"将替换为这些参数在"an"，因此将找到一些学生，并将其返回。

    这是只需测试根据不断变化的应用程序 UI 的一些输入的连接复原能力的简便方法。 此外可以编写生成的所有查询或更新，暂时性错误的代码如下所述的评论的更高版本中有关*DbInterception.Add*方法。
3. 在中*Global.asax*，添加以下`using`语句：

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample8.cs)]
4. 添加突出显示的行`Application_Start`方法：

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample9.cs?highlight=7-8)]

    以下几行代码是哪些因素会导致在 Entity Framework 将查询发送到数据库时要运行的侦听器代码。 请注意，因为创建单独的侦听器类进行模拟暂时性错误和日志记录，你可以单独启用和禁用它们。

    您可以添加使用侦听器`DbInterception.Add`你的代码; 中的任意位置的方法其实不一定要在`Application_Start`方法。 另一种方法是将此代码放入你前面创建配置执行策略的 DbConfiguration 类。

    [!code-csharp[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample10.cs?highlight=6-7)]

    只要您将此代码中，请注意不要执行`DbInterception.Add`相同的侦听器的时间超过一次，或者你将获得其他侦听器实例。 例如，如果两次添加日志记录的侦听器，将看到两个日志对于每个 SQL 查询。

    侦听器执行以注册顺序 (依据的顺序`DbInterception.Add`调用方法)。 顺序可能重要具体取决于你在侦听器中所执行的操作。 例如，侦听器可能会更改 SQL 命令可获取中`CommandText`属性。 如果它确实发生更改的 SQL 命令下, 一步侦听器将获取已更改的 SQL 命令，而不是原始的 SQL 命令。

    您已编写的暂时性错误模拟代码可以通过在 UI 中输入一个不同的值导致暂时性错误的方式。 或者，可以编写以始终生成暂时性异常的序列，而检查特定的参数值的侦听器代码。 然后，仅当你想要生成暂时性错误时，可以添加侦听器。 如果这样做，但是，数据库初始化完成后不添加到侦听器。 换而言之，执行如查询一个实体集上的至少一个数据库操作，然后开始生成暂时性错误。 实体框架在数据库初始化期间执行多个查询和它们在事务中，因此在初始化期间的错误可能会导致上下文才能进入不一致的状态不会执行。

## <a name="test-the-new-configuration"></a>测试新的配置

1. 按**F5**若要在调试模式下运行应用程序，然后单击**学生**选项卡。
2. 查看 Visual Studio**输出**窗口以查看跟踪输出。 您可能需要向上滚动过去的某些 JavaScript 错误以获取到记录器所写入的日志。

    请注意，您可以看到实际的 SQL 查询发送到数据库。 您看到的一些初始查询和实体框架执行的操作以开始，请检查数据库版本的命令和迁移历史记录表 （你将了解如何迁移下一个教程中）。 和查看分页，以找出多少名学生的查询，最后您会看到获取学生数据的查询。

    ![常规查询日志记录](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image1.png)
3. 在中**学生**页上，输入"引发"作为搜索字符串，然后单击**搜索**。

    ![引发搜索字符串](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image2.png)

    您会注意到浏览器看起来时实体框架重试查询多次挂起数秒钟的时间。 第一个重试速度非常快，然后发生之前增加每个额外的重试之前等待。 此过程的调用每次重试之前再等待*指数退避算法*。

    当页面显示时，显示学生都有"的"在其名称中，查看输出窗口中，并你将看到相同的查询已尝试五次，第一次四次返回暂时性异常。 对于每个暂时性错误，你将看到您在生成中的暂时性错误时编写的日志`SchoolInterceptorTransientErrors`类 （"Returning 暂时性错误命令..."），将会看到写入时的日志`SchoolInterceptorLogging`获取的异常。

    ![日志记录输出显示了重试次数](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image3.png)

    因为您输入的搜索字符串，返回学生数据的查询已参数化：

    [!code-sql[Main](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/samples/sample11.sql)]

    不日志记录的值的参数，但您可以这样做。 如果想要查看参数值，则可以编写日志记录代码获取参数值从`Parameters`属性的`DbCommand`侦听器方法中获取的对象。

    请注意，不能重复此测试，除非停止应用程序并重新启动它。 如果你想要能够测试应用程序的单次运行中的连接复原多次，您可以编写代码来重置中的错误计数器`SchoolInterceptorTransientErrors`。
4. 若要查看不同之处执行策略 （重试策略） 发出，注释`SetExecutionStrategy`行中*SchoolConfiguration.cs*，同样，在调试模式下运行学生页上，并再次搜索"Throw"。

    这一次调试器停止上第一个生成的异常后尝试执行第一次的查询，将立即。

    ![虚拟异常](connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application/_static/image4.png)
5. 取消注释*SetExecutionStrategy*行中*SchoolConfiguration.cs*。

## <a name="get-the-code"></a>获取代码

[下载已完成的项目](http://code.msdn.microsoft.com/ASPNET-MVC-Application-b01a9fe8)

## <a name="additional-resources"></a>其他资源

其他实体框架资源的链接可在[ASP.NET 数据访问-推荐的资源](../../../../whitepapers/aspnet-data-access-content-map.md)。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 已启用的连接复原
> * 已启用的命令截获
> * 测试新的配置

转到下一步的文章，了解有关 Code First 迁移和 Azure 部署信息。
> [!div class="nextstepaction"]
> [Code First 迁移和 Azure 部署](migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application.md)
