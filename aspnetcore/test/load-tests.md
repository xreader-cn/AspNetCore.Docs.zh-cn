---
title: ASP.NET Core 负载/压力测试
author: Jeremy-Meng
description: 了解几个值得注意的工具和方法可用于负载测试和压力测试 ASP.NET Core 应用。
ms.author: riande
ms.custom: mvc
ms.date: 04/05/2019
uid: test/loadtests
ms.openlocfilehash: 3c21da6c799bc3080a1a16cb62ae4535b8890a1b
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724488"
---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="35172-103">ASP.NET Core 负载/压力测试</span><span class="sxs-lookup"><span data-stu-id="35172-103">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="35172-104">负载测试和压力测试是非常重要的 web 应用的性能和可缩放。</span><span class="sxs-lookup"><span data-stu-id="35172-104">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="35172-105">其目标是不同的即使它们通常会共享类似测试。</span><span class="sxs-lookup"><span data-stu-id="35172-105">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="35172-106">**负载测试**&ndash;测试是否应用可以处理表示特定的场景中的用户指定的负载，同时仍能满足响应目标。</span><span class="sxs-lookup"><span data-stu-id="35172-106">**Load tests** &ndash; Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="35172-107">在正常情况下运行该应用。</span><span class="sxs-lookup"><span data-stu-id="35172-107">The app is run under normal conditions.</span></span>

