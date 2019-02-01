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
# <a name="tutorial-server-broadcast-with-signalr-2"></a><span data-ttu-id="5fdc7-103">教程：使用 SignalR 2 广播的服务器</span><span class="sxs-lookup"><span data-stu-id="5fdc7-103">Tutorial: Server broadcast with SignalR 2</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

<span data-ttu-id="5fdc7-104">本教程演示如何创建使用 ASP.NET SignalR 2 来提供服务器广播的功能的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-104">This tutorial shows how to create a web application that uses ASP.NET SignalR 2 to provide server broadcast functionality.</span></span> <span data-ttu-id="5fdc7-105">服务器广播意味着在服务器开始发送到客户端的通信。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-105">Server broadcast means that the server starts the communications sent to clients.</span></span>

<span data-ttu-id="5fdc7-106">你将在本教程中创建的应用程序模拟股票行情服务器广播功能的典型方案。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-106">The application that you'll create in this tutorial simulates a stock ticker, a typical scenario for server broadcast functionality.</span></span> <span data-ttu-id="5fdc7-107">我们会定期服务器随机更新股票价格，广播到所有连接的客户端的更新。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-107">Periodically, the server randomly updates stock prices and broadcast the updates to all connected clients.</span></span> <span data-ttu-id="5fdc7-108">在浏览器、 数字和中的符号 **更改** 并 **%** 列动态更改以响应来自服务器的通知。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-108">In the browser, the numbers and symbols in the **Change** and **%** columns dynamically change in response to notifications from the server.</span></span> <span data-ttu-id="5fdc7-109">如果您打开其他浏览器相同的 URL，它们都同时显示相同的数据和对数据的相同更改。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-109">If you open additional browsers to the same URL, they all show the same data and the same changes to the data simultaneously.</span></span>

![创建 web](tutorial-server-broadcast-with-signalr/_static/image1.png)

<span data-ttu-id="5fdc7-111">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-111">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5fdc7-112">创建项目</span><span class="sxs-lookup"><span data-stu-id="5fdc7-112">Create the project</span></span>
> * <span data-ttu-id="5fdc7-113">设置服务器代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-113">Set up the server code</span></span>
> * <span data-ttu-id="5fdc7-114">检查服务器代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-114">Examine the server code</span></span>
> * <span data-ttu-id="5fdc7-115">设置客户端代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-115">Set up the client code</span></span>
> * <span data-ttu-id="5fdc7-116">检查客户端代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-116">Examine the client code</span></span>
> * <span data-ttu-id="5fdc7-117">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="5fdc7-117">Test the application</span></span>
> * <span data-ttu-id="5fdc7-118">启用日志记录</span><span class="sxs-lookup"><span data-stu-id="5fdc7-118">Enable logging</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5fdc7-119">如果不想要逐步完成构建应用程序的步骤，你可以在新的空的 ASP.NET Web 应用程序项目中安装 SignalR.Sample 包。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-119">If you don't want to work through the steps of building the application, you can install the SignalR.Sample package in a new Empty ASP.NET Web Application project.</span></span> <span data-ttu-id="5fdc7-120">如果不执行本教程中的步骤安装 NuGet 包，必须按照中的说明*readme.txt*文件。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-120">If you install the NuGet package without performing the steps in this tutorial, you must follow the instructions in the *readme.txt* file.</span></span> <span data-ttu-id="5fdc7-121">若要运行包需要添加 OWIN 启动类的调用`ConfigureSignalR`中已安装的程序包的方法。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-121">To run the package you need to add an OWIN startup class which calls the `ConfigureSignalR` method in the installed package.</span></span> <span data-ttu-id="5fdc7-122">如果不添加 OWIN 启动类，将收到错误。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-122">You will receive an error if you do not add the OWIN startup class.</span></span> <span data-ttu-id="5fdc7-123">请参阅[安装 StockTicker 示例](#install-the-stockticker-sample)本文的部分。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-123">See the [Install the StockTicker sample](#install-the-stockticker-sample) section of this article.</span></span>


## <a name="prerequisites"></a><span data-ttu-id="5fdc7-124">系统必备</span><span class="sxs-lookup"><span data-stu-id="5fdc7-124">Prerequisites</span></span>

 * <span data-ttu-id="5fdc7-125">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)与**ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-125">[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

## <a name="create-the-project"></a><span data-ttu-id="5fdc7-126">创建项目</span><span class="sxs-lookup"><span data-stu-id="5fdc7-126">Create the project</span></span>

<span data-ttu-id="5fdc7-127">本部分演示如何使用 Visual Studio 2017 创建空的 ASP.NET Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-127">This section shows how to use Visual Studio 2017 to create an empty ASP.NET Web Application.</span></span>

1. <span data-ttu-id="5fdc7-128">在 Visual Studio 中创建 ASP.NET Web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-128">In Visual Studio, create an ASP.NET Web Application.</span></span>

    ![创建 web](tutorial-server-broadcast-with-signalr/_static/image2.png)

1. <span data-ttu-id="5fdc7-130">在中**新 ASP.NET Web 应用程序-SignalR.StockTicker**窗口中，保留**空**，并选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-130">In the **New ASP.NET Web Application - SignalR.StockTicker** window, leave **Empty** selected and select **OK**.</span></span>

## <a name="set-up-the-server-code"></a><span data-ttu-id="5fdc7-131">设置服务器代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-131">Set up the server code</span></span>

<span data-ttu-id="5fdc7-132">在本部分中，您将设置在服务器运行的代码。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-132">In this section, you set up the code that runs on the server.</span></span>

### <a name="create-the-stock-class"></a><span data-ttu-id="5fdc7-133">创建 Stock 类</span><span class="sxs-lookup"><span data-stu-id="5fdc7-133">Create the Stock class</span></span>

<span data-ttu-id="5fdc7-134">首先创建*股票*模型将用于存储和传输有关股票的信息的类。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-134">You begin by creating the *Stock* model class that you'll use to store and transmit information about a stock.</span></span>

1. <span data-ttu-id="5fdc7-135">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **类**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-135">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="5fdc7-136">将类命名*股票*并将其添加到项目。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-136">Name the class *Stock* and add it to the project.</span></span>

1. <span data-ttu-id="5fdc7-137">中的代码替换*Stock.cs*文件使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-137">Replace the code in the *Stock.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample1.cs)]

    <span data-ttu-id="5fdc7-138">在创建股票时将设置的两个属性是`Symbol`(例如，Microsoft 的 MSFT) 和`Price`。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-138">The two properties that you'll set when you create stocks are `Symbol` (for example, MSFT for Microsoft) and `Price`.</span></span> <span data-ttu-id="5fdc7-139">其他属性取决于如何以及何时将`Price`。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-139">The other properties depend on how and when you set `Price`.</span></span> <span data-ttu-id="5fdc7-140">首次设置`Price`，值获取会传播到`DayOpen`。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-140">The first time you set `Price`, the value gets propagated to `DayOpen`.</span></span> <span data-ttu-id="5fdc7-141">在此之后，当您将设置`Price`，该应用程序计算`Change`和`PercentChange`属性值基于之间的差异`Price`和`DayOpen`。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-141">After that, when you set `Price`, the app calculates the `Change` and `PercentChange` property values based on the difference between `Price` and `DayOpen`.</span></span>

### <a name="create-the-stocktickerhub-and-stockticker-classes"></a><span data-ttu-id="5fdc7-142">创建的 StockTickerHub 和 StockTicker 类</span><span class="sxs-lookup"><span data-stu-id="5fdc7-142">Create the StockTickerHub and StockTicker classes</span></span>

