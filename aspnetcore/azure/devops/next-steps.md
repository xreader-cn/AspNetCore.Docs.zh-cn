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
# <a name="next-steps"></a>后续步骤

本指南中，你为 ASP.NET Core 示例应用创建了 DevOps 管道。 祝贺你！ 希望你喜欢学习如何将 ASP.NET Core web 应用发布到 Azure 应用服务并自动执行更改的持续集成的过程。

除了 web 主机和 DevOps，Azure 还提供了一系列可以帮助 ASP.NET Core 开发人员的平台即服务 (PaaS) 服务。 本部分简要概述了一些最常用的服务。

## <a name="storage-and-databases"></a>存储和数据库

[Redis Cache](/azure/redis-cache/) 是​​可作为服务使用的高吞吐量且低延迟的数据缓存。 它可用于缓存页面输出、减少数据库请求以及跨应用多个实例提供 ASP.NET Core 会话状态。

[Azure 存储](/azure/storage/) 是 Azure 的高度可缩放的云存储。 开发人员可以利用[队列存储](/azure/storage/queues/storage-queues-introduction)来实现可靠的消息队列，[表存储](/azure/storage/tables/table-storage-overview)是一个 NoSQL 键值存储，旨在使用大规模半结构化数据集进行快速开发。

[Azure SQL 数据库](/azure/sql-database/)使用 Microsoft SQL Server 引擎以服务形式提供熟悉的关系数据库功能。

[Cosmos DB](/azure/cosmos-db/) 是一种全球分布式多模型 NoSQL 数据库服务。 有多个 API 可用，包括 SQL API（以前称为 DocumentDB）、Cassandra 和 MongoDB。

## <a name="identity"></a>标识

[Azure Active Directory](/azure/active-directory/) 和 [Azure Active Directory B2C](/azure/active-directory-b2c/) 均为标识服务。 Azure Active Directory 为企业场景而设计，可实现 Azure AD B2B（企业到企业）协作，而 Azure Active Directory B2C 用于企业到客户场景，包括社交网络登录。

## <a name="mobile"></a>移动电话

[通知中心](/azure/notification-hubs/)是一种多平台、可缩放的推送通知引擎，用于向在各类设备上运行的应用快速发送数百万条消息。

## <a name="web-infrastructure"></a>Web 基础结构

[Azure 容器服务](/azure/aks/)管理托管的 Kubernetes 环境，使用户无需具备容器业务流程专业知识即可快速、轻松地部署和管理容器化的应用。

[Azure 搜索](/azure/search/)用于创建基于专用异类内容的企业搜索解决方案。

[Service Fabric](/azure/service-fabric/) 是一款分布式系统平台，可方便用户轻松打包、部署和管理可缩放的可靠微服务和容器。
