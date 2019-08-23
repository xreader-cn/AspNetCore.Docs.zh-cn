---
title: ASP.NET Core 全球化和本地化
author: rick-anderson
description: 了解 ASP.NET Core 如何提供服务和中间件，将内容本地化为不同的语言和区域性。
ms.author: riande
ms.date: 01/14/2017
uid: fundamentals/localization
ms.openlocfilehash: ec78d35daf6823779fca491aca7b7b309db4b02e
ms.sourcegitcommit: e7e04a45195d4e0527af6f7cf1807defb56dc3c3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/06/2019
ms.locfileid: "66750039"
---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="40a51-103">ASP.NET Core 全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="40a51-103">Globalization and localization in ASP.NET Core</span></span>

<span data-ttu-id="40a51-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="40a51-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="40a51-105">使用 ASP.NET Core 创建多语言网站，可让网站拥有更多受众。</span><span class="sxs-lookup"><span data-stu-id="40a51-105">Creating a multilingual website with ASP.NET Core will allow your site to reach a wider audience.</span></span> <span data-ttu-id="40a51-106">ASP.NET Core 提供的服务和中间件可将网站本地化为不同的语言和文化。</span><span class="sxs-lookup"><span data-stu-id="40a51-106">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="40a51-107">国际化涉及[全球化](/dotnet/api/system.globalization)和[本地化](/dotnet/standard/globalization-localization/localization)。</span><span class="sxs-lookup"><span data-stu-id="40a51-107">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="40a51-108">全球化是设计支持不同区域性的应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="40a51-108">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="40a51-109">全球化添加了对一组有关特定地理区域的已定义语言脚本的输入、显示和输出支持。</span><span class="sxs-lookup"><span data-stu-id="40a51-109">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="40a51-110">本地化是将已经针对可本地化性进行处理的全球化应用调整为特定的区域性/区域设置的过程。</span><span class="sxs-lookup"><span data-stu-id="40a51-110">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="40a51-111">有关详细信息，请参阅本文档邻近末尾的全球化和本地化术语  。</span><span class="sxs-lookup"><span data-stu-id="40a51-111">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="40a51-112">应用本地化涉及以下内容：</span><span class="sxs-lookup"><span data-stu-id="40a51-112">App localization involves the following:</span></span>

1. <span data-ttu-id="40a51-113">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="40a51-113">Make the app's content localizable</span></span>

2. <span data-ttu-id="40a51-114">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="40a51-114">Provide localized resources for the languages and cultures you support</span></span>

3. <span data-ttu-id="40a51-115">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="40a51-115">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="40a51-116">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="40a51-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="40a51-117">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="40a51-117">Make the app's content localizable</span></span>

