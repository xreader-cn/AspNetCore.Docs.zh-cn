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
<a name="tutorial-server-broadcast-with-aspnet-signalr-1x"></a><span data-ttu-id="fcae7-104">教程：服务器广播使用 ASP.NET SignalR 1.x</span><span class="sxs-lookup"><span data-stu-id="fcae7-104">Tutorial: Server Broadcast with ASP.NET SignalR 1.x</span></span>
====================
<span data-ttu-id="fcae7-105">通过[Patrick Fletcher](https://github.com/pfletcher)， [Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="fcae7-105">by [Patrick Fletcher](https://github.com/pfletcher), [Tom Dykstra](https://github.com/tdykstra)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="fcae7-106">本教程演示如何创建使用 ASP.NET SignalR 来提供服务器广播的功能的 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="fcae7-106">This tutorial shows how to create a web application that uses ASP.NET SignalR to provide server broadcast functionality.</span></span> <span data-ttu-id="fcae7-107">服务器广播意味着发送到客户端的通信由服务器启动。</span><span class="sxs-lookup"><span data-stu-id="fcae7-107">Server broadcast means that communications sent to clients are initiated by the server.</span></span> <span data-ttu-id="fcae7-108">这种情况要求不同的编程方式对等方案，例如聊天应用程序，在其中发送到客户端的通信都是由一个或多个客户端。</span><span class="sxs-lookup"><span data-stu-id="fcae7-108">This scenario requires a different programming approach than peer-to-peer scenarios such as chat applications, in which communications sent to clients are initiated by one or more of the clients.</span></span>
> 
> <span data-ttu-id="fcae7-109">你将在本教程中创建的应用程序模拟股票行情服务器广播功能的典型方案。</span><span class="sxs-lookup"><span data-stu-id="fcae7-109">The application that you'll create in this tutorial simulates a stock ticker, a typical scenario for server broadcast functionality.</span></span>
> 
> <span data-ttu-id="fcae7-110">在本教程的注释是欢迎使用。</span><span class="sxs-lookup"><span data-stu-id="fcae7-110">Comments on the tutorial are welcome.</span></span> <span data-ttu-id="fcae7-111">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com)。</span><span class="sxs-lookup"><span data-stu-id="fcae7-111">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com).</span></span>


## <a name="overview"></a><span data-ttu-id="fcae7-112">概述</span><span class="sxs-lookup"><span data-stu-id="fcae7-112">Overview</span></span>

<span data-ttu-id="fcae7-113">[Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) NuGet 包安装 Visual Studio 项目中的示例模拟股票行情自动收录器应用程序。</span><span class="sxs-lookup"><span data-stu-id="fcae7-113">The [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) NuGet package installs a sample simulated stock ticker application in a Visual Studio project.</span></span> <span data-ttu-id="fcae7-114">在本教程的第一部分，将从零开始创建该应用程序的简化的版本。</span><span class="sxs-lookup"><span data-stu-id="fcae7-114">In the first part of this tutorial, you'll create a simplified version of that application from scratch.</span></span> <span data-ttu-id="fcae7-115">在本教程的其余部分，将安装 NuGet 包，查看其他功能和它将创建的代码。</span><span class="sxs-lookup"><span data-stu-id="fcae7-115">In the remainder of the tutorial, you'll install the NuGet package and review the additional features and code that it creates.</span></span>

<span data-ttu-id="fcae7-116">股票行情自动收录器应用程序是代表要在其中定期"推送"或广播，将通知从服务器到所有连接的客户端的实时应用程序的一种。</span><span class="sxs-lookup"><span data-stu-id="fcae7-116">The stock ticker application is a representative of a kind of real-time application in which you want to periodically "push," or broadcast, notifications from the server to all connected clients.</span></span>

<span data-ttu-id="fcae7-117">在本教程的第一部分中将生成的应用程序显示的网格有库存数据。</span><span class="sxs-lookup"><span data-stu-id="fcae7-117">The application that you'll build in the first part of this tutorial displays a grid with stock data.</span></span>

![StockTicker 初始版本](tutorial-server-broadcast-with-aspnet-signalr/_static/image1.png)

<span data-ttu-id="fcae7-119">定期服务器随机更新股票价格，并将更新推送到所有连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="fcae7-119">Periodically the server randomly updates stock prices and pushes the updates to all connected clients.</span></span> <span data-ttu-id="fcae7-120">在浏览器数字和中的符号 **更改** 并 **%** 列动态更改以响应来自服务器的通知。</span><span class="sxs-lookup"><span data-stu-id="fcae7-120">In the browser the numbers and symbols in the **Change** and **%** columns dynamically change in response to notifications from the server.</span></span> <span data-ttu-id="fcae7-121">如果您打开其他浏览器相同的 URL，它们都同时显示相同的数据和对数据的相同更改。</span><span class="sxs-lookup"><span data-stu-id="fcae7-121">If you open additional browsers to the same URL, they all show the same data and the same changes to the data simultaneously.</span></span>

<span data-ttu-id="fcae7-122">本教程包含以下各节：</span><span class="sxs-lookup"><span data-stu-id="fcae7-122">This tutorial contains the following sections:</span></span>

