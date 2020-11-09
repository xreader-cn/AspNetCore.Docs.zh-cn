---
title: 阻止跨站点脚本 (XSS) 在 ASP.NET Core
author: rick-anderson
description: 了解跨站点脚本 (XSS) 以及在 ASP.NET Core 应用程序中解决此漏洞的方法。
ms.author: riande
ms.date: 10/02/2018
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/cross-site-scripting
ms.openlocfilehash: 1c90a786efe8c3c205a729a2da9d3a99d0222012
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053080"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="48060-103">阻止跨站点脚本 (XSS) 在 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="48060-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="48060-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="48060-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="48060-105">跨站点脚本 (XSS) 是一个安全漏洞，攻击者可利用此漏洞将客户端脚本 (通常是 JavaScript) 到网页中。</span><span class="sxs-lookup"><span data-stu-id="48060-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="48060-106">当其他用户加载受影响的页面时，攻击者的脚本将运行，使攻击者能够盗取 cookie 和会话令牌，通过 DOM 操作更改网页的内容，或者将浏览器重定向到另一页。</span><span class="sxs-lookup"><span data-stu-id="48060-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="48060-107">当应用程序采用用户输入并将其输出到页面而不进行验证、编码或转义时，通常会出现 XSS 漏洞。</span><span class="sxs-lookup"><span data-stu-id="48060-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="48060-108">针对 XSS 保护应用程序</span><span class="sxs-lookup"><span data-stu-id="48060-108">Protecting your application against XSS</span></span>

<span data-ttu-id="48060-109">在基本级别上，XSS 通过引诱你的应用程序 `<script>` 将标记插入到呈现的页中，或通过 `On*` 将事件插入到元素中来发挥作用。</span><span class="sxs-lookup"><span data-stu-id="48060-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="48060-110">开发人员应使用以下预防步骤来避免向其应用程序引入 XSS。</span><span class="sxs-lookup"><span data-stu-id="48060-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="48060-111">请勿将不受信任的数据放入 HTML 输入，除非您按照以下步骤进行操作。</span><span class="sxs-lookup"><span data-stu-id="48060-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="48060-112">不受信任的数据是指攻击者可以控制的任何数据、HTML 窗体输入、查询字符串、HTTP 标头，甚至是源自数据库的数据，即使它们不能破坏您的应用程序，也可能会破坏您的数据库。</span><span class="sxs-lookup"><span data-stu-id="48060-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="48060-113">在 HTML 元素中放置不受信任的数据之前，请确保它经过 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="48060-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="48060-114">HTML 编码使用等字符 &lt; ，并将其更改为像 lt 这样的安全形式 &amp; ;</span><span class="sxs-lookup"><span data-stu-id="48060-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="48060-115">将不受信任的数据放入 HTML 属性之前，请确保已对其进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="48060-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="48060-116">HTML 特性编码是 HTML 编码的超集，并对其他字符（如 "and"）进行编码。</span><span class="sxs-lookup"><span data-stu-id="48060-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="48060-117">将不受信任的数据放入 JavaScript 之前，请将数据放置在运行时检索其内容的 HTML 元素中。</span><span class="sxs-lookup"><span data-stu-id="48060-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="48060-118">如果无法做到这一点，请确保数据为 JavaScript 编码。</span><span class="sxs-lookup"><span data-stu-id="48060-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="48060-119">JavaScript 编码会为 JavaScript 使用危险字符，并将其替换为其十六进制，例如，将 &lt; 编码为 `\u003C` 。</span><span class="sxs-lookup"><span data-stu-id="48060-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="48060-120">将不受信任的数据置于 URL 查询字符串之前，请确保其 URL 已编码。</span><span class="sxs-lookup"><span data-stu-id="48060-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-no-locrazor"></a><span data-ttu-id="48060-121">HTML 编码使用 Razor</span><span class="sxs-lookup"><span data-stu-id="48060-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="48060-122">RazorMVC 中使用的引擎会自动对源自变量的所有输出进行编码，除非您确实很难避免这样做。</span><span class="sxs-lookup"><span data-stu-id="48060-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="48060-123">使用指令时，它将使用 HTML 属性编码规则 *@* 。</span><span class="sxs-lookup"><span data-stu-id="48060-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="48060-124">HTML 特性编码是 HTML 编码的超集，这意味着您无需担心您应该使用 HTML 编码还是 HTML 特性编码。</span><span class="sxs-lookup"><span data-stu-id="48060-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="48060-125">您必须确保在 HTML 上下文中只使用 @，而不能在尝试将不受信任的输入直接插入 JavaScript 时使用。</span><span class="sxs-lookup"><span data-stu-id="48060-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="48060-126">标记帮助程序还将对在标记参数中使用的输入进行编码。</span><span class="sxs-lookup"><span data-stu-id="48060-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="48060-127">获取以下 Razor 视图：</span><span class="sxs-lookup"><span data-stu-id="48060-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="48060-128">此视图输出 *untrustedInput* 变量的内容。</span><span class="sxs-lookup"><span data-stu-id="48060-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="48060-129">此变量包括在 XSS 攻击中使用的一些字符，即 &lt; "和 &gt; 。</span><span class="sxs-lookup"><span data-stu-id="48060-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="48060-130">检查源会显示编码为的呈现输出：</span><span class="sxs-lookup"><span data-stu-id="48060-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="48060-131">ASP.NET Core MVC 提供的 `HtmlString` 类在输出时不自动编码。</span><span class="sxs-lookup"><span data-stu-id="48060-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="48060-132">请勿将此项与不受信任的输入结合使用，因为这将公开 XSS 漏洞。</span><span class="sxs-lookup"><span data-stu-id="48060-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-no-locrazor"></a><span data-ttu-id="48060-133">JavaScript 编码使用 Razor</span><span class="sxs-lookup"><span data-stu-id="48060-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="48060-134">有时可能需要将值插入 JavaScript 中，以便在视图中进行处理。</span><span class="sxs-lookup"><span data-stu-id="48060-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="48060-135">可通过两种方式来执行此操作。</span><span class="sxs-lookup"><span data-stu-id="48060-135">There are two ways to do this.</span></span> <span data-ttu-id="48060-136">插入值的最安全方式是将值放入标记的数据属性中，并在 JavaScript 中检索它。</span><span class="sxs-lookup"><span data-stu-id="48060-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="48060-137">例如： 。</span><span class="sxs-lookup"><span data-stu-id="48060-137">For example:</span></span>