<span data-ttu-id="40a51-118">ASP.NET Core 中引入并架构了 `IStringLocalizer` 和 `IStringLocalizer<T>`，以提高开发本地化应用的工作效率。</span><span class="sxs-lookup"><span data-stu-id="40a51-118">Introduced in ASP.NET Core, `IStringLocalizer` and `IStringLocalizer<T>` were architected to improve productivity when developing localized apps.</span></span> <span data-ttu-id="40a51-119">`IStringLocalizer` 使用 [ResourceManager](/dotnet/api/system.resources.resourcemanager) 和 [ResourceReader](/dotnet/api/system.resources.resourcereader)，在运行时提供区域性特定资源。</span><span class="sxs-lookup"><span data-stu-id="40a51-119">`IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time.</span></span> <span data-ttu-id="40a51-120">简单接口具有一个索引器和一个用于返回本地化字符串的 `IEnumerable`。</span><span class="sxs-lookup"><span data-stu-id="40a51-120">The simple interface has an indexer and an `IEnumerable` for returning localized strings.</span></span> <span data-ttu-id="40a51-121">`IStringLocalizer` 不要求在资源文件中存储默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="40a51-121">`IStringLocalizer` doesn't require you to store the default language strings in a resource file.</span></span> <span data-ttu-id="40a51-122">你可以开发针对本地化的应用，且无需在开发初期创建资源资源文件。</span><span class="sxs-lookup"><span data-stu-id="40a51-122">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="40a51-123">下面的代码演示如何针对本地化包装字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="40a51-123">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="40a51-124">在上面的代码中，`IStringLocalizer<T>` 实现来源于[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="40a51-124">In the code above, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="40a51-125">如果找不到“About Title”的本地化值，则返回索引器键，即字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="40a51-125">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="40a51-126">可将默认语言文本字符串保留在应用中并将它们包装在本地化工具中，以便你可集中精力开发应用。</span><span class="sxs-lookup"><span data-stu-id="40a51-126">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="40a51-127">你使用默认语言开发应用，并针对本地化步骤进行准备，而无需首先创建默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="40a51-127">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="40a51-128">或者，你可以使用传统方法，并提供键以检索默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="40a51-128">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="40a51-129">对于许多开发者而言，不具有默认语言 .resx 文件且简单包装字符串文本的新工作流可以减少本地化应用的开销  。</span><span class="sxs-lookup"><span data-stu-id="40a51-129">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="40a51-130">其他开发者将首选传统工作流，因为它可以更轻松地使用较长字符串文本，更轻松地更新本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="40a51-130">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="40a51-131">对包含 HTML 的资源使用 `IHtmlLocalizer<T>` 实现。</span><span class="sxs-lookup"><span data-stu-id="40a51-131">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="40a51-132">`IHtmlLocalizer` 对资源字符串中格式化的参数进行 HTML 编码，但不对资源字符串本身进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="40a51-132">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="40a51-133">在下面突出显示的示例中，仅 `name` 参数的值被 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="40a51-133">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="40a51-134">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="40a51-134">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="40a51-135">最低程度，你可以从[依赖关系注入](dependency-injection.md)获取 `IStringLocalizerFactory`：</span><span class="sxs-lookup"><span data-stu-id="40a51-135">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="40a51-136">上面的代码演示了这两种工厂创建方法。</span><span class="sxs-lookup"><span data-stu-id="40a51-136">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="40a51-137">可以按控制器、区域对本地化字符串分区，或只有一个容器。</span><span class="sxs-lookup"><span data-stu-id="40a51-137">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="40a51-138">在示例应用中，名为 `SharedResource` 的虚拟类用于共享资源。</span><span class="sxs-lookup"><span data-stu-id="40a51-138">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="40a51-139">某些开发者使用 `Startup` 类，以包含全局或共享字符串。</span><span class="sxs-lookup"><span data-stu-id="40a51-139">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="40a51-140">在下面的示例中，使用 `InfoController` 和 `SharedResource` 本地化工具：</span><span class="sxs-lookup"><span data-stu-id="40a51-140">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="40a51-141">视图本地化</span><span class="sxs-lookup"><span data-stu-id="40a51-141">View localization</span></span>

<span data-ttu-id="40a51-142">`IViewLocalizer` 服务可为[视图](xref:mvc/views/overview)提供本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="40a51-142">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="40a51-143">`ViewLocalizer` 类可实现此接口，并从视图文件路径找到资源位置。</span><span class="sxs-lookup"><span data-stu-id="40a51-143">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="40a51-144">下面的代码演示如何使用 `IViewLocalizer` 的默认实现：</span><span class="sxs-lookup"><span data-stu-id="40a51-144">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="40a51-145">`IViewLocalizer` 的默认实现可根据视图的文件名查找资源文件。</span><span class="sxs-lookup"><span data-stu-id="40a51-145">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="40a51-146">没有使用全局共享资源文件的选项。</span><span class="sxs-lookup"><span data-stu-id="40a51-146">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="40a51-147">`ViewLocalizer` 使用 `IHtmlLocalizer` 实现本地化工具，因此 Razor 不会对本地化字符串进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="40a51-147">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="40a51-148">你可以参数化资源字符串，`IViewLocalizer` 将对参数进行 HTML 编码，但不会对资源字符串进行。</span><span class="sxs-lookup"><span data-stu-id="40a51-148">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="40a51-149">请考虑以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="40a51-149">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="40a51-150">法语资源文件可以包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="40a51-150">A French resource file could contain the following:</span></span>

| <span data-ttu-id="40a51-151">键</span><span class="sxs-lookup"><span data-stu-id="40a51-151">Key</span></span> | <span data-ttu-id="40a51-152">值</span><span class="sxs-lookup"><span data-stu-id="40a51-152">Value</span></span> |
| ----- | ------ |
| `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |

<span data-ttu-id="40a51-153">呈现的视图可能包含资源文件中的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="40a51-153">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="40a51-154">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="40a51-154">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="40a51-155">若要在视图中使用共享资源文件，请注入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="40a51-155">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="40a51-156">DataAnnotations 本地化</span><span class="sxs-lookup"><span data-stu-id="40a51-156">DataAnnotations localization</span></span>

<span data-ttu-id="40a51-157">DataAnnotations 错误消息已使用 `IStringLocalizer<T>` 本地化。</span><span class="sxs-lookup"><span data-stu-id="40a51-157">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="40a51-158">使用选项 `ResourcesPath = "Resources"`，`RegisterViewModel` 中的错误消息可以存储在以下路径之一：</span><span class="sxs-lookup"><span data-stu-id="40a51-158">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="40a51-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="40a51-159">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="40a51-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="40a51-160">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="40a51-161">在 ASP.NET Core MVC 1.1.0 和更高版本中，非验证属性已经进行了本地化。</span><span class="sxs-lookup"><span data-stu-id="40a51-161">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="40a51-162">ASP.NET Core MVC 1.0 不会为非验证属性查找本地化字符串  。</span><span class="sxs-lookup"><span data-stu-id="40a51-162">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="40a51-163">对多个类使用一个资源字符串</span><span class="sxs-lookup"><span data-stu-id="40a51-163">Using one resource string for multiple classes</span></span>

