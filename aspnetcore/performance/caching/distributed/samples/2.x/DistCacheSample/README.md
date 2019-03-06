# <a name="aspnet-core-distributed-cache-sample"></a>ASP.NET Core 分布式的缓存示例

此示例演示如何使用分布式缓存。 此示例演示中所述的方案[使用 ASP.NET Core 中的分布式缓存](https://docs.microsoft.com/aspnet/core/performance/caching/distributed)主题。

在生产环境中，示例应用程序配置为使用 SQL Server 分布式的缓存。 若要重新配置应用程序以使用分布式的 Redis 缓存，请更改顶部的预处理器指令*Startup.cs*文件以使用 Redis (`#define Redis // SQLServer`)。 有关详细信息，请参阅[示例代码中的预处理器指令](https://docs.microsoft.com/aspnet/core/#preprocessor-directives-in-sample-code)。
