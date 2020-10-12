---
title: ASP.NET Core Blazor 简介
author: guardrex
description: 了解 ASP.NET Core Blazor，用户可用它在 ASP.NET Core 应用中使用 .NET 生成交互式客户端 Web UI。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc, seoapril2019
ms.date: 09/25/2020
no-loc:
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
uid: blazor/index
ms.openlocfilehash: 7ad374fdc7452664e3367d6ef214fd4cebdf3b08
ms.sourcegitcommit: 139c998d37e9f3e3d0e3d72e10dbce8b75957d89
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2020
ms.locfileid: "91805514"
---
# <a name="introduction-to-aspnet-core-no-locblazor"></a><span data-ttu-id="f2dc1-103">ASP.NET Core Blazor 简介</span><span class="sxs-lookup"><span data-stu-id="f2dc1-103">Introduction to ASP.NET Core Blazor</span></span>

<span data-ttu-id="f2dc1-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="f2dc1-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="f2dc1-105">*欢迎使用 Blazor！*</span><span class="sxs-lookup"><span data-stu-id="f2dc1-105">*Welcome to Blazor!*</span></span>

<span data-ttu-id="f2dc1-106">Blazor 是一个使用 [.NET](/dotnet/standard/tour) 生成交互式客户端 Web UI 的框架：</span><span class="sxs-lookup"><span data-stu-id="f2dc1-106">Blazor is a framework for building interactive client-side web UI with [.NET](/dotnet/standard/tour):</span></span>