- [<span data-ttu-id="fcae7-123">系统必备</span><span class="sxs-lookup"><span data-stu-id="fcae7-123">Prerequisites</span></span>](#prerequisites)
- [<span data-ttu-id="fcae7-124">创建项目</span><span class="sxs-lookup"><span data-stu-id="fcae7-124">Create the project</span></span>](#createproject)
- [<span data-ttu-id="fcae7-125">将 SignalR NuGet 包添加</span><span class="sxs-lookup"><span data-stu-id="fcae7-125">Add the SignalR NuGet packages</span></span>](#nugetpackages)
- [<span data-ttu-id="fcae7-126">设置服务器代码</span><span class="sxs-lookup"><span data-stu-id="fcae7-126">Set up the server code</span></span>](#server)
- [<span data-ttu-id="fcae7-127">设置客户端代码</span><span class="sxs-lookup"><span data-stu-id="fcae7-127">Set up the client code</span></span>](#client)
- [<span data-ttu-id="fcae7-128">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="fcae7-128">Test the application</span></span>](#test)
- [<span data-ttu-id="fcae7-129">启用日志记录</span><span class="sxs-lookup"><span data-stu-id="fcae7-129">Enable logging</span></span>](#enablelogging)
- [<span data-ttu-id="fcae7-130">安装和查看完整的 StockTicker 示例</span><span class="sxs-lookup"><span data-stu-id="fcae7-130">Install and review the full StockTicker sample</span></span>](#fullsample)
- [<span data-ttu-id="fcae7-131">后续步骤</span><span class="sxs-lookup"><span data-stu-id="fcae7-131">Next steps</span></span>](#nextsteps)

> [!NOTE]
> <span data-ttu-id="fcae7-132">如果不想要逐步完成构建应用程序的步骤，则可以在新安装 SignalR.Sample 包**空的 ASP.NET Web 应用程序**项目，并通读以下步骤获取代码的说明。</span><span class="sxs-lookup"><span data-stu-id="fcae7-132">If you don't want to work through the steps of building the application, you can install the SignalR.Sample package in a new **Empty ASP.NET Web Application** project, and read through these steps to get explanations of the code.</span></span> <span data-ttu-id="fcae7-133">本教程的第一个部分涵盖子集 SignalR.Sample 代码，并且第二部分介绍 SignalR.Sample 包中的其他功能的主要功能。</span><span class="sxs-lookup"><span data-stu-id="fcae7-133">The first part of the tutorial covers a subset of the SignalR.Sample code, and the second part explains key features of the additional functionality in the SignalR.Sample package.</span></span>


<a id="prerequisites"></a>

## <a name="prerequisites"></a><span data-ttu-id="fcae7-134">系统必备</span><span class="sxs-lookup"><span data-stu-id="fcae7-134">Prerequisites</span></span>

<span data-ttu-id="fcae7-135">在开始之前，请确保安装 Visual Studio 2012 或 2010 SP1 安装在您的计算机上。</span><span class="sxs-lookup"><span data-stu-id="fcae7-135">Before you start, make sure that you have Visual Studio 2012 or 2010 SP1 installed on your computer.</span></span> <span data-ttu-id="fcae7-136">如果未安装 Visual Studio，请参阅[ASP.NET 下载](https://www.asp.net/downloads)以获取免费的 Visual Studio 2012 Express Web。</span><span class="sxs-lookup"><span data-stu-id="fcae7-136">If you don't have Visual Studio, see [ASP.NET Downloads](https://www.asp.net/downloads) to get the free Visual Studio 2012 Express for Web.</span></span>

<span data-ttu-id="fcae7-137">如果你有 Visual Studio 2010，请确保[NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c)安装。</span><span class="sxs-lookup"><span data-stu-id="fcae7-137">If you have Visual Studio 2010, make sure that [NuGet](https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c) is installed.</span></span>

<a id="createproject"></a>

## <a name="create-the-project"></a><span data-ttu-id="fcae7-138">创建项目</span><span class="sxs-lookup"><span data-stu-id="fcae7-138">Create the project</span></span>

1. <span data-ttu-id="fcae7-139">从**文件**菜单上，单击**新项目**。</span><span class="sxs-lookup"><span data-stu-id="fcae7-139">From the **File** menu click **New Project**.</span></span>
2. <span data-ttu-id="fcae7-140">在中**新的项目**对话框框中，展开**C#** 下**模板**，然后选择**Web**。</span><span class="sxs-lookup"><span data-stu-id="fcae7-140">In the **New Project** dialog box, expand **C#** under **Templates** and select **Web**.</span></span>
3. <span data-ttu-id="fcae7-141">选择**ASP.NET 空 Web 应用程序**模板，将项目命名*SignalR.StockTicker*，然后单击**确定**。</span><span class="sxs-lookup"><span data-stu-id="fcae7-141">Select the **ASP.NET Empty Web Application** template, name the project *SignalR.StockTicker*, and click **OK**.</span></span>

    ![“新建项目”对话框](tutorial-server-broadcast-with-aspnet-signalr/_static/image2.png)

<a id="nugetpackages"></a>

## <a name="add-the-signalr-nuget-packages"></a><span data-ttu-id="fcae7-143">将 SignalR NuGet 包添加</span><span class="sxs-lookup"><span data-stu-id="fcae7-143">Add the SignalR NuGet Packages</span></span>

### <a name="add-the-signalr-and-jquery-nuget-packages"></a><span data-ttu-id="fcae7-144">添加的 SignalR 和 JQuery NuGet 包</span><span class="sxs-lookup"><span data-stu-id="fcae7-144">Add the SignalR and JQuery NuGet Packages</span></span>

<span data-ttu-id="fcae7-145">通过安装 NuGet 包，可以将 SignalR 功能添加到项目。</span><span class="sxs-lookup"><span data-stu-id="fcae7-145">You can add SignalR functionality to a project by installing a NuGet package.</span></span>

1. <span data-ttu-id="fcae7-146">单击**工具 |NuGet 包管理器 |包管理器控制台**。</span><span class="sxs-lookup"><span data-stu-id="fcae7-146">Click **Tools | NuGet Package Manager | Package Manager Console**.</span></span>
2. <span data-ttu-id="fcae7-147">在包管理器中输入以下命令。</span><span class="sxs-lookup"><span data-stu-id="fcae7-147">Enter the following command in the package manager.</span></span>

    [!code-powershell[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample1.ps1)]

    <span data-ttu-id="fcae7-148">SignalR 包作为依赖项安装其他 NuGet 包的数。</span><span class="sxs-lookup"><span data-stu-id="fcae7-148">The SignalR package installs a number of other NuGet packages as dependencies.</span></span> <span data-ttu-id="fcae7-149">完成安装后必须全部所需的 ASP.NET 应用程序中使用 SignalR 的服务器和客户端组件。</span><span class="sxs-lookup"><span data-stu-id="fcae7-149">When the installation is finished you have all of the server and client components required to use SignalR in an ASP.NET application.</span></span>

<a id="server"></a>

## <a name="set-up-the-server-code"></a><span data-ttu-id="fcae7-150">设置服务器代码</span><span class="sxs-lookup"><span data-stu-id="fcae7-150">Set up the server code</span></span>

<span data-ttu-id="fcae7-151">在本部分中，设置在服务器运行的代码。</span><span class="sxs-lookup"><span data-stu-id="fcae7-151">In this section you set up the code that runs on the server.</span></span>

### <a name="create-the-stock-class"></a><span data-ttu-id="fcae7-152">创建 Stock 类</span><span class="sxs-lookup"><span data-stu-id="fcae7-152">Create the Stock class</span></span>

<span data-ttu-id="fcae7-153">首先创建将用于存储和传输有关股票的信息的股票模型类。</span><span class="sxs-lookup"><span data-stu-id="fcae7-153">You begin by creating the Stock model class that you'll use to store and transmit information about a stock.</span></span>

1. <span data-ttu-id="fcae7-154">在项目文件夹中创建一个新类文件，将其命名*Stock.cs*，然后使用以下代码替换模板代码：</span><span class="sxs-lookup"><span data-stu-id="fcae7-154">Create a new class file in the project folder, name it *Stock.cs*, and then replace the template code with the following code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample2.cs)]

    <span data-ttu-id="fcae7-155">在创建股票时将设置的两个属性是符号 (例如，Microsoft 的 MSFT) 和价格。</span><span class="sxs-lookup"><span data-stu-id="fcae7-155">The two properties that you'll set when you create stocks are the Symbol (for example, MSFT for Microsoft) and the Price.</span></span> <span data-ttu-id="fcae7-156">其他属性取决于如何以及何时设置价格。</span><span class="sxs-lookup"><span data-stu-id="fcae7-156">The other properties depend on how and when you set Price.</span></span> <span data-ttu-id="fcae7-157">设置价格，第一次的值获取传播到 DayOpen。</span><span class="sxs-lookup"><span data-stu-id="fcae7-157">The first time you set Price, the value gets propagated to DayOpen.</span></span> <span data-ttu-id="fcae7-158">当设置价格，更改并且 PercentChange 属性值进行计算的后续时间基于价格和 DayOpen 之间的差异。</span><span class="sxs-lookup"><span data-stu-id="fcae7-158">Subsequent times when you set Price, the Change and PercentChange property values are calculated based on the difference between Price and DayOpen.</span></span>

### <a name="create-the-stockticker-and-stocktickerhub-classes"></a><span data-ttu-id="fcae7-159">创建在 StockTicker 和 StockTickerHub 类</span><span class="sxs-lookup"><span data-stu-id="fcae7-159">Create the StockTicker and StockTickerHub classes</span></span>

<span data-ttu-id="fcae7-160">SignalR 中心 API 将用于处理服务器到客户端交互。</span><span class="sxs-lookup"><span data-stu-id="fcae7-160">You'll use the SignalR Hub API to handle server-to-client interaction.</span></span> <span data-ttu-id="fcae7-161">从 SignalR Hub 类派生的 StockTickerHub 类将处理来自客户端接收连接和方法调用。</span><span class="sxs-lookup"><span data-stu-id="fcae7-161">A StockTickerHub class that derives from the SignalR Hub class will handle receiving connections and method calls from clients.</span></span> <span data-ttu-id="fcae7-162">此外需要维护股票数据并运行计时器对象，以定期触发价格更新，独立于客户端连接。</span><span class="sxs-lookup"><span data-stu-id="fcae7-162">You also need to maintain stock data and run a Timer object to periodically trigger price updates, independently of client connections.</span></span> <span data-ttu-id="fcae7-163">因为中心实例是瞬态的不能置于 Hub 类，这些函数。</span><span class="sxs-lookup"><span data-stu-id="fcae7-163">You can't put these functions in a Hub class, because Hub instances are transient.</span></span> <span data-ttu-id="fcae7-164">为每个操作的中心，例如连接和从客户端对服务器的调用创建的中心类实例。</span><span class="sxs-lookup"><span data-stu-id="fcae7-164">A Hub class instance is created for each operation on the hub, such as connections and calls from the client to the server.</span></span> <span data-ttu-id="fcae7-165">因此，必须运行在单独的类，您将命名为 StockTicker 的机制，可保护库存数据，更新价格，并会广播价格更新。</span><span class="sxs-lookup"><span data-stu-id="fcae7-165">So the mechanism that keeps stock data, updates prices, and broadcasts the price updates has to run in a separate class, which you'll name StockTicker.</span></span>

![从 StockTicker 的广播](tutorial-server-broadcast-with-aspnet-signalr/_static/image4.png)

<span data-ttu-id="fcae7-167">您只想要运行的服务器上，因此将需要的单个 StockTicker 实例中设置从每个 StockTickerHub 实例引用的 StockTicker 类的一个实例。</span><span class="sxs-lookup"><span data-stu-id="fcae7-167">You only want one instance of the StockTicker class to run on the server, so you'll need to set up a reference from each StockTickerHub instance to the singleton StockTicker instance.</span></span> <span data-ttu-id="fcae7-168">StockTicker 类必须可以广播到客户端，因为它具有股票数据并触发更新，但 StockTicker 不是 Hub 类。</span><span class="sxs-lookup"><span data-stu-id="fcae7-168">The StockTicker class has to be able to broadcast to clients because it has the stock data and triggers updates, but StockTicker is not a Hub class.</span></span> <span data-ttu-id="fcae7-169">因此，StockTicker 类必须获取对 SignalR Hub 连接上下文对象的引用。</span><span class="sxs-lookup"><span data-stu-id="fcae7-169">Therefore, the StockTicker class has to get a reference to the SignalR Hub connection context object.</span></span> <span data-ttu-id="fcae7-170">然后，它可以使用 SignalR 连接上下文对象广播到客户端。</span><span class="sxs-lookup"><span data-stu-id="fcae7-170">It can then use the SignalR connection context object to broadcast to clients.</span></span>

1. <span data-ttu-id="fcae7-171">在中**解决方案资源管理器**，右键单击项目，然后单击**添加新项**。</span><span class="sxs-lookup"><span data-stu-id="fcae7-171">In **Solution Explorer**, right-click the project and click **Add New Item**.</span></span>
2. <span data-ttu-id="fcae7-172">如果你有 Visual Studio 2012 [ASP.NET 和 Web Tools 2012.2 更新](https://go.microsoft.com/fwlink/?LinkId=279941)，单击**Web**下**Visual C#** ，然后选择**SignalR Hub 类**项模板。</span><span class="sxs-lookup"><span data-stu-id="fcae7-172">If you have Visual Studio 2012 with the [ASP.NET and Web Tools 2012.2 Update](https://go.microsoft.com/fwlink/?LinkId=279941), click **Web** under **Visual C#** and select the **SignalR Hub Class** item template.</span></span> <span data-ttu-id="fcae7-173">否则，请选择**类**模板。</span><span class="sxs-lookup"><span data-stu-id="fcae7-173">Otherwise, select the **Class** template.</span></span>
3. <span data-ttu-id="fcae7-174">将新类命名*StockTickerHub.cs*，然后单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="fcae7-174">Name the new class *StockTickerHub.cs*, and then click **Add**.</span></span>

    ![Add StockTickerHub.cs](tutorial-server-broadcast-with-aspnet-signalr/_static/image5.png)
4. <span data-ttu-id="fcae7-176">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="fcae7-176">Replace the template code with the following code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample3.cs)]

    <span data-ttu-id="fcae7-177">[中心](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx)类用于定义客户端可以调用在服务器的方法。</span><span class="sxs-lookup"><span data-stu-id="fcae7-177">The [Hub](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hub(v=vs.111).aspx) class is used to define methods the clients can call on the server.</span></span> <span data-ttu-id="fcae7-178">要定义一种方法： `GetAllStocks()`。</span><span class="sxs-lookup"><span data-stu-id="fcae7-178">You are defining one method: `GetAllStocks()`.</span></span> <span data-ttu-id="fcae7-179">当客户端最初连接到服务器时，它将调用此方法以获取所有其当前价格股票的列表。</span><span class="sxs-lookup"><span data-stu-id="fcae7-179">When a client initially connects to the server, it will call this method to get a list of all of the stocks with their current prices.</span></span> <span data-ttu-id="fcae7-180">该方法可以同步执行并返回`IEnumerable<Stock>`由于它会从内存中返回数据。</span><span class="sxs-lookup"><span data-stu-id="fcae7-180">The method can execute synchronously and return `IEnumerable<Stock>` because it is returning data from memory.</span></span> <span data-ttu-id="fcae7-181">如果该方法必须通过执行将涉及等待，如数据库查找或 web 服务调用，获取的数据则会指定`Task<IEnumerable<Stock>>`作为返回值以启用异步处理。</span><span class="sxs-lookup"><span data-stu-id="fcae7-181">If the method had to get the data by doing something that would involve waiting, such as a database lookup or a web service call, you would specify `Task<IEnumerable<Stock>>` as the return value to enable asynchronous processing.</span></span> <span data-ttu-id="fcae7-182">有关详细信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-时要异步执行](index.md)。</span><span class="sxs-lookup"><span data-stu-id="fcae7-182">For more information, see [ASP.NET SignalR Hubs API Guide - Server - When to execute asynchronously](index.md).</span></span>

    <span data-ttu-id="fcae7-183">HubName 属性指定将如何在客户端上的 JavaScript 代码中引用该中心。</span><span class="sxs-lookup"><span data-stu-id="fcae7-183">The HubName attribute specifies how the Hub will be referenced in JavaScript code on the client.</span></span> <span data-ttu-id="fcae7-184">如果不使用此属性在客户端上的默认名称是类名称，在这种情况下是 stockTickerHub 驼峰式大小写版本。</span><span class="sxs-lookup"><span data-stu-id="fcae7-184">The default name on the client if you don't use this attribute is a camel-cased version of the class name, which in this case would be stockTickerHub.</span></span>

    <span data-ttu-id="fcae7-185">正如您将看到更高版本创建 StockTicker 类时，在其静态实例属性中创建该类的单一实例。</span><span class="sxs-lookup"><span data-stu-id="fcae7-185">As you'll see later when you create the StockTicker class, a singleton instance of that class is created in its static Instance property.</span></span> <span data-ttu-id="fcae7-186">StockTicker 的单一实例保持在多少个客户端连接或断开连接，无论内存中，该实例是 GetAllStocks 方法用于返回当前股票信息。</span><span class="sxs-lookup"><span data-stu-id="fcae7-186">That singleton instance of StockTicker remains in memory no matter how many clients connect or disconnect, and that instance is what the GetAllStocks method uses to return current stock information.</span></span>
5. <span data-ttu-id="fcae7-187">在项目文件夹中创建一个新类文件，将其命名*StockTicker.cs*，然后使用以下代码替换模板代码：</span><span class="sxs-lookup"><span data-stu-id="fcae7-187">Create a new class file in the project folder, name it *StockTicker.cs*, and then replace the template code with the following code:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample4.cs)]

    <span data-ttu-id="fcae7-188">由于多个线程将运行 StockTicker 代码的同一个实例，StockTicker 类必须是 threadsafe。</span><span class="sxs-lookup"><span data-stu-id="fcae7-188">Since multiple threads will be running the same instance of StockTicker code, the StockTicker class has to be threadsafe.</span></span>

    ### <a name="storing-the-singleton-instance-in-a-static-field"></a><span data-ttu-id="fcae7-189">在静态字段中存储的单一实例</span><span class="sxs-lookup"><span data-stu-id="fcae7-189">Storing the singleton instance in a static field</span></span>

    <span data-ttu-id="fcae7-190">此代码初始化静态\_支持的类，并且此实例的实例属性的实例字段是可以创建类的唯一实例，因为构造函数标记为私有。</span><span class="sxs-lookup"><span data-stu-id="fcae7-190">The code initializes the static \_instance field that backs the Instance property with an instance of the class, and this is the only instance of the class that can be created, because the constructor is marked as private.</span></span> <span data-ttu-id="fcae7-191">[迟缓初始化](https://msdn.microsoft.com/library/dd997286.aspx)用于\_实例字段，不出于性能原因，但若要确保创建的实例是 threadsafe。</span><span class="sxs-lookup"><span data-stu-id="fcae7-191">[Lazy initialization](https://msdn.microsoft.com/library/dd997286.aspx) is used for the \_instance field, not for performance reasons but to ensure that the instance creation is threadsafe.</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample5.cs)]

    <span data-ttu-id="fcae7-192">客户端连接到服务器，每次在一个单独的线程中运行的 StockTickerHub 类的新实例获取的 StockTicker 单一实例从 StockTicker.Instance 静态属性，如前面在 StockTickerHub 类中看到。</span><span class="sxs-lookup"><span data-stu-id="fcae7-192">Each time a client connects to the server, a new instance of the StockTickerHub class running in a separate thread gets the StockTicker singleton instance from the StockTicker.Instance static property, as you saw earlier in the StockTickerHub class.</span></span>

    ### <a name="storing-stock-data-in-a-concurrentdictionary"></a><span data-ttu-id="fcae7-193">在 ConcurrentDictionary 中存储常用的数据</span><span class="sxs-lookup"><span data-stu-id="fcae7-193">Storing stock data in a ConcurrentDictionary</span></span>

    <span data-ttu-id="fcae7-194">构造函数初始化\_具有一些示例股票数据和 GetAllStocks 股票集合返回股票。</span><span class="sxs-lookup"><span data-stu-id="fcae7-194">The constructor initializes the \_stocks collection with some sample stock data, and GetAllStocks returns the stocks.</span></span> <span data-ttu-id="fcae7-195">如上文所，StockTickerHub.GetAllStocks 这是客户端可以调用的中心类中的服务器方法又会返回股票的此集合。</span><span class="sxs-lookup"><span data-stu-id="fcae7-195">As you saw earlier, this collection of stocks is in turn returned by StockTickerHub.GetAllStocks which is a server method in the Hub class that clients can call.</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample6.cs)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample7.cs)]

    <span data-ttu-id="fcae7-196">股票集合指[ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx)线程安全的类型。</span><span class="sxs-lookup"><span data-stu-id="fcae7-196">The stocks collection is defined as a [ConcurrentDictionary](https://msdn.microsoft.com/library/dd287191.aspx) type for thread safety.</span></span> <span data-ttu-id="fcae7-197">作为替代方法，您可以使用[字典](https://msdn.microsoft.com/library/xfhwa508.aspx)对象，并显式锁定字典时对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="fcae7-197">As an alternative, you could use a [Dictionary](https://msdn.microsoft.com/library/xfhwa508.aspx) object and explicitly lock the dictionary when you make changes to it.</span></span>

    <span data-ttu-id="fcae7-198">对于此示例应用程序，它是确定在内存中存储应用程序数据并释放 StockTicker 实例时丢失数据。</span><span class="sxs-lookup"><span data-stu-id="fcae7-198">For this sample application, it's OK to store application data in memory and to lose the data when the StockTicker instance is disposed.</span></span> <span data-ttu-id="fcae7-199">在实际的应用程序将如何使用后端数据存储，如数据库。</span><span class="sxs-lookup"><span data-stu-id="fcae7-199">In a real application you would work with a back-end data store such as a database.</span></span>

    ### <a name="periodically-updating-stock-prices"></a><span data-ttu-id="fcae7-200">定期更新股票价格</span><span class="sxs-lookup"><span data-stu-id="fcae7-200">Periodically updating stock prices</span></span>

    <span data-ttu-id="fcae7-201">构造函数启动定期调用的方法，更新基于随机的股票价格的计时器对象。</span><span class="sxs-lookup"><span data-stu-id="fcae7-201">The constructor starts up a Timer object that periodically calls methods that update stock prices on a random basis.</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample8.cs)]

    <span data-ttu-id="fcae7-202">UpdateStockPrices 由计时器，该参数中的 null 将传入调用。</span><span class="sxs-lookup"><span data-stu-id="fcae7-202">UpdateStockPrices is called by the Timer, which passes in null in the state parameter.</span></span> <span data-ttu-id="fcae7-203">在更新之前的价格，采用锁\_updateStockPricesLock 对象。</span><span class="sxs-lookup"><span data-stu-id="fcae7-203">Before updating prices, a lock is taken on the \_updateStockPricesLock object.</span></span> <span data-ttu-id="fcae7-204">如果另一个线程已更新的价格，，然后在列表中每个股票调用 TryUpdateStockPrice 会检查代码。</span><span class="sxs-lookup"><span data-stu-id="fcae7-204">The code checks if another thread is already updating prices, and then it calls TryUpdateStockPrice on each stock in the list.</span></span> <span data-ttu-id="fcae7-205">TryUpdateStockPrice 方法决定是否要更改的股票价格和要对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="fcae7-205">The TryUpdateStockPrice method decides whether to change the stock price, and how much to change it.</span></span> <span data-ttu-id="fcae7-206">如果股票价格已更改，调用 BroadcastStockPrice 广播到所有连接的客户端进行的股票价格更改。</span><span class="sxs-lookup"><span data-stu-id="fcae7-206">If the stock price is changed, BroadcastStockPrice is called to broadcast the stock price change to all connected clients.</span></span>

    <span data-ttu-id="fcae7-207">\_UpdatingStockPrices 标志标记为[易失性](https://msdn.microsoft.com/library/x13ttww7.aspx)以确保进行 threadsafe 到它的访问。</span><span class="sxs-lookup"><span data-stu-id="fcae7-207">The \_updatingStockPrices flag is marked as [volatile](https://msdn.microsoft.com/library/x13ttww7.aspx) to ensure that access to it is threadsafe.</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample9.cs)]

    <span data-ttu-id="fcae7-208">在实际的应用程序，TryUpdateStockPrice 方法需要调用 web 服务，以便查找价格上涨。它在此代码中使用的随机数生成器随机进行更改。</span><span class="sxs-lookup"><span data-stu-id="fcae7-208">In a real application, the TryUpdateStockPrice method would call a web service to look up the price; in this code it uses a random number generator to make changes randomly.</span></span>

    ### <a name="getting-the-signalr-context-so-that-the-stockticker-class-can-broadcast-to-clients"></a><span data-ttu-id="fcae7-209">获取 SignalR 上下文，以便 StockTicker 类可以广播到客户端</span><span class="sxs-lookup"><span data-stu-id="fcae7-209">Getting the SignalR context so that the StockTicker class can broadcast to clients</span></span>

    <span data-ttu-id="fcae7-210">因为价格更改来自此处 StockTicker 对象，所以这是需要在所有连接的客户端上调用 updateStockPrice 方法的对象。</span><span class="sxs-lookup"><span data-stu-id="fcae7-210">Because the price changes originate here in the StockTicker object, this is the object that needs to call an updateStockPrice method on all connected clients.</span></span> <span data-ttu-id="fcae7-211">用于调用客户端的方法，Hub 类中有一个 API，但 StockTicker 不从 Hub 类派生，但尚未对任何集线器对象的引用。</span><span class="sxs-lookup"><span data-stu-id="fcae7-211">In a Hub class you have an API for calling client methods, but StockTicker does not derive from the Hub class and does not have a reference to any Hub object.</span></span> <span data-ttu-id="fcae7-212">因此，若要将广播到连接的客户端，StockTicker 类必须 StockTickerHub 类获取 SignalR 上下文实例并使用它来在客户端上调用方法。</span><span class="sxs-lookup"><span data-stu-id="fcae7-212">Therefore, in order to broadcast to connected clients, the StockTicker class has to get the SignalR context instance for the StockTickerHub class and use that to call methods on clients.</span></span>

    <span data-ttu-id="fcae7-213">该代码获取对 SignalR 上下文的引用时创建的单一类实例，将引用传递给构造函数中，并构造函数将其放入客户端属性。</span><span class="sxs-lookup"><span data-stu-id="fcae7-213">The code gets a reference to the SignalR context when it creates the singleton class instance, passes that reference to the constructor, and the constructor puts it in the Clients property.</span></span>

    <span data-ttu-id="fcae7-214">有两个原因你希望只需一次获取上下文的理由： 获取上下文是代价高昂的操作，并一次获取将确保消息发送到客户端的预期的顺序得到保留。</span><span class="sxs-lookup"><span data-stu-id="fcae7-214">There are two reasons why you want to get the context just once: getting the context is an expensive operation, and getting it once ensures that the intended order of messages sent to clients is preserved.</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample10.cs)]

    <span data-ttu-id="fcae7-215">获取上下文的客户端属性并将其置于 StockTickerClient 属性中可编写代码来调用方法的客户端中的中心类一样外观相同。</span><span class="sxs-lookup"><span data-stu-id="fcae7-215">Getting the Clients property of the context and putting it in the StockTickerClient property lets you write code to call client methods that looks the same as it would in a Hub class.</span></span> <span data-ttu-id="fcae7-216">例如，若要将广播到所有客户端可以编写 Clients.All.updateStockPrice(stock)。</span><span class="sxs-lookup"><span data-stu-id="fcae7-216">For instance, to broadcast to all clients you can write Clients.All.updateStockPrice(stock).</span></span>

    <span data-ttu-id="fcae7-217">在调用 BroadcastStockPrice updateStockPrice 方法尚不存在;在编写客户端上运行的代码时，你将从更高版本添加它。</span><span class="sxs-lookup"><span data-stu-id="fcae7-217">The updateStockPrice method that you are calling in BroadcastStockPrice doesn't exist yet; you'll add it later when you write code that runs on the client.</span></span> <span data-ttu-id="fcae7-218">因为 Clients.All 是动态的这意味着将在运行时计算该表达式可以引用 updateStockPrice 此处。</span><span class="sxs-lookup"><span data-stu-id="fcae7-218">You can refer to updateStockPrice here because Clients.All is dynamic, which means the expression will be evaluated at runtime.</span></span> <span data-ttu-id="fcae7-219">当此方法调用执行时，SignalR 将发送方法名称和参数值到客户端，并在客户端有一个名为 updateStockPrice 方法，将调用该方法和参数值将传递给它。</span><span class="sxs-lookup"><span data-stu-id="fcae7-219">When this method call executes, SignalR will send the method name and the parameter value to the client, and if the client has a method named updateStockPrice, that method will be called and the parameter value will be passed to it.</span></span>

    <span data-ttu-id="fcae7-220">Clients.All 意味着将发送到所有客户端。</span><span class="sxs-lookup"><span data-stu-id="fcae7-220">Clients.All means send to all clients.</span></span> <span data-ttu-id="fcae7-221">SignalR 提供其他选项来指定哪些客户端或客户端将发送到的组。</span><span class="sxs-lookup"><span data-stu-id="fcae7-221">SignalR gives you other options to specify which clients or groups of clients to send to.</span></span> <span data-ttu-id="fcae7-222">有关详细信息，请参阅[HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx)。</span><span class="sxs-lookup"><span data-stu-id="fcae7-222">For more information, see [HubConnectionContext](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubs.hubconnectioncontext(v=vs.111).aspx).</span></span>

