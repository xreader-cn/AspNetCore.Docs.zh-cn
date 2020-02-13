---
title: 将验证添加到 ASP.NET Core Razor 页面
author: rick-anderson
description: 了解如何将验证添加到 ASP.NET Core 中的 Razor 页面。
ms.author: riande
ms.custom: mvc
ms.date: 7/23/2019
uid: tutorials/razor-pages/validation
ms.openlocfilehash: f283234ed8a32dc9b7904bc6fee1cc9c04741029
ms.sourcegitcommit: 85564ee396c74c7651ac47dd45082f3f1803f7a2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172599"
---
# <a name="add-validation-to-an-aspnet-core-razor-page"></a><span data-ttu-id="114a7-103">将验证添加到 ASP.NET Core Razor 页面</span><span class="sxs-lookup"><span data-stu-id="114a7-103">Add validation to an ASP.NET Core Razor Page</span></span>

<span data-ttu-id="114a7-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="114a7-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="114a7-105">本部分中向 `Movie` 模型添加了验证逻辑。</span><span class="sxs-lookup"><span data-stu-id="114a7-105">In this section, validation logic is added to the `Movie` model.</span></span> <span data-ttu-id="114a7-106">每当用户创建或编辑电影时，都会强制执行验证规则。</span><span class="sxs-lookup"><span data-stu-id="114a7-106">The validation rules are enforced any time a user creates or edits a movie.</span></span>

## <a name="validation"></a><span data-ttu-id="114a7-107">验证</span><span class="sxs-lookup"><span data-stu-id="114a7-107">Validation</span></span>

