---
title: 创建和使用 ASP.NET Core Razor 组件
author: guardrex
description: 了解如何创建和使用 Razor 组件, 包括如何绑定到数据、处理事件和管理组件生命周期。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/13/2019
uid: blazor/components
ms.openlocfilehash: e1afae730d61463d31c8a1698fc31904a3fc8f0e
ms.sourcegitcommit: 257cc3fe8c1d61341aa3b07e5bc0fa3d1c1c1d1c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2019
ms.locfileid: "69583089"
---
# <a name="create-and-use-aspnet-core-razor-components"></a>创建和使用 ASP.NET Core Razor 组件

作者: [Luke Latham](https://github.com/guardrex)和[Daniel Roth](https://github.com/danroth27)

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

Blazor 应用是使用*组件*生成的。 组件是自包含的用户界面 (UI) 块, 如页、对话框或窗体。 组件包含插入数据或响应 UI 事件所需的 HTML 标记和处理逻辑。 组件非常灵活且轻型。 它们可以嵌套、重复使用和在项目之间共享。

## <a name="component-classes"></a>组件类

在[razor](xref:mvc/views/razor)组件文件 (*razor*) 中, 使用和 HTML 标记的C#组合实现了组件。 Blazor 中的组件正式称为*Razor 组件*。

组件的名称必须以大写字符开头。 例如, *MyCoolComponent*是有效的, 并且*MyCoolComponent*无效。

可以使用 # 文件扩展名编写组件, 只要使用`_RazorComponentInclude` MSBuild 属性将这些文件标识为 Razor 组件文件即可。 例如, 指定 "*页面*" 文件夹下的所有 *.* 文件都应被视为 Razor 组件文件的应用:

```xml
<PropertyGroup>
  <_RazorComponentInclude>Pages\**\*.cshtml</_RazorComponentInclude>
</PropertyGroup>
```

使用 HTML 定义组件的 UI。 动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。 在编译应用程序时, 会将 HTML 标记C#和呈现逻辑转换为组件类。 生成的类的名称与文件的名称匹配。

组件类的成员在 `@code` 块中定义。 `@code`在块中, 组件状态 ("属性"、"字段") 通过事件处理方法或定义其他组件逻辑来指定。 允许多个 `@code` 块。

> [!NOTE]
> 在以前的 ASP.NET Core 3.0 的预览`@functions`中, 将块用于与 Razor 组件`@code`中的块相同的用途。 `@functions`块继续在 Razor 组件中运行, 但我们建议使用`@code`块 ASP.NET Core 3.0 Preview 6 或更高版本。

组件成员可以使用C#以开头`@`的表达式作为组件的呈现逻辑的一部分。 例如, C#字段通过在字段名称前面`@`加上来呈现。 下面的示例计算并呈现:

* `_headingFontStyle`的 CSS 属性值`font-style`。
* `_headingText``<h1>`元素的内容。

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@code {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

最初呈现组件后, 组件会为响应事件而重新生成其呈现树。 然后, Blazor 将新的呈现树与上一个树进行比较, 并将所有修改应用于浏览器的文档对象模型 (DOM)。

组件是普通C#类, 可以放置在项目中的任何位置。 生成网页的组件通常位于*Pages*文件夹中。 非页组件通常放置在*共享*文件夹中或添加到项目的自定义文件夹中。 若要使用自定义文件夹, 请将自定义文件夹的命名空间添加到父组件或应用程序的 *_Imports*文件。 例如, 以下命名空间使*组件*文件夹中的组件在应用程序的根命名空间为`WebApplication`时可用:

```cshtml
@using WebApplication.Components
```

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>将组件集成到 Razor Pages 和 MVC 应用

将组件用于现有 Razor Pages 和 MVC 应用。 无需重写现有页面或视图即可使用 Razor 组件。 呈现页面或视图时, 组件将同时预呈现。

若要从页面或视图呈现组件, 请使用`RenderComponentAsync<TComponent>` HTML 帮助器方法:

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

尽管页面和视图可以使用组件, 但不是这样。 组件不能使用视图和特定于页的方案, 如分部视图和节。 若要在组件中通过分部视图使用逻辑, 请将分部视图逻辑分解为一个组件。

有关如何呈现组件和在 Blazor 服务器端应用程序中管理组件状态的详细信息, 请参阅<xref:blazor/hosting-models>文章。

## <a name="use-components"></a>使用组件

组件可以通过使用 HTML 元素语法声明组件来包含其他组件。 使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。

属性绑定区分大小写。 例如, `@bind`是有效的, 并且`@Bind`无效。

*Index*中的以下标记将呈现`HeadingComponent`实例:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.razor?name=snippet_HeadingComponent)]

*组件/HeadingComponent*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/HeadingComponent.razor)]

如果某个组件包含一个 HTML 元素, 该元素的首字母大写字母与组件名称不匹配, 则会发出警告, 指示该元素具有意外的名称。 为组件命名空间添加语句使组件可用,从而消除了警告。`@using`

## <a name="component-parameters"></a>组件参数

组件可以具有*组件参数*, 这些参数是使用组件类上的公共属性和`[Parameter]`属性定义的。 使用这些属性在标记中为组件指定参数。

*组件/ChildComponent*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=11-12)]

在下面的示例中, `ParentComponent`设置的`Title` `ChildComponent`属性的值。

*Pages/ParentComponent*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=5-6)]

## <a name="child-content"></a>子内容

组件可以设置另一个组件的内容。 分配组件提供用于指定接收组件的标记之间的内容。

在下面的示例中, `ChildComponent`具有一个`ChildContent`表示`RenderFragment`的属性, 该属性表示要呈现的 UI 段。 的值`ChildContent`定位在应呈现内容的组件标记中。 的值`ChildContent`从父组件接收, 并呈现在启动面板的`panel-body`中。

*组件/ChildComponent*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=3,14-15)]

> [!NOTE]
> 必须按约定命名`RenderFragment` `ChildContent`接收内容的属性。

