---
title: ASP.NET Core Blazor 托管模型
author: guardrex
description: 了解 Blazor WebAssembly 和 Blazor Server 托管模型。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/07/2020
no-loc:
- appsettings.json
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
uid: blazor/hosting-models
ms.openlocfilehash: a6f1c88b8e93c0d8ccfebca482895ebab8d18a81
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97506911"
---
# <a name="aspnet-core-no-locblazor-hosting-models"></a><span data-ttu-id="e0386-103">ASP.NET Core Blazor 托管模型</span><span class="sxs-lookup"><span data-stu-id="e0386-103">ASP.NET Core Blazor hosting models</span></span>

<span data-ttu-id="e0386-104">作者：[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="e0386-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="e0386-105">Blazor 是一种 Web 框架，专用于在基于 [WebAssembly](https://webassembly.org/) 的 .NET 运行时 (Blazor WebAssembly) 上的浏览器中运行客户端，或在 ASP.NET Core (Blazor Server) 中运行服务器端 。</span><span class="sxs-lookup"><span data-stu-id="e0386-105">Blazor is a web framework designed to run client-side in the browser on a [WebAssembly](https://webassembly.org/)-based .NET runtime (*Blazor WebAssembly*) or server-side in ASP.NET Core (*Blazor Server*).</span></span> <span data-ttu-id="e0386-106">对于任意托管模型，应用和组件模型都相同。</span><span class="sxs-lookup"><span data-stu-id="e0386-106">Regardless of the hosting model, the app and component models *are the same*.</span></span>

## Blazor WebAssembly

<span data-ttu-id="e0386-107">主要的 Blazor 托管模型在 WebAssembly 上的浏览器中运行客户端。</span><span class="sxs-lookup"><span data-stu-id="e0386-107">The primary Blazor hosting model is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="e0386-108">将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="e0386-108">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="e0386-109">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="e0386-109">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="e0386-110">UI 更新和事件处理在同一进程中进行。</span><span class="sxs-lookup"><span data-stu-id="e0386-110">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="e0386-111">应用资产作为静态文件部署到可为客户端提供静态内容的 Web 服务器或服务中。</span><span class="sxs-lookup"><span data-stu-id="e0386-111">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor WebAssembly:Blazor 应用在浏览器内部的 UI 线程上运行。](hosting-models/_static/blazor-webassembly.png)

<span data-ttu-id="e0386-113">如果创建了 Blazor WebAssembly 应用进行部署，但没有后端 ASP.NET Core 应用来为其文件提供服务，那么该应用被称为独立 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="e0386-113">When the Blazor WebAssembly app is created for deployment without a backend ASP.NET Core app to serve its files, the app is called a *standalone* Blazor WebAssembly app.</span></span> <span data-ttu-id="e0386-114">如果创建了应用进行部署，但没有后端应用来为其文件提供服务，那么该应用被称为托管的 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="e0386-114">When the app is created for deployment with a backend app to serve its files, the app is called a *hosted* Blazor WebAssembly app.</span></span> <span data-ttu-id="e0386-115">托管的 Blazor WebAssembly 应用通常使用 Web API 调用或 [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>) 通过网络与服务器交互。</span><span class="sxs-lookup"><span data-stu-id="e0386-115">A hosted Blazor WebAssembly app typically interacts with the server over the network using web API calls or [SignalR](xref:signalr/introduction) (<xref:tutorials/signalr-blazor-webassembly>).</span></span>

<span data-ttu-id="e0386-116">`blazor.webassembly.js` 脚本由框架和句柄提供：</span><span class="sxs-lookup"><span data-stu-id="e0386-116">The `blazor.webassembly.js` script is provided by the framework and handles:</span></span>

* <span data-ttu-id="e0386-117">下载 .NET 运行时、应用和应用依赖项。</span><span class="sxs-lookup"><span data-stu-id="e0386-117">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="e0386-118">初始化运行应用的运行时。</span><span class="sxs-lookup"><span data-stu-id="e0386-118">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="e0386-119">Blazor WebAssembly 托管模型具有以下优点：</span><span class="sxs-lookup"><span data-stu-id="e0386-119">The Blazor WebAssembly hosting model offers several benefits:</span></span>

