---
title: 将从 ASP.NET Web API 迁移到 ASP.NET Core
author: ardalis
description: 了解如何将 web API 实现从 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC。
ms.author: scaddie
ms.custom: mvc
ms.date: 12/10/2018
uid: migration/webapi
ms.openlocfilehash: ea14b7128582a21b36c70041d59fb638eebdbef0
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895004"
---
# <a name="migrate-from-aspnet-web-api-to-aspnet-core"></a>将从 ASP.NET Web API 迁移到 ASP.NET Core

通过[Scott Addie](https://twitter.com/scott_addie)和[Steve Smith](https://ardalis.com/)

ASP.NET 4.x Web API 是到达范围广泛的客户端，包括浏览器和移动设备的 HTTP 服务。 ASP.NET Core 统一了 ASP.NET 4.x 的 MVC 和 Web API 应用模型到名为 ASP.NET Core MVC 更简单的编程模型。 本文演示如何将 ASP.NET 4.x Web API 迁移到 ASP.NET Core MVC 所需的步骤。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/migration/webapi/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>系统必备

[!INCLUDE [net-core-prereqs-vs-2.2](../includes/net-core-prereqs-vs-2.2.md)]

## <a name="review-aspnet-4x-web-api-project"></a>检查 ASP.NET 4.x Web API 项目

作为起点，本文使用*ProductsApp*中创建项目[Getting Started with ASP.NET Web API 2](/aspnet/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)。 该项目中，一个简单的 ASP.NET 4.x Web API 项目配置，如下所示。

在中*Global.asax.cs*，调用`WebApiConfig.Register`:

[!code-csharp[](webapi/sample/ProductsApp/Global.asax.cs?highlight=14)]

`WebApiConfig`类位于*App_Start*文件夹并且具有静态`Register`方法：

[!code-csharp[](webapi/sample/ProductsApp/App_Start/WebApiConfig.cs)]

此类配置[的属性路由](/aspnet/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2)，但它实际上未使用项目中。 它还可以配置 ASP.NET Web API 使用的路由表。 在这种情况下，ASP.NET 4.x Web API 需要 Url 以匹配格式`/api/{controller}/{id}`，使用`{id}`可选的。

*ProductsApp*项目包括一个控制器。 在控制器继承`ApiController`和包含两个操作：

[!code-csharp[](webapi/sample/ProductsApp/Controllers/ProductsController.cs?highlight=28,33)]

`Product`使用模型`ProductsController`是一个简单的类：

[!code-csharp[](webapi/sample/ProductsApp/Models/Product.cs)]

以下部分演示了迁移到 ASP.NET Core MVC Web API 项目。

## <a name="create-destination-project"></a>创建目标项目

完成 Visual Studio 中的以下步骤：

* 转到**文件** > **新** > **项目** > **其他项目类型** > **Visual Studio 解决方案**。 选择**空白解决方案**，并将解决方案命名*WebAPIMigration*。 单击**确定**按钮。
* 添加现有*ProductsApp*到解决方案。
* 添加一个新**ASP.NET Core Web 应用程序**到解决方案。 选择 **.NET Core**从下拉列表中的框架为目标，然后选择**API**项目模板。 将项目命名*ProductsCore*，然后单击**确定**按钮。

该解决方案现在包含两个项目。 以下各节介绍了迁移*ProductsApp*项目的内容与*ProductsCore*项目。

## <a name="migrate-configuration"></a>迁移配置

不使用 ASP.NET Core *App_Start*文件夹或*Global.asax*文件，并*web.config*文件添加在发布时间。 *Startup.cs*将取代*Global.asax*和位于项目根目录中。 `Startup`类负责处理所有应用程序启动任务。 有关详细信息，请参阅 <xref:fundamentals/startup>。

在 ASP.NET Core MVC 中，在默认情况下包含的属性路由时<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>名为`Startup.Configure`。 以下`UseMvc`调用替换*ProductsApp*项目的*app_start/webapiconfig.cs*文件：

[!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_Configure&highlight=13])]

## <a name="migrate-models-and-controllers"></a>迁移模型和控制器

通过复制*ProductApp*项目的控制器和它所使用的模型。 请执行以下步骤：

1. 复制*Controllers/ProductsController.cs*从原始到新项目。
1. 复制整个*模型*从原始项目到新的文件夹。
1. 更改复制的文件的命名空间以匹配新的项目名称 (*ProductsCore*)。 调整`using ProductsApp.Models;`中的语句*ProductsController.cs*过。

此时，生成中的多个编译错误的应用程序结果。 发生错误的原因在 ASP.NET Core 中不存在以下组件：

* `ApiController` 类
* `System.Web.Http` 命名空间
* `IHttpActionResult` 接口

修复错误，如下所示：

1. 更改`ApiController`到<xref:Microsoft.AspNetCore.Mvc.ControllerBase>。 添加`using Microsoft.AspNetCore.Mvc;`若要解决`ControllerBase`引用。
1. 删除 `using System.Web.Http;`。
1. 更改`GetProduct`操作的返回类型从`IHttpActionResult`到`ActionResult<Product>`。

