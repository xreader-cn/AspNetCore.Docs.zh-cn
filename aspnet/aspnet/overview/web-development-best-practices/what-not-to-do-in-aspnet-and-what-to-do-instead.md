---
uid: aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
title: 不需要在 ASP.NET 中，做什么和有什么解决办法 |Microsoft Docs
author: Rick-Anderson
description: 本主题介绍几个常见的错误的人也会在 ASP.NET web 项目中。 它提供有关应执行哪些操作来避免这些 commo 建议...
ms.author: riande
ms.date: 01/28/2019
ms.assetid: c39b9965-545c-4b04-8f55-21be7f28a9e5
msc.legacyurl: /aspnet/overview/web-development-best-practices/what-not-to-do-in-aspnet-and-what-to-do-instead
msc.type: authoredcontent
ms.openlocfilehash: 512d2e2b39467635390fa175546f79d8c9f89f4a
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667708"
---
# <a name="what-not-to-do-in-aspnet-and-what-to-do-instead"></a><span data-ttu-id="abebe-104">不得在 ASP.NET 中执行的操作和转而应执行的操作</span><span class="sxs-lookup"><span data-stu-id="abebe-104">What not to do in ASP.NET, and what to do instead</span></span>

> <span data-ttu-id="abebe-105">本主题介绍几个常见的错误的人也会在 ASP.NET web 项目中。</span><span class="sxs-lookup"><span data-stu-id="abebe-105">This topic describes several common mistakes people make within ASP.NET web projects.</span></span> <span data-ttu-id="abebe-106">它提供了有关应执行哪些操作来避免这些常见的错误的建议。</span><span class="sxs-lookup"><span data-stu-id="abebe-106">It provides recommendations for what you should do to avoid these common mistakes.</span></span> <span data-ttu-id="abebe-107">它基于[presentation](http://vimeo.com/68390507)通过**Damian Edwards**挪威开发者大会。</span><span class="sxs-lookup"><span data-stu-id="abebe-107">It is based on a [presentation](http://vimeo.com/68390507) by **Damian Edwards** at Norwegian Developers Conference.</span></span>


## <a name="disclaimer"></a><span data-ttu-id="abebe-108">免责声明</span><span class="sxs-lookup"><span data-stu-id="abebe-108">Disclaimer</span></span>

<span data-ttu-id="abebe-109">本主题不用作完整指南，以确保你的应用程序安全和高效。</span><span class="sxs-lookup"><span data-stu-id="abebe-109">This topic is not intended as a complete guide to ensure your application is secure and efficient.</span></span> <span data-ttu-id="abebe-110">仍需要遵循本主题中未列出的安全性和性能的最佳方案。</span><span class="sxs-lookup"><span data-stu-id="abebe-110">You still need to follow best practices for security and performance that are not outlined in this topic.</span></span> <span data-ttu-id="abebe-111">它仅建议如何避免与.NET 类和进程相关的常见错误。</span><span class="sxs-lookup"><span data-stu-id="abebe-111">It only suggests how to avoid common mistakes related to .NET classes and processes.</span></span>

## <a name="overview"></a><span data-ttu-id="abebe-112">概述</span><span class="sxs-lookup"><span data-stu-id="abebe-112">Overview</span></span>

<span data-ttu-id="abebe-113">本主题包含以下各节：</span><span class="sxs-lookup"><span data-stu-id="abebe-113">This topic contains the following sections:</span></span>

- [<span data-ttu-id="abebe-114">标准符合性</span><span class="sxs-lookup"><span data-stu-id="abebe-114">Standards Compliance</span></span>](#standards)

    - [<span data-ttu-id="abebe-115">控件适配器</span><span class="sxs-lookup"><span data-stu-id="abebe-115">Control Adapters</span></span>](#adapters)
    - [<span data-ttu-id="abebe-116">在控件上的样式属性</span><span class="sxs-lookup"><span data-stu-id="abebe-116">Style Properties on Controls</span></span>](#styleprop)
    - [<span data-ttu-id="abebe-117">页面和控件回调</span><span class="sxs-lookup"><span data-stu-id="abebe-117">Page and Control Callbacks</span></span>](#callback)
    - [<span data-ttu-id="abebe-118">浏览器功能检测</span><span class="sxs-lookup"><span data-stu-id="abebe-118">Browser Capability Detection</span></span>](#browsercap)
- [<span data-ttu-id="abebe-119">安全性</span><span class="sxs-lookup"><span data-stu-id="abebe-119">Security</span></span>](#security)

    - [<span data-ttu-id="abebe-120">请求验证</span><span class="sxs-lookup"><span data-stu-id="abebe-120">Request Validation</span></span>](#validation)
    - [<span data-ttu-id="abebe-121">无 cookie Forms 身份验证和会话</span><span class="sxs-lookup"><span data-stu-id="abebe-121">Cookieless Forms Authentication and Session</span></span>](#cookieless)
    - [<span data-ttu-id="abebe-122">EnableViewStateMac</span><span class="sxs-lookup"><span data-stu-id="abebe-122">EnableViewStateMac</span></span>](#viewstatemac)
    - [<span data-ttu-id="abebe-123">中等信任</span><span class="sxs-lookup"><span data-stu-id="abebe-123">Medium Trust</span></span>](#medium)
    - [<span data-ttu-id="abebe-124">&lt;appSettings&gt;</span><span class="sxs-lookup"><span data-stu-id="abebe-124">&lt;appSettings&gt;</span></span>](#appsettings)
    - [<span data-ttu-id="abebe-125">UrlPathEncode</span><span class="sxs-lookup"><span data-stu-id="abebe-125">UrlPathEncode</span></span>](#urlpathencode)
- [<span data-ttu-id="abebe-126">可靠性和性能</span><span class="sxs-lookup"><span data-stu-id="abebe-126">Reliability and Performance</span></span>](#performance)

    - [<span data-ttu-id="abebe-127">PreSendRequestHeaders 和 PreSendRequestContent</span><span class="sxs-lookup"><span data-stu-id="abebe-127">PreSendRequestHeaders and PreSendRequestContent</span></span>](#presend)
    - [<span data-ttu-id="abebe-128">使用 Web 窗体的异步页面事件</span><span class="sxs-lookup"><span data-stu-id="abebe-128">Asynchronous Page Events with Web Forms</span></span>](#asyncevents)
    - [<span data-ttu-id="abebe-129">Fire-and-Forget Work</span><span class="sxs-lookup"><span data-stu-id="abebe-129">Fire-and-Forget Work</span></span>](#fire)
    - [<span data-ttu-id="abebe-130">请求实体正文</span><span class="sxs-lookup"><span data-stu-id="abebe-130">Request Entity Body</span></span>](#requestentity)
    - [<span data-ttu-id="abebe-131">Response.Redirect 和 Response.End</span><span class="sxs-lookup"><span data-stu-id="abebe-131">Response.Redirect and Response.End</span></span>](#redirect)
    - [<span data-ttu-id="abebe-132">EnableViewState 和 ViewStateMode</span><span class="sxs-lookup"><span data-stu-id="abebe-132">EnableViewState and ViewStateMode</span></span>](#viewstatemode)
    - [<span data-ttu-id="abebe-133">SqlMembershipProvider</span><span class="sxs-lookup"><span data-stu-id="abebe-133">SqlMembershipProvider</span></span>](#sqlprovider)
    - [<span data-ttu-id="abebe-134">长时间运行的请求 (> 110 秒)</span><span class="sxs-lookup"><span data-stu-id="abebe-134">Long Running Requests (>110 seconds)</span></span>](#long)

<a id="standards"></a>

## <a name="standards-compliance"></a><span data-ttu-id="abebe-135">标准符合性</span><span class="sxs-lookup"><span data-stu-id="abebe-135">Standards Compliance</span></span>

<a id="adapters"></a>

### <a name="control-adapters"></a><span data-ttu-id="abebe-136">控件适配器</span><span class="sxs-lookup"><span data-stu-id="abebe-136">Control adapters</span></span>

<span data-ttu-id="abebe-137">建议：停止使用的自适应呈现的控件适配器，而使用 CSS 媒体查询和符合标准的 HTML。</span><span class="sxs-lookup"><span data-stu-id="abebe-137">Recommendation: Stop using control adapters for adaptive rendering, and instead use CSS media queries and standards-compliant HTML.</span></span>

<span data-ttu-id="abebe-138">要呈现为不同的设备和环境进行了自定义表示代码的.NET 2.0 中引入了控件适配器。</span><span class="sxs-lookup"><span data-stu-id="abebe-138">Controls Adapters were introduced in .NET 2.0 to render presentation code that was customized for different devices and environments.</span></span> <span data-ttu-id="abebe-139">现在，可以使用 CSS 和 HTML 完成此自适应呈现。</span><span class="sxs-lookup"><span data-stu-id="abebe-139">Now, this adaptive rendering can be accomplished with CSS and HTML.</span></span> <span data-ttu-id="abebe-140">应停止使用控件适配器，并将任何现有适配器转换为 CSS 和 HTML。</span><span class="sxs-lookup"><span data-stu-id="abebe-140">You should stop using Control Adapters and convert any existing adapters to CSS and HTML.</span></span>

<span data-ttu-id="abebe-141">有关详细信息，请参阅[媒体查询](http://www.w3.org/TR/css3-mediaqueries/)和[How To:将移动页面添加到 ASP.NET Web 窗体 / MVC 应用程序](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md)。</span><span class="sxs-lookup"><span data-stu-id="abebe-141">For more information, see [Media Queries](http://www.w3.org/TR/css3-mediaqueries/) and [How To: Add Mobile Pages to Your ASP.NET Web Forms / MVC Application](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md).</span></span>

<a id="styleprop"></a>

### <a name="style-properties-on-controls"></a><span data-ttu-id="abebe-142">在控件上的样式属性</span><span class="sxs-lookup"><span data-stu-id="abebe-142">Style properties on controls</span></span>

<span data-ttu-id="abebe-143">建议：停止在控件标记中，设置样式值并改为在 CSS 样式表中设置格式设置的值。</span><span class="sxs-lookup"><span data-stu-id="abebe-143">Recommendation: Stop setting style values in the control markup, and instead set formatting values in CSS stylesheets.</span></span>

<span data-ttu-id="abebe-144">Web 服务器控件包含数十个属性用于设置在行中样式属性。</span><span class="sxs-lookup"><span data-stu-id="abebe-144">Web server controls contain dozens of properties which can be used to set in-line style properties.</span></span> <span data-ttu-id="abebe-145">例如，ForeColor 属性设置控件的文本的颜色。</span><span class="sxs-lookup"><span data-stu-id="abebe-145">For example, the ForeColor property sets the color of the text for a control.</span></span> <span data-ttu-id="abebe-146">你可以完成此相同的效果更高效地通过 CSS 样式表。</span><span class="sxs-lookup"><span data-stu-id="abebe-146">You can accomplish this same effect more efficiently through CSS stylesheets.</span></span> <span data-ttu-id="abebe-147">样式表，可以集中样式值并避免设置这些值在整个应用程序。</span><span class="sxs-lookup"><span data-stu-id="abebe-147">Stylesheets enable you to centralize style values and avoid setting these values throughout your application.</span></span>

<span data-ttu-id="abebe-148">下面的示例演示一个 CSS 类集文本为红色。</span><span class="sxs-lookup"><span data-stu-id="abebe-148">The following example shows a CSS class the sets text to red.</span></span>

[!code-css[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample1.css)]

<span data-ttu-id="abebe-149">以下示例演示如何动态应用的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="abebe-149">The next example shows how to dynamically apply the CSS class.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample2.cs)]

<a id="callback"></a>

### <a name="page-and-control-callbacks"></a><span data-ttu-id="abebe-150">页面和控件的回叫</span><span class="sxs-lookup"><span data-stu-id="abebe-150">Page and control callbacks</span></span>

<span data-ttu-id="abebe-151">建议：停止使用页面和控件的回调，并改为使用以下任一项：AJAX、 UpdatePanel、 MVC 操作方法、 Web API 或 SignalR。</span><span class="sxs-lookup"><span data-stu-id="abebe-151">Recommendation: Stop using page and control callbacks, and instead use any of the following: AJAX, UpdatePanel, MVC action methods, Web API, or SignalR.</span></span>

<span data-ttu-id="abebe-152">在早期版本的 ASP.NET，页和控件的回叫方法启用更新而无需刷新整个页面的 web 页的一部分。</span><span class="sxs-lookup"><span data-stu-id="abebe-152">In earlier versions of ASP.NET, Page and Control callback methods enabled you to update part of the web page without refreshing an entire page.</span></span> <span data-ttu-id="abebe-153">您现在可以实现部分页面更新通过[AJAX](../../../ajax/index.md)， [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx)， [MVC](../../../mvc/index.md)， [Web API](../../../web-api/index.md)或[SignalR](../../../signalr/index.md).</span><span class="sxs-lookup"><span data-stu-id="abebe-153">You can now accomplish partial-page updates through [AJAX](../../../ajax/index.md), [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx), [MVC](../../../mvc/index.md), [Web API](../../../web-api/index.md) or [SignalR](../../../signalr/index.md).</span></span> <span data-ttu-id="abebe-154">您应停止使用回调方法，因为它们可能会导致问题使用友好的 Url 和路由。</span><span class="sxs-lookup"><span data-stu-id="abebe-154">You should stop using callback methods because they can cause issues with friendly URLs and routing.</span></span> <span data-ttu-id="abebe-155">默认情况下，控件不能将回叫方法，但如果启用此功能在控件中的，则应将其禁用。</span><span class="sxs-lookup"><span data-stu-id="abebe-155">By default, controls do not enable callback methods, but if you enabled this feature in a control, you should disable it.</span></span>

<a id="browsercap"></a>

### <a name="browser-capability-detection"></a><span data-ttu-id="abebe-156">浏览器功能检测</span><span class="sxs-lookup"><span data-stu-id="abebe-156">Browser capability detection</span></span>

<span data-ttu-id="abebe-157">建议：停止使用静态的浏览器功能检测，并改为使用动态功能检测。</span><span class="sxs-lookup"><span data-stu-id="abebe-157">Recommendation: Stop using static browser capability detection, and instead use dynamic feature detection.</span></span>

<span data-ttu-id="abebe-158">在 ASP.NET 的早期版本中，在 XML 文件中存储的每个浏览器支持的功能。</span><span class="sxs-lookup"><span data-stu-id="abebe-158">In earlier versions of ASP.NET, the supported features for each browser were stored in an XML file.</span></span> <span data-ttu-id="abebe-159">检测功能支持通过静态查找不是最好的方法。</span><span class="sxs-lookup"><span data-stu-id="abebe-159">Detecting feature support through a static lookup is not the best approach.</span></span> <span data-ttu-id="abebe-160">现在，可以动态检测浏览器的支持的功能使用的功能检测框架，如[Modernizr](http://modernizr.com/)。</span><span class="sxs-lookup"><span data-stu-id="abebe-160">Now, you can dynamically detect a browser's supported features by using a feature detection framework, such as [Modernizr](http://modernizr.com/).</span></span> <span data-ttu-id="abebe-161">功能检测通过尝试使用方法或属性，然后检查以查看是否浏览器生成所需的结果来确定支持。</span><span class="sxs-lookup"><span data-stu-id="abebe-161">Feature detection determines support by attempting to use a method or property and then checking to see if the browser produced the desired result.</span></span> <span data-ttu-id="abebe-162">默认情况下，Modernizr 会包含在 Web 应用程序模板。</span><span class="sxs-lookup"><span data-stu-id="abebe-162">By default, Modernizr is included in the Web application templates.</span></span>

<a id="security"></a>

## <a name="security"></a><span data-ttu-id="abebe-163">安全性</span><span class="sxs-lookup"><span data-stu-id="abebe-163">Security</span></span>

<a id="validation"></a>

### <a name="request-validation"></a><span data-ttu-id="abebe-164">请求验证</span><span class="sxs-lookup"><span data-stu-id="abebe-164">Request validation</span></span>

<span data-ttu-id="abebe-165">建议：验证用户输入，并从用户的输出编码。</span><span class="sxs-lookup"><span data-stu-id="abebe-165">Recommendation: Validate user input, and encode output from users.</span></span>

<span data-ttu-id="abebe-166">请求验证，ASP.NET，检查每个请求并停止请求，如果找到感知到的威胁的一项功能。</span><span class="sxs-lookup"><span data-stu-id="abebe-166">Request validation is a feature of ASP.NET that inspects each request and stops the request if a perceived threat is found.</span></span> <span data-ttu-id="abebe-167">不依赖于用于保护应用程序免受跨站点脚本攻击的请求验证。</span><span class="sxs-lookup"><span data-stu-id="abebe-167">Do not depend on request validation for securing your application against cross-site scripting attacks.</span></span> <span data-ttu-id="abebe-168">相反，验证用户的所有输入和输出编码。</span><span class="sxs-lookup"><span data-stu-id="abebe-168">Instead, validate all input from users and encode the output.</span></span> <span data-ttu-id="abebe-169">在一些有限的情况下，您可以使用正则表达式验证输入，但在更复杂的情况下应验证使用.NET 类，以确定如果的值匹配用户输入允许的值。</span><span class="sxs-lookup"><span data-stu-id="abebe-169">In some limited cases, you can use regular expressions to validate the input, but in more complicated cases you should validate user input by using .NET classes that determine if the value matches allowed values.</span></span>

<span data-ttu-id="abebe-170">下面的示例演示如何在 Uri 类中使用的静态方法以确定是否为有效的用户提供的 Uri。</span><span class="sxs-lookup"><span data-stu-id="abebe-170">The following example shows how to use a static method in the Uri class to determine whether the Uri provided by a user is valid.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample3.cs)]

<span data-ttu-id="abebe-171">但是，若要充分验证 Uri，您还应检查以确保它指定`http`或`https`。</span><span class="sxs-lookup"><span data-stu-id="abebe-171">However, to sufficiently verify the Uri, you should also check to make sure it specifies `http` or `https`.</span></span> <span data-ttu-id="abebe-172">以下示例使用实例方法来验证 Uri 有效。</span><span class="sxs-lookup"><span data-stu-id="abebe-172">The following example uses instance methods to verify that the Uri is valid.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample4.cs)]

<span data-ttu-id="abebe-173">然后再呈现为 html 格式的用户输入或 SQL 查询中包括用户输入，以确保恶意代码则不包含的值进行编码。</span><span class="sxs-lookup"><span data-stu-id="abebe-173">Before rendering user input as HTML or including user input in a SQL query, encode the values to ensure malicious code is not included.</span></span>

<span data-ttu-id="abebe-174">你可以 HTML 编码的标记中的值&lt;%: %&gt;语法，如下所示。</span><span class="sxs-lookup"><span data-stu-id="abebe-174">You can HTML encode the value in markup with the &lt;%: %&gt; syntax, as shown below.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample5.aspx?highlight=1)]

<span data-ttu-id="abebe-175">或者，可以在 Razor 语法中，HTML 编码 @，如下所示。</span><span class="sxs-lookup"><span data-stu-id="abebe-175">Or, in Razor syntax, you can HTML encode with @, as shown below.</span></span>

[!code-cshtml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample6.cshtml?highlight=1)]

<span data-ttu-id="abebe-176">下面的示例说明如何为 HTML 编码在代码隐藏中的值。</span><span class="sxs-lookup"><span data-stu-id="abebe-176">The next example shows how to HTML encode a value in code-behind.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample7.cs)]

<span data-ttu-id="abebe-177">若要安全地进行编码的 SQL 命令的值，请使用命令参数如[SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx)。</span><span class="sxs-lookup"><span data-stu-id="abebe-177">To safely encode a value for SQL commands, use command parameters such as the [SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx).</span></span> <a id="cookieless"></a>

### <a name="cookieless-forms-authentication-and-session"></a><span data-ttu-id="abebe-178">无 cookie forms 身份验证和会话</span><span class="sxs-lookup"><span data-stu-id="abebe-178">Cookieless forms authentication and session</span></span>

<span data-ttu-id="abebe-179">建议：需要 cookie。</span><span class="sxs-lookup"><span data-stu-id="abebe-179">Recommendation: Require cookies.</span></span>

<span data-ttu-id="abebe-180">查询字符串中传递身份验证信息并不安全。</span><span class="sxs-lookup"><span data-stu-id="abebe-180">Passing authentication information in the query string is not secure.</span></span> <span data-ttu-id="abebe-181">因此，需要 cookie，应用程序包括身份验证。</span><span class="sxs-lookup"><span data-stu-id="abebe-181">Therefore, require cookies when your application includes authentication.</span></span> <span data-ttu-id="abebe-182">如果您的 cookie 存储敏感信息，请考虑 cookie 要求 SSL。</span><span class="sxs-lookup"><span data-stu-id="abebe-182">If your cookie stores sensitive information, consider requiring SSL for the cookie.</span></span>

<span data-ttu-id="abebe-183">下面的示例演示如何在 Web.config 文件中指定窗体身份验证，需要通过 SSL 传输的 cookie。</span><span class="sxs-lookup"><span data-stu-id="abebe-183">The following example shows how to specify in the Web.config file that Forms Authentication requires a cookie that is transmitted over SSL.</span></span>

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample8.xml)]

<a id="viewstatemac"></a>

### <a name="enableviewstatemac"></a><span data-ttu-id="abebe-184">EnableViewStateMac</span><span class="sxs-lookup"><span data-stu-id="abebe-184">EnableViewStateMac</span></span>

<span data-ttu-id="abebe-185">建议：永远不会设置为 false。</span><span class="sxs-lookup"><span data-stu-id="abebe-185">Recommendation: Never set to false.</span></span>

<span data-ttu-id="abebe-186">默认情况下，设置 EnbableViewStateMac 为 true。</span><span class="sxs-lookup"><span data-stu-id="abebe-186">By default, EnbableViewStateMac is set to true.</span></span> <span data-ttu-id="abebe-187">即使你的应用程序未使用视图状态，请不设置 enableviewstatemac 设为 false。</span><span class="sxs-lookup"><span data-stu-id="abebe-187">Even if your application is not using view state, do not set EnableViewStateMac to false.</span></span> <span data-ttu-id="abebe-188">此值设置为 false 将使你的应用程序容易受到跨站点脚本。</span><span class="sxs-lookup"><span data-stu-id="abebe-188">Setting this value to false will make your application vulnerable to cross-site scripting.</span></span>

<span data-ttu-id="abebe-189">从 ASP.NET 4.5.2 开始，运行时强制实施**EnableViewStateMac = true**。</span><span class="sxs-lookup"><span data-stu-id="abebe-189">Starting with ASP.NET 4.5.2, the runtime enforces **EnableViewStateMac=true**.</span></span> <span data-ttu-id="abebe-190">即使将其设置为 false 时，运行时将忽略此值，并继续执行设置的值为 true。</span><span class="sxs-lookup"><span data-stu-id="abebe-190">Even if you set it to false, the runtime ignores this value and proceeds with the value set to true.</span></span> <span data-ttu-id="abebe-191">有关详细信息，请参阅[ASP.NET 4.5.2 和 EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx)。</span><span class="sxs-lookup"><span data-stu-id="abebe-191">For more information, see [ASP.NET 4.5.2 and EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx).</span></span>

<span data-ttu-id="abebe-192">下面的示例演示如何将 enableviewstatemac 设为 true。</span><span class="sxs-lookup"><span data-stu-id="abebe-192">The following example shows how to set EnableViewStateMac to true.</span></span> <span data-ttu-id="abebe-193">不需要实际设置此值为 true，因为它是默认情况下，则返回 true。</span><span class="sxs-lookup"><span data-stu-id="abebe-193">You do not need to actually set this value to true because it is true by default.</span></span> <span data-ttu-id="abebe-194">但是，如果你已将其设置为 false 中任意页在应用程序中，则必须立即更正此值。</span><span class="sxs-lookup"><span data-stu-id="abebe-194">However, if you have set it to false on any page in your application, you must immediately correct this value.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample9.aspx)]

<a id="medium"></a>

### <a name="medium-trust"></a><span data-ttu-id="abebe-195">中等信任</span><span class="sxs-lookup"><span data-stu-id="abebe-195">Medium trust</span></span>

<span data-ttu-id="abebe-196">建议：作为安全边界不依赖于中等信任 （或任何其他信任级别）。</span><span class="sxs-lookup"><span data-stu-id="abebe-196">Recommendation: Do not depend on Medium Trust (or any other trust level) as a security boundary.</span></span>

<span data-ttu-id="abebe-197">部分信任环境不能充分保护您的应用程序，因此不应。</span><span class="sxs-lookup"><span data-stu-id="abebe-197">Partial trust does not adequately protect your application and should not be used.</span></span> <span data-ttu-id="abebe-198">相反，使用完全信任，并将在单独的应用程序池不受信任的应用程序隔离。</span><span class="sxs-lookup"><span data-stu-id="abebe-198">Instead, use Full Trust, and isolate untrusted applications in separate application pools.</span></span> <span data-ttu-id="abebe-199">此外，运行下一个唯一标识每个应用程序池。</span><span class="sxs-lookup"><span data-stu-id="abebe-199">Also, run each application pool under a unique identity.</span></span> <span data-ttu-id="abebe-200">有关详细信息，请参阅[ASP.NET 部分信任环境不能保证应用程序隔离](https://support.microsoft.com/kb/2698981)。</span><span class="sxs-lookup"><span data-stu-id="abebe-200">For more information, see [ASP.NET Partial Trust does not guarantee application isolation](https://support.microsoft.com/kb/2698981).</span></span>

<a id="appsettings"></a>

### <a name="ltappsettingsgt"></a><span data-ttu-id="abebe-201">&lt;appSettings&gt;</span><span class="sxs-lookup"><span data-stu-id="abebe-201">&lt;appSettings&gt;</span></span>

<span data-ttu-id="abebe-202">建议：不要禁用中的安全设置&lt;appSettings&gt;元素。</span><span class="sxs-lookup"><span data-stu-id="abebe-202">Recommendation: Do not disable security settings in &lt;appSettings&gt; element.</span></span>

<span data-ttu-id="abebe-203">AppSettings 元素包含多个值所需的安全更新。</span><span class="sxs-lookup"><span data-stu-id="abebe-203">The appSettings element contains many values which are required for security updates.</span></span> <span data-ttu-id="abebe-204">您不应更改或禁用这些值。</span><span class="sxs-lookup"><span data-stu-id="abebe-204">You should not change or disable these values.</span></span> <span data-ttu-id="abebe-205">如果部署更新时，必须禁用这些值，请立即重新启用完成部署后。</span><span class="sxs-lookup"><span data-stu-id="abebe-205">If you must disable these values when deploying an update, immediately re-enable after completing deployment.</span></span>

<span data-ttu-id="abebe-206">有关详细信息，请参阅[ASP.NET appSettings 元素](https://msdn.microsoft.com/library/hh975440.aspx)。</span><span class="sxs-lookup"><span data-stu-id="abebe-206">For details, see [ASP.NET appSettings Element](https://msdn.microsoft.com/library/hh975440.aspx).</span></span>

<a id="urlpathencode"></a>

### <a name="urlpathencode"></a><span data-ttu-id="abebe-207">UrlPathEncode</span><span class="sxs-lookup"><span data-stu-id="abebe-207">UrlPathEncode</span></span>

<span data-ttu-id="abebe-208">建议：使用[UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx)相反。</span><span class="sxs-lookup"><span data-stu-id="abebe-208">Recommendation: Use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx) instead.</span></span>

<span data-ttu-id="abebe-209">UrlPathEncode 方法已添加到.NET Framework，才能解决非常特定的浏览器兼容性问题。</span><span class="sxs-lookup"><span data-stu-id="abebe-209">The UrlPathEncode method was added to the .NET Framework to resolve a very specific browser compatibility problem.</span></span> <span data-ttu-id="abebe-210">它不会充分编码 URL，并不会从跨站点脚本保护你的应用程序。</span><span class="sxs-lookup"><span data-stu-id="abebe-210">It does not adequately encode a URL, and does not protect your application from cross-site scripting.</span></span> <span data-ttu-id="abebe-211">你决不应在应用程序中使用它。</span><span class="sxs-lookup"><span data-stu-id="abebe-211">You should never use it in your application.</span></span> <span data-ttu-id="abebe-212">请改用[UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx)。</span><span class="sxs-lookup"><span data-stu-id="abebe-212">Instead, use [UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx).</span></span>

<span data-ttu-id="abebe-213">下面的示例演示如何将编码的 URL 作为超链接控件的查询字符串参数传递。</span><span class="sxs-lookup"><span data-stu-id="abebe-213">The following example shows how to pass an encoded URL as a query string parameter for a hyperlink control.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample10.cs)]

<a id="performance"></a>

## <a name="reliability-and-performance"></a><span data-ttu-id="abebe-214">可靠性和性能</span><span class="sxs-lookup"><span data-stu-id="abebe-214">Reliability and performance</span></span>

<a id="presend"></a>

### <a name="presendrequestheaders-and-presendrequestcontent"></a><span data-ttu-id="abebe-215">PreSendRequestHeaders 和 PreSendRequestContent</span><span class="sxs-lookup"><span data-stu-id="abebe-215">PreSendRequestHeaders and PreSendRequestContent</span></span>

<span data-ttu-id="abebe-216">建议：使用托管模块不使用这些事件。</span><span class="sxs-lookup"><span data-stu-id="abebe-216">Recommendation: Do not use these events with managed modules.</span></span> <span data-ttu-id="abebe-217">相反，编写本机 IIS 模块来执行所需的任务。</span><span class="sxs-lookup"><span data-stu-id="abebe-217">Instead, write a native IIS module to perform the required task.</span></span> <span data-ttu-id="abebe-218">请参阅[创建本机代码 HTTP 模块](https://msdn.microsoft.com/library/ms693629.aspx)。</span><span class="sxs-lookup"><span data-stu-id="abebe-218">See [Creating Native-Code HTTP Modules](https://msdn.microsoft.com/library/ms693629.aspx).</span></span>

<span data-ttu-id="abebe-219">可以使用[PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx)并[PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx)具有本机 IIS 模块的事件。</span><span class="sxs-lookup"><span data-stu-id="abebe-219">You can use the [PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx) and [PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx) events with native IIS modules.</span></span>
> [!WARNING]
> <span data-ttu-id="abebe-220">不要使用`PreSendRequestHeaders`并`PreSendRequestContent`实现的托管模块`IHttpModule`。</span><span class="sxs-lookup"><span data-stu-id="abebe-220">Do not use `PreSendRequestHeaders` and `PreSendRequestContent` with managed modules that implement `IHttpModule`.</span></span> <span data-ttu-id="abebe-221">对于异步请求，设置这些属性可能会导致问题。</span><span class="sxs-lookup"><span data-stu-id="abebe-221">Setting these properties can cause issues with asynchronous requests.</span></span> <span data-ttu-id="abebe-222">应用程序请求路由 (ARR) 和 websocket 的组合可能会导致访问冲突异常可能会导致崩溃的 w3wp。</span><span class="sxs-lookup"><span data-stu-id="abebe-222">The combination of Application Requested Routing (ARR) and websockets might lead to access violation exceptions that can cause w3wp to crash.</span></span> <span data-ttu-id="abebe-223">例如，iiscore ！W3_CONTEXT_BASE::GetIsLastNotification + 68 iiscore.dll 中的导致访问冲突异常 (0xC0000005)。</span><span class="sxs-lookup"><span data-stu-id="abebe-223">For example, iiscore!W3_CONTEXT_BASE::GetIsLastNotification+68 in iiscore.dll has caused an access violation exception (0xC0000005).</span></span>

<a id="asyncevents"></a>

### <a name="asynchronous-page-events-with-web-forms"></a><span data-ttu-id="abebe-224">使用 web 窗体的异步页面事件</span><span class="sxs-lookup"><span data-stu-id="abebe-224">Asynchronous page events with web forms</span></span>

<span data-ttu-id="abebe-225">建议：在 Web 窗体，应避免编写异步 void 方法的页面生命周期事件，并改为使用[Page.RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx)异步代码的。</span><span class="sxs-lookup"><span data-stu-id="abebe-225">Recommendation: In Web Forms, avoid writing async void methods for Page lifecycle events, and instead use [Page.RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx) for asynchronous code.</span></span>

<span data-ttu-id="abebe-226">当标记的页面事件**异步**并**void**，不能确定何时完成的异步代码。</span><span class="sxs-lookup"><span data-stu-id="abebe-226">When you mark a page event with **async** and **void**, you cannot determine when the asynchronous code has finished.</span></span> <span data-ttu-id="abebe-227">相反，使用 Page.RegisterAsyncTask 使您能够跟踪其完成的方式运行异步代码。</span><span class="sxs-lookup"><span data-stu-id="abebe-227">Instead, use Page.RegisterAsyncTask to run the asynchronous code in a way that enables you to track its completion.</span></span>

<span data-ttu-id="abebe-228">下面的示例演示一个按钮的单击处理程序，其中包含异步代码。</span><span class="sxs-lookup"><span data-stu-id="abebe-228">The following example shows a button click handler that contains asynchronous code.</span></span> <span data-ttu-id="abebe-229">此示例包括以异步方式读取一个字符串值提供仅为一个异步任务的简化示例，而不是建议的做法。</span><span class="sxs-lookup"><span data-stu-id="abebe-229">This example includes reading a string value asynchronously, which is provided only as a simplified example of an asynchronous task and not as a recommended practice.</span></span>

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample11.cs)]

<span data-ttu-id="abebe-230">如果使用的异步任务，设置为 4.5 （或更高版本） 的 Http 运行时目标框架的 Web.config 文件中。</span><span class="sxs-lookup"><span data-stu-id="abebe-230">If you are using asynchronous Tasks, set the Http runtime target framework to 4.5 (or later) in the Web.config file.</span></span> <span data-ttu-id="abebe-231">设置目标框架为 4.5 将新的同步上下文上的.NET 4.5 中添加。</span><span class="sxs-lookup"><span data-stu-id="abebe-231">Setting the target framework to 4.5 turns on the new synchronization context that was added in .NET 4.5.</span></span> <span data-ttu-id="abebe-232">此值在新项目在 Visual Studio 中，默认情况下设置，但不是设置，如果您正在使用现有的项目。</span><span class="sxs-lookup"><span data-stu-id="abebe-232">This value is set by default in new projects in Visual Studio, but is not be set if you are working with an existing project.</span></span>

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample12.xml)]

<a id="fire"></a>

### <a name="fire-and-forget-work"></a><span data-ttu-id="abebe-233">即发即弃工作</span><span class="sxs-lookup"><span data-stu-id="abebe-233">Fire-and-forget work</span></span>

<span data-ttu-id="abebe-234">建议：在处理 asp.net 请求时，避免启动即发即弃工作 （此类调用 ThreadPool.QueueUserWorkItem 方法或创建一个计时器，用于重复调用委托）。</span><span class="sxs-lookup"><span data-stu-id="abebe-234">Recommendation: When handling a request within ASP.NET, avoid launching fire-and-forget work (such calling the ThreadPool.QueueUserWorkItem method or creating a timer that repeatedly calls a delegate).</span></span>

<span data-ttu-id="abebe-235">如果你的应用程序已在 ASP.NET 中运行的即发即弃工作，你的应用程序可以获得不同步。在任何时候，应用程序域可以销毁这意味着您正在进行的过程可能不再匹配应用程序的当前状态。</span><span class="sxs-lookup"><span data-stu-id="abebe-235">If your application has fire-and-forget work that runs within ASP.NET, your application can get out of sync. At any time, the app domain can be destroyed which means your ongoing process may no longer match the current state of the application.</span></span>

<span data-ttu-id="abebe-236">应将此类在 ASP.NET 外部工作。</span><span class="sxs-lookup"><span data-stu-id="abebe-236">You should move this type of work outside of ASP.NET.</span></span> <span data-ttu-id="abebe-237">可以使用在 Azure 中的 Web 作业、 Windows 服务或辅助角色，以执行正在进行的工作，并从另一个进程中运行该代码。</span><span class="sxs-lookup"><span data-stu-id="abebe-237">You can use a Web Jobs, Windows Service or a Worker role in Azure to perform ongoing work, and run that code from another process.</span></span>

<span data-ttu-id="abebe-238">如果必须执行这项工作在 ASP.NET 内的，则可以添加名为 Nuget 包[WebBackgrounder](http://www.nuget.org/packages/webbackgrounder)要运行此代码。</span><span class="sxs-lookup"><span data-stu-id="abebe-238">If you must perform this work within ASP.NET, you can add the Nuget package called [WebBackgrounder](http://www.nuget.org/packages/webbackgrounder) to run the code.</span></span>

<a id="requestentity"></a>

### <a name="request-entity-body"></a><span data-ttu-id="abebe-239">请求实体正文</span><span class="sxs-lookup"><span data-stu-id="abebe-239">Request entity body</span></span>

<span data-ttu-id="abebe-240">建议：避免读取 Request.Form 或 Request.InputStream 之前处理程序的执行事件。</span><span class="sxs-lookup"><span data-stu-id="abebe-240">Recommendation: Avoid reading Request.Form or Request.InputStream before the handler's execute event.</span></span>

<span data-ttu-id="abebe-241">应从 Request.Form 或 Request.InputStream 读取的最早是在处理程序的过程执行事件。</span><span class="sxs-lookup"><span data-stu-id="abebe-241">The earliest you should read from Request.Form or Request.InputStream is during the handler's execute event.</span></span> <span data-ttu-id="abebe-242">在 MVC 中，控制器是处理程序并执行事件时运行操作方法。</span><span class="sxs-lookup"><span data-stu-id="abebe-242">In MVC, the Controller is the handler and the execute event is when the action method runs.</span></span> <span data-ttu-id="abebe-243">在 Web 窗体页是处理程序和执行事件是 Page.Init 事件激发时。</span><span class="sxs-lookup"><span data-stu-id="abebe-243">In Web Forms, the Page is the handler and the execute event is when the Page.Init event fires.</span></span> <span data-ttu-id="abebe-244">如果以前的执行事件读取请求实体正文，您会影响处理请求。</span><span class="sxs-lookup"><span data-stu-id="abebe-244">If you read the request entity body earlier than the execute event, you interfere with the processing of the request.</span></span>

<span data-ttu-id="abebe-245">如果需要读取请求实体正文的执行事件之前，可以使用两种[Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx)或[Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx)。</span><span class="sxs-lookup"><span data-stu-id="abebe-245">If you need to read the request entity body before the execute event, use either [Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx) or [Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx).</span></span> <span data-ttu-id="abebe-246">当使用 GetBufferlessInputStream 时，请求中获取原始流并承担处理整个请求。</span><span class="sxs-lookup"><span data-stu-id="abebe-246">When you use GetBufferlessInputStream, you get the raw stream from the request, and assume responsibility for processing the entire request.</span></span> <span data-ttu-id="abebe-247">后因为它们不填充由 ASP.NET，调用 GetBufferlessInputStream、 Request.Form 和 Request.InputStream 不可用。</span><span class="sxs-lookup"><span data-stu-id="abebe-247">After calling GetBufferlessInputStream, Request.Form and Request.InputStream are not available because they have not been populated by ASP.NET.</span></span> <span data-ttu-id="abebe-248">当使用 GetBufferedInputStream 时，将从请求中获取流的副本。</span><span class="sxs-lookup"><span data-stu-id="abebe-248">When you use GetBufferedInputStream, you get a copy of the stream from the request.</span></span> <span data-ttu-id="abebe-249">Request.Form 和 Request.InputStream 是请求更高版本中仍然可用，因为 ASP.NET 填充另一个副本。</span><span class="sxs-lookup"><span data-stu-id="abebe-249">Request.Form and Request.InputStream are still available later in the request because ASP.NET populates the other copy.</span></span>

<a id="redirect"></a>

### <a name="responseredirect-and-responseend"></a><span data-ttu-id="abebe-250">Response.Redirect 和 Response.End</span><span class="sxs-lookup"><span data-stu-id="abebe-250">Response.Redirect and Response.End</span></span>

<span data-ttu-id="abebe-251">建议：请注意的线程之后调用的处理方式的差异[Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx)。</span><span class="sxs-lookup"><span data-stu-id="abebe-251">Recommendation: Be aware of differences in how thread is handled after calling [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx).</span></span>

<span data-ttu-id="abebe-252">[Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx)方法调用 Response.End 方法。</span><span class="sxs-lookup"><span data-stu-id="abebe-252">The [Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx) method calls the Response.End method.</span></span> <span data-ttu-id="abebe-253">在同步过程中，调用 Request.Redirect 会导致当前线程立即中止。</span><span class="sxs-lookup"><span data-stu-id="abebe-253">In a synchronous process, calling Request.Redirect causes the current thread to immediately abort.</span></span> <span data-ttu-id="abebe-254">但是，在异步流程中调用 Response.Redirect 不会中止当前线程，因此请求将继续执行代码。</span><span class="sxs-lookup"><span data-stu-id="abebe-254">However, in an asynchronous process, calling Response.Redirect does not abort the current thread, so code execution continues for the request.</span></span> <span data-ttu-id="abebe-255">在异步过程，必须从要停止执行代码的方法来返回该任务。</span><span class="sxs-lookup"><span data-stu-id="abebe-255">In an asynchronous process, you must return the Task from the method to stop the code execution.</span></span>

<span data-ttu-id="abebe-256">在 MVC 项目中，不应调用 Response.Redirect。</span><span class="sxs-lookup"><span data-stu-id="abebe-256">In an MVC project, you should not call Response.Redirect.</span></span> <span data-ttu-id="abebe-257">改为返回 RedirectResult。</span><span class="sxs-lookup"><span data-stu-id="abebe-257">Instead, return a RedirectResult.</span></span>

<a id="viewstatemode"></a>

### <a name="enableviewstate-and-viewstatemode"></a><span data-ttu-id="abebe-258">EnableViewState 和 ViewStateMode</span><span class="sxs-lookup"><span data-stu-id="abebe-258">EnableViewState and ViewStateMode</span></span>

<span data-ttu-id="abebe-259">建议：使用 ViewStateMode，而不是 EnableViewState，来提供精细控制哪些控件使用视图状态。</span><span class="sxs-lookup"><span data-stu-id="abebe-259">Recommendation: Use ViewStateMode, instead of EnableViewState, to provide granular control over which controls use view state.</span></span>

<span data-ttu-id="abebe-260">当将 EnableViewState 设置为 false 在 Page 指令中时，视图状态的页内的所有控件禁用且无法启用。</span><span class="sxs-lookup"><span data-stu-id="abebe-260">When you set EnableViewState to false in the Page directive, view state is disabled for all controls within the page and cannot be enabled.</span></span> <span data-ttu-id="abebe-261">如果你想要启用仅在页面中某些控件的视图状态，ViewStateMode 为已禁用的页面设置。</span><span class="sxs-lookup"><span data-stu-id="abebe-261">If you want to enable view state for only certain controls in your page, set ViewStateMode to Disabled for the Page.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample13.aspx)]

<span data-ttu-id="abebe-262">然后，实际上需要视图状态的控件上设置为已启用的 ViewStateMode。</span><span class="sxs-lookup"><span data-stu-id="abebe-262">Then, set ViewStateMode to Enabled on only the controls that actually need view state.</span></span>

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample14.aspx)]

<span data-ttu-id="abebe-263">通过启用仅需要它的控件的视图状态，可以为您的 web 页面压缩视图状态的大小。</span><span class="sxs-lookup"><span data-stu-id="abebe-263">By enabling view state for only the controls that need it, you can shrink the size of the view state for your web pages.</span></span>

<a id="sqlprovider"></a>

### <a name="sqlmembershipprovider"></a><span data-ttu-id="abebe-264">SqlMembershipProvider</span><span class="sxs-lookup"><span data-stu-id="abebe-264">SqlMembershipProvider</span></span>

<span data-ttu-id="abebe-265">建议：使用通用提供程序。</span><span class="sxs-lookup"><span data-stu-id="abebe-265">Recommendation: Use Universal Providers.</span></span>

<span data-ttu-id="abebe-266">在当前的项目模板中，SqlMembershipProvider 已由[ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers)，这是可作为 NuGet 程序包。</span><span class="sxs-lookup"><span data-stu-id="abebe-266">In the current project templates, SqlMembershipProvider has been replaced by [ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers), which is available as a NuGet package.</span></span> <span data-ttu-id="abebe-267">如果已使用早期版本的模板生成的项目中使用 SqlMembershipProvider，则应切换到通用提供程序。</span><span class="sxs-lookup"><span data-stu-id="abebe-267">If you are using SqlMembershipProvider in a project that was built with an earlier version of the templates, you should switch to Universal Providers.</span></span> <span data-ttu-id="abebe-268">通用提供程序使用 Entity Framework 支持的所有数据库。</span><span class="sxs-lookup"><span data-stu-id="abebe-268">The Universal Providers work with all databases that are supported by Entity Framework.</span></span>

<span data-ttu-id="abebe-269">有关详细信息，请参阅[引入了 ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx)。</span><span class="sxs-lookup"><span data-stu-id="abebe-269">For more information, see [Introducing ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx).</span></span>

<a id="long"></a>

### <a name="long-running-requests-110-seconds"></a><span data-ttu-id="abebe-270">长时间运行的请求 (> 110 秒)</span><span class="sxs-lookup"><span data-stu-id="abebe-270">Long-running requests (>110 seconds)</span></span>

<span data-ttu-id="abebe-271">建议：使用[Websocket](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx)或[SignalR](../../../signalr/index.md)连接的客户端和使用异步 I/O 操作。</span><span class="sxs-lookup"><span data-stu-id="abebe-271">Recommendation: Use [WebSockets](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx) or [SignalR](../../../signalr/index.md) for connected clients, and use asynchronous I/O operations.</span></span>

<span data-ttu-id="abebe-272">长时间运行的请求可能导致不可预知的结果和性能不佳，在 web 应用程序中。</span><span class="sxs-lookup"><span data-stu-id="abebe-272">Long-running requests can cause unpredictable results and poor performance in your web application.</span></span> <span data-ttu-id="abebe-273">请求的默认超时设置为 110 秒。</span><span class="sxs-lookup"><span data-stu-id="abebe-273">The default timeout setting for a request is 110 seconds.</span></span> <span data-ttu-id="abebe-274">如果将会话状态用于长时间运行请求，ASP.NET 将在 110 秒后释放会话对象上的锁。</span><span class="sxs-lookup"><span data-stu-id="abebe-274">If you are using session state with a long-running request, ASP.NET will release the lock on the Session object after 110 seconds.</span></span> <span data-ttu-id="abebe-275">但是，你的应用程序可能正在会话对象的操作时释放锁，并可能未成功完成该操作。</span><span class="sxs-lookup"><span data-stu-id="abebe-275">However, your application might be in the middle of an operation on the Session object when the lock is released, and the operation might not complete successfully.</span></span> <span data-ttu-id="abebe-276">如果用户从第二个请求被阻止运行的第一个请求时，第二个请求可能会访问处于不一致状态的会话对象。</span><span class="sxs-lookup"><span data-stu-id="abebe-276">If a second request from the user is blocked while the first request is running, the second request might access the Session object in an inconsistent state.</span></span>

<span data-ttu-id="abebe-277">如果你的应用程序包括锁定 （或同步） I/O 操作，该应用程序将停止响应。</span><span class="sxs-lookup"><span data-stu-id="abebe-277">If your application includes blocking (or synchronous) I/O operations, the application will be unresponsive.</span></span>

<span data-ttu-id="abebe-278">若要提高性能，请在.NET Framework 中使用异步 I/O 操作。</span><span class="sxs-lookup"><span data-stu-id="abebe-278">To improve performance, use the asynchronous I/O operations in the .NET Framework.</span></span> <span data-ttu-id="abebe-279">此外，使用 WebSockets 或 SignalR 连接客户端到服务器。</span><span class="sxs-lookup"><span data-stu-id="abebe-279">Also, use WebSockets or SignalR for connecting clients to the server.</span></span> <span data-ttu-id="abebe-280">这些功能旨在有效地处理长时间运行的请求。</span><span class="sxs-lookup"><span data-stu-id="abebe-280">These features are designed to efficiently handle long-running requests.</span></span>
