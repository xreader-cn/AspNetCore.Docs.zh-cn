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
# <a name="introduction-to-razor-components"></a><span data-ttu-id="2a953-103">Razor 组件简介</span><span class="sxs-lookup"><span data-stu-id="2a953-103">Introduction to Razor Components</span></span>

<span data-ttu-id="2a953-104">作者：[Steve Sanderson](http://blog.stevensanderson.com)、[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2a953-104">By [Steve Sanderson](http://blog.stevensanderson.com), [Daniel Roth](https://github.com/danroth27), and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="2a953-105">ASP.NET Core Razor 组件在 ASP.NET Core 中执行服务器端，而 Blazor（Razor 组件已执行的客户端）是使用 C#/Razor 和 HTML 且在具有 WebAssembly 的浏览器中运行的实验性 .NET Web 框架。</span><span class="sxs-lookup"><span data-stu-id="2a953-105">*ASP.NET Core Razor Components* executes server-side in ASP.NET Core, while *Blazor* (Razor Components executed client-side) is an experimental .NET web framework using C#/Razor and HTML that runs in the browser with WebAssembly.</span></span> <span data-ttu-id="2a953-106">Blazor 提供客户端上使用 .NET 的客户端 Web UI 框架的所有好处。</span><span class="sxs-lookup"><span data-stu-id="2a953-106">Blazor provides all of the benefits of a client-side web UI framework using .NET on the client.</span></span>

<span data-ttu-id="2a953-107">多年来，Web 已在许多方面有所改进，但构建新型 Web 应用仍然具有挑战。</span><span class="sxs-lookup"><span data-stu-id="2a953-107">Web development has improved in many ways over the years, but building modern web apps still poses challenges.</span></span> <span data-ttu-id="2a953-108">在浏览器中使用 .NET 可提供多个优势，有助于使 Web 开发更轻松且更高效：</span><span class="sxs-lookup"><span data-stu-id="2a953-108">Using .NET in the browser offers many advantages that can help make web development easier and more productive:</span></span>

* <span data-ttu-id="2a953-109">**稳定性和一致性**：.NET 在各种稳定、功能丰富和易于使用的平台提供标准化编程框架。</span><span class="sxs-lookup"><span data-stu-id="2a953-109">**Stability and consistency**: .NET provides standardized programming frameworks across platforms that are stable, feature-rich, and easy to use.</span></span>
* <span data-ttu-id="2a953-110">**现代创新语言**：.NET 语言通过创新的新语言功能不断改进。</span><span class="sxs-lookup"><span data-stu-id="2a953-110">**Modern innovative languages**: .NET languages are constantly improving with innovative new language features.</span></span>
* <span data-ttu-id="2a953-111">**行业领先工具**：Visual Studio 产品系列在 Windows、Linux 和 macOS 上跨平台提供绝佳的 .NET 开发体验。</span><span class="sxs-lookup"><span data-stu-id="2a953-111">**Industry-leading tools**: The Visual Studio product family provides a fantastic .NET development experience across platforms on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="2a953-112">**快速和可伸缩性**：.NET 在应用开发方面具有强大的性能、可靠性和安全性历史记录。</span><span class="sxs-lookup"><span data-stu-id="2a953-112">**Speed and scalability**: .NET has a strong history of performance, reliability, and security for app development.</span></span> <span data-ttu-id="2a953-113">使用 .NET 作为全面堆栈解决方案，可更轻松地构建快速、可靠和安全的应用。</span><span class="sxs-lookup"><span data-stu-id="2a953-113">Using .NET as a full-stack solution makes it easier to build fast, reliable, and secure apps.</span></span>
* <span data-ttu-id="2a953-114">**利用现有技能的全面堆栈开发**：C#/Razor 开发人员使用其现有的 C#/Razor 技能来编写客户端代码并在应用之间共享服务器和客户端逻辑。</span><span class="sxs-lookup"><span data-stu-id="2a953-114">**Full-stack development that leverages existing skills**: C#/Razor developers use their existing C#/Razor skills to write client-side code and share server and client-side logic among apps.</span></span>
* <span data-ttu-id="2a953-115">**广泛的浏览器支持**：Razor 组件将 UI 呈现为普通标记和 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="2a953-115">**Wide browser support**: Razor Components render the UI as ordinary markup and JavaScript.</span></span> <span data-ttu-id="2a953-116">Blazor 在使用开放 Web 标准的浏览器中的 .NET 上运行，并且没有插件，没有代码转译。</span><span class="sxs-lookup"><span data-stu-id="2a953-116">Blazor runs on .NET in the browser using open web standards with no plugins and no code transpilation.</span></span> <span data-ttu-id="2a953-117">Blazor 适用于所有新式 Web 浏览器，包括移动浏览器。</span><span class="sxs-lookup"><span data-stu-id="2a953-117">Blazor works in all modern web browsers, including mobile browsers.</span></span>

## <a name="hosting-models"></a><span data-ttu-id="2a953-118">托管模型</span><span class="sxs-lookup"><span data-stu-id="2a953-118">Hosting models</span></span>

### <a name="server-side-hosting-model"></a><span data-ttu-id="2a953-119">服务器端托管模型</span><span class="sxs-lookup"><span data-stu-id="2a953-119">Server-side hosting model</span></span>

<span data-ttu-id="2a953-120">由于 Razor 组件可将组件的呈现逻辑从 UI 更新应用方式中分离出来，因此可灵活选择托管 Razor 组件的方式。</span><span class="sxs-lookup"><span data-stu-id="2a953-120">Because Razor Components decouple a component's rendering logic from how the UI updates are applied, there is flexibility in how Razor Components can be hosted.</span></span> <span data-ttu-id="2a953-121">NET Core 3.0 中的 ASP.NET Core Razor 组件在 ASP.NET Core 应用中添加了对在服务器上托管 Razor 组件的支持，在该应用中可通过 SignalR 连接处理所有 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="2a953-121">ASP.NET Core Razor Components in .NET Core 3.0 adds support for hosting Razor Components on the server in an ASP.NET Core app where all UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="2a953-122">运行时处理从浏览器向服务器发送 UI 事件，然后在运行组件后，将服务器发送的 UI 更新应用到浏览器。</span><span class="sxs-lookup"><span data-stu-id="2a953-122">The runtime handles sending UI events from the browser to the server and then applies UI updates sent by the server back to the browser after running the components.</span></span> <span data-ttu-id="2a953-123">还可使用该连接来处理 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="2a953-123">The same connection is also used to handle JavaScript interop calls.</span></span>

![Razor 组件在服务器上运行 .NET 代码，并通过 SignalR 连接与客户端上的文档对象模型进行交互](index/_static/aspnet-core-razor-components.png)

<span data-ttu-id="2a953-125">有关更多信息，请参见<xref:razor-components/hosting-models#server-side-hosting-model>。</span><span class="sxs-lookup"><span data-stu-id="2a953-125">For more information, see <xref:razor-components/hosting-models#server-side-hosting-model>.</span></span>

### <a name="client-side-hosting-model"></a><span data-ttu-id="2a953-126">客户端托管模型</span><span class="sxs-lookup"><span data-stu-id="2a953-126">Client-side hosting model</span></span>

<span data-ttu-id="2a953-127">通过相对较新的技术 [WebAssembly](http://webassembly.org)（缩写为 wasm），可在 Web 浏览器内运行 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="2a953-127">Running .NET code inside web browsers is made possible by a relatively new technology, [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="2a953-128">WebAssembly 是开放的 Web 标准，且支持无插件的 Web 浏览器。</span><span class="sxs-lookup"><span data-stu-id="2a953-128">WebAssembly is an open web standard and is supported in web browsers without plugins.</span></span> <span data-ttu-id="2a953-129">WebAssembly 是针对快速下载和最大执行速度优化的压缩字节码格式。</span><span class="sxs-lookup"><span data-stu-id="2a953-129">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="2a953-130">WebAssembly 代码可通过 JavaScript 互操作访问浏览器的完整功能。</span><span class="sxs-lookup"><span data-stu-id="2a953-130">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="2a953-131">同时，WebAssembly 代码在与 JavaScript 相同的可信沙盒中运行，以防止客户端计算机上的恶意操作。</span><span class="sxs-lookup"><span data-stu-id="2a953-131">At the same time, WebAssembly code runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![Blazor 使用 WebAssembly 在浏览器中运行 .NET 代码。](index/_static/blazor.png)

<span data-ttu-id="2a953-133">生成 Blazor 应用并在浏览器中运行时：</span><span class="sxs-lookup"><span data-stu-id="2a953-133">When a Blazor app is built and run in a browser:</span></span>

1. <span data-ttu-id="2a953-134">C# 代码文件和 Razor 文件将被编译为 .NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="2a953-134">C# code files and Razor files are compiled into .NET assemblies.</span></span>
1. <span data-ttu-id="2a953-135">该程序集和 .NET 运行时将被下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="2a953-135">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
1. <span data-ttu-id="2a953-136">Blazor 使用 JavaScript 启动 .NET 运行时并配置该运行时，以加载所需的程序集引用。</span><span class="sxs-lookup"><span data-stu-id="2a953-136">Blazor uses JavaScript to bootstrap the .NET runtime and configures the runtime to load required assembly references.</span></span> <span data-ttu-id="2a953-137">文档对象模型 (DOM) 操作和浏览器 API 调用将由 Blazor 运行时通过 JavaScript 互操作性处理。</span><span class="sxs-lookup"><span data-stu-id="2a953-137">Document object model (DOM) manipulation and browser API calls are handled by the Blazor runtime via JavaScript interoperability.</span></span>

<span data-ttu-id="2a953-138">若要支持不支持 WebAssembly 的较旧浏览器，可以使用 ASP.NET Core Razor 组件[服务器端托管模型](#server-side-hosting-model)。</span><span class="sxs-lookup"><span data-stu-id="2a953-138">To support older browsers that don't support WebAssembly, you can use the ASP.NET Core Razor Components [server-side hosting model](#server-side-hosting-model).</span></span>

<span data-ttu-id="2a953-139">有关更多信息，请参见<xref:razor-components/hosting-models#client-side-hosting-model>。</span><span class="sxs-lookup"><span data-stu-id="2a953-139">For more information, see <xref:razor-components/hosting-models#client-side-hosting-model>.</span></span>

## <a name="components"></a><span data-ttu-id="2a953-140">组件数</span><span class="sxs-lookup"><span data-stu-id="2a953-140">Components</span></span>

<span data-ttu-id="2a953-141">应用使用组件进行构建。</span><span class="sxs-lookup"><span data-stu-id="2a953-141">Apps are built with *components*.</span></span> <span data-ttu-id="2a953-142">组件是 UI（例如，页面、对话框或数据输入窗体）的一部分。</span><span class="sxs-lookup"><span data-stu-id="2a953-142">A component is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="2a953-143">可在项目之间嵌套、重复使用和共享组件。</span><span class="sxs-lookup"><span data-stu-id="2a953-143">Components can be nested, reused, and shared between projects.</span></span>

<span data-ttu-id="2a953-144">组件是 .NET 类。</span><span class="sxs-lookup"><span data-stu-id="2a953-144">A *component* is a .NET class.</span></span> <span data-ttu-id="2a953-145">可直接将类编写为 C# 类 (\*.cs)，或更常见的是 Razor 标记页 (\*.cshtml) 的形式。</span><span class="sxs-lookup"><span data-stu-id="2a953-145">The class can either be written directly, as a C# class (*\*.cs*), or more commonly in the form of a Razor markup page (*\*.cshtml*).</span></span>

<span data-ttu-id="2a953-146">[Razor](/aspnet/core/mvc/views/razor) 是用于将 HTML 标记与 C# 代码结合在一起的语法。</span><span class="sxs-lookup"><span data-stu-id="2a953-146">[Razor](/aspnet/core/mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="2a953-147">Razor 为提高开发人员工作效率而设计，允许开发人员通过 [IntelliSense](/visualstudio/ide/using-intellisense) 支持在相同文件中的标记和 C# 之间切换。</span><span class="sxs-lookup"><span data-stu-id="2a953-147">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="2a953-148">以下标记是 Razor 文件 (DialogComponent.cshtml) 中的基本自定义对话框组件的示例：</span><span class="sxs-lookup"><span data-stu-id="2a953-148">The following markup is an example of a basic custom dialog component in a Razor file (*DialogComponent.cshtml*):</span></span>

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

<span data-ttu-id="2a953-149">如果在应用的其他位置使用此组件，IntelliSense 可加快使用语法和参数补全的开发。</span><span class="sxs-lookup"><span data-stu-id="2a953-149">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="2a953-150">组件可以是：</span><span class="sxs-lookup"><span data-stu-id="2a953-150">Components can be:</span></span>

* <span data-ttu-id="2a953-151">嵌套。</span><span class="sxs-lookup"><span data-stu-id="2a953-151">Nested.</span></span>
* <span data-ttu-id="2a953-152">使用 Razor (\*.cshtml) 或 C# (\*.cs) 代码创建。</span><span class="sxs-lookup"><span data-stu-id="2a953-152">Created with Razor (*\*.cshtml*) or C# (*\*.cs*) code.</span></span>
* <span data-ttu-id="2a953-153">通过类库共享。</span><span class="sxs-lookup"><span data-stu-id="2a953-153">Shared via class libraries.</span></span>
* <span data-ttu-id="2a953-154">单元测试无需浏览器 DOM。</span><span class="sxs-lookup"><span data-stu-id="2a953-154">Unit tested without requiring a browser DOM.</span></span>

## <a name="infrastructure"></a><span data-ttu-id="2a953-155">基础结构</span><span class="sxs-lookup"><span data-stu-id="2a953-155">Infrastructure</span></span>

<span data-ttu-id="2a953-156">Razor 组件提供大多数应用需要的核心功能，包括：</span><span class="sxs-lookup"><span data-stu-id="2a953-156">Razor Components offers the core facilities that most apps require, including:</span></span>

* <span data-ttu-id="2a953-157">布局</span><span class="sxs-lookup"><span data-stu-id="2a953-157">Layouts</span></span>
* <span data-ttu-id="2a953-158">路由</span><span class="sxs-lookup"><span data-stu-id="2a953-158">Routing</span></span>
* <span data-ttu-id="2a953-159">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="2a953-159">Dependency injection</span></span>

<span data-ttu-id="2a953-160">上述所有功能都是可选的。</span><span class="sxs-lookup"><span data-stu-id="2a953-160">All of these features are optional.</span></span> <span data-ttu-id="2a953-161">如果应用中未使用其中某种功能，则当由[中间语言 (IL) 链接器](xref:host-and-deploy/razor-components/configure-linker)发布时，将从应用中去除实现。</span><span class="sxs-lookup"><span data-stu-id="2a953-161">When one of these features isn't used in an app, the implementation is stripped out of the app when published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/razor-components/configure-linker).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="2a953-162">代码共享和 .NET Standard</span><span class="sxs-lookup"><span data-stu-id="2a953-162">Code sharing and .NET Standard</span></span>

<span data-ttu-id="2a953-163">应用可引用并使用现有的 [.NET Standard](/dotnet/standard/net-standard) 库。</span><span class="sxs-lookup"><span data-stu-id="2a953-163">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="2a953-164">.NET Standard 是正式的 .NET API 规范，常见于 .NET 实现中。</span><span class="sxs-lookup"><span data-stu-id="2a953-164">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="2a953-165">支持 .NET standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="2a953-165">.NET Standard 2.0 or higher is supported.</span></span> <span data-ttu-id="2a953-166">不适用于 Web 浏览器内部的 API（例如，访问文件系统、打开套接字、线程处理和其他功能）将引发 [PlatformNotSupportedException](/dotnet/api/system.platformnotsupportedexception)。</span><span class="sxs-lookup"><span data-stu-id="2a953-166">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw [PlatformNotSupportedException](/dotnet/api/system.platformnotsupportedexception).</span></span> <span data-ttu-id="2a953-167">.NET Standard 类库可在服务器代码和基于浏览器的应用中共享。</span><span class="sxs-lookup"><span data-stu-id="2a953-167">.NET Standard class libraries can be shared across server code and in browser-based apps.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="2a953-168">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="2a953-168">JavaScript interop</span></span>

<span data-ttu-id="2a953-169">对于需要第三方 JavaScript 库和浏览器 API 的应用，WebAssembly 为与 JavaScript 进行互操作而设计。</span><span class="sxs-lookup"><span data-stu-id="2a953-169">For apps that require third-party JavaScript libraries and browser APIs, WebAssembly is designed to interoperate with JavaScript.</span></span> <span data-ttu-id="2a953-170">Razor 组件能够使用 JavaScript 能够使用的任何库或 API。</span><span class="sxs-lookup"><span data-stu-id="2a953-170">Razor Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="2a953-171">C# 代码可以调用到 JavaScript 代码，而 JavaScript 代码可以调用到 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="2a953-171">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="2a953-172">有关详细信息，请参阅 [JavaScript 互操作](xref:razor-components/javascript-interop)。</span><span class="sxs-lookup"><span data-stu-id="2a953-172">For more information, see [JavaScript interop](xref:razor-components/javascript-interop).</span></span>

## <a name="optimization"></a><span data-ttu-id="2a953-173">优化</span><span class="sxs-lookup"><span data-stu-id="2a953-173">Optimization</span></span>

<span data-ttu-id="2a953-174">对于客户端应用，有效负载大小至关重要。</span><span class="sxs-lookup"><span data-stu-id="2a953-174">For client-side apps, payload size is critical.</span></span> <span data-ttu-id="2a953-175">Blazor 优化有效负载大小，以缩短下载时间。</span><span class="sxs-lookup"><span data-stu-id="2a953-175">Blazor optimizes payload size to reduce download times.</span></span> <span data-ttu-id="2a953-176">例如，在生成过程中删除 .NET 程序集未使用的部分、压缩 HTTP 响应以及在浏览器中缓存 .NET 运行时和程序集。</span><span class="sxs-lookup"><span data-stu-id="2a953-176">For example, unused parts of .NET assemblies are removed during the build process, HTTP responses are compressed, and the .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="2a953-177">Razor 组件通过维护 .NET 程序集、应用的程序集和运行时服务器端，提供比 Blazor 更小的有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="2a953-177">Razor Components provides an even smaller payload size than Blazor by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="2a953-178">Razor 组件应用仅对客户端的标记、脚本和样式表提供服务。</span><span class="sxs-lookup"><span data-stu-id="2a953-178">Razor Components apps only serve markup, script, and stylesheets to clients.</span></span>

## <a name="deployment"></a><span data-ttu-id="2a953-179">部署</span><span class="sxs-lookup"><span data-stu-id="2a953-179">Deployment</span></span>

<span data-ttu-id="2a953-180">使用 Blazor 生成纯独立客户端应用或同时包含服务器和客户端应用的全面堆栈 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="2a953-180">Use Blazor to build a pure standalone client-side app or a full-stack ASP.NET Core app that contains both server and client apps:</span></span>

* <span data-ttu-id="2a953-181">在“独立客户端应用”中，Blazor 应用将编译为仅包含静态文件的 dist 文件夹。</span><span class="sxs-lookup"><span data-stu-id="2a953-181">In a **standalone client-side app**, the Blazor app is compiled into a *dist* folder that only contains static files.</span></span> <span data-ttu-id="2a953-182">可以在 Azure 应用服务、GitHub 页、IIS（配置为静态文件服务器）、Node.js 服务器和许多其他服务器和服务上托管文件。</span><span class="sxs-lookup"><span data-stu-id="2a953-182">The files can be hosted on Azure App Service, GitHub Pages, IIS (configured as a static file server), Node.js servers, and many other servers and services.</span></span> <span data-ttu-id="2a953-183">生产环境中的服务器上不需要 .NET。</span><span class="sxs-lookup"><span data-stu-id="2a953-183">.NET isn't required on the server in production.</span></span>
* <span data-ttu-id="2a953-184">在全面堆栈 ASP.NET Core 应用中，可在服务器和客户端应用之间共享代码。</span><span class="sxs-lookup"><span data-stu-id="2a953-184">In a **full-stack ASP.NET Core app**, code can be shared between server and client apps.</span></span> <span data-ttu-id="2a953-185">生成的 ASP.NET Core Razor 组件应用（用于为客户端 UI 和其他服务器端 API 终结点提供服务）可生成并部署到 ASP.NET Core 支持的任何云或本地主机。</span><span class="sxs-lookup"><span data-stu-id="2a953-185">The resulting ASP.NET Core Razor Components app, which serves the client-side UI and other server-side API endpoints, can be built and deployed to any cloud or on-premise host supported by ASP.NET Core.</span></span>

## <a name="suggest-a-feature-or-file-a-bug-report"></a><span data-ttu-id="2a953-186">建议功能或提交 bug 报告</span><span class="sxs-lookup"><span data-stu-id="2a953-186">Suggest a feature or file a bug report</span></span>

<span data-ttu-id="2a953-187">若要提出建议和提交 bug 报告，请[提出问题](https://github.com/aspnet/AspNetCore/issues/new)。</span><span class="sxs-lookup"><span data-stu-id="2a953-187">To make suggestions and file bug reports, please [open an issue](https://github.com/aspnet/AspNetCore/issues/new).</span></span> <span data-ttu-id="2a953-188">如需一般帮助和从社区获得答案，请在 [Gitter](https://gitter.im/aspnet/Blazor) 上加入对话。</span><span class="sxs-lookup"><span data-stu-id="2a953-188">For general help and to get answers from the community, join the conversation on [Gitter](https://gitter.im/aspnet/Blazor).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a953-189">其他资源</span><span class="sxs-lookup"><span data-stu-id="2a953-189">Additional resources</span></span>

* [<span data-ttu-id="2a953-190">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2a953-190">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="2a953-191">C# 指南</span><span class="sxs-lookup"><span data-stu-id="2a953-191">C# Guide</span></span>](/dotnet/csharp/)
* [<span data-ttu-id="2a953-192">Razor</span><span class="sxs-lookup"><span data-stu-id="2a953-192">Razor</span></span>](/aspnet/core/mvc/views/razor)
* [<span data-ttu-id="2a953-193">HTML</span><span class="sxs-lookup"><span data-stu-id="2a953-193">HTML</span></span>](https://www.w3.org/html/)