* <span data-ttu-id="f2dc1-107">使用 [C#](/dotnet/csharp/) 代替 [JavaScript](https://www.javascript.com) 来创建信息丰富的交互式 UI。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-107">Create rich interactive UIs using [C#](/dotnet/csharp/) instead of [JavaScript](https://www.javascript.com).</span></span>
* <span data-ttu-id="f2dc1-108">共享使用 .NET 编写的服务器端和客户端应用逻辑。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-108">Share server-side and client-side app logic written in .NET.</span></span>
* <span data-ttu-id="f2dc1-109">将 UI 呈现为 HTML 和 CSS，以支持众多浏览器，其中包括移动浏览器。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-109">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>
* <span data-ttu-id="f2dc1-110">与新式托管平台（如 [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index)）集成。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-110">Integrate with modern hosting platforms, such as [Docker](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span>

<span data-ttu-id="f2dc1-111">使用 .NET 进行客户端 Web 开发可提供以下优势：</span><span class="sxs-lookup"><span data-stu-id="f2dc1-111">Using .NET for client-side web development offers the following advantages:</span></span>

* <span data-ttu-id="f2dc1-112">使用 C# 代替 JavaScript 来编写代码。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-112">Write code in C# instead of JavaScript.</span></span>
* <span data-ttu-id="f2dc1-113">利用现有的 [.NET 库](/dotnet/standard/class-libraries)生态系统。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-113">Leverage the existing .NET ecosystem of [.NET libraries](/dotnet/standard/class-libraries).</span></span>
* <span data-ttu-id="f2dc1-114">在服务器和客户端之间共享应用逻辑。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-114">Share app logic across server and client.</span></span>
* <span data-ttu-id="f2dc1-115">受益于 .NET 的性能、可靠性和安全性。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-115">Benefit from .NET's performance, reliability, and security.</span></span>
* <span data-ttu-id="f2dc1-116">在 Windows、Linux 和 macOS 上使用 [Visual Studio](https://visualstudio.microsoft.com) 保持高效工作。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-116">Stay productive with [Visual Studio](https://visualstudio.microsoft.com) on Windows, Linux, and macOS.</span></span>
* <span data-ttu-id="f2dc1-117">以一组稳定、功能丰富且易用的通用语言、框架和工具为基础来进行生成。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-117">Build on a common set of languages, frameworks, and tools that are stable, feature-rich, and easy to use.</span></span>

## <a name="components"></a><span data-ttu-id="f2dc1-118">组件</span><span class="sxs-lookup"><span data-stu-id="f2dc1-118">Components</span></span>

<span data-ttu-id="f2dc1-119">Blazor应用基于组件。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-119">Blazor apps are based on *components*.</span></span> <span data-ttu-id="f2dc1-120">Blazor 中的组件是指 UI 元素，例如页面、对话框或数据输入窗体。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-120">A component in Blazor is an element of UI, such as a page, dialog, or data entry form.</span></span>

<span data-ttu-id="f2dc1-121">组件是内置到 [.NET 程序集](/dotnet/standard/assembly/)的 .NET C# 类，它们用于：</span><span class="sxs-lookup"><span data-stu-id="f2dc1-121">Components are .NET C# classes built into [.NET assemblies](/dotnet/standard/assembly/) that:</span></span>

* <span data-ttu-id="f2dc1-122">定义灵活的 UI 呈现逻辑。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-122">Define flexible UI rendering logic.</span></span>
* <span data-ttu-id="f2dc1-123">处理用户事件。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-123">Handle user events.</span></span>
* <span data-ttu-id="f2dc1-124">可以嵌套和重用。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-124">Can be nested and reused.</span></span>
* <span data-ttu-id="f2dc1-125">可作为 [Razor 类库](xref:razor-pages/ui-class)或 [NuGet 包](/nuget/what-is-nuget)共享和分发。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-125">Can be shared and distributed as [Razor class libraries](xref:razor-pages/ui-class) or [NuGet packages](/nuget/what-is-nuget).</span></span>

<span data-ttu-id="f2dc1-126">组件类通常以 [Razor](xref:mvc/views/razor) 标记页（文件扩展名为 `.razor`）的形式编写。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-126">The component class is usually written in the form of a [Razor](xref:mvc/views/razor) markup page with a `.razor` file extension.</span></span> <span data-ttu-id="f2dc1-127">Blazor 中的组件有时被称为 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-127">Components in Blazor are formally referred to as *Razor components*.</span></span> <span data-ttu-id="f2dc1-128">Razor 是一种语法，用于将 HTML 标记与专为提高开发人员工作效率而设计的 C# 代码结合在一起。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-128">Razor is a syntax for combining HTML markup with C# code designed for developer productivity.</span></span> <span data-ttu-id="f2dc1-129">借助 Razor，可使用 Visual Studio 中的 [IntelliSense](/visualstudio/ide/using-intellisense) 编程支持在同一文件中的 HTML 标记与 C# 之间切换。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-129">Razor allows you to switch between HTML markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) programming support in Visual Studio.</span></span> <span data-ttu-id="f2dc1-130">Razor Pages 和 MVC 也使用 Razor。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-130">Razor Pages and MVC also use Razor.</span></span> <span data-ttu-id="f2dc1-131">与基于请求/响应模型生成的 Razor Pages 和 MVC 不同，组件专门用于处理客户端 UI 逻辑和构成。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-131">Unlike Razor Pages and MVC, which are built around a request/response model, components are used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="f2dc1-132">Blazor 使用 UI 构成的自然 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-132">Blazor uses natural HTML tags for UI composition.</span></span> <span data-ttu-id="f2dc1-133">下面的 Razor 标记演示了一个组件 (`Dialog.razor`)，它显示一个对话框，并处理在用户选择按钮时发生的事件：</span><span class="sxs-lookup"><span data-stu-id="f2dc1-133">The following Razor markup demonstrates a component (`Dialog.razor`) that displays a dialog and processes an event when the user selects a button:</span></span>

```razor
<div class="card" style="width:22rem">
    <div class="card-body">
        <h3 class="card-title">@Title</h3>
        <p class="card-text">@ChildContent</p>
        <button @onclick="OnYes">Yes!</button>
    </div>
</div>

@code {
    [Parameter]
    public RenderFragment ChildContent { get; set; }

    [Parameter]
    public string Title { get; set; }

    private void OnYes()
    {
        Console.WriteLine("Write to the console in C#! 'Yes' button selected.");
    }
}
```

<span data-ttu-id="f2dc1-134">在上述示例中，`OnYes` 是由按钮的 `onclick` 事件触发的 C# 方法。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-134">In the preceding example, `OnYes` is a C# method triggered by the button's `onclick` event.</span></span> <span data-ttu-id="f2dc1-135">对话框的文本 (`ChildContent`) 和标题 (`Title`) 由在其 UI 中使用此组件的下述组件提供。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-135">The dialog's text (`ChildContent`) and title (`Title`) are provided by the following component that uses this component in its UI.</span></span>

<span data-ttu-id="f2dc1-136">使用 HTML 标记将 `Dialog` 组件嵌入到另一个组件中。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-136">The `Dialog` component is nested within another component using an HTML tag.</span></span> <span data-ttu-id="f2dc1-137">在以下示例中，`Index` 组件 (`Pages/Index.razor`) 使用前面的 `Dialog` 组件。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-137">In the following example, the `Index` component (`Pages/Index.razor`) uses the preceding `Dialog` component.</span></span> <span data-ttu-id="f2dc1-138">标记的 `Title` 属性向 `Dialog` 组件的 `Title` 属性传递标题的值。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-138">The tag's `Title` attribute passes a value for the title to the `Dialog` component's `Title` property.</span></span>  <span data-ttu-id="f2dc1-139">`Dialog` 组件的文本 (`ChildContent`) 由 `<Dialog>` 元素的内容设置。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-139">The `Dialog` component's text (`ChildContent`) are set by the content of the `<Dialog>` element.</span></span> <span data-ttu-id="f2dc1-140">向 `Index` 组件添加 `Dialog` 组件后，[Visual Studio 中的 IntelliSense](/visualstudio/ide/using-intellisense) 可加快使用语法和参数补全进行开发的速度。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-140">When the `Dialog` component is added to the `Index` component, [IntelliSense in Visual Studio](/visualstudio/ide/using-intellisense) speeds development with syntax and parameter completion.</span></span>

```razor
@page "/"

<h1>Hello, world!</h1>

<p>
    Welcome to your new app.
</p>

<Dialog Title="Learn More">
    Do you want to <i>learn more</i> about Blazor?
</Dialog>
```

<span data-ttu-id="f2dc1-141">在浏览器中访问父级 `Index` 组件时，将呈现该对话框。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-141">The dialog is rendered when the `Index` component is accessed in a browser.</span></span> <span data-ttu-id="f2dc1-142">当用户选择此按钮时，浏览器的开发人员工具控制台会显示由 `OnYes` 方法编写的消息：</span><span class="sxs-lookup"><span data-stu-id="f2dc1-142">When the button is selected by the user, the browser's developer tools console shows the message written by the `OnYes` method:</span></span>

![在 Index 组件中嵌入的浏览器内呈现 Dialog 组件。](index/_static/dialog.png)

<span data-ttu-id="f2dc1-146">组件呈现为浏览器[文档对象模型 (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) 的内存中表现形式，它被称为“呈现树”，用于以灵活高效的方式更新 UI。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-146">Components render into an in-memory representation of the browser's [Document Object Model (DOM)](https://developer.mozilla.org/docs/Web/API/Document_Object_Model/Introduction) called a *render tree*, which is used to update the UI in a flexible and efficient way.</span></span>

## Blazor WebAssembly

<span data-ttu-id="f2dc1-147">Blazor WebAssembly 是[单页应用 (SPA) 框架](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps)，用于使用 .NET 生成交互式客户端 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-147">Blazor WebAssembly is a [single-page app (SPA) framework](/dotnet/architecture/modern-web-apps-azure/choose-between-traditional-web-and-single-page-apps) for building interactive client-side web apps with .NET.</span></span> <span data-ttu-id="f2dc1-148">Blazor WebAssembly 使用无插件或将代码重新编译为其他语言的开放式 Web 标准。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-148">Blazor WebAssembly uses open web standards without plugins or recompiling code into other languages.</span></span> <span data-ttu-id="f2dc1-149">Blazor WebAssembly 适用于所有新式 Web 浏览器，包括移动浏览器。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-149">Blazor WebAssembly works in all modern web browsers, including mobile browsers.</span></span>

<span data-ttu-id="f2dc1-150">通过 [WebAssembly](https://webassembly.org)（缩写为 `wasm`），可在 Web 浏览器内运行 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-150">Running .NET code inside web browsers is made possible by [WebAssembly](https://webassembly.org) (abbreviated `wasm`).</span></span> <span data-ttu-id="f2dc1-151">WebAssembly 是针对快速下载和最大执行速度优化的压缩字节码格式。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-151">WebAssembly is a compact bytecode format optimized for fast download and maximum execution speed.</span></span> <span data-ttu-id="f2dc1-152">WebAssembly 是开放的 Web 标准，支持用于无插件的 Web 浏览器。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-152">WebAssembly is an open web standard and supported in web browsers without plugins.</span></span>

<span data-ttu-id="f2dc1-153">WebAssembly 代码可通过 JavaScript（称为 JavaScript 互操作性，通常简称为 JavaScript 互操作或 JS 互操作）访问浏览器的完整功能  。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-153">WebAssembly code can access the full functionality of the browser via JavaScript, called *JavaScript interoperability*, often shortened to *JavaScript interop* or *JS interop*.</span></span> <span data-ttu-id="f2dc1-154">通过浏览器中的 WebAssembly 执行的 .NET 代码在浏览器的 JavaScript 沙盒中运行，沙盒提供的保护可防御客户端计算机上的恶意操作。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-154">.NET code executed via WebAssembly in the browser runs in the browser's JavaScript sandbox with the protections that the sandbox provides against malicious actions on the client machine.</span></span>

![Blazor WebAssembly 使用 WebAssembly 在浏览器中运行 .NET 代码。](index/_static/blazor-webassembly.png)

<span data-ttu-id="f2dc1-156">当 Blazor WebAssembly 应用生成并在浏览器中运行时：</span><span class="sxs-lookup"><span data-stu-id="f2dc1-156">When a Blazor WebAssembly app is built and run in a browser:</span></span>

* <span data-ttu-id="f2dc1-157">C# 代码文件和 Razor 文件将被编译为 .NET 程序集。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-157">C# code files and Razor files are compiled into .NET assemblies.</span></span>
* <span data-ttu-id="f2dc1-158">该程序集和 [.NET 运行时](/dotnet/framework/get-started/overview)将被下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-158">The assemblies and the [.NET runtime](/dotnet/framework/get-started/overview) are downloaded to the browser.</span></span>
* <span data-ttu-id="f2dc1-159">Blazor WebAssembly 启动 .NET 运行时，并配置运行时，以为应用加载程序集。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-159">Blazor WebAssembly bootstraps the .NET runtime and configures the runtime to load the assemblies for the app.</span></span> <span data-ttu-id="f2dc1-160">Blazor WebAssembly 运行时使用 JavaScript 互操作来处理 DOM 操作和浏览器 API 调用。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-160">The Blazor WebAssembly runtime uses JavaScript interop to handle DOM manipulation and browser API calls.</span></span>

<span data-ttu-id="f2dc1-161">已发布应用的大小（其有效负载大小）是应用可用性的关键性能因素。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-161">The size of the published app, its *payload size*, is a critical performance factor for an app's useability.</span></span> <span data-ttu-id="f2dc1-162">大型应用需要相对较长的时间才能下载到浏览器，这会损害用户体验。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-162">A large app takes a relatively long time to download to a browser, which diminishes the user experience.</span></span> <span data-ttu-id="f2dc1-163">Blazor WebAssembly 优化有效负载大小，以缩短下载时间：</span><span class="sxs-lookup"><span data-stu-id="f2dc1-163">Blazor WebAssembly optimizes payload size to reduce download times:</span></span>

::: moniker range=">= aspnetcore-5.0"

* <span data-ttu-id="f2dc1-164">在[中间语言 (IL) 裁边器](xref:blazor/host-and-deploy/configure-trimmer)发布应用时，会从应用删除未使用的代码。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-164">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Trimmer](xref:blazor/host-and-deploy/configure-trimmer).</span></span>
* <span data-ttu-id="f2dc1-165">压缩 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-165">HTTP responses are compressed.</span></span>
* <span data-ttu-id="f2dc1-166">.NET 运行时和程序集缓存在浏览器中。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-166">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

* <span data-ttu-id="f2dc1-167">在[中间语言 (IL) 链接器](xref:blazor/host-and-deploy/configure-linker)发布应用时，会从应用删除未使用的代码。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-167">Unused code is stripped out of the app when it's published by the [Intermediate Language (IL) Linker](xref:blazor/host-and-deploy/configure-linker).</span></span>
* <span data-ttu-id="f2dc1-168">压缩 HTTP 响应。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-168">HTTP responses are compressed.</span></span>
* <span data-ttu-id="f2dc1-169">.NET 运行时和程序集缓存在浏览器中。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-169">The .NET runtime and assemblies are cached in the browser.</span></span>

::: moniker-end

## Blazor Server

<span data-ttu-id="f2dc1-170">Blazor 将组件呈现逻辑从 UI 更新的应用方式中分离出来。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-170">Blazor decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="f2dc1-171">Blazor Server在 ASP.NET Core 应用中支持在服务器上托管 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-171">*Blazor Server* provides support for hosting Razor components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="f2dc1-172">可通过 [SignalR](xref:signalr/introduction) 连接处理 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-172">UI updates are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

<span data-ttu-id="f2dc1-173">运行时会处理以下事件：</span><span class="sxs-lookup"><span data-stu-id="f2dc1-173">The runtime handles:</span></span>

* <span data-ttu-id="f2dc1-174">将 UI 事件从浏览器发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-174">Sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="f2dc1-175">将 UI 更新应用于服务器发送回的已呈现的组件。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-175">Applying UI updates to the rendered component that are sent back by the server.</span></span>

<span data-ttu-id="f2dc1-176">Blazor Server用于与浏览器通信的连接还用于处理 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-176">The connection used by Blazor Server to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Blazor 服务器在服务器上运行 .NET 代码，并通过 SignalR 连接与客户端上的文档对象模型进行交互](index/_static/blazor-server.png)

## <a name="javascript-interop"></a><span data-ttu-id="f2dc1-178">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="f2dc1-178">JavaScript interop</span></span>

<span data-ttu-id="f2dc1-179">对于需要第三方 JavaScript 库和访问浏览器 API 的应用，组件与 JavaScript 进行互操作。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-179">For apps that require third-party JavaScript libraries and access to browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="f2dc1-180">组件能够使用 JavaScript 能够使用的任何库或 API。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-180">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="f2dc1-181">C# 代码可[调用到 JavaScript 代码](xref:blazor/call-javascript-from-dotnet)，而 JavaScript 代码可[调用到 C# 代码](xref:blazor/call-dotnet-from-javascript)。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-181">C# code can [call into JavaScript code](xref:blazor/call-javascript-from-dotnet), and JavaScript code can [call into C# code](xref:blazor/call-dotnet-from-javascript).</span></span>

## <a name="code-sharing-and-net-standard"></a><span data-ttu-id="f2dc1-182">代码共享和 .NET Standard</span><span class="sxs-lookup"><span data-stu-id="f2dc1-182">Code sharing and .NET Standard</span></span>

<span data-ttu-id="f2dc1-183">Blazor 实现 [.NET Standard](/dotnet/standard/net-standard)，它使 Blazor 项目引用符合 .NET Standard 规范的库。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-183">Blazor implements the [.NET Standard](/dotnet/standard/net-standard), which enables Blazor projects to reference libraries that conform to .NET Standard specifications.</span></span> <span data-ttu-id="f2dc1-184">.NET Standard 是正式的 .NET API 规范，常见于 .NET 实现中。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-184">.NET Standard is a formal specification of .NET APIs that are common across .NET implementations.</span></span> <span data-ttu-id="f2dc1-185">.NET Standard 类库可以在不同 .NET 平台之间共享，例如 Blazor、.NET Framework、.NET Core、Xamarin、Mono 和 Unity。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-185">.NET Standard class libraries can be shared across different .NET platforms, such as Blazor, .NET Framework, .NET Core, Xamarin, Mono, and Unity.</span></span>

<span data-ttu-id="f2dc1-186">不适用于 Web 浏览器内部的 API（例如，访问文件系统、打开套接字和线程处理）将引发 <xref:System.PlatformNotSupportedException>。</span><span class="sxs-lookup"><span data-stu-id="f2dc1-186">APIs that aren't applicable inside of a web browser (for example, accessing the file system, opening a socket, and threading) throw a <xref:System.PlatformNotSupportedException>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="f2dc1-187">其他资源</span><span class="sxs-lookup"><span data-stu-id="f2dc1-187">Additional resources</span></span>

* [<span data-ttu-id="f2dc1-188">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="f2dc1-188">WebAssembly</span></span>](https://webassembly.org)
* <xref:blazor/hosting-models>
* <xref:tutorials/signalr-blazor-webassembly>
* <xref:blazor/call-javascript-from-dotnet>
* <xref:blazor/call-dotnet-from-javascript>
* [<span data-ttu-id="f2dc1-189">C# 指南</span><span class="sxs-lookup"><span data-stu-id="f2dc1-189">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="f2dc1-190">HTML</span><span class="sxs-lookup"><span data-stu-id="f2dc1-190">HTML</span></span>](https://www.w3.org/html/)
* <span data-ttu-id="f2dc1-191">[令人惊叹的 Blazor](https://github.com/AdrienTorris/awesome-blazor) 社区链接</span><span class="sxs-lookup"><span data-stu-id="f2dc1-191">[Awesome Blazor](https://github.com/AdrienTorris/awesome-blazor) community links</span></span>
