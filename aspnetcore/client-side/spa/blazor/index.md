---
title: Blazor 简介
author: guardrex
description: 了解 ASP.NET Core Blazor，它是一种使用 .NET 生成在支持 WebAssembly 的浏览器中运行的交互式客户端应用的新方法。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
uid: spa/blazor/index
ms.openlocfilehash: 0d22365701a4fc1857582c13459280e50d59c858
ms.sourcegitcommit: af8a6eb5375ef547a52ffae22465e265837aa82b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56159570"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="13b9c-103">Blazor 简介</span><span class="sxs-lookup"><span data-stu-id="13b9c-103">Introduction to Blazor</span></span>

<span data-ttu-id="13b9c-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="13b9c-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="13b9c-105">Blazor 是一个单页应用框架，用于使用 .NET 生成交互式客户端 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="13b9c-105">Blazor is a single-page app framework for building interactive client-side Web apps with .NET.</span></span> <span data-ttu-id="13b9c-106">Blazor 使用开放的 Web 标准，没有插件或代码转换。</span><span class="sxs-lookup"><span data-stu-id="13b9c-106">Blazor uses open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="13b9c-107">Blazor 适用于所有新式 Web 浏览器，包括移动浏览器。</span><span class="sxs-lookup"><span data-stu-id="13b9c-107">Blazor works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="13b9c-108">在浏览器中使用 .NET 进行客户端 Web 开发可提供诸多优势：</span><span class="sxs-lookup"><span data-stu-id="13b9c-108">Using .NET in the browser for client-side web development offers many advantages:</span></span>

* <span data-ttu-id="13b9c-109">**C# 语言**：使用 C# 代替 JavaScript 来编写代码。</span><span class="sxs-lookup"><span data-stu-id="13b9c-109">**C# language**: Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="13b9c-110">**.NET 生态系统**：利用现有的 .NET 库生态系统。</span><span class="sxs-lookup"><span data-stu-id="13b9c-110">**.NET Ecosystem**: Leverage the existing ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="13b9c-111">**完整堆栈开发**：共享服务器和客户端逻辑。</span><span class="sxs-lookup"><span data-stu-id="13b9c-111">**Full-stack development**: Share server and client-side logic.</span></span>
* <span data-ttu-id="13b9c-112">**快速且具有可伸缩性**：.NET 旨在实现出色的性能、可靠性和安全性。</span><span class="sxs-lookup"><span data-stu-id="13b9c-112">**Speed and scalability**: NET was built for performance, reliability, and security.</span></span>
* <span data-ttu-id="13b9c-113">**行业领先工具**：始终高效支持 Windows、Linux 和 macOS 上的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="13b9c-113">**Industry-leading tools**: Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="13b9c-114">**稳定性和一致性**：以一组稳定、功能丰富且易用的通用语言、框架和工具为基础来进行生成。</span><span class="sxs-lookup"><span data-stu-id="13b9c-114">**Stability and consistency**:  Build on a commonset of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

<span data-ttu-id="13b9c-115">通过 [WebAssembly](http://webassembly.org)（缩写为 wasm），可在 Web 浏览器内运行 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="13b9c-115">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="13b9c-116">WebAssembly 是开放的 Web 标准，支持用于无插件的 Web 浏览器。</span><span class="sxs-lookup"><span data-stu-id="13b9c-116">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="13b9c-117">WebAssembly 是针对快速下载和最大执行速度优化的压缩字节码格式。</span><span class="sxs-lookup"><span data-stu-id="13b9c-117">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="13b9c-118">WebAssembly 代码可通过 JavaScript 互操作访问浏览器的完整功能。</span><span class="sxs-lookup"><span data-stu-id="13b9c-118">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="13b9c-119">同时，通过 WebAssembly 执行的 .NET 代码在与 JavaScript 相同的可信沙盒中运行，可防止客户端计算机上的恶意操作。</span><span class="sxs-lookup"><span data-stu-id="13b9c-119">At the same time, .NET code executed via WebAssembly runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![Blazor 使用 WebAssembly 在浏览器中运行 .NET 代码。](index/_static/blazor.png)

<span data-ttu-id="13b9c-121">生成 Blazor 应用并在浏览器中运行时：</span><span class="sxs-lookup"><span data-stu-id="13b9c-121">When a Blazor app is built and run in a browser:</span></span>

* <span data-ttu-id="13b9c-122">C# 代码文件和 Razor 文件将被编译为 .NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="13b9c-122">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="13b9c-123">该程序集和 .NET 运行时将被下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="13b9c-123">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="13b9c-124">Blazor 启动 .NET 运行时并配置运行时，为应用加载程序集。</span><span class="sxs-lookup"><span data-stu-id="13b9c-124">Blazor bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="13b9c-125">文档对象模型 (DOM) 操作和浏览器 API 调用将由 Blazor 运行时通过 JavaScript 互操作处理。</span><span class="sxs-lookup"><span data-stu-id="13b9c-125">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor runtime via JavaScript interop.</span></span>

<span data-ttu-id="13b9c-126">Blazor 支持大多数应用所需的核心内容，包括：</span><span class="sxs-lookup"><span data-stu-id="13b9c-126">Blazor supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="13b9c-127">参数</span><span class="sxs-lookup"><span data-stu-id="13b9c-127">Parameters</span></span>
* <span data-ttu-id="13b9c-128">事件处理</span><span class="sxs-lookup"><span data-stu-id="13b9c-128">Event handling</span></span>
* <span data-ttu-id="13b9c-129">数据绑定</span><span class="sxs-lookup"><span data-stu-id="13b9c-129">Data binding</span></span>
* <span data-ttu-id="13b9c-130">路由</span><span class="sxs-lookup"><span data-stu-id="13b9c-130">Routing</span></span>
* <span data-ttu-id="13b9c-131">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="13b9c-131">Dependency injection</span></span>
* <span data-ttu-id="13b9c-132">布局</span><span class="sxs-lookup"><span data-stu-id="13b9c-132">Layouts</span></span>
* <span data-ttu-id="13b9c-133">模板化</span><span class="sxs-lookup"><span data-stu-id="13b9c-133">Templating</span></span>
* <span data-ttu-id="13b9c-134">级联值</span><span class="sxs-lookup"><span data-stu-id="13b9c-134">Cascading values</span></span>

<span data-ttu-id="13b9c-135">以在[中间语言 (IL) 链接器](xref:host-and-deploy/razor-components/configure-linker)发布应用时，从应用中删除未使用的代码以减小已下载应用的大小。</span><span class="sxs-lookup"><span data-stu-id="13b9c-135">To reduce the size of the downloaded app unused code stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/razor-components/configure-linker).</span></span>

