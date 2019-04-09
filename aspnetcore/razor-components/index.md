---
title: ASP.NET Core 中的 Razor 组件简介
author: guardrex
description: 了解 ASP.NET Core Razor 组件，用户可以借助它在 ASP.NET Core 应用中使用 .NET 生成交互式客户端 Web UI。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: seoapril2019
ms.date: 03/27/2019
uid: razor-components/index
ms.openlocfilehash: 43d5cf1d752b66a531c8d974deeb5a5fc8e94b43
ms.sourcegitcommit: 1a7000630e55da90da19b284e1b2f2f13a393d74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/04/2019
ms.locfileid: "59012651"
---
# <a name="introduction-to-razor-components"></a><span data-ttu-id="d3b04-103">Razor 组件简介</span><span class="sxs-lookup"><span data-stu-id="d3b04-103">Introduction to Razor Components</span></span>

<span data-ttu-id="d3b04-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="d3b04-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="d3b04-105">ASP.NET Core 3.0（预览版）中引入的 Razor 组件可用于使用 .NET 生成交互式客户端 Web UI：</span><span class="sxs-lookup"><span data-stu-id="d3b04-105">*Razor Components*, introduced in ASP.NET Core 3.0 (Preview), is a way to build interactive client-side web UI with .NET:</span></span>

* <span data-ttu-id="d3b04-106">使用 C# 代替 JavaScript 来生成丰富的交互式 UI。</span><span class="sxs-lookup"><span data-stu-id="d3b04-106">Build rich interactive UIs using C# instead of JavaScript.</span></span>
* <span data-ttu-id="d3b04-107">共享全部使用 .NET 编写的服务器端和客户端应用逻辑。</span><span class="sxs-lookup"><span data-stu-id="d3b04-107">Share server-side and client-side app logic all written with .NET.</span></span>
* <span data-ttu-id="d3b04-108">将 UI 呈现为 HTML 和 CSS，以支持众多浏览器，其中包括移动浏览器。</span><span class="sxs-lookup"><span data-stu-id="d3b04-108">Render the UI as HTML and CSS for wide browser support, including mobile browsers.</span></span>

<span data-ttu-id="d3b04-109">Razor 组件支持大多数应用所需的核心内容，包括：</span><span class="sxs-lookup"><span data-stu-id="d3b04-109">Razor Components supports core facilities required by most apps, including:</span></span>

* <span data-ttu-id="d3b04-110">参数</span><span class="sxs-lookup"><span data-stu-id="d3b04-110">Parameters</span></span>
* <span data-ttu-id="d3b04-111">事件处理</span><span class="sxs-lookup"><span data-stu-id="d3b04-111">Event handling</span></span>
* <span data-ttu-id="d3b04-112">数据绑定</span><span class="sxs-lookup"><span data-stu-id="d3b04-112">Data binding</span></span>
* <span data-ttu-id="d3b04-113">路由</span><span class="sxs-lookup"><span data-stu-id="d3b04-113">Routing</span></span>
* <span data-ttu-id="d3b04-114">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="d3b04-114">Dependency injection</span></span>
* <span data-ttu-id="d3b04-115">布局</span><span class="sxs-lookup"><span data-stu-id="d3b04-115">Layouts</span></span>
* <span data-ttu-id="d3b04-116">模板化</span><span class="sxs-lookup"><span data-stu-id="d3b04-116">Templating</span></span>
* <span data-ttu-id="d3b04-117">级联值</span><span class="sxs-lookup"><span data-stu-id="d3b04-117">Cascading values</span></span>

<span data-ttu-id="d3b04-118">Razor 组件将组件呈现逻辑从 UI 更新的应用方式中分离出来。</span><span class="sxs-lookup"><span data-stu-id="d3b04-118">Razor Components decouples component rendering logic from how UI updates are applied.</span></span> <span data-ttu-id="d3b04-119">.NET Core 3.0 中的 ASP.NET Core Razor 组件在 ASP.NET Core 应用中添加了对在服务器上托管 Razor 组件的支持。</span><span class="sxs-lookup"><span data-stu-id="d3b04-119">ASP.NET Core Razor Components in .NET Core 3.0 adds support for hosting Razor Components on the server in an ASP.NET Core app.</span></span> <span data-ttu-id="d3b04-120">可通过 SignalR 连接处理 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="d3b04-120">UI updates are handled over a SignalR connection.</span></span>