以下`ParentComponent`内容可以通过将`<ChildComponent>`内容放入`ChildComponent`标记中来提供用于呈现的内容。

*Pages/ParentComponent*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=7-8)]

## <a name="attribute-splatting-and-arbitrary-parameters"></a>Attribute 展开和任意参数

除了组件的声明参数外, 组件还可以捕获和呈现附加属性。 其他属性可以在字典中捕获, 然后在使用[@attributes](xref:mvc/views/razor#attributes) Razor 指令呈现组件时 splatted 到元素。 此方案在定义生成支持各种自定义项的标记元素的组件时非常有用。 例如, 为支持多个参数的指定单独`<input>`定义属性可能会很繁琐。

在下面的示例中, 第`<input>`一个元素`id="useIndividualParams"`() 使用单个组件参数, 第二`<input>`个元素`id="useAttributesDict"`() 使用属性展开:

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

参数的类型必须实现`IEnumerable<KeyValuePair<string, object>>`字符串键。 在`IReadOnlyDictionary<string, object>`此方案中, 还可以选择使用。

使用这`<input>`两种方法的呈现元素相同:

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

若要接受任意属性, 请使用`[Parameter]`属性设置为的特性`CaptureUnmatchedValues`来`true`定义组件参数:

```cshtml
@code {
    [Parameter(CaptureUnmatchedValues = true)]
    public Dictionary<string, object> InputAttributes { get; set; }
}
```

`CaptureUnmatchedValues` 上`[Parameter]`的属性允许参数匹配与任何其他参数不匹配的所有属性。 组件只能使用`CaptureUnmatchedValues`定义一个参数。 使用的属性类型`CaptureUnmatchedValues`必须可从`Dictionary<string, object>`和字符串键赋值。 `IEnumerable<KeyValuePair<string, object>>`在`IReadOnlyDictionary<string, object>`此方案中, 或也是选项。

## <a name="data-binding"></a>数据绑定

对组件和 DOM 元素的数据绑定都是通过[@bind](xref:mvc/views/razor#bind)属性实现的。 下面的示例将`_italicsCheck`字段绑定到复选框的选中状态:

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    @bind="_italicsCheck" />
```

选中并清除该复选框后, 属性的值将分别更新为`true`和。 `false`

仅当呈现组件时, 才会在 UI 中更新此复选框, 而不会在响应属性值的更改时进行更新。 由于组件会在事件处理程序代码执行后呈现自身, 因此属性更新通常会立即反映在 UI 中。

与属性 ( `@bind` )`<input @bind="CurrentValue" />`一起使用本质上等效于以下内容: `CurrentValue`

```cshtml
<input value="@CurrentValue"
    @onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)" />
```

呈现组件时, 输入元素`value` `CurrentValue`的将来自属性。 当用户在文本框中键入内容时, `onchange`将触发事件`CurrentValue` , 并将属性设置为更改的值。 事实上, 由于`@bind`处理类型转换的几种情况, 代码生成会稍微复杂一些。 原则上, `@bind`将表达式的当前值`value`与属性相关联, 并使用注册的处理程序来处理更改。

除了使用`@bind`语法处理`onchange`事件之外, 还可以通过使用`event`参数 ([@bind-value:event](xref:mvc/views/razor#bind)) 指定[@bind-value](xref:mvc/views/razor#bind)属性, 使用其他事件来绑定属性或字段。 下面的示例绑定`CurrentValue` `oninput`事件的属性:

```cshtml
<input @bind-value="CurrentValue" @bind-value:event="oninput" />
```

不同`onchange`于, 当元素失去`oninput`焦点时, 将在文本框的值更改时激发。

**全球化**

`@bind`值的格式设置为显示, 并使用当前区域性的规则进行分析。

可从<xref:System.Globalization.CultureInfo.CurrentCulture?displayProperty=fullName>属性访问当前区域性。

[InvariantCulture](xref:System.Globalization.CultureInfo.InvariantCulture)用于以下字段类型 (`<input type="{TYPE}" />`):

* `date`
* `number`

上述字段类型:

* 使用其基于浏览器的适当格式规则显示。
* 不能包含自由格式的文本。
* 基于浏览器的实现提供用户交互特性。

以下字段类型具有特定的格式要求, Blazor 当前不支持它们, 因为它们不受所有主要浏览器的支持:

* `datetime-local`
* `month`
* `week`

`@bind`支持参数以<xref:System.Globalization.CultureInfo?displayProperty=fullName>提供用于分析值和设置值的格式。 `@bind:culture` 使用`date` 和`number`字段类型时不建议指定区域性。 `date`和`number`具有提供所需区域性的内置 Blazor 支持。

有关如何设置用户的区域性的信息, 请参阅[本地化](#localization)部分。

**格式字符串**

数据绑定<xref:System.DateTime>使用[@bind:format](xref:mvc/views/razor#bind)格式字符串。 现在不能使用其他格式的表达式, 如货币或数字格式。

```cshtml
<input @bind="StartDate" @bind:format="yyyy-MM-dd" />

@code {
    [Parameter]
    public DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

在前面的代码中, `<input>`元素的字段类型 (`type`) 默认为`text`。 `@bind:format`支持绑定以下 .NET 类型:

* <xref:System.DateTime?displayProperty=fullName>
* <xref:System.DateTime?displayProperty=fullName>?
* <xref:System.DateTimeOffset?displayProperty=fullName>
* <xref:System.DateTimeOffset?displayProperty=fullName>?

特性指定要应用`value` `<input>`于元素的的日期格式。 `@bind:format` 当发生`onchange`事件时, 该格式还用于分析值。

不建议为`date`字段类型指定格式, 因为 Blazor 具有对日期格式的内置支持。

**组件参数**

绑定可识别组件参数, `@bind-{property}`可在其中跨组件绑定属性值。

以下子组件 (`ChildComponent`) 具有一个`Year`组件参数和`YearChanged`回调:

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

`EventCallback<T>`在[EventCallback](#eventcallback)部分中进行了介绍。

以下父组件使用`ChildComponent`并将`ParentYear`参数从父级绑定到子组件上`Year`的参数:

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

加载将`ParentComponent`生成以下标记:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

如果通过选择`ParentComponent`中的`ParentYear`按钮来更改属性的值, `Year`则将更新的`ChildComponent`属性。 在重新呈现`Year` `ParentComponent`时, 的新值将呈现在 UI 中:

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

参数`Year`是可绑定的, 因为它具有`YearChanged` `Year`与参数类型匹配的伴随事件。

按照约定, `<ChildComponent @bind-Year="ParentYear" />`本质上等效于编写:

```cshtml
<ChildComponent @bind-Year="ParentYear" @bind-Year:event="YearChanged" />
```

通常, 可以使用`@bind-property:event`属性将属性绑定到相应的事件处理程序。 例如, 可以使用以下`MyProp`两个属性将`MyEventHandler`属性绑定到:

```cshtml
<MyComponent @bind-MyProp="MyValue" @bind-MyProp:event="MyEventHandler" />
```

## <a name="event-handling"></a>事件处理

Razor 组件提供事件处理功能。 对于带有委托类型值的`on{event}`名为 ( `onclick`例如和`onsubmit`) 的 HTML 元素特性, Razor 组件将属性的值视为事件处理程序。 特性的名称始终为 "格式[ @on{事件}](xref:mvc/views/razor#onevent)"。

当在 UI 中选择`UpdateHeading`该按钮时, 以下代码将调用方法:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

如果 UI 中的复选`CheckChanged`框发生了更改, 以下代码将调用方法:

```cshtml
<input type="checkbox" class="form-check-input" @onchange="CheckChanged" />

@code {
    private void CheckChanged()
    {
        ...
    }
}
```

事件处理程序也可以是异步的<xref:System.Threading.Tasks.Task>并返回。 无需手动调用`StateHasChanged()`。 异常在发生时进行记录。

在下面的示例中`UpdateHeading` , 当选择按钮时, 将异步调用:

```cshtml
<button class="btn btn-primary" @onclick="UpdateHeading">
    Update heading
</button>

@code {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

### <a name="event-argument-types"></a>事件参数类型

对于某些事件, 允许使用事件参数类型。 如果不需要访问这些事件类型之一, 则方法调用中不需要这样做。

下表显示了支持的[UIEventArgs](https://github.com/aspnet/AspNetCore/blob/release/3.0-preview8/src/Components/Components/src/UIEventArgs.cs) 。

| Event | 类 |
| ----- | ----- |
| 剪贴板 | `UIClipboardEventArgs` |
| 入  | `UIDragEventArgs`用于在拖放操作过程中保存拖动的数据, 并且可以容纳一个或多个`UIDataTransferItem`。 &ndash; `DataTransfer` `UIDataTransferItem`表示一个拖动数据项。 |
| Error | `UIErrorEventArgs` |
| 焦点 | `UIFocusEventArgs`不包含对的`relatedTarget`支持。 &ndash; |
| `<input>` 已更改 | `UIChangeEventArgs` |
| 键盘 | `UIKeyboardEventArgs` |
| 鼠标 | `UIMouseEventArgs` |
| 鼠标指针 | `UIPointerEventArgs` |
| 鼠标滚轮 | `UIWheelEventArgs` |
| 进度 | `UIProgressEventArgs` |
| 触控 | `UITouchEventArgs`&ndash; 表示触摸敏感设备上`UITouchPoint`的单个联系点。 |

有关上表中事件的属性和事件处理行为的信息, 请参阅[引用源中的 EventArgs 类](https://github.com/aspnet/AspNetCore/tree/release/3.0-preview8/src/Components/Web/src)。

### <a name="lambda-expressions"></a>Lambda 表达式

还可以使用 Lambda 表达式:

```cshtml
<button @onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

通常可以很方便地关闭其他值, 如在循环访问一组元素时。 下面的示例创建了三个按钮, 每个`UpdateHeading`按钮在 UI 中选择`UIMouseEventArgs`时均调用传递事件参数`buttonNumber`() 和按钮号 ():

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

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            $"mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> 不要直接在 lambda 表达式中的`i` `for`循环中使用循环变量 ()。 否则, 所有 lambda 表达式都将使用相同的变量`i`, 从而使所有 lambda 中的值都相同。 始终捕获其在本地变量中的值`buttonNumber` (在前面的示例中), 然后使用它。

### <a name="eventcallback"></a>EventCallback

使用嵌套组件的常见方案是, 当发生&mdash;子组件事件时 (例如, `onclick`当子组件发生在子组件中时) 需要运行父组件的方法。 若要跨组件公开事件, 请`EventCallback`使用。 父组件可将回调方法分配给子组件的`EventCallback`。

示例`ChildComponent`应用中的演示如何`EventCallback`设置按钮的`onclick`处理程序, 以接收来自示例的的`ParentComponent`委托。 使用进行类型化, `onclick`这适用于来自外围设备的事件: `UIMouseEventArgs` `EventCallback`

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ChildComponent.razor?highlight=5-7,17-18)]

将子级设置为其`ShowMessage`方法: `ParentComponent` `EventCallback<T>`

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.razor?name=snippet_ParentComponent&highlight=6,16-19)]

