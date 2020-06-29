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
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/content-security-policy
ms.openlocfilehash: 360fff9383e25a6b5b9308cfebd397f7f4ee31a6
ms.sourcegitcommit: 066d66ea150f8aab63f9e0e0668b06c9426296fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2020
ms.locfileid: "85242974"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="8bd51-103">为 ASP.NET Core Blazor 强制实施内容安全策略</span><span class="sxs-lookup"><span data-stu-id="8bd51-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="8bd51-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8bd51-104">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="8bd51-105">[跨站点脚本 (XSS)](xref:security/cross-site-scripting) 是一种安全漏洞，攻击者将一个或多个恶意客户端脚本置于应用的呈现内容中。</span><span class="sxs-lookup"><span data-stu-id="8bd51-105">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="8bd51-106">内容安全策略 (CSP) 通过通知浏览器有效的信息来帮助防御 XSS 攻击：</span><span class="sxs-lookup"><span data-stu-id="8bd51-106">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="8bd51-107">已加载内容的源，包括脚本、样式表和图像。</span><span class="sxs-lookup"><span data-stu-id="8bd51-107">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="8bd51-108">页面执行的操作，指定允许的表单 URL 目标。</span><span class="sxs-lookup"><span data-stu-id="8bd51-108">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="8bd51-109">可加载的插件。</span><span class="sxs-lookup"><span data-stu-id="8bd51-109">Plugins that can be loaded.</span></span>

<span data-ttu-id="8bd51-110">要将 CSP 应用于应用，开发人员会在一个或多个 `Content-Security-Policy` 标头或 `<meta>` 标记中指定多个 CSP 内容安全指令。</span><span class="sxs-lookup"><span data-stu-id="8bd51-110">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="8bd51-111">加载页面时，浏览器会对策略进行评估。</span><span class="sxs-lookup"><span data-stu-id="8bd51-111">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="8bd51-112">浏览器将检查页面的源并确定它们是否满足内容安全指令的要求。</span><span class="sxs-lookup"><span data-stu-id="8bd51-112">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="8bd51-113">如果资源不符合策略指令，浏览器不会加载资源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-113">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="8bd51-114">例如，请考虑不允许第三方脚本的策略。</span><span class="sxs-lookup"><span data-stu-id="8bd51-114">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="8bd51-115">当某个页面的 `src` 属性中包含带有第三方来源的 `<script>` 标记时，浏览器将阻止加载该脚本。</span><span class="sxs-lookup"><span data-stu-id="8bd51-115">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="8bd51-116">大多数新式桌面和移动浏览器（包括 Chrome、Edge、Firefox、Opera 和 Safari）都支持 CSP。</span><span class="sxs-lookup"><span data-stu-id="8bd51-116">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="8bd51-117">建议将 CSP 用于 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="8bd51-117">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="8bd51-118">策略指令</span><span class="sxs-lookup"><span data-stu-id="8bd51-118">Policy directives</span></span>

