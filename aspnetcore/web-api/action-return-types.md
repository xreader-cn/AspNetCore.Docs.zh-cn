---
title: ASP.NET Core Web API 中的控制器操作返回类型
author: scottaddie
description: 了解在 ASP.NET Core Web API 中使用各种控制器操作方法返回类型的相关信息。
ms.author: scaddie
ms.custom: mvc
ms.date: 01/04/2019
uid: web-api/action-return-types
ms.openlocfilehash: 98d70e0379d353cff98a6d7a13f2dd00eb4da206
ms.sourcegitcommit: 97d7a00bd39c83a8f6bccb9daa44130a509f75ce
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2019
ms.locfileid: "54098727"
---
# <a name="controller-action-return-types-in-aspnet-core-web-api"></a>ASP.NET Core Web API 中控制器操作的返回类型

作者：[Scott Addie](https://github.com/scottaddie)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/web-api/action-return-types/samples)（[如何下载](xref:index#how-to-download-a-sample)）

ASP.NET Core 提供以下 Web API 控制器操作返回类型选项：

::: moniker range=">= aspnetcore-2.1"

* [特定类型](#specific-type)
* [IActionResult](#iactionresult-type)
* [ActionResult\<T>](#actionresultt-type)

::: moniker-end

::: moniker range="<= aspnetcore-2.0"

* [特定类型](#specific-type)
* [IActionResult](#iactionresult-type)

::: moniker-end

本文档说明每种返回类型的最佳适用情况。

## <a name="specific-type"></a>特定类型

最简单的操作返回基元或复杂数据类型（如 `string` 或自定义对象类型）。 请参考以下操作，该操作返回自定义 `Product` 对象的集合：

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_Get)]

在执行操作期间无需防范已知条件，返回特定类型即可满足要求。 上述操作不接受任何参数，因此不需要参数约束验证。

当在操作中需要考虑已知条件时，将引入多个返回路径。 在此类情况下，通常会将 [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) 返回类型和基元或复杂返回类型混合。 要支持此类操作，必须使用 [IActionResult](#iactionresult-type) 或 [ActionResult\<T>](#actionresultt-type)。

## <a name="iactionresult-type"></a>IActionResult 类型

当操作中可能有多个 [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) 返回类型时，适合使用 [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) 返回类型。 `ActionResult` 类型表示多种 HTTP 状态代码。 属于此类别的一些常见返回类型包括：[BadRequestResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestresult) (400)、[NotFoundResult](/dotnet/api/microsoft.aspnetcore.mvc.notfoundresult) (404) 和 [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200)。

由于操作中有多个返回类型和路径，因此必须自由使用 [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute.-ctor) 特性。 此特性可针对 [Swagger](/aspnet/core/tutorials/web-api-help-pages-using-swagger) 等工具生成的 API 帮助页生成更多描述性响应详细信息。 `[ProducesResponseType]` 指示操作将返回的已知类型和 HTTP 状态代码。

### <a name="synchronous-action"></a>同步操作

请参考以下同步操作，其中有两种可能的返回类型：

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

在上述操作中，当 `id` 代表的产品不在基础数据存储中时，则返回 404 状态代码。 调用 [NotFound](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.notfound) 帮助程序方法作为 `return new NotFoundResult();` 的快捷方式。 如果产品确实存在，则返回代表有效负载的 `Product` 对象和状态代码 200。 调用 [Ok](/dotnet/api/microsoft.aspnetcore.mvc.controllerbase.ok) 帮助程序方法作为 `return new OkObjectResult(product);` 的快捷方式。

### <a name="asynchronous-action"></a>异步操作

请参考以下异步操作，其中有两种可能的返回类型：

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.Pre21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

在上述代码中：

* 如果项目说明包含“XYZ 小组件”，ASP.NET Core 运行时返回 400 状态代码 ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*))。
* 在产品创建后，[CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) 方法生成 201 状态代码。 在此代码路径中，返回的对象是 `Product`。

例如，以下模型指明请求必须包含 `Name` 和 `Description` 属性。 因此，如果没有在请求中提供 `Name` 和 `Description`，便会导致模型验证失败。

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.DataAccess/Models/Product.cs?name=snippet_ProductClass&highlight=5-6,8-9)]

::: moniker range=">= aspnetcore-2.1"

如果应用的是 ASP.NET Core 2.1 或更高版本中的 [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性，模型验证错误会导致 400 状态代码生成。 有关详细信息，请参阅[自动 HTTP 400 响应](xref:web-api/index#automatic-http-400-responses)。

## <a name="actionresultt-type"></a>ActionResult\<T> 类型

ASP.NET Core 2.1 引入了面向 Web API 控制器操作的 [ActionResult\<T>](/dotnet/api/microsoft.aspnetcore.mvc.actionresult-1) 返回类型。 它支持返回从 [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) 派生的类型或返回[特定类型](#specific-type)。 `ActionResult<T>` 通过 [IActionResult 类型](#iactionresult-type)可提供以下优势：

* 可排除 [[ProducesResponseType]](/dotnet/api/microsoft.aspnetcore.mvc.producesresponsetypeattribute) 特性的 `Type` 属性。 例如，`[ProducesResponseType(200, Type = typeof(Product))]` 可简化为 `[ProducesResponseType(200)]`。 此操作的预期返回类型改为根据 `ActionResult<T>` 中的 `T` 进行推断。
* [隐式强制转换运算符](/dotnet/csharp/language-reference/keywords/implicit)支持将 `T` 和 `ActionResult` 均转换为 `ActionResult<T>`。 将 `T` 转换为 [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult)，也就是将 `return new ObjectResult(T);` 简化为 `return T;`。

C# 不支持对接口使用隐式强制转换运算符。 因此，必须使用 `ActionResult<T>`，才能将接口转换为具体类型。 例如，在下面的示例中，使用 `IEnumerable` 不起作用：

```csharp
[HttpGet]
public ActionResult<IEnumerable<Product>> Get()
{
    return _repository.GetProducts();
}
```

上面代码的一种修复方法是返回 `_repository.GetProducts().ToList();`。

大多数操作具有特定返回类型。 执行操作期间可能出现意外情况，不返回特定类型就是其中之一。 例如，操作的输入参数可能无法通过模型验证。 在此情况下，通常会返回相应的 `ActionResult` 类型，而不是特定类型。

### <a name="synchronous-action"></a>同步操作

请参考以下同步操作，其中有两种可能的返回类型：

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_GetById&highlight=8,11)]

在上述代码中，当产品不在数据库中时则返回状态代码 404。 如果产品确实存在，则返回相应的 `Product` 对象。 ASP.NET Core 2.1 之前，`return product;` 行是 `return Ok(product);`。

> [!TIP]
> 从 ASP.NET Core 2.1 开始，使用 `[ApiController]` 特性修饰控制器类时，将启用操作参数绑定源推理。 与路由模板中的名称相匹配的参数名称将通过请求路由数据自动绑定。 因此，不会使用 [[FromRoute]](/dotnet/api/microsoft.aspnetcore.mvc.fromrouteattribute) 特性对上述操作中的 `id` 参数进行显示批注。

### <a name="asynchronous-action"></a>异步操作

请参考以下异步操作，其中有两种可能的返回类型：

[!code-csharp[](../web-api/action-return-types/samples/WebApiSample.Api.21/Controllers/ProductsController.cs?name=snippet_CreateAsync&highlight=8,13)]

在上述代码中：

* 在以下情况下，ASP.NET Core 运行时返回 400 状态代码 ([BadRequest](xref:Microsoft.AspNetCore.Mvc.ControllerBase.BadRequest*))：
  * 已应用 [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute) 属性，且模型验证失败。
  * 产品说明包含“XYZ 小组件”。
* 在产品创建后，[CreatedAtAction](xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*) 方法生成 201 状态代码。 在此代码路径中，返回的对象是 `Product`。

> [!TIP]
> 从 ASP.NET Core 2.1 开始，使用 `[ApiController]` 特性修饰控制器类时，将启用操作参数绑定源推理。 复杂类型参数通过请求正文自动绑定。 因此，不会使用 [[FromBody]](/dotnet/api/microsoft.aspnetcore.mvc.frombodyattribute) 特性对前面操作中的 `product` 参数进行显示批注。

::: moniker-end

## <a name="additional-resources"></a>其他资源

* <xref:mvc/controllers/actions>
* <xref:mvc/models/validation>
* <xref:tutorials/web-api-help-pages-using-swagger>
