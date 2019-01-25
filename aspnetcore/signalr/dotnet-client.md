---
title: ASP.NET Core SignalR.NET 客户端
author: bradygaster
description: 有关 ASP.NET Core SignalR.NET 客户端信息
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/10/2018
uid: signalr/dotnet-client
ms.openlocfilehash: 25b618f7a424b217c0fb55417754ea358280b95a
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836301"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="7188e-103">ASP.NET Core SignalR.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="7188e-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="7188e-104">ASP.NET Core SignalR.NET 客户端库可让你与 SignalR 集线器从.NET 应用程序进行通信。</span><span class="sxs-lookup"><span data-stu-id="7188e-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

> [!NOTE]
> <span data-ttu-id="7188e-105">Xamarin 有特殊要求 Visual Studio 版本。</span><span class="sxs-lookup"><span data-stu-id="7188e-105">Xamarin has special requirements for Visual Studio version.</span></span> <span data-ttu-id="7188e-106">有关详细信息，请参阅[在 Xamarin 中的 SignalR 客户端 2.1.1](https://github.com/aspnet/Announcements/issues/305)。</span><span class="sxs-lookup"><span data-stu-id="7188e-106">For more information, see [SignalR Client 2.1.1 in Xamarin](https://github.com/aspnet/Announcements/issues/305).</span></span>

<span data-ttu-id="7188e-107">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="7188e-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="7188e-108">这篇文章中的代码示例是使用 ASP.NET Core SignalR.NET 客户端的 WPF 应用。</span><span class="sxs-lookup"><span data-stu-id="7188e-108">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="7188e-109">安装 SignalR.NET 客户端包</span><span class="sxs-lookup"><span data-stu-id="7188e-109">Install the SignalR .NET client package</span></span>

<span data-ttu-id="7188e-110">`Microsoft.AspNetCore.SignalR.Client`包所需的.NET 客户端连接到 SignalR 集线器。</span><span class="sxs-lookup"><span data-stu-id="7188e-110">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="7188e-111">若要安装客户端库，请运行以下命令**程序包管理器控制台**窗口：</span><span class="sxs-lookup"><span data-stu-id="7188e-111">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="7188e-112">连接到中心</span><span class="sxs-lookup"><span data-stu-id="7188e-112">Connect to a hub</span></span>

<span data-ttu-id="7188e-113">若要建立连接，创建`HubConnectionBuilder`，并调用`Build`。</span><span class="sxs-lookup"><span data-stu-id="7188e-113">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="7188e-114">生成一个连接时，可以配置中心 URL、 协议、 传输类型、 日志级别、 标头，和其他选项。</span><span class="sxs-lookup"><span data-stu-id="7188e-114">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="7188e-115">配置任何必需的选项的方法是插入的任何`HubConnectionBuilder`方法转换`Build`。</span><span class="sxs-lookup"><span data-stu-id="7188e-115">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="7188e-116">启动与连接`StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="7188e-116">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="7188e-117">已断开连接的句柄</span><span class="sxs-lookup"><span data-stu-id="7188e-117">Handle lost connection</span></span>

<span data-ttu-id="7188e-118">使用<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>事件响应丢失连接。</span><span class="sxs-lookup"><span data-stu-id="7188e-118">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="7188e-119">例如，你可能想要自动执行重新连接。</span><span class="sxs-lookup"><span data-stu-id="7188e-119">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="7188e-120">`Closed`事件需要返回的委托`Task`，它允许异步代码运行，而不使用`async void`。</span><span class="sxs-lookup"><span data-stu-id="7188e-120">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="7188e-121">若要满足中的委托签名`Closed`运行以同步方式，返回的事件处理程序`Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="7188e-121">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="7188e-122">异步支持的主要原因是使您可以重新启动连接。</span><span class="sxs-lookup"><span data-stu-id="7188e-122">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="7188e-123">正在启动的连接是一个异步操作。</span><span class="sxs-lookup"><span data-stu-id="7188e-123">Starting a connection is an async action.</span></span>

<span data-ttu-id="7188e-124">在`Closed`处理程序重新启动该连接，请考虑等待某些随机延迟，以防止服务器，重载，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="7188e-124">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="7188e-125">从客户端调用集线器方法</span><span class="sxs-lookup"><span data-stu-id="7188e-125">Call hub methods from client</span></span>

<span data-ttu-id="7188e-126">`InvokeAsync` 在该中心将调用方法。</span><span class="sxs-lookup"><span data-stu-id="7188e-126">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="7188e-127">将中心方法名称和到中心方法中定义的任何参数传递`InvokeAsync`。</span><span class="sxs-lookup"><span data-stu-id="7188e-127">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="7188e-128">SignalR 是异步的因此请使用`async`和`await`时进行调用。</span><span class="sxs-lookup"><span data-stu-id="7188e-128">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="7188e-129">从集线器调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="7188e-129">Call client methods from hub</span></span>

<span data-ttu-id="7188e-130">定义使用集线器调用的方法`connection.On`之后生成，但之前启动连接。</span><span class="sxs-lookup"><span data-stu-id="7188e-130">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="7188e-131">在前面的代码`connection.On`运行时服务器端代码将调用它使用`SendAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="7188e-131">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="7188e-132">错误处理和日志记录</span><span class="sxs-lookup"><span data-stu-id="7188e-132">Error handling and logging</span></span>

<span data-ttu-id="7188e-133">处理 try catch 语句的错误。</span><span class="sxs-lookup"><span data-stu-id="7188e-133">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="7188e-134">检查`Exception`对象，以确定要执行后发生错误的适当操作。</span><span class="sxs-lookup"><span data-stu-id="7188e-134">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="7188e-135">其他资源</span><span class="sxs-lookup"><span data-stu-id="7188e-135">Additional resources</span></span>

* [<span data-ttu-id="7188e-136">中心</span><span class="sxs-lookup"><span data-stu-id="7188e-136">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="7188e-137">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="7188e-137">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="7188e-138">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="7188e-138">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
