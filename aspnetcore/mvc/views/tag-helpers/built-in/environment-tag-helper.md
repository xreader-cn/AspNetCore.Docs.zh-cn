---
title: ASP.NET Core 中的环境标记帮助程序
author: pkellner
description: 定义的 ASP.NET Core 环境标记帮助程序（包括所有属性）
ms.author: riande
ms.custom: mvc
ms.date: 10/10/2018
uid: mvc/views/tag-helpers/builtin-th/environment-tag-helper
ms.openlocfilehash: 379f58ed37329f047d53adf1dcfdfd2ad6a6ca4e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64887672"
---
# <a name="environment-tag-helper-in-aspnet-core"></a>ASP.NET Core 中的环境标记帮助程序

作者：[Peter Kellner](http://peterkellner.net)、[Hisham Bin Ateya](https://twitter.com/hishambinateya) 和 [Luke Latham](https://github.com/guardrex)

环境标记帮助程序根据当前[宿主环境](xref:fundamentals/environments)，有条件地呈现其包含的内容。 环境标记帮助程序的单个属性 `names` 是以逗号分隔的环境名称列表。 任何提供的环境名称与当前环境匹配时，都会呈现包含的内容。

有关标记帮助程序的概述，请参阅 <xref:mvc/views/tag-helpers/intro>。

## <a name="environment-tag-helper-attributes"></a>环境标记帮助程序属性

### <a name="names"></a>名称

`names` 采用单个宿主环境名称或以逗号分隔的宿主环境名称列表，用于触发已包含内容的呈现。

将环境值与 [ IHostingEnvironment.EnvironmentName ](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*) 返回的当前值进行比较。 比较不区分大小写。

下面的示例使用图像标记帮助程序。 如果宿主环境是暂存或生产，则呈现内容：

```cshtml
<environment names="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

::: moniker range=">= aspnetcore-2.0"

## <a name="include-and-exclude-attributes"></a>include 和 exclude 属性

`include` & `exclude` 属性基于已包括或已排除的宿主环境名称，控制已包含内容的呈现。

### <a name="include"></a>include

`include` 属性表现出与 `names` 属性相似的行为。 `include` 属性值中列出的环境必须与应用程序的托管环境 ([IHostingEnvironment.EnvironmentName](xref:Microsoft.AspNetCore.Hosting.IHostingEnvironment.EnvironmentName*)) 匹配才能呈现 `<environment>` 标记的内容。

```cshtml
<environment include="Staging,Production">
    <strong>HostingEnvironment.EnvironmentName is Staging or Production</strong>
</environment>
```

### <a name="exclude"></a>exclude

与 `include` 属性相反，当托管环境与 `exclude` 属性值中列出的环境不匹配时，将呈现 `<environment>` 标记的内容。

```cshtml
<environment exclude="Development">
    <strong>HostingEnvironment.EnvironmentName is not Development</strong>
</environment>
```

::: moniker-end

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/environments>
