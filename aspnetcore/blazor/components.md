---
title: 创建和使用 ASP.NET Core Razor 组件
author: guardrex
description: 了解如何创建和使用 Razor 组件，包括如何绑定到数据、处理事件和管理组件生命周期。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/21/2019
uid: blazor/components
ms.openlocfilehash: 8c228b168cdbd58928ef3f57ff26bc86e8dfc1ba
ms.sourcegitcommit: 16cf016035f0c9acf3ff0ad874c56f82e013d415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/29/2019
ms.locfileid: "73033979"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>创建和使用 ASP.NET Core Razor 组件

作者： [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

Blazor 应用是使用*组件*生成的。 组件是自包含的用户界面（UI）块，如页、对话框或窗体。 组件包含插入数据或响应 UI 事件所需的 HTML 标记和处理逻辑。 组件非常灵活且轻型。 它们可以嵌套、重复使用和在项目之间共享。

## <a name="component-classes"></a>组件类

在[razor](xref:mvc/views/razor)组件文件（*razor*）中，使用和 HTML 标记的C#组合实现了组件。 Blazor 中的组件正式称为*Razor 组件*。

组件的名称必须以大写字符开头。 例如， *MyCoolComponent*是有效的，并且*myCoolComponent*无效。

使用 HTML 定义组件的 UI。 动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。 在编译应用程序时，会将 HTML 标记C#和呈现逻辑转换为组件类。 生成的类的名称与文件的名称匹配。

组件类的成员在 `@code` 块中定义。 在 `@code` 块中，组件状态（属性、字段）是通过事件处理方法或定义其他组件逻辑来指定的。 允许多个 `@code` 块。

> [!NOTE]
> 在 ASP.NET Core 3.0 的先前预览中，`@functions` 块用于与 Razor 组件中 `@code` 块相同的目的。 `@functions` 块继续在 Razor 组件中运行，但我们建议在 ASP.NET Core 3.0 Preview 6 或更高版本中使用 `@code` 块。

可以使用C#以 `@` 开头的表达式将组件成员作为组件的呈现逻辑的一部分。 例如， C#字段通过在字段名称 `@` 之前进行呈现。 下面的示例计算并呈现：

* `_headingFontStyle` `font-style`的 CSS 属性值。
* `_headingText` 到 `<h1>` 元素的内容。

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

最初呈现组件后，组件会为响应事件而重新生成其呈现树。 然后，Blazor 将新的呈现树与上一个树进行比较，并将所有修改应用于浏览器的文档对象模型（DOM）。

组件是普通C#类，可以放置在项目中的任何位置。 生成网页的组件通常位于*Pages*文件夹中。 非页组件通常放置在*共享*文件夹中或添加到项目的自定义文件夹中。 若要使用自定义文件夹，请将自定义文件夹的命名空间添加到父组件或应用程序的 *_Imports*文件。 例如，以下命名空间使*组件*文件夹中的组件在应用程序的根命名空间 `WebApplication` 时可用：

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>将组件集成到 Razor Pages 和 MVC 应用

将组件用于现有 Razor Pages 和 MVC 应用。 无需重写现有页面或视图即可使用 Razor 组件。 呈现页面或视图时，组件将同时预呈现。

若要从页面或视图呈现组件，请使用 `RenderComponentAsync<TComponent>` HTML 帮助器方法：

```cshtml
<div id="MyComponent">
    @(await Html.RenderComponentAsync<MyComponent>(RenderMode.ServerPrerendered))
</div>
```

尽管页面和视图可以使用组件，但不是这样。 组件不能使用视图和特定于页的方案，如分部视图和节。 若要在组件中通过分部视图使用逻辑，请将分部视图逻辑分解为一个组件。

有关如何呈现组件和在 Blazor Server 应用程序中管理组件状态的详细信息，请参阅 <xref:blazor/hosting-models> 文章。

## <a name="use-components"></a>使用组件

组件可以通过使用 HTML 元素语法声明组件来包含其他组件。 使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。

属性绑定区分大小写。 例如，`@bind` 有效，`@Bind` 无效。

*Index*中的以下标记会呈现 `HeadingComponent` 实例：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/Index.razor?name=snippet_HeadingComponent)]

*组件/HeadingComponent*：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/HeadingComponent.razor)]

如果某个组件包含一个 HTML 元素，该元素的首字母大写字母与组件名称不匹配，则会发出警告，指示该元素具有意外的名称。 为组件命名空间添加 `@using` 语句会使组件可用，从而消除了警告。

## <a name="component-parameters"></a>组件参数

组件可以具有*组件参数*，这些参数是使用组件类上的公共属性和 `[Parameter]` 特性定义的。 使用这些属性在标记中为组件指定参数。

*组件/ChildComponent*：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=11-12)]

在下面的示例中，`ParentComponent` 设置 `ChildComponent` 的 `Title` 属性的值。

*Pages/ParentComponent*：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a>子内容

组件可以设置另一个组件的内容。 分配组件提供用于指定接收组件的标记之间的内容。

在下面的示例中，`ChildComponent` 具有一个表示 `RenderFragment` 的 `ChildContent` 属性，该属性表示要呈现的 UI 段。 `ChildContent` 的值放置在应呈现内容的组件标记中。 `ChildContent` 的值从父组件接收，并呈现在启动面板的 `panel-body`中。

*组件/ChildComponent*：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> 接收 `RenderFragment` 内容的属性必须按约定命名为 `ChildContent`。

以下 `ParentComponent` 可以通过将内容置于 `<ChildComponent>` 标记中来提供用于呈现 `ChildComponent` 的内容。

*Pages/ParentComponent*：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Attribute 展开和任意参数

