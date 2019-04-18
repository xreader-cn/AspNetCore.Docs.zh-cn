---
title: 在 Docker 容器中托管 ASP.NET Core
author: rick-anderson
description: 了解指向如何在 Docker 容器中托管 ASP.NET Core 应用的相关资源的链接。
ms.author: riande
ms.custom: mvc
ms.date: 01/08/2018
uid: host-and-deploy/docker/index
ms.openlocfilehash: e56f90ec7272ce0411651ee6f8e7c754ae44b78d
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59516256"
---
# <a name="host-aspnet-core-in-docker-containers"></a><span data-ttu-id="a00a9-103">在 Docker 容器中托管 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="a00a9-103">Host ASP.NET Core in Docker containers</span></span>

<span data-ttu-id="a00a9-104">下面的文章可用于了解如何在 Docker 中托管 ASP.NET Core 应用：</span><span class="sxs-lookup"><span data-stu-id="a00a9-104">The following articles are available for learning about hosting ASP.NET Core apps in Docker:</span></span>

[<span data-ttu-id="a00a9-105">容器和 Docker 简介</span><span class="sxs-lookup"><span data-stu-id="a00a9-105">Introduction to Containers and Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/index)  
<span data-ttu-id="a00a9-106">容器化是软件开发的一种方法，通过该方法可将应用程序或服务、其依赖项及其配置一起打包为容器映像。了解相关内容。</span><span class="sxs-lookup"><span data-stu-id="a00a9-106">See how containerization is an approach to software development in which an application or service, its dependencies, and its configuration are packaged together as a container image.</span></span> <span data-ttu-id="a00a9-107">可对该映像进行测试，然后将其部署到主机。</span><span class="sxs-lookup"><span data-stu-id="a00a9-107">The image can be tested and then deployed to a host.</span></span>

[<span data-ttu-id="a00a9-108">什么是 Docker</span><span class="sxs-lookup"><span data-stu-id="a00a9-108">What is Docker</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-defined)  
<span data-ttu-id="a00a9-109">了解如何将 Docker 作为一种开源项目，用于将应用自动部署为可在云或本地运行的便携式独立容器。</span><span class="sxs-lookup"><span data-stu-id="a00a9-109">Discover how Docker is an open-source project for automating the deployment of apps as portable, self-sufficient containers that can run on the cloud or on-premises.</span></span>

[<span data-ttu-id="a00a9-110">Docker 术语</span><span class="sxs-lookup"><span data-stu-id="a00a9-110">Docker Terminology</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-terminology)  
<span data-ttu-id="a00a9-111">了解 Docker 技术的术语和定义。</span><span class="sxs-lookup"><span data-stu-id="a00a9-111">Learn terms and definitions for Docker technology.</span></span>

[<span data-ttu-id="a00a9-112">Docker 容器、映像和注册表</span><span class="sxs-lookup"><span data-stu-id="a00a9-112">Docker containers, images, and registries</span></span>](/dotnet/standard/microservices-architecture/container-docker-introduction/docker-containers-images-registries)  
<span data-ttu-id="a00a9-113">了解如何将 Docker 容器映像存储在映像注册表中，以实现跨环境的一致部署。</span><span class="sxs-lookup"><span data-stu-id="a00a9-113">Find out how Docker container images are stored in an image registry for consistent deployment across environments.</span></span>

[<span data-ttu-id="a00a9-114">Visual Studio Tools for Docker</span><span class="sxs-lookup"><span data-stu-id="a00a9-114">Visual Studio Tools for Docker</span></span>](xref:host-and-deploy/docker/visual-studio-tools-for-docker)  
<span data-ttu-id="a00a9-115">Visual Studio 2017 支持在用于 Windows 的 Docker 上生成、调试和运行面向 .NET Framework 或 .NET Core 的 ASP.NET Core 应用。了解相关内容。</span><span class="sxs-lookup"><span data-stu-id="a00a9-115">Discover how Visual Studio 2017 supports building, debugging, and running ASP.NET Core apps targeting either .NET Framework or .NET Core on Docker for Windows.</span></span> <span data-ttu-id="a00a9-116">Windows 和 Linux 容器均受支持。</span><span class="sxs-lookup"><span data-stu-id="a00a9-116">Both Windows and Linux containers are supported.</span></span>

[<span data-ttu-id="a00a9-117">发布到 Docker 映像</span><span class="sxs-lookup"><span data-stu-id="a00a9-117">Publish to a Docker Image</span></span>](/azure/vs-azure-tools-docker-hosting-web-apps-in-docker)  
<span data-ttu-id="a00a9-118">了解如何通过 Visual Studio Tools for Docker 扩展使用 PowerShell 将 ASP.NET Core 应用部署到 Azure 上的 Docker 主机。</span><span class="sxs-lookup"><span data-stu-id="a00a9-118">Find out how to use the Visual Studio Tools for Docker extension to deploy an ASP.NET Core app to a Docker host on Azure using PowerShell.</span></span>

[<span data-ttu-id="a00a9-119">配置 ASP.NET Core 以使用代理服务器和负载均衡器</span><span class="sxs-lookup"><span data-stu-id="a00a9-119">Configure ASP.NET Core to work with proxy servers and load balancers</span></span>](xref:host-and-deploy/proxy-load-balancer)  
<span data-ttu-id="a00a9-120">对于托管在代理服务器和负载均衡器后方的应用，可能需要附加配置。</span><span class="sxs-lookup"><span data-stu-id="a00a9-120">Additional configuration might be required for apps hosted behind proxy servers and load balancers.</span></span> <span data-ttu-id="a00a9-121">通过代理传递的请求通常会遮盖初始请求相关信息，例如方案和客户端 IP。</span><span class="sxs-lookup"><span data-stu-id="a00a9-121">Passing requests through a proxy often obscures information about the original request, such as the scheme and client IP.</span></span> <span data-ttu-id="a00a9-122">可能必须将请求相关的一些信息手动转发给应用。</span><span class="sxs-lookup"><span data-stu-id="a00a9-122">It might be necessary to forwarded some information about the request manually to the app.</span></span>
