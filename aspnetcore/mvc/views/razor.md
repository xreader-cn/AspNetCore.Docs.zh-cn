---
title: razor 语法参考 ASP.NET Core
author: rick-anderson
description: 了解将 Razor 基于服务器的代码嵌入网页的标记语法。
ms.author: riande
ms.date: 02/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/razor
ms.openlocfilehash: 53ca2a650eb6a3be0ff137953df5a546e9f0b282
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756127"
---
# <a name="razor-syntax-reference-for-aspnet-core"></a>Razor<span data-ttu-id="51e08-103">ASP.NET Core 的语法参考</span><span class="sxs-lookup"><span data-stu-id="51e08-103"> syntax reference for ASP.NET Core</span></span>

<span data-ttu-id="51e08-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Taylor Mullen](https://twitter.com/ntaylormullen)和[Dan Vicarel](https://github.com/Rabadash8820)</span><span class="sxs-lookup"><span data-stu-id="51e08-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Taylor Mullen](https://twitter.com/ntaylormullen), and [Dan Vicarel](https://github.com/Rabadash8820)</span></span>

Razor<span data-ttu-id="51e08-105">是一个用于将基于服务器的代码嵌入到网页中的标记语法。</span><span class="sxs-lookup"><span data-stu-id="51e08-105"> is a markup syntax for embedding server-based code into webpages.</span></span> <span data-ttu-id="51e08-106">Razor语法由 Razor 标记、c # 和 HTML 组成。</span><span class="sxs-lookup"><span data-stu-id="51e08-106">The Razor syntax consists of Razor markup, C#, and HTML.</span></span> <span data-ttu-id="51e08-107">通常包含 Razor 的文件的扩展名为 *...*</span><span class="sxs-lookup"><span data-stu-id="51e08-107">Files containing Razor generally have a *.cshtml* file extension.</span></span> Razor<span data-ttu-id="51e08-108">还可在[ Razor 组件](xref:blazor/components)文件（*razor*）中找到。</span><span class="sxs-lookup"><span data-stu-id="51e08-108"> is also found in [Razor components](xref:blazor/components) files (*.razor*).</span></span>

## <a name="rendering-html"></a><span data-ttu-id="51e08-109">呈现 HTML</span><span class="sxs-lookup"><span data-stu-id="51e08-109">Rendering HTML</span></span>

<span data-ttu-id="51e08-110">默认 Razor 语言为 HTML。</span><span class="sxs-lookup"><span data-stu-id="51e08-110">The default Razor language is HTML.</span></span> <span data-ttu-id="51e08-111">从标记呈现 HTML 与 Razor 从 html 文件呈现 html 没有什么不同。</span><span class="sxs-lookup"><span data-stu-id="51e08-111">Rendering HTML from Razor markup is no different than rendering HTML from an HTML file.</span></span> <span data-ttu-id="51e08-112">在中， *cshtml*文件中的 HTML 标记 Razor 不变。</span><span class="sxs-lookup"><span data-stu-id="51e08-112">HTML markup in *.cshtml* Razor files is rendered by the server unchanged.</span></span>

## <a name="razor-syntax"></a>Razor<span data-ttu-id="51e08-113"> 语法</span><span class="sxs-lookup"><span data-stu-id="51e08-113"> syntax</span></span>

Razor<span data-ttu-id="51e08-114">支持 c #，并使用 `@` 符号从 HTML 转换为 c #。</span><span class="sxs-lookup"><span data-stu-id="51e08-114"> supports C# and uses the `@` symbol to transition from HTML to C#.</span></span> Razor<span data-ttu-id="51e08-115">计算 c # 表达式并在 HTML 输出中呈现。</span><span class="sxs-lookup"><span data-stu-id="51e08-115"> evaluates C# expressions and renders them in the HTML output.</span></span>

<span data-ttu-id="51e08-116">当 `@` 符号后跟[ Razor 保留关键字](#razor-reserved-keywords)时，它会转换为 Razor 特定标记。</span><span class="sxs-lookup"><span data-stu-id="51e08-116">When an `@` symbol is followed by a [Razor reserved keyword](#razor-reserved-keywords), it transitions into Razor-specific markup.</span></span> <span data-ttu-id="51e08-117">否则会转换为纯 C#。</span><span class="sxs-lookup"><span data-stu-id="51e08-117">Otherwise, it transitions into plain C#.</span></span>

<span data-ttu-id="51e08-118">若要对 `@` 标记中的符号进行转义 Razor ，请使用第二个 `@` 符号：</span><span class="sxs-lookup"><span data-stu-id="51e08-118">To escape an `@` symbol in Razor markup, use a second `@` symbol:</span></span>

```cshtml
<p>@@Username</p>
```

<span data-ttu-id="51e08-119">该代码在 HTML 中使用单个 `@` 符号呈现：</span><span class="sxs-lookup"><span data-stu-id="51e08-119">The code is rendered in HTML with a single `@` symbol:</span></span>

```html
<p>@Username</p>
```

<span data-ttu-id="51e08-120">包含电子邮件地址的 HTML 属性和内容不将 `@` 符号视为转换字符。</span><span class="sxs-lookup"><span data-stu-id="51e08-120">HTML attributes and content containing email addresses don't treat the `@` symbol as a transition character.</span></span> <span data-ttu-id="51e08-121">以下示例中的电子邮件地址将通过分析来保持不变 Razor ：</span><span class="sxs-lookup"><span data-stu-id="51e08-121">The email addresses in the following example are untouched by Razor parsing:</span></span>

```cshtml
<a href="mailto:Support@contoso.com">Support@contoso.com</a>
```

## <a name="implicit-razor-expressions"></a><span data-ttu-id="51e08-122">隐式 Razor 表达式</span><span class="sxs-lookup"><span data-stu-id="51e08-122">Implicit Razor expressions</span></span>

<span data-ttu-id="51e08-123">隐式 Razor 表达式以开头， `@` 后跟 c # 代码：</span><span class="sxs-lookup"><span data-stu-id="51e08-123">Implicit Razor expressions start with `@` followed by C# code:</span></span>

```cshtml
<p>@DateTime.Now</p>
<p>@DateTime.IsLeapYear(2016)</p>
```

<span data-ttu-id="51e08-124">隐式表达式不能包含空格，但 C# `await` 关键字除外。</span><span class="sxs-lookup"><span data-stu-id="51e08-124">With the exception of the C# `await` keyword, implicit expressions must not contain spaces.</span></span> <span data-ttu-id="51e08-125">如果该 C# 语句具有明确的结束标记，则可以混用空格：</span><span class="sxs-lookup"><span data-stu-id="51e08-125">If the C# statement has a clear ending, spaces can be intermingled:</span></span>

```cshtml
<p>@await DoSomething("hello", "world")</p>
```

<span data-ttu-id="51e08-126">隐式表达式**不能**包含 C# 泛型，因为括号 (`<>`) 内的字符会被解释为 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="51e08-126">Implicit expressions **cannot** contain C# generics, as the characters inside the brackets (`<>`) are interpreted as an HTML tag.</span></span> <span data-ttu-id="51e08-127">以下代码**无**效：</span><span class="sxs-lookup"><span data-stu-id="51e08-127">The following code is **not** valid:</span></span>

```cshtml
<p>@GenericMethod<int>()</p>
```

<span data-ttu-id="51e08-128">上述代码生成与以下错误之一类似的编译器错误：</span><span class="sxs-lookup"><span data-stu-id="51e08-128">The preceding code generates a compiler error similar to one of the following:</span></span>

* <span data-ttu-id="51e08-129">"int" 元素未结束。</span><span class="sxs-lookup"><span data-stu-id="51e08-129">The "int" element wasn't closed.</span></span> <span data-ttu-id="51e08-130">所有元素都必须自结束或具有匹配的结束标记。</span><span class="sxs-lookup"><span data-stu-id="51e08-130">All elements must be either self-closing or have a matching end tag.</span></span>
* <span data-ttu-id="51e08-131">无法将方法组 "GenericMethod" 转换为非委托类型 "object"。</span><span class="sxs-lookup"><span data-stu-id="51e08-131">Cannot convert method group 'GenericMethod' to non-delegate type 'object'.</span></span> <span data-ttu-id="51e08-132">是否希望调用此方法?\`</span><span class="sxs-lookup"><span data-stu-id="51e08-132">Did you intend to invoke the method?\`</span></span>

<span data-ttu-id="51e08-133">泛型方法调用必须在[显式 Razor 表达式](#explicit-razor-expressions)或[ Razor 代码块](#razor-code-blocks)中进行包装。</span><span class="sxs-lookup"><span data-stu-id="51e08-133">Generic method calls must be wrapped in an [explicit Razor expression](#explicit-razor-expressions) or a [Razor code block](#razor-code-blocks).</span></span>

## <a name="explicit-razor-expressions"></a><span data-ttu-id="51e08-134">显式 Razor 表达式</span><span class="sxs-lookup"><span data-stu-id="51e08-134">Explicit Razor expressions</span></span>

<span data-ttu-id="51e08-135">显式 Razor 表达式由 `@` 带对称括号的符号组成。</span><span class="sxs-lookup"><span data-stu-id="51e08-135">Explicit Razor expressions consist of an `@` symbol with balanced parenthesis.</span></span> <span data-ttu-id="51e08-136">若要呈现上周的时间，请 Razor 使用以下标记：</span><span class="sxs-lookup"><span data-stu-id="51e08-136">To render last week's time, the following Razor markup is used:</span></span>

```cshtml
<p>Last week this time: @(DateTime.Now - TimeSpan.FromDays(7))</p>
```

<span data-ttu-id="51e08-137">将计算 `@()` 括号中的所有内容，并将其呈现到输出中。</span><span class="sxs-lookup"><span data-stu-id="51e08-137">Any content within the `@()` parenthesis is evaluated and rendered to the output.</span></span>

<span data-ttu-id="51e08-138">前面部分中所述的隐式表达式通常不能包含空格。</span><span class="sxs-lookup"><span data-stu-id="51e08-138">Implicit expressions, described in the previous section, generally can't contain spaces.</span></span> <span data-ttu-id="51e08-139">在下面的代码中，不会从当前时间减去一周：</span><span class="sxs-lookup"><span data-stu-id="51e08-139">In the following code, one week isn't subtracted from the current time:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact.cshtml?range=17)]

<span data-ttu-id="51e08-140">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="51e08-140">The code renders the following HTML:</span></span>

```html
<p>Last week: 7/7/2016 4:39:52 PM - TimeSpan.FromDays(7)</p>
```

<span data-ttu-id="51e08-141">可以使用显式表达式将文本与表达式结果串联起来：</span><span class="sxs-lookup"><span data-stu-id="51e08-141">Explicit expressions can be used to concatenate text with an expression result:</span></span>

```cshtml
@{
    var joe = new Person("Joe", 33);
}

<p>Age@(joe.Age)</p>
```

<span data-ttu-id="51e08-142">如果不使用显式表达式，`<p>Age@joe.Age</p>` 会被视为电子邮件地址，因此会呈现 `<p>Age@joe.Age</p>`。</span><span class="sxs-lookup"><span data-stu-id="51e08-142">Without the explicit expression, `<p>Age@joe.Age</p>` is treated as an email address, and `<p>Age@joe.Age</p>` is rendered.</span></span> <span data-ttu-id="51e08-143">如果编写为显式表达式，则呈现 `<p>Age33</p>`。</span><span class="sxs-lookup"><span data-stu-id="51e08-143">When written as an explicit expression, `<p>Age33</p>` is rendered.</span></span>

<span data-ttu-id="51e08-144">显式表达式可用于从 *.cshtml* 文件中的泛型方法呈现输出。</span><span class="sxs-lookup"><span data-stu-id="51e08-144">Explicit expressions can be used to render output from generic methods in *.cshtml* files.</span></span> <span data-ttu-id="51e08-145">以下标记显示了如何更正之前出现的由 C# 泛型的括号引起的错误。</span><span class="sxs-lookup"><span data-stu-id="51e08-145">The following markup shows how to correct the error shown earlier caused by the brackets of a C# generic.</span></span> <span data-ttu-id="51e08-146">此代码以显式表达式的形式编写：</span><span class="sxs-lookup"><span data-stu-id="51e08-146">The code is written as an explicit expression:</span></span>

```cshtml
<p>@(GenericMethod<int>())</p>
```

## <a name="expression-encoding"></a><span data-ttu-id="51e08-147">表达式编码</span><span class="sxs-lookup"><span data-stu-id="51e08-147">Expression encoding</span></span>

<span data-ttu-id="51e08-148">计算结果为字符串的 C# 表达式采用 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="51e08-148">C# expressions that evaluate to a string are HTML encoded.</span></span> <span data-ttu-id="51e08-149">计算结果为 `IHtmlContent` 的 C# 表达式直接通过 `IHtmlContent.WriteTo` 呈现。</span><span class="sxs-lookup"><span data-stu-id="51e08-149">C# expressions that evaluate to `IHtmlContent` are rendered directly through `IHtmlContent.WriteTo`.</span></span> <span data-ttu-id="51e08-150">计算结果不为 `IHtmlContent` 的 C# 表达式通过 `ToString` 转换为字符串，并在呈现前进行编码。</span><span class="sxs-lookup"><span data-stu-id="51e08-150">C# expressions that don't evaluate to `IHtmlContent` are converted to a string by `ToString` and encoded before they're rendered.</span></span>

```cshtml
@("<span>Hello World</span>")
```

<span data-ttu-id="51e08-151">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="51e08-151">The code renders the following HTML:</span></span>

```html
&lt;span&gt;Hello World&lt;/span&gt;
```

<span data-ttu-id="51e08-152">该 HTML 在浏览器中显示为：</span><span class="sxs-lookup"><span data-stu-id="51e08-152">The HTML is shown in the browser as:</span></span>

```html
<span>Hello World</span>
```

<span data-ttu-id="51e08-153">`HtmlHelper.Raw` 输出不进行编码，但呈现为 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="51e08-153">`HtmlHelper.Raw` output isn't encoded but rendered as HTML markup.</span></span>

> [!WARNING]
> <span data-ttu-id="51e08-154">对未经审查的用户输入使用 `HtmlHelper.Raw` 会带来安全风险。</span><span class="sxs-lookup"><span data-stu-id="51e08-154">Using `HtmlHelper.Raw` on unsanitized user input is a security risk.</span></span> <span data-ttu-id="51e08-155">用户输入可能包含恶意的 JavaScript 或其他攻击。</span><span class="sxs-lookup"><span data-stu-id="51e08-155">User input might contain malicious JavaScript or other exploits.</span></span> <span data-ttu-id="51e08-156">审查用户输入比较困难。</span><span class="sxs-lookup"><span data-stu-id="51e08-156">Sanitizing user input is difficult.</span></span> <span data-ttu-id="51e08-157">应避免对用户输入使用 `HtmlHelper.Raw`。</span><span class="sxs-lookup"><span data-stu-id="51e08-157">Avoid using `HtmlHelper.Raw` with user input.</span></span>

```cshtml
@Html.Raw("<span>Hello World</span>")
```

<span data-ttu-id="51e08-158">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="51e08-158">The code renders the following HTML:</span></span>

```html
<span>Hello World</span>
```

## <a name="razor-code-blocks"></a>Razor<span data-ttu-id="51e08-159">代码块</span><span class="sxs-lookup"><span data-stu-id="51e08-159"> code blocks</span></span>

Razor<span data-ttu-id="51e08-160">代码块以开头 `@` ，并由括起来 `{}` 。</span><span class="sxs-lookup"><span data-stu-id="51e08-160"> code blocks start with `@` and are enclosed by `{}`.</span></span> <span data-ttu-id="51e08-161">代码块内的 C# 代码不会呈现，这点与表达式不同。</span><span class="sxs-lookup"><span data-stu-id="51e08-161">Unlike expressions, C# code inside code blocks isn't rendered.</span></span> <span data-ttu-id="51e08-162">一个视图中的代码块和表达式共享相同的作用域并按顺序进行定义：</span><span class="sxs-lookup"><span data-stu-id="51e08-162">Code blocks and expressions in a view share the same scope and are defined in order:</span></span>

```cshtml
@{
    var quote = "The future depends on what you do today. - Mahatma Gandhi";
}

<p>@quote</p>

@{
    quote = "Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.";
}

<p>@quote</p>
```

<span data-ttu-id="51e08-163">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="51e08-163">The code renders the following HTML:</span></span>

```html
<p>The future depends on what you do today. - Mahatma Gandhi</p>
<p>Hate cannot drive out hate, only love can do that. - Martin Luther King, Jr.</p>
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51e08-164">在代码块中，使用标记将[本地函数](/dotnet/csharp/programming-guide/classes-and-structs/local-functions)声明为用作模板化方法：</span><span class="sxs-lookup"><span data-stu-id="51e08-164">In code blocks, declare [local functions](/dotnet/csharp/programming-guide/classes-and-structs/local-functions) with markup to serve as templating methods:</span></span>

```cshtml
@{
    void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }

    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}
```

<span data-ttu-id="51e08-165">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="51e08-165">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

::: moniker-end

### <a name="implicit-transitions"></a><span data-ttu-id="51e08-166">隐式转换</span><span class="sxs-lookup"><span data-stu-id="51e08-166">Implicit transitions</span></span>

<span data-ttu-id="51e08-167">代码块中的默认语言是 c #，但 Razor 页面可转换回 HTML：</span><span class="sxs-lookup"><span data-stu-id="51e08-167">The default language in a code block is C#, but the Razor Page can transition back to HTML:</span></span>

```cshtml
@{
    var inCSharp = true;
    <p>Now in HTML, was in C# @inCSharp</p>
}
```

### <a name="explicit-delimited-transition"></a><span data-ttu-id="51e08-168">带分隔符的显式转换</span><span class="sxs-lookup"><span data-stu-id="51e08-168">Explicit delimited transition</span></span>

<span data-ttu-id="51e08-169">若要定义应呈现 HTML 的代码块的子节，请将字符括在标记后 Razor `<text>` ：</span><span class="sxs-lookup"><span data-stu-id="51e08-169">To define a subsection of a code block that should render HTML, surround the characters for rendering with the Razor `<text>` tag:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <text>Name: @person.Name</text>
}
```

<span data-ttu-id="51e08-170">使用此方法可呈现未被 HTML 标记括起来的 HTML。</span><span class="sxs-lookup"><span data-stu-id="51e08-170">Use this approach to render HTML that isn't surrounded by an HTML tag.</span></span> <span data-ttu-id="51e08-171">如果没有 HTML 或 Razor 标记，则 Razor 会发生运行时错误。</span><span class="sxs-lookup"><span data-stu-id="51e08-171">Without an HTML or Razor tag, a Razor runtime error occurs.</span></span>

<span data-ttu-id="51e08-172">`<text>` 标记可用于在呈现内容时控制空格：</span><span class="sxs-lookup"><span data-stu-id="51e08-172">The `<text>` tag is useful to control whitespace when rendering content:</span></span>

* <span data-ttu-id="51e08-173">仅呈现 `<text>` 标记之间的内容。</span><span class="sxs-lookup"><span data-stu-id="51e08-173">Only the content between the `<text>` tag is rendered.</span></span>
* <span data-ttu-id="51e08-174">`<text>` 标记之前或之后的空格不会显示在 HTML 输出中。</span><span class="sxs-lookup"><span data-stu-id="51e08-174">No whitespace before or after the `<text>` tag appears in the HTML output.</span></span>

### <a name="explicit-line-transition"></a><span data-ttu-id="51e08-175">显式行转换</span><span class="sxs-lookup"><span data-stu-id="51e08-175">Explicit line transition</span></span>

<span data-ttu-id="51e08-176">要在代码块内以 HTML 形式呈现整个行的其余内容，请使用 `@:` 语法：</span><span class="sxs-lookup"><span data-stu-id="51e08-176">To render the rest of an entire line as HTML inside a code block, use `@:` syntax:</span></span>

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    @:Name: @person.Name
}
```

<span data-ttu-id="51e08-177">如果 `@:` 代码中没有，则 Razor 会生成运行时错误。</span><span class="sxs-lookup"><span data-stu-id="51e08-177">Without the `@:` in the code, a Razor runtime error is generated.</span></span>

<span data-ttu-id="51e08-178">`@`文件中的额外字符 Razor 可能会导致在块中后面的语句中出现编译器错误。</span><span class="sxs-lookup"><span data-stu-id="51e08-178">Extra `@` characters in a Razor file can cause compiler errors at statements later in the block.</span></span> <span data-ttu-id="51e08-179">这些编译器错误可能难以理解，因为实际错误发生在报告的错误之前。</span><span class="sxs-lookup"><span data-stu-id="51e08-179">These compiler errors can be difficult to understand because the actual error occurs before the reported error.</span></span> <span data-ttu-id="51e08-180">将多个隐式/显式表达式合并到单个代码块以后，经常会发生此错误。</span><span class="sxs-lookup"><span data-stu-id="51e08-180">This error is common after combining multiple implicit/explicit expressions into a single code block.</span></span>

## <a name="control-structures"></a><span data-ttu-id="51e08-181">控制结构</span><span class="sxs-lookup"><span data-stu-id="51e08-181">Control structures</span></span>

<span data-ttu-id="51e08-182">控制结构是对代码块的扩展。</span><span class="sxs-lookup"><span data-stu-id="51e08-182">Control structures are an extension of code blocks.</span></span> <span data-ttu-id="51e08-183">代码块的各个方面（转换为标记、内联 C#）同样适用于以下结构：</span><span class="sxs-lookup"><span data-stu-id="51e08-183">All aspects of code blocks (transitioning to markup, inline C#) also apply to the following structures:</span></span>

### <a name="conditionals-if-else-if-else-and-switch"></a><span data-ttu-id="51e08-184">条件语句`@if, else if, else, and @switch`</span><span class="sxs-lookup"><span data-stu-id="51e08-184">Conditionals `@if, else if, else, and @switch`</span></span>

<span data-ttu-id="51e08-185">`@if` 控制何时运行代码：</span><span class="sxs-lookup"><span data-stu-id="51e08-185">`@if` controls when code runs:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
```

<span data-ttu-id="51e08-186">`else` 和 `else if` 不需要 `@` 符号：</span><span class="sxs-lookup"><span data-stu-id="51e08-186">`else` and `else if` don't require the `@` symbol:</span></span>

```cshtml
@if (value % 2 == 0)
{
    <p>The value was even.</p>
}
else if (value >= 1337)
{
    <p>The value is large.</p>
}
else
{
    <p>The value is odd and small.</p>
}
```

<span data-ttu-id="51e08-187">以下标记展示如何使用 switch 语句：</span><span class="sxs-lookup"><span data-stu-id="51e08-187">The following markup shows how to use a switch statement:</span></span>

```cshtml
@switch (value)
{
    case 1:
        <p>The value is 1!</p>
        break;
    case 1337:
        <p>Your number is 1337!</p>
        break;
    default:
        <p>Your number wasn't 1 or 1337.</p>
        break;
}
```

### <a name="looping-for-foreach-while-and-do-while"></a><span data-ttu-id="51e08-188">Hal`@for, @foreach, @while, and @do while`</span><span class="sxs-lookup"><span data-stu-id="51e08-188">Looping `@for, @foreach, @while, and @do while`</span></span>

<span data-ttu-id="51e08-189">可以使用循环控制语句呈现模板化 HTML。</span><span class="sxs-lookup"><span data-stu-id="51e08-189">Templated HTML can be rendered with looping control statements.</span></span> <span data-ttu-id="51e08-190">若要呈现一组人员：</span><span class="sxs-lookup"><span data-stu-id="51e08-190">To render a list of people:</span></span>

```cshtml
@{
    var people = new Person[]
    {
          new Person("Weston", 33),
          new Person("Johnathon", 41),
          ...
    };
}
```

<span data-ttu-id="51e08-191">支持以下循环语句：</span><span class="sxs-lookup"><span data-stu-id="51e08-191">The following looping statements are supported:</span></span>

`@for`

```cshtml
@for (var i = 0; i < people.Length; i++)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@foreach`

```cshtml
@foreach (var person in people)
{
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>
}
```

`@while`

```cshtml
@{ var i = 0; }
@while (i < people.Length)
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
}
```

`@do while`

```cshtml
@{ var i = 0; }
@do
{
    var person = people[i];
    <p>Name: @person.Name</p>
    <p>Age: @person.Age</p>

    i++;
} while (i < people.Length);
```

### <a name="compound-using"></a><span data-ttu-id="51e08-192">复合语句 `@using`</span><span class="sxs-lookup"><span data-stu-id="51e08-192">Compound `@using`</span></span>

<span data-ttu-id="51e08-193">在 C# 中，`using` 语句用于确保释放对象。</span><span class="sxs-lookup"><span data-stu-id="51e08-193">In C#, a `using` statement is used to ensure an object is disposed.</span></span> <span data-ttu-id="51e08-194">在中 Razor ，使用相同的机制来创建包含其他内容的 HTML 帮助器。</span><span class="sxs-lookup"><span data-stu-id="51e08-194">In Razor, the same mechanism is used to create HTML Helpers that contain additional content.</span></span> <span data-ttu-id="51e08-195">在下面的代码中，HTML 帮助程序使用 `@using` 语句呈现 `<form>` 标记：</span><span class="sxs-lookup"><span data-stu-id="51e08-195">In the following code, HTML Helpers render a `<form>` tag with the `@using` statement:</span></span>

```cshtml
@using (Html.BeginForm())
{
    <div>
        Email: <input type="email" id="Email" value="">
        <button>Register</button>
    </div>
}
```

### `@try, catch, finally`

<span data-ttu-id="51e08-196">异常处理与 C# 类似：</span><span class="sxs-lookup"><span data-stu-id="51e08-196">Exception handling is similar to C#:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact7.cshtml)]

### `@lock`

Razor<span data-ttu-id="51e08-197">能够用 lock 语句保护关键部分：</span><span class="sxs-lookup"><span data-stu-id="51e08-197"> has the capability to protect critical sections with lock statements:</span></span>

```cshtml
@lock (SomeLock)
{
    // Do critical section work
}
```

### <a name="comments"></a><span data-ttu-id="51e08-198">注释</span><span class="sxs-lookup"><span data-stu-id="51e08-198">Comments</span></span>

Razor<span data-ttu-id="51e08-199">支持 c # 和 HTML 注释：</span><span class="sxs-lookup"><span data-stu-id="51e08-199"> supports C# and HTML comments:</span></span>

```cshtml
@{
    /* C# comment */
    // Another C# comment
}
<!-- HTML comment -->
```

<span data-ttu-id="51e08-200">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="51e08-200">The code renders the following HTML:</span></span>

```html
<!-- HTML comment -->
```

Razor<span data-ttu-id="51e08-201">在呈现网页之前，服务器将删除注释。</span><span class="sxs-lookup"><span data-stu-id="51e08-201"> comments are removed by the server before the webpage is rendered.</span></span> Razor<span data-ttu-id="51e08-202">用于 `@*  *@` 分隔注释。</span><span class="sxs-lookup"><span data-stu-id="51e08-202"> uses `@*  *@` to delimit comments.</span></span> <span data-ttu-id="51e08-203">以下代码已被注释禁止，因此服务器不呈现任何标记：</span><span class="sxs-lookup"><span data-stu-id="51e08-203">The following code is commented out, so the server doesn't render any markup:</span></span>

```cshtml
@*
    @{
        /* C# comment */
        // Another C# comment
    }
    <!-- HTML comment -->
