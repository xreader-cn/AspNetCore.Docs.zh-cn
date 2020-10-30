---
title: 'ASP.NET Core 中的日志记录和诊断 :::no-loc(SignalR):::'
author: anurse
description: '了解如何从 ASP.NET Core 应用收集诊断信息 :::no-loc(SignalR)::: 。'
monikerRange: '>= aspnetcore-2.1'
ms.author: anurse
ms.custom: devx-track-csharp, signalr, devx-track-js
ms.date: 06/12/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: signalr/diagnostics
ms.openlocfilehash: 6e5e9d866a1e03e69856cc63dcfe30284048dd6d
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061309"
---
# <a name="logging-and-diagnostics-in-aspnet-core-no-locsignalr"></a><span data-ttu-id="b16a1-103">ASP.NET Core 中的日志记录和诊断 :::no-loc(SignalR):::</span><span class="sxs-lookup"><span data-stu-id="b16a1-103">Logging and diagnostics in ASP.NET Core :::no-loc(SignalR):::</span></span>

<span data-ttu-id="b16a1-104">作者： [Andrew Stanton](https://twitter.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="b16a1-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse)</span></span>

<span data-ttu-id="b16a1-105">本文提供了从 ASP.NET Core 应用收集诊断 :::no-loc(SignalR)::: 信息以帮助解决问题的指南。</span><span class="sxs-lookup"><span data-stu-id="b16a1-105">This article provides guidance for gathering diagnostics from your ASP.NET Core :::no-loc(SignalR)::: app to help troubleshoot issues.</span></span>

## <a name="server-side-logging"></a><span data-ttu-id="b16a1-106">服务器端日志记录</span><span class="sxs-lookup"><span data-stu-id="b16a1-106">Server-side logging</span></span>

> [!WARNING]
> <span data-ttu-id="b16a1-107">服务器端日志可能包含来自应用的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b16a1-108">切勿将来自生产应用的原始日志发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="b16a1-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b16a1-109">由于 :::no-loc(SignalR)::: 是 ASP.NET Core 的一部分，因此它使用 ASP.NET Core 日志记录系统。</span><span class="sxs-lookup"><span data-stu-id="b16a1-109">Since :::no-loc(SignalR)::: is part of ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="b16a1-110">在默认配置中，只 :::no-loc(SignalR)::: 记录很少信息，但可以配置这种信息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-110">In the default configuration, :::no-loc(SignalR)::: logs very little information, but this can configured.</span></span> <span data-ttu-id="b16a1-111">有关配置 ASP.NET Core 日志记录的详细信息，请参阅 [ASP.NET Core 日志记录](xref:fundamentals/logging/index#configuration)上的文档。</span><span class="sxs-lookup"><span data-stu-id="b16a1-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="b16a1-112">:::no-loc(SignalR)::: 使用两个记录器类别：</span><span class="sxs-lookup"><span data-stu-id="b16a1-112">:::no-loc(SignalR)::: uses two logger categories:</span></span>

* <span data-ttu-id="b16a1-113">`Microsoft.AspNetCore.:::no-loc(SignalR):::`：用于与集线器协议相关的日志、激活集线器、调用方法以及其他与中心相关的活动。</span><span class="sxs-lookup"><span data-stu-id="b16a1-113">`Microsoft.AspNetCore.:::no-loc(SignalR):::`: For logs related to Hub Protocols, activating Hubs, invoking methods, and other Hub-related activities.</span></span>
* <span data-ttu-id="b16a1-114">`Microsoft.AspNetCore.Http.Connections`：用于与传输相关的日志，例如 Websocket、长轮询、Server-Sent 事件和低级别 :::no-loc(SignalR)::: 基础结构。</span><span class="sxs-lookup"><span data-stu-id="b16a1-114">`Microsoft.AspNetCore.Http.Connections`: For logs related to transports, such as WebSockets, Long Polling, Server-Sent Events, and low-level :::no-loc(SignalR)::: infrastructure.</span></span>

<span data-ttu-id="b16a1-115">若要从中启用详细日志 :::no-loc(SignalR)::: ，请 `Debug` *:::no-loc(appsettings.json):::* 通过将以下项添加到 `LogLevel` 中的子部分，将上述两个前缀配置为文件中的级别 `Logging` ：</span><span class="sxs-lookup"><span data-stu-id="b16a1-115">To enable detailed logs from :::no-loc(SignalR):::, configure both of the preceding prefixes to the `Debug` level in your *:::no-loc(appsettings.json):::* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7-8)]

