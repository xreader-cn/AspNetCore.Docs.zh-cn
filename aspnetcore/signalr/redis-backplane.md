---
title: Redis 的 ASP.NET Core SignalR 横向扩展的底板
author: bradygaster
description: 了解如何设置 Redis 底板以启用 ASP.NET Core SignalR 应用程序的横向扩展。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/redis-backplane
ms.openlocfilehash: 9d2a942dba6abe669126efee7f2b3cdd6560658e
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012638"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="09dc0-103">设置 ASP.NET Core SignalR 横向扩展 Redis 底板</span><span class="sxs-lookup"><span data-stu-id="09dc0-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="09dc0-104">通过[Andrew Stanton-nurse](https://twitter.com/anurse)， [Brady Gaster](https://twitter.com/bradygaster)，并[Tom Dykstra](https://github.com/tdykstra)，</span><span class="sxs-lookup"><span data-stu-id="09dc0-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="09dc0-105">此文章介绍了设置的特定于 SignalR 的方面[Redis](https://redis.io/)要用于横向扩展 ASP.NET Core SignalR 应用程序服务器。</span><span class="sxs-lookup"><span data-stu-id="09dc0-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="09dc0-106">设置 Redis 底板</span><span class="sxs-lookup"><span data-stu-id="09dc0-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="09dc0-107">部署 Redis 服务器。</span><span class="sxs-lookup"><span data-stu-id="09dc0-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="09dc0-108">对于生产用途，Redis 底板时，建议仅在与 SignalR 应用位于同一数据中心中运行。</span><span class="sxs-lookup"><span data-stu-id="09dc0-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="09dc0-109">否则，网络延迟会降低性能。</span><span class="sxs-lookup"><span data-stu-id="09dc0-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="09dc0-110">如果您的 SignalR 应用程序运行在 Azure 云中，我们将建议 Azure SignalR 服务而不是 Redis 底板。</span><span class="sxs-lookup"><span data-stu-id="09dc0-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="09dc0-111">可以使用 Azure Redis 缓存服务进行开发和测试环境。</span><span class="sxs-lookup"><span data-stu-id="09dc0-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="09dc0-112">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="09dc0-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="09dc0-113">Redis 文档</span><span class="sxs-lookup"><span data-stu-id="09dc0-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="09dc0-114">Azure Redis 缓存文档</span><span class="sxs-lookup"><span data-stu-id="09dc0-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="09dc0-115">在 SignalR 应用程序中，将安装`Microsoft.AspNetCore.SignalR.Redis`NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="09dc0-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span> <span data-ttu-id="09dc0-116">(此外，还有`Microsoft.AspNetCore.SignalR.StackExchangeRedis`包，但该之一是 ASP.NET Core 2.2 及更高版本。)</span><span class="sxs-lookup"><span data-stu-id="09dc0-116">(There is also a `Microsoft.AspNetCore.SignalR.StackExchangeRedis` package, but that one is for ASP.NET Core 2.2 and later.)</span></span>

* <span data-ttu-id="09dc0-117">在中`Startup.ConfigureServices`方法中，调用`AddRedis`后`AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="09dc0-117">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="09dc0-118">根据需要配置选项：</span><span class="sxs-lookup"><span data-stu-id="09dc0-118">Configure options as needed:</span></span>
 
  <span data-ttu-id="09dc0-119">可以设置大多数选项，在连接字符串中或在[ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)对象。</span><span class="sxs-lookup"><span data-stu-id="09dc0-119">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="09dc0-120">中指定的选项`ConfigurationOptions`在连接字符串中设置的替代。</span><span class="sxs-lookup"><span data-stu-id="09dc0-120">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="09dc0-121">下面的示例演示如何在中设置选项`ConfigurationOptions`对象。</span><span class="sxs-lookup"><span data-stu-id="09dc0-121">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="09dc0-122">此示例添加通道前缀，以便多个应用程序可以共享同一 Redis 实例下, 一步中所述。</span><span class="sxs-lookup"><span data-stu-id="09dc0-122">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="09dc0-123">在前面的代码，`options.Configuration`使用连接字符串中指定了任何初始化。</span><span class="sxs-lookup"><span data-stu-id="09dc0-123">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* <span data-ttu-id="09dc0-124">在 SignalR 应用程序中，将安装以下 NuGet 包之一：</span><span class="sxs-lookup"><span data-stu-id="09dc0-124">In the SignalR app, install one of the following NuGet packages:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` <span data-ttu-id="09dc0-125">-依赖于 StackExchange.Redis 2.X.X。</span><span class="sxs-lookup"><span data-stu-id="09dc0-125">- Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="09dc0-126">这是建议的包的 ASP.NET Core 2.2 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="09dc0-126">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * `Microsoft.AspNetCore.SignalR.Redis` <span data-ttu-id="09dc0-127">-依赖于 StackExchange.Redis 1.X.X。</span><span class="sxs-lookup"><span data-stu-id="09dc0-127">- Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="09dc0-128">此包不会在 ASP.NET Core 3.0 发布。</span><span class="sxs-lookup"><span data-stu-id="09dc0-128">This package will not be shipping in ASP.NET Core 3.0.</span></span>

* <span data-ttu-id="09dc0-129">在中`Startup.ConfigureServices`方法中，调用`AddStackExchangeRedis`后`AddSignalR`:</span><span class="sxs-lookup"><span data-stu-id="09dc0-129">In the `Startup.ConfigureServices` method, call `AddStackExchangeRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="09dc0-130">根据需要配置选项：</span><span class="sxs-lookup"><span data-stu-id="09dc0-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="09dc0-131">可以设置大多数选项，在连接字符串中或在[ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)对象。</span><span class="sxs-lookup"><span data-stu-id="09dc0-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="09dc0-132">中指定的选项`ConfigurationOptions`在连接字符串中设置的替代。</span><span class="sxs-lookup"><span data-stu-id="09dc0-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="09dc0-133">下面的示例演示如何在中设置选项`ConfigurationOptions`对象。</span><span class="sxs-lookup"><span data-stu-id="09dc0-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="09dc0-134">此示例添加通道前缀，以便多个应用程序可以共享同一 Redis 实例下, 一步中所述。</span><span class="sxs-lookup"><span data-stu-id="09dc0-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="09dc0-135">在前面的代码，`options.Configuration`使用连接字符串中指定了任何初始化。</span><span class="sxs-lookup"><span data-stu-id="09dc0-135">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="09dc0-136">有关 Redis 选项的信息，请参阅[StackExchange Redis 文档](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)。</span><span class="sxs-lookup"><span data-stu-id="09dc0-136">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="09dc0-137">如果您正在使用多个 SignalR 应用一个 Redis 服务器，为每个 SignalR 应用使用不同的通道前缀。</span><span class="sxs-lookup"><span data-stu-id="09dc0-137">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="09dc0-138">设置通道前缀将从其他人使用不同的通道前缀的一个 SignalR 应用程序隔离开来。</span><span class="sxs-lookup"><span data-stu-id="09dc0-138">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="09dc0-139">如果没有分配不同的前缀，从一个应用到所有其自己的客户端发送的消息将转到用作基架使用 Redis 服务器的所有应用的所有客户端。</span><span class="sxs-lookup"><span data-stu-id="09dc0-139">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="09dc0-140">配置服务器场负载平衡软件为粘性会话。</span><span class="sxs-lookup"><span data-stu-id="09dc0-140">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="09dc0-141">下面是文档的有关如何执行该操作的一些示例：</span><span class="sxs-lookup"><span data-stu-id="09dc0-141">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="09dc0-142">IIS</span><span class="sxs-lookup"><span data-stu-id="09dc0-142">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="09dc0-143">HAProxy</span><span class="sxs-lookup"><span data-stu-id="09dc0-143">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="09dc0-144">Nginx</span><span class="sxs-lookup"><span data-stu-id="09dc0-144">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="09dc0-145">pfSense</span><span class="sxs-lookup"><span data-stu-id="09dc0-145">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="09dc0-146">Redis 服务器错误</span><span class="sxs-lookup"><span data-stu-id="09dc0-146">Redis server errors</span></span>

<span data-ttu-id="09dc0-147">当 Redis 服务器出现故障时，SignalR 会引发异常，指示不会将消息传递。</span><span class="sxs-lookup"><span data-stu-id="09dc0-147">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="09dc0-148">一些典型异常消息：</span><span class="sxs-lookup"><span data-stu-id="09dc0-148">Some typical exception messages:</span></span>

* *<span data-ttu-id="09dc0-149">失败的编写消息</span><span class="sxs-lookup"><span data-stu-id="09dc0-149">Failed writing message</span></span>*
* *<span data-ttu-id="09dc0-150">无法调用集线器方法 MethodName</span><span class="sxs-lookup"><span data-stu-id="09dc0-150">Failed to invoke hub method 'MethodName'</span></span>*
* *<span data-ttu-id="09dc0-151">未能连接到 Redis</span><span class="sxs-lookup"><span data-stu-id="09dc0-151">Connection to Redis failed</span></span>*

<span data-ttu-id="09dc0-152">SignalR 不缓冲消息，当服务器重新启动后发送它们。</span><span class="sxs-lookup"><span data-stu-id="09dc0-152">SignalR doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="09dc0-153">Redis 服务器已关闭时发送任何消息都将丢失。</span><span class="sxs-lookup"><span data-stu-id="09dc0-153">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="09dc0-154">当 Redis 服务器再次可用时，自动重新连接 SignalR。</span><span class="sxs-lookup"><span data-stu-id="09dc0-154">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="09dc0-155">连接失败的自定义行为</span><span class="sxs-lookup"><span data-stu-id="09dc0-155">Custom behavior for connection failures</span></span>

<span data-ttu-id="09dc0-156">下面是一个示例，演示如何处理 Redis 连接失败事件。</span><span class="sxs-lookup"><span data-stu-id="09dc0-156">Here's an example that shows how to handle Redis connection failure events.</span></span>

::: moniker range="= aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

::: moniker range="> aspnetcore-2.1"

```csharp
services.AddSignalR()
        .AddMessagePackProtocol()
        .AddStackExchangeRedis(o =>
        {
            o.ConnectionFactory = async writer =>
            {
                var config = new ConfigurationOptions
                {
                    AbortOnConnectFail = false
                };
                config.EndPoints.Add(IPAddress.Loopback, 0);
                config.SetDefaultPorts();
                var connection = await ConnectionMultiplexer.ConnectAsync(config, writer);
                connection.ConnectionFailed += (_, e) =>
                {
                    Console.WriteLine("Connection to Redis failed.");
                };

                if (!connection.IsConnected)
                {
                    Console.WriteLine("Did not connect to Redis.");
                }

                return connection;
            };
        });
```

::: moniker-end

## <a name="clustering"></a><span data-ttu-id="09dc0-157">聚类分析</span><span class="sxs-lookup"><span data-stu-id="09dc0-157">Clustering</span></span>

<span data-ttu-id="09dc0-158">聚类分析是一种方法使用多个 Redis 服务器实现高可用性。</span><span class="sxs-lookup"><span data-stu-id="09dc0-158">Clustering is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="09dc0-159">聚类分析不正式支持，但它可能起作用。</span><span class="sxs-lookup"><span data-stu-id="09dc0-159">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="09dc0-160">后续步骤</span><span class="sxs-lookup"><span data-stu-id="09dc0-160">Next steps</span></span>

<span data-ttu-id="09dc0-161">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="09dc0-161">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="09dc0-162">Redis 文档</span><span class="sxs-lookup"><span data-stu-id="09dc0-162">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="09dc0-163">StackExchange Redis 文档</span><span class="sxs-lookup"><span data-stu-id="09dc0-163">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="09dc0-164">Azure Redis 缓存文档</span><span class="sxs-lookup"><span data-stu-id="09dc0-164">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
