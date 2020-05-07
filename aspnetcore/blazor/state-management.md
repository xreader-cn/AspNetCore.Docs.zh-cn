---
title: ASP.NET Core Blazor 状态管理
author: guardrex
description: 了解如何在 Blazor 服务器应用中保留状态。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/17/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/state-management
ms.openlocfilehash: 75d9a66eb25201c2993b8f922754b8aa7ab84615
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771154"
---
# <a name="aspnet-core-blazor-state-management"></a>ASP.NET Core Blazor 状态管理

作者：[Steve Sanderson](https://github.com/SteveSandersonMS)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 服务器是有状态的应用框架。 大多数情况下，应用保持与服务器的持续连接。 用户的状态保留在线路中的服务器内存中  。 

为用户线路保留的状态示例包括：

* 呈现的 UI &mdash; 组件实例的层次结构及其最新的呈现输出。
* 组件实例中的任何字段和属性的值。
* 在线路范围内的[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 服务实例中保留的数据。

> [!NOTE]
> 本文介绍 Blazor 服务器应用中的状态暂留。 Blazor WebAssembly 应用可以利用[浏览器中的客户端状态暂留](#client-side-in-the-browser)，但需要自定义解决方案或第三方包（这些并不在本文的讨论范围之内）。

## <a name="blazor-circuits"></a>Blazor 线路

如果用户遇到暂时的网络连接丢失问题，Blazor 会尝试将用户重新连接到其原始线路，以便用户继续使用该应用。 但是，将用户重新连接到服务器内存中的原始电路并非总是能够实现的：

* 服务器不能永久保留断开连接的线路。 超时后或在服务器面临内存压力时，服务器必须释放断开连接的线路。
* 在多服务器、负载均衡的部署环境中，任何服务器处理请求在任何给定时间都可能变得不可用。 不再需要单个服务器处理整个请求量时，它可能会失败或被自动删除。 当用户尝试重新连接时，原始服务器可能不可用。
* 用户可能会关闭并重新打开其浏览器或重载页面，这会删除浏览器内存中保留的所有状态。 例如，通过 JavaScript 互操作调用设置的值将丢失。

当无法将用户重新连接到其原始线路时，用户将收到一个具有空状态的新线路。 这等效于关闭并重新打开桌面应用。

## <a name="preserve-state-across-circuits"></a>跨线路保留状态

在某些应用场景下，需要跨线路保留状态。 如果出现以下情况，应用可以为用户保留重要数据：

* Web 服务器不可用。
* 用户的浏览器被强制使用新的 Web 服务器启动新线路。

通常情况下，跨线路保持状态适用于用户主动创建数据，而不是简单地读取已存在的数据的应用场景。

若要在单个电路之外保留状态，请勿只是将数据存储在服务器的内存中  。 应用必须将数据保留到其他存储位置。 状态暂留并非是自动进行的 &mdash; 必须在开发应用时采取措施来实现有状态的数据暂留。

通常，只有用户投入了大量精力所创建的高价值状态才需要数据暂留。 在下面的示例中，保留状态可以节省时间或有助于商业活动：

* 多步骤 Web 窗体 &ndash; 在一个多步骤进程中，如果用户的状态丢失，则需为多个已完成步骤重新输入数据，而这非常耗时。 如果用户离开多步骤窗体并在稍后返回，在这种应用场景下，用户将失去状态。
* 购物车 &ndash; 代表潜在收入的应用的所有具有重要商业价值的组件都可进行维护。 如果用户丢失了其状态，进而丢失了其购物车，则在他们稍后返回站点时可购买较少的产品或服务。

通常不需要保留易于重新创建的状态，例如在登录对话框中输入的尚未提交的用户名。

> [!IMPORTANT]
> 应用只能保留应用状态  。 不能保留 UI，如组件实例及其呈现树。 组件和呈现树通常不能序列化。 若要保留类似于 UI 状态的内容（如 TreeView 的展开节点），应用必须具有自定义代码，以便将行为建模为可序列化应用状态。

## <a name="where-to-persist-state"></a>保留状态的位置

有三个常见位置用于保留 Blazor 服务器应用中的状态。 每种方法分别适用于不同的应用场景，且有不同的注意事项：

* [数据库中的服务器端](#server-side-in-a-database)
* [URL](#url)
* [浏览器中的客户端](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>数据库中的服务器端

对于永久数据暂留或必须跨多个用户或设备的任何数据，独立的服务器端数据库几乎肯定是最佳选择。 选项包括：

* 关系 SQL 数据库
* 键值存储
* Blob 存储
* 表存储

将数据保存到数据库后，用户可以随时启动新线路。 用户数据会保留且在任何新线路中可用。

有关 Azure 数据存储选项的详细信息，请参阅 [Azure 存储文档](/azure/storage/)和 [Azure 数据库](https://azure.microsoft.com/product-categories/databases/)。

### <a name="url"></a>URL

对于表示导航状态的暂时性数据，请将数据作为 URL 的一部分进行建模。 URL 中建模的状态示例包括：

* 已查看实体的 ID。
* 分页网格中的当前页码。

保留浏览器地址栏的内容：

* 如果用户手动重载页面。
* 如果 Web 服务器不可用 &mdash; 用户被强制重载页面，以便连接到其他服务器。

有关使用 `@page` 指令定义 URL 模式的信息，请参阅 <xref:blazor/routing>。

### <a name="client-side-in-the-browser"></a>浏览器中的客户端

对于用户正在主动创建的暂时性数据，通用后备存储是浏览器的 `localStorage` 和 `sessionStorage` 集合。 如果放弃该线路，则应用无需管理或清除存储的状态。与服务器端存储相比，这是一项优势。

> [!NOTE]
> 本节中的“客户端”是指浏览器中的客户端方案，而不是 [BlazorWebAssembly 托管模型](xref:blazor/hosting-models#blazor-webassembly)。 `localStorage` 和 `sessionStorage` 可用于 Blazor WebAssembly 应用，但只能通过编写自定义代码或使用第三方包进行使用。

`localStorage` 和 `sessionStorage` 的区别如下：

* `localStorage` 的应用范围限定为用户的浏览器。 如果用户重载页面或关闭并重新打开浏览器，则状态保持不变。 如果用户打开多个浏览器选项卡，则状态跨选项卡共享。 数据保留在 `localStorage` 中，直到被显式清除为止。
* `sessionStorage` 的应用范围限定为用户的浏览器选项卡。如果用户重载该选项卡，则状态保持不变。 如果用户关闭该选项卡或该浏览器，则状态丢失。 如果用户打开多个浏览器选项卡，则每个选项卡都有自己独立的数据版本。

通常，`sessionStorage` 使用起来更安全。 `sessionStorage` 避免了用户打开多个选项卡并遇到以下问题的风险：

* 跨选项卡的状态存储中出现 bug。
* 一个选项卡覆盖其他选项卡的状态时出现混乱行为。

如果应用必须在关闭和重新打开浏览器期间保持状态，则 `localStorage` 是更好的选择。

使用浏览器存储时的注意事项：

* 与使用服务器端数据库类似，加载和保存数据都是异步的。
* 与服务器端数据库不同，在预呈现期间，存储不可用，因为在预呈现阶段，请求的页面在浏览器中不存在。
* 保留状态的位置对于 Blazor 服务器应用，持久存储几千字节的数据是合理的。 超出几千字节后，你就须考虑性能影响，因为数据是跨网络加载和保存的。
* 用户可以查看或篡改数据。 ASP.NET Core [数据保护](xref:security/data-protection/introduction)可以降低风险。

## <a name="third-party-browser-storage-solutions"></a>第三方浏览器存储解决方案

第三方 NuGet 包提供使用 `localStorage` 和 `sessionStorage` 时采用的 API。

值得考虑的是，选择一个透明地使用 ASP.NET Core [数据保护](xref:security/data-protection/introduction)的包。 ASP.NET Core 数据保护可对存储的数据进行加密，并降低篡改存储数据的潜在风险。 如果 JSON 序列化的数据以纯文本形式存储，则用户可以使用浏览器开发人员工具查看数据，还可以修改存储的数据。 保护数据并非总是一个问题，因为有些数据本质上可能是无足轻重的。 例如，读取或修改 UI 元素的存储颜色不会对用户或组织造成严重的安全风险。 避免允许用户检查或篡改敏感数据  。

## <a name="protected-browser-storage-experimental-package"></a>受保护的浏览器存储实验性包

[Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage) 就是一个为 `localStorage` 和 `sessionStorage` 提供[数据保护](xref:security/data-protection/introduction)的 NuGet 包示例。

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage` 是一个不受支持的实验性包，目前不适合用于生产。

### <a name="installation"></a>安装

安装 `Microsoft.AspNetCore.ProtectedBrowserStorage` 包：

1. 在 Blazor 服务器应用项目中，将包引用添加到 [Microsoft.AspNetCore.ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)。
1. 在顶级 HTML（例如，在默认项目模板中的“Pages/_Host.cshtml”文件中）中，添加以下 `<script>` 标记  ：

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. 在 `Startup.ConfigureServices` 方法中，调用 `AddProtectedBrowserStorage` 以将 `localStorage` 和 `sessionStorage` 服务添加到服务集合：

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>保存和加载组件中的数据

在需要将数据加载或保存到浏览器存储的任何组件中，使用 [`@inject`](xref:blazor/dependency-injection#request-a-service-in-a-component) 注入以下任意一项的实例：

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

此选择取决于你要使用的后备存储。 在以下示例中，使用 `sessionStorage`：

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

可将 `@using` 语句放置在“_Imports.razor”文件而不是组件中  。 使用“_Imports.razor”文件可使命名空间可用于应用的较大部分或整个应用  。

若要在项目模板的 `Counter` 组件中保留 `_currentCount` 值，请修改 `IncrementCount` 方法以使用 `ProtectedSessionStore.SetAsync`：

```csharp
private async Task IncrementCount()
{
    _currentCount++;
    await ProtectedSessionStore.SetAsync("count", _currentCount);
}
```

在更大、更真实的应用中，存储单个字段是不太可能出现的情况。 应用更有可能存储包含复杂状态的整个模型对象。 `ProtectedSessionStore` 自动串行化和反序列化 JSON 数据。

在前面的代码示例中，`_currentCount` 数据存储为用户浏览器中的 `sessionStorage['count']`。 数据不会以纯文本形式存储，而是使用 ASP.NET Core 的[数据保护](xref:security/data-protection/introduction)进行保护。 如果在浏览器的开发人员控制台中评估了 `sessionStorage['count']`，则可以查看加密的数据。

若要在用户稍后返回到 `Counter` 组件时（包括他们位于全新线路上时）恢复 `_currentCount` 数据，请使用 `ProtectedSessionStore.GetAsync`：

```csharp
protected override async Task OnInitializedAsync()
{
    _currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

如果组件的参数包括导航状态，请调用 `ProtectedSessionStore.GetAsync` 并将结果分配给 `OnParametersSetAsync`，而不是 `OnInitializedAsync`。 `OnInitializedAsync` 仅在首次实例化组件时调用一次。 如果用户导航到不同的 URL，而仍然停留在相同的页面上，则 `OnInitializedAsync` 之后不会再次调用。 有关详细信息，请参阅 <xref:blazor/lifecycle>。

> [!WARNING]
> 本节中的示例仅在服务器未启用预呈现的情况下有效。 启用预呈现后，将生成如下错误：
>
> > 此时无法发出 JavaScript 互操作调用。 这是因为该组件已预呈现。
>
> 禁用预呈现或添加其他代码以处理预呈现。 若要了解有关编写可处理预呈现的代码的详细信息，请参阅[处理预呈现](#handle-prerendering)一节。

### <a name="handle-the-loading-state"></a>处理加载状态

由于浏览器存储是异步存储（通过网络连接进行访问），因此在数据已加载并可供组件使用之前始终需要一段时间。 为获得最佳结果，请在加载进行过程中呈现加载状态消息，而不要显示空数据或默认数据。

一种方法是跟踪数据是否为 `null`（仍在加载）。 在默认 `Counter` 组件中，计数保留在 `int` 中。 通过将问号 (`?`) 添加到类型 (`int`)，使 `_currentCount` 可以为 null：

```csharp
private int? _currentCount;
```

请勿无条件地显示计数和“增量”按钮  ，而选择仅在数据已加载后才显示这些元素：

```razor
@if (_currentCount.HasValue)
{
    <p>Current count: <strong>@_currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>处理预呈现

预呈现期间：

* 与用户浏览器的交互式连接不存在。
* 浏览器尚无可在其中运行 JavaScript 代码的页面。

在预呈现期间，`localStorage` 或 `sessionStorage` 不可用。 如果组件尝试与存储进行交互，将生成如下错误：

> 此时无法发出 JavaScript 互操作调用。 这是因为该组件已预呈现。

解决此错误的一种方法是禁用预呈现。 如果应用大量使用基于浏览器的存储，则这通常是最佳选择。 预呈现会增加复杂性，且不会给应用带来好处，因为在 `localStorage` 或 `sessionStorage` 可用之前，应用无法预呈现任何有用的内容。

若要禁用预呈现，请打开“Pages/_Host.cshtml”文件，并将[组件标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/component-tag-helper)的 `render-mode` 更改为 <xref:Microsoft.AspNetCore.Mvc.Rendering.RenderMode.Server>  。

对于不使用 `localStorage` 或 `sessionStorage` 的其他页面，预呈现可能很有用。 若要使预呈现保持启用状态，可延迟加载操作，直到浏览器连接到线路。 以下是存储计数器值的示例：

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore

... rendering code goes here ...

@code {
    private int? _currentCount;
    private bool _isConnected = false;

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        if (firstRender)
        {
            // When execution reaches this point, the first *interactive* render
            // is complete. The component has an active connection to the browser.
            _isConnected = true;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        _currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        _currentCount++;
        await ProtectedSessionStore.SetAsync("count", _currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>将状态保留提取到常见位置

如果多个组件依赖于基于浏览器的存储，则多次重新实现状态提供程序代码会造成代码重复。 若要避免代码重复，一种方法是创建一个封装了状态提供程序逻辑的状态提供程序父组件  。 子组件可处理保留的数据，而无需考虑状态暂留机制。

在 `CounterStateProvider` 组件的以下示例中，保留计数器数据：

```razor
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (_hasLoaded)
{
    <CascadingValue Value="@this">
        @ChildContent
    </CascadingValue>
}
else
{
    <p>Loading...</p>
}

@code {
    private bool _hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        _hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider` 组件处理加载阶段的方式是在加载完成后才呈现其子内容。

若要使用 `CounterStateProvider` 组件，请围绕需要访问计数器状态的任何其他组件包装该组件的实例。 若要使某个应用中的所有组件都可以访问该状态，请围绕 `App` 组件 (App.razor) 中的 `Router` 来包装 `CounterStateProvider` 组件  ：

```razor
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

已包装的组件接收并可修改保留的计数器状态。 以下 `Counter` 组件实现了该模式：

```razor
@page "/counter"

<p>Current count: <strong>@CounterStateProvider.CurrentCount</strong></p>

<button @onclick="IncrementCount">Increment</button>

@code {
    [CascadingParameter]
    private CounterStateProvider CounterStateProvider { get; set; }

    private async Task IncrementCount()
    {
        CounterStateProvider.CurrentCount++;
        await CounterStateProvider.SaveChangesAsync();
    }
}
```

与 `ProtectedBrowserStorage` 进行交互无需前面的组件，该组件也不会处理“正在加载”阶段。

如前所述，若要处理预呈现，可对 `CounterStateProvider` 进行修改，以便所有使用计数器数据的组件均可自动处理预呈现。 有关详细信息，请参阅[处理预呈现](#handle-prerendering)一节。

通常，建议在以下情况下使用状态提供程序父组件模式  ：

* 在多个其他组件中使用状态时。
* 只有一个顶级状态对象要保留时。

若要保留多个不同的状态对象并在不同位置使用不同的对象子集，最好避免全局地处理状态的加载和保存。
