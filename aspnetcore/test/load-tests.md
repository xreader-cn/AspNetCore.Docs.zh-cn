---
title: ASP.NET Core 负载/压力测试
author: Jeremy-Meng
description: 了解 ASP.NET Core 应用的负载测试和压力测试的几个值得注意的工具和方法。
ms.author: riande
ms.custom: mvc
ms.date: 4/05/2019
uid: test/loadtests
ms.openlocfilehash: 7a9dfc1fedf747ab26daa573b61ed01c31709058
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975246"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="4b8e5-103">ASP.NET Core 负载/压力测试</span><span class="sxs-lookup"><span data-stu-id="4b8e5-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="4b8e5-104">负载测试和压力测试非常重要, 可确保 web 应用的性能和可缩放性。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="4b8e5-105">即使它们通常共享类似的测试, 它们的目标仍是不同的。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="4b8e5-106">**负载测试**&ndash;测试应用程序是否可以在特定情况下处理指定的用户负载, 同时仍满足响应目标。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="4b8e5-107">应用在正常情况下运行。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="4b8e5-108">**压力测试**&ndash;在极端条件下运行时测试应用稳定性, 通常长时间内运行。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="4b8e5-109">这些测试会使应用上的高用户负载峰值或逐渐增加负载, 或限制应用程序的计算资源。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="4b8e5-110">压力测试确定压力下的应用是否能够从故障中恢复, 并正常返回到预期的行为。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="4b8e5-111">在压力下, 应用程序不会在正常状态下运行。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="4b8e5-112">Visual Studio 2019 是包含负载测试功能的 Visual Studio 的最新版本。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="4b8e5-113">对于将来需要负载测试工具的客户, 建议采用其他工具, 例如 Apache JMeter、Akamai CloudTest 和 BlazeMeter。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="4b8e5-114">有关详细信息, 请参阅[Visual Studio 2019 发行说明](/visualstudio/releases/2019/release-notes-v16.0#test-tools)。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

<span data-ttu-id="4b8e5-115">Azure DevOps 中的负载测试服务结束于2020。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="4b8e5-116">有关详细信息, 请参阅[基于云的负载测试服务生存期](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="4b8e5-117">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="4b8e5-117">Visual Studio tools</span></span>

<span data-ttu-id="4b8e5-118">通过 Visual Studio, 用户可以创建、开发和调试 web 性能和负载测试。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="4b8e5-119">可以通过在 web 浏览器中记录操作来创建测试。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="4b8e5-120">有关如何使用 Visual Studio 2017 创建、配置和运行负载测试项目的信息, 请参阅[快速入门:创建负载测试项目](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="4b8e5-121">负载测试可以配置为在本地运行, 也可以在云中使用 Azure DevOps 运行。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-121">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="4b8e5-122">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="4b8e5-122">Azure DevOps</span></span>

<span data-ttu-id="4b8e5-123">可以使用[Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts)服务启动负载测试运行。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-123">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Azure DevOps 负载测试登陆页](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="4b8e5-125">该服务支持以下测试格式:</span><span class="sxs-lookup"><span data-stu-id="4b8e5-125">The service supports the following test formats:</span></span>

* <span data-ttu-id="4b8e5-126">Visual studio &ndash; Web 测试在 visual studio 中创建。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-126">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="4b8e5-127">在测试&ndash;期间, 会重播在存档内捕获的 http 存档 http 流量。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-127">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="4b8e5-128">[基于 URL](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts)&ndash;允许指定 url 以加载测试、请求类型、标头和查询字符串。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-128">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="4b8e5-129">可以配置运行设置参数, 如持续时间、负载模式和用户数。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-129">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="4b8e5-130">[Apache JMeter](https://jmeter.apache.org/)。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-130">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="4b8e5-131">Azure 门户</span><span class="sxs-lookup"><span data-stu-id="4b8e5-131">Azure portal</span></span>

<span data-ttu-id="4b8e5-132">Azure 门户允许直接从 Azure 门户中应用服务的 "**性能**" 选项卡[设置和运行 web 应用的负载测试](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-132">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![Azure 门户中的 Azure App Service](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="4b8e5-134">测试可以是具有指定 URL 或 Visual Studio Web 测试文件的手动测试, 可测试多个 Url。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-134">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Azure 门户上的新性能测试页](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="4b8e5-136">在测试结束时, 生成的报表显示应用的性能特征。</span><span class="sxs-lookup"><span data-stu-id="4b8e5-136">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="4b8e5-137">示例统计信息包括:</span><span class="sxs-lookup"><span data-stu-id="4b8e5-137">Example statistics include:</span></span>

* <span data-ttu-id="4b8e5-138">平均响应时间</span><span class="sxs-lookup"><span data-stu-id="4b8e5-138">Average response time</span></span>
* <span data-ttu-id="4b8e5-139">最大吞吐量: 每秒请求数</span><span class="sxs-lookup"><span data-stu-id="4b8e5-139">Max throughput: requests per second</span></span>
* <span data-ttu-id="4b8e5-140">失败百分比</span><span class="sxs-lookup"><span data-stu-id="4b8e5-140">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="4b8e5-141">第三方工具</span><span class="sxs-lookup"><span data-stu-id="4b8e5-141">Third-party tools</span></span>

<span data-ttu-id="4b8e5-142">以下列表包含具有各种功能集的第三方 web 性能工具:</span><span class="sxs-lookup"><span data-stu-id="4b8e5-142">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="4b8e5-143">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="4b8e5-143">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="4b8e5-144">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="4b8e5-144">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="4b8e5-145">Gatling</span><span class="sxs-lookup"><span data-stu-id="4b8e5-145">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="4b8e5-146">Locust</span><span class="sxs-lookup"><span data-stu-id="4b8e5-146">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="4b8e5-147">西风 WebSurge</span><span class="sxs-lookup"><span data-stu-id="4b8e5-147">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="4b8e5-148">Netling</span><span class="sxs-lookup"><span data-stu-id="4b8e5-148">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="4b8e5-149">Vegeta</span><span class="sxs-lookup"><span data-stu-id="4b8e5-149">Vegeta</span></span>](https://github.com/tsenart/vegeta)
