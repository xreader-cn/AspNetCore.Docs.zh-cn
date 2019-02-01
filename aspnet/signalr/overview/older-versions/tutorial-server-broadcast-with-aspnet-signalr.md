---
uid: signalr/overview/older-versions/tutorial-server-broadcast-with-aspnet-signalr
title: 教程：服务器广播使用 ASP.NET SignalR 1.x |Microsoft Docs
author: bradygaster
description: 本教程演示如何创建使用 ASP.NET SignalR 来提供服务器广播的功能的 web 应用程序。 服务器广播方法，communic...
ms.author: bradyg
ms.date: 04/10/2013
ms.assetid: ab7b2554-956a-4f6d-b2a0-4ae0c62e8580
msc.legacyurl: /signalr/overview/older-versions/tutorial-server-broadcast-with-aspnet-signalr
msc.type: authoredcontent
ms.openlocfilehash: 258a55bf72c4b3425d001f478620fa9651952b3f
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837463"
---
<a name="tutorial-server-broadcast-with-aspnet-signalr-1x"></a>教程：服务器广播使用 ASP.NET SignalR 1.x
====================
通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom Dykstra](https://github.com/tdykstra)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本教程演示如何创建使用 ASP.NET SignalR 来提供服务器广播的功能的 web 应用程序。 服务器广播意味着发送到客户端的通信由服务器启动。 这种情况要求不同的编程方式对等方案，例如聊天应用程序，在其中发送到客户端的通信都是由一个或多个客户端。
> 
> 你将在本教程中创建的应用程序模拟股票行情服务器广播功能的典型方案。
> 
> 在本教程的注释是欢迎使用。 如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com)。


## <a name="overview"></a>概述

[Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) NuGet 包安装 Visual Studio 项目中的示例模拟股票行情自动收录器应用程序。 在本教程的第一部分，将从零开始创建该应用程序的简化的版本。 在本教程的其余部分，将安装 NuGet 包，查看其他功能和它将创建的代码。

股票行情自动收录器应用程序是代表要在其中定期"推送"或广播，将通知从服务器到所有连接的客户端的实时应用程序的一种。

在本教程的第一部分中将生成的应用程序显示的网格有库存数据。

![StockTicker 初始版本](tutorial-server-broadcast-with-aspnet-signalr/_static/image1.png)

定期服务器随机更新股票价格，并将更新推送到所有连接的客户端。 在浏览器数字和中的符号 **更改** 并 **%** 列动态更改以响应来自服务器的通知。 如果您打开其他浏览器相同的 URL，它们都同时显示相同的数据和对数据的相同更改。

本教程包含以下各节：

