---
title: ASP.NET Core 中的 Blazor 简介
author: guardrex
description: 了解 ASP.NET Core Blazor，用户可以借助它在 ASP.NET Core 应用中使用 .NET 生成交互式客户端 Web UI。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 04/15/2019
uid: blazor/index
ms.openlocfilehash: a5b12a5c5c10a74ab192d0d67a2ba9a5617232c7
ms.sourcegitcommit: 017b673b3c700d2976b77201d0ac30172e2abc87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2019
ms.locfileid: "59614559"
---
# <a name="introduction-to-blazor"></a><span data-ttu-id="739a7-103">Blazor 简介</span><span class="sxs-lookup"><span data-stu-id="739a7-103">Introduction to Blazor</span></span>

<span data-ttu-id="739a7-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="739a7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="razor-components"></a><span data-ttu-id="739a7-105">Razor 组件</span><span class="sxs-lookup"><span data-stu-id="739a7-105">Razor Components</span></span>

<span data-ttu-id="739a7-106">借助 Razor 组件（Blazor 的服务器端托管模型），可以使用 .NET 生成交互式客户端 Web UI：</span><span class="sxs-lookup"><span data-stu-id="739a7-106">The server-side hosting model of Blazor, *Razor Components*, is a way to build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="739a7-107">使用 C# 代替 JavaScript 来生成丰富的交互式 UI。</span><span class="sxs-lookup"><span data-stu-id="739a7-107">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="739a7-108">共享全部使用 .NET 编写的服务器端和客户端应用逻辑。</span><span class="sxs-lookup"><span data-stu-id="739a7-108">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="739a7-109">将 UI 呈现为 HTML 和 CSS，以支持众多浏览器，其中包括移动浏览器。</span><span class="sxs-lookup"><span data-stu-id="739a7-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="739a7-110">Razor 组件支持大多数应用所需的核心内容，包括：</span><span class="sxs-lookup"><span data-stu-id="739a7-110">Razor Components supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="739a7-111">参数</span><span class="sxs-lookup"><span data-stu-id="739a7-111">Parameters</span></span>
* <span data-ttu-id="739a7-112">事件处理</span><span class="sxs-lookup"><span data-stu-id="739a7-112">Event handling</span></span>
* <span data-ttu-id="739a7-113">数据绑定</span><span class="sxs-lookup"><span data-stu-id="739a7-113">Data binding</span></span>
* <span data-ttu-id="739a7-114">路由</span><span class="sxs-lookup"><span data-stu-id="739a7-114">Routing</span></span>
* <span data-ttu-id="739a7-115">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="739a7-115">Dependency injection</span></span>
* <span data-ttu-id="739a7-116">布局</span><span class="sxs-lookup"><span data-stu-id="739a7-116">Layouts</span></span>
* <span data-ttu-id="739a7-117">模板化</span><span class="sxs-lookup"><span data-stu-id="739a7-117">Templating</span></span>
* <span data-ttu-id="739a7-118">级联值</span><span class="sxs-lookup"><span data-stu-id="739a7-118">Cascading values</span></span>

<span data-ttu-id="739a7-119">Razor 组件将组件呈现逻辑从 UI 更新的应用方式中分离出来。</span><span class="sxs-lookup"><span data-stu-id="739a7-119">Razor Components decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="739a7-120">.NET Core 3.0 中的 ASP.NET Core Razor 组件在 ASP.NET Core 应用中添加了对在服务器上托管 Razor 组件的支持。</span><span class="sxs-lookup"><span data-stu-id="739a7-120">ASP.NET Core Razor Components in .NET Core 3.0 adds support for hosting Razor Components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="739a7-121">可通过 SignalR 连接处理 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="739a7-121">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="739a7-122">运行时执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="739a7-122">The runtime:</span></span>

