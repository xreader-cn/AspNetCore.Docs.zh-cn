---
title: ASP.NET Core MVC 中的模型验证
author: rick-anderson
description: 了解 ASP.NET Core MVC 和 Razor Pages 中的模型验证。
ms.author: riande
ms.custom: mvc
ms.date: 12/15/2019
uid: mvc/models/validation
ms.openlocfilehash: 042a9933e561de4957f6332bdff3c4f09d2e119b
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75355269"
---
# <a name="model-validation-in-aspnet-core-mvc-and-razor-pages"></a><span data-ttu-id="11db3-103">ASP.NET Core MVC 和 Razor Pages 中的模型验证</span><span class="sxs-lookup"><span data-stu-id="11db3-103">Model validation in ASP.NET Core MVC and Razor Pages</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="11db3-104">作者：[Kirk Larkin](https://github.com/serpent5)</span><span class="sxs-lookup"><span data-stu-id="11db3-104">By [Kirk Larkin](https://github.com/serpent5)</span></span>

<span data-ttu-id="11db3-105">本文介绍如何在 ASP.NET Core MVC 或 Razor Pages 应用中验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="11db3-105">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="11db3-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="11db3-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="11db3-107">模型状态</span><span class="sxs-lookup"><span data-stu-id="11db3-107">Model state</span></span>

<span data-ttu-id="11db3-108">模型状态表示两个子系统的错误：模型绑定和模型验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-108">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="11db3-109">源自[模型绑定](model-binding.md)的错误通常是数据转换错误。</span><span class="sxs-lookup"><span data-stu-id="11db3-109">Errors that originate from [model binding](model-binding.md) are generally data conversion errors.</span></span> <span data-ttu-id="11db3-110">例如，在一个整数字段中输入一个“x”。</span><span class="sxs-lookup"><span data-stu-id="11db3-110">For example, an "x" is entered in an integer field.</span></span> <span data-ttu-id="11db3-111">模型验证在模型绑定后发生，并报告数据不符合业务规则的错误。</span><span class="sxs-lookup"><span data-stu-id="11db3-111">Model validation occurs after model binding and reports errors where data doesn't conform to business rules.</span></span> <span data-ttu-id="11db3-112">例如，在需要 1 到 5 之间评分的字段中输入 0。</span><span class="sxs-lookup"><span data-stu-id="11db3-112">For example, a 0 is entered in a field that expects a rating between 1 and 5.</span></span>

<span data-ttu-id="11db3-113">模型绑定和模型验证都在执行控制器操作或 Razor Pages 处理程序方法之前进行。</span><span class="sxs-lookup"><span data-stu-id="11db3-113">Both model binding and model validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="11db3-114">Web 应用负责检查 `ModelState.IsValid` 并做出相应响应。</span><span class="sxs-lookup"><span data-stu-id="11db3-114">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="11db3-115">Web 应用通常会重新显示带有错误消息的页面：</span><span class="sxs-lookup"><span data-stu-id="11db3-115">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_OnPostAsync&highlight=3-6)]

<span data-ttu-id="11db3-116">如果 Web API 控制器具有 `[ApiController]` 特性，则它们不必检查 `ModelState.IsValid`。</span><span class="sxs-lookup"><span data-stu-id="11db3-116">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="11db3-117">在此情况下，如果模型状态无效，将返回包含错误详细信息的自动 HTTP 400 响应。</span><span class="sxs-lookup"><span data-stu-id="11db3-117">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="11db3-118">有关详细信息，请参阅[自动 HTTP 400 响应](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="11db3-118">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="11db3-119">重新运行验证</span><span class="sxs-lookup"><span data-stu-id="11db3-119">Rerun validation</span></span>

<span data-ttu-id="11db3-120">验证自动进行，但是可能需要手动进行重复验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-120">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="11db3-121">例如，你可能为属性计算一个值，并且希望将属性设置为所计算的值后，再重新运行验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-121">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="11db3-122">若要重新运行验证，请调用 `TryValidateModel` 方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-122">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml.cs?name=snippet_TryValidate&highlight=3-6)]

## <a name="validation-attributes"></a><span data-ttu-id="11db3-123">验证特性</span><span class="sxs-lookup"><span data-stu-id="11db3-123">Validation attributes</span></span>

<span data-ttu-id="11db3-124">通过验证特性可以为模型属性指定验证规则。</span><span class="sxs-lookup"><span data-stu-id="11db3-124">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="11db3-125">示例应用的以下示例显示使用验证特性进行注释的模型类。</span><span class="sxs-lookup"><span data-stu-id="11db3-125">The following example from the sample app shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="11db3-126">`[ClassicMovie]` 特性为自定义的验证特性，其他特性为内置的验证特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-126">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="11db3-127">`[ClassicMovieWithClientValidator]` 未显示。</span><span class="sxs-lookup"><span data-stu-id="11db3-127">Not shown is `[ClassicMovieWithClientValidator]`.</span></span> <span data-ttu-id="11db3-128">`[ClassicMovieWithClientValidator]` 显示实现自定义特性的另一种方法。</span><span class="sxs-lookup"><span data-stu-id="11db3-128">`[ClassicMovieWithClientValidator]` shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/Movie.cs?name=snippet_Class)]

## <a name="built-in-attributes"></a><span data-ttu-id="11db3-129">内置特性</span><span class="sxs-lookup"><span data-stu-id="11db3-129">Built-in attributes</span></span>

<span data-ttu-id="11db3-130">以下是一些内置验证特性：</span><span class="sxs-lookup"><span data-stu-id="11db3-130">Here are some of the built-in validation attributes:</span></span>

* <span data-ttu-id="11db3-131">`[CreditCard]`：验证属性是否有信用卡格式。</span><span class="sxs-lookup"><span data-stu-id="11db3-131">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="11db3-132">`[Compare]`：验证模型中的两个属性是否匹配。</span><span class="sxs-lookup"><span data-stu-id="11db3-132">`[Compare]`: Validates that two properties in a model match.</span></span>
* <span data-ttu-id="11db3-133">`[EmailAddress]`：验证属性是否有电子邮件格式。</span><span class="sxs-lookup"><span data-stu-id="11db3-133">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="11db3-134">`[Phone]`：验证属性是否有电话号码格式。</span><span class="sxs-lookup"><span data-stu-id="11db3-134">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="11db3-135">`[Range]`：验证属性值是否在指定范围内。</span><span class="sxs-lookup"><span data-stu-id="11db3-135">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="11db3-136">`[RegularExpression]`：验证属性值是否与指定的正则表达式匹配。</span><span class="sxs-lookup"><span data-stu-id="11db3-136">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="11db3-137">`[Required]`：验证字段是否非 NULL。</span><span class="sxs-lookup"><span data-stu-id="11db3-137">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="11db3-138">请参阅 [`[Required]` 属性](#required-attribute)，获取关于该特性的行为的详细信息。</span><span class="sxs-lookup"><span data-stu-id="11db3-138">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="11db3-139">`[StringLength]`：验证字符串属性值是否未超过指定长度限制。</span><span class="sxs-lookup"><span data-stu-id="11db3-139">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="11db3-140">`[Url]`：验证属性是否有 URL 格式。</span><span class="sxs-lookup"><span data-stu-id="11db3-140">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="11db3-141">`[Remote]`：通过调用服务器上的操作方法，验证客户端上的输入。</span><span class="sxs-lookup"><span data-stu-id="11db3-141">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="11db3-142">请参阅 [`[Remote]` 属性](#remote-attribute)，获取关于该特性的行为的详细信息。</span><span class="sxs-lookup"><span data-stu-id="11db3-142">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="11db3-143">在 [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 命名空间中可找到验证特性的完整列表。</span><span class="sxs-lookup"><span data-stu-id="11db3-143">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="11db3-144">错误消息</span><span class="sxs-lookup"><span data-stu-id="11db3-144">Error messages</span></span>

<span data-ttu-id="11db3-145">通过验证特性可以指定要为无效输入显示的错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-145">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="11db3-146">例如：</span><span class="sxs-lookup"><span data-stu-id="11db3-146">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="11db3-147">在内部，特性使用用于字段名的某个占位符调用 `String.Format`，有时还使用额外占位符。</span><span class="sxs-lookup"><span data-stu-id="11db3-147">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="11db3-148">例如：</span><span class="sxs-lookup"><span data-stu-id="11db3-148">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="11db3-149">应用于 `Name` 属性时，上述代码创建的错误消息将为“名称长度必须介于 6 到 8 之间”。</span><span class="sxs-lookup"><span data-stu-id="11db3-149">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="11db3-150">若要查找为特定特性的错误消息而传递给 `String.Format` 的参数，请参阅 [DataAnnotations 源代码](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="11db3-150">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="11db3-151">[必需] 特性</span><span class="sxs-lookup"><span data-stu-id="11db3-151">[Required] attribute</span></span>

<span data-ttu-id="11db3-152">默认情况下，验证系统将不可为 null 的参数或属性视为具有 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-152">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="11db3-153">`decimal` 和 `int` 等[值类型](/dotnet/csharp/language-reference/keywords/value-types)是不可为 null 的类型。</span><span class="sxs-lookup"><span data-stu-id="11db3-153">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="11db3-154">[必需] 服务器上的验证</span><span class="sxs-lookup"><span data-stu-id="11db3-154">[Required] validation on the server</span></span>

<span data-ttu-id="11db3-155">在服务器上，如果属性为 null，则认为所需值缺失。</span><span class="sxs-lookup"><span data-stu-id="11db3-155">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="11db3-156">不可为 null 的字段始终有效，并且从不显示 `[Required]` 属性的错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-156">A non-nullable field is always valid, and the `[Required]` attribute's error message is never displayed.</span></span>

<span data-ttu-id="11db3-157">但是，不可为 null 的属性的模型绑定可能会失败，从而导致 `The value '' is invalid` 等错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-157">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="11db3-158">若要为不可为 null 的类型的服务器端验证指定自定义错误消息，可使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="11db3-158">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="11db3-159">将字段设置为可以为 null（例如，`decimal?`而不是 `decimal`）。</span><span class="sxs-lookup"><span data-stu-id="11db3-159">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="11db3-160">类似于标准的可以为 null 的类型来处理[可以为 null\<T>](/dotnet/csharp/programming-guide/nullable-types/) 值类型。</span><span class="sxs-lookup"><span data-stu-id="11db3-160">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="11db3-161">指定模型绑定要使用的默认错误消息，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-161">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=5-6)]

  <span data-ttu-id="11db3-162">有关模型绑定错误（可以为其设置默认消息）的更多信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>。</span><span class="sxs-lookup"><span data-stu-id="11db3-162">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="11db3-163">[必需] 客户端上的验证</span><span class="sxs-lookup"><span data-stu-id="11db3-163">[Required] validation on the client</span></span>

<span data-ttu-id="11db3-164">在客户端上处理不可为 null 类型和字符串的方式与在服务器上不同。</span><span class="sxs-lookup"><span data-stu-id="11db3-164">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="11db3-165">在客户端上：</span><span class="sxs-lookup"><span data-stu-id="11db3-165">On the client:</span></span>

