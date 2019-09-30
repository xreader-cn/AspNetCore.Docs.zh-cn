---
title: ASP.NET Core 中的脚本标记帮助程序
author: rick-anderson
ms.author: riande
description: 了解 ASP.NET Core 脚本标记帮助程序属性以及每个属性在扩展 HTML 脚本标记的行为中所起的作用。
ms.custom: mvc
ms.date: 12/18/2018
uid: mvc/views/tag-helpers/builtin-th/script-tag-helper
ms.openlocfilehash: 5f2fb8a45048804afa8aff2989cd53489e45a33b
ms.sourcegitcommit: fae6f0e253f9d62d8f39de5884d2ba2b4b2a6050
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256464"
---
# <a name="script-tag-helper-in-aspnet-core"></a><span data-ttu-id="2c8cd-103">ASP.NET Core 中的脚本标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="2c8cd-103">Script Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="2c8cd-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2c8cd-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="2c8cd-105">[标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)用于生成指向主要或回退脚本文件的链接。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-105">The [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) generates a link to a primary or fall back script file.</span></span> <span data-ttu-id="2c8cd-106">通常主脚本文件位于[内容分发网络](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN)。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-106">Typically the primary script file is on a [Content Delivery Network](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN).</span></span>

[!INCLUDE[](~/includes/cdn.md)]

<span data-ttu-id="2c8cd-107">可以使用脚本标记帮助程序指定脚本文件的 CDN 以及回退文件（CDN 不可用时）。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-107">The Script Tag Helper allows you to specify a CDN for the script file and a fallback when the CDN is not available.</span></span> <span data-ttu-id="2c8cd-108">脚本标记帮助程序借助本地宿主的可靠性提供 CDN 性能优势。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-108">The Script Tag Helper provides the performance advantage of a CDN with the robustness of local hosting.</span></span>

<span data-ttu-id="2c8cd-109">以下 Razor 标记显示使用 ASP.NET Core Web 应用模板创建的布局文件的 `script` 元素：</span><span class="sxs-lookup"><span data-stu-id="2c8cd-109">The following Razor markup shows the `script` element of a layout file created with the ASP.NET Core web app template:</span></span>

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet2)]

<span data-ttu-id="2c8cd-110">以下内容类似于上述代码呈现的 HTML（非开发环境）：</span><span class="sxs-lookup"><span data-stu-id="2c8cd-110">The following is similar to the rendered HTML from the preceding code (in a non-Development environment):</span></span>

[!code-csharp[](link-tag-helper/sample/HtmlPage2.html)]

<span data-ttu-id="2c8cd-111">在上述代码中，脚本标记帮助程序生成了第二个脚本 (`<script>  (window.jQuery || document.write(`) 元素，该元素测试 `window.jQuery`。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-111">In the preceding code, the Script Tag Helper generated the second script ( `<script>  (window.jQuery || document.write(`) element, which tests for `window.jQuery`.</span></span> <span data-ttu-id="2c8cd-112">如果找不到 `window.jQuery`，`document.write(` 将运行并创建脚本</span><span class="sxs-lookup"><span data-stu-id="2c8cd-112">If `window.jQuery` is not found, `document.write(` runs and creates a script</span></span> 

## <a name="commonly-used-script-tag-helper-attributes"></a><span data-ttu-id="2c8cd-113">常用的脚本标记帮助程序属性</span><span class="sxs-lookup"><span data-stu-id="2c8cd-113">Commonly used Script Tag Helper attributes</span></span>

<span data-ttu-id="2c8cd-114">若要了解所有脚本标记帮助程序属性和方法，请参阅[标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-114">See [Script Tag Helper](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper) for all the Script Tag Helper attributes, properties, and methods.</span></span>

### <a name="href"></a><span data-ttu-id="2c8cd-115">href</span><span class="sxs-lookup"><span data-stu-id="2c8cd-115">href</span></span>

<span data-ttu-id="2c8cd-116">链接的资源的首选地址。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-116">Preferred address of the linked resource.</span></span> <span data-ttu-id="2c8cd-117">在任何情况下，均会将此地址传递到生成的 HTML。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-117">The address is passed thought to the generated HTML in all cases.</span></span>

### <a name="asp-fallback-href"></a><span data-ttu-id="2c8cd-118">asp-fallback-href</span><span class="sxs-lookup"><span data-stu-id="2c8cd-118">asp-fallback-href</span></span>

<span data-ttu-id="2c8cd-119">主 URL 失效后要回退到的 CSS 样式表的 URL。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-119">The URL of a CSS stylesheet to fallback to in the case the primary URL fails.</span></span>

### <a name="asp-fallback-test-class"></a><span data-ttu-id="2c8cd-120">asp-fallback-test-class</span><span class="sxs-lookup"><span data-stu-id="2c8cd-120">asp-fallback-test-class</span></span>

<span data-ttu-id="2c8cd-121">样式表中定义的用于回退测试的类名称。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-121">The class name defined in the stylesheet to use for the fallback test.</span></span> <span data-ttu-id="2c8cd-122">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-122">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>.</span></span>

### <a name="asp-fallback-test-property"></a><span data-ttu-id="2c8cd-123">asp-fallback-test-property</span><span class="sxs-lookup"><span data-stu-id="2c8cd-123">asp-fallback-test-property</span></span>

<span data-ttu-id="2c8cd-124">用于回退测试的 CSS 属性名称。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-124">The CSS property name to use for the fallback test.</span></span> <span data-ttu-id="2c8cd-125">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-125">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="2c8cd-126">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="2c8cd-126">asp-fallback-test-value</span></span>

<span data-ttu-id="2c8cd-127">用于回退测试的 CSS 属性值。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-127">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="2c8cd-128">有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-128">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>.</span></span>

### <a name="asp-fallback-test-value"></a><span data-ttu-id="2c8cd-129">asp-fallback-test-value</span><span class="sxs-lookup"><span data-stu-id="2c8cd-129">asp-fallback-test-value</span></span>

<span data-ttu-id="2c8cd-130">用于回退测试的 CSS 属性值。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-130">The CSS property value to use for the fallback test.</span></span> <span data-ttu-id="2c8cd-131">有关详细信息，请参阅<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>。</span><span class="sxs-lookup"><span data-stu-id="2c8cd-131">For more information, see <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue></span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c8cd-132">其他资源</span><span class="sxs-lookup"><span data-stu-id="2c8cd-132">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
