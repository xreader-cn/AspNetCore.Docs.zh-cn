---
title: 本地化可扩展性
author: hishamco
description: 了解如何在 ASP.NET Core 应用中扩展本地化 API。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
uid: fundamentals/localization-extensibility
ms.openlocfilehash: dfa2efe78b2e1e118e6b3f09bfc41f3330e1d721
ms.sourcegitcommit: 020c3760492efed71b19e476f25392dda5dd7388
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/12/2019
ms.locfileid: "72288938"
---
# <a name="localization-extensibility"></a>本地化可扩展性

作者：[Hisham Bin Ateya](https://github.com/hishamco)

本文：

* 列出本地化 API 上的可扩展性点。
* 提供有关如何扩展 ASP.NET Core 应用本地化的说明。

## <a name="extensible-points-in-localization-apis"></a>本地化 API 中的可扩展点

ASP.NET Core 本地化 API 生成是可扩展的。 开发人员可利用可扩展性根据需求自定义本地化。 例如，[OrchardCore](https://github.com/orchardCMS/OrchardCore/) 有一个 `POStringLocalizer`。 `POStringLocalizer` 详细描述了如何使用[可移植对象本地化](xref:fundamentals/portable-object-localization)来使用 `PO` 文件存储本地化资源。

本文列出了本地化 API 提供的两个主要扩展点： 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a>本地化区域性提供程序

ASP.NET Core 本地化 API 有四个默认提供程序，可用于确定正在执行的请求的当前区域性：

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

[本地化中间件](xref:fundamentals/localization)文档中提供了对前面的提供程序的详细介绍。 如果默认提供程序无法满足需求，请使用以下一种方法生成自定义提供程序：

### <a name="use-customrequestcultureprovider"></a>使用 CustomRequestCultureProvider

<xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> 提供了一个自定义 <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>，它使用简单的委托来确定当前的本地化区域性：

::: moniker range="< aspnetcore-3.0"
```csharp
options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

::: moniker range=">= aspnetcore-3.0"
```csharp
options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
{
    var currentCulture = "en";
    var segments = context.Request.Path.Value.Split(new char[] { '/' }, 
        StringSplitOptions.RemoveEmptyEntries);

    if (segments.Length > 1 && segments[0].Length == 2)
    {
        currentCulture = segments[0];
    }

    var requestCulture = new ProviderCultureResult(culture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a>使用 RequestCultureProvider 的新实现

可以创建 <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> 的新实现，以确定来自自定义源的请求区域性信息。 例如，自定义源可以是配置文件或数据库。

以下示例演示了 `AppSettingsRequestCultureProvider`，它扩展了 <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> 以确定来自 appsettings.json 的请求区域性信息  ：

```csharp
public class AppSettingsRequestCultureProvider : RequestCultureProvider
{
    public string CultureKey { get; set; } = "culture";

    public string UICultureKey { get; set; } = "ui-culture";

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
        {
            throw new ArgumentNullException();
        }

        var configuration = httpContext.RequestServices.GetService<IConfigurationRoot>();
        var culture = configuration[CultureKey];
        var uiCulture = configuration[UICultureKey];

        if (culture == null && uiCulture == null)
        {
            return Task.FromResult((ProviderCultureResult)null);
        }

        if (culture != null && uiCulture == null)
        {
            uiCulture = culture;
        }

        if (culture == null && uiCulture != null)
        {
            culture = uiCulture;
        }
        
        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

## <a name="localization-resources"></a>本地化资源

ASP.NET Core 本地化提供 <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>。 <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> 是 <xref:Microsoft.Extensions.Localization.IStringLocalizer> 的实现，它使用 `resx` 来存储本地化资源。

不仅限于使用 `resx` 文件。 通过实现 `IStringLocalized`，可以使用任何数据源。

以下示例项目实现 <xref:Microsoft.Extensions.Localization.IStringLocalizer>： 

* [EFStringLocalizer](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [JsonStringLocalizer](https://github.com/hishamco/My.Extensions.Localization.Json)
* [SqlLocalizer](https://github.com/damienbod/AspNetCoreLocalization)