<span data-ttu-id="b16a1-116">你还可以在方法的代码中对此进行配置 `CreateWebHostBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="b16a1-116">You can also configure this in code in your `CreateWebHostBuilder` method:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5-6)]

<span data-ttu-id="b16a1-117">如果不使用基于 JSON 的配置，请在配置系统中设置以下配置值：</span><span class="sxs-lookup"><span data-stu-id="b16a1-117">If you aren't using JSON-based configuration, set the following configuration values in your configuration system:</span></span>

* `Logging:LogLevel:Microsoft.AspNetCore.:::no-loc(SignalR):::` = `Debug`
* `Logging:LogLevel:Microsoft.AspNetCore.Http.Connections` = `Debug`

<span data-ttu-id="b16a1-118">查看配置系统的文档以确定如何指定嵌套的配置值。</span><span class="sxs-lookup"><span data-stu-id="b16a1-118">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="b16a1-119">例如，使用环境变量时，使用两个 `_` 而不是 `:` 字符（例如 `Logging__LogLevel__Microsoft.AspNetCore.:::no-loc(SignalR):::`）。</span><span class="sxs-lookup"><span data-stu-id="b16a1-119">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Microsoft.AspNetCore.:::no-loc(SignalR):::`).</span></span>

<span data-ttu-id="b16a1-120">建议在为应用收集更详细的诊断时使用 `Debug` 级别。</span><span class="sxs-lookup"><span data-stu-id="b16a1-120">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="b16a1-121">`Trace` 级别产生的诊断级别很低，且很少需要它来诊断应用中的问题。</span><span class="sxs-lookup"><span data-stu-id="b16a1-121">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

## <a name="access-server-side-logs"></a><span data-ttu-id="b16a1-122">访问服务器端日志</span><span class="sxs-lookup"><span data-stu-id="b16a1-122">Access server-side logs</span></span>

<span data-ttu-id="b16a1-123">访问服务器端日志的方式取决于在其中运行的环境。</span><span class="sxs-lookup"><span data-stu-id="b16a1-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app-outside-iis"></a><span data-ttu-id="b16a1-124">作为 IIS 外部的控制台应用</span><span class="sxs-lookup"><span data-stu-id="b16a1-124">As a console app outside IIS</span></span>

