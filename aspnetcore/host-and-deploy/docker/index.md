---
title: 在 Docker 容器中托管 ASP.NET Core
author: rick-anderson
description: 了解指向如何在 Docker 容器中托管 ASP.NET Core 应用的相关资源的链接。
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
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
uid: host-and-deploy/docker/index
ms.openlocfilehash: 6b4b011314be2481e6e71d7782fff6ee99cedb9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93059775"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="03692-103">在 Docker 容器中托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="03692-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="03692-104">下面的文章可用于了解如何在 Docker 中托管 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="03692-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="03692-105">容器和 Docker 简介</span><span class="sxs-lookup"><span data-stu-id="03692-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="03692-106">容器化是软件开发的一种方法，通过该方法可将应用程序或服务、其依赖项及其配置一起打包为容器映像。了解相关内容。</span><span class="sxs-lookup"><span data-stu-id="03692-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="03692-107">可对该映像进行测试，然后将其部署到主机。</span><span class="sxs-lookup"><span data-stu-id="03692-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="03692-108">什么是 Docker</span><span class="sxs-lookup"><span data-stu-id="03692-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="03692-109">了解如何将 Docker 作为一种开源项目，用于将应用自动部署为可在云或本地运行的便携式独立容器。</span><span class="sxs-lookup"><span data-stu-id="03692-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="03692-110">Docker 术语</span><span class="sxs-lookup"><span data-stu-id="03692-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="03692-111">了解 Docker 技术的术语和定义。</span><span class="sxs-lookup"><span data-stu-id="03692-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="03692-112">Docker 容器、映像和注册表</span><span class="sxs-lookup"><span data-stu-id="03692-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="03692-113">了解如何将 Docker 容器映像存储在映像注册表中，以实现跨环境的一致部署。</span><span class="sxs-lookup"><span data-stu-id="03692-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

<span data-ttu-id="03692-114"><xref:host-and-deploy/docker/building-net-docker-images> 了解如何生成和 Docker 化 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="03692-114"><xref:host-and-deploy/docker/building-net-docker-images> Learn how to build and dockerize an ASP.NET Core app.</span></span> <span data-ttu-id="03692-115">了解由 Microsoft 维护的 Docker 映像并检查用例。</span><span class="sxs-lookup"><span data-stu-id="03692-115">Explore Docker images maintained by Microsoft and examine use cases.</span></span>

[<span data-ttu-id="03692-116">Visual Studio 容器工具</span><span class="sxs-lookup"><span data-stu-id="03692-116">Visual Studio Container Tools</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="03692-117">了解 Visual Studio 如何支持在用于 Windows 的 Docker 上生成、调试和运行面向 .NET Framework 或 .NET Core 的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="03692-117">Discover how Visual Studio supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="03692-118">Windows 和 Linux 容器均受支持。</span><span class="sxs-lookup"><span data-stu-id="03692-118">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="03692-119">发布到 Azure 容器注册表</span><span class="sxs-lookup"><span data-stu-id="03692-119">Publish to Azure Container Registry</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="03692-120">了解如何通过 Visual Studio 容器工具扩展使用 PowerShell 将 ASP.NET Core 应用部署到 Azure 上的 Docker 主机。</span><span class="sxs-lookup"><span data-stu-id="03692-120">Find out how to use the Visual Studio Container Tools extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="03692-121">配置 ASP.NET Core 以使用代理服务器和负载均衡器</span><span class="sxs-lookup"><span data-stu-id="03692-121">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="03692-122">对于托管在代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="03692-122">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="03692-123">通过代理传递的请求通常会遮盖初始请求相关信息，例如方案和客户端 IP。</span><span class="sxs-lookup"><span data-stu-id="03692-123">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="03692-124">可能必须将请求相关的一些信息手动转发给应用。</span><span class="sxs-lookup"><span data-stu-id="03692-124">It might be necessary to forwarded some information about the request manually to the app.</span></span>

<span data-ttu-id="03692-125">[使用 Docker 和小型容器的 GC](xref:performance/memory#sc) 讨论了包含小型容器的 GC 选择。</span><span class="sxs-lookup"><span data-stu-id="03692-125">[GC using Docker and small containers](xref:performance/memory#sc) Discusses GC selection with small containers.</span></span>