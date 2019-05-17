---
title: 对 IIS 上的 ASP.NET Core 进行故障排除
author: guardrex
description: 了解如何诊断 ASP.NET Core 应用的 Internet Information Services (IIS) 部署的问题。
ms.author: riande
ms.custom: mvc
ms.date: 03/14/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: f89eac3ae6fc704bc8bf38a9707fc3c6c3568e91
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64884342"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a>对 IIS 上的 ASP.NET Core 进行故障排除

作者：[Luke Latham](https://github.com/guardrex)

本文说明了如何在使用 [Internet Information Services (IIS)](/iis) 托管时诊断 ASP.NET Core 应用启动问题。 本文提供的信息适用于在 Windows Server 和 Windows 桌面上的 IIS 中托管。

::: moniker range=">= aspnetcore-2.2"

在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。 本地调试时出现的“502.5 - 进程失败”或“500.30 - 启动失败”可以使用本主题中的建议进行故障排除。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

在 Visual Studio 中，ASP.NET Core 项目默认为在调试期间进行 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 托管。 可以使用本主题中的建议对本地调试进行故障排除时，出现“502.5 进程失败”。

::: moniker-end

其他故障排除主题：

<xref:host-and-deploy/azure-apps/troubleshoot> 虽然应用服务使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)和 IIS 托管应用，但若要获取特定于应用服务的说明，请参阅专用主题。

<xref:fundamentals/error-handling> 了解如何在本地系统上处理 ASP.NET Core 应用在开发期间的错误。

[了解如何使用 Visual Studio 进行调试](/visualstudio/debugger/getting-started-with-the-debugger)本主题介绍了 Visual Studio 调试器的功能。