- [系统必备](#prerequisites)
- [创建项目](#createproject)
- [将 SignalR NuGet 包添加](#nugetpackages)
- [设置服务器代码](#server)
- [设置客户端代码](#client)
- [测试应用程序](#test)
- [启用日志记录](#enablelogging)
- [安装和查看完整的 StockTicker 示例](#fullsample)
- [后续步骤](#nextsteps)

> [!NOTE]
> 如果不想要逐步完成构建应用程序的步骤，则可以在新安装 SignalR.Sample 包**空的 ASP.NET Web 应用程序**项目，并通读以下步骤获取代码的说明。 本教程的第一个部分涵盖子集 SignalR.Sample 代码，并且第二部分介绍 SignalR.Sample 包中的其他功能的主要功能。


<a id="prerequisites"></a>

## <a name="prerequisites"></a>系统必备

在开始之前，请确保安装 Visual Studio 2012 或 2010 SP1 安装在您的计算机上。 如果未安装 Visual Studio，请参阅[ASP.NET 下载](https://www.asp.net/downloads)以获取免费的 Visual Studio 2012 Express Web。

如果你有 Visual Studio 2010，请确保[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)安装。

<a id="createproject"></a>

## <a name="create-the-project"></a>创建项目

1. 从**文件**菜单上，单击**新项目**。
2. 在中**新的项目**对话框框中，展开**C#** 下**模板**，然后选择**Web**。
3. 选择**ASP.NET 空 Web 应用程序**模板，将项目命名*SignalR.StockTicker*，然后单击**确定**。

    ![“新建项目”对话框](tutorial-server-broadcast-with-aspnet-signalr/_static/image2.png)

<a id="nugetpackages"></a>

## <a name="add-the-signalr-nuget-packages"></a>将 SignalR NuGet 包添加

### <a name="add-the-signalr-and-jquery-nuget-packages"></a>添加的 SignalR 和 JQuery NuGet 包

通过安装 NuGet 包，可以将 SignalR 功能添加到项目。

1. 单击**工具 |NuGet 包管理器 |包管理器控制台**。
2. 在包管理器中输入以下命令。

    [!code-powershell[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample1.ps1)]

    SignalR 包作为依赖项安装其他 NuGet 包的数。 完成安装后必须全部所需的 ASP.NET 应用程序中使用 SignalR 的服务器和客户端组件。

<a id="server"></a>

## <a name="set-up-the-server-code"></a>设置服务器代码

在本部分中，设置在服务器运行的代码。

### <a name="create-the-stock-class"></a>创建 Stock 类

首先创建将用于存储和传输有关股票的信息的股票模型类。

1. 在项目文件夹中创建一个新类文件，将其命名*Stock.cs*，然后使用以下代码替换模板代码：

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample2.cs)]

    在创建股票时将设置的两个属性是符号 (例如，Microsoft 的 MSFT) 和价格。 其他属性取决于如何以及何时设置价格。 设置价格，第一次的值获取传播到 DayOpen。 当设置价格，更改并且 PercentChange 属性值进行计算的后续时间基于价格和 DayOpen 之间的差异。

### <a name="create-the-stockticker-and-stocktickerhub-classes"></a>创建在 StockTicker 和 StockTickerHub 类

SignalR 中心 API 将用于处理服务器到客户端交互。 从 SignalR Hub 类派生的 StockTickerHub 类将处理来自客户端接收连接和方法调用。 此外需要维护股票数据并运行计时器对象，以定期触发价格更新，独立于客户端连接。 因为中心实例是瞬态的不能置于 Hub 类，这些函数。 为每个操作的中心，例如连接和从客户端对服务器的调用创建的中心类实例。 因此，必须运行在单独的类，您将命名为 StockTicker 的机制，可保护库存数据，更新价格，并会广播价格更新。

![从 StockTicker 的广播](tutorial-server-broadcast-with-aspnet-signalr/_static/image4.png)

您只想要运行的服务器上，因此将需要的单个 StockTicker 实例中设置从每个 StockTickerHub 实例引用的 StockTicker 类的一个实例。 StockTicker 类必须可以广播到客户端，因为它具有股票数据并触发更新，但 StockTicker 不是 Hub 类。 因此，StockTicker 类必须获取对 SignalR Hub 连接上下文对象的引用。 然后，它可以使用 SignalR 连接上下文对象广播到客户端。

1. 在中**解决方案资源管理器**，右键单击项目，然后单击**添加新项**。
2. 如果你有 Visual Studio 2012 [ASP.NET 和 Web Tools 2012.2 更新](https://go.microsoft.com/fwlink/?LinkId=279941)，单击**Web**下**Visual C#** ，然后选择**SignalR Hub 类**项模板。 否则，请选择**类**模板。
3. 将新类命名*StockTickerHub.cs*，然后单击**添加**。

    ![Add StockTickerHub.cs](tutorial-server-broadcast-with-aspnet-signalr/_static/image5.png)
4. 将模板代码替换为以下代码：

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample3.cs)]

    [中心](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx)类用于定义客户端可以调用在服务器的方法。 要定义一种方法： `GetAllStocks()`。 当客户端最初连接到服务器时，它将调用此方法以获取所有其当前价格股票的列表。 该方法可以同步执行并返回`IEnumerable<Stock>`由于它会从内存中返回数据。 如果该方法必须通过执行将涉及等待，如数据库查找或 web 服务调用，获取的数据则会指定`Task<IEnumerable<Stock>>`作为返回值以启用异步处理。 有关详细信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-时要异步执行](index.md)。

    HubName 属性指定将如何在客户端上的 JavaScript 代码中引用该中心。 如果不使用此属性在客户端上的默认名称是类名称，在这种情况下是 stockTickerHub 驼峰式大小写版本。

    正如您将看到更高版本创建 StockTicker 类时，在其静态实例属性中创建该类的单一实例。 StockTicker 的单一实例保持在多少个客户端连接或断开连接，无论内存中，该实例是 GetAllStocks 方法用于返回当前股票信息。
5. 在项目文件夹中创建一个新类文件，将其命名*StockTicker.cs*，然后使用以下代码替换模板代码：

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample4.cs)]

    由于多个线程将运行 StockTicker 代码的同一个实例，StockTicker 类必须是 threadsafe。

    ### <a name="storing-the-singleton-instance-in-a-static-field"></a>在静态字段中存储的单一实例

    此代码初始化静态\_支持的类，并且此实例的实例属性的实例字段是可以创建类的唯一实例，因为构造函数标记为私有。 [迟缓初始化](https://msdn.microsoft.com/library/dd997286.aspx)用于\_实例字段，不出于性能原因，但若要确保创建的实例是 threadsafe。

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample5.cs)]

    客户端连接到服务器，每次在一个单独的线程中运行的 StockTickerHub 类的新实例获取的 StockTicker 单一实例从 StockTicker.Instance 静态属性，如前面在 StockTickerHub 类中看到。

    ### <a name="storing-stock-data-in-a-concurrentdictionary"></a>在 ConcurrentDictionary 中存储常用的数据

    构造函数初始化\_具有一些示例股票数据和 GetAllStocks 股票集合返回股票。 如上文所，StockTickerHub.GetAllStocks 这是客户端可以调用的中心类中的服务器方法又会返回股票的此集合。

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample6.cs)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample7.cs)]

    股票集合指[ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx)线程安全的类型。 作为替代方法，您可以使用[字典](https://msdn.microsoft.com/library/xfhwa508.aspx)对象，并显式锁定字典时对其进行更改。

    对于此示例应用程序，它是确定在内存中存储应用程序数据并释放 StockTicker 实例时丢失数据。 在实际的应用程序将如何使用后端数据存储，如数据库。

    ### <a name="periodically-updating-stock-prices"></a>定期更新股票价格

    构造函数启动定期调用的方法，更新基于随机的股票价格的计时器对象。

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample8.cs)]

    UpdateStockPrices 由计时器，该参数中的 null 将传入调用。 在更新之前的价格，采用锁\_updateStockPricesLock 对象。 如果另一个线程已更新的价格，，然后在列表中每个股票调用 TryUpdateStockPrice 会检查代码。 TryUpdateStockPrice 方法决定是否要更改的股票价格和要对其进行更改。 如果股票价格已更改，调用 BroadcastStockPrice 广播到所有连接的客户端进行的股票价格更改。

    \_UpdatingStockPrices 标志标记为[易失性](https://msdn.microsoft.com/library/x13ttww7.aspx)以确保进行 threadsafe 到它的访问。

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample9.cs)]

    在实际的应用程序，TryUpdateStockPrice 方法需要调用 web 服务，以便查找价格上涨。它在此代码中使用的随机数生成器随机进行更改。

    ### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a>获取 SignalR 上下文，以便 StockTicker 类可以广播到客户端

    因为价格更改来自此处 StockTicker 对象，所以这是需要在所有连接的客户端上调用 updateStockPrice 方法的对象。 用于调用客户端的方法，Hub 类中有一个 API，但 StockTicker 不从 Hub 类派生，但尚未对任何集线器对象的引用。 因此，若要将广播到连接的客户端，StockTicker 类必须 StockTickerHub 类获取 SignalR 上下文实例并使用它来在客户端上调用方法。

    该代码获取对 SignalR 上下文的引用时创建的单一类实例，将引用传递给构造函数中，并构造函数将其放入客户端属性。

    有两个原因你希望只需一次获取上下文的理由： 获取上下文是代价高昂的操作，并一次获取将确保消息发送到客户端的预期的顺序得到保留。

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample10.cs)]

    获取上下文的客户端属性并将其置于 StockTickerClient 属性中可编写代码来调用方法的客户端中的中心类一样外观相同。 例如，若要将广播到所有客户端可以编写 Clients.All.updateStockPrice(stock)。

    在调用 BroadcastStockPrice updateStockPrice 方法尚不存在;在编写客户端上运行的代码时，你将从更高版本添加它。 因为 Clients.All 是动态的这意味着将在运行时计算该表达式可以引用 updateStockPrice 此处。 当此方法调用执行时，SignalR 将发送方法名称和参数值到客户端，并在客户端有一个名为 updateStockPrice 方法，将调用该方法和参数值将传递给它。

    Clients.All 意味着将发送到所有客户端。 SignalR 提供其他选项来指定哪些客户端或客户端将发送到的组。 有关详细信息，请参阅[HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx)。