*@
```

## <a name="directives"></a><span data-ttu-id="51e08-204">指令</span><span class="sxs-lookup"><span data-stu-id="51e08-204">Directives</span></span>

Razor<span data-ttu-id="51e08-205">指令由带有符号后的保留关键字的隐式表达式表示 `@` 。</span><span class="sxs-lookup"><span data-stu-id="51e08-205"> directives are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="51e08-206">指令通常用于更改视图分析方式或启用不同的功能。</span><span class="sxs-lookup"><span data-stu-id="51e08-206">A directive typically changes the way a view is parsed or enables different functionality.</span></span>

<span data-ttu-id="51e08-207">了解如何 Razor 为视图生成代码可以更轻松地了解指令的工作方式。</span><span class="sxs-lookup"><span data-stu-id="51e08-207">Understanding how Razor generates code for a view makes it easier to understand how directives work.</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact8.cshtml)]

<span data-ttu-id="51e08-208">该代码生成与下面类似的类：</span><span class="sxs-lookup"><span data-stu-id="51e08-208">The code generates a class similar to the following:</span></span>

```csharp
public class _Views_Something_cshtml : RazorPage<dynamic>
{
    public override async Task ExecuteAsync()
    {
        var output = "Getting old ain't for wimps! - Anonymous";

        WriteLiteral("/r/n<div>Quote of the Day: ");
        Write(output);
        WriteLiteral("</div>");
    }
}
```

<span data-ttu-id="51e08-209">在本文的后面部分中，将[检查 Razor 为视图生成的 c # 类](#inspect-the-razor-c-class-generated-for-a-view)，并说明如何查看此生成的类。</span><span class="sxs-lookup"><span data-stu-id="51e08-209">Later in this article, the section [Inspect the Razor C# class generated for a view](#inspect-the-razor-c-class-generated-for-a-view) explains how to view this generated class.</span></span>

### `@attribute`

<span data-ttu-id="51e08-210">`@attribute` 指令将给定的属性添加到生成的页或视图的类中。</span><span class="sxs-lookup"><span data-stu-id="51e08-210">The `@attribute` directive adds the given attribute to the class of the generated page or view.</span></span> <span data-ttu-id="51e08-211">以下示例添加 `[Authorize]` 属性：</span><span class="sxs-lookup"><span data-stu-id="51e08-211">The following example adds the `[Authorize]` attribute:</span></span>

```cshtml
@attribute [Authorize]
```

::: moniker range=">= aspnetcore-3.0"

### `@code`

<span data-ttu-id="51e08-212">*此方案仅适用于 Razor 组件（razor）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-212">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="51e08-213">`@code`块使[ Razor 组件](xref:blazor/components)能够向组件添加 c # 成员（字段、属性和方法）：</span><span class="sxs-lookup"><span data-stu-id="51e08-213">The `@code` block enables a [Razor component](xref:blazor/components) to add C# members (fields, properties, and methods) to a component:</span></span>

```razor
@code {
    // C# members (fields, properties, and methods)
}
```

<span data-ttu-id="51e08-214">对于 Razor 组件， `@code` 是的别名， [`@functions`](#functions) 建议使用 `@functions` 。</span><span class="sxs-lookup"><span data-stu-id="51e08-214">For Razor components, `@code` is an alias of [`@functions`](#functions) and recommended over `@functions`.</span></span> <span data-ttu-id="51e08-215">允许多个 `@code` 块。</span><span class="sxs-lookup"><span data-stu-id="51e08-215">More than one `@code` block is permissible.</span></span>

::: moniker-end

### `@functions`

<span data-ttu-id="51e08-216">`@functions` 指令允许将 C# 成员（字段、属性和方法）添加到生成的类中：</span><span class="sxs-lookup"><span data-stu-id="51e08-216">The `@functions` directive enables adding C# members (fields, properties, and methods) to the generated class:</span></span>

```cshtml
@functions {
    // C# members (fields, properties, and methods)
}
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51e08-217">在[ Razor 组件](xref:blazor/components)中，使用 `@code` Over `@functions` 来添加 c # 成员。</span><span class="sxs-lookup"><span data-stu-id="51e08-217">In [Razor components](xref:blazor/components), use `@code` over `@functions` to add C# members.</span></span>

