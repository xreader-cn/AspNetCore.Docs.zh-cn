---
title: .NET Core 和 ASP.NET Core 中的日志记录
author: rick-anderson
description: 了解如何使用由 Microsoft Extension.Logging NuGet 包提供的日志记录框架。
ms.author: riande
ms.custom: mvc
ms.date: 6/29/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/logging/index
ms.openlocfilehash: a4962c3132c60b68cb2d4b5a97e9b082aba15d15
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "87330709"
---
# <a name="logging-in-net-core-and-aspnet-core"></a>.NET Core 和 ASP.NET Core 中的日志记录

::: moniker range=">= aspnetcore-3.0"

作者：[Kirk Larkin](https://twitter.com/serpent5)、[Juergen Gutsch](https://github.com/JuergenGutsch) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。 本文介绍了如何将日志记录 API 与内置提供程序一起使用。

本文中所述的大多数代码示例都来自 ASP.NET Core 应用。 这些代码片段的日志记录特定部分适用于任何使用[通用主机](xref:fundamentals/host/generic-host)的 .NET Core 应用。 ASP.NET Core Web 应用模板使用通用主机。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples/3.x)（[如何下载](xref:index#how-to-download-a-sample)）

<a name="lp"></a>

## <a name="logging-providers"></a>日志记录提供程序

日志记录提供程序存储日志，但显示日志的 `Console` 提供程序除外。 例如，Azure Application Insights 提供程序将日志存储在 Azure Application Insights 中。 可以启用多个提供程序。

默认 ASP.NET Core Web 应用模板：

* 使用[通用主机](xref:fundamentals/host/generic-host)。
* 调用 <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder%2A>，这将添加以下日志记录提供程序：
  * [控制台](#console)
  * [调试](#debug)
  * [EventSource](#event-source)
  * [EventLog](#welog)：仅限 Windows

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_TemplateCode&highlight=9)]

上面的代码显示了使用 ASP.NET Core Web 应用模板创建的 `Program` 类。 接下来的几节提供基于使用通用主机的 ASP.NET Core Web 应用模板的示例。 本文档稍后将介绍[非托管控制台应用](#nhca)。

若要替代`Host.CreateDefaultBuilder` 添加的默认日志记录提供程序集，请调用 `ClearProviders` 并添加所需的日志记录提供程序。 例如，以下代码：

* 调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A> 以从生成器中删除所有 <xref:Microsoft.Extensions.Logging.ILoggerProvider> 实例。
* 添加[控制台](#console)日志记录提供程序。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_AddProvider&highlight=5-6)]

有关其他提供程序，请参阅：

* [内置日志记录提供程序](#bilp)
* [第三方日志记录提供程序](#third-party-logging-providers)。

## <a name="create-logs"></a>创建日志

若要创建日志，请使用[依赖项注入](xref:fundamentals/dependency-injection) (DI) 中的 <xref:Microsoft.Extensions.Logging.ILogger%601>对象。

如下示例中：

* 创建一个记录器 `ILogger<AboutModel>`，该记录器使用类型为 `AboutModel` 的完全限定名称的日志类别。 日志类别是与每个日志关联的字符串。
* 调用 <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation*> 以在 `Information` 级别登录。 日志“级别”代表所记录事件的严重程度。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=5,14)]

本文档稍后将更详细地介绍[级别](#log-level)和[类别](#log-category)。

有关 Blazor 的信息，请参阅本文档中的[在 Blazor 和 Blazor WebAssembly](#clib) 中创建日志。

[在 Main 和 Startup 中创建日志](#clms)介绍如何在 `Main` 和 `Startup` 中创建日志。

## <a name="configure-logging"></a>配置日志记录

日志配置通常由 appsettings `{Environment}`.json 文件的 `Logging` 部分提供 。 以下 appsettings.Development.json 文件由 ASP.NET Core Web 应用模板生成：

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Development.json)]

在上述 JSON 中：

* 指定了 `"Default"`、`"Microsoft"` 和 `"Microsoft.Hosting.Lifetime"` 类别。
* `"Microsoft"` 类别适用于以 `"Microsoft"` 开头的所有类别。 例如，此设置适用于 `"Microsoft.AspNetCore.Routing.EndpointMiddleware"` 类别。
* `"Microsoft"` 类别在日志级别 `Warning` 或更高级别记录。
* `"Microsoft.Hosting.Lifetime"` 类别比 `"Microsoft"` 类别更具体，因此 `"Microsoft.Hosting.Lifetime"` 类别在日志级别“Information”和更高级别记录。
* 未指定特定的日志提供程序，因此 `LogLevel` 适用于所有启用的日志记录提供程序，但 [Windows EventLog](#welog) 除外。

`Logging` 属性可以具有 <xref:Microsoft.Extensions.Logging.LogLevel> 和日志提供程序属性。 `LogLevel` 指定要针对所选类别进行记录的最低[级别](#log-level)。 在前面的 JSON 中，指定了 `Information` 和 `Warning` 日志级别。 `LogLevel` 表示日志的严重性，范围为 0 到 6：

`Trace` = 0、`Debug` = 1、`Information` = 2、`Warning` = 3、`Error` = 4、`Critical` = 5 和 `None` = 6。

指定 `LogLevel` 时，将为指定级别和更高级别的消息启用日志记录。 在前面的 JSON 中，记录了 `Information` 及更高级别的 `Default` 类别。 例如，记录了 `Information`、`Warning`、`Error` 和 `Critical` 消息。 如果未指定 `LogLevel`，则日志记录默认为 `Information` 级别。 有关详细信息，请参阅[日志级别](#llvl)。

提供程序属性可以指定 `LogLevel` 属性。 提供程序下的 `LogLevel` 指定要为该提供程序记录的级别，并替代非提供程序日志设置。 请考虑使用以下 appsettings.json 文件：

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Prod2.json)]

`Logging.{providername}.LogLevel` 中的设置将替代 `Logging.LogLevel` 中的设置。 在前面的 JSON 中，`Debug` 提供程序的默认日志级别设置为 `Information`：

`Logging:Debug:LogLevel:Default:Information`

前面的设置为每个 `Logging:Debug:` 类别（`Microsoft.Hosting` 除外）指定 `Information` 日志级别。 当列出特定类别时，该特定类别将替代默认类别。 在前面的 JSON 中，`Logging:Debug:LogLevel` 类别 `"Microsoft.Hosting"` 和 `"Default"` 替代 `Logging:LogLevel` 中的设置

可以为以下任何一项指定最低日志级别：

* 特定提供程序：例如，`Logging:EventSource:LogLevel:Default:Information`
* 特定类别：例如，`Logging:LogLevel:Microsoft:Warning`
* 所有提供程序和所有类别：`Logging:LogLevel:Default:Warning`

低于最低级别的任何日志均不会执行以下操作：

* 传递到提供程序。
* 记录或显示。

要阻止所有日志，请指定 [LogLevel.None](xref:Microsoft.Extensions.Logging.LogLevel)。 `LogLevel.None` 的值为 6，该值高于 `LogLevel.Critical` (5)。

如果提供程序支持[日志作用域](#logscopes)，则 `IncludeScopes` 将指示是否启用这些域。 有关详细信息，请参阅[日志范围](#logscopes)

以下 appsettings.json 文件包含默认情况下启用的所有提供程序：

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Production.json)]

在上述示例中：

* 类别和级别不是建议的值。 提供该示例是为了显示所有默认提供程序。
* `Logging.{providername}.LogLevel` 中的设置将替代 `Logging.LogLevel` 中的设置。 例如，`Debug.LogLevel.Default` 中的级别将替代 `LogLevel.Default` 中的级别。
* 将使用每个默认提供程序别名。 每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称。 内置提供程序别名包括：
  * 控制台
  * 调试
  * EventSource
  * EventLog
  * AzureAppServicesFile
  * AzureAppServicesBlob
  * ApplicationInsights

## <a name="set-log-level-by-command-line-environment-variables-and-other-configuration"></a>通过命令行、环境变量和其他配置设置日志级别

日志级别可以由任何[配置提供程序](xref:fundamentals/configuration/index)设置。 

[!INCLUDE[](~/includes/environmentVarableColon.md)]

以下命令：

* 在 Windows 上，将环境密钥 `Logging:LogLevel:Microsoft` 设置为值 `Information`。
* 使用通过 ASP.NET Core Web 应用模板创建的应用时，请测试设置。 使用 `set` 之后，必须在项目目录中运行 `dotnet run` 命令。

```cmd
set Logging__LogLevel__Microsoft=Information
dotnet run
```

前面的环境设置：

* 仅在进程中设置，这些进程是从设置进程的命令窗口启动的。
* 不由使用 Visual Studio 启动的浏览器读取。

以下 [setx](/windows-server/administration/windows-commands/setx) 命令还可以在 Windows 上设置环境键和值。 与 `set` 不同，`setx` 设置是持久的。 `/M` 开关在系统环境中设置变量。 如果未使用 `/M`，则设置用户环境变量。

```cmd
setx Logging__LogLevel__Microsoft=Information /M
```

在 [Azure 应用服务](https://azure.microsoft.com/services/app-service/)上，选择“设置”>“配置”页面上的“新应用程序设置” 。 Azure 应用服务应用程序设置：

* 已静态加密且通过加密的通道进行传输。
* 已作为环境变量公开。

有关详细信息，请参阅 [Azure 应用：使用 Azure 门户替代应用配置](xref:host-and-deploy/azure-apps/index#override-app-configuration-using-the-azure-portal)。

有关使用环境变量设置 ASP.NET Core 配置值的详细信息，请参阅[环境变量](xref:fundamentals/configuration/index#environment-variables)。 有关使用其他配置源（包括命令行、Azure Key Vault、Azure 应用配置、其他文件格式等）的信息，请参阅 <xref:fundamentals/configuration/index>。

## <a name="how-filtering-rules-are-applied"></a>如何应用筛选规则

创建 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象时，<xref:Microsoft.Extensions.Logging.ILoggerFactory> 对象将根据提供程序选择一条规则，将其应用于该记录器。 将按所选规则筛选 `ILogger` 实例写入的所有消息。 从可用规则中为每个提供程序和类别对选择最具体的规则。

在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：

* 选择匹配提供程序或其别名的所有规则。 如果找不到任何匹配项，则选择提供程序为空的所有规则。
* 根据上一步的结果，选择具有最长匹配类别前缀的规则。 如果找不到任何匹配项，则选择未指定类别的所有规则。
* 如果选择了多条规则，则采用最后一条  。
* 如果未选择任何规则，则使用 `MinimumLevel`。

<a name="dnrvs"></a>

## <a name="logging-output-from-dotnet-run-and-visual-studio"></a>记录来自 dotnet run 和 Visual Studio 的输出

将显示使用[默认日志记录提供程序](#lp)创建的日志：

* 在 Visual Studio 中
  * 在调试时，在“调试输出”窗口中。
  * 在“ASP.NET Core Web 服务器”窗口中。
* 使用 `dotnet run` 运行应用时，在控制台窗口中。

以“Microsoft”类别开头的日志来自 ASP.NET Core 框架代码。 ASP.NET Core 和应用程序代码使用相同的日志记录 API 和提供程序。

<a name="lcat"></a>

## <a name="log-category"></a>日志类别

创建 `ILogger` 对象时，将指定类别。 该类别包含在由此 `ILogger` 实例创建的每条日志消息中。 类别字符串是任意的，但约定将使用类名称。 例如，在控制器中，名称可能为 `"TodoApi.Controllers.TodoController"`。 ASP.NET Core Web 应用使用 `ILogger<T>` 自动获取使用完全限定类型名称 `T` 作为类别的 `ILogger` 实例：

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Privacy.cshtml.cs?name=snippet)]

要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：

[!code-csharp[](index/samples/3.x/TodoApiDTO/Pages/Contact.cshtml.cs?name=snippet)]

`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。

<a name="llvl"></a>

## <a name="log-level"></a>日志级别

下表列出了 <xref:Microsoft.Extensions.Logging.LogLevel> 值、方便的 `Log{LogLevel}` 扩展方法以及建议的用法：

| LogLevel | “值” | 方法 | 描述 |
| -------- | ----- | ------ | ----------- |
| [Trace](xref:Microsoft.Extensions.Logging.LogLevel) | 0 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogTrace%2A> | 包含最详细的消息。 这些消息可能包含敏感的应用数据。 这些消息默认情况下处于禁用状态，并且不应在生产中启用。 |
| [调试](xref:Microsoft.Extensions.Logging.LogLevel) | 1 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogDebug%2A> | 用于调试和开发。 由于量大，请在生产中小心使用。 |
| [信息](xref:Microsoft.Extensions.Logging.LogLevel) | 2 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogInformation%2A> | 跟踪应用的常规流。 可能具有长期值。 |
| [警告](xref:Microsoft.Extensions.Logging.LogLevel) | 3 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogWarning%2A> | 对于异常事件或意外事件。 通常包括不会导致应用失败的错误或情况。 |
| [错误](xref:Microsoft.Extensions.Logging.LogLevel) | 4 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogError%2A> | 表示无法处理的错误和异常。 这些消息表示当前操作或请求失败，而不是整个应用失败。 |
| [严重](xref:Microsoft.Extensions.Logging.LogLevel) | 5 | <xref:Microsoft.Extensions.Logging.LoggerExtensions.LogCritical%2A> | 需要立即关注的失败。 例如数据丢失、磁盘空间不足。 |
| [无](xref:Microsoft.Extensions.Logging.LogLevel) | 6 | | 指定日志记录类别不应写入任何消息。 |

在上表中，`LogLevel` 按严重性由低到高的顺序列出。

[Log](xref:Microsoft.Extensions.Logging.LoggerExtensions) 方法的第一个参数 <xref:Microsoft.Extensions.Logging.LogLevel> 指示日志的严重性。 大多数开发人员调用 [Log{LogLevel}](xref:Microsoft.Extensions.Logging.LoggerExtensions) 扩展方法，而不调用 `Log(LogLevel, ...)`。 `Log{LogLevel}` 扩展方法[调用 Log 方法并指定 LogLevel](https://github.com/dotnet/extensions/blob/release/3.1/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。 例如，以下两个日志记录调用功能相同，并生成相同的日志：

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet0&highlight=6-7)]

`MyLogEvents.TestItem` 是事件 ID。 `MyLogEvents` 是示例应用的一部分，并显示在[日志事件 ID](#leid) 部分中。

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

下面的代码会创建 `Information` 和 `Warning` 日志：

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

在前面的代码中，第一个 `Log{LogLevel}` 参数 `MyLogEvents.GetItem` 是[日志事件 ID](#leid)。 第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。 本文档后面的[消息模板](#lmt)部分介绍了方法参数。

调用相应的 `Log{LogLevel}` 方法，以控制写入到特定存储介质的日志输出量。 例如：

* 生产中：
  * 在 `Trace` 或 `Information` 级别记录日志会产生大量详细的日志消息。 为了控制成本且不超过数据存储限制，请将 `Trace` 和 `Information` 级别消息记录到容量大、成本低的数据存储中。 考虑将 `Trace` 和 `Information` 限制为特定类别。
  * 从 `Warning` 到 `Critical` 级别的日志记录应该很少产生日志消息。
    * 成本和存储限制通常不是问题。
    * 很少有日志可以为数据存储选择提供更大的灵活性。
* 在开发过程中：
  * 设置为 `Warning`。
  * 在进行故障排除时，添加 `Trace` 或 `Information` 消息。 若要限制输出，请仅对正在调查的类别设置 `Trace` 或 `Information`。

ASP.NET Core 为框架事件写入日志。 例如，考虑以下对象的日志输出：

* 使用 ASP.NET Core 模板创建的 Razor Pages 应用。
* 日志记录设置为 `Logging:Console:LogLevel:Microsoft:Information`
* 导航到“隐私”页面：

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 GET https://localhost:5001/Privacy
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint '/Privacy'
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[3]
      Route matched with {page = "/Privacy"}. Executing page /Privacy
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[101]
      Executing handler method DefaultRP.Pages.PrivacyModel.OnGet - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[102]
      Executed handler method OnGet, returned result .
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[103]
      Executing an implicit handler method - ModelState is Valid
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[104]
      Executed an implicit handler method, returned result Microsoft.AspNetCore.Mvc.RazorPages.PageResult.
info: Microsoft.AspNetCore.Mvc.RazorPages.Infrastructure.PageActionInvoker[4]
      Executed page /Privacy in 74.5188ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint '/Privacy'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 149.3023ms 200 text/html; charset=utf-8
```

以下 JSON 设置了 `Logging:Console:LogLevel:Microsoft:Information`：

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

<a name="leid"></a>

## <a name="log-event-id"></a>日志事件 ID

每个日志都可指定一个事件 ID  。 示例应用使用 `MyLogEvents` 类来定义事件 ID：

<!-- Review: to bad there is no way to use an enum for event ID's -->
[!code-csharp[](index/samples/3.x/TodoApiDTO/Models/MyLogEvents.cs?name=snippet_LoggingEvents)]

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

事件 ID 与一组事件相关联。 例如，与在页面上显示项列表相关的所有日志可能是 1001。

日志记录提供程序可将事件 ID 存储在 ID 字段中，存储在日志记录消息中，或者不进行存储。 调试提供程序不显示事件 ID。 控制台提供程序在类别后的括号中显示事件 ID：

```console
info: TodoApi.Controllers.TodoItemsController[1002]
      Getting item 1
warn: TodoApi.Controllers.TodoItemsController[4000]
      Get(1) NOT FOUND
```

一些日志记录提供程序将事件 ID 存储在一个字段中，该字段允许对 ID 进行筛选。

<a name="lmt"></a>

## <a name="log-message-template"></a>日志消息模板
<!-- Review, Each log API uses a message template. -->
每个日志 API 都使用一个消息模板。 消息模板可包含要填写参数的占位符。 请在占位符中使用名称而不是数字。

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TodoItemsController.cs?name=snippet_CallLogMethods&highlight=4,10)]

占位符的顺序（而非其名称）决定了为其提供值的参数。 在以下代码中，消息模板中的参数名称不按顺序排列：

```csharp
string p1 = "param1";
string p2 = "param2";
_logger.LogInformation("Parameter values: {p2}, {p1}", p1, p2);
```

上面的代码按顺序通过参数值创建日志消息：

```text
Parameter values: param1, param2
```

此方法允许日志记录提供程序实现[语义或结构化日志记录](https://github.com/NLog/NLog/wiki/How-to-use-structured-logging)。 参数本身会传递给日志记录系统，而不仅仅是格式化的消息模板。 这使日志记录提供程序可以将参数值存储为字段。 例如，考虑使用以下记录器方法：

```csharp
_logger.LogInformation("Getting item {Id} at {RequestTime}", id, DateTime.Now);
```

例如，登录到 Azure 表存储时：

* 每个 Azure 表实体都可以有 `ID` 和 `RequestTime` 属性。
* 具有属性的表简化了对记录数据的查询。 例如，查询可以找到特定 `RequestTime` 范围内的所有日志，而不必分析文本消息中的时间。

## <a name="log-exceptions"></a>记录异常

记录器方法的重载采用异常参数：

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Exp)]

[!INCLUDE[](~/includes/MyDisplayRouteInfoBoth.md)]

异常日志记录是特定于提供程序的。

### <a name="default-log-level"></a>默认日志级别

如果未设置默认日志级别，则默认的日志级别值为 `Information`。

例如，考虑以下 Web 应用：

* 使用 ASP.NET Web 应用模板创建的应用。
* 已删除 appsettings.json 和 appsettings.Development.json 或对其进行重命名 。

使用上述设置，导航到隐私或主页会生成许多 `Trace`、`Debug` 和 `Information` 消息，并在类别名称中包含 `Microsoft`。

如果未在配置中设置默认日志级别，以下代码会设置默认日志级别：

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_MinLevel&highlight=10)]

<!-- review required: I say this a couple times -->
通常，日志级别应在配置中指定，而不是在代码中指定。

### <a name="filter-function"></a>筛选器函数

对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数：

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterFunction)]

如果类别包含 `Controller` 或 `Microsoft`，并且日志级别为 `Information` 或更高级别，以上代码会显示控制台日志。

通常，日志级别应在配置中指定，而不是在代码中指定。

## <a name="aspnet-core-and-ef-core-categories"></a>ASP.NET Core 和 EF Core 类别

下表包含 ASP.NET Core 和 Entity Framework Core 使用的一些类别，并带有有关日志的注释：

| 类别                            | 说明 |
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

若要在控制台窗口中查看更多类别，请将 appsettings.Development.json 设置为以下各项：

[!code-json[](index/samples/3.x/MyMain/appsettings.Trace.json)]

<!-- Review: What other providers support scopes? Console is not generally used in staging/production  -->

<a name="logscopes"></a>

## <a name="log-scopes"></a>日志作用域

 “作用域”可对一组逻辑操作分组  。 此分组可用于将相同的数据附加到作为集合的一部分而创建的每个日志。 例如，在处理事务期间创建的每个日志都可包括事务 ID。

范围：

* 是 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 方法返回的 <xref:System.IDisposable> 类型。
* 持续到处置完毕。

以下提供程序支持范围：

* `Console`
* [AzureAppServicesFile 和 AzureAppServicesBlob](xref:Microsoft.Extensions.Logging.AzureAppServices.BatchingLoggerOptions.IncludeScopes)

要使用作用域，请在 `using` 块中包装记录器调用：

[!code-csharp[](index/samples/3.x/TodoApiDTO/Controllers/TestController.cs?name=snippet_Scopes)]

以下 JSON 为控制台提供程序启用范围：

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.Scopes.json)]