<span data-ttu-id="5fdc7-143">SignalR 中心 API 将用于处理服务器到客户端交互。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-143">You'll use the SignalR Hub API to handle server-to-client interaction.</span></span> <span data-ttu-id="5fdc7-144">一个`StockTickerHub`派生的类`SignalRHub`类将处理来自客户端接收连接和方法调用。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-144">A `StockTickerHub` class that derives from the `SignalRHub` class will handle receiving connections and method calls from clients.</span></span> <span data-ttu-id="5fdc7-145">您还需要维护股票数据并运行`Timer`对象。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-145">You also need to maintain stock data and run a `Timer` object.</span></span> <span data-ttu-id="5fdc7-146">`Timer`对象将定期触发价格更新独立于客户端连接。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-146">The `Timer` object will periodically trigger price updates independent of client connections.</span></span> <span data-ttu-id="5fdc7-147">无法将这些函数放入`Hub`类，因为中心是暂时的。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-147">You can't put these functions in a `Hub` class, because Hubs are transient.</span></span> <span data-ttu-id="5fdc7-148">应用创建`Hub`集线器，例如连接和从客户端对服务器的调用上每个任务的类实例。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-148">The app creates a `Hub` class instance for each task on the hub, like connections and calls from the client to the server.</span></span> <span data-ttu-id="5fdc7-149">因此，可保护库存数据，更新价格，并会广播价格更新机制必须运行在单独的类。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-149">So the mechanism that keeps stock data, updates prices, and broadcasts the price updates has to run in a separate class.</span></span> <span data-ttu-id="5fdc7-150">将类命名`StockTicker`。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-150">You'll name the class `StockTicker`.</span></span>

![从 StockTicker 的广播](tutorial-server-broadcast-with-signalr/_static/image3.png)

<span data-ttu-id="5fdc7-152">您只想的一个实例`StockTicker`类来运行的服务器上，因此将需要设置从每个引用`StockTickerHub`到单一实例`StockTicker`实例。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-152">You only want one instance of the `StockTicker` class to run on the server, so you'll need to set up a reference from each `StockTickerHub` instance to the singleton `StockTicker` instance.</span></span> <span data-ttu-id="5fdc7-153">`StockTicker`类具有广播到客户端，因为它具有股票数据，并触发更新，但`StockTicker`不是`Hub`类。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-153">The `StockTicker` class has to broadcast to clients because it has the stock data and triggers updates, but `StockTicker` isn't a `Hub` class.</span></span> <span data-ttu-id="5fdc7-154">`StockTicker`类必须获取对 SignalR Hub 连接上下文对象的引用。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-154">The `StockTicker` class has to get a reference to the SignalR Hub connection context object.</span></span> <span data-ttu-id="5fdc7-155">然后，它可以使用 SignalR 连接上下文对象广播到客户端。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-155">It can then use the SignalR connection context object to broadcast to clients.</span></span>

#### <a name="create-stocktickerhubcs"></a><span data-ttu-id="5fdc7-156">Create StockTickerHub.cs</span><span class="sxs-lookup"><span data-stu-id="5fdc7-156">Create StockTickerHub.cs</span></span>

1. <span data-ttu-id="5fdc7-157">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-157">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="5fdc7-158">在中**添加新项-SignalR.StockTicker**，选择**已安装** > **Visual C#**   >  **Web** >  **SignalR** ，然后选择**SignalR Hub 类 (v2)**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-158">In **Add New Item - SignalR.StockTicker**, select **Installed** > **Visual C#** > **Web** > **SignalR**  and then select **SignalR Hub Class (v2)**.</span></span>

1. <span data-ttu-id="5fdc7-159">将类命名*StockTickerHub*并将其添加到项目。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-159">Name the class *StockTickerHub* and add it to the project.</span></span>

    <span data-ttu-id="5fdc7-160">此步骤将创建*StockTickerHub.cs*类文件。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-160">This step creates the *StockTickerHub.cs* class file.</span></span> <span data-ttu-id="5fdc7-161">同时，它会添加到项目支持 SignalR 的脚本文件和程序集引用一组。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-161">Simultaneously, it adds a set of script files and assembly references that supports SignalR to the project.</span></span>

1. <span data-ttu-id="5fdc7-162">中的代码替换*StockTickerHub.cs*文件使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-162">Replace the code in the *StockTickerHub.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample2.cs)]

1. <span data-ttu-id="5fdc7-163">保存该文件。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-163">Save the file.</span></span>

<span data-ttu-id="5fdc7-164">此应用使用[中心](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx)类来定义客户端可以调用在服务器的方法。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-164">The app uses the [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) class to define methods the clients can call on the server.</span></span> <span data-ttu-id="5fdc7-165">要定义一种方法： `GetAllStocks()`。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-165">You're defining one method: `GetAllStocks()`.</span></span> <span data-ttu-id="5fdc7-166">当客户端最初连接到服务器时，它将调用此方法以获取所有其当前价格股票的列表。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-166">When a client initially connects to the server, it will call this method to get a list of all of the stocks with their current prices.</span></span> <span data-ttu-id="5fdc7-167">该方法可以同步运行并返回`IEnumerable<Stock>`由于它会从内存中返回数据。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-167">The method can run synchronously and return `IEnumerable<Stock>` because it's returning data from memory.</span></span>

<span data-ttu-id="5fdc7-168">如果该方法必须通过执行将涉及等待，如数据库查找或 web 服务调用，获取的数据则会指定`Task<IEnumerable<Stock>>`作为返回值以启用异步处理。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-168">If the method had to get the data by doing something that would involve waiting, like a database lookup or a web service call, you would specify `Task<IEnumerable<Stock>>` as the return value to enable asynchronous processing.</span></span> <span data-ttu-id="5fdc7-169">有关详细信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-时要异步执行](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods)。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-169">For more information, see [ASP.NET SignalR Hubs API Guide - Server - When to execute asynchronously](../guide-to-the-api/hubs-api-guide-server.md#asyncmethods).</span></span>

<span data-ttu-id="5fdc7-170">`HubName`属性指定应用程序如何将引用在客户端上的 JavaScript 代码中的中心。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-170">The `HubName` attribute specifies how the app will reference the Hub in JavaScript code on the client.</span></span> <span data-ttu-id="5fdc7-171">在客户端上的默认名称如果不使用此属性是类名称，在这种情况下将驼峰式大小写版本`stockTickerHub`。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-171">The default name on the client if you don't use this attribute, is a camelCase version of the class name, which in this case would be `stockTickerHub`.</span></span>

<span data-ttu-id="5fdc7-172">正如您看到更高版本创建时`StockTicker`类，应用创建该类的单一实例中它的静态`Instance`属性。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-172">As you'll see later when you create the `StockTicker` class, the app creates a singleton instance of that class in its static `Instance` property.</span></span> <span data-ttu-id="5fdc7-173">该单一实例`StockTicker`无论多少个客户端连接或断开连接的内存中。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-173">That singleton instance of `StockTicker` is in memory no matter how many clients connect or disconnect.</span></span> <span data-ttu-id="5fdc7-174">该实例是什么`GetAllStocks()`方法用于返回当前股票信息。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-174">That instance is what the `GetAllStocks()` method uses to return current stock information.</span></span>

#### <a name="create-stocktickercs"></a><span data-ttu-id="5fdc7-175">创建 StockTicker.cs</span><span class="sxs-lookup"><span data-stu-id="5fdc7-175">Create StockTicker.cs</span></span>

1. <span data-ttu-id="5fdc7-176">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **类**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-176">In **Solution Explorer**, right-click the project and select **Add** > **Class**.</span></span>

1. <span data-ttu-id="5fdc7-177">将类命名*StockTicker*并将其添加到项目。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-177">Name the class *StockTicker* and add it to the project.</span></span>

1. <span data-ttu-id="5fdc7-178">中的代码替换*StockTicker.cs*文件使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-178">Replace the code in the *StockTicker.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample3.cs)]

<span data-ttu-id="5fdc7-179">由于所有线程都运行 StockTicker 代码的同一个实例，StockTicker 类必须是线程安全的。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-179">Since all threads will be running the same instance of StockTicker code, the StockTicker class has to be thread-safe.</span></span>

### <a name="examine-the-server-code"></a><span data-ttu-id="5fdc7-180">检查服务器代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-180">Examine the server code</span></span>

<span data-ttu-id="5fdc7-181">如果您检查服务器代码，它将帮助你了解应用程序的工作原理。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-181">If you examine the server code, it will help you understand how the app works.</span></span>

#### <a name="storing-the-singleton-instance-in-a-static-field"></a><span data-ttu-id="5fdc7-182">在静态字段中存储的单一实例</span><span class="sxs-lookup"><span data-stu-id="5fdc7-182">Storing the singleton instance in a static field</span></span>

<span data-ttu-id="5fdc7-183">此代码初始化静态`_instance`支持字段`Instance`与类的实例的属性。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-183">The code initializes the static `_instance` field that backs the `Instance` property with an instance of the class.</span></span> <span data-ttu-id="5fdc7-184">构造函数是私有的因为它是应用程序可以创建类的唯一实例。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-184">Because the constructor is private, it's the only instance of the class that the app can create.</span></span> <span data-ttu-id="5fdc7-185">此应用使用[迟缓初始化](/dotnet/framework/performance/lazy-initialization)为`_instance`字段。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-185">The app uses [Lazy initialization](/dotnet/framework/performance/lazy-initialization) for the `_instance` field.</span></span> <span data-ttu-id="5fdc7-186">它不是为了提高性能。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-186">It's not for performance reasons.</span></span> <span data-ttu-id="5fdc7-187">它是为了确保创建的实例是线程安全。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-187">It's to make sure the instance creation is thread-safe.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample4.cs)]

<span data-ttu-id="5fdc7-188">客户端连接到服务器，每次在一个单独的线程中运行的 StockTickerHub 类的新实例获取从 StockTicker 单一实例`StockTicker.Instance`静态属性，作为您了解到之前在`StockTickerHub`类。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-188">Each time a client connects to the server, a new instance of the StockTickerHub class running in a separate thread gets the StockTicker singleton instance from the `StockTicker.Instance` static property, as you saw earlier in the `StockTickerHub` class.</span></span>

#### <a name="storing-stock-data-in-a-concurrentdictionary"></a><span data-ttu-id="5fdc7-189">在 ConcurrentDictionary 中存储常用的数据</span><span class="sxs-lookup"><span data-stu-id="5fdc7-189">Storing stock data in a ConcurrentDictionary</span></span>

<span data-ttu-id="5fdc7-190">构造函数初始化`_stocks`具有一些示例股票数据集合和`GetAllStocks`返回股票。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-190">The constructor initializes the `_stocks` collection with some sample stock data, and `GetAllStocks` returns the stocks.</span></span> <span data-ttu-id="5fdc7-191">如所见，返回此集合的股票`StockTickerHub.GetAllStocks`，这是中的服务器方法`Hub`客户端可以调用的类。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-191">As you saw earlier, this collection of stocks is returned by `StockTickerHub.GetAllStocks`, which is a server method in the `Hub` class that clients can call.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample5.cs)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample6.cs)]

