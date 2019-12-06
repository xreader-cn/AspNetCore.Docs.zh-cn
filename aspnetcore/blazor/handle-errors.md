---
title: 处理 ASP.NET Core Blazor 应用中的错误
author: guardrex
description: 了解 ASP.NET Core 如何 Blazor 如何 Blazor 管理未经处理的异常以及如何开发检测并处理错误的应用程序。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/01/2019
no-loc:
- Blazor
- SignalR
uid: blazor/handle-errors
ms.openlocfilehash: e737a8a85e7eb83d95618d71e85b0307c54b0766
ms.sourcegitcommit: c0b72b344dadea835b0e7943c52463f13ab98dd1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74879687"
---
# <a name="handle-errors-in-aspnet-core-opno-locblazor-apps"></a>处理 ASP.NET Core Blazor 应用中的错误

作者：[Steve Sanderson](https://github.com/SteveSandersonMS)

本文介绍 Blazor 如何管理未经处理的异常以及如何开发检测和处理错误的应用。

::: moniker range=">= aspnetcore-3.1"

## <a name="detailed-errors-during-development"></a>开发过程中的详细错误

如果在开发过程中 Blazor 应用程序不能正常工作，则从应用程序接收详细的错误信息可帮助进行故障排除并解决问题。 出现错误时，Blazor 应用程序会在屏幕底部显示一个金色栏：

* 在开发过程中，黄金栏会将您定向到浏览器控制台，您可以在其中查看异常。
* 在生产环境中，黄金栏通知用户发生了错误，并建议刷新浏览器。

此错误处理体验的 UI 是 Blazor 项目模板的一部分。 在 Blazor WebAssembly 应用程序中，自定义*wwwroot/index.html*文件中的体验：

```html
<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
```

在 Blazor Server 应用程序中，自定义*Pages/_Host*文件中的体验：

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

`blazor-error-ui` 元素被 Blazor 模板附带的样式隐藏，然后在发生错误时显示。

::: moniker-end

## <a name="how-the-opno-locblazor-framework-reacts-to-unhandled-exceptions"></a>Blazor 框架如何响应未经处理的异常

Blazor Server 是有状态框架。 当用户与应用交互时，它们会保持与服务器（称为*线路*）的连接。 线路包含活动组件实例，以及状态的许多其他方面，例如：

* 最新呈现的组件输出。
* 客户端事件可触发的事件处理委托的当前集合。

如果用户在多个浏览器选项卡中打开应用程序，则它们具有多个独立的线路。

Blazor 将最未经处理的异常视为致命的异常，并将其出现在线路上。 如果线路由于未经处理的异常而终止，则用户只可以通过重新加载页面来创建新线路，从而继续与应用进行交互。 已终止的线路（其他用户或其他浏览器选项卡的线路）不会受到影响。 这种情况类似于桌面应用程序崩溃&mdash;崩溃的应用程序必须重新启动，但其他应用不受影响。

当发生未处理的异常时，线路会终止，原因如下：

* 未经处理的异常通常使线路处于未定义状态。
* 无法在未处理的异常后确保应用的正常操作。
* 如果线路继续存在，则可能会在应用程序中出现安全漏洞。

## <a name="manage-unhandled-exceptions-in-developer-code"></a>在开发人员代码中管理未经处理的异常

若要使应用在出现错误后继续操作，应用必须具有错误处理逻辑。 本文后面的部分将介绍未经处理的异常的潜在来源。

在生产环境中，不要在 UI 中呈现框架异常消息或堆栈跟踪。 呈现异常消息或堆栈跟踪可以：

* 向最终用户公开敏感信息。
* 帮助恶意用户发现应用程序中可能会危及应用程序、服务器或网络安全的弱点。

## <a name="log-errors-with-a-persistent-provider"></a>使用永久性提供程序记录错误

如果发生未处理的异常，则会将异常记录到在服务容器中配置 <xref:Microsoft.Extensions.Logging.ILogger> 实例。 默认情况下，使用控制台日志记录提供程序 Blazor 应用日志输出到控制台输出。 请考虑使用管理日志大小和日志轮换的提供程序，将日志记录到更永久性的位置。 有关更多信息，请参见<xref:fundamentals/logging/index>。

在开发过程中，Blazor 通常会将异常的完整详细信息发送到浏览器的控制台，以帮助进行调试。 在生产环境中，默认情况下禁用浏览器控制台中的详细错误，这意味着不会将错误发送到客户端，但异常的完整详细信息仍记录在服务器端。 有关更多信息，请参见<xref:fundamentals/error-handling>。

您必须确定要记录的事件以及记录事件的严重性级别。 恶意用户可能会特意触发错误。 例如，请勿记录一个错误，其中显示产品详细信息的组件 URL 中提供了未知 `ProductId`。 不是所有的错误都应视为日志记录的高严重性事件。

## <a name="places-where-errors-may-occur"></a>可能发生错误的位置

框架和应用代码可能会在以下任何位置触发未经处理的异常：

* [组件实例化](#component-instantiation)
* [生命周期方法](#lifecycle-methods)
* [呈现逻辑](#rendering-logic)
* [事件处理程序](#event-handlers)
* [组件处置](#component-disposal)
* [JavaScript 互操作](#javascript-interop)
* [线路处理程序](#circuit-handlers)
* [线路处置](#circuit-disposal)
* [呈现](#prerendering)

本文的以下部分介绍了前面未处理的异常。

### <a name="component-instantiation"></a>组件实例化

当 Blazor 创建组件的实例时：

* 调用组件的构造函数。
* 将调用通过[`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component)指令或[`[Inject]`](xref:blazor/dependency-injection#request-a-service-in-a-component)属性提供给组件的构造函数的任何非单一服务器 DI 服务的构造函数。 

任何 `[Inject]` 属性的任何已执行构造函数或 setter 均引发未经处理的异常时，线路会失败。 异常是致命的，因为框架无法实例化组件。 如果构造函数逻辑可能引发异常，应用应使用带有错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句来捕获异常。

### <a name="lifecycle-methods"></a>生命周期方法

在组件的生存期内，Blazor 调用以下[生命周期方法](xref:blazor/lifecycle)：

* `OnInitialized` / `OnInitializedAsync`
* `OnParametersSet` / `OnParametersSetAsync`
* `ShouldRender` / `ShouldRenderAsync`
* `OnAfterRender` / `OnAfterRenderAsync`

如果任何生命周期方法以同步或异步方式引发异常，则该异常对于线路是致命的。 若要使组件处理生命周期方法中的错误，请添加错误处理逻辑。

在下面的示例中，`OnParametersSetAsync` 调用方法来获取产品：

* `ProductRepository.GetProductByIdAsync` 方法中引发的异常由 `try-catch` 语句处理。
* 执行 `catch` 块时：
  * `loadFailed` 设置为 `true`，用于向用户显示一条错误消息。
  * 记录错误。

[!code-cshtml[](handle-errors/samples_snapshot/3.x/product-details.razor?highlight=11,27-39)]

### <a name="rendering-logic"></a>呈现逻辑

`.razor` 组件文件中的声明性标记被编译到称为C# `BuildRenderTree`的方法中。 当组件呈现时，`BuildRenderTree` 执行并生成一个数据结构，该结构描述所呈现组件的元素、文本和子组件。

呈现逻辑可能引发异常。 在计算 `@someObject.PropertyName` 但 `@someObject` `null`时，会发生这种情况。 呈现逻辑引发的未经处理的异常对于线路是致命的。

若要防止呈现逻辑中出现空引用异常，请在访问其成员之前检查 `null` 对象。 在以下示例中，如果 `person.Address` `null`，则不会访问 `person.Address` 属性：

[!code-cshtml[](handle-errors/samples_snapshot/3.x/person-example.razor?highlight=1)]

前面的代码假定 `person` 不 `null`。 通常，代码的结构保证在呈现组件时存在对象。 在这些情况下，不需要检查呈现逻辑中的 `null`。 在前面的示例中，可以保证存在 `person` 因为在实例化组件时创建 `person`。

### <a name="event-handlers"></a>事件处理程序

使用以下代码创建事件处理程序C#时，客户端代码将触发代码调用：

* `@onclick`
* `@onchange`
* 其他 `@on...` 特性
* `@bind`

在这些情况下，事件处理程序代码可能会引发未处理的异常。

如果事件处理程序引发未经处理的异常（例如，数据库查询失败），则异常对于线路是致命的。 如果应用调用可能因外部原因而失败的代码，则使用带有错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句来捕获异常。

如果用户代码不会捕获并处理异常，则框架将记录异常并终止线路。

### <a name="component-disposal"></a>组件处置

例如，可以从 UI 中删除组件，因为用户已导航到另一个页面。 当从 UI 中删除实现 <xref:System.IDisposable?displayProperty=fullName> 的组件时，框架将调用该组件的 <xref:System.IDisposable.Dispose*> 方法。 

如果组件的 `Dispose` 方法引发未处理的异常，则该异常对于线路是致命的。 如果处理逻辑可能引发异常，应用应使用带有错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句来捕获异常。

有关组件处置的详细信息，请参阅 <xref:blazor/lifecycle#component-disposal-with-idisposable>。

### <a name="javascript-interop"></a>JavaScript 互操作

`IJSRuntime.InvokeAsync<T>` 允许 .NET 代码对用户浏览器中的 JavaScript 运行时进行异步调用。

以下条件适用于带有 `InvokeAsync<T>`的错误处理：

* 如果对 `InvokeAsync<T>` 的调用同步失败，则会发生 .NET 异常。 例如，对 `InvokeAsync<T>` 的调用可能会失败，因为不能序列化提供的自变量。 开发人员代码必须捕获异常。 如果事件处理程序或组件生命周期方法中的应用代码未处理异常，则生成的异常对于线路是致命的。
* 如果对 `InvokeAsync<T>` 的调用异步失败，则 .NET <xref:System.Threading.Tasks.Task> 会失败。 例如，对 `InvokeAsync<T>` 的调用可能会失败，这是因为 JavaScript 端代码引发异常或返回以 `rejected`完成的 `Promise`。 开发人员代码必须捕获异常。 如果使用[await](/dotnet/csharp/language-reference/keywords/await)运算符，请考虑在包含错误处理和日志记录的[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句中包装方法调用。 否则，失败的代码会导致未处理的异常，这是对线路至关重要的。
* 默认情况下，对 `InvokeAsync<T>` 的调用必须在特定时间段内完成，否则调用会超时。默认超时期限为一分钟。 超时可防止代码丢失网络连接或从不发送回完成消息的 JavaScript 代码。 如果调用超时，则生成的 `Task` 将失败，并出现 <xref:System.OperationCanceledException>。 捕获并处理日志记录的异常。

同样，JavaScript 代码可能会启动对[`[JSInvokable]`](xref:blazor/javascript-interop#invoke-net-methods-from-javascript-functions)属性所指示的 .net 方法的调用。 如果这些 .NET 方法引发未经处理的异常：

* 此异常不会被视为对线路的严重。
* JavaScript 端 `Promise` 被拒绝。

您可以选择在 .NET 端或方法调用的 JavaScript 端使用错误处理代码。

有关更多信息，请参见<xref:blazor/javascript-interop>。

### <a name="circuit-handlers"></a>线路处理程序

Blazor 允许代码定义一个*线路处理程序，该处理程序*在用户线路的状态发生更改时接收通知。 使用以下状态：

* `initialized`
* `connected`
* `disconnected`
* `disposed`

通过注册从 `CircuitHandler` 抽象基类继承的 DI 服务来管理通知。

如果自定义线路处理程序的方法引发未经处理的异常，则该异常对于线路是致命的。 若要容忍处理程序代码或调用方法中的异常，请使用错误处理和日志记录将代码包装在一个或多个[try-catch](/dotnet/csharp/language-reference/keywords/try-catch)语句中。

### <a name="circuit-disposal"></a>线路处置

当某个线路由于用户已断开连接并且该框架正在清理线路状态而结束时，框架会释放该线路的 DI 范围。 释放作用域将释放任何实现 <xref:System.IDisposable?displayProperty=fullName>的线路范围的 DI 服务。 如果任何 DI 服务在处理过程中引发未经处理的异常，则框架将记录该异常。

### <a name="prerendering"></a>呈现

::: moniker range=">= aspnetcore-3.1"

使用 `Component` 标记帮助器可以预呈现 Blazor 组件，以便在用户初始 HTTP 请求过程中返回其呈现的 HTML 标记。 此功能的工作方式如下：

* 为属于同一页面的所有预呈现组件创建新线路。
* 正在生成初始 HTML。
* 将线路视为 `disconnected`，直到用户的浏览器将 SignalR 连接回同一服务器。 建立连接后，将恢复对线路的交互，并更新组件的 HTML 标记。

如果任何组件在预呈现期间引发未经处理的异常，例如，在生命周期方法或呈现逻辑中：

* 此异常是线路致命的。
* 此异常将从 `Component` 标记帮助程序中的调用堆栈引发。 因此，整个 HTTP 请求将失败，除非开发人员代码显式捕获了异常。

在正常情况下，如果预呈现失败，则继续生成并呈现组件没有意义，因为无法呈现工作组件。

若要容忍在预呈现期间可能发生的错误，必须将错误处理逻辑放置在可能引发异常的组件中。 使用带有错误处理和日志记录的[try catch](/dotnet/csharp/language-reference/keywords/try-catch)语句。 不要将 `Component` 标记帮助程序包装在 `try-catch` 语句中，而是将错误处理逻辑放在由 `Component` 标记帮助器呈现的组件中。

::: moniker-end

::: moniker range="< aspnetcore-3.1"

可以使用 `Html.RenderComponentAsync` 预呈现 Blazor 组件，以便将其呈现的 HTML 标记作为用户初始的 HTTP 请求的一部分返回。 此功能的工作方式如下：

* 为属于同一页面的所有预呈现组件创建新线路。
* 正在生成初始 HTML。
* 将线路视为 `disconnected`，直到用户的浏览器将 SignalR 连接回同一服务器。 建立连接后，将恢复对线路的交互，并更新组件的 HTML 标记。

如果任何组件在预呈现期间引发未经处理的异常，例如，在生命周期方法或呈现逻辑中：

* 此异常是线路致命的。
* 调用堆栈从 `Html.RenderComponentAsync` 调用中引发了异常。 因此，整个 HTTP 请求将失败，除非开发人员代码显式捕获了异常。

在正常情况下，如果预呈现失败，则继续生成并呈现组件没有意义，因为无法呈现工作组件。

若要容忍在预呈现期间可能发生的错误，必须将错误处理逻辑放置在可能引发异常的组件中。 使用带有错误处理和日志记录的[try catch](/dotnet/csharp/language-reference/keywords/try-catch)语句。 不要在 `try-catch` 语句中包装对 `RenderComponentAsync` 的调用，而是将错误处理逻辑放在由 `RenderComponentAsync`呈现的组件中。

::: moniker-end

## <a name="advanced-scenarios"></a>高级方案

### <a name="recursive-rendering"></a>递归呈现

组件可以递归嵌套。 这适用于表示递归数据结构。 例如，`TreeNode` 组件可以为节点的每个子元素呈现更多 `TreeNode` 组件。

以递归方式呈现时，请避免将导致无限递归的编码模式：

* 不以递归方式呈现包含循环的数据结构。 例如，不呈现其子级包含其自身的树节点。
* 不要创建包含循环的布局链。 例如，请勿创建布局本身为的布局。
* 不允许最终用户通过恶意数据输入或 JavaScript 互操作调用来违反递归固定条件（规则）。

呈现过程中的无限循环：

* 导致呈现过程永久继续。
* 等效于创建未终止的循环。

在这些情况下，受影响的线路会挂起，并且该线程通常会尝试执行以下操作：

* 无限期地消耗操作系统允许的 CPU 时间。
* 消耗不限数量的服务器内存。 使用无限制内存等效于未终止的循环在每次迭代时向集合添加项的情况。

若要避免无限递归模式，请确保递归呈现代码包含合适的停止条件。

### <a name="custom-render-tree-logic"></a>自定义呈现器树根逻辑

大多数 Blazor 组件都作为*razor*文件实现，并且编译为生成可对 `RenderTreeBuilder` 进行操作以呈现其输出的逻辑。 开发人员可以使用过程C#代码手动实现 `RenderTreeBuilder` 逻辑。 有关更多信息，请参见<xref:blazor/components#manual-rendertreebuilder-logic>。

> [!WARNING]
> 使用手动渲染树生成器逻辑被视为一种高级不安全的方案，不建议用于常规组件开发。

如果编写 `RenderTreeBuilder` 代码，开发人员必须保证代码的正确性。 例如，开发人员必须确保：

* 对 `OpenElement` 和 `CloseElement` 的调用已正确平衡。
* 特性仅添加到正确的位置。

手动呈现树生成器逻辑不正确可能会导致任意未定义的行为，包括崩溃、服务器挂起和安全漏洞。

请考虑在相同程度的复杂性上手动呈现树生成器逻辑，并使用与手动编写程序集代码或 MSIL 指令相同的*危险*级别。
