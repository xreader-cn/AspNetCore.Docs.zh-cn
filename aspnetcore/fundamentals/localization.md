---
title: 'title:ASP.NET Core 全球化和本地化 author: rick-anderson description:了解 ASP.NET Core 如何提供服务和中间件，将内容本地化为不同的语言和区域性。'
author: rick-anderson
description: 'ms.author: riande ms.date:2019 年 11 月 30 日 no-loc:'
ms.author: riande
ms.date: 11/30/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/localization
ms.openlocfilehash: e3b73a7a559d2f4a0803dc26dd42257c60fab884
ms.sourcegitcommit: e95b90585a9bc353f57643ed7d8e652dc498359b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2020
ms.locfileid: "84356955"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="89989-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="89989-103">'Blazor'</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="89989-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="89989-104">'Identity'</span></span>

<span data-ttu-id="89989-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="89989-105">'Let's Encrypt'</span></span> <span data-ttu-id="89989-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="89989-106">'Razor'</span></span>

<span data-ttu-id="89989-107">'SignalR' uid: fundamentals/localization</span><span class="sxs-lookup"><span data-stu-id="89989-107">'SignalR' uid: fundamentals/localization</span></span> <span data-ttu-id="89989-108">ASP.NET Core 全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="89989-108">Globalization and localization in ASP.NET Core</span></span> <span data-ttu-id="89989-109">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="89989-109">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="89989-110">多语言网站使网站可以覆盖更广泛的受众。</span><span class="sxs-lookup"><span data-stu-id="89989-110">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="89989-111">ASP.NET Core 提供的服务和中间件可将网站本地化为不同的语言和文化。</span><span class="sxs-lookup"><span data-stu-id="89989-111">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="89989-112">国际化涉及[全球化](/dotnet/api/system.globalization)和[本地化](/dotnet/standard/globalization-localization/localization)。</span><span class="sxs-lookup"><span data-stu-id="89989-112">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span>

1. <span data-ttu-id="89989-113">全球化是设计支持不同区域性的应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-113">Globalization is the process of designing apps that support different cultures.</span></span>
1. <span data-ttu-id="89989-114">全球化添加了对一组有关特定地理区域的已定义语言脚本的输入、显示和输出支持。</span><span class="sxs-lookup"><span data-stu-id="89989-114">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>
1. <span data-ttu-id="89989-115">本地化是将已经针对可本地化性进行处理的全球化应用调整为特定的区域性/区域设置的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-115">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span>

<span data-ttu-id="89989-116">有关详细信息，请参阅本文档邻近末尾的全球化和本地化术语。</span><span class="sxs-lookup"><span data-stu-id="89989-116">For more information see **Globalization and localization terms** near the end of this document.</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="89989-117">应用本地化涉及以下内容：</span><span class="sxs-lookup"><span data-stu-id="89989-117">App localization involves the following:</span></span>

<span data-ttu-id="89989-118">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="89989-118">Make the app's content localizable</span></span> <span data-ttu-id="89989-119">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="89989-119">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="89989-120">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="89989-120">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="89989-121">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="89989-121">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="89989-122">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="89989-122">Make the app's content localizable</span></span> <span data-ttu-id="89989-123">已为 <xref:Microsoft.Extensions.Localization.IStringLocalizer> 和 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> 设置架构，可以为开发本地化应用提高工作效率。</span><span class="sxs-lookup"><span data-stu-id="89989-123"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="89989-124">`IStringLocalizer` 使用 <xref:System.Resources.ResourceManager> 和 <xref:System.Resources.ResourceReader> 在运行时提供特定于区域性的资源。</span><span class="sxs-lookup"><span data-stu-id="89989-124">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="89989-125">接口具有一个索引器和一个用于返回本地化字符串的 `IEnumerable`。</span><span class="sxs-lookup"><span data-stu-id="89989-125">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="89989-126">`IStringLocalizer` 不要求在资源文件中存储默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-126">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="89989-127">你可以开发针对本地化的应用，且无需在开发初期创建资源资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-127">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="89989-128">下面的代码演示如何针对本地化包装字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="89989-128">The code below shows how to wrap the string "About Title" for localization.</span></span> <span data-ttu-id="89989-129">在前面的代码中，`IStringLocalizer<T>` 实现来源于[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="89989-129">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="89989-130">如果找不到“About Title”的本地化值，则返回索引器键，即字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="89989-130">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span>

<span data-ttu-id="89989-131">可将默认语言文本字符串保留在应用中并将它们包装在本地化工具中，以便你可集中精力开发应用。</span><span class="sxs-lookup"><span data-stu-id="89989-131">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="89989-132">你使用默认语言开发应用，并针对本地化步骤进行准备，而无需首先创建默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-132">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="89989-133">或者，你可以使用传统方法，并提供键以检索默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-133">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="89989-134">对于许多开发者而言，不具有默认语言 .resx 文件且简单包装字符串文本的新工作流可以减少本地化应用的开销。</span><span class="sxs-lookup"><span data-stu-id="89989-134">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span>

<span data-ttu-id="89989-135">其他开发者将首选传统工作流，因为它可以更轻松地使用较长字符串文本，更轻松地更新本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-135">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="89989-136">对包含 HTML 的资源使用 `IHtmlLocalizer<T>` 实现。</span><span class="sxs-lookup"><span data-stu-id="89989-136">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span>

<span data-ttu-id="89989-137">`IHtmlLocalizer` 对资源字符串中格式化的参数进行 HTML 编码，但不对资源字符串本身进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="89989-137">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="89989-138">在下面突出显示的示例中，仅 `name` 参数的值被 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="89989-138">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="89989-139">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="89989-139">**Note:** You generally want to only localize text and not HTML.</span></span> <span data-ttu-id="89989-140">最低程度，你可以从[依赖关系注入](dependency-injection.md)获取 `IStringLocalizerFactory`：</span><span class="sxs-lookup"><span data-stu-id="89989-140">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="89989-141">上面的代码演示了这两种工厂创建方法。</span><span class="sxs-lookup"><span data-stu-id="89989-141">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="89989-142">可以按控制器、区域对本地化字符串分区，或只有一个容器。</span><span class="sxs-lookup"><span data-stu-id="89989-142">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="89989-143">在示例应用中，名为 `SharedResource` 的虚拟类用于共享资源。</span><span class="sxs-lookup"><span data-stu-id="89989-143">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span> <span data-ttu-id="89989-144">某些开发者使用 `Startup` 类，以包含全局或共享字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-144">Some developers use the `Startup` class to contain global or shared strings.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="89989-145">在下面的示例中，使用 `InfoController` 和 `SharedResource` 本地化工具：</span><span class="sxs-lookup"><span data-stu-id="89989-145">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span> <span data-ttu-id="89989-146">视图本地化</span><span class="sxs-lookup"><span data-stu-id="89989-146">View localization</span></span> <span data-ttu-id="89989-147">`IViewLocalizer` 服务可为[视图](xref:mvc/views/overview)提供本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-147">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="89989-148">`ViewLocalizer` 类可实现此接口，并从视图文件路径找到资源位置。</span><span class="sxs-lookup"><span data-stu-id="89989-148">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="89989-149">下面的代码演示如何使用 `IViewLocalizer` 的默认实现：</span><span class="sxs-lookup"><span data-stu-id="89989-149">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="89989-150">`IViewLocalizer` 的默认实现可根据视图的文件名查找资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-150">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span>

| <span data-ttu-id="89989-151">没有使用全局共享资源文件的选项。</span><span class="sxs-lookup"><span data-stu-id="89989-151">There's no option to use a global shared resource file.</span></span> | <span data-ttu-id="89989-152">`ViewLocalizer` 使用 `IHtmlLocalizer` 实现本地化工具，因此 Razor 不会对本地化字符串进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="89989-152">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="89989-153">你可以参数化资源字符串，`IViewLocalizer` 将对参数进行 HTML 编码，但不会对资源字符串进行。</span><span class="sxs-lookup"><span data-stu-id="89989-153">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span>

<span data-ttu-id="89989-154">请考虑以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="89989-154">Consider the following Razor markup:</span></span>

<span data-ttu-id="89989-155">法语资源文件可以包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="89989-155">A French resource file could contain the following:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="89989-156">键</span><span class="sxs-lookup"><span data-stu-id="89989-156">Key</span></span>

<span data-ttu-id="89989-157">“值”</span><span class="sxs-lookup"><span data-stu-id="89989-157">Value</span></span> <span data-ttu-id="89989-158">呈现的视图可能包含资源文件中的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="89989-158">The rendered view would contain the HTML markup from the resource file.</span></span>

* <span data-ttu-id="89989-159">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="89989-159">**Note:** You generally want to only localize text and not HTML.</span></span>
* <span data-ttu-id="89989-160">若要在视图中使用共享资源文件，请注入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="89989-160">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="89989-161">DataAnnotations 本地化</span><span class="sxs-lookup"><span data-stu-id="89989-161">DataAnnotations localization</span></span> <span data-ttu-id="89989-162">DataAnnotations 错误消息已使用 `IStringLocalizer<T>` 本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-162">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="89989-163">使用选项 `ResourcesPath = "Resources"`，`RegisterViewModel` 中的错误消息可以存储在以下路径之一：</span><span class="sxs-lookup"><span data-stu-id="89989-163">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

<span data-ttu-id="89989-164">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-164">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="89989-165">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-165">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span> <span data-ttu-id="89989-166">在 ASP.NET Core MVC 1.1.0 和更高版本中，非验证属性已经进行了本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-166">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="89989-167">ASP.NET Core MVC 1.0 不会为非验证属性查找本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-167">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="89989-168">对多个类使用一个资源字符串</span><span class="sxs-lookup"><span data-stu-id="89989-168">Using one resource string for multiple classes</span></span>

<span data-ttu-id="89989-169">下面的代码演示如何针对具有多个类的验证属性使用一个资源字符串：</span><span class="sxs-lookup"><span data-stu-id="89989-169">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span> <span data-ttu-id="89989-170">在上面的代码中，`SharedResource` 是对应于存储验证消息的 resx 的类。</span><span class="sxs-lookup"><span data-stu-id="89989-170">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="89989-171">使用此方法，DataAnnotations 将仅使用 `SharedResource`，而不是每个类的资源。</span><span class="sxs-lookup"><span data-stu-id="89989-171">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span> <span data-ttu-id="89989-172">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="89989-172">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="89989-173">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="89989-173">SupportedCultures and SupportedUICultures</span></span> <span data-ttu-id="89989-174">ASP.NET Core 允许指定两个区域性值，`SupportedCultures` 和 `SupportedUICultures`。</span><span class="sxs-lookup"><span data-stu-id="89989-174">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="89989-175">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 对象可决定区域性相关函数的结果，如日期、时间、数字和货币格式等。</span><span class="sxs-lookup"><span data-stu-id="89989-175">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="89989-176">`SupportedCultures` 确定文本的排序顺序、大小写约定和字符串比较。</span><span class="sxs-lookup"><span data-stu-id="89989-176">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="89989-177">请参阅 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) 详细了解服务器如何获取区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-177">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span>

## <a name="resource-files"></a><span data-ttu-id="89989-178">`SupportedUICultures` 可确定按 [ResourceManager](/dotnet/api/system.resources.resourcemanager) 来查找哪些转换字符串（位于 .resx 文件）。</span><span class="sxs-lookup"><span data-stu-id="89989-178">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span>

<span data-ttu-id="89989-179">`ResourceManager` 只需查找 `CurrentUICulture` 决定的区域性特定字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-179">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="89989-180">.NET 中的每个线程都具有 `CurrentCulture` 和 `CurrentUICulture` 对象。</span><span class="sxs-lookup"><span data-stu-id="89989-180">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="89989-181">呈现区域性相关函数时，ASP.NET Core 可检查这些值。</span><span class="sxs-lookup"><span data-stu-id="89989-181">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="89989-182">例如，如果当前线程的区域性设置为“en-US”（英语，美国），`DateTime.Now.ToLongDateString()` 将显示“Thursday, February 18, 2016”，但如果 `CurrentCulture` 设置为“es-ES”（西班牙语，西班牙），则输出将为“jueves，18 de febrero de 2016”。</span><span class="sxs-lookup"><span data-stu-id="89989-182">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span> <span data-ttu-id="89989-183">资源文件</span><span class="sxs-lookup"><span data-stu-id="89989-183">Resource files</span></span>

1. <span data-ttu-id="89989-184">资源文件是将可本地化的字符串与代码分离的有用机制。</span><span class="sxs-lookup"><span data-stu-id="89989-184">A resource file is a useful mechanism for separating localizable strings from code.</span></span>

    ![非默认语言的转换字符串在 .resx 资源文件中单独显示。](localization/_static/newi.png)

2. <span data-ttu-id="89989-187">“es”是西班牙语的语言代码。</span><span class="sxs-lookup"><span data-stu-id="89989-187">"es" is the language code for Spanish.</span></span>

    ![要在 Visual Studio 中创建此资源文件，请支持以下操作：](localization/_static/res.png)

