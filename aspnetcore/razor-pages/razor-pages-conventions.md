---
title: ASP.NET Core 中 Razor 页面的路由和应用约定
author: guardrex
description: 了解路由和应用模型提供程序约定如何帮助控制页面路由、发现和处理。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: razor-pages/razor-pages-conventions
ms.openlocfilehash: d8377c0a0b8a29fe4b6a7fa67beeff84927c8b74
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114765"
---
# <a name="razor-pages-route-and-app-conventions-in-aspnet-core"></a>ASP.NET Core 中 Razor 页面的路由和应用约定

作者：[Luke Latham](https://github.com/guardrex)

::: moniker range=">= aspnetcore-3.0"

了解如何使用[页面路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor 页面应用中的页面路由、发现和处理。

需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。

若要指定页路由、添加路由段或向路由添加参数，请使用页的 `@page` 指令。 有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。

有些保留字不能用作路由段或参数名称。 有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

| 场景 | 示例演示... |
| -------- | --------------------------- |
| [模型约定](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | 将路由模板和标头添加到应用的页面。 |
| [页面路由操作约定](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | 将路由模板添加到某个文件夹中的页面以及单个页面。 |
| [页面模型操作约定](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</li></ul> | 将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。 |

使用 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 扩展方法添加和配置 Razor Pages 约定，以 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` 类中的服务集合。 本主题稍后会介绍以下约定示例：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>路由顺序

路由指定了用于处理的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> （路由匹配）。

| Order            | 行为 |
| :--------------: | -------- |
| -1               | 在处理其他路由之前处理路由。 |
| 0                | 未指定顺序（默认值）。 不分配 `Order` （`Order = null`）默认情况下，路由 `Order` 为0（零）以进行处理。 |
| 1，2，&hellip; n | 指定路由处理顺序。 |

按约定建立路由处理：

* 按顺序处理路由（-1、0、1、2、&hellip; n）。
* 当路由具有相同的 `Order`时，首先匹配最具体的路由，然后匹配不太具体的路由。
* 当具有相同 `Order` 和相同数量参数的路由匹配请求 URL 时，路由将按照它们添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>的顺序进行处理。

如果可能，请避免依赖于建立的路由处理顺序。 通常，路由将选择 URL 匹配的正确路由。 如果必须将路由 `Order` 属性设置为正确路由请求，则应用的路由方案可能会使客户端混乱，并使其保持脆弱。 设法简化应用的路由方案。 该示例应用需要显式路由处理顺序才能使用单个应用来演示几个路由方案。 但是，你应尝试避免在生产应用中设置路由 `Order` 的做法。

Razor Pages 路由和 MVC 控制器路由共享一个实现。 有关 MVC 主题中的路由顺序的信息，请[参阅路由到控制器操作：排序属性路由](xref:mvc/controllers/routing#ordering-attribute-routes)。

## <a name="model-conventions"></a>模型约定

为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加一个委托，以便添加适用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。

### <a name="add-a-route-model-convention-to-all-pages"></a>向所有页面添加路由模型约定

使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，并将其添加到在页路由模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。

示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `1`。 这可确保示例应用中的以下路由匹配行为：

* 本主题稍后会添加 `TheContactPage/{text?}` 的路由模板。 联系人页路由的默认顺序为 `null` （`Order = 0`），因此它在 `{globalTemplate?}` 路由模板之前匹配。
* 本主题稍后会添加一个 `{aboutTemplate?}` 的路由模板。 为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。 当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `RouteData.Values["globalTemplate"]` 属性，“RouteDataValue”会加载到 `Order = 1` (`RouteData.Values["aboutTemplate"]`) 而不是 `Order = 2` (`Order`) 中。
* 本主题稍后会添加一个 `{otherPagesTemplate?}` 的路由模板。 为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。 当使用路由参数（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的任何页面时，将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。

请尽可能不要将 `Order`设置为 `Order = 0`。 依赖 "路由" 选择正确的路由。

将 MVC 添加到 `Startup.ConfigureServices`中的服务集合时，会添加 Razor Pages 选项，如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。 有关示例，请参阅[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet1)]

在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：

![使用 GlobalRouteValue 路由段请求“关于”页面。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>将应用模型约定添加到所有页面

使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，并将其添加到在页面应用模式构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。

为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。 类构造函数采用 `name` 字符串和 `values` 字符串数组。 将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。 本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。

示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet2)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>将处理程序模型约定添加到所有页面

使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>，并将其添加到在页处理程序模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>页面路由操作约定

派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 的默认路由模型提供程序调用用于提供扩展点以配置页面路由的约定。

### <a name="folder-route-model-convention"></a>文件夹路由模型约定

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 来创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该可对指定文件夹下的所有页面调用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上的操作。

示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。 这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。 如果使用路由参数值（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。

请尽可能不要将 `Order`设置为 `Order = 0`。 依赖 "路由" 选择正确的路由。

在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>页面路由模型约定

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。

示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。 这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。 如果使用 `/About/RouteDataValue`的路由参数值请求 "关于" 页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["aboutTemplate"]` （`Order = 2`），因为设置 `Order` 属性。

请尽可能不要将 `Order`设置为 `Order = 0`。 依赖 "路由" 选择正确的路由。

在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>使用参数转换器自定义页面路由

可以使用参数转换器自定义 ASP.NET Core 生成的页面路由。 参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。 例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。

`PageRouteTransformerConvention` 页路由模型约定将参数变压器应用到应用中自动生成的页面路由的文件夹和文件名段。 例如，位于 */Pages/SubscriptionManagement/ViewAll.cshtml*的 Razor Pages 文件会将其路由从 `/SubscriptionManagement/ViewAll` 重写到 `/subscription-management/view-all`中。

`PageRouteTransformerConvention` 仅转换来自 Razor Pages 文件夹和文件名的自动生成的页面路由段。 它不会转换添加了 `@page` 指令的路由段。 该约定还不会转换 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>添加的路由。

`PageRouteTransformerConvention` 注册为 `Startup.ConfigureServices`中的一个选项：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddRazorPages()
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

## <a name="configure-a-page-route"></a>配置页面路由

使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置指向指定页面路径中的页面的路由。 生成的页面链接使用指定的路由。 `AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。

示例应用为 `/TheContactPage`Contact.cshtml*创建指向* 的路由：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet5)]

还可在 `/Contact` 中通过默认路由访问“联系人”页面。

示例应用的“联系人”页面自定义路由允许使用可选的 `text` 路由段 (`{text?}`)。 该页面还在其 `@page` 指令中包含此可选段，以便访问者在 `/Contact` 路由中访问该页面：

[!code-cshtml[](razor-pages-conventions/samples/3.x/SampleApp/Pages/Contact.cshtml?highlight=1)]

请注意，在呈现的页面中，为**联系人**链接生成的 URL 反映了已更新的路由：

![导航栏中的示例应用“联系人”链接](razor-pages-conventions/_static/contact-link.png)

![检查呈现的 HTML 中的“联系人”链接，可看到 href 设置为“/TheContactPage”](razor-pages-conventions/_static/contact-link-source.png)

在常规路由 `/Contact` 或自定义路由 `/TheContactPage` 中访问“联系人”页面。 如果提供附加的 `text` 路由段，该页面会显示所提供的 HTML 编码段：

![在 URL 中提供可选“'text”路由段“TextValue”的 Microsoft Edge 浏览器示例。 呈现的页面显示“text”段值。](razor-pages-conventions/_static/route-segment-with-custom-route.png)

## <a name="page-model-action-conventions"></a>页面模型操作约定

实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序调用用于为配置页面模型提供扩展点的约定。 在生成和修改页面发现及处理方案时，可使用这些约定。

对于本部分中的示例，示例应用使用 `AddHeaderAttribute` 类，该类是一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>，它应用响应标头：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。

**文件夹应用模型约定**

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在指定文件夹下的所有页面的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。

示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet6)]

在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

**页面应用模型约定**

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。

示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet7)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

**配置筛选器**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置要应用的指定筛选器。 用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet8)]

页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。 如果条件通过，则添加标头。 如果不通过，则应用 `EmptyFilter`。

`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。 由于 Razor Pages 忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，则 `EmptyFilter` 不起作用。

在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

**配置筛选器工厂**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置指定工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。

示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*：

[!code-csharp[](razor-pages-conventions/samples/3.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC 筛选器和页面筛选器 (IPageFilter)

Razor 页面会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor 页面使用处理程序方法。 可使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。 有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。

页面筛选器（<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>）是应用于 Razor Pages 的筛选器。 有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

了解如何使用[页面路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor 页面应用中的页面路由、发现和处理。

需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。

若要指定页路由、添加路由段或向路由添加参数，请使用页的 `@page` 指令。 有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。

有些保留字不能用作路由段或参数名称。 有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

| 场景 | 示例演示... |
| -------- | --------------------------- |
| [模型约定](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | 将路由模板和标头添加到应用的页面。 |
| [页面路由操作约定](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | 将路由模板添加到某个文件夹中的页面以及单个页面。 |
| [页面模型操作约定](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</li></ul> | 将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。 |

使用 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 扩展方法添加和配置 Razor Pages 约定，以 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` 类中的服务集合。 本主题稍后会介绍以下约定示例：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>路由顺序

路由指定了用于处理的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> （路由匹配）。

| Order            | 行为 |
| :--------------: | -------- |
| -1               | 在处理其他路由之前处理路由。 |
| 0                | 未指定顺序（默认值）。 不分配 `Order` （`Order = null`）默认情况下，路由 `Order` 为0（零）以进行处理。 |
| 1，2，&hellip; n | 指定路由处理顺序。 |

按约定建立路由处理：

* 按顺序处理路由（-1、0、1、2、&hellip; n）。
* 当路由具有相同的 `Order`时，首先匹配最具体的路由，然后匹配不太具体的路由。
* 当具有相同 `Order` 和相同数量参数的路由匹配请求 URL 时，路由将按照它们添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>的顺序进行处理。

如果可能，请避免依赖于建立的路由处理顺序。 通常，路由将选择 URL 匹配的正确路由。 如果必须将路由 `Order` 属性设置为正确路由请求，则应用的路由方案可能会使客户端混乱，并使其保持脆弱。 设法简化应用的路由方案。 该示例应用需要显式路由处理顺序才能使用单个应用来演示几个路由方案。 但是，你应尝试避免在生产应用中设置路由 `Order` 的做法。

Razor Pages 路由和 MVC 控制器路由共享一个实现。 有关 MVC 主题中的路由顺序的信息，请[参阅路由到控制器操作：排序属性路由](xref:mvc/controllers/routing#ordering-attribute-routes)。

## <a name="model-conventions"></a>模型约定

为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加一个委托，以便添加适用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。

### <a name="add-a-route-model-convention-to-all-pages"></a>向所有页面添加路由模型约定

使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，并将其添加到在页路由模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。

示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `1`。 这可确保示例应用中的以下路由匹配行为：

* 本主题稍后会添加 `TheContactPage/{text?}` 的路由模板。 联系人页路由的默认顺序为 `null` （`Order = 0`），因此它在 `{globalTemplate?}` 路由模板之前匹配。
* 本主题稍后会添加一个 `{aboutTemplate?}` 的路由模板。 为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。 当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `RouteData.Values["globalTemplate"]` 属性，“RouteDataValue”会加载到 `Order = 1` (`RouteData.Values["aboutTemplate"]`) 而不是 `Order = 2` (`Order`) 中。
* 本主题稍后会添加一个 `{otherPagesTemplate?}` 的路由模板。 为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。 当使用路由参数（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的任何页面时，将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。

请尽可能不要将 `Order`设置为 `Order = 0`。 依赖 "路由" 选择正确的路由。

将 MVC 添加到 `Startup.ConfigureServices`中的服务集合时，会添加 Razor Pages 选项，如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。 有关示例，请参阅[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：

![使用 GlobalRouteValue 路由段请求“关于”页面。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>将应用模型约定添加到所有页面

使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，并将其添加到在页面应用模式构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。

为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。 类构造函数采用 `name` 字符串和 `values` 字符串数组。 将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。 本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。

示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>将处理程序模型约定添加到所有页面

使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>，并将其添加到在页处理程序模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>页面路由操作约定

派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 的默认路由模型提供程序调用用于提供扩展点以配置页面路由的约定。

### <a name="folder-route-model-convention"></a>文件夹路由模型约定

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 来创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该可对指定文件夹下的所有页面调用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上的操作。

示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。 这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。 如果使用路由参数值（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。

请尽可能不要将 `Order`设置为 `Order = 0`。 依赖 "路由" 选择正确的路由。

在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>页面路由模型约定

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。

示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。 这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。 如果使用 `/About/RouteDataValue`的路由参数值请求 "关于" 页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["aboutTemplate"]` （`Order = 2`），因为设置 `Order` 属性。

请尽可能不要将 `Order`设置为 `Order = 0`。 依赖 "路由" 选择正确的路由。

在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="use-a-parameter-transformer-to-customize-page-routes"></a>使用参数转换器自定义页面路由

可以使用参数转换器自定义 ASP.NET Core 生成的页面路由。 参数转换程序实现 `IOutboundParameterTransformer` 并转换参数值。 例如，一个自定义 `SlugifyParameterTransformer` 参数转换程序可将 `SubscriptionManagement` 路由值更改为 `subscription-management`。

`PageRouteTransformerConvention` 页路由模型约定将参数变压器应用到应用中自动生成的页面路由的文件夹和文件名段。 例如，位于 */Pages/SubscriptionManagement/ViewAll.cshtml*的 Razor Pages 文件会将其路由从 `/SubscriptionManagement/ViewAll` 重写到 `/subscription-management/view-all`中。

`PageRouteTransformerConvention` 仅转换来自 Razor Pages 文件夹和文件名的自动生成的页面路由段。 它不会转换添加了 `@page` 指令的路由段。 该约定还不会转换 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*>添加的路由。

`PageRouteTransformerConvention` 注册为 `Startup.ConfigureServices`中的一个选项：

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

## <a name="configure-a-page-route"></a>配置页面路由

使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置指向指定页面路径中的页面的路由。 生成的页面链接使用指定的路由。 `AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。

示例应用为 `/TheContactPage`Contact.cshtml*创建指向* 的路由：

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

实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序调用用于为配置页面模型提供扩展点的约定。 在生成和修改页面发现及处理方案时，可使用这些约定。

对于本部分中的示例，示例应用使用 `AddHeaderAttribute` 类，该类是一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>，它应用响应标头：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。

**文件夹应用模型约定**

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在指定文件夹下的所有页面的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。

示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

**页面应用模型约定**

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。

示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

**配置筛选器**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置要应用的指定筛选器。 用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。 如果条件通过，则添加标头。 如果不通过，则应用 `EmptyFilter`。

`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。 由于 Razor Pages 忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，则 `EmptyFilter` 不起作用。

在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

**配置筛选器工厂**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置指定工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。

示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC 筛选器和页面筛选器 (IPageFilter)

Razor 页面会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor 页面使用处理程序方法。 可使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。 有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。

页面筛选器（<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>）是应用于 Razor Pages 的筛选器。 有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

了解如何使用[页面路由和应用模型提供程序约定](xref:mvc/controllers/application-model#conventions)来控制 Razor 页面应用中的页面路由、发现和处理。

需要为各个页面配置自定义页面路由时，可使用本主题稍后所述的 [AddPageRoute 约定](#configure-a-page-route)配置页面路由。

若要指定页路由、添加路由段或向路由添加参数，请使用页的 `@page` 指令。 有关详细信息，请参阅[自定义路由](xref:razor-pages/index#custom-routes)。

有些保留字不能用作路由段或参数名称。 有关详细信息，请参阅[路由：保留的路由名称](xref:fundamentals/routing#reserved-routing-names)。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

| 场景 | 示例演示... |
| -------- | --------------------------- |
| [模型约定](#model-conventions)<br><br>Conventions.Add<ul><li>IPageRouteModelConvention</li><li>IPageApplicationModelConvention</li><li>IPageHandlerModelConvention</li></ul> | 将路由模板和标头添加到应用的页面。 |
| [页面路由操作约定](#page-route-action-conventions)<ul><li>AddFolderRouteModelConvention</li><li>AddPageRouteModelConvention</li><li>AddPageRoute</li></ul> | 将路由模板添加到某个文件夹中的页面以及单个页面。 |
| [页面模型操作约定](#page-model-action-conventions)<ul><li>AddFolderApplicationModelConvention</li><li>AddPageApplicationModelConvention</li><li>ConfigureFilter（筛选器类、Lambda 表达式或筛选器工厂）</li></ul> | 将标头添加到某个文件夹中的多个页面，将标头添加到单个页面，以及配置[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)以将标头添加到应用的页面。 |

使用 <xref:Microsoft.Extensions.DependencyInjection.MvcRazorPagesMvcBuilderExtensions.AddRazorPagesOptions*> 扩展方法添加和配置 Razor Pages 约定，以 <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddMvc*> `Startup` 类中的服务集合。 本主题稍后会介绍以下约定示例：

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddRazorPagesOptions(options =>
        {
            options.Conventions.Add( ... );
            options.Conventions.AddFolderRouteModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageRouteModelConvention(
                "/About", model => { ... });
            options.Conventions.AddPageRoute(
                "/Contact", "TheContactPage/{text?}");
            options.Conventions.AddFolderApplicationModelConvention(
                "/OtherPages", model => { ... });
            options.Conventions.AddPageApplicationModelConvention(
                "/About", model => { ... });
            options.Conventions.ConfigureFilter(model => { ... });
            options.Conventions.ConfigureFilter( ... );
        });
}
```

## <a name="route-order"></a>路由顺序

路由指定了用于处理的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> （路由匹配）。

| Order            | 行为 |
| :--------------: | -------- |
| -1               | 在处理其他路由之前处理路由。 |
| 0                | 未指定顺序（默认值）。 不分配 `Order` （`Order = null`）默认情况下，路由 `Order` 为0（零）以进行处理。 |
| 1，2，&hellip; n | 指定路由处理顺序。 |

按约定建立路由处理：

* 按顺序处理路由（-1、0、1、2、&hellip; n）。
* 当路由具有相同的 `Order`时，首先匹配最具体的路由，然后匹配不太具体的路由。
* 当具有相同 `Order` 和相同数量参数的路由匹配请求 URL 时，路由将按照它们添加到 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection>的顺序进行处理。

如果可能，请避免依赖于建立的路由处理顺序。 通常，路由将选择 URL 匹配的正确路由。 如果必须将路由 `Order` 属性设置为正确路由请求，则应用的路由方案可能会使客户端混乱，并使其保持脆弱。 设法简化应用的路由方案。 该示例应用需要显式路由处理顺序才能使用单个应用来演示几个路由方案。 但是，你应尝试避免在生产应用中设置路由 `Order` 的做法。

Razor Pages 路由和 MVC 控制器路由共享一个实现。 有关 MVC 主题中的路由顺序的信息，请[参阅路由到控制器操作：排序属性路由](xref:mvc/controllers/routing#ordering-attribute-routes)。

## <a name="model-conventions"></a>模型约定

为 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 添加一个委托，以便添加适用于 Razor Pages 的[模型约定](xref:mvc/controllers/application-model#conventions)。

### <a name="add-a-route-model-convention-to-all-pages"></a>向所有页面添加路由模型约定

使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，并将其添加到在页路由模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。

示例应用将 `{globalTemplate?}` 路由模板添加到应用中的所有页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalTemplatePageRouteModelConvention.cs?name=snippet1)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `1`。 这可确保示例应用中的以下路由匹配行为：

* 本主题稍后会添加 `TheContactPage/{text?}` 的路由模板。 联系人页路由的默认顺序为 `null` （`Order = 0`），因此它在 `{globalTemplate?}` 路由模板之前匹配。
* 本主题稍后会添加一个 `{aboutTemplate?}` 的路由模板。 为 `{aboutTemplate?}` 模板指定的 `Order` 为 `2`。 当在 `/About/RouteDataValue` 中请求“关于”页面时，由于设置了 `RouteData.Values["globalTemplate"]` 属性，“RouteDataValue”会加载到 `Order = 1` (`RouteData.Values["aboutTemplate"]`) 而不是 `Order = 2` (`Order`) 中。
* 本主题稍后会添加一个 `{otherPagesTemplate?}` 的路由模板。 为 `{otherPagesTemplate?}` 模板指定的 `Order` 为 `2`。 当使用路由参数（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的任何页面时，将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。

请尽可能不要将 `Order`设置为 `Order = 0`。 依赖 "路由" 选择正确的路由。

将 MVC 添加到 `Startup.ConfigureServices`中的服务集合时，会添加 Razor Pages 选项，如添加 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions>。 有关示例，请参阅[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/razor-pages-conventions/samples/)。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet1)]

在 `localhost:5000/About/GlobalRouteValue` 中请求示例的“关于”页面并检查结果：

![使用 GlobalRouteValue 路由段请求“关于”页面。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/about-page-global-template.png)

### <a name="add-an-app-model-convention-to-all-pages"></a>将应用模型约定添加到所有页面

使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，并将其添加到在页面应用模式构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。

为了演示此约定以及本主题后面的其他约定，示例应用包含了一个 `AddHeaderAttribute` 类。 类构造函数采用 `name` 字符串和 `values` 字符串数组。 将在其 `OnResultExecuting` 方法中使用这些值来设置响应标头。 本主题后面的[页面模型操作约定](#page-model-action-conventions)部分展示了完整的类。

示例应用使用 `AddHeaderAttribute` 类将标头 `GlobalHeader` 添加到应用中的所有页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalHeaderPageApplicationModelConvention.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet2)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加 GlobalHeader。](razor-pages-conventions/_static/about-page-global-header.png)

### <a name="add-a-handler-model-convention-to-all-pages"></a>将处理程序模型约定添加到所有页面

使用 <xref:Microsoft.AspNetCore.Mvc.RazorPages.RazorPagesOptions.Conventions> 创建 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageHandlerModelConvention>，并将其添加到在页处理程序模型构造期间应用的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageConvention> 实例的集合。

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Conventions/GlobalPageHandlerModelConvention.cs?name=snippet1)]

*Startup.cs*：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet10)]

## <a name="page-route-action-conventions"></a>页面路由操作约定

派生自 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelProvider> 的默认路由模型提供程序调用用于提供扩展点以配置页面路由的约定。

### <a name="folder-route-model-convention"></a>文件夹路由模型约定

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 来创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该可对指定文件夹下的所有页面调用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上的操作。

示例应用使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderRouteModelConvention*> 将 `{otherPagesTemplate?}` 路由模板添加到 *OtherPages* 文件夹中的页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet3)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。 这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。 如果使用路由参数值（例如 `/OtherPages/Page1/RouteDataValue`）请求*Pages/OtherPages*文件夹中的页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["otherPagesTemplate"]` （`Order = 2`），因为设置 `Order` 属性。

请尽可能不要将 `Order`设置为 `Order = 0`。 依赖 "路由" 选择正确的路由。

在 `localhost:5000/OtherPages/Page1/GlobalRouteValue/OtherPagesRouteValue` 中请求示例的 Page1 页面并检查结果：

![使用 GlobalRouteValue 和 OtherPagesRouteValue 路由段请求 OtherPages 文件夹中的 Page1。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/otherpages-page1-global-and-otherpages-templates.png)

### <a name="page-route-model-convention"></a>页面路由模型约定

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageRouteModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageRouteModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageRouteModel> 上调用操作。

示例应用使用 `AddPageRouteModelConvention` 将 `{aboutTemplate?}` 路由模板添加到“关于”页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet4)]

<xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel.Order*> 的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.AttributeRouteModel> 属性设置为 `2`。 这可确保在提供单个路由值时，将 `{globalTemplate?}` 的模板（在主题中设置为 `1`）被授予第一个路由数据值位置的优先级。 如果使用 `/About/RouteDataValue`的路由参数值请求 "关于" 页，则将 "RouteDataValue" 加载到 `RouteData.Values["globalTemplate"]` （`Order = 1`），而不是 `RouteData.Values["aboutTemplate"]` （`Order = 2`），因为设置 `Order` 属性。

请尽可能不要将 `Order`设置为 `Order = 0`。 依赖 "路由" 选择正确的路由。

在 `localhost:5000/About/GlobalRouteValue/AboutRouteValue` 中请求示例的“关于”页面并检查结果：

![使用 GlobalRouteValue 和 AboutRouteValue 路由段请求“关于”页面。 呈现的页面显示，在页面的 OnGet 方法中捕获了路由数据值。](razor-pages-conventions/_static/about-page-global-and-about-templates.png)

## <a name="configure-a-page-route"></a>配置页面路由

使用 <xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.AddPageRoute*> 配置指向指定页面路径中的页面的路由。 生成的页面链接使用指定的路由。 `AddPageRoute` 使用 `AddPageRouteModelConvention` 建立路由。

示例应用为 `/TheContactPage`Contact.cshtml*创建指向* 的路由：

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

实现 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelProvider> 的默认页面模型提供程序调用用于为配置页面模型提供扩展点的约定。 在生成和修改页面发现及处理方案时，可使用这些约定。

对于本部分中的示例，示例应用使用 `AddHeaderAttribute` 类，该类是一个 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>，它应用响应标头：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Filters/AddHeader.cs?name=snippet1)]

示例演示了如何使用约定将该属性应用于某个文件夹中的所有页面以及单个页面。

**文件夹应用模型约定**

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在指定文件夹下的所有页面的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 实例上调用操作。

示例演示了如何使用 `AddFolderApplicationModelConvention` 将标头 `OtherPagesHeader` 添加到应用的 *OtherPages* 文件夹内的页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet6)]

在 `localhost:5000/OtherPages/Page1` 中请求示例的 Page1 页面，并检查标头以查看结果：

![OtherPages/Page1 页面的响应标头显示已添加 OtherPagesHeader。](razor-pages-conventions/_static/page1-otherpages-header.png)

**页面应用模型约定**

使用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddPageApplicationModelConvention*> 创建和添加 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.IPageApplicationModelConvention>，该在具有指定名称的页的 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageApplicationModel> 上调用操作。

示例演示了如何使用 `AddPageApplicationModelConvention` 将标头 `AboutHeader` 添加到“关于”页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet7)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加 AboutHeader。](razor-pages-conventions/_static/about-page-about-header.png)

**配置筛选器**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置要应用的指定筛选器。 用户可以实现筛选器类，但示例应用演示了如何在 Lambda 表达式中实现筛选器，该筛选器在后台作为可返回筛选器的工厂实现：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet8)]

页面应用模型用于检查指向 *OtherPages* 文件夹中 Page2 页面的段的相对路径。 如果条件通过，则添加标头。 如果不通过，则应用 `EmptyFilter`。

`EmptyFilter` 是一种[操作筛选器](xref:mvc/controllers/filters#action-filters)。 由于 Razor Pages 忽略操作筛选器，因此，如果路径不包含 `OtherPages/Page2`，则 `EmptyFilter` 不起作用。

在 `localhost:5000/OtherPages/Page2` 中请求示例的 Page2 页面，并检查标头以查看结果：

![OtherPagesPage2Header 已添加到 Page2 的响应。](razor-pages-conventions/_static/page2-filter-header.png)

**配置筛选器工厂**

<xref:Microsoft.Extensions.DependencyInjection.PageConventionCollectionExtensions.ConfigureFilter*> 配置指定工厂，以将[筛选器](xref:mvc/controllers/filters)应用于所有 Razor Pages。

示例应用提供了一个示例，说明如何使用[筛选器工厂](xref:mvc/controllers/filters#ifilterfactory)将具有两个值的标头 `FilterFactoryHeader` 添加到应用的页面：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Startup.cs?name=snippet9)]

*AddHeaderWithFactory.cs*：

[!code-csharp[](razor-pages-conventions/samples/2.x/SampleApp/Factories/AddHeaderWithFactory.cs?name=snippet1)]

在 `localhost:5000/About` 中请求示例的“关于”页面，并检查标头以查看结果：

![“关于”页面的响应标头显示已添加两个 FilterFactoryHeader 标头。](razor-pages-conventions/_static/about-page-filter-factory-header.png)

## <a name="mvc-filters-and-the-page-filter-ipagefilter"></a>MVC 筛选器和页面筛选器 (IPageFilter)

Razor 页面会忽略 MVC [操作筛选器](xref:mvc/controllers/filters#action-filters)，因为 Razor 页面使用处理程序方法。 可使用其他类型的 MVC 筛选器：[授权](xref:mvc/controllers/filters#authorization-filters)、[异常](xref:mvc/controllers/filters#exception-filters)、[资源](xref:mvc/controllers/filters#resource-filters)和[结果](xref:mvc/controllers/filters#result-filters)。 有关详细信息，请参阅[筛选器](xref:mvc/controllers/filters)主题。

页面筛选器（<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>）是应用于 Razor Pages 的筛选器。 有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。

## <a name="additional-resources"></a>其他资源

* <xref:security/authorization/razor-pages-authorization>
* <xref:mvc/controllers/areas#areas-with-razor-pages>

::: moniker-end