### <a name="register-the-signalr-route"></a>注册 SignalR 路由

服务器需要知道要截获并将定向到 SignalR 的 URL。 若要执行操作，将添加一些代码以*Global.asax*文件。

1. 在中**解决方案资源管理器**，右键单击该项目，然后单击**添加新项**。
2. 选择**全局应用程序类**项目模板，然后单击**添加**。

    ![添加 global.asax](tutorial-server-broadcast-with-aspnet-signalr/_static/image6.png)
3. 将 SignalR 路由注册代码添加到应用程序\_Start 方法：

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample11.cs)]

    默认情况下，所有 SignalR 流量的基 URL 是"/ signalr"，并"/ signalr/中心"用于检索一个动态生成的 JavaScript 文件，定义用于应用程序中包含的所有集线器代理。 MapHubs 方法包括让您可以指定不同的基 URL 和某些 SignalR 选项中的一个实例的重载[HubConfiguration](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubconfiguration(v=vs.111).aspx)类。
4. 添加 using 语句的文件的顶部：

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample12.cs)]
5. 保存并关闭*Global.asax*文件，并生成项目。

现在已经完成设置服务器代码。 下一节中将设置客户端。

<a id="client"></a>

## <a name="set-up-the-client-code"></a>设置客户端代码

