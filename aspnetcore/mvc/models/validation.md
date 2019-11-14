---
title: ASP.NET Core MVC 中的模型验证
author: rick-anderson
description: 了解 ASP.NET Core MVC 和 Razor Pages 中的模型验证。
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2019
uid: mvc/models/validation
ms.openlocfilehash: 8e9e588c8665962b2fe285b0feab977b16938154
ms.sourcegitcommit: 99cdd60a26ff0970880bb43c558d78b1ef17c237
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2019
ms.locfileid: "73915529"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="71c53-103">ASP.NET Core MVC 和 Razor Pages 中的模型验证</span><span class="sxs-lookup"><span data-stu-id="71c53-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="71c53-104">本文介绍如何在 ASP.NET Core MVC 或 Razor Pages 应用中验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="71c53-104">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="71c53-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="71c53-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="71c53-106">模型状态</span><span class="sxs-lookup"><span data-stu-id="71c53-106">Model state</span></span>

<span data-ttu-id="71c53-107">模型状态表示两个子系统的错误：模型绑定和模型验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-107">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="71c53-108">[模型绑定](model-binding.md)的错误通常是数据转换错误（例如，在要求为整数的字段中输入“x”）。</span><span class="sxs-lookup"><span data-stu-id="71c53-108">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="71c53-109">模型验证在模型绑定之后进行，并在数据不符合业务规则时报告错误（例如，在要求评级为 1 至 5 之间的字段中输入 0）。</span><span class="sxs-lookup"><span data-stu-id="71c53-109">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="71c53-110">模型绑定和验证都在执行控制器操作或 Razor Pages 处理程序方法之前进行。</span><span class="sxs-lookup"><span data-stu-id="71c53-110">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="71c53-111">Web 应用负责检查 `ModelState.IsValid` 并做出相应响应。</span><span class="sxs-lookup"><span data-stu-id="71c53-111">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="71c53-112">Web 应用通常会重新显示带有错误消息的页面：</span><span class="sxs-lookup"><span data-stu-id="71c53-112">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="71c53-113">如果 Web API 控制器具有 `[ApiController]` 特性，则它们不必检查 `ModelState.IsValid`。</span><span class="sxs-lookup"><span data-stu-id="71c53-113">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="71c53-114">在此情况下，如果模型状态无效，将返回包含问题详细信息的自动 HTTP 400 响应。</span><span class="sxs-lookup"><span data-stu-id="71c53-114">In that case, an automatic HTTP 400 response containing issue details is returned when model state is invalid.</span></span> <span data-ttu-id="71c53-115">有关详细信息，请参阅[自动 HTTP 400 响应](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="71c53-115">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="71c53-116">重新运行验证</span><span class="sxs-lookup"><span data-stu-id="71c53-116">Rerun validation</span></span>

<span data-ttu-id="71c53-117">验证自动进行，但是可能需要手动进行重复验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-117">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="71c53-118">例如，你可能为属性计算一个值，并且希望将属性设置为所计算的值后，再重新运行验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-118">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="71c53-119">若要重新运行验证，请调用 `TryValidateModel` 方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-119">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/sample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="71c53-120">验证特性</span><span class="sxs-lookup"><span data-stu-id="71c53-120">Validation attributes</span></span>

<span data-ttu-id="71c53-121">通过验证特性可以为模型属性指定验证规则。</span><span class="sxs-lookup"><span data-stu-id="71c53-121">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="71c53-122">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample)的以下示例显示使用验证特性进行注释的模型类。</span><span class="sxs-lookup"><span data-stu-id="71c53-122">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="71c53-123">`[ClassicMovie]` 特性为自定义的验证特性，其他特性为内置的验证特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-123">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="71c53-124">（`[ClassicMovie2]` 未显示，它表示实现自定义特性的另一种方法。）</span><span class="sxs-lookup"><span data-stu-id="71c53-124">(Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.)</span></span>

