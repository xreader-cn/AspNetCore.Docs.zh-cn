---
title: ASP.NET Core Web API 中的 JSON 修补程序
author: tdykstra
description: 了解如何处理 ASP.NET Core Web API 中的 JSON 修补程序请求。
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/24/2019
uid: web-api/jsonpatch
ms.openlocfilehash: 97264903d85dbb397e85fdbf7b070e2aaae74bc8
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67815541"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="9508c-103">ASP.NET Core Web API 中的 JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="9508c-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="9508c-104">作者：[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="9508c-104">By [Tom Dykstra](https://github.com/tdykstra)</span></span>

<span data-ttu-id="9508c-105">本文介绍如何处理 ASP.NET Core Web API 中的 JSON 修补程序请求。</span><span class="sxs-lookup"><span data-stu-id="9508c-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="9508c-106">PATCH HTTP 请求方法</span><span class="sxs-lookup"><span data-stu-id="9508c-106">PATCH HTTP request method</span></span>

<span data-ttu-id="9508c-107">PUT 和 [PATCH](https://tools.ietf.org/html/rfc5789) 方法用于更新现有资源。</span><span class="sxs-lookup"><span data-stu-id="9508c-107">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="9508c-108">它们之间的区别是，PUT 会替换整个资源，而PATCH 仅指定更改。</span><span class="sxs-lookup"><span data-stu-id="9508c-108">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="9508c-109">JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="9508c-109">JSON Patch</span></span>

<span data-ttu-id="9508c-110">[JSON 修补程序](https://tools.ietf.org/html/rfc6902)是一种格式，用于指定要应用于资源的更新。</span><span class="sxs-lookup"><span data-stu-id="9508c-110">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="9508c-111">JSON 修补程序文档有一个  操作数组。</span><span class="sxs-lookup"><span data-stu-id="9508c-111">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="9508c-112">每个操作标识特定类型的更改，例如添加数组元素或替换属性值。</span><span class="sxs-lookup"><span data-stu-id="9508c-112">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="9508c-113">例如，以下 JSON 文档表示资源、资源的 JSON 修补程序文档和应用修补程序操作的结果。</span><span class="sxs-lookup"><span data-stu-id="9508c-113">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="9508c-114">资源示例</span><span class="sxs-lookup"><span data-stu-id="9508c-114">Resource example</span></span>

[!code-csharp[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="9508c-115">JSON 修补程序示例</span><span class="sxs-lookup"><span data-stu-id="9508c-115">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="9508c-116">在上述 JSON 中：</span><span class="sxs-lookup"><span data-stu-id="9508c-116">In the preceding JSON:</span></span>

* <span data-ttu-id="9508c-117">`op` 属性指示操作的类型。</span><span class="sxs-lookup"><span data-stu-id="9508c-117">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="9508c-118">`path` 属性指示要更新的元素。</span><span class="sxs-lookup"><span data-stu-id="9508c-118">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="9508c-119">`value` 属性提供新值。</span><span class="sxs-lookup"><span data-stu-id="9508c-119">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="9508c-120">修补程序之后的资源</span><span class="sxs-lookup"><span data-stu-id="9508c-120">Resource after patch</span></span>

<span data-ttu-id="9508c-121">下面是应用上述 JSON 修补程序文档后的资源：</span><span class="sxs-lookup"><span data-stu-id="9508c-121">Here's the resource after applying the preceding JSON Patch document:</span></span>

```json
{
  "customerName": "Barry",
  "orders": [
    {
      "orderName": "Order0",
      "orderType": null
    },
    {
      "orderName": "Order1",
      "orderType": null
    },
    {
      "orderName": "Order2",
      "orderType": null
    }
  ]
}
```

<span data-ttu-id="9508c-122">通过将 JSON 修补程序文档应用于资源所做的更改是原子操作：如果列表中的任何操作失败，则不会应用列表中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="9508c-122">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="9508c-123">路径语法</span><span class="sxs-lookup"><span data-stu-id="9508c-123">Path syntax</span></span>

<span data-ttu-id="9508c-124">操作对象的[路径](https://tools.ietf.org/html/rfc6901)属性的级别之间有斜杠。</span><span class="sxs-lookup"><span data-stu-id="9508c-124">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="9508c-125">例如 `"/address/zipCode"`。</span><span class="sxs-lookup"><span data-stu-id="9508c-125">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="9508c-126">使用从零开始的索引来指定数组元素。</span><span class="sxs-lookup"><span data-stu-id="9508c-126">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="9508c-127">`addresses` 数组的第一个元素将位于 `/addresses/0`。</span><span class="sxs-lookup"><span data-stu-id="9508c-127">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="9508c-128">若要将 `add` 置于数组末尾，请使用连字符 (-)，而不是索引号：`/addresses/-`。</span><span class="sxs-lookup"><span data-stu-id="9508c-128">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="9508c-129">操作</span><span class="sxs-lookup"><span data-stu-id="9508c-129">Operations</span></span>

<span data-ttu-id="9508c-130">下表显示了 [JSON 修补程序规范](https://tools.ietf.org/html/rfc6902)中定义的支持操作：</span><span class="sxs-lookup"><span data-stu-id="9508c-130">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="9508c-131">操作</span><span class="sxs-lookup"><span data-stu-id="9508c-131">Operation</span></span>  | <span data-ttu-id="9508c-132">说明</span><span class="sxs-lookup"><span data-stu-id="9508c-132">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="9508c-133">添加属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="9508c-133">Add a property or array element.</span></span> <span data-ttu-id="9508c-134">对于现有属性：设置值。</span><span class="sxs-lookup"><span data-stu-id="9508c-134">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="9508c-135">删除属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="9508c-135">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="9508c-136">与在相同位置后跟 `add` 的 `remove` 相同。</span><span class="sxs-lookup"><span data-stu-id="9508c-136">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="9508c-137">与从后跟 `add` 的源到使用源中的值的目标的 `remove` 相同。</span><span class="sxs-lookup"><span data-stu-id="9508c-137">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="9508c-138">与到使用源中的值的目标的 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="9508c-138">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="9508c-139">如果 `path` 处的值 = 提供的 `value`，则返回成功状态代码。</span><span class="sxs-lookup"><span data-stu-id="9508c-139">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="9508c-140">ASP.NET Core 中的 JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="9508c-140">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="9508c-141">[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 包中提供了 JSON 修补程序的 ASP.NET Core 实现。</span><span class="sxs-lookup"><span data-stu-id="9508c-141">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="9508c-142">该包包含在 [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) 元包中。</span><span class="sxs-lookup"><span data-stu-id="9508c-142">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="9508c-143">操作方法代码</span><span class="sxs-lookup"><span data-stu-id="9508c-143">Action method code</span></span>

<span data-ttu-id="9508c-144">在 API 控制器中，JSON 修补程序的操作方法：</span><span class="sxs-lookup"><span data-stu-id="9508c-144">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="9508c-145">使用 `HttpPatch` 属性进行批注。</span><span class="sxs-lookup"><span data-stu-id="9508c-145">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="9508c-146">接受 `JsonPatchDocument<T>`，通常带有 [FromBody]。</span><span class="sxs-lookup"><span data-stu-id="9508c-146">Accepts a `JsonPatchDocument<T>`, typically with [FromBody].</span></span>
* <span data-ttu-id="9508c-147">在修补程序文档上调用 `ApplyTo` 以应用更改。</span><span class="sxs-lookup"><span data-stu-id="9508c-147">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="9508c-148">以下是一个示例：</span><span class="sxs-lookup"><span data-stu-id="9508c-148">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="9508c-149">示例应用中的此代码适用于以下 `Customer` 模型。</span><span class="sxs-lookup"><span data-stu-id="9508c-149">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="9508c-150">示例操作方法：</span><span class="sxs-lookup"><span data-stu-id="9508c-150">The sample action method:</span></span>

* <span data-ttu-id="9508c-151">构造一个 `Customer`。</span><span class="sxs-lookup"><span data-stu-id="9508c-151">Constructs a `Customer`.</span></span>
* <span data-ttu-id="9508c-152">应用修补程序。</span><span class="sxs-lookup"><span data-stu-id="9508c-152">Applies the patch.</span></span>
* <span data-ttu-id="9508c-153">在响应的正文中返回结果。</span><span class="sxs-lookup"><span data-stu-id="9508c-153">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="9508c-154">在实际应用中，该代码将从存储（如数据库）中检索数据并在应用修补程序后更新数据库。</span><span class="sxs-lookup"><span data-stu-id="9508c-154">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="9508c-155">模型状态</span><span class="sxs-lookup"><span data-stu-id="9508c-155">Model state</span></span>

<span data-ttu-id="9508c-156">上述操作方法示例调用将模型状态用作其参数之一的 `ApplyTo` 的重载。</span><span class="sxs-lookup"><span data-stu-id="9508c-156">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="9508c-157">使用此选项，可以在响应中收到错误消息。</span><span class="sxs-lookup"><span data-stu-id="9508c-157">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="9508c-158">以下示例显示了 `test` 操作的“400 错误请求”响应的正文：</span><span class="sxs-lookup"><span data-stu-id="9508c-158">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="9508c-159">动态对象</span><span class="sxs-lookup"><span data-stu-id="9508c-159">Dynamic objects</span></span>

<span data-ttu-id="9508c-160">以下操作方法示例演示如何将修补程序应用于动态对象。</span><span class="sxs-lookup"><span data-stu-id="9508c-160">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="9508c-161">添加操作</span><span class="sxs-lookup"><span data-stu-id="9508c-161">The add operation</span></span>

* <span data-ttu-id="9508c-162">如果 `path` 指向数组元素：将新元素插入到 `path` 指定的元素之前。</span><span class="sxs-lookup"><span data-stu-id="9508c-162">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="9508c-163">如果 `path` 指向属性：设置属性值。</span><span class="sxs-lookup"><span data-stu-id="9508c-163">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="9508c-164">如果 `path` 指向不存在的位置：</span><span class="sxs-lookup"><span data-stu-id="9508c-164">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="9508c-165">如果要修补的资源是一个动态对象：添加属性。</span><span class="sxs-lookup"><span data-stu-id="9508c-165">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="9508c-166">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="9508c-166">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="9508c-167">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Order` 对象添加到 `Orders` 数组末尾。</span><span class="sxs-lookup"><span data-stu-id="9508c-167">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="9508c-168">删除操作</span><span class="sxs-lookup"><span data-stu-id="9508c-168">The remove operation</span></span>

* <span data-ttu-id="9508c-169">如果 `path` 指向数组元素：删除元素。</span><span class="sxs-lookup"><span data-stu-id="9508c-169">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="9508c-170">如果 `path` 指向属性：</span><span class="sxs-lookup"><span data-stu-id="9508c-170">If `path` points to a property:</span></span>
  * <span data-ttu-id="9508c-171">如果要修补的资源是一个动态对象：删除属性。</span><span class="sxs-lookup"><span data-stu-id="9508c-171">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="9508c-172">如果要修补的资源是一个静态对象：</span><span class="sxs-lookup"><span data-stu-id="9508c-172">If resource to patch is a static object:</span></span> 
    * <span data-ttu-id="9508c-173">如果属性可以为 Null：将其设置为 null。</span><span class="sxs-lookup"><span data-stu-id="9508c-173">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="9508c-174">如果属性不可以为 Null，将其设置为 `default<T>`。</span><span class="sxs-lookup"><span data-stu-id="9508c-174">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="9508c-175">以下示例修补程序文档将 `CustomerName` 设置为 null 并删除 `Orders[0]`。</span><span class="sxs-lookup"><span data-stu-id="9508c-175">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="9508c-176">替换操作</span><span class="sxs-lookup"><span data-stu-id="9508c-176">The replace operation</span></span>

<span data-ttu-id="9508c-177">此操作在功能上与后跟 `add` 的 `remove` 相同。</span><span class="sxs-lookup"><span data-stu-id="9508c-177">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="9508c-178">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Orders[0]` 替换为新的 `Order` 对象。</span><span class="sxs-lookup"><span data-stu-id="9508c-178">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="9508c-179">移动操作</span><span class="sxs-lookup"><span data-stu-id="9508c-179">The move operation</span></span>

* <span data-ttu-id="9508c-180">如果 `path` 指向数组元素：将 `from` 元素复制到 `path` 元素的位置，然后对 `from` 元素运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="9508c-180">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="9508c-181">如果 `path` 指向属性：将 `from` 属性的值复制到 `path` 属性，然后对 `from` 属性运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="9508c-181">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="9508c-182">如果 `path` 指向不存在的属性：</span><span class="sxs-lookup"><span data-stu-id="9508c-182">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="9508c-183">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="9508c-183">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="9508c-184">如果要修补的资源是一个动态对象：将 `from` 属性复制到 `path` 指示的位置，然后对 `from` 属性运行 `remove` 操作。</span><span class="sxs-lookup"><span data-stu-id="9508c-184">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="9508c-185">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="9508c-185">The following sample patch document:</span></span>

* <span data-ttu-id="9508c-186">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="9508c-186">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="9508c-187">将 `Orders[0].OrderName` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="9508c-187">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="9508c-188">将 `Orders[1]` 移动到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="9508c-188">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="9508c-189">复制操作</span><span class="sxs-lookup"><span data-stu-id="9508c-189">The copy operation</span></span>

<span data-ttu-id="9508c-190">此操作在功能上与不包含最后 `remove` 步骤的 `move` 操作相同。</span><span class="sxs-lookup"><span data-stu-id="9508c-190">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="9508c-191">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="9508c-191">The following sample patch document:</span></span>

* <span data-ttu-id="9508c-192">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="9508c-192">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="9508c-193">将 `Orders[1]` 的副本插入到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="9508c-193">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="9508c-194">测试操作</span><span class="sxs-lookup"><span data-stu-id="9508c-194">The test operation</span></span>

<span data-ttu-id="9508c-195">如果 `path` 指示的位置处的值与 `value` 中提供的值不同，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="9508c-195">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="9508c-196">在这种情况下，整个 PATCH 请求会失败，即使修补程序文档中的所有其他操作均成功也是如此。</span><span class="sxs-lookup"><span data-stu-id="9508c-196">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="9508c-197">`test` 操作通常用于在发生并发冲突时阻止更新。</span><span class="sxs-lookup"><span data-stu-id="9508c-197">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="9508c-198">如果 `CustomerName` 的初始值是“John”，则以下示例修补程序文档不起任何作用，因为测试失败：</span><span class="sxs-lookup"><span data-stu-id="9508c-198">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="9508c-199">获取代码</span><span class="sxs-lookup"><span data-stu-id="9508c-199">Get the code</span></span>

<span data-ttu-id="9508c-200">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)。</span><span class="sxs-lookup"><span data-stu-id="9508c-200">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="9508c-201">（[下载方法](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="9508c-201">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="9508c-202">若要测试此示例，请使用以下设置运行应用并发送 HTTP 请求：</span><span class="sxs-lookup"><span data-stu-id="9508c-202">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="9508c-203">URL：`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="9508c-203">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="9508c-204">HTTP 方法：`PATCH`</span><span class="sxs-lookup"><span data-stu-id="9508c-204">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="9508c-205">标头：`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="9508c-205">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="9508c-206">正文：从 JSON  项目文件夹中复制并粘贴其中一个 JSON 修补程序文档示例。</span><span class="sxs-lookup"><span data-stu-id="9508c-206">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9508c-207">其他资源</span><span class="sxs-lookup"><span data-stu-id="9508c-207">Additional resources</span></span>

* [<span data-ttu-id="9508c-208">IETF RFC 5789 PATCH 方法规范</span><span class="sxs-lookup"><span data-stu-id="9508c-208">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="9508c-209">IETF RFC 6902 JSON 修补程序规范</span><span class="sxs-lookup"><span data-stu-id="9508c-209">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="9508c-210">IETF RFC 6901 JSON 修补程序路径格式规范</span><span class="sxs-lookup"><span data-stu-id="9508c-210">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="9508c-211">[JSON 路径文档](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="9508c-211">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="9508c-212">包括指向用于创建 JSON 修补程序文档的资源的链接。</span><span class="sxs-lookup"><span data-stu-id="9508c-212">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="9508c-213">ASP.NET Core JSON 修补程序源代码</span><span class="sxs-lookup"><span data-stu-id="9508c-213">ASP.NET Core JSON Patch source code</span></span>](https://github.com/aspnet/AspNetCore/tree/master/src/Features/JsonPatch/src)
