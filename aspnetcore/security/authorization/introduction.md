---
title: ASP.NET Core 中的授权简介
author: rick-anderson
description: 了解授权基础知识和授权在 ASP.NET Core 应用中的工作原理。
ms.author: riande
ms.date: 10/14/2016
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: security/authorization/introduction
ms.openlocfilehash: 8afee7d8bc6b7ad71154916f4a41a2b417b24f9a
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060243"
---
# <a name="introduction-to-authorization-in-aspnet-core"></a><span data-ttu-id="cfc85-103">ASP.NET Core 中的授权简介</span><span class="sxs-lookup"><span data-stu-id="cfc85-103">Introduction to authorization in ASP.NET Core</span></span>

<a name="security-authorization-introduction"></a>

<span data-ttu-id="cfc85-104">授权是指确定用户可执行的操作的过程。</span><span class="sxs-lookup"><span data-stu-id="cfc85-104">Authorization refers to the process that determines what a user is able to do.</span></span> <span data-ttu-id="cfc85-105">例如，允许管理用户创建文档库、添加文档、编辑文档和删除文档。</span><span class="sxs-lookup"><span data-stu-id="cfc85-105">For example, an administrative user is allowed to create a document library, add documents, edit documents, and delete them.</span></span> <span data-ttu-id="cfc85-106">使用库的非管理用户仅获得读取文档的权限。</span><span class="sxs-lookup"><span data-stu-id="cfc85-106">A non-administrative user working with the library is only authorized to read the documents.</span></span>

<span data-ttu-id="cfc85-107">授权与身份验证相互独立。</span><span class="sxs-lookup"><span data-stu-id="cfc85-107">Authorization is orthogonal and independent from authentication.</span></span> <span data-ttu-id="cfc85-108">但是，授权需要一种身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="cfc85-108">However, authorization requires an authentication mechanism.</span></span> <span data-ttu-id="cfc85-109">身份验证是认定用户的过程。</span><span class="sxs-lookup"><span data-stu-id="cfc85-109">Authentication is the process of ascertaining who a user is.</span></span> <span data-ttu-id="cfc85-110">身份验证可为当前用户创建一个或多个标识。</span><span class="sxs-lookup"><span data-stu-id="cfc85-110">Authentication may create one or more identities for the current user.</span></span>

<span data-ttu-id="cfc85-111">有关 ASP.NET Core 中的身份验证的详细信息，请参阅 <xref:security/authentication/index> 。</span><span class="sxs-lookup"><span data-stu-id="cfc85-111">For more information about authentication in ASP.NET Core, see <xref:security/authentication/index>.</span></span>

## <a name="authorization-types"></a><span data-ttu-id="cfc85-112">授权类型</span><span class="sxs-lookup"><span data-stu-id="cfc85-112">Authorization types</span></span>

<span data-ttu-id="cfc85-113">ASP.NET Core 授权提供简单的声明性 [角色](xref:security/authorization/roles) 和 [基于策略](xref:security/authorization/policies) 的丰富模型。</span><span class="sxs-lookup"><span data-stu-id="cfc85-113">ASP.NET Core authorization provides a simple, declarative [role](xref:security/authorization/roles) and a rich [policy-based](xref:security/authorization/policies) model.</span></span> <span data-ttu-id="cfc85-114">授权在要求中表示，而处理程序根据要求评估用户的声明。</span><span class="sxs-lookup"><span data-stu-id="cfc85-114">Authorization is expressed in requirements, and handlers evaluate a user's claims against requirements.</span></span> <span data-ttu-id="cfc85-115">命令式检查可以基于简单的策略或策略，这些策略可评估用户尝试访问的资源的用户标识和属性。</span><span class="sxs-lookup"><span data-stu-id="cfc85-115">Imperative checks can be based on simple policies or policies which evaluate both the user identity and properties of the resource that the user is attempting to access.</span></span>

## <a name="namespaces"></a><span data-ttu-id="cfc85-116">命名空间</span><span class="sxs-lookup"><span data-stu-id="cfc85-116">Namespaces</span></span>

<span data-ttu-id="cfc85-117">`AuthorizeAttribute` `AllowAnonymousAttribute` 可在命名空间中找到授权组件，包括和属性 `Microsoft.AspNetCore.Authorization` 。</span><span class="sxs-lookup"><span data-stu-id="cfc85-117">Authorization components, including the `AuthorizeAttribute` and `AllowAnonymousAttribute` attributes, are found in the `Microsoft.AspNetCore.Authorization` namespace.</span></span>

<span data-ttu-id="cfc85-118">请查阅有关 [简单授权](xref:security/authorization/simple)的文档。</span><span class="sxs-lookup"><span data-stu-id="cfc85-118">Consult the documentation on [simple authorization](xref:security/authorization/simple).</span></span>
