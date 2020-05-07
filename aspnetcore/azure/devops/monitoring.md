---
title: 监视和调试 - 通过 ASP.NET Core 和 Azure 实现 DevOps
author: CamSoper
description: 在通过 ASP.NET Core 和 Azure 实现的 DevOps 解决方案中监视和调试代码
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 07/10/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: azure/devops/monitor
ms.openlocfilehash: 3af36a37124968e13952e8bf5de1b643265a4a5b
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766883"
---
# <a name="monitor-and-debug"></a><span data-ttu-id="adef1-103">监视和调试</span><span class="sxs-lookup"><span data-stu-id="adef1-103">Monitor and debug</span></span>

<span data-ttu-id="adef1-104">部署了应用并构建了 DevOps 管道后，必须了解如何对应用进行监视和故障排除。</span><span class="sxs-lookup"><span data-stu-id="adef1-104">Having deployed the app and built a DevOps pipeline, it's important to understand how to monitor and troubleshoot the app.</span></span>

<span data-ttu-id="adef1-105">在本部分中，你将完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="adef1-105">In this section, you'll complete the following tasks:</span></span>

* <span data-ttu-id="adef1-106">在 Azure 门户中查找基本监视和故障排除数据</span><span class="sxs-lookup"><span data-stu-id="adef1-106">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="adef1-107">了解如何通过 Azure Monitor 更深入地了解所有 Azure 服务中的指标</span><span class="sxs-lookup"><span data-stu-id="adef1-107">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="adef1-108">将 Web 应用与 Application Insights 连接以进行应用分析</span><span class="sxs-lookup"><span data-stu-id="adef1-108">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="adef1-109">打开日志记录并了解在何处下载日志</span><span class="sxs-lookup"><span data-stu-id="adef1-109">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="adef1-110">实时流式传输日志</span><span class="sxs-lookup"><span data-stu-id="adef1-110">Stream logs in real time</span></span>
* <span data-ttu-id="adef1-111">了解在何处设置警报</span><span class="sxs-lookup"><span data-stu-id="adef1-111">Learn where to set up alerts</span></span>
* <span data-ttu-id="adef1-112">了解远程调试 Azure 应用服务 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="adef1-112">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="basic-monitoring-and-troubleshooting"></a><span data-ttu-id="adef1-113">基本监视和故障排除</span><span class="sxs-lookup"><span data-stu-id="adef1-113">Basic monitoring and troubleshooting</span></span>

<span data-ttu-id="adef1-114">应用服务 Web 应用可轻松地实时进行监视。</span><span class="sxs-lookup"><span data-stu-id="adef1-114">App Service web apps are easily monitored in real time.</span></span> <span data-ttu-id="adef1-115">Azure 门户在易于理解的图表和图形中呈现指标。</span><span class="sxs-lookup"><span data-stu-id="adef1-115">The Azure portal renders metrics in easy-to-understand charts and graphs.</span></span>

