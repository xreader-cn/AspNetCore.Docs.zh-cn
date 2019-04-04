---
title: Blazor 简介
author: guardrex
description: 了解 ASP.NET Core Blazor，它是一种使用 .NET 生成在支持 WebAssembly 的浏览器中运行的交互式客户端应用的新方法。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/27/2019
uid: spa/blazor/index
ms.openlocfilehash: c42363b0ce70a1a8edc19b1c428ee802a6eff3dd
ms.sourcegitcommit: 3e9e1f6d572947e15347e818f769e27dea56b648
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2019
ms.locfileid: "58750491"
---
# <a name="introduction-to-blazor"></a>Blazor 简介

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

Blazor 是一个单页应用框架，用于使用 .NET 生成交互式客户端 Web 应用。 Blazor 使用开放的 Web 标准，没有插件或代码转换。 Blazor 适用于所有新式 Web 浏览器，包括移动浏览器。

在浏览器中使用 .NET 进行客户端 Web 开发可提供诸多优势：

* **C# 语言**：使用 C# 代替 JavaScript 来编写代码。
* **.NET 生态系统**：利用现有的 .NET 库生态系统。
* **完整堆栈开发**：共享服务器和客户端逻辑。
* **快速且具有可伸缩性**：.NET 旨在实现出色的性能、可靠性和安全性。
* **行业领先工具**：始终高效支持 Windows、Linux 和 macOS 上的 Visual Studio。
* **稳定性和一致性**：以一组稳定、功能丰富且易用的通用语言、框架和工具为基础来进行生成。

通过 [WebAssembly](http://webassembly.org)（缩写为 wasm），可在 Web 浏览器内运行 .NET 代码。 WebAssembly 是开放的 Web 标准，支持用于无插件的 Web 浏览器。 WebAssembly 是针对快速下载和最大执行速度优化的压缩字节码格式。

WebAssembly 代码可通过 JavaScript 互操作访问浏览器的完整功能。 同时，通过 WebAssembly 执行的 .NET 代码在与 JavaScript 相同的可信沙盒中运行，可防止客户端计算机上的恶意操作。

![Blazor 使用 WebAssembly 在浏览器中运行 .NET 代码。](index/_static/blazor.png)

生成 Blazor 应用并在浏览器中运行时：

* C# 代码文件和 Razor 文件将被编译为 .NET 程序集。
* 该程序集和 .NET 运行时将被下载到浏览器。
* Blazor 启动 .NET 运行时并配置运行时，为应用加载程序集。 文档对象模型 (DOM) 操作和浏览器 API 调用将由 Blazor 运行时通过 JavaScript 互操作处理。

Blazor 支持大多数应用所需的核心内容，包括：

* 参数
* 事件处理
* 数据绑定
* 路由
* 依赖关系注入
* 布局
* 模板化
* 级联值

以在[中间语言 (IL) 链接器](xref:host-and-deploy/razor-components/configure-linker)发布应用时，从应用中删除未使用的代码以减小已下载应用的大小。

Blazor 是 Razor 组件的客户端托管模型。 由于 Razor 组件可将组件的呈现逻辑从 UI 更新应用方式中分离出来，因此可灵活选择托管 Razor 组件的方式。 使用 ASP.NET Core Razor 组件在 ASP.NET Core 应用中的服务器上托管 Razor 组件，在该应用中可通过 SignalR 连接处理 UI 更新。 有关详细信息，请参阅 <xref:razor-components/index> 和 <xref:razor-components/hosting-models#server-side-hosting-model>。 

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

对于需要第三方 JavaScript 库和浏览器 API 的应用，Blazor 与 JavaScript 进行互操作。 组件能够使用 JavaScript 能够使用的任何库或 API。 C# 代码可以调用到 JavaScript 代码，而 JavaScript 代码可以调用到 C# 代码。 有关详细信息，请参阅 [JavaScript 互操作](xref:razor-components/javascript-interop)。

## <a name="code-sharing-and-net-standard"></a>代码共享和 .NET Standard

应用可引用并使用现有的 [.NET Standard](/dotnet/standard/net-standard) 库。 .NET Standard 是正式的 .NET API 规范，常见于 .NET 实现中。 支持 .NET standard 2.0 或更高版本。 不适用于 Web 浏览器内部的 API（例如，访问文件系统、打开套接字、线程处理和其他功能）将引发 <xref:System.PlatformNotSupportedException>。 .NET Standard 类库可在服务器代码和基于浏览器的应用中共享。

## <a name="optimization"></a>优化

有效负载大小是应用可用性的关键性能因素。 Blazor 优化有效负载大小，以缩短下载时间：

* 在生成过程中，将删除未使用的 .NET 程序集。
* 压缩 HTTP 响应。
* .NET 运行时和程序集缓存在浏览器中。

[服务器端 Razor 组件](xref:razor-components/index)通过维护 .NET 程序集、应用的程序集和运行时服务器端，提供比 Blazor 更小的有效负载大小。 Razor 组件应用仅向客户端提供标记文件和静态资产。

## <a name="additional-resources"></a>其他资源

* <xref:razor-components/index>
* [WebAssembly](http://webassembly.org/)
* [C# 指南](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [HTML](https://www.w3.org/html/)