::: moniker-end

<span data-ttu-id="51e08-218">例如：</span><span class="sxs-lookup"><span data-stu-id="51e08-218">For example:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact6.cshtml)]

<span data-ttu-id="51e08-219">该代码生成以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="51e08-219">The code generates the following HTML markup:</span></span>

```html
<div>From method: Hello</div>
```

<span data-ttu-id="51e08-220">下面的代码是生成的 Razor c # 类：</span><span class="sxs-lookup"><span data-stu-id="51e08-220">The following code is the generated Razor C# class:</span></span>

[!code-csharp[](razor/sample/Classes/Views_Home_Test_cshtml.cs?range=1-19)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51e08-221">`@functions` 方法有标记时，会用作模板化方法：</span><span class="sxs-lookup"><span data-stu-id="51e08-221">`@functions` methods serve as templating methods when they have markup:</span></span>

```cshtml
@{
    RenderName("Mahatma Gandhi");
    RenderName("Martin Luther King, Jr.");
}

@functions {
    private void RenderName(string name)
    {
        <p>Name: <strong>@name</strong></p>
    }
}
```

<span data-ttu-id="51e08-222">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="51e08-222">The code renders the following HTML:</span></span>

```html
<p>Name: <strong>Mahatma Gandhi</strong></p>
<p>Name: <strong>Martin Luther King, Jr.</strong></p>
```

### `@implements`

<span data-ttu-id="51e08-223">`@implements` 指令为生成的类实现接口。</span><span class="sxs-lookup"><span data-stu-id="51e08-223">The `@implements` directive implements an interface for the generated class.</span></span>

<span data-ttu-id="51e08-224">以下示例实现 <xref:System.IDisposable?displayProperty=fullName>，以便可以调用 <xref:System.IDisposable.Dispose*> 方法：</span><span class="sxs-lookup"><span data-stu-id="51e08-224">The following example implements <xref:System.IDisposable?displayProperty=fullName> so that the <xref:System.IDisposable.Dispose*> method can be called:</span></span>

```cshtml
@implements IDisposable

<h1>Example</h1>

@functions {
    private bool _isDisposed;

    ...

    public void Dispose() => _isDisposed = true;
}
```

::: moniker-end

### `@inherits`

<span data-ttu-id="51e08-225">`@inherits` 指令对视图继承的类提供完全控制：</span><span class="sxs-lookup"><span data-stu-id="51e08-225">The `@inherits` directive provides full control of the class the view inherits:</span></span>

```cshtml
@inherits TypeNameOfClassToInheritFrom
```

<span data-ttu-id="51e08-226">下面的代码是一个自定义的 Razor 页类型：</span><span class="sxs-lookup"><span data-stu-id="51e08-226">The following code is a custom Razor page type:</span></span>

[!code-csharp[](razor/sample/Classes/CustomRazorPage.cs)]

<span data-ttu-id="51e08-227">`CustomText` 显示在视图中：</span><span class="sxs-lookup"><span data-stu-id="51e08-227">The `CustomText` is displayed in a view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact10.cshtml)]