* <span data-ttu-id="11db3-166">只有在为值输入一个输入时，才认为该值存在。</span><span class="sxs-lookup"><span data-stu-id="11db3-166">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="11db3-167">因此，客户端验证处理不可为 null 类型的方式与处理可以为 null 类型的方式相同。</span><span class="sxs-lookup"><span data-stu-id="11db3-167">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="11db3-168">jQuery 验证[必需](https://jqueryvalidation.org/required-method/)方法将字符串字段中的空格视为有效输入。</span><span class="sxs-lookup"><span data-stu-id="11db3-168">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="11db3-169">如果只输入空格，服务器端验证会将必需的字符串字段视为无效。</span><span class="sxs-lookup"><span data-stu-id="11db3-169">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="11db3-170">如前所述，将不可为 null 类型视为具有 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-170">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="11db3-171">这意味着即使不应用 `[Required]` 特性，也可进行客户端验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-171">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="11db3-172">但如果不使用该特性，将收到默认错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-172">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="11db3-173">若要指定自定义错误消息，使用该特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-173">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="11db3-174">[远程] 特性</span><span class="sxs-lookup"><span data-stu-id="11db3-174">[Remote] attribute</span></span>

<span data-ttu-id="11db3-175">`[Remote]` 特性实现客户端验证，该验证需要在服务器上调用方法，以确定字段输入是否有效。</span><span class="sxs-lookup"><span data-stu-id="11db3-175">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="11db3-176">例如，应用可能需要验证用户名是否已在使用。</span><span class="sxs-lookup"><span data-stu-id="11db3-176">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="11db3-177">若要实现远程验证：</span><span class="sxs-lookup"><span data-stu-id="11db3-177">To implement remote validation:</span></span>

1. <span data-ttu-id="11db3-178">创建可供 JavaScript 调用的操作方法。</span><span class="sxs-lookup"><span data-stu-id="11db3-178">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="11db3-179">jQuery Validate [远程](https://jqueryvalidation.org/remote-method/)方法要求 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="11db3-179">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="11db3-180">`true` 表示输入数据有效。</span><span class="sxs-lookup"><span data-stu-id="11db3-180">`true` means the input data is valid.</span></span>
   * <span data-ttu-id="11db3-181">`false`、`undefined` 或 `null` 表示输入无效。</span><span class="sxs-lookup"><span data-stu-id="11db3-181">`false`, `undefined`, or `null` means the input is invalid.</span></span> <span data-ttu-id="11db3-182">显示默认错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-182">Display the default error message.</span></span>
   * <span data-ttu-id="11db3-183">任何其他字符串都表示输入无效。</span><span class="sxs-lookup"><span data-stu-id="11db3-183">Any other string means the input is invalid.</span></span> <span data-ttu-id="11db3-184">将字符串显示为自定义错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-184">Display the string as a custom error message.</span></span>

   <span data-ttu-id="11db3-185">以下是返回自定义错误消息的操作方法示例：</span><span class="sxs-lookup"><span data-stu-id="11db3-185">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="11db3-186">在模型类中，使用指向验证操作方法的 `[Remote]` 特性注释属性，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-186">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Email)]
 
   <span data-ttu-id="11db3-187">`[Remote]` 特性位于 `Microsoft.AspNetCore.Mvc` 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="11db3-187">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="11db3-188">其他字段</span><span class="sxs-lookup"><span data-stu-id="11db3-188">Additional fields</span></span>

<span data-ttu-id="11db3-189">通过 `[Remote]` 特性的 `AdditionalFields` 属性可以根据服务器上的数据验证字段组合。</span><span class="sxs-lookup"><span data-stu-id="11db3-189">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="11db3-190">例如，如果 `User` 模型具有 `FirstName` 和 `LastName` 属性，可能需要验证该名称对尚未被现有用户占用。</span><span class="sxs-lookup"><span data-stu-id="11db3-190">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="11db3-191">下面的示例演示如何使用 `AdditionalFields`：</span><span class="sxs-lookup"><span data-stu-id="11db3-191">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/User.cs?name=snippet_Name&highlight=1,5)]

<span data-ttu-id="11db3-192">`AdditionalFields` 可以显式设置为字符串 "FirstName" 和 "LastName"，但使用 [nameof](/dotnet/csharp/language-reference/keywords/nameof) 运算符可简化稍后的重构过程。</span><span class="sxs-lookup"><span data-stu-id="11db3-192">`AdditionalFields` could be set explicitly to the strings "FirstName" and "LastName", but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="11db3-193">此验证的操作方法必须接受 `firstName` 和 `lastName` 参数：</span><span class="sxs-lookup"><span data-stu-id="11db3-193">The action method for this validation must accept both `firstName` and `lastName` arguments:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="11db3-194">用户输入名字或姓氏时，JavaScript 会进行远程调用，查看该名称对是否已占用。</span><span class="sxs-lookup"><span data-stu-id="11db3-194">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="11db3-195">若要验证两个或更多附加字段，可将其以逗号分隔列表形式提供。</span><span class="sxs-lookup"><span data-stu-id="11db3-195">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="11db3-196">例如，若要向模型中添加 `MiddleName` 属性，可按以下示例所示设置 `[Remote]` 特性：</span><span class="sxs-lookup"><span data-stu-id="11db3-196">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="11db3-197">`AdditionalFields` 与所有属性参数一样，必须是常量表达式。</span><span class="sxs-lookup"><span data-stu-id="11db3-197">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="11db3-198">因此，请勿使用[内插字符串](/dotnet/csharp/language-reference/keywords/interpolated-strings)或调用 <xref:System.String.Join*> 来初始化 `AdditionalFields`。</span><span class="sxs-lookup"><span data-stu-id="11db3-198">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="11db3-199">内置特性的替代特性</span><span class="sxs-lookup"><span data-stu-id="11db3-199">Alternatives to built-in attributes</span></span>

<span data-ttu-id="11db3-200">如果需要并非由内置属性提供的验证，可以：</span><span class="sxs-lookup"><span data-stu-id="11db3-200">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="11db3-201">[创建自定义特性](#custom-attributes)。</span><span class="sxs-lookup"><span data-stu-id="11db3-201">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="11db3-202">[实现 IValidatableObject](#ivalidatableobject)。</span><span class="sxs-lookup"><span data-stu-id="11db3-202">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="11db3-203">自定义特性</span><span class="sxs-lookup"><span data-stu-id="11db3-203">Custom attributes</span></span>

<span data-ttu-id="11db3-204">对于内置验证特性无法处理的情况，可以创建自定义验证特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-204">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="11db3-205">创建继承自 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> 的类，并替代 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> 方法。</span><span class="sxs-lookup"><span data-stu-id="11db3-205">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="11db3-206">`IsValid` 方法接受名为 value 的对象，该对象是要进行验证的输入  。</span><span class="sxs-lookup"><span data-stu-id="11db3-206">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="11db3-207">重载还接受 `ValidationContext` 对象，该对象提供其他信息，例如模型绑定创建的模型实例。</span><span class="sxs-lookup"><span data-stu-id="11db3-207">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="11db3-208">以下示例验证“经典”流派电影的发行日期是否不晚于指定年份  。</span><span class="sxs-lookup"><span data-stu-id="11db3-208">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="11db3-209">`[ClassicMovie]` 属性：</span><span class="sxs-lookup"><span data-stu-id="11db3-209">The `[ClassicMovie]` attribute:</span></span>

* <span data-ttu-id="11db3-210">仅在服务器上运行。</span><span class="sxs-lookup"><span data-stu-id="11db3-210">Is only run on the server.</span></span>
* <span data-ttu-id="11db3-211">对于经典电影，验证发行日期：</span><span class="sxs-lookup"><span data-stu-id="11db3-211">For Classic movies, validates the release date:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttribute.cs?name=snippet_Class)]

<span data-ttu-id="11db3-212">上述示例中的 `movie` 变量表示 `Movie` 对象，其中包含表单提交中的数据。</span><span class="sxs-lookup"><span data-stu-id="11db3-212">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="11db3-213">验证失败时，返回 `ValidationResult` 和错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-213">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="11db3-214">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="11db3-214">IValidatableObject</span></span>

<span data-ttu-id="11db3-215">上述示例只适用于 `Movie` 类型。</span><span class="sxs-lookup"><span data-stu-id="11db3-215">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="11db3-216">类级别验证的另一方式是在模型类中实现 `IValidatableObject`，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-216">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Models/ValidatableMovie.cs?name=snippet_Class&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="11db3-217">顶级节点验证</span><span class="sxs-lookup"><span data-stu-id="11db3-217">Top-level node validation</span></span>

<span data-ttu-id="11db3-218">顶级节点包括：</span><span class="sxs-lookup"><span data-stu-id="11db3-218">Top-level nodes include:</span></span>

* <span data-ttu-id="11db3-219">操作参数</span><span class="sxs-lookup"><span data-stu-id="11db3-219">Action parameters</span></span>
* <span data-ttu-id="11db3-220">控制器属性</span><span class="sxs-lookup"><span data-stu-id="11db3-220">Controller properties</span></span>
* <span data-ttu-id="11db3-221">页处理程序参数</span><span class="sxs-lookup"><span data-stu-id="11db3-221">Page handler parameters</span></span>
* <span data-ttu-id="11db3-222">页模型属性</span><span class="sxs-lookup"><span data-stu-id="11db3-222">Page model properties</span></span>

<span data-ttu-id="11db3-223">除了验证模型属性之外，还验证了模型绑定的顶级节点。</span><span class="sxs-lookup"><span data-stu-id="11db3-223">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="11db3-224">在示例应用的以下示例中，`VerifyPhone` 方法使用 <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> 验证 `phone` 操作参数：</span><span class="sxs-lookup"><span data-stu-id="11db3-224">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="11db3-225">顶级节点可以将 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> 与验证属性结合使用。</span><span class="sxs-lookup"><span data-stu-id="11db3-225">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="11db3-226">在示例应用的以下示例中，`CheckAge` 方法指定在提交表单时必须从查询字符串绑定 `age` 参数：</span><span class="sxs-lookup"><span data-stu-id="11db3-226">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAgeSignature)]

<span data-ttu-id="11db3-227">在“检查年限”页 (CheckAge.cshtml) 中，有两个表单  。</span><span class="sxs-lookup"><span data-stu-id="11db3-227">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="11db3-228">第一个表单将 `99` 的 `Age` 值作为查询字符串参数提交：`https://localhost:5001/Users/CheckAge?Age=99`。</span><span class="sxs-lookup"><span data-stu-id="11db3-228">The first form submits an `Age` value of `99` as a query string parameter: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="11db3-229">当提交查询字符串中格式设置正确的 `age` 参数时，表单将进行验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-229">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="11db3-230">“检查年限”页面上的第二个表单提交请求正文中的 `Age` 值，验证失败。</span><span class="sxs-lookup"><span data-stu-id="11db3-230">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="11db3-231">绑定失败，因为 `age` 参数必须来自查询字符串。</span><span class="sxs-lookup"><span data-stu-id="11db3-231">Binding fails because the `age` parameter must come from a query string.</span></span>

## <a name="maximum-errors"></a><span data-ttu-id="11db3-232">最大错误数</span><span class="sxs-lookup"><span data-stu-id="11db3-232">Maximum errors</span></span>

<span data-ttu-id="11db3-233">达到最大错误数（默认为 200）时，验证停止。</span><span class="sxs-lookup"><span data-stu-id="11db3-233">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="11db3-234">可以使用 `Startup.ConfigureServices` 中的以下代码配置该数字：</span><span class="sxs-lookup"><span data-stu-id="11db3-234">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=4)]

## <a name="maximum-recursion"></a><span data-ttu-id="11db3-235">最大递归次数</span><span class="sxs-lookup"><span data-stu-id="11db3-235">Maximum recursion</span></span>

<span data-ttu-id="11db3-236"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> 遍历所验证模型的对象图。</span><span class="sxs-lookup"><span data-stu-id="11db3-236"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="11db3-237">对于深度或无限递归的模型，验证可能会导致堆栈溢出。</span><span class="sxs-lookup"><span data-stu-id="11db3-237">For models that are deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="11db3-238">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) 提供了在访问者递归超过配置深度时提前停止验证的方法。</span><span class="sxs-lookup"><span data-stu-id="11db3-238">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="11db3-239">`MvcOptions.MaxValidationDepth` 的默认值为 32。</span><span class="sxs-lookup"><span data-stu-id="11db3-239">The default value of `MvcOptions.MaxValidationDepth` is 32.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="11db3-240">自动短路</span><span class="sxs-lookup"><span data-stu-id="11db3-240">Automatic short-circuit</span></span>

<span data-ttu-id="11db3-241">如果模型图不需要验证，验证将自动短路（跳过）。</span><span class="sxs-lookup"><span data-stu-id="11db3-241">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="11db3-242">运行时为其跳过验证的对象包括基元集合（如 `byte[]`、`string[]`、`Dictionary<string, string>`）和不具有任何验证器的复杂对象图。</span><span class="sxs-lookup"><span data-stu-id="11db3-242">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="11db3-243">禁用验证</span><span class="sxs-lookup"><span data-stu-id="11db3-243">Disable validation</span></span>

<span data-ttu-id="11db3-244">若要禁用验证：</span><span class="sxs-lookup"><span data-stu-id="11db3-244">To disable validation:</span></span>

1. <span data-ttu-id="11db3-245">创建不会将任何字段标记为无效的 `IObjectModelValidator` 实现。</span><span class="sxs-lookup"><span data-stu-id="11db3-245">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/NullObjectModelValidator.cs?name=snippet_Class)]

