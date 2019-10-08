---
title: ASP.NET Core 中的集成测试
author: guardrex
description: 了解集成测试如何在基础结构级别（包括数据库、文件系统和网络）确保应用组件功能正常。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/07/2019
uid: test/integration-tests
ms.openlocfilehash: 2825073962d135608c52e7bde42106e7786de521
ms.sourcegitcommit: 3d082bd46e9e00a3297ea0314582b1ed2abfa830
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2019
ms.locfileid: "72007450"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="ebfdf-103">ASP.NET Core 中的集成测试</span><span class="sxs-lookup"><span data-stu-id="ebfdf-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="ebfdf-104">作者： [Luke Latham](https://github.com/guardrex)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="ebfdf-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ebfdf-105">集成测试可确保应用程序的组件在包含应用程序支持的基础结构的级别（例如数据库、文件系统和网络）正常运行。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="ebfdf-106">ASP.NET Core 支持结合使用单元测试框架和测试 web 主机和内存中测试服务器的集成测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="ebfdf-107">本主题假定基本了解单元测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="ebfdf-108">如果不熟悉测试概念，请参阅[.Net Core 中的单元测试和 .NET Standard](/dotnet/core/testing/)主题及其链接的内容。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="ebfdf-109">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="ebfdf-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ebfdf-110">该示例应用是 Razor Pages 应用程序，并假定基本了解 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="ebfdf-111">如果不熟悉 Razor Pages，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="ebfdf-112">Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="ebfdf-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="ebfdf-113">Razor 页面入门</span><span class="sxs-lookup"><span data-stu-id="ebfdf-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="ebfdf-114">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="ebfdf-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="ebfdf-115">对于测试 Spa，我们建议使用[Selenium](https://www.seleniumhq.org/)这样的工具，它可以自动执行浏览器。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="ebfdf-116">集成测试简介</span><span class="sxs-lookup"><span data-stu-id="ebfdf-116">Introduction to integration tests</span></span>

<span data-ttu-id="ebfdf-117">与[单元测试](/dotnet/core/testing/)相比，集成测试在更广泛的级别上评估应用的组件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="ebfdf-118">单元测试用于测试独立的软件组件，如单独的类方法。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="ebfdf-119">集成测试确认两个或更多应用程序组件一起工作以生成预期的结果，其中可能包括完全处理请求所需的每个组件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="ebfdf-120">这些广泛的测试用于测试应用程序的基础结构和整个框架，通常包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="ebfdf-121">数据库</span><span class="sxs-lookup"><span data-stu-id="ebfdf-121">Database</span></span>
* <span data-ttu-id="ebfdf-122">文件系统</span><span class="sxs-lookup"><span data-stu-id="ebfdf-122">File system</span></span>
* <span data-ttu-id="ebfdf-123">网络设备</span><span class="sxs-lookup"><span data-stu-id="ebfdf-123">Network appliances</span></span>
* <span data-ttu-id="ebfdf-124">请求-响应管道</span><span class="sxs-lookup"><span data-stu-id="ebfdf-124">Request-response pipeline</span></span>

<span data-ttu-id="ebfdf-125">单元测试使用称为*fakes*或*mock 对象*的制造组件来代替基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="ebfdf-126">与单元测试相比，集成测试：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="ebfdf-127">使用应用在生产环境中使用的实际组件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="ebfdf-128">需要进行更多的代码和数据处理。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-128">Require more code and data processing.</span></span>
* <span data-ttu-id="ebfdf-129">需要较长时间才能运行。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-129">Take longer to run.</span></span>

<span data-ttu-id="ebfdf-130">因此，将集成测试的使用限制为最重要的基础结构方案。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="ebfdf-131">如果可以使用单元测试或集成测试来测试行为，请选择单元测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="ebfdf-132">请勿为数据库和文件系统的每个可能的数据排列和文件访问编写集成测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="ebfdf-133">无论应用程序中有多少位置与数据库和文件系统交互，一组集中式的读取、写入、更新和删除集成测试通常都能充分测试数据库和文件系统组件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="ebfdf-134">使用单元测试对与这些组件进行交互的方法逻辑进行例程测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="ebfdf-135">在单元测试中，使用基础结构 fakes/模拟会导致更快地执行测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="ebfdf-136">在讨论集成测试时，测试的项目经常称为 "测试中的*系统*" 或简称 "SUT"。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="ebfdf-137">*本主题中使用 "SUT" 来引用已测试的 ASP.NET Core 应用。*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="ebfdf-138">ASP.NET Core 集成测试</span><span class="sxs-lookup"><span data-stu-id="ebfdf-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="ebfdf-139">ASP.NET Core 中的集成测试需要以下各项：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="ebfdf-140">测试项目用于包含和执行测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="ebfdf-141">测试项目具有对 SUT 的引用。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="ebfdf-142">测试项目将为 SUT 创建测试 web 主机，并使用测试服务器客户端来处理 SUT 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="ebfdf-143">测试运行程序用于执行测试并报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="ebfdf-144">集成测试按一个顺序特定的事件序列发生, 其中包括常见的 *Arrange*、*Act* 和 *Assert* 测试步骤:</span><span class="sxs-lookup"><span data-stu-id="ebfdf-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="ebfdf-145">已配置 SUT 的 web 主机。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="ebfdf-146">创建测试服务器客户端以向应用程序提交请求。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="ebfdf-147">执行 *Arrange* 测试步骤:测试应用将准备一个请求。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="ebfdf-148">执行*Act*测试步骤：客户端提交请求并接收响应。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="ebfdf-149">执行*Assert*测试步骤：*实际*响应会根据*预期*响应验证为*通过*或*失败*。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="ebfdf-150">此过程将一直继续，直到执行了所有测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="ebfdf-151">将报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-151">The test results are reported.</span></span>

<span data-ttu-id="ebfdf-152">通常，测试 web 主机的配置与应用程序用于测试运行的普通 web 主机的配置不同。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="ebfdf-153">例如，可以将不同的数据库或不同的应用设置用于测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="ebfdf-154">基础结构组件（例如测试 web 主机和内存中测试服务器（[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)））由[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包提供或管理。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="ebfdf-155">使用此包简化了测试的创建和执行。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="ebfdf-156">@No__t-0 包处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="ebfdf-157">将依赖项文件（ *. .deps.json*）从 SUT 复制到测试项目的*bin*目录中。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="ebfdf-158">将[内容根](xref:fundamentals/index#content-root)设置为 SUT 的项目根，以便在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="ebfdf-159">提供[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)类，以简化与 `TestServer` 的 SUT 的引导。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="ebfdf-160">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍了如何设置测试项目和测试运行程序，以及有关如何为测试和测试类命名测试和建议的详细说明。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="ebfdf-161">为应用程序创建测试项目时，请将集成测试中的单元测试分成不同的项目。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="ebfdf-162">这有助于确保不会意外地将基础结构测试组件包含在单元测试中。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="ebfdf-163">单元测试和集成测试的隔离还允许控制运行的测试集。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="ebfdf-164">Razor Pages 应用和 MVC 应用的测试的配置几乎没有任何区别。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="ebfdf-165">唯一的区别在于测试的命名方式。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="ebfdf-166">在 Razor Pages 应用中，页终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 来测试索引页的组件集成）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="ebfdf-167">在 MVC 应用中，通常按控制器类对测试进行组织，并按它们所测试的控制器（例如 `HomeControllerTests` 来测试主控制器的组件集成）进行命名。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="ebfdf-168">测试应用必备组件</span><span class="sxs-lookup"><span data-stu-id="ebfdf-168">Test app prerequisites</span></span>

<span data-ttu-id="ebfdf-169">测试项目必须：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-169">The test project must:</span></span>

* <span data-ttu-id="ebfdf-170">请参阅[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="ebfdf-171">在项目文件中指定 Web SDK （`<Project Sdk="Microsoft.NET.Sdk.Web">`）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="ebfdf-172">可以在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-172">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="ebfdf-173">检查 "*测试"/"RazorPagesProject"/"RazorPagesProject* " 文件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="ebfdf-174">示例应用使用[xUnit](https://xunit.github.io/)测试框架和[AngleSharp](https://anglesharp.github.io/)分析器库，因此示例应用还引用：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="ebfdf-175">xunit</span><span class="sxs-lookup"><span data-stu-id="ebfdf-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="ebfdf-176">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="ebfdf-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="ebfdf-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="ebfdf-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="ebfdf-178">还可在测试中使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="ebfdf-179">应用引用：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-179">The app references:</span></span>

* [<span data-ttu-id="ebfdf-180">AspNetCore. Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="ebfdf-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [<span data-ttu-id="ebfdf-181">AspNetCore. Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="ebfdf-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [<span data-ttu-id="ebfdf-182">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="ebfdf-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="ebfdf-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="ebfdf-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="ebfdf-184">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="ebfdf-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="ebfdf-185">SUT 环境</span><span class="sxs-lookup"><span data-stu-id="ebfdf-185">SUT environment</span></span>

<span data-ttu-id="ebfdf-186">如果未设置 SUT 的[环境](xref:fundamentals/environments)，环境将默认为 "开发"。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="ebfdf-187">具有默认 WebApplicationFactory 的基本测试</span><span class="sxs-lookup"><span data-stu-id="ebfdf-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="ebfdf-188">[WebApplicationFactory @ no__t-1TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用于创建集成测试的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="ebfdf-189">@no__t 为 SUT 的入口点类，通常为 @no__t 的类。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="ebfdf-190">测试类实现*类装置*接口（[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)）以指示类包含测试，并跨类中的测试提供共享对象实例。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="ebfdf-191">应用终结点的基本测试</span><span class="sxs-lookup"><span data-stu-id="ebfdf-191">Basic test of app endpoints</span></span>

<span data-ttu-id="ebfdf-192">下面的测试类 `BasicTests`，使用 @no__t 来启动 SUT，并为测试方法提供[HttpClient](/dotnet/api/system.net.http.httpclient) @no__t。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-192">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="ebfdf-193">此方法检查响应状态代码是否成功（范围200-299 中的状态代码）和 `Content-Type` 标头对于多个应用程序页 @no__t 为-1。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-193">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="ebfdf-194">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)创建自动跟随重定向并处理 cookie 的 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-194">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="ebfdf-195">默认情况下，当启用[GDPR 同意策略](xref:security/gdpr)时，不会跨请求保留非关键 cookie。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-195">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="ebfdf-196">若要保留不重要的 cookie （如 TempData 提供程序使用的 cookie），请将它们标记为测试中的重要 cookie。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-196">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="ebfdf-197">有关将 cookie 标记为必要的说明，请参阅[基本 cookie](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-197">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="ebfdf-198">测试安全终结点</span><span class="sxs-lookup"><span data-stu-id="ebfdf-198">Test a secure endpoint</span></span>

<span data-ttu-id="ebfdf-199">@No__t-0 类中的另一个测试检查安全终结点是否将未经身份验证的用户重定向到应用程序的登录页。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-199">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="ebfdf-200">在 SUT 中，`/SecurePage` 页面使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)约定将[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)应用到页面。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-200">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="ebfdf-201">有关详细信息，请参阅[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-201">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="ebfdf-202">在`Get_SecurePageRequiresAnAuthenticatedUser`测试中, 通过将[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)设置为`false`, 将 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 设置为禁止重定向:</span><span class="sxs-lookup"><span data-stu-id="ebfdf-202">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="ebfdf-203">通过禁止客户端按照重定向操作，可以执行以下检查：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-203">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="ebfdf-204">可以对照预期的[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)结果检查 SUT 返回的状态代码，而不是在重定向到登录页后返回最终状态代码，这会是[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-204">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="ebfdf-205">系统将检查响应标头中的 @no__t 0 标头值，以确认它以 @no__t 1 而不是最终的登录页响应开头，其中 @no__t 2 标头不存在。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-205">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="ebfdf-206">有关 `WebApplicationFactoryClientOptions` 的详细信息，请参阅[Client options](#client-options)部分。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-206">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="ebfdf-207">自定义 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="ebfdf-207">Customize WebApplicationFactory</span></span>

<span data-ttu-id="ebfdf-208">通过继承 `WebApplicationFactory` 创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-208">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="ebfdf-209">从 @no__t 继承，并覆盖[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-209">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="ebfdf-210">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)允许通过[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)配置服务集合：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-210">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="ebfdf-211">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-211">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="ebfdf-212">0Integration 测试示例 @no__t 中介绍了方法：测试应用组织 @ no__t 部分。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-212">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="ebfdf-213">使用测试类中的自定义 `CustomWebApplicationFactory`。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-213">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="ebfdf-214">下面的示例使用 `IndexPageTests` 类中的工厂：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-214">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="ebfdf-215">示例应用的客户端配置为阻止以下重定向中的 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-215">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="ebfdf-216">如 "[测试安全终结点](#test-a-secure-endpoint)" 一节中所述，这允许测试检查应用程序的第一个响应的结果。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-216">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="ebfdf-217">第一个响应是使用 @no__t 0 标头在其中许多测试中进行重定向。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-217">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="ebfdf-218">典型测试使用 @no__t 0 和 helper 方法来处理请求和响应：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-218">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="ebfdf-219">对 SUT 的任何 POST 请求必须满足防伪检查，该检查是由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动完成的。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-219">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="ebfdf-220">为了安排测试的 POST 请求，测试应用必须：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-220">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="ebfdf-221">发出对页面的请求。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-221">Make a request for the page.</span></span>
1. <span data-ttu-id="ebfdf-222">分析防伪 cookie 并请求响应中的验证令牌。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-222">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="ebfdf-223">发出带有防伪 cookie 的 POST 请求，并就地请求验证令牌。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-223">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="ebfdf-224">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 @no__t 0 帮助器扩展方法（Helper */HttpClientExtensions*）和 `GetDocumentAsync` helper 方法（helper */HtmlHelpers*）使用[AngleSharp](https://anglesharp.github.io/)分析器来处理防伪检查以下方法：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-224">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="ebfdf-225">`GetDocumentAsync` &ndash; 接收 [HttpResponseMessage ](/dotnet/api/system.net.http.httpresponsemessage)并返回`IHtmlDocument`。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-225">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="ebfdf-226">`GetDocumentAsync` 使用工厂来准备基于原始 `HttpResponseMessage` 的*虚拟响应*。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-226">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="ebfdf-227">有关详细信息，请参阅[AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-227">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="ebfdf-228">@no__t `HttpClient` 扩展方法：编写[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)并调用[SendAsync （HttpRequestMessage）](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-228">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="ebfdf-229">@No__t 的重载会接受 HTML 格式（`IHtmlFormElement`）和以下内容：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-229">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="ebfdf-230">窗体的 "提交" 按钮（`IHtmlElement`）</span><span class="sxs-lookup"><span data-stu-id="ebfdf-230">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="ebfdf-231">窗体值集合（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="ebfdf-231">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="ebfdf-232">提交按钮（`IHtmlElement`）和窗体值（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="ebfdf-232">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="ebfdf-233">[AngleSharp](https://anglesharp.github.io/)是用于演示目的的第三方解析库，适用于本主题和示例应用。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-233">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="ebfdf-234">ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-234">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="ebfdf-235">可以使用其他分析器，如[Html 灵活性包（HAP）](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-235">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="ebfdf-236">另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-236">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="ebfdf-237">用 WithWebHostBuilder 自定义客户端</span><span class="sxs-lookup"><span data-stu-id="ebfdf-237">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="ebfdf-238">如果在测试方法中需要其他配置， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)会创建一个新的 `WebApplicationFactory`，其中包含由配置进一步自定义的[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-238">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="ebfdf-239">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的 @no__t 0 测试方法演示了 `WithWebHostBuilder` 的使用。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-239">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="ebfdf-240">此测试通过在 SUT 中触发窗体提交来在数据库中执行记录删除。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-240">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="ebfdf-241">因为 `IndexPageTests` 类中的另一个测试会执行一个操作，该操作将删除数据库中的所有记录，并且该操作可能在 @no__t 1 方法之前运行，因此，该数据库在此测试方法中种子，以确保存在记录，以便将其删除。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-241">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="ebfdf-242">选择 SUT 中 `messages` 窗体的第一个 "删除" 按钮时，会将请求中的内容模拟到 SUT：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-242">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="ebfdf-243">客户端选项</span><span class="sxs-lookup"><span data-stu-id="ebfdf-243">Client options</span></span>

<span data-ttu-id="ebfdf-244">下表显示了创建 `HttpClient` 实例时可用的默认[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-244">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="ebfdf-245">选项</span><span class="sxs-lookup"><span data-stu-id="ebfdf-245">Option</span></span> | <span data-ttu-id="ebfdf-246">描述</span><span class="sxs-lookup"><span data-stu-id="ebfdf-246">Description</span></span> | <span data-ttu-id="ebfdf-247">默认</span><span class="sxs-lookup"><span data-stu-id="ebfdf-247">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="ebfdf-248">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="ebfdf-248">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="ebfdf-249">获取或设置 @no__t 实例是否应自动跟随重定向响应。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-249">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="ebfdf-250">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="ebfdf-250">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="ebfdf-251">获取或设置 @no__t 实例的基址。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-251">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="ebfdf-252">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="ebfdf-252">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="ebfdf-253">获取或设置 @no__t 实例是否应处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-253">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="ebfdf-254">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="ebfdf-254">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="ebfdf-255">获取或设置 @no__t 实例应遵循的重定向响应的最大数目。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-255">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="ebfdf-256">7</span><span class="sxs-lookup"><span data-stu-id="ebfdf-256">7</span></span> |

<span data-ttu-id="ebfdf-257">创建`WebApplicationFactoryClientOptions`类并将其传递给 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 方法 (在代码示例中显示默认值):</span><span class="sxs-lookup"><span data-stu-id="ebfdf-257">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="ebfdf-258">注入模拟服务</span><span class="sxs-lookup"><span data-stu-id="ebfdf-258">Inject mock services</span></span>

<span data-ttu-id="ebfdf-259">通过在主机生成器上调用[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，可以在测试中覆盖服务。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-259">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="ebfdf-260">**若要注入 mock 服务，SUT 必须具有具有 @no__t 2 方法的 @no__t 1 类。**</span><span class="sxs-lookup"><span data-stu-id="ebfdf-260">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="ebfdf-261">示例 SUT 包含一个返回 quote 的作用域服务。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-261">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="ebfdf-262">请求索引页时，引号嵌入到索引页上的隐藏字段中。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-262">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="ebfdf-263">*服务/IQuoteService*：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-263">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="ebfdf-264">*服务/QuoteService*：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-264">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="ebfdf-265">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-265">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="ebfdf-266">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-266">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="ebfdf-267">*Pages/Index. cs*：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-267">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="ebfdf-268">运行 SUT 应用时，将生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-268">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="ebfdf-269">若要在集成测试中测试服务和引号注入，测试会将模拟服务注入到 SUT。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-269">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="ebfdf-270">模拟服务会将应用的 @no__t 0 替换为测试应用提供的服务，称为 `TestQuoteService`：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-270">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="ebfdf-271">*IntegrationTests.IndexPageTests.cs*：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-271">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="ebfdf-272">调用 `ConfigureTestServices`，并注册作用域内服务：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-272">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="ebfdf-273">在测试执行过程中生成的标记反映 @no__t 提供的引号文本，因此断言通过：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-273">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="ebfdf-274">测试基础结构如何推断应用内容根路径</span><span class="sxs-lookup"><span data-stu-id="ebfdf-274">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="ebfdf-275">@No__t-0 构造函数通过搜索包含集成测试的程序集上的[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)来推断应用[内容根](xref:fundamentals/index#content-root)路径，该键等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName`。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-275">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="ebfdf-276">如果找不到具有正确键的属性，`WebApplicationFactory` 将回退到搜索解决方案文件（ *.sln*），并在解决方案目录中追加 @no__t 2 程序集名称。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-276">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="ebfdf-277">应用根目录（内容根路径）用于发现视图和内容文件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-277">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="ebfdf-278">禁用卷影复制</span><span class="sxs-lookup"><span data-stu-id="ebfdf-278">Disable shadow copying</span></span>

<span data-ttu-id="ebfdf-279">卷影复制会导致在输出目录以外的目录中执行测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-279">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="ebfdf-280">要使测试正常工作，必须禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-280">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="ebfdf-281">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的*xUnit*文件来禁用 xUnit 的卷影复制。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-281">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="ebfdf-282">有关详细信息，请参阅[配置 xUnit 与 JSON](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-282">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="ebfdf-283">将*xunit*文件添加到测试项目的根，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-283">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="ebfdf-284">对象的处理</span><span class="sxs-lookup"><span data-stu-id="ebfdf-284">Disposal of objects</span></span>

<span data-ttu-id="ebfdf-285">执行 `IClassFixture` 实现的测试后，当 xUnit 释放[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)时，将释放[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-285">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="ebfdf-286">如果开发人员实例化的对象需要处置，请在 `IClassFixture` 实现中释放这些对象。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-286">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="ebfdf-287">有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-287">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="ebfdf-288">集成测试示例</span><span class="sxs-lookup"><span data-stu-id="ebfdf-288">Integration tests sample</span></span>

<span data-ttu-id="ebfdf-289">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由两个应用组成：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-289">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="ebfdf-290">应用</span><span class="sxs-lookup"><span data-stu-id="ebfdf-290">App</span></span> | <span data-ttu-id="ebfdf-291">项目目录</span><span class="sxs-lookup"><span data-stu-id="ebfdf-291">Project directory</span></span> | <span data-ttu-id="ebfdf-292">描述</span><span class="sxs-lookup"><span data-stu-id="ebfdf-292">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="ebfdf-293">消息应用（SUT）</span><span class="sxs-lookup"><span data-stu-id="ebfdf-293">Message app (the SUT)</span></span> | <span data-ttu-id="ebfdf-294">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-294">*src/RazorPagesProject*</span></span> | <span data-ttu-id="ebfdf-295">允许用户添加、删除一个、删除和分析消息。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-295">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="ebfdf-296">测试应用</span><span class="sxs-lookup"><span data-stu-id="ebfdf-296">Test app</span></span> | <span data-ttu-id="ebfdf-297">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-297">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="ebfdf-298">用于集成测试 SUT。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-298">Used to integration test the SUT.</span></span> |

<span data-ttu-id="ebfdf-299">可以使用 IDE （如[Visual Studio](https://visualstudio.microsoft.com)）的内置测试功能来运行测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-299">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="ebfdf-300">如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在*RazorPagesProject 目录中的命令*提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-300">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="ebfdf-301">消息应用（SUT）组织</span><span class="sxs-lookup"><span data-stu-id="ebfdf-301">Message app (SUT) organization</span></span>

<span data-ttu-id="ebfdf-302">SUT 是 Razor Pages 的消息系统，具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-302">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="ebfdf-303">应用的 "索引" 页（*Pages/索引. cshtml*和*pages/index*）提供了一个 UI 和页面模型方法，用于控制消息的添加、删除和分析（每条消息的平均单词数）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-303">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="ebfdf-304">消息由具有两个属性的 @no__t 0 类（*Data/message*）描述： `Id` （键）和 `Text` （message）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-304">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="ebfdf-305">@No__t-0 属性是必需的，并且限制为200个字符。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-305">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="ebfdf-306">使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-306">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="ebfdf-307">应用在其数据库上下文 @no__t 类（*AppDbContext*）中包含数据访问层（DAL）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-307">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="ebfdf-308">如果数据库在应用启动时为空，则会用三条消息初始化消息存储。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-308">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="ebfdf-309">应用包括只能由经过身份验证的用户访问的 @no__t 0。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-309">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="ebfdf-310">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-310">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="ebfdf-311">本主题使用[xUnit](https://xunit.github.io/)测试框架。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-311">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="ebfdf-312">不同测试框架中的测试概念和测试实现相似，但并不完全相同。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-312">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="ebfdf-313">尽管应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-313">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="ebfdf-314">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-314">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="ebfdf-315">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="ebfdf-315">Test app organization</span></span>

<span data-ttu-id="ebfdf-316">测试应用是 "*测试"/"RazorPagesProject* " 目录中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-316">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="ebfdf-317">测试应用程序目录</span><span class="sxs-lookup"><span data-stu-id="ebfdf-317">Test app directory</span></span> | <span data-ttu-id="ebfdf-318">描述</span><span class="sxs-lookup"><span data-stu-id="ebfdf-318">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="ebfdf-319">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-319">*BasicTests*</span></span> | <span data-ttu-id="ebfdf-320">*BasicTests.cs*包含用于路由的测试方法、通过未经身份验证的用户访问安全页以及获取 GitHub 用户配置文件和检查配置文件的用户登录名。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-320">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="ebfdf-321">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-321">*IntegrationTests*</span></span> | <span data-ttu-id="ebfdf-322">*IndexPageTests.cs*包含使用自定义 `WebApplicationFactory` 类的索引页的集成测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-322">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="ebfdf-323">*帮助程序/实用工具*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-323">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="ebfdf-324">*Utilities.cs*包含用于使数据库具有测试数据的 @no__t 1 方法。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-324">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="ebfdf-325">*HtmlHelpers.cs*提供了一个方法，用于返回 AngleSharp `IHtmlDocument` 以供测试方法使用。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-325">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="ebfdf-326">*HttpClientExtensions.cs*提供 `SendAsync` 的重载，以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-326">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="ebfdf-327">测试框架为[xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-327">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="ebfdf-328">集成测试是使用 TestHost 的[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)进行的，其中包括[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-328">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="ebfdf-329">由于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包用于配置测试主机和测试服务器，因此，在测试应用程序的项目文件或测试的开发人员配置中，`TestHost` 和 `TestServer` 包不需要直接包引用应用.</span><span class="sxs-lookup"><span data-stu-id="ebfdf-329">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="ebfdf-330">**播种要测试的数据库**</span><span class="sxs-lookup"><span data-stu-id="ebfdf-330">**Seeding the database for testing**</span></span>

<span data-ttu-id="ebfdf-331">集成测试在执行测试前通常需要数据库中的一个小型数据集。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-331">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="ebfdf-332">例如，删除测试调用数据库记录，因此数据库必须至少有一条记录，删除请求才能成功。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-332">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="ebfdf-333">该示例应用在*Utilities.cs*中使用三个消息对数据库进行种子设定，当测试执行时，可以使用这些消息：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-333">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ebfdf-334">集成测试可确保应用程序的组件在包含应用程序支持的基础结构的级别（例如数据库、文件系统和网络）正常运行。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-334">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="ebfdf-335">ASP.NET Core 支持结合使用单元测试框架和测试 web 主机和内存中测试服务器的集成测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-335">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="ebfdf-336">本主题假定基本了解单元测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-336">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="ebfdf-337">如果不熟悉测试概念，请参阅[.Net Core 中的单元测试和 .NET Standard](/dotnet/core/testing/)主题及其链接的内容。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-337">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="ebfdf-338">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="ebfdf-338">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="ebfdf-339">该示例应用是 Razor Pages 应用程序，并假定基本了解 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-339">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="ebfdf-340">如果不熟悉 Razor Pages，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-340">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="ebfdf-341">Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="ebfdf-341">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="ebfdf-342">Razor 页面入门</span><span class="sxs-lookup"><span data-stu-id="ebfdf-342">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="ebfdf-343">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="ebfdf-343">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="ebfdf-344">对于测试 Spa，我们建议使用[Selenium](https://www.seleniumhq.org/)这样的工具，它可以自动执行浏览器。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-344">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="ebfdf-345">集成测试简介</span><span class="sxs-lookup"><span data-stu-id="ebfdf-345">Introduction to integration tests</span></span>

<span data-ttu-id="ebfdf-346">与[单元测试](/dotnet/core/testing/)相比，集成测试在更广泛的级别上评估应用的组件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-346">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="ebfdf-347">单元测试用于测试独立的软件组件，如单独的类方法。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-347">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="ebfdf-348">集成测试确认两个或更多应用程序组件一起工作以生成预期的结果，其中可能包括完全处理请求所需的每个组件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-348">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="ebfdf-349">这些广泛的测试用于测试应用程序的基础结构和整个框架，通常包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-349">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="ebfdf-350">数据库</span><span class="sxs-lookup"><span data-stu-id="ebfdf-350">Database</span></span>
* <span data-ttu-id="ebfdf-351">文件系统</span><span class="sxs-lookup"><span data-stu-id="ebfdf-351">File system</span></span>
* <span data-ttu-id="ebfdf-352">网络设备</span><span class="sxs-lookup"><span data-stu-id="ebfdf-352">Network appliances</span></span>
* <span data-ttu-id="ebfdf-353">请求-响应管道</span><span class="sxs-lookup"><span data-stu-id="ebfdf-353">Request-response pipeline</span></span>

<span data-ttu-id="ebfdf-354">单元测试使用称为*fakes*或*mock 对象*的制造组件来代替基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-354">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="ebfdf-355">与单元测试相比，集成测试：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-355">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="ebfdf-356">使用应用在生产环境中使用的实际组件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-356">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="ebfdf-357">需要进行更多的代码和数据处理。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-357">Require more code and data processing.</span></span>
* <span data-ttu-id="ebfdf-358">需要较长时间才能运行。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-358">Take longer to run.</span></span>

<span data-ttu-id="ebfdf-359">因此，将集成测试的使用限制为最重要的基础结构方案。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-359">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="ebfdf-360">如果可以使用单元测试或集成测试来测试行为，请选择单元测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-360">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="ebfdf-361">请勿为数据库和文件系统的每个可能的数据排列和文件访问编写集成测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-361">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="ebfdf-362">无论应用程序中有多少位置与数据库和文件系统交互，一组集中式的读取、写入、更新和删除集成测试通常都能充分测试数据库和文件系统组件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-362">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="ebfdf-363">使用单元测试对与这些组件进行交互的方法逻辑进行例程测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-363">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="ebfdf-364">在单元测试中，使用基础结构 fakes/模拟会导致更快地执行测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-364">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="ebfdf-365">在讨论集成测试时，测试的项目经常称为 "测试中的*系统*" 或简称 "SUT"。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-365">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="ebfdf-366">*本主题中使用 "SUT" 来引用已测试的 ASP.NET Core 应用。*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-366">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="ebfdf-367">ASP.NET Core 集成测试</span><span class="sxs-lookup"><span data-stu-id="ebfdf-367">ASP.NET Core integration tests</span></span>

<span data-ttu-id="ebfdf-368">ASP.NET Core 中的集成测试需要以下各项：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-368">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="ebfdf-369">测试项目用于包含和执行测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-369">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="ebfdf-370">测试项目具有对 SUT 的引用。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-370">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="ebfdf-371">测试项目将为 SUT 创建测试 web 主机，并使用测试服务器客户端来处理 SUT 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-371">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="ebfdf-372">测试运行程序用于执行测试并报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-372">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="ebfdf-373">集成测试按一个顺序特定的事件序列发生, 其中包括常见的 *Arrange*、*Act* 和 *Assert* 测试步骤:</span><span class="sxs-lookup"><span data-stu-id="ebfdf-373">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="ebfdf-374">已配置 SUT 的 web 主机。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-374">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="ebfdf-375">创建测试服务器客户端以向应用程序提交请求。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-375">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="ebfdf-376">执行 *Arrange* 测试步骤:测试应用将准备一个请求。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-376">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="ebfdf-377">执行*Act*测试步骤：客户端提交请求并接收响应。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-377">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="ebfdf-378">执行*Assert*测试步骤：*实际*响应会根据*预期*响应验证为*通过*或*失败*。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-378">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="ebfdf-379">此过程将一直继续，直到执行了所有测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-379">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="ebfdf-380">将报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-380">The test results are reported.</span></span>

<span data-ttu-id="ebfdf-381">通常，测试 web 主机的配置与应用程序用于测试运行的普通 web 主机的配置不同。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-381">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="ebfdf-382">例如，可以将不同的数据库或不同的应用设置用于测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-382">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="ebfdf-383">基础结构组件（例如测试 web 主机和内存中测试服务器（[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)））由[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包提供或管理。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-383">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="ebfdf-384">使用此包简化了测试的创建和执行。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-384">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="ebfdf-385">@No__t-0 包处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-385">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="ebfdf-386">将依赖项文件（ *. .deps.json*）从 SUT 复制到测试项目的*bin*目录中。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-386">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="ebfdf-387">将[内容根](xref:fundamentals/index#content-root)设置为 SUT 的项目根，以便在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-387">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="ebfdf-388">提供[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)类，以简化与 `TestServer` 的 SUT 的引导。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-388">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="ebfdf-389">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍了如何设置测试项目和测试运行程序，以及有关如何为测试和测试类命名测试和建议的详细说明。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-389">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="ebfdf-390">为应用程序创建测试项目时，请将集成测试中的单元测试分成不同的项目。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-390">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="ebfdf-391">这有助于确保不会意外地将基础结构测试组件包含在单元测试中。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-391">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="ebfdf-392">单元测试和集成测试的隔离还允许控制运行的测试集。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-392">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="ebfdf-393">Razor Pages 应用和 MVC 应用的测试的配置几乎没有任何区别。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-393">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="ebfdf-394">唯一的区别在于测试的命名方式。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-394">The only difference is in how the tests are named.</span></span> <span data-ttu-id="ebfdf-395">在 Razor Pages 应用中，页终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 来测试索引页的组件集成）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-395">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="ebfdf-396">在 MVC 应用中，通常按控制器类对测试进行组织，并按它们所测试的控制器（例如 `HomeControllerTests` 来测试主控制器的组件集成）进行命名。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-396">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="ebfdf-397">测试应用必备组件</span><span class="sxs-lookup"><span data-stu-id="ebfdf-397">Test app prerequisites</span></span>

<span data-ttu-id="ebfdf-398">测试项目必须：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-398">The test project must:</span></span>

* <span data-ttu-id="ebfdf-399">引用以下包：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-399">Reference the following packages:</span></span>
  * [<span data-ttu-id="ebfdf-400">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="ebfdf-400">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="ebfdf-401">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="ebfdf-401">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="ebfdf-402">在项目文件中指定 Web SDK （`<Project Sdk="Microsoft.NET.Sdk.Web">`）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-402">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="ebfdf-403">引用[AspNetCore 元包](xref:fundamentals/metapackage-app)时，需要 Web SDK。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-403">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="ebfdf-404">可以在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-404">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="ebfdf-405">检查 "*测试"/"RazorPagesProject"/"RazorPagesProject* " 文件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-405">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="ebfdf-406">示例应用使用[xUnit](https://xunit.github.io/)测试框架和[AngleSharp](https://anglesharp.github.io/)分析器库，因此示例应用还引用：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-406">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="ebfdf-407">xunit</span><span class="sxs-lookup"><span data-stu-id="ebfdf-407">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="ebfdf-408">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="ebfdf-408">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="ebfdf-409">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="ebfdf-409">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="ebfdf-410">SUT 环境</span><span class="sxs-lookup"><span data-stu-id="ebfdf-410">SUT environment</span></span>

<span data-ttu-id="ebfdf-411">如果未设置 SUT 的[环境](xref:fundamentals/environments)，环境将默认为 "开发"。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-411">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="ebfdf-412">具有默认 WebApplicationFactory 的基本测试</span><span class="sxs-lookup"><span data-stu-id="ebfdf-412">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="ebfdf-413">[WebApplicationFactory @ no__t-1TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用于创建集成测试的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-413">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="ebfdf-414">@no__t 为 SUT 的入口点类，通常为 @no__t 的类。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-414">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="ebfdf-415">测试类实现*类装置*接口（[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)）以指示类包含测试，并跨类中的测试提供共享对象实例。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-415">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="ebfdf-416">应用终结点的基本测试</span><span class="sxs-lookup"><span data-stu-id="ebfdf-416">Basic test of app endpoints</span></span>

<span data-ttu-id="ebfdf-417">下面的测试类 `BasicTests`，使用 @no__t 来启动 SUT，并为测试方法提供[HttpClient](/dotnet/api/system.net.http.httpclient) @no__t。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-417">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="ebfdf-418">此方法检查响应状态代码是否成功（范围200-299 中的状态代码）和 `Content-Type` 标头对于多个应用程序页 @no__t 为-1。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-418">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="ebfdf-419">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)创建自动跟随重定向并处理 cookie 的 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-419">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="ebfdf-420">默认情况下，当启用[GDPR 同意策略](xref:security/gdpr)时，不会跨请求保留非关键 cookie。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-420">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="ebfdf-421">若要保留不重要的 cookie （如 TempData 提供程序使用的 cookie），请将它们标记为测试中的重要 cookie。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-421">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="ebfdf-422">有关将 cookie 标记为必要的说明，请参阅[基本 cookie](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-422">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="ebfdf-423">测试安全终结点</span><span class="sxs-lookup"><span data-stu-id="ebfdf-423">Test a secure endpoint</span></span>

<span data-ttu-id="ebfdf-424">@No__t-0 类中的另一个测试检查安全终结点是否将未经身份验证的用户重定向到应用程序的登录页。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-424">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="ebfdf-425">在 SUT 中，`/SecurePage` 页面使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)约定将[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)应用到页面。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-425">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="ebfdf-426">有关详细信息，请参阅[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-426">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="ebfdf-427">在`Get_SecurePageRequiresAnAuthenticatedUser`测试中, 通过将[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)设置为`false`, 将 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 设置为禁止重定向:</span><span class="sxs-lookup"><span data-stu-id="ebfdf-427">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="ebfdf-428">通过禁止客户端按照重定向操作，可以执行以下检查：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-428">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="ebfdf-429">可以对照预期的[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)结果检查 SUT 返回的状态代码，而不是在重定向到登录页后返回最终状态代码，这会是[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-429">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="ebfdf-430">系统将检查响应标头中的 @no__t 0 标头值，以确认它以 @no__t 1 而不是最终的登录页响应开头，其中 @no__t 2 标头不存在。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-430">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="ebfdf-431">有关 `WebApplicationFactoryClientOptions` 的详细信息，请参阅[Client options](#client-options)部分。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-431">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="ebfdf-432">自定义 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="ebfdf-432">Customize WebApplicationFactory</span></span>

<span data-ttu-id="ebfdf-433">通过继承 `WebApplicationFactory` 创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-433">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="ebfdf-434">从 @no__t 继承，并覆盖[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-434">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="ebfdf-435">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)允许通过[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)配置服务集合：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-435">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="ebfdf-436">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-436">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="ebfdf-437">0Integration 测试示例 @no__t 中介绍了方法：测试应用组织 @ no__t 部分。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-437">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="ebfdf-438">使用测试类中的自定义 `CustomWebApplicationFactory`。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-438">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="ebfdf-439">下面的示例使用 `IndexPageTests` 类中的工厂：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-439">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="ebfdf-440">示例应用的客户端配置为阻止以下重定向中的 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-440">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="ebfdf-441">如 "[测试安全终结点](#test-a-secure-endpoint)" 一节中所述，这允许测试检查应用程序的第一个响应的结果。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-441">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="ebfdf-442">第一个响应是使用 @no__t 0 标头在其中许多测试中进行重定向。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-442">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="ebfdf-443">典型测试使用 @no__t 0 和 helper 方法来处理请求和响应：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-443">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="ebfdf-444">对 SUT 的任何 POST 请求必须满足防伪检查，该检查是由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动完成的。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-444">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="ebfdf-445">为了安排测试的 POST 请求，测试应用必须：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-445">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="ebfdf-446">发出对页面的请求。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-446">Make a request for the page.</span></span>
1. <span data-ttu-id="ebfdf-447">分析防伪 cookie 并请求响应中的验证令牌。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-447">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="ebfdf-448">发出带有防伪 cookie 的 POST 请求，并就地请求验证令牌。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-448">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="ebfdf-449">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 @no__t 0 帮助器扩展方法（Helper */HttpClientExtensions*）和 `GetDocumentAsync` helper 方法（helper */HtmlHelpers*）使用[AngleSharp](https://anglesharp.github.io/)分析器来处理防伪检查以下方法：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-449">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="ebfdf-450">`GetDocumentAsync` &ndash; 接收 [HttpResponseMessage ](/dotnet/api/system.net.http.httpresponsemessage)并返回`IHtmlDocument`。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-450">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="ebfdf-451">`GetDocumentAsync` 使用工厂来准备基于原始 `HttpResponseMessage` 的*虚拟响应*。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-451">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="ebfdf-452">有关详细信息，请参阅[AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-452">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="ebfdf-453">@no__t `HttpClient` 扩展方法：编写[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)并调用[SendAsync （HttpRequestMessage）](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-453">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="ebfdf-454">@No__t 的重载会接受 HTML 格式（`IHtmlFormElement`）和以下内容：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-454">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="ebfdf-455">窗体的 "提交" 按钮（`IHtmlElement`）</span><span class="sxs-lookup"><span data-stu-id="ebfdf-455">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="ebfdf-456">窗体值集合（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="ebfdf-456">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="ebfdf-457">提交按钮（`IHtmlElement`）和窗体值（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="ebfdf-457">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="ebfdf-458">[AngleSharp](https://anglesharp.github.io/)是用于演示目的的第三方解析库，适用于本主题和示例应用。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-458">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="ebfdf-459">ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-459">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="ebfdf-460">可以使用其他分析器，如[Html 灵活性包（HAP）](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-460">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="ebfdf-461">另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-461">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="ebfdf-462">用 WithWebHostBuilder 自定义客户端</span><span class="sxs-lookup"><span data-stu-id="ebfdf-462">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="ebfdf-463">如果在测试方法中需要其他配置， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)会创建一个新的 `WebApplicationFactory`，其中包含由配置进一步自定义的[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-463">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="ebfdf-464">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的 @no__t 0 测试方法演示了 `WithWebHostBuilder` 的使用。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-464">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="ebfdf-465">此测试通过在 SUT 中触发窗体提交来在数据库中执行记录删除。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-465">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="ebfdf-466">因为 `IndexPageTests` 类中的另一个测试会执行一个操作，该操作将删除数据库中的所有记录，并且该操作可能在 @no__t 1 方法之前运行，因此，该数据库在此测试方法中种子，以确保存在记录，以便将其删除。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-466">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="ebfdf-467">选择 SUT 中 `messages` 窗体的第一个 "删除" 按钮时，会将请求中的内容模拟到 SUT：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-467">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="ebfdf-468">客户端选项</span><span class="sxs-lookup"><span data-stu-id="ebfdf-468">Client options</span></span>

<span data-ttu-id="ebfdf-469">下表显示了创建 `HttpClient` 实例时可用的默认[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-469">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="ebfdf-470">选项</span><span class="sxs-lookup"><span data-stu-id="ebfdf-470">Option</span></span> | <span data-ttu-id="ebfdf-471">描述</span><span class="sxs-lookup"><span data-stu-id="ebfdf-471">Description</span></span> | <span data-ttu-id="ebfdf-472">默认</span><span class="sxs-lookup"><span data-stu-id="ebfdf-472">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="ebfdf-473">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="ebfdf-473">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="ebfdf-474">获取或设置 @no__t 实例是否应自动跟随重定向响应。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-474">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="ebfdf-475">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="ebfdf-475">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="ebfdf-476">获取或设置 @no__t 实例的基址。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-476">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="ebfdf-477">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="ebfdf-477">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="ebfdf-478">获取或设置 @no__t 实例是否应处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-478">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="ebfdf-479">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="ebfdf-479">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="ebfdf-480">获取或设置 @no__t 实例应遵循的重定向响应的最大数目。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-480">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="ebfdf-481">7</span><span class="sxs-lookup"><span data-stu-id="ebfdf-481">7</span></span> |

<span data-ttu-id="ebfdf-482">创建`WebApplicationFactoryClientOptions`类并将其传递给 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 方法 (在代码示例中显示默认值):</span><span class="sxs-lookup"><span data-stu-id="ebfdf-482">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="ebfdf-483">注入模拟服务</span><span class="sxs-lookup"><span data-stu-id="ebfdf-483">Inject mock services</span></span>

<span data-ttu-id="ebfdf-484">通过在主机生成器上调用[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，可以在测试中覆盖服务。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-484">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="ebfdf-485">**若要注入 mock 服务，SUT 必须具有具有 @no__t 2 方法的 @no__t 1 类。**</span><span class="sxs-lookup"><span data-stu-id="ebfdf-485">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="ebfdf-486">示例 SUT 包含一个返回 quote 的作用域服务。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-486">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="ebfdf-487">请求索引页时，引号嵌入到索引页上的隐藏字段中。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-487">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="ebfdf-488">*服务/IQuoteService*：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-488">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="ebfdf-489">*服务/QuoteService*：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-489">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="ebfdf-490">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-490">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="ebfdf-491">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-491">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="ebfdf-492">*Pages/Index. cs*：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-492">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="ebfdf-493">运行 SUT 应用时，将生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-493">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="ebfdf-494">若要在集成测试中测试服务和引号注入，测试会将模拟服务注入到 SUT。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-494">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="ebfdf-495">模拟服务会将应用的 @no__t 0 替换为测试应用提供的服务，称为 `TestQuoteService`：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-495">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="ebfdf-496">*IntegrationTests.IndexPageTests.cs*：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-496">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="ebfdf-497">调用 `ConfigureTestServices`，并注册作用域内服务：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-497">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="ebfdf-498">在测试执行过程中生成的标记反映 @no__t 提供的引号文本，因此断言通过：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-498">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="ebfdf-499">测试基础结构如何推断应用内容根路径</span><span class="sxs-lookup"><span data-stu-id="ebfdf-499">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="ebfdf-500">@No__t-0 构造函数通过搜索包含集成测试的程序集上的[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)来推断应用[内容根](xref:fundamentals/index#content-root)路径，该键等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName`。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-500">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="ebfdf-501">如果找不到具有正确键的属性，`WebApplicationFactory` 将回退到搜索解决方案文件（ *.sln*），并在解决方案目录中追加 @no__t 2 程序集名称。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-501">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="ebfdf-502">应用根目录（内容根路径）用于发现视图和内容文件。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-502">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="ebfdf-503">禁用卷影复制</span><span class="sxs-lookup"><span data-stu-id="ebfdf-503">Disable shadow copying</span></span>

<span data-ttu-id="ebfdf-504">卷影复制会导致在输出目录以外的目录中执行测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-504">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="ebfdf-505">要使测试正常工作，必须禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-505">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="ebfdf-506">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的*xUnit*文件来禁用 xUnit 的卷影复制。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-506">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="ebfdf-507">有关详细信息，请参阅[配置 xUnit 与 JSON](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-507">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="ebfdf-508">将*xunit*文件添加到测试项目的根，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-508">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="ebfdf-509">如果使用的是 Visual Studio，请将文件的 "**复制到输出目录**" 属性设置为 "**始终复制**"。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-509">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="ebfdf-510">如果不使用 Visual Studio，请将 `Content` 目标添加到测试应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-510">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="ebfdf-511">对象的处理</span><span class="sxs-lookup"><span data-stu-id="ebfdf-511">Disposal of objects</span></span>

<span data-ttu-id="ebfdf-512">执行 `IClassFixture` 实现的测试后，当 xUnit 释放[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)时，将释放[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-512">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="ebfdf-513">如果开发人员实例化的对象需要处置，请在 `IClassFixture` 实现中释放这些对象。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-513">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="ebfdf-514">有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-514">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="ebfdf-515">集成测试示例</span><span class="sxs-lookup"><span data-stu-id="ebfdf-515">Integration tests sample</span></span>

<span data-ttu-id="ebfdf-516">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由两个应用组成：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-516">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="ebfdf-517">应用</span><span class="sxs-lookup"><span data-stu-id="ebfdf-517">App</span></span> | <span data-ttu-id="ebfdf-518">项目目录</span><span class="sxs-lookup"><span data-stu-id="ebfdf-518">Project directory</span></span> | <span data-ttu-id="ebfdf-519">描述</span><span class="sxs-lookup"><span data-stu-id="ebfdf-519">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="ebfdf-520">消息应用（SUT）</span><span class="sxs-lookup"><span data-stu-id="ebfdf-520">Message app (the SUT)</span></span> | <span data-ttu-id="ebfdf-521">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-521">*src/RazorPagesProject*</span></span> | <span data-ttu-id="ebfdf-522">允许用户添加、删除一个、删除和分析消息。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-522">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="ebfdf-523">测试应用</span><span class="sxs-lookup"><span data-stu-id="ebfdf-523">Test app</span></span> | <span data-ttu-id="ebfdf-524">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-524">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="ebfdf-525">用于集成测试 SUT。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-525">Used to integration test the SUT.</span></span> |

<span data-ttu-id="ebfdf-526">可以使用 IDE （如[Visual Studio](https://visualstudio.microsoft.com)）的内置测试功能来运行测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-526">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="ebfdf-527">如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在*RazorPagesProject 目录中的命令*提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-527">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="ebfdf-528">消息应用（SUT）组织</span><span class="sxs-lookup"><span data-stu-id="ebfdf-528">Message app (SUT) organization</span></span>

<span data-ttu-id="ebfdf-529">SUT 是 Razor Pages 的消息系统，具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-529">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="ebfdf-530">应用的 "索引" 页（*Pages/索引. cshtml*和*pages/index*）提供了一个 UI 和页面模型方法，用于控制消息的添加、删除和分析（每条消息的平均单词数）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-530">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="ebfdf-531">消息由具有两个属性的 @no__t 0 类（*Data/message*）描述： `Id` （键）和 `Text` （message）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-531">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="ebfdf-532">@No__t-0 属性是必需的，并且限制为200个字符。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-532">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="ebfdf-533">使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-533">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="ebfdf-534">应用在其数据库上下文 @no__t 类（*AppDbContext*）中包含数据访问层（DAL）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-534">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="ebfdf-535">如果数据库在应用启动时为空，则会用三条消息初始化消息存储。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-535">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="ebfdf-536">应用包括只能由经过身份验证的用户访问的 @no__t 0。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-536">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="ebfdf-537">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-537">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="ebfdf-538">本主题使用[xUnit](https://xunit.github.io/)测试框架。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-538">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="ebfdf-539">不同测试框架中的测试概念和测试实现相似，但并不完全相同。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-539">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="ebfdf-540">尽管应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-540">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="ebfdf-541">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-541">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="ebfdf-542">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="ebfdf-542">Test app organization</span></span>

<span data-ttu-id="ebfdf-543">测试应用是 "*测试"/"RazorPagesProject* " 目录中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-543">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="ebfdf-544">测试应用程序目录</span><span class="sxs-lookup"><span data-stu-id="ebfdf-544">Test app directory</span></span> | <span data-ttu-id="ebfdf-545">描述</span><span class="sxs-lookup"><span data-stu-id="ebfdf-545">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="ebfdf-546">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-546">*BasicTests*</span></span> | <span data-ttu-id="ebfdf-547">*BasicTests.cs*包含用于路由的测试方法、通过未经身份验证的用户访问安全页以及获取 GitHub 用户配置文件和检查配置文件的用户登录名。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-547">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="ebfdf-548">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-548">*IntegrationTests*</span></span> | <span data-ttu-id="ebfdf-549">*IndexPageTests.cs*包含使用自定义 `WebApplicationFactory` 类的索引页的集成测试。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-549">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="ebfdf-550">*帮助程序/实用工具*</span><span class="sxs-lookup"><span data-stu-id="ebfdf-550">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="ebfdf-551">*Utilities.cs*包含用于使数据库具有测试数据的 @no__t 1 方法。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-551">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="ebfdf-552">*HtmlHelpers.cs*提供了一个方法，用于返回 AngleSharp `IHtmlDocument` 以供测试方法使用。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-552">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="ebfdf-553">*HttpClientExtensions.cs*提供 `SendAsync` 的重载，以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-553">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="ebfdf-554">测试框架为[xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-554">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="ebfdf-555">集成测试是使用 TestHost 的[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)进行的，其中包括[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-555">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="ebfdf-556">由于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包用于配置测试主机和测试服务器，因此，在测试应用程序的项目文件或测试的开发人员配置中，`TestHost` 和 `TestServer` 包不需要直接包引用应用.</span><span class="sxs-lookup"><span data-stu-id="ebfdf-556">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="ebfdf-557">**播种要测试的数据库**</span><span class="sxs-lookup"><span data-stu-id="ebfdf-557">**Seeding the database for testing**</span></span>

<span data-ttu-id="ebfdf-558">集成测试在执行测试前通常需要数据库中的一个小型数据集。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-558">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="ebfdf-559">例如，删除测试调用数据库记录，因此数据库必须至少有一条记录，删除请求才能成功。</span><span class="sxs-lookup"><span data-stu-id="ebfdf-559">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="ebfdf-560">该示例应用在*Utilities.cs*中使用三个消息对数据库进行种子设定，当测试执行时，可以使用这些消息：</span><span class="sxs-lookup"><span data-stu-id="ebfdf-560">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ebfdf-561">其他资源</span><span class="sxs-lookup"><span data-stu-id="ebfdf-561">Additional resources</span></span>

* [<span data-ttu-id="ebfdf-562">单元测试</span><span class="sxs-lookup"><span data-stu-id="ebfdf-562">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="ebfdf-563">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="ebfdf-563">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)
* [<span data-ttu-id="ebfdf-564">中间件</span><span class="sxs-lookup"><span data-stu-id="ebfdf-564">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="ebfdf-565">测试控制器</span><span class="sxs-lookup"><span data-stu-id="ebfdf-565">Test controllers</span></span>](xref:mvc/controllers/testing)
