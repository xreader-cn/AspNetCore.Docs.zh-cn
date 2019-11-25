---
title: ASP.NET Core 负载/压力测试
author: Jeremy-Meng
description: 了解 ASP.NET Core 应用的负载测试和压力测试的几个值得注意的工具和方法。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: edaa9e873e8e489f0c560c1736f81358ca1720d0
ms.sourcegitcommit: f40c9311058c9b1add4ec043ddc5629384af6c56
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74289027"
---
# <a name="aspnet-core-loadstress-testing"></a>ASP.NET Core 负载/压力测试

负载测试和压力测试非常重要，可确保 web 应用的性能和可缩放性。 即使它们通常共享类似的测试，它们的目标仍是不同的。

**负载测试**&ndash; 测试应用是否可以在特定情况下处理指定的用户负载，同时仍满足响应目标。 应用在正常情况下运行。

**压力测试**&ndash; 在极端条件下运行时测试应用稳定性，通常长时间内运行。 这些测试会使应用上的高用户负载峰值或逐渐增加负载，或限制应用程序的计算资源。

压力测试确定压力下的应用是否能够从故障中恢复，并正常返回到预期的行为。 在压力下，应用程序不会在正常状态下运行。

Visual Studio 2019 是包含负载测试功能的 Visual Studio 的最新版本。 对于将来需要负载测试工具的客户，建议采用其他工具，例如 Apache JMeter、Akamai CloudTest 和 BlazeMeter。 有关详细信息，请参阅[Visual Studio 2019 发行说明](/visualstudio/releases/2019/release-notes-v16.0#test-tools)。

## <a name="visual-studio-tools"></a>Visual Studio Tools

通过 Visual Studio，用户可以创建、开发和调试 web 性能和负载测试。 可以通过在 web 浏览器中记录操作来创建测试。

有关如何使用 Visual Studio 2017 创建、配置和运行负载测试项目的信息，请参阅[快速入门：创建负载测试项目](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)。

负载测试可以配置为在本地运行，也可以在云中使用 Azure DevOps 运行。

## <a name="third-party-tools"></a>第三方工具

以下列表包含具有各种功能集的第三方 web 性能工具：

* [Apache JMeter](https://jmeter.apache.org/)
* [ApacheBench (ab)](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [Gatling](https://gatling.io/)
* [Locust](https://locust.io/)
* [西风 WebSurge](https://websurge.west-wind.com/)
* [Netling](https://github.com/hallatore/Netling)
* [Vegeta](https://github.com/tsenart/vegeta)