### <a name="register-the-signalr-route"></a><span data-ttu-id="fcae7-223">注册 SignalR 路由</span><span class="sxs-lookup"><span data-stu-id="fcae7-223">Register the SignalR route</span></span>

<span data-ttu-id="fcae7-224">服务器需要知道要截获并将定向到 SignalR 的 URL。</span><span class="sxs-lookup"><span data-stu-id="fcae7-224">The server needs to know which URL to intercept and direct to SignalR.</span></span> <span data-ttu-id="fcae7-225">若要执行操作，将添加一些代码以*Global.asax*文件。</span><span class="sxs-lookup"><span data-stu-id="fcae7-225">To do that you'll add some code to the *Global.asax* file.</span></span>

1. <span data-ttu-id="fcae7-226">在中**解决方案资源管理器**，右键单击该项目，然后单击**添加新项**。</span><span class="sxs-lookup"><span data-stu-id="fcae7-226">In **Solution Explorer**, right-click the project, and then click **Add New Item**.</span></span>
2. <span data-ttu-id="fcae7-227">选择**全局应用程序类**项目模板，然后单击**添加**。</span><span class="sxs-lookup"><span data-stu-id="fcae7-227">Select the **Global Application Class** item template, and then click **Add**.</span></span>

    ![添加 global.asax](tutorial-server-broadcast-with-aspnet-signalr/_static/image6.png)