1. <span data-ttu-id="11db3-246">将以下代码添加到 `Startup.ConfigureServices`，以便替换依赖项注入容器中的默认 `IObjectModelValidator` 实现。</span><span class="sxs-lookup"><span data-stu-id="11db3-246">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="11db3-247">你可能仍然会看到模型绑定的模型状态错误。</span><span class="sxs-lookup"><span data-stu-id="11db3-247">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="11db3-248">客户端验证</span><span class="sxs-lookup"><span data-stu-id="11db3-248">Client-side validation</span></span>

<span data-ttu-id="11db3-249">客户端验证将阻止提交，直到表单变为有效为止。</span><span class="sxs-lookup"><span data-stu-id="11db3-249">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="11db3-250">“提交”按钮运行 JavaScript：要么提交表单要么显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-250">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="11db3-251">表单上存在输入错误时，客户端验证会避免到服务器的不必要往返。</span><span class="sxs-lookup"><span data-stu-id="11db3-251">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="11db3-252">_Layout.cshtml 和 _ValidationScriptsPartial.cshtml 中的以下脚本引用支持客户端验证   ：</span><span class="sxs-lookup"><span data-stu-id="11db3-252">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_Scripts)]

[!code-cshtml[](validation/samples/3.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_Scripts)]

<span data-ttu-id="11db3-253">[jQuery 非介入式验证](https://github.com/aspnet/jquery-validation-unobtrusive)脚本是一个基于热门 [jQuery Validate](https://jqueryvalidation.org/) 插件的自定义 Microsoft 前端库。</span><span class="sxs-lookup"><span data-stu-id="11db3-253">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="11db3-254">如果没有 jQuery 非介入式验证，则必须在两个位置编码相同的验证逻辑：一次是在模型属性上的服务器端验证特性中，一次是在客户端脚本中。</span><span class="sxs-lookup"><span data-stu-id="11db3-254">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="11db3-255">[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 [HTML 帮助程序](xref:mvc/views/overview)则使用模型属性中的验证特性和类型元数据，呈现需要验证的表单元素的 HTML 5 `data-` 特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-255">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="11db3-256">jQuery 非介入式验证分析 `data-` 特性并将逻辑传递给 jQuery Validate，从而将服务器端验证逻辑有效地“复制”到客户端。</span><span class="sxs-lookup"><span data-stu-id="11db3-256">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="11db3-257">可以使用标记帮助程序在客户端上显示验证错误，如下所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-257">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/3.x/ValidationSample/Pages/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=3-4)]

<span data-ttu-id="11db3-258">上述标记帮助程序呈现以下 HTML：</span><span class="sxs-lookup"><span data-stu-id="11db3-258">The preceding tag helpers render the following HTML:</span></span>

```html
<div class="form-group">
    <label class="control-label" for="Movie_ReleaseDate">Release Date</label>
    <input class="form-control" type="date" data-val="true"
        data-val-required="The Release Date field is required."
        id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
    <span class="text-danger field-validation-valid"
        data-valmsg-for="Movie.ReleaseDate" data-valmsg-replace="true"></span>
</div>
```

<span data-ttu-id="11db3-259">请注意，HTML 输出中的 `data-` 特性与 `Movie.ReleaseDate` 属性的验证特性相对应。</span><span class="sxs-lookup"><span data-stu-id="11db3-259">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `Movie.ReleaseDate` property.</span></span> <span data-ttu-id="11db3-260">`data-val-required` 特性包含在用户未填写上映日期字段时将显示的错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-260">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="11db3-261">jQuery 非介入式验证将此值传递给 jQuery Validate [required()](https://jqueryvalidation.org/required-method/) 方法，该方法随后在随附的 **\<span>** 元素中显示该消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-261">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="11db3-262">如果 `[DataType]` 特性未替代属性的 .NET 类型，则数据类型验证基于该类型。</span><span class="sxs-lookup"><span data-stu-id="11db3-262">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="11db3-263">浏览器具有自己的默认错误消息，但是 jQuery 验证非介入式验证包可以替代这些消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-263">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="11db3-264">通过 `[DataType]` 特性和 `[EmailAddress]` 等子类可以指定错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-264">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

## <a name="unobtrusive-validation"></a><span data-ttu-id="11db3-265">非介入式验证</span><span class="sxs-lookup"><span data-stu-id="11db3-265">Unobtrusive validation</span></span>

<span data-ttu-id="11db3-266">有关非介入式验证的信息，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore.Docs/issues/1111)。</span><span class="sxs-lookup"><span data-stu-id="11db3-266">For information on unobtrusive validation, see [this GitHub issue](https://github.com/aspnet/AspNetCore.Docs/issues/1111).</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="11db3-267">向动态表单添加验证</span><span class="sxs-lookup"><span data-stu-id="11db3-267">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="11db3-268">jQuery 非介入式验证会在第一次加载页面时将验证逻辑和参数传递到 jQuery Validate。</span><span class="sxs-lookup"><span data-stu-id="11db3-268">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="11db3-269">因此，不会对动态生成的表单自动执行验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-269">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="11db3-270">若要启用验证，指示 jQuery 非介入式验证在创建动态表单后立即对其进行分析。</span><span class="sxs-lookup"><span data-stu-id="11db3-270">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="11db3-271">例如，以下代码在通过 AJAX 添加的表单上设置客户端验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-271">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="11db3-272">`$.validator.unobtrusive.parse()` 方法采用 jQuery 选择器作为它的一个参数。</span><span class="sxs-lookup"><span data-stu-id="11db3-272">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="11db3-273">此方法指示 jQuery 非介入式验证分析该选择器内表单的 `data-` 属性。</span><span class="sxs-lookup"><span data-stu-id="11db3-273">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="11db3-274">然后将这些特性的值传递给 jQuery Validate 插件。</span><span class="sxs-lookup"><span data-stu-id="11db3-274">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="11db3-275">向动态控件添加验证</span><span class="sxs-lookup"><span data-stu-id="11db3-275">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="11db3-276">`$.validator.unobtrusive.parse()` 方法适用于整个表单，而不是 `<input>` 和 `<select/>` 等单个动态生成的控件。</span><span class="sxs-lookup"><span data-stu-id="11db3-276">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="11db3-277">若要重新分析表单，删除之前分析表单时添加的验证数据，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-277">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="11db3-278">自定义客户端验证</span><span class="sxs-lookup"><span data-stu-id="11db3-278">Custom client-side validation</span></span>

<span data-ttu-id="11db3-279">生成适用于自定义 jQuery Validate 适配器的 `data-` HTML 特性，完成自定义客户端验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-279">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="11db3-280">以下示例适配器代码是为本文前面部分介绍的 `[ClassicMovie]` 和 `[ClassicMovieWithClientValidator]` 特性编写的：</span><span class="sxs-lookup"><span data-stu-id="11db3-280">The following sample adapter code was written for the `[ClassicMovie]` and `[ClassicMovieWithClientValidator]` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/3.x/ValidationSample/wwwroot/js/classicMovieValidator.js)]

<span data-ttu-id="11db3-281">有关如何编写适配器的信息，请参阅 [jQuery Validate 文档](https://jqueryvalidation.org/documentation/)。</span><span class="sxs-lookup"><span data-stu-id="11db3-281">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="11db3-282">给定字段的适配器的使用由 `data-` 特性触发，这些特性：</span><span class="sxs-lookup"><span data-stu-id="11db3-282">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="11db3-283">将字段标记为正在验证 (`data-val="true"`)。</span><span class="sxs-lookup"><span data-stu-id="11db3-283">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="11db3-284">确定验证规则名称和错误消息文本（例如，`data-val-rulename="Error message."`）。</span><span class="sxs-lookup"><span data-stu-id="11db3-284">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="11db3-285">提供验证器所需的任何其他参数（例如，`data-val-rulename-param1="value"`）。</span><span class="sxs-lookup"><span data-stu-id="11db3-285">Provide any additional parameters the validator needs (for example, `data-val-rulename-param1="value"`).</span></span>

<span data-ttu-id="11db3-286">以下示例显示示例应用的 `ClassicMovie` 特性的 `data-` 特性：</span><span class="sxs-lookup"><span data-stu-id="11db3-286">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="date"
    data-val="true"
    data-val-classicmovie="Classic movies must have a release year no later than 1960."
    data-val-classicmovie-year="1960"
    data-val-required="The Release Date field is required."
    id="Movie_ReleaseDate" name="Movie.ReleaseDate" value="">
```

<span data-ttu-id="11db3-287">如前所述，[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 [HTML 帮助程序](xref:mvc/views/overview)使用验证特性的信息呈现 `data-` 特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-287">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="11db3-288">编写用于创建自定义 `data-` HTML 特性的代码有以下两种方式：</span><span class="sxs-lookup"><span data-stu-id="11db3-288">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="11db3-289">创建派生自 `AttributeAdapterBase<TAttribute>` 的类和实现 `IValidationAttributeAdapterProvider` 的类，并在 DI 中注册特性及其适配器。</span><span class="sxs-lookup"><span data-stu-id="11db3-289">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="11db3-290">此方法遵循[单一责任主体](https://wikipedia.org/wiki/Single_responsibility_principle)，这体现在与服务器相关的验证代码和与客户端相关的验证代码位于不同的类中。</span><span class="sxs-lookup"><span data-stu-id="11db3-290">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="11db3-291">适配器还有以下优势：由于适配器是在 DI 中注册的，所以如果需要，适配器可以使用 DI 中的其他服务。</span><span class="sxs-lookup"><span data-stu-id="11db3-291">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="11db3-292">在 `ValidationAttribute` 类中实现 `IClientModelValidator`。</span><span class="sxs-lookup"><span data-stu-id="11db3-292">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="11db3-293">如果特性既不进行任何服务器端验证，也不需要 DI 的任何服务，则此方法可能适用。</span><span class="sxs-lookup"><span data-stu-id="11db3-293">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="11db3-294">用于客户端验证的 AttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="11db3-294">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="11db3-295">在 HTML 中呈现 `data-` 特性的方法在示例应用中由 `ClassicMovie` 特性使用。</span><span class="sxs-lookup"><span data-stu-id="11db3-295">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="11db3-296">若要使用此方法添加客户端验证：</span><span class="sxs-lookup"><span data-stu-id="11db3-296">To add client validation by using this method:</span></span>

1. <span data-ttu-id="11db3-297">为自定义验证特性创建特性适配器类。</span><span class="sxs-lookup"><span data-stu-id="11db3-297">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="11db3-298">从 [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2) 派生类。</span><span class="sxs-lookup"><span data-stu-id="11db3-298">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="11db3-299">创建将 `data-` 特性添加到所呈现输出中的 `AddValidation` 方法，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-299">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieAttributeAdapter.cs?name=snippet_Class)]

1. <span data-ttu-id="11db3-300">创建实现 <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> 的适配器提供程序类。</span><span class="sxs-lookup"><span data-stu-id="11db3-300">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="11db3-301">使用 `GetAttributeAdapter` 方法，将自定义属性传递给适配器的构造函数，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-301">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/CustomValidationAttributeAdapterProvider.cs?name=snippet_Class)]

1. <span data-ttu-id="11db3-302">在 `Startup.ConfigureServices` 中为 DI 注册适配器提供程序：</span><span class="sxs-lookup"><span data-stu-id="11db3-302">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_Configuration&highlight=9-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="11db3-303">用于客户端验证的 IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="11db3-303">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="11db3-304">在 HTML 中呈现 `data-` 特性的方法在示例应用中由 `ClassicMovieWithClientValidator` 特性使用。</span><span class="sxs-lookup"><span data-stu-id="11db3-304">This method of rendering `data-` attributes in HTML is used by the `ClassicMovieWithClientValidator` attribute in the sample app.</span></span> <span data-ttu-id="11db3-305">若要使用此方法添加客户端验证：</span><span class="sxs-lookup"><span data-stu-id="11db3-305">To add client validation by using this method:</span></span>

* <span data-ttu-id="11db3-306">在自定义验证特性中，实现 `IClientModelValidator` 接口并创建 `AddValidation` 方法。</span><span class="sxs-lookup"><span data-stu-id="11db3-306">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="11db3-307">使用 `AddValidation` 方法，添加 `data-` 特性进行验证，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-307">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/3.x/ValidationSample/Validation/ClassicMovieWithClientValidatorAttribute.cs?name=snippet_Class)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="11db3-308">禁用客户端验证</span><span class="sxs-lookup"><span data-stu-id="11db3-308">Disable client-side validation</span></span>

<span data-ttu-id="11db3-309">以下代码禁用 Razor Pages 中的客户端验证：</span><span class="sxs-lookup"><span data-stu-id="11db3-309">The following code disables client validation in Razor Pages:</span></span>

[!code-csharp[](validation/samples/3.x/ValidationSample/Startup.cs?name=snippet_DisableClientValidation&highlight=2-5)]

<span data-ttu-id="11db3-310">可禁用客户端验证的其他选项：</span><span class="sxs-lookup"><span data-stu-id="11db3-310">Other options to disable client-side validation:</span></span>

* <span data-ttu-id="11db3-311">在所有 *.cshtml* 文件中注释掉对 `_ValidationScriptsPartial` 的引用。</span><span class="sxs-lookup"><span data-stu-id="11db3-311">Comment out the reference to `_ValidationScriptsPartial` in all the *.cshtml* files.</span></span>
* <span data-ttu-id="11db3-312">删除 *Pages\Shared\_ValidationScriptsPartial.cshtml* 文件的内容。</span><span class="sxs-lookup"><span data-stu-id="11db3-312">Remove the contents of the *Pages\Shared\_ValidationScriptsPartial.cshtml* file.</span></span>

<span data-ttu-id="11db3-313">上述方法不会阻止 ASP.NET Core Identity Razor Class Library 的客户端验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-313">The preceding approach won't prevent client side validation of ASP.NET Core Identity Razor Class Library.</span></span> <span data-ttu-id="11db3-314">有关详细信息，请参阅 <xref:security/authentication/scaffold-identity>。</span><span class="sxs-lookup"><span data-stu-id="11db3-314">For more information, see <xref:security/authentication/scaffold-identity>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="11db3-315">其他资源</span><span class="sxs-lookup"><span data-stu-id="11db3-315">Additional resources</span></span>

* [<span data-ttu-id="11db3-316">System.ComponentModel.DataAnnotations 命名空间</span><span class="sxs-lookup"><span data-stu-id="11db3-316">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="11db3-317">模型绑定</span><span class="sxs-lookup"><span data-stu-id="11db3-317">Model Binding</span></span>](model-binding.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="11db3-318">本文介绍如何在 ASP.NET Core MVC 或 Razor Pages 应用中验证用户输入。</span><span class="sxs-lookup"><span data-stu-id="11db3-318">This article explains how to validate user input in an ASP.NET Core MVC or Razor Pages app.</span></span>

<span data-ttu-id="11db3-319">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="11db3-319">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="model-state"></a><span data-ttu-id="11db3-320">模型状态</span><span class="sxs-lookup"><span data-stu-id="11db3-320">Model state</span></span>

<span data-ttu-id="11db3-321">模型状态表示两个子系统的错误：模型绑定和模型验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-321">Model state represents errors that come from two subsystems: model binding and model validation.</span></span> <span data-ttu-id="11db3-322">[模型绑定](model-binding.md)的错误通常是数据转换错误（例如，在要求为整数的字段中输入“x”）。</span><span class="sxs-lookup"><span data-stu-id="11db3-322">Errors that originate from [model binding](model-binding.md) are generally data conversion errors (for example, an "x" is entered in a field that expects an integer).</span></span> <span data-ttu-id="11db3-323">模型验证在模型绑定之后进行，并在数据不符合业务规则时报告错误（例如，在要求评级为 1 至 5 之间的字段中输入 0）。</span><span class="sxs-lookup"><span data-stu-id="11db3-323">Model validation occurs after model binding and reports errors where the data doesn't conform to business rules (for example, a 0 is entered in a field that expects a rating between 1 and 5).</span></span>

<span data-ttu-id="11db3-324">模型绑定和验证都在执行控制器操作或 Razor Pages 处理程序方法之前进行。</span><span class="sxs-lookup"><span data-stu-id="11db3-324">Both model binding and validation occur before the execution of a controller action or a Razor Pages handler method.</span></span> <span data-ttu-id="11db3-325">Web 应用负责检查 `ModelState.IsValid` 并做出相应响应。</span><span class="sxs-lookup"><span data-stu-id="11db3-325">For web apps, it's the app's responsibility to inspect `ModelState.IsValid` and react appropriately.</span></span> <span data-ttu-id="11db3-326">Web 应用通常会重新显示带有错误消息的页面：</span><span class="sxs-lookup"><span data-stu-id="11db3-326">Web apps typically redisplay the page with an error message:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Create.cshtml.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="11db3-327">如果 Web API 控制器具有 `[ApiController]` 特性，则它们不必检查 `ModelState.IsValid`。</span><span class="sxs-lookup"><span data-stu-id="11db3-327">Web API controllers don't have to check `ModelState.IsValid` if they have the `[ApiController]` attribute.</span></span> <span data-ttu-id="11db3-328">在此情况下，如果模型状态无效，将返回包含错误详细信息的自动 HTTP 400 响应。</span><span class="sxs-lookup"><span data-stu-id="11db3-328">In that case, an automatic HTTP 400 response containing error details is returned when model state is invalid.</span></span> <span data-ttu-id="11db3-329">有关详细信息，请参阅[自动 HTTP 400 响应](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="11db3-329">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

## <a name="rerun-validation"></a><span data-ttu-id="11db3-330">重新运行验证</span><span class="sxs-lookup"><span data-stu-id="11db3-330">Rerun validation</span></span>

<span data-ttu-id="11db3-331">验证自动进行，但是可能需要手动进行重复验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-331">Validation is automatic, but you might want to repeat it manually.</span></span> <span data-ttu-id="11db3-332">例如，你可能为属性计算一个值，并且希望将属性设置为所计算的值后，再重新运行验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-332">For example, you might compute a value for a property and want to rerun validation after setting the property to the computed value.</span></span> <span data-ttu-id="11db3-333">若要重新运行验证，请调用 `TryValidateModel` 方法，如下所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-333">To rerun validation, call the `TryValidateModel` method, as shown here:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/MoviesController.cs?name=snippet_TryValidateModel&highlight=11)]

## <a name="validation-attributes"></a><span data-ttu-id="11db3-334">验证特性</span><span class="sxs-lookup"><span data-stu-id="11db3-334">Validation attributes</span></span>

<span data-ttu-id="11db3-335">通过验证特性可以为模型属性指定验证规则。</span><span class="sxs-lookup"><span data-stu-id="11db3-335">Validation attributes let you specify validation rules for model properties.</span></span> <span data-ttu-id="11db3-336">[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample)的以下示例显示使用验证特性进行注释的模型类。</span><span class="sxs-lookup"><span data-stu-id="11db3-336">The following example from [the sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/models/validation/sample) shows a model class that is annotated with validation attributes.</span></span> <span data-ttu-id="11db3-337">`[ClassicMovie]` 特性为自定义的验证特性，其他特性为内置的验证特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-337">The `[ClassicMovie]` attribute is a custom validation attribute and the others are built-in.</span></span> <span data-ttu-id="11db3-338">`[ClassicMovie2]` 未显示，它表示实现自定义特性的另一种方法。</span><span class="sxs-lookup"><span data-stu-id="11db3-338">Not shown is `[ClassicMovie2]`, which shows an alternative way to implement a custom attribute.</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/Movie.cs?name=snippet_ModelClass)]

## <a name="built-in-attributes"></a><span data-ttu-id="11db3-339">内置特性</span><span class="sxs-lookup"><span data-stu-id="11db3-339">Built-in attributes</span></span>

<span data-ttu-id="11db3-340">内置验证特性包括：</span><span class="sxs-lookup"><span data-stu-id="11db3-340">Built-in validation attributes include:</span></span>

* <span data-ttu-id="11db3-341">`[CreditCard]`：验证属性是否有信用卡格式。</span><span class="sxs-lookup"><span data-stu-id="11db3-341">`[CreditCard]`: Validates that the property has a credit card format.</span></span>
* <span data-ttu-id="11db3-342">`[Compare]`：验证模型中的两个属性是否匹配。</span><span class="sxs-lookup"><span data-stu-id="11db3-342">`[Compare]`: Validates that two properties in a model match.</span></span> <span data-ttu-id="11db3-343">例如，*Register.cshtml.cs* 文件使用 `[Compare]` 来验证输入的两个密码是否匹配。</span><span class="sxs-lookup"><span data-stu-id="11db3-343">For example, the *Register.cshtml.cs* file uses `[Compare]` to validate the two entered passwords match.</span></span> <span data-ttu-id="11db3-344">[基架标识](xref:security/authentication/scaffold-identity)可查看注册代码。</span><span class="sxs-lookup"><span data-stu-id="11db3-344">[Scaffold Identity](xref:security/authentication/scaffold-identity) to see the Register code.</span></span>
* <span data-ttu-id="11db3-345">`[EmailAddress]`：验证属性是否有电子邮件格式。</span><span class="sxs-lookup"><span data-stu-id="11db3-345">`[EmailAddress]`: Validates that the property has an email format.</span></span>
* <span data-ttu-id="11db3-346">`[Phone]`：验证属性是否有电话号码格式。</span><span class="sxs-lookup"><span data-stu-id="11db3-346">`[Phone]`: Validates that the property has a telephone number format.</span></span>
* <span data-ttu-id="11db3-347">`[Range]`：验证属性值是否在指定范围内。</span><span class="sxs-lookup"><span data-stu-id="11db3-347">`[Range]`: Validates that the property value falls within a specified range.</span></span>
* <span data-ttu-id="11db3-348">`[RegularExpression]`：验证属性值是否与指定的正则表达式匹配。</span><span class="sxs-lookup"><span data-stu-id="11db3-348">`[RegularExpression]`: Validates that the property value matches a specified regular expression.</span></span>
* <span data-ttu-id="11db3-349">`[Required]`：验证字段是否非 NULL。</span><span class="sxs-lookup"><span data-stu-id="11db3-349">`[Required]`: Validates that the field is not null.</span></span> <span data-ttu-id="11db3-350">请参阅 [`[Required]` 属性](#required-attribute)，获取关于该特性的行为的详细信息。</span><span class="sxs-lookup"><span data-stu-id="11db3-350">See [`[Required]` attribute](#required-attribute) for details about this attribute's behavior.</span></span>
* <span data-ttu-id="11db3-351">`[StringLength]`：验证字符串属性值是否未超过指定长度限制。</span><span class="sxs-lookup"><span data-stu-id="11db3-351">`[StringLength]`: Validates that a string property value doesn't exceed a specified length limit.</span></span>
* <span data-ttu-id="11db3-352">`[Url]`：验证属性是否有 URL 格式。</span><span class="sxs-lookup"><span data-stu-id="11db3-352">`[Url]`: Validates that the property has a URL format.</span></span>
* <span data-ttu-id="11db3-353">`[Remote]`：通过调用服务器上的操作方法，验证客户端上的输入。</span><span class="sxs-lookup"><span data-stu-id="11db3-353">`[Remote]`: Validates input on the client by calling an action method on the server.</span></span> <span data-ttu-id="11db3-354">请参阅 [`[Remote]` 属性](#remote-attribute)，获取关于该特性的行为的详细信息。</span><span class="sxs-lookup"><span data-stu-id="11db3-354">See [`[Remote]` attribute](#remote-attribute) for details about this attribute's behavior.</span></span>

<span data-ttu-id="11db3-355">将 `[RegularExpression]` 属性用于客户端验证时，在客户端上使用 JavaScript 执行正则表达式。</span><span class="sxs-lookup"><span data-stu-id="11db3-355">When using the `[RegularExpression]` attribute with client-side validation, the regex is executed in JavaScript on the client.</span></span> <span data-ttu-id="11db3-356">这意味着将使用 [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) 匹配行为。</span><span class="sxs-lookup"><span data-stu-id="11db3-356">This means [ECMAScript](/dotnet/standard/base-types/regular-expression-options#ecmascript-matching-behavior) matching behavior will be used.</span></span> <span data-ttu-id="11db3-357">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/corefx/issues/42487)。</span><span class="sxs-lookup"><span data-stu-id="11db3-357">For more information, see [this GitHub issue](https://github.com/dotnet/corefx/issues/42487).</span></span>

<span data-ttu-id="11db3-358">在 [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) 命名空间中可找到验证特性的完整列表。</span><span class="sxs-lookup"><span data-stu-id="11db3-358">A complete list of validation attributes can be found in the [System.ComponentModel.DataAnnotations](xref:System.ComponentModel.DataAnnotations) namespace.</span></span>

### <a name="error-messages"></a><span data-ttu-id="11db3-359">错误消息</span><span class="sxs-lookup"><span data-stu-id="11db3-359">Error messages</span></span>

<span data-ttu-id="11db3-360">通过验证特性可以指定要为无效输入显示的错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-360">Validation attributes let you specify the error message to be displayed for invalid input.</span></span> <span data-ttu-id="11db3-361">例如：</span><span class="sxs-lookup"><span data-stu-id="11db3-361">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "Name length can't be more than 8.")]
```

<span data-ttu-id="11db3-362">在内部，特性使用用于字段名的某个占位符调用 `String.Format`，有时还使用额外占位符。</span><span class="sxs-lookup"><span data-stu-id="11db3-362">Internally, the attributes call `String.Format` with a placeholder for the field name and sometimes additional placeholders.</span></span> <span data-ttu-id="11db3-363">例如：</span><span class="sxs-lookup"><span data-stu-id="11db3-363">For example:</span></span>

```csharp
[StringLength(8, ErrorMessage = "{0} length must be between {2} and {1}.", MinimumLength = 6)]
```

<span data-ttu-id="11db3-364">应用于 `Name` 属性时，上述代码创建的错误消息将为“名称长度必须介于 6 到 8 之间”。</span><span class="sxs-lookup"><span data-stu-id="11db3-364">When applied to a `Name` property, the error message created by the preceding code would be "Name length must be between 6 and 8.".</span></span>

<span data-ttu-id="11db3-365">若要查找为特定特性的错误消息而传递给 `String.Format` 的参数，请参阅 [DataAnnotations 源代码](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations)。</span><span class="sxs-lookup"><span data-stu-id="11db3-365">To find out which parameters are passed to `String.Format` for a particular attribute's error message, see the [DataAnnotations source code](https://github.com/dotnet/corefx/tree/master/src/System.ComponentModel.Annotations/src/System/ComponentModel/DataAnnotations).</span></span>

## <a name="required-attribute"></a><span data-ttu-id="11db3-366">[必需] 特性</span><span class="sxs-lookup"><span data-stu-id="11db3-366">[Required] attribute</span></span>

<span data-ttu-id="11db3-367">默认情况下，验证系统将不可为 null 的参数或属性视为具有 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-367">By default, the validation system treats non-nullable parameters or properties as if they had a `[Required]` attribute.</span></span> <span data-ttu-id="11db3-368">`decimal` 和 `int` 等[值类型](/dotnet/csharp/language-reference/keywords/value-types)是不可为 null 的类型。</span><span class="sxs-lookup"><span data-stu-id="11db3-368">[Value types](/dotnet/csharp/language-reference/keywords/value-types) such as `decimal` and `int` are non-nullable.</span></span>

### <a name="required-validation-on-the-server"></a><span data-ttu-id="11db3-369">[必需] 服务器上的验证</span><span class="sxs-lookup"><span data-stu-id="11db3-369">[Required] validation on the server</span></span>

<span data-ttu-id="11db3-370">在服务器上，如果属性为 null，则认为所需值缺失。</span><span class="sxs-lookup"><span data-stu-id="11db3-370">On the server, a required value is considered missing if the property is null.</span></span> <span data-ttu-id="11db3-371">不可为 null 的字段始终有效，并且从不显示 [必需] 特性的错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-371">A non-nullable field is always valid, and the [Required] attribute's error message is never displayed.</span></span>

<span data-ttu-id="11db3-372">但是，不可为 null 的属性的模型绑定可能会失败，从而导致 `The value '' is invalid` 等错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-372">However, model binding for a non-nullable property may fail, resulting in an error message such as `The value '' is invalid`.</span></span> <span data-ttu-id="11db3-373">若要为不可为 null 的类型的服务器端验证指定自定义错误消息，可使用以下选项：</span><span class="sxs-lookup"><span data-stu-id="11db3-373">To specify a custom error message for server-side validation of non-nullable types, you have the following options:</span></span>

* <span data-ttu-id="11db3-374">将字段设置为可以为 null（例如，`decimal?`而不是 `decimal`）。</span><span class="sxs-lookup"><span data-stu-id="11db3-374">Make the field nullable (for example, `decimal?` instead of `decimal`).</span></span> <span data-ttu-id="11db3-375">类似于标准的可以为 null 的类型来处理[可以为 null\<T>](/dotnet/csharp/programming-guide/nullable-types/) 值类型。</span><span class="sxs-lookup"><span data-stu-id="11db3-375">[Nullable\<T>](/dotnet/csharp/programming-guide/nullable-types/) value types are treated like standard nullable types.</span></span>
* <span data-ttu-id="11db3-376">指定模型绑定要使用的默认错误消息，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-376">Specify the default error message to be used by model binding, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=4-5)]

  <span data-ttu-id="11db3-377">有关模型绑定错误（可以为其设置默认消息）的更多信息，请参阅 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>。</span><span class="sxs-lookup"><span data-stu-id="11db3-377">For more information about model binding errors that you can set default messages for, see <xref:Microsoft.AspNetCore.Mvc.ModelBinding.Metadata.DefaultModelBindingMessageProvider#methods>.</span></span>

### <a name="required-validation-on-the-client"></a><span data-ttu-id="11db3-378">[必需] 客户端上的验证</span><span class="sxs-lookup"><span data-stu-id="11db3-378">[Required] validation on the client</span></span>

<span data-ttu-id="11db3-379">在客户端上处理不可为 null 类型和字符串的方式与在服务器上不同。</span><span class="sxs-lookup"><span data-stu-id="11db3-379">Non-nullable types and strings are handled differently on the client compared to the server.</span></span> <span data-ttu-id="11db3-380">在客户端上：</span><span class="sxs-lookup"><span data-stu-id="11db3-380">On the client:</span></span>

* <span data-ttu-id="11db3-381">只有在为值输入一个输入时，才认为该值存在。</span><span class="sxs-lookup"><span data-stu-id="11db3-381">A value is considered present only if input is entered for it.</span></span> <span data-ttu-id="11db3-382">因此，客户端验证处理不可为 null 类型的方式与处理可以为 null 类型的方式相同。</span><span class="sxs-lookup"><span data-stu-id="11db3-382">Therefore, client-side validation handles non-nullable types the same as nullable types.</span></span>
* <span data-ttu-id="11db3-383">jQuery 验证[必需](https://jqueryvalidation.org/required-method/)方法将字符串字段中的空格视为有效输入。</span><span class="sxs-lookup"><span data-stu-id="11db3-383">Whitespace in a string field is considered valid input by the jQuery Validation [required](https://jqueryvalidation.org/required-method/) method.</span></span> <span data-ttu-id="11db3-384">如果只输入空格，服务器端验证会将必需的字符串字段视为无效。</span><span class="sxs-lookup"><span data-stu-id="11db3-384">Server-side validation considers a required string field invalid if only whitespace is entered.</span></span>

<span data-ttu-id="11db3-385">如前所述，将不可为 null 类型视为具有 `[Required]` 特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-385">As noted earlier, non-nullable types are treated as though they had a `[Required]` attribute.</span></span> <span data-ttu-id="11db3-386">这意味着即使不应用 `[Required]` 特性，也可进行客户端验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-386">That means you get client-side validation even if you don't apply the `[Required]` attribute.</span></span> <span data-ttu-id="11db3-387">但如果不使用该特性，将收到默认错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-387">But if you don't use the attribute, you get a default error message.</span></span> <span data-ttu-id="11db3-388">若要指定自定义错误消息，使用该特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-388">To specify a custom error message, use the attribute.</span></span>

## <a name="remote-attribute"></a><span data-ttu-id="11db3-389">[远程] 特性</span><span class="sxs-lookup"><span data-stu-id="11db3-389">[Remote] attribute</span></span>

<span data-ttu-id="11db3-390">`[Remote]` 特性实现客户端验证，该验证需要在服务器上调用方法，以确定字段输入是否有效。</span><span class="sxs-lookup"><span data-stu-id="11db3-390">The `[Remote]` attribute implements client-side validation that requires calling a method on the server to determine whether field input is valid.</span></span> <span data-ttu-id="11db3-391">例如，应用可能需要验证用户名是否已在使用。</span><span class="sxs-lookup"><span data-stu-id="11db3-391">For example, the app may need to verify whether a user name is already in use.</span></span>

<span data-ttu-id="11db3-392">若要实现远程验证：</span><span class="sxs-lookup"><span data-stu-id="11db3-392">To implement remote validation:</span></span>

1. <span data-ttu-id="11db3-393">创建可供 JavaScript 调用的操作方法。</span><span class="sxs-lookup"><span data-stu-id="11db3-393">Create an action method for JavaScript to call.</span></span>  <span data-ttu-id="11db3-394">jQuery Validate [远程](https://jqueryvalidation.org/remote-method/)方法要求 JSON 响应：</span><span class="sxs-lookup"><span data-stu-id="11db3-394">The jQuery Validate [remote](https://jqueryvalidation.org/remote-method/) method expects a JSON response:</span></span>

   * <span data-ttu-id="11db3-395">`"true"` 表示输入数据有效。</span><span class="sxs-lookup"><span data-stu-id="11db3-395">`"true"` means the input data is valid.</span></span>
   * <span data-ttu-id="11db3-396">`"false"`、`undefined` 或 `null` 表示输入无效。</span><span class="sxs-lookup"><span data-stu-id="11db3-396">`"false"`, `undefined`, or `null` means the input is invalid.</span></span>  <span data-ttu-id="11db3-397">显示默认错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-397">Display the default error message.</span></span>
   * <span data-ttu-id="11db3-398">任何其他字符串都表示输入无效。</span><span class="sxs-lookup"><span data-stu-id="11db3-398">Any other string means the input is invalid.</span></span> <span data-ttu-id="11db3-399">将字符串显示为自定义错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-399">Display the string as a custom error message.</span></span>

   <span data-ttu-id="11db3-400">以下是返回自定义错误消息的操作方法示例：</span><span class="sxs-lookup"><span data-stu-id="11db3-400">Here's an example of an action method that returns a custom error message:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyEmail)]

1. <span data-ttu-id="11db3-401">在模型类中，使用指向验证操作方法的 `[Remote]` 特性注释属性，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-401">In the model class, annotate the property with a `[Remote]` attribute that points to the validation action method, as shown in the following example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserEmailProperty)]
 
   <span data-ttu-id="11db3-402">`[Remote]` 特性位于 `Microsoft.AspNetCore.Mvc` 命名空间中。</span><span class="sxs-lookup"><span data-stu-id="11db3-402">The `[Remote]` attribute is in the `Microsoft.AspNetCore.Mvc` namespace.</span></span> <span data-ttu-id="11db3-403">如果未使用 `Microsoft.AspNetCore.App` 或 `Microsoft.AspNetCore.All` 元包，则请安装 [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="11db3-403">Install the [Microsoft.AspNetCore.Mvc.ViewFeatures](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc.ViewFeatures) NuGet package if you're not using the `Microsoft.AspNetCore.App` or `Microsoft.AspNetCore.All` metapackage.</span></span>
   
### <a name="additional-fields"></a><span data-ttu-id="11db3-404">其他字段</span><span class="sxs-lookup"><span data-stu-id="11db3-404">Additional fields</span></span>

<span data-ttu-id="11db3-405">通过 `[Remote]` 特性的 `AdditionalFields` 属性可以根据服务器上的数据验证字段组合。</span><span class="sxs-lookup"><span data-stu-id="11db3-405">The `AdditionalFields` property of the `[Remote]` attribute lets you validate combinations of fields against data on the server.</span></span> <span data-ttu-id="11db3-406">例如，如果 `User` 模型具有 `FirstName` 和 `LastName` 属性，可能需要验证该名称对尚未被现有用户占用。</span><span class="sxs-lookup"><span data-stu-id="11db3-406">For example, if the `User` model had `FirstName` and `LastName` properties, you might want to verify that no existing users already have that pair of names.</span></span> <span data-ttu-id="11db3-407">下面的示例演示如何使用 `AdditionalFields`：</span><span class="sxs-lookup"><span data-stu-id="11db3-407">The following example shows how to use `AdditionalFields`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/User.cs?name=snippet_UserNameProperties)]

<span data-ttu-id="11db3-408">`AdditionalFields`可以显式设置为字符串 `"FirstName"` 和 `"LastName"`，但使用 [nameof](/dotnet/csharp/language-reference/keywords/nameof) 运算符可简化稍后的重构过程。</span><span class="sxs-lookup"><span data-stu-id="11db3-408">`AdditionalFields` could be set explicitly to the strings `"FirstName"` and `"LastName"`, but using the [nameof](/dotnet/csharp/language-reference/keywords/nameof) operator simplifies later refactoring.</span></span> <span data-ttu-id="11db3-409">此验证的操作方法必须接受 first name 和 last name 参数：</span><span class="sxs-lookup"><span data-stu-id="11db3-409">The action method for this validation must accept both first name and last name arguments:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyName)]

<span data-ttu-id="11db3-410">用户输入名字或姓氏时，JavaScript 会进行远程调用，查看该名称对是否已占用。</span><span class="sxs-lookup"><span data-stu-id="11db3-410">When the user enters a first or last name, JavaScript makes a remote call to see if that pair of names has been taken.</span></span>

<span data-ttu-id="11db3-411">若要验证两个或更多附加字段，可将其以逗号分隔列表形式提供。</span><span class="sxs-lookup"><span data-stu-id="11db3-411">To validate two or more additional fields, provide them as a comma-delimited list.</span></span> <span data-ttu-id="11db3-412">例如，若要向模型中添加 `MiddleName` 属性，可按以下示例所示设置 `[Remote]` 特性：</span><span class="sxs-lookup"><span data-stu-id="11db3-412">For example, to add a `MiddleName` property to the model, set the `[Remote]` attribute as shown in the following example:</span></span>

```cs
[Remote(action: "VerifyName", controller: "Users", AdditionalFields = nameof(FirstName) + "," + nameof(LastName))]
public string MiddleName { get; set; }
```

<span data-ttu-id="11db3-413">`AdditionalFields` 与所有属性参数一样，必须是常量表达式。</span><span class="sxs-lookup"><span data-stu-id="11db3-413">`AdditionalFields`, like all attribute arguments, must be a constant expression.</span></span> <span data-ttu-id="11db3-414">因此，请勿使用[内插字符串](/dotnet/csharp/language-reference/keywords/interpolated-strings)或调用 <xref:System.String.Join*> 来初始化 `AdditionalFields`。</span><span class="sxs-lookup"><span data-stu-id="11db3-414">Therefore, don't use an [interpolated string](/dotnet/csharp/language-reference/keywords/interpolated-strings) or call <xref:System.String.Join*> to initialize `AdditionalFields`.</span></span>

## <a name="alternatives-to-built-in-attributes"></a><span data-ttu-id="11db3-415">内置特性的替代特性</span><span class="sxs-lookup"><span data-stu-id="11db3-415">Alternatives to built-in attributes</span></span>

<span data-ttu-id="11db3-416">如果需要并非由内置属性提供的验证，可以：</span><span class="sxs-lookup"><span data-stu-id="11db3-416">If you need validation not provided by built-in attributes, you can:</span></span>

* <span data-ttu-id="11db3-417">[创建自定义特性](#custom-attributes)。</span><span class="sxs-lookup"><span data-stu-id="11db3-417">[Create custom attributes](#custom-attributes).</span></span>
* <span data-ttu-id="11db3-418">[实现 IValidatableObject](#ivalidatableobject)。</span><span class="sxs-lookup"><span data-stu-id="11db3-418">[Implement IValidatableObject](#ivalidatableobject).</span></span>

## <a name="custom-attributes"></a><span data-ttu-id="11db3-419">自定义特性</span><span class="sxs-lookup"><span data-stu-id="11db3-419">Custom attributes</span></span>

<span data-ttu-id="11db3-420">对于内置验证特性无法处理的情况，可以创建自定义验证特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-420">For scenarios that the built-in validation attributes don't handle, you can create custom validation attributes.</span></span> <span data-ttu-id="11db3-421">创建继承自 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute> 的类，并替代 <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> 方法。</span><span class="sxs-lookup"><span data-stu-id="11db3-421">Create a class that inherits from <xref:System.ComponentModel.DataAnnotations.ValidationAttribute>, and override the <xref:System.ComponentModel.DataAnnotations.ValidationAttribute.IsValid*> method.</span></span>

<span data-ttu-id="11db3-422">`IsValid` 方法接受名为 value 的对象，该对象是要进行验证的输入  。</span><span class="sxs-lookup"><span data-stu-id="11db3-422">The `IsValid` method accepts an object named *value*, which is the input to be validated.</span></span> <span data-ttu-id="11db3-423">重载还接受 `ValidationContext` 对象，该对象提供其他信息，例如模型绑定创建的模型实例。</span><span class="sxs-lookup"><span data-stu-id="11db3-423">An overload also accepts a `ValidationContext` object, which provides additional information, such as the model instance created by model binding.</span></span>

<span data-ttu-id="11db3-424">以下示例验证“经典”流派电影的发行日期是否不晚于指定年份  。</span><span class="sxs-lookup"><span data-stu-id="11db3-424">The following example validates that the release date for a movie in the *Classic* genre isn't later than a specified year.</span></span> <span data-ttu-id="11db3-425">`[ClassicMovie2]` 特性首先检查流派，只有流派为“经典”时才会继续检查  。</span><span class="sxs-lookup"><span data-stu-id="11db3-425">The `[ClassicMovie2]` attribute checks the genre first and continues only if it's *Classic*.</span></span> <span data-ttu-id="11db3-426">如果电影流派为经典，它会检查发行日期，确保该日期不晚于传递给特性构造函数的限值。）</span><span class="sxs-lookup"><span data-stu-id="11db3-426">For movies identified as classics, it checks the release date to make sure it's not later than the limit passed to the attribute constructor.)</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttribute.cs?name=snippet_ClassicMovieAttribute)]

<span data-ttu-id="11db3-427">上述示例中的 `movie` 变量表示 `Movie` 对象，其中包含表单提交中的数据。</span><span class="sxs-lookup"><span data-stu-id="11db3-427">The `movie` variable in the preceding example represents a `Movie` object that contains the data from the form submission.</span></span> <span data-ttu-id="11db3-428">`IsValid` 方法检查日期和流派。</span><span class="sxs-lookup"><span data-stu-id="11db3-428">The `IsValid` method checks the date and genre.</span></span> <span data-ttu-id="11db3-429">验证成功时，`IsValid` 返回 `ValidationResult.Success` 代码。</span><span class="sxs-lookup"><span data-stu-id="11db3-429">Upon successful validation, `IsValid` returns a `ValidationResult.Success` code.</span></span> <span data-ttu-id="11db3-430">验证失败时，返回 `ValidationResult` 和错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-430">When validation fails, a `ValidationResult` with an error message is returned.</span></span>

## <a name="ivalidatableobject"></a><span data-ttu-id="11db3-431">IValidatableObject</span><span class="sxs-lookup"><span data-stu-id="11db3-431">IValidatableObject</span></span>

<span data-ttu-id="11db3-432">上述示例只适用于 `Movie` 类型。</span><span class="sxs-lookup"><span data-stu-id="11db3-432">The preceding example works only with `Movie` types.</span></span> <span data-ttu-id="11db3-433">类级别验证的另一方式是在模型类中实现 `IValidatableObject`，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-433">Another option for class-level validation is to implement `IValidatableObject` in the model class, as shown in the following example:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Models/MovieIValidatable.cs?name=snippet&highlight=1,26-34)]

## <a name="top-level-node-validation"></a><span data-ttu-id="11db3-434">顶级节点验证</span><span class="sxs-lookup"><span data-stu-id="11db3-434">Top-level node validation</span></span>

<span data-ttu-id="11db3-435">顶级节点包括：</span><span class="sxs-lookup"><span data-stu-id="11db3-435">Top-level nodes include:</span></span>

* <span data-ttu-id="11db3-436">操作参数</span><span class="sxs-lookup"><span data-stu-id="11db3-436">Action parameters</span></span>
* <span data-ttu-id="11db3-437">控制器属性</span><span class="sxs-lookup"><span data-stu-id="11db3-437">Controller properties</span></span>
* <span data-ttu-id="11db3-438">页处理程序参数</span><span class="sxs-lookup"><span data-stu-id="11db3-438">Page handler parameters</span></span>
* <span data-ttu-id="11db3-439">页模型属性</span><span class="sxs-lookup"><span data-stu-id="11db3-439">Page model properties</span></span>

<span data-ttu-id="11db3-440">除了验证模型属性之外，还验证了模型绑定的顶级节点。</span><span class="sxs-lookup"><span data-stu-id="11db3-440">Model-bound top-level nodes are validated in addition to validating model properties.</span></span> <span data-ttu-id="11db3-441">在示例应用的以下示例中，`VerifyPhone` 方法使用 <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> 验证 `phone` 操作参数：</span><span class="sxs-lookup"><span data-stu-id="11db3-441">In the following example from the sample app, the `VerifyPhone` method uses the <xref:System.ComponentModel.DataAnnotations.RegularExpressionAttribute> to validate the `phone` action parameter:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_VerifyPhone)]

<span data-ttu-id="11db3-442">顶级节点可以将 <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> 与验证属性结合使用。</span><span class="sxs-lookup"><span data-stu-id="11db3-442">Top-level nodes can use <xref:Microsoft.AspNetCore.Mvc.ModelBinding.BindRequiredAttribute> with validation attributes.</span></span> <span data-ttu-id="11db3-443">在示例应用的以下示例中，`CheckAge` 方法指定在提交表单时必须从查询字符串绑定 `age` 参数：</span><span class="sxs-lookup"><span data-stu-id="11db3-443">In the following example from the sample app, the `CheckAge` method specifies that the `age` parameter must be bound from the query string when the form is submitted:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Controllers/UsersController.cs?name=snippet_CheckAge)]