<span data-ttu-id="40a51-164">下面的代码演示如何针对具有多个类的验证属性使用一个资源字符串：</span><span class="sxs-lookup"><span data-stu-id="40a51-164">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="40a51-165">在上面的代码中，`SharedResource` 是对应于存储验证消息的 resx 的类。</span><span class="sxs-lookup"><span data-stu-id="40a51-165">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="40a51-166">使用此方法，DataAnnotations 将仅使用 `SharedResource`，而不是每个类的资源。</span><span class="sxs-lookup"><span data-stu-id="40a51-166">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="40a51-167">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="40a51-167">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="40a51-168">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="40a51-168">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="40a51-169">ASP.NET Core 允许指定两个区域性值，`SupportedCultures` 和 `SupportedUICultures`。</span><span class="sxs-lookup"><span data-stu-id="40a51-169">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="40a51-170">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 对象可决定区域性相关函数的结果，如日期、时间、数字和货币格式等。</span><span class="sxs-lookup"><span data-stu-id="40a51-170">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="40a51-171">`SupportedCultures` 确定文本的排序顺序、大小写约定和字符串比较。</span><span class="sxs-lookup"><span data-stu-id="40a51-171">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="40a51-172">请参阅 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) 详细了解服务器如何获取区域性。</span><span class="sxs-lookup"><span data-stu-id="40a51-172">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="40a51-173">`SupportedUICultures` 可确定哪些转换字符串（.resx 文件中）按 [ResourceManager](/dotnet/api/system.resources.resourcemanager) 查找  。</span><span class="sxs-lookup"><span data-stu-id="40a51-173">The `SupportedUICultures` determines which translates strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="40a51-174">`ResourceManager` 只需查找 `CurrentUICulture` 决定的区域性特定字符串。</span><span class="sxs-lookup"><span data-stu-id="40a51-174">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="40a51-175">.NET 中的每个线程都具有 `CurrentCulture` 和 `CurrentUICulture` 对象。</span><span class="sxs-lookup"><span data-stu-id="40a51-175">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="40a51-176">呈现区域性相关函数时，ASP.NET Core 可检查这些值。</span><span class="sxs-lookup"><span data-stu-id="40a51-176">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="40a51-177">例如，如果当前线程的区域性设置为“en-US”（英语，美国），`DateTime.Now.ToLongDateString()` 将显示“Thursday, February 18, 2016”，但如果 `CurrentCulture` 设置为“es-ES”（西班牙语，西班牙），则输出将为“jueves，18 de febrero de 2016”。</span><span class="sxs-lookup"><span data-stu-id="40a51-177">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="40a51-178">资源文件</span><span class="sxs-lookup"><span data-stu-id="40a51-178">Resource files</span></span>

<span data-ttu-id="40a51-179">资源文件是将可本地化的字符串与代码分离的有用机制。</span><span class="sxs-lookup"><span data-stu-id="40a51-179">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="40a51-180">非默认语言的转换字符串是独立的 .resx 资源文件  。</span><span class="sxs-lookup"><span data-stu-id="40a51-180">Translated strings for the non-default language are isolated *.resx* resource files.</span></span> <span data-ttu-id="40a51-181">例如，你可能想要创建包含转换字符串、名为 Welcome.es.resx 的西班牙语资源文件  。</span><span class="sxs-lookup"><span data-stu-id="40a51-181">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="40a51-182">“es”是西班牙语的语言代码。</span><span class="sxs-lookup"><span data-stu-id="40a51-182">"es" is the language code for Spanish.</span></span> <span data-ttu-id="40a51-183">要在 Visual Studio 中创建此资源文件，请支持以下操作：</span><span class="sxs-lookup"><span data-stu-id="40a51-183">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="40a51-184">在“解决方案资源管理器”中，右键单击将包含资源文件的文件夹 >“添加” > “新项”    。</span><span class="sxs-lookup"><span data-stu-id="40a51-184">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![嵌套的上下文菜单：在“解决方案资源管理器”中，“资源”可打开上下文菜单。](localization/_static/newi.png)

2. <span data-ttu-id="40a51-187">在“搜索已安装的模板”框中，输入“资源”并命名该文件  。</span><span class="sxs-lookup"><span data-stu-id="40a51-187">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![“添加新项”对话框](localization/_static/res.png)

