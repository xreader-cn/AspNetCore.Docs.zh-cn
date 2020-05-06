---
title: ASP.NET Core Web API 中控制器操作的返回类型
author: scottaddie
description: 了解在 ASP.NET Core Web API 中使用各种控制器操作方法返回类型的相关信息。
ms.author: scaddie
ms.custom: mvc
ms.date: 02/03/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: web-api/action-return-types
ms.openlocfilehash: 4db553a61ca0eeabe35a08731295333f588ee0fc
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82774937"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a><span data-ttu-id="affc5-103">ASP.NET Core Web API 中控制器操作的返回类型</span><span class="sxs-lookup"><span data-stu-id="affc5-103">Controller action return types in ASP.NET Core web API</span></span>

<span data-ttu-id="affc5-104">作者：[Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="affc5-104">By [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="affc5-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="affc5-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/action-return-types/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="affc5-106">ASP.NET Core 提供以下 Web API 控制器操作返回类型选项：</span><span class="sxs-lookup"><span data-stu-id="affc5-106">ASP.NET Core offers the following options for web API controller action return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

* [<span data-ttu-id="affc5-107">特定类型</span><span class="sxs-lookup"><span data-stu-id="affc5-107">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="affc5-108">IActionResult</span><span class="sxs-lookup"><span data-stu-id="affc5-108">IActionResult</span></span>](#iactionresult-type)
* [<span data-ttu-id="affc5-109">ActionResult\<T></span><span class="sxs-lookup"><span data-stu-id="affc5-109">ActionResult\<T></span></span>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [<span data-ttu-id="affc5-110">特定类型</span><span class="sxs-lookup"><span data-stu-id="affc5-110">Specific type</span></span>](#specific-type)
* [<span data-ttu-id="affc5-111">IActionResult</span><span class="sxs-lookup"><span data-stu-id="affc5-111">IActionResult</span></span>](#iactionresult-type)

::: moniker-end

<span data-ttu-id="affc5-112">本文档说明每种返回类型的最佳适用情况。</span><span class="sxs-lookup"><span data-stu-id="affc5-112">This document explains when it's most appropriate to use each return type.</span></span>

## <a name="specific-type"></a><span data-ttu-id="affc5-113">特定类型</span><span class="sxs-lookup"><span data-stu-id="affc5-113">Specific type</span></span>

<span data-ttu-id="affc5-114">最简单的操作返回基元或复杂数据类型（如 `string` 或自定义对象类型）。</span><span class="sxs-lookup"><span data-stu-id="affc5-114">The simplest action returns a primitive or complex data type (for example, `string` or a custom object type).</span></span> <span data-ttu-id="affc5-115">请参考以下操作，该操作返回自定义 `Product` 对象的集合：</span><span class="sxs-lookup"><span data-stu-id="affc5-115">Consider the following action, which returns a collection of custom `Product` objects:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

<span data-ttu-id="affc5-116">在执行操作期间无需防范已知条件，返回特定类型即可满足要求。</span><span class="sxs-lookup"><span data-stu-id="affc5-116">Without known conditions to safeguard against during action execution, returning a specific type could suffice.</span></span> <span data-ttu-id="affc5-117">上述操作不接受任何参数，因此不需要参数约束验证。</span><span class="sxs-lookup"><span data-stu-id="affc5-117">The preceding action accepts no parameters, so parameter constraints validation isn't needed.</span></span>

<span data-ttu-id="affc5-118">当在操作中需要考虑已知条件时，将引入多个返回路径。</span><span class="sxs-lookup"><span data-stu-id="affc5-118">When known conditions need to be accounted for in an action, multiple return paths are introduced.</span></span> <span data-ttu-id="affc5-119">在此类情况下，通常会将 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 返回类型和基元或复杂返回类型混合。</span><span class="sxs-lookup"><span data-stu-id="affc5-119">In such a case, it's common to mix an <xref:Microsoft.AspNetCore.Mvc.ActionResult> return type with the primitive or complex return type.</span></span> <span data-ttu-id="affc5-120">要支持此类操作，必须使用 [IActionResult](#iactionresult-type) 或 [ActionResult\<T>](#actionresultt-type)。</span><span class="sxs-lookup"><span data-stu-id="affc5-120">Either [IActionResult](#iactionresult-type) or [ActionResult\<T>](#actionresultt-type) are necessary to accommodate this type of action.</span></span>

### <a name="return-ienumerablet-or-iasyncenumerablet"></a><span data-ttu-id="affc5-121">返回 IEnumerable\<T> 或 IAsyncEnumerable\<T></span><span class="sxs-lookup"><span data-stu-id="affc5-121">Return IEnumerable\<T> or IAsyncEnumerable\<T></span></span>

<span data-ttu-id="affc5-122">在 ASP.NET Core 2.2 及更低版本中，从操作返回 <xref:System.Collections.Generic.IEnumerable%601> 会导致序列化程序同步集合迭代。</span><span class="sxs-lookup"><span data-stu-id="affc5-122">In ASP.NET Core 2.2 and earlier, returning <xref:System.Collections.Generic.IEnumerable%601> from an action results in synchronous collection iteration by the serializer.</span></span> <span data-ttu-id="affc5-123">因此会阻止调用，并且可能会导致线程池资源不足。</span><span class="sxs-lookup"><span data-stu-id="affc5-123">The result is the blocking of calls and a potential for thread pool starvation.</span></span> <span data-ttu-id="affc5-124">为了说明这一点，假设 Entity Framework (EF) Core 用于满足 Web API 的数据访问需求。</span><span class="sxs-lookup"><span data-stu-id="affc5-124">To illustrate, imagine that Entity Framework (EF) Core is being used for the web API's data access needs.</span></span> <span data-ttu-id="affc5-125">序列化期间，将同步枚举以下操作的返回类型：</span><span class="sxs-lookup"><span data-stu-id="affc5-125">The following action's return type is synchronously enumerated during serialization:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="affc5-126">若要避免在 ASP.NET Core 2.2 及更低版本的数据库上同步枚举和阻止等待，请调用 `ToListAsync`：</span><span class="sxs-lookup"><span data-stu-id="affc5-126">To avoid synchronous enumeration and blocking waits on the database in ASP.NET Core 2.2 and earlier, invoke `ToListAsync`:</span></span>

```csharp
public async Task<IEnumerable<Product>> GetOnSaleProducts() =>
    await _context.Products.Where(p => p.IsOnSale).ToListAsync();
```

<span data-ttu-id="affc5-127">在 ASP.NET Core 3.0 及更高版本中，从操作返回 `IAsyncEnumerable<T>`：</span><span class="sxs-lookup"><span data-stu-id="affc5-127">In ASP.NET Core 3.0 and later, returning `IAsyncEnumerable<T>` from an action:</span></span>

* <span data-ttu-id="affc5-128">不再会导致同步迭代。</span><span class="sxs-lookup"><span data-stu-id="affc5-128">No longer results in synchronous iteration.</span></span>
* <span data-ttu-id="affc5-129">变成和返回 <xref:System.Collections.Generic.IEnumerable%601> 一样高效。</span><span class="sxs-lookup"><span data-stu-id="affc5-129">Becomes as efficient as returning <xref:System.Collections.Generic.IEnumerable%601>.</span></span>

<span data-ttu-id="affc5-130">ASP.NET Core 3.0 和更高版本将缓冲以下操作的结果，然后将其提供给序列化程序：</span><span class="sxs-lookup"><span data-stu-id="affc5-130">ASP.NET Core 3.0 and later buffers the result of the following action before providing it to the serializer:</span></span>

```csharp
public IEnumerable<Product> GetOnSaleProducts() =>
    _context.Products.Where(p => p.IsOnSale);
```

<span data-ttu-id="affc5-131">考虑将操作签名的返回类型声明为 `IAsyncEnumerable<T>` 以保证异步迭代。</span><span class="sxs-lookup"><span data-stu-id="affc5-131">Consider declaring the action signature's return type as `IAsyncEnumerable<T>` to guarantee the asynchronous iteration.</span></span> <span data-ttu-id="affc5-132">最终，迭代模式基于要返回的基础具体类型。</span><span class="sxs-lookup"><span data-stu-id="affc5-132">Ultimately, the iteration mode is based on the underlying concrete type being returned.</span></span> <span data-ttu-id="affc5-133">MVC 自动对实现 `IAsyncEnumerable<T>` 的任何具体类型进行缓冲。</span><span class="sxs-lookup"><span data-stu-id="affc5-133">MVC automatically buffers any concrete type that implements `IAsyncEnumerable<T>`.</span></span>

<span data-ttu-id="affc5-134">请考虑以下操作，该操作将销售价格的产品记录返回为 `IEnumerable<Product>`：</span><span class="sxs-lookup"><span data-stu-id="affc5-134">Consider the following action, which returns sale-priced product records as `IEnumerable<Product>`:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProducts)]

<span data-ttu-id="affc5-135">上述操作的 `IAsyncEnumerable<Product>` 等效项为：</span><span class="sxs-lookup"><span data-stu-id="affc5-135">The `IAsyncEnumerable<Product>` equivalent of the preceding action is:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/3x/WebApiSample.Api.30/Controllers/ProductsController.cs?name=snippet_GetOnSaleProductsAsync)]

<span data-ttu-id="affc5-136">自 ASP.NET Core 3.0 起，前面两个操作均为非阻止性。</span><span class="sxs-lookup"><span data-stu-id="affc5-136">Both of the preceding actions are non-blocking as of ASP.NET Core 3.0.</span></span>

## <a name="iactionresult-type"></a><span data-ttu-id="affc5-137">IActionResult 类型</span><span class="sxs-lookup"><span data-stu-id="affc5-137">IActionResult type</span></span>

<span data-ttu-id="affc5-138">当操作中可能有多个 `ActionResult` 返回类型时，适合使用 <xref:Microsoft.AspNetCore.Mvc.IActionResult> 返回类型。</span><span class="sxs-lookup"><span data-stu-id="affc5-138">The <xref:Microsoft.AspNetCore.Mvc.IActionResult> return type is appropriate when multiple `ActionResult` return types are possible in an action.</span></span> <span data-ttu-id="affc5-139">`ActionResult` 类型表示多种 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="affc5-139">The `ActionResult` types represent various HTTP status codes.</span></span> <span data-ttu-id="affc5-140">派生自 `ActionResult` 的任何非抽象类都限定为有效的返回类型。</span><span class="sxs-lookup"><span data-stu-id="affc5-140">Any non-abstract class deriving from `ActionResult` qualifies as a valid return type.</span></span> <span data-ttu-id="affc5-141">此类别中的某些常见返回类型为 <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400)、<xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404) 和 <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200)。</span><span class="sxs-lookup"><span data-stu-id="affc5-141">Some common return types in this category are <xref:Microsoft.AspNetCore.Mvc.BadRequestResult> (400), <xref:Microsoft.AspNetCore.Mvc.NotFoundResult> (404), and <xref:Microsoft.AspNetCore.Mvc.OkObjectResult> (200).</span></span> <span data-ttu-id="affc5-142">或者，可以使用 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 类中的便利方法从操作返回 `ActionResult` 类型。</span><span class="sxs-lookup"><span data-stu-id="affc5-142">Alternatively, convenience methods in the <xref:Microsoft.AspNetCore.Mvc.ControllerBase> class can be used to return `ActionResult` types from an action.</span></span> <span data-ttu-id="affc5-143">例如，`return BadRequest();` 是 `return new BadRequestResult();` 的简写形式。</span><span class="sxs-lookup"><span data-stu-id="affc5-143">For example, `return BadRequest();` is a shorthand form of `return new BadRequestResult();`.</span></span>

