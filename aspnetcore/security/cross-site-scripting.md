---
title: 防止跨站点脚本 (XSS) 在 ASP.NET Core
author: rick-anderson
description: 了解有关跨站点脚本 (XSS) 和一些解决这一漏洞在 ASP.NET Core 应用程序技术。
ms.author: riande
ms.date: 10/02/2018
uid: security/cross-site-scripting
ms.openlocfilehash: 50f0211a2c64708d9b788dd10ce9064e66014d55
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895344"
---
# <a name="prevent-cross-site-scripting-xss-in-aspnet-core"></a><span data-ttu-id="896f4-103">防止跨站点脚本 (XSS) 在 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="896f4-103">Prevent Cross-Site Scripting (XSS) in ASP.NET Core</span></span>

<span data-ttu-id="896f4-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="896f4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="896f4-105">跨站点脚本 (XSS) 是一个安全漏洞，这会使攻击者将客户端脚本 (通常是 JavaScript) 放入网页。</span><span class="sxs-lookup"><span data-stu-id="896f4-105">Cross-Site Scripting (XSS) is a security vulnerability which enables an attacker to place client side scripts (usually JavaScript) into web pages.</span></span> <span data-ttu-id="896f4-106">当其他用户加载攻击者的脚本将运行受影响的页面时，攻击者可以窃取 cookie 和会话令牌，更改通过 DOM 操作网页的内容或将浏览器重定向到另一页。</span><span class="sxs-lookup"><span data-stu-id="896f4-106">When other users load affected pages the attacker's scripts will run, enabling the attacker to steal cookies and session tokens, change the contents of the web page through DOM manipulation or redirect the browser to another page.</span></span> <span data-ttu-id="896f4-107">当应用程序接受用户输入，并将其输出到页面而无需验证、 编码或进行转义，通常会发生 XSS 漏洞。</span><span class="sxs-lookup"><span data-stu-id="896f4-107">XSS vulnerabilities generally occur when an application takes user input and outputs it to a page without validating, encoding or escaping it.</span></span>

## <a name="protecting-your-application-against-xss"></a><span data-ttu-id="896f4-108">保护防御 XSS 应用程序</span><span class="sxs-lookup"><span data-stu-id="896f4-108">Protecting your application against XSS</span></span>

<span data-ttu-id="896f4-109">在基本级别 XSS 的工作方式是以欺骗手段使应用程序插入到`<script>`标记插入到呈现的页面，或通过插入`On*`事件的元素。</span><span class="sxs-lookup"><span data-stu-id="896f4-109">At a basic level XSS works by tricking your application into inserting a `<script>` tag into your rendered page, or by inserting an `On*` event into an element.</span></span> <span data-ttu-id="896f4-110">开发人员应使用以下的预防步骤以避免 XSS 引入其应用程序。</span><span class="sxs-lookup"><span data-stu-id="896f4-110">Developers should use the following prevention steps to avoid introducing XSS into their application.</span></span>

1. <span data-ttu-id="896f4-111">永远不会，将不受信任的数据放入 HTML 输入，除非按照下面的步骤的其余部分。</span><span class="sxs-lookup"><span data-stu-id="896f4-111">Never put untrusted data into your HTML input, unless you follow the rest of the steps below.</span></span> <span data-ttu-id="896f4-112">不受信任的数据是可能由攻击者、 HTML 窗体输入、 查询字符串、 HTTP 标头，即使数据源自数据库，因为攻击者可能能够破坏您的数据库，即使它们不能破坏您的应用程序控制的任何数据。</span><span class="sxs-lookup"><span data-stu-id="896f4-112">Untrusted data is any data that may be controlled by an attacker, HTML form inputs, query strings, HTTP headers, even data sourced from a database as an attacker may be able to breach your database even if they cannot breach your application.</span></span>

2. <span data-ttu-id="896f4-113">将 HTML 元素中的不受信任的数据之前，先确保它是 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="896f4-113">Before putting untrusted data inside an HTML element ensure it's HTML encoded.</span></span> <span data-ttu-id="896f4-114">HTML 编码如采用字符&lt;并将其更改到之类的安全形式&amp;l t;</span><span class="sxs-lookup"><span data-stu-id="896f4-114">HTML encoding takes characters such as &lt; and changes them into a safe form like &amp;lt;</span></span>

