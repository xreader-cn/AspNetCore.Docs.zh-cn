---
title: ASP.NET Core 模块的日志创建和重定向
author: rick-anderson
description: 配置 IIS 和 ASP.NET Core 模块以捕获日志和诊断信息。
monikerRange: '>= aspnetcore-5.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: host-and-deploy/iis/logging-and-diagnostics
ms.openlocfilehash: b866be130a93491bce7c5c7e08045de961ff91b2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93057435"
---
# <a name="log-creation-and-redirection"></a><span data-ttu-id="d6ee0-103">日志创建和重定向</span><span class="sxs-lookup"><span data-stu-id="d6ee0-103">Log creation and redirection</span></span>

<span data-ttu-id="d6ee0-104">如果已设置 `aspNetCore` 元素的 `stdoutLogEnabled` 和 `stdoutLogFile` 属性，ASP.NET Core 模块会将 stdout 和 stderr 控制台输出重定向到磁盘。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-104">The ASP.NET Core Module redirects stdout and stderr console output to disk if the `stdoutLogEnabled` and `stdoutLogFile` attributes of the `aspNetCore` element are set.</span></span> <span data-ttu-id="d6ee0-105">创建日志文件时，模块会创建 `stdoutLogFile` 路径中提供的所有文件夹。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-105">Any folders in the `stdoutLogFile` path are created by the module when the log file is created.</span></span> <span data-ttu-id="d6ee0-106">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\{APP POOL NAME}` 提供写入权限，其中占位符 `{APP POOL NAME}` 为应用池名称）。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-106">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="d6ee0-107">日志不会旋转，除非回收/重新启动进程。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-107">Logs aren't rotated, unless process recycling/restart occurs.</span></span> <span data-ttu-id="d6ee0-108">宿主负责限制日志占用的磁盘空间。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-108">It's the responsibility of the hoster to limit the disk space the logs consume.</span></span>

<span data-ttu-id="d6ee0-109">仅当在 IIS 上托管或使用 [Visual Studio 中针对 IIS 的开发时支持](xref:host-and-deploy/iis/development-time-iis-support)时，而不是在本地调试和使用 IIS Express 运行应用时，才建议使用 stdout 日志来排查应用程序启动问题。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-109">Using the stdout log is only recommended for troubleshooting app startup issues when hosting on IIS or when using [development-time support for IIS with Visual Studio](xref:host-and-deploy/iis/development-time-iis-support), not while debugging locally and running the app with IIS Express.</span></span>

<span data-ttu-id="d6ee0-110">不要将 stdout 日志用于常规应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-110">Don't use the stdout log for general app logging purposes.</span></span> <span data-ttu-id="d6ee0-111">对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-111">For routine logging in an ASP.NET Core app, use a logging library that limits log file size and rotates logs.</span></span> <span data-ttu-id="d6ee0-112">有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-112">For more information, see [third-party logging providers](xref:fundamentals/logging/index#third-party-logging-providers).</span></span>

<span data-ttu-id="d6ee0-113">创建日志文件时，将自动添加时间戳和文件扩展名。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-113">A timestamp and file extension are added automatically when the log file is created.</span></span> <span data-ttu-id="d6ee0-114">日志文件名是通过将时间戳、进程 ID 和文件扩展名 (`.log`) 附加到由下划线分隔的 `stdoutLogFile` 路径的最后一段（通常为 `stdout`）组合而成的。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-114">The log file name is composed by appending the timestamp, process ID, and file extension (`.log`) to the last segment of the `stdoutLogFile` path (typically `stdout`) delimited by underscores.</span></span> <span data-ttu-id="d6ee0-115">如果 `stdoutLogFile` 路径以 `stdout` 结尾，则 PID 为 1934 且于 2018 年 2 月 5 日 19:42:32 创建的应用的日志的文件名为 `stdout_20180205194132_1934.log`。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-115">If the `stdoutLogFile` path ends with `stdout`, a log for an app with a PID of 1934 created on 2/5/2018 at 19:42:32 has the file name `stdout_20180205194132_1934.log`.</span></span>

<span data-ttu-id="d6ee0-116">如果 `stdoutLogEnabled` 为 false，则会捕获应用启动时发生的错误，并将其发送到事件日志，最大为 30 KB。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-116">If `stdoutLogEnabled` is false, errors that occur on app startup are captured and emitted to the event log up to 30 KB.</span></span> <span data-ttu-id="d6ee0-117">启动后，将丢弃所有其他日志。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-117">After startup, all additional logs are discarded.</span></span>

<span data-ttu-id="d6ee0-118">以下示例 `aspNetCore` 元素在相对路径 `.\log\` 上配置 stdout 日志记录。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-118">The following sample `aspNetCore` element configures stdout logging at the relative path `.\log\`.</span></span> <span data-ttu-id="d6ee0-119">确认应用池用户标识是否已提供写入路径的权限。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-119">Confirm that the AppPool user identity has permission to write to the path provided.</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="true"
    stdoutLogFile=".\logs\stdout"
    hostingModel="inprocess">
</aspNetCore>
```

