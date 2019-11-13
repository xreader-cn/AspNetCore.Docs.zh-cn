---
title: 用于 ASP.NET Core SignalR 横向扩展的 Redis 底板
author: bradygaster
description: 了解如何设置 Redis 底板以便为 ASP.NET Core SignalR 应用启用横向扩展。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 379d46fcaabb8eb0d04e521a5ad698229f947b7c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963920"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-opno-locsignalr-scale-out"></a><span data-ttu-id="d20b9-103">为 ASP.NET Core SignalR 横向扩展设置 Redis 底板</span><span class="sxs-lookup"><span data-stu-id="d20b9-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="d20b9-104">作者： [Andrew Stanton](https://twitter.com/anurse)、 [Brady Gaster](https://twitter.com/bradygaster)和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="d20b9-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="d20b9-105">本文介绍设置[Redis](https://redis.io/)服务器以用于向外扩展 ASP.NET Core SignalR 应用程序 SignalR特定方面。</span><span class="sxs-lookup"><span data-stu-id="d20b9-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="d20b9-106">设置 Redis 底板</span><span class="sxs-lookup"><span data-stu-id="d20b9-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="d20b9-107">部署 Redis 服务器。</span><span class="sxs-lookup"><span data-stu-id="d20b9-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="d20b9-108">对于生产用途，建议仅当 Redis 底板与 SignalR 应用在同一数据中心内运行时，才建议使用底板。</span><span class="sxs-lookup"><span data-stu-id="d20b9-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="d20b9-109">否则，网络延迟会降低性能。</span><span class="sxs-lookup"><span data-stu-id="d20b9-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="d20b9-110">如果 SignalR 应用在 Azure 云中运行，我们建议使用 Azure SignalR 服务，而不是 Redis 底板。</span><span class="sxs-lookup"><span data-stu-id="d20b9-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="d20b9-111">可以使用 Azure Redis 缓存服务进行开发和测试环境。</span><span class="sxs-lookup"><span data-stu-id="d20b9-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="d20b9-112">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="d20b9-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="d20b9-113">Redis 文档</span><span class="sxs-lookup"><span data-stu-id="d20b9-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="d20b9-114">Azure Redis 缓存文档</span><span class="sxs-lookup"><span data-stu-id="d20b9-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="d20b9-115">在 SignalR 应用中，安装 `Microsoft.AspNetCore.SignalR.Redis` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="d20b9-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span> <span data-ttu-id="d20b9-116">（还有一个 `Microsoft.AspNetCore.SignalR.StackExchangeRedis` 包，但它适用于 ASP.NET Core 2.2 及更高版本。）</span><span class="sxs-lookup"><span data-stu-id="d20b9-116">(There is also a `Microsoft.AspNetCore.SignalR.StackExchangeRedis` package, but that one is for ASP.NET Core 2.2 and later.)</span></span>

* <span data-ttu-id="d20b9-117">在 `Startup.ConfigureServices` 方法中，在 `AddSignalR`后调用 `AddRedis`：</span><span class="sxs-lookup"><span data-stu-id="d20b9-117">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="d20b9-118">根据需要配置选项：</span><span class="sxs-lookup"><span data-stu-id="d20b9-118">Configure options as needed:</span></span>
 
  <span data-ttu-id="d20b9-119">可以在连接字符串中或在[ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)对象中设置大多数选项。</span><span class="sxs-lookup"><span data-stu-id="d20b9-119">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="d20b9-120">在 `ConfigurationOptions` 中指定的选项会替代在连接字符串中设置的选项。</span><span class="sxs-lookup"><span data-stu-id="d20b9-120">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="d20b9-121">下面的示例演示如何在 `ConfigurationOptions` 对象中设置选项。</span><span class="sxs-lookup"><span data-stu-id="d20b9-121">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="d20b9-122">此示例将添加一个通道前缀，以便多个应用可以共享同一 Redis 实例，如以下步骤中所述。</span><span class="sxs-lookup"><span data-stu-id="d20b9-122">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="d20b9-123">在前面的代码中，`options.Configuration` 用连接字符串中指定的内容进行初始化。</span><span class="sxs-lookup"><span data-stu-id="d20b9-123">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* <span data-ttu-id="d20b9-124">在 SignalR 应用中，安装以下 NuGet 包之一：</span><span class="sxs-lookup"><span data-stu-id="d20b9-124">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="d20b9-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`-依赖于 Stackexchange.redis Redis 采用2.X.X。</span><span class="sxs-lookup"><span data-stu-id="d20b9-125">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="d20b9-126">建议将此包用于 ASP.NET Core 2.2 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="d20b9-126">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="d20b9-127">`Microsoft.AspNetCore.SignalR.Redis`-依赖于 Stackexchange.redis Redis 采用2.X.X。</span><span class="sxs-lookup"><span data-stu-id="d20b9-127">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="d20b9-128">此包不会在 ASP.NET Core 3.0 中发布。</span><span class="sxs-lookup"><span data-stu-id="d20b9-128">This package will not be shipping in ASP.NET Core 3.0.</span></span>

* <span data-ttu-id="d20b9-129">在 `Startup.ConfigureServices` 方法中，在 `AddSignalR`后调用 `AddStackExchangeRedis`：</span><span class="sxs-lookup"><span data-stu-id="d20b9-129">In the `Startup.ConfigureServices` method, call `AddStackExchangeRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="d20b9-130">根据需要配置选项：</span><span class="sxs-lookup"><span data-stu-id="d20b9-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="d20b9-131">可以在连接字符串中或在[ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)对象中设置大多数选项。</span><span class="sxs-lookup"><span data-stu-id="d20b9-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="d20b9-132">在 `ConfigurationOptions` 中指定的选项会替代在连接字符串中设置的选项。</span><span class="sxs-lookup"><span data-stu-id="d20b9-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="d20b9-133">下面的示例演示如何在 `ConfigurationOptions` 对象中设置选项。</span><span class="sxs-lookup"><span data-stu-id="d20b9-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="d20b9-134">此示例将添加一个通道前缀，以便多个应用可以共享同一 Redis 实例，如以下步骤中所述。</span><span class="sxs-lookup"><span data-stu-id="d20b9-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="d20b9-135">在前面的代码中，`options.Configuration` 用连接字符串中指定的内容进行初始化。</span><span class="sxs-lookup"><span data-stu-id="d20b9-135">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="d20b9-136">有关 Redis 选项的信息，请参阅[Stackexchange.redis Redis 文档](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)。</span><span class="sxs-lookup"><span data-stu-id="d20b9-136">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="d20b9-137">如果对多个 SignalR 应用使用一个 Redis 服务器，请为每个 SignalR 应用使用不同的通道前缀。</span><span class="sxs-lookup"><span data-stu-id="d20b9-137">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="d20b9-138">设置通道前缀会将一个 SignalR 应用与其他使用不同通道前缀的应用隔离开来。</span><span class="sxs-lookup"><span data-stu-id="d20b9-138">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="d20b9-139">如果未分配不同的前缀，则从一个应用发送到其所有客户端的消息将发送到使用 Redis 服务器作为底板的所有应用的所有客户端。</span><span class="sxs-lookup"><span data-stu-id="d20b9-139">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="d20b9-140">为粘滞会话配置服务器场负载平衡软件。</span><span class="sxs-lookup"><span data-stu-id="d20b9-140">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="d20b9-141">下面是有关如何执行此操作的一些文档示例：</span><span class="sxs-lookup"><span data-stu-id="d20b9-141">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="d20b9-142">IIS</span><span class="sxs-lookup"><span data-stu-id="d20b9-142">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="d20b9-143">HAProxy</span><span class="sxs-lookup"><span data-stu-id="d20b9-143">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="d20b9-144">Nginx</span><span class="sxs-lookup"><span data-stu-id="d20b9-144">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="d20b9-145">pfSense</span><span class="sxs-lookup"><span data-stu-id="d20b9-145">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="d20b9-146">Redis 服务器错误</span><span class="sxs-lookup"><span data-stu-id="d20b9-146">Redis server errors</span></span>

<span data-ttu-id="d20b9-147">当 Redis 服务器发生故障时，SignalR 会引发指示消息无法传递的异常。</span><span class="sxs-lookup"><span data-stu-id="d20b9-147">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="d20b9-148">一些典型的异常消息：</span><span class="sxs-lookup"><span data-stu-id="d20b9-148">Some typical exception messages:</span></span>

* <span data-ttu-id="d20b9-149">*写入消息失败*</span><span class="sxs-lookup"><span data-stu-id="d20b9-149">*Failed writing message*</span></span>
* <span data-ttu-id="d20b9-150">*未能调用中心方法 "方法名称"*</span><span class="sxs-lookup"><span data-stu-id="d20b9-150">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="d20b9-151">*未能连接到 Redis*</span><span class="sxs-lookup"><span data-stu-id="d20b9-151">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="d20b9-152"> 不会缓冲消息，以便在服务器重新启动时发送这些消息。</span><span class="sxs-lookup"><span data-stu-id="d20b9-152"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="d20b9-153">Redis 服务器关闭时发送的任何消息都将丢失。</span><span class="sxs-lookup"><span data-stu-id="d20b9-153">Any messages sent while the Redis server is down are lost.</span></span>

<span data-ttu-id="d20b9-154">当 Redis 服务器再次可用时，SignalR 自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="d20b9-154">SignalR automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="d20b9-155">连接失败的自定义行为</span><span class="sxs-lookup"><span data-stu-id="d20b9-155">Custom behavior for connection failures</span></span>

<span data-ttu-id="d20b9-156">下面是演示如何处理 Redis 连接失败事件的示例。</span><span class="sxs-lookup"><span data-stu-id="d20b9-156">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="redis-clustering"></a><span data-ttu-id="d20b9-157">Redis 群集</span><span class="sxs-lookup"><span data-stu-id="d20b9-157">Redis Clustering</span></span>

<span data-ttu-id="d20b9-158">[Redis 聚类分析](https://redis.io/topics/cluster-spec)是一种通过使用多个 Redis 服务器实现高可用性的方法。</span><span class="sxs-lookup"><span data-stu-id="d20b9-158">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="d20b9-159">群集不是正式支持的，但它可能会起作用。</span><span class="sxs-lookup"><span data-stu-id="d20b9-159">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="d20b9-160">后续步骤</span><span class="sxs-lookup"><span data-stu-id="d20b9-160">Next steps</span></span>

<span data-ttu-id="d20b9-161">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="d20b9-161">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="d20b9-162">Redis 文档</span><span class="sxs-lookup"><span data-stu-id="d20b9-162">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="d20b9-163">Stackexchange.redis Redis 文档</span><span class="sxs-lookup"><span data-stu-id="d20b9-163">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="d20b9-164">Azure Redis 缓存文档</span><span class="sxs-lookup"><span data-stu-id="d20b9-164">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