3. <span data-ttu-id="896f4-115">将不受信任的数据放入 HTML 特性之前请确保它是 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="896f4-115">Before putting untrusted data into an HTML attribute ensure it's HTML encoded.</span></span> <span data-ttu-id="896f4-116">HTML 特性编码为 HTML 编码的超集，并将其他字符编码如"和。</span><span class="sxs-lookup"><span data-stu-id="896f4-116">HTML attribute encoding is a superset of HTML encoding and encodes additional characters such as " and '.</span></span>

4. <span data-ttu-id="896f4-117">将不受信任的数据放入 JavaScript 之前将在运行时检索其内容的 HTML 元素中的数据。</span><span class="sxs-lookup"><span data-stu-id="896f4-117">Before putting untrusted data into JavaScript place the data in an HTML element whose contents you retrieve at runtime.</span></span> <span data-ttu-id="896f4-118">如果不可行，请确保数据是 JavaScript 编码。</span><span class="sxs-lookup"><span data-stu-id="896f4-118">If this isn't possible, then ensure the data is JavaScript encoded.</span></span> <span data-ttu-id="896f4-119">JavaScript 编码采用适用于 JavaScript 的危险的字符并将其替换为其十六进制表示，例如&lt;将编码为`\u003C`。</span><span class="sxs-lookup"><span data-stu-id="896f4-119">JavaScript encoding takes dangerous characters for JavaScript and replaces them with their hex, for example &lt; would be encoded as `\u003C`.</span></span>

5. <span data-ttu-id="896f4-120">将不受信任的数据放入 URL 查询字符串之前请确保它进行 URL 编码。</span><span class="sxs-lookup"><span data-stu-id="896f4-120">Before putting untrusted data into a URL query string ensure it's URL encoded.</span></span>

## <a name="html-encoding-using-razor"></a><span data-ttu-id="896f4-121">使用 Razor 的 HTML 编码</span><span class="sxs-lookup"><span data-stu-id="896f4-121">HTML Encoding using Razor</span></span>

<span data-ttu-id="896f4-122">Razor 引擎会自动在 MVC 中使用编码所有输出源自变量，除非您真正努力工作以防止其执行此操作。</span><span class="sxs-lookup"><span data-stu-id="896f4-122">The Razor engine used in MVC automatically encodes all output sourced from variables, unless you work really hard to prevent it doing so.</span></span> <span data-ttu-id="896f4-123">它使用 HTML 特性编码规则，每当你使用*@* 指令。</span><span class="sxs-lookup"><span data-stu-id="896f4-123">It uses HTML attribute encoding rules whenever you use the *@* directive.</span></span> <span data-ttu-id="896f4-124">为 HTML 特性编码是 HTML 编码，这意味着无需关注是否应该使用 HTML 编码或 HTML 特性编码的超集。</span><span class="sxs-lookup"><span data-stu-id="896f4-124">As HTML attribute encoding is a superset of HTML encoding this means you don't have to concern yourself with whether you should use HTML encoding or HTML attribute encoding.</span></span> <span data-ttu-id="896f4-125">您必须确保您仅使用在 HTML 上下文中，不是在尝试将直接插入 JavaScript 不受信任的输入时。</span><span class="sxs-lookup"><span data-stu-id="896f4-125">You must ensure that you only use @ in an HTML context, not when attempting to insert untrusted input directly into JavaScript.</span></span> <span data-ttu-id="896f4-126">标记帮助程序还将对标记参数中使用的输入进行编码。</span><span class="sxs-lookup"><span data-stu-id="896f4-126">Tag helpers will also encode input you use in tag parameters.</span></span>

<span data-ttu-id="896f4-127">执行以下 Razor 视图：</span><span class="sxs-lookup"><span data-stu-id="896f4-127">Take the following Razor view:</span></span>

```cshtml
@{
       var untrustedInput = "<\"123\">";
   }

   @untrustedInput
   ```

