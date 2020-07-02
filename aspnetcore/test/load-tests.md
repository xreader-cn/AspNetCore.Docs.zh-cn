---
title: ASP.NET Core 负载/压力测试
author: Jeremy-Meng
description: 了解针对 ASP.NET Core 应用进行负载测试和压力测试的几种实用工具和方法。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/loadtests
ms.openlocfilehash: 5df2dd906d52aaec4fc13b07f3d92c87c802f37f
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406506"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="0bc99-103">ASP.NET Core 负载/压力测试</span><span class="sxs-lookup"><span data-stu-id="0bc99-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="0bc99-104">负载测试和压力测试对于确保 web 应用的性能和可缩放性非常重要。</span><span class="sxs-lookup"><span data-stu-id="0bc99-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="0bc99-105">尽管它们的某些测试是相同的，但目标不同。</span><span class="sxs-lookup"><span data-stu-id="0bc99-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="0bc99-106">负载测试\*\*\*\*：测试应用是否可以在特定情况下处理指定的用户负载，同时仍满足响应目标。</span><span class="sxs-lookup"><span data-stu-id="0bc99-106">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="0bc99-107">应用在正常状态下运行。</span><span class="sxs-lookup"><span data-stu-id="0bc99-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="0bc99-108">压力测试\*\*\*\*：在极端条件下（通常为长时间）运行时测试应用的稳定性。</span><span class="sxs-lookup"><span data-stu-id="0bc99-108">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="0bc99-109">测试会对应用施加高用户负载（峰值或逐渐增加的负载）或限制应用的计算资源。</span><span class="sxs-lookup"><span data-stu-id="0bc99-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="0bc99-110">压力测试可确定压力下的应用是否能够从故障中恢复，并正常返回到预期的行为。</span><span class="sxs-lookup"><span data-stu-id="0bc99-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="0bc99-111">在压力下，应用不会在正常状态下运行。</span><span class="sxs-lookup"><span data-stu-id="0bc99-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="0bc99-112">Visual Studio 2019 宣布计划[取消负载测试](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)。</span><span class="sxs-lookup"><span data-stu-id="0bc99-112">Visual Studio 2019 announced plans to [deprecate the load testing](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span> <span data-ttu-id="0bc99-113">已关闭相应的 Azure DevOps 基于云的负载测试服务。</span><span class="sxs-lookup"><span data-stu-id="0bc99-113">The corresponding Azure DevOps cloud-based load testing service has been closed.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="0bc99-114">第三方工具</span><span class="sxs-lookup"><span data-stu-id="0bc99-114">Third-party tools</span></span>

<span data-ttu-id="0bc99-115">以下列表包含具有各种功能集的第三方 web 性能工具：</span><span class="sxs-lookup"><span data-stu-id="0bc99-115">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="0bc99-116">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="0bc99-116">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="0bc99-117">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="0bc99-117">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="0bc99-118">Gatling</span><span class="sxs-lookup"><span data-stu-id="0bc99-118">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="0bc99-119">k6</span><span class="sxs-lookup"><span data-stu-id="0bc99-119">k6</span></span>](https://k6.io)
* [<span data-ttu-id="0bc99-120">Locust</span><span class="sxs-lookup"><span data-stu-id="0bc99-120">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="0bc99-121">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="0bc99-121">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="0bc99-122">Netling</span><span class="sxs-lookup"><span data-stu-id="0bc99-122">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="0bc99-123">Vegeta</span><span class="sxs-lookup"><span data-stu-id="0bc99-123">Vegeta</span></span>](https://github.com/tsenart/vegeta)