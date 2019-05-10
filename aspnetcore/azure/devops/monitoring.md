---
title: 监视和调试-使用 ASP.NET Core 和 Azure 进行开发运营
author: CamSoper
description: 监视和使用 ASP.NET Core 和 Azure DevOps 解决方案的一部分进行调试你的代码
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/monitor
ms.openlocfilehash: 00489bd92dfff8fd80bd24c2e60193d32031d7c4
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64893064"
---
# <a name="monitor-and-debug"></a>监视和调试

部署应用程序和构建 DevOps 管道，务必要了解如何监视和故障排除应用程序。

在本部分中，将完成以下任务：

* 查找基本的监视和故障排除 Azure 门户中的数据
* 了解 Azure Monitor 如何跨所有 Azure 服务提供更深层次查看的度量值
* 使用 Application Insights 的 web 应用连接有关的应用程序分析
* 启用日志记录并了解在何处下载日志
* Stream 实时日志
* 了解在何处设置警报
* 了解远程调试 Azure 应用服务 web 应用。

## <a name="basic-monitoring-and-troubleshooting"></a>基本监视和故障排除

在真实时间中轻松地监视应用服务 web 应用。 在 Azure 门户将呈现易于理解图表和图形中的指标。

1. 打开[Azure 门户](https://portal.azure.com)，然后导航到*mywebapp\<unique_number\>* 应用服务。

1. **概述**选项卡显示有用"眼"的信息，包括关系图显示最近的指标。

    ![屏幕截图显示概述面板](./media/monitoring/overview.png)

    * **Http 5xx**:服务器端错误，通常在 ASP.NET Core 代码中的异常的计数。
    * **中的数据**:数据入口进入你的 web 应用。
    * **输出数据量**:数据流出量从 web 应用程序向客户端。
    * **请求**:计数的 HTTP 请求。
    * **平均响应时间**:Web 应用响应 HTTP 请求的平均时间。

    此外在此页上找到多个自助服务工具进行故障排除和优化。

    ![屏幕截图显示自助服务工具](./media/monitoring/wizards.png)

    * **诊断并解决问题**是自助服务的故障排除程序。
    * **Application Insights**用于分析性能和应用程序行为，稍后在本部分中讨论。
    * **应用服务顾问**会提出建议来优化您的应用体验。

## <a name="advanced-monitoring"></a>高级监视

[Azure 监视器](/azure/monitoring-and-diagnostics/)是用于监视所有度量值以及跨 Azure 服务中设置警报的集中式的服务。 在 Azure Monitor，管理员可以精细地跟踪性能和确定趋势。 每个 Azure 服务提供其自己[度量值组](/azure/monitoring-and-diagnostics/monitoring-supported-metrics#microsoftwebsites-excluding-functions)到 Azure Monitor。

## <a name="profile-with-application-insights"></a>使用 Application Insights 配置文件

[Application Insights](/azure/application-insights/app-insights-overview)是用于分析的性能和稳定性的 web 应用和用户如何使用这些 Azure 服务。 从 Application Insights 的数据是更广泛的和更深入地与 Azure Monitor。 开发人员和管理员使用的信息用于改进应用，可以提供数据。 可以将 application Insights 添加到 Azure 应用服务资源无需更改代码。

1. 打开[Azure 门户](https://portal.azure.com)，然后导航到*mywebapp\<unique_number\>* 应用服务。
1. 从**概述**选项卡上，单击**Application Insights**磁贴。

    ![Application Insights 磁贴](./media/monitoring/app-insights.png)

1. 选择**创建新的资源**单选按钮。 使用默认资源名称，并选择 Application Insights 资源的位置。 位置不需要你的 web 应用的相匹配。

    ![Application Insights 安装](./media/monitoring/new-app-insights.png)

1. 有关**运行时/框架**，选择**ASP.NET Core**。 接受默认设置。
1. 选择“确定”。 如果系统提示你确认，请选择**继续**。
1. 创建资源后，单击要直接导航到 Application Insights 页的 Application Insights 资源的名称。

    ![新的 Application Insights 资源是准备就绪](./media/monitoring/new-app-insights-done.png)

使用应用程序时，数据累积。 选择**刷新**重新加载新数据的边栏选项卡。

![Application Insights 概述选项卡](./media/monitoring/app-insights-overview.png)

Application Insights 提供有用的服务器端的信息，而无需其他配置。 若要从 Application Insights，获取最大的价值[使用 Application Insights SDK 检测应用](/azure/application-insights/app-insights-asp-net-core)。 如果配置正确，该服务提供端对端监控跨 web 服务器和浏览器中，包括客户端的性能。 有关详细信息，请参阅[Application Insights 文档](/azure/application-insights/app-insights-overview)。

## <a name="logging"></a>日志记录

在 Azure 应用服务中的默认情况下禁用 web 服务器和应用程序日志。 启用日志通过以下步骤：

1. 打开[Azure 门户](https://portal.azure.com)，并导航到*mywebapp\<unique_number\>* 应用服务。
1. 在左侧菜单中，向下滚动到**监视**部分。 选择**诊断日志**。

    ![诊断日志链接](./media/monitoring/logging.png)

1. 开启**应用程序日志记录 （文件系统）**。 如果系统提示，请单击此框来安装扩展，使应用程序中 web 应用的日志记录。
1. 设置**Web 服务器日志记录**到**文件系统**。
1. 输入**保留期**以天为单位。 例如，30。
1. 单击“保存” 。

为 web 应用生成 ASP.NET Core 和 web 服务器 （应用服务） 日志。 可以使用显示的 FTP/FTPS 信息下载它们。 密码是之前在本指南中创建的部署凭据相同。 日志可能很[流式传输到使用 PowerShell 或 Azure CLI 在本地计算机直接](/azure/app-service/web-sites-enable-diagnostic-log#download)。 也可以是日志[Application Insights 中查看](/azure/app-service/web-sites-enable-diagnostic-log#how-to-view-logs-in-application-insights)。

## <a name="log-streaming"></a>日志流式处理

可以通过门户实时传输应用程序和 web 服务器日志。

1. 打开[Azure 门户](https://portal.azure.com)，并导航到*mywebapp\<unique_number\>* 应用服务。
1. 在左侧菜单中，向下滚动到**监视**部分，并选择**日志流**。

    ![显示日志流链接的屏幕截图](./media/monitoring/log-stream.png)

也可以是日志[流式处理通过 Azure CLI 或 Azure PowerShell](/azure/app-service/web-sites-enable-diagnostic-log#streamlogs)，其中包括通过 Cloud Shell。

## <a name="alerts"></a>警报

Azure 监视器还提供[实时警报](/azure/monitoring-and-diagnostics/insights-alerts-portal)基于度量值、 管理事件和其他条件。

> *注意：当前在 web 应用度量值均可收到警报仅在警报 （经典） 服务中。*

[警报 （经典） 服务](/azure/monitoring-and-diagnostics/monitor-quick-resource-metric-alert-portal)在 Azure Monitor 或下可以找到**监视**部分中的应用服务设置。

![警报 （经典） 链接](./media/monitoring/alerts.png)

## <a name="live-debugging"></a>实时调试

Azure 应用服务可以是[使用 Visual Studio 远程调试](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio#remotedebug)日志时不提供足够的信息。 但是，远程调试要求应用程序以使用调试符号编译。 调试不应执行在生产中，除了作为最后的手段。

## <a name="conclusion"></a>结束语

在本部分中，您将完成以下任务：

* 查找基本的监视和故障排除 Azure 门户中的数据
* 了解 Azure Monitor 如何跨所有 Azure 服务提供更深层次查看的度量值
* 使用 Application Insights 的 web 应用连接有关的应用程序分析
* 启用日志记录并了解在何处下载日志
* Stream 实时日志
* 了解在何处设置警报
* 了解远程调试 Azure 应用服务 web 应用。

## <a name="additional-reading"></a>其他阅读材料

* <xref:host-and-deploy/azure-apps/troubleshoot>
* <xref:host-and-deploy/azure-iis-errors-reference>
* [监视使用 Application Insights 的 Azure web 应用性能](/azure/application-insights/app-insights-azure-web-apps)
* [在 Azure 应用服务中为应用启用诊断日志记录](/azure/app-service/web-sites-enable-diagnostic-log)
* [使用 Visual Studio 对 Azure 应用服务中的 Web 应用进行故障排除](/azure/app-service/web-sites-dotnet-troubleshoot-visual-studio)
* [Azure Monitor 中创建经典指标警报的 Azure 服务-Azure 门户](/azure/monitoring-and-diagnostics/insights-alerts-portal)
