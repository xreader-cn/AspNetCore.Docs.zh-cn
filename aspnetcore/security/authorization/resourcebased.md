---
title: ASP.NET Core 中基于资源的授权
author: scottaddie
description: 了解如何在 ASP.NET Core 应用程序中实现的基于资源的授权，Authorize 属性不会满足要求。
ms.author: scaddie
ms.custom: mvc
ms.date: 11/15/2018
uid: security/authorization/resourcebased
ms.openlocfilehash: afc152ea677cab42d57bd642b4821159f125117e
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64891734"
---
# <a name="resource-based-authorization-in-aspnet-core"></a><span data-ttu-id="15c38-103">ASP.NET Core 中基于资源的授权</span><span class="sxs-lookup"><span data-stu-id="15c38-103">Resource-based authorization in ASP.NET Core</span></span>

<span data-ttu-id="15c38-104">授权策略取决于要访问的资源。</span><span class="sxs-lookup"><span data-stu-id="15c38-104">Authorization strategy depends upon the resource being accessed.</span></span> <span data-ttu-id="15c38-105">请考虑具有 author 属性的文档。</span><span class="sxs-lookup"><span data-stu-id="15c38-105">Consider a document that has an author property.</span></span> <span data-ttu-id="15c38-106">允许仅作者更新文档。</span><span class="sxs-lookup"><span data-stu-id="15c38-106">Only the author is allowed to update the document.</span></span> <span data-ttu-id="15c38-107">因此，文档必须检索从数据存储区进行授权评估之前。</span><span class="sxs-lookup"><span data-stu-id="15c38-107">Consequently, the document must be retrieved from the data store before authorization evaluation can occur.</span></span>

<span data-ttu-id="15c38-108">数据绑定之前和之后，页面处理程序或加载文档的操作的执行进行属性评估。</span><span class="sxs-lookup"><span data-stu-id="15c38-108">Attribute evaluation occurs before data binding and before execution of the page handler or action that loads the document.</span></span> <span data-ttu-id="15c38-109">出于这些原因，使用声明性授权`[Authorize]`属性不足以满足需求。</span><span class="sxs-lookup"><span data-stu-id="15c38-109">For these reasons, declarative authorization with an `[Authorize]` attribute doesn't suffice.</span></span> <span data-ttu-id="15c38-110">相反，可以调用自定义授权方法&mdash;称为样式*命令性授权*。</span><span class="sxs-lookup"><span data-stu-id="15c38-110">Instead, you can invoke a custom authorization method&mdash;a style known as *imperative authorization*.</span></span>

<span data-ttu-id="15c38-111">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="15c38-111">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/authorization/resourcebased/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="15c38-112">[使用受授权的用户数据创建 ASP.NET Core 应用](xref:security/authorization/secure-data)包含的示例应用程序使用的基于资源的授权。</span><span class="sxs-lookup"><span data-stu-id="15c38-112">[Create an ASP.NET Core app with user data protected by authorization](xref:security/authorization/secure-data) contains a sample app that uses resource-based authorization.</span></span>

## <a name="use-imperative-authorization"></a><span data-ttu-id="15c38-113">使用命令性授权</span><span class="sxs-lookup"><span data-stu-id="15c38-113">Use imperative authorization</span></span>

<span data-ttu-id="15c38-114">授权作为实现[IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice)服务并注册内的服务集合中`Startup`类。</span><span class="sxs-lookup"><span data-stu-id="15c38-114">Authorization is implemented as an [IAuthorizationService](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizationservice) service and is registered in the service collection within the `Startup` class.</span></span> <span data-ttu-id="15c38-115">该服务可通过[依赖关系注入](xref:fundamentals/dependency-injection)页面处理程序或进行操作。</span><span class="sxs-lookup"><span data-stu-id="15c38-115">The service is made available via [dependency injection](xref:fundamentals/dependency-injection) to page handlers or actions.</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Controllers/DocumentController.cs?name=snippet_IAuthServiceDI&highlight=6)]

<span data-ttu-id="15c38-116">`IAuthorizationService` 具有两个`AuthorizeAsync`方法重载： 一个接受资源和策略名称和其他接受资源和要求来评估的列表。</span><span class="sxs-lookup"><span data-stu-id="15c38-116">`IAuthorizationService` has two `AuthorizeAsync` method overloads: one accepting the resource and the policy name and the other accepting the resource and a list of requirements to evaluate.</span></span>

::: moniker range=">= aspnetcore-2.0"

```csharp
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<AuthorizationResult> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

```csharp
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          IEnumerable<IAuthorizationRequirement> requirements);
Task<bool> AuthorizeAsync(ClaimsPrincipal user,
                          object resource,
                          string policyName);
