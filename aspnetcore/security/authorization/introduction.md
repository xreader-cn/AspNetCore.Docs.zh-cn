---
title: ASP.NET Core 中的授权简介
author: rick-anderson
description: 了解授权基础知识和授权在 ASP.NET Core 应用中的工作原理。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/introduction
ms.openlocfilehash: 241ef8b00e9dcbd1983d32edcd9c1db2eaa5c687
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777522"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="8a017-103">ASP.NET Core 中的授权简介</span><span class="sxs-lookup"><span data-stu-id="8a017-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="8a017-104">授权是指确定用户可执行的操作的过程。</span><span class="sxs-lookup"><span data-stu-id="8a017-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="8a017-105">例如，允许管理用户创建文档库、添加文档、编辑文档和删除文档。</span><span class="sxs-lookup"><span data-stu-id="8a017-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="8a017-106">使用库的非管理用户仅获得读取文档的权限。</span><span class="sxs-lookup"><span data-stu-id="8a017-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="8a017-107">授权与身份验证相互独立。</span><span class="sxs-lookup"><span data-stu-id="8a017-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="8a017-108">但是，授权需要一种身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="8a017-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="8a017-109">身份验证是认定用户的过程。</span><span class="sxs-lookup"><span data-stu-id="8a017-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="8a017-110">身份验证可为当前用户创建一个或多个标识。</span><span class="sxs-lookup"><span data-stu-id="8a017-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="8a017-111">有关 ASP.NET Core 中的身份验证的详细信息<xref:security/authentication/index>，请参阅。</span><span class="sxs-lookup"><span data-stu-id="8a017-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="8a017-112">授权类型</span><span class="sxs-lookup"><span data-stu-id="8a017-112">Authorization types</span></span>

<span data-ttu-id="8a017-113">ASP.NET Core 授权提供简单的声明性[角色](xref:security/authorization/roles)和[基于策略](xref:security/authorization/policies)的丰富模型。</span><span class="sxs-lookup"><span data-stu-id="8a017-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="8a017-114">授权在要求中表示，而处理程序根据要求评估用户的声明。</span><span class="sxs-lookup"><span data-stu-id="8a017-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="8a017-115">命令式检查可以基于简单的策略或策略，这些策略可评估用户尝试访问的资源的用户标识和属性。</span><span class="sxs-lookup"><span data-stu-id="8a017-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="8a017-116">命名空间</span><span class="sxs-lookup"><span data-stu-id="8a017-116">Namespaces</span></span>

<span data-ttu-id="8a017-117">可在`Microsoft.AspNetCore.Authorization`命名空间中`AuthorizeAttribute`找到`AllowAnonymousAttribute`授权组件，包括和属性。</span><span class="sxs-lookup"><span data-stu-id="8a017-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="8a017-118">请查阅有关[简单授权](xref:security/authorization/simple)的文档。</span><span class="sxs-lookup"><span data-stu-id="8a017-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
