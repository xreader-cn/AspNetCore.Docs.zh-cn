---
title: 承载模型的 razor 组件
author: guardrex
description: 了解客户端 Blazor 和服务器端 ASP.NET Core Razor 组件承载模型。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 03/28/2019
uid: razor-components/hosting-models
ms.openlocfilehash: 8ed70117c94bf1a3e4c208f70310bbf0473bae44
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515413"
---
# <a name="razor-components-hosting-models"></a><span data-ttu-id="1082f-103">承载模型的 razor 组件</span><span class="sxs-lookup"><span data-stu-id="1082f-103">Razor Components hosting models</span></span>

<span data-ttu-id="1082f-104">通过[Daniel Roth](https://github.com/danroth27)</span><span class="sxs-lookup"><span data-stu-id="1082f-104">By [Daniel Roth](https://github.com/danroth27)</span></span>

<span data-ttu-id="1082f-105">Razor 组件是一个 web 框架，用于运行客户端上的浏览器中[WebAssembly](http://webassembly.org/)-基于.NET 运行时 (*Blazor*) 或 ASP.NET Core 中的服务器端 (*ASP.NET Core Razor组件*)。</span><span class="sxs-lookup"><span data-stu-id="1082f-105">Razor Components is a web framework designed to run client-side in the browser on a [WebAssembly](http://webassembly.org/)-based .NET runtime (*Blazor*) or server-side in ASP.NET Core (*ASP.NET Core Razor Components*).</span></span> <span data-ttu-id="1082f-106">无论托管模型、 应用和组件模型*保持不变*。</span><span class="sxs-lookup"><span data-stu-id="1082f-106">Regardless of the hosting model, the app and component models *remain the same*.</span></span> <span data-ttu-id="1082f-107">本文讨论了可用的托管模型：</span><span class="sxs-lookup"><span data-stu-id="1082f-107">This article discusses the available hosting models:</span></span>

* [<span data-ttu-id="1082f-108">客户端 Blazor</span><span class="sxs-lookup"><span data-stu-id="1082f-108">Client-side Blazor</span></span>](#client-side-hosting-model)
* [<span data-ttu-id="1082f-109">服务器端 ASP.NET Core Razor 组件</span><span class="sxs-lookup"><span data-stu-id="1082f-109">Server-side ASP.NET Core Razor Components</span></span>](#server-side-hosting-model)

## <a name="client-side-hosting-model"></a><span data-ttu-id="1082f-110">客户端托管模型</span><span class="sxs-lookup"><span data-stu-id="1082f-110">Client-side hosting model</span></span>

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

<span data-ttu-id="1082f-111">Blazor 的主体托管模型是 WebAssembly 上的浏览器中的正在运行客户端。</span><span class="sxs-lookup"><span data-stu-id="1082f-111">The principal hosting model for Blazor is running client-side in the browser on WebAssembly.</span></span> <span data-ttu-id="1082f-112">将 Blazor 应用、其依赖项以及 .NET 运行时下载到浏览器。</span><span class="sxs-lookup"><span data-stu-id="1082f-112">The Blazor app, its dependencies, and the .NET runtime are downloaded to the browser.</span></span> <span data-ttu-id="1082f-113">应用将在浏览器线程中直接执行。</span><span class="sxs-lookup"><span data-stu-id="1082f-113">The app is executed directly on the browser UI thread.</span></span> <span data-ttu-id="1082f-114">UI 更新和事件处理发生在同一进程内。</span><span class="sxs-lookup"><span data-stu-id="1082f-114">UI updates and event handling occur within the same process.</span></span> <span data-ttu-id="1082f-115">应用程序的资产部署到 web 服务器或服务能够向客户端提供静态内容的静态文件的方式。</span><span class="sxs-lookup"><span data-stu-id="1082f-115">The app's assets are deployed as static files to a web server or service capable of serving static content to clients.</span></span>

![Blazor 客户端：在浏览器在 UI 线程上运行 Blazor 应用。](hosting-models/_static/client-side.png)

<span data-ttu-id="1082f-117">若要创建一个 Blazor 应用使用客户端的托管模型，请使用以下模板之一：</span><span class="sxs-lookup"><span data-stu-id="1082f-117">To create a Blazor app using the client-side hosting model, use either of the following templates:</span></span>

* <span data-ttu-id="1082f-118">**Blazor** ([dotnet 新 blazor](/dotnet/core/tools/dotnet-new))&ndash;部署为一组静态文件。</span><span class="sxs-lookup"><span data-stu-id="1082f-118">**Blazor** ([dotnet new blazor](/dotnet/core/tools/dotnet-new)) &ndash; Deployed as a set of static files.</span></span>
* <span data-ttu-id="1082f-119">**（ASP.NET Core 托管） 的 Blazor** ([dotnet 新 blazorhosted](/dotnet/core/tools/dotnet-new))&ndash;由 ASP.NET Core 服务器托管。</span><span class="sxs-lookup"><span data-stu-id="1082f-119">**Blazor (ASP.NET Core Hosted)** ([dotnet new blazorhosted](/dotnet/core/tools/dotnet-new)) &ndash; Hosted by an ASP.NET Core server.</span></span> <span data-ttu-id="1082f-120">ASP.NET Core 应用提供给客户端 Blazor 应用。</span><span class="sxs-lookup"><span data-stu-id="1082f-120">The ASP.NET Core app serves the Blazor app to clients.</span></span> <span data-ttu-id="1082f-121">与服务器进行交互的客户端 Blazor 应用程序可以通过使用 web API 调用网络或[SignalR](xref:signalr/introduction)。</span><span class="sxs-lookup"><span data-stu-id="1082f-121">The client-side Blazor app can interact with the server over the network using web API calls or [SignalR](xref:signalr/introduction).</span></span>

<span data-ttu-id="1082f-122">这些模板包含*components.webassembly.js*处理的脚本：</span><span class="sxs-lookup"><span data-stu-id="1082f-122">The templates include the *components.webassembly.js* script that handles:</span></span>

* <span data-ttu-id="1082f-123">下载.NET 运行时、 应用和应用程序的依赖关系。</span><span class="sxs-lookup"><span data-stu-id="1082f-123">Downloading the .NET runtime, the app, and the app's dependencies.</span></span>
* <span data-ttu-id="1082f-124">若要运行该应用程序的运行时初始化。</span><span class="sxs-lookup"><span data-stu-id="1082f-124">Initialization of the runtime to run the app.</span></span>

<span data-ttu-id="1082f-125">客户端的托管模型提供了以下几个好处。</span><span class="sxs-lookup"><span data-stu-id="1082f-125">The client-side hosting model offers several benefits.</span></span> <span data-ttu-id="1082f-126">客户端 Blazor:</span><span class="sxs-lookup"><span data-stu-id="1082f-126">Client-side Blazor:</span></span>

* <span data-ttu-id="1082f-127">不具有任何.NET 服务器端依赖项。</span><span class="sxs-lookup"><span data-stu-id="1082f-127">Has no .NET server-side dependency.</span></span>
* <span data-ttu-id="1082f-128">充分利用客户端资源和功能。</span><span class="sxs-lookup"><span data-stu-id="1082f-128">Fully leverages client resources and capabilities.</span></span>
* <span data-ttu-id="1082f-129">卸载可从服务器到客户端。</span><span class="sxs-lookup"><span data-stu-id="1082f-129">Offloads work from the server to the client.</span></span>
* <span data-ttu-id="1082f-130">支持脱机方案。</span><span class="sxs-lookup"><span data-stu-id="1082f-130">Supports offline scenarios.</span></span>

<span data-ttu-id="1082f-131">有客户端托管的弊端。</span><span class="sxs-lookup"><span data-stu-id="1082f-131">There are downsides to client-side hosting.</span></span> <span data-ttu-id="1082f-132">客户端 Blazor:</span><span class="sxs-lookup"><span data-stu-id="1082f-132">Client-side Blazor:</span></span>

* <span data-ttu-id="1082f-133">将应用程序限制为浏览器的功能。</span><span class="sxs-lookup"><span data-stu-id="1082f-133">Restricts the app to the capabilities of the browser.</span></span>
* <span data-ttu-id="1082f-134">需要支持客户端硬件和软件 （例如，WebAssembly 支持）。</span><span class="sxs-lookup"><span data-stu-id="1082f-134">Requires capable client hardware and software (for example, WebAssembly support).</span></span>
* <span data-ttu-id="1082f-135">具有更大的下载大小和加载时间较长的应用。</span><span class="sxs-lookup"><span data-stu-id="1082f-135">Has a larger download size and longer app load time.</span></span>
* <span data-ttu-id="1082f-136">更小成熟的.NET 运行时和工具支持 (例如中的限制[.NET Standard](/dotnet/standard/net-standard)支持和调试)。</span><span class="sxs-lookup"><span data-stu-id="1082f-136">Has less mature .NET runtime and tooling support (for example, limitations in [.NET Standard](/dotnet/standard/net-standard) support and debugging).</span></span>

## <a name="server-side-hosting-model"></a><span data-ttu-id="1082f-137">服务器端托管模型</span><span class="sxs-lookup"><span data-stu-id="1082f-137">Server-side hosting model</span></span>

<span data-ttu-id="1082f-138">使用 ASP.NET Core Razor 组件服务器端的托管模型，在服务器上从 ASP.NET Core 应用中执行应用程序。</span><span class="sxs-lookup"><span data-stu-id="1082f-138">With the ASP.NET Core Razor Components server-side hosting model, the app is executed on the server from within an ASP.NET Core app.</span></span> <span data-ttu-id="1082f-139">通过处理 UI 更新、 事件处理和 JavaScript 调用[SignalR](xref:signalr/introduction)连接。</span><span class="sxs-lookup"><span data-stu-id="1082f-139">UI updates, event handling, and JavaScript calls are handled over a [SignalR](xref:signalr/introduction) connection.</span></span>

![ASP.NET Core Razor 组件服务器端：在浏览器与交互 （托管在 ASP.NET Core 应用内） 的应用的服务器上通过 SignalR 连接。](hosting-models/_static/server-side.png)

<span data-ttu-id="1082f-141">若要创建一个 Razor 组件应用使用服务器端的托管模型，使用 ASP.NET Core **Razor 组件**模板 ([dotnet 新 razorcomponents](/dotnet/core/tools/dotnet-new))。</span><span class="sxs-lookup"><span data-stu-id="1082f-141">To create a Razor Components app using the server-side hosting model, use the ASP.NET Core **Razor Components** template ([dotnet new razorcomponents](/dotnet/core/tools/dotnet-new)).</span></span> <span data-ttu-id="1082f-142">ASP.NET Core 应用托管 Razor 组件服务器端应用程序，并设置 SignalR 终结点，客户端连接。</span><span class="sxs-lookup"><span data-stu-id="1082f-142">The ASP.NET Core app hosts the Razor Components server-side app and sets up the SignalR endpoint where clients connect.</span></span> <span data-ttu-id="1082f-143">ASP.NET Core 应用引用应用程序的`Startup`要添加类：</span><span class="sxs-lookup"><span data-stu-id="1082f-143">The ASP.NET Core app references the app's `Startup` class to add:</span></span>

* <span data-ttu-id="1082f-144">服务器端 Razor 组件服务。</span><span class="sxs-lookup"><span data-stu-id="1082f-144">Server-side Razor Components services.</span></span>
* <span data-ttu-id="1082f-145">在应用请求处理管道中。</span><span class="sxs-lookup"><span data-stu-id="1082f-145">The app to the request handling pipeline.</span></span>

[!code-csharp[](hosting-models/samples_snapshot/Startup.cs?highlight=5,27)]

<span data-ttu-id="1082f-146">*Components.server.js*脚本&dagger;建立客户端连接。</span><span class="sxs-lookup"><span data-stu-id="1082f-146">The *components.server.js* script&dagger; establishes the client connection.</span></span> <span data-ttu-id="1082f-147">它是应用程序负责保持和还原应用程序状态，根据需要 （例如，发生时丢失的网络连接）。</span><span class="sxs-lookup"><span data-stu-id="1082f-147">It's the app's responsibility to persist and restore app state as required (for example, in the event of a lost network connection).</span></span>

<span data-ttu-id="1082f-148">服务器端的托管模型提供了以下几个好处。</span><span class="sxs-lookup"><span data-stu-id="1082f-148">The server-side hosting model offers several benefits.</span></span> <span data-ttu-id="1082f-149">服务器端 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="1082f-149">Server-side Razor Components:</span></span>

* <span data-ttu-id="1082f-150">比客户端 Blazor 应用程序的显著变小应用程序大小和速度更快加载。</span><span class="sxs-lookup"><span data-stu-id="1082f-150">Have a significantly smaller app size than a client-side Blazor app and load much faster.</span></span>
* <span data-ttu-id="1082f-151">可以充分利用的服务器功能，其中包括使用任何.NET Core 兼容的 Api。</span><span class="sxs-lookup"><span data-stu-id="1082f-151">Can take full advantage of server capabilities, including using any .NET Core compatible APIs.</span></span>
* <span data-ttu-id="1082f-152">.NET Core 上运行的服务器上，因此现有的.NET 工具，如调试，按预期方式工作。</span><span class="sxs-lookup"><span data-stu-id="1082f-152">Run on .NET Core on the server, so existing .NET tooling, such as debugging, works as expected.</span></span>
* <span data-ttu-id="1082f-153">适用于瘦客户端 （例如，浏览器不支持 WebAssembly 和资源约束设备）。</span><span class="sxs-lookup"><span data-stu-id="1082f-153">Works with thin clients (for example, browsers that don't support WebAssembly and resource constrained devices).</span></span>
* <span data-ttu-id="1082f-154">.NET /C#的基本代码，包括应用程序的组件代码不提供给客户端。</span><span class="sxs-lookup"><span data-stu-id="1082f-154">.NET/C# code base, including the app's component code, isn't served to clients.</span></span>

<span data-ttu-id="1082f-155">有服务器端托管的弊端。</span><span class="sxs-lookup"><span data-stu-id="1082f-155">There are downsides to server-side hosting.</span></span> <span data-ttu-id="1082f-156">服务器端 Razor 组件：</span><span class="sxs-lookup"><span data-stu-id="1082f-156">Server-side Razor Components:</span></span>

* <span data-ttu-id="1082f-157">具有较高的延迟：每个用户交互涉及到的网络跃点。</span><span class="sxs-lookup"><span data-stu-id="1082f-157">Have higher latency: Every user interaction involves a network hop.</span></span>
* <span data-ttu-id="1082f-158">产品/服务没有脱机支持：如果客户端连接失败，应用将停止工作。</span><span class="sxs-lookup"><span data-stu-id="1082f-158">Offer no offline support: If the client connection fails, the app stops working.</span></span>
* <span data-ttu-id="1082f-159">减少可伸缩性：服务器必须管理多个客户端连接，并处理客户端状态。</span><span class="sxs-lookup"><span data-stu-id="1082f-159">Have reduced scalability: The server must manage multiple client connections and handle client state.</span></span>
* <span data-ttu-id="1082f-160">需要 ASP.NET Core 服务器来提供应用。</span><span class="sxs-lookup"><span data-stu-id="1082f-160">Require an ASP.NET Core server to serve the app.</span></span> <span data-ttu-id="1082f-161">不使用 （例如，从 CDN) 的服务器的部署不能。</span><span class="sxs-lookup"><span data-stu-id="1082f-161">Deployment without a server (for example, from a CDN) isn't possible.</span></span>

<span data-ttu-id="1082f-162">&dagger;*Components.server.js*脚本发布到以下路径： *bin / {调试 |版本} / {目标框架} /publish/ {应用程序名称}。应用/dist/框架 （_f)*。</span><span class="sxs-lookup"><span data-stu-id="1082f-162">&dagger;The *components.server.js* script is published to the following path: *bin/{Debug|Release}/{TARGET FRAMEWORK}/publish/{APPLICATION NAME}.App/dist/_framework*.</span></span>