3. <span data-ttu-id="fcae7-229">将 SignalR 路由注册代码添加到应用程序\_Start 方法：</span><span class="sxs-lookup"><span data-stu-id="fcae7-229">Add the SignalR route registration code to the Application\_Start method:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample11.cs)]

    <span data-ttu-id="fcae7-230">默认情况下，所有 SignalR 流量的基 URL 是"/ signalr"，并"/ signalr/中心"用于检索一个动态生成的 JavaScript 文件，定义用于应用程序中包含的所有集线器代理。</span><span class="sxs-lookup"><span data-stu-id="fcae7-230">By default, the base URL for all SignalR traffic is "/signalr", and "/signalr/hubs" is used to retrieve a dynamically generated JavaScript file that defines proxies for all the Hubs you have in your application.</span></span> <span data-ttu-id="fcae7-231">MapHubs 方法包括让您可以指定不同的基 URL 和某些 SignalR 选项中的一个实例的重载[HubConfiguration](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubconfiguration(v=vs.111).aspx)类。</span><span class="sxs-lookup"><span data-stu-id="fcae7-231">The MapHubs method includes overloads that let you specify a different base URL and certain SignalR options in an instance of the [HubConfiguration](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.hubconfiguration(v=vs.111).aspx) class.</span></span>
4. <span data-ttu-id="fcae7-232">添加 using 语句的文件的顶部：</span><span class="sxs-lookup"><span data-stu-id="fcae7-232">Add a using statement at the top of the file:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample12.cs)]
5. <span data-ttu-id="fcae7-233">保存并关闭*Global.asax*文件，并生成项目。</span><span class="sxs-lookup"><span data-stu-id="fcae7-233">Save and close the *Global.asax* file, and build the project.</span></span>

