---
title: 在 ASP.NET Core 中路由到控制器操作
author: rick-anderson
description: 了解 ASP.NET Core MVC 如何使用路由中间件来匹配传入请求的 URL 并将它们映射到操作。
ms.author: riande
ms.date: 3/25/2020
uid: mvc/controllers/routing
ms.openlocfilehash: c63313ec060c5be368fcbd20edf5f0d557046d2e
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2020
ms.locfileid: "80977207"
---
# <a name="routing-to-controller-actions-in-aspnet-core"></a>在 ASP.NET Core 中路由到控制器操作

由[瑞安·诺瓦克](https://github.com/rynowak)，[柯克·拉金](https://twitter.com/serpent5)和[里克·安德森](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

ASP.NET核心控制器使用路由[中间件](xref:fundamentals/middleware/index)来匹配传入请求的 URL 并将其映射到[操作](#action)。  路由模板：

* 在启动代码或属性中定义。
* 描述 URL 路径如何与[操作](#action)匹配。
* 用于生成链接的 URL。 生成的链接通常在响应中返回。

操作是[常规路由](#cr)的，要么是[属性路由的](#ar)。 在控制器或[操作](#action)上放置路由可使其属性路由。 有关详细信息，请参阅[混合路由](#routing-mixed-ref-label)。

本文档：

* 解释 MVC 和路由之间的交互：
  * 典型的 MVC 应用如何使用路由功能。
  * 涵盖两者：
    * [常规路由](#cr)通常与控制器和视图一起使用。
    * 与 REST API 一起使用*的属性路由*。 如果您主要对 REST API 的路由感兴趣，请跳转到[REST API 部分的属性路由](#ar)。
  * 有关高级路由详细信息，请参阅[路由](xref:fundamentals/routing)。
* 指在 ASP.NET Core 3.0 中添加的默认路由系统，称为终结点路由。 出于兼容性目的，可以使用控制器与早期版本的路由。 有关说明，请参阅[2.2-3.0 迁移指南](xref:migration/22-to-30)。 有关旧路由系统的参考资料，请参阅[本文档的 2.2 版本](xref:mvc/controllers/routing?view=aspnetcore-2.2)。

<a name="cr"></a>

## <a name="set-up-conventional-route"></a>设置常规路线

`Startup.Configure`使用[常规路由](#crd)时，通常具有类似于以下内容的代码：

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet)]

调用 中<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>用于<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>创建单个路由。 单个路由名为`default`路由。 大多数具有控制器和视图的应用使用与`default`路由类似的路由模板。 REST API 应使用[属性路由](#ar)。

路由模板`"{controller=Home}/{action=Index}/{id?}"`：

* 匹配 URL 路径，如`/Products/Details/5`
* 通过标记路径来提取`{ controller = Products, action = Details, id = 5 }`路由值。 如果应用具有名为`ProductsController`控制器和`Details`操作的控制器，则路由值的提取将生成匹配：

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippetA)]

  [!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

* `/Products/Details/5`模型将 的值`id = 5`绑定到 将`id`参数设置为`5`。 有关详细信息，请参阅[模型绑定](xref:mvc/models/model-binding)。
* `{controller=Home}`定义为`Home`默认值`controller`。
* `{action=Index}`定义为`Index`默认值`action`。
*  中的`?``{id?}`字符`id`定义为可选。
  * 默认路由参数和可选路由参数不必包含在 URL 路径中进行匹配。 有关路由模板语法的详细说明，请参阅[路由模板参考](xref:fundamentals/routing#route-template-reference)。
* 匹配 URL`/`路径 。
* 生成路由值`{ controller = Home, action = Index }`。

的值`controller`和`action`默认值的使用。 `id`不会生成值，因为 URL 路径中没有相应的段。 `/`仅当存在 和`HomeController``Index`操作时匹配：

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

使用前面的控制器定义和路由模板，`HomeController.Index`操作将针对以下 URL 路径运行：

* `/Home/Index/17`
* `/Home/Index`
* `/Home`
* `/`

URL 路径`/`使用路由模板默认`Home`控制器和`Index`操作。 URL 路径`/Home`使用路由模板默认`Index`操作。

简便方法 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>：

```csharp
endpoints.MapDefaultControllerRoute();
```

取代：

```csharp
endpoints.MapControllerRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

路由使用<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting*>和<xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints*>中间件进行配置。 要使用控制器：

* 调用<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*>内部`UseEndpoints`以映射[属性路由](#ar)控制器。
* 调用<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>或 ，以映射[常规路由](#cr)的控制器。

<a name="routing-conventional-ref-label"></a>
<a name="crd"></a>

## <a name="conventional-routing"></a>传统路由

常规路由与控制器和视图一起使用。 `default` 路由：

[!code-csharp[](routing/samples/3.x/main/StartupDefaultMVC.cs?name=snippet2)]

是一种*传统路由*。 它被称为*传统路由*，因为它为 URL 路径建立了*约定*：

* 第一个路径段`{controller=Home}`映射到控制器名称。
* 第二个段`{action=Index}`， 映射到[操作](#action)名称。
* 第三个段`{id?}`用于可选`id`。 in`?``{id?}`使其可选。 `id`用于映射到模型实体。

使用此`default`路由，URL 路径：

* `/Products/List`映射到`ProductsController.List`操作。
* `/Blog/Article/17`映射到`BlogController.Article`和通常模型将`id`参数绑定到 17。

此映射：

* **仅**基于控制器和[操作](#action)名称。
* 不基于命名空间、源文件位置或方法参数。

使用常规路由与默认路由允许创建应用，而无需为每个操作提出新的 URL 模式。 对于具有[CRUD](https://wikipedia.org/wiki/Create,_read,_update_and_delete)样式操作的应用，具有跨控制器的 URL 的一致性：

* 有助于简化代码。
* 使 UI 更加可预测。

> [!WARNING]
> 前面的`id`代码中由工艺路线模板定义为可选。 操作可以在没有作为 URL 的一部分提供的可选 ID 的情况下执行。 通常，当`id`从 URL 中省略时：
>
> * `id`由模型绑定`0`设置为。
> * 在数据库匹配`id == 0`中找不到任何实体。
>
> [属性路由](#ar)提供细粒度控件，使某些操作不需要 ID，而不是其他操作的 ID。 按照惯例，文档包括可选参数，例如`id`它们可能以正确使用的方式出现。

大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。 默认传统路由 `{controller=Home}/{action=Index}/{id?}`：

* 支持基本的描述性路由方案。
* 是基于 UI 的应用的有用起点。
* 是许多 Web UI 应用所需的唯一路由模板。 对于较大的 Web UI 应用，使用["区域](#areas)"的另一个路由（如果经常是所有所需的内容）。

<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>和<xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*>：

* 根据调用其终结点的顺序自动为其终结点分配**订单**值。

ASP.NET Core 3.0 及更高版本中的终结点路由：

* 没有路线的概念。
* 不为执行扩展性提供命令保证，所有终结点都一次性处理。

启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。

[属性路由](#ar)将在本文中稍后介绍。

<a name="mr"></a>

### <a name="multiple-conventional-routes"></a>多条常规路线

通过向<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*>和 添加更多调用`UseEndpoints`，可以添加多个[常规路由。](#cr) <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> 这样做允许定义多个约定，或添加专用于特定[操作](#action)的传统路由，例如：

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

<a name="dcr"></a>

前面`blog`代码中的路由是**专用的常规路由**。 它被称为专用常规路由，因为：

* 它使用[传统的路由](#cr)。
* 它致力于一个具体[的行动](#action)。

因为`controller``action`并且不以参数显示在工艺路线`"blog/{*article}"`模板中：

* 它们只能具有默认值`{ controller = "Blog", action = "Article" }`。
* 这条路线总是映射到操作`BlogController.Article`。

`/Blog``/Blog/Article`，并且`/Blog/{any-string}`是唯一与博客路由匹配的 URL 路径。

前面的示例：

* `blog`路由的匹配优先级高于路由，`default`因为它首先添加。
* 是[Slug](https://developer.mozilla.org/docs/Glossary/Slug)样式路由的示例，其中通常将文章名称作为 URL 的一部分。

> [!WARNING]
> 在ASP.NET核心 3.0 及更高版本中，路由不会：
> * 定义一个称为*路由*的概念。 `UseRouting`将路由匹配添加到中间件管道。 中间`UseRouting`件查看应用中定义的终结点集，并根据请求选择最佳终结点匹配。
> * 提供有关扩展性执行顺序的保证，例如<xref:Microsoft.AspNetCore.Routing.IRouteConstraint>或<xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>。
>
>有关路由的参考资料，请参阅[工艺路线](xref:fundamentals/routing)。

<a name="cro"></a>

### <a name="conventional-routing-order"></a>常规路由顺序

常规路由仅与应用定义的操作和控制器的组合匹配。 这旨在简化传统路由重叠的情况。
使用<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllerRoute*><xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapDefaultControllerRoute*>添加路由，并根据<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>调用其终结点的顺序自动为其终结点分配订单值。 前面显示的路由的匹配具有更高的优先级。 传统路由依赖于顺序。 通常，具有区域的路由应更早放置，因为它们比没有区域的路由更具体。 具有 Catch 所有路由参数的`{*article}`[专用常规路由](#dcr)会使路由过于[贪婪](xref:fundamentals/routing#greedy)，这意味着它匹配了您打算与其他路由匹配的 URL。 稍后将贪婪路由放在路由表中，以防止贪婪匹配。

<a name="best"></a>

### <a name="resolving-ambiguous-actions"></a>解决模棱两可的操作

当两个终结点通过路由匹配时，路由必须执行以下操作之一：

* 选择最佳候选人。
* 引发异常。

例如：

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet9)]

前面的控制器定义了两个匹配的操作：

* URL 路径`/Products33/Edit/17`
* 路由数据`{ controller = Products33, action = Edit, id = 17 }`。

这是 MVC 控制器的典型模式：

* `Edit(int)`显示用于编辑产品的窗体。
* `Edit(int, Product)`处理过帐的表单。

要解析正确的路由：

* `Edit(int, Product)`当请求为 HTTP`POST`时，将选择 。
* `Edit(int)`当 HTTP[谓词](#verb)是任何其他内容时，将选中。 `Edit(int)`通常通过`GET`调用 。

提供<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute>`[HttpPost]`到路由，以便它可以根据请求的 HTTP 方法进行选择。 比 使比 更匹配`Edit(int)` `HttpPostAttribute` `Edit(int, Product)`

了解属性（如`HttpPostAttribute`）的作用非常重要。 其他[HTTP 谓词](#verb)定义了类似的属性。 [在常规路由](#cr)中，当操作是显示表单的一部分时，通常使用相同的操作名称，提交表单工作流。 例如，请参阅[检查两种编辑操作方法](xref:tutorials/first-mvc-app/controller-methods-views#get-post)。

如果路由无法选择最佳候选项，则引发<xref:System.Reflection.AmbiguousMatchException>，列出多个匹配的终结点。

<a name="routing-route-name-ref-label"></a>

### <a name="conventional-route-names"></a>常规路由名称

字符串`"blog"`和`"default"`以下示例中是常规路由名称：

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

路由名称为路由指定一个逻辑名称。 命名路由可用于 URL 生成。 当路由的顺序会使 URL 生成变得复杂时，使用命名路由可简化 URL 创建。 路由名称必须是唯一的应用程序宽。

路由名称：

* 对 URL 匹配或请求处理没有影响。
* 仅用于 URL 生成。

路由名称概念在路由中表示为[IEndpointName元数据](xref:Microsoft.AspNetCore.Routing.IEndpointNameMetadata)。 术语**路由名称**和**终结点名称**：

* 可互换。
* 在文档和代码中使用的哪个取决于描述的 API。

<a name="attribute-routing-ref-label"></a>
<a name="ar"></a>

## <a name="attribute-routing-for-rest-apis"></a>REST API 的属性路由

REST API 应使用属性路由将应用的功能建模为一组资源，其中操作由[HTTP 谓词](#verb)表示。

属性路由使用一组属性将操作直接映射到路由模板。 以下`StartUp.Configure`代码是 REST API 的典型代码，在下一个示例中使用：

[!code-csharp[](routing/samples/3.x/main/StartupApi.cs?name=snippet)]

在前面的代码中，<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapControllers*>在内部`UseEndpoints`调用映射属性路由控制器。

在以下示例中：

* 使用上述`Configure`方法。
* `HomeController`匹配一组类似于默认常规路由`{controller=Home}/{action=Index}/{id?}`匹配的 URL。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

操作`HomeController.Index`为任何 URL`/`路径`/Home`、或`/Home/Index``/Home/Index/3`运行。

此示例突出显示属性路由[和传统路由](#cr)之间的关键编程差异。 属性路由需要更多的输入来指定路由。 传统的默认路由处理路由更简洁。 但是，属性路由允许并且需要精确控制应用于每个[操作](#action)的路由模板。

使用属性路由时，控制器名称和操作名称在匹配操作时**不扮演任何**角色。 以下示例与上一示例匹配相同的 URL：

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

以下代码使用 和`action``controller`的令牌替换。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet22)]

以下代码适用于`[Route("[controller]/[action]")]`控制器：

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

在前面的代码中，`Index`方法模板必须准备`/`或`~/`到工艺路线模板。 应用于操作的以 `/` 或 `~/` 开头的路由模板不与应用于控制器的路由模板合并。

有关工艺路线模板选择的信息，请参阅[工艺路线模板优先级](xref:fundamentals/routing#rtp)。

## <a name="reserved-routing-names"></a>保留的路由名称

使用控制器或剃刀页时，以下关键字为保留路由参数名称：

* `action`
* `area`
* `controller`
* `handler`
* `page`

使用`page`作为具有属性路由的路由参数是一个常见的错误。 这样做会导致与 URL 生成不一致且令人困惑的行为。

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo2Controller.cs?name=snippet)]

URL 生成使用特殊参数名称来确定 URL 生成操作是引用 Razor 页还是控制器。

<a name="verb"></a>

## <a name="http-verb-templates"></a>HTTP 动词模板

ASP.NET核心具有以下 HTTP 谓词模板：

* [[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)
* [[HttpPost]](xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute)
* [[HttpPut]](xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute)
* [[HttpDelete]](xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute)
* [[HttpHead]](xref:Microsoft.AspNetCore.Mvc.HttpHeadAttribute)
* [[HttpPatch]](xref:Microsoft.AspNetCore.Mvc.HttpPatchAttribute)

<a name="rt"></a>

### <a name="route-templates"></a>路由模板

ASP.NET核心具有以下路由模板：

* 所有[HTTP 谓词模板](#verb)都是路由模板。
* [[路线]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)

<a name="arx"></a>

### <a name="attribute-routing-with-http-verb-attributes"></a>具有 Http 谓词属性的属性路由

请考虑以下控制器：

[!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet)]

在上述代码中：

* 每个操作都包含该`[HttpGet]`属性，该属性仅约束与 HTTP GET 请求的匹配。
* 该`GetProduct`操作包括`"{id}"`模板，因此`id`将追加到控制器上的`"api/[controller]"`模板。 方法模板为`"api/[controller]/"{id}""`。 因此，此操作仅匹配窗体`/api/test2/xyz`的`/api/test2/123`GET 请求，`/api/test2/{any string}`、 等。
  [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet2)]
* 该`GetIntProduct`操作包含`"int/{id:int}")`模板。 `:int`模板部分将`id`路由值约束到可以转换为整数的字符串。 到`/api/test2/int/abc`的 GET 请求：
  * 与此操作不匹配。
  * 返回[404 未找到](https://developer.mozilla.org/docs/Web/HTTP/Status/404)错误。
    [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet3)]
* 该`GetInt2Product`操作包含在`{id}`模板中，但不限制`id`为可转换为整数的值。 到`/api/test2/int2/abc`的 GET 请求：
  * 匹配此路由。
  * 模型绑定无法转换为`abc`整数。 该方法`id`的参数为整数。
  * 返回[400 错误请求](https://developer.mozilla.org/docs/Web/HTTP/Status/400)，因为模型绑定无法`abc`转换为整数。
      [!code-csharp[](routing/samples/3.x/main/Controllers/Test2Controller.cs?name=snippet4)]

属性路由可以使用<xref:Microsoft.AspNetCore.Mvc.Routing.HttpMethodAttribute>属性，如<xref:Microsoft.AspNetCore.Mvc.HttpPostAttribute> <xref:Microsoft.AspNetCore.Mvc.HttpPutAttribute>。<xref:Microsoft.AspNetCore.Mvc.HttpDeleteAttribute>和 。 所有[HTTP 谓词](#verb)属性都接受路由模板。 下面的示例显示了与同一路由模板匹配的两个操作：

[!code-csharp[](routing/samples/3.x/main/Controllers/MyProductsController.cs?name=snippet1)]

使用 URL`/products3`路径 ：

* 当`MyProductsController.ListProducts` [HTTP 谓词](#verb)为`GET`时，操作将运行。
* 当`MyProductsController.CreateProduct` [HTTP 谓词](#verb)为`POST`时，操作将运行。

构建 REST API 时，很少需要在操作方法上使用`[Route(...)]`，因为操作接受所有 HTTP 方法。 最好使用更具体的[HTTP 动词属性](#verb)来精确了解 API 支持的内容。 REST API 的客户端需要知道映射到特定逻辑操作的路径和 Http 谓词。

REST API 应使用属性路由将应用的功能建模为一组资源，其中操作由 HTTP 谓词表示。 这意味着，同一逻辑资源上的许多操作（例如，GET 和 POST）使用相同的 URL。 属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。

由于属性路由适用于特定操作，因此，使参数变成路由模板定义中的必需参数很简单。 在以下示例中，`id`作为 URL 路径的一部分是必需的：

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

操作`Products2ApiController.GetProduct(int)`：

* 使用 URL 路径运行，如`/products2/3`
* 不使用 URL 路径`/products2`运行。

使用 [[Consumes]](<xref:Microsoft.AspNetCore.Mvc.ConsumesAttribute>) 属性，操作可以限制支持的请求内容类型。 有关详细信息，请参阅使用[使用 属性定义受支持的请求内容类型](xref:web-api/index#consumes)。

 请参阅[路由](xref:fundamentals/routing)了解路由模板和相关选项的完整说明。

有关 的详细信息`[ApiController]`，请参阅[ApiController 属性](xref:web-api/index##apicontroller-attribute)。

## <a name="route-name"></a>路由名称

以下代码定义  的`Products_List`路由名称：

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet2)]

可以使用路由名称基于特定路由生成 URL。 路由名称：

* 对路由的 URL 匹配行为没有影响。
* 仅用于 URL 生成。

路由名称必须在应用程序范围内唯一。

将前面的代码与传统默认路由进行对比，后者将`id`参数定义为可选 （ 。`{id?}` 精确指定 API 的能力具有优势，例如允许`/products`并`/products/5`发送到不同的操作。

<a name="routing-combining-ref-label"></a>

## <a name="combining-attribute-routes"></a>组合属性路由

若要使属性路由减少重复，可将控制器上的路由属性与各个操作上的路由属性合并。 控制器上定义的所有路由模板均作为操作上路由模板的前缀。 在控制器上放置路由属性会使控制器中的**所有**操作都使用属性路由。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsApiController.cs?name=snippet)]

在上面的示例中：

* URL 路径`/products`可以匹配`ProductsApi.ListProducts`
* URL 路径`/products/5`可以匹配`ProductsApi.GetProduct(int)`。

这两个操作都仅与`GET`HTTP 匹配，因为它们被`[HttpGet]`标记为属性。

应用于操作的以 `/` 或 `~/` 开头的路由模板不与应用于控制器的路由模板合并。 下面的示例匹配一组类似于默认路由的 URL 路径。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet)]

下表解释了前面代码中`[Route]`的属性：

| 特性               | 与`[Route("Home")]` | 定义工艺路线模板 |
| ----------------- | ------------ | --------- |
| `[Route("")]` | 是 | `"Home"` |
| `[Route("Index")]` | 是 | `"Home/Index"` |
| `[Route("/")]` | **否** | `""` |
| `[Route("About")]` | 是 | `"Home/About"` |

<a name="routing-ordering-ref-label"></a>
<a name="oar"></a>

### <a name="attribute-route-order"></a>属性路由顺序

路由生成树并同时匹配所有终结点：

* 路由条目的行像放置在理想的顺序中一样。
* 最具体的路由有机会在更常规的路由之前执行。

例如，这样的`blog/search/{topic}`属性路由比 属性路由（如`blog/{*article}`）更具体。 默认情况下`blog/search/{topic}`，路由具有更高的优先级，因为它更具体。 使用[常规路由](#cr)，开发人员负责按所需顺序放置路由。

属性路由可以使用 属性<xref:Microsoft.AspNetCore.Mvc.RouteAttribute.Order>配置订单。 提供的所有框架[路由属性](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)都包括`Order`。 路由按 `Order` 属性的升序进行处理。 默认顺序为 `0`。 使用`Order = -1`未设置订单的路由之前使用运行设置路由。 在默认路由排序`Order = 1`后使用运行设置路由。

**避免**根据`Order`。 如果应用的 URL 空间需要显式顺序值才能正确路由，则客户端也可能感到困惑。 通常，属性路由选择具有 URL 匹配的正确路由。 如果用于 URL 生成的替代默认顺序不起作用，则使用路由名称作为重写通常比应用属性`Order`更简单。

请考虑以下两个控制器，它们都定义了路由匹配`/home`：

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet2)]

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemoController.cs?name=snippet)]

`/home`使用上述代码请求将引发类似于以下内容的异常：

```text
AmbiguousMatchException: The request matched multiple endpoints. Matches:

 WebMvcRouting.Controllers.HomeController.Index
 WebMvcRouting.Controllers.MyDemoController.MyIndex
```

添加到`Order`其中一个路由属性可解决歧义：

[!code-csharp[](routing/samples/3.x/main/Controllers/MyDemo3Controller.cs?name=snippet3& highlight=2)]

使用前面的代码，`/home`运行`HomeController.Index`终结点。 要访问 请求`MyDemoController.MyIndex` `/home/MyIndex`。 **注意：**

* 前面的代码是一个示例或糟糕的路由设计。 它被用来说明属性`Order`。
* 该`Order`属性仅解决歧义，该模板无法匹配。 最好删除`[Route("Home")]`模板。

请参阅[Razor 页面路由和应用约定：使用](xref:razor-pages/razor-pages-conventions#route-order)Razor 页面获取有关工艺路线顺序的信息的路由顺序。

在某些情况下，HTTP 500 错误返回与不明确的路由。 使用[日志记录](xref:fundamentals/logging/index)查看导致 的`AmbiguousMatchException`终结点。

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>路由模板中的令牌替换 [控制器]、[操作]、[区域]

为方便起见，属性路由通过将令牌包装在以下其中一个来支持保留路由参数的令牌替换：

* 方形大括号：`[]`
* 大括号：`{}`

标记`[action]`、`[area]`和`[controller]`替换为操作名称、区域名称和控制器名称的值，这些值来自定义路由的操作：

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet)]

在上述代码中：

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet10)]

  * 比赛`/Products0/List`

  [!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet11)]

  * 比赛`/Products0/Edit/{id}`

标记替换发生在属性路由生成的最后一步。 前面的示例的行为与以下代码相同：

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet20)]

[!INCLUDE[](~/includes/MTcomments.md)]

属性路由还可以与继承结合使用。 这是强大的结合令牌更换。 标记替换也适用于属性路由定义的路由名称。
`[Route("[controller]/[action]", Name="[controller]_[action]")]`为每个操作生成唯一的路由名称：

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet5)]

标记替换也适用于属性路由定义的路由名称。
`[Route("[controller]/[action]", Name="[controller]_[action]")]`
 为每项操作生成一个唯一的路由名称。

若要匹配文本标记替换分隔符 `[` 或 `]`，可通过重复该字符（`[[` 或 `]]`）对其进行转义。

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>使用参数转换程序自定义标记替换

使用参数转换程序可以自定义标记替换。 参数转换程序实现 <xref:Microsoft.AspNetCore.Routing.IOutboundParameterTransformer> 并转换参数值。 例如，自定义`SlugifyParameterTransformer`参数转换器将`SubscriptionManagement`路由值更改为`subscription-management`：

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet2)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention> 是应用程序模型约定，可以：

* 将参数转换程序应用到应用程序中的所有属性路由。
* 在替换属性路由标记值时对其进行自定义。

[!code-csharp[](routing/samples/3.x/main/Controllers/SubscriptionManagementController.cs?name=snippet)]

前面的`ListAll`方法匹配`/subscription-management/list-all`。

`RouteTokenTransformerConvention` 在 `ConfigureServices` 中注册为选项。

[!code-csharp[](routing/samples/3.x/main/StartupSlugifyParamTransformer.cs?name=snippet)]

有关 Slug 的定义，请参阅[Slug 上的 MDN Web 文档](https://developer.mozilla.org/docs/Glossary/Slug)。

<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-attribute-routes"></a>多个属性路由

属性路由支持定义多个访问同一操作的路由。 此操作最常用于模拟默认传统路由的行为，如以下示例所示：

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6x)]

在控制器上放置多个路由属性意味着每个路由属性都与操作方法上的每个路由属性结合在一起：

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet6)]

所有[HTTP 谓词](#verb)路由`IActionConstraint`约束实现 。

将实现<xref:Microsoft.AspNetCore.Mvc.ActionConstraints.IActionConstraint>实现的多个路由属性放置在操作上时：

* 每个操作约束与应用于控制器的路由模板结合使用。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet7)]

在操作上使用多个路由可能看起来有用且功能强大，最好保持应用的 URL 空间基本且定义良好。 仅在需要时**对**操作使用多个路由，例如，支持现有客户端。

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>指定属性路由的可选参数、默认值和约束

属性路由支持使用与传统路由相同的内联语法，来指定可选参数、默认值和约束。

[!code-csharp[](routing/samples/3.x/main/Controllers/ProductsController.cs?name=snippet8&highlight=3)]

在前面的代码中，`[HttpPost("product/{id:int}")]`应用路由约束。 操作`ProductsController.ShowProduct`仅由 URL 路径（如`/product/3`）匹配。 工艺路线模板部分`{id:int}`仅将该段限制为整数。

[!code-csharp[](routing/samples/3.x/main/Controllers/HomeController.cs?name=snippet24)]

有关路由模板语法的详细说明，请参阅[路由模板参考](xref:fundamentals/routing#route-template-reference)。

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>使用 IRouteTemplate 提供程序的自定义路由属性

所有[路由属性](#rt)实现<xref:Microsoft.AspNetCore.Mvc.Routing.IRouteTemplateProvider>。 ASP.NET核心运行时：

* 在应用启动时查找控制器类和操作方法的属性。
* 使用实现`IRouteTemplateProvider`的属性生成初始路由集。

实现`IRouteTemplateProvider`定义自定义路由属性。 每个 `IRouteTemplateProvider` 都允许定义一个包含自定义路由模板、顺序和名称的路由：

[!code-csharp[](routing/samples/3.x/main/Controllers/MyTestApiController.cs?name=snippet&highlight=1-10)]

前面的`Get`方法返回`Order = 2, Template = api/MyTestApi`。

<a name="routing-app-model-ref-label"></a>

### <a name="use-application-model-to-customize-attribute-routes"></a>使用应用程序模型自定义属性路由

应用程序模型：

* 是在启动时创建的对象模型。
* 包含 ASP.NET 核心用于路由和执行应用中操作的所有元数据。

应用程序模型包括从路由属性收集的所有数据。 路由属性中的数据由`IRouteTemplateProvider`实现提供。 约定：

* 可以编写以修改应用程序模型以自定义路由的行为。
* 在应用启动时读取。

本节显示了使用应用程序模型自定义路由的基本示例。 以下代码使路由大致与项目的文件夹结构一致。

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet)]

以下代码可防止将`namespace`约定应用于属性路由的控制器：

[!code-csharp[](routing/samples/3.x/nsrc/NamespaceRoutingConvention.cs?name=snippet2)]

例如，以下控制器不使用`NamespaceRoutingConvention`：

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/ManagersController.cs?name=snippet&highlight=1)]

`NamespaceRoutingConvention.Apply` 方法：

* 如果控制器是属性路由的，则不执行任何操作。
* 根据 设置控制器模板，`namespace`删除底座。 `namespace`

`NamespaceRoutingConvention`可应用于`Startup.ConfigureServices`：

[!code-csharp[](routing/samples/3.x/nsrc/Startup.cs?name=snippet&highlight=1,14-18)]

例如，请考虑以下控制器：

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/UsersController.cs)]

在上述代码中：

* 基地`namespace`为`My.Application`。
* 前面的控制器的全名是`My.Application.Admin.Controllers.UsersController`。
* 将`NamespaceRoutingConvention`控制器模板设置到`Admin/Controllers/Users/[action]/{id?`。

`NamespaceRoutingConvention`也可以作为属性应用于控制器：

[!code-csharp[](routing/samples/3.x/nsrc/Controllers/TestController.cs?name=snippet&highlight=1)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>混合路由：属性路由与传统路由

ASP.NET核心应用可以混合使用传统的路由和属性路由。 通常将传统路由用于为浏览器处理 HTML 页面的控制器，将属性路由用于处理 REST API 的控制器。

操作既支持传统路由，也支持属性路由。 通过在控制器或操作上放置路由可实现属性路由。 不能通过传统路由访问定义属性路由的操作，反之亦然。 控制器上**的任何**路由**属性使控制器**属性中的所有操作都路由。

属性路由和传统路由使用相同的路由引擎。

<a name="routing-url-gen-ref-label"></a>
<a name="ambient"></a>

## <a name="url-generation-and-ambient-values"></a>URL 生成和环境值

应用可以使用路由 URL 生成功能生成指向操作的 URL 链接。 生成 URL 消除了硬编码 URL，使代码更加可靠且可维护。 本节重点介绍 MVC 提供的 URL 生成功能，仅介绍 URL 生成工作原理的基础知识。 有关 URL 生成的详细说明，请参阅[路由](xref:fundamentals/routing)。

该<xref:Microsoft.AspNetCore.Mvc.IUrlHelper>接口是 MVC 和 URL 生成路由之间的基础结构的基础元素。 中的`IUrlHelper`实例可通过控制器、视图和`Url`视图组件中的属性提供。

在下面的示例中，`IUrlHelper`接口通过`Controller.Url`属性生成另一个操作的 URL。

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

如果应用使用默认的常规路由，`url`则变量的值是 URL 路径字符串`/UrlGeneration/Destination`。 此 URL 路径是通过合并路由创建的：

* 当前请求的路由值，称为**环境值**。
* 传递给`Url.Action`这些值并将这些值替换到工艺路线模板中的值：

``` text
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

路由模板中的每个路由参数都会通过将名称与这些值和环境值匹配，来替换掉原来的值。 没有值的路由参数可以：

* 如果默认值有默认值，请使用默认值。
* 如果可选，则跳过。 例如，`id`从路由模板`{controller}/{action}/{id?}`。

如果任何必需的路由参数没有相应的值，则 URL 生成将失败。 如果某个路由的 URL 生成失败，则尝试下一个路由，直到尝试所有路由或找到匹配项为止。

前面的假设`Url.Action`[路由](#cr)示例。 URL 生成与[属性路由](#ar)类似，尽管概念不同。 使用传统路由：

* 工艺路线值用于展开模板。
* 和`action`通常出现在`controller`该模板中的路由值。 这之所以有效，是因为路由匹配的 URL 符合约定。

以下示例使用属性路由：

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGenerationAttrController.cs?name=snippet_1)]

前面的`Source`代码中的操作将生成`custom/url/to/destination`。

<xref:Microsoft.AspNetCore.Routing.LinkGenerator>在 ASP.NET 酷 3.0 中添加`IUrlHelper`，作为 的替代方法。 `LinkGenerator`提供类似但更灵活的功能。 上`IUrlHelper`的每种方法都有相应的方法`LinkGenerator`系列。

### <a name="generating-urls-by-action-name"></a>根据操作名称生成 URL

[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*) [Url.action，LinkGenerator.GetPathByAction，](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*)以及所有相关重载都旨在通过指定控制器名称和操作名称来生成目标终结点。

使用`Url.Action`时，的当前路由值`controller``action`由 运行时提供：

* 的值`controller``action`是[环境值和值](#ambient)的一部分。 该方法`Url.Action`始终使用 和`action``controller`的当前值，并生成路由到当前操作的 URL 路径。

路由尝试使用环境值中的值来填充生成 URL 时未提供的信息。 考虑环境值`{ a = Alice, b = Bob, c = Carol, d = David }`这样的`{a}/{b}/{c}/{d}`路由：

* 路由有足够的信息来生成 URL，而不需要任何其他值。
* 路由具有足够的信息，因为所有路由参数都有一个值。

如果添加该`{ d = Donovan }`值：

* 该值`{ d = David }`将被忽略。
* 生成的 URL 路径`Alice/Bob/Carol/Donovan`为 。

**警告**： URL 路径是分层的。 在前面的示例中，如果添加该值`{ c = Cheryl }`：

* 将忽略这两`{ c = Carol, d = David }`个值。
* 不再有值`d`，URL 生成失败。
* 必须指定 的`c``d`所需值才能生成 URL。  

您可能期望使用默认路由`{controller}/{action}/{id?}`遇到此问题。 此问题在实践中很少见，因为`Url.Action`始终显式指定 和`controller``action`值。

[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)的多个重载采用路由值对象来为 路由参数提供 和`controller``action`以外的值。 路由值对象经常与 一起使用`id`。 例如，`Url.Action("Buy", "Products", new { id = 17 })` 。 路由值对象：

* 根据约定，通常是匿名类型的对象。
* 可以是 POCO`IDictionary<>`或[POCO。](https://wikipedia.org/wiki/Plain_old_CLR_object)

任何与路由参数不匹配的附加路由值都放在查询字符串中。

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet)]

前面的代码生成`/Products/Buy/17?color=red`。

以下代码生成绝对 URL：

[!code-csharp[](routing/samples/3.x/main/Controllers/TestController.cs?name=snippet2)]

要创建绝对 URL，请使用以下 URL 之一：

* 接受 的`protocol`重载。 例如，前面的代码。
* [链接生成器.GetUriByAction](xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*)，默认情况下生成绝对 URI。

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generate-urls-by-route"></a>按路由生成 URL

前面的代码演示通过传入控制器和操作名称来生成 URL。 `IUrlHelper`还提供[Url.RouteUrl](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.RouteUrl*)方法系列。 这些方法类似于[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)，但它们不会将 和`action``controller`的当前值复制到路由值。 最常见的用法`Url.RouteUrl`：

* 指定要生成 URL 的路由名称。
* 通常不指定控制器或操作名称。

[!code-csharp[](routing/samples/3.x/main/Controllers/UrlGeneration2Controller.cs?name=snippet_1)]

以下 Razor 文件生成指向 的`Destination_Route`HTML 链接：

[!code-cshtml[](routing/samples/3.x/main/Views/Shared/MyLink.cshtml)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generate-urls-in-html-and-razor"></a>在 HTML 和 Razor 中生成 URL

<xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper>提供了分别<xref:Microsoft.AspNetCore.Mvc.ViewFeatures.HtmlHelper>`<form>`生成`<a>`[的方法 Html.BeginForm](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.BeginForm*)和[Html.ActionLink。](xref:Microsoft.AspNetCore.Mvc.Rendering.IHtmlHelper.ActionLink*) 这些方法使用[Url.Action](xref:Microsoft.AspNetCore.Mvc.IUrlHelper.Action*)方法生成 URL，并且它们接受类似的参数。 `HtmlHelper` 的配套 `Url.RouteUrl` 为 `Html.BeginRouteForm` 和 `Html.RouteLink`，两者具有相似的功能。

TagHelper 通过 `form` TagHelper 和 `<a>` TagHelper 生成 URL。 两者均通过 `IUrlHelper` 来实现。 有关详细信息[，请参阅窗体中标记帮助人员](xref:mvc/views/working-with-forms)。

在视图内，可通过 `Url` 属性将 `IUrlHelper` 用于前文未涵盖的任何临时 URL 生成。

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="url-generation-in-action-results"></a>操作结果中的 URL 生成

前面的示例显示了`IUrlHelper`在控制器中使用。 控制器中最常见的用法是生成 URL 作为操作结果的一部分。

<xref:Microsoft.AspNetCore.Mvc.ControllerBase> 和 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类为操作结果提供简便的方法来引用另一项操作。 一个典型的用法是在接受用户输入后重定向：

[!code-csharp[](routing/samples/3.x/main/Controllers/CustomerController.cs?name=snippet)]

操作结果工厂方法，如<xref:Microsoft.AspNetCore.Mvc.ControllerBase.RedirectToAction*>和<xref:Microsoft.AspNetCore.Mvc.ControllerBase.CreatedAtAction*>遵循与 上`IUrlHelper`的方法类似的模式。

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>专用传统路由的特殊情况

[常规路由](#cr)可以使用一种称为[专用常规路由](#dcr)的特殊路由定义。 在下面的示例中，命名的`blog`路由是专用的常规路由：

[!code-csharp[](routing/samples/3.x/main/Startup.cs?name=snippet_1)]

使用前面的路由定义，`Url.Action("Index", "Home")`使用`/``default`路由生成 URL 路径，但为什么？ 用户可能认为使用 `blog`，路由值 `{ controller = Home, action = Index }` 就足以生成 URL，且结果为 `/blog?action=Index&controller=Home`。

[专用常规路由](#dcr)依赖于默认值的特殊行为，这些默认值没有相应的路由参数，以防止路由在 URL 生成时过于[贪婪](xref:fundamentals/routing#greedy)。 在此例中，默认值是为 `{ controller = Blog, action = Article }`，`controller` 和 `action` 均未显示为路由参数。 当路由执行 URL 生成时，提供的值必须与默认值匹配。 使用`blog`URL 生成失败，`{ controller = Home, action = Index }`因为值不匹配`{ controller = Blog, action = Article }`。 然后，路由回退，尝试使用 `default`，并最终成功。

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>区域

[区域](xref:mvc/controllers/areas)是 MVC 功能，用于将相关功能组织到组中作为单独的功能：

* 为控制器操作路由命名空间。
* 视图的文件夹结构。

使用区域允许应用具有多个具有相同名称的控制器，只要它们具有不同的区域。 通过向 `controller` 和 `action` 添加另一个路由参数 `area`，可使用区域为路由创建层次结构。 本节讨论路由如何与区域交互。 有关如何使用视图区域的详细信息，请参阅[区域](xref:mvc/controllers/areas)。

下面的示例将 MVC 配置为使用默认常规路由和`area``area`命名路由`Blog`的路由：

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

在前面的代码中，<xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*>调用 以创建 。 `"blog_route"` 第二个`"Blog"`参数 ， 是区域名称。

匹配 URL 路径（`/Manage/Users/AddUser`如`"blog_route"`）时，路由将`{ area = Blog, controller = Users, action = AddUser }`生成路由值。 路由`area`值由 的`area`默认值生成。 由 创建的`MapAreaControllerRoute`路由等效于以下内容：

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup2.cs?name=snippet2)]

`MapAreaControllerRoute` 通过为使用所提供的区域名称（本例中为 `Blog`）的 `area` 提供默认值和约束，来创建路由。 默认值确保路由始终生成 `{ area = Blog, ... }`，约束要求在生成 URL 时使用值 `{ area = Blog, ... }`。

传统路由依赖于顺序。 通常，具有区域的路由应更早放置，因为它们比没有区域的路由更具体。

使用前面的示例，路由值`{ area = Blog, controller = Users, action = AddUser }`匹配以下操作：

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[[区域]](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性表示控制器是区域的一部分。 此控制器位于该区域`Blog`。 没有属性的`[Area]`控制器不是任何区域的成员，并且**与**路由值由路由提供时`area`不匹配。 在下面的示例中，只有所列出的第一个控制器才能与路由值 `{ area = Blog, controller = Users, action = AddUser }` 匹配。

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

此处显示每个控制器的命名空间，以便完整性。 如果前面的控制器使用相同的命名空间，则将生成编译器错误。 类命名空间对 MVC 的路由没有影响。

前两个控制器是区域成员，仅在 `area` 路由值提供其各自的区域名称时匹配。 第三个控制器不是任何区域的成员，只能在路由没有为 `area` 提供任何值时匹配。

<a name="aa"></a>

就*不匹配任何值*而言，缺少 `area` 值相当于 `area` 的值为 NULL 或空字符串。

在区域内执行操作时，for 的`area`路由值可作为用于 URL 生成的路线[的环境值](#ambient)。 这意味着默认情况下，区域在 URL 生成中具有*粘性*，如以下示例所示。

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup3.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

以下代码生成到 的`/Zebra/Users/AddUser`URL：

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/HomeController.cs?name=snippet)]

<a name="action"></a>

## <a name="action-definition"></a>操作定义

控制器上的公共方法（具有[非操作](xref:Microsoft.AspNetCore.Mvc.NonActionAttribute)属性的这些方法除外）是操作。

## <a name="sample-code"></a>示例代码

 * [MyDisplayRouteInfo](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x/main/Extensions/ControllerContextExtensions.cs)方法包含在[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)中，用于显示路由信息。
* [查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/routing/samples/3.x)（[如何下载](xref:index#how-to-download-a-sample)）

[!INCLUDE[](~/includes/dbg-route.md)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

ASP.NET Core MVC 使用路由[中间件](xref:fundamentals/middleware/index)来匹配传入请求的 URL 并将它们映射到操作。 路由在启动代码或属性中定义。 路由描述应如何将 URL 路径与操作相匹配。 它还用于在响应中生成送出的 URL（用于链接）。

操作既支持传统路由，也支持属性路由。 通过在控制器或操作上放置路由可实现属性路由。 有关详细信息，请参阅[混合路由](#routing-mixed-ref-label)。

本文档将介绍 MVC 与路由之间的交互，以及典型的 MVC 应用如何使用各种路由功能。 有关高级路由的详细信息，请参阅[路由](xref:fundamentals/routing)。

## <a name="setting-up-routing-middleware"></a>设置路由中间件

在 *Configure* 方法中，可能会看到与下面类似的代码：

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

在对 `UseMvc` 的调用中，`MapRoute` 用于创建单个路由，亦称 `default` 路由。 大多数 MVC 应用使用带有模板的路由，与 `default` 路由类似。

路由模板 `"{controller=Home}/{action=Index}/{id?}"` 可以匹配诸如 `/Products/Details/5` 之类的 URL 路径，并通过对路径进行标记来提取路由值 `{ controller = Products, action = Details, id = 5 }`。 MVC 将尝试查找名为 `ProductsController` 的控制器并运行 `Details` 操作：

```csharp
public class ProductsController : Controller
{
   public IActionResult Details(int id) { ... }
}
```

请注意，在此示例中，当调用此操作时，模型绑定会使用值 `id = 5` 将 `id` 参数设置为 `5`。 有关更多详细信息，请参阅[模型绑定](../models/model-binding.md)。

使用 `default` 路由：

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

路由模板：

* `{controller=Home}` 将 `Home` 定义为默认 `controller`

* `{action=Index}` 将 `Index` 定义为默认 `action`

* `{id?}` 将 `id` 定义为可选参数

默认路由参数和可选路由参数不必包含在 URL 路径中进行匹配。 有关路由模板语法的详细说明，请参阅[路由模板参考](xref:fundamentals/routing#route-template-reference)。

`"{controller=Home}/{action=Index}/{id?}"` 可以匹配 URL 路径 `/` 并生成路由值 `{ controller = Home, action = Index }`。 `controller` 和 `action` 的值使用默认值，`id` 不生成值，因为 URL 路径中没有相应的段。 MVC 使用这些路由值选择 `HomeController` 和 `Index` 操作：

```csharp
public class HomeController : Controller
{
  public IActionResult Index() { ... }
}
```

通过使用此控制器定义和路由模板，将对以下任意 URL 路径执行 `HomeController.Index` 操作：

* `/Home/Index/17`

* `/Home/Index`

* `/Home`

* `/`

简便方法 `UseMvcWithDefaultRoute`：

```csharp
app.UseMvcWithDefaultRoute();
```

可用于替换：

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

`UseMvc` 和 `UseMvcWithDefaultRoute` 可向中间件管道添加 `RouterMiddleware` 的实例。 MVC 不直接与中间件交互，而是使用路由来处理请求。 MVC 通过 `MvcRouteHandler` 实例连接到路由。 `UseMvc` 内的代码与下面类似：

```csharp
var routes = new RouteBuilder(app);

// Add connection to MVC, will be hooked up by calls to MapRoute.
routes.DefaultHandler = new MvcRouteHandler(...);

// Execute callback to register routes.
// routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");

// Create route collection and add the middleware.
app.UseRouter(routes.Build());
```

`UseMvc` 不直接定义任何路由，它向 `attribute` 路由的路由集合添加占位符。 重载 `UseMvc(Action<IRouteBuilder>)` 则允许用户添加自己的路由，并且还支持属性路由。  `UseMvc` 及其所有变体都会为属性路由添加占位符：无论如何配置 `UseMvc`，属性路由始终可用。 `UseMvcWithDefaultRoute` 定义默认路由并支持属性路由。 [属性路由](#attribute-routing-ref-label)部分提供了有关属性路由的更多详细信息。

<a name="routing-conventional-ref-label"></a>

## <a name="conventional-routing"></a>传统路由

`default` 路由：

```csharp
routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
```

前面的代码是常规路由的示例。 此样式称为传统路由，因为它为 URL 路径建立了*约定*：

* 第一个路径段映射到控制器名称。
* 第二个映射到操作名称。
* 第三个段用于可选`id`。 `id`映射到模型实体。

使用此 `default` 路由时，URL 路径 `/Products/List` 映射到 `ProductsController.List` 操作，`/Blog/Article/17` 映射到 `BlogController.Article`。 此映射**仅**基于控制器和操作名称，而不基于命名空间、源文件位置或方法参数。

> [!TIP]
> 使用默认路由进行传统路由时，可快速生成应用程序，无需为所定义的每项操作提供一个新的 URL 模式。 对于包含 CRUD 样式操作的应用程序，通过保持各控制器间 URL 的一致性，可帮助简化代码，使 UI 更易预测。

> [!WARNING]
> 路由模板将 `id` 定义为可选参数，意味着无需在 URL 中提供 ID 也可执行操作。 从 URL 中省略 `id` 通常会导致模型绑定将它设置为 `0`，进而导致在数据库中找不到与 `id == 0` 匹配的实体。 属性路由可以提供细化控制，使某些操作需要 ID，某些操作不需要 ID。 按照惯例，当可选参数（比如 `id`）有可能在正确的用法中出现时，本文档将涵盖这些参数。

## <a name="multiple-routes"></a>多个路由

通过添加对 `MapRoute` 的多次调用，可以在 `UseMvc` 内添加多个路由。 这样做可以定义多个约定，或添加专用于特定操作的传统路由，比如：

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
            defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

此处的 `blog` 路由是一个*专用的传统路由*，这表示它使用传统路由系统，但专用于特定的操作。 由于 `controller` 和 `action` 不会在路由模板中作为参数显示，它们只能有默认值，因此，此路由将始终映射到 `BlogController.Article` 操作。

路由集合中的路由会进行排序，并按添加顺序进行处理。 因此，在此示例中，将先尝试 `blog` 路由，再尝试 `default` 路由。

> [!NOTE]
> *专用常规路由*通常使用**捕获所有**路由参数，如`{*article}`捕获 URL 路径的剩余部分。 这会使某个路由变得“太贪婪”，也就是说，它会匹配用户想要使用其他路由来匹配的 URL。 将“贪婪的”路由放在路由表中靠后的位置可解决此问题。

### <a name="fallback"></a>回退

在处理请求时，MVC 将验证路由值能否用于在应用程序中查找控制器和操作。 如果路由值与任何操作都不匹配，则将该路由视为不匹配，并尝试下一个路由。 这称为*回退*，其目的是简化传统路由重叠的情况。

### <a name="disambiguating-actions"></a>区分操作

当通过路由匹配到两项操作时，MVC 必须进行区分，以选择“最佳”候选项，否则会引发异常。 例如：

```csharp
public class ProductsController : Controller
{
   public IActionResult Edit(int id) { ... }

   [HttpPost]
   public IActionResult Edit(int id, Product product) { ... }
}
```

此控制器定义了两项操作，这两项操作均与 URL 路径 `/Products/Edit/17` 和路由数据 `{ controller = Products, action = Edit, id = 17 }` 匹配。 这是 MVC 控制器的典型模式，其中 `Edit(int)` 显示用于编辑产品的表单，`Edit(int, Product)` 处理已发布的表单。 为此，MVC 需要在请求为 HTTP `POST` 时选择 `Edit(int, Product)`，在 Http 谓词为任何其他内容时选择 `Edit(int)`。

`HttpPostAttribute` ( `[HttpPost]` ) 是 `IActionConstraint` 的实现，它仅允许执行当 Http 谓词为 `POST` 时选择的操作。 `IActionConstraint` 的存在使 `Edit(int, Product)` 成为比 `Edit(int)`“更好”的匹配项，因此会先尝试 `Edit(int, Product)`。

只需在特殊化方案中编写自定义 `IActionConstraint` 实现，但务必了解 `HttpPostAttribute` 等属性的角色 — 为其他 Http 谓词定义了类似的属性。 在传统路由中，当操作属于 `show form -> submit form` 工作流时通常使用相同的操作名称。 在阅读[了解 IActionConstraint](#understanding-iactionconstraint) 部分后，此模式的便利性将变得更加明显。

如果匹配多个路由，但 MVC 找不到“最佳”路由，则会引发 `AmbiguousActionException`。

<a name="routing-route-name-ref-label"></a>

### <a name="route-names"></a>路由名称

以下示例中的字符串 `"blog"` 和 `"default"` 都是路由名称：

```csharp
app.UseMvc(routes =>
{
   routes.MapRoute("blog", "blog/{*article}",
               defaults: new { controller = "Blog", action = "Article" });
   routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

路由名称为路由提供一个逻辑名称，以便使用命名路由来生成 URL。 路由排序会使 URL 生成复杂化，而这极大地简化了 URL 创建。 路由名称必须在应用程序范围内唯一。

路由名称不影响请求的 URL 匹配或处理；它们仅用于 URL 生成。 [路由](xref:fundamentals/routing)提供了有关 URL 生成（包括 MVC 特定帮助程序中的 URL 生成）的更多详细信息。

<a name="attribute-routing-ref-label"></a>

## <a name="attribute-routing"></a>属性路由

属性路由使用一组属性将操作直接映射到路由模板。 在下面的示例中，`Configure` 方法使用 `app.UseMvc();`，不传递任何路由。 `HomeController` 将匹配一组 URL，这组 URL 与默认路由 `{controller=Home}/{action=Index}/{id?}` 匹配的 URL 类似：

```csharp
public class HomeController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult Index()
   {
      return View();
   }
   [Route("Home/About")]
   public IActionResult About()
   {
      return View();
   }
   [Route("Home/Contact")]
   public IActionResult Contact()
   {
      return View();
   }
}
```

将针对任意 URL 路径 `/`、`/Home` 或 `/Home/Index` 执行 `HomeController.Index()` 操作。

> [!NOTE]
> 此示例重点介绍属性路由与传统路由之间的主要编程差异。 属性路由需要更多输入来指定路由；传统的默认路由处理路由的方式则更简洁。 但是，属性路由允许（并需要）精确控制应用于每项操作的路由模板。

使用属性路由时，控制器名称和操作名称对于操作的选择**没有**影响。 此示例匹配的 URL 与上一示例相同。

```csharp
public class MyDemoController : Controller
{
   [Route("")]
   [Route("Home")]
   [Route("Home/Index")]
   public IActionResult MyIndex()
   {
      return View("Index");
   }
   [Route("Home/About")]
   public IActionResult MyAbout()
   {
      return View("About");
   }
   [Route("Home/Contact")]
   public IActionResult MyContact()
   {
      return View("Contact");
   }
}
```

> [!NOTE]
> 上述路由模板未定义 `action`、`area` 和 `controller` 的路由参数。 事实上，属性路由中不允许使用这些路由参数。 由于路由模板已与某项操作关联，因此，分析 URL 中的操作名称毫无意义。

## <a name="attribute-routing-with-httpverb-attributes"></a>使用 Http[Verb] 属性的属性路由

属性路由还可以使用 `Http[Verb]` 属性，比如 `HttpPostAttribute`。 所有这些属性都可采用路由模板。 此示例展示与同一路由模板匹配的两项操作：

```csharp
[HttpGet("/products")]
public IActionResult ListProducts()
{
   // ...
}

[HttpPost("/products")]
public IActionResult CreateProduct(...)
{
   // ...
}
```

对于诸如 `/products` 之类的 URL 路径，当 Http 谓词为 `GET` 时将执行 `ProductsApi.ListProducts` 操作，当 Http 谓词为 `POST` 时将执行 `ProductsApi.CreateProduct`。 属性路由首先将 URL 与路由属性定义的路由模板集进行匹配。 一旦某个路由模板匹配，就会应用 `IActionConstraint` 约束来确定可以执行的操作。

> [!TIP]
> 生成 REST API 时，很少会在操作方法上使用 `[Route(...)]`这是因为该操作将接受所有 HTTP 方法。 建议使用更特定的 `Http*Verb*Attributes` 来明确 API 所支持的操作。 REST API 的客户端需要知道映射到特定逻辑操作的路径和 Http 谓词。

由于属性路由适用于特定操作，因此，使参数变成路由模板定义中的必需参数很简单。 在此示例中，`id` 是 URL 路径中的必需参数。

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

将针对诸如 `/products/3`（而非 `/products`）之类的 URL 路径执行 `ProductsApi.GetProduct(int)` 操作。 请参阅[路由](xref:fundamentals/routing)了解路由模板和相关选项的完整说明。

## <a name="route-name"></a>路由名称

以下代码定义 `Products_List` 的*路由名称*：

```csharp
public class ProductsApiController : Controller
{
   [HttpGet("/products/{id}", Name = "Products_List")]
   public IActionResult GetProduct(int id) { ... }
}
```

可以使用路由名称基于特定路由生成 URL。 路由名称不影响路由的 URL 匹配行为，仅用于生成 URL。 路由名称必须在应用程序范围内唯一。

> [!NOTE]
> 这一点与传统的*默认路由*相反，后者将 `id` 参数定义为可选参数 (`{id?}`)。 这种精确指定 API 的功能可带来一些好处，比如允许将 `/products` 和 `/products/5` 分派到不同的操作。

<a name="routing-combining-ref-label"></a>

### <a name="combining-routes"></a>合并路由

若要使属性路由减少重复，可将控制器上的路由属性与各个操作上的路由属性合并。 控制器上定义的所有路由模板均作为操作上路由模板的前缀。 在控制器上放置路由属性会使控制器中的**所有**操作都使用属性路由。

```csharp
[Route("products")]
public class ProductsApiController : Controller
{
   [HttpGet]
   public IActionResult ListProducts() { ... }

   [HttpGet("{id}")]
   public ActionResult GetProduct(int id) { ... }
}
```

在此示例中，URL 路径 `/products` 可以匹配 `ProductsApi.ListProducts`，URL 路径 `/products/5` 可以匹配 `ProductsApi.GetProduct(int)`。 这两项操作仅匹配 HTTP `GET`，因为它们用 `HttpGetAttribute` 标记。

应用于操作的以 `/` 或 `~/` 开头的路由模板不与应用于控制器的路由模板合并。 此示例匹配一组与*默认路由*类似的 URL 路径。

```csharp
[Route("Home")]
public class HomeController : Controller
{
    [Route("")]      // Combines to define the route template "Home"
    [Route("Index")] // Combines to define the route template "Home/Index"
    [Route("/")]     // Doesn't combine, defines the route template ""
    public IActionResult Index()
    {
        ViewData["Message"] = "Home index";
        var url = Url.Action("Index", "Home");
        ViewData["Message"] = "Home index" + "var url = Url.Action; =  " + url;
        return View();
    }

    [Route("About")] // Combines to define the route template "Home/About"
    public IActionResult About()
    {
        return View();
    }   
}
```

<a name="routing-ordering-ref-label"></a>

### <a name="ordering-attribute-routes"></a>对属性路由排序

与按定义顺序执行的传统路由不同，属性路由生成树并同时匹配所有路由。 其行为就像路由条目是以理想排序方式放置的一样；最特定的路由有机会比较一般的路由先执行。

例如，像 `blog/search/{topic}` 这样的路由比像 `blog/{*article}` 这样的路由更特定。 从逻辑上讲，`blog/search/{topic}` 路由默认情况下先“运行”，因为这是唯一合理的排序。 使用传统路由时，开发人员负责按所需顺序放置路由。

属性路由可以使用框架提供的所有路由属性的 `Order` 属性来配置顺序。 路由按 `Order` 属性的升序进行处理。 默认顺序为 `0`。 使用 `Order = -1` 设置的路由比未设置顺序的路由先运行。 使用 `Order = 1` 设置的路由在默认路由排序后运行。

> [!TIP]
> 避免依赖 `Order`。 如果 URL 空间需要有显式顺序值才能正确进行路由，则同样可能使客户端混淆不清。 属性路由通常选择与 URL 匹配的正确路由。 如果用于 URL 生成的默认顺序不起作用，使用路由名称作为替代项通常比应用 `Order` 属性更简单。

Razor Pages 路由和 MVC 控制器路由共享一个实现。 可在 [ 路由和应用约定：路由顺序](xref:razor-pages/razor-pages-conventions#route-order)中找到有关 Razor Pages 主题中路由顺序的信息。

<a name="routing-token-replacement-templates-ref-label"></a>

## <a name="token-replacement-in-route-templates-controller-action-area"></a>路由模板中的标记替换（[controller]、[action]、[area]）

为方便起见，属性路由通过将令牌包装在方形括号 （，`[` `]`） 支持*令牌替换*。 标记 `[action]`、`[area]` 和 `[controller]` 替换为定义了路由的操作中的操作名称值、区域名称值和控制器名称值。 在接下来的示例中，操作与注释中所述的 URL 路径匹配：

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController.cs?range=7-11,13-17,20-22)]

标记替换发生在属性路由生成的最后一步。 上述示例的行为方式将与以下代码相同：

[!code-csharp[](routing/samples/2.x/main/Controllers/ProductsController2.cs?range=7-11,13-17,20-22)]

属性路由还可以与继承结合使用。 与标记替换结合使用时尤为强大。

```csharp
[Route("api/[controller]")]
public abstract class MyBaseController : Controller { ... }

public class ProductsController : MyBaseController
{
   [HttpGet] // Matches '/api/Products'
   public IActionResult List() { ... }

   [HttpPut("{id}")] // Matches '/api/Products/{id}'
   public IActionResult Edit(int id) { ... }
}
```

标记替换也适用于属性路由定义的路由名称。 `[Route("[controller]/[action]", Name="[controller]_[action]")]` 为每项操作生成一个唯一的路由名称。

若要匹配文本标记替换分隔符 `[` 或 `]`，可通过重复该字符（`[[` 或 `]]`）对其进行转义。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<a name="routing-token-replacement-transformers-ref-label"></a>

### <a name="use-a-parameter-transformer-to-customize-token-replacement"></a>使用参数转换程序自定义标记替换

使用参数转换程序可以自定义标记替换。 参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。 例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。

`RouteTokenTransformerConvention` 是应用程序模型约定，可以：

* 将参数转换程序应用到应用程序中的所有属性路由。
* 在替换属性路由标记值时对其进行自定义。

```csharp
public class SubscriptionManagementController : Controller
{
    [HttpGet("[controller]/[action]")] // Matches '/subscription-management/list-all'
    public IActionResult ListAll() { ... }
}
```

`RouteTokenTransformerConvention` 在 `ConfigureServices` 中注册为选项。

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options =>
    {
        options.Conventions.Add(new RouteTokenTransformerConvention(
                                     new SlugifyParameterTransformer()));
    });
}

public class SlugifyParameterTransformer : IOutboundParameterTransformer
{
    public string TransformOutbound(object value)
    {
        if (value == null) { return null; }

        // Slugify value
        return Regex.Replace(value.ToString(), "([a-z])([A-Z])", "$1-$2").ToLower();
    }
}
```

::: moniker-end


::: moniker range="< aspnetcore-3.0"
<a name="routing-multiple-routes-ref-label"></a>

### <a name="multiple-routes"></a>多个路由

属性路由支持定义多个访问同一操作的路由。 此操作最常用于模拟*默认传统路由*的行为，如以下示例所示：

```csharp
[Route("[controller]")]
public class ProductsController : Controller
{
   [Route("")]     // Matches 'Products'
   [Route("Index")] // Matches 'Products/Index'
   public IActionResult Index()
}
```

在控制器上放置多个路由属性意味着，每个路由属性将与操作方法上的每个路由属性合并。

```csharp
[Route("Store")]
[Route("[controller]")]
public class ProductsController : Controller
{
   [HttpPost("Buy")]     // Matches 'Products/Buy' and 'Store/Buy'
   [HttpPost("Checkout")] // Matches 'Products/Checkout' and 'Store/Checkout'
   public IActionResult Buy()
}
```

当在某个操作上放置多个路由属性（可实现 `IActionConstraint`）时，每个操作约束将与定义它的属性中的路由模板合并。

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
   [HttpPut("Buy")]      // Matches PUT 'api/Products/Buy'
   [HttpPost("Checkout")] // Matches POST 'api/Products/Checkout'
   public IActionResult Buy()
}
```

> [!TIP]
> 在操作上使用多个路由可能看起来很强大，但更建议使应用程序的 URL 空间保持简洁且定义完善。 仅在需要时，例如为了支持现有客户端，才在操作上使用多个路由。

<a name="routing-attr-options"></a>

### <a name="specifying-attribute-route-optional-parameters-default-values-and-constraints"></a>指定属性路由的可选参数、默认值和约束

属性路由支持使用与传统路由相同的内联语法，来指定可选参数、默认值和约束。

```csharp
[HttpPost("product/{id:int}")]
public IActionResult ShowProduct(int id)
{
   // ...
}
```

有关路由模板语法的详细说明，请参阅[路由模板参考](xref:fundamentals/routing#route-template-reference)。

<a name="routing-cust-rt-attr-irt-ref-label"></a>

### <a name="custom-route-attributes-using-iroutetemplateprovider"></a>使用 `IRouteTemplateProvider` 的自定义路由属性

该框架中提供的所有路由属性（`[Route(...)]`、`[HttpGet(...)]` 等）都可实现 `IRouteTemplateProvider` 接口。 当应用启动时，MVC 会查找控制器类和操作方法上的属性，并使用可实现 `IRouteTemplateProvider` 的属性生成一组初始路由。

用户可以实现 `IRouteTemplateProvider` 来定义自己的路由属性。 每个 `IRouteTemplateProvider` 都允许定义一个包含自定义路由模板、顺序和名称的路由：

```csharp
public class MyApiControllerAttribute : Attribute, IRouteTemplateProvider
{
   public string Template => "api/[controller]";

   public int? Order { get; set; }

   public string Name { get; set; }
}
```

应用 `[MyApiController]` 时，上述示例中的属性会自动将 `Template` 设置为 `"api/[controller]"`。

<a name="routing-app-model-ref-label"></a>

### <a name="using-application-model-to-customize-attribute-routes"></a>使用应用程序模型自定义属性路由

*应用程序模型*是一个在启动时创建的对象模型，MVC 可使用其中的所有元数据来路由和执行操作。 *应用程序模型*包含从路由属性收集（通过 `IRouteTemplateProvider`）的所有数据。 可通过编写*约定*在启动时修改应用程序模型，以便自定义路由的行为方式。 此部分通过一个简单的示例说明了如何使用应用程序模型自定义路由。

[!code-csharp[](routing/samples/2.x/main/NamespaceRoutingConvention.cs)]

<a name="routing-mixed-ref-label"></a>

## <a name="mixed-routing-attribute-routing-vs-conventional-routing"></a>混合路由：属性路由与传统路由

MVC 应用程序可以混合使用传统路由与属性路由。 通常将传统路由用于为浏览器处理 HTML 页面的控制器，将属性路由用于处理 REST API 的控制器。

操作既支持传统路由，也支持属性路由。 通过在控制器或操作上放置路由可实现属性路由。 不能通过传统路由访问定义属性路由的操作，反之亦然。 控制器上的**任何**路由属性都会使控制器中的所有操作使用属性路由。

> [!NOTE]
> 这两种路由系统的区别在于 URL 与路由模板匹配后所应用的过程。 在传统路由中，将使用匹配项中的路由值，从包含所有传统路由操作的查找表中选择操作和控制器。 在属性路由中，每个模板都与某项操作关联，无需进行进一步的查找。

## <a name="complex-segments"></a>复杂段

复杂段（例如，`[Route("/dog{token}cat")]`）通过非贪婪的方式从右到左匹配文字进行处理。 有关说明，请参阅[源代码](https://github.com/aspnet/Routing/blob/9cea167cfac36cf034dbb780e3f783114ef94780/src/Microsoft.AspNetCore.Routing/Patterns/RoutePatternMatcher.cs#L296)。 有关详细信息，请参阅[此问题](https://github.com/dotnet/AspNetCore.Docs/issues/8197)。

<a name="routing-url-gen-ref-label"></a>

## <a name="url-generation"></a>URL 生成

MVC 应用程序可以使用路由的 URL 生成功能，生成指向操作的 URL 链接。 生成 URL 可消除硬编码 URL，使代码更稳定、更易维护。 此部分重点介绍 MVC 提供的 URL 生成功能，并且仅涵盖 URL 生成工作原理的基础知识。 有关 URL 生成的详细说明，请参阅[路由](xref:fundamentals/routing)。

`IUrlHelper` 接口用于生成 URL，是 MVC 与路由之间的基础结构的基础部分。 在控制器、视图和视图组件中，可通过 `Url` 属性找到 `IUrlHelper` 的实例。

在此示例中，将通过 `Controller.Url` 属性使用 `IUrlHelper` 接口来生成指向另一项操作的 URL。

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationController.cs?name=snippet_1)]

如果应用程序使用的是传统默认路由，则 `url` 变量的值将为 URL 路径字符串 `/UrlGeneration/Destination`。 此 URL 路径由路由创建，方法是将当前请求中的路由值（环境值）与传递到 `Url.Action` 的值合并，并将这些值替换到路由模板中：

```
ambient values: { controller = "UrlGeneration", action = "Source" }
values passed to Url.Action: { controller = "UrlGeneration", action = "Destination" }
route template: {controller}/{action}/{id?}

result: /UrlGeneration/Destination
```

路由模板中的每个路由参数都会通过将名称与这些值和环境值匹配，来替换掉原来的值。 没有值的路由参数如果有默认值，则可使用默认值；如果本身是可选参数（比如此示例中的 `id`），则可直接跳过。 如果任何所需路由参数没有对应的值，URL 生成将失败。 如果某个路由的 URL 生成失败，则尝试下一个路由，直到尝试所有路由或找到匹配项为止。

上面的 `Url.Action` 示例假定使用传统路由，但 URL 生成功能的工作方式与属性路由相似，只不过概念不同。 在传统路由中，路由值用于扩展模板，`controller` 和 `action` 的路由值通常出现在该模板中 — 这种做法可行是因为通过路由匹配的 URL 遵守某项*约定*。 在属性路由中，`controller` 和 `action` 的路由值不能出现在模板中，它们用于查找要使用的模板。

此示例使用属性路由：

[!code-csharp[](routing/samples/2.x/main/StartupUseMvc.cs?name=snippet_1)]

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerAttr.cs?name=snippet_1)]

MVC 生成一个包含所有属性路由操作的查找表，并匹配 `controller` 和 `action` 的值，以选择要用于生成 URL 的路由模板。 在上述示例中，生成了 `custom/url/to/destination`。

### <a name="generating-urls-by-action-name"></a>根据操作名称生成 URL

`Url.Action` (`IUrlHelper` . `Action`) 以及所有相关重载都基于这样一种想法：用户想通过指定控制器名称和操作名称来指定要链接的内容。

> [!NOTE]
> 使用 `Url.Action` 时，将为用户指定 `controller` 和 `action` 的当前路由值，`controller` 和 `action` 的值是环境值*和* **值** ** 的一部分。 `Url.Action` 方法始终使用 `action` 和 `controller` 的当前值，并将生成将路由到当前操作的 URL 路径。

路由尝试使用环境值中的值来填充生成 URL 时未提供的信息。 通过使用路由（比如 `{a}/{b}/{c}/{d}`）和环境值 `{ a = Alice, b = Bob, c = Carol, d = David }`，路由就具有足够的信息来生成 URL，而无需任何附加值，因为所有路由参数都有值。 如果添加了值 `{ d = Donovan }`，则会忽略值 `{ d = David }`，生成的 URL 路径将为 `Alice/Bob/Carol/Donovan`。

> [!WARNING]
> URL 路径是分层的。 在上述示例中，如果添加了值 `{ c = Cheryl }`，则会忽略 `{ c = Carol, d = David }` 这两个值。 在这种情况下，`d` 不再具有任何值，URL 生成将失败。 用户需要指定 `c` 和 `d` 所需的值。  使用默认路由 (`{controller}/{action}/{id?}`) 时可能会遇到此问题，但在实际操作中很少遇到此行为，因为 `Url.Action` 始终显式指定 `controller` 和 `action` 值。

较长的 `Url.Action` 重载还采用附加*路由值*对象，为 `controller` 和 `action` 以外的路由参数提供值。 此重载最常与 `id` 结合使用，比如 `Url.Action("Buy", "Products", new { id = 17 })`。 按照惯例，*路由值*对象通常是匿名类型的对象，但它也可以是 `IDictionary<>` 或*普通旧 .NET 对象*。 任何与路由参数不匹配的附加路由值都放在查询字符串中。

[!code-csharp[](routing/samples/2.x/main/Controllers/TestController.cs)]

> [!TIP]
> 若要创建绝对 URL，请使用采用 `protocol` 的重载：`Url.Action("Buy", "Products", new { id = 17 }, protocol: Request.Scheme)`

<a name="routing-gen-urls-route-ref-label"></a>

### <a name="generating-urls-by-route"></a>根据路由生成 URL

上面的代码演示了如何通过传入控制器和操作名称来生成 URL。 `IUrlHelper` 还提供 `Url.RouteUrl` 系列的方法。 这些方法类似于 `Url.Action`，但它们不会将 `action` 和 `controller` 的当前值复制到路由值。 最常见的用法是指定一个路由名称，以使用特定路由来生成 URL，通常*不*指定控制器或操作名称。

[!code-csharp[](routing/samples/2.x/main/Controllers/UrlGenerationControllerRouting.cs?name=snippet_1)]

<a name="routing-gen-urls-html-ref-label"></a>

### <a name="generating-urls-in-html"></a>在 HTML 中生成 URL

`IHtmlHelper` 提供 `HtmlHelper` 方法 `Html.BeginForm` 和 `Html.ActionLink`，可分别生成 `<form>` 和 `<a>` 元素。 这些方法使用 `Url.Action` 方法来生成 URL，并且采用相似的参数。 `HtmlHelper` 的配套 `Url.RouteUrl` 为 `Html.BeginRouteForm` 和 `Html.RouteLink`，两者具有相似的功能。

TagHelper 通过 `form` TagHelper 和 `<a>` TagHelper 生成 URL。 两者均通过 `IUrlHelper` 来实现。 有关详细信息，请参阅[使用表单](../views/working-with-forms.md)。

在视图内，可通过 `Url` 属性将 `IUrlHelper` 用于前文未涵盖的任何临时 URL 生成。

<a name="routing-gen-urls-action-ref-label"></a>

### <a name="generating-urls-in-action-results"></a>在操作结果中生成 URL

以上示例展示了如何在控制器中使用 `IUrlHelper`，不过，控制器中最常见的用法是将 URL 生成为操作结果的一部分。

`ControllerBase` 和 `Controller` 基类为操作结果提供简便的方法来引用另一项操作。 一种典型用法是在接受用户输入后进行重定向。

```csharp
public IActionResult Edit(int id, Customer customer)
{
    if (ModelState.IsValid)
    {
        // Update DB with new details.
        return RedirectToAction("Index");
    }
    return View(customer);
}
```

操作结果工厂方法遵循与 `IUrlHelper` 上的方法类似的模式。

<a name="routing-dedicated-ref-label"></a>

### <a name="special-case-for-dedicated-conventional-routes"></a>专用传统路由的特殊情况

传统路由可以使用一种特殊的路由定义，称为*专用传统路由*。 在下面的示例中，名为 `blog` 的路由是一种专用传统路由。

```csharp
app.UseMvc(routes =>
{
    routes.MapRoute("blog", "blog/{*article}",
        defaults: new { controller = "Blog", action = "Article" });
    routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
});
```

使用这些路由定义，`Url.Action("Index", "Home")` 将通过 `default` 路由生成 URL 路径 `/`，但是，为什么会这样？ 用户可能认为使用 `blog`，路由值 `{ controller = Home, action = Index }` 就足以生成 URL，且结果为 `/blog?action=Index&controller=Home`。

专用传统路由依赖于不具有相应路由参数的默认值的特殊行为，以防止路由在 URL 生成过程中“太贪婪”。 在此例中，默认值是为 `{ controller = Blog, action = Article }`，`controller` 和 `action` 均未显示为路由参数。 当路由执行 URL 生成时，提供的值必须与默认值匹配。 使用 `blog` 的 URL 生成将失败，因为值 `{ controller = Home, action = Index }` 与 `{ controller = Blog, action = Article }` 不匹配。 然后，路由回退，尝试使用 `default`，并最终成功。

<a name="routing-areas-ref-label"></a>

## <a name="areas"></a>区域

[区域](areas.md)是一种 MVC 功能，用于将相关功能整理到一个组中，作为单独的路由命名空间（用于控制器操作）和文件夹结构（用于视图）。 通过使用区域，应用程序可以有多个名称相同的控制器，只要它们具有不同的*区域*。 通过向 `controller` 和 `action` 添加另一个路由参数 `area`，可使用区域为路由创建层次结构。 此部分将讨论路由如何与区域交互；有关如何将区域与视图结合使用的详细信息，请参阅[区域](areas.md)。

下面的示例将 MVC 配置为使用默认传统路由和*区域路由*（用于名为 `Blog` 的区域）：

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet1)]

与 URL 路径（比如 `/Manage/Users/AddUser`）匹配时，第一个路由将生成路由值 `{ area = Blog, controller = Users, action = AddUser }`。 `area` 路由值由 `area` 的默认值生成，事实上，通过 `MapAreaRoute` 创建的路由等效于以下路由：

[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet2)]

`MapAreaRoute` 通过为使用所提供的区域名称（本例中为 `Blog`）的 `area` 提供默认值和约束，来创建路由。 默认值确保路由始终生成 `{ area = Blog, ... }`，约束要求在生成 URL 时使用值 `{ area = Blog, ... }`。

> [!TIP]
> 传统路由依赖于顺序。 一般情况下，具有区域的路由应放在路由表中靠前的位置，因为它们比没有区域的路由更特定。

在上面的示例中，路由值将与以下操作匹配：

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

`AreaAttribute` 用于将控制器表示为某个区域的一部分，比方说，此控制器位于 `Blog` 区域中。 没有 `[Area]` 属性的控制器不是任何区域的成员，在路由提供 `area` 路由值时**不**匹配。 在下面的示例中，只有所列出的第一个控制器才能与路由值 `{ area = Blog, controller = Users, action = AddUser }` 匹配。

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Blog/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Zebra/Controllers/UsersController.cs)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Controllers/UsersController.cs)]

> [!NOTE]
> 出于完整性考虑，此处显示了每个控制器的命名空间，否则，控制器会发生命名冲突并生成编译器错误。 类命名空间对 MVC 的路由没有影响。

前两个控制器是区域成员，仅在 `area` 路由值提供其各自的区域名称时匹配。 第三个控制器不是任何区域的成员，只能在路由没有为 `area` 提供任何值时匹配。

> [!NOTE]
> 就*不匹配任何值*而言，缺少 `area` 值相当于 `area` 的值为 NULL 或空字符串。

在某个区域内执行某项操作时，`area` 的路由值将以*环境值*的形式提供，以便路由用于生成 URL。 这意味着默认情况下，区域在 URL 生成中具有*粘性*，如以下示例所示。
[!code-csharp[](routing/samples/3.x/AreasRouting/Startup.cs?name=snippet3)]

[!code-csharp[](routing/samples/3.x/AreasRouting/Areas/Duck/Controllers/UsersController.cs)]

<a name="iactionconstraint-ref-label"></a>

## <a name="understanding-iactionconstraint"></a>了解 IActionConstraint

> [!NOTE]
> 此部分深入介绍框架内部结构以及 MVC 如何选择要执行的操作。 典型的应用程序不需要自定义 `IActionConstraint`

即使不熟悉 `IActionConstraint`，也可能已经用过该接口。 `[HttpGet]` 属性和类似的 `[Http-VERB]` 属性可实现 `IActionConstraint` 来限制操作方法的执行。

```csharp
public class ProductsController : Controller
{
    [HttpGet]
    public IActionResult Edit() { }

    public IActionResult Edit(...) { }
}
```

假定使用默认传统路由，URL 路径 `/Products/Edit` 将生成值 `{ controller = Products, action = Edit }`，这将匹配此处所示的**两项**操作。 在 `IActionConstraint` 术语中，我们会说，这两项操作都被视为候选项，因为它们都与该路由数据匹配。

当 `HttpGetAttribute` 执行时，它认为 *Edit()* 是 *GET* 的匹配项，而不是任何其他 Http 谓词的匹配项。 `Edit(...)` 操作未定义任何约束，因此将匹配任何 Http 谓词。 因此，假定 Http 谓词为 `POST`，则仅 `Edit(...)` 匹配。 不过，对于 `GET`，这两项操作仍然都能匹配，只是具有 `IActionConstraint` 的操作始终被认为比没有该接口的操作*更匹配*。 因此，由于 `Edit()` 具有 `[HttpGet]`，则认为它更特定，在两项操作都能匹配的情况将选择它。

从概念上讲，`IActionConstraint` 是一种*重载*形式，但它并不重载具有相同名称的方法，而在匹配相同 URL 的操作之间重载。 属性路由也使用 `IActionConstraint`，这可能会导致将不同控制器中的操作都视为候选项。

<a name="iactionconstraint-impl-ref-label"></a>

### <a name="implementing-iactionconstraint"></a>实现 IActionConstraint

实现 `IActionConstraint` 最简单的方法是创建派生自 `System.Attribute` 的类，并将其置于操作和控制器上。 MVC 将自动发现任何应用为属性的 `IActionConstraint`。 可使用应用程序模型应用约束，这可能是最灵活的一种方法，因为它允许对其应用方式进行元编程。

在下面的示例中，约束根据路由数据*中的国家/地区代码*选择操作。 [GitHub 上的完整示例](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.ActionConstraintSample.Web/CountrySpecificAttribute.cs)。

```csharp
public class CountrySpecificAttribute : Attribute, IActionConstraint
{
    private readonly string _countryCode;

    public CountrySpecificAttribute(string countryCode)
    {
        _countryCode = countryCode;
    }

    public int Order
    {
        get
        {
            return 0;
        }
    }

    public bool Accept(ActionConstraintContext context)
    {
        return string.Equals(
            context.RouteContext.RouteData.Values["country"].ToString(),
            _countryCode,
            StringComparison.OrdinalIgnoreCase);
    }
}
```

用户负责实现 `Accept` 方法，并为要执行的约束选择“顺序”。 在此例中，当 `country` 路由值匹配时，`Accept` 方法返回 `true` 以表示该操作是匹配项。 它与 `RouteValueAttribute` 的不同之处在于，它允许回退到非属性化操作。 通过该示例可以了解到，如果定义 `en-US` 操作，则像 `fr-FR` 这样的国家/地区代码将回退到一个未应用 `[CountrySpecific(...)]` 的较通用的控制器。

`Order` 属性决定约束所属的*阶段*。 操作约束基于 `Order` 分组运行。 例如，该框架提供的所有 HTTP 方法属性均使用相同的 `Order` 值，以便在相同的阶段运行。 用户可以按需设置阶段数来实现所需的策略。

> [!TIP]
> 若要确定 `Order` 的值，请考虑是否应在 HTTP 方法前应用约束。 数值较低的先运行。

::: moniker-end
