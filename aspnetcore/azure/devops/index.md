---
title: 通过 ASP.NET Core 和 Azure 实现 DevOps
author: CamSoper
description: 提供有关为托管在 Azure 中的 ASP.NET Core 应用构建 DevOps 管道的端到端指导的指南。
ms.author: casoper
ms.date: 08/07/2018
ms.custom: devx-track-csharp, mvc, seodec18
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
uid: azure/devops/index
ms.openlocfilehash: efabfd7fdcd695186207654887292c6b2104342d
ms.sourcegitcommit: ba4872dd5a93780fe6cfacb2711ec1e69e0df92c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88130179"
---
# <a name="devops-with-aspnet-core-and-azure"></a><span data-ttu-id="f11d2-103">通过 ASP.NET Core 和 Azure 实现 DevOps</span><span class="sxs-lookup"><span data-stu-id="f11d2-103">DevOps with ASP.NET Core and Azure</span></span>

<span data-ttu-id="f11d2-104">[![封面图像](./media/cover-large.png)](https://aka.ms/devopsbook)</span><span class="sxs-lookup"><span data-stu-id="f11d2-104">[![Cover Image](./media/cover-large.png)](https://aka.ms/devopsbook)</span></span>

<span data-ttu-id="f11d2-105">作者：[Cam Soper](https://twitter.com/camsoper)[Scott Addie](https://twitter.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="f11d2-105">By [Cam Soper](https://twitter.com/camsoper) and [Scott Addie](https://twitter.com/scottaddie)</span></span>

<span data-ttu-id="f11d2-106">本指南以[可下载 PDF 电子书](https://aka.ms/devopsbook)的形式提供。</span><span class="sxs-lookup"><span data-stu-id="f11d2-106">This guide is available as a [downloadable PDF e-book](https://aka.ms/devopsbook).</span></span>

## <a name="welcome"></a><span data-ttu-id="f11d2-107">欢迎使用</span><span class="sxs-lookup"><span data-stu-id="f11d2-107">Welcome</span></span> 

<span data-ttu-id="f11d2-108">欢迎使用适用于 .NET 的 Azure 开发生命周期指南！</span><span class="sxs-lookup"><span data-stu-id="f11d2-108">Welcome to the Azure Development Lifecycle guide for .NET!</span></span> <span data-ttu-id="f11d2-109">本指南介绍使用 .NET 工具和进程围绕 Azure 构建开发生命周期的基本概念。</span><span class="sxs-lookup"><span data-stu-id="f11d2-109">This guide introduces the basic concepts of building a development lifecycle around Azure using .NET tools and processes.</span></span> <span data-ttu-id="f11d2-110">读完本指南后，可以获得成熟 DevOps 工具链的优势。</span><span class="sxs-lookup"><span data-stu-id="f11d2-110">After finishing this guide, you'll reap the benefits of a mature DevOps toolchain.</span></span>

## <a name="who-this-guide-is-for"></a><span data-ttu-id="f11d2-111">本指南适用对象</span><span class="sxs-lookup"><span data-stu-id="f11d2-111">Who this guide is for</span></span>

<span data-ttu-id="f11d2-112">读者应是有经验的 ASP.NET Core 开发人员（200-300 级别）。</span><span class="sxs-lookup"><span data-stu-id="f11d2-112">You should be an experienced ASP.NET Core developer (200-300 level).</span></span> <span data-ttu-id="f11d2-113">无须了解有关 Azure 的任何内容，因为本指南中将进行介绍。</span><span class="sxs-lookup"><span data-stu-id="f11d2-113">You don't need to know anything about Azure, as we'll cover that in this introduction.</span></span> <span data-ttu-id="f11d2-114">本指南对于更侧重于操作（而不是开发）的 DevOps 工程师可能也很有用。</span><span class="sxs-lookup"><span data-stu-id="f11d2-114">This guide may also be useful for DevOps engineers who are more focused on operations than development.</span></span>

<span data-ttu-id="f11d2-115">本指南面向 Windows 开发人员。</span><span class="sxs-lookup"><span data-stu-id="f11d2-115">This guide targets Windows developers.</span></span> <span data-ttu-id="f11d2-116">但是，.NET Core 完全支持 Linux 和 macOS。</span><span class="sxs-lookup"><span data-stu-id="f11d2-116">However, Linux and macOS are fully supported by .NET Core.</span></span> <span data-ttu-id="f11d2-117">若要针对 Linux/macOS 调整本指南，请注意有关 Linux/macOS 差异的标注。</span><span class="sxs-lookup"><span data-stu-id="f11d2-117">To adapt this guide for Linux/macOS, watch for callouts for Linux/macOS differences.</span></span>

## <a name="what-this-guide-doesnt-cover"></a><span data-ttu-id="f11d2-118">本指南未涵盖的内容</span><span class="sxs-lookup"><span data-stu-id="f11d2-118">What this guide doesn't cover</span></span>

<span data-ttu-id="f11d2-119">本指南专注于 .NET 开发人员的端到端持续部署体验。</span><span class="sxs-lookup"><span data-stu-id="f11d2-119">This guide is focused on an end-to-end continuous deployment experience for .NET developers.</span></span> <span data-ttu-id="f11d2-120">这不是介绍 Azure 中所有内容的全面指南，并不特别着重于适用于 Azure 服务的 .NET API。</span><span class="sxs-lookup"><span data-stu-id="f11d2-120">It's not an exhaustive guide to all things Azure, and it doesn't focus extensively on .NET APIs for Azure services.</span></span> <span data-ttu-id="f11d2-121">介绍的重点在于持续集成、部署、监视和调试。</span><span class="sxs-lookup"><span data-stu-id="f11d2-121">The emphasis is all around continuous integration, deployment, monitoring, and debugging.</span></span> <span data-ttu-id="f11d2-122">本指南结尾附近提供了后续步骤建议。</span><span class="sxs-lookup"><span data-stu-id="f11d2-122">Near the end of the guide, recommendations for next steps are offered.</span></span> <span data-ttu-id="f11d2-123">建议包括对 ASP.NET Core 开发人员有用的 Azure 平台服务。</span><span class="sxs-lookup"><span data-stu-id="f11d2-123">Included in the suggestions are Azure platform services that are useful to ASP.NET Core developers.</span></span>

## <a name="whats-in-this-guide"></a><span data-ttu-id="f11d2-124">本指南内容</span><span class="sxs-lookup"><span data-stu-id="f11d2-124">What's in this guide</span></span>

### <a name="tools-and-downloads"></a>[<span data-ttu-id="f11d2-125">工具和下载</span><span class="sxs-lookup"><span data-stu-id="f11d2-125">Tools and downloads</span></span>](xref:azure/devops/tools-and-downloads)

<span data-ttu-id="f11d2-126">了解在何处获取本指南中使用的工具。</span><span class="sxs-lookup"><span data-stu-id="f11d2-126">Learn where to acquire the tools used in this guide.</span></span>

### <a name="deploy-to-app-service"></a>[<span data-ttu-id="f11d2-127">部署到应用服务</span><span class="sxs-lookup"><span data-stu-id="f11d2-127">Deploy to App Service</span></span>](xref:azure/devops/deploy-to-app-service)

<span data-ttu-id="f11d2-128">了解将 ASP.NET Core 应用部署到 Azure 应用服务的各种方法。</span><span class="sxs-lookup"><span data-stu-id="f11d2-128">Learn the various methods for deploying an ASP.NET Core app to Azure App Service.</span></span>

### <a name="continuous-integration-and-deployment"></a>[<span data-ttu-id="f11d2-129">持续集成和部署</span><span class="sxs-lookup"><span data-stu-id="f11d2-129">Continuous integration and deployment</span></span>](xref:azure/devops/cicd)

<span data-ttu-id="f11d2-130">通过 GitHub、Azure DevOps Services 和 Azure，为 ASP.NET Core 应用生成端到端持续集成和部署解决方案。</span><span class="sxs-lookup"><span data-stu-id="f11d2-130">Build an end-to-end continuous integration and deployment solution for your ASP.NET Core app with GitHub, Azure DevOps Services, and Azure.</span></span>

### <a name="monitor-and-debug"></a>[<span data-ttu-id="f11d2-131">监视和调试</span><span class="sxs-lookup"><span data-stu-id="f11d2-131">Monitor and debug</span></span>](xref:azure/devops/monitor)

<span data-ttu-id="f11d2-132">使用 Azure 工具监视、故障排除和优化应用程序。</span><span class="sxs-lookup"><span data-stu-id="f11d2-132">Use Azure's tools to monitor, troubleshoot, and tune your application.</span></span>

### <a name="next-steps"></a>[<span data-ttu-id="f11d2-133">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f11d2-133">Next steps</span></span>](xref:azure/devops/next-steps)

<span data-ttu-id="f11d2-134">ASP.NET Core 开发人员学习 Azure 的其他学习路径。</span><span class="sxs-lookup"><span data-stu-id="f11d2-134">Other learning paths for the ASP.NET Core developer learning Azure.</span></span>

## <a name="additional-introductory-reading"></a><span data-ttu-id="f11d2-135">其他介绍性读物</span><span class="sxs-lookup"><span data-stu-id="f11d2-135">Additional introductory reading</span></span>

<span data-ttu-id="f11d2-136">如果这是你首次接触云计算，这些文章介绍了基础知识。</span><span class="sxs-lookup"><span data-stu-id="f11d2-136">If this is your first exposure to cloud computing, these articles explain the basics.</span></span>

* [<span data-ttu-id="f11d2-137">什么是云计算？</span><span class="sxs-lookup"><span data-stu-id="f11d2-137">What is Cloud Computing?</span></span>](https://azure.microsoft.com/overview/what-is-cloud-computing/)
* [<span data-ttu-id="f11d2-138">云计算示例</span><span class="sxs-lookup"><span data-stu-id="f11d2-138">Examples of Cloud Computing</span></span>](https://azure.microsoft.com/overview/examples-of-cloud-computing/)
* [<span data-ttu-id="f11d2-139">什么是 IaaS？</span><span class="sxs-lookup"><span data-stu-id="f11d2-139">What is IaaS?</span></span>](https://azure.microsoft.com/overview/what-is-iaas/)
* [<span data-ttu-id="f11d2-140">什么是 PaaS？</span><span class="sxs-lookup"><span data-stu-id="f11d2-140">What is PaaS?</span></span>](https://azure.microsoft.com/overview/what-is-paas/)
