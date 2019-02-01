---
uid: signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
title: 教程：服务器广播 SignalR 2 |Microsoft Docs
author: tdykstra
description: 本教程演示如何创建使用 ASP.NET SignalR 2 来提供服务器广播的功能的 web 应用程序。
ms.author: bradyg
ms.date: 01/02/2019
ms.topic: tutorial
ms.assetid: 1568247f-60b5-4eca-96e0-e661fbb2b273
msc.legacyurl: /signalr/overview/getting-started/tutorial-server-broadcast-with-signalr
msc.type: authoredcontent
ms.openlocfilehash: a243c78c7d552f1c82a88c6083871fcd16538618
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837424"
---
# <a name="tutorial-server-broadcast-with-signalr-2"></a>教程：使用 SignalR 2 广播的服务器

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

本教程演示如何创建使用 ASP.NET SignalR 2 来提供服务器广播的功能的 web 应用程序。 服务器广播意味着在服务器开始发送到客户端的通信。

你将在本教程中创建的应用程序模拟股票行情服务器广播功能的典型方案。 我们会定期服务器随机更新股票价格，广播到所有连接的客户端的更新。 在浏览器、 数字和中的符号 **更改** 并 **%** 列动态更改以响应来自服务器的通知。 如果您打开其他浏览器相同的 URL，它们都同时显示相同的数据和对数据的相同更改。

![创建 web](tutorial-server-broadcast-with-signalr/_static/image1.png)

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建项目
> * 设置服务器代码
> * 检查服务器代码
> * 设置客户端代码
> * 检查客户端代码
> * 测试应用程序
> * 启用日志记录

> [!IMPORTANT]
> 如果不想要逐步完成构建应用程序的步骤，你可以在新的空的 ASP.NET Web 应用程序项目中安装 SignalR.Sample 包。 如果不执行本教程中的步骤安装 NuGet 包，必须按照中的说明*readme.txt*文件。 若要运行包需要添加 OWIN 启动类的调用`ConfigureSignalR`中已安装的程序包的方法。 如果不添加 OWIN 启动类，将收到错误。 请参阅[安装 StockTicker 示例](#install-the-stockticker-sample)本文的部分。


## <a name="prerequisites"></a>系统必备

 * [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)与**ASP.NET 和 web 开发**工作负荷。

## <a name="create-the-project"></a>创建项目

本部分演示如何使用 Visual Studio 2017 创建空的 ASP.NET Web 应用程序。

1. 在 Visual Studio 中创建 ASP.NET Web 应用程序。

    ![创建 web](tutorial-server-broadcast-with-signalr/_static/image2.png)

1. 在中**新 ASP.NET Web 应用程序-SignalR.StockTicker**窗口中，保留**空**，并选择**确定**。

## <a name="set-up-the-server-code"></a>设置服务器代码

在本部分中，您将设置在服务器运行的代码。

### <a name="create-the-stock-class"></a>创建 Stock 类

首先创建*股票*模型将用于存储和传输有关股票的信息的类。

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **类**。

1. 将类命名*股票*并将其添加到项目。

1. 中的代码替换*Stock.cs*文件使用以下代码：

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample1.cs)]

    在创建股票时将设置的两个属性是`Symbol`(例如，Microsoft 的 MSFT) 和`Price`。 其他属性取决于如何以及何时将`Price`。 首次设置`Price`，值获取会传播到`DayOpen`。 在此之后，当您将设置`Price`，该应用程序计算`Change`和`PercentChange`属性值基于之间的差异`Price`和`DayOpen`。

### <a name="create-the-stocktickerhub-and-stockticker-classes"></a>创建的 StockTickerHub 和 StockTicker 类

SignalR 中心 API 将用于处理服务器到客户端交互。 一个`StockTickerHub`派生的类`SignalRHub`类将处理来自客户端接收连接和方法调用。 您还需要维护股票数据并运行`Timer`对象。 `Timer`对象将定期触发价格更新独立于客户端连接。 无法将这些函数放入`Hub`类，因为中心是暂时的。 应用创建`Hub`集线器，例如连接和从客户端对服务器的调用上每个任务的类实例。 因此，可保护库存数据，更新价格，并会广播价格更新机制必须运行在单独的类。 将类命名`StockTicker`。

![从 StockTicker 的广播](tutorial-server-broadcast-with-signalr/_static/image3.png)

