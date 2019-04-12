---
title: 创建和使用 Razor 组件
author: guardrex
description: 了解如何创建和使用 Razor 组件，包括如何将绑定到数据、 处理事件，以及管理组件生命周期。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/08/2019
uid: razor-components/components
ms.openlocfilehash: f8ac7f3844b94a162e8d1c45f80ae153d89536ee
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59515437"
---
# <a name="create-and-use-razor-components"></a>创建和使用 Razor 组件

通过[Luke Latham](https://github.com/guardrex)， [Daniel Roth](https://github.com/danroth27)，和[Morné Zaayman](https://github.com/MorneZaayman)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/)（[如何下载](xref:index#how-to-download-a-sample)）。 有关先决条件，请参阅[入门](xref:razor-components/get-started)主题。

使用生成 razor 组件应用*组件*。 组件是自包含的块的用户界面 (UI)，如页、 对话框中或窗体。 一个组件，包括 HTML 标记和将数据注入或响应 UI 事件所需的处理逻辑。 组件是灵活、 轻量。 可以嵌套，重复使用，并在项目间共享。

## <a name="component-classes"></a>组件类

组件通常在 Razor 组件文件中实现 (*.razor*) 组合使用C#和 HTML 标记 (*.cshtml* Blazor 应用中使用文件)。

可以在 Razor 组件使用的应用程序中编写组件 *.cshtml*文件扩展名，只要这些文件被标识为 Razor 组件文件使用`_RazorComponentInclude`MSBuild 属性。 例如，使用 Razor 组件模板创建的应用指定的所有 *.cshtml*文件下*组件*文件夹应被视为 Razor 组件文件：

```xml
<_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
```

使用 HTML 定义组件的 UI。 动态呈现逻辑（例如，循环、条件、表达式）是使用名为 [Razor](xref:mvc/views/razor) 的嵌入式 C# 语法添加的。 当编译 Razor 组件的应用程序、 HTML 标记和C#呈现逻辑转换为 component 类。 生成的类的名称匹配的文件的名称。

在中定义的 component 类成员`@functions`块 (多个`@functions`块是允许)。 在`@functions`块中，组件状态 （属性、 字段） 指定与事件处理或定义其他组件的逻辑的方法。

组件成员然后，可以使用组件的一部分的呈现逻辑使用如C#表达式的开头`@`。 例如，C#通过前缀来呈现字段`@`对字段名称。 下面的示例的计算结果并呈现：

* `_headingFontStyle` 为的 CSS 属性值`font-style`。
* `_headingText` 内容`<h1>`元素。

```cshtml
<h1 style="font-style:@_headingFontStyle">@_headingText</h1>

@functions {
    private string _headingFontStyle = "italic";
    private string _headingText = "Put on your new Blazor!";
}
```

最初呈现该组件后，将组件重新生成其呈现树以响应事件。 Razor 组件然后将对上一个新的呈现树进行比较，并应用到浏览器的文档对象模型 (DOM) 的任何修改。

## <a name="integrate-components-into-razor-pages-and-mvc-apps"></a>将组件集成到 Razor 页面和 MVC 应用

使用与现有的 Razor 页面和 MVC 应用程序组件。 没有无需重写现有页面或视图，以使用 Razor 组件。 呈现的页面或视图时，组件是预呈现&dagger;在同一时间。 

> [!NOTE]
> &dagger;默认情况下，服务器端预呈现启用 Razor 组件应用。 客户端 Blazor 应用将在即将发布的预览版 4 版本中支持预呈现。 有关详细信息，请参阅[更新模板/中间件使用 MapFallbackToPage/文件](https://github.com/aspnet/AspNetCore/issues/8852)。

若要呈现的页面或视图中的组件，使用`RenderComponentAsync<TComponent>`HTML 帮助器方法：

```cshtml
<div id="Counter">
    @(await Html.RenderComponentAsync<Counter>(new { IncrementAmount = 10 }))
</div>
```

呈现页面和视图组件尚不可在预览版 3 发布交互式。 例如，选择一个按钮不会触发方法调用。 将来的预览版将解除此限制，并添加对呈现组件使用正常的元素和属性语法的支持。

虽然页面和视图可以使用组件，相反说法不正确。 组件不能使用视图和页面特定方案，如分部视图和部分。 若要使用到组件的逻辑从组件中的分部视图、 分部视图逻辑的身份。

## <a name="using-components"></a>使用组件

组件可以通过声明它们中包括其他组件使用 HTML 元素的语法。 使用组件的标记类似于 HTML 标记，其中标记的名称是组件类型。

以下标记呈现`HeadingComponent`(*HeadingComponent.cshtml*) 实例：

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/Index.cshtml?name=snippet_HeadingComponent)]

