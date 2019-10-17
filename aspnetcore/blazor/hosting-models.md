---
title: ASP.NET Core Blazor 宿主模型
author: guardrex
description: 了解 Blazor WebAssembly 和 Blazor 服务器承载模型。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: blazor/hosting-models
ms.openlocfilehash: 072f9bbdcf7171ede63383b085f9f0f030bf1076
ms.sourcegitcommit: 35a86ce48041caaf6396b1e88b0472578ba24483
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391174"
---
# <a name="aspnet-core-blazor-hosting-models"></a><span data-ttu-id="15409-103">ASP.NET Core Blazor 宿主模型</span><span class="sxs-lookup"><span data-stu-id="15409-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="15409-104">作者： [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="15409-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="15409-105">Blazor 是一种 web 框架，旨在在基于[WebAssembly](https://webassembly.org/)的 .net 运行时（*Blazor WebAssembly*）上的浏览器中运行客户端，或在 ASP.NET Core 中的服务器端（*Blazor 服务器*）运行。</span><span class="sxs-lookup"><span data-stu-id="15409-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="15409-106">无论采用何种托管模型，应用和组件模型*都是相同*的。</span><span class="sxs-lookup"><span data-stu-id="15409-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="15409-107">若要为本文所述的宿主模型创建项目，请参阅 <xref:blazor/get-started>。</span><span class="sxs-lookup"><span data-stu-id="15409-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="blazor-webassembly"></a><span data-ttu-id="15409-108">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="15409-108">Blazor WebAssembly</span></span>

<span data-ttu-id="15409-109">Blazor 的主体托管模型在 WebAssembly 上的浏览器中运行客户端。</span><span class="sxs-lookup"><span data-stu-id="15409-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="15409-110">将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="15409-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="15409-111">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="15409-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="15409-112">UI 更新和事件处理发生在同一进程中。</span><span class="sxs-lookup"><span data-stu-id="15409-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="15409-113">应用的资产将作为静态文件部署到 web 服务器或可为客户端提供静态内容的服务。</span><span class="sxs-lookup"><span data-stu-id="15409-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor WebAssembly： Blazor 应用程序在浏览器内的 UI 线程上运行。](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="15409-115">若要使用客户端托管模型创建 Blazor 应用，请使用**Blazor WebAssembly 应用**模板（[dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)）。</span><span class="sxs-lookup"><span data-stu-id="15409-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="15409-116">选择**Blazor WebAssembly 应用程序**模板之后，你可以选择将应用配置为使用 ASP.NET Core 后端，方法是选择 " **ASP.NET Core 托管**" 复选框（"[dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)"）。</span><span class="sxs-lookup"><span data-stu-id="15409-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="15409-117">ASP.NET Core 应用程序将 Blazor 应用程序提供给客户端。</span><span class="sxs-lookup"><span data-stu-id="15409-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="15409-118">Blazor WebAssembly 应用可通过网络使用 web API 调用或[SignalR](xref:signalr/introduction)与服务器交互。</span><span class="sxs-lookup"><span data-stu-id="15409-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="15409-119">这些模板包含处理以下内容的*blazor. webassembly*脚本：</span><span class="sxs-lookup"><span data-stu-id="15409-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="15409-120">下载 .NET 运行时、应用程序和应用程序的依赖项。</span><span class="sxs-lookup"><span data-stu-id="15409-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="15409-121">用于运行应用程序的运行时初始化。</span><span class="sxs-lookup"><span data-stu-id="15409-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="15409-122">Blazor WebAssembly 托管模型具有以下几个优点：</span><span class="sxs-lookup"><span data-stu-id="15409-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="15409-123">没有 .NET 服务器端依赖项。</span><span class="sxs-lookup"><span data-stu-id="15409-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="15409-124">应用在下载到客户端之后完全正常运行。</span><span class="sxs-lookup"><span data-stu-id="15409-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="15409-125">完全利用客户端资源和功能。</span><span class="sxs-lookup"><span data-stu-id="15409-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="15409-126">工作从服务器卸载到客户端。</span><span class="sxs-lookup"><span data-stu-id="15409-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="15409-127">不需要 ASP.NET Core web 服务器来托管应用程序。</span><span class="sxs-lookup"><span data-stu-id="15409-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="15409-128">无服务器部署方案可能（例如，通过 CDN 提供应用）。</span><span class="sxs-lookup"><span data-stu-id="15409-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="15409-129">Blazor WebAssembly 托管有缺点：</span><span class="sxs-lookup"><span data-stu-id="15409-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="15409-130">应用程序限制为浏览器的功能。</span><span class="sxs-lookup"><span data-stu-id="15409-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="15409-131">需要支持的客户端硬件和软件（例如，WebAssembly 支持）。</span><span class="sxs-lookup"><span data-stu-id="15409-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="15409-132">下载大小较大，应用需要较长时间才能加载。</span><span class="sxs-lookup"><span data-stu-id="15409-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="15409-133">.NET 运行时和工具支持不太成熟。</span><span class="sxs-lookup"><span data-stu-id="15409-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="15409-134">例如， [.NET Standard](/dotnet/standard/net-standard)支持和调试中存在限制。</span><span class="sxs-lookup"><span data-stu-id="15409-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="blazor-server"></a><span data-ttu-id="15409-135">Blazor 服务器</span><span class="sxs-lookup"><span data-stu-id="15409-135">Blazor Server</span></span>

