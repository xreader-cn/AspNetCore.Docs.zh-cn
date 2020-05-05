---
title: 从 ASP.NET Web API 迁移到 ASP.NET Core
author: ardalis
description: 了解如何将 web API 实现从 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/webapi
ms.openlocfilehash: dda457daa0cb8a59ccd4c326a601e375fe4a81bb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82766584"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>从 ASP.NET Web API 迁移到 ASP.NET Core

作者： [Scott Addie](https://twitter.com/scott_addie)和[Steve Smith](https://ardalis.com/)

ASP.NET 4.x Web API 是一种 HTTP 服务，它可达到各种客户端，包括浏览器和移动设备。 ASP.NET Core 将 ASP.NET 4.x 的 MVC 和 Web API 应用模型统一到称为 ASP.NET Core MVC 的更简单的编程模型中。 本文演示从 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC 所需的步骤。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>先决条件

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs2019-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>查看 ASP.NET 4.x Web API 项目

作为起点，本文使用[ASP.NET Web API 2 入门](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)中创建的*ProductsApp*项目。 在该项目中，简单的 ASP.NET 4.x Web API 项目配置如下。

在*Global.asax.cs*中，对进行调用`WebApiConfig.Register`：

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

类位于*App_Start*文件夹中，并具有一个静态`Register`方法： `WebApiConfig`

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

此类配置[属性路由](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)，不过实际上它并不是在项目中使用。 它还配置 ASP.NET Web API 使用的路由表。 在这种情况下，ASP.NET 4.x Web API 需要 Url 来匹配格式`/api/{controller}/{id}`，这`{id}`是可选的。

*ProductsApp*项目包含一个控制器。 控制器继承自`ApiController` ，其中包含两个操作：

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

使用`Product`的模型`ProductsController`是一个简单的类：

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

以下部分演示了如何将 Web API 项目迁移到 ASP.NET Core MVC。

## <a name="create-destination-project"></a>创建目标项目

在 Visual Studio 中完成以下步骤：

* 中转到 "**文件** > " "**新建** > **项目** > " "**其他项目类型** > " "**Visual Studio 解决方案**"。 选择 "**空白解决方案**"，并将解决方案命名为 " *WebAPIMigration*"。 单击“确定”**** 按钮。
* 将现有的*ProductsApp*项目添加到解决方案。
* 向解决方案添加新的**ASP.NET Core Web 应用程序**项目。 从下拉选择 " **.Net Core**目标框架"，然后选择 " **API**项目" 模板。 将项目命名为 " *ProductsCore*"，然后单击 **"确定"** 按钮。

解决方案现在包含两个项目。 以下各节介绍了如何将*ProductsApp*项目的内容迁移到*ProductsCore*项目。

## <a name="migrate-configuration"></a>迁移配置

ASP.NET Core 不使用*App_Start*文件夹或*global.asax*文件，并且在发布时添加*web.config*文件。 *Startup.cs*是*global.asa*的替代，位于项目根目录中。 `Startup`类处理所有应用启动任务。 有关详细信息，请参阅 <xref:fundamentals/startup>。

在 ASP.NET Core MVC 中，当在中<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*> `Startup.Configure`调用时，默认情况下包含特性路由。 以下`UseMvc`调用将替换*ProductsApp*项目的*App_Start/webapiconfig.cs*文件：

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>迁移模型和控制器

复制*ProductApp*项目的控制器及其使用的模型。 执行以下步骤:

1. 将*控制器/ProductsController*从原始项目复制到新项目。
1. 将整个*模型*文件夹从原始项目复制到新的项目。
1. 更改复制的文件的命名空间，使其与新项目名称（*ProductsCore*）相匹配。 同时调整`using ProductsApp.Models;` *ProductsController.cs*中的语句。

此时，生成应用会导致大量编译错误。 之所以发生这些错误是因为 ASP.NET Core 中不存在下列组件：

* `ApiController` 类
* `System.Web.Http` 命名空间
* `IHttpActionResult` 接口

修复错误，如下所示：

1. 将 `ApiController` 更改为 <xref:Microsoft.AspNetCore.Mvc.ControllerBase>。 添加`using Microsoft.AspNetCore.Mvc;`以解析`ControllerBase`引用。
1. 删除 `using System.Web.Http;`。
1. 将`GetProduct`操作的返回类型从`IHttpActionResult`更改为`ActionResult<Product>`。

简化`GetProduct`操作的`return`语句：

```csharp
return product;
```

## <a name="configure-routing"></a>配置路由

按如下所示配置路由：

1. 用以下`ProductsController`特性标记类：

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    上述[`[Route]`](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)属性配置控制器的属性路由模式。 [`[ApiController]`](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)特性使特性路由成为此控制器中所有操作的要求。

    特性路由支持标记，例如`[controller]`和。 `[action]` 在运行时，每个标记分别替换为应用了属性的控制器或操作的名称。 这些标记减少了项目中的幻字符串的数目。 标记还可确保在应用自动重命名重构时，路由与相应的控制器和操作保持同步。
1. 将项目的兼容模式设置为 ASP.NET Core 2.2：

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    上述更改：

    * 需要在控制器级别使用`[ApiController]`特性。
    * 在 ASP.NET Core 2.2 中引入了可能中断的行为。
1. 为`ProductController`操作启用 HTTP Get 请求：
    * 将[`[HttpGet]`](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)特性应用于`GetAllProducts`操作。
    * 将`[HttpGet("{id}")]`特性应用于`GetProduct`操作。

前面的更改和删除未使用`using`的语句后， *ProductsController.cs*文件如下所示：

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

运行迁移的项目，并浏览到`/api/products`。 此时会显示三个产品的完整列表。 浏览到 `/api/products/1` 。 第一个产品随即出现。

## <a name="compatibility-shim"></a>兼容性填充码

[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)库提供兼容性填充码，以将 ASP.NET 4.X Web API 项目移动到 ASP.NET Core。 兼容性填充码扩展 ASP.NET Core，以支持 ASP.NET 4.x Web API 2 中的一些约定。 本文档前面的示例移植的基本操作足以确保兼容性填充程序是不必要的。 对于较大的项目，使用兼容性填充码对于临时桥接 ASP.NET Core 与 ASP.NET 4.x Web API 2 之间的 API 间隙非常有用。

Web API 兼容性填充码旨在用作一种临时度量，以支持将大型 ASP.NET 4.x Web API 项目迁移到 ASP.NET Core。 随着时间的推移，应将项目更新为使用 ASP.NET Core 模式，而不是依赖于兼容性填充码。

中包含的`Microsoft.AspNetCore.Mvc.WebApiCompatShim`兼容性功能包括：

* 添加一个`ApiController`类型，以便不需要更新控制器的基类型。
* 启用 Web API 样式的模型绑定。 默认情况下，ASP.NET Core MVC 模型绑定函数与 ASP.NET 4.x MVC 5 的绑定函数类似。 兼容性填充码更改模型绑定，更类似于 ASP.NET 4.x Web API 2 模型绑定约定。 例如，复杂类型会自动从请求正文进行绑定。
* 扩展模型绑定，以便控制器操作可以接受类型`HttpRequestMessage`的参数。
* 添加消息格式化程序，以允许操作返回类型`HttpResponseMessage`为的结果。
* 添加 Web API 2 操作可能用于提供响应的其他响应方法：
  * `HttpResponseMessage`生成器
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * 操作结果方法：
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* 将的`IContentNegotiator`实例添加到应用的依赖项注入（DI）容器，并使[WebApi](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)中与内容协商相关的类型可用。 此类类型的示例`DefaultContentNegotiator`包括`MediaTypeFormatter`和。

使用兼容性填充码：

1. 安装[AspNetCore WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 包。
1. 通过调用`services.AddMvc().AddWebApiConventions()`在中`Startup.ConfigureServices`，将兼容性填充程序的服务注册到应用的 DI 容器。
1. 在应用的`MapWebApiRoute` `IRouteBuilder` `IApplicationBuilder.UseMvc`调用中，使用在上定义特定于 web API 的路由。

## <a name="additional-resources"></a>其他资源

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