<span data-ttu-id="5fdc7-192">股票集合指[ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx)线程安全的类型。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-192">The stocks collection is defined as a [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) type for thread safety.</span></span> <span data-ttu-id="5fdc7-193">作为替代方法，您可以使用[字典](https://msdn.microsoft.com/library/xfhwa508.aspx)对象，并显式锁定字典时对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-193">As an alternative, you could use a [Dictionary](https://msdn.microsoft.com/library/xfhwa508.aspx) object and explicitly lock the dictionary when you make changes to it.</span></span>

<span data-ttu-id="5fdc7-194">对于此示例应用程序，它是确定将应用程序数据存储在内存中并释放应用程序时，会丢失数据`StockTicker`实例。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-194">For this sample application, it's OK to store application data in memory and to lose the data when the app disposes of the  `StockTicker` instance.</span></span> <span data-ttu-id="5fdc7-195">在实际的应用程序，您将如何使用后端数据存储，如数据库。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-195">In a real application, you would work with a back-end data store like a database.</span></span>

#### <a name="periodically-updating-stock-prices"></a><span data-ttu-id="5fdc7-196">定期更新股票价格</span><span class="sxs-lookup"><span data-stu-id="5fdc7-196">Periodically updating stock prices</span></span>

<span data-ttu-id="5fdc7-197">构造函数启动`Timer`定期调用的方法，更新基于随机的股票价格的对象。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-197">The constructor starts up a `Timer` object that periodically calls methods that update stock prices on a random basis.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample7.cs)]

 <span data-ttu-id="5fdc7-198">`Timer` 调用`UpdateStockPrices`，空值参数中的阶段。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-198">`Timer` calls `UpdateStockPrices`, which passes in null in the state parameter.</span></span> <span data-ttu-id="5fdc7-199">在更新之前的价格，该应用上采用锁`_updateStockPricesLock`对象。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-199">Before updating prices, the app takes a lock on the `_updateStockPricesLock` object.</span></span> <span data-ttu-id="5fdc7-200">如果另一个线程已更新的价格，，然后调用代码将检查`TryUpdateStockPrice`上列表中每个股票。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-200">The code checks if another thread is already updating prices, and then it calls `TryUpdateStockPrice` on each stock in the list.</span></span> <span data-ttu-id="5fdc7-201">`TryUpdateStockPrice`方法决定是否要更改的股票价格和要对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-201">The `TryUpdateStockPrice` method decides whether to change the stock price, and how much to change it.</span></span> <span data-ttu-id="5fdc7-202">如果股票价格发生更改，该应用程序调用`BroadcastStockPrice`广播到所有的股票价格更改连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-202">If the stock price changes, the app calls `BroadcastStockPrice` to broadcast the stock price change to all connected clients.</span></span>

<span data-ttu-id="5fdc7-203">`_updatingStockPrices`指定的标志[易失性](https://msdn.microsoft.com/library/x13ttww7.aspx)以确保它是线程安全。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-203">The `_updatingStockPrices` flag designated [volatile](https://msdn.microsoft.com/library/x13ttww7.aspx) to make sure it is thread-safe.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample8.cs)]

<span data-ttu-id="5fdc7-204">在实际的应用程序，`TryUpdateStockPrice`方法将调用 web 服务，以便查找价格。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-204">In a real application, the `TryUpdateStockPrice` method would call a web service to look up the price.</span></span> <span data-ttu-id="5fdc7-205">在此代码中，该应用使用的随机数生成器随机进行更改。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-205">In this code, the app uses a random number generator to make changes randomly.</span></span>

#### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a><span data-ttu-id="5fdc7-206">获取 SignalR 上下文，以便 StockTicker 类可以广播到客户端</span><span class="sxs-lookup"><span data-stu-id="5fdc7-206">Getting the SignalR context so that the StockTicker class can broadcast to clients</span></span>

<span data-ttu-id="5fdc7-207">因为价格更改此处源自`StockTicker`对象，它是对象，需要调用`updateStockPrice`所有连接的客户端上的方法。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-207">Because the price changes originate here in the `StockTicker` object, it's the object that needs to call an `updateStockPrice` method on all connected clients.</span></span> <span data-ttu-id="5fdc7-208">在中`Hub`类，您用于调用客户端的方法，有一个 API，但`StockTicker`也不是派生`Hub`类，并不提供任何参考`Hub`对象。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-208">In a `Hub` class, you have an API for calling client methods, but `StockTicker` doesn't derive from the `Hub` class and doesn't have a reference to any `Hub` object.</span></span> <span data-ttu-id="5fdc7-209">用于向已连接客户端广播`StockTicker`类具有要获取有关 SignalR 上下文实例`StockTickerHub`类，并使用它来在客户端上调用方法。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-209">To broadcast to connected clients, the `StockTicker` class has to get the SignalR context instance for the `StockTickerHub` class and use that to call methods on clients.</span></span>

<span data-ttu-id="5fdc7-210">该代码获取对 SignalR 上下文的引用时创建的单一类实例，将引用传递给构造函数中，并构造函数将其放入`Clients`属性。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-210">The code gets a reference to the SignalR context when it creates the singleton class instance, passes that reference to the constructor, and the constructor puts it in the `Clients` property.</span></span>

<span data-ttu-id="5fdc7-211">有两个原因你想要一次只能获取上下文的原因： 获取上下文是代价高昂的任务，并可确保应用程序保留的消息发送到客户端的预期的顺序后获取它。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-211">There are two reasons why you want to get the context only once: getting the context is an expensive task, and getting it once makes sure the app preserves the intended order of messages sent to the clients.</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample9.cs)]

<span data-ttu-id="5fdc7-212">获取`Clients`属性的上下文并将其放入`StockTickerClient`属性，可以编写代码来调用方法的客户端中一样看上去相同`Hub`类。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-212">Getting the `Clients` property of the context and putting it in the `StockTickerClient` property lets you write code to call client methods that looks the same as it would in a `Hub` class.</span></span> <span data-ttu-id="5fdc7-213">例如，若要广播到所有客户端可以编写`Clients.All.updateStockPrice(stock)`。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-213">For instance, to broadcast to all clients you can write `Clients.All.updateStockPrice(stock)`.</span></span>

<span data-ttu-id="5fdc7-214">`updateStockPrice`方法中调用`BroadcastStockPrice`尚不存在。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-214">The `updateStockPrice` method that you're calling in `BroadcastStockPrice` doesn't exist yet.</span></span> <span data-ttu-id="5fdc7-215">在编写客户端上运行的代码时，你将从更高版本添加它。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-215">You'll add it later when you write code that runs on the client.</span></span> <span data-ttu-id="5fdc7-216">您可以参考`updateStockPrice`此处因为`Clients.All`是动态的这意味着应用程序将计算在运行时表达式的值。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-216">You can refer to `updateStockPrice` here because `Clients.All` is dynamic, which means the app will evaluate the expression at runtime.</span></span> <span data-ttu-id="5fdc7-217">当此方法调用执行时，SignalR 会方法名称和参数值向发送客户端，并在客户端有一个名为方法`updateStockPrice`，应用程序将调用该方法并向其传递参数值。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-217">When this method call executes, SignalR will send the method name and the parameter value to the client, and if the client has a method named `updateStockPrice`, the app will call that method and pass the parameter value to it.</span></span>

<span data-ttu-id="5fdc7-218">`Clients.All` 表示将发送到所有客户端。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-218">`Clients.All` means send to all clients.</span></span> <span data-ttu-id="5fdc7-219">SignalR 提供其他选项来指定哪些客户端或客户端将发送到的组。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-219">SignalR gives you other options to specify which clients or groups of clients to send to.</span></span> <span data-ttu-id="5fdc7-220">有关详细信息，请参阅[HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx)。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-220">For more information, see [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).</span></span>

### <a name="register-the-signalr-route"></a><span data-ttu-id="5fdc7-221">注册 SignalR 路由</span><span class="sxs-lookup"><span data-stu-id="5fdc7-221">Register the SignalR route</span></span>

<span data-ttu-id="5fdc7-222">服务器需要知道要截获并将定向到 SignalR 的 URL。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-222">The server needs to know which URL to intercept and direct to SignalR.</span></span> <span data-ttu-id="5fdc7-223">若要执行此操作，添加 OWIN 启动类：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-223">To do that, add an OWIN startup class:</span></span>

1. <span data-ttu-id="5fdc7-224">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **新项**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-224">In **Solution Explorer**, right-click the project and select **Add** > **New Item**.</span></span>

1. <span data-ttu-id="5fdc7-225">在中**添加新项-SignalR.StockTicker**选择**已安装** > **Visual C#**   >  **Web**和然后选择**OWIN 启动类**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-225">In **Add New Item - SignalR.StockTicker** select **Installed** > **Visual C#** > **Web** and then select **OWIN Startup Class**.</span></span>

1. <span data-ttu-id="5fdc7-226">将类命名*启动*，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-226">Name the class *Startup* and select **OK**.</span></span>

1. <span data-ttu-id="5fdc7-227">中的默认代码替换*Startup.cs*文件使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-227">Replace the default code in the *Startup.cs* file with this code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample10.cs)]

<span data-ttu-id="5fdc7-228">现在，你已设置的服务器代码。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-228">You have now finished setting up the server code.</span></span> <span data-ttu-id="5fdc7-229">在下一步部分中，将设置客户端。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-229">In the next section, you'll set up the client.</span></span>

## <a name="set-up-the-client-code"></a><span data-ttu-id="5fdc7-230">设置客户端代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-230">Set up the client code</span></span>

<span data-ttu-id="5fdc7-231">在本部分中，您将设置在客户端运行的代码。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-231">In this section, you set up the code that runs on the client.</span></span>

### <a name="create-the-html-page-and-javascript-file"></a><span data-ttu-id="5fdc7-232">创建 HTML 页和 JavaScript 文件</span><span class="sxs-lookup"><span data-stu-id="5fdc7-232">Create the HTML page and JavaScript file</span></span>

<span data-ttu-id="5fdc7-233">HTML 页将显示的数据和 JavaScript 文件将组织的数据。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-233">The HTML page will display the data and the JavaScript file will organize the data.</span></span>

#### <a name="create-stocktickerhtml"></a><span data-ttu-id="5fdc7-234">创建 StockTicker.html</span><span class="sxs-lookup"><span data-stu-id="5fdc7-234">Create StockTicker.html</span></span>

<span data-ttu-id="5fdc7-235">首先，你将添加 HTML 客户端。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-235">First, you'll add the HTML client.</span></span>

1. <span data-ttu-id="5fdc7-236">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **HTML 页**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-236">In **Solution Explorer**, right-click the project and select **Add** > **HTML Page**.</span></span>

1. <span data-ttu-id="5fdc7-237">将文件命名*StockTicker* ，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-237">Name the file *StockTicker* and select **OK**.</span></span>

1. <span data-ttu-id="5fdc7-238">中的默认代码替换*StockTicker.html*文件使用以下代码：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-238">Replace the default code in the *StockTicker.html* file with this code:</span></span>

    [!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample11.html?highlight=40-43)]

    <span data-ttu-id="5fdc7-239">HTML 5 列、 一个标题行，与具有一个单元格跨越所有五个列的数据行创建一个表。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-239">The HTML creates a table with five columns, a header row, and a data row with a single cell that spans all five columns.</span></span> <span data-ttu-id="5fdc7-240">数据行显示了"正在加载..."暂时不可用时启动该应用程序。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-240">The data row shows "loading..." momentarily when the app starts.</span></span> <span data-ttu-id="5fdc7-241">JavaScript 代码将删除该行，并添加其位置的行中使用从服务器中检索的股票数据。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-241">JavaScript code will remove that row and add in its place rows with stock data retrieved from the server.</span></span>

    <span data-ttu-id="5fdc7-242">指定脚本标记：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-242">The script tags specify:</span></span>

    * <span data-ttu-id="5fdc7-243">JQuery 脚本文件。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-243">The jQuery script file.</span></span>

    * <span data-ttu-id="5fdc7-244">SignalR 核心脚本文件。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-244">The SignalR core script file.</span></span>

    * <span data-ttu-id="5fdc7-245">SignalR 代理脚本文件。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-245">The SignalR proxies script file.</span></span>

    * <span data-ttu-id="5fdc7-246">更高版本，您将创建一个 StockTicker 脚本文件。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-246">A StockTicker script file that you'll create later.</span></span>

    <span data-ttu-id="5fdc7-247">应用动态生成 SignalR 代理脚本文件。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-247">The app dynamically generates the SignalR proxies script file.</span></span> <span data-ttu-id="5fdc7-248">它指定"/ signalr/中心"URL 并定义用于 Hub 类，在此情况下，对方法的代理方法`StockTickerHub.GetAllStocks`。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-248">It specifies the "/signalr/hubs" URL and defines proxy methods for the methods on the Hub class, in this case, for `StockTickerHub.GetAllStocks`.</span></span> <span data-ttu-id="5fdc7-249">如果您愿意，您可以通过使用手动生成此 JavaScript 文件[SignalR 实用程序](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/)。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-249">If you prefer, you can generate this JavaScript file manually by using [SignalR Utilities](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/).</span></span> <span data-ttu-id="5fdc7-250">别忘了禁用动态文件创建在`MapHubs`方法调用。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-250">Don't forget to disable dynamic file creation in the `MapHubs` method call.</span></span>

1. <span data-ttu-id="5fdc7-251">在中**解决方案资源管理器**，展开**脚本**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-251">In **Solution Explorer**, expand **Scripts**.</span></span>

    <span data-ttu-id="5fdc7-252">适用于 jQuery 和 SignalR 的脚本库将显示在该项目。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-252">Script libraries for jQuery and SignalR are visible in the project.</span></span>

    > [!IMPORTANT]
    > <span data-ttu-id="5fdc7-253">包管理器将安装 SignalR 脚本的更高版本。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-253">The package manager will install a later version of the SignalR scripts.</span></span>

1. <span data-ttu-id="5fdc7-254">更新要与项目中的脚本文件的版本相对应的代码块中的脚本引用。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-254">Update the script references in the code block to correspond to the versions of the script files in the project.</span></span>

1. <span data-ttu-id="5fdc7-255">在中**解决方案资源管理器**，右键单击*StockTicker.html*，然后选择**设为起始页**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-255">In **Solution Explorer**, right-click *StockTicker.html*, and then select **Set as Start Page**.</span></span>

#### <a name="create-stocktickerjs"></a><span data-ttu-id="5fdc7-256">创建 StockTicker.js</span><span class="sxs-lookup"><span data-stu-id="5fdc7-256">Create StockTicker.js</span></span>

<span data-ttu-id="5fdc7-257">现在，创建 JavaScript 文件。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-257">Now create the JavaScript file.</span></span>

1. <span data-ttu-id="5fdc7-258">在中**解决方案资源管理器**，右键单击该项目并选择**添加** > **JavaScript 文件**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-258">In **Solution Explorer**, right-click the project and select **Add** > **JavaScript File**.</span></span>

1. <span data-ttu-id="5fdc7-259">将文件命名*StockTicker* ，然后选择**确定**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-259">Name the file *StockTicker* and select **OK**.</span></span>

1. <span data-ttu-id="5fdc7-260">添加到此代码*StockTicker.js*文件：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-260">Add this code to the *StockTicker.js* file:</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample12.js)]

### <a name="examine-the-client-code"></a><span data-ttu-id="5fdc7-261">检查客户端代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-261">Examine the client code</span></span>

<span data-ttu-id="5fdc7-262">如果您检查客户端代码，它将帮助您了解客户端代码如何与服务器代码以将应用程序结合进行交互。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-262">If you examine the client code, it will help you learn how the client code interacts with the server code to make the app work.</span></span>

#### <a name="starting-the-connection"></a><span data-ttu-id="5fdc7-263">正在启动的连接</span><span class="sxs-lookup"><span data-stu-id="5fdc7-263">Starting the connection</span></span>

<span data-ttu-id="5fdc7-264">`$.connection` 表示 SignalR 代理。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-264">`$.connection` refers to the SignalR proxies.</span></span> <span data-ttu-id="5fdc7-265">代码获取到的代理的引用`StockTickerHub`类，并将其放入`ticker`变量。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-265">The code gets a reference to the proxy for the `StockTickerHub` class and puts it in the `ticker` variable.</span></span> <span data-ttu-id="5fdc7-266">代理名称是所设置的名称`HubName`属性：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-266">The proxy name is the name that was set by the `HubName` attribute:</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample13.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample14.cs)]

<span data-ttu-id="5fdc7-267">文件中的代码的最后一行定义所有变量和函数后，通过调用 SignalR 初始化 SignalR 连接`start`函数。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-267">After you define all the variables and functions, the last line of code in the file initializes the SignalR connection by calling the SignalR `start` function.</span></span> <span data-ttu-id="5fdc7-268">`start`函数以异步方式执行，并返回[jQuery 延迟对象](http://api.jquery.com/category/deferred-object/)。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-268">The `start` function executes asynchronously and returns a [jQuery Deferred object](http://api.jquery.com/category/deferred-object/).</span></span> <span data-ttu-id="5fdc7-269">可以调用 done 的函数可指定当应用完成异步操作时要调用的函数。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-269">You can call the done function to specify the function to call when the app finishes the asynchronous action.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample15.js)]

#### <a name="getting-all-the-stocks"></a><span data-ttu-id="5fdc7-270">获取所有股票</span><span class="sxs-lookup"><span data-stu-id="5fdc7-270">Getting all the stocks</span></span>

<span data-ttu-id="5fdc7-271">`init`函数调用`getAllStocks`在服务器上的函数，并使用服务器将返回以更新库存表的信息。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-271">The `init` function calls the `getAllStocks` function on the server and uses the information that the server returns to update the stock table.</span></span> <span data-ttu-id="5fdc7-272">请注意，默认情况下，您必须在客户端上使用 camelCasing，即使方法名称是 pascal 大小写的服务器上。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-272">Notice that, by default, you have to use camelCasing on the client even though the method name is pascal-cased on the server.</span></span> <span data-ttu-id="5fdc7-273">CamelCasing 规则仅适用于方法，而不是对象。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-273">The camelCasing rule only applies to methods, not objects.</span></span> <span data-ttu-id="5fdc7-274">例如，请参阅`stock.Symbol`并`stock.Price`，而非`stock.symbol`或`stock.price`。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-274">For example, you refer to `stock.Symbol` and `stock.Price`, not `stock.symbol` or `stock.price`.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample16.js)]

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample17.cs)]

