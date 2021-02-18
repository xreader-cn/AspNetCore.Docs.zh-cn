---
title: 为 ASP.NET Core Blazor 强制实施内容安全策略
author: guardrex
description: 了解如何将内容安全策略 (CSP) 用于 ASP.NET Core Blazor 应用，以帮助防范跨站点脚本 (XSS) 攻击。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/security/content-security-policy
ms.openlocfilehash: f44f348947e31864f5d0d44c9caf1a3aa9c3b1d4
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280340"
---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="bbaa8-103">为 ASP.NET Core Blazor 强制实施内容安全策略</span><span class="sxs-lookup"><span data-stu-id="bbaa8-103">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="bbaa8-104">[跨站点脚本 (XSS)](xref:security/cross-site-scripting) 是一种安全漏洞，攻击者将一个或多个恶意客户端脚本置于应用的呈现内容中。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-104">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="bbaa8-105">内容安全策略 (CSP) 通过通知浏览器有效的信息来帮助防御 XSS 攻击：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-105">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="bbaa8-106">已加载内容的源，包括脚本、样式表和图像。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-106">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="bbaa8-107">页面执行的操作，指定允许的表单 URL 目标。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-107">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="bbaa8-108">可加载的插件。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-108">Plugins that can be loaded.</span></span>

<span data-ttu-id="bbaa8-109">要将 CSP 应用于应用，开发人员会在一个或多个 `Content-Security-Policy` 标头或 `<meta>` 标记中指定多个 CSP 内容安全指令。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-109">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="bbaa8-110">加载页面时，浏览器会对策略进行评估。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-110">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="bbaa8-111">浏览器将检查页面的源并确定它们是否满足内容安全指令的要求。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-111">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="bbaa8-112">如果资源不符合策略指令，浏览器不会加载资源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-112">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="bbaa8-113">例如，请考虑不允许第三方脚本的策略。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-113">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="bbaa8-114">当某个页面的 `src` 属性中包含带有第三方来源的 `<script>` 标记时，浏览器将阻止加载该脚本。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-114">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="bbaa8-115">大多数新式桌面和移动浏览器（包括 Chrome、Microsoft Edge、Firefox、Opera 和 Safari）都支持 CSP。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-115">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="bbaa8-116">建议将 CSP 用于 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-116">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="bbaa8-117">策略指令</span><span class="sxs-lookup"><span data-stu-id="bbaa8-117">Policy directives</span></span>

<span data-ttu-id="bbaa8-118">至少为 Blazor 应用指定以下指令和源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-118">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="bbaa8-119">按需添加其他指令和源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-119">Add additional directives and sources as needed.</span></span> <span data-ttu-id="bbaa8-120">本文的[应用策略](#apply-the-policy)部分中使用了以下指令，其中提供了 Blazor WebAssembly 和 Blazor Server 的示例安全策略：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-120">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="bbaa8-121">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri)：限制页面的 `<base>` 标记的 URL。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-121">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri): Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="bbaa8-122">指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-122">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="bbaa8-123">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content)：阻止加载混合 HTTP 和 HTTPS 内容。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-123">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content): Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="bbaa8-124">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src)：指示未被策略显式指定的源指令的回退。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-124">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src): Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="bbaa8-125">指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-125">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="bbaa8-126">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src)：指示图像的有效源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-126">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src): Indicates valid sources for images.</span></span>
  * <span data-ttu-id="bbaa8-127">指定 `data:`，以允许从 `data:` URL 加载图像。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-127">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="bbaa8-128">指定 `https:`，以允许从 HTTPS 终结点加载图像。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-128">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="bbaa8-129">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src)：指示 `<object>`、`<embed>` 和 `<applet>` 标记的有效源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-129">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src): Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="bbaa8-130">指定 `none` 以阻止所有 URL 源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-130">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="bbaa8-131">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src)：指示脚本的有效源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-131">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src): Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="bbaa8-132">指定用于启动脚本的 `https://stackpath.bootstrapcdn.com/` 主机源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-132">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="bbaa8-133">指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-133">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="bbaa8-134">在 Blazor WebAssembly 应用中：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-134">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="bbaa8-135">执行哈希以允许加载所需的脚本。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-135">Specify hashes to permit required scripts to load.</span></span>
    * <span data-ttu-id="bbaa8-136">指定 `unsafe-eval`，以使用 `eval()` 以及通过字符串创建代码的方法。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-136">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="bbaa8-137">在 Blazor Server应用中，执行哈希以允许加载所需的脚本。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-137">In a Blazor Server app, specify hashes to permit required scripts to load.</span></span>