如果在中`ChildComponent`选择了该按钮:

* 调用`ParentComponent`的`ShowMessage`方法。 `messageText`会更新并显示在中`ParentComponent`。
* 在回调的`StateHasChanged`方法 (`ShowMessage`) 中不需要对的调用。 `StateHasChanged`自动调用以诸如此类`ParentComponent`, 就像子事件在子中执行的事件处理程序中 rerendering。

`EventCallback`和`EventCallback<T>`允许异步委托。 `EventCallback<T>`为强类型, 需要特定的参数类型。 `EventCallback`是弱类型, 允许任何参数类型。

```cshtml
<p><b>@messageText</b></p>

@{ var message = "Default Text"; }

<ChildComponent 
    OnClick="@(async () => { await Task.Yield(); messageText = "Blaze It!"; })" />

@code {
    private string messageText;
}
```

`EventCallback`调用或`EventCallback<T>` , 并等待<xref:System.Threading.Tasks.Task>: `InvokeAsync`

```csharp
await callback.InvokeAsync(arg);
```

将`EventCallback` 和`EventCallback<T>`用于事件处理和绑定组件参数。

优先使用强类型`EventCallback<T>`化`EventCallback`。 `EventCallback<T>`向组件的用户提供更好的错误反馈。 与其他 UI 事件处理程序类似, 指定事件参数是可选的。 当`EventCallback`没有任何值传递到回调时使用。