<span data-ttu-id="affc5-144">由于这种类型的操作具有多个返回类型和路径，因此需要大量使用[`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute)特性。</span><span class="sxs-lookup"><span data-stu-id="affc5-144">Because there are multiple return types and paths in this type of action, liberal use of the [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute is necessary.</span></span> <span data-ttu-id="affc5-145">此特性可针对 [Swagger](xref:tutorials/web-api-help-pages-using-swagger) 等工具生成的 Web API 帮助页生成更多描述性响应详细信息。</span><span class="sxs-lookup"><span data-stu-id="affc5-145">This attribute produces more descriptive response details for web API help pages generated by tools like [Swagger](xref:tutorials/web-api-help-pages-using-swagger).</span></span> <span data-ttu-id="affc5-146">`[ProducesResponseType]` 指示操作将返回的已知类型和 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="affc5-146">`[ProducesResponseType]` indicates the known types and HTTP status codes to be returned by the action.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="affc5-147">同步操作</span><span class="sxs-lookup"><span data-stu-id="affc5-147">Synchronous action</span></span>

<span data-ttu-id="affc5-148">请参考以下同步操作，其中有两种可能的返回类型：</span><span class="sxs-lookup"><span data-stu-id="affc5-148">Consider the following synchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdIActionResult&highlight=8,11)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

::: moniker-end

<span data-ttu-id="affc5-149">在上述操作中：</span><span class="sxs-lookup"><span data-stu-id="affc5-149">In the preceding action:</span></span>

* <span data-ttu-id="affc5-150">当 `id` 代表的产品不在基础数据存储中时，则返回 404 状态代码。</span><span class="sxs-lookup"><span data-stu-id="affc5-150">A 404 status code is returned when the product represented by `id` doesn't exist in the underlying data store.</span></span> <span data-ttu-id="affc5-151"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> 便利方法作为 `return new NotFoundResult();` 的简写调用。</span><span class="sxs-lookup"><span data-stu-id="affc5-151">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.NotFound*> convenience method is invoked as shorthand for `return new NotFoundResult();`.</span></span>
* <span data-ttu-id="affc5-152">如果产品确实存在，则返回状态代码 200 及 `Product` 对象。</span><span class="sxs-lookup"><span data-stu-id="affc5-152">A 200 status code is returned with the `Product` object when the product does exist.</span></span> <span data-ttu-id="affc5-153"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> 便利方法作为 `return new OkObjectResult(product);` 的简写调用。</span><span class="sxs-lookup"><span data-stu-id="affc5-153">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.Ok*> convenience method is invoked as shorthand for `return new OkObjectResult(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="affc5-154">异步操作</span><span class="sxs-lookup"><span data-stu-id="affc5-154">Asynchronous action</span></span>

<span data-ttu-id="affc5-155">请参考以下异步操作，其中有两种可能的返回类型：</span><span class="sxs-lookup"><span data-stu-id="affc5-155">Consider the following asynchronous action in which there are two possible return types:</span></span>

::: moniker range=">= aspnetcore-2.1"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncIActionResult&highlight=9,14)]

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=9,14)]