<span data-ttu-id="5fdc7-275">在中`init`方法时，应用程序创建 HTML 通过调用从服务器收到的每个股票对象为表行`formatStock`的格式属性`stock`对象，并通过调用`supplant`来替换中的占位符`rowTemplate`变量`stock`对象属性值。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-275">In the `init` method, the app creates HTML for a table row for each stock object received from the server by calling `formatStock` to format properties of the `stock` object, and then by calling `supplant` to replace placeholders in the `rowTemplate` variable with the `stock` object property values.</span></span> <span data-ttu-id="5fdc7-276">生成的 HTML 然后追加到常用的表。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-276">The resulting HTML is then appended to the stock table.</span></span>

> [!NOTE]
> <span data-ttu-id="5fdc7-277">在调用`init`通过将其作为传递`callback`后异步执行的函数`start`函数完成。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-277">You call `init` by passing it in as a `callback` function that executes after the asynchronous `start` function finishes.</span></span> <span data-ttu-id="5fdc7-278">如果您调用`init`作为单独的 JavaScript 语句后调用`start`，该函数会失败，因为它而无需等待启动函数以完成建立连接立即运行。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-278">If you called `init` as a separate JavaScript statement after calling `start`, the function would fail because it would run immediately without waiting for the start function to finish establishing the connection.</span></span> <span data-ttu-id="5fdc7-279">在这种情况下，`init`函数会尝试调用`getAllStocks`函数应用在建立服务器连接之前。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-279">In that case, the `init` function would try to call the `getAllStocks` function before the app establishes a server connection.</span></span>

