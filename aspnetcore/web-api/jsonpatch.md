---
title: ASP.NET Core Web API 中的 JSON 修补程序
author: rick-anderson
description: 了解如何处理 ASP.NET Core Web API 中的 JSON 修补程序请求。
ms.author: riande
ms.custom: mvc
ms.date: 11/01/2019
uid: web-api/jsonpatch
ms.openlocfilehash: cf1a00c1928652bf5210b2442087209e23b8868e
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78652950"
---
# <a name="jsonpatch-in-aspnet-core-web-api"></a><span data-ttu-id="b3af3-103">ASP.NET Core Web API 中的 JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="b3af3-103">JsonPatch in ASP.NET Core web API</span></span>

<span data-ttu-id="b3af3-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="b3af3-104">By [Tom Dykstra](https://github.com/tdykstra) and [Kirk Larkin](https://github.com/serpent5)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b3af3-105">本文介绍如何处理 ASP.NET Core Web API 中的 JSON 修补程序请求。</span><span class="sxs-lookup"><span data-stu-id="b3af3-105">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="package-installation"></a><span data-ttu-id="b3af3-106">包安装</span><span class="sxs-lookup"><span data-stu-id="b3af3-106">Package installation</span></span>

<span data-ttu-id="b3af3-107">使用 `Microsoft.AspNetCore.Mvc.NewtonsoftJson` 包实现了对 JsonPatch 的支持。</span><span class="sxs-lookup"><span data-stu-id="b3af3-107">Support for JsonPatch is enabled using the `Microsoft.AspNetCore.Mvc.NewtonsoftJson` package.</span></span> <span data-ttu-id="b3af3-108">要启用此功能，应用必须：</span><span class="sxs-lookup"><span data-stu-id="b3af3-108">To enable this feature, apps must:</span></span>