<span data-ttu-id="11db3-444">在“检查年限”页 (CheckAge.cshtml) 中，有两个表单  。</span><span class="sxs-lookup"><span data-stu-id="11db3-444">In the Check Age page (*CheckAge.cshtml*), there are two forms.</span></span> <span data-ttu-id="11db3-445">第一个表单提交 `Age` 的值 `99` 作为查询字符串：`https://localhost:5001/Users/CheckAge?Age=99`。</span><span class="sxs-lookup"><span data-stu-id="11db3-445">The first form submits an `Age` value of `99` as a query string: `https://localhost:5001/Users/CheckAge?Age=99`.</span></span>

<span data-ttu-id="11db3-446">当提交查询字符串中格式设置正确的 `age` 参数时，表单将进行验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-446">When a properly formatted `age` parameter from the query string is submitted, the form validates.</span></span>

<span data-ttu-id="11db3-447">“检查年限”页面上的第二个表单提交请求正文中的 `Age` 值，验证失败。</span><span class="sxs-lookup"><span data-stu-id="11db3-447">The second form on the Check Age page submits the `Age` value in the body of the request, and validation fails.</span></span> <span data-ttu-id="11db3-448">绑定失败，因为 `age` 参数必须来自查询字符串。</span><span class="sxs-lookup"><span data-stu-id="11db3-448">Binding fails because the `age` parameter must come from a query string.</span></span>