```cshtml
@{
    var untrustedInput = "<script>alert(1)</script>";
}

<div id="injectedData"
     data-untrustedinput="@untrustedInput" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it
    // can lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="48060-138">上述标记生成以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="48060-138">The preceding markup generates the following HTML:</span></span>

```html
<div id="injectedData"
     data-untrustedinput="&lt;script&gt;alert(1)&lt;/script&gt;" />

<div id="scriptedWrite" />
<div id="scriptedWrite-html5" />

<script>
    var injectedData = document.getElementById("injectedData");

    // All clients
    var clientSideUntrustedInputOldStyle =
        injectedData.getAttribute("data-untrustedinput");

    // HTML 5 clients only
    var clientSideUntrustedInputHtml5 =
        injectedData.dataset.untrustedinput;

    // Put the injected, untrusted data into the scriptedWrite div tag.
    // Do NOT use document.write() on dynamically generated data as it can
    // lead to XSS.

    document.getElementById("scriptedWrite").innerText += clientSideUntrustedInputOldStyle;

    // Or you can use createElement() to dynamically create document elements
    // This time we're using textContent to ensure the data is properly encoded.
    var x = document.createElement("div");
    x.textContent = clientSideUntrustedInputHtml5;
    document.body.appendChild(x);

    // You can also use createTextNode on an element to ensure data is properly encoded.
    var y = document.createElement("div");
    y.appendChild(document.createTextNode(clientSideUntrustedInputHtml5));
    document.body.appendChild(y);

