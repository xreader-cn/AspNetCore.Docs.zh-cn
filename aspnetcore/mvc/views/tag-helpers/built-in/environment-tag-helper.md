---
title: ASP.NET Core 中的环境标记帮助程序
author: pkellner
description: 定义的 ASP.NET Core 环境标记帮助程序（包括所有属性）
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
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
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: d63364b0c052ba7f9e745e1ad829b8d1ca9122d2
ms.sourcegitcommit: 063a06b644d3ade3c15ce00e72a758ec1187dd06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/16/2021
ms.locfileid: "98253119"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>ASP.NET Core 中的环境标记帮助程序

作者：[Peter Kellner](https://peterkellner.net) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)

环境标记帮助程序根据当前 [宿主环境](xref:fundamentals/environments)有条件地呈现其包含的内容。 环境标记帮助程序的单个属性 `names` 是以逗号分隔的环境名称列表。 任何提供的环境名称与当前环境匹配时，都会呈现包含的内容。

有关标记帮助程序的概述，请参阅 <xref:mvc/views/tag-helpers/intro>。

## <a name="environment-tag-helper-attributes"></a>环境标记帮助程序属性

### <a name="names"></a>姓名

`names` 采用单个宿主环境名称或以逗号分隔的宿主环境名称列表，用于触发已包含内容的呈现。

将环境值与 [IWebHostEnvironment](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)返回的当前值进行比较。 比较不区分大小写。

下面的示例使用图像标记帮助程序。 如果宿主环境是暂存或生产，则呈现内容：

```cshtml
<environment names="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>include 和 exclude 属性

`include`& `exclude` 属性控件基于包含或排除的宿主环境名称呈现包含的内容。

### <a name="include"></a>include

`include` 属性表现出与 `names` 属性相似的行为。 属性值中列出的环境 `include` 必须与应用的宿主环境 ([EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) ，才能呈现标记的内容 `<environment>` 。

```cshtml
<environment include="Staging,Production">
    <strong>IWebHostEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>排除

与 `include` 属性相反，当托管环境与 `exclude` 属性值中列出的环境不匹配时，将呈现 `<environment>` 标记的内容。

```cshtml
<environment exclude="Development">
    <strong>IWebHostEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/environments>
