---
title: 在 ASP.NET Core 中配置可移植对象本地化
author: sebastienros
description: 本文介绍可移植对象文件，并概述通过 Orchard Core 框架在 ASP.NET Core 应用程序中使用这些文件的步骤。
ms.author: scaddie
ms.date: 09/26/2017
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: fundamentals/portable-object-localization
ms.openlocfilehash: 2e28ebaf1962ebd834c43f1cfbc28929b1937c40
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053717"
---
# <a name="configure-portable-object-localization-in-aspnet-core"></a><span data-ttu-id="12508-103">在 ASP.NET Core 中配置可移植对象本地化</span><span class="sxs-lookup"><span data-stu-id="12508-103">Configure portable object localization in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="12508-104">作者：[Sébastien Ros](https://github.com/sebastienros)、[Scott Addie](https://twitter.com/Scott_Addie) 和 [Hisham Bin Ateya](https://github.com/hishamco)</span><span class="sxs-lookup"><span data-stu-id="12508-104">By [Sébastien Ros](https://github.com/sebastienros), [Scott Addie](https://twitter.com/Scott_Addie) and [Hisham Bin Ateya](https://github.com/hishamco)</span></span>

<span data-ttu-id="12508-105">本文演示通过 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 框架在 ASP.NET Core 应用程序中使用可移植对象 (PO) 文件的步骤。</span><span class="sxs-lookup"><span data-stu-id="12508-105">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="12508-106">**注意：** Orchard Core 不是 Microsoft 产品。</span><span class="sxs-lookup"><span data-stu-id="12508-106">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="12508-107">因此，Microsoft 不提供针对此功能的支持。</span><span class="sxs-lookup"><span data-stu-id="12508-107">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="12508-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="12508-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/3.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="12508-109">什么是 PO 文件？</span><span class="sxs-lookup"><span data-stu-id="12508-109">What is a PO file?</span></span>

<span data-ttu-id="12508-110">PO 文件作为包含给定语言的已转换字符串的文本文件分发。</span><span class="sxs-lookup"><span data-stu-id="12508-110">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="12508-111">使用 PO 文件替代 .resx 文件的一些优势包括：</span><span class="sxs-lookup"><span data-stu-id="12508-111">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="12508-112">PO 文件支持复数形式；而 .resx 文件不支持复数形式。</span><span class="sxs-lookup"><span data-stu-id="12508-112">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="12508-113">PO 文件的编译方法与 .resx 文件不同。</span><span class="sxs-lookup"><span data-stu-id="12508-113">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="12508-114">同样，无需专用工具和生成步骤。</span><span class="sxs-lookup"><span data-stu-id="12508-114">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="12508-115">PO 文件可很好地与协作联机编辑工具结合使用。</span><span class="sxs-lookup"><span data-stu-id="12508-115">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="12508-116">示例</span><span class="sxs-lookup"><span data-stu-id="12508-116">Example</span></span>

<span data-ttu-id="12508-117">下面是一个包含两个法语字符串（其中一个具有复数形式）转换的示例 PO 文件：</span><span class="sxs-lookup"><span data-stu-id="12508-117">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="12508-118">fr.po</span><span class="sxs-lookup"><span data-stu-id="12508-118">*fr.po*</span></span>

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

<span data-ttu-id="12508-119">此示例使用下列语法：</span><span class="sxs-lookup"><span data-stu-id="12508-119">This example uses the following syntax:</span></span>

- <span data-ttu-id="12508-120">`#:`：用于指示要转换的字符串的上下文的注释。</span><span class="sxs-lookup"><span data-stu-id="12508-120">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="12508-121">根据使用的位置，可对相同字符串进行不同转换。</span><span class="sxs-lookup"><span data-stu-id="12508-121">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="12508-122">`msgid`：未转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-122">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="12508-123">`msgstr`：已转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-123">`msgstr`: The translated string.</span></span>

<span data-ttu-id="12508-124">在支持复数形式的情况下，可定义多个条目。</span><span class="sxs-lookup"><span data-stu-id="12508-124">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="12508-125">`msgid_plural`：未转换的复数形式字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-125">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="12508-126">`msgstr[0]`：针对事例 0 的已转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-126">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="12508-127">`msgstr[N]`：针对事例 N 的已转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-127">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="12508-128">可在[此处](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)找到 PO 文件规范。</span><span class="sxs-lookup"><span data-stu-id="12508-128">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="12508-129">在 ASP.NET Core 中配置 PO 文件支持</span><span class="sxs-lookup"><span data-stu-id="12508-129">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="12508-130">此示例基于从 Visual Studio 2017 项目模板中生成的 ASP.NET Core MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="12508-130">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="12508-131">引用包</span><span class="sxs-lookup"><span data-stu-id="12508-131">Referencing the package</span></span>

