---
<span data-ttu-id="17d8e-101">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-101">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-102">'Blazor'</span></span>
- <span data-ttu-id="17d8e-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-103">'Identity'</span></span>
- <span data-ttu-id="17d8e-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-105">'Razor'</span></span>
- <span data-ttu-id="17d8e-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-106">'SignalR' uid:</span></span> 

---
# <a name="globalization-and-localization-in-aspnet-core"></a><span data-ttu-id="17d8e-107">ASP.NET Core 全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-107">Globalization and localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="17d8e-108">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="17d8e-108">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="17d8e-109">多语言网站使网站可以覆盖更广泛的受众。</span><span class="sxs-lookup"><span data-stu-id="17d8e-109">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="17d8e-110">ASP.NET Core 提供的服务和中间件可将网站本地化为不同的语言和文化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-110">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="17d8e-111">国际化涉及[全球化](/dotnet/api/system.globalization)和[本地化](/dotnet/standard/globalization-localization/localization)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-111">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="17d8e-112">全球化是设计支持不同区域性的应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-112">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="17d8e-113">全球化添加了对一组有关特定地理区域的已定义语言脚本的输入、显示和输出支持。</span><span class="sxs-lookup"><span data-stu-id="17d8e-113">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="17d8e-114">本地化是将已经针对可本地化性进行处理的全球化应用调整为特定的区域性/区域设置的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-114">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="17d8e-115">有关详细信息，请参阅本文档邻近末尾的全球化和本地化术语。</span><span class="sxs-lookup"><span data-stu-id="17d8e-115">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="17d8e-116">应用本地化涉及以下内容：</span><span class="sxs-lookup"><span data-stu-id="17d8e-116">App localization involves the following:</span></span>

1. <span data-ttu-id="17d8e-117">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-117">Make the app's content localizable</span></span>
1. <span data-ttu-id="17d8e-118">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-118">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="17d8e-119">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-119">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="17d8e-120">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="17d8e-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="17d8e-121">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-121">Make the app's content localizable</span></span>

<span data-ttu-id="17d8e-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer 不要求在资源文件中存储默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-122"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="17d8e-123">你可以开发针对本地化的应用，且无需在开发初期创建资源资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-123">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="17d8e-124">下面的代码演示如何针对本地化包装字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-124">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="17d8e-125">在前面的代码中，`IStringLocalizer<T>` 实现来源于[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-125">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="17d8e-126">如果找不到“About Title”的本地化值，则返回索引器键，即字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-126">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="17d8e-127">可将默认语言文本字符串保留在应用中并将它们包装在本地化工具中，以便你可集中精力开发应用。</span><span class="sxs-lookup"><span data-stu-id="17d8e-127">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="17d8e-128">你使用默认语言开发应用，并针对本地化步骤进行准备，而无需首先创建默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-128">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="17d8e-129">或者，你可以使用传统方法，并提供键以检索默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-129">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="17d8e-130">对于许多开发者而言，不具有默认语言 .resx 文件且简单包装字符串文本的新工作流可以减少本地化应用的开销。</span><span class="sxs-lookup"><span data-stu-id="17d8e-130">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="17d8e-131">其他开发者将首选传统工作流，因为它可以更轻松地使用较长字符串文本，更轻松地更新本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-131">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="17d8e-132">对包含 HTML 的资源使用 `IHtmlLocalizer<T>` 实现。</span><span class="sxs-lookup"><span data-stu-id="17d8e-132">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="17d8e-133">`IHtmlLocalizer` 对资源字符串中格式化的参数进行 HTML 编码，但不对资源字符串本身进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-133">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="17d8e-134">在下面突出显示的示例中，仅 `name` 参数的值被 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-134">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="17d8e-135">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17d8e-135">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="17d8e-136">最低程度，你可以从[依赖关系注入](dependency-injection.md)获取 `IStringLocalizerFactory`：</span><span class="sxs-lookup"><span data-stu-id="17d8e-136">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="17d8e-137">上面的代码演示了这两种工厂创建方法。</span><span class="sxs-lookup"><span data-stu-id="17d8e-137">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="17d8e-138">可以按控制器、区域对本地化字符串分区，或只有一个容器。</span><span class="sxs-lookup"><span data-stu-id="17d8e-138">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="17d8e-139">在示例应用中，名为 `SharedResource` 的虚拟类用于共享资源。</span><span class="sxs-lookup"><span data-stu-id="17d8e-139">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="17d8e-140">某些开发者使用 `Startup` 类，以包含全局或共享字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-140">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="17d8e-141">在下面的示例中，使用 `InfoController` 和 `SharedResource` 本地化工具：</span><span class="sxs-lookup"><span data-stu-id="17d8e-141">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="17d8e-142">视图本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-142">View localization</span></span>

<span data-ttu-id="17d8e-143">`IViewLocalizer` 服务可为[视图](xref:mvc/views/overview)提供本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-143">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="17d8e-144">`ViewLocalizer` 类可实现此接口，并从视图文件路径找到资源位置。</span><span class="sxs-lookup"><span data-stu-id="17d8e-144">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="17d8e-145">下面的代码演示如何使用 `IViewLocalizer` 的默认实现：</span><span class="sxs-lookup"><span data-stu-id="17d8e-145">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="17d8e-146">`IViewLocalizer` 的默认实现可根据视图的文件名查找资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-146">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="17d8e-147">没有使用全局共享资源文件的选项。</span><span class="sxs-lookup"><span data-stu-id="17d8e-147">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="17d8e-148">`ViewLocalizer` 使用 `IHtmlLocalizer` 实现本地化工具，因此 Razor 不会对本地化字符串进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-148">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="17d8e-149">你可以参数化资源字符串，`IViewLocalizer` 将对参数进行 HTML 编码，但不会对资源字符串进行。</span><span class="sxs-lookup"><span data-stu-id="17d8e-149">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="17d8e-150">请考虑以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="17d8e-150">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="17d8e-151">法语资源文件可以包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="17d8e-151">A French resource file could contain the following:</span></span>

| <span data-ttu-id="17d8e-152">键</span><span class="sxs-lookup"><span data-stu-id="17d8e-152">Key</span></span> | <span data-ttu-id="17d8e-153">“值”</span><span class="sxs-lookup"><span data-stu-id="17d8e-153">Value</span></span> |
| ----- | ---
<span data-ttu-id="17d8e-154">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-154">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-155">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-155">'Blazor'</span></span>
- <span data-ttu-id="17d8e-156">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-156">'Identity'</span></span>
- <span data-ttu-id="17d8e-157">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-157">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-158">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-158">'Razor'</span></span>
- <span data-ttu-id="17d8e-159">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-159">'SignalR' uid:</span></span> 

<span data-ttu-id="17d8e-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="17d8e-160">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="17d8e-161">呈现的视图可能包含资源文件中的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="17d8e-161">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="17d8e-162">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17d8e-162">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="17d8e-163">若要在视图中使用共享资源文件，请注入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="17d8e-163">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="17d8e-164">DataAnnotations 本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-164">DataAnnotations localization</span></span>

<span data-ttu-id="17d8e-165">DataAnnotations 错误消息已使用 `IStringLocalizer<T>` 本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-165">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="17d8e-166">使用选项 `ResourcesPath = "Resources"`，`RegisterViewModel` 中的错误消息可以存储在以下路径之一：</span><span class="sxs-lookup"><span data-stu-id="17d8e-166">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="17d8e-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-167">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="17d8e-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-168">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="17d8e-169">在 ASP.NET Core MVC 1.1.0 和更高版本中，非验证属性已经进行了本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-169">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="17d8e-170">ASP.NET Core MVC 1.0 不会为非验证属性查找本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-170">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="17d8e-171">对多个类使用一个资源字符串</span><span class="sxs-lookup"><span data-stu-id="17d8e-171">Using one resource string for multiple classes</span></span>

<span data-ttu-id="17d8e-172">下面的代码演示如何针对具有多个类的验证属性使用一个资源字符串：</span><span class="sxs-lookup"><span data-stu-id="17d8e-172">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="17d8e-173">在上面的代码中，`SharedResource` 是对应于存储验证消息的 resx 的类。</span><span class="sxs-lookup"><span data-stu-id="17d8e-173">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="17d8e-174">使用此方法，DataAnnotations 将仅使用 `SharedResource`，而不是每个类的资源。</span><span class="sxs-lookup"><span data-stu-id="17d8e-174">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="17d8e-175">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-175">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="17d8e-176">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="17d8e-176">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="17d8e-177">ASP.NET Core 允许指定两个区域性值，`SupportedCultures` 和 `SupportedUICultures`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-177">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="17d8e-178">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 对象可决定区域性相关函数的结果，如日期、时间、数字和货币格式等。</span><span class="sxs-lookup"><span data-stu-id="17d8e-178">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="17d8e-179">`SupportedCultures` 确定文本的排序顺序、大小写约定和字符串比较。</span><span class="sxs-lookup"><span data-stu-id="17d8e-179">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="17d8e-180">请参阅 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) 详细了解服务器如何获取区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-180">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="17d8e-181">`SupportedUICultures` 可确定按 [ResourceManager](/dotnet/api/system.resources.resourcemanager) 来查找哪些转换字符串（位于 .resx 文件）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-181">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="17d8e-182">`ResourceManager` 只需查找 `CurrentUICulture` 决定的区域性特定字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-182">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="17d8e-183">.NET 中的每个线程都具有 `CurrentCulture` 和 `CurrentUICulture` 对象。</span><span class="sxs-lookup"><span data-stu-id="17d8e-183">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="17d8e-184">呈现区域性相关函数时，ASP.NET Core 可检查这些值。</span><span class="sxs-lookup"><span data-stu-id="17d8e-184">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="17d8e-185">例如，如果当前线程的区域性设置为“en-US”（英语，美国），`DateTime.Now.ToLongDateString()` 将显示“Thursday, February 18, 2016”，但如果 `CurrentCulture` 设置为“es-ES”（西班牙语，西班牙），则输出将为“jueves，18 de febrero de 2016”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-185">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="17d8e-186">资源文件</span><span class="sxs-lookup"><span data-stu-id="17d8e-186">Resource files</span></span>

<span data-ttu-id="17d8e-187">资源文件是将可本地化的字符串与代码分离的有用机制。</span><span class="sxs-lookup"><span data-stu-id="17d8e-187">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="17d8e-188">非默认语言的转换字符串在 .resx 资源文件中单独显示。</span><span class="sxs-lookup"><span data-stu-id="17d8e-188">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="17d8e-189">例如，你可能想要创建包含转换字符串、名为 Welcome.es.resx 的西班牙语资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-189">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="17d8e-190">“es”是西班牙语的语言代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-190">"es" is the language code for Spanish.</span></span> <span data-ttu-id="17d8e-191">要在 Visual Studio 中创建此资源文件，请支持以下操作：</span><span class="sxs-lookup"><span data-stu-id="17d8e-191">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="17d8e-192">在“解决方案资源管理器”中，右键单击将包含资源文件的文件夹 >“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="17d8e-192">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![嵌套的上下文菜单：在“解决方案资源管理器”中，“资源”可打开上下文菜单。](localization/_static/newi.png)

2. <span data-ttu-id="17d8e-195">在“搜索已安装的模板”框中，输入“资源”并命名该文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-195">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![“添加新项”对话框](localization/_static/res.png)

3. <span data-ttu-id="17d8e-197">在“名称”列中输入键值（本机字符串），在“值”列中输入转换字符串 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-197">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx 文件（西班牙语版 Welcome 资源文件）的单词 Hello 位于“名称”列，Hola（西班牙语版 Hello）位于“值”列](localization/_static/hola.png)

    <span data-ttu-id="17d8e-199">Visual Studio 将显示 Welcome.es.resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-199">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![显示 Welcome Spanish (es) 资源文件的解决方案资源管理器](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="17d8e-201">资源文件命名</span><span class="sxs-lookup"><span data-stu-id="17d8e-201">Resource file naming</span></span>

<span data-ttu-id="17d8e-202">资源名称是类的完整类型名称减去程序集名称。</span><span class="sxs-lookup"><span data-stu-id="17d8e-202">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="17d8e-203">例如，类 `LocalizationWebsite.Web.Startup` 的主要程序集为 `LocalizationWebsite.Web.dll` 的项目中的法语资源将命名为 Startup.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-203">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="17d8e-204">类 `LocalizationWebsite.Web.Controllers.HomeController` 的资源将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-204">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-205">如果目标类的命名空间与将需要完整类型名称的程序集名称不同。</span><span class="sxs-lookup"><span data-stu-id="17d8e-205">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="17d8e-206">例如，在示例项目中，类型 `ExtraNamespace.Tools` 的资源将命名为 ExtraNamespace.Tools.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-206">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="17d8e-207">在示例项目中，`ConfigureServices` 方法将 `ResourcesPath` 设置为“资源”，因此主控制器的法语资源文件的项目相对路径是 Resources/Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-207">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-208">或者，你可以使用文件夹组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-208">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="17d8e-209">对于主控制器，该路径将为 Resources/Controllers/HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-209">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-210">如果不使用 `ResourcesPath` 选项，.resx 文件将转到项目的基目录中。</span><span class="sxs-lookup"><span data-stu-id="17d8e-210">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="17d8e-211">`HomeController` 的资源文件将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-211">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-212">是选择使用圆点还是路径命名约定，具体取决于你想如何组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-212">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="17d8e-213">资源名称</span><span class="sxs-lookup"><span data-stu-id="17d8e-213">Resource name</span></span> | <span data-ttu-id="17d8e-214">圆点或路径命名</span><span class="sxs-lookup"><span data-stu-id="17d8e-214">Dot or path naming</span></span> |
| ---
<span data-ttu-id="17d8e-215">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-215">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-216">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-216">'Blazor'</span></span>
- <span data-ttu-id="17d8e-217">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-217">'Identity'</span></span>
- <span data-ttu-id="17d8e-218">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-218">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-219">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-219">'Razor'</span></span>
- <span data-ttu-id="17d8e-220">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-220">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-221">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-221">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-222">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-222">'Blazor'</span></span>
- <span data-ttu-id="17d8e-223">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-223">'Identity'</span></span>
- <span data-ttu-id="17d8e-224">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-224">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-225">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-225">'Razor'</span></span>
- <span data-ttu-id="17d8e-226">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-226">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-227">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-227">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-228">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-228">'Blazor'</span></span>
- <span data-ttu-id="17d8e-229">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-229">'Identity'</span></span>
- <span data-ttu-id="17d8e-230">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-230">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-231">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-231">'Razor'</span></span>
- <span data-ttu-id="17d8e-232">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-232">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-233">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-233">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-234">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-234">'Blazor'</span></span>
- <span data-ttu-id="17d8e-235">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-235">'Identity'</span></span>
- <span data-ttu-id="17d8e-236">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-236">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-237">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-237">'Razor'</span></span>
- <span data-ttu-id="17d8e-238">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-238">'SignalR' uid:</span></span> 

<span data-ttu-id="17d8e-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-239">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-240">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-240">'Blazor'</span></span>
- <span data-ttu-id="17d8e-241">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-241">'Identity'</span></span>
- <span data-ttu-id="17d8e-242">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-242">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-243">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-243">'Razor'</span></span>
- <span data-ttu-id="17d8e-244">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-244">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-245">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-245">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-246">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-246">'Blazor'</span></span>
- <span data-ttu-id="17d8e-247">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-247">'Identity'</span></span>
- <span data-ttu-id="17d8e-248">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-248">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-249">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-249">'Razor'</span></span>
- <span data-ttu-id="17d8e-250">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-250">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-251">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-251">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-252">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-252">'Blazor'</span></span>
- <span data-ttu-id="17d8e-253">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-253">'Identity'</span></span>
- <span data-ttu-id="17d8e-254">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-254">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-255">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-255">'Razor'</span></span>
- <span data-ttu-id="17d8e-256">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-256">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-257">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-257">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-258">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-258">'Blazor'</span></span>
- <span data-ttu-id="17d8e-259">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-259">'Identity'</span></span>
- <span data-ttu-id="17d8e-260">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-260">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-261">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-261">'Razor'</span></span>
- <span data-ttu-id="17d8e-262">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-262">'SignalR' uid:</span></span> 

