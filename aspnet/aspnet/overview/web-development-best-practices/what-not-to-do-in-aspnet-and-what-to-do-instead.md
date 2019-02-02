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
# <a name="what-not-to-do-in-aspnet-and-what-to-do-instead"></a>不得在 ASP.NET 中执行的操作和转而应执行的操作

> 本主题介绍几个常见的错误的人也会在 ASP.NET web 项目中。 它提供了有关应执行哪些操作来避免这些常见的错误的建议。 它基于[presentation](http://vimeo.com/68390507)通过**Damian Edwards**挪威开发者大会。


## <a name="disclaimer"></a>免责声明

本主题不用作完整指南，以确保你的应用程序安全和高效。 仍需要遵循本主题中未列出的安全性和性能的最佳方案。 它仅建议如何避免与.NET 类和进程相关的常见错误。

## <a name="overview"></a>概述

本主题包含以下各节：

- [标准符合性](#standards)

    - [控件适配器](#adapters)
    - [在控件上的样式属性](#styleprop)
    - [页面和控件回调](#callback)
    - [浏览器功能检测](#browsercap)
- [安全性](#security)

    - [请求验证](#validation)
    - [无 cookie Forms 身份验证和会话](#cookieless)
    - [EnableViewStateMac](#viewstatemac)
    - [中等信任](#medium)
    - [&lt;appSettings&gt;](#appsettings)
    - [UrlPathEncode](#urlpathencode)
- [可靠性和性能](#performance)

    - [PreSendRequestHeaders 和 PreSendRequestContent](#presend)
    - [使用 Web 窗体的异步页面事件](#asyncevents)
    - [Fire-and-Forget Work](#fire)
    - [请求实体正文](#requestentity)
    - [Response.Redirect 和 Response.End](#redirect)
    - [EnableViewState 和 ViewStateMode](#viewstatemode)
    - [SqlMembershipProvider](#sqlprovider)
    - [长时间运行的请求 (> 110 秒)](#long)

<a id="standards"></a>

## <a name="standards-compliance"></a>标准符合性

<a id="adapters"></a>

### <a name="control-adapters"></a>控件适配器

建议：停止使用的自适应呈现的控件适配器，而使用 CSS 媒体查询和符合标准的 HTML。

要呈现为不同的设备和环境进行了自定义表示代码的.NET 2.0 中引入了控件适配器。 现在，可以使用 CSS 和 HTML 完成此自适应呈现。 应停止使用控件适配器，并将任何现有适配器转换为 CSS 和 HTML。

有关详细信息，请参阅[媒体查询](http://www.w3.org/TR/css3-mediaqueries/)和[How To:将移动页面添加到 ASP.NET Web 窗体 / MVC 应用程序](../../../whitepapers/add-mobile-pages-to-your-aspnet-web-forms-mvc-application.md)。

<a id="styleprop"></a>

### <a name="style-properties-on-controls"></a>在控件上的样式属性

建议：停止在控件标记中，设置样式值并改为在 CSS 样式表中设置格式设置的值。

Web 服务器控件包含数十个属性用于设置在行中样式属性。 例如，ForeColor 属性设置控件的文本的颜色。 你可以完成此相同的效果更高效地通过 CSS 样式表。 样式表，可以集中样式值并避免设置这些值在整个应用程序。

下面的示例演示一个 CSS 类集文本为红色。

[!code-css[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample1.css)]

以下示例演示如何动态应用的 CSS 类。

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample2.cs)]

<a id="callback"></a>

### <a name="page-and-control-callbacks"></a>页面和控件的回叫

建议：停止使用页面和控件的回调，并改为使用以下任一项：AJAX、 UpdatePanel、 MVC 操作方法、 Web API 或 SignalR。

在早期版本的 ASP.NET，页和控件的回叫方法启用更新而无需刷新整个页面的 web 页的一部分。 您现在可以实现部分页面更新通过[AJAX](../../../ajax/index.md)， [UpdatePanel](https://msdn.microsoft.com/library/bb386454.aspx)， [MVC](../../../mvc/index.md)， [Web API](../../../web-api/index.md)或[SignalR](../../../signalr/index.md). 您应停止使用回调方法，因为它们可能会导致问题使用友好的 Url 和路由。 默认情况下，控件不能将回叫方法，但如果启用此功能在控件中的，则应将其禁用。

<a id="browsercap"></a>

### <a name="browser-capability-detection"></a>浏览器功能检测

建议：停止使用静态的浏览器功能检测，并改为使用动态功能检测。

在 ASP.NET 的早期版本中，在 XML 文件中存储的每个浏览器支持的功能。 检测功能支持通过静态查找不是最好的方法。 现在，可以动态检测浏览器的支持的功能使用的功能检测框架，如[Modernizr](http://modernizr.com/)。 功能检测通过尝试使用方法或属性，然后检查以查看是否浏览器生成所需的结果来确定支持。 默认情况下，Modernizr 会包含在 Web 应用程序模板。

<a id="security"></a>

## <a name="security"></a>安全性

<a id="validation"></a>

### <a name="request-validation"></a>请求验证

建议：验证用户输入，并从用户的输出编码。

请求验证，ASP.NET，检查每个请求并停止请求，如果找到感知到的威胁的一项功能。 不依赖于用于保护应用程序免受跨站点脚本攻击的请求验证。 相反，验证用户的所有输入和输出编码。 在一些有限的情况下，您可以使用正则表达式验证输入，但在更复杂的情况下应验证使用.NET 类，以确定如果的值匹配用户输入允许的值。

下面的示例演示如何在 Uri 类中使用的静态方法以确定是否为有效的用户提供的 Uri。

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample3.cs)]

但是，若要充分验证 Uri，您还应检查以确保它指定`http`或`https`。 以下示例使用实例方法来验证 Uri 有效。

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample4.cs)]

然后再呈现为 html 格式的用户输入或 SQL 查询中包括用户输入，以确保恶意代码则不包含的值进行编码。

你可以 HTML 编码的标记中的值&lt;%: %&gt;语法，如下所示。

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample5.aspx?highlight=1)]

或者，可以在 Razor 语法中，HTML 编码 @，如下所示。

[!code-cshtml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample6.cshtml?highlight=1)]

下面的示例说明如何为 HTML 编码在代码隐藏中的值。

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample7.cs)]

若要安全地进行编码的 SQL 命令的值，请使用命令参数如[SqlParameter](https://msdn.microsoft.com/library/system.data.sqlclient.sqlparameter.aspx)。 <a id="cookieless"></a>

### <a name="cookieless-forms-authentication-and-session"></a>无 cookie forms 身份验证和会话

建议：需要 cookie。

查询字符串中传递身份验证信息并不安全。 因此，需要 cookie，应用程序包括身份验证。 如果您的 cookie 存储敏感信息，请考虑 cookie 要求 SSL。

下面的示例演示如何在 Web.config 文件中指定窗体身份验证，需要通过 SSL 传输的 cookie。

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample8.xml)]

<a id="viewstatemac"></a>

### <a name="enableviewstatemac"></a>EnableViewStateMac

建议：永远不会设置为 false。

默认情况下，设置 EnbableViewStateMac 为 true。 即使你的应用程序未使用视图状态，请不设置 enableviewstatemac 设为 false。 此值设置为 false 将使你的应用程序容易受到跨站点脚本。

从 ASP.NET 4.5.2 开始，运行时强制实施**EnableViewStateMac = true**。 即使将其设置为 false 时，运行时将忽略此值，并继续执行设置的值为 true。 有关详细信息，请参阅[ASP.NET 4.5.2 和 EnableViewStateMac](https://blogs.msdn.com/b/webdev/archive/2014/05/07/asp-net-4-5-2-and-enableviewstatemac.aspx)。

下面的示例演示如何将 enableviewstatemac 设为 true。 不需要实际设置此值为 true，因为它是默认情况下，则返回 true。 但是，如果你已将其设置为 false 中任意页在应用程序中，则必须立即更正此值。

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample9.aspx)]

<a id="medium"></a>

### <a name="medium-trust"></a>中等信任

建议：作为安全边界不依赖于中等信任 （或任何其他信任级别）。

部分信任环境不能充分保护您的应用程序，因此不应。 相反，使用完全信任，并将在单独的应用程序池不受信任的应用程序隔离。 此外，运行下一个唯一标识每个应用程序池。 有关详细信息，请参阅[ASP.NET 部分信任环境不能保证应用程序隔离](https://support.microsoft.com/kb/2698981)。

<a id="appsettings"></a>

### <a name="ltappsettingsgt"></a>&lt;appSettings&gt;

建议：不要禁用中的安全设置&lt;appSettings&gt;元素。

AppSettings 元素包含多个值所需的安全更新。 您不应更改或禁用这些值。 如果部署更新时，必须禁用这些值，请立即重新启用完成部署后。

有关详细信息，请参阅[ASP.NET appSettings 元素](https://msdn.microsoft.com/library/hh975440.aspx)。

<a id="urlpathencode"></a>

### <a name="urlpathencode"></a>UrlPathEncode

建议：使用[UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx)相反。

UrlPathEncode 方法已添加到.NET Framework，才能解决非常特定的浏览器兼容性问题。 它不会充分编码 URL，并不会从跨站点脚本保护你的应用程序。 你决不应在应用程序中使用它。 请改用[UrlEncode](https://msdn.microsoft.com/library/zttxte6w.aspx)。

下面的示例演示如何将编码的 URL 作为超链接控件的查询字符串参数传递。

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample10.cs)]

<a id="performance"></a>

## <a name="reliability-and-performance"></a>可靠性和性能

<a id="presend"></a>

### <a name="presendrequestheaders-and-presendrequestcontent"></a>PreSendRequestHeaders 和 PreSendRequestContent

建议：使用托管模块不使用这些事件。 相反，编写本机 IIS 模块来执行所需的任务。 请参阅[创建本机代码 HTTP 模块](https://msdn.microsoft.com/library/ms693629.aspx)。

可以使用[PreSendRequestHeaders](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestheaders.aspx)并[PreSendRequestContent](https://msdn.microsoft.com/library/system.web.httpapplication.presendrequestcontent.aspx)具有本机 IIS 模块的事件。
> [!WARNING]
> 不要使用`PreSendRequestHeaders`并`PreSendRequestContent`实现的托管模块`IHttpModule`。 对于异步请求，设置这些属性可能会导致问题。 应用程序请求路由 (ARR) 和 websocket 的组合可能会导致访问冲突异常可能会导致崩溃的 w3wp。 例如，iiscore ！W3_CONTEXT_BASE::GetIsLastNotification + 68 iiscore.dll 中的导致访问冲突异常 (0xC0000005)。

<a id="asyncevents"></a>

### <a name="asynchronous-page-events-with-web-forms"></a>使用 web 窗体的异步页面事件

建议：在 Web 窗体，应避免编写异步 void 方法的页面生命周期事件，并改为使用[Page.RegisterAsyncTask](https://msdn.microsoft.com/library/system.web.ui.page.registerasynctask.aspx)异步代码的。

当标记的页面事件**异步**并**void**，不能确定何时完成的异步代码。 相反，使用 Page.RegisterAsyncTask 使您能够跟踪其完成的方式运行异步代码。

下面的示例演示一个按钮的单击处理程序，其中包含异步代码。 此示例包括以异步方式读取一个字符串值提供仅为一个异步任务的简化示例，而不是建议的做法。

[!code-csharp[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample11.cs)]

如果使用的异步任务，设置为 4.5 （或更高版本） 的 Http 运行时目标框架的 Web.config 文件中。 设置目标框架为 4.5 将新的同步上下文上的.NET 4.5 中添加。 此值在新项目在 Visual Studio 中，默认情况下设置，但不是设置，如果您正在使用现有的项目。

[!code-xml[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample12.xml)]

<a id="fire"></a>

### <a name="fire-and-forget-work"></a>即发即弃工作

建议：在处理 asp.net 请求时，避免启动即发即弃工作 （此类调用 ThreadPool.QueueUserWorkItem 方法或创建一个计时器，用于重复调用委托）。

如果你的应用程序已在 ASP.NET 中运行的即发即弃工作，你的应用程序可以获得不同步。在任何时候，应用程序域可以销毁这意味着您正在进行的过程可能不再匹配应用程序的当前状态。

应将此类在 ASP.NET 外部工作。 可以使用在 Azure 中的 Web 作业、 Windows 服务或辅助角色，以执行正在进行的工作，并从另一个进程中运行该代码。

如果必须执行这项工作在 ASP.NET 内的，则可以添加名为 Nuget 包[WebBackgrounder](http://www.nuget.org/packages/webbackgrounder)要运行此代码。

<a id="requestentity"></a>

### <a name="request-entity-body"></a>请求实体正文

建议：避免读取 Request.Form 或 Request.InputStream 之前处理程序的执行事件。

应从 Request.Form 或 Request.InputStream 读取的最早是在处理程序的过程执行事件。 在 MVC 中，控制器是处理程序并执行事件时运行操作方法。 在 Web 窗体页是处理程序和执行事件是 Page.Init 事件激发时。 如果以前的执行事件读取请求实体正文，您会影响处理请求。

如果需要读取请求实体正文的执行事件之前，可以使用两种[Request.GetBufferlessInputStream](https://msdn.microsoft.com/library/ff406798.aspx)或[Request.GetBufferedInputStream](https://msdn.microsoft.com/library/system.web.httprequest.getbufferedinputstream.aspx)。 当使用 GetBufferlessInputStream 时，请求中获取原始流并承担处理整个请求。 后因为它们不填充由 ASP.NET，调用 GetBufferlessInputStream、 Request.Form 和 Request.InputStream 不可用。 当使用 GetBufferedInputStream 时，将从请求中获取流的副本。 Request.Form 和 Request.InputStream 是请求更高版本中仍然可用，因为 ASP.NET 填充另一个副本。

<a id="redirect"></a>

### <a name="responseredirect-and-responseend"></a>Response.Redirect 和 Response.End

建议：请注意的线程之后调用的处理方式的差异[Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx)。

[Response.Redirect(String)](https://msdn.microsoft.com/library/t9dwyts4.aspx)方法调用 Response.End 方法。 在同步过程中，调用 Request.Redirect 会导致当前线程立即中止。 但是，在异步流程中调用 Response.Redirect 不会中止当前线程，因此请求将继续执行代码。 在异步过程，必须从要停止执行代码的方法来返回该任务。

在 MVC 项目中，不应调用 Response.Redirect。 改为返回 RedirectResult。

<a id="viewstatemode"></a>

### <a name="enableviewstate-and-viewstatemode"></a>EnableViewState 和 ViewStateMode

建议：使用 ViewStateMode，而不是 EnableViewState，来提供精细控制哪些控件使用视图状态。

当将 EnableViewState 设置为 false 在 Page 指令中时，视图状态的页内的所有控件禁用且无法启用。 如果你想要启用仅在页面中某些控件的视图状态，ViewStateMode 为已禁用的页面设置。

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample13.aspx)]

然后，实际上需要视图状态的控件上设置为已启用的 ViewStateMode。

[!code-aspx[Main](what-not-to-do-in-aspnet-and-what-to-do-instead/samples/sample14.aspx)]

通过启用仅需要它的控件的视图状态，可以为您的 web 页面压缩视图状态的大小。

<a id="sqlprovider"></a>

### <a name="sqlmembershipprovider"></a>SqlMembershipProvider

建议：使用通用提供程序。

在当前的项目模板中，SqlMembershipProvider 已由[ASP.NET Universal Providers](http://www.nuget.org/packages/Microsoft.AspNet.Providers)，这是可作为 NuGet 程序包。 如果已使用早期版本的模板生成的项目中使用 SqlMembershipProvider，则应切换到通用提供程序。 通用提供程序使用 Entity Framework 支持的所有数据库。

有关详细信息，请参阅[引入了 ASP.NET Universal Providers](http://www.hanselman.com/blog/IntroducingSystemWebProvidersASPNETUniversalProvidersForSessionMembershipRolesAndUserProfileOnSQLCompactAndSQLAzure.aspx)。

<a id="long"></a>

### <a name="long-running-requests-110-seconds"></a>长时间运行的请求 (> 110 秒)

建议：使用[Websocket](https://msdn.microsoft.com/library/system.net.websockets.websocket.aspx)或[SignalR](../../../signalr/index.md)连接的客户端和使用异步 I/O 操作。

长时间运行的请求可能导致不可预知的结果和性能不佳，在 web 应用程序中。 请求的默认超时设置为 110 秒。 如果将会话状态用于长时间运行请求，ASP.NET 将在 110 秒后释放会话对象上的锁。 但是，你的应用程序可能正在会话对象的操作时释放锁，并可能未成功完成该操作。 如果用户从第二个请求被阻止运行的第一个请求时，第二个请求可能会访问处于不一致状态的会话对象。

如果你的应用程序包括锁定 （或同步） I/O 操作，该应用程序将停止响应。

若要提高性能，请在.NET Framework 中使用异步 I/O 操作。 此外，使用 WebSockets 或 SignalR 连接客户端到服务器。 这些功能旨在有效地处理长时间运行的请求。
