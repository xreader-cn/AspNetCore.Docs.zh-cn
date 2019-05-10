---
title: 在 ASP.NET Core 中授权简介
author: rick-anderson
description: 了解授权以及授权 ASP.NET Core 应用中的工作方式的基础知识。
ms.author: riande
ms.date: 10/14/2016
uid: security/authorization/introduction
ms.openlocfilehash: 5465eb7875ebecd77b628376ef886db0ddd05025
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64896974"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a>在 ASP.NET Core 中授权简介

<a name="security-authorization-introduction"></a>

授权是指确定何种操作的进程的用户便可执行操作。 例如，允许管理用户创建文档库、 将文档添加、 编辑文档，并将其删除。 使用库的非管理用户仅有权读取文档。

授权是正交和独立于身份验证。 但是，授权要求的身份验证机制。 身份验证是认定用户是谁的过程。 身份验证可能会创建一个或多个标识当前用户。

## <a name="authorization-types"></a>授权类型

ASP.NET Core 授权提供一个简单、 声明性[角色](xref:security/authorization/roles)以及丰富[基于策略的](xref:security/authorization/policies)模型。 授权要求，以表示和处理程序评估针对要求的用户的声明。 命令性检查可以基于简单的策略或策略求值的用户标识和该用户尝试访问资源的属性。

## <a name="namespaces"></a>命名空间

授权组件，包括`AuthorizeAttribute`并`AllowAnonymousAttribute`在找不到属性，`Microsoft.AspNetCore.Authorization`命名空间。

请参阅上的文档[简单授权](xref:security/authorization/simple)。