</script>
   ```

<span data-ttu-id="48060-139">前面的代码生成以下输出：</span><span class="sxs-lookup"><span data-stu-id="48060-139">The preceding code generates the following output:</span></span>

```
<script>alert(1)</script>
<script>alert(1)</script>
<script>alert(1)</script>
```

>[!WARNING]
> <span data-ttu-id="48060-140">确实要在 JavaScript 中 **连接不受** 信任的输入，以创建 DOM 元素或 `document.write()` 在动态生成的内容上使用。</span><span class="sxs-lookup"><span data-stu-id="48060-140">Do \* **NOT** _ concatenate untrusted input in JavaScript to create DOM elements or use `document.write()` on dynamically generated content.</span></span>
>
> <span data-ttu-id="48060-141">使用以下方法之一来阻止将代码公开给基于 DOM 的 XSS： _ `createElement()` 并使用适当的方法或属性（如或节点）分配属性值 `node.textContent=` 。InnerText = '。</span><span class="sxs-lookup"><span data-stu-id="48060-141">Use one of the following approaches to prevent code from being exposed to DOM-based XSS: _ `createElement()` and assign property values with appropriate methods or properties such as `node.textContent=` or node.InnerText=\`.</span></span>
> * <span data-ttu-id="48060-142">`document.CreateTextNode()` 并将其追加到适当的 DOM 位置。</span><span class="sxs-lookup"><span data-stu-id="48060-142">`document.CreateTextNode()` and append it in the appropriate DOM location.</span></span>
> * `element.SetAttribute()`
> * `element[attribute]=`

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="48060-143">在代码中访问编码器</span><span class="sxs-lookup"><span data-stu-id="48060-143">Accessing encoders in code</span></span>

<span data-ttu-id="48060-144">HTML、JavaScript 和 URL 编码器通过两种方式提供给你的代码，你可以通过 [依赖关系注入](xref:fundamentals/dependency-injection) 来注入它们，也可以使用命名空间中包含的默认编码器 `System.Text.Encodings.Web` 。</span><span class="sxs-lookup"><span data-stu-id="48060-144">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="48060-145">如果使用默认编码器，则应用于字符范围的任何被视为安全的都不会生效-默认编码器可能会使用最安全的编码规则。</span><span class="sxs-lookup"><span data-stu-id="48060-145">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="48060-146">若要通过 DI 使用可配置编码器，你的构造函数应适当地采用 *HtmlEncoder* 、 *JavaScriptEncoder* 和 *UrlEncoder* 参数。</span><span class="sxs-lookup"><span data-stu-id="48060-146">To use the configurable encoders via DI your constructors should take an *HtmlEncoder* , *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="48060-147">例如，</span><span class="sxs-lookup"><span data-stu-id="48060-147">For example;</span></span>

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

## <a name="encoding-url-parameters"></a><span data-ttu-id="48060-148">编码 URL 参数</span><span class="sxs-lookup"><span data-stu-id="48060-148">Encoding URL Parameters</span></span>

<span data-ttu-id="48060-149">如果要使用不受信任的输入生成 URL 查询字符串作为值，请使用 `UrlEncoder` 对值进行编码。</span><span class="sxs-lookup"><span data-stu-id="48060-149">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="48060-150">例如，</span><span class="sxs-lookup"><span data-stu-id="48060-150">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="48060-151">编码后，Url-encodedvalue 变量将包含 `%22Quoted%20Value%20with%20spaces%20and%20%26%22` 。</span><span class="sxs-lookup"><span data-stu-id="48060-151">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="48060-152">空格、引号、标点符号和其他不安全字符的百分比将编码为其十六进制值，例如，空格字符将变为 %20。</span><span class="sxs-lookup"><span data-stu-id="48060-152">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="48060-153">请勿使用不受信任的输入作为 URL 路径的一部分。</span><span class="sxs-lookup"><span data-stu-id="48060-153">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="48060-154">始终将不受信任的输入传递为查询字符串值。</span><span class="sxs-lookup"><span data-stu-id="48060-154">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="48060-155">自定义编码器</span><span class="sxs-lookup"><span data-stu-id="48060-155">Customizing the Encoders</span></span>

<span data-ttu-id="48060-156">默认情况下，编码器使用限制为基本拉丁语 Unicode 范围的安全列表，并将该范围之外的所有字符编码为等效的字符代码。</span><span class="sxs-lookup"><span data-stu-id="48060-156">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="48060-157">此行为还会影响 Razor TagHelper 和 HtmlHelper 渲染，因为它将使用编码器输出字符串。</span><span class="sxs-lookup"><span data-stu-id="48060-157">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="48060-158">这种情况的原因是为了防止未知或将来的浏览器 bug， (以前的浏览器 bug 在处理) 的非英语字符时，将会触发分析。</span><span class="sxs-lookup"><span data-stu-id="48060-158">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="48060-159">如果你的网站大量使用非拉丁字符（如中文、西里尔语或其他），这可能不是你所希望的行为。</span><span class="sxs-lookup"><span data-stu-id="48060-159">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="48060-160">在中，你可以自定义编码器安全列表，以包含在启动过程中适用于你的应用程序的 Unicode 范围 `ConfigureServices()` 。</span><span class="sxs-lookup"><span data-stu-id="48060-160">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="48060-161">例如，使用默认配置时，可以使用 HtmlHelper， Razor 如下所示：</span><span class="sxs-lookup"><span data-stu-id="48060-161">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="48060-162">当您查看网页的源时，您将看到它的呈现方式如下：中文文本已编码;</span><span class="sxs-lookup"><span data-stu-id="48060-162">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="48060-163">若要放宽编码器被视为安全字符，请将以下行插入到 `ConfigureServices()` 中的方法 `startup.cs` ;</span><span class="sxs-lookup"><span data-stu-id="48060-163">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="48060-164">此示例将安全列表扩大为包含 Unicode 范围 CjkUnifiedIdeographs。</span><span class="sxs-lookup"><span data-stu-id="48060-164">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="48060-165">呈现的输出现在变为</span><span class="sxs-lookup"><span data-stu-id="48060-165">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="48060-166">安全列表范围指定为 Unicode 代码图表，而不是语言。</span><span class="sxs-lookup"><span data-stu-id="48060-166">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="48060-167">[Unicode 标准](https://unicode.org/)包含可用来查找包含字符的图表的[代码图表](https://www.unicode.org/charts/index.html)列表。</span><span class="sxs-lookup"><span data-stu-id="48060-167">The [Unicode standard](https://unicode.org/) has a list of [code charts](https://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="48060-168">每个编码器、Html、JavaScript 和 Url 都必须单独配置。</span><span class="sxs-lookup"><span data-stu-id="48060-168">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="48060-169">安全列表的自定义仅影响通过 DI 的编码器。</span><span class="sxs-lookup"><span data-stu-id="48060-169">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="48060-170">如果直接通过访问编码器 `System.Text.Encodings.Web.*Encoder.Default` ，则默认情况下将使用仅限基本拉丁语的唯一安全安全。</span><span class="sxs-lookup"><span data-stu-id="48060-170">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="48060-171">编码应发生在何处？</span><span class="sxs-lookup"><span data-stu-id="48060-171">Where should encoding take place?</span></span>

<span data-ttu-id="48060-172">一般接受的做法是，在输出和编码值中进行编码时，永远不应将其存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="48060-172">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="48060-173">使用输出点编码，可以更改数据的使用，例如，从 HTML 到查询字符串值。</span><span class="sxs-lookup"><span data-stu-id="48060-173">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="48060-174">它还使您能够轻松搜索您的数据，而无需在搜索之前对值进行编码，还可以利用对编码器进行的任何更改或 bug 修复。</span><span class="sxs-lookup"><span data-stu-id="48060-174">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="48060-175">验证为 XSS 保护方法</span><span class="sxs-lookup"><span data-stu-id="48060-175">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="48060-176">验证是限制 XSS 攻击的有用工具。</span><span class="sxs-lookup"><span data-stu-id="48060-176">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="48060-177">例如，仅包含字符0-9 的数字字符串将不会触发 XSS 攻击。</span><span class="sxs-lookup"><span data-stu-id="48060-177">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="48060-178">在用户输入中接受 HTML 时，验证变得更加复杂。</span><span class="sxs-lookup"><span data-stu-id="48060-178">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="48060-179">分析 HTML 输入很困难，如果不可能。</span><span class="sxs-lookup"><span data-stu-id="48060-179">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="48060-180">Markdown 与带嵌入 HTML 的分析器结合使用，是接受丰富输入的一种更安全的选项。</span><span class="sxs-lookup"><span data-stu-id="48060-180">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="48060-181">请勿仅依赖于验证。</span><span class="sxs-lookup"><span data-stu-id="48060-181">Never rely on validation alone.</span></span> <span data-ttu-id="48060-182">在输出之前始终对不受信任的输入进行编码，无论执行了哪些验证或清理。</span><span class="sxs-lookup"><span data-stu-id="48060-182">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