您只想的一个实例`StockTicker`类来运行的服务器上，因此将需要设置从每个引用`StockTickerHub`到单一实例`StockTicker`实例。 `StockTicker`类具有广播到客户端，因为它具有股票数据，并触发更新，但`StockTicker`不是`Hub`类。 `StockTicker`类必须获取对 SignalR Hub 连接上下文对象的引用。 然后，它可以使用 SignalR 连接上下文对象广播到客户端。

#### <a name="create-stocktickerhubcs"></a>Create StockTickerHub.cs

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。

1. 在中**添加新项-SignalR.StockTicker**，选择**已安装** > **Visual C#**   >  **Web** >  **SignalR** ，然后选择**SignalR Hub 类 (v2)**。

1. 将类命名*StockTickerHub*并将其添加到项目。

    此步骤将创建*StockTickerHub.cs*类文件。 同时，它会添加到项目支持 SignalR 的脚本文件和程序集引用一组。

1. 中的代码替换*StockTickerHub.cs*文件使用以下代码：

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample2.cs)]

1. 保存该文件。

此应用使用[中心](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx)类来定义客户端可以调用在服务器的方法。 要定义一种方法： `GetAllStocks()`。 当客户端最初连接到服务器时，它将调用此方法以获取所有其当前价格股票的列表。 该方法可以同步运行并返回`IEnumerable<Stock>`由于它会从内存中返回数据。

如果该方法必须通过执行将涉及等待，如数据库查找或 web 服务调用，获取的数据则会指定`Task<IEnumerable<Stock>>`作为返回值以启用异步处理。 有关详细信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-时要异步执行](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods)。

`HubName`属性指定应用程序如何将引用在客户端上的 JavaScript 代码中的中心。 在客户端上的默认名称如果不使用此属性是类名称，在这种情况下将驼峰式大小写版本`stockTickerHub`。

正如您看到更高版本创建时`StockTicker`类，应用创建该类的单一实例中它的静态`Instance`属性。 该单一实例`StockTicker`无论多少个客户端连接或断开连接的内存中。 该实例是什么`GetAllStocks()`方法用于返回当前股票信息。

#### <a name="create-stocktickercs"></a>创建 StockTicker.cs

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **类**。

1. 将类命名*StockTicker*并将其添加到项目。

1. 中的代码替换*StockTicker.cs*文件使用以下代码：

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample3.cs)]

由于所有线程都运行 StockTicker 代码的同一个实例，StockTicker 类必须是线程安全的。

### <a name="examine-the-server-code"></a>检查服务器代码

如果您检查服务器代码，它将帮助你了解应用程序的工作原理。

#### <a name="storing-the-singleton-instance-in-a-static-field"></a>在静态字段中存储的单一实例

此代码初始化静态`_instance`支持字段`Instance`与类的实例的属性。 构造函数是私有的因为它是应用程序可以创建类的唯一实例。 此应用使用[迟缓初始化](/dotnet/framework/performance/lazy-initialization)为`_instance`字段。 它不是为了提高性能。 它是为了确保创建的实例是线程安全。

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample4.cs)]

客户端连接到服务器，每次在一个单独的线程中运行的 StockTickerHub 类的新实例获取从 StockTicker 单一实例`StockTicker.Instance`静态属性，作为您了解到之前在`StockTickerHub`类。

#### <a name="storing-stock-data-in-a-concurrentdictionary"></a>在 ConcurrentDictionary 中存储常用的数据

构造函数初始化`_stocks`具有一些示例股票数据集合和`GetAllStocks`返回股票。 如所见，返回此集合的股票`StockTickerHub.GetAllStocks`，这是中的服务器方法`Hub`客户端可以调用的类。

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample5.cs)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample6.cs)]

股票集合指[ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx)线程安全的类型。 作为替代方法，您可以使用[字典](https://msdn.microsoft.com/library/xfhwa508.aspx)对象，并显式锁定字典时对其进行更改。

对于此示例应用程序，它是确定将应用程序数据存储在内存中并释放应用程序时，会丢失数据`StockTicker`实例。 在实际的应用程序，您将如何使用后端数据存储，如数据库。

#### <a name="periodically-updating-stock-prices"></a>定期更新股票价格

构造函数启动`Timer`定期调用的方法，更新基于随机的股票价格的对象。

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample7.cs)]

 `Timer` 调用`UpdateStockPrices`，空值参数中的阶段。 在更新之前的价格，该应用上采用锁`_updateStockPricesLock`对象。 如果另一个线程已更新的价格，，然后调用代码将检查`TryUpdateStockPrice`上列表中每个股票。 `TryUpdateStockPrice`方法决定是否要更改的股票价格和要对其进行更改。 如果股票价格发生更改，该应用程序调用`BroadcastStockPrice`广播到所有的股票价格更改连接的客户端。