## <a name="capture-references-to-components"></a>捕获对组件的引用

组件引用提供了一种方法来引用组件实例, 以便可以向该实例发出命令, 如`Show`或。 `Reset` 捕获组件引用:

* 向子组件添加[属性。@ref](xref:mvc/views/razor#ref)
* 定义与子组件类型相同的字段。
* `@ref:suppressField`提供参数, 该参数可取消支持字段的生成。 有关详细信息, 请参阅[3.0.0-preview9 中的@ref删除自动支持字段支持](https://github.com/aspnet/Announcements/issues/381)。

```cshtml
<MyLoginDialog @ref="loginDialog" @ref:suppressField ... />

@code {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

呈现组件时, `loginDialog`将`MyLoginDialog`用子组件实例填充字段。 然后, 可以在组件实例上调用 .NET 方法。

> [!IMPORTANT]
> 仅在呈现组件后填充`MyLoginDialog` 变量,并且它的输出包含元素。`loginDialog` 在该点之前, 没有任何内容可供参考。 若要在组件完成呈现后操作组件引用, 请使用`OnAfterRenderAsync`或`OnAfterRender`方法。

<!-- HOLD https://github.com/aspnet/AspNetCore.Docs/pull/13818
Component references provide a way to reference a component instance so that you can issue commands to that instance, such as `Show` or `Reset`.

The Razor compiler automatically generates a backing field for element and component references when using [@ref](xref:mvc/views/razor#ref). In the following example, there's no need to create a `myLoginDialog` field for the `LoginDialog` component:

```cshtml
<LoginDialog @ref="myLoginDialog" ... />

@code {
    private void OnSomething()
    {
        myLoginDialog.Show();
    }
}
```

When the component is rendered, the generated `myLoginDialog` field is populated with the `LoginDialog` component instance. You can then invoke .NET methods on the component instance.

In some cases, a backing field is required. For example, declare a backing field when referencing generic components. To suppress backing field generation, specify the `@ref:suppressField` parameter.

> [!IMPORTANT]
> The generated `myLoginDialog` variable is only populated after the component is rendered and its output includes the `LoginDialog` element. Until that point, there's nothing to reference. To manipulate components references after the component has finished rendering, use the `OnAfterRenderAsync` or `OnAfterRender` methods.
-->

捕获组件引用时, 请使用类似的语法来[捕获元素引用](xref:blazor/javascript-interop#capture-references-to-elements), 而不是[JavaScript 互操作](xref:blazor/javascript-interop)功能。 不向 JavaScript 代码&mdash;传递组件引用, 它们仅用于 .net 代码。

> [!NOTE]
> 不要使用组件引用来改变子组件的状态。 请改用常规声明性参数将数据传递给子组件。 使用正常的声明性参数会导致子组件自动诸如此类正确的时间。

## <a name="use-key-to-control-the-preservation-of-elements-and-components"></a>使用\@key 控制是否保留元素和组件

在呈现元素或组件的列表并且元素或组件随后发生变化时, Blazor 的比较算法必须决定哪些之前的元素或组件可以保留, 以及模型对象应如何映射到它们。 通常, 此过程是自动的, 可以忽略, 但在某些情况下, 您可能需要控制该过程。

请看下面的示例：

```csharp
@foreach (var person in People)
{
    <DetailsEditor Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

`People`集合的内容可能会随插入、删除或重新排序条目而更改。 当组件 rerenders 时, `<DetailsEditor>`组件可能会更改以接收不同`Details`的参数值。 这可能导致比预期更复杂的 rerendering。 在某些情况下, rerendering 可能会导致可见行为差异, 如失去元素焦点。

可以用`@key`指令特性来控制映射过程。 `@key`使比较算法根据键的值保证保留元素或组件:

```csharp
@foreach (var person in People)
{
    <DetailsEditor @key="@person" Details="@person.Details" />
}

@code {
    [Parameter]
    public IEnumerable<Person> People { get; set; }
}
```

当集合更改时, 比较算法会保留实例和`person`实例`<DetailsEditor>`之间的关联: `People`

* 如果从列表中删除, 则只会从 UI `<DetailsEditor>`中删除相应的实例。 `People` `Person` 其他实例保持不变。
* 如果在列表中的某个位置插入, 则在相应位置`<DetailsEditor>`插入一个新实例。 `Person` 其他实例保持不变。
* 如果`Person`项是重新排序的, 则相应`<DetailsEditor>`的实例会保留并在 UI 中重新排序。

在某些情况下, 使用`@key`可最大程度地降低 rerendering 的复杂性, 并避免在 DOM 的有状态部分发生变化时出现潜在问题, 如焦点位置。

> [!IMPORTANT]
> 键对于每个容器元素或组件都是本地的。 不会在整个文档中全局地比较键。

### <a name="when-to-use-key"></a>何时使用\@密钥

通常, `@key`每次呈现列表时 (例如, `@foreach`在块中) 以及定义的`@key`合适值都有意义。

当对象发生更改`@key`时, 还可以使用阻止 Blazor 保留元素或组件子树:

```cshtml
<div @key="@currentPerson">
    ... content that depends on @currentPerson ...
</div>
```

如果`@currentPerson`发生更改, `@key`则 attribute 指令强制 Blazor 丢弃整个`<div>`及其子代, 并通过新的元素和组件重新生成 UI 中的子树。 如果需要确保更改时`@currentPerson`不保留 UI 状态, 这会很有用。

### <a name="when-not-to-use-key"></a>何时不使用\@密钥

与进行`@key`比较时, 会产生性能开销。 性能开销不大, 但仅指定`@key`控制元素或组件保留规则是否会对应用进行权益。

`@key`即使未使用, Blazor 也会尽可能地保留子元素和组件实例。 使用`@key`的唯一优点是控制*如何*将模型实例映射到保留的组件实例, 而不是选择映射的比较算法。

### <a name="what-values-to-use-for-key"></a>\@键要使用哪些值

通常, 为提供以下类型的值`@key`有意义:

* 模型对象实例 (例如, `Person`在前面的示例中为实例)。 这可确保基于对象引用相等性保存。
* 唯一标识符 (例如,、或`int` `Guid`类型`string`的主键值)。

确保用于的`@key`值不冲突。 如果在同一父元素内检测到冲突值, 则 Blazor 会引发异常, 因为它无法确定将旧元素或组件映射到新元素或组件。 仅使用非重复值, 例如对象实例或主键值。

## <a name="lifecycle-methods"></a>生命周期方法

`OnInitializedAsync`并`OnInitialized`执行代码来初始化该组件。 若要执行异步操作, 请`OnInitializedAsync`在操作`await`中使用和关键字:

```csharp
protected override async Task OnInitializedAsync()
{
    await ...
}
```

对于同步操作, 请使用`OnInitialized`:

```csharp
protected override void OnInitialized()
{
    ...
}
```

`OnParametersSetAsync`当`OnParametersSet`组件已从其父级接收参数并且为属性分配了值时, 将调用和。 这些方法在组件初始化之后以及每次呈现组件时执行:

```csharp
protected override async Task OnParametersSetAsync()
{
    await ...
}
```

```csharp
protected override void OnParametersSet()
{
    ...
}
```

`OnAfterRenderAsync`和`OnAfterRender`在组件完成呈现后被调用。 此时将填充元素和组件引用。 使用此阶段, 可以使用呈现的内容来执行其他初始化步骤, 如激活在呈现的 DOM 元素上操作的第三方 JavaScript 库。

```csharp
protected override async Task OnAfterRenderAsync()
{
    await ...
}
```

```csharp
protected override void OnAfterRender()
{
    ...
}
```

### <a name="handle-incomplete-async-actions-at-render"></a>在呈现时处理未完成的异步操作

在呈现组件之前, 在生命周期事件中执行的异步操作可能尚未完成。 当执行生命`null`周期方法时, 对象可能会或未完全填充数据。 提供呈现逻辑以确认对象已初始化。 在对象为`null`时呈现占位符 UI 元素 (例如, 加载消息)。

在 Blazor 模板的`OnInitializedAsync` `forecasts`组件中, 将重写为 asychronously 接收预测数据 ()。 `FetchData` 如果`forecasts` 为`null`, 则向用户显示一条加载消息。 完成`OnInitializedAsync`返回后, 组件将重新呈现已更新状态。 `Task`

*Pages/FetchData.razor*：

[!code-cshtml[](components/samples_snapshot/3.x/FetchData.razor?highlight=9)]

### <a name="execute-code-before-parameters-are-set"></a>在设置参数之前执行代码

`SetParameters`可以重写, 以在设置参数之前执行代码:

```csharp
public override void SetParameters(ParameterView parameters)
{
    ...

    base.SetParameters(parameters);
}
```

如果`base.SetParameters`未调用, 则自定义代码可以通过任何所需的方式解释传入参数值。 例如, 无需将传入参数分配给类的属性。

### <a name="suppress-refreshing-of-the-ui"></a>禁止刷新 UI

`ShouldRender`可以重写以禁止刷新 UI。 如果实现返回`true`, 则刷新 UI。 `ShouldRender`即使重写, 组件也始终是最初呈现的。

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>用 IDisposable 进行的组件处置

如果某个组件实现<xref:System.IDisposable>, 则从 UI 中删除该组件时, 将调用[Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)。 以下组件使用`@implements IDisposable` `Dispose`和方法:

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

## <a name="routing"></a>路由

通过向应用程序中的每个可访问组件提供路由模板, 实现 Blazor 中的路由。

在编译包含`@page`指令的 Razor 文件时, 将为<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>生成的类指定指定路由模板的。 在运行时, 路由器将使用`RouteAttribute`查找组件类, 并呈现哪个组件包含与请求的 URL 匹配的路由模板。

可以将多个路由模板应用于组件。 以下组件响应和`/BlazorRoute` `/DifferentBlazorRoute`的请求:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.razor?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>路由参数

组件可以从`@page`指令中提供的路由模板接收路由参数。 路由器使用路由参数来填充相应的组件参数。

*路由参数组件*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.razor?name=snippet_RouteParameter)]

不支持可选参数, 因此上面`@page`的示例中应用了两个指令。 第一个允许导航到没有参数的组件。 第二`@page`个指令`{text}`采用路由参数, 并将值`Text`分配给属性。

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>"代码隐藏" 体验的基类继承

组件文件将 HTML 标记和C#处理代码组合到同一个文件中。 `@inherits`指令可用于通过 "代码隐藏" 体验来提供 Blazor 应用, 这些应用将组件标记与处理代码分开。

[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/blazor/common/samples/)演示组件如何继承基类, `BlazorRocksBase`以提供组件的属性和方法。

*Pages/BlazorRocks*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.razor?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

基类应派生自`ComponentBase`。

## <a name="import-components"></a>导入组件

使用 Razor 编写的组件的命名空间基于:

* 项目的`RootNamespace`。
* 从项目根到组件的路径。 例如, `ComponentsSample/Pages/Index.razor`位于命名空间`ComponentsSample.Pages`中。 组件遵循C#名称绑定规则。 对于*Index*, 同一文件夹、*页面*和父文件夹*ComponentsSample*中的所有组件都在作用域内。

在不同的命名空间中定义的组件可以使用 Razor 的[ \@using](xref:mvc/views/razor#using)指令进入作用域。

`NavMenu.razor`如果文件夹`ComponentsSample/Shared/`中存在另一个组件, 则可在中`Index.razor`将组件与以下`@using`语句一起使用:

```cshtml
@using ComponentsSample.Shared

This is the Index page.

<NavMenu></NavMenu>
```

还可以使用其完全限定名称来引用组件, 这无需[ \@使用](xref:mvc/views/razor#using)指令:

```cshtml
This is the Index page.

<ComponentsSample.Shared.NavMenu></ComponentsSample.Shared.NavMenu>
```

> [!NOTE]
> 不`global::`支持此限制。
>
> 不支持导入`using`带有别名的语句的`@using Foo = Bar`组件 (例如)。
>
> 不支持部分限定的名称。 例如, `<Shared.NavMenu></Shared.NavMenu>`不支持`@using ComponentsSample`添加和`NavMenu.razor`引用。

## <a name="conditional-html-element-attributes"></a>条件 HTML 元素特性

HTML 元素特性根据 .NET 值有条件地呈现。 如果值为`false`或`null`, 则不呈现特性。 如果该值为`true`, 则以最小化的特性呈现特性。

在下面的示例中`IsCompleted` , `checked`确定是否在元素的标记中呈现:

```cshtml
<input type="checkbox" checked="@IsCompleted" />

@code {
    [Parameter]
    public bool IsCompleted { get; set; }
}
```

如果`IsCompleted` 为`true`, 则复选框将呈现为:

```html
<input type="checkbox" checked />
```

如果`IsCompleted` 为`false`, 则复选框将呈现为:

```html
<input type="checkbox" />
```

有关详细信息，请参阅 <xref:mvc/views/razor> 。

## <a name="raw-html"></a>原始 HTML

通常使用 DOM 文本节点呈现字符串, 这意味着将忽略它们可能包含的任何标记, 并将其视为文本文本。 若要呈现原始 html, 请在`MarkupString`值中换行 html 内容。 该值将分析为 HTML 或 SVG, 并插入 DOM 中。

> [!WARNING]
> 呈现从任何不受信任的源构造的原始 HTML 会**带来安全风险**, 应避免!

下面的示例演示如何使用`MarkupString`类型向组件的呈现输出添加静态 HTML 内容块:

```html
@((MarkupString)myMarkup)

@code {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>模板化组件

模板化组件是接受一个或多个 UI 模板作为参数的组件, 可将其用作组件呈现逻辑的一部分。 模板化组件允许你创作比常规组件更易于使用的更高级别的组件。 几个示例包括:

* 允许用户为表的标头、行和脚注指定模板的表组件。
* 允许用户在列表中指定用于呈现项的模板的列表组件。

### <a name="template-parameters"></a>模板参数

通过指定一个或多个类型`RenderFragment`为或`RenderFragment<T>`的组件参数来定义模板化组件。 呈现片段表示要呈现的 UI 段。 `RenderFragment<T>`采用可在调用呈现片段时指定的类型参数。

`TableTemplate`组件

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.razor)]

使用模板化组件时, 可以使用与参数名称匹配的子元素 (`TableHeader` `RowTemplate`在以下示例中) 指定模板参数:

```cshtml
<TableTemplate Items="@pets">
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

作为元素传递的`RenderFragment<T>`类型的组件参数具有一个名为`context`的隐式参数 (如前面`@context.PetId`的代码示例所示), 但你可以使用子上`Context`的属性更改参数名称element. 在下面的示例中, `RowTemplate`元素的`Context`属性指定`pet`参数:

```cshtml
<TableTemplate Items="@pets">
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

或者, 您可以在 component `Context`元素上指定属性。 指定`Context`的特性应用于所有指定的模板参数。 如果要为隐式子内容指定内容参数名称 (不包含任何换行子元素), 这会很有用。 在下面的示例中, `Context`属性出现`TableTemplate`在元素上, 并应用于所有模板参数:

```cshtml
<TableTemplate Items="@pets" Context="pet">
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

模板化组件通常是通用类型。 例如, 泛型`ListViewTemplate`组件可用于呈现`IEnumerable<T>`值。 若要定义一般组件, 请使用`@typeparam`指令指定类型参数:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.razor)]

当使用泛型类型的组件时, 将在可能的情况下推断类型参数:

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

否则, 必须使用与类型参数的名称匹配的属性显式指定 type 参数。 在下面的示例中`TItem="Pet"` , 指定类型:

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>级联值和参数

在某些情况下, 使用[组件参数](#component-parameters)将数据从祖先组件流式传输到附属组件是不方便的, 尤其是在有多个组件层时。 级联值和参数通过提供一种方便的方法, 使上级组件为其所有子代组件提供值。 级联值和参数还提供了一种方法来协调组件。

### <a name="theme-example"></a>主题示例

在下面的示例应用程序示例中, `ThemeInfo`类指定要向下传递组件层次结构的主题信息, 以便应用程序中给定部分内的所有按钮共享相同样式。

*UIThemeClasses/ThemeInfo*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

祖先组件可以使用级联值组件提供级联值。 `CascadingValue`组件包装组件层次结构的子树, 并向该子树内的所有组件提供单个值。

例如, 示例应用程序将应用程序的一个`ThemeInfo`布局中的主题信息 () 指定为构成`@Body`属性布局正文的所有组件的级联参数。 `ButtonClass`为指定了布局组件`btn-success`中的值。 任何子代组件都可以通过`ThemeInfo`级联对象使用此属性。

`CascadingValuesParametersLayout`组件

```cshtml
@inherits LayoutComponentBase
@using BlazorSample.UIThemeClasses

<div class="container-fluid">
    <div class="row">
        <div class="col-sm-3">
            <NavMenu />
        </div>
        <div class="col-sm-9">
            <CascadingValue Value="@theme">
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

若要使用级联值, 组件将使用`[CascadingParameter]`属性或基于字符串名称值声明级联参数:

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

如果有多个相同类型的级联值并且需要在同一子树内区分它们, 则与字符串名称值的绑定是相关的。

级联值按类型绑定到级联参数。

在示例应用中, 该`CascadingValuesParametersTheme`组件将`ThemeInfo`级联值绑定到级联参数。 参数用于设置由组件显示的一个按钮的 CSS 类。

`CascadingValuesParametersTheme`组件

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

### <a name="tabset-example"></a>TabSet 示例

级联参数还允许组件跨组件层次结构进行协作。 例如, 请看下面的示例应用中的*TabSet*示例。

该示例应用程序具有`ITab`选项卡实现的接口:

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

组件使用包含若干`TabSet` `Tab`组件的组件: `CascadingValuesParametersTabSet`

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.razor?name=snippet_TabSet)]

子`Tab`组件不会显式作为参数传递`TabSet`给。 子`Tab`组件是的子内容`TabSet`的一部分。 但是, `TabSet`仍然需要了解每个`Tab`组件, 以便能够呈现标头和活动的选项卡。若要启用此协调而不需要额外的`TabSet`代码, 组件*可将自身作为级联值提供*, 然后由子代`Tab`组件选取。

`TabSet`组件

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.razor)]

子代`Tab`组件将包含`TabSet`的作为`Tab`级联参数捕获, 因此组件会将其自身添加`TabSet`到中, 并协调选项卡处于活动状态。

`Tab`组件

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.razor)]

## <a name="razor-templates"></a>Razor 模板

可以使用 Razor 模板语法来定义呈现片段。 Razor 模板是一种定义 UI 代码段并采用以下格式的一种方法:

```cshtml
@<{HTML tag}>...</{HTML tag}>
```

下面的示例演示如何在组件`RenderFragment`中`RenderFragment<T>`直接指定和值和呈现模板。 呈现片段还可以作为参数传递给[模板化组件](#templated-components)。

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

前面的代码的呈现输出:

```html
<p>The time is 10/04/2018 01:26:52.</p>

<p>Your pet's name is Rex.</p>
```

## <a name="manual-rendertreebuilder-logic"></a>手动 RenderTreeBuilder 逻辑

`Microsoft.AspNetCore.Components.RenderTree`提供操作组件和元素的方法, 包括在代码中C#手动生成组件。

> [!NOTE]
> `RenderTreeBuilder`使用创建组件是一种高级方案。 格式不正确的组件 (例如, 未闭合的标记标记) 可能会导致未定义的行为。

请考虑以下`PetDetails`组件, 该组件可以手动内置到另一个组件中:

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote</p>

@code
{
    [Parameter]
    public string PetDetailsQuote { get; set; }
}
```

在下面的示例中, `CreateComponent`方法中的循环生成三个`PetDetails`组件。 当调用`RenderTreeBuilder`方法来创建组件 (`OpenComponent`和`AddAttribute`) 时, 序列号是源代码行号。 Blazor 差异算法依赖于与不同代码行对应的序列号, 而不是不同的调用调用。 使用`RenderTreeBuilder`方法创建组件时, 将序列号的参数硬编码。 **使用计算或计数器生成序列号会导致性能不佳。** 有关详细信息, 请参阅 "[序列号与代码行号和非执行顺序相关](#sequence-numbers-relate-to-code-line-numbers-and-not-execution-order)" 部分。

`BuiltContent`组件

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

### <a name="sequence-numbers-relate-to-code-line-numbers-and-not-execution-order"></a>序列号与代码行号相关, 而不是与执行顺序相关

始终`.razor`编译 Blazor 文件。 这可能是一个很好的`.razor`优点, 因为编译步骤可用于注入在运行时提高应用性能的信息。

这些改进涉及到*序列号*的主要示例。 序列号指示运行时输出来自代码的不同和有序行。 运行时使用此信息以线性时间生成有效的树差异, 其速度远快于一般树差异算法通常可以实现的速度。

请考虑以下简单`.razor`文件:

```cshtml
@if (someFlag)
{
    <text>First</text>
}

Second
```

前面的代码编译为类似于下面的内容:

```csharp
if (someFlag)
{
    builder.AddContent(0, "First");
}

builder.AddContent(1, "Second");
```

当第一次执行代码时, `someFlag` `true`生成器将接收:

| 序列 | 类型      | Data   |
| :------: | --------- | :----: |
| 0        | Text 节点 | 第一个  |
| 1        | Text 节点 | 第二个 |

假设它`someFlag`变为`false`, 并再次呈现标记。 这次, 生成器将接收:

| 序列 | 类型       | Data   |
| :------: | ---------- | :----: |
| 1        | Text 节点  | 第二个 |

当运行时执行差异时, 它会发现序列`0`中的项已被删除, 因此它生成以下简单的*编辑脚本*:

* 删除第一个文本节点。

#### <a name="what-goes-wrong-if-you-generate-sequence-numbers-programmatically"></a>如果以编程方式生成序列号, 会出现什么错误

假设你编写了以下呈现树生成器逻辑:

```csharp
var seq = 0;

if (someFlag)
{
    builder.AddContent(seq++, "First");
}

builder.AddContent(seq++, "Second");
```

现在, 第一个输出是:

| 序列 | 类型      | Data   |
| :------: | --------- | :----: |
| 0        | Text 节点 | 第一个  |
| 1        | Text 节点 | 第二个 |

此结果与以前的情况相同, 因此不存在负面问题。 `someFlag``false`在第二次呈现时, 输出为:

| 序列 | 类型      | Data   |
| :------: | --------- | ------ |
| 0        | Text 节点 | 第二个 |

这次, 比较算法会发现*两个*更改已发生, 并且算法将生成以下编辑脚本:

* 将第一个文本节点的值更改为`Second`。
* 删除第二个文本节点。

生成序列号丢失了有关`if/else`分支和循环在原始代码中的位置的所有有用信息。 这会导致**两次**比较, 就像以前一样。

这是一个简单的示例。 在具有复杂、深度嵌套结构的更真实的情况下, 特别是在循环中, 性能开销更严重。 比较算法不必立即确定哪些循环块或分支已插入或删除, 而是必须将其深入地递归到呈现树, 并且通常会生成更长的编辑脚本, 因为它 misinformed 了新的和新的结构彼此关联。

#### <a name="guidance-and-conclusions"></a>指导和结论

* 如果动态生成了序列号, 则应用程序性能会受到影响。
* 框架无法在运行时自动创建自己的序列号, 因为所需信息不存在, 除非它在编译时捕获。
* 不要编写长时间的手动实现`RenderTreeBuilder`逻辑。 首选`.razor`文件, 并允许编译器处理序列号。
* 如果对序列号进行硬编码, 则 diff 算法只要求序列号的值增加。 初始值和间隙无关。 一个合法选项是将代码行号用作序列号, 或从零开始, 并按一个或数百个 (或任何首选间隔) 递增。 
* Blazor 使用序列号, 而其他树比较的 UI 框架不使用序列号。 使用序列号时, 比较速度要快得多, 并且 Blazor 具有用于为开发人员创作`.razor`文件自动处理序列号的编译步骤。

## <a name="localization"></a>本地化

Blazor 服务器端应用使用[本地化中间件](xref:fundamentals/localization#localization-middleware)进行本地化。 中间件为从应用程序请求资源的用户选择相应的区域性。

可以使用以下方法之一设置区域性:

* [Cookie](#cookies)
* [提供用于选择区域性的 UI](#provide-ui-to-choose-the-culture)

有关更多信息和示例，请参见<xref:fundamentals/localization>。

### <a name="cookies"></a>Cookie

本地化区域性 cookie 可以保存用户的区域性。 Cookie 是通过`OnGet`应用程序主机页 (*Pages/host. .cs*) 的方法创建的。 本地化中间件会在后续请求上读取 cookie, 以设置用户的区域性。 

使用 cookie 可确保 WebSocket 连接可以正确地传播区域性。 如果本地化方案基于 URL 路径或查询字符串, 则该方案可能无法与 Websocket 一起使用, 因此无法持久保存区域性。 因此, 建议使用本地化区域性 cookie。

如果在本地化 cookie 中保留了区域性, 则可使用任何方法来分配区域性。 如果应用已建立服务器端 ASP.NET Core 的本地化方案, 请继续使用应用的现有本地化基础结构, 并在应用方案中设置本地化区域性 cookie。

下面的示例演示如何在可由本地化中间件读取的 cookie 中设置当前区域性。 在 Blazor 服务器端应用中创建包含以下内容的*页面/主机 .cs*文件:

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

在应用程序中处理本地化:

1. 浏览器将初始 HTTP 请求发送到应用程序。
1. 区域性由本地化中间件分配。
1. _Host `OnGet`中的方法将区域性作为响应的一部分保留在 cookie 中。
1. 浏览器将打开 WebSocket 连接以创建交互式 Blazor 服务器端会话。
1. 本地化中间件读取 cookie 并分配区域性。
1. Blazor 服务器端会话以正确的区域性开头。

## <a name="provide-ui-to-choose-the-culture"></a>提供用于选择区域性的 UI

为了提供允许用户选择区域性的 UI, 建议使用*基于重定向的方法*。 此过程类似于用户尝试访问安全资源&mdash;时在 web 应用中发生的情况, 用户将重定向到登录页, 然后重定向回原始资源。 

应用程序通过重定向到控制器来持久保存用户的所选区域性。 控制器将用户选定的区域性设置为 cookie, 并将用户重定向回原始 URI。

在服务器上创建一个 HTTP 终结点, 以在 cookie 中设置用户的所选区域性, 并执行重定向回原始 URI:

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
> `LocalRedirect`使用操作结果可防止开放重定向攻击。 有关详细信息，请参阅 <xref:security/preventing-open-redirects> 。

以下组件显示了一个示例, 说明如何在用户选择区域性时执行初始重定向:

```cshtml
@inject IUriHelper UriHelper

<h3>Select your language</h3>

<select @onchange="OnSelected">
    <option>Select...</option>
    <option value="en-US">English</option>
    <option value="fr-FR">Français</option>
</select>

@code {
    private double textNumber;

    private void OnSelected(UIChangeEventArgs e)
    {
        var culture = (string)e.Value;
        var uri = new Uri(UriHelper.GetAbsoluteUri())
            .GetComponents(UriComponents.PathAndQuery, UriFormat.Unescaped);
        var query = $"?culture={Uri.EscapeDataString(culture)}&" +
            $"redirectUri={Uri.EscapeDataString(uri)}";

        UriHelper.NavigateTo("/Culture/SetCulture" + query, forceLoad: true);
    }
}
```

### <a name="use-net-localization-scenarios-in-blazor-apps"></a>在 Blazor 应用中使用 .NET 本地化方案

在 Blazor 应用程序中, 可以使用以下 .NET 本地化和全球化方案:

* .网络资源系统
* 区域性特定的数字和日期格式设置

Blazor 的`@bind`功能基于用户的当前区域性执行全球化。 有关详细信息, 请参阅[数据绑定](#data-binding)部分。

目前支持有限的一组 ASP.NET Core 本地化方案:

* `IStringLocalizer<>`在 Blazor 应用中*受支持*。
* `IHtmlLocalizer<>`、 `IViewLocalizer<>`和数据批注本地化 ASP.NET Core MVC 方案, 在 Blazor 应用中**不受支持**。

有关详细信息，请参阅 <xref:fundamentals/localization> 。