<span data-ttu-id="896f4-128">此视图输出的内容*untrustedInput*变量。</span><span class="sxs-lookup"><span data-stu-id="896f4-128">This view outputs the contents of the *untrustedInput* variable.</span></span> <span data-ttu-id="896f4-129">此变量包含即 XSS 攻击中使用的一些字符&lt;，"和&gt;。</span><span class="sxs-lookup"><span data-stu-id="896f4-129">This variable includes some characters which are used in XSS attacks, namely &lt;, " and &gt;.</span></span> <span data-ttu-id="896f4-130">检查源显示呈现的输出编码为：</span><span class="sxs-lookup"><span data-stu-id="896f4-130">Examining the source shows the rendered output encoded as:</span></span>

```html
&lt;&quot;123&quot;&gt;
   ```

>[!WARNING]
> <span data-ttu-id="896f4-131">ASP.NET Core MVC 提供`HtmlString`在输出时不自动编码的类。</span><span class="sxs-lookup"><span data-stu-id="896f4-131">ASP.NET Core MVC provides an `HtmlString` class which isn't automatically encoded upon output.</span></span> <span data-ttu-id="896f4-132">这应永远不会用作与不受信任的输入结合使用这将公开 XSS 漏洞。</span><span class="sxs-lookup"><span data-stu-id="896f4-132">This should never be used in combination with untrusted input as this will expose an XSS vulnerability.</span></span>

## <a name="javascript-encoding-using-razor"></a><span data-ttu-id="896f4-133">使用 Razor 的 JavaScript 编码</span><span class="sxs-lookup"><span data-stu-id="896f4-133">JavaScript Encoding using Razor</span></span>

<span data-ttu-id="896f4-134">可能的有时你想要将值插入到 JavaScript 来处理在视图中。</span><span class="sxs-lookup"><span data-stu-id="896f4-134">There may be times you want to insert a value into JavaScript to process in your view.</span></span> <span data-ttu-id="896f4-135">有两种方法可以实现此目的。</span><span class="sxs-lookup"><span data-stu-id="896f4-135">There are two ways to do this.</span></span> <span data-ttu-id="896f4-136">插入值的最安全方法是将值放在标记的数据的属性中，并在 JavaScript 中检索它。</span><span class="sxs-lookup"><span data-stu-id="896f4-136">The safest way to insert values is to place the value in a data attribute of a tag and retrieve it in your JavaScript.</span></span> <span data-ttu-id="896f4-137">例如：</span><span class="sxs-lookup"><span data-stu-id="896f4-137">For example:</span></span>

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

<span data-ttu-id="896f4-138">这将生成以下 HTML</span><span class="sxs-lookup"><span data-stu-id="896f4-138">This will produce the following HTML</span></span>

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

<span data-ttu-id="896f4-139">其中，运行时，将呈现以下：</span><span class="sxs-lookup"><span data-stu-id="896f4-139">Which, when it runs, will render the following:</span></span>

```none
<"123">
   <"123">
   ```

<span data-ttu-id="896f4-140">您还可以直接调用 JavaScript 编码器：</span><span class="sxs-lookup"><span data-stu-id="896f4-140">You can also call the JavaScript encoder directly:</span></span>

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

<span data-ttu-id="896f4-141">这将按如下所示呈现在浏览器中：</span><span class="sxs-lookup"><span data-stu-id="896f4-141">This will render in the browser as follows:</span></span>

```html
<script>
       document.write("\u003C\u0022123\u0022\u003E");
   </script>
   ```

>[!WARNING]
> <span data-ttu-id="896f4-142">不串联不受信任的输入，在 JavaScript 中创建 DOM 元素。</span><span class="sxs-lookup"><span data-stu-id="896f4-142">Don't concatenate untrusted input in JavaScript to create DOM elements.</span></span> <span data-ttu-id="896f4-143">应使用`createElement()`并将属性值分配适当如`node.TextContent=`，或使用`element.SetAttribute()` / `element[attribute]=`否则向基于 DOM 的 XSS 公开自己。</span><span class="sxs-lookup"><span data-stu-id="896f4-143">You should use `createElement()` and assign property values appropriately such as `node.TextContent=`, or use `element.SetAttribute()`/`element[attribute]=` otherwise you expose yourself to DOM-based XSS.</span></span>

## <a name="accessing-encoders-in-code"></a><span data-ttu-id="896f4-144">访问代码中的编码器</span><span class="sxs-lookup"><span data-stu-id="896f4-144">Accessing encoders in code</span></span>