<span data-ttu-id="11db3-449">在 `CompatibilityVersion.Version_2_1` 或更高版本上运行时，默认启用顶级节点验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-449">When running with `CompatibilityVersion.Version_2_1` or later, top-level node validation is enabled by default.</span></span> <span data-ttu-id="11db3-450">否则，禁用顶级节点验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-450">Otherwise, top-level node validation is disabled.</span></span> <span data-ttu-id="11db3-451">通过在 (`Startup.ConfigureServices`) 中设置 <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> 属性，可以替代默认选项，如下所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-451">The default option can be overridden by setting the <xref:Microsoft.AspNetCore.Mvc.MvcOptions.AllowValidatingTopLevelNodes*> property in (`Startup.ConfigureServices`), as shown here:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup.cs?name=snippet_AddMvc&highlight=4)]

## <a name="maximum-errors"></a><span data-ttu-id="11db3-452">最大错误数</span><span class="sxs-lookup"><span data-stu-id="11db3-452">Maximum errors</span></span>

<span data-ttu-id="11db3-453">达到最大错误数（默认为 200）时，验证停止。</span><span class="sxs-lookup"><span data-stu-id="11db3-453">Validation stops when the maximum number of errors is reached (200 by default).</span></span> <span data-ttu-id="11db3-454">可以使用 `Startup.ConfigureServices` 中的以下代码配置该数字：</span><span class="sxs-lookup"><span data-stu-id="11db3-454">You can configure this number with the following code in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=3)]

