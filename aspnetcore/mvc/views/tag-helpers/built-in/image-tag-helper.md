---
title: ASP.NET Core 中的图像标记帮助程序
author: pkellner
description: 演示如何使用图像标记帮助程序。
ms.author: riande
ms.custom: mvc
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/image-tag-helper
ms.openlocfilehash: e1e3566f09c44d70650fa1f21e1921e76e46d384
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777405"
---
# <a name="image-tag-helper-in-aspnet-core"></a><span data-ttu-id="30181-103">ASP.NET Core 中的图像标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="30181-103">Image Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="30181-104">作者：[Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="30181-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="30181-105">图像标记帮助程序增强了 `<img>` 标记，为静态图像文件提供缓存破坏行为。</span><span class="sxs-lookup"><span data-stu-id="30181-105">The Image Tag Helper enhances the `<img>` tag to provide cache-busting behavior for static image files.</span></span>

<span data-ttu-id="30181-106">缓存破坏字符串是一个唯一值，表示附加到资产 URL 的静态图像文件的哈希值。</span><span class="sxs-lookup"><span data-stu-id="30181-106">A cache-busting string is a unique value representing the hash of the static image file appended to the asset's URL.</span></span> <span data-ttu-id="30181-107">唯一字符串会提示客户端（和某些代理）从主机 Web 服务器重新加载图像，而不是从客户端的缓存重新加载。</span><span class="sxs-lookup"><span data-stu-id="30181-107">The unique string prompts clients (and some proxies) to reload the image from the host web server and not from the client's cache.</span></span>

<span data-ttu-id="30181-108">如果图像源 (`src`) 是主机 web 服务器上的静态文件：</span><span class="sxs-lookup"><span data-stu-id="30181-108">If the image source (`src`) is a static file on the host web server:</span></span>

* <span data-ttu-id="30181-109">将唯一缓存破坏字符串作为图像源的查询参数进行追加。</span><span class="sxs-lookup"><span data-stu-id="30181-109">A unique cache-busting string is appended as a query parameter to the image source.</span></span>
* <span data-ttu-id="30181-110">如果主机 Web 服务器上的文件发生更改，将生成包含已更新请求参数的唯一请求 URL。</span><span class="sxs-lookup"><span data-stu-id="30181-110">If the file on the host web server changes, a unique request URL is generated that includes the updated request parameter.</span></span>

<span data-ttu-id="30181-111">有关标签帮助程序的概述，请参阅 <xref:mvc/views/tag-helpers/intro>。</span><span class="sxs-lookup"><span data-stu-id="30181-111">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

## <a name="image-tag-helper-attributes"></a><span data-ttu-id="30181-112">图像标记帮助程序属性</span><span class="sxs-lookup"><span data-stu-id="30181-112">Image Tag Helper Attributes</span></span>

### <a name="src"></a><span data-ttu-id="30181-113">src</span><span class="sxs-lookup"><span data-stu-id="30181-113">src</span></span>

<span data-ttu-id="30181-114">若要激活图像标记帮助程序，`<img>` 元素需要有 `src` 属性。</span><span class="sxs-lookup"><span data-stu-id="30181-114">To activate the Image Tag Helper, the `src` attribute is required on the `<img>` element.</span></span>

<span data-ttu-id="30181-115">图像源 (`src`) 必须指向服务器上的物理静态文件。</span><span class="sxs-lookup"><span data-stu-id="30181-115">The image source (`src`) must point to a physical static file on the server.</span></span> <span data-ttu-id="30181-116">如果 `src` 是一个远程 URI，则不会生成缓存破坏查询字符串参数。</span><span class="sxs-lookup"><span data-stu-id="30181-116">If the `src` is a remote URI, the cache-busting query string parameter isn't generated.</span></span>

### <a name="asp-append-version"></a><span data-ttu-id="30181-117">asp-append-version</span><span class="sxs-lookup"><span data-stu-id="30181-117">asp-append-version</span></span>

<span data-ttu-id="30181-118">如果使用 `true` 值和 `src` 属性指定 `asp-append-version`，则会调用图像标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="30181-118">When `asp-append-version` is specified with a `true` value along with a `src` attribute, the Image Tag Helper is invoked.</span></span>

<span data-ttu-id="30181-119">下面的示例使用图像标记帮助程序：</span><span class="sxs-lookup"><span data-stu-id="30181-119">The following example uses an Image Tag Helper:</span></span>

```cshtml
<img src="~/images/asplogo.png" asp-append-version="true">
```

<span data-ttu-id="30181-120">如果目录 /wwwroot/images/\*\* 中存在静态文件，则生成的 html 与下面类似（哈希有所不同）：</span><span class="sxs-lookup"><span data-stu-id="30181-120">If the static file exists in the directory */wwwroot/images/*, the generated HTML is similar to the following (the hash will be different):</span></span>

```html
<img src="/images/asplogo.png?v=Kl_dqr9NVtnMdsM2MUg4qthUnWZm5T1fCEimBPWDNgM">
```

<span data-ttu-id="30181-121">分配给参数 `v` 的值是磁盘上的 asplogo.png 文件的哈希值。\*\*</span><span class="sxs-lookup"><span data-stu-id="30181-121">The value assigned to the parameter `v` is the hash value of the *asplogo.png* file on disk.</span></span> <span data-ttu-id="30181-122">如果 Web 服务器无法获取对静态文件的读取访问权限，则不会向呈现在标记中的 `src` 属性添加 `v` 参数。</span><span class="sxs-lookup"><span data-stu-id="30181-122">If the web server is unable to obtain read access to the static file, no `v` parameter is added to the `src` attribute in the rendered markup.</span></span>

## <a name="hash-caching-behavior"></a><span data-ttu-id="30181-123">哈希缓存行为</span><span class="sxs-lookup"><span data-stu-id="30181-123">Hash caching behavior</span></span>

<span data-ttu-id="30181-124">图像标记帮助程序使用本地 Web 服务器上的缓存提供程序来存储给定文件的已计算 `Sha512` 哈希。</span><span class="sxs-lookup"><span data-stu-id="30181-124">The Image Tag Helper uses the cache provider on the local web server to store the calculated `Sha512` hash of a given file.</span></span> <span data-ttu-id="30181-125">如果多次请求文件，则不重新计算哈希值。</span><span class="sxs-lookup"><span data-stu-id="30181-125">If the file is requested multiple times, the hash isn't recalculated.</span></span> <span data-ttu-id="30181-126">当计算该文件的 `Sha512` 哈希时，附加到该文件的文件观察程序会让 Cache 失效。</span><span class="sxs-lookup"><span data-stu-id="30181-126">The cache is invalidated by a file watcher that's attached to the file when the file's `Sha512` hash is calculated.</span></span> <span data-ttu-id="30181-127">当磁盘上的的文件发生更改时，将会计算和缓存新的哈希。</span><span class="sxs-lookup"><span data-stu-id="30181-127">When the file changes on disk, a new hash is calculated and cached.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="30181-128">其他资源</span><span class="sxs-lookup"><span data-stu-id="30181-128">Additional resources</span></span>

* <xref:performance/caching/memory>