* <span data-ttu-id="e0386-120">没有 .NET 服务器端依赖项。</span><span class="sxs-lookup"><span data-stu-id="e0386-120">There's no .NET server-side dependency.</span></span> <span data-ttu-id="e0386-121">应用下载到客户端后即可正常运行。</span><span class="sxs-lookup"><span data-stu-id="e0386-121">The app is fully functioning after it's downloaded to the client.</span></span>
* <span data-ttu-id="e0386-122">可充分利用客户端资源和功能。</span><span class="sxs-lookup"><span data-stu-id="e0386-122">Client resources and capabilities are fully leveraged.</span></span>
* <span data-ttu-id="e0386-123">工作可从服务器转移到客户端。</span><span class="sxs-lookup"><span data-stu-id="e0386-123">Work is offloaded from the server to the client.</span></span>
* <span data-ttu-id="e0386-124">无需 ASP.NET Core Web 服务器即可托管应用。</span><span class="sxs-lookup"><span data-stu-id="e0386-124">An ASP.NET Core web server isn't required to host the app.</span></span> <span data-ttu-id="e0386-125">无服务器部署方案可行，例如通过内容分发网络 (CDN) 为应用提供服务的方案。</span><span class="sxs-lookup"><span data-stu-id="e0386-125">Serverless deployment scenarios are possible, such as serving the app from a Content Delivery Network (CDN).</span></span>

<span data-ttu-id="e0386-126">Blazor WebAssembly 托管模型具有以下局限性：</span><span class="sxs-lookup"><span data-stu-id="e0386-126">The Blazor WebAssembly hosting model has the following limitations:</span></span>

* <span data-ttu-id="e0386-127">应用仅可使用浏览器功能。</span><span class="sxs-lookup"><span data-stu-id="e0386-127">The app is restricted to the capabilities of the browser.</span></span>
* <span data-ttu-id="e0386-128">需要可用的客户端硬件和软件（例如 WebAssembly 支持）。</span><span class="sxs-lookup"><span data-stu-id="e0386-128">Capable client hardware and software (for example, WebAssembly support) is required.</span></span>
* <span data-ttu-id="e0386-129">下载项大小较大，应用加载耗时较长。</span><span class="sxs-lookup"><span data-stu-id="e0386-129">Download size is larger, and apps take longer to load.</span></span>
* <span data-ttu-id="e0386-130">.NET 运行时和工具支持不够完善。</span><span class="sxs-lookup"><span data-stu-id="e0386-130">.NET runtime and tooling support is less mature.</span></span> <span data-ttu-id="e0386-131">例如，[.NET Standard](/dotnet/standard/net-standard) 支持和调试方面存在限制。</span><span class="sxs-lookup"><span data-stu-id="e0386-131">For example, limitations exist in [.NET Standard](/dotnet/standard/net-standard) support and debugging.</span></span>

<span data-ttu-id="e0386-132">若要创建 Blazor WebAssembly 应用，请参阅 <xref:blazor/tooling>。</span><span class="sxs-lookup"><span data-stu-id="e0386-132">To create a Blazor WebAssembly app, see <xref:blazor/tooling>.</span></span>

