---
title: 第 8 部分：添加验证
author: rick-anderson
description: Razor 页面教程系列的第 8 部分。
ms.author: riande
ms.custom: mvc
ms.date: 09/29/2020
no-loc:
- Index
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f155922c9cb5ea7fdbad0963221ceddd19f4fe60
ms.sourcegitcommit: db0a6eb0be7bd7f22810a71fe9bf30e957fd116a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96419949"
---
# <a name="part-8-of-tutorial-series-on-no-locrazor-pages"></a><span data-ttu-id="dab2c-103">Razor 页面教程系列的第 8 部分。</span><span class="sxs-lookup"><span data-stu-id="dab2c-103">Part 8 of tutorial series on Razor Pages.</span></span>

<span data-ttu-id="dab2c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="dab2c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="dab2c-105">本部分中向 `Movie` 模型添加了验证逻辑。</span><span class="sxs-lookup"><span data-stu-id="dab2c-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="dab2c-106">每当用户创建或编辑电影时，都会强制执行验证规则。</span><span class="sxs-lookup"><span data-stu-id="dab2c-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="dab2c-107">验证</span><span class="sxs-lookup"><span data-stu-id="dab2c-107">Validation</span></span>

<span data-ttu-id="dab2c-108">软件开发的一个关键原则被称为 [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself)（即“不要自我重复”）  。</span><span class="sxs-lookup"><span data-stu-id="dab2c-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D** on't **R** epeat **Y** ourself").</span></span> <span data-ttu-id="dab2c-109">Razor 页面鼓励进行仅指定一次功能的开发，且功能在整个应用中反映。</span><span class="sxs-lookup"><span data-stu-id="dab2c-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="dab2c-110">DRY 可以帮助：</span><span class="sxs-lookup"><span data-stu-id="dab2c-110">DRY can help:</span></span>

* <span data-ttu-id="dab2c-111">减少应用中的代码量。</span><span class="sxs-lookup"><span data-stu-id="dab2c-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="dab2c-112">使代码更加不易出错，且更易于测试和维护。</span><span class="sxs-lookup"><span data-stu-id="dab2c-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="dab2c-113">Razor 页面和实体框架提供的验证支持是 DRY 原则的极佳示例：</span><span class="sxs-lookup"><span data-stu-id="dab2c-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle:</span></span>

* <span data-ttu-id="dab2c-114">验证规则在模型类中的某处以声明方式指定。</span><span class="sxs-lookup"><span data-stu-id="dab2c-114">Validation rules are declaratively specified in one place, in the model class.</span></span>
* <span data-ttu-id="dab2c-115">规则在应用的所有位置强制执行。</span><span class="sxs-lookup"><span data-stu-id="dab2c-115">Rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="dab2c-116">将验证规则添加到电影模型</span><span class="sxs-lookup"><span data-stu-id="dab2c-116">Add validation rules to the movie model</span></span>

<span data-ttu-id="dab2c-117">`DataAnnotations` 命名空间提供以下内容：</span><span class="sxs-lookup"><span data-stu-id="dab2c-117">The `DataAnnotations` namespace provides:</span></span>

