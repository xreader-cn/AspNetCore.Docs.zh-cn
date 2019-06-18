---
title: ASP.NET Core 中的标记帮助程序组件
author: scottaddie
description: 了解标记帮助程序组件的定义及其在 ASP.NET Core 中的用法。
monikerRange: '>= aspnetcore-2.0'
ms.author: scaddie
ms.date: 06/12/2019
uid: mvc/views/tag-helpers/th-components
ms.openlocfilehash: b5b3abea6492cfaa7d6acd0e54073a8db12eb2a5
ms.sourcegitcommit: 335a88c1b6e7f0caa8a3a27db57c56664d676d34
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2019
ms.locfileid: "67034761"
---
# <a name="tag-helper-components-in-aspnet-core"></a>ASP.NET Core 中的标记帮助程序组件

作者：[Scott Addie](https://twitter.com/Scott_Addie) 和 [Fiyaz Bin Hasan](https://github.com/fiyazbinhasan)

标记帮助程序组件是可用于有条件地修改或添加服务器端代码中的 HTML 元素的标记帮助程序。 ASP.NET Core 2.0 或更高版本中提供了此功能。

ASP.NET Core 包括两个内置标记帮助程序组件：`head` 和 `body`。 它们位于 <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers> 命名空间中，可用于 MVC 和 Razor Pages。 标记帮助程序组件不需要在 *_ViewImports.cshtml* 中注册应用。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/tag-helpers/th-components/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="use-cases"></a>用例

标记帮助程序组件的两个常见用例包括：

1. [将 `<link>` 注入到 `<head>` 中。](#inject-into-html-head-element)
1. [将 `<script>` 注入到 `<body>` 中。](#inject-into-html-body-element)

以下各节介绍了这些用例。

### <a name="inject-into-html-head-element"></a>注入到 HTML head 元素中

在 HTML `<head>` 元素中，通常使用 HTML `<link>` 元素导入 CSS 文件。 以下代码使用 `head` 标记帮助程序组件将 `<link>` 元素注入到 `<head>` 元素中：

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressStyleTagHelperComponent.cs)]

在上述代码中：

* `AddressStyleTagHelperComponent` 可实现 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent>。 抽象：
  * 允许初始化带有 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext> 的类。
  * 启用标记帮助程序组件以添加或修改 HTML 元素。
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.Order*> 属性可定义呈现组件的顺序。 应用中的标记帮助程序组件有多种用法时，`Order` 是必需的。
* <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperComponent.ProcessAsync*> 会将执行上下文的 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContext.TagName*> 属性值与 `head` 进行比较。 如果比较的计算结果为 true，则 `_style` 字段的内容将注入到 HTML `<head>` 元素中。

### <a name="inject-into-html-body-element"></a>注入到 HTML body 元素中

`body` 标记帮助程序组件可以将 `<script>` 元素注入到 `<body>` 元素中。 以下代码演示了此项技术：

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressScriptTagHelperComponent.cs)]

单独的 HTML 文件用于存储 `<script>` 元素。 HTML 文件使代码更清洁且更易于维护。 上述代码可读取 *TagHelpers/Templates/AddressToolTipScript.html* 的内容并将其追加到标记帮助程序输出中。 *AddressToolTipScript.html* 文件包括以下标记：

[!code-html[](th-components/samples/RazorPagesSample/TagHelpers/Templates/AddressToolTipScript.html)]

