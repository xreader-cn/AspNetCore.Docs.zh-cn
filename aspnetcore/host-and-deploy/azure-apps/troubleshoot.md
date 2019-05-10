---
title: 对 Azure 应用服务上的 ASP.NET Core 进行故障排除
author: guardrex
description: 了解如何诊断 ASP.NET Core Azure 应用服务部署问题。
ms.author: riande
ms.custom: mvc
ms.date: 03/06/2019
uid: host-and-deploy/azure-apps/troubleshoot
ms.openlocfilehash: 36c2bdfa585a0fd54ca93bf4c0edb4cf6f7d934a
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64886902"
---
# <a name="troubleshoot-aspnet-core-on-azure-app-service"></a>对 Azure 应用服务上的 ASP.NET Core 进行故障排除

作者：[Luke Latham](https://github.com/guardrex)

[!INCLUDE [Azure App Service Preview Notice](../../includes/azure-apps-preview-notice.md)]

本文说明了如何使用 Azure 应用服务的诊断工具诊断 ASP.NET Core 应用启动问题。 有关其他故障排除建议，请参阅 Azure 文档中的 [Azure 应用服务诊断概述](/azure/app-service/app-service-diagnostics)和[如何：在 Azure 应用服务中监视应用](/azure/app-service/web-sites-monitor)。

## <a name="app-startup-errors"></a>应用启动错误

**502.5 进程故障**工作进程失败。 应用不启动。

[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)尝试启动工作进程，但启动失败。 检查应用程序事件日志通常可帮助解决此类型的问题。 [应用程序事件日志](#application-event-log)部分中介绍了访问日志。

配置错误的应用导致工作进程失败时，将返回“502.5 进程故障”错误页面：

![显示“502.5 进程故障”页面的浏览器窗口](troubleshoot/_static/process-failure-page.png)

**500 内部服务器错误**

应用启动，但某个错误阻止了服务器完成请求。

在启动期间或在创建响应时，应用的代码内出现此错误。 响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”。 应用程序事件日志通常表明应用正常启动。 从服务器的角度来看，这是正确的。 应用已启动，但无法生成有效的响应。 [在 Kudu 控制台中运行应用](#run-the-app-in-the-kudu-console)或[启用 ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)以解决该问题。

**连接重置**

如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”已经太晚了。 通常在序列化响应的复杂对象期间出现错误时发生这种情况。 此类型的错误在客户端上显示为“连接重置”错误。 [应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。

## <a name="default-startup-limits"></a>默认启动限制

ASP.NET Core 模块的默认“startupTimeLimit”配置为 120 秒。 保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。 有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。

## <a name="troubleshoot-app-startup-errors"></a>解决应用启动错误

### <a name="application-event-log"></a>应用程序事件日志

若要访问应用程序事件日志，请在 Azure 门户中使用“诊断并解决问题”边栏选项卡：

1. 在 Azure 门户中打开“应用服务”中的应用。
1. 选择“诊断并解决问题”。
1. 选择“诊断工具”标题。
1. 在“支持工具”下，选择“应用程序事件”按钮。
1. 检查“源”列中由 IIS AspNetCoreModule 或 IIS AspNetCoreModule V2 条目提供的最新错误。

使用“诊断并解决问题”边栏选项卡的替代方法是直接使用 [Kudu](https://github.com/projectkudu/kudu/wiki) 检查应用程序事件日志文件：

1. 打开“开发工具”区域中的“高级工具”。 选择“转到&rarr;”按钮。 此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。
1. 使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。
1. 打开 LogFiles 文件夹。
1. 选择 eventlog.xml 文件旁边的铅笔图标。
1. 检查日志。 滚动到日志底部以查看最新事件。

### <a name="run-the-app-in-the-kudu-console"></a>在 Kudu 控制台中运行应用

许多启动错误未在应用程序事件日志中生成有用信息。 可以在 [Kudu](https://github.com/projectkudu/kudu/wiki) 远程执行控制台中运行应用以发现错误：

1. 打开“开发工具”区域中的“高级工具”。 选择“转到&rarr;”按钮。 此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。
1. 使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。

#### <a name="test-a-32-bit-x86-app"></a>测试 32 位 (x86) 应用

##### <a name="current-release"></a>当前版本

1. `cd d:\home\site\wwwroot`
1. 运行应用：
   * 如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：

     ```console
     dotnet .\{ASSEMBLY NAME}.dll
     ```

   * 如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：

     ```console
     {ASSEMBLY NAME}.exe
     ```

来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>在预览版上运行的依赖框架的部署

必须安装 ASP.NET Core {VERSION} (x86) 运行时站点扩展。

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x32`（`{X.Y}` 是运行时版本）
1. 运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。

#### <a name="test-a-64-bit-x64-app"></a>测试 64 位 (x64) 应用

##### <a name="current-release"></a>当前版本

* 如果应用是 64 位 (x64) [依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：
  1. `cd D:\Program Files\dotnet`
  1. 运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`
* 如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：
  1. `cd D:\home\site\wwwroot`
  1. 运行应用：`{ASSEMBLY NAME}.exe`

来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。

##### <a name="framework-dependent-deployment-running-on-a-preview-release"></a>在预览版上运行的依赖框架的部署

必须安装 ASP.NET Core {VERSION} (x64) 运行时站点扩展。

1. `cd D:\home\SiteExtensions\AspNetCoreRuntime.{X.Y}.x64`（`{X.Y}` 是运行时版本）
1. 运行应用：`dotnet \home\site\wwwroot\{ASSEMBLY NAME}.dll`

来自应用且显示任何错误的控制台输出将传送到 Kudu 控制台。

### <a name="aspnet-core-module-stdout-log"></a>ASP.NET Core 模块 stdout 日志

ASP.NET Core 模块 stdout 日志通常记录应用程序事件日志中找不到的有用错误消息。 若要启用和查看 stdout 日志，请执行以下操作：

1. 在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。
1. 在“选择问题类别”下，选择“Web 应用关闭”按钮。
1. 在“建议的解决方案” > “启用 Stdout 日志重定向”下，选择“打开 Kudu 控制台以编辑 Web.Config”对应的按钮。
1. 在 Kudu 诊断控制台中，打开路径“站点 > wwwroot”下的文件夹。 向下滚动以在列表底部显示“web.config”文件。
1. 单击“web.config”文件旁边的铅笔图标。
1. 将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。
1. 选择“保存”以保存已更新的 web.config 文件。
1. 向应用发出请求。
1. 返回到 Azure 门户。 选择“开发工具”区域中的“高级工具”边栏选项卡。 选择“转到&rarr;”按钮。 此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。
1. 使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。
1. 选择“LogFiles”文件夹。
1. 检查“已修改”列并选择铅笔图标以编辑具有最新修改日期的 stdout 日志。
1. 打开日志文件后，将显示错误。

故障排除完成后，禁用 stdout 日志记录：

1. 在 Kudu 诊断控制台中，返回到路径“site > wwwroot”以显示 web.config 文件。 通过选择铅笔图标再次打开 web.config 文件。
1. 将“stdoutLogEnabled”设置为 `false`。
1. 选择“保存”以保存文件。

> [!WARNING]
> 无法禁用 stdout 日志可能会导致应用或服务器出现故障。 日志文件大小或创建的日志文件数没有限制。 仅使用 stdout 日志记录来解决应用启动问题。
>
> 对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。 有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。

::: moniker range=">= aspnetcore-2.2"

### <a name="aspnet-core-module-debug-log"></a>ASP.NET Core 模块调试日志

ASP.NET Core 模块调试日志从 ASP.NET Core 模块提供了更多、更详细的日志记录。 若要启用和查看 stdout 日志，请执行以下操作：

1. 要启用增强的诊断日志，请执行以下任一操作：
   * 按照[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中的说明配置应用以获取增强的诊断日志记录。 重新部署应用。
   * 使用 Kudu 控制台将[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中显示的 `<handlerSettings>` 添加到动态应用的 web.config 文件中：
     1. 打开“开发工具”区域中的“高级工具”。 选择“转到&rarr;”按钮。 此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。
     1. 使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。
     1. 打开路径“site > wwwroot”下的文件夹。 通过选择铅笔按钮编辑 web.config 文件。 添加 `<handlerSettings>` 部分（如[增强的诊断日志](xref:host-and-deploy/aspnet-core-module#enhanced-diagnostic-logs)中所示）。 选择“保存”按钮。
1. 打开“开发工具”区域中的“高级工具”。 选择“转到&rarr;”按钮。 此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。
1. 使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。
1. 打开路径“site > wwwroot”下的文件夹。 如果没有为 aspnetcore-debug.log 文件提供路径，则该文件将显示在列表中。 如果提供了路径，请导航到日志文件的位置。
1. 使用文件名旁边的铅笔按钮打开日志文件。

故障排除完成后，禁用调试日志记录：

1. 要禁用增强的调试日志，请执行以下任一操作：
   * 从本地删除 web.config 文件中的 `<handlerSettings>` 并重新部署该应用。
   * 使用 Kudu 控制台编辑 web.config 文件并删除 `<handlerSettings>` 部分。 保存该文件。

> [!WARNING]
> 无法禁用调试日志可能会导致应用或服务器出现故障。 日志文件大小没有任何限制。 仅使用调试日志记录来解决应用启动问题。
>
> 对于在 ASP.NET Core 应用启动后生成的常规日志记录，使用限制日志文件大小和旋转日志的日志记录库。 有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。

::: moniker-end

## <a name="common-startup-errors"></a>常见启动错误

请参阅 <xref:host-and-deploy/azure-iis-errors-reference>。 参考主题介绍了阻止应用启动的大部分常见问题。

## <a name="slow-or-hanging-app"></a>应用缓慢或挂起

如果应用程序响应缓慢或在有请求时挂起，请参阅以下文章：

* [解决 Azure 应用服务中 Web 应用性能缓慢的问题](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [使用故障诊断程序站点扩展来捕获 Azure Web 应用程序上间歇性异常问题或性能问题的转储](https://blogs.msdn.microsoft.com/asiatech/2015/12/28/use-crash-diagnoser-site-extension-to-capture-dump-for-intermittent-exception-issues-or-performance-issues-on-azure-web-app/)

## <a name="remote-debugging"></a>远程调试

请参见下面的主题：

* [使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除的远程调试 Web 应用部分](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)（Azure 文档）
* [在 Visual Studio 2017 的 Azure 中的 IIS 上远程调试 ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)（Visual Studio 文档）

## <a name="application-insights"></a>Application Insights

[Application Insights](https://azure.microsoft.com/services/application-insights/) 提供来自 Azure 应用服务中托管的应用的遥测，包括错误日志记录和报告功能。 当应用的日志记录功能变得可用时，Application Insights 只能报告应用启动后出现的错误。 有关详细信息，请参阅[用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。

## <a name="monitoring-blades"></a>监视边栏选项卡

监视边栏选项卡提供了本主题前面所述的方法的替代故障排除体验。 这些边栏选项卡可用于诊断 500 系列错误。

确认是否已安装 ASP.NET Core 扩展。 如果未安装扩展，请手动进行安装：

1. 在“开发工具”边栏选项卡部分中，选择“扩展”边栏选项卡。
1. “ASP.NET Core 扩展”应显示在列表中。
1. 如果未安装扩展，请选择“添加”按钮。
1. 从列表中选择“ASP.NET Core 扩展”。
1. 选择“确定”以接受法律条款。
1. 选择“添加扩展”边栏选项卡上的“确定”。
1. 信息性弹出消息指示成功安装扩展的时间。

如果未启用 stdout 日志记录，请执行以下步骤：

1. 在 Azure 门户中，选择“开发工具”区域中的“高级工具”边栏选项卡。 选择“转到&rarr;”按钮。 此时将在新的浏览器选项卡或窗口中打开 Kudu 控制台。
1. 使用页面顶部的导航栏，打开“调试控制台”并选择“CMD”。
1. 打开路径“site > wwwroot”下的文件夹，然后向下滚动以显示列表底部的 web.config 文件。
1. 单击“web.config”文件旁边的铅笔图标。
1. 将“stdoutLogEnabled”设置为 `true`，并将“stdoutLogFile”路径更改为 `\\?\%home%\LogFiles\stdout`。
1. 选择“保存”以保存已更新的 web.config 文件。

继续激活诊断日志记录：

1. 在 Azure 门户中，选择“诊断日志”边栏选项卡。
1. 选择“应用程序日志记录(文件系统)”和“详细错误消息”的“开”开关。 选择边栏选项卡顶部的“保存”按钮。
1. 若要包含失败请求跟踪（也称为失败请求事件缓冲 (FREB) 日志记录），请选择“失败请求跟踪”的“开”开关。
1. 选择“日志流”边栏选项卡，将在门户中的“诊断日志”边栏选项卡下立即列出。
1. 向应用发出请求。
1. 在日志流数据中，指示了错误的原因。

故障排除完成后，请务必禁用 stdout 日志记录。 请参阅 [ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)部分中的说明。

若要查看失败请求跟踪日志（FREB 日志），请执行以下操作：

1. 在 Azure 门户中导航到“诊断并解决问题”边栏选项卡。
1. 从侧栏的“支持工具”区域中选择“失败请求跟踪日志”。

有关详细信息，请参阅[“在 Azure 应用服务中启用 Web 应用的诊断日志记录”主题的“失败请求跟踪”部分](/azure/app-service/web-sites-enable-diagnostic-log#failed-request-traces)和 [Azure 中的 Web 应用的应用程序性能常见问题：如何打开失败请求跟踪？](/azure/app-service/app-service-web-availability-performance-application-issues-faq#how-do-i-turn-on-failed-request-tracing)。

有关详细信息，请参阅[在 Azure 应用服务中启用 Web 应用的诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)。

> [!WARNING]
> 无法禁用 stdout 日志可能会导致应用或服务器出现故障。 日志文件大小或创建的日志文件数没有限制。
>
> 对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。 有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [解决 Azure Web 应用中的“502 错误的网关”和“503 服务不可用”HTTP 错误](/azure/app-service/app-service-web-troubleshoot-http-502-http-503)
* [解决 Azure 应用服务中 Web 应用性能缓慢的问题](/azure/app-service/app-service-web-troubleshoot-performance-degradation)
* [Azure 中的 Web 应用的应用程序性能常见问题](/azure/app-service/app-service-web-availability-performance-application-issues-faq)
* [Azure Web 应用沙盒（应用服务运行时执行限制）](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox)
* [Azure Friday：Azure 应用服务诊断和疑难解答体验（12 分钟视频）](https://channel9.msdn.com/Shows/Azure-Friday/Azure-App-Service-Diagnostic-and-Troubleshooting-Experience)
