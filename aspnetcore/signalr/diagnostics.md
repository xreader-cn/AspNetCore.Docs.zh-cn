---
title: 日志记录和诊断在 ASP.NET Core SignalR
author: anurse
description: 了解如何从 ASP.NET Core SignalR 应用程序中收集诊断。
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: signalr
ms.date: 02/27/2019
uid: signalr/diagnostics
ms.openlocfilehash: b6bd21314ed183488999bcff3553e53493537a11
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400914"
---
# <a name="logging-and-diagnostics-in-aspnet-core-signalr"></a><span data-ttu-id="693b0-103">日志记录和诊断在 ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="693b0-103">Logging and diagnostics in ASP.NET Core SignalR</span></span>

<span data-ttu-id="693b0-104">通过[Andrew Stanton-nurse](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="693b0-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="693b0-105">本文提供了用于诊断收集从您的 ASP.NET Core SignalR 应用程序来帮助解决问题的指南。</span><span class="sxs-lookup"><span data-stu-id="693b0-105">This article provides guidance for gathering diagnostics from your ASP.NET Core SignalR app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="693b0-106">服务器端日志记录</span><span class="sxs-lookup"><span data-stu-id="693b0-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="693b0-107">服务器端日志可能包含您的应用程序中的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="693b0-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="693b0-108">**永远不会**原始日志从生产应用程序发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="693b0-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="693b0-109">由于 SignalR 是 ASP.NET Core 的组成部分，它使用 ASP.NET Core 日志记录系统。</span><span class="sxs-lookup"><span data-stu-id="693b0-109">Since SignalR is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="693b0-110">在默认配置中，SignalR 日志很少的信息，但这可以配置。</span><span class="sxs-lookup"><span data-stu-id="693b0-110">In the default configuration, SignalR logs very little information, but this can configured.</span></span> <span data-ttu-id="693b0-111">在查看文档[ASP.NET Core 日志记录](xref:fundamentals/logging/index#configuration)有关配置 ASP.NET Core 日志记录详细信息。</span><span class="sxs-lookup"><span data-stu-id="693b0-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="693b0-112">SignalR 使用两个记录器类别：</span><span class="sxs-lookup"><span data-stu-id="693b0-112">SignalR uses two logger categories:</span></span>

* <span data-ttu-id="693b0-113">`Microsoft.AspNetCore.SignalR` -对于与中心协议相关的日志，激活中心、 调用方法和其他与中心相关的活动。</span><span class="sxs-lookup"><span data-stu-id="693b0-113">`Microsoft.AspNetCore.SignalR` - for logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="693b0-114">`Microsoft.AspNetCore.Http.Connections` -对于与传输如 WebSockets、 长轮询和服务器发送事件和低级别的 SignalR 基础结构相关的日志。</span><span class="sxs-lookup"><span data-stu-id="693b0-114">`Microsoft.AspNetCore.Http.Connections` - for logs related to transports such as WebSockets, Long Polling and Server-Sent Events and low-level SignalR infrastructure.</span></span>

<span data-ttu-id="693b0-115">若要启用详细的日志来自 SignalR，配置这两个到前面的前缀`Debug`级中您`appsettings.json`文件添加到以下各项`LogLevel`子节中`Logging`:</span><span class="sxs-lookup"><span data-stu-id="693b0-115">To enable detailed logs from SignalR, configure both of the preceding prefixes to the `Debug` level in your `appsettings.json` file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[Configuring logging](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="693b0-116">你还可以在代码中配置此应用`CreateWebHostBuilder`方法：</span><span class="sxs-lookup"><span data-stu-id="693b0-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[Configuring logging in code](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="693b0-117">如果不使用基于 JSON 的配置，配置系统中设置以下配置值：</span><span class="sxs-lookup"><span data-stu-id="693b0-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.SignalR` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="693b0-118">查看配置系统，以确定如何指定嵌套的配置值的文档。</span><span class="sxs-lookup"><span data-stu-id="693b0-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="693b0-119">例如，当使用环境变量，两个`_`而不是使用字符`:`(例如： `Logging__LogLevel__Microsoft.AspNetCore.SignalR`)。</span><span class="sxs-lookup"><span data-stu-id="693b0-119">For example, when using environment variables, two `_` characters are used instead of the `:` (such as: `Logging__LogLevel__Microsoft.AspNetCore.SignalR`).</span></span>

<span data-ttu-id="693b0-120">我们建议使用`Debug`级别收集更多详细诊断您的应用程序时。</span><span class="sxs-lookup"><span data-stu-id="693b0-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="693b0-121">`Trace`级别生成非常低级的诊断和很少需要诊断应用程序中的问题。</span><span class="sxs-lookup"><span data-stu-id="693b0-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="693b0-122">访问服务器端日志</span><span class="sxs-lookup"><span data-stu-id="693b0-122">Access server-side logs</span></span>

<span data-ttu-id="693b0-123">访问服务器端日志的方式取决于运行你的环境。</span><span class="sxs-lookup"><span data-stu-id="693b0-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="693b0-124">为 IIS 外部控制台应用</span><span class="sxs-lookup"><span data-stu-id="693b0-124">As a console app outside IIS</span></span>

<span data-ttu-id="693b0-125">如果您在控制台应用中，运行[控制台记录器](xref:fundamentals/logging/index#console-provider)应默认情况下启用。</span><span class="sxs-lookup"><span data-stu-id="693b0-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="693b0-126">SignalR 日志显示在控制台中。</span><span class="sxs-lookup"><span data-stu-id="693b0-126">SignalR logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="693b0-127">从 Visual Studio 的 IIS Express 中</span><span class="sxs-lookup"><span data-stu-id="693b0-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="693b0-128">Visual Studio 将显示中的日志输出**输出**窗口。</span><span class="sxs-lookup"><span data-stu-id="693b0-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="693b0-129">选择**ASP.NET Core Web 服务器**下拉列表选项。</span><span class="sxs-lookup"><span data-stu-id="693b0-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="693b0-130">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="693b0-130">Azure App Service</span></span>

<span data-ttu-id="693b0-131">启用 Azure 应用服务门户的"诊断日志"部分中的"应用程序日志记录 （文件系统）"选项，并配置到级别`Verbose`。</span><span class="sxs-lookup"><span data-stu-id="693b0-131">Enable the "Application Logging (Filesystem)" option in the "Diagnostics logs" section of the Azure App Service portal and configure the Level to `Verbose`.</span></span> <span data-ttu-id="693b0-132">日志应可从"日志流式处理"服务，以及与你的应用服务在文件系统上的日志。</span><span class="sxs-lookup"><span data-stu-id="693b0-132">Logs should be available from the "Log streaming" service, as well as in logs on the file system of your App Service.</span></span> <span data-ttu-id="693b0-133">详细信息，请参阅文档上[Azure 日志流式处理](xref:fundamentals/logging/index#azure-log-streaming)。</span><span class="sxs-lookup"><span data-stu-id="693b0-133">For more information, see the documentation on [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="693b0-134">其他环境</span><span class="sxs-lookup"><span data-stu-id="693b0-134">Other environments</span></span>

<span data-ttu-id="693b0-135">如果您运行在另一个环境 （Docker、 Kubernetes、 Windows 服务等），请参阅上的完整文档[ASP.NET Core 日志记录](xref:fundamentals/logging/index)有关如何配置日志记录提供程序适用于你的环境的详细信息。</span><span class="sxs-lookup"><span data-stu-id="693b0-135">If you're running in another environment (Docker, Kubernetes, Windows Service, etc.), see the full documentation on [ASP.NET Core Logging](xref:fundamentals/logging/index) for more information on how to configure logging providers suitable to your environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="693b0-136">JavaScript 客户端日志记录</span><span class="sxs-lookup"><span data-stu-id="693b0-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="693b0-137">客户端的日志可能包含您的应用程序中的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="693b0-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="693b0-138">**永远不会**原始日志从生产应用程序发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="693b0-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="693b0-139">在使用 JavaScript 客户端时，可以配置使用的日志记录选项`configureLogging`方法`HubConnectionBuilder`:</span><span class="sxs-lookup"><span data-stu-id="693b0-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[Configuring logging in the JavaScript client](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="693b0-140">若要禁用完全日志记录，请指定`signalR.LogLevel.None`在`configureLogging`方法。</span><span class="sxs-lookup"><span data-stu-id="693b0-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="693b0-141">下表显示可用的日志级别向 JavaScript 客户端。</span><span class="sxs-lookup"><span data-stu-id="693b0-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="693b0-142">将日志级别设置为下列值之一，则在该级别和其上表中的所有级别的记录。</span><span class="sxs-lookup"><span data-stu-id="693b0-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="693b0-143">级别</span><span class="sxs-lookup"><span data-stu-id="693b0-143">Level</span></span> | <span data-ttu-id="693b0-144">描述</span><span class="sxs-lookup"><span data-stu-id="693b0-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="693b0-145">未不记录任何消息。</span><span class="sxs-lookup"><span data-stu-id="693b0-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="693b0-146">表示在整个应用程序时失败的消息。</span><span class="sxs-lookup"><span data-stu-id="693b0-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="693b0-147">表示在当前操作失败的消息。</span><span class="sxs-lookup"><span data-stu-id="693b0-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="693b0-148">表示非致命性问题的消息。</span><span class="sxs-lookup"><span data-stu-id="693b0-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="693b0-149">信息性消息。</span><span class="sxs-lookup"><span data-stu-id="693b0-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="693b0-150">用于调试的诊断消息。</span><span class="sxs-lookup"><span data-stu-id="693b0-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="693b0-151">用于诊断特定问题非常详细的诊断消息。</span><span class="sxs-lookup"><span data-stu-id="693b0-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="693b0-152">配置详细级别后，日志将写入到浏览器控制台 （或在 NodeJS 应用中的标准输出） 中。</span><span class="sxs-lookup"><span data-stu-id="693b0-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="693b0-153">如果你想要将日志发送到自定义日志记录系统，则可以提供 JavaScript 对象实现`ILogger`接口。</span><span class="sxs-lookup"><span data-stu-id="693b0-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="693b0-154">需要实现的唯一方法是`log`、 接受事件的级别和与事件关联的消息。</span><span class="sxs-lookup"><span data-stu-id="693b0-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="693b0-155">例如：</span><span class="sxs-lookup"><span data-stu-id="693b0-155">For example:</span></span>

[!code-typescript[Creating a custom logger](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="693b0-156">.NET 客户端日志记录</span><span class="sxs-lookup"><span data-stu-id="693b0-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="693b0-157">客户端的日志可能包含您的应用程序中的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="693b0-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="693b0-158">**永远不会**原始日志从生产应用程序发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="693b0-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="693b0-159">若要从.NET 客户端获取日志，可以使用`ConfigureLogging`方法`HubConnectionBuilder`。</span><span class="sxs-lookup"><span data-stu-id="693b0-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="693b0-160">此工作方式相同`ConfigureLogging`方法`WebHostBuilder`和`HostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="693b0-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="693b0-161">在 ASP.NET Core 中，可以配置您使用的相同日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="693b0-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="693b0-162">但是，您必须手动安装并启用单个日志记录提供程序 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="693b0-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

### <a name="console-logging"></a><span data-ttu-id="693b0-163">控制台日志记录</span><span class="sxs-lookup"><span data-stu-id="693b0-163">Console logging</span></span>

<span data-ttu-id="693b0-164">若要启用控制台日志记录，添加[Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console)包。</span><span class="sxs-lookup"><span data-stu-id="693b0-164">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="693b0-165">然后，使用`AddConsole`方法来配置控制台记录器：</span><span class="sxs-lookup"><span data-stu-id="693b0-165">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[Configuring console logging in .NET client](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="693b0-166">调试输出窗口日志记录</span><span class="sxs-lookup"><span data-stu-id="693b0-166">Debug output window logging</span></span>

<span data-ttu-id="693b0-167">此外可以配置日志以转到**输出**Visual Studio 窗口中的。</span><span class="sxs-lookup"><span data-stu-id="693b0-167">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="693b0-168">安装[Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug)包并使用`AddDebug`方法：</span><span class="sxs-lookup"><span data-stu-id="693b0-168">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[Configuring debug output window logging in .NET client](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="693b0-169">其他日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="693b0-169">Other logging providers</span></span>

<span data-ttu-id="693b0-170">SignalR 支持其他日志记录提供程序，例如 Serilog、 Seq、 NLog 或任何其他日志记录系统以与集成`Microsoft.Extensions.Logging`。</span><span class="sxs-lookup"><span data-stu-id="693b0-170">SignalR supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="693b0-171">如果您的日志记录系统提供了`ILoggerProvider`，可以将它注册`AddProvider`:</span><span class="sxs-lookup"><span data-stu-id="693b0-171">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[Configuring a custom logging provider in .NET client](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="693b0-172">控制详细级别</span><span class="sxs-lookup"><span data-stu-id="693b0-172">Control verbosity</span></span>

<span data-ttu-id="693b0-173">如果应用程序中，要从其他位置进行登录，更改到的默认级别`Debug`可能太详细。</span><span class="sxs-lookup"><span data-stu-id="693b0-173">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="693b0-174">筛选器可用于配置 SignalR 日志的日志记录级别。</span><span class="sxs-lookup"><span data-stu-id="693b0-174">You can use a Filter to configure the logging level for SignalR logs.</span></span> <span data-ttu-id="693b0-175">这可以在代码中，在很大程度是相同的服务器上：</span><span class="sxs-lookup"><span data-stu-id="693b0-175">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="693b0-176">网络跟踪</span><span class="sxs-lookup"><span data-stu-id="693b0-176">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="693b0-177">网络跟踪包含每个应用发送的消息的全部内容。</span><span class="sxs-lookup"><span data-stu-id="693b0-177">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="693b0-178">**永远不会**原始网络跟踪从生产应用程序发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="693b0-178">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="693b0-179">如果遇到问题，网络跟踪有时可以提供大量有用的信息。</span><span class="sxs-lookup"><span data-stu-id="693b0-179">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="693b0-180">这是您要在问题跟踪程序上提出问题的情况特别有用。</span><span class="sxs-lookup"><span data-stu-id="693b0-180">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="693b0-181">收集网络跟踪使用 Fiddler （首选选择）</span><span class="sxs-lookup"><span data-stu-id="693b0-181">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="693b0-182">此方法适用于所有应用。</span><span class="sxs-lookup"><span data-stu-id="693b0-182">This method works for all apps.</span></span>

<span data-ttu-id="693b0-183">Fiddler 是非常强大的工具，用于收集 HTTP 跟踪。</span><span class="sxs-lookup"><span data-stu-id="693b0-183">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="693b0-184">安装从[telerik.com/fiddler](https://www.telerik.com/fiddler)，启动它，然后运行你的应用并重现此问题。</span><span class="sxs-lookup"><span data-stu-id="693b0-184">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="693b0-185">Fiddler 是适用于 Windows，并且没有适用于 macOS 和 Linux 的 beta 版本。</span><span class="sxs-lookup"><span data-stu-id="693b0-185">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="693b0-186">如果使用 HTTPS 连接，有一些额外步骤才能确保 Fiddler 可以解密 HTTPS 流量。</span><span class="sxs-lookup"><span data-stu-id="693b0-186">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="693b0-187">有关更多详细信息，请参阅[Fiddler 文档](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)。</span><span class="sxs-lookup"><span data-stu-id="693b0-187">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="693b0-188">一旦已收集跟踪，可以通过选择导出跟踪**文件** > **保存** > **所有会话...** 从菜单栏中。</span><span class="sxs-lookup"><span data-stu-id="693b0-188">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions...** from the menu bar.</span></span>

![Fiddler 从导出的所有会话](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="693b0-190">收集网络跟踪与 tcpdump （macOS 和仅限 Linux）</span><span class="sxs-lookup"><span data-stu-id="693b0-190">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="693b0-191">此方法适用于所有应用。</span><span class="sxs-lookup"><span data-stu-id="693b0-191">This method works for all apps.</span></span>

<span data-ttu-id="693b0-192">你可以收集通过运行以下命令从命令行界面使用 tcpdump 原始 TCP 跟踪。</span><span class="sxs-lookup"><span data-stu-id="693b0-192">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="693b0-193">可能需要进行`root`前缀的命令或`sudo`如果出现权限错误：</span><span class="sxs-lookup"><span data-stu-id="693b0-193">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="693b0-194">替换为`[interface]`与你想要在捕获的网络接口。</span><span class="sxs-lookup"><span data-stu-id="693b0-194">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="693b0-195">通常，这是类似`/dev/eth0`（适用于在标准的以太网接口） 或`/dev/lo0`（适用于 localhost 流量）。</span><span class="sxs-lookup"><span data-stu-id="693b0-195">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="693b0-196">有关详细信息，请参阅`tcpdump`在主机系统上的手册页。</span><span class="sxs-lookup"><span data-stu-id="693b0-196">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="693b0-197">收集网络跟踪在浏览器中</span><span class="sxs-lookup"><span data-stu-id="693b0-197">Collect a network trace in the browser</span></span>

<span data-ttu-id="693b0-198">此方法仅适用于基于浏览器的应用。</span><span class="sxs-lookup"><span data-stu-id="693b0-198">This method only works for browser-based apps.</span></span>

<span data-ttu-id="693b0-199">大多数浏览器开发人员工具具有可以捕获浏览器和服务器之间的网络活动的"网络"选项卡。</span><span class="sxs-lookup"><span data-stu-id="693b0-199">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="693b0-200">但是，这些跟踪将不包括 WebSocket 和服务器发送事件消息。</span><span class="sxs-lookup"><span data-stu-id="693b0-200">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="693b0-201">如果使用这些传输，使用 Fiddler 或 TcpDump （如下所述） 之类的工具是更好的方法。</span><span class="sxs-lookup"><span data-stu-id="693b0-201">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="693b0-202">Microsoft Edge 和 Internet 资源管理器</span><span class="sxs-lookup"><span data-stu-id="693b0-202">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="693b0-203">（说明是相同的 Edge 和 Internet Explorer）</span><span class="sxs-lookup"><span data-stu-id="693b0-203">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="693b0-204">按 F12 打开开发人员工具</span><span class="sxs-lookup"><span data-stu-id="693b0-204">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="693b0-205">单击网络选项卡</span><span class="sxs-lookup"><span data-stu-id="693b0-205">Click the Network Tab</span></span>
3. <span data-ttu-id="693b0-206">（如果需要），请刷新页面并再现该问题</span><span class="sxs-lookup"><span data-stu-id="693b0-206">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="693b0-207">单击工具栏将跟踪导出为"HAR"文件中的保存图标：</span><span class="sxs-lookup"><span data-stu-id="693b0-207">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![保存图标上 Microsoft Edge 开发人员工具网络选项卡](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="693b0-209">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="693b0-209">Google Chrome</span></span>

1. <span data-ttu-id="693b0-210">按 F12 打开开发人员工具</span><span class="sxs-lookup"><span data-stu-id="693b0-210">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="693b0-211">单击网络选项卡</span><span class="sxs-lookup"><span data-stu-id="693b0-211">Click the Network Tab</span></span>
3. <span data-ttu-id="693b0-212">（如果需要），请刷新页面并再现该问题</span><span class="sxs-lookup"><span data-stu-id="693b0-212">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="693b0-213">在列表中的请求的任意位置右键单击并选择"另存为 HAR 内容":</span><span class="sxs-lookup"><span data-stu-id="693b0-213">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome 开发工具网络选项卡中的"另存为包含内容的 HAR"选项](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="693b0-215">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="693b0-215">Mozilla Firefox</span></span>

1. <span data-ttu-id="693b0-216">按 F12 打开开发人员工具</span><span class="sxs-lookup"><span data-stu-id="693b0-216">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="693b0-217">单击网络选项卡</span><span class="sxs-lookup"><span data-stu-id="693b0-217">Click the Network Tab</span></span>
3. <span data-ttu-id="693b0-218">（如果需要），请刷新页面并再现该问题</span><span class="sxs-lookup"><span data-stu-id="693b0-218">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="693b0-219">在列表中的请求的任意位置右键单击并选择"保存所有为 HAR"</span><span class="sxs-lookup"><span data-stu-id="693b0-219">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox 适用于开发人员工具网络选项卡中"保存全部为 HAR"选项](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="693b0-221">将诊断文件附加到 GitHub 问题</span><span class="sxs-lookup"><span data-stu-id="693b0-221">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="693b0-222">您可以通过重命名它们以便它们具有诊断文件附加到 GitHub 问题`.txt`扩展，然后将拖放到此问题。</span><span class="sxs-lookup"><span data-stu-id="693b0-222">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="693b0-223">请不要将日志文件或网络跟踪的内容粘贴中 GitHub 问题。</span><span class="sxs-lookup"><span data-stu-id="693b0-223">Please don't paste the content of log files or network traces in GitHub issue.</span></span> <span data-ttu-id="693b0-224">这些日志和跟踪可能会很大和 GitHub 通常将截断这些。</span><span class="sxs-lookup"><span data-stu-id="693b0-224">These logs and traces can be quite large and GitHub will usually truncate them.</span></span>

![拖动到 GitHub 问题的日志文件](diagnostics/attaching-diagnostics-files.png)

## <a name="additional-resources"></a><span data-ttu-id="693b0-226">其他资源</span><span class="sxs-lookup"><span data-stu-id="693b0-226">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