<span data-ttu-id="13b9c-136">Blazor 是 Razor 组件的客户端托管模型。</span><span class="sxs-lookup"><span data-stu-id="13b9c-136">Blazor is the client-side hosting model for Razor Components.</span></span> <span data-ttu-id="13b9c-137">由于 Razor 组件可将组件的呈现逻辑从 UI 更新应用方式中分离出来，因此可灵活选择托管 Razor 组件的方式。</span><span class="sxs-lookup"><span data-stu-id="13b9c-137">Because Razor Components decouple a component's rendering logic from how UI updates are applied, there's flexibility in how Razor Components can be hosted.</span></span> <span data-ttu-id="13b9c-138">使用 ASP.NET Core Razor 组件在 ASP.NET Core 应用中的服务器上托管 Razor 组件，在该应用中可通过 SignalR 连接处理 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="13b9c-138">Use ASP.NET Core Razor Components to host Razor Components on the server in an ASP.NET Core app where UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="13b9c-139">有关更多信息，请参见<xref:razor-components/hosting-models#server-side-hosting-model>。</span><span class="sxs-lookup"><span data-stu-id="13b9c-139">For more information, see <xref:razor-components/hosting-models#server-side-hosting-model>.</span></span> 

## <a name="components"></a><span data-ttu-id="13b9c-140">组件数</span><span class="sxs-lookup"><span data-stu-id="13b9c-140">Components</span></span>

<span data-ttu-id="13b9c-141">Razor 组件是 UI（例如，页面、对话框或数据输入窗体）的一部分。</span><span class="sxs-lookup"><span data-stu-id="13b9c-141">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="13b9c-142">组件处理用户事件，并定义灵活的 UI 呈现逻辑。</span><span class="sxs-lookup"><span data-stu-id="13b9c-142">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="13b9c-143">组件可以嵌套和重用。</span><span class="sxs-lookup"><span data-stu-id="13b9c-143">Components can be nested and reused.</span></span>

<span data-ttu-id="13b9c-144">组件是内置于 .NET 程序集的 .NET 类，可以作为 NuGet 包进行共享和分发。</span><span class="sxs-lookup"><span data-stu-id="13b9c-144">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="13b9c-145">可以将类编写为 Razor 标记页 (.cshtml) 的形式，也可以编写为 C# 类 (.cs)。</span><span class="sxs-lookup"><span data-stu-id="13b9c-145">The class can either be written in the form of a Razor markup page (*.cshtml*) or as a C# class (*.cs*).</span></span>

<span data-ttu-id="13b9c-146">[Razor](xref:mvc/views/razor) 是用于将 HTML 标记与 C# 代码结合在一起的语法。</span><span class="sxs-lookup"><span data-stu-id="13b9c-146">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="13b9c-147">Razor 为提高开发人员工作效率而设计，允许开发人员通过 [IntelliSense](/visualstudio/ide/using-intellisense) 支持在相同文件中的标记和 C# 之间切换。</span><span class="sxs-lookup"><span data-stu-id="13b9c-147">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="13b9c-148">Razor Pages 和 MVC 视图也使用 Razor。</span><span class="sxs-lookup"><span data-stu-id="13b9c-148">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="13b9c-149">与围绕请求/响应模型生成的 Razor Pages 和 MVC 视图不同，组件专门用于处理 UI 构成。</span><span class="sxs-lookup"><span data-stu-id="13b9c-149">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="13b9c-150">Razor 组件可专门用于客户端 UI 逻辑和构成。</span><span class="sxs-lookup"><span data-stu-id="13b9c-150">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="13b9c-151">以下标记是 Razor 文件 (DialogComponent.cshtml) 中的自定义对话框组件的示例：</span><span class="sxs-lookup"><span data-stu-id="13b9c-151">The following markup is an example of a custom dialog component in a Razor file (*DialogComponent.cshtml*):</span></span>

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