#### <a name="getting-updated-stock-prices"></a><span data-ttu-id="5fdc7-280">获取已更新的股票价格</span><span class="sxs-lookup"><span data-stu-id="5fdc7-280">Getting updated stock prices</span></span>

<span data-ttu-id="5fdc7-281">当服务器更改股票的价格时，它将调用`updateStockPrice`上连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-281">When the server changes a stock's price, it calls the `updateStockPrice` on connected clients.</span></span> <span data-ttu-id="5fdc7-282">该应用将函数添加到的客户端属性`stockTicker`代理，以使其可供调用从服务器。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-282">The app adds the function to the client property of the `stockTicker` proxy to make it available to calls from the server.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample18.js)]

<span data-ttu-id="5fdc7-283">`updateStockPrice`常用对象从服务器接收到一个表行中的相同方法的函数格式`init`函数。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-283">The `updateStockPrice` function formats a stock object received from the server into a table row the same way as in the `init` function.</span></span> <span data-ttu-id="5fdc7-284">而不是追加到表的行，它将表中找到股票的当前行并替换新行。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-284">Instead of appending the row to the table, it finds the stock's current row in the table and replaces that row with the new one.</span></span>

## <a name="test-the-application"></a><span data-ttu-id="5fdc7-285">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="5fdc7-285">Test the application</span></span>

<span data-ttu-id="5fdc7-286">您可以测试应用程序以确保它是否正常工作。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-286">You can test the app to make sure it's working.</span></span> <span data-ttu-id="5fdc7-287">你将看到显示股票价格波动的实时股票表的所有浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-287">You'll see all browser windows display the live stock table with stock prices fluctuating.</span></span>

1. <span data-ttu-id="5fdc7-288">在工具栏中，开启**脚本调试**，然后选择播放按钮以在调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-288">In the toolbar, turn on **Script Debugging** and then select the play button to run the app in Debug mode.</span></span>

    ![启用调试模式下和选择播放的用户的屏幕截图。](tutorial-server-broadcast-with-signalr/_static/image4.png)

    <span data-ttu-id="5fdc7-290">将打开一个浏览器窗口，显示**Live 库存表**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-290">A browser window will open displaying the **Live Stock Table**.</span></span> <span data-ttu-id="5fdc7-291">库存表最初显示了"正在加载..."行，然后，在短时间之后, 应用将显示初始的库存数据，然后股票价格开始更改。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-291">The stock table initially shows the "loading..." line, then, after a short time, the app shows the initial stock data, and then the stock prices start to change.</span></span>

