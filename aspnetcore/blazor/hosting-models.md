---
title: ASP.NET Core Blazor 宿主模型
author: guardrex
description: 了解 Blazor WebAssembly 和 Blazor 服务器托管模型。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- SignalR
uid: blazor/hosting-models
ms.openlocfilehash: 7676d16bddf146ea38619ed35c5e32c5bce731de
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943754"
---
# <a name="aspnet-core-opno-locblazor-hosting-models"></a><span data-ttu-id="e773b-103">ASP.NET Core Blazor 宿主模型</span><span class="sxs-lookup"><span data-stu-id="e773b-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="e773b-104">作者： [Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e773b-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor<span data-ttu-id="e773b-105"> 是一种 web 框架，旨在在基于[WebAssembly](https://webassembly.org/)的 .net 运行时（ *Blazor WebAssembly*）上的浏览器中运行客户端，或者在 ASP.NET Core （ *Blazor 服务器*）中运行服务器端。</span><span class="sxs-lookup"><span data-stu-id="e773b-105"> is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="e773b-106">无论采用何种托管模型，应用和组件模型*都是相同*的。</span><span class="sxs-lookup"><span data-stu-id="e773b-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

<span data-ttu-id="e773b-107">若要为本文中所述的托管模型创建项目，请参阅 <xref:blazor/get-started>。</span><span class="sxs-lookup"><span data-stu-id="e773b-107">To create a project for the hosting models described in this article, see <xref:blazor/get-started>.</span></span>

## <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="e773b-108"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e773b-108"> WebAssembly</span></span>