* <span data-ttu-id="bbaa8-138">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src)：指示样式表的有效源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-138">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src): Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="bbaa8-139">指定用于启动样式表的 `https://stackpath.bootstrapcdn.com/` 主机源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-139">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="bbaa8-140">指定 `self`，以指示应用的来源（包括方案和端口号）是有效源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-140">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="bbaa8-141">指定 `unsafe-inline`，以允许使用内联样式。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-141">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="bbaa8-142">Blazor Server 应用中的 UI 需要内联声明，以便在初始请求后重新连接客户端和服务器。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-142">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="bbaa8-143">在将来的版本中，可能会删除内联样式，以便不再需要 `unsafe-inline`。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-143">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="bbaa8-144">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests)：指示应通过 HTTPS 安全地获取来自不安全 (HTTP) 源的内容 URL。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-144">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests): Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="bbaa8-145">除 Microsoft Internet Explorer 外的所有浏览器都支持上述指令。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-145">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="bbaa8-146">获取其他内联脚本的 SHA 哈希：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-146">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="bbaa8-147">应用[应用策略](#apply-the-policy)部分中所示的 CSP。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-147">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="bbaa8-148">在本地运行应用时访问浏览器的开发人员工具控制台。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-148">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="bbaa8-149">当存在 CSP 标头或 `meta` 标记时，浏览器将为遭到阻止的脚本计算和显示哈希。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-149">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="bbaa8-150">将浏览器提供的哈希复制到 `script-src` 源。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-150">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="bbaa8-151">在每个哈希两边使用单引号。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-151">Use single quotes around each hash.</span></span>

