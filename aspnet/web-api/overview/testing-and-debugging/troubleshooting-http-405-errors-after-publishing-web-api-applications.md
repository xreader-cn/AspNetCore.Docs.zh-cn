---
uid: web-api/overview/testing-and-debugging/troubleshooting-http-405-errors-after-publishing-web-api-applications
title: 故障排除 HTTP 405 错误后发布的 Web API 应用程序 |Microsoft Docs
author: rmcmurray
description: 本教程介绍如何在发布到生产 web 服务器的 Web API 应用程序后排除 HTTP 405 错误。
ms.author: riande
ms.date: 01/23/2019
ms.assetid: 07ec7d37-023f-43ea-b471-60b08ce338f7
msc.legacyurl: /web-api/overview/testing-and-debugging/troubleshooting-http-405-errors-after-publishing-web-api-applications
msc.type: authoredcontent
ms.openlocfilehash: ce5b617cc1032d190cc2450aa554b462ea6f6156
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667409"
---
# <a name="troubleshooting-http-405-errors-after-publishing-web-api-applications"></a>在发布 Web API 应用程序后排除 HTTP 405 错误

> 本教程介绍如何在发布到生产 web 服务器的 Web API 应用程序后排除 HTTP 405 错误。
> 
> ## <a name="software-used-in-this-tutorial"></a>本教程中使用的软件
> 
> 
> - [Internet 信息服务 (IIS)](https://www.iis.net/) （7 或更高版本）
> - [Web API](../../index.md) 


Web API 应用程序通常使用多个常见的 HTTP 谓词：GET、 POST、 PUT、 DELETE，有时修补程序。 话虽如此，开发人员可能会遇到这些动词由另一个在其生产服务器上，这将导致在 Visual Studio 或开发服务器上正常工作的 Web API 控制器将返回其中的情况下的 IIS 模块的情况下HTTP 405 错误时部署到生产服务器。 幸运的是此问题得到轻松解决，但解决方法需要发生问题的原因的说明。

## <a name="what-causes-http-405-errors"></a>哪些因素会导致 HTTP 405 错误

了解如何故障 HTTP 405 错误的第一步是了解 HTTP 405 错误的实际含义。 HTTP 是主要用于管理文档[RFC 2616](http://www.ietf.org/rfc/rfc2616.txt)，用于定义 HTTP 405 状态代码作为***不允许的方法***，并进一步介绍了此状态代码以这种情况其中&quot;方法指定在请求 URI 标识的资源不允许请求行。&quot;换而言之，HTTP 客户端已请求的特定 URL 不是允许的 HTTP 谓词。

作为简要回顾，以下是几种最常用的 HTTP 方法定义在 RFC 2616、 RFC 4918 和 RFC 5789:

| HTTP 方法 | 描述 |
| --- | --- |
| **GET** | 此方法用于从和检索数据 URI，它可能最常用的 HTTP 方法。 |
| **HEAD** | 此方法很像 GET 方法，不同之处在于它不会实际检索请求 URI 中的数据-它只需检索 HTTP 状态。 |
| **POST** | 此方法通常用于将新数据发送到 URI;通常使用 POST 提交表单数据。 |
| **PUT** | 此方法通常用于将原始数据发送到的 URI;PUT 通常用于提交到 Web API 应用程序的 JSON 或 XML 数据。 |
| **DELETE** | 此方法用于从 URI 中删除数据。 |
| **选项** | 此方法通常用于检索的 uri，支持的 HTTP 方法的列表。 |
| **复制移动** | 这两种方法用于 WebDAV，并且它们的用途是很容易理解。 |
| **MKCOL** | 使用 WebDAV，使用此方法，它用于在指定 URI 处创建集合 （例如目录）。 |
| **PROPFIND PROPPATCH** | 这两种方法用于 WebDAV，并且它们用于查询或设置一个 URI 的属性。 |
| **锁定解除锁定** | 使用 WebDAV，使用这两种方法，它们用于锁定/解锁创作时，请求 URI 标识的资源。 |
| **PATCH** | 此方法用于修改现有的 HTTP 资源。 |

当这些 HTTP 方法之一配置为在服务器上使用时，服务器将使用的 HTTP 状态和其他适用于请求的数据响应。 (例如，GET 方法可能会收到 HTTP 200***确定***响应和 PUT 方法可能会收到 HTTP 201***创建***响应。)

如果不配置为在服务器上使用的 HTTP 方法，服务器将响应 HTTP 501***未实现***错误。

但是，当某个 HTTP 方法配置为使用的服务器上，但已禁用为给定的 URI，则服务器将响应使用 HTTP 405***不允许的方法***错误。

## <a name="example-http-405-error"></a>示例 HTTP 405 错误

以下示例 HTTP 请求和响应说明了其中的 HTTP 客户端尝试将值放到 web 服务器上的 Web API 应用程序和服务器将返回 HTTP 错误状态的 PUT 方法不是允许的情况：


HTTP 请求：


[!code-console[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample1.cmd)]


HTTP 响应：


[!code-console[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample2.cmd)]


在此示例中，HTTP 客户端将有效的 JSON 请求发送到 web 服务器上的 Web API 应用程序的 URL，但服务器返回 HTTP 405 错误消息指示该 PUT 方法不允许的 url。 与此相反，如果在请求 URI 与 Web API 应用程序的路由不匹配，服务器将返回 HTTP 404***找不到***错误。

## <a name="resolve-http-405-errors"></a>解决 HTTP 405 错误

有多个原因为什么可能不允许特定的 HTTP 谓词，但没有前导导致此错误在 IIS 中的一个主要方案： 为相同的谓词/方法，定义多个处理程序和一个处理程序正在阻止从预期的处理程序处理请求。 通过说明，IIS 将处理从第一个上次根据 applicationHost.config 和 web.config 文件，将使用的路径、 谓词、 资源、 等，第一个匹配的组合来处理该请求中的顺序处理程序条目的处理程序。

下面的示例是使用 PUT 方法将数据提交到 Web API 应用程序时，已返回 HTTP 405 错误的 IIS 服务器的 applicationHost.config 文件的摘录。 在此摘录中，定义了多个 HTTP 处理程序，以及每个处理程序具有一组不同的 HTTP 方法为其配置-列表中的最后一个条目是静态内容处理程序，这是其他处理程序有 chanc 之后使用的默认处理程序若要检查请求的 e:

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample3.xml)]