<span data-ttu-id="d3b04-121">运行时执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d3b04-121">The runtime:</span></span>

* <span data-ttu-id="d3b04-122">处理从浏览器到服务器的发送 UI 事件。</span><span class="sxs-lookup"><span data-stu-id="d3b04-122">Handles sending UI events from the browser to the server.</span></span>
* <span data-ttu-id="d3b04-123">运行组件后，将服务器发送的 UI 更新重新应用到浏览器。</span><span class="sxs-lookup"><span data-stu-id="d3b04-123">Applies UI updates sent by the server back to the browser after running the components.</span></span>

<span data-ttu-id="d3b04-124">Razor 组件用于与浏览器通信的连接还用于处理 JavaScript 互操作调用。</span><span class="sxs-lookup"><span data-stu-id="d3b04-124">The connection used by Razor Components to communicate with the browser is also used to handle JavaScript interop calls.</span></span>

![Razor 组件在服务器上运行 .NET 代码，并通过 SignalR 连接与客户端上的文档对象模型进行交互](index/_static/aspnet-core-razor-components.png)

<span data-ttu-id="d3b04-126">有关更多信息，请参见<xref:razor-components/hosting-models#server-side-hosting-model>。</span><span class="sxs-lookup"><span data-stu-id="d3b04-126">For more information, see <xref:razor-components/hosting-models#server-side-hosting-model>.</span></span>

<span data-ttu-id="d3b04-127">Blazor 是 Razor 组件的实验性客户端托管模型。</span><span class="sxs-lookup"><span data-stu-id="d3b04-127">*Blazor* is the experimental client-side hosting model of Razor Components.</span></span> <span data-ttu-id="d3b04-128">Blazor 在使用开放 Web 标准的浏览器中的 .NET 上运行，没有插件，也没有代码转译。</span><span class="sxs-lookup"><span data-stu-id="d3b04-128">Blazor runs on .NET in the browser using open web standards without plugins or code transpilation.</span></span> <span data-ttu-id="d3b04-129">有关详细信息，请参阅 <xref:spa/blazor/index> 和 <xref:razor-components/hosting-models#client-side-hosting-model>。</span><span class="sxs-lookup"><span data-stu-id="d3b04-129">For more information, see <xref:spa/blazor/index> and <xref:razor-components/hosting-models#client-side-hosting-model>.</span></span>

## <a name="components"></a><span data-ttu-id="d3b04-130">组件数</span><span class="sxs-lookup"><span data-stu-id="d3b04-130">Components</span></span>

<span data-ttu-id="d3b04-131">Razor 组件是 UI（例如，页面、对话框或数据输入窗体）的一部分。</span><span class="sxs-lookup"><span data-stu-id="d3b04-131">A *Razor Component* is a piece of UI, such as a page, dialog, or data entry form.</span></span> <span data-ttu-id="d3b04-132">组件处理用户事件，并定义灵活的 UI 呈现逻辑。</span><span class="sxs-lookup"><span data-stu-id="d3b04-132">Components handle user events and define flexible UI rendering logic.</span></span> <span data-ttu-id="d3b04-133">组件可以嵌套和重用。</span><span class="sxs-lookup"><span data-stu-id="d3b04-133">Components can be nested and reused.</span></span>

<span data-ttu-id="d3b04-134">组件是内置于 .NET 程序集的 .NET 类，可以作为 NuGet 包进行共享和分发。</span><span class="sxs-lookup"><span data-stu-id="d3b04-134">Components are .NET classes built into .NET assemblies that can be shared and distributed as NuGet packages.</span></span> <span data-ttu-id="d3b04-135">类通常以 Razor 标记页（文件扩展名为 .razor）的形式编写。</span><span class="sxs-lookup"><span data-stu-id="d3b04-135">The class is normally written in the form of a Razor markup page with a *.razor* file extension.</span></span>

