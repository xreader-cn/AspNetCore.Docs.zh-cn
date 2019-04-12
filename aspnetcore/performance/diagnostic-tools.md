---
title: 性能诊断工具
author: mjrousos
description: 诊断 ASP.NET Core 应用中的性能问题的有用工具。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/diagnostic-tools
ms.openlocfilehash: 66676b5a2b95b87bfbbd50022e279e35a12b9793
ms.sourcegitcommit: 9b7fcb4ce00a3a32e153a080ebfaae4ef417aafa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/12/2019
ms.locfileid: "59516217"
---
# <a name="performance-diagnostic-tools"></a>性能诊断工具

通过[Mike Rousos](https://github.com/mjrousos)

本文列出了诊断 ASP.NET Core 中的性能问题的工具。

## <a name="visual-studio-diagnostic-tools"></a>Visual Studio 诊断工具

[分析和诊断工具](/visualstudio/profiling)内置到 Visual Studio 是开始调查性能问题的好时机。 这些工具是功能强大且方便地从 Visual Studio 开发环境使用。 此工具允许在 ASP.NET Core 应用中 CPU 使用情况、 内存使用情况和性能事件的分析。 正在内置，可以在开发时轻松分析。

中提供了详细信息[Visual Studio 文档](/visualstudio/profiling/profiling-overview)。

## <a name="application-insights"></a>Application Insights

[Application Insights](/azure/application-insights/app-insights-overview)为您的应用程序提供深入的性能数据。 Application Insights 自动响应率、 故障率、 依赖项响应时间和的详细信息上收集数据。 Application Insights 支持自定义事件和特定的指标记录到您的应用程序。

Azure Application Insights 提供了多种方式对受监视的应用程序提供见解：

- [应用程序映射](/azure/application-insights/app-insights-app-map)– 有助于跨分布式应用程序的所有组件的发现的性能瓶颈或失败的热点。
- [Azure 指标资源管理器](/azure/azure-monitor/platform/metrics-getting-started)是允许绘制图表，直观地关联趋势，在 Microsoft Azure 门户的组件和调查的峰值和低谷指标值。
- [在 Application Insights 门户中的性能边栏选项卡](/azure/application-insights/app-insights-tutorial-performance):

  - 受监视的应用程序中显示不同的操作的性能详细信息。
  - 允许钻取到单个操作来检查所有部件/依赖项也会影响持续时间长。
  - 可以从此处可收集性能跟踪根据调用 Profiler。

- [Azure Application Insights Profiler](/azure/azure-monitor/app/profiler)允许常规和按需分析的.NET 应用程序。  Azure 门户会显示捕获的调用堆栈和热路径具有的性能跟踪。 此外可以使用 PerfView 的更深入分析下载跟踪文件。

可以在各种环境中使用 application Insights:

- 优化，可在 Azure 中。
- 生产、 开发和过渡环境中的工作原理。
- 可从在本地工作[Visual Studio](/azure/application-insights/app-insights-visual-studio)或其他托管环境中。

有关详细信息，请参阅[用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。

## <a name="perfview"></a>PerfView

[PerfView](https://github.com/Microsoft/perfview)是性能分析工具创建的.NET 团队专门为诊断.NET 性能问题。 PerfView 允许分析 CPU 使用情况、 内存和 GC 行为、 性能事件和时钟时间。

您可以了解有关 PerfView 以及如何入门的详细信息[PerfView 视频教程](http://channel9.msdn.com/Series/PerfView-Tutorial)或通过读取用户的指南，可以在工具或[GitHub 上](https://github.com/Microsoft/perfview)。

## <a name="windows-performance-toolkit"></a>Windows 性能工具包

[Windows 性能工具包](/windows-hardware/test/wpt/)(WPT) 包含两个组件：Windows 性能记录器 (WPR) 和 Windows 性能分析器 (WPA)。 这些工具生成 Windows 操作系统和应用程序的深入的性能配置的文件。 WPT 具有更多方式可视化数据，但收集其数据比 PerfView 的功能较弱。

## <a name="perfcollect"></a>PerfCollect

PerfView 时.NET 方案一有用的性能分析工具，它仅在上运行 Windows 因此不能使用它来从 ASP.NET Core 应用在 Linux 环境中运行收集跟踪。

[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)是使用分析工具的本机 Linux 的 bash 脚本 ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page)并[LTTng](https://lttng.org/)) 以 PerfView 可以分析在 Linux 上收集跟踪。 在其中不能直接使用 PerfView 的 Linux 环境中出现的性能问题时，PerfCollect 非常有用。 相反，PerfCollect 可以收集跟踪然后分析的.NET Core 应用中使用 PerfView 的 Windows 计算机上。

提供了有关如何安装和开始使用 PerfCollect 的详细信息[GitHub 上](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)。

## <a name="other-third-party-performance-tools"></a>其他第三方性能工具

下面列出了一些非常有用的.NET Core 应用程序的性能调查中的第三方性能工具。

- [MiniProfiler](https://miniprofiler.com/)
- dotTrace 和来自 JetBrains dotMemory
- 从 Intel VTune
