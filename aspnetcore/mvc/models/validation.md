---
title: ASP.NET Core MVC 中的模型验证
author: tdykstra
description: 了解 ASP.NET Core MVC 中的模型验证。
ms.author: riande
ms.custom: mvc
ms.date: 01/14/2019
uid: mvc/models/validation
ms.openlocfilehash: 7c8255097dfc72480794930ebe4d6cb568edbd7c
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396189"
---
# <a name="model-validation-in-aspnet-core-mvc"></a><span data-ttu-id="9fad3-103">ASP.NET Core MVC 中的模型验证</span><span class="sxs-lookup"><span data-stu-id="9fad3-103">Model validation in ASP.NET Core MVC</span></span>

<span data-ttu-id="9fad3-104">作者：[Rachel Appel](https://github.com/rachelappel)</span><span class="sxs-lookup"><span data-stu-id="9fad3-104">By [Rachel Appel](https://github.com/rachelappel)</span></span>

## <a name="introduction-to-model-validation"></a><span data-ttu-id="9fad3-105">模型验证简介</span><span class="sxs-lookup"><span data-stu-id="9fad3-105">Introduction to model validation</span></span>

<span data-ttu-id="9fad3-106">在将数据存储到数据库之前，应用必须先验证数据。</span><span class="sxs-lookup"><span data-stu-id="9fad3-106">Before an app stores data in a database, the app must validate the data.</span></span> <span data-ttu-id="9fad3-107">必须检查数据是否存在潜在的安全威胁，确保数据已设置适当的类型和大小格式，并且必须符合相关规则。</span><span class="sxs-lookup"><span data-stu-id="9fad3-107">Data must be checked for potential security threats, verified that it's appropriately formatted by type and size, and it must conform to your rules.</span></span> <span data-ttu-id="9fad3-108">实施验证的过程可能有些单调乏味，但却必不可少。</span><span class="sxs-lookup"><span data-stu-id="9fad3-108">Validation is necessary although it can be redundant and tedious to implement.</span></span> <span data-ttu-id="9fad3-109">在 MVC 中，验证发生在客户端和服务器上。</span><span class="sxs-lookup"><span data-stu-id="9fad3-109">In MVC, validation happens on both the client and server.</span></span>

<span data-ttu-id="9fad3-110">幸运的是，.NET 已将验证抽象化为验证属性。</span><span class="sxs-lookup"><span data-stu-id="9fad3-110">Fortunately, .NET has abstracted validation into validation attributes.</span></span> <span data-ttu-id="9fad3-111">这些属性包含验证代码，从而减少了所需编写的代码量。</span><span class="sxs-lookup"><span data-stu-id="9fad3-111">These attributes contain validation code, thereby reducing the amount of code you must write.</span></span>

<span data-ttu-id="9fad3-112">在 ASP.NET Core 2.2 及更高版本中，如果能够确定给定模型关系图不需要进行验证，ASP.NET Core 运行时便会简化（跳过）验证。</span><span class="sxs-lookup"><span data-stu-id="9fad3-112">In ASP.NET Core 2.2 and later, the ASP.NET Core runtime short-circuits (skips) validation if it can determine that a given model graph doesn't require validation.</span></span> <span data-ttu-id="9fad3-113">验证无法或没有关联任何验证程序的模型时，跳过验证可能会显著提升性能。</span><span class="sxs-lookup"><span data-stu-id="9fad3-113">Skipping validation can provide significant performance improvements when validating models that cannot or do not have any associated validators.</span></span> <span data-ttu-id="9fad3-114">已跳过的验证包括诸如基元集合（`byte[]`、`string[]`、`Dictionary<string, string>` 等）之类的对象，或没有任何验证程序的复杂对象关系图。</span><span class="sxs-lookup"><span data-stu-id="9fad3-114">The skipped validation includes objects such as collections of primitives (`byte[]`, `string[]`, `Dictionary<string, string>`, etc.), or complex object graphs without any validators.</span></span>

<span data-ttu-id="9fad3-115">[查看或下载 GitHub 中的示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample)。</span><span class="sxs-lookup"><span data-stu-id="9fad3-115">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/models/validation/sample).</span></span>

## <a name="validation-attributes"></a><span data-ttu-id="9fad3-116">验证属性</span><span class="sxs-lookup"><span data-stu-id="9fad3-116">Validation Attributes</span></span>

<span data-ttu-id="9fad3-117">验证属性用于配置模型验证，因此，在概念上类似于数据库表中字段上的验证。</span><span class="sxs-lookup"><span data-stu-id="9fad3-117">Validation attributes are a way to configure model validation so it's similar conceptually to validation on fields in database tables.</span></span> <span data-ttu-id="9fad3-118">它包括诸如分配数据类型或必填字段之类的约束。</span><span class="sxs-lookup"><span data-stu-id="9fad3-118">This includes constraints such as assigning data types or required fields.</span></span> <span data-ttu-id="9fad3-119">其他类型的验证包括将向数据应用模式以强制实施业务规则，比如信用卡、电话号码或电子邮件地址。</span><span class="sxs-lookup"><span data-stu-id="9fad3-119">Other types of validation include applying patterns to data to enforce business rules, such as a credit card, phone number, or email address.</span></span> <span data-ttu-id="9fad3-120">验证属性更易使用，并使这些要求的实施变得更简单。</span><span class="sxs-lookup"><span data-stu-id="9fad3-120">Validation attributes make enforcing these requirements much simpler and easier to use.</span></span>

<span data-ttu-id="9fad3-121">验证特性在属性级别指定：</span><span class="sxs-lookup"><span data-stu-id="9fad3-121">Validation attributes are specified at the property level:</span></span> 

```csharp
[Required]
public string MyProperty { get; set; }
```

<span data-ttu-id="9fad3-122">下面是一个应用的已批注 `Movie` 模型，该应用用于存储电影和电视节目的相关信息。</span><span class="sxs-lookup"><span data-stu-id="9fad3-122">Below is an annotated `Movie` model from an app that stores information about movies and TV shows.</span></span> <span data-ttu-id="9fad3-123">大多数属性都是必需属性，多个字符串属性具有长度要求。</span><span class="sxs-lookup"><span data-stu-id="9fad3-123">Most of the properties are required and several string properties have length requirements.</span></span> <span data-ttu-id="9fad3-124">此外，还有一个针对·`Price` 属性设置的从 0 到 $999.99 的数值范围限制，以及一个自定义验证特性。</span><span class="sxs-lookup"><span data-stu-id="9fad3-124">Additionally, there's a numeric range restriction in place for the `Price` property from 0 to $999.99, along with a custom validation attribute.</span></span>

[!code-csharp[](validation/sample/Movie.cs?range=6-29)]

