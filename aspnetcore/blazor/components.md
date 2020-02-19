---
title: 创建和使用 ASP.NET Core Razor 组件
author: guardrex
description: 了解如何创建和使用 Razor 组件，包括如何绑定到数据、处理事件和管理组件生命周期。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2020
no-loc:
- Blazor
- SignalR
uid: blazor/components
ms.openlocfilehash: f9b4eab29fafe8113528062f57d28dadd0f57577
ms.sourcegitcommit: 6645435fc8f5092fc7e923742e85592b56e37ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2020
ms.locfileid: "77447095"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>创建和使用 ASP.NET Core Razor 组件

作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

使用*组件*构建 Blazor 应用。 组件是自包含的用户界面（UI）块，如页、对话框或窗体。 组件包含插入数据或响应 UI 事件所需的 HTML 标记和处理逻辑。 组件非常灵活且轻型。 它们可以嵌套、重复使用和在项目之间共享。

## <a name="component-classes"></a>组件类

在[razor](xref:mvc/views/razor)组件文件（*razor*）中，使用和 HTML 标记的C#组合实现了组件。 Blazor 中的组件正式称为*Razor 组件*。

组件的名称必须以大写字符开头。 例如， *MyCoolComponent*是有效的，并且*MyCoolComponent*无效。

使用 HTML 定义组件的 UI。 动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。 在编译应用程序时，会将 HTML 标记C#和呈现逻辑转换为组件类。 生成的类的名称与文件的名称匹配。

组件类的成员在 `@code` 块中定义。 在 `@code` 块中，组件状态（属性、字段）是通过用于事件处理的方法或定义其他组件逻辑来指定的。 允许多个 `@code` 块。

可以使用C#以 `@`开头的表达式将组件成员作为组件的呈现逻辑的一部分。 例如， C#字段通过在字段名称 `@` 前缀来呈现。 下面的示例计算并呈现：

* `_headingFontStyle` `font-style`的 CSS 属性值。
* `_headingText` 到 `<h1>` 元素的内容。

```razor
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

最初呈现组件后，组件会为响应事件而重新生成其呈现树。 然后 Blazor 将新的呈现树与上一个树进行比较，并将所有修改应用于浏览器的文档对象模型（DOM）。

组件是普通C#类，可以放置在项目中的任何位置。 生成网页的组件通常位于*Pages*文件夹中。 非页组件通常放置在*共享*文件夹中或添加到项目的自定义文件夹中。

通常，组件的命名空间是从应用程序的根命名空间和该组件在应用内的位置（文件夹）派生而来的。 如果应用的根命名空间是 `BlazorApp` 的，并且 `Counter` 组件位于*Pages*文件夹中：

* `Counter` 组件的命名空间为 `BlazorApp.Pages`。
* 组件的完全限定类型名称为 `BlazorApp.Pages.Counter`。

有关详细信息，请参阅[导入组件](#import-components)部分。

若要使用自定义文件夹，请将自定义文件夹的命名空间添加到父组件或应用程序的 *_Imports*文件。 例如，以下命名空间使*组件*文件夹中的组件在应用的根命名空间 `BlazorApp`时可用：

```razor
@using BlazorApp.Components
```

## <a name="tag-helpers-arent-used-in-components"></a>标记帮助程序不用于组件

Razor 组件（*razor*文件）不支持[标记帮助](xref:mvc/views/tag-helpers/intro)程序。 若要在 Blazor中提供标记帮助程序的功能，请创建一个组件，其功能与标记帮助程序相同，并改为使用该组件。

## <a name="use-components"></a>使用组件

组件可以通过使用 HTML 元素语法声明组件来包含其他组件。 使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。

属性绑定区分大小写。 例如，`@bind` 是有效的，并且 `@Bind` 无效。

*Index*中的以下标记会呈现 `HeadingComponent` 实例：

```razor
<HeadingComponent />
```

*组件/HeadingComponent*：

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

如果某个组件包含一个 HTML 元素，该元素的首字母大写字母与组件名称不匹配，则会发出警告，指示该元素具有意外的名称。 添加组件命名空间的 `@using` 指令使组件可用，从而解决了此警告。

## <a name="routing"></a>路由

可以通过为应用中的每个可访问组件提供路由模板来实现 Blazor 中的路由。

编译具有 `@page` 指令的 Razor 文件时，将为生成的类指定 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 指定路由模板的。 在运行时，路由器将使用 `RouteAttribute` 查找组件类，并呈现哪个组件包含与请求的 URL 匹配的路由模板。

```razor
@page "/ParentComponent"