简化`GetProduct`操作的`return`与以下语句：

```csharp
return product;
```

## <a name="configure-routing"></a>配置路由

配置路由，如下所示：

1. 修饰`ProductsController`类具有以下属性：

    ```csharp
    [Route("api/[controller]")]
    [ApiController]
    ```

    在前面[[Route]](xref:Microsoft.AspNetCore.Mvc.RouteAttribute)属性所配置控制器的属性路由模式。 [[ApiController]](xref:Microsoft.AspNetCore.Mvc.ApiControllerAttribute)特性使此控制器中的路由所必需的所有操作的属性。

    属性路由支持令牌，如`[controller]`和`[action]`。 在运行时，每个令牌是分别替换为控制器或操作的名称，已向其应用了该属性。 令牌减少的项目中的魔幻字符串。 路由保持与相应的控制器同步，并将应用时自动重命名重构操作，还请确保令牌。
1. 将项目的兼容性模式设置为 ASP.NET Core 2.2:

    [!code-csharp[](webapi/sample/ProductsCore/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

    上述更改：

    * 使用所需`[ApiController]`在控制器级别属性。
    * 选择启用可能出现的中断 ASP.NET Core 2.2 中引入的行为。
1. 启用 HTTP Get 请求到`ProductController`操作：
    * 将应用[[HttpGet]](xref:Microsoft.AspNetCore.Mvc.HttpGetAttribute)属性为`GetAllProducts`操作。
    * 将应用`[HttpGet("{id}")]`属性为`GetProduct`操作。

上述更改和删除未使用之后`using`语句， *ProductsController.cs*文件如下所示：

[!code-csharp[](webapi/sample/ProductsCore/Controllers/ProductsController.cs)]

运行已迁移的项目，并浏览到`/api/products`。 将显示三种产品的完整列表。 浏览到 `/api/products/1`。 第一个产品将出现。

## <a name="compatibility-shim"></a>兼容性修补程序

[Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim)库提供了兼容性填充程序移动到 ASP.NET Core 的 ASP.NET 4.x Web API 项目。 兼容性填充程序扩展了 ASP.NET Core 支持数量从 ASP.NET 4.x Web API 2 的约定。 移植之前在本文档中的示例是基本兼容性填充程序是不需要的。 对于大型项目，使用兼容性修补程序也可用于临时之间的隔阂 API ASP.NET Core 和 ASP.NET 4.x Web API 2。

旨在用作临时度量值以支持大型 ASP.NET 4.x Web API 项目迁移到 ASP.NET Core Web API 兼容性修补程序。 随着时间推移，应更新项目以使用 ASP.NET Core 模式，而不是依靠兼容性填充码。

中包含的兼容性功能`Microsoft.AspNetCore.Mvc.WebApiCompatShim`包括：

* 添加`ApiController`类型，以便控制器的基类型不需要更新。
* 启用 Web API 样式模型绑定。 ASP.NET Core MVC 模型绑定函数类似于的 ASP.NET 4.x MVC 5，默认情况下。 兼容性填充程序更改模型绑定是更类似于 ASP.NET 4.x Web API 2 模型绑定约定。 例如，复杂类型自动绑定从请求正文。
* 扩展模型绑定，以便控制器操作可以采取的类型参数`HttpRequestMessage`。
* 添加消息格式化程序，允许的操作以返回类型的结果`HttpResponseMessage`。
* 添加 Web API 2 操作可能具有用于为响应提供服务的其他响应方法：
  * `HttpResponseMessage` 生成器：
    * `CreateResponse<T>`
    * `CreateErrorResponse`
  * 结果的操作方法：
    * `BadRequestErrorMessageResult`
    * `ExceptionResult`
    * `InternalServerErrorResult`
    * `InvalidModelStateResult`
    * `NegotiatedContentResult`
    * `ResponseMessageResult`
* 添加的一个实例`IContentNegotiator`向应用程序的依赖关系注入 (DI) 容器，并使可从内容协商相关类型[Microsoft.AspNet.WebApi.Client](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Client/)。 此类类型的示例包括`DefaultContentNegotiator`和`MediaTypeFormatter`。

若要使用兼容性修补程序：

1. 安装[Microsoft.AspNetCore.Mvc.WebApiCompatShim](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.WebApiCompatShim) NuGet 包。
1. 兼容性修补程序的服务注册应用程序的 DI 容器通过调用`services.AddMvc().AddWebApiConventions()`在`Startup.ConfigureServices`。
1. 定义特定于 API 的路由使用的 web`MapWebApiRoute`上`IRouteBuilder`在应用的`IApplicationBuilder.UseMvc`调用。

## <a name="additional-resources"></a>其他资源

* <xref:web-api/index>
* <xref:web-api/action-return-types>
* <xref:mvc/compatibility-version>