<span data-ttu-id="9fad3-125">通过读取整个模型即可显示有关此应用的数据的规则，从而使代码维护变得更轻松。</span><span class="sxs-lookup"><span data-stu-id="9fad3-125">Simply reading through the model reveals the rules about data for this app, making it easier to maintain the code.</span></span> <span data-ttu-id="9fad3-126">下面是几个常用的内置验证属性：</span><span class="sxs-lookup"><span data-stu-id="9fad3-126">Below are several popular built-in validation attributes:</span></span>

* <span data-ttu-id="9fad3-127">`[CreditCard]`：验证属性是否有信用卡格式。</span><span class="sxs-lookup"><span data-stu-id="9fad3-127">`[CreditCard]`: Validates the property has a credit card format.</span></span>

* <span data-ttu-id="9fad3-128">`[Compare]`：验证模型中的两个属性是否匹配。</span><span class="sxs-lookup"><span data-stu-id="9fad3-128">`[Compare]`: Validates two properties in a model match.</span></span>

* <span data-ttu-id="9fad3-129">`[EmailAddress]`：验证属性是否有电子邮件格式。</span><span class="sxs-lookup"><span data-stu-id="9fad3-129">`[EmailAddress]`: Validates the property has an email format.</span></span>

* <span data-ttu-id="9fad3-130">`[Phone]`：验证属性是否有电话格式。</span><span class="sxs-lookup"><span data-stu-id="9fad3-130">`[Phone]`: Validates the property has a telephone format.</span></span>

* <span data-ttu-id="9fad3-131">`[Range]`：验证属性值是否在给定范围内。</span><span class="sxs-lookup"><span data-stu-id="9fad3-131">`[Range]`: Validates the property value falls within the given range.</span></span>

* <span data-ttu-id="9fad3-132">`[RegularExpression]`：验证数据是否与指定的正则表达式匹配。</span><span class="sxs-lookup"><span data-stu-id="9fad3-132">`[RegularExpression]`: Validates that the data matches the specified regular expression.</span></span>

* <span data-ttu-id="9fad3-133">`[Required]`：将属性设置为必需属性。</span><span class="sxs-lookup"><span data-stu-id="9fad3-133">`[Required]`: Makes a property required.</span></span>

* <span data-ttu-id="9fad3-134">`[StringLength]`：验证字符串属性是否不超过给定的最大长度。</span><span class="sxs-lookup"><span data-stu-id="9fad3-134">`[StringLength]`: Validates that a string property has at most the given maximum length.</span></span>

* <span data-ttu-id="9fad3-135">`[Url]`：验证属性是否有 URL 格式。</span><span class="sxs-lookup"><span data-stu-id="9fad3-135">`[Url]`: Validates the property has a URL format.</span></span>

<span data-ttu-id="9fad3-136">MVC 支持从 `ValidationAttribute` 派生的所有用于验证的属性。</span><span class="sxs-lookup"><span data-stu-id="9fad3-136">MVC supports any attribute that derives from `ValidationAttribute` for validation purposes.</span></span> <span data-ttu-id="9fad3-137">在 [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) 命名空间中可找到许多有用的验证属性。</span><span class="sxs-lookup"><span data-stu-id="9fad3-137">Many useful validation attributes can be found in the [System.ComponentModel.DataAnnotations](/dotnet/api/system.componentmodel.dataannotations) namespace.</span></span>

<span data-ttu-id="9fad3-138">在某些情况下，内置属性可能无法提供所需的功能。</span><span class="sxs-lookup"><span data-stu-id="9fad3-138">There may be instances where you need more features than built-in attributes provide.</span></span> <span data-ttu-id="9fad3-139">这时，就可以通过从 `ValidationAttribute` 派生或将模型更改为实现 `IValidatableObject`，来创建自定义验证属性。</span><span class="sxs-lookup"><span data-stu-id="9fad3-139">For those times, you can create custom validation attributes by deriving from `ValidationAttribute` or changing your model to implement `IValidatableObject`.</span></span>

## <a name="notes-on-the-use-of-the-required-attribute"></a><span data-ttu-id="9fad3-140">必需属性的使用说明</span><span class="sxs-lookup"><span data-stu-id="9fad3-140">Notes on the use of the Required attribute</span></span>

<span data-ttu-id="9fad3-141">从本质上来说，需要不可以为 null 的[值类型](/dotnet/csharp/language-reference/keywords/value-types)（如 `decimal`、`int`、`float` 和 `DateTime`），但不需要 `Required` 特性。</span><span class="sxs-lookup"><span data-stu-id="9fad3-141">Non-nullable [value types](/dotnet/csharp/language-reference/keywords/value-types) (such as `decimal`, `int`, `float`, and `DateTime`) are inherently required and don't need the `Required` attribute.</span></span> <span data-ttu-id="9fad3-142">应用不会对标记为 `Required` 的不可为 null 的类型执行任何服务器端验证检查。</span><span class="sxs-lookup"><span data-stu-id="9fad3-142">The app performs no server-side validation checks for non-nullable types that are marked `Required`.</span></span>

<span data-ttu-id="9fad3-143">对于不可为 null 的类型，MVC 模型绑定（与验证和验证属性无关）会拒绝包含缺失值或空白的表单域提交。</span><span class="sxs-lookup"><span data-stu-id="9fad3-143">MVC model binding, which isn't concerned with validation and validation attributes, rejects a form field submission containing a missing value or whitespace for a non-nullable type.</span></span> <span data-ttu-id="9fad3-144">如果目标属性上缺少 `BindRequired` 特性，模型绑定会忽略不可为 null 的类型的缺失数据，导致传入表单数据中缺少表单域。</span><span class="sxs-lookup"><span data-stu-id="9fad3-144">In the absence of a `BindRequired` attribute on the target property, model binding ignores missing data for non-nullable types, where the form field is absent from the incoming form data.</span></span>

<span data-ttu-id="9fad3-145">[BindRequired 特性](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.bindrequiredattribute)（另请参阅 <xref:mvc/models/model-binding#customize-model-binding-behavior-with-attributes>）可用于确保表单数据完整。</span><span class="sxs-lookup"><span data-stu-id="9fad3-145">The [BindRequired attribute](/dotnet/api/microsoft.aspnetcore.mvc.modelbinding.bindrequiredattribute) (also see <xref:mvc/models/model-binding#customize-model-binding-behavior-with-attributes>) is useful to ensure form data is complete.</span></span> <span data-ttu-id="9fad3-146">当应用于某个属性时，模型绑定系统要求该属性具有值。</span><span class="sxs-lookup"><span data-stu-id="9fad3-146">When applied to a property, the model binding system requires a value for that property.</span></span> <span data-ttu-id="9fad3-147">当应用于某个类型时，模型绑定系统要求该类型的所有属性都具有值。</span><span class="sxs-lookup"><span data-stu-id="9fad3-147">When applied to a type, the model binding system requires values for all of the properties of that type.</span></span>

