---
title: 防止跨站点脚本 (XSS) 在 ASP.NET Core
author: rick-anderson
description: 了解有关跨站点脚本 (XSS) 和一些解决这一漏洞在 ASP.NET Core 应用程序技术。
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 1e9e988be68313cfd493832519c1be89335d6e48
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815206"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>防止跨站点脚本 (XSS) 在 ASP.NET Core

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

跨站点脚本 (XSS) 是一个安全漏洞，这会使攻击者将客户端脚本 (通常是 JavaScript) 放入网页。 当其他用户加载攻击者的脚本将运行受影响的页面时，攻击者可以窃取 cookie 和会话令牌，更改通过 DOM 操作网页的内容或将浏览器重定向到另一页。 当应用程序接受用户输入，并将其输出到页面而无需验证、 编码或进行转义，通常会发生 XSS 漏洞。

## <a name="protecting-your-application-against-xss"></a>保护防御 XSS 应用程序

在基本级别 XSS 的工作方式是以欺骗手段使应用程序插入到`<script>`标记插入到呈现的页面，或通过插入`On*`事件的元素。 开发人员应使用以下的预防步骤以避免 XSS 引入其应用程序。

1. 永远不会，将不受信任的数据放入 HTML 输入，除非按照下面的步骤的其余部分。 不受信任的数据是可能由攻击者、 HTML 窗体输入、 查询字符串、 HTTP 标头，即使数据源自数据库，因为攻击者可能能够破坏您的数据库，即使它们不能破坏您的应用程序控制的任何数据。

2. 将 HTML 元素中的不受信任的数据之前，先确保它是 HTML 编码。 HTML 编码如采用字符&lt;并将其更改到之类的安全形式&amp;l t;

3. 将不受信任的数据放入 HTML 特性之前请确保它是 HTML 编码。 HTML 特性编码为 HTML 编码的超集，并将其他字符编码如"和。

4. 将不受信任的数据放入 JavaScript 之前将在运行时检索其内容的 HTML 元素中的数据。 如果不可行，请确保数据是 JavaScript 编码。 JavaScript 编码采用适用于 JavaScript 的危险的字符并将其替换为其十六进制表示，例如&lt;将编码为`\u003C`。

5. 将不受信任的数据放入 URL 查询字符串之前请确保它进行 URL 编码。

## <a name="html-encoding-using-razor"></a>使用 Razor 的 HTML 编码

Razor 引擎会自动在 MVC 中使用编码所有输出源自变量，除非您真正努力工作以防止其执行此操作。 它使用 HTML 特性编码规则，每当你使用 *@* 指令。 为 HTML 特性编码是 HTML 编码，这意味着无需关注是否应该使用 HTML 编码或 HTML 特性编码的超集。 您必须确保您仅使用在 HTML 上下文中，不是在尝试将直接插入 JavaScript 不受信任的输入时。 标记帮助程序还将对标记参数中使用的输入进行编码。

执行以下 Razor 视图：

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

此视图输出的内容*untrustedInput*变量。 此变量包含即 XSS 攻击中使用的一些字符&lt;，"和&gt;。 检查源显示呈现的输出编码为：

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC 提供`HtmlString`在输出时不自动编码的类。 这应永远不会用作与不受信任的输入结合使用这将公开 XSS 漏洞。

## <a name="javascript-encoding-using-razor"></a>使用 Razor 的 JavaScript 编码

可能的有时你想要将值插入到 JavaScript 来处理在视图中。 有两种方法可以实现此目的。 插入值的最安全方法是将值放在标记的数据的属性中，并在 JavaScript 中检索它。 例如:

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   <div
       id="injectedData"
       data-untrustedinput="@untrustedInput" />

   <script>
     var injectedData = document.getElementById("injectedData");

     // All clients
     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     // HTML 5 clients only
     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

这将生成以下 HTML

```html
<div
     id="injectedData"
     data-untrustedinput="&lt;&quot;123&quot;&gt;" />

   <script>
     var injectedData = document.getElementById("injectedData");

     var clientSideUntrustedInputOldStyle =
         injectedData.getAttribute("data-untrustedinput");

     var clientSideUntrustedInputHtml5 =
         injectedData.dataset.untrustedinput;

     document.write(clientSideUntrustedInputOldStyle);
     document.write("<br />")
     document.write(clientSideUntrustedInputHtml5);
   </script>
   ```

其中，运行时，将呈现以下：

```none
<"123">
   <"123">
   ```

您还可以直接调用 JavaScript 编码器：

```cshtml
@using System.Text.Encodings.Web;
   @inject JavaScriptEncoder encoder;

   @{
       var untrustedInput = "<\"123\">";
   }

   <script>
       document.write("@encoder.Encode(untrustedInput)");
   </script>
   ```