3. <span data-ttu-id="40a51-189">在“名称”列中输入键值（本机字符串），在“值”列中输入转换字符串   。</span><span class="sxs-lookup"><span data-stu-id="40a51-189">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx 文件（西班牙语版 Welcome 资源文件）的单词 Hello 位于“名称”列，Hola（西班牙语版 Hello）位于“值”列](localization/_static/hola.png)

    <span data-ttu-id="40a51-191">Visual Studio 将显示 Welcome.es.resx 文件  。</span><span class="sxs-lookup"><span data-stu-id="40a51-191">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![显示 Welcome Spanish (es) 资源文件的解决方案资源管理器](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="40a51-193">资源文件命名</span><span class="sxs-lookup"><span data-stu-id="40a51-193">Resource file naming</span></span>

<span data-ttu-id="40a51-194">资源名称是类的完整类型名称减去程序集名称。</span><span class="sxs-lookup"><span data-stu-id="40a51-194">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="40a51-195">例如，类 `LocalizationWebsite.Web.Startup` 的主要程序集为 `LocalizationWebsite.Web.dll` 的项目中的法语资源将命名为 Startup.fr.resx  。</span><span class="sxs-lookup"><span data-stu-id="40a51-195">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="40a51-196">类 `LocalizationWebsite.Web.Controllers.HomeController` 的资源将命名为 Controllers.HomeController.fr.resx  。</span><span class="sxs-lookup"><span data-stu-id="40a51-196">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="40a51-197">如果目标类的命名空间与将需要完整类型名称的程序集名称不同。</span><span class="sxs-lookup"><span data-stu-id="40a51-197">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="40a51-198">例如，在示例项目中，类型 `ExtraNamespace.Tools` 的资源将命名为 ExtraNamespace.Tools.fr.resx  。</span><span class="sxs-lookup"><span data-stu-id="40a51-198">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="40a51-199">在示例项目中，`ConfigureServices` 方法将 `ResourcesPath` 设置为“Resources”，因此主控制器的法语资源文件的项目相对路径是 Resources/Controllers.HomeController.fr.resx  。</span><span class="sxs-lookup"><span data-stu-id="40a51-199">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="40a51-200">或者，你可以使用文件夹组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="40a51-200">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="40a51-201">对于主控制器，该路径将为 *Resources/Controllers/HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="40a51-201">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="40a51-202">如果不使用 `ResourcesPath` 选项， *.resx* 文件将转到项目的基目录中。</span><span class="sxs-lookup"><span data-stu-id="40a51-202">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="40a51-203">`HomeController` 的资源文件将命名为 *Controllers.HomeController.fr.resx*。</span><span class="sxs-lookup"><span data-stu-id="40a51-203">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="40a51-204">是选择使用圆点还是路径命名约定，具体取决于你想如何组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="40a51-204">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="40a51-205">资源名称</span><span class="sxs-lookup"><span data-stu-id="40a51-205">Resource name</span></span> | <span data-ttu-id="40a51-206">圆点或路径命名</span><span class="sxs-lookup"><span data-stu-id="40a51-206">Dot or path naming</span></span> |
| ------------   | ------------- |
| <span data-ttu-id="40a51-207">Resources/Controllers.HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="40a51-207">Resources/Controllers.HomeController.fr.resx</span></span> | <span data-ttu-id="40a51-208">圆点</span><span class="sxs-lookup"><span data-stu-id="40a51-208">Dot</span></span>  |
| <span data-ttu-id="40a51-209">Resources/Controllers/HomeController.fr.resx</span><span class="sxs-lookup"><span data-stu-id="40a51-209">Resources/Controllers/HomeController.fr.resx</span></span>  | <span data-ttu-id="40a51-210">路径</span><span class="sxs-lookup"><span data-stu-id="40a51-210">Path</span></span> |
|    |     |

<span data-ttu-id="40a51-211">Razor 视图中使用 `@inject IViewLocalizer` 的资源文件遵循类似的模式。</span><span class="sxs-lookup"><span data-stu-id="40a51-211">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="40a51-212">可以使用圆点命名或路径命名约定对视图的资源文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="40a51-212">The resource file for a view can be named using either dot naming or path naming.</span></span> <span data-ttu-id="40a51-213">Razor 视图资源文件可模拟其关联视图文件的路径。</span><span class="sxs-lookup"><span data-stu-id="40a51-213">Razor view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="40a51-214">假设我们将 `ResourcesPath` 设置为“Resources”，与 *Views/Home/About.cshtml* 视图关联的法语资源文件可能是下面其中之一 ：</span><span class="sxs-lookup"><span data-stu-id="40a51-214">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="40a51-215">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="40a51-215">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="40a51-216">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="40a51-216">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="40a51-217">如果不使用 `ResourcesPath` 选项，视图的 .resx 文件将位于视图所在的文件夹  。</span><span class="sxs-lookup"><span data-stu-id="40a51-217">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="40a51-218">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="40a51-218">RootNamespaceAttribute</span></span> 

<span data-ttu-id="40a51-219">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性在程序集的根命名空间不同于程序集名称时，提供程序集的根命名空间。</span><span class="sxs-lookup"><span data-stu-id="40a51-219">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

<span data-ttu-id="40a51-220">如果程序集的根命名空间不同于程序集名称：</span><span class="sxs-lookup"><span data-stu-id="40a51-220">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="40a51-221">默认情况下无法进行本地化。</span><span class="sxs-lookup"><span data-stu-id="40a51-221">Localization does not work by default.</span></span>
* <span data-ttu-id="40a51-222">因程序集内搜索资源的方式导致本地化失败。</span><span class="sxs-lookup"><span data-stu-id="40a51-222">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="40a51-223">`RootNamespace` 是生成时间值，不可用于正在执行的进程。</span><span class="sxs-lookup"><span data-stu-id="40a51-223">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="40a51-224">如果 `RootNamespace` 不同于 `AssemblyName`，请在 AssemblyInfo.cs 中包括以下内容（参数值替换为实际值）  ：</span><span class="sxs-lookup"><span data-stu-id="40a51-224">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="40a51-225">上述代码可成功解析 resx 文件。</span><span class="sxs-lookup"><span data-stu-id="40a51-225">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="40a51-226">区域性回退行为</span><span class="sxs-lookup"><span data-stu-id="40a51-226">Culture fallback behavior</span></span>

<span data-ttu-id="40a51-227">在搜索资源时，本地化会进行“区域性回退”。</span><span class="sxs-lookup"><span data-stu-id="40a51-227">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="40a51-228">从所请求的区域性开始，如果未能找到，则还原至该区域性的父区域性。</span><span class="sxs-lookup"><span data-stu-id="40a51-228">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="40a51-229">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 属性代表父区域性。</span><span class="sxs-lookup"><span data-stu-id="40a51-229">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="40a51-230">这通常（但并不是总是）意味着从 ISO 中移除区域签名。</span><span class="sxs-lookup"><span data-stu-id="40a51-230">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="40a51-231">例如，墨西哥的西班牙语方言为“es-MX”。</span><span class="sxs-lookup"><span data-stu-id="40a51-231">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="40a51-232">它具备一个父级“es”西班牙，没有特别指定国家/地区。</span><span class="sxs-lookup"><span data-stu-id="40a51-232">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="40a51-233">假设你的站点接收到了一个区域性为“fr-CA”的“Welcome”资源的请求。</span><span class="sxs-lookup"><span data-stu-id="40a51-233">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="40a51-234">本地化系统按顺序查找以下资源，并选择第一个匹配项：</span><span class="sxs-lookup"><span data-stu-id="40a51-234">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="40a51-235">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="40a51-235">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="40a51-236">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="40a51-236">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="40a51-237">*Welcome.resx*（如果 `NeutralResourcesLanguage` 为“fr-CA”）</span><span class="sxs-lookup"><span data-stu-id="40a51-237">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="40a51-238">例如，如果删除了“.fr”区域性指示符，而且已将区域性设置为“法语”，将读取默认资源文件，并本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="40a51-238">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="40a51-239">对于不满足所请求区域性的情况，资源管理器可指定默认资源或回退资源。</span><span class="sxs-lookup"><span data-stu-id="40a51-239">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="40a51-240">缺少适用于请求区域性的资源时，如果只想返回键，不得具有默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="40a51-240">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="40a51-241">使用 Visual Studio 生成资源文件</span><span class="sxs-lookup"><span data-stu-id="40a51-241">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="40a51-242">如果在 Visual Studio 中创建文件名没有区域性的资源文件（例如 Welcome.resx），Visual Studio 将创建一个 C# 类，并且具有每个字符串的属性  。</span><span class="sxs-lookup"><span data-stu-id="40a51-242">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="40a51-243">这通常不是你想在 ASP.NET Core 中使用的。</span><span class="sxs-lookup"><span data-stu-id="40a51-243">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="40a51-244">你通常没有默认的 .resx 资源文件（没有区域性名称的 .resx 文件）   。</span><span class="sxs-lookup"><span data-stu-id="40a51-244">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="40a51-245">建议创建具有区域性名称（例如 Welcome.fr.resx）的 .resx 文件   。</span><span class="sxs-lookup"><span data-stu-id="40a51-245">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="40a51-246">创建具有区域性名称的 .resx 文件时，Visual Studio 不会生成类文件  。</span><span class="sxs-lookup"><span data-stu-id="40a51-246">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span> <span data-ttu-id="40a51-247">我们预计许多开发者不会创建默认语言资源文件。</span><span class="sxs-lookup"><span data-stu-id="40a51-247">We anticipate that many developers won't create a default language resource file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="40a51-248">添加其他区域性</span><span class="sxs-lookup"><span data-stu-id="40a51-248">Add other cultures</span></span>

<span data-ttu-id="40a51-249">每个语言和区域性组合（除默认语言外）都需要唯一资源文件。</span><span class="sxs-lookup"><span data-stu-id="40a51-249">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="40a51-250">通过新建 ISO 语言代码属于名称一部分的资源文件，为不同的区域性和区域设置创建资源文件（例如，en-us、fr-ca 和 en-gb）    。</span><span class="sxs-lookup"><span data-stu-id="40a51-250">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="40a51-251">这些 ISO 编码位于文件名和 .resx 文件扩展之间，如 Welcome.es-MX.resx（西班牙语/墨西哥）   。</span><span class="sxs-lookup"><span data-stu-id="40a51-251">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="40a51-252">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="40a51-252">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="40a51-253">配置本地化</span><span class="sxs-lookup"><span data-stu-id="40a51-253">Configure localization</span></span>

<span data-ttu-id="40a51-254">通过 `Startup.ConfigureServices` 方法配置本地化：</span><span class="sxs-lookup"><span data-stu-id="40a51-254">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="40a51-255">`AddLocalization` 将本地化服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="40a51-255">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="40a51-256">上面的代码还将资源路径设置为“Resources”。</span><span class="sxs-lookup"><span data-stu-id="40a51-256">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="40a51-257">`AddViewLocalization` 添加对本地化视图文件的支持。</span><span class="sxs-lookup"><span data-stu-id="40a51-257">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="40a51-258">在此示例视图中，本地化基于视图文件后缀。</span><span class="sxs-lookup"><span data-stu-id="40a51-258">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="40a51-259">例如，Index.fr.cshtml 文件中的“fr”  。</span><span class="sxs-lookup"><span data-stu-id="40a51-259">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="40a51-260">`AddDataAnnotationsLocalization` 添加通过 `IStringLocalizer` 抽象对本地化 `DataAnnotations` 验证消息的支持。</span><span class="sxs-lookup"><span data-stu-id="40a51-260">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="40a51-261">本地化中间件</span><span class="sxs-lookup"><span data-stu-id="40a51-261">Localization middleware</span></span>

<span data-ttu-id="40a51-262">在本地化[中间件](xref:fundamentals/middleware/index)中设置有关请求的当前区域性。</span><span class="sxs-lookup"><span data-stu-id="40a51-262">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="40a51-263">在 `Startup.Configure` 方法中启用本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="40a51-263">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="40a51-264">必须在中间件前面配置本地化中间件，它可能检查请求区域性（例如，`app.UseMvcWithDefaultRoute()`）。</span><span class="sxs-lookup"><span data-stu-id="40a51-264">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]