<span data-ttu-id="114a7-108">软件开发的一个关键原则被称为 [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself)（即“不要自我重复”）    。</span><span class="sxs-lookup"><span data-stu-id="114a7-108">A key tenet of software development is called [DRY](https://wikipedia.org/wiki/Don%27t_repeat_yourself) ("**D**on't **R**epeat **Y**ourself").</span></span> <span data-ttu-id="114a7-109">Razor 页面鼓励进行仅指定一次功能的开发，且功能在整个应用中反映。</span><span class="sxs-lookup"><span data-stu-id="114a7-109">Razor Pages encourages development where functionality is specified once, and it's reflected throughout the app.</span></span> <span data-ttu-id="114a7-110">DRY 可以帮助：</span><span class="sxs-lookup"><span data-stu-id="114a7-110">DRY can help:</span></span>

* <span data-ttu-id="114a7-111">减少应用中的代码量。</span><span class="sxs-lookup"><span data-stu-id="114a7-111">Reduce the amount of code in an app.</span></span>
* <span data-ttu-id="114a7-112">使代码更加不易出错，且更易于测试和维护。</span><span class="sxs-lookup"><span data-stu-id="114a7-112">Make the code less error prone, and easier to test and maintain.</span></span>

<span data-ttu-id="114a7-113">Razor 页面和 Entity Framework 提供的验证支持是 DRY 原则的极佳示例。</span><span class="sxs-lookup"><span data-stu-id="114a7-113">The validation support provided by Razor Pages and Entity Framework is a good example of the DRY principle.</span></span> <span data-ttu-id="114a7-114">验证规则在模型类中的某处以声明方式指定，且在应用的所有位置强制执行。</span><span class="sxs-lookup"><span data-stu-id="114a7-114">Validation rules are declaratively specified in one place (in the model class), and the rules are enforced everywhere in the app.</span></span>

## <a name="add-validation-rules-to-the-movie-model"></a><span data-ttu-id="114a7-115">将验证规则添加到电影模型</span><span class="sxs-lookup"><span data-stu-id="114a7-115">Add validation rules to the movie model</span></span>

<span data-ttu-id="114a7-116">DataAnnotations 命名空间提供一组内置验证特性，可通过声明方式应用于类或属性。</span><span class="sxs-lookup"><span data-stu-id="114a7-116">The DataAnnotations namespace provides a set of built-in validation attributes that are applied declaratively to a class or property.</span></span> <span data-ttu-id="114a7-117">DataAnnotations 还包含 `DataType` 等格式特性，有助于格式设置但不提供任何验证。</span><span class="sxs-lookup"><span data-stu-id="114a7-117">DataAnnotations also contains formatting attributes like `DataType` that help with formatting and don't provide any validation.</span></span>

<span data-ttu-id="114a7-118">更新 `Movie` 类以使用内置的 `Required`、`StringLength`、`RegularExpression` 和 `Range` 验证特性。</span><span class="sxs-lookup"><span data-stu-id="114a7-118">Update the `Movie` class to take advantage of the built-in `Required`, `StringLength`, `RegularExpression`, and `Range` validation attributes.</span></span>

[!code-csharp[](~/tutorials/razor-pages/razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet1)]

<span data-ttu-id="114a7-119">验证特性指定要对应用这些特性的模型属性强制执行的行为：</span><span class="sxs-lookup"><span data-stu-id="114a7-119">The validation attributes specify behavior that you want to enforce on the model properties they're applied to:</span></span>

* <span data-ttu-id="114a7-120">`Required` 和 `MinimumLength` 特性表示属性必须有值；但用户可输入空格来满足此验证。</span><span class="sxs-lookup"><span data-stu-id="114a7-120">The `Required` and `MinimumLength` attributes indicate that a property must have a value; but nothing prevents a user from entering white space to satisfy this validation.</span></span>
* <span data-ttu-id="114a7-121">`RegularExpression` 特性用于限制可输入的字符。</span><span class="sxs-lookup"><span data-stu-id="114a7-121">The `RegularExpression` attribute is used to limit what characters can be input.</span></span> <span data-ttu-id="114a7-122">在上述代码中，即“Genre”（分类）：</span><span class="sxs-lookup"><span data-stu-id="114a7-122">In the preceding code, "Genre":</span></span>

  * <span data-ttu-id="114a7-123">只能使用字母。</span><span class="sxs-lookup"><span data-stu-id="114a7-123">Must only use letters.</span></span>
  * <span data-ttu-id="114a7-124">第一个字母必须为大写。</span><span class="sxs-lookup"><span data-stu-id="114a7-124">The first letter is required to be uppercase.</span></span> <span data-ttu-id="114a7-125">不允许使用空格、数字和特殊字符。</span><span class="sxs-lookup"><span data-stu-id="114a7-125">White space, numbers, and special characters are not allowed.</span></span>

* <span data-ttu-id="114a7-126">`RegularExpression`“Rating”（分级）：</span><span class="sxs-lookup"><span data-stu-id="114a7-126">The `RegularExpression` "Rating":</span></span>

  * <span data-ttu-id="114a7-127">要求第一个字符为大写字母。</span><span class="sxs-lookup"><span data-stu-id="114a7-127">Requires that the first character be an uppercase letter.</span></span>
  * <span data-ttu-id="114a7-128">允许在后续空格中使用特殊字符和数字。</span><span class="sxs-lookup"><span data-stu-id="114a7-128">Allows special characters and numbers in  subsequent spaces.</span></span> <span data-ttu-id="114a7-129">“PG-13”对“分级”有效，但对于“分类”无效。</span><span class="sxs-lookup"><span data-stu-id="114a7-129">"PG-13" is valid for a rating, but fails for a "Genre".</span></span>

* <span data-ttu-id="114a7-130">`Range` 特性将值限制在指定范围内。</span><span class="sxs-lookup"><span data-stu-id="114a7-130">The `Range` attribute constrains a value to within a specified range.</span></span>
* <span data-ttu-id="114a7-131">`StringLength` 特性使你能够设置字符串属性的最大长度，以及可选的最小长度。</span><span class="sxs-lookup"><span data-stu-id="114a7-131">The `StringLength` attribute lets you set the maximum length of a string property, and optionally its minimum length.</span></span>
* <span data-ttu-id="114a7-132">从本质上来说，需要值类型（如 `decimal`、`int`、`float`、`DateTime`），但不需要 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="114a7-132">Value types (such as `decimal`, `int`, `float`, `DateTime`) are inherently required and don't need the `[Required]` attribute.</span></span>

<span data-ttu-id="114a7-133">让 ASP.NET Core 强制自动执行验证规则有助于提升你的应用的可靠性。</span><span class="sxs-lookup"><span data-stu-id="114a7-133">Having validation rules automatically enforced by ASP.NET Core helps make your app more robust.</span></span> <span data-ttu-id="114a7-134">同时它能确保你无法忘记验证某些内容，并防止你无意中将错误数据导入数据库。</span><span class="sxs-lookup"><span data-stu-id="114a7-134">It also ensures that you can't forget to validate something and inadvertently let bad data into the database.</span></span>

### <a name="validation-error-ui-in-razor-pages"></a><span data-ttu-id="114a7-135">Razor 页面中的“验证错误”UI</span><span class="sxs-lookup"><span data-stu-id="114a7-135">Validation Error UI in Razor Pages</span></span>

<span data-ttu-id="114a7-136">运行应用并导航到“页面/电影”。</span><span class="sxs-lookup"><span data-stu-id="114a7-136">Run the app and navigate to Pages/Movies.</span></span>

<span data-ttu-id="114a7-137">选择“新建”链接  。</span><span class="sxs-lookup"><span data-stu-id="114a7-137">Select the **Create New** link.</span></span> <span data-ttu-id="114a7-138">使用无效值填写表单。</span><span class="sxs-lookup"><span data-stu-id="114a7-138">Complete the form with some invalid values.</span></span> <span data-ttu-id="114a7-139">当 jQuery 客户端验证检测到错误时，会显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="114a7-139">When jQuery client-side validation detects the error, it displays an error message.</span></span>

![带有多个 jQuery 客户端验证错误的电影视图表单](validation/_static/val.png)

[!INCLUDE[](~/includes/localization/currency.md)]

<span data-ttu-id="114a7-141">请注意表单如何自动呈现每个包含无效值的字段中的验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="114a7-141">Notice how the form has automatically rendered a validation error message in each field containing an invalid value.</span></span> <span data-ttu-id="114a7-142">客户端（使用 JavaScript 和 jQuery）和服务器端（若用户禁用 JavaScript）都必定会遇到这些错误。</span><span class="sxs-lookup"><span data-stu-id="114a7-142">The errors are enforced both client-side (using JavaScript and jQuery) and server-side (when a user has JavaScript disabled).</span></span>

<span data-ttu-id="114a7-143">一项重要优势是，无需在“创建”或“编辑”页面中更改代码  。</span><span class="sxs-lookup"><span data-stu-id="114a7-143">A significant benefit is that **no** code changes were necessary in the Create  or Edit pages.</span></span> <span data-ttu-id="114a7-144">在模型应用 DataAnnotations 后，即已启用验证 UI。</span><span class="sxs-lookup"><span data-stu-id="114a7-144">Once DataAnnotations were applied to the model, the validation UI was enabled.</span></span> <span data-ttu-id="114a7-145">本教程中自动创建的 Razor 页面自动选取了验证规则（使用 `Movie` 模型类的属性上的验证特性）。</span><span class="sxs-lookup"><span data-stu-id="114a7-145">The Razor Pages created in this tutorial automatically picked up the validation rules (using validation attributes on the properties of the `Movie` model class).</span></span> <span data-ttu-id="114a7-146">使用“编辑”页面测试验证后，即已应用相同验证。</span><span class="sxs-lookup"><span data-stu-id="114a7-146">Test validation using the Edit page, the same validation is applied.</span></span>

<span data-ttu-id="114a7-147">存在客户端验证错误时，不会将表单数据发布到服务器。</span><span class="sxs-lookup"><span data-stu-id="114a7-147">The form data isn't posted to the server until there are no client-side validation errors.</span></span> <span data-ttu-id="114a7-148">请通过以下一种或多种方法验证是否未发布表单数据：</span><span class="sxs-lookup"><span data-stu-id="114a7-148">Verify form data isn't posted by one or more of the following approaches:</span></span>

* <span data-ttu-id="114a7-149">在 `OnPostAsync` 方法中放置一个断点。</span><span class="sxs-lookup"><span data-stu-id="114a7-149">Put a break point in the `OnPostAsync` method.</span></span> <span data-ttu-id="114a7-150">提交表单（选择“创建”或“保存”）   。</span><span class="sxs-lookup"><span data-stu-id="114a7-150">Submit the form (select **Create** or **Save**).</span></span> <span data-ttu-id="114a7-151">从未命中断点。</span><span class="sxs-lookup"><span data-stu-id="114a7-151">The break point is never hit.</span></span>
* <span data-ttu-id="114a7-152">使用 [Fiddler 工具](https://www.telerik.com/fiddler)。</span><span class="sxs-lookup"><span data-stu-id="114a7-152">Use the [Fiddler tool](https://www.telerik.com/fiddler).</span></span>
* <span data-ttu-id="114a7-153">使用浏览器开发人员工具监视网络流量。</span><span class="sxs-lookup"><span data-stu-id="114a7-153">Use the browser developer tools to monitor network traffic.</span></span>

### <a name="server-side-validation"></a><span data-ttu-id="114a7-154">服务器端验证</span><span class="sxs-lookup"><span data-stu-id="114a7-154">Server-side validation</span></span>

<span data-ttu-id="114a7-155">在浏览器中禁用 JavaScript 后，提交出错表单将发布到服务器。</span><span class="sxs-lookup"><span data-stu-id="114a7-155">When JavaScript is disabled in the browser, submitting the form with errors will post to the server.</span></span>

<span data-ttu-id="114a7-156">（可选）测试服务器端验证：</span><span class="sxs-lookup"><span data-stu-id="114a7-156">Optional, test server-side validation:</span></span>

* <span data-ttu-id="114a7-157">在浏览器中禁用 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="114a7-157">Disable JavaScript in the browser.</span></span> <span data-ttu-id="114a7-158">可以使用浏览器的开发人员工具禁用 JavaScript。</span><span class="sxs-lookup"><span data-stu-id="114a7-158">You can disable JavaScript using browser's developer tools.</span></span> <span data-ttu-id="114a7-159">如果无法在浏览器中禁用 JavaScript，请尝试其他浏览器。</span><span class="sxs-lookup"><span data-stu-id="114a7-159">If you can't disable JavaScript in the browser, try another browser.</span></span>
* <span data-ttu-id="114a7-160">在“创建”或“编辑”页面的 `OnPostAsync` 方法中设置断点。</span><span class="sxs-lookup"><span data-stu-id="114a7-160">Set a break point in the `OnPostAsync` method of the Create or Edit page.</span></span>
* <span data-ttu-id="114a7-161">提交包含无效数据的表单。</span><span class="sxs-lookup"><span data-stu-id="114a7-161">Submit a form with invalid data.</span></span>
* <span data-ttu-id="114a7-162">验证模型状态是否无效：</span><span class="sxs-lookup"><span data-stu-id="114a7-162">Verify the model state is invalid:</span></span>

  ```csharp
   if (!ModelState.IsValid)
   {
      return Page();
   }
  ```
  
<span data-ttu-id="114a7-163">或者可以[禁用服务器上的客户端验证](xref:mvc/models/validation#disable-client-side-validation)。</span><span class="sxs-lookup"><span data-stu-id="114a7-163">Alternatively, you can [Disable client-side validation on the server](xref:mvc/models/validation#disable-client-side-validation).</span></span>

<span data-ttu-id="114a7-164">以下代码显示了之前在本教程中设定其基架的“Create.cshtml”的一部分  。</span><span class="sxs-lookup"><span data-stu-id="114a7-164">The following code shows a portion of the *Create.cshtml* page scaffolded earlier in the tutorial.</span></span> <span data-ttu-id="114a7-165">它用于在“创建”和“编辑”页面中显示初始表单并在发生错误后重新显示表单。</span><span class="sxs-lookup"><span data-stu-id="114a7-165">It's used by the Create and Edit pages to display the initial form and to redisplay the form in the event of an error.</span></span>

[!code-cshtml[](razor-pages-start/sample/RazorPagesMovie/Pages/Movies/Create.cshtml?range=14-20)]

<span data-ttu-id="114a7-166">[输入标记帮助程序](xref:mvc/views/working-with-forms)使用 [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) 特性并在客户端生成 jQuery 验证所需的 HTML 特性。</span><span class="sxs-lookup"><span data-stu-id="114a7-166">The [Input Tag Helper](xref:mvc/views/working-with-forms) uses the [DataAnnotations](/aspnet/mvc/overview/older-versions/mvc-music-store/mvc-music-store-part-6) attributes and produces HTML attributes needed for jQuery Validation on the client-side.</span></span> <span data-ttu-id="114a7-167">[验证标记帮助程序](xref:mvc/views/working-with-forms#the-validation-tag-helpers)用于显示验证错误。</span><span class="sxs-lookup"><span data-stu-id="114a7-167">The [Validation Tag Helper](xref:mvc/views/working-with-forms#the-validation-tag-helpers) displays validation errors.</span></span> <span data-ttu-id="114a7-168">有关详细信息，请参阅[验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="114a7-168">See [Validation](xref:mvc/models/validation) for more information.</span></span>

<span data-ttu-id="114a7-169">“创建”和“编辑”页面中没有验证规则。</span><span class="sxs-lookup"><span data-stu-id="114a7-169">The Create and Edit pages have no validation rules in them.</span></span> <span data-ttu-id="114a7-170">仅可在 `Movie` 类中指定验证规则和错误字符串。</span><span class="sxs-lookup"><span data-stu-id="114a7-170">The validation rules and the error strings are specified only in the `Movie` class.</span></span> <span data-ttu-id="114a7-171">这些验证规则将自动应用于编辑 `Movie` 模型的 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="114a7-171">These validation rules are automatically applied to Razor Pages that edit the `Movie` model.</span></span>

<span data-ttu-id="114a7-172">需要更改验证逻辑时，也只能在该模型中更改。</span><span class="sxs-lookup"><span data-stu-id="114a7-172">When validation logic needs to change, it's done only in the model.</span></span> <span data-ttu-id="114a7-173">将始终在整个应用程序中应用验证（在一处定义验证逻辑）。</span><span class="sxs-lookup"><span data-stu-id="114a7-173">Validation is applied consistently throughout the application (validation logic is defined in one place).</span></span> <span data-ttu-id="114a7-174">单处验证有助于保持代码干净，且更易于维护和更新。</span><span class="sxs-lookup"><span data-stu-id="114a7-174">Validation in one place helps keep the code clean, and makes it easier to maintain and update.</span></span>

## <a name="using-datatype-attributes"></a><span data-ttu-id="114a7-175">使用 DataType 特性</span><span class="sxs-lookup"><span data-stu-id="114a7-175">Using DataType Attributes</span></span>

<span data-ttu-id="114a7-176">检查 `Movie` 类。</span><span class="sxs-lookup"><span data-stu-id="114a7-176">Examine the `Movie` class.</span></span> <span data-ttu-id="114a7-177">除了一组内置的验证特性，`System.ComponentModel.DataAnnotations` 命名空间还提供格式特性。</span><span class="sxs-lookup"><span data-stu-id="114a7-177">The `System.ComponentModel.DataAnnotations` namespace provides formatting attributes in addition to the built-in set of validation attributes.</span></span> <span data-ttu-id="114a7-178">`DataType` 特性应用于 `ReleaseDate` 和 `Price` 属性。</span><span class="sxs-lookup"><span data-stu-id="114a7-178">The `DataType` attribute is applied to the `ReleaseDate` and `Price` properties.</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie/Models/MovieDateRatingDA.cs?highlight=2,6&name=snippet2)]

<span data-ttu-id="114a7-179">`DataType` 特性仅提供相关提示来帮助视图引擎设置数据格式（并提供特性，例如向 URL 提供 `<a>` 和向电子邮件提供 `<a href="mailto:EmailAddress.com">`）。</span><span class="sxs-lookup"><span data-stu-id="114a7-179">The `DataType` attributes only provide hints for the view engine to format the data (and supplies attributes such as `<a>` for URL's and `<a href="mailto:EmailAddress.com">` for email).</span></span> <span data-ttu-id="114a7-180">使用 `RegularExpression` 特性验证数据的格式。</span><span class="sxs-lookup"><span data-stu-id="114a7-180">Use the `RegularExpression` attribute to validate the format of the data.</span></span> <span data-ttu-id="114a7-181">`DataType` 属性用于指定比数据库内部类型更具体的数据类型。</span><span class="sxs-lookup"><span data-stu-id="114a7-181">The `DataType` attribute is used to specify a data type that's more specific than the database intrinsic type.</span></span> <span data-ttu-id="114a7-182">`DataType` 特性不是验证特性。</span><span class="sxs-lookup"><span data-stu-id="114a7-182">`DataType` attributes are not validation attributes.</span></span> <span data-ttu-id="114a7-183">示例应用程序中仅显示日期，不显示时间。</span><span class="sxs-lookup"><span data-stu-id="114a7-183">In the sample application, only the date is displayed, without time.</span></span>

<span data-ttu-id="114a7-184">`DataType` 枚举提供了多种数据类型，例如日期、时间、电话号码、货币、电子邮件地址等。</span><span class="sxs-lookup"><span data-stu-id="114a7-184">The `DataType` Enumeration provides for many data types, such as Date, Time, PhoneNumber, Currency, EmailAddress, and more.</span></span> <span data-ttu-id="114a7-185">应用程序还可通过 `DataType` 特性自动提供类型特定的功能。</span><span class="sxs-lookup"><span data-stu-id="114a7-185">The `DataType` attribute can also enable the application to automatically provide type-specific features.</span></span> <span data-ttu-id="114a7-186">例如，可为 `DataType.EmailAddress` 创建 `mailto:` 链接。</span><span class="sxs-lookup"><span data-stu-id="114a7-186">For example, a `mailto:` link can be created for `DataType.EmailAddress`.</span></span> <span data-ttu-id="114a7-187">可在支持 HTML5 的浏览器中为 `DataType.Date` 提供日期选择器。</span><span class="sxs-lookup"><span data-stu-id="114a7-187">A date selector can be provided for `DataType.Date` in browsers that support HTML5.</span></span> <span data-ttu-id="114a7-188">`DataType` 特性发出 HTML 5 `data-`（读作 data dash）特性供 HTML 5 浏览器使用。</span><span class="sxs-lookup"><span data-stu-id="114a7-188">The `DataType` attributes emits HTML 5 `data-` (pronounced data dash) attributes that HTML 5 browsers consume.</span></span> <span data-ttu-id="114a7-189">`DataType` 特性不提供任何验证  。</span><span class="sxs-lookup"><span data-stu-id="114a7-189">The `DataType` attributes do **not** provide any validation.</span></span>

<span data-ttu-id="114a7-190">`DataType.Date` 不指定显示日期的格式。</span><span class="sxs-lookup"><span data-stu-id="114a7-190">`DataType.Date` doesn't specify the format of the date that's displayed.</span></span> <span data-ttu-id="114a7-191">默认情况下，数据字段根据基于服务器的 `CultureInfo` 的默认格式进行显示。</span><span class="sxs-lookup"><span data-stu-id="114a7-191">By default, the data field is displayed according to the default formats based on the server's `CultureInfo`.</span></span>

<span data-ttu-id="114a7-192">要使 Entity Framework Core 能将 `Price` 正确地映射到数据库中的货币，则必须使用 `[Column(TypeName = "decimal(18, 2)")]` 数据注释。</span><span class="sxs-lookup"><span data-stu-id="114a7-192">The `[Column(TypeName = "decimal(18, 2)")]` data annotation is required so Entity Framework Core can correctly map `Price` to currency in the database.</span></span> <span data-ttu-id="114a7-193">有关详细信息，请参阅[数据类型](/ef/core/modeling/relational/data-types)。</span><span class="sxs-lookup"><span data-stu-id="114a7-193">For more information, see [Data Types](/ef/core/modeling/relational/data-types).</span></span>

<span data-ttu-id="114a7-194">`DisplayFormat` 特性用于显式指定日期格式：</span><span class="sxs-lookup"><span data-stu-id="114a7-194">The `DisplayFormat` attribute is used to explicitly specify the date format:</span></span>

```csharp
[DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
public DateTime ReleaseDate { get; set; }
```

<span data-ttu-id="114a7-195">`ApplyFormatInEditMode` 设置用于指定在显示值进行编辑时需应用格式。</span><span class="sxs-lookup"><span data-stu-id="114a7-195">The `ApplyFormatInEditMode` setting specifies that the formatting should be applied when the value is displayed for editing.</span></span> <span data-ttu-id="114a7-196">可能不希望某些字段具有此行为。</span><span class="sxs-lookup"><span data-stu-id="114a7-196">You might not want that behavior for some fields.</span></span> <span data-ttu-id="114a7-197">例如，在货币值中，可能不希望编辑 UI 中存在货币符号。</span><span class="sxs-lookup"><span data-stu-id="114a7-197">For example, in currency values, you probably don't want the currency symbol in the edit UI.</span></span>

<span data-ttu-id="114a7-198">可单独使用 `DisplayFormat` 特性，但通常建议使用 `DataType` 特性。</span><span class="sxs-lookup"><span data-stu-id="114a7-198">The `DisplayFormat` attribute can be used by itself, but it's generally a good idea to use the `DataType` attribute.</span></span> <span data-ttu-id="114a7-199">`DataType` 特性传达数据的语义而不是传达如何在屏幕上呈现数据，并提供 DisplayFormat 不具备的以下优势：</span><span class="sxs-lookup"><span data-stu-id="114a7-199">The `DataType` attribute conveys the semantics of the data as opposed to how to render it on a screen, and provides the following benefits that you don't get with DisplayFormat:</span></span>

* <span data-ttu-id="114a7-200">浏览器可启用 HTML5 功能（例如显示日历控件、区域设置适用的货币符号、电子邮件链接等）</span><span class="sxs-lookup"><span data-stu-id="114a7-200">The browser can enable HTML5 features (for example to show a calendar control, the locale-appropriate currency symbol, email links, etc.)</span></span>
* <span data-ttu-id="114a7-201">默认情况下，浏览器将根据区域设置采用正确的格式呈现数据。</span><span class="sxs-lookup"><span data-stu-id="114a7-201">By default, the browser will render data using the correct format based on your locale.</span></span>
* <span data-ttu-id="114a7-202">借助 `DataType` 特性，ASP.NET Core 框架可选择适当的字段模板来呈现数据。</span><span class="sxs-lookup"><span data-stu-id="114a7-202">The `DataType` attribute can enable the ASP.NET Core framework to choose the right field template to render the data.</span></span> <span data-ttu-id="114a7-203">单独使用时，`DisplayFormat` 特性将使用字符串模板。</span><span class="sxs-lookup"><span data-stu-id="114a7-203">The `DisplayFormat` if used by itself uses the string template.</span></span>

<span data-ttu-id="114a7-204">注意：jQuery 验证不适用于 `Range` 属性和 `DateTime`。</span><span class="sxs-lookup"><span data-stu-id="114a7-204">Note: jQuery validation doesn't work with the `Range` attribute and `DateTime`.</span></span> <span data-ttu-id="114a7-205">例如，以下代码将始终显示客户端验证错误，即便日期在指定的范围内：</span><span class="sxs-lookup"><span data-stu-id="114a7-205">For example, the following code will always display a client-side validation error, even when the date is in the specified range:</span></span>

```csharp
[Range(typeof(DateTime), "1/1/1966", "1/1/2020")]
   ```

<span data-ttu-id="114a7-206">通常，在模型中编译固定日期是不恰当的，因此不推荐使用 `Range` 特性和 `DateTime`。</span><span class="sxs-lookup"><span data-stu-id="114a7-206">It's generally not a good practice to compile hard dates in your models, so using the `Range` attribute and `DateTime` is discouraged.</span></span>

<span data-ttu-id="114a7-207">以下代码显示组合在一行上的特性：</span><span class="sxs-lookup"><span data-stu-id="114a7-207">The following code shows combining attributes on one line:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDAmult.cs?name=snippet1)]

<span data-ttu-id="114a7-208">[Razor Pages 和 EF Core 入门](xref:data/ef-rp/intro)显示了 Razor Pages 的高级 EF Core 操作。</span><span class="sxs-lookup"><span data-stu-id="114a7-208">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) shows advanced EF Core operations with Razor Pages.</span></span>