<span data-ttu-id="fcae7-234">现在已经完成设置服务器代码。</span><span class="sxs-lookup"><span data-stu-id="fcae7-234">You have now completed setting up the server code.</span></span> <span data-ttu-id="fcae7-235">下一节中将设置客户端。</span><span class="sxs-lookup"><span data-stu-id="fcae7-235">In the next section you'll set up the client.</span></span>

<a id="client"></a>

## <a name="set-up-the-client-code"></a><span data-ttu-id="fcae7-236">设置客户端代码</span><span class="sxs-lookup"><span data-stu-id="fcae7-236">Set up the client code</span></span>

1. <span data-ttu-id="fcae7-237">在项目文件夹中，创建一个新的 HTML 文件并将其命名*StockTicker.html*。</span><span class="sxs-lookup"><span data-stu-id="fcae7-237">Create a new HTML file in the project folder, and name it *StockTicker.html*.</span></span>
2. <span data-ttu-id="fcae7-238">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="fcae7-238">Replace the template code with the following code:</span></span>

    [!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample13.html)]

    <span data-ttu-id="fcae7-239">HTML 5 列、 一个标题行，与具有一个单元格跨越所有 5 个列的数据行创建一个表。</span><span class="sxs-lookup"><span data-stu-id="fcae7-239">The HTML creates a table with 5 columns, a header row, and a data row with a single cell that spans all 5 columns.</span></span> <span data-ttu-id="fcae7-240">数据行显示"正在加载..."，并暂时不可用时在应用程序启动后，才会显示。</span><span class="sxs-lookup"><span data-stu-id="fcae7-240">The data row displays "loading..." and will only be shown momentarily when the application starts.</span></span> <span data-ttu-id="fcae7-241">JavaScript 代码将删除该行，并添加其位置的行中使用从服务器中检索的股票数据。</span><span class="sxs-lookup"><span data-stu-id="fcae7-241">JavaScript code will remove that row and add in its place rows with stock data retrieved from the server.</span></span>

    <span data-ttu-id="fcae7-242">脚本标记指定的 jQuery 脚本文件、 SignalR 核心脚本文件、 SignalR 代理脚本文件和更高版本，您将创建一个 StockTicker 脚本文件。</span><span class="sxs-lookup"><span data-stu-id="fcae7-242">The script tags specify the jQuery script file, the SignalR core script file, the SignalR proxies script file, and a StockTicker script file that you'll create later.</span></span> <span data-ttu-id="fcae7-243">SignalR 代理脚本文件的说明进行操作，它指定"/ signalr/中心"URL，动态生成，并在这种情况下定义 StockTickerHub.GetAllStocks 的 Hub 类上的方法的代理方法。</span><span class="sxs-lookup"><span data-stu-id="fcae7-243">The SignalR proxies script file, which specifies the "/signalr/hubs" URL, is dynamically generated and defines proxy methods for the methods on the Hub class, in this case for StockTickerHub.GetAllStocks.</span></span> <span data-ttu-id="fcae7-244">如果您愿意，您可以通过使用手动生成此 JavaScript 文件[SignalR 实用程序](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/)和禁用 MapHubs 方法调用中的动态文件创建。</span><span class="sxs-lookup"><span data-stu-id="fcae7-244">If you prefer, you can generate this JavaScript file manually by using [SignalR Utilities](http://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) and disable dynamic file creation in the MapHubs method call.</span></span>
3. > [!IMPORTANT]
   > <span data-ttu-id="fcae7-245">请确保在 JavaScript 文件中引用*StockTicker.html*正确无误。</span><span class="sxs-lookup"><span data-stu-id="fcae7-245">Make sure that the JavaScript file references in *StockTicker.html* are correct.</span></span> <span data-ttu-id="fcae7-246">也就是说，确保你的脚本标记 (1.8.2 在示例中) 中的 jQuery 版本是在项目中的 jQuery 版本相同*脚本*文件夹，并确保你的脚本标记中的 SignalR 版本是 SignalR 相同在项目的版本*脚本*文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcae7-246">That is, make sure that the jQuery version in your script tag (1.8.2 in the example) is the same as the jQuery version in your project's *Scripts* folder, and make sure that the SignalR version in your script tag is the same as the SignalR version in your project's *Scripts* folder.</span></span> <span data-ttu-id="fcae7-247">如有必要，请更改脚本标记中的文件名称。</span><span class="sxs-lookup"><span data-stu-id="fcae7-247">Change the file names in the script tags if necessary.</span></span>
4. <span data-ttu-id="fcae7-248">在中**解决方案资源管理器**，右键单击*StockTicker.html*，然后单击**设为起始页**。</span><span class="sxs-lookup"><span data-stu-id="fcae7-248">In **Solution Explorer**, right-click *StockTicker.html*, and then click **Set as Start Page**.</span></span>
5. <span data-ttu-id="fcae7-249">在项目文件夹中创建的新 JavaScript 文件并将其命名*StockTicker.js*...</span><span class="sxs-lookup"><span data-stu-id="fcae7-249">Create a new JavaScript file in the project folder and name it *StockTicker.js*..</span></span>
6. <span data-ttu-id="fcae7-250">将模板代码替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="fcae7-250">Replace the template code with the following code:</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample14.js)]

    <span data-ttu-id="fcae7-251">$.connection 指 SignalR 代理。</span><span class="sxs-lookup"><span data-stu-id="fcae7-251">$.connection refers to the SignalR proxies.</span></span> <span data-ttu-id="fcae7-252">该代码获取 StockTickerHub 类对代理的引用，并将其放入行情自动收录器变量。</span><span class="sxs-lookup"><span data-stu-id="fcae7-252">The code gets a reference to the proxy for the StockTickerHub class and puts it in the ticker variable.</span></span> <span data-ttu-id="fcae7-253">代理名称是 [HubName] 属性已设置的名称：</span><span class="sxs-lookup"><span data-stu-id="fcae7-253">The proxy name is the name that was set by the [HubName] attribute:</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample15.js)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample16.cs)]

    <span data-ttu-id="fcae7-254">定义所有变量和函数后，文件中的代码的最后一行初始化 SignalR 连接通过调用 SignalR 启动函数。</span><span class="sxs-lookup"><span data-stu-id="fcae7-254">After all the variables and functions are defined, the last line of code in the file initializes the SignalR connection by calling the SignalR start function.</span></span> <span data-ttu-id="fcae7-255">启动函数以异步方式执行，并返回[jQuery 延迟对象](http://api.jquery.com/category/deferred-object/)，这意味着您可以调用 done 的函数可指定异步操作完成时要调用的函数...</span><span class="sxs-lookup"><span data-stu-id="fcae7-255">The start function executes asynchronously and returns a [jQuery Deferred object](http://api.jquery.com/category/deferred-object/), which means you can call the done function to specify the function to call when the asynchronous operation is completed..</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample17.js)]

    <span data-ttu-id="fcae7-256">Init 函数 getAllStocks 函数调用服务器上，并使用服务器将返回以更新库存表的信息。</span><span class="sxs-lookup"><span data-stu-id="fcae7-256">The init function calls the getAllStocks function on the server and uses the information that the server returns to update the stock table.</span></span> <span data-ttu-id="fcae7-257">请注意，默认情况下，您必须使用 camel 大小写格式的客户端上但方法名是 pascal 大小写的服务器上。</span><span class="sxs-lookup"><span data-stu-id="fcae7-257">Notice that by default, you have to use camel casing on the client although the method name is pascal-cased on the server.</span></span> <span data-ttu-id="fcae7-258">Camel 大小写规则仅适用于方法，而不是对象。</span><span class="sxs-lookup"><span data-stu-id="fcae7-258">The camel-casing rule only applies to methods, not objects.</span></span> <span data-ttu-id="fcae7-259">例如，指股票。符号和库存。价格、 不 stock.symbol 或 stock.price。</span><span class="sxs-lookup"><span data-stu-id="fcae7-259">For example, you refer to stock.Symbol and stock.Price, not stock.symbol or stock.price.</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample18.js)]

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample19.cs)]

    <span data-ttu-id="fcae7-260">如果你想要在客户端上使用 pascal 大小写，或者如果你想要使用完全不同的方法名称，无法修饰具有 HubMethodName 特性中心的方法相同的方式，修饰，Hub 类本身与 HubName 属性。</span><span class="sxs-lookup"><span data-stu-id="fcae7-260">If you wanted to use pascal casing on the client, or if you wanted to use a completely different method name, you could decorate the Hub method with the HubMethodName attribute the same way you decorated the Hub class itself with the HubName attribute.</span></span>

    <span data-ttu-id="fcae7-261">在 init 方法中，为从服务器收到通过调用 formatStock 格式属性的常用对象，每个股票对象创建 HTML 表行，然后通过调用取代 (顶部定义*StockTicker.js*) 将 rowTemplate 变量中的占位符替换为股票对象属性值。</span><span class="sxs-lookup"><span data-stu-id="fcae7-261">In the init method, HTML for a table row is created for each stock object received from the server by calling formatStock to format properties of the stock object, and then by calling supplant (which is defined at the top of *StockTicker.js*) to replace placeholders in the rowTemplate variable with the stock object property values.</span></span> <span data-ttu-id="fcae7-262">生成的 HTML 然后追加到常用的表。</span><span class="sxs-lookup"><span data-stu-id="fcae7-262">The resulting HTML is then appended to the stock table.</span></span>

    <span data-ttu-id="fcae7-263">通过将其作为执行异步启动函数完成后的回调函数传递调用 init。</span><span class="sxs-lookup"><span data-stu-id="fcae7-263">You call init by passing it in as a callback function that executes after the asynchronous start function completes.</span></span> <span data-ttu-id="fcae7-264">如果作为单独的 JavaScript 语句调用开始后调用 init，该函数会失败，因为立即会执行而不必等待启动函数以完成建立连接。</span><span class="sxs-lookup"><span data-stu-id="fcae7-264">If you called init as a separate JavaScript statement after calling start, the function would fail because it would execute immediately without waiting for the start function to finish establishing the connection.</span></span> <span data-ttu-id="fcae7-265">在这种情况下，init 函数会尝试调用 getAllStocks 函数，才能建立服务器连接。</span><span class="sxs-lookup"><span data-stu-id="fcae7-265">In that case, the init function would try to call the getAllStocks function before the server connection is established.</span></span>

    <span data-ttu-id="fcae7-266">当服务器更改股票的价格时，它调用 updateStockPrice 上连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="fcae7-266">When the server changes a stock's price, it calls the updateStockPrice on connected clients.</span></span> <span data-ttu-id="fcae7-267">若要在服务器使其可供调用情况下，该函数添加到 stockTicker 代理的客户端属性。</span><span class="sxs-lookup"><span data-stu-id="fcae7-267">The function is added to the client property of the stockTicker proxy in order to make it available to calls from the server.</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample20.js)]

    <span data-ttu-id="fcae7-268">UpdateStockPrice 函数设置从服务器接收到一个表行，如 init 函数中所示相同的方式的常用对象的格式。</span><span class="sxs-lookup"><span data-stu-id="fcae7-268">The updateStockPrice function formats a stock object received from the server into a table row the same way as in the init function.</span></span> <span data-ttu-id="fcae7-269">但是，而不是追加到表的行，它查找股票的当前行的表中并替换新行。</span><span class="sxs-lookup"><span data-stu-id="fcae7-269">However, instead of appending the row to the table, it finds the stock's current row in the table and replaces that row with the new one.</span></span>