<span data-ttu-id="40a51-265">`UseRequestLocalization` 初始化 `RequestLocalizationOptions` 对象。</span><span class="sxs-lookup"><span data-stu-id="40a51-265">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="40a51-266">在每个请求上，枚举了 `RequestLocalizationOptions` 的 `RequestCultureProvider` 列表，使用了可成功决定请求区域性的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="40a51-266">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="40a51-267">默认提供程序来自 `RequestLocalizationOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="40a51-267">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="40a51-268">默认列表从最具体到最不具体排序。</span><span class="sxs-lookup"><span data-stu-id="40a51-268">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="40a51-269">在本文的后面部分，我们将了解如何更改顺序，甚至添加一个自定义区域性提供程序。</span><span class="sxs-lookup"><span data-stu-id="40a51-269">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="40a51-270">如果没有一个提供程序可以确定请求区域性，则使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="40a51-270">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="40a51-271">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="40a51-271">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="40a51-272">某些应用将使用查询字符串来设置[区域性和 UI 区域性](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)。</span><span class="sxs-lookup"><span data-stu-id="40a51-272">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="40a51-273">对于使用 Cookie 或接受语言标题方法的应用，向 URL 添加查询字符串有助于调试和测试代码。</span><span class="sxs-lookup"><span data-stu-id="40a51-273">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="40a51-274">默认情况下，`QueryStringRequestCultureProvider` 注册为 `RequestCultureProvider` 列表中的第一个本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="40a51-274">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="40a51-275">传递查询字符串参数 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="40a51-275">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="40a51-276">下面的示例将特定区域性（语言和区域）设置为“西班牙语/墨西哥”：</span><span class="sxs-lookup"><span data-stu-id="40a51-276">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="40a51-277">如果仅传入两种区域性之一（`culture` 或 `ui-culture`，查询字符串提供程序将使用你传入的区域性设置这两个值。</span><span class="sxs-lookup"><span data-stu-id="40a51-277">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="40a51-278">例如，仅设置区域性将同时设置 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="40a51-278">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="40a51-279">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="40a51-279">CookieRequestCultureProvider</span></span>

<span data-ttu-id="40a51-280">通常，生产应用将提供一种机制来使用 ASP.NET Core 区域性 Cookie 设置区域性。</span><span class="sxs-lookup"><span data-stu-id="40a51-280">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="40a51-281">若要创建 Cookie，请使用 `MakeCookieValue` 方法。</span><span class="sxs-lookup"><span data-stu-id="40a51-281">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="40a51-282">`CookieRequestCultureProvider` `DefaultCookieName` 将返回用来跟踪用户首选区域性信息的默认 Cookie 名称。</span><span class="sxs-lookup"><span data-stu-id="40a51-282">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="40a51-283">默认 Cookie 名称是 `.AspNetCore.Culture`。</span><span class="sxs-lookup"><span data-stu-id="40a51-283">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="40a51-284">Cookie 格式为 `c=%LANGCODE%|uic=%LANGCODE%`，其中`c` 是 `Culture`，`uic` 是 `UICulture`，例如：</span><span class="sxs-lookup"><span data-stu-id="40a51-284">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="40a51-285">如果仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将同时用于区域性信息和 UI 区域性。</span><span class="sxs-lookup"><span data-stu-id="40a51-285">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="40a51-286">接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="40a51-286">The Accept-Language HTTP header</span></span>

<span data-ttu-id="40a51-287">[接受语言标题](https://www.w3.org/International/questions/qa-accept-lang-locales)在大多数浏览器中可设置，最初用于指定用户的语言。</span><span class="sxs-lookup"><span data-stu-id="40a51-287">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="40a51-288">此设置指示浏览器已设置为发送或已从基础操作系统继承的内容。</span><span class="sxs-lookup"><span data-stu-id="40a51-288">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="40a51-289">浏览器请求的接受语言 HTTP 标题不是检测用户首选语言的可靠方法（请参阅 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)（在浏览器中设置首选项）。</span><span class="sxs-lookup"><span data-stu-id="40a51-289">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="40a51-290">生产应用应包括一种用户可以自定义区域性选择的方法。</span><span class="sxs-lookup"><span data-stu-id="40a51-290">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="40a51-291">在 IE 中设置接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="40a51-291">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="40a51-292">在齿轮图标中，点击“Internet 选项”  。</span><span class="sxs-lookup"><span data-stu-id="40a51-292">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="40a51-293">点击“语言”  。</span><span class="sxs-lookup"><span data-stu-id="40a51-293">Tap **Languages**.</span></span>

    ![Internet 选项](localization/_static/lang.png)

3. <span data-ttu-id="40a51-295">点击“设置语言首选项”  。</span><span class="sxs-lookup"><span data-stu-id="40a51-295">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="40a51-296">点击“添加语言”  。</span><span class="sxs-lookup"><span data-stu-id="40a51-296">Tap **Add a language**.</span></span>

5. <span data-ttu-id="40a51-297">添加语言。</span><span class="sxs-lookup"><span data-stu-id="40a51-297">Add the language.</span></span>

6. <span data-ttu-id="40a51-298">点击语言，然后点击“向上移动”  。</span><span class="sxs-lookup"><span data-stu-id="40a51-298">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="40a51-299">使用自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="40a51-299">Use a custom provider</span></span>

<span data-ttu-id="40a51-300">假设你想要让客户在数据库中存储其语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="40a51-300">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="40a51-301">你可以编写一个提供程序来查找用户的这些值。</span><span class="sxs-lookup"><span data-stu-id="40a51-301">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="40a51-302">下面的代码演示如何添加自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="40a51-302">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="40a51-303">使用 `RequestLocalizationOptions` 添加或删除本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="40a51-303">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="40a51-304">以编程方式设置区域性</span><span class="sxs-lookup"><span data-stu-id="40a51-304">Set the culture programmatically</span></span>

<span data-ttu-id="40a51-305">[GitHub](https://github.com/aspnet/entropy) 上的示例项目 Localization.StarterWeb 包含设置 `Culture` 的 UI  。</span><span class="sxs-lookup"><span data-stu-id="40a51-305">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="40a51-306">Views/Shared/_SelectLanguagePartial.cshtml 文件允许你从支持的区域性列表中选择区域性  ：</span><span class="sxs-lookup"><span data-stu-id="40a51-306">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="40a51-307">Views/Shared/_SelectLanguagePartial.cshtml 文件添加到了布局文件的 `footer` 部分，使它将可供所有视图使用  ：</span><span class="sxs-lookup"><span data-stu-id="40a51-307">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="40a51-308">`SetLanguage` 方法可设置区域性 Cookie。</span><span class="sxs-lookup"><span data-stu-id="40a51-308">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="40a51-309">不能将 _SelectLanguagePartial.cshtml 插入此项目的示例代码  。</span><span class="sxs-lookup"><span data-stu-id="40a51-309">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="40a51-310">[GitHub](https://github.com/aspnet/entropy) 上的项目 Localization.StarterWeb 包含代码，可部分通过[依赖关系注入](dependency-injection.md)容器将 `RequestLocalizationOptions` 流到 Razor  。</span><span class="sxs-lookup"><span data-stu-id="40a51-310">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="40a51-311">全球化和本地化术语</span><span class="sxs-lookup"><span data-stu-id="40a51-311">Globalization and localization terms</span></span>

<span data-ttu-id="40a51-312">本地化应用的过程还要求基本了解现代软件开发中常用的相关字符集，以及与之相关的问题。</span><span class="sxs-lookup"><span data-stu-id="40a51-312">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="40a51-313">尽管所有计算机将文本都存储为数字（代码），但不同的系统使用不同的数字存储相同的文本。</span><span class="sxs-lookup"><span data-stu-id="40a51-313">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="40a51-314">本地化过程是指针对特定区域性/区域设置转换应用的用户界面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="40a51-314">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="40a51-315">[本地化性](/dotnet/standard/globalization-localization/localizability-review)是一个中间过程，用于验证全球化应用是否准备好进行本地化。</span><span class="sxs-lookup"><span data-stu-id="40a51-315">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="40a51-316">区域性名称的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式为 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是语言代码，`<country/regioncode2>` 是子区域性代码。</span><span class="sxs-lookup"><span data-stu-id="40a51-316">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="40a51-317">例如，`es-CL` 表示西班牙语（智利），`en-US` 表示英语（美国），而 `en-AU` 表示英语（澳大利亚）。</span><span class="sxs-lookup"><span data-stu-id="40a51-317">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="40a51-318">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是一个与语言相关的 ISO 639 双小写字母的区域性代码和一个与国家/地区相关的 ISO 3166 双大写字母子区域性代码的组合。</span><span class="sxs-lookup"><span data-stu-id="40a51-318">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="40a51-319">请参阅[语言区域性名称](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)。</span><span class="sxs-lookup"><span data-stu-id="40a51-319">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="40a51-320">国际化常缩写为“I18N”。</span><span class="sxs-lookup"><span data-stu-id="40a51-320">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="40a51-321">缩写采用第一个和最后一个字母以及它们之间的字母数，因此 18 代表第一个字母“I”和最后一个“N”之间的字母数。</span><span class="sxs-lookup"><span data-stu-id="40a51-321">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="40a51-322">这同样适用于全球化 (G11N) 和本地化 (L10N)。</span><span class="sxs-lookup"><span data-stu-id="40a51-322">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="40a51-323">术语：</span><span class="sxs-lookup"><span data-stu-id="40a51-323">Terms:</span></span>

* <span data-ttu-id="40a51-324">全球化 (G11N)：使应用支持不同语言和区域的过程。</span><span class="sxs-lookup"><span data-stu-id="40a51-324">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="40a51-325">本地化 (L10N)：针对给定语言和区域自定义应用的过程。</span><span class="sxs-lookup"><span data-stu-id="40a51-325">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="40a51-326">国际化 (I18N)：介绍了全球化和本地化。</span><span class="sxs-lookup"><span data-stu-id="40a51-326">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="40a51-327">区域性：它是一种语言和区域（可选）。</span><span class="sxs-lookup"><span data-stu-id="40a51-327">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="40a51-328">非特定区域性：具有指定语言但不具有区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="40a51-328">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="40a51-329">（例如，“en”，“es”）</span><span class="sxs-lookup"><span data-stu-id="40a51-329">(for example "en", "es")</span></span>
* <span data-ttu-id="40a51-330">特定区域性：具有指定语言和区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="40a51-330">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="40a51-331">（例如，“en-US”，“en-GB”，“es-CL”）</span><span class="sxs-lookup"><span data-stu-id="40a51-331">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="40a51-332">父区域性：包含特定区域性的非特定区域性。</span><span class="sxs-lookup"><span data-stu-id="40a51-332">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="40a51-333">（例如，“en”是“en-US”和“en-GB”的父区域性）</span><span class="sxs-lookup"><span data-stu-id="40a51-333">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="40a51-334">区域设置：区域设置与区域性相同。</span><span class="sxs-lookup"><span data-stu-id="40a51-334">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="40a51-335">其他资源</span><span class="sxs-lookup"><span data-stu-id="40a51-335">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="40a51-336">本文所用的 [Localization.StarterWeb 项目](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="40a51-336">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="40a51-337">对 .NET 应用程序进行全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="40a51-337">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="40a51-338">.resx 文件中的资源</span><span class="sxs-lookup"><span data-stu-id="40a51-338">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="40a51-339">Microsoft 多语言应用工具包</span><span class="sxs-lookup"><span data-stu-id="40a51-339">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="40a51-340">本地化与泛型</span><span class="sxs-lookup"><span data-stu-id="40a51-340">Localization & Generics</span></span>](https://github.com/hishamco/hishambinateya.com/blob/master/Posts/localization-and-generics.md)