1. <span data-ttu-id="5fdc7-292">从浏览器中复制 URL、 打开两个其他浏览器，并将 Url 粘贴到地址栏。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-292">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

    <span data-ttu-id="5fdc7-293">初始股票显示第一个浏览器相同，同时发生了更改。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-293">The initial stock display is the same as the first browser and changes happen simultaneously.</span></span>

1. <span data-ttu-id="5fdc7-294">关闭所有浏览器中，打开新浏览器并转到相同的 URL。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-294">Close all browsers, open a new browser, and go to the same URL.</span></span>

    <span data-ttu-id="5fdc7-295">StockTicker 单一对象不断在服务器中运行。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-295">The StockTicker singleton object continued to run in the server.</span></span> <span data-ttu-id="5fdc7-296">**Live 库存表**股票不断更改的显示。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-296">The **Live Stock Table** shows that the stocks have continued to change.</span></span> <span data-ttu-id="5fdc7-297">不会看到具有零更改图形的初始表。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-297">You don't see the initial table with zero change figures.</span></span>

1. <span data-ttu-id="5fdc7-298">关闭浏览器。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-298">Close the browser.</span></span>

## <a name="enable-logging"></a><span data-ttu-id="5fdc7-299">启用日志记录</span><span class="sxs-lookup"><span data-stu-id="5fdc7-299">Enable logging</span></span>

<span data-ttu-id="5fdc7-300">SignalR 有一个可以在客户端启用帮助解决疑难问题的内置日志记录函数。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-300">SignalR has a built-in logging function that you can enable on the client to aid in troubleshooting.</span></span> <span data-ttu-id="5fdc7-301">在本部分中，启用日志记录并演示如何日志识别您哪一台以下的传输方法使用 SignalR 的示例，请参阅：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-301">In this section, you enable logging and see examples that show how logs tell you which of the following transport methods SignalR is using:</span></span>

* <span data-ttu-id="5fdc7-302">[Websocket](http://en.wikipedia.org/wiki/WebSocket)、 IIS 8 和当前浏览器中受支持。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-302">[WebSockets](http://en.wikipedia.org/wiki/WebSocket), supported by IIS 8 and current browsers.</span></span>

* <span data-ttu-id="5fdc7-303">[服务器发送事件](http://en.wikipedia.org/wiki/Server-sent_events)、 Internet Explorer 以外的浏览器中受支持。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-303">[Server-sent events](http://en.wikipedia.org/wiki/Server-sent_events), supported by browsers other than Internet Explorer.</span></span>

* <span data-ttu-id="5fdc7-304">[永久帧](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe)、 Internet Explorer 中受支持。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-304">[Forever frame](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), supported by Internet Explorer.</span></span>

* <span data-ttu-id="5fdc7-305">[Ajax 长轮询](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling)，所有浏览器中受支持。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-305">[Ajax long polling](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), supported by all browsers.</span></span>

<span data-ttu-id="5fdc7-306">对于任何给定的连接，SignalR 会选择最佳的传输方法在服务器和客户端支持。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-306">For any given connection, SignalR chooses the best transport method that both the server and the client support.</span></span>

1. <span data-ttu-id="5fdc7-307">打开*StockTicker.js*。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-307">Open *StockTicker.js*.</span></span>

1. <span data-ttu-id="5fdc7-308">添加此突出显示的代码，以启用日志记录之前初始化连接文件末尾的代码行：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-308">Add this highlighted line of code to enable logging immediately before the code that initializes the connection at the end of the file:</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample19.js?highlight=2)]

1. <span data-ttu-id="5fdc7-309">按 F5 运行项目。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-309">Press **F5** to run the project.</span></span>

1. <span data-ttu-id="5fdc7-310">打开浏览器的开发人员工具窗口，并选择控制台以查看的日志。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-310">Open your browser's developer tools window, and select the Console to see the logs.</span></span> <span data-ttu-id="5fdc7-311">您可能需要刷新页面以查看 SignalR 协商新连接的传输方法的日志。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-311">You might have to refresh the page to see the logs of SignalR negotiating the transport method for a new connection.</span></span>

    * <span data-ttu-id="5fdc7-312">如果您在 Windows 8 (IIS 8) 上运行 Internet Explorer 10，传输方法是**Websocket**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-312">If you're running Internet Explorer 10 on Windows 8 (IIS 8), the transport method is **WebSockets**.</span></span>

    * <span data-ttu-id="5fdc7-313">如果您在 Windows 7 (IIS 7.5) 上运行 Internet Explorer 10，传输方法是**iframe**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-313">If you're running Internet Explorer 10 on Windows 7 (IIS 7.5), the transport method is **iframe**.</span></span>

    * <span data-ttu-id="5fdc7-314">如果您在 Windows 8 (IIS 8) 上运行 Firefox 19，传输方法是**Websocket**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-314">If you're running Firefox 19 on Windows 8 (IIS 8), the transport method is **WebSockets**.</span></span>

        > [!TIP]
        > <span data-ttu-id="5fdc7-315">在 Firefox 中安装的 Firebug 外接程序以获取控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-315">In Firefox, install the Firebug add-in to get a Console window.</span></span>

    * <span data-ttu-id="5fdc7-316">如果您在 Windows 7 (IIS 7.5) 上运行 Firefox 19，传输方法是**服务器发送**事件。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-316">If you're running Firefox 19 on Windows 7 (IIS 7.5), the transport method is **server-sent** events.</span></span>

## <a name="install-the-stockticker-sample"></a><span data-ttu-id="5fdc7-317">安装 StockTicker 示例</span><span class="sxs-lookup"><span data-stu-id="5fdc7-317">Install the StockTicker sample</span></span>

<span data-ttu-id="5fdc7-318">[Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample)安装 StockTicker 的应用程序。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-318">The [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) installs the StockTicker application.</span></span> <span data-ttu-id="5fdc7-319">NuGet 包包含多个功能比您从头开始创建的简化版本。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-319">The NuGet package includes more features than the simplified version that you created from scratch.</span></span> <span data-ttu-id="5fdc7-320">在本教程的此部分中，安装 NuGet 包并查看新功能和实现它们的代码。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-320">In this section of the tutorial, you install the NuGet package and review the new features and the code that implements them.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="5fdc7-321">如果无需执行本教程的前面的步骤安装包，您必须将 OWIN 启动类添加到你的项目。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-321">If you install the package without performing the earlier steps of this tutorial, you must add an OWIN startup class to your project.</span></span> <span data-ttu-id="5fdc7-322">NuGet 包的此 readme.txt 文件解释了此步骤。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-322">This readme.txt file for the NuGet package explains this step.</span></span>

### <a name="install-the-signalrsample-nuget-package"></a><span data-ttu-id="5fdc7-323">安装 SignalR.Sample NuGet 包</span><span class="sxs-lookup"><span data-stu-id="5fdc7-323">Install the SignalR.Sample NuGet package</span></span>

1. <span data-ttu-id="5fdc7-324">在“解决方案资源管理器”中，右键单击项目，然后选择“管理 NuGet 包”。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-324">In **Solution Explorer**, right-click the project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="5fdc7-325">在**NuGet 包管理器：SignalR.StockTicker**，选择**浏览**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-325">In **NuGet Package manager: SignalR.StockTicker**, select **Browse**.</span></span>

1. <span data-ttu-id="5fdc7-326">从**包源**，选择**nuget.org**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-326">From **Package source**, select **nuget.org**.</span></span>

1. <span data-ttu-id="5fdc7-327">输入*SignalR.Sample*在搜索框中，选择**Microsoft.AspNet.SignalR.Sample** > **安装**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-327">Enter *SignalR.Sample* in the search box and select **Microsoft.AspNet.SignalR.Sample** > **Install**.</span></span>

1. <span data-ttu-id="5fdc7-328">在中**解决方案资源管理器**，展开*SignalR.Sample*文件夹。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-328">In **Solution Explorer**, expand the *SignalR.Sample* folder.</span></span>

    <span data-ttu-id="5fdc7-329">安装 SignalR.Sample 包创建文件夹及其内容。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-329">Installing the SignalR.Sample package created the folder and its contents.</span></span>

1. <span data-ttu-id="5fdc7-330">在中*SignalR.Sample*文件夹中，右键单击*StockTicker.html*，然后选择**设为起始页**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-330">In the *SignalR.Sample* folder, right-click *StockTicker.html*, and then select **Set As Start Page**.</span></span>

    > [!NOTE]
    > <span data-ttu-id="5fdc7-331">安装 SignalR.Sample NuGet 包可能会更改中有的 jQuery 版本你*脚本*文件夹。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-331">Installing The SignalR.Sample NuGet package might change the version of jQuery that you have in your *Scripts* folder.</span></span> <span data-ttu-id="5fdc7-332">新*StockTicker.html*包将安装中的文件*SignalR.Sample*文件夹将与包将安装，但如果你想要运行原始的jQuery版本同步*StockTicker.html*再次文件中，您可能必须先更新中的脚本标记的 jQuery 引用。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-332">The new *StockTicker.html* file that the package installs in the *SignalR.Sample* folder will be in sync with the jQuery version that the package installs, but if you want to run your original *StockTicker.html* file again, you might have to update the jQuery reference in the script tag first.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="5fdc7-333">运行此应用程序</span><span class="sxs-lookup"><span data-stu-id="5fdc7-333">Run the application</span></span>

 <span data-ttu-id="5fdc7-334">在第一个应用中看到的表具有有用的功能。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-334">The table that you saw in the first app had useful features.</span></span> <span data-ttu-id="5fdc7-335">完整股票行情自动收录器应用程序显示了新功能： 水平滚动窗口，显示股票数据并更改颜色，因为它们提高或降低的股票。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-335">The full stock ticker application shows new features: a horizontally scrolling window that shows the stock data and stocks that change color as they rise and fall.</span></span>

1. <span data-ttu-id="5fdc7-336">按 F5  运行应用。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-336">Press **F5** to run the app.</span></span>

     <span data-ttu-id="5fdc7-337">当第一次运行该应用程序时，"市场""已关闭"，你看到的静态表并不滚动行情自动收录器窗口。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-337">When you run the app for the first time, the "market" is "closed" and you see a static table and a ticker window that isn't scrolling.</span></span>

1. <span data-ttu-id="5fdc7-338">选择**Open Market**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-338">Select **Open Market**.</span></span>

    ![实时行情自动收录器的屏幕截图。](tutorial-server-broadcast-with-signalr/_static/image5.png)

    * <span data-ttu-id="5fdc7-340">**实时股票行情自动收录器**水平滚动框开始和在服务器开始定期广播在随机的基础上的股票价格发生变化。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-340">The **Live Stock Ticker** box starts to scroll horizontally, and the server starts to periodically broadcast stock price changes on a random basis.</span></span>

    * <span data-ttu-id="5fdc7-341">每次股票价格更改时，应用程序更新都**实时股票表格**并**实时股票行情自动收录器**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-341">Each time a stock price changes, the app updates both the **Live Stock Table** and the **Live Stock Ticker**.</span></span>

    * <span data-ttu-id="5fdc7-342">正股票的价格变化时，应用将显示绿色背景的股票。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-342">When a stock's price change is positive, the app shows the stock with a green background.</span></span>

    * <span data-ttu-id="5fdc7-343">更改为负，应用将显示红色背景的股票。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-343">When the change is negative, the app shows the stock with a red background.</span></span>

1. <span data-ttu-id="5fdc7-344">选择**关闭市场**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-344">Select **Close Market**.</span></span>

    * <span data-ttu-id="5fdc7-345">表更新停止。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-345">The table updates stop.</span></span>

    * <span data-ttu-id="5fdc7-346">行情自动收录器停止滚动。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-346">The ticker stops scrolling.</span></span>

1. <span data-ttu-id="5fdc7-347">选择**重置**。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-347">Select **Reset**.</span></span>

    * <span data-ttu-id="5fdc7-348">重置所有常用数据。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-348">All stock data is reset.</span></span>

    * <span data-ttu-id="5fdc7-349">应用程序还原之前启动的价格变化的初始状态。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-349">The app restores the initial state before price changes started.</span></span>

1. <span data-ttu-id="5fdc7-350">从浏览器中复制 URL、 打开两个其他浏览器，并将 Url 粘贴到地址栏。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-350">Copy the URL from the browser, open two other browsers, and paste the URLs into the address bars.</span></span>

1. <span data-ttu-id="5fdc7-351">你看到相同的数据在同一时间在每个浏览器中动态更新。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-351">You see the same data dynamically updated at the same time in each browser.</span></span>

1. <span data-ttu-id="5fdc7-352">在您选择的任何控件，所有浏览器在同一时间响应相同的方式。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-352">When you select any of the controls, all browsers respond the same way at the same time.</span></span>

### <a name="live-stock-ticker-display"></a><span data-ttu-id="5fdc7-353">实时股票行情自动收录器显示</span><span class="sxs-lookup"><span data-stu-id="5fdc7-353">Live Stock Ticker display</span></span>

<span data-ttu-id="5fdc7-354">**实时股票行情自动收录器**显示为未排序的列表中`<div>`设置格式到单个行的元素的 CSS 样式。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-354">The **Live Stock Ticker** display is an unordered list in a `<div>` element formatted into a single line by CSS styles.</span></span> <span data-ttu-id="5fdc7-355">应用初始化和更新表一样的行情自动收录器： 通过替换中的占位符`<li>`模板字符串和动态添加`<li>`元素与`<ul>`元素。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-355">The app initializes and updates the ticker the same way as the table: by replacing placeholders in an `<li>` template string and dynamically adding the `<li>` elements to the `<ul>` element.</span></span> <span data-ttu-id="5fdc7-356">该应用包含通过使用 jQuery 滚动`animate`函数来改变左边距中的未排序列表的`<div>`。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-356">The app includes  scrolling by using the jQuery `animate` function to vary the margin-left of the unordered list within the `<div>`.</span></span>

#### <a name="signalrsample-stocktickerhtml"></a><span data-ttu-id="5fdc7-357">SignalR.Sample StockTicker.html</span><span class="sxs-lookup"><span data-stu-id="5fdc7-357">SignalR.Sample StockTicker.html</span></span>

<span data-ttu-id="5fdc7-358">股票行情 HTML 代码：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-358">The stock ticker HTML code:</span></span>

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample20.html)]