* <span data-ttu-id="dab2c-118">一组内置验证特性，可通过声明方式应用于类或属性。</span><span class="sxs-lookup"><span data-stu-id="dab2c-118">A set of built-in validation attributes that are applied declaratively to a class or property.</span></span>
* <span data-ttu-id="dab2c-119">`[DataType]` 等格式特性，这些特性可帮助进行格式设置，但不提供任何验证。</span><span class="sxs-lookup"><span data-stu-id="dab2c-119">Formatting attributes like `[DataType]` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="dab2c-120">更新 `Movie` 类以使用内置的 `[Required]`、`[StringLength]`、`[RegularExpression]` 和 `[Range]` 验证特性。</span><span class="sxs-lookup"><span data-stu-id="dab2c-120">Update the `Movie` class to take advantage of the built-in `[Required]`, `[StringLength]`, `[RegularExpression]`, and `[Range]` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="dab2c-121">验证特性指定要对应用这些特性的模型属性强制执行的行为：</span><span class="sxs-lookup"><span data-stu-id="dab2c-121">The validation attributes specify behavior to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="dab2c-122">`[Required]` 和 `[MinimumLength]` 特性指示属性必须具有一个值。</span><span class="sxs-lookup"><span data-stu-id="dab2c-122">The `[Required]` and `[MinimumLength]` attributes indicate that a property must have a value.</span></span> <span data-ttu-id="dab2c-123">不阻止用户输入空格来满足此验证。</span><span class="sxs-lookup"><span data-stu-id="dab2c-123">Nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="dab2c-124">`[RegularExpression]` 特性用于限制可输入的字符。</span><span class="sxs-lookup"><span data-stu-id="dab2c-124">The `[RegularExpression]` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="dab2c-125">在上述代码中，`Genre`：</span><span class="sxs-lookup"><span data-stu-id="dab2c-125">In the preceding code, `Genre`:</span></span>

  * <span data-ttu-id="dab2c-126">只能使用字母。</span><span class="sxs-lookup"><span data-stu-id="dab2c-126">Must only use letters.</span></span>
  * <span data-ttu-id="dab2c-127">第一个字母必须为大写。</span><span class="sxs-lookup"><span data-stu-id="dab2c-127">The first letter is required to be uppercase.</span></span> <span data-ttu-id="dab2c-128">不允许使用空格、数字和特殊字符。</span><span class="sxs-lookup"><span data-stu-id="dab2c-128">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="dab2c-129">`RegularExpression` `Rating`：</span><span class="sxs-lookup"><span data-stu-id="dab2c-129">The `RegularExpression` `Rating`:</span></span>

  * <span data-ttu-id="dab2c-130">要求第一个字符为大写字母。</span><span class="sxs-lookup"><span data-stu-id="dab2c-130">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="dab2c-131">允许在后续空格中使用特殊字符和数字。</span><span class="sxs-lookup"><span data-stu-id="dab2c-131">Allows special characters and numbers in subsequent spaces.</span></span> <span data-ttu-id="dab2c-132">“PG-13”对“分级”有效，但对于“`Genre`”无效。</span><span class="sxs-lookup"><span data-stu-id="dab2c-132">"PG-13" is valid for a rating, but fails for a `Genre`.</span></span>

* <span data-ttu-id="dab2c-133">`[Range]` 特性将值限制在指定的范围内。</span><span class="sxs-lookup"><span data-stu-id="dab2c-133">The `[Range]` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="dab2c-134">`[StringLength]` 特性可以设置字符串属性的最大长度，以及可选的最小长度。</span><span class="sxs-lookup"><span data-stu-id="dab2c-134">The `[StringLength]` attribute can set a maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="dab2c-135">从本质上来说，需要值类型（如 `decimal`、`int`、`float`、`DateTime`），但不需要 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="dab2c-135">Value types, such as `decimal`, `int`, `float`, `DateTime`, are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="dab2c-136">上述验证规则用于进行演示，它们并不是适用于生产系统的最佳规则。</span><span class="sxs-lookup"><span data-stu-id="dab2c-136">The preceding validation rules are used for demonstration, they are not optimal for a production system.</span></span> <span data-ttu-id="dab2c-137">例如，前面的规则会阻止用户仅使用两个字符输入一部电影，并且不允许 `Genre` 中有特殊字符。</span><span class="sxs-lookup"><span data-stu-id="dab2c-137">For example, the preceding prevents entering a movie with only two chars and doesn't allow special characters in `Genre`.</span></span>

<span data-ttu-id="dab2c-138">让 ASP.NET Core 强制自动执行验证规则有助于：</span><span class="sxs-lookup"><span data-stu-id="dab2c-138">Having validation rules automatically enforced by ASP.NET Core helps:</span></span>

* <span data-ttu-id="dab2c-139">提升应用的可靠性。</span><span class="sxs-lookup"><span data-stu-id="dab2c-139">Helps make the app more robust.</span></span>
* <span data-ttu-id="dab2c-140">减少将无效数据保存到数据库的几率。</span><span class="sxs-lookup"><span data-stu-id="dab2c-140">Reduce chances of saving invalid data to the database.</span></span>