## <a name="component-parameters"></a>组件参数

组件可以具有*组件参数*，这使用定义*非公共*使用修饰组件类上的属性， `[Parameter]`。 使用这些属性在标记中为组件指定参数。

在以下示例中，`ParentComponent`设置的值`Title`属性的`ChildComponent`:

*ParentComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=5)]

*ChildComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=7-8)]

## <a name="child-content"></a>子内容

组件可以设置另一个组件的内容。 分配的组件提供标记，用于指定接收组件之间的内容。 例如，`ParentComponent`可以提供内容呈现子组件上来将中的内容`<ChildComponent>`标记。

*ParentComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ParentComponent.cshtml?name=snippet_ParentComponent&highlight=6-7)]

子组件具有`ChildContent`属性表示`RenderFragment`。 值`ChildContent`定位子组件的标记中来呈现内容的位置。 在下面的示例中，值`ChildContent`收到来自父组件和 Bootstrap 面板内呈现`panel-body`。

*ChildComponent.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/ChildComponent.cshtml?highlight=3,10-11)]

> [!NOTE]
> 属性接收`RenderFragment`内容必须命名为`ChildContent`按照约定。

## <a name="data-binding"></a>数据绑定

可以使用数据绑定到组件和 DOM 元素来完成`bind`属性。 以下示例将绑定`ItalicsCheck`属性设置为复选框的选中状态：

```cshtml
<input type="checkbox" class="form-check-input" id="italicsCheck" 
    bind="@_italicsCheck">
```

如果复选框被选中，然后清除，该属性的值将更新为`true`和`false`分别。

仅当呈现该组件时，不在响应不断变化的属性的值时，将在 UI 中更新复选框。 由于组件自身的呈现事件处理程序代码执行后，属性更新是通常立即反映在用户界面。

使用`bind`与`CurrentValue`属性 (`<input bind="@CurrentValue">`) 实质上等同于以下：

```cshtml
<input value="@CurrentValue" 
    onchange="@((UIChangeEventArgs __e) => CurrentValue = __e.Value)">
```

当呈现该组件时，`value`输入元素的来自`CurrentValue`属性。 当用户在文本框中，键入`onchange`触发事件和`CurrentValue`属性设置为已更改的值。 在实际情况下，代码生成是一种较为复杂，因为`bind`处理少数情况下，将执行类型转换。 原则上，`bind`将具有的表达式的当前值相关联`value`使用已注册处理程序的属性和处理更改。

除了`onchange`，可以使用其他事件，例如绑定的属性`oninput`更清楚地了解要将绑定到内容的方式：

```cshtml
<input type="text" bind-value-oninput="@CurrentValue">
```

与不同`onchange`，`oninput`文本框中输入的每个字符时激发。

**格式字符串**

数据绑定的工作方式与<xref:System.DateTime>格式字符串。 其他格式的表达式，如货币或数字格式，此时不可用。

```cshtml
<input bind="@StartDate" format-value="yyyy-MM-dd">

@functions {
    [Parameter]
    private DateTime StartDate { get; set; } = new DateTime(2020, 1, 1);
}
```

`format-value`属性指定要应用到的日期格式`value`的`input`元素。 此外使用的格式将值解析时`onchange`事件发生。

**组件参数**

绑定还可识别组件参数，其中`bind-{property}`可以跨组件绑定的属性值。

以下组件使用`ChildComponent`，并将绑定`ParentYear`参数是从父级到`Year`的子组件的参数：

父组件：

```cshtml
@page "/ParentComponent"

<h1>Parent Component</h1>

<p>ParentYear: @ParentYear</p>

<ChildComponent bind-Year="@ParentYear" />

<button class="btn btn-primary" onclick="@ChangeTheYear">
    Change Year to 1986
</button>

@functions {
    [Parameter]
    private int ParentYear { get; set; } = 1978;

    private void ChangeTheYear()
    {
        ParentYear = 1986;
    }
}
```