#### <a name="signalrsample-stocktickercss"></a><span data-ttu-id="5fdc7-359">SignalR.Sample StockTicker.css</span><span class="sxs-lookup"><span data-stu-id="5fdc7-359">SignalR.Sample StockTicker.css</span></span>

<span data-ttu-id="5fdc7-360">股票行情 CSS 代码：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-360">The stock ticker CSS code:</span></span>

[!code-html[Main](tutorial-server-broadcast-with-signalr/samples/sample21.html)]

#### <a name="signalrsample-signalrstocktickerjs"></a><span data-ttu-id="5fdc7-361">SignalR.Sample SignalR.StockTicker.js</span><span class="sxs-lookup"><span data-stu-id="5fdc7-361">SignalR.Sample SignalR.StockTicker.js</span></span>

<span data-ttu-id="5fdc7-362">使它的 jQuery 代码滚动：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-362">The jQuery code that makes it scroll:</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample22.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a><span data-ttu-id="5fdc7-363">客户端可以调用的服务器上的其他方法</span><span class="sxs-lookup"><span data-stu-id="5fdc7-363">Additional methods on the server that the client can call</span></span>

<span data-ttu-id="5fdc7-364">若要向应用添加灵活性，还有一些其他应用程序可以调用的方法。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-364">To add flexibility to the app, there are additional methods the app can call.</span></span>

#### <a name="signalrsample-stocktickerhubcs"></a><span data-ttu-id="5fdc7-365">SignalR.Sample StockTickerHub.cs</span><span class="sxs-lookup"><span data-stu-id="5fdc7-365">SignalR.Sample StockTickerHub.cs</span></span>

<span data-ttu-id="5fdc7-366">`StockTickerHub`类定义了四个客户端可以调用的其他方法：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-366">The `StockTickerHub` class defines four additional methods that the client can call:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample23.cs)]

<span data-ttu-id="5fdc7-367">应用程序调用`OpenMarket`， `CloseMarket`，和`Reset`以响应在页面顶部的按钮。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-367">The app calls `OpenMarket`, `CloseMarket`, and `Reset` in response to the buttons at the top of the page.</span></span> <span data-ttu-id="5fdc7-368">它们演示了一个客户端触发立即传播到所有客户端的状态的更改的模式。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-368">They demonstrate the pattern of one client triggering a change in state immediately propagated to all clients.</span></span> <span data-ttu-id="5fdc7-369">每个方法调用中的方法`StockTicker`类，该类将导致市场状态更改，然后广播新状态。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-369">Each of these methods calls a method in the `StockTicker` class that causes the market state change and then broadcasts the new state.</span></span>