<span data-ttu-id="bbaa8-152">有关内容安全策略级别 2 浏览器的支持矩阵，请参阅[我可以使用：内容安全策略级别 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-152">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="bbaa8-153">应用策略</span><span class="sxs-lookup"><span data-stu-id="bbaa8-153">Apply the policy</span></span>

<span data-ttu-id="bbaa8-154">使用 `<meta>` 标记来应用策略：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-154">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="bbaa8-155">将 `http-equiv` 属性的值设置为 `Content-Security-Policy`。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-155">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="bbaa8-156">将指令置于 `content` 属性值中。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-156">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="bbaa8-157">使用分号 (`;`) 分隔指令。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-157">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="bbaa8-158">始终将 `meta` 标记置于 `<head>` 内容中。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-158">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="bbaa8-159">以下各部分介绍 Blazor WebAssembly 和 Blazor Server 的示例策略。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-159">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="bbaa8-160">对于 Blazor 的每个版本，本文都对这些示例进行了版本控制。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-160">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="bbaa8-161">要使用适用于你的版本的版本，请在此网页上使用“版本”下拉选择器选择文档版本。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-161">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### Blazor WebAssembly

<span data-ttu-id="bbaa8-162">在 `wwwroot/index.html` 主机页面的 `<head>` 内容中，应用[策略指令](#policy-directives)部分中描述的指令：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-162">In the `<head>` content of the `wwwroot/index.html` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```html
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self' 
                          'sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=' 
                          'unsafe-eval';
               style-src https://stackpath.bootstrapcdn.com/
                         'self'
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

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

::: moniker-end

<span data-ttu-id="bbaa8-163">添加应用所需的其他 `script-src` 和 `style-src` 哈希。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-163">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="bbaa8-164">在开发期间，使用联机工具或浏览器开发人员工具来计算哈希。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-164">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="bbaa8-165">例如，以下浏览器工具控制台错误为策略未涵盖的必需脚本报告哈希：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-165">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="bbaa8-166">拒绝执行内联脚本，因为它违反以下内容安全策略指令：“...”。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-166">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="bbaa8-167">若要启用内联执行，需要使用“unsafe-inline”关键字、哈希（“sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=”）或 nonce（“nonce-...”）。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-167">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="bbaa8-168">与错误关联的特定脚本显示在错误旁边的控制台中。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-168">The particular script associated with the error is displayed in the console next to the error.</span></span>

### Blazor Server

<span data-ttu-id="bbaa8-169">在 `Pages/_Host.cshtml` 主机页面的 `<head>` 内容中，应用[策略指令](#policy-directives)部分中描述的指令：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-169">In the `<head>` content of the `Pages/_Host.cshtml` host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

::: moniker range=">= aspnetcore-5.0"

```cshtml
<meta http-equiv="Content-Security-Policy" 
      content="base-uri 'self';
               block-all-mixed-content;
               default-src 'self';
               img-src data: https:;
               object-src 'none';
               script-src https://stackpath.bootstrapcdn.com/ 
                          'self';
               style-src https://stackpath.bootstrapcdn.com/
                         'self' 
                         'unsafe-inline';
               upgrade-insecure-requests;">
```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

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

::: moniker-end

<span data-ttu-id="bbaa8-170">添加应用所需的其他 `script-src` 和 `style-src` 哈希。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-170">Add additional `script-src` and `style-src` hashes as required by the app.</span></span> <span data-ttu-id="bbaa8-171">在开发期间，使用联机工具或浏览器开发人员工具来计算哈希。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-171">During development, use an online tool or browser developer tools to have the hashes calculated for you.</span></span> <span data-ttu-id="bbaa8-172">例如，以下浏览器工具控制台错误为策略未涵盖的必需脚本报告哈希：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-172">For example, the following browser tools console error reports the hash for a required script not covered by the policy:</span></span>

> <span data-ttu-id="bbaa8-173">拒绝执行内联脚本，因为它违反以下内容安全策略指令：“...”。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-173">Refused to execute inline script because it violates the following Content Security Policy directive: " ... ".</span></span> <span data-ttu-id="bbaa8-174">若要启用内联执行，需要使用“unsafe-inline”关键字、哈希（“sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=”）或 nonce（“nonce-...”）。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-174">Either the 'unsafe-inline' keyword, a hash ('sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA='), or a nonce ('nonce-...') is required to enable inline execution.</span></span>

<span data-ttu-id="bbaa8-175">与错误关联的特定脚本显示在错误旁边的控制台中。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-175">The particular script associated with the error is displayed in the console next to the error.</span></span>

## <a name="meta-tag-limitations"></a><span data-ttu-id="bbaa8-176">元标记限制</span><span class="sxs-lookup"><span data-stu-id="bbaa8-176">Meta tag limitations</span></span>

<span data-ttu-id="bbaa8-177">`<meta>` 标记策略不支持以下指令：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-177">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="bbaa8-178">frame-ancestors</span><span class="sxs-lookup"><span data-stu-id="bbaa8-178">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="bbaa8-179">report-to</span><span class="sxs-lookup"><span data-stu-id="bbaa8-179">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="bbaa8-180">report-uri</span><span class="sxs-lookup"><span data-stu-id="bbaa8-180">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="bbaa8-181">sandbox</span><span class="sxs-lookup"><span data-stu-id="bbaa8-181">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="bbaa8-182">要支持上述指令，请使用名为 `Content-Security-Policy` 的标头。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-182">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="bbaa8-183">指令字符串是标头的值。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-183">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="bbaa8-184">测试策略并接收违规报告</span><span class="sxs-lookup"><span data-stu-id="bbaa8-184">Test a policy and receive violation reports</span></span>

<span data-ttu-id="bbaa8-185">测试有助于确认在生成初始策略时不会无意中阻止第三方脚本。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-185">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="bbaa8-186">要在不强制实施策略指令的情况下在一段时间内测试策略，请将基于标头的策略的 `<meta>` 标记的 `http-equiv` 属性或标头名称设置为 `Content-Security-Policy-Report-Only`。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-186">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="bbaa8-187">将故障报告作为 JSON 文档发送到指定的 URL。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-187">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="bbaa8-188">有关详细信息，请参阅 [MDN Web 文档：Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-188">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="bbaa8-189">要在策略处于活动状态时报告违规，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-189">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="bbaa8-190">report-to</span><span class="sxs-lookup"><span data-stu-id="bbaa8-190">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="bbaa8-191">report-uri</span><span class="sxs-lookup"><span data-stu-id="bbaa8-191">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="bbaa8-192">虽然不再建议使用 `report-uri`，但在所有主要浏览器都支持 `report-to` 之前，都应使用这两个指令。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-192">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="bbaa8-193">不要只使用 `report-uri`，因为随时可能从浏览器中删除对 `report-uri` 的支持 。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-193">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="bbaa8-194">完全支持 `report-to` 时，请删除策略中对 `report-uri` 的支持。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-194">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="bbaa8-195">要跟踪 `report-to` 的使用情况，请参阅[我可以使用：report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-195">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="bbaa8-196">每次发布时测试和更新应用的策略。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-196">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="bbaa8-197">疑难解答</span><span class="sxs-lookup"><span data-stu-id="bbaa8-197">Troubleshoot</span></span>

* <span data-ttu-id="bbaa8-198">浏览器的开发人员工具控制台中出现错误。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-198">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="bbaa8-199">浏览器提供以下信息：</span><span class="sxs-lookup"><span data-stu-id="bbaa8-199">Browsers provide information about:</span></span>
  * <span data-ttu-id="bbaa8-200">不符合策略的元素。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-200">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="bbaa8-201">如何修改策略以允许遭到阻止的项目。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-201">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="bbaa8-202">仅当客户端的浏览器支持所有包含的指令时，策略才完全有效。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-202">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="bbaa8-203">有关当前浏览器支持矩阵，请参阅[我可以使用：Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy)。</span><span class="sxs-lookup"><span data-stu-id="bbaa8-203">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bbaa8-204">其他资源</span><span class="sxs-lookup"><span data-stu-id="bbaa8-204">Additional resources</span></span>

* [<span data-ttu-id="bbaa8-205">MDN Web 文档：Content-Security-Policy</span><span class="sxs-lookup"><span data-stu-id="bbaa8-205">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="bbaa8-206">内容安全策略级别 2</span><span class="sxs-lookup"><span data-stu-id="bbaa8-206">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="bbaa8-207">Google CSP Evaluator</span><span class="sxs-lookup"><span data-stu-id="bbaa8-207">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