<span data-ttu-id="b16a1-125">如果在控制台应用中运行，则默认情况下应启用[控制台记录器](xref:fundamentals/logging/index#console)。</span><span class="sxs-lookup"><span data-stu-id="b16a1-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="b16a1-126">:::no-loc(SignalR)::: 日志将显示在控制台中。</span><span class="sxs-lookup"><span data-stu-id="b16a1-126">:::no-loc(SignalR)::: logs will appear in the console.</span></span>

### <a name="within-iis-express-from-visual-studio"></a><span data-ttu-id="b16a1-127">在 Visual Studio IIS Express 中</span><span class="sxs-lookup"><span data-stu-id="b16a1-127">Within IIS Express from Visual Studio</span></span>

<span data-ttu-id="b16a1-128">Visual Studio 会在 " **输出** " 窗口中显示日志输出。</span><span class="sxs-lookup"><span data-stu-id="b16a1-128">Visual Studio displays the log output in the **Output** window.</span></span> <span data-ttu-id="b16a1-129">选择 **ASP.NET Core Web 服务器** "下拉选项。</span><span class="sxs-lookup"><span data-stu-id="b16a1-129">Select the **ASP.NET Core Web Server** drop down option.</span></span>

### <a name="azure-app-service"></a><span data-ttu-id="b16a1-130">Azure 应用服务</span><span class="sxs-lookup"><span data-stu-id="b16a1-130">Azure App Service</span></span>

<span data-ttu-id="b16a1-131">在 Azure App Service 门户的 " **诊断日志** " 部分中，启用 **应用程序日志记录 (Filesystem)** 选项，并将 **级别** 配置为 `Verbose` 。</span><span class="sxs-lookup"><span data-stu-id="b16a1-131">Enable the **Application Logging (Filesystem)** option in the **Diagnostics logs** section of the Azure App Service portal and configure the **Level** to `Verbose`.</span></span> <span data-ttu-id="b16a1-132">日志 **流** 服务和应用服务文件系统的日志中应提供日志。</span><span class="sxs-lookup"><span data-stu-id="b16a1-132">Logs should be available from the **Log streaming** service and in logs on the file system of the App Service.</span></span> <span data-ttu-id="b16a1-133">有关详细信息，请参阅 [Azure 日志流式处理](xref:fundamentals/logging/index#azure-log-streaming)。</span><span class="sxs-lookup"><span data-stu-id="b16a1-133">For more information, see [Azure log streaming](xref:fundamentals/logging/index#azure-log-streaming).</span></span>

### <a name="other-environments"></a><span data-ttu-id="b16a1-134">其他环境</span><span class="sxs-lookup"><span data-stu-id="b16a1-134">Other environments</span></span>

<span data-ttu-id="b16a1-135">如果将应用部署到另一个环境（例如 Docker、Kubernetes 或 Windows 服务），请参阅 <xref:fundamentals/logging/index>，了解有关如何配置适用于环境的日志记录提供程序的详细信息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-135">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="javascript-client-logging"></a><span data-ttu-id="b16a1-136">JavaScript 客户端日志记录</span><span class="sxs-lookup"><span data-stu-id="b16a1-136">JavaScript client logging</span></span>

> [!WARNING]
> <span data-ttu-id="b16a1-137">客户端日志可能包含来自应用的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-137">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b16a1-138">切勿将来自生产应用的原始日志发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="b16a1-138">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b16a1-139">使用 JavaScript 客户端时，可以使用中的方法配置日志记录选项 `configureLogging` `HubConnectionBuilder` ：</span><span class="sxs-lookup"><span data-stu-id="b16a1-139">When using the JavaScript client, you can configure logging options using the `configureLogging` method on `HubConnectionBuilder`:</span></span>

[!code-javascript[](diagnostics/logging-config-js.js?highlight=3)]

<span data-ttu-id="b16a1-140">若要完全禁用日志记录，请 `signalR.LogLevel.None` 在方法中指定 `configureLogging` 。</span><span class="sxs-lookup"><span data-stu-id="b16a1-140">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b16a1-141">下表显示了可用于 JavaScript 客户端的日志级别。</span><span class="sxs-lookup"><span data-stu-id="b16a1-141">The following table shows log levels available to the JavaScript client.</span></span> <span data-ttu-id="b16a1-142">将日志级别设置为这些值之一，可以在表中对该级别和其之上的所有级别进行日志记录。</span><span class="sxs-lookup"><span data-stu-id="b16a1-142">Setting the log level to one of these values enables logging at that level and all levels above it in the table.</span></span>

| <span data-ttu-id="b16a1-143">Level</span><span class="sxs-lookup"><span data-stu-id="b16a1-143">Level</span></span> | <span data-ttu-id="b16a1-144">说明</span><span class="sxs-lookup"><span data-stu-id="b16a1-144">Description</span></span> |
| ----- | ----------- |
| `None` | <span data-ttu-id="b16a1-145">不记录任何消息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-145">No messages are logged.</span></span> |
| `Critical` | <span data-ttu-id="b16a1-146">指示整个应用程序中的失败的消息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-146">Messages that indicate a failure in the entire app.</span></span> |
| `Error` | <span data-ttu-id="b16a1-147">指示当前操作失败的消息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-147">Messages that indicate a failure in the current operation.</span></span> |
| `Warning` | <span data-ttu-id="b16a1-148">指示非严重问题的消息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-148">Messages that indicate a non-fatal problem.</span></span> |
| `Information` | <span data-ttu-id="b16a1-149">信息性消息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-149">Informational messages.</span></span> |
| `Debug` | <span data-ttu-id="b16a1-150">诊断消息对于调试很有用。</span><span class="sxs-lookup"><span data-stu-id="b16a1-150">Diagnostic messages useful for debugging.</span></span> |
| `Trace` | <span data-ttu-id="b16a1-151">旨在诊断特定问题的详细诊断消息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-151">Very detailed diagnostic messages designed for diagnosing specific issues.</span></span> |

<span data-ttu-id="b16a1-152">配置详细级别后，日志将写入浏览器控制台 (或) 中的 NodeJS 应用程序中的标准输出。</span><span class="sxs-lookup"><span data-stu-id="b16a1-152">Once you've configured the verbosity, the logs will be written to the Browser Console (or Standard Output in a NodeJS app).</span></span>

<span data-ttu-id="b16a1-153">如果要将日志发送到自定义日志记录系统，可以提供实现接口的 JavaScript 对象 `ILogger` 。</span><span class="sxs-lookup"><span data-stu-id="b16a1-153">If you want to send logs to a custom logging system, you can provide a JavaScript object implementing the `ILogger` interface.</span></span> <span data-ttu-id="b16a1-154">唯一需要实现的方法是 `log` ，它将使用事件的级别和与事件关联的消息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-154">The only method that needs to be implemented is `log`, which takes the level of the event and the message associated with the event.</span></span> <span data-ttu-id="b16a1-155">例如：</span><span class="sxs-lookup"><span data-stu-id="b16a1-155">For example:</span></span>

[!code-typescript[](diagnostics/custom-logger.ts?highlight=3-7,13)]

## <a name="net-client-logging"></a><span data-ttu-id="b16a1-156"> .NET 客户端日志记录</span><span class="sxs-lookup"><span data-stu-id="b16a1-156">.NET client logging</span></span>

> [!WARNING]
> <span data-ttu-id="b16a1-157">客户端日志可能包含来自应用的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-157">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="b16a1-158">切勿将来自生产应用的原始日志发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="b16a1-158">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b16a1-159">若要从 .NET 客户端获取日志，可以对使用 `ConfigureLogging` 方法 `HubConnectionBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="b16a1-159">To get logs from the .NET client, you can use the `ConfigureLogging` method on `HubConnectionBuilder`.</span></span> <span data-ttu-id="b16a1-160">这与和上的方法的工作方式相同 `ConfigureLogging` `WebHostBuilder` `HostBuilder` 。</span><span class="sxs-lookup"><span data-stu-id="b16a1-160">This works the same way as the `ConfigureLogging` method on `WebHostBuilder` and `HostBuilder`.</span></span> <span data-ttu-id="b16a1-161">你可以配置 ASP.NET Core 中使用的相同日志记录提供程序。</span><span class="sxs-lookup"><span data-stu-id="b16a1-161">You can configure the same logging providers you use in ASP.NET Core.</span></span> <span data-ttu-id="b16a1-162">但是，您必须为单独的日志提供程序手动安装和启用 NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="b16a1-162">However, you have to manually install and enable the NuGet packages for the individual logging providers.</span></span>

<span data-ttu-id="b16a1-163">若要将 .NET 客户端日志记录添加到 :::no-loc(Blazor WebAssembly)::: 应用程序，请参阅 <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging> 。</span><span class="sxs-lookup"><span data-stu-id="b16a1-163">To add .NET client logging to a :::no-loc(Blazor WebAssembly)::: app, see <xref:blazor/fundamentals/logging#blazor-webassembly-signalr-net-client-logging>.</span></span>

### <a name="console-logging"></a><span data-ttu-id="b16a1-164">控制台日志记录</span><span class="sxs-lookup"><span data-stu-id="b16a1-164">Console logging</span></span>

<span data-ttu-id="b16a1-165">若要启用控制台日志记录，请添加[""。](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console)</span><span class="sxs-lookup"><span data-stu-id="b16a1-165">In order to enable Console logging, add the [Microsoft.Extensions.Logging.Console](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console) package.</span></span> <span data-ttu-id="b16a1-166">然后，使用 `AddConsole` 方法来配置控制台记录器：</span><span class="sxs-lookup"><span data-stu-id="b16a1-166">Then, use the `AddConsole` method to configure the console logger:</span></span>

[!code-csharp[](diagnostics/net-client-console-log.cs?highlight=6)]

### <a name="debug-output-window-logging"></a><span data-ttu-id="b16a1-167">调试输出窗口日志记录</span><span class="sxs-lookup"><span data-stu-id="b16a1-167">Debug output window logging</span></span>

<span data-ttu-id="b16a1-168">还可以配置日志，以便在 Visual Studio 中切换到 " **输出** " 窗口。</span><span class="sxs-lookup"><span data-stu-id="b16a1-168">You can also configure logs to go to the **Output** window in Visual Studio.</span></span> <span data-ttu-id="b16a1-169">安装 " ["，](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) 然后使用 `AddDebug` 方法：</span><span class="sxs-lookup"><span data-stu-id="b16a1-169">Install the [Microsoft.Extensions.Logging.Debug](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Debug) package and use the `AddDebug` method:</span></span>

[!code-csharp[](diagnostics/net-client-debug-log.cs?highlight=6)]

### <a name="other-logging-providers"></a><span data-ttu-id="b16a1-170">其他日志记录提供程序</span><span class="sxs-lookup"><span data-stu-id="b16a1-170">Other logging providers</span></span>

<span data-ttu-id="b16a1-171">:::no-loc(SignalR)::: 支持其他日志记录提供程序，如 Serilog、Seq、NLog 或与集成的任何其他日志记录系统 `Microsoft.Extensions.Logging` 。</span><span class="sxs-lookup"><span data-stu-id="b16a1-171">:::no-loc(SignalR)::: supports other logging providers such as Serilog, Seq, NLog, or any other logging system that integrates with `Microsoft.Extensions.Logging`.</span></span> <span data-ttu-id="b16a1-172">如果日志记录系统提供 `ILoggerProvider` ，则可以将其注册到 `AddProvider` ：</span><span class="sxs-lookup"><span data-stu-id="b16a1-172">If your logging system provides an `ILoggerProvider`, you can register it with `AddProvider`:</span></span>

[!code-csharp[](diagnostics/net-client-custom-log.cs?highlight=6)]

### <a name="control-verbosity"></a><span data-ttu-id="b16a1-173">控件详细级别</span><span class="sxs-lookup"><span data-stu-id="b16a1-173">Control verbosity</span></span>

<span data-ttu-id="b16a1-174">如果要从应用中的其他位置进行日志记录，则将默认级别更改为 `Debug` 可能太详细。</span><span class="sxs-lookup"><span data-stu-id="b16a1-174">If you are logging from other places in your app, changing the default level to `Debug` may be too verbose.</span></span> <span data-ttu-id="b16a1-175">您可以使用筛选器来配置日志的日志记录级别 :::no-loc(SignalR)::: 。</span><span class="sxs-lookup"><span data-stu-id="b16a1-175">You can use a Filter to configure the logging level for :::no-loc(SignalR)::: logs.</span></span> <span data-ttu-id="b16a1-176">可以在代码中完成此操作，其方式与在服务器上的操作大致相同：</span><span class="sxs-lookup"><span data-stu-id="b16a1-176">This can be done in code, in much the same way as on the server:</span></span>

[!code-csharp[Controlling verbosity in .NET client](diagnostics/logging-config-client-code.cs?highlight=9-10)]

## <a name="network-traces"></a><span data-ttu-id="b16a1-177">网络跟踪</span><span class="sxs-lookup"><span data-stu-id="b16a1-177">Network traces</span></span>

> [!WARNING]
> <span data-ttu-id="b16a1-178">网络跟踪包含应用发送的每个消息的全部内容。</span><span class="sxs-lookup"><span data-stu-id="b16a1-178">A network trace contains the full contents of every message sent by your app.</span></span> <span data-ttu-id="b16a1-179">**切勿** 将原始网络跟踪从生产应用发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="b16a1-179">**Never** post raw network traces from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="b16a1-180">如果遇到问题，网络跟踪有时可以提供很多有用的信息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-180">If you encounter an issue, a network trace can sometimes provide a lot of helpful information.</span></span> <span data-ttu-id="b16a1-181">如果要在我们的问题跟踪程序上发布问题，此方法特别有用。</span><span class="sxs-lookup"><span data-stu-id="b16a1-181">This is particularly useful if you're going to file an issue on our issue tracker.</span></span>

## <a name="collect-a-network-trace-with-fiddler-preferred-option"></a><span data-ttu-id="b16a1-182">使用 Fiddler (首选选项收集网络跟踪) </span><span class="sxs-lookup"><span data-stu-id="b16a1-182">Collect a network trace with Fiddler (preferred option)</span></span>

<span data-ttu-id="b16a1-183">此方法适用于所有应用。</span><span class="sxs-lookup"><span data-stu-id="b16a1-183">This method works for all apps.</span></span>

<span data-ttu-id="b16a1-184">Fiddler 是一个非常强大的工具，用于收集 HTTP 跟踪。</span><span class="sxs-lookup"><span data-stu-id="b16a1-184">Fiddler is a very powerful tool for collecting HTTP traces.</span></span> <span data-ttu-id="b16a1-185">从 [telerik.com/fiddler](https://www.telerik.com/fiddler)安装它，启动它，然后运行你的应用程序并重现此问题。</span><span class="sxs-lookup"><span data-stu-id="b16a1-185">Install it from [telerik.com/fiddler](https://www.telerik.com/fiddler), launch it, and then run your app and reproduce the issue.</span></span> <span data-ttu-id="b16a1-186">Fiddler 适用于 Windows，并且有适用于 macOS 和 Linux 的 beta 版本。</span><span class="sxs-lookup"><span data-stu-id="b16a1-186">Fiddler is available for Windows, and there are beta versions for macOS and Linux.</span></span>

<span data-ttu-id="b16a1-187">如果使用 HTTPS 进行连接，则需要执行一些额外的步骤来确保 Fiddler 可以解密 HTTPS 流量。</span><span class="sxs-lookup"><span data-stu-id="b16a1-187">If you connect using HTTPS, there are some extra steps to ensure Fiddler can decrypt the HTTPS traffic.</span></span> <span data-ttu-id="b16a1-188">有关更多详细信息，请参阅 [Fiddler 文档](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)。</span><span class="sxs-lookup"><span data-stu-id="b16a1-188">For more details, see the [Fiddler documentation](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).</span></span>

<span data-ttu-id="b16a1-189">收集跟踪后，可以通过从菜单栏中选择 "文件" **File**  >  " **保存**  >  **所有会话** " 来导出跟踪。</span><span class="sxs-lookup"><span data-stu-id="b16a1-189">Once you've collected the trace, you can export the trace by choosing **File** > **Save** > **All Sessions** from the menu bar.</span></span>

![正在从 Fiddler 导出所有会话](diagnostics/fiddler-export.png)

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a><span data-ttu-id="b16a1-191">仅使用 tcpdump (macOS 和 Linux 收集网络跟踪) </span><span class="sxs-lookup"><span data-stu-id="b16a1-191">Collect a network trace with tcpdump (macOS and Linux only)</span></span>

<span data-ttu-id="b16a1-192">此方法适用于所有应用。</span><span class="sxs-lookup"><span data-stu-id="b16a1-192">This method works for all apps.</span></span>

<span data-ttu-id="b16a1-193">可以通过在命令行界面中运行以下命令，使用 tcpdump 收集原始 TCP 跟踪。</span><span class="sxs-lookup"><span data-stu-id="b16a1-193">You can collect raw TCP traces using tcpdump by running the following command from a command shell.</span></span> <span data-ttu-id="b16a1-194">如果出现权限错误，你可能需要将 `root` 命令作为或的前缀 `sudo` ：</span><span class="sxs-lookup"><span data-stu-id="b16a1-194">You may need to be `root` or prefix the command with `sudo` if you get a permissions error:</span></span>

```console
tcpdump -i [interface] -w trace.pcap
```

<span data-ttu-id="b16a1-195">将替换 `[interface]` 为要捕获的网络接口。</span><span class="sxs-lookup"><span data-stu-id="b16a1-195">Replace `[interface]` with the network interface you wish to capture on.</span></span> <span data-ttu-id="b16a1-196">通常，这种情况类似于 `/dev/eth0` 标准以太网接口的 () 或 `/dev/lo0` (localhost 流量) 。</span><span class="sxs-lookup"><span data-stu-id="b16a1-196">Usually, this is something like `/dev/eth0` (for your standard Ethernet interface) or `/dev/lo0` (for localhost traffic).</span></span> <span data-ttu-id="b16a1-197">有关详细信息，请参阅 `tcpdump` 主机系统上的手册页。</span><span class="sxs-lookup"><span data-stu-id="b16a1-197">For more information, see the `tcpdump` man page on your host system.</span></span>

## <a name="collect-a-network-trace-in-the-browser"></a><span data-ttu-id="b16a1-198">在浏览器中收集网络跟踪</span><span class="sxs-lookup"><span data-stu-id="b16a1-198">Collect a network trace in the browser</span></span>

<span data-ttu-id="b16a1-199">此方法仅适用于基于浏览器的应用。</span><span class="sxs-lookup"><span data-stu-id="b16a1-199">This method only works for browser-based apps.</span></span>

<span data-ttu-id="b16a1-200">大多数浏览器开发人员工具都有一个 "网络" 选项卡，该选项卡允许您捕获浏览器和服务器之间的网络活动。</span><span class="sxs-lookup"><span data-stu-id="b16a1-200">Most browser Developer Tools have a "Network" tab that allows you to capture network activity between the browser and the server.</span></span> <span data-ttu-id="b16a1-201">但是，这些跟踪不包括 WebSocket 和 Server-Sent 事件消息。</span><span class="sxs-lookup"><span data-stu-id="b16a1-201">However, these traces don't include WebSocket and Server-Sent Event messages.</span></span> <span data-ttu-id="b16a1-202">如果使用的是这些传输，请使用) 下面所述的工具（如 Fiddler 或 TcpDump） (更好的方法。</span><span class="sxs-lookup"><span data-stu-id="b16a1-202">If you are using those transports, using a tool like Fiddler or TcpDump (described below) is a better approach.</span></span>

### <a name="microsoft-edge-and-internet-explorer"></a><span data-ttu-id="b16a1-203">Microsoft Edge 和 Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="b16a1-203">Microsoft Edge and Internet Explorer</span></span>

<span data-ttu-id="b16a1-204"> (边缘和 Internet Explorer 的说明相同) </span><span class="sxs-lookup"><span data-stu-id="b16a1-204">(The instructions are the same for both Edge and Internet Explorer)</span></span>

1. <span data-ttu-id="b16a1-205">按 F12 打开开发工具</span><span class="sxs-lookup"><span data-stu-id="b16a1-205">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="b16a1-206">单击 "网络" 选项卡</span><span class="sxs-lookup"><span data-stu-id="b16a1-206">Click the Network Tab</span></span>
3. <span data-ttu-id="b16a1-207">如果需要，请刷新页面 () 并重现此问题</span><span class="sxs-lookup"><span data-stu-id="b16a1-207">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="b16a1-208">单击工具栏中的 "保存" 图标，将跟踪作为 "HAR" 文件导出：</span><span class="sxs-lookup"><span data-stu-id="b16a1-208">Click the Save icon in the toolbar to export the trace as a "HAR" file:</span></span>

![Microsoft Edge 开发工具网络选项卡上的 "保存" 图标](diagnostics/ie-edge-har-export.png)

### <a name="google-chrome"></a><span data-ttu-id="b16a1-210">Google Chrome</span><span class="sxs-lookup"><span data-stu-id="b16a1-210">Google Chrome</span></span>

1. <span data-ttu-id="b16a1-211">按 F12 打开开发工具</span><span class="sxs-lookup"><span data-stu-id="b16a1-211">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="b16a1-212">单击 "网络" 选项卡</span><span class="sxs-lookup"><span data-stu-id="b16a1-212">Click the Network Tab</span></span>
3. <span data-ttu-id="b16a1-213">如果需要，请刷新页面 () 并重现此问题</span><span class="sxs-lookup"><span data-stu-id="b16a1-213">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="b16a1-214">右键单击请求列表中的任意位置，然后选择 "另存为包含内容的 HAR"：</span><span class="sxs-lookup"><span data-stu-id="b16a1-214">Right click anywhere in the list of requests and choose "Save as HAR with content":</span></span>

![Google Chrome 开发工具网络选项卡中的 "另存为 HAR 与内容" 选项](diagnostics/chrome-har-export.png)

### <a name="mozilla-firefox"></a><span data-ttu-id="b16a1-216">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="b16a1-216">Mozilla Firefox</span></span>

1. <span data-ttu-id="b16a1-217">按 F12 打开开发工具</span><span class="sxs-lookup"><span data-stu-id="b16a1-217">Press F12 to open the Dev Tools</span></span>
2. <span data-ttu-id="b16a1-218">单击 "网络" 选项卡</span><span class="sxs-lookup"><span data-stu-id="b16a1-218">Click the Network Tab</span></span>
3. <span data-ttu-id="b16a1-219">如果需要，请刷新页面 () 并重现此问题</span><span class="sxs-lookup"><span data-stu-id="b16a1-219">Refresh the page (if needed) and reproduce the problem</span></span>
4. <span data-ttu-id="b16a1-220">右键单击请求列表中的任意位置，然后选择 "全部保存为 HAR"</span><span class="sxs-lookup"><span data-stu-id="b16a1-220">Right click anywhere in the list of requests and choose "Save All As HAR"</span></span>

![Mozilla Firefox 开发工具网络选项卡中的 "全部保存为 HAR" 选项](diagnostics/firefox-har-export.png)

## <a name="attach-diagnostics-files-to-github-issues"></a><span data-ttu-id="b16a1-222">将诊断文件附加到 GitHub 问题</span><span class="sxs-lookup"><span data-stu-id="b16a1-222">Attach diagnostics files to GitHub issues</span></span>

<span data-ttu-id="b16a1-223">可以通过对其进行重命名，将诊断文件附加到 GitHub 问题，以便它们具有一个 `.txt` 扩展，然后将其拖放到该问题上。</span><span class="sxs-lookup"><span data-stu-id="b16a1-223">You can attach Diagnostics files to GitHub issues by renaming them so they have a `.txt` extension and then dragging and dropping them on to the issue.</span></span>

> [!NOTE]
> <span data-ttu-id="b16a1-224">请不要将日志文件或网络跟踪的内容粘贴到 GitHub 问题中。</span><span class="sxs-lookup"><span data-stu-id="b16a1-224">Please don't paste the content of log files or network traces into a GitHub issue.</span></span> <span data-ttu-id="b16a1-225">这些日志和跟踪可能会很大，GitHub 通常会将其截断。</span><span class="sxs-lookup"><span data-stu-id="b16a1-225">These logs and traces can be quite large, and GitHub usually truncates them.</span></span>

![将日志文件拖到 GitHub 问题上](diagnostics/attaching-diagnostics-files.png)

## <a name="metrics"></a><span data-ttu-id="b16a1-227">指标</span><span class="sxs-lookup"><span data-stu-id="b16a1-227">Metrics</span></span>

<span data-ttu-id="b16a1-228">度量值是基于时间间隔的数据度量值的表示形式。</span><span class="sxs-lookup"><span data-stu-id="b16a1-228">Metrics is a representation of data measures over intervals of time.</span></span> <span data-ttu-id="b16a1-229">例如，每秒请求数。</span><span class="sxs-lookup"><span data-stu-id="b16a1-229">For example, requests per second.</span></span> <span data-ttu-id="b16a1-230">指标数据允许以较高的级别观察应用的状态。</span><span class="sxs-lookup"><span data-stu-id="b16a1-230">Metrics data allows observation of the state of an app at a high level.</span></span> <span data-ttu-id="b16a1-231">.NET gRPC 指标是使用 <xref:System.Diagnostics.Tracing.EventCounter> 发出的。</span><span class="sxs-lookup"><span data-stu-id="b16a1-231">.NET gRPC metrics are emitted using <xref:System.Diagnostics.Tracing.EventCounter>.</span></span>

### <a name="no-locsignalr-server-metrics"></a><span data-ttu-id="b16a1-232">:::no-loc(SignalR)::: 服务器指标</span><span class="sxs-lookup"><span data-stu-id="b16a1-232">:::no-loc(SignalR)::: server metrics</span></span>

<span data-ttu-id="b16a1-233">:::no-loc(SignalR)::: 在事件源上报告服务器指标 <xref:Microsoft.AspNetCore.Http.Connections> 。</span><span class="sxs-lookup"><span data-stu-id="b16a1-233">:::no-loc(SignalR)::: server metrics are reported on the <xref:Microsoft.AspNetCore.Http.Connections> event source.</span></span>

| <span data-ttu-id="b16a1-234">“属性”</span><span class="sxs-lookup"><span data-stu-id="b16a1-234">Name</span></span>                    | <span data-ttu-id="b16a1-235">说明</span><span class="sxs-lookup"><span data-stu-id="b16a1-235">Description</span></span>                 |
|-------------------------|-----------------------------|
| `connections-started`   | <span data-ttu-id="b16a1-236">已启动的连接总数</span><span class="sxs-lookup"><span data-stu-id="b16a1-236">Total connections started</span></span>   |
| `connections-stopped`   | <span data-ttu-id="b16a1-237">已停止的连接总数</span><span class="sxs-lookup"><span data-stu-id="b16a1-237">Total connections stopped</span></span>   |
| `connections-timed-out` | <span data-ttu-id="b16a1-238">总连接超时</span><span class="sxs-lookup"><span data-stu-id="b16a1-238">Total connections timed out</span></span> |
| `current-connections`   | <span data-ttu-id="b16a1-239">当前连接数</span><span class="sxs-lookup"><span data-stu-id="b16a1-239">Current connections</span></span>         |
| `connections-duration`  | <span data-ttu-id="b16a1-240">平均连接持续时间</span><span class="sxs-lookup"><span data-stu-id="b16a1-240">Average connection duration</span></span> |

### <a name="observe-metrics"></a><span data-ttu-id="b16a1-241">观察指标</span><span class="sxs-lookup"><span data-stu-id="b16a1-241">Observe metrics</span></span>

<span data-ttu-id="b16a1-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) 是一个性能监视工具，用于临时运行状况监视和初级性能调查。</span><span class="sxs-lookup"><span data-stu-id="b16a1-242">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="b16a1-243">使用 `Microsoft.AspNetCore.Http.Connections` 作为提供程序名称监视 .net 应用程序。</span><span class="sxs-lookup"><span data-stu-id="b16a1-243">Monitor a .NET app with `Microsoft.AspNetCore.Http.Connections` as the provider name.</span></span> <span data-ttu-id="b16a1-244">例如：</span><span class="sxs-lookup"><span data-stu-id="b16a1-244">For example:</span></span>

```console
> dotnet-counters monitor --process-id 37016 Microsoft.AspNetCore.Http.Connections

Press p to pause, r to resume, q to quit.
    Status: Running
[Microsoft.AspNetCore.Http.Connections]
    Average Connection Duration (ms)       16,040.56
    Current Connections                         1
    Total Connections Started                   8
    Total Connections Stopped                   7
    Total Connections Timed Out                 0
```

## <a name="additional-resources"></a><span data-ttu-id="b16a1-245">其他资源</span><span class="sxs-lookup"><span data-stu-id="b16a1-245">Additional resources</span></span>

* <xref:signalr/configuration>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