<span data-ttu-id="12508-132">添加对 `OrchardCore.Localization.Core` NuGet 包的引用。</span><span class="sxs-lookup"><span data-stu-id="12508-132">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="12508-133">它可在 [MyGet](https://www.myget.org/) 上的以下包源中获得： https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="12508-133">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="12508-134">.csproj 文件现在包含类似于以下内容的行（版本号可能不同）：</span><span class="sxs-lookup"><span data-stu-id="12508-134">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/3.x/POLocalization/POLocalization.csproj?range=8)]

### <a name="registering-the-service"></a><span data-ttu-id="12508-135">注册服务</span><span class="sxs-lookup"><span data-stu-id="12508-135">Registering the service</span></span>

<span data-ttu-id="12508-136">将所需服务添加到 Startup.cs 的 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="12508-136">Add the required services to the `ConfigureServices` method of *Startup.cs* :</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="12508-137">将所需中间件添加到 Startup.cs 的 `Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="12508-137">Add the required middleware to the `Configure` method of *Startup.cs* :</span></span>

[!code-csharp[](localization/sample/3.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="12508-138">将以下代码添加到所选的 :::no-loc(Razor)::: 视图中。</span><span class="sxs-lookup"><span data-stu-id="12508-138">Add the following code to your :::no-loc(Razor)::: view of choice.</span></span> <span data-ttu-id="12508-139">在此示例中，使用了 About.cshtml。</span><span class="sxs-lookup"><span data-stu-id="12508-139">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/3.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="12508-140">注入了 `IViewLocalizer` 实例并将其用于转换文本“Hello world！”。</span><span class="sxs-lookup"><span data-stu-id="12508-140">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="12508-141">创建 PO 文件</span><span class="sxs-lookup"><span data-stu-id="12508-141">Creating a PO file</span></span>

<span data-ttu-id="12508-142">在应用程序根文件夹中创建名为 \<culture code>.po 的文件。</span><span class="sxs-lookup"><span data-stu-id="12508-142">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="12508-143">在此示例中，文件名为 fr.po，因为使用了法语：</span><span class="sxs-lookup"><span data-stu-id="12508-143">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

<span data-ttu-id="12508-144">此文件存储了要转换的字符串和已转换为法语的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-144">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="12508-145">如有必要，转换将还原为其父级区域性。</span><span class="sxs-lookup"><span data-stu-id="12508-145">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="12508-146">在此示例中，如果请求的区域性为 `fr-FR` 或 `fr-CA`，则使用 fr.po 文件。</span><span class="sxs-lookup"><span data-stu-id="12508-146">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="12508-147">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="12508-147">Testing the application</span></span>

<span data-ttu-id="12508-148">运行应用程序并导航到 URL `/Home/About`。</span><span class="sxs-lookup"><span data-stu-id="12508-148">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="12508-149">此时将显示文本 Hello world!</span><span class="sxs-lookup"><span data-stu-id="12508-149">The text **Hello world!**</span></span> <span data-ttu-id="12508-150">。</span><span class="sxs-lookup"><span data-stu-id="12508-150">is displayed.</span></span>

<span data-ttu-id="12508-151">导航到 URL `/Home/About?culture=fr-FR`。</span><span class="sxs-lookup"><span data-stu-id="12508-151">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="12508-152">此时将显示文本 Bonjour le monde!</span><span class="sxs-lookup"><span data-stu-id="12508-152">The text **Bonjour le monde!**</span></span> <span data-ttu-id="12508-153">。</span><span class="sxs-lookup"><span data-stu-id="12508-153">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="12508-154">复数形式</span><span class="sxs-lookup"><span data-stu-id="12508-154">Pluralization</span></span>

<span data-ttu-id="12508-155">PO 文件支持复数形式，在相同字符串需要基于基数以不同方式进行转换时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="12508-155">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="12508-156">此任务较为复杂，因为每种语言均定义了自定义规则，以基于基数选择要使用的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-156">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="12508-157">Orchard 本地化包提供了一个 API 以自动调用这些不同的复数形式。</span><span class="sxs-lookup"><span data-stu-id="12508-157">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="12508-158">创建复数形式 PO 文件</span><span class="sxs-lookup"><span data-stu-id="12508-158">Creating pluralization PO files</span></span>

<span data-ttu-id="12508-159">将以下内容添加到前面所述的 fr.po 文件：</span><span class="sxs-lookup"><span data-stu-id="12508-159">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="12508-160">有关此示例中每个条目所表示的内容的说明，请参阅[什么是 PO 文件？](#what-is-a-po-file)。</span><span class="sxs-lookup"><span data-stu-id="12508-160">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="12508-161">使用不同的复数形式添加语言</span><span class="sxs-lookup"><span data-stu-id="12508-161">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="12508-162">前面的示例中使用了英语和法语字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-162">English and French strings were used in the previous example.</span></span> <span data-ttu-id="12508-163">英语和法语只有两种复数形式且拥有相同形式规则，即一的基数映射到第一种复数形式。</span><span class="sxs-lookup"><span data-stu-id="12508-163">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="12508-164">任何其他基数映射到第二种复数形式。</span><span class="sxs-lookup"><span data-stu-id="12508-164">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="12508-165">并非所有语言都拥有相同的规则。</span><span class="sxs-lookup"><span data-stu-id="12508-165">Not all languages share the same rules.</span></span> <span data-ttu-id="12508-166">捷克语就是一个例子，它具有三种复数形式。</span><span class="sxs-lookup"><span data-stu-id="12508-166">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="12508-167">如下所示，创建 `cs.po` 文件，并记下复数形式如何需要三种不同转换：</span><span class="sxs-lookup"><span data-stu-id="12508-167">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/cs.po)]

<span data-ttu-id="12508-168">若要接受捷克语本地化，请将 `"cs"` 添加到 `ConfigureServices` 方法中受支持的区域性列表：</span><span class="sxs-lookup"><span data-stu-id="12508-168">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="12508-169">编辑 Views/Home/About.cshtml 文件以呈现一些基数的已本地化复数形式字符串：</span><span class="sxs-lookup"><span data-stu-id="12508-169">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="12508-170">**注意：** 在实际方案中，将使用变量表示计数。</span><span class="sxs-lookup"><span data-stu-id="12508-170">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="12508-171">此处，我们通过三个不同的值重复相同代码，以公开非常特定的事例。</span><span class="sxs-lookup"><span data-stu-id="12508-171">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="12508-172">切换区域性时，将显示如下内容：</span><span class="sxs-lookup"><span data-stu-id="12508-172">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="12508-173">对于 `/Home/About`：</span><span class="sxs-lookup"><span data-stu-id="12508-173">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="12508-174">对于 `/Home/About?culture=fr`：</span><span class="sxs-lookup"><span data-stu-id="12508-174">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="12508-175">对于 `/Home/About?culture=cs`：</span><span class="sxs-lookup"><span data-stu-id="12508-175">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="12508-176">请注意，对于捷克语区域性，这三种转换各不相同。</span><span class="sxs-lookup"><span data-stu-id="12508-176">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="12508-177">对于最后两个已转换字符串，法语和英语区域性具有相同构造。</span><span class="sxs-lookup"><span data-stu-id="12508-177">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="12508-178">高级任务</span><span class="sxs-lookup"><span data-stu-id="12508-178">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="12508-179">将字符串置于上下文中理解</span><span class="sxs-lookup"><span data-stu-id="12508-179">Contextualizing strings</span></span>

<span data-ttu-id="12508-180">应用程序通常包含要在多个位置中进行转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-180">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="12508-181">在应用中的特定位置（:::no-loc(Razor)::: 视图或类文件），相同字符串可能具有不同转换。</span><span class="sxs-lookup"><span data-stu-id="12508-181">The same string may have a different translation in certain locations within an app (:::no-loc(Razor)::: views or class files).</span></span> <span data-ttu-id="12508-182">PO 文件支持文件上下文概念，此概念可用于对所表示的字符串进行分类。</span><span class="sxs-lookup"><span data-stu-id="12508-182">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="12508-183">使用文件上下文，可将字符串进行不同转换，具体取决于文件上下文（或缺乏文件上下文）。</span><span class="sxs-lookup"><span data-stu-id="12508-183">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="12508-184">PO 本地化服务使用完整类的名称或转换字符串时使用的视图。</span><span class="sxs-lookup"><span data-stu-id="12508-184">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="12508-185">这通过在 `msgctxt` 条目上设置值来完成。</span><span class="sxs-lookup"><span data-stu-id="12508-185">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="12508-186">请考虑对以前的 fr.po 示例作一点小小的补充。</span><span class="sxs-lookup"><span data-stu-id="12508-186">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="12508-187">可通过设置保留的 `msgctxt` 条目的值将位于 Views/Home/About.cshtml 的 :::no-loc(Razor)::: 视图定义为文件上下文：</span><span class="sxs-lookup"><span data-stu-id="12508-187">A :::no-loc(Razor)::: view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="12508-188">这样设置 `msgctxt` 后，导航到 `/Home/About?culture=fr-FR` 时将发生文本转换。</span><span class="sxs-lookup"><span data-stu-id="12508-188">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="12508-189">而导航到 `/Home/Contact?culture=fr-FR` 时，则不发生转换。</span><span class="sxs-lookup"><span data-stu-id="12508-189">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="12508-190">当没有特定条目与给定文件上下文相匹配时，Orchard Core 的回退机制将在没有上下文的情况下查找适当的 PO 文件。</span><span class="sxs-lookup"><span data-stu-id="12508-190">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="12508-191">假设不存在针对 Views/Home/Contact.cshtml 定义的特定文件上下文，导航到 `/Home/Contact?culture=fr-FR`，加载 PO 文件，如：</span><span class="sxs-lookup"><span data-stu-id="12508-191">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml* , navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/3.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="12508-192">更改 PO 文件的位置</span><span class="sxs-lookup"><span data-stu-id="12508-192">Changing the location of PO files</span></span>

<span data-ttu-id="12508-193">可以在 `ConfigureServices` 中更改 PO 文件的默认位置：</span><span class="sxs-lookup"><span data-stu-id="12508-193">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="12508-194">在此示例中，从本地化文件夹加载 PO 文件。</span><span class="sxs-lookup"><span data-stu-id="12508-194">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="12508-195">实现用于查找本地化文件的自定义逻辑</span><span class="sxs-lookup"><span data-stu-id="12508-195">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="12508-196">当需要更复杂的逻辑以查找 PO 文件时，可实现 `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` 接口并将其注册为服务。</span><span class="sxs-lookup"><span data-stu-id="12508-196">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="12508-197">在可将 PO 文件存储于不同位置或在文件夹层次结构中找到文件时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="12508-197">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="12508-198">使用不同默认复数形式语言</span><span class="sxs-lookup"><span data-stu-id="12508-198">Using a different default pluralized language</span></span>

<span data-ttu-id="12508-199">此包包含特定于两种复数形式的 `Plural` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="12508-199">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="12508-200">对于需要更多复数形式的语言，请创建扩展方法。</span><span class="sxs-lookup"><span data-stu-id="12508-200">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="12508-201">通过扩展方法，无需提供默认语言的任何本地化文件 &mdash; 可在代码中直接使用原始字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-201">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="12508-202">可使用更加广泛的、接受转换的字符串数组的 `Plural(int count, string[] pluralForms, params object[] arguments)` 重载。</span><span class="sxs-lookup"><span data-stu-id="12508-202">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="12508-203">作者：[Sébastien Ros](https://github.com/sebastienros) 和 [Scott Addie](https://twitter.com/Scott_Addie)</span><span class="sxs-lookup"><span data-stu-id="12508-203">By [Sébastien Ros](https://github.com/sebastienros) and [Scott Addie](https://twitter.com/Scott_Addie)</span></span>

<span data-ttu-id="12508-204">本文演示通过 [Orchard Core](https://github.com/OrchardCMS/OrchardCore) 框架在 ASP.NET Core 应用程序中使用可移植对象 (PO) 文件的步骤。</span><span class="sxs-lookup"><span data-stu-id="12508-204">This article walks through the steps for using Portable Object (PO) files in an ASP.NET Core application with the [Orchard Core](https://github.com/OrchardCMS/OrchardCore) framework.</span></span>

<span data-ttu-id="12508-205">**注意：** Orchard Core 不是 Microsoft 产品。</span><span class="sxs-lookup"><span data-stu-id="12508-205">**Note:** Orchard Core isn't a Microsoft product.</span></span> <span data-ttu-id="12508-206">因此，Microsoft 不提供针对此功能的支持。</span><span class="sxs-lookup"><span data-stu-id="12508-206">Consequently, Microsoft provides no support for this feature.</span></span>

<span data-ttu-id="12508-207">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="12508-207">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/localization/sample/2.x/POLocalization) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="what-is-a-po-file"></a><span data-ttu-id="12508-208">什么是 PO 文件？</span><span class="sxs-lookup"><span data-stu-id="12508-208">What is a PO file?</span></span>

<span data-ttu-id="12508-209">PO 文件作为包含给定语言的已转换字符串的文本文件分发。</span><span class="sxs-lookup"><span data-stu-id="12508-209">PO files are distributed as text files containing the translated strings for a given language.</span></span> <span data-ttu-id="12508-210">使用 PO 文件替代 .resx 文件的一些优势包括：</span><span class="sxs-lookup"><span data-stu-id="12508-210">Some advantages of using PO files instead *.resx* files include:</span></span>
- <span data-ttu-id="12508-211">PO 文件支持复数形式；而 .resx 文件不支持复数形式。</span><span class="sxs-lookup"><span data-stu-id="12508-211">PO files support pluralization; *.resx* files don't support pluralization.</span></span>
- <span data-ttu-id="12508-212">PO 文件的编译方法与 .resx 文件不同。</span><span class="sxs-lookup"><span data-stu-id="12508-212">PO files aren't compiled like *.resx* files.</span></span> <span data-ttu-id="12508-213">同样，无需专用工具和生成步骤。</span><span class="sxs-lookup"><span data-stu-id="12508-213">As such, specialized tooling and build steps aren't required.</span></span>
- <span data-ttu-id="12508-214">PO 文件可很好地与协作联机编辑工具结合使用。</span><span class="sxs-lookup"><span data-stu-id="12508-214">PO files work well with collaborative online editing tools.</span></span>

### <a name="example"></a><span data-ttu-id="12508-215">示例</span><span class="sxs-lookup"><span data-stu-id="12508-215">Example</span></span>

<span data-ttu-id="12508-216">下面是一个包含两个法语字符串（其中一个具有复数形式）转换的示例 PO 文件：</span><span class="sxs-lookup"><span data-stu-id="12508-216">Here is a sample PO file containing the translation for two strings in French, including one with its plural form:</span></span>

<span data-ttu-id="12508-217">fr.po</span><span class="sxs-lookup"><span data-stu-id="12508-217">*fr.po*</span></span>

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

<span data-ttu-id="12508-218">此示例使用下列语法：</span><span class="sxs-lookup"><span data-stu-id="12508-218">This example uses the following syntax:</span></span>

- <span data-ttu-id="12508-219">`#:`：用于指示要转换的字符串的上下文的注释。</span><span class="sxs-lookup"><span data-stu-id="12508-219">`#:`: A comment indicating the context of the string to be translated.</span></span> <span data-ttu-id="12508-220">根据使用的位置，可对相同字符串进行不同转换。</span><span class="sxs-lookup"><span data-stu-id="12508-220">The same string might be translated differently depending on where it's being used.</span></span>
- <span data-ttu-id="12508-221">`msgid`：未转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-221">`msgid`: The untranslated string.</span></span>
- <span data-ttu-id="12508-222">`msgstr`：已转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-222">`msgstr`: The translated string.</span></span>

<span data-ttu-id="12508-223">在支持复数形式的情况下，可定义多个条目。</span><span class="sxs-lookup"><span data-stu-id="12508-223">In the case of pluralization support, more entries can be defined.</span></span>

- <span data-ttu-id="12508-224">`msgid_plural`：未转换的复数形式字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-224">`msgid_plural`: The untranslated plural string.</span></span>
- <span data-ttu-id="12508-225">`msgstr[0]`：针对事例 0 的已转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-225">`msgstr[0]`: The translated string for the case 0.</span></span>
- <span data-ttu-id="12508-226">`msgstr[N]`：针对事例 N 的已转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-226">`msgstr[N]`: The translated string for the case N.</span></span>

<span data-ttu-id="12508-227">可在[此处](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html)找到 PO 文件规范。</span><span class="sxs-lookup"><span data-stu-id="12508-227">The PO file specification can be found [here](https://www.gnu.org/savannah-checkouts/gnu/gettext/manual/html_node/PO-Files.html).</span></span>

## <a name="configuring-po-file-support-in-aspnet-core"></a><span data-ttu-id="12508-228">在 ASP.NET Core 中配置 PO 文件支持</span><span class="sxs-lookup"><span data-stu-id="12508-228">Configuring PO file support in ASP.NET Core</span></span>

<span data-ttu-id="12508-229">此示例基于从 Visual Studio 2017 项目模板中生成的 ASP.NET Core MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="12508-229">This example is based on an ASP.NET Core MVC application generated from a Visual Studio 2017 project template.</span></span>

### <a name="referencing-the-package"></a><span data-ttu-id="12508-230">引用包</span><span class="sxs-lookup"><span data-stu-id="12508-230">Referencing the package</span></span>

<span data-ttu-id="12508-231">添加对 `OrchardCore.Localization.Core` NuGet 包的引用。</span><span class="sxs-lookup"><span data-stu-id="12508-231">Add a reference to the `OrchardCore.Localization.Core` NuGet package.</span></span> <span data-ttu-id="12508-232">它可在 [MyGet](https://www.myget.org/) 上的以下包源中获得： https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span><span class="sxs-lookup"><span data-stu-id="12508-232">It's available on [MyGet](https://www.myget.org/) at the following package source: https://www.myget.org/F/orchardcore-preview/api/v3/index.json</span></span>

<span data-ttu-id="12508-233">.csproj 文件现在包含类似于以下内容的行（版本号可能不同）：</span><span class="sxs-lookup"><span data-stu-id="12508-233">The *.csproj* file now contains a line similar to the following (version number may vary):</span></span>

[!code-xml[](localization/sample/2.x/POLocalization/POLocalization.csproj?range=9)]

### <a name="registering-the-service"></a><span data-ttu-id="12508-234">注册服务</span><span class="sxs-lookup"><span data-stu-id="12508-234">Registering the service</span></span>

<span data-ttu-id="12508-235">将所需服务添加到 Startup.cs 的 `ConfigureServices` 方法：</span><span class="sxs-lookup"><span data-stu-id="12508-235">Add the required services to the `ConfigureServices` method of *Startup.cs* :</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_ConfigureServices&highlight=4-21)]

<span data-ttu-id="12508-236">将所需中间件添加到 Startup.cs 的 `Configure` 方法：</span><span class="sxs-lookup"><span data-stu-id="12508-236">Add the required middleware to the `Configure` method of *Startup.cs* :</span></span>

[!code-csharp[](localization/sample/2.x/POLocalization/Startup.cs?name=snippet_Configure&highlight=15)]

<span data-ttu-id="12508-237">将以下代码添加到所选的 :::no-loc(Razor)::: 视图中。</span><span class="sxs-lookup"><span data-stu-id="12508-237">Add the following code to your :::no-loc(Razor)::: view of choice.</span></span> <span data-ttu-id="12508-238">在此示例中，使用了 About.cshtml。</span><span class="sxs-lookup"><span data-stu-id="12508-238">*About.cshtml* is used in this example.</span></span>

[!code-cshtml[](localization/sample/2.x/POLocalization/Views/Home/About.cshtml)]

<span data-ttu-id="12508-239">注入了 `IViewLocalizer` 实例并将其用于转换文本“Hello world！”。</span><span class="sxs-lookup"><span data-stu-id="12508-239">An `IViewLocalizer` instance is injected and used to translate the text "Hello world!".</span></span>

### <a name="creating-a-po-file"></a><span data-ttu-id="12508-240">创建 PO 文件</span><span class="sxs-lookup"><span data-stu-id="12508-240">Creating a PO file</span></span>

<span data-ttu-id="12508-241">在应用程序根文件夹中创建名为 \<culture code>.po 的文件。</span><span class="sxs-lookup"><span data-stu-id="12508-241">Create a file named *\<culture code>.po* in your application root folder.</span></span> <span data-ttu-id="12508-242">在此示例中，文件名为 fr.po，因为使用了法语：</span><span class="sxs-lookup"><span data-stu-id="12508-242">In this example, the file name is *fr.po* because the French language is used:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

<span data-ttu-id="12508-243">此文件存储了要转换的字符串和已转换为法语的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-243">This file stores both the string to translate and the French-translated string.</span></span> <span data-ttu-id="12508-244">如有必要，转换将还原为其父级区域性。</span><span class="sxs-lookup"><span data-stu-id="12508-244">Translations revert to their parent culture, if necessary.</span></span> <span data-ttu-id="12508-245">在此示例中，如果请求的区域性为 `fr-FR` 或 `fr-CA`，则使用 fr.po 文件。</span><span class="sxs-lookup"><span data-stu-id="12508-245">In this example, the *fr.po* file is used if the requested culture is `fr-FR` or `fr-CA`.</span></span>

### <a name="testing-the-application"></a><span data-ttu-id="12508-246">测试应用程序</span><span class="sxs-lookup"><span data-stu-id="12508-246">Testing the application</span></span>

<span data-ttu-id="12508-247">运行应用程序并导航到 URL `/Home/About`。</span><span class="sxs-lookup"><span data-stu-id="12508-247">Run your application, and navigate to the URL `/Home/About`.</span></span> <span data-ttu-id="12508-248">此时将显示文本 Hello world!</span><span class="sxs-lookup"><span data-stu-id="12508-248">The text **Hello world!**</span></span> <span data-ttu-id="12508-249">。</span><span class="sxs-lookup"><span data-stu-id="12508-249">is displayed.</span></span>

<span data-ttu-id="12508-250">导航到 URL `/Home/About?culture=fr-FR`。</span><span class="sxs-lookup"><span data-stu-id="12508-250">Navigate to the URL `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="12508-251">此时将显示文本 Bonjour le monde!</span><span class="sxs-lookup"><span data-stu-id="12508-251">The text **Bonjour le monde!**</span></span> <span data-ttu-id="12508-252">。</span><span class="sxs-lookup"><span data-stu-id="12508-252">is displayed.</span></span>

## <a name="pluralization"></a><span data-ttu-id="12508-253">复数形式</span><span class="sxs-lookup"><span data-stu-id="12508-253">Pluralization</span></span>

<span data-ttu-id="12508-254">PO 文件支持复数形式，在相同字符串需要基于基数以不同方式进行转换时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="12508-254">PO files support pluralization forms, which is useful when the same string needs to be translated differently based on a cardinality.</span></span> <span data-ttu-id="12508-255">此任务较为复杂，因为每种语言均定义了自定义规则，以基于基数选择要使用的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-255">This task is made complicated by the fact that each language defines custom rules to select which string to use based on the cardinality.</span></span>

<span data-ttu-id="12508-256">Orchard 本地化包提供了一个 API 以自动调用这些不同的复数形式。</span><span class="sxs-lookup"><span data-stu-id="12508-256">The Orchard Localization package provides an API to invoke these different plural forms automatically.</span></span>

### <a name="creating-pluralization-po-files"></a><span data-ttu-id="12508-257">创建复数形式 PO 文件</span><span class="sxs-lookup"><span data-stu-id="12508-257">Creating pluralization PO files</span></span>

<span data-ttu-id="12508-258">将以下内容添加到前面所述的 fr.po 文件：</span><span class="sxs-lookup"><span data-stu-id="12508-258">Add the following content to the previously mentioned *fr.po* file:</span></span>

```text
msgid "There is one item."
msgid_plural "There are {0} items."
msgstr[0] "Il y a un élément."
msgstr[1] "Il y a {0} éléments."
```

<span data-ttu-id="12508-259">有关此示例中每个条目所表示的内容的说明，请参阅[什么是 PO 文件？](#what-is-a-po-file)。</span><span class="sxs-lookup"><span data-stu-id="12508-259">See [What is a PO file?](#what-is-a-po-file) for an explanation of what each entry in this example represents.</span></span>

### <a name="adding-a-language-using-different-pluralization-forms"></a><span data-ttu-id="12508-260">使用不同的复数形式添加语言</span><span class="sxs-lookup"><span data-stu-id="12508-260">Adding a language using different pluralization forms</span></span>

<span data-ttu-id="12508-261">前面的示例中使用了英语和法语字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-261">English and French strings were used in the previous example.</span></span> <span data-ttu-id="12508-262">英语和法语只有两种复数形式且拥有相同形式规则，即一的基数映射到第一种复数形式。</span><span class="sxs-lookup"><span data-stu-id="12508-262">English and French have only two pluralization forms and share the same form rules, which is that a cardinality of one is mapped to the first plural form.</span></span> <span data-ttu-id="12508-263">任何其他基数映射到第二种复数形式。</span><span class="sxs-lookup"><span data-stu-id="12508-263">Any other cardinality is mapped to the second plural form.</span></span>

<span data-ttu-id="12508-264">并非所有语言都拥有相同的规则。</span><span class="sxs-lookup"><span data-stu-id="12508-264">Not all languages share the same rules.</span></span> <span data-ttu-id="12508-265">捷克语就是一个例子，它具有三种复数形式。</span><span class="sxs-lookup"><span data-stu-id="12508-265">This is illustrated with the Czech language, which has three plural forms.</span></span>

<span data-ttu-id="12508-266">如下所示，创建 `cs.po` 文件，并记下复数形式如何需要三种不同转换：</span><span class="sxs-lookup"><span data-stu-id="12508-266">Create the `cs.po` file as follows, and note how the pluralization needs three different translations:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/cs.po)]

<span data-ttu-id="12508-267">若要接受捷克语本地化，请将 `"cs"` 添加到 `ConfigureServices` 方法中受支持的区域性列表：</span><span class="sxs-lookup"><span data-stu-id="12508-267">To accept Czech localizations, add `"cs"` to the list of supported cultures in the `ConfigureServices` method:</span></span>

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

<span data-ttu-id="12508-268">编辑 Views/Home/About.cshtml 文件以呈现一些基数的已本地化复数形式字符串：</span><span class="sxs-lookup"><span data-stu-id="12508-268">Edit the *Views/Home/About.cshtml* file to render localized, plural strings for several cardinalities:</span></span>

```cshtml
<p>@Localizer.Plural(1, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(2, "There is one item.", "There are {0} items.")</p>
<p>@Localizer.Plural(5, "There is one item.", "There are {0} items.")</p>
```

<span data-ttu-id="12508-269">**注意：** 在实际方案中，将使用变量表示计数。</span><span class="sxs-lookup"><span data-stu-id="12508-269">**Note:** In a real world scenario, a variable would be used to represent the count.</span></span> <span data-ttu-id="12508-270">此处，我们通过三个不同的值重复相同代码，以公开非常特定的事例。</span><span class="sxs-lookup"><span data-stu-id="12508-270">Here, we repeat the same code with three different values to expose a very specific case.</span></span>

<span data-ttu-id="12508-271">切换区域性时，将显示如下内容：</span><span class="sxs-lookup"><span data-stu-id="12508-271">Upon switching cultures, you see the following:</span></span>

<span data-ttu-id="12508-272">对于 `/Home/About`：</span><span class="sxs-lookup"><span data-stu-id="12508-272">For `/Home/About`:</span></span>

```html
There is one item.
There are 2 items.
There are 5 items.
```

<span data-ttu-id="12508-273">对于 `/Home/About?culture=fr`：</span><span class="sxs-lookup"><span data-stu-id="12508-273">For `/Home/About?culture=fr`:</span></span>

```html
Il y a un élément.
Il y a 2 éléments.
Il y a 5 éléments.
```

<span data-ttu-id="12508-274">对于 `/Home/About?culture=cs`：</span><span class="sxs-lookup"><span data-stu-id="12508-274">For `/Home/About?culture=cs`:</span></span>

```html
Existuje jedna položka.
Existují 2 položky.
Existuje 5 položek.
```

<span data-ttu-id="12508-275">请注意，对于捷克语区域性，这三种转换各不相同。</span><span class="sxs-lookup"><span data-stu-id="12508-275">Note that for the Czech culture, the three translations are different.</span></span> <span data-ttu-id="12508-276">对于最后两个已转换字符串，法语和英语区域性具有相同构造。</span><span class="sxs-lookup"><span data-stu-id="12508-276">The French and English cultures share the same construction for the two last translated strings.</span></span>

## <a name="advanced-tasks"></a><span data-ttu-id="12508-277">高级任务</span><span class="sxs-lookup"><span data-stu-id="12508-277">Advanced tasks</span></span>

### <a name="contextualizing-strings"></a><span data-ttu-id="12508-278">将字符串置于上下文中理解</span><span class="sxs-lookup"><span data-stu-id="12508-278">Contextualizing strings</span></span>

<span data-ttu-id="12508-279">应用程序通常包含要在多个位置中进行转换的字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-279">Applications often contain the strings to be translated in several places.</span></span> <span data-ttu-id="12508-280">在应用中的特定位置（:::no-loc(Razor)::: 视图或类文件），相同字符串可能具有不同转换。</span><span class="sxs-lookup"><span data-stu-id="12508-280">The same string may have a different translation in certain locations within an app (:::no-loc(Razor)::: views or class files).</span></span> <span data-ttu-id="12508-281">PO 文件支持文件上下文概念，此概念可用于对所表示的字符串进行分类。</span><span class="sxs-lookup"><span data-stu-id="12508-281">A PO file supports the notion of a file context, which can be used to categorize the string being represented.</span></span> <span data-ttu-id="12508-282">使用文件上下文，可将字符串进行不同转换，具体取决于文件上下文（或缺乏文件上下文）。</span><span class="sxs-lookup"><span data-stu-id="12508-282">Using a file context, a string can be translated differently, depending on the file context (or lack of a file context).</span></span>

<span data-ttu-id="12508-283">PO 本地化服务使用完整类的名称或转换字符串时使用的视图。</span><span class="sxs-lookup"><span data-stu-id="12508-283">The PO localization services use the name of the full class or the view that's used when translating a string.</span></span> <span data-ttu-id="12508-284">这通过在 `msgctxt` 条目上设置值来完成。</span><span class="sxs-lookup"><span data-stu-id="12508-284">This is accomplished by setting the value on the `msgctxt` entry.</span></span>

<span data-ttu-id="12508-285">请考虑对以前的 fr.po 示例作一点小小的补充。</span><span class="sxs-lookup"><span data-stu-id="12508-285">Consider a minor addition to the previous *fr.po* example.</span></span> <span data-ttu-id="12508-286">可通过设置保留的 `msgctxt` 条目的值将位于 Views/Home/About.cshtml 的 :::no-loc(Razor)::: 视图定义为文件上下文：</span><span class="sxs-lookup"><span data-stu-id="12508-286">A :::no-loc(Razor)::: view located at *Views/Home/About.cshtml* can be defined as the file context by setting the reserved `msgctxt` entry's value:</span></span>

```text
msgctxt "Views.Home.About"
msgid "Hello world!"
msgstr "Bonjour le monde!"
```

<span data-ttu-id="12508-287">这样设置 `msgctxt` 后，导航到 `/Home/About?culture=fr-FR` 时将发生文本转换。</span><span class="sxs-lookup"><span data-stu-id="12508-287">With the `msgctxt` set as such, text translation occurs when navigating to `/Home/About?culture=fr-FR`.</span></span> <span data-ttu-id="12508-288">而导航到 `/Home/Contact?culture=fr-FR` 时，则不发生转换。</span><span class="sxs-lookup"><span data-stu-id="12508-288">The translation won't occur when navigating to `/Home/Contact?culture=fr-FR`.</span></span>

<span data-ttu-id="12508-289">当没有特定条目与给定文件上下文相匹配时，Orchard Core 的回退机制将在没有上下文的情况下查找适当的 PO 文件。</span><span class="sxs-lookup"><span data-stu-id="12508-289">When no specific entry is matched with a given file context, Orchard Core's fallback mechanism looks for an appropriate PO file without a context.</span></span> <span data-ttu-id="12508-290">假设不存在针对 Views/Home/Contact.cshtml 定义的特定文件上下文，导航到 `/Home/Contact?culture=fr-FR`，加载 PO 文件，如：</span><span class="sxs-lookup"><span data-stu-id="12508-290">Assuming there's no specific file context defined for *Views/Home/Contact.cshtml* , navigating to `/Home/Contact?culture=fr-FR` loads a PO file such as:</span></span>

[!code-text[](localization/sample/2.x/POLocalization/fr.po)]

### <a name="changing-the-location-of-po-files"></a><span data-ttu-id="12508-291">更改 PO 文件的位置</span><span class="sxs-lookup"><span data-stu-id="12508-291">Changing the location of PO files</span></span>

<span data-ttu-id="12508-292">可以在 `ConfigureServices` 中更改 PO 文件的默认位置：</span><span class="sxs-lookup"><span data-stu-id="12508-292">The default location of PO files can be changed in `ConfigureServices`:</span></span>

```csharp
services.AddPortableObjectLocalization(options => options.ResourcesPath = "Localization");
```

<span data-ttu-id="12508-293">在此示例中，从本地化文件夹加载 PO 文件。</span><span class="sxs-lookup"><span data-stu-id="12508-293">In this example, the PO files are loaded from the *Localization* folder.</span></span>

### <a name="implementing-a-custom-logic-for-finding-localization-files"></a><span data-ttu-id="12508-294">实现用于查找本地化文件的自定义逻辑</span><span class="sxs-lookup"><span data-stu-id="12508-294">Implementing a custom logic for finding localization files</span></span>

<span data-ttu-id="12508-295">当需要更复杂的逻辑以查找 PO 文件时，可实现 `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` 接口并将其注册为服务。</span><span class="sxs-lookup"><span data-stu-id="12508-295">When more complex logic is needed to locate PO files, the `OrchardCore.Localization.PortableObject.ILocalizationFileLocationProvider` interface can be implemented and registered as a service.</span></span> <span data-ttu-id="12508-296">在可将 PO 文件存储于不同位置或在文件夹层次结构中找到文件时，这非常有用。</span><span class="sxs-lookup"><span data-stu-id="12508-296">This is useful when PO files can be stored in varying locations or when the files have to be found within a hierarchy of folders.</span></span>

### <a name="using-a-different-default-pluralized-language"></a><span data-ttu-id="12508-297">使用不同默认复数形式语言</span><span class="sxs-lookup"><span data-stu-id="12508-297">Using a different default pluralized language</span></span>

<span data-ttu-id="12508-298">此包包含特定于两种复数形式的 `Plural` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="12508-298">The package includes a `Plural` extension method that's specific to two plural forms.</span></span> <span data-ttu-id="12508-299">对于需要更多复数形式的语言，请创建扩展方法。</span><span class="sxs-lookup"><span data-stu-id="12508-299">For languages requiring more plural forms, create an extension method.</span></span> <span data-ttu-id="12508-300">通过扩展方法，无需提供默认语言的任何本地化文件 &mdash; 可在代码中直接使用原始字符串。</span><span class="sxs-lookup"><span data-stu-id="12508-300">With an extension method, you won't need to provide any localization file for the default language &mdash; the original strings are already available directly in the code.</span></span>

<span data-ttu-id="12508-301">可使用更加广泛的、接受转换的字符串数组的 `Plural(int count, string[] pluralForms, params object[] arguments)` 重载。</span><span class="sxs-lookup"><span data-stu-id="12508-301">You can use the more generic `Plural(int count, string[] pluralForms, params object[] arguments)` overload which accepts a string array of translations.</span></span>

::: moniker-end