<span data-ttu-id="896f4-145">HTML、 JavaScript 和 URL 编码器可供你的代码通过两种方式，您可以将它们通过注入[依赖关系注入](xref:fundamentals/dependency-injection)也可以使用中包含的默认编码器`System.Text.Encodings.Web`命名空间。</span><span class="sxs-lookup"><span data-stu-id="896f4-145">The HTML, JavaScript and URL encoders are available to your code in two ways, you can inject them via [dependency injection](xref:fundamentals/dependency-injection) or you can use the default encoders contained in the `System.Text.Encodings.Web` namespace.</span></span> <span data-ttu-id="896f4-146">如果你使用默认编码器，然后对应用的任何字符范围来为安全处理不会生效-默认编码器使用可能的最安全的编码规则。</span><span class="sxs-lookup"><span data-stu-id="896f4-146">If you use the default encoders then any  you applied to character ranges to be treated as safe won't take effect - the default encoders use the safest encoding rules possible.</span></span>

<span data-ttu-id="896f4-147">若要使用通过 DI 在构造函数应采取的可配置编码器*HtmlEncoder*， *JavaScriptEncoder*并*UrlEncoder*作为适当的参数。</span><span class="sxs-lookup"><span data-stu-id="896f4-147">To use the configurable encoders via DI your constructors should take an *HtmlEncoder*, *JavaScriptEncoder* and *UrlEncoder* parameter as appropriate.</span></span> <span data-ttu-id="896f4-148">例如，</span><span class="sxs-lookup"><span data-stu-id="896f4-148">For example;</span></span>

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

## <a name="encoding-url-parameters"></a><span data-ttu-id="896f4-149">编码的 URL 参数</span><span class="sxs-lookup"><span data-stu-id="896f4-149">Encoding URL Parameters</span></span>

<span data-ttu-id="896f4-150">如果你想要生成的 URL 查询字符串以不受信任的输入作为值使用`UrlEncoder`对值进行编码。</span><span class="sxs-lookup"><span data-stu-id="896f4-150">If you want to build a URL query string with untrusted input as a value use the `UrlEncoder` to encode the value.</span></span> <span data-ttu-id="896f4-151">例如，应用于对象的</span><span class="sxs-lookup"><span data-stu-id="896f4-151">For example,</span></span>

```csharp
var example = "\"Quoted Value with spaces and &\"";
   var encodedValue = _urlEncoder.Encode(example);
   ```

<span data-ttu-id="896f4-152">编码 encodedValue 后变量将包含`%22Quoted%20Value%20with%20spaces%20and%20%26%22`。</span><span class="sxs-lookup"><span data-stu-id="896f4-152">After encoding the encodedValue variable will contain `%22Quoted%20Value%20with%20spaces%20and%20%26%22`.</span></span> <span data-ttu-id="896f4-153">空格、 引号、 标点符号和其他不安全字符百分比编码为其十六进制值，例如空格字符将变成 %20。</span><span class="sxs-lookup"><span data-stu-id="896f4-153">Spaces, quotes, punctuation and other unsafe characters will be percent encoded to their hexadecimal value, for example a space character will become %20.</span></span>

>[!WARNING]
> <span data-ttu-id="896f4-154">不要使用不受信任的输入的 URL 路径的一部分。</span><span class="sxs-lookup"><span data-stu-id="896f4-154">Don't use untrusted input as part of a URL path.</span></span> <span data-ttu-id="896f4-155">始终将不受信任的输入传递作为查询字符串值。</span><span class="sxs-lookup"><span data-stu-id="896f4-155">Always pass untrusted input as a query string value.</span></span>

<a name="security-cross-site-scripting-customization"></a>

## <a name="customizing-the-encoders"></a><span data-ttu-id="896f4-156">自定义编码器</span><span class="sxs-lookup"><span data-stu-id="896f4-156">Customizing the Encoders</span></span>