### <a name="apply-migrations"></a><span data-ttu-id="114a7-209">应用迁移</span><span class="sxs-lookup"><span data-stu-id="114a7-209">Apply migrations</span></span>

<span data-ttu-id="114a7-210">应用于类的 DataAnnotations 会更改架构。</span><span class="sxs-lookup"><span data-stu-id="114a7-210">The DataAnnotations applied to the class change the schema.</span></span> <span data-ttu-id="114a7-211">例如，应用于 `Title` 字段的 DataAnnotations 会：</span><span class="sxs-lookup"><span data-stu-id="114a7-211">For example, the DataAnnotations applied to the `Title` field:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Models/MovieDateRatingDA.cs?name=snippet11)]

* <span data-ttu-id="114a7-212">将字符数限制为 60。</span><span class="sxs-lookup"><span data-stu-id="114a7-212">Limits the characters to 60.</span></span>
* <span data-ttu-id="114a7-213">不允许使用 `null` 值。</span><span class="sxs-lookup"><span data-stu-id="114a7-213">Doesn't allow a `null` value.</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="114a7-214">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="114a7-214">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="114a7-215">`Movie` 表当前具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="114a7-215">The `Movie` table currently has the following schema:</span></span>

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

<span data-ttu-id="114a7-216">前面的架构更改不会导致 EF 引发异常。</span><span class="sxs-lookup"><span data-stu-id="114a7-216">The preceding schema changes don't cause EF to throw an exception.</span></span> <span data-ttu-id="114a7-217">不过，请创建迁移，使架构与模型保持一致。</span><span class="sxs-lookup"><span data-stu-id="114a7-217">However, create a migration so the schema is consistent with the model.</span></span>

