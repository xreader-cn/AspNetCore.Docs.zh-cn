---
title: 在 ASP.NET Core Blazor 中测试组件
author: guardrex
description: 了解如何在 Blazor 应用中测试组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/test
ms.openlocfilehash: 67ebfcd322ae08acf2fddae9bd6101f13fa77e7e
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280711"
---
# <a name="test-components-in-aspnet-core-blazor"></a><span data-ttu-id="07f11-103">在 ASP.NET Core Blazor 中测试组件</span><span class="sxs-lookup"><span data-stu-id="07f11-103">Test components in ASP.NET Core Blazor</span></span>

<span data-ttu-id="07f11-104">作者：[Egil Hansen](https://egilhansen.com/)</span><span class="sxs-lookup"><span data-stu-id="07f11-104">By: [Egil Hansen](https://egilhansen.com/)</span></span>

<span data-ttu-id="07f11-105">测试是构建稳定且可维护的软件的一个重要方面。</span><span class="sxs-lookup"><span data-stu-id="07f11-105">Testing is an important aspect of building stable and maintainable software.</span></span>

<span data-ttu-id="07f11-106">若要测试 Blazor 组件，则测试中的组件 (CUT)：</span><span class="sxs-lookup"><span data-stu-id="07f11-106">To test a Blazor component, the *Component Under Test* (CUT) is:</span></span>

* <span data-ttu-id="07f11-107">使用测试的相关输入进行呈现。</span><span class="sxs-lookup"><span data-stu-id="07f11-107">Rendered with relevant input for the test.</span></span>
* <span data-ttu-id="07f11-108">根据执行的测试类型，可能会进行交互或修改。</span><span class="sxs-lookup"><span data-stu-id="07f11-108">Depending on the type of test performed, possibly subject to interaction or modification.</span></span> <span data-ttu-id="07f11-109">例如，事件处理程序可能会被触发，如按钮的 `onclick` 事件。</span><span class="sxs-lookup"><span data-stu-id="07f11-109">For example, event handlers can be triggered, such as an `onclick` event for a button.</span></span>
* <span data-ttu-id="07f11-110">已检查预期的值。</span><span class="sxs-lookup"><span data-stu-id="07f11-110">Inspected for expected values.</span></span>

## <a name="test-approaches"></a><span data-ttu-id="07f11-111">测试方法</span><span class="sxs-lookup"><span data-stu-id="07f11-111">Test approaches</span></span>

<span data-ttu-id="07f11-112">测试 Blazor 组件的两种常见方法是端到端 (E2E) 测试和单元测试：</span><span class="sxs-lookup"><span data-stu-id="07f11-112">Two common approaches for testing Blazor components are end-to-end (E2E) testing and unit testing:</span></span>

* <span data-ttu-id="07f11-113">**单元测试**：[单元测试](/dotnet/core/testing/)使用单元测试库编写，该库提供：</span><span class="sxs-lookup"><span data-stu-id="07f11-113">**Unit testing**: [Unit tests](/dotnet/core/testing/) are written with a unit testing library that provides:</span></span>
  * <span data-ttu-id="07f11-114">组件呈现。</span><span class="sxs-lookup"><span data-stu-id="07f11-114">Component rendering.</span></span>
  * <span data-ttu-id="07f11-115">组件输出和状态的检查。</span><span class="sxs-lookup"><span data-stu-id="07f11-115">Inspection of component output and state.</span></span>
  * <span data-ttu-id="07f11-116">事件处理程序和生命周期方法的触发。</span><span class="sxs-lookup"><span data-stu-id="07f11-116">Triggering of event handlers and life cycle methods.</span></span>
  * <span data-ttu-id="07f11-117">关于组件行为正确的断言。</span><span class="sxs-lookup"><span data-stu-id="07f11-117">Assertions that component behavior is correct.</span></span>

  <span data-ttu-id="07f11-118">[bUnit](https://github.com/egil/bUnit) 就是启用 Razor 组件单元测试的一个库。</span><span class="sxs-lookup"><span data-stu-id="07f11-118">[bUnit](https://github.com/egil/bUnit) is an example of a library that enables Razor component unit testing.</span></span>

* <span data-ttu-id="07f11-119">**E2E 测试**：测试运行程序会运行包含 CUT 的 Blazor 应用，并自动执行浏览器实例。</span><span class="sxs-lookup"><span data-stu-id="07f11-119">**E2E testing**: A test runner runs a Blazor app containing the CUT and automates a browser instance.</span></span> <span data-ttu-id="07f11-120">测试工具通过浏览器进行检查并与 CUT 交互。</span><span class="sxs-lookup"><span data-stu-id="07f11-120">The testing tool inspects and interacts with the CUT through the browser.</span></span> <span data-ttu-id="07f11-121">[Selenium](https://github.com/SeleniumHQ/selenium) 就是一个可用于 Blazor 应用的 E2E 测试框架。</span><span class="sxs-lookup"><span data-stu-id="07f11-121">[Selenium](https://github.com/SeleniumHQ/selenium) is an example of an E2E testing framework that can be used with Blazor apps.</span></span>

<span data-ttu-id="07f11-122">在单元测试中，仅涉及到 Blazor 组件 (Razor/C#)。</span><span class="sxs-lookup"><span data-stu-id="07f11-122">In unit testing, only the Blazor component (Razor/C#) is involved.</span></span> <span data-ttu-id="07f11-123">必须模拟外部依赖项（如服务和 JS 互操作）。</span><span class="sxs-lookup"><span data-stu-id="07f11-123">External dependencies, such as services and JS interop, must be mocked.</span></span> <span data-ttu-id="07f11-124">在 E2E 测试中，Blazor 组件及其所有辅助基础结构是测试的一部分，包括 CSS、JS 以及 DOM 和浏览器 API。</span><span class="sxs-lookup"><span data-stu-id="07f11-124">In E2E testing, the Blazor component and all of it's auxiliary infrastructure are part of the test, including CSS, JS, and the DOM and browser APIs.</span></span>

<span data-ttu-id="07f11-125">“测试范围”描述了测试的范围。</span><span class="sxs-lookup"><span data-stu-id="07f11-125">*Test scope* describes how extensive the tests are.</span></span> <span data-ttu-id="07f11-126">测试范围通常会影响测试的速度。</span><span class="sxs-lookup"><span data-stu-id="07f11-126">Test scope typically has an influence on the speed of the tests.</span></span> <span data-ttu-id="07f11-127">单元测试在应用的部分子系统上运行，通常以毫秒为单位执行。</span><span class="sxs-lookup"><span data-stu-id="07f11-127">Unit tests run on a subset of the app's subsystems and usually execute in milliseconds.</span></span> <span data-ttu-id="07f11-128">E2E 测试会测试应用的大量子系统，可能需要几秒钟才能完成。</span><span class="sxs-lookup"><span data-stu-id="07f11-128">E2E tests, which test a broad group of the app's subsystems, can take several seconds to complete.</span></span> 

<span data-ttu-id="07f11-129">单元测试还提供对 CUT 实例的访问，从而允许组件内部状态的检查和验证。</span><span class="sxs-lookup"><span data-stu-id="07f11-129">Unit testing also provides access to the instance of the CUT, allowing for inspection and verification of the component's internal state.</span></span> <span data-ttu-id="07f11-130">这在 E2E 测试中通常是不可能的。</span><span class="sxs-lookup"><span data-stu-id="07f11-130">This normally isn't possible in E2E testing.</span></span>

<span data-ttu-id="07f11-131">关于组件的环境，E2E 测试必须确保在开始验证之前已达到预期的环境状态。</span><span class="sxs-lookup"><span data-stu-id="07f11-131">With regard to the component's environment, E2E tests must make sure that the expected environmental state has been reached before verification starts.</span></span> <span data-ttu-id="07f11-132">否则，结果不可预知。</span><span class="sxs-lookup"><span data-stu-id="07f11-132">Otherwise, the result is unpredictable.</span></span> <span data-ttu-id="07f11-133">在单元测试中，CUT 的呈现和测试的生命周期更加集成，从而提高了测试的稳定性。</span><span class="sxs-lookup"><span data-stu-id="07f11-133">In unit testing, the rendering of the CUT and the life cycle of the test are more integrated, which improves test stability.</span></span>

<span data-ttu-id="07f11-134">E2E 测试涉及启动多个进程、网络和磁盘 I/O 以及其他子系统活动，这些活动通常会导致测试可靠性不佳。</span><span class="sxs-lookup"><span data-stu-id="07f11-134">E2E testing involves launching multiple processes, network and disk I/O, and other subsystem activity that often lead to poor test reliability.</span></span> <span data-ttu-id="07f11-135">单元测试通常不存在这类问题。</span><span class="sxs-lookup"><span data-stu-id="07f11-135">Unit tests are typically insulated from these sorts of issues.</span></span>

<span data-ttu-id="07f11-136">下表总结了这两种测试方法之间的差异。</span><span class="sxs-lookup"><span data-stu-id="07f11-136">The following table summarizes the difference between the two testing approaches.</span></span>

| <span data-ttu-id="07f11-137">功能</span><span class="sxs-lookup"><span data-stu-id="07f11-137">Capability</span></span>                       | <span data-ttu-id="07f11-138">单元测试</span><span class="sxs-lookup"><span data-stu-id="07f11-138">Unit testing</span></span>                     | <span data-ttu-id="07f11-139">E2E 测试</span><span class="sxs-lookup"><span data-stu-id="07f11-139">E2E testing</span></span>                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| <span data-ttu-id="07f11-140">测试范围</span><span class="sxs-lookup"><span data-stu-id="07f11-140">Test scope</span></span>                       | <span data-ttu-id="07f11-141">仅 Blazor 组件 (Razor/C#)</span><span class="sxs-lookup"><span data-stu-id="07f11-141">Blazor component (Razor/C#) only</span></span> | <span data-ttu-id="07f11-142">带 CSS/JS 的 Blazor 组件 (Razor/C#)</span><span class="sxs-lookup"><span data-stu-id="07f11-142">Blazor component (Razor/C#) with CSS/JS</span></span> |
| <span data-ttu-id="07f11-143">测试执行时间</span><span class="sxs-lookup"><span data-stu-id="07f11-143">Test execution time</span></span>              | <span data-ttu-id="07f11-144">毫秒</span><span class="sxs-lookup"><span data-stu-id="07f11-144">Milliseconds</span></span>                     | <span data-ttu-id="07f11-145">秒</span><span class="sxs-lookup"><span data-stu-id="07f11-145">Seconds</span></span>                                 |
| <span data-ttu-id="07f11-146">访问组件实例</span><span class="sxs-lookup"><span data-stu-id="07f11-146">Access to the component instance</span></span> | <span data-ttu-id="07f11-147">是</span><span class="sxs-lookup"><span data-stu-id="07f11-147">Yes</span></span>                              | <span data-ttu-id="07f11-148">否</span><span class="sxs-lookup"><span data-stu-id="07f11-148">No</span></span>                                      |
| <span data-ttu-id="07f11-149">对环境敏感</span><span class="sxs-lookup"><span data-stu-id="07f11-149">Sensitive to the environment</span></span>     | <span data-ttu-id="07f11-150">否</span><span class="sxs-lookup"><span data-stu-id="07f11-150">No</span></span>                               | <span data-ttu-id="07f11-151">是</span><span class="sxs-lookup"><span data-stu-id="07f11-151">Yes</span></span>                                     |
| <span data-ttu-id="07f11-152">可靠性</span><span class="sxs-lookup"><span data-stu-id="07f11-152">Reliability</span></span>                      | <span data-ttu-id="07f11-153">可靠性更高</span><span class="sxs-lookup"><span data-stu-id="07f11-153">More reliable</span></span>                    | <span data-ttu-id="07f11-154">可靠性更低</span><span class="sxs-lookup"><span data-stu-id="07f11-154">Less reliable</span></span>                           |

## <a name="choose-the-most-appropriate-test-approach"></a><span data-ttu-id="07f11-155">选择最合适的测试方法</span><span class="sxs-lookup"><span data-stu-id="07f11-155">Choose the most appropriate test approach</span></span>

<span data-ttu-id="07f11-156">在选择要执行的测试类型时，请考虑方案。</span><span class="sxs-lookup"><span data-stu-id="07f11-156">Consider the scenario when choosing the type of testing to perform.</span></span> <span data-ttu-id="07f11-157">下表描述了一些注意事项。</span><span class="sxs-lookup"><span data-stu-id="07f11-157">Some considerations are described in the following table.</span></span>

| <span data-ttu-id="07f11-158">方案</span><span class="sxs-lookup"><span data-stu-id="07f11-158">Scenario</span></span> | <span data-ttu-id="07f11-159">推荐的方法</span><span class="sxs-lookup"><span data-stu-id="07f11-159">Suggested approach</span></span> | <span data-ttu-id="07f11-160">注解</span><span class="sxs-lookup"><span data-stu-id="07f11-160">Remarks</span></span> |
| -------- | ------------------ | ------- |
| <span data-ttu-id="07f11-161">没有 JS 互操作逻辑的组件</span><span class="sxs-lookup"><span data-stu-id="07f11-161">Component without JS interop logic</span></span> | <span data-ttu-id="07f11-162">单元测试</span><span class="sxs-lookup"><span data-stu-id="07f11-162">Unit testing</span></span> | <span data-ttu-id="07f11-163">当 Blazor 组件中对 JS 互操作没有依赖项时，无需访问 JS 或 DOM API 即可对组件进行测试。</span><span class="sxs-lookup"><span data-stu-id="07f11-163">When there's no dependency on JS interop in a Blazor component, the component can be tested without access to JS or the DOM API.</span></span> <span data-ttu-id="07f11-164">在这种情况下，选择单元测试没有缺点。</span><span class="sxs-lookup"><span data-stu-id="07f11-164">In this scenario, there are no disadvantages to choosing unit testing.</span></span> |
| <span data-ttu-id="07f11-165">具有简单 JS 互操作逻辑的组件</span><span class="sxs-lookup"><span data-stu-id="07f11-165">Component with simple JS interop logic</span></span> | <span data-ttu-id="07f11-166">单元测试</span><span class="sxs-lookup"><span data-stu-id="07f11-166">Unit testing</span></span> | <span data-ttu-id="07f11-167">组件通常通过 JS 互操作查询 DOM 或触发动画。</span><span class="sxs-lookup"><span data-stu-id="07f11-167">It's common for components to query the DOM or trigger animations through JS interop.</span></span> <span data-ttu-id="07f11-168">在这种情况下，单元测试通常是首选，因为通过 <xref:Microsoft.JSInterop.IJSRuntime> 接口模拟 JS 交互非常简单。</span><span class="sxs-lookup"><span data-stu-id="07f11-168">Unit testing is usually preferred in this scenario, since it's straightforward to mock the JS interaction through the <xref:Microsoft.JSInterop.IJSRuntime> interface.</span></span> |
| <span data-ttu-id="07f11-169">依赖于复杂 JS 代码的组件</span><span class="sxs-lookup"><span data-stu-id="07f11-169">Component that depends on complex JS code</span></span> | <span data-ttu-id="07f11-170">单元测试和单独的 JS 测试</span><span class="sxs-lookup"><span data-stu-id="07f11-170">Unit testing and separate JS testing</span></span> | <span data-ttu-id="07f11-171">如果组件使用 JS 互操作调用大型或复杂的 JS 库，但 Blazor 组件和 JS 库之间的交互很简单，那么最好的方法可能是将组件和 JS 库（或代码）视为两个单独的部分，分别进行测试。</span><span class="sxs-lookup"><span data-stu-id="07f11-171">If a component uses JS interop to call a large or complex JS library but the interaction between the Blazor component and JS library is simple, then the best approach is likely to treat the component and JS library or code as two separate parts and test each individually.</span></span> <span data-ttu-id="07f11-172">使用单元测试库测试 Blazor 组件，使用 JS 测试库测试 JS。</span><span class="sxs-lookup"><span data-stu-id="07f11-172">Test the Blazor component with a unit testing library, and test the JS with a JS testing library.</span></span> |
| <span data-ttu-id="07f11-173">具有依赖于浏览器 DOM 的 JS 操作的逻辑的组件</span><span class="sxs-lookup"><span data-stu-id="07f11-173">Component with logic that depends on JS manipulation of the browser DOM</span></span> | <span data-ttu-id="07f11-174">E2E 测试</span><span class="sxs-lookup"><span data-stu-id="07f11-174">E2E testing</span></span> | <span data-ttu-id="07f11-175">当组件的功能依赖于 JS 及其对 DOM 的操作时，在 E2E 测试中同时验证 JS 和 Blazor 代码。</span><span class="sxs-lookup"><span data-stu-id="07f11-175">When a component's functionality is dependent on JS and its manipulation of the DOM, verify both the JS and Blazor code together in an E2E test.</span></span> <span data-ttu-id="07f11-176">这是 Blazor 框架开发人员使用 Blazor 的浏览器呈现逻辑采用的方法，该逻辑具有紧密耦合的 C# 和 JS 代码。</span><span class="sxs-lookup"><span data-stu-id="07f11-176">This is the approach that the Blazor framework developers have taken with Blazor's browser rendering logic, which has tightly-coupled C# and JS code.</span></span> <span data-ttu-id="07f11-177">C# 和 JS 代码必须协同工作才能在浏览器中正确呈现 Blazor 组件。</span><span class="sxs-lookup"><span data-stu-id="07f11-177">The C# and JS code must work together to correctly render Blazor components in a browser.</span></span>
| <span data-ttu-id="07f11-178">依赖于具有难以模拟的依赖项的第三方组件库的组件</span><span class="sxs-lookup"><span data-stu-id="07f11-178">Component that depends on 3rd party component library with hard-to-mock dependencies</span></span> | <span data-ttu-id="07f11-179">E2E 测试</span><span class="sxs-lookup"><span data-stu-id="07f11-179">E2E testing</span></span> | <span data-ttu-id="07f11-180">当组件的功能依赖于具有难以模拟的依赖项的第三方组件库（如 JS 互操作）时，可能只能使用 E2E 测试来测试组件。</span><span class="sxs-lookup"><span data-stu-id="07f11-180">When a component's functionality is dependent on a 3rd party component library that has hard-to-mock dependencies, such as JS interop, E2E testing might be the only option to test the component.</span></span> |

## <a name="test-components-with-bunit"></a><span data-ttu-id="07f11-181">使用 bUnit 测试组件</span><span class="sxs-lookup"><span data-stu-id="07f11-181">Test components with bUnit</span></span>

<span data-ttu-id="07f11-182">没有面向 Blazor 的官方 Microsoft 测试框架，但社区驱动的项目 [bUnit](https://github.com/egil/bUnit) 提供一种方便的方法来对 Blazor 组件进行单元测试。</span><span class="sxs-lookup"><span data-stu-id="07f11-182">There's no official Microsoft testing framework for Blazor, but the community-driven project [bUnit](https://github.com/egil/bUnit) provides a convenient way to unit test Blazor components.</span></span>

> [!NOTE]
> <span data-ttu-id="07f11-183">bUnit 是第三方测试库，不受 Microsoft 支持或维护。</span><span class="sxs-lookup"><span data-stu-id="07f11-183">bUnit is a third-party testing library and isn't supported or maintained by Microsoft.</span></span>

<span data-ttu-id="07f11-184">bUnit 适用于常规用途的测试框架，例如 [MSTest](/dotnet/core/testing/unit-testing-with-mstest)、[NUnit](https://nunit.org/) 和 [xUnit](https://xunit.github.io/)。</span><span class="sxs-lookup"><span data-stu-id="07f11-184">bUnit works with general-purpose testing frameworks, such as [MSTest](/dotnet/core/testing/unit-testing-with-mstest), [NUnit](https://nunit.org/), and [xUnit](https://xunit.github.io/).</span></span> <span data-ttu-id="07f11-185">这些测试框架使 bUnit 测试的外观和感觉像常规单元测试。</span><span class="sxs-lookup"><span data-stu-id="07f11-185">These testing frameworks make bUnit tests look and feel like regular unit tests.</span></span> <span data-ttu-id="07f11-186">与常规用途的测试框架集成的 bUnit 测试通常通过下列内容执行：</span><span class="sxs-lookup"><span data-stu-id="07f11-186">bUnit tests integrated with a general-purpose testing framework are ordinarily executed with:</span></span>

* <span data-ttu-id="07f11-187">[Visual Studio 测试资源管理器](/visualstudio/test/run-unit-tests-with-test-explorer)。</span><span class="sxs-lookup"><span data-stu-id="07f11-187">[Visual Studio's Test Explorer](/visualstudio/test/run-unit-tests-with-test-explorer).</span></span>
* <span data-ttu-id="07f11-188">命令行界面中的 [`dotnet test`](/dotnet/core/tools/dotnet-test) CLI 命令。</span><span class="sxs-lookup"><span data-stu-id="07f11-188">[`dotnet test`](/dotnet/core/tools/dotnet-test) CLI command in a command shell.</span></span>
* <span data-ttu-id="07f11-189">自动化的 DevOps 测试管道。</span><span class="sxs-lookup"><span data-stu-id="07f11-189">An automated DevOps testing pipeline.</span></span>

> [!NOTE]
> <span data-ttu-id="07f11-190">不同测试框架中的测试概念和测试实现相似，但不完全相同。</span><span class="sxs-lookup"><span data-stu-id="07f11-190">Test concepts and test implementations across different test frameworks are similar but not identical.</span></span> <span data-ttu-id="07f11-191">有关指导，请参阅测试框架的文档。</span><span class="sxs-lookup"><span data-stu-id="07f11-191">Refer to the test framework's documentation for guidance.</span></span>

<span data-ttu-id="07f11-192">下面演示了基于 `Counter` 项目模板的应用中 Blazor 组件上的 bUnit 测试的结构。</span><span class="sxs-lookup"><span data-stu-id="07f11-192">The following demonstrates the structure of a bUnit test on the `Counter` component in an app based on a Blazor project template.</span></span> <span data-ttu-id="07f11-193">`Counter` 组件根据用户在页面中选择按钮显示并递增计数器：</span><span class="sxs-lookup"><span data-stu-id="07f11-193">The `Counter` component displays and increments a counter based on the user selecting a button in the page:</span></span>

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

<span data-ttu-id="07f11-194">以下 bUnit 测试会验证在选中按钮时 CUT 计数器是否正确递增：</span><span class="sxs-lookup"><span data-stu-id="07f11-194">The following bUnit test verifies that the CUT's counter is incremented correctly when the button is selected:</span></span>

```csharp
[Fact]
public void CounterShouldIncrementWhenSelected()
{
    // Arrange
    using var ctx = new TestContext();
    var cut = ctx.RenderComponent<Counter>();
    var paraElm = cut.Find("p");

    // Act
    cut.Find("button").Click();
    var paraElmText = paraElm.TextContent;

    // Assert
    paraElmText.MarkupMatches("Current count: 1");
}
```

<span data-ttu-id="07f11-195">测试的每个步骤都执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="07f11-195">The following actions take place at each step of the test:</span></span>

* <span data-ttu-id="07f11-196">*安排*：使用 bUnit 的 `TestContext` 呈现 `Counter` 组件。</span><span class="sxs-lookup"><span data-stu-id="07f11-196">*Arrange*: The `Counter` component is rendered using bUnit's `TestContext`.</span></span> <span data-ttu-id="07f11-197">找到 CUT 的段落元素 (`<p>`)，并将其分配给 `paraElm`。</span><span class="sxs-lookup"><span data-stu-id="07f11-197">The CUT's paragraph element (`<p>`) is found and assigned to `paraElm`.</span></span>

* <span data-ttu-id="07f11-198">*执行*：找到按钮的元素 (`<button>`)，然后通过调用 `Click` 选择它，该调用应增加计数器并更新段落标记 (`<p>`) 的内容。</span><span class="sxs-lookup"><span data-stu-id="07f11-198">*Act*: The button's element (`<button>`) is located and then selected by calling `Click`, which should increment the counter and update the content of the paragraph tag (`<p>`).</span></span> <span data-ttu-id="07f11-199">段落元素文本内容通过调用 `TextContent` 获得。</span><span class="sxs-lookup"><span data-stu-id="07f11-199">The paragraph element text content is obtained by calling `TextContent`.</span></span>

* <span data-ttu-id="07f11-200">*断言*：在文本内容上调用 `MarkupMatches`，以验证它是否与预期字符串 `Current count: 1` 匹配。</span><span class="sxs-lookup"><span data-stu-id="07f11-200">*Assert*: `MarkupMatches` is called on the text content to verify that it matches the expected string, which is `Current count: 1`.</span></span>

> [!NOTE]
> <span data-ttu-id="07f11-201">`MarkupMatches` 断言方法不同于常规字符串比较断言（例如 `Assert.Equal("Current count: 1", paraElmText);`），`MarkupMatches` 执行输入和预期的 HTML 标记的语义比较。</span><span class="sxs-lookup"><span data-stu-id="07f11-201">The `MarkupMatches` assert method differs from a regular string comparison assertion (for example, `Assert.Equal("Current count: 1", paraElmText);`) `MarkupMatches` performs a semantic comparison of the input and expected HTML markup.</span></span> <span data-ttu-id="07f11-202">语义比较会识别 HTML 语义，这意味着忽略无关紧要的空格。</span><span class="sxs-lookup"><span data-stu-id="07f11-202">A semantic comparison is aware of HTML semantics, meaning things like insignificant whitespace is ignored.</span></span> <span data-ttu-id="07f11-203">这会生成更稳定的测试。</span><span class="sxs-lookup"><span data-stu-id="07f11-203">This results in more stable tests.</span></span> <span data-ttu-id="07f11-204">有关详细信息，请参阅[自定义语义 HTML 比较](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison)。</span><span class="sxs-lookup"><span data-stu-id="07f11-204">For more information, see [Customizing the Semantic HTML Comparison](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="07f11-205">其他资源</span><span class="sxs-lookup"><span data-stu-id="07f11-205">Additional resources</span></span>

* <span data-ttu-id="07f11-206">[bUnit 入门](https://bunit.egilhansen.com/docs/getting-started/)：bUnit 说明中有指南来介绍如何创建测试项目、引用测试框架包以及构建和运行测试。</span><span class="sxs-lookup"><span data-stu-id="07f11-206">[Getting Started with bUnit](https://bunit.egilhansen.com/docs/getting-started/): bUnit instructions include guidance on creating a test project, referencing testing framework packages, and building and running tests.</span></span>
