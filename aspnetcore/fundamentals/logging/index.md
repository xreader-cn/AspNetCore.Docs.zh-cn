---
title: .NET Core 和 ASP.NET Core 中的日志记录
author: rick-anderson
description: 了解如何使用由 Microsoft Extension.Logging NuGet 包提供的日志记录框架。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/05/2020
uid: fundamentals/logging/index
ms.openlocfilehash: 58e236ad7f0863b87907d5585e1cb6bf61d46e99
ms.sourcegitcommit: 72792e349458190b4158fcbacb87caf3fc605268
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78644448"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>.NET Core 和 ASP.NET Core 中的日志记录

作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Steve Smith](https://ardalis.com/)

.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。 本文介绍了如何将日志记录 API 与内置提供程序一起使用。

::: moniker range=">= aspnetcore-3.0"

本文中所述的大多数代码示例都来自 ASP.NET Core 应用。 这些代码片段的日志记录特定部分适用于任何使用[通用主机](xref:fundamentals/host/generic-host)的 .NET Core 应用。 要通过示例了解如何在非 Web 控制台应用中使用一般主机，请参阅*后台任务示例应用* 的 Program.cs 文件 ([)](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples)<xref:fundamentals/host/hosted-services>。

对于没有通用主机的应用，日志记录代码在[添加提供程序](#add-providers)和[创建记录器](#create-logs)的方式上有所不同。 本文的这些部分显示了非主机代码示例。

::: moniker-end

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="add-providers"></a>添加提供程序

日志记录提供程序会显示或存储日志。 例如，控制台提供程序在控制台上显示日志，Azure Application Insights 提供程序会将这些日志存储在 Azure Application Insights 中。 可通过添加多个提供程序将日志发送到多个目标。

::: moniker range=">= aspnetcore-3.0"

若要在使用通用主机的应用中添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法  ：

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=6)]

在非主机控制台应用中，在创建 `Add{provider name}` 时调用提供程序的 `LoggerFactory` 扩展方法：

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=1,7)]

`LoggerFactory` 和 `AddConsole` 需要用于 `using` 的 `Microsoft.Extensions.Logging` 语句。

默认 ASP.NET Core 项目模板调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>，该操作将添加以下日志记录提供程序：