`_updatingStockPrices`指定的标志[易失性](https://msdn.microsoft.com/library/x13ttww7.aspx)以确保它是线程安全。

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample8.cs)]

在实际的应用程序，`TryUpdateStockPrice`方法将调用 web 服务，以便查找价格。 在此代码中，该应用使用的随机数生成器随机进行更改。

#### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a>获取 SignalR 上下文，以便 StockTicker 类可以广播到客户端

因为价格更改此处源自`StockTicker`对象，它是对象，需要调用`updateStockPrice`所有连接的客户端上的方法。 在中`Hub`类，您用于调用客户端的方法，有一个 API，但`StockTicker`也不是派生`Hub`类，并不提供任何参考`Hub`对象。 用于向已连接客户端广播`StockTicker`类具有要获取有关 SignalR 上下文实例`StockTickerHub`类，并使用它来在客户端上调用方法。

该代码获取对 SignalR 上下文的引用时创建的单一类实例，将引用传递给构造函数中，并构造函数将其放入`Clients`属性。

有两个原因你想要一次只能获取上下文的原因： 获取上下文是代价高昂的任务，并可确保应用程序保留的消息发送到客户端的预期的顺序后获取它。

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample9.cs)]

获取`Clients`属性的上下文并将其放入`StockTickerClient`属性，可以编写代码来调用方法的客户端中一样看上去相同`Hub`类。 例如，若要广播到所有客户端可以编写`Clients.All.updateStockPrice(stock)`。

`updateStockPrice`方法中调用`BroadcastStockPrice`尚不存在。 在编写客户端上运行的代码时，你将从更高版本添加它。 您可以参考`updateStockPrice`此处因为`Clients.All`是动态的这意味着应用程序将计算在运行时表达式的值。 当此方法调用执行时，SignalR 会方法名称和参数值向发送客户端，并在客户端有一个名为方法`updateStockPrice`，应用程序将调用该方法并向其传递参数值。

`Clients.All` 表示将发送到所有客户端。 SignalR 提供其他选项来指定哪些客户端或客户端将发送到的组。 有关详细信息，请参阅[HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx)。

### <a name="register-the-signalr-route"></a>注册 SignalR 路由

服务器需要知道要截获并将定向到 SignalR 的 URL。 若要执行此操作，添加 OWIN 启动类：

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。

1. 在中**添加新项-SignalR.StockTicker**选择**已安装** > **Visual C#**   >  **Web**和然后选择**OWIN 启动类**。

1. 将类命名*启动*，然后选择**确定**。

1. 中的默认代码替换*Startup.cs*文件使用以下代码：

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample10.cs)]

现在，你已设置的服务器代码。 在下一步部分中，将设置客户端。

## <a name="set-up-the-client-code"></a>设置客户端代码

在本部分中，您将设置在客户端运行的代码。

### <a name="create-the-html-page-and-javascript-file"></a>创建 HTML 页和 JavaScript 文件

HTML 页将显示的数据和 JavaScript 文件将组织的数据。

#### <a name="create-stocktickerhtml"></a>创建 StockTicker.html

首先，你将添加 HTML 客户端。

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **HTML 页**。

1. 将文件命名*StockTicker* ，然后选择**确定**。

1. 中的默认代码替换*StockTicker.html*文件使用以下代码：

    [!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample11.html?highlight=40-43)]

    HTML 5 列、 一个标题行，与具有一个单元格跨越所有五个列的数据行创建一个表。 数据行显示了"正在加载..."暂时不可用时启动该应用程序。 JavaScript 代码将删除该行，并添加其位置的行中使用从服务器中检索的股票数据。

    指定脚本标记：

    * JQuery 脚本文件。

    * SignalR 核心脚本文件。

    * SignalR 代理脚本文件。

    * 更高版本，您将创建一个 StockTicker 脚本文件。

    应用动态生成 SignalR 代理脚本文件。 它指定"/ signalr/中心"URL 并定义用于 Hub 类，在此情况下，对方法的代理方法`StockTickerHub.GetAllStocks`。 如果您愿意，您可以通过使用手动生成此 JavaScript 文件[SignalR 实用程序](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/)。 别忘了禁用动态文件创建在`MapHubs`方法调用。