### <a name="validation-error-ui-in-no-locrazor-pages"></a><span data-ttu-id="dab2c-141">Razor 页面中的验证错误 UI</span><span class="sxs-lookup"><span data-stu-id="dab2c-141">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="dab2c-142">运行应用并导航到“页面/电影”。</span><span class="sxs-lookup"><span data-stu-id="dab2c-142">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="dab2c-143">选择“新建”链接。</span><span class="sxs-lookup"><span data-stu-id="dab2c-143">Select the **Create New** link.</span></span> <span data-ttu-id="dab2c-144">使用无效值填写表单。</span><span class="sxs-lookup"><span data-stu-id="dab2c-144">Complete the form with some invalid values.</span></span> <span data-ttu-id="dab2c-145">当 jQuery 客户端验证检测到错误时，会显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="dab2c-145">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![带有多个 jQuery 客户端验证错误的电影视图表单](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="dab2c-147">请注意表单如何自动呈现每个包含无效值的字段中的验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="dab2c-147">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="dab2c-148">客户端（使用 JavaScript 和 jQuery）和服务器端（若用户禁用 JavaScript）都必定会遇到这些错误。</span><span class="sxs-lookup"><span data-stu-id="dab2c-148">The errors are enforced both client-side, using JavaScript and jQuery, and server-side, when a user has JavaScript disabled.</span></span>