1. <span data-ttu-id="adef1-116">打开 [Azure 门户](https://portal.azure.com)，然后导航到 mywebapp\<unique_number\>  应用服务。</span><span class="sxs-lookup"><span data-stu-id="adef1-116">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>

1. <span data-ttu-id="adef1-117">“概览”  选项卡显示有用的“一览式”信息，其中包括显示最近指标的图形。</span><span class="sxs-lookup"><span data-stu-id="adef1-117">The **Overview** tab displays useful "at-a-glance" information, including graphs displaying recent metrics.</span></span>

    ![显示概览面板的屏幕截图](./media/monitoring/overview.png)

    * <span data-ttu-id="adef1-119">Http 5xx  ：服务器端错误计数，通常是 ASP.NET Core 代码中的异常。</span><span class="sxs-lookup"><span data-stu-id="adef1-119">**Http 5xx**: Count of server-side errors, usually exceptions in ASP.NET Core code.</span></span>
    * <span data-ttu-id="adef1-120">**数据输入**：进入 Web 应用的数据流入量。</span><span class="sxs-lookup"><span data-stu-id="adef1-120">**Data In**: Data ingress coming into your web app.</span></span>
    * <span data-ttu-id="adef1-121">**数据输出**：从 Web 应用到客户端的数据流出量。</span><span class="sxs-lookup"><span data-stu-id="adef1-121">**Data Out**: Data egress from your web app to clients.</span></span>
    * <span data-ttu-id="adef1-122">**请求**：HTTP 请求计数。</span><span class="sxs-lookup"><span data-stu-id="adef1-122">**Requests**: Count of HTTP requests.</span></span>
    * <span data-ttu-id="adef1-123">**平均响应时间**：Web 应用响应 HTTP 请求的平均时间。</span><span class="sxs-lookup"><span data-stu-id="adef1-123">**Average Response Time**: Average time for the web app to respond to HTTP requests.</span></span>

    <span data-ttu-id="adef1-124">此页面还提供一些用于故障排除和优化的自助服务工具。</span><span class="sxs-lookup"><span data-stu-id="adef1-124">Several self-service tools for troubleshooting and optimization are also found on this page.</span></span>

    ![显示自助服务工具的屏幕截图](./media/monitoring/wizards.png)

    * <span data-ttu-id="adef1-126">“诊断并解决问题”  是一种自助服务故障排除程序。</span><span class="sxs-lookup"><span data-stu-id="adef1-126">**Diagnose and solve problems** is a self-service troubleshooter.</span></span>
    * <span data-ttu-id="adef1-127">Application Insights  用于分析性能和应用行为，会在此部分后面进行讨论。</span><span class="sxs-lookup"><span data-stu-id="adef1-127">**Application Insights** is for profiling performance and app behavior, and is discussed later in this section.</span></span>
    * <span data-ttu-id="adef1-128">“应用服务顾问”  为优化应用体验提供建议。</span><span class="sxs-lookup"><span data-stu-id="adef1-128">**App Service Advisor** makes recommendations to tune your app experience.</span></span>

## <a name="advanced-monitoring"></a><span data-ttu-id="adef1-129">高级监视</span><span class="sxs-lookup"><span data-stu-id="adef1-129">Advanced monitoring</span></span>

<span data-ttu-id="adef1-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) 是一种用于在 Azure 服务间监视所有指标和设置警报的集中服务。</span><span class="sxs-lookup"><span data-stu-id="adef1-130">[Azure Monitor](/azure/monitoring-and-diagnostics/) is the centralized service for monitoring all metrics and setting alerts across Azure services.</span></span> <span data-ttu-id="adef1-131">在 Azure Monitor 中，管理员可以精细地跟踪性能和确定趋势。</span><span class="sxs-lookup"><span data-stu-id="adef1-131">Within Azure Monitor, administrators can granularly track performance and identify trends.</span></span> <span data-ttu-id="adef1-132">每个 Azure 服务都向 Azure Monitor 提供自己的[指标集](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions)。</span><span class="sxs-lookup"><span data-stu-id="adef1-132">Each Azure service offers its own [set of metrics](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions) to Azure Monitor.</span></span>

## <a name="profile-with-application-insights"></a><span data-ttu-id="adef1-133">使用 Application Insights 进行分析</span><span class="sxs-lookup"><span data-stu-id="adef1-133">Profile with Application Insights</span></span>

<span data-ttu-id="adef1-134">[Application Insights](/azure/application-insights/app-insights-overview) 是一种 Azure 服务，用于分析 Web 应用的性能和稳定性以及用户使用它们的方式。</span><span class="sxs-lookup"><span data-stu-id="adef1-134">[Application Insights](/azure/application-insights/app-insights-overview) is an Azure service for analyzing the performance and stability of web apps and how users use them.</span></span> <span data-ttu-id="adef1-135">Application Insights 中的数据比 Azure Monitor 更广泛且更深入。</span><span class="sxs-lookup"><span data-stu-id="adef1-135">The data from Application Insights is broader and deeper than that of Azure Monitor.</span></span> <span data-ttu-id="adef1-136">数据可以为开发者和管理员提供重要信息来改善应用。</span><span class="sxs-lookup"><span data-stu-id="adef1-136">The data can provide developers and administrators with key information for improving apps.</span></span> <span data-ttu-id="adef1-137">Application Insights 可以添加到 Azure 应用服务资源，而无需更改代码。</span><span class="sxs-lookup"><span data-stu-id="adef1-137">Application Insights can be added to an Azure App Service resource without code changes.</span></span>

1. <span data-ttu-id="adef1-138">打开 [Azure 门户](https://portal.azure.com)，然后导航到 mywebapp\<unique_number\>  应用服务。</span><span class="sxs-lookup"><span data-stu-id="adef1-138">Open the [Azure portal](https://portal.azure.com), and then navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="adef1-139">在“概览”  选项卡上，单击“Application Insights”  磁贴。</span><span class="sxs-lookup"><span data-stu-id="adef1-139">From the **Overview** tab, click the **Application Insights** tile.</span></span>

    ![Application Insights 磁贴](./media/monitoring/app-insights.png)

1. <span data-ttu-id="adef1-141">选择“新建资源”  单选按钮。</span><span class="sxs-lookup"><span data-stu-id="adef1-141">Select the **Create new resource** radio button.</span></span> <span data-ttu-id="adef1-142">使用默认资源名称，并选择 Application Insights 资源的位置。</span><span class="sxs-lookup"><span data-stu-id="adef1-142">Use the default resource name, and select the location for the Application Insights resource.</span></span> <span data-ttu-id="adef1-143">该位置不需要与 Web 应用的位置匹配。</span><span class="sxs-lookup"><span data-stu-id="adef1-143">The location doesn't need to match that of your web app.</span></span>

    ![Application Insights 设置](./media/monitoring/new-app-insights.png)

1. <span data-ttu-id="adef1-145">对于“运行时/框架”  ，选择“ASP.NET Core”  。</span><span class="sxs-lookup"><span data-stu-id="adef1-145">For **Runtime/Framework**, select **ASP.NET Core**.</span></span> <span data-ttu-id="adef1-146">接受默认设置。</span><span class="sxs-lookup"><span data-stu-id="adef1-146">Accept the default settings.</span></span>
1. <span data-ttu-id="adef1-147">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="adef1-147">Select **OK**.</span></span> <span data-ttu-id="adef1-148">如果系统提示进行确认，请选择“继续”  。</span><span class="sxs-lookup"><span data-stu-id="adef1-148">If prompted to confirm, select **Continue**.</span></span>
1. <span data-ttu-id="adef1-149">创建资源之后，单击 Application Insights 资源的名称，直接导航到 Application Insights 页面。</span><span class="sxs-lookup"><span data-stu-id="adef1-149">After the resource has been created, click the name of Application Insights resource to navigate directly to the Application Insights page.</span></span>

    ![新 Application Insights 资源已准备就绪](./media/monitoring/new-app-insights-done.png)

<span data-ttu-id="adef1-151">使用应用时，数据会累积。</span><span class="sxs-lookup"><span data-stu-id="adef1-151">As the app is used, data accumulates.</span></span> <span data-ttu-id="adef1-152">选择“刷新”  以使用新数据重载边栏选项卡。</span><span class="sxs-lookup"><span data-stu-id="adef1-152">Select **Refresh** to reload the blade with new data.</span></span>

![Application Insights 概览选项卡](./media/monitoring/app-insights-overview.png)

<span data-ttu-id="adef1-154">Application Insights 可提供有用的服务器端信息，无需额外配置。</span><span class="sxs-lookup"><span data-stu-id="adef1-154">Application Insights provides useful server-side information with no additional configuration.</span></span> <span data-ttu-id="adef1-155">若要从 Application Insights 获得最大价值，请[通过 Application Insights SDK 检测应用](/azure/application-insights/app-insights-asp-net-core)。</span><span class="sxs-lookup"><span data-stu-id="adef1-155">To get the most value from Application Insights, [instrument your app with the Application Insights SDK](/azure/application-insights/app-insights-asp-net-core).</span></span> <span data-ttu-id="adef1-156">正确配置后，该服务会在 Web 服务器和浏览器中提供端到端监视，包括客户端性能。</span><span class="sxs-lookup"><span data-stu-id="adef1-156">When properly configured, the service provides end-to-end monitoring across the web server and browser, including client-side performance.</span></span> <span data-ttu-id="adef1-157">有关详细信息，请参阅 [Application Insights 文档](/azure/application-insights/app-insights-overview)。</span><span class="sxs-lookup"><span data-stu-id="adef1-157">For more information, see the [Application Insights documentation](/azure/application-insights/app-insights-overview).</span></span>

## <a name="logging"></a><span data-ttu-id="adef1-158">Logging</span><span class="sxs-lookup"><span data-stu-id="adef1-158">Logging</span></span>

<span data-ttu-id="adef1-159">默认情况下，Web 服务器和应用日志在 Azure 应用服务中处于禁用状态。</span><span class="sxs-lookup"><span data-stu-id="adef1-159">Web server and app logs are disabled by default in Azure App Service.</span></span> <span data-ttu-id="adef1-160">可通过以下步骤启用日志：</span><span class="sxs-lookup"><span data-stu-id="adef1-160">Enable the logs with the following steps:</span></span>

1. <span data-ttu-id="adef1-161">打开 [Azure 门户](https://portal.azure.com)，然后导航到 mywebapp\<unique_number\>  应用服务。</span><span class="sxs-lookup"><span data-stu-id="adef1-161">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="adef1-162">在左侧菜单中，向下滚动到“监视”部分  。</span><span class="sxs-lookup"><span data-stu-id="adef1-162">In the menu to the left, scroll down to the **Monitoring** section.</span></span> <span data-ttu-id="adef1-163">选择“诊断日志”  。</span><span class="sxs-lookup"><span data-stu-id="adef1-163">Select **Diagnostics logs**.</span></span>

    ![诊断日志链接](./media/monitoring/logging.png)

1. <span data-ttu-id="adef1-165">打开“应用程序日志记录(文件系统)”  。</span><span class="sxs-lookup"><span data-stu-id="adef1-165">Turn on **Application Logging (Filesystem)**.</span></span> <span data-ttu-id="adef1-166">如果出现提示，请单击框来安装扩展，以在 Web 应用中启用应用日志记录。</span><span class="sxs-lookup"><span data-stu-id="adef1-166">If prompted, click the box to install the extensions to enable app logging in the web app.</span></span>
1. <span data-ttu-id="adef1-167">将“Web 服务器日志记录”  设置为“文件系统”  。</span><span class="sxs-lookup"><span data-stu-id="adef1-167">Set **Web server logging** to **File System**.</span></span>
1. <span data-ttu-id="adef1-168">输入“保留期”  （以天为单位）。</span><span class="sxs-lookup"><span data-stu-id="adef1-168">Enter the **Retention Period** in days.</span></span> <span data-ttu-id="adef1-169">例如 30。</span><span class="sxs-lookup"><span data-stu-id="adef1-169">For example, 30.</span></span>
1. <span data-ttu-id="adef1-170">单击“保存”  。</span><span class="sxs-lookup"><span data-stu-id="adef1-170">Click **Save**.</span></span>

<span data-ttu-id="adef1-171">会为 Web 应用生成 ASP.NET Core 和 Web 服务器（应用服务）日志。</span><span class="sxs-lookup"><span data-stu-id="adef1-171">ASP.NET Core and web server (App Service) logs are generated for the web app.</span></span> <span data-ttu-id="adef1-172">可以使用显示的 FTP/FTPS 信息下载它们。</span><span class="sxs-lookup"><span data-stu-id="adef1-172">They can be downloaded using the FTP/FTPS information displayed.</span></span> <span data-ttu-id="adef1-173">密码与本指南前面部分中创建的部署凭据相同。</span><span class="sxs-lookup"><span data-stu-id="adef1-173">The password is the same as the deployment credentials created earlier in this guide.</span></span> <span data-ttu-id="adef1-174">日志可以[通过 PowerShell 或 Azure CLI 直接流式传输到本地计算机](/azure/app-service/web-sites-enable-diagnostic-log#download)。</span><span class="sxs-lookup"><span data-stu-id="adef1-174">The logs can be [streamed directly to your local machine with PowerShell or Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#download).</span></span> <span data-ttu-id="adef1-175">也可以[在 Application Insights 中查看](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)日志。</span><span class="sxs-lookup"><span data-stu-id="adef1-175">Logs can also be [viewed in Application Insights](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights).</span></span>

## <a name="log-streaming"></a><span data-ttu-id="adef1-176">日志流式处理</span><span class="sxs-lookup"><span data-stu-id="adef1-176">Log streaming</span></span>

<span data-ttu-id="adef1-177">应用和 Web 服务器日志可以通过门户实时流式传输。</span><span class="sxs-lookup"><span data-stu-id="adef1-177">App and web server logs can be streamed in real time through the portal.</span></span>

1. <span data-ttu-id="adef1-178">打开 [Azure 门户](https://portal.azure.com)，然后导航到 mywebapp\<unique_number\>  应用服务。</span><span class="sxs-lookup"><span data-stu-id="adef1-178">Open the [Azure portal](https://portal.azure.com), and navigate to the *mywebapp\<unique_number\>* App Service.</span></span>
1. <span data-ttu-id="adef1-179">在左侧菜单中，向下滚动到“监视”部分  并选择“日志流”  。</span><span class="sxs-lookup"><span data-stu-id="adef1-179">In the menu to the left, scroll down to the **Monitoring** section and select **Log stream**.</span></span>

    ![显示日志流链接的屏幕截图](./media/monitoring/log-stream.png)

<span data-ttu-id="adef1-181">也可以通过 [Azure CLI 或 Azure PowerShell 流式传输](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)日志（包括通过 Cloud Shell）。</span><span class="sxs-lookup"><span data-stu-id="adef1-181">Logs can also be [streamed via Azure CLI or Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs), including through the Cloud Shell.</span></span>

## <a name="alerts"></a><span data-ttu-id="adef1-182">警报</span><span class="sxs-lookup"><span data-stu-id="adef1-182">Alerts</span></span>

<span data-ttu-id="adef1-183">Azure Monitor 还会基于指标、管理事件和其他条件提供[实时警报](/azure/monitoring-and-diagnostics/insights-alerts-portal)。</span><span class="sxs-lookup"><span data-stu-id="adef1-183">Azure Monitor also provides [real time alerts](/azure/monitoring-and-diagnostics/insights-alerts-portal) based on metrics, administrative events, and other criteria.</span></span>

> <span data-ttu-id="adef1-184">*注意：当前仅在警报（经典）服务中提供有关 Web 应用指标的警报。*</span><span class="sxs-lookup"><span data-stu-id="adef1-184">*Note: Currently alerting on web app metrics is only available in the Alerts (classic) service.*</span></span>

<span data-ttu-id="adef1-185">[警报（经典）服务](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal)可以在 Azure Monitor 或是应用服务设置的“监视”  部分下找到。</span><span class="sxs-lookup"><span data-stu-id="adef1-185">The [Alerts (classic) service](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal) can be found in Azure Monitor or under the **Monitoring** section of the App Service settings.</span></span>

![警报（经典）链接](./media/monitoring/alerts.png)

## <a name="live-debugging"></a><span data-ttu-id="adef1-187">实时调试</span><span class="sxs-lookup"><span data-stu-id="adef1-187">Live debugging</span></span>

<span data-ttu-id="adef1-188">当日志未提供足够信息时，可以通过 [Visual Studio 远程调试](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) Azure 应用服务。</span><span class="sxs-lookup"><span data-stu-id="adef1-188">Azure App Service can be [debugged remotely with Visual Studio](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) when logs don't provide enough information.</span></span> <span data-ttu-id="adef1-189">但是，远程调试需要使用调试符号编译应用。</span><span class="sxs-lookup"><span data-stu-id="adef1-189">However, remote debugging requires the app to be compiled with debug symbols.</span></span> <span data-ttu-id="adef1-190">除非万不得已，否则不应在生产中进行调试。</span><span class="sxs-lookup"><span data-stu-id="adef1-190">Debugging shouldn't be done in production, except as a last resort.</span></span>

## <a name="conclusion"></a><span data-ttu-id="adef1-191">结束语</span><span class="sxs-lookup"><span data-stu-id="adef1-191">Conclusion</span></span>

<span data-ttu-id="adef1-192">在此部分中，已完成以下任务：</span><span class="sxs-lookup"><span data-stu-id="adef1-192">In this section, you completed the following tasks:</span></span>

* <span data-ttu-id="adef1-193">在 Azure 门户中查找基本监视和故障排除数据</span><span class="sxs-lookup"><span data-stu-id="adef1-193">Find basic monitoring and troubleshooting data in the Azure portal</span></span>
* <span data-ttu-id="adef1-194">了解如何通过 Azure Monitor 更深入地了解所有 Azure 服务中的指标</span><span class="sxs-lookup"><span data-stu-id="adef1-194">Learn how Azure Monitor provides a deeper look at metrics across all Azure services</span></span>
* <span data-ttu-id="adef1-195">将 Web 应用与 Application Insights 连接以进行应用分析</span><span class="sxs-lookup"><span data-stu-id="adef1-195">Connect the web app with Application Insights for app profiling</span></span>
* <span data-ttu-id="adef1-196">打开日志记录并了解在何处下载日志</span><span class="sxs-lookup"><span data-stu-id="adef1-196">Turn on logging and learn where to download logs</span></span>
* <span data-ttu-id="adef1-197">实时流式传输日志</span><span class="sxs-lookup"><span data-stu-id="adef1-197">Stream logs in real time</span></span>
* <span data-ttu-id="adef1-198">了解在何处设置警报</span><span class="sxs-lookup"><span data-stu-id="adef1-198">Learn where to set up alerts</span></span>
* <span data-ttu-id="adef1-199">了解远程调试 Azure 应用服务 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="adef1-199">Learn about remote debugging Azure App Service web apps.</span></span>

## <a name="additional-reading"></a><span data-ttu-id="adef1-200">其他阅读材料</span><span class="sxs-lookup"><span data-stu-id="adef1-200">Additional reading</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [<span data-ttu-id="adef1-201">使用 Application Insights 监视 Azure Web 应用性能</span><span class="sxs-lookup"><span data-stu-id="adef1-201">Monitor Azure web app performance with Application Insights</span></span>](/azure/application-insights/app-insights-azure-web-apps)
* [<span data-ttu-id="adef1-202">在 Azure 应用服务中为应用启用诊断日志记录</span><span class="sxs-lookup"><span data-stu-id="adef1-202">Enable diagnostics logging for web apps in Azure App Service</span></span>](/azure/app-service/web-sites-enable-diagnostic-log)
* [<span data-ttu-id="adef1-203">使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除</span><span class="sxs-lookup"><span data-stu-id="adef1-203">Troubleshoot a web app in Azure App Service using Visual Studio</span></span>](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [<span data-ttu-id="adef1-204">在 Azure Monitor 中为 Azure 服务创建经典指标警报 - Azure 门户</span><span class="sxs-lookup"><span data-stu-id="adef1-204">Create classic metric alerts in Azure Monitor for Azure services - Azure portal</span></span>](/azure/monitoring-and-diagnostics/insights-alerts-portal)