* [控制台](#console-provider)
* [调试](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)（仅当在 Windows 上运行时）

可自行选择提供程序来替换默认提供程序。 调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AddProvider&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-3.0 "

要添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法  ：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

前面的代码需要引用 `Microsoft.Extensions.Logging` 和 `Microsoft.Extensions.Configuration`。

默认项目模板调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>，该操作将添加以下日志记录提供程序：

* Console
* 调试
* EventSource（启动位置：ASP.NET Core 2.2）

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

如果使用 `CreateDefaultBuilder`，则可自行选择提供程序来替换默认提供程序。 调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

::: moniker-end

详细了解[内置日志记录提供程序](#built-in-logging-providers)，以及本文稍后部分介绍的[第三方日志记录提供程序](#third-party-logging-providers)。

## <a name="create-logs"></a>创建日志

若要创建日志，请使用 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象。 在 Web 应用或托管服务中，由依赖关系注入 (DI) 获取 `ILogger`。 在非主机控制台应用中，使用 `LoggerFactory` 来创建 `ILogger`。

下面的 ASP.NET Core 示例创建了一个以 `TodoApiSample.Pages.AboutModel` 为类别的记录器。 日志“类别”是与每个日志关联的字符串  。 DI 提供的 `ILogger<T>` 实例创建日志，该日志以类型 `T` 的完全限定名称为类别。 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

下面的非主机控制台应用示例创建了一个以 `LoggingConsoleApp.Program` 为类别的记录器。

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

::: moniker-end

在下面的 ASP.NET Core 和控制台应用示例中，记录器用于创建以 `Information` 为级别的日志。 日志“级别”代表所记录事件的严重程度  。 

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

::: moniker-end

本文稍后部分将更详细地介绍[级别](#log-level)和[类别](#log-category)。 

::: moniker range=">= aspnetcore-3.0"

### <a name="create-logs-in-the-program-class"></a>在 Program 类中创建日志

若要将日志写入 ASP.NET Core 应用的 `Program` 类中，请在生成主机后从 DI 获取 `ILogger`实例：

[!code-csharp[](index/samples_snapshot/3.x/TodoApiSample/Program.cs?highlight=9,10)]

不直接支持在主机构造期间进行日志记录。 但是，可以使用单独的记录器。 在以下示例中，[Serilog](https://serilog.net/) 记录器用于登录 `CreateHostBuilder`。 `AddSerilog` 使用 `Log.Logger` 中指定的静态配置：

```csharp
using System;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Hosting;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }

    public static IHostBuilder CreateHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return Host.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddRazorPages();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {   
                    logging.AddSerilog();
                })
                .ConfigureWebHostDefaults(webBuilder =>
                {
                    webBuilder.UseStartup<Startup>();
                });
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

### <a name="create-logs-in-the-startup-class"></a>在 Startup 类中创建日志

若要将日志写入 ASP.NET Core 应用的 `Startup.Configure` 方法中，请在方法签名中包含 `ILogger` 参数：

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_Configure&highlight=1,5)]

不支持在 `Startup.ConfigureServices` 方法中完成 DI 容器设置之前就写入日志：

* 不支持将记录器注入到 `Startup` 构造函数中。
* 不支持将记录器注入到 `Startup.ConfigureServices` 方法签名中

这一限制的原因是，日志记录依赖于 DI 和配置，而配置又依赖于 DI。 在完成 `ConfigureServices` 之前，不会设置 DI 容器。

由于为 Web 主机创建了单独的 DI 容器，所以在 ASP.NET Core 的早期版本中，构造函数将记录器注入到 `Startup` 工作。 若要了解为何仅为通用主机创建一个容器，请参阅[重大更改公告](https://github.com/aspnet/Announcements/issues/353)。

如果需要配置依赖于 `ILogger<T>` 的服务，则仍可通过使用构造函数注入或提供工厂方法的方式来执行此操作。 只有在没有其他选择的情况下，才建议使用工厂方法。 例如，假设你需要由 DI 为服务填充属性：

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup.cs?name=snippet_ConfigureServices&highlight=6-10)]

前面突出显示的代码是 `Func`，该代码在 DI 容器第一次需要构造 `MyService` 实例时运行。 可以用这种方式访问任何已注册的服务。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

### <a name="create-logs-in-startup"></a>启动时创建日志

要将日志写入 `Startup` 类，构造函数签名需包含 `ILogger` 参数：

[!code-csharp[](index/samples/2.x/TodoApiSample/Startup.cs?name=snippet_Startup&highlight=3,5,8,20,27)]

### <a name="create-logs-in-the-program-class"></a>在 Program 类中创建日志

要将日志写入 `Program` 类，请从 DI 获取 `ILogger` 实例：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=9,10)]

不直接支持在主机构造期间进行日志记录。 但是，可以使用单独的记录器。 在以下示例中，[Serilog](https://serilog.net/) 记录器用于登录 `CreateWebHostBuilder`。 `AddSerilog` 使用 `Log.Logger` 中指定的静态配置：

```csharp
using System;
using Microsoft.AspNetCore;
using Microsoft.AspNetCore.Hosting;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.Logging;

public class Program
{
    public static void Main(string[] args)
    {
        CreateWebHostBuilder(args).Build().Run();
    }

    public static IWebHostBuilder CreateWebHostBuilder(string[] args)
    {
        var builtConfig = new ConfigurationBuilder()
            .AddJsonFile("appsettings.json")
            .AddCommandLine(args)
            .Build();

        Log.Logger = new LoggerConfiguration()
            .WriteTo.Console()
            .WriteTo.File(builtConfig["Logging:FilePath"])
            .CreateLogger();

        try
        {
            return WebHost.CreateDefaultBuilder(args)
                .ConfigureServices((context, services) =>
                {
                    services.AddMvc();
                })
                .ConfigureAppConfiguration((hostingContext, config) =>
                {
                    config.AddConfiguration(builtConfig);
                })
                .ConfigureLogging(logging =>
                {
                    logging.AddSerilog();
                })
                .UseStartup<Startup>();
        }
        catch (Exception ex)
        {
            Log.Fatal(ex, "Host builder error");

            throw;
        }
        finally
        {
            Log.CloseAndFlush();
        }
    }
}
```

::: moniker-end

### <a name="no-asynchronous-logger-methods"></a>没有异步记录器方法

日志记录应该会很快，不值得牺牲性能来使用异步代码。 如果你的日志数据存储很慢，请不要直接写入它。 首先考虑将日志消息写入快速存储，稍后再将其变为慢速存储。 例如，如果你要记录到 SQL Server，你可能不想直接在 `Log` 方法中记录，因为 `Log` 方法是同步的。 相反，你会将日志消息同步添加到内存中的队列，并让后台辅助线程从队列中拉出消息，以完成将数据推送到 SQL Server 的异步工作。 有关详细信息，请参阅[此](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 问题。

## <a name="configuration"></a>配置

日志记录提供程序配置由一个或多个配置提供程序提供：

* 文件格式（INI、JSON 和 XML）。
* 命令行参数。
* 环境变量。
* 内存中的 .NET 对象。
* 未加密的[机密管理器](xref:security/app-secrets)存储。
* 加密的用户存储，如 [Azure Key Vault](xref:security/key-vault-configuration)。
* （已安装或已创建的）自定义提供程序。

例如，日志记录配置通常由应用设置文件的 `Logging` 部分提供。 以下示例显示了典型 *appsettings.Development.json* 文件的内容：

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "System": "Information",
      "Microsoft": "Information"
    },
    "Console":
    {
      "IncludeScopes": true
    }
  }
}
```

`Logging` 属性可具有 `LogLevel` 和日志提供程序属性（显示控制台）。

`LogLevel` 下的 `Logging` 属性指定了用于记录所选类别的最低[级别](#log-level)。 在本例中，`System` 和 `Microsoft` 类别在 `Information` 级别记录，其他均在 `Debug` 级别记录。

`Logging` 下的其他属性均指定了日志记录提供程序。 本示例针对控制台提供程序。 如果提供程序支持[日志作用域](#log-scopes)，则 `IncludeScopes` 将指示是否启用这些域。 提供程序属性（例如本例的 `Console`）也可指定 `LogLevel` 属性。 提供程序下的 `LogLevel` 指定了该提供程序记录的级别。

如果在 `Logging.{providername}.LogLevel` 中指定了级别，则这些级别将重写 `Logging.LogLevel` 中设置的所有内容。

不可使用日志记录 API 在应用运行时更改日志记录。 但是，一些配置提供程序可重新加载配置，这将对日志记录配置立即产生影响。 例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)会默认重新加载日志记录配置，该程序由 `CreateDefaultBuilder` 添加用来读取设置文件。 如果在应用运行时在代码中更改了配置，则该应用可调用 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) 来更新应用的日志记录配置。

若要了解如何实现配置提供程序，请参阅 <xref:fundamentals/configuration/index>。

## <a name="sample-logging-output"></a>日志记录输出示例

使用上一部分中显示的示例代码从命令行运行应用时，将在控制台中看到日志。 以下是控制台输出示例：

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 84.26180000000001ms 307
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/api/todo/0
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:5000/api/todo/0
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 42.9286ms
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 148.889ms 404
```

::: moniker-end

通过向 `http://localhost:5000/api/todo/0` 处的示例应用发出 HTTP Get 请求来生成前述日志。

在 Visual Studio 中运行示例应用时，“调试”窗口中将显示如下日志：

::: moniker range=">= aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request starting HTTP/2.0 GET https://localhost:44328/api/todo/0  
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executing endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
TodoApiSample.Controllers.TodoController: Information: Getting item 0
TodoApiSample.Controllers.TodoController: Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult: Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker: Information: Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 34.167ms
Microsoft.AspNetCore.Routing.EndpointMiddleware: Information: Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
Microsoft.AspNetCore.Hosting.Diagnostics: Information: Request finished in 98.41300000000001ms 404
```

由上一部分所示的 `ILogger` 调用创建的日志以“TodoApiSample”开头。 以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。 ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request starting HTTP/1.1 GET http://localhost:53104/api/todo/0  
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
TodoApi.Controllers.TodoController:Information: Getting item 0
TodoApi.Controllers.TodoController:Warning: GetById(0) NOT FOUND
Microsoft.AspNetCore.Mvc.StatusCodeResult:Information: Executing HttpStatusCodeResult, setting HTTP status code 404
Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker:Information: Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 152.5657ms
Microsoft.AspNetCore.Hosting.Internal.WebHost:Information: Request finished in 316.3195ms 404
```

由上一部分所示的 `ILogger` 调用创建的日志以“TodoApi”开头。 以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。 ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。

::: moniker-end

本文余下部分将介绍有关日志记录的某些详细信息及选项。

## <a name="nuget-packages"></a>NuGet 包

`ILogger` 和 `ILoggerFactory` 接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中，其默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。

## <a name="log-category"></a>日志类别

创建 `ILogger` 对象后，将为其指定“类别”  。 该类别包含在由此 `ILogger` 实例创建的每条日志消息中。 类别可以是任何字符串，但约定需使用类名，例如“TodoApi.Controllers.TodoController”。

使用 `ILogger<T>` 获取一个 `ILogger` 实例，该实例使用 `T` 的完全限定类型名称作为类别：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

::: moniker-end

要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

::: moniker-end

`ILogger<T>` 相当于使用 `CreateLogger` 的完全限定类型名称来调用 `T`。

## <a name="log-level"></a>日志级别

每个日志都指定了一个 <xref:Microsoft.Extensions.Logging.LogLevel> 值。 日志级别指示严重性或重要程度。 例如，可在方法正常结束时写入 `Information` 日志，在方法返回“404 找不到”状态代码时写入 `Warning` 日志  。

下面的代码会创建 `Information` 和 `Warning` 日志：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

在上述代码中，第一个参数是[日志事件 ID](#log-event-id)。 第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。 稍后将在本文的[消息模板部分](#log-message-template)介绍方法参数。

在方法名称中包含级别的日志方法（例如 `LogInformation` 和 `LogWarning`）是 [ILogger 的扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)。 这些方法会调用可接受 `Log` 参数的 `LogLevel` 方法。 可直接调用 `Log` 方法而不调用其中某个扩展方法，但语法相对复杂。 有关详细信息，请参阅 <xref:Microsoft.Extensions.Logging.ILogger> 和[记录器扩展源代码](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。

ASP.NET Core 定义了以下日志级别（按严重性从低到高排列）。

* 跟踪 = 0

  有关通常仅用于调试的信息。 这些消息可能包含敏感应用程序数据，因此不得在生产环境中启用它们。 默认情况下禁用。 

* 调试 = 1

  有关在开发和调试中可能有用的信息。 示例：由于 日志数量过多，因此`Entering method Configure with flag set to true.` 仅当故障排除时才在生产中启用 `Debug` 级别日志。

* 信息 = 2

  用于跟踪应用的常规流。 这些日志通常有长期价值。 示例：`Request received for path /api/todo`

* 警告 = 3

  表示应用流中的异常或意外事件。 可能包括不会中断应用运行但仍需调查的错误或其他条件。 `Warning` 日志级别常用于已处理的异常。 示例：`FileNotFoundException for file quotes.txt.`

* 错误 = 4

  表示无法处理的错误和异常。 这些消息指示的是当前活动或操作（例如当前 HTTP 请求）中的失败，而不是整个应用中的失败。 日志消息示例：`Cannot insert record due to duplicate key violation.`

* 严重 = 5

  需要立即关注的失败。 例如数据丢失、磁盘空间不足。

使用日志级别控制写入到特定存储介质或显示窗口的日志输出量。 例如:

* 生产中：
  * 如果通过 `Trace` 级别在 `Information` 处记录，则会生成大量详细的日志消息。 为控制成本且不超过数据存储限制，请通过 `Trace` 级别消息将 `Information` 记录到容量大、成本低的数据存储中。
  * 如果通过 `Warning` 级别在 `Critical` 处记录，通常生成的日志消息更少且更小。 因此，成本和存储限制通常不是问题，而这使得在选择数据信息时更为灵活。
* 在开发过程中：
  * 通过 `Warning` 消息将 `Critical` 记录到控制台。
  * 在疑难解答时通过 `Trace` 消息添加 `Information`。

本文稍后的[日志筛选](#log-filtering)部分介绍如何控制提供程序处理的日志级别。

ASP.NET Core 为框架事件写入日志。 本文前面部分提供的日志示例排除了低于 `Information` 级别的日志，因此未创建 `Debug` 或 `Trace` 级别日志。 以下示例介绍了通过运行配置为显示 `Debug` 日志的示例应用而生成的控制台日志：

::: moniker range=">= aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "GetById", controller = "Todo", page = ""}. Executing controller action with signature Microsoft.AspNetCore.Mvc.IActionResult GetById(System.String) on controller TodoApiSample.Controllers.TodoController (TodoApiSample).
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of authorization filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of resource filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of action filters (in the following order): Microsoft.AspNetCore.Mvc.Filters.ControllerActionFilter (Order: -2147483648), Microsoft.AspNetCore.Mvc.ModelBinding.UnsupportedContentTypeFilter (Order: -3000)
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of exception filters (in the following order): None
dbug: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[1]
      Execution plan of result filters (in the following order): Microsoft.AspNetCore.Mvc.ViewFeatures.Filters.SaveTempDataFilter
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[22]
      Attempting to bind parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[44]
      Attempting to bind parameter 'id' of type 'System.String' using the name 'id' in request data ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.Binders.SimpleTypeModelBinder[45]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[23]
      Done attempting to bind parameter 'id' of type 'System.String'.
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[26]
      Attempting to validate the bound parameter 'id' of type 'System.String' ...
dbug: Microsoft.AspNetCore.Mvc.ModelBinding.ParameterBinder[27]
      Done attempting to validate the bound parameter 'id' of type 'System.String'.
info: TodoApiSample.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action TodoApiSample.Controllers.TodoController.GetById (TodoApiSample) in 32.690400000000004ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'TodoApiSample.Controllers.TodoController.GetById (TodoApiSample)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 176.9103ms 404
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```console
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[1]
      Request starting HTTP/1.1 GET http://localhost:62555/api/todo/0
dbug: Microsoft.AspNetCore.Routing.Tree.TreeRouter[1]
      Request successfully matched the route with name 'GetTodo' and template 'api/Todo/{id}'.
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Update (TodoApi)' with id '089d59b6-92ec-472d-b552-cc613dfd625d' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ActionSelector[2]
      Action 'TodoApi.Controllers.TodoController.Delete (TodoApi)' with id 'f3476abe-4bd9-4ad3-9261-3ead09607366' did not match the constraint 'Microsoft.AspNetCore.Mvc.Internal.HttpMethodActionConstraint'
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action TodoApi.Controllers.TodoController.GetById (TodoApi)
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[1]
      Executing action method TodoApi.Controllers.TodoController.GetById (TodoApi) with arguments (0) - ModelState is Valid
info: TodoApi.Controllers.TodoController[1002]
      Getting item 0
warn: TodoApi.Controllers.TodoController[4000]
      GetById(0) NOT FOUND
dbug: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action method TodoApi.Controllers.TodoController.GetById (TodoApi), returned result Microsoft.AspNetCore.Mvc.NotFoundResult.
info: Microsoft.AspNetCore.Mvc.StatusCodeResult[1]
      Executing HttpStatusCodeResult, setting HTTP status code 404
info: Microsoft.AspNetCore.Mvc.Internal.ControllerActionInvoker[2]
      Executed action TodoApi.Controllers.TodoController.GetById (TodoApi) in 0.8788ms
dbug: Microsoft.AspNetCore.Server.Kestrel[9]
      Connection id "0HL6L7NEFF2QD" completed keep alive response.
info: Microsoft.AspNetCore.Hosting.Internal.WebHost[2]
      Request finished in 2.7286ms 404
```

::: moniker-end

## <a name="log-event-id"></a>日志事件 ID

每个日志都可指定一个事件 ID  。 该示例应用通过使用本地定义的 `LoggingEvents` 类来执行此操作：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/3.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

::: moniker-end

事件 ID 与一组事件相关联。 例如，与在页面上显示项列表相关的所有日志可能是 1001。

日志记录提供程序可将事件 ID 存储在 ID 字段中，存储在日志记录消息中，或者不进行存储。 调试提供程序不显示事件 ID。 控制台提供程序在类别后的括号中显示事件 ID：

```console
info: TodoApi.Controllers.TodoController[1002]
      Getting item invalidid
warn: TodoApi.Controllers.TodoController[4000]
      GetById(invalidid) NOT FOUND
```

## <a name="log-message-template"></a>日志消息模板

每个日志都会指定一个消息模板。 消息模板可包含要填写参数的占位符。 请在占位符中使用名称而不是数字。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

::: moniker-end

占位符的顺序（而非其名称）决定了为其提供值的参数。 在以下代码中，请注意消息模板中的参数名称未按顺序排列：

```csharp
string p1 = "parm1";
string p2 = "parm2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

此代码创建了一个参数值按顺序排列的日志消息：

```text
Parameter values: parm1, parm2
```

日志记录框架按此方式工作，这样日志记录提供程序即可实现[语义日志记录，也称为结构化日志记录](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。 参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。 通过此信息，日志记录提供程序能够将参数值存储为字段。 例如，假设记录器方法调用如下所示：

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

如果要将日志发送到 Azure 表存储，则每个 Azure 表实体都可具有 `ID` 和 `RequestTime` 属性，这简化了对日志数据的查询。 无需分析文本消息的超时，查询即可找到特定 `RequestTime` 范围内的全部日志。

## <a name="logging-exceptions"></a>日志记录异常

记录器方法有可传入异常的重载，如下方示例所示：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

::: moniker-end

不同的提供程序处理异常信息的方式不同。 以下是上示代码的调试提供程序输出示例。

```text
TodoApiSample.Controllers.TodoController: Warning: GetById(55) NOT FOUND

System.Exception: Item not found exception.
   at TodoApiSample.Controllers.TodoController.GetById(String id) in C:\TodoApiSample\Controllers\TodoController.cs:line 226
```

## <a name="log-filtering"></a>日志筛选

可为特定或所有提供程序和类别指定最低日志级别。 最低级别以下的日志不会传递给该提供程序，因此不会显示或存储它们。

要禁止显示所有日志，可将 `LogLevel.None` 指定为最低日志级别。 `LogLevel.None` 的整数值为 6，它大于 `LogLevel.Critical` (5)。

### <a name="create-filter-rules-in-configuration"></a>在配置中创建筛选规则

项目模板代码调用 `CreateDefaultBuilder` 来为 Console、Debug 和 EventSource（ASP.NET Core 2.2 或更高版本）提供程序设置日志记录。 正如`CreateDefaultBuilder`本文前面部分`Logging`所述，[ 方法设置日志记录以在 ](#configuration) 部分查找配置。

配置数据按提供程序和类别指定最低日志级别，如下方示例所示：

::: moniker range=">= aspnetcore-3.0"

[!code-json[](index/samples/3.x/TodoApiSample/appsettings.json)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

::: moniker-end

此 JSON 将创建 6 条筛选规则：1 条用于调试提供程序， 4 条用于控制台提供程序， 1 条用于所有提供程序。 创建 `ILogger` 对象时，为每个提供程序选择一个规则。

### <a name="filter-rules-in-code"></a>代码中的筛选规则

下面的示例演示了如何在代码中注册筛选规则：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=2-3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

::: moniker-end

第二个 `AddFilter` 使用类型名称来指定调试提供程序。 第一个 `AddFilter` 应用于全部提供程序，因为它未指定提供程序类型。

### <a name="how-filtering-rules-are-applied"></a>如何应用筛选规则

先前示例中显示的配置数据和 `AddFilter` 代码会创建下表所示的规则。 前六条由配置示例创建，后两条由代码示例创建。

| 数字 | 提供程序      | 类别的开头为...          | 最低日志级别 |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | 调试         | 全部类别                          | 信息       |
| 2      | Console       | Microsoft.AspNetCore.Mvc.Razor.Internal | 警告           |
| 3      | Console       | Microsoft.AspNetCore.Mvc.Razor.Razor    | 调试             |
| 4      | Console       | Microsoft.AspNetCore.Mvc.Razor          | 错误             |
| 5      | Console       | 全部类别                          | 信息       |
| 6      | 全部提供程序 | 全部类别                          | 调试             |
| 7      | 全部提供程序 | 系统                                  | 调试             |
| 8      | 调试         | Microsoft                               | 跟踪             |

创建 `ILogger` 对象时，`ILoggerFactory` 对象将根据提供程序选择一条规则，将其应用于该记录器。 将按所选规则筛选 `ILogger` 实例写入的所有消息。 从可用规则中为每个提供程序和类别对选择最具体的规则。

在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：

* 选择匹配提供程序或其别名的所有规则。 如果找不到任何匹配项，则选择提供程序为空的所有规则。
* 根据上一步的结果，选择具有最长匹配类别前缀的规则。 如果找不到任何匹配项，则选择未指定类别的所有规则。
* 如果选择了多条规则，则采用最后一条  。
* 如果未选择任何规则，则使用 `MinimumLevel`。

假设你使用上述规则列表为类别“Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine”创建了 `ILogger` 对象：

* 对于调试提供程序，规则 1、6 和 8 适用。 规则 8 最为具体，因此选择了它。
* 对于控制台提供程序，符合的有规则 3、规则 4、规则 5 和规则 6。 规则 3 最为具体。

生成的 `ILogger` 实例将 `Trace` 级别及更高级别的日志发送到调试提供程序。 `Debug` 级别及更高级别的日志会发送到控制台提供程序。

### <a name="provider-aliases"></a>提供程序别名

每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称  。  对于内置提供程序，请使用以下别名：

* Console
* 调试
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>默认最低级别

仅当配置或代码中的规则对给定提供程序和类别都不适用时，最低级别设置才会生效。 下面的示例演示如何设置最低级别：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

::: moniker-end

如果没有明确设置最低级别，则默认值为 `Information`，它表示 `Trace` 和 `Debug` 日志将被忽略。

### <a name="filter-functions"></a>筛选器函数

对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数。 函数中的代码可访问提供程序类型、类别和日志级别。 例如:

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=3-11)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

::: moniker-end

## <a name="system-categories-and-levels"></a>系统类别和级别

下面是 ASP.NET Core 和 Entity Framework Core 使用的一些类别，备注中说明了可从这些类别获取的具体日志：

| 类别                            | 注意 |
| ----------------------------------- | ----- |
| Microsoft.AspNetCore                | 常规 ASP.NET Core 诊断。 |
| Microsoft.AspNetCore.DataProtection | 考虑、找到并使用了哪些密钥。 |
| Microsoft.AspNetCore.HostFiltering  | 所允许的主机。 |
| Microsoft.AspNetCore.Hosting        | HTTP 请求完成的时间和启动时间。 加载了哪些承载启动程序集。 |
| Microsoft.AspNetCore.Mvc            | MVC 和 Razor 诊断。 模型绑定、筛选器执行、视图编译和操作选择。 |
| Microsoft.AspNetCore.Routing        | 路由匹配信息。 |
| Microsoft.AspNetCore.Server         | 连接启动、停止和保持活动响应。 HTTP 证书信息。 |
| Microsoft.AspNetCore.StaticFiles    | 提供的文件。 |
| Microsoft.EntityFrameworkCore       | 常规 Entity Framework Core 诊断。 数据库活动和配置、更改检测、迁移。 |

## <a name="log-scopes"></a>日志作用域

 “作用域”可对一组逻辑操作分组  。 此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。 例如，在处理事务期间创建的每个日志都可包括事务 ID。

范围是由 `IDisposable` 方法返回的 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 类型，持续至释放为止。 要使用作用域，请在 `using` 块中包装记录器调用：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

::: moniker-end

下列代码为控制台提供程序启用作用域：

Program.cs  :

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=6)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

::: moniker-end

> [!NOTE]
> 要启用基于作用域的日志记录，必须先配置 `IncludeScopes` 控制台记录器选项。
>
> 若要了解关配置，请参阅[配置](#configuration)部分。

每条日志消息都包含作用域内的信息：

```
info: TodoApiSample.Controllers.TodoController[1002]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      Getting item 0
warn: TodoApiSample.Controllers.TodoController[4000]
      => RequestId:0HKV9C49II9CK RequestPath:/api/todo/0 => TodoApiSample.Controllers.TodoController.GetById (TodoApi) => Message attached to logs created in the using block
      GetById(0) NOT FOUND
```

## <a name="built-in-logging-providers"></a>内置日志记录提供程序

ASP.NET Core 提供以下提供程序：

* [控制台](#console-provider)
* [调试](#debug-provider)
* [EventSource](#event-source-provider)
* [EventLog](#windows-eventlog-provider)
* [TraceSource](#tracesource-provider)
* [AzureAppServicesFile](#azure-app-service-provider)
* [AzureAppServicesBlob](#azure-app-service-provider)
* [ApplicationInsights](#azure-application-insights-trace-logging)

要通过 ASP.NET Core 模块了解 stdout 和调试日志记录，请参阅 <xref:test/troubleshoot-azure-iis> 和 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。

### <a name="console-provider"></a>控制台提供程序

[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) 提供程序包向控制台发送日志输出。 

```csharp
logging.AddConsole();
```

要查看控制台日志记录输出，请在项目文件夹中打开命令提示符并运行以下命令：

```dotnetcli
dotnet run
```

### <a name="debug-provider"></a>调试提供程序

[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 提供程序包使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类（`Debug.WriteLine` 方法调用）来写入日志输出。

在 Linux 中，此提供程序将日志写入 /var/log/message  。

```csharp
logging.AddDebug();
```

### <a name="event-source-provider"></a>事件源提供程序

[Microsoft.Extensions.Logging.EventSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventSource) 提供程序包会使用名称 `Microsoft-Extensions-Logging` 跨平台写入事件源。 在 Windows 上，提供程序使用的是 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。

```csharp
logging.AddEventSourceLogger();
```

在调用 `CreateDefaultBuilder` 来生成主机时，会自动添加事件源提供程序。

::: moniker range=">= aspnetcore-3.0"

#### <a name="dotnet-trace-tooling"></a>dotnet 跟踪工具

[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) 工具是一种跨平台 CLI 全局工具，可用于收集正在运行的进程的 .NET Core 跟踪。 该工具会使用 <xref:Microsoft.Extensions.Logging.EventSource> 收集 <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> 提供程序数据。

使用以下命令安装 dotnet 跟踪工具：

```dotnetcli
dotnet tool install --global dotnet-trace
```

使用 dotnet 跟踪工具从应用中收集跟踪：

1. 如果应用不使用 `CreateDefaultBuilder` 生成主机，则请向应用的日志记录配置添加[事件源提供程序](#event-source-provider)

1. 使用 `dotnet run` 命令运行此应用。

1. 确定 .NET Core 应用的进程标识符 (PID)：

   * 在 Windows 上，使用下述方法之一：
     * 任务管理器 (Ctrl+Alt+Del)
     * [tasklist 命令](/windows-server/administration/windows-commands/tasklist)
     * [Get-Process Powershell 命令](/powershell/module/microsoft.powershell.management/get-process)
   * 在 Linux 上，使用 [pidof 命令](https://refspecs.linuxfoundation.org/LSB_5.0.0/LSB-Core-generic/LSB-Core-generic/pidof.html)。

   找到进程的 PID，它与应用的程序集的名称相同。

1. 执行 `dotnet trace` 命令。

   常规命令语法：

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"
   ```

   使用 PowerShell 命令行界面时，将 `--providers` 值用单引号 (`'`) 引起来：

   ```dotnetcli
   dotnet trace collect -p {PID} 
       --providers 'Microsoft-Extensions-Logging:{Keyword}:{Event Level}
           :FilterSpecs=\"
               {Logger Category 1}:{Event Level 1};
               {Logger Category 2}:{Event Level 2};
               ...
               {Logger Category N}:{Event Level N}\"'
   ```

   在非 Windows 平台上，添加 `-f speedscope` 选项，将输出跟踪文件更改为 `speedscope`。

   | 关键字 | 描述 |
   | :-----: | ----------- |
   | 1       | 记录有关 `LoggingEventSource` 的 meta 事件。 请不要从 `ILogger` 记录事件。 |
   | 2       | 在调用 `Message` 时启用 `ILogger.Log()` 事件。 以编程（未格式化）方式提供信息。 |
   | 4       | 在调用 `FormatMessage` 时启用 `ILogger.Log()` 事件。 提供格式化字符串版本的信息。 |
   | 8       | 在调用 `MessageJson` 时启用 `ILogger.Log()` 事件。 提供参数的 JSON 表示形式。 |

   | 事件级别 | 描述     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `FilterSpecs` 和 `{Logger Category}` 的 `{Event Level}` 条目表示其他日志筛选条件。 请用分号 (`FilterSpecs`) 分隔 `;` 条目。

   下例使用 Windows 命令界面（ **值不用单引号引起来）** `--providers`：

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   上面的命令会激活：

   * 事件源记录器，它用于为错误 (`4`) 生成格式化字符串 (`2`)。
   * `Microsoft.AspNetCore.Hosting` 日志记录级别 (`Informational`) 的 `4` 日志记录。

1. 通过按 Enter 键或 Ctrl+C 停止 dotnet 跟踪工具。

   跟踪使用名称 trace.nettrace 保存在执行  *命令的文件夹中*`dotnet trace`。

1. 使用[预览](#perfview)功能打开跟踪。 打开 trace.nettrace 文件并浏览跟踪事件  。

有关详细信息，请参见:

* [跟踪性能分析实用工具 (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace)（.NET Core 文档）
* [跟踪性能分析实用工具 (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md)（dotnet/诊断 GitHub 存储库文档）
* [LoggingEventSource 类](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource)（.NET API 浏览器）
* <xref:System.Diagnostics.Tracing.EventLevel>
* [LoggingEventSource 引用源 (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs) &ndash; 若要获取不同版本的引用源，请将分支更改为 `release/{Version}`，其中 `{Version}` 是所需的 ASP.NET Core 版本。
* [预览](#perfview) &ndash; 用于查看事件源跟踪。

#### <a name="perfview"></a>Perfview

::: moniker-end

使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。 虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET Core 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。

要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging`  。 （请勿遗漏字符串起始处的星号。）

![其他 Perfview 提供程序](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows EventLog 提供程序

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 提供程序包向 Windows 事件日志发送日志输出。

```csharp
logging.AddEventLog();
```

[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)允许传入 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>。 如果为 `null` 或未指定，则使用以下默认设置：

* `LogName` &ndash; "Application"
* `SourceName` &ndash; ".NET Runtime"
* `MachineName` &ndash; local machine

为[警告等级和更高等级](#log-level)记录事件。 若要记录低于 `Warning` 的事件，请显式设置日志级别。 例如，将以下内容添加到 appsettings 文件中  ：

```json
"EventLog": {
  "LogLevel": {
    "Default": "Information"
  }
}
```

### <a name="tracesource-provider"></a>TraceSource 提供程序

[Microsoft.Extensions.Logging.TraceSource](https://www.nuget.org/packages/Microsoft.Extensions.Logging.TraceSource) 提供程序包使用 <xref:System.Diagnostics.TraceSource> 库和提供程序。

```csharp
logging.AddTraceSource(sourceSwitchName);
```

[AddTraceSource 重载](xref:Microsoft.Extensions.Logging.TraceSourceFactoryExtensions) 允许传入资源开关和跟踪侦听器。

要使用此提供程序，应用必须在 .NET Framework（而非 .NET Core）上运行。 提供程序可将消息路由到多个[侦听器](/dotnet/framework/debug-trace-profile/trace-listeners)，例如示例应用中使用的 <xref:System.Diagnostics.TextWriterTraceListener>。

### <a name="azure-app-service-provider"></a>Azure 应用服务提供程序

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)。

```csharp
logging.AddAzureWebAppDiagnostics();
```

::: moniker range=">= aspnetcore-3.0"

共享框架中不包括该提供程序包。 若要使用提供程序，请将提供程序包添加到项目。

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不包括此提供程序包。 如果面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包，请向项目添加提供程序包。 

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

要配置提供程序设置，请使用 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 和 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>，如以下示例所示：

[!code-csharp[](index/samples/3.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=17-28)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

要配置提供程序设置，请使用 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 和 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>，如以下示例所示：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_AzLogOptions&highlight=19-27)]

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 重载允许传入 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>。 设置对象可以覆盖默认设置，例如日志记录输出模板、blob 名称和文件大小限制。 （“输出模板”是一个消息模板，除了通过  *方法调用提供的内容之外，还可将其应用于所有日志。* `ILogger`）

::: moniker-end

在部署应用服务应用时，应用程序将采用 Azure 门户中“应用服务”页面下的[应用服务日志](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag)部分的设置  。 更新以下设置后，更改立即生效，无需重启或重新部署应用。

* 应用程序日志记录(Filesystem) 
* 应用程序日志记录(Blob) 

日志文件的默认位置是 D:*home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt\\*  。 默认文件大小上限为 10 MB，默认最大保留文件数为 2。 默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt  。

该提供程序仅当项目在 Azure 环境中运行时有效。 项目在本地运行时，该提供程序无效 &mdash; 它不会写入本地文件或 blob 的本地开发存储。

#### <a name="azure-log-streaming"></a>Azure 日志流式处理

通过 Azure 日志流式处理，可从以下位置实时查看日志活动：

* 应用服务器
* Web 服务器
* 请求跟踪失败

要配置 Azure 日志流式处理，请执行以下操作：

* 从应用的门户页导航到“应用服务日志”页  。
* 将“应用程序日志记录(Filesystem)”设置为“开”   。
* 选择日志级别  。 此设置仅适用于 Azure 日志流，不适用于应用中的其他日志记录提供程序。

导航到“日志流”页面来查看应用消息  。 它们由应用通过 `ILogger` 接口记录。

### <a name="azure-application-insights-trace-logging"></a>Azure Application Insights 跟踪日志记录

[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 提供程序包将日志写入 Azure Application Insights。 Application Insights 是一项服务，可监视 Web 应用并提供用于查询和分析遥测数据的工具。 如果使用此提供程序，则可以使用 Application Insights 工具来查询和分析日志。

日志记录提供程序作为 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)（这是提供 ASP.NET Core 的所有可用遥测的包）的依赖项包括在内。 如果使用此包，则无需安装提供程序包。

请勿使用用于 ASP.NET 4.x 的 [Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 包&mdash;。

有关更多信息，请参见以下资源：

* [Application Insights 概述](/azure/application-insights/app-insights-overview)
* [用于 ASP.NET Core 应用程序的 Application Insights](/azure/azure-monitor/app/asp-net-core) - 如果想要实现各种 Application Insights 遥测以及日志记录，请从这里开始。
* [.NET Core ILogger 日志的 ApplicationInsightsLoggerProvider](/azure/azure-monitor/app/ilogger) - 如果想要在没有其他 Application Insights 遥测的情况下实现日志记录提供程序，请从这里开始。
* [Application Insights 日志记录适配器](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-trace-logs)。
* [安装、配置和初始化 Application Insights SDK](/learn/modules/instrument-web-app-code-with-application-insights) - Microsoft Learn 网站上的交互式教程。

## <a name="third-party-logging-providers"></a>第三方日志记录提供程序

适用于 ASP.NET Core 的第三方日志记录框架：

* [elmah.io](https://elmah.io/)（[GitHub 存储库](https://github.com/elmahio/Elmah.Io.Extensions.Logging)）
* [Gelf](https://docs.graylog.org/en/2.3/pages/gelf.html)（[GitHub 存储库](https://github.com/mattwcole/gelf-extensions-logging)）
* [JSNLog](https://jsnlog.com/)（[GitHub 存储库](https://github.com/mperdeck/jsnlog)）
* [KissLog.net](https://kisslog.net/)（[GitHub 存储库](https://github.com/catalingavan/KissLog-net)）
* [Log4Net](https://logging.apache.org/log4net/)（[GitHub 存储库](https://github.com/huorswords/Microsoft.Extensions.Logging.Log4Net.AspNetCore)）
* [Loggr](https://loggr.net/)（[GitHub 存储库](https://github.com/imobile3/Loggr.Extensions.Logging)）
* [NLog](https://nlog-project.org/)（[GitHub 存储库](https://github.com/NLog/NLog.Extensions.Logging)）
* [Sentry](https://sentry.io/welcome/)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）
* [Serilog](https://serilog.net/)（[GitHub 存储库](https://github.com/serilog/serilog-aspnetcore)）
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[Github 存储库](https://github.com/googleapis/google-cloud-dotnet)）

某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。

使用第三方框架类似于使用以下内置提供程序之一：

1. 将 NuGet 包添加到你的项目。
1. 调用日志记录框架提供的 `ILoggerFactory` 扩展方法。

有关详细信息，请参阅各提供程序的相关文档。 Microsoft 不支持第三方日志记录提供程序。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/logging/loggermessage>
