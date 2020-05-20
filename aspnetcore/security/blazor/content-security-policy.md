---
<span data-ttu-id="98ebc-101">标题： "对 ASP.NET Core 的作者强制实施内容安全策略 Blazor ：说明：" 了解如何结合使用内容安全策略（CSP）与 ASP.NET Core 的 Blazor 应用程序来帮助防范跨站点脚本（XSS）攻击。</span><span class="sxs-lookup"><span data-stu-id="98ebc-101">title: 'Enforce a Content Security Policy for ASP.NET Core Blazor' author: description: 'Learn how to use a Content Security Policy (CSP) with ASP.NET Core Blazor apps to help protect against Cross-Site Scripting (XSS) attacks.'</span></span>
<span data-ttu-id="98ebc-102">monikerRange： ms-chap： ms-chap： ms-chap：非 loc：</span><span class="sxs-lookup"><span data-stu-id="98ebc-102">monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="98ebc-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="98ebc-103">'Blazor'</span></span>
- <span data-ttu-id="98ebc-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="98ebc-104">'Identity'</span></span>
- <span data-ttu-id="98ebc-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="98ebc-105">'Let's Encrypt'</span></span>
- <span data-ttu-id="98ebc-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="98ebc-106">'Razor'</span></span>
- <span data-ttu-id="98ebc-107">" SignalR " uid：</span><span class="sxs-lookup"><span data-stu-id="98ebc-107">'SignalR' uid:</span></span> 

---
# <a name="enforce-a-content-security-policy-for-aspnet-core-blazor"></a><span data-ttu-id="98ebc-108">为 ASP.NET Core 强制实施内容安全策略Blazor</span><span class="sxs-lookup"><span data-stu-id="98ebc-108">Enforce a Content Security Policy for ASP.NET Core Blazor</span></span>

