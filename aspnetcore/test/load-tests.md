---
<span data-ttu-id="22caa-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="22caa-101">title: author: description: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="22caa-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="22caa-102">'Blazor'</span></span>
- <span data-ttu-id="22caa-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="22caa-103">'Identity'</span></span>
- <span data-ttu-id="22caa-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="22caa-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="22caa-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="22caa-105">'Razor'</span></span>
- <span data-ttu-id="22caa-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="22caa-106">'SignalR' uid:</span></span> 

---
# <a name="aspnet-core-loadstress-testing"></a><span data-ttu-id="22caa-107">ASP.NET Core 负载/压力测试</span><span class="sxs-lookup"><span data-stu-id="22caa-107">ASP.NET Core load/stress testing</span></span>

<span data-ttu-id="22caa-108">负载测试和压力测试对于确保 web 应用的性能和可缩放性非常重要。</span><span class="sxs-lookup"><span data-stu-id="22caa-108">Load testing and stress testing are important to ensure a web app is performant and scalable.</span></span> <span data-ttu-id="22caa-109">尽管它们的某些测试是相同的，但目标不同。</span><span class="sxs-lookup"><span data-stu-id="22caa-109">Their goals are different even though they often share similar tests.</span></span>

<span data-ttu-id="22caa-110">负载测试：测试应用是否可以在特定情况下处理指定的用户负载，同时仍满足响应目标。</span><span class="sxs-lookup"><span data-stu-id="22caa-110">**Load tests**: Test whether the app can handle a specified load of users for a certain scenario while still satisfying the response goal.</span></span> <span data-ttu-id="22caa-111">应用在正常状态下运行。</span><span class="sxs-lookup"><span data-stu-id="22caa-111">The app is run under normal conditions.</span></span>

<span data-ttu-id="22caa-112">压力测试：在极端条件下（通常为长时间）运行时测试应用的稳定性。</span><span class="sxs-lookup"><span data-stu-id="22caa-112">**Stress tests**: Test app stability when running under extreme conditions, often for a long period of time.</span></span> <span data-ttu-id="22caa-113">测试会对应用施加高用户负载（峰值或逐渐增加的负载）或限制应用的计算资源。</span><span class="sxs-lookup"><span data-stu-id="22caa-113">The tests place high user load, either spikes or gradually increasing load, on the app, or they limit the app's computing resources.</span></span>

<span data-ttu-id="22caa-114">压力测试可确定压力下的应用是否能够从故障中恢复，并正常返回到预期的行为。</span><span class="sxs-lookup"><span data-stu-id="22caa-114">Stress tests determine if an app under stress can recover from failure and gracefully return to expected behavior.</span></span> <span data-ttu-id="22caa-115">在压力下，应用不会在正常状态下运行。</span><span class="sxs-lookup"><span data-stu-id="22caa-115">Under stress, the app isn't run under normal conditions.</span></span>

<span data-ttu-id="22caa-116">Visual Studio 2019 是具有负载测试功能的最后一个 Visual Studio 版本。</span><span class="sxs-lookup"><span data-stu-id="22caa-116">Visual Studio 2019 is the last version of Visual Studio with load test features.</span></span> <span data-ttu-id="22caa-117">对于将来需要负载测试工具的客户，建议采用其他工具，例如 Apache JMeter、Akamai CloudTest 和 BlazeMeter。</span><span class="sxs-lookup"><span data-stu-id="22caa-117">For customers requiring load testing tools in the future, we recommend alternate tools, such as Apache JMeter, Akamai CloudTest, and BlazeMeter.</span></span> <span data-ttu-id="22caa-118">有关详细信息，请参阅 [Visual Studio 2019 发行说明](/visualstudio/releases/2019/release-notes-v16.0#test-tools)。</span><span class="sxs-lookup"><span data-stu-id="22caa-118">For more information, see the [Visual Studio 2019 Release Notes](/visualstudio/releases/2019/release-notes-v16.0#test-tools).</span></span>

## <a name="visual-studio-tools"></a><span data-ttu-id="22caa-119">Visual Studio Tools</span><span class="sxs-lookup"><span data-stu-id="22caa-119">Visual Studio tools</span></span>

<span data-ttu-id="22caa-120">通过 Visual Studio，用户可以创建、开发和调试 web 性能和负载测试。</span><span class="sxs-lookup"><span data-stu-id="22caa-120">Visual Studio allows users to create, develop, and debug web performance and load tests.</span></span> <span data-ttu-id="22caa-121">一种创建测试的方式是在 web 浏览器中记录操作。</span><span class="sxs-lookup"><span data-stu-id="22caa-121">An option is available to create tests by recording actions in a web browser.</span></span>

<span data-ttu-id="22caa-122">有关如何使用 Visual Studio 2017 创建、配置和运行负载测试项目的信息，请参阅[快速入门：创建负载测试项目](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017)。</span><span class="sxs-lookup"><span data-stu-id="22caa-122">For information on how to create, configure, and run a load test projects using Visual Studio 2017, see [Quickstart: Create a load test project](/visualstudio/test/quickstart-create-a-load-test-project?view=vs-2017).</span></span>

<span data-ttu-id="22caa-123">负载测试可以配置为在本地运行，也可以配置为使用 Azure DevOps 在云中运行。</span><span class="sxs-lookup"><span data-stu-id="22caa-123">Load tests can be configured to run on-premise or run in the cloud using Azure DevOps.</span></span>

## <a name="third-party-tools"></a><span data-ttu-id="22caa-124">第三方工具</span><span class="sxs-lookup"><span data-stu-id="22caa-124">Third-party tools</span></span>

<span data-ttu-id="22caa-125">以下列表包含具有各种功能集的第三方 web 性能工具：</span><span class="sxs-lookup"><span data-stu-id="22caa-125">The following list contains third-party web performance tools with various feature sets:</span></span>

* [<span data-ttu-id="22caa-126">Apache JMeter</span><span class="sxs-lookup"><span data-stu-id="22caa-126">Apache JMeter</span></span>](https://jmeter.apache.org/)
* [<span data-ttu-id="22caa-127">ApacheBench (ab)</span><span class="sxs-lookup"><span data-stu-id="22caa-127">ApacheBench (ab)</span></span>](https://httpd.apache.org/docs/2.4/programs/ab.html)
* [<span data-ttu-id="22caa-128">Gatling</span><span class="sxs-lookup"><span data-stu-id="22caa-128">Gatling</span></span>](https://gatling.io/)
* [<span data-ttu-id="22caa-129">k6</span><span class="sxs-lookup"><span data-stu-id="22caa-129">k6</span></span>](https://k6.io)
* [<span data-ttu-id="22caa-130">Locust</span><span class="sxs-lookup"><span data-stu-id="22caa-130">Locust</span></span>](https://locust.io/)
* [<span data-ttu-id="22caa-131">West Wind WebSurge</span><span class="sxs-lookup"><span data-stu-id="22caa-131">West Wind WebSurge</span></span>](https://websurge.west-wind.com/)
* [<span data-ttu-id="22caa-132">Netling</span><span class="sxs-lookup"><span data-stu-id="22caa-132">Netling</span></span>](https://github.com/hallatore/Netling)
* [<span data-ttu-id="22caa-133">Vegeta</span><span class="sxs-lookup"><span data-stu-id="22caa-133">Vegeta</span></span>](https://github.com/tsenart/vegeta)