<span data-ttu-id="e0386-133">Blazor 托管应用模型支持 [Docker 容器](/dotnet/standard/microservices-architecture/container-docker-introduction/index)。</span><span class="sxs-lookup"><span data-stu-id="e0386-133">The hosted Blazor app model supports [Docker containers](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span> <span data-ttu-id="e0386-134">对于 Visual Studio 中的 Docker 支持，请右键单击托管的 Blazor WebAssembly 解决方案的 `Server` 项目，然后选择“添加” > “Docker 支持” 。</span><span class="sxs-lookup"><span data-stu-id="e0386-134">For Docker support in Visual Studio, right-click on the `Server` project of a hosted Blazor WebAssembly solution and select **Add** > **Docker Support**.</span></span>

## Blazor Server

<span data-ttu-id="e0386-135">使用 Blazor Server 托管模型可从 ASP.NET Core 应用中在服务器上执行应用。</span><span class="sxs-lookup"><span data-stu-id="e0386-135">With the Blazor Server hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="e0386-136">UI 更新、事件处理和 JavaScript 调用是通过 [SignalR](xref:signalr/introduction) 连接进行处理。</span><span class="sxs-lookup"><span data-stu-id="e0386-136">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![浏览器通过 SignalR 连接与服务器上的应用（该应用托管在 ASP.NET Core 应用内部）进行交互。](hosting-models/_static/blazor-server.png)

<span data-ttu-id="e0386-138">ASP.NET Core 应用会引用应用的 `Startup` 类以添加以下内容：</span><span class="sxs-lookup"><span data-stu-id="e0386-138">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="e0386-139">服务器端服务。</span><span class="sxs-lookup"><span data-stu-id="e0386-139">Server-side services.</span></span>
* <span data-ttu-id="e0386-140">用于请求处理管道的应用。</span><span class="sxs-lookup"><span data-stu-id="e0386-140">The app to the request handling pipeline.</span></span>

<span data-ttu-id="e0386-141">在客户端上，`blazor.server.js` 脚本与服务器建立 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="e0386-141">On the client, the `blazor.server.js` script establishes the SignalR connection with the server.</span></span> <span data-ttu-id="e0386-142">脚本由 ASP.NET Core 共享框架中的嵌入资源提供给客户端应用。</span><span class="sxs-lookup"><span data-stu-id="e0386-142">The script is served to the client-side app from an embedded resource in the ASP.NET Core shared framework.</span></span> <span data-ttu-id="e0386-143">客户端应用负责根据需要保持和还原应用状态。</span><span class="sxs-lookup"><span data-stu-id="e0386-143">The client-side app is responsible for persisting and restoring app state as required.</span></span> 

<span data-ttu-id="e0386-144">Blazor Server 托管模型具有以下优点：</span><span class="sxs-lookup"><span data-stu-id="e0386-144">The Blazor Server hosting model offers several benefits:</span></span>

* <span data-ttu-id="e0386-145">下载项大小明显小于 Blazor WebAssembly 应用，且应用加载速度快得多。</span><span class="sxs-lookup"><span data-stu-id="e0386-145">Download size is significantly smaller than a Blazor WebAssembly app, and the app loads much faster.</span></span>
* <span data-ttu-id="e0386-146">应用可充分利用服务器功能，包括使用任何与 .NET Core 兼容的 API。</span><span class="sxs-lookup"><span data-stu-id="e0386-146">The app takes full advantage of server capabilities, including use of any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="e0386-147">服务器上的 .NET Core 用于运行应用，因此调试等现有 .NET 工具可按预期正常工作。</span><span class="sxs-lookup"><span data-stu-id="e0386-147">.NET Core on the server is used to run the app, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="e0386-148">支持瘦客户端。</span><span class="sxs-lookup"><span data-stu-id="e0386-148">Thin clients are supported.</span></span> <span data-ttu-id="e0386-149">例如，Blazor Server 应用适用于不支持 WebAssembly 的浏览器以及资源受限的设备。</span><span class="sxs-lookup"><span data-stu-id="e0386-149">For example, Blazor Server apps work with browsers that don't support WebAssembly and on resource-constrained devices.</span></span>
* <span data-ttu-id="e0386-150">应用的 .NET/C# 代码库（其中包括应用的组件代码）不适用于客户端。</span><span class="sxs-lookup"><span data-stu-id="e0386-150">The app's .NET/C# code base, including the app's component code, isn't served to clients.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="e0386-151">Blazor Server 应用预呈现以响应第一个客户端请求，这会在服务器上创建 UI 状态。</span><span class="sxs-lookup"><span data-stu-id="e0386-151">A Blazor Server app prerenders in response to the first client request, which creates the UI state on the server.</span></span> <span data-ttu-id="e0386-152">客户端尝试创建 SignalR 连接时，“必须重新连接到同一服务器”。</span><span class="sxs-lookup"><span data-stu-id="e0386-152">When the client attempts to create a SignalR connection, **the client must reconnect to the same server**.</span></span> <span data-ttu-id="e0386-153">使用多个后端服务器的 Blazor Server 应用应实现粘滞会话，从而建立 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="e0386-153">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span> <span data-ttu-id="e0386-154">有关更多信息，请参见[连接到服务器](#connection-to-the-server)一节。</span><span class="sxs-lookup"><span data-stu-id="e0386-154">For more information, see the [Connection to the server](#connection-to-the-server) section.</span></span>

<span data-ttu-id="e0386-155">Blazor Server 托管模型具有以下局限性：</span><span class="sxs-lookup"><span data-stu-id="e0386-155">The Blazor Server hosting model has the following limitations:</span></span>

* <span data-ttu-id="e0386-156">通常延迟较高。</span><span class="sxs-lookup"><span data-stu-id="e0386-156">Higher latency usually exists.</span></span> <span data-ttu-id="e0386-157">每次用户交互都涉及到网络跃点。</span><span class="sxs-lookup"><span data-stu-id="e0386-157">Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="e0386-158">不支持脱机工作。</span><span class="sxs-lookup"><span data-stu-id="e0386-158">There's no offline support.</span></span> <span data-ttu-id="e0386-159">如果客户端连接失败，应用会停止工作。</span><span class="sxs-lookup"><span data-stu-id="e0386-159">If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="e0386-160">如果具有多名用户，则应用扩缩性存在挑战。</span><span class="sxs-lookup"><span data-stu-id="e0386-160">Scalability is challenging for apps with many users.</span></span> <span data-ttu-id="e0386-161">服务器必须管理多个客户端连接并处理客户端状态。</span><span class="sxs-lookup"><span data-stu-id="e0386-161">The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="e0386-162">需要 ASP.NET Core 服务器为应用提供服务。</span><span class="sxs-lookup"><span data-stu-id="e0386-162">An ASP.NET Core server is required to serve the app.</span></span> <span data-ttu-id="e0386-163">无服务器部署方案不可行，例如通过内容分发网络 (CDN) 为应用提供服务的方案。</span><span class="sxs-lookup"><span data-stu-id="e0386-163">Serverless deployment scenarios aren't possible, such as serving the app from a Content Delivery Network (CDN).</span></span>

<span data-ttu-id="e0386-164">若要创建 Blazor Server 应用，请参阅 <xref:blazor/tooling>。</span><span class="sxs-lookup"><span data-stu-id="e0386-164">To create a Blazor Server app, see <xref:blazor/tooling>.</span></span>

<span data-ttu-id="e0386-165">Blazor Server 应用模型支持 [Docker 容器](/dotnet/standard/microservices-architecture/container-docker-introduction/index)。</span><span class="sxs-lookup"><span data-stu-id="e0386-165">The Blazor Server app model supports [Docker containers](/dotnet/standard/microservices-architecture/container-docker-introduction/index).</span></span> <span data-ttu-id="e0386-166">对于 Visual Studio 中的 Docker 支持，请右键单击 Visual Studio 中的项目，然后选择“添加” > “Docker 支持” 。</span><span class="sxs-lookup"><span data-stu-id="e0386-166">For Docker support in Visual Studio, right-click on the project in Visual Studio and select **Add** > **Docker Support**.</span></span>

### <a name="comparison-to-server-rendered-ui"></a><span data-ttu-id="e0386-167">与服务器呈现的 UI 进行比较</span><span class="sxs-lookup"><span data-stu-id="e0386-167">Comparison to server-rendered UI</span></span>

<span data-ttu-id="e0386-168">理解 Blazor Server 应用的一种方法是，了解其与使用 Razor 视图或 Razor Pages 在 ASP.NET Core 应用中呈现 UI 的惯用模型之间的差异。</span><span class="sxs-lookup"><span data-stu-id="e0386-168">One way to understand Blazor Server apps is to understand how it differs from traditional models for rendering UI in ASP.NET Core apps using Razor views or Razor Pages.</span></span> <span data-ttu-id="e0386-169">这两种模型都使用 [Razor 语言](xref:mvc/views/razor)描述 HTML 内容，但两者在标记的呈现方式上差别显著。</span><span class="sxs-lookup"><span data-stu-id="e0386-169">Both models use the [Razor language](xref:mvc/views/razor) to describe HTML content for rendering, but they significantly differ in *how* markup is rendered.</span></span>

<span data-ttu-id="e0386-170">呈现 Razor 页面或视图时，每行 Razor 代码都以文本形式发出 HTML。</span><span class="sxs-lookup"><span data-stu-id="e0386-170">When a Razor Page or view is rendered, every line of Razor code emits HTML in text form.</span></span> <span data-ttu-id="e0386-171">呈现后，服务器会丢弃页面或视图实例，包括生成的任何状态。</span><span class="sxs-lookup"><span data-stu-id="e0386-171">After rendering, the server disposes of the page or view instance, including any state that was produced.</span></span> <span data-ttu-id="e0386-172">出现另一个对该页面的请求时，例如，服务器验证失败并显示验证摘要时：</span><span class="sxs-lookup"><span data-stu-id="e0386-172">When another request for the page occurs, for instance when server validation fails and the validation summary is displayed:</span></span>

* <span data-ttu-id="e0386-173">整个页面将再次重新呈现为 HTML 文本。</span><span class="sxs-lookup"><span data-stu-id="e0386-173">The entire page is rerendered to HTML text again.</span></span>
* <span data-ttu-id="e0386-174">页面会发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="e0386-174">The page is sent to the client.</span></span>

<span data-ttu-id="e0386-175">Blazor 应用由 UI 的可重用元素组成，这些元素称为组件。</span><span class="sxs-lookup"><span data-stu-id="e0386-175">A Blazor app is composed of reusable elements of UI called *components*.</span></span> <span data-ttu-id="e0386-176">组件包含 C# 代码、标记和其他成分。</span><span class="sxs-lookup"><span data-stu-id="e0386-176">A component contains C# code, markup, and other components.</span></span> <span data-ttu-id="e0386-177">呈现组件时，Blazor 会生成所含组件的图，类似于 HTML 或 XML 文档对象模型 (DOM)。</span><span class="sxs-lookup"><span data-stu-id="e0386-177">When a component is rendered, Blazor produces a graph of the included components similar to an HTML or XML Document Object Model (DOM).</span></span> <span data-ttu-id="e0386-178">此图包含属性和字段中保存的组件状态。</span><span class="sxs-lookup"><span data-stu-id="e0386-178">This graph includes component state held in properties and fields.</span></span> <span data-ttu-id="e0386-179">Blazor 会评估组件图，生成二进制形式的标记。</span><span class="sxs-lookup"><span data-stu-id="e0386-179">Blazor evaluates the component graph to produce a binary representation of the markup.</span></span> <span data-ttu-id="e0386-180">二进制格式可以：</span><span class="sxs-lookup"><span data-stu-id="e0386-180">The binary format can be:</span></span>

* <span data-ttu-id="e0386-181">转换为 HTML 文本（预呈现 &dagger; 期间）。</span><span class="sxs-lookup"><span data-stu-id="e0386-181">Turned into HTML text (during prerendering&dagger;).</span></span>
* <span data-ttu-id="e0386-182">用于在定期呈现期间高效更新标记。</span><span class="sxs-lookup"><span data-stu-id="e0386-182">Used to efficiently update the markup during regular rendering.</span></span>

<span data-ttu-id="e0386-183">&dagger;预呈现：请求获取的 Razor 组件在服务器上编译为静态 HTML，并发送到客户端，然后呈现给用户。</span><span class="sxs-lookup"><span data-stu-id="e0386-183">&dagger;*Prerendering*: The requested Razor component is compiled on the server into static HTML and sent to the client, where it's rendered to the user.</span></span> <span data-ttu-id="e0386-184">客户端与服务器之间建立连接后，组件的静态预呈现元素会替换为交互式元素。</span><span class="sxs-lookup"><span data-stu-id="e0386-184">After the connection is made between the client and the server, the component's static prerendered elements are replaced with interactive elements.</span></span> <span data-ttu-id="e0386-185">预呈现会使应用对用户的响应更加迅速。</span><span class="sxs-lookup"><span data-stu-id="e0386-185">Prerendering makes the app feel more responsive to the user.</span></span>

<span data-ttu-id="e0386-186">Blazor 中的 UI 更新由以下内容触发：</span><span class="sxs-lookup"><span data-stu-id="e0386-186">A UI update in Blazor is triggered by:</span></span>

* <span data-ttu-id="e0386-187">用户交互，例如选中按钮。</span><span class="sxs-lookup"><span data-stu-id="e0386-187">User interaction, such as selecting a button.</span></span>
* <span data-ttu-id="e0386-188">应用触发器，例如计时器。</span><span class="sxs-lookup"><span data-stu-id="e0386-188">App triggers, such as a timer.</span></span>

<span data-ttu-id="e0386-189">组件组已预呈现，且已计算 UI diff（差异）。</span><span class="sxs-lookup"><span data-stu-id="e0386-189">The component graph is rerendered, and a UI *diff* (difference) is calculated.</span></span> <span data-ttu-id="e0386-190">此差异是更新客户端上的 UI 所需的最小一组 DOM 编辑。</span><span class="sxs-lookup"><span data-stu-id="e0386-190">This diff is the smallest set of DOM edits required to update the UI on the client.</span></span> <span data-ttu-id="e0386-191">差异以二进制格式发送到客户端，并由浏览器应用。</span><span class="sxs-lookup"><span data-stu-id="e0386-191">The diff is sent to the client in a binary format and applied by the browser.</span></span>

<span data-ttu-id="e0386-192">用户在客户端上退出组件之后，组件会被丢弃。</span><span class="sxs-lookup"><span data-stu-id="e0386-192">A component is disposed after the user navigates away from it on the client.</span></span> <span data-ttu-id="e0386-193">用户与组件交互时，组件的状态（服务、资源）必须保存在服务器的内存中。</span><span class="sxs-lookup"><span data-stu-id="e0386-193">While a user is interacting with a component, the component's state (services, resources) must be held in the server's memory.</span></span> <span data-ttu-id="e0386-194">由于服务器可能同时保存多个组件的状态，因此必须解决内存不足的问题。</span><span class="sxs-lookup"><span data-stu-id="e0386-194">Because the state of many components might be maintained by the server concurrently, memory exhaustion is a concern that must be addressed.</span></span> <span data-ttu-id="e0386-195">要了解如何创作 Blazor Server 应用以确保充分使用服务器内存，请参阅 <xref:blazor/security/server/threat-mitigation>。</span><span class="sxs-lookup"><span data-stu-id="e0386-195">For guidance on how to author a Blazor Server app to ensure the best use of server memory, see <xref:blazor/security/server/threat-mitigation>.</span></span>

### <a name="circuits"></a><span data-ttu-id="e0386-196">线路</span><span class="sxs-lookup"><span data-stu-id="e0386-196">Circuits</span></span>

<span data-ttu-id="e0386-197">Blazor Server 应用基于 [ASP.NET CoreSignalR](xref:signalr/introduction) 构建。</span><span class="sxs-lookup"><span data-stu-id="e0386-197">A Blazor Server app is built on top of [ASP.NET Core SignalR](xref:signalr/introduction).</span></span> <span data-ttu-id="e0386-198">每个客户端通过一个或多个称为“线路”的 SignalR 连接与服务器通信。</span><span class="sxs-lookup"><span data-stu-id="e0386-198">Each client communicates to the server over one or more SignalR connections called a *circuit*.</span></span> <span data-ttu-id="e0386-199">线路是 Blazor 对可容忍短暂网络中断的 SignalR 连接的抽象。</span><span class="sxs-lookup"><span data-stu-id="e0386-199">A circuit is Blazor's abstraction over SignalR connections that can tolerate temporary network interruptions.</span></span> <span data-ttu-id="e0386-200">Blazor 客户端发现 SignalR 连接已断开时，它会尝试使用新的 SignalR 连接来重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="e0386-200">When a Blazor client sees that the SignalR connection is disconnected, it attempts to reconnect to the server using a new SignalR connection.</span></span>

<span data-ttu-id="e0386-201">连接到 Blazor Server 应用的每个浏览器屏幕（浏览器标签页或 iframe）均使用 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="e0386-201">Each browser screen (browser tab or iframe) that is connected to a Blazor Server app uses a SignalR connection.</span></span> <span data-ttu-id="e0386-202">与典型服务器呈现应用相比，这是另一个关键差异。</span><span class="sxs-lookup"><span data-stu-id="e0386-202">This is yet another important distinction compared to typical server-rendered apps.</span></span> <span data-ttu-id="e0386-203">在服务器呈现应用的多个浏览器屏幕中打开同一应用通常不需要服务器上的其他资源。</span><span class="sxs-lookup"><span data-stu-id="e0386-203">In a server-rendered app, opening the same app in multiple browser screens typically doesn't translate into additional resource demands on the server.</span></span> <span data-ttu-id="e0386-204">在 Blazor Server 应用中，若服务器要管理浏览器屏幕，则每个浏览器屏幕均需要独立线路和组件状态的独立实例。</span><span class="sxs-lookup"><span data-stu-id="e0386-204">In a Blazor Server app, each browser screen requires a separate circuit and separate instances of component state to be managed by the server.</span></span>

<span data-ttu-id="e0386-205">Blazor 将关闭浏览器标签页或访问外部 URL 视为正常终止。</span><span class="sxs-lookup"><span data-stu-id="e0386-205">Blazor considers closing a browser tab or navigating to an external URL a *graceful* termination.</span></span> <span data-ttu-id="e0386-206">如果正常终止，则会立即释放线路和关联的资源。</span><span class="sxs-lookup"><span data-stu-id="e0386-206">In the event of a graceful termination, the circuit and associated resources are immediately released.</span></span> <span data-ttu-id="e0386-207">例如，由于网络中断，客户端也可能异常地断开连接。</span><span class="sxs-lookup"><span data-stu-id="e0386-207">A client may also disconnect non-gracefully, for instance due to a network interruption.</span></span> <span data-ttu-id="e0386-208">Blazor Server 会将断开连接的路线存储一段时间（可配置），以便客户端重新连接。</span><span class="sxs-lookup"><span data-stu-id="e0386-208">Blazor Server stores disconnected circuits for a configurable interval to allow the client to reconnect.</span></span>

<span data-ttu-id="e0386-209">Blazor Server 允许代码定义线路处理程序，后者允许在用户线路的状态发生更改时运行代码。</span><span class="sxs-lookup"><span data-stu-id="e0386-209">Blazor Server allows code to define a *circuit handler*, which allows running code on changes to the state of a user's circuit.</span></span> <span data-ttu-id="e0386-210">有关详细信息，请参阅 <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>。</span><span class="sxs-lookup"><span data-stu-id="e0386-210">For more information, see <xref:blazor/advanced-scenarios#blazor-server-circuit-handler>.</span></span>

### <a name="ui-latency"></a><span data-ttu-id="e0386-211">UI 延迟</span><span class="sxs-lookup"><span data-stu-id="e0386-211">UI Latency</span></span>

<span data-ttu-id="e0386-212">UI 延迟是指从启动操作到 UI 更新所需的时间。</span><span class="sxs-lookup"><span data-stu-id="e0386-212">UI latency is the time it takes from an initiated action to the time the UI is updated.</span></span> <span data-ttu-id="e0386-213">要使应用对用户进行响应，需要 UI 延迟值较小。</span><span class="sxs-lookup"><span data-stu-id="e0386-213">Smaller values for UI latency are imperative for an app to feel responsive to a user.</span></span> <span data-ttu-id="e0386-214">在 Blazor Server 应用中，每个操作都会发送到服务器并进行处理，然后发回 UI 差异。</span><span class="sxs-lookup"><span data-stu-id="e0386-214">In a Blazor Server app, each action is sent to the server, processed, and a UI diff is sent back.</span></span> <span data-ttu-id="e0386-215">因此，UI 延迟是网络延迟和处理操作时的服务器延迟的总和。</span><span class="sxs-lookup"><span data-stu-id="e0386-215">Consequently, UI latency is the sum of network latency and the server latency in processing the action.</span></span>

<span data-ttu-id="e0386-216">如果是仅用于专用公司网络的商业应用程序，用户通常不易感受到因网络延迟而导致的延迟。</span><span class="sxs-lookup"><span data-stu-id="e0386-216">For a business app that's limited to a private corporate network, the effect on user perceptions of latency due to network latency are usually imperceptible.</span></span> <span data-ttu-id="e0386-217">如果是通过 Internet 部署的应用，用户可能会更容易感受到延迟，用户分布广泛时感受尤为明显。</span><span class="sxs-lookup"><span data-stu-id="e0386-217">For an app deployed over the Internet, latency may become noticeable to users, particularly if users are widely distributed geographically.</span></span>

<span data-ttu-id="e0386-218">内存使用也会导致应用延迟。</span><span class="sxs-lookup"><span data-stu-id="e0386-218">Memory usage can also contribute to app latency.</span></span> <span data-ttu-id="e0386-219">内存使用率增大会导致垃圾收集频繁或内存分页到磁盘，两者均会降低应用性能，进而增大 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="e0386-219">Increased memory usage results in frequent garbage collection or paging memory to disk, both of which degrade app performance and consequently increase UI latency.</span></span>

<span data-ttu-id="e0386-220">Blazor Server 应用应降低网络延迟和内存使用率，从而优化以最大限度地降低 UI 延迟。</span><span class="sxs-lookup"><span data-stu-id="e0386-220">Blazor Server apps should be optimized to minimize UI latency by reducing network latency and memory usage.</span></span> <span data-ttu-id="e0386-221">有关测量网络延迟的方法，请参阅 <xref:blazor/host-and-deploy/server#measure-network-latency>。</span><span class="sxs-lookup"><span data-stu-id="e0386-221">For an approach to measuring network latency, see <xref:blazor/host-and-deploy/server#measure-network-latency>.</span></span> <span data-ttu-id="e0386-222">有关 SignalR 和 Blazor 的详细信息，请参阅以下内容：</span><span class="sxs-lookup"><span data-stu-id="e0386-222">For more information on SignalR and Blazor, see:</span></span>

* <xref:blazor/host-and-deploy/server>
* <xref:blazor/security/server/threat-mitigation>

### <a name="connection-to-the-server"></a><span data-ttu-id="e0386-223">连接到服务器</span><span class="sxs-lookup"><span data-stu-id="e0386-223">Connection to the server</span></span>

<span data-ttu-id="e0386-224">Blazor Server 应用需要与服务器建立有效的 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="e0386-224">Blazor Server apps require an active SignalR connection to the server.</span></span> <span data-ttu-id="e0386-225">如果连接丢失，应用会尝试重新连接到服务器。</span><span class="sxs-lookup"><span data-stu-id="e0386-225">If the connection is lost, the app attempts to reconnect to the server.</span></span> <span data-ttu-id="e0386-226">只要客户端的状态仍在服务器的内存中，客户端会话即可恢复，且不会失去状态。</span><span class="sxs-lookup"><span data-stu-id="e0386-226">As long as the client's state remains in the server's memory, the client session resumes without losing state.</span></span>

<span data-ttu-id="e0386-227">Blazor Server 应用预呈现以响应第一个客户端请求，这会在服务器上创建 UI 状态。</span><span class="sxs-lookup"><span data-stu-id="e0386-227">A Blazor Server app prerenders in response to the first client request, which creates the UI state on the server.</span></span> <span data-ttu-id="e0386-228">客户端尝试创建 SignalR 连接时，必须重新连接到同一服务器。</span><span class="sxs-lookup"><span data-stu-id="e0386-228">When the client attempts to create a SignalR connection, the client must reconnect to the same server.</span></span> <span data-ttu-id="e0386-229">使用多个后端服务器的 Blazor Server 应用应实现粘滞会话，从而建立 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="e0386-229">Blazor Server apps that use more than one backend server should implement *sticky sessions* for SignalR connections.</span></span>

<span data-ttu-id="e0386-230">我们建议将 [Azure SignalR 服务](/azure/azure-signalr)用于 Blazor Server 应用。</span><span class="sxs-lookup"><span data-stu-id="e0386-230">We recommend using the [Azure SignalR Service](/azure/azure-signalr) for Blazor Server apps.</span></span> <span data-ttu-id="e0386-231">该服务允许将 Blazor Server 应用扩展到大量并发 SignalR 连接。</span><span class="sxs-lookup"><span data-stu-id="e0386-231">The service allows for scaling up a Blazor Server app to a large number of concurrent SignalR connections.</span></span> <span data-ttu-id="e0386-232">可将服务的 `ServerStickyMode` 选项或配置值设置为 `Required`，从而为 Azure SignalR 服务启用粘滞会话。</span><span class="sxs-lookup"><span data-stu-id="e0386-232">Sticky sessions are enabled for the Azure SignalR Service by setting the service's `ServerStickyMode` option or configuration value to `Required`.</span></span> <span data-ttu-id="e0386-233">有关详细信息，请参阅 <xref:blazor/host-and-deploy/server#signalr-configuration>。</span><span class="sxs-lookup"><span data-stu-id="e0386-233">For more information, see <xref:blazor/host-and-deploy/server#signalr-configuration>.</span></span>

<span data-ttu-id="e0386-234">使用 IIS 时，粘滞会话通过应用程序请求路由启用。</span><span class="sxs-lookup"><span data-stu-id="e0386-234">When using IIS, sticky sessions are enabled with *Application Request Routing*.</span></span> <span data-ttu-id="e0386-235">有关详细信息，请参阅[使用应用程序请求路由实现 HTTP 负载均衡](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing)。</span><span class="sxs-lookup"><span data-stu-id="e0386-235">For more information, see [HTTP Load Balancing using Application Request Routing](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="e0386-236">其他资源</span><span class="sxs-lookup"><span data-stu-id="e0386-236">Additional resources</span></span>

* <xref:blazor/tooling>
* <xref:signalr/introduction>
* <xref:blazor/fundamentals/additional-scenarios>
* <xref:tutorials/signalr-blazor-webassembly>
