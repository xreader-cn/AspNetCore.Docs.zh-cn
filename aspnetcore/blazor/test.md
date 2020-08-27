---
title: 在 ASP.NET Core Blazor 中测试组件
author: guardrex
description: 了解如何在 Blazor 应用中测试组件。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/10/2020
no-loc:
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
ms.openlocfilehash: 572b9a293e2fd6f51431cd1de6ada737addf5efa
ms.sourcegitcommit: dd0e87abf2bb50ee992d9185bb256ed79d48f545
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2020
ms.locfileid: "88746528"
---
# <a name="test-components-in-aspnet-core-no-locblazor"></a>在 ASP.NET Core Blazor 中测试组件

[Egil Hansen](https://egilhansen.com/)

测试是构建稳定且可维护的软件的一个重要方面。

若要测试 Blazor 组件，则测试中的组件 (CUT)：

* 使用测试的相关输入进行呈现。
* 根据执行的测试类型，可能会进行交互或修改。 例如，事件处理程序可能会被触发，如按钮的 `onclick` 事件。
* 已检查预期的值。

## <a name="test-approaches"></a>测试方法

测试 Blazor 组件的两种常见方法是端到端 (E2E) 测试和单元测试：

* **单元测试**：[单元测试](/dotnet/core/testing/)使用单元测试库编写，该库提供：
  * 组件呈现。
  * 组件输出和状态的检查。
  * 事件处理程序和生命周期方法的触发。
  * 关于组件行为正确的断言。

  [bUnit](https://github.com/egil/bUnit) 就是启用 Razor 组件单元测试的一个库。

* **E2E 测试**：测试运行程序会运行包含 CUT 的 Blazor 应用，并自动执行浏览器实例。 测试工具通过浏览器进行检查并与 CUT 交互。 [Selenium](https://github.com/SeleniumHQ/selenium) 就是一个可用于 Blazor 应用的 E2E 测试框架。

在单元测试中，仅涉及到 Blazor 组件 (Razor/C#)。 必须模拟外部依赖项（如服务和 JS 互操作）。 在 E2E 测试中，Blazor 组件及其所有辅助基础结构是测试的一部分，包括 CSS、JS 以及 DOM 和浏览器 API。

“测试范围”描述了测试的范围。 测试范围通常会影响测试的速度。 单元测试在应用的部分子系统上运行，通常以毫秒为单位执行。 E2E 测试会测试应用的大量子系统，可能需要几秒钟才能完成。 

单元测试还提供对 CUT 实例的访问，从而允许组件内部状态的检查和验证。 这在 E2E 测试中通常是不可能的。

关于组件的环境，E2E 测试必须确保在开始验证之前已达到预期的环境状态。 否则，结果不可预知。 在单元测试中，CUT 的呈现和测试的生命周期更加集成，从而提高了测试的稳定性。

E2E 测试涉及启动多个进程、网络和磁盘 I/O 以及其他子系统活动，这些活动通常会导致测试可靠性不佳。 单元测试通常不存在这类问题。

下表总结了这两种测试方法之间的差异。

| 功能                       | 单元测试                     | E2E 测试                             |
| -------------------------------- | -------------------------------- | --------------------------------------- |
| 测试范围                       | 仅 Blazor 组件 (Razor/C#) | 带 CSS/JS 的 Blazor 组件 (Razor/C#) |
| 测试执行时间              | 毫秒                     | 秒                                 |
| 访问组件实例 | 是                              | 否                                      |
| 对环境敏感     | 否                               | 是                                     |
| 可靠性                      | 可靠性更高                    | 可靠性更低                           |

## <a name="choose-the-most-appropriate-test-approach"></a>选择最合适的测试方法

在选择要执行的测试类型时，请考虑方案。 下表描述了一些注意事项。

| 方案 | 推荐的方法 | 注解 |
| -------- | ------------------ | ------- |
| 没有 JS 互操作逻辑的组件 | 单元测试 | 当 Blazor 组件中对 JS 互操作没有依赖项时，无需访问 JS 或 DOM API 即可对组件进行测试。 在这种情况下，选择单元测试没有缺点。 |
| 具有简单 JS 互操作逻辑的组件 | 单元测试 | 组件通常通过 JS 互操作查询 DOM 或触发动画。 在这种情况下，单元测试通常是首选，因为通过 <xref:Microsoft.JSInterop.IJSRuntime> 接口模拟 JS 交互非常简单。 |
| 依赖于复杂 JS 代码的组件 | 单元测试和单独的 JS 测试 | 如果组件使用 JS 互操作调用大型或复杂的 JS 库，但 Blazor 组件和 JS 库之间的交互很简单，那么最好的方法可能是将组件和 JS 库（或代码）视为两个单独的部分，分别进行测试。 使用单元测试库测试 Blazor 组件，使用 JS 测试库测试 JS。 |
| 具有依赖于浏览器 DOM 的 JS 操作的逻辑的组件 | E2E 测试 | 当组件的功能依赖于 JS 及其对 DOM 的操作时，在 E2E 测试中同时验证 JS 和 Blazor 代码。 这是 Blazor 框架开发人员使用 Blazor 的浏览器呈现逻辑采用的方法，该逻辑具有紧密耦合的 C# 和 JS 代码。 C# 和 JS 代码必须协同工作才能在浏览器中正确呈现 Blazor 组件。
| 依赖于具有难以模拟的依赖项的第三方组件库的组件 | E2E 测试 | 当组件的功能依赖于具有难以模拟的依赖项的第三方组件库（如 JS 互操作）时，可能只能使用 E2E 测试来测试组件。 |

## <a name="test-components-with-bunit"></a>使用 bUnit 测试组件

没有面向 Blazor 的官方 Microsoft 测试框架，但社区驱动的项目 [bUnit](https://github.com/egil/bUnit) 提供一种方便的方法来对 Blazor 组件进行单元测试。

> [!NOTE]
> bUnit 是第三方测试库，不受 Microsoft 支持或维护。

bUnit 适用于常规用途的测试框架，例如 [MSTest](/dotnet/core/testing/unit-testing-with-mstest)、[NUnit](https://nunit.org/) 和 [xUnit](https://xunit.github.io/)。 这些测试框架使 bUnit 测试的外观和感觉像常规单元测试。 与常规用途的测试框架集成的 bUnit 测试通常通过下列内容执行：

* [Visual Studio 测试资源管理器](/visualstudio/test/run-unit-tests-with-test-explorer)。
* 命令行界面中的 [`dotnet test`](/dotnet/core/tools/dotnet-test) CLI 命令。
* 自动化的 DevOps 测试管道。

> [!NOTE]
> 不同测试框架中的测试概念和测试实现相似，但不完全相同。 有关指导，请参阅测试框架的文档。

下面演示了基于 `Counter` 项目模板的应用中 Blazor 组件上的 bUnit 测试的结构。 `Counter` 组件根据用户在页面中选择按钮显示并递增计数器：

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

以下 bUnit 测试会验证在选中按钮时 CUT 计数器是否正确递增：

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

测试的每个步骤都执行以下操作：

* *安排*：使用 bUnit 的 `TestContext` 呈现 `Counter` 组件。 找到 CUT 的段落元素 (`<p>`)，并将其分配给 `paraElm`。

* *执行*：找到按钮的元素 (`<button>`)，然后通过调用 `Click` 选择它，该调用应增加计数器并更新段落标记 (`<p>`) 的内容。 段落元素文本内容通过调用 `TextContent` 获得。

* *断言*：在文本内容上调用 `MarkupMatches`，以验证它是否与预期字符串 `Current count: 1` 匹配。

> [!NOTE]
> `MarkupMatches` 断言方法不同于常规字符串比较断言（例如 `Assert.Equal("Current count: 1", paraElmText);`），`MarkupMatches` 执行输入和预期的 HTML 标记的语义比较。 语义比较会识别 HTML 语义，这意味着忽略无关紧要的空格。 这会生成更稳定的测试。 有关详细信息，请参阅[自定义语义 HTML 比较](https://bunit.egilhansen.com/docs/verification/semantic-html-comparison)。

## <a name="additional-resources"></a>其他资源

* [bUnit 入门](https://bunit.egilhansen.com/docs/getting-started/)：bUnit 说明中有指南来介绍如何创建测试项目、引用测试框架包以及构建和运行测试。