<span data-ttu-id="114a7-218">从“工具”菜单中，选择“NuGet 包管理器”>“包管理器控制台”。  </span><span class="sxs-lookup"><span data-stu-id="114a7-218">From the **Tools** menu, select **NuGet Package Manager > Package Manager Console**.</span></span>
<span data-ttu-id="114a7-219">在 PMC 中，输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="114a7-219">In the PMC, enter the following commands:</span></span>

```powershell
Add-Migration New_DataAnnotations
Update-Database
```

<span data-ttu-id="114a7-220">`Update-Database` 运行 `New_DataAnnotations` 类的 `Up` 方法。</span><span class="sxs-lookup"><span data-stu-id="114a7-220">`Update-Database` runs the `Up` methods of the `New_DataAnnotations` class.</span></span> <span data-ttu-id="114a7-221">检查 `Up` 方法：</span><span class="sxs-lookup"><span data-stu-id="114a7-221">Examine the `Up` method:</span></span>

[!code-csharp[](razor-pages-start/sample/RazorPagesMovie30/Migrations/20190724163003_New_DataAnnotations.cs?name=snippet)]

<span data-ttu-id="114a7-222">更新的 `Movie` 表具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="114a7-222">The updated `Movie` table has the following schema:</span></span>

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

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="114a7-223">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="114a7-223">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="114a7-224">SQLite 不需要迁移。</span><span class="sxs-lookup"><span data-stu-id="114a7-224">Migrations are not required for SQLite.</span></span>

