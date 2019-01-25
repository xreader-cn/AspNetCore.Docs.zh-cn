---
uid: signalr/overview/older-versions/troubleshooting
title: SignalR 疑难解答 (SignalR 1.x) |Microsoft Docs
author: bradygaster
description: 本文介绍开发 SignalR 应用程序的常见问题。
ms.author: bradyg
ms.date: 06/05/2013
ms.assetid: 347210ba-c452-4feb-886f-b51d89f58971
msc.legacyurl: /signalr/overview/older-versions/troubleshooting
msc.type: authoredcontent
ms.openlocfilehash: d485829fb60b32b063405f37ae9c6c278bf7255b
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836267"
---
<a name="signalr-troubleshooting-signalr-1x"></a><span data-ttu-id="28b90-103">SignalR 疑难解答 (SignalR 1.x)</span><span class="sxs-lookup"><span data-stu-id="28b90-103">SignalR Troubleshooting (SignalR 1.x)</span></span>
====================
<span data-ttu-id="28b90-104">通过[Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="28b90-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="28b90-105">本文档介绍使用 signalr 实现的常见故障排除问题。</span><span class="sxs-lookup"><span data-stu-id="28b90-105">This document describes common troubleshooting issues with SignalR.</span></span>


<span data-ttu-id="28b90-106">本文档包含以下各节。</span><span class="sxs-lookup"><span data-stu-id="28b90-106">This document contains the following sections.</span></span>

- [<span data-ttu-id="28b90-107">以无提示方式调用客户端和服务器之间的方法，则会失败</span><span class="sxs-lookup"><span data-stu-id="28b90-107">Calling methods between the client and server silently fails</span></span>](#connection)
- [<span data-ttu-id="28b90-108">其他的连接问题</span><span class="sxs-lookup"><span data-stu-id="28b90-108">Other connection issues</span></span>](#other)
- [<span data-ttu-id="28b90-109">编译和服务器端错误</span><span class="sxs-lookup"><span data-stu-id="28b90-109">Compilation and server-side errors</span></span>](#server)
- [<span data-ttu-id="28b90-110">Visual Studio 问题</span><span class="sxs-lookup"><span data-stu-id="28b90-110">Visual Studio issues</span></span>](#vs)
- [<span data-ttu-id="28b90-111">Internet 信息服务的问题</span><span class="sxs-lookup"><span data-stu-id="28b90-111">Internet Information Services issues</span></span>](#iis)
- [<span data-ttu-id="28b90-112">Azure 问题</span><span class="sxs-lookup"><span data-stu-id="28b90-112">Azure issues</span></span>](#azure)

<a id="connection"></a>

## <a name="calling-methods-between-the-client-and-server-silently-fails"></a><span data-ttu-id="28b90-113">以无提示方式调用客户端和服务器之间的方法，则会失败</span><span class="sxs-lookup"><span data-stu-id="28b90-113">Calling methods between the client and server silently fails</span></span>

<span data-ttu-id="28b90-114">本部分介绍的方法调用失败但不会有意义的错误消息的客户端和服务器之间可能的原因。</span><span class="sxs-lookup"><span data-stu-id="28b90-114">This section describes possible causes for a method call between client and server to fail without a meaningful error message.</span></span> <span data-ttu-id="28b90-115">在 SignalR 应用程序中，服务器具有任何信息的方法的客户端实现;当服务器调用客户端方法，方法名称和参数数据发送到客户端，并执行的方法，仅当它存在于指定的服务器的格式。</span><span class="sxs-lookup"><span data-stu-id="28b90-115">In a SignalR application, the server has no information about the methods that the client implements; when the server invokes a client method, the method name and parameter data are sent to the client, and the method is executed only if it exists in the format that the server specified.</span></span> <span data-ttu-id="28b90-116">如果没有匹配的方法找到在客户端上没有任何反应，并在服务器上引发任何错误消息。</span><span class="sxs-lookup"><span data-stu-id="28b90-116">If no matching method is found on the client, nothing happens, and no error message is raised on the server.</span></span>

<span data-ttu-id="28b90-117">若要进一步调查您尚未进行调用的客户端方法，您可以启用日志记录之前调用的中心查看哪些调用 start 方法来自服务器的选项。</span><span class="sxs-lookup"><span data-stu-id="28b90-117">To further investigate client methods not getting called, you can turn on logging before the calling the start method on the hub to see what calls are coming from the server.</span></span> <span data-ttu-id="28b90-118">若要启用日志记录中的 JavaScript 应用程序，请参阅[如何启用客户端的日志记录 （JavaScript 客户端版本）](../guide-to-the-api/hubs-api-guide-javascript-client.md#logging)。</span><span class="sxs-lookup"><span data-stu-id="28b90-118">To enable logging in a JavaScript application, see [How to enable client-side logging (JavaScript client version)](../guide-to-the-api/hubs-api-guide-javascript-client.md#logging).</span></span> <span data-ttu-id="28b90-119">若要启用.NET 客户端应用程序中的日志记录，请参阅[如何启用客户端的日志记录 （.NET 客户端版本）](../guide-to-the-api/hubs-api-guide-net-client.md#logging)。</span><span class="sxs-lookup"><span data-stu-id="28b90-119">To enable logging in a .NET client application, see [How to enable client-side logging (.NET Client version)](../guide-to-the-api/hubs-api-guide-net-client.md#logging).</span></span>

### <a name="misspelled-method-incorrect-method-signature-or-incorrect-hub-name"></a><span data-ttu-id="28b90-120">拼写错误的方法、 不正确的方法签名或不正确的中心名称</span><span class="sxs-lookup"><span data-stu-id="28b90-120">Misspelled method, incorrect method signature, or incorrect hub name</span></span>

<span data-ttu-id="28b90-121">如果名称或调用方法的签名与客户端上适当的方法不完全匹配，则调用将失败。</span><span class="sxs-lookup"><span data-stu-id="28b90-121">If the name or signature of a called method does not exactly match an appropriate method on the client, the call will fail.</span></span> <span data-ttu-id="28b90-122">验证服务器调用的方法名称与在客户端上方法的名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="28b90-122">Verify that the method name called by the server matches the name of the method on the client.</span></span> <span data-ttu-id="28b90-123">此外，SignalR 会创建集线器代理使用 camel 大小写的方法，以根据在 JavaScript 中，因此调用的方法`SendMessage`在服务器上则将调用`sendMessage`客户端代理中。</span><span class="sxs-lookup"><span data-stu-id="28b90-123">Also, SignalR creates the hub proxy using camel-cased methods, as is appropriate in JavaScript, so a method called `SendMessage` on the server would be called `sendMessage` in the client proxy.</span></span> <span data-ttu-id="28b90-124">如果使用`HubName`属性在服务器端代码中，验证所使用的名称与用来在客户端上创建中心的名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="28b90-124">If you use the `HubName` attribute in your server-side code, verify that the name used matches the name used to create the hub on the client.</span></span> <span data-ttu-id="28b90-125">如果不使用`HubName`属性，请验证是否驼峰式大小写，而不是 ChatHub chatHub 如 JavaScript 客户端中的中心的名称。</span><span class="sxs-lookup"><span data-stu-id="28b90-125">If you do not use the `HubName` attribute, verify that the name of the hub in a JavaScript client is camel-cased, such as chatHub instead of ChatHub.</span></span>

### <a name="duplicate-method-name-on-client"></a><span data-ttu-id="28b90-126">客户端上的方法名称重复</span><span class="sxs-lookup"><span data-stu-id="28b90-126">Duplicate method name on client</span></span>

<span data-ttu-id="28b90-127">验证仅大小写上不同的客户端上有了重复的方法。</span><span class="sxs-lookup"><span data-stu-id="28b90-127">Verify that you do not have a duplicate method on the client that differs only by case.</span></span> <span data-ttu-id="28b90-128">如果客户端应用程序具有一个名为方法`sendMessage`，验证是否未还调用的方法`SendMessage`也。</span><span class="sxs-lookup"><span data-stu-id="28b90-128">If your client application has a method called `sendMessage`, verify that there isn't also a method called `SendMessage` as well.</span></span>

### <a name="missing-json-parser-on-the-client"></a><span data-ttu-id="28b90-129">在客户端上缺少 JSON 分析器</span><span class="sxs-lookup"><span data-stu-id="28b90-129">Missing JSON parser on the client</span></span>

<span data-ttu-id="28b90-130">SignalR 需要 JSON 分析器必须存在要序列化的服务器和客户端之间的调用。</span><span class="sxs-lookup"><span data-stu-id="28b90-130">SignalR requires a JSON parser to be present to serialize calls between the server and the client.</span></span> <span data-ttu-id="28b90-131">如果您的客户端不具有内置的 JSON 分析器 （如 Internet Explorer 7)，你将需要包含一个应用程序中。</span><span class="sxs-lookup"><span data-stu-id="28b90-131">If your client doesn't have a built-in JSON parser (such as Internet Explorer 7), you'll need to include one in your application.</span></span> <span data-ttu-id="28b90-132">您可以下载 JSON 分析器[此处](http://nuget.org/packages/json2)。</span><span class="sxs-lookup"><span data-stu-id="28b90-132">You can download the JSON parser [here](http://nuget.org/packages/json2).</span></span>

### <a name="mixing-hub-and-persistentconnection-syntax"></a><span data-ttu-id="28b90-133">混合使用中心 PersistentConnection 语法</span><span class="sxs-lookup"><span data-stu-id="28b90-133">Mixing Hub and PersistentConnection syntax</span></span>

<span data-ttu-id="28b90-134">SignalR 使用两个通信模型：中心和 PersistentConnections。</span><span class="sxs-lookup"><span data-stu-id="28b90-134">SignalR uses two communication models: Hubs and PersistentConnections.</span></span> <span data-ttu-id="28b90-135">调用这两种通信模型的语法中是不同的客户端代码。</span><span class="sxs-lookup"><span data-stu-id="28b90-135">The syntax for calling these two communication models is different in the client code.</span></span> <span data-ttu-id="28b90-136">如果在服务器代码中添加了一个中心，验证所有客户端代码中使用正确的中心语法。</span><span class="sxs-lookup"><span data-stu-id="28b90-136">If you have added a hub in your server code, verify that all of your client code uses the proper hub syntax.</span></span>

<span data-ttu-id="28b90-137">**在 JavaScript 客户端中创建 PersistentConnection 的 JavaScript 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="28b90-137">**JavaScript client code that creates a PersistentConnection in a JavaScript client**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample1.js)]

<span data-ttu-id="28b90-138">**在 Javascript 客户端创建集线器代理的 JavaScript 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="28b90-138">**JavaScript client code that creates a Hub Proxy in a Javascript client**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample2.js)]

<span data-ttu-id="28b90-139">**将路由映射到 PersistentConnection 的 C# 服务器代码**</span><span class="sxs-lookup"><span data-stu-id="28b90-139">**C# server code that maps a route to a PersistentConnection**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample3.cs)]

<span data-ttu-id="28b90-140">**如果有多个应用程序映射路由到集线器，或多个中心的 C# 服务器代码**</span><span class="sxs-lookup"><span data-stu-id="28b90-140">**C# server code that maps a route to a Hub, or to mulitple hubs if you have multiple applications**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample4.cs)]

### <a name="connection-started-before-subscriptions-are-added"></a><span data-ttu-id="28b90-141">添加订阅之前启动的连接</span><span class="sxs-lookup"><span data-stu-id="28b90-141">Connection started before subscriptions are added</span></span>

<span data-ttu-id="28b90-142">如果可以从服务器调用的方法添加到代理之前启动中心的连接，则将不会收到消息。</span><span class="sxs-lookup"><span data-stu-id="28b90-142">If the Hub's connection is started before methods that can be called from the server are added to the proxy, messages will not be received.</span></span> <span data-ttu-id="28b90-143">以下 JavaScript 代码将不会正确启动中心：</span><span class="sxs-lookup"><span data-stu-id="28b90-143">The following JavaScript code will not start the hub properly:</span></span>

<span data-ttu-id="28b90-144">**将不允许中心以接收的消息的不正确的 JavaScript 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="28b90-144">**Incorrect JavaScript client code that will not allow Hubs messages to be received**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample5.js)]

<span data-ttu-id="28b90-145">相反，将方法订阅添加调用开始之前：</span><span class="sxs-lookup"><span data-stu-id="28b90-145">Instead, add the method subscriptions before calling Start:</span></span>

<span data-ttu-id="28b90-146">**正确地将订阅添加到集线器的 JavaScript 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="28b90-146">**JavaScript client code that correctly adds subscriptions to a hub**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample6.js)]