<span data-ttu-id="13b9c-152">如果在应用的其他位置使用此组件，IntelliSense 可加快使用语法和参数补全的开发。</span><span class="sxs-lookup"><span data-stu-id="13b9c-152">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="13b9c-153">组件呈现为浏览器 DOM 的内存中表现形式，称为“呈现树”，然后可以使用它以灵活高效的方式更新 UI。</span><span class="sxs-lookup"><span data-stu-id="13b9c-153">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="13b9c-154">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="13b9c-154">JavaScript interop</span></span>

<span data-ttu-id="13b9c-155">对于需要第三方 JavaScript 库和浏览器 API 的应用，Blazor 与 JavaScript 进行互操作。</span><span class="sxs-lookup"><span data-stu-id="13b9c-155">For apps that require third-party JavaScript libraries and browser APIs, Blazor interoperates with JavaScript.</span></span> <span data-ttu-id="13b9c-156">组件能够使用 JavaScript 能够使用的任何库或 API。</span><span class="sxs-lookup"><span data-stu-id="13b9c-156">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="13b9c-157">C# 代码可以调用到 JavaScript 代码，而 JavaScript 代码可以调用到 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="13b9c-157">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="13b9c-158">有关详细信息，请参阅 [JavaScript 互操作](xref:razor-components/javascript-interop)。</span><span class="sxs-lookup"><span data-stu-id="13b9c-158">For more information, see [JavaScript interop](xref:razor-components/javascript-interop).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="13b9c-159">代码共享和 .NET Standard</span><span class="sxs-lookup"><span data-stu-id="13b9c-159">Code sharing and .NET Standard</span></span>

<span data-ttu-id="13b9c-160">应用可引用并使用现有的 [.NET Standard](/dotnet/standard/net-standard) 库。</span><span class="sxs-lookup"><span data-stu-id="13b9c-160">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="13b9c-161">.NET Standard 是正式的 .NET API 规范，常见于 .NET 实现中。</span><span class="sxs-lookup"><span data-stu-id="13b9c-161">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="13b9c-162">支持 .NET standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="13b9c-162">.NET Standard 2.0 or higher is supported.</span></span> <span data-ttu-id="13b9c-163">不适用于 Web 浏览器内部的 API（例如，访问文件系统、打开套接字、线程处理和其他功能）将引发 <xref:System.PlatformNotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="13b9c-163">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw <xref:System.PlatformNotSupportedException>.</span></span> <span data-ttu-id="13b9c-164">.NET Standard 类库可在服务器代码和基于浏览器的应用中共享。</span><span class="sxs-lookup"><span data-stu-id="13b9c-164">.NET Standard class libraries can be shared across server code and in browser-based apps.</span></span>

## <a name="optimization"></a><span data-ttu-id="13b9c-165">优化</span><span class="sxs-lookup"><span data-stu-id="13b9c-165">Optimization</span></span>

<span data-ttu-id="13b9c-166">有效负载大小是应用可用性的关键性能因素。</span><span class="sxs-lookup"><span data-stu-id="13b9c-166">Payload size is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="13b9c-167">Blazor 优化有效负载大小，以缩短下载时间：</span><span class="sxs-lookup"><span data-stu-id="13b9c-167">Blazor optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="13b9c-168">在生成过程中，将删除未使用的 .NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="13b9c-168">Unused parts of .NET assemblies are removed during the build process.</span></span>
* <span data-ttu-id="13b9c-169">压缩 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="13b9c-169">HTTP responses are compressed.</span></span>
* <span data-ttu-id="13b9c-170">.NET 运行时和程序集缓存在浏览器中。</span><span class="sxs-lookup"><span data-stu-id="13b9c-170">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="13b9c-171">[服务器端 Razor 组件](xref:razor-components/index)通过维护 .NET 程序集、应用的程序集和运行时服务器端，提供比 Blazor 更小的有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="13b9c-171">[Server-side Razor Components](xref:razor-components/index) provides an even smaller payload size than Blazor by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="13b9c-172">Razor 组件应用仅向客户端提供标记文件和静态资产。</span><span class="sxs-lookup"><span data-stu-id="13b9c-172">Razor Components apps only serve markup files and static assets to clients.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="13b9c-173">其他资源</span><span class="sxs-lookup"><span data-stu-id="13b9c-173">Additional resources</span></span>

* <xref:razor-components/index>
* [<span data-ttu-id="13b9c-174">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="13b9c-174">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="13b9c-175">C# 指南</span><span class="sxs-lookup"><span data-stu-id="13b9c-175">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="13b9c-176">HTML</span><span class="sxs-lookup"><span data-stu-id="13b9c-176">HTML</span></span>](https://www.w3.org/html/)