## <a name="maximum-recursion"></a><span data-ttu-id="11db3-455">最大递归次数</span><span class="sxs-lookup"><span data-stu-id="11db3-455">Maximum recursion</span></span>

<span data-ttu-id="11db3-456"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> 遍历所验证模型的对象图。</span><span class="sxs-lookup"><span data-stu-id="11db3-456"><xref:Microsoft.AspNetCore.Mvc.ModelBinding.Validation.ValidationVisitor> traverses the object graph of the model being validated.</span></span> <span data-ttu-id="11db3-457">如果模型非常深或无限递归，验证可能导致堆栈溢出。</span><span class="sxs-lookup"><span data-stu-id="11db3-457">For models that are very deep or are infinitely recursive, validation may result in stack overflow.</span></span> <span data-ttu-id="11db3-458">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) 提供了在访问者递归超过配置深度时提前停止验证的方法。</span><span class="sxs-lookup"><span data-stu-id="11db3-458">[MvcOptions.MaxValidationDepth](xref:Microsoft.AspNetCore.Mvc.MvcOptions.MaxValidationDepth) provides a way to stop validation early if the visitor recursion exceeds a configured depth.</span></span> <span data-ttu-id="11db3-459">在 `CompatibilityVersion.Version_2_2` 或更高版本上运行时，`MvcOptions.MaxValidationDepth` 的默认值为 32。</span><span class="sxs-lookup"><span data-stu-id="11db3-459">The default value of `MvcOptions.MaxValidationDepth` is 32 when running with `CompatibilityVersion.Version_2_2` or later.</span></span> <span data-ttu-id="11db3-460">对于更低版本，该值为 null，这表示没有深度约束。</span><span class="sxs-lookup"><span data-stu-id="11db3-460">For earlier versions, the value is null, which means no depth constraint.</span></span>

