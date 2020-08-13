---
title: 本地化可扩展性
author: hishamco
description: 了解如何在 ASP.NET Core 应用中扩展本地化 API。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/03/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization-extensibility
ms.openlocfilehash: e31a724107145ab72adfb395afec1cbd2775bac0
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017124"
---
# <a name="localization-extensibility"></a><span data-ttu-id="61a4d-103">本地化可扩展性</span><span class="sxs-lookup"><span data-stu-id="61a4d-103">Localization Extensibility</span></span>

<span data-ttu-id="61a4d-104">作者：[Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="61a4d-104">By [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="61a4d-105">本文：</span><span class="sxs-lookup"><span data-stu-id="61a4d-105">This article:</span></span>

* <span data-ttu-id="61a4d-106">列出本地化 API 上的可扩展性点。</span><span class="sxs-lookup"><span data-stu-id="61a4d-106">Lists the extensibility points on the localization APIs.</span></span>
* <span data-ttu-id="61a4d-107">提供有关如何扩展 ASP.NET Core 应用本地化的说明。</span><span class="sxs-lookup"><span data-stu-id="61a4d-107">Provides instructions on how to extend ASP.NET Core app localization.</span></span>

## <a name="extensible-points-in-localization-apis"></a><span data-ttu-id="61a4d-108">本地化 API 中的可扩展点</span><span class="sxs-lookup"><span data-stu-id="61a4d-108">Extensible Points in Localization APIs</span></span>

<span data-ttu-id="61a4d-109">ASP.NET Core 本地化 API 生成是可扩展的。</span><span class="sxs-lookup"><span data-stu-id="61a4d-109">ASP.NET Core localization APIs are built to be extensible.</span></span> <span data-ttu-id="61a4d-110">开发人员可利用可扩展性根据需求自定义本地化。</span><span class="sxs-lookup"><span data-stu-id="61a4d-110">Extensibility allows developers to customize the localization according to their needs.</span></span> <span data-ttu-id="61a4d-111">例如，[OrchardCore](https://github.com/orchardCMS/OrchardCore/) 有一个 `POStringLocalizer`。</span><span class="sxs-lookup"><span data-stu-id="61a4d-111">For instance, [OrchardCore](https://github.com/orchardCMS/OrchardCore/) has a `POStringLocalizer`.</span></span> <span data-ttu-id="61a4d-112">`POStringLocalizer` 详细描述了如何使用[可移植对象本地化](xref:fundamentals/portable-object-localization)来使用 `PO` 文件存储本地化资源。</span><span class="sxs-lookup"><span data-stu-id="61a4d-112">`POStringLocalizer` describes in detail using [Portable Object localization](xref:fundamentals/portable-object-localization) to use `PO` files to store localization resources.</span></span>

<span data-ttu-id="61a4d-113">本文列出了本地化 API 提供的两个主要扩展点：</span><span class="sxs-lookup"><span data-stu-id="61a4d-113">This article lists the two main extensibility points that localization APIs provide:</span></span> 

* <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>
* <xref:Microsoft.Extensions.Localization.IStringLocalizer>

## <a name="localization-culture-providers"></a><span data-ttu-id="61a4d-114">本地化区域性提供程序</span><span class="sxs-lookup"><span data-stu-id="61a4d-114">Localization Culture Providers</span></span>

<span data-ttu-id="61a4d-115">ASP.NET Core 本地化 API 有四个默认提供程序，可用于确定正在执行的请求的当前区域性：</span><span class="sxs-lookup"><span data-stu-id="61a4d-115">ASP.NET Core localization APIs have four default providers that can determine the current culture of an executing request:</span></span>

* <xref:Microsoft.AspNetCore.Localization.QueryStringRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CookieRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.AcceptLanguageHeaderRequestCultureProvider>
* <xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider>

<span data-ttu-id="61a4d-116">[本地化中间件](xref:fundamentals/localization)文档中提供了对前面的提供程序的详细介绍。</span><span class="sxs-lookup"><span data-stu-id="61a4d-116">The preceding providers are described in detail in the [Localization Middleware](xref:fundamentals/localization) documentation.</span></span> <span data-ttu-id="61a4d-117">如果默认提供程序无法满足需求，请使用以下一种方法生成自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="61a4d-117">If the default providers don't meet your needs, build a custom provider using one of the following approaches:</span></span>

### <a name="use-customrequestcultureprovider"></a><span data-ttu-id="61a4d-118">使用 CustomRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="61a4d-118">Use CustomRequestCultureProvider</span></span>

<span data-ttu-id="61a4d-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> 提供了一个自定义 <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider>，它使用简单的委托来确定当前的本地化区域性：</span><span class="sxs-lookup"><span data-stu-id="61a4d-119"><xref:Microsoft.AspNetCore.Localization.CustomRequestCultureProvider> provides a custom <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> that uses a simple delegate to determine the current localization culture:</span></span>

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

    var requestCulture = new ProviderCultureResult(currentCulture);
    
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

    var requestCulture = new ProviderCultureResult(currentCulture);
    
    return Task.FromResult(requestCulture);
}));
```

::: moniker-end

### <a name="use-a-new-implemetation-of-requestcultureprovider"></a><span data-ttu-id="61a4d-120">使用 RequestCultureProvider 的新实现</span><span class="sxs-lookup"><span data-stu-id="61a4d-120">Use a new implemetation of RequestCultureProvider</span></span>

<span data-ttu-id="61a4d-121">可以创建 <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> 的新实现，以确定来自自定义源的请求区域性信息。</span><span class="sxs-lookup"><span data-stu-id="61a4d-121">A new implementation of <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> can be created that determines the request culture information from a custom source.</span></span> <span data-ttu-id="61a4d-122">例如，自定义源可以是配置文件或数据库。</span><span class="sxs-lookup"><span data-stu-id="61a4d-122">For example, the custom source can be a configuration file or database.</span></span>

<span data-ttu-id="61a4d-123">以下示例演示了 `AppSettingsRequestCultureProvider`，它扩展了 <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> 以确定来自 appsettings.json 的请求区域性信息：</span><span class="sxs-lookup"><span data-stu-id="61a4d-123">The following example shows `AppSettingsRequestCultureProvider`, which extends the <xref:Microsoft.AspNetCore.Localization.RequestCultureProvider> to determine the request culture information from *appsettings.json*:</span></span>

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

## <a name="localization-resources"></a><span data-ttu-id="61a4d-124">本地化资源</span><span class="sxs-lookup"><span data-stu-id="61a4d-124">Localization resources</span></span>

<span data-ttu-id="61a4d-125">ASP.NET Core 本地化提供 <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>。</span><span class="sxs-lookup"><span data-stu-id="61a4d-125">ASP.NET Core localization provides <xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer>.</span></span> <span data-ttu-id="61a4d-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> 是 <xref:Microsoft.Extensions.Localization.IStringLocalizer> 的实现，它使用 `resx` 来存储本地化资源。</span><span class="sxs-lookup"><span data-stu-id="61a4d-126"><xref:Microsoft.Extensions.Localization.ResourceManagerStringLocalizer> is an implementation of <xref:Microsoft.Extensions.Localization.IStringLocalizer> that is uses `resx` to store localization resources.</span></span>

<span data-ttu-id="61a4d-127">不仅限于使用 `resx` 文件。</span><span class="sxs-lookup"><span data-stu-id="61a4d-127">You aren't limited to using `resx` files.</span></span> <span data-ttu-id="61a4d-128">通过实现 `IStringLocalized`，可以使用任何数据源。</span><span class="sxs-lookup"><span data-stu-id="61a4d-128">By implementing `IStringLocalized`, any data source can be used.</span></span>

<span data-ttu-id="61a4d-129">以下示例项目实现 <xref:Microsoft.Extensions.Localization.IStringLocalizer>：</span><span class="sxs-lookup"><span data-stu-id="61a4d-129">The following example projects implement <xref:Microsoft.Extensions.Localization.IStringLocalizer>:</span></span> 

* [<span data-ttu-id="61a4d-130">EFStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="61a4d-130">EFStringLocalizer</span></span>](https://github.com/aspnet/Entropy/tree/master/samples/Localization.EntityFramework)
* [<span data-ttu-id="61a4d-131">JsonStringLocalizer</span><span class="sxs-lookup"><span data-stu-id="61a4d-131">JsonStringLocalizer</span></span>](https://github.com/hishamco/My.Extensions.Localization.Json)
* [<span data-ttu-id="61a4d-132">SqlLocalizer</span><span class="sxs-lookup"><span data-stu-id="61a4d-132">SqlLocalizer</span></span>](https://github.com/damienbod/AspNetCoreLocalization)
