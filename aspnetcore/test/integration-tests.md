---
title: ASP.NET Core 中的集成测试
author: guardrex
description: 了解集成测试如何在基础结构级别（包括数据库、文件系统和网络）确保应用组件功能正常。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/28/2019
uid: test/integration-tests
ms.openlocfilehash: 33f3e29bc649fa65efdff0c47e54a83662005577
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143361"
---
# <a name="integration-tests-in-aspnet-core"></a><span data-ttu-id="16091-103">ASP.NET Core 中的集成测试</span><span class="sxs-lookup"><span data-stu-id="16091-103">Integration tests in ASP.NET Core</span></span>

<span data-ttu-id="16091-104">作者： [Luke Latham](https://github.com/guardrex)、 [Javier Calvarro 使用](https://github.com/javiercn)、 [Steve Smith](https://ardalis.com/)和[Jos van der 等到](https://jvandertil.nl)</span><span class="sxs-lookup"><span data-stu-id="16091-104">By [Luke Latham](https://github.com/guardrex), [Javier Calvarro Nelson](https://github.com/javiercn), [Steve Smith](https://ardalis.com/), and [Jos van der Til](https://jvandertil.nl)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="16091-105">集成测试可确保应用程序的组件在包含应用程序支持的基础结构的级别（例如数据库、文件系统和网络）正常运行。</span><span class="sxs-lookup"><span data-stu-id="16091-105">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="16091-106">ASP.NET Core 支持结合使用单元测试框架和测试 web 主机和内存中测试服务器的集成测试。</span><span class="sxs-lookup"><span data-stu-id="16091-106">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="16091-107">本主题假定基本了解单元测试。</span><span class="sxs-lookup"><span data-stu-id="16091-107">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="16091-108">如果不熟悉测试概念，请参阅[.Net Core 中的单元测试和 .NET Standard](/dotnet/core/testing/)主题及其链接的内容。</span><span class="sxs-lookup"><span data-stu-id="16091-108">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="16091-109">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="16091-109">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="16091-110">该示例应用是 Razor Pages 应用程序，并假定基本了解 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="16091-110">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="16091-111">如果不熟悉 Razor Pages，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="16091-111">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="16091-112">Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="16091-112">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="16091-113">Razor 页面入门</span><span class="sxs-lookup"><span data-stu-id="16091-113">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="16091-114">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="16091-114">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="16091-115">对于测试 Spa，我们建议使用[Selenium](https://www.seleniumhq.org/)这样的工具，它可以自动执行浏览器。</span><span class="sxs-lookup"><span data-stu-id="16091-115">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="16091-116">集成测试简介</span><span class="sxs-lookup"><span data-stu-id="16091-116">Introduction to integration tests</span></span>

<span data-ttu-id="16091-117">与[单元测试](/dotnet/core/testing/)相比，集成测试在更广泛的级别上评估应用的组件。</span><span class="sxs-lookup"><span data-stu-id="16091-117">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="16091-118">单元测试用于测试独立的软件组件，如单独的类方法。</span><span class="sxs-lookup"><span data-stu-id="16091-118">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="16091-119">集成测试确认两个或更多应用程序组件一起工作以生成预期的结果，其中可能包括完全处理请求所需的每个组件。</span><span class="sxs-lookup"><span data-stu-id="16091-119">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="16091-120">这些广泛的测试用于测试应用程序的基础结构和整个框架，通常包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="16091-120">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="16091-121">数据库</span><span class="sxs-lookup"><span data-stu-id="16091-121">Database</span></span>
* <span data-ttu-id="16091-122">文件系统</span><span class="sxs-lookup"><span data-stu-id="16091-122">File system</span></span>
* <span data-ttu-id="16091-123">网络设备</span><span class="sxs-lookup"><span data-stu-id="16091-123">Network appliances</span></span>
* <span data-ttu-id="16091-124">请求-响应管道</span><span class="sxs-lookup"><span data-stu-id="16091-124">Request-response pipeline</span></span>

<span data-ttu-id="16091-125">单元测试使用称为*fakes*或*mock 对象*的制造组件来代替基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="16091-125">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="16091-126">与单元测试相比，集成测试：</span><span class="sxs-lookup"><span data-stu-id="16091-126">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="16091-127">使用应用在生产环境中使用的实际组件。</span><span class="sxs-lookup"><span data-stu-id="16091-127">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="16091-128">需要进行更多的代码和数据处理。</span><span class="sxs-lookup"><span data-stu-id="16091-128">Require more code and data processing.</span></span>
* <span data-ttu-id="16091-129">需要较长时间才能运行。</span><span class="sxs-lookup"><span data-stu-id="16091-129">Take longer to run.</span></span>

<span data-ttu-id="16091-130">因此，将集成测试的使用限制为最重要的基础结构方案。</span><span class="sxs-lookup"><span data-stu-id="16091-130">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="16091-131">如果可以使用单元测试或集成测试来测试行为，请选择单元测试。</span><span class="sxs-lookup"><span data-stu-id="16091-131">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="16091-132">请勿为数据库和文件系统的每个可能的数据排列和文件访问编写集成测试。</span><span class="sxs-lookup"><span data-stu-id="16091-132">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="16091-133">无论应用程序中有多少位置与数据库和文件系统交互，一组集中式的读取、写入、更新和删除集成测试通常都能充分测试数据库和文件系统组件。</span><span class="sxs-lookup"><span data-stu-id="16091-133">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="16091-134">使用单元测试对与这些组件进行交互的方法逻辑进行例程测试。</span><span class="sxs-lookup"><span data-stu-id="16091-134">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="16091-135">在单元测试中，使用基础结构 fakes/模拟会导致更快地执行测试。</span><span class="sxs-lookup"><span data-stu-id="16091-135">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="16091-136">在讨论集成测试时，测试的项目经常称为 "测试中的*系统*" 或简称 "SUT"。</span><span class="sxs-lookup"><span data-stu-id="16091-136">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="16091-137">*本主题中使用 "SUT" 来引用已测试的 ASP.NET Core 应用。*</span><span class="sxs-lookup"><span data-stu-id="16091-137">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="16091-138">ASP.NET Core 集成测试</span><span class="sxs-lookup"><span data-stu-id="16091-138">ASP.NET Core integration tests</span></span>

<span data-ttu-id="16091-139">ASP.NET Core 中的集成测试需要以下各项：</span><span class="sxs-lookup"><span data-stu-id="16091-139">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="16091-140">测试项目用于包含和执行测试。</span><span class="sxs-lookup"><span data-stu-id="16091-140">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="16091-141">测试项目具有对 SUT 的引用。</span><span class="sxs-lookup"><span data-stu-id="16091-141">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="16091-142">测试项目将为 SUT 创建测试 web 主机，并使用测试服务器客户端来处理 SUT 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="16091-142">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="16091-143">测试运行程序用于执行测试并报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="16091-143">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="16091-144">集成测试遵循一系列事件，其中包括常见的*排列*、*操作*和*断言*测试步骤：</span><span class="sxs-lookup"><span data-stu-id="16091-144">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="16091-145">已配置 SUT 的 web 主机。</span><span class="sxs-lookup"><span data-stu-id="16091-145">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="16091-146">创建测试服务器客户端以向应用程序提交请求。</span><span class="sxs-lookup"><span data-stu-id="16091-146">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="16091-147">执行 "*排列*测试" 步骤：测试应用准备请求。</span><span class="sxs-lookup"><span data-stu-id="16091-147">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="16091-148">执行*Act*测试步骤：客户端提交请求并接收响应。</span><span class="sxs-lookup"><span data-stu-id="16091-148">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="16091-149">将*执行断言*测试步骤：根据*预期*的响应验证*实际*响应是*通过*还是*失败*。</span><span class="sxs-lookup"><span data-stu-id="16091-149">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="16091-150">此过程将一直继续，直到执行了所有测试。</span><span class="sxs-lookup"><span data-stu-id="16091-150">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="16091-151">将报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="16091-151">The test results are reported.</span></span>

<span data-ttu-id="16091-152">通常，测试 web 主机的配置与应用程序用于测试运行的普通 web 主机的配置不同。</span><span class="sxs-lookup"><span data-stu-id="16091-152">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="16091-153">例如，可以将不同的数据库或不同的应用设置用于测试。</span><span class="sxs-lookup"><span data-stu-id="16091-153">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="16091-154">基础结构组件（例如测试 web 主机和内存中测试服务器（[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)））由[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包提供或管理。</span><span class="sxs-lookup"><span data-stu-id="16091-154">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="16091-155">使用此包简化了测试的创建和执行。</span><span class="sxs-lookup"><span data-stu-id="16091-155">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="16091-156">`Microsoft.AspNetCore.Mvc.Testing` 包处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="16091-156">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="16091-157">将依赖项文件（ *. .deps.json*）从 SUT 复制到测试项目的*bin*目录中。</span><span class="sxs-lookup"><span data-stu-id="16091-157">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="16091-158">将[内容根](xref:fundamentals/index#content-root)设置为 SUT 的项目根，以便在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="16091-158">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="16091-159">提供[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)类，以简化与 `TestServer` 的 SUT 的引导。</span><span class="sxs-lookup"><span data-stu-id="16091-159">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="16091-160">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍了如何设置测试项目和测试运行程序，以及有关如何为测试和测试类命名测试和建议的详细说明。</span><span class="sxs-lookup"><span data-stu-id="16091-160">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="16091-161">为应用程序创建测试项目时，请将集成测试中的单元测试分成不同的项目。</span><span class="sxs-lookup"><span data-stu-id="16091-161">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="16091-162">这有助于确保不会意外地将基础结构测试组件包含在单元测试中。</span><span class="sxs-lookup"><span data-stu-id="16091-162">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="16091-163">单元测试和集成测试的隔离还允许控制运行的测试集。</span><span class="sxs-lookup"><span data-stu-id="16091-163">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="16091-164">Razor Pages 应用和 MVC 应用的测试的配置几乎没有任何区别。</span><span class="sxs-lookup"><span data-stu-id="16091-164">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="16091-165">唯一的区别在于测试的命名方式。</span><span class="sxs-lookup"><span data-stu-id="16091-165">The only difference is in how the tests are named.</span></span> <span data-ttu-id="16091-166">在 Razor Pages 应用中，页终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 来测试索引页的组件集成）。</span><span class="sxs-lookup"><span data-stu-id="16091-166">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="16091-167">在 MVC 应用中，通常按控制器类对测试进行组织，并按它们所测试的控制器（例如 `HomeControllerTests` 来测试主控制器的组件集成）进行命名。</span><span class="sxs-lookup"><span data-stu-id="16091-167">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="16091-168">测试应用必备组件</span><span class="sxs-lookup"><span data-stu-id="16091-168">Test app prerequisites</span></span>

<span data-ttu-id="16091-169">测试项目必须：</span><span class="sxs-lookup"><span data-stu-id="16091-169">The test project must:</span></span>

* <span data-ttu-id="16091-170">请参阅[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包。</span><span class="sxs-lookup"><span data-stu-id="16091-170">Reference the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span>
* <span data-ttu-id="16091-171">在项目文件中指定 Web SDK （`<Project Sdk="Microsoft.NET.Sdk.Web">`）。</span><span class="sxs-lookup"><span data-stu-id="16091-171">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span>

<span data-ttu-id="16091-172">可以在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。</span><span class="sxs-lookup"><span data-stu-id="16091-172">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="16091-173">检查 "*测试"/"RazorPagesProject"/"RazorPagesProject* " 文件。</span><span class="sxs-lookup"><span data-stu-id="16091-173">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="16091-174">示例应用使用[xUnit](https://xunit.github.io/)测试框架和[AngleSharp](https://anglesharp.github.io/)分析器库，因此示例应用还引用：</span><span class="sxs-lookup"><span data-stu-id="16091-174">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="16091-175">xunit</span><span class="sxs-lookup"><span data-stu-id="16091-175">xunit</span></span>](https://www.nuget.org/packages/xunit)
* [<span data-ttu-id="16091-176">xunit. visualstudio</span><span class="sxs-lookup"><span data-stu-id="16091-176">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio)
* [<span data-ttu-id="16091-177">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="16091-177">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp)

<span data-ttu-id="16091-178">还可在测试中使用 Entity Framework Core。</span><span class="sxs-lookup"><span data-stu-id="16091-178">Entity Framework Core is also used in the tests.</span></span> <span data-ttu-id="16091-179">应用引用：</span><span class="sxs-lookup"><span data-stu-id="16091-179">The app references:</span></span>

* [<span data-ttu-id="16091-180">AspNetCore. Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="16091-180">Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore)
* [<span data-ttu-id="16091-181">AspNetCore. Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="16091-181">Microsoft.AspNetCore.Identity.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.EntityFrameworkCore)
* [<span data-ttu-id="16091-182">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="16091-182">Microsoft.EntityFrameworkCore</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
* [<span data-ttu-id="16091-183">Microsoft.EntityFrameworkCore.InMemory</span><span class="sxs-lookup"><span data-stu-id="16091-183">Microsoft.EntityFrameworkCore.InMemory</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.InMemory)
* [<span data-ttu-id="16091-184">Microsoft.entityframeworkcore</span><span class="sxs-lookup"><span data-stu-id="16091-184">Microsoft.EntityFrameworkCore.Tools</span></span>](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.Tools)

## <a name="sut-environment"></a><span data-ttu-id="16091-185">SUT 环境</span><span class="sxs-lookup"><span data-stu-id="16091-185">SUT environment</span></span>

<span data-ttu-id="16091-186">如果未设置 SUT 的[环境](xref:fundamentals/environments)，环境将默认为 "开发"。</span><span class="sxs-lookup"><span data-stu-id="16091-186">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="16091-187">具有默认 WebApplicationFactory 的基本测试</span><span class="sxs-lookup"><span data-stu-id="16091-187">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="16091-188">[WebApplicationFactory\<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用于创建集成测试的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。</span><span class="sxs-lookup"><span data-stu-id="16091-188">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="16091-189">`TEntryPoint` 是 SUT （通常是 `Startup` 类）的入口点类。</span><span class="sxs-lookup"><span data-stu-id="16091-189">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="16091-190">测试类实现*类装置*接口（[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)）以指示类包含测试，并跨类中的测试提供共享对象实例。</span><span class="sxs-lookup"><span data-stu-id="16091-190">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="16091-191">下面的测试类 `BasicTests`使用 `WebApplicationFactory` 来启动 SUT，并为测试方法 `Get_EndpointsReturnSuccessAndCorrectContentType`提供[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="16091-191">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="16091-192">方法将检查响应状态代码是否成功（范围200-299 中的状态代码）和多个应用页面的 `Content-Type` 标头是否 `text/html; charset=utf-8`。</span><span class="sxs-lookup"><span data-stu-id="16091-192">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="16091-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)创建自动跟随重定向并处理 cookie 的 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="16091-193">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="16091-194">默认情况下，当启用[GDPR 同意策略](xref:security/gdpr)时，不会跨请求保留非关键 cookie。</span><span class="sxs-lookup"><span data-stu-id="16091-194">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="16091-195">若要保留不重要的 cookie （如 TempData 提供程序使用的 cookie），请将它们标记为测试中的重要 cookie。</span><span class="sxs-lookup"><span data-stu-id="16091-195">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="16091-196">有关将 cookie 标记为必要的说明，请参阅[基本 cookie](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="16091-196">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="16091-197">自定义 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="16091-197">Customize WebApplicationFactory</span></span>

<span data-ttu-id="16091-198">通过继承 `WebApplicationFactory` 创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：</span><span class="sxs-lookup"><span data-stu-id="16091-198">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="16091-199">从 `WebApplicationFactory` 继承并重写[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="16091-199">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="16091-200">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)允许通过[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)配置服务集合：</span><span class="sxs-lookup"><span data-stu-id="16091-200">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="16091-201">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。</span><span class="sxs-lookup"><span data-stu-id="16091-201">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="16091-202">[集成测试示例：测试应用组织](#test-app-organization)部分介绍了方法。</span><span class="sxs-lookup"><span data-stu-id="16091-202">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

   <span data-ttu-id="16091-203">在其 `Startup.ConfigureServices` 方法中注册了 SUT 的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="16091-203">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="16091-204">执行应用的 `Startup.ConfigureServices` 代码*后*，将执行测试应用的 `builder.ConfigureServices` 回调。</span><span class="sxs-lookup"><span data-stu-id="16091-204">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="16091-205">若要将不同于应用程序的数据库用于测试，则必须将应用程序的数据库上下文替换 `builder.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="16091-205">To use a different database for the tests than the app's database, the app's database context must be replaced in `builder.ConfigureServices`.</span></span>

   <span data-ttu-id="16091-206">示例应用将查找数据库上下文的服务描述符，并使用描述符来删除服务注册。</span><span class="sxs-lookup"><span data-stu-id="16091-206">The sample app finds the service descriptor for the database context and uses the descriptor to remove the service registration.</span></span> <span data-ttu-id="16091-207">接下来，工厂添加新的 `ApplicationDbContext`，使用内存中数据库进行测试。</span><span class="sxs-lookup"><span data-stu-id="16091-207">Next, the factory adds a new `ApplicationDbContext` that uses an in-memory database for the tests.</span></span>

   <span data-ttu-id="16091-208">若要连接到与内存中数据库不同的数据库，请更改 `UseInMemoryDatabase` 调用以将上下文连接到其他数据库。</span><span class="sxs-lookup"><span data-stu-id="16091-208">To connect to a different database than the in-memory database, change the `UseInMemoryDatabase` call to connect the context to a different database.</span></span> <span data-ttu-id="16091-209">使用 SQL Server 测试数据库：</span><span class="sxs-lookup"><span data-stu-id="16091-209">To use a SQL Server test database:</span></span>

   * <span data-ttu-id="16091-210">引用项目文件中的[Microsoft.entityframeworkcore](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="16091-210">Reference the [Microsoft.EntityFrameworkCore.SqlServer](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore.SqlServer/) NuGet package in the project file.</span></span>
   * <span data-ttu-id="16091-211">使用数据库的连接字符串调用 `UseSqlServer`。</span><span class="sxs-lookup"><span data-stu-id="16091-211">Call `UseSqlServer` with a connection string to the database.</span></span>

   ```csharp
   services.AddDbContext<ApplicationDbContext>((options, context) => 
   {
       context.UseSqlServer(
           Configuration.GetConnectionString("TestingDbConnectionString"));
   });
   ```

2. <span data-ttu-id="16091-212">使用测试类中的自定义 `CustomWebApplicationFactory`。</span><span class="sxs-lookup"><span data-stu-id="16091-212">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="16091-213">下面的示例使用 `IndexPageTests` 类中的工厂：</span><span class="sxs-lookup"><span data-stu-id="16091-213">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="16091-214">示例应用的客户端配置为阻止以下重定向中的 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="16091-214">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="16091-215">如稍后在 "[模拟身份验证](#mock-authentication)" 部分中所述，这允许测试检查应用程序的第一个响应的结果。</span><span class="sxs-lookup"><span data-stu-id="16091-215">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="16091-216">第一个响应是包含 `Location` 标头的多个测试的重定向。</span><span class="sxs-lookup"><span data-stu-id="16091-216">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="16091-217">典型的测试使用 `HttpClient` 和 helper 方法来处理请求和响应：</span><span class="sxs-lookup"><span data-stu-id="16091-217">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="16091-218">对 SUT 的任何 POST 请求必须满足防伪检查，该检查是由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动完成的。</span><span class="sxs-lookup"><span data-stu-id="16091-218">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="16091-219">为了安排测试的 POST 请求，测试应用必须：</span><span class="sxs-lookup"><span data-stu-id="16091-219">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="16091-220">发出对页面的请求。</span><span class="sxs-lookup"><span data-stu-id="16091-220">Make a request for the page.</span></span>
1. <span data-ttu-id="16091-221">分析防伪 cookie 并请求响应中的验证令牌。</span><span class="sxs-lookup"><span data-stu-id="16091-221">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="16091-222">发出带有防伪 cookie 的 POST 请求，并就地请求验证令牌。</span><span class="sxs-lookup"><span data-stu-id="16091-222">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="16091-223">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 `SendAsync` helper 扩展方法（Helper */HttpClientExtensions*）和 `GetDocumentAsync` helper 方法（helper */HtmlHelpers*）使用[AngleSharp](https://anglesharp.github.io/)分析器来处理防伪检查以下方法：</span><span class="sxs-lookup"><span data-stu-id="16091-223">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="16091-224">`GetDocumentAsync` &ndash; 接收[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)并返回 `IHtmlDocument`。</span><span class="sxs-lookup"><span data-stu-id="16091-224">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="16091-225">`GetDocumentAsync` 使用工厂来准备基于原始 `HttpResponseMessage` 的*虚拟响应*。</span><span class="sxs-lookup"><span data-stu-id="16091-225">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="16091-226">有关详细信息，请参阅[AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="16091-226">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="16091-227">`SendAsync` 扩展方法 `HttpClient` 构成[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)并调用[SendAsync （HttpRequestMessage）](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="16091-227">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="16091-228">`SendAsync` 的重载接受 HTML 窗体（`IHtmlFormElement`）以及以下内容：</span><span class="sxs-lookup"><span data-stu-id="16091-228">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="16091-229">窗体的 "提交" 按钮（`IHtmlElement`）</span><span class="sxs-lookup"><span data-stu-id="16091-229">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="16091-230">窗体值集合（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="16091-230">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="16091-231">提交按钮（`IHtmlElement`）和窗体值（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="16091-231">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="16091-232">[AngleSharp](https://anglesharp.github.io/)是用于演示目的的第三方解析库，适用于本主题和示例应用。</span><span class="sxs-lookup"><span data-stu-id="16091-232">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="16091-233">ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="16091-233">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="16091-234">可以使用其他分析器，如[Html 灵活性包（HAP）](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="16091-234">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="16091-235">另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。</span><span class="sxs-lookup"><span data-stu-id="16091-235">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="16091-236">用 WithWebHostBuilder 自定义客户端</span><span class="sxs-lookup"><span data-stu-id="16091-236">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="16091-237">如果在测试方法中需要其他配置， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)会创建一个新的 `WebApplicationFactory`，其中包含由配置进一步自定义的[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。</span><span class="sxs-lookup"><span data-stu-id="16091-237">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="16091-238">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 测试方法演示如何使用 `WithWebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="16091-238">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="16091-239">此测试通过在 SUT 中触发窗体提交来在数据库中执行记录删除。</span><span class="sxs-lookup"><span data-stu-id="16091-239">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="16091-240">由于 `IndexPageTests` 类中的另一个测试会执行一个操作，该操作将删除数据库中的所有记录，并且该操作可能在 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 方法之前运行，因此，该数据库在此测试方法中种子，以确保要删除的 SUT 存在记录。</span><span class="sxs-lookup"><span data-stu-id="16091-240">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="16091-241">选择 SUT 中 `messages` 窗体的第一个 "删除" 按钮时，会将请求中的内容模拟到 SUT：</span><span class="sxs-lookup"><span data-stu-id="16091-241">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="16091-242">客户端选项</span><span class="sxs-lookup"><span data-stu-id="16091-242">Client options</span></span>

<span data-ttu-id="16091-243">下表显示了创建 `HttpClient` 实例时可用的默认[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 。</span><span class="sxs-lookup"><span data-stu-id="16091-243">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="16091-244">选项</span><span class="sxs-lookup"><span data-stu-id="16091-244">Option</span></span> | <span data-ttu-id="16091-245">描述</span><span class="sxs-lookup"><span data-stu-id="16091-245">Description</span></span> | <span data-ttu-id="16091-246">Default</span><span class="sxs-lookup"><span data-stu-id="16091-246">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="16091-247">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="16091-247">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="16091-248">获取或设置 `HttpClient` 实例是否应自动跟随重定向响应。</span><span class="sxs-lookup"><span data-stu-id="16091-248">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="16091-249">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="16091-249">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="16091-250">获取或设置 `HttpClient` 实例的基址。</span><span class="sxs-lookup"><span data-stu-id="16091-250">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="16091-251">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="16091-251">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="16091-252">获取或设置 `HttpClient` 实例是否应处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="16091-252">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="16091-253">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="16091-253">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="16091-254">获取或设置 `HttpClient` 实例应遵循的重定向响应的最大数目。</span><span class="sxs-lookup"><span data-stu-id="16091-254">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="16091-255">7</span><span class="sxs-lookup"><span data-stu-id="16091-255">7</span></span> |

<span data-ttu-id="16091-256">创建 `WebApplicationFactoryClientOptions` 类并将其传递给[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)方法（在代码示例中显示默认值）：</span><span class="sxs-lookup"><span data-stu-id="16091-256">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="16091-257">注入模拟服务</span><span class="sxs-lookup"><span data-stu-id="16091-257">Inject mock services</span></span>

<span data-ttu-id="16091-258">通过在主机生成器上调用[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，可以在测试中覆盖服务。</span><span class="sxs-lookup"><span data-stu-id="16091-258">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="16091-259">**若要注入模拟服务，SUT 必须有一个具有 `Startup.ConfigureServices` 方法的 `Startup` 类。**</span><span class="sxs-lookup"><span data-stu-id="16091-259">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="16091-260">示例 SUT 包含一个返回 quote 的作用域服务。</span><span class="sxs-lookup"><span data-stu-id="16091-260">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="16091-261">请求索引页时，引号嵌入到索引页上的隐藏字段中。</span><span class="sxs-lookup"><span data-stu-id="16091-261">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="16091-262">*服务/IQuoteService*：</span><span class="sxs-lookup"><span data-stu-id="16091-262">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="16091-263">*服务/QuoteService*：</span><span class="sxs-lookup"><span data-stu-id="16091-263">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="16091-264">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="16091-264">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="16091-265">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="16091-265">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="16091-266">*Pages/Index. cs*：</span><span class="sxs-lookup"><span data-stu-id="16091-266">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="16091-267">运行 SUT 应用时，将生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="16091-267">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="16091-268">若要在集成测试中测试服务和引号注入，测试会将模拟服务注入到 SUT。</span><span class="sxs-lookup"><span data-stu-id="16091-268">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="16091-269">模拟服务会将应用的 `QuoteService` 替换为测试应用提供的服务，称为 `TestQuoteService`：</span><span class="sxs-lookup"><span data-stu-id="16091-269">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="16091-270">*IntegrationTests.IndexPageTests.cs*：</span><span class="sxs-lookup"><span data-stu-id="16091-270">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="16091-271">调用 `ConfigureTestServices`，并注册作用域内服务：</span><span class="sxs-lookup"><span data-stu-id="16091-271">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="16091-272">在测试执行过程中生成的标记反映 `TestQuoteService`提供的引号文本，因此断言通过：</span><span class="sxs-lookup"><span data-stu-id="16091-272">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="16091-273">模拟身份验证</span><span class="sxs-lookup"><span data-stu-id="16091-273">Mock authentication</span></span>

<span data-ttu-id="16091-274">`AuthTests` 类中的测试检查安全终结点：</span><span class="sxs-lookup"><span data-stu-id="16091-274">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="16091-275">将未经身份验证的用户重定向到应用程序的登录页。</span><span class="sxs-lookup"><span data-stu-id="16091-275">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="16091-276">返回已经过身份验证的用户的内容。</span><span class="sxs-lookup"><span data-stu-id="16091-276">Returns content for an authenticated user.</span></span>

<span data-ttu-id="16091-277">在 SUT 中，`/SecurePage` 页面使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)约定将[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)应用到页面。</span><span class="sxs-lookup"><span data-stu-id="16091-277">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="16091-278">有关详细信息，请参阅[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="16091-278">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="16091-279">在 `Get_SecurePageRedirectsAnUnauthenticatedUser` 测试中，通过将[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)设置为 `false`，将[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)设置为禁止重定向：</span><span class="sxs-lookup"><span data-stu-id="16091-279">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="16091-280">通过禁止客户端按照重定向操作，可以执行以下检查：</span><span class="sxs-lookup"><span data-stu-id="16091-280">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="16091-281">可以对照预期的[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)结果检查 SUT 返回的状态代码，而不是在重定向到登录页后返回最终状态代码，这会是[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)。</span><span class="sxs-lookup"><span data-stu-id="16091-281">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="16091-282">将检查响应标头中的 `Location` 标头值，以确认该标头值以 `http://localhost/Identity/Account/Login`（而不是最终的登录页响应）开头，而 `Location` 标头不存在。</span><span class="sxs-lookup"><span data-stu-id="16091-282">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="16091-283">测试应用可以模拟[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)中的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>，以便测试身份验证和授权的各个方面。</span><span class="sxs-lookup"><span data-stu-id="16091-283">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="16091-284">最小方案返回[AuthenticateResult](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：</span><span class="sxs-lookup"><span data-stu-id="16091-284">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="16091-285">当身份验证方案设置为 `Test` 在其中向 `ConfigureTestServices`注册了 `AddAuthentication` 时，将调用 `TestAuthHandler` 来对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="16091-285">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="16091-286">有关 `WebApplicationFactoryClientOptions` 的详细信息，请参阅[Client options](#client-options)部分。</span><span class="sxs-lookup"><span data-stu-id="16091-286">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="16091-287">设置环境</span><span class="sxs-lookup"><span data-stu-id="16091-287">Set the environment</span></span>

<span data-ttu-id="16091-288">默认情况下，SUT 的主机和应用环境配置为使用开发环境。</span><span class="sxs-lookup"><span data-stu-id="16091-288">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="16091-289">重写 SUT 的环境：</span><span class="sxs-lookup"><span data-stu-id="16091-289">To override the SUT's environment:</span></span>

* <span data-ttu-id="16091-290">设置 `ASPNETCORE_ENVIRONMENT` 环境变量（例如，`Staging`、`Production`或其他自定义值，例如 `Testing`）。</span><span class="sxs-lookup"><span data-stu-id="16091-290">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="16091-291">重写测试应用中 `CreateHostBuilder`，以读取以 `ASPNETCORE`为前缀的环境变量。</span><span class="sxs-lookup"><span data-stu-id="16091-291">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="16091-292">测试基础结构如何推断应用内容根路径</span><span class="sxs-lookup"><span data-stu-id="16091-292">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="16091-293">`WebApplicationFactory` 构造函数通过使用等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName`的键搜索包含集成测试的程序集上的[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)来推断应用[内容根](xref:fundamentals/index#content-root)路径。</span><span class="sxs-lookup"><span data-stu-id="16091-293">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="16091-294">如果找不到具有正确键的属性，`WebApplicationFactory` 将回退到搜索解决方案文件（ *.sln*），并在解决方案目录中追加 `TEntryPoint` 程序集名称。</span><span class="sxs-lookup"><span data-stu-id="16091-294">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="16091-295">应用根目录（内容根路径）用于发现视图和内容文件。</span><span class="sxs-lookup"><span data-stu-id="16091-295">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="16091-296">禁用卷影复制</span><span class="sxs-lookup"><span data-stu-id="16091-296">Disable shadow copying</span></span>

<span data-ttu-id="16091-297">卷影复制会导致在输出目录以外的目录中执行测试。</span><span class="sxs-lookup"><span data-stu-id="16091-297">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="16091-298">要使测试正常工作，必须禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="16091-298">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="16091-299">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的*xUnit*文件来禁用 xUnit 的卷影复制。</span><span class="sxs-lookup"><span data-stu-id="16091-299">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="16091-300">有关详细信息，请参阅[配置 xUnit 与 JSON](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="16091-300">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="16091-301">将*xunit*文件添加到测试项目的根，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="16091-301">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

## <a name="disposal-of-objects"></a><span data-ttu-id="16091-302">对象的处理</span><span class="sxs-lookup"><span data-stu-id="16091-302">Disposal of objects</span></span>

<span data-ttu-id="16091-303">执行 `IClassFixture` 实现的测试后，当 xUnit 释放[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)时，将释放[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="16091-303">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="16091-304">如果开发人员实例化的对象需要处置，请在 `IClassFixture` 实现中释放这些对象。</span><span class="sxs-lookup"><span data-stu-id="16091-304">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="16091-305">有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="16091-305">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="16091-306">集成测试示例</span><span class="sxs-lookup"><span data-stu-id="16091-306">Integration tests sample</span></span>

<span data-ttu-id="16091-307">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由两个应用组成：</span><span class="sxs-lookup"><span data-stu-id="16091-307">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="16091-308">应用</span><span class="sxs-lookup"><span data-stu-id="16091-308">App</span></span> | <span data-ttu-id="16091-309">项目目录</span><span class="sxs-lookup"><span data-stu-id="16091-309">Project directory</span></span> | <span data-ttu-id="16091-310">描述</span><span class="sxs-lookup"><span data-stu-id="16091-310">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="16091-311">消息应用（SUT）</span><span class="sxs-lookup"><span data-stu-id="16091-311">Message app (the SUT)</span></span> | <span data-ttu-id="16091-312">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="16091-312">*src/RazorPagesProject*</span></span> | <span data-ttu-id="16091-313">允许用户添加、删除一个、删除和分析消息。</span><span class="sxs-lookup"><span data-stu-id="16091-313">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="16091-314">测试应用</span><span class="sxs-lookup"><span data-stu-id="16091-314">Test app</span></span> | <span data-ttu-id="16091-315">*测试/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="16091-315">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="16091-316">用于集成测试 SUT。</span><span class="sxs-lookup"><span data-stu-id="16091-316">Used to integration test the SUT.</span></span> |

<span data-ttu-id="16091-317">可以使用 IDE （如[Visual Studio](https://visualstudio.microsoft.com)）的内置测试功能来运行测试。</span><span class="sxs-lookup"><span data-stu-id="16091-317">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="16091-318">如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在*RazorPagesProject 目录中的命令*提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="16091-318">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```console
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="16091-319">消息应用（SUT）组织</span><span class="sxs-lookup"><span data-stu-id="16091-319">Message app (SUT) organization</span></span>

<span data-ttu-id="16091-320">SUT 是 Razor Pages 的消息系统，具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="16091-320">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="16091-321">应用的 "索引" 页（*Pages/索引. cshtml*和*pages/index*）提供了一个 UI 和页面模型方法，用于控制消息的添加、删除和分析（每条消息的平均单词数）。</span><span class="sxs-lookup"><span data-stu-id="16091-321">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="16091-322">消息由 `Message` 类（*Data/message*）描述，具有两个属性： `Id` （密钥）和 `Text` （message）。</span><span class="sxs-lookup"><span data-stu-id="16091-322">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="16091-323">`Text` 属性是必需的，并且限制为200个字符。</span><span class="sxs-lookup"><span data-stu-id="16091-323">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="16091-324">使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。</span><span class="sxs-lookup"><span data-stu-id="16091-324">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="16091-325">应用在其数据库上下文类中包含数据访问层（DAL），`AppDbContext` （*data/AppDbContext*）。</span><span class="sxs-lookup"><span data-stu-id="16091-325">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="16091-326">如果数据库在应用启动时为空，则会用三条消息初始化消息存储。</span><span class="sxs-lookup"><span data-stu-id="16091-326">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="16091-327">应用包括只能由经过身份验证的用户访问的 `/SecurePage`。</span><span class="sxs-lookup"><span data-stu-id="16091-327">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="16091-328">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。</span><span class="sxs-lookup"><span data-stu-id="16091-328">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="16091-329">本主题使用[xUnit](https://xunit.github.io/)测试框架。</span><span class="sxs-lookup"><span data-stu-id="16091-329">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="16091-330">不同测试框架中的测试概念和测试实现相似，但并不完全相同。</span><span class="sxs-lookup"><span data-stu-id="16091-330">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="16091-331">尽管应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。</span><span class="sxs-lookup"><span data-stu-id="16091-331">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="16091-332">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="16091-332">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="16091-333">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="16091-333">Test app organization</span></span>

<span data-ttu-id="16091-334">测试应用是 "*测试"/"RazorPagesProject* " 目录中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="16091-334">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="16091-335">测试应用程序目录</span><span class="sxs-lookup"><span data-stu-id="16091-335">Test app directory</span></span> | <span data-ttu-id="16091-336">描述</span><span class="sxs-lookup"><span data-stu-id="16091-336">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="16091-337">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="16091-337">*AuthTests*</span></span> | <span data-ttu-id="16091-338">包含的测试方法：</span><span class="sxs-lookup"><span data-stu-id="16091-338">Contains test methods for:</span></span><ul><li><span data-ttu-id="16091-339">通过未经身份验证的用户访问安全页。</span><span class="sxs-lookup"><span data-stu-id="16091-339">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="16091-340">使用模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>通过经过身份验证的用户访问安全页。</span><span class="sxs-lookup"><span data-stu-id="16091-340">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="16091-341">获取 GitHub 用户配置文件，并检查配置文件的用户登录名。</span><span class="sxs-lookup"><span data-stu-id="16091-341">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="16091-342">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="16091-342">*BasicTests*</span></span> | <span data-ttu-id="16091-343">包含路由和内容类型的测试方法。</span><span class="sxs-lookup"><span data-stu-id="16091-343">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="16091-344">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="16091-344">*IntegrationTests*</span></span> | <span data-ttu-id="16091-345">包含使用自定义 `WebApplicationFactory` 类的索引页的集成测试。</span><span class="sxs-lookup"><span data-stu-id="16091-345">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="16091-346">*帮助程序/实用工具*</span><span class="sxs-lookup"><span data-stu-id="16091-346">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="16091-347">*Utilities.cs*包含用于使用测试数据对数据库进行种子设定的 `InitializeDbForTests` 方法。</span><span class="sxs-lookup"><span data-stu-id="16091-347">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="16091-348">*HtmlHelpers.cs*提供了一个方法，用于返回 AngleSharp `IHtmlDocument` 以供测试方法使用。</span><span class="sxs-lookup"><span data-stu-id="16091-348">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="16091-349">*HttpClientExtensions.cs*提供 `SendAsync` 的重载，以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="16091-349">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="16091-350">测试框架为[xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="16091-350">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="16091-351">集成测试是使用 TestHost 的[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)进行的，其中包括[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="16091-351">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="16091-352">由于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包用于配置测试主机和测试服务器，因此，在测试应用程序的项目文件或测试的开发人员配置中，`TestHost` 和 `TestServer` 包不需要直接包引用应用.</span><span class="sxs-lookup"><span data-stu-id="16091-352">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="16091-353">**播种要测试的数据库**</span><span class="sxs-lookup"><span data-stu-id="16091-353">**Seeding the database for testing**</span></span>

<span data-ttu-id="16091-354">集成测试在执行测试前通常需要数据库中的一个小型数据集。</span><span class="sxs-lookup"><span data-stu-id="16091-354">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="16091-355">例如，删除测试调用数据库记录，因此数据库必须至少有一条记录，删除请求才能成功。</span><span class="sxs-lookup"><span data-stu-id="16091-355">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="16091-356">该示例应用在*Utilities.cs*中使用三个消息对数据库进行种子设定，当测试执行时，可以使用这些消息：</span><span class="sxs-lookup"><span data-stu-id="16091-356">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/3.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

<span data-ttu-id="16091-357">在其 `Startup.ConfigureServices` 方法中注册了 SUT 的数据库上下文。</span><span class="sxs-lookup"><span data-stu-id="16091-357">The SUT's database context is registered in its `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="16091-358">执行应用的 `Startup.ConfigureServices` 代码*后*，将执行测试应用的 `builder.ConfigureServices` 回调。</span><span class="sxs-lookup"><span data-stu-id="16091-358">The test app's `builder.ConfigureServices` callback is executed *after* the app's `Startup.ConfigureServices` code is executed.</span></span> <span data-ttu-id="16091-359">若要将不同的数据库用于测试，则必须将应用程序的数据库上下文替换 `builder.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="16091-359">To use a different database for the tests, the app's database context must be replaced in `builder.ConfigureServices`.</span></span> <span data-ttu-id="16091-360">有关详细信息，请参阅[自定义 WebApplicationFactory](#customize-webapplicationfactory)部分。</span><span class="sxs-lookup"><span data-stu-id="16091-360">For more information, see the [Customize WebApplicationFactory](#customize-webapplicationfactory) section.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="16091-361">集成测试可确保应用程序的组件在包含应用程序支持的基础结构的级别（例如数据库、文件系统和网络）正常运行。</span><span class="sxs-lookup"><span data-stu-id="16091-361">Integration tests ensure that an app's components function correctly at a level that includes the app's supporting infrastructure, such as the database, file system, and network.</span></span> <span data-ttu-id="16091-362">ASP.NET Core 支持结合使用单元测试框架和测试 web 主机和内存中测试服务器的集成测试。</span><span class="sxs-lookup"><span data-stu-id="16091-362">ASP.NET Core supports integration tests using a unit test framework with a test web host and an in-memory test server.</span></span>

<span data-ttu-id="16091-363">本主题假定基本了解单元测试。</span><span class="sxs-lookup"><span data-stu-id="16091-363">This topic assumes a basic understanding of unit tests.</span></span> <span data-ttu-id="16091-364">如果不熟悉测试概念，请参阅[.Net Core 中的单元测试和 .NET Standard](/dotnet/core/testing/)主题及其链接的内容。</span><span class="sxs-lookup"><span data-stu-id="16091-364">If unfamiliar with test concepts, see the [Unit Testing in .NET Core and .NET Standard](/dotnet/core/testing/) topic and its linked content.</span></span>

<span data-ttu-id="16091-365">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="16091-365">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="16091-366">该示例应用是 Razor Pages 应用程序，并假定基本了解 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="16091-366">The sample app is a Razor Pages app and assumes a basic understanding of Razor Pages.</span></span> <span data-ttu-id="16091-367">如果不熟悉 Razor Pages，请参阅以下主题：</span><span class="sxs-lookup"><span data-stu-id="16091-367">If unfamiliar with Razor Pages, see the following topics:</span></span>

* [<span data-ttu-id="16091-368">Razor 页面介绍</span><span class="sxs-lookup"><span data-stu-id="16091-368">Introduction to Razor Pages</span></span>](xref:razor-pages/index)
* [<span data-ttu-id="16091-369">Razor 页面入门</span><span class="sxs-lookup"><span data-stu-id="16091-369">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start)
* [<span data-ttu-id="16091-370">Razor 页面单元测试</span><span class="sxs-lookup"><span data-stu-id="16091-370">Razor Pages unit tests</span></span>](xref:test/razor-pages-tests)

> [!NOTE]
> <span data-ttu-id="16091-371">对于测试 Spa，我们建议使用[Selenium](https://www.seleniumhq.org/)这样的工具，它可以自动执行浏览器。</span><span class="sxs-lookup"><span data-stu-id="16091-371">For testing SPAs, we recommended a tool such as [Selenium](https://www.seleniumhq.org/), which can automate a browser.</span></span>

## <a name="introduction-to-integration-tests"></a><span data-ttu-id="16091-372">集成测试简介</span><span class="sxs-lookup"><span data-stu-id="16091-372">Introduction to integration tests</span></span>

<span data-ttu-id="16091-373">与[单元测试](/dotnet/core/testing/)相比，集成测试在更广泛的级别上评估应用的组件。</span><span class="sxs-lookup"><span data-stu-id="16091-373">Integration tests evaluate an app's components on a broader level than [unit tests](/dotnet/core/testing/).</span></span> <span data-ttu-id="16091-374">单元测试用于测试独立的软件组件，如单独的类方法。</span><span class="sxs-lookup"><span data-stu-id="16091-374">Unit tests are used to test isolated software components, such as individual class methods.</span></span> <span data-ttu-id="16091-375">集成测试确认两个或更多应用程序组件一起工作以生成预期的结果，其中可能包括完全处理请求所需的每个组件。</span><span class="sxs-lookup"><span data-stu-id="16091-375">Integration tests confirm that two or more app components work together to produce an expected result, possibly including every component required to fully process a request.</span></span>

<span data-ttu-id="16091-376">这些广泛的测试用于测试应用程序的基础结构和整个框架，通常包括以下组件：</span><span class="sxs-lookup"><span data-stu-id="16091-376">These broader tests are used to test the app's infrastructure and whole framework, often including the following components:</span></span>

* <span data-ttu-id="16091-377">数据库</span><span class="sxs-lookup"><span data-stu-id="16091-377">Database</span></span>
* <span data-ttu-id="16091-378">文件系统</span><span class="sxs-lookup"><span data-stu-id="16091-378">File system</span></span>
* <span data-ttu-id="16091-379">网络设备</span><span class="sxs-lookup"><span data-stu-id="16091-379">Network appliances</span></span>
* <span data-ttu-id="16091-380">请求-响应管道</span><span class="sxs-lookup"><span data-stu-id="16091-380">Request-response pipeline</span></span>

<span data-ttu-id="16091-381">单元测试使用称为*fakes*或*mock 对象*的制造组件来代替基础结构组件。</span><span class="sxs-lookup"><span data-stu-id="16091-381">Unit tests use fabricated components, known as *fakes* or *mock objects*, in place of infrastructure components.</span></span>

<span data-ttu-id="16091-382">与单元测试相比，集成测试：</span><span class="sxs-lookup"><span data-stu-id="16091-382">In contrast to unit tests, integration tests:</span></span>

* <span data-ttu-id="16091-383">使用应用在生产环境中使用的实际组件。</span><span class="sxs-lookup"><span data-stu-id="16091-383">Use the actual components that the app uses in production.</span></span>
* <span data-ttu-id="16091-384">需要进行更多的代码和数据处理。</span><span class="sxs-lookup"><span data-stu-id="16091-384">Require more code and data processing.</span></span>
* <span data-ttu-id="16091-385">需要较长时间才能运行。</span><span class="sxs-lookup"><span data-stu-id="16091-385">Take longer to run.</span></span>

<span data-ttu-id="16091-386">因此，将集成测试的使用限制为最重要的基础结构方案。</span><span class="sxs-lookup"><span data-stu-id="16091-386">Therefore, limit the use of integration tests to the most important infrastructure scenarios.</span></span> <span data-ttu-id="16091-387">如果可以使用单元测试或集成测试来测试行为，请选择单元测试。</span><span class="sxs-lookup"><span data-stu-id="16091-387">If a behavior can be tested using either a unit test or an integration test, choose the unit test.</span></span>

> [!TIP]
> <span data-ttu-id="16091-388">请勿为数据库和文件系统的每个可能的数据排列和文件访问编写集成测试。</span><span class="sxs-lookup"><span data-stu-id="16091-388">Don't write integration tests for every possible permutation of data and file access with databases and file systems.</span></span> <span data-ttu-id="16091-389">无论应用程序中有多少位置与数据库和文件系统交互，一组集中式的读取、写入、更新和删除集成测试通常都能充分测试数据库和文件系统组件。</span><span class="sxs-lookup"><span data-stu-id="16091-389">Regardless of how many places across an app interact with databases and file systems, a focused set of read, write, update, and delete integration tests are usually capable of adequately testing database and file system components.</span></span> <span data-ttu-id="16091-390">使用单元测试对与这些组件进行交互的方法逻辑进行例程测试。</span><span class="sxs-lookup"><span data-stu-id="16091-390">Use unit tests for routine tests of method logic that interact with these components.</span></span> <span data-ttu-id="16091-391">在单元测试中，使用基础结构 fakes/模拟会导致更快地执行测试。</span><span class="sxs-lookup"><span data-stu-id="16091-391">In unit tests, the use of infrastructure fakes/mocks result in faster test execution.</span></span>

> [!NOTE]
> <span data-ttu-id="16091-392">在讨论集成测试时，测试的项目经常称为 "测试中的*系统*" 或简称 "SUT"。</span><span class="sxs-lookup"><span data-stu-id="16091-392">In discussions of integration tests, the tested project is frequently called the *System Under Test*, or "SUT" for short.</span></span>
>
> <span data-ttu-id="16091-393">*本主题中使用 "SUT" 来引用已测试的 ASP.NET Core 应用。*</span><span class="sxs-lookup"><span data-stu-id="16091-393">*"SUT" is used throughout this topic to refer to the tested ASP.NET Core app.*</span></span>

## <a name="aspnet-core-integration-tests"></a><span data-ttu-id="16091-394">ASP.NET Core 集成测试</span><span class="sxs-lookup"><span data-stu-id="16091-394">ASP.NET Core integration tests</span></span>

<span data-ttu-id="16091-395">ASP.NET Core 中的集成测试需要以下各项：</span><span class="sxs-lookup"><span data-stu-id="16091-395">Integration tests in ASP.NET Core require the following:</span></span>

* <span data-ttu-id="16091-396">测试项目用于包含和执行测试。</span><span class="sxs-lookup"><span data-stu-id="16091-396">A test project is used to contain and execute the tests.</span></span> <span data-ttu-id="16091-397">测试项目具有对 SUT 的引用。</span><span class="sxs-lookup"><span data-stu-id="16091-397">The test project has a reference to the SUT.</span></span>
* <span data-ttu-id="16091-398">测试项目将为 SUT 创建测试 web 主机，并使用测试服务器客户端来处理 SUT 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="16091-398">The test project creates a test web host for the SUT and uses a test server client to handle requests and responses with the SUT.</span></span>
* <span data-ttu-id="16091-399">测试运行程序用于执行测试并报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="16091-399">A test runner is used to execute the tests and report the test results.</span></span>

<span data-ttu-id="16091-400">集成测试遵循一系列事件，其中包括常见的*排列*、*操作*和*断言*测试步骤：</span><span class="sxs-lookup"><span data-stu-id="16091-400">Integration tests follow a sequence of events that include the usual *Arrange*, *Act*, and *Assert* test steps:</span></span>

1. <span data-ttu-id="16091-401">已配置 SUT 的 web 主机。</span><span class="sxs-lookup"><span data-stu-id="16091-401">The SUT's web host is configured.</span></span>
1. <span data-ttu-id="16091-402">创建测试服务器客户端以向应用程序提交请求。</span><span class="sxs-lookup"><span data-stu-id="16091-402">A test server client is created to submit requests to the app.</span></span>
1. <span data-ttu-id="16091-403">执行 "*排列*测试" 步骤：测试应用准备请求。</span><span class="sxs-lookup"><span data-stu-id="16091-403">The *Arrange* test step is executed: The test app prepares a request.</span></span>
1. <span data-ttu-id="16091-404">执行*Act*测试步骤：客户端提交请求并接收响应。</span><span class="sxs-lookup"><span data-stu-id="16091-404">The *Act* test step is executed: The client submits the request and receives the response.</span></span>
1. <span data-ttu-id="16091-405">将*执行断言*测试步骤：根据*预期*的响应验证*实际*响应是*通过*还是*失败*。</span><span class="sxs-lookup"><span data-stu-id="16091-405">The *Assert* test step is executed: The *actual* response is validated as a *pass* or *fail* based on an *expected* response.</span></span>
1. <span data-ttu-id="16091-406">此过程将一直继续，直到执行了所有测试。</span><span class="sxs-lookup"><span data-stu-id="16091-406">The process continues until all of the tests are executed.</span></span>
1. <span data-ttu-id="16091-407">将报告测试结果。</span><span class="sxs-lookup"><span data-stu-id="16091-407">The test results are reported.</span></span>

<span data-ttu-id="16091-408">通常，测试 web 主机的配置与应用程序用于测试运行的普通 web 主机的配置不同。</span><span class="sxs-lookup"><span data-stu-id="16091-408">Usually, the test web host is configured differently than the app's normal web host for the test runs.</span></span> <span data-ttu-id="16091-409">例如，可以将不同的数据库或不同的应用设置用于测试。</span><span class="sxs-lookup"><span data-stu-id="16091-409">For example, a different database or different app settings might be used for the tests.</span></span>

<span data-ttu-id="16091-410">基础结构组件（例如测试 web 主机和内存中测试服务器（[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)））由[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包提供或管理。</span><span class="sxs-lookup"><span data-stu-id="16091-410">Infrastructure components, such as the test web host and in-memory test server ([TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)), are provided or managed by the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package.</span></span> <span data-ttu-id="16091-411">使用此包简化了测试的创建和执行。</span><span class="sxs-lookup"><span data-stu-id="16091-411">Use of this package streamlines test creation and execution.</span></span>

<span data-ttu-id="16091-412">`Microsoft.AspNetCore.Mvc.Testing` 包处理以下任务：</span><span class="sxs-lookup"><span data-stu-id="16091-412">The `Microsoft.AspNetCore.Mvc.Testing` package handles the following tasks:</span></span>

* <span data-ttu-id="16091-413">将依赖项文件（ *. .deps.json*）从 SUT 复制到测试项目的*bin*目录中。</span><span class="sxs-lookup"><span data-stu-id="16091-413">Copies the dependencies file (*.deps*) from the SUT into the test project's *bin* directory.</span></span>
* <span data-ttu-id="16091-414">将[内容根](xref:fundamentals/index#content-root)设置为 SUT 的项目根，以便在执行测试时找到静态文件和页面/视图。</span><span class="sxs-lookup"><span data-stu-id="16091-414">Sets the [content root](xref:fundamentals/index#content-root) to the SUT's project root so that static files and pages/views are found when the tests are executed.</span></span>
* <span data-ttu-id="16091-415">提供[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)类，以简化与 `TestServer` 的 SUT 的引导。</span><span class="sxs-lookup"><span data-stu-id="16091-415">Provides the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) class to streamline bootstrapping the SUT with `TestServer`.</span></span>

<span data-ttu-id="16091-416">[单元测试](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)文档介绍了如何设置测试项目和测试运行程序，以及有关如何为测试和测试类命名测试和建议的详细说明。</span><span class="sxs-lookup"><span data-stu-id="16091-416">The [unit tests](/dotnet/articles/core/testing/unit-testing-with-dotnet-test) documentation describes how to set up a test project and test runner, along with detailed instructions on how to run tests and recommendations for how to name tests and test classes.</span></span>

> [!NOTE]
> <span data-ttu-id="16091-417">为应用程序创建测试项目时，请将集成测试中的单元测试分成不同的项目。</span><span class="sxs-lookup"><span data-stu-id="16091-417">When creating a test project for an app, separate the unit tests from the integration tests into different projects.</span></span> <span data-ttu-id="16091-418">这有助于确保不会意外地将基础结构测试组件包含在单元测试中。</span><span class="sxs-lookup"><span data-stu-id="16091-418">This helps ensure that infrastructure testing components aren't accidentally included in the unit tests.</span></span> <span data-ttu-id="16091-419">单元测试和集成测试的隔离还允许控制运行的测试集。</span><span class="sxs-lookup"><span data-stu-id="16091-419">Separation of unit and integration tests also allows control over which set of tests are run.</span></span>

<span data-ttu-id="16091-420">Razor Pages 应用和 MVC 应用的测试的配置几乎没有任何区别。</span><span class="sxs-lookup"><span data-stu-id="16091-420">There's virtually no difference between the configuration for tests of Razor Pages apps and MVC apps.</span></span> <span data-ttu-id="16091-421">唯一的区别在于测试的命名方式。</span><span class="sxs-lookup"><span data-stu-id="16091-421">The only difference is in how the tests are named.</span></span> <span data-ttu-id="16091-422">在 Razor Pages 应用中，页终结点的测试通常以页面模型类命名（例如，`IndexPageTests` 来测试索引页的组件集成）。</span><span class="sxs-lookup"><span data-stu-id="16091-422">In a Razor Pages app, tests of page endpoints are usually named after the page model class (for example, `IndexPageTests` to test component integration for the Index page).</span></span> <span data-ttu-id="16091-423">在 MVC 应用中，通常按控制器类对测试进行组织，并按它们所测试的控制器（例如 `HomeControllerTests` 来测试主控制器的组件集成）进行命名。</span><span class="sxs-lookup"><span data-stu-id="16091-423">In an MVC app, tests are usually organized by controller classes and named after the controllers they test (for example, `HomeControllerTests` to test component integration for the Home controller).</span></span>

## <a name="test-app-prerequisites"></a><span data-ttu-id="16091-424">测试应用必备组件</span><span class="sxs-lookup"><span data-stu-id="16091-424">Test app prerequisites</span></span>

<span data-ttu-id="16091-425">测试项目必须：</span><span class="sxs-lookup"><span data-stu-id="16091-425">The test project must:</span></span>

* <span data-ttu-id="16091-426">引用以下包：</span><span class="sxs-lookup"><span data-stu-id="16091-426">Reference the following packages:</span></span>
  * [<span data-ttu-id="16091-427">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="16091-427">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)
  * [<span data-ttu-id="16091-428">AspNetCore。测试</span><span class="sxs-lookup"><span data-stu-id="16091-428">Microsoft.AspNetCore.Mvc.Testing</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing/)
* <span data-ttu-id="16091-429">在项目文件中指定 Web SDK （`<Project Sdk="Microsoft.NET.Sdk.Web">`）。</span><span class="sxs-lookup"><span data-stu-id="16091-429">Specify the Web SDK in the project file (`<Project Sdk="Microsoft.NET.Sdk.Web">`).</span></span> <span data-ttu-id="16091-430">引用[AspNetCore 元包](xref:fundamentals/metapackage-app)时，需要 Web SDK。</span><span class="sxs-lookup"><span data-stu-id="16091-430">The Web SDK is required when referencing the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="16091-431">可以在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中查看这些先决条件。</span><span class="sxs-lookup"><span data-stu-id="16091-431">These prerequisites can be seen in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/).</span></span> <span data-ttu-id="16091-432">检查 "*测试"/"RazorPagesProject"/"RazorPagesProject* " 文件。</span><span class="sxs-lookup"><span data-stu-id="16091-432">Inspect the *tests/RazorPagesProject.Tests/RazorPagesProject.Tests.csproj* file.</span></span> <span data-ttu-id="16091-433">示例应用使用[xUnit](https://xunit.github.io/)测试框架和[AngleSharp](https://anglesharp.github.io/)分析器库，因此示例应用还引用：</span><span class="sxs-lookup"><span data-stu-id="16091-433">The sample app uses the [xUnit](https://xunit.github.io/) test framework and the [AngleSharp](https://anglesharp.github.io/) parser library, so the sample app also references:</span></span>

* [<span data-ttu-id="16091-434">xunit</span><span class="sxs-lookup"><span data-stu-id="16091-434">xunit</span></span>](https://www.nuget.org/packages/xunit/)
* [<span data-ttu-id="16091-435">xunit. visualstudio</span><span class="sxs-lookup"><span data-stu-id="16091-435">xunit.runner.visualstudio</span></span>](https://www.nuget.org/packages/xunit.runner.visualstudio/)
* [<span data-ttu-id="16091-436">AngleSharp</span><span class="sxs-lookup"><span data-stu-id="16091-436">AngleSharp</span></span>](https://www.nuget.org/packages/AngleSharp/)

## <a name="sut-environment"></a><span data-ttu-id="16091-437">SUT 环境</span><span class="sxs-lookup"><span data-stu-id="16091-437">SUT environment</span></span>

<span data-ttu-id="16091-438">如果未设置 SUT 的[环境](xref:fundamentals/environments)，环境将默认为 "开发"。</span><span class="sxs-lookup"><span data-stu-id="16091-438">If the SUT's [environment](xref:fundamentals/environments) isn't set, the environment defaults to Development.</span></span>

## <a name="basic-tests-with-the-default-webapplicationfactory"></a><span data-ttu-id="16091-439">具有默认 WebApplicationFactory 的基本测试</span><span class="sxs-lookup"><span data-stu-id="16091-439">Basic tests with the default WebApplicationFactory</span></span>

<span data-ttu-id="16091-440">[WebApplicationFactory\<TEntryPoint >](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)用于创建集成测试的[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) 。</span><span class="sxs-lookup"><span data-stu-id="16091-440">[WebApplicationFactory\<TEntryPoint>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1) is used to create a [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) for the integration tests.</span></span> <span data-ttu-id="16091-441">`TEntryPoint` 是 SUT （通常是 `Startup` 类）的入口点类。</span><span class="sxs-lookup"><span data-stu-id="16091-441">`TEntryPoint` is the entry point class of the SUT, usually the `Startup` class.</span></span>

<span data-ttu-id="16091-442">测试类实现*类装置*接口（[IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)）以指示类包含测试，并跨类中的测试提供共享对象实例。</span><span class="sxs-lookup"><span data-stu-id="16091-442">Test classes implement a *class fixture* interface ([IClassFixture](https://xunit.github.io/docs/shared-context#class-fixture)) to indicate the class contains tests and provide shared object instances across the tests in the class.</span></span>

<span data-ttu-id="16091-443">下面的测试类 `BasicTests`使用 `WebApplicationFactory` 来启动 SUT，并为测试方法 `Get_EndpointsReturnSuccessAndCorrectContentType`提供[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="16091-443">The following test class, `BasicTests`, uses the `WebApplicationFactory` to bootstrap the SUT and provide an [HttpClient](/dotnet/api/system.net.http.httpclient) to a test method, `Get_EndpointsReturnSuccessAndCorrectContentType`.</span></span> <span data-ttu-id="16091-444">方法将检查响应状态代码是否成功（范围200-299 中的状态代码）和多个应用页面的 `Content-Type` 标头是否 `text/html; charset=utf-8`。</span><span class="sxs-lookup"><span data-stu-id="16091-444">The method checks if the response status code is successful (status codes in the range 200-299) and the `Content-Type` header is `text/html; charset=utf-8` for several app pages.</span></span>

<span data-ttu-id="16091-445">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)创建自动跟随重定向并处理 cookie 的 `HttpClient` 的实例。</span><span class="sxs-lookup"><span data-stu-id="16091-445">[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) creates an instance of `HttpClient` that automatically follows redirects and handles cookies.</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/BasicTests.cs?name=snippet1)]

<span data-ttu-id="16091-446">默认情况下，当启用[GDPR 同意策略](xref:security/gdpr)时，不会跨请求保留非关键 cookie。</span><span class="sxs-lookup"><span data-stu-id="16091-446">By default, non-essential cookies aren't preserved across requests when the [GDPR consent policy](xref:security/gdpr) is enabled.</span></span> <span data-ttu-id="16091-447">若要保留不重要的 cookie （如 TempData 提供程序使用的 cookie），请将它们标记为测试中的重要 cookie。</span><span class="sxs-lookup"><span data-stu-id="16091-447">To preserve non-essential cookies, such as those used by the TempData provider, mark them as essential in your tests.</span></span> <span data-ttu-id="16091-448">有关将 cookie 标记为必要的说明，请参阅[基本 cookie](xref:security/gdpr#essential-cookies)。</span><span class="sxs-lookup"><span data-stu-id="16091-448">For instructions on marking a cookie as essential, see [Essential cookies](xref:security/gdpr#essential-cookies).</span></span>

## <a name="customize-webapplicationfactory"></a><span data-ttu-id="16091-449">自定义 WebApplicationFactory</span><span class="sxs-lookup"><span data-stu-id="16091-449">Customize WebApplicationFactory</span></span>

<span data-ttu-id="16091-450">通过继承 `WebApplicationFactory` 创建一个或多个自定义工厂，可以独立于测试类创建 Web 主机配置：</span><span class="sxs-lookup"><span data-stu-id="16091-450">Web host configuration can be created independently of the test classes by inheriting from `WebApplicationFactory` to create one or more custom factories:</span></span>

1. <span data-ttu-id="16091-451">从 `WebApplicationFactory` 继承并重写[ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost)。</span><span class="sxs-lookup"><span data-stu-id="16091-451">Inherit from `WebApplicationFactory` and override [ConfigureWebHost](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.configurewebhost).</span></span> <span data-ttu-id="16091-452">[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder)允许通过[ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices)配置服务集合：</span><span class="sxs-lookup"><span data-stu-id="16091-452">The [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) allows the configuration of the service collection with [ConfigureServices](/dotnet/api/microsoft.aspnetcore.hosting.istartup.configureservices):</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/CustomWebApplicationFactory.cs?name=snippet1)]

   <span data-ttu-id="16091-453">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)中的数据库种子设定由 `InitializeDbForTests` 方法执行。</span><span class="sxs-lookup"><span data-stu-id="16091-453">Database seeding in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is performed by the `InitializeDbForTests` method.</span></span> <span data-ttu-id="16091-454">[集成测试示例：测试应用组织](#test-app-organization)部分介绍了方法。</span><span class="sxs-lookup"><span data-stu-id="16091-454">The method is described in the [Integration tests sample: Test app organization](#test-app-organization) section.</span></span>

2. <span data-ttu-id="16091-455">使用测试类中的自定义 `CustomWebApplicationFactory`。</span><span class="sxs-lookup"><span data-stu-id="16091-455">Use the custom `CustomWebApplicationFactory` in test classes.</span></span> <span data-ttu-id="16091-456">下面的示例使用 `IndexPageTests` 类中的工厂：</span><span class="sxs-lookup"><span data-stu-id="16091-456">The following example uses the factory in the `IndexPageTests` class:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet1)]

   <span data-ttu-id="16091-457">示例应用的客户端配置为阻止以下重定向中的 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="16091-457">The sample app's client is configured to prevent the `HttpClient` from following redirects.</span></span> <span data-ttu-id="16091-458">如稍后在 "[模拟身份验证](#mock-authentication)" 部分中所述，这允许测试检查应用程序的第一个响应的结果。</span><span class="sxs-lookup"><span data-stu-id="16091-458">As explained later in the [Mock authentication](#mock-authentication) section, this permits tests to check the result of the app's first response.</span></span> <span data-ttu-id="16091-459">第一个响应是包含 `Location` 标头的多个测试的重定向。</span><span class="sxs-lookup"><span data-stu-id="16091-459">The first response is a redirect in many of these tests with a `Location` header.</span></span>

3. <span data-ttu-id="16091-460">典型的测试使用 `HttpClient` 和 helper 方法来处理请求和响应：</span><span class="sxs-lookup"><span data-stu-id="16091-460">A typical test uses the `HttpClient` and helper methods to process the request and the response:</span></span>

   [!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet2)]

<span data-ttu-id="16091-461">对 SUT 的任何 POST 请求必须满足防伪检查，该检查是由应用的[数据保护防伪系统](xref:security/data-protection/introduction)自动完成的。</span><span class="sxs-lookup"><span data-stu-id="16091-461">Any POST request to the SUT must satisfy the antiforgery check that's automatically made by the app's [data protection antiforgery system](xref:security/data-protection/introduction).</span></span> <span data-ttu-id="16091-462">为了安排测试的 POST 请求，测试应用必须：</span><span class="sxs-lookup"><span data-stu-id="16091-462">In order to arrange for a test's POST request, the test app must:</span></span>

1. <span data-ttu-id="16091-463">发出对页面的请求。</span><span class="sxs-lookup"><span data-stu-id="16091-463">Make a request for the page.</span></span>
1. <span data-ttu-id="16091-464">分析防伪 cookie 并请求响应中的验证令牌。</span><span class="sxs-lookup"><span data-stu-id="16091-464">Parse the antiforgery cookie and request validation token from the response.</span></span>
1. <span data-ttu-id="16091-465">发出带有防伪 cookie 的 POST 请求，并就地请求验证令牌。</span><span class="sxs-lookup"><span data-stu-id="16091-465">Make the POST request with the antiforgery cookie and request validation token in place.</span></span>

<span data-ttu-id="16091-466">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/)中的 `SendAsync` helper 扩展方法（Helper */HttpClientExtensions*）和 `GetDocumentAsync` helper 方法（helper */HtmlHelpers*）使用[AngleSharp](https://anglesharp.github.io/)分析器来处理防伪检查以下方法：</span><span class="sxs-lookup"><span data-stu-id="16091-466">The `SendAsync` helper extension methods (*Helpers/HttpClientExtensions.cs*) and the `GetDocumentAsync` helper method (*Helpers/HtmlHelpers.cs*) in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples/) use the [AngleSharp](https://anglesharp.github.io/) parser to handle the antiforgery check with the following methods:</span></span>

* <span data-ttu-id="16091-467">`GetDocumentAsync` &ndash; 接收[HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage)并返回 `IHtmlDocument`。</span><span class="sxs-lookup"><span data-stu-id="16091-467">`GetDocumentAsync` &ndash; Receives the [HttpResponseMessage](/dotnet/api/system.net.http.httpresponsemessage) and returns an `IHtmlDocument`.</span></span> <span data-ttu-id="16091-468">`GetDocumentAsync` 使用工厂来准备基于原始 `HttpResponseMessage` 的*虚拟响应*。</span><span class="sxs-lookup"><span data-stu-id="16091-468">`GetDocumentAsync` uses a factory that prepares a *virtual response* based on the original `HttpResponseMessage`.</span></span> <span data-ttu-id="16091-469">有关详细信息，请参阅[AngleSharp 文档](https://github.com/AngleSharp/AngleSharp#documentation)。</span><span class="sxs-lookup"><span data-stu-id="16091-469">For more information, see the [AngleSharp documentation](https://github.com/AngleSharp/AngleSharp#documentation).</span></span>
* <span data-ttu-id="16091-470">`SendAsync` 扩展方法 `HttpClient` 构成[HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage)并调用[SendAsync （HttpRequestMessage）](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_)将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="16091-470">`SendAsync` extension methods for the `HttpClient` compose an [HttpRequestMessage](/dotnet/api/system.net.http.httprequestmessage) and call [SendAsync(HttpRequestMessage)](/dotnet/api/system.net.http.httpclient.sendasync#System_Net_Http_HttpClient_SendAsync_System_Net_Http_HttpRequestMessage_) to submit requests to the SUT.</span></span> <span data-ttu-id="16091-471">`SendAsync` 的重载接受 HTML 窗体（`IHtmlFormElement`）以及以下内容：</span><span class="sxs-lookup"><span data-stu-id="16091-471">Overloads for `SendAsync` accept the HTML form (`IHtmlFormElement`) and the following:</span></span>
  * <span data-ttu-id="16091-472">窗体的 "提交" 按钮（`IHtmlElement`）</span><span class="sxs-lookup"><span data-stu-id="16091-472">Submit button of the form (`IHtmlElement`)</span></span>
  * <span data-ttu-id="16091-473">窗体值集合（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="16091-473">Form values collection (`IEnumerable<KeyValuePair<string, string>>`)</span></span>
  * <span data-ttu-id="16091-474">提交按钮（`IHtmlElement`）和窗体值（`IEnumerable<KeyValuePair<string, string>>`）</span><span class="sxs-lookup"><span data-stu-id="16091-474">Submit button (`IHtmlElement`) and form values (`IEnumerable<KeyValuePair<string, string>>`)</span></span>

> [!NOTE]
> <span data-ttu-id="16091-475">[AngleSharp](https://anglesharp.github.io/)是用于演示目的的第三方解析库，适用于本主题和示例应用。</span><span class="sxs-lookup"><span data-stu-id="16091-475">[AngleSharp](https://anglesharp.github.io/) is a third-party parsing library used for demonstration purposes in this topic and the sample app.</span></span> <span data-ttu-id="16091-476">ASP.NET Core 应用的集成测试不支持或不需要 AngleSharp。</span><span class="sxs-lookup"><span data-stu-id="16091-476">AngleSharp isn't supported or required for integration testing of ASP.NET Core apps.</span></span> <span data-ttu-id="16091-477">可以使用其他分析器，如[Html 灵活性包（HAP）](https://html-agility-pack.net/)。</span><span class="sxs-lookup"><span data-stu-id="16091-477">Other parsers can be used, such as the [Html Agility Pack (HAP)](https://html-agility-pack.net/).</span></span> <span data-ttu-id="16091-478">另一种方法是编写代码来直接处理防伪系统的请求验证令牌和防伪 cookie。</span><span class="sxs-lookup"><span data-stu-id="16091-478">Another approach is to write code to handle the antiforgery system's request verification token and antiforgery cookie directly.</span></span>

## <a name="customize-the-client-with-withwebhostbuilder"></a><span data-ttu-id="16091-479">用 WithWebHostBuilder 自定义客户端</span><span class="sxs-lookup"><span data-stu-id="16091-479">Customize the client with WithWebHostBuilder</span></span>

<span data-ttu-id="16091-480">如果在测试方法中需要其他配置， [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder)会创建一个新的 `WebApplicationFactory`，其中包含由配置进一步自定义的[IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) 。</span><span class="sxs-lookup"><span data-stu-id="16091-480">When additional configuration is required within a test method, [WithWebHostBuilder](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.withwebhostbuilder) creates a new `WebApplicationFactory` with an [IWebHostBuilder](/dotnet/api/microsoft.aspnetcore.hosting.iwebhostbuilder) that is further customized by configuration.</span></span>

<span data-ttu-id="16091-481">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)的 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 测试方法演示如何使用 `WithWebHostBuilder`。</span><span class="sxs-lookup"><span data-stu-id="16091-481">The `Post_DeleteMessageHandler_ReturnsRedirectToRoot` test method of the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) demonstrates the use of `WithWebHostBuilder`.</span></span> <span data-ttu-id="16091-482">此测试通过在 SUT 中触发窗体提交来在数据库中执行记录删除。</span><span class="sxs-lookup"><span data-stu-id="16091-482">This test performs a record delete in the database by triggering a form submission in the SUT.</span></span>

<span data-ttu-id="16091-483">由于 `IndexPageTests` 类中的另一个测试会执行一个操作，该操作将删除数据库中的所有记录，并且该操作可能在 `Post_DeleteMessageHandler_ReturnsRedirectToRoot` 方法之前运行，因此，该数据库在此测试方法中种子，以确保要删除的 SUT 存在记录。</span><span class="sxs-lookup"><span data-stu-id="16091-483">Because another test in the `IndexPageTests` class performs an operation that deletes all of the records in the database and may run before the `Post_DeleteMessageHandler_ReturnsRedirectToRoot` method, the database is reseeded in this test method to ensure that a record is present for the SUT to delete.</span></span> <span data-ttu-id="16091-484">选择 SUT 中 `messages` 窗体的第一个 "删除" 按钮时，会将请求中的内容模拟到 SUT：</span><span class="sxs-lookup"><span data-stu-id="16091-484">Selecting the first delete button of the `messages` form in the SUT is simulated in the request to the SUT:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet3)]

## <a name="client-options"></a><span data-ttu-id="16091-485">客户端选项</span><span class="sxs-lookup"><span data-stu-id="16091-485">Client options</span></span>

<span data-ttu-id="16091-486">下表显示了创建 `HttpClient` 实例时可用的默认[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) 。</span><span class="sxs-lookup"><span data-stu-id="16091-486">The following table shows the default [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) available when creating `HttpClient` instances.</span></span>

| <span data-ttu-id="16091-487">选项</span><span class="sxs-lookup"><span data-stu-id="16091-487">Option</span></span> | <span data-ttu-id="16091-488">描述</span><span class="sxs-lookup"><span data-stu-id="16091-488">Description</span></span> | <span data-ttu-id="16091-489">Default</span><span class="sxs-lookup"><span data-stu-id="16091-489">Default</span></span> |
| ------ | ----------- | ------- |
| [<span data-ttu-id="16091-490">AllowAutoRedirect</span><span class="sxs-lookup"><span data-stu-id="16091-490">AllowAutoRedirect</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) | <span data-ttu-id="16091-491">获取或设置 `HttpClient` 实例是否应自动跟随重定向响应。</span><span class="sxs-lookup"><span data-stu-id="16091-491">Gets or sets whether or not `HttpClient` instances should automatically follow redirect responses.</span></span> | `true` |
| [<span data-ttu-id="16091-492">BaseAddress</span><span class="sxs-lookup"><span data-stu-id="16091-492">BaseAddress</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.baseaddress) | <span data-ttu-id="16091-493">获取或设置 `HttpClient` 实例的基址。</span><span class="sxs-lookup"><span data-stu-id="16091-493">Gets or sets the base address of `HttpClient` instances.</span></span> | `http://localhost` |
| [<span data-ttu-id="16091-494">HandleCookies</span><span class="sxs-lookup"><span data-stu-id="16091-494">HandleCookies</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.handlecookies) | <span data-ttu-id="16091-495">获取或设置 `HttpClient` 实例是否应处理 cookie。</span><span class="sxs-lookup"><span data-stu-id="16091-495">Gets or sets whether `HttpClient` instances should handle cookies.</span></span> | `true` |
| [<span data-ttu-id="16091-496">MaxAutomaticRedirections</span><span class="sxs-lookup"><span data-stu-id="16091-496">MaxAutomaticRedirections</span></span>](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.maxautomaticredirections) | <span data-ttu-id="16091-497">获取或设置 `HttpClient` 实例应遵循的重定向响应的最大数目。</span><span class="sxs-lookup"><span data-stu-id="16091-497">Gets or sets the maximum number of redirect responses that `HttpClient` instances should follow.</span></span> | <span data-ttu-id="16091-498">7</span><span class="sxs-lookup"><span data-stu-id="16091-498">7</span></span> |

<span data-ttu-id="16091-499">创建 `WebApplicationFactoryClientOptions` 类并将其传递给[CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient)方法（在代码示例中显示默认值）：</span><span class="sxs-lookup"><span data-stu-id="16091-499">Create the `WebApplicationFactoryClientOptions` class and pass it to the [CreateClient](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1.createclient) method (default values are shown in the code example):</span></span>

```csharp
// Default client option values are shown
var clientOptions = new WebApplicationFactoryClientOptions();
clientOptions.AllowAutoRedirect = true;
clientOptions.BaseAddress = new Uri("http://localhost");
clientOptions.HandleCookies = true;
clientOptions.MaxAutomaticRedirections = 7;

_client = _factory.CreateClient(clientOptions);
```

## <a name="inject-mock-services"></a><span data-ttu-id="16091-500">注入模拟服务</span><span class="sxs-lookup"><span data-stu-id="16091-500">Inject mock services</span></span>

<span data-ttu-id="16091-501">通过在主机生成器上调用[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) ，可以在测试中覆盖服务。</span><span class="sxs-lookup"><span data-stu-id="16091-501">Services can be overridden in a test with a call to [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) on the host builder.</span></span> <span data-ttu-id="16091-502">**若要注入模拟服务，SUT 必须有一个具有 `Startup.ConfigureServices` 方法的 `Startup` 类。**</span><span class="sxs-lookup"><span data-stu-id="16091-502">**To inject mock services, the SUT must have a `Startup` class with a `Startup.ConfigureServices` method.**</span></span>

<span data-ttu-id="16091-503">示例 SUT 包含一个返回 quote 的作用域服务。</span><span class="sxs-lookup"><span data-stu-id="16091-503">The sample SUT includes a scoped service that returns a quote.</span></span> <span data-ttu-id="16091-504">请求索引页时，引号嵌入到索引页上的隐藏字段中。</span><span class="sxs-lookup"><span data-stu-id="16091-504">The quote is embedded in a hidden field on the Index page when the Index page is requested.</span></span>

<span data-ttu-id="16091-505">*服务/IQuoteService*：</span><span class="sxs-lookup"><span data-stu-id="16091-505">*Services/IQuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/IQuoteService.cs?name=snippet1)]

<span data-ttu-id="16091-506">*服务/QuoteService*：</span><span class="sxs-lookup"><span data-stu-id="16091-506">*Services/QuoteService.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Services/QuoteService.cs?name=snippet1)]

<span data-ttu-id="16091-507">*Startup.cs*：</span><span class="sxs-lookup"><span data-stu-id="16091-507">*Startup.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet2)]

<span data-ttu-id="16091-508">Pages/Index.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="16091-508">*Pages/Index.cshtml.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml.cs?name=snippet1&highlight=4,9,20,26)]

<span data-ttu-id="16091-509">*Pages/Index. cs*：</span><span class="sxs-lookup"><span data-stu-id="16091-509">*Pages/Index.cs*:</span></span>

[!code-cshtml[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Pages/Index.cshtml?name=snippet_Quote)]

<span data-ttu-id="16091-510">运行 SUT 应用时，将生成以下标记：</span><span class="sxs-lookup"><span data-stu-id="16091-510">The following markup is generated when the SUT app is run:</span></span>

```html
<input id="quote" type="hidden" value="Come on, Sarah. We&#x27;ve an appointment in 
    London, and we&#x27;re already 30,000 years late.">
```

<span data-ttu-id="16091-511">若要在集成测试中测试服务和引号注入，测试会将模拟服务注入到 SUT。</span><span class="sxs-lookup"><span data-stu-id="16091-511">To test the service and quote injection in an integration test, a mock service is injected into the SUT by the test.</span></span> <span data-ttu-id="16091-512">模拟服务会将应用的 `QuoteService` 替换为测试应用提供的服务，称为 `TestQuoteService`：</span><span class="sxs-lookup"><span data-stu-id="16091-512">The mock service replaces the app's `QuoteService` with a service provided by the test app, called `TestQuoteService`:</span></span>

<span data-ttu-id="16091-513">*IntegrationTests.IndexPageTests.cs*：</span><span class="sxs-lookup"><span data-stu-id="16091-513">*IntegrationTests.IndexPageTests.cs*:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet4)]

<span data-ttu-id="16091-514">调用 `ConfigureTestServices`，并注册作用域内服务：</span><span class="sxs-lookup"><span data-stu-id="16091-514">`ConfigureTestServices` is called, and the scoped service is registered:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/IndexPageTests.cs?name=snippet5&highlight=7-10,17,20-21)]

<span data-ttu-id="16091-515">在测试执行过程中生成的标记反映 `TestQuoteService`提供的引号文本，因此断言通过：</span><span class="sxs-lookup"><span data-stu-id="16091-515">The markup produced during the test's execution reflects the quote text supplied by `TestQuoteService`, thus the assertion passes:</span></span>

```html
<input id="quote" type="hidden" value="Something&#x27;s interfering with time, 
    Mr. Scarman, and time is my business.">
```

## <a name="mock-authentication"></a><span data-ttu-id="16091-516">模拟身份验证</span><span class="sxs-lookup"><span data-stu-id="16091-516">Mock authentication</span></span>

<span data-ttu-id="16091-517">`AuthTests` 类中的测试检查安全终结点：</span><span class="sxs-lookup"><span data-stu-id="16091-517">Tests in the `AuthTests` class check that a secure endpoint:</span></span>

* <span data-ttu-id="16091-518">将未经身份验证的用户重定向到应用程序的登录页。</span><span class="sxs-lookup"><span data-stu-id="16091-518">Redirects an unauthenticated user to the app's Login page.</span></span>
* <span data-ttu-id="16091-519">返回已经过身份验证的用户的内容。</span><span class="sxs-lookup"><span data-stu-id="16091-519">Returns content for an authenticated user.</span></span>

<span data-ttu-id="16091-520">在 SUT 中，`/SecurePage` 页面使用[AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage)约定将[AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter)应用到页面。</span><span class="sxs-lookup"><span data-stu-id="16091-520">In the SUT, the `/SecurePage` page uses an [AuthorizePage](/dotnet/api/microsoft.extensions.dependencyinjection.pageconventioncollectionextensions.authorizepage) convention to apply an [AuthorizeFilter](/dotnet/api/microsoft.aspnetcore.mvc.authorization.authorizefilter) to the page.</span></span> <span data-ttu-id="16091-521">有关详细信息，请参阅[Razor Pages 授权约定](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page)。</span><span class="sxs-lookup"><span data-stu-id="16091-521">For more information, see [Razor Pages authorization conventions](xref:security/authorization/razor-pages-authorization#require-authorization-to-access-a-page).</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/src/RazorPagesProject/Startup.cs?name=snippet1)]

<span data-ttu-id="16091-522">在 `Get_SecurePageRedirectsAnUnauthenticatedUser` 测试中，通过将[AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect)设置为 `false`，将[WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions)设置为禁止重定向：</span><span class="sxs-lookup"><span data-stu-id="16091-522">In the `Get_SecurePageRedirectsAnUnauthenticatedUser` test, a [WebApplicationFactoryClientOptions](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions) is set to disallow redirects by setting [AllowAutoRedirect](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactoryclientoptions.allowautoredirect) to `false`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet2)]

<span data-ttu-id="16091-523">通过禁止客户端按照重定向操作，可以执行以下检查：</span><span class="sxs-lookup"><span data-stu-id="16091-523">By disallowing the client to follow the redirect, the following checks can be made:</span></span>

* <span data-ttu-id="16091-524">可以对照预期的[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)结果检查 SUT 返回的状态代码，而不是在重定向到登录页后返回最终状态代码，这会是[HttpStatusCode](/dotnet/api/system.net.httpstatuscode)。</span><span class="sxs-lookup"><span data-stu-id="16091-524">The status code returned by the SUT can be checked against the expected [HttpStatusCode.Redirect](/dotnet/api/system.net.httpstatuscode) result, not the final status code after the redirect to the Login page, which would be [HttpStatusCode.OK](/dotnet/api/system.net.httpstatuscode).</span></span>
* <span data-ttu-id="16091-525">将检查响应标头中的 `Location` 标头值，以确认该标头值以 `http://localhost/Identity/Account/Login`（而不是最终的登录页响应）开头，而 `Location` 标头不存在。</span><span class="sxs-lookup"><span data-stu-id="16091-525">The `Location` header value in the response headers is checked to confirm that it starts with `http://localhost/Identity/Account/Login`, not the final Login page response, where the `Location` header wouldn't be present.</span></span>

<span data-ttu-id="16091-526">测试应用可以模拟[ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices)中的 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>，以便测试身份验证和授权的各个方面。</span><span class="sxs-lookup"><span data-stu-id="16091-526">The test app can mock an <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1> in [ConfigureTestServices](/dotnet/api/microsoft.aspnetcore.testhost.webhostbuilderextensions.configuretestservices) in order to test aspects of authentication and authorization.</span></span> <span data-ttu-id="16091-527">最小方案返回[AuthenticateResult](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*)：</span><span class="sxs-lookup"><span data-stu-id="16091-527">A minimal scenario returns an [AuthenticateResult.Success](xref:Microsoft.AspNetCore.Authentication.AuthenticateResult.Success*):</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet4&highlight=11-18)]

<span data-ttu-id="16091-528">当身份验证方案设置为 `Test` 在其中向 `ConfigureTestServices`注册了 `AddAuthentication` 时，将调用 `TestAuthHandler` 来对用户进行身份验证：</span><span class="sxs-lookup"><span data-stu-id="16091-528">The `TestAuthHandler` is called to authenticate a user when the authentication scheme is set to `Test` where `AddAuthentication` is registered for `ConfigureTestServices`:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/IntegrationTests/AuthTests.cs?name=snippet3&highlight=7-12)]