<span data-ttu-id="896f4-157">默认情况下编码器使用限制为基本拉丁语 Unicode 范围的安全列表，并为其等效的字符代码在该范围以外的所有字符进行都编码。</span><span class="sxs-lookup"><span data-stu-id="896f4-157">By default encoders use a safe list limited to the Basic Latin Unicode range and encode all characters outside of that range as their character code equivalents.</span></span> <span data-ttu-id="896f4-158">此行为还会影响 Razor TagHelper 和 HtmlHelper 呈现，因为它将使用编码器输出字符串。</span><span class="sxs-lookup"><span data-stu-id="896f4-158">This behavior also affects Razor TagHelper and HtmlHelper rendering as it will use the encoders to output your strings.</span></span>

<span data-ttu-id="896f4-159">这背后的原因是为了防止未知或将来的浏览器 bug （上一个浏览器 bug 具有掣肘： 分析基于非英语字符的处理）。</span><span class="sxs-lookup"><span data-stu-id="896f4-159">The reasoning behind this is to protect against unknown or future browser bugs (previous browser bugs have tripped up parsing based on the processing of non-English characters).</span></span> <span data-ttu-id="896f4-160">如果您的网站重度使用的非拉丁字符，例如中文，西里尔文或其他人这是可能不希望的行为。</span><span class="sxs-lookup"><span data-stu-id="896f4-160">If your web site makes heavy use of non-Latin characters, such as Chinese, Cyrillic or others this is probably not the behavior you want.</span></span>

<span data-ttu-id="896f4-161">你可以自定义编码器安全列表，包括 Unicode 范围适合于你的应用程序启动过程中，在`ConfigureServices()`。</span><span class="sxs-lookup"><span data-stu-id="896f4-161">You can customize the encoder safe lists to include Unicode ranges appropriate to your application during startup, in `ConfigureServices()`.</span></span>

<span data-ttu-id="896f4-162">例如，使用默认配置可能会使用 Razor HtmlHelper 如下所示;</span><span class="sxs-lookup"><span data-stu-id="896f4-162">For example, using the default configuration you might use a Razor HtmlHelper like so;</span></span>

```html
<p>This link text is in Chinese: @Html.ActionLink("汉语/漢語", "Index")</p>
   ```

<span data-ttu-id="896f4-163">当你查看网页的源时将看到已呈现，如下所示，使用中文文本编码;</span><span class="sxs-lookup"><span data-stu-id="896f4-163">When you view the source of the web page you will see it has been rendered as follows, with the Chinese text encoded;</span></span>

```html
<p>This link text is in Chinese: <a href="/">&#x6C49;&#x8BED;/&#x6F22;&#x8A9E;</a></p>
   ```

<span data-ttu-id="896f4-164">若要扩大范围的字符视为安全编码器插入以下行`ConfigureServices()`中的方法`startup.cs`;</span><span class="sxs-lookup"><span data-stu-id="896f4-164">To widen the characters treated as safe by the encoder you would insert the following line into the `ConfigureServices()` method in `startup.cs`;</span></span>

```csharp
services.AddSingleton<HtmlEncoder>(
     HtmlEncoder.Create(allowedRanges: new[] { UnicodeRanges.BasicLatin,
                                               UnicodeRanges.CjkUnifiedIdeographs }));
   ```

<span data-ttu-id="896f4-165">此示例将加宽安全列表，以包括 Unicode 范围 CjkUnifiedIdeographs。</span><span class="sxs-lookup"><span data-stu-id="896f4-165">This example widens the safe list to include the Unicode Range CjkUnifiedIdeographs.</span></span> <span data-ttu-id="896f4-166">现在将变为呈现的输出</span><span class="sxs-lookup"><span data-stu-id="896f4-166">The rendered output would now become</span></span>

```html
<p>This link text is in Chinese: <a href="/">汉语/漢語</a></p>
   ```