* <span data-ttu-id="b3af3-109">安装 [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="b3af3-109">Install the [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson/) NuGet package.</span></span>
* <span data-ttu-id="b3af3-110">将项目的 `Startup.ConfigureServices` 方法更新为包含对 `AddNewtonsoftJson` 的调用：</span><span class="sxs-lookup"><span data-stu-id="b3af3-110">Update the project's `Startup.ConfigureServices` method to include a call to `AddNewtonsoftJson`:</span></span>

  ```csharp
  services
      .AddControllersWithViews()
      .AddNewtonsoftJson();
  ```

<span data-ttu-id="b3af3-111">`AddNewtonsoftJson` 与 MVC 服务注册方法兼容：</span><span class="sxs-lookup"><span data-stu-id="b3af3-111">`AddNewtonsoftJson` is compatible with the MVC service registration methods:</span></span>

  * `AddRazorPages`
  * `AddControllersWithViews`
  * `AddControllers`

## <a name="jsonpatch-addnewtonsoftjson-and-systemtextjson"></a><span data-ttu-id="b3af3-112">JsonPatch、AddNewtonsoftJson 和 System.Text.Json</span><span class="sxs-lookup"><span data-stu-id="b3af3-112">JsonPatch, AddNewtonsoftJson, and System.Text.Json</span></span>
  
<span data-ttu-id="b3af3-113">`AddNewtonsoftJson` 替换了基于 `System.Text.Json` 的输入和输出格式化程序，该格式化程序用于设置所有 JSON 内容的格式。</span><span class="sxs-lookup"><span data-stu-id="b3af3-113">`AddNewtonsoftJson` replaces the `System.Text.Json` based input and output formatters used for formatting **all** JSON content.</span></span> <span data-ttu-id="b3af3-114">要使用 `JsonPatch` 添加对 `Newtonsoft.Json` 的支持，同时使其他格式化程序保持不变，请按如下所示更新项目的 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="b3af3-114">To add support for `JsonPatch` using `Newtonsoft.Json`, while leaving the other formatters unchanged, update the project's `Startup.ConfigureServices` as follows:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet)]

<span data-ttu-id="b3af3-115">上述代码需要对 [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) 的引用和以下 using 语句：</span><span class="sxs-lookup"><span data-stu-id="b3af3-115">The preceding code requires a reference to [Microsoft.AspNetCore.Mvc.NewtonsoftJson](https://nuget.org/packages/Microsoft.AspNetCore.Mvc.NewtonsoftJson) and the following using statements:</span></span>

[!code-csharp[](jsonpatch/samples/3.0/WebApp1/Startup.cs?name=snippet1)]

## <a name="patch-http-request-method"></a><span data-ttu-id="b3af3-116">PATCH HTTP 请求方法</span><span class="sxs-lookup"><span data-stu-id="b3af3-116">PATCH HTTP request method</span></span>

<span data-ttu-id="b3af3-117">PUT 和 [PATCH](https://tools.ietf.org/html/rfc5789) 方法用于更新现有资源。</span><span class="sxs-lookup"><span data-stu-id="b3af3-117">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="b3af3-118">它们之间的区别是，PUT 会替换整个资源，而PATCH 仅指定更改。</span><span class="sxs-lookup"><span data-stu-id="b3af3-118">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="b3af3-119">JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="b3af3-119">JSON Patch</span></span>

<span data-ttu-id="b3af3-120">[JSON 修补程序](https://tools.ietf.org/html/rfc6902)是一种格式，用于指定要应用于资源的更新。</span><span class="sxs-lookup"><span data-stu-id="b3af3-120">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="b3af3-121">JSON 修补程序文档有一个操作数组。</span><span class="sxs-lookup"><span data-stu-id="b3af3-121">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="b3af3-122">每个操作标识特定类型的更改，例如添加数组元素或替换属性值。</span><span class="sxs-lookup"><span data-stu-id="b3af3-122">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="b3af3-123">例如，以下 JSON 文档表示资源、资源的 JSON 修补程序文档和应用修补程序操作的结果。</span><span class="sxs-lookup"><span data-stu-id="b3af3-123">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="b3af3-124">资源示例</span><span class="sxs-lookup"><span data-stu-id="b3af3-124">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="b3af3-125">JSON 修补程序示例</span><span class="sxs-lookup"><span data-stu-id="b3af3-125">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="b3af3-126">在上述 JSON 中：</span><span class="sxs-lookup"><span data-stu-id="b3af3-126">In the preceding JSON:</span></span>

* <span data-ttu-id="b3af3-127">`op` 属性指示操作的类型。</span><span class="sxs-lookup"><span data-stu-id="b3af3-127">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="b3af3-128">`path` 属性指示要更新的元素。</span><span class="sxs-lookup"><span data-stu-id="b3af3-128">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="b3af3-129">`value` 属性提供新值。</span><span class="sxs-lookup"><span data-stu-id="b3af3-129">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="b3af3-130">修补程序之后的资源</span><span class="sxs-lookup"><span data-stu-id="b3af3-130">Resource after patch</span></span>

<span data-ttu-id="b3af3-131">下面是应用上述 JSON 修补程序文档后的资源：</span><span class="sxs-lookup"><span data-stu-id="b3af3-131">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="b3af3-132">通过将 JSON 修补程序文档应用于资源所做的更改是原子操作：如果列表中的任何操作失败，则不会应用列表中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="b3af3-132">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="b3af3-133">路径语法</span><span class="sxs-lookup"><span data-stu-id="b3af3-133">Path syntax</span></span>

<span data-ttu-id="b3af3-134">操作对象的[路径](https://tools.ietf.org/html/rfc6901)属性的级别之间有斜杠。</span><span class="sxs-lookup"><span data-stu-id="b3af3-134">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="b3af3-135">例如，`"/address/zipCode"` 。</span><span class="sxs-lookup"><span data-stu-id="b3af3-135">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="b3af3-136">使用从零开始的索引来指定数组元素。</span><span class="sxs-lookup"><span data-stu-id="b3af3-136">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="b3af3-137">`addresses` 数组的第一个元素将位于 `/addresses/0`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-137">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="b3af3-138">若要将 `add` 置于数组末尾，请使用连字符 (-)，而不是索引号：`/addresses/-`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-138">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="b3af3-139">操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-139">Operations</span></span>

<span data-ttu-id="b3af3-140">下表显示了 [JSON 修补程序规范](https://tools.ietf.org/html/rfc6902)中定义的支持操作：</span><span class="sxs-lookup"><span data-stu-id="b3af3-140">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="b3af3-141">Operation</span><span class="sxs-lookup"><span data-stu-id="b3af3-141">Operation</span></span>  | <span data-ttu-id="b3af3-142">说明</span><span class="sxs-lookup"><span data-stu-id="b3af3-142">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="b3af3-143">添加属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="b3af3-143">Add a property or array element.</span></span> <span data-ttu-id="b3af3-144">对于现有属性：设置值。</span><span class="sxs-lookup"><span data-stu-id="b3af3-144">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="b3af3-145">删除属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="b3af3-145">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="b3af3-146">与在相同位置后跟 `remove` 的 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="b3af3-146">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="b3af3-147">与从后跟 `remove` 的源到使用源中的值的目标的 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="b3af3-147">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="b3af3-148">与到使用源中的值的目标的 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="b3af3-148">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="b3af3-149">如果 `path` 处的值 = 提供的 `value`，则返回成功状态代码。</span><span class="sxs-lookup"><span data-stu-id="b3af3-149">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="b3af3-150">ASP.NET Core 中的 JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="b3af3-150">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="b3af3-151">[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 包中提供了 JSON 修补程序的 ASP.NET Core 实现。</span><span class="sxs-lookup"><span data-stu-id="b3af3-151">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="b3af3-152">操作方法代码</span><span class="sxs-lookup"><span data-stu-id="b3af3-152">Action method code</span></span>

<span data-ttu-id="b3af3-153">在 API 控制器中，JSON 修补程序的操作方法：</span><span class="sxs-lookup"><span data-stu-id="b3af3-153">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="b3af3-154">使用 `HttpPatch` 属性进行批注。</span><span class="sxs-lookup"><span data-stu-id="b3af3-154">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="b3af3-155">接受 `JsonPatchDocument<T>`，通常带有 `[FromBody]`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-155">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="b3af3-156">在修补程序文档上调用 `ApplyTo` 以应用更改。</span><span class="sxs-lookup"><span data-stu-id="b3af3-156">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="b3af3-157">下面是一个示例：</span><span class="sxs-lookup"><span data-stu-id="b3af3-157">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="b3af3-158">示例应用中的此代码适用于以下 `Customer` 模型。</span><span class="sxs-lookup"><span data-stu-id="b3af3-158">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="b3af3-159">示例操作方法：</span><span class="sxs-lookup"><span data-stu-id="b3af3-159">The sample action method:</span></span>

* <span data-ttu-id="b3af3-160">构造一个 `Customer`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-160">Constructs a `Customer`.</span></span>
* <span data-ttu-id="b3af3-161">应用修补程序。</span><span class="sxs-lookup"><span data-stu-id="b3af3-161">Applies the patch.</span></span>
* <span data-ttu-id="b3af3-162">在响应的正文中返回结果。</span><span class="sxs-lookup"><span data-stu-id="b3af3-162">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="b3af3-163">在实际应用中，该代码将从存储（如数据库）中检索数据并在应用修补程序后更新数据库。</span><span class="sxs-lookup"><span data-stu-id="b3af3-163">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="b3af3-164">模型状态</span><span class="sxs-lookup"><span data-stu-id="b3af3-164">Model state</span></span>

<span data-ttu-id="b3af3-165">上述操作方法示例调用将模型状态用作其参数之一的 `ApplyTo` 的重载。</span><span class="sxs-lookup"><span data-stu-id="b3af3-165">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="b3af3-166">使用此选项，可以在响应中收到错误消息。</span><span class="sxs-lookup"><span data-stu-id="b3af3-166">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="b3af3-167">以下示例显示了 `test` 操作的“400 错误请求”响应的正文：</span><span class="sxs-lookup"><span data-stu-id="b3af3-167">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="b3af3-168">动态对象</span><span class="sxs-lookup"><span data-stu-id="b3af3-168">Dynamic objects</span></span>

<span data-ttu-id="b3af3-169">以下操作方法示例演示如何将修补程序应用于动态对象。</span><span class="sxs-lookup"><span data-stu-id="b3af3-169">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="b3af3-170">添加操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-170">The add operation</span></span>

* <span data-ttu-id="b3af3-171">如果 `path` 指向数组元素：将新元素插入到 `path` 指定的元素之前。</span><span class="sxs-lookup"><span data-stu-id="b3af3-171">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="b3af3-172">如果 `path` 指向属性：设置属性值。</span><span class="sxs-lookup"><span data-stu-id="b3af3-172">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="b3af3-173">如果 `path` 指向不存在的位置：</span><span class="sxs-lookup"><span data-stu-id="b3af3-173">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="b3af3-174">如果要修补的资源是一个动态对象：添加属性。</span><span class="sxs-lookup"><span data-stu-id="b3af3-174">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="b3af3-175">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="b3af3-175">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="b3af3-176">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Order` 对象添加到 `Orders` 数组末尾。</span><span class="sxs-lookup"><span data-stu-id="b3af3-176">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="b3af3-177">删除操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-177">The remove operation</span></span>

* <span data-ttu-id="b3af3-178">如果 `path` 指向数组元素：删除元素。</span><span class="sxs-lookup"><span data-stu-id="b3af3-178">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="b3af3-179">如果 `path` 指向属性：</span><span class="sxs-lookup"><span data-stu-id="b3af3-179">If `path` points to a property:</span></span>
  * <span data-ttu-id="b3af3-180">如果要修补的资源是一个动态对象：删除属性。</span><span class="sxs-lookup"><span data-stu-id="b3af3-180">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="b3af3-181">如果要修补的资源是一个静态对象：</span><span class="sxs-lookup"><span data-stu-id="b3af3-181">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="b3af3-182">如果属性可以为 Null：将其设置为 null。</span><span class="sxs-lookup"><span data-stu-id="b3af3-182">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="b3af3-183">如果属性不可以为 Null，将其设置为 `default<T>`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-183">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="b3af3-184">以下示例修补程序文档将 `CustomerName` 设置为 null 并删除 `Orders[0]`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-184">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="b3af3-185">替换操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-185">The replace operation</span></span>

<span data-ttu-id="b3af3-186">此操作在功能上与后跟 `remove` 的 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="b3af3-186">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="b3af3-187">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Orders[0]` 替换为新的 `Order` 对象。</span><span class="sxs-lookup"><span data-stu-id="b3af3-187">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="b3af3-188">移动操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-188">The move operation</span></span>

* <span data-ttu-id="b3af3-189">如果 `path` 指向数组元素：将 `from` 元素复制到 `path` 元素的位置，然后对 `remove` 元素运行 `from` 操作。</span><span class="sxs-lookup"><span data-stu-id="b3af3-189">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="b3af3-190">如果 `path` 指向属性：将 `from` 属性的值复制到 `path` 属性，然后对 `remove` 属性运行 `from` 操作。</span><span class="sxs-lookup"><span data-stu-id="b3af3-190">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="b3af3-191">如果 `path` 指向不存在的属性：</span><span class="sxs-lookup"><span data-stu-id="b3af3-191">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="b3af3-192">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="b3af3-192">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="b3af3-193">如果要修补的资源是一个动态对象：将 `from` 属性复制到 `path` 指示的位置，然后对 `remove` 属性运行 `from` 操作。</span><span class="sxs-lookup"><span data-stu-id="b3af3-193">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="b3af3-194">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="b3af3-194">The following sample patch document:</span></span>

* <span data-ttu-id="b3af3-195">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-195">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="b3af3-196">将 `Orders[0].OrderName` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="b3af3-196">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="b3af3-197">将 `Orders[1]` 移动到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="b3af3-197">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="b3af3-198">复制操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-198">The copy operation</span></span>

<span data-ttu-id="b3af3-199">此操作在功能上与不包含最后 `move` 步骤的 `remove` 操作相同。</span><span class="sxs-lookup"><span data-stu-id="b3af3-199">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="b3af3-200">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="b3af3-200">The following sample patch document:</span></span>

* <span data-ttu-id="b3af3-201">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-201">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="b3af3-202">将 `Orders[1]` 的副本插入到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="b3af3-202">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="b3af3-203">测试操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-203">The test operation</span></span>

<span data-ttu-id="b3af3-204">如果 `path` 指示的位置处的值与 `value` 中提供的值不同，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="b3af3-204">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="b3af3-205">在这种情况下，整个 PATCH 请求会失败，即使修补程序文档中的所有其他操作均成功也是如此。</span><span class="sxs-lookup"><span data-stu-id="b3af3-205">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="b3af3-206">`test` 操作通常用于在发生并发冲突时阻止更新。</span><span class="sxs-lookup"><span data-stu-id="b3af3-206">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="b3af3-207">如果 `CustomerName` 的初始值是“John”，则以下示例修补程序文档不起任何作用，因为测试失败：</span><span class="sxs-lookup"><span data-stu-id="b3af3-207">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="b3af3-208">获取代码</span><span class="sxs-lookup"><span data-stu-id="b3af3-208">Get the code</span></span>

<span data-ttu-id="b3af3-209">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)。</span><span class="sxs-lookup"><span data-stu-id="b3af3-209">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="b3af3-210">（[下载方法](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="b3af3-210">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b3af3-211">若要测试此示例，请使用以下设置运行应用并发送 HTTP 请求：</span><span class="sxs-lookup"><span data-stu-id="b3af3-211">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="b3af3-212">URL：`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="b3af3-212">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="b3af3-213">HTTP 方法：`PATCH`</span><span class="sxs-lookup"><span data-stu-id="b3af3-213">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="b3af3-214">标头：`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="b3af3-214">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="b3af3-215">Body：从*json*项目文件夹复制并粘贴一个 json 修补文档示例。</span><span class="sxs-lookup"><span data-stu-id="b3af3-215">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3af3-216">其他资源</span><span class="sxs-lookup"><span data-stu-id="b3af3-216">Additional resources</span></span>

* [<span data-ttu-id="b3af3-217">IETF RFC 5789 PATCH 方法规范</span><span class="sxs-lookup"><span data-stu-id="b3af3-217">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="b3af3-218">IETF RFC 6902 JSON 修补程序规范</span><span class="sxs-lookup"><span data-stu-id="b3af3-218">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="b3af3-219">IETF RFC 6901 JSON 修补程序路径格式规范</span><span class="sxs-lookup"><span data-stu-id="b3af3-219">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="b3af3-220">[JSON 路径文档](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="b3af3-220">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="b3af3-221">包括指向用于创建 JSON 修补程序文档的资源的链接。</span><span class="sxs-lookup"><span data-stu-id="b3af3-221">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="b3af3-222">ASP.NET Core JSON 修补程序源代码</span><span class="sxs-lookup"><span data-stu-id="b3af3-222">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b3af3-223">本文介绍如何处理 ASP.NET Core Web API 中的 JSON 修补程序请求。</span><span class="sxs-lookup"><span data-stu-id="b3af3-223">This article explains how to handle JSON Patch requests in an ASP.NET Core web API.</span></span>

## <a name="patch-http-request-method"></a><span data-ttu-id="b3af3-224">PATCH HTTP 请求方法</span><span class="sxs-lookup"><span data-stu-id="b3af3-224">PATCH HTTP request method</span></span>

<span data-ttu-id="b3af3-225">PUT 和 [PATCH](https://tools.ietf.org/html/rfc5789) 方法用于更新现有资源。</span><span class="sxs-lookup"><span data-stu-id="b3af3-225">The PUT and [PATCH](https://tools.ietf.org/html/rfc5789) methods are used to update an existing resource.</span></span> <span data-ttu-id="b3af3-226">它们之间的区别是，PUT 会替换整个资源，而PATCH 仅指定更改。</span><span class="sxs-lookup"><span data-stu-id="b3af3-226">The difference between them is that PUT replaces the entire resource, while PATCH specifies only the changes.</span></span>

## <a name="json-patch"></a><span data-ttu-id="b3af3-227">JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="b3af3-227">JSON Patch</span></span>

<span data-ttu-id="b3af3-228">[JSON 修补程序](https://tools.ietf.org/html/rfc6902)是一种格式，用于指定要应用于资源的更新。</span><span class="sxs-lookup"><span data-stu-id="b3af3-228">[JSON Patch](https://tools.ietf.org/html/rfc6902) is a format for specifying updates to be applied to a resource.</span></span> <span data-ttu-id="b3af3-229">JSON 修补程序文档有一个操作数组。</span><span class="sxs-lookup"><span data-stu-id="b3af3-229">A JSON Patch document has an array of *operations*.</span></span> <span data-ttu-id="b3af3-230">每个操作标识特定类型的更改，例如添加数组元素或替换属性值。</span><span class="sxs-lookup"><span data-stu-id="b3af3-230">Each operation identifies a particular type of change, such as add an array element or replace a property value.</span></span>

<span data-ttu-id="b3af3-231">例如，以下 JSON 文档表示资源、资源的 JSON 修补程序文档和应用修补程序操作的结果。</span><span class="sxs-lookup"><span data-stu-id="b3af3-231">For example, the following JSON documents represent a resource, a JSON patch document for the resource, and the result of applying the patch operations.</span></span>

### <a name="resource-example"></a><span data-ttu-id="b3af3-232">资源示例</span><span class="sxs-lookup"><span data-stu-id="b3af3-232">Resource example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/customer.json)]

### <a name="json-patch-example"></a><span data-ttu-id="b3af3-233">JSON 修补程序示例</span><span class="sxs-lookup"><span data-stu-id="b3af3-233">JSON patch example</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

<span data-ttu-id="b3af3-234">在上述 JSON 中：</span><span class="sxs-lookup"><span data-stu-id="b3af3-234">In the preceding JSON:</span></span>

* <span data-ttu-id="b3af3-235">`op` 属性指示操作的类型。</span><span class="sxs-lookup"><span data-stu-id="b3af3-235">The `op` property indicates the type of operation.</span></span>
* <span data-ttu-id="b3af3-236">`path` 属性指示要更新的元素。</span><span class="sxs-lookup"><span data-stu-id="b3af3-236">The `path` property indicates the element to update.</span></span>
* <span data-ttu-id="b3af3-237">`value` 属性提供新值。</span><span class="sxs-lookup"><span data-stu-id="b3af3-237">The `value` property provides the new value.</span></span>

### <a name="resource-after-patch"></a><span data-ttu-id="b3af3-238">修补程序之后的资源</span><span class="sxs-lookup"><span data-stu-id="b3af3-238">Resource after patch</span></span>

<span data-ttu-id="b3af3-239">下面是应用上述 JSON 修补程序文档后的资源：</span><span class="sxs-lookup"><span data-stu-id="b3af3-239">Here's the resource after applying the preceding JSON Patch document:</span></span>

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

<span data-ttu-id="b3af3-240">通过将 JSON 修补程序文档应用于资源所做的更改是原子操作：如果列表中的任何操作失败，则不会应用列表中的任何操作。</span><span class="sxs-lookup"><span data-stu-id="b3af3-240">The changes made by applying a JSON Patch document to a resource are atomic: if any operation in the list fails, no operation in the list is applied.</span></span>

## <a name="path-syntax"></a><span data-ttu-id="b3af3-241">路径语法</span><span class="sxs-lookup"><span data-stu-id="b3af3-241">Path syntax</span></span>

<span data-ttu-id="b3af3-242">操作对象的[路径](https://tools.ietf.org/html/rfc6901)属性的级别之间有斜杠。</span><span class="sxs-lookup"><span data-stu-id="b3af3-242">The [path](https://tools.ietf.org/html/rfc6901) property of an operation object has slashes between levels.</span></span> <span data-ttu-id="b3af3-243">例如，`"/address/zipCode"` 。</span><span class="sxs-lookup"><span data-stu-id="b3af3-243">For example, `"/address/zipCode"`.</span></span>

<span data-ttu-id="b3af3-244">使用从零开始的索引来指定数组元素。</span><span class="sxs-lookup"><span data-stu-id="b3af3-244">Zero-based indexes are used to specify array elements.</span></span> <span data-ttu-id="b3af3-245">`addresses` 数组的第一个元素将位于 `/addresses/0`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-245">The first element of the `addresses` array would be at `/addresses/0`.</span></span> <span data-ttu-id="b3af3-246">若要将 `add` 置于数组末尾，请使用连字符 (-)，而不是索引号：`/addresses/-`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-246">To `add` to the end of an array, use a hyphen (-) rather than an index number: `/addresses/-`.</span></span>

### <a name="operations"></a><span data-ttu-id="b3af3-247">操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-247">Operations</span></span>

<span data-ttu-id="b3af3-248">下表显示了 [JSON 修补程序规范](https://tools.ietf.org/html/rfc6902)中定义的支持操作：</span><span class="sxs-lookup"><span data-stu-id="b3af3-248">The following table shows supported operations as defined in the [JSON Patch specification](https://tools.ietf.org/html/rfc6902):</span></span>

|<span data-ttu-id="b3af3-249">Operation</span><span class="sxs-lookup"><span data-stu-id="b3af3-249">Operation</span></span>  | <span data-ttu-id="b3af3-250">说明</span><span class="sxs-lookup"><span data-stu-id="b3af3-250">Notes</span></span> |
|-----------|--------------------------------|
| `add`     | <span data-ttu-id="b3af3-251">添加属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="b3af3-251">Add a property or array element.</span></span> <span data-ttu-id="b3af3-252">对于现有属性：设置值。</span><span class="sxs-lookup"><span data-stu-id="b3af3-252">For existing property: set value.</span></span>|
| `remove`  | <span data-ttu-id="b3af3-253">删除属性或数组元素。</span><span class="sxs-lookup"><span data-stu-id="b3af3-253">Remove a property or array element.</span></span> |
| `replace` | <span data-ttu-id="b3af3-254">与在相同位置后跟 `remove` 的 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="b3af3-254">Same as `remove` followed by `add` at same location.</span></span> |
| `move`    | <span data-ttu-id="b3af3-255">与从后跟 `remove` 的源到使用源中的值的目标的 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="b3af3-255">Same as `remove` from source followed by `add` to destination using value from source.</span></span> |
| `copy`    | <span data-ttu-id="b3af3-256">与到使用源中的值的目标的 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="b3af3-256">Same as `add` to destination using value from source.</span></span> |
| `test`    | <span data-ttu-id="b3af3-257">如果 `path` 处的值 = 提供的 `value`，则返回成功状态代码。</span><span class="sxs-lookup"><span data-stu-id="b3af3-257">Return success status code if value at `path` = provided `value`.</span></span>|

## <a name="jsonpatch-in-aspnet-core"></a><span data-ttu-id="b3af3-258">ASP.NET Core 中的 JSON 修补程序</span><span class="sxs-lookup"><span data-stu-id="b3af3-258">JsonPatch in ASP.NET Core</span></span>

<span data-ttu-id="b3af3-259">[Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet 包中提供了 JSON 修补程序的 ASP.NET Core 实现。</span><span class="sxs-lookup"><span data-stu-id="b3af3-259">The ASP.NET Core implementation of JSON Patch is provided in the [Microsoft.AspNetCore.JsonPatch](https://www.nuget.org/packages/microsoft.aspnetcore.jsonpatch/) NuGet package.</span></span> <span data-ttu-id="b3af3-260">该包包含在 [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) 元包中。</span><span class="sxs-lookup"><span data-stu-id="b3af3-260">The package is included in the [Microsoft.AspnetCore.App](xref:fundamentals/metapackage-app) metapackage.</span></span>

## <a name="action-method-code"></a><span data-ttu-id="b3af3-261">操作方法代码</span><span class="sxs-lookup"><span data-stu-id="b3af3-261">Action method code</span></span>

<span data-ttu-id="b3af3-262">在 API 控制器中，JSON 修补程序的操作方法：</span><span class="sxs-lookup"><span data-stu-id="b3af3-262">In an API controller, an action method for JSON Patch:</span></span>

* <span data-ttu-id="b3af3-263">使用 `HttpPatch` 属性进行批注。</span><span class="sxs-lookup"><span data-stu-id="b3af3-263">Is annotated with the `HttpPatch` attribute.</span></span>
* <span data-ttu-id="b3af3-264">接受 `JsonPatchDocument<T>`，通常带有 `[FromBody]`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-264">Accepts a `JsonPatchDocument<T>`, typically with `[FromBody]`.</span></span>
* <span data-ttu-id="b3af3-265">在修补程序文档上调用 `ApplyTo` 以应用更改。</span><span class="sxs-lookup"><span data-stu-id="b3af3-265">Calls `ApplyTo` on the patch document to apply the changes.</span></span>

<span data-ttu-id="b3af3-266">下面是一个示例：</span><span class="sxs-lookup"><span data-stu-id="b3af3-266">Here's an example:</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_PatchAction&highlight=1,3,9)]

<span data-ttu-id="b3af3-267">示例应用中的此代码适用于以下 `Customer` 模型。</span><span class="sxs-lookup"><span data-stu-id="b3af3-267">This code from the sample app works with the following `Customer` model.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Models/Customer.cs?name=snippet_Customer)]

[!code-csharp[](jsonpatch/samples/2.2/Models/Order.cs?name=snippet_Order)]

<span data-ttu-id="b3af3-268">示例操作方法：</span><span class="sxs-lookup"><span data-stu-id="b3af3-268">The sample action method:</span></span>

* <span data-ttu-id="b3af3-269">构造一个 `Customer`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-269">Constructs a `Customer`.</span></span>
* <span data-ttu-id="b3af3-270">应用修补程序。</span><span class="sxs-lookup"><span data-stu-id="b3af3-270">Applies the patch.</span></span>
* <span data-ttu-id="b3af3-271">在响应的正文中返回结果。</span><span class="sxs-lookup"><span data-stu-id="b3af3-271">Returns the result in the body of the response.</span></span>

 <span data-ttu-id="b3af3-272">在实际应用中，该代码将从存储（如数据库）中检索数据并在应用修补程序后更新数据库。</span><span class="sxs-lookup"><span data-stu-id="b3af3-272">In a real app, the code would retrieve the data from a store such as a database and update the database after applying the patch.</span></span>

### <a name="model-state"></a><span data-ttu-id="b3af3-273">模型状态</span><span class="sxs-lookup"><span data-stu-id="b3af3-273">Model state</span></span>

<span data-ttu-id="b3af3-274">上述操作方法示例调用将模型状态用作其参数之一的 `ApplyTo` 的重载。</span><span class="sxs-lookup"><span data-stu-id="b3af3-274">The preceding action method example calls an overload of `ApplyTo` that takes model state as one of its parameters.</span></span> <span data-ttu-id="b3af3-275">使用此选项，可以在响应中收到错误消息。</span><span class="sxs-lookup"><span data-stu-id="b3af3-275">With this option, you can get error messages in responses.</span></span> <span data-ttu-id="b3af3-276">以下示例显示了 `test` 操作的“400 错误请求”响应的正文：</span><span class="sxs-lookup"><span data-stu-id="b3af3-276">The following example shows the body of a 400 Bad Request response for a `test` operation:</span></span>

```json
{
    "Customer": [
        "The current value 'John' at path 'customerName' is not equal to the test value 'Nancy'."
    ]
}
```

### <a name="dynamic-objects"></a><span data-ttu-id="b3af3-277">动态对象</span><span class="sxs-lookup"><span data-stu-id="b3af3-277">Dynamic objects</span></span>

<span data-ttu-id="b3af3-278">以下操作方法示例演示如何将修补程序应用于动态对象。</span><span class="sxs-lookup"><span data-stu-id="b3af3-278">The following action method example shows how to apply a patch to a dynamic object.</span></span>

[!code-csharp[](jsonpatch/samples/2.2/Controllers/HomeController.cs?name=snippet_Dynamic)]

## <a name="the-add-operation"></a><span data-ttu-id="b3af3-279">添加操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-279">The add operation</span></span>

* <span data-ttu-id="b3af3-280">如果 `path` 指向数组元素：将新元素插入到 `path` 指定的元素之前。</span><span class="sxs-lookup"><span data-stu-id="b3af3-280">If `path` points to an array element: inserts new element before the one specified by `path`.</span></span>
* <span data-ttu-id="b3af3-281">如果 `path` 指向属性：设置属性值。</span><span class="sxs-lookup"><span data-stu-id="b3af3-281">If `path` points to a property: sets the property value.</span></span>
* <span data-ttu-id="b3af3-282">如果 `path` 指向不存在的位置：</span><span class="sxs-lookup"><span data-stu-id="b3af3-282">If `path` points to a nonexistent location:</span></span>
  * <span data-ttu-id="b3af3-283">如果要修补的资源是一个动态对象：添加属性。</span><span class="sxs-lookup"><span data-stu-id="b3af3-283">If the resource to patch is a dynamic object: adds a property.</span></span>
  * <span data-ttu-id="b3af3-284">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="b3af3-284">If the resource to patch is a static object: the request fails.</span></span>

<span data-ttu-id="b3af3-285">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Order` 对象添加到 `Orders` 数组末尾。</span><span class="sxs-lookup"><span data-stu-id="b3af3-285">The following sample patch document sets the value of `CustomerName` and adds an `Order` object to the end of the `Orders` array.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/add.json)]

## <a name="the-remove-operation"></a><span data-ttu-id="b3af3-286">删除操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-286">The remove operation</span></span>

* <span data-ttu-id="b3af3-287">如果 `path` 指向数组元素：删除元素。</span><span class="sxs-lookup"><span data-stu-id="b3af3-287">If `path` points to an array element: removes the element.</span></span>
* <span data-ttu-id="b3af3-288">如果 `path` 指向属性：</span><span class="sxs-lookup"><span data-stu-id="b3af3-288">If `path` points to a property:</span></span>
  * <span data-ttu-id="b3af3-289">如果要修补的资源是一个动态对象：删除属性。</span><span class="sxs-lookup"><span data-stu-id="b3af3-289">If resource to patch is a dynamic object: removes the property.</span></span>
  * <span data-ttu-id="b3af3-290">如果要修补的资源是一个静态对象：</span><span class="sxs-lookup"><span data-stu-id="b3af3-290">If resource to patch is a static object:</span></span>
    * <span data-ttu-id="b3af3-291">如果属性可以为 Null：将其设置为 null。</span><span class="sxs-lookup"><span data-stu-id="b3af3-291">If the property is nullable: sets it to null.</span></span>
    * <span data-ttu-id="b3af3-292">如果属性不可以为 Null，将其设置为 `default<T>`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-292">If the property is non-nullable, sets it to `default<T>`.</span></span>

<span data-ttu-id="b3af3-293">以下示例修补程序文档将 `CustomerName` 设置为 null 并删除 `Orders[0]`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-293">The following sample patch document sets `CustomerName` to null and deletes `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/remove.json)]

## <a name="the-replace-operation"></a><span data-ttu-id="b3af3-294">替换操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-294">The replace operation</span></span>

<span data-ttu-id="b3af3-295">此操作在功能上与后跟 `remove` 的 `add` 相同。</span><span class="sxs-lookup"><span data-stu-id="b3af3-295">This operation is functionally the same as a `remove` followed by an `add`.</span></span>

<span data-ttu-id="b3af3-296">以下示例修补程序文档设置 `CustomerName` 的值，并将 `Orders[0]` 替换为新的 `Order` 对象。</span><span class="sxs-lookup"><span data-stu-id="b3af3-296">The following sample patch document sets the value of `CustomerName` and replaces `Orders[0]`with a new `Order` object.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/replace.json)]

## <a name="the-move-operation"></a><span data-ttu-id="b3af3-297">移动操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-297">The move operation</span></span>

* <span data-ttu-id="b3af3-298">如果 `path` 指向数组元素：将 `from` 元素复制到 `path` 元素的位置，然后对 `remove` 元素运行 `from` 操作。</span><span class="sxs-lookup"><span data-stu-id="b3af3-298">If `path` points to an array element: copies `from` element to location of `path` element, then runs a `remove` operation on the `from` element.</span></span>
* <span data-ttu-id="b3af3-299">如果 `path` 指向属性：将 `from` 属性的值复制到 `path` 属性，然后对 `remove` 属性运行 `from` 操作。</span><span class="sxs-lookup"><span data-stu-id="b3af3-299">If `path` points to a property: copies value of `from` property to `path` property, then runs a `remove` operation on the `from` property.</span></span>
* <span data-ttu-id="b3af3-300">如果 `path` 指向不存在的属性：</span><span class="sxs-lookup"><span data-stu-id="b3af3-300">If `path` points to a nonexistent property:</span></span>
  * <span data-ttu-id="b3af3-301">如果要修补的资源是一个静态对象：请求失败。</span><span class="sxs-lookup"><span data-stu-id="b3af3-301">If the resource to patch is a static object: the request fails.</span></span>
  * <span data-ttu-id="b3af3-302">如果要修补的资源是一个动态对象：将 `from` 属性复制到 `path` 指示的位置，然后对 `remove` 属性运行 `from` 操作。</span><span class="sxs-lookup"><span data-stu-id="b3af3-302">If the resource to patch is a dynamic object: copies `from` property to location indicated by `path`, then runs a `remove` operation on the `from` property.</span></span>

<span data-ttu-id="b3af3-303">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="b3af3-303">The following sample patch document:</span></span>

* <span data-ttu-id="b3af3-304">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-304">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="b3af3-305">将 `Orders[0].OrderName` 设置为 null。</span><span class="sxs-lookup"><span data-stu-id="b3af3-305">Sets `Orders[0].OrderName` to null.</span></span>
* <span data-ttu-id="b3af3-306">将 `Orders[1]` 移动到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="b3af3-306">Moves `Orders[1]` to before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/move.json)]

## <a name="the-copy-operation"></a><span data-ttu-id="b3af3-307">复制操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-307">The copy operation</span></span>

<span data-ttu-id="b3af3-308">此操作在功能上与不包含最后 `move` 步骤的 `remove` 操作相同。</span><span class="sxs-lookup"><span data-stu-id="b3af3-308">This operation is functionally the same as a `move` operation without the final `remove` step.</span></span>

<span data-ttu-id="b3af3-309">以下示例修补程序文档：</span><span class="sxs-lookup"><span data-stu-id="b3af3-309">The following sample patch document:</span></span>

* <span data-ttu-id="b3af3-310">将 `Orders[0].OrderName` 的值复制到 `CustomerName`。</span><span class="sxs-lookup"><span data-stu-id="b3af3-310">Copies the value of `Orders[0].OrderName` to `CustomerName`.</span></span>
* <span data-ttu-id="b3af3-311">将 `Orders[1]` 的副本插入到 `Orders[0]` 之前。</span><span class="sxs-lookup"><span data-stu-id="b3af3-311">Inserts a copy of `Orders[1]` before `Orders[0]`.</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/copy.json)]

## <a name="the-test-operation"></a><span data-ttu-id="b3af3-312">测试操作</span><span class="sxs-lookup"><span data-stu-id="b3af3-312">The test operation</span></span>

<span data-ttu-id="b3af3-313">如果 `path` 指示的位置处的值与 `value` 中提供的值不同，则请求会失败。</span><span class="sxs-lookup"><span data-stu-id="b3af3-313">If the value at the location indicated by `path` is different from the value provided in `value`, the request fails.</span></span> <span data-ttu-id="b3af3-314">在这种情况下，整个 PATCH 请求会失败，即使修补程序文档中的所有其他操作均成功也是如此。</span><span class="sxs-lookup"><span data-stu-id="b3af3-314">In that case, the whole PATCH request fails even if all other operations in the patch document would otherwise succeed.</span></span>

<span data-ttu-id="b3af3-315">`test` 操作通常用于在发生并发冲突时阻止更新。</span><span class="sxs-lookup"><span data-stu-id="b3af3-315">The `test` operation is commonly used to prevent an update when there's a concurrency conflict.</span></span>

<span data-ttu-id="b3af3-316">如果 `CustomerName` 的初始值是“John”，则以下示例修补程序文档不起任何作用，因为测试失败：</span><span class="sxs-lookup"><span data-stu-id="b3af3-316">The following sample patch document has no effect if the initial value of `CustomerName` is "John", because the test fails:</span></span>

[!code-json[](jsonpatch/samples/2.2/JSON/test-fail.json)]

## <a name="get-the-code"></a><span data-ttu-id="b3af3-317">获取代码</span><span class="sxs-lookup"><span data-stu-id="b3af3-317">Get the code</span></span>

<span data-ttu-id="b3af3-318">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2)。</span><span class="sxs-lookup"><span data-stu-id="b3af3-318">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/web-api/jsonpatch/samples/2.2).</span></span> <span data-ttu-id="b3af3-319">（[下载方法](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="b3af3-319">([How to download](xref:index#how-to-download-a-sample)).</span></span>

<span data-ttu-id="b3af3-320">若要测试此示例，请使用以下设置运行应用并发送 HTTP 请求：</span><span class="sxs-lookup"><span data-stu-id="b3af3-320">To test the sample, run the app and send HTTP requests with the following settings:</span></span>

* <span data-ttu-id="b3af3-321">URL：`http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span><span class="sxs-lookup"><span data-stu-id="b3af3-321">URL: `http://localhost:{port}/jsonpatch/jsonpatchwithmodelstate`</span></span>
* <span data-ttu-id="b3af3-322">HTTP 方法：`PATCH`</span><span class="sxs-lookup"><span data-stu-id="b3af3-322">HTTP method: `PATCH`</span></span>
* <span data-ttu-id="b3af3-323">标头：`Content-Type: application/json-patch+json`</span><span class="sxs-lookup"><span data-stu-id="b3af3-323">Header: `Content-Type: application/json-patch+json`</span></span>
* <span data-ttu-id="b3af3-324">Body：从*json*项目文件夹复制并粘贴一个 json 修补文档示例。</span><span class="sxs-lookup"><span data-stu-id="b3af3-324">Body: Copy and paste one of the JSON patch document samples from the *JSON* project folder.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b3af3-325">其他资源</span><span class="sxs-lookup"><span data-stu-id="b3af3-325">Additional resources</span></span>

* [<span data-ttu-id="b3af3-326">IETF RFC 5789 PATCH 方法规范</span><span class="sxs-lookup"><span data-stu-id="b3af3-326">IETF RFC 5789 PATCH method specification</span></span>](https://tools.ietf.org/html/rfc5789)
* [<span data-ttu-id="b3af3-327">IETF RFC 6902 JSON 修补程序规范</span><span class="sxs-lookup"><span data-stu-id="b3af3-327">IETF RFC 6902 JSON Patch specification</span></span>](https://tools.ietf.org/html/rfc6902)
* [<span data-ttu-id="b3af3-328">IETF RFC 6901 JSON 修补程序路径格式规范</span><span class="sxs-lookup"><span data-stu-id="b3af3-328">IETF RFC 6901 JSON Patch path format spec</span></span>](https://tools.ietf.org/html/rfc6901)
* <span data-ttu-id="b3af3-329">[JSON 路径文档](https://jsonpatch.com/)。</span><span class="sxs-lookup"><span data-stu-id="b3af3-329">[JSON Patch documentation](https://jsonpatch.com/).</span></span> <span data-ttu-id="b3af3-330">包括指向用于创建 JSON 修补程序文档的资源的链接。</span><span class="sxs-lookup"><span data-stu-id="b3af3-330">Includes links to resources for creating JSON Patch documents.</span></span>
* [<span data-ttu-id="b3af3-331">ASP.NET Core JSON 修补程序源代码</span><span class="sxs-lookup"><span data-stu-id="b3af3-331">ASP.NET Core JSON Patch source code</span></span>](https://github.com/dotnet/AspNetCore/tree/master/src/Features/JsonPatch/src)

::: moniker-end
