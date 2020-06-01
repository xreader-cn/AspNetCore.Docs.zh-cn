# <a name="aspnet-core-distributed-cache-sample"></a>ASP.NET Core 分布式缓存示例

此示例演示如何使用分布式缓存。 此示例演示了在[中使用分布式缓存 ASP.NET Core](https://docs.microsoft.com/aspnet/core/performance/caching/distributed)主题中描述的方案。

在生产环境中，示例应用配置为使用分布式 SQL Server 缓存。 若要将应用重新配置为使用分布式 Redis 缓存，请将*Startup.cs*文件顶部的预处理器指令更改为使用 Redis （ `#define Redis // SQLServer` ）。 有关详细信息，请参阅[示例代码中的预处理器指令](https://docs.microsoft.com/aspnet/core/introduction-to-aspnet-core#preprocessor-directives-in-sample-code)。