### <a name="missing-method-name-on-the-hub-proxy"></a><span data-ttu-id="28b90-147">缺少的集线器代理的方法名称</span><span class="sxs-lookup"><span data-stu-id="28b90-147">Missing method name on the hub proxy</span></span>

<span data-ttu-id="28b90-148">验证在服务器上定义的方法在客户端上订阅了。</span><span class="sxs-lookup"><span data-stu-id="28b90-148">Verify that the method defined on the server is subscribed to on the client.</span></span> <span data-ttu-id="28b90-149">即使服务器定义的方法，则它必须仍将添加到客户端代理。</span><span class="sxs-lookup"><span data-stu-id="28b90-149">Even though the server defines the method, it must still be added to the client proxy.</span></span> <span data-ttu-id="28b90-150">方法可以按以下方式添加到客户端代理 (请注意，该方法添加到`client`的中心，而不是中心直接成员):</span><span class="sxs-lookup"><span data-stu-id="28b90-150">Methods can be added to the client proxy in the following ways (Note that the method is added to the `client` member of the hub, not the hub directly):</span></span>

<span data-ttu-id="28b90-151">**将方法添加到集线器代理的 JavaScript 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="28b90-151">**JavaScript client code that adds methods to a hub proxy**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample7.js)]

### <a name="hub-or-hub-methods-not-declared-as-public"></a><span data-ttu-id="28b90-152">中心或未声明为公共的集线器方法</span><span class="sxs-lookup"><span data-stu-id="28b90-152">Hub or hub methods not declared as Public</span></span>