<span data-ttu-id="9fad3-148">使用 [Nullable\<T> 类型](/dotnet/csharp/programming-guide/nullable-types/)（例如，`decimal?` 或 `System.Nullable<decimal>`）并将其标记为 `Required` 时，将执行服务器端验证检查，就像该属性是标准的可以为 null 的类型（例如，`string`）一样。</span><span class="sxs-lookup"><span data-stu-id="9fad3-148">When you use a [Nullable\<T> type](/dotnet/csharp/programming-guide/nullable-types/) (for example, `decimal?` or `System.Nullable<decimal>`) and mark it `Required`, a server-side validation check is performed as if the property were a standard nullable type (for example, a `string`).</span></span>

<span data-ttu-id="9fad3-149">客户端验证要求与标记为 `Required` 的模型属性对应的表单域以及未标记为 `Required` 的不可为 null 的类型属性具有值。</span><span class="sxs-lookup"><span data-stu-id="9fad3-149">Client-side validation requires a value for a form field that corresponds to a model property that you've marked `Required` and for a non-nullable type property that you haven't marked `Required`.</span></span> <span data-ttu-id="9fad3-150">`Required` 可用于控制客户端验证错误消息。</span><span class="sxs-lookup"><span data-stu-id="9fad3-150">`Required` can be used to control the client-side validation error message.</span></span>

::: moniker range=">= aspnetcore-2.1"

## <a name="top-level-node-validation"></a><span data-ttu-id="9fad3-151">顶级节点验证</span><span class="sxs-lookup"><span data-stu-id="9fad3-151">Top-level node validation</span></span>

<span data-ttu-id="9fad3-152">顶级节点包括：</span><span class="sxs-lookup"><span data-stu-id="9fad3-152">Top-level nodes include:</span></span>

* <span data-ttu-id="9fad3-153">操作参数</span><span class="sxs-lookup"><span data-stu-id="9fad3-153">Action parameters</span></span>
* <span data-ttu-id="9fad3-154">控制器属性</span><span class="sxs-lookup"><span data-stu-id="9fad3-154">Controller properties</span></span>
* <span data-ttu-id="9fad3-155">页处理程序参数</span><span class="sxs-lookup"><span data-stu-id="9fad3-155">Page handler parameters</span></span>
* <span data-ttu-id="9fad3-156">页模型属性</span><span class="sxs-lookup"><span data-stu-id="9fad3-156">Page model properties</span></span>

<span data-ttu-id="9fad3-157">除了验证模型属性之外，还验证了模型绑定的顶级节点。</span><span class="sxs-lookup"><span data-stu-id="9fad3-157">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="9fad3-158">在示例应用的以下示例中，`VerifyPhone` 方法使用 <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> 验证表单的“电话”字段中的用户数据：</span><span class="sxs-lookup"><span data-stu-id="9fad3-158">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the user data in the Phone field of a form:</span></span>

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="9fad3-159">顶级节点可以将 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> 与验证属性结合使用。</span><span class="sxs-lookup"><span data-stu-id="9fad3-159">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="9fad3-160">在示例应用的以下示例中，`CheckAge` 方法指定在提交表单时必须从查询字符串绑定 `age` 参数：</span><span class="sxs-lookup"><span data-stu-id="9fad3-160">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="9fad3-161">在“检查年限”页 (CheckAge.cshtml) 中，有两个表单。</span><span class="sxs-lookup"><span data-stu-id="9fad3-161">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="9fad3-162">第一个表单提交 `Age` 的值 `99` 作为查询字符串：`https://localhost:5001/Users/CheckAge?Age=99`。</span><span class="sxs-lookup"><span data-stu-id="9fad3-162">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="9fad3-163">当提交查询字符串中格式设置正确的 `age` 参数时，表单将进行验证。</span><span class="sxs-lookup"><span data-stu-id="9fad3-163">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="9fad3-164">“检查年限”页面上的第二个表单提交请求正文中的 `Age` 值，验证失败。</span><span class="sxs-lookup"><span data-stu-id="9fad3-164">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="9fad3-165">绑定失败，因为 `age` 参数必须来自查询字符串。</span><span class="sxs-lookup"><span data-stu-id="9fad3-165">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="9fad3-166">默认情况下，验证处于启用状态，并由 <xref:Microsoft.AspNetCore.Mvc.MvcOptions> 的 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> 属性控制。</span><span class="sxs-lookup"><span data-stu-id="9fad3-166">Validation is enabled by default and controlled by the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property of <xref:Microsoft.AspNetCore.Mvc.MvcOptions>.</span></span> <span data-ttu-id="9fad3-167">要禁用顶级节点验证，请在 MVC 选项 (`Startup.ConfigureServices`) 中将 `AllowValidatingTopLevelNodes` 设置为 `false`：</span><span class="sxs-lookup"><span data-stu-id="9fad3-167">To disable top-level node validation, set `AllowValidatingTopLevelNodes` to `false` in MVC options (`Startup.ConfigureServices`):</span></span>

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

::: moniker-end

## <a name="model-state"></a><span data-ttu-id="9fad3-168">模型状态</span><span class="sxs-lookup"><span data-stu-id="9fad3-168">Model State</span></span>

<span data-ttu-id="9fad3-169">模型状态表示已提交的 HTML 表单值中的验证错误。</span><span class="sxs-lookup"><span data-stu-id="9fad3-169">Model state represents validation errors in submitted HTML form values.</span></span>

<span data-ttu-id="9fad3-170">MVC 将继续验证字段，直至达到错误数上限（默认为 200 个）。</span><span class="sxs-lookup"><span data-stu-id="9fad3-170">MVC will continue validating fields until it reaches the maximum number of errors (200 by default).</span></span> <span data-ttu-id="9fad3-171">可以使用 `Startup.ConfigureServices` 中的以下代码配置该数字：</span><span class="sxs-lookup"><span data-stu-id="9fad3-171">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors)]

## <a name="handle-model-state-errors"></a><span data-ttu-id="9fad3-172">处理模型状态错误</span><span class="sxs-lookup"><span data-stu-id="9fad3-172">Handle Model State errors</span></span>