1. 在中**解决方案资源管理器**，展开**脚本**。

    适用于 jQuery 和 SignalR 的脚本库将显示在该项目。

    > [!IMPORTANT]
    > 包管理器将安装 SignalR 脚本的更高版本。

1. 更新要与项目中的脚本文件的版本相对应的代码块中的脚本引用。

1. 在中**解决方案资源管理器**，右键单击*StockTicker.html*，然后选择**设为起始页**。

#### <a name="create-stocktickerjs"></a>创建 StockTicker.js

现在，创建 JavaScript 文件。

1. 在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **JavaScript 文件**。

1. 将文件命名*StockTicker* ，然后选择**确定**。

1. 添加到此代码*StockTicker.js*文件：

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample12.js)]

### <a name="examine-the-client-code"></a>检查客户端代码

如果您检查客户端代码，它将帮助您了解客户端代码如何与服务器代码以将应用程序结合进行交互。

#### <a name="starting-the-connection"></a>正在启动的连接

`$.connection` 表示 SignalR 代理。 代码获取到的代理的引用`StockTickerHub`类，并将其放入`ticker`变量。 代理名称是所设置的名称`HubName`属性：

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample13.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample14.cs)]

文件中的代码的最后一行定义所有变量和函数后，通过调用 SignalR 初始化 SignalR 连接`start`函数。 `start`函数以异步方式执行，并返回[jQuery 延迟对象](http://api.jquery.com/category/deferred-object/)。 可以调用 done 的函数可指定当应用完成异步操作时要调用的函数。

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample15.js)]

#### <a name="getting-all-the-stocks"></a>获取所有股票

`init`函数调用`getAllStocks`在服务器上的函数，并使用服务器将返回以更新库存表的信息。 请注意，默认情况下，您必须在客户端上使用 camelCasing，即使方法名称是 pascal 大小写的服务器上。 CamelCasing 规则仅适用于方法，而不是对象。 例如，请参阅`stock.Symbol`并`stock.Price`，而非`stock.symbol`或`stock.price`。

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample16.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample17.cs)]

在中`init`方法时，应用程序创建 HTML 通过调用从服务器收到的每个股票对象为表行`formatStock`的格式属性`stock`对象，并通过调用`supplant`来替换中的占位符`rowTemplate`变量`stock`对象属性值。 生成的 HTML 然后追加到常用的表。

> [!NOTE]
> 在调用`init`通过将其作为传递`callback`后异步执行的函数`start`函数完成。 如果您调用`init`作为单独的 JavaScript 语句后调用`start`，该函数会失败，因为它而无需等待启动函数以完成建立连接立即运行。 在这种情况下，`init`函数会尝试调用`getAllStocks`函数应用在建立服务器连接之前。

#### <a name="getting-updated-stock-prices"></a>获取已更新的股票价格

当服务器更改股票的价格时，它将调用`updateStockPrice`上连接的客户端。 该应用将函数添加到的客户端属性`stockTicker`代理，以使其可供调用从服务器。

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample18.js)]

`updateStockPrice`常用对象从服务器接收到一个表行中的相同方法的函数格式`init`函数。 而不是追加到表的行，它将表中找到股票的当前行并替换新行。

## <a name="test-the-application"></a>测试应用程序

您可以测试应用程序以确保它是否正常工作。 你将看到显示股票价格波动的实时股票表的所有浏览器窗口。

1. 在工具栏中，开启**脚本调试**，然后选择播放按钮以在调试模式下运行应用。

    ![启用调试模式下和选择播放的用户的屏幕截图。](tutorial-server-broadcast-with-signalr/_static/image4.png)

    将打开一个浏览器窗口，显示**Live 库存表**。 库存表最初显示了"正在加载..."行，然后，在短时间之后, 应用将显示初始的库存数据，然后股票价格开始更改。

1. 从浏览器中复制 URL、 打开两个其他浏览器，并将 Url 粘贴到地址栏。

    初始股票显示第一个浏览器相同，同时发生了更改。

1. 关闭所有浏览器中，打开新浏览器并转到相同的 URL。

    StockTicker 单一对象不断在服务器中运行。 **Live 库存表**股票不断更改的显示。 不会看到具有零更改图形的初始表。

1. 关闭浏览器。

## <a name="enable-logging"></a>启用日志记录

SignalR 有一个可以在客户端启用帮助解决疑难问题的内置日志记录函数。 在本部分中，启用日志记录并演示如何日志识别您哪一台以下的传输方法使用 SignalR 的示例，请参阅：