子组件：

```cshtml
<h2>Child Component</h2>

<p>Year: @Year</p>

@functions {
    [Parameter]
    private int Year { get; set; }

    [Parameter]
    private Action<int> YearChanged { get; set; }
}
```

正在加载`ParentComponent`生成以下标记：

```html
<h1>Parent Component</h1>

<p>ParentYear: 1978</p>

<h2>Child Component</h2>

<p>Year: 1978</p>
```

如果的值`ParentYear`通过选择中的按钮更改属性`ParentComponent`，则`Year`属性的`ChildComponent`更新。 新值`Year`在 UI 中显示时`ParentComponent`重新呈现：

```html
<h1>Parent Component</h1>

<p>ParentYear: 1986</p>

<h2>Child Component</h2>

<p>Year: 1986</p>
```

`Year`参数是可绑定，因为它具有一个辅助性`YearChanged`相匹配的类型的事件`Year`参数。

按照约定，`<ChildComponent bind-Year="@ParentYear" />`实质上等同于写入操作，

```cshtml
    <ChildComponent bind-Year-YearChanged="@ParentYear" />
```

一般情况下，将属性绑定到相应的事件处理程序使用`bind-property-event`属性。

## <a name="event-handling"></a>事件处理

Razor 组件提供的事件处理功能。 HTML 元素特性名为`on<event>`(例如， `onclick`， `onsubmit`) 与委托类型的值，Razor 组件属性的值将视为一个事件处理程序。 该属性的名称始终以开头`on`。

下面的代码调用`UpdateHeading`方法在 UI 中选择该按钮时：

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private void UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

下面的代码调用`CheckboxChanged`方法时在 UI 中更改该复选框：

```cshtml
<input type="checkbox" class="form-check-input" onchange="@CheckboxChanged">

@functions {
    private void CheckboxChanged()
    {
        ...
    }
}
```

事件处理程序也可以是异步和返回<xref:System.Threading.Tasks.Task>。 无需手动调用`StateHasChanged()`。 发生时，会记录异常。

```cshtml
<button class="btn btn-primary" onclick="@UpdateHeading">
    Update heading
</button>

@functions {
    private async Task UpdateHeading(UIMouseEventArgs e)
    {
        ...
    }
}
```

对于某些事件，允许使用特定于事件的事件参数类型。 如果对其中一种事件类型的访问不必要的它无需在方法调用中。

支持的事件自变量的列表是：

* UIEventArgs
* UIChangeEventArgs
* UIKeyboardEventArgs
* UIMouseEventArgs

此外可以使用 lambda 表达式：

```cshtml
<button onclick="@(e => Console.WriteLine("Hello, world!"))">Say hello</button>
```

通常很方便地通过其他值，如关闭时循环访问一组元素。 下面的示例创建三个按钮的它调用的每个`UpdateHeading`传递的事件自变量 (`UIMouseEventArgs`)，以及它按钮编号 (`buttonNumber`) 时在 UI 中选择：

```cshtml
<h2>@message</h2>

@for (var i = 1; i < 4; i++)
{
    var buttonNumber = i;

    <button class="btn btn-primary"
            onclick="@(e => UpdateHeading(e, buttonNumber))">
        Button #@i
    </button>
}

@functions {
    private string message = "Select a button to learn its position.";

    private void UpdateHeading(UIMouseEventArgs e, int buttonNumber)
    {
        message = $"You selected Button #{buttonNumber} at " +
            "mouse position: {e.ClientX} X {e.ClientY}.";
    }
}
```

> [!NOTE]
> 不要**不**使用循环变量 (`i`) 中`for`直接在 lambda 表达式中的循环。 否则相同的变量可供所有 lambda 表达式导致`i`要在所有 lambda 是相同的值。 始终捕获本地变量中的其值 (`buttonNumber`在前面的示例)，然后使用它。

## <a name="capture-references-to-components"></a>捕获对组件的引用

组件的引用提供的方式获取对组件实例的引用，以便您可以发出命令到该实例，如`Show`或`Reset`。 若要捕获的组件引用，请添加`ref`属性到子组件，然后将具有相同名称和相同类型的字段定义为子组件。