<span data-ttu-id="17d8e-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span><span class="sxs-lookup"><span data-stu-id="17d8e-263">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="17d8e-264">Razor 视图中使用 `@inject IViewLocalizer` 的资源文件遵循类似的模式。</span><span class="sxs-lookup"><span data-stu-id="17d8e-264">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="17d8e-265">可以使用圆点命名或路径命名约定对视图的资源文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="17d8e-265">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="17d8e-266"> 视图资源文件可模拟其关联视图文件的路径。</span><span class="sxs-lookup"><span data-stu-id="17d8e-266"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="17d8e-267">假设我们将 `ResourcesPath` 设置为“Resources”，与 *Views/Home/About.cshtml* 视图关联的法语资源文件可能是下面其中之一 ：</span><span class="sxs-lookup"><span data-stu-id="17d8e-267">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="17d8e-268">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17d8e-268">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="17d8e-269">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17d8e-269">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="17d8e-270">如果不使用 `ResourcesPath` 选项，视图的 .resx 文件将位于视图所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="17d8e-270">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="17d8e-271">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="17d8e-271">RootNamespaceAttribute</span></span> 

<span data-ttu-id="17d8e-272">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性在程序集的根命名空间不同于程序集名称时，提供程序集的根命名空间。</span><span class="sxs-lookup"><span data-stu-id="17d8e-272">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="17d8e-273">当项目名称不是有效的 .NET 标识符时，可能会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="17d8e-273">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="17d8e-274">例如，`my-project-name.csproj` 将使用根命名空间 `my_project_name` 和导致此错误的程序集名称 `my-project-name`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-274">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="17d8e-275">如果程序集的根命名空间不同于程序集名称：</span><span class="sxs-lookup"><span data-stu-id="17d8e-275">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="17d8e-276">默认情况下无法进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-276">Localization does not work by default.</span></span>
* <span data-ttu-id="17d8e-277">因程序集内搜索资源的方式导致本地化失败。</span><span class="sxs-lookup"><span data-stu-id="17d8e-277">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="17d8e-278">`RootNamespace` 是生成时间值，不可用于正在执行的进程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-278">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="17d8e-279">如果 `RootNamespace` 不同于 `AssemblyName`，请在 AssemblyInfo.cs 中包括以下内容（参数值替换为实际值）：</span><span class="sxs-lookup"><span data-stu-id="17d8e-279">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="17d8e-280">上述代码可成功解析 resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-280">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="17d8e-281">区域性回退行为</span><span class="sxs-lookup"><span data-stu-id="17d8e-281">Culture fallback behavior</span></span>

<span data-ttu-id="17d8e-282">在搜索资源时，本地化会进行“区域性回退”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-282">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="17d8e-283">从所请求的区域性开始，如果未能找到，则还原至该区域性的父区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-283">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="17d8e-284">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 属性代表父区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-284">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="17d8e-285">这通常（但并不是总是）意味着从 ISO 中移除区域签名。</span><span class="sxs-lookup"><span data-stu-id="17d8e-285">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="17d8e-286">例如，墨西哥的西班牙语方言为“es-MX”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-286">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="17d8e-287">它具备一个父级“es”西班牙，没有特别指定国家/地区。</span><span class="sxs-lookup"><span data-stu-id="17d8e-287">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="17d8e-288">假设你的站点接收到了一个区域性为“fr-CA”的“Welcome”资源的请求。</span><span class="sxs-lookup"><span data-stu-id="17d8e-288">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="17d8e-289">本地化系统按顺序查找以下资源，并选择第一个匹配项：</span><span class="sxs-lookup"><span data-stu-id="17d8e-289">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="17d8e-290">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-290">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="17d8e-291">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-291">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="17d8e-292">*Welcome.resx*（如果 `NeutralResourcesLanguage` 为“fr-CA”）</span><span class="sxs-lookup"><span data-stu-id="17d8e-292">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="17d8e-293">例如，如果删除了“.fr”区域性指示符，而且已将区域性设置为“法语”，将读取默认资源文件，并本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-293">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="17d8e-294">对于不满足所请求区域性的情况，资源管理器可指定默认资源或回退资源。</span><span class="sxs-lookup"><span data-stu-id="17d8e-294">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="17d8e-295">缺少适用于请求区域性的资源时，如果只想返回键，不得具有默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-295">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="17d8e-296">使用 Visual Studio 生成资源文件</span><span class="sxs-lookup"><span data-stu-id="17d8e-296">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="17d8e-297">如果在 Visual Studio 中创建文件名没有区域性的资源文件（例如 Welcome.resx），Visual Studio 将创建一个 C# 类，并且具有每个字符串的属性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-297">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="17d8e-298">这通常不是你想在 ASP.NET Core 中使用的。</span><span class="sxs-lookup"><span data-stu-id="17d8e-298">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="17d8e-299">你通常没有默认的 .resx 资源文件（没有区域性名称的 .resx 文件） 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-299">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="17d8e-300">建议创建具有区域性名称（例如 Welcome.fr.resx）的 .resx 文件 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-300">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="17d8e-301">创建具有区域性名称的 .resx 文件时，Visual Studio 不会生成类文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-301">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="17d8e-302">添加其他区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-302">Add other cultures</span></span>

<span data-ttu-id="17d8e-303">每个语言和区域性组合（除默认语言外）都需要唯一资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-303">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="17d8e-304">通过新建 ISO 语言代码属于名称一部分的资源文件，为不同的区域性和区域设置创建资源文件（例如，en-us、fr-ca 和 en-gb）  。</span><span class="sxs-lookup"><span data-stu-id="17d8e-304">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="17d8e-305">这些 ISO 编码位于文件名和 .resx 文件扩展之间，如 Welcome.es-MX.resx（西班牙语/墨西哥） 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-305">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="17d8e-306">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-306">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="17d8e-307">配置本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-307">Configure localization</span></span>

<span data-ttu-id="17d8e-308">通过 `Startup.ConfigureServices` 方法配置本地化：</span><span class="sxs-lookup"><span data-stu-id="17d8e-308">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="17d8e-309">`AddLocalization` 将本地化服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="17d8e-309">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="17d8e-310">上面的代码还将资源路径设置为“Resources”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-310">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="17d8e-311">`AddViewLocalization` 添加对本地化视图文件的支持。</span><span class="sxs-lookup"><span data-stu-id="17d8e-311">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="17d8e-312">在此示例视图中，本地化基于视图文件后缀。</span><span class="sxs-lookup"><span data-stu-id="17d8e-312">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="17d8e-313">例如，Index.fr.cshtml 文件中的“fr”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-313">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="17d8e-314">`AddDataAnnotationsLocalization` 添加通过 `IStringLocalizer` 抽象对本地化 `DataAnnotations` 验证消息的支持。</span><span class="sxs-lookup"><span data-stu-id="17d8e-314">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="17d8e-315">本地化中间件</span><span class="sxs-lookup"><span data-stu-id="17d8e-315">Localization middleware</span></span>

