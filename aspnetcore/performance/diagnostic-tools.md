---
title: 性能诊断工具
author: mjrousos
description: 诊断 ASP.NET Core 应用中的性能问题的有用工具。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
uid: performance/diagnostic-tools
ms.openlocfilehash: 66676b5a2b95b87bfbbd50022e279e35a12b9793
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59516217"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="9a3bb-103">性能诊断工具</span><span class="sxs-lookup"><span data-stu-id="9a3bb-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="9a3bb-104">通过[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="9a3bb-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="9a3bb-105">本文列出了诊断 ASP.NET Core 中的性能问题的工具。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="9a3bb-106">Visual Studio 诊断工具</span><span class="sxs-lookup"><span data-stu-id="9a3bb-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="9a3bb-107">[分析和诊断工具](/visualstudio/profiling)内置到 Visual Studio 是开始调查性能问题的好时机。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="9a3bb-108">这些工具是功能强大且方便地从 Visual Studio 开发环境使用。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="9a3bb-109">此工具允许在 ASP.NET Core 应用中 CPU 使用情况、 内存使用情况和性能事件的分析。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="9a3bb-110">正在内置，可以在开发时轻松分析。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="9a3bb-111">中提供了详细信息[Visual Studio 文档](/visualstudio/profiling/profiling-overview)。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="9a3bb-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="9a3bb-112">Application Insights</span></span>

<span data-ttu-id="9a3bb-113">[Application Insights](/azure/application-insights/app-insights-overview)为您的应用程序提供深入的性能数据。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="9a3bb-114">Application Insights 自动响应率、 故障率、 依赖项响应时间和的详细信息上收集数据。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="9a3bb-115">Application Insights 支持自定义事件和特定的指标记录到您的应用程序。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="9a3bb-116">Azure Application Insights 提供了多种方式对受监视的应用程序提供见解：</span><span class="sxs-lookup"><span data-stu-id="9a3bb-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="9a3bb-117">[应用程序映射](/azure/application-insights/app-insights-app-map)– 有助于跨分布式应用程序的所有组件的发现的性能瓶颈或失败的热点。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="9a3bb-118">[Azure 指标资源管理器](/azure/azure-monitor/platform/metrics-getting-started)是允许绘制图表，直观地关联趋势，在 Microsoft Azure 门户的组件和调查的峰值和低谷指标值。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="9a3bb-119">[在 Application Insights 门户中的性能边栏选项卡](/azure/application-insights/app-insights-tutorial-performance):</span><span class="sxs-lookup"><span data-stu-id="9a3bb-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="9a3bb-120">受监视的应用程序中显示不同的操作的性能详细信息。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="9a3bb-121">允许钻取到单个操作来检查所有部件/依赖项也会影响持续时间长。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="9a3bb-122">可以从此处可收集性能跟踪根据调用 Profiler。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="9a3bb-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler)允许常规和按需分析的.NET 应用程序。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="9a3bb-124">Azure 门户会显示捕获的调用堆栈和热路径具有的性能跟踪。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="9a3bb-125">此外可以使用 PerfView 的更深入分析下载跟踪文件。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="9a3bb-126">可以在各种环境中使用 application Insights:</span><span class="sxs-lookup"><span data-stu-id="9a3bb-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="9a3bb-127">优化，可在 Azure 中。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="9a3bb-128">生产、 开发和过渡环境中的工作原理。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="9a3bb-129">可从在本地工作[Visual Studio](/azure/application-insights/app-insights-visual-studio)或其他托管环境中。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="9a3bb-130">有关详细信息，请参阅[用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="9a3bb-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="9a3bb-131">PerfView</span></span>

<span data-ttu-id="9a3bb-132">[PerfView](https://github.com/Microsoft/perfview)是性能分析工具创建的.NET 团队专门为诊断.NET 性能问题。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="9a3bb-133">PerfView 允许分析 CPU 使用情况、 内存和 GC 行为、 性能事件和时钟时间。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="9a3bb-134">您可以了解有关 PerfView 以及如何入门的详细信息[PerfView 视频教程](http://channel9.msdn.com/Series/PerfView-Tutorial)或通过读取用户的指南，可以在工具或[GitHub 上](https://github.com/Microsoft/perfview)。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](http://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="9a3bb-135">Windows 性能工具包</span><span class="sxs-lookup"><span data-stu-id="9a3bb-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="9a3bb-136">[Windows 性能工具包](/windows-hardware/test/wpt/)(WPT) 包含两个组件：Windows 性能记录器 (WPR) 和 Windows 性能分析器 (WPA)。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="9a3bb-137">这些工具生成 Windows 操作系统和应用程序的深入的性能配置的文件。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="9a3bb-138">WPT 具有更多方式可视化数据，但收集其数据比 PerfView 的功能较弱。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="9a3bb-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="9a3bb-139">PerfCollect</span></span>

<span data-ttu-id="9a3bb-140">PerfView 时.NET 方案一有用的性能分析工具，它仅在上运行 Windows 因此不能使用它来从 ASP.NET Core 应用在 Linux 环境中运行收集跟踪。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="9a3bb-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)是使用分析工具的本机 Linux 的 bash 脚本 ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page)并[LTTng](https://lttng.org/)) 以 PerfView 可以分析在 Linux 上收集跟踪。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="9a3bb-142">在其中不能直接使用 PerfView 的 Linux 环境中出现的性能问题时，PerfCollect 非常有用。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="9a3bb-143">相反，PerfCollect 可以收集跟踪然后分析的.NET Core 应用中使用 PerfView 的 Windows 计算机上。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="9a3bb-144">提供了有关如何安装和开始使用 PerfCollect 的详细信息[GitHub 上](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="9a3bb-145">其他第三方性能工具</span><span class="sxs-lookup"><span data-stu-id="9a3bb-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="9a3bb-146">下面列出了一些非常有用的.NET Core 应用程序的性能调查中的第三方性能工具。</span><span class="sxs-lookup"><span data-stu-id="9a3bb-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="9a3bb-147">MiniProfiler</span><span class="sxs-lookup"><span data-stu-id="9a3bb-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="9a3bb-148">dotTrace 和来自 JetBrains dotMemory</span><span class="sxs-lookup"><span data-stu-id="9a3bb-148">dotTrace and dotMemory from JetBrains</span></span>
- <span data-ttu-id="9a3bb-149">从 Intel VTune</span><span class="sxs-lookup"><span data-stu-id="9a3bb-149">VTune from Intel</span></span>