<span data-ttu-id="28b90-153">若要在客户端上可见，中心实现和方法必须声明为`public`。</span><span class="sxs-lookup"><span data-stu-id="28b90-153">To be visible on the client, the hub implementation and methods must be declared as `public`.</span></span>

### <a name="accessing-hub-from-a-different-application"></a><span data-ttu-id="28b90-154">从不同的应用程序访问中心</span><span class="sxs-lookup"><span data-stu-id="28b90-154">Accessing hub from a different application</span></span>

<span data-ttu-id="28b90-155">SignalR 集线器仅可以通过实现 SignalR 客户端的应用程序访问。</span><span class="sxs-lookup"><span data-stu-id="28b90-155">SignalR Hubs can only be accessed through applications that implement SignalR clients.</span></span> <span data-ttu-id="28b90-156">SignalR 不能与其他通信库 （例如 SOAP 或 WCF web 服务。） 进行互操作如果没有可用于您的目标平台的无 SignalR 客户端，您不能直接访问服务器的终结点。</span><span class="sxs-lookup"><span data-stu-id="28b90-156">SignalR cannot interoperate with other communication libraries (like SOAP or WCF web services.) If there is no SignalR client available for your target platform, you cannot access the server's endpoint directly.</span></span>

### <a name="manually-serializing-data"></a><span data-ttu-id="28b90-157">手动序列化数据</span><span class="sxs-lookup"><span data-stu-id="28b90-157">Manually serializing data</span></span>

<span data-ttu-id="28b90-158">SignalR 将自动使用 JSON 进行序列化您的方法参数-这不需要自行执行。</span><span class="sxs-lookup"><span data-stu-id="28b90-158">SignalR will automatically use JSON to serialize your method parameters- there's no need to do it yourself.</span></span>

### <a name="remote-hub-method-not-executed-on-client-in-ondisconnected-function"></a><span data-ttu-id="28b90-159">OnDisconnected 函数中的客户端上不执行远程中心方法</span><span class="sxs-lookup"><span data-stu-id="28b90-159">Remote Hub method not executed on client in OnDisconnected function</span></span>

<span data-ttu-id="28b90-160">此行为是有意安排的。</span><span class="sxs-lookup"><span data-stu-id="28b90-160">This behavior is by design.</span></span> <span data-ttu-id="28b90-161">当`OnDisconnected`是调用，已进入中心`Disconnected`状态，不允许进一步集线器方法调用。</span><span class="sxs-lookup"><span data-stu-id="28b90-161">When `OnDisconnected` is called, the hub has already entered the `Disconnected` state, which does not allow further hub methods to be called.</span></span>

<span data-ttu-id="28b90-162">**正确 OnDisconnected 事件中执行代码的 C# 服务器代码**</span><span class="sxs-lookup"><span data-stu-id="28b90-162">**C# server code that correctly executes code in the OnDisconnected event**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample8.cs)]

### <a name="connection-limit-reached"></a><span data-ttu-id="28b90-163">已达到连接限制</span><span class="sxs-lookup"><span data-stu-id="28b90-163">Connection limit reached</span></span>

<span data-ttu-id="28b90-164">当在 Windows 7 等客户端操作系统上使用 IIS 的完整版本，10 连接限制。</span><span class="sxs-lookup"><span data-stu-id="28b90-164">When using the full version of IIS on a client operating system like Windows 7, a 10-connection limit is imposed.</span></span> <span data-ttu-id="28b90-165">当使用客户端操作系统，改用 IIS Express 以避免此限制。</span><span class="sxs-lookup"><span data-stu-id="28b90-165">When using a client OS, use IIS Express instead to avoid this limit.</span></span>

### <a name="cross-domain-connection-not-set-up-properly"></a><span data-ttu-id="28b90-166">未正确设置跨域连接</span><span class="sxs-lookup"><span data-stu-id="28b90-166">Cross-domain connection not set up properly</span></span>

<span data-ttu-id="28b90-167">如果跨域连接 （连接为其 SignalR URL 不是在托管的页面所在的域中） 未正确设置、 连接而不显示错误消息可能会失败。</span><span class="sxs-lookup"><span data-stu-id="28b90-167">If a cross-domain connection (a connection for which the SignalR URL is not in the same domain as the hosting page) is not set up correctly, the connection may fail without an error message.</span></span> <span data-ttu-id="28b90-168">有关如何启用跨域通信的信息，请参阅[如何建立跨域连接](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain)。</span><span class="sxs-lookup"><span data-stu-id="28b90-168">For information on how to enable cross-domain communication, see [How to establish a cross-domain connection](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).</span></span>

### <a name="connection-using-ntlm-active-directory-not-working-in-net-client"></a><span data-ttu-id="28b90-169">连接使用 NTLM (Active Directory) 中.NET 客户端无法正常工作</span><span class="sxs-lookup"><span data-stu-id="28b90-169">Connection using NTLM (Active Directory) not working in .NET client</span></span>

<span data-ttu-id="28b90-170">如果连接未正确配置，使用域安全的.NET 客户端应用程序中的连接可能会失败。</span><span class="sxs-lookup"><span data-stu-id="28b90-170">A connection in a .NET client application that uses Domain security may fail if the connection is not configured properly.</span></span> <span data-ttu-id="28b90-171">若要在域环境中使用 SignalR，请按如下所示设置必要的连接属性：</span><span class="sxs-lookup"><span data-stu-id="28b90-171">To use SignalR in a domain environment, set the requisite connection property as follows:</span></span>

<span data-ttu-id="28b90-172">**实现连接凭据的 C# 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="28b90-172">**C# client code that implements connection credentials**</span></span>

[!code-csharp[Main](troubleshooting/samples/sample9.cs)]

<a id="other"></a>

## <a name="other-connection-issues"></a><span data-ttu-id="28b90-173">其他的连接问题</span><span class="sxs-lookup"><span data-stu-id="28b90-173">Other connection issues</span></span>