<span data-ttu-id="896f4-167">安全列表范围被指定为 Unicode 编码图表，不语言。</span><span class="sxs-lookup"><span data-stu-id="896f4-167">Safe list ranges are specified as Unicode code charts, not languages.</span></span> <span data-ttu-id="896f4-168">[Unicode 标准](http://unicode.org/)具有一系列[代码图表](http://www.unicode.org/charts/index.html)可用于查找包含你字符的图表。</span><span class="sxs-lookup"><span data-stu-id="896f4-168">The [Unicode standard](http://unicode.org/) has a list of [code charts](http://www.unicode.org/charts/index.html) you can use to find the chart containing your characters.</span></span> <span data-ttu-id="896f4-169">每个编码器，Html、 JavaScript 和 Url，必须单独配置。</span><span class="sxs-lookup"><span data-stu-id="896f4-169">Each encoder, Html, JavaScript and Url, must be configured separately.</span></span>

> [!NOTE]
> <span data-ttu-id="896f4-170">自定义的安全列表只会影响源自通过 DI 的编码器。</span><span class="sxs-lookup"><span data-stu-id="896f4-170">Customization of the safe list only affects encoders sourced via DI.</span></span> <span data-ttu-id="896f4-171">如果你直接访问通过编码器`System.Text.Encodings.Web.*Encoder.Default`然后默认情况下，基本拉丁语将使用仅安全列表。</span><span class="sxs-lookup"><span data-stu-id="896f4-171">If you directly access an encoder via `System.Text.Encodings.Web.*Encoder.Default` then the default, Basic Latin only safelist will be used.</span></span>

## <a name="where-should-encoding-take-place"></a><span data-ttu-id="896f4-172">编码 take 应放置位置？</span><span class="sxs-lookup"><span data-stu-id="896f4-172">Where should encoding take place?</span></span>

<span data-ttu-id="896f4-173">常规接受的做法是编码发生在输出时和编码的值应永远不会存储在数据库中。</span><span class="sxs-lookup"><span data-stu-id="896f4-173">The general accepted practice is that encoding takes place at the point of output and encoded values should never be stored in a database.</span></span> <span data-ttu-id="896f4-174">编码在输出时，可更改数据，例如，使用从为查询字符串值的 HTML。</span><span class="sxs-lookup"><span data-stu-id="896f4-174">Encoding at the point of output allows you to change the use of data, for example, from HTML to a query string value.</span></span> <span data-ttu-id="896f4-175">它还使您可以轻松地搜索你的数据而无需对值进行编码搜索之前，并允许您充分任何的利用更改或对编码器进行 bug 修复。</span><span class="sxs-lookup"><span data-stu-id="896f4-175">It also enables you to easily search your data without having to encode values before searching and allows you to take advantage of any changes or bug fixes made to encoders.</span></span>

## <a name="validation-as-an-xss-prevention-technique"></a><span data-ttu-id="896f4-176">作为 XSS 防护技术验证</span><span class="sxs-lookup"><span data-stu-id="896f4-176">Validation as an XSS prevention technique</span></span>

<span data-ttu-id="896f4-177">验证过程可能在限制的 XSS 攻击的有用工具。</span><span class="sxs-lookup"><span data-stu-id="896f4-177">Validation can be a useful tool in limiting XSS attacks.</span></span> <span data-ttu-id="896f4-178">例如，包含字符 0-9 的数字字符串将不会触发 XSS 攻击。</span><span class="sxs-lookup"><span data-stu-id="896f4-178">For example, a numeric string containing only the characters 0-9 won't trigger an XSS attack.</span></span> <span data-ttu-id="896f4-179">接受用户输入中的 HTML 时，验证变得更加复杂。</span><span class="sxs-lookup"><span data-stu-id="896f4-179">Validation becomes more complicated when accepting HTML in user input.</span></span> <span data-ttu-id="896f4-180">分析 HTML 输入是困难的即使不是不可能。</span><span class="sxs-lookup"><span data-stu-id="896f4-180">Parsing HTML input is difficult, if not impossible.</span></span> <span data-ttu-id="896f4-181">Markdown，结合一个分析器，它去除嵌入式的 HTML 是用于接收丰富输入比较安全的选项。</span><span class="sxs-lookup"><span data-stu-id="896f4-181">Markdown, coupled with a parser that strips embedded HTML, is a safer option for accepting rich input.</span></span> <span data-ttu-id="896f4-182">永远不会依赖于单独的验证。</span><span class="sxs-lookup"><span data-stu-id="896f4-182">Never rely on validation alone.</span></span> <span data-ttu-id="896f4-183">始终对不受信任的输入输出，无论执行何种验证或清理之前进行编码。</span><span class="sxs-lookup"><span data-stu-id="896f4-183">Always encode untrusted input before output, no matter what validation or sanitization has been performed.</span></span>