1. 在项目文件夹中，创建一个新的 HTML 文件并将其命名*StockTicker.html*。
2. 将模板代码替换为以下代码：

    [!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample13.html)]

    HTML 5 列、 一个标题行，与具有一个单元格跨越所有 5 个列的数据行创建一个表。 数据行显示"正在加载..."，并暂时不可用时在应用程序启动后，才会显示。 JavaScript 代码将删除该行，并添加其位置的行中使用从服务器中检索的股票数据。

    脚本标记指定的 jQuery 脚本文件、 SignalR 核心脚本文件、 SignalR 代理脚本文件和更高版本，您将创建一个 StockTicker 脚本文件。 SignalR 代理脚本文件的说明进行操作，它指定"/ signalr/中心"URL，动态生成，并在这种情况下定义 StockTickerHub.GetAllStocks 的 Hub 类上的方法的代理方法。 如果您愿意，您可以通过使用手动生成此 JavaScript 文件[SignalR 实用程序](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/)和禁用 MapHubs 方法调用中的动态文件创建。
3. > [!IMPORTANT]
   > 请确保在 JavaScript 文件中引用*StockTicker.html*正确无误。 也就是说，确保你的脚本标记 (1.8.2 在示例中) 中的 jQuery 版本是在项目中的 jQuery 版本相同*脚本*文件夹，并确保你的脚本标记中的 SignalR 版本是 SignalR 相同在项目的版本*脚本*文件夹。 如有必要，请更改脚本标记中的文件名称。