```cshtml
<MyLoginDialog ref="loginDialog" ... />

@functions {
    private MyLoginDialog loginDialog;

    private void OnSomething()
    {
        loginDialog.Show();
    }
}
```

当呈现该组件时，`loginDialog`字段填充`MyLoginDialog`子组件实例。 然后可以调用组件实例上的.NET 方法。

> [!IMPORTANT]
> `loginDialog`呈现该组件并包括其输出后，仅填充变量`MyLoginDialog`元素。 该点，直到没有要引用。 若要操作组件的引用，该组件已经完成呈现后，使用`OnAfterRenderAsync`或`OnAfterRender`方法。

而捕获组件引用使用到类似的语法[捕获元素引用](xref:razor-components/javascript-interop#capture-references-to-elements)，它不是[JavaScript 互操作](xref:razor-components/javascript-interop)功能。 组件的引用不会传递给 JavaScript 代码;它们仅用于.NET 代码中。

> [!NOTE]
> 不要**不**组件引用用于改变子组件的状态。 相反，使用普通的声明性参数将数据传递给子组件。 这将导致子组件来自动 rerender 在正确的时间。

## <a name="lifecycle-methods"></a>生命周期方法

`OnInitAsync` 和`OnInit`执行代码以初始化该组件。 若要执行异步操作，请使用`OnInitAsync`和`await`关键字的操作：

```csharp
protected override async Task OnInitAsync()
{
    await ...
}
```

为同步操作，使用`OnInit`:

```csharp
protected override void OnInit()
{
    ...
}
```

`OnParametersSetAsync` 和`OnParametersSet`组件已收到来自其父级的参数和值分配给属性时会调用。 组件初始化后执行这些方法，然后每次呈现该组件是：

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

`OnAfterRenderAsync` 和`OnAfterRender`组件已完成呈现后调用。 此时即会填充元素和组件的引用。 使用此阶段执行附加的初始化步骤中使用的呈现的内容，如激活对呈现的 DOM 元素的第三方 JavaScript 库。

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

`SetParameters` 可以重写以执行代码之前设置参数：

```csharp
public override void SetParameters(ParameterCollection parameters)
{
    ...

    base.SetParameters(parameters);
}
```

如果`base.SetParameters`不是调用，自定义代码可以解释中任何方式所需的传入参数值。 例如，传入参数无需分配给类的属性。

`ShouldRender` 可以重写以禁止显示 UI 的刷新。 如果该实现返回`true`，刷新用户界面。 即使`ShouldRender`是被重写时，务必使最初呈现该组件。

```csharp
protected override bool ShouldRender()
{
    var renderUI = true;

    return renderUI;
}
```

## <a name="component-disposal-with-idisposable"></a>使用 IDisposable 组件可供使用

如果组件实现<xref:System.IDisposable>，则[Dispose 方法](/dotnet/standard/garbage-collection/implementing-dispose)从 UI 中移除该组件时调用。 以下组件使用`@implements IDisposable`和`Dispose`方法：

```csharp
@using System
@implements IDisposable

...

@functions {
    public void Dispose()
    {
        ...
    }
}
```

## <a name="routing"></a>路由

Razor 组件中的路由是通过提供对应用程序中每个可访问组件的路由模板实现的。

当 *.cshtml*文件具有`@page`编译指令，则生成的类提供<xref:Microsoft.AspNetCore.Mvc.RouteAttribute>指定路由模板。 在运行时，路由器将查看与组件类`RouteAttribute`并呈现任何组件有匹配的请求的 URL 的路由模板。

多个路由模板可以应用于一个组件。 以下组件响应的请求`/BlazorRoute`和`/DifferentBlazorRoute`:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRoute.cshtml?name=snippet_BlazorRoute)]

## <a name="route-parameters"></a>路由参数

组件可以接收来自路由模板中提供的路由参数`@page`指令。 路由器使用路由参数来填充相应的组件参数。

*RouteParameter.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/RouteParameter.cshtml?name=snippet_RouteParameter)]

不支持可选参数，因此两个`@page`指令收取在上面的示例。 第一个可以导航到不带参数的组件。 第二个`@page`指令采用`{text}`路由参数和值赋给`Text`属性。