* <span data-ttu-id="739a7-123">处理从浏览器到服务器的发送 UI 事件。</span><span class="sxs-lookup"><span data-stu-id="739a7-123">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="739a7-124">运行组件后，将服务器发送的 UI 更新重新应用到浏览器。</span><span class="sxs-lookup"><span data-stu-id="739a7-124">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="739a7-125">Razor 组件用于与浏览器通信的连接还用于处理 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="739a7-125">The connection used by Razor Components to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Razor 组件在服务器上运行 .NET 代码，并通过 SignalR 连接与客户端上的文档对象模型进行交互](index/_static/aspnet-core-razor-components.png)

<span data-ttu-id="739a7-127">有关更多信息，请参见<xref:blazor/hosting-models#server-side-hosting-model>。</span><span class="sxs-lookup"><span data-stu-id="739a7-127">For more information, see <xref:blazor/hosting-models#server-side-hosting-model>.</span></span>

## <a name="components"></a><span data-ttu-id="739a7-128">组件数</span><span class="sxs-lookup"><span data-stu-id="739a7-128">Components</span></span>

<span data-ttu-id="739a7-129">Razor 组件是 UI（例如，页面、对话框或数据输入窗体）的一部分。</span><span class="sxs-lookup"><span data-stu-id="739a7-129">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="739a7-130">组件处理用户事件，并定义灵活的 UI 呈现逻辑。</span><span class="sxs-lookup"><span data-stu-id="739a7-130">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="739a7-131">组件可以嵌套和重用。</span><span class="sxs-lookup"><span data-stu-id="739a7-131">Components can be nested and reused.</span></span>

<span data-ttu-id="739a7-132">组件是内置于 .NET 程序集的 .NET 类，可以作为 NuGet 包进行共享和分发。</span><span class="sxs-lookup"><span data-stu-id="739a7-132">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="739a7-133">类通常以带有 .razor 文件扩展名的 Razor 标记页（Razor 组件）或带有 .cshtml 文件扩展名的 Razor 标记页 (Blazor) 的形式编写。</span><span class="sxs-lookup"><span data-stu-id="739a7-133">The class is normally written in the form of a Razor markup page with a *.razor* file extension (Razor Components) or Razor markup page with a *.cshtml* file extension (Blazor).</span></span>

<span data-ttu-id="739a7-134">[Razor](xref:mvc/views/razor) 是用于将 HTML 标记与 C# 代码结合在一起的语法。</span><span class="sxs-lookup"><span data-stu-id="739a7-134">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="739a7-135">Razor 为提高开发人员工作效率而设计，允许开发人员通过 [IntelliSense](/visualstudio/ide/using-intellisense) 支持在相同文件中的标记和 C# 之间切换。</span><span class="sxs-lookup"><span data-stu-id="739a7-135">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="739a7-136">Razor Pages 和 MVC 视图也使用 Razor。</span><span class="sxs-lookup"><span data-stu-id="739a7-136">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="739a7-137">与围绕请求/响应模型生成的 Razor Pages 和 MVC 视图不同，组件专门用于处理 UI 构成。</span><span class="sxs-lookup"><span data-stu-id="739a7-137">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="739a7-138">Razor 组件可专门用于客户端 UI 逻辑和构成。</span><span class="sxs-lookup"><span data-stu-id="739a7-138">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="739a7-139">以下标记是自定义对话框组件的示例：</span><span class="sxs-lookup"><span data-stu-id="739a7-139">The following markup is an example of a custom dialog component:</span></span>

```cshtml
<div>
    <h2>@Title</h2>
    @BodyContent
    <button onclick="@OnOK">OK</button>
</div>

@functions {
    public string Title { get; set; }
    public RenderFragment BodyContent { get; set; }
    public Action OnOK { get; set; }
}
```

<span data-ttu-id="739a7-140">如果在应用的其他位置使用此组件，IntelliSense 可加快使用语法和参数补全的开发。</span><span class="sxs-lookup"><span data-stu-id="739a7-140">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="739a7-141">组件呈现为浏览器 DOM 的内存中表现形式，称为“呈现树”，然后可以使用它以灵活高效的方式更新 UI。</span><span class="sxs-lookup"><span data-stu-id="739a7-141">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="739a7-142">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="739a7-142">JavaScript interop</span></span>