<span data-ttu-id="51e08-228">该代码呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="51e08-228">The code renders the following HTML:</span></span>

```html
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

 <span data-ttu-id="51e08-229">`@model` 和 `@inherits` 可在同一视图中使用。</span><span class="sxs-lookup"><span data-stu-id="51e08-229">`@model` and `@inherits` can be used in the same view.</span></span> <span data-ttu-id="51e08-230">`@inherits` 可位于视图导入的 *_ViewImports.cshtml* 文件中：</span><span class="sxs-lookup"><span data-stu-id="51e08-230">`@inherits` can be in a *_ViewImports.cshtml* file that the view imports:</span></span>

[!code-cshtml[](razor/sample/Views/_ViewImportsModel.cshtml)]

<span data-ttu-id="51e08-231">下面的代码是一种强类型视图：</span><span class="sxs-lookup"><span data-stu-id="51e08-231">The following code is an example of a strongly-typed view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Login1.cshtml)]

<span data-ttu-id="51e08-232">如果在模型中传递“rick@contoso.com”，视图将生成以下 HTML 标记：</span><span class="sxs-lookup"><span data-stu-id="51e08-232">If "rick@contoso.com" is passed in the model, the view generates the following HTML markup:</span></span>

```html
<div>The Login Email: rick@contoso.com</div>
<div>
    Custom text: Gardyloo! - A Scottish warning yelled from a window before dumping
    a slop bucket on the street below.