::: moniker-end

<span data-ttu-id="affc5-156">在上述操作中：</span><span class="sxs-lookup"><span data-stu-id="affc5-156">In the preceding action:</span></span>

* <span data-ttu-id="affc5-157">当产品说明包含“XYZ 小组件”时，返回 400 状态代码。</span><span class="sxs-lookup"><span data-stu-id="affc5-157">A 400 status code is returned when the product description contains "XYZ Widget".</span></span> <span data-ttu-id="affc5-158"><xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> 便利方法作为 `return new BadRequestResult();` 的简写调用。</span><span class="sxs-lookup"><span data-stu-id="affc5-158">The <xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*> convenience method is invoked as shorthand for `return new BadRequestResult();`.</span></span>
* <span data-ttu-id="affc5-159">在创建产品后，<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 便利方法生成 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="affc5-159">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> convenience method when a product is created.</span></span> <span data-ttu-id="affc5-160">调用 `CreatedAtAction` 的替代方法是 `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`。</span><span class="sxs-lookup"><span data-stu-id="affc5-160">An alternative to calling `CreatedAtAction` is `return new CreatedAtActionResult(nameof(GetById), "Products", new { id = product.Id }, product);`.</span></span> <span data-ttu-id="affc5-161">在此代码路径中，将在响应正文中提供 `Product` 对象。</span><span class="sxs-lookup"><span data-stu-id="affc5-161">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="affc5-162">提供了包含新建产品 URL 的 `Location` 响应标头。</span><span class="sxs-lookup"><span data-stu-id="affc5-162">A `Location` response header containing the newly created product's URL is provided.</span></span>

