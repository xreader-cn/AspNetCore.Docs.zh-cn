---
title: 后续步骤 - 通过 ASP.NET Core 和 Azure 实现 DevOps
author: CamSoper
description: 通过 ASP.NET Core 和 Azure 实现 DevOps 的其他学习路径。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647430"
---
# <a name="next-steps"></a><span data-ttu-id="c28f2-103">后续步骤</span><span class="sxs-lookup"><span data-stu-id="c28f2-103">Next steps</span></span>

<span data-ttu-id="c28f2-104">本指南中，你为 ASP.NET Core 示例应用创建了 DevOps 管道。</span><span class="sxs-lookup"><span data-stu-id="c28f2-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="c28f2-105">祝贺你！</span><span class="sxs-lookup"><span data-stu-id="c28f2-105">Congratulations!</span></span> <span data-ttu-id="c28f2-106">希望你喜欢学习如何将 ASP.NET Core web 应用发布到 Azure 应用服务并自动执行更改的持续集成的过程。</span><span class="sxs-lookup"><span data-stu-id="c28f2-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="c28f2-107">除了 web 主机和 DevOps，Azure 还提供了一系列可以帮助 ASP.NET Core 开发人员的平台即服务 (PaaS) 服务。</span><span class="sxs-lookup"><span data-stu-id="c28f2-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="c28f2-108">本部分简要概述了一些最常用的服务。</span><span class="sxs-lookup"><span data-stu-id="c28f2-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="c28f2-109">存储和数据库</span><span class="sxs-lookup"><span data-stu-id="c28f2-109">Storage and databases</span></span>

<span data-ttu-id="c28f2-110">[Redis Cache](/azure/redis-cache/) 是​​可作为服务使用的高吞吐量且低延迟的数据缓存。</span><span class="sxs-lookup"><span data-stu-id="c28f2-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="c28f2-111">它可用于缓存页面输出、减少数据库请求以及跨应用多个实例提供 ASP.NET Core 会话状态。</span><span class="sxs-lookup"><span data-stu-id="c28f2-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="c28f2-112">[Azure 存储](/azure/storage/) 是 Azure 的高度可缩放的云存储。</span><span class="sxs-lookup"><span data-stu-id="c28f2-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="c28f2-113">开发人员可以利用[队列存储](/azure/storage/queues/storage-queues-introduction)来实现可靠的消息队列，[表存储](/azure/storage/tables/table-storage-overview)是一个 NoSQL 键值存储，旨在使用大规模半结构化数据集进行快速开发。</span><span class="sxs-lookup"><span data-stu-id="c28f2-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="c28f2-114">[Azure SQL 数据库](/azure/sql-database/)使用 Microsoft SQL Server 引擎以服务形式提供熟悉的关系数据库功能。</span><span class="sxs-lookup"><span data-stu-id="c28f2-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="c28f2-115">[Cosmos DB](/azure/cosmos-db/) 是一种全球分布式多模型 NoSQL 数据库服务。</span><span class="sxs-lookup"><span data-stu-id="c28f2-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="c28f2-116">有多个 API 可用，包括 SQL API（以前称为 DocumentDB）、Cassandra 和 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="c28f2-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## <a name="identity"></a><span data-ttu-id="c28f2-117">标识</span><span class="sxs-lookup"><span data-stu-id="c28f2-117">Identity</span></span>

<span data-ttu-id="c28f2-118">[Azure Active Directory](/azure/active-directory/) 和 [Azure Active Directory B2C](/azure/active-directory-b2c/) 均为标识服务。</span><span class="sxs-lookup"><span data-stu-id="c28f2-118">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="c28f2-119">Azure Active Directory 为企业场景而设计，可实现 Azure AD B2B（企业到企业）协作，而 Azure Active Directory B2C 用于企业到客户场景，包括社交网络登录。</span><span class="sxs-lookup"><span data-stu-id="c28f2-119">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="c28f2-120">移动电话</span><span class="sxs-lookup"><span data-stu-id="c28f2-120">Mobile</span></span>

<span data-ttu-id="c28f2-121">[通知中心](/azure/notification-hubs/)是一种多平台、可缩放的推送通知引擎，用于向在各类设备上运行的应用快速发送数百万条消息。</span><span class="sxs-lookup"><span data-stu-id="c28f2-121">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="c28f2-122">Web 基础结构</span><span class="sxs-lookup"><span data-stu-id="c28f2-122">Web infrastructure</span></span>

<span data-ttu-id="c28f2-123">[Azure 容器服务](/azure/aks/)管理托管的 Kubernetes 环境，使用户无需具备容器业务流程专业知识即可快速、轻松地部署和管理容器化的应用。</span><span class="sxs-lookup"><span data-stu-id="c28f2-123">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="c28f2-124">[Azure 搜索](/azure/search/)用于创建基于专用异类内容的企业搜索解决方案。</span><span class="sxs-lookup"><span data-stu-id="c28f2-124">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="c28f2-125">[Service Fabric](/azure/service-fabric/) 是一款分布式系统平台，可方便用户轻松打包、部署和管理可缩放的可靠微服务和容器。</span><span class="sxs-lookup"><span data-stu-id="c28f2-125">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