<span data-ttu-id="17d8e-316">在本地化[中间件](xref:fundamentals/middleware/index)中设置有关请求的当前区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-316">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="17d8e-317">在 `Startup.Configure` 方法中启用本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-317">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="17d8e-318">必须在中间件前面配置本地化中间件，它可能检查请求区域性（例如，`app.UseMvcWithDefaultRoute()`）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-318">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="17d8e-319">`UseRequestLocalization` 初始化 `RequestLocalizationOptions` 对象。</span><span class="sxs-lookup"><span data-stu-id="17d8e-319">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="17d8e-320">在每个请求上，枚举了 `RequestLocalizationOptions` 的 `RequestCultureProvider` 列表，使用了可成功决定请求区域性的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-320">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="17d8e-321">默认提供程序来自 `RequestLocalizationOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="17d8e-321">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="17d8e-322">默认列表从最具体到最不具体排序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-322">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="17d8e-323">在本文的后面部分，我们将了解如何更改顺序，甚至添加一个自定义区域性提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-323">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="17d8e-324">如果没有一个提供程序可以确定请求区域性，则使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-324">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="17d8e-325">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17d8e-325">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="17d8e-326">某些应用将使用查询字符串来设置[区域性和 UI 区域性](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-326">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="17d8e-327">对于使用 Cookie 或接受语言标题方法的应用，向 URL 添加查询字符串有助于调试和测试代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-327">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="17d8e-328">默认情况下，`QueryStringRequestCultureProvider` 注册为 `RequestCultureProvider` 列表中的第一个本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-328">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="17d8e-329">传递查询字符串参数 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-329">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="17d8e-330">下面的示例将特定区域性（语言和区域）设置为“西班牙语/墨西哥”：</span><span class="sxs-lookup"><span data-stu-id="17d8e-330">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="17d8e-331">如果仅传入两种区域性之一（`culture` 或 `ui-culture`，查询字符串提供程序将使用你传入的区域性设置这两个值。</span><span class="sxs-lookup"><span data-stu-id="17d8e-331">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="17d8e-332">例如，仅设置区域性将同时设置 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="17d8e-332">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="17d8e-333">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17d8e-333">CookieRequestCultureProvider</span></span>

<span data-ttu-id="17d8e-334">通常，生产应用将提供一种机制来使用 ASP.NET Core 区域性 Cookie 设置区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-334">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="17d8e-335">若要创建 Cookie，请使用 `MakeCookieValue` 方法。</span><span class="sxs-lookup"><span data-stu-id="17d8e-335">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="17d8e-336">`CookieRequestCultureProvider``DefaultCookieName` 将返回用来跟踪用户首选区域性信息的默认 Cookie 名称。</span><span class="sxs-lookup"><span data-stu-id="17d8e-336">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="17d8e-337">默认 Cookie 名称是 `.AspNetCore.Culture`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-337">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="17d8e-338">Cookie 格式为 `c=%LANGCODE%|uic=%LANGCODE%`，其中`c` 是 `Culture`，`uic` 是 `UICulture`，例如：</span><span class="sxs-lookup"><span data-stu-id="17d8e-338">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="17d8e-339">如果仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将同时用于区域性信息和 UI 区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-339">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="17d8e-340">接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17d8e-340">The Accept-Language HTTP header</span></span>

<span data-ttu-id="17d8e-341">[接受语言标题](https://www.w3.org/International/questions/qa-accept-lang-locales)在大多数浏览器中可设置，最初用于指定用户的语言。</span><span class="sxs-lookup"><span data-stu-id="17d8e-341">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="17d8e-342">此设置指示浏览器已设置为发送或已从基础操作系统继承的内容。</span><span class="sxs-lookup"><span data-stu-id="17d8e-342">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="17d8e-343">浏览器请求的接受语言 HTTP 标题不是检测用户首选语言的可靠方法（请参阅 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)（在浏览器中设置首选项）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-343">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="17d8e-344">生产应用应包括一种用户可以自定义区域性选择的方法。</span><span class="sxs-lookup"><span data-stu-id="17d8e-344">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="17d8e-345">在 IE 中设置接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17d8e-345">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="17d8e-346">在齿轮图标中，点击“Internet 选项”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-346">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="17d8e-347">点击“语言”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-347">Tap **Languages**.</span></span>

    ![Internet 选项](localization/_static/lang.png)

3. <span data-ttu-id="17d8e-349">点击“设置语言首选项”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-349">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="17d8e-350">点击“添加语言”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-350">Tap **Add a language**.</span></span>

5. <span data-ttu-id="17d8e-351">添加语言。</span><span class="sxs-lookup"><span data-stu-id="17d8e-351">Add the language.</span></span>

6. <span data-ttu-id="17d8e-352">点击语言，然后点击“向上移动”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-352">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="17d8e-353">使用自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="17d8e-353">Use a custom provider</span></span>

<span data-ttu-id="17d8e-354">假设你想要让客户在数据库中存储其语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-354">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="17d8e-355">你可以编写一个提供程序来查找用户的这些值。</span><span class="sxs-lookup"><span data-stu-id="17d8e-355">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="17d8e-356">下面的代码演示如何添加自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="17d8e-356">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="17d8e-357">使用 `RequestLocalizationOptions` 添加或删除本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-357">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="17d8e-358">以编程方式设置区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-358">Set the culture programmatically</span></span>

<span data-ttu-id="17d8e-359">[GitHub](https://github.com/aspnet/entropy) 上的示例项目 Localization.StarterWeb 包含设置 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="17d8e-359">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="17d8e-360">Views/Shared/_SelectLanguagePartial.cshtml 文件允许你从支持的区域性列表中选择区域性：</span><span class="sxs-lookup"><span data-stu-id="17d8e-360">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="17d8e-361">Views/Shared/_SelectLanguagePartial.cshtml 文件添加到了布局文件的 `footer` 部分，使它将可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="17d8e-361">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="17d8e-362">`SetLanguage` 方法可设置区域性 Cookie。</span><span class="sxs-lookup"><span data-stu-id="17d8e-362">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="17d8e-363">不能将 _SelectLanguagePartial.cshtml 插入此项目的示例代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-363">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="17d8e-364">[GitHub](https://github.com/aspnet/entropy) 上的 Localization.StarterWeb 项目包含的代码可通过[依赖项注入](dependency-injection.md)容器将 `RequestLocalizationOptions` 流到 Razor 部分。</span><span class="sxs-lookup"><span data-stu-id="17d8e-364">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="17d8e-365">模型绑定路由数据和查询字符串</span><span class="sxs-lookup"><span data-stu-id="17d8e-365">Model binding route data and query strings</span></span>

<span data-ttu-id="17d8e-366">请参阅[模型绑定路由数据和查询字符串的全球化行为](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-366">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="17d8e-367">全球化和本地化术语</span><span class="sxs-lookup"><span data-stu-id="17d8e-367">Globalization and localization terms</span></span>

<span data-ttu-id="17d8e-368">本地化应用的过程还要求基本了解现代软件开发中常用的相关字符集，以及与之相关的问题。</span><span class="sxs-lookup"><span data-stu-id="17d8e-368">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="17d8e-369">尽管所有计算机将文本都存储为数字（代码），但不同的系统使用不同的数字存储相同的文本。</span><span class="sxs-lookup"><span data-stu-id="17d8e-369">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="17d8e-370">本地化过程是指针对特定区域性/区域设置转换应用的用户界面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-370">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="17d8e-371">[本地化性](/dotnet/standard/globalization-localization/localizability-review)是一个中间过程，用于验证全球化应用是否准备好进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-371">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="17d8e-372">区域性名称的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式为 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是语言代码，`<country/regioncode2>` 是子区域性代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-372">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="17d8e-373">例如，`es-CL` 表示西班牙语（智利），`en-US` 表示英语（美国），而 `en-AU` 表示英语（澳大利亚）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-373">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="17d8e-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是一个与语言相关的 ISO 639 双小写字母的区域性代码和一个与国家/地区相关的 ISO 3166 双大写字母子区域性代码的组合。</span><span class="sxs-lookup"><span data-stu-id="17d8e-374">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="17d8e-375">请参阅[语言区域性名称](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-375">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="17d8e-376">国际化常缩写为“I18N”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-376">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="17d8e-377">缩写采用第一个和最后一个字母以及它们之间的字母数，因此 18 代表第一个字母“I”和最后一个“N”之间的字母数。</span><span class="sxs-lookup"><span data-stu-id="17d8e-377">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="17d8e-378">这同样适用于全球化 (G11N) 和本地化 (L10N)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-378">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="17d8e-379">术语：</span><span class="sxs-lookup"><span data-stu-id="17d8e-379">Terms:</span></span>

* <span data-ttu-id="17d8e-380">全球化 (G11N)：使应用支持不同语言和区域的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-380">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="17d8e-381">本地化 (L10N)：针对给定语言和区域自定义应用的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-381">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="17d8e-382">国际化 (I18N)：介绍了全球化和本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-382">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="17d8e-383">区域性：它是一种语言和区域（可选）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-383">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="17d8e-384">非特定区域性：具有指定语言但不具有区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-384">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="17d8e-385">（例如，“en”，“es”）</span><span class="sxs-lookup"><span data-stu-id="17d8e-385">(for example "en", "es")</span></span>
* <span data-ttu-id="17d8e-386">特定区域性：具有指定语言和区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-386">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="17d8e-387">（例如，“en-US”，“en-GB”，“es-CL”）</span><span class="sxs-lookup"><span data-stu-id="17d8e-387">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="17d8e-388">父区域性：包含特定区域性的非特定区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-388">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="17d8e-389">（例如，“en”是“en-US”和“en-GB”的父区域性）</span><span class="sxs-lookup"><span data-stu-id="17d8e-389">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="17d8e-390">区域设置：区域设置与区域性相同。</span><span class="sxs-lookup"><span data-stu-id="17d8e-390">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="17d8e-391">其他资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-391">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="17d8e-392">本文所用的 [Localization.StarterWeb 项目](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-392">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="17d8e-393">对 .NET 应用程序进行全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-393">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="17d8e-394">.resx 文件中的资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-394">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="17d8e-395">Microsoft 多语言应用工具包</span><span class="sxs-lookup"><span data-stu-id="17d8e-395">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="17d8e-396">本地化与泛型</span><span class="sxs-lookup"><span data-stu-id="17d8e-396">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="17d8e-397">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="17d8e-397">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="17d8e-398">多语言网站使网站可以覆盖更广泛的受众。</span><span class="sxs-lookup"><span data-stu-id="17d8e-398">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="17d8e-399">ASP.NET Core 提供的服务和中间件可将网站本地化为不同的语言和文化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-399">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="17d8e-400">国际化涉及[全球化](/dotnet/api/system.globalization)和[本地化](/dotnet/standard/globalization-localization/localization)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-400">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="17d8e-401">全球化是设计支持不同区域性的应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-401">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="17d8e-402">全球化添加了对一组有关特定地理区域的已定义语言脚本的输入、显示和输出支持。</span><span class="sxs-lookup"><span data-stu-id="17d8e-402">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="17d8e-403">本地化是将已经针对可本地化性进行处理的全球化应用调整为特定的区域性/区域设置的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-403">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="17d8e-404">有关详细信息，请参阅本文档邻近末尾的全球化和本地化术语。</span><span class="sxs-lookup"><span data-stu-id="17d8e-404">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="17d8e-405">应用本地化涉及以下内容：</span><span class="sxs-lookup"><span data-stu-id="17d8e-405">App localization involves the following:</span></span>

1. <span data-ttu-id="17d8e-406">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-406">Make the app's content localizable</span></span>
1. <span data-ttu-id="17d8e-407">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-407">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="17d8e-408">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-408">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="17d8e-409">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="17d8e-409">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="17d8e-410">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-410">Make the app's content localizable</span></span>

<span data-ttu-id="17d8e-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer 不要求在资源文件中存储默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-411"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="17d8e-412">你可以开发针对本地化的应用，且无需在开发初期创建资源资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-412">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="17d8e-413">下面的代码演示如何针对本地化包装字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-413">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="17d8e-414">在前面的代码中，`IStringLocalizer<T>` 实现来源于[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-414">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="17d8e-415">如果找不到“About Title”的本地化值，则返回索引器键，即字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-415">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="17d8e-416">可将默认语言文本字符串保留在应用中并将它们包装在本地化工具中，以便你可集中精力开发应用。</span><span class="sxs-lookup"><span data-stu-id="17d8e-416">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="17d8e-417">你使用默认语言开发应用，并针对本地化步骤进行准备，而无需首先创建默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-417">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="17d8e-418">或者，你可以使用传统方法，并提供键以检索默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-418">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="17d8e-419">对于许多开发者而言，不具有默认语言 .resx 文件且简单包装字符串文本的新工作流可以减少本地化应用的开销。</span><span class="sxs-lookup"><span data-stu-id="17d8e-419">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="17d8e-420">其他开发者将首选传统工作流，因为它可以更轻松地使用较长字符串文本，更轻松地更新本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-420">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="17d8e-421">对包含 HTML 的资源使用 `IHtmlLocalizer<T>` 实现。</span><span class="sxs-lookup"><span data-stu-id="17d8e-421">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="17d8e-422">`IHtmlLocalizer` 对资源字符串中格式化的参数进行 HTML 编码，但不对资源字符串本身进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-422">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="17d8e-423">在下面突出显示的示例中，仅 `name` 参数的值被 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-423">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="17d8e-424">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17d8e-424">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="17d8e-425">最低程度，你可以从[依赖关系注入](dependency-injection.md)获取 `IStringLocalizerFactory`：</span><span class="sxs-lookup"><span data-stu-id="17d8e-425">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="17d8e-426">上面的代码演示了这两种工厂创建方法。</span><span class="sxs-lookup"><span data-stu-id="17d8e-426">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="17d8e-427">可以按控制器、区域对本地化字符串分区，或只有一个容器。</span><span class="sxs-lookup"><span data-stu-id="17d8e-427">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="17d8e-428">在示例应用中，名为 `SharedResource` 的虚拟类用于共享资源。</span><span class="sxs-lookup"><span data-stu-id="17d8e-428">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="17d8e-429">某些开发者使用 `Startup` 类，以包含全局或共享字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-429">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="17d8e-430">在下面的示例中，使用 `InfoController` 和 `SharedResource` 本地化工具：</span><span class="sxs-lookup"><span data-stu-id="17d8e-430">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="17d8e-431">视图本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-431">View localization</span></span>

<span data-ttu-id="17d8e-432">`IViewLocalizer` 服务可为[视图](xref:mvc/views/overview)提供本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-432">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="17d8e-433">`ViewLocalizer` 类可实现此接口，并从视图文件路径找到资源位置。</span><span class="sxs-lookup"><span data-stu-id="17d8e-433">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="17d8e-434">下面的代码演示如何使用 `IViewLocalizer` 的默认实现：</span><span class="sxs-lookup"><span data-stu-id="17d8e-434">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="17d8e-435">`IViewLocalizer` 的默认实现可根据视图的文件名查找资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-435">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="17d8e-436">没有使用全局共享资源文件的选项。</span><span class="sxs-lookup"><span data-stu-id="17d8e-436">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="17d8e-437">`ViewLocalizer` 使用 `IHtmlLocalizer` 实现本地化工具，因此 Razor 不会对本地化字符串进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-437">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="17d8e-438">你可以参数化资源字符串，`IViewLocalizer` 将对参数进行 HTML 编码，但不会对资源字符串进行。</span><span class="sxs-lookup"><span data-stu-id="17d8e-438">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="17d8e-439">请考虑以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="17d8e-439">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="17d8e-440">法语资源文件可以包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="17d8e-440">A French resource file could contain the following:</span></span>

| <span data-ttu-id="17d8e-441">键</span><span class="sxs-lookup"><span data-stu-id="17d8e-441">Key</span></span> | <span data-ttu-id="17d8e-442">“值”</span><span class="sxs-lookup"><span data-stu-id="17d8e-442">Value</span></span> |
| ----- | ---
<span data-ttu-id="17d8e-443">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-443">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-444">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-444">'Blazor'</span></span>
- <span data-ttu-id="17d8e-445">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-445">'Identity'</span></span>
- <span data-ttu-id="17d8e-446">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-446">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-447">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-447">'Razor'</span></span>
- <span data-ttu-id="17d8e-448">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-448">'SignalR' uid:</span></span> 

<span data-ttu-id="17d8e-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="17d8e-449">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="17d8e-450">呈现的视图可能包含资源文件中的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="17d8e-450">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="17d8e-451">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17d8e-451">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="17d8e-452">若要在视图中使用共享资源文件，请注入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="17d8e-452">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="17d8e-453">DataAnnotations 本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-453">DataAnnotations localization</span></span>

<span data-ttu-id="17d8e-454">DataAnnotations 错误消息已使用 `IStringLocalizer<T>` 本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-454">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="17d8e-455">使用选项 `ResourcesPath = "Resources"`，`RegisterViewModel` 中的错误消息可以存储在以下路径之一：</span><span class="sxs-lookup"><span data-stu-id="17d8e-455">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="17d8e-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-456">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="17d8e-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-457">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="17d8e-458">在 ASP.NET Core MVC 1.1.0 和更高版本中，非验证属性已经进行了本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-458">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="17d8e-459">ASP.NET Core MVC 1.0 不会为非验证属性查找本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-459">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="17d8e-460">对多个类使用一个资源字符串</span><span class="sxs-lookup"><span data-stu-id="17d8e-460">Using one resource string for multiple classes</span></span>

<span data-ttu-id="17d8e-461">下面的代码演示如何针对具有多个类的验证属性使用一个资源字符串：</span><span class="sxs-lookup"><span data-stu-id="17d8e-461">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="17d8e-462">在上面的代码中，`SharedResource` 是对应于存储验证消息的 resx 的类。</span><span class="sxs-lookup"><span data-stu-id="17d8e-462">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="17d8e-463">使用此方法，DataAnnotations 将仅使用 `SharedResource`，而不是每个类的资源。</span><span class="sxs-lookup"><span data-stu-id="17d8e-463">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="17d8e-464">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-464">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="17d8e-465">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="17d8e-465">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="17d8e-466">ASP.NET Core 允许指定两个区域性值，`SupportedCultures` 和 `SupportedUICultures`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-466">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="17d8e-467">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 对象可决定区域性相关函数的结果，如日期、时间、数字和货币格式等。</span><span class="sxs-lookup"><span data-stu-id="17d8e-467">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="17d8e-468">`SupportedCultures` 确定文本的排序顺序、大小写约定和字符串比较。</span><span class="sxs-lookup"><span data-stu-id="17d8e-468">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="17d8e-469">请参阅 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) 详细了解服务器如何获取区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-469">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="17d8e-470">`SupportedUICultures` 可确定按 [ResourceManager](/dotnet/api/system.resources.resourcemanager) 来查找哪些转换字符串（位于 .resx 文件）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-470">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="17d8e-471">`ResourceManager` 只需查找 `CurrentUICulture` 决定的区域性特定字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-471">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="17d8e-472">.NET 中的每个线程都具有 `CurrentCulture` 和 `CurrentUICulture` 对象。</span><span class="sxs-lookup"><span data-stu-id="17d8e-472">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="17d8e-473">呈现区域性相关函数时，ASP.NET Core 可检查这些值。</span><span class="sxs-lookup"><span data-stu-id="17d8e-473">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="17d8e-474">例如，如果当前线程的区域性设置为“en-US”（英语，美国），`DateTime.Now.ToLongDateString()` 将显示“Thursday, February 18, 2016”，但如果 `CurrentCulture` 设置为“es-ES”（西班牙语，西班牙），则输出将为“jueves，18 de febrero de 2016”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-474">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="17d8e-475">资源文件</span><span class="sxs-lookup"><span data-stu-id="17d8e-475">Resource files</span></span>

<span data-ttu-id="17d8e-476">资源文件是将可本地化的字符串与代码分离的有用机制。</span><span class="sxs-lookup"><span data-stu-id="17d8e-476">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="17d8e-477">非默认语言的转换字符串在 .resx 资源文件中单独显示。</span><span class="sxs-lookup"><span data-stu-id="17d8e-477">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="17d8e-478">例如，你可能想要创建包含转换字符串、名为 Welcome.es.resx 的西班牙语资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-478">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="17d8e-479">“es”是西班牙语的语言代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-479">"es" is the language code for Spanish.</span></span> <span data-ttu-id="17d8e-480">要在 Visual Studio 中创建此资源文件，请支持以下操作：</span><span class="sxs-lookup"><span data-stu-id="17d8e-480">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="17d8e-481">在“解决方案资源管理器”中，右键单击将包含资源文件的文件夹 >“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="17d8e-481">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![嵌套的上下文菜单：在“解决方案资源管理器”中，“资源”可打开上下文菜单。](localization/_static/newi.png)

2. <span data-ttu-id="17d8e-484">在“搜索已安装的模板”框中，输入“资源”并命名该文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-484">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![“添加新项”对话框](localization/_static/res.png)

3. <span data-ttu-id="17d8e-486">在“名称”列中输入键值（本机字符串），在“值”列中输入转换字符串 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-486">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx 文件（西班牙语版 Welcome 资源文件）的单词 Hello 位于“名称”列，Hola（西班牙语版 Hello）位于“值”列](localization/_static/hola.png)

    <span data-ttu-id="17d8e-488">Visual Studio 将显示 Welcome.es.resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-488">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![显示 Welcome Spanish (es) 资源文件的解决方案资源管理器](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="17d8e-490">资源文件命名</span><span class="sxs-lookup"><span data-stu-id="17d8e-490">Resource file naming</span></span>

<span data-ttu-id="17d8e-491">资源名称是类的完整类型名称减去程序集名称。</span><span class="sxs-lookup"><span data-stu-id="17d8e-491">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="17d8e-492">例如，类 `LocalizationWebsite.Web.Startup` 的主要程序集为 `LocalizationWebsite.Web.dll` 的项目中的法语资源将命名为 Startup.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-492">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="17d8e-493">类 `LocalizationWebsite.Web.Controllers.HomeController` 的资源将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-493">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-494">如果目标类的命名空间与将需要完整类型名称的程序集名称不同。</span><span class="sxs-lookup"><span data-stu-id="17d8e-494">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="17d8e-495">例如，在示例项目中，类型 `ExtraNamespace.Tools` 的资源将命名为 ExtraNamespace.Tools.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-495">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="17d8e-496">在示例项目中，`ConfigureServices` 方法将 `ResourcesPath` 设置为“资源”，因此主控制器的法语资源文件的项目相对路径是 Resources/Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-496">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-497">或者，你可以使用文件夹组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-497">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="17d8e-498">对于主控制器，该路径将为 Resources/Controllers/HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-498">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-499">如果不使用 `ResourcesPath` 选项，.resx 文件将转到项目的基目录中。</span><span class="sxs-lookup"><span data-stu-id="17d8e-499">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="17d8e-500">`HomeController` 的资源文件将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-500">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-501">是选择使用圆点还是路径命名约定，具体取决于你想如何组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-501">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="17d8e-502">资源名称</span><span class="sxs-lookup"><span data-stu-id="17d8e-502">Resource name</span></span> | <span data-ttu-id="17d8e-503">圆点或路径命名</span><span class="sxs-lookup"><span data-stu-id="17d8e-503">Dot or path naming</span></span> |
| ---
<span data-ttu-id="17d8e-504">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-504">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-505">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-505">'Blazor'</span></span>
- <span data-ttu-id="17d8e-506">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-506">'Identity'</span></span>
- <span data-ttu-id="17d8e-507">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-507">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-508">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-508">'Razor'</span></span>
- <span data-ttu-id="17d8e-509">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-509">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-510">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-510">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-511">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-511">'Blazor'</span></span>
- <span data-ttu-id="17d8e-512">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-512">'Identity'</span></span>
- <span data-ttu-id="17d8e-513">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-513">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-514">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-514">'Razor'</span></span>
- <span data-ttu-id="17d8e-515">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-515">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-516">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-516">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-517">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-517">'Blazor'</span></span>
- <span data-ttu-id="17d8e-518">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-518">'Identity'</span></span>
- <span data-ttu-id="17d8e-519">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-519">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-520">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-520">'Razor'</span></span>
- <span data-ttu-id="17d8e-521">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-521">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-522">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-522">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-523">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-523">'Blazor'</span></span>
- <span data-ttu-id="17d8e-524">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-524">'Identity'</span></span>
- <span data-ttu-id="17d8e-525">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-525">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-526">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-526">'Razor'</span></span>
- <span data-ttu-id="17d8e-527">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-527">'SignalR' uid:</span></span> 

<span data-ttu-id="17d8e-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-528">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-529">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-529">'Blazor'</span></span>
- <span data-ttu-id="17d8e-530">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-530">'Identity'</span></span>
- <span data-ttu-id="17d8e-531">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-531">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-532">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-532">'Razor'</span></span>
- <span data-ttu-id="17d8e-533">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-533">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-534">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-534">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-535">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-535">'Blazor'</span></span>
- <span data-ttu-id="17d8e-536">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-536">'Identity'</span></span>
- <span data-ttu-id="17d8e-537">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-537">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-538">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-538">'Razor'</span></span>
- <span data-ttu-id="17d8e-539">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-539">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-540">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-540">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-541">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-541">'Blazor'</span></span>
- <span data-ttu-id="17d8e-542">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-542">'Identity'</span></span>
- <span data-ttu-id="17d8e-543">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-543">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-544">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-544">'Razor'</span></span>
- <span data-ttu-id="17d8e-545">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-545">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-546">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-546">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-547">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-547">'Blazor'</span></span>
- <span data-ttu-id="17d8e-548">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-548">'Identity'</span></span>
- <span data-ttu-id="17d8e-549">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-549">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-550">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-550">'Razor'</span></span>
- <span data-ttu-id="17d8e-551">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-551">'SignalR' uid:</span></span> 

<span data-ttu-id="17d8e-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span><span class="sxs-lookup"><span data-stu-id="17d8e-552">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="17d8e-553">Razor 视图中使用 `@inject IViewLocalizer` 的资源文件遵循类似的模式。</span><span class="sxs-lookup"><span data-stu-id="17d8e-553">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="17d8e-554">可以使用圆点命名或路径命名约定对视图的资源文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="17d8e-554">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="17d8e-555"> 视图资源文件可模拟其关联视图文件的路径。</span><span class="sxs-lookup"><span data-stu-id="17d8e-555"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="17d8e-556">假设我们将 `ResourcesPath` 设置为“Resources”，与 *Views/Home/About.cshtml* 视图关联的法语资源文件可能是下面其中之一 ：</span><span class="sxs-lookup"><span data-stu-id="17d8e-556">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="17d8e-557">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17d8e-557">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="17d8e-558">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17d8e-558">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="17d8e-559">如果不使用 `ResourcesPath` 选项，视图的 .resx 文件将位于视图所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="17d8e-559">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="17d8e-560">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="17d8e-560">RootNamespaceAttribute</span></span> 

<span data-ttu-id="17d8e-561">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性在程序集的根命名空间不同于程序集名称时，提供程序集的根命名空间。</span><span class="sxs-lookup"><span data-stu-id="17d8e-561">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="17d8e-562">当项目名称不是有效的 .NET 标识符时，可能会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="17d8e-562">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="17d8e-563">例如，`my-project-name.csproj` 将使用根命名空间 `my_project_name` 和导致此错误的程序集名称 `my-project-name`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-563">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="17d8e-564">如果程序集的根命名空间不同于程序集名称：</span><span class="sxs-lookup"><span data-stu-id="17d8e-564">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="17d8e-565">默认情况下无法进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-565">Localization does not work by default.</span></span>
* <span data-ttu-id="17d8e-566">因程序集内搜索资源的方式导致本地化失败。</span><span class="sxs-lookup"><span data-stu-id="17d8e-566">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="17d8e-567">`RootNamespace` 是生成时间值，不可用于正在执行的进程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-567">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="17d8e-568">如果 `RootNamespace` 不同于 `AssemblyName`，请在 AssemblyInfo.cs 中包括以下内容（参数值替换为实际值）：</span><span class="sxs-lookup"><span data-stu-id="17d8e-568">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="17d8e-569">上述代码可成功解析 resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-569">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="17d8e-570">区域性回退行为</span><span class="sxs-lookup"><span data-stu-id="17d8e-570">Culture fallback behavior</span></span>

<span data-ttu-id="17d8e-571">在搜索资源时，本地化会进行“区域性回退”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-571">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="17d8e-572">从所请求的区域性开始，如果未能找到，则还原至该区域性的父区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-572">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="17d8e-573">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 属性代表父区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-573">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="17d8e-574">这通常（但并不是总是）意味着从 ISO 中移除区域签名。</span><span class="sxs-lookup"><span data-stu-id="17d8e-574">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="17d8e-575">例如，墨西哥的西班牙语方言为“es-MX”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-575">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="17d8e-576">它具备一个父级“es”西班牙，没有特别指定国家/地区。</span><span class="sxs-lookup"><span data-stu-id="17d8e-576">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="17d8e-577">假设你的站点接收到了一个区域性为“fr-CA”的“Welcome”资源的请求。</span><span class="sxs-lookup"><span data-stu-id="17d8e-577">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="17d8e-578">本地化系统按顺序查找以下资源，并选择第一个匹配项：</span><span class="sxs-lookup"><span data-stu-id="17d8e-578">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="17d8e-579">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-579">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="17d8e-580">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-580">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="17d8e-581">*Welcome.resx*（如果 `NeutralResourcesLanguage` 为“fr-CA”）</span><span class="sxs-lookup"><span data-stu-id="17d8e-581">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="17d8e-582">例如，如果删除了“.fr”区域性指示符，而且已将区域性设置为“法语”，将读取默认资源文件，并本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-582">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="17d8e-583">对于不满足所请求区域性的情况，资源管理器可指定默认资源或回退资源。</span><span class="sxs-lookup"><span data-stu-id="17d8e-583">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="17d8e-584">缺少适用于请求区域性的资源时，如果只想返回键，不得具有默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-584">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="17d8e-585">使用 Visual Studio 生成资源文件</span><span class="sxs-lookup"><span data-stu-id="17d8e-585">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="17d8e-586">如果在 Visual Studio 中创建文件名没有区域性的资源文件（例如 Welcome.resx），Visual Studio 将创建一个 C# 类，并且具有每个字符串的属性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-586">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="17d8e-587">这通常不是你想在 ASP.NET Core 中使用的。</span><span class="sxs-lookup"><span data-stu-id="17d8e-587">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="17d8e-588">你通常没有默认的 .resx 资源文件（没有区域性名称的 .resx 文件） 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-588">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="17d8e-589">建议创建具有区域性名称（例如 Welcome.fr.resx）的 .resx 文件 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-589">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="17d8e-590">创建具有区域性名称的 .resx 文件时，Visual Studio 不会生成类文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-590">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="17d8e-591">添加其他区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-591">Add other cultures</span></span>

<span data-ttu-id="17d8e-592">每个语言和区域性组合（除默认语言外）都需要唯一资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-592">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="17d8e-593">通过新建 ISO 语言代码属于名称一部分的资源文件，为不同的区域性和区域设置创建资源文件（例如，en-us、fr-ca 和 en-gb）  。</span><span class="sxs-lookup"><span data-stu-id="17d8e-593">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="17d8e-594">这些 ISO 编码位于文件名和 .resx 文件扩展之间，如 Welcome.es-MX.resx（西班牙语/墨西哥） 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-594">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="17d8e-595">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-595">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="17d8e-596">配置本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-596">Configure localization</span></span>

<span data-ttu-id="17d8e-597">通过 `Startup.ConfigureServices` 方法配置本地化：</span><span class="sxs-lookup"><span data-stu-id="17d8e-597">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="17d8e-598">`AddLocalization` 将本地化服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="17d8e-598">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="17d8e-599">上面的代码还将资源路径设置为“Resources”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-599">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="17d8e-600">`AddViewLocalization` 添加对本地化视图文件的支持。</span><span class="sxs-lookup"><span data-stu-id="17d8e-600">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="17d8e-601">在此示例视图中，本地化基于视图文件后缀。</span><span class="sxs-lookup"><span data-stu-id="17d8e-601">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="17d8e-602">例如，Index.fr.cshtml 文件中的“fr”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-602">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="17d8e-603">`AddDataAnnotationsLocalization` 添加通过 `IStringLocalizer` 抽象对本地化 `DataAnnotations` 验证消息的支持。</span><span class="sxs-lookup"><span data-stu-id="17d8e-603">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="17d8e-604">本地化中间件</span><span class="sxs-lookup"><span data-stu-id="17d8e-604">Localization middleware</span></span>

<span data-ttu-id="17d8e-605">在本地化[中间件](xref:fundamentals/middleware/index)中设置有关请求的当前区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-605">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="17d8e-606">在 `Startup.Configure` 方法中启用本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-606">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="17d8e-607">必须在中间件前面配置本地化中间件，它可能检查请求区域性（例如，`app.UseMvcWithDefaultRoute()`）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-607">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="17d8e-608">`UseRequestLocalization` 初始化 `RequestLocalizationOptions` 对象。</span><span class="sxs-lookup"><span data-stu-id="17d8e-608">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="17d8e-609">在每个请求上，枚举了 `RequestLocalizationOptions` 的 `RequestCultureProvider` 列表，使用了可成功决定请求区域性的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-609">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="17d8e-610">默认提供程序来自 `RequestLocalizationOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="17d8e-610">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="17d8e-611">默认列表从最具体到最不具体排序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-611">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="17d8e-612">在本文的后面部分，我们将了解如何更改顺序，甚至添加一个自定义区域性提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-612">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="17d8e-613">如果没有一个提供程序可以确定请求区域性，则使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-613">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="17d8e-614">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17d8e-614">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="17d8e-615">某些应用将使用查询字符串来设置[区域性和 UI 区域性](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-615">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="17d8e-616">对于使用 Cookie 或接受语言标题方法的应用，向 URL 添加查询字符串有助于调试和测试代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-616">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="17d8e-617">默认情况下，`QueryStringRequestCultureProvider` 注册为 `RequestCultureProvider` 列表中的第一个本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-617">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="17d8e-618">传递查询字符串参数 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-618">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="17d8e-619">下面的示例将特定区域性（语言和区域）设置为“西班牙语/墨西哥”：</span><span class="sxs-lookup"><span data-stu-id="17d8e-619">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="17d8e-620">如果仅传入两种区域性之一（`culture` 或 `ui-culture`，查询字符串提供程序将使用你传入的区域性设置这两个值。</span><span class="sxs-lookup"><span data-stu-id="17d8e-620">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="17d8e-621">例如，仅设置区域性将同时设置 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="17d8e-621">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="17d8e-622">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17d8e-622">CookieRequestCultureProvider</span></span>

<span data-ttu-id="17d8e-623">通常，生产应用将提供一种机制来使用 ASP.NET Core 区域性 Cookie 设置区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-623">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="17d8e-624">若要创建 Cookie，请使用 `MakeCookieValue` 方法。</span><span class="sxs-lookup"><span data-stu-id="17d8e-624">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="17d8e-625">`CookieRequestCultureProvider``DefaultCookieName` 将返回用来跟踪用户首选区域性信息的默认 Cookie 名称。</span><span class="sxs-lookup"><span data-stu-id="17d8e-625">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="17d8e-626">默认 Cookie 名称是 `.AspNetCore.Culture`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-626">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="17d8e-627">Cookie 格式为 `c=%LANGCODE%|uic=%LANGCODE%`，其中`c` 是 `Culture`，`uic` 是 `UICulture`，例如：</span><span class="sxs-lookup"><span data-stu-id="17d8e-627">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="17d8e-628">如果仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将同时用于区域性信息和 UI 区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-628">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="17d8e-629">接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17d8e-629">The Accept-Language HTTP header</span></span>

<span data-ttu-id="17d8e-630">[接受语言标题](https://www.w3.org/International/questions/qa-accept-lang-locales)在大多数浏览器中可设置，最初用于指定用户的语言。</span><span class="sxs-lookup"><span data-stu-id="17d8e-630">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="17d8e-631">此设置指示浏览器已设置为发送或已从基础操作系统继承的内容。</span><span class="sxs-lookup"><span data-stu-id="17d8e-631">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="17d8e-632">浏览器请求的接受语言 HTTP 标题不是检测用户首选语言的可靠方法（请参阅 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)（在浏览器中设置首选项）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-632">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="17d8e-633">生产应用应包括一种用户可以自定义区域性选择的方法。</span><span class="sxs-lookup"><span data-stu-id="17d8e-633">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="17d8e-634">在 IE 中设置接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17d8e-634">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="17d8e-635">在齿轮图标中，点击“Internet 选项”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-635">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="17d8e-636">点击“语言”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-636">Tap **Languages**.</span></span>

    ![Internet 选项](localization/_static/lang.png)

3. <span data-ttu-id="17d8e-638">点击“设置语言首选项”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-638">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="17d8e-639">点击“添加语言”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-639">Tap **Add a language**.</span></span>

5. <span data-ttu-id="17d8e-640">添加语言。</span><span class="sxs-lookup"><span data-stu-id="17d8e-640">Add the language.</span></span>

6. <span data-ttu-id="17d8e-641">点击语言，然后点击“向上移动”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-641">Tap the language, then tap **Move Up**.</span></span>

### <a name="use-a-custom-provider"></a><span data-ttu-id="17d8e-642">使用自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="17d8e-642">Use a custom provider</span></span>

<span data-ttu-id="17d8e-643">假设你想要让客户在数据库中存储其语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-643">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="17d8e-644">你可以编写一个提供程序来查找用户的这些值。</span><span class="sxs-lookup"><span data-stu-id="17d8e-644">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="17d8e-645">下面的代码演示如何添加自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="17d8e-645">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="17d8e-646">使用 `RequestLocalizationOptions` 添加或删除本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-646">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="17d8e-647">以编程方式设置区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-647">Set the culture programmatically</span></span>

<span data-ttu-id="17d8e-648">[GitHub](https://github.com/aspnet/entropy) 上的示例项目 Localization.StarterWeb 包含设置 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="17d8e-648">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="17d8e-649">Views/Shared/_SelectLanguagePartial.cshtml 文件允许你从支持的区域性列表中选择区域性：</span><span class="sxs-lookup"><span data-stu-id="17d8e-649">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="17d8e-650">Views/Shared/_SelectLanguagePartial.cshtml 文件添加到了布局文件的 `footer` 部分，使它将可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="17d8e-650">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="17d8e-651">`SetLanguage` 方法可设置区域性 Cookie。</span><span class="sxs-lookup"><span data-stu-id="17d8e-651">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="17d8e-652">不能将 _SelectLanguagePartial.cshtml 插入此项目的示例代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-652">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="17d8e-653">[GitHub](https://github.com/aspnet/entropy) 上的 Localization.StarterWeb 项目包含的代码可通过[依赖项注入](dependency-injection.md)容器将 `RequestLocalizationOptions` 流到 Razor 部分。</span><span class="sxs-lookup"><span data-stu-id="17d8e-653">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="17d8e-654">模型绑定路由数据和查询字符串</span><span class="sxs-lookup"><span data-stu-id="17d8e-654">Model binding route data and query strings</span></span>

<span data-ttu-id="17d8e-655">请参阅[模型绑定路由数据和查询字符串的全球化行为](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-655">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="17d8e-656">全球化和本地化术语</span><span class="sxs-lookup"><span data-stu-id="17d8e-656">Globalization and localization terms</span></span>

<span data-ttu-id="17d8e-657">本地化应用的过程还要求基本了解现代软件开发中常用的相关字符集，以及与之相关的问题。</span><span class="sxs-lookup"><span data-stu-id="17d8e-657">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="17d8e-658">尽管所有计算机将文本都存储为数字（代码），但不同的系统使用不同的数字存储相同的文本。</span><span class="sxs-lookup"><span data-stu-id="17d8e-658">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="17d8e-659">本地化过程是指针对特定区域性/区域设置转换应用的用户界面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-659">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="17d8e-660">[本地化性](/dotnet/standard/globalization-localization/localizability-review)是一个中间过程，用于验证全球化应用是否准备好进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-660">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="17d8e-661">区域性名称的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式为 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是语言代码，`<country/regioncode2>` 是子区域性代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-661">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="17d8e-662">例如，`es-CL` 表示西班牙语（智利），`en-US` 表示英语（美国），而 `en-AU` 表示英语（澳大利亚）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-662">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="17d8e-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是一个与语言相关的 ISO 639 双小写字母的区域性代码和一个与国家/地区相关的 ISO 3166 双大写字母子区域性代码的组合。</span><span class="sxs-lookup"><span data-stu-id="17d8e-663">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="17d8e-664">请参阅[语言区域性名称](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-664">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="17d8e-665">国际化常缩写为“I18N”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-665">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="17d8e-666">缩写采用第一个和最后一个字母以及它们之间的字母数，因此 18 代表第一个字母“I”和最后一个“N”之间的字母数。</span><span class="sxs-lookup"><span data-stu-id="17d8e-666">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="17d8e-667">这同样适用于全球化 (G11N) 和本地化 (L10N)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-667">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="17d8e-668">术语：</span><span class="sxs-lookup"><span data-stu-id="17d8e-668">Terms:</span></span>

* <span data-ttu-id="17d8e-669">全球化 (G11N)：使应用支持不同语言和区域的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-669">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="17d8e-670">本地化 (L10N)：针对给定语言和区域自定义应用的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-670">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="17d8e-671">国际化 (I18N)：介绍了全球化和本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-671">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="17d8e-672">区域性：它是一种语言和区域（可选）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-672">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="17d8e-673">非特定区域性：具有指定语言但不具有区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-673">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="17d8e-674">（例如，“en”，“es”）</span><span class="sxs-lookup"><span data-stu-id="17d8e-674">(for example "en", "es")</span></span>
* <span data-ttu-id="17d8e-675">特定区域性：具有指定语言和区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-675">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="17d8e-676">（例如，“en-US”，“en-GB”，“es-CL”）</span><span class="sxs-lookup"><span data-stu-id="17d8e-676">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="17d8e-677">父区域性：包含特定区域性的非特定区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-677">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="17d8e-678">（例如，“en”是“en-US”和“en-GB”的父区域性）</span><span class="sxs-lookup"><span data-stu-id="17d8e-678">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="17d8e-679">区域设置：区域设置与区域性相同。</span><span class="sxs-lookup"><span data-stu-id="17d8e-679">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

## <a name="additional-resources"></a><span data-ttu-id="17d8e-680">其他资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-680">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="17d8e-681">本文所用的 [Localization.StarterWeb 项目](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-681">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="17d8e-682">对 .NET 应用程序进行全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-682">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="17d8e-683">.resx 文件中的资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-683">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="17d8e-684">Microsoft 多语言应用工具包</span><span class="sxs-lookup"><span data-stu-id="17d8e-684">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="17d8e-685">本地化与泛型</span><span class="sxs-lookup"><span data-stu-id="17d8e-685">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end

<!-- ASP.NET Core 5.x starts here -->
::: moniker range="> aspnetcore-3.1"

<span data-ttu-id="17d8e-686">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Damien Bowden](https://twitter.com/damien_bod)、[Bart Calixto](https://twitter.com/bartmax)、[Nadeem Afana](https://afana.me/) 和 [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span><span class="sxs-lookup"><span data-stu-id="17d8e-686">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Damien Bowden](https://twitter.com/damien_bod), [Bart Calixto](https://twitter.com/bartmax), [Nadeem Afana](https://afana.me/), and [Hisham Bin Ateya](https://twitter.com/hishambinateya)</span></span>

<span data-ttu-id="17d8e-687">多语言网站使网站可以覆盖更广泛的受众。</span><span class="sxs-lookup"><span data-stu-id="17d8e-687">A multilingual website allows the site to reach a wider audience.</span></span> <span data-ttu-id="17d8e-688">ASP.NET Core 提供的服务和中间件可将网站本地化为不同的语言和文化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-688">ASP.NET Core provides services and middleware for localizing into different languages and cultures.</span></span>

<span data-ttu-id="17d8e-689">国际化涉及[全球化](/dotnet/api/system.globalization)和[本地化](/dotnet/standard/globalization-localization/localization)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-689">Internationalization involves [Globalization](/dotnet/api/system.globalization) and [Localization](/dotnet/standard/globalization-localization/localization).</span></span> <span data-ttu-id="17d8e-690">全球化是设计支持不同区域性的应用程序的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-690">Globalization is the process of designing apps that support different cultures.</span></span> <span data-ttu-id="17d8e-691">全球化添加了对一组有关特定地理区域的已定义语言脚本的输入、显示和输出支持。</span><span class="sxs-lookup"><span data-stu-id="17d8e-691">Globalization adds support for input, display, and output of a defined set of language scripts that relate to specific geographic areas.</span></span>

<span data-ttu-id="17d8e-692">本地化是将已经针对可本地化性进行处理的全球化应用调整为特定的区域性/区域设置的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-692">Localization is the process of adapting a globalized app, which you have already processed for localizability, to a particular culture/locale.</span></span> <span data-ttu-id="17d8e-693">有关详细信息，请参阅本文档邻近末尾的全球化和本地化术语。</span><span class="sxs-lookup"><span data-stu-id="17d8e-693">For more information see **Globalization and localization terms** near the end of this document.</span></span>

<span data-ttu-id="17d8e-694">应用本地化涉及以下内容：</span><span class="sxs-lookup"><span data-stu-id="17d8e-694">App localization involves the following:</span></span>

1. <span data-ttu-id="17d8e-695">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-695">Make the app's content localizable</span></span>
1. <span data-ttu-id="17d8e-696">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-696">Provide localized resources for the languages and cultures you support</span></span>
1. <span data-ttu-id="17d8e-697">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-697">Implement a strategy to select the language/culture for each request</span></span>

<span data-ttu-id="17d8e-698">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="17d8e-698">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/Localization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="make-the-apps-content-localizable"></a><span data-ttu-id="17d8e-699">使应用内容可本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-699">Make the app's content localizable</span></span>

<span data-ttu-id="17d8e-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer 不要求在资源文件中存储默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-700"><xref:Microsoft.Extensions.Localization.IStringLocalizer>` and <xref:Microsoft.Extensions.Localization.IStringLocalizer%601> were architected to improve productivity when developing localized apps. `IStringLocalizer` uses the [ResourceManager](/dotnet/api/system.resources.resourcemanager) and [ResourceReader](/dotnet/api/system.resources.resourcereader) to provide culture-specific resources at run time. The interface has an indexer and an `IEnumerable` for returning localized strings. `IStringLocalizer\` doesn't require storing the default language strings in a resource file.</span></span> <span data-ttu-id="17d8e-701">你可以开发针对本地化的应用，且无需在开发初期创建资源资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-701">You can develop an app targeted for localization and not need to create resource files early in development.</span></span> <span data-ttu-id="17d8e-702">下面的代码演示如何针对本地化包装字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-702">The code below shows how to wrap the string "About Title" for localization.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/AboutController.cs)]

<span data-ttu-id="17d8e-703">在前面的代码中，`IStringLocalizer<T>` 实现来源于[依赖关系注入](dependency-injection.md)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-703">In the preceding code, the `IStringLocalizer<T>` implementation comes from [Dependency Injection](dependency-injection.md).</span></span> <span data-ttu-id="17d8e-704">如果找不到“About Title”的本地化值，则返回索引器键，即字符串“About Title”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-704">If the localized value of "About Title" isn't found, then the indexer key is returned, that is, the string "About Title".</span></span> <span data-ttu-id="17d8e-705">可将默认语言文本字符串保留在应用中并将它们包装在本地化工具中，以便你可集中精力开发应用。</span><span class="sxs-lookup"><span data-stu-id="17d8e-705">You can leave the default language literal strings in the app and wrap them in the localizer, so that you can focus on developing the app.</span></span> <span data-ttu-id="17d8e-706">你使用默认语言开发应用，并针对本地化步骤进行准备，而无需首先创建默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-706">You develop your app with your default language and prepare it for the localization step without first creating a default resource file.</span></span> <span data-ttu-id="17d8e-707">或者，你可以使用传统方法，并提供键以检索默认语言字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-707">Alternatively, you can use the traditional approach and provide a key to retrieve the default language string.</span></span> <span data-ttu-id="17d8e-708">对于许多开发者而言，不具有默认语言 .resx 文件且简单包装字符串文本的新工作流可以减少本地化应用的开销。</span><span class="sxs-lookup"><span data-stu-id="17d8e-708">For many developers the new workflow of not having a default language *.resx* file and simply wrapping the string literals can reduce the overhead of localizing an app.</span></span> <span data-ttu-id="17d8e-709">其他开发者将首选传统工作流，因为它可以更轻松地使用较长字符串文本，更轻松地更新本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-709">Other developers will prefer the traditional work flow as it can make it easier to work with longer string literals and make it easier to update localized strings.</span></span>

<span data-ttu-id="17d8e-710">对包含 HTML 的资源使用 `IHtmlLocalizer<T>` 实现。</span><span class="sxs-lookup"><span data-stu-id="17d8e-710">Use the `IHtmlLocalizer<T>` implementation for resources that contain HTML.</span></span> <span data-ttu-id="17d8e-711">`IHtmlLocalizer` 对资源字符串中格式化的参数进行 HTML 编码，但不对资源字符串本身进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-711">`IHtmlLocalizer` HTML encodes arguments that are formatted in the resource string, but doesn't HTML encode the resource string itself.</span></span> <span data-ttu-id="17d8e-712">在下面突出显示的示例中，仅 `name` 参数的值被 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-712">In the sample highlighted below, only the value of `name` parameter is HTML encoded.</span></span>

[!code-csharp[](../fundamentals/localization/sample/Localization/Controllers/BookController.cs?highlight=3,5,20&start=1&end=24)]

<span data-ttu-id="17d8e-713">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17d8e-713">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="17d8e-714">最低程度，你可以从[依赖关系注入](dependency-injection.md)获取 `IStringLocalizerFactory`：</span><span class="sxs-lookup"><span data-stu-id="17d8e-714">At the lowest level, you can get `IStringLocalizerFactory` out of [Dependency Injection](dependency-injection.md):</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/TestController.cs?start=9&end=26&highlight=7-13)]

<span data-ttu-id="17d8e-715">上面的代码演示了这两种工厂创建方法。</span><span class="sxs-lookup"><span data-stu-id="17d8e-715">The code above demonstrates each of the two factory create methods.</span></span>

<span data-ttu-id="17d8e-716">可以按控制器、区域对本地化字符串分区，或只有一个容器。</span><span class="sxs-lookup"><span data-stu-id="17d8e-716">You can partition your localized strings by controller, area, or have just one container.</span></span> <span data-ttu-id="17d8e-717">在示例应用中，名为 `SharedResource` 的虚拟类用于共享资源。</span><span class="sxs-lookup"><span data-stu-id="17d8e-717">In the sample app, a dummy class named `SharedResource` is used for shared resources.</span></span>

[!code-csharp[](localization/sample/Localization/Resources/SharedResource.cs)]

<span data-ttu-id="17d8e-718">某些开发者使用 `Startup` 类，以包含全局或共享字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-718">Some developers use the `Startup` class to contain global or shared strings.</span></span> <span data-ttu-id="17d8e-719">在下面的示例中，使用 `InfoController` 和 `SharedResource` 本地化工具：</span><span class="sxs-lookup"><span data-stu-id="17d8e-719">In the sample below, the `InfoController` and the `SharedResource` localizers are used:</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/InfoController.cs?range=9-26)]

## <a name="view-localization"></a><span data-ttu-id="17d8e-720">视图本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-720">View localization</span></span>

<span data-ttu-id="17d8e-721">`IViewLocalizer` 服务可为[视图](xref:mvc/views/overview)提供本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-721">The `IViewLocalizer` service provides localized strings for a [view](xref:mvc/views/overview).</span></span> <span data-ttu-id="17d8e-722">`ViewLocalizer` 类可实现此接口，并从视图文件路径找到资源位置。</span><span class="sxs-lookup"><span data-stu-id="17d8e-722">The `ViewLocalizer` class implements this interface and finds the resource location from the view file path.</span></span> <span data-ttu-id="17d8e-723">下面的代码演示如何使用 `IViewLocalizer` 的默认实现：</span><span class="sxs-lookup"><span data-stu-id="17d8e-723">The following code shows how to use the default implementation of `IViewLocalizer`:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Home/About.cshtml)]

<span data-ttu-id="17d8e-724">`IViewLocalizer` 的默认实现可根据视图的文件名查找资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-724">The default implementation of `IViewLocalizer` finds the resource file based on the view's file name.</span></span> <span data-ttu-id="17d8e-725">没有使用全局共享资源文件的选项。</span><span class="sxs-lookup"><span data-stu-id="17d8e-725">There's no option to use a global shared resource file.</span></span> <span data-ttu-id="17d8e-726">`ViewLocalizer` 使用 `IHtmlLocalizer` 实现本地化工具，因此 Razor 不会对本地化字符串进行 HTML 编码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-726">`ViewLocalizer` implements the localizer using `IHtmlLocalizer`, so Razor doesn't HTML encode the localized string.</span></span> <span data-ttu-id="17d8e-727">你可以参数化资源字符串，`IViewLocalizer` 将对参数进行 HTML 编码，但不会对资源字符串进行。</span><span class="sxs-lookup"><span data-stu-id="17d8e-727">You can parameterize resource strings and `IViewLocalizer` will HTML encode the parameters, but not the resource string.</span></span> <span data-ttu-id="17d8e-728">请考虑以下 Razor 标记：</span><span class="sxs-lookup"><span data-stu-id="17d8e-728">Consider the following Razor markup:</span></span>

```cshtml
@Localizer["<i>Hello</i> <b>{0}!</b>", UserManager.GetUserName(User)]
```

<span data-ttu-id="17d8e-729">法语资源文件可以包含以下信息：</span><span class="sxs-lookup"><span data-stu-id="17d8e-729">A French resource file could contain the following:</span></span>

| <span data-ttu-id="17d8e-730">键</span><span class="sxs-lookup"><span data-stu-id="17d8e-730">Key</span></span> | <span data-ttu-id="17d8e-731">“值”</span><span class="sxs-lookup"><span data-stu-id="17d8e-731">Value</span></span> |
| ----- | ---
<span data-ttu-id="17d8e-732">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-732">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-733">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-733">'Blazor'</span></span>
- <span data-ttu-id="17d8e-734">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-734">'Identity'</span></span>
- <span data-ttu-id="17d8e-735">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-735">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-736">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-736">'Razor'</span></span>
- <span data-ttu-id="17d8e-737">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-737">'SignalR' uid:</span></span> 

<span data-ttu-id="17d8e-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span><span class="sxs-lookup"><span data-stu-id="17d8e-738">--- | | `<i>Hello</i> <b>{0}!</b>` | `<i>Bonjour</i> <b>{0} !</b>` |</span></span>

<span data-ttu-id="17d8e-739">呈现的视图可能包含资源文件中的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="17d8e-739">The rendered view would contain the HTML markup from the resource file.</span></span>

<span data-ttu-id="17d8e-740">**注意：** 通常只想要本地化文本，而不是 HTML。</span><span class="sxs-lookup"><span data-stu-id="17d8e-740">**Note:** You generally want to only localize text and not HTML.</span></span>

<span data-ttu-id="17d8e-741">若要在视图中使用共享资源文件，请注入 `IHtmlLocalizer<T>`：</span><span class="sxs-lookup"><span data-stu-id="17d8e-741">To use a shared resource file in a view, inject `IHtmlLocalizer<T>`:</span></span>

[!code-cshtml[](../fundamentals/localization/sample/Localization/Views/Test/About.cshtml?highlight=5,12)]

## <a name="dataannotations-localization"></a><span data-ttu-id="17d8e-742">DataAnnotations 本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-742">DataAnnotations localization</span></span>

<span data-ttu-id="17d8e-743">DataAnnotations 错误消息已使用 `IStringLocalizer<T>` 本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-743">DataAnnotations error messages are localized with `IStringLocalizer<T>`.</span></span> <span data-ttu-id="17d8e-744">使用选项 `ResourcesPath = "Resources"`，`RegisterViewModel` 中的错误消息可以存储在以下路径之一：</span><span class="sxs-lookup"><span data-stu-id="17d8e-744">Using the option `ResourcesPath = "Resources"`, the error messages in `RegisterViewModel` can be stored in either of the following paths:</span></span>

* <span data-ttu-id="17d8e-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-745">*Resources/ViewModels.Account.RegisterViewModel.fr.resx*</span></span>
* <span data-ttu-id="17d8e-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-746">*Resources/ViewModels/Account/RegisterViewModel.fr.resx*</span></span>

[!code-csharp[](localization/sample/Localization/ViewModels/Account/RegisterViewModel.cs?start=9&end=26)]

<span data-ttu-id="17d8e-747">在 ASP.NET Core MVC 1.1.0 和更高版本中，非验证属性已经进行了本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-747">In ASP.NET Core MVC 1.1.0 and higher, non-validation attributes are localized.</span></span> <span data-ttu-id="17d8e-748">ASP.NET Core MVC 1.0 不会为非验证属性查找本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-748">ASP.NET Core MVC 1.0 does **not** look up localized strings for non-validation attributes.</span></span>

<a name="one-resource-string-multiple-classes"></a>

### <a name="using-one-resource-string-for-multiple-classes"></a><span data-ttu-id="17d8e-749">对多个类使用一个资源字符串</span><span class="sxs-lookup"><span data-stu-id="17d8e-749">Using one resource string for multiple classes</span></span>

<span data-ttu-id="17d8e-750">下面的代码演示如何针对具有多个类的验证属性使用一个资源字符串：</span><span class="sxs-lookup"><span data-stu-id="17d8e-750">The following code shows how to use one resource string for validation attributes with multiple classes:</span></span>

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

<span data-ttu-id="17d8e-751">在上面的代码中，`SharedResource` 是对应于存储验证消息的 resx 的类。</span><span class="sxs-lookup"><span data-stu-id="17d8e-751">In the preceding code, `SharedResource` is the class corresponding to the resx where your validation messages are stored.</span></span> <span data-ttu-id="17d8e-752">使用此方法，DataAnnotations 将仅使用 `SharedResource`，而不是每个类的资源。</span><span class="sxs-lookup"><span data-stu-id="17d8e-752">With this approach, DataAnnotations will only use `SharedResource`, rather than the resource for each class.</span></span>

## <a name="provide-localized-resources-for-the-languages-and-cultures-you-support"></a><span data-ttu-id="17d8e-753">为支持的语言和区域性提供本地化资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-753">Provide localized resources for the languages and cultures you support</span></span>

### <a name="supportedcultures-and-supporteduicultures"></a><span data-ttu-id="17d8e-754">SupportedCultures 和 SupportedUICultures</span><span class="sxs-lookup"><span data-stu-id="17d8e-754">SupportedCultures and SupportedUICultures</span></span>

<span data-ttu-id="17d8e-755">ASP.NET Core 允许指定两个区域性值，`SupportedCultures` 和 `SupportedUICultures`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-755">ASP.NET Core allows you to specify two culture values, `SupportedCultures` and `SupportedUICultures`.</span></span> <span data-ttu-id="17d8e-756">`SupportedCultures` 的 [CultureInfo](/dotnet/api/system.globalization.cultureinfo) 对象可决定区域性相关函数的结果，如日期、时间、数字和货币格式等。</span><span class="sxs-lookup"><span data-stu-id="17d8e-756">The [CultureInfo](/dotnet/api/system.globalization.cultureinfo) object for `SupportedCultures` determines the results of culture-dependent functions, such as date, time, number, and currency formatting.</span></span> <span data-ttu-id="17d8e-757">`SupportedCultures` 确定文本的排序顺序、大小写约定和字符串比较。</span><span class="sxs-lookup"><span data-stu-id="17d8e-757">`SupportedCultures` also determines the sorting order of text, casing conventions, and string comparisons.</span></span> <span data-ttu-id="17d8e-758">请参阅 [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) 详细了解服务器如何获取区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-758">See [CultureInfo.CurrentCulture](/dotnet/api/system.stringcomparer.currentculture#System_StringComparer_CurrentCulture) for more info on how the server gets the Culture.</span></span> <span data-ttu-id="17d8e-759">`SupportedUICultures` 可确定按 [ResourceManager](/dotnet/api/system.resources.resourcemanager) 来查找哪些转换字符串（位于 .resx 文件）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-759">The `SupportedUICultures` determines which translated strings (from *.resx* files) are looked up by the [ResourceManager](/dotnet/api/system.resources.resourcemanager).</span></span> <span data-ttu-id="17d8e-760">`ResourceManager` 只需查找 `CurrentUICulture` 决定的区域性特定字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-760">The `ResourceManager` simply looks up culture-specific strings that's determined by `CurrentUICulture`.</span></span> <span data-ttu-id="17d8e-761">.NET 中的每个线程都具有 `CurrentCulture` 和 `CurrentUICulture` 对象。</span><span class="sxs-lookup"><span data-stu-id="17d8e-761">Every thread in .NET has `CurrentCulture` and `CurrentUICulture` objects.</span></span> <span data-ttu-id="17d8e-762">呈现区域性相关函数时，ASP.NET Core 可检查这些值。</span><span class="sxs-lookup"><span data-stu-id="17d8e-762">ASP.NET Core inspects these values when rendering culture-dependent functions.</span></span> <span data-ttu-id="17d8e-763">例如，如果当前线程的区域性设置为“en-US”（英语，美国），`DateTime.Now.ToLongDateString()` 将显示“Thursday, February 18, 2016”，但如果 `CurrentCulture` 设置为“es-ES”（西班牙语，西班牙），则输出将为“jueves，18 de febrero de 2016”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-763">For example, if the current thread's culture is set to "en-US" (English, United States), `DateTime.Now.ToLongDateString()` displays "Thursday, February 18, 2016", but if `CurrentCulture` is set to "es-ES" (Spanish, Spain) the output will be "jueves, 18 de febrero de 2016".</span></span>

## <a name="resource-files"></a><span data-ttu-id="17d8e-764">资源文件</span><span class="sxs-lookup"><span data-stu-id="17d8e-764">Resource files</span></span>

<span data-ttu-id="17d8e-765">资源文件是将可本地化的字符串与代码分离的有用机制。</span><span class="sxs-lookup"><span data-stu-id="17d8e-765">A resource file is a useful mechanism for separating localizable strings from code.</span></span> <span data-ttu-id="17d8e-766">非默认语言的转换字符串在 .resx 资源文件中单独显示。</span><span class="sxs-lookup"><span data-stu-id="17d8e-766">Translated strings for the non-default language are isolated in *.resx* resource files.</span></span> <span data-ttu-id="17d8e-767">例如，你可能想要创建包含转换字符串、名为 Welcome.es.resx 的西班牙语资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-767">For example, you might want to create Spanish resource file named *Welcome.es.resx* containing translated strings.</span></span> <span data-ttu-id="17d8e-768">“es”是西班牙语的语言代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-768">"es" is the language code for Spanish.</span></span> <span data-ttu-id="17d8e-769">要在 Visual Studio 中创建此资源文件，请支持以下操作：</span><span class="sxs-lookup"><span data-stu-id="17d8e-769">To create this resource file in Visual Studio:</span></span>

1. <span data-ttu-id="17d8e-770">在“解决方案资源管理器”中，右键单击将包含资源文件的文件夹 >“添加”>“新项”  。</span><span class="sxs-lookup"><span data-stu-id="17d8e-770">In **Solution Explorer**, right click on the folder which will contain the resource file > **Add** > **New Item**.</span></span>

    ![嵌套的上下文菜单：在“解决方案资源管理器”中，“资源”可打开上下文菜单。](localization/_static/newi.png)

2. <span data-ttu-id="17d8e-773">在“搜索已安装的模板”框中，输入“资源”并命名该文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-773">In the **Search installed templates** box, enter "resource" and name the file.</span></span>

    ![“添加新项”对话框](localization/_static/res.png)

3. <span data-ttu-id="17d8e-775">在“名称”列中输入键值（本机字符串），在“值”列中输入转换字符串 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-775">Enter the key value (native string) in the **Name** column and the translated string in the **Value** column.</span></span>

    ![Welcome.es.resx 文件（西班牙语版 Welcome 资源文件）的单词 Hello 位于“名称”列，Hola（西班牙语版 Hello）位于“值”列](localization/_static/hola.png)

    <span data-ttu-id="17d8e-777">Visual Studio 将显示 Welcome.es.resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-777">Visual Studio shows the *Welcome.es.resx* file.</span></span>

    ![显示 Welcome Spanish (es) 资源文件的解决方案资源管理器](localization/_static/se.png)

## <a name="resource-file-naming"></a><span data-ttu-id="17d8e-779">资源文件命名</span><span class="sxs-lookup"><span data-stu-id="17d8e-779">Resource file naming</span></span>

<span data-ttu-id="17d8e-780">资源名称是类的完整类型名称减去程序集名称。</span><span class="sxs-lookup"><span data-stu-id="17d8e-780">Resources are named for the full type name of their class minus the assembly name.</span></span> <span data-ttu-id="17d8e-781">例如，类 `LocalizationWebsite.Web.Startup` 的主要程序集为 `LocalizationWebsite.Web.dll` 的项目中的法语资源将命名为 Startup.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-781">For example, a French resource in a project whose main assembly is `LocalizationWebsite.Web.dll` for the class `LocalizationWebsite.Web.Startup` would be named *Startup.fr.resx*.</span></span> <span data-ttu-id="17d8e-782">类 `LocalizationWebsite.Web.Controllers.HomeController` 的资源将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-782">A resource for the class `LocalizationWebsite.Web.Controllers.HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-783">如果目标类的命名空间与将需要完整类型名称的程序集名称不同。</span><span class="sxs-lookup"><span data-stu-id="17d8e-783">If your targeted class's namespace isn't the same as the assembly name you will need the full type name.</span></span> <span data-ttu-id="17d8e-784">例如，在示例项目中，类型 `ExtraNamespace.Tools` 的资源将命名为 ExtraNamespace.Tools.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-784">For example, in the sample project a resource for the type `ExtraNamespace.Tools` would be named *ExtraNamespace.Tools.fr.resx*.</span></span>

<span data-ttu-id="17d8e-785">在示例项目中，`ConfigureServices` 方法将 `ResourcesPath` 设置为“资源”，因此主控制器的法语资源文件的项目相对路径是 Resources/Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-785">In the sample project, the `ConfigureServices` method sets the `ResourcesPath` to "Resources", so the project relative path for the home controller's French resource file is *Resources/Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-786">或者，你可以使用文件夹组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-786">Alternatively, you can use folders to organize resource files.</span></span> <span data-ttu-id="17d8e-787">对于主控制器，该路径将为 Resources/Controllers/HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-787">For the home controller, the path would be *Resources/Controllers/HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-788">如果不使用 `ResourcesPath` 选项，.resx 文件将转到项目的基目录中。</span><span class="sxs-lookup"><span data-stu-id="17d8e-788">If you don't use the `ResourcesPath` option, the *.resx* file would go in the project base directory.</span></span> <span data-ttu-id="17d8e-789">`HomeController` 的资源文件将命名为 Controllers.HomeController.fr.resx。</span><span class="sxs-lookup"><span data-stu-id="17d8e-789">The resource file for `HomeController` would be named *Controllers.HomeController.fr.resx*.</span></span> <span data-ttu-id="17d8e-790">是选择使用圆点还是路径命名约定，具体取决于你想如何组织资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-790">The choice of using the dot or path naming convention depends on how you want to organize your resource files.</span></span>

| <span data-ttu-id="17d8e-791">资源名称</span><span class="sxs-lookup"><span data-stu-id="17d8e-791">Resource name</span></span> | <span data-ttu-id="17d8e-792">圆点或路径命名</span><span class="sxs-lookup"><span data-stu-id="17d8e-792">Dot or path naming</span></span> |
| ---
<span data-ttu-id="17d8e-793">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-793">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-794">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-794">'Blazor'</span></span>
- <span data-ttu-id="17d8e-795">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-795">'Identity'</span></span>
- <span data-ttu-id="17d8e-796">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-796">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-797">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-797">'Razor'</span></span>
- <span data-ttu-id="17d8e-798">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-798">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-799">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-799">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-800">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-800">'Blazor'</span></span>
- <span data-ttu-id="17d8e-801">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-801">'Identity'</span></span>
- <span data-ttu-id="17d8e-802">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-802">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-803">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-803">'Razor'</span></span>
- <span data-ttu-id="17d8e-804">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-804">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-805">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-805">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-806">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-806">'Blazor'</span></span>
- <span data-ttu-id="17d8e-807">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-807">'Identity'</span></span>
- <span data-ttu-id="17d8e-808">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-808">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-809">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-809">'Razor'</span></span>
- <span data-ttu-id="17d8e-810">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-810">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-811">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-811">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-812">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-812">'Blazor'</span></span>
- <span data-ttu-id="17d8e-813">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-813">'Identity'</span></span>
- <span data-ttu-id="17d8e-814">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-814">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-815">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-815">'Razor'</span></span>
- <span data-ttu-id="17d8e-816">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-816">'SignalR' uid:</span></span> 

<span data-ttu-id="17d8e-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-817">------   | --- title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-818">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-818">'Blazor'</span></span>
- <span data-ttu-id="17d8e-819">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-819">'Identity'</span></span>
- <span data-ttu-id="17d8e-820">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-820">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-821">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-821">'Razor'</span></span>
- <span data-ttu-id="17d8e-822">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-822">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-823">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-823">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-824">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-824">'Blazor'</span></span>
- <span data-ttu-id="17d8e-825">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-825">'Identity'</span></span>
- <span data-ttu-id="17d8e-826">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-826">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-827">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-827">'Razor'</span></span>
- <span data-ttu-id="17d8e-828">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-828">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-829">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-829">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-830">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-830">'Blazor'</span></span>
- <span data-ttu-id="17d8e-831">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-831">'Identity'</span></span>
- <span data-ttu-id="17d8e-832">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-832">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-833">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-833">'Razor'</span></span>
- <span data-ttu-id="17d8e-834">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-834">'SignalR' uid:</span></span> 

-
<span data-ttu-id="17d8e-835">title: author: description: ms.author: ms.date: no-loc:</span><span class="sxs-lookup"><span data-stu-id="17d8e-835">title: author: description: ms.author: ms.date: no-loc:</span></span>
- <span data-ttu-id="17d8e-836">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-836">'Blazor'</span></span>
- <span data-ttu-id="17d8e-837">'Identity'</span><span class="sxs-lookup"><span data-stu-id="17d8e-837">'Identity'</span></span>
- <span data-ttu-id="17d8e-838">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="17d8e-838">'Let's Encrypt'</span></span>
- <span data-ttu-id="17d8e-839">'Razor'</span><span class="sxs-lookup"><span data-stu-id="17d8e-839">'Razor'</span></span>
- <span data-ttu-id="17d8e-840">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="17d8e-840">'SignalR' uid:</span></span> 

<span data-ttu-id="17d8e-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span><span class="sxs-lookup"><span data-stu-id="17d8e-841">------- | | Resources/Controllers.HomeController.fr.resx | Dot  | | Resources/Controllers/HomeController.fr.resx  | Path | |    |     |</span></span>

<span data-ttu-id="17d8e-842">Razor 视图中使用 `@inject IViewLocalizer` 的资源文件遵循类似的模式。</span><span class="sxs-lookup"><span data-stu-id="17d8e-842">Resource files using `@inject IViewLocalizer` in Razor views follow a similar pattern.</span></span> <span data-ttu-id="17d8e-843">可以使用圆点命名或路径命名约定对视图的资源文件进行命名。</span><span class="sxs-lookup"><span data-stu-id="17d8e-843">The resource file for a view can be named using either dot naming or path naming.</span></span> Razor<span data-ttu-id="17d8e-844"> 视图资源文件可模拟其关联视图文件的路径。</span><span class="sxs-lookup"><span data-stu-id="17d8e-844"> view resource files mimic the path of their associated view file.</span></span> <span data-ttu-id="17d8e-845">假设我们将 `ResourcesPath` 设置为“Resources”，与 *Views/Home/About.cshtml* 视图关联的法语资源文件可能是下面其中之一 ：</span><span class="sxs-lookup"><span data-stu-id="17d8e-845">Assuming we set the `ResourcesPath` to "Resources", the French resource file associated with the *Views/Home/About.cshtml* view could be either of the following:</span></span>

* <span data-ttu-id="17d8e-846">Resources/Views/Home/About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17d8e-846">Resources/Views/Home/About.fr.resx</span></span>

* <span data-ttu-id="17d8e-847">Resources/Views.Home.About.fr.resx</span><span class="sxs-lookup"><span data-stu-id="17d8e-847">Resources/Views.Home.About.fr.resx</span></span>

<span data-ttu-id="17d8e-848">如果不使用 `ResourcesPath` 选项，视图的 .resx 文件将位于视图所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="17d8e-848">If you don't use the `ResourcesPath` option, the *.resx* file for a view would be located in the same folder as the view.</span></span>

### <a name="rootnamespaceattribute"></a><span data-ttu-id="17d8e-849">RootNamespaceAttribute</span><span class="sxs-lookup"><span data-stu-id="17d8e-849">RootNamespaceAttribute</span></span> 

<span data-ttu-id="17d8e-850">[RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) 属性在程序集的根命名空间不同于程序集名称时，提供程序集的根命名空间。</span><span class="sxs-lookup"><span data-stu-id="17d8e-850">The [RootNamespace](/dotnet/api/microsoft.extensions.localization.rootnamespaceattribute?view=aspnetcore-2.1) attribute provides the root namespace of an assembly when the root namespace of an assembly is different than the assembly name.</span></span> 

> [!WARNING]
> <span data-ttu-id="17d8e-851">当项目名称不是有效的 .NET 标识符时，可能会发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="17d8e-851">This can occur when a project's name is not a valid .NET identifier.</span></span> <span data-ttu-id="17d8e-852">例如，`my-project-name.csproj` 将使用根命名空间 `my_project_name` 和导致此错误的程序集名称 `my-project-name`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-852">For instance `my-project-name.csproj` will use the root namespace `my_project_name` and the assembly name `my-project-name` leading to this error.</span></span> 

<span data-ttu-id="17d8e-853">如果程序集的根命名空间不同于程序集名称：</span><span class="sxs-lookup"><span data-stu-id="17d8e-853">If the root namespace of an assembly is different than the assembly name:</span></span>

* <span data-ttu-id="17d8e-854">默认情况下无法进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-854">Localization does not work by default.</span></span>
* <span data-ttu-id="17d8e-855">因程序集内搜索资源的方式导致本地化失败。</span><span class="sxs-lookup"><span data-stu-id="17d8e-855">Localization fails due to the way resources are searched for within the assembly.</span></span> <span data-ttu-id="17d8e-856">`RootNamespace` 是生成时间值，不可用于正在执行的进程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-856">`RootNamespace` is a build-time value which is not available to the executing process.</span></span> 

<span data-ttu-id="17d8e-857">如果 `RootNamespace` 不同于 `AssemblyName`，请在 AssemblyInfo.cs 中包括以下内容（参数值替换为实际值）：</span><span class="sxs-lookup"><span data-stu-id="17d8e-857">If the `RootNamespace` is different from the `AssemblyName`, include the following in *AssemblyInfo.cs* (with parameter values replaced with the actual values):</span></span>

```csharp
using System.Reflection;
using Microsoft.Extensions.Localization;

[assembly: ResourceLocation("Resource Folder Name")]
[assembly: RootNamespace("App Root Namespace")]
```

<span data-ttu-id="17d8e-858">上述代码可成功解析 resx 文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-858">The preceding code enables the successful resolution of resx files.</span></span>

## <a name="culture-fallback-behavior"></a><span data-ttu-id="17d8e-859">区域性回退行为</span><span class="sxs-lookup"><span data-stu-id="17d8e-859">Culture fallback behavior</span></span>

<span data-ttu-id="17d8e-860">在搜索资源时，本地化会进行“区域性回退”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-860">When searching for a resource, localization engages in "culture fallback".</span></span> <span data-ttu-id="17d8e-861">从所请求的区域性开始，如果未能找到，则还原至该区域性的父区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-861">Starting from the requested culture, if not found, it reverts to the parent culture of that culture.</span></span> <span data-ttu-id="17d8e-862">另外，[CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) 属性代表父区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-862">As an aside, the [CultureInfo.Parent](/dotnet/api/system.globalization.cultureinfo.parent) property represents the parent culture.</span></span> <span data-ttu-id="17d8e-863">这通常（但并不是总是）意味着从 ISO 中移除区域签名。</span><span class="sxs-lookup"><span data-stu-id="17d8e-863">This usually (but not always) means removing the national signifier from the ISO.</span></span> <span data-ttu-id="17d8e-864">例如，墨西哥的西班牙语方言为“es-MX”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-864">For example, the dialect of Spanish spoken in Mexico is "es-MX".</span></span> <span data-ttu-id="17d8e-865">它具备一个父级“es”西班牙，没有特别指定国家/地区。</span><span class="sxs-lookup"><span data-stu-id="17d8e-865">It has the parent "es"&mdash;Spanish non-specific to any country.</span></span>

<span data-ttu-id="17d8e-866">假设你的站点接收到了一个区域性为“fr-CA”的“Welcome”资源的请求。</span><span class="sxs-lookup"><span data-stu-id="17d8e-866">Imagine your site receives a request for a "Welcome" resource using culture "fr-CA".</span></span> <span data-ttu-id="17d8e-867">本地化系统按顺序查找以下资源，并选择第一个匹配项：</span><span class="sxs-lookup"><span data-stu-id="17d8e-867">The localization system looks for the following resources, in order, and selects the first match:</span></span>

* <span data-ttu-id="17d8e-868">*Welcome.fr-CA.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-868">*Welcome.fr-CA.resx*</span></span>
* <span data-ttu-id="17d8e-869">*Welcome.fr.resx*</span><span class="sxs-lookup"><span data-stu-id="17d8e-869">*Welcome.fr.resx*</span></span>
* <span data-ttu-id="17d8e-870">*Welcome.resx*（如果 `NeutralResourcesLanguage` 为“fr-CA”）</span><span class="sxs-lookup"><span data-stu-id="17d8e-870">*Welcome.resx* (if the `NeutralResourcesLanguage` is "fr-CA")</span></span>

<span data-ttu-id="17d8e-871">例如，如果删除了“.fr”区域性指示符，而且已将区域性设置为“法语”，将读取默认资源文件，并本地化字符串。</span><span class="sxs-lookup"><span data-stu-id="17d8e-871">As an example, if you remove the ".fr" culture designator and you have the culture set to French, the default resource file is read and strings are localized.</span></span> <span data-ttu-id="17d8e-872">对于不满足所请求区域性的情况，资源管理器可指定默认资源或回退资源。</span><span class="sxs-lookup"><span data-stu-id="17d8e-872">The Resource manager designates a default or fallback resource for when nothing meets your requested culture.</span></span> <span data-ttu-id="17d8e-873">缺少适用于请求区域性的资源时，如果只想返回键，不得具有默认资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-873">If you want to just return the key when missing a resource for the requested culture you must not have a default resource file.</span></span>

### <a name="generate-resource-files-with-visual-studio"></a><span data-ttu-id="17d8e-874">使用 Visual Studio 生成资源文件</span><span class="sxs-lookup"><span data-stu-id="17d8e-874">Generate resource files with Visual Studio</span></span>

<span data-ttu-id="17d8e-875">如果在 Visual Studio 中创建文件名没有区域性的资源文件（例如 Welcome.resx），Visual Studio 将创建一个 C# 类，并且具有每个字符串的属性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-875">If you create a resource file in Visual Studio without a culture in the file name (for example, *Welcome.resx*), Visual Studio will create a C# class with a property for each string.</span></span> <span data-ttu-id="17d8e-876">这通常不是你想在 ASP.NET Core 中使用的。</span><span class="sxs-lookup"><span data-stu-id="17d8e-876">That's usually not what you want with ASP.NET Core.</span></span> <span data-ttu-id="17d8e-877">你通常没有默认的 .resx 资源文件（没有区域性名称的 .resx 文件） 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-877">You typically don't have a default *.resx* resource file (a *.resx* file without the culture name).</span></span> <span data-ttu-id="17d8e-878">建议创建具有区域性名称（例如 Welcome.fr.resx）的 .resx 文件 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-878">We suggest you create the *.resx* file with a culture name (for example *Welcome.fr.resx*).</span></span> <span data-ttu-id="17d8e-879">创建具有区域性名称的 .resx 文件时，Visual Studio 不会生成类文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-879">When you create a *.resx* file with a culture name, Visual Studio won't generate the class file.</span></span>

### <a name="add-other-cultures"></a><span data-ttu-id="17d8e-880">添加其他区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-880">Add other cultures</span></span>

<span data-ttu-id="17d8e-881">每个语言和区域性组合（除默认语言外）都需要唯一资源文件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-881">Each language and culture combination (other than the default language) requires a unique resource file.</span></span> <span data-ttu-id="17d8e-882">通过新建 ISO 语言代码属于名称一部分的资源文件，为不同的区域性和区域设置创建资源文件（例如，en-us、fr-ca 和 en-gb）  。</span><span class="sxs-lookup"><span data-stu-id="17d8e-882">You create resource files for different cultures and locales by creating new resource files in which the ISO language codes are part of the file name (for example, **en-us**, **fr-ca**, and **en-gb**).</span></span> <span data-ttu-id="17d8e-883">这些 ISO 编码位于文件名和 .resx 文件扩展之间，如 Welcome.es-MX.resx（西班牙语/墨西哥） 。</span><span class="sxs-lookup"><span data-stu-id="17d8e-883">These ISO codes are placed between the file name and the *.resx* file extension, as in *Welcome.es-MX.resx* (Spanish/Mexico).</span></span>

## <a name="implement-a-strategy-to-select-the-languageculture-for-each-request"></a><span data-ttu-id="17d8e-884">实施策略，为每个请求选择语言/区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-884">Implement a strategy to select the language/culture for each request</span></span>

### <a name="configure-localization"></a><span data-ttu-id="17d8e-885">配置本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-885">Configure localization</span></span>

<span data-ttu-id="17d8e-886">通过 `Startup.ConfigureServices` 方法配置本地化：</span><span class="sxs-lookup"><span data-stu-id="17d8e-886">Localization is configured in the `Startup.ConfigureServices` method:</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet1)]

* <span data-ttu-id="17d8e-887">`AddLocalization` 将本地化服务添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="17d8e-887">`AddLocalization` Adds the localization services to the services container.</span></span> <span data-ttu-id="17d8e-888">上面的代码还将资源路径设置为“Resources”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-888">The code above also sets the resources path to "Resources".</span></span>

* <span data-ttu-id="17d8e-889">`AddViewLocalization` 添加对本地化视图文件的支持。</span><span class="sxs-lookup"><span data-stu-id="17d8e-889">`AddViewLocalization` Adds support for localized view files.</span></span> <span data-ttu-id="17d8e-890">在此示例视图中，本地化基于视图文件后缀。</span><span class="sxs-lookup"><span data-stu-id="17d8e-890">In this sample view localization is based on the view file suffix.</span></span> <span data-ttu-id="17d8e-891">例如，Index.fr.cshtml 文件中的“fr”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-891">For example "fr" in the *Index.fr.cshtml* file.</span></span>

* <span data-ttu-id="17d8e-892">`AddDataAnnotationsLocalization` 添加通过 `IStringLocalizer` 抽象对本地化 `DataAnnotations` 验证消息的支持。</span><span class="sxs-lookup"><span data-stu-id="17d8e-892">`AddDataAnnotationsLocalization` Adds support for localized `DataAnnotations` validation messages through `IStringLocalizer` abstractions.</span></span>

### <a name="localization-middleware"></a><span data-ttu-id="17d8e-893">本地化中间件</span><span class="sxs-lookup"><span data-stu-id="17d8e-893">Localization middleware</span></span>

<span data-ttu-id="17d8e-894">在本地化[中间件](xref:fundamentals/middleware/index)中设置有关请求的当前区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-894">The current culture on a request is set in the localization [Middleware](xref:fundamentals/middleware/index).</span></span> <span data-ttu-id="17d8e-895">在 `Startup.Configure` 方法中启用本地化中间件。</span><span class="sxs-lookup"><span data-stu-id="17d8e-895">The localization middleware is enabled in the `Startup.Configure` method.</span></span> <span data-ttu-id="17d8e-896">必须在中间件前面配置本地化中间件，它可能检查请求区域性（例如，`app.UseMvcWithDefaultRoute()`）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-896">The localization middleware must be configured before any middleware which might check the request culture (for example, `app.UseMvcWithDefaultRoute()`).</span></span>

[!code-csharp[](localization/sample/Localization/Startup.cs?name=snippet2)]
[!INCLUDE[about the series](~/includes/code-comments-loc.md)]

<span data-ttu-id="17d8e-897">`UseRequestLocalization` 初始化 `RequestLocalizationOptions` 对象。</span><span class="sxs-lookup"><span data-stu-id="17d8e-897">`UseRequestLocalization` initializes a `RequestLocalizationOptions` object.</span></span> <span data-ttu-id="17d8e-898">在每个请求上，枚举了 `RequestLocalizationOptions` 的 `RequestCultureProvider` 列表，使用了可成功决定请求区域性的第一个提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-898">On every request the list of `RequestCultureProvider` in the `RequestLocalizationOptions` is enumerated and the first provider that can successfully determine the request culture is used.</span></span> <span data-ttu-id="17d8e-899">默认提供程序来自 `RequestLocalizationOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="17d8e-899">The default providers come from the `RequestLocalizationOptions` class:</span></span>

1. `QueryStringRequestCultureProvider`
2. `CookieRequestCultureProvider`
3. `AcceptLanguageHeaderRequestCultureProvider`

<span data-ttu-id="17d8e-900">默认列表从最具体到最不具体排序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-900">The default list goes from most specific to least specific.</span></span> <span data-ttu-id="17d8e-901">在本文的后面部分，我们将了解如何更改顺序，甚至添加一个自定义区域性提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-901">Later in the article we'll see how you can change the order and even add a custom culture provider.</span></span> <span data-ttu-id="17d8e-902">如果没有一个提供程序可以确定请求区域性，则使用 `DefaultRequestCulture`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-902">If none of the providers can determine the request culture, the `DefaultRequestCulture` is used.</span></span>

### <a name="querystringrequestcultureprovider"></a><span data-ttu-id="17d8e-903">QueryStringRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17d8e-903">QueryStringRequestCultureProvider</span></span>

<span data-ttu-id="17d8e-904">某些应用将使用查询字符串来设置[区域性和 UI 区域性](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-904">Some apps will use a query string to set the [culture and UI culture](https://msdn.microsoft.com/library/system.globalization.cultureinfo.aspx).</span></span> <span data-ttu-id="17d8e-905">对于使用 Cookie 或接受语言标题方法的应用，向 URL 添加查询字符串有助于调试和测试代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-905">For apps that use the cookie or Accept-Language header approach, adding a query string to the URL is useful for debugging and testing code.</span></span> <span data-ttu-id="17d8e-906">默认情况下，`QueryStringRequestCultureProvider` 注册为 `RequestCultureProvider` 列表中的第一个本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-906">By default, the `QueryStringRequestCultureProvider` is registered as the first localization provider in the `RequestCultureProvider` list.</span></span> <span data-ttu-id="17d8e-907">传递查询字符串参数 `culture` 和 `ui-culture`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-907">You pass the query string parameters `culture` and `ui-culture`.</span></span> <span data-ttu-id="17d8e-908">下面的示例将特定区域性（语言和区域）设置为“西班牙语/墨西哥”：</span><span class="sxs-lookup"><span data-stu-id="17d8e-908">The following example sets the specific culture (language and region) to Spanish/Mexico:</span></span>

   `http://localhost:5000/?culture=es-MX&ui-culture=es-MX`

<span data-ttu-id="17d8e-909">如果仅传入两种区域性之一（`culture` 或 `ui-culture`，查询字符串提供程序将使用你传入的区域性设置这两个值。</span><span class="sxs-lookup"><span data-stu-id="17d8e-909">If you only pass in one of the two (`culture` or `ui-culture`), the query string provider will set both values using the one you passed in.</span></span> <span data-ttu-id="17d8e-910">例如，仅设置区域性将同时设置 `Culture` 和 `UICulture`：</span><span class="sxs-lookup"><span data-stu-id="17d8e-910">For example, setting just the culture will set both the `Culture` and the `UICulture`:</span></span>

   `http://localhost:5000/?culture=es-MX`

### <a name="cookierequestcultureprovider"></a><span data-ttu-id="17d8e-911">CookieRequestCultureProvider</span><span class="sxs-lookup"><span data-stu-id="17d8e-911">CookieRequestCultureProvider</span></span>

<span data-ttu-id="17d8e-912">通常，生产应用将提供一种机制来使用 ASP.NET Core 区域性 Cookie 设置区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-912">Production apps will often provide a mechanism to set the culture with the ASP.NET Core culture cookie.</span></span> <span data-ttu-id="17d8e-913">若要创建 Cookie，请使用 `MakeCookieValue` 方法。</span><span class="sxs-lookup"><span data-stu-id="17d8e-913">Use the `MakeCookieValue` method to create a cookie.</span></span>

<span data-ttu-id="17d8e-914">`CookieRequestCultureProvider``DefaultCookieName` 将返回用来跟踪用户首选区域性信息的默认 Cookie 名称。</span><span class="sxs-lookup"><span data-stu-id="17d8e-914">The `CookieRequestCultureProvider` `DefaultCookieName` returns the default cookie name used to track the user's preferred culture information.</span></span> <span data-ttu-id="17d8e-915">默认 Cookie 名称是 `.AspNetCore.Culture`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-915">The default cookie name is `.AspNetCore.Culture`.</span></span>

<span data-ttu-id="17d8e-916">Cookie 格式为 `c=%LANGCODE%|uic=%LANGCODE%`，其中`c` 是 `Culture`，`uic` 是 `UICulture`，例如：</span><span class="sxs-lookup"><span data-stu-id="17d8e-916">The cookie format is `c=%LANGCODE%|uic=%LANGCODE%`, where `c` is `Culture` and `uic` is `UICulture`, for example:</span></span>

    c=en-UK|uic=en-US

<span data-ttu-id="17d8e-917">如果仅指定其中一个区域性信息和 UI 区域性，则指定的区域性将同时用于区域性信息和 UI 区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-917">If you only specify one of culture info and UI culture, the specified culture will be used for both culture info and UI culture.</span></span>

### <a name="the-accept-language-http-header"></a><span data-ttu-id="17d8e-918">接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17d8e-918">The Accept-Language HTTP header</span></span>

<span data-ttu-id="17d8e-919">[接受语言标题](https://www.w3.org/International/questions/qa-accept-lang-locales)在大多数浏览器中可设置，最初用于指定用户的语言。</span><span class="sxs-lookup"><span data-stu-id="17d8e-919">The [Accept-Language header](https://www.w3.org/International/questions/qa-accept-lang-locales) is settable in most browsers and was originally intended to specify the user's language.</span></span> <span data-ttu-id="17d8e-920">此设置指示浏览器已设置为发送或已从基础操作系统继承的内容。</span><span class="sxs-lookup"><span data-stu-id="17d8e-920">This setting indicates what the browser has been set to send or has inherited from the underlying operating system.</span></span> <span data-ttu-id="17d8e-921">浏览器请求的接受语言 HTTP 标题不是检测用户首选语言的可靠方法（请参阅 [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)（在浏览器中设置首选项）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-921">The Accept-Language HTTP header from a browser request isn't an infallible way to detect the user's preferred language (see [Setting language preferences in a browser](https://www.w3.org/International/questions/qa-lang-priorities.en.php)).</span></span> <span data-ttu-id="17d8e-922">生产应用应包括一种用户可以自定义区域性选择的方法。</span><span class="sxs-lookup"><span data-stu-id="17d8e-922">A production app should include a way for a user to customize their choice of culture.</span></span>

### <a name="set-the-accept-language-http-header-in-ie"></a><span data-ttu-id="17d8e-923">在 IE 中设置接受语言 HTTP 标题</span><span class="sxs-lookup"><span data-stu-id="17d8e-923">Set the Accept-Language HTTP header in IE</span></span>

1. <span data-ttu-id="17d8e-924">在齿轮图标中，点击“Internet 选项”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-924">From the gear icon, tap **Internet Options**.</span></span>

2. <span data-ttu-id="17d8e-925">点击“语言”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-925">Tap **Languages**.</span></span>

    ![Internet 选项](localization/_static/lang.png)

3. <span data-ttu-id="17d8e-927">点击“设置语言首选项”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-927">Tap **Set Language Preferences**.</span></span>

4. <span data-ttu-id="17d8e-928">点击“添加语言”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-928">Tap **Add a language**.</span></span>

5. <span data-ttu-id="17d8e-929">添加语言。</span><span class="sxs-lookup"><span data-stu-id="17d8e-929">Add the language.</span></span>

6. <span data-ttu-id="17d8e-930">点击语言，然后点击“向上移动”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-930">Tap the language, then tap **Move Up**.</span></span>

### <a name="the-content-language-http-header"></a><span data-ttu-id="17d8e-931">Content-Language HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="17d8e-931">The Content-Language HTTP header</span></span>

<span data-ttu-id="17d8e-932">[Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) 实体标头：</span><span class="sxs-lookup"><span data-stu-id="17d8e-932">The [Content-Language](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Language) entity header:</span></span>

 - <span data-ttu-id="17d8e-933">用于描述面向受众的语言。</span><span class="sxs-lookup"><span data-stu-id="17d8e-933">Is used to describe the language(s) intended for the audience.</span></span>
 - <span data-ttu-id="17d8e-934">允许用户根据用户的首选语言来区分。</span><span class="sxs-lookup"><span data-stu-id="17d8e-934">Allows a user to differentiate according to the users' own preferred language.</span></span>

<span data-ttu-id="17d8e-935">实体标头用于 HTTP 请求和响应。</span><span class="sxs-lookup"><span data-stu-id="17d8e-935">Entity headers are used in both HTTP requests and responses.</span></span>

<span data-ttu-id="17d8e-936">可通过设置属性 `ApplyCurrentCultureToResponseHeaders` 来添加 `Content-Language` 标头。</span><span class="sxs-lookup"><span data-stu-id="17d8e-936">The `Content-Language` header can be added by setting the property `ApplyCurrentCultureToResponseHeaders`.</span></span>

<span data-ttu-id="17d8e-937">添加 `Content-Language` 标头：</span><span class="sxs-lookup"><span data-stu-id="17d8e-937">Adding the `Content-Language` header:</span></span>

 - <span data-ttu-id="17d8e-938">允许 RequestLocalizationMiddleware 使用 `CurrentUICulture` 设置 `Content-Language` 标头。</span><span class="sxs-lookup"><span data-stu-id="17d8e-938">Allows the RequestLocalizationMiddleware to set the `Content-Language` header with the `CurrentUICulture`.</span></span>
 - <span data-ttu-id="17d8e-939">无需显式设置响应标头 `Content-Language`。</span><span class="sxs-lookup"><span data-stu-id="17d8e-939">Eliminates the need to set the response header `Content-Language` explicitly.</span></span>

```csharp
app.UseRequestLocalization(new RequestLocalizationOptions
{
    ApplyCurrentCultureToResponseHeaders = true
});
```

### <a name="use-a-custom-provider"></a><span data-ttu-id="17d8e-940">使用自定义提供程序</span><span class="sxs-lookup"><span data-stu-id="17d8e-940">Use a custom provider</span></span>

<span data-ttu-id="17d8e-941">假设你想要让客户在数据库中存储其语言和区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-941">Suppose you want to let your customers store their language and culture in your databases.</span></span> <span data-ttu-id="17d8e-942">你可以编写一个提供程序来查找用户的这些值。</span><span class="sxs-lookup"><span data-stu-id="17d8e-942">You could write a provider to look up these values for the user.</span></span> <span data-ttu-id="17d8e-943">下面的代码演示如何添加自定义提供程序：</span><span class="sxs-lookup"><span data-stu-id="17d8e-943">The following code shows how to add a custom provider:</span></span>

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

<span data-ttu-id="17d8e-944">使用 `RequestLocalizationOptions` 添加或删除本地化提供程序。</span><span class="sxs-lookup"><span data-stu-id="17d8e-944">Use `RequestLocalizationOptions` to add or remove localization providers.</span></span>

### <a name="set-the-culture-programmatically"></a><span data-ttu-id="17d8e-945">以编程方式设置区域性</span><span class="sxs-lookup"><span data-stu-id="17d8e-945">Set the culture programmatically</span></span>

<span data-ttu-id="17d8e-946">[GitHub](https://github.com/aspnet/entropy) 上的示例项目 Localization.StarterWeb 包含设置 `Culture` 的 UI。</span><span class="sxs-lookup"><span data-stu-id="17d8e-946">This sample **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) contains UI to set the `Culture`.</span></span> <span data-ttu-id="17d8e-947">Views/Shared/_SelectLanguagePartial.cshtml 文件允许你从支持的区域性列表中选择区域性：</span><span class="sxs-lookup"><span data-stu-id="17d8e-947">The *Views/Shared/_SelectLanguagePartial.cshtml* file allows you to select the culture from the list of supported cultures:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_SelectLanguagePartial.cshtml)]

<span data-ttu-id="17d8e-948">Views/Shared/_SelectLanguagePartial.cshtml 文件添加到了布局文件的 `footer` 部分，使它将可供所有视图使用：</span><span class="sxs-lookup"><span data-stu-id="17d8e-948">The *Views/Shared/_SelectLanguagePartial.cshtml* file is added to the `footer` section of the layout file so it will be available to all views:</span></span>

[!code-cshtml[](localization/sample/Localization/Views/Shared/_Layout.cshtml?range=43-56&highlight=10)]

<span data-ttu-id="17d8e-949">`SetLanguage` 方法可设置区域性 Cookie。</span><span class="sxs-lookup"><span data-stu-id="17d8e-949">The `SetLanguage` method sets the culture cookie.</span></span>

[!code-csharp[](localization/sample/Localization/Controllers/HomeController.cs?range=57-67)]

<span data-ttu-id="17d8e-950">不能将 _SelectLanguagePartial.cshtml 插入此项目的示例代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-950">You can't plug in the *_SelectLanguagePartial.cshtml* to sample code for this project.</span></span> <span data-ttu-id="17d8e-951">[GitHub](https://github.com/aspnet/entropy) 上的 Localization.StarterWeb 项目包含的代码可通过[依赖项注入](dependency-injection.md)容器将 `RequestLocalizationOptions` 流到 Razor 部分。</span><span class="sxs-lookup"><span data-stu-id="17d8e-951">The **Localization.StarterWeb** project on [GitHub](https://github.com/aspnet/entropy) has code to flow the `RequestLocalizationOptions` to a Razor partial through the [Dependency Injection](dependency-injection.md) container.</span></span>

## <a name="model-binding-route-data-and-query-strings"></a><span data-ttu-id="17d8e-952">模型绑定路由数据和查询字符串</span><span class="sxs-lookup"><span data-stu-id="17d8e-952">Model binding route data and query strings</span></span>

<span data-ttu-id="17d8e-953">请参阅[模型绑定路由数据和查询字符串的全球化行为](xref:mvc/models/model-binding#glob)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-953">See [Globalization behavior of model binding route data and query strings](xref:mvc/models/model-binding#glob).</span></span>

## <a name="globalization-and-localization-terms"></a><span data-ttu-id="17d8e-954">全球化和本地化术语</span><span class="sxs-lookup"><span data-stu-id="17d8e-954">Globalization and localization terms</span></span>

<span data-ttu-id="17d8e-955">本地化应用的过程还要求基本了解现代软件开发中常用的相关字符集，以及与之相关的问题。</span><span class="sxs-lookup"><span data-stu-id="17d8e-955">The process of localizing your app also requires a basic understanding of relevant character sets commonly used in modern software development and an understanding of the issues associated with them.</span></span> <span data-ttu-id="17d8e-956">尽管所有计算机将文本都存储为数字（代码），但不同的系统使用不同的数字存储相同的文本。</span><span class="sxs-lookup"><span data-stu-id="17d8e-956">Although all computers store text as numbers (codes), different systems store the same text using different numbers.</span></span> <span data-ttu-id="17d8e-957">本地化过程是指针对特定区域性/区域设置转换应用的用户界面 (UI)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-957">The localization process refers to translating the app user interface (UI) for a specific culture/locale.</span></span>

<span data-ttu-id="17d8e-958">[本地化性](/dotnet/standard/globalization-localization/localizability-review)是一个中间过程，用于验证全球化应用是否准备好进行本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-958">[Localizability](/dotnet/standard/globalization-localization/localizability-review) is an intermediate process for verifying that a globalized app is ready for localization.</span></span>

<span data-ttu-id="17d8e-959">区域性名称的 [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 格式为 `<languagecode2>-<country/regioncode2>`，其中 `<languagecode2>` 是语言代码，`<country/regioncode2>` 是子区域性代码。</span><span class="sxs-lookup"><span data-stu-id="17d8e-959">The [RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) format for the culture name is `<languagecode2>-<country/regioncode2>`, where `<languagecode2>` is the language code and `<country/regioncode2>` is the subculture code.</span></span> <span data-ttu-id="17d8e-960">例如，`es-CL` 表示西班牙语（智利），`en-US` 表示英语（美国），而 `en-AU` 表示英语（澳大利亚）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-960">For example, `es-CL` for Spanish (Chile), `en-US` for English (United States), and `en-AU` for English (Australia).</span></span> <span data-ttu-id="17d8e-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) 是一个与语言相关的 ISO 639 双小写字母的区域性代码和一个与国家/地区相关的 ISO 3166 双大写字母子区域性代码的组合。</span><span class="sxs-lookup"><span data-stu-id="17d8e-961">[RFC 4646](https://www.ietf.org/rfc/rfc4646.txt) is a combination of an ISO 639 two-letter lowercase culture code associated with a language and an ISO 3166 two-letter uppercase subculture code associated with a country or region.</span></span> <span data-ttu-id="17d8e-962">请参阅[语言区域性名称](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-962">See [Language Culture Name](https://msdn.microsoft.com/library/ee825488(v=cs.20).aspx).</span></span>

<span data-ttu-id="17d8e-963">国际化常缩写为“I18N”。</span><span class="sxs-lookup"><span data-stu-id="17d8e-963">Internationalization is often abbreviated to "I18N".</span></span> <span data-ttu-id="17d8e-964">缩写采用第一个和最后一个字母以及它们之间的字母数，因此 18 代表第一个字母“I”和最后一个“N”之间的字母数。</span><span class="sxs-lookup"><span data-stu-id="17d8e-964">The abbreviation takes the first and last letters and the number of letters between them, so 18 stands for the number of letters between the first "I" and the last "N".</span></span> <span data-ttu-id="17d8e-965">这同样适用于全球化 (G11N) 和本地化 (L10N)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-965">The same applies to Globalization (G11N), and Localization (L10N).</span></span>

<span data-ttu-id="17d8e-966">术语：</span><span class="sxs-lookup"><span data-stu-id="17d8e-966">Terms:</span></span>

* <span data-ttu-id="17d8e-967">全球化 (G11N)：使应用支持不同语言和区域的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-967">Globalization (G11N): The process of making an app support different languages and regions.</span></span>
* <span data-ttu-id="17d8e-968">本地化 (L10N)：针对给定语言和区域自定义应用的过程。</span><span class="sxs-lookup"><span data-stu-id="17d8e-968">Localization (L10N): The process of customizing an app for a given language and region.</span></span>
* <span data-ttu-id="17d8e-969">国际化 (I18N)：介绍了全球化和本地化。</span><span class="sxs-lookup"><span data-stu-id="17d8e-969">Internationalization (I18N): Describes both globalization and localization.</span></span>
* <span data-ttu-id="17d8e-970">区域性：它是一种语言和区域（可选）。</span><span class="sxs-lookup"><span data-stu-id="17d8e-970">Culture: It's a language and, optionally, a region.</span></span>
* <span data-ttu-id="17d8e-971">非特定区域性：具有指定语言但不具有区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-971">Neutral culture: A culture that has a specified language, but not a region.</span></span> <span data-ttu-id="17d8e-972">（例如，“en”，“es”）</span><span class="sxs-lookup"><span data-stu-id="17d8e-972">(for example "en", "es")</span></span>
* <span data-ttu-id="17d8e-973">特定区域性：具有指定语言和区域的区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-973">Specific culture: A culture that has a specified language and region.</span></span> <span data-ttu-id="17d8e-974">（例如，“en-US”，“en-GB”，“es-CL”）</span><span class="sxs-lookup"><span data-stu-id="17d8e-974">(for example "en-US", "en-GB", "es-CL")</span></span>
* <span data-ttu-id="17d8e-975">父区域性：包含特定区域性的非特定区域性。</span><span class="sxs-lookup"><span data-stu-id="17d8e-975">Parent culture: The neutral culture that contains a specific culture.</span></span> <span data-ttu-id="17d8e-976">（例如，“en”是“en-US”和“en-GB”的父区域性）</span><span class="sxs-lookup"><span data-stu-id="17d8e-976">(for example, "en" is the parent culture of "en-US" and "en-GB")</span></span>
* <span data-ttu-id="17d8e-977">区域设置：区域设置与区域性相同。</span><span class="sxs-lookup"><span data-stu-id="17d8e-977">Locale: A locale is the same as a culture.</span></span>

[!INCLUDE[](~/includes/localization/currency.md)]

[!INCLUDE[](~/includes/localization/unsupported-culture-log-level.md)]

## <a name="additional-resources"></a><span data-ttu-id="17d8e-978">其他资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-978">Additional resources</span></span>

* <xref:fundamentals/troubleshoot-aspnet-core-localization>
* <span data-ttu-id="17d8e-979">本文所用的 [Localization.StarterWeb 项目](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb)。</span><span class="sxs-lookup"><span data-stu-id="17d8e-979">[Localization.StarterWeb project](https://github.com/aspnet/Entropy/tree/master/samples/Localization.StarterWeb) used in the article.</span></span>
* [<span data-ttu-id="17d8e-980">对 .NET 应用程序进行全球化和本地化</span><span class="sxs-lookup"><span data-stu-id="17d8e-980">Globalizing and localizing .NET applications</span></span>](/dotnet/standard/globalization-localization/index)
* [<span data-ttu-id="17d8e-981">.resx 文件中的资源</span><span class="sxs-lookup"><span data-stu-id="17d8e-981">Resources in .resx Files</span></span>](/dotnet/framework/resources/working-with-resx-files-programmatically)
* [<span data-ttu-id="17d8e-982">Microsoft 多语言应用工具包</span><span class="sxs-lookup"><span data-stu-id="17d8e-982">Microsoft Multilingual App Toolkit</span></span>](https://marketplace.visualstudio.com/items?itemName=MultilingualAppToolkit.MultilingualAppToolkit-18308)
* [<span data-ttu-id="17d8e-983">本地化与泛型</span><span class="sxs-lookup"><span data-stu-id="17d8e-983">Localization & Generics</span></span>](http://hishambinateya.com/localization-and-generics)

::: moniker-end
