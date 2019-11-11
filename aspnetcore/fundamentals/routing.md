---
title: ASP.NET Core 中的路由
author: rick-anderson
description: 了解 ASP.NET Core 路由如何负责将请求 URI 映射到终结点选择器并向终结点调度传入的请求。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/24/2019
uid: fundamentals/routing
ms.openlocfilehash: be4493cc927bd5437a2c9dab00b6a555756195bb
ms.sourcegitcommit: eb2fe5ad2e82fab86ca952463af8d017ba659b25
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2019
ms.locfileid: "73416140"
---
# <a name="routing-in-aspnet-core"></a>ASP.NET Core 中的路由

撰写者：[Ryan Nowak](https://github.com/rynowak)、[Steve Smith](https://ardalis.com/) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)

::: moniker range=">= aspnetcore-3.0"

路由负责将请求 URI 映射到终结点并向这些终结点调度传入的请求。 路由在应用中定义，并在应用启动时进行配置。 路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。 通过使用应用中的路由信息，路由还能生成映射到终结点的 URL。

> [!IMPORTANT]
> 本文档介绍较低级别的 ASP.NET Core 路由。 有关 ASP.NET Core MVC 路由的信息，请参阅 <xref:mvc/controllers/routing>。 有关 Razor Pages 中路由约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions>。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="routing-basics"></a>路由基础知识

大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。 默认传统路由 `{controller=Home}/{action=Index}/{id?}`：

* 支持基本的描述性路由方案。
* 是基于 UI 的应用的有用起点。

开发人员通常在专业情况下（例如，博客和电子商务终结点）使用[属性路由](xref:mvc/controllers/routing#attribute-routing)或专用传统路由向应用的高流量区域添加其他简洁路由。

Web API 应使用属性路由，将应用功能建模为一组资源，其中操作是由 HTTP 谓词表示。 也就是说，对同一逻辑资源执行的许多操作（例如，GET、POST）都将使用相同 URL。 属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。

Razor Pages 应用使用默认的传统路由从应用的“页面”文件夹中提供命名资源  。 还可以使用其他约定来自定义 Razor Pages 路由行为。 有关详细信息，请参阅 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。

借助 URL 生成支持，无需通过硬编码 URL 将应用关联到一起，即可开发应用。 此支持允许从基本路由配置入手，并在确定应用的资源布局后修改路由。

路由使用“终结点”(`Endpoint`) 来表示应用中的逻辑终结点  。

终结点定义用于处理请求的委托和任意元数据的集合。 元数据用于实现横切关注点，该实现基于附加到每个终结点的策略和配置。

路由系统具有以下特征：

* 路由模板语法用于通过标记化路由参数来定义路由。
* 可以使用常规样式和属性样式终结点配置。
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 用于确定 URL 参数是否包含给定的终结点约束的有效值。
* 应用模型（如 MVC/Razor Pages）注册其所有终结点，这些终结点具有可预测的路由方案实现。
* 路由实现会在中间件管道中任何所需位置制定路由决策。
* 路由中间件之后出现的中间件可以检查路由中间件针对给定请求 URI 的终结点决策结果。
* 可以在中间件管道中的任何位置枚举应用中的所有终结点。
* 应用可根据终结点信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。
* URL 生成是基于支持任意可扩展性的地址：

  * 可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 在任意位置解析链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) 以生成 URL。
  * 如果无法通过 DI 获得链接生成器 API，则 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 会提供生成 URL 的方法。

> [!NOTE]
> 终结点链接仅限于 MVC/Razor Pages 操作和页。 将计划在未来发布的版本中扩展终结点链接的功能。

路由通过 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 类连接到[中间件](xref:fundamentals/middleware/index)管道。 [ASP.NET Core MVC](xref:mvc/overview) 向中间件管道添加路由，作为其配置的一部分，并处理 MVC 和 Razor Pages 应用中的路由。 要了解如何将路由用作独立组件，请参阅[使用路由中间件](#use-routing-middleware)部分。

### <a name="url-matching"></a>URL 匹配

URL 匹配是路由向终结点调度传入请求的过程  。 此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。 向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。

当路由中间件执行时，它会设置终结点 (`Endpoint`) 并将值路由到 <xref:Microsoft.AspNetCore.Http.HttpContext> 上的功能。 对于当前请求：

* 调用 `HttpContext.GetEndpoint` 将获取终结点。
* `HttpRequest.RouteValues` 将获取路由值的集合。

在路由中间件之后运行的中间件可以看到终结点并采取措施。 例如，授权中间件可以在终结点的元数据集合中询问授权策略。 请求处理管道中的所有中间件执行后，将调用所选终结点的委托。

终结点路由中的路由系统负责所有的调度决策。 由于中间件是基于所选终结点来应用策略，因此任何可能影响调度或安全策略应用的决策都应在路由系统内部制定，这一点很重要。

执行终结点委托时，将根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是从路由中生成的路由值的字典  。 这些值通常通过标记 URL 来确定，可用来接受用户输入，或者在应用内作出进一步的调度决策。

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是一个与匹配的路由相关的其他数据的属性包。 提供 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 以支持将状态数据与每个路由相关联，以便应用可根据所匹配的路由作出决策。 这些值是开发者定义的，不会影响通过任何方式路由的行为  。 此外，存储于 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以属于任何类型，与 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 相反，后者必须能够转换为字符串，或从字符串进行转换。

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是参与成功匹配请求的路由的列表。 路由可以相互嵌套。 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 属性可以通过导致匹配的逻辑路由树反映该路径。 通常情况下，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一项是路由集合，应该用于生成 URL。 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最后一项是匹配的路由处理程序。

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>使用 LinkGenerator 的 URL 生成

URL 生成是通过其可根据一组路由值创建 URL 路径的过程。 这需要考虑终结点与访问它们的 URL 之间的逻辑分隔。

终结点路由包含链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)。 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 是可从 DI 检索的单一实例服务。 该 API 可在执行请求的上下文之外使用。 MVC 的 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 和依赖 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的方案（如 [Tag Helpers](xref:mvc/views/tag-helpers/intro)、HTML Helpers 和 [Action Results](xref:mvc/controllers/actions)）使用链接生成器提供链接生成功能。

链接生成器基于“地址”和“地址方案”概念   。 地址方案是确定哪些终结点用于链接生成的方式。 例如，许多用户熟悉的来自 MVC/Razor Pages 的路由名称和路由值方案都是作为地址方案实现的。

链接生成器可以通过以下扩展方法链接到 MVC/Razor Pages 操作和页面：

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

这些方法的重载接受包含 `HttpContext` 的参数。 这些方法在功能上等同于 `Url.Action` 和 `Url.Page`，但提供了更大的灵活性和更多的选项。

`GetPath*` 方法与 `Url.Action` 和 `Url.Page` 最相似，因为它们生成包含绝对路径的 URI。 `GetUri*` 方法始终生成包含方案和主机的绝对 URI。 接受 `HttpContext` 的方法在执行请求的上下文中生成 URI。 除非重写，否则将使用来自执行请求的环境路由值、URL 基本路径、方案和主机。

使用地址调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>。 生成 URI 的过程分两步进行：

1. 将地址绑定到与地址匹配的终结点列表。
1. 计算每个终结点的 `RoutePattern`，直到找到与提供的值匹配的路由模式。 输出结果会与提供给链接生成器的其他 URI 部分进行组合并返回。

对任何类型的地址，<xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法均支持标准链接生成功能。 使用链接生成器的最简便方法是通过扩展方法对特定地址类型执行操作。

| 扩展方法 | 说明 |
| ---------------- | ----------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 根据提供的值生成具有绝对路径的 URI。 |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 根据提供的值生成绝对 URI。             |

> [!WARNING]
> 请注意有关调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列含义：
>
> * 对于不验证传入请求的 `Host` 标头的应用配置，请谨慎使用 `GetUri*` 扩展方法。 如果未验证传入请求的 `Host`标头，则可能以视图/页面中 URI 的形式将不受信任的请求输入发送回客户端。 建议所有生产应用都将其服务器配置为针对已知有效值验证 `Host` 标头。
>
> * 在中间件中将 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 与 `Map` 或 `MapWhen` 结合使用时，请小心谨慎。 `Map*` 会更改执行请求的基路径，这会影响链接生成的输出。 所有 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允许指定基路径。 始终指定一个空的基路径来撤消 `Map*` 对链接生成的影响。

## <a name="endpoint-routing-differences-from-earlier-versions-of-routing"></a>终结点路由与早期版本路由的差异

比 ASP.NET Core 2.2 更早的版本中的终结点路由与版本路由之间存在一些差异：

* 终结点路由系统不支持基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的可扩展性，包括从 <xref:Microsoft.AspNetCore.Routing.Route> 继承。

* 终结点路由不支持 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。 使用 2.1 [兼容性版本](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) 以继续使用兼容性填充码。

* 在使用传统路由时，终结点路由对生成的 URI 的大小写具有不同的行为。

  请考虑以下默认路由模板：

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  假设使用以下路由生成某个操作的链接：

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  如果使用基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由，此代码生成 URI `/blog/ReadPost/17`，该 URI 遵循所提供路由值的大小写。 ASP.NET Core 2.2 或更高版本中的终结点路由生成 `/Blog/ReadPost/17`（“Blog”大写）。 终结点路由提供 `IOutboundParameterTransformer` 接口，可用于在全局范围自定义此行为或应用不同的约定来映射 URL。

  有关详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。

* 在试图链接到不存在的控制器/操作或页面时，MVC/Razor Pages 通过传统路由执行的链接生成，其操作具有不同的行为。

  请考虑以下默认路由模板：

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  假设使用以下路由通过默认模板生成某个操作的链接：

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  如果使用基于 `IRouter` 的路由，即使 `BlogController` 不存在或没有 `ReadPost` 操作方法，结果也始终为 `/Blog/ReadPost/17`。 正如所料，如果操作方法存在，ASP.NET Core 2.2 或更高版本中的终结点路由会生成 `/Blog/ReadPost/17`。 但是，如果操作不存在，终结点路由会生成空字符串。  从概念上讲，如果操作不存在，终结点路由不会假定终结点存在。

* 与终结点路由一起使用时，链接生成环境值失效算法的行为会有所不同  。

  *环境值失效*是一种算法，用于决定当前正在执行的请求（环境值）中的哪些路由值可用于链接生成操作。 链接到不同操作时，传统路由会使额外的路由值失效。 ASP.NET Core 2.2 之前的版本中，属性路由不具有此行为。 在 ASP.NET Core 的早期版本中，如果有另一个操作使用同一路由参数名称，则该操作的链接会导致发生链接生成错误。 在 ASP.NET Core 2.2 或更高版本中，链接到另一个操作时，这两种路由形式都会使值失效。

  请考虑 ASP.NET Core2.1 或更高版本中的以下示例。 链接到另一个操作（或另一页面）时，路由值可能会按非预期的方式被重用。

  在 /Pages/Store/Product.cshtml 中  ：

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  在 /Pages/Login.cshtml 中  ：

  ```cshtml
  @page "{id?}"
  ```

  如果 ASP.NET Core 2.1 或更早版本中的 URI 为 `/Store/Product/18`，则由 `@Url.Page("/Login")` 在 Store/Info 页面上生成的链接为 `/Login/18`。 即使链接目标完全是应用的其他部分，仍会重用 `id` 值 18。 `/Login` 页面上下文中的 `id` 路由值可能是用户 ID 值，而非存储产品 ID 值。

  在 ASP.NET Core 2.2 或更高版本的终结点路由中，结果为 `/Login`。 当链接的目标是另一个操作或页面时，不会重复使用环境值。

* 往返路由参数语法：使用双星号 (`**`) catch-all 参数语法时，不对正斜杠进行编码。

  在链接生成期间，路由系统对双星号 (`**`) catch-all 参数（例如，`{**myparametername}`）中捕获的除正斜杠外的值进行编码。 在 ASP.NET Core 2.2 或更高版本中，基于 `IRouter` 的路由支持双星号 catch-all 参数。

  ASP.NET Core (`{*myparametername}`) 早期版本中的单星号 catch-all 参数语法仍然受支持，并对正斜杠进行编码。

  | 路由              | 链接生成方式为<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts`（对正斜杠进行编码）             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>中间件示例

在以下示例中，中间件使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 创建列出存储产品的操作方法的链接。 应用中的任何类都可通过将链接生成器注入类并调用 `GenerateLink` 来使用链接生成器。

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>创建路由

大多数应用通过调用 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定义的一种类似的扩展方法来创建路由。 任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 扩展方法都会创建 <xref:Microsoft.AspNetCore.Routing.Route> 的实例并将其添加到路由集合中。

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由处理程序参数。 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。 要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。

以下代码示例是典型 ASP.NET Core MVC 路由定义使用的一个 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 调用示例：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

此模板与 URL 路径相匹配，并且提取路由值。 例如，路径 `/Products/Details/17` 生成以下路由值：`{ controller = Products, action = Details, id = 17 }`。

路由值是通过将 URL 路径拆分成段，并且将每段与路由模板中的路由参数名称相匹配来确定的  。 路由参数已命名。 参数通过将参数名称括在大括号 `{ ... }` 中来定义。

上述模板还可匹配 URL 路径 `/`，并且生成值 `{ controller = Home, action = Index }`。 这是因为 `{controller}` 和 `{action}` 路由参数具有默认值，且 `id` 路由参数是可选的。 路由参数名称为参数定义默认值后，等号 (`=`) 后将有一个值。 路由参数名称后面的问号 (`?`) 定义了可选参数。

路由匹配时，具有默认值的路由参数始终会生成路由值  。 如果没有相应的 URL 路径段，则可选参数不会生成路由值。 有关路由模板方案和语法的详细说明，请参阅[路由模板参考](#route-template-reference)部分。

在以下示例中，路由参数定义 `{id:int}` 为 `id` 路由参数定义[路由约束](#route-constraint-reference)：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

此模板与类似于 `/Products/Details/17` 而不是 `/Products/Details/Apples` 的 URL 路径相匹配。 路由约束实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 并检查路由值，以验证它们。 在此示例中，路由值 `id` 必须可转换为整数。 有关框架提供的路由约束的说明，请参阅[路由约束参考](#route-constraint-reference)。

其他 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 重载接受 `constraints`、`dataTokens` 和 `defaults` 的值。 这些参数的典型用法是传递匿名类型化对象，其中匿名类型的属性名匹配路由参数名称。

以下 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 示例可创建等效路由：

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> 定义约束和默认值的内联语法对于简单路由很方便。 但是，存在内联语法不支持的方案，例如数据令牌。

以下示例演示了一些其他方案：

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。 `controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。 可在路由模板中指定默认值。 根据路由参数名称前的双星号 (`**`) 外观，`article` 路由参数被定义为 catch-all  。 全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。

以下示例添加了路由约束和数据令牌：

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

上述模板与 `/en-US/Products/5` 等 URL 路径相匹配，并且提取值 `{ controller = Products, action = Details, id = 5 }` 和数据令牌 `{ locale = en-US }`。

![本地 Windows 令牌](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>路由类 URL 生成

<xref:Microsoft.AspNetCore.Routing.Route> 类还可通过将一组路由值与其路由模板组合来生成 URL。 从逻辑上来说，这是匹配 URL 路径的反向过程。

> [!TIP]
> 为更好了解 URL 生成，请想象你要生成的 URL，然后考虑路由模板将如何匹配该 URL。 将生成什么值？ 这大致相当于 URL 在 <xref:Microsoft.AspNetCore.Routing.Route> 类中的生成方式。

以下示例使用常规 ASP.NET Core MVC 默认路由：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

使用路由值 `{ controller = Products, action = List }`，将生成 URL `/Products/List`。 路由值将替换为相应的路由参数，以形成 URL 路径。 由于 `id` 是可选路由参数，因此成功生成的 URL 不具有 `id` 的值。

使用路由值 `{ controller = Home, action = Index }`，将生成 URL `/`。 提供的路由值与默认值匹配，并且会安全地省略与默认值对应的段。

已生成的两个 URL 将往返以下路由定义 (`/Home/Index` 和 `/`)，并生成用于生成该 URL 的相同路由值。

> [!NOTE]
> 使用 ASP.NET Core MVC 应用应该使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 生成 URL，而不是直接调用到路由。

有关 URL 生成的详细信息，请参阅 [Url 生成参考](#url-generation-reference)部分。

## <a name="use-routing-middleware"></a>使用路由中间件

引用应用项目文件中的 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。

将路由添加到 `Startup.ConfigureServices` 中的服务容器：

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

必须在 `Startup.Configure` 方法中配置路由。 该示例应用使用以下 API：

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; 仅匹配 HTTP GET 请求。
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

下表显示了具有给定 URI 的响应。

| URI                    | 响应                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Route values: [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/`      | 请求失败，不匹配。              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | 请求失败，仅匹配 HTTP GET。 |
| `GET /hello/Joe/Smith` | 请求失败，不匹配。              |

框架可提供一组用于创建路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) 的扩展方法：

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

`Map[Verb]` 方法将使用约束来将路由限制为方法名称中的 HTTP 谓词。 有关示例，请参阅 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 和 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。

## <a name="route-template-reference"></a>路由模板参考

如果路由找到匹配项，大括号 (`{ ... }`) 内的令牌定义绑定的路由参数  。 可在路由段中定义多个路由参数，但必须由文本值隔开。 例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。 这些路由参数必须具有名称，且可能指定了其他属性。

路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。 文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。 要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符（`{{` 或 `}}`）来转义分隔符。

尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。 例如，考虑模板 `files/{filename}.{ext?}`。 当 `filename` 和 `ext` 的值都存在时，将填充这两个值。 如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随句点 (`.`) 是可选的。 以下 URL 与此路由相匹配：

* `/files/myFile.txt`
* `/files/myFile`

可以使用星号 (`*`) 或双星号 (`**`) 作为路由参数的前缀，以绑定到 URI 的其余部分。 这些称为 catch-all 参数  。 例如，`blog/{**slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。 全方位参数还可以匹配空字符串。

使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。 例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。 请注意转义的正斜杠。 要往返路径分隔符，请使用 `**` 路由参数前缀。 `{ path = "my/path" }` 的路由 `foo/{**path}` 生成 `foo/my/path`。

路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值  。 例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。 如果参数的 URL 中不存在任何值，则使用默认值。 通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项，如 `id?` 中所示。 可选值和默认路径参数的区别在于具有默认值的路由参数始终会生成值 &mdash;，而可选参数仅当请求 URL 提供值时才会具有值。

路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。 在路由参数后面添加一个冒号 (`:`) 和约束名称可指定路由参数上的内联约束  。 如果约束需要参数，将以在约束名称后括在括号 (`(...)`) 中的形式提供。 通过追加另一个冒号 (`:`) 和约束名称，可指定多个内联约束。

约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。 例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。 有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。

路由参数还可以具有参数转换器，用于在生成链接以及将操作和页面匹配到 URI 时转换参数的值。 与约束类似，可以通过在路由参数名称后面添加冒号 (`:`) 和转换器名称，将参数变换器内联添加到路径参数。 例如，路由模板 `blog/{article:slugify}` 指定 `slugify` 转换器。 有关参数转换的详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。

下表演示了示例路由模板及其行为。

| 路由模板                           | 示例匹配 URI    | 请求 URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | 仅匹配单个路径 `/hello`。                                     |
| `{Page=Home}`                            | `/`                     | 匹配并将 `Page` 设置为 `Home`。                                         |
| `{Page=Home}`                            | `/Contact`              | 匹配并将 `Page` 设置为 `Contact`。                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | 映射到 `Products` 控制器和 `List` 操作。                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | 映射到 `Products` 控制器和 `Details` 操作（`id` 设置为 123）。 |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | 映射到 `Home` 控制器和 `Index` 方法（忽略 `id`）。        |

使用模板通常是进行路由最简单的方法。 还可在路由模板外指定约束和默认值。

> [!TIP]
> 启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。

## <a name="reserved-routing-names"></a>保留的路由名称

以下关键字是保留的名称，它们不能用作路由名称或参数：

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>路由约束参考

路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。 路径约束通常检查通过路径模板关联的路径值，并对该值是否可接受做出是/否决定。 某些路由约束使用路由值以外的数据来考虑是否可以路由请求。 例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。 约束用于路由请求和链接生成。

> [!WARNING]
> 请勿将约束用于“输入验证”  。 如果将约束用于“输入约束”，那么无效输入将导致“404 - 未找到”响应，而不是含相应错误消息的“400 - 错误请求”    。 路由约束用于消除类似路由的歧义，而不是验证特定路由的输入  。

下表演示示例路由约束及其预期行为。

| 约束 | 示例 | 匹配项示例 | 说明 |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`、`-123456789` | 匹配任何整数 |
| `bool` | `{active:bool}` | `true`、`FALSE` | 匹配 `true`或 `false`（区分大小写） |
| `datetime` | `{dob:datetime}` | `2016-12-31`、`2016-12-31 7:32pm` | 匹配有效的 `DateTime` 值（位于固定区域性中 - 查看警告） |
| `decimal` | `{price:decimal}` | `49.99`、`-1,000.01` | 匹配有效的 `decimal` 值（位于固定区域性中 - 查看警告） |
| `double` | `{weight:double}` | `1.234`、`-1,001.01e8` | 匹配有效的 `double` 值（位于固定区域性中 - 查看警告） |
| `float` | `{weight:float}` | `1.234`、`-1,001.01e8` | 匹配有效的 `float` 值（位于固定区域性中 - 查看警告） |
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`、`{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 匹配有效的 `Guid` 值 |
| `long` | `{ticks:long}` | `123456789`、`-123456789` | 匹配有效的 `long` 值 |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 字符串必须至少为 4 个字符 |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 字符串不得超过 8 个字符 |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 字符串必须正好为 12 个字符 |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 字符串必须至少为 8 个字符，且不得超过 16 个字符 |
| `min(value)` | `{age:min(18)}` | `19` | 整数值必须至少为 18 |
| `max(value)` | `{age:max(120)}` | `91` | 整数值不得超过 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 整数值必须至少为 18，且不得超过 120 |
| `alpha` | `{name:alpha}` | `Rick` | 字符串必须由一个或多个字母字符（`a`-`z`，区分大小写）组成 |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 字符串必须匹配正则表达式（参见有关定义正则表达式的提示） |
| `required` | `{name:required}` | `Rick` | 用于强制在 URL 生成过程中存在非参数值 |

可向单个参数应用多个由冒号分隔的约束。 例如，以下约束将参数限制为大于或等于 1 的整数值：

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> 验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。 这些约束假定 URL 不可本地化。 框架提供的路由约束不会修改存储于路由值中的值。 从 URL 中分析的所有路由值都将存储为字符串。 例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。

## <a name="regular-expressions"></a>正则表达式

ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。 有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。

正则表达式与路由和 C# 计算机语言使用的分隔符和令牌相似。 必须对正则表达式令牌进行转义。 要在路由中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`，表达式必须在字符串中提供 `\`（单反斜杠）字符，正如 C# 源文件中的 `\\`（双反斜杠）字符一样，以便对 `\` 字符串转义字符进行转义（除非使用[字符串文本](/dotnet/csharp/language-reference/keywords/string)）。 要对路由参数分隔符进行转义（`{`、`}`、`[`、`]`），请将表达式（`{{`、`}`、`[[`、`]]`）中的字符数加倍。 下表展示了正则表达式和转义版本。

| 正则表达式    | 转义后的正则表达式     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

路由中使用的正则表达式通常以脱字号 (`^`) 开头，并匹配字符串的起始位置。 表达式通常以美元符号 (`$`) 字符结尾，并匹配字符串的结尾。 `^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。 如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。 下表提供了示例并说明了它们匹配或匹配失败的原因。

| 表达式   | String    | 匹配 | 注释               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | 是   | 子字符串匹配     |
| `[a-z]{2}`   | 123abc456 | 是   | 子字符串匹配     |
| `[a-z]{2}`   | mz        | 是   | 匹配表达式    |
| `[a-z]{2}`   | MZ        | 是   | 不区分大小写    |
| `^[a-z]{2}$` | hello     | No    | 参阅上述 `^` 和 `$` |
| `^[a-z]{2}$` | 123abc456 | No    | 参阅上述 `^` 和 `$` |

有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。

若要将参数限制为一组已知的可能值，可使用正则表达式。 例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。 如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。 已传递到约束字典（不与模板内联）且不匹配任何已知约束的约束还将被视为正则表达式。

## <a name="custom-route-constraints"></a>自定义路由约束

除了内置路由约束以外，还可以通过实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口包含一个方法 `Match`，当满足约束时，它返回 `true`，否则返回 `false`。

若要使用自定义 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，必须在应用的服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是将路由约束键映射到验证这些约束的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 实现的目录。 应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。 例如:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

然后，可以使用在注册约束类型时指定的名称，以常规方式将约束应用于路由。 例如:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>参数转换器参考

参数转换器：

* 在为 <xref:Microsoft.AspNetCore.Routing.Route> 生成链接时执行。
* 实现 `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`。
* 使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 进行配置。
* 获取参数的路由值并将其转换为新的字符串值。
* 在生成的链接中使用转换后的值的结果。

例如，路由模式 `blog\{article:slugify}`（具有 `Url.Action(new { article = "MyTestArticle" })`）中的自定义 `slugify` 参数转换器生成 `blog\my-test-article`。

若要在路由模式中使用参数转换器，请先在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 对其进行配置：

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

框架使用参数转化器来转换进行终结点解析的 URI。 例如，ASP.NET Core MVC 使用参数转换器来转换用于匹配 `area` `controller` `action` 和 `page` 的路由值。

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

使用上述路由，操作 `SubscriptionManagementController.GetAll()` 与 URI `/subscription-management/get-all` 匹配。 参数转换器不会更改用于生成链接的路由值。 例如，`Url.Action("GetAll", "SubscriptionManagement")` 输出 `/subscription-management/get-all`。

对于结合使用参数转换器和所生成的路由，ASP.NET Core 提供了 API 约定：

* ASP.NET Core MVC 还具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 约定。 该约定将指定的参数转换器应用于应用中的所有属性路由。 在替换属性路径令牌时，参数转换器将转换这些令牌。 有关详细信息，请参阅[使用参数转换器自定义标记替换](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。
* Razor Pages 具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 约定。 此约定将指定的参数转换器应用于所有自动发现的 Razor Pages。 参数转换器转换 Razor Pages.路由的文件夹和文件名段。 有关详细信息，请参阅[使用参数转换器自定义页面路由](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。

## <a name="url-generation-reference"></a>URL 生成参考

以下示例演示如何在给定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情况下生成路由链接。

[!code-csharp[](routing/samples/3.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

上述示例末尾生成的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 为 `/package/create/123`。 字典提供“跟踪包路由”模板 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。 有关详细信息，请参阅[使用路由中间件](#use-routing-middleware)部分或[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的示例代码。

<xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 构造函数的第二个参数是环境值的集合  。 由于环境值限制了开发人员在请求上下文中必须指定的值数，因此环境值使用起来很方便。 当前请求的当前路由值被视为链接生成的环境值。 在 ASP.NET Core MVC 应用 `HomeController` 的 `About` 操作中，无需指定控制器路由值即可链接到使用 `Home` 环境值的 `Index` 操作&mdash;。

忽略与参数不匹配的环境值。 在显式提供的值会覆盖环境值的情况下，也会忽略环境值。 在 URL 中将从左到右进行匹配。

显式提供但与路由片段不匹配的值将添加到查询字符串中。 下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。

| 环境值                     | 显式值                        | 结果                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| 控制器 =“Home”                | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 控制器 =“Order”，操作 =“About” | `/Order/About`          |
| 控制器 = “Home”，颜色 = “Red” | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 操作 =“About”，颜色 =“Red”        | `/Home/About?color=Red` |

如果路由具有不对应于参数的默认值，且该值以显式方式提供，则它必须与默认值相匹配：

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

当提供 `controller` 和 `action` 的匹配值时，链接生成仅为此路由生成链接。

## <a name="complex-segments"></a>复杂段

复杂段（例如，`[Route("/x{token}y")]`）通过非贪婪的方式从右到左匹配文字进行处理。 请参阅[此代码](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解有关如何匹配复杂段的详细说明。 ASP.NET Core 无法使用[代码示例](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)，但它提供了对复杂段的合理说明。
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

## <a name="configuring-endpoint-metadata"></a>配置终结点元数据

以下链接提供有关配置终结点元数据的信息：

* [通过终结点路由启用 Cors](xref:security/cors#enable-cors-with-endpoint-routing)
* 使用自定义 `[MinimumAgeAuthorize]` 属性的 [IAuthorizationPolicyProvider 示例](https://github.com/aspnet/AspNetCore/tree/release/3.0/src/Security/samples/CustomPolicyProvider)
* [使用 [Authorize] 属性测试身份验证](xref:security/authentication/identity#test-identity)
* <xref:Microsoft.AspNetCore.Builder.AuthorizationEndpointConventionBuilderExtensions.RequireAuthorization*>
* [使用 [Authorize] 属性选择方案](xref:security/authorization/limitingidentitybyscheme#selecting-the-scheme-with-the-authorize-attribute)
* [使用 [Authorize] 属性应用策略](xref:security/authorization/policies#applying-policies-to-mvc-controllers)
* <xref:security/authorization/roles>

<a name="hostmatch"></a>

## <a name="host-matching-in-routes-with-requirehost"></a>路由中与 RequireHost 匹配的主机

`RequireHost` 将约束应用于需要指定主机的路由。 `RequireHost` 或 `[Host]` 参数可以是：

* 主机：`www.domain.com`（匹配任何端口的 `www.domain.com`）
* 带有通配符的主机：`*.domain.com`（匹配任何端口上的 `www.domain.com`、`subdomain.domain.com` 或 `www.subdomain.domain.com`）
* 端口：`*:5000`（与任何主机的端口 5000 匹配）
* 主机和端口：`www.domain.com:5000`、`*.domain.com:5000`（匹配主机和端口）

可以使用 `RequireHost` 或 `[Host]` 指定多个参数。 约束将匹配对任何参数有效的主机。 例如，`[Host("domain.com", "*.domain.com")]` 将匹配 `domain.com`、`www.domain.com` 或 `subdomain.domain.com`。

以下代码使用 `RequireHost` 来要求路由上的指定主机：

```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapGet("/", context => context.Response.WriteAsync("Hi Contoso!"))
            .RequireHost("contoso.com");
        endpoints.MapGet("/", context => context.Response.WriteAsync("Hi AdventureWorks!"))
            .RequireHost("adventure-works.com");
        endpoints.MapHealthChecks("/healthz").RequireHost("*:8080");
    });
}
```

以下代码使用 `[Host]` 属性来要求控制器上的指定主机：

```csharp
[Host("contoso.com", "adventure-works.com")]
public class HomeController : Controller
{
    private readonly ILogger<HomeController> _logger;

    public HomeController(ILogger<HomeController> logger)
    {
        _logger = logger;
    }

    public IActionResult Index()
    {
        return View();
    }

    [Host("example.com:8080")]
    public IActionResult Privacy()
    {
        return View();
    }

}
```

当 `[Host]` 属性同时应用于控制器和操作方法时：

* 使用操作上的属性。
* 忽略控制器属性。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

路由负责将请求 URI 映射到终结点并向这些终结点调度传入的请求。 路由在应用中定义，并在应用启动时进行配置。 路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。 通过使用应用中的路由信息，路由还能生成映射到终结点的 URL。

要在 ASP.NET Core 2.2 中使用最新路由方案，请在 `Startup.ConfigureServices` 中为 MVC 服务注册指定[兼容性版本](xref:mvc/compatibility-version)：

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

<xref:Microsoft.AspNetCore.Mvc.MvcOptions.EnableEndpointRouting> 选项确定路由是应在内部使用 ASP.NET Core 2.1 或更早版本的基于终结点的逻辑还是使用其基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的逻辑。 兼容性版本设置为 2.2 或更高版本时，默认值为 `true`。 将值设置为 `false` 以使用先前的路由逻辑：

```csharp
// Use the routing logic of ASP.NET Core 2.1 or earlier:
services.AddMvc(options => options.EnableEndpointRouting = false)
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
```

有关基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由的详细信息，请参阅本主题的 [ASP.NET Core 2.1 版本](/aspnet/core/fundamentals/routing?view=aspnetcore-2.1)。

> [!IMPORTANT]
> 本文档介绍较低级别的 ASP.NET Core 路由。 有关 ASP.NET Core MVC 路由的信息，请参阅 <xref:mvc/controllers/routing>。 有关 Razor Pages 中路由约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions>。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="routing-basics"></a>路由基础知识

大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。 默认传统路由 `{controller=Home}/{action=Index}/{id?}`：

* 支持基本的描述性路由方案。
* 是基于 UI 的应用的有用起点。

开发人员通常在专业情况下（例如，博客和电子商务终结点）使用[属性路由](xref:mvc/controllers/routing#attribute-routing)或专用传统路由向应用的高流量区域添加其他简洁路由。

Web API 应使用属性路由，将应用功能建模为一组资源，其中操作是由 HTTP 谓词表示。 也就是说，对同一逻辑资源执行的许多操作（例如，GET、POST）都将使用相同 URL。 属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。

Razor Pages 应用使用默认的传统路由从应用的“页面”文件夹中提供命名资源  。 还可以使用其他约定来自定义 Razor Pages 路由行为。 有关详细信息，请参阅 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。

借助 URL 生成支持，无需通过硬编码 URL 将应用关联到一起，即可开发应用。 此支持允许从基本路由配置入手，并在确定应用的资源布局后修改路由。

路由使用“终结点”(`Endpoint`) 来表示应用中的逻辑终结点  。

终结点定义用于处理请求的委托和任意元数据的集合。 元数据用于实现横切关注点，该实现基于附加到每个终结点的策略和配置。

路由系统具有以下特征：

* 路由模板语法用于通过标记化路由参数来定义路由。
* 可以使用常规样式和属性样式终结点配置。
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 用于确定 URL 参数是否包含给定的终结点约束的有效值。
* 应用模型（如 MVC/Razor Pages）注册其所有终结点，这些终结点具有可预测的路由方案实现。
* 路由实现会在中间件管道中任何所需位置制定路由决策。
* 路由中间件之后出现的中间件可以检查路由中间件针对给定请求 URI 的终结点决策结果。
* 可以在中间件管道中的任何位置枚举应用中的所有终结点。
* 应用可根据终结点信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。
* URL 生成是基于支持任意可扩展性的地址：

  * 可以使用[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 在任意位置解析链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>) 以生成 URL。
  * 如果无法通过 DI 获得链接生成器 API，则 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 会提供生成 URL 的方法。

> [!NOTE]
> 在 ASP.NET Core 2.2 中发布终结点路由后，终结点链接的适用范围限制为 MVC/Razor Pages 操作和页面。 将计划在未来发布的版本中扩展终结点链接的功能。

路由通过 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 类连接到[中间件](xref:fundamentals/middleware/index)管道。 [ASP.NET Core MVC](xref:mvc/overview) 向中间件管道添加路由，作为其配置的一部分，并处理 MVC 和 Razor Pages 应用中的路由。 要了解如何将路由用作独立组件，请参阅[使用路由中间件](#use-routing-middleware)部分。

### <a name="url-matching"></a>URL 匹配

URL 匹配是路由向终结点调度传入请求的过程  。 此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。 向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。

终结点路由中的路由系统负责所有的调度决策。 由于中间件是基于所选终结点来应用策略，因此任何可能影响调度或安全策略应用的决策都应在路由系统内部制定，这一点很重要。

执行终结点委托时，将根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是从路由中生成的路由值的字典  。 这些值通常通过标记 URL 来确定，可用来接受用户输入，或者在应用内作出进一步的调度决策。

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是一个与匹配的路由相关的其他数据的属性包。 提供 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 以支持将状态数据与每个路由相关联，以便应用可根据所匹配的路由作出决策。 这些值是开发者定义的，不会影响通过任何方式路由的行为  。 此外，存储于 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以属于任何类型，与 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 相反，后者必须能够转换为字符串，或从字符串进行转换。

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是参与成功匹配请求的路由的列表。 路由可以相互嵌套。 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 属性可以通过导致匹配的逻辑路由树反映该路径。 通常情况下，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一项是路由集合，应该用于生成 URL。 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最后一项是匹配的路由处理程序。

<a name="lg"></a>

### <a name="url-generation-with-linkgenerator"></a>使用 LinkGenerator 的 URL 生成

URL 生成是通过其可根据一组路由值创建 URL 路径的过程。 这需要考虑终结点与访问它们的 URL 之间的逻辑分隔。

终结点路由包含链接生成器 API (<xref:Microsoft.AspNetCore.Routing.LinkGenerator>)。 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 是可从 DI 检索的单一实例服务。 该 API 可在执行请求的上下文之外使用。 MVC 的 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 和依赖 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 的方案（如 [Tag Helpers](xref:mvc/views/tag-helpers/intro)、HTML Helpers 和 [Action Results](xref:mvc/controllers/actions)）使用链接生成器提供链接生成功能。

链接生成器基于“地址”和“地址方案”概念   。 地址方案是确定哪些终结点用于链接生成的方式。 例如，许多用户熟悉的来自 MVC/Razor Pages 的路由名称和路由值方案都是作为地址方案实现的。

链接生成器可以通过以下扩展方法链接到 MVC/Razor Pages 操作和页面：

* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetPathByAction*>
* <xref:Microsoft.AspNetCore.Routing.ControllerLinkGeneratorExtensions.GetUriByAction*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetPathByPage*>
* <xref:Microsoft.AspNetCore.Routing.PageLinkGeneratorExtensions.GetUriByPage*>

这些方法的重载接受包含 `HttpContext` 的参数。 这些方法在功能上等同于 `Url.Action` 和 `Url.Page`，但提供了更大的灵活性和更多的选项。

`GetPath*` 方法与 `Url.Action` 和 `Url.Page` 最相似，因为它们生成包含绝对路径的 URI。 `GetUri*` 方法始终生成包含方案和主机的绝对 URI。 接受 `HttpContext` 的方法在执行请求的上下文中生成 URI。 除非重写，否则将使用来自执行请求的环境路由值、URL 基本路径、方案和主机。

使用地址调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator>。 生成 URI 的过程分两步进行：

1. 将地址绑定到与地址匹配的终结点列表。
1. 计算每个终结点的 `RoutePattern`，直到找到与提供的值匹配的路由模式。 输出结果会与提供给链接生成器的其他 URI 部分进行组合并返回。

对任何类型的地址，<xref:Microsoft.AspNetCore.Routing.LinkGenerator> 提供的方法均支持标准链接生成功能。 使用链接生成器的最简便方法是通过扩展方法对特定地址类型执行操作。

| 扩展方法   | 说明                                                         |
| ------------------ | ------------------------------------------------------------------- |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetPathByAddress*> | 根据提供的值生成具有绝对路径的 URI。 |
| <xref:Microsoft.AspNetCore.Routing.LinkGenerator.GetUriByAddress*> | 根据提供的值生成绝对 URI。             |

> [!WARNING]
> 请注意有关调用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 方法的下列含义：
>
> * 对于不验证传入请求的 `Host` 标头的应用配置，请谨慎使用 `GetUri*` 扩展方法。 如果未验证传入请求的 `Host`标头，则可能以视图/页面中 URI 的形式将不受信任的请求输入发送回客户端。 建议所有生产应用都将其服务器配置为针对已知有效值验证 `Host` 标头。
>
> * 在中间件中将 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> 与 `Map` 或 `MapWhen` 结合使用时，请小心谨慎。 `Map*` 会更改执行请求的基路径，这会影响链接生成的输出。 所有 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 都允许指定基路径。 始终指定一个空的基路径来撤消 `Map*` 对链接生成的影响。

## <a name="differences-from-earlier-versions-of-routing"></a>与早期版本路由的差异

ASP.NET Core 2.2 或更高版本中的终结点路由与 ASP.NET Core 中早期版本的路由之间存在一些差异：

* 终结点路由系统不支持基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的可扩展性，包括从 <xref:Microsoft.AspNetCore.Routing.Route> 继承。

* 终结点路由不支持 [WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)。 使用 2.1 [兼容性版本](xref:mvc/compatibility-version) (`.SetCompatibilityVersion(CompatibilityVersion.Version_2_1)`) 以继续使用兼容性填充码。

* 在使用传统路由时，终结点路由对生成的 URI 的大小写具有不同的行为。

  请考虑以下默认路由模板：

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  假设使用以下路由生成某个操作的链接：

  ```csharp
  var link = Url.Action("ReadPost", "blog", new { id = 17, });
  ```

  如果使用基于 <xref:Microsoft.AspNetCore.Routing.IRouter> 的路由，此代码生成 URI `/blog/ReadPost/17`，该 URI 遵循所提供路由值的大小写。 ASP.NET Core 2.2 或更高版本中的终结点路由生成 `/Blog/ReadPost/17`（“Blog”大写）。 终结点路由提供 `IOutboundParameterTransformer` 接口，可用于在全局范围自定义此行为或应用不同的约定来映射 URL。

  有关详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。

* 在试图链接到不存在的控制器/操作或页面时，MVC/Razor Pages 通过传统路由执行的链接生成，其操作具有不同的行为。

  请考虑以下默认路由模板：

  ```csharp
  app.UseMvc(routes =>
  {
      routes.MapRoute("default", "{controller=Home}/{action=Index}/{id?}");
  });
  ```

  假设使用以下路由通过默认模板生成某个操作的链接：

  ```csharp
  var link = Url.Action("ReadPost", "Blog", new { id = 17, });
  ```

  如果使用基于 `IRouter` 的路由，即使 `BlogController` 不存在或没有 `ReadPost` 操作方法，结果也始终为 `/Blog/ReadPost/17`。 正如所料，如果操作方法存在，ASP.NET Core 2.2 或更高版本中的终结点路由会生成 `/Blog/ReadPost/17`。 但是，如果操作不存在，终结点路由会生成空字符串。  从概念上讲，如果操作不存在，终结点路由不会假定终结点存在。

* 与终结点路由一起使用时，链接生成环境值失效算法的行为会有所不同  。

  *环境值失效*是一种算法，用于决定当前正在执行的请求（环境值）中的哪些路由值可用于链接生成操作。 链接到不同操作时，传统路由会使额外的路由值失效。 ASP.NET Core 2.2 之前的版本中，属性路由不具有此行为。 在 ASP.NET Core 的早期版本中，如果有另一个操作使用同一路由参数名称，则该操作的链接会导致发生链接生成错误。 在 ASP.NET Core 2.2 或更高版本中，链接到另一个操作时，这两种路由形式都会使值失效。

  请考虑 ASP.NET Core2.1 或更高版本中的以下示例。 链接到另一个操作（或另一页面）时，路由值可能会按非预期的方式被重用。

  在 /Pages/Store/Product.cshtml 中  ：

  ```cshtml
  @page "{id}"
  @Url.Page("/Login")
  ```

  在 /Pages/Login.cshtml 中  ：

  ```cshtml
  @page "{id?}"
  ```

  如果 ASP.NET Core 2.1 或更早版本中的 URI 为 `/Store/Product/18`，则由 `@Url.Page("/Login")` 在 Store/Info 页面上生成的链接为 `/Login/18`。 即使链接目标完全是应用的其他部分，仍会重用 `id` 值 18。 `/Login` 页面上下文中的 `id` 路由值可能是用户 ID 值，而非存储产品 ID 值。

  在 ASP.NET Core 2.2 或更高版本的终结点路由中，结果为 `/Login`。 当链接的目标是另一个操作或页面时，不会重复使用环境值。

* 往返路由参数语法：使用双星号 (`**`) catch-all 参数语法时，不对正斜杠进行编码。

  在链接生成期间，路由系统对双星号 (`**`) catch-all 参数（例如，`{**myparametername}`）中捕获的除正斜杠外的值进行编码。 在 ASP.NET Core 2.2 或更高版本中，基于 `IRouter` 的路由支持双星号 catch-all 参数。

  ASP.NET Core (`{*myparametername}`) 早期版本中的单星号 catch-all 参数语法仍然受支持，并对正斜杠进行编码。

  | 路由              | 链接生成方式为<br>`Url.Action(new { category = "admin/products" })`&hellip; |
  | ------------------ | --------------------------------------------------------------------- |
  | `/search/{*page}`  | `/search/admin%2Fproducts`（对正斜杠进行编码）             |
  | `/search/{**page}` | `/search/admin/products`                                              |

### <a name="middleware-example"></a>中间件示例

在以下示例中，中间件使用 <xref:Microsoft.AspNetCore.Routing.LinkGenerator> API 创建列出存储产品的操作方法的链接。 应用中的任何类都可通过将链接生成器注入类并调用 `GenerateLink` 来使用链接生成器。

```csharp
using Microsoft.AspNetCore.Routing;

public class ProductsLinkMiddleware
{
    private readonly LinkGenerator _linkGenerator;

    public ProductsLinkMiddleware(RequestDelegate next, LinkGenerator linkGenerator)
    {
        _linkGenerator = linkGenerator;
    }

    public async Task InvokeAsync(HttpContext httpContext)
    {
        var url = _linkGenerator.GetPathByAction("ListProducts", "Store");

        httpContext.Response.ContentType = "text/plain";

        await httpContext.Response.WriteAsync($"Go to {url} to see our products.");
    }
}
```

### <a name="create-routes"></a>创建路由

大多数应用通过调用 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定义的一种类似的扩展方法来创建路由。 任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 扩展方法都会创建 <xref:Microsoft.AspNetCore.Routing.Route> 的实例并将其添加到路由集合中。

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由处理程序参数。 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。 要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。

以下代码示例是典型 ASP.NET Core MVC 路由定义使用的一个 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 调用示例：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

此模板与 URL 路径相匹配，并且提取路由值。 例如，路径 `/Products/Details/17` 生成以下路由值：`{ controller = Products, action = Details, id = 17 }`。

路由值是通过将 URL 路径拆分成段，并且将每段与路由模板中的路由参数名称相匹配来确定的  。 路由参数已命名。 参数通过将参数名称括在大括号 `{ ... }` 中来定义。

上述模板还可匹配 URL 路径 `/`，并且生成值 `{ controller = Home, action = Index }`。 这是因为 `{controller}` 和 `{action}` 路由参数具有默认值，且 `id` 路由参数是可选的。 路由参数名称为参数定义默认值后，等号 (`=`) 后将有一个值。 路由参数名称后面的问号 (`?`) 定义了可选参数。

路由匹配时，具有默认值的路由参数始终会生成路由值  。 如果没有相应的 URL 路径段，则可选参数不会生成路由值。 有关路由模板方案和语法的详细说明，请参阅[路由模板参考](#route-template-reference)部分。

在以下示例中，路由参数定义 `{id:int}` 为 `id` 路由参数定义[路由约束](#route-constraint-reference)：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

此模板与类似于 `/Products/Details/17` 而不是 `/Products/Details/Apples` 的 URL 路径相匹配。 路由约束实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 并检查路由值，以验证它们。 在此示例中，路由值 `id` 必须可转换为整数。 有关框架提供的路由约束的说明，请参阅[路由约束参考](#route-constraint-reference)。

其他 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 重载接受 `constraints`、`dataTokens` 和 `defaults` 的值。 这些参数的典型用法是传递匿名类型化对象，其中匿名类型的属性名匹配路由参数名称。

以下 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 示例可创建等效路由：

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> 定义约束和默认值的内联语法对于简单路由很方便。 但是，存在内联语法不支持的方案，例如数据令牌。

以下示例演示了一些其他方案：

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{**article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。 `controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。 可在路由模板中指定默认值。 根据路由参数名称前的双星号 (`**`) 外观，`article` 路由参数被定义为 catch-all  。 全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。

以下示例添加了路由约束和数据令牌：

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

上述模板与 `/en-US/Products/5` 等 URL 路径相匹配，并且提取值 `{ controller = Products, action = Details, id = 5 }` 和数据令牌 `{ locale = en-US }`。

![本地 Windows 令牌](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>路由类 URL 生成

<xref:Microsoft.AspNetCore.Routing.Route> 类还可通过将一组路由值与其路由模板组合来生成 URL。 从逻辑上来说，这是匹配 URL 路径的反向过程。

> [!TIP]
> 为更好了解 URL 生成，请想象你要生成的 URL，然后考虑路由模板将如何匹配该 URL。 将生成什么值？ 这大致相当于 URL 在 <xref:Microsoft.AspNetCore.Routing.Route> 类中的生成方式。

以下示例使用常规 ASP.NET Core MVC 默认路由：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

使用路由值 `{ controller = Products, action = List }`，将生成 URL `/Products/List`。 路由值将替换为相应的路由参数，以形成 URL 路径。 由于 `id` 是可选路由参数，因此成功生成的 URL 不具有 `id` 的值。

使用路由值 `{ controller = Home, action = Index }`，将生成 URL `/`。 提供的路由值与默认值匹配，并且会安全地省略与默认值对应的段。

已生成的两个 URL 将往返以下路由定义 (`/Home/Index` 和 `/`)，并生成用于生成该 URL 的相同路由值。

> [!NOTE]
> 使用 ASP.NET Core MVC 应用应该使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 生成 URL，而不是直接调用到路由。

有关 URL 生成的详细信息，请参阅 [Url 生成参考](#url-generation-reference)部分。

## <a name="use-routing-middleware"></a>使用路由中间件

引用应用项目文件中的 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。

将路由添加到 `Startup.ConfigureServices` 中的服务容器：

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

必须在 `Startup.Configure` 方法中配置路由。 该示例应用使用以下 API：

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; 仅匹配 HTTP GET 请求。
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

下表显示了具有给定 URI 的响应。

| URI                    | 响应                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Route values: [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/`      | 请求失败，不匹配。              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | 请求失败，仅匹配 HTTP GET。 |
| `GET /hello/Joe/Smith` | 请求失败，不匹配。              |

框架可提供一组用于创建路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) 的扩展方法：

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

`Map[Verb]` 方法将使用约束来将路由限制为方法名称中的 HTTP 谓词。 有关示例，请参阅 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 和 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。

## <a name="route-template-reference"></a>路由模板参考

如果路由找到匹配项，大括号 (`{ ... }`) 内的令牌定义绑定的路由参数  。 可在路由段中定义多个路由参数，但必须由文本值隔开。 例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。 这些路由参数必须具有名称，且可能指定了其他属性。

路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。 文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。 要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符（`{{` 或 `}}`）来转义分隔符。

尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。 例如，考虑模板 `files/{filename}.{ext?}`。 当 `filename` 和 `ext` 的值都存在时，将填充这两个值。 如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随句点 (`.`) 是可选的。 以下 URL 与此路由相匹配：

* `/files/myFile.txt`
* `/files/myFile`

可以使用星号 (`*`) 或双星号 (`**`) 作为路由参数的前缀，以绑定到 URI 的其余部分。 这些称为 catch-all 参数  。 例如，`blog/{**slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。 全方位参数还可以匹配空字符串。

使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。 例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。 请注意转义的正斜杠。 要往返路径分隔符，请使用 `**` 路由参数前缀。 `{ path = "my/path" }` 的路由 `foo/{**path}` 生成 `foo/my/path`。

路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值  。 例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。 如果参数的 URL 中不存在任何值，则使用默认值。 通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项，如 `id?` 中所示。 可选值和默认路径参数的区别在于具有默认值的路由参数始终会生成值 &mdash;，而可选参数仅当请求 URL 提供值时才会具有值。

路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。 在路由参数后面添加一个冒号 (`:`) 和约束名称可指定路由参数上的内联约束  。 如果约束需要参数，将以在约束名称后括在括号 (`(...)`) 中的形式提供。 通过追加另一个冒号 (`:`) 和约束名称，可指定多个内联约束。

约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。 例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。 有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。

路由参数还可以具有参数转换器，用于在生成链接以及将操作和页面匹配到 URI 时转换参数的值。 与约束类似，可以通过在路由参数名称后面添加冒号 (`:`) 和转换器名称，将参数变换器内联添加到路径参数。 例如，路由模板 `blog/{article:slugify}` 指定 `slugify` 转换器。 有关参数转换的详细信息，请参阅[参数转换器参考](#parameter-transformer-reference)部分。

下表演示了示例路由模板及其行为。

| 路由模板                           | 示例匹配 URI    | 请求 URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | 仅匹配单个路径 `/hello`。                                     |
| `{Page=Home}`                            | `/`                     | 匹配并将 `Page` 设置为 `Home`。                                         |
| `{Page=Home}`                            | `/Contact`              | 匹配并将 `Page` 设置为 `Contact`。                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | 映射到 `Products` 控制器和 `List` 操作。                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | 映射到 `Products` 控制器和 `Details` 操作（`id` 设置为 123）。 |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | 映射到 `Home` 控制器和 `Index` 方法（忽略 `id`）。        |

使用模板通常是进行路由最简单的方法。 还可在路由模板外指定约束和默认值。

> [!TIP]
> 启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。

## <a name="reserved-routing-names"></a>保留的路由名称

以下关键字是保留的名称，它们不能用作路由名称或参数：

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>路由约束参考

路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。 路径约束通常检查通过路径模板关联的路径值，并对该值是否可接受做出是/否决定。 某些路由约束使用路由值以外的数据来考虑是否可以路由请求。 例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。 约束用于路由请求和链接生成。

> [!WARNING]
> 请勿将约束用于“输入验证”  。 如果将约束用于“输入约束”，那么无效输入将导致“404 - 未找到”响应，而不是含相应错误消息的“400 - 错误请求”    。 路由约束用于消除类似路由的歧义，而不是验证特定路由的输入  。

下表演示示例路由约束及其预期行为。

| 约束 | 示例 | 匹配项示例 | 说明 |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`、`-123456789` | 匹配任何整数 |
| `bool` | `{active:bool}` | `true`、`FALSE` | 匹配 `true`或 `false`（区分大小写） |
| `datetime` | `{dob:datetime}` | `2016-12-31`、`2016-12-31 7:32pm` | 匹配有效的 `DateTime` 值（位于固定区域性中 - 查看警告） |
| `decimal` | `{price:decimal}` | `49.99`、`-1,000.01` | 匹配有效的 `decimal` 值（位于固定区域性中 - 查看警告） |
| `double` | `{weight:double}` | `1.234`、`-1,001.01e8` | 匹配有效的 `double` 值（位于固定区域性中 - 查看警告） |
| `float` | `{weight:float}` | `1.234`、`-1,001.01e8` | 匹配有效的 `float` 值（位于固定区域性中 - 查看警告） |
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`、`{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 匹配有效的 `Guid` 值 |
| `long` | `{ticks:long}` | `123456789`、`-123456789` | 匹配有效的 `long` 值 |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 字符串必须至少为 4 个字符 |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 字符串不得超过 8 个字符 |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 字符串必须正好为 12 个字符 |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 字符串必须至少为 8 个字符，且不得超过 16 个字符 |
| `min(value)` | `{age:min(18)}` | `19` | 整数值必须至少为 18 |
| `max(value)` | `{age:max(120)}` | `91` | 整数值不得超过 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 整数值必须至少为 18，且不得超过 120 |
| `alpha` | `{name:alpha}` | `Rick` | 字符串必须由一个或多个字母字符（`a`-`z`，区分大小写）组成 |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 字符串必须匹配正则表达式（参见有关定义正则表达式的提示） |
| `required` | `{name:required}` | `Rick` | 用于强制在 URL 生成过程中存在非参数值 |

可向单个参数应用多个由冒号分隔的约束。 例如，以下约束将参数限制为大于或等于 1 的整数值：

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> 验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。 这些约束假定 URL 不可本地化。 框架提供的路由约束不会修改存储于路由值中的值。 从 URL 中分析的所有路由值都将存储为字符串。 例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。

## <a name="regular-expressions"></a>正则表达式

ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。 有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。

正则表达式与路由和 C# 计算机语言使用的分隔符和令牌相似。 必须对正则表达式令牌进行转义。 要在路由中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`，表达式必须在字符串中提供 `\`（单反斜杠）字符，正如 C# 源文件中的 `\\`（双反斜杠）字符一样，以便对 `\` 字符串转义字符进行转义（除非使用[字符串文本](/dotnet/csharp/language-reference/keywords/string)）。 要对路由参数分隔符进行转义（`{`、`}`、`[`、`]`），请将表达式（`{{`、`}`、`[[`、`]]`）中的字符数加倍。 下表展示了正则表达式和转义版本。

| 正则表达式    | 转义后的正则表达式     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

路由中使用的正则表达式通常以脱字号 (`^`) 开头，并匹配字符串的起始位置。 表达式通常以美元符号 (`$`) 字符结尾，并匹配字符串的结尾。 `^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。 如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。 下表提供了示例并说明了它们匹配或匹配失败的原因。

| 表达式   | String    | 匹配 | 注释               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | 是   | 子字符串匹配     |
| `[a-z]{2}`   | 123abc456 | 是   | 子字符串匹配     |
| `[a-z]{2}`   | mz        | 是   | 匹配表达式    |
| `[a-z]{2}`   | MZ        | 是   | 不区分大小写    |
| `^[a-z]{2}$` | hello     | No    | 参阅上述 `^` 和 `$` |
| `^[a-z]{2}$` | 123abc456 | No    | 参阅上述 `^` 和 `$` |

有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。

若要将参数限制为一组已知的可能值，可使用正则表达式。 例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。 如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。 已传递到约束字典（不与模板内联）且不匹配任何已知约束的约束还将被视为正则表达式。

## <a name="custom-route-constraints"></a>自定义路由约束

除了内置路由约束以外，还可以通过实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口包含一个方法 `Match`，当满足约束时，它返回 `true`，否则返回 `false`。

若要使用自定义 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，必须在应用的服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是将路由约束键映射到验证这些约束的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 实现的目录。 应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。 例如:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

然后，可以使用在注册约束类型时指定的名称，以常规方式将约束应用于路由。 例如:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="parameter-transformer-reference"></a>参数转换器参考

参数转换器：

* 在为 <xref:Microsoft.AspNetCore.Routing.Route> 生成链接时执行。
* 实现 `Microsoft.AspNetCore.Routing.IOutboundParameterTransformer`。
* 使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 进行配置。
* 获取参数的路由值并将其转换为新的字符串值。
* 在生成的链接中使用转换后的值的结果。

例如，路由模式 `blog\{article:slugify}`（具有 `Url.Action(new { article = "MyTestArticle" })`）中的自定义 `slugify` 参数转换器生成 `blog\my-test-article`。

若要在路由模式中使用参数转换器，请先在 `Startup.ConfigureServices` 中使用 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 对其进行配置：

```csharp
services.AddRouting(options =>
{
    // Replace the type and the name used to refer to it with your own
    // IOutboundParameterTransformer implementation
    options.ConstraintMap["slugify"] = typeof(SlugifyParameterTransformer);
});
```

框架使用参数转化器来转换进行终结点解析的 URI。 例如，ASP.NET Core MVC 使用参数转换器来转换用于匹配 `area` `controller` `action` 和 `page` 的路由值。

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller:slugify=Home}/{action:slugify=Index}/{id?}");
```

使用上述路由，操作 `SubscriptionManagementController.GetAll()` 与 URI `/subscription-management/get-all` 匹配。 参数转换器不会更改用于生成链接的路由值。 例如，`Url.Action("GetAll", "SubscriptionManagement")` 输出 `/subscription-management/get-all`。

对于结合使用参数转换器和所生成的路由，ASP.NET Core 提供了 API 约定：

* ASP.NET Core MVC 还具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.RouteTokenTransformerConvention` API 约定。 该约定将指定的参数转换器应用于应用中的所有属性路由。 在替换属性路径令牌时，参数转换器将转换这些令牌。 有关详细信息，请参阅[使用参数转换器自定义标记替换](/aspnet/core/mvc/controllers/routing#use-a-parameter-transformer-to-customize-token-replacement)。
* Razor Pages 具有 `Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteTransformerConvention` API 约定。 此约定将指定的参数转换器应用于所有自动发现的 Razor Pages。 参数转换器转换 Razor Pages.路由的文件夹和文件名段。 有关详细信息，请参阅[使用参数转换器自定义页面路由](/aspnet/core/razor-pages/razor-pages-conventions#use-a-parameter-transformer-to-customize-page-routes)。

## <a name="url-generation-reference"></a>URL 生成参考

以下示例演示如何在给定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情况下生成路由链接。

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

上述示例末尾生成的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 为 `/package/create/123`。 字典提供“跟踪包路由”模板 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。 有关详细信息，请参阅[使用路由中间件](#use-routing-middleware)部分或[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的示例代码。

<xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 构造函数的第二个参数是环境值的集合  。 由于环境值限制了开发人员在请求上下文中必须指定的值数，因此环境值使用起来很方便。 当前请求的当前路由值被视为链接生成的环境值。 在 ASP.NET Core MVC 应用 `HomeController` 的 `About` 操作中，无需指定控制器路由值即可链接到使用 `Home` 环境值的 `Index` 操作&mdash;。

忽略与参数不匹配的环境值。 在显式提供的值会覆盖环境值的情况下，也会忽略环境值。 在 URL 中将从左到右进行匹配。

显式提供但与路由片段不匹配的值将添加到查询字符串中。 下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。

| 环境值                     | 显式值                        | 结果                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| 控制器 =“Home”                | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 控制器 =“Order”，操作 =“About” | `/Order/About`          |
| 控制器 = “Home”，颜色 = “Red” | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 操作 =“About”，颜色 =“Red”        | `/Home/About?color=Red` |

如果路由具有不对应于参数的默认值，且该值以显式方式提供，则它必须与默认值相匹配：

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

当提供 `controller` 和 `action` 的匹配值时，链接生成仅为此路由生成链接。

## <a name="complex-segments"></a>复杂段

复杂段（例如，`[Route("/x{token}y")]`）通过非贪婪的方式从右到左匹配文字进行处理。 请参阅[此代码](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解有关如何匹配复杂段的详细说明。 ASP.NET Core 无法使用[代码示例](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)，但它提供了对复杂段的合理说明。
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end

::: moniker range="< aspnetcore-2.2"

路由负责将请求 URI 映射到路由处理程序和调度传入的请求。 路由在应用中定义，并在应用启动时进行配置。 路由可以选择从请求包含的 URL 中提取值，然后这些值便可用于处理请求。 如果使用应用中配置的路由，路由能生成映射到路由处理程序的 URL。

要在 ASP.NET Core 2.1 中使用最新路由方案，请在 `Startup.ConfigureServices` 中为 MVC 服务注册指定[兼容性版本](xref:mvc/compatibility-version)：

```csharp
services.AddMvc()
    .SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
```

> [!IMPORTANT]
> 本文档介绍较低级别的 ASP.NET Core 路由。 有关 ASP.NET Core MVC 路由的信息，请参阅 <xref:mvc/controllers/routing>。 有关 Razor Pages 中路由约定的信息，请参阅 <xref:razor-pages/razor-pages-conventions>。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="routing-basics"></a>路由基础知识

大多数应用应选择基本的描述性路由方案，让 URL 有可读性和意义。 默认传统路由 `{controller=Home}/{action=Index}/{id?}`：

* 支持基本的描述性路由方案。
* 是基于 UI 的应用的有用起点。

开发人员通常在专业情况下（例如，博客和电子商务终结点）使用[属性路由](xref:mvc/controllers/routing#attribute-routing)或专用传统路由向应用的高流量区域添加其他简洁路由。

Web API 应使用属性路由，将应用功能建模为一组资源，其中操作是由 HTTP 谓词表示。 也就是说，对同一逻辑资源执行的许多操作（例如，GET、POST）都将使用相同 URL。 属性路由提供了精心设计 API 的公共终结点布局所需的控制级别。

Razor Pages 应用使用默认的传统路由从应用的“页面”文件夹中提供命名资源  。 还可以使用其他约定来自定义 Razor Pages 路由行为。 有关详细信息，请参阅 <xref:razor-pages/index> 和 <xref:razor-pages/razor-pages-conventions>。

借助 URL 生成支持，无需通过硬编码 URL 将应用关联到一起，即可开发应用。 此支持允许从基本路由配置入手，并在确定应用的资源布局后修改路由。

路由使用路由（<xref:Microsoft.AspNetCore.Routing.IRouter> 的实现）  ：

* 将传入请求映射到路由处理程序  。
* 生成响应中使用的 URL。

默认情况下，应用只有一个路由集合。 当请求到达时，将按照路由在集合中的存在顺序来处理路由。 框架试图通过在集合中的每个路由上调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法，将传入请求的 URL 与集合中的路由进行匹配。 响应可根据路由信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。

路由系统具有以下特征：

* 路由模板语法用于通过标记化路由参数来定义路由。
* 可以使用常规样式和属性样式终结点配置。
* <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 用于确定 URL 参数是否包含给定的终结点约束的有效值。
* 应用模型（如 MVC/Razor Pages）注册了其所有具有可预测的路由方案实现的路由。
* 响应可根据路由信息使用路由生成 URL（例如，用于重定向或链接），从而避免硬编码 URL，这有助于可维护性。
* URL 生成基于支持任意可扩展性的路由。 <xref:Microsoft.AspNetCore.Mvc.IUrlHelper> 提供生成 URL 的方法。

路由通过 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware> 类连接到[中间件](xref:fundamentals/middleware/index)管道。 [ASP.NET Core MVC](xref:mvc/overview) 向中间件管道添加路由，作为其配置的一部分，并处理 MVC 和 Razor Pages 应用中的路由。 要了解如何将路由用作独立组件，请参阅[使用路由中间件](#use-routing-middleware)部分。

### <a name="url-matching"></a>URL 匹配

URL 匹配是一个过程，通过该过程，路由可向处理程序调度传入请求  。 此过程基于 URL 路径中的数据，但可以进行扩展以考虑请求中的任何数据。 向单独的处理程序调度请求的功能是缩放应用的大小和复杂性的关键。

传入请求将进入 <xref:Microsoft.AspNetCore.Builder.RouterMiddleware>，后者将对序列中的每个路由调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 方法。 <xref:Microsoft.AspNetCore.Routing.IRouter> 实例将选择是否通过将 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler*) 设置为非 NULL <xref:Microsoft.AspNetCore.Http.RequestDelegate> 来处理请求  。 如果路由为请求设置处理程序，将停止路由处理，并调用处理程序来处理该请求。 如果未找到用于处理请求的路由处理程序，中间件会将请求传递给请求管道中的下一个中间件。

<xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的主要输入是与当前请求关联的 [RouteContext.HttpContext](xref:Microsoft.AspNetCore.Routing.RouteContext.HttpContext*)。 [RouteContext.Handler](xref:Microsoft.AspNetCore.Routing.RouteContext.Handler) 和 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData*) 是路由匹配后设置的输出。

调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 的匹配还可根据迄今执行的请求处理将 [RouteContext.RouteData](xref:Microsoft.AspNetCore.Routing.RouteContext.RouteData) 的属性设为适当的值。

[RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values*) 是从路由中生成的路由值的字典  。 这些值通常通过标记 URL 来确定，可用来接受用户输入，或者在应用内作出进一步的调度决策。

[RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 是一个与匹配的路由相关的其他数据的属性包。 提供 <xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*> 以支持将状态数据与每个路由相关联，以便应用可根据所匹配的路由作出决策。 这些值是开发者定义的，不会影响通过任何方式路由的行为  。 此外，存储于 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 中的值可以属于任何类型，与 [RouteData.Values](xref:Microsoft.AspNetCore.Routing.RouteData.Values) 相反，后者必须能够转换为字符串，或从字符串进行转换。

[RouteData.Routers](xref:Microsoft.AspNetCore.Routing.RouteData.Routers) 是参与成功匹配请求的路由的列表。 路由可以相互嵌套。 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 属性可以通过导致匹配的逻辑路由树反映该路径。 通常情况下，<xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的第一项是路由集合，应该用于生成 URL。 <xref:Microsoft.AspNetCore.Routing.RouteData.Routers> 中的最后一项是匹配的路由处理程序。

<a name="lg"></a>

### <a name="url-generation"></a>URL 生成

URL 生成是通过其可根据一组路由值创建 URL 路径的过程。 这需要考虑处理程序与访问它们的 URL 之间的逻辑分隔。

URL 遵循类似的迭代过程，但开头是将用户或框架代码调用到路由集合的 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法中。 每个路由按顺序调用其 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 方法，直到返回非 NULL 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>  。

<xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的主输入有：

* [VirtualPathContext.HttpContext](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext)
* [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values)
* [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues)

路由主要使用 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 和 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 提供的路由值来确定是否可能生成 URL 以及要包括哪些值。 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues> 是通过匹配当前请求而生成的路由值集。 与此相反，<xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values> 是指定如何为当前操作生成所需 URL 的路由值。 如果路由应获取与当前上下文关联的服务或其他数据时，则提供 <xref:Microsoft.AspNetCore.Routing.VirtualPathContext.HttpContext>。

> [!TIP]
> 将 [VirtualPathContext.Values](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.Values*) 视为 [VirtualPathContext.AmbientValues](xref:Microsoft.AspNetCore.Routing.VirtualPathContext.AmbientValues*) 的一组替代。 URL 生成尝试重复使用当前请求中的路由值，以便使用相同路由或路由值生成链接的 URL。

<xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 的输出是 <xref:Microsoft.AspNetCore.Routing.VirtualPathData>。 <xref:Microsoft.AspNetCore.Routing.VirtualPathData> 是 <xref:Microsoft.AspNetCore.Routing.RouteData> 的并行值。 <xref:Microsoft.AspNetCore.Routing.VirtualPathData> 包含输出 URL 的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 以及路由应该设置的某些其他属性。

[VirtualPathData.VirtualPath](xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath*) 属性包含路由生成的虚拟路径  。 你可能需要进一步处理路径，具体取决于你的需求。 如果要在 HTML 中呈现生成的 URL，请预置应用的基路径。

[VirtualPathData.Router](xref:Microsoft.AspNetCore.Routing.VirtualPathData.Router*) 是对已成功生成 URL 的路由的引用。

[VirtualPathData.DataTokens](xref:Microsoft.AspNetCore.Routing.VirtualPathData.DataTokens*) 属性是生成 URL 的路由的其他相关数据的字典。 这是 [RouteData.DataTokens](xref:Microsoft.AspNetCore.Routing.RouteData.DataTokens*) 的并性值。

### <a name="create-routes"></a>创建路由

路由提供 <xref:Microsoft.AspNetCore.Routing.Route> 类，作为 <xref:Microsoft.AspNetCore.Routing.IRouter> 的标准实现。 <xref:Microsoft.AspNetCore.Routing.Route> 使用 route template 语法来定义模式，以便在调用 <xref:Microsoft.AspNetCore.Routing.IRouter.RouteAsync*> 时匹配 URL 路径  。 调用 <xref:Microsoft.AspNetCore.Routing.IRouter.GetVirtualPath*> 时，<xref:Microsoft.AspNetCore.Routing.Route> 使用同一路由模板生成 URL。

大多数应用通过调用 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 或 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 上定义的一种类似的扩展方法来创建路由。 任何 <xref:Microsoft.AspNetCore.Routing.IRouteBuilder> 扩展方法都会创建 <xref:Microsoft.AspNetCore.Routing.Route> 的实例并将其添加到路由集合中。

<xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 不接受路由处理程序参数。 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 仅添加由 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*> 处理的路由。 默认处理程序是 `IRouter`，该处理程序可能无法处理该请求。 例如，ASP.NET Core MVC 通常被配置为默认处理程序，仅处理与可用控制器和操作匹配的请求。 要了解 MVC 中的路由的详细信息，请参阅 <xref:mvc/controllers/routing>。

以下代码示例是典型 ASP.NET Core MVC 路由定义使用的一个 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 调用示例：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

此模板与 URL 路径相匹配，并且提取路由值。 例如，路径 `/Products/Details/17` 生成以下路由值：`{ controller = Products, action = Details, id = 17 }`。

路由值是通过将 URL 路径拆分成段，并且将每段与路由模板中的路由参数名称相匹配来确定的  。 路由参数已命名。 参数通过将参数名称括在大括号 `{ ... }` 中来定义。

上述模板还可匹配 URL 路径 `/`，并且生成值 `{ controller = Home, action = Index }`。 这是因为 `{controller}` 和 `{action}` 路由参数具有默认值，且 `id` 路由参数是可选的。 路由参数名称为参数定义默认值后，等号 (`=`) 后将有一个值。 路由参数名称后面的问号 (`?`) 定义了可选参数。

路由匹配时，具有默认值的路由参数始终会生成路由值  。 如果没有相应的 URL 路径段，则可选参数不会生成路由值。 有关路由模板方案和语法的详细说明，请参阅[路由模板参考](#route-template-reference)部分。

在以下示例中，路由参数定义 `{id:int}` 为 `id` 路由参数定义[路由约束](#route-constraint-reference)：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id:int}");
```

此模板与类似于 `/Products/Details/17` 而不是 `/Products/Details/Apples` 的 URL 路径相匹配。 路由约束实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 并检查路由值，以验证它们。 在此示例中，路由值 `id` 必须可转换为整数。 有关框架提供的路由约束的说明，请参阅[路由约束参考](#route-constraint-reference)。

其他 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 重载接受 `constraints`、`dataTokens` 和 `defaults` 的值。 这些参数的典型用法是传递匿名类型化对象，其中匿名类型的属性名匹配路由参数名称。

以下 <xref:Microsoft.AspNetCore.Builder.MapRouteRouteBuilderExtensions.MapRoute*> 示例可创建等效路由：

```csharp
routes.MapRoute(
    name: "default_route",
    template: "{controller}/{action}/{id?}",
    defaults: new { controller = "Home", action = "Index" });

routes.MapRoute(
    name: "default_route",
    template: "{controller=Home}/{action=Index}/{id?}");
```

> [!TIP]
> 定义约束和默认值的内联语法对于简单路由很方便。 但是，存在内联语法不支持的方案，例如数据令牌。

以下示例演示了一些其他方案：

```csharp
routes.MapRoute(
    name: "blog",
    template: "Blog/{*article}",
    defaults: new { controller = "Blog", action = "ReadArticle" });
```

上述模板与 `/Blog/All-About-Routing/Introduction` 等的 URL 路径相匹配，并提取值 `{ controller = Blog, action = ReadArticle, article = All-About-Routing/Introduction }`。 `controller` 和 `action` 的默认路由值由路由生成，即便模板中没有对应的路由参数。 可在路由模板中指定默认值。 根据路由参数名称前的星号 (`*`) 外观，`article` 路由参数被定义为 catch-all  。 全方位路由参数可捕获 URL 路径的其余部分，也能匹配空白字符串。

以下示例添加了路由约束和数据令牌：

```csharp
routes.MapRoute(
    name: "us_english_products",
    template: "en-US/Products/{id}",
    defaults: new { controller = "Products", action = "Details" },
    constraints: new { id = new IntRouteConstraint() },
    dataTokens: new { locale = "en-US" });
```

上述模板与 `/en-US/Products/5` 等 URL 路径相匹配，并且提取值 `{ controller = Products, action = Details, id = 5 }` 和数据令牌 `{ locale = en-US }`。

![本地 Windows 令牌](routing/_static/tokens.png)

### <a name="route-class-url-generation"></a>路由类 URL 生成

<xref:Microsoft.AspNetCore.Routing.Route> 类还可通过将一组路由值与其路由模板组合来生成 URL。 从逻辑上来说，这是匹配 URL 路径的反向过程。

> [!TIP]
> 为更好了解 URL 生成，请想象你要生成的 URL，然后考虑路由模板将如何匹配该 URL。 将生成什么值？ 这大致相当于 URL 在 <xref:Microsoft.AspNetCore.Routing.Route> 类中的生成方式。

以下示例使用常规 ASP.NET Core MVC 默认路由：

```csharp
routes.MapRoute(
    name: "default",
    template: "{controller=Home}/{action=Index}/{id?}");
```

使用路由值 `{ controller = Products, action = List }`，将生成 URL `/Products/List`。 路由值将替换为相应的路由参数，以形成 URL 路径。 由于 `id` 是可选路由参数，因此成功生成的 URL 不具有 `id` 的值。

使用路由值 `{ controller = Home, action = Index }`，将生成 URL `/`。 提供的路由值与默认值匹配，并且会安全地省略与默认值对应的段。

已生成的两个 URL 将往返以下路由定义 (`/Home/Index` 和 `/`)，并生成用于生成该 URL 的相同路由值。

> [!NOTE]
> 使用 ASP.NET Core MVC 应用应该使用 <xref:Microsoft.AspNetCore.Mvc.Routing.UrlHelper> 生成 URL，而不是直接调用到路由。

有关 URL 生成的详细信息，请参阅 [Url 生成参考](#url-generation-reference)部分。

## <a name="use-routing-middleware"></a>使用路由中间件

引用应用项目文件中的 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。

将路由添加到 `Startup.ConfigureServices` 中的服务容器：

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_ConfigureServices&highlight=3)]

必须在 `Startup.Configure` 方法中配置路由。 该示例应用使用以下 API：

* <xref:Microsoft.AspNetCore.Routing.RouteBuilder>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> &ndash; 仅匹配 HTTP GET 请求。
* <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*>

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_RouteHandler)]

下表显示了具有给定 URI 的响应。

| URI                    | 响应                                          |
| ---------------------- | ------------------------------------------------- |
| `/package/create/3`    | Hello! Route values: [operation, create], [id, 3] |
| `/package/track/-3`    | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/-3/`   | Hello! Route values: [operation, track], [id, -3] |
| `/package/track/`      | 请求失败，不匹配。              |
| `GET /hello/Joe`       | Hi, Joe!                                          |
| `POST /hello/Joe`      | 请求失败，仅匹配 HTTP GET。 |
| `GET /hello/Joe/Smith` | 请求失败，不匹配。              |

如果要配置单个路由，请在 `IRouter` 实例中调用 <xref:Microsoft.AspNetCore.Builder.RoutingBuilderExtensions.UseRouter*> 传入。 无需使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder>。

框架可提供一组用于创建路由 (<xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions>) 的扩展方法：

* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareDelete*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareGet*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewarePut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapMiddlewareVerb*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPost*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapPut*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>
* <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>

一些列出的方法（如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*>）需要 <xref:Microsoft.AspNetCore.Http.RequestDelegate>。 路由匹配时，<xref:Microsoft.AspNetCore.Http.RequestDelegate> 会用作路由处理程序  。 此系列中的其他方法允许配置中间件管道，将其用作路由处理程序。 如果 `Map*` 方法不接受处理程序（例如 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapRoute*>），则它将使用 <xref:Microsoft.AspNetCore.Routing.RouteBuilder.DefaultHandler*>。

`Map[Verb]` 方法将使用约束来将路由限制为方法名称中的 HTTP 谓词。 有关示例，请参阅 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapGet*> 和 <xref:Microsoft.AspNetCore.Routing.RequestDelegateRouteBuilderExtensions.MapVerb*>。

## <a name="route-template-reference"></a>路由模板参考

如果路由找到匹配项，大括号 (`{ ... }`) 内的令牌定义绑定的路由参数  。 可在路由段中定义多个路由参数，但必须由文本值隔开。 例如，`{controller=Home}{action=Index}` 不是有效的路由，因为 `{controller}` 和 `{action}` 之间没有文本值。 这些路由参数必须具有名称，且可能指定了其他属性。

路由参数以外的文本（例如 `{id}`）和路径分隔符 `/` 必须匹配 URL 中的文本。 文本匹配区分大小写，并且基于 URL 路径已解码的表示形式。 要匹配文字路由参数分隔符（`{` 或 `}`），请通过重复该字符（`{{` 或 `}}`）来转义分隔符。

尝试捕获具有可选文件扩展名的文件名的 URL 模式还有其他注意事项。 例如，考虑模板 `files/{filename}.{ext?}`。 当 `filename` 和 `ext` 的值都存在时，将填充这两个值。 如果 URL 中仅存在 `filename` 的值，则路由匹配，因为尾随句点 (`.`) 是可选的。 以下 URL 与此路由相匹配：

* `/files/myFile.txt`
* `/files/myFile`

可以使用星号 (`*`) 作为路由参数的前缀，以绑定到 URI 的其余部分。 这称为 catch-all 参数  。 例如，`blog/{*slug}` 将匹配以 `/blog` 开头且其后带有任何值（将分配给 `slug` 路由值）的 URI。 全方位参数还可以匹配空字符串。

使用路由生成 URL（包括路径分隔符 (`/`)）时，catch-all 参数会转义相应的字符。 例如，路由值为 `{ path = "my/path" }` 的路由 `foo/{*path}` 生成 `foo/my%2Fpath`。 请注意转义的正斜杠。

路由参数可能具有指定的默认值，方法是在参数名称后使用等号 (`=`) 隔开以指定默认值  。 例如，`{controller=Home}` 将 `Home` 定义为 `controller` 的默认值。 如果参数的 URL 中不存在任何值，则使用默认值。 通过在参数名称的末尾附加问号 (`?`) 可使路由参数成为可选项，如 `id?` 中所示。 可选值和默认路径参数的区别在于具有默认值的路由参数始终会生成值 &mdash;，而可选参数仅当请求 URL 提供值时才会具有值。

路由参数可能具有必须与从 URL 中绑定的路由值匹配的约束。 在路由参数后面添加一个冒号 (`:`) 和约束名称可指定路由参数上的内联约束  。 如果约束需要参数，将以在约束名称后括在括号 (`(...)`) 中的形式提供。 通过追加另一个冒号 (`:`) 和约束名称，可指定多个内联约束。

约束名称和参数将传递给 <xref:Microsoft.AspNetCore.Routing.IInlineConstraintResolver> 服务，以创建 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 的实例，用于处理 URL。 例如，路由模板 `blog/{article:minlength(10)}` 使用参数 `10` 指定 `minlength` 约束。 有关路由约束详情以及框架提供的约束列表，请参阅[路由约束引用](#route-constraint-reference)部分。

下表演示了示例路由模板及其行为。

| 路由模板                           | 示例匹配 URI    | 请求 URI&hellip;                                                    |
| ---------------------------------------- | ----------------------- | -------------------------------------------------------------------------- |
| `hello`                                  | `/hello`                | 仅匹配单个路径 `/hello`。                                     |
| `{Page=Home}`                            | `/`                     | 匹配并将 `Page` 设置为 `Home`。                                         |
| `{Page=Home}`                            | `/Contact`              | 匹配并将 `Page` 设置为 `Contact`。                                      |
| `{controller}/{action}/{id?}`            | `/Products/List`        | 映射到 `Products` 控制器和 `List` 操作。                       |
| `{controller}/{action}/{id?}`            | `/Products/Details/123` | 映射到 `Products` 控制器和 `Details` 操作（`id` 设置为 123）。 |
| `{controller=Home}/{action=Index}/{id?}` | `/`                     | 映射到 `Home` 控制器和 `Index` 方法（忽略 `id`）。        |

使用模板通常是进行路由最简单的方法。 还可在路由模板外指定约束和默认值。

> [!TIP]
> 启用[日志记录](xref:fundamentals/logging/index)以查看内置路由实现（如 <xref:Microsoft.AspNetCore.Routing.Route>）如何匹配请求。

## <a name="reserved-routing-names"></a>保留的路由名称

以下关键字是保留的名称，它们不能用作路由名称或参数：

* `action`
* `area`
* `controller`
* `handler`
* `page`

## <a name="route-constraint-reference"></a>路由约束参考

路由约束在传入 URL 发生匹配时执行，URL 路径标记为路由值。 路径约束通常检查通过路径模板关联的路径值，并对该值是否可接受做出是/否决定。 某些路由约束使用路由值以外的数据来考虑是否可以路由请求。 例如，<xref:Microsoft.AspNetCore.Routing.Constraints.HttpMethodRouteConstraint> 可以根据其 HTTP 谓词接受或拒绝请求。 约束用于路由请求和链接生成。

> [!WARNING]
> 请勿将约束用于“输入验证”  。 如果将约束用于“输入约束”，那么无效输入将导致“404 - 未找到”响应，而不是含相应错误消息的“400 - 错误请求”    。 路由约束用于消除类似路由的歧义，而不是验证特定路由的输入  。

下表演示示例路由约束及其预期行为。

| 约束 | 示例 | 匹配项示例 | 说明 |
| ---------- | ------- | --------------- | ----- |
| `int` | `{id:int}` | `123456789`、`-123456789` | 匹配任何整数 |
| `bool` | `{active:bool}` | `true`、`FALSE` | 匹配 `true`或 `false`（区分大小写） |
| `datetime` | `{dob:datetime}` | `2016-12-31`、`2016-12-31 7:32pm` | 匹配有效的 `DateTime` 值（位于固定区域性中 - 查看警告） |
| `decimal` | `{price:decimal}` | `49.99`、`-1,000.01` | 匹配有效的 `decimal` 值（位于固定区域性中 - 查看警告） |
| `double` | `{weight:double}` | `1.234`、`-1,001.01e8` | 匹配有效的 `double` 值（位于固定区域性中 - 查看警告） |
| `float` | `{weight:float}` | `1.234`、`-1,001.01e8` | 匹配有效的 `float` 值（位于固定区域性中 - 查看警告） |
| `guid` | `{id:guid}` | `CD2C1638-1638-72D5-1638-DEADBEEF1638`、`{CD2C1638-1638-72D5-1638-DEADBEEF1638}` | 匹配有效的 `Guid` 值 |
| `long` | `{ticks:long}` | `123456789`、`-123456789` | 匹配有效的 `long` 值 |
| `minlength(value)` | `{username:minlength(4)}` | `Rick` | 字符串必须至少为 4 个字符 |
| `maxlength(value)` | `{filename:maxlength(8)}` | `Richard` | 字符串不得超过 8 个字符 |
| `length(length)` | `{filename:length(12)}` | `somefile.txt` | 字符串必须正好为 12 个字符 |
| `length(min,max)` | `{filename:length(8,16)}` | `somefile.txt` | 字符串必须至少为 8 个字符，且不得超过 16 个字符 |
| `min(value)` | `{age:min(18)}` | `19` | 整数值必须至少为 18 |
| `max(value)` | `{age:max(120)}` | `91` | 整数值不得超过 120 |
| `range(min,max)` | `{age:range(18,120)}` | `91` | 整数值必须至少为 18，且不得超过 120 |
| `alpha` | `{name:alpha}` | `Rick` | 字符串必须由一个或多个字母字符（`a`-`z`，区分大小写）组成 |
| `regex(expression)` | `{ssn:regex(^\\d{{3}}-\\d{{2}}-\\d{{4}}$)}` | `123-45-6789` | 字符串必须匹配正则表达式（参见有关定义正则表达式的提示） |
| `required` | `{name:required}` | `Rick` | 用于强制在 URL 生成过程中存在非参数值 |

可向单个参数应用多个由冒号分隔的约束。 例如，以下约束将参数限制为大于或等于 1 的整数值：

```csharp
[Route("users/{id:int:min(1)}")]
public User GetUserById(int id) { }
```

> [!WARNING]
> 验证 URL 的路由约束并将转换为始终使用固定区域性的 CLR 类型（例如 `int` 或 `DateTime`）。 这些约束假定 URL 不可本地化。 框架提供的路由约束不会修改存储于路由值中的值。 从 URL 中分析的所有路由值都将存储为字符串。 例如，`float` 约束会尝试将路由值转换为浮点数，但转换后的值仅用来验证其是否可转换为浮点数。

## <a name="regular-expressions"></a>正则表达式

ASP.NET Core 框架将向正则表达式构造函数添加 `RegexOptions.IgnoreCase | RegexOptions.Compiled | RegexOptions.CultureInvariant`。 有关这些成员的说明，请参阅 <xref:System.Text.RegularExpressions.RegexOptions>。

正则表达式与路由和 C# 计算机语言使用的分隔符和令牌相似。 必须对正则表达式令牌进行转义。 要在路由中使用正则表达式 `^\d{3}-\d{2}-\d{4}$`，表达式必须在字符串中提供 `\`（单反斜杠）字符，正如 C# 源文件中的 `\\`（双反斜杠）字符一样，以便对 `\` 字符串转义字符进行转义（除非使用[字符串文本](/dotnet/csharp/language-reference/keywords/string)）。 要对路由参数分隔符进行转义（`{`、`}`、`[`、`]`），请将表达式（`{{`、`}`、`[[`、`]]`）中的字符数加倍。 下表展示了正则表达式和转义版本。

| 正则表达式    | 转义后的正则表达式     |
| --------------------- | ------------------------------ |
| `^\d{3}-\d{2}-\d{4}$` | `^\\d{{3}}-\\d{{2}}-\\d{{4}}$` |
| `^[a-z]{2}$`          | `^[[a-z]]{{2}}$`               |

路由中使用的正则表达式通常以脱字号 (`^`) 开头，并匹配字符串的起始位置。 表达式通常以美元符号 (`$`) 字符结尾，并匹配字符串的结尾。 `^` 和 `$` 字符可确保正则表达式匹配整个路由参数值。 如果没有 `^` 和 `$` 字符，正则表达式将匹配字符串内的所有子字符串，而这通常是不需要的。 下表提供了示例并说明了它们匹配或匹配失败的原因。

| 表达式   | String    | 匹配 | 注释               |
| ------------ | --------- | :---: |  -------------------- |
| `[a-z]{2}`   | hello     | 是   | 子字符串匹配     |
| `[a-z]{2}`   | 123abc456 | 是   | 子字符串匹配     |
| `[a-z]{2}`   | mz        | 是   | 匹配表达式    |
| `[a-z]{2}`   | MZ        | 是   | 不区分大小写    |
| `^[a-z]{2}$` | hello     | No    | 参阅上述 `^` 和 `$` |
| `^[a-z]{2}$` | 123abc456 | No    | 参阅上述 `^` 和 `$` |

有关正则表达式语法的详细信息，请参阅 [.NET Framework 正则表达式](/dotnet/standard/base-types/regular-expression-language-quick-reference)。

若要将参数限制为一组已知的可能值，可使用正则表达式。 例如，`{action:regex(^(list|get|create)$)}` 仅将 `action` 路由值匹配到 `list`、`get` 或 `create`。 如果传递到约束字典中，字符串 `^(list|get|create)$` 将等效。 已传递到约束字典（不与模板内联）且不匹配任何已知约束的约束还将被视为正则表达式。

## <a name="custom-route-constraints"></a>自定义路由约束

除了内置路由约束以外，还可以通过实现 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口来创建自定义路由约束。 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 接口包含一个方法 `Match`，当满足约束时，它返回 `true`，否则返回 `false`。

若要使用自定义 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint>，必须在应用的服务容器中使用应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 注册路由约束类型。 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 是将路由约束键映射到验证这些约束的 <xref:Microsoft.AspNetCore.Routing.IRouteConstraint> 实现的目录。 应用的 <xref:Microsoft.AspNetCore.Routing.RouteOptions.ConstraintMap> 可作为 [services.AddRouting](xref:Microsoft.Extensions.DependencyInjection.RoutingServiceCollectionExtensions.AddRouting*) 调用的一部分在 `Startup.ConfigureServices` 中进行更新，也可以通过使用 `services.Configure<RouteOptions>` 直接配置 <xref:Microsoft.AspNetCore.Routing.RouteOptions> 进行更新。 例如:

```csharp
services.AddRouting(options =>
{
    options.ConstraintMap.Add("customName", typeof(MyCustomConstraint));
});
```

然后，可以使用在注册约束类型时指定的名称，以常规方式将约束应用于路由。 例如:

```csharp
[HttpGet("{id:customName}")]
public ActionResult<string> Get(string id)
```

## <a name="url-generation-reference"></a>URL 生成参考

以下示例演示如何在给定路由值字典和 <xref:Microsoft.AspNetCore.Routing.RouteCollection> 的情况下生成路由链接。

[!code-csharp[](routing/samples/2.x/RoutingSample/Startup.cs?name=snippet_Dictionary)]

上述示例末尾生成的 <xref:Microsoft.AspNetCore.Routing.VirtualPathData.VirtualPath> 为 `/package/create/123`。 字典提供“跟踪包路由”模板 `package/{operation}/{id}` 的 `operation` 和 `id` 路由值。 有关详细信息，请参阅[使用路由中间件](#use-routing-middleware)部分或[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/routing/samples)中的示例代码。

<xref:Microsoft.AspNetCore.Routing.VirtualPathContext> 构造函数的第二个参数是环境值的集合  。 由于环境值限制了开发人员在请求上下文中必须指定的值数，因此环境值使用起来很方便。 当前请求的当前路由值被视为链接生成的环境值。 在 ASP.NET Core MVC 应用 `HomeController` 的 `About` 操作中，无需指定控制器路由值即可链接到使用 `Home` 环境值的 `Index` 操作&mdash;。

忽略与参数不匹配的环境值。 在显式提供的值会覆盖环境值的情况下，也会忽略环境值。 在 URL 中将从左到右进行匹配。

显式提供但与路由片段不匹配的值将添加到查询字符串中。 下表显示使用路由模板 `{controller}/{action}/{id?}` 时的结果。

| 环境值                     | 显式值                        | 结果                  |
| ---------------------------------- | -------------------------------------- | ----------------------- |
| 控制器 =“Home”                | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 控制器 =“Order”，操作 =“About” | `/Order/About`          |
| 控制器 = “Home”，颜色 = “Red” | 操作 =“About”                       | `/Home/About`           |
| 控制器 =“Home”                | 操作 =“About”，颜色 =“Red”        | `/Home/About?color=Red` |

如果路由具有不对应于参数的默认值，且该值以显式方式提供，则它必须与默认值相匹配：

```csharp
routes.MapRoute("blog_route", "blog/{*slug}",
    defaults: new { controller = "Blog", action = "ReadPost" });
```

当提供 `controller` 和 `action` 的匹配值时，链接生成仅为此路由生成链接。

## <a name="complex-segments"></a>复杂段

复杂段（例如，`[Route("/x{token}y")]`）通过非贪婪的方式从右到左匹配文字进行处理。 请参阅[此代码](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)以了解有关如何匹配复杂段的详细说明。 ASP.NET Core 无法使用[代码示例](https://github.com/aspnet/AspNetCore/blob/release/2.2/src/Http/Routing/src/Patterns/RoutePatternMatcher.cs#L293)，但它提供了对复杂段的合理说明。
<!-- While that code is no longer used by ASP.NET Core for complex segment matching, it provides a good match to the current algorithm. The [current code](https://github.com/aspnet/AspNetCore/blob/91514c9af7e0f4c44029b51f05a01c6fe4c96e4c/src/Http/Routing/src/Matching/DfaMatcherBuilder.cs#L227-L244) is too abstracted from matching to be useful for understanding complex segment matching.
-->

::: moniker-end