<span data-ttu-id="739a7-143">对于需要第三方 JavaScript 库和浏览器 API 的应用，组件与 JavaScript 进行互操作。</span><span class="sxs-lookup"><span data-stu-id="739a7-143">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="739a7-144">组件能够使用 JavaScript 能够使用的任何库或 API。</span><span class="sxs-lookup"><span data-stu-id="739a7-144">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="739a7-145">C# 代码可以调用到 JavaScript 代码，而 JavaScript 代码可以调用到 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="739a7-145">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="739a7-146">有关详细信息，请参阅 [JavaScript 互操作](xref:blazor/javascript-interop)。</span><span class="sxs-lookup"><span data-stu-id="739a7-146">For more information, see [JavaScript interop](xref:blazor/javascript-interop).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="739a7-147">代码共享和 .NET Standard</span><span class="sxs-lookup"><span data-stu-id="739a7-147">Code sharing and .NET Standard</span></span>

<span data-ttu-id="739a7-148">应用可引用并使用现有的 [.NET Standard](/dotnet/standard/net-standard) 库。</span><span class="sxs-lookup"><span data-stu-id="739a7-148">Apps can reference and use existing [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="739a7-149">.NET Standard 是正式的 .NET API 规范，常见于 .NET 实现中。</span><span class="sxs-lookup"><span data-stu-id="739a7-149">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="739a7-150">Blazor 实现 .NET Standard 2.0。</span><span class="sxs-lookup"><span data-stu-id="739a7-150">Blazor implements .NET Standard 2.0.</span></span> <span data-ttu-id="739a7-151">不适用于 Web 浏览器内部的 API（例如，访问文件系统、打开套接字、线程处理和其他功能）将引发 <xref:System.PlatformNotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="739a7-151">APIs that aren't applicable inside a web browser (for example, accessing the file system, opening a socket, threading, and other features) throw <xref:System.PlatformNotSupportedException>.</span></span> <span data-ttu-id="739a7-152">.NET Standard 类库可以在不同 .NET 平台之间共享，例如 Blazor、.NET Framework、.NET Core、Xamarin、Mono 和 Unity。</span><span class="sxs-lookup"><span data-stu-id="739a7-152">.NET Standard class libraries can be shared across different .NET platforms, like Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

## <a name="blazor"></a><span data-ttu-id="739a7-153">Blazor</span><span class="sxs-lookup"><span data-stu-id="739a7-153">Blazor</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="739a7-154">Blazor 是一个单页应用框架，用于使用 .NET 生成交互式客户端 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="739a7-154">Blazor is a single-page app framework for building interactive client-side Web apps with .NET.</span></span> <span data-ttu-id="739a7-155">Blazor 使用开放的 Web 标准，没有插件或代码转换。</span><span class="sxs-lookup"><span data-stu-id="739a7-155">Blazor uses open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="739a7-156">Blazor 适用于所有新式 Web 浏览器，包括移动浏览器。</span><span class="sxs-lookup"><span data-stu-id="739a7-156">Blazor works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="739a7-157">在浏览器中使用 .NET 进行客户端 Web 开发可提供诸多优势：</span><span class="sxs-lookup"><span data-stu-id="739a7-157">Using .NET in the browser for client-side web development offers many advantages:</span></span>

* <span data-ttu-id="739a7-158">**C# 语言**：使用 C# 代替 JavaScript 来编写代码。</span><span class="sxs-lookup"><span data-stu-id="739a7-158">**C# language**: Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="739a7-159">**.NET 生态系统**：利用现有的 .NET 库生态系统。</span><span class="sxs-lookup"><span data-stu-id="739a7-159">**.NET Ecosystem**: Leverage the existing ecosystem of .NET libraries.</span></span>
* <span data-ttu-id="739a7-160">**完整堆栈开发**：共享服务器和客户端逻辑。</span><span class="sxs-lookup"><span data-stu-id="739a7-160">**Full-stack development**: Share server and client-side logic.</span></span>
* <span data-ttu-id="739a7-161">**快速且具有可伸缩性**：.NET 旨在实现出色的性能、可靠性和安全性。</span><span class="sxs-lookup"><span data-stu-id="739a7-161">**Speed and scalability**: .NET was built for performance, reliability, and security.</span></span>
* <span data-ttu-id="739a7-162">**行业领先工具**：始终高效支持 Windows、Linux 和 macOS 上的 Visual Studio。</span><span class="sxs-lookup"><span data-stu-id="739a7-162">**Industry-leading tools**: Stay productive with Visual Studio on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="739a7-163">**稳定性和一致性**：以一组稳定、功能丰富且易用的通用语言、框架和工具为基础来进行生成。</span><span class="sxs-lookup"><span data-stu-id="739a7-163">**Stability and consistency**:  Build on a commonset of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

<span data-ttu-id="739a7-164">通过 [WebAssembly](http://webassembly.org)（缩写为 wasm），可在 Web 浏览器内运行 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="739a7-164">Running .NET code inside web browsers is made possible by [WebAssembly](http://webassembly.org) (abbreviated *wasm*).</span></span> <span data-ttu-id="739a7-165">WebAssembly 是开放的 Web 标准，支持用于无插件的 Web 浏览器。</span><span class="sxs-lookup"><span data-stu-id="739a7-165">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span> <span data-ttu-id="739a7-166">WebAssembly 是针对快速下载和最大执行速度优化的压缩字节码格式。</span><span class="sxs-lookup"><span data-stu-id="739a7-166">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span>

<span data-ttu-id="739a7-167">WebAssembly 代码可通过 JavaScript 互操作访问浏览器的完整功能。</span><span class="sxs-lookup"><span data-stu-id="739a7-167">WebAssembly code can access the full functionality of the browser via JavaScript interop.</span></span> <span data-ttu-id="739a7-168">同时，通过 WebAssembly 执行的 .NET 代码在与 JavaScript 相同的可信沙盒中运行，可防止客户端计算机上的恶意操作。</span><span class="sxs-lookup"><span data-stu-id="739a7-168">At the same time, .NET code executed via WebAssembly runs in the same trusted sandbox as JavaScript to prevent malicious actions on the client machine.</span></span>

![Blazor 使用 WebAssembly 在浏览器中运行 .NET 代码。](index/_static/blazor.png)

<span data-ttu-id="739a7-170">生成 Blazor 应用并在浏览器中运行时：</span><span class="sxs-lookup"><span data-stu-id="739a7-170">When a Blazor app is built and run in a browser:</span></span>

* <span data-ttu-id="739a7-171">C# 代码文件和 Razor 文件将被编译为 .NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="739a7-171">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="739a7-172">该程序集和 .NET 运行时将被下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="739a7-172">The assemblies and the .NET runtime are downloaded to the browser.</span></span>
* <span data-ttu-id="739a7-173">Blazor 启动 .NET 运行时并配置运行时，为应用加载程序集。</span><span class="sxs-lookup"><span data-stu-id="739a7-173">Blazor bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="739a7-174">文档对象模型 (DOM) 操作和浏览器 API 调用将由 Blazor 运行时通过 JavaScript 互操作处理。</span><span class="sxs-lookup"><span data-stu-id="739a7-174">Document Object Model (DOM) manipulation and browser API calls are handled by the Blazor runtime via JavaScript interop.</span></span>

<span data-ttu-id="739a7-175">Blazor 支持大多数应用所需的核心内容，包括：</span><span class="sxs-lookup"><span data-stu-id="739a7-175">Blazor supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="739a7-176">参数</span><span class="sxs-lookup"><span data-stu-id="739a7-176">Parameters</span></span>
* <span data-ttu-id="739a7-177">事件处理</span><span class="sxs-lookup"><span data-stu-id="739a7-177">Event handling</span></span>
* <span data-ttu-id="739a7-178">数据绑定</span><span class="sxs-lookup"><span data-stu-id="739a7-178">Data binding</span></span>
* <span data-ttu-id="739a7-179">路由</span><span class="sxs-lookup"><span data-stu-id="739a7-179">Routing</span></span>
* <span data-ttu-id="739a7-180">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="739a7-180">Dependency injection</span></span>
* <span data-ttu-id="739a7-181">布局</span><span class="sxs-lookup"><span data-stu-id="739a7-181">Layouts</span></span>
* <span data-ttu-id="739a7-182">模板化</span><span class="sxs-lookup"><span data-stu-id="739a7-182">Templating</span></span>
* <span data-ttu-id="739a7-183">级联值</span><span class="sxs-lookup"><span data-stu-id="739a7-183">Cascading values</span></span>

<span data-ttu-id="739a7-184">为减小已下载应用的大小，在[中间语言 (IL) 链接器](xref:host-and-deploy/blazor/configure-linker)发布应用时已从中删除未使用的代码。</span><span class="sxs-lookup"><span data-stu-id="739a7-184">To reduce the size of the downloaded app, unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:host-and-deploy/blazor/configure-linker).</span></span>

<span data-ttu-id="739a7-185">Blazor 客户端是一个客户端托管模型。</span><span class="sxs-lookup"><span data-stu-id="739a7-185">Blazor client-side is a client-side hosting model.</span></span> <span data-ttu-id="739a7-186">由于 Blazor 可将组件的呈现逻辑从 UI 更新应用方式中分离出来，因此可灵活选择托管 Blazor 的方式。</span><span class="sxs-lookup"><span data-stu-id="739a7-186">Because Blazor decouples a component's rendering logic from how UI updates are applied, there's flexibility in how Blazor can be hosted.</span></span> <span data-ttu-id="739a7-187">使用 ASP.NET Core [Razor 组件](#razor-components)在 ASP.NET Core 应用中的服务器上托管 Razor 组件，在该应用中可通过 SignalR 连接处理 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="739a7-187">Use ASP.NET Core [Razor Components](#razor-components) to host Razor Components on the server in an ASP.NET Core app where UI updates are handled over a SignalR connection.</span></span> <span data-ttu-id="739a7-188">有关更多信息，请参见<xref:blazor/hosting-models#server-side-hosting-model>。</span><span class="sxs-lookup"><span data-stu-id="739a7-188">For more information, see <xref:blazor/hosting-models#server-side-hosting-model>.</span></span> 

<span data-ttu-id="739a7-189">有效负载大小是应用可用性的关键性能因素。</span><span class="sxs-lookup"><span data-stu-id="739a7-189">Payload size is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="739a7-190">Blazor 优化有效负载大小，以缩短下载时间：</span><span class="sxs-lookup"><span data-stu-id="739a7-190">Blazor optimizes payload size to reduce download times:</span></span>

* <span data-ttu-id="739a7-191">在生成过程中，将删除未使用的 .NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="739a7-191">Unused parts of .NET assemblies are removed during the build process.</span></span>
* <span data-ttu-id="739a7-192">压缩 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="739a7-192">HTTP responses are compressed.</span></span>
* <span data-ttu-id="739a7-193">.NET 运行时和程序集缓存在浏览器中。</span><span class="sxs-lookup"><span data-stu-id="739a7-193">The .NET runtime and assemblies are cached in the browser.</span></span>

<span data-ttu-id="739a7-194">[Razor 组件](#razor-components)通过维护 .NET 程序集、应用的程序集和运行时服务器端，提供比 Blazor 更小的有效负载大小。</span><span class="sxs-lookup"><span data-stu-id="739a7-194">[Razor Components](#razor-components) provides a smaller payload size than Blazor by maintaining .NET assemblies, the app's assembly, and the runtime server-side.</span></span> <span data-ttu-id="739a7-195">Razor 组件应用仅向客户端提供标记文件和静态资产。</span><span class="sxs-lookup"><span data-stu-id="739a7-195">Razor Components apps only serve markup files and static assets to clients.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="739a7-196">其他资源</span><span class="sxs-lookup"><span data-stu-id="739a7-196">Additional resources</span></span>

* [<span data-ttu-id="739a7-197">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="739a7-197">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="739a7-198">C# 指南</span><span class="sxs-lookup"><span data-stu-id="739a7-198">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="739a7-199">HTML</span><span class="sxs-lookup"><span data-stu-id="739a7-199">HTML</span></span>](https://www.w3.org/html/)