</div>
```

### `@inject`

<span data-ttu-id="51e08-233">`@inject`指令使 Razor 页面可以将服务从[服务容器](xref:fundamentals/dependency-injection)注入到视图。</span><span class="sxs-lookup"><span data-stu-id="51e08-233">The `@inject` directive enables the Razor Page to inject a service from the [service container](xref:fundamentals/dependency-injection) into a view.</span></span> <span data-ttu-id="51e08-234">有关详细信息，请参阅[视图中的依赖关系注入](xref:mvc/views/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="51e08-234">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

### `@layout`

<span data-ttu-id="51e08-235">*此方案仅适用于 Razor 组件（razor）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-235">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="51e08-236">`@layout`指令指定组件的布局 Razor 。</span><span class="sxs-lookup"><span data-stu-id="51e08-236">The `@layout` directive specifies a layout for a Razor component.</span></span> <span data-ttu-id="51e08-237">布局组件用于避免代码重复和不一致。</span><span class="sxs-lookup"><span data-stu-id="51e08-237">Layout components are used to avoid code duplication and inconsistency.</span></span> <span data-ttu-id="51e08-238">有关详细信息，请参阅 <xref:blazor/layouts>。</span><span class="sxs-lookup"><span data-stu-id="51e08-238">For more information, see <xref:blazor/layouts>.</span></span>

::: moniker-end

### `@model`

<span data-ttu-id="51e08-239">*此方案仅适用于 MVC 视图和 Razor 页面（cshtml）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-239">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="51e08-240">`@model` 指令指定传递到视图或页面的模型类型：</span><span class="sxs-lookup"><span data-stu-id="51e08-240">The `@model` directive specifies the type of the model passed to a view or page:</span></span>

```cshtml
@model TypeNameOfModel
```

<span data-ttu-id="51e08-241">在 Razor 使用单独的用户帐户创建的 ASP.NET CORE MVC 或页面应用中， *Views/Account/Login。 cshtml*包含以下模型声明：</span><span class="sxs-lookup"><span data-stu-id="51e08-241">In an ASP.NET Core MVC or Razor Pages app created with individual user accounts, *Views/Account/Login.cshtml* contains the following model declaration:</span></span>

```cshtml
@model LoginViewModel
```

<span data-ttu-id="51e08-242">生成的类继承自 `RazorPage<dynamic>`：</span><span class="sxs-lookup"><span data-stu-id="51e08-242">The class generated inherits from `RazorPage<dynamic>`:</span></span>

```csharp
public class _Views_Account_Login_cshtml : RazorPage<LoginViewModel>
```

Razor<span data-ttu-id="51e08-243">公开 `Model` 用于访问传递到视图的模型的属性：</span><span class="sxs-lookup"><span data-stu-id="51e08-243"> exposes a `Model` property for accessing the model passed to the view:</span></span>

```cshtml
<div>The Login Email: @Model.Email</div>
```

<span data-ttu-id="51e08-244">`@model` 指令指定 `Model` 属性的类型。</span><span class="sxs-lookup"><span data-stu-id="51e08-244">The `@model` directive specifies the type of the `Model` property.</span></span> <span data-ttu-id="51e08-245">该指令将 `RazorPage<T>` 中的 `T` 指定为生成的类，视图便派生自该类。</span><span class="sxs-lookup"><span data-stu-id="51e08-245">The directive specifies the `T` in `RazorPage<T>` that the generated class that the view derives from.</span></span> <span data-ttu-id="51e08-246">如果未指定 `@model` 指令，则 `Model` 属性的类型为 `dynamic`。</span><span class="sxs-lookup"><span data-stu-id="51e08-246">If the `@model` directive isn't specified, the `Model` property is of type `dynamic`.</span></span> <span data-ttu-id="51e08-247">有关详细信息，请参阅[强类型模型和 @model 关键字](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword)。</span><span class="sxs-lookup"><span data-stu-id="51e08-247">For more information, see [Strongly typed models and the @model keyword](xref:tutorials/first-mvc-app/adding-model#strongly-typed-models-and-the--keyword).</span></span>

### `@namespace`

<span data-ttu-id="51e08-248">`@namespace` 指令：</span><span class="sxs-lookup"><span data-stu-id="51e08-248">The `@namespace` directive:</span></span>

* <span data-ttu-id="51e08-249">设置生成的 Razor 页、MVC 视图或组件的类的命名空间 Razor 。</span><span class="sxs-lookup"><span data-stu-id="51e08-249">Sets the namespace of the class of the generated Razor page, MVC view, or Razor component.</span></span>
* <span data-ttu-id="51e08-250">在目录树中最近的导入文件中设置页面、视图或组件类的根派生命名空间， *_ViewImports* （视图或页面）或 *_Imports razor* （ Razor 组件）。</span><span class="sxs-lookup"><span data-stu-id="51e08-250">Sets the root derived namespaces of a pages, views, or components classes from the closest imports file in the directory tree, *_ViewImports.cshtml* (views or pages) or *_Imports.razor* (Razor components).</span></span>

```cshtml
@namespace Your.Namespace.Here
```

<span data-ttu-id="51e08-251">对于 Razor 下表中所示的页面示例：</span><span class="sxs-lookup"><span data-stu-id="51e08-251">For the Razor Pages example shown in the following table:</span></span>

* <span data-ttu-id="51e08-252">每个页面都导入 Pages/_ViewImports.cshtml\*\*。</span><span class="sxs-lookup"><span data-stu-id="51e08-252">Each page imports *Pages/_ViewImports.cshtml*.</span></span>
* <span data-ttu-id="51e08-253">Pages/_ViewImports.cshtml 包含 `@namespace Hello.World`\*\*。</span><span class="sxs-lookup"><span data-stu-id="51e08-253">*Pages/_ViewImports.cshtml* contains `@namespace Hello.World`.</span></span>
* <span data-ttu-id="51e08-254">每个页面都有 `Hello.World`，作为其命名空间的根。</span><span class="sxs-lookup"><span data-stu-id="51e08-254">Each page has `Hello.World` as the root of it's namespace.</span></span>

| <span data-ttu-id="51e08-255">页面</span><span class="sxs-lookup"><span data-stu-id="51e08-255">Page</span></span>                                        | <span data-ttu-id="51e08-256">命名空间</span><span class="sxs-lookup"><span data-stu-id="51e08-256">Namespace</span></span>                             |
| ------------------------------------------- | ------------------------------------- |
| <span data-ttu-id="51e08-257">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="51e08-257">*Pages/Index.cshtml*</span></span>                        | `Hello.World`                         |
| <span data-ttu-id="51e08-258">Pages/MorePages/Page.cshtml\*\*</span><span class="sxs-lookup"><span data-stu-id="51e08-258">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages`               |
| <span data-ttu-id="51e08-259">Pages/MorePages/EvenMorePages/Page.cshtml\*\*</span><span class="sxs-lookup"><span data-stu-id="51e08-259">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Hello.World.MorePages.EvenMorePages` |

<span data-ttu-id="51e08-260">上述关系适用于导入与 MVC 视图和组件一起使用的文件 Razor 。</span><span class="sxs-lookup"><span data-stu-id="51e08-260">The preceding relationships apply to import files used with MVC views and Razor components.</span></span>

<span data-ttu-id="51e08-261">当多个导入文件具有 `@namespace` 指令时，最靠近目录树中的页面、视图或组件的文件将用于设置根命名空间。</span><span class="sxs-lookup"><span data-stu-id="51e08-261">When multiple import files have a `@namespace` directive, the file closest to the page, view, or component in the directory tree is used to set the root namespace.</span></span>

<span data-ttu-id="51e08-262">如果前面示例中的 EvenMorePages 文件夹具有包含 `@namespace Another.Planet` 的导入文件（或 Pages/MorePages/EvenMorePages/Page.cshtml 文件包含 `@namespace Another.Planet`），则结果如下表所示\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="51e08-262">If the *EvenMorePages* folder in the preceding example has an imports file with `@namespace Another.Planet` (or the *Pages/MorePages/EvenMorePages/Page.cshtml* file contains `@namespace Another.Planet`), the result is shown in the following table.</span></span>

| <span data-ttu-id="51e08-263">页面</span><span class="sxs-lookup"><span data-stu-id="51e08-263">Page</span></span>                                        | <span data-ttu-id="51e08-264">命名空间</span><span class="sxs-lookup"><span data-stu-id="51e08-264">Namespace</span></span>               |
| ------------------------------------------- | ----------------------- |
| <span data-ttu-id="51e08-265">*Pages/Index. cshtml*</span><span class="sxs-lookup"><span data-stu-id="51e08-265">*Pages/Index.cshtml*</span></span>                        | `Hello.World`           |
| <span data-ttu-id="51e08-266">Pages/MorePages/Page.cshtml\*\*</span><span class="sxs-lookup"><span data-stu-id="51e08-266">*Pages/MorePages/Page.cshtml*</span></span>               | `Hello.World.MorePages` |
| <span data-ttu-id="51e08-267">Pages/MorePages/EvenMorePages/Page.cshtml\*\*</span><span class="sxs-lookup"><span data-stu-id="51e08-267">*Pages/MorePages/EvenMorePages/Page.cshtml*</span></span> | `Another.Planet`        |

### `@page`

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51e08-268">`@page` 指令具有不同的效果，具体取决于其所在文件的类型。</span><span class="sxs-lookup"><span data-stu-id="51e08-268">The `@page` directive has different effects depending on the type of the file where it appears.</span></span> <span data-ttu-id="51e08-269">指令：</span><span class="sxs-lookup"><span data-stu-id="51e08-269">The directive:</span></span>

* <span data-ttu-id="51e08-270">在中， *# 文件中*的指示该文件是一个 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="51e08-270">In in a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="51e08-271">有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)和 <xref:razor-pages/index>。</span><span class="sxs-lookup"><span data-stu-id="51e08-271">For more information, see [Custom routes](xref:razor-pages/index#custom-routes) and <xref:razor-pages/index>.</span></span>
* <span data-ttu-id="51e08-272">指定 Razor 组件应直接处理请求。</span><span class="sxs-lookup"><span data-stu-id="51e08-272">Specifies that a Razor component should handle requests directly.</span></span> <span data-ttu-id="51e08-273">有关详细信息，请参阅 <xref:blazor/routing>。</span><span class="sxs-lookup"><span data-stu-id="51e08-273">For more information, see <xref:blazor/routing>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="51e08-274">`@page` *Cshtml*文件第一行的指令指示该文件是一个 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="51e08-274">The `@page` directive on the first line of a *.cshtml* file indicates that the file is a Razor Page.</span></span> <span data-ttu-id="51e08-275">有关详细信息，请参阅 <xref:razor-pages/index>。</span><span class="sxs-lookup"><span data-stu-id="51e08-275">For more information, see <xref:razor-pages/index>.</span></span>

::: moniker-end

### `@section`

<span data-ttu-id="51e08-276">*此方案仅适用于 MVC 视图和 Razor 页面（cshtml）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-276">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="51e08-277">`@section`指令与[MVC 和 Razor 页面布局](xref:mvc/views/layout)结合使用，以使视图或页面能够在 HTML 页面的不同部分中呈现内容。</span><span class="sxs-lookup"><span data-stu-id="51e08-277">The `@section` directive is used in conjunction with [MVC and Razor Pages layouts](xref:mvc/views/layout) to enable views or pages to render content in different parts of the HTML page.</span></span> <span data-ttu-id="51e08-278">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="51e08-278">For more information, see <xref:mvc/views/layout>.</span></span>

### `@using`

<span data-ttu-id="51e08-279">`@using` 指令用于向生成的视图添加 C# `using` 指令：</span><span class="sxs-lookup"><span data-stu-id="51e08-279">The `@using` directive adds the C# `using` directive to the generated view:</span></span>

[!code-cshtml[](razor/sample/Views/Home/Contact9.cshtml)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="51e08-280">在 " [ Razor 组件](xref:blazor/components)" 中， `@using` 还控制哪些组件在范围内。</span><span class="sxs-lookup"><span data-stu-id="51e08-280">In [Razor components](xref:blazor/components), `@using` also controls which components are in scope.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

## <a name="directive-attributes"></a><span data-ttu-id="51e08-281">指令属性</span><span class="sxs-lookup"><span data-stu-id="51e08-281">Directive attributes</span></span>

Razor<span data-ttu-id="51e08-282">指令特性由带有符号后的保留关键字的隐式表达式表示 `@` 。</span><span class="sxs-lookup"><span data-stu-id="51e08-282"> directive attributes are represented by implicit expressions with reserved keywords following the `@` symbol.</span></span> <span data-ttu-id="51e08-283">指令特性通常会改变元素的分析方式，或实现不同的功能。</span><span class="sxs-lookup"><span data-stu-id="51e08-283">A directive attribute typically changes the way an element is parsed or enables different functionality.</span></span>

### `@attributes`

<span data-ttu-id="51e08-284">*此方案仅适用于 Razor 组件（razor）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-284">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="51e08-285">`@attributes` 允许组件呈现未声明的属性。</span><span class="sxs-lookup"><span data-stu-id="51e08-285">`@attributes` allows a component to render non-declared attributes.</span></span> <span data-ttu-id="51e08-286">有关详细信息，请参阅 <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>。</span><span class="sxs-lookup"><span data-stu-id="51e08-286">For more information, see <xref:blazor/components#attribute-splatting-and-arbitrary-parameters>.</span></span>

### `@bind`

<span data-ttu-id="51e08-287">*此方案仅适用于 Razor 组件（razor）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-287">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="51e08-288">组件中的数据绑定通过 `@bind` 属性实现。</span><span class="sxs-lookup"><span data-stu-id="51e08-288">Data binding in components is accomplished with the `@bind` attribute.</span></span> <span data-ttu-id="51e08-289">有关详细信息，请参阅 <xref:blazor/data-binding>。</span><span class="sxs-lookup"><span data-stu-id="51e08-289">For more information, see <xref:blazor/data-binding>.</span></span>

### `@on{EVENT}`

<span data-ttu-id="51e08-290">*此方案仅适用于 Razor 组件（razor）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-290">*This scenario only applies to Razor components (.razor).*</span></span>

Razor<span data-ttu-id="51e08-291">为组件提供事件处理功能。</span><span class="sxs-lookup"><span data-stu-id="51e08-291"> provides event handling features for components.</span></span> <span data-ttu-id="51e08-292">有关详细信息，请参阅 <xref:blazor/event-handling>。</span><span class="sxs-lookup"><span data-stu-id="51e08-292">For more information, see <xref:blazor/event-handling>.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.1"

### `@on{EVENT}:preventDefault`

<span data-ttu-id="51e08-293">*此方案仅适用于 Razor 组件（razor）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-293">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="51e08-294">禁止事件的默认操作。</span><span class="sxs-lookup"><span data-stu-id="51e08-294">Prevents the default action for the event.</span></span>

### `@on{EVENT}:stopPropagation`

<span data-ttu-id="51e08-295">*此方案仅适用于 Razor 组件（razor）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-295">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="51e08-296">停止事件的事件传播。</span><span class="sxs-lookup"><span data-stu-id="51e08-296">Stops event propagation for the event.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-3.0"

### `@key`

<span data-ttu-id="51e08-297">*此方案仅适用于 Razor 组件（razor）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-297">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="51e08-298">`@key` 指令属性使组件比较算法保证基于键的值保留元素或组件。</span><span class="sxs-lookup"><span data-stu-id="51e08-298">The `@key` directive attribute causes the components diffing algorithm to guarantee preservation of elements or components based on the key's value.</span></span> <span data-ttu-id="51e08-299">有关详细信息，请参阅 <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>。</span><span class="sxs-lookup"><span data-stu-id="51e08-299">For more information, see <xref:blazor/components#use-key-to-control-the-preservation-of-elements-and-components>.</span></span>

### `@ref`

<span data-ttu-id="51e08-300">*此方案仅适用于 Razor 组件（razor）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-300">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="51e08-301">组件引用 (`@ref`) 提供了一种引用组件实例的方法，以便可以向该实例发出命令。</span><span class="sxs-lookup"><span data-stu-id="51e08-301">Component references (`@ref`) provide a way to reference a component instance so that you can issue commands to that instance.</span></span> <span data-ttu-id="51e08-302">有关详细信息，请参阅 <xref:blazor/components#capture-references-to-components>。</span><span class="sxs-lookup"><span data-stu-id="51e08-302">For more information, see <xref:blazor/components#capture-references-to-components>.</span></span>

### `@typeparam`

<span data-ttu-id="51e08-303">*此方案仅适用于 Razor 组件（razor）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-303">*This scenario only applies to Razor components (.razor).*</span></span>

<span data-ttu-id="51e08-304">`@typeparam` 指令声明生成的组件类的泛型类型参数。</span><span class="sxs-lookup"><span data-stu-id="51e08-304">The `@typeparam` directive declares a generic type parameter for the generated component class.</span></span> <span data-ttu-id="51e08-305">有关详细信息，请参阅 <xref:blazor/templated-components#generic-typed-components>。</span><span class="sxs-lookup"><span data-stu-id="51e08-305">For more information, see <xref:blazor/templated-components#generic-typed-components>.</span></span>

::: moniker-end

## <a name="templated-razor-delegates"></a><span data-ttu-id="51e08-306">模板化 Razor 委托</span><span class="sxs-lookup"><span data-stu-id="51e08-306">Templated Razor delegates</span></span>

Razor<span data-ttu-id="51e08-307">模板允许使用以下格式定义 UI 代码段：</span><span class="sxs-lookup"><span data-stu-id="51e08-307"> templates allow you to define a UI snippet with the following format:</span></span>

```cshtml
@<tag>...</tag>
```

<span data-ttu-id="51e08-308">下面的示例演示如何将模板化 Razor 委托指定为 <xref:System.Func%602> 。</span><span class="sxs-lookup"><span data-stu-id="51e08-308">The following example illustrates how to specify a templated Razor delegate as a <xref:System.Func%602>.</span></span> <span data-ttu-id="51e08-309">为委托封装的方法的参数指定[动态类型](/dotnet/csharp/programming-guide/types/using-type-dynamic)。</span><span class="sxs-lookup"><span data-stu-id="51e08-309">The [dynamic type](/dotnet/csharp/programming-guide/types/using-type-dynamic) is specified for the parameter of the method that the delegate encapsulates.</span></span> <span data-ttu-id="51e08-310">将[对象类型](/dotnet/csharp/language-reference/keywords/object)指定为委托的返回值。</span><span class="sxs-lookup"><span data-stu-id="51e08-310">An [object type](/dotnet/csharp/language-reference/keywords/object) is specified as the return value of the delegate.</span></span> <span data-ttu-id="51e08-311">该模板与 `Pet`（具有 `Name` 属性）的 <xref:System.Collections.Generic.List%601> 一起使用。</span><span class="sxs-lookup"><span data-stu-id="51e08-311">The template is used with a <xref:System.Collections.Generic.List%601> of `Pet` that has a `Name` property.</span></span>

```csharp
public class Pet
{
    public string Name { get; set; }
}
```

```cshtml
@{
    Func<dynamic, object> petTemplate = @<p>You have a pet named <strong>@item.Name</strong>.</p>;

    var pets = new List<Pet>
    {
        new Pet { Name = "Rin Tin Tin" },
        new Pet { Name = "Mr. Bigglesworth" },
        new Pet { Name = "K-9" }
    };
}
```

<span data-ttu-id="51e08-312">使用 `foreach` 语句提供的 `pets` 呈现该模板：</span><span class="sxs-lookup"><span data-stu-id="51e08-312">The template is rendered with `pets` supplied by a `foreach` statement:</span></span>

```cshtml
@foreach (var pet in pets)
{
    @petTemplate(pet)
}
```

<span data-ttu-id="51e08-313">呈现的输出：</span><span class="sxs-lookup"><span data-stu-id="51e08-313">Rendered output:</span></span>

```html
<p>You have a pet named <strong>Rin Tin Tin</strong>.</p>
<p>You have a pet named <strong>Mr. Bigglesworth</strong>.</p>
<p>You have a pet named <strong>K-9</strong>.</p>
```

<span data-ttu-id="51e08-314">你还可以将内联 Razor 模板作为参数提供给方法。</span><span class="sxs-lookup"><span data-stu-id="51e08-314">You can also supply an inline Razor template as an argument to a method.</span></span> <span data-ttu-id="51e08-315">在下面的示例中， `Repeat` 方法接收 Razor 模板。</span><span class="sxs-lookup"><span data-stu-id="51e08-315">In the following example, the `Repeat` method receives a Razor template.</span></span> <span data-ttu-id="51e08-316">该方法使用模板生成 HTML 内容，其中包含列表中提供的重复项：</span><span class="sxs-lookup"><span data-stu-id="51e08-316">The method uses the template to produce HTML content with repeats of items supplied from a list:</span></span>

```cshtml
@using Microsoft.AspNetCore.Html

