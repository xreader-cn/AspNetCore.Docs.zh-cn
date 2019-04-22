---
title: ASP.NET Core 负载/压力测试
author: Jeremy-Meng
description: 了解几个值得注意的工具和方法可用于负载测试和压力测试 ASP.NET Core 应用。
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: test/loadtests
ms.openlocfilehash: 0a8449ea2c9df0f2ac93058f03af0a1a2aa66508
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59068178"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core 负载/压力测试

负载测试和压力测试是非常重要的 web 应用的性能和可缩放。 其目标是不同的即使它们通常会共享类似测试。

**负载测试**&ndash;测试是否应用可以处理表示特定的场景中的用户指定的负载，同时仍能满足响应目标。 在正常情况下运行该应用。

**压力测试**&ndash;极端情况下，通常用于长时间运行时测试应用程序稳定性。 测试应用程序中，放置高用户负载，峰值或负载逐渐增加，或将它们限制应用的计算资源。

压力测试确定是否在高负荷下的应用可以从故障中恢复并适当地返回到预期的行为。 在负载下，应用程序不会在正常情况下运行。

Visual Studio 2019 是最后一个版本的 Visual Studio 中使用负载测试功能。 对于需要负载测试工具在将来的客户，我们建议备用工具，如 Apache JMeter、 Akamai CloudTest 和 BlazeMeter。 有关详细信息，请参阅[Visual Studio 2019 发行说明](/visualstudio/releases/2019/release-notes#test-tools)。

在 2020年中结束的负载测试中 Azure DevOps 服务。 有关详细信息，请参阅[基于云的负载测试服务生命周期结束](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)。

## <a name="visual-studio-tools"></a>Visual Studio Tools

Visual Studio 允许用户创建、 开发和调试 web 性能和负载测试。 一个选项，可通过 web 浏览器中录制操作创建测试。

有关如何创建、 配置和运行负载测试项目使用 Visual Studio 2017 的信息，请参阅[快速入门：创建负载测试项目](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)。 有关详细信息，请参阅[其他资源](#additional-resources)部分。

负载测试可以配置为使用 Azure DevOps 在云中运行的本地或运行。

## <a name="azure-devops"></a>Azure DevOps

可以使用启动负载测试运行[Azure DevOps 测试计划](/azure/devops/test/load-test/index?view=vsts)服务。

![Azure DevOps 负载测试登录页](./load-tests/_static/azure-devops-load-test.png)

该服务支持以下格式的测试：

* Visual Studio &ndash; Visual Studio 中创建的 Web 测试。
* HTTP 存档&ndash;存档内捕获的 HTTP 流量重播在测试过程。
* [基于 URL 的](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts)&ndash;允许指定的 Url 来加载测试、 请求类型、 标头和查询字符串。 运行设置参数，如持续时间，负载模式，且用户数量可以配置。
* [Apache JMeter](https://jmeter.apache.org/)。

## <a name="azure-portal"></a>Azure 门户

[Azure 门户允许设置和运行负载测试的 web 应用](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)直接从**性能**应用服务在 Azure 门户中的选项卡。

![在 Azure 门户中的 azure 应用服务](./load-tests/_static/azure-appservice-perf-test.png)

测试可以是包含指定的 URL 或可以测试多个 Url 的 Visual Studio Web 测试文件的手动测试。

![Azure 门户上的新性能测试页](./load-tests/_static/azure-appservice-perf-test-config.png)

在测试结束时，生成的报告显示应用的性能特征。 示例统计信息包括：

* 平均响应时间
* 最大吞吐量： 每秒请求数
* 失败百分比

## <a name="third-party-tools"></a>第三方工具

以下列表包含各种功能集与第三方 web 性能工具：

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Locust](https://locust.io/)
* [West Wind WebSurge](http://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)

## <a name="additional-resources"></a>其他资源

* [加载测试博客系列](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
