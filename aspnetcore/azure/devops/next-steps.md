---
title: 后续步骤-使用 ASP.NET Core 和 Azure 进行开发运营
author: CamSoper
description: 更多的 ASP.NET Core 和 Azure 中的 DevOps 学习路径。
ms.author: casoper
ms.custom: mvc, seodec18
ms.date: 10/24/2018
uid: azure/devops/next-steps
ms.openlocfilehash: a775dc42551a43bcce72b5f9ca364ed00b1dc4e6
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892004"
---
# <a name="next-steps"></a><span data-ttu-id="211fb-103">后续步骤</span><span class="sxs-lookup"><span data-stu-id="211fb-103">Next steps</span></span>

<span data-ttu-id="211fb-104">在本指南中，您将创建 ASP.NET Core 示例应用的 DevOps 管道。</span><span class="sxs-lookup"><span data-stu-id="211fb-104">In this guide, you created a DevOps pipeline for an ASP.NET Core sample app.</span></span> <span data-ttu-id="211fb-105">祝贺你！</span><span class="sxs-lookup"><span data-stu-id="211fb-105">Congratulations!</span></span> <span data-ttu-id="211fb-106">我们希望您喜欢学习来将 ASP.NET Core web 应用发布到 Azure 应用服务，并自动执行更改的持续集成。</span><span class="sxs-lookup"><span data-stu-id="211fb-106">We hope you enjoyed learning to publish ASP.NET Core web apps to Azure App Service and automate the continuous integration of changes.</span></span>

<span data-ttu-id="211fb-107">Web 宿主和 DevOps，超出 Azure 有广泛的平台-作为-服务 (PaaS) 服务适用于 ASP.NET Core 开发人员。</span><span class="sxs-lookup"><span data-stu-id="211fb-107">Beyond web hosting and DevOps, Azure has a wide array of Platform-as-a-Service (PaaS) services useful to ASP.NET Core developers.</span></span> <span data-ttu-id="211fb-108">本部分提供的一些最常使用的服务的简要概述。</span><span class="sxs-lookup"><span data-stu-id="211fb-108">This section gives a brief overview of some of the most commonly used services.</span></span>

## <a name="storage-and-databases"></a><span data-ttu-id="211fb-109">存储和数据库</span><span class="sxs-lookup"><span data-stu-id="211fb-109">Storage and databases</span></span>

<span data-ttu-id="211fb-110">[Redis 缓存](/azure/redis-cache/)是缓存可作为服务的高吞吐量、 低延迟数据。</span><span class="sxs-lookup"><span data-stu-id="211fb-110">[Redis Cache](/azure/redis-cache/) is high-throughput, low-latency data caching available as a service.</span></span> <span data-ttu-id="211fb-111">它可以用于缓存页面输出、 减少数据库请求和 ASP.NET Core 会话状态提供应用的多个实例。</span><span class="sxs-lookup"><span data-stu-id="211fb-111">It can be used for caching page output, reducing database requests, and providing ASP.NET Core session state across multiple instances of an app.</span></span>

<span data-ttu-id="211fb-112">[Azure 存储](/azure/storage/)是 Azure 的大规模可缩放的云存储。</span><span class="sxs-lookup"><span data-stu-id="211fb-112">[Azure Storage](/azure/storage/) is Azure's massively scalable cloud storage.</span></span> <span data-ttu-id="211fb-113">开发人员可以利用[队列存储](/azure/storage/queues/storage-queues-introduction)可靠的消息队列，并[表存储](/azure/storage/tables/table-storage-overview)是设计用于快速开发使用大规模、 半结构化数据集的 NoSQL 键-值存储。</span><span class="sxs-lookup"><span data-stu-id="211fb-113">Developers can take advantage of [Queue Storage](/azure/storage/queues/storage-queues-introduction) for reliable message queuing, and [Table Storage](/azure/storage/tables/table-storage-overview) is a NoSQL key-value store designed for rapid development using massive, semi-structured data sets.</span></span>

