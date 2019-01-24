---
uid: signalr/overview/getting-started/supported-platforms
title: 支持的平台 |Microsoft Docs
author: bradygaster
description: 本文介绍通过 SignalR 支持哪些客户端和服务器。
ms.author: bradyg
ms.date: 04/18/2018
ms.assetid: eac31beb-0f46-4afa-9def-e80904dea4f0
msc.legacyurl: /signalr/overview/getting-started/supported-platforms
msc.type: authoredcontent
ms.openlocfilehash: 60fa74b54797efbe14ba525160b2f750a4f5a451
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836046"
---
<a name="supported-platforms"></a><span data-ttu-id="ea411-103">支持的平台</span><span class="sxs-lookup"><span data-stu-id="ea411-103">Supported Platforms</span></span>
====================
<span data-ttu-id="ea411-104">通过[Patrick Fletcher](https://github.com/pfletcher)</span><span class="sxs-lookup"><span data-stu-id="ea411-104">by [Patrick Fletcher](https://github.com/pfletcher)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="ea411-105">本文介绍通过 SignalR 支持哪些客户端和服务器。</span><span class="sxs-lookup"><span data-stu-id="ea411-105">This article describes what clients and servers are supported by SignalR.</span></span> 
> 
> ## <a name="questions-and-comments"></a><span data-ttu-id="ea411-106">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="ea411-106">Questions and comments</span></span>
> 
> <span data-ttu-id="ea411-107">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="ea411-107">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="ea411-108">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="ea411-108">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

<span data-ttu-id="ea411-109">SignalR 支持到不同的服务器和客户端配置。</span><span class="sxs-lookup"><span data-stu-id="ea411-109">SignalR is supported under a variety of server and client configurations.</span></span> <span data-ttu-id="ea411-110">此外，每个传输选项具有一组其自身; 的要求如果传输的系统要求不可用，SignalR 会顺利地实现故障转移到其他传输。</span><span class="sxs-lookup"><span data-stu-id="ea411-110">In addition, each transport option has a set of requirements of its own; if the system requirements for a transport are not available, SignalR will gracefully failover to other transports.</span></span> <span data-ttu-id="ea411-111">SignalR 支持的传输的详细信息，请参阅[传输和回退](introduction-to-signalr.md#transports)。</span><span class="sxs-lookup"><span data-stu-id="ea411-111">For more information on the transports that SignalR supports, see [Transports and Fallbacks](introduction-to-signalr.md#transports).</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="ea411-112">服务器系统要求</span><span class="sxs-lookup"><span data-stu-id="ea411-112">Server system requirements</span></span>

<span data-ttu-id="ea411-113">SignalR 服务器组件可以驻留在不同的服务器配置。</span><span class="sxs-lookup"><span data-stu-id="ea411-113">The SignalR server component can be hosted on a variety of server configurations.</span></span> <span data-ttu-id="ea411-114">本部分介绍受支持的版本的操作系统，.NET framework、 Internet Information Server 和其他组件。</span><span class="sxs-lookup"><span data-stu-id="ea411-114">This section describes the supported versions of operating systems, .NET framework, Internet Information Server, and other components.</span></span>

### <a name="supported-server-operating-systems"></a><span data-ttu-id="ea411-115">支持的服务器操作系统</span><span class="sxs-lookup"><span data-stu-id="ea411-115">Supported server operating systems</span></span>

<span data-ttu-id="ea411-116">SignalR 服务器组件可以托管在以下服务器或客户端操作系统。</span><span class="sxs-lookup"><span data-stu-id="ea411-116">The SignalR server component can be hosted in the following server or client operating systems.</span></span> <span data-ttu-id="ea411-117">请注意，对于使用 Websocket 的 SignalR，Windows Server 2012、 Windows Server 2016 或 Windows 8 需 （WebSocket 可以使用 Windows Azure 网站上，只要站点的.NET framework 版本设置为 4.5，并且在站点中启用 Web 套接字配置页）。</span><span class="sxs-lookup"><span data-stu-id="ea411-117">Note that for SignalR to use WebSockets, Windows Server 2012, Windows Server 2016, or Windows 8 is required (WebSocket can be used on Windows Azure Web Sites, as long as the site's .NET framework version is set to 4.5, and Web Sockets is enabled in the site's Configuration page).</span></span>

- <span data-ttu-id="ea411-118">Windows 2016 Server</span><span class="sxs-lookup"><span data-stu-id="ea411-118">Windows Server 2016</span></span>
- <span data-ttu-id="ea411-119">Windows Server 2012</span><span class="sxs-lookup"><span data-stu-id="ea411-119">Windows Server 2012</span></span>
- <span data-ttu-id="ea411-120">Windows Server 2008 r2</span><span class="sxs-lookup"><span data-stu-id="ea411-120">Windows Server 2008 r2</span></span>
- <span data-ttu-id="ea411-121">Windows 10</span><span class="sxs-lookup"><span data-stu-id="ea411-121">Windows 10</span></span>
- <span data-ttu-id="ea411-122">Windows 8</span><span class="sxs-lookup"><span data-stu-id="ea411-122">Windows 8</span></span>
- <span data-ttu-id="ea411-123">Windows 7</span><span class="sxs-lookup"><span data-stu-id="ea411-123">Windows 7</span></span>
- <span data-ttu-id="ea411-124">Microsoft Azure</span><span class="sxs-lookup"><span data-stu-id="ea411-124">Windows Azure</span></span>

### <a name="supported-server-net-framework-version"></a><span data-ttu-id="ea411-125">受支持的服务器的.NET Framework 版本</span><span class="sxs-lookup"><span data-stu-id="ea411-125">Supported server .NET Framework version</span></span>

<span data-ttu-id="ea411-126">SignalR 2 仅支持.NET Framework 4.5 上。</span><span class="sxs-lookup"><span data-stu-id="ea411-126">SignalR 2 is only supported on .NET Framework 4.5.</span></span> <span data-ttu-id="ea411-127">请参阅[推荐的更新](#updates)增强可靠性、 兼容性、 稳定性和性能的更新的部分。</span><span class="sxs-lookup"><span data-stu-id="ea411-127">See the [Recommended Updates](#updates) section for updates that enhance reliability, compatibility, stability, and performance.</span></span>

### <a name="supported-server-iis-versions"></a><span data-ttu-id="ea411-128">受支持的服务器的 IIS 版本</span><span class="sxs-lookup"><span data-stu-id="ea411-128">Supported server IIS versions</span></span>

<span data-ttu-id="ea411-129">当在 IIS 中托管 SignalR 时，支持以下版本。</span><span class="sxs-lookup"><span data-stu-id="ea411-129">When SignalR is hosted in IIS, the following versions are supported.</span></span> <span data-ttu-id="ea411-130">请注意，是否使用客户端操作系统，例如用于开发 （Windows 8 或 Windows 7） 的完整版本的 IIS 或 Cassini 不应使用，因为限制为 10 个并发连接的规定，这将非常快速地达到自连接以来暂时性故障，经常重新建立，并且不会释放，立即在不再使用时。</span><span class="sxs-lookup"><span data-stu-id="ea411-130">Note that if a client operating system is used, such as for development (Windows 8 or Windows 7), full versions of IIS or Cassini should not be used, since there will be a limit of 10 simultaneous connections imposed, which will be reached very quickly since connections are transient, frequently re-established, and are not disposed immediately upon no longer being used.</span></span> <span data-ttu-id="ea411-131">应在客户端操作系统上使用 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="ea411-131">IIS Express should be used on client operating systems.</span></span>

<span data-ttu-id="ea411-132">此外请注意，signalr 使用 WebSocket，必须使用 IIS 8 或 IIS 8 Express，服务器必须使用 Windows 8，Windows Server 2012 或更高版本，必须在 IIS 中启用 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="ea411-132">Also note that for SignalR to use WebSocket, IIS 8 or IIS 8 Express must be used, the server must be using Windows 8, Windows Server 2012, or later, and WebSocket must be enabled in IIS.</span></span> <span data-ttu-id="ea411-133">有关如何启用 WebSocket 在 IIS 中的信息，请参阅[IIS 8.0 WebSocket 协议支持](https://www.iis.net/learn/get-started/whats-new-in-iis-8/iis-80-websocket-protocol-support)。</span><span class="sxs-lookup"><span data-stu-id="ea411-133">For information on how to enable WebSocket in IIS, see [IIS 8.0 WebSocket Protocol Support](https://www.iis.net/learn/get-started/whats-new-in-iis-8/iis-80-websocket-protocol-support).</span></span>

- <span data-ttu-id="ea411-134">IIS 8 IIS Express。</span><span class="sxs-lookup"><span data-stu-id="ea411-134">IIS 8 or IIS 8 Express.</span></span>
- <span data-ttu-id="ea411-135">IIS 7 和 7.5。</span><span class="sxs-lookup"><span data-stu-id="ea411-135">IIS 7 and 7.5.</span></span> <span data-ttu-id="ea411-136">为支持[无扩展名 Url](https://support.microsoft.com/kb/980368)是必需的。</span><span class="sxs-lookup"><span data-stu-id="ea411-136">Support for [extensionless URLs](https://support.microsoft.com/kb/980368) is required.</span></span>
- <span data-ttu-id="ea411-137">IIS 必须运行在集成模式下;不支持经典模式。</span><span class="sxs-lookup"><span data-stu-id="ea411-137">IIS must be running in integrated mode; classic mode is not supported.</span></span> <span data-ttu-id="ea411-138">如果在使用服务器发送事件传输的经典模式下运行 IIS，则可能遇到的最多 30 秒的消息延迟。</span><span class="sxs-lookup"><span data-stu-id="ea411-138">Message delays of up to 30 seconds may be experienced if IIS is run in classic mode using the Server-Sent Events transport.</span></span>
- <span data-ttu-id="ea411-139">托管应用程序必须在完全信任模式下运行。</span><span class="sxs-lookup"><span data-stu-id="ea411-139">The hosting application must be running in full trust mode.</span></span>

## <a name="client-system-requirements"></a><span data-ttu-id="ea411-140">客户端系统要求</span><span class="sxs-lookup"><span data-stu-id="ea411-140">Client system requirements</span></span>

<span data-ttu-id="ea411-141">SignalR 可在多个客户端平台。</span><span class="sxs-lookup"><span data-stu-id="ea411-141">SignalR can be used in a variety of client platforms.</span></span> <span data-ttu-id="ea411-142">本部分介绍使用 SignalR 在 web 浏览器、 Windows 桌面应用程序、 Silverlight 应用程序和移动设备的系统要求。</span><span class="sxs-lookup"><span data-stu-id="ea411-142">This section describes the system requirements for using SignalR in web browsers, Windows desktop applications, Silverlight applications, and mobile devices.</span></span>

### <a name="web-browsers"></a><span data-ttu-id="ea411-143">Web 浏览器</span><span class="sxs-lookup"><span data-stu-id="ea411-143">Web browsers</span></span>

<span data-ttu-id="ea411-144">SignalR 可在各种 web 浏览器，但通常情况下，支持的最新两个版本。</span><span class="sxs-lookup"><span data-stu-id="ea411-144">SignalR can be used in a variety of web browsers, but typically, only the latest two versions are supported.</span></span>

<span data-ttu-id="ea411-145">使用 SignalR 在浏览器中的应用程序必须使用 jQuery 版本 1.6.4 或主要更高版本 （如 1.7.2、 1.8.2 或 1.9.1）。</span><span class="sxs-lookup"><span data-stu-id="ea411-145">Applications that use SignalR in browsers must use jQuery version 1.6.4 or major later versions (such as 1.7.2, 1.8.2, or 1.9.1).</span></span>

<span data-ttu-id="ea411-146">可以在以下浏览器中使用 SignalR:</span><span class="sxs-lookup"><span data-stu-id="ea411-146">SignalR can be used in the following browsers:</span></span>

- <span data-ttu-id="ea411-147">Microsoft Internet Explorer 版本 8、 9、 10 和 11。</span><span class="sxs-lookup"><span data-stu-id="ea411-147">Microsoft Internet Explorer versions 8, 9, 10, and 11.</span></span> <span data-ttu-id="ea411-148">新式、 桌面和移动版支持。</span><span class="sxs-lookup"><span data-stu-id="ea411-148">Modern, Desktop, and Mobile versions are supported.</span></span>
- <span data-ttu-id="ea411-149">Mozilla Firefox： 当前版本-1，Windows 和 Mac 版本。</span><span class="sxs-lookup"><span data-stu-id="ea411-149">Mozilla Firefox: current version - 1, both Windows and Mac versions.</span></span>
- <span data-ttu-id="ea411-150">Google Chrome： 当前版本-1，Windows 和 Mac 版本。</span><span class="sxs-lookup"><span data-stu-id="ea411-150">Google Chrome: current version - 1, both Windows and Mac versions.</span></span>
- <span data-ttu-id="ea411-151">Safari： 当前版本-1，Mac 和 iOS 版本。</span><span class="sxs-lookup"><span data-stu-id="ea411-151">Safari: current version - 1, both Mac and iOS versions.</span></span>
- <span data-ttu-id="ea411-152">Opera： 当前版本-1，仅 Windows。</span><span class="sxs-lookup"><span data-stu-id="ea411-152">Opera: current version - 1, Windows only.</span></span>
- <span data-ttu-id="ea411-153">Android 浏览器</span><span class="sxs-lookup"><span data-stu-id="ea411-153">Android browser</span></span>

<span data-ttu-id="ea411-154">除了需要某些浏览器，SignalR 使用的各种传输具有其自己的要求。</span><span class="sxs-lookup"><span data-stu-id="ea411-154">In addition to requiring certain browsers, the various transports that SignalR uses have requirements of their own.</span></span> <span data-ttu-id="ea411-155">下列传输支持在以下配置：</span><span class="sxs-lookup"><span data-stu-id="ea411-155">The following transports are supported under the following configurations:</span></span>

<a id="browser"></a>

<span data-ttu-id="ea411-156">**Web 浏览器传输要求**</span><span class="sxs-lookup"><span data-stu-id="ea411-156">**Web Browser Transport Requirements**</span></span>

| <span data-ttu-id="ea411-157">传输</span><span class="sxs-lookup"><span data-stu-id="ea411-157">Transport</span></span> | <span data-ttu-id="ea411-158">Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="ea411-158">Internet Explorer</span></span> | <span data-ttu-id="ea411-159">Chrome （Windows 或 iOS）</span><span class="sxs-lookup"><span data-stu-id="ea411-159">Chrome (Windows or iOS)</span></span> | <span data-ttu-id="ea411-160">Firefox</span><span class="sxs-lookup"><span data-stu-id="ea411-160">Firefox</span></span> | <span data-ttu-id="ea411-161">Safari （OSX 或 iOS）</span><span class="sxs-lookup"><span data-stu-id="ea411-161">Safari (OSX or iOS)</span></span> | <span data-ttu-id="ea411-162">Android</span><span class="sxs-lookup"><span data-stu-id="ea411-162">Android</span></span> |
| --- | --- | --- | --- | --- | --- |
| <span data-ttu-id="ea411-163">WebSockets</span><span class="sxs-lookup"><span data-stu-id="ea411-163">WebSockets</span></span> | <span data-ttu-id="ea411-164">10+</span><span class="sxs-lookup"><span data-stu-id="ea411-164">10+</span></span> | <span data-ttu-id="ea411-165">当前值-1</span><span class="sxs-lookup"><span data-stu-id="ea411-165">current - 1</span></span> | <span data-ttu-id="ea411-166">当前值-1</span><span class="sxs-lookup"><span data-stu-id="ea411-166">current - 1</span></span> | <span data-ttu-id="ea411-167">当前值-1</span><span class="sxs-lookup"><span data-stu-id="ea411-167">current - 1</span></span> | <span data-ttu-id="ea411-168">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-168">N/A</span></span> |
| <span data-ttu-id="ea411-169">服务器发送事件</span><span class="sxs-lookup"><span data-stu-id="ea411-169">Server-Sent Events</span></span> | <span data-ttu-id="ea411-170">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-170">N/A</span></span> | <span data-ttu-id="ea411-171">当前值-1</span><span class="sxs-lookup"><span data-stu-id="ea411-171">current - 1</span></span> | <span data-ttu-id="ea411-172">当前值-1</span><span class="sxs-lookup"><span data-stu-id="ea411-172">current - 1</span></span> | <span data-ttu-id="ea411-173">当前值-1</span><span class="sxs-lookup"><span data-stu-id="ea411-173">current - 1</span></span> | <span data-ttu-id="ea411-174">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-174">N/A</span></span> |
| <span data-ttu-id="ea411-175">ForeverFrame</span><span class="sxs-lookup"><span data-stu-id="ea411-175">ForeverFrame</span></span> | <span data-ttu-id="ea411-176">8+</span><span class="sxs-lookup"><span data-stu-id="ea411-176">8+</span></span> | <span data-ttu-id="ea411-177">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-177">N/A</span></span> | <span data-ttu-id="ea411-178">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-178">N/A</span></span> | <span data-ttu-id="ea411-179">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-179">N/A</span></span> | <span data-ttu-id="ea411-180">4.1</span><span class="sxs-lookup"><span data-stu-id="ea411-180">4.1</span></span> |
| <span data-ttu-id="ea411-181">长轮询</span><span class="sxs-lookup"><span data-stu-id="ea411-181">Long Polling</span></span> | <span data-ttu-id="ea411-182">8+</span><span class="sxs-lookup"><span data-stu-id="ea411-182">8+</span></span> | <span data-ttu-id="ea411-183">当前值-1</span><span class="sxs-lookup"><span data-stu-id="ea411-183">current - 1</span></span> | <span data-ttu-id="ea411-184">当前值-1</span><span class="sxs-lookup"><span data-stu-id="ea411-184">current - 1</span></span> | <span data-ttu-id="ea411-185">当前值-1</span><span class="sxs-lookup"><span data-stu-id="ea411-185">current - 1</span></span> | <span data-ttu-id="ea411-186">4.1</span><span class="sxs-lookup"><span data-stu-id="ea411-186">4.1</span></span> |

<span data-ttu-id="ea411-187">\*：6 + 所需的全部功能。</span><span class="sxs-lookup"><span data-stu-id="ea411-187">\*: 6+ required for full functionality.</span></span>

#### <a name="unsupported-browsers"></a><span data-ttu-id="ea411-188">不受支持的浏览器</span><span class="sxs-lookup"><span data-stu-id="ea411-188">Unsupported Browsers</span></span>

<span data-ttu-id="ea411-189">尽管 SignalR*可能*运行而无需较旧的浏览器版本中的主要问题，我们不要主动进行测试，SignalR 中它们，并通常不修复可能会出现在它们的 bug。</span><span class="sxs-lookup"><span data-stu-id="ea411-189">While SignalR *may* run without major issues in older browser versions, we do not actively test SignalR in them and generally do not fix bugs that may appear in them.</span></span>

### <a name="windows-desktop-and-silverlight-applications"></a><span data-ttu-id="ea411-190">Windows 桌面和 Silverlight 应用程序</span><span class="sxs-lookup"><span data-stu-id="ea411-190">Windows Desktop and Silverlight Applications</span></span>

<span data-ttu-id="ea411-191">除了在 web 浏览器中运行，可以在独立的 Windows 客户端或 Silverlight 应用程序中托管 SignalR。</span><span class="sxs-lookup"><span data-stu-id="ea411-191">In addition to running in a web browser, SignalR can be hosted in standalone Windows client or Silverlight applications.</span></span> <span data-ttu-id="ea411-192">Windows 桌面和 Silverlight SignalR 应用程序具有以下系统要求。</span><span class="sxs-lookup"><span data-stu-id="ea411-192">Windows Desktop and Silverlight SignalR applications have the following system requirements.</span></span>

- <span data-ttu-id="ea411-193">Windows XP SP3 或更高版本支持使用.NET 4 应用程序。</span><span class="sxs-lookup"><span data-stu-id="ea411-193">Applications using .NET 4 are supported on Windows XP SP3 or later.</span></span>
- <span data-ttu-id="ea411-194">Windows Vista 或更高版本支持使用.NET Framework 4.5 的应用程序。</span><span class="sxs-lookup"><span data-stu-id="ea411-194">Applications using .NET Framework 4.5 are supported on Windows Vista or later.</span></span>

<span data-ttu-id="ea411-195">除了操作系统和.NET framework 要求，可与 SignalR 的传输具有其自己的要求。</span><span class="sxs-lookup"><span data-stu-id="ea411-195">In addition to operating system and .NET framework requirements, the transports available to SignalR have requirements of their own.</span></span> <span data-ttu-id="ea411-196">下列传输支持在以下配置：</span><span class="sxs-lookup"><span data-stu-id="ea411-196">The following transports are supported under the following configurations:</span></span>

<span data-ttu-id="ea411-197">**Windows 桌面和 Silverlight 传输要求**</span><span class="sxs-lookup"><span data-stu-id="ea411-197">**Windows Desktop and Silverlight Transport Requirements**</span></span>

| <span data-ttu-id="ea411-198">传输</span><span class="sxs-lookup"><span data-stu-id="ea411-198">Transport</span></span> | <span data-ttu-id="ea411-199">.NET 应用程序</span><span class="sxs-lookup"><span data-stu-id="ea411-199">.NET application</span></span> | <span data-ttu-id="ea411-200">Silverlight</span><span class="sxs-lookup"><span data-stu-id="ea411-200">Silverlight</span></span> |
| --- | --- | --- |
| <span data-ttu-id="ea411-201">Web 套接字</span><span class="sxs-lookup"><span data-stu-id="ea411-201">Web Sockets</span></span> | <span data-ttu-id="ea411-202">Windows 8 + 和.NET 4.5 +</span><span class="sxs-lookup"><span data-stu-id="ea411-202">Windows 8+ and .NET 4.5+</span></span> | <span data-ttu-id="ea411-203">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-203">N/A</span></span> |
| <span data-ttu-id="ea411-204">永久帧</span><span class="sxs-lookup"><span data-stu-id="ea411-204">Forever Frame</span></span> | <span data-ttu-id="ea411-205">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-205">N/A</span></span> | <span data-ttu-id="ea411-206">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-206">N/A</span></span> |
| <span data-ttu-id="ea411-207">服务器发送事件</span><span class="sxs-lookup"><span data-stu-id="ea411-207">Server-Sent Events</span></span> | <span data-ttu-id="ea411-208">.NET 4+</span><span class="sxs-lookup"><span data-stu-id="ea411-208">.NET 4+</span></span> | <span data-ttu-id="ea411-209">5+</span><span class="sxs-lookup"><span data-stu-id="ea411-209">5+</span></span> |
| <span data-ttu-id="ea411-210">长轮询</span><span class="sxs-lookup"><span data-stu-id="ea411-210">Long Polling</span></span> | <span data-ttu-id="ea411-211">.NET 4+</span><span class="sxs-lookup"><span data-stu-id="ea411-211">.NET 4+</span></span> | <span data-ttu-id="ea411-212">5+</span><span class="sxs-lookup"><span data-stu-id="ea411-212">5+</span></span> |

<a id="android"></a>

### <a name="windows-store-and-windows-phone-applications"></a><span data-ttu-id="ea411-213">Windows 应用商店和 Windows Phone 应用程序</span><span class="sxs-lookup"><span data-stu-id="ea411-213">Windows Store and Windows Phone Applications</span></span>

<span data-ttu-id="ea411-214">可以在 Windows 应用商店应用程序和 Windows Phone 8 应用程序使用 SignalR。</span><span class="sxs-lookup"><span data-stu-id="ea411-214">SignalR can be used in Windows Store applications and Windows Phone 8 applications.</span></span> <span data-ttu-id="ea411-215">下列传输支持在以下配置：</span><span class="sxs-lookup"><span data-stu-id="ea411-215">The following transports are supported under the following configurations:</span></span>

<span data-ttu-id="ea411-216">**Windows 应用商店和 Windows Phone 传输要求**</span><span class="sxs-lookup"><span data-stu-id="ea411-216">**Windows Store and Windows Phone Transport Requirements**</span></span>

| <span data-ttu-id="ea411-217">传输</span><span class="sxs-lookup"><span data-stu-id="ea411-217">Transport</span></span> | <span data-ttu-id="ea411-218">Windows 应用商店 /.NET</span><span class="sxs-lookup"><span data-stu-id="ea411-218">Windows Store/ .NET</span></span> | <span data-ttu-id="ea411-219">Windows 应用商店 / JavaScript</span><span class="sxs-lookup"><span data-stu-id="ea411-219">Windows Store/ JavaScript</span></span> | <span data-ttu-id="ea411-220">Windows Phone/ IE</span><span class="sxs-lookup"><span data-stu-id="ea411-220">Windows Phone/ IE</span></span> | <span data-ttu-id="ea411-221">Windows Phone/ .NET</span><span class="sxs-lookup"><span data-stu-id="ea411-221">Windows Phone/ .NET</span></span> |
| --- | --- | --- | --- | --- |
| <span data-ttu-id="ea411-222">WebSockets</span><span class="sxs-lookup"><span data-stu-id="ea411-222">WebSockets</span></span> | <span data-ttu-id="ea411-223">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-223">N/A</span></span> | <span data-ttu-id="ea411-224">Win8+</span><span class="sxs-lookup"><span data-stu-id="ea411-224">Win8+</span></span> | <span data-ttu-id="ea411-225">8+</span><span class="sxs-lookup"><span data-stu-id="ea411-225">8+</span></span> | <span data-ttu-id="ea411-226">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-226">N/A</span></span> |
| <span data-ttu-id="ea411-227">永久帧</span><span class="sxs-lookup"><span data-stu-id="ea411-227">Forever Frame</span></span> | <span data-ttu-id="ea411-228">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-228">N/A</span></span> | <span data-ttu-id="ea411-229">Win8+</span><span class="sxs-lookup"><span data-stu-id="ea411-229">Win8+</span></span> | <span data-ttu-id="ea411-230">7.5+</span><span class="sxs-lookup"><span data-stu-id="ea411-230">7.5+</span></span> | <span data-ttu-id="ea411-231">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-231">N/A</span></span> |
| <span data-ttu-id="ea411-232">服务器发送事件</span><span class="sxs-lookup"><span data-stu-id="ea411-232">Server-Sent Events</span></span> | <span data-ttu-id="ea411-233">Win8+</span><span class="sxs-lookup"><span data-stu-id="ea411-233">Win8+</span></span> | <span data-ttu-id="ea411-234">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-234">N/A</span></span> | <span data-ttu-id="ea411-235">不可用</span><span class="sxs-lookup"><span data-stu-id="ea411-235">N/A</span></span> | <span data-ttu-id="ea411-236">8+</span><span class="sxs-lookup"><span data-stu-id="ea411-236">8+</span></span> |
| <span data-ttu-id="ea411-237">长轮询</span><span class="sxs-lookup"><span data-stu-id="ea411-237">Long Polling</span></span> | <span data-ttu-id="ea411-238">Win8+</span><span class="sxs-lookup"><span data-stu-id="ea411-238">Win8+</span></span> | <span data-ttu-id="ea411-239">Win8+</span><span class="sxs-lookup"><span data-stu-id="ea411-239">Win8+</span></span> | <span data-ttu-id="ea411-240">7.5+</span><span class="sxs-lookup"><span data-stu-id="ea411-240">7.5+</span></span> | <span data-ttu-id="ea411-241">8+</span><span class="sxs-lookup"><span data-stu-id="ea411-241">8+</span></span> |

<a id="updates"></a>

## <a name="recommended-updates"></a><span data-ttu-id="ea411-242">建议的更新</span><span class="sxs-lookup"><span data-stu-id="ea411-242">Recommended Updates</span></span>

<span data-ttu-id="ea411-243">SignalR 服务器建议以下更新：</span><span class="sxs-lookup"><span data-stu-id="ea411-243">The following updates are recommended for SignalR servers:</span></span>

- <span data-ttu-id="ea411-244">有可用的更新的.NET Framework 4.5[此处](https://support.microsoft.com/kb/2750149)。</span><span class="sxs-lookup"><span data-stu-id="ea411-244">An update for .NET Framework 4.5 is available [here](https://support.microsoft.com/kb/2750149).</span></span>
- <span data-ttu-id="ea411-245">对于 ASP.NET，Microsoft 将定期发布 Qfe。</span><span class="sxs-lookup"><span data-stu-id="ea411-245">Microsoft will periodically release QFEs for ASP.NET.</span></span> <span data-ttu-id="ea411-246">这些应应用为可用。</span><span class="sxs-lookup"><span data-stu-id="ea411-246">These should be applied as available.</span></span>