## <a name="automatic-short-circuit"></a><span data-ttu-id="11db3-461">自动短路</span><span class="sxs-lookup"><span data-stu-id="11db3-461">Automatic short-circuit</span></span>

<span data-ttu-id="11db3-462">如果模型图不需要验证，验证将自动短路（跳过）。</span><span class="sxs-lookup"><span data-stu-id="11db3-462">Validation is automatically short-circuited (skipped) if the model graph doesn't require validation.</span></span> <span data-ttu-id="11db3-463">运行时为其跳过验证的对象包括基元集合（如 `byte[]`、`string[]`、`Dictionary<string, string>`）和不具有任何验证器的复杂对象图。</span><span class="sxs-lookup"><span data-stu-id="11db3-463">Objects that the runtime skips validation for include collections of primitives (such as `byte[]`, `string[]`, `Dictionary<string, string>`) and complex object graphs that don't have any validators.</span></span>

## <a name="disable-validation"></a><span data-ttu-id="11db3-464">禁用验证</span><span class="sxs-lookup"><span data-stu-id="11db3-464">Disable validation</span></span>

<span data-ttu-id="11db3-465">若要禁用验证：</span><span class="sxs-lookup"><span data-stu-id="11db3-465">To disable validation:</span></span>

1. <span data-ttu-id="11db3-466">创建不会将任何字段标记为无效的 `IObjectModelValidator` 实现。</span><span class="sxs-lookup"><span data-stu-id="11db3-466">Create an implementation of `IObjectModelValidator` that doesn't mark any fields as invalid.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/NullObjectModelValidator.cs?name=snippet_DisableValidation)]

1. <span data-ttu-id="11db3-467">将以下代码添加到 `Startup.ConfigureServices`，以便替换依赖项注入容器中的默认 `IObjectModelValidator` 实现。</span><span class="sxs-lookup"><span data-stu-id="11db3-467">Add the following code to `Startup.ConfigureServices` to replace the default `IObjectModelValidator` implementation in the dependency injection container.</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_DisableValidation)]

<span data-ttu-id="11db3-468">你可能仍然会看到模型绑定的模型状态错误。</span><span class="sxs-lookup"><span data-stu-id="11db3-468">You might still see model state errors that originate from model binding.</span></span>

## <a name="client-side-validation"></a><span data-ttu-id="11db3-469">客户端验证</span><span class="sxs-lookup"><span data-stu-id="11db3-469">Client-side validation</span></span>

<span data-ttu-id="11db3-470">客户端验证将阻止提交，直到表单变为有效为止。</span><span class="sxs-lookup"><span data-stu-id="11db3-470">Client-side validation prevents submission until the form is valid.</span></span> <span data-ttu-id="11db3-471">“提交”按钮运行 JavaScript：要么提交表单要么显示错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-471">The Submit button runs JavaScript that either submits the form or displays error messages.</span></span>

<span data-ttu-id="11db3-472">表单上存在输入错误时，客户端验证会避免到服务器的不必要往返。</span><span class="sxs-lookup"><span data-stu-id="11db3-472">Client-side validation avoids an unnecessary round trip to the server when there are input errors on a form.</span></span> <span data-ttu-id="11db3-473">_Layout.cshtml 和 _ValidationScriptsPartial.cshtml 中的以下脚本引用支持客户端验证   ：</span><span class="sxs-lookup"><span data-stu-id="11db3-473">The following script references in *_Layout.cshtml* and *_ValidationScriptsPartial.cshtml* support client-side validation:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_Layout.cshtml?name=snippet_ScriptTag)]

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Shared/_ValidationScriptsPartial.cshtml?name=snippet_ScriptTags)]

