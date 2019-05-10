---
title: 在 ASP.NET Core 中的集成测试
author: guardrex
description: 了解集成测试如何在基础结构级别（包括数据库、文件系统和网络）确保应用组件功能正常。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/25/2019
uid: test/integration-tests
ms.openlocfilehash: 46c3b227ca0b3def5ab7d527a2f6ef2497d55f83
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892064"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="c419c-103">在 ASP.NET Core 中的集成测试</span><span class="sxs-lookup"><span data-stu-id="c419c-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="c419c-104">通过[Luke Latham](https://github.com/guardrex)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="c419c-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="c419c-105">集成测试可确保应用程序的组件正常工作，包括应用程序的支持的基础结构，如数据库、 文件系统和网络级别。</span><span class="sxs-lookup"><span data-stu-id="c419c-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="c419c-106">ASP.NET Core 支持集成测试的测试 web 主机和内存中测试服务器中使用单元测试框架。</span><span class="sxs-lookup"><span data-stu-id="c419c-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="c419c-107">本主题假定你基本了解单元测试。</span><span class="sxs-lookup"><span data-stu-id="c419c-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="c419c-108">如果测试概念不太熟悉，请参阅[.NET Core 和.NET Standard 中的单元测试](/dotnet/core/testing/)主题和其链接的内容。</span><span class="sxs-lookup"><span data-stu-id="c419c-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="c419c-109">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="c419c-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="c419c-110">示例应用是 Razor 页面应用，并假定你基本了解 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="c419c-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="c419c-111">如果不熟悉使用 Razor 页面，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="c419c-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="c419c-112">Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="c419c-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="c419c-113">Razor 页面入门</span><span class="sxs-lookup"><span data-stu-id="c419c-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="c419c-114">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="c419c-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="c419c-115">用于测试 Spa，我们建议使用一种工具如[Selenium](https://www.seleniumhq.org/)，这可以自动执行浏览器。</span><span class="sxs-lookup"><span data-stu-id="c419c-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="c419c-116">集成测试简介</span><span class="sxs-lookup"><span data-stu-id="c419c-116">Introduction to integration tests</span></span>

<span data-ttu-id="c419c-117">集成测试评估上水平比更为广泛的应用程序的组件[单元测试](/dotnet/core/testing/)。</span><span class="sxs-lookup"><span data-stu-id="c419c-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="c419c-118">单元测试用于测试独立的软件组件，例如单独的类的方法。</span><span class="sxs-lookup"><span data-stu-id="c419c-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="c419c-119">集成测试确认，两个或多个应用程序组件的协作产生预期的结果，可能包括将完全处理一个请求所需的每个组件。</span><span class="sxs-lookup"><span data-stu-id="c419c-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="c419c-120">这些更广泛的测试用于测试应用程序的基础结构和整个 framework 中，通常包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="c419c-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="c419c-121">数据库</span><span class="sxs-lookup"><span data-stu-id="c419c-121">Database</span></span>
* <span data-ttu-id="c419c-122">文件系统</span><span class="sxs-lookup"><span data-stu-id="c419c-122">File system</span></span>
* <span data-ttu-id="c419c-123">网络设备</span><span class="sxs-lookup"><span data-stu-id="c419c-123">Network appliances</span></span>
* <span data-ttu-id="c419c-124">请求-响应管道</span><span class="sxs-lookup"><span data-stu-id="c419c-124">Request-response pipeline</span></span>

<span data-ttu-id="c419c-125">单元测试使用制造的组件，称为*fakes*或*mock 对象*，代替基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="c419c-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="c419c-126">与单元测试，集成测试：</span><span class="sxs-lookup"><span data-stu-id="c419c-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="c419c-127">使用该应用在生产环境中使用的实际组件。</span><span class="sxs-lookup"><span data-stu-id="c419c-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="c419c-128">需要更多代码和数据处理。</span><span class="sxs-lookup"><span data-stu-id="c419c-128">Require more code and data processing.</span></span>
* <span data-ttu-id="c419c-129">需要更长时间运行。</span><span class="sxs-lookup"><span data-stu-id="c419c-129">Take longer to run.</span></span>

<span data-ttu-id="c419c-130">因此，限制为最重要的基础结构方案的集成测试使用。</span><span class="sxs-lookup"><span data-stu-id="c419c-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="c419c-131">如果使用单元测试或集成测试，可以测试行为，请选择单元测试。</span><span class="sxs-lookup"><span data-stu-id="c419c-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="c419c-132">不编写与数据库和文件系统的数据和文件访问的每个可能的排列的集成测试。</span><span class="sxs-lookup"><span data-stu-id="c419c-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="c419c-133">无论多少位跨应用程序与数据库和文件系统、 已设定焦点的一组读取、 写入、 更新和删除集成测试通常能够充分地测试数据库和文件系统组件进行交互。</span><span class="sxs-lookup"><span data-stu-id="c419c-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="c419c-134">使用单元测试的与这些组件进行交互的例程测试的方法逻辑。</span><span class="sxs-lookup"><span data-stu-id="c419c-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="c419c-135">在单元测试基础结构使用虚设/模拟测试执行速度更快的结果。</span><span class="sxs-lookup"><span data-stu-id="c419c-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="c419c-136">在讨论的集成测试时，通常称为测试的项目*待测试系统*，或简称"SUT"。</span><span class="sxs-lookup"><span data-stu-id="c419c-136">In discussions of integration tests, the tested project is frequently called the *system under test*, or "SUT" for short.</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="c419c-137">ASP.NET Core 集成测试</span><span class="sxs-lookup"><span data-stu-id="c419c-137">ASP.NET Core integration tests</span></span>

<span data-ttu-id="c419c-138">在 ASP.NET Core 中的集成测试需要以下项：</span><span class="sxs-lookup"><span data-stu-id="c419c-138">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="c419c-139">测试项目用于包含和执行测试。</span><span class="sxs-lookup"><span data-stu-id="c419c-139">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="c419c-140">测试项目具有对名为经过测试的 ASP.NET Core 项目的引用*待测试系统*(SUT)。</span><span class="sxs-lookup"><span data-stu-id="c419c-140">The test project has a reference to the tested ASP.NET Core project, called the *system under test* (SUT).</span></span> <span data-ttu-id="c419c-141">_在本主题中使用"SUT"来指代经过测试的应用。_</span><span class="sxs-lookup"><span data-stu-id="c419c-141">_"SUT" is used throughout this topic to refer to the tested app._</span></span>
* <span data-ttu-id="c419c-142">测试项目创建 SUT 的测试 web 主机，并使用测试服务器客户端处理请求和对 SUT 的响应。</span><span class="sxs-lookup"><span data-stu-id="c419c-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses to the SUT.</span></span>
* <span data-ttu-id="c419c-143">测试运行程序用于执行测试和报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="c419c-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="c419c-144">集成测试遵循一系列事件，其中包括常用*排列*， *Act*，并*Assert*测试步骤：</span><span class="sxs-lookup"><span data-stu-id="c419c-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="c419c-145">SUT 的 web 主机配置。</span><span class="sxs-lookup"><span data-stu-id="c419c-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="c419c-146">创建测试服务器客户端以将请求提交到应用程序。</span><span class="sxs-lookup"><span data-stu-id="c419c-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="c419c-147">*排列*执行测试步骤：测试应用程序准备请求。</span><span class="sxs-lookup"><span data-stu-id="c419c-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="c419c-148">*Act*执行测试步骤：客户端提交该请求并接收响应。</span><span class="sxs-lookup"><span data-stu-id="c419c-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="c419c-149">*Assert*执行测试步骤：*实际*响应中进行验证*传递*或*失败*基于*预期*响应。</span><span class="sxs-lookup"><span data-stu-id="c419c-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="c419c-150">过程持续进行的所有测试执行。</span><span class="sxs-lookup"><span data-stu-id="c419c-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="c419c-151">报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="c419c-151">The test results are reported.</span></span>

<span data-ttu-id="c419c-152">通常情况下，测试 web 主机配置以不同的方式不是测试应用程序的普通的 web 主机在运行。</span><span class="sxs-lookup"><span data-stu-id="c419c-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="c419c-153">例如，测试可能使用不同的数据库或不同的应用设置。</span><span class="sxs-lookup"><span data-stu-id="c419c-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="c419c-154">基础结构组件，如测试 web 主机和内存中测试服务器 ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver))、 提供或由托管[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包。</span><span class="sxs-lookup"><span data-stu-id="c419c-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="c419c-155">使用此包的简化了测试创建和执行。</span><span class="sxs-lookup"><span data-stu-id="c419c-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="c419c-156">`Microsoft.AspNetCore.Mvc.Testing`程序包处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="c419c-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="c419c-157">将复制依赖项文件 (*\*.deps*) 到测试项目的 sut *bin*文件夹。</span><span class="sxs-lookup"><span data-stu-id="c419c-157">Copies the dependencies file (*\*.deps*) from the SUT into the test project's *bin* folder.</span></span>
* <span data-ttu-id="c419c-158">内容根设置为 SUT 的项目根，使静态文件和网页/视图时执行的测试发现。</span><span class="sxs-lookup"><span data-stu-id="c419c-158">Sets the content root to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="c419c-159">提供了[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)类，以简化启动与 SUT `TestServer`。</span><span class="sxs-lookup"><span data-stu-id="c419c-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="c419c-160">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍了如何设置测试项目和测试运行程序以及如何运行测试和的建议的名称测试和测试类的详细说明。</span><span class="sxs-lookup"><span data-stu-id="c419c-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="c419c-161">在创建应用程序的测试项目时，分离到不同的项目集成测试中的单元测试。</span><span class="sxs-lookup"><span data-stu-id="c419c-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="c419c-162">这样可以帮助确保测试基础结构组件不会意外地包括在单元测试。</span><span class="sxs-lookup"><span data-stu-id="c419c-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="c419c-163">单元测试和集成测试的分离还允许控制对哪些组的测试运行。</span><span class="sxs-lookup"><span data-stu-id="c419c-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="c419c-164">几乎的 Razor 页面应用程序测试的配置和 MVC 应用程序之间没有差异。</span><span class="sxs-lookup"><span data-stu-id="c419c-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="c419c-165">唯一区别是在测试的命名方式。</span><span class="sxs-lookup"><span data-stu-id="c419c-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="c419c-166">在 Razor 页面应用中，测试页终结点的名称通常以在页面模型类后 (例如，`IndexPageTests`测试索引页的组件集成)。</span><span class="sxs-lookup"><span data-stu-id="c419c-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="c419c-167">在 MVC 应用中，测试是通常按控制器类和命名这些测试的控制器 (例如，`HomeControllerTests`测试 Home 控制器的组件集成)。</span><span class="sxs-lookup"><span data-stu-id="c419c-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="c419c-168">测试应用程序必备组件</span><span class="sxs-lookup"><span data-stu-id="c419c-168">Test app prerequisites</span></span>

<span data-ttu-id="c419c-169">测试项目必须：</span><span class="sxs-lookup"><span data-stu-id="c419c-169">The test project must:</span></span>

* <span data-ttu-id="c419c-170">引用以下包：</span><span class="sxs-lookup"><span data-stu-id="c419c-170">Reference the following packages:</span></span>
  * [<span data-ttu-id="c419c-171">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="c419c-171">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="c419c-172">Microsoft.AspNetCore.Mvc.Testing</span><span class="sxs-lookup"><span data-stu-id="c419c-172">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="c419c-173">在项目文件中指定 Web SDK (`<Project Sdk="Microsoft.NET.Sdk.Web">`)。</span><span class="sxs-lookup"><span data-stu-id="c419c-173">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="c419c-174">Web SDK 时是必需的引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="c419c-174">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="c419c-175">这些系统必备组件中所示[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)。</span><span class="sxs-lookup"><span data-stu-id="c419c-175">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="c419c-176">检查*tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj*文件。</span><span class="sxs-lookup"><span data-stu-id="c419c-176">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="c419c-177">示例应用使用[xUnit](https://xunit.github.io/)测试框架和[AngleSharp](https://anglesharp.github.io/)分析器库，因此示例应用还引用：</span><span class="sxs-lookup"><span data-stu-id="c419c-177">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="c419c-178">xunit</span><span class="sxs-lookup"><span data-stu-id="c419c-178">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="c419c-179">xunit.runner.visualstudio</span><span class="sxs-lookup"><span data-stu-id="c419c-179">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="c419c-180">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="c419c-180">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="c419c-181">SUT 环境</span><span class="sxs-lookup"><span data-stu-id="c419c-181">SUT environment</span></span>

<span data-ttu-id="c419c-182">如果 SUT[环境](xref:fundamentals/environments)未设置开发环境的默认值。</span><span class="sxs-lookup"><span data-stu-id="c419c-182">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="c419c-183">默认值 WebApplicationFactory 基本测试</span><span class="sxs-lookup"><span data-stu-id="c419c-183">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="c419c-184">[WebApplicationFactory&lt;TEntryPoint&gt; ](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用来创建[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)集成测试。</span><span class="sxs-lookup"><span data-stu-id="c419c-184">[WebApplicationFactory&lt;TEntryPoint&gt;](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="c419c-185">`TEntryPoint` 通常是 SUT 的入口点类`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="c419c-185">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="c419c-186">测试类将实现*类装置*接口 ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) 表明类包含测试以及在测试类中提供共享的对象实例。</span><span class="sxs-lookup"><span data-stu-id="c419c-186">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="c419c-187">基本应用程序终结点的测试</span><span class="sxs-lookup"><span data-stu-id="c419c-187">Basic test of app endpoints</span></span>

<span data-ttu-id="c419c-188">以下测试类， `BasicTests`，使用`WebApplicationFactory`若要启动 SUT 和提供[HttpClient](/dotnet/api/system.net.http.httpclient)到测试方法、 `Get_EndpointsReturnSuccessAndCorrectContentType`。</span><span class="sxs-lookup"><span data-stu-id="c419c-188">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="c419c-189">该方法检查是否成功的响应状态代码 （状态代码在 200-299 范围） 和`Content-Type`标头是`text/html; charset=utf-8`若干应用程序页。</span><span class="sxs-lookup"><span data-stu-id="c419c-189">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="c419c-190">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)创建的实例`HttpClient`的自动遵循重定向，并处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="c419c-190">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="c419c-191">测试安全终结点</span><span class="sxs-lookup"><span data-stu-id="c419c-191">Test a secure endpoint</span></span>

<span data-ttu-id="c419c-192">中的另一个测试`BasicTests`类将检查安全终结点到应用程序的登录页将未经身份验证的用户重定向。</span><span class="sxs-lookup"><span data-stu-id="c419c-192">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="c419c-193">在 SUT 中`/SecurePage`页上使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)约定应用[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)到页。</span><span class="sxs-lookup"><span data-stu-id="c419c-193">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="c419c-194">有关详细信息，请参阅[Razor 页面授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="c419c-194">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="c419c-195">在中`Get_SecurePageRequiresAnAuthenticatedUser`测试，请[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)设置为不重定向允许通过设置[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)到`false`:</span><span class="sxs-lookup"><span data-stu-id="c419c-195">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="c419c-196">通过禁止客户端遵循重定向，可以进行以下检查：</span><span class="sxs-lookup"><span data-stu-id="c419c-196">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="c419c-197">可以检查返回的 SUT 的状态代码针对预期[HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode)结果中，不是最终状态代码后将重定向到登录页面，将是[HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span><span class="sxs-lookup"><span data-stu-id="c419c-197">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="c419c-198">`Location`响应标头中的标头值进行检查以确认它开头`http://localhost/Identity/Account/Login`，不将最终的登录页响应，其中`Location`标头不会为存在。</span><span class="sxs-lookup"><span data-stu-id="c419c-198">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="c419c-199">有关详细信息`WebApplicationFactoryClientOptions`，请参阅[客户端选项](#client-options)部分。</span><span class="sxs-lookup"><span data-stu-id="c419c-199">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="c419c-200">自定义 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="c419c-200">Customize WebApplicationFactory</span></span>

<span data-ttu-id="c419c-201">Web 主机配置可以通过继承创建独立的测试类于`WebApplicationFactory`来创建一个或多个自定义工厂：</span><span class="sxs-lookup"><span data-stu-id="c419c-201">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="c419c-202">继承自`WebApplicationFactory`并重写[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="c419c-202">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="c419c-203">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)允许使用的服务集合的配置[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span><span class="sxs-lookup"><span data-stu-id="c419c-203">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="c419c-204">数据库在种子设定[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由执行`InitializeDbForTests`方法。</span><span class="sxs-lookup"><span data-stu-id="c419c-204">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="c419c-205">中介绍了方法[集成测试示例：测试应用程序的组织](#test-app-organization)部分。</span><span class="sxs-lookup"><span data-stu-id="c419c-205">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="c419c-206">使用自定义`CustomWebApplicationFactory`测试类中。</span><span class="sxs-lookup"><span data-stu-id="c419c-206">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="c419c-207">下面的示例使用中的工厂`IndexPageTests`类：</span><span class="sxs-lookup"><span data-stu-id="c419c-207">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="c419c-208">示例应用程序的客户端配置为阻止`HttpClient`从以下重定向。</span><span class="sxs-lookup"><span data-stu-id="c419c-208">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="c419c-209">中所述[测试安全终结点](#test-a-secure-endpoint)部分中，这允许测试以检查应用程序的第一个响应的结果。</span><span class="sxs-lookup"><span data-stu-id="c419c-209">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="c419c-210">第一个响应是许多与这些测试中的重定向`Location`标头。</span><span class="sxs-lookup"><span data-stu-id="c419c-210">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="c419c-211">典型测试中使用`HttpClient`和帮助器方法来处理请求和响应：</span><span class="sxs-lookup"><span data-stu-id="c419c-211">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="c419c-212">任何对 SUT 的 POST 请求必须满足应用的自动所做的防伪复选[的数据保护防伪系统](xref:security/data-protection/introduction)。</span><span class="sxs-lookup"><span data-stu-id="c419c-212">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="c419c-213">若要安排某个测试的 POST 请求，测试应用程序必须：</span><span class="sxs-lookup"><span data-stu-id="c419c-213">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="c419c-214">发出页请求。</span><span class="sxs-lookup"><span data-stu-id="c419c-214">Make a request for the page.</span></span>
1. <span data-ttu-id="c419c-215">分析防伪 cookie 和请求验证令牌响应中。</span><span class="sxs-lookup"><span data-stu-id="c419c-215">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="c419c-216">在位置进行 POST 请求，其中防伪 cookie 和请求验证令牌。</span><span class="sxs-lookup"><span data-stu-id="c419c-216">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="c419c-217">`SendAsync` Helper 扩展方法 (*Helpers/HttpClientExtensions.cs*) 和`GetDocumentAsync`帮助器方法 (*Helpers/HtmlHelpers.cs*) 中[的示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)使用[AngleSharp](https://anglesharp.github.io/)分析器以使用以下方法处理防伪检查：</span><span class="sxs-lookup"><span data-stu-id="c419c-217">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="c419c-218">`GetDocumentAsync` &ndash; 接收[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) ，并返回`IHtmlDocument`。</span><span class="sxs-lookup"><span data-stu-id="c419c-218">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="c419c-219">`GetDocumentAsync` 使用工厂，用于准备*虚拟响应*根据原始`HttpResponseMessage`。</span><span class="sxs-lookup"><span data-stu-id="c419c-219">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="c419c-220">有关详细信息，请参阅[AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="c419c-220">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="c419c-221">`SendAsync` 扩展方法`HttpClient`compose [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) ，并调用[SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="c419c-221">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="c419c-222">有关重载`SendAsync`接受 HTML 窗体 (`IHtmlFormElement`) 和以下：</span><span class="sxs-lookup"><span data-stu-id="c419c-222">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="c419c-223">提交窗体的按钮 (`IHtmlElement`)</span><span class="sxs-lookup"><span data-stu-id="c419c-223">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="c419c-224">窗体值集合 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="c419c-224">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="c419c-225">提交按钮 (`IHtmlElement`) 和窗体值 (`IEnumerable<KeyValuePair<string, string>>`)</span><span class="sxs-lookup"><span data-stu-id="c419c-225">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="c419c-226">[AngleSharp](https://anglesharp.github.io/)第三方分析用于演示目的，在本主题和示例应用程序中的库。</span><span class="sxs-lookup"><span data-stu-id="c419c-226">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="c419c-227">AngleSharp 不受支持或所需的集成测试的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="c419c-227">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="c419c-228">其他分析器可以使用，如[Html 灵活性包 (HAP)](http://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="c419c-228">Other parsers can be used, such as the [Html Agility Pack (HAP)](http://html-agility-pack.net/).</span></span> <span data-ttu-id="c419c-229">另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。</span><span class="sxs-lookup"><span data-stu-id="c419c-229">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="c419c-230">自定义客户端与 WithWebHostBuilder</span><span class="sxs-lookup"><span data-stu-id="c419c-230">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="c419c-231">在测试方法中，需要额外的配置时[WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)创建一个新`WebApplicationFactory`与[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)的配置通过进一步自定义。</span><span class="sxs-lookup"><span data-stu-id="c419c-231">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="c419c-232">`Post_DeleteMessageHandler_ReturnsRedirectToRoot`测试的方法[示例应用程序](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)演示如何使用`WithWebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="c419c-232">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="c419c-233">此测试可执行记录删除数据库中通过触发在 SUT 中的窗体提交。</span><span class="sxs-lookup"><span data-stu-id="c419c-233">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="c419c-234">因为另一个测试中`IndexPageTests`类执行的操作将删除所有数据库中的记录，并且可能运行之前`Post_DeleteMessageHandler_ReturnsRedirectToRoot`方法中，数据库将植入以确保记录存在的 SUT，若要删除此测试方法中。</span><span class="sxs-lookup"><span data-stu-id="c419c-234">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is seeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="c419c-235">选择`deleteBtn1`按钮的`messages`窗体在 SUT 中的模拟对 SUT 的请求中：</span><span class="sxs-lookup"><span data-stu-id="c419c-235">Selecting the `deleteBtn1` button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="c419c-236">客户端选项</span><span class="sxs-lookup"><span data-stu-id="c419c-236">Client options</span></span>

<span data-ttu-id="c419c-237">下表显示的默认[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)时创建可用`HttpClient`实例。</span><span class="sxs-lookup"><span data-stu-id="c419c-237">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="c419c-238">选项</span><span class="sxs-lookup"><span data-stu-id="c419c-238">Option</span></span> | <span data-ttu-id="c419c-239">描述</span><span class="sxs-lookup"><span data-stu-id="c419c-239">Description</span></span> | <span data-ttu-id="c419c-240">默认</span><span class="sxs-lookup"><span data-stu-id="c419c-240">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="c419c-241">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="c419c-241">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="c419c-242">获取或设置是否`HttpClient`实例应自动跟随重定向响应。</span><span class="sxs-lookup"><span data-stu-id="c419c-242">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="c419c-243">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="c419c-243">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="c419c-244">获取或设置的基址`HttpClient`实例。</span><span class="sxs-lookup"><span data-stu-id="c419c-244">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="c419c-245">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="c419c-245">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="c419c-246">获取或设置是否`HttpClient`实例应处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="c419c-246">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="c419c-247">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="c419c-247">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="c419c-248">获取或设置重定向响应的最大数目`HttpClient`实例应遵循。</span><span class="sxs-lookup"><span data-stu-id="c419c-248">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="c419c-249">7</span><span class="sxs-lookup"><span data-stu-id="c419c-249">7</span></span> |

<span data-ttu-id="c419c-250">创建`WebApplicationFactoryClientOptions`类，并将其传递给[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)方法 （默认值代码示例所示）：</span><span class="sxs-lookup"><span data-stu-id="c419c-250">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="c419c-251">插入模拟服务</span><span class="sxs-lookup"><span data-stu-id="c419c-251">Inject mock services</span></span>

<span data-ttu-id="c419c-252">服务可以通过调用测试中重写[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)主机生成器上。</span><span class="sxs-lookup"><span data-stu-id="c419c-252">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="c419c-253">**若要插入的模拟服务，SUT 必须具有`Startup`类的`Startup.ConfigureServices`方法。**</span><span class="sxs-lookup"><span data-stu-id="c419c-253">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="c419c-254">示例 SUT 包含作用域内返回的服务的引号。</span><span class="sxs-lookup"><span data-stu-id="c419c-254">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="c419c-255">请求索引页时，在索引页的隐藏字段中嵌入引号。</span><span class="sxs-lookup"><span data-stu-id="c419c-255">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="c419c-256">*Services/IQuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="c419c-256">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="c419c-257">*Services/QuoteService.cs*:</span><span class="sxs-lookup"><span data-stu-id="c419c-257">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="c419c-258">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="c419c-258">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="c419c-259">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="c419c-259">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="c419c-260">*Pages/Index.cs*:</span><span class="sxs-lookup"><span data-stu-id="c419c-260">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="c419c-261">当 SUT 应用运行时，会生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="c419c-261">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="c419c-262">若要在集成测试中测试服务和引号注入，模拟服务注入到 SUT 的测试。</span><span class="sxs-lookup"><span data-stu-id="c419c-262">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="c419c-263">模拟服务替代了应用程序的`QuoteService`测试应用程序提供的服务，名为`TestQuoteService`:</span><span class="sxs-lookup"><span data-stu-id="c419c-263">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="c419c-264">*IntegrationTests.IndexPageTests.cs*:</span><span class="sxs-lookup"><span data-stu-id="c419c-264">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="c419c-265">`ConfigureTestServices` 调用时，并指定了作用域的服务将注册：</span><span class="sxs-lookup"><span data-stu-id="c419c-265">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="c419c-266">在执行测试的执行期间生成的标记反映了由提供的报价文本`TestQuoteService`，因此在断言阶段：</span><span class="sxs-lookup"><span data-stu-id="c419c-266">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="c419c-267">测试基础结构如何推断应用内容根路径</span><span class="sxs-lookup"><span data-stu-id="c419c-267">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="c419c-268">`WebApplicationFactory`构造函数通过搜索来推断应用内容根路径[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)包含其键等于集成测试的程序集上`TEntryPoint`的程序集`System.Reflection.Assembly.FullName`.</span><span class="sxs-lookup"><span data-stu-id="c419c-268">The `WebApplicationFactory` constructor infers the app content root path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="c419c-269">如果找不到具有正确的密钥的属性，`WebApplicationFactory`回退到搜索解决方案文件 (*\*.sln*)，并追加`TEntryPoint`到解决方案目录的程序集名称。</span><span class="sxs-lookup"><span data-stu-id="c419c-269">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*\*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="c419c-270">应用程序根目录下 （内容根路径） 用于发现视图和内容文件。</span><span class="sxs-lookup"><span data-stu-id="c419c-270">The app root directory (the content root path) is used to discover views and content files.</span></span>

<span data-ttu-id="c419c-271">在大多数情况下，无需显式设置应用程序内容根，在搜索逻辑通常在运行时找到正确的内容根。</span><span class="sxs-lookup"><span data-stu-id="c419c-271">In most cases, it isn't necessary to explicitly set the app content root, as the search logic usually finds the correct content root at runtime.</span></span> <span data-ttu-id="c419c-272">内容根位置找不到的特殊方案中使用内置的搜索算法，内容可以指定根目录，显式或使用自定义逻辑应用。</span><span class="sxs-lookup"><span data-stu-id="c419c-272">In special scenarios where the content root isn't found using the built-in search algorithm, the app content root can be specified explicitly or by using custom logic.</span></span> <span data-ttu-id="c419c-273">若要将应用内容根目录设置在这些情况下，调用`UseSolutionRelativeContentRoot`扩展方法来源[Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost)包。</span><span class="sxs-lookup"><span data-stu-id="c419c-273">To set the app content root in those scenarios, call the `UseSolutionRelativeContentRoot` extension method from the [Microsoft.AspNetCore.TestHost](https://www.nuget.org/packages/Microsoft.AspNetCore.TestHost) package.</span></span> <span data-ttu-id="c419c-274">提供解决方案的相对路径和可选的解决方案文件的名称或 glob 模式 (默认值 = `*.sln`)。</span><span class="sxs-lookup"><span data-stu-id="c419c-274">Supply the solution's relative path and optional solution file name or glob pattern (default = `*.sln`).</span></span>

<span data-ttu-id="c419c-275">调用[UseSolutionRelativeContentRoot](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.usesolutionrelativecontentroot)扩展方法使用*一个*以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="c419c-275">Call the [UseSolutionRelativeContentRoot](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.usesolutionrelativecontentroot) extension method using *ONE* of the following approaches:</span></span>

* <span data-ttu-id="c419c-276">配置与测试类时`WebApplicationFactory`，提供的自定义配置[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder):</span><span class="sxs-lookup"><span data-stu-id="c419c-276">When configuring test classes with `WebApplicationFactory`, provide a custom configuration with the [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder):</span></span>

   ```csharp
   public IndexPageTests(
       WebApplicationFactory<RazorPagesProject.Startup> factory)
   {
       var _factory = factory.WithWebHostBuilder(builder =>
       {
           builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

           ...
       });
   }
   ```

* <span data-ttu-id="c419c-277">使用自定义配置测试类时`WebApplicationFactory`，继承自`WebApplicationFactory`并重写[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost):</span><span class="sxs-lookup"><span data-stu-id="c419c-277">When configuring test classes with a custom `WebApplicationFactory`, inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost):</span></span>

   ```csharp
   public class CustomWebApplicationFactory<TStartup>
       : WebApplicationFactory<RazorPagesProject.Startup>
   {
       protected override void ConfigureWebHost(IWebHostBuilder builder)
       {
           builder.ConfigureServices(services =>
           {
               builder.UseSolutionRelativeContentRoot("<SOLUTION-RELATIVE-PATH>");

               ...
           });
       }
   }
   ```

## <a name="disable-shadow-copying"></a><span data-ttu-id="c419c-278">禁用卷影复制</span><span class="sxs-lookup"><span data-stu-id="c419c-278">Disable shadow copying</span></span>

<span data-ttu-id="c419c-279">卷影复制会导致要在不同的文件夹的输出文件夹中执行的测试。</span><span class="sxs-lookup"><span data-stu-id="c419c-279">Shadow copying causes the tests to execute in a different folder than the output folder.</span></span> <span data-ttu-id="c419c-280">对于测试才能正常工作，卷影复制必须禁用。</span><span class="sxs-lookup"><span data-stu-id="c419c-280">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="c419c-281">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 和禁用卷影复制对于 xUnit 通过包括*xunit.runner.json*文件，并正确的配置设置。</span><span class="sxs-lookup"><span data-stu-id="c419c-281">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="c419c-282">有关详细信息，请参阅[使用 JSON 配置 xUnit](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="c419c-282">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="c419c-283">添加*xunit.runner.json*到测试项目包含以下内容的根目录的文件：</span><span class="sxs-lookup"><span data-stu-id="c419c-283">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="c419c-284">可供使用的对象</span><span class="sxs-lookup"><span data-stu-id="c419c-284">Disposal of objects</span></span>

<span data-ttu-id="c419c-285">之后的测试`IClassFixture`实现会执行， [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)并[HttpClient](/dotnet/api/system.net.http.httpclient) xUnit 释放时，将释放[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span><span class="sxs-lookup"><span data-stu-id="c419c-285">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="c419c-286">如果对象实例化由开发人员需要可供使用，释放它们在`IClassFixture`实现。</span><span class="sxs-lookup"><span data-stu-id="c419c-286">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="c419c-287">有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="c419c-287">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="c419c-288">集成测试示例</span><span class="sxs-lookup"><span data-stu-id="c419c-288">Integration tests sample</span></span>

<span data-ttu-id="c419c-289">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由两个应用的组成：</span><span class="sxs-lookup"><span data-stu-id="c419c-289">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="c419c-290">应用</span><span class="sxs-lookup"><span data-stu-id="c419c-290">App</span></span> | <span data-ttu-id="c419c-291">项目文件夹</span><span class="sxs-lookup"><span data-stu-id="c419c-291">Project folder</span></span> | <span data-ttu-id="c419c-292">描述</span><span class="sxs-lookup"><span data-stu-id="c419c-292">Description</span></span> |
| --- | -------------- | ----------- |
| <span data-ttu-id="c419c-293">消息应用程序 (SUT)</span><span class="sxs-lookup"><span data-stu-id="c419c-293">Message app (the SUT)</span></span> | <span data-ttu-id="c419c-294">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="c419c-294">*src/RazorPagesProject*</span></span> | <span data-ttu-id="c419c-295">允许用户添加、 删除其中一个，删除所有，并分析消息。</span><span class="sxs-lookup"><span data-stu-id="c419c-295">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="c419c-296">测试应用</span><span class="sxs-lookup"><span data-stu-id="c419c-296">Test app</span></span> | <span data-ttu-id="c419c-297">*tests/RazorPagesProject.Tests*</span><span class="sxs-lookup"><span data-stu-id="c419c-297">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="c419c-298">用于集成测试 SUT。</span><span class="sxs-lookup"><span data-stu-id="c419c-298">Used to integration test the SUT.</span></span> |

<span data-ttu-id="c419c-299">可以使用内置测试功能的 IDE，如运行测试[Visual Studio](https://visualstudio.microsoft.com)。</span><span class="sxs-lookup"><span data-stu-id="c419c-299">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="c419c-300">如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行中，执行以下命令在命令提示符处*tests/RazorPagesProject.Tests*文件夹：</span><span class="sxs-lookup"><span data-stu-id="c419c-300">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* folder:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="c419c-301">消息应用 (SUT) 组织</span><span class="sxs-lookup"><span data-stu-id="c419c-301">Message app (SUT) organization</span></span>

<span data-ttu-id="c419c-302">SUT 是 Razor 页面消息系统具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="c419c-302">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="c419c-303">应用程序的索引页 (*pages/Index.cshtml*并*Pages/Index.cshtml.cs*) 提供 UI 和页面模型方法，用于控制添加、 删除和分析的消息 （每个消息的平均词）.</span><span class="sxs-lookup"><span data-stu-id="c419c-303">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="c419c-304">一条消息由描述`Message`类 (*Data/Message.cs*) 具有两个属性： `Id` （密钥） 和`Text`（消息）。</span><span class="sxs-lookup"><span data-stu-id="c419c-304">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="c419c-305">`Text`属性是必需的限制为 200 个字符。</span><span class="sxs-lookup"><span data-stu-id="c419c-305">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="c419c-306">使用存储的消息[Entity Framework 的内存中数据库](/ef/core/providers/in-memory/)&#8224;。</span><span class="sxs-lookup"><span data-stu-id="c419c-306">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="c419c-307">应用程序包含在其数据库上下文类中，数据访问层 (DAL) `AppDbContext` (*Data/AppDbContext.cs*)。</span><span class="sxs-lookup"><span data-stu-id="c419c-307">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="c419c-308">如果数据库为空应用程序启动时，使用三个消息初始化消息存储区。</span><span class="sxs-lookup"><span data-stu-id="c419c-308">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="c419c-309">该应用包含`/SecurePage`仅可通过身份验证的用户访问。</span><span class="sxs-lookup"><span data-stu-id="c419c-309">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="c419c-310">&#8224;EF 主题[测试与 InMemory](/ef/core/miscellaneous/testing/in-memory)，说明如何使用内存中数据库的使用 MSTest 的测试。</span><span class="sxs-lookup"><span data-stu-id="c419c-310">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="c419c-311">本主题使用[xUnit](https://xunit.github.io/)测试框架。</span><span class="sxs-lookup"><span data-stu-id="c419c-311">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="c419c-312">测试概念和跨不同测试框架的测试实现有类似，但不是完全相同。</span><span class="sxs-lookup"><span data-stu-id="c419c-312">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="c419c-313">虽然应用不使用存储库模式，并且不是有效的示例[工作单元 (UoW) 模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)，Razor 页面支持开发这些模式。</span><span class="sxs-lookup"><span data-stu-id="c419c-313">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="c419c-314">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)并[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（此示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="c419c-314">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="c419c-315">测试应用程序的组织</span><span class="sxs-lookup"><span data-stu-id="c419c-315">Test app organization</span></span>

<span data-ttu-id="c419c-316">测试应用程序是一个控制台应用程序内的*tests/RazorPagesProject.Tests*文件夹。</span><span class="sxs-lookup"><span data-stu-id="c419c-316">The test app is a console app inside the *tests/RazorPagesProject.Tests* folder.</span></span>

| <span data-ttu-id="c419c-317">测试应用程序文件夹</span><span class="sxs-lookup"><span data-stu-id="c419c-317">Test app folder</span></span> | <span data-ttu-id="c419c-318">描述</span><span class="sxs-lookup"><span data-stu-id="c419c-318">Description</span></span> |
| --------------- | ----------- |
| <span data-ttu-id="c419c-319">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="c419c-319">*BasicTests*</span></span> | <span data-ttu-id="c419c-320">*BasicTests.cs*包含测试方法的路由、 访问的安全页由未经身份验证的用户，并获取 GitHub 用户配置文件和检查配置文件的用户登录名。</span><span class="sxs-lookup"><span data-stu-id="c419c-320">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="c419c-321">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="c419c-321">*IntegrationTests*</span></span> | <span data-ttu-id="c419c-322">*IndexPageTests.cs*包含使用自定义的索引页的集成测试`WebApplicationFactory`类。</span><span class="sxs-lookup"><span data-stu-id="c419c-322">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="c419c-323">*帮助程序/实用程序*</span><span class="sxs-lookup"><span data-stu-id="c419c-323">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="c419c-324">*Utilities.cs*包含`InitializeDbForTests`方法用于设置测试数据与数据库的种子。</span><span class="sxs-lookup"><span data-stu-id="c419c-324">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="c419c-325">*HtmlHelpers.cs*提供了一个方法来返回 AngleSharp`IHtmlDocument`以供测试方法。</span><span class="sxs-lookup"><span data-stu-id="c419c-325">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="c419c-326">*HttpClientExtensions.cs*提供的重载`SendAsync`以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="c419c-326">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="c419c-327">测试框架这[xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="c419c-327">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="c419c-328">使用进行集成测试[Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost)，其中包括[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="c419c-328">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="c419c-329">因为[Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包用于配置测试主机和测试服务器`TestHost`和`TestServer`的包不需要测试应用的项目文件中的直接包引用或在测试应用程序的开发人员配置。</span><span class="sxs-lookup"><span data-stu-id="c419c-329">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="c419c-330">**用于测试数据库进行种子设定**</span><span class="sxs-lookup"><span data-stu-id="c419c-330">**Seeding the database for testing**</span></span>

<span data-ttu-id="c419c-331">集成测试通常需要在测试执行之前在数据库中的一个小型数据集。</span><span class="sxs-lookup"><span data-stu-id="c419c-331">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="c419c-332">例如，删除测试的数据库记录删除的调用，因此数据库必须具有至少一个记录删除请求才能成功。</span><span class="sxs-lookup"><span data-stu-id="c419c-332">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="c419c-333">示例应用程序中的三个消息数据库种子*Utilities.cs*测试可以使用它们执行时：</span><span class="sxs-lookup"><span data-stu-id="c419c-333">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

## <a name="additional-resources"></a><span data-ttu-id="c419c-334">其他资源</span><span class="sxs-lookup"><span data-stu-id="c419c-334">Additional resources</span></span>

* [<span data-ttu-id="c419c-335">单元测试</span><span class="sxs-lookup"><span data-stu-id="c419c-335">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="c419c-336">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="c419c-336">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)
* [<span data-ttu-id="c419c-337">中间件</span><span class="sxs-lookup"><span data-stu-id="c419c-337">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="c419c-338">测试控制器</span><span class="sxs-lookup"><span data-stu-id="c419c-338">Test controllers</span></span>](xref:mvc/controllers/testing)