4. 在中**解决方案资源管理器**，右键单击*StockTicker.html*，然后单击**设为起始页**。
5. 在项目文件夹中创建的新 JavaScript 文件并将其命名*StockTicker.js*...
6. 将模板代码替换为以下代码：

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample14.js)]

    $.connection 指 SignalR 代理。 该代码获取 StockTickerHub 类对代理的引用，并将其放入行情自动收录器变量。 代理名称是 [HubName] 属性已设置的名称：

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample15.js)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample16.cs)]

    定义所有变量和函数后，文件中的代码的最后一行初始化 SignalR 连接通过调用 SignalR 启动函数。 启动函数以异步方式执行，并返回[jQuery 延迟对象](http://api.jquery.com/category/deferred-object/)，这意味着您可以调用 done 的函数可指定异步操作完成时要调用的函数...

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample17.js)]

    Init 函数 getAllStocks 函数调用服务器上，并使用服务器将返回以更新库存表的信息。 请注意，默认情况下，您必须使用 camel 大小写格式的客户端上但方法名是 pascal 大小写的服务器上。 Camel 大小写规则仅适用于方法，而不是对象。 例如，指股票。符号和库存。价格、 不 stock.symbol 或 stock.price。

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample18.js)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample19.cs)]

    如果你想要在客户端上使用 pascal 大小写，或者如果你想要使用完全不同的方法名称，无法修饰具有 HubMethodName 特性中心的方法相同的方式，修饰，Hub 类本身与 HubName 属性。

    在 init 方法中，为从服务器收到通过调用 formatStock 格式属性的常用对象，每个股票对象创建 HTML 表行，然后通过调用取代 (顶部定义*StockTicker.js*) 将 rowTemplate 变量中的占位符替换为股票对象属性值。 生成的 HTML 然后追加到常用的表。

    通过将其作为执行异步启动函数完成后的回调函数传递调用 init。 如果作为单独的 JavaScript 语句调用开始后调用 init，该函数会失败，因为立即会执行而不必等待启动函数以完成建立连接。 在这种情况下，init 函数会尝试调用 getAllStocks 函数，才能建立服务器连接。

    当服务器更改股票的价格时，它调用 updateStockPrice 上连接的客户端。 若要在服务器使其可供调用情况下，该函数添加到 stockTicker 代理的客户端属性。

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample20.js)]

    UpdateStockPrice 函数设置从服务器接收到一个表行，如 init 函数中所示相同的方式的常用对象的格式。 但是，而不是追加到表的行，它查找股票的当前行的表中并替换新行。

<a id="test"></a>

## <a name="test-the-application"></a>测试应用程序

1. 按 F5 在调试模式下运行应用程序。

    库存表最初会显示"正在加载..."行，然后显示初始的股票数据时，一小段延迟后，然后股票价格开始更改。

    ![“加载”](tutorial-server-broadcast-with-aspnet-signalr/_static/image7.png)

    ![初始库存表](tutorial-server-broadcast-with-aspnet-signalr/_static/image8.png)

    ![从服务器接收更改的库存表](tutorial-server-broadcast-with-aspnet-signalr/_static/image9.png)
2. 从浏览器地址栏复制 URL 并将其粘贴到一个或多个新的浏览器窗口。

    初始股票显示第一个浏览器相同，同时发生了更改。
3. 关闭所有浏览器并打开新浏览器，然后转到相同的 URL。

    在服务器中运行，因此库存表屏幕将显示在股票已继续更改逐渐 StockTicker 单一实例对象。 （不到具有零更改图形的初始表。）
4. 关闭浏览器。

<a id="enablelogging"></a>

## <a name="enable-logging"></a>启用日志记录

SignalR 有一个可以在客户端启用帮助解决疑难问题的内置日志记录函数。 在本部分中启用日志记录并演示如何日志识别您哪一台以下的传输方法使用 SignalR 的示例，请参阅：

