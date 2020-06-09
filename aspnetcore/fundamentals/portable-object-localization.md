---
title: 'title:在 ASP.NET Core 中配置可移植对象本地化 author: sebastienros description:本文介绍可移植对象文件，并概述通过 Orchard Core 框架在 ASP.NET Core 应用程序中使用这些文件的步骤。'
author: sebastienros
description: 'ms.author: scaddie ms.date:2017 年 9 月 26 日 no-loc:'
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 57498cc2a773e5147b6eda653cc89d303f238b78
ms.sourcegitcommit: 829dca1d5a7dcccbfe90644101c6be1d1c94ac62
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2020
ms.locfileid: "84355079"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="1fd9d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="1fd9d-103">'Blazor'</span></span>

<span data-ttu-id="1fd9d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="1fd9d-104">'Identity'</span></span>

<span data-ttu-id="1fd9d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="1fd9d-105">'Let's Encrypt'</span></span>

<span data-ttu-id="1fd9d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="1fd9d-106">'Razor'</span></span> <span data-ttu-id="1fd9d-107">'SignalR' uid: fundamentals/portable-object-localization</span><span class="sxs-lookup"><span data-stu-id="1fd9d-107">'SignalR' uid: fundamentals/portable-object-localization</span></span>

<span data-ttu-id="1fd9d-108">在 ASP.NET Core 中配置可移植对象本地化</span><span class="sxs-lookup"><span data-stu-id="1fd9d-108">Configure portable object localization in ASP.NET Core</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="1fd9d-109">作者：[Sébastien Ros](https://github.com/sebastienros) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="1fd9d-109">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="1fd9d-110">本文演示通过 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 框架在 ASP.NET Core 应用程序中使用可移植对象 (PO) 文件的步骤。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-110">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span> <span data-ttu-id="1fd9d-111">**注意：** Orchard Core 不是 Microsoft 产品。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-111">**Note:** Orchard Core isn't a Microsoft product.</span></span>
- <span data-ttu-id="1fd9d-112">因此，Microsoft 不提供针对此功能的支持。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-112">Consequently, Microsoft provides no support for this feature.</span></span>
- <span data-ttu-id="1fd9d-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="1fd9d-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span> <span data-ttu-id="1fd9d-114">什么是 PO 文件？</span><span class="sxs-lookup"><span data-stu-id="1fd9d-114">What is a PO file?</span></span>
- <span data-ttu-id="1fd9d-115">PO 文件作为包含给定语言的已转换字符串的文本文件分发。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-115">PO files are distributed as text files containing the translated strings for a given language.</span></span>

### <a name="example"></a><span data-ttu-id="1fd9d-116">使用 PO 文件替代 .resx 文件的一些优势包括：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-116">Some advantages of using PO files instead *.resx* files include:</span></span>

<span data-ttu-id="1fd9d-117">PO 文件支持复数形式；而 .resx 文件不支持复数形式。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-117">PO files support pluralization; *.resx* files don't support pluralization.</span></span>

<span data-ttu-id="1fd9d-118">PO 文件的编译方法与 .resx 文件不同。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-118">PO files aren't compiled like *.resx* files.</span></span>

```text
#: Services/EmailService.cs:29
msgid "Enter a comma separated list of email addresses."
msgstr "Entrez une liste d'emails séparés par une virgule."

#: Views/Email.cshtml:112
msgid "The email address is \"{0}\"."
msgid_plural "The email addresses are \"{0}\"."
msgstr[0] "L'adresse email est \"{0}\"."
msgstr[1] "Les adresses email sont \"{0}\""
```

<span data-ttu-id="1fd9d-119">同样，无需专用工具和生成步骤。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-119">As such, specialized tooling and build steps aren't required.</span></span>

- <span data-ttu-id="1fd9d-120">PO 文件可很好地与协作联机编辑工具结合使用。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-120">PO files work well with collaborative online editing tools.</span></span> <span data-ttu-id="1fd9d-121">示例</span><span class="sxs-lookup"><span data-stu-id="1fd9d-121">Example</span></span>
- <span data-ttu-id="1fd9d-122">下面是一个包含两个法语字符串（其中一个具有复数形式）转换的示例 PO 文件：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-122">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>
- <span data-ttu-id="1fd9d-123">fr.po</span><span class="sxs-lookup"><span data-stu-id="1fd9d-123">*fr.po*</span></span>

<span data-ttu-id="1fd9d-124">此示例使用下列语法：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-124">This example uses the following syntax:</span></span>

- <span data-ttu-id="1fd9d-125">`#:`：用于指示要转换的字符串的上下文的注释。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-125">`#:`: A comment indicating the context of the string to be translated.</span></span>
- <span data-ttu-id="1fd9d-126">根据使用的位置，可对相同字符串进行不同转换。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-126">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="1fd9d-127">`msgid`：未转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-127">`msgid`: The untranslated string.</span></span>

<span data-ttu-id="1fd9d-128">`msgstr`：已转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-128">`msgstr`: The translated string.</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="1fd9d-129">在支持复数形式的情况下，可定义多个条目。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-129">In the case of pluralization support, more entries can be defined.</span></span>

<span data-ttu-id="1fd9d-130">`msgid_plural`：未转换的复数形式字符串。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-130">`msgid_plural`: The untranslated plural string.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="1fd9d-131">`msgstr[0]`：针对事例 0 的已转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-131">`msgstr[0]`: The translated string for the case 0.</span></span>

<span data-ttu-id="1fd9d-132">`msgstr[N]`：针对事例 N 的已转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-132">`msgstr[N]`: The translated string for the case N.</span></span> <span data-ttu-id="1fd9d-133">可在[此处](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)找到 PO 文件规范。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-133">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

<span data-ttu-id="1fd9d-134">在 ASP.NET Core 中配置 PO 文件支持</span><span class="sxs-lookup"><span data-stu-id="1fd9d-134">Configuring PO file support in ASP.NET Core</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="1fd9d-135">此示例基于从 Visual Studio 2017 项目模板中生成的 ASP.NET Core MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-135">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

<span data-ttu-id="1fd9d-136">引用包</span><span class="sxs-lookup"><span data-stu-id="1fd9d-136">Referencing the package</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="1fd9d-137">添加对 `OrchardCore.Localization.Core` NuGet 包的引用。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-137">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="1fd9d-138">它可在 [MyGet](https://www.myget.org/) 上的以下包源中获得： https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="1fd9d-138">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span> <span data-ttu-id="1fd9d-139">.csproj 文件现在包含类似于以下内容的行（版本号可能不同）：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-139">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="1fd9d-140">注册服务</span><span class="sxs-lookup"><span data-stu-id="1fd9d-140">Registering the service</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="1fd9d-141">将所需服务添加到 Startup.cs 的 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-141">Add the required services to the `ConfigureServices` method of *Startup.cs*:</span></span>

<span data-ttu-id="1fd9d-142">将所需中间件添加到 Startup.cs 的 `Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-142">Add the required middleware to the `Configure` method of *Startup.cs*:</span></span> <span data-ttu-id="1fd9d-143">将以下代码添加到所选的 Razor 视图中。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-143">Add the following code to your Razor view of choice.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="1fd9d-144">在此示例中，使用了 About.cshtml。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-144">*About.cshtml* is used in this example.</span></span> <span data-ttu-id="1fd9d-145">注入了 `IViewLocalizer` 实例并将其用于转换文本“Hello world！”。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-145">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span> <span data-ttu-id="1fd9d-146">创建 PO 文件</span><span class="sxs-lookup"><span data-stu-id="1fd9d-146">Creating a PO file</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="1fd9d-147">在应用程序根文件夹中创建名为 \<culture code>.po 的文件。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-147">Create a file named *\<culture code>.po* in your application root folder.</span></span>

<span data-ttu-id="1fd9d-148">在此示例中，文件名为 fr.po，因为使用了法语：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-148">In this example, the file name is *fr.po* because the French language is used:</span></span> <span data-ttu-id="1fd9d-149">此文件存储了要转换的字符串和已转换为法语的字符串。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-149">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="1fd9d-150">如有必要，转换将还原为其父级区域性。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-150">Translations revert to their parent culture, if necessary.</span></span>

<span data-ttu-id="1fd9d-151">在此示例中，如果请求的区域性为 `fr-FR` 或 `fr-CA`，则使用 fr.po 文件。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-151">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span> <span data-ttu-id="1fd9d-152">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="1fd9d-152">Testing the application</span></span> <span data-ttu-id="1fd9d-153">运行应用程序并导航到 URL `/Home/About`。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-153">Run your application, and navigate to the URL `/Home/About`.</span></span>

## <a name="pluralization"></a><span data-ttu-id="1fd9d-154">此时将显示文本 Hello world!</span><span class="sxs-lookup"><span data-stu-id="1fd9d-154">The text **Hello world!**</span></span>

<span data-ttu-id="1fd9d-155">。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-155">is displayed.</span></span> <span data-ttu-id="1fd9d-156">导航到 URL `/Home/About?culture=fr-FR`。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-156">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span>

<span data-ttu-id="1fd9d-157">此时将显示文本 Bonjour le monde!</span><span class="sxs-lookup"><span data-stu-id="1fd9d-157">The text **Bonjour le monde!**</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="1fd9d-158">。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-158">is displayed.</span></span>

<span data-ttu-id="1fd9d-159">复数形式</span><span class="sxs-lookup"><span data-stu-id="1fd9d-159">Pluralization</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="1fd9d-160">PO 文件支持复数形式，在相同字符串需要基于基数以不同方式进行转换时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-160">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="1fd9d-161">此任务较为复杂，因为每种语言均定义了自定义规则，以基于基数选择要使用的字符串。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-161">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="1fd9d-162">Orchard 本地化包提供了一个 API 以自动调用这些不同的复数形式。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-162">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span> <span data-ttu-id="1fd9d-163">创建复数形式 PO 文件</span><span class="sxs-lookup"><span data-stu-id="1fd9d-163">Creating pluralization PO files</span></span> <span data-ttu-id="1fd9d-164">将以下内容添加到前面所述的 fr.po 文件：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-164">Add the following content to the previously mentioned *fr.po* file:</span></span>

<span data-ttu-id="1fd9d-165">有关此示例中每个条目所表示的内容的说明，请参阅[什么是 PO 文件？](#what-is-a-po-file)。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-165">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span> <span data-ttu-id="1fd9d-166">使用不同的复数形式添加语言</span><span class="sxs-lookup"><span data-stu-id="1fd9d-166">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="1fd9d-167">前面的示例中使用了英语和法语字符串。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-167">English and French strings were used in the previous example.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="1fd9d-168">英语和法语只有两种复数形式且拥有相同形式规则，即一的基数映射到第一种复数形式。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-168">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span>

```csharp
var supportedCultures = new List<CultureInfo>
{
    new CultureInfo("en-US"),
    new CultureInfo("en"),
    new CultureInfo("fr-FR"),
    new CultureInfo("fr"),
    new CultureInfo("cs")
};
```

<span data-ttu-id="1fd9d-169">任何其他基数映射到第二种复数形式。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-169">Any other cardinality is mapped to the second plural form.</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="1fd9d-170">并非所有语言都拥有相同的规则。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-170">Not all languages share the same rules.</span></span> <span data-ttu-id="1fd9d-171">捷克语就是一个例子，它具有三种复数形式。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-171">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="1fd9d-172">如下所示，创建 `cs.po` 文件，并记下复数形式如何需要三种不同转换：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-172">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

<span data-ttu-id="1fd9d-173">若要接受捷克语本地化，请将 `"cs"` 添加到 `ConfigureServices` 方法中受支持的区域性列表：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-173">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="1fd9d-174">编辑 Views/Home/About.cshtml 文件以呈现一些基数的已本地化复数形式字符串：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-174">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="1fd9d-175">**注意：** 在实际方案中，将使用变量表示计数。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-175">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="1fd9d-176">此处，我们通过三个不同的值重复相同代码，以公开非常特定的事例。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-176">Here, we repeat the same code with three different values to expose a very specific case.</span></span> <span data-ttu-id="1fd9d-177">切换区域性时，将显示如下内容：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-177">Upon switching cultures, you see the following:</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="1fd9d-178">对于 `/Home/About`：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-178">For `/Home/About`:</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="1fd9d-179">对于 `/Home/About?culture=fr`：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-179">For `/Home/About?culture=fr`:</span></span>

<span data-ttu-id="1fd9d-180">对于 `/Home/About?culture=cs`：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-180">For `/Home/About?culture=cs`:</span></span> <span data-ttu-id="1fd9d-181">请注意，对于捷克语区域性，这三种转换各不相同。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-181">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="1fd9d-182">对于最后两个已转换字符串，法语和英语区域性具有相同构造。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-182">The French and English cultures share the same construction for the two last translated strings.</span></span> <span data-ttu-id="1fd9d-183">高级任务</span><span class="sxs-lookup"><span data-stu-id="1fd9d-183">Advanced tasks</span></span>

<span data-ttu-id="1fd9d-184">将字符串置于上下文中理解</span><span class="sxs-lookup"><span data-stu-id="1fd9d-184">Contextualizing strings</span></span> <span data-ttu-id="1fd9d-185">应用程序通常包含要在多个位置中进行转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-185">Applications often contain the strings to be translated in several places.</span></span>

<span data-ttu-id="1fd9d-186">在应用中的特定位置（Razor 视图或类文件），相同字符串可能具有不同转换。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-186">The same string may have a different translation in certain locations within an app (Razor views or class files).</span></span> <span data-ttu-id="1fd9d-187">PO 文件支持文件上下文概念，此概念可用于对所表示的字符串进行分类。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-187">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="1fd9d-188">使用文件上下文，可将字符串进行不同转换，具体取决于文件上下文（或缺乏文件上下文）。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-188">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span> <span data-ttu-id="1fd9d-189">PO 本地化服务使用完整类的名称或转换字符串时使用的视图。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-189">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span>

<span data-ttu-id="1fd9d-190">这通过在 `msgctxt` 条目上设置值来完成。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-190">This is accomplished by setting the value on the `msgctxt` entry.</span></span> <span data-ttu-id="1fd9d-191">请考虑对以前的 fr.po 示例作一点小小的补充。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-191">Consider a minor addition to the previous *fr.po* example.</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="1fd9d-192">可通过设置保留的 `msgctxt` 条目的值将位于 Views/Home/About.cshtml 的 Razor 视图定义为文件上下文：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-192">A Razor view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

<span data-ttu-id="1fd9d-193">这样设置 `msgctxt` 后，导航到 `/Home/About?culture=fr-FR` 时将发生文本转换。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-193">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="1fd9d-194">而导航到 `/Home/Contact?culture=fr-FR` 时，则不发生转换。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-194">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="1fd9d-195">当没有特定条目与给定文件上下文相匹配时，Orchard Core 的回退机制将在没有上下文的情况下查找适当的 PO 文件。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-195">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span>

<span data-ttu-id="1fd9d-196">假设不存在针对 Views/Home/Contact.cshtml 定义的特定文件上下文，导航到 `/Home/Contact?culture=fr-FR`，加载 PO 文件，如：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-196">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml*, navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span> <span data-ttu-id="1fd9d-197">更改 PO 文件的位置</span><span class="sxs-lookup"><span data-stu-id="1fd9d-197">Changing the location of PO files</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="1fd9d-198">可以在 `ConfigureServices` 中更改 PO 文件的默认位置：</span><span class="sxs-lookup"><span data-stu-id="1fd9d-198">The default location of PO files can be changed in `ConfigureServices`:</span></span>

<span data-ttu-id="1fd9d-199">在此示例中，从本地化文件夹加载 PO 文件。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-199">In this example, the PO files are loaded from the *Localization* folder.</span></span> <span data-ttu-id="1fd9d-200">实现用于查找本地化文件的自定义逻辑</span><span class="sxs-lookup"><span data-stu-id="1fd9d-200">Implementing a custom logic for finding localization files</span></span> <span data-ttu-id="1fd9d-201">当需要更复杂的逻辑以查找 PO 文件时，可实现 `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` 接口并将其注册为服务。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-201">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span>

<span data-ttu-id="1fd9d-202">在可将 PO 文件存储于不同位置或在文件夹层次结构中找到文件时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="1fd9d-202">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>
