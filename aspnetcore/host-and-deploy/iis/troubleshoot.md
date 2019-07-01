---
title: 对 IIS 上的 ASP.NET Core 进行故障排除
author: guardrex
description: 了解如何诊断 ASP.NET Core 应用的 Internet Information Services (IIS) 部署的问题。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/19/2019
uid: host-and-deploy/iis/troubleshoot
ms.openlocfilehash: 4df370dd9b1a5a651bcf767b8b9ace4220bdc345
ms.sourcegitcommit: 9f11685382eb1f4dd0fb694dea797adacedf9e20
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2019
ms.locfileid: "67313655"
---
# <a name="troubleshoot-aspnet-core-on-iis"></a>对 IIS 上的 ASP.NET Core 进行故障排除

作者：[Luke Latham](https://github.com/guardrex)

本文说明了如何在使用 [Internet Information Services (IIS)](/iis) 托管时诊断 ASP.NET Core 应用启动问题。 本文提供的信息适用于在 Windows Server 和 Windows 桌面上的 IIS 中托管。

其他故障排除主题：

* Azure 应用服务还使用 [ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)和 IIS 来托管应用。 有关专门针对 Azure 应用服务提出的疑难解答建议，请参阅 <xref:host-and-deploy/azure-apps/troubleshoot>。
* <xref:fundamentals/error-handling> 介绍了如何在本地系统上进行开发期间处理 ASP.NET Core 应用中的错误。
* [了解如何使用 Visual Studio 进行调试](/visualstudio/debugger/getting-started-with-the-debugger)介绍了 Visual Studio 调试器的功能。
* [使用 Visual Studio Code 进行调试](https://code.visualstudio.com/docs/editor/debugging)介绍了 Visual Studio Code 中内置的调试支持。

[!INCLUDE[](~/includes/azure-iis-startup-errors.md)]

## <a name="troubleshoot-app-startup-errors"></a>解决应用启动错误

### <a name="enable-the-aspnet-core-module-debug-log"></a>启用 ASP.NET Core 模块调试日志

将以下处理程序设置添加到应用的 web.config  文件以启用 ASP.NET Core 模块调试日志：

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

1. 打开“开始”菜单，搜索“事件查看器”  ，然后选择“事件查看器”  应用。
1. 在“事件查看器”  中，打开“Windows 日志”  节点。
1. 选择“应用程序”  以打开应用程序事件日志。
1. 搜索与失败应用相关联的错误。 错误具有“源”  列中“IIS AspNetCore 模块”  或“IIS Express AspNetCore 模块”  的值。

### <a name="run-the-app-at-a-command-prompt"></a>在命令提示符处运行应用

许多启动错误未在应用程序事件日志中生成有用信息。 可以通过在托管系统上在命令提示符处运行应用来找到某些错误的原因。

#### <a name="framework-dependent-deployment"></a>依赖框架的部署

如果应用是[依赖框架的部署](/dotnet/core/deploying/#framework-dependent-deployments-fdd)：

1. 在命令提示符处，导航到部署文件夹并通过使用 dotnet.exe  执行应用的程序集来运行应用。 在以下命令中，替换 \<assembly_name> 的应用程序集的名称：`dotnet .\<assembly_name>.dll`。
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
1. 如果 logs  文件夹不存在，请创建该文件夹。 有关如何启用 MSBuild 以在部署中自动创建 logs  文件夹的说明，请参阅[目录结构](xref:host-and-deploy/directory-structure)主题。
1. 编辑 web.config  文件。 将“stdoutLogEnabled”  设置为 `true` 并更改“stdoutLogFile”  路径以指向 logs  文件夹（例如，`.\logs\stdout`）。 路径中的 `stdout` 是日志文件名的前缀。 创建日志时，将自动添加时间戳、进程 ID 和文件扩展名。 如果将 `stdout` 用作文件名的前缀，典型的日志文件将命名为“stdout_20180205184032_5412.log”  。
1. 请确保应用程序池的标识具有对日志文件夹的写入权限  。
1. 保存已更新的 web.config  文件。
1. 向应用发出请求。
1. 导航到 logs  文件夹。 查找并打开最新的 stdout 日志。
1. 研究日志以查找错误。

> [!IMPORTANT]
> 故障排除完成后，禁用 stdout 日志记录。

1. 编辑 web.config  文件。
1. 将“stdoutLogEnabled”  设置为 `false`。
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

仅建议在未向 Internet 公开的暂存服务器和测试服务器上设置 `ASPNETCORE_ENVIRONMENT` 的环境变量。 在故障排除后从 web.config  文件中删除环境变量。 有关设置 web.config  中的环境变量的信息，请参阅 [aspNetCore 的 environmentVariables 子元素](xref:host-and-deploy/aspnet-core-module#setting-environment-variables)。

## <a name="common-startup-errors"></a>常见启动错误

请参阅 <xref:host-and-deploy/azure-iis-errors-reference>。 参考主题介绍了阻止应用启动的大部分常见问题。

## <a name="obtain-data-from-an-app"></a>从应用中获取数据

如果应用能够响应请求，则使用终端内联中间件从应用中获取请求、连接和其他数据。 有关详细信息和示例代码，请参阅<xref:test/troubleshoot#obtain-data-from-an-app>。

## <a name="create-a-dump"></a>创建转储

转储是系统内存的一个快照，可帮助确定应用崩溃、启动故障或应用速度缓慢等状况的原因  。

### <a name="app-crashes-or-encounters-an-exception"></a>应用崩溃或引发异常

从 [Windows 错误报告 (WER)](/windows/desktop/wer/windows-error-reporting) 中获取转储并进行分析：

1. 创建文件夹，将崩溃转储文件保存在 `c:\dumps`。 应用池必须对该文件夹具有写权限。
1. 运行 [EnableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/EnableDumps.ps1)：
   * 如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：

     ```console
     .\EnableDumps w3wp.exe c:\dumps
     ```

   * 如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：

     ```console
     .\EnableDumps dotnet.exe c:\dumps
     ```

1. 在造成崩溃的条件下运行应用。
1. 出现崩溃后，运行 [DisableDumps PowerShell 脚本](https://github.com/aspnet/AspNetCore.Docs/blob/master/aspnetcore/host-and-deploy/iis/troubleshoot/scripts/DisableDumps.ps1)：
   * 如果应用使用[进程内托管模型](xref:host-and-deploy/iis/index#in-process-hosting-model)，则请为 w3wp.exe 运行脚本  ：

     ```console
     .\DisableDumps w3wp.exe
     ```

   * 如果应用使用[进程外托管模型](xref:host-and-deploy/iis/index#out-of-process-hosting-model)，则请为 dotnet.exe 运行脚本  ：

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

1. 删除 bin  和 obj  文件夹。
1. 清除 %UserProfile%\\.nuget\\packages  和 %LocalAppData%\\Nuget\\v3-cache  中的包缓存。
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