* [Websocket](http://en.wikipedia.org/wiki/WebSocket)、 IIS 8 和当前浏览器中受支持。

* [服务器发送事件](http://en.wikipedia.org/wiki/Server-sent_events)、 Internet Explorer 以外的浏览器中受支持。

* [永久帧](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe)、 Internet Explorer 中受支持。

* [Ajax 长轮询](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling)，所有浏览器中受支持。

对于任何给定的连接，SignalR 会选择最佳的传输方法在服务器和客户端支持。

1. 打开*StockTicker.js*。

1. 添加此突出显示的代码，以启用日志记录之前初始化连接文件末尾的代码行：

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample19.js?highlight=2)]

1. 按 F5 运行项目。

1. 打开浏览器的开发人员工具窗口，并选择控制台以查看的日志。 您可能需要刷新页面以查看 SignalR 协商新连接的传输方法的日志。

    * 如果您在 Windows 8 (IIS 8) 上运行 Internet Explorer 10，传输方法是**Websocket**。

    * 如果您在 Windows 7 (IIS 7.5) 上运行 Internet Explorer 10，传输方法是**iframe**。

    * 如果您在 Windows 8 (IIS 8) 上运行 Firefox 19，传输方法是**Websocket**。

        > [!TIP]
        > 在 Firefox 中安装的 Firebug 外接程序以获取控制台窗口。

    * 如果您在 Windows 7 (IIS 7.5) 上运行 Firefox 19，传输方法是**服务器发送**事件。

## <a name="install-the-stockticker-sample"></a>安装 StockTicker 示例

[Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample)安装 StockTicker 的应用程序。 NuGet 包包含多个功能比您从头开始创建的简化版本。 在本教程的此部分中，安装 NuGet 包并查看新功能和实现它们的代码。

> [!IMPORTANT]
> 如果无需执行本教程的前面的步骤安装包，您必须将 OWIN 启动类添加到你的项目。 NuGet 包的此 readme.txt 文件解释了此步骤。

### <a name="install-the-signalrsample-nuget-package"></a>安装 SignalR.Sample NuGet 包

1. 在“解决方案资源管理器”中，右键单击项目，然后选择“管理 NuGet 包”。

1. 在**NuGet 包管理器：SignalR.StockTicker**，选择**浏览**。

1. 从**包源**，选择**nuget.org**。

1. 输入*SignalR.Sample*在搜索框中，选择**Microsoft.AspNet.SignalR.Sample** > **安装**。

1. 在中**解决方案资源管理器**，展开*SignalR.Sample*文件夹。

    安装 SignalR.Sample 包创建文件夹及其内容。

1. 在中*SignalR.Sample*文件夹中，右键单击*StockTicker.html*，然后选择**设为起始页**。

    > [!NOTE]
    > 安装 SignalR.Sample NuGet 包可能会更改中有的 jQuery 版本你*脚本*文件夹。 新*StockTicker.html*包将安装中的文件*SignalR.Sample*文件夹将与包将安装，但如果你想要运行原始的jQuery版本同步*StockTicker.html*再次文件中，您可能必须先更新中的脚本标记的 jQuery 引用。

### <a name="run-the-application"></a>运行此应用程序

 在第一个应用中看到的表具有有用的功能。 完整股票行情自动收录器应用程序显示了新功能： 水平滚动窗口，显示股票数据并更改颜色，因为它们提高或降低的股票。

1. 按 F5  运行应用。

     当第一次运行该应用程序时，"市场""已关闭"，你看到的静态表并不滚动行情自动收录器窗口。

1. 选择**Open Market**。

    ![实时行情自动收录器的屏幕截图。](tutorial-server-broadcast-with-signalr/_static/image5.png)

    * **实时股票行情自动收录器**水平滚动框开始和在服务器开始定期广播在随机的基础上的股票价格发生变化。

    * 每次股票价格更改时，应用程序更新都**实时股票表格**并**实时股票行情自动收录器**。

    * 正股票的价格变化时，应用将显示绿色背景的股票。

    * 更改为负，应用将显示红色背景的股票。

1. 选择**关闭市场**。

    * 表更新停止。

    * 行情自动收录器停止滚动。

1. 选择**重置**。

    * 重置所有常用数据。

    * 应用程序还原之前启动的价格变化的初始状态。

1. 从浏览器中复制 URL、 打开两个其他浏览器，并将 Url 粘贴到地址栏。

1. 你看到相同的数据在同一时间在每个浏览器中动态更新。

1. 在您选择的任何控件，所有浏览器在同一时间响应相同的方式。