<a id="test"></a>

## <a name="test-the-application"></a><span data-ttu-id="fcae7-270">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="fcae7-270">Test the application</span></span>

1. <span data-ttu-id="fcae7-271">按 F5 在调试模式下运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="fcae7-271">Press F5 to run the application in debug mode.</span></span>

    <span data-ttu-id="fcae7-272">库存表最初会显示"正在加载..."行，然后显示初始的股票数据时，一小段延迟后，然后股票价格开始更改。</span><span class="sxs-lookup"><span data-stu-id="fcae7-272">The stock table initially displays the "loading..." line, then after a short delay the initial stock data is displayed, and then the stock prices start to change.</span></span>

    ![“加载”](tutorial-server-broadcast-with-aspnet-signalr/_static/image7.png)

    ![初始库存表](tutorial-server-broadcast-with-aspnet-signalr/_static/image8.png)

    ![从服务器接收更改的库存表](tutorial-server-broadcast-with-aspnet-signalr/_static/image9.png)
2. <span data-ttu-id="fcae7-276">从浏览器地址栏复制 URL 并将其粘贴到一个或多个新的浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="fcae7-276">Copy the URL from the browser address bar and paste it into one or more new browser window(s).</span></span>

    <span data-ttu-id="fcae7-277">初始股票显示第一个浏览器相同，同时发生了更改。</span><span class="sxs-lookup"><span data-stu-id="fcae7-277">The initial stock display is the same as the first browser and changes happen simultaneously.</span></span>
