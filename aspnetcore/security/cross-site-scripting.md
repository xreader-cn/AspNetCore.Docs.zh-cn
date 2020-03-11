---
title: 防止跨站点脚本 (XSS) 在 ASP.NET Core
author: rick-anderson
description: 了解有关跨站点脚本 (XSS) 和一些解决这一漏洞在 ASP.NET Core 应用程序技术。
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 1d6f605dc336d8768b8a47e4995f119d198a61af
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655074"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a>防止跨站点脚本 (XSS) 在 ASP.NET Core

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

跨站点脚本（XSS）是一个安全漏洞，攻击者可利用此漏洞将客户端脚本（通常为 JavaScript）放入网页中。 当其他用户加载受影响的页面时，攻击者的脚本将运行，使攻击者能够盗取 cookie 和会话令牌，通过 DOM 操作更改网页的内容，或者将浏览器重定向到另一页。 当应用程序采用用户输入并将其输出到页面而不进行验证、编码或转义时，通常会出现 XSS 漏洞。

## <a name="protecting-your-application-against-xss"></a>针对 XSS 保护应用程序

在基本级别，XSS 通过引诱你的应用程序将 `<script>` 标记插入所呈现的页中，或通过将 `On*` 事件插入到元素中来发挥作用。 开发人员应使用以下预防步骤来避免向其应用程序引入 XSS。

1. 请勿将不受信任的数据放入 HTML 输入，除非您按照以下步骤进行操作。 不受信任的数据是指攻击者可以控制的任何数据、HTML 窗体输入、查询字符串、HTTP 标头，甚至是源自数据库的数据，即使它们不能破坏您的应用程序，也可能会破坏您的数据库。

2. 在 HTML 元素中放置不受信任的数据之前，请确保它经过 HTML 编码。 HTML 编码使用 &lt; 等字符，并将其更改为 &amp;lt; 格式的安全窗体。

3. 将不受信任的数据放入 HTML 属性之前，请确保已对其进行 HTML 编码。 HTML 特性编码是 HTML 编码的超集，并对其他字符（如 "and"）进行编码。

4. 将不受信任的数据放入 JavaScript 之前，请将数据放置在运行时检索其内容的 HTML 元素中。 如果无法做到这一点，请确保数据为 JavaScript 编码。 JavaScript 编码会为 JavaScript 使用危险字符，并将其替换为其十六进制，例如 &lt; 将编码为 `\u003C`。

5. 将不受信任的数据置于 URL 查询字符串之前，请确保其 URL 已编码。

## <a name="html-encoding-using-razor"></a>使用 Razor 的 HTML 编码

MVC 中使用的 Razor 引擎会自动对源自变量的所有输出进行编码，除非您确实很难避免这样做。 当你使用 *@* 指令时，它将使用 HTML 属性编码规则。 HTML 特性编码是 HTML 编码的超集，这意味着您无需担心您应该使用 HTML 编码还是 HTML 特性编码。 您必须确保在 HTML 上下文中只使用 @，而不能在尝试将不受信任的输入直接插入 JavaScript 时使用。 标记帮助程序还将对在标记参数中使用的输入进行编码。

获取以下 Razor 视图：

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

此视图输出*untrustedInput*变量的内容。 此变量包含某些在 XSS 攻击中使用的字符，即 &lt;"和 &gt;。 检查源会显示编码为的呈现输出：

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> ASP.NET Core MVC 提供的 `HtmlString` 类在输出时不会自动编码。 请勿将此项与不受信任的输入结合使用，因为这将公开 XSS 漏洞。

## <a name="javascript-encoding-using-razor"></a>使用 Razor 的 JavaScript 编码

有时可能需要将值插入 JavaScript 中，以便在视图中进行处理。 可通过两种方式来执行此操作。 插入值的最安全方式是将值放入标记的数据属性中，并在 JavaScript 中检索它。 例如：

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

这会生成以下 HTML

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

当它运行时，将呈现以下内容：

```
<"123">
   <"123">
```

还可以直接调用 JavaScript 编码器：

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

这将在浏览器中呈现，如下所示：

```html
<script>
    document.write("\u003C\u0022123\u0022\u003E");
</script>
```

