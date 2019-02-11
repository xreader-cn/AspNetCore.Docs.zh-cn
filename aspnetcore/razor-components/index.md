---
title: Razor 组件简介
author: guardrex
description: 了解 Blazor，一种使用 C#/Razor 和 HTML 且在具有 WebAssembly 的浏览器中运行的 .NET Web 框架。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 01/29/2019
uid: razor-components/index
ms.openlocfilehash: 0f7a4b2ec404b6ceec2e643fea9e0635de6ad716
ms.sourcegitcommit: ed76cc752966c604a795fbc56d5a71d16ded0b58
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55667911"
---
# <a name="introduction-to-razor-components"></a>Razor 组件简介

作者：[Steve Sanderson](http://blog.stevensanderson.com)、[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

ASP.NET Core Razor 组件在 ASP.NET Core 中执行服务器端，而 Blazor（Razor 组件已执行的客户端）是使用 C#/Razor 和 HTML 且在具有 WebAssembly 的浏览器中运行的实验性 .NET Web 框架。 Blazor 提供客户端上使用 .NET 的客户端 Web UI 框架的所有好处。

多年来，Web 已在许多方面有所改进，但构建新型 Web 应用仍然具有挑战。 在浏览器中使用 .NET 可提供多个优势，有助于使 Web 开发更轻松且更高效：

* **稳定性和一致性**：.NET 在各种稳定、功能丰富和易于使用的平台提供标准化编程框架。
* **现代创新语言**：.NET 语言通过创新的新语言功能不断改进。
* **行业领先工具**：Visual Studio 产品系列在 Windows、Linux 和 macOS 上跨平台提供绝佳的 .NET 开发体验。
* **快速和可伸缩性**：.NET 在应用开发方面具有强大的性能、可靠性和安全性历史记录。 使用 .NET 作为全面堆栈解决方案，可更轻松地构建快速、可靠和安全的应用。
* **利用现有技能的全面堆栈开发**：C#/Razor 开发人员使用其现有的 C#/Razor 技能来编写客户端代码并在应用之间共享服务器和客户端逻辑。
* **广泛的浏览器支持**：Razor 组件将 UI 呈现为普通标记和 JavaScript。 Blazor 在使用开放 Web 标准的浏览器中的 .NET 上运行，并且没有插件，没有代码转译。 Blazor 适用于所有新式 Web 浏览器，包括移动浏览器。

## <a name="hosting-models"></a>托管模型

### <a name="server-side-hosting-model"></a>服务器端托管模型

由于 Razor 组件可将组件的呈现逻辑从 UI 更新应用方式中分离出来，因此可灵活选择托管 Razor 组件的方式。 NET Core 3.0 中的 ASP.NET Core Razor 组件在 ASP.NET Core 应用中添加了对在服务器上托管 Razor 组件的支持，在该应用中可通过 SignalR 连接处理所有 UI 更新。 运行时处理从浏览器向服务器发送 UI 事件，然后在运行组件后，将服务器发送的 UI 更新应用到浏览器。 还可使用该连接来处理 JavaScript 互操作调用。

![Razor 组件在服务器上运行 .NET 代码，并通过 SignalR 连接与客户端上的文档对象模型进行交互](index/_static/aspnet-core-razor-components.png)

有关更多信息，请参见<xref:razor-components/hosting-models#server-side-hosting-model>。

### <a name="client-side-hosting-model"></a>客户端托管模型

通过相对较新的技术 [WebAssembly](http://webassembly.org)（缩写为 wasm），可在 Web 浏览器内运行 .NET 代码。 WebAssembly 是开放的 Web 标准，且支持无插件的 Web 浏览器。 WebAssembly 是针对快速下载和最大执行速度优化的压缩字节码格式。

WebAssembly 代码可通过 JavaScript 互操作访问浏览器的完整功能。 同时，WebAssembly 代码在与 JavaScript 相同的可信沙盒中运行，以防止客户端计算机上的恶意操作。

![Blazor 使用 WebAssembly 在浏览器中运行 .NET 代码。](index/_static/blazor.png)

生成 Blazor 应用并在浏览器中运行时：

1. C# 代码文件和 Razor 文件将被编译为 .NET 程序集。
1. 该程序集和 .NET 运行时将被下载到浏览器。
1. Blazor 使用 JavaScript 启动 .NET 运行时并配置该运行时，以加载所需的程序集引用。 文档对象模型 (DOM) 操作和浏览器 API 调用将由 Blazor 运行时通过 JavaScript 互操作性处理。

若要支持不支持 WebAssembly 的较旧浏览器，可以使用 ASP.NET Core Razor 组件[服务器端托管模型](#server-side-hosting-model)。

有关更多信息，请参见<xref:razor-components/hosting-models#client-side-hosting-model>。

## <a name="components"></a>组件数

应用使用组件进行构建。 组件是 UI（例如，页面、对话框或数据输入窗体）的一部分。 可在项目之间嵌套、重复使用和共享组件。

组件是 .NET 类。 可直接将类编写为 C# 类 (\*.cs)，或更常见的是 Razor 标记页 (\*.cshtml) 的形式。

[Razor](/aspnet/core/mvc/views/razor) 是用于将 HTML 标记与 C# 代码结合在一起的语法。 Razor 为提高开发人员工作效率而设计，允许开发人员通过 [IntelliSense](/visualstudio/ide/using-intellisense) 支持在相同文件中的标记和 C# 之间切换。 以下标记是 Razor 文件 (DialogComponent.cshtml) 中的基本自定义对话框组件的示例：

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

组件可以是：

* 嵌套。
* 使用 Razor (\*.cshtml) 或 C# (\*.cs) 代码创建。
* 通过类库共享。
* 单元测试无需浏览器 DOM。

## <a name="infrastructure"></a>基础结构

Razor 组件提供大多数应用需要的核心功能，包括：

* 布局
* 路由
* 依赖关系注入

上述所有功能都是可选的。 如果应用中未使用其中某种功能，则当由[中间语言 (IL) 链接器](xref:host-and-deploy/razor-components/configure-linker)发布时，将从应用中去除实现。

## <a name="code-sharing-and-net-standard"></a>代码共享和 .NET Standard

应用可引用并使用现有的 [.NET Standard](/dotnet/standard/net-standard) 库。 .NET Standard 是正式的 .NET API 规范，常见于 .NET 实现中。 支持 .NET standard 2.0 或更高版本。 不适用于 Web 浏览器内部的 API（例如，访问文件系统、打开套接字、线程处理和其他功能）将引发 [PlatformNotSupportedException](/dotnet/api/system.platformnotsupportedexception)。 .NET Standard 类库可在服务器代码和基于浏览器的应用中共享。

## <a name="javascript-interop"></a>JavaScript 互操作

对于需要第三方 JavaScript 库和浏览器 API 的应用，WebAssembly 为与 JavaScript 进行互操作而设计。 Razor 组件能够使用 JavaScript 能够使用的任何库或 API。 C# 代码可以调用到 JavaScript 代码，而 JavaScript 代码可以调用到 C# 代码。 有关详细信息，请参阅 [JavaScript 互操作](xref:razor-components/javascript-interop)。

## <a name="optimization"></a>优化

对于客户端应用，有效负载大小至关重要。 Blazor 优化有效负载大小，以缩短下载时间。 例如，在生成过程中删除 .NET 程序集未使用的部分、压缩 HTTP 响应以及在浏览器中缓存 .NET 运行时和程序集。

Razor 组件通过维护 .NET 程序集、应用的程序集和运行时服务器端，提供比 Blazor 更小的有效负载大小。 Razor 组件应用仅对客户端的标记、脚本和样式表提供服务。

## <a name="deployment"></a>部署

使用 Blazor 生成纯独立客户端应用或同时包含服务器和客户端应用的全面堆栈 ASP.NET Core 应用：

* 在“独立客户端应用”中，Blazor 应用将编译为仅包含静态文件的 dist 文件夹。 可以在 Azure 应用服务、GitHub 页、IIS（配置为静态文件服务器）、Node.js 服务器和许多其他服务器和服务上托管文件。 生产环境中的服务器上不需要 .NET。
* 在全面堆栈 ASP.NET Core 应用中，可在服务器和客户端应用之间共享代码。 生成的 ASP.NET Core Razor 组件应用（用于为客户端 UI 和其他服务器端 API 终结点提供服务）可生成并部署到 ASP.NET Core 支持的任何云或本地主机。

## <a name="suggest-a-feature-or-file-a-bug-report"></a>建议功能或提交 bug 报告

若要提出建议和提交 bug 报告，请[提出问题](https://github.com/aspnet/AspNetCore/issues/new)。 如需一般帮助和从社区获得答案，请在 [Gitter](https://gitter.im/aspnet/Blazor) 上加入对话。

## <a name="additional-resources"></a>其他资源

* [WebAssembly](http://webassembly.org/)
* [C# 指南](/dotnet/csharp/)
* [Razor](/aspnet/core/mvc/views/razor)
* [HTML](https://www.w3.org/html/)