<span data-ttu-id="211fb-114">[Azure SQL 数据库](/azure/sql-database/)提供的熟悉的关系数据库功能与使用 Microsoft SQL Server 引擎的服务。</span><span class="sxs-lookup"><span data-stu-id="211fb-114">[Azure SQL Database](/azure/sql-database/) provides familiar relational database functionality as a service using the Microsoft SQL Server Engine.</span></span>

<span data-ttu-id="211fb-115">[Cosmos DB](/azure/cosmos-db/)全球分布的多模型 NoSQL 数据库服务。</span><span class="sxs-lookup"><span data-stu-id="211fb-115">[Cosmos DB](/azure/cosmos-db/) globally distributed, multi-model NoSQL database service.</span></span> <span data-ttu-id="211fb-116">多个 Api 都可用，包括 SQL API （以前称为 DocumentDB）、 Cassandra 和 MongoDB。</span><span class="sxs-lookup"><span data-stu-id="211fb-116">Multiple APIs are available, including SQL API (formerly called DocumentDB), Cassandra, and MongoDB.</span></span>

## <a name="identity"></a><span data-ttu-id="211fb-117">标识</span><span class="sxs-lookup"><span data-stu-id="211fb-117">Identity</span></span>

<span data-ttu-id="211fb-118">[Azure Active Directory](/azure/active-directory/)并[Azure Active Directory B2C](/azure/active-directory-b2c/)是这两个标识服务。</span><span class="sxs-lookup"><span data-stu-id="211fb-118">[Azure Active Directory](/azure/active-directory/) and [Azure Active Directory B2C](/azure/active-directory-b2c/) are both identity services.</span></span> <span data-ttu-id="211fb-119">Azure Active Directory 专为企业方案和时在 Azure Active Directory B2C 是预期的业务客户方案，包括社交网络单一登录启用 Azure AD B2B （企业到企业） 协作。</span><span class="sxs-lookup"><span data-stu-id="211fb-119">Azure Active Directory is designed for enterprise scenarios and enables Azure AD B2B (business-to-business) collaboration, while Azure Active Directory B2C is intended business-to-customer scenarios, including social network sign-in.</span></span>

## <a name="mobile"></a><span data-ttu-id="211fb-120">移动电话</span><span class="sxs-lookup"><span data-stu-id="211fb-120">Mobile</span></span>

<span data-ttu-id="211fb-121">[通知中心](/azure/notification-hubs/)是一种多平台且可扩展的推送通知引擎快速发送数百万条消息到各种类型的设备上运行的应用。</span><span class="sxs-lookup"><span data-stu-id="211fb-121">[Notification Hubs](/azure/notification-hubs/) is a multi-platform, scalable push-notification engine to quickly send millions of messages to apps running on various types of devices.</span></span>

## <a name="web-infrastructure"></a><span data-ttu-id="211fb-122">Web 基础结构</span><span class="sxs-lookup"><span data-stu-id="211fb-122">Web infrastructure</span></span>

<span data-ttu-id="211fb-123">[Azure 容器服务](/azure/aks/)管理托管的 Kubernetes 环境，使它快速、 轻松地部署和管理容器化的应用而无需容器业务流程专业知识。</span><span class="sxs-lookup"><span data-stu-id="211fb-123">[Azure Container Service](/azure/aks/) manages your hosted Kubernetes environment, making it quick and easy to deploy and manage containerized apps without container orchestration expertise.</span></span>

<span data-ttu-id="211fb-124">[Azure 搜索](/azure/search/)用于基于专用异类内容创建一种企业搜索解决方案。</span><span class="sxs-lookup"><span data-stu-id="211fb-124">[Azure Search](/azure/search/) is used to create an enterprise search solution over private, heterogenous content.</span></span>

<span data-ttu-id="211fb-125">[Service Fabric](/azure/service-fabric/)是一个分布式的系统平台，轻松地打包、 部署和管理可缩放和可靠的微服务和容器。</span><span class="sxs-lookup"><span data-stu-id="211fb-125">[Service Fabric](/azure/service-fabric/) is a distributed systems platform that makes it easy to package, deploy, and manage scalable and reliable microservices and containers.</span></span>
