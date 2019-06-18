---
title: Redis 的 ASP.NET Core SignalR 横向扩展的底板
author: bradygaster
description: 了解如何设置 Redis 底板以启用 ASP.NET Core SignalR 应用程序的横向扩展。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/redis-backplane
ms.openlocfilehash: adf9bbce1353fd811a4044e173533f76bc4193de
ms.sourcegitcommit: 4ef0362ef8b6e5426fc5af18f22734158fe587e1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152916"
---
# <a name="set-up-a-redis-backplane-for-aspnet-core-signalr-scale-out"></a>设置 ASP.NET Core SignalR 横向扩展 Redis 底板

通过[Andrew Stanton-nurse](https://twitter.com/anurse)， [Brady Gaster](https://twitter.com/bradygaster)，并[Tom Dykstra](https://github.com/tdykstra)，

此文章介绍了设置的特定于 SignalR 的方面[Redis](https://redis.io/)要用于横向扩展 ASP.NET Core SignalR 应用程序服务器。

## <a name="set-up-a-redis-backplane"></a>设置 Redis 底板

* 部署 Redis 服务器。

  > [!IMPORTANT] 
  > 对于生产用途，Redis 底板时，建议仅在与 SignalR 应用位于同一数据中心中运行。 否则，网络延迟会降低性能。 如果您的 SignalR 应用程序运行在 Azure 云中，我们将建议 Azure SignalR 服务而不是 Redis 底板。 可以使用 Azure Redis 缓存服务进行开发和测试环境。

  有关更多信息，请参见以下资源：

  * <xref:signalr/scale>
  * [Redis 文档](https://redis.io/)
  * [Azure Redis 缓存文档](https://docs.microsoft.com/azure/redis-cache/)

::: moniker range="= aspnetcore-2.1"

* 在 SignalR 应用程序中，将安装`Microsoft.AspNetCore.SignalR.Redis`NuGet 包。 (此外，还有`Microsoft.AspNetCore.SignalR.StackExchangeRedis`包，但该之一是 ASP.NET Core 2.2 及更高版本。)

* 在中`Startup.ConfigureServices`方法中，调用`AddRedis`后`AddSignalR`:

  ```csharp
  services.AddSignalR().AddRedis("<your_Redis_connection_string>");
  ```

* 根据需要配置选项：
 
  可以设置大多数选项，在连接字符串中或在[ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)对象。 中指定的选项`ConfigurationOptions`在连接字符串中设置的替代。

  下面的示例演示如何在中设置选项`ConfigurationOptions`对象。 此示例添加通道前缀，以便多个应用程序可以共享同一 Redis 实例下, 一步中所述。

  ```csharp
  services.AddSignalR()
    .AddRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  在前面的代码，`options.Configuration`使用连接字符串中指定了任何初始化。

::: moniker-end

::: moniker range="> aspnetcore-2.1"

* 在 SignalR 应用程序中，将安装以下 NuGet 包之一：

  * `Microsoft.AspNetCore.SignalR.StackExchangeRedis` -依赖于 StackExchange.Redis 2.X.X。 这是建议的包的 ASP.NET Core 2.2 及更高版本。
  * `Microsoft.AspNetCore.SignalR.Redis` -依赖于 StackExchange.Redis 1.X.X。 此包不会在 ASP.NET Core 3.0 发布。

* 在中`Startup.ConfigureServices`方法中，调用`AddStackExchangeRedis`后`AddSignalR`:

  ```csharp
  services.AddSignalR().AddStackExchangeRedis("<your_Redis_connection_string>");
  ```

* 根据需要配置选项：
 
  可以设置大多数选项，在连接字符串中或在[ConfigurationOptions](https://stackexchange.github.io/StackExchange.Redis/Configuration#configuration-options)对象。 中指定的选项`ConfigurationOptions`在连接字符串中设置的替代。

  下面的示例演示如何在中设置选项`ConfigurationOptions`对象。 此示例添加通道前缀，以便多个应用程序可以共享同一 Redis 实例下, 一步中所述。

  ```csharp
  services.AddSignalR()
    .AddStackExchangeRedis(connectionString, options => {
        options.Configuration.ChannelPrefix = "MyApp";
    });
  ```

  在前面的代码，`options.Configuration`使用连接字符串中指定了任何初始化。

  有关 Redis 选项的信息，请参阅[StackExchange Redis 文档](https://stackexchange.github.io/StackExchange.Redis/Configuration.html)。

::: moniker-end

* 如果您正在使用多个 SignalR 应用一个 Redis 服务器，为每个 SignalR 应用使用不同的通道前缀。

  设置通道前缀将从其他人使用不同的通道前缀的一个 SignalR 应用程序隔离开来。 如果没有分配不同的前缀，从一个应用到所有其自己的客户端发送的消息将转到用作基架使用 Redis 服务器的所有应用的所有客户端。

* 配置服务器场负载平衡软件为粘性会话。 下面是文档的有关如何执行该操作的一些示例：

  * [IIS](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)
  * [HAProxy](https://www.haproxy.com/blog/load-balancing-affinity-persistence-sticky-sessions-what-you-need-to-know/)
  * [Nginx](https://docs.nginx.com/nginx/admin-guide/load-balancer/http-load-balancer/#sticky)
  * [pfSense](https://www.netgate.com/docs/pfsense/loadbalancing/inbound-load-balancing.html#sticky-connections)

## <a name="redis-server-errors"></a>Redis 服务器错误

当 Redis 服务器出现故障时，SignalR 会引发异常，指示不会将消息传递。 一些典型异常消息：

* *失败的编写消息*
* *无法调用集线器方法 MethodName*
* *未能连接到 Redis*

SignalR 不缓冲消息，当服务器重新启动后发送它们。 Redis 服务器已关闭时发送任何消息都将丢失。

当 Redis 服务器再次可用时，自动重新连接 SignalR。

### <a name="custom-behavior-for-connection-failures"></a>连接失败的自定义行为

下面是一个示例，演示如何处理 Redis 连接失败事件。

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

## <a name="redis-clustering"></a>Redis 群集功能

[Redis 聚类分析](https://redis.io/topics/cluster-spec)是通过使用多个 Redis 服务器实现高可用性的方法。 聚类分析不正式支持，但它可能起作用。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* <xref:signalr/scale>
* [Redis 文档](https://redis.io/documentation)
* [StackExchange Redis 文档](https://stackexchange.github.io/StackExchange.Redis/)
* [Azure Redis 缓存文档](https://docs.microsoft.com/azure/redis-cache/)
