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
# <a name="monitor-and-debug"></a>监视和调试

部署了应用并构建了 DevOps 管道后，必须了解如何对应用进行监视和故障排除。

在本部分中，你将完成以下任务：

* 在 Azure 门户中查找基本监视和故障排除数据
* 了解如何通过 Azure Monitor 更深入地了解所有 Azure 服务中的指标
* 将 Web 应用与 Application Insights 连接以进行应用分析
* 打开日志记录并了解在何处下载日志
* 实时流式传输日志
* 了解在何处设置警报
* 了解远程调试 Azure 应用服务 Web 应用。

## <a name="basic-monitoring-and-troubleshooting"></a>基本监视和故障排除

应用服务 Web 应用可轻松地实时进行监视。 Azure 门户在易于理解的图表和图形中呈现指标。

1. 打开 [Azure 门户](https://portal.azure.com)，然后导航到 mywebapp\<unique_number\>  应用服务。

1. “概览”  选项卡显示有用的“一览式”信息，其中包括显示最近指标的图形。

    ![显示概览面板的屏幕截图](./media/monitoring/overview.png)

    * Http 5xx  ：服务器端错误计数，通常是 ASP.NET Core 代码中的异常。
    * **数据输入**：进入 Web 应用的数据流入量。
    * **数据输出**：从 Web 应用到客户端的数据流出量。
    * **请求**：HTTP 请求计数。
    * **平均响应时间**：Web 应用响应 HTTP 请求的平均时间。

    此页面还提供一些用于故障排除和优化的自助服务工具。

    ![显示自助服务工具的屏幕截图](./media/monitoring/wizards.png)

    * “诊断并解决问题”  是一种自助服务故障排除程序。
    * Application Insights  用于分析性能和应用行为，会在此部分后面进行讨论。
    * “应用服务顾问”  为优化应用体验提供建议。

## <a name="advanced-monitoring"></a>高级监视

[Azure Monitor](/azure/monitoring-and-diagnostics/) 是一种用于在 Azure 服务间监视所有指标和设置警报的集中服务。 在 Azure Monitor 中，管理员可以精细地跟踪性能和确定趋势。 每个 Azure 服务都向 Azure Monitor 提供自己的[指标集](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions)。

## <a name="profile-with-application-insights"></a>使用 Application Insights 进行分析

[Application Insights](/azure/application-insights/app-insights-overview) 是一种 Azure 服务，用于分析 Web 应用的性能和稳定性以及用户使用它们的方式。 Application Insights 中的数据比 Azure Monitor 更广泛且更深入。 数据可以为开发者和管理员提供重要信息来改善应用。 Application Insights 可以添加到 Azure 应用服务资源，而无需更改代码。

1. 打开 [Azure 门户](https://portal.azure.com)，然后导航到 mywebapp\<unique_number\>  应用服务。
1. 在“概览”  选项卡上，单击“Application Insights”  磁贴。

    ![Application Insights 磁贴](./media/monitoring/app-insights.png)

1. 选择“新建资源”  单选按钮。 使用默认资源名称，并选择 Application Insights 资源的位置。 该位置不需要与 Web 应用的位置匹配。

    ![Application Insights 设置](./media/monitoring/new-app-insights.png)

1. 对于“运行时/框架”  ，选择“ASP.NET Core”  。 接受默认设置。
1. 选择“确定”  。 如果系统提示进行确认，请选择“继续”  。
1. 创建资源之后，单击 Application Insights 资源的名称，直接导航到 Application Insights 页面。

    ![新 Application Insights 资源已准备就绪](./media/monitoring/new-app-insights-done.png)

使用应用时，数据会累积。 选择“刷新”  以使用新数据重载边栏选项卡。

![Application Insights 概览选项卡](./media/monitoring/app-insights-overview.png)

Application Insights 可提供有用的服务器端信息，无需额外配置。 若要从 Application Insights 获得最大价值，请[通过 Application Insights SDK 检测应用](/azure/application-insights/app-insights-asp-net-core)。 正确配置后，该服务会在 Web 服务器和浏览器中提供端到端监视，包括客户端性能。 有关详细信息，请参阅 [Application Insights 文档](/azure/application-insights/app-insights-overview)。

## <a name="logging"></a>Logging

默认情况下，Web 服务器和应用日志在 Azure 应用服务中处于禁用状态。 可通过以下步骤启用日志：

1. 打开 [Azure 门户](https://portal.azure.com)，然后导航到 mywebapp\<unique_number\>  应用服务。
1. 在左侧菜单中，向下滚动到“监视”部分  。 选择“诊断日志”  。

    ![诊断日志链接](./media/monitoring/logging.png)

1. 打开“应用程序日志记录(文件系统)”  。 如果出现提示，请单击框来安装扩展，以在 Web 应用中启用应用日志记录。
1. 将“Web 服务器日志记录”  设置为“文件系统”  。
1. 输入“保留期”  （以天为单位）。 例如 30。
1. 单击“保存”  。

会为 Web 应用生成 ASP.NET Core 和 Web 服务器（应用服务）日志。 可以使用显示的 FTP/FTPS 信息下载它们。 密码与本指南前面部分中创建的部署凭据相同。 日志可以[通过 PowerShell 或 Azure CLI 直接流式传输到本地计算机](/azure/app-service/web-sites-enable-diagnostic-log#download)。 也可以[在 Application Insights 中查看](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)日志。

## <a name="log-streaming"></a>日志流式处理

应用和 Web 服务器日志可以通过门户实时流式传输。

1. 打开 [Azure 门户](https://portal.azure.com)，然后导航到 mywebapp\<unique_number\>  应用服务。
1. 在左侧菜单中，向下滚动到“监视”部分  并选择“日志流”  。

    ![显示日志流链接的屏幕截图](./media/monitoring/log-stream.png)

也可以通过 [Azure CLI 或 Azure PowerShell 流式传输](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)日志（包括通过 Cloud Shell）。

## <a name="alerts"></a>警报

Azure Monitor 还会基于指标、管理事件和其他条件提供[实时警报](/azure/monitoring-and-diagnostics/insights-alerts-portal)。

> *注意：当前仅在警报（经典）服务中提供有关 Web 应用指标的警报。*

[警报（经典）服务](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal)可以在 Azure Monitor 或是应用服务设置的“监视”  部分下找到。

![警报（经典）链接](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>实时调试

当日志未提供足够信息时，可以通过 [Visual Studio 远程调试](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug) Azure 应用服务。 但是，远程调试需要使用调试符号编译应用。 除非万不得已，否则不应在生产中进行调试。

## <a name="conclusion"></a>结束语

在此部分中，已完成以下任务：

* 在 Azure 门户中查找基本监视和故障排除数据
* 了解如何通过 Azure Monitor 更深入地了解所有 Azure 服务中的指标
* 将 Web 应用与 Application Insights 连接以进行应用分析
* 打开日志记录并了解在何处下载日志
* 实时流式传输日志
* 了解在何处设置警报
* 了解远程调试 Azure 应用服务 Web 应用。

## <a name="additional-reading"></a>其他阅读材料

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [使用 Application Insights 监视 Azure Web 应用性能](/azure/application-insights/app-insights-azure-web-apps)
* [在 Azure 应用服务中为应用启用诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)
* [使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [在 Azure Monitor 中为 Azure 服务创建经典指标警报 - Azure 门户](/azure/monitoring-and-diagnostics/insights-alerts-portal)
