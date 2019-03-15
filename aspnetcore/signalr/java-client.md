---
title: ASP.NET Core SignalR Java 客户端
author: mikaelm12
description: 了解如何使用 ASP.NET Core SignalR Java 客户端。
monikerRange: '>= aspnetcore-2.2'
ms.author: mimengis
ms.custom: mvc
ms.date: 03/14/2019
uid: signalr/java-client
ms.openlocfilehash: 09e5ce23ddcc250d212a8cdf1176f39531a9c0ba
ms.sourcegitcommit: d913bca90373c07f89b1d1df01af5fc01fc908ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2019
ms.locfileid: "57978485"
---
# <a name="aspnet-core-signalr-java-client"></a><span data-ttu-id="d8eee-103">ASP.NET Core SignalR Java 客户端</span><span class="sxs-lookup"><span data-stu-id="d8eee-103">ASP.NET Core SignalR Java client</span></span>

<span data-ttu-id="d8eee-104">通过[Mikael Mengistu](https://twitter.com/MikaelM_12)</span><span class="sxs-lookup"><span data-stu-id="d8eee-104">By [Mikael Mengistu](https://twitter.com/MikaelM_12)</span></span>

<span data-ttu-id="d8eee-105">Java 客户端，包括 Android 应用程序的 Java 代码中连接到 ASP.NET Core SignalR 服务器。</span><span class="sxs-lookup"><span data-stu-id="d8eee-105">The Java client enables connecting to an ASP.NET Core SignalR server from Java code, including Android apps.</span></span> <span data-ttu-id="d8eee-106">像[JavaScript 客户端](xref:signalr/javascript-client)并[.NET 客户端](xref:signalr/dotnet-client)，Java 客户端，可接收并将消息发送到实时的中心。</span><span class="sxs-lookup"><span data-stu-id="d8eee-106">Like the [JavaScript client](xref:signalr/javascript-client) and the [.NET client](xref:signalr/dotnet-client), the Java client enables you to receive and send messages to a hub in real time.</span></span> <span data-ttu-id="d8eee-107">Java 客户端是可在 ASP.NET Core 2.2 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="d8eee-107">The Java client is available in ASP.NET Core 2.2 and later.</span></span>

<span data-ttu-id="d8eee-108">在这篇文章中引用示例 Java 控制台应用程序使用 SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="d8eee-108">The sample Java console app referenced in this article uses the SignalR Java client.</span></span>

<span data-ttu-id="d8eee-109">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d8eee-109">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/signalr/java-client/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="install-the-signalr-java-client-package"></a><span data-ttu-id="d8eee-110">SignalR Java 客户端程序包安装</span><span class="sxs-lookup"><span data-stu-id="d8eee-110">Install the SignalR Java client package</span></span>

<span data-ttu-id="d8eee-111">*Signalr 1.0.0* JAR 文件允许客户端连接到 SignalR 集线器。</span><span class="sxs-lookup"><span data-stu-id="d8eee-111">The *signalr-1.0.0* JAR file allows clients to connect to SignalR hubs.</span></span> <span data-ttu-id="d8eee-112">若要查找最新的 JAR 文件版本号，请参阅[Maven 搜索结果](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr)。</span><span class="sxs-lookup"><span data-stu-id="d8eee-112">To find the latest JAR file version number, see the [Maven search results](https://search.maven.org/search?q=g:com.microsoft.signalr%20AND%20a:signalr).</span></span>

<span data-ttu-id="d8eee-113">如果使用 Gradle，添加下面的代码行`dependencies`一部分您*build.gradle*文件：</span><span class="sxs-lookup"><span data-stu-id="d8eee-113">If using Gradle, add the following line to the `dependencies` section of your *build.gradle* file:</span></span>

```gradle
implementation 'com.microsoft.signalr:signalr:1.0.0'
```

<span data-ttu-id="d8eee-114">如果使用 Maven，添加以下行`<dependencies>`的元素在*pom.xml*文件：</span><span class="sxs-lookup"><span data-stu-id="d8eee-114">If using Maven, add the following lines inside the `<dependencies>` element of your *pom.xml* file:</span></span>

[!code-xml[pom.xml dependency element](java-client/sample/pom.xml?name=snippet_dependencyElement)]

## <a name="connect-to-a-hub"></a><span data-ttu-id="d8eee-115">连接到中心</span><span class="sxs-lookup"><span data-stu-id="d8eee-115">Connect to a hub</span></span>

<span data-ttu-id="d8eee-116">若要建立`HubConnection`，则`HubConnectionBuilder`应使用。</span><span class="sxs-lookup"><span data-stu-id="d8eee-116">To establish a `HubConnection`, the `HubConnectionBuilder` should be used.</span></span> <span data-ttu-id="d8eee-117">生成连接时，可以配置中心 URL 和日志级别。</span><span class="sxs-lookup"><span data-stu-id="d8eee-117">The hub URL and log level can be configured while building a connection.</span></span> <span data-ttu-id="d8eee-118">配置任何必需的选项，方法是调用的任何`HubConnectionBuilder`方法之前`build`。</span><span class="sxs-lookup"><span data-stu-id="d8eee-118">Configure any required options by calling any of the `HubConnectionBuilder` methods before `build`.</span></span> <span data-ttu-id="d8eee-119">启动与连接`start`。</span><span class="sxs-lookup"><span data-stu-id="d8eee-119">Start the connection with `start`.</span></span>

[!code-java[Build hub connection](java-client/sample/src/main/java/Chat.java?range=16-17)]

## <a name="call-hub-methods-from-client"></a><span data-ttu-id="d8eee-120">从客户端调用集线器方法</span><span class="sxs-lookup"><span data-stu-id="d8eee-120">Call hub methods from client</span></span>

<span data-ttu-id="d8eee-121">调用`send`调用集线器方法。</span><span class="sxs-lookup"><span data-stu-id="d8eee-121">A call to `send` invokes a hub method.</span></span> <span data-ttu-id="d8eee-122">将中心方法名称和到中心方法中定义的任何参数传递`send`。</span><span class="sxs-lookup"><span data-stu-id="d8eee-122">Pass the hub method name and any arguments defined in the hub method to `send`.</span></span>

[!code-java[send method](java-client/sample/src/main/java/Chat.java?range=28)]

> [!NOTE]
> <span data-ttu-id="d8eee-123">如果使用 Azure SignalR 服务的*无服务器模式*，不能从客户端调用集线器方法。</span><span class="sxs-lookup"><span data-stu-id="d8eee-123">If you're using Azure SignalR Service in *Serverless mode*, you cannot call hub methods from a client.</span></span> <span data-ttu-id="d8eee-124">有关详细信息，请参阅[SignalR 服务文档](/azure/azure-signalr/signalr-concept-serverless-development-config)。</span><span class="sxs-lookup"><span data-stu-id="d8eee-124">For more information, see the [SignalR Service documentation](/azure/azure-signalr/signalr-concept-serverless-development-config).</span></span>

## <a name="call-client-methods-from-hub"></a><span data-ttu-id="d8eee-125">从集线器调用客户端方法</span><span class="sxs-lookup"><span data-stu-id="d8eee-125">Call client methods from hub</span></span>

<span data-ttu-id="d8eee-126">使用`hubConnection.on`可以调用该集线器的客户端上定义的方法。</span><span class="sxs-lookup"><span data-stu-id="d8eee-126">Use `hubConnection.on` to define methods on the client that the hub can call.</span></span> <span data-ttu-id="d8eee-127">构建之后启动连接之前定义的方法。</span><span class="sxs-lookup"><span data-stu-id="d8eee-127">Define the methods after building but before starting the connection.</span></span>

[!code-java[Define client methods](java-client/sample/src/main/java/Chat.java?range=19-21)]

## <a name="add-logging"></a><span data-ttu-id="d8eee-128">添加日志记录</span><span class="sxs-lookup"><span data-stu-id="d8eee-128">Add logging</span></span>

<span data-ttu-id="d8eee-129">SignalR Java 客户端使用[SLF4J](https://www.slf4j.org/)用于日志记录库。</span><span class="sxs-lookup"><span data-stu-id="d8eee-129">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="d8eee-130">它是库的一个高级日志记录 API，允许通过将特定的日志记录依赖项中选择其自己特定的日志记录实现的用户。</span><span class="sxs-lookup"><span data-stu-id="d8eee-130">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="d8eee-131">下面的代码段演示如何使用`java.util.logging`的 SignalR Java 客户端。</span><span class="sxs-lookup"><span data-stu-id="d8eee-131">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="d8eee-132">如果没有配置依赖项中的日志记录，SLF4J 加载具有以下警告消息的默认无操作记录器：</span><span class="sxs-lookup"><span data-stu-id="d8eee-132">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="d8eee-133">这可以安全地忽略。</span><span class="sxs-lookup"><span data-stu-id="d8eee-133">This can safely be ignored.</span></span>

## <a name="android-development-notes"></a><span data-ttu-id="d8eee-134">Android 开发说明</span><span class="sxs-lookup"><span data-stu-id="d8eee-134">Android development notes</span></span>

<span data-ttu-id="d8eee-135">与 SignalR 客户端功能的 Android SDK 兼容性时，请考虑以下各项指定你的目标 Android SDK 版本：</span><span class="sxs-lookup"><span data-stu-id="d8eee-135">With regards to Android SDK compatibility for the SignalR client features, consider the following items when specifying your target Android SDK version:</span></span>

* <span data-ttu-id="d8eee-136">SignalR Java 客户端将运行 Android API 级别 16 和更高版本。</span><span class="sxs-lookup"><span data-stu-id="d8eee-136">The SignalR Java Client will run on Android API Level 16 and later.</span></span>
* <span data-ttu-id="d8eee-137">通过 Azure SignalR 服务连接将需要 20 及更高版本的 Android API 级别因为[Azure SignalR 服务](/azure/azure-signalr/signalr-overview)需要 TLS 1.2，并且不支持基于 SHA-1 的密码套件。</span><span class="sxs-lookup"><span data-stu-id="d8eee-137">Connecting through the Azure SignalR Service will require Android API Level 20 and later because the [Azure SignalR Service](/azure/azure-signalr/signalr-overview) requires TLS 1.2 and doesn't support SHA-1-based cipher suites.</span></span> <span data-ttu-id="d8eee-138">Android[添加支持 SHA-256 （和更高版本） 的密码套件](https://developer.android.com/reference/javax/net/ssl/SSLSocket)API 20 级中。</span><span class="sxs-lookup"><span data-stu-id="d8eee-138">Android [added support for SHA-256 (and above) cipher suites](https://developer.android.com/reference/javax/net/ssl/SSLSocket) in API Level 20.</span></span>

## <a name="configure-bearer-token-authentication"></a><span data-ttu-id="d8eee-139">配置持有者令牌身份验证</span><span class="sxs-lookup"><span data-stu-id="d8eee-139">Configure bearer token authentication</span></span>

<span data-ttu-id="d8eee-140">在 SignalR Java 客户端，可配置的持有者令牌，用于通过提供"访问令牌工厂"进行身份验证，向[HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)。</span><span class="sxs-lookup"><span data-stu-id="d8eee-140">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an "access token factory" to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="d8eee-141">使用[withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__)提供[RxJava](https://github.com/ReactiveX/RxJava) [单一<String>](http://reactivex.io/documentation/single.html)。</span><span class="sxs-lookup"><span data-stu-id="d8eee-141">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single<String>](http://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="d8eee-142">通过调用[Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)，可以编写逻辑来为您的客户端生成访问令牌。</span><span class="sxs-lookup"><span data-stu-id="d8eee-142">With a call to [Single.defer](http://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("YOUR HUB URL HERE")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

## <a name="known-limitations"></a><span data-ttu-id="d8eee-143">已知限制</span><span class="sxs-lookup"><span data-stu-id="d8eee-143">Known limitations</span></span>

* <span data-ttu-id="d8eee-144">支持仅 JSON 协议。</span><span class="sxs-lookup"><span data-stu-id="d8eee-144">Only the JSON protocol is supported.</span></span>
* <span data-ttu-id="d8eee-145">支持仅 Websocket 传输。</span><span class="sxs-lookup"><span data-stu-id="d8eee-145">Only the WebSockets transport is supported.</span></span>
* <span data-ttu-id="d8eee-146">流式处理尚不支持。</span><span class="sxs-lookup"><span data-stu-id="d8eee-146">Streaming isn't supported yet.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8eee-147">其他资源</span><span class="sxs-lookup"><span data-stu-id="d8eee-147">Additional resources</span></span>

* [<span data-ttu-id="d8eee-148">Java API 参考</span><span class="sxs-lookup"><span data-stu-id="d8eee-148">Java API reference</span></span>](/java/api/com.microsoft.signalr?view=aspnet-signalr-java)
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/publish-to-azure-web-app>
* [<span data-ttu-id="d8eee-149">Azure SignalR 服务无服务器文档</span><span class="sxs-lookup"><span data-stu-id="d8eee-149">Azure SignalR Service serverless documentation</span></span>](/azure/azure-signalr/signalr-concept-serverless-development-config)
