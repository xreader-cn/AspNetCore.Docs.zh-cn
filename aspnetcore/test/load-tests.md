---
title: ASP.NET Core 负载/压力测试
author: Jeremy-Meng
description: 了解 ASP.NET Core 应用的负载测试和压力测试的几个值得注意的工具和方法。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 1fd77a767fb53b9276081dd712e13108094a0382
ms.sourcegitcommit: cb6015f737b6a93127016ab0f21b58e34b624ff3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "77004287"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="7e763-103">ASP.NET Core 负载/压力测试</span><span class="sxs-lookup"><span data-stu-id="7e763-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="7e763-104">负载测试和压力测试非常重要，可确保 web 应用的性能和可缩放性。</span><span class="sxs-lookup"><span data-stu-id="7e763-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="7e763-105">即使它们通常共享类似的测试，它们的目标仍是不同的。</span><span class="sxs-lookup"><span data-stu-id="7e763-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="7e763-106">**负载测试**&ndash; 测试应用是否可以在特定情况下处理指定的用户负载，同时仍满足响应目标。</span><span class="sxs-lookup"><span data-stu-id="7e763-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="7e763-107">应用在正常情况下运行。</span><span class="sxs-lookup"><span data-stu-id="7e763-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="7e763-108">**压力测试**&ndash; 在极端条件下运行时测试应用稳定性，通常长时间内运行。</span><span class="sxs-lookup"><span data-stu-id="7e763-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="7e763-109">这些测试会使应用上的高用户负载峰值或逐渐增加负载，或限制应用程序的计算资源。</span><span class="sxs-lookup"><span data-stu-id="7e763-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="7e763-110">压力测试确定压力下的应用是否能够从故障中恢复，并正常返回到预期的行为。</span><span class="sxs-lookup"><span data-stu-id="7e763-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="7e763-111">在压力下，应用程序不会在正常状态下运行。</span><span class="sxs-lookup"><span data-stu-id="7e763-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="7e763-112">Visual Studio 2019 是包含负载测试功能的 Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="7e763-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="7e763-113">对于将来需要负载测试工具的客户，建议采用其他工具，例如 Apache JMeter、Akamai CloudTest 和 BlazeMeter。</span><span class="sxs-lookup"><span data-stu-id="7e763-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="7e763-114">有关详细信息，请参阅[Visual Studio 2019 发行说明](/visualstudio/releases/2019/release-notes-v16.0#test-tools)。</span><span class="sxs-lookup"><span data-stu-id="7e763-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="7e763-115">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="7e763-115">Visual Studio tools</span></span>

<span data-ttu-id="7e763-116">通过 Visual Studio，用户可以创建、开发和调试 web 性能和负载测试。</span><span class="sxs-lookup"><span data-stu-id="7e763-116">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="7e763-117">可以通过在 web 浏览器中记录操作来创建测试。</span><span class="sxs-lookup"><span data-stu-id="7e763-117">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="7e763-118">有关如何使用 Visual Studio 2017 创建、配置和运行负载测试项目的信息，请参阅[快速入门：创建负载测试项目](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)。</span><span class="sxs-lookup"><span data-stu-id="7e763-118">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="7e763-119">负载测试可以配置为在本地运行，也可以在云中使用 Azure DevOps 运行。</span><span class="sxs-lookup"><span data-stu-id="7e763-119">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="7e763-120">第三方工具</span><span class="sxs-lookup"><span data-stu-id="7e763-120">Third-party tools</span></span>

<span data-ttu-id="7e763-121">以下列表包含具有各种功能集的第三方 web 性能工具：</span><span class="sxs-lookup"><span data-stu-id="7e763-121">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="7e763-122">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="7e763-122">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="7e763-123">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="7e763-123">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="7e763-124">Gatling</span><span class="sxs-lookup"><span data-stu-id="7e763-124">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="7e763-125">k6</span><span class="sxs-lookup"><span data-stu-id="7e763-125">k6</span></span>](https://k6.io)
* [<span data-ttu-id="7e763-126">Locust</span><span class="sxs-lookup"><span data-stu-id="7e763-126">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="7e763-127">西风 WebSurge</span><span class="sxs-lookup"><span data-stu-id="7e763-127">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="7e763-128">Netling</span><span class="sxs-lookup"><span data-stu-id="7e763-128">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="7e763-129">Vegeta</span><span class="sxs-lookup"><span data-stu-id="7e763-129">Vegeta</span></span>](https://github.com/tsenart/vegeta)