<span data-ttu-id="9fad3-173">在执行控制器操作之前进行模型验证。</span><span class="sxs-lookup"><span data-stu-id="9fad3-173">Model validation occurs before the execution of a controller action.</span></span> <span data-ttu-id="9fad3-174">该操作负责检查 `ModelState.IsValid` 并做出相应响应。</span><span class="sxs-lookup"><span data-stu-id="9fad3-174">It's the action's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="9fad3-175">在许多情况下，正确的反应是返回错误响应，理想状况下会详细说明模型验证失败的原因。</span><span class="sxs-lookup"><span data-stu-id="9fad3-175">In many cases, the appropriate reaction is to return an error response, ideally detailing the reason why model validation failed.</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="9fad3-176">如果在使用 `[ApiController]` 属性的 web API 控制器中，`ModelState.IsValid` 的计算结果为 `false`，将返回包含问题详细信息的自动 HTTP 400 响应。</span><span class="sxs-lookup"><span data-stu-id="9fad3-176">When `ModelState.IsValid` evaluates to `false` in web API controllers using the `[ApiController]` attribute, an automatic HTTP 400 response containing issue details is returned.</span></span> <span data-ttu-id="9fad3-177">有关详细信息，请参阅[自动 HTTP 400 响应](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="9fad3-177">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

::: moniker-end

<span data-ttu-id="9fad3-178">某些应用会选择遵循标准约定来处理模型验证错误，在这种情况下，可以在筛选器中实现此类策略。</span><span class="sxs-lookup"><span data-stu-id="9fad3-178">Some apps will choose to follow a standard convention for dealing with model validation errors, in which case a filter may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="9fad3-179">应测试操作在有效模型状态和无效模型状态下的行为方式。</span><span class="sxs-lookup"><span data-stu-id="9fad3-179">You should test how your actions behave with valid and invalid model states.</span></span>

## <a name="manual-validation"></a><span data-ttu-id="9fad3-180">手动验证</span><span class="sxs-lookup"><span data-stu-id="9fad3-180">Manual validation</span></span>

<span data-ttu-id="9fad3-181">完成模型绑定和验证后，可能需要重复其中的某些步骤。</span><span class="sxs-lookup"><span data-stu-id="9fad3-181">After model binding and validation are complete, you may want to repeat parts of it.</span></span> <span data-ttu-id="9fad3-182">例如，用户可能在应输入整数的字段中输入了文本，或者你可能需要计算模型的某个属性的值。</span><span class="sxs-lookup"><span data-stu-id="9fad3-182">For example, a user may have entered text in a field expecting an integer, or you may need to compute a value for a model's property.</span></span>

<span data-ttu-id="9fad3-183">你可能需要手动运行验证。</span><span class="sxs-lookup"><span data-stu-id="9fad3-183">You may need to run validation manually.</span></span> <span data-ttu-id="9fad3-184">为此，请调用 `TryValidateModel` 方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9fad3-184">To do so, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/sample/MoviesController.cs?name=snippet_TryValidateModel)]

## <a name="custom-validation"></a><span data-ttu-id="9fad3-185">自定义验证</span><span class="sxs-lookup"><span data-stu-id="9fad3-185">Custom validation</span></span>

<span data-ttu-id="9fad3-186">验证属性适用于大多数验证需求。</span><span class="sxs-lookup"><span data-stu-id="9fad3-186">Validation attributes work for most validation needs.</span></span> <span data-ttu-id="9fad3-187">但是，某些验证规则特定于你的业务。</span><span class="sxs-lookup"><span data-stu-id="9fad3-187">However, some validation rules are specific to your business.</span></span> <span data-ttu-id="9fad3-188">你的规则可能不是常见的数据验证技术，比如确保字段是必填字段或符合一系列值。</span><span class="sxs-lookup"><span data-stu-id="9fad3-188">Your rules might not be common data validation techniques such as ensuring a field is required or that it conforms to a range of values.</span></span> <span data-ttu-id="9fad3-189">在这些情况下，自定义验证属性是一种不错的解决方案。</span><span class="sxs-lookup"><span data-stu-id="9fad3-189">For these scenarios, custom validation attributes are a great solution.</span></span> <span data-ttu-id="9fad3-190">在 MVC 中创建你自己的自定义验证属性很简单。</span><span class="sxs-lookup"><span data-stu-id="9fad3-190">Creating your own custom validation attributes in MVC is easy.</span></span> <span data-ttu-id="9fad3-191">只需从 `ValidationAttribute` 继承并重写 `IsValid` 方法。</span><span class="sxs-lookup"><span data-stu-id="9fad3-191">Just inherit from the `ValidationAttribute`, and override the `IsValid` method.</span></span> <span data-ttu-id="9fad3-192">`IsValid` 方法采用两个参数，第一个是名为 *value* 的对象，第二个是名为 *validationContext* 的 `ValidationContext` 对象。</span><span class="sxs-lookup"><span data-stu-id="9fad3-192">The `IsValid` method accepts two parameters, the first is an object named *value* and the second is a `ValidationContext` object named *validationContext*.</span></span> <span data-ttu-id="9fad3-193">*Value* 引用自定义验证程序要验证的字段中的实际值。</span><span class="sxs-lookup"><span data-stu-id="9fad3-193">*Value* refers to the actual value from the field that your custom validator is validating.</span></span>

<span data-ttu-id="9fad3-194">在下面的示例中，一项业务规则规定，用户不能将 1960 年以后发行的电影的流派设置为 *Classic*。</span><span class="sxs-lookup"><span data-stu-id="9fad3-194">In the following sample, a business rule states that users may not set the genre to *Classic* for a movie released after 1960.</span></span> <span data-ttu-id="9fad3-195">`[ClassicMovie]` 属性会先检查流派，如果是经典流派，则查看发行日期是否晚于 1960 年。</span><span class="sxs-lookup"><span data-stu-id="9fad3-195">The `[ClassicMovie]` attribute checks the genre first, and if it's a classic, then it checks the release date to see that it's later than 1960.</span></span> <span data-ttu-id="9fad3-196">如果晚于 1960 年，则验证失败。</span><span class="sxs-lookup"><span data-stu-id="9fad3-196">If it's released after 1960, validation fails.</span></span> <span data-ttu-id="9fad3-197">此属性采用一个表示年份的整数参数，可用于验证数据。</span><span class="sxs-lookup"><span data-stu-id="9fad3-197">The attribute accepts an integer parameter representing the year that you can use to validate data.</span></span> <span data-ttu-id="9fad3-198">可以在该属性的构造函数中捕获该参数的值，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9fad3-198">You can capture the value of the parameter in the attribute's constructor, as shown here:</span></span>

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="9fad3-199">上面的 `movie` 变量表示一个 `Movie` 对象，其中包含要验证的表单提交中的数据。</span><span class="sxs-lookup"><span data-stu-id="9fad3-199">The `movie` variable above represents a `Movie` object that contains the data from the form submission to validate.</span></span> <span data-ttu-id="9fad3-200">在此例中，验证代码会根据规则检查 `ClassicMovieAttribute` 类的 `IsValid` 方法中的日期和流派。</span><span class="sxs-lookup"><span data-stu-id="9fad3-200">In this case, the validation code checks the date and genre in the `IsValid` method of the `ClassicMovieAttribute` class as per the rules.</span></span> <span data-ttu-id="9fad3-201">验证成功时，`IsValid` 返回 `ValidationResult.Success` 代码。</span><span class="sxs-lookup"><span data-stu-id="9fad3-201">Upon successful validation ,`IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="9fad3-202">验证失败时，返回 `ValidationResult` 和错误消息：</span><span class="sxs-lookup"><span data-stu-id="9fad3-202">When validation fails, a `ValidationResult` with an error message is returned:</span></span>

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_GetErrorMessage)]

<span data-ttu-id="9fad3-203">当用户修改 `Genre` 字段并提交表单时，`ClassicMovieAttribute` 的 `IsValid` 方法将验证该电影是否为经典电影。</span><span class="sxs-lookup"><span data-stu-id="9fad3-203">When a user modifies the `Genre` field and submits the form, the `IsValid` method of the `ClassicMovieAttribute` will verify whether the movie is a classic.</span></span> <span data-ttu-id="9fad3-204">将 `ClassicMovieAttribute` 像所有内置特性一样应用于属性（如 `ReleaseDate`）以确保执行验证，如前面的代码示例所示。</span><span class="sxs-lookup"><span data-stu-id="9fad3-204">Like any built-in attribute, apply the `ClassicMovieAttribute` to a property such as `ReleaseDate` to ensure validation happens, as shown in the previous code sample.</span></span> <span data-ttu-id="9fad3-205">由于此示例仅适用于 `Movie` 类型，因此建议使用 `IValidatableObject`，如下一段中所示。</span><span class="sxs-lookup"><span data-stu-id="9fad3-205">Since the example works only with `Movie` types, a better option is to use `IValidatableObject` as shown in the following paragraph.</span></span>

<span data-ttu-id="9fad3-206">也可以通过实现 `IValidatableObject` 接口上的 `Validate` 方法，将这段代码直接放入模型中。</span><span class="sxs-lookup"><span data-stu-id="9fad3-206">Alternatively, this same code could be placed in the model by implementing the `Validate` method on the `IValidatableObject` interface.</span></span> <span data-ttu-id="9fad3-207">如果自定义验证特性可用于验证各个属性，则可使用 `IValidatableObject` 来实现类级别的验证，如下所示。</span><span class="sxs-lookup"><span data-stu-id="9fad3-207">While custom validation attributes work well for validating individual properties, implementing `IValidatableObject` can be used to implement class-level validation as seen here.</span></span>

[!code-csharp[](validation/sample/MovieIValidatable.cs?name=snippet_Validate)]

## <a name="client-side-validation"></a><span data-ttu-id="9fad3-208">客户端验证</span><span class="sxs-lookup"><span data-stu-id="9fad3-208">Client side validation</span></span>

<span data-ttu-id="9fad3-209">客户端验证极大地方便了用户。</span><span class="sxs-lookup"><span data-stu-id="9fad3-209">Client side validation is a great convenience for users.</span></span> <span data-ttu-id="9fad3-210">它节省了时间，让用户不必浪费时间等待服务器往返。</span><span class="sxs-lookup"><span data-stu-id="9fad3-210">It saves time they would otherwise spend waiting for a round trip to the server.</span></span> <span data-ttu-id="9fad3-211">从商业角度而言，即使每次只有几分之一秒，但如果每天有几百次，也会耗费大量的时间和成本，带来很多不必要的烦恼。</span><span class="sxs-lookup"><span data-stu-id="9fad3-211">In business terms, even a few fractions of seconds multiplied hundreds of times each day adds up to be a lot of time, expense, and frustration.</span></span> <span data-ttu-id="9fad3-212">简单直接的验证能够提高用户的工作效率和投入产出比。</span><span class="sxs-lookup"><span data-stu-id="9fad3-212">Straightforward and immediate validation enables users to work more efficiently and produce better quality input and output.</span></span>

<span data-ttu-id="9fad3-213">你必须有一个包含适当的 JavaScript 脚本引用的视图，才能让客户端验证正常工作，如下所示。</span><span class="sxs-lookup"><span data-stu-id="9fad3-213">You must have a view with the proper JavaScript script references in place for client-side validation to work as you see here.</span></span>

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml)]

<span data-ttu-id="9fad3-214">[jQuery 非介入式验证](https://github.com/aspnet/jquery-validation-unobtrusive)脚本是一个基于热门 [jQuery Validate](https://jqueryvalidation.org/) 插件的自定义 Microsoft 前端库。</span><span class="sxs-lookup"><span data-stu-id="9fad3-214">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="9fad3-215">如果没有 jQuery 非介入式验证，则必须在两个位置编码相同的验证逻辑：一次是在模型属性上的服务器端验证特性中，一次是在客户端脚本中（jQuery Validate 的 [`validate()`](https://jqueryvalidation.org/validate/) 方法示例展示了这种情况可能的复杂程度）。</span><span class="sxs-lookup"><span data-stu-id="9fad3-215">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server side validation attributes on model properties, and then again in client side scripts (the examples for jQuery Validate's [`validate()`](https://jqueryvalidation.org/validate/) method shows how complex this could become).</span></span> <span data-ttu-id="9fad3-216">MVC 的[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 [HTML 帮助程序](xref:mvc/views/overview)则能够使用模型属性中的验证特性和类型元数据，呈现需要验证的表单元素中的 HTML 5 [data- 特性](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes)。</span><span class="sxs-lookup"><span data-stu-id="9fad3-216">Instead, MVC's [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) are able to use the validation attributes and type metadata from model properties to render HTML 5 [data- attributes](http://w3c.github.io/html/dom.html#embedding-custom-non-visible-data-with-the-data-attributes) in the form elements that need validation.</span></span> <span data-ttu-id="9fad3-217">MVC 为内置属性和自定义属性生成 `data-` 属性。</span><span class="sxs-lookup"><span data-stu-id="9fad3-217">MVC generates the `data-` attributes for both built-in and custom attributes.</span></span> <span data-ttu-id="9fad3-218">然后，jQuery 非介入式验证分析 `data-` 属性并将逻辑传递给 jQuery Validate，从而将服务器端验证逻辑有效地“复制”到客户端。</span><span class="sxs-lookup"><span data-stu-id="9fad3-218">jQuery Unobtrusive Validation then parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server side validation logic to the client.</span></span> <span data-ttu-id="9fad3-219">可以使用相关标记帮助程序在客户端上显示验证错误，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9fad3-219">You can display validation errors on the client using the relevant tag helpers as shown here:</span></span>

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="9fad3-220">上面的标记帮助程序将呈现以下 HTML。</span><span class="sxs-lookup"><span data-stu-id="9fad3-220">The tag helpers above render the HTML below.</span></span> <span data-ttu-id="9fad3-221">请注意，HTML 输出中的 `data-` 特性与 `ReleaseDate` 属性的验证特性相对应。</span><span class="sxs-lookup"><span data-stu-id="9fad3-221">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="9fad3-222">下面的 `data-val-required` 属性包含在用户未填写发行日期字段时将显示的错误消息。</span><span class="sxs-lookup"><span data-stu-id="9fad3-222">The `data-val-required` attribute below contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="9fad3-223">jQuery 非介入式验证将此值传递给 jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) 方法，该方法随后在随附的 **\<span>** 元素中显示该消息。</span><span class="sxs-lookup"><span data-stu-id="9fad3-223">jQuery Unobtrusive Validation passes this value to the jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

```html
<form action="/Movies/Create" method="post">
    <div class="form-horizontal">
        <h4>Movie</h4>
        <div class="text-danger"></div>
        <div class="form-group">
            <label class="col-md-2 control-label" for="ReleaseDate">ReleaseDate</label>
            <div class="col-md-10">
                <input class="form-control" type="datetime"
                data-val="true" data-val-required="The ReleaseDate field is required."
                id="ReleaseDate" name="ReleaseDate" value="" />
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="9fad3-224">客户端验证将阻止提交，直到表单变为有效为止。</span><span class="sxs-lookup"><span data-stu-id="9fad3-224">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="9fad3-225">“提交”按钮运行 JavaScript：要么提交表单要么显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="9fad3-225">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="9fad3-226">MVC 基于属性的 .NET 数据类型确定类型特性值（有可能使用 `[DataType]` 特性进行重写）。</span><span class="sxs-lookup"><span data-stu-id="9fad3-226">MVC determines type attribute values based on the .NET data type of a property, possibly overridden using `[DataType]` attributes.</span></span> <span data-ttu-id="9fad3-227">`[DataType]` 基本特性不执行真正的服务器端验证。</span><span class="sxs-lookup"><span data-stu-id="9fad3-227">The base `[DataType]` attribute does no real server-side validation.</span></span> <span data-ttu-id="9fad3-228">浏览器选择自己的错误消息，并根据需要显示这些错误，但 jQuery 非介入式验证包可以重写消息，并使它们与其他消息的显示保持一致。</span><span class="sxs-lookup"><span data-stu-id="9fad3-228">Browsers choose their own error messages and display those errors as they wish, however the jQuery Validation Unobtrusive package can override the messages and display them consistently with others.</span></span> <span data-ttu-id="9fad3-229">当用户应用 `[DataType]` 子类（比如 `[EmailAddress]`）时，最常发生这种情况。</span><span class="sxs-lookup"><span data-stu-id="9fad3-229">This happens most obviously when users apply `[DataType]` subclasses such as `[EmailAddress]`.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="9fad3-230">向动态表单添加验证</span><span class="sxs-lookup"><span data-stu-id="9fad3-230">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="9fad3-231">由于 jQuery 非介入式验证会在第一次加载页面时将验证逻辑和参数传递到 jQuery Validate，因此，动态生成的表单不会自动展示验证。</span><span class="sxs-lookup"><span data-stu-id="9fad3-231">Because jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads, dynamically generated forms won't automatically exhibit validation.</span></span> <span data-ttu-id="9fad3-232">你必须指示 jQuery 非介入式验证在创建动态表单后立即对其进行分析。</span><span class="sxs-lookup"><span data-stu-id="9fad3-232">Instead, you must tell jQuery Unobtrusive Validation to parse the dynamic form immediately after creating it.</span></span> <span data-ttu-id="9fad3-233">例如，下面的代码展示如何对通过 AJAX 添加的表单设置客户端验证。</span><span class="sxs-lookup"><span data-stu-id="9fad3-233">For example, the code below shows how you might set up client side validation on a form added via AJAX.</span></span>

```js
$.get({
    url: "https://url/that/returns/a/form",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add form. " + errorThrown);
    },
    success: function(newFormHTML) {
        var container = document.getElementById("form-container");
        container.insertAdjacentHTML("beforeend", newFormHTML);
        var forms = container.getElementsByTagName("form");
        var newForm = forms[forms.length - 1];
        $.validator.unobtrusive.parse(newForm);
    }
})
```

<span data-ttu-id="9fad3-234">`$.validator.unobtrusive.parse()` 方法采用 jQuery 选择器作为它的一个参数。</span><span class="sxs-lookup"><span data-stu-id="9fad3-234">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="9fad3-235">此方法指示 jQuery 非介入式验证分析该选择器内表单的 `data-` 属性。</span><span class="sxs-lookup"><span data-stu-id="9fad3-235">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="9fad3-236">这些属性的值随后传递到 jQuery Validate 插件中，以便表单展示所需的客户端验证规则。</span><span class="sxs-lookup"><span data-stu-id="9fad3-236">The values of those attributes are then passed to the jQuery Validate plugin so that the form exhibits the desired client side validation rules.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="9fad3-237">向动态控件添加验证</span><span class="sxs-lookup"><span data-stu-id="9fad3-237">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="9fad3-238">也可以在动态生成各个控件（比如 `<input/>` 和 `<select/>`）时，更新表单上的验证规则。</span><span class="sxs-lookup"><span data-stu-id="9fad3-238">You can also update the validation rules on a form when individual controls, such as `<input/>`s and `<select/>`s, are dynamically generated.</span></span> <span data-ttu-id="9fad3-239">不能将用于这些元素的选择器直接传递到 `parse()` 方法，因为周围表单已进行分析并且不会更新。</span><span class="sxs-lookup"><span data-stu-id="9fad3-239">You cannot pass selectors for these elements to the `parse()` method directly because the surrounding form has already been parsed and won't update.</span></span> <span data-ttu-id="9fad3-240">应当先删除现有的验证数据，然后重新分析整个表单，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9fad3-240">Instead, you first remove the existing validation data, then reparse the entire form, as shown below:</span></span>

```js
$.get({
    url: "https://url/that/returns/a/control",
    dataType: "html",
    error: function(jqXHR, textStatus, errorThrown) {
        alert(textStatus + ": Couldn't add control. " + errorThrown);
    },
    success: function(newInputHTML) {
        var form = document.getElementById("my-form");
        form.insertAdjacentHTML("beforeend", newInputHTML);
        $(form).removeData("validator")    // Added by jQuery Validate
               .removeData("unobtrusiveValidation");   // Added by jQuery Unobtrusive Validation
        $.validator.unobtrusive.parse(form);
    }
})
```

## <a name="iclientmodelvalidator"></a><span data-ttu-id="9fad3-241">IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="9fad3-241">IClientModelValidator</span></span>

<span data-ttu-id="9fad3-242">可为自定义属性创建客户端逻辑，创建 [jQuery 验证](http://jqueryvalidation.org/documentation/)的适配器的[非介入式验证](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html)将在验证过程中，在客户端上自动为你执行此逻辑。</span><span class="sxs-lookup"><span data-stu-id="9fad3-242">You may create client side logic for your custom attribute, and [unobtrusive validation](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html) which creates an adapter to [jquery validation](http://jqueryvalidation.org/documentation/) will execute it on the client for you automatically as part of validation.</span></span> <span data-ttu-id="9fad3-243">第一步是通过实现 `IClientModelValidator` 接口来控制要添加哪些 data- 属性，如下所示：</span><span class="sxs-lookup"><span data-stu-id="9fad3-243">The first step is to control what data- attributes are added by implementing the `IClientModelValidator` interface as shown here:</span></span>

[!code-csharp[](validation/sample/ClassicMovieAttribute.cs?name=snippet_AddValidation)]

<span data-ttu-id="9fad3-244">实现此接口的属性可以将 HTML 属性添加到生成的字段。</span><span class="sxs-lookup"><span data-stu-id="9fad3-244">Attributes that implement this interface can add HTML attributes to generated fields.</span></span> <span data-ttu-id="9fad3-245">检查 `ReleaseDate` 元素的输出时，将显示与上一示例类似的 HTML，唯一不同的是，此示例包含一个已在 `IClientModelValidator` 的 `AddValidation` 方法中定义的 `data-val-classicmovie` 属性。</span><span class="sxs-lookup"><span data-stu-id="9fad3-245">Examining the output for the `ReleaseDate` element reveals HTML that's similar to the previous example, except now there's a `data-val-classicmovie` attribute that was defined in the `AddValidation` method of `IClientModelValidator`.</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="" />
```

<span data-ttu-id="9fad3-246">非介入式验证使用 `data-` 属性中的数据来显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="9fad3-246">Unobtrusive validation uses the data in the `data-` attributes to display error messages.</span></span> <span data-ttu-id="9fad3-247">不过，除非将规则或消息添加到 jQuery 的 `validator` 对象，否则 jQuery 并不知道它们的存在。</span><span class="sxs-lookup"><span data-stu-id="9fad3-247">However, jQuery doesn't know about rules or messages until you add them to jQuery's `validator` object.</span></span> <span data-ttu-id="9fad3-248">如以下示例所示，将一个自定义 `classicmovie` 客户端验证方法添加到 `validator` 对象。</span><span class="sxs-lookup"><span data-stu-id="9fad3-248">This is shown in the following example, which adds a custom `classicmovie` client validation method to the jQuery `validator` object.</span></span> <span data-ttu-id="9fad3-249">有关 `unobtrusive.adapters.add` 方法的说明，请参阅 [ASP.NET MVC 中的非介入式客户端验证](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html)。</span><span class="sxs-lookup"><span data-stu-id="9fad3-249">For an explanation of the `unobtrusive.adapters.add` method, see [Unobtrusive Client Validation in ASP.NET MVC](http://bradwilson.typepad.com/blog/2010/10/mvc3-unobtrusive-validation.html).</span></span>

[!code-javascript[](validation/sample/Views/Movies/Create.cshtml?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="9fad3-250">`classicmovie` 方法使用前面的代码对电影发行日期执行客户端验证。</span><span class="sxs-lookup"><span data-stu-id="9fad3-250">With the preceding code, the `classicmovie` method performs client-side validation on the movie release date.</span></span> <span data-ttu-id="9fad3-251">如果该方法返回 `false`，则显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="9fad3-251">The error message displays if the method returns `false`.</span></span>

## <a name="remote-validation"></a><span data-ttu-id="9fad3-252">远程验证</span><span class="sxs-lookup"><span data-stu-id="9fad3-252">Remote validation</span></span>

<span data-ttu-id="9fad3-253">远程验证是一项非常不错的功能，可在需要根据服务器上的数据验证客户端上的数据时使用。</span><span class="sxs-lookup"><span data-stu-id="9fad3-253">Remote validation is a great feature to use when you need to validate data on the client against data on the server.</span></span> <span data-ttu-id="9fad3-254">例如，应用可能需要验证某个电子邮件或用户名是否已被使用，并且它必须为此查询大量数据。</span><span class="sxs-lookup"><span data-stu-id="9fad3-254">For example, your app may need to verify whether an email or user name is already in use, and it must query a large amount of data to do so.</span></span> <span data-ttu-id="9fad3-255">为验证一个或几个字段而下载大量数据会占用过多资源。</span><span class="sxs-lookup"><span data-stu-id="9fad3-255">Downloading large sets of data for validating one or a few fields consumes too many resources.</span></span> <span data-ttu-id="9fad3-256">它还有可能暴露敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9fad3-256">It may also expose sensitive information.</span></span> <span data-ttu-id="9fad3-257">一种替代方法是发出往返请求来验证字段。</span><span class="sxs-lookup"><span data-stu-id="9fad3-257">An alternative is to make a round-trip request to validate a field.</span></span>

<span data-ttu-id="9fad3-258">可以分两步实现远程验证。</span><span class="sxs-lookup"><span data-stu-id="9fad3-258">You can implement remote validation in a two step process.</span></span> <span data-ttu-id="9fad3-259">首先，必须使用 `[Remote]` 属性为模型添加批注。</span><span class="sxs-lookup"><span data-stu-id="9fad3-259">First, you must annotate your model with the `[Remote]` attribute.</span></span> <span data-ttu-id="9fad3-260">`[Remote]` 属性采用多个重载，可用于将客户端 JavaScript 定向到要调用的相应代码。</span><span class="sxs-lookup"><span data-stu-id="9fad3-260">The `[Remote]` attribute accepts multiple overloads you can use to direct client side JavaScript to the appropriate code to call.</span></span> <span data-ttu-id="9fad3-261">下面的示例指向 `Users` 控制器的 `VerifyEmail` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="9fad3-261">The example below points to the `VerifyEmail` action method of the `Users` controller.</span></span>

[!code-csharp[](validation/sample/User.cs?name=snippet_UserEmailProperty)]

<span data-ttu-id="9fad3-262">第二步是按照 `[Remote]` 属性中的定义，将验证代码放入相应的操作方法。</span><span class="sxs-lookup"><span data-stu-id="9fad3-262">The second step is putting the validation code in the corresponding action method as defined in the `[Remote]` attribute.</span></span> <span data-ttu-id="9fad3-263">根据 jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) 方法文档，服务器响应必须是符合以下条件的 JSON 字符串：</span><span class="sxs-lookup"><span data-stu-id="9fad3-263">According to the jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method documentation, the server response must be a JSON string that's either:</span></span>

* <span data-ttu-id="9fad3-264">对于有效元素，为 `"true"`。</span><span class="sxs-lookup"><span data-stu-id="9fad3-264">`"true"` for valid elements.</span></span>
* <span data-ttu-id="9fad3-265">对于无效元素，为 `"false"`、`undefined` 或 `null`，使用默认错误消息。</span><span class="sxs-lookup"><span data-stu-id="9fad3-265">`"false"`, `undefined`, or `null` for invalid elements, using the default error message.</span></span>

<span data-ttu-id="9fad3-266">如果服务器响应是一个字符串（例如，`"That name is already taken, try peter123 instead"`），则该字符串显示为一条自定义错误消息来替代默认字符串。</span><span class="sxs-lookup"><span data-stu-id="9fad3-266">If the server response is a string (for example, `"That name is already taken, try peter123 instead"`), the string is displayed as a custom error message in place of the default string.</span></span>

<span data-ttu-id="9fad3-267">`VerifyEmail` 方法的定义遵循这些规则，如下所示。</span><span class="sxs-lookup"><span data-stu-id="9fad3-267">The definition of the `VerifyEmail` method follows these rules, as shown below.</span></span> <span data-ttu-id="9fad3-268">如果电子邮件已被占用，它会返回验证错误消息；如果电子邮件可用，则返回 `true`，并将结果包装在 `JsonResult` 对象中。</span><span class="sxs-lookup"><span data-stu-id="9fad3-268">It returns a validation error message if the email is taken, or `true` if the email is free, and wraps the result in a `JsonResult` object.</span></span> <span data-ttu-id="9fad3-269">然后，客户端可以使用返回的值，继续进行下一步操作或根据需要显示错误。</span><span class="sxs-lookup"><span data-stu-id="9fad3-269">The client side can then use the returned value to proceed or display the error if needed.</span></span>

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyEmail)]

<span data-ttu-id="9fad3-270">现在，当用户输入电子邮件时，视图中的 JavaScript 会发出远程调用，以了解该电子邮件是否已被占用，如果是，则显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="9fad3-270">Now when users enter an email, JavaScript in the view makes a remote call to see if that email has been taken and, if so, displays the error message.</span></span> <span data-ttu-id="9fad3-271">如果不是，用户就可以像往常一样提交表单。</span><span class="sxs-lookup"><span data-stu-id="9fad3-271">Otherwise, the user can submit the form as usual.</span></span>

<span data-ttu-id="9fad3-272">`[Remote]` 特性的 `AdditionalFields` 属性可用于根据服务器上的数据验证字段组合。</span><span class="sxs-lookup"><span data-stu-id="9fad3-272">The `AdditionalFields` property of the `[Remote]` attribute is useful for validating combinations of fields against data on the server.</span></span> <span data-ttu-id="9fad3-273">例如，如果上面的 `User` 模型具有两个附加属性，名为 `FirstName` 和 `LastName`，你可能想要验证该名称对尚未被现有用户占用。</span><span class="sxs-lookup"><span data-stu-id="9fad3-273">For example, if the `User` model from above had two additional properties called `FirstName` and `LastName`, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="9fad3-274">按以下代码所示定义新属性：</span><span class="sxs-lookup"><span data-stu-id="9fad3-274">You define the new properties as shown in the following code:</span></span>

[!code-csharp[](validation/sample/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="9fad3-275">`AdditionalFields` 可能已显式设置为字符串 `"FirstName"` 和 `"LastName"`，但使用 [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) 这样的操作符可简化稍后的重构过程。</span><span class="sxs-lookup"><span data-stu-id="9fad3-275">`AdditionalFields` could've been set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operator like this simplifies later refactoring.</span></span> <span data-ttu-id="9fad3-276">然后，用于执行验证的操作方法必须采用两个参数，一个用于 `FirstName` 的值，一个用于 `LastName` 的值。</span><span class="sxs-lookup"><span data-stu-id="9fad3-276">The action method to perform the validation must then accept two arguments, one for the value of `FirstName` and one for the value of `LastName`.</span></span>

[!code-csharp[](validation/sample/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="9fad3-277">现在，当用户输入名和姓时，JavaScript 会:</span><span class="sxs-lookup"><span data-stu-id="9fad3-277">Now when users enter a first and last name, JavaScript:</span></span>

* <span data-ttu-id="9fad3-278">发出远程调用，以了解该名称对是否已被占用。</span><span class="sxs-lookup"><span data-stu-id="9fad3-278">Makes a remote call to see if that pair of names has been taken.</span></span>
* <span data-ttu-id="9fad3-279">如果被占用，则显示一条错误消息。</span><span class="sxs-lookup"><span data-stu-id="9fad3-279">If the pair has been taken, an error message is displayed.</span></span>
* <span data-ttu-id="9fad3-280">如果未被占用，则用户可以提交表单。</span><span class="sxs-lookup"><span data-stu-id="9fad3-280">If not taken, the user can submit the form.</span></span>

<span data-ttu-id="9fad3-281">如果需要使用 `[Remote]` 特性验证两个或更多附加字段，可将其以逗号分隔的列表形式列出。</span><span class="sxs-lookup"><span data-stu-id="9fad3-281">If you need to validate two or more additional fields with the `[Remote]` attribute, you provide them as a comma-delimited list.</span></span> <span data-ttu-id="9fad3-282">例如，若要向模型中添加 `MiddleName` 属性，可按以下代码所示设置 `[Remote]` 特性：</span><span class="sxs-lookup"><span data-stu-id="9fad3-282">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following code:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="9fad3-283">`AdditionalFields` 与所有属性参数一样，必须是常量表达式。</span><span class="sxs-lookup"><span data-stu-id="9fad3-283">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="9fad3-284">因此，不能使用[内插字符串](/dotnet/csharp/language-reference/keywords/interpolated-strings)或调用 <xref:System.String.Join*> 来初始化 `AdditionalFields`。</span><span class="sxs-lookup"><span data-stu-id="9fad3-284">Therefore, you must not use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span> <span data-ttu-id="9fad3-285">对于添加到 `[Remote]` 特性的每个附加字段，都必须向相应的控制器操作方法另外添加一个参数。</span><span class="sxs-lookup"><span data-stu-id="9fad3-285">For every additional field that you add to the `[Remote]` attribute, you must add another argument to the corresponding controller action method.</span></span>
