---
title: Razor 组件简介
author: guardrex
description: 了解 ASP.NET Core Razor 组件，用户可以借助它在 ASP.NET Core 应用中使用 .NET 生成交互式客户端 Web UI。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/12/2019
uid: razor-components/index
ms.openlocfilehash: 04a73d33cee0deedaf3dc97395836a936b580fbd
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159521"
---
# <a name="introduction-to-razor-components"></a>Razor 组件简介

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

借助 Razor 组件，可以使用 .NET 生成交互式客户端 Web UI：

* 使用 C# 代替 JavaScript 来生成丰富的交互式 UI。
* 共享全部使用 .NET 编写的服务器端和客户端应用逻辑。
* 将 UI 呈现为 HTML 和 CSS，以支持众多浏览器，其中包括移动浏览器。

Razor 组件支持大多数应用所需的核心内容，包括：

* 参数
* 事件处理
* 数据绑定
* 路由
* 依赖关系注入
* 布局
* 模板化
* 级联值

Razor 组件将组件呈现逻辑从 UI 更新的应用方式中分离出来。 .NET Core 3.0 中的 ASP.NET Core Razor 组件在 ASP.NET Core 应用中添加了对在服务器上托管 Razor 组件的支持。 可通过 SignalR 连接处理 UI 更新。

运行时执行以下操作：

* 处理从浏览器到服务器的发送 UI 事件。
* 运行组件后，将服务器发送的 UI 更新重新应用到浏览器。

Razor 组件用于与浏览器通信的连接还用于处理 JavaScript 互操作调用。

![Razor 组件在服务器上运行 .NET 代码，并通过 SignalR 连接与客户端上的文档对象模型进行交互](index/_static/aspnet-core-razor-components.png)

有关更多信息，请参见<xref:razor-components/hosting-models#server-side-hosting-model>。

Blazor 是 Razor 组件的实验性客户端托管模型。 Blazor 在使用开放 Web 标准的浏览器中的 .NET 上运行，没有插件，也没有代码转译。 有关更多信息，请参见<xref:razor-components/hosting-models#client-side-hosting-model>。

## <a name="components"></a>组件数

Razor 组件是 UI（例如，页面、对话框或数据输入窗体）的一部分。 组件处理用户事件，并定义灵活的 UI 呈现逻辑。 组件可以嵌套和重用。

组件是内置于 .NET 程序集的 .NET 类，可以作为 NuGet 包进行共享和分发。 可以将类编写为 Razor 标记页 (.cshtml) 的形式，也可以编写为 C# 类 (.cs)。

[Razor](xref:mvc/views/razor) 是用于将 HTML 标记与 C# 代码结合在一起的语法。 Razor 为提高开发人员工作效率而设计，允许开发人员通过 [IntelliSense](/visualstudio/ide/using-intellisense) 支持在相同文件中的标记和 C# 之间切换。 Razor Pages 和 MVC 视图也使用 Razor。 与围绕请求/响应模型生成的 Razor Pages 和 MVC 视图不同，组件专门用于处理 UI 构成。 Razor 组件可专门用于客户端 UI 逻辑和构成。

以下标记是 Razor 文件 (DialogComponent.cshtml) 中的自定义对话框组件的示例：

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick=@OnOK>OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

如果在应用的其他位置使用此组件，IntelliSense 可加快使用语法和参数补全的开发。

组件呈现为浏览器 DOM 的内存中表现形式，称为“呈现树”，然后可以使用它以灵活高效的方式更新 UI。

## <a name="javascript-interop"></a>JavaScript 互操作

对于需要第三方 JavaScript 库和浏览器 API 的应用，组件与 JavaScript 进行互操作。 组件能够使用 JavaScript 能够使用的任何库或 API。 C# 代码可以调用到 JavaScript 代码，而 JavaScript 代码可以调用到 C# 代码。 有关详细信息，请参阅 [JavaScript 互操作](xref:razor-components/javascript-interop)。

## <a name="additional-resources"></a>其他资源

* <xref:spa/blazor/index>
* [WebAssembly](http://webassembly.org/)
* [C# 指南](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
