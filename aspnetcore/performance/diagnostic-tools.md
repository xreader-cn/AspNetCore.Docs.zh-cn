---
title: 性能诊断工具
author: mjrousos
description: 用于诊断 ASP.NET Core 应用程序中的性能问题的有用工具。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.date: 04/11/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/diagnostic-tools
ms.openlocfilehash: 5f3daaf132b903898e756160a459d4df5f421c11
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746541"
---
# <a name="performance-diagnostic-tools"></a><span data-ttu-id="6401e-103">性能诊断工具</span><span class="sxs-lookup"><span data-stu-id="6401e-103">Performance Diagnostic Tools</span></span>

<span data-ttu-id="6401e-104">作者：[Mike Rousos](https://github.com/mjrousos)</span><span class="sxs-lookup"><span data-stu-id="6401e-104">By [Mike Rousos](https://github.com/mjrousos)</span></span>

<span data-ttu-id="6401e-105">本文列出了用于诊断 ASP.NET Core 中性能问题的工具。</span><span class="sxs-lookup"><span data-stu-id="6401e-105">This article lists tools for diagnosing performance issues in ASP.NET Core.</span></span>

## <a name="visual-studio-diagnostic-tools"></a><span data-ttu-id="6401e-106">Visual Studio 诊断工具</span><span class="sxs-lookup"><span data-stu-id="6401e-106">Visual Studio Diagnostic Tools</span></span>

<span data-ttu-id="6401e-107">Visual Studio 中内置的 [分析和诊断工具](/visualstudio/profiling) 是开始调查性能问题的好地方。</span><span class="sxs-lookup"><span data-stu-id="6401e-107">The [profiling and diagnostic tools](/visualstudio/profiling) built into Visual Studio are a good place to start investigating performance issues.</span></span> <span data-ttu-id="6401e-108">这些工具的功能非常强大，可以方便地从 Visual Studio 开发环境使用。</span><span class="sxs-lookup"><span data-stu-id="6401e-108">These tools are powerful and convenient to use from the Visual Studio development environment.</span></span> <span data-ttu-id="6401e-109">该工具允许分析 ASP.NET Core 应用中的 CPU 使用率、内存使用率和性能事件。</span><span class="sxs-lookup"><span data-stu-id="6401e-109">The tooling allows analysis of CPU usage, memory usage, and performance events in ASP.NET Core apps.</span></span> <span data-ttu-id="6401e-110">内置使分析变得非常简单。</span><span class="sxs-lookup"><span data-stu-id="6401e-110">Being built-in makes profiling easy at development time.</span></span>

<span data-ttu-id="6401e-111">有关详细信息，请 [参阅 Visual Studio 文档](/visualstudio/profiling/profiling-overview)。</span><span class="sxs-lookup"><span data-stu-id="6401e-111">More information is available in [Visual Studio documentation](/visualstudio/profiling/profiling-overview).</span></span>

## <a name="application-insights"></a><span data-ttu-id="6401e-112">Application Insights</span><span class="sxs-lookup"><span data-stu-id="6401e-112">Application Insights</span></span>

<span data-ttu-id="6401e-113">[Application Insights](/azure/application-insights/app-insights-overview) 提供应用程序的详细性能数据。</span><span class="sxs-lookup"><span data-stu-id="6401e-113">[Application Insights](/azure/application-insights/app-insights-overview) provides in-depth performance data for your app.</span></span> <span data-ttu-id="6401e-114">Application Insights 会自动收集有关响应速率、失败率、依赖项响应时间等的数据。</span><span class="sxs-lookup"><span data-stu-id="6401e-114">Application Insights automatically collects data on response rates, failure rates, dependency response times, and more.</span></span> <span data-ttu-id="6401e-115">Application Insights 支持记录特定于应用的自定义事件和指标。</span><span class="sxs-lookup"><span data-stu-id="6401e-115">Application Insights supports logging custom events and metrics specific to your app.</span></span>

<span data-ttu-id="6401e-116">Azure 应用程序 Insights 提供多种方法来提供对受监视应用的见解：</span><span class="sxs-lookup"><span data-stu-id="6401e-116">Azure Application Insights provides multiple ways to give insights on monitored apps:</span></span>

- <span data-ttu-id="6401e-117">[应用程序映射](/azure/application-insights/app-insights-app-map) -有助于在分布式应用程序的所有组件中发现性能瓶颈或故障热点。</span><span class="sxs-lookup"><span data-stu-id="6401e-117">[Application Map](/azure/application-insights/app-insights-app-map) – helps spot performance bottlenecks or failure hot-spots across all components of distributed apps.</span></span>
- <span data-ttu-id="6401e-118">[Azure 指标资源管理器](/azure/azure-monitor/platform/metrics-getting-started) 是 Microsoft Azure 门户的一个组件，它允许绘制图表、直观关联趋势，并调查指标值中的峰值和 dip。</span><span class="sxs-lookup"><span data-stu-id="6401e-118">[Azure Metrics Explorer](/azure/azure-monitor/platform/metrics-getting-started) is a component of the Microsoft Azure portal that allows plotting charts, visually correlating trends, and investigating spikes and dips in metrics' values.</span></span>
- <span data-ttu-id="6401e-119">[Application Insights 门户中的 "性能" 边栏选项卡](/azure/application-insights/app-insights-tutorial-performance)：</span><span class="sxs-lookup"><span data-stu-id="6401e-119">[Performance blade in Application Insights portal](/azure/application-insights/app-insights-tutorial-performance):</span></span>

  - <span data-ttu-id="6401e-120">显示监视的应用程序中不同操作的性能详细信息。</span><span class="sxs-lookup"><span data-stu-id="6401e-120">Shows performance details for different operations in the monitored app.</span></span>
  - <span data-ttu-id="6401e-121">允许钻取到单个操作，以检查影响长时间的所有部件/依赖关系。</span><span class="sxs-lookup"><span data-stu-id="6401e-121">Allows drilling into a single operation to check all parts/dependencies that contribute to a long duration.</span></span>
  - <span data-ttu-id="6401e-122">可以从源调用探查器，按需收集性能跟踪。</span><span class="sxs-lookup"><span data-stu-id="6401e-122">Profiler can be invoked from here to collect performance traces on-demand.</span></span>

- <span data-ttu-id="6401e-123">[Azure 应用程序 Insights 探查器](/azure/azure-monitor/app/profiler) 允许 .net 应用的常规和按需分析。</span><span class="sxs-lookup"><span data-stu-id="6401e-123">[Azure Application Insights Profiler](/azure/azure-monitor/app/profiler) allows regular and on-demand profiling of .NET apps.</span></span>  <span data-ttu-id="6401e-124">Azure 门户显示通过调用堆栈和热路径捕获的性能跟踪。</span><span class="sxs-lookup"><span data-stu-id="6401e-124">Azure portal shows captured performance traces with call stacks and hot paths.</span></span> <span data-ttu-id="6401e-125">还可以使用 PerfView 下载跟踪文件以进行更深入的分析。</span><span class="sxs-lookup"><span data-stu-id="6401e-125">The trace files can also be downloaded for deeper analysis using PerfView.</span></span>

<span data-ttu-id="6401e-126">Application Insights 可用于各种环境：</span><span class="sxs-lookup"><span data-stu-id="6401e-126">Application Insights can be used in a variety environments:</span></span>

- <span data-ttu-id="6401e-127">经过优化，可在 Azure 中工作。</span><span class="sxs-lookup"><span data-stu-id="6401e-127">Optimized to work in Azure.</span></span>
- <span data-ttu-id="6401e-128">在生产、开发和过渡环境中工作。</span><span class="sxs-lookup"><span data-stu-id="6401e-128">Works in production, development, and staging.</span></span>
- <span data-ttu-id="6401e-129">从 [Visual Studio](/azure/application-insights/app-insights-visual-studio) 或其他宿主环境中运行。</span><span class="sxs-lookup"><span data-stu-id="6401e-129">Works locally from [Visual Studio](/azure/application-insights/app-insights-visual-studio) or in other hosting environments.</span></span>

<span data-ttu-id="6401e-130">有关详细信息，请参阅[适用于 ASP.NET Core 的 Application Insights](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="6401e-130">For more information, see [Application Insights for ASP.NET Core](/azure/application-insights/app-insights-asp-net-core).</span></span>

## <a name="perfview"></a><span data-ttu-id="6401e-131">PerfView</span><span class="sxs-lookup"><span data-stu-id="6401e-131">PerfView</span></span>

<span data-ttu-id="6401e-132">[PerfView](https://github.com/Microsoft/perfview) 是 .net 团队为诊断 .net 性能问题而创建的一种性能分析工具。</span><span class="sxs-lookup"><span data-stu-id="6401e-132">[PerfView](https://github.com/Microsoft/perfview) is a performance analysis tool created by the .NET team specifically for diagnosing .NET performance issues.</span></span> <span data-ttu-id="6401e-133">PerfView 允许对 CPU 使用情况、内存和 GC 行为、性能事件和时钟时间进行分析。</span><span class="sxs-lookup"><span data-stu-id="6401e-133">PerfView allows analysis of CPU usage, memory and GC behavior, performance events, and wall clock time.</span></span>

<span data-ttu-id="6401e-134">你可以了解有关 PerfView 的详细信息以及如何开始使用 [PerfView 视频教程](https://channel9.msdn.com/Series/PerfView-Tutorial) ，或阅读该工具或 [GitHub 上](https://github.com/Microsoft/perfview)提供的用户指南。</span><span class="sxs-lookup"><span data-stu-id="6401e-134">You can learn more about PerfView and how to get started with [PerfView video tutorials](https://channel9.msdn.com/Series/PerfView-Tutorial) or by reading the user's guide available in the tool or [on GitHub](https://github.com/Microsoft/perfview).</span></span>

## <a name="windows-performance-toolkit"></a><span data-ttu-id="6401e-135">Windows 性能工具包</span><span class="sxs-lookup"><span data-stu-id="6401e-135">Windows Performance Toolkit</span></span>

<span data-ttu-id="6401e-136">[Windows 性能工具包](/windows-hardware/test/wpt/) (WPT) 包含两个组件： Windows 性能记录器 (") 和 Windows 性能分析器 (WPA) 。</span><span class="sxs-lookup"><span data-stu-id="6401e-136">[Windows Performance Toolkit](/windows-hardware/test/wpt/) (WPT) consists of two components: Windows Performance Recorder (WPR) and Windows Performance Analyzer (WPA).</span></span> <span data-ttu-id="6401e-137">这些工具会生成 Windows 操作系统和应用的深入性能配置文件。</span><span class="sxs-lookup"><span data-stu-id="6401e-137">The tools produce in-depth performance profiles of Windows operating systems and apps.</span></span> <span data-ttu-id="6401e-138">WPT 提供了更丰富的数据可视化方法，但其数据收集功能不如 PerfView。</span><span class="sxs-lookup"><span data-stu-id="6401e-138">WPT has richer ways of visualizing data, but its data collecting is less powerful than PerfView's.</span></span>

## <a name="perfcollect"></a><span data-ttu-id="6401e-139">PerfCollect</span><span class="sxs-lookup"><span data-stu-id="6401e-139">PerfCollect</span></span>

<span data-ttu-id="6401e-140">尽管 PerfView 是适用于 .NET 方案的有用性能分析工具，但它仅在 Windows 上运行，因此不能使用它从 Linux 环境中运行的 ASP.NET Core 应用程序收集跟踪。</span><span class="sxs-lookup"><span data-stu-id="6401e-140">While PerfView is a useful performance analysis tool for .NET scenarios, it only runs on Windows so you can't use it to collect traces from ASP.NET Core apps running in Linux environments.</span></span>

<span data-ttu-id="6401e-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) 是一种 bash 脚本，它使用本机 Linux 分析工具 ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) 和 [LTTng](https://lttng.org/)) 收集 Linux 上可通过 PerfView 分析的跟踪。</span><span class="sxs-lookup"><span data-stu-id="6401e-141">[PerfCollect](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md) is a bash script that uses native Linux profiling tools ([Perf](https://perf.wiki.kernel.org/index.php/Main_Page) and [LTTng](https://lttng.org/)) to collect traces on Linux that can be analyzed by PerfView.</span></span> <span data-ttu-id="6401e-142">当在不能直接使用 PerfView 的 Linux 环境中出现性能问题时，PerfCollect 非常有用。</span><span class="sxs-lookup"><span data-stu-id="6401e-142">PerfCollect is useful when performance problems show up in Linux environments where PerfView can't be used directly.</span></span> <span data-ttu-id="6401e-143">相反，PerfCollect 可以从 .NET Core 应用收集跟踪，然后使用 PerfView 在 Windows 计算机上进行分析。</span><span class="sxs-lookup"><span data-stu-id="6401e-143">Instead, PerfCollect can collect traces from .NET Core apps that are then analyzed on a Windows computer using PerfView.</span></span>

<span data-ttu-id="6401e-144">[GitHub 上](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md)提供了有关如何安装和开始使用 PerfCollect 的详细信息。</span><span class="sxs-lookup"><span data-stu-id="6401e-144">More information about how to install and get started with PerfCollect is available [on GitHub](https://github.com/dotnet/coreclr/blob/master/Documentation/project-docs/linux-performance-tracing.md).</span></span>

## <a name="other-third-party-performance-tools"></a><span data-ttu-id="6401e-145">其他第三方性能工具</span><span class="sxs-lookup"><span data-stu-id="6401e-145">Other Third-party Performance Tools</span></span>

<span data-ttu-id="6401e-146">下面列出了一些可在 .NET Core 应用程序的性能调查中发挥作用的第三方性能工具。</span><span class="sxs-lookup"><span data-stu-id="6401e-146">The following lists some third-party performance tools that are useful in performance investigation of .NET Core applications.</span></span>

- [<span data-ttu-id="6401e-147">MiniProfiler</span><span class="sxs-lookup"><span data-stu-id="6401e-147">MiniProfiler</span></span>](https://miniprofiler.com/)
- <span data-ttu-id="6401e-148">[JetBrains](https://www.jetbrains.com/)中的[dotTrace](https://www.jetbrains.com/profiler/)和[dotMemory](https://www.jetbrains.com/dotmemory/)</span><span class="sxs-lookup"><span data-stu-id="6401e-148">[dotTrace](https://www.jetbrains.com/profiler/) and [dotMemory](https://www.jetbrains.com/dotmemory/) from [JetBrains](https://www.jetbrains.com/)</span></span>
- <span data-ttu-id="6401e-149">来自 Intel 的[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html)</span><span class="sxs-lookup"><span data-stu-id="6401e-149">[VTune](https://software.intel.com/content/www/us/en/develop/tools/vtune-profiler.html) from Intel</span></span>