### <a name="live-stock-ticker-display"></a>实时股票行情自动收录器显示

**实时股票行情自动收录器**显示为未排序的列表中`<div>`设置格式到单个行的元素的 CSS 样式。 应用初始化和更新表一样的行情自动收录器： 通过替换中的占位符`<li>`模板字符串和动态添加`<li>`元素与`<ul>`元素。 该应用包含通过使用 jQuery 滚动`animate`函数来改变左边距中的未排序列表的`<div>`。

#### <a name="signalrsample-stocktickerhtml"></a>SignalR.Sample StockTicker.html

股票行情 HTML 代码：

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample20.html)]

#### <a name="signalrsample-stocktickercss"></a>SignalR.Sample StockTicker.css

股票行情 CSS 代码：

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample21.html)]

#### <a name="signalrsample-signalrstocktickerjs"></a>SignalR.Sample SignalR.StockTicker.js

使它的 jQuery 代码滚动：

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample22.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a>客户端可以调用的服务器上的其他方法

若要向应用添加灵活性，还有一些其他应用程序可以调用的方法。

#### <a name="signalrsample-stocktickerhubcs"></a>SignalR.Sample StockTickerHub.cs

`StockTickerHub`类定义了四个客户端可以调用的其他方法：

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample23.cs)]

应用程序调用`OpenMarket`， `CloseMarket`，和`Reset`以响应在页面顶部的按钮。 它们演示了一个客户端触发立即传播到所有客户端的状态的更改的模式。 每个方法调用中的方法`StockTicker`类，该类将导致市场状态更改，然后广播新状态。

#### <a name="signalrsample-stocktickercs"></a>SignalR.Sample StockTicker.cs

在中`StockTicker`类，该应用程序维护的状态与市场`MarketState`返回的属性`MarketState`枚举值：

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample24.cs)]

每个更改市场状态的方法都这样做，在锁块的内部因为`StockTicker`类必须是线程安全：

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample25.cs)]

若要确保此代码是线程安全`_marketState`支持字段`MarketState`属性指定`volatile`:

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample26.cs)]

`BroadcastMarketStateChange`和`BroadcastMarketReset`除它们调用不同的方法在客户端上定义外，方法是您已看到的 BroadcastStockPrice 方法相似：

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample27.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a>服务器可以调用的客户端上的其他函数

`updateStockPrice`函数现在可以处理表和行情自动收录器显示，并使用`jQuery.Color`闪烁红色和绿色的颜色。

中的新函数*SignalR.StockTicker.js*启用和禁用基于市场状态的按钮。 它们还停止或启动**实时股票行情自动收录器**水平滚动。 由于许多函数添加到`ticker.client`，此应用使用[jQuery 扩展函数](http://api.jquery.com/jQuery.extend/)以将其添加。

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample28.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a>建立连接后的其他客户端安装程序

客户端会建立连接后，它具有一些附加工作以执行操作：

* 找出市场是否打开或关闭调用适当`marketOpened`或`marketClosed`函数。

* 将附加到按钮的服务器方法调用。

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample29.js)]

应用程序建立连接后，服务器方法不被绑定到之前的按钮。 它是使代码不能调用服务器方法，然后才能将可用。

## <a name="additional-resources"></a>其他资源

在本教程中介绍了如何将消息从服务器广播到所有连接的客户端 SignalR 应用程序进行编程。 现在可以将广播定期更新和以响应来自任何客户端的通知消息。 多线程的单一实例的概念可用于维护多玩家联机游戏方案中的服务器状态。 有关示例，请参阅[ShootR 游戏基于 SignalR](https://github.com/NTaylorMullen/ShootR)。

显示对等通信方案的教程，请参阅[SignalR 入门](introduction-to-signalr.md)并[使用 SignalR 实时更新](tutorial-high-frequency-realtime-with-signalr.md)。

有关 SignalR 的详细信息，请参阅以下资源：

* [ASP.NET SignalR](../../index.md)
* [SignalR 项目](http://signalr.net/)
* [SignalR GitHub 和示例](https://github.com/SignalR/SignalR)
* [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建项目
> * 设置服务器代码
> * 检查服务器代码
> * 设置客户端代码
> * 检查客户端代码
> * 测试应用程序
> * 已启用日志记录

转到下一步的文章，了解如何创建使用 ASP.NET SignalR 2 的实时 web 应用程序。
> [!div class="nextstepaction"]
> [使用 SignalR 创建实时 web 应用](real-time-web-applications-with-signalr.md)