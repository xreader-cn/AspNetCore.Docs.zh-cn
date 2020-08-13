---
title: ASP.NET Core 简介
author: rick-anderson
description: 获取 ASP.NET Core 的简介，它是一个跨平台的高性能开源框架，用于生成启用云且连接 Internet 的新式应用。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: index
ms.openlocfilehash: 9cf721b381d3c4c737c15225edcf7918dd1fcf5b
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88015343"
---
# <a name="introduction-to-aspnet-core"></a><span data-ttu-id="db8ff-103">ASP.NET Core 简介</span><span class="sxs-lookup"><span data-stu-id="db8ff-103">Introduction to ASP.NET Core</span></span>

<span data-ttu-id="db8ff-104">作者：[Daniel Roth](https://github.com/danroth27)、[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Shaun Luttin](https://twitter.com/dicshaunary)</span><span class="sxs-lookup"><span data-stu-id="db8ff-104">By [Daniel Roth](https://github.com/danroth27), [Rick Anderson](https://twitter.com/RickAndMSFT), and [Shaun Luttin](https://twitter.com/dicshaunary)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="db8ff-105">ASP.NET Core 是一个跨平台的高性能[开源](https://github.com/dotnet/aspnetcore)框架，用于生成启用云且连接 Internet 的新式应用。</span><span class="sxs-lookup"><span data-stu-id="db8ff-105">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="db8ff-106">使用 ASP.NET Core，您可以：</span><span class="sxs-lookup"><span data-stu-id="db8ff-106">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="db8ff-107">生成 Web 应用和服务、[物联网 (IoT)](https://www.microsoft.com/internet-of-things/) 应用和移动后端。</span><span class="sxs-lookup"><span data-stu-id="db8ff-107">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="db8ff-108">在 Windows、macOS 和 Linux 上使用喜爱的开发工具。</span><span class="sxs-lookup"><span data-stu-id="db8ff-108">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="db8ff-109">部署到云或本地。</span><span class="sxs-lookup"><span data-stu-id="db8ff-109">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="db8ff-110">在 [.NET Core](/dotnet/core/introduction) 上运行。</span><span class="sxs-lookup"><span data-stu-id="db8ff-110">Run on [.NET Core](/dotnet/core/introduction).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="db8ff-111">为何选择 ASP.NET Core？</span><span class="sxs-lookup"><span data-stu-id="db8ff-111">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="db8ff-112">数百万开发人员在使用或使用过 [ASP.NET 4.x](/aspnet/overview) 创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="db8ff-112">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="db8ff-113">ASP.NET Core 是对 ASP.NET 4.x 的重新设计，其中包括体系结构上的更改，产生了更精简、更模块化的框架。</span><span class="sxs-lookup"><span data-stu-id="db8ff-113">ASP.NET Core is a redesign of ASP.NET 4.x, including architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="db8ff-114">使用 ASP.NET Core MVC 生成 Web API 和 Web UI</span><span class="sxs-lookup"><span data-stu-id="db8ff-114">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="db8ff-115">ASP.NET Core MVC 提供生成 [Web API](xref:tutorials/first-web-api) 和 [Web 应用](xref:tutorials/razor-pages/index)所需的功能：</span><span class="sxs-lookup"><span data-stu-id="db8ff-115">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="db8ff-116">[Model-View-Controller (MVC) 模式](xref:mvc/overview) 使 Web API 和 Web 应用可测试。</span><span class="sxs-lookup"><span data-stu-id="db8ff-116">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="db8ff-117">[Razor Pages](xref:razor-pages/index) 是基于页面的编程模型，它让 Web UI 的生成更加简单高效。</span><span class="sxs-lookup"><span data-stu-id="db8ff-117">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="db8ff-118">[Razor 标记](xref:mvc/views/razor)提供了适用于 [Razor Pages](xref:razor-pages/index) 和 [MVC 视图](xref:mvc/views/overview)的高效语法。</span><span class="sxs-lookup"><span data-stu-id="db8ff-118">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="db8ff-119">[标记帮助程序](xref:mvc/views/tag-helpers/intro)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="db8ff-119">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="db8ff-120">内置的[多数据格式和内容协商](xref:web-api/advanced/formatting)支持使 Web API 可访问多种客户端，包括浏览器和移动设备。</span><span class="sxs-lookup"><span data-stu-id="db8ff-120">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="db8ff-121">[模型绑定](xref:mvc/models/model-binding)自动将 HTTP 请求中的数据映射到操作方法参数。</span><span class="sxs-lookup"><span data-stu-id="db8ff-121">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="db8ff-122">[模型验证](xref:mvc/models/validation)自动执行客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="db8ff-122">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="db8ff-123">客户端开发</span><span class="sxs-lookup"><span data-stu-id="db8ff-123">Client-side development</span></span>

<span data-ttu-id="db8ff-124">ASP.NET Core 与常用客户端框架和库（包括 [Blazor](xref:blazor/index)、[Angular](xref:spa/angular)、[React](xref:spa/react) 和 [Bootstrap](https://getbootstrap.com/)）无缝集成。</span><span class="sxs-lookup"><span data-stu-id="db8ff-124">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="db8ff-125">有关详细信息，请参阅 <xref:blazor/index> 和“客户端开发”下的相关主题。</span><span class="sxs-lookup"><span data-stu-id="db8ff-125">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-target-frameworks"></a><span data-ttu-id="db8ff-126">ASP.NET Core 目标框架</span><span class="sxs-lookup"><span data-stu-id="db8ff-126">ASP.NET Core target frameworks</span></span>

<span data-ttu-id="db8ff-127">ASP.NET Core 3.x 和更高版本只能面向 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="db8ff-127">ASP.NET Core 3.x and later can only target .NET Core.</span></span> <span data-ttu-id="db8ff-128">通常，ASP.NET Core 由 [.NET Standard](/dotnet/standard/net-standard) 库组成。</span><span class="sxs-lookup"><span data-stu-id="db8ff-128">Generally, ASP.NET Core is composed of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="db8ff-129">使用 .NET Standard 2.0 编写的库在[实现 .NET Standard 2.0 的任何 .NET 平台](/dotnet/standard/net-standard#net-implementation-support)上运行。</span><span class="sxs-lookup"><span data-stu-id="db8ff-129">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="db8ff-130">面向 .NET Core 有以下几个优势，并且这些优势会随着每次发布增加。</span><span class="sxs-lookup"><span data-stu-id="db8ff-130">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="db8ff-131">与 .NET Framework 相比，.NET Core 的部分优势包括：</span><span class="sxs-lookup"><span data-stu-id="db8ff-131">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="db8ff-132">跨平台。</span><span class="sxs-lookup"><span data-stu-id="db8ff-132">Cross-platform.</span></span> <span data-ttu-id="db8ff-133">在 Windows、macOS 和 Linux 上运行。</span><span class="sxs-lookup"><span data-stu-id="db8ff-133">Runs on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="db8ff-134">性能更强</span><span class="sxs-lookup"><span data-stu-id="db8ff-134">Improved performance</span></span>
* [<span data-ttu-id="db8ff-135">并行版本控制</span><span class="sxs-lookup"><span data-stu-id="db8ff-135">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="db8ff-136">新 API</span><span class="sxs-lookup"><span data-stu-id="db8ff-136">New APIs</span></span>
* <span data-ttu-id="db8ff-137">开源</span><span class="sxs-lookup"><span data-stu-id="db8ff-137">Open source</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="db8ff-138">推荐的学习路径</span><span class="sxs-lookup"><span data-stu-id="db8ff-138">Recommended learning path</span></span>

<span data-ttu-id="db8ff-139">建议通过以下一系列教程来了解如何开发 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="db8ff-139">We recommend the following sequence of tutorials for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="db8ff-140">按照你要开发或维护的应用类型的教程操作：</span><span class="sxs-lookup"><span data-stu-id="db8ff-140">Follow a tutorial for the app type you want to develop or maintain.</span></span>

   |<span data-ttu-id="db8ff-141">应用类型</span><span class="sxs-lookup"><span data-stu-id="db8ff-141">App type</span></span>  |<span data-ttu-id="db8ff-142">方案</span><span class="sxs-lookup"><span data-stu-id="db8ff-142">Scenario</span></span>  |<span data-ttu-id="db8ff-143">教程</span><span class="sxs-lookup"><span data-stu-id="db8ff-143">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="db8ff-144">Web 应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-144">Web app</span></span>                   | <span data-ttu-id="db8ff-145">新的服务器端 Web UI 开发</span><span class="sxs-lookup"><span data-stu-id="db8ff-145">New server-side web UI development</span></span> |[<span data-ttu-id="db8ff-146">Razor Pages 入门</span><span class="sxs-lookup"><span data-stu-id="db8ff-146">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="db8ff-147">Web 应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-147">Web app</span></span>                   | <span data-ttu-id="db8ff-148">维护 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-148">Maintaining an MVC app</span></span> |[<span data-ttu-id="db8ff-149">MVC 入门</span><span class="sxs-lookup"><span data-stu-id="db8ff-149">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="db8ff-150">Web 应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-150">Web app</span></span>                   | <span data-ttu-id="db8ff-151">客户端 Web UI 开发</span><span class="sxs-lookup"><span data-stu-id="db8ff-151">Client-side web UI development</span></span> |[<span data-ttu-id="db8ff-152">开始使用 Blazor</span><span class="sxs-lookup"><span data-stu-id="db8ff-152">Get started with Blazor</span></span>](https://dotnet.microsoft.com/learn/aspnet/blazor-tutorial/intro) |
   |<span data-ttu-id="db8ff-153">Web API</span><span class="sxs-lookup"><span data-stu-id="db8ff-153">Web API</span></span>                   | <span data-ttu-id="db8ff-154">RESTful HTTP 服务</span><span class="sxs-lookup"><span data-stu-id="db8ff-154">RESTful HTTP services</span></span> |<span data-ttu-id="db8ff-155">[创建 Web API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="db8ff-155">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="db8ff-156">远程过程调用应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-156">Remote Procedure Call app</span></span> | <span data-ttu-id="db8ff-157">使用协议缓冲区的协定优先服务</span><span class="sxs-lookup"><span data-stu-id="db8ff-157">Contract-first services using Protocol Buffers</span></span> |[<span data-ttu-id="db8ff-158">开始使用 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="db8ff-158">Get started with a gRPC service</span></span>](xref:tutorials/grpc/grpc-start) |
   |<span data-ttu-id="db8ff-159">实时应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-159">Real-time app</span></span>             | <span data-ttu-id="db8ff-160">服务器和连接的客户端之间的双向通信</span><span class="sxs-lookup"><span data-stu-id="db8ff-160">Bidirectional communication between servers and connected clients</span></span> |[<span data-ttu-id="db8ff-161">开始使用 SignalR</span><span class="sxs-lookup"><span data-stu-id="db8ff-161">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="db8ff-162">按照介绍如何进行基本数据访问的教程操作。</span><span class="sxs-lookup"><span data-stu-id="db8ff-162">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="db8ff-163">方案</span><span class="sxs-lookup"><span data-stu-id="db8ff-163">Scenario</span></span>  |<span data-ttu-id="db8ff-164">教程</span><span class="sxs-lookup"><span data-stu-id="db8ff-164">Tutorial</span></span>  |
   |----------|----------|
   |<span data-ttu-id="db8ff-165">新的开发</span><span class="sxs-lookup"><span data-stu-id="db8ff-165">New development</span></span>        |[<span data-ttu-id="db8ff-166">带 Entity Framework Core 的 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="db8ff-166">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   |<span data-ttu-id="db8ff-167">维护 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-167">Maintaining an MVC app</span></span> |[<span data-ttu-id="db8ff-168">结合使用 MVC 和 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="db8ff-168">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="db8ff-169">阅读适用于所有应用类型的 ASP.NET Core [基础知识](xref:fundamentals/index)的概述。</span><span class="sxs-lookup"><span data-stu-id="db8ff-169">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="db8ff-170">浏览目录以了解其他感兴趣的主题。</span><span class="sxs-lookup"><span data-stu-id="db8ff-170">Browse the table of contents for other topics of interest.</span></span>

<span data-ttu-id="db8ff-171">&dagger;此外，还提供了一个[交互式 Web API 教程](/learn/modules/build-web-api-net-core)。</span><span class="sxs-lookup"><span data-stu-id="db8ff-171">&dagger;There's also an [interactive web API tutorial](/learn/modules/build-web-api-net-core).</span></span> <span data-ttu-id="db8ff-172">无需在本地安装开发工具。</span><span class="sxs-lookup"><span data-stu-id="db8ff-172">No local installation of development tools is required.</span></span> <span data-ttu-id="db8ff-173">代码在浏览器中的 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 中运行，并且 [curl](https://curl.haxx.se/) 用于测试。</span><span class="sxs-lookup"><span data-stu-id="db8ff-173">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) in your browser, and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="db8ff-174">从 .NET Framework 迁移</span><span class="sxs-lookup"><span data-stu-id="db8ff-174">Migrate from .NET Framework</span></span>

<span data-ttu-id="db8ff-175">有关将 ASP.NET 4.x 应用迁移到 ASP.NET Core 的参考指南，请参阅 <xref:migration/proper-to-2x/index>。</span><span class="sxs-lookup"><span data-stu-id="db8ff-175">For a reference guide to migrating ASP.NET 4.x apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="db8ff-176">ASP.NET Core 是一个跨平台的高性能[开源](https://github.com/dotnet/aspnetcore)框架，用于生成启用云且连接 Internet 的新式应用。</span><span class="sxs-lookup"><span data-stu-id="db8ff-176">ASP.NET Core is a cross-platform, high-performance, [open-source](https://github.com/dotnet/aspnetcore) framework for building modern, cloud-enabled, Internet-connected apps.</span></span> <span data-ttu-id="db8ff-177">使用 ASP.NET Core，您可以：</span><span class="sxs-lookup"><span data-stu-id="db8ff-177">With ASP.NET Core, you can:</span></span>

* <span data-ttu-id="db8ff-178">生成 Web 应用和服务、[物联网 (IoT)](https://www.microsoft.com/internet-of-things/) 应用和移动后端。</span><span class="sxs-lookup"><span data-stu-id="db8ff-178">Build web apps and services, [Internet of Things (IoT)](https://www.microsoft.com/internet-of-things/) apps, and mobile backends.</span></span>
* <span data-ttu-id="db8ff-179">在 Windows、macOS 和 Linux 上使用喜爱的开发工具。</span><span class="sxs-lookup"><span data-stu-id="db8ff-179">Use your favorite development tools on Windows, macOS, and Linux.</span></span>
* <span data-ttu-id="db8ff-180">部署到云或本地。</span><span class="sxs-lookup"><span data-stu-id="db8ff-180">Deploy to the cloud or on-premises.</span></span>
* <span data-ttu-id="db8ff-181">在 [.NET Core 或 .NET Framework](/dotnet/articles/standard/choosing-core-framework-server) 上运行。</span><span class="sxs-lookup"><span data-stu-id="db8ff-181">Run on [.NET Core or .NET Framework](/dotnet/articles/standard/choosing-core-framework-server).</span></span>

## <a name="why-choose-aspnet-core"></a><span data-ttu-id="db8ff-182">为何选择 ASP.NET Core？</span><span class="sxs-lookup"><span data-stu-id="db8ff-182">Why choose ASP.NET Core?</span></span>

<span data-ttu-id="db8ff-183">数百万开发人员在使用或使用过 [ASP.NET 4.x](/aspnet/overview) 创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="db8ff-183">Millions of developers use or have used [ASP.NET 4.x](/aspnet/overview) to create web apps.</span></span> <span data-ttu-id="db8ff-184">ASP.NET Core 是对 ASP.NET 4.x 的重新设计，通过体系结构上的更改，产生了更精简、更模块化的框架。</span><span class="sxs-lookup"><span data-stu-id="db8ff-184">ASP.NET Core is a redesign of ASP.NET 4.x, with architectural changes that result in a leaner, more modular framework.</span></span>

[!INCLUDE[](~/includes/benefits.md)]

## <a name="build-web-apis-and-web-ui-using-aspnet-core-mvc"></a><span data-ttu-id="db8ff-185">使用 ASP.NET Core MVC 生成 Web API 和 Web UI</span><span class="sxs-lookup"><span data-stu-id="db8ff-185">Build web APIs and web UI using ASP.NET Core MVC</span></span>

<span data-ttu-id="db8ff-186">ASP.NET Core MVC 提供生成 [Web API](xref:tutorials/first-web-api) 和 [Web 应用](xref:tutorials/razor-pages/index)所需的功能：</span><span class="sxs-lookup"><span data-stu-id="db8ff-186">ASP.NET Core MVC provides features to build [web APIs](xref:tutorials/first-web-api) and [web apps](xref:tutorials/razor-pages/index):</span></span>

* <span data-ttu-id="db8ff-187">[Model-View-Controller (MVC) 模式](xref:mvc/overview) 使 Web API 和 Web 应用可测试。</span><span class="sxs-lookup"><span data-stu-id="db8ff-187">The [Model-View-Controller (MVC) pattern](xref:mvc/overview) helps make your web APIs and web apps testable.</span></span>
* <span data-ttu-id="db8ff-188">[Razor Pages](xref:razor-pages/index) 是基于页面的编程模型，它让 Web UI 的生成更加简单高效。</span><span class="sxs-lookup"><span data-stu-id="db8ff-188">[Razor Pages](xref:razor-pages/index) is a page-based programming model that makes building web UI easier and more productive.</span></span>
* <span data-ttu-id="db8ff-189">[Razor 标记](xref:mvc/views/razor)提供了适用于 [Razor Pages](xref:razor-pages/index) 和 [MVC 视图](xref:mvc/views/overview)的高效语法。</span><span class="sxs-lookup"><span data-stu-id="db8ff-189">[Razor markup](xref:mvc/views/razor) provides a productive syntax for [Razor Pages](xref:razor-pages/index) and [MVC views](xref:mvc/views/overview).</span></span>
* <span data-ttu-id="db8ff-190">[标记帮助程序](xref:mvc/views/tag-helpers/intro)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="db8ff-190">[Tag Helpers](xref:mvc/views/tag-helpers/intro) enable server-side code to participate in creating and rendering HTML elements in Razor files.</span></span>
* <span data-ttu-id="db8ff-191">内置的[多数据格式和内容协商](xref:web-api/advanced/formatting)支持使 Web API 可访问多种客户端，包括浏览器和移动设备。</span><span class="sxs-lookup"><span data-stu-id="db8ff-191">Built-in support for [multiple data formats and content negotiation](xref:web-api/advanced/formatting) lets your web APIs reach a broad range of clients, including browsers and mobile devices.</span></span>
* <span data-ttu-id="db8ff-192">[模型绑定](xref:mvc/models/model-binding)自动将 HTTP 请求中的数据映射到操作方法参数。</span><span class="sxs-lookup"><span data-stu-id="db8ff-192">[Model binding](xref:mvc/models/model-binding) automatically maps data from HTTP requests to action method parameters.</span></span>
* <span data-ttu-id="db8ff-193">[模型验证](xref:mvc/models/validation)自动执行客户端和服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="db8ff-193">[Model validation](xref:mvc/models/validation) automatically performs client-side and server-side validation.</span></span>

## <a name="client-side-development"></a><span data-ttu-id="db8ff-194">客户端开发</span><span class="sxs-lookup"><span data-stu-id="db8ff-194">Client-side development</span></span>

<span data-ttu-id="db8ff-195">ASP.NET Core 与常用客户端框架和库（包括 [Blazor](xref:blazor/index)、[Angular](xref:spa/angular)、[React](xref:spa/react) 和 [Bootstrap](https://getbootstrap.com/)）无缝集成。</span><span class="sxs-lookup"><span data-stu-id="db8ff-195">ASP.NET Core integrates seamlessly with popular client-side frameworks and libraries, including [Blazor](xref:blazor/index), [Angular](xref:spa/angular), [React](xref:spa/react), and [Bootstrap](https://getbootstrap.com/).</span></span> <span data-ttu-id="db8ff-196">有关详细信息，请参阅 <xref:blazor/index> 和“客户端开发”下的相关主题。</span><span class="sxs-lookup"><span data-stu-id="db8ff-196">For more information, see <xref:blazor/index> and related topics under *Client-side development*.</span></span>

<a name="target-framework"></a>

## <a name="aspnet-core-targeting-net-framework"></a><span data-ttu-id="db8ff-197">面向 .NET Framework 的 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="db8ff-197">ASP.NET Core targeting .NET Framework</span></span>

<span data-ttu-id="db8ff-198">ASP.NET Core 2.x 可以面向 .NET Core 或 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="db8ff-198">ASP.NET Core 2.x can target .NET Core or .NET Framework.</span></span> <span data-ttu-id="db8ff-199">面向 .NET Framework 的 ASP.NET Core 应用无法跨平台，它们仅在 Windows 上运行。</span><span class="sxs-lookup"><span data-stu-id="db8ff-199">ASP.NET Core apps targeting .NET Framework aren't cross-platform&mdash;they run on Windows only.</span></span> <span data-ttu-id="db8ff-200">通常，ASP.NET Core 2.x 由 [.NET Standard](/dotnet/standard/net-standard) 库组成。</span><span class="sxs-lookup"><span data-stu-id="db8ff-200">Generally, ASP.NET Core 2.x is made up of [.NET Standard](/dotnet/standard/net-standard) libraries.</span></span> <span data-ttu-id="db8ff-201">使用 .NET Standard 2.0 编写的库在[实现 .NET Standard 2.0 的任何 .NET 平台](/dotnet/standard/net-standard#net-implementation-support)上运行。</span><span class="sxs-lookup"><span data-stu-id="db8ff-201">Libraries written with .NET Standard 2.0 run on any [.NET platform that implements .NET Standard 2.0](/dotnet/standard/net-standard#net-implementation-support).</span></span>

<span data-ttu-id="db8ff-202">ASP.NET Core 2.x 在实现 .NET Standard 2.0 的 .NET Framework 版本上受支持：</span><span class="sxs-lookup"><span data-stu-id="db8ff-202">ASP.NET Core 2.x is supported on .NET Framework versions that implement .NET Standard 2.0:</span></span>

* <span data-ttu-id="db8ff-203">建议使用最新版本的 .NET Framework。</span><span class="sxs-lookup"><span data-stu-id="db8ff-203">.NET Framework latest version is recommended.</span></span>
* <span data-ttu-id="db8ff-204">.NET Framework 4.6.1 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="db8ff-204">.NET Framework 4.6.1 and later.</span></span>

<span data-ttu-id="db8ff-205">ASP.NET Core 3.0 以及更高版本只能在 .NET Core 中运行。</span><span class="sxs-lookup"><span data-stu-id="db8ff-205">ASP.NET Core 3.0 and later will only run on .NET Core.</span></span> <span data-ttu-id="db8ff-206">有关此更改的详细信息，请参阅 [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/)（抢先了解 ASP.NET Core 3.0 即将推出的更改）。</span><span class="sxs-lookup"><span data-stu-id="db8ff-206">For more details regarding this change, see [A first look at changes coming in ASP.NET Core 3.0](https://blogs.msdn.microsoft.com/webdev/2018/10/29/a-first-look-at-changes-coming-in-asp-net-core-3-0/).</span></span>

<span data-ttu-id="db8ff-207">面向 .NET Core 有以下几个优势，并且这些优势会随着每次发布增加。</span><span class="sxs-lookup"><span data-stu-id="db8ff-207">There are several advantages to targeting .NET Core, and these advantages increase with each release.</span></span> <span data-ttu-id="db8ff-208">与 .NET Framework 相比，.NET Core 的部分优势包括：</span><span class="sxs-lookup"><span data-stu-id="db8ff-208">Some advantages of .NET Core over .NET Framework include:</span></span>

* <span data-ttu-id="db8ff-209">跨平台。</span><span class="sxs-lookup"><span data-stu-id="db8ff-209">Cross-platform.</span></span> <span data-ttu-id="db8ff-210">在 macOS、Linux 和 Windows 上运行。</span><span class="sxs-lookup"><span data-stu-id="db8ff-210">Runs on macOS, Linux, and Windows.</span></span>
* <span data-ttu-id="db8ff-211">性能更强</span><span class="sxs-lookup"><span data-stu-id="db8ff-211">Improved performance</span></span>
* [<span data-ttu-id="db8ff-212">并行版本控制</span><span class="sxs-lookup"><span data-stu-id="db8ff-212">Side-by-side versioning</span></span>](/dotnet/standard/choosing-core-framework-server#side-by-side-net-versions-per-application-level)
* <span data-ttu-id="db8ff-213">新 API</span><span class="sxs-lookup"><span data-stu-id="db8ff-213">New APIs</span></span>
* <span data-ttu-id="db8ff-214">开源</span><span class="sxs-lookup"><span data-stu-id="db8ff-214">Open source</span></span>

<span data-ttu-id="db8ff-215">为了帮助缩小从 .NET Framework 到 .NET Core 的 API 差距，[Windows 兼容包](/dotnet/core/porting/windows-compat-pack)使数千个仅可在 Windows 中运行的 API 可在 .NET Core 中使用。</span><span class="sxs-lookup"><span data-stu-id="db8ff-215">To help close the API gap from .NET Framework to .NET Core, the [Windows Compatibility Pack](/dotnet/core/porting/windows-compat-pack) made thousands of Windows-only APIs available in .NET Core.</span></span> <span data-ttu-id="db8ff-216">这些 API 在 .NET Core 1.x 中不可用。</span><span class="sxs-lookup"><span data-stu-id="db8ff-216">These APIs weren't available in .NET Core 1.x.</span></span>

## <a name="recommended-learning-path"></a><span data-ttu-id="db8ff-217">推荐的学习路径</span><span class="sxs-lookup"><span data-stu-id="db8ff-217">Recommended learning path</span></span>

<span data-ttu-id="db8ff-218">建议通过以下一系列教程和文章来了解如何开发 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="db8ff-218">We recommend the following sequence of tutorials and articles for an introduction to developing ASP.NET Core apps:</span></span>

1. <span data-ttu-id="db8ff-219">按照你要开发或维护的应用类型的教程操作。</span><span class="sxs-lookup"><span data-stu-id="db8ff-219">Follow a tutorial for the type of app you want to develop or maintain.</span></span>

   |<span data-ttu-id="db8ff-220">应用类型</span><span class="sxs-lookup"><span data-stu-id="db8ff-220">App type</span></span>  |<span data-ttu-id="db8ff-221">方案</span><span class="sxs-lookup"><span data-stu-id="db8ff-221">Scenario</span></span>  |<span data-ttu-id="db8ff-222">教程</span><span class="sxs-lookup"><span data-stu-id="db8ff-222">Tutorial</span></span>  |
   |----------|----------|----------|
   |<span data-ttu-id="db8ff-223">Web 应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-223">Web app</span></span>                   | <span data-ttu-id="db8ff-224">用于新的开发</span><span class="sxs-lookup"><span data-stu-id="db8ff-224">For new development</span></span>        |[<span data-ttu-id="db8ff-225">Razor Pages 入门</span><span class="sxs-lookup"><span data-stu-id="db8ff-225">Get started with Razor Pages</span></span>](xref:tutorials/razor-pages/razor-pages-start) |
   |<span data-ttu-id="db8ff-226">Web 应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-226">Web app</span></span>                   | <span data-ttu-id="db8ff-227">用于维护 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-227">For maintaining an MVC app</span></span> |[<span data-ttu-id="db8ff-228">MVC 入门</span><span class="sxs-lookup"><span data-stu-id="db8ff-228">Get started with MVC</span></span>](xref:tutorials/first-mvc-app/start-mvc)|
   |<span data-ttu-id="db8ff-229">Web API</span><span class="sxs-lookup"><span data-stu-id="db8ff-229">Web API</span></span>                   |                            |<span data-ttu-id="db8ff-230">[创建 Web API](xref:tutorials/first-web-api)&dagger;</span><span class="sxs-lookup"><span data-stu-id="db8ff-230">[Create a web API](xref:tutorials/first-web-api)&dagger;</span></span> |
   |<span data-ttu-id="db8ff-231">实时应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-231">Real-time app</span></span>             |                            |[<span data-ttu-id="db8ff-232">开始使用 SignalR</span><span class="sxs-lookup"><span data-stu-id="db8ff-232">Get started with SignalR</span></span>](xref:tutorials/signalr) |

1. <span data-ttu-id="db8ff-233">按照介绍如何进行基本数据访问的教程操作。</span><span class="sxs-lookup"><span data-stu-id="db8ff-233">Follow a tutorial that shows how to do basic data access.</span></span>

   |<span data-ttu-id="db8ff-234">方案</span><span class="sxs-lookup"><span data-stu-id="db8ff-234">Scenario</span></span>  |<span data-ttu-id="db8ff-235">教程</span><span class="sxs-lookup"><span data-stu-id="db8ff-235">Tutorial</span></span>  |
   |----------|----------|
   | <span data-ttu-id="db8ff-236">用于新的开发</span><span class="sxs-lookup"><span data-stu-id="db8ff-236">For new development</span></span>        |[<span data-ttu-id="db8ff-237">带 Entity Framework Core 的 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="db8ff-237">Razor Pages with Entity Framework Core</span></span>](xref:data/ef-rp/intro) |
   | <span data-ttu-id="db8ff-238">用于维护 MVC 应用</span><span class="sxs-lookup"><span data-stu-id="db8ff-238">For maintaining an MVC app</span></span> |[<span data-ttu-id="db8ff-239">结合使用 MVC 和 Entity Framework Core</span><span class="sxs-lookup"><span data-stu-id="db8ff-239">MVC with Entity Framework Core</span></span>](xref:data/ef-mvc/intro) |

1. <span data-ttu-id="db8ff-240">阅读适用于所有应用类型的 ASP.NET Core [基础知识](xref:fundamentals/index)的概述。</span><span class="sxs-lookup"><span data-stu-id="db8ff-240">Read an overview of ASP.NET Core [fundamentals](xref:fundamentals/index) that apply to all app types.</span></span>

1. <span data-ttu-id="db8ff-241">浏览目录以了解其他感兴趣的主题。</span><span class="sxs-lookup"><span data-stu-id="db8ff-241">Browse the Table of Contents for other topics of interest.</span></span>

<span data-ttu-id="db8ff-242">&dagger;此外，还提供了一个 [Web API 教程](/learn/modules/build-web-api-net-core)，需要在浏览器中完全遵循，无需在本地安装 IDE。</span><span class="sxs-lookup"><span data-stu-id="db8ff-242">&dagger;There's also a [web API tutorial that you follow entirely in the browser](/learn/modules/build-web-api-net-core), no local IDE installation required.</span></span> <span data-ttu-id="db8ff-243">代码在 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 中运行，并且 [curl](https://curl.haxx.se/) 用于测试。</span><span class="sxs-lookup"><span data-stu-id="db8ff-243">The code runs in an [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), and [curl](https://curl.haxx.se/) is used for testing.</span></span>

## <a name="migrate-from-net-framework"></a><span data-ttu-id="db8ff-244">从 .NET Framework 迁移</span><span class="sxs-lookup"><span data-stu-id="db8ff-244">Migrate from .NET Framework</span></span>

<span data-ttu-id="db8ff-245">有关将 ASP.NET 应用迁移到 ASP.NET Core 的参考指南，请参阅 <xref:migration/proper-to-2x/index>。</span><span class="sxs-lookup"><span data-stu-id="db8ff-245">For a reference guide to migrating ASP.NET apps to ASP.NET Core, see <xref:migration/proper-to-2x/index>.</span></span>

::: moniker-end

## <a name="how-to-download-a-sample"></a><span data-ttu-id="db8ff-246">如何下载示例</span><span class="sxs-lookup"><span data-stu-id="db8ff-246">How to download a sample</span></span>

<span data-ttu-id="db8ff-247">很多文章和教程中都包含有示例代码链接。</span><span class="sxs-lookup"><span data-stu-id="db8ff-247">Many of the articles and tutorials include links to sample code.</span></span>

1. <span data-ttu-id="db8ff-248">[下载 ASP.NET 存储库 zip 文件](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master)。</span><span class="sxs-lookup"><span data-stu-id="db8ff-248">[Download the ASP.NET repository zip file](https://codeload.github.com/dotnet/AspNetCore.Docs/zip/master).</span></span>
1. <span data-ttu-id="db8ff-249">解压缩 Docs-master.zip 文件。</span><span class="sxs-lookup"><span data-stu-id="db8ff-249">Unzip the *Docs-master.zip* file.</span></span>
1. <span data-ttu-id="db8ff-250">使用示例链接中的 URL 帮助你导航到示例目录。</span><span class="sxs-lookup"><span data-stu-id="db8ff-250">Use the URL in the sample link to help you navigate to the sample directory.</span></span>

### <a name="preprocessor-directives-in-sample-code"></a><span data-ttu-id="db8ff-251">示例代码中的预处理器指令</span><span class="sxs-lookup"><span data-stu-id="db8ff-251">Preprocessor directives in sample code</span></span>

<span data-ttu-id="db8ff-252">为了演示多个方案，示例应用使用 `#define` 和 `#if-#else/#elif-#endif` 预处理器指令选择性地编译和运行示例代码中不同的片段。</span><span class="sxs-lookup"><span data-stu-id="db8ff-252">To demonstrate multiple scenarios, sample apps use the `#define` and `#if-#else/#elif-#endif` preprocessor directives to selectively compile and run different sections of sample code.</span></span> <span data-ttu-id="db8ff-253">对于那些利用此方法的示例，请将 C# 文件顶部的 `#define` 指令设置为定义与你想要运行的方案相关联的符号。</span><span class="sxs-lookup"><span data-stu-id="db8ff-253">For those samples that make use of this approach, set the `#define` directive at the top of the C# files to define the symbol associated with the scenario that you want to run.</span></span> <span data-ttu-id="db8ff-254">一些示例要求在多个文件的顶部定义符号才能运行方案。</span><span class="sxs-lookup"><span data-stu-id="db8ff-254">Some samples require defining the symbol at the top of multiple files in order to run a scenario.</span></span>

<span data-ttu-id="db8ff-255">例如，以下 `#define` 符号列表指示四个方案可用（每个符号一个方案）。</span><span class="sxs-lookup"><span data-stu-id="db8ff-255">For example, the following `#define` symbol list indicates that four scenarios are available (one scenario per symbol).</span></span> <span data-ttu-id="db8ff-256">当前示例配置运行 `TemplateCode` 方案：</span><span class="sxs-lookup"><span data-stu-id="db8ff-256">The current sample configuration runs the `TemplateCode` scenario:</span></span>

```csharp
#define TemplateCode // or LogFromMain or ExpandDefault or FilterInCode
```

<span data-ttu-id="db8ff-257">若要更改示例以运行 `ExpandDefault` 方案，请定义 `ExpandDefault` 符号并保留剩余的符号处于被注释掉的状态：</span><span class="sxs-lookup"><span data-stu-id="db8ff-257">To change the sample to run the `ExpandDefault` scenario, define the `ExpandDefault` symbol and leave the remaining symbols commented-out:</span></span>

```csharp
#define ExpandDefault // TemplateCode or LogFromMain or FilterInCode
```

<span data-ttu-id="db8ff-258">若要详细了解如何使用 [C# 预处理器指令](/dotnet/csharp/language-reference/preprocessor-directives/)选择性地编译代码段，请参阅 [#define（C# 参考）](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define)和 [#if（C# 参考）](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if)。</span><span class="sxs-lookup"><span data-stu-id="db8ff-258">For more information on using [C# preprocessor directives](/dotnet/csharp/language-reference/preprocessor-directives/) to selectively compile sections of code, see [#define (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-define) and [#if (C# Reference)](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-if).</span></span>

### <a name="regions-in-sample-code"></a><span data-ttu-id="db8ff-259">示例代码中的区域</span><span class="sxs-lookup"><span data-stu-id="db8ff-259">Regions in sample code</span></span>

<span data-ttu-id="db8ff-260">一些示例应用包含由 [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) 和 [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# 指令包围的代码片段。</span><span class="sxs-lookup"><span data-stu-id="db8ff-260">Some sample apps contain sections of code surrounded by [#region](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-region) and [#endregion](/dotnet/csharp/language-reference/preprocessor-directives/preprocessor-endregion) C# directives.</span></span> <span data-ttu-id="db8ff-261">文档生成系统会将这些区域注入到所呈现的文档主题中。</span><span class="sxs-lookup"><span data-stu-id="db8ff-261">The documentation build system injects these regions into the rendered documentation topics.</span></span>  

<span data-ttu-id="db8ff-262">区域名称通常包含“代码段”一词。</span><span class="sxs-lookup"><span data-stu-id="db8ff-262">Region names usually contain the word "snippet."</span></span> <span data-ttu-id="db8ff-263">下面的示例显示了一个名为 `snippet_WebHostDefaults` 的区域：</span><span class="sxs-lookup"><span data-stu-id="db8ff-263">The following example shows a region named `snippet_WebHostDefaults`:</span></span>

```csharp
#region snippet_WebHostDefaults
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.UseStartup<Startup>();
    });
#endregion
```

<span data-ttu-id="db8ff-264">主题的 Markdown 文件在以下行中引用了前面的 C# 代码片段：</span><span class="sxs-lookup"><span data-stu-id="db8ff-264">The preceding C# code snippet is referenced in the topic's markdown file with the following line:</span></span>

```md
[!code-csharp[](sample/SampleApp/Program.cs?name=snippet_WebHostDefaults)]
```

<span data-ttu-id="db8ff-265">可放心忽略（或删除）代码两侧的 `#region` 和 `#endregion` 指令。</span><span class="sxs-lookup"><span data-stu-id="db8ff-265">You may safely ignore (or remove) the `#region` and `#endregion` directives that surround the code.</span></span> <span data-ttu-id="db8ff-266">如果计划运行主题中所述的示例方案，请不要更改这些指令中的代码。</span><span class="sxs-lookup"><span data-stu-id="db8ff-266">Don't alter the code within these directives if you plan to run the sample scenarios described in the topic.</span></span> <span data-ttu-id="db8ff-267">试用其他方案时，可随时更改代码。</span><span class="sxs-lookup"><span data-stu-id="db8ff-267">Feel free to alter the code when experimenting with other scenarios.</span></span>

<span data-ttu-id="db8ff-268">有关详细信息，请参阅[参与 ASP.NET 文档：代码片段](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets)。</span><span class="sxs-lookup"><span data-stu-id="db8ff-268">For more information, see [Contribute to the ASP.NET documentation: Code snippets](https://github.com/dotnet/AspNetCore.Docs/blob/master/CONTRIBUTING.md#code-snippets).</span></span>

## <a name="next-steps"></a><span data-ttu-id="db8ff-269">后续步骤</span><span class="sxs-lookup"><span data-stu-id="db8ff-269">Next steps</span></span>

<span data-ttu-id="db8ff-270">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="db8ff-270">For more information, see the following resources:</span></span>

* <xref:getting-started>
* <xref:tutorials/publish-to-azure-webapp-using-vs>
* [<span data-ttu-id="db8ff-271">ASP.NET Core 基础知识</span><span class="sxs-lookup"><span data-stu-id="db8ff-271">ASP.NET Core fundamentals</span></span>](xref:fundamentals/index)
* <span data-ttu-id="db8ff-272">[每周 ASP.NET Community Standup](https://live.asp.net/) 介绍了团队的工作进度和计划。</span><span class="sxs-lookup"><span data-stu-id="db8ff-272">[The weekly ASP.NET community standup](https://live.asp.net/) covers the team's progress and plans.</span></span> <span data-ttu-id="db8ff-273">它以新博客和第三方软件为重点。</span><span class="sxs-lookup"><span data-stu-id="db8ff-273">It features new blogs and third-party software.</span></span>