上述代码可将[启动工具提示小组件](https://getbootstrap.com/docs/3.3/javascript/#tooltips)绑定到包含 `printable` 属性的任何 `<address>` 元素。 当鼠标指针悬停在元素上时，可以显示效果。

## <a name="register-a-component"></a>注册组件

标记帮助程序组件必须添加到应用的标记帮助程序组件集合。 有以下三种方法可添加到集合：

* [ASP.NET Core 中的标记帮助程序组件](#tag-helper-components-in-aspnet-core)
  * [用例](#use-cases)
    * [注入到 HTML head 元素中](#inject-into-html-head-element)
    * [注入到 HTML body 元素中](#inject-into-html-body-element)
  * [注册组件](#register-a-component)
    * [通过服务容器注册](#registration-via-services-container)
    * [通过 Razor 文件注册](#registration-via-razor-file)
    * [通过页面模型或控制器注册](#registration-via-page-model-or-controller)
  * [创建组件](#create-a-component)
  * [其他资源](#additional-resources)

### <a name="registration-via-services-container"></a>通过服务容器注册

如果未使用 <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.ITagHelperComponentManager> 管理标记帮助程序组件类，则必须向[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 系统注册。 以下 `Startup.ConfigureServices` 代码可注册带有[瞬态生存期](xref:fundamentals/dependency-injection#lifetime-and-registration-options)的 `AddressStyleTagHelperComponent` 和 `AddressScriptTagHelperComponent` 类:

[!code-csharp[](th-components/samples/RazorPagesSample/Startup.cs?name=snippet_ConfigureServices&highlight=12-15)]

### <a name="registration-via-razor-file"></a>通过 Razor 文件注册

如果未向 DI 注册标记帮助程序组件，则可以从 Razor Pages 页或 MVC 视图注册。 此项技术用于控制注入的标记和 Razor 文件中的组件执行顺序。

`ITagHelperComponentManager` 用于添加标记帮助程序组件或从应用中删除这些组件。 以下代码演示了使用 `AddressTagHelperComponent` 的此项技术：

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_ITagHelperComponentManager)]

在上述代码中：

* `@inject` 指令提供了 `ITagHelperComponentManager` 的实例。 该实例将分配到名为 `manager` 的变量以访问 Razor 文件中的下游。
* `AddressTagHelperComponent` 的实例将添加到应用的标记帮助程序组件集合。

修改 `AddressTagHelperComponent` 以适应接受 `markup` 和 `order` 参数的构造函数：

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_Constructor)]

提供的 `markup` 参数用于 `ProcessAsync`，如下所示：

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponent.cs?name=snippet_ProcessAsync&highlight=10-11)]

### <a name="registration-via-page-model-or-controller"></a>通过页面模型或控制器注册

如果未向 DI 注册标记帮助程序组件，则可以从 Razor Pages 页面模型或 MVC 控制器注册。 此项技术可用于将 C# 逻辑与 Razor 文件隔离。

构造函数注入用于访问 `ITagHelperComponentManager` 的实例。 标记帮助程序组件将添加到该实例的标记帮助程序组件集合。 以下 Razor Pages 页面模型演示了使用 `AddressTagHelperComponent` 的此项技术：

[!code-csharp[](th-components/samples/RazorPagesSample/Pages/Index.cshtml.cs?name=snippet_IndexModelClass)]

在上述代码中：

* 构造函数注入用于访问 `ITagHelperComponentManager` 的实例。
* `AddressTagHelperComponent` 的实例将添加到应用的标记帮助程序组件集合。

## <a name="create-a-component"></a>创建组件

创建自定义标记帮助程序组件：

* 创建派生自 <xref:Microsoft.AspNetCore.Mvc.Razor.TagHelpers.TagHelperComponentTagHelper> 的公共类。
* 将 [[HtmlTargetElement]](xref:Microsoft.AspNetCore.Razor.TagHelpers.HtmlTargetElementAttribute) 属性应用于该类。 指定目标 HTML 元素的名称。
* *可选*：将 [[EditorBrowsable(EditorBrowsableState.Never)]](xref:System.ComponentModel.EditorBrowsableAttribute) 属性应用于类，以取消在 IntelliSense 中显示类型。

以下代码可创建面向 `<address>` HTML 元素的自定义标记帮助程序组件：

[!code-csharp[](th-components/samples/RazorPagesSample/TagHelpers/AddressTagHelperComponentTagHelper.cs)]

按如下所示使用自定义 `address` 标记帮助程序组件注入 HTML 标记：

```csharp
public class AddressTagHelperComponent : TagHelperComponent
{
    private readonly string _printableButton =
        "<button type='button' class='btn btn-info' onclick=\"window.open("
        "'https://binged.it/2AXRRYw')\">" +
        "<span class='glyphicon glyphicon-road' aria-hidden='true'></span>" +
        "</button>";

    public override int Order => 3;

    public override async Task ProcessAsync(TagHelperContext context,
                                            TagHelperOutput output)
    {
        if (string.Equals(context.TagName, "address",
                StringComparison.OrdinalIgnoreCase) &&
            output.Attributes.ContainsName("printable"))
        {
            var content = await output.GetChildContentAsync();
            output.Content.SetHtmlContent(
                $"<div>{content.GetContent()}</div>{_printableButton}");
        }
    }
}
```

上述 `ProcessAsync` 方法可将向 <xref:Microsoft.AspNetCore.Razor.TagHelpers.TagHelperContent.SetHtmlContent*> 提供的 HTML 注入到匹配的 `<address>` 元素中。 在以下情况下进行注入：

* 执行上下文的 `TagName` 属性值等于 `address`。
* 相应的 `<address>` 元素具有 `printable` 属性。

例如，在处理以下 `<address>` 元素时，`if` 语句的计算结果为 true：

[!code-cshtml[](th-components/samples/RazorPagesSample/Pages/Contact.cshtml?name=snippet_AddressPrintable)]

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/dependency-injection>
* <xref:mvc/views/dependency-injection>
* <xref:mvc/views/tag-helpers/builtin-th/Index>