```

::: moniker-end

<a name="security-authorization-resource-based-imperative"></a>

<span data-ttu-id="15c38-117">在以下示例中，要保护的资源加载到自定义`Document`对象。</span><span class="sxs-lookup"><span data-stu-id="15c38-117">In the following example, the resource to be secured is loaded into a custom `Document` object.</span></span> <span data-ttu-id="15c38-118">`AuthorizeAsync`重载进行调用以确定当前用户是否允许编辑提供的文档。</span><span class="sxs-lookup"><span data-stu-id="15c38-118">An `AuthorizeAsync` overload is invoked to determine whether the current user is allowed to edit the provided document.</span></span> <span data-ttu-id="15c38-119">自定义"EditPolicy"授权策略可分解为决策。</span><span class="sxs-lookup"><span data-stu-id="15c38-119">A custom "EditPolicy" authorization policy is factored into the decision.</span></span> <span data-ttu-id="15c38-120">请参阅[自定义基于策略的授权](xref:security/authorization/policies)创建授权策略的详细信息。</span><span class="sxs-lookup"><span data-stu-id="15c38-120">See [Custom policy-based authorization](xref:security/authorization/policies) for more on creating authorization policies.</span></span>

> [!NOTE]
> <span data-ttu-id="15c38-121">下面的代码示例假定已经运行了身份验证和组`User`属性。</span><span class="sxs-lookup"><span data-stu-id="15c38-121">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/Edit.cshtml.cs?name=snippet_DocumentEditHandler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentEditAction)]

::: moniker-end

## <a name="write-a-resource-based-handler"></a><span data-ttu-id="15c38-122">编写基于资源的处理程序</span><span class="sxs-lookup"><span data-stu-id="15c38-122">Write a resource-based handler</span></span>

<span data-ttu-id="15c38-123">编写处理程序的基于资源的授权没有太大区别比[编写普通要求处理程序](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler)。</span><span class="sxs-lookup"><span data-stu-id="15c38-123">Writing a handler for resource-based authorization isn't much different than [writing a plain requirements handler](xref:security/authorization/policies#security-authorization-policies-based-authorization-handler).</span></span> <span data-ttu-id="15c38-124">创建自定义要求类，并实现要求处理程序类。</span><span class="sxs-lookup"><span data-stu-id="15c38-124">Create a custom requirement class, and implement a requirement handler class.</span></span> <span data-ttu-id="15c38-125">创建要求类的详细信息，请参阅[要求](xref:security/authorization/policies#requirements)。</span><span class="sxs-lookup"><span data-stu-id="15c38-125">For more information on creating a requirement class, see [Requirements](xref:security/authorization/policies#requirements).</span></span>

<span data-ttu-id="15c38-126">处理程序类指定的要求和资源类型。</span><span class="sxs-lookup"><span data-stu-id="15c38-126">The handler class specifies both the requirement and resource type.</span></span> <span data-ttu-id="15c38-127">例如，处理程序利用`SameAuthorRequirement`和一个`Document`资源如下所示：</span><span class="sxs-lookup"><span data-stu-id="15c38-127">For example, a handler utilizing a `SameAuthorRequirement` and a `Document` resource follows:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationHandler.cs?name=snippet_HandlerAndRequirement)]

::: moniker-end

<span data-ttu-id="15c38-128">在上述示例中，假设`SameAuthorRequirement`是一种特殊情况的多个泛型`SpecificAuthorRequirement`类。</span><span class="sxs-lookup"><span data-stu-id="15c38-128">In the preceding example, imagine that `SameAuthorRequirement` is a special case of a more generic `SpecificAuthorRequirement` class.</span></span> <span data-ttu-id="15c38-129">`SpecificAuthorRequirement`类 （未显示） 包含`Name`属性表示的作者的名称。</span><span class="sxs-lookup"><span data-stu-id="15c38-129">The `SpecificAuthorRequirement` class (not shown) contains a `Name` property representing the name of the author.</span></span> <span data-ttu-id="15c38-130">`Name`属性可以设置为当前用户。</span><span class="sxs-lookup"><span data-stu-id="15c38-130">The `Name` property could be set to the current user.</span></span>

<span data-ttu-id="15c38-131">注册的要求和处理程序中的`Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="15c38-131">Register the requirement and handler in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Startup.cs?name=snippet_ConfigureServicesSample&highlight=3-7,9)]

### <a name="operational-requirements"></a><span data-ttu-id="15c38-132">操作要求</span><span class="sxs-lookup"><span data-stu-id="15c38-132">Operational requirements</span></span>