<span data-ttu-id="d6ee0-120">发布用于 Azure 应用服务部署的应用时，Web SDK 会将 `stdoutLogFile` 值设置为 `\\?\%home%\LogFiles\stdout`。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-120">When publishing an app for Azure App Service deployment, the Web SDK sets the `stdoutLogFile` value to `\\?\%home%\LogFiles\stdout`.</span></span> <span data-ttu-id="d6ee0-121">已为 Azure 应用服务托管的应用预定义了 `%home` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-121">The `%home` environment variable is predefined for apps hosted by Azure App Service.</span></span>

<span data-ttu-id="d6ee0-122">要创建日志记录筛选器规则，请参阅 ASP.NET Core 日志记录文档的[配置](xref:fundamentals/logging/index#log-filtering)和[日志筛选](xref:fundamentals/logging/index#log-filtering)部分。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-122">To create logging filter rules, see the [Configuration](xref:fundamentals/logging/index#log-filtering) and [Log filtering](xref:fundamentals/logging/index#log-filtering) sections of the ASP.NET Core logging documentation.</span></span>

<span data-ttu-id="d6ee0-123">有关路径格式的详细信息，请参阅 [Windows 系统上的文件路径格式](/dotnet/standard/io/file-path-formats)。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-123">For more information on path formats, see [File path formats on Windows systems](/dotnet/standard/io/file-path-formats).</span></span>

## <a name="enhanced-diagnostic-logs"></a><span data-ttu-id="d6ee0-124">增强的诊断日志</span><span class="sxs-lookup"><span data-stu-id="d6ee0-124">Enhanced diagnostic logs</span></span>

<span data-ttu-id="d6ee0-125">可以配置 ASP.NET Core 模块，以提供增强的诊断日志。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-125">The ASP.NET Core Module is configurable to provide enhanced diagnostics logs.</span></span> <span data-ttu-id="d6ee0-126">将 `<handlerSettings>` 元素添加到 `web.config` 中的 `<aspNetCore>` 元素。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-126">Add the `<handlerSettings>` element to the `<aspNetCore>` element in `web.config`.</span></span> <span data-ttu-id="d6ee0-127">将 `debugLevel` 设置为 `TRACE` 将提供更准确的诊断信息：</span><span class="sxs-lookup"><span data-stu-id="d6ee0-127">Setting the `debugLevel` to `TRACE` exposes a higher fidelity of diagnostic information:</span></span>

```xml
<aspNetCore processPath="dotnet"
    arguments=".\MyApp.dll"
    stdoutLogEnabled="false"
    stdoutLogFile="\\?\%home%\LogFiles\stdout"
    hostingModel="inprocess">
  <handlerSettings>
    <handlerSetting name="debugFile" value=".\logs\aspnetcore-debug.log" />
    <handlerSetting name="debugLevel" value="FILE,TRACE" />
  </handlerSettings>
</aspNetCore>
```

<span data-ttu-id="d6ee0-128">创建日志文件时，路径中的任何文件夹（上述示例中为 `logs`）由模块创建。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-128">Any folders in the path (`logs` in the preceding example) are created by the module when the log file is created.</span></span> <span data-ttu-id="d6ee0-129">应用池必须对写入日志的位置具有写入权限（使用 `IIS AppPool\{APP POOL NAME}` 提供写入权限，其中占位符 `{APP POOL NAME}` 为应用池名称）。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-129">The app pool must have write access to the location where the logs are written (use `IIS AppPool\{APP POOL NAME}` to provide write permission, where the placeholder `{APP POOL NAME}` is the app pool name).</span></span>

<span data-ttu-id="d6ee0-130">调试级别 (`debugLevel`) 值可以同时包含级别和位置。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-130">Debug level (`debugLevel`) values can include both the level and the location.</span></span>

<span data-ttu-id="d6ee0-131">级别（详细程度由低到高）：</span><span class="sxs-lookup"><span data-stu-id="d6ee0-131">Levels (in order from least to most verbose):</span></span>

* <span data-ttu-id="d6ee0-132">错误</span><span class="sxs-lookup"><span data-stu-id="d6ee0-132">ERROR</span></span>
* <span data-ttu-id="d6ee0-133">警告</span><span class="sxs-lookup"><span data-stu-id="d6ee0-133">WARNING</span></span>
* <span data-ttu-id="d6ee0-134">INFO</span><span class="sxs-lookup"><span data-stu-id="d6ee0-134">INFO</span></span>
* <span data-ttu-id="d6ee0-135">TRACE</span><span class="sxs-lookup"><span data-stu-id="d6ee0-135">TRACE</span></span>

<span data-ttu-id="d6ee0-136">位置（允许多个位置）：</span><span class="sxs-lookup"><span data-stu-id="d6ee0-136">Locations (multiple locations are permitted):</span></span>

* <span data-ttu-id="d6ee0-137">CONSOLE</span><span class="sxs-lookup"><span data-stu-id="d6ee0-137">CONSOLE</span></span>
* <span data-ttu-id="d6ee0-138">事件日志</span><span class="sxs-lookup"><span data-stu-id="d6ee0-138">EVENTLOG</span></span>
* <span data-ttu-id="d6ee0-139">文件</span><span class="sxs-lookup"><span data-stu-id="d6ee0-139">FILE</span></span>

<span data-ttu-id="d6ee0-140">还可以通过环境变量提供处理程序设置：</span><span class="sxs-lookup"><span data-stu-id="d6ee0-140">The handler settings can also be provided via environment variables:</span></span>

* <span data-ttu-id="d6ee0-141">`ASPNETCORE_MODULE_DEBUG_FILE`：调试日志文件的路径。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-141">`ASPNETCORE_MODULE_DEBUG_FILE`: Path to the debug log file.</span></span> <span data-ttu-id="d6ee0-142">（默认值：`aspnetcore-debug.log`）</span><span class="sxs-lookup"><span data-stu-id="d6ee0-142">(Default: `aspnetcore-debug.log`)</span></span>
* <span data-ttu-id="d6ee0-143">`ASPNETCORE_MODULE_DEBUG`：调试级别设置。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-143">`ASPNETCORE_MODULE_DEBUG`: Debug level setting.</span></span>

> [!WARNING]
> <span data-ttu-id="d6ee0-144">部署中启用的调试日志记录的时间不要超出排除故障所需的时间。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-144">Do **not** leave debug logging enabled in the deployment for longer than required to troubleshoot an issue.</span></span> <span data-ttu-id="d6ee0-145">日志大小不限。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-145">The size of the log isn't limited.</span></span> <span data-ttu-id="d6ee0-146">持续启用调试日志可能耗尽可用磁盘空间并导致服务器或应用服务崩溃。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-146">Leaving the debug log enabled can exhaust the available disk space and crash the server or app service.</span></span>

<span data-ttu-id="d6ee0-147">有关 `web.config` 文件中的 `aspNetCore` 元素的示例，请参阅 [`web.config` 的 ASP.NET Core 模块配置](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig)。</span><span class="sxs-lookup"><span data-stu-id="d6ee0-147">See [Configuration of ASP.NET Core Module with `web.config`](xref:host-and-deploy/iis/web-config#configuration-of-aspnet-core-module-with-webconfig) for an example of the `aspNetCore` element in the `web.config` file.</span></span>