- [Websocket](http://en.wikipedia.org/wiki/WebSocket)、 IIS 8 和当前浏览器中受支持。
- [服务器发送事件](http://en.wikipedia.org/wiki/Server-sent_events)、 Internet Explorer 以外的浏览器中受支持。
- [永久帧](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe)、 Internet Explorer 中受支持。
- [Ajax 长轮询](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling)，所有浏览器中受支持。

对于任何给定的连接，SignalR 会选择最佳的传输方法在服务器和客户端支持。

1. 打开*StockTicker.js*并添加代码以启用日志记录之前初始化连接文件末尾的代码行：

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample21.js)]
2. 按 F5 运行项目。
3. 打开浏览器的开发人员工具窗口，并选择控制台以查看的日志。 您可能需要刷新页面以查看 Signalr 协商新连接的传输方法的日志。

    如果在 Windows 8 (IIS 8) 上运行 Internet Explorer 10，传输方法是 Websocket。

    ![IE 10 IIS 8 控制台](tutorial-server-broadcast-with-aspnet-signalr/_static/image10.png)

    如果在 Windows 7 (IIS 7.5) 上运行 Internet Explorer 10，传输方法是 iframe。

    ![IE 10 Console, IIS 7.5](tutorial-server-broadcast-with-aspnet-signalr/_static/image11.png)

    在 Firefox 中安装的 Firebug 外接程序以获取控制台窗口。 如果在 Windows 8 (IIS 8) 上运行 Firefox 19，传输方法是 Websocket。

    ![Firefox 19 IIS 8 Websocket](tutorial-server-broadcast-with-aspnet-signalr/_static/image12.png)

    如果在 Windows 7 (IIS 7.5) 上运行 Firefox 19，传输方法将是服务器发送事件。

    ![Firefox 19 IIS 7.5 控制台](tutorial-server-broadcast-with-aspnet-signalr/_static/image13.png)

<a id="fullsample"></a>

## <a name="install-and-review-the-full-stockticker-sample"></a>安装和查看完整的 StockTicker 示例

StockTicker 应用程序的情况下，安装[Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) NuGet 包包含更多的功能比只是创建从零开始的简化版本。 在本教程的此部分中，安装 NuGet 包并查看新功能和实现它们的代码。

### <a name="install-the-signalrsample-nuget-package"></a>安装 SignalR.Sample NuGet 包

1. 在中**解决方案资源管理器**，右键单击项目，然后单击**管理 NuGet 包**。
2. 在中**管理 NuGet 包**对话框中，单击**联机**，输入*SignalR.Sample*中**联机搜索**框，然后依次**安装**中**SignalR.Sample**包。

    ![安装 SignalR.Sample 包](tutorial-server-broadcast-with-aspnet-signalr/_static/image14.png)
3. 在中*Global.asax*文件中，注释掉 RouteTable.Routes.MapHubs(); 行之前在应用程序中添加\_Start 方法。

    中的代码*Global.asax*不再需要因为 SignalR.Sample 包注册中的 SignalR 路由*应用\_Start/RegisterHubs.cs*文件：

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample22.cs)]

    引用的程序集特性的 WebActivator 类包含在 WebActivatorEx NuGet 包，可为 SignalR.Sample 包的依赖项安装。
4. 在中**解决方案资源管理器**，展开*SignalR.Sample*安装 SignalR.Sample 包创建的文件夹。
5. 在中*SignalR.Sample*文件夹中，右键单击*StockTicker.html*，然后单击**设为起始页**。

    > [!NOTE]
    > 安装 SignalR.Sample NuGet 包可能会更改中有的 jQuery 版本你*脚本*文件夹。 新*StockTicker.html*包将安装中的文件*SignalR.Sample*文件夹将与包将安装，但如果你想要运行原始的jQuery版本同步*StockTicker.html*再次文件中，您可能必须先更新中的脚本标记的 jQuery 引用。

### <a name="run-the-application"></a>运行此应用程序

1. 按 F5 运行该应用程序。

    除了你此前看到的网格中，完整股票行情自动收录器应用程序显示一个水平滚动的窗口，显示相同的库存数据。 第一次的应用程序运行时，"市场""已关闭"，你看到静态网格和不滚动的行情自动收录器窗口。

    ![StockTicker 屏幕开始](tutorial-server-broadcast-with-aspnet-signalr/_static/image15.png)

    当您单击**公开市场**，则**实时股票行情自动收录器**水平滚动框开始和在服务器开始定期广播在随机的基础上的股票价格发生变化。 每次股票价格发生更改，同时**实时股票表格**网格并**实时股票行情自动收录器**框会更新。 当股票的价格更改为正时，股票会显示绿色背景; 以及并更改为负，包含一个红色背景显示股票。

    ![StockTicker 应用市场打开](tutorial-server-broadcast-with-aspnet-signalr/_static/image16.png)

    **关闭市场**按钮停止所做的更改并停止行情自动收录器滚动，并**重置**按钮可重置所有股票数据为初始状态价格更改启动之前。 如果您打开更多的浏览器窗口并转到同一个 URL，您将看到相同的数据在同一时间在每个浏览器中动态更新。 当您单击某个按钮时，所有浏览器都在同一时间具有相同的方式。