## <a name="base-class-inheritance-for-a-code-behind-experience"></a>"代码隐藏"体验的基类继承

组件文件 (*.cshtml*) 混合使用 HTML 标记和C#处理同一文件中的代码。 `@inherits`指令可用于向 Razor 组件应用提供一种"代码隐藏"体验，将组件标记与处理代码分隔开来。

[示例应用](https://github.com/aspnet/Docs/tree/master/aspnetcore/razor-components/common/samples/)显示了如何一个组件可以继承一个基类， `BlazorRocksBase`，以提供该组件的属性和方法。

*BlazorRocks.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/BlazorRocks.cshtml?name=snippet_BlazorRocks)]

*BlazorRocksBase.cs*:

[!code-csharp[](common/samples/3.x/BlazorSample/Pages/BlazorRocksBase.cs)]

应派生自的基类`ComponentBase`。

## <a name="razor-support"></a>Razor 支持

**Razor 指令**

Razor 指令表所示。

| 指令 | 描述 |
| --------- | ----------- |
| [\@函数](xref:mvc/views/razor#section-5) | 添加C#到组件的代码块。 |
| `@implements` | 实现生成的组件类的接口。 |
| [\@继承](xref:mvc/views/razor#section-3) | 提供该组件继承的类的完全控制。 |
| [\@inject](xref:mvc/views/razor#section-4) | 允许服务从注入[服务容器](xref:fundamentals/dependency-injection)。 有关详细信息，请参阅[视图中的依赖关系注入](xref:mvc/views/dependency-injection)。 |
| `@layout` | 指定布局组件。 布局组件用于避免代码重复和不一致。 |
| [\@page](xref:razor-pages/index#razor-pages) | 指定该组件应直接处理请求。 `@page`指令可指定路由参数和可选参数。 Razor 页面与`@page`指令不需要是在文件顶部的第一个指令。 有关详细信息，请参阅[路由](xref:razor-components/routing)。 |
| [\@使用](xref:mvc/views/razor#using) | 添加C#`using`指令生成的组件类。 |
| [\@addTagHelper](xref:mvc/views/razor#tag-helpers) | 使用`@addTagHelper`比应用程序的程序集的不同程序集中使用的组件。 |

**条件属性**

基于.NET 值有条件地呈现属性。 如果值为`false`或`null`，该属性不会呈现。 如果值为`true`，呈现该属性最小化。

在以下示例中，`IsCompleted`确定如果`checked`呈现控件的标记中：

```cshtml
<input type="checkbox" checked="@IsCompleted">

@functions {
    [Parameter]
    private bool IsCompleted { get; set; }
}
```

如果`IsCompleted`是`true`，复选框将呈现为：

```html
<input type="checkbox" checked>
```

如果`IsCompleted`是`false`，复选框将呈现为：

```html
<input type="checkbox">
```

**Razor 的其他信息**

Razor 的详细信息，请参阅[Razor 语法参考](xref:mvc/views/razor)。

## <a name="raw-html"></a>原始 HTML

字符串通常使用呈现文本的 DOM 节点，这意味着它们可能包含任何标记被忽略，视为文字文本。 若要呈现原始 HTML，包装中的 HTML 内容`MarkupString`值。 分析为 HTML 或 SVG 值并将其插入到 dom。

> [!WARNING]
> 呈现的原始 HTML 构造从任何不受信任的源是**安全风险**，应避免使用 ！

下面的示例演示使用`MarkupString`要静态 HTML 内容的块添加到呈现的输出的组件类型：

```html
@((MarkupString)myMarkup)

@functions {
    private string myMarkup = 
        "<p class='markup'>This is a <em>markup string</em>.</p>";
}
```

## <a name="templated-components"></a>模板化组件

模板化组件是接受一个或多个 UI 模板作为参数，然后可以使用的组件的呈现逻辑一部分的组件。 模板化组件允许您编写更高级别的更常规的组件可重用的组件。 几个示例包括：

* 允许用户指定的表的标头、 行和页脚模板的表组件。
* 允许用户在列表中指定用于呈现项的模板的列表组件。

### <a name="template-parameters"></a>模板参数

通过指定类型的一个或多个组件参数来定义模板化组件`RenderFragment`或`RenderFragment<T>`。 呈现片段表示 UI 呈现组件的一个段。 呈现片段 （可选） 使用调用呈现段时可以指定一个参数。

*Components/TableTemplate.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TableTemplate.cshtml)]

在使用模板化组件时，可以使用与匹配的参数的名称的子元素指定模板参数 (`TableHeader`和`RowTemplate`在下面的示例):

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

组件类型的自变量`RenderFragment<T>`元素具有名为的隐式参数传递`context`(例如，从前面的代码示例中， `@context.PetId`)，但您可以更改参数名称使用`Context`在子活动的属性元素。 在以下示例中，`RowTemplate`元素的`Context`特性指定`pet`参数：

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

或者，可以指定`Context`组件元素的特性。 指定`Context`特性应用于所有指定的模板参数。 当你想要指定隐式子内容的内容的参数名称时这很有用 (而无需任何包装子元素)。 在以下示例中，`Context`属性将会显示在`TableTemplate`元素并将应用于所有模板参数：

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

### <a name="generic-typed-components"></a>泛型类型的组件

模板化组件通常以一般方式类型化。 例如，泛型列表视图模板组件可用于呈现`IEnumerable<T>`值。 若要定义的通用组件，请使用`@typeparam`指令，用于指定类型参数。

*Components/ListViewTemplate.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/ListViewTemplate.cshtml)]

当使用泛型类型的组件，如有可能推断类型参数：

```cshtml
<ListViewTemplate Items="@pets">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

否则，类型参数必须显式指定使用匹配的类型参数名称的属性。 在以下示例中，`TItem="Pet"`指定的类型：

```cshtml
<ListViewTemplate Items="@pets" TItem="Pet">
    <ItemTemplate Context="pet">
        <li>@pet.Name</li>
    </ItemTemplate>
</ListViewTemplate>
```

## <a name="cascading-values-and-parameters"></a>级联值和参数

在某些情况下，很不方便将数据流从下级组件使用的祖先组件[组件参数](#component-parameters)，尤其是当存在多个组件层。 级联值和参数的上级组件提供到其所有下级组件的值的简便方法，从而解决此问题。 级联值和参数还提供组件进行协调的方法。

### <a name="theme-example"></a>主题的示例

在下面的示例*主题*示例应用中，从示例`ThemeInfo`类指定要沿组件层次结构向下传递，以便在应用程序的给定部分中的按钮的所有共享相同的样式的主题信息。

*UIThemeClasses/ThemeInfo.cs*:

```csharp
public class ThemeInfo
{
    public string ButtonClass { get; set; }
}
```

上级组件可以提供使用级联值组件的级联值。 级联值组件包装的组件层次结构的子树，并提供对该树内的所有组件的单个值。

例如，示例应用程序指定的主题信息 (`ThemeInfo`) 中的应用的布局的所有组件构成的布局部分的级联参数作为一个`@Body`属性。 `ButtonClass` 分配的值`btn-success`布局组件中。 任何下级组件可以使用此属性通过`ThemeInfo`级联的对象。

*Shared/CascadingValuesParametersLayout.cshtml*:

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

@functions {
    private ThemeInfo theme = new ThemeInfo { ButtonClass = "btn-success" };
}
```

若要使使用级联值中，组件声明使用级联参数`[CascadingParameter]`属性，或基于字符串名称值：

```cshtml
<CascadingValue Value=@PermInfo Name="UserPermissions">...</CascadingValue>

[CascadingParameter(Name = "UserPermissions")]
private PermInfo Permissions { get; set; }
```

如果具有相同类型的多个级联值并且需要区分相同的子树中，使用字符串名称值的绑定是相关。

级联值绑定的类型是为级联参数。

在示例应用中，级联值参数主题组件绑定到`ThemeInfo`级联级联参数值。 参数用于设置用于的一个由组件显示按钮的 CSS 类。

*Pages/CascadingValuesParametersTheme.cshtml*:

```cshtml
@page "/cascadingvaluesparameterstheme"
@layout CascadingValuesParametersLayout
@using BlazorSample.UIThemeClasses

<h1>Cascading Values & Parameters</h1>

<p>Current count: @currentCount</p>

<p>
    <button class="btn" onclick="@IncrementCount">
        Increment Counter (Unthemed)
    </button>
</p>

<p>
    <button class="btn @ThemeInfo.ButtonClass" onclick="@IncrementCount">
        Increment Counter (Themed)
    </button>
</p>

@functions {
    private int currentCount = 0;

    [CascadingParameter] protected ThemeInfo ThemeInfo { get; set; }

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

### <a name="tabset-example"></a>选项卡设置示例

级联参数还启用协作组件层次结构间的组件。 例如，请考虑以下*选项卡设置*示例应用程序中的示例。

示例应用有`ITab`选项卡实现的接口：

[!code-cs[](common/samples/3.x/BlazorSample/UIInterfaces/ITab.cs)]

级联值参数选项卡设置组件使用选项卡上设置的组件，其中包含多个选项卡组件：

[!code-cshtml[](common/samples/3.x/BlazorSample/Pages/CascadingValuesParametersTabSet.cshtml?name=snippet_TabSet)]

子选项卡组件不是显式作为参数传递到选项卡上设置。 相反，子选项卡组件是子内容的选项卡上设置的一部分。 但是，选项卡上设置仍需要了解有关每个选项卡组件，以便它可以呈现标头和活动选项卡。若要启用此协调，而无需其他代码，该选项卡上设置的组件*可以提供自身的级联值作为*，然后拾取后代的选项卡组件。

*Components/TabSet.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/TabSet.cshtml)]

子选项卡组件捕获包含选项卡集作为一个级联参数，因此选项卡组件将自己添加到选项卡上设置和坐标的选项卡上处于活动状态。

*Components/Tab.cshtml*:

[!code-cshtml[](common/samples/3.x/BlazorSample/Components/Tab.cshtml)]

## <a name="razor-templates"></a>Razor 模板

呈现可使用 Razor 模板语法定义片段。 Razor 模板是一种方法来定义 UI 代码段，并采用以下格式：

```cshtml
@<tag>...</tag>
```

下面的示例演示如何指定`RenderFragment`和`RenderFragment<T>`值。

*RazorTemplates.cshtml*:

```cshtml
@{
    RenderFragment template = @<p>The time is @DateTime.Now.</p>;
    RenderFragment<Pet> petTemplate = (pet) => @<p>Your pet's name is @pet.Name.</p>;
}
```

呈现使用的 Razor 模板可以作为参数传递给模板化组件或直接呈现定义的片段。 例如，使用以下 Razor 标记直接呈现上一个模板：

```cshtml
@template

@petTemplate(new Pet { Name = "Rex" })
```

呈现的输出：

```
The time is 10/04/2018 01:26:52.

Your pet's name is Rex.
```

## <a name="manual-rendertreebuilder-logic"></a>手动 RenderTreeBuilder 逻辑

`Microsoft.AspNetCore.Components.RenderTree` 提供用于操作组件和元素，其中包括中手动生成组件的方法C#代码。

> [!NOTE]
> 使用`RenderTreeBuilder`创建组件是一个高级的方案。 格式不正确的组件 （例如，未关闭的标记） 可能会导致未定义的行为。

请考虑以下宠物的详细信息组件 (*PetDetails.razor* Razor 组件; 中*PetDetails.cshtml* Blazor 中)，这可以手动构建到另一个组件：

```cshtml
<h2>Pet Details Component</h2>

<p>@PetDetailsQuote<p>

@functions
{
    [Parameter]
    string PetDetailsQuote { get; set; }
}
```

在下面的示例中的循环`CreateComponent`方法将生成三个宠物的详细信息组件。 调用时`RenderTreeBuilder`方法创建的组件 (`OpenComponent`和`AddAttribute`)，序列号是源代码行号。 差异算法依赖于与非重复行代码，没有不同之处相对应的序列号 Razor 组件调用调用。 创建具有组件时`RenderTreeBuilder`方法、 进行硬编码的序列号的参数。 **使用计算或计数器生成的序列号可能会导致性能不佳。**

构建的组件 (*BuiltContent.razor* Razor 组件; 中*BuiltContent.cshtml* Blazor 中):

```cshtml
@page "/BuiltContent"

<h1>Build a component</h1>

@CustomRender

<button type="button" onclick="@RenderComponent">
    Create three Pet Details components
</button>

@functions {
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