[使用 Visual Studio Code 进行调试](https://code.visualstudio.com/docs/editor/debugging)了解 Visual Studio Code 中内置的调试支持。

## <a name="app-startup-errors"></a>应用启动错误

### <a name="5025-process-failure"></a>502.5 进程失败

工作进程失败。 应用不启动。

ASP.NET Core 模块尝试启动后端 dotnet 进程，但启动失败。 通常可以从“[应用程序事件日志](#application-event-log)”和“[ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)”的条目中确定进程启动失败的原因。

常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。 检查目标计算机上安装的 ASP.NET Core 共享框架版本。 共享框架是安装在计算机上并由 `Microsoft.AspNetCore.App` 等元包引用的一组程序集（.dll 文件）。 元包引用可以指定所需的最低版本。 有关详细信息，请参阅[共享框架](https://natemcmaster.com/blog/2018/08/29/netcore-primitives-2/)。

托管或应用配置错误导致工作进程失败时，将返回“502.5 进程失败”错误页面：

![显示“502.5 进程故障”页面的浏览器窗口](troubleshoot/_static/process-failure-page.png)

::: moniker range=">= aspnetcore-2.2"

### <a name="50030-in-process-startup-failure"></a>500.30 进程内启动失败

工作进程失败。 应用不启动。

ASP.NET Core 模块尝试进程内启动 .NET Core CLR，但启动失败。 通常可以从“[应用程序事件日志](#application-event-log)”和“[ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)”的条目中确定进程启动失败的原因。

常见的失败情况是，由于目标 ASP.NET Core 共享框架版本不存在，因此应用配置错误。 检查目标计算机上安装的 ASP.NET Core 共享框架版本。

### <a name="5000-in-process-handler-load-failure"></a>500.0 进程内处理程序加载失败

工作进程失败。 应用不启动。

ASP.NET Core 模块无法找到 .NET Core CLR 和进程内请求处理程序 (aspnetcorev2_inprocess.dll)。 检查：

* 该应用针对 [Microsoft.AspNetCore.Server.IIS NuGet](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.IIS) 包或 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。
* 目标计算机上安装了该应用所针对的 ASP.NET Core 共享框架版本。

### <a name="5000-out-of-process-handler-load-failure"></a>500.0 进程外处理程序加载失败

工作进程失败。 应用不启动。

ASP.NET Core 模块无法找到进程外托管请求处理程序。 请确保 aspnetcorev2.dll 旁边的子文件夹中存在 aspnetcorev2_outofprocess.dll。

::: moniker-end

### <a name="500-internal-server-error"></a>500 内部服务器错误

应用启动，但某个错误阻止了服务器完成请求。

在启动期间或在创建响应时，应用的代码内出现此错误。 响应可能不包含任何内容，或响应可能会在浏览器中显示为“500 内部服务器错误”。 应用程序事件日志通常表明应用正常启动。 从服务器的角度来看，这是正确的。 应用已启动，但无法生成有效的响应。 在服务器上[在命令提示符处运行应用](#run-the-app-at-a-command-prompt)或[启用 ASP.NET Core 模块 stdout 日志](#aspnet-core-module-stdout-log)以解决该问题。

### <a name="failed-to-start-application-errorcode-0x800700c1"></a>未能启动应用程序（错误代码“0x800700c1”）

```
EventID: 1010
Source: IIS AspNetCore Module V2
Failed to start application '/LM/W3SVC/6/ROOT/', ErrorCode '0x800700c1'.
```

应用未能启动，因为应用的程序集 (.dll) 无法加载。

当已发布的应用与 w3wp/iisexpress 进程之间的位数不匹配时，会出现此错误。

确认应用池的 32 位设置正确：

1. 在 IIS 管理器的“应用程序池”中选择应用池。
1. 在“操作”面板中的“编辑应用程序池”下选择“高级设置”。
1. 设置“启用 32 位应用程序”：
   * 如果部署 32 位 (x86) 应用，则将值设置为 `True`。
   * 如果部署 64 位 (x64) 应用，则将值设置为 `False`。

### <a name="connection-reset"></a>连接重置

如果在发送标头后出现错误，则服务器在出现错误时发送“500 内部服务器错误”已经太晚了。 通常在序列化响应的复杂对象期间出现错误时发生这种情况。 此类型的错误在客户端上显示为“连接重置”错误。 [应用程序日志记录](xref:fundamentals/logging/index)可以帮助解决这些类型的错误。

## <a name="default-startup-limits"></a>默认启动限制

ASP.NET Core 模块的默认“startupTimeLimit”配置为 120 秒。 保留默认值时，在模块记录进程故障之前，可能最多需要两分钟来启动应用。 有关配置模块的信息，请参阅 [aspNetCore 元素的属性](xref:host-and-deploy/aspnet-core-module#attributes-of-the-aspnetcore-element)。

## <a name="troubleshoot-app-startup-errors"></a>解决应用启动错误

### <a name="enable-the-aspnet-core-module-debug-log"></a>启用 ASP.NET Core 模块调试日志

将以下处理程序设置添加到应用的 web.config 文件以启用 ASP.NET Core 模块调试日志：

```xml
<aspNetCore ...>
  <handlerSettings>
    <handlerSetting name="debugLevel" value="file" />
    <handlerSetting name="debugFile" value="c:\temp\ancm.log" />
  </handlerSettings>
</aspNetCore>
```

确认为日志指定的路径存在，并且应用池的标识具有该位置的写入权限。

### <a name="application-event-log"></a>应用程序事件日志

访问应用程序事件日志：

1. 打开“开始”菜单，搜索“事件查看器”，然后选择“事件查看器”应用。
1. 在“事件查看器”中，打开“Windows 日志”节点。
1. 选择“应用程序”以打开应用程序事件日志。
1. 搜索与失败应用相关联的错误。 错误具有“源”列中“IIS AspNetCore 模块”或“IIS Express AspNetCore 模块”的值。

### <a name="run-the-app-at-a-command-prompt"></a>在命令提示符处运行应用

许多启动错误未在应用程序事件日志中生成有用信息。 可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。

#### <a name="framework-dependent-deployment"></a>依赖框架的部署

如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：

1. 在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe 执行应用的程序集来运行应用。 在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。
1. 来自应用且显示任何错误的控制台输出将写入控制台窗口。
1. 如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。 如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。 如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。

#### <a name="self-contained-deployment"></a>独立部署

如果应用是[独立部署](/dotnet/core/deploying/#self-contained-deployments-scd)：

1. 在命令提示符处，导航到部署文件夹并运行应用的可执行文件。 在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`<assembly_name>.exe`。
1. 来自应用且显示任何错误的控制台输出将写入控制台窗口。
1. 如果向应用发出请求时出现错误，请向 Kestrel 侦听所在的主机和端口发出请求。 如果使用默认主机和端口，请向 `http://localhost:5000/` 发出请求。 如果应用在 Kestrel 终结点地址处正常响应，则问题更可能与承载配置相关，而不太可能在于应用。

### <a name="aspnet-core-module-stdout-log"></a>ASP.NET Core 模块 stdout 日志

若要启用和查看 stdout 日志，请执行以下操作：

1. 在托管系统上导航到站点的部署文件夹。
1. 如果 logs 文件夹不存在，请创建该文件夹。 有关如何启用 MSBuild 以在部署中自动创建 logs 文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。
1. 编辑 web.config 文件。 将“stdoutLogEnabled”设置为 `true` 并更改“stdoutLogFile”路径以指向 logs 文件夹（例如，`.\logs\stdout`）。 路径中的 `stdout` 是日志文件名的前缀。 创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。 如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”。
1. 请确保应用程序池的标识具有对日志文件夹的写入权限。
1. 保存已更新的 web.config 文件。
1. 向应用发出请求。
1. 导航到 logs 文件夹。 查找并打开最新的 stdout 日志。
1. 研究日志以查找错误。

> [!IMPORTANT]
> 故障排除完成后，禁用 stdout 日志记录。

1. 编辑 web.config 文件。
1. 将“stdoutLogEnabled”设置为 `false`。
1. 保存该文件。

> [!WARNING]
> 无法禁用 stdout 日志可能会导致应用或服务器出现故障。 日志文件大小或创建的日志文件数没有限制。
>
> 对于 ASP.NET Core 应用中的例程日志记录，使用限制日志文件大小和旋转日志的日志记录库。 有关详细信息，请参阅[第三方日志记录提供程序](xref:fundamentals/logging/index#third-party-logging-providers)。

## <a name="enable-the-developer-exception-page"></a>启用开发人员异常页面

`ASPNETCORE_ENVIRONMENT` [环境变量可以添加到 web.config](xref:host-and-deploy/aspnet-core-module#setting-environment-variables) 以在开发环境中运行应用。 只要在应用启动时环境不被主机生成器上的 `UseEnvironment` 重写，设置环境变量就会在运行应用时显示“[开发人员异常页面](xref:fundamentals/error-handling)”。

::: moniker range=">= aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout"
      hostingModel="InProcess">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```xml
<aspNetCore processPath="dotnet"
      arguments=".\MyApp.dll"
      stdoutLogEnabled="false"
      stdoutLogFile=".\logs\stdout">
  <environmentVariables>
    <environmentVariable name="ASPNETCORE_ENVIRONMENT" value="Development" />
  </environmentVariables>
</aspNetCore>
```

::: moniker-end

仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。 在故障排除后从 web.config 文件中删除环境变量。 有关设置 web.config 中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。

## <a name="common-startup-errors"></a>常见启动错误

请参阅 <xref:host-and-deploy/azure-iis-errors-reference>。 参考主题介绍了阻止应用启动的大部分常见问题。

## <a name="obtain-data-from-an-app"></a>从应用中获取数据

如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。 有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。

## <a name="create-a-dump"></a>创建转储

转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因。

### <a name="app-crashes-or-encounters-an-exception"></a>应用崩溃或引发异常

从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：

1. 创建文件夹，将崩溃转储文件保存在 `c:\dumps`。 应用池必须对该文件夹具有写权限。
1. 运行 [EnableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1)：
   * 如果应用使用[进程内托管模型](xref:fundamentals/servers/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * 如果应用使用[进程外托管模型](xref:fundamentals/servers/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. 在造成崩溃的条件下运行应用。
1. 出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1)：
   * 如果应用使用[进程内托管模型](xref:fundamentals/servers/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本：

     ```console
     .\DisableDumps w3wp.exe
     ```

   * 如果应用使用[进程外托管模型](xref:fundamentals/servers/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本：

     ```console
     .\DisableDumps dotnet.exe
     ```

在应用崩溃并完成转储收集后，即可正常终止应用。 PowerShell 脚本会 WER 来按应用收集转储（最多收集 5 个）。

> [!WARNING]
> 崩溃转储可能会占用大量磁盘空间（每个最多占用数 GB）。

### <a name="app-hangs-fails-during-startup-or-runs-normally"></a>应用挂起、在启动期间失败或正常运行

如果应用挂起（停止响应但不崩溃）、在启动期间失败或者正常运行*hangs*，请参阅[用户模式转储文件：选择最佳工具](/windows-hardware/drivers/debugger/user-mode-dump-files#choosing-the-best-tool)，以选择适合用于生成转储的工具。

### <a name="analyze-the-dump"></a>分析转储

可采用几种方法来分析转储。 有关详细信息，请参阅[分析用户模式转储文件](/windows-hardware/drivers/debugger/analyzing-a-user-mode-dump-file)。

## <a name="remote-debugging"></a>远程调试

请参阅 Visual Studio 文档中的[在 Visual Studio 2017 中远程调试远程 IIS 计算机上的 ASP.NET Core](/visualstudio/debugger/remote-debugging-aspnet-on-a-remote-iis-computer)。

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/) 提供来自 IIS 托管的应用的遥测，包括错误日志记录和报告功能。 当应用的日志记录功能变得可用时，Application Insights 只能报告应用启动后出现的错误。 有关详细信息，请参阅[用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。

## <a name="additional-advice"></a>其他建议

有时，正常运行的应用在开发计算机上升级 .NET Core SDK 或在应用内升级包版本后立即出现故障。 在某些情况下，不同的包可能在执行主要升级时中断应用。 可以按照以下说明来修复其中大部分问题：

1. 删除 bin 和 obj 文件夹。
1. 清除 %UserProfile%\\.nuget\\packages 和 %LocalAppData%\\Nuget\\v3-cache 中的包缓存。
1. 还原并重新生成项目。
1. 确认在重新部署应用之前已完全删除服务器上的先前部署。

> [!TIP]
> 清除包缓存的简便方法是从命令提示符执行 `dotnet nuget locals all --clear`。
>
> 清除包缓存还可通过使用 [nuget.exe](https://www.nuget.org/downloads) 工具并执行命令 `nuget locals all -clear` 来完成。 *nuget.exe* 不是与 Windows 桌面操作系统的捆绑安装，必须从 [NuGet 网站](https://www.nuget.org/downloads)中单独获取。

## <a name="additional-resources"></a>其他资源

* <xref:test/troubleshoot>
* <xref:fundamentals/error-handling>
* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/aspnet-core-module>
* <xref:host-and-deploy/azure-apps/troubleshoot>