在上述示例中，WebDAV 处理程序以及 ASP.NET （这用于 Web API） 的无扩展名 URL 处理是清楚地定义单独的 HTTP 方法的列表。 请注意将 ISAPI DLL 处理程序配置的所有 HTTP 方法，尽管此配置不一定会导致错误。 但是，配置设置，例如 HTTP 405 错误故障排除时，考虑这种需求。

在上述示例中，ISAPI DLL 处理程序不是问题的原因。事实上，在 IIS 服务器的 applicationHost.config 文件中未定义问题-问题由 Visual Studio 中创建 Web API 应用程序时进行的 web.config 文件中的条目。 从应用程序的 web.config 文件的以下摘录显示了此问题的位置：

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample4.xml)]

在此摘录中，ASP.NET 的无扩展名 URL 处理程序重新定义，以包括其他将与 Web API 应用程序一起使用的 HTTP 方法。 但是，由于 WebDAV 处理程序定义一组类似的 HTTP 方法，因此将发生冲突。 在此特定情况下，WebDAV 处理程序定义，并加载的 IIS，即使为包括 Web API 应用程序的网站禁用 WebDAV 也是如此。 在处理期间的 HTTP PUT 请求，IIS 将调用 WebDAV 模块由于定义 PUT 谓词。 当调用 WebDAV 模块时，它会检查其配置，并可以看到，已禁用，因此它将返回 HTTP 405***不允许的方法***错误有关的任何请求都类似于 WebDAV 请求。 若要解决此问题，应从在其中定义 Web API 应用程序的网站的 HTTP 模块的列表中删除 WebDAV。 下面的示例显示了什么，可能看起来：

[!code-xml[Main](troubleshooting-http-405-errors-after-publishing-web-api-applications/samples/sample5.xml)]

应用程序从开发环境发布到生产环境中，并出现这种情况是因为处理程序/模块的列表是开发和生产环境之间的差异后，经常会遇到这种情况。 例如，如果你正在使用 Visual Studio 2012 或更高版本开发的 Web API 应用程序，IIS Express 是用于测试的默认 web 服务器。 此开发 web 服务器是按比例缩小版本的服务器产品中随附的完整 IIS 功能，此开发 web 服务器包含已添加对开发方案的一些更改。 例如，WebDAV 模块通常安装在运行完整版本的 IIS，在生产 web 服务器上但也可能不是在实际应用中。 IIS (IIS Express)，开发版本安装 WebDAV 模块，但 WebDAV 模块的条目故意注释掉，因此 WebDAV 模块在 IIS Express 上永远不会进行加载，除非您专门更改 IIS Express 配置若要添加到 IIS Express 安装 WebDAV 功能的设置。 因此，web 应用程序可能会在开发计算机上正常工作，但当您发布到生产 web 服务器的 Web API 应用时可能会遇到 HTTP 405 错误。

## <a name="summary"></a>总结

HTTP 405 错误导致的 HTTP 方法不允许由 web 服务器的请求的 URL。 已为特定的动词，定义特定的处理程序和该处理程序重写您希望处理该请求的处理程序时，通常是出现这种情况。

如果你遇到的情况下，在其中收到 HTTP 501 错误消息，这意味着在服务器上未实现的特定功能，这通常意味着没有在您的 IIS 设置中定义的与 HTTP 请求匹配的任何处理程序的可能表示某些内容未正确安装在系统上或内容已修改 IIS 设置，因此没有任何处理程序定义该支持特定的 HTTP 方法。 若要解决该问题，您需要重新安装任何应用程序尝试使用具有没有相应的模块或处理程序定义的 HTTP 方法。