<span data-ttu-id="28b90-174">本部分介绍的原因和解决方案的具体的症状或在连接期间发生的错误消息。</span><span class="sxs-lookup"><span data-stu-id="28b90-174">This section describes the causes and solutions for specific symptoms or error messages that occur during a connection.</span></span>

### <a name="start-must-be-called-before-data-can-be-sent-error"></a><span data-ttu-id="28b90-175">"可以发送数据之前，必须调用 start"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-175">"Start must be called before data can be sent" error</span></span>

<span data-ttu-id="28b90-176">如果代码引用 SignalR 对象之前启动连接，则通常会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="28b90-176">This error is commonly seen if code references SignalR objects before the connection is started.</span></span> <span data-ttu-id="28b90-177">处理程序和类似的绑定，将在服务器上定义的调用方法必须添加在连接完成之后。</span><span class="sxs-lookup"><span data-stu-id="28b90-177">The wireup for handlers and the like that will call methods defined on the server must be added after the connection completes.</span></span> <span data-ttu-id="28b90-178">请注意，在调用`Start`是异步的因此代码调用可能会在它之前执行后完成。</span><span class="sxs-lookup"><span data-stu-id="28b90-178">Note that the call to `Start` is asynchronous, so code after the call may be executed before it completes.</span></span> <span data-ttu-id="28b90-179">连接完全启动后添加处理程序的最佳方法是将它们放入作为参数传递给启动方法的回调函数：</span><span class="sxs-lookup"><span data-stu-id="28b90-179">The best way to add handlers after a connection starts completely is to put them into a callback function that is passed as a parameter to the start method:</span></span>

<span data-ttu-id="28b90-180">**正确添加了引用 SignalR 对象的事件处理程序的 JavaScript 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="28b90-180">**JavaScript client code that correctly adds event handlers that reference SignalR objects**</span></span>

[!code-javascript[Main](troubleshooting/samples/sample10.js?highlight=1)]

<span data-ttu-id="28b90-181">如果连接停止时仍引用 SignalR 对象，则还将看到此错误。</span><span class="sxs-lookup"><span data-stu-id="28b90-181">This error will also be seen if a connection stops while SignalR objects are still being referenced.</span></span>

### <a name="301-moved-permanently-or-302-moved-temporarily-error"></a><span data-ttu-id="28b90-182">"301 已永久移动"或者"302 已暂时移动"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-182">"301 Moved Permanently" or "302 Moved Temporarily" error</span></span>

<span data-ttu-id="28b90-183">如果项目包含一个名为 SignalR 中，将会影响自动创建代理的文件夹，则可能出现此错误。</span><span class="sxs-lookup"><span data-stu-id="28b90-183">This error may be seen if the project contains a folder called SignalR, which will interfere with the automatically-created proxy.</span></span> <span data-ttu-id="28b90-184">若要避免此错误，不使用名为的文件夹`SignalR`中你的应用程序或关闭启用自动代理生成。</span><span class="sxs-lookup"><span data-stu-id="28b90-184">To avoid this error, do not use a folder called `SignalR` in your application, or turn automatic proxy generation off.</span></span> <span data-ttu-id="28b90-185">请参阅[生成代理和它为您完成](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy)的更多详细信息。</span><span class="sxs-lookup"><span data-stu-id="28b90-185">See [The Generated Proxy and what it does for you](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy) for more details.</span></span>

### <a name="403-forbidden-error-in-net-or-silverlight-client"></a><span data-ttu-id="28b90-186">.NET 或 Silverlight 客户端中的"403 禁止访问"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-186">"403 Forbidden" error in .NET or Silverlight client</span></span>