...
```

有关详细信息，请参阅 <xref:blazor/routing>。

## <a name="parameters"></a>parameters

### <a name="route-parameters"></a>路由参数

组件可以接收 `@page` 指令中提供的路由模板中的路由参数。 路由器使用路由参数来填充相应的组件参数。

*Pages/RouteParameter*：

[!code-razor[](components/samples_snapshot/RouteParameter.razor?highlight=2,7-8)]

不支持可选参数，因此在前面的示例中应用了两个 `@page` 指令。 第一个允许导航到没有参数的组件。 第二个 `@page` 指令接收 `{text}` 路由参数，并将该值分配给 `Text` 属性。

*捕获所有*参数语法（`*`/`**`），该语法跨多个文件夹边界捕获路径，而 razor 组件（*razor*）**不**支持此语法。

### <a name="component-parameters"></a>组件参数

组件可以具有*组件参数*，这些参数是使用组件类上的公共属性和 `[Parameter]` 特性定义的。 使用这些属性在标记中为组件指定参数。

*组件/ChildComponent*：

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=2,11-12)]

在下面的示例应用程序示例中，`ParentComponent` 设置 `ChildComponent`的 `Title` 属性的值。

*Pages/ParentComponent*：

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=5-6)]

## <a name="child-content"></a>子内容

组件可以设置另一个组件的内容。 分配组件提供用于指定接收组件的标记之间的内容。

在下面的示例中，`ChildComponent` 具有一个表示 `RenderFragment`的 `ChildContent` 属性，该属性表示要呈现的 UI 段。 `ChildContent` 的值放置在应呈现内容的组件标记中。 `ChildContent` 的值从父组件接收，并呈现在启动面板的 `panel-body`中。

*组件/ChildComponent*：

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> 必须按约定将接收 `RenderFragment` 内容的属性命名为 `ChildContent`。

示例应用中的 `ParentComponent` 可以通过将内容置于 `<ChildComponent>` 标记中，提供用于呈现 `ChildComponent` 的内容。

*Pages/ParentComponent*：

[!code-razor[](components/samples_snapshot/ParentComponent.razor?highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Attribute 展开和任意参数

除了组件的声明参数外，组件还可以捕获和呈现附加属性。 其他属性可以在字典中捕获，然后在使用[`@attributes`](xref:mvc/views/razor#attributes) Razor 指令呈现组件时， *splatted*到元素上。 此方案在定义生成支持各种自定义项的标记元素的组件时非常有用。 例如，为支持多个参数的 `<input>` 分别定义属性可能比较繁琐。

在下面的示例中，第一个 `<input>` 元素（`id="useIndividualParams"`）使用单个组件参数，第二个 `<input>` 元素（`id="useAttributesDict"`）使用属性展开：

```razor
<input id="useIndividualParams"
       maxlength="@Maxlength"
       placeholder="@Placeholder"
       required="@Required"
       size="@Size" />

<input id="useAttributesDict"
       @attributes="InputAttributes" />

@code {
    [Parameter]
    public string Maxlength { get; set; } = "10";

    [Parameter]
    public string Placeholder { get; set; } = "Input placeholder text";

    [Parameter]
    public string Required { get; set; } = "required";

    [Parameter]
    public string Size { get; set; } = "50";

    [Parameter]
    public Dictionary<string, object> InputAttributes { get; set; } =
        new Dictionary<string, object>()
        {
            { "maxlength", "10" },
            { "placeholder", "Input placeholder text" },
            { "required", "required" },
            { "size", "50" }
        };
}
```

参数的类型必须实现具有字符串键 `IEnumerable<KeyValuePair<string, object>>`。 在这种情况下，也可以选择使用 `IReadOnlyDictionary<string, object>`。

使用这两种方法呈现的 `<input>` 元素相同：

```html
<input id="useIndividualParams"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">