<span data-ttu-id="15c38-133">若要进行决策基于 CRUD （创建、 读取、 更新、 删除） 操作的结果，请使用[OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement)帮助器类。</span><span class="sxs-lookup"><span data-stu-id="15c38-133">If you're making decisions based on the outcomes of CRUD (Create, Read, Update, Delete) operations, use the [OperationAuthorizationRequirement](/dotnet/api/microsoft.aspnetcore.authorization.infrastructure.operationauthorizationrequirement) helper class.</span></span> <span data-ttu-id="15c38-134">此类，可为每个操作类型编写单个处理程序而不是单独的类。</span><span class="sxs-lookup"><span data-stu-id="15c38-134">This class enables you to write a single handler instead of an individual class for each operation type.</span></span> <span data-ttu-id="15c38-135">若要使用它，提供一些操作名称：</span><span class="sxs-lookup"><span data-stu-id="15c38-135">To use it, provide some operation names:</span></span>

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_OperationsClass)]

<span data-ttu-id="15c38-136">处理程序的实现，如下所示，使用`OperationAuthorizationRequirement`要求和`Document`资源：</span><span class="sxs-lookup"><span data-stu-id="15c38-136">The handler is implemented as follows, using an `OperationAuthorizationRequirement` requirement and a `Document` resource:</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Services/DocumentAuthorizationCrudHandler.cs?name=snippet_Handler)]

::: moniker-end

<span data-ttu-id="15c38-137">前面的处理程序验证该操作使用的资源、 用户的标识和需求的`Name`属性。</span><span class="sxs-lookup"><span data-stu-id="15c38-137">The preceding handler validates the operation using the resource, the user's identity, and the requirement's `Name` property.</span></span>

<span data-ttu-id="15c38-138">若要调用的操作的资源处理程序，指定该操作时调用`AuthorizeAsync`页面处理程序或操作中。</span><span class="sxs-lookup"><span data-stu-id="15c38-138">To call an operational resource handler, specify the operation when invoking `AuthorizeAsync` in your page handler or action.</span></span> <span data-ttu-id="15c38-139">下面的示例确定是否允许经过身份验证的用户若要查看提供的文档。</span><span class="sxs-lookup"><span data-stu-id="15c38-139">The following example determines whether the authenticated user is permitted to view the provided document.</span></span>

> [!NOTE]
> <span data-ttu-id="15c38-140">下面的代码示例假定已经运行了身份验证和组`User`属性。</span><span class="sxs-lookup"><span data-stu-id="15c38-140">The following code samples assume authentication has run and set the `User` property.</span></span>

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp2/Pages/Document/View.cshtml.cs?name=snippet_DocumentViewHandler&highlight=10-11)]

<span data-ttu-id="15c38-141">如果授权成功，则返回查看文档的页。</span><span class="sxs-lookup"><span data-stu-id="15c38-141">If authorization succeeds, the page for viewing the document is returned.</span></span> <span data-ttu-id="15c38-142">如果授权失败而用户进行身份验证，返回`ForbidResult`通知授权失败的任何身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="15c38-142">If authorization fails but the user is authenticated, returning `ForbidResult` informs any authentication middleware that authorization failed.</span></span> <span data-ttu-id="15c38-143">一个`ChallengeResult`时必须执行身份验证，将返回。</span><span class="sxs-lookup"><span data-stu-id="15c38-143">A `ChallengeResult` is returned when authentication must be performed.</span></span> <span data-ttu-id="15c38-144">对于交互式浏览器客户端，可能需要将用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="15c38-144">For interactive browser clients, it may be appropriate to redirect the user to a login page.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-1.1"

[!code-csharp[](resourcebased/samples/ResourceBasedAuthApp1/Controllers/DocumentController.cs?name=snippet_DocumentViewAction&highlight=11-12)]

<span data-ttu-id="15c38-145">如果授权成功，则返回文档的视图。</span><span class="sxs-lookup"><span data-stu-id="15c38-145">If authorization succeeds, the view for the document is returned.</span></span> <span data-ttu-id="15c38-146">如果授权失败，返回`ChallengeResult`授权失败，并将中间件可以采取适当的响应将通知任何身份验证中间件。</span><span class="sxs-lookup"><span data-stu-id="15c38-146">If authorization fails, returning `ChallengeResult` informs any authentication middleware that authorization failed, and the middleware can take the appropriate response.</span></span> <span data-ttu-id="15c38-147">相应的响应可能返回 401 或 403 状态代码。</span><span class="sxs-lookup"><span data-stu-id="15c38-147">An appropriate response could be returning a 401 or 403 status code.</span></span> <span data-ttu-id="15c38-148">对于交互式浏览器客户端，这可能意味着将用户重定向到登录页。</span><span class="sxs-lookup"><span data-stu-id="15c38-148">For interactive browser clients, it could mean redirecting the user to a login page.</span></span>

::: moniker-end
