---
title: ASP.NET Core MVC 中基于视图的授权
author: rick-anderson
description: 本文档演示如何在 ASP.NET Core Razor视图中注入和使用授权服务。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: security/authorization/views
ms.openlocfilehash: 1a3b4a92d270844fa99fc9fb0283226e94edb22d
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775617"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="7434e-103">ASP.NET Core MVC 中基于视图的授权</span><span class="sxs-lookup"><span data-stu-id="7434e-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="7434e-104">开发人员通常需要根据当前用户标识来显示、隐藏或修改 UI。</span><span class="sxs-lookup"><span data-stu-id="7434e-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="7434e-105">可以通过[依赖关系注入](xref:fundamentals/dependency-injection)访问 MVC 视图中的授权服务。</span><span class="sxs-lookup"><span data-stu-id="7434e-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="7434e-106">若要将授权服务注入Razor视图，请使用`@inject`指令：</span><span class="sxs-lookup"><span data-stu-id="7434e-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="7434e-107">如果要在每个视图中设置授权服务，请`@inject`将指令放入*Views*目录的 *_ViewImports.*</span><span class="sxs-lookup"><span data-stu-id="7434e-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="7434e-108">有关详细信息，请参阅[视图中的依赖关系注入](xref:mvc/views/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="7434e-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="7434e-109">使用注入的授权服务进行调用`AuthorizeAsync`的方法与在[基于资源的授权](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)期间进行检查的方式完全相同：</span><span class="sxs-lookup"><span data-stu-id="7434e-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="7434e-110">在某些情况下，资源将是您的视图模型。</span><span class="sxs-lookup"><span data-stu-id="7434e-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="7434e-111">调用`AuthorizeAsync`的方法与在[基于资源的授权](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)期间进行检查的方式完全相同：</span><span class="sxs-lookup"><span data-stu-id="7434e-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="7434e-112">在前面的代码中，该模型将作为资源进行传递，策略评估应考虑到该资源。</span><span class="sxs-lookup"><span data-stu-id="7434e-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="7434e-113">请不要依赖于应用的 UI 元素的切换可见性，作为唯一的授权检查。</span><span class="sxs-lookup"><span data-stu-id="7434e-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="7434e-114">隐藏 UI 元素可能无法完全阻止对其关联控制器操作的访问。</span><span class="sxs-lookup"><span data-stu-id="7434e-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="7434e-115">例如，请考虑前面代码片段中的按钮。</span><span class="sxs-lookup"><span data-stu-id="7434e-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="7434e-116">如果用户知道相对资源`Edit` URL 是 */Document/Edit/1*的，则用户可以调用操作方法。</span><span class="sxs-lookup"><span data-stu-id="7434e-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="7434e-117">出于此原因， `Edit`操作方法应执行其自己的授权检查。</span><span class="sxs-lookup"><span data-stu-id="7434e-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
