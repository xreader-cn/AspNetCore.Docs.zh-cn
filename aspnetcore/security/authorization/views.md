---
title: ASP.NET Core MVC 中基于视图的授权
author: rick-anderson
description: 本文档演示如何在 ASP.NET Core 视图中注入和使用授权服务 Razor 。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 11/08/2019
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
uid: security/authorization/views
ms.openlocfilehash: a9576a48ad6badc5130d89940e4112e69eada1b2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021934"
---
# <a name="view-based-authorization-in-aspnet-core-mvc"></a><span data-ttu-id="ea193-103">ASP.NET Core MVC 中基于视图的授权</span><span class="sxs-lookup"><span data-stu-id="ea193-103">View-based authorization in ASP.NET Core MVC</span></span>

<span data-ttu-id="ea193-104">开发人员通常需要根据当前用户标识来显示、隐藏或修改 UI。</span><span class="sxs-lookup"><span data-stu-id="ea193-104">A developer often wants to show, hide, or otherwise modify a UI based on the current user identity.</span></span> <span data-ttu-id="ea193-105">可以通过[依赖关系注入](xref:fundamentals/dependency-injection)访问 MVC 视图中的授权服务。</span><span class="sxs-lookup"><span data-stu-id="ea193-105">You can access the authorization service within MVC views via [dependency injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="ea193-106">若要将授权服务注入 Razor 视图，请使用 `@inject` 指令：</span><span class="sxs-lookup"><span data-stu-id="ea193-106">To inject the authorization service into a Razor view, use the `@inject` directive:</span></span>

```cshtml
@using Microsoft.AspNetCore.Authorization
@inject IAuthorizationService AuthorizationService
```

<span data-ttu-id="ea193-107">如果要在每个视图中设置授权服务，请将 `@inject` 指令放入*Views*目录的 *_ViewImports.*</span><span class="sxs-lookup"><span data-stu-id="ea193-107">If you want the authorization service in every view, place the `@inject` directive into the *_ViewImports.cshtml* file of the *Views* directory.</span></span> <span data-ttu-id="ea193-108">有关详细信息，请参阅[视图中的依赖关系注入](xref:mvc/views/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="ea193-108">For more information, see [Dependency injection into views](xref:mvc/views/dependency-injection).</span></span>

<span data-ttu-id="ea193-109">使用注入的授权服务进行调用的方法与在 `AuthorizeAsync` [基于资源的授权](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)期间进行检查的方式完全相同：</span><span class="sxs-lookup"><span data-stu-id="ea193-109">Use the injected authorization service to invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, "PolicyName")).Succeeded)
{
    <p>This paragraph is displayed because you fulfilled PolicyName.</p>
}
```

<span data-ttu-id="ea193-110">在某些情况下，资源将是您的视图模型。</span><span class="sxs-lookup"><span data-stu-id="ea193-110">In some cases, the resource will be your view model.</span></span> <span data-ttu-id="ea193-111">调用的方法与在 `AuthorizeAsync` [基于资源的授权](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative)期间进行检查的方式完全相同：</span><span class="sxs-lookup"><span data-stu-id="ea193-111">Invoke `AuthorizeAsync` in exactly the same way you would check during [resource-based authorization](xref:security/authorization/resourcebased#security-authorization-resource-based-imperative):</span></span>

```cshtml
@if ((await AuthorizationService.AuthorizeAsync(User, Model, Operations.Edit)).Succeeded)
{
    <p><a class="btn btn-default" role="button"
        href="@Url.Action("Edit", "Document", new { id = Model.Id })">Edit</a></p>
}
```

<span data-ttu-id="ea193-112">在前面的代码中，该模型将作为资源进行传递，策略评估应考虑到该资源。</span><span class="sxs-lookup"><span data-stu-id="ea193-112">In the preceding code, the model is passed as a resource the policy evaluation should take into consideration.</span></span>

> [!WARNING]
> <span data-ttu-id="ea193-113">请不要依赖于应用的 UI 元素的切换可见性，作为唯一的授权检查。</span><span class="sxs-lookup"><span data-stu-id="ea193-113">Don't rely on toggling visibility of your app's UI elements as the sole authorization check.</span></span> <span data-ttu-id="ea193-114">隐藏 UI 元素可能无法完全阻止对其关联控制器操作的访问。</span><span class="sxs-lookup"><span data-stu-id="ea193-114">Hiding a UI element may not completely prevent access to its associated controller action.</span></span> <span data-ttu-id="ea193-115">例如，请考虑前面代码片段中的按钮。</span><span class="sxs-lookup"><span data-stu-id="ea193-115">For example, consider the button in the preceding code snippet.</span></span> <span data-ttu-id="ea193-116">`Edit`如果用户知道相对资源 URL 是 */Document/Edit/1*的，则用户可以调用操作方法。</span><span class="sxs-lookup"><span data-stu-id="ea193-116">A user can invoke the `Edit` action method if he or she knows the relative resource URL is */Document/Edit/1*.</span></span> <span data-ttu-id="ea193-117">出于此原因， `Edit` 操作方法应执行其自己的授权检查。</span><span class="sxs-lookup"><span data-stu-id="ea193-117">For this reason, the `Edit` action method should perform its own authorization check.</span></span>
