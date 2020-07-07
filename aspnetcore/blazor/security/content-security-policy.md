---
title: 为 ASP.NET Core Blazor 强制实施内容安全策略
author: guardrex
description: 了解如何将内容安全策略 (CSP) 用于 ASP.NET Core Blazor 应用，以帮助防范跨站点脚本 (XSS) 攻击。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/content-security-policy
ms.openlocfilehash: 5c53ac64d3ae1b365b40c519eb119f913d58cad1
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85402437"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a>为 ASP.NET Core Blazor 强制实施内容安全策略

作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)

[跨站点脚本 (XSS)](xref:security/cross-site-scripting) 是一种安全漏洞，攻击者将一个或多个恶意客户端脚本置于应用的呈现内容中。 内容安全策略 (CSP) 通过通知浏览器有效的信息来帮助防御 XSS 攻击：

* 已加载内容的源，包括脚本、样式表和图像。
* 页面执行的操作，指定允许的表单 URL 目标。
* 可加载的插件。

要将 CSP 应用于应用，开发人员会在一个或多个 `Content-Security-Policy` 标头或 `<meta>` 标记中指定多个 CSP 内容安全指令。

加载页面时，浏览器会对策略进行评估。 浏览器将检查页面的源并确定它们是否满足内容安全指令的要求。 如果资源不符合策略指令，浏览器不会加载资源。 例如，请考虑不允许第三方脚本的策略。 当某个页面的 `src` 属性中包含带有第三方来源的 `<script>` 标记时，浏览器将阻止加载该脚本。

大多数新式桌面和移动浏览器（包括 Chrome、Edge、Firefox、Opera 和 Safari）都支持 CSP。 建议将 CSP 用于 Blazor 应用。

## <a name="policy-directives"></a>策略指令

至少为 Blazor 应用指定以下指令和源。 按需添加其他指令和源。 本文的[应用策略](#apply-the-policy)部分中使用了以下指令，其中提供了 Blazor WebAssembly 和 Blazor Server 的示例安全策略：

* [base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri)：限制页面的 `<base>` 标记的 URL。 指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。
* [block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content)：阻止加载混合 HTTP 和 HTTPS 内容。
* [default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src)：指示未被策略显式指定的源指令的回退。 指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。
* [img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src)：指示图像的有效源。
  * 指定 `data:`，以允许从 `data:` URL 加载图像。
  * 指定 `https:`，以允许从 HTTPS 终结点加载图像。
* [object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src)：指示 `<object>`、`<embed>` 和 `<applet>` 标记的有效源。 指定 `none` 以阻止所有 URL 源。
* [script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src)：指示脚本的有效源。
  * 指定用于启动脚本的 `https://stackpath.bootstrapcdn.com/` 主机源。
  * 指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。
  * 在 Blazor WebAssembly 应用中：
    * 指定以下哈希，以允许加载所需的 Blazor WebAssembly 内联脚本：
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * 指定 `unsafe-eval`，以使用 `eval()` 以及通过字符串创建代码的方法。
  * 在 Blazor Server 应用中，为针对样式表执行回退检测的内联脚本指定 `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` 哈希。
* [style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src)：指示样式表的有效源。
  * 指定用于启动样式表的 `https://stackpath.bootstrapcdn.com/` 主机源。
  * 指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。
  * 指定 `unsafe-inline`，以允许使用内联样式。 Blazor Server 应用中的 UI 需要内联声明，以便在初始请求后重新连接客户端和服务器。 在将来的版本中，可能会删除内联样式，以便不再需要 `unsafe-inline`。
* [upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests)：指示应通过 HTTPS 安全地获取来自不安全 (HTTP) 源的内容 URL。

除 Microsoft Internet Explorer 外的所有浏览器都支持上述指令。

获取其他内联脚本的 SHA 哈希：

* 应用[应用策略](#apply-the-policy)部分中所示的 CSP。
* 在本地运行应用时访问浏览器的开发人员工具控制台。 当存在 CSP 标头或 `meta` 标记时，浏览器将为遭到阻止的脚本计算和显示哈希。
* 将浏览器提供的哈希复制到 `script-src` 源。 在每个哈希两边使用单引号。

有关内容安全策略级别 2 浏览器的支持矩阵，请参阅[我可以使用：内容安全策略级别 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)。

## <a name="apply-the-policy"></a>应用策略

使用 `<meta>` 标记来应用策略：

* 将 `http-equiv` 属性的值设置为 `Content-Security-Policy`。
* 将指令置于 `content` 属性值中。 使用分号 (`;`) 分隔指令。
* 始终将 `meta` 标记置于 `<head>` 内容中。

以下各部分介绍 Blazor WebAssembly 和 Blazor Server 的示例策略。 对于 Blazor 的每个版本，本文都对这些示例进行了版本控制。 要使用适用于你的版本的版本，请在此网页上使用“版本”下拉选择器选择文档版本。

### Blazor WebAssembly

在 `wwwroot/index.html` 主机页面的 `<head>` 内容中，应用[策略指令](#policy-directives)部分中描述的指令：

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

### Blazor Server

在 `Pages/_Host.cshtml` 主机页面的 `<head>` 内容中，应用[策略指令](#policy-directives)部分中描述的指令：

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

`<meta>` 标记策略不支持以下指令：

* [frame-ancestors](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [report-to](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [sandbox](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

要支持上述指令，请使用名为 `Content-Security-Policy` 的标头。 指令字符串是标头的值。

## <a name="test-a-policy-and-receive-violation-reports"></a>测试策略并接收违规报告

测试有助于确认在生成初始策略时不会无意中阻止第三方脚本。

要在不强制实施策略指令的情况下在一段时间内测试策略，请将基于标头的策略的 `<meta>` 标记的 `http-equiv` 属性或标头名称设置为 `Content-Security-Policy-Report-Only`。 将故障报告作为 JSON 文档发送到指定的 URL。 有关详细信息，请参阅 [MDN Web 文档：Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)。

要在策略处于活动状态时报告违规，请参阅以下文章：

* [report-to](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [report-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

虽然不再建议使用 `report-uri`，但在所有主要浏览器都支持 `report-to` 之前，都应使用这两个指令。 不要只使用 `report-uri`，因为随时可能从浏览器中删除对 `report-uri` 的支持 。 完全支持 `report-to` 时，请删除策略中对 `report-uri` 的支持。 要跟踪 `report-to` 的使用情况，请参阅[我可以使用：report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)。

每次发布时测试和更新应用的策略。

## <a name="troubleshoot"></a>疑难解答

* 浏览器的开发人员工具控制台中出现错误。 浏览器提供以下信息：
  * 不符合策略的元素。
  * 如何修改策略以允许遭到阻止的项目。
* 仅当客户端的浏览器支持所有包含的指令时，策略才完全有效。 有关当前浏览器支持矩阵，请参阅[我可以使用：Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy)。

## <a name="additional-resources"></a>其他资源

* [MDN Web 文档：Content-Security-Policy](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [内容安全策略级别 2](https://www.w3.org/TR/CSP2/)
* [Google CSP Evaluator](https://csp-evaluator.withgoogle.com/)
