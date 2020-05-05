---
title: 用于 ASP.NET Core SignalR横向扩展的 Redis 底板
author: bradygaster
description: 了解如何设置 Redis 底板以便为 ASP.NET Core SignalR应用启用横向扩展。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/redis-backplane
ms.openlocfilehash: 6068890f4089a13add05bf8cf8009367e343adce
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775344"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a><span data-ttu-id="00ea6-103">设置用于 ASP.NET Core SignalR横向扩展的 Redis 底板</span><span class="sxs-lookup"><span data-stu-id="00ea6-103">Set up a Redis backplane for ASP.NET Core SignalR scale-out</span></span>

<span data-ttu-id="00ea6-104">作者： [Andrew Stanton](https://twitter.com/anurse)、 [Brady Gaster](https://twitter.com/bradygaster)和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="00ea6-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="00ea6-105">本文介绍SignalR了设置[Redis](https://redis.io/)服务器以用于横向扩展 ASP.NET Core SignalR应用程序的特定方面。</span><span class="sxs-lookup"><span data-stu-id="00ea6-105">This article explains SignalR-specific aspects of setting up a [Redis](https://redis.io/) server to use for scaling out an ASP.NET Core SignalR app.</span></span>

## <a name="set-up-a-redis-backplane"></a><span data-ttu-id="00ea6-106">设置 Redis 底板</span><span class="sxs-lookup"><span data-stu-id="00ea6-106">Set up a Redis backplane</span></span>

* <span data-ttu-id="00ea6-107">部署 Redis 服务器。</span><span class="sxs-lookup"><span data-stu-id="00ea6-107">Deploy a Redis server.</span></span>

  > [!IMPORTANT] 
  > <span data-ttu-id="00ea6-108">对于生产用途，建议仅当 Redis 底板与SignalR应用在同一数据中心内运行时才使用它。</span><span class="sxs-lookup"><span data-stu-id="00ea6-108">For production use, a Redis backplane is recommended only when it runs in the same data center as the SignalR app.</span></span> <span data-ttu-id="00ea6-109">否则，网络延迟会降低性能。</span><span class="sxs-lookup"><span data-stu-id="00ea6-109">Otherwise, network latency degrades performance.</span></span> <span data-ttu-id="00ea6-110">如果你SignalR的应用在 azure 云中运行，我们建议使用 azure SignalR服务，而不是 Redis 底板。</span><span class="sxs-lookup"><span data-stu-id="00ea6-110">If your SignalR app is running in the Azure cloud, we recommend Azure SignalR Service instead of a Redis backplane.</span></span> <span data-ttu-id="00ea6-111">可以使用 Azure Redis 缓存服务进行开发和测试环境。</span><span class="sxs-lookup"><span data-stu-id="00ea6-111">You can use the Azure Redis Cache Service for development and test environments.</span></span>

  <span data-ttu-id="00ea6-112">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="00ea6-112">For more information, see the following resources:</span></span>

  * <xref:signalr/scale>
  * [<span data-ttu-id="00ea6-113">Redis 文档</span><span class="sxs-lookup"><span data-stu-id="00ea6-113">Redis documentation</span></span>](https://redis.io/)
  * [<span data-ttu-id="00ea6-114">Azure Redis 缓存文档</span><span class="sxs-lookup"><span data-stu-id="00ea6-114">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* <span data-ttu-id="00ea6-115">在SignalR应用中，安装`Microsoft.AspNetCore.SignalR.Redis` NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="00ea6-115">In the SignalR app, install the `Microsoft.AspNetCore.SignalR.Redis` NuGet package.</span></span>
* <span data-ttu-id="00ea6-116">在`Startup.ConfigureServices`方法中，调用`AddRedis` after `AddSignalR`：</span><span class="sxs-lookup"><span data-stu-id="00ea6-116">In the `Startup.ConfigureServices` method, call `AddRedis` after `AddSignalR`:</span></span>

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* <span data-ttu-id="00ea6-117">根据需要配置选项：</span><span class="sxs-lookup"><span data-stu-id="00ea6-117">Configure options as needed:</span></span>
 
  <span data-ttu-id="00ea6-118">可以在连接字符串中或在[ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)对象中设置大多数选项。</span><span class="sxs-lookup"><span data-stu-id="00ea6-118">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="00ea6-119">中`ConfigurationOptions`指定的选项替代在连接字符串中设置的选项。</span><span class="sxs-lookup"><span data-stu-id="00ea6-119">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="00ea6-120">下面的示例演示如何在`ConfigurationOptions`对象中设置选项。</span><span class="sxs-lookup"><span data-stu-id="00ea6-120">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="00ea6-121">此示例将添加一个通道前缀，以便多个应用可以共享同一 Redis 实例，如以下步骤中所述。</span><span class="sxs-lookup"><span data-stu-id="00ea6-121">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="00ea6-122">在前面的代码中`options.Configuration` ，用连接字符串中指定的内容进行初始化。</span><span class="sxs-lookup"><span data-stu-id="00ea6-122">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

* <span data-ttu-id="00ea6-123">在SignalR应用中，安装以下 NuGet 包之一：</span><span class="sxs-lookup"><span data-stu-id="00ea6-123">In the SignalR app, install one of the following NuGet packages:</span></span>

  * <span data-ttu-id="00ea6-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis`-依赖于 Stackexchange.redis. Redis 2. 采用2.X.X。</span><span class="sxs-lookup"><span data-stu-id="00ea6-124">`Microsoft.AspNetCore.SignalR.StackExchangeRedis` - Depends on StackExchange.Redis 2.X.X.</span></span> <span data-ttu-id="00ea6-125">建议将此包用于 ASP.NET Core 2.2 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="00ea6-125">This is the recommended package for ASP.NET Core 2.2 and later.</span></span>
  * <span data-ttu-id="00ea6-126">`Microsoft.AspNetCore.SignalR.Redis`-依赖于 Stackexchange.redis. Redis 采用2.X.X。</span><span class="sxs-lookup"><span data-stu-id="00ea6-126">`Microsoft.AspNetCore.SignalR.Redis` - Depends on StackExchange.Redis 1.X.X.</span></span> <span data-ttu-id="00ea6-127">此包不包含在 ASP.NET Core 3.0 及更高版本中。</span><span class="sxs-lookup"><span data-stu-id="00ea6-127">This package isn't included in ASP.NET Core 3.0 and later.</span></span>

* <span data-ttu-id="00ea6-128">在`Startup.ConfigureServices`方法中，调用<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>：</span><span class="sxs-lookup"><span data-stu-id="00ea6-128">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

 <span data-ttu-id="00ea6-129">使用`Microsoft.AspNetCore.SignalR.Redis`时，请<xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>调用。</span><span class="sxs-lookup"><span data-stu-id="00ea6-129">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

* <span data-ttu-id="00ea6-130">根据需要配置选项：</span><span class="sxs-lookup"><span data-stu-id="00ea6-130">Configure options as needed:</span></span>
 
  <span data-ttu-id="00ea6-131">可以在连接字符串中或在[ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)对象中设置大多数选项。</span><span class="sxs-lookup"><span data-stu-id="00ea6-131">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="00ea6-132">中`ConfigurationOptions`指定的选项替代在连接字符串中设置的选项。</span><span class="sxs-lookup"><span data-stu-id="00ea6-132">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="00ea6-133">下面的示例演示如何在`ConfigurationOptions`对象中设置选项。</span><span class="sxs-lookup"><span data-stu-id="00ea6-133">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="00ea6-134">此示例将添加一个通道前缀，以便多个应用可以共享同一 Redis 实例，如以下步骤中所述。</span><span class="sxs-lookup"><span data-stu-id="00ea6-134">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

 <span data-ttu-id="00ea6-135">使用`Microsoft.AspNetCore.SignalR.Redis`时，请<xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>调用。</span><span class="sxs-lookup"><span data-stu-id="00ea6-135">When using `Microsoft.AspNetCore.SignalR.Redis`, call <xref:Microsoft.Extensions.DependencyInjection.RedisDependencyInjectionExtensions.AddRedis*>.</span></span>

  <span data-ttu-id="00ea6-136">在前面的代码中`options.Configuration` ，用连接字符串中指定的内容进行初始化。</span><span class="sxs-lookup"><span data-stu-id="00ea6-136">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="00ea6-137">有关 Redis 选项的信息，请参阅[Stackexchange.redis Redis 文档](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)。</span><span class="sxs-lookup"><span data-stu-id="00ea6-137">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

* <span data-ttu-id="00ea6-138">在SignalR应用中，安装以下 NuGet 包：</span><span class="sxs-lookup"><span data-stu-id="00ea6-138">In the SignalR app, install the following NuGet package:</span></span>

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis`
  
* <span data-ttu-id="00ea6-139">在`Startup.ConfigureServices`方法中，调用<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>：</span><span class="sxs-lookup"><span data-stu-id="00ea6-139">In the `Startup.ConfigureServices` method, call <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisDependencyInjectionExtensions.AddStackExchangeRedis*>:</span></span>

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```
  
* <span data-ttu-id="00ea6-140">根据需要配置选项：</span><span class="sxs-lookup"><span data-stu-id="00ea6-140">Configure options as needed:</span></span>
 
  <span data-ttu-id="00ea6-141">可以在连接字符串中或在[ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)对象中设置大多数选项。</span><span class="sxs-lookup"><span data-stu-id="00ea6-141">Most options can be set in the connection string or in the [ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options) object.</span></span> <span data-ttu-id="00ea6-142">中`ConfigurationOptions`指定的选项替代在连接字符串中设置的选项。</span><span class="sxs-lookup"><span data-stu-id="00ea6-142">Options specified in `ConfigurationOptions` override the ones set in the connection string.</span></span>

  <span data-ttu-id="00ea6-143">下面的示例演示如何在`ConfigurationOptions`对象中设置选项。</span><span class="sxs-lookup"><span data-stu-id="00ea6-143">The following example shows how to set options in the `ConfigurationOptions` object.</span></span> <span data-ttu-id="00ea6-144">此示例将添加一个通道前缀，以便多个应用可以共享同一 Redis 实例，如以下步骤中所述。</span><span class="sxs-lookup"><span data-stu-id="00ea6-144">This example adds a channel prefix so that multiple apps can share the same Redis instance, as explained in the following step.</span></span>

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  <span data-ttu-id="00ea6-145">在前面的代码中`options.Configuration` ，用连接字符串中指定的内容进行初始化。</span><span class="sxs-lookup"><span data-stu-id="00ea6-145">In the preceding code, `options.Configuration` is initialized with whatever was specified in the connection string.</span></span>

  <span data-ttu-id="00ea6-146">有关 Redis 选项的信息，请参阅[Stackexchange.redis Redis 文档](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)。</span><span class="sxs-lookup"><span data-stu-id="00ea6-146">For information about Redis options, see the [StackExchange Redis documentation](https://stackexchange.github.io/StackExchange.Redis/Configuration.html).</span></span>

::: moniker-end

* <span data-ttu-id="00ea6-147">如果将一个 Redis 服务器用于多个SignalR应用程序，请为每个SignalR应用程序使用不同的通道前缀。</span><span class="sxs-lookup"><span data-stu-id="00ea6-147">If you're using one Redis server for multiple SignalR apps, use a different channel prefix for each SignalR app.</span></span>

  <span data-ttu-id="00ea6-148">设置通道前缀会将一个SignalR应用与其他使用不同通道前缀的应用隔离开来。</span><span class="sxs-lookup"><span data-stu-id="00ea6-148">Setting a channel prefix isolates one SignalR app from others that use different channel prefixes.</span></span> <span data-ttu-id="00ea6-149">如果未分配不同的前缀，则从一个应用发送到其所有客户端的消息将发送到使用 Redis 服务器作为底板的所有应用的所有客户端。</span><span class="sxs-lookup"><span data-stu-id="00ea6-149">If you don't assign different prefixes, a message sent from one app to all of its own clients will go to all clients of all apps that use the Redis server as a backplane.</span></span>

* <span data-ttu-id="00ea6-150">为粘滞会话配置服务器场负载平衡软件。</span><span class="sxs-lookup"><span data-stu-id="00ea6-150">Configure your server farm load balancing software for sticky sessions.</span></span> <span data-ttu-id="00ea6-151">下面是有关如何执行此操作的一些文档示例：</span><span class="sxs-lookup"><span data-stu-id="00ea6-151">Here are some examples of documentation on how to do that:</span></span>

  * [<span data-ttu-id="00ea6-152">IIS</span><span class="sxs-lookup"><span data-stu-id="00ea6-152">IIS</span></span>](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [<span data-ttu-id="00ea6-153">HAProxy</span><span class="sxs-lookup"><span data-stu-id="00ea6-153">HAProxy</span></span>](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [<span data-ttu-id="00ea6-154">Nginx</span><span class="sxs-lookup"><span data-stu-id="00ea6-154">Nginx</span></span>](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [<span data-ttu-id="00ea6-155">pfSense</span><span class="sxs-lookup"><span data-stu-id="00ea6-155">pfSense</span></span>](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a><span data-ttu-id="00ea6-156">Redis 服务器错误</span><span class="sxs-lookup"><span data-stu-id="00ea6-156">Redis server errors</span></span>

<span data-ttu-id="00ea6-157">当 Redis 服务器发生故障时， SignalR将引发指示消息无法传递的异常。</span><span class="sxs-lookup"><span data-stu-id="00ea6-157">When a Redis server goes down, SignalR throws exceptions that indicate messages won't be delivered.</span></span> <span data-ttu-id="00ea6-158">一些典型的异常消息：</span><span class="sxs-lookup"><span data-stu-id="00ea6-158">Some typical exception messages:</span></span>

* <span data-ttu-id="00ea6-159">*写入消息失败*</span><span class="sxs-lookup"><span data-stu-id="00ea6-159">*Failed writing message*</span></span>
* <span data-ttu-id="00ea6-160">*未能调用中心方法 "方法名称"*</span><span class="sxs-lookup"><span data-stu-id="00ea6-160">*Failed to invoke hub method 'MethodName'*</span></span>
* <span data-ttu-id="00ea6-161">*未能连接到 Redis*</span><span class="sxs-lookup"><span data-stu-id="00ea6-161">*Connection to Redis failed*</span></span>

SignalR<span data-ttu-id="00ea6-162">不会在服务器重新启动时缓冲消息以发送这些消息。</span><span class="sxs-lookup"><span data-stu-id="00ea6-162"> doesn't buffer messages to send them when the server comes back up.</span></span> <span data-ttu-id="00ea6-163">Redis 服务器关闭时发送的任何消息都将丢失。</span><span class="sxs-lookup"><span data-stu-id="00ea6-163">Any messages sent while the Redis server is down are lost.</span></span>

SignalR<span data-ttu-id="00ea6-164">当 Redis 服务器再次可用时，自动重新连接。</span><span class="sxs-lookup"><span data-stu-id="00ea6-164"> automatically reconnects when the Redis server is available again.</span></span>

### <a name="custom-behavior-for-connection-failures"></a><span data-ttu-id="00ea6-165">连接失败的自定义行为</span><span class="sxs-lookup"><span data-stu-id="00ea6-165">Custom behavior for connection failures</span></span>

<span data-ttu-id="00ea6-166">下面是演示如何处理 Redis 连接失败事件的示例。</span><span class="sxs-lookup"><span data-stu-id="00ea6-166">Here's an example that shows how to handle Redis connection failure events.</span></span>

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

## <a name="redis-clustering"></a><span data-ttu-id="00ea6-167">Redis 群集</span><span class="sxs-lookup"><span data-stu-id="00ea6-167">Redis Clustering</span></span>

<span data-ttu-id="00ea6-168">[Redis 聚类分析](https://redis.io/topics/cluster-spec)是一种通过使用多个 Redis 服务器实现高可用性的方法。</span><span class="sxs-lookup"><span data-stu-id="00ea6-168">[Redis Clustering](https://redis.io/topics/cluster-spec) is a method for achieving high availability by using multiple Redis servers.</span></span> <span data-ttu-id="00ea6-169">群集不是正式支持的，但它可能会起作用。</span><span class="sxs-lookup"><span data-stu-id="00ea6-169">Clustering isn't officially supported, but it might work.</span></span>

## <a name="next-steps"></a><span data-ttu-id="00ea6-170">后续步骤</span><span class="sxs-lookup"><span data-stu-id="00ea6-170">Next steps</span></span>

<span data-ttu-id="00ea6-171">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="00ea6-171">For more information, see the following resources:</span></span>

* <xref:signalr/scale>
* [<span data-ttu-id="00ea6-172">Redis 文档</span><span class="sxs-lookup"><span data-stu-id="00ea6-172">Redis documentation</span></span>](https://redis.io/documentation)
* [<span data-ttu-id="00ea6-173">Stackexchange.redis Redis 文档</span><span class="sxs-lookup"><span data-stu-id="00ea6-173">StackExchange Redis documentation</span></span>](https://stackexchange.github.io/StackExchange.Redis/)
* [<span data-ttu-id="00ea6-174">Azure Redis 缓存文档</span><span class="sxs-lookup"><span data-stu-id="00ea6-174">Azure Redis Cache documentation</span></span>](https://docs.microsoft.com/azure/redis-cache/)
