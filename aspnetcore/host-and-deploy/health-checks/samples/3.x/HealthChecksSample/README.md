# <a name="aspnet-core-health-check-sample"></a>ASP.NET Core 运行状况检查示例

此示例演示如何使用运行状况检查中间件和服务。 此示例演示 [ASP.NET Core 中的运行状况检查](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks)主题中所述的方案。

若要运行主题中所述方案的示例应用，请在命令行界面中从项目文件夹中使用 [dotnet run](https://docs.microsoft.com/dotnet/core/tools/dotnet-run) 命令。 针对所浏览的方案传递开关。 未向 `dotnet run` 提供开关时，应用默认为 `basic` 配置。

| 场景                                               | 示例应用命令               | 说明 |
| ------------------------------------------------------ | -------------------------------- | ----------- |
| 基本运行状况探测（默认）                           | `dotnet run --scenario basic`    | 确认应用可以处理 HTTP 请求。 |
| 数据库探测                                         | `dotnet run --scenario db`       | 检查 SQL Server 数据库连接。 有关说明，请参阅主题的[数据库探测](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#database-probe)部分。 |
| 就绪情况/运行情况探测                              | `dotnet run --scenario liveness` | 针对动态应用状态（运行情况）与正在准备成为动态状态的应用（就绪情况）执行检查。 |
| 基于指标的探测（内存）/<br>自定义响应编写器 | `dotnet run --scenario writer`   | 对内存使用情况进行检查，并在检查运行状况终结点时写出自定义 JSON。 |
| 按端口筛选                                         | `dotnet run --scenario port`     | 将运行状况检查筛选到给定端口。 有关说明，请参阅主题的[按端口筛选](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks#filter-by-port)部分。 |

数据库探测和端口筛选器方案需要进行其他配置。 有关详细信息，请参阅[运行状况检查](https://docs.microsoft.com/aspnet/core/host-and-deploy/health-checks)主题。
