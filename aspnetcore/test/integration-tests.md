---
title: ASP.NET Core 中的集成测试
author: guardrex
description: 了解集成测试如何在基础结构级别（包括数据库、文件系统和网络）确保应用组件功能正常。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/14/2019
uid: test/integration-tests
ms.openlocfilehash: c0fede8f9f46d1b10502055d8e1fe7caa48cf351
ms.sourcegitcommit: 810d5831169770ee240d03207d6671dabea2486e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72779234"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="f94b4-103">ASP.NET Core 中的集成测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="f94b4-104">作者： [Luke Latham](https://github.com/guardrex)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="f94b4-104">By [Luke Latham](https://github.com/guardrex) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="f94b4-105">集成测试可确保应用程序的组件在包含应用程序支持的基础结构的级别（例如数据库、文件系统和网络）正常运行。</span><span class="sxs-lookup"><span data-stu-id="f94b4-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="f94b4-106">ASP.NET Core 支持结合使用单元测试框架和测试 web 主机和内存中测试服务器的集成测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="f94b4-107">本主题假定基本了解单元测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="f94b4-108">如果不熟悉测试概念，请参阅[.Net Core 中的单元测试和 .NET Standard](/dotnet/core/testing/)主题及其链接的内容。</span><span class="sxs-lookup"><span data-stu-id="f94b4-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="f94b4-109">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f94b4-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f94b4-110">该示例应用是 Razor Pages 应用程序，并假定基本了解 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="f94b4-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="f94b4-111">如果不熟悉 Razor Pages，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="f94b4-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="f94b4-112">Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="f94b4-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="f94b4-113">Razor 页面入门</span><span class="sxs-lookup"><span data-stu-id="f94b4-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="f94b4-114">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="f94b4-115">对于测试 Spa，我们建议使用[Selenium](https://www.seleniumhq.org/)这样的工具，它可以自动执行浏览器。</span><span class="sxs-lookup"><span data-stu-id="f94b4-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="f94b4-116">集成测试简介</span><span class="sxs-lookup"><span data-stu-id="f94b4-116">Introduction to integration tests</span></span>

<span data-ttu-id="f94b4-117">与[单元测试](/dotnet/core/testing/)相比，集成测试在更广泛的级别上评估应用的组件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="f94b4-118">单元测试用于测试独立的软件组件，如单独的类方法。</span><span class="sxs-lookup"><span data-stu-id="f94b4-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="f94b4-119">集成测试确认两个或更多应用程序组件一起工作以生成预期的结果，其中可能包括完全处理请求所需的每个组件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="f94b4-120">这些广泛的测试用于测试应用程序的基础结构和整个框架，通常包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="f94b4-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="f94b4-121">数据库</span><span class="sxs-lookup"><span data-stu-id="f94b4-121">Database</span></span>
* <span data-ttu-id="f94b4-122">文件系统</span><span class="sxs-lookup"><span data-stu-id="f94b4-122">File system</span></span>
* <span data-ttu-id="f94b4-123">网络设备</span><span class="sxs-lookup"><span data-stu-id="f94b4-123">Network appliances</span></span>
* <span data-ttu-id="f94b4-124">请求-响应管道</span><span class="sxs-lookup"><span data-stu-id="f94b4-124">Request-response pipeline</span></span>

<span data-ttu-id="f94b4-125">单元测试使用称为*fakes*或*mock 对象*的制造组件来代替基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="f94b4-126">与单元测试相比，集成测试：</span><span class="sxs-lookup"><span data-stu-id="f94b4-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="f94b4-127">使用应用在生产环境中使用的实际组件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="f94b4-128">需要进行更多的代码和数据处理。</span><span class="sxs-lookup"><span data-stu-id="f94b4-128">Require more code and data processing.</span></span>
* <span data-ttu-id="f94b4-129">需要较长时间才能运行。</span><span class="sxs-lookup"><span data-stu-id="f94b4-129">Take longer to run.</span></span>

<span data-ttu-id="f94b4-130">因此，将集成测试的使用限制为最重要的基础结构方案。</span><span class="sxs-lookup"><span data-stu-id="f94b4-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="f94b4-131">如果可以使用单元测试或集成测试来测试行为，请选择单元测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="f94b4-132">请勿为数据库和文件系统的每个可能的数据排列和文件访问编写集成测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="f94b4-133">无论应用程序中有多少位置与数据库和文件系统交互，一组集中式的读取、写入、更新和删除集成测试通常都能充分测试数据库和文件系统组件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="f94b4-134">使用单元测试对与这些组件进行交互的方法逻辑进行例程测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="f94b4-135">在单元测试中，使用基础结构 fakes/模拟会导致更快地执行测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="f94b4-136">在讨论集成测试时，测试的项目经常称为 "测试中的*系统*" 或简称 "SUT"。</span><span class="sxs-lookup"><span data-stu-id="f94b4-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="f94b4-137">*本主题中使用 "SUT" 来引用已测试的 ASP.NET Core 应用。*</span><span class="sxs-lookup"><span data-stu-id="f94b4-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="f94b4-138">ASP.NET Core 集成测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="f94b4-139">ASP.NET Core 中的集成测试需要以下各项：</span><span class="sxs-lookup"><span data-stu-id="f94b4-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="f94b4-140">测试项目用于包含和执行测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="f94b4-141">测试项目具有对 SUT 的引用。</span><span class="sxs-lookup"><span data-stu-id="f94b4-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="f94b4-142">测试项目将为 SUT 创建测试 web 主机，并使用测试服务器客户端来处理 SUT 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="f94b4-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="f94b4-143">测试运行程序用于执行测试并报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="f94b4-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="f94b4-144">集成测试遵循一系列事件，其中包括常见的*排列*、*操作*和*断言*测试步骤：</span><span class="sxs-lookup"><span data-stu-id="f94b4-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="f94b4-145">已配置 SUT 的 web 主机。</span><span class="sxs-lookup"><span data-stu-id="f94b4-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="f94b4-146">创建测试服务器客户端以向应用程序提交请求。</span><span class="sxs-lookup"><span data-stu-id="f94b4-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="f94b4-147">执行 "*排列*测试" 步骤：测试应用准备请求。</span><span class="sxs-lookup"><span data-stu-id="f94b4-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="f94b4-148">执行*Act*测试步骤：客户端提交请求并接收响应。</span><span class="sxs-lookup"><span data-stu-id="f94b4-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="f94b4-149">将*执行断言*测试步骤：根据*预期*的响应验证*实际*响应是*通过*还是*失败*。</span><span class="sxs-lookup"><span data-stu-id="f94b4-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="f94b4-150">此过程将一直继续，直到执行了所有测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="f94b4-151">将报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="f94b4-151">The test results are reported.</span></span>

<span data-ttu-id="f94b4-152">通常，测试 web 主机的配置与应用程序用于测试运行的普通 web 主机的配置不同。</span><span class="sxs-lookup"><span data-stu-id="f94b4-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="f94b4-153">例如，可以将不同的数据库或不同的应用设置用于测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="f94b4-154">基础结构组件（例如测试 web 主机和内存中测试服务器（[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)））由[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包提供或管理。</span><span class="sxs-lookup"><span data-stu-id="f94b4-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="f94b4-155">使用此包简化了测试的创建和执行。</span><span class="sxs-lookup"><span data-stu-id="f94b4-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="f94b4-156">`Microsoft.AspNetCore.Mvc.Testing` 包处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="f94b4-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="f94b4-157">将依赖项文件（ *. .deps.json*）从 SUT 复制到测试项目的*bin*目录中。</span><span class="sxs-lookup"><span data-stu-id="f94b4-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="f94b4-158">将[内容根](xref:fundamentals/index#content-root)设置为 SUT 的项目根，以便在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="f94b4-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="f94b4-159">提供[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)类，以简化与 `TestServer` 的 SUT 的引导。</span><span class="sxs-lookup"><span data-stu-id="f94b4-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="f94b4-160">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍了如何设置测试项目和测试运行程序，以及有关如何为测试和测试类命名测试和建议的详细说明。</span><span class="sxs-lookup"><span data-stu-id="f94b4-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="f94b4-161">为应用程序创建测试项目时，请将集成测试中的单元测试分成不同的项目。</span><span class="sxs-lookup"><span data-stu-id="f94b4-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="f94b4-162">这有助于确保不会意外地将基础结构测试组件包含在单元测试中。</span><span class="sxs-lookup"><span data-stu-id="f94b4-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="f94b4-163">单元测试和集成测试的隔离还允许控制运行的测试集。</span><span class="sxs-lookup"><span data-stu-id="f94b4-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="f94b4-164">Razor Pages 应用和 MVC 应用的测试的配置几乎没有任何区别。</span><span class="sxs-lookup"><span data-stu-id="f94b4-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="f94b4-165">唯一的区别在于测试的命名方式。</span><span class="sxs-lookup"><span data-stu-id="f94b4-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="f94b4-166">在 Razor Pages 应用中，页终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 来测试索引页的组件集成）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="f94b4-167">在 MVC 应用中，通常按控制器类对测试进行组织，并按它们所测试的控制器（例如 `HomeControllerTests` 来测试主控制器的组件集成）进行命名。</span><span class="sxs-lookup"><span data-stu-id="f94b4-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="f94b4-168">测试应用必备组件</span><span class="sxs-lookup"><span data-stu-id="f94b4-168">Test app prerequisites</span></span>

<span data-ttu-id="f94b4-169">测试项目必须：</span><span class="sxs-lookup"><span data-stu-id="f94b4-169">The test project must:</span></span>

* <span data-ttu-id="f94b4-170">请参阅[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包。</span><span class="sxs-lookup"><span data-stu-id="f94b4-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="f94b4-171">在项目文件中指定 Web SDK （`<Project Sdk="Microsoft.NET.Sdk.Web">`）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="f94b4-172">可以在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-172">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="f94b4-173">检查 "*测试"/"RazorPagesProject"/"RazorPagesProject* " 文件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="f94b4-174">示例应用使用[xUnit](https://xunit.github.io/)测试框架和[AngleSharp](https://anglesharp.github.io/)分析器库，因此示例应用还引用：</span><span class="sxs-lookup"><span data-stu-id="f94b4-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="f94b4-175">xunit</span><span class="sxs-lookup"><span data-stu-id="f94b4-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="f94b4-176">xunit. visualstudio</span><span class="sxs-lookup"><span data-stu-id="f94b4-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="f94b4-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="f94b4-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="f94b4-178">还可在测试中使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="f94b4-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="f94b4-179">应用引用：</span><span class="sxs-lookup"><span data-stu-id="f94b4-179">The app references:</span></span>

* [<span data-ttu-id="f94b4-180">AspNetCore. Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="f94b4-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [<span data-ttu-id="f94b4-181">AspNetCore. Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="f94b4-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [<span data-ttu-id="f94b4-182">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="f94b4-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="f94b4-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="f94b4-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="f94b4-184">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="f94b4-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="f94b4-185">SUT 环境</span><span class="sxs-lookup"><span data-stu-id="f94b4-185">SUT environment</span></span>

<span data-ttu-id="f94b4-186">如果未设置 SUT 的[环境](xref:fundamentals/environments)，环境将默认为 "开发"。</span><span class="sxs-lookup"><span data-stu-id="f94b4-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="f94b4-187">具有默认 WebApplicationFactory 的基本测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="f94b4-188">[WebApplicationFactory\<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用于创建集成测试的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。</span><span class="sxs-lookup"><span data-stu-id="f94b4-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="f94b4-189">`TEntryPoint` 是 SUT （通常是 `Startup` 类）的入口点类。</span><span class="sxs-lookup"><span data-stu-id="f94b4-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="f94b4-190">测试类实现*类装置*接口（[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)）以指示类包含测试，并跨类中的测试提供共享对象实例。</span><span class="sxs-lookup"><span data-stu-id="f94b4-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="f94b4-191">应用终结点的基本测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-191">Basic test of app endpoints</span></span>

<span data-ttu-id="f94b4-192">下面的测试类 `BasicTests`使用 `WebApplicationFactory` 来启动 SUT，并为测试方法 `Get_EndpointsReturnSuccessAndCorrectContentType`提供[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="f94b4-192">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="f94b4-193">方法将检查响应状态代码是否成功（范围200-299 中的状态代码）和多个应用页面的 `Content-Type` 标头是否 `text/html; charset=utf-8`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-193">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="f94b4-194">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)创建自动跟随重定向并处理 cookie 的 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="f94b4-194">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="f94b4-195">默认情况下，当启用[GDPR 同意策略](xref:security/gdpr)时，不会跨请求保留非关键 cookie。</span><span class="sxs-lookup"><span data-stu-id="f94b4-195">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="f94b4-196">若要保留不重要的 cookie （如 TempData 提供程序使用的 cookie），请将它们标记为测试中的重要 cookie。</span><span class="sxs-lookup"><span data-stu-id="f94b4-196">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="f94b4-197">有关将 cookie 标记为必要的说明，请参阅[基本 cookie](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-197">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="f94b4-198">测试安全终结点</span><span class="sxs-lookup"><span data-stu-id="f94b4-198">Test a secure endpoint</span></span>

<span data-ttu-id="f94b4-199">`BasicTests` 类中的另一个测试检查安全终结点是否将未经身份验证的用户重定向到应用程序的登录页。</span><span class="sxs-lookup"><span data-stu-id="f94b4-199">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="f94b4-200">在 SUT 中，`/SecurePage` 页面使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)约定将[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)应用到页面。</span><span class="sxs-lookup"><span data-stu-id="f94b4-200">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="f94b4-201">有关详细信息，请参阅[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-201">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="f94b4-202">在 `Get_SecurePageRequiresAnAuthenticatedUser` 测试中，通过将[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)设置为 `false`，将[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)设置为禁止重定向：</span><span class="sxs-lookup"><span data-stu-id="f94b4-202">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="f94b4-203">通过禁止客户端按照重定向操作，可以执行以下检查：</span><span class="sxs-lookup"><span data-stu-id="f94b4-203">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="f94b4-204">可以对照预期的[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)结果检查 SUT 返回的状态代码，而不是在重定向到登录页后返回最终状态代码，这会是[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-204">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="f94b4-205">将检查响应标头中的 `Location` 标头值，以确认该标头值以 `http://localhost/Identity/Account/Login`（而不是最终的登录页响应）开头，而 `Location` 标头不存在。</span><span class="sxs-lookup"><span data-stu-id="f94b4-205">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="f94b4-206">有关 `WebApplicationFactoryClientOptions` 的详细信息，请参阅[Client options](#client-options)部分。</span><span class="sxs-lookup"><span data-stu-id="f94b4-206">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="f94b4-207">自定义 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="f94b4-207">Customize WebApplicationFactory</span></span>

<span data-ttu-id="f94b4-208">通过继承 `WebApplicationFactory` 创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：</span><span class="sxs-lookup"><span data-stu-id="f94b4-208">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="f94b4-209">从 `WebApplicationFactory` 继承并重写[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-209">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="f94b4-210">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)允许通过[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)配置服务集合：</span><span class="sxs-lookup"><span data-stu-id="f94b4-210">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="f94b4-211">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。</span><span class="sxs-lookup"><span data-stu-id="f94b4-211">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="f94b4-212">[集成测试示例：测试应用组织](#test-app-organization)部分介绍了方法。</span><span class="sxs-lookup"><span data-stu-id="f94b4-212">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="f94b4-213">在其 `Startup.ConfigureServices` 方法中注册了 SUT 的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="f94b4-213">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f94b4-214">执行应用的 `Startup.ConfigureServices` 代码*后*，将执行测试应用的 `builder.ConfigureServices` 回调。</span><span class="sxs-lookup"><span data-stu-id="f94b4-214">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="f94b4-215">若要将不同于应用程序的数据库用于测试，则必须将应用程序的数据库上下文替换 `builder.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-215">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="f94b4-216">示例应用将查找数据库上下文的服务描述符，并使用描述符来删除服务注册。</span><span class="sxs-lookup"><span data-stu-id="f94b4-216">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="f94b4-217">接下来，工厂添加新的 `ApplicationDbContext`，使用内存中数据库进行测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-217">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="f94b4-218">若要连接到与内存中数据库不同的数据库，请更改 `UseInMemoryDatabase` 调用以将上下文连接到其他数据库。</span><span class="sxs-lookup"><span data-stu-id="f94b4-218">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="f94b4-219">使用 SQL Server 测试数据库：</span><span class="sxs-lookup"><span data-stu-id="f94b4-219">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="f94b4-220">引用项目文件中的[Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="f94b4-220">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="f94b4-221">使用数据库的连接字符串调用 `UseSqlServer`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-221">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="f94b4-222">使用测试类中的自定义 `CustomWebApplicationFactory`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-222">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="f94b4-223">下面的示例使用 `IndexPageTests` 类中的工厂：</span><span class="sxs-lookup"><span data-stu-id="f94b4-223">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="f94b4-224">示例应用的客户端配置为阻止以下重定向中的 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-224">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="f94b4-225">如 "[测试安全终结点](#test-a-secure-endpoint)" 一节中所述，这允许测试检查应用程序的第一个响应的结果。</span><span class="sxs-lookup"><span data-stu-id="f94b4-225">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="f94b4-226">第一个响应是包含 `Location` 标头的多个测试的重定向。</span><span class="sxs-lookup"><span data-stu-id="f94b4-226">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="f94b4-227">典型的测试使用 `HttpClient` 和 helper 方法来处理请求和响应：</span><span class="sxs-lookup"><span data-stu-id="f94b4-227">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="f94b4-228">对 SUT 的任何 POST 请求必须满足防伪检查，该检查是由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动完成的。</span><span class="sxs-lookup"><span data-stu-id="f94b4-228">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="f94b4-229">为了安排测试的 POST 请求，测试应用必须：</span><span class="sxs-lookup"><span data-stu-id="f94b4-229">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="f94b4-230">发出对页面的请求。</span><span class="sxs-lookup"><span data-stu-id="f94b4-230">Make a request for the page.</span></span>
1. <span data-ttu-id="f94b4-231">分析防伪 cookie 并请求响应中的验证令牌。</span><span class="sxs-lookup"><span data-stu-id="f94b4-231">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="f94b4-232">发出带有防伪 cookie 的 POST 请求，并就地请求验证令牌。</span><span class="sxs-lookup"><span data-stu-id="f94b4-232">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="f94b4-233">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 `SendAsync` helper 扩展方法（Helper */HttpClientExtensions*）和 `GetDocumentAsync` helper 方法（helper */HtmlHelpers*）使用[AngleSharp](https://anglesharp.github.io/)分析器来处理防伪检查以下方法：</span><span class="sxs-lookup"><span data-stu-id="f94b4-233">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="f94b4-234">`GetDocumentAsync` &ndash; 接收[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)并返回 `IHtmlDocument`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-234">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="f94b4-235">`GetDocumentAsync` 使用工厂来准备基于原始 `HttpResponseMessage` 的*虚拟响应*。</span><span class="sxs-lookup"><span data-stu-id="f94b4-235">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="f94b4-236">有关详细信息，请参阅[AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-236">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="f94b4-237">`SendAsync` 扩展方法 `HttpClient` 构成[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)并调用[SendAsync （HttpRequestMessage）](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="f94b4-237">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="f94b4-238">`SendAsync` 的重载接受 HTML 窗体（`IHtmlFormElement`）以及以下内容：</span><span class="sxs-lookup"><span data-stu-id="f94b4-238">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="f94b4-239">窗体的 "提交" 按钮（`IHtmlElement`）</span><span class="sxs-lookup"><span data-stu-id="f94b4-239">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="f94b4-240">窗体值集合（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="f94b4-240">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="f94b4-241">提交按钮（`IHtmlElement`）和窗体值（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="f94b4-241">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="f94b4-242">[AngleSharp](https://anglesharp.github.io/)是用于演示目的的第三方解析库，适用于本主题和示例应用。</span><span class="sxs-lookup"><span data-stu-id="f94b4-242">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="f94b4-243">ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="f94b4-243">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="f94b4-244">可以使用其他分析器，如[Html 灵活性包（HAP）](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-244">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="f94b4-245">另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。</span><span class="sxs-lookup"><span data-stu-id="f94b4-245">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="f94b4-246">用 WithWebHostBuilder 自定义客户端</span><span class="sxs-lookup"><span data-stu-id="f94b4-246">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="f94b4-247">如果在测试方法中需要其他配置， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)会创建一个新的 `WebApplicationFactory`，其中包含由配置进一步自定义的[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。</span><span class="sxs-lookup"><span data-stu-id="f94b4-247">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="f94b4-248">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 测试方法演示如何使用 `WithWebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-248">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="f94b4-249">此测试通过在 SUT 中触发窗体提交来在数据库中执行记录删除。</span><span class="sxs-lookup"><span data-stu-id="f94b4-249">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="f94b4-250">由于 `IndexPageTests` 类中的另一个测试会执行一个操作，该操作将删除数据库中的所有记录，并且该操作可能在 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 方法之前运行，因此，该数据库在此测试方法中种子，以确保要删除的 SUT 存在记录。</span><span class="sxs-lookup"><span data-stu-id="f94b4-250">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="f94b4-251">选择 SUT 中 `messages` 窗体的第一个 "删除" 按钮时，会将请求中的内容模拟到 SUT：</span><span class="sxs-lookup"><span data-stu-id="f94b4-251">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="f94b4-252">客户端选项</span><span class="sxs-lookup"><span data-stu-id="f94b4-252">Client options</span></span>

<span data-ttu-id="f94b4-253">下表显示了创建 `HttpClient` 实例时可用的默认[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 。</span><span class="sxs-lookup"><span data-stu-id="f94b4-253">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="f94b4-254">选项</span><span class="sxs-lookup"><span data-stu-id="f94b4-254">Option</span></span> | <span data-ttu-id="f94b4-255">描述</span><span class="sxs-lookup"><span data-stu-id="f94b4-255">Description</span></span> | <span data-ttu-id="f94b4-256">Default</span><span class="sxs-lookup"><span data-stu-id="f94b4-256">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="f94b4-257">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="f94b4-257">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="f94b4-258">获取或设置 `HttpClient` 实例是否应自动跟随重定向响应。</span><span class="sxs-lookup"><span data-stu-id="f94b4-258">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="f94b4-259">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="f94b4-259">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="f94b4-260">获取或设置 `HttpClient` 实例的基址。</span><span class="sxs-lookup"><span data-stu-id="f94b4-260">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="f94b4-261">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="f94b4-261">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="f94b4-262">获取或设置 `HttpClient` 实例是否应处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="f94b4-262">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="f94b4-263">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="f94b4-263">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="f94b4-264">获取或设置 `HttpClient` 实例应遵循的重定向响应的最大数目。</span><span class="sxs-lookup"><span data-stu-id="f94b4-264">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="f94b4-265">7</span><span class="sxs-lookup"><span data-stu-id="f94b4-265">7</span></span> |

<span data-ttu-id="f94b4-266">创建 `WebApplicationFactoryClientOptions` 类并将其传递给[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)方法（在代码示例中显示默认值）：</span><span class="sxs-lookup"><span data-stu-id="f94b4-266">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="f94b4-267">注入模拟服务</span><span class="sxs-lookup"><span data-stu-id="f94b4-267">Inject mock services</span></span>

<span data-ttu-id="f94b4-268">通过在主机生成器上调用[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，可以在测试中覆盖服务。</span><span class="sxs-lookup"><span data-stu-id="f94b4-268">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="f94b4-269">**若要注入模拟服务，SUT 必须有一个具有 `Startup.ConfigureServices` 方法的 `Startup` 类。**</span><span class="sxs-lookup"><span data-stu-id="f94b4-269">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="f94b4-270">示例 SUT 包含一个返回 quote 的作用域服务。</span><span class="sxs-lookup"><span data-stu-id="f94b4-270">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="f94b4-271">请求索引页时，引号嵌入到索引页上的隐藏字段中。</span><span class="sxs-lookup"><span data-stu-id="f94b4-271">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="f94b4-272">*服务/IQuoteService*：</span><span class="sxs-lookup"><span data-stu-id="f94b4-272">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="f94b4-273">*服务/QuoteService*：</span><span class="sxs-lookup"><span data-stu-id="f94b4-273">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="f94b4-274">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="f94b4-274">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="f94b4-275">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="f94b4-275">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="f94b4-276">*Pages/Index. cs*：</span><span class="sxs-lookup"><span data-stu-id="f94b4-276">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="f94b4-277">运行 SUT 应用时，将生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="f94b4-277">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="f94b4-278">若要在集成测试中测试服务和引号注入，测试会将模拟服务注入到 SUT。</span><span class="sxs-lookup"><span data-stu-id="f94b4-278">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="f94b4-279">模拟服务会将应用的 `QuoteService` 替换为测试应用提供的服务，称为 `TestQuoteService`：</span><span class="sxs-lookup"><span data-stu-id="f94b4-279">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="f94b4-280">*IntegrationTests.IndexPageTests.cs*：</span><span class="sxs-lookup"><span data-stu-id="f94b4-280">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="f94b4-281">调用 `ConfigureTestServices`，并注册作用域内服务：</span><span class="sxs-lookup"><span data-stu-id="f94b4-281">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="f94b4-282">在测试执行过程中生成的标记反映 `TestQuoteService`提供的引号文本，因此断言通过：</span><span class="sxs-lookup"><span data-stu-id="f94b4-282">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="f94b4-283">测试基础结构如何推断应用内容根路径</span><span class="sxs-lookup"><span data-stu-id="f94b4-283">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="f94b4-284">`WebApplicationFactory` 构造函数通过使用等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName`的键搜索包含集成测试的程序集上的[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)来推断应用[内容根](xref:fundamentals/index#content-root)路径。</span><span class="sxs-lookup"><span data-stu-id="f94b4-284">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="f94b4-285">如果找不到具有正确键的属性，`WebApplicationFactory` 将回退到搜索解决方案文件（ *.sln*），并在解决方案目录中追加 `TEntryPoint` 程序集名称。</span><span class="sxs-lookup"><span data-stu-id="f94b4-285">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="f94b4-286">应用根目录（内容根路径）用于发现视图和内容文件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-286">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="f94b4-287">禁用卷影复制</span><span class="sxs-lookup"><span data-stu-id="f94b4-287">Disable shadow copying</span></span>

<span data-ttu-id="f94b4-288">卷影复制会导致在输出目录以外的目录中执行测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-288">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="f94b4-289">要使测试正常工作，必须禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="f94b4-289">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="f94b4-290">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的*xUnit*文件来禁用 xUnit 的卷影复制。</span><span class="sxs-lookup"><span data-stu-id="f94b4-290">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="f94b4-291">有关详细信息，请参阅[配置 xUnit 与 JSON](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-291">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="f94b4-292">将*xunit*文件添加到测试项目的根，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="f94b4-292">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="f94b4-293">对象的处理</span><span class="sxs-lookup"><span data-stu-id="f94b4-293">Disposal of objects</span></span>

<span data-ttu-id="f94b4-294">执行 `IClassFixture` 实现的测试后，当 xUnit 释放[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)时，将释放[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="f94b4-294">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="f94b4-295">如果开发人员实例化的对象需要处置，请在 `IClassFixture` 实现中释放这些对象。</span><span class="sxs-lookup"><span data-stu-id="f94b4-295">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="f94b4-296">有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-296">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="f94b4-297">集成测试示例</span><span class="sxs-lookup"><span data-stu-id="f94b4-297">Integration tests sample</span></span>

<span data-ttu-id="f94b4-298">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由两个应用组成：</span><span class="sxs-lookup"><span data-stu-id="f94b4-298">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="f94b4-299">应用</span><span class="sxs-lookup"><span data-stu-id="f94b4-299">App</span></span> | <span data-ttu-id="f94b4-300">项目目录</span><span class="sxs-lookup"><span data-stu-id="f94b4-300">Project directory</span></span> | <span data-ttu-id="f94b4-301">描述</span><span class="sxs-lookup"><span data-stu-id="f94b4-301">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="f94b4-302">消息应用（SUT）</span><span class="sxs-lookup"><span data-stu-id="f94b4-302">Message app (the SUT)</span></span> | <span data-ttu-id="f94b4-303">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="f94b4-303">*src/RazorPagesProject*</span></span> | <span data-ttu-id="f94b4-304">允许用户添加、删除一个、删除和分析消息。</span><span class="sxs-lookup"><span data-stu-id="f94b4-304">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="f94b4-305">测试应用</span><span class="sxs-lookup"><span data-stu-id="f94b4-305">Test app</span></span> | <span data-ttu-id="f94b4-306">*测试/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="f94b4-306">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="f94b4-307">用于集成测试 SUT。</span><span class="sxs-lookup"><span data-stu-id="f94b4-307">Used to integration test the SUT.</span></span> |

<span data-ttu-id="f94b4-308">可以使用 IDE （如[Visual Studio](https://visualstudio.microsoft.com)）的内置测试功能来运行测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-308">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="f94b4-309">如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在*RazorPagesProject 目录中的命令*提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f94b4-309">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="f94b4-310">消息应用（SUT）组织</span><span class="sxs-lookup"><span data-stu-id="f94b4-310">Message app (SUT) organization</span></span>

<span data-ttu-id="f94b4-311">SUT 是 Razor Pages 的消息系统，具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="f94b4-311">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="f94b4-312">应用的 "索引" 页（*Pages/索引. cshtml*和*pages/index*）提供了一个 UI 和页面模型方法，用于控制消息的添加、删除和分析（每条消息的平均单词数）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-312">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="f94b4-313">消息由 `Message` 类（*Data/message*）描述，具有两个属性： `Id` （密钥）和 `Text` （message）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-313">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="f94b4-314">`Text` 属性是必需的，并且限制为200个字符。</span><span class="sxs-lookup"><span data-stu-id="f94b4-314">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="f94b4-315">使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。</span><span class="sxs-lookup"><span data-stu-id="f94b4-315">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="f94b4-316">应用在其数据库上下文类中包含数据访问层（DAL），`AppDbContext` （*data/AppDbContext*）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-316">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="f94b4-317">如果数据库在应用启动时为空，则会用三条消息初始化消息存储。</span><span class="sxs-lookup"><span data-stu-id="f94b4-317">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="f94b4-318">应用包括只能由经过身份验证的用户访问的 `/SecurePage`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-318">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="f94b4-319">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-319">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="f94b4-320">本主题使用[xUnit](https://xunit.github.io/)测试框架。</span><span class="sxs-lookup"><span data-stu-id="f94b4-320">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="f94b4-321">不同测试框架中的测试概念和测试实现相似，但并不完全相同。</span><span class="sxs-lookup"><span data-stu-id="f94b4-321">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="f94b4-322">尽管应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。</span><span class="sxs-lookup"><span data-stu-id="f94b4-322">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="f94b4-323">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-323">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="f94b4-324">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="f94b4-324">Test app organization</span></span>

<span data-ttu-id="f94b4-325">测试应用是 "*测试"/"RazorPagesProject* " 目录中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="f94b4-325">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="f94b4-326">测试应用程序目录</span><span class="sxs-lookup"><span data-stu-id="f94b4-326">Test app directory</span></span> | <span data-ttu-id="f94b4-327">描述</span><span class="sxs-lookup"><span data-stu-id="f94b4-327">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="f94b4-328">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="f94b4-328">*BasicTests*</span></span> | <span data-ttu-id="f94b4-329">*BasicTests.cs*包含用于路由的测试方法、通过未经身份验证的用户访问安全页以及获取 GitHub 用户配置文件和检查配置文件的用户登录名。</span><span class="sxs-lookup"><span data-stu-id="f94b4-329">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="f94b4-330">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="f94b4-330">*IntegrationTests*</span></span> | <span data-ttu-id="f94b4-331">*IndexPageTests.cs*包含使用自定义 `WebApplicationFactory` 类的索引页的集成测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-331">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="f94b4-332">*帮助程序/实用工具*</span><span class="sxs-lookup"><span data-stu-id="f94b4-332">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="f94b4-333">*Utilities.cs*包含用于使用测试数据对数据库进行种子设定的 `InitializeDbForTests` 方法。</span><span class="sxs-lookup"><span data-stu-id="f94b4-333">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="f94b4-334">*HtmlHelpers.cs*提供了一个方法，用于返回 AngleSharp `IHtmlDocument` 以供测试方法使用。</span><span class="sxs-lookup"><span data-stu-id="f94b4-334">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="f94b4-335">*HttpClientExtensions.cs*提供 `SendAsync` 的重载，以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="f94b4-335">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="f94b4-336">测试框架为[xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-336">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="f94b4-337">集成测试是使用 TestHost 的[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)进行的，其中包括[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-337">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="f94b4-338">由于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包用于配置测试主机和测试服务器，因此，在测试应用程序的项目文件或测试的开发人员配置中，`TestHost` 和 `TestServer` 包不需要直接包引用应用.</span><span class="sxs-lookup"><span data-stu-id="f94b4-338">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="f94b4-339">**播种要测试的数据库**</span><span class="sxs-lookup"><span data-stu-id="f94b4-339">**Seeding the database for testing**</span></span>

<span data-ttu-id="f94b4-340">集成测试在执行测试前通常需要数据库中的一个小型数据集。</span><span class="sxs-lookup"><span data-stu-id="f94b4-340">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="f94b4-341">例如，删除测试调用数据库记录，因此数据库必须至少有一条记录，删除请求才能成功。</span><span class="sxs-lookup"><span data-stu-id="f94b4-341">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="f94b4-342">该示例应用在*Utilities.cs*中使用三个消息对数据库进行种子设定，当测试执行时，可以使用这些消息：</span><span class="sxs-lookup"><span data-stu-id="f94b4-342">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="f94b4-343">在其 `Startup.ConfigureServices` 方法中注册了 SUT 的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="f94b4-343">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="f94b4-344">执行应用的 `Startup.ConfigureServices` 代码*后*，将执行测试应用的 `builder.ConfigureServices` 回调。</span><span class="sxs-lookup"><span data-stu-id="f94b4-344">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="f94b4-345">若要将不同的数据库用于测试，则必须将应用程序的数据库上下文替换 `builder.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-345">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="f94b4-346">有关详细信息，请参阅[自定义 WebApplicationFactory](#customize-webapplicationfactory)部分。</span><span class="sxs-lookup"><span data-stu-id="f94b4-346">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="f94b4-347">集成测试可确保应用程序的组件在包含应用程序支持的基础结构的级别（例如数据库、文件系统和网络）正常运行。</span><span class="sxs-lookup"><span data-stu-id="f94b4-347">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="f94b4-348">ASP.NET Core 支持结合使用单元测试框架和测试 web 主机和内存中测试服务器的集成测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-348">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="f94b4-349">本主题假定基本了解单元测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-349">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="f94b4-350">如果不熟悉测试概念，请参阅[.Net Core 中的单元测试和 .NET Standard](/dotnet/core/testing/)主题及其链接的内容。</span><span class="sxs-lookup"><span data-stu-id="f94b4-350">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="f94b4-351">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="f94b4-351">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="f94b4-352">该示例应用是 Razor Pages 应用程序，并假定基本了解 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="f94b4-352">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="f94b4-353">如果不熟悉 Razor Pages，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="f94b4-353">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="f94b4-354">Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="f94b4-354">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="f94b4-355">Razor 页面入门</span><span class="sxs-lookup"><span data-stu-id="f94b4-355">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="f94b4-356">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-356">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="f94b4-357">对于测试 Spa，我们建议使用[Selenium](https://www.seleniumhq.org/)这样的工具，它可以自动执行浏览器。</span><span class="sxs-lookup"><span data-stu-id="f94b4-357">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="f94b4-358">集成测试简介</span><span class="sxs-lookup"><span data-stu-id="f94b4-358">Introduction to integration tests</span></span>

<span data-ttu-id="f94b4-359">与[单元测试](/dotnet/core/testing/)相比，集成测试在更广泛的级别上评估应用的组件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-359">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="f94b4-360">单元测试用于测试独立的软件组件，如单独的类方法。</span><span class="sxs-lookup"><span data-stu-id="f94b4-360">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="f94b4-361">集成测试确认两个或更多应用程序组件一起工作以生成预期的结果，其中可能包括完全处理请求所需的每个组件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-361">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="f94b4-362">这些广泛的测试用于测试应用程序的基础结构和整个框架，通常包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="f94b4-362">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="f94b4-363">数据库</span><span class="sxs-lookup"><span data-stu-id="f94b4-363">Database</span></span>
* <span data-ttu-id="f94b4-364">文件系统</span><span class="sxs-lookup"><span data-stu-id="f94b4-364">File system</span></span>
* <span data-ttu-id="f94b4-365">网络设备</span><span class="sxs-lookup"><span data-stu-id="f94b4-365">Network appliances</span></span>
* <span data-ttu-id="f94b4-366">请求-响应管道</span><span class="sxs-lookup"><span data-stu-id="f94b4-366">Request-response pipeline</span></span>

<span data-ttu-id="f94b4-367">单元测试使用称为*fakes*或*mock 对象*的制造组件来代替基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-367">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="f94b4-368">与单元测试相比，集成测试：</span><span class="sxs-lookup"><span data-stu-id="f94b4-368">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="f94b4-369">使用应用在生产环境中使用的实际组件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-369">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="f94b4-370">需要进行更多的代码和数据处理。</span><span class="sxs-lookup"><span data-stu-id="f94b4-370">Require more code and data processing.</span></span>
* <span data-ttu-id="f94b4-371">需要较长时间才能运行。</span><span class="sxs-lookup"><span data-stu-id="f94b4-371">Take longer to run.</span></span>

<span data-ttu-id="f94b4-372">因此，将集成测试的使用限制为最重要的基础结构方案。</span><span class="sxs-lookup"><span data-stu-id="f94b4-372">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="f94b4-373">如果可以使用单元测试或集成测试来测试行为，请选择单元测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-373">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="f94b4-374">请勿为数据库和文件系统的每个可能的数据排列和文件访问编写集成测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-374">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="f94b4-375">无论应用程序中有多少位置与数据库和文件系统交互，一组集中式的读取、写入、更新和删除集成测试通常都能充分测试数据库和文件系统组件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-375">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="f94b4-376">使用单元测试对与这些组件进行交互的方法逻辑进行例程测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-376">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="f94b4-377">在单元测试中，使用基础结构 fakes/模拟会导致更快地执行测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-377">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="f94b4-378">在讨论集成测试时，测试的项目经常称为 "测试中的*系统*" 或简称 "SUT"。</span><span class="sxs-lookup"><span data-stu-id="f94b4-378">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="f94b4-379">*本主题中使用 "SUT" 来引用已测试的 ASP.NET Core 应用。*</span><span class="sxs-lookup"><span data-stu-id="f94b4-379">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="f94b4-380">ASP.NET Core 集成测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-380">ASP.NET Core integration tests</span></span>

<span data-ttu-id="f94b4-381">ASP.NET Core 中的集成测试需要以下各项：</span><span class="sxs-lookup"><span data-stu-id="f94b4-381">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="f94b4-382">测试项目用于包含和执行测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-382">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="f94b4-383">测试项目具有对 SUT 的引用。</span><span class="sxs-lookup"><span data-stu-id="f94b4-383">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="f94b4-384">测试项目将为 SUT 创建测试 web 主机，并使用测试服务器客户端来处理 SUT 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="f94b4-384">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="f94b4-385">测试运行程序用于执行测试并报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="f94b4-385">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="f94b4-386">集成测试遵循一系列事件，其中包括常见的*排列*、*操作*和*断言*测试步骤：</span><span class="sxs-lookup"><span data-stu-id="f94b4-386">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="f94b4-387">已配置 SUT 的 web 主机。</span><span class="sxs-lookup"><span data-stu-id="f94b4-387">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="f94b4-388">创建测试服务器客户端以向应用程序提交请求。</span><span class="sxs-lookup"><span data-stu-id="f94b4-388">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="f94b4-389">执行 "*排列*测试" 步骤：测试应用准备请求。</span><span class="sxs-lookup"><span data-stu-id="f94b4-389">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="f94b4-390">执行*Act*测试步骤：客户端提交请求并接收响应。</span><span class="sxs-lookup"><span data-stu-id="f94b4-390">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="f94b4-391">将*执行断言*测试步骤：根据*预期*的响应验证*实际*响应是*通过*还是*失败*。</span><span class="sxs-lookup"><span data-stu-id="f94b4-391">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="f94b4-392">此过程将一直继续，直到执行了所有测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-392">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="f94b4-393">将报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="f94b4-393">The test results are reported.</span></span>

<span data-ttu-id="f94b4-394">通常，测试 web 主机的配置与应用程序用于测试运行的普通 web 主机的配置不同。</span><span class="sxs-lookup"><span data-stu-id="f94b4-394">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="f94b4-395">例如，可以将不同的数据库或不同的应用设置用于测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-395">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="f94b4-396">基础结构组件（例如测试 web 主机和内存中测试服务器（[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)））由[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包提供或管理。</span><span class="sxs-lookup"><span data-stu-id="f94b4-396">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="f94b4-397">使用此包简化了测试的创建和执行。</span><span class="sxs-lookup"><span data-stu-id="f94b4-397">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="f94b4-398">`Microsoft.AspNetCore.Mvc.Testing` 包处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="f94b4-398">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="f94b4-399">将依赖项文件（ *. .deps.json*）从 SUT 复制到测试项目的*bin*目录中。</span><span class="sxs-lookup"><span data-stu-id="f94b4-399">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="f94b4-400">将[内容根](xref:fundamentals/index#content-root)设置为 SUT 的项目根，以便在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="f94b4-400">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="f94b4-401">提供[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)类，以简化与 `TestServer` 的 SUT 的引导。</span><span class="sxs-lookup"><span data-stu-id="f94b4-401">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="f94b4-402">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍了如何设置测试项目和测试运行程序，以及有关如何为测试和测试类命名测试和建议的详细说明。</span><span class="sxs-lookup"><span data-stu-id="f94b4-402">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="f94b4-403">为应用程序创建测试项目时，请将集成测试中的单元测试分成不同的项目。</span><span class="sxs-lookup"><span data-stu-id="f94b4-403">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="f94b4-404">这有助于确保不会意外地将基础结构测试组件包含在单元测试中。</span><span class="sxs-lookup"><span data-stu-id="f94b4-404">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="f94b4-405">单元测试和集成测试的隔离还允许控制运行的测试集。</span><span class="sxs-lookup"><span data-stu-id="f94b4-405">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="f94b4-406">Razor Pages 应用和 MVC 应用的测试的配置几乎没有任何区别。</span><span class="sxs-lookup"><span data-stu-id="f94b4-406">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="f94b4-407">唯一的区别在于测试的命名方式。</span><span class="sxs-lookup"><span data-stu-id="f94b4-407">The only difference is in how the tests are named.</span></span> <span data-ttu-id="f94b4-408">在 Razor Pages 应用中，页终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 来测试索引页的组件集成）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-408">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="f94b4-409">在 MVC 应用中，通常按控制器类对测试进行组织，并按它们所测试的控制器（例如 `HomeControllerTests` 来测试主控制器的组件集成）进行命名。</span><span class="sxs-lookup"><span data-stu-id="f94b4-409">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="f94b4-410">测试应用必备组件</span><span class="sxs-lookup"><span data-stu-id="f94b4-410">Test app prerequisites</span></span>

<span data-ttu-id="f94b4-411">测试项目必须：</span><span class="sxs-lookup"><span data-stu-id="f94b4-411">The test project must:</span></span>

* <span data-ttu-id="f94b4-412">引用以下包：</span><span class="sxs-lookup"><span data-stu-id="f94b4-412">Reference the following packages:</span></span>
  * [<span data-ttu-id="f94b4-413">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="f94b4-413">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="f94b4-414">AspNetCore。测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-414">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="f94b4-415">在项目文件中指定 Web SDK （`<Project Sdk="Microsoft.NET.Sdk.Web">`）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-415">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="f94b4-416">引用[AspNetCore 元包](xref:fundamentals/metapackage-app)时，需要 Web SDK。</span><span class="sxs-lookup"><span data-stu-id="f94b4-416">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="f94b4-417">可以在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-417">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="f94b4-418">检查 "*测试"/"RazorPagesProject"/"RazorPagesProject* " 文件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-418">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="f94b4-419">示例应用使用[xUnit](https://xunit.github.io/)测试框架和[AngleSharp](https://anglesharp.github.io/)分析器库，因此示例应用还引用：</span><span class="sxs-lookup"><span data-stu-id="f94b4-419">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="f94b4-420">xunit</span><span class="sxs-lookup"><span data-stu-id="f94b4-420">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="f94b4-421">xunit. visualstudio</span><span class="sxs-lookup"><span data-stu-id="f94b4-421">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="f94b4-422">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="f94b4-422">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="f94b4-423">SUT 环境</span><span class="sxs-lookup"><span data-stu-id="f94b4-423">SUT environment</span></span>

<span data-ttu-id="f94b4-424">如果未设置 SUT 的[环境](xref:fundamentals/environments)，环境将默认为 "开发"。</span><span class="sxs-lookup"><span data-stu-id="f94b4-424">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="f94b4-425">具有默认 WebApplicationFactory 的基本测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-425">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="f94b4-426">[WebApplicationFactory\<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用于创建集成测试的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。</span><span class="sxs-lookup"><span data-stu-id="f94b4-426">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="f94b4-427">`TEntryPoint` 是 SUT （通常是 `Startup` 类）的入口点类。</span><span class="sxs-lookup"><span data-stu-id="f94b4-427">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="f94b4-428">测试类实现*类装置*接口（[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)）以指示类包含测试，并跨类中的测试提供共享对象实例。</span><span class="sxs-lookup"><span data-stu-id="f94b4-428">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

### <a name="basic-test-of-app-endpoints"></a><span data-ttu-id="f94b4-429">应用终结点的基本测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-429">Basic test of app endpoints</span></span>

<span data-ttu-id="f94b4-430">下面的测试类 `BasicTests`使用 `WebApplicationFactory` 来启动 SUT，并为测试方法 `Get_EndpointsReturnSuccessAndCorrectContentType`提供[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="f94b4-430">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="f94b4-431">方法将检查响应状态代码是否成功（范围200-299 中的状态代码）和多个应用页面的 `Content-Type` 标头是否 `text/html; charset=utf-8`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-431">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="f94b4-432">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)创建自动跟随重定向并处理 cookie 的 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="f94b4-432">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="f94b4-433">默认情况下，当启用[GDPR 同意策略](xref:security/gdpr)时，不会跨请求保留非关键 cookie。</span><span class="sxs-lookup"><span data-stu-id="f94b4-433">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="f94b4-434">若要保留不重要的 cookie （如 TempData 提供程序使用的 cookie），请将它们标记为测试中的重要 cookie。</span><span class="sxs-lookup"><span data-stu-id="f94b4-434">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="f94b4-435">有关将 cookie 标记为必要的说明，请参阅[基本 cookie](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-435">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

### <a name="test-a-secure-endpoint"></a><span data-ttu-id="f94b4-436">测试安全终结点</span><span class="sxs-lookup"><span data-stu-id="f94b4-436">Test a secure endpoint</span></span>

<span data-ttu-id="f94b4-437">`BasicTests` 类中的另一个测试检查安全终结点是否将未经身份验证的用户重定向到应用程序的登录页。</span><span class="sxs-lookup"><span data-stu-id="f94b4-437">Another test in the `BasicTests` class checks that a secure endpoint redirects an unauthenticated user to the app's Login page.</span></span>

<span data-ttu-id="f94b4-438">在 SUT 中，`/SecurePage` 页面使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)约定将[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)应用到页面。</span><span class="sxs-lookup"><span data-stu-id="f94b4-438">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="f94b4-439">有关详细信息，请参阅[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-439">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="f94b4-440">在 `Get_SecurePageRequiresAnAuthenticatedUser` 测试中，通过将[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)设置为 `false`，将[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)设置为禁止重定向：</span><span class="sxs-lookup"><span data-stu-id="f94b4-440">In the `Get_SecurePageRequiresAnAuthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet2)]

<span data-ttu-id="f94b4-441">通过禁止客户端按照重定向操作，可以执行以下检查：</span><span class="sxs-lookup"><span data-stu-id="f94b4-441">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="f94b4-442">可以对照预期的[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)结果检查 SUT 返回的状态代码，而不是在重定向到登录页后返回最终状态代码，这会是[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-442">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="f94b4-443">将检查响应标头中的 `Location` 标头值，以确认该标头值以 `http://localhost/Identity/Account/Login`（而不是最终的登录页响应）开头，而 `Location` 标头不存在。</span><span class="sxs-lookup"><span data-stu-id="f94b4-443">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="f94b4-444">有关 `WebApplicationFactoryClientOptions` 的详细信息，请参阅[Client options](#client-options)部分。</span><span class="sxs-lookup"><span data-stu-id="f94b4-444">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="f94b4-445">自定义 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="f94b4-445">Customize WebApplicationFactory</span></span>

<span data-ttu-id="f94b4-446">通过继承 `WebApplicationFactory` 创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：</span><span class="sxs-lookup"><span data-stu-id="f94b4-446">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="f94b4-447">从 `WebApplicationFactory` 继承并重写[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-447">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="f94b4-448">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)允许通过[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)配置服务集合：</span><span class="sxs-lookup"><span data-stu-id="f94b4-448">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="f94b4-449">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。</span><span class="sxs-lookup"><span data-stu-id="f94b4-449">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="f94b4-450">[集成测试示例：测试应用组织](#test-app-organization)部分介绍了方法。</span><span class="sxs-lookup"><span data-stu-id="f94b4-450">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="f94b4-451">使用测试类中的自定义 `CustomWebApplicationFactory`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-451">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="f94b4-452">下面的示例使用 `IndexPageTests` 类中的工厂：</span><span class="sxs-lookup"><span data-stu-id="f94b4-452">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="f94b4-453">示例应用的客户端配置为阻止以下重定向中的 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-453">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="f94b4-454">如 "[测试安全终结点](#test-a-secure-endpoint)" 一节中所述，这允许测试检查应用程序的第一个响应的结果。</span><span class="sxs-lookup"><span data-stu-id="f94b4-454">As explained in the [Test a secure endpoint](#test-a-secure-endpoint) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="f94b4-455">第一个响应是包含 `Location` 标头的多个测试的重定向。</span><span class="sxs-lookup"><span data-stu-id="f94b4-455">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="f94b4-456">典型的测试使用 `HttpClient` 和 helper 方法来处理请求和响应：</span><span class="sxs-lookup"><span data-stu-id="f94b4-456">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="f94b4-457">对 SUT 的任何 POST 请求必须满足防伪检查，该检查是由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动完成的。</span><span class="sxs-lookup"><span data-stu-id="f94b4-457">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="f94b4-458">为了安排测试的 POST 请求，测试应用必须：</span><span class="sxs-lookup"><span data-stu-id="f94b4-458">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="f94b4-459">发出对页面的请求。</span><span class="sxs-lookup"><span data-stu-id="f94b4-459">Make a request for the page.</span></span>
1. <span data-ttu-id="f94b4-460">分析防伪 cookie 并请求响应中的验证令牌。</span><span class="sxs-lookup"><span data-stu-id="f94b4-460">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="f94b4-461">发出带有防伪 cookie 的 POST 请求，并就地请求验证令牌。</span><span class="sxs-lookup"><span data-stu-id="f94b4-461">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="f94b4-462">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 `SendAsync` helper 扩展方法（Helper */HttpClientExtensions*）和 `GetDocumentAsync` helper 方法（helper */HtmlHelpers*）使用[AngleSharp](https://anglesharp.github.io/)分析器来处理防伪检查以下方法：</span><span class="sxs-lookup"><span data-stu-id="f94b4-462">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="f94b4-463">`GetDocumentAsync` &ndash; 接收[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)并返回 `IHtmlDocument`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-463">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="f94b4-464">`GetDocumentAsync` 使用工厂来准备基于原始 `HttpResponseMessage` 的*虚拟响应*。</span><span class="sxs-lookup"><span data-stu-id="f94b4-464">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="f94b4-465">有关详细信息，请参阅[AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-465">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="f94b4-466">`SendAsync` 扩展方法 `HttpClient` 构成[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)并调用[SendAsync （HttpRequestMessage）](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="f94b4-466">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="f94b4-467">`SendAsync` 的重载接受 HTML 窗体（`IHtmlFormElement`）以及以下内容：</span><span class="sxs-lookup"><span data-stu-id="f94b4-467">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="f94b4-468">窗体的 "提交" 按钮（`IHtmlElement`）</span><span class="sxs-lookup"><span data-stu-id="f94b4-468">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="f94b4-469">窗体值集合（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="f94b4-469">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="f94b4-470">提交按钮（`IHtmlElement`）和窗体值（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="f94b4-470">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="f94b4-471">[AngleSharp](https://anglesharp.github.io/)是用于演示目的的第三方解析库，适用于本主题和示例应用。</span><span class="sxs-lookup"><span data-stu-id="f94b4-471">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="f94b4-472">ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="f94b4-472">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="f94b4-473">可以使用其他分析器，如[Html 灵活性包（HAP）](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-473">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="f94b4-474">另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。</span><span class="sxs-lookup"><span data-stu-id="f94b4-474">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="f94b4-475">用 WithWebHostBuilder 自定义客户端</span><span class="sxs-lookup"><span data-stu-id="f94b4-475">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="f94b4-476">如果在测试方法中需要其他配置， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)会创建一个新的 `WebApplicationFactory`，其中包含由配置进一步自定义的[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。</span><span class="sxs-lookup"><span data-stu-id="f94b4-476">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="f94b4-477">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 测试方法演示如何使用 `WithWebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-477">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="f94b4-478">此测试通过在 SUT 中触发窗体提交来在数据库中执行记录删除。</span><span class="sxs-lookup"><span data-stu-id="f94b4-478">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="f94b4-479">由于 `IndexPageTests` 类中的另一个测试会执行一个操作，该操作将删除数据库中的所有记录，并且该操作可能在 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 方法之前运行，因此，该数据库在此测试方法中种子，以确保要删除的 SUT 存在记录。</span><span class="sxs-lookup"><span data-stu-id="f94b4-479">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="f94b4-480">选择 SUT 中 `messages` 窗体的第一个 "删除" 按钮时，会将请求中的内容模拟到 SUT：</span><span class="sxs-lookup"><span data-stu-id="f94b4-480">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="f94b4-481">客户端选项</span><span class="sxs-lookup"><span data-stu-id="f94b4-481">Client options</span></span>

<span data-ttu-id="f94b4-482">下表显示了创建 `HttpClient` 实例时可用的默认[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 。</span><span class="sxs-lookup"><span data-stu-id="f94b4-482">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="f94b4-483">选项</span><span class="sxs-lookup"><span data-stu-id="f94b4-483">Option</span></span> | <span data-ttu-id="f94b4-484">描述</span><span class="sxs-lookup"><span data-stu-id="f94b4-484">Description</span></span> | <span data-ttu-id="f94b4-485">Default</span><span class="sxs-lookup"><span data-stu-id="f94b4-485">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="f94b4-486">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="f94b4-486">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="f94b4-487">获取或设置 `HttpClient` 实例是否应自动跟随重定向响应。</span><span class="sxs-lookup"><span data-stu-id="f94b4-487">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="f94b4-488">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="f94b4-488">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="f94b4-489">获取或设置 `HttpClient` 实例的基址。</span><span class="sxs-lookup"><span data-stu-id="f94b4-489">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="f94b4-490">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="f94b4-490">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="f94b4-491">获取或设置 `HttpClient` 实例是否应处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="f94b4-491">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="f94b4-492">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="f94b4-492">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="f94b4-493">获取或设置 `HttpClient` 实例应遵循的重定向响应的最大数目。</span><span class="sxs-lookup"><span data-stu-id="f94b4-493">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="f94b4-494">7</span><span class="sxs-lookup"><span data-stu-id="f94b4-494">7</span></span> |

<span data-ttu-id="f94b4-495">创建 `WebApplicationFactoryClientOptions` 类并将其传递给[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)方法（在代码示例中显示默认值）：</span><span class="sxs-lookup"><span data-stu-id="f94b4-495">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="f94b4-496">注入模拟服务</span><span class="sxs-lookup"><span data-stu-id="f94b4-496">Inject mock services</span></span>

<span data-ttu-id="f94b4-497">通过在主机生成器上调用[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，可以在测试中覆盖服务。</span><span class="sxs-lookup"><span data-stu-id="f94b4-497">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="f94b4-498">**若要注入模拟服务，SUT 必须有一个具有 `Startup.ConfigureServices` 方法的 `Startup` 类。**</span><span class="sxs-lookup"><span data-stu-id="f94b4-498">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="f94b4-499">示例 SUT 包含一个返回 quote 的作用域服务。</span><span class="sxs-lookup"><span data-stu-id="f94b4-499">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="f94b4-500">请求索引页时，引号嵌入到索引页上的隐藏字段中。</span><span class="sxs-lookup"><span data-stu-id="f94b4-500">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="f94b4-501">*服务/IQuoteService*：</span><span class="sxs-lookup"><span data-stu-id="f94b4-501">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="f94b4-502">*服务/QuoteService*：</span><span class="sxs-lookup"><span data-stu-id="f94b4-502">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="f94b4-503">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="f94b4-503">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="f94b4-504">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="f94b4-504">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="f94b4-505">*Pages/Index. cs*：</span><span class="sxs-lookup"><span data-stu-id="f94b4-505">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="f94b4-506">运行 SUT 应用时，将生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="f94b4-506">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="f94b4-507">若要在集成测试中测试服务和引号注入，测试会将模拟服务注入到 SUT。</span><span class="sxs-lookup"><span data-stu-id="f94b4-507">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="f94b4-508">模拟服务会将应用的 `QuoteService` 替换为测试应用提供的服务，称为 `TestQuoteService`：</span><span class="sxs-lookup"><span data-stu-id="f94b4-508">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="f94b4-509">*IntegrationTests.IndexPageTests.cs*：</span><span class="sxs-lookup"><span data-stu-id="f94b4-509">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="f94b4-510">调用 `ConfigureTestServices`，并注册作用域内服务：</span><span class="sxs-lookup"><span data-stu-id="f94b4-510">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="f94b4-511">在测试执行过程中生成的标记反映 `TestQuoteService`提供的引号文本，因此断言通过：</span><span class="sxs-lookup"><span data-stu-id="f94b4-511">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="f94b4-512">测试基础结构如何推断应用内容根路径</span><span class="sxs-lookup"><span data-stu-id="f94b4-512">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="f94b4-513">`WebApplicationFactory` 构造函数通过使用等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName`的键搜索包含集成测试的程序集上的[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)来推断应用[内容根](xref:fundamentals/index#content-root)路径。</span><span class="sxs-lookup"><span data-stu-id="f94b4-513">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="f94b4-514">如果找不到具有正确键的属性，`WebApplicationFactory` 将回退到搜索解决方案文件（ *.sln*），并在解决方案目录中追加 `TEntryPoint` 程序集名称。</span><span class="sxs-lookup"><span data-stu-id="f94b4-514">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="f94b4-515">应用根目录（内容根路径）用于发现视图和内容文件。</span><span class="sxs-lookup"><span data-stu-id="f94b4-515">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="f94b4-516">禁用卷影复制</span><span class="sxs-lookup"><span data-stu-id="f94b4-516">Disable shadow copying</span></span>

<span data-ttu-id="f94b4-517">卷影复制会导致在输出目录以外的目录中执行测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-517">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="f94b4-518">要使测试正常工作，必须禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="f94b4-518">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="f94b4-519">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的*xUnit*文件来禁用 xUnit 的卷影复制。</span><span class="sxs-lookup"><span data-stu-id="f94b4-519">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="f94b4-520">有关详细信息，请参阅[配置 xUnit 与 JSON](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-520">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="f94b4-521">将*xunit*文件添加到测试项目的根，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="f94b4-521">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="f94b4-522">如果使用的是 Visual Studio，请将文件的 "**复制到输出目录**" 属性设置为 "**始终复制**"。</span><span class="sxs-lookup"><span data-stu-id="f94b4-522">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="f94b4-523">如果不使用 Visual Studio，请将 `Content` 目标添加到测试应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="f94b4-523">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="f94b4-524">对象的处理</span><span class="sxs-lookup"><span data-stu-id="f94b4-524">Disposal of objects</span></span>

<span data-ttu-id="f94b4-525">执行 `IClassFixture` 实现的测试后，当 xUnit 释放[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)时，将释放[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="f94b4-525">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="f94b4-526">如果开发人员实例化的对象需要处置，请在 `IClassFixture` 实现中释放这些对象。</span><span class="sxs-lookup"><span data-stu-id="f94b4-526">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="f94b4-527">有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-527">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="f94b4-528">集成测试示例</span><span class="sxs-lookup"><span data-stu-id="f94b4-528">Integration tests sample</span></span>

<span data-ttu-id="f94b4-529">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由两个应用组成：</span><span class="sxs-lookup"><span data-stu-id="f94b4-529">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="f94b4-530">应用</span><span class="sxs-lookup"><span data-stu-id="f94b4-530">App</span></span> | <span data-ttu-id="f94b4-531">项目目录</span><span class="sxs-lookup"><span data-stu-id="f94b4-531">Project directory</span></span> | <span data-ttu-id="f94b4-532">描述</span><span class="sxs-lookup"><span data-stu-id="f94b4-532">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="f94b4-533">消息应用（SUT）</span><span class="sxs-lookup"><span data-stu-id="f94b4-533">Message app (the SUT)</span></span> | <span data-ttu-id="f94b4-534">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="f94b4-534">*src/RazorPagesProject*</span></span> | <span data-ttu-id="f94b4-535">允许用户添加、删除一个、删除和分析消息。</span><span class="sxs-lookup"><span data-stu-id="f94b4-535">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="f94b4-536">测试应用</span><span class="sxs-lookup"><span data-stu-id="f94b4-536">Test app</span></span> | <span data-ttu-id="f94b4-537">*测试/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="f94b4-537">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="f94b4-538">用于集成测试 SUT。</span><span class="sxs-lookup"><span data-stu-id="f94b4-538">Used to integration test the SUT.</span></span> |

<span data-ttu-id="f94b4-539">可以使用 IDE （如[Visual Studio](https://visualstudio.microsoft.com)）的内置测试功能来运行测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-539">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="f94b4-540">如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在*RazorPagesProject 目录中的命令*提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="f94b4-540">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="f94b4-541">消息应用（SUT）组织</span><span class="sxs-lookup"><span data-stu-id="f94b4-541">Message app (SUT) organization</span></span>

<span data-ttu-id="f94b4-542">SUT 是 Razor Pages 的消息系统，具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="f94b4-542">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="f94b4-543">应用的 "索引" 页（*Pages/索引. cshtml*和*pages/index*）提供了一个 UI 和页面模型方法，用于控制消息的添加、删除和分析（每条消息的平均单词数）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-543">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="f94b4-544">消息由 `Message` 类（*Data/message*）描述，具有两个属性： `Id` （密钥）和 `Text` （message）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-544">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="f94b4-545">`Text` 属性是必需的，并且限制为200个字符。</span><span class="sxs-lookup"><span data-stu-id="f94b4-545">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="f94b4-546">使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。</span><span class="sxs-lookup"><span data-stu-id="f94b4-546">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="f94b4-547">应用在其数据库上下文类中包含数据访问层（DAL），`AppDbContext` （*data/AppDbContext*）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-547">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="f94b4-548">如果数据库在应用启动时为空，则会用三条消息初始化消息存储。</span><span class="sxs-lookup"><span data-stu-id="f94b4-548">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="f94b4-549">应用包括只能由经过身份验证的用户访问的 `/SecurePage`。</span><span class="sxs-lookup"><span data-stu-id="f94b4-549">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="f94b4-550">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-550">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="f94b4-551">本主题使用[xUnit](https://xunit.github.io/)测试框架。</span><span class="sxs-lookup"><span data-stu-id="f94b4-551">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="f94b4-552">不同测试框架中的测试概念和测试实现相似，但并不完全相同。</span><span class="sxs-lookup"><span data-stu-id="f94b4-552">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="f94b4-553">尽管应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。</span><span class="sxs-lookup"><span data-stu-id="f94b4-553">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="f94b4-554">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="f94b4-554">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="f94b4-555">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="f94b4-555">Test app organization</span></span>

<span data-ttu-id="f94b4-556">测试应用是 "*测试"/"RazorPagesProject* " 目录中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="f94b4-556">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="f94b4-557">测试应用程序目录</span><span class="sxs-lookup"><span data-stu-id="f94b4-557">Test app directory</span></span> | <span data-ttu-id="f94b4-558">描述</span><span class="sxs-lookup"><span data-stu-id="f94b4-558">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="f94b4-559">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="f94b4-559">*BasicTests*</span></span> | <span data-ttu-id="f94b4-560">*BasicTests.cs*包含用于路由的测试方法、通过未经身份验证的用户访问安全页以及获取 GitHub 用户配置文件和检查配置文件的用户登录名。</span><span class="sxs-lookup"><span data-stu-id="f94b4-560">*BasicTests.cs* contains test methods for routing, accessing a secure page by an unauthenticated user, and obtaining a GitHub user profile and checking the profile's user login.</span></span> |
| <span data-ttu-id="f94b4-561">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="f94b4-561">*IntegrationTests*</span></span> | <span data-ttu-id="f94b4-562">*IndexPageTests.cs*包含使用自定义 `WebApplicationFactory` 类的索引页的集成测试。</span><span class="sxs-lookup"><span data-stu-id="f94b4-562">*IndexPageTests.cs* contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="f94b4-563">*帮助程序/实用工具*</span><span class="sxs-lookup"><span data-stu-id="f94b4-563">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="f94b4-564">*Utilities.cs*包含用于使用测试数据对数据库进行种子设定的 `InitializeDbForTests` 方法。</span><span class="sxs-lookup"><span data-stu-id="f94b4-564">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="f94b4-565">*HtmlHelpers.cs*提供了一个方法，用于返回 AngleSharp `IHtmlDocument` 以供测试方法使用。</span><span class="sxs-lookup"><span data-stu-id="f94b4-565">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="f94b4-566">*HttpClientExtensions.cs*提供 `SendAsync` 的重载，以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="f94b4-566">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="f94b4-567">测试框架为[xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-567">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="f94b4-568">集成测试是使用 TestHost 的[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)进行的，其中包括[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="f94b4-568">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="f94b4-569">由于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包用于配置测试主机和测试服务器，因此，在测试应用程序的项目文件或测试的开发人员配置中，`TestHost` 和 `TestServer` 包不需要直接包引用应用.</span><span class="sxs-lookup"><span data-stu-id="f94b4-569">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="f94b4-570">**播种要测试的数据库**</span><span class="sxs-lookup"><span data-stu-id="f94b4-570">**Seeding the database for testing**</span></span>

<span data-ttu-id="f94b4-571">集成测试在执行测试前通常需要数据库中的一个小型数据集。</span><span class="sxs-lookup"><span data-stu-id="f94b4-571">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="f94b4-572">例如，删除测试调用数据库记录，因此数据库必须至少有一条记录，删除请求才能成功。</span><span class="sxs-lookup"><span data-stu-id="f94b4-572">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="f94b4-573">该示例应用在*Utilities.cs*中使用三个消息对数据库进行种子设定，当测试执行时，可以使用这些消息：</span><span class="sxs-lookup"><span data-stu-id="f94b4-573">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="f94b4-574">其他资源</span><span class="sxs-lookup"><span data-stu-id="f94b4-574">Additional resources</span></span>

* [<span data-ttu-id="f94b4-575">单元测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-575">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* [<span data-ttu-id="f94b4-576">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="f94b4-576">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)
* [<span data-ttu-id="f94b4-577">中间件</span><span class="sxs-lookup"><span data-stu-id="f94b4-577">Middleware</span></span>](xref:fundamentals/middleware/index)
* [<span data-ttu-id="f94b4-578">测试控制器</span><span class="sxs-lookup"><span data-stu-id="f94b4-578">Test controllers</span></span>](xref:mvc/controllers/testing)
