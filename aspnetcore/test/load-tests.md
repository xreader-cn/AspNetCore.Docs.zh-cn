---
title: ASP.NET Core 负载/压力测试
author: Jeremy-Meng
description: 介绍了几个值得注意的工具和方法可用于负载测试和压力测试 ASP.NET Core 应用。
ms.author: riande
ms.custom: mvc
ms.date: 01/04/2019
uid: test/loadtests
ms.openlocfilehash: d989bc841a372bed7ebf2c84c6abe1a57762ad04
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207351"
---
# <a name="load-and-stress-testing-aspnet-core"></a>负载测试和压力测试 ASP.NET Core

负载测试和压力测试是非常重要的 web 应用的性能和可缩放。 其目标是不同甚至它们通常共享类似测试。

**负载测试**:测试是否应用可以处理表示特定的场景中的用户指定的负载，同时仍能满足响应目标。 在正常情况下运行该应用。

**压力测试**:测试应用程序稳定性下极端条件和通常长时间运行时：

* 较高用户负载 – 峰值或逐渐增加。
* 有限的计算资源。  

在负载下，可以应用从故障中恢复并适当地返回到预期的行为？ 应用程序是在压力下*不*在正常情况下运行。

## <a name="visual-studio-tools"></a>Visual Studio Tools

Visual Studio 允许用户创建、 开发和调试 web 性能和负载测试。 一个选项，可通过 web 浏览器中录制操作创建测试。

[快速入门：创建负载测试项目](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)演示如何创建、 配置和运行负载测试项目使用 Visual Studio 2017。

有关详细信息，请参阅[其他资源](#add)。

可以配置负载测试运行中的本地或在云中使用 Azure DevOps 运行。

## <a name="azure-devops"></a>Azure DevOps

可以使用启动负载测试运行[Azure DevOps 测试计划](/azure/devops/test/load-test/index?view=vsts)服务。

![](./load-tests/_static/azure-devops-load-test.png)

该服务支持以下类型的测试格式：

- Visual Studio 测试 – Visual Studio 中创建的 web 测试。
- 在测试期间重播基于 HTTP 存档的测试 – 存档内捕获的 HTTP 流量。
- [基于 URL 的测试](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts)– 允许指定的 Url 来加载测试、 请求类型、 标头和查询字符串。 运行设置参数，如持续时间，可以配置负载模式，用户等。
- [Apache JMeter](https://jmeter.apache.org/)测试。

## <a name="azure-portal"></a>Azure 门户

[Azure 门户允许设置和运行负载测试的 Web 应用，](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)直接从 Azure 门户中的应用服务的性能选项卡。

![](./load-tests/_static/azure-appservice-perf-test.png)

测试可以是包含指定的 URL 或可以测试多个 Url 的 Visual Studio Web 测试文件的手动测试。

![](./load-tests/_static/azure-appservice-perf-test-config.png)

在测试结束时，会生成报表来显示该应用程序的性能特征。 示例统计信息包括：

- 平均响应时间
- 最大吞吐量： 每秒请求数
- 失败百分比

## <a name="third-party-tools"></a>第三方工具

以下列表包含各种功能集与第三方 web 性能工具：

- [Apache JMeter](https://jmeter.apache.org/) :负载测试工具的完整特色的套件。 线程绑定： 需要每个用户的一个线程。
- [ab 的基准测试工具的 Apache HTTP 服务器](https://httpd.apache.org/docs/2.4/programs/ab.html)
- [Gatling](https://gatling.io/) :使用 GUI 和测试的刻录机的桌面工具。 JMeter 性能更强。
- [Locust.io](https://locust.io/) :不受线程。

<a name="add"></a>
## <a name="additional-resources"></a>其他资源

[加载测试博客系列](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)作者： Charles Sterling。 日期，但大多数主题都仍适用。
