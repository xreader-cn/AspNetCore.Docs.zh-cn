---
title: 处理 ASP.NET Core Blazor 应用中的错误
author: guardrex
description: 了解 ASP.NET Core Blazor 如何管理未经处理的异常以及如何开发用于检测和处理错误的应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/23/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/fundamentals/handle-errors
ms.openlocfilehash: 5f7112d9a072f28d387e07bdf69ec0b7595ff6b4
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88014433"
---
# <a name="handle-errors-in-aspnet-core-no-locblazor-apps"></a>处理 ASP.NET Core Blazor 应用中的错误

作者：[Steve Sanderson](https://github.com/SteveSandersonMS)

本文介绍 Blazor 如何管理未经处理的异常以及如何开发用于检测和处理错误的应用。

## <a name="detailed-errors-during-development"></a>开发过程中的错误详细信息

当 Blazor 应用在开发过程中运行不正常时，从该应用接收详细的错误信息有助于故障排除和修复问题。 出现错误时，Blazor 应用会在屏幕底部显示一个黄色条框：

* 在开发过程中，黄色条框会将你定向到浏览器控制台，你可在其中查看异常。
* 在生产过程中，黄色条框会通知用户发生了错误，并建议刷新浏览器。

此错误处理体验的 UI 属于 Blazor 项目模板。

在 Blazor WebAssembly 应用的 `wwwroot/index.html` 文件中自定义体验：

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

在 Blazor Server 应用的 `Pages/_Host.cshtml` 文件中自定义体验：

```cshtml
<div id="blazor-error-ui">
    <environment include="Staging,Production">
        An error has occurred. This application may no longer respond until reloaded.
    </environment>
    <environment include="Development">
        An unhandled exception has occurred. See browser dev tools for details.
    </environment>
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

`blazor-error-ui` 元素被 Blazor 模板（`wwwroot/css/app.css` 或 `wwwroot/css/site.css`）附带的样式隐藏，并会在发生错误时显示：

```css
#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

## <a name="how-a-no-locblazor-server-app-reacts-to-unhandled-exceptions"></a>Blazor Server 应用如何应对未经处理的异常

Blazor Server 是一种有状态框架。 用户与应用进行交互时，会与服务器保持名为“线路”的连接。 线路包含活动组件实例，以及状态的许多其他方面，例如：

* 最新呈现的组件输出。
* 可由客户端事件触发的事件处理委托的当前集合。

如果用户在多个浏览器标签页中打开应用，则具有多条独立线路。

Blazor 将大部分未经处理的异常视为发生该异常的线路的严重异常。 如果线路由于未经处理的异常而终止，则用户只能重新加载页面来创建新线路，从而继续与应用进行交互。 终止的线路以外的其他线路（即其他用户或其他浏览器标签页的线路）不会受到影响。 此场景类似于出现故障的桌面应用。 出现故障的应用必须重新启动，但其他应用不受影响。

当发生未经处理的异常时，线路会终止，原因如下：

* 未经处理的异常通常会将线路置于未定义状态。
* 发生未经处理的异常后，应用可能无法正常运行。
* 如果不终止线路，则可能导致应用中出现安全漏洞。

## <a name="manage-unhandled-exceptions-in-developer-code"></a>在开发人员代码中管理未经处理的异常

若要在出现错误后继续运行应用，该应用必须具备错误处理逻辑。 本文后面的部分将介绍未经处理的异常出现的潜在原因。

在生产环境中，不要在 UI 中呈现框架异常消息或堆栈跟踪信息。 呈现异常消息或堆栈跟踪信息可能导致：

* 向最终用户公开敏感信息。
* 帮助恶意用户发现应用中可能会危及应用、服务器或网络安全的弱点。

## <a name="log-errors-with-a-persistent-provider"></a>使用永久性提供程序记录错误信息

在发生未经处理的异常时，将异常记录到在服务容器中配置的 <xref:Microsoft.Extensions.Logging.ILogger> 实例。 默认情况下，Blazor 应用使用控制台日志记录提供程序记录到控制台输出中。 请考虑使用管理日志大小和日志轮换的提供程序将日志记录到更持久的位置。 有关详细信息，请参阅 <xref:fundamentals/logging/index>。

在开发过程中，Blazor 通常会将异常的完整详细信息发送到浏览器的控制台，以帮助进行调试。 在生产环境中，浏览器控制台中的错误详细信息默认禁用，也就是说错误信息不会发送到客户端，但异常的完整详细信息仍记录在服务器端。 有关详细信息，请参阅 <xref:fundamentals/error-handling>。

必须确定要记录的事件以及已记录的事件的严重性级别。 恶意用户也许能刻意触发错误。 例如，若显示产品详细信息的组件的 URL 中提供了未知的 `ProductId`，则请勿记录错误中的事件。 不是所有的错误都应被视为高严重性事件进行记录。

有关详细信息，请参阅 <xref:blazor/fundamentals/logging>。

## <a name="places-where-errors-may-occur"></a>可能发生错误的位置

框架和应用代码可能会在以下任一位置触发未经处理的异常：

* [组件实例化](#component-instantiation)
* [生命周期方法](#lifecycle-methods)
* [呈现逻辑](#rendering-logic)
* [事件处理程序](#event-handlers)
* [组件处置](#component-disposal)
* [JavaScript 互操作](#javascript-interop)
* [Blazor Server 重新呈现](#blazor-server-prerendering)

本文的以下部分介绍了上述未经处理的异常。

### <a name="component-instantiation"></a>组件实例化

当 Blazor 创建某组件的实例时：

* 会调用该组件的构造函数。
* 会调用通过 [`@inject`](xref:mvc/views/razor#inject) 指令或 [`[Inject]`](xref:blazor/fundamentals/dependency-injection#request-a-service-in-a-component) 特性提供给组件构造函数的非单一 DI 设备的构造函数。

如果任何已执行的构造函数或任何 `[Inject]` 属性的资源库引发了未经处理的异常，则 Blazor Server 线路会失败。 这是严重异常，因为框架无法实例化组件。 如果构造函数逻辑可能引发异常，应用应使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。

### <a name="lifecycle-methods"></a>生命周期方法

在组件的生命周期内，Blazor 会调用以下[生命周期方法](xref:blazor/components/lifecycle)：

* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitialized%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSet%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.ShouldRender%2A>
* <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRender%2A> / <xref:Microsoft.AspNetCore.Components.ComponentBase.OnAfterRenderAsync%2A>

如果任何生命周期方法以同步或异步方式引发异常，则该异常对于 Blazor Server 线路而言是严重异常。 若要使组件处理生命周期方法中的错误，请添加错误处理逻辑。

在下面的示例中，<xref:Microsoft.AspNetCore.Components.ComponentBase.OnParametersSetAsync%2A> 会调用方法来获取产品：

* `ProductRepository.GetProductByIdAsync` 方法中引发的异常由 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句处理。
* 在执行 `catch` 块时：
  * `loadFailed` 设置为 `true`，用于向用户显示一条错误消息。
  * 错误会被记录。

[!code-razor[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>呈现逻辑

`.razor` 组件文件中的声明性标记被编译到名为 <xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 的 C# 方法中。 当组件呈现时，<xref:Microsoft.AspNetCore.Components.ComponentBase.BuildRenderTree%2A> 会执行并构建一个数据结构，该结构描述所呈现组件的元素、文本和子组件。

呈现逻辑可能会引发异常。 例如评估了 `@someObject.PropertyName`，但 `@someObject` 为 `null` 时，就会发生这种情况。 呈现逻辑引发的未经处理的异常对于 Blazor Server 线路来说是严重异常。

为防止呈现逻辑中出现空引用异常，请在访问其成员之前检查 `null` 对象。 在以下示例中，如果 `person.Address` 为 `null`，则不访问 `person.Address` 属性：

[!code-razor[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

上述代码假定 `person` 不是 `null`。 通常，代码的结构保证了呈现组件时存在对象。 在这些情况下，不需要检查呈现逻辑中是否存在 `null`。 在前面的示例中，由于在实例化组件时创建了 `person`，因此可保证存在 `person`。

### <a name="event-handlers"></a>事件处理程序

使用以下内容创建事件处理程序时，客户端代码将触发 C# 代码调用：

* `@onclick`
* `@onchange`
* 其他 `@on...` 特性
* `@bind`

在这些情况下，事件处理程序代码可能会引发未经处理的异常。

如果事件处理程序引发未经处理的异常（例如数据库查询失败），则该异常对于 Blazor Server 线路来说是严重异常。 如果应用调用可能因外部原因而失败的代码，请使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。

如果用户代码不会捕获和处理异常，则框架将记录异常并终止线路。

### <a name="component-disposal"></a>组件处置

例如，可从 UI 中删除组件，因为用户已导航到其他页面。 当从 UI 中删除实现 <xref:System.IDisposable?displayProperty=fullName> 的组件时，框架将调用该组件的 <xref:System.IDisposable.Dispose%2A> 方法。

如果组件的 `Dispose` 方法引发未经处理的异常，则该异常对于 Blazor Server 线路来说是严重异常。 如果处置逻辑可能引发异常，应用应使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句捕获异常，并进行错误处理和日志记录。

要详细了解组件处置，请参阅 <xref:blazor/components/lifecycle#component-disposal-with-idisposable>。

### <a name="javascript-interop"></a>JavaScript 互操作

<xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A?displayProperty=nameWithType> 允许 .NET 代码在用户浏览器中对 JavaScript 运行时进行异步调用。

以下条件适用于带有 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的错误处理：

* 如果无法对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 进行同步调用，则会发生 .NET 异常。 例如，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用可能会失败，因为不能序列化提供的自变量。 开发人员代码必须捕获异常。 如果事件处理程序或组件生命周期方法中的应用代码未处理异常，则该异常对于 Blazor Server 线路来说是严重异常。
* 如果无法对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 进行异步调用，则 .NET <xref:System.Threading.Tasks.Task> 会失败。 例如，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用可能会失败，这是因为 JavaScript 端代码会引发异常或返回完成状态为 `rejected` 的 `Promise`。 开发人员代码必须捕获异常。 如果使用 [`await`](/dotnet/csharp/language-reference/keywords/await) 运算符，请考虑使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句包装方法调用，并进行错误处理和日志记录。 否则，失败的代码会导致未经处理的异常，这对于 Blazor Server 线路来说是严重异常。
* 默认情况下，对 <xref:Microsoft.JSInterop.IJSRuntime.InvokeAsync%2A> 的调用必须在特定时间段内完成，否则调用会超时。默认超时期限为一分钟。 超时会保护代码免受网络连接丢失的影响，或者保护永远不会发回完成消息的 JavaScript 代码。 如果调用超时，则生成的 <xref:System.Threading.Tasks> 将失败，并出现 <xref:System.OperationCanceledException>。 捕获异常，并进行异常处理和日志记录。

同样，JavaScript 代码可以对 [`[JSInvokable]`](xref:blazor/call-dotnet-from-javascript) 特性指示的 .NET 方法发起调用。 如果这些 .NET 方法引发未经处理的异常：

* 此异常不会被视为 Blazor Server 线路的严重异常。
* JavaScript 端 `Promise` 会被拒绝。

可选择在方法调用的 .NET 端或 JavaScript 端使用错误处理代码。

有关详细信息，请参阅以下文章：

* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>

### <a name="no-locblazor-server-prerendering"></a>Blazor Server 预呈现

Blazor 组件可使用[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)进行预呈现，以便在用户的初始 HTTP 请求过程中返回其呈现的 HTML 标记。 实现方式如下：

* 为属于同一页面的所有预呈现组件创建新的线路。
* 生成初始 HTML。
* 将线路视为 `disconnected`，直到用户浏览器与同一服务器重新建立起 SignalR 连接。 建立该连接后，将恢复线路的交互性，并更新组件的 HTML 标记。

如果任何组件在预呈现期间引发未经处理的异常，例如在生命周期方法或呈现逻辑中：

* 则该异常对线路是严重的。
* 此异常将从 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 标记帮助程序中的调用堆栈引发。 这将导致整个 HTTP 请求失败，除非开发人员代码显式捕获该异常。

在正常情况下，如果预呈现失败，则继续生成和呈现组件都将没有作用，因为无法呈现工作组件。

若要容许在预呈现期间可能发生的错误，必须将错误处理逻辑置于可能引发异常的组件中。 请使用 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句，并进行错误处理和日志记录。 请勿将 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 标记帮助程序包装在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句中，而是将错误处理逻辑放在由 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.ComponentTagHelper> 标记帮助程序呈现的组件中。

## <a name="advanced-scenarios"></a>高级方案

### <a name="recursive-rendering"></a>递归呈现

组件能以递归方式嵌套。 这适用于表示递归数据结构。 例如，`TreeNode` 组件可以为节点的每个子级呈现更多 `TreeNode` 组件。

以递归方式呈现时，请避免采用会导致无限递归的编码模式：

* 请勿以递归方式呈现包含循环的数据结构。 例如，请勿呈现其子级包含其自身的树节点。
* 请勿创建包含循环的布局链。 例如，请勿创建布局为其本身的布局。
* 请勿允许最终用户通过恶意数据输入或 JavaScript 互操作调用违反递归固定协定（规则）。

呈现过程中的无限循环：

* 会导致呈现过程永久地继续下去。
* 相当于创建不终止的循环。

在这些情况下，受影响的 Blazor Server 线路会失败，并且该线程通常会尝试执行以下操作：

* 在操作系统允许范围内无限期地消耗 CPU 时间。
* 消耗不限量的服务器内存。 消耗不限量的内存相当于不终止的循环在每次迭代时向集合添加条目的情况。

若要避免无限递归模式，请确保递归呈现代码包含合适的停止条件。

### <a name="custom-render-tree-logic"></a>自定义呈现器树逻辑

大多数 Blazor 组件都实现为 `.razor` 文件，并经过编译以生成在 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 上运行的逻辑，目的是呈现其输出。 开发人员可使用程序 C# 代码手动实现 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 逻辑。 有关详细信息，请参阅 <xref:blazor/advanced-scenarios#manual-rendertreebuilder-logic>。

> [!WARNING]
> 手动呈现树生成器逻辑被视为一种高级且不安全的方案，不建议开发人员在常规组件开发工作中采用。

如果编写 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder> 代码，开发人员必须保证代码的正确性。 例如，开发人员必须确保：

* 对 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.OpenElement%2A> 和 <xref:Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder.CloseElement%2A> 的调用已正确均衡。
* 仅将特性添加到正确的位置。

若手动呈现树生成器逻辑不正确，则可能出现任意未定义的行为（包括崩溃和服务器挂起）以及安全漏洞。

请知悉：手动呈现树生成器逻辑的复杂程度和危险程度与手动编写程序集代码或 MSIL 指令是一样的。