@functions {
    public static IHtmlContent Repeat(IEnumerable<dynamic> items, int times,
        Func<dynamic, IHtmlContent> template)
    {
        var html = new HtmlContentBuilder();

        foreach (var item in items)
        {
            for (var i = 0; i < times; i++)
            {
                html.AppendHtml(template(item));
            }
        }

        return html;
    }
}
```

<span data-ttu-id="51e08-317">使用前面示例中的 pets 列表，调用 `Repeat` 方法以及：</span><span class="sxs-lookup"><span data-stu-id="51e08-317">Using the list of pets from the prior example, the `Repeat` method is called with:</span></span>

* <span data-ttu-id="51e08-318">`Pet` 的 <xref:System.Collections.Generic.List%601>。</span><span class="sxs-lookup"><span data-stu-id="51e08-318"><xref:System.Collections.Generic.List%601> of `Pet`.</span></span>
* <span data-ttu-id="51e08-319">每只宠物的重复次数。</span><span class="sxs-lookup"><span data-stu-id="51e08-319">Number of times to repeat each pet.</span></span>
* <span data-ttu-id="51e08-320">用于无序列表的列表项的内联模板。</span><span class="sxs-lookup"><span data-stu-id="51e08-320">Inline template to use for the list items of an unordered list.</span></span>

```cshtml
<ul>
    @Repeat(pets, 3, @<li>@item.Name</li>)