<input id="useAttributesDict"
       maxlength="10"
       placeholder="Input placeholder text"
       required="required"
       size="50">
```

若要接受任意属性，请使用 `[Parameter]` 特性定义组件参数，并将 `CaptureUnmatchedValues` 属性设置为 `true`：

```razor
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`[Parameter]` 上的 `CaptureUnmatchedValues` 属性允许参数匹配所有不匹配任何其他参数的属性。 组件只能使用 `CaptureUnmatchedValues`定义单个参数。 与 `CaptureUnmatchedValues` 一起使用的属性类型必须从具有字符串键的 `Dictionary<string, object>` 中赋值。 此方案中也可以选择 `IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>`。

相对于元素特性位置 `@attributes` 的位置很重要。 当在元素上 splatted `@attributes` 时，将从右到左（从上到下）处理特性。 请考虑以下示例：使用 `Child` 组件的组件：

*ParentComponent*：

```razor
<ChildComponent extra="10" />
```

*ChildComponent*：

```razor
<div @attributes="AdditionalAttributes" extra="5" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Child` 组件的 `extra` 属性设置为 `@attributes`的右侧。 当通过附加属性传递时，`Parent` 组件的呈现 `<div>` 包含 `extra="5"`，因为属性从右到左处理（从上到第一）：

```html
<div extra="5" />
```

在下面的示例中，`Child` 组件的 `<div>`中反转 `extra` 和 `@attributes` 的顺序：

*ParentComponent*：

```razor
<ChildComponent extra="10" />
```

*ChildComponent*：

```razor
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Parent` 组件中呈现的 `<div>` 包含通过附加属性传递的 `extra="10"`：

```html
<div extra="10" />
```

## <a name="capture-references-to-components"></a>捕获对组件的引用

组件引用提供了一种方法来引用组件实例，以便可以向该实例发出命令，如 `Show` 或 `Reset`。 捕获组件引用：

