---
title: ASP.NET Core 中 Razor 页面的路由和应用约定
author: guardrex
description: 了解路由和应用模型提供程序约定如何帮助控制页面路由、发现和处理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/07/2019
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: 59c8af648b50deb51f3762c14348d08acd48886e
ms.sourcegitcommit: bee530454ae2b3c25dc7ffebf93536f479a14460
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2019
ms.locfileid: "67724449"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>ASP.NET Core 中 Razor 页面的路由和应用约定

作者：[Luke Latham](https://github.com/guardrex)

了解如何使用[页面路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor 页面应用中的页面路由、发现和处理。

需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。

若要指定页面路由、 添加路由段，或将参数添加到路由，请使用页面的`@page`指令。 有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。

有不能用作路由段或参数名称的保留的字。 有关详细信息，请参阅[路由：保留路由名称](xref:fundamentals/routing#reserved-routing-names)。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

| 方案 | 示例演示... |
| -------- | --------------------------- |
| [模型约定](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | 将路由模板和标头添加到应用的页面。 |
| [页面路由操作约定](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | 将路由模板添加到某个文件夹中的页面以及单个页面。 |
| [页面模型操作约定](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</li></ul> | 将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。 |

Razor 页面约定添加和配置为使用<xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*>扩展方法<xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*>中的服务集合上`Startup`类。 本主题稍后会介绍以下约定示例：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add( ... );
                options.Conventions.AddFolderRouteModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageRouteModelConvention("/About", model => { ... });
                options.Conventions.AddPageRoute("/Contact", "TheContactPage/{text?}");
                options.Conventions.AddFolderApplicationModelConvention("/OtherPages", model => { ... });
                options.Conventions.AddPageApplicationModelConvention("/About", model => { ... });
                options.Conventions.ConfigureFilter(model => { ... });
                options.Conventions.ConfigureFilter( ... );
            });
}
```

## <a name="route-order"></a>路由顺序

路由指定<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*>用于处理 （路由匹配）。

| 顺序            | 行为 |
| :--------------: | -------- |
| -1               | 处理其他路由之前处理该路由。 |
| 0                | 未指定顺序 （默认值）。 不分配`Order`(`Order = null`) 将默认路由`Order`为 0 （零） 进行处理。 |
| 1、 2、 &hellip; n | 指定路由处理顺序。 |

按照约定来建立路由处理：

* 按顺序处理的路由 (-1、 0、 1、 2、 &hellip; n)。
* 当路由具有相同`Order`、 最具体的路由匹配跟有更具体的路由。
* 当具有相同的路由`Order`和相同数量的参数匹配请求 URL，将它们添加到顺序处理路由<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>。

如果可能，避免具体取决于已建立的路由处理顺序。 通常情况下，路由选择与 URL 匹配的正确路由。 如果必须设置路由`Order`属性路由请求是否正确，应用程序的路由方案，可能令人困惑，向客户端和脆弱来维护。 查找以简化应用程序的路由方案。 示例应用程序需要处理订单，若要演示几个路由方案使用的单个应用程序的显式路由。 但是，您应尝试避免设置路由的做法`Order`生产应用中。

Razor Pages 路由和 MVC 控制器路由共享一个实现。 路由顺序 MVC 主题中的信息位于[路由到控制器操作：对属性路由排序](xref:mvc/controllers/routing#ordering-attribute-routes)。

## <a name="model-conventions"></a>模型约定

添加的委托<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>以添加[建模约定](xref:mvc/controllers/application-model#conventions)适用于 Razor 页面。

### <a name="add-a-route-model-convention-to-all-pages"></a>将路由模型约定添加到所有页面

使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>到的集合<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>在页面路由过程中应用的实例模型构造。

示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `1`。 这可确保匹配行为示例应用程序中的以下路由：

* 路由模板`TheContactPage/{text?}`添加本主题后面。 联系人页面路由的默认顺序`null`(`Order = 0`)，因此它匹配之前`{globalTemplate?}`路由模板。
* `{aboutTemplate?}`路由模板添加本主题后面。 为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。 当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `Order` 属性，“RouteDataValue”会加载到 `RouteData.Values["globalTemplate"]` (`Order = 1`) 而不是 `RouteData.Values["aboutTemplate"]` (`Order = 2`) 中。
* `{otherPagesTemplate?}`路由模板添加本主题后面。 为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。 中的任何页时*Pages/OtherPages*文件夹请求与路由参数 (例如， `/OtherPages/Page1/RouteDataValue`)，"RouteDataValue"加载到`RouteData.Values["globalTemplate"]`(`Order = 1`)，而不`RouteData.Values["otherPagesTemplate"]`(`Order = 2`)由于设置`Order`属性。

如有可能，未设置`Order`，这会导致`Order = 0`。 依赖于路由，以选择正确的路由。

Razor 页面选项，例如，添加<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>，将添加到服务集合中添加 MVC 时`Startup.ConfigureServices`。 有关示例，请参阅[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：

![使用 GlobalRouteValue 路由段请求“关于”页面。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>将应用模型约定添加到所有页面

使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>到的集合<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>期间应用的实例页上的应用程序模型构建。

为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。 类构造函数采用 `name` 字符串和 `values` 字符串数组。 将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。 本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。

示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>将处理程序模型约定添加到的所有页面

使用<xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>到的集合<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention>期间应用的实例页处理程序模型构建。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>页面路由操作约定

默认路由模型提供程序派生自<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider>调用旨在为页面路由配置提供扩展点的约定。

### <a name="folder-route-model-convention"></a>文件夹路由模型约定

使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它在调用操作<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>所有指定的文件夹下的页面。

示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。 这可以保证的模板`{globalTemplate?}`(设置到主题中前面`1`) 提供单个路由值时，第一个路由数据值位置分配优先级。 如果在页*Pages/OtherPages*文件夹请求与路由参数值 (例如， `/OtherPages/Page1/RouteDataValue`)，"RouteDataValue"加载到`RouteData.Values["globalTemplate"]`(`Order = 1`)，而不`RouteData.Values["otherPagesTemplate"]`(`Order = 2`)由于设置`Order`属性。

如有可能，未设置`Order`，这会导致`Order = 0`。 依赖于路由，以选择正确的路由。

在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>页面路由模型约定

使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，它在调用操作<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel>具有指定名称的页。

示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 属性设置为 `2`。 这可以保证的模板`{globalTemplate?}`(设置到主题中前面`1`) 提供单个路由值时，第一个路由数据值位置分配优先级。 如果使用路由参数值在请求关于页面`/About/RouteDataValue`，"RouteDataValue"加载到`RouteData.Values["globalTemplate"]`(`Order = 1`)，而不`RouteData.Values["aboutTemplate"]`(`Order = 2`) 因设置而`Order`属性。

如有可能，未设置`Order`，这会导致`Order = 0`。 依赖于路由，以选择正确的路由。

在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

::: moniker range=">= aspnetcore-2.2"

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>参数转换器用于自定义页面路由

使用参数转换器可以自定义页面路由生成的 ASP.NET Core。 参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。 例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。

`PageRouteTransformerConvention`页面路由模型约定适用于在应用中自动生成的页路由文件夹和文件名称段参数转换器。 例如，Razor 页面文件位于 */Pages/SubscriptionManagement/ViewAll.cshtml*必须重写中其路由`/SubscriptionManagement/ViewAll`到`/subscription-management/view-all`。

`PageRouteTransformerConvention` 仅将转换的页面路由来自 Razor 页面文件夹和文件名称自动生成的段。 它不会转换与添加的路由段`@page`指令。 约定也不会转换添加的路由<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>。

`PageRouteTransformerConvention`注册中的一个选项为`Startup.ConfigureServices`:

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
            {
                options.Conventions.Add(
                    new PageRouteTransformerConvention(
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

## <a name="configure-a-page-route"></a>配置页面路由

使用<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>到页面的路由配置在指定的页的路径。 生成的页面链接使用指定的路由。 `AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。

示例应用为 *Contact.cshtml* 创建指向 `/TheContactPage` 的路由：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet5)]

还可在 `/Contact` 中通过默认路由访问“联系人”页面。

示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。 该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：

[!code-cshtml[](razor-pages-conventions/samples/2.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

请注意，在呈现的页面中，为**联系人**链接生成的 URL 反映了已更新的路由：

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。 如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。 呈现的页面显示“text”段值。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>页面模型操作约定

默认页面模型提供程序实现<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider>调用旨在为页面模型配置提供扩展点的约定。 在生成和修改页面发现及处理方案时，可使用这些约定。

在本部分中的示例，示例应用使用`AddHeaderAttribute`类，该类是<xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>，应用的响应标头：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。

**文件夹应用模型约定**

使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它在调用操作<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>实例指定的文件夹下的所有页面。

示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

**页面应用模型约定**

使用<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*>创建并添加<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，它在调用操作<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel>具有指定名称的页。

示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

**配置筛选器**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置指定的筛选器应用。 用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。 如果条件通过，则添加标头。 如果不通过，则应用 `EmptyFilter`。

`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。 由于 Razor 页面会忽略操作筛选器`EmptyFilter`运行安装程序，如果路径不包含将不起`OtherPages/Page2`。

在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

**配置筛选器工厂**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置指定的工厂应用[筛选器](xref:mvc/controllers/filters)所有 Razor 页面。

示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC 筛选器和页面筛选器 (IPageFilter)

Razor 页面会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor 页面使用处理程序方法。 其他类型的 MVC 筛选器是可供你使用：[授权](xref:mvc/controllers/filters#authorization-filters)，[异常](xref:mvc/controllers/filters#exception-filters)，[资源](xref:mvc/controllers/filters#resource-filters)，并且[结果](xref:mvc/controllers/filters#result-filters)。 有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。

页面筛选器 (<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>) 是适用于 Razor 页面的筛选器。 有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>
