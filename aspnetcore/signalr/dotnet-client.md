---
title: ASP.NET Core SignalR.NET 客户端
author: bradygaster
description: 有关 ASP.NET Core SignalR.NET 客户端信息
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/17/2019
uid: signalr/dotnet-client
ms.openlocfilehash: 640d75157e42ffa6d78235c5be03e4846e8dcde9
ms.sourcegitcommit: eb784a68219b4829d8e50c8a334c38d4b94e0cfa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2019
ms.locfileid: "59982941"
---
# <a name="aspnet-core-signalr-net-client"></a><span data-ttu-id="0b43e-103">ASP.NET Core SignalR.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="0b43e-103">ASP.NET Core SignalR .NET Client</span></span>

<span data-ttu-id="0b43e-104">ASP.NET Core SignalR.NET 客户端库可让你与 SignalR 集线器从.NET 应用程序进行通信。</span><span class="sxs-lookup"><span data-stu-id="0b43e-104">The ASP.NET Core SignalR .NET client library lets you communicate with SignalR hubs from .NET apps.</span></span>

> [!NOTE]
> <span data-ttu-id="0b43e-105">Xamarin 有特殊要求 Visual Studio 版本。</span><span class="sxs-lookup"><span data-stu-id="0b43e-105">Xamarin has special requirements for Visual Studio version.</span></span> <span data-ttu-id="0b43e-106">有关详细信息，请参阅[在 Xamarin 中的 SignalR 客户端 2.1.1](https://github.com/aspnet/Announcements/issues/305)。</span><span class="sxs-lookup"><span data-stu-id="0b43e-106">For more information, see [SignalR Client 2.1.1 in Xamarin](https://github.com/aspnet/Announcements/issues/305).</span></span>

<span data-ttu-id="0b43e-107">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0b43e-107">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/dotnet-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="0b43e-108">这篇文章中的代码示例是使用 ASP.NET Core SignalR.NET 客户端的 WPF 应用。</span><span class="sxs-lookup"><span data-stu-id="0b43e-108">The code sample in this article is a WPF app that uses the ASP.NET Core SignalR .NET client.</span></span>

## <a name="install-the-signalr-net-client-package"></a><span data-ttu-id="0b43e-109">安装 SignalR.NET 客户端包</span><span class="sxs-lookup"><span data-stu-id="0b43e-109">Install the SignalR .NET client package</span></span>

<span data-ttu-id="0b43e-110">`Microsoft.AspNetCore.SignalR.Client`包所需的.NET 客户端连接到 SignalR 集线器。</span><span class="sxs-lookup"><span data-stu-id="0b43e-110">The `Microsoft.AspNetCore.SignalR.Client` package is needed for .NET clients to connect to SignalR hubs.</span></span> <span data-ttu-id="0b43e-111">若要安装客户端库，请运行以下命令**程序包管理器控制台**窗口：</span><span class="sxs-lookup"><span data-stu-id="0b43e-111">To install the client library, run the following command in the **Package Manager Console** window:</span></span>

```powershell
Install-Package Microsoft.AspNetCore.SignalR.Client
```

## <a name="connect-to-a-hub"></a><span data-ttu-id="0b43e-112">连接到中心</span><span class="sxs-lookup"><span data-stu-id="0b43e-112">Connect to a hub</span></span>

<span data-ttu-id="0b43e-113">若要建立连接，创建`HubConnectionBuilder`，并调用`Build`。</span><span class="sxs-lookup"><span data-stu-id="0b43e-113">To establish a connection, create a `HubConnectionBuilder` and call `Build`.</span></span> <span data-ttu-id="0b43e-114">生成一个连接时，可以配置中心 URL、 协议、 传输类型、 日志级别、 标头，和其他选项。</span><span class="sxs-lookup"><span data-stu-id="0b43e-114">The hub URL, protocol, transport type, log level, headers, and other options can be configured while building a connection.</span></span> <span data-ttu-id="0b43e-115">配置任何必需的选项的方法是插入的任何`HubConnectionBuilder`方法转换`Build`。</span><span class="sxs-lookup"><span data-stu-id="0b43e-115">Configure any required options by inserting any of the `HubConnectionBuilder` methods into `Build`.</span></span> <span data-ttu-id="0b43e-116">启动与连接`StartAsync`。</span><span class="sxs-lookup"><span data-stu-id="0b43e-116">Start the connection with `StartAsync`.</span></span>

[!code-csharp[Build hub connection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_MainWindowClass&highlight=15-17,39)]

## <a name="handle-lost-connection"></a><span data-ttu-id="0b43e-117">已断开连接的句柄</span><span class="sxs-lookup"><span data-stu-id="0b43e-117">Handle lost connection</span></span>

<span data-ttu-id="0b43e-118">使用<xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed>事件响应丢失连接。</span><span class="sxs-lookup"><span data-stu-id="0b43e-118">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnection.Closed> event to respond to a lost connection.</span></span> <span data-ttu-id="0b43e-119">例如，你可能想要自动执行重新连接。</span><span class="sxs-lookup"><span data-stu-id="0b43e-119">For example, you might want to automate reconnection.</span></span>

<span data-ttu-id="0b43e-120">`Closed`事件需要返回的委托`Task`，它允许异步代码运行，而不使用`async void`。</span><span class="sxs-lookup"><span data-stu-id="0b43e-120">The `Closed` event requires a delegate that returns a `Task`, which allows async code to run without using `async void`.</span></span> <span data-ttu-id="0b43e-121">若要满足中的委托签名`Closed`运行以同步方式，返回的事件处理程序`Task.CompletedTask`:</span><span class="sxs-lookup"><span data-stu-id="0b43e-121">To satisfy the delegate signature in a `Closed` event handler that runs synchronously, return `Task.CompletedTask`:</span></span>

```csharp
connection.Closed += (error) => {
    // Do your close logic.
    return Task.CompletedTask;
};
```

<span data-ttu-id="0b43e-122">异步支持的主要原因是使您可以重新启动连接。</span><span class="sxs-lookup"><span data-stu-id="0b43e-122">The main reason for the async support is so you can restart the connection.</span></span> <span data-ttu-id="0b43e-123">正在启动的连接是一个异步操作。</span><span class="sxs-lookup"><span data-stu-id="0b43e-123">Starting a connection is an async action.</span></span>

<span data-ttu-id="0b43e-124">在`Closed`处理程序重新启动该连接，请考虑等待某些随机延迟，以防止服务器，重载，如下面的示例中所示：</span><span class="sxs-lookup"><span data-stu-id="0b43e-124">In a `Closed` handler that restarts the connection, consider waiting for some random delay to prevent overloading the server, as shown in the following example:</span></span>

[!code-csharp[Use Closed event handler to automate reconnection](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ClosedRestart)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="0b43e-125">从客户端调用集线器方法</span><span class="sxs-lookup"><span data-stu-id="0b43e-125">Call hub methods from client</span></span>

<span data-ttu-id="0b43e-126">`InvokeAsync` 在该中心将调用方法。</span><span class="sxs-lookup"><span data-stu-id="0b43e-126">`InvokeAsync` calls methods on the hub.</span></span> <span data-ttu-id="0b43e-127">将中心方法名称和到中心方法中定义的任何参数传递`InvokeAsync`。</span><span class="sxs-lookup"><span data-stu-id="0b43e-127">Pass the hub method name and any arguments defined in the hub method to `InvokeAsync`.</span></span> <span data-ttu-id="0b43e-128">SignalR 是异步的因此请使用`async`和`await`时进行调用。</span><span class="sxs-lookup"><span data-stu-id="0b43e-128">SignalR is asynchronous, so use `async` and `await` when making the calls.</span></span>

[!code-csharp[InvokeAsync method](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_InvokeAsync)]

<span data-ttu-id="0b43e-129">`InvokeAsync`方法将返回`Task`服务器方法返回时完成。</span><span class="sxs-lookup"><span data-stu-id="0b43e-129">The `InvokeAsync` method returns a `Task` which completes when the server method returns.</span></span> <span data-ttu-id="0b43e-130">返回值，如果有的话，提供的结果作为`Task`。</span><span class="sxs-lookup"><span data-stu-id="0b43e-130">The return value, if any, is provided as the result of the `Task`.</span></span> <span data-ttu-id="0b43e-131">在服务器上的方法引发的任何异常生成出错`Task`。</span><span class="sxs-lookup"><span data-stu-id="0b43e-131">Any exceptions thrown by the method on the server produce a faulted `Task`.</span></span> <span data-ttu-id="0b43e-132">使用`await`要等待的时间才能完成的服务器方法的语法和`try...catch`语法来处理错误。</span><span class="sxs-lookup"><span data-stu-id="0b43e-132">Use `await` syntax to wait for the server method to complete and `try...catch` syntax to handle errors.</span></span>

<span data-ttu-id="0b43e-133">`SendAsync`方法将返回`Task`并完成时该消息已发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="0b43e-133">The `SendAsync` method returns a `Task` which completes when the message has been sent to the server.</span></span> <span data-ttu-id="0b43e-134">由于这未提供任何返回值`Task`不等待，直到服务器方法完成。</span><span class="sxs-lookup"><span data-stu-id="0b43e-134">No return value is provided since this `Task` doesn't wait until the server method completes.</span></span> <span data-ttu-id="0b43e-135">在发送消息时在客户端上引发的任何异常生成出错`Task`。</span><span class="sxs-lookup"><span data-stu-id="0b43e-135">Any exceptions thrown on the client while sending the message produce a faulted `Task`.</span></span> <span data-ttu-id="0b43e-136">使用`await`和`try...catch`语法来处理发送错误。</span><span class="sxs-lookup"><span data-stu-id="0b43e-136">Use `await` and `try...catch` syntax to handle send errors.</span></span>

> [!NOTE]
> <span data-ttu-id="0b43e-137">如果使用 Azure SignalR 服务的*无服务器模式*，不能从客户端调用集线器方法。</span><span class="sxs-lookup"><span data-stu-id="0b43e-137">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="0b43e-138">有关详细信息，请参阅[SignalR 服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。</span><span class="sxs-lookup"><span data-stu-id="0b43e-138">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="0b43e-139">从集线器调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="0b43e-139">Call client methods from hub</span></span>

<span data-ttu-id="0b43e-140">定义使用集线器调用的方法`connection.On`之后生成，但之前启动连接。</span><span class="sxs-lookup"><span data-stu-id="0b43e-140">Define methods the hub calls using `connection.On` after building, but before starting the connection.</span></span>

[!code-csharp[Define client methods](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ConnectionOn)]

<span data-ttu-id="0b43e-141">在前面的代码`connection.On`运行时服务器端代码将调用它使用`SendAsync`方法。</span><span class="sxs-lookup"><span data-stu-id="0b43e-141">The preceding code in `connection.On` runs when server-side code calls it using the `SendAsync` method.</span></span>

[!code-csharp[Call client method](dotnet-client/sample/signalrchat/hubs/chathub.cs?name=snippet_SendMessage)]

## <a name="error-handling-and-logging"></a><span data-ttu-id="0b43e-142">错误处理和日志记录</span><span class="sxs-lookup"><span data-stu-id="0b43e-142">Error handling and logging</span></span>

<span data-ttu-id="0b43e-143">处理 try catch 语句的错误。</span><span class="sxs-lookup"><span data-stu-id="0b43e-143">Handle errors with a try-catch statement.</span></span> <span data-ttu-id="0b43e-144">检查`Exception`对象，以确定要执行后发生错误的适当操作。</span><span class="sxs-lookup"><span data-stu-id="0b43e-144">Inspect the `Exception` object to determine the proper action to take after an error occurs.</span></span>

[!code-csharp[Logging](dotnet-client/sample/signalrchatclient/MainWindow.xaml.cs?name=snippet_ErrorHandling)]

## <a name="additional-resources"></a><span data-ttu-id="0b43e-145">其他资源</span><span class="sxs-lookup"><span data-stu-id="0b43e-145">Additional resources</span></span>

* [<span data-ttu-id="0b43e-146">中心</span><span class="sxs-lookup"><span data-stu-id="0b43e-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="0b43e-147">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="0b43e-147">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="0b43e-148">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="0b43e-148">Publish to Azure</span></span>](xref:signalr/publish-to-azure-web-app)
* [<span data-ttu-id="0b43e-149">Azure SignalR 服务无服务器文档</span><span class="sxs-lookup"><span data-stu-id="0b43e-149">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