3. <span data-ttu-id="89989-189">在“解决方案资源管理器”中，右键单击将包含资源文件的文件夹 >“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="89989-189">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![嵌套的上下文菜单：在“解决方案资源管理器”中，“资源”可打开上下文菜单。](localization/_static/hola.png)

    <span data-ttu-id="89989-191">“添加”可打开第二个上下文菜单，突出显示“新项”命令。</span><span class="sxs-lookup"><span data-stu-id="89989-191">A second contextual menu is open for Add showing the New Item command highlighted.</span></span>

    ![在“搜索已安装的模板”框中，输入“资源”并命名该文件。](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="89989-193">“添加新项”对话框</span><span class="sxs-lookup"><span data-stu-id="89989-193">Add New Item dialog</span></span>

<span data-ttu-id="89989-194">在“名称”列中输入键值（本机字符串），在“值”列中输入转换字符串 。</span><span class="sxs-lookup"><span data-stu-id="89989-194">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span> <span data-ttu-id="89989-195">Welcome.es.resx 文件（西班牙语版 Welcome 资源文件）的单词 Hello 位于“名称”列，Hola（西班牙语版 Hello）位于“值”列</span><span class="sxs-lookup"><span data-stu-id="89989-195">Welcome.es.resx file (the Welcome resource file for Spanish) with the word Hello in the Name column and the word Hola (Hello in Spanish) in the Value column</span></span> <span data-ttu-id="89989-196">Visual Studio 将显示 Welcome.es.resx 文件。</span><span class="sxs-lookup"><span data-stu-id="89989-196">Visual Studio shows the *Welcome.es.resx* file.</span></span> <span data-ttu-id="89989-197">显示 Welcome Spanish (es) 资源文件的解决方案资源管理器</span><span class="sxs-lookup"><span data-stu-id="89989-197">Solution Explorer showing the Welcome Spanish (es) resource file</span></span> <span data-ttu-id="89989-198">资源文件命名</span><span class="sxs-lookup"><span data-stu-id="89989-198">Resource file naming</span></span>

<span data-ttu-id="89989-199">资源名称是类的完整类型名称减去程序集名称。</span><span class="sxs-lookup"><span data-stu-id="89989-199">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="89989-200">例如，类 `LocalizationWebsite.Web.Startup` 的主要程序集为 `LocalizationWebsite.Web.dll` 的项目中的法语资源将命名为 Startup.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-200">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="89989-201">类 `LocalizationWebsite.Web.Controllers.HomeController` 的资源将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-201">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="89989-202">如果目标类的命名空间与将需要完整类型名称的程序集名称不同。</span><span class="sxs-lookup"><span data-stu-id="89989-202">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="89989-203">例如，在示例项目中，类型 `ExtraNamespace.Tools` 的资源将命名为 ExtraNamespace.Tools.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-203">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span> <span data-ttu-id="89989-204">在示例项目中，`ConfigureServices` 方法将 `ResourcesPath` 设置为“资源”，因此主控制器的法语资源文件的项目相对路径是 Resources/Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-204">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span>

| <span data-ttu-id="89989-205">或者，你可以使用文件夹组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-205">Alternatively, you can use folders to organize resource files.</span></span> | <span data-ttu-id="89989-206">对于主控制器，该路径将为 Resources/Controllers/HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-206">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="89989-207">如果不使用 `ResourcesPath` 选项，.resx 文件将转到项目的基目录中。</span><span class="sxs-lookup"><span data-stu-id="89989-207">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> | <span data-ttu-id="89989-208">`HomeController` 的资源文件将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-208">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span>  |
| <span data-ttu-id="89989-209">是选择使用圆点还是路径命名约定，具体取决于你想如何组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-209">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>  | <span data-ttu-id="89989-210">资源名称</span><span class="sxs-lookup"><span data-stu-id="89989-210">Resource name</span></span> |
|    |     |

<span data-ttu-id="89989-211">圆点或路径命名</span><span class="sxs-lookup"><span data-stu-id="89989-211">Dot or path naming</span></span> <span data-ttu-id="89989-212">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-212">Resources/Controllers.HomeController.fr.resx</span></span> <span data-ttu-id="89989-213">圆点</span><span class="sxs-lookup"><span data-stu-id="89989-213">Dot</span></span> <span data-ttu-id="89989-214">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-214">Resources/Controllers/HomeController.fr.resx</span></span>

* <span data-ttu-id="89989-215">路径</span><span class="sxs-lookup"><span data-stu-id="89989-215">Path</span></span>

* <span data-ttu-id="89989-216">Razor 视图中使用 `@inject IViewLocalizer` 的资源文件遵循类似的模式。</span><span class="sxs-lookup"><span data-stu-id="89989-216">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span>

<span data-ttu-id="89989-217">可以使用圆点命名或路径命名约定对视图的资源文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="89989-217">The resource file for a view can be named using either dot naming or path naming.</span></span>

### <a name="rootnamespaceattribute"></a>Razor<span data-ttu-id="89989-218"> 视图资源文件可模拟其关联视图文件的路径。</span><span class="sxs-lookup"><span data-stu-id="89989-218"> view resource files mimic the path of their associated view file.</span></span> 

<span data-ttu-id="89989-219">假设我们将 `ResourcesPath` 设置为“Resources”，与 *Views/Home/About.cshtml* 视图关联的法语资源文件可能是下面其中之一 ：</span><span class="sxs-lookup"><span data-stu-id="89989-219">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span> 

> [!WARNING]
> <span data-ttu-id="89989-220">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-220">Resources/Views/Home/About.fr.resx</span></span> <span data-ttu-id="89989-221">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-221">Resources/Views.Home.About.fr.resx</span></span> 

<span data-ttu-id="89989-222">如果不使用 `ResourcesPath` 选项，视图的 .resx 文件将位于视图所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="89989-222">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

* <span data-ttu-id="89989-223">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="89989-223">RootNamespaceAttribute</span></span>
* <span data-ttu-id="89989-224">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性在程序集的根命名空间不同于程序集名称时，提供程序集的根命名空间。</span><span class="sxs-lookup"><span data-stu-id="89989-224">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> <span data-ttu-id="89989-225">当项目名称不是有效的 .NET 标识符时，可能会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="89989-225">This can occur when a project's name is not a valid .NET identifier.</span></span> 

<span data-ttu-id="89989-226">例如，`my-project-name.csproj` 将使用根命名空间 `my_project_name` 和导致此错误的程序集名称 `my-project-name`。</span><span class="sxs-lookup"><span data-stu-id="89989-226">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="89989-227">如果程序集的根命名空间不同于程序集名称：</span><span class="sxs-lookup"><span data-stu-id="89989-227">If the root namespace of an assembly is different than the assembly name:</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="89989-228">默认情况下无法进行本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-228">Localization does not work by default.</span></span>

<span data-ttu-id="89989-229">因程序集内搜索资源的方式导致本地化失败。</span><span class="sxs-lookup"><span data-stu-id="89989-229">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="89989-230">`RootNamespace` 是生成时间值，不可用于正在执行的进程。</span><span class="sxs-lookup"><span data-stu-id="89989-230">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> <span data-ttu-id="89989-231">如果 `RootNamespace` 不同于 `AssemblyName`，请在 AssemblyInfo.cs 中包括以下内容（参数值替换为实际值）：</span><span class="sxs-lookup"><span data-stu-id="89989-231">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span> <span data-ttu-id="89989-232">上述代码可成功解析 resx 文件。</span><span class="sxs-lookup"><span data-stu-id="89989-232">The preceding code enables the successful resolution of resx files.</span></span> <span data-ttu-id="89989-233">区域性回退行为</span><span class="sxs-lookup"><span data-stu-id="89989-233">Culture fallback behavior</span></span> <span data-ttu-id="89989-234">在搜索资源时，本地化会进行“区域性回退”。</span><span class="sxs-lookup"><span data-stu-id="89989-234">When searching for a resource, localization engages in "culture fallback".</span></span>

<span data-ttu-id="89989-235">从所请求的区域性开始，如果未能找到，则还原至该区域性的父区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-235">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="89989-236">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 属性代表父区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-236">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span>

* <span data-ttu-id="89989-237">这通常（但并不是总是）意味着从 ISO 中移除区域签名。</span><span class="sxs-lookup"><span data-stu-id="89989-237">This usually (but not always) means removing the national signifier from the ISO.</span></span>
* <span data-ttu-id="89989-238">例如，墨西哥的西班牙语方言为“es-MX”。</span><span class="sxs-lookup"><span data-stu-id="89989-238">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span>
* <span data-ttu-id="89989-239">它具备一个父级“es”西班牙，没有特别指定国家/地区。</span><span class="sxs-lookup"><span data-stu-id="89989-239">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="89989-240">假设你的站点接收到了一个区域性为“fr-CA”的“Welcome”资源的请求。</span><span class="sxs-lookup"><span data-stu-id="89989-240">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="89989-241">本地化系统按顺序查找以下资源，并选择第一个匹配项：</span><span class="sxs-lookup"><span data-stu-id="89989-241">The localization system looks for the following resources, in order, and selects the first match:</span></span> <span data-ttu-id="89989-242">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-242">*Welcome.fr-CA.resx*</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="89989-243">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-243">*Welcome.fr.resx*</span></span>

<span data-ttu-id="89989-244">*Welcome.resx*（如果 `NeutralResourcesLanguage` 为“fr-CA”）</span><span class="sxs-lookup"><span data-stu-id="89989-244">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span> <span data-ttu-id="89989-245">例如，如果删除了“.fr”区域性指示符，而且已将区域性设置为“法语”，将读取默认资源文件，并本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-245">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="89989-246">对于不满足所请求区域性的情况，资源管理器可指定默认资源或回退资源。</span><span class="sxs-lookup"><span data-stu-id="89989-246">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="89989-247">缺少适用于请求区域性的资源时，如果只想返回键，不得具有默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-247">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span> <span data-ttu-id="89989-248">使用 Visual Studio 生成资源文件</span><span class="sxs-lookup"><span data-stu-id="89989-248">Generate resource files with Visual Studio</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="89989-249">如果在 Visual Studio 中创建文件名没有区域性的资源文件（例如 Welcome.resx），Visual Studio 将创建一个 C# 类，并且具有每个字符串的属性。</span><span class="sxs-lookup"><span data-stu-id="89989-249">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span>

<span data-ttu-id="89989-250">这通常不是你想在 ASP.NET Core 中使用的。</span><span class="sxs-lookup"><span data-stu-id="89989-250">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="89989-251">你通常没有默认的 .resx 资源文件（没有区域性名称的 .resx 文件） 。</span><span class="sxs-lookup"><span data-stu-id="89989-251">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="89989-252">建议创建具有区域性名称（例如 Welcome.fr.resx）的 .resx 文件 。</span><span class="sxs-lookup"><span data-stu-id="89989-252">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="89989-253">创建具有区域性名称的 .resx 文件时，Visual Studio 不会生成类文件。</span><span class="sxs-lookup"><span data-stu-id="89989-253">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="configure-localization"></a><span data-ttu-id="89989-254">添加其他区域性</span><span class="sxs-lookup"><span data-stu-id="89989-254">Add other cultures</span></span>

<span data-ttu-id="89989-255">每个语言和区域性组合（除默认语言外）都需要唯一资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-255">Each language and culture combination (other than the default language) requires a unique resource file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="89989-256">通过新建 ISO 语言代码属于名称一部分的资源文件，为不同的区域性和区域设置创建资源文件（例如，en-us、fr-ca 和 en-gb）  。</span><span class="sxs-lookup"><span data-stu-id="89989-256">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="89989-257">这些 ISO 编码位于文件名和 .resx 文件扩展之间，如 Welcome.es-MX.resx（西班牙语/墨西哥） 。</span><span class="sxs-lookup"><span data-stu-id="89989-257">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

* <span data-ttu-id="89989-258">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="89989-258">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="89989-259">配置本地化</span><span class="sxs-lookup"><span data-stu-id="89989-259">Configure localization</span></span> <span data-ttu-id="89989-260">通过 `Startup.ConfigureServices` 方法配置本地化：</span><span class="sxs-lookup"><span data-stu-id="89989-260">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

* <span data-ttu-id="89989-261">`AddLocalization` 将本地化服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="89989-261">`AddLocalization` Adds the localization services to the services container.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="89989-262">上面的代码还将资源路径设置为“Resources”。</span><span class="sxs-lookup"><span data-stu-id="89989-262">The code above also sets the resources path to "Resources".</span></span>

<span data-ttu-id="89989-263">`AddViewLocalization` 添加对本地化视图文件的支持。</span><span class="sxs-lookup"><span data-stu-id="89989-263">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="89989-264">在此示例视图中，本地化基于视图文件后缀。</span><span class="sxs-lookup"><span data-stu-id="89989-264">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="89989-265">例如，Index.fr.cshtml 文件中的“fr”。</span><span class="sxs-lookup"><span data-stu-id="89989-265">For example "fr" in the *Index.fr.cshtml* file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="89989-266">`AddDataAnnotationsLocalization` 添加通过 `IStringLocalizer` 抽象对本地化 `DataAnnotations` 验证消息的支持。</span><span class="sxs-lookup"><span data-stu-id="89989-266">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span> <span data-ttu-id="89989-267">本地化中间件</span><span class="sxs-lookup"><span data-stu-id="89989-267">Localization middleware</span></span> <span data-ttu-id="89989-268">在本地化[中间件](xref:fundamentals/middleware/index)中设置有关请求的当前区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-268">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="89989-269">在 `Startup.Configure` 方法中启用本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="89989-269">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="89989-270">必须在中间件前面配置本地化中间件，它可能检查请求区域性（例如，`app.UseMvcWithDefaultRoute()`）。</span><span class="sxs-lookup"><span data-stu-id="89989-270">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span> <span data-ttu-id="89989-271">`UseRequestLocalization` 初始化 `RequestLocalizationOptions` 对象。</span><span class="sxs-lookup"><span data-stu-id="89989-271">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="89989-272">在每个请求上，枚举了 `RequestLocalizationOptions` 的 `RequestCultureProvider` 列表，使用了可成功决定请求区域性的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-272">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span>

<span data-ttu-id="89989-273">默认提供程序来自 `RequestLocalizationOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="89989-273">The default providers come from the `RequestLocalizationOptions` class:</span></span> <span data-ttu-id="89989-274">默认列表从最具体到最不具体排序。</span><span class="sxs-lookup"><span data-stu-id="89989-274">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="89989-275">在本文的后面部分，我们将了解如何更改顺序，甚至添加一个自定义区域性提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-275">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="89989-276">如果没有一个提供程序可以确定请求区域性，则使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="89989-276">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span> <span data-ttu-id="89989-277">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="89989-277">QueryStringRequestCultureProvider</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="89989-278">某些应用将使用查询字符串来设置[区域性和 UI 区域性](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)。</span><span class="sxs-lookup"><span data-stu-id="89989-278">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="89989-279">对于使用 Cookie 或接受语言标题方法的应用，向 URL 添加查询字符串有助于调试和测试代码。</span><span class="sxs-lookup"><span data-stu-id="89989-279">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="89989-280">默认情况下，`QueryStringRequestCultureProvider` 注册为 `RequestCultureProvider` 列表中的第一个本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-280">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span>

<span data-ttu-id="89989-281">传递查询字符串参数 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="89989-281">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="89989-282">下面的示例将特定区域性（语言和区域）设置为“西班牙语/墨西哥”：</span><span class="sxs-lookup"><span data-stu-id="89989-282">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

<span data-ttu-id="89989-283">如果仅传入两种区域性之一（`culture` 或 `ui-culture`，查询字符串提供程序将使用你传入的区域性设置这两个值。</span><span class="sxs-lookup"><span data-stu-id="89989-283">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="89989-284">例如，仅设置区域性将同时设置 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="89989-284">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

<span data-ttu-id="89989-285">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="89989-285">CookieRequestCultureProvider</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="89989-286">通常，生产应用将提供一种机制来使用 ASP.NET Core 区域性 Cookie 设置区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-286">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="89989-287">若要创建 Cookie，请使用 `MakeCookieValue` 方法。</span><span class="sxs-lookup"><span data-stu-id="89989-287">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="89989-288">`CookieRequestCultureProvider``DefaultCookieName` 将返回用来跟踪用户首选区域性信息的默认 Cookie 名称。</span><span class="sxs-lookup"><span data-stu-id="89989-288">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="89989-289">默认 Cookie 名称是 `.AspNetCore.Culture`。</span><span class="sxs-lookup"><span data-stu-id="89989-289">The default cookie name is `.AspNetCore.Culture`.</span></span> <span data-ttu-id="89989-290">Cookie 格式为 `c=%LANGCODE%|uic=%LANGCODE%`，其中`c` 是 `Culture`，`uic` 是 `UICulture`，例如：</span><span class="sxs-lookup"><span data-stu-id="89989-290">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span> <span data-ttu-id="89989-291">如果仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将同时用于区域性信息和 UI 区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-291">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="89989-292">接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="89989-292">The Accept-Language HTTP header</span></span>

1. <span data-ttu-id="89989-293">[接受语言标题](https://www.w3.org/International/questions/qa-accept-lang-locales)在大多数浏览器中可设置，最初用于指定用户的语言。</span><span class="sxs-lookup"><span data-stu-id="89989-293">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span>

2. <span data-ttu-id="89989-294">此设置指示浏览器已设置为发送或已从基础操作系统继承的内容。</span><span class="sxs-lookup"><span data-stu-id="89989-294">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span>

    ![浏览器请求的接受语言 HTTP 标题不是检测用户首选语言的可靠方法（请参阅 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)（在浏览器中设置首选项）。](localization/_static/lang.png)

3. <span data-ttu-id="89989-296">生产应用应包括一种用户可以自定义区域性选择的方法。</span><span class="sxs-lookup"><span data-stu-id="89989-296">A production app should include a way for a user to customize their choice of culture.</span></span>

4. <span data-ttu-id="89989-297">在 IE 中设置接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="89989-297">Set the Accept-Language HTTP header in IE</span></span>

5. <span data-ttu-id="89989-298">在齿轮图标中，点击“Internet 选项”。</span><span class="sxs-lookup"><span data-stu-id="89989-298">From the gear icon, tap **Internet Options**.</span></span>

6. <span data-ttu-id="89989-299">点击“语言”。</span><span class="sxs-lookup"><span data-stu-id="89989-299">Tap **Languages**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="89989-300">Internet 选项</span><span class="sxs-lookup"><span data-stu-id="89989-300">Internet Options</span></span>

<span data-ttu-id="89989-301">点击“设置语言首选项”。</span><span class="sxs-lookup"><span data-stu-id="89989-301">Tap **Set Language Preferences**.</span></span> <span data-ttu-id="89989-302">点击“添加语言”。</span><span class="sxs-lookup"><span data-stu-id="89989-302">Tap **Add a language**.</span></span> <span data-ttu-id="89989-303">添加语言。</span><span class="sxs-lookup"><span data-stu-id="89989-303">Add the language.</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="89989-304">点击语言，然后点击“向上移动”。</span><span class="sxs-lookup"><span data-stu-id="89989-304">Tap the language, then tap **Move Up**.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="89989-305">使用自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="89989-305">Use a custom provider</span></span>

<span data-ttu-id="89989-306">假设你想要让客户在数据库中存储其语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-306">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="89989-307">你可以编写一个提供程序来查找用户的这些值。</span><span class="sxs-lookup"><span data-stu-id="89989-307">You could write a provider to look up these values for the user.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="89989-308">下面的代码演示如何添加自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="89989-308">The following code shows how to add a custom provider:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="89989-309">使用 `RequestLocalizationOptions` 添加或删除本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-309">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="89989-310">以编程方式设置区域性</span><span class="sxs-lookup"><span data-stu-id="89989-310">Set the culture programmatically</span></span> <span data-ttu-id="89989-311">[GitHub](https://github.com/aspnet/entropy) 上的示例项目 Localization.StarterWeb 包含设置 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="89989-311">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="89989-312">Views/Shared/_SelectLanguagePartial.cshtml 文件允许你从支持的区域性列表中选择区域性：</span><span class="sxs-lookup"><span data-stu-id="89989-312">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

<span data-ttu-id="89989-313">Views/Shared/_SelectLanguagePartial.cshtml 文件添加到了布局文件的 `footer` 部分，使它将可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="89989-313">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="89989-314">`SetLanguage` 方法可设置区域性 Cookie。</span><span class="sxs-lookup"><span data-stu-id="89989-314">The `SetLanguage` method sets the culture cookie.</span></span>

<span data-ttu-id="89989-315">不能将 _SelectLanguagePartial.cshtml 插入此项目的示例代码。</span><span class="sxs-lookup"><span data-stu-id="89989-315">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="89989-316">[GitHub](https://github.com/aspnet/entropy) 上的 Localization.StarterWeb 项目包含的代码可通过[依赖项注入](dependency-injection.md)容器将 `RequestLocalizationOptions` 流到 Razor 部分。</span><span class="sxs-lookup"><span data-stu-id="89989-316">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span> <span data-ttu-id="89989-317">模型绑定路由数据和查询字符串</span><span class="sxs-lookup"><span data-stu-id="89989-317">Model binding route data and query strings</span></span>

<span data-ttu-id="89989-318">请参阅[模型绑定路由数据和查询字符串的全球化行为](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="89989-318">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

<span data-ttu-id="89989-319">全球化和本地化术语</span><span class="sxs-lookup"><span data-stu-id="89989-319">Globalization and localization terms</span></span> <span data-ttu-id="89989-320">本地化应用的过程还要求基本了解现代软件开发中常用的相关字符集，以及与之相关的问题。</span><span class="sxs-lookup"><span data-stu-id="89989-320">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="89989-321">尽管所有计算机将文本都存储为数字（代码），但不同的系统使用不同的数字存储相同的文本。</span><span class="sxs-lookup"><span data-stu-id="89989-321">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="89989-322">本地化过程是指针对特定区域性/区域设置转换应用的用户界面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="89989-322">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="89989-323">[本地化性](/dotnet/standard/globalization-localization/localizability-review)是一个中间过程，用于验证全球化应用是否准备好进行本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-323">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span> <span data-ttu-id="89989-324">区域性名称的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式为 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是语言代码，`<country/regioncode2>` 是子区域性代码。</span><span class="sxs-lookup"><span data-stu-id="89989-324">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="89989-325">例如，`es-CL` 表示西班牙语（智利），`en-US` 表示英语（美国），而 `en-AU` 表示英语（澳大利亚）。</span><span class="sxs-lookup"><span data-stu-id="89989-325">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span>

<span data-ttu-id="89989-326">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是一个与语言相关的 ISO 639 双小写字母的区域性代码和一个与国家/地区相关的 ISO 3166 双大写字母子区域性代码的组合。</span><span class="sxs-lookup"><span data-stu-id="89989-326">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span>

* <span data-ttu-id="89989-327">请参阅[语言区域性名称](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)。</span><span class="sxs-lookup"><span data-stu-id="89989-327">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>
* <span data-ttu-id="89989-328">国际化常缩写为“I18N”。</span><span class="sxs-lookup"><span data-stu-id="89989-328">Internationalization is often abbreviated to "I18N".</span></span>
* <span data-ttu-id="89989-329">缩写采用第一个和最后一个字母以及它们之间的字母数，因此 18 代表第一个字母“I”和最后一个“N”之间的字母数。</span><span class="sxs-lookup"><span data-stu-id="89989-329">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span>
* <span data-ttu-id="89989-330">这同样适用于全球化 (G11N) 和本地化 (L10N)。</span><span class="sxs-lookup"><span data-stu-id="89989-330">The same applies to Globalization (G11N), and Localization (L10N).</span></span>
* <span data-ttu-id="89989-331">术语：</span><span class="sxs-lookup"><span data-stu-id="89989-331">Terms:</span></span> <span data-ttu-id="89989-332">全球化 (G11N)：使应用支持不同语言和区域的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-332">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="89989-333">本地化 (L10N)：针对给定语言和区域自定义应用的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-333">Localization (L10N): The process of customizing an app for a given language and region.</span></span> <span data-ttu-id="89989-334">国际化 (I18N)：介绍了全球化和本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-334">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="89989-335">区域性：它是一种语言和区域（可选）。</span><span class="sxs-lookup"><span data-stu-id="89989-335">Culture: It's a language and, optionally, a region.</span></span> <span data-ttu-id="89989-336">非特定区域性：具有指定语言但不具有区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-336">Neutral culture: A culture that has a specified language, but not a region.</span></span>
* <span data-ttu-id="89989-337">（例如，“en”，“es”）</span><span class="sxs-lookup"><span data-stu-id="89989-337">(for example "en", "es")</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="89989-338">特定区域性：具有指定语言和区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-338">Specific culture: A culture that has a specified language and region.</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="89989-339">（例如，“en-US”，“en-GB”，“es-CL”）</span><span class="sxs-lookup"><span data-stu-id="89989-339">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="89989-340">父区域性：包含特定区域性的非特定区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-340">Parent culture: The neutral culture that contains a specific culture.</span></span>
* <span data-ttu-id="89989-341">（例如，“en”是“en-US”和“en-GB”的父区域性）</span><span class="sxs-lookup"><span data-stu-id="89989-341">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="89989-342">区域设置：区域设置与区域性相同。</span><span class="sxs-lookup"><span data-stu-id="89989-342">Locale: A locale is the same as a culture.</span></span>
* <span data-ttu-id="89989-343">其他资源</span><span class="sxs-lookup"><span data-stu-id="89989-343">Additional resources</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="89989-344">本文所用的 [Localization.StarterWeb 项目](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="89989-344">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>

[<span data-ttu-id="89989-345">对 .NET 应用程序进行全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="89989-345">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index) [<span data-ttu-id="89989-346">.resx 文件中的资源</span><span class="sxs-lookup"><span data-stu-id="89989-346">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)

[<span data-ttu-id="89989-347">Microsoft 多语言应用工具包</span><span class="sxs-lookup"><span data-stu-id="89989-347">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308) [<span data-ttu-id="89989-348">本地化与泛型</span><span class="sxs-lookup"><span data-stu-id="89989-348">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics) <span data-ttu-id="89989-349">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="89989-349">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="89989-350">多语言网站使网站可以覆盖更广泛的受众。</span><span class="sxs-lookup"><span data-stu-id="89989-350">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="89989-351">ASP.NET Core 提供的服务和中间件可将网站本地化为不同的语言和文化。</span><span class="sxs-lookup"><span data-stu-id="89989-351">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="89989-352">国际化涉及[全球化](/dotnet/api/system.globalization)和[本地化](/dotnet/standard/globalization-localization/localization)。</span><span class="sxs-lookup"><span data-stu-id="89989-352">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span>

1. <span data-ttu-id="89989-353">全球化是设计支持不同区域性的应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-353">Globalization is the process of designing apps that support different cultures.</span></span>
1. <span data-ttu-id="89989-354">全球化添加了对一组有关特定地理区域的已定义语言脚本的输入、显示和输出支持。</span><span class="sxs-lookup"><span data-stu-id="89989-354">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>
1. <span data-ttu-id="89989-355">本地化是将已经针对可本地化性进行处理的全球化应用调整为特定的区域性/区域设置的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-355">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span>

<span data-ttu-id="89989-356">有关详细信息，请参阅本文档邻近末尾的全球化和本地化术语。</span><span class="sxs-lookup"><span data-stu-id="89989-356">For more information see **Globalization and localization terms** near the end of this document.</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="89989-357">应用本地化涉及以下内容：</span><span class="sxs-lookup"><span data-stu-id="89989-357">App localization involves the following:</span></span>

<span data-ttu-id="89989-358">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="89989-358">Make the app's content localizable</span></span> <span data-ttu-id="89989-359">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="89989-359">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="89989-360">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="89989-360">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="89989-361">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="89989-361">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="89989-362">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="89989-362">Make the app's content localizable</span></span> <span data-ttu-id="89989-363">已为 <xref:Microsoft.Extensions.Localization.IStringLocalizer> 和 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> 设置架构，可以为开发本地化应用提高工作效率。</span><span class="sxs-lookup"><span data-stu-id="89989-363"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="89989-364">`IStringLocalizer` 使用 <xref:System.Resources.ResourceManager> 和 <xref:System.Resources.ResourceReader> 在运行时提供特定于区域性的资源。</span><span class="sxs-lookup"><span data-stu-id="89989-364">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="89989-365">接口具有一个索引器和一个用于返回本地化字符串的 `IEnumerable`。</span><span class="sxs-lookup"><span data-stu-id="89989-365">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="89989-366">`IStringLocalizer` 不要求在资源文件中存储默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-366">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="89989-367">你可以开发针对本地化的应用，且无需在开发初期创建资源资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-367">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="89989-368">下面的代码演示如何针对本地化包装字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="89989-368">The code below shows how to wrap the string "About Title" for localization.</span></span> <span data-ttu-id="89989-369">在前面的代码中，`IStringLocalizer<T>` 实现来源于[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="89989-369">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="89989-370">如果找不到“About Title”的本地化值，则返回索引器键，即字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="89989-370">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span>

<span data-ttu-id="89989-371">可将默认语言文本字符串保留在应用中并将它们包装在本地化工具中，以便你可集中精力开发应用。</span><span class="sxs-lookup"><span data-stu-id="89989-371">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="89989-372">你使用默认语言开发应用，并针对本地化步骤进行准备，而无需首先创建默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-372">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="89989-373">或者，你可以使用传统方法，并提供键以检索默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-373">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="89989-374">对于许多开发者而言，不具有默认语言 .resx 文件且简单包装字符串文本的新工作流可以减少本地化应用的开销。</span><span class="sxs-lookup"><span data-stu-id="89989-374">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span>

<span data-ttu-id="89989-375">其他开发者将首选传统工作流，因为它可以更轻松地使用较长字符串文本，更轻松地更新本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-375">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="89989-376">对包含 HTML 的资源使用 `IHtmlLocalizer<T>` 实现。</span><span class="sxs-lookup"><span data-stu-id="89989-376">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span>

<span data-ttu-id="89989-377">`IHtmlLocalizer` 对资源字符串中格式化的参数进行 HTML 编码，但不对资源字符串本身进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="89989-377">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="89989-378">在下面突出显示的示例中，仅 `name` 参数的值被 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="89989-378">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="89989-379">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="89989-379">**Note:** You generally want to only localize text and not HTML.</span></span> <span data-ttu-id="89989-380">最低程度，你可以从[依赖关系注入](dependency-injection.md)获取 `IStringLocalizerFactory`：</span><span class="sxs-lookup"><span data-stu-id="89989-380">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="89989-381">上面的代码演示了这两种工厂创建方法。</span><span class="sxs-lookup"><span data-stu-id="89989-381">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="89989-382">可以按控制器、区域对本地化字符串分区，或只有一个容器。</span><span class="sxs-lookup"><span data-stu-id="89989-382">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="89989-383">在示例应用中，名为 `SharedResource` 的虚拟类用于共享资源。</span><span class="sxs-lookup"><span data-stu-id="89989-383">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span> <span data-ttu-id="89989-384">某些开发者使用 `Startup` 类，以包含全局或共享字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-384">Some developers use the `Startup` class to contain global or shared strings.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="89989-385">在下面的示例中，使用 `InfoController` 和 `SharedResource` 本地化工具：</span><span class="sxs-lookup"><span data-stu-id="89989-385">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span> <span data-ttu-id="89989-386">视图本地化</span><span class="sxs-lookup"><span data-stu-id="89989-386">View localization</span></span> <span data-ttu-id="89989-387">`IViewLocalizer` 服务可为[视图](xref:mvc/views/overview)提供本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-387">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="89989-388">`ViewLocalizer` 类可实现此接口，并从视图文件路径找到资源位置。</span><span class="sxs-lookup"><span data-stu-id="89989-388">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="89989-389">下面的代码演示如何使用 `IViewLocalizer` 的默认实现：</span><span class="sxs-lookup"><span data-stu-id="89989-389">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="89989-390">`IViewLocalizer` 的默认实现可根据视图的文件名查找资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-390">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span>

| <span data-ttu-id="89989-391">没有使用全局共享资源文件的选项。</span><span class="sxs-lookup"><span data-stu-id="89989-391">There's no option to use a global shared resource file.</span></span> | <span data-ttu-id="89989-392">`ViewLocalizer` 使用 `IHtmlLocalizer` 实现本地化工具，因此 Razor 不会对本地化字符串进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="89989-392">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="89989-393">你可以参数化资源字符串，`IViewLocalizer` 将对参数进行 HTML 编码，但不会对资源字符串进行。</span><span class="sxs-lookup"><span data-stu-id="89989-393">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span>

<span data-ttu-id="89989-394">请考虑以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="89989-394">Consider the following Razor markup:</span></span>

<span data-ttu-id="89989-395">法语资源文件可以包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="89989-395">A French resource file could contain the following:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="89989-396">键</span><span class="sxs-lookup"><span data-stu-id="89989-396">Key</span></span>

<span data-ttu-id="89989-397">“值”</span><span class="sxs-lookup"><span data-stu-id="89989-397">Value</span></span> <span data-ttu-id="89989-398">呈现的视图可能包含资源文件中的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="89989-398">The rendered view would contain the HTML markup from the resource file.</span></span>

* <span data-ttu-id="89989-399">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="89989-399">**Note:** You generally want to only localize text and not HTML.</span></span>
* <span data-ttu-id="89989-400">若要在视图中使用共享资源文件，请注入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="89989-400">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="89989-401">DataAnnotations 本地化</span><span class="sxs-lookup"><span data-stu-id="89989-401">DataAnnotations localization</span></span> <span data-ttu-id="89989-402">DataAnnotations 错误消息已使用 `IStringLocalizer<T>` 本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-402">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="89989-403">使用选项 `ResourcesPath = "Resources"`，`RegisterViewModel` 中的错误消息可以存储在以下路径之一：</span><span class="sxs-lookup"><span data-stu-id="89989-403">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

<span data-ttu-id="89989-404">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-404">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="89989-405">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-405">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span> <span data-ttu-id="89989-406">在 ASP.NET Core MVC 1.1.0 和更高版本中，非验证属性已经进行了本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-406">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="89989-407">ASP.NET Core MVC 1.0 不会为非验证属性查找本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-407">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="89989-408">对多个类使用一个资源字符串</span><span class="sxs-lookup"><span data-stu-id="89989-408">Using one resource string for multiple classes</span></span>

<span data-ttu-id="89989-409">下面的代码演示如何针对具有多个类的验证属性使用一个资源字符串：</span><span class="sxs-lookup"><span data-stu-id="89989-409">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span> <span data-ttu-id="89989-410">在上面的代码中，`SharedResource` 是对应于存储验证消息的 resx 的类。</span><span class="sxs-lookup"><span data-stu-id="89989-410">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="89989-411">使用此方法，DataAnnotations 将仅使用 `SharedResource`，而不是每个类的资源。</span><span class="sxs-lookup"><span data-stu-id="89989-411">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span> <span data-ttu-id="89989-412">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="89989-412">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="89989-413">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="89989-413">SupportedCultures and SupportedUICultures</span></span> <span data-ttu-id="89989-414">ASP.NET Core 允许指定两个区域性值，`SupportedCultures` 和 `SupportedUICultures`。</span><span class="sxs-lookup"><span data-stu-id="89989-414">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="89989-415">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 对象可决定区域性相关函数的结果，如日期、时间、数字和货币格式等。</span><span class="sxs-lookup"><span data-stu-id="89989-415">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="89989-416">`SupportedCultures` 确定文本的排序顺序、大小写约定和字符串比较。</span><span class="sxs-lookup"><span data-stu-id="89989-416">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="89989-417">请参阅 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) 详细了解服务器如何获取区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-417">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span>

## <a name="resource-files"></a><span data-ttu-id="89989-418">`SupportedUICultures` 可确定按 [ResourceManager](/dotnet/api/system.resources.resourcemanager) 来查找哪些转换字符串（位于 .resx 文件）。</span><span class="sxs-lookup"><span data-stu-id="89989-418">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span>

<span data-ttu-id="89989-419">`ResourceManager` 只需查找 `CurrentUICulture` 决定的区域性特定字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-419">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="89989-420">.NET 中的每个线程都具有 `CurrentCulture` 和 `CurrentUICulture` 对象。</span><span class="sxs-lookup"><span data-stu-id="89989-420">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="89989-421">呈现区域性相关函数时，ASP.NET Core 可检查这些值。</span><span class="sxs-lookup"><span data-stu-id="89989-421">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="89989-422">例如，如果当前线程的区域性设置为“en-US”（英语，美国），`DateTime.Now.ToLongDateString()` 将显示“Thursday, February 18, 2016”，但如果 `CurrentCulture` 设置为“es-ES”（西班牙语，西班牙），则输出将为“jueves，18 de febrero de 2016”。</span><span class="sxs-lookup"><span data-stu-id="89989-422">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span> <span data-ttu-id="89989-423">资源文件</span><span class="sxs-lookup"><span data-stu-id="89989-423">Resource files</span></span>

1. <span data-ttu-id="89989-424">资源文件是将可本地化的字符串与代码分离的有用机制。</span><span class="sxs-lookup"><span data-stu-id="89989-424">A resource file is a useful mechanism for separating localizable strings from code.</span></span>

    ![非默认语言的转换字符串在 .resx 资源文件中单独显示。](localization/_static/newi.png)

2. <span data-ttu-id="89989-427">“es”是西班牙语的语言代码。</span><span class="sxs-lookup"><span data-stu-id="89989-427">"es" is the language code for Spanish.</span></span>

    ![要在 Visual Studio 中创建此资源文件，请支持以下操作：](localization/_static/res.png)

3. <span data-ttu-id="89989-429">在“解决方案资源管理器”中，右键单击将包含资源文件的文件夹 >“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="89989-429">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![嵌套的上下文菜单：在“解决方案资源管理器”中，“资源”可打开上下文菜单。](localization/_static/hola.png)

    <span data-ttu-id="89989-431">“添加”可打开第二个上下文菜单，突出显示“新项”命令。</span><span class="sxs-lookup"><span data-stu-id="89989-431">A second contextual menu is open for Add showing the New Item command highlighted.</span></span>

    ![在“搜索已安装的模板”框中，输入“资源”并命名该文件。](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="89989-433">“添加新项”对话框</span><span class="sxs-lookup"><span data-stu-id="89989-433">Add New Item dialog</span></span>

<span data-ttu-id="89989-434">在“名称”列中输入键值（本机字符串），在“值”列中输入转换字符串 。</span><span class="sxs-lookup"><span data-stu-id="89989-434">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span> <span data-ttu-id="89989-435">Welcome.es.resx 文件（西班牙语版 Welcome 资源文件）的单词 Hello 位于“名称”列，Hola（西班牙语版 Hello）位于“值”列</span><span class="sxs-lookup"><span data-stu-id="89989-435">Welcome.es.resx file (the Welcome resource file for Spanish) with the word Hello in the Name column and the word Hola (Hello in Spanish) in the Value column</span></span> <span data-ttu-id="89989-436">Visual Studio 将显示 Welcome.es.resx 文件。</span><span class="sxs-lookup"><span data-stu-id="89989-436">Visual Studio shows the *Welcome.es.resx* file.</span></span> <span data-ttu-id="89989-437">显示 Welcome Spanish (es) 资源文件的解决方案资源管理器</span><span class="sxs-lookup"><span data-stu-id="89989-437">Solution Explorer showing the Welcome Spanish (es) resource file</span></span> <span data-ttu-id="89989-438">资源文件命名</span><span class="sxs-lookup"><span data-stu-id="89989-438">Resource file naming</span></span>

<span data-ttu-id="89989-439">资源名称是类的完整类型名称减去程序集名称。</span><span class="sxs-lookup"><span data-stu-id="89989-439">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="89989-440">例如，类 `LocalizationWebsite.Web.Startup` 的主要程序集为 `LocalizationWebsite.Web.dll` 的项目中的法语资源将命名为 Startup.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-440">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="89989-441">类 `LocalizationWebsite.Web.Controllers.HomeController` 的资源将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-441">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="89989-442">如果目标类的命名空间与将需要完整类型名称的程序集名称不同。</span><span class="sxs-lookup"><span data-stu-id="89989-442">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="89989-443">例如，在示例项目中，类型 `ExtraNamespace.Tools` 的资源将命名为 ExtraNamespace.Tools.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-443">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span> <span data-ttu-id="89989-444">在示例项目中，`ConfigureServices` 方法将 `ResourcesPath` 设置为“资源”，因此主控制器的法语资源文件的项目相对路径是 Resources/Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-444">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span>

| <span data-ttu-id="89989-445">或者，你可以使用文件夹组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-445">Alternatively, you can use folders to organize resource files.</span></span> | <span data-ttu-id="89989-446">对于主控制器，该路径将为 Resources/Controllers/HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-446">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="89989-447">如果不使用 `ResourcesPath` 选项，.resx 文件将转到项目的基目录中。</span><span class="sxs-lookup"><span data-stu-id="89989-447">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> | <span data-ttu-id="89989-448">`HomeController` 的资源文件将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-448">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span>  |
| <span data-ttu-id="89989-449">是选择使用圆点还是路径命名约定，具体取决于你想如何组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-449">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>  | <span data-ttu-id="89989-450">资源名称</span><span class="sxs-lookup"><span data-stu-id="89989-450">Resource name</span></span> |
|    |     |

<span data-ttu-id="89989-451">圆点或路径命名</span><span class="sxs-lookup"><span data-stu-id="89989-451">Dot or path naming</span></span> <span data-ttu-id="89989-452">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-452">Resources/Controllers.HomeController.fr.resx</span></span> <span data-ttu-id="89989-453">圆点</span><span class="sxs-lookup"><span data-stu-id="89989-453">Dot</span></span> <span data-ttu-id="89989-454">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-454">Resources/Controllers/HomeController.fr.resx</span></span>

* <span data-ttu-id="89989-455">路径</span><span class="sxs-lookup"><span data-stu-id="89989-455">Path</span></span>

* <span data-ttu-id="89989-456">Razor 视图中使用 `@inject IViewLocalizer` 的资源文件遵循类似的模式。</span><span class="sxs-lookup"><span data-stu-id="89989-456">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span>

<span data-ttu-id="89989-457">可以使用圆点命名或路径命名约定对视图的资源文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="89989-457">The resource file for a view can be named using either dot naming or path naming.</span></span>

### <a name="rootnamespaceattribute"></a>Razor<span data-ttu-id="89989-458"> 视图资源文件可模拟其关联视图文件的路径。</span><span class="sxs-lookup"><span data-stu-id="89989-458"> view resource files mimic the path of their associated view file.</span></span> 

<span data-ttu-id="89989-459">假设我们将 `ResourcesPath` 设置为“Resources”，与 *Views/Home/About.cshtml* 视图关联的法语资源文件可能是下面其中之一 ：</span><span class="sxs-lookup"><span data-stu-id="89989-459">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span> 

> [!WARNING]
> <span data-ttu-id="89989-460">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-460">Resources/Views/Home/About.fr.resx</span></span> <span data-ttu-id="89989-461">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-461">Resources/Views.Home.About.fr.resx</span></span> 

<span data-ttu-id="89989-462">如果不使用 `ResourcesPath` 选项，视图的 .resx 文件将位于视图所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="89989-462">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

* <span data-ttu-id="89989-463">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="89989-463">RootNamespaceAttribute</span></span>
* <span data-ttu-id="89989-464">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性在程序集的根命名空间不同于程序集名称时，提供程序集的根命名空间。</span><span class="sxs-lookup"><span data-stu-id="89989-464">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> <span data-ttu-id="89989-465">当项目名称不是有效的 .NET 标识符时，可能会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="89989-465">This can occur when a project's name is not a valid .NET identifier.</span></span> 

<span data-ttu-id="89989-466">例如，`my-project-name.csproj` 将使用根命名空间 `my_project_name` 和导致此错误的程序集名称 `my-project-name`。</span><span class="sxs-lookup"><span data-stu-id="89989-466">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="89989-467">如果程序集的根命名空间不同于程序集名称：</span><span class="sxs-lookup"><span data-stu-id="89989-467">If the root namespace of an assembly is different than the assembly name:</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="89989-468">默认情况下无法进行本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-468">Localization does not work by default.</span></span>

<span data-ttu-id="89989-469">因程序集内搜索资源的方式导致本地化失败。</span><span class="sxs-lookup"><span data-stu-id="89989-469">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="89989-470">`RootNamespace` 是生成时间值，不可用于正在执行的进程。</span><span class="sxs-lookup"><span data-stu-id="89989-470">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> <span data-ttu-id="89989-471">如果 `RootNamespace` 不同于 `AssemblyName`，请在 AssemblyInfo.cs 中包括以下内容（参数值替换为实际值）：</span><span class="sxs-lookup"><span data-stu-id="89989-471">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span> <span data-ttu-id="89989-472">上述代码可成功解析 resx 文件。</span><span class="sxs-lookup"><span data-stu-id="89989-472">The preceding code enables the successful resolution of resx files.</span></span> <span data-ttu-id="89989-473">区域性回退行为</span><span class="sxs-lookup"><span data-stu-id="89989-473">Culture fallback behavior</span></span> <span data-ttu-id="89989-474">在搜索资源时，本地化会进行“区域性回退”。</span><span class="sxs-lookup"><span data-stu-id="89989-474">When searching for a resource, localization engages in "culture fallback".</span></span>

<span data-ttu-id="89989-475">从所请求的区域性开始，如果未能找到，则还原至该区域性的父区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-475">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="89989-476">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 属性代表父区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-476">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span>

* <span data-ttu-id="89989-477">这通常（但并不是总是）意味着从 ISO 中移除区域签名。</span><span class="sxs-lookup"><span data-stu-id="89989-477">This usually (but not always) means removing the national signifier from the ISO.</span></span>
* <span data-ttu-id="89989-478">例如，墨西哥的西班牙语方言为“es-MX”。</span><span class="sxs-lookup"><span data-stu-id="89989-478">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span>
* <span data-ttu-id="89989-479">它具备一个父级“es”西班牙，没有特别指定国家/地区。</span><span class="sxs-lookup"><span data-stu-id="89989-479">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="89989-480">假设你的站点接收到了一个区域性为“fr-CA”的“Welcome”资源的请求。</span><span class="sxs-lookup"><span data-stu-id="89989-480">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="89989-481">本地化系统按顺序查找以下资源，并选择第一个匹配项：</span><span class="sxs-lookup"><span data-stu-id="89989-481">The localization system looks for the following resources, in order, and selects the first match:</span></span> <span data-ttu-id="89989-482">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-482">*Welcome.fr-CA.resx*</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="89989-483">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-483">*Welcome.fr.resx*</span></span>

<span data-ttu-id="89989-484">*Welcome.resx*（如果 `NeutralResourcesLanguage` 为“fr-CA”）</span><span class="sxs-lookup"><span data-stu-id="89989-484">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span> <span data-ttu-id="89989-485">例如，如果删除了“.fr”区域性指示符，而且已将区域性设置为“法语”，将读取默认资源文件，并本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-485">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="89989-486">对于不满足所请求区域性的情况，资源管理器可指定默认资源或回退资源。</span><span class="sxs-lookup"><span data-stu-id="89989-486">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="89989-487">缺少适用于请求区域性的资源时，如果只想返回键，不得具有默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-487">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span> <span data-ttu-id="89989-488">使用 Visual Studio 生成资源文件</span><span class="sxs-lookup"><span data-stu-id="89989-488">Generate resource files with Visual Studio</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="89989-489">如果在 Visual Studio 中创建文件名没有区域性的资源文件（例如 Welcome.resx），Visual Studio 将创建一个 C# 类，并且具有每个字符串的属性。</span><span class="sxs-lookup"><span data-stu-id="89989-489">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span>

<span data-ttu-id="89989-490">这通常不是你想在 ASP.NET Core 中使用的。</span><span class="sxs-lookup"><span data-stu-id="89989-490">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="89989-491">你通常没有默认的 .resx 资源文件（没有区域性名称的 .resx 文件） 。</span><span class="sxs-lookup"><span data-stu-id="89989-491">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="89989-492">建议创建具有区域性名称（例如 Welcome.fr.resx）的 .resx 文件 。</span><span class="sxs-lookup"><span data-stu-id="89989-492">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="89989-493">创建具有区域性名称的 .resx 文件时，Visual Studio 不会生成类文件。</span><span class="sxs-lookup"><span data-stu-id="89989-493">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="configure-localization"></a><span data-ttu-id="89989-494">添加其他区域性</span><span class="sxs-lookup"><span data-stu-id="89989-494">Add other cultures</span></span>

<span data-ttu-id="89989-495">每个语言和区域性组合（除默认语言外）都需要唯一资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-495">Each language and culture combination (other than the default language) requires a unique resource file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="89989-496">通过新建 ISO 语言代码属于名称一部分的资源文件，为不同的区域性和区域设置创建资源文件（例如，en-us、fr-ca 和 en-gb）  。</span><span class="sxs-lookup"><span data-stu-id="89989-496">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="89989-497">这些 ISO 编码位于文件名和 .resx 文件扩展之间，如 Welcome.es-MX.resx（西班牙语/墨西哥） 。</span><span class="sxs-lookup"><span data-stu-id="89989-497">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

* <span data-ttu-id="89989-498">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="89989-498">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="89989-499">配置本地化</span><span class="sxs-lookup"><span data-stu-id="89989-499">Configure localization</span></span> <span data-ttu-id="89989-500">通过 `Startup.ConfigureServices` 方法配置本地化：</span><span class="sxs-lookup"><span data-stu-id="89989-500">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

* <span data-ttu-id="89989-501">`AddLocalization` 将本地化服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="89989-501">`AddLocalization` Adds the localization services to the services container.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="89989-502">上面的代码还将资源路径设置为“Resources”。</span><span class="sxs-lookup"><span data-stu-id="89989-502">The code above also sets the resources path to "Resources".</span></span>

<span data-ttu-id="89989-503">`AddViewLocalization` 添加对本地化视图文件的支持。</span><span class="sxs-lookup"><span data-stu-id="89989-503">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="89989-504">在此示例视图中，本地化基于视图文件后缀。</span><span class="sxs-lookup"><span data-stu-id="89989-504">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="89989-505">例如，Index.fr.cshtml 文件中的“fr”。</span><span class="sxs-lookup"><span data-stu-id="89989-505">For example "fr" in the *Index.fr.cshtml* file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="89989-506">`AddDataAnnotationsLocalization` 添加通过 `IStringLocalizer` 抽象对本地化 `DataAnnotations` 验证消息的支持。</span><span class="sxs-lookup"><span data-stu-id="89989-506">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span> <span data-ttu-id="89989-507">本地化中间件</span><span class="sxs-lookup"><span data-stu-id="89989-507">Localization middleware</span></span> <span data-ttu-id="89989-508">在本地化[中间件](xref:fundamentals/middleware/index)中设置有关请求的当前区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-508">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="89989-509">在 `Startup.Configure` 方法中启用本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="89989-509">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="89989-510">必须在中间件前面配置本地化中间件，它可能检查请求区域性（例如，`app.UseMvcWithDefaultRoute()`）。</span><span class="sxs-lookup"><span data-stu-id="89989-510">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span> <span data-ttu-id="89989-511">`UseRequestLocalization` 初始化 `RequestLocalizationOptions` 对象。</span><span class="sxs-lookup"><span data-stu-id="89989-511">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="89989-512">在每个请求上，枚举了 `RequestLocalizationOptions` 的 `RequestCultureProvider` 列表，使用了可成功决定请求区域性的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-512">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span>

<span data-ttu-id="89989-513">默认提供程序来自 `RequestLocalizationOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="89989-513">The default providers come from the `RequestLocalizationOptions` class:</span></span> <span data-ttu-id="89989-514">默认列表从最具体到最不具体排序。</span><span class="sxs-lookup"><span data-stu-id="89989-514">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="89989-515">在本文的后面部分，我们将了解如何更改顺序，甚至添加一个自定义区域性提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-515">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="89989-516">如果没有一个提供程序可以确定请求区域性，则使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="89989-516">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span> <span data-ttu-id="89989-517">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="89989-517">QueryStringRequestCultureProvider</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="89989-518">某些应用将使用查询字符串来设置[区域性和 UI 区域性](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)。</span><span class="sxs-lookup"><span data-stu-id="89989-518">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="89989-519">对于使用 Cookie 或接受语言标题方法的应用，向 URL 添加查询字符串有助于调试和测试代码。</span><span class="sxs-lookup"><span data-stu-id="89989-519">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="89989-520">默认情况下，`QueryStringRequestCultureProvider` 注册为 `RequestCultureProvider` 列表中的第一个本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-520">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span>

<span data-ttu-id="89989-521">传递查询字符串参数 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="89989-521">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="89989-522">下面的示例将特定区域性（语言和区域）设置为“西班牙语/墨西哥”：</span><span class="sxs-lookup"><span data-stu-id="89989-522">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

<span data-ttu-id="89989-523">如果仅传入两种区域性之一（`culture` 或 `ui-culture`，查询字符串提供程序将使用你传入的区域性设置这两个值。</span><span class="sxs-lookup"><span data-stu-id="89989-523">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="89989-524">例如，仅设置区域性将同时设置 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="89989-524">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

<span data-ttu-id="89989-525">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="89989-525">CookieRequestCultureProvider</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="89989-526">通常，生产应用将提供一种机制来使用 ASP.NET Core 区域性 Cookie 设置区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-526">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="89989-527">若要创建 Cookie，请使用 `MakeCookieValue` 方法。</span><span class="sxs-lookup"><span data-stu-id="89989-527">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="89989-528">`CookieRequestCultureProvider``DefaultCookieName` 将返回用来跟踪用户首选区域性信息的默认 Cookie 名称。</span><span class="sxs-lookup"><span data-stu-id="89989-528">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="89989-529">默认 Cookie 名称是 `.AspNetCore.Culture`。</span><span class="sxs-lookup"><span data-stu-id="89989-529">The default cookie name is `.AspNetCore.Culture`.</span></span> <span data-ttu-id="89989-530">Cookie 格式为 `c=%LANGCODE%|uic=%LANGCODE%`，其中`c` 是 `Culture`，`uic` 是 `UICulture`，例如：</span><span class="sxs-lookup"><span data-stu-id="89989-530">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span> <span data-ttu-id="89989-531">如果仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将同时用于区域性信息和 UI 区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-531">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="89989-532">接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="89989-532">The Accept-Language HTTP header</span></span>

1. <span data-ttu-id="89989-533">[接受语言标题](https://www.w3.org/International/questions/qa-accept-lang-locales)在大多数浏览器中可设置，最初用于指定用户的语言。</span><span class="sxs-lookup"><span data-stu-id="89989-533">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span>

2. <span data-ttu-id="89989-534">此设置指示浏览器已设置为发送或已从基础操作系统继承的内容。</span><span class="sxs-lookup"><span data-stu-id="89989-534">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span>

    ![浏览器请求的接受语言 HTTP 标题不是检测用户首选语言的可靠方法（请参阅 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)（在浏览器中设置首选项）。](localization/_static/lang.png)

3. <span data-ttu-id="89989-536">生产应用应包括一种用户可以自定义区域性选择的方法。</span><span class="sxs-lookup"><span data-stu-id="89989-536">A production app should include a way for a user to customize their choice of culture.</span></span>

4. <span data-ttu-id="89989-537">在 IE 中设置接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="89989-537">Set the Accept-Language HTTP header in IE</span></span>

5. <span data-ttu-id="89989-538">在齿轮图标中，点击“Internet 选项”。</span><span class="sxs-lookup"><span data-stu-id="89989-538">From the gear icon, tap **Internet Options**.</span></span>

6. <span data-ttu-id="89989-539">点击“语言”。</span><span class="sxs-lookup"><span data-stu-id="89989-539">Tap **Languages**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="89989-540">Internet 选项</span><span class="sxs-lookup"><span data-stu-id="89989-540">Internet Options</span></span>

<span data-ttu-id="89989-541">点击“设置语言首选项”。</span><span class="sxs-lookup"><span data-stu-id="89989-541">Tap **Set Language Preferences**.</span></span> <span data-ttu-id="89989-542">点击“添加语言”。</span><span class="sxs-lookup"><span data-stu-id="89989-542">Tap **Add a language**.</span></span> <span data-ttu-id="89989-543">添加语言。</span><span class="sxs-lookup"><span data-stu-id="89989-543">Add the language.</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.RequestCultureProviders.Insert(0, new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="89989-544">点击语言，然后点击“向上移动”。</span><span class="sxs-lookup"><span data-stu-id="89989-544">Tap the language, then tap **Move Up**.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="89989-545">使用自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="89989-545">Use a custom provider</span></span>

<span data-ttu-id="89989-546">假设你想要让客户在数据库中存储其语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-546">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="89989-547">你可以编写一个提供程序来查找用户的这些值。</span><span class="sxs-lookup"><span data-stu-id="89989-547">You could write a provider to look up these values for the user.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="89989-548">下面的代码演示如何添加自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="89989-548">The following code shows how to add a custom provider:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="89989-549">使用 `RequestLocalizationOptions` 添加或删除本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-549">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="89989-550">以编程方式设置区域性</span><span class="sxs-lookup"><span data-stu-id="89989-550">Set the culture programmatically</span></span> <span data-ttu-id="89989-551">[GitHub](https://github.com/aspnet/entropy) 上的示例项目 Localization.StarterWeb 包含设置 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="89989-551">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="89989-552">Views/Shared/_SelectLanguagePartial.cshtml 文件允许你从支持的区域性列表中选择区域性：</span><span class="sxs-lookup"><span data-stu-id="89989-552">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

<span data-ttu-id="89989-553">Views/Shared/_SelectLanguagePartial.cshtml 文件添加到了布局文件的 `footer` 部分，使它将可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="89989-553">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="89989-554">`SetLanguage` 方法可设置区域性 Cookie。</span><span class="sxs-lookup"><span data-stu-id="89989-554">The `SetLanguage` method sets the culture cookie.</span></span>

<span data-ttu-id="89989-555">不能将 _SelectLanguagePartial.cshtml 插入此项目的示例代码。</span><span class="sxs-lookup"><span data-stu-id="89989-555">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="89989-556">[GitHub](https://github.com/aspnet/entropy) 上的 Localization.StarterWeb 项目包含的代码可通过[依赖项注入](dependency-injection.md)容器将 `RequestLocalizationOptions` 流到 Razor 部分。</span><span class="sxs-lookup"><span data-stu-id="89989-556">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span> <span data-ttu-id="89989-557">模型绑定路由数据和查询字符串</span><span class="sxs-lookup"><span data-stu-id="89989-557">Model binding route data and query strings</span></span>

<span data-ttu-id="89989-558">请参阅[模型绑定路由数据和查询字符串的全球化行为](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="89989-558">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

<span data-ttu-id="89989-559">全球化和本地化术语</span><span class="sxs-lookup"><span data-stu-id="89989-559">Globalization and localization terms</span></span> <span data-ttu-id="89989-560">本地化应用的过程还要求基本了解现代软件开发中常用的相关字符集，以及与之相关的问题。</span><span class="sxs-lookup"><span data-stu-id="89989-560">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="89989-561">尽管所有计算机将文本都存储为数字（代码），但不同的系统使用不同的数字存储相同的文本。</span><span class="sxs-lookup"><span data-stu-id="89989-561">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="89989-562">本地化过程是指针对特定区域性/区域设置转换应用的用户界面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="89989-562">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="89989-563">[本地化性](/dotnet/standard/globalization-localization/localizability-review)是一个中间过程，用于验证全球化应用是否准备好进行本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-563">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span> <span data-ttu-id="89989-564">区域性名称的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式为 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是语言代码，`<country/regioncode2>` 是子区域性代码。</span><span class="sxs-lookup"><span data-stu-id="89989-564">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="89989-565">例如，`es-CL` 表示西班牙语（智利），`en-US` 表示英语（美国），而 `en-AU` 表示英语（澳大利亚）。</span><span class="sxs-lookup"><span data-stu-id="89989-565">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span>

<span data-ttu-id="89989-566">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是一个与语言相关的 ISO 639 双小写字母的区域性代码和一个与国家/地区相关的 ISO 3166 双大写字母子区域性代码的组合。</span><span class="sxs-lookup"><span data-stu-id="89989-566">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span>

* <span data-ttu-id="89989-567">请参阅[语言区域性名称](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)。</span><span class="sxs-lookup"><span data-stu-id="89989-567">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>
* <span data-ttu-id="89989-568">国际化常缩写为“I18N”。</span><span class="sxs-lookup"><span data-stu-id="89989-568">Internationalization is often abbreviated to "I18N".</span></span>
* <span data-ttu-id="89989-569">缩写采用第一个和最后一个字母以及它们之间的字母数，因此 18 代表第一个字母“I”和最后一个“N”之间的字母数。</span><span class="sxs-lookup"><span data-stu-id="89989-569">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span>
* <span data-ttu-id="89989-570">这同样适用于全球化 (G11N) 和本地化 (L10N)。</span><span class="sxs-lookup"><span data-stu-id="89989-570">The same applies to Globalization (G11N), and Localization (L10N).</span></span>
* <span data-ttu-id="89989-571">术语：</span><span class="sxs-lookup"><span data-stu-id="89989-571">Terms:</span></span> <span data-ttu-id="89989-572">全球化 (G11N)：使应用支持不同语言和区域的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-572">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="89989-573">本地化 (L10N)：针对给定语言和区域自定义应用的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-573">Localization (L10N): The process of customizing an app for a given language and region.</span></span> <span data-ttu-id="89989-574">国际化 (I18N)：介绍了全球化和本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-574">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="89989-575">区域性：它是一种语言和区域（可选）。</span><span class="sxs-lookup"><span data-stu-id="89989-575">Culture: It's a language and, optionally, a region.</span></span> <span data-ttu-id="89989-576">非特定区域性：具有指定语言但不具有区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-576">Neutral culture: A culture that has a specified language, but not a region.</span></span>
* <span data-ttu-id="89989-577">（例如，“en”，“es”）</span><span class="sxs-lookup"><span data-stu-id="89989-577">(for example "en", "es")</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="89989-578">特定区域性：具有指定语言和区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-578">Specific culture: A culture that has a specified language and region.</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="89989-579">（例如，“en-US”，“en-GB”，“es-CL”）</span><span class="sxs-lookup"><span data-stu-id="89989-579">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="89989-580">父区域性：包含特定区域性的非特定区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-580">Parent culture: The neutral culture that contains a specific culture.</span></span>
* <span data-ttu-id="89989-581">（例如，“en”是“en-US”和“en-GB”的父区域性）</span><span class="sxs-lookup"><span data-stu-id="89989-581">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="89989-582">区域设置：区域设置与区域性相同。</span><span class="sxs-lookup"><span data-stu-id="89989-582">Locale: A locale is the same as a culture.</span></span>
* <span data-ttu-id="89989-583">其他资源</span><span class="sxs-lookup"><span data-stu-id="89989-583">Additional resources</span></span>

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="89989-584">本文所用的 [Localization.StarterWeb 项目](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="89989-584">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>

[<span data-ttu-id="89989-585">对 .NET 应用程序进行全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="89989-585">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index) [<span data-ttu-id="89989-586">.resx 文件中的资源</span><span class="sxs-lookup"><span data-stu-id="89989-586">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)

[<span data-ttu-id="89989-587">Microsoft 多语言应用工具包</span><span class="sxs-lookup"><span data-stu-id="89989-587">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308) [<span data-ttu-id="89989-588">本地化与泛型</span><span class="sxs-lookup"><span data-stu-id="89989-588">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics) <span data-ttu-id="89989-589">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="89989-589">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="89989-590">多语言网站使网站可以覆盖更广泛的受众。</span><span class="sxs-lookup"><span data-stu-id="89989-590">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="89989-591">ASP.NET Core 提供的服务和中间件可将网站本地化为不同的语言和文化。</span><span class="sxs-lookup"><span data-stu-id="89989-591">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="89989-592">国际化涉及[全球化](/dotnet/api/system.globalization)和[本地化](/dotnet/standard/globalization-localization/localization)。</span><span class="sxs-lookup"><span data-stu-id="89989-592">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span>

1. <span data-ttu-id="89989-593">全球化是设计支持不同区域性的应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-593">Globalization is the process of designing apps that support different cultures.</span></span>
1. <span data-ttu-id="89989-594">全球化添加了对一组有关特定地理区域的已定义语言脚本的输入、显示和输出支持。</span><span class="sxs-lookup"><span data-stu-id="89989-594">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>
1. <span data-ttu-id="89989-595">本地化是将已经针对可本地化性进行处理的全球化应用调整为特定的区域性/区域设置的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-595">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span>

<span data-ttu-id="89989-596">有关详细信息，请参阅本文档邻近末尾的全球化和本地化术语。</span><span class="sxs-lookup"><span data-stu-id="89989-596">For more information see **Globalization and localization terms** near the end of this document.</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="89989-597">应用本地化涉及以下内容：</span><span class="sxs-lookup"><span data-stu-id="89989-597">App localization involves the following:</span></span>

<span data-ttu-id="89989-598">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="89989-598">Make the app's content localizable</span></span> <span data-ttu-id="89989-599">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="89989-599">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="89989-600">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="89989-600">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="89989-601">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="89989-601">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="89989-602">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="89989-602">Make the app's content localizable</span></span> <span data-ttu-id="89989-603">已为 <xref:Microsoft.Extensions.Localization.IStringLocalizer> 和 <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> 设置架构，可以为开发本地化应用提高工作效率。</span><span class="sxs-lookup"><span data-stu-id="89989-603"><xref:Microsoft.Extensions.Localization.IStringLocalizer> and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="89989-604">`IStringLocalizer` 使用 <xref:System.Resources.ResourceManager> 和 <xref:System.Resources.ResourceReader> 在运行时提供特定于区域性的资源。</span><span class="sxs-lookup"><span data-stu-id="89989-604">`IStringLocalizer` uses the <xref:System.Resources.ResourceManager> and <xref:System.Resources.ResourceReader> to provide culture-specific resources at run time.</span></span> <span data-ttu-id="89989-605">接口具有一个索引器和一个用于返回本地化字符串的 `IEnumerable`。</span><span class="sxs-lookup"><span data-stu-id="89989-605">The interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="89989-606">`IStringLocalizer` 不要求在资源文件中存储默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-606">`IStringLocalizer` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="89989-607">你可以开发针对本地化的应用，且无需在开发初期创建资源资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-607">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="89989-608">下面的代码演示如何针对本地化包装字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="89989-608">The code below shows how to wrap the string "About Title" for localization.</span></span> <span data-ttu-id="89989-609">在前面的代码中，`IStringLocalizer<T>` 实现来源于[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="89989-609">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="89989-610">如果找不到“About Title”的本地化值，则返回索引器键，即字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="89989-610">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span>

<span data-ttu-id="89989-611">可将默认语言文本字符串保留在应用中并将它们包装在本地化工具中，以便你可集中精力开发应用。</span><span class="sxs-lookup"><span data-stu-id="89989-611">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="89989-612">你使用默认语言开发应用，并针对本地化步骤进行准备，而无需首先创建默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-612">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="89989-613">或者，你可以使用传统方法，并提供键以检索默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-613">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span>

[!code-csharp[](~/fundamentals/localization/sample/3.x/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="89989-614">对于许多开发者而言，不具有默认语言 .resx 文件且简单包装字符串文本的新工作流可以减少本地化应用的开销。</span><span class="sxs-lookup"><span data-stu-id="89989-614">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span>

<span data-ttu-id="89989-615">其他开发者将首选传统工作流，因为它可以更轻松地使用较长字符串文本，更轻松地更新本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-615">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="89989-616">对包含 HTML 的资源使用 `IHtmlLocalizer<T>` 实现。</span><span class="sxs-lookup"><span data-stu-id="89989-616">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span>

<span data-ttu-id="89989-617">`IHtmlLocalizer` 对资源字符串中格式化的参数进行 HTML 编码，但不对资源字符串本身进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="89989-617">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="89989-618">在下面突出显示的示例中，仅 `name` 参数的值被 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="89989-618">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="89989-619">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="89989-619">**Note:** You generally want to only localize text and not HTML.</span></span> <span data-ttu-id="89989-620">最低程度，你可以从[依赖关系注入](dependency-injection.md)获取 `IStringLocalizerFactory`：</span><span class="sxs-lookup"><span data-stu-id="89989-620">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="89989-621">上面的代码演示了这两种工厂创建方法。</span><span class="sxs-lookup"><span data-stu-id="89989-621">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="89989-622">可以按控制器、区域对本地化字符串分区，或只有一个容器。</span><span class="sxs-lookup"><span data-stu-id="89989-622">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="89989-623">在示例应用中，名为 `SharedResource` 的虚拟类用于共享资源。</span><span class="sxs-lookup"><span data-stu-id="89989-623">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span> <span data-ttu-id="89989-624">某些开发者使用 `Startup` 类，以包含全局或共享字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-624">Some developers use the `Startup` class to contain global or shared strings.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="89989-625">在下面的示例中，使用 `InfoController` 和 `SharedResource` 本地化工具：</span><span class="sxs-lookup"><span data-stu-id="89989-625">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span> <span data-ttu-id="89989-626">视图本地化</span><span class="sxs-lookup"><span data-stu-id="89989-626">View localization</span></span> <span data-ttu-id="89989-627">`IViewLocalizer` 服务可为[视图](xref:mvc/views/overview)提供本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-627">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="89989-628">`ViewLocalizer` 类可实现此接口，并从视图文件路径找到资源位置。</span><span class="sxs-lookup"><span data-stu-id="89989-628">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="89989-629">下面的代码演示如何使用 `IViewLocalizer` 的默认实现：</span><span class="sxs-lookup"><span data-stu-id="89989-629">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="89989-630">`IViewLocalizer` 的默认实现可根据视图的文件名查找资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-630">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span>

| <span data-ttu-id="89989-631">没有使用全局共享资源文件的选项。</span><span class="sxs-lookup"><span data-stu-id="89989-631">There's no option to use a global shared resource file.</span></span> | <span data-ttu-id="89989-632">`ViewLocalizer` 使用 `IHtmlLocalizer` 实现本地化工具，因此 Razor 不会对本地化字符串进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="89989-632">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="89989-633">你可以参数化资源字符串，`IViewLocalizer` 将对参数进行 HTML 编码，但不会对资源字符串进行。</span><span class="sxs-lookup"><span data-stu-id="89989-633">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span>

<span data-ttu-id="89989-634">请考虑以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="89989-634">Consider the following Razor markup:</span></span>

<span data-ttu-id="89989-635">法语资源文件可以包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="89989-635">A French resource file could contain the following:</span></span>

[!code-cshtml[](~/fundamentals/localization/sample/3.x/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="89989-636">键</span><span class="sxs-lookup"><span data-stu-id="89989-636">Key</span></span>

<span data-ttu-id="89989-637">“值”</span><span class="sxs-lookup"><span data-stu-id="89989-637">Value</span></span> <span data-ttu-id="89989-638">呈现的视图可能包含资源文件中的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="89989-638">The rendered view would contain the HTML markup from the resource file.</span></span>

* <span data-ttu-id="89989-639">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="89989-639">**Note:** You generally want to only localize text and not HTML.</span></span>
* <span data-ttu-id="89989-640">若要在视图中使用共享资源文件，请注入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="89989-640">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-csharp[](localization/sample/3.x/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="89989-641">DataAnnotations 本地化</span><span class="sxs-lookup"><span data-stu-id="89989-641">DataAnnotations localization</span></span> <span data-ttu-id="89989-642">DataAnnotations 错误消息已使用 `IStringLocalizer<T>` 本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-642">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="89989-643">使用选项 `ResourcesPath = "Resources"`，`RegisterViewModel` 中的错误消息可以存储在以下路径之一：</span><span class="sxs-lookup"><span data-stu-id="89989-643">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

<span data-ttu-id="89989-644">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-644">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc()
        .AddDataAnnotationsLocalization(options => {
            options.DataAnnotationLocalizerProvider = (type, factory) =>
                factory.Create(typeof(SharedResource));
        });
}
```

<span data-ttu-id="89989-645">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-645">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span> <span data-ttu-id="89989-646">在 ASP.NET Core MVC 1.1.0 和更高版本中，非验证属性已经进行了本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-646">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="89989-647">ASP.NET Core MVC 1.0 不会为非验证属性查找本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-647">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="89989-648">对多个类使用一个资源字符串</span><span class="sxs-lookup"><span data-stu-id="89989-648">Using one resource string for multiple classes</span></span>

<span data-ttu-id="89989-649">下面的代码演示如何针对具有多个类的验证属性使用一个资源字符串：</span><span class="sxs-lookup"><span data-stu-id="89989-649">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span> <span data-ttu-id="89989-650">在上面的代码中，`SharedResource` 是对应于存储验证消息的 resx 的类。</span><span class="sxs-lookup"><span data-stu-id="89989-650">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="89989-651">使用此方法，DataAnnotations 将仅使用 `SharedResource`，而不是每个类的资源。</span><span class="sxs-lookup"><span data-stu-id="89989-651">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span> <span data-ttu-id="89989-652">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="89989-652">Provide localized resources for the languages and cultures you support</span></span> <span data-ttu-id="89989-653">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="89989-653">SupportedCultures and SupportedUICultures</span></span> <span data-ttu-id="89989-654">ASP.NET Core 允许指定两个区域性值，`SupportedCultures` 和 `SupportedUICultures`。</span><span class="sxs-lookup"><span data-stu-id="89989-654">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="89989-655">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 对象可决定区域性相关函数的结果，如日期、时间、数字和货币格式等。</span><span class="sxs-lookup"><span data-stu-id="89989-655">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="89989-656">`SupportedCultures` 确定文本的排序顺序、大小写约定和字符串比较。</span><span class="sxs-lookup"><span data-stu-id="89989-656">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="89989-657">请参阅 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) 详细了解服务器如何获取区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-657">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span>

## <a name="resource-files"></a><span data-ttu-id="89989-658">`SupportedUICultures` 可确定按 [ResourceManager](/dotnet/api/system.resources.resourcemanager) 来查找哪些转换字符串（位于 .resx 文件）。</span><span class="sxs-lookup"><span data-stu-id="89989-658">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span>

<span data-ttu-id="89989-659">`ResourceManager` 只需查找 `CurrentUICulture` 决定的区域性特定字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-659">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="89989-660">.NET 中的每个线程都具有 `CurrentCulture` 和 `CurrentUICulture` 对象。</span><span class="sxs-lookup"><span data-stu-id="89989-660">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="89989-661">呈现区域性相关函数时，ASP.NET Core 可检查这些值。</span><span class="sxs-lookup"><span data-stu-id="89989-661">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="89989-662">例如，如果当前线程的区域性设置为“en-US”（英语，美国），`DateTime.Now.ToLongDateString()` 将显示“Thursday, February 18, 2016”，但如果 `CurrentCulture` 设置为“es-ES”（西班牙语，西班牙），则输出将为“jueves，18 de febrero de 2016”。</span><span class="sxs-lookup"><span data-stu-id="89989-662">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span> <span data-ttu-id="89989-663">资源文件</span><span class="sxs-lookup"><span data-stu-id="89989-663">Resource files</span></span>

1. <span data-ttu-id="89989-664">资源文件是将可本地化的字符串与代码分离的有用机制。</span><span class="sxs-lookup"><span data-stu-id="89989-664">A resource file is a useful mechanism for separating localizable strings from code.</span></span>

    ![非默认语言的转换字符串在 .resx 资源文件中单独显示。](localization/_static/newi.png)

2. <span data-ttu-id="89989-667">“es”是西班牙语的语言代码。</span><span class="sxs-lookup"><span data-stu-id="89989-667">"es" is the language code for Spanish.</span></span>

    ![要在 Visual Studio 中创建此资源文件，请支持以下操作：](localization/_static/res.png)

3. <span data-ttu-id="89989-669">在“解决方案资源管理器”中，右键单击将包含资源文件的文件夹 >“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="89989-669">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![嵌套的上下文菜单：在“解决方案资源管理器”中，“资源”可打开上下文菜单。](localization/_static/hola.png)

    <span data-ttu-id="89989-671">“添加”可打开第二个上下文菜单，突出显示“新项”命令。</span><span class="sxs-lookup"><span data-stu-id="89989-671">A second contextual menu is open for Add showing the New Item command highlighted.</span></span>

    ![在“搜索已安装的模板”框中，输入“资源”并命名该文件。](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="89989-673">“添加新项”对话框</span><span class="sxs-lookup"><span data-stu-id="89989-673">Add New Item dialog</span></span>

<span data-ttu-id="89989-674">在“名称”列中输入键值（本机字符串），在“值”列中输入转换字符串 。</span><span class="sxs-lookup"><span data-stu-id="89989-674">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span> <span data-ttu-id="89989-675">Welcome.es.resx 文件（西班牙语版 Welcome 资源文件）的单词 Hello 位于“名称”列，Hola（西班牙语版 Hello）位于“值”列</span><span class="sxs-lookup"><span data-stu-id="89989-675">Welcome.es.resx file (the Welcome resource file for Spanish) with the word Hello in the Name column and the word Hola (Hello in Spanish) in the Value column</span></span> <span data-ttu-id="89989-676">Visual Studio 将显示 Welcome.es.resx 文件。</span><span class="sxs-lookup"><span data-stu-id="89989-676">Visual Studio shows the *Welcome.es.resx* file.</span></span> <span data-ttu-id="89989-677">显示 Welcome Spanish (es) 资源文件的解决方案资源管理器</span><span class="sxs-lookup"><span data-stu-id="89989-677">Solution Explorer showing the Welcome Spanish (es) resource file</span></span> <span data-ttu-id="89989-678">资源文件命名</span><span class="sxs-lookup"><span data-stu-id="89989-678">Resource file naming</span></span>

<span data-ttu-id="89989-679">资源名称是类的完整类型名称减去程序集名称。</span><span class="sxs-lookup"><span data-stu-id="89989-679">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="89989-680">例如，类 `LocalizationWebsite.Web.Startup` 的主要程序集为 `LocalizationWebsite.Web.dll` 的项目中的法语资源将命名为 Startup.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-680">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="89989-681">类 `LocalizationWebsite.Web.Controllers.HomeController` 的资源将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-681">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="89989-682">如果目标类的命名空间与将需要完整类型名称的程序集名称不同。</span><span class="sxs-lookup"><span data-stu-id="89989-682">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="89989-683">例如，在示例项目中，类型 `ExtraNamespace.Tools` 的资源将命名为 ExtraNamespace.Tools.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-683">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span> <span data-ttu-id="89989-684">在示例项目中，`ConfigureServices` 方法将 `ResourcesPath` 设置为“资源”，因此主控制器的法语资源文件的项目相对路径是 Resources/Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-684">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span>

| <span data-ttu-id="89989-685">或者，你可以使用文件夹组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-685">Alternatively, you can use folders to organize resource files.</span></span> | <span data-ttu-id="89989-686">对于主控制器，该路径将为 Resources/Controllers/HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-686">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="89989-687">如果不使用 `ResourcesPath` 选项，.resx 文件将转到项目的基目录中。</span><span class="sxs-lookup"><span data-stu-id="89989-687">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> | <span data-ttu-id="89989-688">`HomeController` 的资源文件将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="89989-688">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span>  |
| <span data-ttu-id="89989-689">是选择使用圆点还是路径命名约定，具体取决于你想如何组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-689">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>  | <span data-ttu-id="89989-690">资源名称</span><span class="sxs-lookup"><span data-stu-id="89989-690">Resource name</span></span> |
|    |     |

<span data-ttu-id="89989-691">圆点或路径命名</span><span class="sxs-lookup"><span data-stu-id="89989-691">Dot or path naming</span></span> <span data-ttu-id="89989-692">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-692">Resources/Controllers.HomeController.fr.resx</span></span> <span data-ttu-id="89989-693">圆点</span><span class="sxs-lookup"><span data-stu-id="89989-693">Dot</span></span> <span data-ttu-id="89989-694">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-694">Resources/Controllers/HomeController.fr.resx</span></span>

* <span data-ttu-id="89989-695">路径</span><span class="sxs-lookup"><span data-stu-id="89989-695">Path</span></span>

* <span data-ttu-id="89989-696">Razor 视图中使用 `@inject IViewLocalizer` 的资源文件遵循类似的模式。</span><span class="sxs-lookup"><span data-stu-id="89989-696">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span>

<span data-ttu-id="89989-697">可以使用圆点命名或路径命名约定对视图的资源文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="89989-697">The resource file for a view can be named using either dot naming or path naming.</span></span>

### <a name="rootnamespaceattribute"></a>Razor<span data-ttu-id="89989-698"> 视图资源文件可模拟其关联视图文件的路径。</span><span class="sxs-lookup"><span data-stu-id="89989-698"> view resource files mimic the path of their associated view file.</span></span> 

<span data-ttu-id="89989-699">假设我们将 `ResourcesPath` 设置为“Resources”，与 *Views/Home/About.cshtml* 视图关联的法语资源文件可能是下面其中之一 ：</span><span class="sxs-lookup"><span data-stu-id="89989-699">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span> 

> [!WARNING]
> <span data-ttu-id="89989-700">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-700">Resources/Views/Home/About.fr.resx</span></span> <span data-ttu-id="89989-701">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="89989-701">Resources/Views.Home.About.fr.resx</span></span> 

<span data-ttu-id="89989-702">如果不使用 `ResourcesPath` 选项，视图的 .resx 文件将位于视图所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="89989-702">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

* <span data-ttu-id="89989-703">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="89989-703">RootNamespaceAttribute</span></span>
* <span data-ttu-id="89989-704">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性在程序集的根命名空间不同于程序集名称时，提供程序集的根命名空间。</span><span class="sxs-lookup"><span data-stu-id="89989-704">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> <span data-ttu-id="89989-705">当项目名称不是有效的 .NET 标识符时，可能会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="89989-705">This can occur when a project's name is not a valid .NET identifier.</span></span> 

<span data-ttu-id="89989-706">例如，`my-project-name.csproj` 将使用根命名空间 `my_project_name` 和导致此错误的程序集名称 `my-project-name`。</span><span class="sxs-lookup"><span data-stu-id="89989-706">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="89989-707">如果程序集的根命名空间不同于程序集名称：</span><span class="sxs-lookup"><span data-stu-id="89989-707">If the root namespace of an assembly is different than the assembly name:</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="89989-708">默认情况下无法进行本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-708">Localization does not work by default.</span></span>

<span data-ttu-id="89989-709">因程序集内搜索资源的方式导致本地化失败。</span><span class="sxs-lookup"><span data-stu-id="89989-709">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="89989-710">`RootNamespace` 是生成时间值，不可用于正在执行的进程。</span><span class="sxs-lookup"><span data-stu-id="89989-710">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> <span data-ttu-id="89989-711">如果 `RootNamespace` 不同于 `AssemblyName`，请在 AssemblyInfo.cs 中包括以下内容（参数值替换为实际值）：</span><span class="sxs-lookup"><span data-stu-id="89989-711">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span> <span data-ttu-id="89989-712">上述代码可成功解析 resx 文件。</span><span class="sxs-lookup"><span data-stu-id="89989-712">The preceding code enables the successful resolution of resx files.</span></span> <span data-ttu-id="89989-713">区域性回退行为</span><span class="sxs-lookup"><span data-stu-id="89989-713">Culture fallback behavior</span></span> <span data-ttu-id="89989-714">在搜索资源时，本地化会进行“区域性回退”。</span><span class="sxs-lookup"><span data-stu-id="89989-714">When searching for a resource, localization engages in "culture fallback".</span></span>

<span data-ttu-id="89989-715">从所请求的区域性开始，如果未能找到，则还原至该区域性的父区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-715">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="89989-716">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 属性代表父区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-716">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span>

* <span data-ttu-id="89989-717">这通常（但并不是总是）意味着从 ISO 中移除区域签名。</span><span class="sxs-lookup"><span data-stu-id="89989-717">This usually (but not always) means removing the national signifier from the ISO.</span></span>
* <span data-ttu-id="89989-718">例如，墨西哥的西班牙语方言为“es-MX”。</span><span class="sxs-lookup"><span data-stu-id="89989-718">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span>
* <span data-ttu-id="89989-719">它具备一个父级“es”西班牙，没有特别指定国家/地区。</span><span class="sxs-lookup"><span data-stu-id="89989-719">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="89989-720">假设你的站点接收到了一个区域性为“fr-CA”的“Welcome”资源的请求。</span><span class="sxs-lookup"><span data-stu-id="89989-720">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="89989-721">本地化系统按顺序查找以下资源，并选择第一个匹配项：</span><span class="sxs-lookup"><span data-stu-id="89989-721">The localization system looks for the following resources, in order, and selects the first match:</span></span> <span data-ttu-id="89989-722">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-722">*Welcome.fr-CA.resx*</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="89989-723">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="89989-723">*Welcome.fr.resx*</span></span>

<span data-ttu-id="89989-724">*Welcome.resx*（如果 `NeutralResourcesLanguage` 为“fr-CA”）</span><span class="sxs-lookup"><span data-stu-id="89989-724">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span> <span data-ttu-id="89989-725">例如，如果删除了“.fr”区域性指示符，而且已将区域性设置为“法语”，将读取默认资源文件，并本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="89989-725">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="89989-726">对于不满足所请求区域性的情况，资源管理器可指定默认资源或回退资源。</span><span class="sxs-lookup"><span data-stu-id="89989-726">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="89989-727">缺少适用于请求区域性的资源时，如果只想返回键，不得具有默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-727">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span> <span data-ttu-id="89989-728">使用 Visual Studio 生成资源文件</span><span class="sxs-lookup"><span data-stu-id="89989-728">Generate resource files with Visual Studio</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="89989-729">如果在 Visual Studio 中创建文件名没有区域性的资源文件（例如 Welcome.resx），Visual Studio 将创建一个 C# 类，并且具有每个字符串的属性。</span><span class="sxs-lookup"><span data-stu-id="89989-729">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span>

<span data-ttu-id="89989-730">这通常不是你想在 ASP.NET Core 中使用的。</span><span class="sxs-lookup"><span data-stu-id="89989-730">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="89989-731">你通常没有默认的 .resx 资源文件（没有区域性名称的 .resx 文件） 。</span><span class="sxs-lookup"><span data-stu-id="89989-731">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="89989-732">建议创建具有区域性名称（例如 Welcome.fr.resx）的 .resx 文件 。</span><span class="sxs-lookup"><span data-stu-id="89989-732">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="89989-733">创建具有区域性名称的 .resx 文件时，Visual Studio 不会生成类文件。</span><span class="sxs-lookup"><span data-stu-id="89989-733">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="configure-localization"></a><span data-ttu-id="89989-734">添加其他区域性</span><span class="sxs-lookup"><span data-stu-id="89989-734">Add other cultures</span></span>

<span data-ttu-id="89989-735">每个语言和区域性组合（除默认语言外）都需要唯一资源文件。</span><span class="sxs-lookup"><span data-stu-id="89989-735">Each language and culture combination (other than the default language) requires a unique resource file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="89989-736">通过新建 ISO 语言代码属于名称一部分的资源文件，为不同的区域性和区域设置创建资源文件（例如，en-us、fr-ca 和 en-gb）  。</span><span class="sxs-lookup"><span data-stu-id="89989-736">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="89989-737">这些 ISO 编码位于文件名和 .resx 文件扩展之间，如 Welcome.es-MX.resx（西班牙语/墨西哥） 。</span><span class="sxs-lookup"><span data-stu-id="89989-737">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

* <span data-ttu-id="89989-738">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="89989-738">Implement a strategy to select the language/culture for each request</span></span> <span data-ttu-id="89989-739">配置本地化</span><span class="sxs-lookup"><span data-stu-id="89989-739">Configure localization</span></span> <span data-ttu-id="89989-740">通过 `Startup.ConfigureServices` 方法配置本地化：</span><span class="sxs-lookup"><span data-stu-id="89989-740">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

* <span data-ttu-id="89989-741">`AddLocalization` 将本地化服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="89989-741">`AddLocalization` Adds the localization services to the services container.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="89989-742">上面的代码还将资源路径设置为“Resources”。</span><span class="sxs-lookup"><span data-stu-id="89989-742">The code above also sets the resources path to "Resources".</span></span>

<span data-ttu-id="89989-743">`AddViewLocalization` 添加对本地化视图文件的支持。</span><span class="sxs-lookup"><span data-stu-id="89989-743">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="89989-744">在此示例视图中，本地化基于视图文件后缀。</span><span class="sxs-lookup"><span data-stu-id="89989-744">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="89989-745">例如，Index.fr.cshtml 文件中的“fr”。</span><span class="sxs-lookup"><span data-stu-id="89989-745">For example "fr" in the *Index.fr.cshtml* file.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="89989-746">`AddDataAnnotationsLocalization` 添加通过 `IStringLocalizer` 抽象对本地化 `DataAnnotations` 验证消息的支持。</span><span class="sxs-lookup"><span data-stu-id="89989-746">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span> <span data-ttu-id="89989-747">本地化中间件</span><span class="sxs-lookup"><span data-stu-id="89989-747">Localization middleware</span></span> <span data-ttu-id="89989-748">在本地化[中间件](xref:fundamentals/middleware/index)中设置有关请求的当前区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-748">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="89989-749">在 `Startup.Configure` 方法中启用本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="89989-749">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="89989-750">必须在中间件前面配置本地化中间件，它可能检查请求区域性（例如，`app.UseMvcWithDefaultRoute()`）。</span><span class="sxs-lookup"><span data-stu-id="89989-750">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span> <span data-ttu-id="89989-751">`UseRequestLocalization` 初始化 `RequestLocalizationOptions` 对象。</span><span class="sxs-lookup"><span data-stu-id="89989-751">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="89989-752">在每个请求上，枚举了 `RequestLocalizationOptions` 的 `RequestCultureProvider` 列表，使用了可成功决定请求区域性的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-752">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span>

<span data-ttu-id="89989-753">默认提供程序来自 `RequestLocalizationOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="89989-753">The default providers come from the `RequestLocalizationOptions` class:</span></span> <span data-ttu-id="89989-754">默认列表从最具体到最不具体排序。</span><span class="sxs-lookup"><span data-stu-id="89989-754">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="89989-755">在本文的后面部分，我们将了解如何更改顺序，甚至添加一个自定义区域性提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-755">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="89989-756">如果没有一个提供程序可以确定请求区域性，则使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="89989-756">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span> <span data-ttu-id="89989-757">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="89989-757">QueryStringRequestCultureProvider</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="89989-758">某些应用将使用查询字符串来设置[区域性和 UI 区域性](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)。</span><span class="sxs-lookup"><span data-stu-id="89989-758">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="89989-759">对于使用 Cookie 或接受语言标题方法的应用，向 URL 添加查询字符串有助于调试和测试代码。</span><span class="sxs-lookup"><span data-stu-id="89989-759">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="89989-760">默认情况下，`QueryStringRequestCultureProvider` 注册为 `RequestCultureProvider` 列表中的第一个本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-760">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span>

<span data-ttu-id="89989-761">传递查询字符串参数 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="89989-761">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="89989-762">下面的示例将特定区域性（语言和区域）设置为“西班牙语/墨西哥”：</span><span class="sxs-lookup"><span data-stu-id="89989-762">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

<span data-ttu-id="89989-763">如果仅传入两种区域性之一（`culture` 或 `ui-culture`，查询字符串提供程序将使用你传入的区域性设置这两个值。</span><span class="sxs-lookup"><span data-stu-id="89989-763">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="89989-764">例如，仅设置区域性将同时设置 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="89989-764">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

<span data-ttu-id="89989-765">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="89989-765">CookieRequestCultureProvider</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="89989-766">通常，生产应用将提供一种机制来使用 ASP.NET Core 区域性 Cookie 设置区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-766">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="89989-767">若要创建 Cookie，请使用 `MakeCookieValue` 方法。</span><span class="sxs-lookup"><span data-stu-id="89989-767">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="89989-768">`CookieRequestCultureProvider``DefaultCookieName` 将返回用来跟踪用户首选区域性信息的默认 Cookie 名称。</span><span class="sxs-lookup"><span data-stu-id="89989-768">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="89989-769">默认 Cookie 名称是 `.AspNetCore.Culture`。</span><span class="sxs-lookup"><span data-stu-id="89989-769">The default cookie name is `.AspNetCore.Culture`.</span></span> <span data-ttu-id="89989-770">Cookie 格式为 `c=%LANGCODE%|uic=%LANGCODE%`，其中`c` 是 `Culture`，`uic` 是 `UICulture`，例如：</span><span class="sxs-lookup"><span data-stu-id="89989-770">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span> <span data-ttu-id="89989-771">如果仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将同时用于区域性信息和 UI 区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-771">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="89989-772">接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="89989-772">The Accept-Language HTTP header</span></span>

1. <span data-ttu-id="89989-773">[接受语言标题](https://www.w3.org/International/questions/qa-accept-lang-locales)在大多数浏览器中可设置，最初用于指定用户的语言。</span><span class="sxs-lookup"><span data-stu-id="89989-773">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span>

2. <span data-ttu-id="89989-774">此设置指示浏览器已设置为发送或已从基础操作系统继承的内容。</span><span class="sxs-lookup"><span data-stu-id="89989-774">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span>

    ![浏览器请求的接受语言 HTTP 标题不是检测用户首选语言的可靠方法（请参阅 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)（在浏览器中设置首选项）。](localization/_static/lang.png)

3. <span data-ttu-id="89989-776">生产应用应包括一种用户可以自定义区域性选择的方法。</span><span class="sxs-lookup"><span data-stu-id="89989-776">A production app should include a way for a user to customize their choice of culture.</span></span>

4. <span data-ttu-id="89989-777">在 IE 中设置接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="89989-777">Set the Accept-Language HTTP header in IE</span></span>

5. <span data-ttu-id="89989-778">在齿轮图标中，点击“Internet 选项”。</span><span class="sxs-lookup"><span data-stu-id="89989-778">From the gear icon, tap **Internet Options**.</span></span>

6. <span data-ttu-id="89989-779">点击“语言”。</span><span class="sxs-lookup"><span data-stu-id="89989-779">Tap **Languages**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="89989-780">Internet 选项</span><span class="sxs-lookup"><span data-stu-id="89989-780">Internet Options</span></span>

<span data-ttu-id="89989-781">点击“设置语言首选项”。</span><span class="sxs-lookup"><span data-stu-id="89989-781">Tap **Set Language Preferences**.</span></span>

 - <span data-ttu-id="89989-782">点击“添加语言”。</span><span class="sxs-lookup"><span data-stu-id="89989-782">Tap **Add a language**.</span></span>
 - <span data-ttu-id="89989-783">添加语言。</span><span class="sxs-lookup"><span data-stu-id="89989-783">Add the language.</span></span>

<span data-ttu-id="89989-784">点击语言，然后点击“向上移动”。</span><span class="sxs-lookup"><span data-stu-id="89989-784">Tap the language, then tap **Move Up**.</span></span>

<span data-ttu-id="89989-785">Content-Language HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="89989-785">The Content-Language HTTP header</span></span>

<span data-ttu-id="89989-786">[Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) 实体标头：</span><span class="sxs-lookup"><span data-stu-id="89989-786">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="89989-787">用于描述面向受众的语言。</span><span class="sxs-lookup"><span data-stu-id="89989-787">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="89989-788">允许用户根据用户的首选语言来区分。</span><span class="sxs-lookup"><span data-stu-id="89989-788">Allows a user to differentiate according to the users' own preferred language.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="89989-789">实体标头用于 HTTP 请求和响应。</span><span class="sxs-lookup"><span data-stu-id="89989-789">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="89989-790">可通过设置属性 `ApplyCurrentCultureToResponseHeaders` 来添加 `Content-Language` 标头。</span><span class="sxs-lookup"><span data-stu-id="89989-790">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span> <span data-ttu-id="89989-791">添加 `Content-Language` 标头：</span><span class="sxs-lookup"><span data-stu-id="89989-791">Adding the `Content-Language` header:</span></span> <span data-ttu-id="89989-792">允许 RequestLocalizationMiddleware 使用 `CurrentUICulture` 设置 `Content-Language` 标头。</span><span class="sxs-lookup"><span data-stu-id="89989-792">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>

```csharp
private const string enUSCulture = "en-US";

services.Configure<RequestLocalizationOptions>(options =>
{
    var supportedCultures = new[]
    {
        new CultureInfo(enUSCulture),
        new CultureInfo("fr")
    };

    options.DefaultRequestCulture = new RequestCulture(culture: enUSCulture, uiCulture: enUSCulture);
    options.SupportedCultures = supportedCultures;
    options.SupportedUICultures = supportedCultures;

    options.AddInitialRequestCultureProvider(new CustomRequestCultureProvider(async context =>
    {
        // My custom request culture logic
        return new ProviderCultureResult("en");
    }));
});
```

<span data-ttu-id="89989-793">无需显式设置响应标头 `Content-Language`。</span><span class="sxs-lookup"><span data-stu-id="89989-793">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="89989-794">使用自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="89989-794">Use a custom provider</span></span>

<span data-ttu-id="89989-795">假设你想要让客户在数据库中存储其语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-795">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="89989-796">你可以编写一个提供程序来查找用户的这些值。</span><span class="sxs-lookup"><span data-stu-id="89989-796">You could write a provider to look up these values for the user.</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="89989-797">下面的代码演示如何添加自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="89989-797">The following code shows how to add a custom provider:</span></span>

[!code-cshtml[](localization/sample/3.x/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="89989-798">使用 `RequestLocalizationOptions` 添加或删除本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="89989-798">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

[!code-csharp[](localization/sample/3.x/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="89989-799">以编程方式设置区域性</span><span class="sxs-lookup"><span data-stu-id="89989-799">Set the culture programmatically</span></span> <span data-ttu-id="89989-800">[GitHub](https://github.com/aspnet/entropy) 上的示例项目 Localization.StarterWeb 包含设置 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="89989-800">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="89989-801">Views/Shared/_SelectLanguagePartial.cshtml 文件允许你从支持的区域性列表中选择区域性：</span><span class="sxs-lookup"><span data-stu-id="89989-801">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

<span data-ttu-id="89989-802">Views/Shared/_SelectLanguagePartial.cshtml 文件添加到了布局文件的 `footer` 部分，使它将可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="89989-802">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="89989-803">`SetLanguage` 方法可设置区域性 Cookie。</span><span class="sxs-lookup"><span data-stu-id="89989-803">The `SetLanguage` method sets the culture cookie.</span></span>

<span data-ttu-id="89989-804">不能将 _SelectLanguagePartial.cshtml 插入此项目的示例代码。</span><span class="sxs-lookup"><span data-stu-id="89989-804">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="89989-805">[GitHub](https://github.com/aspnet/entropy) 上的 Localization.StarterWeb 项目包含的代码可通过[依赖项注入](dependency-injection.md)容器将 `RequestLocalizationOptions` 流到 Razor 部分。</span><span class="sxs-lookup"><span data-stu-id="89989-805">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span> <span data-ttu-id="89989-806">模型绑定路由数据和查询字符串</span><span class="sxs-lookup"><span data-stu-id="89989-806">Model binding route data and query strings</span></span>

<span data-ttu-id="89989-807">请参阅[模型绑定路由数据和查询字符串的全球化行为](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="89989-807">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

<span data-ttu-id="89989-808">全球化和本地化术语</span><span class="sxs-lookup"><span data-stu-id="89989-808">Globalization and localization terms</span></span> <span data-ttu-id="89989-809">本地化应用的过程还要求基本了解现代软件开发中常用的相关字符集，以及与之相关的问题。</span><span class="sxs-lookup"><span data-stu-id="89989-809">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="89989-810">尽管所有计算机将文本都存储为数字（代码），但不同的系统使用不同的数字存储相同的文本。</span><span class="sxs-lookup"><span data-stu-id="89989-810">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="89989-811">本地化过程是指针对特定区域性/区域设置转换应用的用户界面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="89989-811">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="89989-812">[本地化性](/dotnet/standard/globalization-localization/localizability-review)是一个中间过程，用于验证全球化应用是否准备好进行本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-812">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span> <span data-ttu-id="89989-813">区域性名称的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式为 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是语言代码，`<country/regioncode2>` 是子区域性代码。</span><span class="sxs-lookup"><span data-stu-id="89989-813">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="89989-814">例如，`es-CL` 表示西班牙语（智利），`en-US` 表示英语（美国），而 `en-AU` 表示英语（澳大利亚）。</span><span class="sxs-lookup"><span data-stu-id="89989-814">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span>

<span data-ttu-id="89989-815">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是一个与语言相关的 ISO 639 双小写字母的区域性代码和一个与国家/地区相关的 ISO 3166 双大写字母子区域性代码的组合。</span><span class="sxs-lookup"><span data-stu-id="89989-815">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span>

* <span data-ttu-id="89989-816">请参阅[语言区域性名称](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)。</span><span class="sxs-lookup"><span data-stu-id="89989-816">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>
* <span data-ttu-id="89989-817">国际化常缩写为“I18N”。</span><span class="sxs-lookup"><span data-stu-id="89989-817">Internationalization is often abbreviated to "I18N".</span></span>
* <span data-ttu-id="89989-818">缩写采用第一个和最后一个字母以及它们之间的字母数，因此 18 代表第一个字母“I”和最后一个“N”之间的字母数。</span><span class="sxs-lookup"><span data-stu-id="89989-818">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span>
* <span data-ttu-id="89989-819">这同样适用于全球化 (G11N) 和本地化 (L10N)。</span><span class="sxs-lookup"><span data-stu-id="89989-819">The same applies to Globalization (G11N), and Localization (L10N).</span></span>
* <span data-ttu-id="89989-820">术语：</span><span class="sxs-lookup"><span data-stu-id="89989-820">Terms:</span></span> <span data-ttu-id="89989-821">全球化 (G11N)：使应用支持不同语言和区域的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-821">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="89989-822">本地化 (L10N)：针对给定语言和区域自定义应用的过程。</span><span class="sxs-lookup"><span data-stu-id="89989-822">Localization (L10N): The process of customizing an app for a given language and region.</span></span> <span data-ttu-id="89989-823">国际化 (I18N)：介绍了全球化和本地化。</span><span class="sxs-lookup"><span data-stu-id="89989-823">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="89989-824">区域性：它是一种语言和区域（可选）。</span><span class="sxs-lookup"><span data-stu-id="89989-824">Culture: It's a language and, optionally, a region.</span></span> <span data-ttu-id="89989-825">非特定区域性：具有指定语言但不具有区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-825">Neutral culture: A culture that has a specified language, but not a region.</span></span>
* <span data-ttu-id="89989-826">（例如，“en”，“es”）</span><span class="sxs-lookup"><span data-stu-id="89989-826">(for example "en", "es")</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="89989-827">特定区域性：具有指定语言和区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-827">Specific culture: A culture that has a specified language and region.</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="89989-828">（例如，“en-US”，“en-GB”，“es-CL”）</span><span class="sxs-lookup"><span data-stu-id="89989-828">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="89989-829">父区域性：包含特定区域性的非特定区域性。</span><span class="sxs-lookup"><span data-stu-id="89989-829">Parent culture: The neutral culture that contains a specific culture.</span></span>
* <span data-ttu-id="89989-830">（例如，“en”是“en-US”和“en-GB”的父区域性）</span><span class="sxs-lookup"><span data-stu-id="89989-830">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="89989-831">区域设置：区域设置与区域性相同。</span><span class="sxs-lookup"><span data-stu-id="89989-831">Locale: A locale is the same as a culture.</span></span>
* <span data-ttu-id="89989-832">其他资源</span><span class="sxs-lookup"><span data-stu-id="89989-832">Additional resources</span></span>

::: moniker-end