3. <span data-ttu-id="fcae7-278">关闭所有浏览器并打开新浏览器，然后转到相同的 URL。</span><span class="sxs-lookup"><span data-stu-id="fcae7-278">Close all browsers and open a new browser, then go to the same URL.</span></span>

    <span data-ttu-id="fcae7-279">在服务器中运行，因此库存表屏幕将显示在股票已继续更改逐渐 StockTicker 单一实例对象。</span><span class="sxs-lookup"><span data-stu-id="fcae7-279">The StockTicker singleton object has continued to run in the server, so the stock table display shows that the stocks have continued to change.</span></span> <span data-ttu-id="fcae7-280">（不到具有零更改图形的初始表。）</span><span class="sxs-lookup"><span data-stu-id="fcae7-280">(You don't see the initial table with zero change figures.)</span></span>
4. <span data-ttu-id="fcae7-281">关闭浏览器。</span><span class="sxs-lookup"><span data-stu-id="fcae7-281">Close the browser.</span></span>

<a id="enablelogging"></a>

## <a name="enable-logging"></a><span data-ttu-id="fcae7-282">启用日志记录</span><span class="sxs-lookup"><span data-stu-id="fcae7-282">Enable logging</span></span>

<span data-ttu-id="fcae7-283">SignalR 有一个可以在客户端启用帮助解决疑难问题的内置日志记录函数。</span><span class="sxs-lookup"><span data-stu-id="fcae7-283">SignalR has a built-in logging function that you can enable on the client to aid in troubleshooting.</span></span> <span data-ttu-id="fcae7-284">在本部分中启用日志记录并演示如何日志识别您哪一台以下的传输方法使用 SignalR 的示例，请参阅：</span><span class="sxs-lookup"><span data-stu-id="fcae7-284">In this section you enable logging and see examples that show how logs tell you which of the following transport methods SignalR is using:</span></span>

- <span data-ttu-id="fcae7-285">[Websocket](http://en.wikipedia.org/wiki/WebSocket)、 IIS 8 和当前浏览器中受支持。</span><span class="sxs-lookup"><span data-stu-id="fcae7-285">[WebSockets](http://en.wikipedia.org/wiki/WebSocket), supported by IIS 8 and current browsers.</span></span>
- <span data-ttu-id="fcae7-286">[服务器发送事件](http://en.wikipedia.org/wiki/Server-sent_events)、 Internet Explorer 以外的浏览器中受支持。</span><span class="sxs-lookup"><span data-stu-id="fcae7-286">[Server-sent events](http://en.wikipedia.org/wiki/Server-sent_events), supported by browsers other than Internet Explorer.</span></span>
- <span data-ttu-id="fcae7-287">[永久帧](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe)、 Internet Explorer 中受支持。</span><span class="sxs-lookup"><span data-stu-id="fcae7-287">[Forever frame](http://en.wikipedia.org/wiki/Comet_(programming)#Hidden_iframe), supported by Internet Explorer.</span></span>
- <span data-ttu-id="fcae7-288">[Ajax 长轮询](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling)，所有浏览器中受支持。</span><span class="sxs-lookup"><span data-stu-id="fcae7-288">[Ajax long polling](http://en.wikipedia.org/wiki/Comet_(programming)#Ajax_with_long_polling), supported by all browsers.</span></span>

<span data-ttu-id="fcae7-289">对于任何给定的连接，SignalR 会选择最佳的传输方法在服务器和客户端支持。</span><span class="sxs-lookup"><span data-stu-id="fcae7-289">For any given connection, SignalR chooses the best transport method that both the server and the client support.</span></span>

1. <span data-ttu-id="fcae7-290">打开*StockTicker.js*并添加代码以启用日志记录之前初始化连接文件末尾的代码行：</span><span class="sxs-lookup"><span data-stu-id="fcae7-290">Open *StockTicker.js* and add a line of code to enable logging immediately before the code that initializes the connection at the end of the file:</span></span>

    [!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample21.js)]
2. <span data-ttu-id="fcae7-291">按 F5 运行项目。</span><span class="sxs-lookup"><span data-stu-id="fcae7-291">Press F5 to run the project.</span></span>
3. <span data-ttu-id="fcae7-292">打开浏览器的开发人员工具窗口，并选择控制台以查看的日志。</span><span class="sxs-lookup"><span data-stu-id="fcae7-292">Open your browser's developer tools window, and select the Console to see the logs.</span></span> <span data-ttu-id="fcae7-293">您可能需要刷新页面以查看 Signalr 协商新连接的传输方法的日志。</span><span class="sxs-lookup"><span data-stu-id="fcae7-293">You might have to refresh the page to see the logs of Signalr negotiating the transport method for a new connection.</span></span>

    <span data-ttu-id="fcae7-294">如果在 Windows 8 (IIS 8) 上运行 Internet Explorer 10，传输方法是 Websocket。</span><span class="sxs-lookup"><span data-stu-id="fcae7-294">If you are running Internet Explorer 10 on Windows 8 (IIS 8), the transport method is WebSockets.</span></span>

    ![IE 10 IIS 8 控制台](tutorial-server-broadcast-with-aspnet-signalr/_static/image10.png)

    <span data-ttu-id="fcae7-296">如果在 Windows 7 (IIS 7.5) 上运行 Internet Explorer 10，传输方法是 iframe。</span><span class="sxs-lookup"><span data-stu-id="fcae7-296">If you are running Internet Explorer 10 on Windows 7 (IIS 7.5), the transport method is iframe.</span></span>

    ![IE 10 Console, IIS 7.5](tutorial-server-broadcast-with-aspnet-signalr/_static/image11.png)

    <span data-ttu-id="fcae7-298">在 Firefox 中安装的 Firebug 外接程序以获取控制台窗口。</span><span class="sxs-lookup"><span data-stu-id="fcae7-298">In Firefox, install the Firebug add-in to get a Console window.</span></span> <span data-ttu-id="fcae7-299">如果在 Windows 8 (IIS 8) 上运行 Firefox 19，传输方法是 Websocket。</span><span class="sxs-lookup"><span data-stu-id="fcae7-299">If you are running Firefox 19 on Windows 8 (IIS 8), the transport method is WebSockets.</span></span>

    ![Firefox 19 IIS 8 Websocket](tutorial-server-broadcast-with-aspnet-signalr/_static/image12.png)

    <span data-ttu-id="fcae7-301">如果在 Windows 7 (IIS 7.5) 上运行 Firefox 19，传输方法将是服务器发送事件。</span><span class="sxs-lookup"><span data-stu-id="fcae7-301">If you are running Firefox 19 on Windows 7 (IIS 7.5), the transport method is server-sent events.</span></span>

    ![Firefox 19 IIS 7.5 控制台](tutorial-server-broadcast-with-aspnet-signalr/_static/image13.png)

<a id="fullsample"></a>

## <a name="install-and-review-the-full-stockticker-sample"></a><span data-ttu-id="fcae7-303">安装和查看完整的 StockTicker 示例</span><span class="sxs-lookup"><span data-stu-id="fcae7-303">Install and review the full StockTicker sample</span></span>

<span data-ttu-id="fcae7-304">StockTicker 应用程序的情况下，安装[Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) NuGet 包包含更多的功能比只是创建从零开始的简化版本。</span><span class="sxs-lookup"><span data-stu-id="fcae7-304">The StockTicker application that is installed by the [Microsoft.AspNet.SignalR.Sample](http://nuget.org/packages/microsoft.aspnet.signalr.sample) NuGet package includes more features than the simplified version that you just created from scratch.</span></span> <span data-ttu-id="fcae7-305">在本教程的此部分中，安装 NuGet 包并查看新功能和实现它们的代码。</span><span class="sxs-lookup"><span data-stu-id="fcae7-305">In this section of the tutorial, you install the NuGet package and review the new features and the code that implements them.</span></span>

### <a name="install-the-signalrsample-nuget-package"></a><span data-ttu-id="fcae7-306">安装 SignalR.Sample NuGet 包</span><span class="sxs-lookup"><span data-stu-id="fcae7-306">Install the SignalR.Sample NuGet package</span></span>

1. <span data-ttu-id="fcae7-307">在中**解决方案资源管理器**，右键单击项目，然后单击**管理 NuGet 包**。</span><span class="sxs-lookup"><span data-stu-id="fcae7-307">In **Solution Explorer**, right-click the project and click **Manage NuGet Packages**.</span></span>
2. <span data-ttu-id="fcae7-308">在中**管理 NuGet 包**对话框中，单击**联机**，输入*SignalR.Sample*中**联机搜索**框，然后依次**安装**中**SignalR.Sample**包。</span><span class="sxs-lookup"><span data-stu-id="fcae7-308">In the **Manage NuGet Packages** dialog box, click **Online**, enter *SignalR.Sample* in the **Search Online** box, and then click **Install** in the **SignalR.Sample** package.</span></span>

    ![安装 SignalR.Sample 包](tutorial-server-broadcast-with-aspnet-signalr/_static/image14.png)
3. <span data-ttu-id="fcae7-310">在中*Global.asax*文件中，注释掉 RouteTable.Routes.MapHubs(); 行之前在应用程序中添加\_Start 方法。</span><span class="sxs-lookup"><span data-stu-id="fcae7-310">In the *Global.asax* file, comment out the RouteTable.Routes.MapHubs(); line that you added earlier in the Application\_Start method.</span></span>

    <span data-ttu-id="fcae7-311">中的代码*Global.asax*不再需要因为 SignalR.Sample 包注册中的 SignalR 路由*应用\_Start/RegisterHubs.cs*文件：</span><span class="sxs-lookup"><span data-stu-id="fcae7-311">The code in *Global.asax* is no longer needed because the SignalR.Sample package registers the SignalR route in the *App\_Start/RegisterHubs.cs* file:</span></span>

    [!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample22.cs)]

    <span data-ttu-id="fcae7-312">引用的程序集特性的 WebActivator 类包含在 WebActivatorEx NuGet 包，可为 SignalR.Sample 包的依赖项安装。</span><span class="sxs-lookup"><span data-stu-id="fcae7-312">The WebActivator class that is referenced by the assembly attribute is included in the WebActivatorEx NuGet package, which is installed as a dependency of the SignalR.Sample package.</span></span>
4. <span data-ttu-id="fcae7-313">在中**解决方案资源管理器**，展开*SignalR.Sample*安装 SignalR.Sample 包创建的文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcae7-313">In **Solution Explorer**, expand the *SignalR.Sample* folder which was created by installing the SignalR.Sample package.</span></span>
5. <span data-ttu-id="fcae7-314">在中*SignalR.Sample*文件夹中，右键单击*StockTicker.html*，然后单击**设为起始页**。</span><span class="sxs-lookup"><span data-stu-id="fcae7-314">In the *SignalR.Sample* folder, right-click *StockTicker.html*, and then click **Set As Start Page**.</span></span>

    > [!NOTE]
    > <span data-ttu-id="fcae7-315">安装 SignalR.Sample NuGet 包可能会更改中有的 jQuery 版本你*脚本*文件夹。</span><span class="sxs-lookup"><span data-stu-id="fcae7-315">Installing The SignalR.Sample NuGet package might change the version of jQuery that you have in your *Scripts* folder.</span></span> <span data-ttu-id="fcae7-316">新*StockTicker.html*包将安装中的文件*SignalR.Sample*文件夹将与包将安装，但如果你想要运行原始的jQuery版本同步*StockTicker.html*再次文件中，您可能必须先更新中的脚本标记的 jQuery 引用。</span><span class="sxs-lookup"><span data-stu-id="fcae7-316">The new *StockTicker.html* file that the package installs in the *SignalR.Sample* folder will be in sync with the jQuery version that the package installs, but if you want to run your original *StockTicker.html* file again, you might have to update the jQuery reference in the script tag first.</span></span>

### <a name="run-the-application"></a><span data-ttu-id="fcae7-317">运行此应用程序</span><span class="sxs-lookup"><span data-stu-id="fcae7-317">Run the application</span></span>

1. <span data-ttu-id="fcae7-318">按 F5 运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="fcae7-318">Press F5 to run the application.</span></span>

    <span data-ttu-id="fcae7-319">除了你此前看到的网格中，完整股票行情自动收录器应用程序显示一个水平滚动的窗口，显示相同的库存数据。</span><span class="sxs-lookup"><span data-stu-id="fcae7-319">In addition to the grid that you saw earlier, the full stock ticker application shows a horizontally scrolling window that displays the same stock data.</span></span> <span data-ttu-id="fcae7-320">第一次的应用程序运行时，"市场""已关闭"，你看到静态网格和不滚动的行情自动收录器窗口。</span><span class="sxs-lookup"><span data-stu-id="fcae7-320">When you run the application for the first time, the "market" is "closed" and you see a static grid and a ticker window that isn't scrolling.</span></span>

    ![StockTicker 屏幕开始](tutorial-server-broadcast-with-aspnet-signalr/_static/image15.png)

    <span data-ttu-id="fcae7-322">当您单击**公开市场**，则**实时股票行情自动收录器**水平滚动框开始和在服务器开始定期广播在随机的基础上的股票价格发生变化。</span><span class="sxs-lookup"><span data-stu-id="fcae7-322">When you click **Open Market**, the **Live Stock Ticker** box starts to scroll horizontally, and the server starts to periodically broadcast stock price changes on a random basis.</span></span> <span data-ttu-id="fcae7-323">每次股票价格发生更改，同时**实时股票表格**网格并**实时股票行情自动收录器**框会更新。</span><span class="sxs-lookup"><span data-stu-id="fcae7-323">Each time a stock price changes, both the **Live Stock Table** grid and the **Live Stock Ticker** box are updated.</span></span> <span data-ttu-id="fcae7-324">当股票的价格更改为正时，股票会显示绿色背景; 以及并更改为负，包含一个红色背景显示股票。</span><span class="sxs-lookup"><span data-stu-id="fcae7-324">When a stock's price change is positive, the stock is shown with a green background, and when the change is negative, the stock is shown with a red background.</span></span>

    ![StockTicker 应用市场打开](tutorial-server-broadcast-with-aspnet-signalr/_static/image16.png)

    <span data-ttu-id="fcae7-326">**关闭市场**按钮停止所做的更改并停止行情自动收录器滚动，并**重置**按钮可重置所有股票数据为初始状态价格更改启动之前。</span><span class="sxs-lookup"><span data-stu-id="fcae7-326">The **Close Market** button stops the changes and stops the ticker scrolling, and the **Reset** button resets all stock data to the initial state before price changes started.</span></span> <span data-ttu-id="fcae7-327">如果您打开更多的浏览器窗口并转到同一个 URL，您将看到相同的数据在同一时间在每个浏览器中动态更新。</span><span class="sxs-lookup"><span data-stu-id="fcae7-327">If you open more browser windows and go to the same URL, you see the same data dynamically updated at the same time in each browser.</span></span> <span data-ttu-id="fcae7-328">当您单击某个按钮时，所有浏览器都在同一时间具有相同的方式。</span><span class="sxs-lookup"><span data-stu-id="fcae7-328">When you click one of the buttons, all browsers respond the same way at the same time.</span></span>

### <a name="live-stock-ticker-display"></a><span data-ttu-id="fcae7-329">实时股票行情自动收录器显示</span><span class="sxs-lookup"><span data-stu-id="fcae7-329">Live Stock Ticker display</span></span>

<span data-ttu-id="fcae7-330">**实时股票行情自动收录器**显示为未排序的列表中的 CSS 样式格式设置为单个行的 div 元素。</span><span class="sxs-lookup"><span data-stu-id="fcae7-330">The **Live Stock Ticker** display is an unordered list in a div element that is formatted into a single line by CSS styles.</span></span> <span data-ttu-id="fcae7-331">行情自动收录器会被初始化并更新表一样： 通过替换中的占位符&lt;li&gt;模板字符串和动态添加&lt;l i&gt;元素&lt;u l&gt;元素。</span><span class="sxs-lookup"><span data-stu-id="fcae7-331">The ticker is initialized and updated the same way as the table: by replacing placeholders in a &lt;li&gt; template string and dynamically adding the &lt;li&gt; elements to the &lt;ul&gt; element.</span></span> <span data-ttu-id="fcae7-332">通过使用 jQuery 进行动画处理函数来改变该 div。 中的未排序列表距左侧执行滚动</span><span class="sxs-lookup"><span data-stu-id="fcae7-332">The scrolling is performed by using the jQuery animate function to vary the margin-left of the unordered list within the div.</span></span>

<span data-ttu-id="fcae7-333">股票行情 HTML:</span><span class="sxs-lookup"><span data-stu-id="fcae7-333">The stock ticker HTML:</span></span>

[!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample23.html)]

<span data-ttu-id="fcae7-334">股票行情 CSS:</span><span class="sxs-lookup"><span data-stu-id="fcae7-334">The stock ticker CSS:</span></span>

[!code-html[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample24.html)]

<span data-ttu-id="fcae7-335">使它的 jQuery 代码滚动：</span><span class="sxs-lookup"><span data-stu-id="fcae7-335">The jQuery code that makes it scroll:</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample25.js)]

### <a name="additional-methods-on-the-server-that-the-client-can-call"></a><span data-ttu-id="fcae7-336">客户端可以调用的服务器上的其他方法</span><span class="sxs-lookup"><span data-stu-id="fcae7-336">Additional methods on the server that the client can call</span></span>

<span data-ttu-id="fcae7-337">StockTickerHub 类定义了四个客户端可以调用的其他方法：</span><span class="sxs-lookup"><span data-stu-id="fcae7-337">The StockTickerHub class defines four additional methods that the client can call:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample26.cs)]