除了组件的声明参数外，组件还可以捕获和呈现附加属性。 其他属性可以在字典中捕获，然后在使用[@attributes](xref:mvc/views/razor#attributes) Razor 指令呈现组件时， *splatted*到元素上。 此方案在定义生成支持各种自定义项的标记元素的组件时非常有用。 例如，为支持多个参数的 `<input>` 分别定义属性可能会很繁琐。

在下面的示例中，第一个 `<input>` 元素（`id="useIndividualParams"`）使用单个组件参数，第二个 `<input>` 元素（`id="useAttributesDict"`）使用属性展开：

```cshtml
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

参数的类型必须实现 `IEnumerable<KeyValuePair<string, object>>` 替换字符串键。 在此方案中，还可以选择使用 `IReadOnlyDictionary<string, object>`。

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

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`[Parameter]` 上的 `CaptureUnmatchedValues` 属性允许参数匹配所有不匹配任何其他参数的属性。 组件只能使用 `CaptureUnmatchedValues` 定义单个参数。 与 `CaptureUnmatchedValues` 一起使用的属性类型必须从具有字符串键的 `Dictionary<string, object>` 中赋值。 此方案中也可以选择 `IEnumerable<KeyValuePair<string, object>>` 或 `IReadOnlyDictionary<string, object>`。

相对于元素特性位置 `@attributes` 的位置很重要。 当在元素上 splatted `@attributes` 时，将从右到左（从上到下）处理特性。 请考虑以下示例：使用 `Child` 组件的组件：

*ParentComponent*：

```cshtml
<ChildComponent extra="10" />
```

*ChildComponent*：

```cshtml
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

```cshtml
<ChildComponent extra="10" />
```

*ChildComponent*：

```cshtml
<div extra="5" @attributes="AdditionalAttributes" />

[Parameter(CaptureUnmatchedValues = true)]
public IDictionary<string, object> AdditionalAttributes { get; set; }
```

`Parent` 组件中呈现的 `<div>` 包含通过附加属性传递的 `extra="10"`：

```html
<div extra="10" />
```

## <a name="data-binding"></a>数据绑定

对组件和 DOM 元素的数据绑定都是通过[@bind](xref:mvc/views/razor#bind)属性来完成的。 下面的示例将 `CurrentValue` 属性绑定到文本框的值：

```cshtml
<input @bind="CurrentValue" />

@code {
    private string CurrentValue { get; set; }
}
```

当文本框失去焦点时，会更新该属性的值。

仅当呈现组件时，才会在 UI 中更新文本框，而不是响应更改属性值。 由于在事件处理程序代码执行后组件会自行呈现，因此在事件处理程序触发后，属性更新*通常*会立即反映在 UI 中。

结合使用 `@bind` 与 `CurrentValue` 属性（`<input @bind="CurrentValue" />`）本质上等效于以下内容：

```cshtml
<input value="@CurrentValue"
    @onchange="@((ChangeEventArgs __e) => CurrentValue = 
        __e.Value.ToString())" />
        
@code {
    private string CurrentValue { get; set; }
}
```

呈现组件时，输入元素的 `value` 来自 `CurrentValue` 属性。 当用户在文本框中键入内容并更改元素焦点时，将激发 `onchange` 事件并将 `CurrentValue` 属性设置为更改的值。 事实上，代码生成更复杂，因为 `@bind` 处理执行类型转换的情况。 原则上，`@bind` 将表达式的当前值与 `value` 属性相关联，并使用注册的处理程序来处理更改。

除了使用 `@bind` 语法处理 `onchange` 事件外，还可以使用其他事件来绑定属性或字段，方法是使用 `event` 参数指定[@bind-value](xref:mvc/views/razor#bind)属性（[@bind-value:event](xref:mvc/views/razor#bind)）。 下面的示例绑定了 `oninput` 事件的 `CurrentValue` 属性：

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />

@code {
    private string CurrentValue { get; set; }
}
```

与 `onchange` 不同，后者在元素失去焦点时激发，`oninput` 在文本框的值更改时激发。

**不可分析值**

当用户向数据绑定元素提供无法分析的值时，触发绑定事件时，无法分析的值将自动恢复为其以前的值。

请参考以下方案：

* `<input>` 元素绑定到 `int` 类型，其初始值为 `123`：

  ```cshtml
  <input @bind="MyProperty" />

  @code {
      [Parameter]
      public int MyProperty { get; set; } = 123;
  }
  ```
* 用户将元素的值更新为该页 `123.45`，并更改元素焦点。

在上述方案中，将元素的值还原为 `123`。 如果拒绝值 `123.45` 以便于 `123` 的原始值，则用户知道其值不被接受。

默认情况下，绑定适用于元素的 `onchange` 事件（`@bind="{PROPERTY OR FIELD}"`）。 使用 `@bind-value="{PROPERTY OR FIELD}" @bind-value:event={EVENT}` 设置不同的事件。 对于 `oninput` 事件（`@bind-value:event="oninput"`），重新确定会在引入了可分析值的任何击键之后发生。 当以 `int` 绑定类型为 `oninput` 事件定位时，将阻止用户键入 `.` 字符。 会立即删除 `.` 字符，因此用户会立即收到仅允许整数的反馈。 在某些情况下，在 `oninput` 事件上恢复该值的情况并不理想，例如当用户应允许清除无法解析的 `<input>` 值时。 替代项包括：

* 不要使用 `oninput` 事件。 使用默认 `onchange` 事件（`@bind="{PROPERTY OR FIELD}"`），其中无效值直到元素失去焦点时才会被还原。
* 绑定到可为 null 的类型，例如 `int?` 或 `string`，并提供自定义逻辑来处理无效条目。
* 使用[窗体验证组件](xref:blazor/forms-validation)，如 `InputNumber` 或 `InputDate`。 窗体验证组件具有用于管理无效输入的内置支持。 窗体验证组件：
  * 允许用户提供无效输入，并在关联的 `EditContext` 上收到验证错误。
  * 在 UI 中显示验证错误，不干扰用户输入其他 webform 数据。

**全球化**

`@bind` 值的格式设置为显示，并使用当前区域性的规则进行分析。

可以从 <xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName> 属性访问当前区域性。

[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)用于以下字段类型（`<input type="{TYPE}" />`）：

* `date`
* `number`

上述字段类型：

* 使用其基于浏览器的适当格式规则显示。
* 不能包含自由格式的文本。
* 基于浏览器的实现提供用户交互特性。

以下字段类型具有特定的格式要求，Blazor 当前不支持它们，因为它们不受所有主要浏览器的支持：

* `datetime-local`
* `month`
* `week`

`@bind` 支持 `@bind:culture` 参数，以提供 <xref:System.Globalization.CultureInfo?displayProperty=fullName> 用于分析和设置值的格式。 使用 `date` 和 `number` 字段类型时，不建议指定区域性。 `date` 和 `number` 具有提供所需区域性的内置 Blazor 支持。

有关如何设置用户的区域性的信息，请参阅[本地化](#localization)部分。

**格式字符串**

数据绑定使用[@bind:format](xref:mvc/views/razor#bind)<xref:System.DateTime> 格式字符串。 现在不能使用其他格式的表达式，如货币或数字格式。

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

在前面的代码中，`<input>` 元素的字段类型（`type`）默认为 `text`。 支持 `@bind:format` 绑定以下 .NET 类型：

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

`@bind:format` 特性指定要应用于 `<input>` 元素的 `value` 的日期格式。 此格式还用于分析 `onchange` 事件发生时的值。

不建议为 `date` 字段类型指定格式，因为 Blazor 具有设置日期格式的内置支持。

**组件参数**

绑定可识别组件参数，其中 `@bind-{property}` 可跨组件绑定属性值。

以下子组件（`ChildComponent`）具有 `Year` 组件参数和 `YearChanged` 回调：

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@code {
    [Parameter]
    public int Year { get; set; }

    [Parameter]
    public EventCallback<int> YearChanged { get; set; }
}
```

[EventCallback](#eventcallback)部分说明了 `EventCallback<T>`。

以下父组件使用 `ChildComponent`，并将 `ParentYear` 参数从父级绑定到子组件上的 `Year` 参数：

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent @bind-Year="ParentYear" />

<button class="btn btn-primary" @onclick="ChangeTheYear">
    Change Year to 1986
</button>

@code {
    [Parameter]
    public int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

加载 `ParentComponent` 会生成以下标记：

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

如果通过在 `ParentComponent` 中选择按钮更改了 `ParentYear` 属性的值，则将更新 `ChildComponent` 的 `Year` 属性。 当 `ParentComponent` 重新呈现时，用户界面中将 `Year` 的新值：

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year` 参数是可绑定的，因为它具有与 `Year` 参数类型相匹配的伴随 `YearChanged` 事件。

按照约定，`<ChildComponent @bind-Year="ParentYear" />` 实质上等同于编写：

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

通常，可以使用 `@bind-property:event` 特性将属性绑定到相应的事件处理程序。 例如，可以使用以下两个属性将属性 `MyProp` 绑定到 `MyEventHandler`：

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>事件处理

Razor 组件提供事件处理功能。 对于名为 `on{event}` 的 HTML 元素特性（例如，`onclick` 和 `onsubmit`）和委托类型的值，Razor 组件将属性的值视为事件处理程序。 特性的名称始终[@on {event}](xref:mvc/views/razor#onevent)格式。

下面的代码在用户界面中选择按钮时调用 `UpdateHeading` 方法：

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

以下代码会在 UI 中的复选框发生更改时调用 `CheckChanged` 方法：

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

事件处理程序也可以是异步的，并返回 <xref:System.Threading.Tasks.Task>。 无需手动调用 `StateHasChanged()`。 异常在发生时进行记录。

在下面的示例中，当选择按钮时，将异步调用 `UpdateHeading`：

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(MouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a>事件参数类型

对于某些事件，允许使用事件参数类型。 如果不需要访问这些事件类型之一，则方法调用中不需要这样做。

下表显示了支持的 `EventArgs`。

| Event — 事件 | 实例 |
| ----- | ----- |
| 剪贴板        | `ClipboardEventArgs` |
| 入             | `DragEventArgs` &ndash; `DataTransfer` 和 `DataTransferItem` 保存拖动的项数据。 |
| Error            | `ErrorEventArgs` |
| 焦点            | `FocusEventArgs` &ndash; 不包括对 `relatedTarget` 的支持。 |
| `<input>` 已更改 | `ChangeEventArgs` |
| 键盘         | `KeyboardEventArgs` |
| 鼠标            | `MouseEventArgs` |
| 鼠标指针    | `PointerEventArgs` |
| 鼠标滚轮      | `WheelEventArgs` |
| 进度         | `ProgressEventArgs` |
| 触控            | `TouchEventArgs` &ndash; `TouchPoint` 表示触摸敏感设备上的单个联系点。 |

有关上表中事件的属性和事件处理行为的信息，请参阅[引用源中的 EventArgs 类（aspnet/AspNetCore release/3.0 分支）](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Components/Web/src/Web)。

### <a name="lambda-expressions"></a>Lambda 表达式

还可以使用 Lambda 表达式：

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

通常可以很方便地关闭其他值，如在循环访问一组元素时。 下面的示例创建了三个按钮，其中每个按钮都调用 `UpdateHeading` 在 UI 中选择时传递事件参数（`MouseEventArgs`）和其按钮号（`buttonNumber`）：

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            @onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@code {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(MouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> 不要直接在 lambda 表达式**中使用 `for`** 循环中的循环变量（`i`）。 否则，所有 lambda 表达式都将使用相同的变量，导致 `i` 的值在所有 lambda 中都相同。 始终捕获其在本地变量中的值（在前面的示例中 `buttonNumber`），然后使用它。

### <a name="eventcallback"></a>EventCallback

使用嵌套组件的常见方案是，需要在子组件事件发生时运行父组件的方法 &mdash;for 例如，当子组件中发生 `onclick` 事件时。 若要跨组件公开事件，请使用 `EventCallback`。 父组件可将回调方法分配给子组件的 `EventCallback`。

示例应用中的 `ChildComponent` 演示如何设置按钮的 `onclick` 处理程序，以便从示例的 `ParentComponent` 接收 `EventCallback` 委托。 `EventCallback` 是使用 `MouseEventArgs`键入的，这适用于来自外围设备的 `onclick` 事件：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ChildComponent.razor?highlight=5-7,17-18)]

`ParentComponent` 将子级的 `EventCallback<T>` 设置为其 `ShowMessage` 方法：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

在 `ChildComponent` 中选择该按钮时：

* 调用 `ParentComponent` 的 `ShowMessage` 方法。 `messageText` 会更新并显示在 `ParentComponent` 中。
* 回调的方法（`ShowMessage`）中不需要调用 `StateHasChanged`。 `StateHasChanged` 会自动调用以诸如此类 `ParentComponent`，就像子事件在子中执行的事件处理程序中 rerendering。

`EventCallback` 和 `EventCallback<T>` 允许异步委托。 `EventCallback<T>` 为强类型，并且需要特定的参数类型。 `EventCallback` 弱类型化，并允许任何参数类型。

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

使用 `InvokeAsync` 调用 `EventCallback` 或 `EventCallback<T>`，并等待 <xref:System.Threading.Tasks.Task>：

```csharp
await callback.InvokeAsync(arg);
```

使用 `EventCallback` 并 `EventCallback<T>` 进行事件处理和绑定组件参数。

优先于 `EventCallback` 的强类型 `EventCallback<T>`。 `EventCallback<T>` 向组件用户提供更好的错误反馈。 与其他 UI 事件处理程序类似，指定事件参数是可选的。 当没有任何值传递到回调时，使用 `EventCallback`。

## <a name="chained-bind"></a>链式绑定

常见的情况是将数据绑定参数链接到组件输出中的页元素。 此方案称为*链接绑定*，因为多个级别的绑定同时发生。

无法使用页面元素中的 `@bind` 语法实现链式绑定。 必须单独指定事件处理程序和值。 但是，父组件可以将 `@bind` 语法与组件的参数一起使用。

以下 `PasswordField` 组件（*self.passwordfield.text*）：

* 将 `<input>` 元素的值设置为 `Password` 属性。
* 使用[EventCallback](#eventcallback)向父组件公开 `Password` 属性的更改。

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

@code {
    private bool showPassword;

    [Parameter]
    public string Password { get; set; }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

`PasswordField` 组件用于另一个组件：

```cshtml
<PasswordField @bind-Password="password" />

@code {
    private string password;
}
```

对上述示例中的密码执行检查或陷阱错误：

* 为 `Password` 创建支持字段（在下面的示例代码中 `password`）。
* 执行 `Password` setter 中的检查或陷阱错误。

如果密码的值中使用了空格，则以下示例向用户提供即时反馈：

```cshtml
Password: 

<input @oninput="OnPasswordChanged" 
       required 
       type="@(showPassword ? "text" : "password")" 
       value="@Password" />

<button class="btn btn-primary" @onclick="ToggleShowPassword">
    Show password
</button>

<span class="text-danger">@validationMessage</span>

@code {
    private bool showPassword;
    private string password;
    private string validationMessage;

    [Parameter]
    public string Password
    {
        get { return password ?? string.Empty; }
        set
        {
            if (password != value)
            {
                if (value.Contains(' '))
                {
                    validationMessage = "Spaces not allowed!";
                }
                else
                {
                    password = value;
                    validationMessage = string.Empty;
                }
            }
        }
    }

    [Parameter]
    public EventCallback<string> PasswordChanged { get; set; }

    private Task OnPasswordChanged(ChangeEventArgs e)
    {
        Password = e.Value.ToString();

        return PasswordChanged.InvokeAsync(Password);
    }

    private void ToggleShowPassword()
    {
        showPassword = !showPassword;
    }
}
```

## <a name="capture-references-to-components"></a>捕获对组件的引用

组件引用提供了一种方法来引用组件实例，以便可以向该实例发出命令，如 `Show` 或 `Reset`。 捕获组件引用：

* 向子组件添加[@ref](xref:mvc/views/razor#ref)属性。
* 定义与子组件类型相同的字段。

```cshtml
<MyLoginDialog @ref="loginDialog" ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

呈现组件时，将用 `MyLoginDialog` 子组件实例填充 `loginDialog` 字段。 然后，可以在组件实例上调用 .NET 方法。

> [!IMPORTANT]
> 仅在呈现组件后填充 `loginDialog` 变量，其输出包含 `MyLoginDialog` 元素。 在该点之前，没有任何内容可供参考。 若要在组件完成呈现后操作组件引用，请使用[OnAfterRenderAsync 或 OnAfterRender 方法](#lifecycle-methods)。

捕获组件引用时，请使用类似的语法来[捕获元素引用](xref:blazor/javascript-interop#capture-references-to-elements)，而不是[JavaScript 互操作](xref:blazor/javascript-interop)功能。 不会将组件引用传递给 JavaScript 代码 &mdash;they 仅用于 .NET 代码。

> [!NOTE]
> 不要**使用组件**引用来改变子组件的状态。 请改用常规声明性参数将数据传递给子组件。 使用正常的声明性参数会导致子组件自动诸如此类正确的时间。

## <a name="invoke-component-methods-externally-to-update-state"></a>在外部调用组件方法以更新状态

Blazor 使用 `SynchronizationContext` 来强制执行单个逻辑线程。 此 `SynchronizationContext` 上将执行 Blazor 引发的组件生命周期方法和任何事件回调。 如果必须根据外部事件（如计时器或其他通知）更新组件，请使用 `InvokeAsync` 方法，该方法将调度到 Blazor 的 `SynchronizationContext`。

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

使用 `NotifierService` 更新组件：

```cshtml
@page "/"
@inject NotifierService Notifier
@implements IDisposable

<p>Last update: @lastNotification.key = @lastNotification.value</p>

@code {
    private (string key, int value) lastNotification;

    protected override void OnInitialized()
    {
        Notifier.Notify += OnNotify;
    }

    public async Task OnNotify(string key, int value)
    {
        await InvokeAsync(() =>
        {
            lastNotification = (key, value);
            StateHasChanged();
        });
    }

    public void Dispose()
    {
        Notifier.Notify -= OnNotify;
    }
}
```

在前面的示例中，`NotifierService` 在 Blazor 的 `SynchronizationContext` 之外调用组件的 `OnNotify` 方法。 `InvokeAsync` 用于切换到正确的上下文，并将呈现器排队。

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>使用 \@key 来控制是否保留元素和组件

在呈现元素或组件的列表并且元素或组件随后发生变化时，Blazor 的比较算法必须决定哪些之前的元素或组件可以保留，以及模型对象应如何映射到它们。 通常，此过程是自动的，可以忽略，但在某些情况下，您可能需要控制该过程。

请看下面的示例：

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

可以用 `@key` 指令特性来控制映射过程。 `@key` 会使比较算法根据键的值保证保留元素或组件：

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

在某些情况下，使用 `@key` 可最大程度地降低 rerendering 的复杂性，并避免在 DOM 的有状态部分发生变化时出现潜在问题，如焦点位置。

> [!IMPORTANT]
> 键对于每个容器元素或组件都是本地的。 不会在整个文档中全局地比较键。

### <a name="when-to-use-key"></a>何时使用 \@key

通常，每当呈现列表时（例如，在 `@foreach` 块中）和适当的值（用于定义 `@key`），都有必要使用 `@key`。

当对象发生更改时，还可以使用 `@key` 来防止 Blazor 保留元素或组件子树：

```cshtml
<div @key="currentPerson">
    ... content that depends on currentPerson ...
</div>
```

如果 `@currentPerson` 更改，则 `@key` attribute 指令强制 Blazor 丢弃整个 `<div>` 及其后代，并将 UI 中的子树与新元素和组件重新生成。 如果需要确保在 `@currentPerson` 更改时不保留 UI 状态，这会很有用。

### <a name="when-not-to-use-key"></a>何时不使用 \@key

与 `@key` 进行比较时，性能会降低。 性能开销不大，但只有在控制元素或组件保留规则对应用有利的情况下才指定 `@key`。

即使不使用 `@key`，Blazor 也会尽可能地保留子元素和组件实例。 使用 `@key` 的唯一优点是控制*如何*将模型实例映射到保留的组件实例，而不是选择映射的比较算法。

### <a name="what-values-to-use-for-key"></a>要用于 \@key 的值

通常，为 `@key` 提供以下类型之一：

* 模型对象实例（例如，在前面的示例中为 `Person` 实例）。 这可确保基于对象引用相等性保存。
* 唯一标识符（例如，类型的主键值 `int`、`string` 或 `Guid`）。

确保用于 `@key` 的值不冲突。 如果在同一父元素内检测到冲突值，则 Blazor 会引发异常，因为它无法确定将旧元素或组件映射到新元素或组件。 仅使用非重复值，例如对象实例或主键值。

## <a name="lifecycle-methods"></a>生命周期方法

`OnInitializedAsync` 和 `OnInitialized` 执行代码来初始化组件。 若要执行异步操作，请使用 `OnInitializedAsync` 和操作的 `await` 关键字：

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

> [!NOTE]
> 在 `OnInitializedAsync` 生命周期事件期间，必须在组件初始化期间进行异步工作。

对于同步操作，请使用 `OnInitialized`：

```csharp
protected override void OnInitialized()
{
    ...
}
```

当组件已从其父级接收参数并且为属性分配了值时，将调用 `OnParametersSetAsync` 和 `OnParametersSet`。 这些方法在组件初始化之后以及每次呈现组件时执行：

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

> [!NOTE]
> 应用参数和属性值时的异步工作必须在 `OnParametersSetAsync` 生命周期事件期间发生。

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync` 和 `OnAfterRender` 在组件完成呈现后调用。 此时将填充元素和组件引用。 使用此阶段，可以使用呈现的内容来执行其他初始化步骤，如激活在呈现的 DOM 元素上操作的第三方 JavaScript 库。

在*服务器上预呈现时不会调用*`OnAfterRender`。

`OnAfterRenderAsync` 的 `firstRender` 参数和 `OnAfterRender` 为：

* 第一次调用组件实例时，设置为 `true`。
* 确保仅执行一次初始化工作。

```csharp
protected override async Task OnAfterRenderAsync(bool firstRender)
{
    if (firstRender)
    {
        await ...
    }
}
```

> [!NOTE]
> 呈现后，应立即在 `OnAfterRenderAsync` 生命周期事件期间进行异步工作。

```csharp
protected override void OnAfterRender(bool firstRender)
{
    if (firstRender)
    {
        ...
    }
}
```

### <a name="handle-incomplete-async-actions-at-render"></a>在呈现时处理未完成的异步操作

在呈现组件之前，在生命周期事件中执行的异步操作可能尚未完成。 在执行生命周期方法时，对象可能 `null` 或未完全填充数据。 提供呈现逻辑以确认对象已初始化。 `null`对象时呈现占位符 UI 元素（例如，加载消息）。

在 Blazor 模板的 `FetchData` 组件中，`OnInitializedAsync` 被重写为 asychronously 接收预测数据（`forecasts`）。 `null``forecasts` 时，将向用户显示一条加载消息。 `OnInitializedAsync` 的 `Task` 返回完成后，组件将重新呈现已更新状态。

*Pages/FetchData.razor*：

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a>在设置参数之前执行代码

在设置参数之前，可以重写 `SetParameters` 以执行代码：

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

如果未调用 `base.SetParameters`，则自定义代码可以通过任何所需的方式解释传入参数值。 例如，无需将传入参数分配给类的属性。

### <a name="suppress-refreshing-of-the-ui"></a>禁止刷新 UI

可以重写 `ShouldRender` 以禁止刷新 UI。 如果实现返回 `true`，则刷新 UI。 即使重写 `ShouldRender`，组件也始终最初呈现。

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>用 IDisposable 进行的组件处置

如果某个组件实现 <xref:System.IDisposable>，则当从 UI 中删除该组件时，将调用[Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。 以下组件使用 `@implements IDisposable` 和 `Dispose` 方法：

```csharp
@using System
@implements IDisposable

...

@code {
    public void Dispose()
    {
        ...
    }
}
```

> [!NOTE]
> 不支持在 `Dispose` 中调用 `StateHasChanged`。 `StateHasChanged` 可能会在要关闭的呈现器中被调用。 不支持在该点请求 UI 更新。

## <a name="routing"></a>路由

通过向应用程序中的每个可访问组件提供路由模板，实现 Blazor 中的路由。

编译具有 `@page` 指令的 Razor 文件时，将为生成的类指定 <xref:Microsoft.AspNetCore.Mvc.RouteAttribute> 指定路由模板的。 在运行时，路由器将使用 `RouteAttribute` 查找组件类，并呈现哪个组件包含与请求的 URL 匹配的路由模板。

可以将多个路由模板应用于组件。 以下组件响应 `/BlazorRoute` 和 `/DifferentBlazorRoute` 的请求：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>路由参数

组件可以从 `@page` 指令提供的路由模板接收路由参数。 路由器使用路由参数来填充相应的组件参数。

*路由参数组件*：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

不支持可选参数，因此在上面的示例中应用了两个 `@page` 指令。 第一个允许导航到没有参数的组件。 第二个 `@page` 指令采用 `{text}` 路由参数，并将该值分配给 `Text` 属性。

::: moniker range=">= aspnetcore-3.1"

## <a name="partial-class-support"></a>分部类支持

Razor 组件以分部类的形式生成。 使用以下方法之一创作 Razor 组件：

* C#在一个文件中使用 HTML 标记和 Razor 代码在[@code](xref:mvc/views/razor#code)块中定义代码。 Blazor 模板使用此方法来定义其 Razor 组件。
* C#代码位于定义为分部类的代码隐藏文件中。

下面的示例演示了在 Blazor 模板生成的应用中具有 `@code` 块的默认 `Counter` 组件。 HTML 标记、Razor 代码和C#代码位于同一个文件中：

*Counter*：

```cshtml
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>

@code {
    int currentCount = 0;

    void IncrementCount()
    {
        currentCount++;
    }
}
```

还可以使用具有分部类的代码隐藏文件创建 `Counter` 组件：

*Counter*：

```cshtml
@page "/counter"

<h1>Counter</h1>

<p>Current count: @currentCount</p>

<button class="btn btn-primary" @onclick="IncrementCount">Click me</button>
```

*Counter.razor.cs*：

```csharp
namespace BlazorApp.Pages
{
    public partial class Counter
    {
        int currentCount = 0;

        void IncrementCount()
        {
            currentCount++;
        }
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

## <a name="specify-a-component-base-class"></a>指定组件基类

`@inherits` 指令可用于指定组件的基类。

[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)演示组件如何继承基类 `BlazorRocksBase`，以便提供组件的属性和方法。

*Pages/BlazorRocks*：

```cshtml
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

基类应派生自 `ComponentBase`。

::: moniker-end

## <a name="import-components"></a>导入组件

使用 Razor 编写的组件的命名空间基于（按优先级顺序）：

* Razor 文件（*razor*）标记中[@namespace](xref:mvc/views/razor#namespace)指定（`@namespace BlazorSample.MyNamespace`）。
* 项目在项目文件中的 `RootNamespace` （`<RootNamespace>BlazorSample</RootNamespace>`）。
* 项目名称，从项目文件的文件名（ *.csproj*）获取，并从项目根路径到组件。 例如，框架将 *{PROJECT ROOT}/Pages/Index.razor* （*BlazorSample*）解析为命名空间 `BlazorSample.Pages`。 组件遵循C#名称绑定规则。 对于本示例中的 `Index` 组件，范围内的组件都是组件：
  * 在相同的文件夹中，*页*。
  * 项目的根中未显式指定其他命名空间的组件。

使用 Razor 的[@using](xref:mvc/views/razor#using)指令将不同命名空间中定义的组件引入作用域。

如果*BlazorSample/Shared/* 文件夹中存在另一个组件 `NavMenu.razor`，则可以使用以下 `@using` 语句在 `Index.razor` 中使用该组件：

```cshtml
@using BlazorSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

还可以使用其完全限定名称（不需要[@using](xref:mvc/views/razor#using)指令）来引用组件：

```cshtml
This is the Index page.

<BlazorSample.Shared.NavMenu></BlazorSample.Shared.NavMenu>
```

> [!NOTE]
> 不支持 `global::` 限制。
>
> 不支持导入具有化名 `using` 语句（例如，`@using Foo = Bar`）的组件。
>
> 不支持部分限定的名称。 例如，不支持添加 `@using BlazorSample` 和引用 `<Shared.NavMenu></Shared.NavMenu>` `NavMenu.razor`。

## <a name="conditional-html-element-attributes"></a>条件 HTML 元素特性

HTML 元素特性根据 .NET 值有条件地呈现。 如果该值 `false` 或 `null`，则不会呈现特性。 如果值为 `true`，则以最小化的特性呈现特性。

在下面的示例中，`IsCompleted` 确定元素的标记中是否呈现 `checked`：

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

如果 `true` `IsCompleted`，则复选框将呈现为：

```html
<input type="checkbox" checked />
```

如果 `false` `IsCompleted`，则复选框将呈现为：

```html
<input type="checkbox" />
```

有关更多信息，请参见<xref:mvc/views/razor>。

> [!WARNING]
> 当 .NET 类型为 `bool` 时，某些 HTML 特性（如[aria 按下](https://developer.mozilla.org/docs/Web/Accessibility/ARIA/Roles/button_role#Toggle_buttons)）不会正常运行。 在这些情况下，请使用 `string` 类型，而不是 `bool`。

## <a name="raw-html"></a>原始 HTML

通常使用 DOM 文本节点呈现字符串，这意味着将忽略它们可能包含的任何标记，并将其视为文本文本。 若要呈现原始 HTML，请将 HTML 内容换 `MarkupString` 值。 该值将分析为 HTML 或 SVG，并插入 DOM 中。

> [!WARNING]
> 呈现从任何不受信任的源构造的原始 HTML 会**带来安全风险**，应避免！

下面的示例演示如何使用 `MarkupString` 类型向组件的呈现输出添加静态 HTML 内容块：

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>模板化组件

模板化组件是接受一个或多个 UI 模板作为参数的组件，可将其用作组件呈现逻辑的一部分。 模板化组件允许你创作比常规组件更易于使用的更高级别的组件。 几个示例包括：

* 允许用户为表的标头、行和脚注指定模板的表组件。
* 允许用户在列表中指定用于呈现项的模板的列表组件。

### <a name="template-parameters"></a>模板参数

模板化组件是通过指定一个或多个 `RenderFragment` 或 `RenderFragment<T>` 类型的组件参数定义的。 呈现片段表示要呈现的 UI 段。 `RenderFragment<T>` 采用可在调用呈现片段时指定的类型参数。

`TableTemplate` 组件：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/TableTemplate.razor)]

使用模板化组件时，可以使用与参数名称匹配的子元素（在以下示例中为 `TableHeader` 和 `RowTemplate`）指定模板参数：

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@context.PetId</td>
        <td>@context.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="template-context-parameters"></a>模板上下文参数

作为元素传递的类型 `RenderFragment<T>` 的组件参数具有一个名为 `context` 的隐式参数（例如，前面的代码示例 `@context.PetId`），但你可以使用子元素上的 `Context` 特性来更改参数名称。 在下面的示例中，`RowTemplate` 元素的 `Context` 特性指定了 `pet` 参数：

```cshtml
<TableTemplate Items="pets">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate Context="pet">
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

另外，还可以在 component 元素上指定 `Context` 属性。 指定的 `Context` 特性适用于所有指定的模板参数。 如果要为隐式子内容指定内容参数名称（不包含任何换行子元素），这会很有用。 在下面的示例中，`Context` 特性显示在 `TableTemplate` 元素上，并应用于所有模板参数：

```cshtml
<TableTemplate Items="pets" Context="pet">
    <TableHeader>
        <th>ID</th>
        <th>Name</th>
    </TableHeader>
    <RowTemplate>
        <td>@pet.PetId</td>
        <td>@pet.Name</td>
    </RowTemplate>
</TableTemplate>
```

### <a name="generic-typed-components"></a>泛型类型化组件

模板化组件通常是通用类型。 例如，泛型 `ListViewTemplate` 组件可用于呈现 `IEnumerable<T>` 值。 若要定义一般组件，请使用[@typeparam](xref:mvc/views/razor#typeparam)指令指定类型参数：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/ListViewTemplate.razor)]

当使用泛型类型的组件时，将在可能的情况下推断类型参数：

```cshtml
<ListViewTemplate Items="pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

否则，必须使用与类型参数的名称匹配的属性显式指定 type 参数。 在下面的示例中，`TItem="Pet"` 指定类型：

```cshtml
<ListViewTemplate Items="pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
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

例如，示例应用程序将应用程序布局之一中的主题信息（`ThemeInfo`）指定为构成 `@Body` 属性布局正文的所有组件的级联参数。 在布局组件中为 `ButtonClass` 分配 `btn-success` 值。 任何子代组件都可以通过 `ThemeInfo` 级联对象使用此属性。

`CascadingValuesParametersLayout` 组件：

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="theme">
                <div class="content px-4">
                    @Body
                </div>
            </CascadingValue>
        </div>
    </div>
</div>

@code {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

为了使用级联值，组件使用 `[CascadingParameter]` 属性声明级联参数。 级联值按类型绑定到级联参数。

在示例应用中，`CascadingValuesParametersTheme` 组件将 `ThemeInfo` 级联值绑定到级联参数。 参数用于设置由组件显示的一个按钮的 CSS 类。

`CascadingValuesParametersTheme` 组件：

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

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
    private int currentCount = 0;

    [CascadingParameter]
    protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

若要在同一子树内级联多个相同类型的值，请为每个 `CascadingValue` 组件及其相应的 `CascadingParameter` 提供唯一的 `Name` 字符串。 在下面的示例中，两个 `CascadingValue` 组件按名称级联了 `MyCascadingType` 的不同实例：

```cshtml
<CascadingValue Value=@ParentCascadeParameter1 Name="CascadeParam1">
    <CascadingValue Value=@ParentCascadeParameter2 Name="CascadeParam2">
        ...
    </CascadingValue>
</CascadingValue>

@code {
    private MyCascadingType ParentCascadeParameter1;

    [Parameter]
    public MyCascadingType ParentCascadeParameter2 { get; set; }

    ...
}
```

在子代组件中，级联参数按名称从上级组件中相应的级联值接收值：

```cshtml
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

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

子 `Tab` 组件不会显式作为参数传递给 `TabSet`。 子 `Tab` 组件是 `TabSet` 的子内容的一部分。 但 `TabSet` 仍需要了解每个 `Tab` 组件，以便它可以呈现标头和活动选项卡。若要启用此协调而不需要额外的代码，`TabSet` 组件*可将自身作为级联值提供*，然后由子代 `Tab` 组件选取。

`TabSet` 组件：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/TabSet.razor)]

子代 `Tab` 组件将包含 `TabSet` 作为级联参数捕获，因此 `Tab` 组件会将其自身添加到 `TabSet` 并协调哪个选项卡处于活动状态。

`Tab` 组件：

[!code-cshtml[](common/samples/3.x/BlazorWebAssemblySample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor 模板

可以使用 Razor 模板语法来定义呈现片段。 Razor 模板是一种定义 UI 代码段并采用以下格式的一种方法：

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

下面的示例演示如何在组件中直接指定 `RenderFragment` 和 `RenderFragment<T>` 值以及呈现模板。 呈现片段还可以作为参数传递给[模板化组件](#templated-components)。

```cshtml
@timeTemplate

@petTemplate(new Pet { Name = "Rex" })

@code {
    private RenderFragment timeTemplate = @<p>The time is @DateTime.Now.</p>;
    private RenderFragment<Pet> petTemplate = 
        (pet) => @<p>Your pet's name is @pet.Name.</p>;

    private class Pet
    {
        public string Name { get; set; }
    }
}
```

前面的代码的呈现输出：

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a>手动 RenderTreeBuilder 逻辑

`Microsoft.AspNetCore.Components.Rendering.RenderTreeBuilder` 提供了用于操作组件和元素的方法，包括在代码C#中手动生成组件。

> [!NOTE]
> 使用 `RenderTreeBuilder` 创建组件是一种高级方案。 格式不正确的组件（例如，未闭合的标记标记）可能会导致未定义的行为。

请考虑以下 `PetDetails` 组件，可以手动将其内置到另一个组件中：

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

在下面的示例中，`CreateComponent` 方法中的循环生成三个 `PetDetails` 组件。 当调用 `RenderTreeBuilder` 方法来创建组件（`OpenComponent` 和 `AddAttribute`）时，序列号为源代码行号。 Blazor 差异算法依赖于与不同代码行对应的序列号，而不是不同的调用调用。 使用 `RenderTreeBuilder` 方法创建组件时，将序列号的参数硬编码。 **使用计算或计数器生成序列号会导致性能不佳。** 有关详细信息，请参阅 "[序列号与代码行号和非执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)" 部分。

`BuiltContent` 组件：

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" @onclick="RenderComponent">
    Create three Pet Details components
</button>

@code {
    private RenderFragment CustomRender { get; set; }
    
    private RenderFragment CreateComponent() => builder =>
    {
        for (var i = 0; i < 3; i++) 
        {
            builder.OpenComponent(0, typeof(PetDetails));
            builder.AddAttribute(1, "PetDetailsQuote", "Someone's best friend!");
            builder.CloseComponent();
        }
    };    
    
    private void RenderComponent()
    {
        CustomRender = CreateComponent();
    }
}
```

> !出现`Microsoft.AspNetCore.Components.RenderTree` 中的类型允许处理呈现操作的*结果*。 这是 Blazor 框架实现的内部详细信息。 这些类型应被视为不*稳定*，并且在将来的版本中可能会更改。

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>序列号与代码行号相关，而不是与执行顺序相关

Blazor `.razor` 文件始终被编译。 这可能是 `.razor` 的优点，因为编译步骤可用于注入在运行时提高应用性能的信息。

这些改进涉及到*序列号*的主要示例。 序列号指示运行时输出来自代码的不同和有序行。 运行时使用此信息以线性时间生成有效的树差异，其速度远快于一般树差异算法通常可以实现的速度。

请考虑以下简单的 `.razor` 文件：

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

前面的代码编译为类似于下面的内容：

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

当第一次执行代码时，如果 `true` `someFlag`，生成器将接收：

| 序列 | 键入      | 数据   |
| :------: | --------- | :----: |
| 0        | Text 节点 | First  |
| 1        | Text 节点 | 秒 |

假设 `someFlag` 成为 `false`，并再次呈现标记。 这次，生成器将接收：

| 序列 | 键入       | 数据   |
| :------: | ---------- | :----: |
| 1        | Text 节点  | 秒 |

当运行时执行差异时，它会发现序列 `0` 的项已被删除，因此它生成以下简单的*编辑脚本*：

* 删除第一个文本节点。

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a>如果以编程方式生成序列号，会出现什么错误

假设你编写了以下呈现树生成器逻辑：

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

现在，第一个输出是：

| 序列 | 键入      | 数据   |
| :------: | --------- | :----: |
| 0        | Text 节点 | First  |
| 1        | Text 节点 | 秒 |

此结果与以前的情况相同，因此不存在负面问题。 第二个呈现 `false` `someFlag`，输出为：

| 序列 | 键入      | 数据   |
| :------: | --------- | ------ |
| 0        | Text 节点 | 秒 |

这次，比较算法会发现*两个*更改已发生，并且算法将生成以下编辑脚本：

* 将第一个文本节点的值更改为 `Second`。
* 删除第二个文本节点。

生成序列号丢失了有关原始代码中出现 `if/else` 分支和循环的位置的所有有用信息。 这会导致**两次**比较，就像以前一样。

这是一个简单的示例。 在具有复杂、深度嵌套结构的更真实的情况下，特别是在循环中，性能开销更严重。 比较算法不必立即确定哪些循环块或分支已插入或删除，而是必须将其深入地递归到呈现树，并且通常会生成更长的编辑脚本，因为它 misinformed 了新的和新的结构彼此关联。

#### <a name="guidance-and-conclusions"></a>指导和结论

* 如果动态生成了序列号，则应用程序性能会受到影响。
* 框架无法在运行时自动创建自己的序列号，因为所需信息不存在，除非它在编译时捕获。
* 请勿写入长块手动实现 `RenderTreeBuilder` 逻辑。 首选 `.razor` 文件，并允许编译器处理序列号。 如果无法避免手动 `RenderTreeBuilder` 逻辑，请将长代码块拆分为 `OpenRegion` / `CloseRegion` 调用中的小块。 每个区域都有其自己单独的序列号空间，因此可以从每个区域内的零（或任何其他任意数字）重新启动。
* 如果对序列号进行硬编码，则 diff 算法只要求序列号的值增加。 初始值和间隙无关。 一个合法选项是将代码行号用作序列号，或从零开始，并按一个或数百个（或任何首选间隔）递增。 
* Blazor 使用序列号，而其他树比较的 UI 框架不使用序列号。 使用序列号时，比较速度要快得多，Blazor 的优点是编译步骤会自动处理序列号，以便开发人员创作 `.razor` 文件。

## <a name="localization"></a>本地化

使用[本地化中间件](xref:fundamentals/localization#localization-middleware)对 Blazor 服务器应用进行本地化。 中间件为从应用程序请求资源的用户选择相应的区域性。

可以使用以下方法之一设置区域性：

* [Cookie](#cookies)
* [提供用于选择区域性的 UI](#provide-ui-to-choose-the-culture)

有关更多信息和示例，请参见<xref:fundamentals/localization>。

### <a name="cookies"></a>Cookie

本地化区域性 cookie 可以保存用户的区域性。 该 cookie 是由应用程序主机页（*Pages/host. .cs*）的 `OnGet` 方法创建的。 本地化中间件会在后续请求上读取 cookie，以设置用户的区域性。 

使用 cookie 可确保 WebSocket 连接可以正确地传播区域性。 如果本地化方案基于 URL 路径或查询字符串，则该方案可能无法与 Websocket 一起使用，因此无法持久保存区域性。 因此，建议使用本地化区域性 cookie。

如果在本地化 cookie 中保留了区域性，则可使用任何方法来分配区域性。 如果应用已建立服务器端 ASP.NET Core 的本地化方案，请继续使用应用的现有本地化基础结构，并在应用方案中设置本地化区域性 cookie。

下面的示例演示如何在可由本地化中间件读取的 cookie 中设置当前区域性。 在 Blazor 服务器应用中创建包含以下内容的*页面/主机 .cs*文件：

```csharp
public class HostModel : PageModel
{
    public void OnGet()
    {
        HttpContext.Response.Cookies.Append(
            CookieRequestCultureProvider.DefaultCookieName,
            CookieRequestCultureProvider.MakeCookieValue(
                new RequestCulture(
                    CultureInfo.CurrentCulture,
                    CultureInfo.CurrentUICulture)));
    }
}
```

在应用程序中处理本地化：

1. 浏览器将初始 HTTP 请求发送到应用程序。
1. 区域性由本地化中间件分配。
1. *_Host*中的 `OnGet` 方法将区域性作为响应的一部分保留在 cookie 中。
1. 浏览器将打开 WebSocket 连接以创建交互式 Blazor 服务器会话。
1. 本地化中间件读取 cookie 并分配区域性。
1. Blazor 服务器会话以正确的区域性开头。

## <a name="provide-ui-to-choose-the-culture"></a>提供用于选择区域性的 UI

为了提供允许用户选择区域性的 UI，建议使用*基于重定向的方法*。 此过程类似于用户尝试访问安全资源时在 web 应用中发生的情况，&mdash;the 用户重定向到登录页，然后重定向回原始资源。 

应用程序通过重定向到控制器来持久保存用户的所选区域性。 控制器将用户选定的区域性设置为 cookie，并将用户重定向回原始 URI。

在服务器上创建一个 HTTP 终结点，以在 cookie 中设置用户的所选区域性，并执行重定向回原始 URI：

```csharp
[Route("[controller]/[action]")]
public class CultureController : Controller
{
    public IActionResult SetCulture(string culture, string redirectUri)
    {
        if (culture != null)
        {
            HttpContext.Response.Cookies.Append(
                CookieRequestCultureProvider.DefaultCookieName,
                CookieRequestCultureProvider.MakeCookieValue(
                    new RequestCulture(culture)));
        }

        return LocalRedirect(redirectUri);
    }
}
```

> [!WARNING]
> 使用 `LocalRedirect` 操作结果可防止开放重定向攻击。 有关更多信息，请参见<xref:security/preventing-open-redirects>。

以下组件显示了一个示例，说明如何在用户选择区域性时执行初始重定向：

```cshtml
@inject NavigationManager NavigationManager

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(ChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(NavigationManager.Uri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        NavigationManager.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a>在 Blazor 应用中使用 .NET 本地化方案

在 Blazor 应用程序中，可以使用以下 .NET 本地化和全球化方案：

* .网络资源系统
* 区域性特定的数字和日期格式设置

Blazor 的 `@bind` 功能基于用户的当前区域性执行全球化。 有关详细信息，请参阅[数据绑定](#data-binding)部分。

目前支持有限的一组 ASP.NET Core 本地化方案：

* Blazor 应用*支持*`IStringLocalizer<>`。
* `IHtmlLocalizer<>`、`IViewLocalizer<>` 和数据批注本地化 ASP.NET Core MVC 方案，在 Blazor 应用中**不受支持**。

有关更多信息，请参见<xref:fundamentals/localization>。

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

但是，在所有情况下均不支持内联 SVG 标记。 如果将 `<svg>` 标记直接放入组件文件（*razor*），则支持基本图像呈现，但尚不支持很多高级方案。 例如，当前未遵循 `<use>` 标记，并且不能将 `@bind` 与某些 SVG 标记一起使用。 我们预计会在将来的版本中解决这些限制。

## <a name="additional-resources"></a>其他资源

* <xref:security/blazor/server> &ndash; 包括构建必须与资源耗尽相关的 Blazor 服务器应用的指南。
