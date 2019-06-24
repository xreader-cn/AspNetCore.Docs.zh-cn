---
title: 日志记录和诊断在 ASP.NET Core SignalR
author: anurse
description: 了解如何从 ASP.NET Core SignalR 应用程序中收集诊断。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 06/19/2019
uid: signalr/diagnostics
ms.openlocfilehash: 69dbd057b3dcadeb3ca5d94ede1234530fb447db
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313698"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a>日志记录和诊断在 ASP.NET Core SignalR

通过[Andrew Stanton-nurse](https://twitter.com/anurse)

本文提供了用于诊断收集从您的 ASP.NET Core SignalR 应用程序来帮助解决问题的指南。

## <a name="server-side-logging"></a>服务器端日志记录

> [!WARNING]
> 服务器端日志可能包含您的应用程序中的敏感信息。 **永远不会**原始日志从生产应用程序发布到 GitHub 等公共论坛。

由于 SignalR 是 ASP.NET Core 的组成部分，它使用 ASP.NET Core 日志记录系统。 在默认配置中，SignalR 日志很少的信息，但这可以配置。 在查看文档[ASP.NET Core 日志记录](xref:fundamentals/logging/index#configuration)有关配置 ASP.NET Core 日志记录详细信息。

SignalR 使用两个记录器类别：

* `Microsoft.AspNetCore.SignalR` &ndash; 对于与中心协议相关的日志，激活中心，调用方法和其他与中心相关的活动。
* `Microsoft.AspNetCore.Http.Connections` &ndash; 日志与传输如 WebSockets、 长轮询和服务器发送事件和低级别的 SignalR 基础结构。

若要启用详细的日志来自 SignalR，配置这两个到前面的前缀`Debug`级中您*appsettings.json*文件添加到以下各项`LogLevel`子节中`Logging`:

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

你还可以在代码中配置此应用`CreateWebHostBuilder`方法：

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

如果不使用基于 JSON 的配置，配置系统中设置以下配置值：

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

查看配置系统，以确定如何指定嵌套的配置值的文档。 例如，当使用环境变量，两个`_`而不是使用字符`:`(例如， `Logging__LogLevel__Microsoft.AspNetCore.SignalR`)。

我们建议使用`Debug`级别收集更多详细诊断您的应用程序时。 `Trace`级别生成非常低级的诊断和很少需要诊断应用程序中的问题。

## <a name="access-server-side-logs"></a>访问服务器端日志

访问服务器端日志的方式取决于运行你的环境。

### <a name="as-a-console-app-outside-iis"></a>为 IIS 外部控制台应用

如果您在控制台应用中，运行[控制台记录器](xref:fundamentals/logging/index#console-provider)应默认情况下启用。 SignalR 日志显示在控制台中。

### <a name="within-iis-express-from-visual-studio"></a>从 Visual Studio 的 IIS Express 中

Visual Studio 将显示中的日志输出**输出**窗口。 选择**ASP.NET Core Web 服务器**下拉列表选项。

### <a name="azure-app-service"></a>Azure 应用服务

启用**应用程序日志记录 （文件系统）** 选项**诊断日志**Azure 应用服务门户的一部分，并配置**级别**到`Verbose`。 日志应可从**日志流式处理**服务和应用服务在文件系统上的日志中。 有关详细信息，请参阅[Azure 日志流式处理](xref:fundamentals/logging/index#azure-log-streaming)。

### <a name="other-environments"></a>其他环境

如果应用程序部署到另一个环境 （例如，Docker、 Kubernetes 或 Windows 服务），请参阅<xref:fundamentals/logging/index>有关如何配置日志记录提供程序适用于环境的详细信息。

## <a name="javascript-client-logging"></a>JavaScript 客户端日志记录

> [!WARNING]
> 客户端的日志可能包含您的应用程序中的敏感信息。 **永远不会**原始日志从生产应用程序发布到 GitHub 等公共论坛。

在使用 JavaScript 客户端时，可以配置使用的日志记录选项`configureLogging`方法`HubConnectionBuilder`:

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

若要禁用完全日志记录，请指定`signalR.LogLevel.None`在`configureLogging`方法。

下表显示可用的日志级别向 JavaScript 客户端。 将日志级别设置为下列值之一，则在该级别和其上表中的所有级别的记录。

| 级别 | 描述 |
| ----- | ----------- |
| `None` | 未不记录任何消息。 |
| `Critical` | 表示在整个应用程序时失败的消息。 |
| `Error` | 表示在当前操作失败的消息。 |
| `Warning` | 表示非致命性问题的消息。 |
| `Information` | 信息性消息。 |
| `Debug` | 用于调试的诊断消息。 |
| `Trace` | 用于诊断特定问题非常详细的诊断消息。 |

配置详细级别后，日志将写入到浏览器控制台 （或在 NodeJS 应用中的标准输出） 中。

如果你想要将日志发送到自定义日志记录系统，则可以提供 JavaScript 对象实现`ILogger`接口。 需要实现的唯一方法是`log`、 接受事件的级别和与事件关联的消息。 例如：

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a>.NET 客户端日志记录

> [!WARNING]
> 客户端的日志可能包含您的应用程序中的敏感信息。 **永远不会**原始日志从生产应用程序发布到 GitHub 等公共论坛。

若要从.NET 客户端获取日志，可以使用`ConfigureLogging`方法`HubConnectionBuilder`。 此工作方式相同`ConfigureLogging`方法`WebHostBuilder`和`HostBuilder`。 在 ASP.NET Core 中，可以配置您使用的相同日志记录提供程序。 但是，您必须手动安装并启用单个日志记录提供程序 NuGet 包。

### <a name="console-logging"></a>控制台日志记录

若要启用控制台日志记录，添加[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console)包。 然后，使用`AddConsole`方法来配置控制台记录器：

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a>调试输出窗口日志记录

此外可以配置日志以转到**输出**Visual Studio 窗口中的。 安装[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)包并使用`AddDebug`方法：

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a>其他日志记录提供程序

SignalR 支持其他日志记录提供程序，例如 Serilog、 Seq、 NLog 或任何其他日志记录系统以与集成`Microsoft.Extensions.Logging`。 如果您的日志记录系统提供了`ILoggerProvider`，可以将它注册`AddProvider`:

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a>控制详细级别

如果应用程序中，要从其他位置进行登录，更改到的默认级别`Debug`可能太详细。 筛选器可用于配置 SignalR 日志的日志记录级别。 这可以在代码中，在很大程度是相同的服务器上：

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a>网络跟踪

> [!WARNING]
> 网络跟踪包含每个应用发送的消息的全部内容。 **永远不会**原始网络跟踪从生产应用程序发布到 GitHub 等公共论坛。

如果遇到问题，网络跟踪有时可以提供大量有用的信息。 这是您要在问题跟踪程序上提出问题的情况特别有用。

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a>收集网络跟踪使用 Fiddler （首选选择）

此方法适用于所有应用。

Fiddler 是非常强大的工具，用于收集 HTTP 跟踪。 安装从[telerik.com/fiddler](https://www.telerik.com/fiddler)，启动它，然后运行你的应用并重现此问题。 Fiddler 是适用于 Windows，并且没有适用于 macOS 和 Linux 的 beta 版本。

如果使用 HTTPS 连接，有一些额外步骤才能确保 Fiddler 可以解密 HTTPS 流量。 有关更多详细信息，请参阅[Fiddler 文档](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)。

一旦已收集跟踪，可以通过选择导出跟踪**文件** > **保存** > **所有会话**从菜单栏中。

![Fiddler 从导出的所有会话](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>收集网络跟踪与 tcpdump （macOS 和仅限 Linux）

此方法适用于所有应用。

你可以收集通过运行以下命令从命令行界面使用 tcpdump 原始 TCP 跟踪。 可能需要进行`root`前缀的命令或`sudo`如果出现权限错误：

```console
tcpdump -i [interface] -w trace.pcap
```

替换为`[interface]`与你想要在捕获的网络接口。 通常，这是类似`/dev/eth0`（适用于在标准的以太网接口） 或`/dev/lo0`（适用于 localhost 流量）。 有关详细信息，请参阅`tcpdump`在主机系统上的手册页。

## <a name="collect-a-network-trace-in-the-browser"></a>收集网络跟踪在浏览器中

此方法仅适用于基于浏览器的应用。

大多数浏览器开发人员工具具有可以捕获浏览器和服务器之间的网络活动的"网络"选项卡。 但是，这些跟踪将不包括 WebSocket 和服务器发送事件消息。 如果使用这些传输，使用 Fiddler 或 TcpDump （如下所述） 之类的工具是更好的方法。

### <a name="microsoft-edge-and-internet-explorer"></a>Microsoft Edge 和 Internet 资源管理器

（说明是相同的 Edge 和 Internet Explorer）

1. 按 F12 打开开发人员工具
2. 单击网络选项卡
3. （如果需要），请刷新页面并再现该问题
4. 单击工具栏将跟踪导出为"HAR"文件中的保存图标：

![保存图标上 Microsoft Edge 开发人员工具网络选项卡](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a>Google Chrome

1. 按 F12 打开开发人员工具
2. 单击网络选项卡
3. （如果需要），请刷新页面并再现该问题
4. 在列表中的请求的任意位置右键单击并选择"另存为 HAR 内容":

![Google Chrome 开发工具网络选项卡中的"另存为包含内容的 HAR"选项](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. 按 F12 打开开发人员工具
2. 单击网络选项卡
3. （如果需要），请刷新页面并再现该问题
4. 在列表中的请求的任意位置右键单击并选择"保存所有为 HAR"

![Mozilla Firefox 适用于开发人员工具网络选项卡中"保存全部为 HAR"选项](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a>将诊断文件附加到 GitHub 问题

您可以通过重命名它们以便它们具有诊断文件附加到 GitHub 问题`.txt`扩展，然后将拖放到此问题。

> [!NOTE]
> 请不要将日志文件或网络跟踪的内容粘贴到 GitHub 问题。 这些日志和跟踪可能会很大，和 GitHub 通常将其截断。

![拖动到 GitHub 问题的日志文件](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a>其他资源

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