<span data-ttu-id="11db3-474">[jQuery 非介入式验证](https://github.com/aspnet/jquery-validation-unobtrusive)脚本是一个基于热门 [jQuery Validate](https://jqueryvalidation.org/) 插件的自定义 Microsoft 前端库。</span><span class="sxs-lookup"><span data-stu-id="11db3-474">The [jQuery Unobtrusive Validation](https://github.com/aspnet/jquery-validation-unobtrusive) script is a custom Microsoft front-end library that builds on the popular [jQuery Validate](https://jqueryvalidation.org/) plugin.</span></span> <span data-ttu-id="11db3-475">如果没有 jQuery 非介入式验证，则必须在两个位置编码相同的验证逻辑：一次是在模型属性上的服务器端验证特性中，一次是在客户端脚本中。</span><span class="sxs-lookup"><span data-stu-id="11db3-475">Without jQuery Unobtrusive Validation, you would have to code the same validation logic in two places: once in the server-side validation attributes on model properties, and then again in client-side scripts.</span></span> <span data-ttu-id="11db3-476">[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 [HTML 帮助程序](xref:mvc/views/overview)则使用模型属性中的验证特性和类型元数据，呈现需要验证的表单元素的 HTML 5 `data-` 特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-476">Instead, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use the validation attributes and type metadata from model properties to render HTML 5 `data-` attributes for the form elements that need validation.</span></span> <span data-ttu-id="11db3-477">jQuery 非介入式验证分析 `data-` 特性并将逻辑传递给 jQuery Validate，从而将服务器端验证逻辑有效地“复制”到客户端。</span><span class="sxs-lookup"><span data-stu-id="11db3-477">jQuery Unobtrusive Validation parses the `data-` attributes and passes the logic to jQuery Validate, effectively "copying" the server-side validation logic to the client.</span></span> <span data-ttu-id="11db3-478">可以使用标记帮助程序在客户端上显示验证错误，如下所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-478">You can display validation errors on the client using tag helpers as shown here:</span></span>

[!code-cshtml[](validation/samples/2.x/ValidationSample/Views/Movies/Create.cshtml?name=snippet_ReleaseDate&highlight=4-5)]

<span data-ttu-id="11db3-479">上述标记帮助程序呈现以下 HTML。</span><span class="sxs-lookup"><span data-stu-id="11db3-479">The preceding tag helpers render the following HTML.</span></span>

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

<span data-ttu-id="11db3-480">请注意，HTML 输出中的 `data-` 特性与 `ReleaseDate` 属性的验证特性相对应。</span><span class="sxs-lookup"><span data-stu-id="11db3-480">Notice that the `data-` attributes in the HTML output correspond to the validation attributes for the `ReleaseDate` property.</span></span> <span data-ttu-id="11db3-481">`data-val-required` 特性包含在用户未填写上映日期字段时将显示的错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-481">The `data-val-required` attribute contains an error message to display if the user doesn't fill in the release date field.</span></span> <span data-ttu-id="11db3-482">jQuery 非介入式验证将此值传递给 jQuery Validate [required()](https://jqueryvalidation.org/required-method/) 方法，该方法随后在随附的 **\<span>** 元素中显示该消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-482">jQuery Unobtrusive Validation passes this value to the jQuery Validate [required()](https://jqueryvalidation.org/required-method/) method, which then displays that message in the accompanying **\<span>** element.</span></span>

<span data-ttu-id="11db3-483">如果 `[DataType]` 特性未替代属性的 .NET 类型，则数据类型验证基于该类型。</span><span class="sxs-lookup"><span data-stu-id="11db3-483">Data type validation is based on the .NET type of a property, unless that is overridden by a `[DataType]` attribute.</span></span> <span data-ttu-id="11db3-484">浏览器具有自己的默认错误消息，但是 jQuery 验证非介入式验证包可以替代这些消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-484">Browsers have their own default error messages, but the jQuery Validation Unobtrusive Validation package can override those messages.</span></span> <span data-ttu-id="11db3-485">通过 `[DataType]` 特性和 `[EmailAddress]` 等子类可以指定错误消息。</span><span class="sxs-lookup"><span data-stu-id="11db3-485">`[DataType]` attributes and subclasses such as `[EmailAddress]` let you specify the error message.</span></span>

### <a name="add-validation-to-dynamic-forms"></a><span data-ttu-id="11db3-486">向动态表单添加验证</span><span class="sxs-lookup"><span data-stu-id="11db3-486">Add Validation to Dynamic Forms</span></span>

<span data-ttu-id="11db3-487">jQuery 非介入式验证会在第一次加载页面时将验证逻辑和参数传递到 jQuery Validate。</span><span class="sxs-lookup"><span data-stu-id="11db3-487">jQuery Unobtrusive Validation passes validation logic and parameters to jQuery Validate when the page first loads.</span></span> <span data-ttu-id="11db3-488">因此，不会对动态生成的表单自动执行验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-488">Therefore, validation doesn't work automatically on dynamically generated forms.</span></span> <span data-ttu-id="11db3-489">若要启用验证，指示 jQuery 非介入式验证在创建动态表单后立即对其进行分析。</span><span class="sxs-lookup"><span data-stu-id="11db3-489">To enable validation, tell jQuery Unobtrusive Validation to parse the dynamic form immediately after you create it.</span></span> <span data-ttu-id="11db3-490">例如，以下代码在通过 AJAX 添加的表单上设置客户端验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-490">For example, the following code sets up client-side validation on a form added via AJAX.</span></span>

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

<span data-ttu-id="11db3-491">`$.validator.unobtrusive.parse()` 方法采用 jQuery 选择器作为它的一个参数。</span><span class="sxs-lookup"><span data-stu-id="11db3-491">The `$.validator.unobtrusive.parse()` method accepts a jQuery selector for its one argument.</span></span> <span data-ttu-id="11db3-492">此方法指示 jQuery 非介入式验证分析该选择器内表单的 `data-` 属性。</span><span class="sxs-lookup"><span data-stu-id="11db3-492">This method tells jQuery Unobtrusive Validation to parse the `data-` attributes of forms within that selector.</span></span> <span data-ttu-id="11db3-493">然后将这些特性的值传递给 jQuery Validate 插件。</span><span class="sxs-lookup"><span data-stu-id="11db3-493">The values of those attributes are then passed to the jQuery Validate plugin.</span></span>

### <a name="add-validation-to-dynamic-controls"></a><span data-ttu-id="11db3-494">向动态控件添加验证</span><span class="sxs-lookup"><span data-stu-id="11db3-494">Add Validation to Dynamic Controls</span></span>

<span data-ttu-id="11db3-495">`$.validator.unobtrusive.parse()` 方法适用于整个表单，而不是 `<input>` 和 `<select/>` 等单个动态生成的控件。</span><span class="sxs-lookup"><span data-stu-id="11db3-495">The `$.validator.unobtrusive.parse()` method works on an entire form, not on individual dynamically generated controls, such as `<input>` and `<select/>`.</span></span> <span data-ttu-id="11db3-496">若要重新分析表单，删除之前分析表单时添加的验证数据，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-496">To reparse the form, remove the validation data that was added when the form was parsed earlier, as shown in the following example:</span></span>

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

## <a name="custom-client-side-validation"></a><span data-ttu-id="11db3-497">自定义客户端验证</span><span class="sxs-lookup"><span data-stu-id="11db3-497">Custom client-side validation</span></span>

<span data-ttu-id="11db3-498">生成适用于自定义 jQuery Validate 适配器的 `data-` HTML 特性，完成自定义客户端验证。</span><span class="sxs-lookup"><span data-stu-id="11db3-498">Custom client-side validation is done by generating `data-` HTML attributes that work with a custom jQuery Validate adapter.</span></span> <span data-ttu-id="11db3-499">以下示例适配器代码是为本文前面部分介绍的 `ClassicMovie` 和 `ClassicMovie2` 特性编写的：</span><span class="sxs-lookup"><span data-stu-id="11db3-499">The following sample adapter code was written for the `ClassicMovie` and `ClassicMovie2` attributes that were introduced earlier in this article:</span></span>

[!code-javascript[](validation/samples/2.x/ValidationSample/wwwroot/js/classicMovieValidator.js?name=snippet_UnobtrusiveValidation)]

<span data-ttu-id="11db3-500">有关如何编写适配器的信息，请参阅 [jQuery Validate 文档](https://jqueryvalidation.org/documentation/)。</span><span class="sxs-lookup"><span data-stu-id="11db3-500">For information about how to write adapters, see the [jQuery Validate documentation](https://jqueryvalidation.org/documentation/).</span></span>

<span data-ttu-id="11db3-501">给定字段的适配器的使用由 `data-` 特性触发，这些特性：</span><span class="sxs-lookup"><span data-stu-id="11db3-501">The use of an adapter for a given field is triggered by `data-` attributes that:</span></span>

* <span data-ttu-id="11db3-502">将字段标记为正在验证 (`data-val="true"`)。</span><span class="sxs-lookup"><span data-stu-id="11db3-502">Flag the field as being subject to validation (`data-val="true"`).</span></span>
* <span data-ttu-id="11db3-503">确定验证规则名称和错误消息文本（例如，`data-val-rulename="Error message."`）。</span><span class="sxs-lookup"><span data-stu-id="11db3-503">Identify a validation rule name and error message text (for example, `data-val-rulename="Error message."`).</span></span>
* <span data-ttu-id="11db3-504">提供验证器所需的任何其他参数（例如，`data-val-rulename-parm1="value"`）。</span><span class="sxs-lookup"><span data-stu-id="11db3-504">Provide any additional parameters the validator needs (for example, `data-val-rulename-parm1="value"`).</span></span>

<span data-ttu-id="11db3-505">以下示例显示示例应用的 `ClassicMovie` 特性的 `data-` 特性：</span><span class="sxs-lookup"><span data-stu-id="11db3-505">The following example shows the `data-` attributes for the sample app's `ClassicMovie` attribute:</span></span>

```html
<input class="form-control" type="datetime"
    data-val="true"
    data-val-classicmovie1="Classic movies must have a release year earlier than 1960."
    data-val-classicmovie1-year="1960"
    data-val-required="The ReleaseDate field is required."
    id="ReleaseDate" name="ReleaseDate" value="">
```

<span data-ttu-id="11db3-506">如前所述，[标记帮助程序](xref:mvc/views/tag-helpers/intro)和 [HTML 帮助程序](xref:mvc/views/overview)使用验证特性的信息呈现 `data-` 特性。</span><span class="sxs-lookup"><span data-stu-id="11db3-506">As noted earlier, [Tag Helpers](xref:mvc/views/tag-helpers/intro) and [HTML helpers](xref:mvc/views/overview) use information from validation attributes to render `data-` attributes.</span></span> <span data-ttu-id="11db3-507">编写用于创建自定义 `data-` HTML 特性的代码有以下两种方式：</span><span class="sxs-lookup"><span data-stu-id="11db3-507">There are two options for writing code that results in the creation of custom `data-` HTML attributes:</span></span>

* <span data-ttu-id="11db3-508">创建派生自 `AttributeAdapterBase<TAttribute>` 的类和实现 `IValidationAttributeAdapterProvider` 的类，并在 DI 中注册特性及其适配器。</span><span class="sxs-lookup"><span data-stu-id="11db3-508">Create a class that derives from `AttributeAdapterBase<TAttribute>` and a class that implements `IValidationAttributeAdapterProvider`, and register your attribute and its adapter in DI.</span></span> <span data-ttu-id="11db3-509">此方法遵循[单一责任主体](https://wikipedia.org/wiki/Single_responsibility_principle)，这体现在与服务器相关的验证代码和与客户端相关的验证代码位于不同的类中。</span><span class="sxs-lookup"><span data-stu-id="11db3-509">This method follows the [single responsibility principal](https://wikipedia.org/wiki/Single_responsibility_principle) in that server-related and client-related validation code is in separate classes.</span></span> <span data-ttu-id="11db3-510">适配器还有以下优势：由于适配器是在 DI 中注册的，所以如果需要，适配器可以使用 DI 中的其他服务。</span><span class="sxs-lookup"><span data-stu-id="11db3-510">The adapter also has the advantage that since it is registered in DI, other services in DI are available to it if needed.</span></span>
* <span data-ttu-id="11db3-511">在 `ValidationAttribute` 类中实现 `IClientModelValidator`。</span><span class="sxs-lookup"><span data-stu-id="11db3-511">Implement `IClientModelValidator` in your `ValidationAttribute` class.</span></span> <span data-ttu-id="11db3-512">如果特性既不进行任何服务器端验证，也不需要 DI 的任何服务，则此方法可能适用。</span><span class="sxs-lookup"><span data-stu-id="11db3-512">This method might be appropriate if the attribute doesn't do any server-side validation and doesn't need any services from DI.</span></span>

### <a name="attributeadapter-for-client-side-validation"></a><span data-ttu-id="11db3-513">用于客户端验证的 AttributeAdapter</span><span class="sxs-lookup"><span data-stu-id="11db3-513">AttributeAdapter for client-side validation</span></span>

<span data-ttu-id="11db3-514">在 HTML 中呈现 `data-` 特性的方法在示例应用中由 `ClassicMovie` 特性使用。</span><span class="sxs-lookup"><span data-stu-id="11db3-514">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie` attribute in the sample app.</span></span> <span data-ttu-id="11db3-515">若要使用此方法添加客户端验证：</span><span class="sxs-lookup"><span data-stu-id="11db3-515">To add client validation by using this method:</span></span>

1. <span data-ttu-id="11db3-516">为自定义验证特性创建特性适配器类。</span><span class="sxs-lookup"><span data-stu-id="11db3-516">Create an attribute adapter class for the custom validation attribute.</span></span> <span data-ttu-id="11db3-517">从 [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2) 派生类。</span><span class="sxs-lookup"><span data-stu-id="11db3-517">Derive the class from [AttributeAdapterBase\<T>](/dotnet/api/microsoft.aspnetcore.mvc.dataannotations.attributeadapterbase-1?view=aspnetcore-2.2).</span></span> <span data-ttu-id="11db3-518">创建将 `data-` 特性添加到所呈现输出中的 `AddValidation` 方法，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-518">Create an `AddValidation` method that adds `data-` attributes to the rendered output, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovieAttributeAdapter.cs?name=snippet_ClassicMovieAttributeAdapter)]

1. <span data-ttu-id="11db3-519">创建实现 <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider> 的适配器提供程序类。</span><span class="sxs-lookup"><span data-stu-id="11db3-519">Create an adapter provider class that implements <xref:Microsoft.AspNetCore.Mvc.DataAnnotations.IValidationAttributeAdapterProvider>.</span></span> <span data-ttu-id="11db3-520">使用 `GetAttributeAdapter` 方法，将自定义属性传递给适配器的构造函数，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-520">In the `GetAttributeAdapter` method pass in the custom attribute to the adapter's constructor, as shown in this example:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/CustomValidationAttributeAdapterProvider.cs?name=snippet_CustomValidationAttributeAdapterProvider)]

1. <span data-ttu-id="11db3-521">在 `Startup.ConfigureServices` 中为 DI 注册适配器提供程序：</span><span class="sxs-lookup"><span data-stu-id="11db3-521">Register the adapter provider for DI in `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](validation/samples/2.x/ValidationSample/Startup.cs?name=snippet_MaxModelValidationErrors&highlight=8-10)]

### <a name="iclientmodelvalidator-for-client-side-validation"></a><span data-ttu-id="11db3-522">用于客户端验证的 IClientModelValidator</span><span class="sxs-lookup"><span data-stu-id="11db3-522">IClientModelValidator for client-side validation</span></span>

<span data-ttu-id="11db3-523">在 HTML 中呈现 `data-` 特性的方法在示例应用中由 `ClassicMovie2` 特性使用。</span><span class="sxs-lookup"><span data-stu-id="11db3-523">This method of rendering `data-` attributes in HTML is used by the `ClassicMovie2` attribute in the sample app.</span></span> <span data-ttu-id="11db3-524">若要使用此方法添加客户端验证：</span><span class="sxs-lookup"><span data-stu-id="11db3-524">To add client validation by using this method:</span></span>

* <span data-ttu-id="11db3-525">在自定义验证特性中，实现 `IClientModelValidator` 接口并创建 `AddValidation` 方法。</span><span class="sxs-lookup"><span data-stu-id="11db3-525">In the custom validation attribute, implement the `IClientModelValidator` interface and create an `AddValidation` method.</span></span> <span data-ttu-id="11db3-526">使用 `AddValidation` 方法，添加 `data-` 特性进行验证，如下例所示：</span><span class="sxs-lookup"><span data-stu-id="11db3-526">In the `AddValidation` method, add `data-` attributes for validation, as shown in the following example:</span></span>

  [!code-csharp[](validation/samples/2.x/ValidationSample/Attributes/ClassicMovie2Attribute.cs?name=snippet_ClassicMovie2Attribute)]

## <a name="disable-client-side-validation"></a><span data-ttu-id="11db3-527">禁用客户端验证</span><span class="sxs-lookup"><span data-stu-id="11db3-527">Disable client-side validation</span></span>

<span data-ttu-id="11db3-528">以下代码禁用 MVC 视图中的客户端验证：</span><span class="sxs-lookup"><span data-stu-id="11db3-528">The following code disables client validation in MVC views:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup2.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="11db3-529">在 Razor Pages 中：</span><span class="sxs-lookup"><span data-stu-id="11db3-529">And in Razor Pages:</span></span>

[!code-csharp[](validation/samples_snapshot/2.x/Startup3.cs?name=snippet_DisableClientValidation)]

<span data-ttu-id="11db3-530">禁用客户端验证的另一方式是在 .cshtml 文件中为 `_ValidationScriptsPartial` 的引用添加注释  。</span><span class="sxs-lookup"><span data-stu-id="11db3-530">Another option for disabling client validation is to comment out the reference to `_ValidationScriptsPartial` in your *.cshtml* file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="11db3-531">其他资源</span><span class="sxs-lookup"><span data-stu-id="11db3-531">Additional resources</span></span>

* [<span data-ttu-id="11db3-532">System.ComponentModel.DataAnnotations 命名空间</span><span class="sxs-lookup"><span data-stu-id="11db3-532">System.ComponentModel.DataAnnotations namespace</span></span>](xref:System.ComponentModel.DataAnnotations)
* [<span data-ttu-id="11db3-533">模型绑定</span><span class="sxs-lookup"><span data-stu-id="11db3-533">Model Binding</span></span>](model-binding.md)

::: moniker-end