<span data-ttu-id="d3b04-136">[Razor](xref:mvc/views/razor) 是用于将 HTML 标记与 C# 代码结合在一起的语法。</span><span class="sxs-lookup"><span data-stu-id="d3b04-136">[Razor](xref:mvc/views/razor) is a syntax for combining HTML markup with C# code.</span></span> <span data-ttu-id="d3b04-137">Razor 为提高开发人员工作效率而设计，允许开发人员通过 [IntelliSense](/visualstudio/ide/using-intellisense) 支持在相同文件中的标记和 C# 之间切换。</span><span class="sxs-lookup"><span data-stu-id="d3b04-137">Razor is designed for developer productivity, allowing the developer to switch between markup and C# in the same file with [IntelliSense](/visualstudio/ide/using-intellisense) support.</span></span> <span data-ttu-id="d3b04-138">Razor Pages 和 MVC 视图也使用 Razor。</span><span class="sxs-lookup"><span data-stu-id="d3b04-138">Razor Pages and MVC views also use Razor.</span></span> <span data-ttu-id="d3b04-139">与围绕请求/响应模型生成的 Razor Pages 和 MVC 视图不同，组件专门用于处理 UI 构成。</span><span class="sxs-lookup"><span data-stu-id="d3b04-139">Unlike Razor Pages and MVC views, which are built around a request/response model, components are used specifically for handling UI composition.</span></span> <span data-ttu-id="d3b04-140">Razor 组件可专门用于客户端 UI 逻辑和构成。</span><span class="sxs-lookup"><span data-stu-id="d3b04-140">Razor Components can be used specifically for client-side UI logic and composition.</span></span>

<span data-ttu-id="d3b04-141">下面的标记示例展示了 Razor 文件 (DialogComponent.razor) 中的自定义对话框组件：</span><span class="sxs-lookup"><span data-stu-id="d3b04-141">The following markup is an example of a custom dialog component in a Razor file (*DialogComponent.razor*):</span></span>

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

<span data-ttu-id="d3b04-142">如果在应用的其他位置使用此组件，IntelliSense 可加快使用语法和参数补全的开发。</span><span class="sxs-lookup"><span data-stu-id="d3b04-142">When this component is used elsewhere in the app, IntelliSense speeds development with syntax and parameter completion.</span></span>

<span data-ttu-id="d3b04-143">组件呈现为浏览器 DOM 的内存中表现形式，称为“呈现树”，然后可以使用它以灵活高效的方式更新 UI。</span><span class="sxs-lookup"><span data-stu-id="d3b04-143">Components render into an in-memory representation of the browser DOM called a *render tree* that can then be used to update the UI in a flexible and efficient way.</span></span>

## <a name="javascript-interop"></a><span data-ttu-id="d3b04-144">JavaScript 互操作</span><span class="sxs-lookup"><span data-stu-id="d3b04-144">JavaScript interop</span></span>

<span data-ttu-id="d3b04-145">对于需要第三方 JavaScript 库和浏览器 API 的应用，组件与 JavaScript 进行互操作。</span><span class="sxs-lookup"><span data-stu-id="d3b04-145">For apps that require third-party JavaScript libraries and browser APIs, components interoperate with JavaScript.</span></span> <span data-ttu-id="d3b04-146">组件能够使用 JavaScript 能够使用的任何库或 API。</span><span class="sxs-lookup"><span data-stu-id="d3b04-146">Components are capable of using any library or API that JavaScript is able to use.</span></span> <span data-ttu-id="d3b04-147">C# 代码可以调用到 JavaScript 代码，而 JavaScript 代码可以调用到 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="d3b04-147">C# code can call into JavaScript code, and JavaScript code can call into C# code.</span></span> <span data-ttu-id="d3b04-148">有关详细信息，请参阅 [JavaScript 互操作](xref:razor-components/javascript-interop)。</span><span class="sxs-lookup"><span data-stu-id="d3b04-148">For more information, see [JavaScript interop](xref:razor-components/javascript-interop).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d3b04-149">其他资源</span><span class="sxs-lookup"><span data-stu-id="d3b04-149">Additional resources</span></span>

* <xref:spa/blazor/index>
* [<span data-ttu-id="d3b04-150">WebAssembly</span><span class="sxs-lookup"><span data-stu-id="d3b04-150">WebAssembly</span></span>](http://webassembly.org/)
* [<span data-ttu-id="d3b04-151">C# 指南</span><span class="sxs-lookup"><span data-stu-id="d3b04-151">C# Guide</span></span>](/dotnet/csharp/)
* <xref:mvc/views/razor>
* [<span data-ttu-id="d3b04-152">HTML</span><span class="sxs-lookup"><span data-stu-id="d3b04-152">HTML</span></span>](https://www.w3.org/html/)