<span data-ttu-id="e773b-109">Blazor 的主体宿主模型在 WebAssembly 上的浏览器中运行客户端。</span><span class="sxs-lookup"><span data-stu-id="e773b-109">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="e773b-110">将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="e773b-110">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="e773b-111">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="e773b-111">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="e773b-112">UI 更新和事件处理发生在同一进程中。</span><span class="sxs-lookup"><span data-stu-id="e773b-112">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="e773b-113">应用的资产将作为静态文件部署到 web 服务器或可为客户端提供静态内容的服务。</span><span class="sxs-lookup"><span data-stu-id="e773b-113">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![[!基金.非 LOC （Blazor）]<span data-ttu-id="e773b-114"> WebAssembly： [！基金.无 LOC （Blazor）] 应用在浏览器内的 UI 线程上运行。</span><span class="sxs-lookup"><span data-stu-id="e773b-114"> WebAssembly: The Blazor app runs on a UI thread inside the browser.</span></span>](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="e773b-115">若要使用客户端托管模型创建 Blazor 应用，请使用 **Blazor WebAssembly 应用**模板（[dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)）。</span><span class="sxs-lookup"><span data-stu-id="e773b-115">To create a Blazor app using the client-side hosting model, use the **Blazor WebAssembly App** template ([dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)).</span></span>

<span data-ttu-id="e773b-116">选择 **Blazor WebAssembly 应用程序**模板之后，你可以选择将应用配置为使用 ASP.NET Core 后端，方法是选中 " **ASP.NET Core 托管**" 复选框（"[dotnet new blazorwasm](/dotnet/core/tools/dotnet-new)"）。</span><span class="sxs-lookup"><span data-stu-id="e773b-116">After selecting the **Blazor WebAssembly App** template, you have the option of configuring the app to use an ASP.NET Core backend by selecting the **ASP.NET Core hosted** check box ([dotnet new blazorwasm --hosted](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="e773b-117">ASP.NET Core 应用将 Blazor 应用程序提供给客户端。</span><span class="sxs-lookup"><span data-stu-id="e773b-117">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="e773b-118">Blazor WebAssembly 应用可通过网络使用 web API 调用或[SignalR](xref:signalr/introduction)与服务器交互。</span><span class="sxs-lookup"><span data-stu-id="e773b-118">The Blazor WebAssembly app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="e773b-119">这些模板包含处理以下内容的*blazor. webassembly*脚本：</span><span class="sxs-lookup"><span data-stu-id="e773b-119">The templates include the *blazor.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="e773b-120">下载 .NET 运行时、应用程序和应用程序的依赖项。</span><span class="sxs-lookup"><span data-stu-id="e773b-120">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="e773b-121">用于运行应用程序的运行时初始化。</span><span class="sxs-lookup"><span data-stu-id="e773b-121">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="e773b-122">Blazor WebAssembly 宿主模型具有以下几个优点：</span><span class="sxs-lookup"><span data-stu-id="e773b-122">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="e773b-123">没有 .NET 服务器端依赖项。</span><span class="sxs-lookup"><span data-stu-id="e773b-123">There's no .NET server-side dependency.</span></span> <span data-ttu-id="e773b-124">应用在下载到客户端之后完全正常运行。</span><span class="sxs-lookup"><span data-stu-id="e773b-124">The app is fully functioning after downloaded to the client.</span></span>
* <span data-ttu-id="e773b-125">完全利用客户端资源和功能。</span><span class="sxs-lookup"><span data-stu-id="e773b-125">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="e773b-126">工作从服务器卸载到客户端。</span><span class="sxs-lookup"><span data-stu-id="e773b-126">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="e773b-127">不需要 ASP.NET Core web 服务器来托管应用程序。</span><span class="sxs-lookup"><span data-stu-id="e773b-127">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="e773b-128">无服务器部署方案可能（例如，通过 CDN 提供应用）。</span><span class="sxs-lookup"><span data-stu-id="e773b-128">Serverless deployment scenarios are possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="e773b-129">Blazor WebAssembly 托管有一些缺点：</span><span class="sxs-lookup"><span data-stu-id="e773b-129">There are downsides to Blazor WebAssembly hosting:</span></span>

* <span data-ttu-id="e773b-130">应用程序限制为浏览器的功能。</span><span class="sxs-lookup"><span data-stu-id="e773b-130">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="e773b-131">需要支持的客户端硬件和软件（例如，WebAssembly 支持）。</span><span class="sxs-lookup"><span data-stu-id="e773b-131">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="e773b-132">下载大小较大，应用需要较长时间才能加载。</span><span class="sxs-lookup"><span data-stu-id="e773b-132">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="e773b-133">.NET 运行时和工具支持不太成熟。</span><span class="sxs-lookup"><span data-stu-id="e773b-133">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="e773b-134">例如， [.NET Standard](/dotnet/standard/net-standard)支持和调试中存在限制。</span><span class="sxs-lookup"><span data-stu-id="e773b-134">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

## <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="e773b-135"> 服务器</span><span class="sxs-lookup"><span data-stu-id="e773b-135"> Server</span></span>

<span data-ttu-id="e773b-136">使用 Blazor 服务器托管模型，可在服务器上从 ASP.NET Core 应用中执行应用。</span><span class="sxs-lookup"><span data-stu-id="e773b-136">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="e773b-137">UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。</span><span class="sxs-lookup"><span data-stu-id="e773b-137">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![浏览器与应用程序（托管在 ASP.NET Core 应用内的应用程序）通过 [！基金.无 LOC （SignalR）] 连接。](hosting-models/_static/blazor-server.png)

<span data-ttu-id="e773b-139">若要使用 Blazor 服务器托管模型创建 Blazor 应用，请使用 ASP.NET Core **Blazor 服务器应用**模板（[dotnet new blazorserver](/dotnet/core/tools/dotnet-new)）。</span><span class="sxs-lookup"><span data-stu-id="e773b-139">To create a Blazor app using the Blazor Server hosting model, use the ASP.NET Core **Blazor Server App** template ([dotnet new blazorserver](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="e773b-140">ASP.NET Core 应用承载 Blazor 服务器应用，并创建客户端连接到 SignalR 终结点。</span><span class="sxs-lookup"><span data-stu-id="e773b-140">The ASP.NET Core app hosts the Blazor Server app and creates the SignalR endpoint where clients connect.</span></span>

<span data-ttu-id="e773b-141">ASP.NET Core 应用引用要添加的应用 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="e773b-141">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="e773b-142">服务器端服务。</span><span class="sxs-lookup"><span data-stu-id="e773b-142">Server-side services.</span></span>
* <span data-ttu-id="e773b-143">请求处理管道的应用。</span><span class="sxs-lookup"><span data-stu-id="e773b-143">The app to the request handling pipeline.</span></span>

<span data-ttu-id="e773b-144">*Blazor*脚本&dagger; 建立客户端连接。</span><span class="sxs-lookup"><span data-stu-id="e773b-144">The *blazor.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="e773b-145">应用负责根据需要保存和还原应用状态（例如，在网络连接丢失的情况下）。</span><span class="sxs-lookup"><span data-stu-id="e773b-145">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="e773b-146">Blazor Server 宿主模型具有以下几个优点：</span><span class="sxs-lookup"><span data-stu-id="e773b-146">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="e773b-147">下载大小明显小于 Blazor WebAssembly 应用，且应用加载速度快得多。</span><span class="sxs-lookup"><span data-stu-id="e773b-147">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="e773b-148">应用充分利用服务器功能，包括使用任何与 .NET Core 兼容的 Api。</span><span class="sxs-lookup"><span data-stu-id="e773b-148">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="e773b-149">服务器上的 .NET Core 用于运行应用程序，因此现有的 .NET 工具（如调试）可按预期方式工作。</span><span class="sxs-lookup"><span data-stu-id="e773b-149">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="e773b-150">支持瘦客户端。</span><span class="sxs-lookup"><span data-stu-id="e773b-150">Thin clients are supported.</span></span> <span data-ttu-id="e773b-151">例如，Blazor Server apps 适用于不支持 WebAssembly 的浏览器以及资源受限设备上的浏览器。</span><span class="sxs-lookup"><span data-stu-id="e773b-151">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="e773b-152">应用程序的 .NET/C#代码库（包括应用程序的组件代码）不会提供给客户端。</span><span class="sxs-lookup"><span data-stu-id="e773b-152">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="e773b-153">Blazor Server 宿主有一些缺点：</span><span class="sxs-lookup"><span data-stu-id="e773b-153">There are downsides to Blazor Server hosting:</span></span>

* <span data-ttu-id="e773b-154">通常存在较高的延迟。</span><span class="sxs-lookup"><span data-stu-id="e773b-154">Higher latency usually exists.</span></span> <span data-ttu-id="e773b-155">每个用户交互都涉及网络跃点。</span><span class="sxs-lookup"><span data-stu-id="e773b-155">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="e773b-156">无脱机支持。</span><span class="sxs-lookup"><span data-stu-id="e773b-156">There's no offline support.</span></span> <span data-ttu-id="e773b-157">如果客户端连接失败，应用将停止工作。</span><span class="sxs-lookup"><span data-stu-id="e773b-157">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="e773b-158">对于包含多个用户的应用而言，可伸缩性非常困难。</span><span class="sxs-lookup"><span data-stu-id="e773b-158">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="e773b-159">服务器必须管理多个客户端连接并处理客户端状态。</span><span class="sxs-lookup"><span data-stu-id="e773b-159">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="e773b-160">为应用提供服务需要 ASP.NET Core 服务器。</span><span class="sxs-lookup"><span data-stu-id="e773b-160">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="e773b-161">不可能的无服务器部署方案（例如，通过 CDN 为应用提供服务）。</span><span class="sxs-lookup"><span data-stu-id="e773b-161">Serverless deployment scenarios aren't possible (for example, serving the app from a CDN).</span></span>

<span data-ttu-id="e773b-162">&dagger;*blazor*脚本是从 ASP.NET Core 共享框架中的嵌入资源提供的。</span><span class="sxs-lookup"><span data-stu-id="e773b-162">&dagger;The *blazor.server.js* script is served from an embedded resource in the ASP.NET Core shared framework.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="e773b-163">与服务器呈现的 UI 的比较</span><span class="sxs-lookup"><span data-stu-id="e773b-163">Comparison to server-rendered UI</span></span>

<span data-ttu-id="e773b-164">了解 Blazor Server 应用程序的一种方法是，了解它与传统模型的不同之处在于，使用 Razor 视图或 Razor Pages 在 ASP.NET Core 应用程序中呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="e773b-164">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="e773b-165">这两种模型都使用 Razor 语言来描述 HTML 内容，但在显示标记的方式上差别很大。</span><span class="sxs-lookup"><span data-stu-id="e773b-165">Both models use the Razor language to describe HTML content, but they significantly differ in how markup is rendered.</span></span>

<span data-ttu-id="e773b-166">在显示 Razor 页面或视图时，每行 Razor 代码都会以文本形式发出 HTML。</span><span class="sxs-lookup"><span data-stu-id="e773b-166">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="e773b-167">在呈现后，服务器将释放页面或视图实例，包括生成的任何状态。</span><span class="sxs-lookup"><span data-stu-id="e773b-167">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="e773b-168">当对该页的另一请求出现时，例如，当服务器验证失败并显示验证摘要时：</span><span class="sxs-lookup"><span data-stu-id="e773b-168">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="e773b-169">整个页面将再次重新呈现到 HTML 文本。</span><span class="sxs-lookup"><span data-stu-id="e773b-169">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="e773b-170">该页将发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="e773b-170">The page is sent to the client.</span></span>

<span data-ttu-id="e773b-171">Blazor 应用由 UI 的可重用元素组成，这些元素称为*组件*。</span><span class="sxs-lookup"><span data-stu-id="e773b-171">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="e773b-172">组件包含C#代码、标记和其他组件。</span><span class="sxs-lookup"><span data-stu-id="e773b-172">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="e773b-173">呈现组件时，Blazor 生成包含的组件的图，类似于 HTML 或 XML 文档对象模型（DOM）。</span><span class="sxs-lookup"><span data-stu-id="e773b-173">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="e773b-174">此图包含属性和字段中保存的组件状态。</span><span class="sxs-lookup"><span data-stu-id="e773b-174">This graph includes component state held in properties and fields.</span></span> Blazor<span data-ttu-id="e773b-175"> 计算组件图以生成标记的二进制表示形式。</span><span class="sxs-lookup"><span data-stu-id="e773b-175"> evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="e773b-176">二进制格式可以是：</span><span class="sxs-lookup"><span data-stu-id="e773b-176">The binary format can be:</span></span>

* <span data-ttu-id="e773b-177">转换为 HTML 文本（在预呈现期间）。</span><span class="sxs-lookup"><span data-stu-id="e773b-177">Turned into HTML text (during prerendering).</span></span>
* <span data-ttu-id="e773b-178">用于在常规呈现期间有效地更新标记。</span><span class="sxs-lookup"><span data-stu-id="e773b-178">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="e773b-179">Blazor 中的 UI 更新由以下用户触发：</span><span class="sxs-lookup"><span data-stu-id="e773b-179">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="e773b-180">用户交互，如选择一个按钮。</span><span class="sxs-lookup"><span data-stu-id="e773b-180">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="e773b-181">应用触发器，如计时器。</span><span class="sxs-lookup"><span data-stu-id="e773b-181">App triggers, such as a timer.</span></span>

<span data-ttu-id="e773b-182">关系图为重新呈现，并计算了 UI*差异*（差异）。</span><span class="sxs-lookup"><span data-stu-id="e773b-182">The graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="e773b-183">这种差异是更新客户端上 UI 所需的最小 DOM 编辑集。</span><span class="sxs-lookup"><span data-stu-id="e773b-183">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="e773b-184">将以二进制格式将差异发送到客户端，并由浏览器应用。</span><span class="sxs-lookup"><span data-stu-id="e773b-184">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="e773b-185">当用户在客户端上导航掉组件后，将释放该组件。</span><span class="sxs-lookup"><span data-stu-id="e773b-185">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="e773b-186">当用户与组件交互时，组件的状态（服务、资源）必须保存在服务器的内存中。</span><span class="sxs-lookup"><span data-stu-id="e773b-186">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="e773b-187">由于多个组件的状态可能同时由服务器维护，因此内存耗尽是必须解决的问题。</span><span class="sxs-lookup"><span data-stu-id="e773b-187">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="e773b-188">有关如何创作 Blazor Server 应用程序以确保最大程度地使用服务器内存的指导，请参阅 <xref:security/blazor/server>。</span><span class="sxs-lookup"><span data-stu-id="e773b-188">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:security/blazor/server>.</span></span>

### <a name="circuits"></a><span data-ttu-id="e773b-189">而言</span><span class="sxs-lookup"><span data-stu-id="e773b-189">Circuits</span></span>

<span data-ttu-id="e773b-190">Blazor Server 应用是在[ASP.NET Core SignalR](xref:signalr/introduction)的基础上构建的。</span><span class="sxs-lookup"><span data-stu-id="e773b-190">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="e773b-191">每个客户端通过一个或多个称为*线路*的 SignalR 连接与服务器通信。</span><span class="sxs-lookup"><span data-stu-id="e773b-191">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="e773b-192">线路是对可容忍暂时网络中断的 SignalR 连接 Blazor抽象。</span><span class="sxs-lookup"><span data-stu-id="e773b-192">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="e773b-193">当 Blazor 客户端发现 SignalR 连接已断开连接时，它会尝试使用新的 SignalR 连接重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="e773b-193">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="e773b-194">连接到 Blazor Server 应用程序的每个浏览器屏幕（浏览器选项卡或 iframe）都使用 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="e773b-194">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="e773b-195">与典型服务器呈现的应用相比，这一点还有一个重要的区别。</span><span class="sxs-lookup"><span data-stu-id="e773b-195">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="e773b-196">在服务器呈现的应用程序中，在多个浏览器屏幕中打开同一应用程序通常不会转换为服务器上的其他资源需求。</span><span class="sxs-lookup"><span data-stu-id="e773b-196">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="e773b-197">在 Blazor Server 应用程序中，每个浏览器屏幕都需要单独的线路和组件状态的单独实例，由服务器来管理。</span><span class="sxs-lookup"><span data-stu-id="e773b-197">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

Blazor<span data-ttu-id="e773b-198"> 考虑关闭浏览器选项卡或导航到外部 URL，*正常*终止。</span><span class="sxs-lookup"><span data-stu-id="e773b-198"> considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="e773b-199">如果正常终止，则会立即释放线路和关联的资源。</span><span class="sxs-lookup"><span data-stu-id="e773b-199">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="e773b-200">例如，由于网络中断，客户端也可能断开连接。</span><span class="sxs-lookup"><span data-stu-id="e773b-200">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> Blazor<span data-ttu-id="e773b-201"> 服务器会将断开连接的线路存储为可配置的间隔，以允许客户端重新连接。</span><span class="sxs-lookup"><span data-stu-id="e773b-201"> Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span> <span data-ttu-id="e773b-202">有关详细信息，请参阅重新[连接到同一服务器](#reconnection-to-the-same-server)部分。</span><span class="sxs-lookup"><span data-stu-id="e773b-202">For more information, see the [Reconnection to the same server](#reconnection-to-the-same-server) section.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="e773b-203">UI 延迟</span><span class="sxs-lookup"><span data-stu-id="e773b-203">UI Latency</span></span>

<span data-ttu-id="e773b-204">UI 延迟是指从启动的操作到 UI 更新的时间。</span><span class="sxs-lookup"><span data-stu-id="e773b-204">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="e773b-205">对于应用程序来说，更小的 UI 延迟值非常适合应用程序对用户的响应。</span><span class="sxs-lookup"><span data-stu-id="e773b-205">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="e773b-206">在 Blazor 服务器应用中，每个操作都将发送到服务器并进行处理，并将向后发送 UI 差异。</span><span class="sxs-lookup"><span data-stu-id="e773b-206">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="e773b-207">因此，UI 延迟是网络延迟和处理操作时服务器延迟的总和。</span><span class="sxs-lookup"><span data-stu-id="e773b-207">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="e773b-208">对于仅限于专用公司网络的业务线应用，对用户而言，由于网络延迟导致的延迟通常是让的。</span><span class="sxs-lookup"><span data-stu-id="e773b-208">For a line of business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="e773b-209">对于通过 Internet 部署的应用，用户可能会对延迟造成明显的影响，尤其是用户广泛分散于各地。</span><span class="sxs-lookup"><span data-stu-id="e773b-209">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="e773b-210">内存使用率还会导致应用延迟。</span><span class="sxs-lookup"><span data-stu-id="e773b-210">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="e773b-211">增加的内存使用会导致频繁垃圾收集或将内存分页到磁盘，这两者都会降低应用程序性能，进而增加 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="e773b-211">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span> <span data-ttu-id="e773b-212">有关更多信息，请参见<xref:security/blazor/server>。</span><span class="sxs-lookup"><span data-stu-id="e773b-212">For more information, see <xref:security/blazor/server>.</span></span>

Blazor<span data-ttu-id="e773b-213"> Server apps 应进行优化，以通过减少网络延迟和内存使用率来最大限度地减少 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="e773b-213"> Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="e773b-214">有关测量网络延迟的方法，请参阅 <xref:host-and-deploy/blazor/server#measure-network-latency>。</span><span class="sxs-lookup"><span data-stu-id="e773b-214">For an approach to measuring network latency, see <xref:host-and-deploy/blazor/server#measure-network-latency>.</span></span> <span data-ttu-id="e773b-215">有关 SignalR 和 Blazor的详细信息，请参阅：</span><span class="sxs-lookup"><span data-stu-id="e773b-215">For more information on SignalR and Blazor, see:</span></span>

* <xref:host-and-deploy/blazor/server>
* <xref:security/blazor/server>

### <a name="connection-to-the-server"></a><span data-ttu-id="e773b-216">与服务器的连接</span><span class="sxs-lookup"><span data-stu-id="e773b-216">Connection to the server</span></span>

Blazor<span data-ttu-id="e773b-217"> Server 应用需要与服务器建立活动的 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="e773b-217"> Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="e773b-218">如果连接丢失，应用会尝试重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="e773b-218">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="e773b-219">只要客户端的状态仍在内存中，客户端会话便会恢复而不会失去状态。</span><span class="sxs-lookup"><span data-stu-id="e773b-219">As long as the client's state is still in memory, the client session resumes without losing state.</span></span>

#### <a name="reconnection-to-the-same-server"></a><span data-ttu-id="e773b-220">重新连接到同一台服务器</span><span class="sxs-lookup"><span data-stu-id="e773b-220">Reconnection to the same server</span></span>

<span data-ttu-id="e773b-221">Blazor Server 应用 prerenders 为响应第一个客户端请求，该请求在服务器上设置 UI 状态。</span><span class="sxs-lookup"><span data-stu-id="e773b-221">A Blazor Server app prerenders in response to the first client request, which sets up the UI state on the server.</span></span> <span data-ttu-id="e773b-222">当客户端尝试创建 SignalR 连接时，客户端必须重新连接到同一服务器。</span><span class="sxs-lookup"><span data-stu-id="e773b-222">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> <span data-ttu-id="e773b-223">使用多台后端服务器 Blazor 服务器应用应为 SignalR 连接实现*粘滞会话*。</span><span class="sxs-lookup"><span data-stu-id="e773b-223">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="e773b-224">我们建议将 [Azure SignalR 服务](/azure/azure-signalr)用于 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="e773b-224">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="e773b-225">该服务允许将 Blazor Server 应用扩展到大量并发 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="e773b-225">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="e773b-226">可以通过将服务的 `ServerStickyMode` 选项或配置值设置为 `Required`，为 Azure SignalR 服务启用粘滞会话。</span><span class="sxs-lookup"><span data-stu-id="e773b-226">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="e773b-227">有关更多信息，请参见<xref:host-and-deploy/blazor/server#signalr-configuration>。</span><span class="sxs-lookup"><span data-stu-id="e773b-227">For more information, see <xref:host-and-deploy/blazor/server#signalr-configuration>.</span></span>

<span data-ttu-id="e773b-228">使用 IIS 时，将使用应用程序请求路由启用粘滞会话。</span><span class="sxs-lookup"><span data-stu-id="e773b-228">When using IIS, sticky sessions are enabled with Application Request Routing.</span></span> <span data-ttu-id="e773b-229">有关详细信息，请参阅[使用应用程序请求路由的 HTTP 负载平衡](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)。</span><span class="sxs-lookup"><span data-stu-id="e773b-229">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

#### <a name="reflect-the-connection-state-in-the-ui"></a><span data-ttu-id="e773b-230">反映 UI 中的连接状态</span><span class="sxs-lookup"><span data-stu-id="e773b-230">Reflect the connection state in the UI</span></span>

<span data-ttu-id="e773b-231">当客户端检测到连接已丢失时，用户会在客户端尝试重新连接时向用户显示默认 UI。</span><span class="sxs-lookup"><span data-stu-id="e773b-231">When the client detects that the connection has been lost, a default UI is displayed to the user while the client attempts to reconnect.</span></span> <span data-ttu-id="e773b-232">如果重新连接失败，则会向用户提供重试选项。</span><span class="sxs-lookup"><span data-stu-id="e773b-232">If reconnection fails, the user is provided the option to retry.</span></span>

<span data-ttu-id="e773b-233">若要自定义 UI，请在 *_Host* Razor page 的 `<body>` 中定义具有 `components-reconnect-modal` `id` 的元素：</span><span class="sxs-lookup"><span data-stu-id="e773b-233">To customize the UI, define an element with an `id` of `components-reconnect-modal` in the `<body>` of the *_Host.cshtml* Razor page:</span></span>

```cshtml
<div id="components-reconnect-modal">
    ...
</div>
```

<span data-ttu-id="e773b-234">下表描述了应用于 `components-reconnect-modal` 元素的 CSS 类。</span><span class="sxs-lookup"><span data-stu-id="e773b-234">The following table describes the CSS classes applied to the `components-reconnect-modal` element.</span></span>

| <span data-ttu-id="e773b-235">CSS 类</span><span class="sxs-lookup"><span data-stu-id="e773b-235">CSS class</span></span>                       | <span data-ttu-id="e773b-236">指示&hellip;</span><span class="sxs-lookup"><span data-stu-id="e773b-236">Indicates&hellip;</span></span> |
| ------------------------------- | ------------------------- |
| `components-reconnect-show`     | <span data-ttu-id="e773b-237">断开连接。</span><span class="sxs-lookup"><span data-stu-id="e773b-237">A lost connection.</span></span> <span data-ttu-id="e773b-238">客户端正在尝试重新连接。</span><span class="sxs-lookup"><span data-stu-id="e773b-238">The client is attempting to reconnect.</span></span> <span data-ttu-id="e773b-239">显示模式。</span><span class="sxs-lookup"><span data-stu-id="e773b-239">Show the modal.</span></span> |
| `components-reconnect-hide`     | <span data-ttu-id="e773b-240">将为服务器重新建立活动连接。</span><span class="sxs-lookup"><span data-stu-id="e773b-240">An active connection is re-established to the server.</span></span> <span data-ttu-id="e773b-241">隐藏模式。</span><span class="sxs-lookup"><span data-stu-id="e773b-241">Hide the modal.</span></span> |
| `components-reconnect-failed`   | <span data-ttu-id="e773b-242">重新连接失败，可能是由于网络故障引起的。</span><span class="sxs-lookup"><span data-stu-id="e773b-242">Reconnection failed, probably due to a network failure.</span></span> <span data-ttu-id="e773b-243">若要尝试重新连接，请调用 `window.Blazor.reconnect()`。</span><span class="sxs-lookup"><span data-stu-id="e773b-243">To attempt reconnection, call `window.Blazor.reconnect()`.</span></span> |
| `components-reconnect-rejected` | <span data-ttu-id="e773b-244">已拒绝重新连接。</span><span class="sxs-lookup"><span data-stu-id="e773b-244">Reconnection rejected.</span></span> <span data-ttu-id="e773b-245">服务器已达到但拒绝了连接，并且服务器上的用户状态丢失。</span><span class="sxs-lookup"><span data-stu-id="e773b-245">The server was reached but refused the connection, and the user's state on the server is lost.</span></span> <span data-ttu-id="e773b-246">若要重新加载应用，请调用 `location.reload()`。</span><span class="sxs-lookup"><span data-stu-id="e773b-246">To reload the app, call `location.reload()`.</span></span> <span data-ttu-id="e773b-247">当以下情况时，可能会导致此连接状态：</span><span class="sxs-lookup"><span data-stu-id="e773b-247">This connection state may result when:</span></span><ul><li><span data-ttu-id="e773b-248">服务器端线路发生崩溃。</span><span class="sxs-lookup"><span data-stu-id="e773b-248">A crash in the server-side circuit occurs.</span></span></li><li><span data-ttu-id="e773b-249">断开客户端的连接时间足以使服务器删除用户的状态。</span><span class="sxs-lookup"><span data-stu-id="e773b-249">The client is disconnected long enough for the server to drop the user's state.</span></span> <span data-ttu-id="e773b-250">将释放用户与之交互的组件的实例。</span><span class="sxs-lookup"><span data-stu-id="e773b-250">Instances of the components that the user is interacting with are disposed.</span></span></li><li><span data-ttu-id="e773b-251">服务器已重新启动，或者应用程序的工作进程被回收。</span><span class="sxs-lookup"><span data-stu-id="e773b-251">The server is restarted, or the app's worker process is recycled.</span></span></li></ul> |

### <a name="stateful-reconnection-after-prerendering"></a><span data-ttu-id="e773b-252">预呈现后有状态重新连接</span><span class="sxs-lookup"><span data-stu-id="e773b-252">Stateful reconnection after prerendering</span></span>

<span data-ttu-id="e773b-253">默认情况下，Blazor 服务器应用程序设置为在建立客户端与服务器之间的连接之前，在服务器上预呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="e773b-253">Blazor Server apps are set up by default to prerender the UI on the server before the client connection to the server is established.</span></span> <span data-ttu-id="e773b-254">这是在 *_Host*的 "Razor" Razor 页面中设置的：</span><span class="sxs-lookup"><span data-stu-id="e773b-254">This is set up in the *_Host.cshtml* Razor page:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<body>
    <app>
      <component type="typeof(App)" render-mode="ServerPrerendered" />
    </app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<body>
    <app>@(await Html.RenderComponentAsync<App>(RenderMode.ServerPrerendered))</app>

    <script src="_framework/blazor.server.js"></script>
</body>
```

::: moniker-end

<span data-ttu-id="e773b-255">`RenderMode` 配置组件是否：</span><span class="sxs-lookup"><span data-stu-id="e773b-255">`RenderMode` configures whether the component:</span></span>

* <span data-ttu-id="e773b-256">已预呈现到页面中。</span><span class="sxs-lookup"><span data-stu-id="e773b-256">Is prerendered into the page.</span></span>
* <span data-ttu-id="e773b-257">在页面上呈现为静态 HTML，或者它包含从用户代理启动 Blazor 应用程序所需的信息。</span><span class="sxs-lookup"><span data-stu-id="e773b-257">Is rendered as static HTML on the page or if it includes the necessary information to bootstrap a Blazor app from the user agent.</span></span>

::: moniker range=">= aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="e773b-258">描述</span><span class="sxs-lookup"><span data-stu-id="e773b-258">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="e773b-259">将组件呈现为静态 HTML，并为 Blazor 服务器应用包含标记。</span><span class="sxs-lookup"><span data-stu-id="e773b-259">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e773b-260">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="e773b-260">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Server`            | <span data-ttu-id="e773b-261">呈现 Blazor 服务器应用程序的标记。</span><span class="sxs-lookup"><span data-stu-id="e773b-261">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e773b-262">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="e773b-262">Output from the component isn't included.</span></span> <span data-ttu-id="e773b-263">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="e773b-263">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> |
| `Static`            | <span data-ttu-id="e773b-264">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="e773b-264">Renders the component into static HTML.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.1"

| `RenderMode`        | <span data-ttu-id="e773b-265">描述</span><span class="sxs-lookup"><span data-stu-id="e773b-265">Description</span></span> |
| ------------------- | ----------- |
| `ServerPrerendered` | <span data-ttu-id="e773b-266">将组件呈现为静态 HTML，并为 Blazor 服务器应用包含标记。</span><span class="sxs-lookup"><span data-stu-id="e773b-266">Renders the component into static HTML and includes a marker for a Blazor Server app.</span></span> <span data-ttu-id="e773b-267">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="e773b-267">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="e773b-268">不支持参数。</span><span class="sxs-lookup"><span data-stu-id="e773b-268">Parameters aren't supported.</span></span> |
| `Server`            | <span data-ttu-id="e773b-269">呈现 Blazor 服务器应用程序的标记。</span><span class="sxs-lookup"><span data-stu-id="e773b-269">Renders a marker for a Blazor Server app.</span></span> <span data-ttu-id="e773b-270">不包括组件的输出。</span><span class="sxs-lookup"><span data-stu-id="e773b-270">Output from the component isn't included.</span></span> <span data-ttu-id="e773b-271">用户代理启动时，此标记用于启动 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="e773b-271">When the user-agent starts, this marker is used to bootstrap a Blazor app.</span></span> <span data-ttu-id="e773b-272">不支持参数。</span><span class="sxs-lookup"><span data-stu-id="e773b-272">Parameters aren't supported.</span></span> |
| `Static`            | <span data-ttu-id="e773b-273">将组件呈现为静态 HTML。</span><span class="sxs-lookup"><span data-stu-id="e773b-273">Renders the component into static HTML.</span></span> <span data-ttu-id="e773b-274">支持参数。</span><span class="sxs-lookup"><span data-stu-id="e773b-274">Parameters are supported.</span></span> |

::: moniker-end

<span data-ttu-id="e773b-275">不支持从静态 HTML 页面呈现服务器组件。</span><span class="sxs-lookup"><span data-stu-id="e773b-275">Rendering server components from a static HTML page isn't supported.</span></span>

<span data-ttu-id="e773b-276">当 `ServerPrerendered``RenderMode` 时，组件最初作为页面的一部分以静态方式呈现。</span><span class="sxs-lookup"><span data-stu-id="e773b-276">When `RenderMode` is `ServerPrerendered`, the component is initially rendered statically as part of the page.</span></span> <span data-ttu-id="e773b-277">一旦浏览器与服务器建立了连接，该组件将*再次*呈现，并且该组件现在是交互式的。</span><span class="sxs-lookup"><span data-stu-id="e773b-277">Once the browser establishes a connection back to the server, the component is rendered *again*, and the component is now interactive.</span></span> <span data-ttu-id="e773b-278">如果存在用于初始化组件的[OnInitialized {Async}](xref:blazor/lifecycle#component-initialization-methods)生命周期方法，则将执行*两次*此方法：</span><span class="sxs-lookup"><span data-stu-id="e773b-278">If the [OnInitialized{Async}](xref:blazor/lifecycle#component-initialization-methods) lifecycle method for initializing the component is present, the method is executed *twice*:</span></span>

* <span data-ttu-id="e773b-279">如果组件预呈现静态，则为。</span><span class="sxs-lookup"><span data-stu-id="e773b-279">When the component is prerendered statically.</span></span>
* <span data-ttu-id="e773b-280">建立服务器连接之后。</span><span class="sxs-lookup"><span data-stu-id="e773b-280">After the server connection has been established.</span></span>

<span data-ttu-id="e773b-281">这可能会导致在最终呈现组件时，UI 中显示的数据发生显著变化。</span><span class="sxs-lookup"><span data-stu-id="e773b-281">This can result in a noticeable change in the data displayed in the UI when the component is finally rendered.</span></span>

<span data-ttu-id="e773b-282">若要避免 Blazor 服务器应用中出现双重渲染方案：</span><span class="sxs-lookup"><span data-stu-id="e773b-282">To avoid the double-rendering scenario in a Blazor Server app:</span></span>

* <span data-ttu-id="e773b-283">传入一个标识符，该标识符可用于在预呈现期间缓存状态并在应用重新启动后检索状态。</span><span class="sxs-lookup"><span data-stu-id="e773b-283">Pass in an identifier that can be used to cache the state during prerendering and to retrieve the state after the app restarts.</span></span>
* <span data-ttu-id="e773b-284">在预呈现期间使用标识符以保存组件状态。</span><span class="sxs-lookup"><span data-stu-id="e773b-284">Use the identifier during prerendering to save component state.</span></span>
* <span data-ttu-id="e773b-285">在预呈现后使用标识符检索缓存状态。</span><span class="sxs-lookup"><span data-stu-id="e773b-285">Use the identifier after prerendering to retrieve the cached state.</span></span>

<span data-ttu-id="e773b-286">下面的代码演示基于模板的 Blazor 服务器应用中的更新 `WeatherForecastService`，可避免双重呈现：</span><span class="sxs-lookup"><span data-stu-id="e773b-286">The following code demonstrates an updated `WeatherForecastService` in a template-based Blazor Server app that avoids the double rendering:</span></span>

```csharp
public class WeatherForecastService
{
    private static readonly string[] Summaries = new[]
    {
        "Freezing", "Bracing", "Chilly", "Cool", "Mild",
        "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
    };
    
    public WeatherForecastService(IMemoryCache memoryCache)
    {
        MemoryCache = memoryCache;
    }
    
    public IMemoryCache MemoryCache { get; }

    public Task<WeatherForecast[]> GetForecastAsync(DateTime startDate)
    {
        return MemoryCache.GetOrCreateAsync(startDate, async e =>
        {
            e.SetOptions(new MemoryCacheEntryOptions
            {
                AbsoluteExpirationRelativeToNow = 
                    TimeSpan.FromSeconds(30)
            });

            var rng = new Random();

            await Task.Delay(TimeSpan.FromSeconds(10));

            return Enumerable.Range(1, 5).Select(index => new WeatherForecast
            {
                Date = startDate.AddDays(index),
                TemperatureC = rng.Next(-20, 55),
                Summary = Summaries[rng.Next(Summaries.Length)]
            }).ToArray();
        });
    }
}
```

### <a name="render-stateful-interactive-components-from-razor-pages-and-views"></a><span data-ttu-id="e773b-287">从 Razor 页面和视图呈现有状态交互式组件</span><span class="sxs-lookup"><span data-stu-id="e773b-287">Render stateful interactive components from Razor pages and views</span></span>

<span data-ttu-id="e773b-288">可以将有状态交互组件添加到 Razor 页面或视图。</span><span class="sxs-lookup"><span data-stu-id="e773b-288">Stateful interactive components can be added to a Razor page or view.</span></span>

<span data-ttu-id="e773b-289">呈现页面或视图时：</span><span class="sxs-lookup"><span data-stu-id="e773b-289">When the page or view renders:</span></span>

* <span data-ttu-id="e773b-290">该组件与页面或视图预呈现。</span><span class="sxs-lookup"><span data-stu-id="e773b-290">The component is prerendered with the page or view.</span></span>
* <span data-ttu-id="e773b-291">用于预呈现的初始组件状态将丢失。</span><span class="sxs-lookup"><span data-stu-id="e773b-291">The initial component state used for prerendering is lost.</span></span>
* <span data-ttu-id="e773b-292">建立 SignalR 连接时，将创建新的组件状态。</span><span class="sxs-lookup"><span data-stu-id="e773b-292">New component state is created when the SignalR connection is established.</span></span>

<span data-ttu-id="e773b-293">以下 Razor 页面将呈现一个 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="e773b-293">The following Razor page renders a `Counter` component:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<component type="typeof(Counter)" render-mode="ServerPrerendered" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

@(await Html.RenderComponentAsync<Counter>(RenderMode.ServerPrerendered))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

### <a name="render-noninteractive-components-from-razor-pages-and-views"></a><span data-ttu-id="e773b-294">从 Razor 页面和视图呈现非交互式组件</span><span class="sxs-lookup"><span data-stu-id="e773b-294">Render noninteractive components from Razor pages and views</span></span>

<span data-ttu-id="e773b-295">在以下 Razor 页面中，使用以下格式使用指定的初始值静态呈现 `Counter` 组件：</span><span class="sxs-lookup"><span data-stu-id="e773b-295">In the following Razor page, the `Counter` component is statically rendered with an initial value that's specified using a form:</span></span>

::: moniker range=">= aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

<component type="typeof(Counter)" render-mode="Static" 
    param-InitialValue="InitialValue" />

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.1"

```cshtml
<h1>My Razor Page</h1>

<form>
    <input type="number" asp-for="InitialValue" />
    <button type="submit">Set initial value</button>
</form>

@(await Html.RenderComponentAsync<Counter>(RenderMode.Static, 
    new { InitialValue = InitialValue }))

@code {
    [BindProperty(SupportsGet=true)]
    public int InitialValue { get; set; }
}
```

::: moniker-end

<span data-ttu-id="e773b-296">由于 `MyComponent` 是以静态方式呈现的，因此该组件不能是交互式的。</span><span class="sxs-lookup"><span data-stu-id="e773b-296">Since `MyComponent` is statically rendered, the component can't be interactive.</span></span>

### <a name="detect-when-the-app-is-prerendering"></a><span data-ttu-id="e773b-297">检测预呈现应用的时间</span><span class="sxs-lookup"><span data-stu-id="e773b-297">Detect when the app is prerendering</span></span>

[!INCLUDE[](~/includes/blazor-prerendering.md)]

### <a name="configure-the-opno-locsignalr-client-for-opno-locblazor-server-apps"></a><span data-ttu-id="e773b-298">为 Blazor 服务器应用配置 SignalR 客户端</span><span class="sxs-lookup"><span data-stu-id="e773b-298">Configure the SignalR client for Blazor Server apps</span></span>

<span data-ttu-id="e773b-299">有时，需要配置 Blazor 服务器应用使用的 SignalR 客户端。</span><span class="sxs-lookup"><span data-stu-id="e773b-299">Sometimes, you need to configure the SignalR client used by Blazor Server apps.</span></span> <span data-ttu-id="e773b-300">例如，你可能想要在 SignalR 客户端上配置日志记录以诊断连接问题。</span><span class="sxs-lookup"><span data-stu-id="e773b-300">For example, you might want to configure logging on the SignalR client to diagnose a connection issue.</span></span>

<span data-ttu-id="e773b-301">若要在*Pages/_Host* # 文件中配置 SignalR 客户端：</span><span class="sxs-lookup"><span data-stu-id="e773b-301">To configure the SignalR client in the *Pages/_Host.cshtml* file:</span></span>

* <span data-ttu-id="e773b-302">将 `autostart="false"` 特性添加到*blazor*脚本的 `<script>` 标记中。</span><span class="sxs-lookup"><span data-stu-id="e773b-302">Add an `autostart="false"` attribute to the `<script>` tag for the *blazor.server.js* script.</span></span>
* <span data-ttu-id="e773b-303">调用 `Blazor.start` 并传入指定 SignalR 生成器的配置对象。</span><span class="sxs-lookup"><span data-stu-id="e773b-303">Call `Blazor.start` and pass in a configuration object that specifies the SignalR builder.</span></span>

```html
<script src="_framework/blazor.server.js" autostart="false"></script>
<script>
  Blazor.start({
    configureSignalR: function (builder) {
      builder.configureLogging("information"); // LogLevel.Information
    }
  });
</script>
```

## <a name="additional-resources"></a><span data-ttu-id="e773b-304">其他资源</span><span class="sxs-lookup"><span data-stu-id="e773b-304">Additional resources</span></span>

* <xref:blazor/get-started>
* <xref:signalr/introduction>
