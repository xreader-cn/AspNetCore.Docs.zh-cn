---
title: ASP.NET Core Blazor 状态管理
author: guardrex
description: 了解如何在 Blazor Server 应用程序中持久保存状态。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/state-management
ms.openlocfilehash: e1c3b030f466a820d49c36839d7ee26bb7cea4d3
ms.sourcegitcommit: 092061c4f6ef46ed2165fa84de6273d3786fb97e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70963856"
---
# <a name="aspnet-core-blazor-state-management"></a>ASP.NET Core Blazor 状态管理

作者：[Steve Sanderson](https://github.com/SteveSandersonMS)

Blazor 服务器是有状态的应用程序框架。 大多数情况下，应用保持与服务器的持续连接。 用户处于*线路*中的服务器内存中。 

用户线路的状态的示例包括：

* 呈现的 UI&mdash;，组件实例的层次结构及其最新的呈现输出。
* 组件实例中的任何字段和属性的值。
* 依赖于线路的[依赖关系注入（DI）](xref:fundamentals/dependency-injection)服务实例中保存的数据。

> [!NOTE]
> 本文介绍 Blazor 服务器应用中的状态持久性。 Blazor WebAssembly 应用程序可以[在浏览器中利用客户端状态持久性，但在](#client-side-in-the-browser)本文的讨论范围之外需要自定义解决方案或第三方包。

## <a name="blazor-circuits"></a>Blazor 电路

如果用户遇到暂时的网络连接丢失，Blazor 会尝试将用户重新连接到其原始线路，以便他们可以继续使用该应用。 但是，并不总是能够将用户重新连接到服务器内存中的原始线路：

* 服务器不能永久保留断开连接的线路。 在超时或服务器处于内存压力下时，服务器必须释放断开连接的线路。
* 在多服务器、负载平衡的部署环境中，任何服务器处理请求在任何给定时间都可能会变得不可用。 当不再需要处理请求的总数量时，单独的服务器可能会失败或自动删除。 当用户尝试重新连接时，原始服务器可能不可用。
* 用户可能会关闭并重新打开其浏览器或重新加载页面，这会删除浏览器内存中保存的任何状态。 例如，通过 JavaScript 互操作调用设置的值将丢失。

当无法将用户重新连接到其原始线路时，用户将收到一个具有空状态的新线路。 这等效于关闭和重新打开桌面应用程序。

## <a name="preserve-state-across-circuits"></a>跨线路保留状态

在某些情况下，需要保留线路上的状态。 如果出现以下情况，应用可以为用户保留重要数据：

* Web 服务器不可用。
* 用户的浏览器将强制使用新的 web 服务器启动新线路。

通常情况下，跨线路维护状态适用于用户主动创建数据的情况，不只是读取已存在的数据。

若要将状态保留在单个线路以外，请*不要只将数据存储在服务器的内存中*。 应用必须将数据保存到其他某个存储位置。 状态持久性不是&mdash;自动的，你必须在开发应用程序时采取措施来实现有状态数据持久性。

数据暂留通常只需要用于用户需要花费大量精力才能创建的高值状态。 在下面的示例中，保存状态可节省商业活动的时间或辅助：

* 多步骤 webform &ndash; ，如果用户的状态丢失，则用户需要为多步骤进程的几个已完成步骤重新输入数据。 如果用户离开多步骤窗体并稍后返回窗体，则该用户将失去状态。
* 购物车&ndash;可以维持任何代表潜在收入的应用的商业重要组件。 如果用户的购物车丢失了其状态，因此他们的购物车以后可能会购买更少的产品或服务。

通常不需要保留轻松重新创建的状态，例如输入到未提交的登录对话框中的用户名。

> [!IMPORTANT]
> 应用只能保留*应用状态*。 不能持久保存 Ui，如组件实例及其呈现树。 组件和呈现树通常不能序列化。 若要保存类似于 UI 状态的内容（如 TreeView 的扩展节点），应用必须具有自定义代码，以便将行为建模为可序列化应用状态。

## <a name="where-to-persist-state"></a>保留状态的位置

在 Blazor 服务器应用程序中保存状态有三个常见位置。 每种方法最适合于不同的方案，并且有不同的注意事项：

* [数据库中的服务器端](#server-side-in-a-database)
* [URL](#url)
* [浏览器中的客户端](#client-side-in-the-browser)

### <a name="server-side-in-a-database"></a>数据库中的服务器端

对于永久数据持久性或必须跨多个用户或设备的任何数据，一个独立的服务器端数据库几乎无疑是最佳选择。 选项包括：

* 关系 SQL 数据库
* 键-值存储
* Blob 存储区
* 表存储

将数据保存到数据库后，用户可以随时启动新线路。 用户的数据会保留在任何新线路中并可用。

有关 Azure 数据存储选项的详细信息，请参阅[Azure 存储空间文档](/azure/storage/)和[azure 数据库](https://azure.microsoft.com/product-categories/databases/)。

### <a name="url"></a>URL

对于表示导航状态的暂时性数据，请将数据作为 URL 的一部分进行建模。 URL 中的状态模型示例包括：

* 已查看实体的 ID。
* 分页网格中的当前页码。

将保留浏览器地址栏的内容：

* 如果用户手动重新加载页面，则为。
* 如果 web 服务器变为不可用&mdash;，则会强制用户重新加载页面，以便连接到其他服务器。

有关用`@page`指令定义 URL 模式的信息，请参阅<xref:blazor/routing>。

### <a name="client-side-in-the-browser"></a>浏览器中的客户端

对于用户正在主动创建的暂时性数据，公共后备存储是浏览器的`localStorage`和`sessionStorage`集合。 如果放弃该线路，则无需使用该应用程序管理或清除已存储状态，这与服务器端存储相比，这是一项优势。

> [!NOTE]
> 本部分中的 "客户端" 是指浏览器中的客户端方案，而不是[Blazor WebAssembly 托管模型](xref:blazor/hosting-models#blazor-webassembly)。 `localStorage`和`sessionStorage`可在 Blazor WebAssembly 应用程序中使用，但只能通过编写自定义代码或使用第三方包来使用。

`localStorage`和`sessionStorage`不同之处如下：

* `localStorage`的作用域限定为用户的浏览器。 如果用户重新加载页面或关闭并重新打开浏览器，则状态将保持不变。 如果用户打开多个浏览器选项卡，则状态在选项卡上共享。 数据一直保留`localStorage`在明确清除之前。
* `sessionStorage`的作用域限定为用户的浏览器选项卡。如果用户重新加载该选项卡，则状态将保持不变。 如果用户关闭该选项卡或浏览器，则状态将丢失。 如果用户打开多个浏览器选项卡，则每个选项卡都有自己独立的数据版本。

通常， `sessionStorage`使用更安全。 `sessionStorage`避免了用户打开多个选项卡并遇到以下问题的风险：

* 跨选项卡的状态存储中的 bug。
* 当选项卡覆盖其他选项卡状态时，混乱的行为。

`localStorage`如果应用必须在关闭时保持状态，然后重新打开浏览器，则是更好的选择。

使用浏览器存储时的注意事项：

* 与使用服务器端数据库类似，数据的加载和保存都是异步的。
* 与服务器端数据库不同，在预呈现期间，存储不能使用，因为在预呈现阶段，请求的页面在浏览器中不存在。
* 存储少量数据对于 Blazor 服务器应用而言是合理的。 除了几 kb 外，还必须考虑性能的影响，因为数据是通过网络加载并保存的。
* 用户可以查看或篡改数据。 ASP.NET Core 的[数据保护](xref:security/data-protection/introduction)可以降低风险。

## <a name="third-party-browser-storage-solutions"></a>第三方浏览器存储解决方案

第三方 NuGet 包提供使用`localStorage`和`sessionStorage`的 api。

值得一提的是，选择一个可透明地使用 ASP.NET Core 的[数据保护](xref:security/data-protection/introduction)的包。 ASP.NET Core 的数据保护会对存储的数据进行加密，并降低篡改存储数据的潜在风险。 如果 JSON 序列化的数据以纯文本形式存储，则用户可以使用浏览器开发人员工具查看数据，还可以修改存储的数据。 保护数据并不总是问题，因为数据在性质上可能很简单。 例如，读取或修改 UI 元素的存储颜色不会对用户或组织造成严重的安全风险。 避免允许用户检查或篡改*敏感数据*。

## <a name="protected-browser-storage-experimental-package"></a>受保护的浏览器存储试验包

为提供[数据保护](xref:security/data-protection/introduction) `localStorage` `sessionStorage`的 NuGet 包的一个示例是[AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)。

> [!WARNING]
> `Microsoft.AspNetCore.ProtectedBrowserStorage`是一个不受支持的实验性包，此时不适合用于生产。

### <a name="installation"></a>安装

安装`Microsoft.AspNetCore.ProtectedBrowserStorage`包：

1. 在 Blazor 服务器应用程序项目中，将包引用添加到[AspNetCore. ProtectedBrowserStorage](https://www.nuget.org/packages/Microsoft.AspNetCore.ProtectedBrowserStorage)。
1. 在顶级 HTML （例如，在默认项目模板中的*Pages/_Host*文件中）添加以下`<script>`标记：

   ```html
   <script src="_content/Microsoft.AspNetCore.ProtectedBrowserStorage/protectedBrowserStorage.js"></script>
   ```

1. 在方法中，调用`AddProtectedBrowserStorage`以将`localStorage`和`sessionStorage`服务添加到服务集合： `Startup.ConfigureServices`

   ```csharp
   services.AddProtectedBrowserStorage();
   ```

### <a name="save-and-load-data-within-a-component"></a>保存和加载组件中的数据

在需要将数据加载到浏览器存储或将数据保存到[@inject](xref:blazor/dependency-injection#request-a-service-in-a-component)浏览器存储的任何组件中，使用注入下列任一的实例：

* `ProtectedLocalStorage`
* `ProtectedSessionStorage`

选择取决于要使用的备份存储区。 在下面的示例中`sessionStorage` ，使用：

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore
```

语句可以放在 _Imports 文件中，而不是组件中。 `@using` 使用 *_Imports*文件可使命名空间可用于应用或整个应用的更大段。

若要`currentCount` `ProtectedSessionStore.SetAsync`在项目模板的`Counter`组件中保留该值，请将方法`IncrementCount`修改为使用：

```csharp
private async Task IncrementCount()
{
    currentCount++;
    await ProtectedSessionStore.SetAsync("count", currentCount);
}
```

在更大、更真实的应用中，每个字段的存储都是不太可能的方案。 应用更有可能存储包含复杂状态的整个模型对象。 `ProtectedSessionStore`自动序列化并反序列化 JSON 数据。

在上面的代码示例中， `currentCount`数据存储为`sessionStorage['count']`用户浏览器中的。 数据不会以纯文本形式存储，而是使用 ASP.NET Core 的[数据保护](xref:security/data-protection/introduction)进行保护。 如果`sessionStorage['count']`在浏览器的开发人员控制台中计算，则可以查看加密的数据。

若要在`currentCount`用户以后返回`Counter`到组件时恢复数据（包括在全新线路上时），请使用`ProtectedSessionStore.GetAsync`：

```csharp
protected override async Task OnInitializedAsync()
{
    currentCount = await ProtectedSessionStore.GetAsync<int>("count");
}
```

如果组件的参数包括导航状态，请调用`ProtectedSessionStore.GetAsync` `OnParametersSetAsync`，并将结果分配到， `OnInitializedAsync`而不是。 `OnInitializedAsync`仅在首次实例化组件时调用一次。 `OnInitializedAsync`如果用户在同一页面上保留到不同的 URL，则不会再次调用。

> [!WARNING]
> 本部分中的示例仅适用于服务器未启用预呈现功能的情况。 启用预呈现后，会生成错误，如下所示：
>
> > 此时无法发出 JavaScript 互操作调用。 这是因为该组件正在预呈现。
>
> 禁用预呈现或添加其他代码以使用预呈现。 若要了解有关编写适用于预呈现的代码的详细信息，请参阅[处理预呈现](#handle-prerendering)部分。

### <a name="handle-the-loading-state"></a>处理加载状态

由于浏览器存储是异步的（通过网络连接进行访问），因此，在加载数据之前始终有一段时间，并且可供组件使用。 为获得最佳结果，请在加载正在进行时呈现加载状态消息，而不是显示空数据或默认数据。

一种方法是跟踪数据是否为`null` （仍在加载）。 在默认`Counter`组件中，计数保存`int`在中。 将问号（`?`）添加到类型（`int`），可以为null：`currentCount`

```csharp
private int? currentCount;
```

如果只是加载数据，请选择仅显示这些元素，而不是无条件地显示 "计数和**增量**" 按钮：

```cshtml
@if (currentCount.HasValue)
{
    <p>Current count: <strong>@currentCount</strong></p>

    <button @onclick="IncrementCount">Increment</button>
}
else
{
    <p>Loading...</p>
}
```

### <a name="handle-prerendering"></a>处理预呈现

预呈现期间：

* 与用户浏览器之间的交互连接不存在。
* 浏览器还没有可在其中运行 JavaScript 代码的页面。

`localStorage`或`sessionStorage`在预呈现期间不可用。 如果组件尝试与存储交互，则会生成类似于以下内容的错误：

> 此时无法发出 JavaScript 互操作调用。 这是因为该组件正在预呈现。

解决错误的一种方法是禁用预呈现。 如果应用大量使用基于浏览器的存储，则这通常是最佳选择。 预呈现增加了复杂性，因此不会给应用带来好处，因为应用无法`localStorage`将`sessionStorage`任何有用的内容预呈现到或可用。

若要禁用预呈现，请打开*Pages/_Host*文件并将对的调用`Html.RenderComponentAsync<App>(RenderMode.Server)`更改为。

对于不使用`localStorage`或`sessionStorage`的其他页，预呈现可能很有用。 要使预呈现功能保持启用状态，请推迟加载操作，直到浏览器连接到线路。 下面是存储计数器值的示例：

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedLocalStorage ProtectedLocalStore
@inject IComponentContext ComponentContext

... rendering code goes here ...

@code {
    private int? currentCount;
    private bool isWaitingForConnection;

    protected override async Task OnInitializedAsync()
    {
        if (ComponentContext.IsConnected)
        {
            // It looks like the app isn't prerendering, so the data can be
            // immediately loaded from browser storage.
            await LoadStateAsync();
        }
        else
        {
            // Prerendering is in progress, so the app defers the load operation
            // until later.
            isWaitingForConnection = true;
        }
    }

    protected override async Task OnAfterRenderAsync(bool firstRender)
    {
        // By this stage, the client has connected back to the server, and
        // browser services are available. If the app didn't load the data earlier,
        // the app should do so now and then trigger a new render.
        if (firstRender && isWaitingForConnection)
        {
            isWaitingForConnection = false;
            await LoadStateAsync();
            StateHasChanged();
        }
    }

    private async Task LoadStateAsync()
    {
        currentCount = await ProtectedLocalStore.GetAsync<int>("prerenderedCount");
    }

    private async Task IncrementCount()
    {
        currentCount++;
        await ProtectedSessionStore.SetAsync("count", currentCount);
    }
}
```

### <a name="factor-out-the-state-preservation-to-a-common-location"></a>将状态保存分解到常见位置

如果许多组件依赖于基于浏览器的存储，则多次重新实现状态提供程序代码会创建代码重复。 避免代码重复的一个选择是创建一个用于封装状态提供程序逻辑的*状态提供程序父组件*。 子组件可以使用永久性数据，而不考虑状态持久性机制。

在以下`CounterStateProvider`组件示例中，将保留计数器数据：

```cshtml
@using Microsoft.AspNetCore.ProtectedBrowserStorage
@inject ProtectedSessionStorage ProtectedSessionStore

@if (hasLoaded)
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
    private bool hasLoaded;

    [Parameter]
    public RenderFragment ChildContent { get; set; }

    public int CurrentCount { get; set; }

    protected override async Task OnInitializedAsync()
    {
        CurrentCount = await ProtectedSessionStore.GetAsync<int>("count");
        hasLoaded = true;
    }

    public async Task SaveChangesAsync()
    {
        await ProtectedSessionStore.SetAsync("count", CurrentCount);
    }
}
```

`CounterStateProvider`组件通过在加载完成之前不呈现其子内容来处理加载阶段。

若要使用`CounterStateProvider`该组件，请围绕需要访问计数器状态的任何其他组件环绕组件实例。 若要使某个应用中的所有组件都可以访问该状态`CounterStateProvider` ，请在`Router` `App`组件（*app.config*）中围绕的组件：

```cshtml
<CounterStateProvider>
    <Router AppAssembly="typeof(Startup).Assembly">
        ...
    </Router>
</CounterStateProvider>
```

包装的组件接收并可以修改持久化计数器状态。 以下`Counter`组件实现模式：

```cshtml
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

前面的组件无需与进行交互`ProtectedBrowserStorage`，也不会处理 "正在加载" 阶段。

若要处理前面所述的预`CounterStateProvider`呈现，可以修改以使使用计数器数据的所有组件自动使用预呈现。 有关详细信息，请参阅[处理预呈现](#handle-prerendering)部分。

通常，建议使用*状态提供程序父组件*模式：

* 在许多其他组件中使用状态。
* 如果只有一个顶级状态对象要保持，则为。

若要保留许多不同的状态对象并在不同的位置使用不同的对象子集，最好避免在全局处理状态的加载和保存。
