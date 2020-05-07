---
title: ASP.NET Core 中的集成测试
author: rick-anderson
description: 了解集成测试如何在基础结构级别（包括数据库、文件系统和网络）确保应用组件功能正常。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: test/integration-tests
ms.openlocfilehash: a01d2881133f39c1a26e4724ae25336c54746bc5
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766389"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="4e28a-103">ASP.NET Core 中的集成测试</span><span class="sxs-lookup"><span data-stu-id="4e28a-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="4e28a-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn)[Steve Smith](https://ardalis.com/) 和 [Jos van der Til](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="4e28a-104">By [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="4e28a-105">集成测试可在包含应用支持基础结构（如数据库、文件系统和网络）的级别上确保应用组件功能正常。</span><span class="sxs-lookup"><span data-stu-id="4e28a-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="4e28a-106">ASP.NET Core 通过将单元测试框架与测试 Web 主机和内存中测试服务器结合使用来支持集成测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="4e28a-107">本主题假设读者基本了解单元测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="4e28a-108">如果不熟悉测试概念，请参阅 [.NET Core 和 .NET Standard 中的单元测试](/dotnet/core/testing/)主题及其链接内容。</span><span class="sxs-lookup"><span data-stu-id="4e28a-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="4e28a-109">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4e28a-109">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4e28a-110">示例应用是 Razor Pages 应用，假设读者基本了解 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="4e28a-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="4e28a-111">如果不熟悉 Razor Pages，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="4e28a-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="4e28a-112">Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="4e28a-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="4e28a-113">Razor 页面入门</span><span class="sxs-lookup"><span data-stu-id="4e28a-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="4e28a-114">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="4e28a-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="4e28a-115">对于测试 SPA，建议使用可以自动执行浏览器的工具，如 [Selenium](https://www.seleniumhq.org/)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="4e28a-116">集成测试简介</span><span class="sxs-lookup"><span data-stu-id="4e28a-116">Introduction to integration tests</span></span>

<span data-ttu-id="4e28a-117">与[单元测试](/dotnet/core/testing/)相比，集成测试可在更广泛的级别上评估应用的组件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="4e28a-118">单元测试用于测试独立软件组件，如单独的类方法。</span><span class="sxs-lookup"><span data-stu-id="4e28a-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="4e28a-119">集成测试确认两个或更多应用组件一起工作以生成预期结果，可能包括完整处理请求所需的每个组件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="4e28a-120">这些更广泛的测试用于测试应用的基础结构和整个框架，通常包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="4e28a-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="4e28a-121">数据库</span><span class="sxs-lookup"><span data-stu-id="4e28a-121">Database</span></span>
* <span data-ttu-id="4e28a-122">文件系统</span><span class="sxs-lookup"><span data-stu-id="4e28a-122">File system</span></span>
* <span data-ttu-id="4e28a-123">网络设备</span><span class="sxs-lookup"><span data-stu-id="4e28a-123">Network appliances</span></span>
* <span data-ttu-id="4e28a-124">请求-响应管道</span><span class="sxs-lookup"><span data-stu-id="4e28a-124">Request-response pipeline</span></span>

<span data-ttu-id="4e28a-125">单元测试使用称为 fake  或 mock 对象  的制造组件，而不是基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="4e28a-126">与单元测试相比，集成测试：</span><span class="sxs-lookup"><span data-stu-id="4e28a-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="4e28a-127">使用应用在生产环境中使用的实际组件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="4e28a-128">需要进行更多代码和数据处理。</span><span class="sxs-lookup"><span data-stu-id="4e28a-128">Require more code and data processing.</span></span>
* <span data-ttu-id="4e28a-129">需要更长时间来运行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-129">Take longer to run.</span></span>

<span data-ttu-id="4e28a-130">因此，将集成测试的使用限制为最重要的基础结构方案。</span><span class="sxs-lookup"><span data-stu-id="4e28a-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="4e28a-131">如果可以使用单元测试或集成测试来测试行为，请选择单元测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="4e28a-132">请勿为通过数据库和文件系统进行的数据和文件访问的每个可能排列编写集成测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="4e28a-133">无论应用中有多少位置与数据库和文件系统交互，一组集中式读取、写入、更新和删除集成测试通常能够充分测试数据库和文件系统组件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="4e28a-134">将单元测试用于与这些组件交互的方法逻辑的例程测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="4e28a-135">在单元测试中，使用基础结构 fake/mock 会导致更快地执行测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="4e28a-136">在集成测试的讨论中，测试的项目经常称为“测试中的系统”  ，简称“SUT”。</span><span class="sxs-lookup"><span data-stu-id="4e28a-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="4e28a-137">本主题中使用“SUT”来指代测试的 ASP.NET Core 应用。 </span><span class="sxs-lookup"><span data-stu-id="4e28a-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="4e28a-138">ASP.NET Core 集成测试</span><span class="sxs-lookup"><span data-stu-id="4e28a-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="4e28a-139">ASP.NET Core 中的集成测试需要以下内容：</span><span class="sxs-lookup"><span data-stu-id="4e28a-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="4e28a-140">测试项目用于包含和执行测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="4e28a-141">测试项目具有对 SUT 的引用。</span><span class="sxs-lookup"><span data-stu-id="4e28a-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="4e28a-142">测试项目为 SUT 创建测试 Web 主机，并使用测试服务器客户端处理 SUT 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="4e28a-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="4e28a-143">测试运行程序用于执行测试并报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="4e28a-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="4e28a-144">集成测试后跟一系列事件，包括常规“排列”  、“操作”  和“断言”  测试步骤：</span><span class="sxs-lookup"><span data-stu-id="4e28a-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="4e28a-145">已配置 SUT 的 Web 主机。</span><span class="sxs-lookup"><span data-stu-id="4e28a-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="4e28a-146">创建测试服务器客户端以向应用提交请求。</span><span class="sxs-lookup"><span data-stu-id="4e28a-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="4e28a-147">执行“排列”  测试步骤：测试应用会准备请求。</span><span class="sxs-lookup"><span data-stu-id="4e28a-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="4e28a-148">执行“操作”  测试步骤：客户端提交请求并接收响应。</span><span class="sxs-lookup"><span data-stu-id="4e28a-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="4e28a-149">执行“断言”  测试步骤：实际  响应基于预期  响应验证为通过  或失败  。</span><span class="sxs-lookup"><span data-stu-id="4e28a-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="4e28a-150">该过程会一直继续，直到执行了所有测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="4e28a-151">报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="4e28a-151">The test results are reported.</span></span>

<span data-ttu-id="4e28a-152">通常，测试 Web 主机的配置与用于测试运行的应用常规 Web 主机不同。</span><span class="sxs-lookup"><span data-stu-id="4e28a-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="4e28a-153">例如，可以将不同的数据库或不同的应用设置用于测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="4e28a-154">基础结构组件（如测试 Web 主机和内存中测试服务器 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))）由 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 包提供或管理。</span><span class="sxs-lookup"><span data-stu-id="4e28a-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="4e28a-155">使用此包可简化测试创建和执行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="4e28a-156">`Microsoft.AspNetCore.Mvc.Testing` 包处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="4e28a-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="4e28a-157">将依赖项文件 (.deps  ) 从 SUT 复制到测试项目的 bin  目录中。</span><span class="sxs-lookup"><span data-stu-id="4e28a-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="4e28a-158">将[内容根目录](xref:fundamentals/index#content-root)设置为 SUT 的项目根目录，以便可在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="4e28a-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="4e28a-159">提供 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 类，以简化 SUT 在 `TestServer` 中的启动过程。</span><span class="sxs-lookup"><span data-stu-id="4e28a-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="4e28a-160">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍如何设置测试项目和测试运行程序，以及有关如何运行测试的详细说明与有关如何命名测试和测试类的建议。</span><span class="sxs-lookup"><span data-stu-id="4e28a-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="4e28a-161">为应用创建测试项目时，请将集成测试中的单元测试分隔到不同的项目中。</span><span class="sxs-lookup"><span data-stu-id="4e28a-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="4e28a-162">这可帮助确保不会意外地将基础结构测试组件包含在单元测试中。</span><span class="sxs-lookup"><span data-stu-id="4e28a-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="4e28a-163">通过分隔单元测试和集成测试还可以控制运行的测试集。</span><span class="sxs-lookup"><span data-stu-id="4e28a-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="4e28a-164">Razor Pages 应用与 MVC 应用的测试配置之间几乎没有任何区别。</span><span class="sxs-lookup"><span data-stu-id="4e28a-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="4e28a-165">唯一的区别在于测试的命名方式。</span><span class="sxs-lookup"><span data-stu-id="4e28a-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="4e28a-166">在 Razor Pages 应用中，页面终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 用于为索引页面测试组件集成）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="4e28a-167">在 MVC 应用中，测试通常按控制器类进行组织，并以它们所测试的控制器来命令（例如 `HomeControllerTests` 用于为主页控制器测试组件集成）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="4e28a-168">测试应用先决条件</span><span class="sxs-lookup"><span data-stu-id="4e28a-168">Test app prerequisites</span></span>

<span data-ttu-id="4e28a-169">测试项目必须：</span><span class="sxs-lookup"><span data-stu-id="4e28a-169">The test project must:</span></span>

* <span data-ttu-id="4e28a-170">引用 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包。</span><span class="sxs-lookup"><span data-stu-id="4e28a-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="4e28a-171">在项目文件中指定 Web SDK (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="4e28a-172">可以在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-172">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="4e28a-173">检查 tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj  文件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="4e28a-174">示例应用使用 [xUnit](https://xunit.github.io/) 测试框架和 [AngleSharp](https://anglesharp.github.io/) 分析程序库，因此示例应用还引用：</span><span class="sxs-lookup"><span data-stu-id="4e28a-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="4e28a-175">xunit</span><span class="sxs-lookup"><span data-stu-id="4e28a-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="4e28a-176">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="4e28a-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="4e28a-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="4e28a-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="4e28a-178">还在测试中使用了 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="4e28a-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="4e28a-179">应用引用：</span><span class="sxs-lookup"><span data-stu-id="4e28a-179">The app references:</span></span>

* [<span data-ttu-id="4e28a-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="4e28a-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [<span data-ttu-id="4e28a-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="4e28a-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [<span data-ttu-id="4e28a-182">Microsoft.EntityFrameworkCore</span><span class="sxs-lookup"><span data-stu-id="4e28a-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="4e28a-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="4e28a-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="4e28a-184">Microsoft.EntityFrameworkCore.Tools</span><span class="sxs-lookup"><span data-stu-id="4e28a-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="4e28a-185">SUT 环境</span><span class="sxs-lookup"><span data-stu-id="4e28a-185">SUT environment</span></span>

<span data-ttu-id="4e28a-186">如果未设置 SUT 的[环境](xref:fundamentals/environments)，则环境会默认为“开发”。</span><span class="sxs-lookup"><span data-stu-id="4e28a-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="4e28a-187">使用默认 WebApplicationFactory 的基本测试</span><span class="sxs-lookup"><span data-stu-id="4e28a-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="4e28a-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 用于为集成测试创建 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="4e28a-189">`TEntryPoint` 是 SUT 的入口点类，通常是 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="4e28a-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="4e28a-190">测试类实现一个类固定例程  接口 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))，以指示类包含测试，并在类中的所有测试间提供共享对象实例。</span><span class="sxs-lookup"><span data-stu-id="4e28a-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="4e28a-191">以下测试类 `BasicTests` 使用 `WebApplicationFactory` 启动 SUT，并向测试方法 `Get_EndpointsReturnSuccessAndCorrectContentType` 提供 [HttpClient](/dotnet/api/system.net.http.httpclient)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-191">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="4e28a-192">该方法检查响应状态代码是否成功（处于范围 200-299 中的状态代码），以及 `Content-Type` 标头是否为适用于多个应用页面的 `text/html; charset=utf-8`。</span><span class="sxs-lookup"><span data-stu-id="4e28a-192">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="4e28a-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 创建自动追随重定向并处理 cookie 的 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="4e28a-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="4e28a-194">默认情况下，在启用了 [GDPR 同意策略](xref:security/gdpr)时，不会在请求间保留非必要 cookie。</span><span class="sxs-lookup"><span data-stu-id="4e28a-194">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="4e28a-195">若要保留非必要 cookie（如 TempData 提供程序使用的 cookie），请在测试中将它们标记为必要。</span><span class="sxs-lookup"><span data-stu-id="4e28a-195">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="4e28a-196">有关将 cookie 标记为必要的说明，请参阅[必要 cookie](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-196">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="4e28a-197">自定义 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="4e28a-197">Customize WebApplicationFactory</span></span>

<span data-ttu-id="4e28a-198">通过从 `WebApplicationFactory` 来创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：</span><span class="sxs-lookup"><span data-stu-id="4e28a-198">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="4e28a-199">从 `WebApplicationFactory` 继承并替代 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-199">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="4e28a-200">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 允许使用 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) 配置服务集合：</span><span class="sxs-lookup"><span data-stu-id="4e28a-200">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="4e28a-201">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-201">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="4e28a-202">[集成测试示例：测试应用组织](#test-app-organization)部分中介绍了该方法。</span><span class="sxs-lookup"><span data-stu-id="4e28a-202">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="4e28a-203">SUT 的数据库上下文在其 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="4e28a-203">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4e28a-204">测试应用的 `builder.ConfigureServices` 回调在执行应用的 `Startup.ConfigureServices` 代码之后  执行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-204">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="4e28a-205">随着 ASP.NET Core 3.0 的发布，执行顺序是针对[泛型主机](xref:fundamentals/host/generic-host)的一个重大更改。</span><span class="sxs-lookup"><span data-stu-id="4e28a-205">The execution order is a breaking change for the [Generic Host](xref:fundamentals/host/generic-host) with the release of ASP.NET Core 3.0.</span></span> <span data-ttu-id="4e28a-206">若要将与应用数据库不同的数据库用于测试，必须在 `builder.ConfigureServices` 中替换应用的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="4e28a-206">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="4e28a-207">示例应用会查找数据库上下文的服务描述符，并使用该描述符删除服务注册。</span><span class="sxs-lookup"><span data-stu-id="4e28a-207">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="4e28a-208">接下来，工厂会添加一个新 `ApplicationDbContext`，它使用内存中数据库进行测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-208">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="4e28a-209">若要连接到与内存中数据库不同的数据库，请更改 `UseInMemoryDatabase` 调用以将上下文连接到不同数据库。</span><span class="sxs-lookup"><span data-stu-id="4e28a-209">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="4e28a-210">使用 SQL Server 测试数据库：</span><span class="sxs-lookup"><span data-stu-id="4e28a-210">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="4e28a-211">在项目文件中引用 [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="4e28a-211">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="4e28a-212">使用连接到数据库的连接字符串调用 `UseSqlServer`。</span><span class="sxs-lookup"><span data-stu-id="4e28a-212">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="4e28a-213">在测试类中使用自定义 `CustomWebApplicationFactory`。</span><span class="sxs-lookup"><span data-stu-id="4e28a-213">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="4e28a-214">下面的示例使用 `IndexPageTests` 类中的工厂：</span><span class="sxs-lookup"><span data-stu-id="4e28a-214">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="4e28a-215">示例应用的客户端配置为阻止 `HttpClient` 追随重定向。</span><span class="sxs-lookup"><span data-stu-id="4e28a-215">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="4e28a-216">如稍后在[模拟身份验证](#mock-authentication)部分中所述，这允许测试检查应用第一个响应的结果。</span><span class="sxs-lookup"><span data-stu-id="4e28a-216">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="4e28a-217">第一个响应是在许多具有 `Location` 标头的测试中进行重定向。</span><span class="sxs-lookup"><span data-stu-id="4e28a-217">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="4e28a-218">典型测试使用 `HttpClient` 和帮助程序方法处理请求和响应：</span><span class="sxs-lookup"><span data-stu-id="4e28a-218">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="4e28a-219">对 SUT 发出的任何 POST 请求都必须满足防伪检查，该检查由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动执行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-219">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="4e28a-220">若要安排测试的 POST 请求，测试应用必须：</span><span class="sxs-lookup"><span data-stu-id="4e28a-220">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="4e28a-221">对页面发出请求。</span><span class="sxs-lookup"><span data-stu-id="4e28a-221">Make a request for the page.</span></span>
1. <span data-ttu-id="4e28a-222">分析来自响应的防伪 cookie 和请求验证令牌。</span><span class="sxs-lookup"><span data-stu-id="4e28a-222">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="4e28a-223">发出放置了防伪 cookie 和请求验证令牌的 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="4e28a-223">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="4e28a-224">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 `SendAsync` 帮助程序扩展方法 (Helpers/HttpClientExtensions.cs  ) 和 `GetDocumentAsync` 帮助程序方法 (Helpers/HtmlHelpers.cs  ) 使用 [AngleSharp](https://anglesharp.github.io/) 分析程序，通过以下方法处理防伪检查：</span><span class="sxs-lookup"><span data-stu-id="4e28a-224">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="4e28a-225">`GetDocumentAsync` &ndash; 接收 [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 并返回 `IHtmlDocument`。</span><span class="sxs-lookup"><span data-stu-id="4e28a-225">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="4e28a-226">`GetDocumentAsync` 使用一个基于原始 `HttpResponseMessage` 准备虚拟响应  的工厂。</span><span class="sxs-lookup"><span data-stu-id="4e28a-226">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="4e28a-227">有关详细信息，请参阅 [AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-227">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="4e28a-228">`HttpClient` 的 `SendAsync` 扩展方法撰写 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) 并调用 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)，以向 SUT 提交请求。</span><span class="sxs-lookup"><span data-stu-id="4e28a-228">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="4e28a-229">`SendAsync` 的重载接受 HTML 窗体 (`IHtmlFormElement`) 和以下内容：</span><span class="sxs-lookup"><span data-stu-id="4e28a-229">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="4e28a-230">窗体的“提交”按钮 (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="4e28a-230">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="4e28a-231">窗体值集合 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="4e28a-231">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="4e28a-232">提交按钮 (`IHtmlElement`) 和窗体值 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="4e28a-232">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="4e28a-233">[AngleSharp](https://anglesharp.github.io/) 是在本主题和示例应用中用于演示的第三方分析库。</span><span class="sxs-lookup"><span data-stu-id="4e28a-233">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="4e28a-234">ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="4e28a-234">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="4e28a-235">可以使用其他分析程序，如 [Html Agility Pack (HAP)](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-235">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="4e28a-236">另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。</span><span class="sxs-lookup"><span data-stu-id="4e28a-236">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="4e28a-237">使用 WithWebHostBuilder 自定义客户端</span><span class="sxs-lookup"><span data-stu-id="4e28a-237">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="4e28a-238">当测试方法中需要其他配置时，[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 可创建新 `WebApplicationFactory`，其中包含通过配置进一步自定义的 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-238">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="4e28a-239">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 的 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 测试方法演示了 `WithWebHostBuilder` 的使用。</span><span class="sxs-lookup"><span data-stu-id="4e28a-239">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="4e28a-240">此测试通过在 SUT 中触发窗体提交，在数据库中执行记录删除。</span><span class="sxs-lookup"><span data-stu-id="4e28a-240">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="4e28a-241">由于 `IndexPageTests` 类中的另一个测试会执行删除数据库中所有记录的操作，并且可能在 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 方法之前运行，因此数据库会在此测试方法中重新进行种子设定，以确保存在记录供 SUT 删除。</span><span class="sxs-lookup"><span data-stu-id="4e28a-241">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="4e28a-242">在 SUT 中选择 `messages` 窗体的第一个删除按钮可在向 SUT 发出的请求中进行模拟：</span><span class="sxs-lookup"><span data-stu-id="4e28a-242">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="4e28a-243">客户端选项</span><span class="sxs-lookup"><span data-stu-id="4e28a-243">Client options</span></span>

<span data-ttu-id="4e28a-244">下表显示在创建 `HttpClient` 实例时可用的默认 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-244">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="4e28a-245">选项</span><span class="sxs-lookup"><span data-stu-id="4e28a-245">Option</span></span> | <span data-ttu-id="4e28a-246">描述</span><span class="sxs-lookup"><span data-stu-id="4e28a-246">Description</span></span> | <span data-ttu-id="4e28a-247">默认</span><span class="sxs-lookup"><span data-stu-id="4e28a-247">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="4e28a-248">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="4e28a-248">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="4e28a-249">获取或设置 `HttpClient` 实例是否应自动追随重定向响应。</span><span class="sxs-lookup"><span data-stu-id="4e28a-249">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="4e28a-250">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="4e28a-250">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="4e28a-251">获取或设置 `HttpClient` 实例的基址。</span><span class="sxs-lookup"><span data-stu-id="4e28a-251">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="4e28a-252">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="4e28a-252">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="4e28a-253">获取或设置 `HttpClient` 实例是否应处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="4e28a-253">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="4e28a-254">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="4e28a-254">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="4e28a-255">获取或设置 `HttpClient` 实例应追随的重定向响应的最大数量。</span><span class="sxs-lookup"><span data-stu-id="4e28a-255">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="4e28a-256">7</span><span class="sxs-lookup"><span data-stu-id="4e28a-256">7</span></span> |

<span data-ttu-id="4e28a-257">创建 `WebApplicationFactoryClientOptions` 类并将它传递给 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 方法（默认值显示在代码示例中）：</span><span class="sxs-lookup"><span data-stu-id="4e28a-257">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="4e28a-258">注入模拟服务</span><span class="sxs-lookup"><span data-stu-id="4e28a-258">Inject mock services</span></span>

<span data-ttu-id="4e28a-259">可以通过在主机生成器上调用 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)，在测试中替代服务。</span><span class="sxs-lookup"><span data-stu-id="4e28a-259">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="4e28a-260">若要注入模拟服务，SUT 必须具有包含 `Startup.ConfigureServices` 方法的 `Startup` 类。 </span><span class="sxs-lookup"><span data-stu-id="4e28a-260">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="4e28a-261">示例 SUT 包含返回引用的作用域服务。</span><span class="sxs-lookup"><span data-stu-id="4e28a-261">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="4e28a-262">向索引页面进行请求时，引用嵌入在索引页面上的隐藏字段中。</span><span class="sxs-lookup"><span data-stu-id="4e28a-262">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="4e28a-263">Services/IQuoteService.cs  ：</span><span class="sxs-lookup"><span data-stu-id="4e28a-263">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="4e28a-264">Services/QuoteService.cs  ：</span><span class="sxs-lookup"><span data-stu-id="4e28a-264">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="4e28a-265">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="4e28a-265">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="4e28a-266"> Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="4e28a-266">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="4e28a-267">Pages/Index.cs  ：</span><span class="sxs-lookup"><span data-stu-id="4e28a-267">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="4e28a-268">运行 SUT 应用时，会生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="4e28a-268">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="4e28a-269">若要在集成测试中测试服务和引用注入，测试会将模拟服务注入到 SUT 中。</span><span class="sxs-lookup"><span data-stu-id="4e28a-269">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="4e28a-270">模拟服务会将应用的 `QuoteService` 替换为测试应用提供的服务，称为 `TestQuoteService`：</span><span class="sxs-lookup"><span data-stu-id="4e28a-270">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="4e28a-271">IntegrationTests.IndexPageTests.cs  ：</span><span class="sxs-lookup"><span data-stu-id="4e28a-271">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="4e28a-272">调用 `ConfigureTestServices`，并注册作用域服务：</span><span class="sxs-lookup"><span data-stu-id="4e28a-272">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="4e28a-273">在测试执行过程中生成的标记反映由 `TestQuoteService` 提供的引用文本，因而断言通过：</span><span class="sxs-lookup"><span data-stu-id="4e28a-273">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="4e28a-274">模拟身份验证</span><span class="sxs-lookup"><span data-stu-id="4e28a-274">Mock authentication</span></span>

<span data-ttu-id="4e28a-275">`AuthTests` 类中的测试检查安全终结点是否：</span><span class="sxs-lookup"><span data-stu-id="4e28a-275">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="4e28a-276">将未经身份验证的用户重定向到应用的登录页面。</span><span class="sxs-lookup"><span data-stu-id="4e28a-276">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="4e28a-277">为经过身份验证的用户返回内容。</span><span class="sxs-lookup"><span data-stu-id="4e28a-277">Returns content for an authenticated user.</span></span>

<span data-ttu-id="4e28a-278">在 SUT 中，`/SecurePage` 页面使用 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 约定将 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 应用于页面。</span><span class="sxs-lookup"><span data-stu-id="4e28a-278">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="4e28a-279">有关详细信息，请参阅 [Razor Pages 约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-279">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="4e28a-280">在 `Get_SecurePageRedirectsAnUnauthenticatedUser` 测试中，[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 设置为禁止重定向，具体方法是将 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="4e28a-280">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="4e28a-281">通过禁止客户端追随重定向，可以执行以下检查：</span><span class="sxs-lookup"><span data-stu-id="4e28a-281">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="4e28a-282">可以根据预期 [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) 结果检查 SUT 返回的状态代码，而不是在重定向到登录页面之后的最终状态代码（这会是 [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-282">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="4e28a-283">检查响应标头中的 `Location` 标头值，以确认它以 `http://localhost/Identity/Account/Login` 开头，而不是最终登录页面响应（其中 `Location` 标头不存在）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-283">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="4e28a-284">测试应用可以在 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 中模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>，以便测试身份验证和授权的各个方面。</span><span class="sxs-lookup"><span data-stu-id="4e28a-284">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="4e28a-285">最小方案返回一个 [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：</span><span class="sxs-lookup"><span data-stu-id="4e28a-285">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="4e28a-286">当身份验证方案设置为 `Test`（其中为 `ConfigureTestServices` 注册了 `AddAuthentication`）时，会调用 `TestAuthHandler` 以对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="4e28a-286">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="4e28a-287">有关 `WebApplicationFactoryClientOptions` 的详细信息，请参阅[客户端选项](#client-options)部分。</span><span class="sxs-lookup"><span data-stu-id="4e28a-287">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="4e28a-288">设置环境</span><span class="sxs-lookup"><span data-stu-id="4e28a-288">Set the environment</span></span>

<span data-ttu-id="4e28a-289">默认情况下，SUT 的主机和应用环境配置为使用开发环境。</span><span class="sxs-lookup"><span data-stu-id="4e28a-289">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="4e28a-290">替代 SUT 的环境：</span><span class="sxs-lookup"><span data-stu-id="4e28a-290">To override the SUT's environment:</span></span>

* <span data-ttu-id="4e28a-291">设置 `ASPNETCORE_ENVIRONMENT` 环境变量（例如，`Staging`、`Production` 或其他自定义值，例如 `Testing`）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-291">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="4e28a-292">在测试应用中替代 `CreateHostBuilder`，以读取以 `ASPNETCORE` 为前缀的环境变量。</span><span class="sxs-lookup"><span data-stu-id="4e28a-292">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="4e28a-293">测试基础结构如何推断应用内容根路径</span><span class="sxs-lookup"><span data-stu-id="4e28a-293">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="4e28a-294">`WebApplicationFactory` 构造函数通过在包含集成测试的程序集中搜索键等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName` 的 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)，来推断应用[内容根](xref:fundamentals/index#content-root)路径。</span><span class="sxs-lookup"><span data-stu-id="4e28a-294">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="4e28a-295">如果找不到具有正确键的属性，则 `WebApplicationFactory` 会回退到搜索解决方案文件 (.sln  ) 并将 `TEntryPoint` 程序集名称追加到解决方案目录。</span><span class="sxs-lookup"><span data-stu-id="4e28a-295">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="4e28a-296">应用根目录（内容根路径）用于发现视图和内容文件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-296">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="4e28a-297">禁用卷影复制</span><span class="sxs-lookup"><span data-stu-id="4e28a-297">Disable shadow copying</span></span>

<span data-ttu-id="4e28a-298">卷影复制会导致在与输出目录不同的目录中执行测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-298">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="4e28a-299">若要使测试正常工作，必须禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="4e28a-299">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="4e28a-300">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的 xunit.runner.json  文件来对 xUnit 禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="4e28a-300">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="4e28a-301">有关详细信息，请参阅[使用 JSON 配置 xUnit](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-301">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="4e28a-302">将包含以下内容的 xunit.runner.json  文件添加到测试项目的根：</span><span class="sxs-lookup"><span data-stu-id="4e28a-302">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="4e28a-303">对象的处置</span><span class="sxs-lookup"><span data-stu-id="4e28a-303">Disposal of objects</span></span>

<span data-ttu-id="4e28a-304">执行 `IClassFixture` 实现的测试之后，当 xUnit 处置 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 时，[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 和 [HttpClient](/dotnet/api/system.net.http.httpclient) 会进行处置。</span><span class="sxs-lookup"><span data-stu-id="4e28a-304">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="4e28a-305">如果开发者实例化的对象需要处置，请在 `IClassFixture` 实现中处置它们。</span><span class="sxs-lookup"><span data-stu-id="4e28a-305">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="4e28a-306">有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-306">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="4e28a-307">集成测试示例</span><span class="sxs-lookup"><span data-stu-id="4e28a-307">Integration tests sample</span></span>

<span data-ttu-id="4e28a-308">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)包含两个应用：</span><span class="sxs-lookup"><span data-stu-id="4e28a-308">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="4e28a-309">应用</span><span class="sxs-lookup"><span data-stu-id="4e28a-309">App</span></span> | <span data-ttu-id="4e28a-310">项目目录</span><span class="sxs-lookup"><span data-stu-id="4e28a-310">Project directory</span></span> | <span data-ttu-id="4e28a-311">描述</span><span class="sxs-lookup"><span data-stu-id="4e28a-311">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="4e28a-312">消息应用 (SUT)</span><span class="sxs-lookup"><span data-stu-id="4e28a-312">Message app (the SUT)</span></span> | <span data-ttu-id="4e28a-313">src/RazorPagesProject </span><span class="sxs-lookup"><span data-stu-id="4e28a-313">*src/RazorPagesProject*</span></span> | <span data-ttu-id="4e28a-314">允许用户添加消息、删除一个消息、删除所有消息和分析消息。</span><span class="sxs-lookup"><span data-stu-id="4e28a-314">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="4e28a-315">测试应用</span><span class="sxs-lookup"><span data-stu-id="4e28a-315">Test app</span></span> | <span data-ttu-id="4e28a-316">tests/RazorPagesProject.Tests </span><span class="sxs-lookup"><span data-stu-id="4e28a-316">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="4e28a-317">用于集成测试 SUT。</span><span class="sxs-lookup"><span data-stu-id="4e28a-317">Used to integration test the SUT.</span></span> |

<span data-ttu-id="4e28a-318">可使用 IDE 的内置测试功能（例如 [Visual Studio](https://visualstudio.microsoft.com)）运行测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-318">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="4e28a-319">如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令行，请在 tests/RazorPagesProject.Tests  目录中的命令提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4e28a-319">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="4e28a-320">消息应用 (SUT) 组织</span><span class="sxs-lookup"><span data-stu-id="4e28a-320">Message app (SUT) organization</span></span>

<span data-ttu-id="4e28a-321">SUT 是具有以下特征的 Razor Pages 消息系统：</span><span class="sxs-lookup"><span data-stu-id="4e28a-321">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="4e28a-322">应用的索引页面（Pages/Index.cshtml  和 Pages/Index.cshtml.cs  ）提供 UI 和页面模型方法，用于控制添加、删除和分析消息（每个消息的平均字词数）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-322">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="4e28a-323">消息由 `Message` 类 (Data/Message.cs  ) 描述，并具有两个属性：`Id`（键）和 `Text`（消息）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-323">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="4e28a-324">`Text` 属性是必需的，并限制为 200 个字符。</span><span class="sxs-lookup"><span data-stu-id="4e28a-324">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="4e28a-325">消息使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)存储。&#8224;</span><span class="sxs-lookup"><span data-stu-id="4e28a-325">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="4e28a-326">应用在其数据库上下文类 `AppDbContext` (Data/AppDbContext.cs  ) 中包含数据访问层 (DAL)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-326">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="4e28a-327">如果应用启动时数据库为空，则消息存储初始化为三条消息。</span><span class="sxs-lookup"><span data-stu-id="4e28a-327">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="4e28a-328">应用包含只能由经过身份验证的用户访问的 `/SecurePage`。</span><span class="sxs-lookup"><span data-stu-id="4e28a-328">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="4e28a-329">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明如何将内存中数据库用于 MSTest 测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-329">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="4e28a-330">本主题使用 [xUnit](https://xunit.github.io/) 测试框架。</span><span class="sxs-lookup"><span data-stu-id="4e28a-330">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="4e28a-331">不同测试框架中的测试概念和测试实现相似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="4e28a-331">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="4e28a-332">尽管应用未使用存储库模式且不是[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些开发模式。</span><span class="sxs-lookup"><span data-stu-id="4e28a-332">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="4e28a-333">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（该示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-333">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="4e28a-334">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="4e28a-334">Test app organization</span></span>

<span data-ttu-id="4e28a-335">测试应用是 tests/RazorPagesProject.Tests  目录中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="4e28a-335">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="4e28a-336">测试应用目录</span><span class="sxs-lookup"><span data-stu-id="4e28a-336">Test app directory</span></span> | <span data-ttu-id="4e28a-337">描述</span><span class="sxs-lookup"><span data-stu-id="4e28a-337">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="4e28a-338">AuthTests </span><span class="sxs-lookup"><span data-stu-id="4e28a-338">*AuthTests*</span></span> | <span data-ttu-id="4e28a-339">包含针对以下方面的测试方法：</span><span class="sxs-lookup"><span data-stu-id="4e28a-339">Contains test methods for:</span></span><ul><li><span data-ttu-id="4e28a-340">未经身份验证的用户访问安全页面。</span><span class="sxs-lookup"><span data-stu-id="4e28a-340">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="4e28a-341">经过身份验证的用户访问安全页面（通过模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-341">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="4e28a-342">获取 GitHub 用户配置文件，并检查配置文件的用户登录。</span><span class="sxs-lookup"><span data-stu-id="4e28a-342">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="4e28a-343">BasicTests </span><span class="sxs-lookup"><span data-stu-id="4e28a-343">*BasicTests*</span></span> | <span data-ttu-id="4e28a-344">包含用于路由和内容类型的测试方法。</span><span class="sxs-lookup"><span data-stu-id="4e28a-344">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="4e28a-345">IntegrationTests </span><span class="sxs-lookup"><span data-stu-id="4e28a-345">*IntegrationTests*</span></span> | <span data-ttu-id="4e28a-346">包含使用自定义 `WebApplicationFactory` 类的索引页面的集成测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-346">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="4e28a-347">Helpers/Utilities </span><span class="sxs-lookup"><span data-stu-id="4e28a-347">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="4e28a-348">Utilities.cs  包含用于通过测试数据设定数据库种子的 `InitializeDbForTests` 方法。</span><span class="sxs-lookup"><span data-stu-id="4e28a-348">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="4e28a-349">HtmlHelpers.cs  提供了一种方法，用于返回 AngleSharp `IHtmlDocument` 供测试方法使用。</span><span class="sxs-lookup"><span data-stu-id="4e28a-349">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="4e28a-350">HttpClientExtensions.cs  为 `SendAsync` 提供重载，以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="4e28a-350">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="4e28a-351">测试框架为 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-351">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="4e28a-352">集成测试使用 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)（包括 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)）执行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-352">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="4e28a-353">由于 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 包用于配置测试主机和测试服务器，因此 `TestHost` 和 `TestServer` 包在测试应用的项目文件或测试应用的开发者配置中不需要直接包引用。</span><span class="sxs-lookup"><span data-stu-id="4e28a-353">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="4e28a-354">设定数据库种子以进行测试 </span><span class="sxs-lookup"><span data-stu-id="4e28a-354">**Seeding the database for testing**</span></span>

<span data-ttu-id="4e28a-355">集成测试在执行测试前通常需要数据库中的小型数据集。</span><span class="sxs-lookup"><span data-stu-id="4e28a-355">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="4e28a-356">例如，删除测试需要进行数据库记录删除，因此数据库必须至少有一个记录，删除请求才能成功。</span><span class="sxs-lookup"><span data-stu-id="4e28a-356">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="4e28a-357">示例应用使用 Utilities.cs  中的三个消息（测试在执行时可以使用它们）设定数据库种子：</span><span class="sxs-lookup"><span data-stu-id="4e28a-357">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="4e28a-358">SUT 的数据库上下文在其 `Startup.ConfigureServices` 方法中注册。</span><span class="sxs-lookup"><span data-stu-id="4e28a-358">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4e28a-359">测试应用的 `builder.ConfigureServices` 回调在执行应用的 `Startup.ConfigureServices` 代码之后  执行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-359">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="4e28a-360">若要将不同的数据库用于测试，必须在 `builder.ConfigureServices` 中替换应用的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="4e28a-360">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="4e28a-361">有关详细信息，请参阅[自定义 WebApplicationFactory](#customize-webapplicationfactory) 部分。</span><span class="sxs-lookup"><span data-stu-id="4e28a-361">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4e28a-362">集成测试可在包含应用支持基础结构（如数据库、文件系统和网络）的级别上确保应用组件功能正常。</span><span class="sxs-lookup"><span data-stu-id="4e28a-362">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="4e28a-363">ASP.NET Core 通过将单元测试框架与测试 Web 主机和内存中测试服务器结合使用来支持集成测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-363">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="4e28a-364">本主题假设读者基本了解单元测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-364">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="4e28a-365">如果不熟悉测试概念，请参阅 [.NET Core 和 .NET Standard 中的单元测试](/dotnet/core/testing/)主题及其链接内容。</span><span class="sxs-lookup"><span data-stu-id="4e28a-365">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="4e28a-366">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="4e28a-366">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="4e28a-367">示例应用是 Razor Pages 应用，假设读者基本了解 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="4e28a-367">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="4e28a-368">如果不熟悉 Razor Pages，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="4e28a-368">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="4e28a-369">Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="4e28a-369">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="4e28a-370">Razor 页面入门</span><span class="sxs-lookup"><span data-stu-id="4e28a-370">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="4e28a-371">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="4e28a-371">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="4e28a-372">对于测试 SPA，建议使用可以自动执行浏览器的工具，如 [Selenium](https://www.seleniumhq.org/)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-372">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="4e28a-373">集成测试简介</span><span class="sxs-lookup"><span data-stu-id="4e28a-373">Introduction to integration tests</span></span>

<span data-ttu-id="4e28a-374">与[单元测试](/dotnet/core/testing/)相比，集成测试可在更广泛的级别上评估应用的组件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-374">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="4e28a-375">单元测试用于测试独立软件组件，如单独的类方法。</span><span class="sxs-lookup"><span data-stu-id="4e28a-375">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="4e28a-376">集成测试确认两个或更多应用组件一起工作以生成预期结果，可能包括完整处理请求所需的每个组件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-376">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="4e28a-377">这些更广泛的测试用于测试应用的基础结构和整个框架，通常包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="4e28a-377">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="4e28a-378">数据库</span><span class="sxs-lookup"><span data-stu-id="4e28a-378">Database</span></span>
* <span data-ttu-id="4e28a-379">文件系统</span><span class="sxs-lookup"><span data-stu-id="4e28a-379">File system</span></span>
* <span data-ttu-id="4e28a-380">网络设备</span><span class="sxs-lookup"><span data-stu-id="4e28a-380">Network appliances</span></span>
* <span data-ttu-id="4e28a-381">请求-响应管道</span><span class="sxs-lookup"><span data-stu-id="4e28a-381">Request-response pipeline</span></span>

<span data-ttu-id="4e28a-382">单元测试使用称为 fake  或 mock 对象  的制造组件，而不是基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-382">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="4e28a-383">与单元测试相比，集成测试：</span><span class="sxs-lookup"><span data-stu-id="4e28a-383">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="4e28a-384">使用应用在生产环境中使用的实际组件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-384">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="4e28a-385">需要进行更多代码和数据处理。</span><span class="sxs-lookup"><span data-stu-id="4e28a-385">Require more code and data processing.</span></span>
* <span data-ttu-id="4e28a-386">需要更长时间来运行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-386">Take longer to run.</span></span>

<span data-ttu-id="4e28a-387">因此，将集成测试的使用限制为最重要的基础结构方案。</span><span class="sxs-lookup"><span data-stu-id="4e28a-387">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="4e28a-388">如果可以使用单元测试或集成测试来测试行为，请选择单元测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-388">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="4e28a-389">请勿为通过数据库和文件系统进行的数据和文件访问的每个可能排列编写集成测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-389">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="4e28a-390">无论应用中有多少位置与数据库和文件系统交互，一组集中式读取、写入、更新和删除集成测试通常能够充分测试数据库和文件系统组件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-390">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="4e28a-391">将单元测试用于与这些组件交互的方法逻辑的例程测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-391">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="4e28a-392">在单元测试中，使用基础结构 fake/mock 会导致更快地执行测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-392">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="4e28a-393">在集成测试的讨论中，测试的项目经常称为“测试中的系统”  ，简称“SUT”。</span><span class="sxs-lookup"><span data-stu-id="4e28a-393">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="4e28a-394">本主题中使用“SUT”来指代测试的 ASP.NET Core 应用。 </span><span class="sxs-lookup"><span data-stu-id="4e28a-394">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="4e28a-395">ASP.NET Core 集成测试</span><span class="sxs-lookup"><span data-stu-id="4e28a-395">ASP.NET Core integration tests</span></span>

<span data-ttu-id="4e28a-396">ASP.NET Core 中的集成测试需要以下内容：</span><span class="sxs-lookup"><span data-stu-id="4e28a-396">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="4e28a-397">测试项目用于包含和执行测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-397">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="4e28a-398">测试项目具有对 SUT 的引用。</span><span class="sxs-lookup"><span data-stu-id="4e28a-398">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="4e28a-399">测试项目为 SUT 创建测试 Web 主机，并使用测试服务器客户端处理 SUT 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="4e28a-399">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="4e28a-400">测试运行程序用于执行测试并报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="4e28a-400">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="4e28a-401">集成测试后跟一系列事件，包括常规“排列”  、“操作”  和“断言”  测试步骤：</span><span class="sxs-lookup"><span data-stu-id="4e28a-401">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="4e28a-402">已配置 SUT 的 Web 主机。</span><span class="sxs-lookup"><span data-stu-id="4e28a-402">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="4e28a-403">创建测试服务器客户端以向应用提交请求。</span><span class="sxs-lookup"><span data-stu-id="4e28a-403">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="4e28a-404">执行“排列”  测试步骤：测试应用会准备请求。</span><span class="sxs-lookup"><span data-stu-id="4e28a-404">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="4e28a-405">执行“操作”  测试步骤：客户端提交请求并接收响应。</span><span class="sxs-lookup"><span data-stu-id="4e28a-405">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="4e28a-406">执行“断言”  测试步骤：实际  响应基于预期  响应验证为通过  或失败  。</span><span class="sxs-lookup"><span data-stu-id="4e28a-406">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="4e28a-407">该过程会一直继续，直到执行了所有测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-407">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="4e28a-408">报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="4e28a-408">The test results are reported.</span></span>

<span data-ttu-id="4e28a-409">通常，测试 Web 主机的配置与用于测试运行的应用常规 Web 主机不同。</span><span class="sxs-lookup"><span data-stu-id="4e28a-409">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="4e28a-410">例如，可以将不同的数据库或不同的应用设置用于测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-410">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="4e28a-411">基础结构组件（如测试 Web 主机和内存中测试服务器 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))）由 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 包提供或管理。</span><span class="sxs-lookup"><span data-stu-id="4e28a-411">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="4e28a-412">使用此包可简化测试创建和执行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-412">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="4e28a-413">`Microsoft.AspNetCore.Mvc.Testing` 包处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="4e28a-413">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="4e28a-414">将依赖项文件 (.deps  ) 从 SUT 复制到测试项目的 bin  目录中。</span><span class="sxs-lookup"><span data-stu-id="4e28a-414">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="4e28a-415">将[内容根目录](xref:fundamentals/index#content-root)设置为 SUT 的项目根目录，以便可在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="4e28a-415">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="4e28a-416">提供 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 类，以简化 SUT 在 `TestServer` 中的启动过程。</span><span class="sxs-lookup"><span data-stu-id="4e28a-416">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="4e28a-417">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍如何设置测试项目和测试运行程序，以及有关如何运行测试的详细说明与有关如何命名测试和测试类的建议。</span><span class="sxs-lookup"><span data-stu-id="4e28a-417">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="4e28a-418">为应用创建测试项目时，请将集成测试中的单元测试分隔到不同的项目中。</span><span class="sxs-lookup"><span data-stu-id="4e28a-418">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="4e28a-419">这可帮助确保不会意外地将基础结构测试组件包含在单元测试中。</span><span class="sxs-lookup"><span data-stu-id="4e28a-419">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="4e28a-420">通过分隔单元测试和集成测试还可以控制运行的测试集。</span><span class="sxs-lookup"><span data-stu-id="4e28a-420">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="4e28a-421">Razor Pages 应用与 MVC 应用的测试配置之间几乎没有任何区别。</span><span class="sxs-lookup"><span data-stu-id="4e28a-421">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="4e28a-422">唯一的区别在于测试的命名方式。</span><span class="sxs-lookup"><span data-stu-id="4e28a-422">The only difference is in how the tests are named.</span></span> <span data-ttu-id="4e28a-423">在 Razor Pages 应用中，页面终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 用于为索引页面测试组件集成）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-423">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="4e28a-424">在 MVC 应用中，测试通常按控制器类进行组织，并以它们所测试的控制器来命令（例如 `HomeControllerTests` 用于为主页控制器测试组件集成）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-424">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="4e28a-425">测试应用先决条件</span><span class="sxs-lookup"><span data-stu-id="4e28a-425">Test app prerequisites</span></span>

<span data-ttu-id="4e28a-426">测试项目必须：</span><span class="sxs-lookup"><span data-stu-id="4e28a-426">The test project must:</span></span>

* <span data-ttu-id="4e28a-427">引用以下包：</span><span class="sxs-lookup"><span data-stu-id="4e28a-427">Reference the following packages:</span></span>
  * [<span data-ttu-id="4e28a-428">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="4e28a-428">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="4e28a-429">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="4e28a-429">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="4e28a-430">在项目文件中指定 Web SDK (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-430">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="4e28a-431">引用 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)时需要 Web SDK。</span><span class="sxs-lookup"><span data-stu-id="4e28a-431">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="4e28a-432">可以在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-432">These prerequisites can be seen in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="4e28a-433">检查 tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj  文件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-433">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="4e28a-434">示例应用使用 [xUnit](https://xunit.github.io/) 测试框架和 [AngleSharp](https://anglesharp.github.io/) 分析程序库，因此示例应用还引用：</span><span class="sxs-lookup"><span data-stu-id="4e28a-434">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="4e28a-435">xunit</span><span class="sxs-lookup"><span data-stu-id="4e28a-435">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="4e28a-436">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="4e28a-436">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="4e28a-437">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="4e28a-437">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="4e28a-438">SUT 环境</span><span class="sxs-lookup"><span data-stu-id="4e28a-438">SUT environment</span></span>

<span data-ttu-id="4e28a-439">如果未设置 SUT 的[环境](xref:fundamentals/environments)，则环境会默认为“开发”。</span><span class="sxs-lookup"><span data-stu-id="4e28a-439">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="4e28a-440">使用默认 WebApplicationFactory 的基本测试</span><span class="sxs-lookup"><span data-stu-id="4e28a-440">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="4e28a-441">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 用于为集成测试创建 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-441">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="4e28a-442">`TEntryPoint` 是 SUT 的入口点类，通常是 `Startup` 类。</span><span class="sxs-lookup"><span data-stu-id="4e28a-442">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="4e28a-443">测试类实现一个类固定例程  接口 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture))，以指示类包含测试，并在类中的所有测试间提供共享对象实例。</span><span class="sxs-lookup"><span data-stu-id="4e28a-443">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="4e28a-444">以下测试类 `BasicTests` 使用 `WebApplicationFactory` 启动 SUT，并向测试方法 `Get_EndpointsReturnSuccessAndCorrectContentType` 提供 [HttpClient](/dotnet/api/system.net.http.httpclient)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-444">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="4e28a-445">该方法检查响应状态代码是否成功（处于范围 200-299 中的状态代码），以及 `Content-Type` 标头是否为适用于多个应用页面的 `text/html; charset=utf-8`。</span><span class="sxs-lookup"><span data-stu-id="4e28a-445">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="4e28a-446">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 创建自动追随重定向并处理 cookie 的 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="4e28a-446">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="4e28a-447">默认情况下，在启用了 [GDPR 同意策略](xref:security/gdpr)时，不会在请求间保留非必要 cookie。</span><span class="sxs-lookup"><span data-stu-id="4e28a-447">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="4e28a-448">若要保留非必要 cookie（如 TempData 提供程序使用的 cookie），请在测试中将它们标记为必要。</span><span class="sxs-lookup"><span data-stu-id="4e28a-448">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="4e28a-449">有关将 cookie 标记为必要的说明，请参阅[必要 cookie](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-449">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="4e28a-450">自定义 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="4e28a-450">Customize WebApplicationFactory</span></span>

<span data-ttu-id="4e28a-451">通过从 `WebApplicationFactory` 来创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：</span><span class="sxs-lookup"><span data-stu-id="4e28a-451">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="4e28a-452">从 `WebApplicationFactory` 继承并替代 [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-452">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="4e28a-453">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 允许使用 [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices) 配置服务集合：</span><span class="sxs-lookup"><span data-stu-id="4e28a-453">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="4e28a-454">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-454">Database seeding in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="4e28a-455">[集成测试示例：测试应用组织](#test-app-organization)部分中介绍了该方法。</span><span class="sxs-lookup"><span data-stu-id="4e28a-455">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="4e28a-456">在测试类中使用自定义 `CustomWebApplicationFactory`。</span><span class="sxs-lookup"><span data-stu-id="4e28a-456">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="4e28a-457">下面的示例使用 `IndexPageTests` 类中的工厂：</span><span class="sxs-lookup"><span data-stu-id="4e28a-457">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="4e28a-458">示例应用的客户端配置为阻止 `HttpClient` 追随重定向。</span><span class="sxs-lookup"><span data-stu-id="4e28a-458">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="4e28a-459">如稍后在[模拟身份验证](#mock-authentication)部分中所述，这允许测试检查应用第一个响应的结果。</span><span class="sxs-lookup"><span data-stu-id="4e28a-459">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="4e28a-460">第一个响应是在许多具有 `Location` 标头的测试中进行重定向。</span><span class="sxs-lookup"><span data-stu-id="4e28a-460">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="4e28a-461">典型测试使用 `HttpClient` 和帮助程序方法处理请求和响应：</span><span class="sxs-lookup"><span data-stu-id="4e28a-461">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="4e28a-462">对 SUT 发出的任何 POST 请求都必须满足防伪检查，该检查由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动执行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-462">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="4e28a-463">若要安排测试的 POST 请求，测试应用必须：</span><span class="sxs-lookup"><span data-stu-id="4e28a-463">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="4e28a-464">对页面发出请求。</span><span class="sxs-lookup"><span data-stu-id="4e28a-464">Make a request for the page.</span></span>
1. <span data-ttu-id="4e28a-465">分析来自响应的防伪 cookie 和请求验证令牌。</span><span class="sxs-lookup"><span data-stu-id="4e28a-465">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="4e28a-466">发出放置了防伪 cookie 和请求验证令牌的 POST 请求。</span><span class="sxs-lookup"><span data-stu-id="4e28a-466">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="4e28a-467">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 `SendAsync` 帮助程序扩展方法 (Helpers/HttpClientExtensions.cs  ) 和 `GetDocumentAsync` 帮助程序方法 (Helpers/HtmlHelpers.cs  ) 使用 [AngleSharp](https://anglesharp.github.io/) 分析程序，通过以下方法处理防伪检查：</span><span class="sxs-lookup"><span data-stu-id="4e28a-467">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="4e28a-468">`GetDocumentAsync` &ndash; 接收 [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) 并返回 `IHtmlDocument`。</span><span class="sxs-lookup"><span data-stu-id="4e28a-468">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="4e28a-469">`GetDocumentAsync` 使用一个基于原始 `HttpResponseMessage` 准备虚拟响应  的工厂。</span><span class="sxs-lookup"><span data-stu-id="4e28a-469">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="4e28a-470">有关详细信息，请参阅 [AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-470">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="4e28a-471">`HttpClient` 的 `SendAsync` 扩展方法撰写 [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) 并调用 [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)，以向 SUT 提交请求。</span><span class="sxs-lookup"><span data-stu-id="4e28a-471">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="4e28a-472">`SendAsync` 的重载接受 HTML 窗体 (`IHtmlFormElement`) 和以下内容：</span><span class="sxs-lookup"><span data-stu-id="4e28a-472">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="4e28a-473">窗体的“提交”按钮 (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="4e28a-473">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="4e28a-474">窗体值集合 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="4e28a-474">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="4e28a-475">提交按钮 (`IHtmlElement`) 和窗体值 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="4e28a-475">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="4e28a-476">[AngleSharp](https://anglesharp.github.io/) 是在本主题和示例应用中用于演示的第三方分析库。</span><span class="sxs-lookup"><span data-stu-id="4e28a-476">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="4e28a-477">ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="4e28a-477">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="4e28a-478">可以使用其他分析程序，如 [Html Agility Pack (HAP)](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-478">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="4e28a-479">另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。</span><span class="sxs-lookup"><span data-stu-id="4e28a-479">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="4e28a-480">使用 WithWebHostBuilder 自定义客户端</span><span class="sxs-lookup"><span data-stu-id="4e28a-480">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="4e28a-481">当测试方法中需要其他配置时，[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) 可创建新 `WebApplicationFactory`，其中包含通过配置进一步自定义的 [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-481">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="4e28a-482">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) 的 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 测试方法演示了 `WithWebHostBuilder` 的使用。</span><span class="sxs-lookup"><span data-stu-id="4e28a-482">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="4e28a-483">此测试通过在 SUT 中触发窗体提交，在数据库中执行记录删除。</span><span class="sxs-lookup"><span data-stu-id="4e28a-483">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="4e28a-484">由于 `IndexPageTests` 类中的另一个测试会执行删除数据库中所有记录的操作，并且可能在 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 方法之前运行，因此数据库会在此测试方法中重新进行种子设定，以确保存在记录供 SUT 删除。</span><span class="sxs-lookup"><span data-stu-id="4e28a-484">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="4e28a-485">在 SUT 中选择 `messages` 窗体的第一个删除按钮可在向 SUT 发出的请求中进行模拟：</span><span class="sxs-lookup"><span data-stu-id="4e28a-485">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="4e28a-486">客户端选项</span><span class="sxs-lookup"><span data-stu-id="4e28a-486">Client options</span></span>

<span data-ttu-id="4e28a-487">下表显示在创建 `HttpClient` 实例时可用的默认 [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-487">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="4e28a-488">选项</span><span class="sxs-lookup"><span data-stu-id="4e28a-488">Option</span></span> | <span data-ttu-id="4e28a-489">描述</span><span class="sxs-lookup"><span data-stu-id="4e28a-489">Description</span></span> | <span data-ttu-id="4e28a-490">默认</span><span class="sxs-lookup"><span data-stu-id="4e28a-490">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="4e28a-491">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="4e28a-491">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="4e28a-492">获取或设置 `HttpClient` 实例是否应自动追随重定向响应。</span><span class="sxs-lookup"><span data-stu-id="4e28a-492">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="4e28a-493">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="4e28a-493">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="4e28a-494">获取或设置 `HttpClient` 实例的基址。</span><span class="sxs-lookup"><span data-stu-id="4e28a-494">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="4e28a-495">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="4e28a-495">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="4e28a-496">获取或设置 `HttpClient` 实例是否应处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="4e28a-496">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="4e28a-497">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="4e28a-497">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="4e28a-498">获取或设置 `HttpClient` 实例应追随的重定向响应的最大数量。</span><span class="sxs-lookup"><span data-stu-id="4e28a-498">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="4e28a-499">7</span><span class="sxs-lookup"><span data-stu-id="4e28a-499">7</span></span> |

<span data-ttu-id="4e28a-500">创建 `WebApplicationFactoryClientOptions` 类并将它传递给 [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) 方法（默认值显示在代码示例中）：</span><span class="sxs-lookup"><span data-stu-id="4e28a-500">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="4e28a-501">注入模拟服务</span><span class="sxs-lookup"><span data-stu-id="4e28a-501">Inject mock services</span></span>

<span data-ttu-id="4e28a-502">可以通过在主机生成器上调用 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)，在测试中替代服务。</span><span class="sxs-lookup"><span data-stu-id="4e28a-502">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="4e28a-503">若要注入模拟服务，SUT 必须具有包含 `Startup.ConfigureServices` 方法的 `Startup` 类。 </span><span class="sxs-lookup"><span data-stu-id="4e28a-503">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="4e28a-504">示例 SUT 包含返回引用的作用域服务。</span><span class="sxs-lookup"><span data-stu-id="4e28a-504">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="4e28a-505">向索引页面进行请求时，引用嵌入在索引页面上的隐藏字段中。</span><span class="sxs-lookup"><span data-stu-id="4e28a-505">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="4e28a-506">Services/IQuoteService.cs  ：</span><span class="sxs-lookup"><span data-stu-id="4e28a-506">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="4e28a-507">Services/QuoteService.cs  ：</span><span class="sxs-lookup"><span data-stu-id="4e28a-507">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="4e28a-508">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="4e28a-508">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="4e28a-509"> Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="4e28a-509">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="4e28a-510">Pages/Index.cs  ：</span><span class="sxs-lookup"><span data-stu-id="4e28a-510">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="4e28a-511">运行 SUT 应用时，会生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="4e28a-511">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="4e28a-512">若要在集成测试中测试服务和引用注入，测试会将模拟服务注入到 SUT 中。</span><span class="sxs-lookup"><span data-stu-id="4e28a-512">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="4e28a-513">模拟服务会将应用的 `QuoteService` 替换为测试应用提供的服务，称为 `TestQuoteService`：</span><span class="sxs-lookup"><span data-stu-id="4e28a-513">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="4e28a-514">IntegrationTests.IndexPageTests.cs  ：</span><span class="sxs-lookup"><span data-stu-id="4e28a-514">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="4e28a-515">调用 `ConfigureTestServices`，并注册作用域服务：</span><span class="sxs-lookup"><span data-stu-id="4e28a-515">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="4e28a-516">在测试执行过程中生成的标记反映由 `TestQuoteService` 提供的引用文本，因而断言通过：</span><span class="sxs-lookup"><span data-stu-id="4e28a-516">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="4e28a-517">模拟身份验证</span><span class="sxs-lookup"><span data-stu-id="4e28a-517">Mock authentication</span></span>

<span data-ttu-id="4e28a-518">`AuthTests` 类中的测试检查安全终结点是否：</span><span class="sxs-lookup"><span data-stu-id="4e28a-518">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="4e28a-519">将未经身份验证的用户重定向到应用的登录页面。</span><span class="sxs-lookup"><span data-stu-id="4e28a-519">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="4e28a-520">为经过身份验证的用户返回内容。</span><span class="sxs-lookup"><span data-stu-id="4e28a-520">Returns content for an authenticated user.</span></span>

<span data-ttu-id="4e28a-521">在 SUT 中，`/SecurePage` 页面使用 [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) 约定将 [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) 应用于页面。</span><span class="sxs-lookup"><span data-stu-id="4e28a-521">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="4e28a-522">有关详细信息，请参阅 [Razor Pages 约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-522">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="4e28a-523">在 `Get_SecurePageRedirectsAnUnauthenticatedUser` 测试中，[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 设置为禁止重定向，具体方法是将 [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) 设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="4e28a-523">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="4e28a-524">通过禁止客户端追随重定向，可以执行以下检查：</span><span class="sxs-lookup"><span data-stu-id="4e28a-524">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="4e28a-525">可以根据预期 [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) 结果检查 SUT 返回的状态代码，而不是在重定向到登录页面之后的最终状态代码（这会是 [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode)）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-525">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="4e28a-526">检查响应标头中的 `Location` 标头值，以确认它以 `http://localhost/Identity/Account/Login` 开头，而不是最终登录页面响应（其中 `Location` 标头不存在）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-526">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="4e28a-527">测试应用可以在 [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) 中模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>，以便测试身份验证和授权的各个方面。</span><span class="sxs-lookup"><span data-stu-id="4e28a-527">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="4e28a-528">最小方案返回一个 [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：</span><span class="sxs-lookup"><span data-stu-id="4e28a-528">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="4e28a-529">当身份验证方案设置为 `Test`（其中为 `ConfigureTestServices` 注册了 `AddAuthentication`）时，会调用 `TestAuthHandler` 以对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="4e28a-529">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="4e28a-530">有关 `WebApplicationFactoryClientOptions` 的详细信息，请参阅[客户端选项](#client-options)部分。</span><span class="sxs-lookup"><span data-stu-id="4e28a-530">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="4e28a-531">设置环境</span><span class="sxs-lookup"><span data-stu-id="4e28a-531">Set the environment</span></span>

<span data-ttu-id="4e28a-532">默认情况下，SUT 的主机和应用环境配置为使用开发环境。</span><span class="sxs-lookup"><span data-stu-id="4e28a-532">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="4e28a-533">替代 SUT 的环境：</span><span class="sxs-lookup"><span data-stu-id="4e28a-533">To override the SUT's environment:</span></span>

* <span data-ttu-id="4e28a-534">设置 `ASPNETCORE_ENVIRONMENT` 环境变量（例如，`Staging`、`Production` 或其他自定义值，例如 `Testing`）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-534">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="4e28a-535">在测试应用中替代 `CreateHostBuilder`，以读取以 `ASPNETCORE` 为前缀的环境变量。</span><span class="sxs-lookup"><span data-stu-id="4e28a-535">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="4e28a-536">测试基础结构如何推断应用内容根路径</span><span class="sxs-lookup"><span data-stu-id="4e28a-536">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="4e28a-537">`WebApplicationFactory` 构造函数通过在包含集成测试的程序集中搜索键等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName` 的 [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)，来推断应用[内容根](xref:fundamentals/index#content-root)路径。</span><span class="sxs-lookup"><span data-stu-id="4e28a-537">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="4e28a-538">如果找不到具有正确键的属性，则 `WebApplicationFactory` 会回退到搜索解决方案文件 (.sln  ) 并将 `TEntryPoint` 程序集名称追加到解决方案目录。</span><span class="sxs-lookup"><span data-stu-id="4e28a-538">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="4e28a-539">应用根目录（内容根路径）用于发现视图和内容文件。</span><span class="sxs-lookup"><span data-stu-id="4e28a-539">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="4e28a-540">禁用卷影复制</span><span class="sxs-lookup"><span data-stu-id="4e28a-540">Disable shadow copying</span></span>

<span data-ttu-id="4e28a-541">卷影复制会导致在与输出目录不同的目录中执行测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-541">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="4e28a-542">若要使测试正常工作，必须禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="4e28a-542">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="4e28a-543">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的 xunit.runner.json  文件来对 xUnit 禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="4e28a-543">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="4e28a-544">有关详细信息，请参阅[使用 JSON 配置 xUnit](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-544">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="4e28a-545">将包含以下内容的 xunit.runner.json  文件添加到测试项目的根：</span><span class="sxs-lookup"><span data-stu-id="4e28a-545">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="4e28a-546">如果使用 Visual Studio，将文件的“复制到输出目录”  属性设置为“始终复制”  。</span><span class="sxs-lookup"><span data-stu-id="4e28a-546">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="4e28a-547">如果不使用 Visual Studio，请将 `Content` 目标添加到测试应用的项目文件：</span><span class="sxs-lookup"><span data-stu-id="4e28a-547">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="4e28a-548">对象的处置</span><span class="sxs-lookup"><span data-stu-id="4e28a-548">Disposal of objects</span></span>

<span data-ttu-id="4e28a-549">执行 `IClassFixture` 实现的测试之后，当 xUnit 处置 [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) 时，[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 和 [HttpClient](/dotnet/api/system.net.http.httpclient) 会进行处置。</span><span class="sxs-lookup"><span data-stu-id="4e28a-549">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="4e28a-550">如果开发者实例化的对象需要处置，请在 `IClassFixture` 实现中处置它们。</span><span class="sxs-lookup"><span data-stu-id="4e28a-550">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="4e28a-551">有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-551">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="4e28a-552">集成测试示例</span><span class="sxs-lookup"><span data-stu-id="4e28a-552">Integration tests sample</span></span>

<span data-ttu-id="4e28a-553">[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)包含两个应用：</span><span class="sxs-lookup"><span data-stu-id="4e28a-553">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="4e28a-554">应用</span><span class="sxs-lookup"><span data-stu-id="4e28a-554">App</span></span> | <span data-ttu-id="4e28a-555">项目目录</span><span class="sxs-lookup"><span data-stu-id="4e28a-555">Project directory</span></span> | <span data-ttu-id="4e28a-556">描述</span><span class="sxs-lookup"><span data-stu-id="4e28a-556">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="4e28a-557">消息应用 (SUT)</span><span class="sxs-lookup"><span data-stu-id="4e28a-557">Message app (the SUT)</span></span> | <span data-ttu-id="4e28a-558">src/RazorPagesProject </span><span class="sxs-lookup"><span data-stu-id="4e28a-558">*src/RazorPagesProject*</span></span> | <span data-ttu-id="4e28a-559">允许用户添加消息、删除一个消息、删除所有消息和分析消息。</span><span class="sxs-lookup"><span data-stu-id="4e28a-559">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="4e28a-560">测试应用</span><span class="sxs-lookup"><span data-stu-id="4e28a-560">Test app</span></span> | <span data-ttu-id="4e28a-561">tests/RazorPagesProject.Tests </span><span class="sxs-lookup"><span data-stu-id="4e28a-561">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="4e28a-562">用于集成测试 SUT。</span><span class="sxs-lookup"><span data-stu-id="4e28a-562">Used to integration test the SUT.</span></span> |

<span data-ttu-id="4e28a-563">可使用 IDE 的内置测试功能（例如 [Visual Studio](https://visualstudio.microsoft.com)）运行测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-563">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="4e28a-564">如果使用 [Visual Studio Code](https://code.visualstudio.com/) 或命令行，请在 tests/RazorPagesProject.Tests  目录中的命令提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="4e28a-564">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="4e28a-565">消息应用 (SUT) 组织</span><span class="sxs-lookup"><span data-stu-id="4e28a-565">Message app (SUT) organization</span></span>

<span data-ttu-id="4e28a-566">SUT 是具有以下特征的 Razor Pages 消息系统：</span><span class="sxs-lookup"><span data-stu-id="4e28a-566">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="4e28a-567">应用的索引页面（Pages/Index.cshtml  和 Pages/Index.cshtml.cs  ）提供 UI 和页面模型方法，用于控制添加、删除和分析消息（每个消息的平均字词数）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-567">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="4e28a-568">消息由 `Message` 类 (Data/Message.cs  ) 描述，并具有两个属性：`Id`（键）和 `Text`（消息）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-568">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="4e28a-569">`Text` 属性是必需的，并限制为 200 个字符。</span><span class="sxs-lookup"><span data-stu-id="4e28a-569">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="4e28a-570">消息使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)存储。&#8224;</span><span class="sxs-lookup"><span data-stu-id="4e28a-570">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="4e28a-571">应用在其数据库上下文类 `AppDbContext` (Data/AppDbContext.cs  ) 中包含数据访问层 (DAL)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-571">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="4e28a-572">如果应用启动时数据库为空，则消息存储初始化为三条消息。</span><span class="sxs-lookup"><span data-stu-id="4e28a-572">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="4e28a-573">应用包含只能由经过身份验证的用户访问的 `/SecurePage`。</span><span class="sxs-lookup"><span data-stu-id="4e28a-573">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="4e28a-574">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明如何将内存中数据库用于 MSTest 测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-574">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="4e28a-575">本主题使用 [xUnit](https://xunit.github.io/) 测试框架。</span><span class="sxs-lookup"><span data-stu-id="4e28a-575">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="4e28a-576">不同测试框架中的测试概念和测试实现相似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="4e28a-576">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="4e28a-577">尽管应用未使用存储库模式且不是[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些开发模式。</span><span class="sxs-lookup"><span data-stu-id="4e28a-577">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="4e28a-578">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（该示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-578">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="4e28a-579">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="4e28a-579">Test app organization</span></span>

<span data-ttu-id="4e28a-580">测试应用是 tests/RazorPagesProject.Tests  目录中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="4e28a-580">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="4e28a-581">测试应用目录</span><span class="sxs-lookup"><span data-stu-id="4e28a-581">Test app directory</span></span> | <span data-ttu-id="4e28a-582">描述</span><span class="sxs-lookup"><span data-stu-id="4e28a-582">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="4e28a-583">AuthTests </span><span class="sxs-lookup"><span data-stu-id="4e28a-583">*AuthTests*</span></span> | <span data-ttu-id="4e28a-584">包含针对以下方面的测试方法：</span><span class="sxs-lookup"><span data-stu-id="4e28a-584">Contains test methods for:</span></span><ul><li><span data-ttu-id="4e28a-585">未经身份验证的用户访问安全页面。</span><span class="sxs-lookup"><span data-stu-id="4e28a-585">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="4e28a-586">经过身份验证的用户访问安全页面（通过模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>）。</span><span class="sxs-lookup"><span data-stu-id="4e28a-586">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="4e28a-587">获取 GitHub 用户配置文件，并检查配置文件的用户登录。</span><span class="sxs-lookup"><span data-stu-id="4e28a-587">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="4e28a-588">BasicTests </span><span class="sxs-lookup"><span data-stu-id="4e28a-588">*BasicTests*</span></span> | <span data-ttu-id="4e28a-589">包含用于路由和内容类型的测试方法。</span><span class="sxs-lookup"><span data-stu-id="4e28a-589">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="4e28a-590">IntegrationTests </span><span class="sxs-lookup"><span data-stu-id="4e28a-590">*IntegrationTests*</span></span> | <span data-ttu-id="4e28a-591">包含使用自定义 `WebApplicationFactory` 类的索引页面的集成测试。</span><span class="sxs-lookup"><span data-stu-id="4e28a-591">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="4e28a-592">Helpers/Utilities </span><span class="sxs-lookup"><span data-stu-id="4e28a-592">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="4e28a-593">Utilities.cs  包含用于通过测试数据设定数据库种子的 `InitializeDbForTests` 方法。</span><span class="sxs-lookup"><span data-stu-id="4e28a-593">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="4e28a-594">HtmlHelpers.cs  提供了一种方法，用于返回 AngleSharp `IHtmlDocument` 供测试方法使用。</span><span class="sxs-lookup"><span data-stu-id="4e28a-594">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="4e28a-595">HttpClientExtensions.cs  为 `SendAsync` 提供重载，以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="4e28a-595">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="4e28a-596">测试框架为 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="4e28a-596">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="4e28a-597">集成测试使用 [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)（包括 [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)）执行。</span><span class="sxs-lookup"><span data-stu-id="4e28a-597">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="4e28a-598">由于 [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) 包用于配置测试主机和测试服务器，因此 `TestHost` 和 `TestServer` 包在测试应用的项目文件或测试应用的开发者配置中不需要直接包引用。</span><span class="sxs-lookup"><span data-stu-id="4e28a-598">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="4e28a-599">设定数据库种子以进行测试 </span><span class="sxs-lookup"><span data-stu-id="4e28a-599">**Seeding the database for testing**</span></span>

<span data-ttu-id="4e28a-600">集成测试在执行测试前通常需要数据库中的小型数据集。</span><span class="sxs-lookup"><span data-stu-id="4e28a-600">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="4e28a-601">例如，删除测试需要进行数据库记录删除，因此数据库必须至少有一个记录，删除请求才能成功。</span><span class="sxs-lookup"><span data-stu-id="4e28a-601">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="4e28a-602">示例应用使用 Utilities.cs  中的三个消息（测试在执行时可以使用它们）设定数据库种子：</span><span class="sxs-lookup"><span data-stu-id="4e28a-602">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="4e28a-603">其他资源</span><span class="sxs-lookup"><span data-stu-id="4e28a-603">Additional resources</span></span>

* [<span data-ttu-id="4e28a-604">单元测试</span><span class="sxs-lookup"><span data-stu-id="4e28a-604">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