</ul>
```

<span data-ttu-id="51e08-321">呈现的输出：</span><span class="sxs-lookup"><span data-stu-id="51e08-321">Rendered output:</span></span>

```html
<ul>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Rin Tin Tin</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>Mr. Bigglesworth</li>
    <li>K-9</li>
    <li>K-9</li>
    <li>K-9</li>
</ul>
```

## <a name="tag-helpers"></a><span data-ttu-id="51e08-322">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="51e08-322">Tag Helpers</span></span>

<span data-ttu-id="51e08-323">*此方案仅适用于 MVC 视图和 Razor 页面（cshtml）。*</span><span class="sxs-lookup"><span data-stu-id="51e08-323">*This scenario only applies to MVC views and Razor Pages (.cshtml).*</span></span>

<span data-ttu-id="51e08-324">[标记帮助程序](xref:mvc/views/tag-helpers/intro)有三个相关指令。</span><span class="sxs-lookup"><span data-stu-id="51e08-324">There are three directives that pertain to [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span>

| <span data-ttu-id="51e08-325">指令</span><span class="sxs-lookup"><span data-stu-id="51e08-325">Directive</span></span> | <span data-ttu-id="51e08-326">函数</span><span class="sxs-lookup"><span data-stu-id="51e08-326">Function</span></span> |
| --------- | -------- |
| [`@addTagHelper`](xref:mvc/views/tag-helpers/intro#add-helper-label) | <span data-ttu-id="51e08-327">向视图提供标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="51e08-327">Makes Tag Helpers available to a view.</span></span> |
| [`@removeTagHelper`](xref:mvc/views/tag-helpers/intro#remove-razor-directives-label) | <span data-ttu-id="51e08-328">从视图中删除以前添加的标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="51e08-328">Removes Tag Helpers previously added from a view.</span></span> |
| [`@tagHelperPrefix`](xref:mvc/views/tag-helpers/intro#prefix-razor-directives-label) | <span data-ttu-id="51e08-329">指定标记前缀，以启用标记帮助程序支持并阐明标记帮助程序的用法。</span><span class="sxs-lookup"><span data-stu-id="51e08-329">Specifies a tag prefix to enable Tag Helper support and to make Tag Helper usage explicit.</span></span> |

## <a name="razor-reserved-keywords"></a>Razor<span data-ttu-id="51e08-330">保留关键字</span><span class="sxs-lookup"><span data-stu-id="51e08-330"> reserved keywords</span></span>

### <a name="razor-keywords"></a>Razor<span data-ttu-id="51e08-331">字</span><span class="sxs-lookup"><span data-stu-id="51e08-331"> keywords</span></span>

* <span data-ttu-id="51e08-332">`page`（需要 ASP.NET Core 2.1 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="51e08-332">`page` (Requires ASP.NET Core 2.1 or later)</span></span>
* `namespace`
* `functions`
* `inherits`
* `model`
* `section`
* <span data-ttu-id="51e08-333">`helper`（目前不受 ASP.NET Core 支持）</span><span class="sxs-lookup"><span data-stu-id="51e08-333">`helper` (Not currently supported by ASP.NET Core)</span></span>

Razor<span data-ttu-id="51e08-334">关键字通过 `@(Razor Keyword)` （例如）进行转义 `@(functions)` 。</span><span class="sxs-lookup"><span data-stu-id="51e08-334"> keywords are escaped with `@(Razor Keyword)` (for example, `@(functions)`).</span></span>

### <a name="c-razor-keywords"></a><span data-ttu-id="51e08-335">C # Razor 关键字</span><span class="sxs-lookup"><span data-stu-id="51e08-335">C# Razor keywords</span></span>

* `case`
* `do`
* `default`
* `for`
* `foreach`
* `if`
* `else`
* `lock`
* `switch`
* `try`
* `catch`
* `finally`
* `using`
* `while`

<span data-ttu-id="51e08-336">C # Razor 关键字必须与 `@(@C# Razor Keyword)` （例如）进行双转义 `@(@case)` 。</span><span class="sxs-lookup"><span data-stu-id="51e08-336">C# Razor keywords must be double-escaped with `@(@C# Razor Keyword)` (for example, `@(@case)`).</span></span> <span data-ttu-id="51e08-337">第一个 `@` 转义 Razor 分析器。</span><span class="sxs-lookup"><span data-stu-id="51e08-337">The first `@` escapes the Razor parser.</span></span> <span data-ttu-id="51e08-338">第二个 `@` 对 C# 分析器转义。</span><span class="sxs-lookup"><span data-stu-id="51e08-338">The second `@` escapes the C# parser.</span></span>

### <a name="reserved-keywords-not-used-by-razor"></a><span data-ttu-id="51e08-339">不使用的保留关键字Razor</span><span class="sxs-lookup"><span data-stu-id="51e08-339">Reserved keywords not used by Razor</span></span>

* `class`

## <a name="inspect-the-razor-c-class-generated-for-a-view"></a><span data-ttu-id="51e08-340">检查 Razor 为视图生成的 c # 类</span><span class="sxs-lookup"><span data-stu-id="51e08-340">Inspect the Razor C# class generated for a view</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="51e08-341">对于 .NET Core SDK 2.1 或更高版本， [ Razor SDK](xref:razor-pages/sdk)会处理 Razor 文件的编译。</span><span class="sxs-lookup"><span data-stu-id="51e08-341">With .NET Core SDK 2.1 or later, the [Razor SDK](xref:razor-pages/sdk) handles compilation of Razor files.</span></span> <span data-ttu-id="51e08-342">生成项目时，SDK 会 Razor 在项目根目录中生成一个 *<build_configuration>/<target_framework_moniker> Razor /* 目录。</span><span class="sxs-lookup"><span data-stu-id="51e08-342">When building a project, the Razor SDK generates an *obj/<build_configuration>/<target_framework_moniker>/Razor* directory in the project root.</span></span> <span data-ttu-id="51e08-343">目录中的目录结构 *Razor* 反映了项目的目录结构。</span><span class="sxs-lookup"><span data-stu-id="51e08-343">The directory structure within the *Razor* directory mirrors the project's directory structure.</span></span>

<span data-ttu-id="51e08-344">请考虑面向 .NET Core 2.1 的 ASP.NET Core 2.1 页项目中的以下目录结构 Razor ：</span><span class="sxs-lookup"><span data-stu-id="51e08-344">Consider the following directory structure in an ASP.NET Core 2.1 Razor Pages project targeting .NET Core 2.1:</span></span>

```
 Areas/
   Admin/
     Pages/
       Index.cshtml
       Index.cshtml.cs
 Pages/
   Shared/
     _Layout.cshtml
   _ViewImports.cshtml
   _ViewStart.cshtml
   Index.cshtml
   Index.cshtml.cs
  ```

<span data-ttu-id="51e08-345">在 Debug 配置下生成项目将生成以下 obj 目录\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="51e08-345">Building the project in *Debug* configuration yields the following *obj* directory:</span></span>

```
 obj/
   Debug/
     netcoreapp2.1/
       Razor/
         Areas/
           Admin/
             Pages/
               Index.g.cshtml.cs
         Pages/
           Shared/
             _Layout.g.cshtml.cs
           _ViewImports.g.cshtml.cs
           _ViewStart.g.cshtml.cs
           Index.g.cshtml.cs
```

<span data-ttu-id="51e08-346">若要查看*页面/索引*生成的类，请打开*obj/Debug/netcoreapp 2.1/ Razor /Pages/Index.g.cshtml.cs*。</span><span class="sxs-lookup"><span data-stu-id="51e08-346">To view the generated class for *Pages/Index.cshtml*, open *obj/Debug/netcoreapp2.1/Razor/Pages/Index.g.cshtml.cs*.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

<span data-ttu-id="51e08-347">将下面的类添加到 ASP.NET Core MVC 项目：</span><span class="sxs-lookup"><span data-stu-id="51e08-347">Add the following class to the ASP.NET Core MVC project:</span></span>

[!code-csharp[](razor/sample/Utilities/CustomTemplateEngine.cs)]

<span data-ttu-id="51e08-348">在 `Startup.ConfigureServices` 中，使用 `CustomTemplateEngine` 类替代 MVC 添加的 `RazorTemplateEngine`：</span><span class="sxs-lookup"><span data-stu-id="51e08-348">In `Startup.ConfigureServices`, override the `RazorTemplateEngine` added by MVC with the `CustomTemplateEngine` class:</span></span>

[!code-csharp[](razor/sample/Startup.cs?highlight=4&range=10-14)]

<span data-ttu-id="51e08-349">在 `CustomTemplateEngine` 的 `return csharpDocument;` 语句上设置断点。</span><span class="sxs-lookup"><span data-stu-id="51e08-349">Set a breakpoint on the `return csharpDocument;` statement of `CustomTemplateEngine`.</span></span> <span data-ttu-id="51e08-350">当程序执行在断点处停止时，查看 `generatedCode` 的值。</span><span class="sxs-lookup"><span data-stu-id="51e08-350">When program execution stops at the breakpoint, view the value of `generatedCode`.</span></span>

![generatedCode 的文本可视化工具视图](razor/_static/tvr.png)

::: moniker-end

## <a name="view-lookups-and-case-sensitivity"></a><span data-ttu-id="51e08-352">视图查找和区分大小写</span><span class="sxs-lookup"><span data-stu-id="51e08-352">View lookups and case sensitivity</span></span>

<span data-ttu-id="51e08-353">Razor视图引擎为视图执行区分大小写的查找。</span><span class="sxs-lookup"><span data-stu-id="51e08-353">The Razor view engine performs case-sensitive lookups for views.</span></span> <span data-ttu-id="51e08-354">但是，实际查找取决于基础文件系统：</span><span class="sxs-lookup"><span data-stu-id="51e08-354">However, the actual lookup is determined by the underlying file system:</span></span>

* <span data-ttu-id="51e08-355">基于文件的源：</span><span class="sxs-lookup"><span data-stu-id="51e08-355">File based source:</span></span>
  * <span data-ttu-id="51e08-356">在使用不区分大小写的文件系统的操作系统（例如，Windows）上，物理文件提供程序查找不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="51e08-356">On operating systems with case insensitive file systems (for example, Windows), physical file provider lookups are case insensitive.</span></span> <span data-ttu-id="51e08-357">例如，`return View("Test")` 可匹配 */Views/Home/Test.cshtml*、*/Views/home/test.cshtml* 以及任何其他大小写变体。</span><span class="sxs-lookup"><span data-stu-id="51e08-357">For example, `return View("Test")` results in matches for */Views/Home/Test.cshtml*, */Views/home/test.cshtml*, and any other casing variant.</span></span>
  * <span data-ttu-id="51e08-358">在区分大小写的文件系统（例如，Linux、OSX 以及使用 `EmbeddedFileProvider` 构建的文件系统）上，查找区分大小写。</span><span class="sxs-lookup"><span data-stu-id="51e08-358">On case-sensitive file systems (for example, Linux, OSX, and with `EmbeddedFileProvider`), lookups are case-sensitive.</span></span> <span data-ttu-id="51e08-359">例如，`return View("Test")` 专门匹配 */Views/Home/Test.cshtml*。</span><span class="sxs-lookup"><span data-stu-id="51e08-359">For example, `return View("Test")` specifically matches */Views/Home/Test.cshtml*.</span></span>
* <span data-ttu-id="51e08-360">预编译视图：在 ASP.NET Core 2.0 及更高版本中，预编译视图查找在所有操作系统上均不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="51e08-360">Precompiled views: With ASP.NET Core 2.0 and later, looking up precompiled views is case insensitive on all operating systems.</span></span> <span data-ttu-id="51e08-361">该行为与 Windows 上物理文件提供程序的行为相同。</span><span class="sxs-lookup"><span data-stu-id="51e08-361">The behavior is identical to physical file provider's behavior on Windows.</span></span> <span data-ttu-id="51e08-362">如果两个预编译视图仅大小写不同，则查找的结果具有不确定性。</span><span class="sxs-lookup"><span data-stu-id="51e08-362">If two precompiled views differ only in case, the result of lookup is non-deterministic.</span></span>

<span data-ttu-id="51e08-363">建议开发人员将文件和目录名称的大小写与以下项的大小写匹配：</span><span class="sxs-lookup"><span data-stu-id="51e08-363">Developers are encouraged to match the casing of file and directory names to the casing of:</span></span>

* <span data-ttu-id="51e08-364">区域、控制器和操作名称。</span><span class="sxs-lookup"><span data-stu-id="51e08-364">Area, controller, and action names.</span></span>
* Razor<span data-ttu-id="51e08-365">页.</span><span class="sxs-lookup"><span data-stu-id="51e08-365"> Pages.</span></span>

<span data-ttu-id="51e08-366">匹配大小写可确保无论使用哪种基础文件系统，部署都能找到其视图。</span><span class="sxs-lookup"><span data-stu-id="51e08-366">Matching case ensures the deployments find their views regardless of the underlying file system.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="51e08-367">其他资源</span><span class="sxs-lookup"><span data-stu-id="51e08-367">Additional resources</span></span>

<span data-ttu-id="51e08-368">[使用 Razor ASP.NET Web 编程简介语法](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c)提供了许多用语法编程的示例 Razor 。</span><span class="sxs-lookup"><span data-stu-id="51e08-368">[Introduction to ASP.NET Web Programming Using the Razor Syntax](/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c) provides many samples of programming with Razor syntax.</span></span>