<span data-ttu-id="fcae7-338">在页面顶部的按钮响应称为 OpenMarket、 CloseMarket 和重置。</span><span class="sxs-lookup"><span data-stu-id="fcae7-338">OpenMarket, CloseMarket, and Reset are called in response to the buttons at the top of the page.</span></span> <span data-ttu-id="fcae7-339">它们演示了一个客户端触发立即传播到所有客户端的状态的更改的模式。</span><span class="sxs-lookup"><span data-stu-id="fcae7-339">They demonstrate the pattern of one client triggering a change in state that is immediately propagated to all clients.</span></span> <span data-ttu-id="fcae7-340">每个方法调用中的方法 StockTicker 类市场状态更改，然后再将广播新状态的效果。</span><span class="sxs-lookup"><span data-stu-id="fcae7-340">Each of these methods calls a method in the StockTicker class that effects the market state change and then broadcasts the new state.</span></span>

<span data-ttu-id="fcae7-341">在 StockTicker 类中，通过返回 MarketState 枚举值的 MarketState 属性的维护是市场的状态：</span><span class="sxs-lookup"><span data-stu-id="fcae7-341">In the StockTicker class, the state of the market is maintained by a MarketState property that returns a MarketState enum value:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample27.cs)]

<span data-ttu-id="fcae7-342">每个更改市场状态的方法都这样做，在锁块的内部因为 StockTicker 类必须是 threadsafe:</span><span class="sxs-lookup"><span data-stu-id="fcae7-342">Each of the methods that change the market state do so inside a lock block because the StockTicker class has to be threadsafe:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample28.cs)]

<span data-ttu-id="fcae7-343">若要确保此代码是 threadsafe， \_marketState 字段支持 MarketState 属性标记为易失性，</span><span class="sxs-lookup"><span data-stu-id="fcae7-343">To ensure that this code is threadsafe, the \_marketState field that backs the MarketState property is marked as volatile,</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample29.cs)]

<span data-ttu-id="fcae7-344">BroadcastMarketStateChange 和 BroadcastMarketReset 方法是您已看到的 BroadcastStockPrice 方法相似，只不过它们调用在客户端定义的不同方法：</span><span class="sxs-lookup"><span data-stu-id="fcae7-344">The BroadcastMarketStateChange and BroadcastMarketReset methods are similar to the BroadcastStockPrice method that you already saw, except they call different methods defined at the client:</span></span>

[!code-csharp[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample30.cs)]

### <a name="additional-functions-on-the-client-that-the-server-can-call"></a><span data-ttu-id="fcae7-345">服务器可以调用的客户端上的其他函数</span><span class="sxs-lookup"><span data-stu-id="fcae7-345">Additional functions on the client that the server can call</span></span>

<span data-ttu-id="fcae7-346">UpdateStockPrice 函数现在可处理网格和行情自动收录器显示，并使用 jQuery.Color 闪烁红色和绿色的颜色。</span><span class="sxs-lookup"><span data-stu-id="fcae7-346">The updateStockPrice function now handles both the grid and the ticker display, and it uses jQuery.Color to flash red and green colors.</span></span>

<span data-ttu-id="fcae7-347">中的新函数*SignalR.StockTicker.js*启用和禁用按钮根据市场的状态，并在停止或启动行情自动收录器窗口水平滚动。</span><span class="sxs-lookup"><span data-stu-id="fcae7-347">New functions in *SignalR.StockTicker.js* enable and disable the buttons based on market state, and they stop or start the ticker window horizontal scrolling.</span></span> <span data-ttu-id="fcae7-348">由于多个函数添加到 ticker.client，因此[jQuery 扩展函数](http://api.jquery.com/jQuery.extend/)用于添加它们。</span><span class="sxs-lookup"><span data-stu-id="fcae7-348">Since multiple functions are being added to ticker.client, the [jQuery extend function](http://api.jquery.com/jQuery.extend/) is used to add them.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample31.js)]

### <a name="additional-client-setup-after-establishing-the-connection"></a><span data-ttu-id="fcae7-349">建立连接后的其他客户端安装程序</span><span class="sxs-lookup"><span data-stu-id="fcae7-349">Additional client setup after establishing the connection</span></span>

<span data-ttu-id="fcae7-350">客户端会建立连接后，它具有一些附加工作以执行操作： 找出市场是否打开或关闭以便调用相应 marketOpened 或 marketClosed 函数，并附加到按钮的服务器方法调用。</span><span class="sxs-lookup"><span data-stu-id="fcae7-350">After the client establishes the connection, it has some additional work to do: find out if the market is open or closed in order to call the appropriate marketOpened or marketClosed function, and attach the server method calls to the buttons.</span></span>

[!code-javascript[Main](tutorial-server-broadcast-with-aspnet-signalr/samples/sample32.js)]

<span data-ttu-id="fcae7-351">服务器方法不是绑定到之前的按钮之后建立的连接，从而使代码不能尝试之前，它们可调用服务器方法。</span><span class="sxs-lookup"><span data-stu-id="fcae7-351">The server methods are not wired up to the buttons until after the connection is established, so that the code can't try to call the server methods before they are available.</span></span>

<a id="nextsteps"></a>

## <a name="next-steps"></a><span data-ttu-id="fcae7-352">后续步骤</span><span class="sxs-lookup"><span data-stu-id="fcae7-352">Next steps</span></span>

<span data-ttu-id="fcae7-353">在本教程中介绍了如何将消息从服务器广播到所有连接的客户端，定期更新，以响应来自任何客户端通知的 SignalR 应用程序进行编程。</span><span class="sxs-lookup"><span data-stu-id="fcae7-353">In this tutorial you've learned how to program a SignalR application that broadcasts messages from the server to all connected clients, both on a periodic basis and in response to notifications from any client.</span></span> <span data-ttu-id="fcae7-354">使用多线程的单一实例维护服务器状态模式也还可在多玩家联机游戏方案。</span><span class="sxs-lookup"><span data-stu-id="fcae7-354">The pattern of using a multi-threaded singleton instance to maintain server state can also be also used in multi-player online game scenarios.</span></span> <span data-ttu-id="fcae7-355">有关示例，请参阅[依赖于使用 SignalR ShootR 游戏](https://github.com/NTaylorMullen/ShootR)。</span><span class="sxs-lookup"><span data-stu-id="fcae7-355">For an example, see [the ShootR game that is based on SignalR](https://github.com/NTaylorMullen/ShootR).</span></span>

<span data-ttu-id="fcae7-356">显示对等通信方案的教程，请参阅[SignalR 入门](index.md)并[使用 SignalR 实时更新](index.md)。</span><span class="sxs-lookup"><span data-stu-id="fcae7-356">For tutorials that show peer-to-peer communication scenarios, see [Getting Started with SignalR](index.md) and [Real-Time Updating with SignalR](index.md).</span></span>

<span data-ttu-id="fcae7-357">若要了解更高级的 SignalR 开发概念，请访问以下站点 SignalR 源代码和资源：</span><span class="sxs-lookup"><span data-stu-id="fcae7-357">To learn more advanced SignalR development concepts, visit the following sites for SignalR source code and resources:</span></span>

- [<span data-ttu-id="fcae7-358">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="fcae7-358">ASP.NET SignalR</span></span>](https://asp.net/signalr/)
- [<span data-ttu-id="fcae7-359">SignalR 项目</span><span class="sxs-lookup"><span data-stu-id="fcae7-359">SignalR Project</span></span>](http://signalr.net/)
- [<span data-ttu-id="fcae7-360">SignalR Github 和示例</span><span class="sxs-lookup"><span data-stu-id="fcae7-360">SignalR Github and Samples</span></span>](https://github.com/SignalR/SignalR)
- [<span data-ttu-id="fcae7-361">SignalR Wiki</span><span class="sxs-lookup"><span data-stu-id="fcae7-361">SignalR Wiki</span></span>](https://github.com/SignalR/SignalR/wiki)