* 向子组件添加[`@ref`](xref:mvc/views/razor#ref)特性。
* 定义与子组件类型相同的字段。

```razor
<MyLoginDialog @ref="_loginDialog" ... />

@code {
    private MyLoginDialog _loginDialog;

    private void OnSomething()
    {
        _loginDialog.Show();
    }
}
```

呈现组件时，将用 `MyLoginDialog` 子组件实例填充 `_loginDialog` 字段。 然后，可以在组件实例上调用 .NET 方法。

> [!IMPORTANT]
> 仅在呈现组件后填充 `_loginDialog` 变量，其输出包含 `MyLoginDialog` 元素。 在该点之前，没有任何内容可供参考。 若要在组件完成呈现后操作组件引用，请使用[OnAfterRenderAsync 或 OnAfterRender 方法](xref:blazor/lifecycle#after-component-render)。

捕获组件引用时，请使用类似的语法来[捕获元素引用](xref:blazor/javascript-interop#capture-references-to-elements)，而不是[JavaScript 互操作](xref:blazor/javascript-interop)功能。 不会将组件引用传递给 JavaScript 代码&mdash;它们只在 .NET 代码中使用。

> [!NOTE]
> 不要**使用组件**引用来改变子组件的状态。 请改用常规声明性参数将数据传递给子组件。 使用正常的声明性参数会导致子组件自动诸如此类正确的时间。

## <a name="invoke-component-methods-externally-to-update-state"></a>在外部调用组件方法以更新状态

Blazor 使用 `SynchronizationContext` 来强制执行单个逻辑线程。 在此 `SynchronizationContext`上执行由 Blazor 引发的组件[生命周期方法](xref:blazor/lifecycle)和任何事件回调。 如果组件必须根据外部事件（如计时器或其他通知）进行更新，请使用 `InvokeAsync` 方法，该方法将调度到 Blazor的 `SynchronizationContext`。

例如，假设有一个*通告程序服务*可以通知已更新状态的任何侦听组件：

```csharp
public class NotifierService
{
    // Can be called from anywhere
    public async Task Update(string key, int value)
    {
        if (Notify != null)
        {
            await Notify.Invoke(key, value);
        }
    }

    public event Func<string, int, Task> Notify;
}
```

将 `NotifierService` 注册为 singletion：

* 在 Blazor WebAssembly 中，在 `Program.Main`中注册服务：

  ```csharp
  builder.Services.AddSingleton<NotifierService>();
  ```

* 在 Blazor 服务器中，在 `Startup.ConfigureServices`中注册服务：

  ```csharp
  services.AddSingleton<NotifierService>();
  ```

使用 `NotifierService` 更新组件：

```razor
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @_lastNotification.key = @_lastNotification.value</p>

@code {
    private (string key, int value) _lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            _lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

在前面的示例中，`NotifierService` 在 Blazor的 `SynchronizationContext`之外调用组件的 `OnNotify` 方法。 `InvokeAsync` 用于切换到正确的上下文，并将呈现器排队。

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>使用 \@键控制是否保留元素和组件

在呈现元素或组件的列表并且元素或组件随后发生变化时，Blazor的比较算法必须决定哪些之前的元素或组件可以保留，以及模型对象应如何映射到它们。 通常，此过程是自动的，可以忽略，但在某些情况下，您可能需要控制该过程。

请考虑以下示例：

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People` 集合的内容可能会随插入、删除或重新排序条目而更改。 当组件 rerenders 时，`<DetailsEditor>` 组件可能会更改以接收不同 `Details` 参数值。 这可能导致比预期更复杂的 rerendering。 在某些情况下，rerendering 可能会导致可见行为差异，如失去元素焦点。

可以通过 `@key` 指令特性来控制映射过程。 `@key` 使比较算法根据键的值保证保留元素或组件：

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="person" Details="person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

当 `People` 集合发生更改时，比较算法会保留 `<DetailsEditor>` 实例与 `person` 实例之间的关联：

* 如果从 `People` 列表中删除 `Person`，则仅从 UI 中删除相应的 `<DetailsEditor>` 实例。 其他实例保持不变。
* 如果在列表中的某个位置插入 `Person`，则会在相应位置插入一个新的 `<DetailsEditor>` 实例。 其他实例保持不变。
* 如果重新排序 `Person` 项，则将保留相应的 `<DetailsEditor>` 实例，并在 UI 中重新排序。

在某些情况下，使用 `@key` 最大程度地降低了 rerendering 的复杂性，并避免了在 DOM 的有状态部分发生变化时出现潜在问题，如焦点位置。

> [!IMPORTANT]
> 键对于每个容器元素或组件都是本地的。 不会在整个文档中全局地比较键。

### <a name="when-to-use-key"></a>何时使用 \@密钥

通常，每当呈现列表时（例如，在 `@foreach` 块中）和适当的值（用于定义 `@key`），都有必要使用 `@key`。

还可以使用 `@key` 来防止 Blazor 在对象发生更改时保留元素或组件子树：

```razor
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

如果 `@currentPerson` 更改，则 `@key` attribute 指令强制 Blazor 丢弃整个 `<div>` 及其后代，并利用新的元素和组件重新生成 UI 中的子树。 如果需要确保在 `@currentPerson` 更改时不保留 UI 状态，这会很有用。

### <a name="when-not-to-use-key"></a>何时不使用 \@键

与 `@key`进行比较时，性能会降低。 性能开销不是很大，但只有在控制元素或组件保留规则对应用有利的情况下才指定 `@key`。

即使未使用 `@key`，Blazor 也会尽可能地保留子元素和组件实例。 使用 `@key` 的唯一优点是控制*如何*将模型实例映射到保留的组件实例，而不是选择映射的比较算法。

### <a name="what-values-to-use-for-key"></a>\@键使用哪些值

通常，为 `@key`提供以下类型之一：

* 模型对象实例（例如，在前面的示例中，`Person` 实例）。 这可确保基于对象引用相等性保存。
* 唯一标识符（例如，类型的主键值 `int`、`string`或 `Guid`）。

确保用于 `@key` 的值不冲突。 如果在同一父元素内检测到冲突值，Blazor 引发异常，因为它无法确定将旧元素或组件映射到新元素或组件。 仅使用非重复值，例如对象实例或主键值。

## <a name="partial-class-support"></a>分部类支持

Razor 组件以分部类的形式生成。 使用以下方法之一创作 Razor 组件：

* C#在一个文件中使用 HTML 标记和 Razor 代码在[`@code`](xref:mvc/views/razor#code)块中定义代码。 Blazor 模板使用此方法来定义其 Razor 组件。
* C#代码位于定义为分部类的代码隐藏文件中。

下面的示例演示了默认 `Counter` 组件，该组件在 Blazor 模板生成的应用程序中具有 `@code` 块。 HTML 标记、Razor 代码和C#代码位于同一个文件中：

*Counter*：

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    private int _currentCount = 0;

    void IncrementCount()
    {
        _currentCount++;
    }
}
```

还可以使用具有分部类的代码隐藏文件创建 `Counter` 组件：

*Counter*：

```razor
@page "/counter"

<h1>Counter</h1>

<p>Current count: @_currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs*：

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        private int _currentCount = 0;

        void IncrementCount()
        {
            _currentCount++;
        }
    }
}
```

根据需要将任何所需的命名空间添加到分部类文件中。 Razor 组件使用的典型命名空间包括：

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Components;
using Microsoft.AspNetCore.Components.Authorization;
using Microsoft.AspNetCore.Components.Forms;
using Microsoft.AspNetCore.Components.Routing;
using Microsoft.AspNetCore.Components.Web;
```

## <a name="specify-a-base-class"></a>指定基类

[`@inherits`](xref:mvc/views/razor#inherits)指令可用于指定组件的基类。 下面的示例演示组件如何继承基类（`BlazorRocksBase`）以提供组件的属性和方法。 基类应派生自 `ComponentBase`。

*Pages/BlazorRocks*：

```razor
@page "/BlazorRocks"
@inherits BlazorRocksBase

<h1>@BlazorRocksText</h1>
```

*BlazorRocksBase.cs*：

```csharp
using Microsoft.AspNetCore.Components;

namespace BlazorSample
{
    public class BlazorRocksBase : ComponentBase
    {
        public string BlazorRocksText { get; set; } = 
            "Blazor rocks the browser!";
    }
}
```

## <a name="specify-an-attribute"></a>指定属性

可在 Razor 组件中通过[`@attribute`](xref:mvc/views/razor#attribute)指令指定属性。 下面的示例将 `[Authorize]` 特性应用于组件类：

```razor
@page "/"
@attribute [Authorize]
```

## <a name="import-components"></a>导入组件

使用 Razor 编写的组件的命名空间基于（按优先级顺序）：

* Razor 文件（*razor*）标记中的[`@namespace`](xref:mvc/views/razor#namespace)指定（`@namespace BlazorSample.MyNamespace`）。
* 项目在项目文件中的 `RootNamespace` （`<RootNamespace>BlazorSample</RootNamespace>`）。
* 项目名称，从项目文件的文件名（ *.csproj*）获取，并从项目根路径到组件。 例如，框架将 *{PROJECT ROOT}/Pages/Index.razor* （*BlazorSample*）解析为命名空间 `BlazorSample.Pages`。 组件遵循C#名称绑定规则。 对于本示例中的 `Index` 组件，范围内的组件都是组件：
  * 在相同的文件夹中，*页*。
  * 项目的根中未显式指定其他命名空间的组件。

使用 Razor 的[`@using`](xref:mvc/views/razor#using)指令将不同命名空间中定义的组件引入作用域。

如果*BlazorSample/Shared/* 文件夹中存在另一个组件 `NavMenu.razor`，则可以使用以下 `@using` 语句在 `Index.razor` 中使用该组件：

```razor
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

还可以使用其完全限定名称（不需要[`@using`](xref:mvc/views/razor#using)指令）来引用组件：

```razor
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> 不支持 `global::` 限定。
>
> 不支持导入具有化名 `using` 语句的组件（例如，`@using Foo = Bar`）。
>
> 不支持部分限定的名称。 例如，不支持添加 `@using BlazorSample` 和引用 `<Shared.NavMenu></Shared.NavMenu>` `NavMenu.razor`。

## <a name="conditional-html-element-attributes"></a>条件 HTML 元素特性

HTML 元素特性根据 .NET 值有条件地呈现。 如果值是 `false` 或 `null`，则不会呈现特性。 如果 `true`值，则以最小化的特性呈现特性。

在下面的示例中，`IsCompleted` 确定是否在元素的标记中呈现 `checked`：

```razor
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

如果 `true``IsCompleted`，则复选框将呈现为：

```html
<input type="checkbox" checked />
```

如果 `false``IsCompleted`，则复选框将呈现为：

```html
<input type="checkbox" />
```

有关详细信息，请参阅 <xref:mvc/views/razor>。

> [!WARNING]
> 当 .NET 类型为 `bool`时，某些 HTML 特性（如[aria 按下](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)）不会正常运行。 在这些情况下，请使用 `string` 类型，而不是 `bool`。

## <a name="raw-html"></a>原始 HTML

通常使用 DOM 文本节点呈现字符串，这意味着将忽略它们可能包含的任何标记，并将其视为文本文本。 若要呈现原始 HTML，请将 HTML 内容换 `MarkupString` 值。 该值将分析为 HTML 或 SVG，并插入 DOM 中。

> [!WARNING]
> 呈现从任何不受信任的源构造的原始 HTML 会**带来安全风险**，应避免！

下面的示例演示如何使用 `MarkupString` 类型向组件的呈现输出添加静态 HTML 内容块：

```html
@((MarkupString)_myMarkup)

@code {
    private string _myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="cascading-values-and-parameters"></a>级联值和参数

在某些情况下，使用[组件参数](#component-parameters)将数据从祖先组件流式传输到附属组件是不方便的，尤其是在有多个组件层时。 级联值和参数通过提供一种方便的方法，使上级组件为其所有子代组件提供值。 级联值和参数还提供了一种方法来协调组件。

### <a name="theme-example"></a>主题示例

在下面的示例应用程序示例中，`ThemeInfo` 类指定用于向下传递组件层次结构的主题信息，以便应用程序中给定部分内的所有按钮共享相同样式。

*UIThemeClasses/ThemeInfo*：

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

祖先组件可以使用级联值组件提供级联值。 `CascadingValue` 组件包装组件层次结构的子树，并向该子树内的所有组件提供单个值。

例如，示例应用程序将应用程序布局之一中的主题信息（`ThemeInfo`）指定为组成 `@Body` 属性的布局正文的所有组件的级联参数。 在布局组件中为 `ButtonClass` 分配 `btn-success` 值。 任何子代组件都可以通过 `ThemeInfo` 级联对象使用此属性。

`CascadingValuesParametersLayout` 组件：

```razor
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="_theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo _theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

为了使用级联值，组件使用 `[CascadingParameter]` 属性声明级联参数。 级联值按类型绑定到级联参数。

在示例应用中，`CascadingValuesParametersTheme` 组件将 `ThemeInfo` 级联值绑定到级联参数。 参数用于设置由组件显示的一个按钮的 CSS 类。

`CascadingValuesParametersTheme` 组件：

```razor
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @_currentCount</p>

<p>
    <button class="btn" @onclick="IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" @onclick="IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@code {
    private int _currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        _currentCount++;
    }
}
```

若要在同一子树内级联多个相同类型的值，请为每个 `CascadingValue` 组件及其相应的 `CascadingParameter`提供唯一的 `Name` 字符串。 在下面的示例中，两个 `CascadingValue` 组件按名称级联不同实例 `MyCascadingType`：

```razor
<CascadingValue Value=@_parentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType _parentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

在子代组件中，级联参数按名称从上级组件中相应的级联值接收值：

```razor
...

@code {
    [CascadingParameter(Name = "CascadeParam1")]
    protected MyCascadingType ChildCascadeParameter1 { get; set; }
    
    [CascadingParameter(Name = "CascadeParam2")]
    protected MyCascadingType ChildCascadeParameter2 { get; set; }
}
```

### <a name="tabset-example"></a>TabSet 示例

级联参数还允许组件跨组件层次结构进行协作。 例如，请看下面的示例应用中的*TabSet*示例。

该示例应用包含一个选项卡实现的 `ITab` 接口：

[!code-csharp[](common/samples/3.x/BlazorWebAssemblySample/UIInterfaces/ITab.cs)]

`CascadingValuesParametersTabSet` 组件使用 `TabSet` 组件，该组件包含多个 `Tab` 组件：

```razor
<TabSet>
    <Tab Title="First tab">
        <h4>Greetings from the first tab!</h4>

        <label>
            <input type="checkbox" @bind="showThirdTab" />
            Toggle third tab
        </label>
    </Tab>
    <Tab Title="Second tab">
        <h4>The second tab says Hello World!</h4>
    </Tab>

    @if (showThirdTab)
    {
        <Tab Title="Third tab">
            <h4>Welcome to the disappearing third tab!</h4>
            <p>Toggle this tab from the first tab.</p>
        </Tab>
    }
</TabSet>
```

子 `Tab` 组件不会显式作为参数传递给 `TabSet`。 子 `Tab` 组件是 `TabSet`的子内容的一部分。 但 `TabSet` 仍需要了解每个 `Tab` 组件，以便它可以呈现标头和活动选项卡。若要启用此协调而不需要额外的代码，`TabSet` 组件*可将自身作为级联值提供*，然后由子代 `Tab` 组件选取。

`TabSet` 组件：

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

子代 `Tab` 组件将包含 `TabSet` 作为级联参数捕获，因此 `Tab` 组件会将其自身添加到 `TabSet` 并协调哪个选项卡处于活动状态。

`Tab` 组件：

[!code-razor[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor 模板

可以使用 Razor 模板语法来定义呈现片段。 Razor 模板是一种定义 UI 代码段并采用以下格式的一种方法：

```razor
@<{HTML tag}>...</{HTML tag}>
```

下面的示例演示如何在组件中直接指定 `RenderFragment` 和 `RenderFragment<T>` 值以及呈现模板。 呈现片段还可以作为参数传递给[模板化组件](xref:blazor/templated-components)。

```razor
@_timeTemplate

@_petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment _timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> _petTemplate = (pet) => @<p>Pet: @pet.Name</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

前面的代码的呈现输出：

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Pet: Rex</p>
```

## <a name="scalable-vector-graphics-svg-images"></a>可缩放的向量图形（SVG）图像

由于 Blazor 呈现 HTML，因此支持浏览器支持的图像（包括可缩放的向量图形（SVG）图像（*svg*））是通过 `<img>` 标记支持的：

```html
<img alt="Example image" src="some-image.svg" />
```

同样，样式表文件（ *.css*）的 CSS 规则支持 SVG 图像：

```css
.my-element {
    background-image: url("some-image.svg");
}
```

但是，在所有情况下均不支持内联 SVG 标记。 如果将 `<svg>` 标记直接放入组件文件（*razor*），则支持基本图像呈现，但尚不支持很多高级方案。 例如，`<use>` 标记当前未遵循，并且不能将 `@bind` 与某些 SVG 标记一起使用。 我们预计会在将来的版本中解决这些限制。

## <a name="additional-resources"></a>其他资源

* <xref:security/blazor/server> &ndash; 包括构建必须应对资源耗尽的 Blazor 服务器应用的指南。