<span data-ttu-id="15409-136">使用 Blazor 服务器托管模型，可在服务器上从 ASP.NET Core 应用中执行应用。</span><span class="sxs-lookup"><span data-stu-id="15409-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="15409-137">UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。</span><span class="sxs-lookup"><span data-stu-id="15409-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![浏览器通过 SignalR 连接与服务器上 ASP.NET Core 应用程序内托管的应用程序进行交互。](hosting-models/_static/blazor-server.png)

<span data-ttu-id="15409-139">若要使用 Blazor 服务器托管模型创建 Blazor 应用，请使用 ASP.NET Core **Blazor 服务器应用**模板（[dotnet new blazorserver](/dotnet/core/tools/dotnet-new)）。</span><span class="sxs-lookup"><span data-stu-id="15409-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="15409-140">ASP.NET Core 应用托管 Blazor 服务器应用，并创建客户端连接到的 SignalR 终结点。</span><span class="sxs-lookup"><span data-stu-id="15409-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="15409-141">ASP.NET Core 应用引用要添加的应用的 @no__t 0 类：</span><span class="sxs-lookup"><span data-stu-id="15409-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="15409-142">服务器端服务。</span><span class="sxs-lookup"><span data-stu-id="15409-142">Server-side services.</span></span>
* <span data-ttu-id="15409-143">请求处理管道的应用。</span><span class="sxs-lookup"><span data-stu-id="15409-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="15409-144">*Blazor* script @ no__t-1 用于建立客户端连接。</span><span class="sxs-lookup"><span data-stu-id="15409-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="15409-145">应用负责根据需要保存和还原应用状态（例如，在网络连接丢失的情况下）。</span><span class="sxs-lookup"><span data-stu-id="15409-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="15409-146">Blazor 服务器托管模型具有以下几个优点：</span><span class="sxs-lookup"><span data-stu-id="15409-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="15409-147">下载大小明显小于 Blazor WebAssembly 应用，且应用加载速度快得多。</span><span class="sxs-lookup"><span data-stu-id="15409-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="15409-148">应用充分利用服务器功能，包括使用任何与 .NET Core 兼容的 Api。</span><span class="sxs-lookup"><span data-stu-id="15409-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="15409-149">服务器上的 .NET Core 用于运行应用程序，因此现有的 .NET 工具（如调试）可按预期方式工作。</span><span class="sxs-lookup"><span data-stu-id="15409-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="15409-150">支持瘦客户端。</span><span class="sxs-lookup"><span data-stu-id="15409-150">Thin clients are supported.</span></span> <span data-ttu-id="15409-151">例如，Blazor 服务器应用程序适用于不支持 WebAssembly 的浏览器以及资源受限设备上的浏览器。</span><span class="sxs-lookup"><span data-stu-id="15409-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="15409-152">应用程序的 .NET/C#代码库（包括应用程序的组件代码）不会提供给客户端。</span><span class="sxs-lookup"><span data-stu-id="15409-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="15409-153">Blazor 服务器托管有缺点：</span><span class="sxs-lookup"><span data-stu-id="15409-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="15409-154">通常存在较高的延迟。</span><span class="sxs-lookup"><span data-stu-id="15409-154">Higher latency usually exists.</span></span> <span data-ttu-id="15409-155">每个用户交互都涉及网络跃点。</span><span class="sxs-lookup"><span data-stu-id="15409-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="15409-156">无脱机支持。</span><span class="sxs-lookup"><span data-stu-id="15409-156">There's no offline support.</span></span> <span data-ttu-id="15409-157">如果客户端连接失败，应用将停止工作。</span><span class="sxs-lookup"><span data-stu-id="15409-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="15409-158">对于包含多个用户的应用而言，可伸缩性非常困难。</span><span class="sxs-lookup"><span data-stu-id="15409-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="15409-159">服务器必须管理多个客户端连接并处理客户端状态。</span><span class="sxs-lookup"><span data-stu-id="15409-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="15409-160">为应用提供服务需要 ASP.NET Core 服务器。</span><span class="sxs-lookup"><span data-stu-id="15409-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="15409-161">不可能的无服务器部署方案（例如，通过 CDN 为应用提供服务）。</span><span class="sxs-lookup"><span data-stu-id="15409-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="15409-162">0The *blazor*脚本是从 ASP.NET Core 共享框架中的嵌入资源提供的。 @no__t</span><span class="sxs-lookup"><span data-stu-id="15409-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="15409-163">与服务器呈现的 UI 的比较</span><span class="sxs-lookup"><span data-stu-id="15409-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="15409-164">了解 Blazor 服务器应用程序的一种方法是了解它与传统模型的不同之处在于，使用 Razor 视图或 Razor Pages 在 ASP.NET Core 应用程序中呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="15409-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="15409-165">这两种模型都使用 Razor 语言来描述 HTML 内容，但在显示标记的方式上差别很大。</span><span class="sxs-lookup"><span data-stu-id="15409-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="15409-166">在显示 Razor 页面或视图时，每行 Razor 代码都会以文本形式发出 HTML。</span><span class="sxs-lookup"><span data-stu-id="15409-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="15409-167">在呈现后，服务器将释放页面或视图实例，包括生成的任何状态。</span><span class="sxs-lookup"><span data-stu-id="15409-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="15409-168">当对该页的另一请求出现时，例如，当服务器验证失败并显示验证摘要时：</span><span class="sxs-lookup"><span data-stu-id="15409-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="15409-169">整个页面将再次重新呈现到 HTML 文本。</span><span class="sxs-lookup"><span data-stu-id="15409-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="15409-170">该页将发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="15409-170">The page is sent to the client.</span></span>