<span data-ttu-id="28b90-187">在跨域通信未正确启用跨域环境中可能出现此错误。</span><span class="sxs-lookup"><span data-stu-id="28b90-187">This error may occur in cross-domain environments where cross-domain communication is not properly enabled.</span></span> <span data-ttu-id="28b90-188">有关如何启用跨域通信的信息，请参阅[如何建立跨域连接](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain)。</span><span class="sxs-lookup"><span data-stu-id="28b90-188">For information on how to enable cross-domain communication, see [How to establish a cross-domain connection](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain).</span></span> <span data-ttu-id="28b90-189">若要建立 Silverlight 客户端中的跨域连接，请参阅[Silverlight 客户端的跨域连接](../guide-to-the-api/hubs-api-guide-net-client.md#slcrossdomain)。</span><span class="sxs-lookup"><span data-stu-id="28b90-189">To establish a cross-domain connection in a Silverlight client, see [Cross-domain connections from Silverlight clients](../guide-to-the-api/hubs-api-guide-net-client.md#slcrossdomain).</span></span>

### <a name="404-not-found-error"></a><span data-ttu-id="28b90-190">"404 未找到"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-190">"404 Not Found" error</span></span>

<span data-ttu-id="28b90-191">有多个此问题的原因。</span><span class="sxs-lookup"><span data-stu-id="28b90-191">There are several causes for this issue.</span></span> <span data-ttu-id="28b90-192">验证以下所有内容的：</span><span class="sxs-lookup"><span data-stu-id="28b90-192">Verify all of the following:</span></span>

- <span data-ttu-id="28b90-193">**集线器代理地址引用的格式不正确：** 如果对生成的中心代理地址的引用的格式不正确，则通常会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="28b90-193">**Hub proxy address reference not formatted correctly:** This error is commonly seen if the reference to the generated hub proxy address is not formatted correctly.</span></span> <span data-ttu-id="28b90-194">验证可以正确地完成对中心地址的引用。</span><span class="sxs-lookup"><span data-stu-id="28b90-194">Verify that the reference to the hub address is made properly.</span></span> <span data-ttu-id="28b90-195">请参阅[如何引用动态生成的代理](../guide-to-the-api/hubs-api-guide-javascript-client.md#dynamicproxy)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="28b90-195">See [How to reference the dynamically generated proxy](../guide-to-the-api/hubs-api-guide-javascript-client.md#dynamicproxy) for details.</span></span>
- <span data-ttu-id="28b90-196">**将路由添加到应用程序，然后添加中心路由：** 如果应用程序使用其他路由，请验证添加的第一个路由是对的调用`MapHubs`。</span><span class="sxs-lookup"><span data-stu-id="28b90-196">**Adding routes to application before adding the hub route:** If your application uses other routes, verify that the first route added is the call to `MapHubs`.</span></span>

### <a name="500-internal-server-error"></a><span data-ttu-id="28b90-197">"500 内部服务器错误"</span><span class="sxs-lookup"><span data-stu-id="28b90-197">"500 Internal Server Error"</span></span>

<span data-ttu-id="28b90-198">这是一个非常一般性错误，可能有各种不同的原因。</span><span class="sxs-lookup"><span data-stu-id="28b90-198">This is a very generic error that could have a wide variety of causes.</span></span> <span data-ttu-id="28b90-199">错误的详细信息应出现在服务器的事件日志，或可通过调试服务器查找。</span><span class="sxs-lookup"><span data-stu-id="28b90-199">The details of the error should appear in the server's event log, or can be found through debugging the server.</span></span> <span data-ttu-id="28b90-200">可通过启用详细错误的服务器上获取更详细的错误信息。</span><span class="sxs-lookup"><span data-stu-id="28b90-200">More detailed error information may be obtained by turning on detailed errors on the server.</span></span> <span data-ttu-id="28b90-201">有关详细信息，请参阅[如何处理错误，Hub 类](../guide-to-the-api/hubs-api-guide-server.md#handleErrors)。</span><span class="sxs-lookup"><span data-stu-id="28b90-201">For more information, see [How to handle errors in the Hub class](../guide-to-the-api/hubs-api-guide-server.md#handleErrors).</span></span>

### <a name="typeerror-lthubtypegt-is-undefined-error"></a><span data-ttu-id="28b90-202">"TypeError: &lt;hubType&gt;未定义"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-202">"TypeError: &lt;hubType&gt; is undefined" error</span></span>

<span data-ttu-id="28b90-203">如果此错误将导致调用`MapHubs`进行不正确。</span><span class="sxs-lookup"><span data-stu-id="28b90-203">This error will result if the call to `MapHubs` is not made properly.</span></span> <span data-ttu-id="28b90-204">请参阅[如何注册 SignalR 路由和配置 SignalR 选项](../guide-to-the-api/hubs-api-guide-server.md#route)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="28b90-204">See [How to register the SignalR route and configure SignalR options](../guide-to-the-api/hubs-api-guide-server.md#route) for more information.</span></span>

### <a name="jsonserializationexception-was-unhandled-by-user-code"></a><span data-ttu-id="28b90-205">用户代码未处理 JsonSerializationException</span><span class="sxs-lookup"><span data-stu-id="28b90-205">JsonSerializationException was unhandled by user code</span></span>

<span data-ttu-id="28b90-206">验证发送到你的方法的参数不包括非可序列化类型 （如文件句柄或数据库连接）。</span><span class="sxs-lookup"><span data-stu-id="28b90-206">Verify that the parameters you send to your methods do not include non-serializable types (like file handles or database connections).</span></span> <span data-ttu-id="28b90-207">如果你需要在不想要发送到客户端 （不管是安全或者序列化的原因），使用服务器端对象上使用成员`JSONIgnore`属性。</span><span class="sxs-lookup"><span data-stu-id="28b90-207">If you need to use members on a server-side object that you don't want to be sent to the client (either for security or for reasons of serialization), use the `JSONIgnore` attribute.</span></span>

### <a name="protocol-error-unknown-transport-error"></a><span data-ttu-id="28b90-208">"协议错误：未知的传输"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-208">"Protocol error: Unknown transport" error</span></span>

<span data-ttu-id="28b90-209">如果客户端不支持 SignalR 使用的传输，则可能出现此错误。</span><span class="sxs-lookup"><span data-stu-id="28b90-209">This error may occur if the client does not support the transports that SignalR uses.</span></span> <span data-ttu-id="28b90-210">请参阅[传输和回退](../getting-started/introduction-to-signalr.md#transports)与 SignalR 可以在其上使用浏览器的信息。</span><span class="sxs-lookup"><span data-stu-id="28b90-210">See [Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports) for information on which browsers can be used with SignalR.</span></span>

### <a name="javascript-hub-proxy-generation-has-been-disabled"></a><span data-ttu-id="28b90-211">"JavaScript 中心代理生成已禁用。"</span><span class="sxs-lookup"><span data-stu-id="28b90-211">"JavaScript Hub proxy generation has been disabled."</span></span>

<span data-ttu-id="28b90-212">如果将发生此错误`DisableJavaScriptProxies`时还包括对在动态生成的代理的引用设置`signalr/hubs`。</span><span class="sxs-lookup"><span data-stu-id="28b90-212">This error will occur if `DisableJavaScriptProxies` is set while also including a reference to the dynamically generated proxy at `signalr/hubs`.</span></span> <span data-ttu-id="28b90-213">有关手动创建代理的详细信息，请参阅[生成的代理和它为您完成](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy)。</span><span class="sxs-lookup"><span data-stu-id="28b90-213">For more information on creating the proxy manually, see [The generated proxy and what it does for you](../guide-to-the-api/hubs-api-guide-javascript-client.md#genproxy).</span></span>

### <a name="the-connection-id-is-in-the-incorrect-format-or-the-user-identity-cannot-change-during-an-active-signalr-connection-error"></a><span data-ttu-id="28b90-214">"连接 ID 的格式不正确"或"用户标识不能更改期间的活动的 SignalR 连接"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-214">"The connection ID is in the incorrect format" or "The user identity cannot change during an active SignalR connection" error</span></span>

<span data-ttu-id="28b90-215">如果正在使用身份验证，并且客户端已注销，停止连接之前，可能会出现此错误。</span><span class="sxs-lookup"><span data-stu-id="28b90-215">This error may be seen if authentication is being used, and the client is logged out before the connection is stopped.</span></span> <span data-ttu-id="28b90-216">解决方案是停止 SignalR 连接之前注销客户端。</span><span class="sxs-lookup"><span data-stu-id="28b90-216">The solution is to stop the SignalR connection before logging the client out.</span></span>

### <a name="uncaught-error-signalr-jquery-not-found-please-ensure-jquery-is-referenced-before-the-signalrjs-file-error"></a><span data-ttu-id="28b90-217">"未捕获的错误：SignalR： 找不到 jQuery。</span><span class="sxs-lookup"><span data-stu-id="28b90-217">"Uncaught Error: SignalR: jQuery not found.</span></span> <span data-ttu-id="28b90-218">请确保前 SignalR.js 文件引用 jQuery"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-218">Please ensure jQuery is referenced before the SignalR.js file" error</span></span>

<span data-ttu-id="28b90-219">SignalR JavaScript 客户端需要 jQuery 来运行。</span><span class="sxs-lookup"><span data-stu-id="28b90-219">The SignalR JavaScript client requires jQuery to run.</span></span> <span data-ttu-id="28b90-220">验证你对 jQuery 的引用正确，所用的路径有效，以及对 jQuery 的引用是对 SignalR 的引用之前。</span><span class="sxs-lookup"><span data-stu-id="28b90-220">Verify that your reference to jQuery is correct, that the path used is valid, and that the reference to jQuery is before the reference to SignalR.</span></span>

### <a name="uncaught-typeerror-cannot-read-property-ltpropertygt-of-undefined-error"></a><span data-ttu-id="28b90-221">"未捕获的 TypeError:无法读取属性&lt;属性&gt;未定义的"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-221">"Uncaught TypeError: Cannot read property '&lt;property&gt;' of undefined" error</span></span>

<span data-ttu-id="28b90-222">防止不 jQuery 或引用正确的中心代理产生此错误。</span><span class="sxs-lookup"><span data-stu-id="28b90-222">This error results from not having jQuery or the hubs proxy referenced properly.</span></span> <span data-ttu-id="28b90-223">验证引用与 jQuery 和中心代理正确，所用的路径有效，以及对 jQuery 的引用是对中心代理的引用之前。</span><span class="sxs-lookup"><span data-stu-id="28b90-223">Verify that your reference to jQuery and the hubs proxy is correct, that the path used is valid, and that the reference to jQuery is before the reference to the hubs proxy.</span></span> <span data-ttu-id="28b90-224">集线器代理的默认引用应如下所示：</span><span class="sxs-lookup"><span data-stu-id="28b90-224">The default reference to the hubs proxy should look like the following:</span></span>

<span data-ttu-id="28b90-225">**HTML 客户端代码的正确引用中心代理**</span><span class="sxs-lookup"><span data-stu-id="28b90-225">**HTML client-side code that correctly references the Hubs proxy**</span></span>

[!code-html[Main](troubleshooting/samples/sample11.html)]

### <a name="runtimebinderexception-was-unhandled-by-user-code-error"></a><span data-ttu-id="28b90-226">"通过用户代码未处理 RuntimeBinderException"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-226">"RuntimeBinderException was unhandled by user code" error</span></span>

<span data-ttu-id="28b90-227">可能会发生此错误时的不正确的重载`Hub.On`使用。</span><span class="sxs-lookup"><span data-stu-id="28b90-227">This error may occur when the incorrect overload of `Hub.On` is used.</span></span> <span data-ttu-id="28b90-228">如果该方法具有返回值，则必须作为泛型类型参数指定的返回类型：</span><span class="sxs-lookup"><span data-stu-id="28b90-228">If the method has a return value, the return type must be specified as a generic type parameter:</span></span>

<span data-ttu-id="28b90-229">**（无需生成的代理） 客户端上定义的方法**</span><span class="sxs-lookup"><span data-stu-id="28b90-229">**Method defined on the client (without generated proxy)**</span></span>

[!code-html[Main](troubleshooting/samples/sample12.html?highlight=1)]

### <a name="connection-id-is-inconsistent-or-connection-breaks-between-page-loads"></a><span data-ttu-id="28b90-230">连接 ID 不一致或页面加载之间的连接中断</span><span class="sxs-lookup"><span data-stu-id="28b90-230">Connection ID is inconsistent or connection breaks between page loads</span></span>

<span data-ttu-id="28b90-231">此行为是有意安排的。</span><span class="sxs-lookup"><span data-stu-id="28b90-231">This behavior is by design.</span></span> <span data-ttu-id="28b90-232">由于集线器对象托管在 page 对象中，中心被销毁时刷新该页面。</span><span class="sxs-lookup"><span data-stu-id="28b90-232">Since the hub object is hosted in the page object, the hub is destroyed when the page refreshes.</span></span> <span data-ttu-id="28b90-233">多页应用程序需要维护用户与连接 Id 之间的关联，以便将页面加载之间保持一致。</span><span class="sxs-lookup"><span data-stu-id="28b90-233">A multi-page application needs to maintain the association between users and connection IDs so that they will be consistent between page loads.</span></span> <span data-ttu-id="28b90-234">可以在服务器上存储的连接 Id`ConcurrentDictionary`对象或数据库。</span><span class="sxs-lookup"><span data-stu-id="28b90-234">The connection IDs can be stored on the server in either a `ConcurrentDictionary` object or a database.</span></span>

### <a name="value-cannot-be-null-error"></a><span data-ttu-id="28b90-235">"值不能为 null"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-235">"Value cannot be null" error</span></span>

<span data-ttu-id="28b90-236">当前不支持具有可选参数的服务器端方法;如果省略了可选参数，则该方法将失败。</span><span class="sxs-lookup"><span data-stu-id="28b90-236">Server-side methods with optional parameters are not currently supported; if the optional parameter is omitted, the method will fail.</span></span> <span data-ttu-id="28b90-237">有关详细信息，请参阅[可选参数](https://github.com/SignalR/SignalR/issues/324)。</span><span class="sxs-lookup"><span data-stu-id="28b90-237">For more information, see [Optional Parameters](https://github.com/SignalR/SignalR/issues/324).</span></span>

### <a name="firefox-cant-establish-a-connection-to-the-server-at-ltaddressgt-error-in-firebug"></a><span data-ttu-id="28b90-238">"Firefox 无法建立与服务器上的连接&lt;地址&gt;"在 Firebug 中的错误</span><span class="sxs-lookup"><span data-stu-id="28b90-238">"Firefox can't establish a connection to the server at &lt;address&gt;" error in Firebug</span></span>

<span data-ttu-id="28b90-239">如果协商的 WebSocket 传输会失败，而是使用另一个传输，可在 Firebug 中出现此错误消息。</span><span class="sxs-lookup"><span data-stu-id="28b90-239">This error message can be seen in Firebug if negotiation of the WebSocket transport fails and another transport is used instead.</span></span> <span data-ttu-id="28b90-240">此行为是有意安排的。</span><span class="sxs-lookup"><span data-stu-id="28b90-240">This behavior is by design.</span></span>

### <a name="the-remote-certificate-is-invalid-according-to-the-validation-procedure-error-in-net-client-application"></a><span data-ttu-id="28b90-241">在.NET 客户端应用程序中的"远程证书无效，验证过程根据"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-241">"The remote certificate is invalid according to the validation procedure" error in .NET client application</span></span>

<span data-ttu-id="28b90-242">如果你的服务器要求自定义客户端证书，然后可以将 x509 证书添加到连接之前发出请求。</span><span class="sxs-lookup"><span data-stu-id="28b90-242">If your server requires custom client certificates, then you can add an x509certificate to the connection before the request is made.</span></span> <span data-ttu-id="28b90-243">将证书添加到连接使用`Connection.AddClientCertificate`。</span><span class="sxs-lookup"><span data-stu-id="28b90-243">Add the certificate to the connection using `Connection.AddClientCertificate`.</span></span>

### <a name="connection-drops-after-authentication-times-out"></a><span data-ttu-id="28b90-244">连接中断后身份验证超时</span><span class="sxs-lookup"><span data-stu-id="28b90-244">Connection drops after authentication times out</span></span>

<span data-ttu-id="28b90-245">此行为是有意安排的。</span><span class="sxs-lookup"><span data-stu-id="28b90-245">This behavior is by design.</span></span> <span data-ttu-id="28b90-246">当连接处于活动状态; 不能修改身份验证凭据若要刷新的凭据，必须停止并重新启动连接。</span><span class="sxs-lookup"><span data-stu-id="28b90-246">Authentication credentials cannot be modified while a connection is active; to refresh credentials, the connection must be stopped and restarted.</span></span>

### <a name="onconnected-gets-called-twice-when-using-jquery-mobile"></a><span data-ttu-id="28b90-247">OnConnected 被调用两次时使用的 jQuery Mobile</span><span class="sxs-lookup"><span data-stu-id="28b90-247">OnConnected gets called twice when using jQuery Mobile</span></span>

<span data-ttu-id="28b90-248">jQuery Mobile`initializePage`函数强制重新执行，每个页面中的脚本，从而创建第二个连接。</span><span class="sxs-lookup"><span data-stu-id="28b90-248">jQuery Mobile's `initializePage` function forces the scripts in each page to be re-executed, thus creating a second connection.</span></span> <span data-ttu-id="28b90-249">此问题的解决方案包括：</span><span class="sxs-lookup"><span data-stu-id="28b90-249">Solutions for this issue include:</span></span>

- <span data-ttu-id="28b90-250">包括对你的 JavaScript 文件之前的 jQuery Mobile 的引用。</span><span class="sxs-lookup"><span data-stu-id="28b90-250">Include the reference to jQuery Mobile before your JavaScript file.</span></span>
- <span data-ttu-id="28b90-251">禁用`initializePage`函数通过设置`$.mobile.autoInitializePage = false`。</span><span class="sxs-lookup"><span data-stu-id="28b90-251">Disable the `initializePage` function by setting `$.mobile.autoInitializePage = false`.</span></span>
- <span data-ttu-id="28b90-252">等待页后，可以完成初始化之前启动连接。</span><span class="sxs-lookup"><span data-stu-id="28b90-252">Wait for the page to finish initializing before starting the connection.</span></span>

### <a name="messages-are-delayed-in-silverlight-applications-using-server-sent-events"></a><span data-ttu-id="28b90-253">消息被延迟在 Silverlight 应用程序中使用服务器发送事件</span><span class="sxs-lookup"><span data-stu-id="28b90-253">Messages are delayed in Silverlight applications using Server Sent Events</span></span>

<span data-ttu-id="28b90-254">在 Silverlight 中使用服务器发送事件时，消息被延迟。</span><span class="sxs-lookup"><span data-stu-id="28b90-254">Messages are delayed when using server sent events on Silverlight.</span></span> <span data-ttu-id="28b90-255">若要强制长轮询以改为使用，使用以下命令启动连接时：</span><span class="sxs-lookup"><span data-stu-id="28b90-255">To force long polling to be used instead, use the following when starting the connection:</span></span>

[!code-css[Main](troubleshooting/samples/sample13.css)]

### <a name="permission-denied-using-forever-frame-protocol"></a><span data-ttu-id="28b90-256">使用"权限被拒绝"永久帧协议</span><span class="sxs-lookup"><span data-stu-id="28b90-256">"Permission Denied" using Forever Frame protocol</span></span>

<span data-ttu-id="28b90-257">这是一个已知的问题，所述[此处](https://github.com/SignalR/SignalR/issues/1963)。</span><span class="sxs-lookup"><span data-stu-id="28b90-257">This is a known issue, described [here](https://github.com/SignalR/SignalR/issues/1963).</span></span> <span data-ttu-id="28b90-258">这种现象，可能会看到使用最新的 JQuery 库;解决方法是降级到 JQuery 1.8.2 应用程序。</span><span class="sxs-lookup"><span data-stu-id="28b90-258">This symptom may be seen using the latest JQuery library; the workaround is to downgrade your application to JQuery 1.8.2.</span></span>

<a id="server"></a>

## <a name="compilation-and-server-side-errors"></a><span data-ttu-id="28b90-259">编译和服务器端错误</span><span class="sxs-lookup"><span data-stu-id="28b90-259">Compilation and server-side errors</span></span>

 <span data-ttu-id="28b90-260">以下部分包含对编译器和服务器端运行时错误可能的解决方案。</span><span class="sxs-lookup"><span data-stu-id="28b90-260">The following section contains possible solutions to compiler and server-side runtime errors.</span></span> 

### <a name="reference-to-hub-instance-is-null"></a><span data-ttu-id="28b90-261">到中心实例的引用为 null</span><span class="sxs-lookup"><span data-stu-id="28b90-261">Reference to Hub instance is null</span></span>

<span data-ttu-id="28b90-262">由于每个连接创建集线器实例时，您不能自己创建实例的中心在代码中。</span><span class="sxs-lookup"><span data-stu-id="28b90-262">Since a hub instance is created for each connection, you can't create an instance of a hub in your code yourself.</span></span> <span data-ttu-id="28b90-263">若要对集线器从中心本身之外调用方法，请参阅[如何调用方法的客户端和管理的中心类外部的组](../guide-to-the-api/hubs-api-guide-server.md#callfromoutsidehub)有关如何获取对的集线器上下文的引用。</span><span class="sxs-lookup"><span data-stu-id="28b90-263">To call methods on a hub from outside the hub itself, see [How to call client methods and manage groups from outside the Hub class](../guide-to-the-api/hubs-api-guide-server.md#callfromoutsidehub) for how to obtain a reference to the hub context.</span></span>

### <a name="httpcontextcurrentsession-is-null"></a><span data-ttu-id="28b90-264">HTTPContext.Current.Session 为 null</span><span class="sxs-lookup"><span data-stu-id="28b90-264">HTTPContext.Current.Session is null</span></span>

<span data-ttu-id="28b90-265">此行为是有意安排的。</span><span class="sxs-lookup"><span data-stu-id="28b90-265">This behavior is by design.</span></span> <span data-ttu-id="28b90-266">SignalR 不支持 ASP.NET 会话状态，因为启用会话状态将会破坏双工消息传递。</span><span class="sxs-lookup"><span data-stu-id="28b90-266">SignalR does not support the ASP.NET session state, since enabling the session state would break duplex messaging.</span></span>

### <a name="no-suitable-method-to-override"></a><span data-ttu-id="28b90-267">没有合适的方法重写</span><span class="sxs-lookup"><span data-stu-id="28b90-267">No suitable method to override</span></span>

<span data-ttu-id="28b90-268">如果使用较旧的文档或博客中的代码，可能会看到此错误。</span><span class="sxs-lookup"><span data-stu-id="28b90-268">You may see this error if you are using code from older documentation or blogs.</span></span> <span data-ttu-id="28b90-269">验证是否未引用的已更改或已弃用的方法名称 (如`OnConnectedAsync`)。</span><span class="sxs-lookup"><span data-stu-id="28b90-269">Verify that you are not referencing names of methods that have been changed or deprecated (like `OnConnectedAsync`).</span></span>

### <a name="hostcontextextensionswebsocketserverurl-is-null"></a><span data-ttu-id="28b90-270">HostContextExtensions.WebSocketServerUrl 为 null</span><span class="sxs-lookup"><span data-stu-id="28b90-270">HostContextExtensions.WebSocketServerUrl is null</span></span>

<span data-ttu-id="28b90-271">此行为是有意安排的。</span><span class="sxs-lookup"><span data-stu-id="28b90-271">This behavior is by design.</span></span> <span data-ttu-id="28b90-272">此成员已弃用，不应使用。</span><span class="sxs-lookup"><span data-stu-id="28b90-272">This member is deprecated and should not be used.</span></span>

### <a name="a-route-named-signalrhubs-is-already-in-the-route-collection-error"></a><span data-ttu-id="28b90-273">"名为 signalr.hubs 的路由已路由集合中"错误</span><span class="sxs-lookup"><span data-stu-id="28b90-273">"A route named 'signalr.hubs' is already in the route collection" error</span></span>

<span data-ttu-id="28b90-274">将出现此错误，如果`MapHubs`两次由应用程序调用。</span><span class="sxs-lookup"><span data-stu-id="28b90-274">This error will be seen if `MapHubs` is called twice by your application.</span></span> <span data-ttu-id="28b90-275">某些示例应用程序调用`MapHubs`直接在全局应用程序文件中; 其他人进行调用包装器类中的。</span><span class="sxs-lookup"><span data-stu-id="28b90-275">Some example applications call `MapHubs` directly in the global application file; others make the call in a wrapper class.</span></span> <span data-ttu-id="28b90-276">请确保，你的应用程序并未同时。</span><span class="sxs-lookup"><span data-stu-id="28b90-276">Ensure that your application does not do both.</span></span>

<a id="vs"></a>

## <a name="visual-studio-issues"></a><span data-ttu-id="28b90-277">Visual Studio 问题</span><span class="sxs-lookup"><span data-stu-id="28b90-277">Visual Studio issues</span></span>

<span data-ttu-id="28b90-278">本部分介绍在 Visual Studio 中遇到的问题。</span><span class="sxs-lookup"><span data-stu-id="28b90-278">This section describes issues encountered in Visual Studio.</span></span>

### <a name="script-documents-node-does-not-appear-in-solution-explorer"></a><span data-ttu-id="28b90-279">脚本文档节点不显示在解决方案资源管理器</span><span class="sxs-lookup"><span data-stu-id="28b90-279">Script Documents node does not appear in Solution Explorer</span></span>

<span data-ttu-id="28b90-280">我们的一些教程进行调试时将你定向到在解决方案资源管理器中的"脚本文档"节点。</span><span class="sxs-lookup"><span data-stu-id="28b90-280">Some of our tutorials direct you to the "Script Documents" node in Solution Explorer while debugging.</span></span> <span data-ttu-id="28b90-281">此节点由 JavaScript 调试器中，并且将只会显示调试 Internet Explorer 中的浏览器客户端时如果使用 Chrome 或 Firefox，则不会出现该节点。</span><span class="sxs-lookup"><span data-stu-id="28b90-281">This node is produced by the JavaScript debugger, and will only appear while debugging browser clients in Internet Explorer; the node will not appear if Chrome or Firefox are used.</span></span> <span data-ttu-id="28b90-282">如果正在运行另一个客户端调试器，例如 Silverlight 调试器 JavaScript 调试器也将不运行。</span><span class="sxs-lookup"><span data-stu-id="28b90-282">The JavaScript debugger will also not run if another client debugger is running, such as the Silverlight debugger.</span></span>

### <a name="signalr-does-not-work-on-visual-studio-2008-or-earlier"></a><span data-ttu-id="28b90-283">Visual Studio 2008 或更低版本 SignalR 不起作用</span><span class="sxs-lookup"><span data-stu-id="28b90-283">SignalR does not work on Visual Studio 2008 or earlier</span></span>

<span data-ttu-id="28b90-284">此行为是有意安排的。</span><span class="sxs-lookup"><span data-stu-id="28b90-284">This behavior is by design.</span></span> <span data-ttu-id="28b90-285">SignalR 需要.NET Framework 4 或更高版本;这需要在 Visual Studio 2010 或更高版本来开发 SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="28b90-285">SignalR requires .NET Framework 4 or later; this requires that SignalR applications be developed in Visual Studio 2010 or later.</span></span>

<a id="iis"></a>

## <a name="iis-issues"></a><span data-ttu-id="28b90-286">IIS 问题</span><span class="sxs-lookup"><span data-stu-id="28b90-286">IIS issues</span></span>

<span data-ttu-id="28b90-287">本部分包含使用 Internet Information Services 的问题。</span><span class="sxs-lookup"><span data-stu-id="28b90-287">This section contains issues with Internet Information Services.</span></span>

### <a name="web-site-crashes-after-maphubs-call"></a><span data-ttu-id="28b90-288">网站 MapHubs 调用之后就故障</span><span class="sxs-lookup"><span data-stu-id="28b90-288">Web site crashes after MapHubs call</span></span>

<span data-ttu-id="28b90-289">SignalR 的最新版本中已修复此问题。</span><span class="sxs-lookup"><span data-stu-id="28b90-289">This issue has been fixed in the latest version of SignalR.</span></span> <span data-ttu-id="28b90-290">验证通过更新使用 NuGet 安装使用 SignalR 最新发行的版本。</span><span class="sxs-lookup"><span data-stu-id="28b90-290">Verify that you are using the latest released version of SignalR by updating your installation using NuGet.</span></span>

<a id="azure"></a>

## <a name="azure-issues"></a><span data-ttu-id="28b90-291">Azure 问题</span><span class="sxs-lookup"><span data-stu-id="28b90-291">Azure issues</span></span>

<span data-ttu-id="28b90-292">本部分包含使用 Microsoft Azure 的问题。</span><span class="sxs-lookup"><span data-stu-id="28b90-292">This section contains issues with Microsoft Azure.</span></span>

### <a name="messages-are-not-received-through-the-azure-backplane-after-altering-topic-names"></a><span data-ttu-id="28b90-293">消息不会接收通过 Azure 底板后更改的主题名称</span><span class="sxs-lookup"><span data-stu-id="28b90-293">Messages are not received through the Azure backplane after altering topic names</span></span>

<span data-ttu-id="28b90-294">使用 Azure 的基架的主题并不是用户可配置。</span><span class="sxs-lookup"><span data-stu-id="28b90-294">The topics used by the Azure backplane are not intended to be user-configurable.</span></span>