<span data-ttu-id="affc5-163">例如，以下模型指明请求必须包含 `Name` 和 `Description` 属性。</span><span class="sxs-lookup"><span data-stu-id="affc5-163">For example, the following model indicates that requests must include the `Name` and `Description` properties.</span></span> <span data-ttu-id="affc5-164">未在请求中提供 `Name` 和 `Description` 会导致模型验证失败。</span><span class="sxs-lookup"><span data-stu-id="affc5-164">Failure to provide `Name` and `Description` in the request causes model validation to fail.</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="affc5-165">如果应用[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) ASP.NET Core 2.1 或更高版本中的属性，则模型验证错误将导致400状态代码。</span><span class="sxs-lookup"><span data-stu-id="affc5-165">If the [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute in ASP.NET Core 2.1 or later is applied, model validation errors result in a 400 status code.</span></span> <span data-ttu-id="affc5-166">有关详细信息，请参阅[自动 HTTP 400 响应](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="affc5-166">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="actionresultt-type"></a><span data-ttu-id="affc5-167">ActionResult\<T> 类型</span><span class="sxs-lookup"><span data-stu-id="affc5-167">ActionResult\<T> type</span></span>

<span data-ttu-id="affc5-168">ASP.NET Core 2.1 引入了面向 Web API 控制器操作的 [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) 返回类型。</span><span class="sxs-lookup"><span data-stu-id="affc5-168">ASP.NET Core 2.1 introduced the [ActionResult\<T>](xref:Microsoft.AspNetCore.Mvc.ActionResult`1) return type for web API controller actions.</span></span> <span data-ttu-id="affc5-169">它支持返回从 <xref:Microsoft.AspNetCore.Mvc.ActionResult> 派生的类型或返回[特定类型](#specific-type)。</span><span class="sxs-lookup"><span data-stu-id="affc5-169">It enables you to return a type deriving from <xref:Microsoft.AspNetCore.Mvc.ActionResult> or return a [specific type](#specific-type).</span></span> <span data-ttu-id="affc5-170">`ActionResult<T>` 通过 [IActionResult 类型](#iactionresult-type)可提供以下优势：</span><span class="sxs-lookup"><span data-stu-id="affc5-170">`ActionResult<T>` offers the following benefits over the [IActionResult type](#iactionresult-type):</span></span>

* <span data-ttu-id="affc5-171">可以[`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute)排除特性`Type`的属性。</span><span class="sxs-lookup"><span data-stu-id="affc5-171">The [`[ProducesResponseType]`](xref:Microsoft.AspNetCore.Mvc.ProducesResponseTypeAttribute) attribute's `Type` property can be excluded.</span></span> <span data-ttu-id="affc5-172">例如，`[ProducesResponseType(200, Type = typeof(Product))]` 简化为 `[ProducesResponseType(200)]`。</span><span class="sxs-lookup"><span data-stu-id="affc5-172">For example, `[ProducesResponseType(200, Type = typeof(Product))]` is simplified to `[ProducesResponseType(200)]`.</span></span> <span data-ttu-id="affc5-173">此操作的预期返回类型改为根据 `ActionResult<T>` 中的 `T` 进行推断。</span><span class="sxs-lookup"><span data-stu-id="affc5-173">The action's expected return type is instead inferred from the `T` in `ActionResult<T>`.</span></span>
* <span data-ttu-id="affc5-174">[隐式强制转换运算符](/dotnet/csharp/language-reference/keywords/implicit)支持将 `T` 和 `ActionResult` 均转换为 `ActionResult<T>`。</span><span class="sxs-lookup"><span data-stu-id="affc5-174">[Implicit cast operators](/dotnet/csharp/language-reference/keywords/implicit) support the conversion of both `T` and `ActionResult` to `ActionResult<T>`.</span></span> <span data-ttu-id="affc5-175">将 `T` 转换为 <xref:Microsoft.AspNetCore.Mvc.ObjectResult>，也就是将 `return new ObjectResult(T);` 简化为 `return T;`。</span><span class="sxs-lookup"><span data-stu-id="affc5-175">`T` converts to <xref:Microsoft.AspNetCore.Mvc.ObjectResult>, which means `return new ObjectResult(T);` is simplified to `return T;`.</span></span>

<span data-ttu-id="affc5-176">C# 不支持对接口使用隐式强制转换运算符。</span><span class="sxs-lookup"><span data-stu-id="affc5-176">C# doesn't support implicit cast operators on interfaces.</span></span> <span data-ttu-id="affc5-177">因此，必须使用 `ActionResult<T>`，才能将接口转换为具体类型。</span><span class="sxs-lookup"><span data-stu-id="affc5-177">Consequently, conversion of the interface to a concrete type is necessary to use `ActionResult<T>`.</span></span> <span data-ttu-id="affc5-178">例如，在下面的示例中，使用 `IEnumerable` 不起作用：</span><span class="sxs-lookup"><span data-stu-id="affc5-178">For example, use of `IEnumerable` in the following example doesn't work:</span></span>

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get() =>
    _repository.GetProducts();
```

<span data-ttu-id="affc5-179">上面代码的一种修复方法是返回 `_repository.GetProducts().ToList();`。</span><span class="sxs-lookup"><span data-stu-id="affc5-179">One option to fix the preceding code is to return `_repository.GetProducts().ToList();`.</span></span>

<span data-ttu-id="affc5-180">大多数操作具有特定返回类型。</span><span class="sxs-lookup"><span data-stu-id="affc5-180">Most actions have a specific return type.</span></span> <span data-ttu-id="affc5-181">执行操作期间可能出现意外情况，不返回特定类型就是其中之一。</span><span class="sxs-lookup"><span data-stu-id="affc5-181">Unexpected conditions can occur during action execution, in which case the specific type isn't returned.</span></span> <span data-ttu-id="affc5-182">例如，操作的输入参数可能无法通过模型验证。</span><span class="sxs-lookup"><span data-stu-id="affc5-182">For example, an action's input parameter may fail model validation.</span></span> <span data-ttu-id="affc5-183">在此情况下，通常会返回相应的 `ActionResult` 类型，而不是特定类型。</span><span class="sxs-lookup"><span data-stu-id="affc5-183">In such a case, it's common to return the appropriate `ActionResult` type instead of the specific type.</span></span>

### <a name="synchronous-action"></a><span data-ttu-id="affc5-184">同步操作</span><span class="sxs-lookup"><span data-stu-id="affc5-184">Synchronous action</span></span>

<span data-ttu-id="affc5-185">请参考以下同步操作，其中有两种可能的返回类型：</span><span class="sxs-lookup"><span data-stu-id="affc5-185">Consider a synchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetByIdActionResult&highlight=8,11)]

<span data-ttu-id="affc5-186">在上述操作中：</span><span class="sxs-lookup"><span data-stu-id="affc5-186">In the preceding action:</span></span>

* <span data-ttu-id="affc5-187">当产品不在数据库中时返回状态代码 404。</span><span class="sxs-lookup"><span data-stu-id="affc5-187">A 404 status code is returned when the product doesn't exist in the database.</span></span>
* <span data-ttu-id="affc5-188">如果产品确实存在，则返回状态代码 200 及相应的 `Product` 对象。</span><span class="sxs-lookup"><span data-stu-id="affc5-188">A 200 status code is returned with the corresponding `Product` object when the product does exist.</span></span> <span data-ttu-id="affc5-189">ASP.NET Core 2.1 之前，`return product;` 行必须是 `return Ok(product);`。</span><span class="sxs-lookup"><span data-stu-id="affc5-189">Before ASP.NET Core 2.1, the `return product;` line had to be `return Ok(product);`.</span></span>

### <a name="asynchronous-action"></a><span data-ttu-id="affc5-190">异步操作</span><span class="sxs-lookup"><span data-stu-id="affc5-190">Asynchronous action</span></span>

<span data-ttu-id="affc5-191">请参考以下异步操作，其中有两种可能的返回类型：</span><span class="sxs-lookup"><span data-stu-id="affc5-191">Consider an asynchronous action in which there are two possible return types:</span></span>

[!code-csharp[](../web-api/action-return-types/samples/2x/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsyncActionResult&highlight=9,14)]

<span data-ttu-id="affc5-192">在上述操作中：</span><span class="sxs-lookup"><span data-stu-id="affc5-192">In the preceding action:</span></span>

* <span data-ttu-id="affc5-193">在以下情况下，ASP.NET Core 运行时返回 400 状态代码 (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>)：</span><span class="sxs-lookup"><span data-stu-id="affc5-193">A 400 status code (<xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*>) is returned by the ASP.NET Core runtime when:</span></span>
  * <span data-ttu-id="affc5-194">已[`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)应用该属性，并且模型验证失败。</span><span class="sxs-lookup"><span data-stu-id="affc5-194">The [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) attribute has been applied and model validation fails.</span></span>
  * <span data-ttu-id="affc5-195">产品说明包含“XYZ 小组件”。</span><span class="sxs-lookup"><span data-stu-id="affc5-195">The product description contains "XYZ Widget".</span></span>
* <span data-ttu-id="affc5-196">在创建产品后，<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> 方法生成 201 状态代码。</span><span class="sxs-lookup"><span data-stu-id="affc5-196">A 201 status code is generated by the <xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*> method when a product is created.</span></span> <span data-ttu-id="affc5-197">在此代码路径中，将在响应正文中提供 `Product` 对象。</span><span class="sxs-lookup"><span data-stu-id="affc5-197">In this code path, the `Product` object is provided in the response body.</span></span> <span data-ttu-id="affc5-198">提供了包含新建产品 URL 的 `Location` 响应标头。</span><span class="sxs-lookup"><span data-stu-id="affc5-198">A `Location` response header containing the newly created product's URL is provided.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="affc5-199">其他资源</span><span class="sxs-lookup"><span data-stu-id="affc5-199">Additional resources</span></span>

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