<span data-ttu-id="16091-529">有关 `WebApplicationFactoryClientOptions` 的详细信息，请参阅[Client options](#client-options)部分。</span><span class="sxs-lookup"><span data-stu-id="16091-529">For more information on `WebApplicationFactoryClientOptions`, see the [Client options](#client-options) section.</span></span>

## <a name="set-the-environment"></a><span data-ttu-id="16091-530">设置环境</span><span class="sxs-lookup"><span data-stu-id="16091-530">Set the environment</span></span>

<span data-ttu-id="16091-531">默认情况下，SUT 的主机和应用环境配置为使用开发环境。</span><span class="sxs-lookup"><span data-stu-id="16091-531">By default, the SUT's host and app environment is configured to use the Development environment.</span></span> <span data-ttu-id="16091-532">重写 SUT 的环境：</span><span class="sxs-lookup"><span data-stu-id="16091-532">To override the SUT's environment:</span></span>

* <span data-ttu-id="16091-533">设置 `ASPNETCORE_ENVIRONMENT` 环境变量（例如，`Staging`、`Production`或其他自定义值，例如 `Testing`）。</span><span class="sxs-lookup"><span data-stu-id="16091-533">Set the `ASPNETCORE_ENVIRONMENT` environment variable (for example, `Staging`, `Production`, or other custom value, such as `Testing`).</span></span>
* <span data-ttu-id="16091-534">重写测试应用中 `CreateHostBuilder`，以读取以 `ASPNETCORE`为前缀的环境变量。</span><span class="sxs-lookup"><span data-stu-id="16091-534">Override `CreateHostBuilder` in the test app to read environment variables prefixed with `ASPNETCORE`.</span></span>

```csharp
protected override IHostBuilder CreateHostBuilder() => 
    base.CreateHostBuilder()
        .ConfigureHostConfiguration(
            config => config.AddEnvironmentVariables("ASPNETCORE"));
```

## <a name="how-the-test-infrastructure-infers-the-app-content-root-path"></a><span data-ttu-id="16091-535">测试基础结构如何推断应用内容根路径</span><span class="sxs-lookup"><span data-stu-id="16091-535">How the test infrastructure infers the app content root path</span></span>

<span data-ttu-id="16091-536">`WebApplicationFactory` 构造函数通过使用等于 `TEntryPoint` 程序集 `System.Reflection.Assembly.FullName`的键搜索包含集成测试的程序集上的[WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute)来推断应用[内容根](xref:fundamentals/index#content-root)路径。</span><span class="sxs-lookup"><span data-stu-id="16091-536">The `WebApplicationFactory` constructor infers the app [content root](xref:fundamentals/index#content-root) path by searching for a [WebApplicationFactoryContentRootAttribute](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactorycontentrootattribute) on the assembly containing the integration tests with a key equal to the `TEntryPoint` assembly `System.Reflection.Assembly.FullName`.</span></span> <span data-ttu-id="16091-537">如果找不到具有正确键的属性，`WebApplicationFactory` 将回退到搜索解决方案文件（ *.sln*），并在解决方案目录中追加 `TEntryPoint` 程序集名称。</span><span class="sxs-lookup"><span data-stu-id="16091-537">In case an attribute with the correct key isn't found, `WebApplicationFactory` falls back to searching for a solution file (*.sln*) and appends the `TEntryPoint` assembly name to the solution directory.</span></span> <span data-ttu-id="16091-538">应用根目录（内容根路径）用于发现视图和内容文件。</span><span class="sxs-lookup"><span data-stu-id="16091-538">The app root directory (the content root path) is used to discover views and content files.</span></span>

## <a name="disable-shadow-copying"></a><span data-ttu-id="16091-539">禁用卷影复制</span><span class="sxs-lookup"><span data-stu-id="16091-539">Disable shadow copying</span></span>

<span data-ttu-id="16091-540">卷影复制会导致在输出目录以外的目录中执行测试。</span><span class="sxs-lookup"><span data-stu-id="16091-540">Shadow copying causes the tests to execute in a different directory than the output directory.</span></span> <span data-ttu-id="16091-541">要使测试正常工作，必须禁用卷影复制。</span><span class="sxs-lookup"><span data-stu-id="16091-541">For tests to work properly, shadow copying must be disabled.</span></span> <span data-ttu-id="16091-542">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)使用 xUnit 并通过包含具有正确配置设置的*xUnit*文件来禁用 xUnit 的卷影复制。</span><span class="sxs-lookup"><span data-stu-id="16091-542">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) uses xUnit and disables shadow copying for xUnit by including an *xunit.runner.json* file with the correct configuration setting.</span></span> <span data-ttu-id="16091-543">有关详细信息，请参阅[配置 xUnit 与 JSON](https://xunit.github.io/docs/configuring-with-json.html)。</span><span class="sxs-lookup"><span data-stu-id="16091-543">For more information, see [Configuring xUnit with JSON](https://xunit.github.io/docs/configuring-with-json.html).</span></span>

<span data-ttu-id="16091-544">将*xunit*文件添加到测试项目的根，其中包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="16091-544">Add the *xunit.runner.json* file to root of the test project with the following content:</span></span>

```json
{
  "shadowCopy": false
}
```

<span data-ttu-id="16091-545">如果使用的是 Visual Studio，请将文件的 "**复制到输出目录**" 属性设置为 "**始终复制**"。</span><span class="sxs-lookup"><span data-stu-id="16091-545">If using Visual Studio, set the file's **Copy to Output Directory** property to **Copy always**.</span></span> <span data-ttu-id="16091-546">如果不使用 Visual Studio，请将 `Content` 目标添加到测试应用的项目文件中：</span><span class="sxs-lookup"><span data-stu-id="16091-546">If not using Visual Studio, add a `Content` target to the test app's project file:</span></span>

```xml
<ItemGroup>
  <Content Update="xunit.runner.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
  </Content>
</ItemGroup>
```

## <a name="disposal-of-objects"></a><span data-ttu-id="16091-547">对象的处理</span><span class="sxs-lookup"><span data-stu-id="16091-547">Disposal of objects</span></span>

<span data-ttu-id="16091-548">执行 `IClassFixture` 实现的测试后，当 xUnit 释放[WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1)时，将释放[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)和[HttpClient](/dotnet/api/system.net.http.httpclient) 。</span><span class="sxs-lookup"><span data-stu-id="16091-548">After the tests of the `IClassFixture` implementation are executed, [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver) and [HttpClient](/dotnet/api/system.net.http.httpclient) are disposed when xUnit disposes of the [WebApplicationFactory](/dotnet/api/microsoft.aspnetcore.mvc.testing.webapplicationfactory-1).</span></span> <span data-ttu-id="16091-549">如果开发人员实例化的对象需要处置，请在 `IClassFixture` 实现中释放这些对象。</span><span class="sxs-lookup"><span data-stu-id="16091-549">If objects instantiated by the developer require disposal, dispose of them in the `IClassFixture` implementation.</span></span> <span data-ttu-id="16091-550">有关详细信息，请参阅[实现 Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。</span><span class="sxs-lookup"><span data-stu-id="16091-550">For more information, see [Implementing a Dispose method](/dotnet/standard/garbage-collection/implementing-dispose).</span></span>

## <a name="integration-tests-sample"></a><span data-ttu-id="16091-551">集成测试示例</span><span class="sxs-lookup"><span data-stu-id="16091-551">Integration tests sample</span></span>

<span data-ttu-id="16091-552">该[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples)由两个应用组成：</span><span class="sxs-lookup"><span data-stu-id="16091-552">The [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/test/integration-tests/samples) is composed of two apps:</span></span>

| <span data-ttu-id="16091-553">应用</span><span class="sxs-lookup"><span data-stu-id="16091-553">App</span></span> | <span data-ttu-id="16091-554">项目目录</span><span class="sxs-lookup"><span data-stu-id="16091-554">Project directory</span></span> | <span data-ttu-id="16091-555">描述</span><span class="sxs-lookup"><span data-stu-id="16091-555">Description</span></span> |
| --- | ----------------- | ----------- |
| <span data-ttu-id="16091-556">消息应用（SUT）</span><span class="sxs-lookup"><span data-stu-id="16091-556">Message app (the SUT)</span></span> | <span data-ttu-id="16091-557">*src/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="16091-557">*src/RazorPagesProject*</span></span> | <span data-ttu-id="16091-558">允许用户添加、删除一个、删除和分析消息。</span><span class="sxs-lookup"><span data-stu-id="16091-558">Allows a user to add, delete one, delete all, and analyze messages.</span></span> |
| <span data-ttu-id="16091-559">测试应用</span><span class="sxs-lookup"><span data-stu-id="16091-559">Test app</span></span> | <span data-ttu-id="16091-560">*测试/RazorPagesProject*</span><span class="sxs-lookup"><span data-stu-id="16091-560">*tests/RazorPagesProject.Tests*</span></span> | <span data-ttu-id="16091-561">用于集成测试 SUT。</span><span class="sxs-lookup"><span data-stu-id="16091-561">Used to integration test the SUT.</span></span> |

<span data-ttu-id="16091-562">可以使用 IDE （如[Visual Studio](https://visualstudio.microsoft.com)）的内置测试功能来运行测试。</span><span class="sxs-lookup"><span data-stu-id="16091-562">The tests can be run using the built-in test features of an IDE, such as [Visual Studio](https://visualstudio.microsoft.com).</span></span> <span data-ttu-id="16091-563">如果使用[Visual Studio Code](https://code.visualstudio.com/)或命令行，请在*RazorPagesProject 目录中的命令*提示符处执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="16091-563">If using [Visual Studio Code](https://code.visualstudio.com/) or the command line, execute the following command at a command prompt in the *tests/RazorPagesProject.Tests* directory:</span></span>

```dotnetcli
dotnet test
```

### <a name="message-app-sut-organization"></a><span data-ttu-id="16091-564">消息应用（SUT）组织</span><span class="sxs-lookup"><span data-stu-id="16091-564">Message app (SUT) organization</span></span>

<span data-ttu-id="16091-565">SUT 是 Razor Pages 的消息系统，具有以下特征：</span><span class="sxs-lookup"><span data-stu-id="16091-565">The SUT is a Razor Pages message system with the following characteristics:</span></span>

* <span data-ttu-id="16091-566">应用的 "索引" 页（*Pages/索引. cshtml*和*pages/index*）提供了一个 UI 和页面模型方法，用于控制消息的添加、删除和分析（每条消息的平均单词数）。</span><span class="sxs-lookup"><span data-stu-id="16091-566">The Index page of the app (*Pages/Index.cshtml* and *Pages/Index.cshtml.cs*) provides a UI and page model methods to control the addition, deletion, and analysis of messages (average words per message).</span></span>
* <span data-ttu-id="16091-567">消息由 `Message` 类（*Data/message*）描述，具有两个属性： `Id` （密钥）和 `Text` （message）。</span><span class="sxs-lookup"><span data-stu-id="16091-567">A message is described by the `Message` class (*Data/Message.cs*) with two properties: `Id` (key) and `Text` (message).</span></span> <span data-ttu-id="16091-568">`Text` 属性是必需的，并且限制为200个字符。</span><span class="sxs-lookup"><span data-stu-id="16091-568">The `Text` property is required and limited to 200 characters.</span></span>
* <span data-ttu-id="16091-569">使用[实体框架的内存中数据库](/ef/core/providers/in-memory/)&#8224;来存储消息。</span><span class="sxs-lookup"><span data-stu-id="16091-569">Messages are stored using [Entity Framework's in-memory database](/ef/core/providers/in-memory/)&#8224;.</span></span>
* <span data-ttu-id="16091-570">应用在其数据库上下文类中包含数据访问层（DAL），`AppDbContext` （*data/AppDbContext*）。</span><span class="sxs-lookup"><span data-stu-id="16091-570">The app contains a data access layer (DAL) in its database context class, `AppDbContext` (*Data/AppDbContext.cs*).</span></span>
* <span data-ttu-id="16091-571">如果数据库在应用启动时为空，则会用三条消息初始化消息存储。</span><span class="sxs-lookup"><span data-stu-id="16091-571">If the database is empty on app startup, the message store is initialized with three messages.</span></span>
* <span data-ttu-id="16091-572">应用包括只能由经过身份验证的用户访问的 `/SecurePage`。</span><span class="sxs-lookup"><span data-stu-id="16091-572">The app includes a `/SecurePage` that can only be accessed by an authenticated user.</span></span>

<span data-ttu-id="16091-573">&#8224;EF 主题[使用 InMemory 进行测试](/ef/core/miscellaneous/testing/in-memory)说明了如何将内存中数据库用于使用 MSTest 进行测试。</span><span class="sxs-lookup"><span data-stu-id="16091-573">&#8224;The EF topic, [Test with InMemory](/ef/core/miscellaneous/testing/in-memory), explains how to use an in-memory database for tests with MSTest.</span></span> <span data-ttu-id="16091-574">本主题使用[xUnit](https://xunit.github.io/)测试框架。</span><span class="sxs-lookup"><span data-stu-id="16091-574">This topic uses the [xUnit](https://xunit.github.io/) test framework.</span></span> <span data-ttu-id="16091-575">不同测试框架中的测试概念和测试实现相似，但并不完全相同。</span><span class="sxs-lookup"><span data-stu-id="16091-575">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span>

<span data-ttu-id="16091-576">尽管应用程序不使用存储库模式，并且不是[工作单元（UoW）模式](https://martinfowler.com/eaaCatalog/unitOfWork.html)的有效示例，但 Razor Pages 支持这些模式的开发模式。</span><span class="sxs-lookup"><span data-stu-id="16091-576">Although the app doesn't use the repository pattern and isn't an effective example of the [Unit of Work (UoW) pattern](https://martinfowler.com/eaaCatalog/unitOfWork.html), Razor Pages supports these patterns of development.</span></span> <span data-ttu-id="16091-577">有关详细信息，请参阅[设计基础结构持久性层](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和[测试控制器逻辑](/aspnet/core/mvc/controllers/testing)（示例实现存储库模式）。</span><span class="sxs-lookup"><span data-stu-id="16091-577">For more information, see [Designing the infrastructure persistence layer](/dotnet/standard/microservices-architecture/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design) and [Test controller logic](/aspnet/core/mvc/controllers/testing) (the sample implements the repository pattern).</span></span>

### <a name="test-app-organization"></a><span data-ttu-id="16091-578">测试应用组织</span><span class="sxs-lookup"><span data-stu-id="16091-578">Test app organization</span></span>

<span data-ttu-id="16091-579">测试应用是 "*测试"/"RazorPagesProject* " 目录中的控制台应用。</span><span class="sxs-lookup"><span data-stu-id="16091-579">The test app is a console app inside the *tests/RazorPagesProject.Tests* directory.</span></span>

| <span data-ttu-id="16091-580">测试应用程序目录</span><span class="sxs-lookup"><span data-stu-id="16091-580">Test app directory</span></span> | <span data-ttu-id="16091-581">描述</span><span class="sxs-lookup"><span data-stu-id="16091-581">Description</span></span> |
| ------------------ | ----------- |
| <span data-ttu-id="16091-582">*AuthTests*</span><span class="sxs-lookup"><span data-stu-id="16091-582">*AuthTests*</span></span> | <span data-ttu-id="16091-583">包含的测试方法：</span><span class="sxs-lookup"><span data-stu-id="16091-583">Contains test methods for:</span></span><ul><li><span data-ttu-id="16091-584">通过未经身份验证的用户访问安全页。</span><span class="sxs-lookup"><span data-stu-id="16091-584">Accessing a secure page by an unauthenticated user.</span></span></li><li><span data-ttu-id="16091-585">使用模拟 <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>通过经过身份验证的用户访问安全页。</span><span class="sxs-lookup"><span data-stu-id="16091-585">Accessing a secure page by an authenticated user with a mock <xref:Microsoft.AspNetCore.Authentication.AuthenticationHandler`1>.</span></span></li><li><span data-ttu-id="16091-586">获取 GitHub 用户配置文件，并检查配置文件的用户登录名。</span><span class="sxs-lookup"><span data-stu-id="16091-586">Obtaining a GitHub user profile and checking the profile's user login.</span></span></li></ul> |
| <span data-ttu-id="16091-587">*BasicTests*</span><span class="sxs-lookup"><span data-stu-id="16091-587">*BasicTests*</span></span> | <span data-ttu-id="16091-588">包含路由和内容类型的测试方法。</span><span class="sxs-lookup"><span data-stu-id="16091-588">Contains a test method for routing and content type.</span></span> |
| <span data-ttu-id="16091-589">*IntegrationTests*</span><span class="sxs-lookup"><span data-stu-id="16091-589">*IntegrationTests*</span></span> | <span data-ttu-id="16091-590">包含使用自定义 `WebApplicationFactory` 类的索引页的集成测试。</span><span class="sxs-lookup"><span data-stu-id="16091-590">Contains the integration tests for the Index page using custom `WebApplicationFactory` class.</span></span> |
| <span data-ttu-id="16091-591">*帮助程序/实用工具*</span><span class="sxs-lookup"><span data-stu-id="16091-591">*Helpers/Utilities*</span></span> | <ul><li><span data-ttu-id="16091-592">*Utilities.cs*包含用于使用测试数据对数据库进行种子设定的 `InitializeDbForTests` 方法。</span><span class="sxs-lookup"><span data-stu-id="16091-592">*Utilities.cs* contains the `InitializeDbForTests` method used to seed the database with test data.</span></span></li><li><span data-ttu-id="16091-593">*HtmlHelpers.cs*提供了一个方法，用于返回 AngleSharp `IHtmlDocument` 以供测试方法使用。</span><span class="sxs-lookup"><span data-stu-id="16091-593">*HtmlHelpers.cs* provides a method to return an AngleSharp `IHtmlDocument` for use by the test methods.</span></span></li><li><span data-ttu-id="16091-594">*HttpClientExtensions.cs*提供 `SendAsync` 的重载，以将请求提交到 SUT。</span><span class="sxs-lookup"><span data-stu-id="16091-594">*HttpClientExtensions.cs* provide overloads for `SendAsync` to submit requests to the SUT.</span></span></li></ul> |

<span data-ttu-id="16091-595">测试框架为[xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="16091-595">The test framework is [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="16091-596">集成测试是使用 TestHost 的[AspNetCore](/dotnet/api/microsoft.aspnetcore.testhost)进行的，其中包括[TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver)。</span><span class="sxs-lookup"><span data-stu-id="16091-596">Integration tests are conducted using the [Microsoft.AspNetCore.TestHost](/dotnet/api/microsoft.aspnetcore.testhost), which includes the [TestServer](/dotnet/api/microsoft.aspnetcore.testhost.testserver).</span></span> <span data-ttu-id="16091-597">由于[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing)包用于配置测试主机和测试服务器，因此，在测试应用程序的项目文件或测试的开发人员配置中，`TestHost` 和 `TestServer` 包不需要直接包引用应用.</span><span class="sxs-lookup"><span data-stu-id="16091-597">Because the [Microsoft.AspNetCore.Mvc.Testing](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.Testing) package is used to configure the test host and test server, the `TestHost` and `TestServer` packages don't require direct package references in the test app's project file or developer configuration in the test app.</span></span>

<span data-ttu-id="16091-598">**播种要测试的数据库**</span><span class="sxs-lookup"><span data-stu-id="16091-598">**Seeding the database for testing**</span></span>

<span data-ttu-id="16091-599">集成测试在执行测试前通常需要数据库中的一个小型数据集。</span><span class="sxs-lookup"><span data-stu-id="16091-599">Integration tests usually require a small dataset in the database prior to the test execution.</span></span> <span data-ttu-id="16091-600">例如，删除测试调用数据库记录，因此数据库必须至少有一条记录，删除请求才能成功。</span><span class="sxs-lookup"><span data-stu-id="16091-600">For example, a delete test calls for a database record deletion, so the database must have at least one record for the delete request to succeed.</span></span>

<span data-ttu-id="16091-601">该示例应用在*Utilities.cs*中使用三个消息对数据库进行种子设定，当测试执行时，可以使用这些消息：</span><span class="sxs-lookup"><span data-stu-id="16091-601">The sample app seeds the database with three messages in *Utilities.cs* that tests can use when they execute:</span></span>

[!code-csharp[](integration-tests/samples/2.x/IntegrationTestsSample/tests/RazorPagesProject.Tests/Helpers/Utilities.cs?name=snippet1)]

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="16091-602">其他资源</span><span class="sxs-lookup"><span data-stu-id="16091-602">Additional resources</span></span>

* [<span data-ttu-id="16091-603">单元测试</span><span class="sxs-lookup"><span data-stu-id="16091-603">Unit tests</span></span>](/dotnet/articles/core/testing/unit-testing-with-dotnet-test)
* <xref:test/razor-pages-tests>
* <xref:fundamentals/middleware/index>
* <xref:mvc/controllers/testing>