---

### <a name="publish-to-azure"></a><span data-ttu-id="114a7-225">发布到 Azure</span><span class="sxs-lookup"><span data-stu-id="114a7-225">Publish to Azure</span></span>

<span data-ttu-id="114a7-226">若要了解如何部署到 Azure，请参阅[教程：使用 SQL 数据库在 Azure 中生成 ASP.NET Core 应用](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)。</span><span class="sxs-lookup"><span data-stu-id="114a7-226">For information on deploying to Azure, see [Tutorial: Build an ASP.NET Core app in Azure with SQL Database](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb).</span></span>

<span data-ttu-id="114a7-227">感谢读完这篇 Razor 页面简介。</span><span class="sxs-lookup"><span data-stu-id="114a7-227">Thanks for completing this introduction to Razor Pages.</span></span> <span data-ttu-id="114a7-228">[Razor Pages 和 EF Core 入门](xref:data/ef-rp/intro)是本教程的优选后续教程。</span><span class="sxs-lookup"><span data-stu-id="114a7-228">[Get started with Razor Pages and EF Core](xref:data/ef-rp/intro) is an excellent follow up to this tutorial.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="114a7-229">其他资源</span><span class="sxs-lookup"><span data-stu-id="114a7-229">Additional resources</span></span>

* <xref:mvc/views/working-with-forms>
* <xref:fundamentals/localization>
* <xref:mvc/views/tag-helpers/intro>
* <xref:mvc/views/tag-helpers/authoring>
* [<span data-ttu-id="114a7-230">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="114a7-230">YouTube version of this tutorial</span></span>](https://youtu.be/b63m66eu7us)

> [!div class="step-by-step"]
> [<span data-ttu-id="114a7-231">上一篇：添加新字段</span><span class="sxs-lookup"><span data-stu-id="114a7-231">Previous: Adding a new field</span></span>](xref:tutorials/razor-pages/new-field)