[!code-csharp[](validation/sample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="71c53-125">内置特性</span><span class="sxs-lookup"><span data-stu-id="71c53-125">Built-in attributes</span></span>

<span data-ttu-id="71c53-126">以下是一些内置验证特性：</span><span class="sxs-lookup"><span data-stu-id="71c53-126">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="71c53-127">`[CreditCard]`：验证属性是否有信用卡格式。</span><span class="sxs-lookup"><span data-stu-id="71c53-127">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="71c53-128">`[Compare]`：验证模型中的两个属性是否匹配。</span><span class="sxs-lookup"><span data-stu-id="71c53-128">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="71c53-129">`[EmailAddress]`：验证属性是否有电子邮件格式。</span><span class="sxs-lookup"><span data-stu-id="71c53-129">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="71c53-130">`[Phone]`：验证属性是否有电话号码格式。</span><span class="sxs-lookup"><span data-stu-id="71c53-130">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="71c53-131">`[Range]`：验证属性值是否在指定范围内。</span><span class="sxs-lookup"><span data-stu-id="71c53-131">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="71c53-132">`[RegularExpression]`：验证属性值是否与指定的正则表达式匹配。</span><span class="sxs-lookup"><span data-stu-id="71c53-132">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="71c53-133">`[Required]`：验证字段是否非 NULL。</span><span class="sxs-lookup"><span data-stu-id="71c53-133">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="71c53-134">请参阅 [[必需] 特性](#required-attribute)，获取关于该特性的行为的详细信息。</span><span class="sxs-lookup"><span data-stu-id="71c53-134">See [[Required] attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="71c53-135">`[StringLength]`：验证字符串属性值是否未超过指定长度限制。</span><span class="sxs-lookup"><span data-stu-id="71c53-135">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="71c53-136">`[Url]`：验证属性是否有 URL 格式。</span><span class="sxs-lookup"><span data-stu-id="71c53-136">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="71c53-137">`[Remote]`：通过调用服务器上的操作方法，验证客户端上的输入。</span><span class="sxs-lookup"><span data-stu-id="71c53-137">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="71c53-138">请参阅 [[远程] 特性](#remote-attribute)获取关于该特性的行为的详细信息。</span><span class="sxs-lookup"><span data-stu-id="71c53-138">See [[Remote] attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="71c53-139">在 [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 命名空间中可找到验证特性的完整列表。</span><span class="sxs-lookup"><span data-stu-id="71c53-139">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="71c53-140">错误消息</span><span class="sxs-lookup"><span data-stu-id="71c53-140">Error messages</span></span>

<span data-ttu-id="71c53-141">通过验证特性可以指定要为无效输入显示的错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-141">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="71c53-142">例如:</span><span class="sxs-lookup"><span data-stu-id="71c53-142">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="71c53-143">在内部，特性使用用于字段名的某个占位符调用 `String.Format`，有时还使用额外占位符。</span><span class="sxs-lookup"><span data-stu-id="71c53-143">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="71c53-144">例如:</span><span class="sxs-lookup"><span data-stu-id="71c53-144">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="71c53-145">应用于 `Name` 属性时，上述代码创建的错误消息将为“名称长度必须介于 6 到 8 之间”。</span><span class="sxs-lookup"><span data-stu-id="71c53-145">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="71c53-146">若要查找为特定特性的错误消息而传递给 `String.Format` 的参数，请参阅 [DataAnnotations 源代码](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="71c53-146">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="71c53-147">[必需] 特性</span><span class="sxs-lookup"><span data-stu-id="71c53-147">[Required] attribute</span></span>

<span data-ttu-id="71c53-148">默认情况下，验证系统将不可为 null 的参数或属性视为具有 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-148">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="71c53-149">`decimal` 和 `int` 等[值类型](/dotnet/csharp/language-reference/keywords/value-types)是不可为 null 的类型。</span><span class="sxs-lookup"><span data-stu-id="71c53-149">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="71c53-150">[必需] 服务器上的验证</span><span class="sxs-lookup"><span data-stu-id="71c53-150">[Required] validation on the server</span></span>

<span data-ttu-id="71c53-151">在服务器上，如果属性为 null，则认为所需值缺失。</span><span class="sxs-lookup"><span data-stu-id="71c53-151">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="71c53-152">不可为 null 的字段始终有效，并且从不显示 [必需] 特性的错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-152">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="71c53-153">但是，不可为 null 的属性的模型绑定可能会失败，从而导致 `The value '' is invalid` 等错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-153">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="71c53-154">若要为不可为 null 的类型的服务器端验证指定自定义错误消息，可使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="71c53-154">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="71c53-155">将字段设置为可以为 null（例如，`decimal?`而不是 `decimal`）。</span><span class="sxs-lookup"><span data-stu-id="71c53-155">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="71c53-156">类似于标准的可以为 null 的类型来处理[可以为 null\<T>](/dotnet/csharp/programming-guide/nullable-types/) 值类型。</span><span class="sxs-lookup"><span data-stu-id="71c53-156">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="71c53-157">指定模型绑定要使用的默认错误消息，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-157">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="71c53-158">有关模型绑定错误（可以为其设置默认消息）的更多信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>。</span><span class="sxs-lookup"><span data-stu-id="71c53-158">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="71c53-159">[必需] 客户端上的验证</span><span class="sxs-lookup"><span data-stu-id="71c53-159">[Required] validation on the client</span></span>

<span data-ttu-id="71c53-160">在客户端上处理不可为 null 类型和字符串的方式与在服务器上不同。</span><span class="sxs-lookup"><span data-stu-id="71c53-160">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="71c53-161">在客户端上：</span><span class="sxs-lookup"><span data-stu-id="71c53-161">On the client:</span></span>

* <span data-ttu-id="71c53-162">只有在为值输入一个输入时，才认为该值存在。</span><span class="sxs-lookup"><span data-stu-id="71c53-162">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="71c53-163">因此，客户端验证处理不可为 null 类型的方式与处理可以为 null 类型的方式相同。</span><span class="sxs-lookup"><span data-stu-id="71c53-163">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="71c53-164">jQuery 验证[必需](https://jqueryvalidation.org/required-method/)方法将字符串字段中的空格视为有效输入。</span><span class="sxs-lookup"><span data-stu-id="71c53-164">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="71c53-165">如果只输入空格，服务器端验证会将必需的字符串字段视为无效。</span><span class="sxs-lookup"><span data-stu-id="71c53-165">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="71c53-166">如前所述，将不可为 null 类型视为具有 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-166">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="71c53-167">这意味着即使不应用 `[Required]` 特性，也可进行客户端验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-167">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="71c53-168">但如果不使用该特性，将收到默认错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-168">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="71c53-169">若要指定自定义错误消息，使用该特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-169">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="71c53-170">[远程] 特性</span><span class="sxs-lookup"><span data-stu-id="71c53-170">[Remote] attribute</span></span>

<span data-ttu-id="71c53-171">`[Remote]` 特性实现客户端验证，该验证需要在服务器上调用方法，以确定字段输入是否有效。</span><span class="sxs-lookup"><span data-stu-id="71c53-171">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="71c53-172">例如，应用可能需要验证用户名是否已在使用。</span><span class="sxs-lookup"><span data-stu-id="71c53-172">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="71c53-173">若要实现远程验证：</span><span class="sxs-lookup"><span data-stu-id="71c53-173">To implement remote validation:</span></span>

1. <span data-ttu-id="71c53-174">创建可供 JavaScript 调用的操作方法。</span><span class="sxs-lookup"><span data-stu-id="71c53-174">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="71c53-175">jQuery Validate [远程](https://jqueryvalidation.org/remote-method/)方法要求 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="71c53-175">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="71c53-176">`"true"` 表示输入数据有效。</span><span class="sxs-lookup"><span data-stu-id="71c53-176">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="71c53-177">`"false"`、`undefined` 或 `null` 表示输入无效。</span><span class="sxs-lookup"><span data-stu-id="71c53-177">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="71c53-178">显示默认错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-178">Display the default error message.</span></span>
   * <span data-ttu-id="71c53-179">任何其他字符串都表示输入无效。</span><span class="sxs-lookup"><span data-stu-id="71c53-179">Any other string means the input is invalid.</span></span> <span data-ttu-id="71c53-180">将字符串显示为自定义错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-180">Display the string as a custom error message.</span></span>

   <span data-ttu-id="71c53-181">以下是返回自定义错误消息的操作方法示例：</span><span class="sxs-lookup"><span data-stu-id="71c53-181">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="71c53-182">在模型类中，使用指向验证操作方法的 `[Remote]` 特性注释属性，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-182">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="71c53-183">`[Remote]` 特性位于 `Microsoft.AspNetCore.Mvc` 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="71c53-183">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="71c53-184">如果未使用 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 元包，则请安装 [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="71c53-184">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="71c53-185">其他字段</span><span class="sxs-lookup"><span data-stu-id="71c53-185">Additional fields</span></span>

<span data-ttu-id="71c53-186">通过 `[Remote]` 特性的 `AdditionalFields` 属性可以根据服务器上的数据验证字段组合。</span><span class="sxs-lookup"><span data-stu-id="71c53-186">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="71c53-187">例如，如果 `User` 模型具有 `FirstName` 和 `LastName` 属性，可能需要验证该名称对尚未被现有用户占用。</span><span class="sxs-lookup"><span data-stu-id="71c53-187">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="71c53-188">下面的示例演示如何使用 `AdditionalFields`：</span><span class="sxs-lookup"><span data-stu-id="71c53-188">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="71c53-189">`AdditionalFields` 可能已显式设置为字符串 `"FirstName"` 和 `"LastName"`，但使用 [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) 操作符可简化稍后的重构过程。</span><span class="sxs-lookup"><span data-stu-id="71c53-189">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="71c53-190">此验证的操作方法必须接受 first name 和 last name 参数：</span><span class="sxs-lookup"><span data-stu-id="71c53-190">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="71c53-191">用户输入名字或姓氏时，JavaScript 会进行远程调用，查看该名称对是否已占用。</span><span class="sxs-lookup"><span data-stu-id="71c53-191">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="71c53-192">若要验证两个或更多附加字段，可将其以逗号分隔列表形式提供。</span><span class="sxs-lookup"><span data-stu-id="71c53-192">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="71c53-193">例如，若要向模型中添加 `MiddleName` 属性，可按以下示例所示设置 `[Remote]` 特性：</span><span class="sxs-lookup"><span data-stu-id="71c53-193">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="71c53-194">`AdditionalFields` 与所有属性参数一样，必须是常量表达式。</span><span class="sxs-lookup"><span data-stu-id="71c53-194">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="71c53-195">因此，请勿使用[内插字符串](/dotnet/csharp/language-reference/keywords/interpolated-strings)或调用 <xref:System.String.Join*> 来初始化 `AdditionalFields`。</span><span class="sxs-lookup"><span data-stu-id="71c53-195">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="71c53-196">内置特性的替代特性</span><span class="sxs-lookup"><span data-stu-id="71c53-196">Alternatives to built-in attributes</span></span>

<span data-ttu-id="71c53-197">如果需要并非由内置属性提供的验证，可以：</span><span class="sxs-lookup"><span data-stu-id="71c53-197">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="71c53-198">[创建自定义特性](#custom-attributes)。</span><span class="sxs-lookup"><span data-stu-id="71c53-198">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="71c53-199">[实现 IValidatableObject](#ivalidatableobject)。</span><span class="sxs-lookup"><span data-stu-id="71c53-199">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="71c53-200">自定义特性</span><span class="sxs-lookup"><span data-stu-id="71c53-200">Custom attributes</span></span>

<span data-ttu-id="71c53-201">对于内置验证特性无法处理的情况，可以创建自定义验证特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-201">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="71c53-202">创建继承自 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> 的类，并替代 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> 方法。</span><span class="sxs-lookup"><span data-stu-id="71c53-202">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="71c53-203">`IsValid` 方法接受名为 value 的对象，该对象是要进行验证的输入  。</span><span class="sxs-lookup"><span data-stu-id="71c53-203">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="71c53-204">重载还接受 `ValidationContext` 对象，该对象提供其他信息，例如模型绑定创建的模型实例。</span><span class="sxs-lookup"><span data-stu-id="71c53-204">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="71c53-205">以下示例验证“经典”流派电影的发行日期是否不晚于指定年份  。</span><span class="sxs-lookup"><span data-stu-id="71c53-205">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="71c53-206">`[ClassicMovie2]` 特性首先检查流派，只有流派为“经典”时才会继续检查  。</span><span class="sxs-lookup"><span data-stu-id="71c53-206">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="71c53-207">如果电影流派为经典，它会检查发行日期，确保该日期不晚于传递给特性构造函数的限值。）</span><span class="sxs-lookup"><span data-stu-id="71c53-207">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/sample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="71c53-208">上述示例中的 `movie` 变量表示 `Movie` 对象，其中包含表单提交中的数据。</span><span class="sxs-lookup"><span data-stu-id="71c53-208">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="71c53-209">`IsValid` 方法检查日期和流派。</span><span class="sxs-lookup"><span data-stu-id="71c53-209">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="71c53-210">验证成功时，`IsValid` 返回 `ValidationResult.Success` 代码。</span><span class="sxs-lookup"><span data-stu-id="71c53-210">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="71c53-211">验证失败时，返回 `ValidationResult` 和错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-211">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="71c53-212">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="71c53-212">IValidatableObject</span></span>

<span data-ttu-id="71c53-213">上述示例只适用于 `Movie` 类型。</span><span class="sxs-lookup"><span data-stu-id="71c53-213">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="71c53-214">类级别验证的另一方式是在模型类中实现 `IValidatableObject`，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-214">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/sample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="71c53-215">顶级节点验证</span><span class="sxs-lookup"><span data-stu-id="71c53-215">Top-level node validation</span></span>

<span data-ttu-id="71c53-216">顶级节点包括：</span><span class="sxs-lookup"><span data-stu-id="71c53-216">Top-level nodes include:</span></span>

* <span data-ttu-id="71c53-217">操作参数</span><span class="sxs-lookup"><span data-stu-id="71c53-217">Action parameters</span></span>
* <span data-ttu-id="71c53-218">控制器属性</span><span class="sxs-lookup"><span data-stu-id="71c53-218">Controller properties</span></span>
* <span data-ttu-id="71c53-219">页处理程序参数</span><span class="sxs-lookup"><span data-stu-id="71c53-219">Page handler parameters</span></span>
* <span data-ttu-id="71c53-220">页模型属性</span><span class="sxs-lookup"><span data-stu-id="71c53-220">Page model properties</span></span>

<span data-ttu-id="71c53-221">除了验证模型属性之外，还验证了模型绑定的顶级节点。</span><span class="sxs-lookup"><span data-stu-id="71c53-221">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="71c53-222">在示例应用的以下示例中，`VerifyPhone` 方法使用 <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> 验证 `phone` 操作参数：</span><span class="sxs-lookup"><span data-stu-id="71c53-222">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="71c53-223">顶级节点可以将 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> 与验证属性结合使用。</span><span class="sxs-lookup"><span data-stu-id="71c53-223">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="71c53-224">在示例应用的以下示例中，`CheckAge` 方法指定在提交表单时必须从查询字符串绑定 `age` 参数：</span><span class="sxs-lookup"><span data-stu-id="71c53-224">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="71c53-225">在“检查年限”页 (CheckAge.cshtml) 中，有两个表单  。</span><span class="sxs-lookup"><span data-stu-id="71c53-225">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="71c53-226">第一个表单提交 `Age` 的值 `99` 作为查询字符串：`https://localhost:5001/Users/CheckAge?Age=99`。</span><span class="sxs-lookup"><span data-stu-id="71c53-226">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="71c53-227">当提交查询字符串中格式设置正确的 `age` 参数时，表单将进行验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-227">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="71c53-228">“检查年限”页面上的第二个表单提交请求正文中的 `Age` 值，验证失败。</span><span class="sxs-lookup"><span data-stu-id="71c53-228">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="71c53-229">绑定失败，因为 `age` 参数必须来自查询字符串。</span><span class="sxs-lookup"><span data-stu-id="71c53-229">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="71c53-230">在 `CompatibilityVersion.Version_2_1` 或更高版本上运行时，默认启用顶级节点验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-230">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="71c53-231">否则，禁用顶级节点验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-231">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="71c53-232">通过在 (`Startup.ConfigureServices`) 中设置 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> 属性，可以替代默认选项，如下所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-232">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="71c53-233">最大错误数</span><span class="sxs-lookup"><span data-stu-id="71c53-233">Maximum errors</span></span>

<span data-ttu-id="71c53-234">达到最大错误数（默认为 200）时，验证停止。</span><span class="sxs-lookup"><span data-stu-id="71c53-234">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="71c53-235">可以使用 `Startup.ConfigureServices` 中的以下代码配置该数字：</span><span class="sxs-lookup"><span data-stu-id="71c53-235">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="71c53-236">最大递归次数</span><span class="sxs-lookup"><span data-stu-id="71c53-236">Maximum recursion</span></span>

<span data-ttu-id="71c53-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> 遍历所验证模型的对象图。</span><span class="sxs-lookup"><span data-stu-id="71c53-237"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="71c53-238">如果模型非常深或无限递归，验证可能导致堆栈溢出。</span><span class="sxs-lookup"><span data-stu-id="71c53-238">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="71c53-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) 提供了在访问者递归超过配置深度时提前停止验证的方法。</span><span class="sxs-lookup"><span data-stu-id="71c53-239">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="71c53-240">在 `CompatibilityVersion.Version_2_2` 或更高版本上运行时，`MvcOptions.MaxValidationDepth` 的默认值为 200。</span><span class="sxs-lookup"><span data-stu-id="71c53-240">The default value of `MvcOptions.MaxValidationDepth` is 200 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="71c53-241">对于更低版本，该值为 null，这表示没有深度约束。</span><span class="sxs-lookup"><span data-stu-id="71c53-241">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="71c53-242">自动短路</span><span class="sxs-lookup"><span data-stu-id="71c53-242">Automatic short-circuit</span></span>

<span data-ttu-id="71c53-243">如果模型图不需要验证，验证将自动短路（跳过）。</span><span class="sxs-lookup"><span data-stu-id="71c53-243">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="71c53-244">运行时为其跳过验证的对象包括基元集合（如 `byte[]`、`string[]`、`Dictionary<string, string>`）和不具有任何验证器的复杂对象图。</span><span class="sxs-lookup"><span data-stu-id="71c53-244">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="71c53-245">禁用验证</span><span class="sxs-lookup"><span data-stu-id="71c53-245">Disable validation</span></span>

<span data-ttu-id="71c53-246">若要禁用验证：</span><span class="sxs-lookup"><span data-stu-id="71c53-246">To disable validation:</span></span>

1. <span data-ttu-id="71c53-247">创建不会将任何字段标记为无效的 `IObjectModelValidator` 实现。</span><span class="sxs-lookup"><span data-stu-id="71c53-247">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/sample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="71c53-248">将以下代码添加到 `Startup.ConfigureServices`，以便替换依赖项注入容器中的默认 `IObjectModelValidator` 实现。</span><span class="sxs-lookup"><span data-stu-id="71c53-248">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="71c53-249">你可能仍然会看到模型绑定的模型状态错误。</span><span class="sxs-lookup"><span data-stu-id="71c53-249">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="71c53-250">客户端验证</span><span class="sxs-lookup"><span data-stu-id="71c53-250">Client-side validation</span></span>

<span data-ttu-id="71c53-251">客户端验证将阻止提交，直到表单变为有效为止。</span><span class="sxs-lookup"><span data-stu-id="71c53-251">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="71c53-252">“提交”按钮运行 JavaScript：要么提交表单要么显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-252">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="71c53-253">表单上存在输入错误时，客户端验证会避免到服务器的不必要往返。</span><span class="sxs-lookup"><span data-stu-id="71c53-253">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="71c53-254">_Layout.cshtml 和 _ValidationScriptsPartial.cshtml 中的以下脚本引用支持客户端验证   ：</span><span class="sxs-lookup"><span data-stu-id="71c53-254">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="71c53-255">[jQuery 非介入式验证](https://github.com/aspnet/jquery-validation-unobtrusive)脚本是一个基于热门 [jQuery Validate](https://jqueryvalidation.org/) 插件的自定义 Microsoft 前端库。</span><span class="sxs-lookup"><span data-stu-id="71c53-255">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="71c53-256">如果没有 jQuery 非介入式验证，则必须在两个位置编码相同的验证逻辑：一次是在模型属性上的服务器端验证特性中，一次是在客户端脚本中。</span><span class="sxs-lookup"><span data-stu-id="71c53-256">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="71c53-257">[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 [HTML 帮助程序](xref:mvc/views/overview)则使用模型属性中的验证特性和类型元数据，呈现需要验证的表单元素的 HTML 5 `data-` 特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-257">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="71c53-258">jQuery 非介入式验证分析 `data-` 特性并将逻辑传递给 jQuery Validate，从而将服务器端验证逻辑有效地“复制”到客户端。</span><span class="sxs-lookup"><span data-stu-id="71c53-258">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="71c53-259">可以使用标记帮助程序在客户端上显示验证错误，如下所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-259">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="71c53-260">上述标记帮助程序呈现以下 HTML。</span><span class="sxs-lookup"><span data-stu-id="71c53-260">The preceding tag helpers render the following HTML.</span></span>

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
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="71c53-261">请注意，HTML 输出中的 `data-` 特性与 `ReleaseDate` 属性的验证特性相对应。</span><span class="sxs-lookup"><span data-stu-id="71c53-261">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="71c53-262">`data-val-required` 特性包含在用户未填写上映日期字段时将显示的错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-262">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="71c53-263">jQuery 非介入式验证将此值传递给 jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) 方法，该方法随后在随附的 **\<span>** 元素中显示该消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-263">jQuery Unobtrusive Validation passes this value to the jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="71c53-264">如果 `[DataType]` 特性未替代属性的 .NET 类型，则数据类型验证基于该类型。</span><span class="sxs-lookup"><span data-stu-id="71c53-264">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="71c53-265">浏览器具有自己的默认错误消息，但是 jQuery 验证非介入式验证包可以替代这些消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-265">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="71c53-266">通过 `[DataType]` 特性和 `[EmailAddress]` 等子类可以指定错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-266">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="71c53-267">向动态表单添加验证</span><span class="sxs-lookup"><span data-stu-id="71c53-267">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="71c53-268">jQuery 非介入式验证会在第一次加载页面时将验证逻辑和参数传递到 jQuery Validate。</span><span class="sxs-lookup"><span data-stu-id="71c53-268">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="71c53-269">因此，不会对动态生成的表单自动执行验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-269">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="71c53-270">若要启用验证，指示 jQuery 非介入式验证在创建动态表单后立即对其进行分析。</span><span class="sxs-lookup"><span data-stu-id="71c53-270">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="71c53-271">例如，以下代码在通过 AJAX 添加的表单上设置客户端验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-271">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="71c53-272">`$.validator.unobtrusive.parse()` 方法采用 jQuery 选择器作为它的一个参数。</span><span class="sxs-lookup"><span data-stu-id="71c53-272">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="71c53-273">此方法指示 jQuery 非介入式验证分析该选择器内表单的 `data-` 属性。</span><span class="sxs-lookup"><span data-stu-id="71c53-273">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="71c53-274">然后将这些特性的值传递给 jQuery Validate 插件。</span><span class="sxs-lookup"><span data-stu-id="71c53-274">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="71c53-275">向动态控件添加验证</span><span class="sxs-lookup"><span data-stu-id="71c53-275">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="71c53-276">`$.validator.unobtrusive.parse()` 方法适用于整个表单，而不是 `<input>` 和 `<select/>` 等单个动态生成的控件。</span><span class="sxs-lookup"><span data-stu-id="71c53-276">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="71c53-277">若要重新分析表单，删除之前分析表单时添加的验证数据，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-277">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="71c53-278">自定义客户端验证</span><span class="sxs-lookup"><span data-stu-id="71c53-278">Custom client-side validation</span></span>

<span data-ttu-id="71c53-279">生成适用于自定义 jQuery Validate 适配器的 `data-` HTML 特性，完成自定义客户端验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-279">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="71c53-280">以下示例适配器代码是为本文前面部分介绍的 `ClassicMovie` 和 `ClassicMovie2` 特性编写的：</span><span class="sxs-lookup"><span data-stu-id="71c53-280">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/sample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="71c53-281">有关如何编写适配器的信息，请参阅 [jQuery Validate 文档](https://jqueryvalidation.org/documentation/)。</span><span class="sxs-lookup"><span data-stu-id="71c53-281">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="71c53-282">给定字段的适配器的使用由 `data-` 特性触发，这些特性：</span><span class="sxs-lookup"><span data-stu-id="71c53-282">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="71c53-283">将字段标记为正在验证 (`data-val="true"`)。</span><span class="sxs-lookup"><span data-stu-id="71c53-283">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="71c53-284">确定验证规则名称和错误消息文本（例如，`data-val-rulename="Error message."`）。</span><span class="sxs-lookup"><span data-stu-id="71c53-284">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="71c53-285">提供验证器所需的任何其他参数（例如，`data-val-rulename-parm1="value"`）。</span><span class="sxs-lookup"><span data-stu-id="71c53-285">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="71c53-286">以下示例显示示例应用的 `ClassicMovie` 特性的 `data-` 特性：</span><span class="sxs-lookup"><span data-stu-id="71c53-286">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="71c53-287">如前所述，[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 [HTML 帮助程序](xref:mvc/views/overview)使用验证特性的信息呈现 `data-` 特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-287">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="71c53-288">编写用于创建自定义 `data-` HTML 特性的代码有以下两种方式：</span><span class="sxs-lookup"><span data-stu-id="71c53-288">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="71c53-289">创建派生自 `AttributeAdapterBase<TAttribute>` 的类和实现 `IValidationAttributeAdapterProvider` 的类，并在 DI 中注册特性及其适配器。</span><span class="sxs-lookup"><span data-stu-id="71c53-289">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="71c53-290">此方法遵循[单一责任主体](https://wikipedia.org/wiki/Single_responsibility_principle)，这体现在与服务器相关的验证代码和与客户端相关的验证代码位于不同的类中。</span><span class="sxs-lookup"><span data-stu-id="71c53-290">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="71c53-291">适配器还有以下优势：由于适配器是在 DI 中注册的，所以如果需要，适配器可以使用 DI 中的其他服务。</span><span class="sxs-lookup"><span data-stu-id="71c53-291">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="71c53-292">在 `ValidationAttribute` 类中实现 `IClientModelValidator`。</span><span class="sxs-lookup"><span data-stu-id="71c53-292">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="71c53-293">如果特性既不进行任何服务器端验证，也不需要 DI 的任何服务，则此方法可能适用。</span><span class="sxs-lookup"><span data-stu-id="71c53-293">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="71c53-294">用于客户端验证的 AttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="71c53-294">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="71c53-295">在 HTML 中呈现 `data-` 特性的方法在示例应用中由 `ClassicMovie` 特性使用。</span><span class="sxs-lookup"><span data-stu-id="71c53-295">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="71c53-296">若要使用此方法添加客户端验证：</span><span class="sxs-lookup"><span data-stu-id="71c53-296">To add client validation by using this method:</span></span>

1. <span data-ttu-id="71c53-297">为自定义验证特性创建特性适配器类。</span><span class="sxs-lookup"><span data-stu-id="71c53-297">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="71c53-298">从 [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2) 派生类。</span><span class="sxs-lookup"><span data-stu-id="71c53-298">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="71c53-299">创建将 `data-` 特性添加到所呈现输出中的 `AddValidation` 方法，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-299">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="71c53-300">创建实现 <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> 的适配器提供程序类。</span><span class="sxs-lookup"><span data-stu-id="71c53-300">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="71c53-301">使用 `GetAttributeAdapter` 方法，将自定义属性传递给适配器的构造函数，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-301">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="71c53-302">在 `Startup.ConfigureServices` 中为 DI 注册适配器提供程序：</span><span class="sxs-lookup"><span data-stu-id="71c53-302">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="71c53-303">用于客户端验证的 IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="71c53-303">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="71c53-304">在 HTML 中呈现 `data-` 特性的方法在示例应用中由 `ClassicMovie2` 特性使用。</span><span class="sxs-lookup"><span data-stu-id="71c53-304">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="71c53-305">若要使用此方法添加客户端验证：</span><span class="sxs-lookup"><span data-stu-id="71c53-305">To add client validation by using this method:</span></span>

* <span data-ttu-id="71c53-306">在自定义验证特性中，实现 `IClientModelValidator` 接口并创建 `AddValidation` 方法。</span><span class="sxs-lookup"><span data-stu-id="71c53-306">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="71c53-307">使用 `AddValidation` 方法，添加 `data-` 特性进行验证，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-307">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="71c53-308">禁用客户端验证</span><span class="sxs-lookup"><span data-stu-id="71c53-308">Disable client-side validation</span></span>

<span data-ttu-id="71c53-309">以下代码禁用 MVC 视图中的客户端验证：</span><span class="sxs-lookup"><span data-stu-id="71c53-309">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="71c53-310">在 Razor Pages 中：</span><span class="sxs-lookup"><span data-stu-id="71c53-310">And in Razor Pages:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="71c53-311">禁用客户端验证的另一方式是在 .cshtml 文件中为 `_ValidationScriptsPartial` 的引用添加注释  。</span><span class="sxs-lookup"><span data-stu-id="71c53-311">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71c53-312">其他资源</span><span class="sxs-lookup"><span data-stu-id="71c53-312">Additional resources</span></span>

* [<span data-ttu-id="71c53-313">System.ComponentModel.DataAnnotations 命名空间</span><span class="sxs-lookup"><span data-stu-id="71c53-313">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="71c53-314">模型绑定</span><span class="sxs-lookup"><span data-stu-id="71c53-314">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="71c53-315">本文介绍如何在 ASP.NET Core MVC 或 Razor Pages 应用中验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="71c53-315">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="71c53-316">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="71c53-316">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="71c53-317">模型状态</span><span class="sxs-lookup"><span data-stu-id="71c53-317">Model state</span></span>

<span data-ttu-id="71c53-318">模型状态表示两个子系统的错误：模型绑定和模型验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-318">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="71c53-319">[模型绑定](model-binding.md)的错误通常是数据转换错误（例如，在要求为整数的字段中输入“x”）。</span><span class="sxs-lookup"><span data-stu-id="71c53-319">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="71c53-320">模型验证在模型绑定之后进行，并在数据不符合业务规则时报告错误（例如，在要求评级为 1 至 5 之间的字段中输入 0）。</span><span class="sxs-lookup"><span data-stu-id="71c53-320">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="71c53-321">模型绑定和验证都在执行控制器操作或 Razor Pages 处理程序方法之前进行。</span><span class="sxs-lookup"><span data-stu-id="71c53-321">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="71c53-322">Web 应用负责检查 `ModelState.IsValid` 并做出相应响应。</span><span class="sxs-lookup"><span data-stu-id="71c53-322">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="71c53-323">Web 应用通常会重新显示带有错误消息的页面：</span><span class="sxs-lookup"><span data-stu-id="71c53-323">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/sample_snapshot/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="71c53-324">如果 Web API 控制器具有 `[ApiController]` 特性，则它们不必检查 `ModelState.IsValid`。</span><span class="sxs-lookup"><span data-stu-id="71c53-324">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="71c53-325">在此情况下，如果模型状态无效，将返回包含问题详细信息的自动 HTTP 400 响应。</span><span class="sxs-lookup"><span data-stu-id="71c53-325">In that case, an automatic HTTP 400 response containing issue details is returned when model state is invalid.</span></span> <span data-ttu-id="71c53-326">有关详细信息，请参阅[自动 HTTP 400 响应](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="71c53-326">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="71c53-327">重新运行验证</span><span class="sxs-lookup"><span data-stu-id="71c53-327">Rerun validation</span></span>

<span data-ttu-id="71c53-328">验证自动进行，但是可能需要手动进行重复验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-328">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="71c53-329">例如，你可能为属性计算一个值，并且希望将属性设置为所计算的值后，再重新运行验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-329">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="71c53-330">若要重新运行验证，请调用 `TryValidateModel` 方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-330">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/sample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="71c53-331">验证特性</span><span class="sxs-lookup"><span data-stu-id="71c53-331">Validation attributes</span></span>

<span data-ttu-id="71c53-332">通过验证特性可以为模型属性指定验证规则。</span><span class="sxs-lookup"><span data-stu-id="71c53-332">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="71c53-333">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample)的以下示例显示使用验证特性进行注释的模型类。</span><span class="sxs-lookup"><span data-stu-id="71c53-333">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="71c53-334">`[ClassicMovie]` 特性为自定义的验证特性，其他特性为内置的验证特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-334">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="71c53-335">（`[ClassicMovie2]` 未显示，它表示实现自定义特性的另一种方法。）</span><span class="sxs-lookup"><span data-stu-id="71c53-335">(Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.)</span></span>

[!code-csharp[](validation/sample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="71c53-336">内置特性</span><span class="sxs-lookup"><span data-stu-id="71c53-336">Built-in attributes</span></span>

<span data-ttu-id="71c53-337">以下是一些内置验证特性：</span><span class="sxs-lookup"><span data-stu-id="71c53-337">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="71c53-338">`[CreditCard]`：验证属性是否有信用卡格式。</span><span class="sxs-lookup"><span data-stu-id="71c53-338">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="71c53-339">`[Compare]`：验证模型中的两个属性是否匹配。</span><span class="sxs-lookup"><span data-stu-id="71c53-339">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="71c53-340">`[EmailAddress]`：验证属性是否有电子邮件格式。</span><span class="sxs-lookup"><span data-stu-id="71c53-340">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="71c53-341">`[Phone]`：验证属性是否有电话号码格式。</span><span class="sxs-lookup"><span data-stu-id="71c53-341">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="71c53-342">`[Range]`：验证属性值是否在指定范围内。</span><span class="sxs-lookup"><span data-stu-id="71c53-342">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="71c53-343">`[RegularExpression]`：验证属性值是否与指定的正则表达式匹配。</span><span class="sxs-lookup"><span data-stu-id="71c53-343">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="71c53-344">`[Required]`：验证字段是否非 NULL。</span><span class="sxs-lookup"><span data-stu-id="71c53-344">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="71c53-345">请参阅 [[必需] 特性](#required-attribute)，获取关于该特性的行为的详细信息。</span><span class="sxs-lookup"><span data-stu-id="71c53-345">See [[Required] attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="71c53-346">`[StringLength]`：验证字符串属性值是否未超过指定长度限制。</span><span class="sxs-lookup"><span data-stu-id="71c53-346">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="71c53-347">`[Url]`：验证属性是否有 URL 格式。</span><span class="sxs-lookup"><span data-stu-id="71c53-347">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="71c53-348">`[Remote]`：通过调用服务器上的操作方法，验证客户端上的输入。</span><span class="sxs-lookup"><span data-stu-id="71c53-348">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="71c53-349">请参阅 [[远程] 特性](#remote-attribute)获取关于该特性的行为的详细信息。</span><span class="sxs-lookup"><span data-stu-id="71c53-349">See [[Remote] attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="71c53-350">在 [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 命名空间中可找到验证特性的完整列表。</span><span class="sxs-lookup"><span data-stu-id="71c53-350">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="71c53-351">错误消息</span><span class="sxs-lookup"><span data-stu-id="71c53-351">Error messages</span></span>

<span data-ttu-id="71c53-352">通过验证特性可以指定要为无效输入显示的错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-352">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="71c53-353">例如:</span><span class="sxs-lookup"><span data-stu-id="71c53-353">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="71c53-354">在内部，特性使用用于字段名的某个占位符调用 `String.Format`，有时还使用额外占位符。</span><span class="sxs-lookup"><span data-stu-id="71c53-354">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="71c53-355">例如:</span><span class="sxs-lookup"><span data-stu-id="71c53-355">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="71c53-356">应用于 `Name` 属性时，上述代码创建的错误消息将为“名称长度必须介于 6 到 8 之间”。</span><span class="sxs-lookup"><span data-stu-id="71c53-356">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="71c53-357">若要查找为特定特性的错误消息而传递给 `String.Format` 的参数，请参阅 [DataAnnotations 源代码](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="71c53-357">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="71c53-358">[必需] 特性</span><span class="sxs-lookup"><span data-stu-id="71c53-358">[Required] attribute</span></span>

<span data-ttu-id="71c53-359">默认情况下，验证系统将不可为 null 的参数或属性视为具有 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-359">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="71c53-360">`decimal` 和 `int` 等[值类型](/dotnet/csharp/language-reference/keywords/value-types)是不可为 null 的类型。</span><span class="sxs-lookup"><span data-stu-id="71c53-360">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="71c53-361">[必需] 服务器上的验证</span><span class="sxs-lookup"><span data-stu-id="71c53-361">[Required] validation on the server</span></span>

<span data-ttu-id="71c53-362">在服务器上，如果属性为 null，则认为所需值缺失。</span><span class="sxs-lookup"><span data-stu-id="71c53-362">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="71c53-363">不可为 null 的字段始终有效，并且从不显示 [必需] 特性的错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-363">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="71c53-364">但是，不可为 null 的属性的模型绑定可能会失败，从而导致 `The value '' is invalid` 等错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-364">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="71c53-365">若要为不可为 null 的类型的服务器端验证指定自定义错误消息，可使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="71c53-365">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="71c53-366">将字段设置为可以为 null（例如，`decimal?`而不是 `decimal`）。</span><span class="sxs-lookup"><span data-stu-id="71c53-366">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="71c53-367">类似于标准的可以为 null 的类型来处理[可以为 null\<T>](/dotnet/csharp/programming-guide/nullable-types/) 值类型。</span><span class="sxs-lookup"><span data-stu-id="71c53-367">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="71c53-368">指定模型绑定要使用的默认错误消息，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-368">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="71c53-369">有关模型绑定错误（可以为其设置默认消息）的更多信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>。</span><span class="sxs-lookup"><span data-stu-id="71c53-369">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="71c53-370">[必需] 客户端上的验证</span><span class="sxs-lookup"><span data-stu-id="71c53-370">[Required] validation on the client</span></span>

<span data-ttu-id="71c53-371">在客户端上处理不可为 null 类型和字符串的方式与在服务器上不同。</span><span class="sxs-lookup"><span data-stu-id="71c53-371">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="71c53-372">在客户端上：</span><span class="sxs-lookup"><span data-stu-id="71c53-372">On the client:</span></span>

* <span data-ttu-id="71c53-373">只有在为值输入一个输入时，才认为该值存在。</span><span class="sxs-lookup"><span data-stu-id="71c53-373">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="71c53-374">因此，客户端验证处理不可为 null 类型的方式与处理可以为 null 类型的方式相同。</span><span class="sxs-lookup"><span data-stu-id="71c53-374">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="71c53-375">jQuery 验证[必需](https://jqueryvalidation.org/required-method/)方法将字符串字段中的空格视为有效输入。</span><span class="sxs-lookup"><span data-stu-id="71c53-375">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="71c53-376">如果只输入空格，服务器端验证会将必需的字符串字段视为无效。</span><span class="sxs-lookup"><span data-stu-id="71c53-376">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="71c53-377">如前所述，将不可为 null 类型视为具有 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-377">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="71c53-378">这意味着即使不应用 `[Required]` 特性，也可进行客户端验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-378">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="71c53-379">但如果不使用该特性，将收到默认错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-379">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="71c53-380">若要指定自定义错误消息，使用该特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-380">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="71c53-381">[远程] 特性</span><span class="sxs-lookup"><span data-stu-id="71c53-381">[Remote] attribute</span></span>

<span data-ttu-id="71c53-382">`[Remote]` 特性实现客户端验证，该验证需要在服务器上调用方法，以确定字段输入是否有效。</span><span class="sxs-lookup"><span data-stu-id="71c53-382">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="71c53-383">例如，应用可能需要验证用户名是否已在使用。</span><span class="sxs-lookup"><span data-stu-id="71c53-383">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="71c53-384">若要实现远程验证：</span><span class="sxs-lookup"><span data-stu-id="71c53-384">To implement remote validation:</span></span>

1. <span data-ttu-id="71c53-385">创建可供 JavaScript 调用的操作方法。</span><span class="sxs-lookup"><span data-stu-id="71c53-385">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="71c53-386">jQuery Validate [远程](https://jqueryvalidation.org/remote-method/)方法要求 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="71c53-386">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="71c53-387">`"true"` 表示输入数据有效。</span><span class="sxs-lookup"><span data-stu-id="71c53-387">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="71c53-388">`"false"`、`undefined` 或 `null` 表示输入无效。</span><span class="sxs-lookup"><span data-stu-id="71c53-388">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="71c53-389">显示默认错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-389">Display the default error message.</span></span>
   * <span data-ttu-id="71c53-390">任何其他字符串都表示输入无效。</span><span class="sxs-lookup"><span data-stu-id="71c53-390">Any other string means the input is invalid.</span></span> <span data-ttu-id="71c53-391">将字符串显示为自定义错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-391">Display the string as a custom error message.</span></span>

   <span data-ttu-id="71c53-392">以下是返回自定义错误消息的操作方法示例：</span><span class="sxs-lookup"><span data-stu-id="71c53-392">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="71c53-393">在模型类中，使用指向验证操作方法的 `[Remote]` 特性注释属性，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-393">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="71c53-394">`[Remote]` 特性位于 `Microsoft.AspNetCore.Mvc` 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="71c53-394">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="71c53-395">如果未使用 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 元包，则请安装 [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="71c53-395">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="71c53-396">其他字段</span><span class="sxs-lookup"><span data-stu-id="71c53-396">Additional fields</span></span>

<span data-ttu-id="71c53-397">通过 `[Remote]` 特性的 `AdditionalFields` 属性可以根据服务器上的数据验证字段组合。</span><span class="sxs-lookup"><span data-stu-id="71c53-397">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="71c53-398">例如，如果 `User` 模型具有 `FirstName` 和 `LastName` 属性，可能需要验证该名称对尚未被现有用户占用。</span><span class="sxs-lookup"><span data-stu-id="71c53-398">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="71c53-399">下面的示例演示如何使用 `AdditionalFields`：</span><span class="sxs-lookup"><span data-stu-id="71c53-399">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/sample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="71c53-400">`AdditionalFields` 可能已显式设置为字符串 `"FirstName"` 和 `"LastName"`，但使用 [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) 操作符可简化稍后的重构过程。</span><span class="sxs-lookup"><span data-stu-id="71c53-400">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [`nameof`](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="71c53-401">此验证的操作方法必须接受 first name 和 last name 参数：</span><span class="sxs-lookup"><span data-stu-id="71c53-401">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="71c53-402">用户输入名字或姓氏时，JavaScript 会进行远程调用，查看该名称对是否已占用。</span><span class="sxs-lookup"><span data-stu-id="71c53-402">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="71c53-403">若要验证两个或更多附加字段，可将其以逗号分隔列表形式提供。</span><span class="sxs-lookup"><span data-stu-id="71c53-403">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="71c53-404">例如，若要向模型中添加 `MiddleName` 属性，可按以下示例所示设置 `[Remote]` 特性：</span><span class="sxs-lookup"><span data-stu-id="71c53-404">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="71c53-405">`AdditionalFields` 与所有属性参数一样，必须是常量表达式。</span><span class="sxs-lookup"><span data-stu-id="71c53-405">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="71c53-406">因此，请勿使用[内插字符串](/dotnet/csharp/language-reference/keywords/interpolated-strings)或调用 <xref:System.String.Join*> 来初始化 `AdditionalFields`。</span><span class="sxs-lookup"><span data-stu-id="71c53-406">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="71c53-407">内置特性的替代特性</span><span class="sxs-lookup"><span data-stu-id="71c53-407">Alternatives to built-in attributes</span></span>

<span data-ttu-id="71c53-408">如果需要并非由内置属性提供的验证，可以：</span><span class="sxs-lookup"><span data-stu-id="71c53-408">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="71c53-409">[创建自定义特性](#custom-attributes)。</span><span class="sxs-lookup"><span data-stu-id="71c53-409">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="71c53-410">[实现 IValidatableObject](#ivalidatableobject)。</span><span class="sxs-lookup"><span data-stu-id="71c53-410">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="71c53-411">自定义特性</span><span class="sxs-lookup"><span data-stu-id="71c53-411">Custom attributes</span></span>

<span data-ttu-id="71c53-412">对于内置验证特性无法处理的情况，可以创建自定义验证特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-412">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="71c53-413">创建继承自 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> 的类，并替代 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> 方法。</span><span class="sxs-lookup"><span data-stu-id="71c53-413">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="71c53-414">`IsValid` 方法接受名为 value 的对象，该对象是要进行验证的输入  。</span><span class="sxs-lookup"><span data-stu-id="71c53-414">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="71c53-415">重载还接受 `ValidationContext` 对象，该对象提供其他信息，例如模型绑定创建的模型实例。</span><span class="sxs-lookup"><span data-stu-id="71c53-415">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="71c53-416">以下示例验证“经典”流派电影的发行日期是否不晚于指定年份  。</span><span class="sxs-lookup"><span data-stu-id="71c53-416">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="71c53-417">`[ClassicMovie2]` 特性首先检查流派，只有流派为“经典”时才会继续检查  。</span><span class="sxs-lookup"><span data-stu-id="71c53-417">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="71c53-418">如果电影流派为经典，它会检查发行日期，确保该日期不晚于传递给特性构造函数的限值。）</span><span class="sxs-lookup"><span data-stu-id="71c53-418">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/sample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="71c53-419">上述示例中的 `movie` 变量表示 `Movie` 对象，其中包含表单提交中的数据。</span><span class="sxs-lookup"><span data-stu-id="71c53-419">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="71c53-420">`IsValid` 方法检查日期和流派。</span><span class="sxs-lookup"><span data-stu-id="71c53-420">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="71c53-421">验证成功时，`IsValid` 返回 `ValidationResult.Success` 代码。</span><span class="sxs-lookup"><span data-stu-id="71c53-421">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="71c53-422">验证失败时，返回 `ValidationResult` 和错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-422">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="71c53-423">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="71c53-423">IValidatableObject</span></span>

<span data-ttu-id="71c53-424">上述示例只适用于 `Movie` 类型。</span><span class="sxs-lookup"><span data-stu-id="71c53-424">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="71c53-425">类级别验证的另一方式是在模型类中实现 `IValidatableObject`，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-425">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/sample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="71c53-426">顶级节点验证</span><span class="sxs-lookup"><span data-stu-id="71c53-426">Top-level node validation</span></span>

<span data-ttu-id="71c53-427">顶级节点包括：</span><span class="sxs-lookup"><span data-stu-id="71c53-427">Top-level nodes include:</span></span>

* <span data-ttu-id="71c53-428">操作参数</span><span class="sxs-lookup"><span data-stu-id="71c53-428">Action parameters</span></span>
* <span data-ttu-id="71c53-429">控制器属性</span><span class="sxs-lookup"><span data-stu-id="71c53-429">Controller properties</span></span>
* <span data-ttu-id="71c53-430">页处理程序参数</span><span class="sxs-lookup"><span data-stu-id="71c53-430">Page handler parameters</span></span>
* <span data-ttu-id="71c53-431">页模型属性</span><span class="sxs-lookup"><span data-stu-id="71c53-431">Page model properties</span></span>

<span data-ttu-id="71c53-432">除了验证模型属性之外，还验证了模型绑定的顶级节点。</span><span class="sxs-lookup"><span data-stu-id="71c53-432">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="71c53-433">在示例应用的以下示例中，`VerifyPhone` 方法使用 <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> 验证 `phone` 操作参数：</span><span class="sxs-lookup"><span data-stu-id="71c53-433">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="71c53-434">顶级节点可以将 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> 与验证属性结合使用。</span><span class="sxs-lookup"><span data-stu-id="71c53-434">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="71c53-435">在示例应用的以下示例中，`CheckAge` 方法指定在提交表单时必须从查询字符串绑定 `age` 参数：</span><span class="sxs-lookup"><span data-stu-id="71c53-435">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/sample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="71c53-436">在“检查年限”页 (CheckAge.cshtml) 中，有两个表单  。</span><span class="sxs-lookup"><span data-stu-id="71c53-436">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="71c53-437">第一个表单提交 `Age` 的值 `99` 作为查询字符串：`https://localhost:5001/Users/CheckAge?Age=99`。</span><span class="sxs-lookup"><span data-stu-id="71c53-437">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="71c53-438">当提交查询字符串中格式设置正确的 `age` 参数时，表单将进行验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-438">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="71c53-439">“检查年限”页面上的第二个表单提交请求正文中的 `Age` 值，验证失败。</span><span class="sxs-lookup"><span data-stu-id="71c53-439">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="71c53-440">绑定失败，因为 `age` 参数必须来自查询字符串。</span><span class="sxs-lookup"><span data-stu-id="71c53-440">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="71c53-441">在 `CompatibilityVersion.Version_2_1` 或更高版本上运行时，默认启用顶级节点验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-441">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="71c53-442">否则，禁用顶级节点验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-442">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="71c53-443">通过在 (`Startup.ConfigureServices`) 中设置 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> 属性，可以替代默认选项，如下所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-443">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="71c53-444">最大错误数</span><span class="sxs-lookup"><span data-stu-id="71c53-444">Maximum errors</span></span>

<span data-ttu-id="71c53-445">达到最大错误数（默认为 200）时，验证停止。</span><span class="sxs-lookup"><span data-stu-id="71c53-445">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="71c53-446">可以使用 `Startup.ConfigureServices` 中的以下代码配置该数字：</span><span class="sxs-lookup"><span data-stu-id="71c53-446">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="71c53-447">最大递归次数</span><span class="sxs-lookup"><span data-stu-id="71c53-447">Maximum recursion</span></span>

<span data-ttu-id="71c53-448"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> 遍历所验证模型的对象图。</span><span class="sxs-lookup"><span data-stu-id="71c53-448"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="71c53-449">如果模型非常深或无限递归，验证可能导致堆栈溢出。</span><span class="sxs-lookup"><span data-stu-id="71c53-449">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="71c53-450">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) 提供了在访问者递归超过配置深度时提前停止验证的方法。</span><span class="sxs-lookup"><span data-stu-id="71c53-450">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="71c53-451">在 `CompatibilityVersion.Version_2_2` 或更高版本上运行时，`MvcOptions.MaxValidationDepth` 的默认值为 200。</span><span class="sxs-lookup"><span data-stu-id="71c53-451">The default value of `MvcOptions.MaxValidationDepth` is 200 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="71c53-452">对于更低版本，该值为 null，这表示没有深度约束。</span><span class="sxs-lookup"><span data-stu-id="71c53-452">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="71c53-453">自动短路</span><span class="sxs-lookup"><span data-stu-id="71c53-453">Automatic short-circuit</span></span>

<span data-ttu-id="71c53-454">如果模型图不需要验证，验证将自动短路（跳过）。</span><span class="sxs-lookup"><span data-stu-id="71c53-454">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="71c53-455">运行时为其跳过验证的对象包括基元集合（如 `byte[]`、`string[]`、`Dictionary<string, string>`）和不具有任何验证器的复杂对象图。</span><span class="sxs-lookup"><span data-stu-id="71c53-455">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="71c53-456">禁用验证</span><span class="sxs-lookup"><span data-stu-id="71c53-456">Disable validation</span></span>

<span data-ttu-id="71c53-457">若要禁用验证：</span><span class="sxs-lookup"><span data-stu-id="71c53-457">To disable validation:</span></span>

1. <span data-ttu-id="71c53-458">创建不会将任何字段标记为无效的 `IObjectModelValidator` 实现。</span><span class="sxs-lookup"><span data-stu-id="71c53-458">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/sample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="71c53-459">将以下代码添加到 `Startup.ConfigureServices`，以便替换依赖项注入容器中的默认 `IObjectModelValidator` 实现。</span><span class="sxs-lookup"><span data-stu-id="71c53-459">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="71c53-460">你可能仍然会看到模型绑定的模型状态错误。</span><span class="sxs-lookup"><span data-stu-id="71c53-460">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="71c53-461">客户端验证</span><span class="sxs-lookup"><span data-stu-id="71c53-461">Client-side validation</span></span>

<span data-ttu-id="71c53-462">客户端验证将阻止提交，直到表单变为有效为止。</span><span class="sxs-lookup"><span data-stu-id="71c53-462">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="71c53-463">“提交”按钮运行 JavaScript：要么提交表单要么显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-463">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="71c53-464">表单上存在输入错误时，客户端验证会避免到服务器的不必要往返。</span><span class="sxs-lookup"><span data-stu-id="71c53-464">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="71c53-465">_Layout.cshtml 和 _ValidationScriptsPartial.cshtml 中的以下脚本引用支持客户端验证   ：</span><span class="sxs-lookup"><span data-stu-id="71c53-465">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/sample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/sample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="71c53-466">[jQuery 非介入式验证](https://github.com/aspnet/jquery-validation-unobtrusive)脚本是一个基于热门 [jQuery Validate](https://jqueryvalidation.org/) 插件的自定义 Microsoft 前端库。</span><span class="sxs-lookup"><span data-stu-id="71c53-466">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="71c53-467">如果没有 jQuery 非介入式验证，则必须在两个位置编码相同的验证逻辑：一次是在模型属性上的服务器端验证特性中，一次是在客户端脚本中。</span><span class="sxs-lookup"><span data-stu-id="71c53-467">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="71c53-468">[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 [HTML 帮助程序](xref:mvc/views/overview)则使用模型属性中的验证特性和类型元数据，呈现需要验证的表单元素的 HTML 5 `data-` 特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-468">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="71c53-469">jQuery 非介入式验证分析 `data-` 特性并将逻辑传递给 jQuery Validate，从而将服务器端验证逻辑有效地“复制”到客户端。</span><span class="sxs-lookup"><span data-stu-id="71c53-469">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="71c53-470">可以使用标记帮助程序在客户端上显示验证错误，如下所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-470">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/sample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="71c53-471">上述标记帮助程序呈现以下 HTML。</span><span class="sxs-lookup"><span data-stu-id="71c53-471">The preceding tag helpers render the following HTML.</span></span>

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
                id="ReleaseDate" name="ReleaseDate" value="">
                <span class="text-danger field-validation-valid"
                data-valmsg-for="ReleaseDate" data-valmsg-replace="true"></span>
            </div>
        </div>
    </div>
</form>
```

<span data-ttu-id="71c53-472">请注意，HTML 输出中的 `data-` 特性与 `ReleaseDate` 属性的验证特性相对应。</span><span class="sxs-lookup"><span data-stu-id="71c53-472">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="71c53-473">`data-val-required` 特性包含在用户未填写上映日期字段时将显示的错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-473">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="71c53-474">jQuery 非介入式验证将此值传递给 jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) 方法，该方法随后在随附的 **\<span>** 元素中显示该消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-474">jQuery Unobtrusive Validation passes this value to the jQuery Validate [`required()`](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="71c53-475">如果 `[DataType]` 特性未替代属性的 .NET 类型，则数据类型验证基于该类型。</span><span class="sxs-lookup"><span data-stu-id="71c53-475">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="71c53-476">浏览器具有自己的默认错误消息，但是 jQuery 验证非介入式验证包可以替代这些消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-476">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="71c53-477">通过 `[DataType]` 特性和 `[EmailAddress]` 等子类可以指定错误消息。</span><span class="sxs-lookup"><span data-stu-id="71c53-477">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="71c53-478">向动态表单添加验证</span><span class="sxs-lookup"><span data-stu-id="71c53-478">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="71c53-479">jQuery 非介入式验证会在第一次加载页面时将验证逻辑和参数传递到 jQuery Validate。</span><span class="sxs-lookup"><span data-stu-id="71c53-479">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="71c53-480">因此，不会对动态生成的表单自动执行验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-480">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="71c53-481">若要启用验证，指示 jQuery 非介入式验证在创建动态表单后立即对其进行分析。</span><span class="sxs-lookup"><span data-stu-id="71c53-481">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="71c53-482">例如，以下代码在通过 AJAX 添加的表单上设置客户端验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-482">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="71c53-483">`$.validator.unobtrusive.parse()` 方法采用 jQuery 选择器作为它的一个参数。</span><span class="sxs-lookup"><span data-stu-id="71c53-483">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="71c53-484">此方法指示 jQuery 非介入式验证分析该选择器内表单的 `data-` 属性。</span><span class="sxs-lookup"><span data-stu-id="71c53-484">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="71c53-485">然后将这些特性的值传递给 jQuery Validate 插件。</span><span class="sxs-lookup"><span data-stu-id="71c53-485">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="71c53-486">向动态控件添加验证</span><span class="sxs-lookup"><span data-stu-id="71c53-486">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="71c53-487">`$.validator.unobtrusive.parse()` 方法适用于整个表单，而不是 `<input>` 和 `<select/>` 等单个动态生成的控件。</span><span class="sxs-lookup"><span data-stu-id="71c53-487">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="71c53-488">若要重新分析表单，删除之前分析表单时添加的验证数据，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-488">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="71c53-489">自定义客户端验证</span><span class="sxs-lookup"><span data-stu-id="71c53-489">Custom client-side validation</span></span>

<span data-ttu-id="71c53-490">生成适用于自定义 jQuery Validate 适配器的 `data-` HTML 特性，完成自定义客户端验证。</span><span class="sxs-lookup"><span data-stu-id="71c53-490">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="71c53-491">以下示例适配器代码是为本文前面部分介绍的 `ClassicMovie` 和 `ClassicMovie2` 特性编写的：</span><span class="sxs-lookup"><span data-stu-id="71c53-491">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/sample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="71c53-492">有关如何编写适配器的信息，请参阅 [jQuery Validate 文档](https://jqueryvalidation.org/documentation/)。</span><span class="sxs-lookup"><span data-stu-id="71c53-492">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="71c53-493">给定字段的适配器的使用由 `data-` 特性触发，这些特性：</span><span class="sxs-lookup"><span data-stu-id="71c53-493">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="71c53-494">将字段标记为正在验证 (`data-val="true"`)。</span><span class="sxs-lookup"><span data-stu-id="71c53-494">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="71c53-495">确定验证规则名称和错误消息文本（例如，`data-val-rulename="Error message."`）。</span><span class="sxs-lookup"><span data-stu-id="71c53-495">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="71c53-496">提供验证器所需的任何其他参数（例如，`data-val-rulename-parm1="value"`）。</span><span class="sxs-lookup"><span data-stu-id="71c53-496">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="71c53-497">以下示例显示示例应用的 `ClassicMovie` 特性的 `data-` 特性：</span><span class="sxs-lookup"><span data-stu-id="71c53-497">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="71c53-498">如前所述，[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 [HTML 帮助程序](xref:mvc/views/overview)使用验证特性的信息呈现 `data-` 特性。</span><span class="sxs-lookup"><span data-stu-id="71c53-498">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="71c53-499">编写用于创建自定义 `data-` HTML 特性的代码有以下两种方式：</span><span class="sxs-lookup"><span data-stu-id="71c53-499">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="71c53-500">创建派生自 `AttributeAdapterBase<TAttribute>` 的类和实现 `IValidationAttributeAdapterProvider` 的类，并在 DI 中注册特性及其适配器。</span><span class="sxs-lookup"><span data-stu-id="71c53-500">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="71c53-501">此方法遵循[单一责任主体](https://wikipedia.org/wiki/Single_responsibility_principle)，这体现在与服务器相关的验证代码和与客户端相关的验证代码位于不同的类中。</span><span class="sxs-lookup"><span data-stu-id="71c53-501">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="71c53-502">适配器还有以下优势：由于适配器是在 DI 中注册的，所以如果需要，适配器可以使用 DI 中的其他服务。</span><span class="sxs-lookup"><span data-stu-id="71c53-502">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="71c53-503">在 `ValidationAttribute` 类中实现 `IClientModelValidator`。</span><span class="sxs-lookup"><span data-stu-id="71c53-503">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="71c53-504">如果特性既不进行任何服务器端验证，也不需要 DI 的任何服务，则此方法可能适用。</span><span class="sxs-lookup"><span data-stu-id="71c53-504">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="71c53-505">用于客户端验证的 AttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="71c53-505">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="71c53-506">在 HTML 中呈现 `data-` 特性的方法在示例应用中由 `ClassicMovie` 特性使用。</span><span class="sxs-lookup"><span data-stu-id="71c53-506">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="71c53-507">若要使用此方法添加客户端验证：</span><span class="sxs-lookup"><span data-stu-id="71c53-507">To add client validation by using this method:</span></span>

1. <span data-ttu-id="71c53-508">为自定义验证特性创建特性适配器类。</span><span class="sxs-lookup"><span data-stu-id="71c53-508">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="71c53-509">从 [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2) 派生类。</span><span class="sxs-lookup"><span data-stu-id="71c53-509">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="71c53-510">创建将 `data-` 特性添加到所呈现输出中的 `AddValidation` 方法，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-510">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="71c53-511">创建实现 <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> 的适配器提供程序类。</span><span class="sxs-lookup"><span data-stu-id="71c53-511">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="71c53-512">使用 `GetAttributeAdapter` 方法，将自定义属性传递给适配器的构造函数，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-512">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/sample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="71c53-513">在 `Startup.ConfigureServices` 中为 DI 注册适配器提供程序：</span><span class="sxs-lookup"><span data-stu-id="71c53-513">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/sample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="71c53-514">用于客户端验证的 IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="71c53-514">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="71c53-515">在 HTML 中呈现 `data-` 特性的方法在示例应用中由 `ClassicMovie2` 特性使用。</span><span class="sxs-lookup"><span data-stu-id="71c53-515">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="71c53-516">若要使用此方法添加客户端验证：</span><span class="sxs-lookup"><span data-stu-id="71c53-516">To add client validation by using this method:</span></span>

* <span data-ttu-id="71c53-517">在自定义验证特性中，实现 `IClientModelValidator` 接口并创建 `AddValidation` 方法。</span><span class="sxs-lookup"><span data-stu-id="71c53-517">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="71c53-518">使用 `AddValidation` 方法，添加 `data-` 特性进行验证，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="71c53-518">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/sample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="71c53-519">禁用客户端验证</span><span class="sxs-lookup"><span data-stu-id="71c53-519">Disable client-side validation</span></span>

<span data-ttu-id="71c53-520">以下代码禁用 MVC 视图中的客户端验证：</span><span class="sxs-lookup"><span data-stu-id="71c53-520">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="71c53-521">在 Razor Pages 中：</span><span class="sxs-lookup"><span data-stu-id="71c53-521">And in Razor Pages:</span></span>

[!code-csharp[](validation/sample_snapshot/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="71c53-522">禁用客户端验证的另一方式是在 .cshtml 文件中为 `_ValidationScriptsPartial` 的引用添加注释  。</span><span class="sxs-lookup"><span data-stu-id="71c53-522">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="71c53-523">其他资源</span><span class="sxs-lookup"><span data-stu-id="71c53-523">Additional resources</span></span>

* [<span data-ttu-id="71c53-524">System.ComponentModel.DataAnnotations 命名空间</span><span class="sxs-lookup"><span data-stu-id="71c53-524">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="71c53-525">模型绑定</span><span class="sxs-lookup"><span data-stu-id="71c53-525">Model Binding</span></span>](model-binding.md)

::: moniker-end