#### <a name="signalrsample-stocktickercs"></a><span data-ttu-id="5fdc7-370">SignalR.Sample StockTicker.cs</span><span class="sxs-lookup"><span data-stu-id="5fdc7-370">SignalR.Sample StockTicker.cs</span></span>

<span data-ttu-id="5fdc7-371">在中`StockTicker`类，该应用程序维护的状态与市场`MarketState`返回的属性`MarketState`枚举值：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-371">In the `StockTicker` class, the app maintains the state of the market with a `MarketState` property that returns a `MarketState` enum value:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample24.cs)]

<span data-ttu-id="5fdc7-372">每个更改市场状态的方法都这样做，在锁块的内部因为`StockTicker`类必须是线程安全：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-372">Each of the methods that change the market state do so inside a lock block because the `StockTicker` class has to be thread-safe:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample25.cs)]

<span data-ttu-id="5fdc7-373">若要确保此代码是线程安全`_marketState`支持字段`MarketState`属性指定`volatile`:</span><span class="sxs-lookup"><span data-stu-id="5fdc7-373">To make sure this code is thread-safe, the `_marketState` field that backs the `MarketState` property designated `volatile`:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample26.cs)]

<span data-ttu-id="5fdc7-374">`BroadcastMarketStateChange`和`BroadcastMarketReset`除它们调用不同的方法在客户端上定义外，方法是您已看到的 BroadcastStockPrice 方法相似：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-374">The `BroadcastMarketStateChange` and `BroadcastMarketReset` methods are similar to the BroadcastStockPrice method that you already saw, except they call different methods defined at the client:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-signalr/samples/sample27.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a><span data-ttu-id="5fdc7-375">服务器可以调用的客户端上的其他函数</span><span class="sxs-lookup"><span data-stu-id="5fdc7-375">Additional functions on the client that the server can call</span></span>

<span data-ttu-id="5fdc7-376">`updateStockPrice`函数现在可以处理表和行情自动收录器显示，并使用`jQuery.Color`闪烁红色和绿色的颜色。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-376">The `updateStockPrice` function now handles both the table and the ticker display, and it uses `jQuery.Color` to flash red and green colors.</span></span>

<span data-ttu-id="5fdc7-377">中的新函数*SignalR.StockTicker.js*启用和禁用基于市场状态的按钮。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-377">New functions in *SignalR.StockTicker.js* enable and disable the buttons based on market state.</span></span> <span data-ttu-id="5fdc7-378">它们还停止或启动**实时股票行情自动收录器**水平滚动。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-378">They also stop or start the **Live Stock Ticker** horizontal scrolling.</span></span> <span data-ttu-id="5fdc7-379">由于许多函数添加到`ticker.client`，此应用使用[jQuery 扩展函数](http://api.jquery.com/jQuery.extend/)以将其添加。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-379">Since many functions are being added to `ticker.client`, the app uses the [jQuery extend function](http://api.jquery.com/jQuery.extend/) to add them.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample28.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a><span data-ttu-id="5fdc7-380">建立连接后的其他客户端安装程序</span><span class="sxs-lookup"><span data-stu-id="5fdc7-380">Additional client setup after establishing the connection</span></span>

<span data-ttu-id="5fdc7-381">客户端会建立连接后，它具有一些附加工作以执行操作：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-381">After the client establishes the connection, it has some additional work to do:</span></span>

* <span data-ttu-id="5fdc7-382">找出市场是否打开或关闭调用适当`marketOpened`或`marketClosed`函数。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-382">Find out if the market is open or closed to call the appropriate `marketOpened` or `marketClosed` function.</span></span>

* <span data-ttu-id="5fdc7-383">将附加到按钮的服务器方法调用。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-383">Attach the server method calls to the buttons.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-signalr/samples/sample29.js)]

<span data-ttu-id="5fdc7-384">应用程序建立连接后，服务器方法不被绑定到之前的按钮。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-384">The server methods aren't wired up to the buttons until after the app establishes the connection.</span></span> <span data-ttu-id="5fdc7-385">它是使代码不能调用服务器方法，然后才能将可用。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-385">It's so the code can't call the server methods before they're available.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="5fdc7-386">其他资源</span><span class="sxs-lookup"><span data-stu-id="5fdc7-386">Additional resources</span></span>

<span data-ttu-id="5fdc7-387">在本教程中介绍了如何将消息从服务器广播到所有连接的客户端 SignalR 应用程序进行编程。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-387">In this tutorial you've learned how to program a SignalR application that broadcasts messages from the server to all connected clients.</span></span> <span data-ttu-id="5fdc7-388">现在可以将广播定期更新和以响应来自任何客户端的通知消息。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-388">Now you can broadcast messages on a periodic basis and in response to notifications from any client.</span></span> <span data-ttu-id="5fdc7-389">多线程的单一实例的概念可用于维护多玩家联机游戏方案中的服务器状态。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-389">You can use the concept of multi-threaded singleton instance to maintain server state in multi-player online game scenarios.</span></span> <span data-ttu-id="5fdc7-390">有关示例，请参阅[ShootR 游戏基于 SignalR](https://github.com/NTaylorMullen/ShootR)。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-390">For an example, see [the ShootR game based on SignalR](https://github.com/NTaylorMullen/ShootR).</span></span>

<span data-ttu-id="5fdc7-391">显示对等通信方案的教程，请参阅[SignalR 入门](introduction-to-signalr.md)并[使用 SignalR 实时更新](tutorial-high-frequency-realtime-with-signalr.md)。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-391">For tutorials that show peer-to-peer communication scenarios, see [Getting Started with SignalR](introduction-to-signalr.md) and [Real-Time Updating with SignalR](tutorial-high-frequency-realtime-with-signalr.md).</span></span>

<span data-ttu-id="5fdc7-392">有关 SignalR 的详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-392">For more about SignalR, see the following resources:</span></span>

* [<span data-ttu-id="5fdc7-393">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="5fdc7-393">ASP.NET SignalR</span></span>](../../index.md)
* [<span data-ttu-id="5fdc7-394">SignalR 项目</span><span class="sxs-lookup"><span data-stu-id="5fdc7-394">SignalR Project</span></span>](http://signalr.net/)
* [<span data-ttu-id="5fdc7-395">SignalR GitHub 和示例</span><span class="sxs-lookup"><span data-stu-id="5fdc7-395">SignalR GitHub and Samples</span></span>](https://github.com/SignalR/SignalR)
* [<span data-ttu-id="5fdc7-396">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="5fdc7-396">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)

## <a name="next-steps"></a><span data-ttu-id="5fdc7-397">后续步骤</span><span class="sxs-lookup"><span data-stu-id="5fdc7-397">Next steps</span></span>

<span data-ttu-id="5fdc7-398">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="5fdc7-398">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5fdc7-399">创建项目</span><span class="sxs-lookup"><span data-stu-id="5fdc7-399">Created the project</span></span>
> * <span data-ttu-id="5fdc7-400">设置服务器代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-400">Set up the server code</span></span>
> * <span data-ttu-id="5fdc7-401">检查服务器代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-401">Examined the server code</span></span>
> * <span data-ttu-id="5fdc7-402">设置客户端代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-402">Set up the client code</span></span>
> * <span data-ttu-id="5fdc7-403">检查客户端代码</span><span class="sxs-lookup"><span data-stu-id="5fdc7-403">Examined the client code</span></span>
> * <span data-ttu-id="5fdc7-404">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="5fdc7-404">Tested the application</span></span>
> * <span data-ttu-id="5fdc7-405">已启用日志记录</span><span class="sxs-lookup"><span data-stu-id="5fdc7-405">Enabled logging</span></span>

<span data-ttu-id="5fdc7-406">转到下一步的文章，了解如何创建使用 ASP.NET SignalR 2 的实时 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="5fdc7-406">Advance to the next article to learn how to create a real-time web application that uses ASP.NET SignalR 2.</span></span>
> [!div class="nextstepaction"]
> [<span data-ttu-id="5fdc7-407">使用 SignalR 创建实时 web 应用</span><span class="sxs-lookup"><span data-stu-id="5fdc7-407">Create real-time web app with SignalR</span></span>](real-time-web-applications-with-signalr.md)