<span data-ttu-id="dab2c-149">一项重要优势是，无需在“创建”或“编辑”页面中更改代码。</span><span class="sxs-lookup"><span data-stu-id="dab2c-149">A significant benefit is that **no** code changes were necessary in the Create or Edit pages.</span></span> <span data-ttu-id="dab2c-150">在模型应用数据注释后，即已启用验证 UI。</span><span class="sxs-lookup"><span data-stu-id="dab2c-150">Once data annotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="dab2c-151">本教程中创建的 Razor 页面自动选取了验证规则（使用 `Movie` 模型类的属性上的验证特性）。</span><span class="sxs-lookup"><span data-stu-id="dab2c-151">The Razor Pages created in this tutorial automatically picked up the validation rules, using validation attributes on the properties of the `Movie` model class.</span></span> <span data-ttu-id="dab2c-152">使用“编辑”页面测试验证后，即已应用相同验证。</span><span class="sxs-lookup"><span data-stu-id="dab2c-152">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="dab2c-153">存在客户端验证错误时，不会将表单数据发布到服务器。</span><span class="sxs-lookup"><span data-stu-id="dab2c-153">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="dab2c-154">请通过以下一种或多种方法验证是否未发布表单数据：</span><span class="sxs-lookup"><span data-stu-id="dab2c-154">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="dab2c-155">在 `OnPostAsync` 方法中放置一个断点。</span><span class="sxs-lookup"><span data-stu-id="dab2c-155">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="dab2c-156">通过选择“创建”或“保存”来提交表单 。</span><span class="sxs-lookup"><span data-stu-id="dab2c-156">Submit the form by selecting **Create** or **Save**.</span></span> <span data-ttu-id="dab2c-157">从未命中断点。</span><span class="sxs-lookup"><span data-stu-id="dab2c-157">The break point is never hit.</span></span>
* <span data-ttu-id="dab2c-158">使用 [Fiddler 工具](https://www.telerik.com/fiddler)。</span><span class="sxs-lookup"><span data-stu-id="dab2c-158">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="dab2c-159">使用浏览器开发人员工具监视网络流量。</span><span class="sxs-lookup"><span data-stu-id="dab2c-159">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="dab2c-160">服务器端验证</span><span class="sxs-lookup"><span data-stu-id="dab2c-160">Server-side validation</span></span>

<span data-ttu-id="dab2c-161">在浏览器中禁用 JavaScript 后，提交出错表单将发布到服务器。</span><span class="sxs-lookup"><span data-stu-id="dab2c-161">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="dab2c-162">（可选）测试服务器端验证：</span><span class="sxs-lookup"><span data-stu-id="dab2c-162">Optional, test server-side validation:</span></span>

1. <span data-ttu-id="dab2c-163">在浏览器中禁用 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="dab2c-163">Disable JavaScript in the browser.</span></span> <span data-ttu-id="dab2c-164">可以使用浏览器的开发人员工具禁用 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="dab2c-164">JavaScript can be disabled using browser's developer tools.</span></span> <span data-ttu-id="dab2c-165">如果无法在此浏览器中禁用 JavaScript，请尝试其他浏览器。</span><span class="sxs-lookup"><span data-stu-id="dab2c-165">If JavaScript cannot be disabled in the browser, try another browser.</span></span>
1. <span data-ttu-id="dab2c-166">在“创建”或“编辑”页面的 `OnPostAsync` 方法中设置断点。</span><span class="sxs-lookup"><span data-stu-id="dab2c-166">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
1. <span data-ttu-id="dab2c-167">提交包含无效数据的表单。</span><span class="sxs-lookup"><span data-stu-id="dab2c-167">Submit a form with invalid data.</span></span>
1. <span data-ttu-id="dab2c-168">验证模型状态是否无效：</span><span class="sxs-lookup"><span data-stu-id="dab2c-168">Verify the model state is invalid:</span></span>

   ```csharp
    if (!ModelState.IsValid)
    {
       return Page();
    }
   ```
  
<span data-ttu-id="dab2c-169">或者可以[禁用服务器上的客户端验证](xref:mvc/models/validation#disable-client-side-validation)。</span><span class="sxs-lookup"><span data-stu-id="dab2c-169">Alternatively, [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="dab2c-170">以下代码显示了之前在本教程中设定其基架的“Create.cshtml”的一部分。</span><span class="sxs-lookup"><span data-stu-id="dab2c-170">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="dab2c-171">“创建”和“编辑”页面使用它来实现以下操作：</span><span class="sxs-lookup"><span data-stu-id="dab2c-171">It's used by the Create and Edit pages to:</span></span>

* <span data-ttu-id="dab2c-172">显示初始表单。</span><span class="sxs-lookup"><span data-stu-id="dab2c-172">Display the initial form.</span></span>
* <span data-ttu-id="dab2c-173">在出现错误时重现显示此表单。</span><span class="sxs-lookup"><span data-stu-id="dab2c-173">Redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="dab2c-174">[输入标记帮助程序](xref:mvc/views/working-with-forms)使用 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 特性并在客户端生成 jQuery 验证所需的 HTML 特性。</span><span class="sxs-lookup"><span data-stu-id="dab2c-174">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="dab2c-175">[验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)用于显示验证错误。</span><span class="sxs-lookup"><span data-stu-id="dab2c-175">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="dab2c-176">有关详细信息，请参阅[验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="dab2c-176">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="dab2c-177">“创建”和“编辑”页面中没有验证规则。</span><span class="sxs-lookup"><span data-stu-id="dab2c-177">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="dab2c-178">仅可在 `Movie` 类中指定验证规则和错误字符串。</span><span class="sxs-lookup"><span data-stu-id="dab2c-178">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="dab2c-179">这些验证规则将自动应用于编辑 `Movie` 模型的 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="dab2c-179">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="dab2c-180">需要更改验证逻辑时，也只能在该模型中更改。</span><span class="sxs-lookup"><span data-stu-id="dab2c-180">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="dab2c-181">将始终在整个应用程序中应用验证（在一处定义验证逻辑）。</span><span class="sxs-lookup"><span data-stu-id="dab2c-181">Validation is applied consistently throughout the application, validation logic is defined in one place.</span></span> <span data-ttu-id="dab2c-182">单处验证有助于保持代码干净，且更易于维护和更新。</span><span class="sxs-lookup"><span data-stu-id="dab2c-182">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="use-datatype-attributes"></a><span data-ttu-id="dab2c-183">使用 DataType 特性</span><span class="sxs-lookup"><span data-stu-id="dab2c-183">Use DataType Attributes</span></span>

<span data-ttu-id="dab2c-184">检查 `Movie` 类。</span><span class="sxs-lookup"><span data-stu-id="dab2c-184">Examine the `Movie` class.</span></span> <span data-ttu-id="dab2c-185">除了一组内置的验证特性，`System.ComponentModel.DataAnnotations` 命名空间还提供格式特性。</span><span class="sxs-lookup"><span data-stu-id="dab2c-185">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="dab2c-186">`[DataType]` 特性应用于 `ReleaseDate` 和 `Price` 属性。</span><span class="sxs-lookup"><span data-stu-id="dab2c-186">The `[DataType]` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="dab2c-187">`[DataType]` 特性提供以下内容：</span><span class="sxs-lookup"><span data-stu-id="dab2c-187">The `[DataType]` attributes provide:</span></span>

* <span data-ttu-id="dab2c-188">提供提示，供视图引擎设置数据的格式。</span><span class="sxs-lookup"><span data-stu-id="dab2c-188">Hints for the view engine to format the data.</span></span>
* <span data-ttu-id="dab2c-189">提供属性，例如 URL 的 `<a>` 以及电子邮件的 `<a href="mailto:EmailAddress.com">`。</span><span class="sxs-lookup"><span data-stu-id="dab2c-189">Supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email.</span></span>

<span data-ttu-id="dab2c-190">使用 `[RegularExpression]` 特性验证数据的格式。</span><span class="sxs-lookup"><span data-stu-id="dab2c-190">Use the `[RegularExpression]` attribute to validate the format of the data.</span></span> <span data-ttu-id="dab2c-191">`[DataType]` 属性用于指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="dab2c-191">The `[DataType]` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="dab2c-192">`[DataType]` 特性不是验证特性。</span><span class="sxs-lookup"><span data-stu-id="dab2c-192">`[DataType]` attributes aren't validation attributes.</span></span> <span data-ttu-id="dab2c-193">示例应用程序中仅显示日期，不显示时间。</span><span class="sxs-lookup"><span data-stu-id="dab2c-193">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="dab2c-194">`DataType` 枚举提供多种数据类型，如 `Date`、`Time`、`PhoneNumber`、`Currency`、`EmailAddress` 等。</span><span class="sxs-lookup"><span data-stu-id="dab2c-194">The `DataType` enumeration provides many data types, such as `Date`, `Time`, `PhoneNumber`, `Currency`, `EmailAddress`, and more.</span></span> 

<span data-ttu-id="dab2c-195">`[DataType]` 特性：</span><span class="sxs-lookup"><span data-stu-id="dab2c-195">The `[DataType]` attributes:</span></span>

* <span data-ttu-id="dab2c-196">可以使应用程序自动提供类型特定的功能。</span><span class="sxs-lookup"><span data-stu-id="dab2c-196">Can enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="dab2c-197">例如，可为 `DataType.EmailAddress` 创建 `mailto:` 链接。</span><span class="sxs-lookup"><span data-stu-id="dab2c-197">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span>
* <span data-ttu-id="dab2c-198">可在支持 HTML5 的浏览器中提供 `DataType.Date` 日期选择器。</span><span class="sxs-lookup"><span data-stu-id="dab2c-198">Can provide a date selector `DataType.Date` in browsers that support HTML5.</span></span>
* <span data-ttu-id="dab2c-199">发出 HTML 5 `data-`（读作 data dash）特性供 HTML 5 浏览器使用。</span><span class="sxs-lookup"><span data-stu-id="dab2c-199">Emit HTML 5 `data-`, pronounced "data dash", attributes that HTML 5 browsers consume.</span></span>
* <span data-ttu-id="dab2c-200">不提供任何验证。</span><span class="sxs-lookup"><span data-stu-id="dab2c-200">Do **not** provide any validation.</span></span>

<span data-ttu-id="dab2c-201">`DataType.Date` 不指定显示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="dab2c-201">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="dab2c-202">默认情况下，数据字段根据基于服务器的 `CultureInfo` 的默认格式进行显示。</span><span class="sxs-lookup"><span data-stu-id="dab2c-202">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="dab2c-203">要使 Entity Framework Core 能将 `Price` 正确地映射到数据库中的货币，则必须使用 `[Column(TypeName = "decimal(18, 2)")]` 数据注释。</span><span class="sxs-lookup"><span data-stu-id="dab2c-203">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="dab2c-204">有关详细信息，请参阅[数据类型](/ef/core/modeling/relational/data-types)。</span><span class="sxs-lookup"><span data-stu-id="dab2c-204">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="dab2c-205">`[DisplayFormat]` 特性用于显式指定日期格式：</span><span class="sxs-lookup"><span data-stu-id="dab2c-205">The `[DisplayFormat]` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="dab2c-206">`ApplyFormatInEditMode` 设置用于指定在显示值进行编辑时将应用格式。</span><span class="sxs-lookup"><span data-stu-id="dab2c-206">The `ApplyFormatInEditMode` setting specifies that the formatting will be applied when the value is displayed for editing.</span></span> <span data-ttu-id="dab2c-207">某些字段可能不需要此行为。</span><span class="sxs-lookup"><span data-stu-id="dab2c-207">That behavior may not be wanted for some fields.</span></span> <span data-ttu-id="dab2c-208">例如，在货币值中，可能不希望编辑 UI 中存在货币符号。</span><span class="sxs-lookup"><span data-stu-id="dab2c-208">For example, in currency values, the currency symbol is usually not wanted in the edit UI.</span></span>

<span data-ttu-id="dab2c-209">可单独使用 `[DisplayFormat]` 特性，但通常建议使用 `[DataType]` 特性。</span><span class="sxs-lookup"><span data-stu-id="dab2c-209">The `[DisplayFormat]` attribute can be used by itself, but it's generally a good idea to use the `[DataType]` attribute.</span></span> <span data-ttu-id="dab2c-210">`[DataType]` 特性按照数据在屏幕上的呈现方式传达数据的语义。</span><span class="sxs-lookup"><span data-stu-id="dab2c-210">The `[DataType]` attribute conveys the semantics of the data as opposed to how to render it on a screen.</span></span> <span data-ttu-id="dab2c-211">`[DataType]` 特性可提供 `[DisplayFormat]` 所不具有的以下优点：</span><span class="sxs-lookup"><span data-stu-id="dab2c-211">The `[DataType]` attribute provides the following benefits that aren't available with `[DisplayFormat]`:</span></span>

* <span data-ttu-id="dab2c-212">浏览器可启用 HTML5 功能（例如显示日历控件、区域设置适用的货币符号、电子邮件链接等）。</span><span class="sxs-lookup"><span data-stu-id="dab2c-212">The browser can enable HTML5 features, for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.</span></span>
* <span data-ttu-id="dab2c-213">默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。</span><span class="sxs-lookup"><span data-stu-id="dab2c-213">By default, the browser renders data using the correct format based on its locale.</span></span>
* <span data-ttu-id="dab2c-214">借助 `[DataType]` 特性，ASP.NET Core 框架可选择适当的字段模板来呈现数据。</span><span class="sxs-lookup"><span data-stu-id="dab2c-214">The `[DataType]` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="dab2c-215">单独使用时，`DisplayFormat` 特性将使用字符串模板。</span><span class="sxs-lookup"><span data-stu-id="dab2c-215">The `DisplayFormat`, if used by itself, uses the string template.</span></span>

<span data-ttu-id="dab2c-216">**注意**：jQuery 验证不适用于 `[Range]` 特性和 `DateTime`。</span><span class="sxs-lookup"><span data-stu-id="dab2c-216">**Note:** jQuery validation doesn't work with the `[Range]` attribute and `DateTime`.</span></span> <span data-ttu-id="dab2c-217">例如，以下代码将始终显示客户端验证错误，即便日期在指定的范围内：</span><span class="sxs-lookup"><span data-stu-id="dab2c-217">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="dab2c-218">最佳做法是避免在模型中编译固定日期，因此不推荐使用 `[Range]` 特性和 `DateTime`。</span><span class="sxs-lookup"><span data-stu-id="dab2c-218">It's a best practice to avoid compiling hard dates in models, so using the `[Range]` attribute and `DateTime` is discouraged.</span></span> <span data-ttu-id="dab2c-219">使用[配置](xref:fundamentals/configuration/index)指定日期范围和其他经常会更改的值，而不是在代码中指定它们。</span><span class="sxs-lookup"><span data-stu-id="dab2c-219">Use [Configuration](xref:fundamentals/configuration/index) for date ranges and other values that are subject to frequent change rather than specifying it in code.</span></span>

<span data-ttu-id="dab2c-220">以下代码显示组合在一行上的特性：</span><span class="sxs-lookup"><span data-stu-id="dab2c-220">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="dab2c-221">[Razor 页面和 EF Core 入门](xref:data/ef-rp/intro)显示了 Razor 页面的高级 EF Core 操作。</span><span class="sxs-lookup"><span data-stu-id="dab2c-221">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="dab2c-222">应用迁移</span><span class="sxs-lookup"><span data-stu-id="dab2c-222">Apply migrations</span></span>

<span data-ttu-id="dab2c-223">应用于类的 DataAnnotations 会更改架构。</span><span class="sxs-lookup"><span data-stu-id="dab2c-223">The DataAnnotations applied to the class changes the schema.</span></span> <span data-ttu-id="dab2c-224">例如，应用于 `Title` 字段的 DataAnnotations 会：</span><span class="sxs-lookup"><span data-stu-id="dab2c-224">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="dab2c-225">将字符数限制为 60。</span><span class="sxs-lookup"><span data-stu-id="dab2c-225">Limits the characters to 60.</span></span>
* <span data-ttu-id="dab2c-226">不允许使用 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="dab2c-226">Doesn't allow a `null` value.</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="dab2c-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="dab2c-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="dab2c-228">`Movie` 表当前具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="dab2c-228">The `Movie` table currently has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (MAX)  NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (MAX)  NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (MAX)  NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

<span data-ttu-id="dab2c-229">前面的架构更改不会导致 EF 引发异常。</span><span class="sxs-lookup"><span data-stu-id="dab2c-229">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="dab2c-230">不过，请创建迁移，使架构与模型保持一致。</span><span class="sxs-lookup"><span data-stu-id="dab2c-230">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="dab2c-231">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。 </span><span class="sxs-lookup"><span data-stu-id="dab2c-231">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="dab2c-232">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="dab2c-232">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="dab2c-233">`Update-Database` 运行 `New_DataAnnotations` 类的 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="dab2c-233">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="dab2c-234">检查 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="dab2c-234">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="dab2c-235">更新的 `Movie` 表具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="dab2c-235">The updated `Movie` table has the following schema:</span></span>

```sql
CREATE TABLE [dbo].[Movie] (
    [ID]          INT             IDENTITY (1, 1) NOT NULL,
    [Title]       NVARCHAR (60)   NOT NULL,
    [ReleaseDate] DATETIME2 (7)   NOT NULL,
    [Genre]       NVARCHAR (30)   NOT NULL,
    [Price]       DECIMAL (18, 2) NOT NULL,
    [Rating]      NVARCHAR (5)    NOT NULL,
    CONSTRAINT [PK_Movie] PRIMARY KEY CLUSTERED ([ID] ASC)
);
```

# <a name="visual-studio-code--visual-studio-for-mac"></a>[<span data-ttu-id="dab2c-236">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="dab2c-236">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="dab2c-237">SQLite 不需要迁移。</span><span class="sxs-lookup"><span data-stu-id="dab2c-237">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="dab2c-238">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="dab2c-238">Publish to Azure</span></span>

<span data-ttu-id="dab2c-239">若要了解如何部署到 Azure，请参阅[教程：使用 SQL 数据库在 Azure 中生成 ASP.NET Core 应用](/azure/app-service/tutorial-dotnetcore-sqldb-app)。</span><span class="sxs-lookup"><span data-stu-id="dab2c-239">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/tutorial-dotnetcore-sqldb-app).</span></span>

<span data-ttu-id="dab2c-240">感谢读完这篇 Razor Pages 简介。</span><span class="sxs-lookup"><span data-stu-id="dab2c-240">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="dab2c-241">[Razor Pages 和 EF Core 入门](xref:data/ef-rp/intro)是本教程的优选后续教程。</span><span class="sxs-lookup"><span data-stu-id="dab2c-241">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="dab2c-242">其他资源</span><span class="sxs-lookup"><span data-stu-id="dab2c-242">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>

> [!div class="step-by-step"]
> [<span data-ttu-id="dab2c-243">上一篇：添加新字段</span><span class="sxs-lookup"><span data-stu-id="dab2c-243">Previous: Add a new field</span></span>](xref:tutorials/razor-pages/new-field)