<span data-ttu-id="15409-171">Blazor 应用由 UI 的可重用元素组成，这些元素称为*组件*。</span><span class="sxs-lookup"><span data-stu-id="15409-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="15409-172">组件包含C#代码、标记和其他组件。</span><span class="sxs-lookup"><span data-stu-id="15409-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="15409-173">呈现组件时，Blazor 将生成包含的组件的图，类似于 HTML 或 XML 文档对象模型（DOM）。</span><span class="sxs-lookup"><span data-stu-id="15409-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="15409-174">此图包含属性和字段中保存的组件状态。</span><span class="sxs-lookup"><span data-stu-id="15409-174">This graph includes component state held in properties and fields.</span></span> <span data-ttu-id="15409-175">Blazor 计算组件图以生成标记的二进制表示形式。</span><span class="sxs-lookup"><span data-stu-id="15409-175">Blazor evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="15409-176">二进制格式可以是：</span><span class="sxs-lookup"><span data-stu-id="15409-176">The binary format can be:</span></span>

* <span data-ttu-id="15409-177">转换为 HTML 文本（在预呈现期间）。</span><span class="sxs-lookup"><span data-stu-id="15409-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="15409-178">用于在常规呈现期间有效地更新标记。</span><span class="sxs-lookup"><span data-stu-id="15409-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="15409-179">Blazor 中的 UI 更新由以下用户触发：</span><span class="sxs-lookup"><span data-stu-id="15409-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="15409-180">用户交互，如选择一个按钮。</span><span class="sxs-lookup"><span data-stu-id="15409-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="15409-181">应用触发器，如计时器。</span><span class="sxs-lookup"><span data-stu-id="15409-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="15409-182">关系图为重新呈现，并计算了 UI*差异*（差异）。</span><span class="sxs-lookup"><span data-stu-id="15409-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="15409-183">这种差异是更新客户端上 UI 所需的最小 DOM 编辑集。</span><span class="sxs-lookup"><span data-stu-id="15409-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="15409-184">将以二进制格式将差异发送到客户端，并由浏览器应用。</span><span class="sxs-lookup"><span data-stu-id="15409-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="15409-185">当用户在客户端上导航掉组件后，将释放该组件。</span><span class="sxs-lookup"><span data-stu-id="15409-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="15409-186">当用户与组件交互时，组件的状态（服务、资源）必须保存在服务器的内存中。</span><span class="sxs-lookup"><span data-stu-id="15409-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="15409-187">由于多个组件的状态可能同时由服务器维护，因此内存耗尽是必须解决的问题。</span><span class="sxs-lookup"><span data-stu-id="15409-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="15409-188">有关如何创作 Blazor 服务器应用以确保最大程度地使用服务器内存的指导，请参阅 <xref:security/blazor/server>。</span><span class="sxs-lookup"><span data-stu-id="15409-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="circuits"></a><span data-ttu-id="15409-189">而言</span><span class="sxs-lookup"><span data-stu-id="15409-189">Circuits</span></span>