### <a name="live-stock-ticker-display"></a>实时股票行情自动收录器显示

**实时股票行情自动收录器**显示为未排序的列表中的 CSS 样式格式设置为单个行的 div 元素。 行情自动收录器会被初始化并更新表一样： 通过替换中的占位符&lt;li&gt;模板字符串和动态添加&lt;l i&gt;元素&lt;u l&gt;元素。 通过使用 jQuery 进行动画处理函数来改变该 div。 中的未排序列表距左侧执行滚动

股票行情 HTML:

[!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample23.html)]

股票行情 CSS:

[!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample24.html)]

使它的 jQuery 代码滚动：

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample25.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a>客户端可以调用的服务器上的其他方法

StockTickerHub 类定义了四个客户端可以调用的其他方法：

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample26.cs)]

在页面顶部的按钮响应称为 OpenMarket、 CloseMarket 和重置。 它们演示了一个客户端触发立即传播到所有客户端的状态的更改的模式。 每个方法调用中的方法 StockTicker 类市场状态更改，然后再将广播新状态的效果。

在 StockTicker 类中，通过返回 MarketState 枚举值的 MarketState 属性的维护是市场的状态：

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample27.cs)]

每个更改市场状态的方法都这样做，在锁块的内部因为 StockTicker 类必须是 threadsafe:

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample28.cs)]

若要确保此代码是 threadsafe， \_marketState 字段支持 MarketState 属性标记为易失性，

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample29.cs)]

BroadcastMarketStateChange 和 BroadcastMarketReset 方法是您已看到的 BroadcastStockPrice 方法相似，只不过它们调用在客户端定义的不同方法：

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample30.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a>服务器可以调用的客户端上的其他函数

UpdateStockPrice 函数现在可处理网格和行情自动收录器显示，并使用 jQuery.Color 闪烁红色和绿色的颜色。

中的新函数*SignalR.StockTicker.js*启用和禁用按钮根据市场的状态，并在停止或启动行情自动收录器窗口水平滚动。 由于多个函数添加到 ticker.client，因此[jQuery 扩展函数](http://api.jquery.com/jQuery.extend/)用于添加它们。

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample31.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a>建立连接后的其他客户端安装程序

客户端会建立连接后，它具有一些附加工作以执行操作： 找出市场是否打开或关闭以便调用相应 marketOpened 或 marketClosed 函数，并附加到按钮的服务器方法调用。

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample32.js)]

服务器方法不是绑定到之前的按钮之后建立的连接，从而使代码不能尝试之前，它们可调用服务器方法。

<a id="nextsteps"></a>

## <a name="next-steps"></a>后续步骤

在本教程中介绍了如何将消息从服务器广播到所有连接的客户端，定期更新，以响应来自任何客户端通知的 SignalR 应用程序进行编程。 使用多线程的单一实例维护服务器状态模式也还可在多玩家联机游戏方案。 有关示例，请参阅[依赖于使用 SignalR ShootR 游戏](https://github.com/NTaylorMullen/ShootR)。

显示对等通信方案的教程，请参阅[SignalR 入门](index.md)并[使用 SignalR 实时更新](index.md)。

若要了解更高级的 SignalR 开发概念，请访问以下站点 SignalR 源代码和资源：

- [ASP.NET SignalR](https://asp.net/signalr/)
- [SignalR 项目](http://signalr.net/)
- [SignalR Github 和示例](https://github.com/SignalR/SignalR)
- [SignalR Wiki](https://github.com/SignalR/SignalR/wiki)
