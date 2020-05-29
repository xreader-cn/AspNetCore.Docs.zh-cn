---
<span data-ttu-id="cdcc1-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-102">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-103">'Identity'</span></span>
- <span data-ttu-id="cdcc1-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-105">'Razor'</span></span>
- <span data-ttu-id="cdcc1-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-106">'SignalR' uid:</span></span> 

---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="cdcc1-107">ASP.NET Core 中的集成测试</span><span class="sxs-lookup"><span data-stu-id="cdcc1-107">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="cdcc1-108">作者：[Javier Calvarro Nelson](https://github.com/javiercn)[Steve Smith](https://ardalis.com/) 和 [Jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-108">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cdcc1-109">集成测试可在包含应用支持基础结构（如数据库、文件系统和网络）的级别上确保应用组件功能正常。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-109">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="cdcc1-110">ASP.NET Core 通过将单元测试框架与测试 Web 主机和内存中测试服务器结合使用来支持集成测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-110">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="cdcc1-111">本主题假设读者基本了解单元测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-111">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="cdcc1-112">如果不熟悉测试概念，请参阅 [.NET Core 和 .NET Standard 中的单元测试](/dotnet/core/testing/)主题及其链接内容。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-112">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="cdcc1-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="cdcc1-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cdcc1-114">示例应用是 Razor Pages 应用，假设读者基本了解 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-114">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="cdcc1-115">如果不熟悉 Razor Pages，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-115">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="cdcc1-116">[Razor Pages 简介](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-116">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="cdcc1-117">[Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-117">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="cdcc1-118">[Razor Pages 单元测试](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-118">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="cdcc1-119">对于测试 SPA，建议使用可以自动执行浏览器的工具，如 [Selenium](https://www.seleniumhq.org/)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-119">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="cdcc1-120">集成测试简介</span><span class="sxs-lookup"><span data-stu-id="cdcc1-120">Introduction to integration tests</span></span>

<span data-ttu-id="cdcc1-121">与[单元测试](/dotnet/core/testing/)相比，集成测试可在更广泛的级别上评估应用的组件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-121">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="cdcc1-122">单元测试用于测试独立软件组件，如单独的类方法。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-122">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="cdcc1-123">集成测试确认两个或更多应用组件一起工作以生成预期结果，可能包括完整处理请求所需的每个组件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-123">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="cdcc1-124">这些更广泛的测试用于测试应用的基础结构和整个框架，通常包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-124">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="cdcc1-125">数据库</span><span class="sxs-lookup"><span data-stu-id="cdcc1-125">Database</span></span>
* <span data-ttu-id="cdcc1-126">文件系统</span><span class="sxs-lookup"><span data-stu-id="cdcc1-126">File system</span></span>
* <span data-ttu-id="cdcc1-127">网络设备</span><span class="sxs-lookup"><span data-stu-id="cdcc1-127">Network appliances</span></span>
* <span data-ttu-id="cdcc1-128">请求-响应管道</span><span class="sxs-lookup"><span data-stu-id="cdcc1-128">Request-response pipeline</span></span>

<span data-ttu-id="cdcc1-129">单元测试使用称为 fake 或 mock 对象的制造组件，而不是基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-129">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="cdcc1-130">与单元测试相比，集成测试：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-130">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="cdcc1-131">使用应用在生产环境中使用的实际组件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-131">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="cdcc1-132">需要进行更多代码和数据处理。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-132">Require more code and data processing.</span></span>
* <span data-ttu-id="cdcc1-133">需要更长时间来运行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-133">Take longer to run.</span></span>

<span data-ttu-id="cdcc1-134">因此，将集成测试的使用限制为最重要的基础结构方案。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-134">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="cdcc1-135">如果可以使用单元测试或集成测试来测试行为，请选择单元测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-135">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="cdcc1-136">请勿为通过数据库和文件系统进行的数据和文件访问的每个可能排列编写集成测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-136">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="cdcc1-137">无论应用中有多少位置与数据库和文件系统交互，一组集中式读取、写入、更新和删除集成测试通常能够充分测试数据库和文件系统组件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-137">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="cdcc1-138">将单元测试用于与这些组件交互的方法逻辑的例程测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-138">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="cdcc1-139">在单元测试中，使用基础结构 fake/mock 会导致更快地执行测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-139">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="cdcc1-140">在集成测试的讨论中，测试的项目经常称为“测试中的系统”，简称“SUT”。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-140">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="cdcc1-141">本主题中使用“SUT”来指代测试的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-141">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="cdcc1-142">ASP.NET Core 集成测试</span><span class="sxs-lookup"><span data-stu-id="cdcc1-142">ASP.NET Core integration tests</span></span>

<span data-ttu-id="cdcc1-143">ASP.NET Core 中的集成测试需要以下内容：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-143">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="cdcc1-144">测试项目用于包含和执行测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-144">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="cdcc1-145">测试项目具有对 SUT 的引用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-145">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="cdcc1-146">测试项目为 SUT 创建测试 Web 主机，并使用测试服务器客户端处理 SUT 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-146">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="cdcc1-147">测试运行程序用于执行测试并报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-147">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="cdcc1-148">集成测试后跟一系列事件，包括常规“排列”、“操作”和“断言”测试步骤：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-148">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="cdcc1-149">已配置 SUT 的 Web 主机。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-149">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="cdcc1-150">创建测试服务器客户端以向应用提交请求。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-150">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="cdcc1-151">执行“排列”测试步骤：测试应用会准备请求。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-151">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="cdcc1-152">执行“操作”测试步骤：客户端提交请求并接收响应。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-152">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="cdcc1-153">执行“断言”测试步骤：实际响应基于预期响应验证为通过或失败。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-153">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="cdcc1-154">该过程会一直继续，直到执行了所有测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-154">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="cdcc1-155">报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-155">The test results are reported.</span></span>

<span data-ttu-id="cdcc1-156">通常，测试 Web 主机的配置与用于测试运行的应用常规 Web 主机不同。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-156">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="cdcc1-157">例如，可以将不同的数据库或不同的应用设置用于测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-157">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="cdcc1-158">基础结构组件（如测试 Web 主机和内存中测试服务器 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))）由 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 包提供或管理。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-158">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="cdcc1-159">使用此包可简化测试创建和执行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-159">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="cdcc1-160">`Microsoft.AspNetCore.Mvc.Testing` 包处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-160">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="cdcc1-161">将依赖项文件 (.deps) 从 SUT 复制到测试项目的 bin 目录中。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-161">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="cdcc1-162">将[内容根目录](xref:fundamentals/index#content-root)设置为 SUT 的项目根目录，以便可在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-162">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="cdcc1-163">提供 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 类，以简化 SUT 在 `TestServer` 中的启动过程。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-163">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="cdcc1-164">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍如何设置测试项目和测试运行程序，以及有关如何运行测试的详细说明与有关如何命名测试和测试类的建议。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-164">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="cdcc1-165">为应用创建测试项目时，请将集成测试中的单元测试分隔到不同的项目中。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-165">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="cdcc1-166">这可帮助确保不会意外地将基础结构测试组件包含在单元测试中。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-166">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="cdcc1-167">通过分隔单元测试和集成测试还可以控制运行的测试集。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-167">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="cdcc1-168">Razor Pages 应用与 MVC 应用的测试配置之间几乎没有任何区别。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-168">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="cdcc1-169">唯一的区别在于测试的命名方式。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-169">The only difference is in how the tests are named.</span></span> <span data-ttu-id="cdcc1-170">在 Razor Pages 应用中，页面终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 用于为索引页面测试组件集成）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-170">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="cdcc1-171">在 MVC 应用中，测试通常按控制器类进行组织，并以它们所测试的控制器来命令（例如 `HomeControllerTests` 用于为主页控制器测试组件集成）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-171">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="cdcc1-172">测试应用先决条件</span><span class="sxs-lookup"><span data-stu-id="cdcc1-172">Test app prerequisites</span></span>

<span data-ttu-id="cdcc1-173">测试项目必须：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-173">The test project must:</span></span>

* <span data-ttu-id="cdcc1-174">引用 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-174">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="cdcc1-175">在项目文件中指定 Web SDK (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-175">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="cdcc1-176">可以在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-176">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="cdcc1-177">检查 tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-177">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="cdcc1-178">示例应用使用 [xUnit](https://xunit.github.io/) 测试框架和 [AngleSharp](https://anglesharp.github.io/) 分析程序库，因此示例应用还引用：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-178">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="cdcc1-179">xunit</span><span class="sxs-lookup"><span data-stu-id="cdcc1-179">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="cdcc1-180">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="cdcc1-180">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="cdcc1-181">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="cdcc1-181">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="cdcc1-182">还在测试中使用了 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-182">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="cdcc1-183">应用引用：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-183">The app references:</span></span>

* [<span data-ttu-id="cdcc1-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="cdcc1-184">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* <span data-ttu-id="cdcc1-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-185">[Microsoft.AspNetCore.Identity.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)</span></span>
* [<span data-ttu-id="cdcc1-186">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="cdcc1-186">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="cdcc1-187">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="cdcc1-187">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="cdcc1-188">Microsoft.EntityFrameworkCore.Tools</span><span class="sxs-lookup"><span data-stu-id="cdcc1-188">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="cdcc1-189">SUT 环境</span><span class="sxs-lookup"><span data-stu-id="cdcc1-189">SUT environment</span></span>

<span data-ttu-id="cdcc1-190">如果未设置 SUT 的[环境](xref:fundamentals/environments)，则环境会默认为“开发”。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-190">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="cdcc1-191">使用默认 WebApplicationFactory 的基本测试</span><span class="sxs-lookup"><span data-stu-id="cdcc1-191">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="cdcc1-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 用于为集成测试创建 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-192">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="cdcc1-193">`TEntryPoint` 是 SUT 的入口点类，通常是 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-193">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="cdcc1-194">测试类实现一个类固定例程接口 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))，以指示类包含测试，并在类中的所有测试间提供共享对象实例。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-194">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="cdcc1-195">以下测试类 `BasicTests` 使用 `WebApplicationFactory` 启动 SUT，并向测试方法 `Get_EndpointsReturnSuccessAndCorrectContentType` 提供 [HttpClient](/dotnet/api/system.net.http.httpclient)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-195">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="cdcc1-196">该方法检查响应状态代码是否成功（处于范围 200-299 中的状态代码），以及 `Content-Type` 标头是否为适用于多个应用页面的 `text/html; charset=utf-8`。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-196">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="cdcc1-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 创建自动追随重定向并处理 cookie 的 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-197">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="cdcc1-198">默认情况下，在启用了 [GDPR 同意策略](xref:security/gdpr)时，不会在请求间保留非必要 cookie。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-198">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="cdcc1-199">若要保留非必要 cookie（如 TempData 提供程序使用的 cookie），请在测试中将它们标记为必要。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-199">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="cdcc1-200">有关将 cookie 标记为必要的说明，请参阅[必要 cookie](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-200">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="cdcc1-201">自定义 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="cdcc1-201">Customize WebApplicationFactory</span></span>

<span data-ttu-id="cdcc1-202">通过从 `WebApplicationFactory` 来创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-202">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="cdcc1-203">从 `WebApplicationFactory` 继承并替代 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-203">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="cdcc1-204">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 允许使用 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) 配置服务集合：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-204">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="cdcc1-205">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-205">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="cdcc1-206">[集成测试示例：测试应用组织](#test-app-organization)部分中介绍了该方法。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-206">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="cdcc1-207">SUT 的数据库上下文在其 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-207">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cdcc1-208">测试应用的 `builder.ConfigureServices` 回调在执行应用的 `Startup.ConfigureServices` 代码之后执行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-208">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="cdcc1-209">随着 ASP.NET Core 3.0 的发布，执行顺序是针对[泛型主机](xref:fundamentals/host/generic-host)的一个重大更改。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-209">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="cdcc1-210">若要将与应用数据库不同的数据库用于测试，必须在 `builder.ConfigureServices` 中替换应用的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-210">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="cdcc1-211">对于仍使用 [Web Host}(xref:fundamentals/host/web-host) 的 SUT，将在 SUT 的 `Startup.ConfigureServices` 代码之前执行测试应用的 `builder.ConfigureServices` 回调。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-211">For SUTs that still use the [Web Host}(xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="cdcc1-212">之后执行测试应用的 `builder.ConfigureTestServices` 回调。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-212">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

   <span data-ttu-id="cdcc1-213">示例应用会查找数据库上下文的服务描述符，并使用该描述符删除服务注册。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-213">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="cdcc1-214">接下来，工厂会添加一个新 `ApplicationDbContext`，它使用内存中数据库进行测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-214">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="cdcc1-215">若要连接到与内存中数据库不同的数据库，请更改 `UseInMemoryDatabase` 调用以将上下文连接到不同数据库。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-215">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="cdcc1-216">使用 SQL Server 测试数据库：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-216">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="cdcc1-217">在项目文件中引用 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-217">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="cdcc1-218">使用连接到数据库的连接字符串调用 `UseSqlServer`。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-218">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="cdcc1-219">在测试类中使用自定义 `CustomWebApplicationFactory`。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-219">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="cdcc1-220">下面的示例使用 `IndexPageTests` 类中的工厂：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-220">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="cdcc1-221">示例应用的客户端配置为阻止 `HttpClient` 追随重定向。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-221">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="cdcc1-222">如稍后在[模拟身份验证](#mock-authentication)部分中所述，这允许测试检查应用第一个响应的结果。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-222">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="cdcc1-223">第一个响应是在许多具有 `Location` 标头的测试中进行重定向。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-223">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="cdcc1-224">典型测试使用 `HttpClient` 和帮助程序方法处理请求和响应：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-224">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="cdcc1-225">对 SUT 发出的任何 POST 请求都必须满足防伪检查，该检查由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动执行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-225">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="cdcc1-226">若要安排测试的 POST 请求，测试应用必须：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-226">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="cdcc1-227">对页面发出请求。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-227">Make a request for the page.</span></span>
1. <span data-ttu-id="cdcc1-228">分析来自响应的防伪 cookie 和请求验证令牌。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-228">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="cdcc1-229">发出放置了防伪 cookie 和请求验证令牌的 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-229">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="cdcc1-230">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 `SendAsync` 帮助程序扩展方法 (Helpers/HttpClientExtensions.cs) 和 `GetDocumentAsync` 帮助程序方法 (Helpers/HtmlHelpers.cs) 使用 [AngleSharp](https://anglesharp.github.io/) 分析程序，通过以下方法处理防伪检查：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-230">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="cdcc1-231">`GetDocumentAsync` &ndash; 接收 [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 并返回 `IHtmlDocument`。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-231">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="cdcc1-232">`GetDocumentAsync` 使用一个基于原始 `HttpResponseMessage` 准备虚拟响应的工厂。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-232">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="cdcc1-233">有关详细信息，请参阅 [AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-233">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="cdcc1-234">`HttpClient` 的 `SendAsync` 扩展方法撰写 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) 并调用 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)，以向 SUT 提交请求。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-234">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="cdcc1-235">`SendAsync` 的重载接受 HTML 窗体 (`IHtmlFormElement`) 和以下内容：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-235">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="cdcc1-236">窗体的“提交”按钮 (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-236">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="cdcc1-237">窗体值集合 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-237">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="cdcc1-238">提交按钮 (`IHtmlElement`) 和窗体值 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-238">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="cdcc1-239">[AngleSharp](https://anglesharp.github.io/) 是在本主题和示例应用中用于演示的第三方分析库。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-239">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="cdcc1-240">ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-240">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="cdcc1-241">可以使用其他分析程序，如 [Html Agility Pack (HAP)](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-241">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="cdcc1-242">另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-242">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="cdcc1-243">使用 WithWebHostBuilder 自定义客户端</span><span class="sxs-lookup"><span data-stu-id="cdcc1-243">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="cdcc1-244">当测试方法中需要其他配置时，[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 可创建新 `WebApplicationFactory`，其中包含通过配置进一步自定义的 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-244">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="cdcc1-245">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 的 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 测试方法演示了 `WithWebHostBuilder` 的使用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-245">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="cdcc1-246">此测试通过在 SUT 中触发窗体提交，在数据库中执行记录删除。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-246">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="cdcc1-247">由于 `IndexPageTests` 类中的另一个测试会执行删除数据库中所有记录的操作，并且可能在 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 方法之前运行，因此数据库会在此测试方法中重新进行种子设定，以确保存在记录供 SUT 删除。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-247">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="cdcc1-248">在 SUT 中选择 `messages` 窗体的第一个删除按钮可在向 SUT 发出的请求中进行模拟：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-248">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="cdcc1-249">客户端选项</span><span class="sxs-lookup"><span data-stu-id="cdcc1-249">Client options</span></span>

<span data-ttu-id="cdcc1-250">下表显示在创建 `HttpClient` 实例时可用的默认 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-250">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="cdcc1-251">选项</span><span class="sxs-lookup"><span data-stu-id="cdcc1-251">Option</span></span> | <span data-ttu-id="cdcc1-252">描述</span><span class="sxs-lookup"><span data-stu-id="cdcc1-252">Description</span></span> | <span data-ttu-id="cdcc1-253">默认</span><span class="sxs-lookup"><span data-stu-id="cdcc1-253">Default</span></span> |
| ---
<span data-ttu-id="cdcc1-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-254">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-255">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-255">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-256">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-256">'Identity'</span></span>
- <span data-ttu-id="cdcc1-257">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-257">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-258">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-258">'Razor'</span></span>
- <span data-ttu-id="cdcc1-259">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-259">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-260">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-261">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-261">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-262">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-262">'Identity'</span></span>
- <span data-ttu-id="cdcc1-263">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-263">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-264">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-264">'Razor'</span></span>
- <span data-ttu-id="cdcc1-265">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-265">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-266">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-267">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-267">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-268">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-268">'Identity'</span></span>
- <span data-ttu-id="cdcc1-269">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-269">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-270">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-270">'Razor'</span></span>
- <span data-ttu-id="cdcc1-271">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-271">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-272">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-273">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-273">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-274">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-274">'Identity'</span></span>
- <span data-ttu-id="cdcc1-275">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-275">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-276">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-276">'Razor'</span></span>
- <span data-ttu-id="cdcc1-277">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-277">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-278">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-279">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-279">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-280">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-280">'Identity'</span></span>
- <span data-ttu-id="cdcc1-281">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-281">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-282">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-282">'Razor'</span></span>
- <span data-ttu-id="cdcc1-283">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-283">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | 获取或设置 `HttpClient` 实例是否应自动追随重定向响应。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-284">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="cdcc1-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | 获取或设置 `HttpClient` 实例的基址。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-285"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="cdcc1-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | 获取或设置 `HttpClient` 实例是否应处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-286"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="cdcc1-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | 获取或设置 `HttpClient` 实例应追随的重定向响应的最大数量。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-287"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="cdcc1-288">| 7 |</span><span class="sxs-lookup"><span data-stu-id="cdcc1-288">| 7 |</span></span>

<span data-ttu-id="cdcc1-289">创建 `WebApplicationFactoryClientOptions` 类并将它传递给 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 方法（默认值显示在代码示例中）：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-289">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="cdcc1-290">注入模拟服务</span><span class="sxs-lookup"><span data-stu-id="cdcc1-290">Inject mock services</span></span>

<span data-ttu-id="cdcc1-291">可以通过在主机生成器上调用 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)，在测试中替代服务。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-291">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="cdcc1-292">若要注入模拟服务，SUT 必须具有包含 `Startup.ConfigureServices` 方法的 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-292">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="cdcc1-293">示例 SUT 包含返回引用的作用域服务。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-293">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="cdcc1-294">向索引页面进行请求时，引用嵌入在索引页面上的隐藏字段中。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-294">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="cdcc1-295">Services/IQuoteService.cs：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-295">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="cdcc1-296">Services/QuoteService.cs：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-296">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="cdcc1-297">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-297">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="cdcc1-298">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-298">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="cdcc1-299">Pages/Index.cs：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-299">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="cdcc1-300">运行 SUT 应用时，会生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-300">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="cdcc1-301">若要在集成测试中测试服务和引用注入，测试会将模拟服务注入到 SUT 中。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-301">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="cdcc1-302">模拟服务会将应用的 `QuoteService` 替换为测试应用提供的服务，称为 `TestQuoteService`：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-302">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="cdcc1-303">IntegrationTests.IndexPageTests.cs：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-303">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="cdcc1-304">调用 `ConfigureTestServices`，并注册作用域服务：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-304">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="cdcc1-305">在测试执行过程中生成的标记反映由 `TestQuoteService` 提供的引用文本，因而断言通过：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-305">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="cdcc1-306">模拟身份验证</span><span class="sxs-lookup"><span data-stu-id="cdcc1-306">Mock authentication</span></span>

<span data-ttu-id="cdcc1-307">`AuthTests` 类中的测试检查安全终结点是否：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-307">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="cdcc1-308">将未经身份验证的用户重定向到应用的登录页面。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-308">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="cdcc1-309">为经过身份验证的用户返回内容。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-309">Returns content for an authenticated user.</span></span>

<span data-ttu-id="cdcc1-310">在 SUT 中，`/SecurePage` 页面使用 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 约定将 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 应用于页面。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-310">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="cdcc1-311">有关详细信息，请参阅 [Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-311">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="cdcc1-312">在 `Get_SecurePageRedirectsAnUnauthenticatedUser` 测试中，[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 设置为禁止重定向，具体方法是将 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-312">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="cdcc1-313">通过禁止客户端追随重定向，可以执行以下检查：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-313">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="cdcc1-314">可以根据预期 [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) 结果检查 SUT 返回的状态代码，而不是在重定向到登录页面之后的最终状态代码（这会是 [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-314">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="cdcc1-315">检查响应标头中的 `Location` 标头值，以确认它以 `http://localhost/Identity/Account/Login` 开头，而不是最终登录页面响应（其中 `Location` 标头不存在）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-315">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="cdcc1-316">测试应用可以在 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 中模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>，以便测试身份验证和授权的各个方面。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-316">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="cdcc1-317">最小方案返回一个 [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-317">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="cdcc1-318">当身份验证方案设置为 `Test`（其中为 `ConfigureTestServices` 注册了 `AddAuthentication`）时，会调用 `TestAuthHandler` 以对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-318">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="cdcc1-319">有关 `WebApplicationFactoryClientOptions` 的详细信息，请参阅[客户端选项](#client-options)部分。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-319">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="cdcc1-320">设置环境</span><span class="sxs-lookup"><span data-stu-id="cdcc1-320">Set the environment</span></span>

<span data-ttu-id="cdcc1-321">默认情况下，SUT 的主机和应用环境配置为使用开发环境。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-321">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="cdcc1-322">使用 `IHostBuilder` 时替代 SUT 的环境：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-322">To override the SUT's environment when using `IHostBuilder`:</span></span>

* <span data-ttu-id="cdcc1-323">设置 `ASPNETCORE_ENVIRONMENT` 环境变量（例如，`Staging`、`Production` 或其他自定义值，例如 `Testing`）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-323">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="cdcc1-324">在测试应用中替代 `CreateHostBuilder`，以读取以 `ASPNETCORE` 为前缀的环境变量。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-324">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() =>
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

<span data-ttu-id="cdcc1-325">如果 SUT 使用 Web 主机 (`IWebHostBuilder`)，则替代 `CreateWebHostBuilder`：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-325">If the SUT uses the Web Host (`IWebHostBuilder`), override `CreateWebHostBuilder`:</span></span>

```csharp
protected override IWebHostBuilder CreateWebHostBuilder() =>
    base.CreateWebHostBuilder().UseEnvironment("Testing");
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="cdcc1-326">测试基础结构如何推断应用内容根路径</span><span class="sxs-lookup"><span data-stu-id="cdcc1-326">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="cdcc1-327">`WebApplicationFactory` 构造函数通过在包含集成测试的程序集中搜索键等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName` 的 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)，来推断应用[内容根](xref:fundamentals/index#content-root)路径。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-327">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="cdcc1-328">如果找不到具有正确键的属性，则 `WebApplicationFactory` 会回退到搜索解决方案文件 (.sln) 并将 `TEntryPoint` 程序集名称追加到解决方案目录。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-328">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="cdcc1-329">应用根目录（内容根路径）用于发现视图和内容文件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-329">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="cdcc1-330">禁用卷影复制</span><span class="sxs-lookup"><span data-stu-id="cdcc1-330">Disable shadow copying</span></span>

<span data-ttu-id="cdcc1-331">卷影复制会导致在与输出目录不同的目录中执行测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-331">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="cdcc1-332">若要使测试正常工作，必须禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-332">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="cdcc1-333">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的 xunit.runner.json 文件来对 xUnit 禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-333">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="cdcc1-334">有关详细信息，请参阅[使用 JSON 配置 xUnit](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-334">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="cdcc1-335">将包含以下内容的 xunit.runner.json 文件添加到测试项目的根：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-335">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="cdcc1-336">对象的处置</span><span class="sxs-lookup"><span data-stu-id="cdcc1-336">Disposal of objects</span></span>

<span data-ttu-id="cdcc1-337">执行 `IClassFixture` 实现的测试之后，当 xUnit 处置 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 时，[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 和 [HttpClient](/dotnet/api/system.net.http.httpclient) 会进行处置。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-337">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="cdcc1-338">如果开发者实例化的对象需要处置，请在 `IClassFixture` 实现中处置它们。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-338">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="cdcc1-339">有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-339">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="cdcc1-340">集成测试示例</span><span class="sxs-lookup"><span data-stu-id="cdcc1-340">Integration tests sample</span></span>

<span data-ttu-id="cdcc1-341">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)包含两个应用：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-341">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="cdcc1-342">应用</span><span class="sxs-lookup"><span data-stu-id="cdcc1-342">App</span></span> | <span data-ttu-id="cdcc1-343">项目目录</span><span class="sxs-lookup"><span data-stu-id="cdcc1-343">Project directory</span></span> | <span data-ttu-id="cdcc1-344">描述</span><span class="sxs-lookup"><span data-stu-id="cdcc1-344">Description</span></span> |
| --- | ---
<span data-ttu-id="cdcc1-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-345">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-346">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-346">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-347">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-347">'Identity'</span></span>
- <span data-ttu-id="cdcc1-348">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-348">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-349">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-349">'Razor'</span></span>
- <span data-ttu-id="cdcc1-350">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-350">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-351">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-352">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-352">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-353">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-353">'Identity'</span></span>
- <span data-ttu-id="cdcc1-354">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-354">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-355">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-355">'Razor'</span></span>
- <span data-ttu-id="cdcc1-356">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-356">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-357">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-358">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-358">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-359">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-359">'Identity'</span></span>
- <span data-ttu-id="cdcc1-360">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-360">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-361">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-361">'Razor'</span></span>
- <span data-ttu-id="cdcc1-362">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-362">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-363">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-364">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-364">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-365">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-365">'Identity'</span></span>
- <span data-ttu-id="cdcc1-366">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-366">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-367">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-367">'Razor'</span></span>
- <span data-ttu-id="cdcc1-368">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-368">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-369">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-370">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-370">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-371">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-371">'Identity'</span></span>
- <span data-ttu-id="cdcc1-372">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-372">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-373">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-373">'Razor'</span></span>
- <span data-ttu-id="cdcc1-374">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-374">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-376">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-377">'Identity'</span></span>
- <span data-ttu-id="cdcc1-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-379">'Razor'</span></span>
- <span data-ttu-id="cdcc1-380">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-380">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-381">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-382">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-383">'Identity'</span></span>
- <span data-ttu-id="cdcc1-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-385">'Razor'</span></span>
- <span data-ttu-id="cdcc1-386">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-388">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-389">'Identity'</span></span>
- <span data-ttu-id="cdcc1-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-391">'Razor'</span></span>
- <span data-ttu-id="cdcc1-392">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-394">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-395">'Identity'</span></span>
- <span data-ttu-id="cdcc1-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-397">'Razor'</span></span>
- <span data-ttu-id="cdcc1-398">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-398">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-399">------ | | Message app (the SUT) | src/RazorPagesProject | 允许用户添加消息、删除一个消息、删除所有消息和分析消息。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-399">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="cdcc1-400">| | Test app | tests/RazorPagesProject.Tests | 用于 SUT 的集成测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-400">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="cdcc1-401">可使用 IDE 的内置测试功能（例如 [Visual Studio](https://visualstudio.microsoft.com)）运行测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-401">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="cdcc1-402">如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令行，请在 tests/RazorPagesProject.Tests 目录中的命令提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-402">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="cdcc1-403">消息应用 (SUT) 组织</span><span class="sxs-lookup"><span data-stu-id="cdcc1-403">Message app (SUT) organization</span></span>

<span data-ttu-id="cdcc1-404">SUT 是具有以下特征的 Razor Pages 消息系统：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-404">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="cdcc1-405">应用的索引页面（Pages/Index.cshtml 和 Pages/Index.cshtml.cs）提供 UI 和页面模型方法，用于控制添加、删除和分析消息（每个消息的平均字词数）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-405">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="cdcc1-406">消息由 `Message` 类 (Data/Message.cs) 描述，并具有两个属性：`Id`（键）和 `Text`（消息）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-406">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="cdcc1-407">`Text` 属性是必需的，并限制为 200 个字符。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-407">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="cdcc1-408">消息使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)存储。&#8224;</span><span class="sxs-lookup"><span data-stu-id="cdcc1-408">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="cdcc1-409">应用在其数据库上下文类 `AppDbContext` (Data/AppDbContext.cs) 中包含数据访问层 (DAL)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-409">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="cdcc1-410">如果应用启动时数据库为空，则消息存储初始化为三条消息。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-410">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="cdcc1-411">应用包含只能由经过身份验证的用户访问的 `/SecurePage`。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-411">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="cdcc1-412">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明如何将内存中数据库用于 MSTest 测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-412">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="cdcc1-413">本主题使用 [xUnit](https://xunit.github.io/) 测试框架。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-413">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="cdcc1-414">不同测试框架中的测试概念和测试实现相似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-414">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="cdcc1-415">尽管应用未使用存储库模式且不是[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些开发模式。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-415">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="cdcc1-416">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（该示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-416">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="cdcc1-417">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="cdcc1-417">Test app organization</span></span>

<span data-ttu-id="cdcc1-418">测试应用是 tests/RazorPagesProject.Tests 目录中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-418">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="cdcc1-419">测试应用目录</span><span class="sxs-lookup"><span data-stu-id="cdcc1-419">Test app directory</span></span> | <span data-ttu-id="cdcc1-420">描述</span><span class="sxs-lookup"><span data-stu-id="cdcc1-420">Description</span></span> |
| ---
<span data-ttu-id="cdcc1-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-421">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-422">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-422">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-423">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-423">'Identity'</span></span>
- <span data-ttu-id="cdcc1-424">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-424">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-425">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-425">'Razor'</span></span>
- <span data-ttu-id="cdcc1-426">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-426">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-427">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-428">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-428">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-429">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-429">'Identity'</span></span>
- <span data-ttu-id="cdcc1-430">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-430">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-431">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-431">'Razor'</span></span>
- <span data-ttu-id="cdcc1-432">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-432">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-433">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-434">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-434">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-435">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-435">'Identity'</span></span>
- <span data-ttu-id="cdcc1-436">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-436">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-437">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-437">'Razor'</span></span>
- <span data-ttu-id="cdcc1-438">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-438">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-439">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-440">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-440">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-441">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-441">'Identity'</span></span>
- <span data-ttu-id="cdcc1-442">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-442">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-443">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-443">'Razor'</span></span>
- <span data-ttu-id="cdcc1-444">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-444">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-445">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-446">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-446">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-447">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-447">'Identity'</span></span>
- <span data-ttu-id="cdcc1-448">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-448">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-449">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-449">'Razor'</span></span>
- <span data-ttu-id="cdcc1-450">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-450">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-451">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-452">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-452">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-453">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-453">'Identity'</span></span>
- <span data-ttu-id="cdcc1-454">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-454">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-455">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-455">'Razor'</span></span>
- <span data-ttu-id="cdcc1-456">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-456">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-457">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-458">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-458">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-459">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-459">'Identity'</span></span>
- <span data-ttu-id="cdcc1-460">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-460">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-461">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-461">'Razor'</span></span>
- <span data-ttu-id="cdcc1-462">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-462">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-463">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-464">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-464">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-465">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-465">'Identity'</span></span>
- <span data-ttu-id="cdcc1-466">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-466">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-467">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-467">'Razor'</span></span>
- <span data-ttu-id="cdcc1-468">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-468">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-469">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-470">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-470">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-471">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-471">'Identity'</span></span>
- <span data-ttu-id="cdcc1-472">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-472">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-473">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-473">'Razor'</span></span>
- <span data-ttu-id="cdcc1-474">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-474">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-475">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-476">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-476">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-477">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-477">'Identity'</span></span>
- <span data-ttu-id="cdcc1-478">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-478">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-479">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-479">'Razor'</span></span>
- <span data-ttu-id="cdcc1-480">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-480">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-481">------ | | AuthTests | 包含针对以下事项的测试方法：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-481">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="cdcc1-482">未经身份验证的用户访问安全页面。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-482">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="cdcc1-483">经过身份验证的用户访问安全页面（通过模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-483">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="cdcc1-484">获取 GitHub 用户配置文件，并检查配置文件的用户登录。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-484">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="cdcc1-485">| | BasicTests | 包含用于路由和内容类型的测试方法。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-485">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="cdcc1-486">| | IntegrationTests | 包含使用自定义 `WebApplicationFactory` 类的索引页面的集成测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-486">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="cdcc1-487">| | Helpers/Utilities | </span><span class="sxs-lookup"><span data-stu-id="cdcc1-487">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="cdcc1-488">Utilities.cs 包含用于通过测试数据设定数据库种子的 `InitializeDbForTests` 方法。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-488">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="cdcc1-489">HtmlHelpers.cs 提供了一种方法，用于返回 AngleSharp `IHtmlDocument` 供测试方法使用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-489">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="cdcc1-490">HttpClientExtensions.cs 为 `SendAsync` 提供重载，以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-490">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="cdcc1-491">测试框架为 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-491">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="cdcc1-492">集成测试使用 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)（包括 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)）执行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-492">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="cdcc1-493">由于 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 包用于配置测试主机和测试服务器，因此 `TestHost` 和 `TestServer` 包在测试应用的项目文件或测试应用的开发者配置中不需要直接包引用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-493">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="cdcc1-494">设定数据库种子以进行测试</span><span class="sxs-lookup"><span data-stu-id="cdcc1-494">**Seeding the database for testing**</span></span>

<span data-ttu-id="cdcc1-495">集成测试在执行测试前通常需要数据库中的小型数据集。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-495">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="cdcc1-496">例如，删除测试需要进行数据库记录删除，因此数据库必须至少有一个记录，删除请求才能成功。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-496">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="cdcc1-497">示例应用使用 Utilities.cs 中的三个消息（测试在执行时可以使用它们）设定数据库种子：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-497">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="cdcc1-498">SUT 的数据库上下文在其 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-498">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="cdcc1-499">测试应用的 `builder.ConfigureServices` 回调在执行应用的 `Startup.ConfigureServices` 代码之后执行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-499">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="cdcc1-500">若要将不同的数据库用于测试，必须在 `builder.ConfigureServices` 中替换应用的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-500">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="cdcc1-501">有关详细信息，请参阅[自定义 WebApplicationFactory](#customize-webapplicationfactory) 部分。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-501">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

<span data-ttu-id="cdcc1-502">对于仍使用 [Web Host}(xref:fundamentals/host/web-host) 的 SUT，将在 SUT 的 `Startup.ConfigureServices` 代码之前执行测试应用的 `builder.ConfigureServices` 回调。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-502">For SUTs that still use the [Web Host}(xref:fundamentals/host/web-host), the test app's `builder.ConfigureServices` callback is executed *before* the SUT's `Startup.ConfigureServices` code.</span></span> <span data-ttu-id="cdcc1-503">之后执行测试应用的 `builder.ConfigureTestServices` 回调。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-503">The test app's `builder.ConfigureTestServices` callback is executed *after*.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="cdcc1-504">集成测试可在包含应用支持基础结构（如数据库、文件系统和网络）的级别上确保应用组件功能正常。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-504">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="cdcc1-505">ASP.NET Core 通过将单元测试框架与测试 Web 主机和内存中测试服务器结合使用来支持集成测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-505">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="cdcc1-506">本主题假设读者基本了解单元测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-506">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="cdcc1-507">如果不熟悉测试概念，请参阅 [.NET Core 和 .NET Standard 中的单元测试](/dotnet/core/testing/)主题及其链接内容。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-507">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="cdcc1-508">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="cdcc1-508">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="cdcc1-509">示例应用是 Razor Pages 应用，假设读者基本了解 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-509">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="cdcc1-510">如果不熟悉 Razor Pages，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-510">If unfamiliar with Razor Pages, see the following topics:</span></span>

* <span data-ttu-id="cdcc1-511">[Razor Pages 简介](xref:razor-pages/index)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-511">[Introduction to Razor Pages](xref:razor-pages/index)</span></span>
* <span data-ttu-id="cdcc1-512">[Razor Pages 入门](xref:tutorials/razor-pages/razor-pages-start)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-512">[Get started with Razor Pages](xref:tutorials/razor-pages/razor-pages-start)</span></span>
* <span data-ttu-id="cdcc1-513">[Razor Pages 单元测试](xref:test/razor-pages-tests)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-513">[Razor Pages unit tests](xref:test/razor-pages-tests)</span></span>

> [!NOTE]
> <span data-ttu-id="cdcc1-514">对于测试 SPA，建议使用可以自动执行浏览器的工具，如 [Selenium](https://www.seleniumhq.org/)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-514">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="cdcc1-515">集成测试简介</span><span class="sxs-lookup"><span data-stu-id="cdcc1-515">Introduction to integration tests</span></span>

<span data-ttu-id="cdcc1-516">与[单元测试](/dotnet/core/testing/)相比，集成测试可在更广泛的级别上评估应用的组件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-516">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="cdcc1-517">单元测试用于测试独立软件组件，如单独的类方法。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-517">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="cdcc1-518">集成测试确认两个或更多应用组件一起工作以生成预期结果，可能包括完整处理请求所需的每个组件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-518">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="cdcc1-519">这些更广泛的测试用于测试应用的基础结构和整个框架，通常包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-519">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="cdcc1-520">数据库</span><span class="sxs-lookup"><span data-stu-id="cdcc1-520">Database</span></span>
* <span data-ttu-id="cdcc1-521">文件系统</span><span class="sxs-lookup"><span data-stu-id="cdcc1-521">File system</span></span>
* <span data-ttu-id="cdcc1-522">网络设备</span><span class="sxs-lookup"><span data-stu-id="cdcc1-522">Network appliances</span></span>
* <span data-ttu-id="cdcc1-523">请求-响应管道</span><span class="sxs-lookup"><span data-stu-id="cdcc1-523">Request-response pipeline</span></span>

<span data-ttu-id="cdcc1-524">单元测试使用称为 fake 或 mock 对象的制造组件，而不是基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-524">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="cdcc1-525">与单元测试相比，集成测试：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-525">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="cdcc1-526">使用应用在生产环境中使用的实际组件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-526">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="cdcc1-527">需要进行更多代码和数据处理。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-527">Require more code and data processing.</span></span>
* <span data-ttu-id="cdcc1-528">需要更长时间来运行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-528">Take longer to run.</span></span>

<span data-ttu-id="cdcc1-529">因此，将集成测试的使用限制为最重要的基础结构方案。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-529">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="cdcc1-530">如果可以使用单元测试或集成测试来测试行为，请选择单元测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-530">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="cdcc1-531">请勿为通过数据库和文件系统进行的数据和文件访问的每个可能排列编写集成测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-531">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="cdcc1-532">无论应用中有多少位置与数据库和文件系统交互，一组集中式读取、写入、更新和删除集成测试通常能够充分测试数据库和文件系统组件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-532">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="cdcc1-533">将单元测试用于与这些组件交互的方法逻辑的例程测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-533">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="cdcc1-534">在单元测试中，使用基础结构 fake/mock 会导致更快地执行测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-534">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="cdcc1-535">在集成测试的讨论中，测试的项目经常称为“测试中的系统”，简称“SUT”。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-535">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="cdcc1-536">本主题中使用“SUT”来指代测试的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-536">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="cdcc1-537">ASP.NET Core 集成测试</span><span class="sxs-lookup"><span data-stu-id="cdcc1-537">ASP.NET Core integration tests</span></span>

<span data-ttu-id="cdcc1-538">ASP.NET Core 中的集成测试需要以下内容：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-538">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="cdcc1-539">测试项目用于包含和执行测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-539">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="cdcc1-540">测试项目具有对 SUT 的引用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-540">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="cdcc1-541">测试项目为 SUT 创建测试 Web 主机，并使用测试服务器客户端处理 SUT 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-541">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="cdcc1-542">测试运行程序用于执行测试并报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-542">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="cdcc1-543">集成测试后跟一系列事件，包括常规“排列”、“操作”和“断言”测试步骤：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-543">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="cdcc1-544">已配置 SUT 的 Web 主机。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-544">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="cdcc1-545">创建测试服务器客户端以向应用提交请求。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-545">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="cdcc1-546">执行“排列”测试步骤：测试应用会准备请求。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-546">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="cdcc1-547">执行“操作”测试步骤：客户端提交请求并接收响应。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-547">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="cdcc1-548">执行“断言”测试步骤：实际响应基于预期响应验证为通过或失败。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-548">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="cdcc1-549">该过程会一直继续，直到执行了所有测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-549">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="cdcc1-550">报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-550">The test results are reported.</span></span>

<span data-ttu-id="cdcc1-551">通常，测试 Web 主机的配置与用于测试运行的应用常规 Web 主机不同。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-551">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="cdcc1-552">例如，可以将不同的数据库或不同的应用设置用于测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-552">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="cdcc1-553">基础结构组件（如测试 Web 主机和内存中测试服务器 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))）由 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 包提供或管理。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-553">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="cdcc1-554">使用此包可简化测试创建和执行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-554">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="cdcc1-555">`Microsoft.AspNetCore.Mvc.Testing` 包处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-555">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="cdcc1-556">将依赖项文件 (.deps) 从 SUT 复制到测试项目的 bin 目录中。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-556">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="cdcc1-557">将[内容根目录](xref:fundamentals/index#content-root)设置为 SUT 的项目根目录，以便可在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-557">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="cdcc1-558">提供 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 类，以简化 SUT 在 `TestServer` 中的启动过程。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-558">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="cdcc1-559">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍如何设置测试项目和测试运行程序，以及有关如何运行测试的详细说明与有关如何命名测试和测试类的建议。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-559">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="cdcc1-560">为应用创建测试项目时，请将集成测试中的单元测试分隔到不同的项目中。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-560">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="cdcc1-561">这可帮助确保不会意外地将基础结构测试组件包含在单元测试中。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-561">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="cdcc1-562">通过分隔单元测试和集成测试还可以控制运行的测试集。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-562">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="cdcc1-563">Razor Pages 应用与 MVC 应用的测试配置之间几乎没有任何区别。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-563">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="cdcc1-564">唯一的区别在于测试的命名方式。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-564">The only difference is in how the tests are named.</span></span> <span data-ttu-id="cdcc1-565">在 Razor Pages 应用中，页面终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 用于为索引页面测试组件集成）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-565">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="cdcc1-566">在 MVC 应用中，测试通常按控制器类进行组织，并以它们所测试的控制器来命令（例如 `HomeControllerTests` 用于为主页控制器测试组件集成）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-566">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="cdcc1-567">测试应用先决条件</span><span class="sxs-lookup"><span data-stu-id="cdcc1-567">Test app prerequisites</span></span>

<span data-ttu-id="cdcc1-568">测试项目必须：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-568">The test project must:</span></span>

* <span data-ttu-id="cdcc1-569">引用以下包：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-569">Reference the following packages:</span></span>
  * [<span data-ttu-id="cdcc1-570">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="cdcc1-570">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="cdcc1-571">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="cdcc1-571">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="cdcc1-572">在项目文件中指定 Web SDK (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-572">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="cdcc1-573">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)时需要 Web SDK。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-573">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="cdcc1-574">可以在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-574">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="cdcc1-575">检查 tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj 文件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-575">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="cdcc1-576">示例应用使用 [xUnit](https://xunit.github.io/) 测试框架和 [AngleSharp](https://anglesharp.github.io/) 分析程序库，因此示例应用还引用：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-576">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="cdcc1-577">xunit</span><span class="sxs-lookup"><span data-stu-id="cdcc1-577">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="cdcc1-578">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="cdcc1-578">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="cdcc1-579">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="cdcc1-579">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="cdcc1-580">SUT 环境</span><span class="sxs-lookup"><span data-stu-id="cdcc1-580">SUT environment</span></span>

<span data-ttu-id="cdcc1-581">如果未设置 SUT 的[环境](xref:fundamentals/environments)，则环境会默认为“开发”。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-581">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="cdcc1-582">使用默认 WebApplicationFactory 的基本测试</span><span class="sxs-lookup"><span data-stu-id="cdcc1-582">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="cdcc1-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 用于为集成测试创建 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-583">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="cdcc1-584">`TEntryPoint` 是 SUT 的入口点类，通常是 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-584">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="cdcc1-585">测试类实现一个类固定例程接口 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))，以指示类包含测试，并在类中的所有测试间提供共享对象实例。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-585">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="cdcc1-586">以下测试类 `BasicTests` 使用 `WebApplicationFactory` 启动 SUT，并向测试方法 `Get_EndpointsReturnSuccessAndCorrectContentType` 提供 [HttpClient](/dotnet/api/system.net.http.httpclient)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-586">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="cdcc1-587">该方法检查响应状态代码是否成功（处于范围 200-299 中的状态代码），以及 `Content-Type` 标头是否为适用于多个应用页面的 `text/html; charset=utf-8`。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-587">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="cdcc1-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 创建自动追随重定向并处理 cookie 的 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-588">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="cdcc1-589">默认情况下，在启用了 [GDPR 同意策略](xref:security/gdpr)时，不会在请求间保留非必要 cookie。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-589">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="cdcc1-590">若要保留非必要 cookie（如 TempData 提供程序使用的 cookie），请在测试中将它们标记为必要。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-590">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="cdcc1-591">有关将 cookie 标记为必要的说明，请参阅[必要 cookie](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-591">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="cdcc1-592">自定义 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="cdcc1-592">Customize WebApplicationFactory</span></span>

<span data-ttu-id="cdcc1-593">通过从 `WebApplicationFactory` 来创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-593">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="cdcc1-594">从 `WebApplicationFactory` 继承并替代 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-594">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="cdcc1-595">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 允许使用 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) 配置服务集合，该配置在应用的 `Startup.ConfigureServices` 之前执行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-595">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices), which is executed before the app's `Startup.ConfigureServices`.</span></span> <span data-ttu-id="cdcc1-596">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 允许使用 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 重写和修改服务集合中已注册的服务：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-596">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows overriding and modifying registered services in the service collection with [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="cdcc1-597">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-597">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="cdcc1-598">[集成测试示例：测试应用组织](#test-app-organization)部分中介绍了该方法。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-598">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="cdcc1-599">在测试类中使用自定义 `CustomWebApplicationFactory`。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-599">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="cdcc1-600">下面的示例使用 `IndexPageTests` 类中的工厂：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-600">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="cdcc1-601">示例应用的客户端配置为阻止 `HttpClient` 追随重定向。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-601">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="cdcc1-602">如稍后在[模拟身份验证](#mock-authentication)部分中所述，这允许测试检查应用第一个响应的结果。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-602">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="cdcc1-603">第一个响应是在许多具有 `Location` 标头的测试中进行重定向。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-603">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="cdcc1-604">典型测试使用 `HttpClient` 和帮助程序方法处理请求和响应：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-604">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="cdcc1-605">对 SUT 发出的任何 POST 请求都必须满足防伪检查，该检查由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动执行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-605">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="cdcc1-606">若要安排测试的 POST 请求，测试应用必须：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-606">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="cdcc1-607">对页面发出请求。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-607">Make a request for the page.</span></span>
1. <span data-ttu-id="cdcc1-608">分析来自响应的防伪 cookie 和请求验证令牌。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-608">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="cdcc1-609">发出放置了防伪 cookie 和请求验证令牌的 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-609">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="cdcc1-610">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 `SendAsync` 帮助程序扩展方法 (Helpers/HttpClientExtensions.cs) 和 `GetDocumentAsync` 帮助程序方法 (Helpers/HtmlHelpers.cs) 使用 [AngleSharp](https://anglesharp.github.io/) 分析程序，通过以下方法处理防伪检查：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-610">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="cdcc1-611">`GetDocumentAsync` &ndash; 接收 [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 并返回 `IHtmlDocument`。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-611">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="cdcc1-612">`GetDocumentAsync` 使用一个基于原始 `HttpResponseMessage` 准备虚拟响应的工厂。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-612">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="cdcc1-613">有关详细信息，请参阅 [AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-613">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="cdcc1-614">`HttpClient` 的 `SendAsync` 扩展方法撰写 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) 并调用 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)，以向 SUT 提交请求。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-614">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="cdcc1-615">`SendAsync` 的重载接受 HTML 窗体 (`IHtmlFormElement`) 和以下内容：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-615">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="cdcc1-616">窗体的“提交”按钮 (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-616">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="cdcc1-617">窗体值集合 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-617">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="cdcc1-618">提交按钮 (`IHtmlElement`) 和窗体值 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="cdcc1-618">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="cdcc1-619">[AngleSharp](https://anglesharp.github.io/) 是在本主题和示例应用中用于演示的第三方分析库。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-619">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="cdcc1-620">ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-620">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="cdcc1-621">可以使用其他分析程序，如 [Html Agility Pack (HAP)](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-621">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="cdcc1-622">另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-622">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="cdcc1-623">使用 WithWebHostBuilder 自定义客户端</span><span class="sxs-lookup"><span data-stu-id="cdcc1-623">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="cdcc1-624">当测试方法中需要其他配置时，[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 可创建新 `WebApplicationFactory`，其中包含通过配置进一步自定义的 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-624">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="cdcc1-625">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 的 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 测试方法演示了 `WithWebHostBuilder` 的使用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-625">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="cdcc1-626">此测试通过在 SUT 中触发窗体提交，在数据库中执行记录删除。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-626">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="cdcc1-627">由于 `IndexPageTests` 类中的另一个测试会执行删除数据库中所有记录的操作，并且可能在 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 方法之前运行，因此数据库会在此测试方法中重新进行种子设定，以确保存在记录供 SUT 删除。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-627">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="cdcc1-628">在 SUT 中选择 `messages` 窗体的第一个删除按钮可在向 SUT 发出的请求中进行模拟：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-628">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="cdcc1-629">客户端选项</span><span class="sxs-lookup"><span data-stu-id="cdcc1-629">Client options</span></span>

<span data-ttu-id="cdcc1-630">下表显示在创建 `HttpClient` 实例时可用的默认 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-630">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="cdcc1-631">选项</span><span class="sxs-lookup"><span data-stu-id="cdcc1-631">Option</span></span> | <span data-ttu-id="cdcc1-632">描述</span><span class="sxs-lookup"><span data-stu-id="cdcc1-632">Description</span></span> | <span data-ttu-id="cdcc1-633">默认</span><span class="sxs-lookup"><span data-stu-id="cdcc1-633">Default</span></span> |
| ---
<span data-ttu-id="cdcc1-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-634">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-635">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-635">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-636">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-636">'Identity'</span></span>
- <span data-ttu-id="cdcc1-637">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-637">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-638">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-638">'Razor'</span></span>
- <span data-ttu-id="cdcc1-639">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-639">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-640">--- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-641">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-641">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-642">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-642">'Identity'</span></span>
- <span data-ttu-id="cdcc1-643">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-643">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-644">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-644">'Razor'</span></span>
- <span data-ttu-id="cdcc1-645">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-645">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-646">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-647">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-647">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-648">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-648">'Identity'</span></span>
- <span data-ttu-id="cdcc1-649">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-649">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-650">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-650">'Razor'</span></span>
- <span data-ttu-id="cdcc1-651">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-651">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-652">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-653">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-653">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-654">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-654">'Identity'</span></span>
- <span data-ttu-id="cdcc1-655">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-655">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-656">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-656">'Razor'</span></span>
- <span data-ttu-id="cdcc1-657">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-657">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-658">------ | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-659">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-659">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-660">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-660">'Identity'</span></span>
- <span data-ttu-id="cdcc1-661">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-661">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-662">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-662">'Razor'</span></span>
- <span data-ttu-id="cdcc1-663">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-663">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | 获取或设置 `HttpClient` 实例是否应自动追随重定向响应。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-664">---- | | [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span><span data-ttu-id="cdcc1-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | 获取或设置 `HttpClient` 实例的基址。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-665"> | `true` | | [BaseAddress](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | Gets or sets the base address of `HttpClient` instances.</span></span><span data-ttu-id="cdcc1-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | 获取或设置 `HttpClient` 实例是否应处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-666"> | `http://localhost` | | [HandleCookies](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | Gets or sets whether `HttpClient` instances should handle cookies.</span></span><span data-ttu-id="cdcc1-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | 获取或设置 `HttpClient` 实例应追随的重定向响应的最大数量。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-667"> | `true` | | [MaxAutomaticRedirections](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> <span data-ttu-id="cdcc1-668">| 7 |</span><span class="sxs-lookup"><span data-stu-id="cdcc1-668">| 7 |</span></span>

<span data-ttu-id="cdcc1-669">创建 `WebApplicationFactoryClientOptions` 类并将它传递给 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 方法（默认值显示在代码示例中）：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-669">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="cdcc1-670">注入模拟服务</span><span class="sxs-lookup"><span data-stu-id="cdcc1-670">Inject mock services</span></span>

<span data-ttu-id="cdcc1-671">可以通过在主机生成器上调用 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)，在测试中替代服务。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-671">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="cdcc1-672">若要注入模拟服务，SUT 必须具有包含 `Startup.ConfigureServices` 方法的 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-672">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="cdcc1-673">示例 SUT 包含返回引用的作用域服务。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-673">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="cdcc1-674">向索引页面进行请求时，引用嵌入在索引页面上的隐藏字段中。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-674">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="cdcc1-675">Services/IQuoteService.cs：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-675">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="cdcc1-676">Services/QuoteService.cs：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-676">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="cdcc1-677">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-677">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="cdcc1-678">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-678">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="cdcc1-679">Pages/Index.cs：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-679">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="cdcc1-680">运行 SUT 应用时，会生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-680">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="cdcc1-681">若要在集成测试中测试服务和引用注入，测试会将模拟服务注入到 SUT 中。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-681">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="cdcc1-682">模拟服务会将应用的 `QuoteService` 替换为测试应用提供的服务，称为 `TestQuoteService`：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-682">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="cdcc1-683">IntegrationTests.IndexPageTests.cs：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-683">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="cdcc1-684">调用 `ConfigureTestServices`，并注册作用域服务：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-684">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="cdcc1-685">在测试执行过程中生成的标记反映由 `TestQuoteService` 提供的引用文本，因而断言通过：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-685">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="cdcc1-686">模拟身份验证</span><span class="sxs-lookup"><span data-stu-id="cdcc1-686">Mock authentication</span></span>

<span data-ttu-id="cdcc1-687">`AuthTests` 类中的测试检查安全终结点是否：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-687">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="cdcc1-688">将未经身份验证的用户重定向到应用的登录页面。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-688">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="cdcc1-689">为经过身份验证的用户返回内容。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-689">Returns content for an authenticated user.</span></span>

<span data-ttu-id="cdcc1-690">在 SUT 中，`/SecurePage` 页面使用 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 约定将 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 应用于页面。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-690">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="cdcc1-691">有关详细信息，请参阅 [Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-691">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="cdcc1-692">在 `Get_SecurePageRedirectsAnUnauthenticatedUser` 测试中，[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 设置为禁止重定向，具体方法是将 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-692">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="cdcc1-693">通过禁止客户端追随重定向，可以执行以下检查：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-693">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="cdcc1-694">可以根据预期 [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) 结果检查 SUT 返回的状态代码，而不是在重定向到登录页面之后的最终状态代码（这会是 [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-694">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="cdcc1-695">检查响应标头中的 `Location` 标头值，以确认它以 `http://localhost/Identity/Account/Login` 开头，而不是最终登录页面响应（其中 `Location` 标头不存在）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-695">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="cdcc1-696">测试应用可以在 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 中模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>，以便测试身份验证和授权的各个方面。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-696">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="cdcc1-697">最小方案返回一个 [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-697">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="cdcc1-698">当身份验证方案设置为 `Test`（其中为 `ConfigureTestServices` 注册了 `AddAuthentication`）时，会调用 `TestAuthHandler` 以对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-698">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="cdcc1-699">有关 `WebApplicationFactoryClientOptions` 的详细信息，请参阅[客户端选项](#client-options)部分。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-699">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="cdcc1-700">设置环境</span><span class="sxs-lookup"><span data-stu-id="cdcc1-700">Set the environment</span></span>

<span data-ttu-id="cdcc1-701">默认情况下，SUT 的主机和应用环境配置为使用开发环境。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-701">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="cdcc1-702">替代 SUT 的环境：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-702">To override the SUT's environment:</span></span>

* <span data-ttu-id="cdcc1-703">设置 `ASPNETCORE_ENVIRONMENT` 环境变量（例如，`Staging`、`Production` 或其他自定义值，例如 `Testing`）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-703">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="cdcc1-704">在测试应用中重写 `CreateWebHostBuilder`，以读取 `ASPNETCORE_ENVIRONMENT` 环境变量。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-704">Override `CreateWebHostBuilder` in the test app to read the `ASPNETCORE_ENVIRONMENT` environment variable.</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override IWebHostBuilder CreateWebHostBuilder()
    {
        return base.CreateWebHostBuilder()
            .UseEnvironment(
                Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    }

    ...
}
```

<span data-ttu-id="cdcc1-705">还可以在自定义 <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601> 中直接在主机生成器上设置环境：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-705">The environment can also be set directly on the host builder in a custom <xref:Microsoft.AspNetCore.Mvc.Testing.WebApplicationFactory%601>:</span></span>

```csharp
public class CustomWebApplicationFactory<TStartup> 
    : WebApplicationFactory<TStartup> where TStartup: class
{
    protected override void ConfigureWebHost(IWebHostBuilder builder)
    {
        builder.UseEnvironment(
            Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT"));
    
        ...
    }

    ...
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="cdcc1-706">测试基础结构如何推断应用内容根路径</span><span class="sxs-lookup"><span data-stu-id="cdcc1-706">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="cdcc1-707">`WebApplicationFactory` 构造函数通过在包含集成测试的程序集中搜索键等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName` 的 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)，来推断应用[内容根](xref:fundamentals/index#content-root)路径。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-707">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="cdcc1-708">如果找不到具有正确键的属性，则 `WebApplicationFactory` 会回退到搜索解决方案文件 (.sln) 并将 `TEntryPoint` 程序集名称追加到解决方案目录。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-708">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="cdcc1-709">应用根目录（内容根路径）用于发现视图和内容文件。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-709">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="cdcc1-710">禁用卷影复制</span><span class="sxs-lookup"><span data-stu-id="cdcc1-710">Disable shadow copying</span></span>

<span data-ttu-id="cdcc1-711">卷影复制会导致在与输出目录不同的目录中执行测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-711">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="cdcc1-712">若要使测试正常工作，必须禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-712">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="cdcc1-713">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的 xunit.runner.json 文件来对 xUnit 禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-713">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="cdcc1-714">有关详细信息，请参阅[使用 JSON 配置 xUnit](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-714">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="cdcc1-715">将包含以下内容的 xunit.runner.json 文件添加到测试项目的根：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-715">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="cdcc1-716">如果使用 Visual Studio，将文件的“复制到输出目录”属性设置为“始终复制”。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-716">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="cdcc1-717">如果不使用 Visual Studio，请将 `Content` 目标添加到测试应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-717">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="cdcc1-718">对象的处置</span><span class="sxs-lookup"><span data-stu-id="cdcc1-718">Disposal of objects</span></span>

<span data-ttu-id="cdcc1-719">执行 `IClassFixture` 实现的测试之后，当 xUnit 处置 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 时，[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 和 [HttpClient](/dotnet/api/system.net.http.httpclient) 会进行处置。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-719">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="cdcc1-720">如果开发者实例化的对象需要处置，请在 `IClassFixture` 实现中处置它们。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-720">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="cdcc1-721">有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-721">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="cdcc1-722">集成测试示例</span><span class="sxs-lookup"><span data-stu-id="cdcc1-722">Integration tests sample</span></span>

<span data-ttu-id="cdcc1-723">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)包含两个应用：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-723">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="cdcc1-724">应用</span><span class="sxs-lookup"><span data-stu-id="cdcc1-724">App</span></span> | <span data-ttu-id="cdcc1-725">项目目录</span><span class="sxs-lookup"><span data-stu-id="cdcc1-725">Project directory</span></span> | <span data-ttu-id="cdcc1-726">描述</span><span class="sxs-lookup"><span data-stu-id="cdcc1-726">Description</span></span> |
| --- | ---
<span data-ttu-id="cdcc1-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-727">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-728">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-728">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-729">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-729">'Identity'</span></span>
- <span data-ttu-id="cdcc1-730">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-730">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-731">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-731">'Razor'</span></span>
- <span data-ttu-id="cdcc1-732">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-732">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-733">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-734">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-734">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-735">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-735">'Identity'</span></span>
- <span data-ttu-id="cdcc1-736">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-736">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-737">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-737">'Razor'</span></span>
- <span data-ttu-id="cdcc1-738">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-738">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-739">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-740">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-740">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-741">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-741">'Identity'</span></span>
- <span data-ttu-id="cdcc1-742">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-742">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-743">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-743">'Razor'</span></span>
- <span data-ttu-id="cdcc1-744">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-744">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-745">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-746">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-746">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-747">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-747">'Identity'</span></span>
- <span data-ttu-id="cdcc1-748">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-748">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-749">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-749">'Razor'</span></span>
- <span data-ttu-id="cdcc1-750">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-750">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-751">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-752">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-752">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-753">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-753">'Identity'</span></span>
- <span data-ttu-id="cdcc1-754">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-754">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-755">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-755">'Razor'</span></span>
- <span data-ttu-id="cdcc1-756">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-756">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-757">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-758">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-758">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-759">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-759">'Identity'</span></span>
- <span data-ttu-id="cdcc1-760">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-760">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-761">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-761">'Razor'</span></span>
- <span data-ttu-id="cdcc1-762">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-762">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-763">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-764">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-764">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-765">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-765">'Identity'</span></span>
- <span data-ttu-id="cdcc1-766">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-766">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-767">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-767">'Razor'</span></span>
- <span data-ttu-id="cdcc1-768">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-768">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-769">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-770">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-770">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-771">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-771">'Identity'</span></span>
- <span data-ttu-id="cdcc1-772">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-772">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-773">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-773">'Razor'</span></span>
- <span data-ttu-id="cdcc1-774">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-774">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-775">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-776">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-776">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-777">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-777">'Identity'</span></span>
- <span data-ttu-id="cdcc1-778">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-778">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-779">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-779">'Razor'</span></span>
- <span data-ttu-id="cdcc1-780">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-780">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-781">------ | | Message app (the SUT) | src/RazorPagesProject | 允许用户添加消息、删除一个消息、删除所有消息和分析消息。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-781">------ | | Message app (the SUT) | *src/RazorPagesProject* | Allows a user to add, delete one, delete all, and analyze messages.</span></span> <span data-ttu-id="cdcc1-782">| | Test app | tests/RazorPagesProject.Tests | 用于 SUT 的集成测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-782">| | Test app | *tests/RazorPagesProject.Tests* | Used to integration test the SUT.</span></span> |

<span data-ttu-id="cdcc1-783">可使用 IDE 的内置测试功能（例如 [Visual Studio](https://visualstudio.microsoft.com)）运行测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-783">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="cdcc1-784">如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令行，请在 tests/RazorPagesProject.Tests 目录中的命令提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-784">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="cdcc1-785">消息应用 (SUT) 组织</span><span class="sxs-lookup"><span data-stu-id="cdcc1-785">Message app (SUT) organization</span></span>

<span data-ttu-id="cdcc1-786">SUT 是具有以下特征的 Razor Pages 消息系统：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-786">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="cdcc1-787">应用的索引页面（Pages/Index.cshtml 和 Pages/Index.cshtml.cs）提供 UI 和页面模型方法，用于控制添加、删除和分析消息（每个消息的平均字词数）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-787">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="cdcc1-788">消息由 `Message` 类 (Data/Message.cs) 描述，并具有两个属性：`Id`（键）和 `Text`（消息）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-788">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="cdcc1-789">`Text` 属性是必需的，并限制为 200 个字符。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-789">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="cdcc1-790">消息使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)存储。&#8224;</span><span class="sxs-lookup"><span data-stu-id="cdcc1-790">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="cdcc1-791">应用在其数据库上下文类 `AppDbContext` (Data/AppDbContext.cs) 中包含数据访问层 (DAL)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-791">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="cdcc1-792">如果应用启动时数据库为空，则消息存储初始化为三条消息。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-792">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="cdcc1-793">应用包含只能由经过身份验证的用户访问的 `/SecurePage`。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-793">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="cdcc1-794">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明如何将内存中数据库用于 MSTest 测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-794">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="cdcc1-795">本主题使用 [xUnit](https://xunit.github.io/) 测试框架。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-795">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="cdcc1-796">不同测试框架中的测试概念和测试实现相似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-796">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="cdcc1-797">尽管应用未使用存储库模式且不是[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些开发模式。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-797">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="cdcc1-798">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（该示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-798">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="cdcc1-799">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="cdcc1-799">Test app organization</span></span>

<span data-ttu-id="cdcc1-800">测试应用是 tests/RazorPagesProject.Tests 目录中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-800">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="cdcc1-801">测试应用目录</span><span class="sxs-lookup"><span data-stu-id="cdcc1-801">Test app directory</span></span> | <span data-ttu-id="cdcc1-802">描述</span><span class="sxs-lookup"><span data-stu-id="cdcc1-802">Description</span></span> |
| ---
<span data-ttu-id="cdcc1-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-803">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-804">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-804">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-805">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-805">'Identity'</span></span>
- <span data-ttu-id="cdcc1-806">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-806">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-807">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-807">'Razor'</span></span>
- <span data-ttu-id="cdcc1-808">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-808">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-809">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-810">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-810">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-811">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-811">'Identity'</span></span>
- <span data-ttu-id="cdcc1-812">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-812">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-813">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-813">'Razor'</span></span>
- <span data-ttu-id="cdcc1-814">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-814">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-815">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-816">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-816">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-817">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-817">'Identity'</span></span>
- <span data-ttu-id="cdcc1-818">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-818">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-819">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-819">'Razor'</span></span>
- <span data-ttu-id="cdcc1-820">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-820">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-821">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-822">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-822">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-823">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-823">'Identity'</span></span>
- <span data-ttu-id="cdcc1-824">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-824">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-825">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-825">'Razor'</span></span>
- <span data-ttu-id="cdcc1-826">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-826">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-827">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-828">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-828">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-829">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-829">'Identity'</span></span>
- <span data-ttu-id="cdcc1-830">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-830">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-831">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-831">'Razor'</span></span>
- <span data-ttu-id="cdcc1-832">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-832">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-833">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-834">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-834">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-835">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-835">'Identity'</span></span>
- <span data-ttu-id="cdcc1-836">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-836">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-837">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-837">'Razor'</span></span>
- <span data-ttu-id="cdcc1-838">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-838">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-839">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-840">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-840">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-841">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-841">'Identity'</span></span>
- <span data-ttu-id="cdcc1-842">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-842">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-843">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-843">'Razor'</span></span>
- <span data-ttu-id="cdcc1-844">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-844">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-845">--------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-846">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-846">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-847">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-847">'Identity'</span></span>
- <span data-ttu-id="cdcc1-848">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-848">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-849">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-849">'Razor'</span></span>
- <span data-ttu-id="cdcc1-850">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-850">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-851">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-852">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-852">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-853">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-853">'Identity'</span></span>
- <span data-ttu-id="cdcc1-854">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-854">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-855">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-855">'Razor'</span></span>
- <span data-ttu-id="cdcc1-856">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-856">'SignalR' uid:</span></span> 

-
<span data-ttu-id="cdcc1-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-857">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="cdcc1-858">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-858">'Blazor'</span></span>
- <span data-ttu-id="cdcc1-859">'Identity'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-859">'Identity'</span></span>
- <span data-ttu-id="cdcc1-860">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-860">'Let's Encrypt'</span></span>
- <span data-ttu-id="cdcc1-861">'Razor'</span><span class="sxs-lookup"><span data-stu-id="cdcc1-861">'Razor'</span></span>
- <span data-ttu-id="cdcc1-862">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="cdcc1-862">'SignalR' uid:</span></span> 

<span data-ttu-id="cdcc1-863">------ | | AuthTests | 包含针对以下事项的测试方法：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-863">------ | | *AuthTests* | Contains test methods for:</span></span><ul><li><span data-ttu-id="cdcc1-864">未经身份验证的用户访问安全页面。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-864">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="cdcc1-865">经过身份验证的用户访问安全页面（通过模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>）。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-865">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="cdcc1-866">获取 GitHub 用户配置文件，并检查配置文件的用户登录。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-866">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> <span data-ttu-id="cdcc1-867">| | BasicTests | 包含用于路由和内容类型的测试方法。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-867">| | *BasicTests* | Contains a test method for routing and content type.</span></span> <span data-ttu-id="cdcc1-868">| | IntegrationTests | 包含使用自定义 `WebApplicationFactory` 类的索引页面的集成测试。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-868">| | *IntegrationTests* | Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> <span data-ttu-id="cdcc1-869">| | Helpers/Utilities | </span><span class="sxs-lookup"><span data-stu-id="cdcc1-869">| | *Helpers/Utilities* | </span></span><ul><li><span data-ttu-id="cdcc1-870">Utilities.cs 包含用于通过测试数据设定数据库种子的 `InitializeDbForTests` 方法。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-870">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="cdcc1-871">HtmlHelpers.cs 提供了一种方法，用于返回 AngleSharp `IHtmlDocument` 供测试方法使用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-871">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="cdcc1-872">HttpClientExtensions.cs 为 `SendAsync` 提供重载，以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-872">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="cdcc1-873">测试框架为 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-873">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="cdcc1-874">集成测试使用 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)（包括 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)）执行。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-874">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="cdcc1-875">由于 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 包用于配置测试主机和测试服务器，因此 `TestHost` 和 `TestServer` 包在测试应用的项目文件或测试应用的开发者配置中不需要直接包引用。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-875">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="cdcc1-876">设定数据库种子以进行测试</span><span class="sxs-lookup"><span data-stu-id="cdcc1-876">**Seeding the database for testing**</span></span>

<span data-ttu-id="cdcc1-877">集成测试在执行测试前通常需要数据库中的小型数据集。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-877">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="cdcc1-878">例如，删除测试需要进行数据库记录删除，因此数据库必须至少有一个记录，删除请求才能成功。</span><span class="sxs-lookup"><span data-stu-id="cdcc1-878">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="cdcc1-879">示例应用使用 Utilities.cs 中的三个消息（测试在执行时可以使用它们）设定数据库种子：</span><span class="sxs-lookup"><span data-stu-id="cdcc1-879">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="cdcc1-880">其他资源</span><span class="sxs-lookup"><span data-stu-id="cdcc1-880">Additional resources</span></span>

* [<span data-ttu-id="cdcc1-881">单元测试</span><span class="sxs-lookup"><span data-stu-id="cdcc1-881">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