<span data-ttu-id="15409-190">Blazor 服务器应用基于[ASP.NET Core SignalR](xref:signalr/introduction)构建。</span><span class="sxs-lookup"><span data-stu-id="15409-190">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="15409-191">每个客户端通过一个或多个称为*线路*的 SignalR 连接与服务器通信。</span><span class="sxs-lookup"><span data-stu-id="15409-191">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="15409-192">线路是 Blazor 对 SignalR 连接的抽象，可容忍临时网络中断。</span><span class="sxs-lookup"><span data-stu-id="15409-192">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="15409-193">当 Blazor 客户端发现 SignalR 连接已断开连接时，它会尝试使用新的 SignalR 连接重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="15409-193">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="15409-194">连接到 Blazor 服务器应用的每个浏览器屏幕（浏览器选项卡或 iframe）都使用 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="15409-194">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="15409-195">与典型服务器呈现的应用相比，这一点还有一个重要的区别。</span><span class="sxs-lookup"><span data-stu-id="15409-195">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="15409-196">在服务器呈现的应用程序中，在多个浏览器屏幕中打开同一应用程序通常不会转换为服务器上的其他资源需求。</span><span class="sxs-lookup"><span data-stu-id="15409-196">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="15409-197">在 Blazor 服务器应用中，每个浏览器屏幕都需要一个单独的线路，并将组件状态的实例单独置于服务器管理的状态。</span><span class="sxs-lookup"><span data-stu-id="15409-197">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