>[!WARNING]
> 请勿连接 JavaScript 中不受信任的输入来创建 DOM 元素。 应使用 `createElement()` 并适当地分配属性值（如 `node.TextContent=`），或使用 `element.SetAttribute()`/`element[attribute]=` 否则，你会自行向基于 DOM 的 XSS 公开。

## <a name="accessing-encoders-in-code"></a>在代码中访问编码器

HTML、JavaScript 和 URL 编码器通过两种方式提供给你的代码，你可以通过[依赖关系注入](xref:fundamentals/dependency-injection)来注入它们，也可以使用 `System.Text.Encodings.Web` 命名空间中包含的默认编码器。 如果使用默认编码器，则应用于字符范围的任何被视为安全的都不会生效-默认编码器可能会使用最安全的编码规则。

若要通过 DI 使用可配置编码器，你的构造函数应适当地采用*HtmlEncoder*、 *JavaScriptEncoder*和*UrlEncoder*参数。 例如，

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

## <a name="encoding-url-parameters"></a>编码 URL 参数

如果要使用不受信任的输入生成 URL 查询字符串作为值，请使用 `UrlEncoder` 对值进行编码。 例如，

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

编码后，Url-encodedvalue 变量将包含 `%22Quoted%20Value%20with%20spaces%20and%20%26%22`。 空格、引号、标点符号和其他不安全字符的百分比将编码为其十六进制值，例如，空格字符将变为 %20。

>[!WARNING]
> 请勿使用不受信任的输入作为 URL 路径的一部分。 始终将不受信任的输入传递为查询字符串值。

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a>自定义编码器

默认情况下，编码器使用限制为基本拉丁语 Unicode 范围的安全列表，并将该范围之外的所有字符编码为等效的字符代码。 此行为还会影响 Razor TagHelper 和 HtmlHelper 渲染，因为它将使用编码器输出字符串。

这种情况的原因是为了防止未知或将来的浏览器 bug （以前的浏览器 bug 基于非英语字符的处理来触发分析）。 如果你的网站大量使用非拉丁字符（如中文、西里尔语或其他），这可能不是你所希望的行为。

在 `ConfigureServices()`中，你可以自定义编码器安全列表，以包含在启动过程中适用于你的应用程序的 Unicode 范围。

例如，使用默认配置时，你可以使用 Razor HtmlHelper，如下所示;

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

当您查看网页的源时，您将看到它的呈现方式如下：中文文本已编码;

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

若要放宽编码器被视为安全字符，请将以下行插入到 `startup.cs`中的 `ConfigureServices()` 方法中：

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

此示例将安全列表扩大为包含 Unicode 范围 CjkUnifiedIdeographs。 呈现的输出现在变为

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

安全列表范围指定为 Unicode 代码图表，而不是语言。 [Unicode 标准](https://unicode.org/)包含可用来查找包含字符的图表的[代码图表](https://www.unicode.org/charts/index.html)列表。 每个编码器、Html、JavaScript 和 Url 都必须单独配置。

> [!NOTE]
> 安全列表的自定义仅影响通过 DI 的编码器。 如果通过 `System.Text.Encodings.Web.*Encoder.Default` 直接访问编码器，则默认情况下将使用仅限基本拉丁语的唯一安全。

## <a name="where-should-encoding-take-place"></a>编码应发生在何处？

一般接受的做法是，在输出和编码值中进行编码时，永远不应将其存储在数据库中。 使用输出点编码，可以更改数据的使用，例如，从 HTML 到查询字符串值。 它还使您能够轻松搜索您的数据，而无需在搜索之前对值进行编码，还可以利用对编码器进行的任何更改或 bug 修复。

## <a name="validation-as-an-xss-prevention-technique"></a>验证为 XSS 保护方法

验证是限制 XSS 攻击的有用工具。 例如，仅包含字符0-9 的数字字符串将不会触发 XSS 攻击。 在用户输入中接受 HTML 时，验证变得更加复杂。 分析 HTML 输入很困难，如果不可能。 Markdown 与带嵌入 HTML 的分析器结合使用，是接受丰富输入的一种更安全的选项。 请勿仅依赖于验证。 在输出之前始终对不受信任的输入进行编码，无论执行了哪些验证或清理。