<span data-ttu-id="98ebc-109">作者： [Javier Calvarro 使用](https://github.com/javiercn)和[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="98ebc-109">By [Javier Calvarro Nelson](https://github.com/javiercn) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="98ebc-110">[跨站点脚本（XSS）](xref:security/cross-site-scripting)是一个安全漏洞，攻击者将一个或多个恶意客户端脚本放入应用程序的呈现内容中。</span><span class="sxs-lookup"><span data-stu-id="98ebc-110">[Cross-Site Scripting (XSS)](xref:security/cross-site-scripting) is a security vulnerability where an attacker places one or more malicious client-side scripts into an app's rendered content.</span></span> <span data-ttu-id="98ebc-111">内容安全策略（CSP）通过通知浏览器的有效情况来帮助防范 XSS 攻击：</span><span class="sxs-lookup"><span data-stu-id="98ebc-111">A Content Security Policy (CSP) helps protect against XSS attacks by informing the browser of valid:</span></span>

* <span data-ttu-id="98ebc-112">已加载内容的源，包括脚本、样式表和图像。</span><span class="sxs-lookup"><span data-stu-id="98ebc-112">Sources for loaded content, including scripts, stylesheets, and images.</span></span>
* <span data-ttu-id="98ebc-113">页执行的操作，指定窗体的允许的 URL 目标。</span><span class="sxs-lookup"><span data-stu-id="98ebc-113">Actions taken by a page, specifying permitted URL targets of forms.</span></span>
* <span data-ttu-id="98ebc-114">可以加载的插件。</span><span class="sxs-lookup"><span data-stu-id="98ebc-114">Plugins that can be loaded.</span></span>

<span data-ttu-id="98ebc-115">若要向应用程序应用 CSP，开发人员需要在一个或多*directives*个 `Content-Security-Policy` 标头或标记中指定若干 csp 内容安全指令 `<meta>` 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-115">To apply a CSP to an app, the developer specifies several CSP content security *directives* in one or more `Content-Security-Policy` headers or `<meta>` tags.</span></span>

<span data-ttu-id="98ebc-116">加载页面时，浏览器会对策略进行评估。</span><span class="sxs-lookup"><span data-stu-id="98ebc-116">Policies are evaluated by the browser while a page is loading.</span></span> <span data-ttu-id="98ebc-117">浏览器将检查页的源并确定它们是否满足内容安全指令的要求。</span><span class="sxs-lookup"><span data-stu-id="98ebc-117">The browser inspects the page's sources and determines if they meet the requirements of the content security directives.</span></span> <span data-ttu-id="98ebc-118">如果资源不满足策略指令，浏览器不会加载资源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-118">When policy directives aren't met for a resource, the browser doesn't load the resource.</span></span> <span data-ttu-id="98ebc-119">例如，请考虑不允许第三方脚本的策略。</span><span class="sxs-lookup"><span data-stu-id="98ebc-119">For example, consider a policy that doesn't allow third-party scripts.</span></span> <span data-ttu-id="98ebc-120">当页面包含 `<script>` 属性中包含第三方来源的标记时 `src` ，浏览器将阻止加载该脚本。</span><span class="sxs-lookup"><span data-stu-id="98ebc-120">When a page contains a `<script>` tag with a third-party origin in the `src` attribute, the browser prevents the script from loading.</span></span>

<span data-ttu-id="98ebc-121">大多数新式桌面和移动浏览器（包括 Chrome、Edge、Firefox、Opera 和 Safari）都支持 CSP。</span><span class="sxs-lookup"><span data-stu-id="98ebc-121">CSP is supported in most modern desktop and mobile browsers, including Chrome, Edge, Firefox, Opera, and Safari.</span></span> <span data-ttu-id="98ebc-122">建议将 CSP 用于 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="98ebc-122">CSP is recommended for Blazor apps.</span></span>

## <a name="policy-directives"></a><span data-ttu-id="98ebc-123">策略指令</span><span class="sxs-lookup"><span data-stu-id="98ebc-123">Policy directives</span></span>

<span data-ttu-id="98ebc-124">至少，为应用指定以下指令和源 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-124">Minimally, specify the following directives and sources for Blazor apps.</span></span> <span data-ttu-id="98ebc-125">根据需要添加其他指令和源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-125">Add additional directives and sources as needed.</span></span> <span data-ttu-id="98ebc-126">以下指令用于本文的[应用策略](#apply-the-policy)部分，其中 Blazor 提供了 WebAssembly 和服务器的示例安全策略 Blazor ：</span><span class="sxs-lookup"><span data-stu-id="98ebc-126">The following directives are used in the [Apply the policy](#apply-the-policy) section of this article, where example security policies for Blazor WebAssembly and Blazor Server are provided:</span></span>

* <span data-ttu-id="98ebc-127">[基-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash;限制页标记的 Url `<base>` 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-127">[base-uri](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/base-uri) &ndash; Restricts the URLs for a page's `<base>` tag.</span></span> <span data-ttu-id="98ebc-128">指定 `self` 以指示应用程序的源（包括方案和端口号）是有效的源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-128">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="98ebc-129">[块-全部混合内容](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash;阻止加载混合 HTTP 和 HTTPS 内容。</span><span class="sxs-lookup"><span data-stu-id="98ebc-129">[block-all-mixed-content](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/block-all-mixed-content) &ndash; Prevents loading mixed HTTP and HTTPS content.</span></span>
* <span data-ttu-id="98ebc-130">[默认值-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash;指示策略未显式指定的源指令的回退。</span><span class="sxs-lookup"><span data-stu-id="98ebc-130">[default-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/default-src) &ndash; Indicates a fallback for source directives that aren't explicitly specified by the policy.</span></span> <span data-ttu-id="98ebc-131">指定 `self` 以指示应用程序的源（包括方案和端口号）是有效的源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-131">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
* <span data-ttu-id="98ebc-132">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash;指示图像的有效源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-132">[img-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/img-src) &ndash; Indicates valid sources for images.</span></span>
  * <span data-ttu-id="98ebc-133">指定 `data:` 以允许从 url 加载图像 `data:` 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-133">Specify `data:` to permit loading images from `data:` URLs.</span></span>
  * <span data-ttu-id="98ebc-134">指定 `https:` 以允许从 HTTPS 终结点加载图像。</span><span class="sxs-lookup"><span data-stu-id="98ebc-134">Specify `https:` to permit loading images from HTTPS endpoints.</span></span>
* <span data-ttu-id="98ebc-135">[对象-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash;指示 `<object>` 、和标记的有效源 `<embed>` `<applet>` 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-135">[object-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/object-src) &ndash; Indicates valid sources for the `<object>`, `<embed>`, and `<applet>` tags.</span></span> <span data-ttu-id="98ebc-136">指定 `none` 以防止所有 URL 源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-136">Specify `none` to prevent all URL sources.</span></span>
* <span data-ttu-id="98ebc-137">[脚本-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash;指示脚本的有效源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-137">[script-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/script-src) &ndash; Indicates valid sources for scripts.</span></span>
  * <span data-ttu-id="98ebc-138">指定 `https://stackpath.bootstrapcdn.com/` 启动脚本的主机源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-138">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap scripts.</span></span>
  * <span data-ttu-id="98ebc-139">指定 `self` 以指示应用程序的源（包括方案和端口号）是有效的源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-139">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="98ebc-140">在 Blazor WebAssembly 应用中：</span><span class="sxs-lookup"><span data-stu-id="98ebc-140">In a Blazor WebAssembly app:</span></span>
    * <span data-ttu-id="98ebc-141">指定以下哈希，以允许加载所需的 Blazor WebAssembly 内联脚本：</span><span class="sxs-lookup"><span data-stu-id="98ebc-141">Specify the following hashes to permit the required Blazor WebAssembly inline scripts to load:</span></span>
      * `sha256-v8ZC9OgMhcnEQ/Me77/R9TlJfzOBqrMTW8e1KuqLaqc=`
      * `sha256-If//FtbPc03afjLezvWHnC3Nbu4fDM04IIzkPaf3pH0=`
      * `sha256-v8v3RKRPmN4odZ1CWM5gw80QKPCCWMcpNeOmimNL2AA=`
    * <span data-ttu-id="98ebc-142">指定 `unsafe-eval` 使用 `eval()` 和方法从字符串创建代码。</span><span class="sxs-lookup"><span data-stu-id="98ebc-142">Specify `unsafe-eval` to use `eval()` and methods for creating code from strings.</span></span>
  * <span data-ttu-id="98ebc-143">在 Blazor 服务器应用中，指定为 `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` 样式表执行回退检测的内联脚本的哈希。</span><span class="sxs-lookup"><span data-stu-id="98ebc-143">In a Blazor Server app, specify the `sha256-34WLX60Tw3aG6hylk0plKbZZFXCuepeQ6Hu7OqRf8PI=` hash for the inline script that performs fallback detection for stylesheets.</span></span>
* <span data-ttu-id="98ebc-144">[样式-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash;指示样式表的有效源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-144">[style-src](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/style-src) &ndash; Indicates valid sources for stylesheets.</span></span>
  * <span data-ttu-id="98ebc-145">指定 `https://stackpath.bootstrapcdn.com/` 用于启动样式表的主机源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-145">Specify the `https://stackpath.bootstrapcdn.com/` host source for Bootstrap stylesheets.</span></span>
  * <span data-ttu-id="98ebc-146">指定 `self` 以指示应用程序的源（包括方案和端口号）是有效的源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-146">Specify `self` to indicate that the app's origin, including the scheme and port number, is a valid source.</span></span>
  * <span data-ttu-id="98ebc-147">指定 `unsafe-inline` 允许使用内联样式。</span><span class="sxs-lookup"><span data-stu-id="98ebc-147">Specify `unsafe-inline` to allow the use of inline styles.</span></span> <span data-ttu-id="98ebc-148">服务器应用中的 UI 需要内联声明， Blazor 以便在初始请求后重新连接客户端和服务器。</span><span class="sxs-lookup"><span data-stu-id="98ebc-148">The inline declaration is required for the UI in Blazor Server apps for reconnecting the client and server after the initial request.</span></span> <span data-ttu-id="98ebc-149">在将来的版本中，可能会删除内联样式，这样 `unsafe-inline` 就不再需要。</span><span class="sxs-lookup"><span data-stu-id="98ebc-149">In a future release, inline styling might be removed so that `unsafe-inline` is no longer required.</span></span>
* <span data-ttu-id="98ebc-150">[升级-不安全-请求](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash;指示应通过 HTTPS 安全地获取来自不安全（HTTP）源的内容 Url。</span><span class="sxs-lookup"><span data-stu-id="98ebc-150">[upgrade-insecure-requests](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/upgrade-insecure-requests) &ndash; Indicates that content URLs from insecure (HTTP) sources should be acquired securely over HTTPS.</span></span>

<span data-ttu-id="98ebc-151">除 Microsoft Internet Explorer 外，所有浏览器都支持前面的指令。</span><span class="sxs-lookup"><span data-stu-id="98ebc-151">The preceding directives are supported by all browsers except Microsoft Internet Explorer.</span></span>

<span data-ttu-id="98ebc-152">获取更多内联脚本的 SHA 哈希：</span><span class="sxs-lookup"><span data-stu-id="98ebc-152">To obtain SHA hashes for additional inline scripts:</span></span>

* <span data-ttu-id="98ebc-153">应用 "[应用策略](#apply-the-policy)" 部分中所示的 CSP。</span><span class="sxs-lookup"><span data-stu-id="98ebc-153">Apply the CSP shown in the [Apply the policy](#apply-the-policy) section.</span></span>
* <span data-ttu-id="98ebc-154">在本地运行应用程序时，访问浏览器的 "开发人员工具" 控制台。</span><span class="sxs-lookup"><span data-stu-id="98ebc-154">Access the browser's developer tools console while running the app locally.</span></span> <span data-ttu-id="98ebc-155">当存在 CSP 标头或标记时，浏览器将为被阻止的脚本计算并显示哈希 `meta` 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-155">The browser calculates and displays hashes for blocked scripts when a CSP header or `meta` tag is present.</span></span>
* <span data-ttu-id="98ebc-156">将浏览器提供的哈希复制到 `script-src` 源。</span><span class="sxs-lookup"><span data-stu-id="98ebc-156">Copy the hashes provided by the browser to the `script-src` sources.</span></span> <span data-ttu-id="98ebc-157">在每个哈希两边使用单引号。</span><span class="sxs-lookup"><span data-stu-id="98ebc-157">Use single quotes around each hash.</span></span>

<span data-ttu-id="98ebc-158">有关内容安全策略级别2浏览器支持矩阵，请参阅[可以使用：内容安全策略级别 2](https://www.caniuse.com/#feat=contentsecuritypolicy2)。</span><span class="sxs-lookup"><span data-stu-id="98ebc-158">For a Content Security Policy Level 2 browser support matrix, see [Can I use: Content Security Policy Level 2](https://www.caniuse.com/#feat=contentsecuritypolicy2).</span></span>

## <a name="apply-the-policy"></a><span data-ttu-id="98ebc-159">应用策略</span><span class="sxs-lookup"><span data-stu-id="98ebc-159">Apply the policy</span></span>

<span data-ttu-id="98ebc-160">使用 `<meta>` 标记来应用策略：</span><span class="sxs-lookup"><span data-stu-id="98ebc-160">Use a `<meta>` tag to apply the policy:</span></span>

* <span data-ttu-id="98ebc-161">将属性的值设置 `http-equiv` 为 `Content-Security-Policy` 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-161">Set the value of the `http-equiv` attribute to `Content-Security-Policy`.</span></span>
* <span data-ttu-id="98ebc-162">将指令放在 `content` 属性值中。</span><span class="sxs-lookup"><span data-stu-id="98ebc-162">Place the directives in the `content` attribute value.</span></span> <span data-ttu-id="98ebc-163">用分号（）分隔指令 `;` 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-163">Separate directives with a semicolon (`;`).</span></span>
* <span data-ttu-id="98ebc-164">始终将 `meta` 标记放在 `<head>` 内容中。</span><span class="sxs-lookup"><span data-stu-id="98ebc-164">Always place the `meta` tag in the `<head>` content.</span></span>

<span data-ttu-id="98ebc-165">以下部分显示了 Blazor WebAssembly 和服务器的示例策略 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-165">The following sections show example policies for Blazor WebAssembly and Blazor Server.</span></span> <span data-ttu-id="98ebc-166">对于每个版本，本文都对这些示例进行了版本控制 Blazor 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-166">These examples are versioned with this article for each release of Blazor.</span></span> <span data-ttu-id="98ebc-167">若要使用适合你版本的版本，请在此网页上选择包含**版本**下拉选择器的文档版本。</span><span class="sxs-lookup"><span data-stu-id="98ebc-167">To use a version appropriate for your release, select the document version with the **Version** drop down selector on this webpage.</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="98ebc-168"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="98ebc-168"> WebAssembly</span></span>

<span data-ttu-id="98ebc-169">在 " `<head>` *wwwroot/index.html*主机" 页的 "内容" 中，应用[策略指令](#policy-directives)部分中描述的指令：</span><span class="sxs-lookup"><span data-stu-id="98ebc-169">In the `<head>` content of the *wwwroot/index.html* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

### <a name="blazor-server"></a>Blazor<span data-ttu-id="98ebc-170"> 服务器</span><span class="sxs-lookup"><span data-stu-id="98ebc-170"> Server</span></span>

<span data-ttu-id="98ebc-171">在 " `<head>` *Pages/_Host. cshtml*主机" 页的 "内容" 中，应用[策略指令](#policy-directives)部分中描述的指令：</span><span class="sxs-lookup"><span data-stu-id="98ebc-171">In the `<head>` content of the *Pages/_Host.cshtml* host page, apply the directives described in the [Policy directives](#policy-directives) section:</span></span>

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

## <a name="meta-tag-limitations"></a><span data-ttu-id="98ebc-172">元标记限制</span><span class="sxs-lookup"><span data-stu-id="98ebc-172">Meta tag limitations</span></span>

<span data-ttu-id="98ebc-173">`<meta>`标记策略不支持以下指令：</span><span class="sxs-lookup"><span data-stu-id="98ebc-173">A `<meta>` tag policy doesn't support the following directives:</span></span>

* [<span data-ttu-id="98ebc-174">框架-上级</span><span class="sxs-lookup"><span data-stu-id="98ebc-174">frame-ancestors</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/frame-ancestors)
* [<span data-ttu-id="98ebc-175">报表到</span><span class="sxs-lookup"><span data-stu-id="98ebc-175">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="98ebc-176">报表-uri</span><span class="sxs-lookup"><span data-stu-id="98ebc-176">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)
* [<span data-ttu-id="98ebc-177">处理</span><span class="sxs-lookup"><span data-stu-id="98ebc-177">sandbox</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/sandbox)

<span data-ttu-id="98ebc-178">若要支持前面的指令，请使用名为的标头 `Content-Security-Policy` 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-178">To support the preceding directives, use a header named `Content-Security-Policy`.</span></span> <span data-ttu-id="98ebc-179">指令字符串是标头的值。</span><span class="sxs-lookup"><span data-stu-id="98ebc-179">The directive string is the header's value.</span></span>

## <a name="test-a-policy-and-receive-violation-reports"></a><span data-ttu-id="98ebc-180">测试策略并接收冲突报告</span><span class="sxs-lookup"><span data-stu-id="98ebc-180">Test a policy and receive violation reports</span></span>

<span data-ttu-id="98ebc-181">测试有助于确认在生成初始策略时不会无意中阻止第三方脚本。</span><span class="sxs-lookup"><span data-stu-id="98ebc-181">Testing helps confirm that third-party scripts aren't inadvertently blocked when building an initial policy.</span></span>

<span data-ttu-id="98ebc-182">若要在不强制实施策略指令的情况下测试策略，请将 `<meta>` `http-equiv` 基于标头的策略的标记特性或标头名称设置为 `Content-Security-Policy-Report-Only` 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-182">To test a policy over a period of time without enforcing the policy directives, set the `<meta>` tag's `http-equiv` attribute or header name of a header-based policy to `Content-Security-Policy-Report-Only`.</span></span> <span data-ttu-id="98ebc-183">故障报告作为 JSON 文档发送到指定的 URL。</span><span class="sxs-lookup"><span data-stu-id="98ebc-183">Failure reports are sent as JSON documents to a specified URL.</span></span> <span data-ttu-id="98ebc-184">有关详细信息，请参阅[MDN web 文档：仅限内容安全策略](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only)。</span><span class="sxs-lookup"><span data-stu-id="98ebc-184">For more information, see [MDN web docs: Content-Security-Policy-Report-Only](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy-Report-Only).</span></span>

<span data-ttu-id="98ebc-185">若要在策略处于活动状态时报告冲突，请参阅以下文章：</span><span class="sxs-lookup"><span data-stu-id="98ebc-185">For reporting on violations while a policy is active, see the following articles:</span></span>

* [<span data-ttu-id="98ebc-186">报表到</span><span class="sxs-lookup"><span data-stu-id="98ebc-186">report-to</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-to)
* [<span data-ttu-id="98ebc-187">报表-uri</span><span class="sxs-lookup"><span data-stu-id="98ebc-187">report-uri</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy/report-uri)

<span data-ttu-id="98ebc-188">尽管不再 `report-uri` 推荐使用，但 `report-to` 在所有主要浏览器都支持之前，应使用两个指令。</span><span class="sxs-lookup"><span data-stu-id="98ebc-188">Although `report-uri` is no longer recommended for use, both directives should be used until `report-to` is supported by all of the major browsers.</span></span> <span data-ttu-id="98ebc-189">请勿独占使用 `report-uri` ，因为对的支持可随时 `report-uri` 从*at any time*浏览器中删除。</span><span class="sxs-lookup"><span data-stu-id="98ebc-189">Don't exclusively use `report-uri` because support for `report-uri` is subject to being dropped *at any time* from browsers.</span></span> <span data-ttu-id="98ebc-190">完全支持时，删除对策略的支持 `report-uri` `report-to` 。</span><span class="sxs-lookup"><span data-stu-id="98ebc-190">Remove support for `report-uri` in your policies when `report-to` is fully supported.</span></span> <span data-ttu-id="98ebc-191">若要跟踪的采用 `report-to` ，请参阅[可以使用：报告到](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to)。</span><span class="sxs-lookup"><span data-stu-id="98ebc-191">To track adoption of `report-to`, see [Can I use: report-to](https://caniuse.com/#feat=mdn-http_headers_csp_content-security-policy_report-to).</span></span>

<span data-ttu-id="98ebc-192">每次发布时测试和更新应用的策略。</span><span class="sxs-lookup"><span data-stu-id="98ebc-192">Test and update an app's policy every release.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="98ebc-193">故障排除</span><span class="sxs-lookup"><span data-stu-id="98ebc-193">Troubleshoot</span></span>

* <span data-ttu-id="98ebc-194">浏览器的 "开发人员工具" 控制台中会出现错误。</span><span class="sxs-lookup"><span data-stu-id="98ebc-194">Errors appear in the browser's developer tools console.</span></span> <span data-ttu-id="98ebc-195">浏览器提供以下信息：</span><span class="sxs-lookup"><span data-stu-id="98ebc-195">Browsers provide information about:</span></span>
  * <span data-ttu-id="98ebc-196">不符合策略的元素。</span><span class="sxs-lookup"><span data-stu-id="98ebc-196">Elements that don't comply with the policy.</span></span>
  * <span data-ttu-id="98ebc-197">如何修改策略以允许阻止的项目。</span><span class="sxs-lookup"><span data-stu-id="98ebc-197">How to modify the policy to allow for a blocked item.</span></span>
* <span data-ttu-id="98ebc-198">仅当客户端的浏览器支持所有包含的指令时，策略才会完全有效。</span><span class="sxs-lookup"><span data-stu-id="98ebc-198">A policy is only completely effective when the client's browser supports all of the included directives.</span></span> <span data-ttu-id="98ebc-199">有关当前的浏览器支持矩阵，请参阅[可以使用：内容安全策略](https://caniuse.com/#search=Content-Security-Policy)。</span><span class="sxs-lookup"><span data-stu-id="98ebc-199">For a current browser support matrix, see [Can I use: Content-Security-Policy](https://caniuse.com/#search=Content-Security-Policy).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="98ebc-200">其他资源</span><span class="sxs-lookup"><span data-stu-id="98ebc-200">Additional resources</span></span>

* [<span data-ttu-id="98ebc-201">MDN web 文档：内容安全策略</span><span class="sxs-lookup"><span data-stu-id="98ebc-201">MDN web docs: Content-Security-Policy</span></span>](https://developer.mozilla.org/docs/Web/HTTP/Headers/Content-Security-Policy)
* [<span data-ttu-id="98ebc-202">内容安全策略级别2</span><span class="sxs-lookup"><span data-stu-id="98ebc-202">Content Security Policy Level 2</span></span>](https://www.w3.org/TR/CSP2/)
* [<span data-ttu-id="98ebc-203">Google CSP 计算器</span><span class="sxs-lookup"><span data-stu-id="98ebc-203">Google CSP Evaluator</span></span>](https://csp-evaluator.withgoogle.com/)
