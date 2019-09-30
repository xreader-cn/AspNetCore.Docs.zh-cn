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
# <a name="script-tag-helper-in-aspnet-core"></a>ASP.NET Core 中的脚本标记帮助程序

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

[标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)用于生成指向主要或回退脚本文件的链接。 通常主脚本文件位于[内容分发网络](/office365/enterprise/content-delivery-networks#what-exactly-is-a-cdn) (CDN)。

[!INCLUDE[](~/includes/cdn.md)]

可以使用脚本标记帮助程序指定脚本文件的 CDN 以及回退文件（CDN 不可用时）。 脚本标记帮助程序借助本地宿主的可靠性提供 CDN 性能优势。

以下 Razor 标记显示使用 ASP.NET Core Web 应用模板创建的布局文件的 `script` 元素：

[!code-html[](link-tag-helper/sample/_Layout.cshtml?name=snippet2)]

以下内容类似于上述代码呈现的 HTML（非开发环境）：

[!code-csharp[](link-tag-helper/sample/HtmlPage2.html)]

在上述代码中，脚本标记帮助程序生成了第二个脚本 (`<script>  (window.jQuery || document.write(`) 元素，该元素测试 `window.jQuery`。 如果找不到 `window.jQuery`，`document.write(` 将运行并创建脚本 

## <a name="commonly-used-script-tag-helper-attributes"></a>常用的脚本标记帮助程序属性

若要了解所有脚本标记帮助程序属性和方法，请参阅[标记帮助程序](xref:Microsoft.AspNetCore.Mvc.TagHelpers.ScriptTagHelper)。

### <a name="href"></a>href

链接的资源的首选地址。 在任何情况下，均会将此地址传递到生成的 HTML。

### <a name="asp-fallback-href"></a>asp-fallback-href

主 URL 失效后要回退到的 CSS 样式表的 URL。

### <a name="asp-fallback-test-class"></a>asp-fallback-test-class

样式表中定义的用于回退测试的类名称。 有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestClass>。

### <a name="asp-fallback-test-property"></a>asp-fallback-test-property

用于回退测试的 CSS 属性名称。 有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestProperty>。

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

用于回退测试的 CSS 属性值。 有关详细信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>。

### <a name="asp-fallback-test-value"></a>asp-fallback-test-value

用于回退测试的 CSS 属性值。 有关详细信息，请参阅<xref:Microsoft.AspNetCore.Mvc.TagHelpers.LinkTagHelper.FallbackTestValue>。

## <a name="additional-resources"></a>其他资源

* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/controllers/areas>
* <xref:razor-pages/index>
* <xref:mvc/compatibility-version>
