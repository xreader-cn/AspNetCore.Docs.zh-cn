---
title: 为 ASP.NET Core 强制实施内容安全策略Blazor
author: guardrex
description: 了解如何结合使用内容安全策略（CSP）与 ASP.NET Core Blazor的应用，帮助防范跨站点脚本（XSS）攻击。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/02/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/blazor/content-security-policy
ms.openlocfilehash: 8c5e1c5dd2d41efade91a612bea2855569a61fee
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775578"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>为 ASP.NET Core 强制实施内容安全策略Blazor

作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

[跨站点脚本（XSS）](xref:security/cross-site-scripting)是一个安全漏洞，攻击者将一个或多个恶意客户端脚本放入应用程序的呈现内容中。 内容安全策略（CSP）通过通知浏览器的有效情况来帮助防范 XSS 攻击：

* 已加载内容的源，包括脚本、样式表和图像。
* 页执行的操作，指定窗体的允许的 URL 目标。
* 可以加载的插件。

若要向应用程序应用 CSP，开发人员需要在一个或多*directives*个`Content-Security-Policy`标头或`<meta>`标记中指定若干 csp 内容安全指令。

加载页面时，浏览器会对策略进行评估。 浏览器将检查页的源并确定它们是否满足内容安全指令的要求。 如果资源不满足策略指令，浏览器不会加载资源。 例如，请考虑不允许第三方脚本的策略。 当页面包含`src`属性中`<script>`包含第三方来源的标记时，浏览器将阻止加载该脚本。

大多数新式桌面和移动浏览器（包括 Chrome、Edge、Firefox、Opera 和 Safari）都支持 CSP。 建议将 CSP 用于Blazor应用。

## <a name="policy-directives"></a>策略指令

至少，为Blazor应用指定以下指令和源。 根据需要添加其他指令和源。 以下指令用于本文的[应用策略](#apply-the-policy)部分，其中提供了Blazor WebAssembly 和Blazor服务器的示例安全策略：

* [base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash;限制页`<base>`标记的 url。 指定`self`以指示应用程序的源（包括方案和端口号）是有效的源。
* [阻止所有混合内容](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; -阻止加载混合 HTTP 和 HTTPS 内容。
* [默认值-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash;指示策略未显式指定的源指令的回退。 指定`self`以指示应用程序的源（包括方案和端口号）是有效的源。
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash;指示映像的有效源。
  * 指定`data:`以允许从`data:` url 加载图像。
  * 指定`https:`以允许从 HTTPS 终结点加载图像。
* [对象-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash;指示`<object>`、 `<embed>`和`<applet>`标记的有效源。 指定`none`以防止所有 URL 源。
* [脚本-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash;表示脚本的有效源。
  * 指定启动`https://stackpath.bootstrapcdn.com/`脚本的主机源。
  * 指定`self`以指示应用程序的源（包括方案和端口号）是有效的源。
  * 在Blazor WebAssembly 应用中：
    * 指定以下哈希，以允许加载Blazor所需的 WebAssembly 内联脚本：
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * 指定`unsafe-eval`使用`eval()`和方法从字符串创建代码。
  * 在Blazor服务器应用中，指定为`sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=`样式表执行回退检测的内联脚本的哈希。
* [style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash;指示样式表的有效源。
  * 指定用于`https://stackpath.bootstrapcdn.com/`启动样式表的主机源。
  * 指定`self`以指示应用程序的源（包括方案和端口号）是有效的源。
  * 指定`unsafe-inline`允许使用内联样式。 服务器应用中Blazor的 UI 需要内联声明，以便在初始请求后重新连接客户端和服务器。 在将来的版本中，可能会删除内联样式， `unsafe-inline`这样就不再需要。
* [升级-不安全-请求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash;指示应通过 HTTPS 安全地获取来自不安全（HTTP）源的内容 url。

除 Microsoft Internet Explorer 外，所有浏览器都支持前面的指令。

获取更多内联脚本的 SHA 哈希：

* 应用 "[应用策略](#apply-the-policy)" 部分中所示的 CSP。
* 在本地运行应用程序时，访问浏览器的 "开发人员工具" 控制台。 当存在 CSP 标头或`meta`标记时，浏览器将为被阻止的脚本计算并显示哈希。
* 将浏览器提供的哈希复制到`script-src`源。 在每个哈希两边使用单引号。

有关内容安全策略级别2浏览器支持矩阵，请参阅[可以使用：内容安全策略级别 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)。

## <a name="apply-the-policy"></a>应用策略

使用`<meta>`标记来应用策略：

* 将`http-equiv`属性的值设置为`Content-Security-Policy`。
* 将指令放在`content`属性值中。 用分号（`;`）分隔指令。
* 始终将`meta`标记放在`<head>`内容中。

以下部分显示了 WebAssembly 和Blazor Blazor服务器的示例策略。 对于每个版本，本文都对这些示例进行Blazor了版本控制。 若要使用适合你版本的版本，请在此网页上选择包含**版本**下拉选择器的文档版本。

### <a name="blazor-webassembly"></a>Blazor WebAssembly

在 " `<head>` *wwwroot/index.html*主机" 页的 "内容" 中，应用[策略指令](#policy-directives)部分中描述的指令：

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=' 
                          'sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

### <a name="blazor-server"></a>Blazor 服务器

在 " `<head>` *Pages/_Host. cshtml*主机" 页的 "内容" 中，应用[策略指令](#policy-directives)部分中描述的指令：

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

## <a name="meta-tag-limitations"></a>元标记限制

`<meta>`标记策略不支持以下指令：

* [框架-上级](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [报表到](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [报表-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [处理](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

若要支持前面的指令，请使用名`Content-Security-Policy`为的标头。 指令字符串是标头的值。

## <a name="test-a-policy-and-receive-violation-reports"></a>测试策略并接收冲突报告

测试有助于确认在生成初始策略时不会无意中阻止第三方脚本。

若要在不强制实施策略指令的情况下测试策略，请将基于`<meta>`标头`http-equiv`的策略的标记特性或标头名称设置为`Content-Security-Policy-Report-Only`。 故障报告作为 JSON 文档发送到指定的 URL。 有关详细信息，请参阅[MDN web 文档：仅限内容安全策略](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)。

若要在策略处于活动状态时报告冲突，请参阅以下文章：

* [报表到](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [报表-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

尽管`report-uri`不再推荐使用，但在所有主要浏览器都支持之前`report-to` ，应使用两个指令。 请勿独占使用`report-uri` ，因为对`report-uri`的支持可*随时从浏览器中删除*。 完全支持时`report-uri` `report-to` ，删除对策略的支持。 若要跟踪的`report-to`采用，请参阅[可以使用：报告到](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)。

每次发布时测试和更新应用的策略。

## <a name="troubleshoot"></a>疑难解答

* 浏览器的 "开发人员工具" 控制台中会出现错误。 浏览器提供以下信息：
  * 不符合策略的元素。
  * 如何修改策略以允许阻止的项目。
* 仅当客户端的浏览器支持所有包含的指令时，策略才会完全有效。 有关当前的浏览器支持矩阵，请参阅[可以使用：内容安全策略](https://caniuse.com/#search=Content-Security-Policy)。

## <a name="additional-resources"></a>其他资源

* [MDN web 文档：内容安全策略](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [内容安全策略级别2](https://www.w3.org/TR/CSP2/)
* [Google CSP 计算器](https://csp-evaluator.withgoogle.com/)