<span data-ttu-id="35172-108">**压力测试**&ndash;极端情况下，通常用于长时间运行时测试应用程序稳定性。</span><span class="sxs-lookup"><span data-stu-id="35172-108">**Stress tests** &ndash; Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="35172-109">测试应用程序中，放置高用户负载，峰值或负载逐渐增加，或将它们限制应用的计算资源。</span><span class="sxs-lookup"><span data-stu-id="35172-109">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="35172-110">压力测试确定是否在高负荷下的应用可以从故障中恢复并适当地返回到预期的行为。</span><span class="sxs-lookup"><span data-stu-id="35172-110">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="35172-111">在负载下，应用程序不会在正常情况下运行。</span><span class="sxs-lookup"><span data-stu-id="35172-111">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="35172-112">Visual Studio 2019 是最后一个版本的 Visual Studio 中使用负载测试功能。</span><span class="sxs-lookup"><span data-stu-id="35172-112">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="35172-113">对于需要负载测试工具在将来的客户，我们建议备用工具，如 Apache JMeter、 Akamai CloudTest 和 BlazeMeter。</span><span class="sxs-lookup"><span data-stu-id="35172-113">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="35172-114">有关详细信息，请参阅[Visual Studio 2019 发行说明](/visualstudio/releases/2019/release-notes#test-tools)。</span><span class="sxs-lookup"><span data-stu-id="35172-114">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes#test-tools).</span></span>

<span data-ttu-id="35172-115">在 2020年中结束的负载测试中 Azure DevOps 服务。</span><span class="sxs-lookup"><span data-stu-id="35172-115">The load testing service in Azure DevOps is ending in 2020.</span></span> <span data-ttu-id="35172-116">有关详细信息，请参阅[基于云的负载测试服务生命周期结束](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/)。</span><span class="sxs-lookup"><span data-stu-id="35172-116">For more information, see [Cloud-based load testing service end of life](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="35172-117">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="35172-117">Visual Studio tools</span></span>

<span data-ttu-id="35172-118">Visual Studio 允许用户创建、 开发和调试 web 性能和负载测试。</span><span class="sxs-lookup"><span data-stu-id="35172-118">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="35172-119">一个选项，可通过 web 浏览器中录制操作创建测试。</span><span class="sxs-lookup"><span data-stu-id="35172-119">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="35172-120">有关如何创建、 配置和运行负载测试项目使用 Visual Studio 2017 的信息，请参阅[快速入门：创建负载测试项目](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)。</span><span class="sxs-lookup"><span data-stu-id="35172-120">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span> <span data-ttu-id="35172-121">有关详细信息，请参阅[其他资源](#additional-resources)部分。</span><span class="sxs-lookup"><span data-stu-id="35172-121">For more information, see the [Additional resources](#additional-resources) section.</span></span>

<span data-ttu-id="35172-122">负载测试可以配置为使用 Azure DevOps 在云中运行的本地或运行。</span><span class="sxs-lookup"><span data-stu-id="35172-122">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="azure-devops"></a><span data-ttu-id="35172-123">Azure DevOps</span><span class="sxs-lookup"><span data-stu-id="35172-123">Azure DevOps</span></span>

<span data-ttu-id="35172-124">可以使用启动负载测试运行[Azure DevOps 测试计划](/azure/devops/test/load-test/index?view=vsts)服务。</span><span class="sxs-lookup"><span data-stu-id="35172-124">Load test runs can be started using the [Azure DevOps Test Plans](/azure/devops/test/load-test/index?view=vsts) service.</span></span>

![Azure DevOps 负载测试登录页](./load-tests/_static/azure-devops-load-test.png)

<span data-ttu-id="35172-126">该服务支持以下格式的测试：</span><span class="sxs-lookup"><span data-stu-id="35172-126">The service supports the following test formats:</span></span>

* <span data-ttu-id="35172-127">Visual Studio &ndash; Visual Studio 中创建的 Web 测试。</span><span class="sxs-lookup"><span data-stu-id="35172-127">Visual Studio &ndash; Web test created in Visual Studio.</span></span>
* <span data-ttu-id="35172-128">HTTP 存档&ndash;存档内捕获的 HTTP 流量重播在测试过程。</span><span class="sxs-lookup"><span data-stu-id="35172-128">HTTP Archive &ndash; Captured HTTP traffic inside archive is replayed during testing.</span></span>
* <span data-ttu-id="35172-129">[基于 URL 的](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts)&ndash;允许指定的 Url 来加载测试、 请求类型、 标头和查询字符串。</span><span class="sxs-lookup"><span data-stu-id="35172-129">[URL-based](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=vsts) &ndash; Allows specifying URLs to load test, request types, headers, and query strings.</span></span> <span data-ttu-id="35172-130">运行设置参数，如持续时间，负载模式，且用户数量可以配置。</span><span class="sxs-lookup"><span data-stu-id="35172-130">Run setting parameters such as duration, load pattern, and number of users can be configured.</span></span>
* <span data-ttu-id="35172-131">[Apache JMeter](https://jmeter.apache.org/)。</span><span class="sxs-lookup"><span data-stu-id="35172-131">[Apache JMeter](https://jmeter.apache.org/).</span></span>

## <a name="azure-portal"></a><span data-ttu-id="35172-132">Azure 门户</span><span class="sxs-lookup"><span data-stu-id="35172-132">Azure portal</span></span>

<span data-ttu-id="35172-133">[Azure 门户允许设置和运行负载测试的 web 应用](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts)直接从**性能**应用服务在 Azure 门户中的选项卡。</span><span class="sxs-lookup"><span data-stu-id="35172-133">[Azure portal allows setting up and running load testing of web apps](/azure/devops/test/load-test/app-service-web-app-performance-test?view=vsts) directly from the **Performance** tab of the App Service in Azure portal.</span></span>

![在 Azure 门户中的 azure 应用服务](./load-tests/_static/azure-appservice-perf-test.png)

<span data-ttu-id="35172-135">测试可以是包含指定的 URL 或可以测试多个 Url 的 Visual Studio Web 测试文件的手动测试。</span><span class="sxs-lookup"><span data-stu-id="35172-135">The test can be a manual test with a specified URL or a Visual Studio Web Test file, which can test multiple URLs.</span></span>

![Azure 门户上的新性能测试页](./load-tests/_static/azure-appservice-perf-test-config.png)

<span data-ttu-id="35172-137">在测试结束时，生成的报告显示应用的性能特征。</span><span class="sxs-lookup"><span data-stu-id="35172-137">At end of the test, generated reports show the performance characteristics of the app.</span></span> <span data-ttu-id="35172-138">示例统计信息包括：</span><span class="sxs-lookup"><span data-stu-id="35172-138">Example statistics include:</span></span>

* <span data-ttu-id="35172-139">平均响应时间</span><span class="sxs-lookup"><span data-stu-id="35172-139">Average response time</span></span>
* <span data-ttu-id="35172-140">最大吞吐量： 每秒请求数</span><span class="sxs-lookup"><span data-stu-id="35172-140">Max throughput: requests per second</span></span>
* <span data-ttu-id="35172-141">失败百分比</span><span class="sxs-lookup"><span data-stu-id="35172-141">Failure percentage</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="35172-142">第三方工具</span><span class="sxs-lookup"><span data-stu-id="35172-142">Third-party tools</span></span>

<span data-ttu-id="35172-143">以下列表包含各种功能集与第三方 web 性能工具：</span><span class="sxs-lookup"><span data-stu-id="35172-143">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="35172-144">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="35172-144">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="35172-145">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="35172-145">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="35172-146">Gatling</span><span class="sxs-lookup"><span data-stu-id="35172-146">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="35172-147">Locust</span><span class="sxs-lookup"><span data-stu-id="35172-147">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="35172-148">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="35172-148">West Wind WebSurge</span></span>](http://websurge.west-wind.com/)
* [<span data-ttu-id="35172-149">Netling</span><span class="sxs-lookup"><span data-stu-id="35172-149">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="35172-150">Vegeta</span><span class="sxs-lookup"><span data-stu-id="35172-150">Vegeta</span></span>](https://github.com/tsenart/vegeta)

## <a name="additional-resources"></a><span data-ttu-id="35172-151">其他资源</span><span class="sxs-lookup"><span data-stu-id="35172-151">Additional resources</span></span>

* [<span data-ttu-id="35172-152">加载测试博客系列</span><span class="sxs-lookup"><span data-stu-id="35172-152">Load Test blog series</span></span>](https://blogs.msdn.microsoft.com/charles_sterling/2015/06/01/load-test-series-part-i-creating-web-performance-tests-for-a-load-test/)