<span data-ttu-id="15409-198">Blazor 考虑关闭浏览器选项卡或导航到外部 URL，*正常*终止。</span><span class="sxs-lookup"><span data-stu-id="15409-198">Blazor considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="15409-199">如果正常终止，则会立即释放线路和关联的资源。</span><span class="sxs-lookup"><span data-stu-id="15409-199">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="15409-200">例如，由于网络中断，客户端也可能断开连接。</span><span class="sxs-lookup"><span data-stu-id="15409-200">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> <span data-ttu-id="15409-201">Blazor 服务器会将断开连接的线路存储为可配置的间隔，以允许客户端重新连接。</span><span class="sxs-lookup"><span data-stu-id="15409-201">Blazor Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="15409-202">有关详细信息，请参阅重新[连接到同一服务器](#reconnection-to-the-same-server)部分。</span><span class="sxs-lookup"><span data-stu-id="15409-202">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="15409-203">UI 延迟</span><span class="sxs-lookup"><span data-stu-id="15409-203">UI Latency</span></span>

<span data-ttu-id="15409-204">UI 延迟是指从启动的操作到 UI 更新的时间。</span><span class="sxs-lookup"><span data-stu-id="15409-204">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="15409-205">对于应用程序来说，更小的 UI 延迟值非常适合应用程序对用户的响应。</span><span class="sxs-lookup"><span data-stu-id="15409-205">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="15409-206">在 Blazor 服务器应用中，每个操作都将发送到服务器，进行处理，并向后发送 UI 差异。</span><span class="sxs-lookup"><span data-stu-id="15409-206">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="15409-207">因此，UI 延迟是网络延迟和处理操作时服务器延迟的总和。</span><span class="sxs-lookup"><span data-stu-id="15409-207">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="15409-208">对于仅限于专用公司网络的业务线应用，对用户而言，由于网络延迟导致的延迟通常是让的。</span><span class="sxs-lookup"><span data-stu-id="15409-208">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="15409-209">对于通过 Internet 部署的应用，用户可能会对延迟造成明显的影响，尤其是用户广泛分散于各地。</span><span class="sxs-lookup"><span data-stu-id="15409-209">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="15409-210">内存使用率还会导致应用延迟。</span><span class="sxs-lookup"><span data-stu-id="15409-210">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="15409-211">增加的内存使用会导致频繁垃圾收集或将内存分页到磁盘，这两者都会降低应用程序性能，进而增加 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="15409-211">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="15409-212">有关更多信息，请参见<xref:security/blazor/server>。</span><span class="sxs-lookup"><span data-stu-id="15409-212">For more information, see <xref:security/blazor/server>.</span></span>

<span data-ttu-id="15409-213">应通过减少网络延迟和内存使用来优化 Blazor 服务器应用，从而最大限度地减少 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="15409-213">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="15409-214">有关测量网络延迟的方法，请参阅 <xref:host-and-deploy/blazor/server#measure-network-latency>。</span><span class="sxs-lookup"><span data-stu-id="15409-214">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="15409-215">有关 SignalR 和 Blazor 的详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="15409-215">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="15409-216">重新连接到同一台服务器</span><span class="sxs-lookup"><span data-stu-id="15409-216">Reconnection to the same server</span></span>

<span data-ttu-id="15409-217">Blazor 服务器应用需要与服务器建立活动的 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="15409-217">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="15409-218">如果连接丢失，应用会尝试重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="15409-218">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="15409-219">只要客户端的状态仍在内存中，客户端会话便会恢复而不会失去状态。</span><span class="sxs-lookup"><span data-stu-id="15409-219">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

<span data-ttu-id="15409-220">当客户端检测到连接已丢失时，用户会在客户端尝试重新连接时向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="15409-220">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="15409-221">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="15409-221">If reconnection fails, the user is provided the option to retry.</span></span> <span data-ttu-id="15409-222">若要自定义 UI，请在 *_Host* Razor 页面中定义一个元素，其 @no__t 为 @no__t，其为-1。</span><span class="sxs-lookup"><span data-stu-id="15409-222">To customize the UI, define an element with `components-reconnect-modal` as its `id` in the *_Host.cshtml* Razor page.</span></span> <span data-ttu-id="15409-223">客户端根据连接状态将此元素更新为下面的一个 CSS 类：</span><span class="sxs-lookup"><span data-stu-id="15409-223">The client updates this element with one of the following CSS classes based on the state of the connection:</span></span>

* <span data-ttu-id="15409-224">`components-reconnect-show` &ndash; 显示 UI 以指示断开连接，并且客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="15409-224">`components-reconnect-show` &ndash; Show the UI to indicate a lost connection and the client is attempting to reconnect.</span></span>
* <span data-ttu-id="15409-225">`components-reconnect-hide` &ndash;：客户端具有活动的连接，隐藏 UI。</span><span class="sxs-lookup"><span data-stu-id="15409-225">`components-reconnect-hide` &ndash; The client has an active connection, hide the UI.</span></span>
* <span data-ttu-id="15409-226">`components-reconnect-failed` &ndash; 重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="15409-226">`components-reconnect-failed` &ndash; Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="15409-227">若要尝试重新连接，请调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="15409-227">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span>
* <span data-ttu-id="15409-228">`components-reconnect-rejected` &ndash; 重新连接被拒绝。</span><span class="sxs-lookup"><span data-stu-id="15409-228">`components-reconnect-rejected` &ndash; Reconnection rejected.</span></span> <span data-ttu-id="15409-229">服务器已达到但拒绝了连接，但该服务器上的用户状态已断开。</span><span class="sxs-lookup"><span data-stu-id="15409-229">The server was reached but refused the connection, and the user's state on the server is gone.</span></span> <span data-ttu-id="15409-230">若要重新加载应用，请调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="15409-230">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="15409-231">当以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="15409-231">This connection state may result when:</span></span>
  * <span data-ttu-id="15409-232">线路（服务器端代码）发生崩溃。</span><span class="sxs-lookup"><span data-stu-id="15409-232">A crash in the circuit (server-side code) occurs.</span></span>
  * <span data-ttu-id="15409-233">断开客户端的连接时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="15409-233">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="15409-234">已释放用户与之交互的组件的实例。</span><span class="sxs-lookup"><span data-stu-id="15409-234">Instances of components that the user was interacting with are disposed.</span></span>

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="15409-235">预呈现后有状态重新连接</span><span class="sxs-lookup"><span data-stu-id="15409-235">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="15409-236">默认情况下，Blazor 服务器应用程序设置为在客户端与服务器建立连接之前，在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="15409-236">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="15409-237">这是在 *_Host* Razor 页面中设置的：</span><span class="sxs-lookup"><span data-stu-id="15409-237">This is set up in the *_Host.cshtml* Razor page:</span></span>

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

<span data-ttu-id="15409-238">`RenderMode` 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="15409-238">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="15409-239">已预呈现到页面中。</span><span class="sxs-lookup"><span data-stu-id="15409-239">Is prerendered into the page.</span></span>
* <span data-ttu-id="15409-240">在页面上呈现为静态 HTML，或者，如果包含从用户代理启动 Blazor 应用所需的信息，则为。</span><span class="sxs-lookup"><span data-stu-id="15409-240">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

| `RenderMode`        | <span data-ttu-id="15409-241">描述</span><span class="sxs-lookup"><span data-stu-id="15409-241">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="15409-242">将组件呈现为静态 HTML，并包含 Blazor 服务器应用的标记。</span><span class="sxs-lookup"><span data-stu-id="15409-242">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="15409-243">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="15409-243">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="15409-244">不支持参数。</span><span class="sxs-lookup"><span data-stu-id="15409-244">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="15409-245">呈现 Blazor 服务器应用程序的标记。</span><span class="sxs-lookup"><span data-stu-id="15409-245">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="15409-246">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="15409-246">Output from the component isn't included.</span></span> <span data-ttu-id="15409-247">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="15409-247">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="15409-248">不支持参数。</span><span class="sxs-lookup"><span data-stu-id="15409-248">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="15409-249">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="15409-249">Renders the component into static HTML.</span></span> <span data-ttu-id="15409-250">支持参数。</span><span class="sxs-lookup"><span data-stu-id="15409-250">Parameters are supported.</span></span> |

<span data-ttu-id="15409-251">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="15409-251">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="15409-252">客户端重新连接到服务器，该服务器具有用于预呈现应用的相同状态。</span><span class="sxs-lookup"><span data-stu-id="15409-252">The client reconnects to the server with the same state that was used to prerender the app.</span></span> <span data-ttu-id="15409-253">如果应用的状态仍在内存中，则在建立 SignalR 连接后，组件状态将不重新呈现。</span><span class="sxs-lookup"><span data-stu-id="15409-253">If the app's state is still in memory, the component state isn't rerendered after the SignalR connection is established.</span></span>

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="15409-254">从 Razor 页面和视图呈现有状态交互式组件</span><span class="sxs-lookup"><span data-stu-id="15409-254">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="15409-255">可以将有状态交互组件添加到 Razor 页面或视图。</span><span class="sxs-lookup"><span data-stu-id="15409-255">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="15409-256">呈现页面或视图时：</span><span class="sxs-lookup"><span data-stu-id="15409-256">When the page or view renders:</span></span>

* <span data-ttu-id="15409-257">该组件与页面或视图预呈现。</span><span class="sxs-lookup"><span data-stu-id="15409-257">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="15409-258">用于预呈现的初始组件状态将丢失。</span><span class="sxs-lookup"><span data-stu-id="15409-258">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="15409-259">建立 SignalR 连接时，将创建新的组件状态。</span><span class="sxs-lookup"><span data-stu-id="15409-259">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="15409-260">以下 Razor 页面将呈现一个 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="15409-260">The following Razor page renders a `Counter` component:</span></span>

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))
```

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="15409-261">从 Razor 页面和视图呈现非交互式组件</span><span class="sxs-lookup"><span data-stu-id="15409-261">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="15409-262">在以下 Razor 页面中，使用以下格式通过指定的初始值静态呈现 `MyComponent` 组件：</span><span class="sxs-lookup"><span data-stu-id="15409-262">In the following Razor page, the `MyComponent` component is statically rendered with an initial value that's specified using a form:</span></span>

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<MyComponent>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

<span data-ttu-id="15409-263">由于 @no__t 是静态呈现的，因此该组件不能是交互式的。</span><span class="sxs-lookup"><span data-stu-id="15409-263">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="15409-264">检测预呈现应用的时间</span><span class="sxs-lookup"><span data-stu-id="15409-264">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-signalr-client-for-blazor-server-apps"></a><span data-ttu-id="15409-265">为 Blazor 服务器应用配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="15409-265">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="15409-266">有时，你需要配置 Blazor 服务器应用使用的 SignalR 客户端。</span><span class="sxs-lookup"><span data-stu-id="15409-266">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="15409-267">例如，你可能想要在 SignalR 客户端上配置日志记录以诊断连接问题。</span><span class="sxs-lookup"><span data-stu-id="15409-267">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="15409-268">若要在*Pages/_Host*文件中配置 SignalR 客户端：</span><span class="sxs-lookup"><span data-stu-id="15409-268">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="15409-269">将 `autostart="false"` 特性添加到*blazor*脚本的 @no__t 标记中。</span><span class="sxs-lookup"><span data-stu-id="15409-269">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="15409-270">调用 `Blazor.start` 并传入指定 SignalR 生成器的配置对象。</span><span class="sxs-lookup"><span data-stu-id="15409-270">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging(2); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="15409-271">其他资源</span><span class="sxs-lookup"><span data-stu-id="15409-271">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
