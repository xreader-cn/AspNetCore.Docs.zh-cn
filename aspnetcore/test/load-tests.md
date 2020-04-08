---
title: ASP.NET Core 负载/压力测试
author: Jeremy-Meng
description: 了解针对 ASP.NET Core 应用进行负载测试和压力测试的几种实用工具和方法。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78649638"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core 负载/压力测试

负载测试和压力测试对于确保 web 应用的性能和可缩放性非常重要。 尽管它们的某些测试是相同的，但目标不同。

**负载测试** &ndash; 测试应用是否可以在特定情况下处理指定的用户负载，同时仍满足响应目标。 应用在正常状态下运行。

**压力测试** &ndash; 在极端条件下（通常为长时间）运行时测试应用的稳定性。 测试会对应用施加高用户负载（峰值或逐渐增加的负载）或限制应用的计算资源。

压力测试可确定压力下的应用是否能够从故障中恢复，并正常返回到预期的行为。 在压力下，应用不会在正常状态下运行。

Visual Studio 2019 是具有负载测试功能的最后一个 Visual Studio 版本。 对于将来需要负载测试工具的客户，建议采用其他工具，例如 Apache JMeter、Akamai CloudTest 和 BlazeMeter。 有关详细信息，请参阅 [Visual Studio 2019 发行说明](/visualstudio/releases/2019/release-notes-v16.0#test-tools)。

## <a name="visual-studio-tools"></a>Visual Studio Tools

通过 Visual Studio，用户可以创建、开发和调试 web 性能和负载测试。 一种创建测试的方式是在 web 浏览器中记录操作。

有关如何使用 Visual Studio 2017 创建、配置和运行负载测试项目的信息，请参阅[快速入门：创建负载测试项目](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)。

负载测试可以配置为在本地运行，也可以配置为使用 Azure DevOps 在云中运行。

## <a name="third-party-tools"></a>第三方工具

以下列表包含具有各种功能集的第三方 web 性能工具：

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [k6](https://k6.io)
* [Locust](https://locust.io/)
* [West Wind WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)