<span data-ttu-id="8bd51-119">至少为 Blazor 应用指定以下指令和源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-119">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="8bd51-120">按需添加其他指令和源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-120">Add additional directives and sources as needed.</span></span> <span data-ttu-id="8bd51-121">本文的[应用策略](#apply-the-policy)部分中使用了以下指令，其中提供了 Blazor WebAssembly 和 Blazor 服务器的示例安全策略：</span><span class="sxs-lookup"><span data-stu-id="8bd51-121">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="8bd51-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri)：限制页面的 `<base>` 标记的 URL。</span><span class="sxs-lookup"><span data-stu-id="8bd51-122">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="8bd51-123">指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-123">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="8bd51-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content)：阻止加载混合 HTTP 和 HTTPS 内容。</span><span class="sxs-lookup"><span data-stu-id="8bd51-124">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="8bd51-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src)：指示未被策略显式指定的源指令的回退。</span><span class="sxs-lookup"><span data-stu-id="8bd51-125">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="8bd51-126">指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-126">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="8bd51-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src)：指示图像的有效源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-127">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="8bd51-128">指定 `data:`，以允许从 `data:` URL 加载图像。</span><span class="sxs-lookup"><span data-stu-id="8bd51-128">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="8bd51-129">指定 `https:`，以允许从 HTTPS 终结点加载图像。</span><span class="sxs-lookup"><span data-stu-id="8bd51-129">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="8bd51-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src)：指示 `<object>`、`<embed>` 和 `<applet>` 标记的有效源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-130">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="8bd51-131">指定 `none` 以阻止所有 URL 源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-131">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="8bd51-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src)：指示脚本的有效源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-132">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="8bd51-133">指定用于启动脚本的 `https://stackpath.bootstrapcdn.com/` 主机源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-133">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="8bd51-134">指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-134">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="8bd51-135">在 Blazor WebAssembly 应用中：</span><span class="sxs-lookup"><span data-stu-id="8bd51-135">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="8bd51-136">指定以下哈希，以允许加载所需的 Blazor WebAssembly 内联脚本：</span><span class="sxs-lookup"><span data-stu-id="8bd51-136">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="8bd51-137">指定 `unsafe-eval`，以使用 `eval()` 以及通过字符串创建代码的方法。</span><span class="sxs-lookup"><span data-stu-id="8bd51-137">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="8bd51-138">在 Blazor 服务器应用中，为针对样式表执行回退检测的内联脚本指定 `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` 哈希。</span><span class="sxs-lookup"><span data-stu-id="8bd51-138">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="8bd51-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src)：指示样式表的有效源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-139">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="8bd51-140">指定用于启动样式表的 `https://stackpath.bootstrapcdn.com/` 主机源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-140">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="8bd51-141">指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-141">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="8bd51-142">指定 `unsafe-inline`，以允许使用内联样式。</span><span class="sxs-lookup"><span data-stu-id="8bd51-142">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="8bd51-143">Blazor 服务器应用中的 UI 需要内联声明，以便在初始请求后重新连接客户端和服务器。</span><span class="sxs-lookup"><span data-stu-id="8bd51-143">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="8bd51-144">在将来的版本中，可能会删除内联样式，以便不再需要 `unsafe-inline`。</span><span class="sxs-lookup"><span data-stu-id="8bd51-144">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="8bd51-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests)：指示应通过 HTTPS 安全地获取来自不安全 (HTTP) 源的内容 URL。</span><span class="sxs-lookup"><span data-stu-id="8bd51-145">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="8bd51-146">除 Microsoft Internet Explorer 外的所有浏览器都支持上述指令。</span><span class="sxs-lookup"><span data-stu-id="8bd51-146">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="8bd51-147">获取其他内联脚本的 SHA 哈希：</span><span class="sxs-lookup"><span data-stu-id="8bd51-147">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="8bd51-148">应用[应用策略](#apply-the-policy)部分中所示的 CSP。</span><span class="sxs-lookup"><span data-stu-id="8bd51-148">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="8bd51-149">在本地运行应用时访问浏览器的开发人员工具控制台。</span><span class="sxs-lookup"><span data-stu-id="8bd51-149">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="8bd51-150">当存在 CSP 标头或 `meta` 标记时，浏览器将为遭到阻止的脚本计算和显示哈希。</span><span class="sxs-lookup"><span data-stu-id="8bd51-150">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="8bd51-151">将浏览器提供的哈希复制到 `script-src` 源。</span><span class="sxs-lookup"><span data-stu-id="8bd51-151">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="8bd51-152">在每个哈希两边使用单引号。</span><span class="sxs-lookup"><span data-stu-id="8bd51-152">Use single quotes around each hash.</span></span>

<span data-ttu-id="8bd51-153">有关内容安全策略级别 2 浏览器的支持矩阵，请参阅[我可以使用：内容安全策略级别 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)。</span><span class="sxs-lookup"><span data-stu-id="8bd51-153">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="8bd51-154">应用策略</span><span class="sxs-lookup"><span data-stu-id="8bd51-154">Apply the policy</span></span>

<span data-ttu-id="8bd51-155">使用 `<meta>` 标记来应用策略：</span><span class="sxs-lookup"><span data-stu-id="8bd51-155">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="8bd51-156">将 `http-equiv` 属性的值设置为 `Content-Security-Policy`。</span><span class="sxs-lookup"><span data-stu-id="8bd51-156">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="8bd51-157">将指令置于 `content` 属性值中。</span><span class="sxs-lookup"><span data-stu-id="8bd51-157">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="8bd51-158">使用分号 (`;`) 分隔指令。</span><span class="sxs-lookup"><span data-stu-id="8bd51-158">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="8bd51-159">始终将 `meta` 标记置于 `<head>` 内容中。</span><span class="sxs-lookup"><span data-stu-id="8bd51-159">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="8bd51-160">以下各部分介绍 Blazor WebAssembly 和 Blazor 服务器的示例策略。</span><span class="sxs-lookup"><span data-stu-id="8bd51-160">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="8bd51-161">对于 Blazor 的每个版本，本文都对这些示例进行了版本控制。</span><span class="sxs-lookup"><span data-stu-id="8bd51-161">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="8bd51-162">要使用适用于你的版本的版本，请在此网页上使用“版本”下拉选择器选择文档版本。</span><span class="sxs-lookup"><span data-stu-id="8bd51-162">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="8bd51-163"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8bd51-163"> WebAssembly</span></span>

<span data-ttu-id="8bd51-164">在 `wwwroot/index.html` 主机页面的 `<head>` 内容中，应用[策略指令](#policy-directives)部分中描述的指令：</span><span class="sxs-lookup"><span data-stu-id="8bd51-164">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="8bd51-165"> 服务器</span><span class="sxs-lookup"><span data-stu-id="8bd51-165"> Server</span></span>

<span data-ttu-id="8bd51-166">在 `Pages/_Host.cshtml` 主机页面的 `<head>` 内容中，应用[策略指令](#policy-directives)部分中描述的指令：</span><span class="sxs-lookup"><span data-stu-id="8bd51-166">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

## <a name="meta-tag-limitations"></a><span data-ttu-id="8bd51-167">元标记限制</span><span class="sxs-lookup"><span data-stu-id="8bd51-167">Meta tag limitations</span></span>

<span data-ttu-id="8bd51-168">`<meta>` 标记策略不支持以下指令：</span><span class="sxs-lookup"><span data-stu-id="8bd51-168">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="8bd51-169">frame-ancestors</span><span class="sxs-lookup"><span data-stu-id="8bd51-169">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="8bd51-170">report-to</span><span class="sxs-lookup"><span data-stu-id="8bd51-170">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="8bd51-171">report-uri</span><span class="sxs-lookup"><span data-stu-id="8bd51-171">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="8bd51-172">sandbox</span><span class="sxs-lookup"><span data-stu-id="8bd51-172">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="8bd51-173">要支持上述指令，请使用名为 `Content-Security-Policy` 的标头。</span><span class="sxs-lookup"><span data-stu-id="8bd51-173">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="8bd51-174">指令字符串是标头的值。</span><span class="sxs-lookup"><span data-stu-id="8bd51-174">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="8bd51-175">测试策略并接收违规报告</span><span class="sxs-lookup"><span data-stu-id="8bd51-175">Test a policy and receive violation reports</span></span>

<span data-ttu-id="8bd51-176">测试有助于确认在生成初始策略时不会无意中阻止第三方脚本。</span><span class="sxs-lookup"><span data-stu-id="8bd51-176">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="8bd51-177">要在不强制实施策略指令的情况下在一段时间内测试策略，请将基于标头的策略的 `<meta>` 标记的 `http-equiv` 属性或标头名称设置为 `Content-Security-Policy-Report-Only`。</span><span class="sxs-lookup"><span data-stu-id="8bd51-177">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="8bd51-178">将故障报告作为 JSON 文档发送到指定的 URL。</span><span class="sxs-lookup"><span data-stu-id="8bd51-178">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="8bd51-179">有关详细信息，请参阅 [MDN Web 文档：Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)。</span><span class="sxs-lookup"><span data-stu-id="8bd51-179">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="8bd51-180">要在策略处于活动状态时报告违规，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="8bd51-180">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="8bd51-181">report-to</span><span class="sxs-lookup"><span data-stu-id="8bd51-181">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="8bd51-182">report-uri</span><span class="sxs-lookup"><span data-stu-id="8bd51-182">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="8bd51-183">虽然不再建议使用 `report-uri`，但在所有主要浏览器都支持 `report-to` 之前，都应使用这两个指令。</span><span class="sxs-lookup"><span data-stu-id="8bd51-183">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="8bd51-184">不要只使用 `report-uri`，因为随时可能从浏览器中删除对 `report-uri` 的支持 。</span><span class="sxs-lookup"><span data-stu-id="8bd51-184">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="8bd51-185">完全支持 `report-to` 时，请删除策略中对 `report-uri` 的支持。</span><span class="sxs-lookup"><span data-stu-id="8bd51-185">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="8bd51-186">要跟踪 `report-to` 的使用情况，请参阅[我可以使用：report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)。</span><span class="sxs-lookup"><span data-stu-id="8bd51-186">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="8bd51-187">每次发布时测试和更新应用的策略。</span><span class="sxs-lookup"><span data-stu-id="8bd51-187">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="8bd51-188">疑难解答</span><span class="sxs-lookup"><span data-stu-id="8bd51-188">Troubleshoot</span></span>

* <span data-ttu-id="8bd51-189">浏览器的开发人员工具控制台中出现错误。</span><span class="sxs-lookup"><span data-stu-id="8bd51-189">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="8bd51-190">浏览器提供以下信息：</span><span class="sxs-lookup"><span data-stu-id="8bd51-190">Browsers provide information about:</span></span>
  * <span data-ttu-id="8bd51-191">不符合策略的元素。</span><span class="sxs-lookup"><span data-stu-id="8bd51-191">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="8bd51-192">如何修改策略以允许遭到阻止的项目。</span><span class="sxs-lookup"><span data-stu-id="8bd51-192">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="8bd51-193">仅当客户端的浏览器支持所有包含的指令时，策略才完全有效。</span><span class="sxs-lookup"><span data-stu-id="8bd51-193">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="8bd51-194">有关当前浏览器支持矩阵，请参阅[我可以使用：Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy)。</span><span class="sxs-lookup"><span data-stu-id="8bd51-194">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8bd51-195">其他资源</span><span class="sxs-lookup"><span data-stu-id="8bd51-195">Additional resources</span></span>

* [<span data-ttu-id="8bd51-196">MDN Web 文档：Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="8bd51-196">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="8bd51-197">内容安全策略级别 2</span><span class="sxs-lookup"><span data-stu-id="8bd51-197">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="8bd51-198">Google CSP Evaluator</span><span class="sxs-lookup"><span data-stu-id="8bd51-198">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