下列代码为控制台提供程序启用作用域：

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_Scopes)]

通常，日志记录应在配置中指定，而不是在代码中指定。

<a name="bilp"></a>

## <a name="built-in-logging-providers"></a>内置日志记录提供程序

ASP.NET Core 包含以下日志记录提供程序：

* [控制台](#console)
* [调试](#debug)
* [EventSource](#event-source)
* [EventLog](#welog)
* [AzureAppServicesFile 和 AzureAppServicesBlob](#azure-app-service)
* [ApplicationInsights](#azure-application-insights)

要了解 `stdout` 以及如何通过 ASP.NET Core 模块调试日志记录，请参阅 <xref:test/troubleshoot-azure-iis> 和 <xref:host-and-deploy/aspnet-core-module#log-creation-and-redirection>。

### <a name="console"></a>控制台

`Console` 提供程序将输出记录到控制台。 如需详细了解如何在开发环境中查看 `Console` 日志，请参阅[记录来自 dotnet run 和 Visual Studio 的输出](#dnrvs)。

### <a name="debug"></a>调试

`Debug` 提供程序通过使用 [System.Diagnostics.Debug](/dotnet/api/system.diagnostics.debug) 类来写入日志输出。 对 `System.Diagnostics.Debug.WriteLine` 的调用写入到 `Debug` 提供程序。

在 Linux 上，`Debug` 提供程序日志位置取决于分发，并且可以是以下位置之一：

* */var/log/message*
* */var/log/syslog*

### <a name="event-source"></a>事件来源

`EventSource` 提供程序写入名称为 `Microsoft-Extensions-Logging` 的跨平台事件源。 在 Windows 上，提供程序使用的是 [ETW](https://msdn.microsoft.com/library/windows/desktop/bb968803)。

#### <a name="dotnet-trace-tooling"></a>dotnet 跟踪工具

[dotnet-trace](/dotnet/core/diagnostics/dotnet-trace) 工具是一种跨平台 CLI 全局工具，可用于收集正在运行的进程的 .NET Core 跟踪。 该工具会使用 <xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource> 收集 <xref:Microsoft.Extensions.Logging.EventSource> 提供程序数据。

有关安装说明，请参阅 [dotnet-trace](/dotnet/core/diagnostics/dotnet-trace)。

使用 dotnet 跟踪工具从应用中收集跟踪：

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

   | 关键字 | 说明 |
   | :-----: | ----------- |
   | 1       | 记录有关 `LoggingEventSource` 的 meta 事件。 请不要从 `ILogger` 记录事件。 |
   | 2       | 在调用 `ILogger.Log()` 时启用 `Message` 事件。 以编程（未格式化）方式提供信息。 |
   | 4       | 在调用 `ILogger.Log()` 时启用 `FormatMessage` 事件。 提供格式化字符串版本的信息。 |
   | 8       | 在调用 `ILogger.Log()` 时启用 `MessageJson` 事件。 提供参数的 JSON 表示形式。 |

   | 事件级别 | 描述     |
   | :---------: | --------------- |
   | 0           | `LogAlways`     |
   | 1           | `Critical`      |
   | 2           | `Error`         |
   | 3           | `Warning`       |
   | 4           | `Informational` |
   | 5           | `Verbose`       |

   `{Logger Category}` 和 `{Event Level}` 的 `FilterSpecs` 条目表示其他日志筛选条件。 请用分号 (`;`) 分隔 `FilterSpecs` 条目。

   下例使用 Windows 命令界面（`--providers` 值不用单引号引起来）  ：

   ```dotnetcli
   dotnet trace collect -p {PID} --providers Microsoft-Extensions-Logging:4:2:FilterSpecs=\"Microsoft.AspNetCore.Hosting*:4\"
   ```

   上面的命令会激活：

   * 事件源记录器，它用于为错误 (`2`) 生成格式化字符串 (`4`)。
   * `Informational` 日志记录级别 (`4`) 的 `Microsoft.AspNetCore.Hosting` 日志记录。

1. 通过按 Enter 键或 Ctrl+C 停止 dotnet 跟踪工具。

   跟踪使用名称 trace.nettrace 保存在执行 `dotnet trace` 命令的文件夹中  。

1. 使用[预览](#perfview)功能打开跟踪。 打开 trace.nettrace 文件并浏览跟踪事件  。

如果应用不使用 `CreateDefaultBuilder` 生成主机，则请向应用的日志记录配置添加[事件源提供程序](#event-source-provider)

有关详细信息，请参见:

* [跟踪性能分析实用工具 (dotnet-trace)](/dotnet/core/diagnostics/dotnet-trace)（.NET Core 文档）
* [跟踪性能分析实用工具 (dotnet-trace)](https://github.com/dotnet/diagnostics/blob/master/documentation/dotnet-trace-instructions.md)（dotnet/诊断 GitHub 存储库文档）
* [LoggingEventSource 类](xref:Microsoft.Extensions.Logging.EventSource.LoggingEventSource)（.NET API 浏览器）
* <xref:System.Diagnostics.Tracing.EventLevel>
* [LoggingEventSource 参考源 (3.0)](https://github.com/dotnet/extensions/blob/release/3.0/src/Logging/Logging.EventSource/src/LoggingEventSource.cs)：要获得其他版本的参考源，请将分支更改为 `release/{Version}`，其中 `{Version}` 是所需的 ASP.NET Core 版本。
* [Perfview](#perfview)：适用于查看事件源跟踪。

#### <a name="perfview"></a>Perfview

使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。 虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET Core 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。

要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging` 。 请勿遗漏字符串起始处的 `*`。

<a name="welog"></a>

### <a name="windows-eventlog"></a>Windows 事件日志

`EventLog` 提供程序将日志输出发送到 Windows 事件日志。 与其他提供程序不同，`EventLog` 提供程序不继承默认的非提供程序设置。 如果未指定 `EventLog` 日志设置，则它们[默认为 LogLevel.Warning](https://github.com/dotnet/extensions/blob/release/3.1/src/Hosting/Hosting/src/Host.cs#L99-L103)。

若要记录低于 <xref:Microsoft.Extensions.Logging.LogLevel.Warning?displayProperty=nameWithType> 的事件，请显式设置日志级别。 以下示例将事件日志的默认日志级别设置为 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>：

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)可以传入 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>。 如果为 `null` 或未指定，则使用以下默认设置：

* `LogName`：“Application”
* `SourceName`：“.NET Runtime”
* `MachineName`：使用本地计算机名称。

以下代码将 `SourceName` 从默认值 `".NET Runtime"` 更改为 `MyLogs`：

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippetEventLog)]

### <a name="azure-app-service"></a>Azure 应用服务

[Microsoft.Extensions.Logging.AzureAppServices](https://www.nuget.org/packages/Microsoft.Extensions.Logging.AzureAppServices) 提供程序包将日志写入 Azure App Service 应用的文件系统，以及 Azure 存储帐户中的 [blob 存储](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#what-is-blob-storage)。

共享框架中不包括该提供程序包。 若要使用提供程序，请将提供程序包添加到项目。

要配置提供程序设置，请使用 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureFileLoggerOptions> 和 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureBlobLoggerOptions>，如以下示例所示：

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_AzLogOptions)]

部署到 Azure 应用服务时，应用使用 Azure 门户的“应用服务”页面的[应用服务日志](/azure/app-service/web-sites-enable-diagnostic-log/#enable-application-logging-windows)部分中的设置。 更新以下设置后，更改立即生效，无需重启或重新部署应用。

* 应用程序日志记录(Filesystem)
* 应用程序日志记录(Blob)

日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt   。 默认文件大小上限为 10 MB，默认最大保留文件数为 2。 默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt  。

仅当项目在 Azure 环境中运行时，此提供程序才记录日志。

#### <a name="azure-log-streaming"></a>Azure 日志流式处理

Azure 日志流式处理支持从以下位置实时查看日志活动：

* 应用服务器
* Web 服务器
* 请求跟踪失败

要配置 Azure 日志流式处理，请执行以下操作：

* 从应用的门户页导航到“应用服务日志”页。
* 将“应用程序日志记录(Filesystem)”设置为“开”   。
* 选择日志级别  。 此设置仅适用于 Azure 日志流式处理。

导航到“日志流”页面以查看日志。 记录的消息使用 `ILogger` 接口进行记录。

### <a name="azure-application-insights"></a>Azure Application Insights

[Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights) 提供程序包将日志写入 [Azure Application Insights](/azure/azure-monitor/app/cloudservices)。 Application Insights 是一项服务，可监视 Web 应用并提供用于查询和分析遥测数据的工具。 如果使用此提供程序，则可以使用 Application Insights 工具来查询和分析日志。

日志记录提供程序作为 [Microsoft.ApplicationInsights.AspNetCore](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore)（这是提供 ASP.NET Core 的所有可用遥测的包）的依赖项包括在内。 如果使用此包，则无需安装提供程序包。

[Microsoft.ApplicationInsights.Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) 包适用于 ASP.NET 4.x，而不适用于 ASP.NET Core。

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
* [PLogger](https://www.nuget.org/packages/InvertedSoftware.PLogger.Core/)（[GitHub 存储库](https://github.com/invertedsoftware/InvertedSoftware.PLogger.Core)）
* [Sentry](https://sentry.io/welcome/)（[GitHub 存储库](https://github.com/getsentry/sentry-dotnet)）
* [Serilog](https://serilog.net/)（[GitHub 存储库](https://github.com/serilog/serilog-aspnetcore)）
* [Stackdriver](https://cloud.google.com/dotnet/docs/stackdriver#logging)（[Github 存储库](https://github.com/googleapis/google-cloud-dotnet)）

某些第三方框架可以执行[语义日志记录（又称结构化日志记录）](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging)。

使用第三方框架类似于使用以下内置提供程序之一：

1. 将 NuGet 包添加到你的项目。
1. 调用日志记录框架提供的 `ILoggerFactory` 扩展方法。

有关详细信息，请参阅各提供程序的相关文档。 Microsoft 不支持第三方日志记录提供程序。

<a name="nhca"></a>

## <a name="non-host-console-app"></a>非托管控制台应用

要通过示例了解如何在非 Web 控制台应用中使用一般主机，请参阅[后台任务示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/host/hosted-services/samples) 的 Program.cs 文件 (<xref:fundamentals/host/hosted-services>)  。

对于没有通用主机的应用，日志记录代码在[添加提供程序](#add-providers)和[创建记录器](#create-logs)的方式上有所不同。 

### <a name="logging-providers"></a>日志记录提供程序

在非主机控制台应用中，在创建 `LoggerFactory` 时调用提供程序的 `Add{provider name}` 扩展方法：

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=11-12)]

### <a name="create-logs"></a>创建日志

若要创建日志，请使用 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象。 使用 `LoggerFactory` 创建一个 `ILogger`。

以下示例创建类别为 `LoggingConsoleApp.Program` 的记录器。

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=14)]

在以下 ASP.NET CORE 示例中，记录器用于创建级别为 `Information` 的日志。 日志“级别”代表所记录事件的严重程度。

[!code-csharp[](index/samples/3.x/LoggingConsoleApp/Program.cs?name=snippet_LoggerFactory&highlight=15)]

本文档中详细介绍了[级别](#log-level)和[类别](#log-category)。

<a name="lhc"></a>

## <a name="log-during-host-construction"></a>主机构造过程中的日志

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

<a name="csdi"></a>

## <a name="configure-a-service-that-depends-on-ilogger"></a>配置依赖于 ILogger 的服务

由于为 Web 主机创建了单独的 DI 容器，所以在 ASP.NET Core 的早期版本中，构造函数将记录器注入到 `Startup` 工作。 若要了解为何仅为通用主机创建一个容器，请参阅[重大更改公告](https://github.com/aspnet/Announcements/issues/353)。

若要配置依赖于 `ILogger<T>` 的服务，请使用构造函数注入或提供工厂方法。 只有在没有其他选择的情况下，才建议使用工厂方法。 例如，假设某个服务需要由 DI 提供的 `ILogger<T>` 实例：

[!code-csharp[](index/samples/3.x/TodoApiSample/Startup2.cs?name=snippet_ConfigureServices&highlight=6-10)]

前面突出显示的代码是 [Func](/dotnet/api/system.func-2)，该代码在 DI 容器第一次需要构造 `MyService` 实例时运行。 可以用这种方式访问任何已注册的服务。

<a name="clms"></a>

## <a name="create-logs-in-main"></a>在 Main 中创建日志

以下代码通过在构建主机之后从 DI 获取 `ILogger` 实例来登录 `Main`：

[!code-csharp[](index/samples/3.x/TodoApiDTO/Program.cs?name=snippet_LogProgram)]

### <a name="create-logs-in-startup"></a>启动时创建日志

以下代码在 `Startup.Configure` 中写入日志：

[!code-csharp[](index/samples/3.x/TodoApiDTO/Startup.cs?name=snippet_Configure)]

不支持在 `Startup.ConfigureServices` 方法中完成 DI 容器设置之前就写入日志：

* 不支持将记录器注入到 `Startup` 构造函数中。
* 不支持将记录器注入到 `Startup.ConfigureServices` 方法签名中

这一限制的原因是，日志记录依赖于 DI 和配置，而配置又依赖于 DI。 在完成 `ConfigureServices` 之前，不会设置 DI 容器。

有关配置依赖于 `ILogger<T>` 的服务或为什么在早期版本中可以使用构造函数将记录器注入 `Startup` 的信息，请参阅[配置依赖 ILogger 的服务](#csdi)

### <a name="no-asynchronous-logger-methods"></a>没有异步记录器方法

日志记录应该会很快，不值得牺牲性能来使用异步代码。 如果日志记录数据存储很慢，请不要直接写入它。 考虑先将日志消息写入快速存储，然后再将其移至慢速存储。 例如，登录到 SQL Server 时，请勿直接使用 `Log` 方法登录，因为 `Log` 方法是同步的。 相反，你会将日志消息同步添加到内存中的队列，并让后台辅助线程从队列中拉出消息，以完成将数据推送到 SQL Server 的异步工作。 有关详细信息，请参阅[此](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 问题。

<a name="clib"></a>

## <a name="change-log-levels-in-a-running-app"></a>更改正在运行的应用中的日志级别

不可使用日志记录 API 在应用运行时更改日志记录。 但是，一些配置提供程序可重新加载配置，这将对日志记录配置立即产生影响。 例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)默认情况下重载日志记录配置。 如果在应用运行时在代码中更改了配置，则该应用可调用 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) 来更新应用的日志记录配置。

## <a name="ilogger-and-iloggerfactory"></a>ILogger 和 ILoggerFactory

<xref:Microsoft.Extensions.Logging.ILogger%601> 和 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 接口和实现都包含在 .NET Core SDK 中。 它们还可以通过以下 NuGet 包获得：  

* 这些接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中。
* 默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。

<!-- review. Why would you want to hard code filtering rules in code? -->
<a name="fric"></a>

## <a name="apply-log-filter-rules-in-code"></a>在代码中应用日志筛选器规则

设置日志筛选器规则的首选方法是使用[配置](xref:fundamentals/configuration/index)。

下面的示例演示了如何在代码中注册筛选规则：

[!code-csharp[](index/samples/3.x/MyMain/Program.cs?name=snippet_FilterInCode)]

`logging.AddFilter("System", LogLevel.Debug)` 指定 `System` 类别和日志级别 `Debug`。 筛选器将应用于所有提供程序，因为未配置特定的提供程序。

`AddFilter<DebugLoggerProvider>("Microsoft", LogLevel.Information)` 指定以下项：

* `Debug` 日志记录提供程序。
* 日志级别 `Information` 及更高级别。
* 以 `"Microsoft"` 开头的所有类别。

## <a name="create-a-custom-logger"></a>创建自定义记录器

若要添加自定义记录器，请添加包含 <xref:Microsoft.Extensions.Logging.ILoggerFactory> 的 <xref:Microsoft.Extensions.Logging.ILoggerProvider>：

```csharp
public void Configure(
    IApplicationBuilder app,
    IWebHostEnvironment env,
    ILoggerFactory loggerFactory)
{
    loggerFactory.AddProvider(new CustomLoggerProvider(new CustomLoggerConfiguration()));
```

`ILoggerProvider` 创建一个或多个 `ILogger` 实例。 框架使用 `ILogger` 实例记录信息。

### <a name="sample-custom-logger-configuration"></a>示例自定义记录器配置

示例：

* 设计为非常基本的示例，可通过事件 ID 和日志级别设置日志控制台的颜色。 记录器通常不会随事件 ID 改变，也不特定于日志级别。
* 使用以下配置类型为每个日志级别和事件 ID 创建不同的颜色控制台条目：

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerConfiguration.cs?name=snippet)]

前面的代码将默认级别设置为 `Warning`，并将颜色设置为 `Yellow`。 如果 `EventId` 设置为 0，我们将记录所有事件。

### <a name="create-the-custom-logger"></a>创建自定义记录器

`ILogger` 实现类别名称通常是日志记录源。 例如，创建记录器的类型：

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLogger.cs?name=snippet)]

前面的代码：

* 为每个类别名称创建一个记录器实例。
* 在 `IsEnabled` 中检查 `logLevel == _config.LogLevel`，因此每个 `logLevel` 都有一个唯一的记录器。 通常，还应为所有更高的日志级别启用记录器：

```csharp
public bool IsEnabled(LogLevel logLevel)
{
    return logLevel >= _config.LogLevel;
}
```

### <a name="create-the-custom-loggerprovider"></a>创建自定义 LoggerProvider

`LoggerProvider` 是创建记录器实例的类。 也许不需要为每个类别创建记录器实例，但这对于某些记录器（例如 NLog 或 log4net）是需要的。 这样，你还可以按需为每个类别选择不同的日志记录输出目标：

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerProvider.cs?name=snippet)]

在前面的代码中，<xref:Microsoft.Build.Logging.LoggerDescription.CreateLogger*> 为每个类别名称创建 `ColorConsoleLogger` 的单个实例并将其存储在 [`ConcurrentDictionary<TKey,TValue>`](/dotnet/api/system.collections.concurrent.concurrentdictionary-2) 中；

### <a name="usage-and-registration-of-the-custom-logger"></a>自定义记录器的使用和注册

在 `Startup.Configure` 中注册记录器：

[!code-csharp[](index/samples/3.x/CustomLogger/Startup.cs?name=snippet)]

对于前面的代码，为 `ILoggerFactory` 提供至少一个扩展方法：

[!code-csharp[](index/samples/3.x/CustomLogger/ColorConsoleLogger/ColorConsoleLoggerExtensions.cs?name=snippet)]

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/logging/loggermessage>
* 记录错误应在 [github.com/dotnet/runtime/](https://github.com/dotnet/runtime/issues) 存储库中创建。
* <xref:blazor/fundamentals/logging>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Steve Smith](https://ardalis.com/)

.NET Core 支持适用于各种内置和第三方日志记录提供程序的日志记录 API。 本文介绍了如何将日志记录 API 与内置提供程序一起使用。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/logging/index/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="add-providers"></a>添加提供程序

日志记录提供程序会显示或存储日志。 例如，控制台提供程序在控制台上显示日志，Azure Application Insights 提供程序会将这些日志存储在 Azure Application Insights 中。 可通过添加多个提供程序将日志发送到多个目标。

要添加提供程序，请在 Program.cs 中调用提供程序的 `Add{provider name}` 扩展方法  ：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_ExpandDefault&highlight=18-20)]

前面的代码需要引用 `Microsoft.Extensions.Logging` 和 `Microsoft.Extensions.Configuration`。

默认项目模板调用 <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder%2A>，该操作将添加以下日志记录提供程序：

* 控制台
* 调试
* EventSource（从 ASP.NET Core 2.2 开始）

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_TemplateCode&highlight=7)]

如果使用 `CreateDefaultBuilder`，则可自行选择提供程序来替换默认提供程序。 调用 <xref:Microsoft.Extensions.Logging.LoggingBuilderExtensions.ClearProviders%2A>，然后添加所需的提供程序。

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_LogFromMain&highlight=18-22)]

详细了解[内置日志记录提供程序](#built-in-logging-providers)，以及本文稍后部分介绍的[第三方日志记录提供程序](#third-party-logging-providers)。

## <a name="create-logs"></a>创建日志

若要创建日志，请使用 <xref:Microsoft.Extensions.Logging.ILogger%601> 对象。 在 Web 应用或托管服务中，由依赖关系注入 (DI) 获取 `ILogger`。 在非主机控制台应用中，使用 `LoggerFactory` 来创建 `ILogger`。

下面的 ASP.NET Core 示例创建了一个以 `TodoApiSample.Pages.AboutModel` 为类别的记录器。 日志“类别”是与每个日志关联的字符串  。 DI 提供的 `ILogger<T>` 实例创建日志，该日志以类型 `T` 的完全限定名称为类别。 

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_LoggerDI&highlight=3,5,7)]

在下面的 ASP.NET Core 和控制台应用示例中，记录器用于创建以 `Information` 为级别的日志。 日志“级别”代表所记录事件的严重程度  。

[!code-csharp[](index/samples/2.x/TodoApiSample/Pages/About.cshtml.cs?name=snippet_CallLogMethods&highlight=4)]

本文稍后部分将更详细地介绍[级别](#log-level)和[类别](#log-category)。

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

### <a name="no-asynchronous-logger-methods"></a>没有异步记录器方法

日志记录应该会很快，不值得牺牲性能来使用异步代码。 如果你的日志数据存储很慢，请不要直接写入它。 首先考虑将日志消息写入快速存储，稍后再将其变为慢速存储。 例如，如果你要记录到 SQL Server，你可能不想直接在 `Log` 方法中记录，因为 `Log` 方法是同步的。 相反，你会将日志消息同步添加到内存中的队列，并让后台辅助线程从队列中拉出消息，以完成将数据推送到 SQL Server 的异步工作。 有关详细信息，请参阅[此](https://github.com/dotnet/AspNetCore.Docs/issues/11801) GitHub 问题。

## <a name="configuration"></a>Configuration

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

`Logging` 下的 `LogLevel` 属性指定了用于记录所选类别的最低[级别](#log-level)。 在本例中，`System` 和 `Microsoft` 类别在 `Information` 级别记录，其他均在 `Debug` 级别记录。

`Logging` 下的其他属性均指定了日志记录提供程序。 本示例针对控制台提供程序。 如果提供程序支持[日志作用域](#log-scopes)，则 `IncludeScopes` 将指示是否启用这些域。 提供程序属性（例如本例的 `Console`）也可指定 `LogLevel` 属性。 提供程序下的 `LogLevel` 指定了该提供程序记录的级别。

如果在 `Logging.{providername}.LogLevel` 中指定了级别，则这些级别将重写 `Logging.LogLevel` 中设置的所有内容。 例如，考虑以下 JSON：

[!code-json[](index/samples/3.x/TodoApiDTO/appsettings.MSFT.json)]

在前面的 JSON 中，`Console` 提供程序设置将替代前面的（默认）日志级别。

不可使用日志记录 API 在应用运行时更改日志记录。 但是，一些配置提供程序可重新加载配置，这将对日志记录配置立即产生影响。 例如，[文件配置提供程序](xref:fundamentals/configuration/index#file-configuration-provider)会默认重新加载日志记录配置，该程序由 `CreateDefaultBuilder` 添加用来读取设置文件。 如果在应用运行时在代码中更改了配置，则该应用可调用 [IConfigurationRoot.Reload](xref:Microsoft.Extensions.Configuration.IConfigurationRoot.Reload*) 来更新应用的日志记录配置。

若要了解如何实现配置提供程序，请参阅 <xref:fundamentals/configuration/index>。

## <a name="sample-logging-output"></a>日志记录输出示例

使用上一部分中显示的示例代码从命令行运行应用时，将在控制台中看到日志。 以下是控制台输出示例：

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

通过向 `http://localhost:5000/api/todo/0` 处的示例应用发出 HTTP Get 请求来生成前述日志。

在 Visual Studio 中运行示例应用时，“调试”窗口中将显示如下日志：

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

本文余下部分将介绍有关日志记录的某些详细信息及选项。

## <a name="nuget-packages"></a>NuGet 包

`ILogger` 和 `ILoggerFactory` 接口位于 [Microsoft.Extensions.Logging.Abstractions](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) 中，其默认实现位于 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 中。

## <a name="log-category"></a>日志类别

创建 `ILogger` 对象后，将为其指定“类别”  。 该类别包含在由此 `ILogger` 实例创建的每条日志消息中。 类别可以是任何字符串，但约定需使用类名，例如“TodoApi.Controllers.TodoController”。

使用 `ILogger<T>` 获取一个 `ILogger` 实例，该实例使用 `T` 的完全限定类型名称作为类别：

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LoggerDI&highlight=7)]

要显式指定类别，请调用 `ILoggerFactory.CreateLogger`：

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CreateLogger&highlight=7,10)]

`ILogger<T>` 相当于使用 `T` 的完全限定类型名称来调用 `CreateLogger`。

## <a name="log-level"></a>日志级别

每个日志都指定了一个 <xref:Microsoft.Extensions.Logging.LogLevel> 值。 日志级别指示严重性或重要程度。 例如，可在方法正常结束时写入 `Information` 日志，在方法返回“404 找不到”状态代码时写入 `Warning` 日志  。

下面的代码会创建 `Information` 和 `Warning` 日志：

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

在前面的代码中，`MyLogEvents.GetItem` 和 `MyLogEvents.GetItemNotFound` 参数是[日志事件 ID](#log-event-id)。 第二个参数是消息模板，其中的占位符用于填写剩余方法形参提供的实参值。 本文的[日志消息模板部分](#lmt)将介绍方法参数。

在方法名称中包含级别的日志方法（例如 `LogInformation` 和 `LogWarning`）是 [ILogger 的扩展方法](xref:Microsoft.Extensions.Logging.LoggerExtensions)。 这些方法会调用可接受 `LogLevel` 参数的 `Log` 方法。 可直接调用 `Log` 方法而不调用其中某个扩展方法，但语法相对复杂。 有关详细信息，请参阅 <xref:Microsoft.Extensions.Logging.ILogger> 和[记录器扩展源代码](https://github.com/dotnet/extensions/blob/release/2.2/src/Logging/Logging.Abstractions/src/LoggerExtensions.cs)。

ASP.NET Core 定义了以下日志级别（按严重性从低到高排列）。

* 跟踪 = 0

  有关通常仅用于调试的信息。 这些消息可能包含敏感应用程序数据，因此不得在生产环境中启用它们。 默认情况下禁用。

* 调试 = 1

  有关在开发和调试中可能有用的信息。 示例：`Entering method Configure with flag set to true.` 由于日志数量过多，因此仅当执行故障排除时，才在生产中启用 `Debug` 级别日志。

* 信息 = 2

  用于跟踪应用的常规流。 这些日志通常有长期价值。 示例：`Request received for path /api/todo`

* 警告 = 3

  表示应用流中的异常或意外事件。 可能包括不会中断应用运行但仍需调查的错误或其他条件。 `Warning` 日志级别常用于已处理的异常。 示例：`FileNotFoundException for file quotes.txt.`

* 错误 = 4

  表示无法处理的错误和异常。 这些消息指示的是当前活动或操作（例如当前 HTTP 请求）中的失败，而不是整个应用中的失败。 日志消息示例：`Cannot insert record due to duplicate key violation.`

* 严重 = 5

  需要立即关注的失败。 例如数据丢失、磁盘空间不足。

使用日志级别控制写入到特定存储介质或显示窗口的日志输出量。 例如：

* 生产中：
  * 如果通过 `Information` 级别在 `Trace` 处记录，则会生成大量详细的日志消息。 为控制成本且不超过数据存储限制，请通过 `Information` 级别消息将 `Trace` 记录到容量大、成本低的数据存储中。
  * 如果通过 `Critical` 级别在 `Warning` 处记录，通常生成的日志消息更少且更小。 因此，成本和存储限制通常不是问题，而这使得在选择数据信息时更为灵活。
* 在开发过程中：
  * 通过 `Critical` 消息将 `Warning` 记录到控制台。
  * 在疑难解答时通过 `Information` 消息添加 `Trace`。

本文稍后的[日志筛选](#log-filtering)部分介绍如何控制提供程序处理的日志级别。

ASP.NET Core 为框架事件写入日志。 本文前面部分提供的日志示例排除了低于 `Information` 级别的日志，因此未创建 `Debug` 或 `Trace` 级别日志。 以下示例介绍了通过运行配置为显示 `Debug` 日志的示例应用而生成的控制台日志：

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

## <a name="log-event-id"></a>日志事件 ID

每个日志都可指定一个事件 ID  。 该示例应用通过使用本地定义的 `LoggingEvents` 类来执行此操作：

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

[!code-csharp[](index/samples/2.x/TodoApiSample/Core/LoggingEvents.cs?name=snippet_LoggingEvents)]

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

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_CallLogMethods&highlight=3,7)]

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

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_LogException&highlight=3)]

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

项目模板代码调用 `CreateDefaultBuilder` 来为 Console、Debug 和 EventSource（ASP.NET Core 2.2 或更高版本）提供程序设置日志记录。 正如[本文前面部分](#configuration)所述，`CreateDefaultBuilder` 方法设置日志记录以在 `Logging` 部分查找配置。

配置数据按提供程序和类别指定最低日志级别，如下方示例所示：

[!code-json[](index/samples/2.x/TodoApiSample/appsettings.json)]

此 JSON 将创建 6 条筛选规则：1 条用于调试提供程序， 4 条用于控制台提供程序， 1 条用于所有提供程序。 创建 `ILogger` 对象时，为每个提供程序选择一个规则。

### <a name="filter-rules-in-code"></a>代码中的筛选规则

下面的示例演示了如何在代码中注册筛选规则：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterInCode&highlight=4-5)]

第二个 `AddFilter` 使用类型名称来指定调试提供程序。 第一个 `AddFilter` 应用于全部提供程序，因为它未指定提供程序类型。

### <a name="how-filtering-rules-are-applied"></a>如何应用筛选规则

先前示例中显示的配置数据和 `AddFilter` 代码会创建下表所示的规则。 前六条由配置示例创建，后两条由代码示例创建。

| 数字 | 提供程序      | 类别的开头为...          | 最低日志级别 |
| :----: | ------------- | --------------------------------------- | ----------------- |
| 1      | 调试         | 全部类别                          | 信息       |
| 2      | 控制台       | Microsoft.AspNetCore.Mvc.Razor.Internal | 警告           |
| 3      | 控制台       | Microsoft.AspNetCore.Mvc.Razor.Razor    | 调试             |
| 4      | 控制台       | Microsoft.AspNetCore.Mvc.Razor          | 错误             |
| 5      | 控制台       | 全部类别                          | 信息       |
| 6      | 全部提供程序 | 全部类别                          | 调试             |
| 7      | 全部提供程序 | System                                  | 调试             |
| 8      | 调试         | Microsoft                               | 跟踪             |

创建 `ILogger` 对象时，`ILoggerFactory` 对象将根据提供程序选择一条规则，将其应用于该记录器。 将按所选规则筛选 `ILogger` 实例写入的所有消息。 从可用规则中为每个提供程序和类别对选择最具体的规则。

在为给定的类别创建 `ILogger` 时，以下算法将用于每个提供程序：

* 选择匹配提供程序或其别名的所有规则。 如果找不到任何匹配项，则选择提供程序为空的所有规则。
* 根据上一步的结果，选择具有最长匹配类别前缀的规则。 如果找不到任何匹配项，则选择未指定类别的所有规则。
* 如果选择了多条规则，则采用最后一条  。
* 如果未选择任何规则，则使用 `MinimumLevel`。

使用前面的规则列表，假设你为类别“Microsoft.AspNetCore.Mvc.Razor.RazorViewEngine”创建 `ILogger` 对象：

* 对于调试提供程序，规则 1、6 和 8 适用。 规则 8 最为具体，因此选择了它。
* 对于控制台提供程序，符合的有规则 3、规则 4、规则 5 和规则 6。 规则 3 最为具体。

生成的 `ILogger` 实例将 `Trace` 级别及更高级别的日志发送到调试提供程序。 `Debug` 级别及更高级别的日志会发送到控制台提供程序。

### <a name="provider-aliases"></a>提供程序别名

每个提供程序都定义了一个别名；可在配置中使用该别名来代替完全限定的类型名称  。  对于内置提供程序，请使用以下别名：

* 控制台
* 调试
* EventSource
* EventLog
* TraceSource
* AzureAppServicesFile
* AzureAppServicesBlob
* ApplicationInsights

### <a name="default-minimum-level"></a>默认最低级别

仅当配置或代码中的规则对给定提供程序和类别都不适用时，最低级别设置才会生效。 下面的示例演示如何设置最低级别：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_MinLevel&highlight=3)]

如果没有明确设置最低级别，则默认值为 `Information`，它表示 `Trace` 和 `Debug` 日志将被忽略。

### <a name="filter-functions"></a>筛选器函数

对配置或代码没有向其分配规则的所有提供程序和类别调用筛选器函数。 函数中的代码可访问提供程序类型、类别和日志级别。 例如：

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_FilterFunction&highlight=5-13)]

## <a name="system-categories-and-levels"></a>系统类别和级别

下面是 ASP.NET Core 和 Entity Framework Core 使用的一些类别，备注中说明了可从这些类别获取的具体日志：

| 类别                            | 说明 |
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

范围是由 <xref:Microsoft.Extensions.Logging.ILogger.BeginScope*> 方法返回的 `IDisposable` 类型，持续至释放为止。 要使用作用域，请在 `using` 块中包装记录器调用：

[!code-csharp[](index/samples/2.x/TodoApiSample/Controllers/TodoController.cs?name=snippet_Scopes&highlight=4-5,13)]

下列代码为控制台提供程序启用作用域：

Program.cs  :

[!code-csharp[](index/samples/2.x/TodoApiSample/Program.cs?name=snippet_Scopes&highlight=4)]

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

使用 [PerfView 实用工具](https://github.com/Microsoft/perfview)收集和查看日志。 虽然其他工具也可以查看 ETW 日志，但在处理由 ASP.NET Core 发出的 ETW 事件时，使用 PerfView 能获得最佳体验。

要将 PerfView 配置为收集此提供程序记录的事件，请向 Additional Providers 列表添加字符串 `*Microsoft-Extensions-Logging` 。 （请勿遗漏字符串起始处的星号。）

![其他 Perfview 提供程序](index/_static/perfview-additional-providers.png)

### <a name="windows-eventlog-provider"></a>Windows EventLog 提供程序

[Microsoft.Extensions.Logging.EventLog](https://www.nuget.org/packages/Microsoft.Extensions.Logging.EventLog) 提供程序包向 Windows 事件日志发送日志输出。

```csharp
logging.AddEventLog();
```

[AddEventLog 重载](xref:Microsoft.Extensions.Logging.EventLoggerFactoryExtensions)允许传入 <xref:Microsoft.Extensions.Logging.EventLog.EventLogSettings>。 如果为 `null` 或未指定，则使用以下默认设置：

* `LogName`：“Application”
* `SourceName`：“.NET Runtime”
* `MachineName`：使用本地计算机名称。

为[警告等级和更高等级](#log-level)记录事件。 以下示例将事件日志的默认日志级别设置为 <xref:Microsoft.Extensions.Logging.LogLevel.Information?displayProperty=nameWithType>：

```json
"Logging": {
  "EventLog": {
    "LogLevel": {
      "Default": "Information"
    }
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

[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中不包括此提供程序包。 如果面向 .NET Framework 或引用 `Microsoft.AspNetCore.App` 元包，请向项目添加提供程序包。 

<xref:Microsoft.Extensions.Logging.AzureAppServicesLoggerFactoryExtensions.AddAzureWebAppDiagnostics*> 重载允许传入 <xref:Microsoft.Extensions.Logging.AzureAppServices.AzureAppServicesDiagnosticsSettings>。 设置对象可以覆盖默认设置，例如日志记录输出模板、blob 名称和文件大小限制。 （“输出模板”是一个消息模板，除了通过 `ILogger` 方法调用提供的内容之外，还可将其应用于所有日志。  ）

在部署应用服务应用时，应用程序将采用 Azure 门户中“应用服务”页面下的[应用服务日志](/azure/app-service/web-sites-enable-diagnostic-log/#enablediag)部分的设置  。 更新以下设置后，更改立即生效，无需重启或重新部署应用。

* 应用程序日志记录(Filesystem)
* 应用程序日志记录(Blob)

日志文件的默认位置是 D:\\home\\LogFiles\\Application 文件夹，默认文件名为 diagnostics-yyyymmdd.txt   。 默认文件大小上限为 10 MB，默认最大保留文件数为 2。 默认 blob 名为 {app-name}{timestamp}/yyyy/mm/dd/hh/{guid}-applicationLog.txt  。

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

::: moniker-end