这将按如下所示呈现在浏览器中：

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> 不串联不受信任的输入，在 JavaScript 中创建 DOM 元素。 应使用`createElement()`并将属性值分配适当如`node.TextContent=`，或使用`element.SetAttribute()` / `element[attribute]=`否则向基于 DOM 的 XSS 公开自己。

## <a name="accessing-encoders-in-code"></a>访问代码中的编码器

HTML、 JavaScript 和 URL 编码器可供你的代码通过两种方式，您可以将它们通过注入[依赖关系注入](xref:fundamentals/dependency-injection)也可以使用中包含的默认编码器`System.Text.Encodings.Web`命名空间。 如果你使用默认编码器，然后对应用的任何字符范围来为安全处理不会生效-默认编码器使用可能的最安全的编码规则。

若要使用通过 DI 在构造函数应采取的可配置编码器*HtmlEncoder*， *JavaScriptEncoder*并*UrlEncoder*作为适当的参数。 例如，

```csharp
public class HomeController : Controller
   {
       HtmlEncoder _htmlEncoder;
       JavaScriptEncoder _javaScriptEncoder;
       UrlEncoder _urlEncoder;

       public HomeController(HtmlEncoder htmlEncoder,
                             JavaScriptEncoder javascriptEncoder,
                             UrlEncoder urlEncoder)
       {
           _htmlEncoder = htmlEncoder;
           _javaScriptEncoder = javascriptEncoder;
           _urlEncoder = urlEncoder;
       }
   }
   ```

## <a name="encoding-url-parameters"></a>编码的 URL 参数

如果你想要生成的 URL 查询字符串以不受信任的输入作为值使用`UrlEncoder`对值进行编码。 例如，应用于对象的

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

编码 encodedValue 后变量将包含`%22Quoted%20Value%20with%20spaces%20and%20%26%22`。 空格、 引号、 标点符号和其他不安全字符百分比编码为其十六进制值，例如空格字符将变成 %20。

>[!WARNING]
> 不要使用不受信任的输入的 URL 路径的一部分。 始终将不受信任的输入传递作为查询字符串值。

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>自定义编码器

默认情况下编码器使用限制为基本拉丁语 Unicode 范围的安全列表，并为其等效的字符代码在该范围以外的所有字符进行都编码。 此行为还会影响 Razor TagHelper 和 HtmlHelper 呈现，因为它将使用编码器输出字符串。

这背后的原因是为了防止未知或将来的浏览器 bug （上一个浏览器 bug 具有掣肘： 分析基于非英语字符的处理）。 如果您的网站重度使用的非拉丁字符，例如中文，西里尔文或其他人这是可能不希望的行为。

你可以自定义编码器安全列表，包括 Unicode 范围适合于你的应用程序启动过程中，在`ConfigureServices()`。

例如，使用默认配置可能会使用 Razor HtmlHelper 如下所示;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

当你查看网页的源时将看到已呈现，如下所示，使用中文文本编码;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

若要扩大范围的字符视为安全编码器插入以下行`ConfigureServices()`中的方法`startup.cs`;

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

此示例将加宽安全列表，以包括 Unicode 范围 CjkUnifiedIdeographs。 现在将变为呈现的输出

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

安全列表范围被指定为 Unicode 编码图表，不语言。 [Unicode 标准](https://unicode.org/)具有一系列[代码图表](https://www.unicode.org/charts/index.html)可用于查找包含你字符的图表。 每个编码器，Html、 JavaScript 和 Url，必须单独配置。

> [!NOTE]
> 自定义的安全列表只会影响源自通过 DI 的编码器。 如果你直接访问通过编码器`System.Text.Encodings.Web.*Encoder.Default`然后默认情况下，基本拉丁语将使用仅安全列表。

## <a name="where-should-encoding-take-place"></a>编码 take 应放置位置？

常规接受的做法是编码发生在输出时和编码的值应永远不会存储在数据库中。 编码在输出时，可更改数据，例如，使用从为查询字符串值的 HTML。 它还使您可以轻松地搜索你的数据而无需对值进行编码搜索之前，并允许您充分任何的利用更改或对编码器进行 bug 修复。

## <a name="validation-as-an-xss-prevention-technique"></a>作为 XSS 防护技术验证

验证过程可能在限制的 XSS 攻击的有用工具。 例如，包含字符 0-9 的数字字符串将不会触发 XSS 攻击。 接受用户输入中的 HTML 时，验证变得更加复杂。 分析 HTML 输入是困难的即使不是不可能。 Markdown，结合一个分析器，它去除嵌入式的 HTML 是用于接收丰富输入比较安全的选项。 永远不会依赖于单独的验证。 始终对不受信任的输入输出，无论执行何种验证或清理之前进行编码。
