---
title: 'ASP.NET Core :::no-loc(Blazor Server)::: 的威胁缓解指南'
author: guardrex
description: '了解如何缓解 :::no-loc(Blazor Server)::: 应用面临的安全威胁。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/05/2020
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: blazor/security/server/threat-mitigation
ms.openlocfilehash: 5c3a002a8e3df030d53c8625597342a68ca0d4b5
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93055407"
---
# <a name="threat-mitigation-guidance-for-aspnet-core-no-locblazor-server"></a><span data-ttu-id="3d8fc-103">ASP.NET Core :::no-loc(Blazor Server)::: 的威胁缓解指南</span><span class="sxs-lookup"><span data-stu-id="3d8fc-103">Threat mitigation guidance for ASP.NET Core :::no-loc(Blazor Server):::</span></span>

<span data-ttu-id="3d8fc-104">作者：[Javier Calvarro Nelson](https://github.com/javiercn)</span><span class="sxs-lookup"><span data-stu-id="3d8fc-104">By [Javier Calvarro Nelson](https://github.com/javiercn)</span></span>

<span data-ttu-id="3d8fc-105">:::no-loc(Blazor Server)::: 应用采用有状态数据处理模型，其中服务器和客户端保持长期的关系。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-105">:::no-loc(Blazor Server)::: apps adopt a *stateful* data processing model, where the server and client maintain a long-lived relationship.</span></span> <span data-ttu-id="3d8fc-106">持久状态通过[线路](xref:blazor/state-management)维持，该线路可在同样可能长期存在的连接中持续。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-106">The persistent state is maintained by a [circuit](xref:blazor/state-management), which can span connections that are also potentially long-lived.</span></span>

<span data-ttu-id="3d8fc-107">当用户访问 :::no-loc(Blazor Server)::: 站点时，服务器会在服务器内存中创建一条线路。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-107">When a user visits a :::no-loc(Blazor Server)::: site, the server creates a circuit in the server's memory.</span></span> <span data-ttu-id="3d8fc-108">该线路向浏览器指示要呈现的内容和对事件的响应，例如当用户在 UI 中选择按钮时。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-108">The circuit indicates to the browser what content to render and responds to events, such as when the user selects a button in the UI.</span></span> <span data-ttu-id="3d8fc-109">为执行这些操作，线路会在用户的浏览器中调用 JavaScript 函数，在服务器上调用 .NET 方法。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-109">To perform these actions, a circuit invokes JavaScript functions in the user's browser and .NET methods on the server.</span></span> <span data-ttu-id="3d8fc-110">这种基于 JavaScript 的双向交互被称为 [JavaScript 互操作（JS 互操作）](xref:blazor/call-javascript-from-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-110">This two-way JavaScript-based interaction is referred to as [JavaScript interop (JS interop)](xref:blazor/call-javascript-from-dotnet).</span></span>

<span data-ttu-id="3d8fc-111">由于 JS 互操作发生在 Internet 上，且客户端使用远程浏览器，因此 :::no-loc(Blazor Server)::: 应用中的大多数 Web 应用安全问题是一样的。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-111">Because JS interop occurs over the Internet and the client uses a remote browser, :::no-loc(Blazor Server)::: apps share most web app security concerns.</span></span> <span data-ttu-id="3d8fc-112">本主题介绍 :::no-loc(Blazor Server)::: 应用面临的常见威胁，并提供重点讲解面向 Internet 的应用的威胁缓解指南。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-112">This topic describes common threats to :::no-loc(Blazor Server)::: apps and provides threat mitigation guidance focused on Internet-facing apps.</span></span>

<span data-ttu-id="3d8fc-113">在受限制的环境中（例如公司网络或 Intranet），该缓解指南的部分内容：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-113">In constrained environments, such as inside corporate networks or intranets, some of the mitigation guidance either:</span></span>

* <span data-ttu-id="3d8fc-114">不适用于受限制的环境。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-114">Doesn't apply in the constrained environment.</span></span>
* <span data-ttu-id="3d8fc-115">由于受限制的环境中的安全风险很低，因此不值得花费成本来实施。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-115">Isn't worth the cost to implement because the security risk is low in a constrained environment.</span></span>

## <a name="no-locblazor-and-shared-state"></a><span data-ttu-id="3d8fc-116">:::no-loc(Blazor)::: 和共享状态</span><span class="sxs-lookup"><span data-stu-id="3d8fc-116">:::no-loc(Blazor)::: and shared state</span></span>

[!INCLUDE[](~/includes/blazor-security/blazor-shared-state.md)]

## <a name="resource-exhaustion"></a><span data-ttu-id="3d8fc-117">资源耗尽</span><span class="sxs-lookup"><span data-stu-id="3d8fc-117">Resource exhaustion</span></span>

<span data-ttu-id="3d8fc-118">当客户端与服务器交互并导致服务器使用过多资源时，可能会发生资源耗尽的情况。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-118">Resource exhaustion can occur when a client interacts with the server and causes the server to consume excessive resources.</span></span> <span data-ttu-id="3d8fc-119">资源过度消耗会主要影响：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-119">Excessive resource consumption primarily affects:</span></span>

* [<span data-ttu-id="3d8fc-120">CPU</span><span class="sxs-lookup"><span data-stu-id="3d8fc-120">CPU</span></span>](#cpu)
* [<span data-ttu-id="3d8fc-121">内存</span><span class="sxs-lookup"><span data-stu-id="3d8fc-121">Memory</span></span>](#memory)
* [<span data-ttu-id="3d8fc-122">客户端连接</span><span class="sxs-lookup"><span data-stu-id="3d8fc-122">Client connections</span></span>](#client-connections)

<span data-ttu-id="3d8fc-123">拒绝服务 (DoS) 攻击通常企图耗尽应用或服务器的资源。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-123">Denial of service (DoS) attacks usually seek to exhaust an app or server's resources.</span></span> <span data-ttu-id="3d8fc-124">然而，资源耗尽并不一定是系统受到攻击而导致的。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-124">However, resource exhaustion isn't necessarily the result of an attack on the system.</span></span> <span data-ttu-id="3d8fc-125">例如，如果资源有限而用户需求高，则资源可能会被耗尽。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-125">For example, finite resources can be exhausted due to high user demand.</span></span> <span data-ttu-id="3d8fc-126">DoS 将在[拒绝服务 (DoS) 攻击](#denial-of-service-dos-attacks)部分得到进一步讲解。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-126">DoS is covered further in the [Denial of service (DoS) attacks](#denial-of-service-dos-attacks) section.</span></span>

<span data-ttu-id="3d8fc-127">:::no-loc(Blazor)::: 框架外部的资源，例如数据库和文件句柄（用于读取和写入文件），也可能会耗尽资源。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-127">Resources external to the :::no-loc(Blazor)::: framework, such as databases and file handles (used to read and write files), may also experience resource exhaustion.</span></span> <span data-ttu-id="3d8fc-128">有关详细信息，请参阅 <xref:performance/performance-best-practices>。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-128">For more information, see <xref:performance/performance-best-practices>.</span></span>

### <a name="cpu"></a><span data-ttu-id="3d8fc-129">CPU</span><span class="sxs-lookup"><span data-stu-id="3d8fc-129">CPU</span></span>

<span data-ttu-id="3d8fc-130">当一个或多个客户端强制服务器执行大量消耗 CPU 的工作时，可能会发生 CPU 耗尽的情况。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-130">CPU exhaustion can occur when one or more clients force the server to perform intensive CPU work.</span></span>

<span data-ttu-id="3d8fc-131">例如，假设有一个 :::no-loc(Blazor Server)::: 应用要计算 Fibonnacci 编号。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-131">For example, consider a :::no-loc(Blazor Server)::: app that calculates a *Fibonnacci number*.</span></span> <span data-ttu-id="3d8fc-132">Fibonnacci 编号是根据 Fibonnacci 序列生成的，其中序列中的每个编号都是前两个编号之和。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-132">A Fibonnacci number is produced from a Fibonnacci sequence, where each number in the sequence is the sum of the two preceding numbers.</span></span> <span data-ttu-id="3d8fc-133">获得答案所需的工作量取决于序列的长度和初始值的大小。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-133">The amount of work required to reach the answer depends on the length of the sequence and the size of the initial value.</span></span> <span data-ttu-id="3d8fc-134">如果应用不对客户端的请求进行限制，CPU 密集型计算可能会占用 CPU 的时间，并降低其他任务的性能。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-134">If the app doesn't place limits on a client's request, the CPU-intensive calculations may dominate the CPU's time and diminish the performance of other tasks.</span></span> <span data-ttu-id="3d8fc-135">资源过度消耗是一项影响可用性的安全问题。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-135">Excessive resource consumption is a security concern impacting availability.</span></span>

<span data-ttu-id="3d8fc-136">而 CPU 耗尽是所有面向公众的应用面临的一个问题。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-136">CPU exhaustion is a concern for all public-facing apps.</span></span> <span data-ttu-id="3d8fc-137">在常规的 Web 应用中，请求和连接会超时（这用于保障安全），但是 :::no-loc(Blazor Server)::: 应用不提供这样的安全措施。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-137">In regular web apps, requests and connections time out as a safeguard, but :::no-loc(Blazor Server)::: apps don't provide the same safeguards.</span></span> <span data-ttu-id="3d8fc-138">:::no-loc(Blazor Server)::: 应用必须包含适当的检查和限制，然后再执行可能会大量消耗 CPU 的工作。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-138">:::no-loc(Blazor Server)::: apps must include appropriate checks and limits before performing potentially CPU-intensive work.</span></span>

### <a name="memory"></a><span data-ttu-id="3d8fc-139">内存</span><span class="sxs-lookup"><span data-stu-id="3d8fc-139">Memory</span></span>

<span data-ttu-id="3d8fc-140">当一个或多个客户端强制服务器消耗大量内存时，可能会发生内存耗尽的情况。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-140">Memory exhaustion can occur when one or more clients force the server to consume a large amount of memory.</span></span>

<span data-ttu-id="3d8fc-141">例如，假设有一个 :::no-loc(Blazor)::: 服务器端应用，它的组件会接受和显示一个项列表。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-141">For example, consider a :::no-loc(Blazor):::-server side app with a component that accepts and displays a list of items.</span></span> <span data-ttu-id="3d8fc-142">如果 :::no-loc(Blazor)::: 应用不限制允许的项数或返回给客户端的项数，则服务器的内存可能主要由内存密集型处理和呈现占用，以致于降低服务器性能。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-142">If the :::no-loc(Blazor)::: app doesn't place limits on the number of items allowed or the number of items rendered back to the client, the memory-intensive processing and rendering may dominate the server's memory to the point where performance of the server suffers.</span></span> <span data-ttu-id="3d8fc-143">服务器可能会崩溃，或者速度慢到看起来已经崩溃的程度。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-143">The server may crash or slow to the point that it appears to have crashed.</span></span>

<span data-ttu-id="3d8fc-144">请思考下面的场景，维持和显示一个项列表，而该场景可能会耗尽服务器上的内存：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-144">Consider the following scenario for maintaining and displaying a list of items that pertain to a potential memory exhaustion scenario on the server:</span></span>

* <span data-ttu-id="3d8fc-145">`List<MyItem>` 属性或字段中的项使用服务器的内存。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-145">The items in a `List<MyItem>` property or field use the server's memory.</span></span> <span data-ttu-id="3d8fc-146">如果应用允许项列表无限扩充，则存在服务器内存不足的风险。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-146">If the app allows the list of items to grow unbounded, there's a risk of the server running out of memory.</span></span> <span data-ttu-id="3d8fc-147">内存不足会导致当前会话结束（崩溃），并导致该服务器实例中的所有并发会话收到“内存不足”异常。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-147">Running out of memory causes the current session to end (crash) and all of the concurrent sessions in that server instance receive an out-of-memory exception.</span></span> <span data-ttu-id="3d8fc-148">为防止这种情况发生，应用必须使用一种数据结构来对并发用户施加项限制。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-148">To prevent this scenario from occurring, the app must use a data structure that imposes an item limit on concurrent users.</span></span>
* <span data-ttu-id="3d8fc-149">如果呈现时不采用分页方案，则 UI 中不可见的对象将在服务器上占用额外的内存。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-149">If a paging scheme isn't used for rendering, the server uses additional memory for objects that aren't visible in the UI.</span></span> <span data-ttu-id="3d8fc-150">如果不对项数进行限制，内存需求可能会导致可用服务器内存被耗尽。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-150">Without a limit on the number of items, memory demands may exhaust the available server memory.</span></span> <span data-ttu-id="3d8fc-151">若要防止这种情况，请使用以下方法之一：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-151">To prevent this scenario, use one of the following approaches:</span></span>
  * <span data-ttu-id="3d8fc-152">在呈现时使用分页列表。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-152">Use paginated lists when rendering.</span></span>
  * <span data-ttu-id="3d8fc-153">仅显示前 100 到 1000 项，并要求用户输入搜索条件来查找所显示项之外的项。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-153">Only display the first 100 to 1,000 items and require the user to enter search criteria to find items beyond the items displayed.</span></span>
  * <span data-ttu-id="3d8fc-154">对于更高级的呈现方案，实现支持虚拟化的列表或网格。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-154">For a more advanced rendering scenario, implement lists or grids that support *virtualization*.</span></span> <span data-ttu-id="3d8fc-155">使用虚拟化时，列表只呈现用户当前可查看的部分项。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-155">Using virtualization, lists only render a subset of items currently visible to the user.</span></span> <span data-ttu-id="3d8fc-156">当用户与 UI 中的滚动条交互时，组件只呈现显示所需的项。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-156">When the user interacts with the scrollbar in the UI, the component renders only those items required for display.</span></span> <span data-ttu-id="3d8fc-157">当前不需要显示的项可保存在辅助存储器中，这是理想的方法。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-157">The items that aren't currently required for display can be held in secondary storage, which is the ideal approach.</span></span> <span data-ttu-id="3d8fc-158">未显示的项也可保存在内存中，这种方法不太理想。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-158">Undisplayed items can also be held in memory, which is less ideal.</span></span>

<span data-ttu-id="3d8fc-159">:::no-loc(Blazor Server)::: 应用为有状态应用（如 WPF、Windows 窗体或 :::no-loc(Blazor WebAssembly):::）提供了与其他 UI 框架类似的编程模型。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-159">:::no-loc(Blazor Server)::: apps offer a similar programming model to other UI frameworks for stateful apps, such as WPF, Windows Forms, or :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="3d8fc-160">主要区别是，在一些 UI 框架中，应用消耗的内存属于客户端，只影响单个客户端。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-160">The main difference is that in several of the UI frameworks the memory consumed by the app belongs to the client and only affects that individual client.</span></span> <span data-ttu-id="3d8fc-161">例如，:::no-loc(Blazor WebAssembly)::: 应用完全在客户端上运行，只使用客户端内存资源。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-161">For example, a :::no-loc(Blazor WebAssembly)::: app runs entirely on the client and only uses client memory resources.</span></span> <span data-ttu-id="3d8fc-162">在 :::no-loc(Blazor Server)::: 方案中，应用消耗的内存属于服务器，并在服务器实例上的客户端之间共享。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-162">In the :::no-loc(Blazor Server)::: scenario, the memory consumed by the app belongs to the server and is shared among clients on the server instance.</span></span>

<span data-ttu-id="3d8fc-163">所有 :::no-loc(Blazor Server)::: 应用都要考虑到服务器端内存需求。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-163">Server-side memory demands are a consideration for all :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="3d8fc-164">但是，大多数 Web 应用都是无状态的，在返回响应时，处理请求时使用的内存会被释放。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-164">However, most web apps are stateless, and the memory used while processing a request is released when the response is returned.</span></span> <span data-ttu-id="3d8fc-165">一般建议是，禁止客户端分配未绑定的内存量，就像在长期维持客户端连接的其他任何服务器端应用中一样。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-165">As a general recommendation, don't permit clients to allocate an unbound amount of memory as in any other server-side app that persists client connections.</span></span> <span data-ttu-id="3d8fc-166">在单个请求被处理后，:::no-loc(Blazor Server)::: 应用消耗的内存还要等很长时间才会被释放。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-166">The memory consumed by a :::no-loc(Blazor Server)::: app persists for a longer time than a single request.</span></span>

> [!NOTE]
> <span data-ttu-id="3d8fc-167">在开发过程中，可使用探查器或捕获跟踪来评估客户端的内存需求。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-167">During development, a profiler can be used or a trace captured to assess memory demands of clients.</span></span> <span data-ttu-id="3d8fc-168">探查器或跟踪不会捕获分配给特定客户端的内存。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-168">A profiler or trace won't capture the memory allocated to a specific client.</span></span> <span data-ttu-id="3d8fc-169">若要在开发期间捕获特定客户端的内存使用情况，请捕获转储并检查根在用户线路上的所有对象的内存需求。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-169">To capture the memory use of a specific client during development, capture a dump and examine the memory demand of all the objects rooted at a user's circuit.</span></span>

### <a name="client-connections"></a><span data-ttu-id="3d8fc-170">客户端连接</span><span class="sxs-lookup"><span data-stu-id="3d8fc-170">Client connections</span></span>

<span data-ttu-id="3d8fc-171">当一个或多个客户端打开太多到服务器的并发连接，导致其他客户端无法建立新连接时，可能会出现连接耗尽的情况。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-171">Connection exhaustion can occur when one or more clients open too many concurrent connections to the server, preventing other clients from establishing new connections.</span></span>

<span data-ttu-id="3d8fc-172">:::no-loc(Blazor)::: 客户端会为每个会话建立一个连接，并且只要浏览器窗口不关闭，连接一直打开。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-172">:::no-loc(Blazor)::: clients establish a single connection per session and keep the connection open for as long as the browser window is open.</span></span> <span data-ttu-id="3d8fc-173">在服务器上维护所有连接的需求并不特定于 :::no-loc(Blazor)::: 应用。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-173">The demands on the server of maintaining all of the connections isn't specific to :::no-loc(Blazor)::: apps.</span></span> <span data-ttu-id="3d8fc-174">鉴于连接是持久的且 :::no-loc(Blazor Server)::: 应用具有状态，连接耗尽更可能影响应用的可用性。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-174">Given the persistent nature of the connections and the stateful nature of :::no-loc(Blazor Server)::: apps, connection exhaustion is a greater risk to availability of the app.</span></span>

<span data-ttu-id="3d8fc-175">默认情况下，每位用户可在 :::no-loc(Blazor Server)::: 应用上建立任意数量的连接。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-175">By default, there's no limit on the number of connections per user for a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="3d8fc-176">如果应用要求设定连接限制，请采用下面一种或多种方法：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-176">If the app requires a connection limit, take one or more of the following approaches:</span></span>

* <span data-ttu-id="3d8fc-177">要求进行身份验证，这自然会限制未经授权的用户连接到应用的能力。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-177">Require authentication, which naturally limits the ability of unauthorized users to connect to the app.</span></span> <span data-ttu-id="3d8fc-178">若要使此方案有效，必须防止用户随意预配新用户。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-178">For this scenario to be effective, users must be prevented from provisioning new users at will.</span></span>
* <span data-ttu-id="3d8fc-179">限制每位用户的连接数。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-179">Limit the number of connections per user.</span></span> <span data-ttu-id="3d8fc-180">限制连接数可通过以下方法来实现。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-180">Limiting connections can be accomplished via the following approaches.</span></span> <span data-ttu-id="3d8fc-181">请在允许合法用户访问应用时小心谨慎（例如，当基于客户端的 IP 地址设定连接限制时）。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-181">Exercise care to allow legitimate users to access the app (for example, when a connection limit is established based on the client's IP address).</span></span>
  * <span data-ttu-id="3d8fc-182">在应用级别：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-182">At the app level:</span></span>
    * <span data-ttu-id="3d8fc-183">终结点路由扩展性。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-183">Endpoint routing extensibility.</span></span>
    * <span data-ttu-id="3d8fc-184">需要身份验证才能连接到应用并跟踪每位用户的活动会话。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-184">Require authentication to connect to the app and keep track of the active sessions per user.</span></span>
    * <span data-ttu-id="3d8fc-185">达到限制后拒绝新会话。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-185">Reject new sessions upon reaching a limit.</span></span>
    * <span data-ttu-id="3d8fc-186">代理 WebSocket 通过使用代理连接到应用，例如使用 [Azure :::no-loc(SignalR)::: 服务](/azure/azure-signalr/signalr-overview)，它将从客户端到应用的连接进行多路复用处理。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-186">Proxy WebSocket connections to an app through the use of a proxy, such as the [Azure :::no-loc(SignalR)::: Service](/azure/azure-signalr/signalr-overview) that multiplexes connections from clients to an app.</span></span> <span data-ttu-id="3d8fc-187">这样，应用拥有的连接容量比单个客户端可建立的大，从而防止客户端耗尽与服务器的连接。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-187">This provides an app with greater connection capacity than a single client can establish, preventing a client from exhausting the connections to the server.</span></span>
  * <span data-ttu-id="3d8fc-188">在服务器级别：先使用代理/网关，再使用应用。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-188">At the server level: Use a proxy/gateway in front of the app.</span></span> <span data-ttu-id="3d8fc-189">例如，通过 [Azure Front Door](/azure/frontdoor/front-door-overview)，可定义、管理和监视 Web 流量到应用的全局路由。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-189">For example, [Azure Front Door](/azure/frontdoor/front-door-overview) enables you to define, manage, and monitor the global routing of web traffic to an app.</span></span>

## <a name="denial-of-service-dos-attacks"></a><span data-ttu-id="3d8fc-190">拒绝服务 (DoS) 攻击</span><span class="sxs-lookup"><span data-stu-id="3d8fc-190">Denial of service (DoS) attacks</span></span>

<span data-ttu-id="3d8fc-191">拒绝服务 (DoS) 攻击包括客户端导致服务器耗尽其一个或多个资源，从而使应用不可用。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-191">Denial of service (DoS) attacks involve a client causing the server to exhaust one or more of its resources making the app unavailable.</span></span> <span data-ttu-id="3d8fc-192">:::no-loc(Blazor Server)::: 应用包含一些默认限制，并依赖其他 ASP.NET Core 和 :::no-loc(SignalR)::: 限制来防范 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions> 上设置的 DoS 攻击。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-192">:::no-loc(Blazor Server)::: apps include some default limits and rely on other ASP.NET Core and :::no-loc(SignalR)::: limits to protect against DoS attacks that are set on <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions>.</span></span>

| <span data-ttu-id="3d8fc-193">:::no-loc(Blazor Server)::: 应用限制</span><span class="sxs-lookup"><span data-stu-id="3d8fc-193">:::no-loc(Blazor Server)::: app limit</span></span> | <span data-ttu-id="3d8fc-194">描述</span><span class="sxs-lookup"><span data-stu-id="3d8fc-194">Description</span></span> | <span data-ttu-id="3d8fc-195">默认</span><span class="sxs-lookup"><span data-stu-id="3d8fc-195">Default</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitMaxRetained> | <span data-ttu-id="3d8fc-196">给定服务器在内存中一次保留的断开连接的线路数上限。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-196">Maximum number of disconnected circuits that a given server holds in memory at a time.</span></span> | <span data-ttu-id="3d8fc-197">100</span><span class="sxs-lookup"><span data-stu-id="3d8fc-197">100</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DisconnectedCircuitRetentionPeriod> | <span data-ttu-id="3d8fc-198">断开连接的线路被移除前在内存中保留的最长时间。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-198">Maximum amount of time a disconnected circuit is held in memory before being torn down.</span></span> | <span data-ttu-id="3d8fc-199">3 分钟</span><span class="sxs-lookup"><span data-stu-id="3d8fc-199">3 minutes</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout> | <span data-ttu-id="3d8fc-200">服务器在使异步 JavaScript 函数调用超时之前等待的最长时间。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-200">Maximum amount of time the server waits before timing out an asynchronous JavaScript function invocation.</span></span> | <span data-ttu-id="3d8fc-201">1 分钟</span><span class="sxs-lookup"><span data-stu-id="3d8fc-201">1 minute</span></span> |
| <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches> | <span data-ttu-id="3d8fc-202">在给定时间内，服务器在内存中对每条线路保留用以支持重新连接可靠的未确认的呈现批处理的最大数量。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-202">Maximum number of unacknowledged render batches the server keeps in memory per circuit at a given time to support robust reconnection.</span></span> <span data-ttu-id="3d8fc-203">达到限制后，服务器会停止生成新的呈现批处理，直到客户端确认了一个或多个批处理。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-203">After reaching the limit, the server stops producing new render batches until one or more batches have been acknowledged by the client.</span></span> | <span data-ttu-id="3d8fc-204">10</span><span class="sxs-lookup"><span data-stu-id="3d8fc-204">10</span></span> |

<span data-ttu-id="3d8fc-205">使用 <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubConnectionContextOptions> 设置单个传入中心消息的最大消息大小。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-205">Set the maximum message size of a single incoming hub message with <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubConnectionContextOptions>.</span></span>

| <span data-ttu-id="3d8fc-206">:::no-loc(SignalR)::: 和 ASP.NET Core 限制</span><span class="sxs-lookup"><span data-stu-id="3d8fc-206">:::no-loc(SignalR)::: and ASP.NET Core limit</span></span> | <span data-ttu-id="3d8fc-207">描述</span><span class="sxs-lookup"><span data-stu-id="3d8fc-207">Description</span></span> | <span data-ttu-id="3d8fc-208">默认</span><span class="sxs-lookup"><span data-stu-id="3d8fc-208">Default</span></span> |
| --- | --- | --- |
| <xref:Microsoft.AspNetCore.:::no-loc(SignalR):::.HubConnectionContextOptions.MaximumReceiveMessageSize?displayProperty=nameWithType> | <span data-ttu-id="3d8fc-209">单个消息的大小。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-209">Message size for an individual message.</span></span> | <span data-ttu-id="3d8fc-210">32 KB</span><span class="sxs-lookup"><span data-stu-id="3d8fc-210">32 KB</span></span> |

## <a name="interactions-with-the-browser-client"></a><span data-ttu-id="3d8fc-211">与浏览器（客户端）的交互</span><span class="sxs-lookup"><span data-stu-id="3d8fc-211">Interactions with the browser (client)</span></span>

<span data-ttu-id="3d8fc-212">客户端通过 JS 互操作事件调度和呈现完成来与服务器交互。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-212">A client interacts with the server through JS interop event dispatching and render completion.</span></span> <span data-ttu-id="3d8fc-213">JS 互操作通信在 JavaScript 和 .NET 之间双向进行：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-213">JS interop communication goes both ways between JavaScript and .NET:</span></span>

* <span data-ttu-id="3d8fc-214">浏览器事件以异步方式从客户端发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-214">Browser events are dispatched from the client to the server in an asynchronous fashion.</span></span>
* <span data-ttu-id="3d8fc-215">服务器以异步方式进行响应，从而根据需要重新呈现 UI。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-215">The server responds asynchronously rerendering the UI as necessary.</span></span>

### <a name="javascript-functions-invoked-from-net"></a><span data-ttu-id="3d8fc-216">从 .NET 调用的 JavaScript 函数</span><span class="sxs-lookup"><span data-stu-id="3d8fc-216">JavaScript functions invoked from .NET</span></span>

<span data-ttu-id="3d8fc-217">对于从 .NET 方法到 JavaScript 的调用：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-217">For calls from .NET methods to JavaScript:</span></span>

* <span data-ttu-id="3d8fc-218">所有调用都具有可配置的超时时间，在此超时之后它们将失败，并向调用方返回 <xref:System.OperationCanceledException>。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-218">All invocations have a configurable timeout after which they fail, returning a <xref:System.OperationCanceledException> to the caller.</span></span>
  * <span data-ttu-id="3d8fc-219">调用 (<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>) 的默认超时为 1 分钟。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-219">There's a default timeout for the calls (<xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>) of one minute.</span></span> <span data-ttu-id="3d8fc-220">若要配置此限制，请参阅 <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-220">To configure this limit, see <xref:blazor/call-javascript-from-dotnet#harden-js-interop-calls>.</span></span>
  * <span data-ttu-id="3d8fc-221">可提供取消令牌，根据每次调用来控制相应的取消操作。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-221">A cancellation token can be provided to control the cancellation on a per-call basis.</span></span> <span data-ttu-id="3d8fc-222">如果可能，请采用默认调用超时；如果提供了取消令牌，则采用对客户端的任何调用的时间限制。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-222">Rely on the default call timeout where possible and time-bound any call to the client if a cancellation token is provided.</span></span>
* <span data-ttu-id="3d8fc-223">不能信任 JavaScript 调用的结果。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-223">The result of a JavaScript call can't be trusted.</span></span> <span data-ttu-id="3d8fc-224">在浏览器中运行的 :::no-loc(Blazor)::: 应用客户端会搜索要调用的 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-224">The :::no-loc(Blazor)::: app client running in the browser searches for the JavaScript function to invoke.</span></span> <span data-ttu-id="3d8fc-225">函数会被调用，并生成结果或错误。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-225">The function is invoked, and either the result or an error is produced.</span></span> <span data-ttu-id="3d8fc-226">恶意客户端可能会尝试：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-226">A malicious client can attempt to:</span></span>
  * <span data-ttu-id="3d8fc-227">通过从 JavaScript 函数返回错误，在应用中引发问题。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-227">Cause an issue in the app by returning an error from the JavaScript function.</span></span>
  * <span data-ttu-id="3d8fc-228">通过从 JavaScript 函数返回意外结果，在服务器上引发意外行为。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-228">Induce an unintended behavior on the server by returning an unexpected result from the JavaScript function.</span></span>

<span data-ttu-id="3d8fc-229">请采取以下预防措施来防止出现上述情况：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-229">Take the following precautions to guard against the preceding scenarios:</span></span>

* <span data-ttu-id="3d8fc-230">在 [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) 语句中包装 JS 互操作调用，以处理调用期间可能发生的错误。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-230">Wrap JS interop calls within [`try-catch`](/dotnet/csharp/language-reference/keywords/try-catch) statements to account for errors that might occur during the invocations.</span></span> <span data-ttu-id="3d8fc-231">有关详细信息，请参阅 <xref:blazor/fundamentals/handle-errors#javascript-interop>。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-231">For more information, see <xref:blazor/fundamentals/handle-errors#javascript-interop>.</span></span>
* <span data-ttu-id="3d8fc-232">在执行任何操作之前，请验证从 JS 互操作调用返回的数据（包括错误消息）。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-232">Validate data returned from JS interop invocations, including error messages, before taking any action.</span></span>

### <a name="net-methods-invoked-from-the-browser"></a><span data-ttu-id="3d8fc-233">从浏览器调用的 .NET 方法</span><span class="sxs-lookup"><span data-stu-id="3d8fc-233">.NET methods invoked from the browser</span></span>

<span data-ttu-id="3d8fc-234">不要信任从 JavaScript 到 .NET 方法的调用。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-234">Don't trust calls from JavaScript to .NET methods.</span></span> <span data-ttu-id="3d8fc-235">当 .NET 方法公开给 JavaScript 时，请思考如何来调用 .NET 方法：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-235">When a .NET method is exposed to JavaScript, consider how the .NET method is invoked:</span></span>

* <span data-ttu-id="3d8fc-236">将所有公开给 JavaScript 的 .NET 方法都看做是应用的公共终结点。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-236">Treat any .NET method exposed to JavaScript as you would a public endpoint to the app.</span></span>
  * <span data-ttu-id="3d8fc-237">验证输入的内容。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-237">Validate input.</span></span>
    * <span data-ttu-id="3d8fc-238">确保值在预期范围内。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-238">Ensure that values are within expected ranges.</span></span>
    * <span data-ttu-id="3d8fc-239">确保用户有权执行请求的操作。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-239">Ensure that the user has permission to perform the action requested.</span></span>
  * <span data-ttu-id="3d8fc-240">不要在 .NET 方法调用期间分配太多资源。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-240">Don't allocate an excessive quantity of resources as part of the .NET method invocation.</span></span> <span data-ttu-id="3d8fc-241">例如，执行检查并限制 CPU 和内存的使用。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-241">For example, perform checks and place limits on CPU and memory use.</span></span>
  * <span data-ttu-id="3d8fc-242">要考虑到静态和实例方法可公开给 JavaScript 客户端。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-242">Take into account that static and instance methods can be exposed to JavaScript clients.</span></span> <span data-ttu-id="3d8fc-243">不要跨会话共享状态，除非设计要求在设有适当约束的情况下共享状态。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-243">Avoid sharing state across sessions unless the design calls for sharing state with appropriate constraints.</span></span>
    * <span data-ttu-id="3d8fc-244">如果实例方法通过 `DotNetReference` 对象公开，而这些对象最初是通过依赖注入 (DI) 创建的，那么应将这些对象注册为范围对象。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-244">For instance methods exposed through `DotNetReference` objects that are originally created through dependency injection (DI), the objects should be registered as scoped objects.</span></span> <span data-ttu-id="3d8fc-245">这适用于 :::no-loc(Blazor Server)::: 应用使用的任何 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-245">This applies to any DI service that the :::no-loc(Blazor Server)::: app uses.</span></span>
    * <span data-ttu-id="3d8fc-246">对于静态方法，请不要建立范围无法限定为客户端的状态，除非应用根据设计在服务器实例上跨所有用户明确共享状态。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-246">For static methods, avoid establishing state that can't be scoped to the client unless the app is explicitly sharing state by-design across all users on a server instance.</span></span>
  * <span data-ttu-id="3d8fc-247">不要在参数中向 JavaScript 调用传递用户提供的数据。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-247">Avoid passing user-supplied data in parameters to JavaScript calls.</span></span> <span data-ttu-id="3d8fc-248">如果一定需要在参数中传递数据，请确保 JavaScript 代码负责数据的传递时不引入[跨站点脚本 (XSS)](#cross-site-scripting-xss) 漏洞。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-248">If passing data in parameters is absolutely required, ensure that the JavaScript code handles passing the data without introducing [Cross-site scripting (XSS)](#cross-site-scripting-xss) vulnerabilities.</span></span> <span data-ttu-id="3d8fc-249">例如，不要通过设置元素的 `innerHTML` 属性将用户提供的数据写入文档对象模型 (DOM)。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-249">For example, don't write user-supplied data to the Document Object Model (DOM) by setting the `innerHTML` property of an element.</span></span> <span data-ttu-id="3d8fc-250">请考虑使用[内容安全策略 (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 来禁用 `eval` 以及其他不安全的 JavaScript 基元。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-250">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to disable `eval` and other unsafe JavaScript primitives.</span></span>
* <span data-ttu-id="3d8fc-251">不要在框架的调度实现之上实现 .NET 调用的自定义调度。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-251">Avoid implementing custom dispatching of .NET invocations on top of the framework's dispatching implementation.</span></span> <span data-ttu-id="3d8fc-252">向浏览器公开 .NET 方法是一种高级方案，不建议用于常规的 :::no-loc(Blazor)::: 开发。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-252">Exposing .NET methods to the browser is an advanced scenario, not recommended for general :::no-loc(Blazor)::: development.</span></span>

### <a name="events"></a><span data-ttu-id="3d8fc-253">事件</span><span class="sxs-lookup"><span data-stu-id="3d8fc-253">Events</span></span>

<span data-ttu-id="3d8fc-254">事件提供到 :::no-loc(Blazor Server)::: 应用的入口点。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-254">Events provide an entry point to a :::no-loc(Blazor Server)::: app.</span></span> <span data-ttu-id="3d8fc-255">在 Web 应用中用来保护终结点的规则也适用于 :::no-loc(Blazor Server)::: 应用中的事件处理。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-255">The same rules for safeguarding endpoints in web apps apply to event handling in :::no-loc(Blazor Server)::: apps.</span></span> <span data-ttu-id="3d8fc-256">恶意客户端可能会发送其希望作为事件有效负载发送的任何数据。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-256">A malicious client can send any data it wishes to send as the payload for an event.</span></span>

<span data-ttu-id="3d8fc-257">例如：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-257">For example:</span></span>

* <span data-ttu-id="3d8fc-258">`<select>` 的更改事件可能会发送一个在应用提供给客户端的选项中没有的值。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-258">A change event for a `<select>` could send a value that isn't within the options that the app presented to the client.</span></span>
* <span data-ttu-id="3d8fc-259">`<input>` 可能会向服务器发送任何文本数据，试图绕过客户端验证。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-259">An `<input>` could send any text data to the server, bypassing client-side validation.</span></span>

<span data-ttu-id="3d8fc-260">应用必须对应用处理的任何事件的数据进行验证。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-260">The app must validate the data for any event that the app handles.</span></span> <span data-ttu-id="3d8fc-261">基本验证由 :::no-loc(Blazor)::: 框架[窗体组件](xref:blazor/forms-validation)负责执行。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-261">The :::no-loc(Blazor)::: framework [forms components](xref:blazor/forms-validation) perform basic validations.</span></span> <span data-ttu-id="3d8fc-262">如果应用使用自定义窗体组件，则必须根据需要编写自定义代码来验证事件数据。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-262">If the app uses custom forms components, custom code must be written to validate event data as appropriate.</span></span>

<span data-ttu-id="3d8fc-263">:::no-loc(Blazor Server)::: 事件是异步的，因此在应用有时间通过生成新的呈现来作出回应之前，可将多个事件调度到服务器。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-263">:::no-loc(Blazor Server)::: events are asynchronous, so multiple events can be dispatched to the server before the app has time to react by producing a new render.</span></span> <span data-ttu-id="3d8fc-264">这需要考虑一些安全问题。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-264">This has some security implications to consider.</span></span> <span data-ttu-id="3d8fc-265">应用中对客户端操作的限制必须在事件处理程序内执行，而不依赖于当前呈现的视图状态。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-265">Limiting client actions in the app must be performed inside event handlers and not depend on the current rendered view state.</span></span>

<span data-ttu-id="3d8fc-266">假设有一个计数器组件，它应该允许用户最多递增三次计数器。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-266">Consider a counter component that should allow a user to increment a counter a maximum of three times.</span></span> <span data-ttu-id="3d8fc-267">根据 `count` 的值，用于递增计数器的按钮需遵循相应条件：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-267">The button to increment the counter is conditionally based on the value of `count`:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        count++;
    }
}
```

<span data-ttu-id="3d8fc-268">在框架生成该组件的新呈现之前，客户端可调度一个或多个递增事件。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-268">A client can dispatch one or more increment events before the framework produces a new render of this component.</span></span> <span data-ttu-id="3d8fc-269">结果就是用户可将 `count` 递增三次以上，因为 UI 没有足够快地删除该按钮。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-269">The result is that the `count` can be incremented *over three times* by the user because the button isn't removed by the UI quickly enough.</span></span> <span data-ttu-id="3d8fc-270">以下示例显示了实现三次 `count` 递增限制的正确方法：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-270">The correct way to achieve the limit of three `count` increments is shown in the following example:</span></span>

```razor
<p>Count: @count<p>

@if (count < 3)
{
    <button @onclick="IncrementCount" value="Increment count" />
}

@code 
{
    private int count = 0;

    private void IncrementCount()
    {
        if (count < 3)
        {
            count++;
        }
    }
}
```

<span data-ttu-id="3d8fc-271">通过在处理程序中添加 `if (count < 3) { ... }` 检查，根据当前应用状态来决定递增 `count`。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-271">By adding the `if (count < 3) { ... }` check inside the handler, the decision to increment `count` is based on the current app state.</span></span> <span data-ttu-id="3d8fc-272">该决定并不像前一示例中是根据 UI 的状态作出的，该状态可能暂时无效。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-272">The decision isn't based on the state of the UI as it was in the previous example, which might be temporarily stale.</span></span>

### <a name="guard-against-multiple-dispatches"></a><span data-ttu-id="3d8fc-273">避免多次调度</span><span class="sxs-lookup"><span data-stu-id="3d8fc-273">Guard against multiple dispatches</span></span>

<span data-ttu-id="3d8fc-274">如果事件回调以异步方式调用长期运行的操作，例如从外部服务或数据库提取数据，请考虑采用保护措施。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-274">If an event callback invokes a long running operation asynchronously, such as fetching data from an external service or database, consider using a guard.</span></span> <span data-ttu-id="3d8fc-275">该保护可防止用户在操作进行过程中对多个操作排队，并提供可视反馈。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-275">The guard can prevent the user from queueing up multiple operations while the operation is in progress with visual feedback.</span></span> <span data-ttu-id="3d8fc-276">当 `GetForecastAsync` 从服务器获取数据时，下列组件代码会将 `isLoading` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-276">The following component code sets `isLoading` to `true` while `GetForecastAsync` obtains data from the server.</span></span> <span data-ttu-id="3d8fc-277">当 `isLoading` 为 `true` 时，该按钮在 UI 中被禁用：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-277">While `isLoading` is `true`, the button is disabled in the UI:</span></span>

```razor
@page "/fetchdata"
@using :::no-loc(Blazor):::ServerSample.Data
@inject WeatherForecastService ForecastService

<button disabled="@isLoading" @onclick="UpdateForecasts">Update</button>

@code {
    private bool isLoading;
    private WeatherForecast[] forecasts;

    private async Task UpdateForecasts()
    {
        if (!isLoading)
        {
            isLoading = true;
            forecasts = await ForecastService.GetForecastAsync(DateTime.Now);
            isLoading = false;
        }
    }
}
```

<span data-ttu-id="3d8fc-278">如果后台操作使用 `async`-`await` 模式异步执行，则前面示例中演示的保护模式将发挥作用。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-278">The guard pattern demonstrated in the preceding example works if the background operation is executed asynchronously with the `async`-`await` pattern.</span></span>

### <a name="cancel-early-and-avoid-use-after-dispose"></a><span data-ttu-id="3d8fc-279">提前取消，避免释放后使用</span><span class="sxs-lookup"><span data-stu-id="3d8fc-279">Cancel early and avoid use-after-dispose</span></span>

<span data-ttu-id="3d8fc-280">除了使用[避免多次调度](#guard-against-multiple-dispatches)部分中描述的保护之外，还可考虑在释放组件时使用 <xref:System.Threading.CancellationToken> 取消长时间运行的操作。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-280">In addition to using a guard as described in the [Guard against multiple dispatches](#guard-against-multiple-dispatches) section, consider using a <xref:System.Threading.CancellationToken> to cancel long-running operations when the component is disposed.</span></span> <span data-ttu-id="3d8fc-281">该方法还有一个好处，就是避免组件中的“释放后使用”：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-281">This approach has the added benefit of avoiding *use-after-dispose* in components:</span></span>

```razor
@implements IDisposable

...

@code {
    private readonly CancellationTokenSource TokenSource = 
        new CancellationTokenSource();

    private async Task UpdateForecasts()
    {
        ...

        forecasts = await ForecastService.GetForecastAsync(DateTime.Now, 
            TokenSource.Token);

        if (TokenSource.Token.IsCancellationRequested)
        {
           return;
        }

        ...
    }

    public void Dispose()
    {
        TokenSource.Cancel();
    }
}
```

### <a name="avoid-events-that-produce-large-amounts-of-data"></a><span data-ttu-id="3d8fc-282">避免产生大量数据的事件</span><span class="sxs-lookup"><span data-stu-id="3d8fc-282">Avoid events that produce large amounts of data</span></span>

<span data-ttu-id="3d8fc-283">某些 DOM 事件（例如 `oninput` 或 `onscroll`）可能会生成大量数据。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-283">Some DOM events, such as `oninput` or `onscroll`, can produce a large amount of data.</span></span> <span data-ttu-id="3d8fc-284">不要在 :::no-loc(Blazor)::: 服务器应用中使用这些事件。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-284">Avoid using these events in :::no-loc(Blazor)::: server apps.</span></span>

## <a name="additional-security-guidance"></a><span data-ttu-id="3d8fc-285">其他安全指南</span><span class="sxs-lookup"><span data-stu-id="3d8fc-285">Additional security guidance</span></span>

<span data-ttu-id="3d8fc-286">ASP.NET Core 应用的保护指南适用于 :::no-loc(Blazor Server)::: 应用，将在以下部分进行介绍：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-286">The guidance for securing ASP.NET Core apps apply to :::no-loc(Blazor Server)::: apps and are covered in the following sections:</span></span>

* [<span data-ttu-id="3d8fc-287">日志记录和敏感数据</span><span class="sxs-lookup"><span data-stu-id="3d8fc-287">Logging and sensitive data</span></span>](#logging-and-sensitive-data)
* [<span data-ttu-id="3d8fc-288">使用 HTTPS 保护传输中的信息</span><span class="sxs-lookup"><span data-stu-id="3d8fc-288">Protect information in transit with HTTPS</span></span>](#protect-information-in-transit-with-https)
* [<span data-ttu-id="3d8fc-289">跨站点脚本 (XSS)</span><span class="sxs-lookup"><span data-stu-id="3d8fc-289">Cross-site scripting (XSS)</span></span>](#cross-site-scripting-xss)
* [<span data-ttu-id="3d8fc-290">跨源保护</span><span class="sxs-lookup"><span data-stu-id="3d8fc-290">Cross-origin protection</span></span>](#cross-origin-protection)
* [<span data-ttu-id="3d8fc-291">点击劫持</span><span class="sxs-lookup"><span data-stu-id="3d8fc-291">Click-jacking</span></span>](#click-jacking)
* [<span data-ttu-id="3d8fc-292">打开重定向</span><span class="sxs-lookup"><span data-stu-id="3d8fc-292">Open redirects</span></span>](#open-redirects)

### <a name="logging-and-sensitive-data"></a><span data-ttu-id="3d8fc-293">日志记录和敏感数据</span><span class="sxs-lookup"><span data-stu-id="3d8fc-293">Logging and sensitive data</span></span>

<span data-ttu-id="3d8fc-294">客户端和服务器之间的 JS 互操作交互通过 <xref:Microsoft.Extensions.Logging.ILogger> 实例记录在服务器的日志中。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-294">JS interop interactions between the client and server are recorded in the server's logs with <xref:Microsoft.Extensions.Logging.ILogger> instances.</span></span> <span data-ttu-id="3d8fc-295">:::no-loc(Blazor)::: 避免记录敏感信息，例如实际事件或 JS 互操作输入和输出。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-295">:::no-loc(Blazor)::: avoids logging sensitive information, such as actual events or JS interop inputs and outputs.</span></span>

<span data-ttu-id="3d8fc-296">当服务器上发生错误时，框架会通知客户端并关闭会话。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-296">When an error occurs on the server, the framework notifies the client and tears down the session.</span></span> <span data-ttu-id="3d8fc-297">默认情况下，客户端会收到一条通用错误消息，可在浏览器的开发人员工具中查看。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-297">By default, the client receives a generic error message that can be seen in the browser's developer tools.</span></span>

<span data-ttu-id="3d8fc-298">客户端错误不包括调用堆栈，也不提供有关错误原因的详细信息，但服务器日志的确包含此类信息。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-298">The client-side error doesn't include the callstack and doesn't provide detail on the cause of the error, but server logs do contain such information.</span></span> <span data-ttu-id="3d8fc-299">出于开发目的，可通过启用详细错误向客户端提供敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-299">For development purposes, sensitive error information can be made available to the client by enabling detailed errors.</span></span>

<span data-ttu-id="3d8fc-300">使用以下项在 JavaScript 中启用详细错误：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-300">Enable detailed errors in JavaScript with:</span></span>

* <span data-ttu-id="3d8fc-301"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-301"><xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.DetailedErrors?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="3d8fc-302">`DetailedErrors` 配置键设置为 `true`，在应用设置文件 (`:::no-loc(appsettings.json):::`) 中可进行此设置。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-302">The `DetailedErrors` configuration key set to `true`, which can be set in the app settings file (`:::no-loc(appsettings.json):::`).</span></span> <span data-ttu-id="3d8fc-303">也可使用值为 `true` 的 `ASPNETCORE_DETAILEDERRORS` 环境变量设置此键。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-303">The key can also be set using the `ASPNETCORE_DETAILEDERRORS` environment variable with a value of `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="3d8fc-304">在 Internet 上向客户端公开错误信息是一项始终应该避免的安全风险。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-304">Exposing error information to clients on the Internet is a security risk that should always be avoided.</span></span>

### <a name="protect-information-in-transit-with-https"></a><span data-ttu-id="3d8fc-305">使用 HTTPS 保护传输中的信息</span><span class="sxs-lookup"><span data-stu-id="3d8fc-305">Protect information in transit with HTTPS</span></span>

<span data-ttu-id="3d8fc-306">:::no-loc(Blazor Server)::: 使用 :::no-loc(SignalR)::: 在客户端和服务器之间进行通信。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-306">:::no-loc(Blazor Server)::: uses :::no-loc(SignalR)::: for communication between the client and the server.</span></span> <span data-ttu-id="3d8fc-307">:::no-loc(Blazor Server)::: 通常使用 :::no-loc(SignalR)::: 协商的传输，这通常是 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-307">:::no-loc(Blazor Server)::: normally uses the transport that :::no-loc(SignalR)::: negotiates, which is typically WebSockets.</span></span>

<span data-ttu-id="3d8fc-308">:::no-loc(Blazor Server)::: 无法确保服务器和客户端之间发送的数据的完整性和机密性。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-308">:::no-loc(Blazor Server)::: doesn't ensure the integrity and confidentiality of the data sent between the server and the client.</span></span> <span data-ttu-id="3d8fc-309">请始终使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-309">Always use HTTPS.</span></span>

### <a name="cross-site-scripting-xss"></a><span data-ttu-id="3d8fc-310">跨站点脚本 (XSS)</span><span class="sxs-lookup"><span data-stu-id="3d8fc-310">Cross-site scripting (XSS)</span></span>

<span data-ttu-id="3d8fc-311">跨站点脚本 (XSS) 允许未经授权的一方在浏览器的上下文中执行任意逻辑。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-311">Cross-site scripting (XSS) allows an unauthorized party to execute arbitrary logic in the context of the browser.</span></span> <span data-ttu-id="3d8fc-312">遭到入侵的应用可能会在客户端上运行任意代码。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-312">A compromised app could potentially run arbitrary code on the client.</span></span> <span data-ttu-id="3d8fc-313">该漏洞可能会被利用来对服务器执行大量恶意操作，例如：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-313">The vulnerability could be used to potentially perform a number of malicious actions against the server:</span></span>

* <span data-ttu-id="3d8fc-314">向服务器发送虚假/无效事件。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-314">Dispatch fake/invalid events to the server.</span></span>
* <span data-ttu-id="3d8fc-315">调度失败/无效的呈现完成。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-315">Dispatch fail/invalid render completions.</span></span>
* <span data-ttu-id="3d8fc-316">避免调度呈现完成。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-316">Avoid dispatching render completions.</span></span>
* <span data-ttu-id="3d8fc-317">调度从 JavaScript 对 .NET 的互操作调用。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-317">Dispatch interop calls from JavaScript to .NET.</span></span>
* <span data-ttu-id="3d8fc-318">修改从 .NET 到 JavaScript 的互操作调用的响应。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-318">Modify the response of interop calls from .NET to JavaScript.</span></span>
* <span data-ttu-id="3d8fc-319">避免调度从 .NET 到 JS 的互操作结果。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-319">Avoid dispatching .NET to JS interop results.</span></span>

<span data-ttu-id="3d8fc-320">:::no-loc(Blazor Server)::: 框架采取了一些步骤来防范前面的部分威胁：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-320">The :::no-loc(Blazor Server)::: framework takes steps to protect against some of the preceding threats:</span></span>

* <span data-ttu-id="3d8fc-321">如果客户端未确认呈现批处理，则停止生成新的 UI 更新。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-321">Stops producing new UI updates if the client isn't acknowledging render batches.</span></span> <span data-ttu-id="3d8fc-322">配置了 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-322">Configured with <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.MaxBufferedUnacknowledgedRenderBatches?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="3d8fc-323">如果没有收到来自客户端的响应，则任何从 .NET 到 JavaScript 的调用一分钟后都会超时。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-323">Times out any .NET to JavaScript call after one minute without receiving a response from the client.</span></span> <span data-ttu-id="3d8fc-324">配置了 <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-324">Configured with <xref:Microsoft.AspNetCore.Components.Server.CircuitOptions.JSInteropDefaultCallTimeout?displayProperty=nameWithType>.</span></span>
* <span data-ttu-id="3d8fc-325">在 JS 互操作期间对来自浏览器的所有输入执行基本验证以确保：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-325">Performs basic validation on all input coming from the browser during JS interop:</span></span>
  * <span data-ttu-id="3d8fc-326">.NET 引用有效，且是 .NET 方法所需的类型。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-326">.NET references are valid and of the type expected by the .NET method.</span></span>
  * <span data-ttu-id="3d8fc-327">数据格式正确。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-327">The data isn't malformed.</span></span>
  * <span data-ttu-id="3d8fc-328">方法的参数数目是有效负载中是正确的。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-328">The correct number of arguments for the method are present in the payload.</span></span>
  * <span data-ttu-id="3d8fc-329">在调用方法之前，可适当地对参数或结果进行反序列化处理。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-329">The arguments or result can be deserialized correctly before invoking the method.</span></span>
* <span data-ttu-id="3d8fc-330">在来已调度事件的浏览器的所有输入中执行基本验证以确保：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-330">Performs basic validation in all input coming from the browser from dispatched events:</span></span>
  * <span data-ttu-id="3d8fc-331">事件的类型有效。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-331">The event has a valid type.</span></span>
  * <span data-ttu-id="3d8fc-332">可对事件的数据进行反序列化处理。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-332">The data for the event can be deserialized.</span></span>
  * <span data-ttu-id="3d8fc-333">存在与事件关联的事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-333">There's an event handler associated with the event.</span></span>

<span data-ttu-id="3d8fc-334">除了框架实现的安全措施外，开发人员还必须对应用进行编码，以防范威胁并采取适当操作，例如：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-334">In addition to the safeguards that the framework implements, the app must be coded by the developer to safeguard against threats and take appropriate actions:</span></span>

* <span data-ttu-id="3d8fc-335">处理事件时始终验证数据。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-335">Always validate data when handling events.</span></span>
* <span data-ttu-id="3d8fc-336">接收无效数据时采取适当的操作：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-336">Take appropriate action upon receiving invalid data:</span></span>
  * <span data-ttu-id="3d8fc-337">忽略数据并返回。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-337">Ignore the data and return.</span></span> <span data-ttu-id="3d8fc-338">这允许应用继续处理请求。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-338">This allows the app to continue processing requests.</span></span>
  * <span data-ttu-id="3d8fc-339">如果应用确定输入是非法的并且无法由合法客户端生成，则引发异常。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-339">If the app determines that the input is illegitimate and couldn't be produced by legitimate client, throw an exception.</span></span> <span data-ttu-id="3d8fc-340">引发异常会中断线路并结束会话。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-340">Throwing an exception tears down the circuit and ends the session.</span></span>
* <span data-ttu-id="3d8fc-341">不要信任日志中包含的呈现批处理完成所提供的错误消息。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-341">Don't trust the error message provided by render batch completions included in the logs.</span></span> <span data-ttu-id="3d8fc-342">该错误由客户端提供，通常不可信任，因为客户端可能已遭到入侵。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-342">The error is provided by the client and can't generally be trusted, as the client might be compromised.</span></span>
* <span data-ttu-id="3d8fc-343">不要在 JavaScript 和 .NET 方法之间的任意方向信任 JS 互操作调用的输入。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-343">Don't trust the input on JS interop calls in either direction between JavaScript and .NET methods.</span></span>
* <span data-ttu-id="3d8fc-344">应用负责验证参数和结果的内容是否有效，即使参数或结果已正确反序列化也要验证。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-344">The app is responsible for validating that the content of arguments and results are valid, even if the arguments or results are correctly deserialized.</span></span>

<span data-ttu-id="3d8fc-345">要使 XSS 漏洞存在，应用必须将用户输入合并到呈现的页面中。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-345">For a XSS vulnerability to exist, the app must incorporate user input in the rendered page.</span></span> <span data-ttu-id="3d8fc-346">:::no-loc(Blazor Server)::: 组件会执行一个编译时步骤，其中 `.razor` 文件中的标记转换为过程 C# 逻辑。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-346">:::no-loc(Blazor Server)::: components execute a compile-time step where the markup in a `.razor` file is transformed into procedural C# logic.</span></span> <span data-ttu-id="3d8fc-347">在运行时，C# 逻辑会生成一个呈现树来描述元素、文本和子组件。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-347">At runtime, the C# logic builds a *render tree* describing the elements, text, and child components.</span></span> <span data-ttu-id="3d8fc-348">它通过一系列 JavaScript 指令应用于浏览器的 DOM（如果预呈现，则序列化为 HTML）：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-348">This is applied to the browser's DOM via a sequence of JavaScript instructions (or is serialized to HTML in the case of prerendering):</span></span>

* <span data-ttu-id="3d8fc-349">通过常规 :::no-loc(Razor)::: 语法（例如 `@someStringValue`）呈现的用户输入不会公开 XSS 漏洞，因为 :::no-loc(Razor)::: 语法通过只能写入文本的命令添加到 DOM 中。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-349">User input rendered via normal :::no-loc(Razor)::: syntax (for example, `@someStringValue`) doesn't expose a XSS vulnerability because the :::no-loc(Razor)::: syntax is added to the DOM via commands that can only write text.</span></span> <span data-ttu-id="3d8fc-350">即使该值包含 HTML 标记，它也显示为静态文本。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-350">Even if the value includes HTML markup, the value is displayed as static text.</span></span> <span data-ttu-id="3d8fc-351">预呈现时，输出经过 HTML 编码，它还将内容显示为静态文本。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-351">When prerendering, the output is HTML-encoded, which also displays the content as static text.</span></span>
* <span data-ttu-id="3d8fc-352">禁止使用脚本标记，也不得在应用的组件呈现树中包含它们。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-352">Script tags aren't allowed and shouldn't be included in the app's component render tree.</span></span> <span data-ttu-id="3d8fc-353">如果组件标记中包含脚本标记，则会生成编译时错误。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-353">If a script tag is included in a component's markup, a compile-time error is generated.</span></span>
* <span data-ttu-id="3d8fc-354">组件作者无需使用 :::no-loc(Razor)::: 就可在 C# 中编写组件。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-354">Component authors can author components in C# without using :::no-loc(Razor):::.</span></span> <span data-ttu-id="3d8fc-355">组件作者负责在发出输出时使用正确的 API。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-355">The component author is responsible for using the correct APIs when emitting output.</span></span> <span data-ttu-id="3d8fc-356">例如，使用 `builder.AddContent(0, someUserSuppliedString)` 而不是 `builder.AddMarkupContent(0, someUserSuppliedString)`，因为后者可能会导致出现 XSS 漏洞。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-356">For example, use `builder.AddContent(0, someUserSuppliedString)` and *not* `builder.AddMarkupContent(0, someUserSuppliedString)`, as the latter could create a XSS vulnerability.</span></span>

<span data-ttu-id="3d8fc-357">作为防范 XSS 攻击的一部分，请考虑实施 XSS 缓解措施，例如[内容安全策略 (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP)。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-357">As part of protecting against XSS attacks, consider implementing XSS mitigations, such as [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP).</span></span>

<span data-ttu-id="3d8fc-358">有关详细信息，请参阅 <xref:security/cross-site-scripting>。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-358">For more information, see <xref:security/cross-site-scripting>.</span></span>

### <a name="cross-origin-protection"></a><span data-ttu-id="3d8fc-359">跨源保护</span><span class="sxs-lookup"><span data-stu-id="3d8fc-359">Cross-origin protection</span></span>

<span data-ttu-id="3d8fc-360">跨源攻击涉及到来自不同源的客户端对服务器执行操作。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-360">Cross-origin attacks involve a client from a different origin performing an action against the server.</span></span> <span data-ttu-id="3d8fc-361">恶意操作通常是 GET 请求或窗体 POST（跨站点请求伪造，简称为 CSRF），但也可能是打开恶意 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-361">The malicious action is typically a GET request or a form POST (Cross-Site Request Forgery, CSRF), but opening a malicious WebSocket is also possible.</span></span> <span data-ttu-id="3d8fc-362">:::no-loc(Blazor Server)::: 应用提供[与使用中心协议产品/服务的任何其他 :::no-loc(SignalR)::: 应用相同的保证](xref:signalr/security)：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-362">:::no-loc(Blazor Server)::: apps offer [the same guarantees that any other :::no-loc(SignalR)::: app using the hub protocol offer](xref:signalr/security):</span></span>

* <span data-ttu-id="3d8fc-363">可跨源访问 :::no-loc(Blazor Server)::: 应用，除非采取额外的措施进行阻止。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-363">:::no-loc(Blazor Server)::: apps can be accessed cross-origin unless additional measures are taken to prevent it.</span></span> <span data-ttu-id="3d8fc-364">若要禁用跨源访问，请将 CORS 中间件添加到管道并将 <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> 添加到 :::no-loc(Blazor)::: 终结点元数据来禁用终结点中的 CORS，或者[配置 :::no-loc(SignalR)::: 实现跨源资源共享](xref:signalr/security#cross-origin-resource-sharing)来仅允许跨一组来源进行访问。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-364">To disable cross-origin access, either disable CORS in the endpoint by adding the CORS middleware to the pipeline and adding the <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> to the :::no-loc(Blazor)::: endpoint metadata or limit the set of allowed origins by [configuring :::no-loc(SignalR)::: for cross-origin resource sharing](xref:signalr/security#cross-origin-resource-sharing).</span></span>
* <span data-ttu-id="3d8fc-365">如果启用了 CORS，则可能需要执行额外的步骤来保护应用，具体取决于 CORS 配置。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-365">If CORS is enabled, extra steps might be required to protect the app depending on the CORS configuration.</span></span> <span data-ttu-id="3d8fc-366">如果 CORS 已全局启用，则可在终结点路由生成器上调用 <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub%2A> 之后，将 <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> 元数据添加到终结点元数据来为 :::no-loc(Blazor Server)::: 中心禁用 CORS。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-366">If CORS is globally enabled, CORS can be disabled for the :::no-loc(Blazor Server)::: hub by adding the <xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute> metadata to the endpoint metadata after calling <xref:Microsoft.AspNetCore.Builder.ComponentEndpointRouteBuilderExtensions.Map:::no-loc(Blazor):::Hub%2A> on the endpoint route builder.</span></span>

<span data-ttu-id="3d8fc-367">有关详细信息，请参阅 <xref:security/anti-request-forgery>。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-367">For more information, see <xref:security/anti-request-forgery>.</span></span>

### <a name="click-jacking"></a><span data-ttu-id="3d8fc-368">点击劫持</span><span class="sxs-lookup"><span data-stu-id="3d8fc-368">Click-jacking</span></span>

<span data-ttu-id="3d8fc-369">点击劫持涉及到将一个站点呈现为来自不同来源的站点内的 `<iframe>`，以诱骗用户在受到攻击的站点上执行操作。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-369">Click-jacking involves rendering a site as an `<iframe>` inside a site from a different origin in order to trick the user into performing actions on the site under attack.</span></span>

<span data-ttu-id="3d8fc-370">若要防止应用在 `<iframe>` 内部呈现，请使用[内容安全策略 (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 和 `X-Frame-Options` 标头。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-370">To protect an app from rendering inside of an `<iframe>`, use [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) and the `X-Frame-Options` header.</span></span> <span data-ttu-id="3d8fc-371">有关详细信息，请参阅 [MDN Web 文档：X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options)。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-371">For more information, see [MDN web docs: X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options).</span></span>

### <a name="open-redirects"></a><span data-ttu-id="3d8fc-372">打开重定向</span><span class="sxs-lookup"><span data-stu-id="3d8fc-372">Open redirects</span></span>

<span data-ttu-id="3d8fc-373">当 :::no-loc(Blazor Server)::: 应用会话启动时，服务器对会话启动期间发送的 URL 执行基本验证。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-373">When a :::no-loc(Blazor Server)::: app session starts, the server performs basic validation of the URLs sent as part of starting the session.</span></span> <span data-ttu-id="3d8fc-374">在建立线路之前，框架会检查基 URL 是否是当前 URL 的父 URL。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-374">The framework checks that the base URL is a parent of the current URL before establishing the circuit.</span></span> <span data-ttu-id="3d8fc-375">此框架不会执行其他检查。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-375">No additional checks are performed by the framework.</span></span>

<span data-ttu-id="3d8fc-376">当用户选择客户端上的链接时，该链接的 URL 会发送到服务器，后者将确定要采取的操作。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-376">When a user selects a link on the client, the URL for the link is sent to the server, which determines what action to take.</span></span> <span data-ttu-id="3d8fc-377">例如，应用可能会执行客户端导航，也可能会指示浏览器转到新位置。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-377">For example, the app may perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="3d8fc-378">组件还可使用 <xref:Microsoft.AspNetCore.Components.NavigationManager> 以编程的方式触发导航请求。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-378">Components can also trigger navigation requests programatically through the use of <xref:Microsoft.AspNetCore.Components.NavigationManager>.</span></span> <span data-ttu-id="3d8fc-379">在这种情况下，应用可能会执行客户端导航或指示浏览器转到新位置。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-379">In such scenarios, the app might perform a client-side navigation or indicate to the browser to go to the new location.</span></span>

<span data-ttu-id="3d8fc-380">组件必须：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-380">Components must:</span></span>

* <span data-ttu-id="3d8fc-381">避免将用户输入用作导航调用参数的一部分。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-381">Avoid using user input as part of the navigation call arguments.</span></span>
* <span data-ttu-id="3d8fc-382">验证参数以确保应用允许目标。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-382">Validate arguments to ensure that the target is allowed by the app.</span></span>

<span data-ttu-id="3d8fc-383">否则，恶意用户可能会强制浏览器转到受攻击者控制的站点。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-383">Otherwise, a malicious user can force the browser to go to an attacker-controlled site.</span></span> <span data-ttu-id="3d8fc-384">在这种情况下，攻击者会诱使应用使用某些用户输入作为 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 方法调用的一部分。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-384">In this scenario, the attacker tricks the app into using some user input as part of the invocation of the <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> method.</span></span>

<span data-ttu-id="3d8fc-385">当将链接作为应用的一部分呈现时，此建议也适用：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-385">This advice also applies when rendering links as part of the app:</span></span>

* <span data-ttu-id="3d8fc-386">如果可能，请使用相对链接。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-386">If possible, use relative links.</span></span>
* <span data-ttu-id="3d8fc-387">在将绝对链接目标包含在页面中之前，请验证这些目标是否有效。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-387">Validate that absolute link destinations are valid before including them in a page.</span></span>

<span data-ttu-id="3d8fc-388">有关详细信息，请参阅 <xref:security/preventing-open-redirects>。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-388">For more information, see <xref:security/preventing-open-redirects>.</span></span>

## <a name="security-checklist"></a><span data-ttu-id="3d8fc-389">安全清单</span><span class="sxs-lookup"><span data-stu-id="3d8fc-389">Security checklist</span></span>

<span data-ttu-id="3d8fc-390">下面仅列出了部分安全注意事项：</span><span class="sxs-lookup"><span data-stu-id="3d8fc-390">The following list of security considerations isn't comprehensive:</span></span>

* <span data-ttu-id="3d8fc-391">验证来自事件的参数。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-391">Validate arguments from events.</span></span>
* <span data-ttu-id="3d8fc-392">验证 JS 互操作调用的输入和结果。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-392">Validate inputs and results from JS interop calls.</span></span>
* <span data-ttu-id="3d8fc-393">避免对 .NET 到 JS 的互操作调用使用（或预先验证）用户输入。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-393">Avoid using (or validate beforehand) user input for .NET to JS interop calls.</span></span>
* <span data-ttu-id="3d8fc-394">阻止客户端分配未绑定的内存量。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-394">Prevent the client from allocating an unbound amount of memory.</span></span>
  * <span data-ttu-id="3d8fc-395">组件中的数据。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-395">Data within the component.</span></span>
  * <span data-ttu-id="3d8fc-396">返回给客户端的 `DotNetObject` 引用。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-396">`DotNetObject` references returned to the client.</span></span>
* <span data-ttu-id="3d8fc-397">避免多次调度。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-397">Guard against multiple dispatches.</span></span>
* <span data-ttu-id="3d8fc-398">释放组件时，取消长时间运行的操作。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-398">Cancel long-running operations when the component is disposed.</span></span>
* <span data-ttu-id="3d8fc-399">避免产生大量数据的事件。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-399">Avoid events that produce large amounts of data.</span></span>
* <span data-ttu-id="3d8fc-400">避免将用户输入用作对 <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> 调用的一部分；如果必须这样做，请先对照一组允许的来源验证 URL 的用户输入。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-400">Avoid using user input as part of calls to <xref:Microsoft.AspNetCore.Components.NavigationManager.NavigateTo%2A?displayProperty=nameWithType> and validate user input for URLs against a set of allowed origins first if unavoidable.</span></span>
* <span data-ttu-id="3d8fc-401">只根据组件状态做出授权决策，不要依据 UI 的状态。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-401">Don't make authorization decisions based on the state of the UI but only from component state.</span></span>
* <span data-ttu-id="3d8fc-402">考虑使用[内容安全策略 (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) 来阻止 XSS 攻击。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-402">Consider using [Content Security Policy (CSP)](https://developer.mozilla.org/docs/Web/HTTP/CSP) to protect against XSS attacks.</span></span>
* <span data-ttu-id="3d8fc-403">请考虑使用 CSP 和 [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) 来阻止点击劫持。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-403">Consider using CSP and [X-Frame-Options](https://developer.mozilla.org/docs/Web/HTTP/Headers/X-Frame-Options) to protect against click-jacking.</span></span>
* <span data-ttu-id="3d8fc-404">在启用 CORS 或显式禁用 :::no-loc(Blazor)::: 应用的 CORS 时，请确保 CORS 设置是恰当的。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-404">Ensure CORS settings are appropriate when enabling CORS or explicitly disable CORS for :::no-loc(Blazor)::: apps.</span></span>
* <span data-ttu-id="3d8fc-405">进行测试，确保 :::no-loc(Blazor)::: 应用的服务器端限制提供可接受的用户体验，不会带来不可接受的风险级别。</span><span class="sxs-lookup"><span data-stu-id="3d8fc-405">Test to ensure that the server-side limits for the :::no-loc(Blazor)::: app provide an acceptable user experience without unacceptable levels of risk.</span></span>
